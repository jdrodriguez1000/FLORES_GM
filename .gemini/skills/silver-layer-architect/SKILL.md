---
name: silver-layer-architect
description: Protocolo para la construcción de la capa de datos Silver, centrada en la limpieza, normalización y estructuración de datos para consumo analítico.
user-invocable: false
agent: ai-analytics-engineer
allowed-tools: [Read, Write, Edit, Bash, Python-Interpreter]
---

## 🏗️ I. Procesos de Limpieza y Normalización
El agente debe transformar los datos de Bronze a Silver siguiendo estas reglas:
1. **Normalización de Formatos:** Estandarizar fechas (ISO 8601), strings (trim, lower/upper case) y unidades de medida.
2. **Deduplicación de Negocio:** A diferencia de la deduplicación técnica, aquí se aplican reglas de negocio (ej: "mismo cliente si coinciden DNI y Apellido").
3. **Manejo de Errores Tipificados:** Convertir valores basura conocidos (ej: "0000", "TEST") en nulos formales (`None`/`np.nan`).

## 📐 II. Desarrollo Modular bajo SpecDD
Cada tarea de limpieza debe vivir en un módulo `.py` independiente:
1. **Implementación de Clases de Transformación:** Seguir las interfaces definidas por el Solutions Architect.
2. **Manejo de Esquemas:** Asegurar que el output del módulo coincida con el contrato de la capa Silver.
3. **Optimización de Memoria:** Utilizar procesamiento vectorizado (Pandas/Polars) para garantizar la eficiencia en grandes volúmenes.

## 🚀 III. Versionado de Lógica de Limpieza
1. **Control de Versiones de Código:** Todos los cambios en la lógica de normalización deben estar versionados en Git.
2. **Documentación de Reglas:** Cada transformación debe estar comentada explicando el "por qué" detrás del cambio (ej: "¿Por qué se filtran precios > 1M?").

---

> **Check de Certificación Silver:**
> - [ ] ¿Los datos están normalizados según el estándar de la industria definido?
> - [ ] ¿El código sigue estrictamente la interfaz del SpecDD?
> - [ ] ¿Se han eliminado los ruidos técnicos detectados en la Phase Discovery?
