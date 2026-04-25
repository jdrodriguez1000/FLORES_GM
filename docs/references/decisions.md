# Log de Decisiones y Aprendizajes

## [2026-04-24] — Sesión de Inicialización (Bootstrap)
*(Sin cambios)*

## [2026-04-24] — Cierre de Fase Discovery (Discovery Iteration 1.2-1.3)
*(Sin cambios)*

## [2026-04-24] — Refactorización Arquitectónica y Contratos "Zero-Trust" (Iteración 1.4-1.5)

### 🧩 Contexto
Revisión técnica exhaustiva bajo mentalidad de "Abogado del Diablo" para eliminar ambigüedades antes de iniciar la construcción del código.

### 💡 Decisiones Clave
1. **Migración a Streamlit (v2.0.0):** Se decidió reemplazar FastAPI + Vanilla JS por Streamlit para unificar el stack en Python puro. Esto reduce la complejidad de infraestructura y elimina problemas de CORS al ser un origen único.
2. **Determinismo Obligatorio (v2.3.0):** Se impuso el uso de `RANDOM_SEED` centralizado en todas las etapas del pipeline (Augmentation, Split, Train) para garantizar que el modelo sea reproducible bit a bit.
3. **Idempotencia de Datos (v1.5.0):** Los `flower_id` se generarán mediante hashing determinista de las medidas, no aleatoriamente. Esto garantiza que re-ejecutar el pipeline no genere duplicados en la capa Silver/Gold.
4. **Integridad Biológica:** Se añadieron reglas de anatomía botánica (`sepal_length >= petal_length`) para detectar y rechazar automáticamente inputs invertidos o mal medidos.
5. **Defensas de Streamlit:** Configuración estricta de `maxUploadSize = 1` y prohibición de `unsafe_allow_html=True` para prevenir ataques de DoS y XSS nativos del framework.

### 🎓 Aprendizajes (Learnings)
- **Mentalidad Devil's Advocate:** Auditar los contratos de datos antes de programar reveló que la comparación de flotantes (`float64`) sin redondeo previo habría fallado en la detección de duplicados por errores de precisión de la FPU.
- **Fail-Fast Policy:** La importancia de validar la versión de `scikit-learn` dentro del artefacto `.joblib` para evitar fallos de deserialización ininteligibles en producción.
