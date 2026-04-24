---
name: data-contract-architect
description: Protocolo para el diseño y cumplimiento de contratos de datos rígidos (Data Schemas) para asegurar la integridad en las fronteras entre módulos.
user-invocable: false
agent: ai-solutions-architect
allowed-tools: [Read, Write, Edit, SQL]
---

## 🏗️ I. Diseño del Esquema de Datos
El agente debe definir la estructura de los datos que fluyen entre fases (Bronze -> Silver -> Gold -> API):
1. **Definición de Esquemas:** Especificar tipos de datos, nulidad y llaves primarias/foráneas.
2. **Validaciones de Rango:** Establecer límites lógicos para variables numéricas (ej: edad entre 0 y 120).
3. **Control de Formato:** Asegurar estandarización de fechas (ISO-8601), strings y codificaciones.
4. **Documentación Obligatoria:** El Contrato de Datos debe guardarse en `docs/Phase_discovery/Data_Contract.md`.

## 📐 II. Protocolos de Validación Técnica
1. **Implementación de Pydantic/Marshmallow:** Creación de modelos de validación en código para el rechazo automático de peticiones mal formadas.
2. **Null Policy:** Definir por cada campo si acepta nulos (`Optional`) y cuál es el valor por defecto si aplica.
3. **Detección de Data Drift:** Establecer los parámetros base para que el sistema detecte cuando los datos de entrada en producción difieren estadísticamente de los de entrenamiento.

## 🚀 III. Diccionario de Datos Técnico
El contrato de datos debe consolidarse en un artefacto que incluya:
1. **Metadata de Campos:** Descripción, origen (Source), y destino (Sink).
2. **Sensibilidad (PII):** Etiquetado de datos sensibles que requieren tratamiento especial (Cifrado/Anonimización).
3. **Versión del Contrato:** Control de versiones del esquema para evitar roturas en despliegues concurrentes.

---

> **Check de Certificación de Contrato:**
> - [ ] ¿El esquema de entrada de la API coincide exactamente con el SpecDD del Backend?
> - [ ] ¿Se han definido mensajes de error claros cuando la validación del esquema falla?
> - [ ] ¿El contrato de datos es compartido y aceptado por el Data Engineer y el ML Engineer?
