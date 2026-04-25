# SpecDD: Specification-Driven Development
> **Proyecto:** Flores_GM (Clasificador de Iris)
> **Versión:** 2.5.0 (Extreme Edge Cases & ML Contracts)
> **Estado:** Aprobado Estricto
> **Responsable:** ai-solutions-architect
> **Trazabilidad:** SAD v2.3.0 → behavior.md v1.0

---

## 1. Estándares Globales de Código
- **Estilo:** PEP 8 (Black formatter).
- **Tipado:** Tipado estático obligatorio (Type Hints) en todas las firmas.
- **Validación:** Pydantic para todos los objetos de transferencia de datos (DTO) y `pydantic-settings` para variables de entorno.
- **Telemetría:** Logs en formato JSON (Level, Timestamp, Module, Message).
- **Testing (Obligatorio):** Todos los módulos deben estar cubiertos por pruebas unitarias y de integración usando `pytest` en el directorio `tests/`.
- **Contrato de Persistencia (Parquet):** Prohibida la inferencia de esquemas. Toda escritura a `.parquet` debe realizarse con un esquema explícito o forzando el casting final (todas las dimensiones florales DEBEN ser estrictamente `float64` en todas las fases) para evitar casteos silenciosos y warnings de Scikit-learn en producción.

---

## 2. Fase 2: Engineering (Pipeline de Datos)

### 2.0 Módulo de Configuración: `src/config.py`
**Responsabilidad:** Centralizar todas las variables de entorno y reglas de negocio dinámicas.
- **Contrato:** Ningún módulo usará `os.environ` directamente. Deben importar `settings` de este archivo.
- **Clase:** `Settings(BaseSettings)` con campos `model_path`, `data_path`, `noise_level`, obligatoriamente `random_seed: int = 42`, y **`confidence_threshold: float = 0.70`**.

### 2.1 Módulo: `src/engineering/ingestion.py`
**Responsabilidad:** Transformar datos de Bronze a Silver (Limpieza e Identificación).

- **Función:** `process_bronze_to_silver(input_path: str, output_path: str) -> bool`
    - **Entrada:** `data/Bronze/Iris.csv`.
    - **Salida:** `data/Silver/Iris_silver.parquet` (o .csv con UUID).
    - **Contrato:**
        - Eliminar duplicados.
        - Generar `flower_id` (UUID4) por cada fila.
        - Asegurar tipos de datos (`float64` para medidas, String para especies).
    - **Excepciones:** `FileNotFoundError`, `ValueError` (si el esquema no coincide).

### 2.2 Módulo: `src/engineering/augmentation.py`
**Responsabilidad:** Transformar datos de Silver a Gold (Robustez ante Measurement Drift).

- **Función:** `apply_augmentation(input_path: str, output_path: str, noise_level: float, random_seed: int) -> bool`
    - **Entrada:** `data/Silver/Iris_silver.parquet`.
    - **Salida:** `data/Gold/Iris_gold.parquet`.
    - **Contrato:**
        - Inyectar ruido Gaussiano ($\mu=0, \sigma=noise\_level$) a las 4 medidas usando `random_seed`.
        - Mantener el `flower_id` original.
        - Redondear a 1 decimal tras el ruido y re-asegurar `float64`.
    - **Criterio de Aceptación:** El dataset Gold debe tener el mismo número de filas que el Silver y ser 100% determinista.

---

## 3. Fase 3: Modeling (Entrenamiento y Evaluación)

### 3.1 Módulo: `src/modeling/split.py`
**Responsabilidad:** Separar el dataset Gold para prevenir **Data Leakage**.

- **Función:** `split_data(input_path: str, train_path: str, test_path: str, test_size: float, random_seed: int) -> None`
    - **Entrada:** `data/Gold/Iris_gold.parquet`.
    - **Salida:** `data/Gold/train.parquet` y `data/Gold/test.parquet`.
    - **Restricción:** El split debe ser estratificado por especie. Debe usar `random_seed` inyectado.

### 3.2 Módulo: `src/modeling/trainer.py`
**Responsabilidad:** Entrenar el modelo regularizado.

