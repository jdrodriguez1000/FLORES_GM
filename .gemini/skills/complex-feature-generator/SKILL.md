---
name: complex-feature-generator
description: Protocolo para la creación de variables predictivas de alto valor (Feature Engineering) mediante agregaciones, funciones de ventana y ratios complejos.
user-invocable: false
agent: ai-feature-store-architect
allowed-tools: [Read, Write, Edit, SQL, Python-Interpreter]
---

## 🏗️ I. Tipología de Variables a Generar
El agente debe diseñar el set de variables independiente ($X$) basándose en el dominio del problema:
1. **Agregaciones Temporales:** Creación de promedios móviles, conteos por periodos (7d, 30d, 90d) y tendencias (ej: % de cambio vs mes anterior).
2. **Funciones de Ventana (Window Functions):** Implementación de `LEAD`, `LAG`, `RANK` y acumulados por entidad (Customer/Product).
3. **Ratios y Composiciones:** Creación de variables que capturan relaciones de negocio (ej: `ratio_gasto_ingreso`, `frecuencia_compra_promedio`).

## 📐 II. Implementación Técnica en Capa Gold
1. **Modularización de Features:** Desarrollar scripts `.py` que permitan generar features de forma aislada para facilitar el re-uso.
2. **Cálculo Determinístico:** Asegurar que el cálculo sea idéntico tanto en el entrenamiento (Batch) como en la inferencia (Real-time).
3. **Escalabilidad del Cálculo:** Optimizar las consultas SQL o código Python para manejar volúmenes masivos de datos históricos.

## 🚀 III. Documentación de Features (Feature Store)
1. **Diccionario de Variables:** Definir el nombre, descripción, lógica de cálculo y unidad de medida de cada feature.
2. **Linaje Inter-Capa:** Documentar qué campos de la capa Silver fueron utilizados para construir cada una de las variables Gold.

---

> **Check de Certificación de Features:**
> - [ ] ¿Se han cubierto todas las hipótesis de negocio planteadas en la Phase Discovery?
> - [ ] ¿La lógica de cálculo es consistente para el entrenamiento y la producción?
> - [ ] ¿Se han evitado variables con baja varianza o excesivos ceros?
