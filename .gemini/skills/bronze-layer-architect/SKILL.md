---
name: bronze-layer-architect
description: Protocolo para la implementación de la capa de almacenamiento Bronze, garantizando la inmutabilidad de los datos y el rastreo de linaje.
user-invocable: false
agent: ai-data-engineer
allowed-tools: [Read, Write, Edit, Bash]
---

## 🏗️ I. Implementación de Inmutabilidad
La capa Bronze es el reflejo exacto de la fuente. El agente debe:
1. **Raw Storage:** Guardar los datos en su formato original o en formatos serializados eficientes (Avro/Parquet) sin aplicar transformaciones de negocio.
2. **Estructura de Particionamiento:** Organizar los datos por tiempo de ingesta (`year=YYYY/month=MM/day=DD/hour=HH`) para facilitar re-procesamientos.
3. **Write-Once Policy:** Garantizar que los archivos en Bronze nunca se sobrescriban; nuevos datos se agregan como nuevas particiones o archivos.

## 📐 II. Gestión de Metadatos y Linaje (Lineage)
Por cada lote de datos, se deben adjuntar campos técnicos obligatorios:
1. `_ingested_at`: Marca de tiempo UTC de la ingesta.
2. `_source_system`: Identificador del sistema de origen.
3. `_ingestion_id`: UUID único para rastrear el proceso de carga.
4. `_file_origin`: Ruta o nombre del archivo original.

## 🚀 III. Gestión de Histórico
1. **Snapshotting vs Delta:** Decidir si se guarda una foto completa (Snapshot) o solo los cambios (Incremental) según lo definido en el SAD.
2. **Retención de Datos:** Aplicar las políticas de archivado definidas para mantener el almacenamiento optimizado.

---

> **Check de Certificación Bronze:**
> - [ ] ¿Los datos están libres de transformaciones de lógica de negocio o limpieza profunda?
> - [ ] ¿El linaje permite rastrear el dato hasta su fuente exacta en caso de error?
> - [ ] ¿Se ha verificado que no hay pérdida de precisión en tipos numéricos durante el guardado?
