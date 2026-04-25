# Software Architecture Document (SAD)
> **Proyecto:** Flores_GM (Clasificador de Iris)
> **Versión:** 2.3.0 (Enterprise Resilience Review)
> **Estado:** Aprobado Definitivo
> **Responsable:** ai-solutions-architect

---

## 1. Resumen de la Arquitectura
La arquitectura de **Flores_GM** es una **Aplicación Web Monolítica basada en Streamlit** altamente fortificada. Se mantiene el desacoplamiento estricto entre el motor de inferencia y la UI. Esta versión incluye defensas extremas orientadas a la reproducibilidad absoluta (Determinismo), protección de la cadena de suministro (Supply Chain Security), seguridad concurrente (Thread-Safety) y gobernanza del repositorio.

## 2. Stack Tecnológico & Dependencias Estrictas

| Capa | Tecnología | Justificación y Mitigación de Riesgo |
| :--- | :--- | :--- |
| **Frontend & API** | Streamlit | Desarrollo ultrarrápido. **Riesgos Mitigados:** Bloqueo de recargas mediante `@st.cache_resource` y mitigación de XSS (sin `unsafe_allow_html`). |
| **ML Framework** | Scikit-learn | **Riesgo Mitigado:** Versión fijada. |
| **Validación & Config**| Pydantic v2 & Settings | Redondeo interceptado, orden estricto, rechazo de `NaN`/`Infinity` y gestión robusta de variables de entorno. |
| **Gestión de Paquetes**| pip-tools / uv | **Seguridad de Cadena de Suministro:** Prohibido usar un `requirements.txt` libre. Se debe usar un lockfile (`requirements.lock` o `poetry.lock`) con hashes criptográficos para blindar todas las dependencias transitivas (Numpy, SciPy, etc.) y evitar envenenamiento o incompatibilidad silente. |

## 3. Gobernanza de Repositorio y Datos

**Riesgo de Fuga de Datos y Bloating (Git):** Los repositorios de ML se corrompen si se versionan datos masivos o binarios mutables.
1. **Regla de `.gitignore`:** Queda terminantemente prohibido hacer commit del contenido de la carpeta `data/` (Bronze/Silver/Gold). Los datos deben existir solo en el entorno local o un bucket en la nube (S3/GCS).
2. **Git LFS (Large File Storage):** Si el artefacto `.joblib` en `models/` supera los 5MB, debe ser versionado obligatoriamente mediante Git LFS para no degradar el historial de Git.

## 4. Topología de Datos y Contratos de ML

### 4.1 Orden Estricto (Vector Contract)
`X = [[ sepal_length, sepal_width, petal_length, petal_width ]]`

### 4.2 Prevención de Data Leakage (Split Contract)
Aislamiento de **Test Set** (`test.parquet`) del Feature Store (Gold) **antes** de cualquier entrenamiento.

### 4.3 Contrato de Determinismo Absoluto (Reproducibilidad)
Un pipeline de ML que no es reproducible es software defectuoso. **Prohibido el uso de aleatoriedad libre.**
Cualquier operación estocástica (Inyección de ruido en Augmentation, Stratified Split, Inicialización de modelos como Random Forest) DEBE utilizar una semilla global (`RANDOM_SEED`) inyectada desde la configuración central (`src/config.py`). Si la pipeline se ejecuta 100 veces, debe generar exactamente el mismo `.joblib` bit a bit.

## 5. Diseño del Artefacto de Modelado (El "Magic Array" Fix)

El archivo `models/iris_model.joblib` debe empaquetar la inteligencia completa:
```python
# Estructura obligatoria del Joblib
{
    "model": <sklearn_estimator>,
    "target_names": ["setosa", "versicolor", "virginica"],
    "feature_names": ["sepal_length", "sepal_width", "petal_length", "petal_width"],
    "version": "1.0.0"
}
```
**Resolución de Rutas:** Acceso a disco resuelto dinámicamente (`pathlib.Path`).

## 6. Estrategia de Infraestructura & Defensas Streamlit

1. **Límite de Escalamiento:** Serverless con `max-instances: 2`.
2. **Streamlit Config Hardening (`.streamlit/config.toml`):**
   - `server.maxUploadSize = 1`, `server.enableXsrfProtection = true`, `server.enableCORS = false`.
3. **Session State Memory Leak Prevention:** Limpieza explícita tras cada inferencia.
4. **Telemetría Obligatoria:** JSON log por inferencia.

## 7. Configuración, Orquestación y Concurrencia

1. **Environment Config:** Centralización vía `pydantic-settings` (`Settings`).
2. **Pipeline Orchestration:** Uso obligatorio de `src/pipeline.py` o `Makefile` para correr las Fases 2 y 3.
3. **Thread-Safety Contract (Mutación de Estado):** Streamlit opera en un entorno multihilo. La instancia cargada del modelo (`IrisPredictor` cacheado por `@st.cache_resource`) será compartida simultáneamente por todos los usuarios conectados. **Está estrictamente prohibido mutar el estado (`self.algo = ...`) del Predictor después de la inicialización (`__init__`).** Toda variable procesada en `predict()` debe ser local a la función para evitar "Race Conditions" donde la predicción de un usuario sobreescriba la de otro.

## 8. Interfaces y Contratos de Red

### 8.1 Interfaz de Usuario (UI)
*   **Input:** 4 Sliders numéricos.
*   **Validación:** Pydantic intercepta antes de llamar a Scikit-learn.

### 8.2 Endpoint de Salud (Healthcheck)
*   **Ruta:** `GET /_stcore/health` (Liveness probe nativo de Streamlit).