---
name: strict-schema-validator
description: Protocolo para la implementación de validaciones de esquema estrictas utilizando Pydantic y Great Expectations para garantizar la salud de los datos.
user-invocable: false
agent: ai-data-qa-engineer
allowed-tools: [Read, Write, Edit, Python-Interpreter]
---

## 🏗️ I. Validación de Esquemas (Pydantic)
El agente debe implementar clases de validación en tiempo de ejecución:
1. **Schema Check:** Validar nombres de campos y tipos de datos en la entrada de cada módulo.
2. **Custom Validators:** Implementar validaciones complejas (ej: "si la columna A existe, la B no puede ser nula").
3. **Manejo de Errores de Esquema:** Definir excepciones claras que detengan el pipeline ante inconsistencias graves.

## 📐 II. Validaciones Estadísticas (Great Expectations)
Implementar expectativas sobre el contenido de los archivos:
1. **Table-level Expectations:** Verificar conteo de filas y presencia de columnas.
2. **Column-level Expectations:** Definir rangos aceptables (`expect_column_values_to_be_between`), formatos de fecha y unicidad.
3. **Profiling Automático:** Generar reportes visuales de cumplimiento para cada carga de datos.

## 🚀 III. Integración en el Pipeline
1. **Checkpointing:** Definir puntos de control donde el pipeline debe morir si las expectativas no se cumplen.
2. **Data Docs:** Publicar los resultados de las validaciones en un formato legible para el resto del equipo.

---

> **Check de Certificación de Esquema:**
> - [ ] ¿Se han definido expectativas para todas las columnas de la Capa Gold?
> - [ ] ¿El validador de Pydantic está integrado en el punto de entrada de la API?
> - [ ] ¿Se disparan alertas automáticas cuando un esquema es violado?