- **Función:** `train_model(data_path: str, model_path: str, random_seed: int) -> Dict[str, Any]`
    - **Entrada:** `data/Gold/train.parquet`.
    - **Salida:** Archivo `models/iris_model.joblib` guardado como diccionario: `{"model": Estimator, "target_names": List[str], "feature_names": List[str], "sklearn_version": str, "version": str}`.
    - **Retorno:** Diccionario con hiperparámetros.
    - **Restricción:** Usar **Stratified K-Fold CV** (K=5). El algoritmo elegido DEBE soportar y exponer el método `predict_proba()` nativamente (ej. si se usa SVM, se debe setear `probability=True`).

### 3.3 Módulo: `src/modeling/evaluator.py`
**Responsabilidad:** Certificar la calidad del modelo contra el BRD.

- **Función:** `evaluate_model(model_path: str, test_data_path: str) -> Dict[str, float]`
    - **Entrada:** El modelo `.joblib` y `data/Gold/test.parquet`.
    - **Retorno:** `{ "accuracy": float, "macro_f1": float, "latency_ms": float }`.
    - **Certificación:** Debe validar si `accuracy >= 0.95`.
    - **Restricción:** `latency_ms` debe calcularse como el tiempo promedio ejecutando 100 predicciones individuales consecutivas (no batch).

---

## 4. Fase 4: Delivery (Inferencia Interactiva)

### 4.1 Módulo: `src/core/predictor.py`
**Responsabilidad:** Motor de inferencia agnóstico al framework web.

- **Clase:** `IrisPredictor`
    - **Método:** `__init__(self, model_path: str)` (Carga el diccionario `joblib`. **Fail-Fast:** Levantar `RuntimeError` si falta alguna clave o si `sklearn_version` no coincide).
    - **Método:** `predict(self, features: IrisFeatures) -> PredictionResult`
        - **Contrato Interno:** Transforma Pydantic a tensor 2D: `np.array([[...]], dtype=np.float64)`. El modelo no debe modificar estado global. Las probabilidades devueltas deben redondearse (ej. a 4 decimales) para evitar bugs de precisión en el frontend.
- **Modelos de Datos (Pydantic):**
    ```python
    class IrisFeatures(BaseModel):
        model_config = {"str_strip_whitespace": True, "extra": "forbid"}
        sepal_length: float = Field(..., gt=0, le=15, allow_inf_nan=False)
        sepal_width: float = Field(..., gt=0, le=15, allow_inf_nan=False)
        petal_length: float = Field(..., gt=0, le=15, allow_inf_nan=False)
        petal_width: float = Field(..., gt=0, le=15, allow_inf_nan=False)

        @field_validator('*', mode='before')
        def round_to_one_decimal(cls, v):
            if isinstance(v, (int, float)):
                return round(v, 1)
            return v

    class PredictionResult(BaseModel):
        species: str
        confidence: float
        probabilities: Dict[str, float]
        status: str # "success" | "low_confidence"
    ```

### 4.2 Módulo: `src/app/main.py`
**Responsabilidad:** Aplicación Web Interactiva basada en Streamlit.

- **Inicialización (Caché y Config):**
    - Obligatorio `@st.cache_resource` para inicializar `IrisPredictor`.
    - Leer `confidence_threshold` desde `src/config.py` (Prohibido hardcodear).
- **Controlador (Logic) y Prevención de Stack Trace:**
    1. Instanciar `IrisFeatures()` dentro de un bloque `try...except ValidationError:`. Si falla, capturar el error, usar `st.error("Datos fuera de límites biológicos")` y usar `st.stop()` para prevenir la fuga de código fuente (Information Disclosure).
    2. Llama a `predict()`. Si `confidence < settings.confidence_threshold`, setea el status a `low_confidence` y lanza un `st.warning()`.
    3. Imprime JSON de Telemetría en `stdout`.
    4. **Mitigación OOM:** Limpiar explícitamente variables temporales de `st.session_state`.

---

## 5. Mocking y Orquestación
- **Orquestador:** Prohibido ejecutar scripts sueltos. Debe existir un script o `Makefile` que ejecute el pipeline entero en orden: Ingesta $\rightarrow$ Augmentation $\rightarrow$ Split $\rightarrow$ Train $\rightarrow$ Eval.
- **Mocking UI:** Para que el equipo Frontend pueda trabajar sin modelo, el `IrisPredictor` debe implementar un modo `mock=True` que retorne valores balanceados aleatorios.

---
> **Validación SpecDD:** Este documento ha sido validado contra el SAD v2.5.0 y el BDD v1.0. Queda terminantemente prohibido iniciar el desarrollo de archivos `.py` sin que su firma esté aquí descrita.