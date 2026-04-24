---
name: data-model-lineage-validator
description: Protocolo para garantizar la trazabilidad (linaje) absoluta entre las versiones de los datos fuentes, datos procesados (Gold) y los artefactos de modelos.
user-invocable: false
agent: ai-mlops-specialist
allowed-tools: [Read, Write, Bash, Python-Interpreter]
---

## 🏗️ I. Mapeo de Versiones (Data-to-Model)
El agente debe vincular cada modelo con su origen exacto:
1. **Data Versioning (DVC):** Asegurar que cada entrenamiento apunte a un `data_hash` específico del repositorio de datos.
2. **Registro de Metadata:** Inyectar en el Model Registry la URI de los datos utilizados (ej: `s3://bucket/gold-v2.1.parquet`).
3. **Auditability del Pipeline:** Poder responder a la pregunta: "¿Qué versión de los datos generó este error en la producción del modelo V1.4?".

## 📐 II. Validación de Linaje Técnico
1. **Consistencia de Features:** Verificar que el modelo solicita exactamente las mismas columnas que fueron registradas en el dataset de entrenamiento.
2. **Trazabilidad de Transformaciones:** Documentar qué versión del código de limpieza (Capa Silver) y generación de variables (Capa Gold) se inyectó en el entrenamiento.

## 🚀 III. Certificación de Reproducibilidad
1. **Sello de Linaje:** Certificar que el modelo es "Auditable" y que se puede reconstruir desde cero (datos + código) si es necesario.

---

> **Check de Certificación de Linaje:**
> - [ ] ¿Cada modelo registrado tiene asociado un hash único de los datos de entrenamiento?
> - [ ] ¿Se puede identificar la versión del código (commit) que generó el artefacto?
> - [ ] ¿El linaje cubre todas las capas desde Bronze hasta Gold?
