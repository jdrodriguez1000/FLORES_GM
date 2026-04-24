---
name: technical-ingestion-profiler
description: Protocolo para la ejecución del EDA técnico post-ingesta, validando la integridad del transporte y la salud de los esquemas.
user-invocable: false
agent: ai-data-engineer
allowed-tools: [Read, Write, Bash, Python-Interpreter]
---

## 🏗️ I. Verificación de Integridad de Transporte
Inmediatamente tras la carga en Bronze, el agente debe validar:
1. **Verificación de Orígenes:** Validar la conexión y el formato de los datos crudos (Bronze).
2. **Perfilado de Integridad:** Identificar nulos, duplicados y tipos de datos inconsistentes.
3. **Análisis de Volumetría:** Evaluar si el volumen de datos coincide con los registros esperados del negocio.
4. **Documentación Obligatoria:** El reporte de EDA de Ingesta debe guardarse en `docs/Phase_engineering/EDA_Ingesta.md`.

## 📐 II. Perfilado de Esquema y Codificación
1. **Detección de Esquemas Rotos:** Identificar cambios inesperados en la estructura de la fuente (columnas nuevas, eliminadas o tipos cambiados).
2. **Encoding Check:** Validar que caracteres especiales se cargaron correctamente (UTF-8, Latin-1).
3. **Data Type Drift:** Alertar si una columna que era numérica ahora contiene texto en el 1% de los registros, provocando fallas de truncamiento.

## 🚀 III. Generación del Informe Técnico
El informe debe ser consumido por el **AI Analytics Engineer** antes de pasar a la Capa Silver:
1. **Resumen de Calidad Técnica:** (Pasa / Falla / Pasa con Advertencias).
2. **Alertas de Truncamiento:** Identificar si algún campo excedió el límite de longitud definido en el Contrato de Datos.

---

> **Check de Certificación de Ingesta:**
> - [ ] ¿Se ha detectado algún error de codificación que afecte la legibilidad del dato?
> - [ ] ¿El conteo de registros coincide entre origen y destino?
> - [ ] ¿Se ha generado la alerta automática al Analytics Engineer en caso de esquema roto?
