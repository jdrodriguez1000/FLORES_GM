# Sincronización NotebookLM — Flores_GM
> **ID de Notebook:** `b7e5c6e8-b0cb-4026-8a2b-7a5d6030c3dd`
> **Fecha de Sincronización:** 2026-04-24
> **Estado:** Cierre de Fase 1: Discovery

---

## 🏗️ Resumen Ejecutivo de la Fase
Se ha completado la Línea Base Documental. El proyecto ha migrado de una arquitectura FastAPI/HTML a un **Monolito Modular basado en Streamlit** para optimizar simplicidad y costos. Se han establecido contratos técnicos y de datos bajo el paradigma **"Zero Trust"**.

---

## 1. Arquitectura del Sistema (SAD v2.3.0)
*   **Tecnología:** Streamlit + Scikit-learn + Pydantic v2.
*   **Despliegue:** Docker en Cloud Run/App Runner (<$50/mes).
*   **Seguridad:** Thread-safety forzado (Predictor inmutable) y configuraciones de hardening de Streamlit.
*   **Observabilidad:** Telemetría JSON vía stdout para entornos serverless sin base de datos.

---

## 2. Contratos Técnicos (SpecDD v2.5.0)
*   **Determinismo:** Uso obligatorio de `RANDOM_SEED` en todo el pipeline.
*   **Fail-Fast:** Validación de versión de `scikit-learn` en el arranque.
*   **Concurrencia:** Inferencia síncrona delegada a threadpool para no bloquear el event loop.
*   **UX:** Captura de errores de Pydantic para evitar Information Disclosure (Stack Traces).

---

## 3. Contrato de Datos (v1.5.0)
*   **Idempotencia:** `flower_id` generado mediante Hashing determinista.
*   **Calidad (EDQ):** 0% tolerancia a nulos, NaN o Infinitos.
*   **Integridad Biológica:** Reglas botánicas estrictas (`sepal_length >= petal_length`).
*   **Persistencia:** Parquet con esquema explícito (float64).
*   **Volumen Mínimo:** El pipeline colapsa si queda menos del 80% de los datos tras la limpieza.

---

## 📊 Backlog de la Fase 2: Engineering (Próximos Pasos)
1. Implementación de `src/config.py` (Pydantic Settings).
2. Construcción de `src/pipeline.py` (Orquestador determinista).
3. Ingesta Bronze -> Silver con validación de Hash e integridad botánica.

---
**Nota para el Usuario:** Copia este bloque de información o el archivo generado en tu sesión de NotebookLM para actualizar su base de conocimientos.
