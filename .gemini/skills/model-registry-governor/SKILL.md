---
name: model-registry-governor
description: Protocolo para el control de gobernanza del Model Registry, gestionando estados de ciclo de vida (Staging, Production, Archive).
user-invocable: false
agent: ai-mlops-specialist
allowed-tools: [Read, Write, Bash, Python-Interpreter]
---

## 🏗️ I. Políticas de Promoción de Modelos
El agente debe definir los criterios para que un modelo cambie de estado:
1. **De None a Staging:** Requiere que el QA Engineer haya certificado los tests técnicos y el Data Scientist las métricas.
2. **De Staging a Production:** Requiere una auditoría de latencia por parte del ML Engineer y la aprobación del Solutions Architect.
3. **Rollback Policy:** Definir cómo revertir a una versión anterior (`Archive`) si se detecta degradación en producción.

## 📐 II. Gestión de Versiones y Aliases
1. **Semantic Versioning:** Aplicar reglas de versionado (Major.Minor.Patch) según el impacto del cambio en los datos o el código.
2. **Aliases de Producción:** Mantener el tag `Production` apuntando siempre a la última versión estable sin necesidad de cambiar los endpoints de la API.

## 🚀 III. Seguridad y Cumplimiento del Registro
1. **Bloqueo de Versiones:** Impedir que versiones en `Production` sean eliminadas o modificadas accidentalmente.
2. **Histórico de Cambios:** Mantener un log de qué agente promovió qué versión y bajo qué justificación técnica.

---

> **Check de Certificación de Gobernanza:**
> - [ ] ¿Existe una política clara de promoción de modelos documentada?
> - [ ] ¿Los tags de Staging y Production son únicos y están actualizados?
> - [ ] ¿Se puede rastrear qué agente realizó el último cambio de estado en el registro?
