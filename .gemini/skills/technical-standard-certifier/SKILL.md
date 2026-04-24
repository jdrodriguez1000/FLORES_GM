---
name: technical-standard-certifier
description: Protocolo para la auditoría técnica de código, gestión de logs y cumplimiento del Software Architecture Document (SAD) y SpecDD.
user-invocable: false
agent: ai-data-qa-engineer
allowed-tools: [Read, Write, Bash, Edit]
---

## 🏗️ I. Auditoría de Estándares de Código
El agente debe certificar que el desarrollo sigue los lineamientos del Solutions Architect:
1. **Linter Compliance:** Validar que el código no tenga errores de estilo ni deudas técnicas detectables automáticamente.
2. **Review de SpecDD:** Confirmar que los módulos `.py` no hayan alterado las interfaces (firmas de funciones) aprobadas.
3. **Optimización Técnica:** Identificar bucles ineficientes o cuellos de botella obvios antes de la fase de certificación.

## 📐 II. Certificación de Logs y Excepciones
1. **Verificación de Telemetría:** Asegurar que cada módulo emita logs con el formato y nivel adecuado (INFO, WARN, ERROR).
2. **Auditability:** Confirmar que los errores críticos guardan suficiente contexto (metadata) para permitir el *debug* rápido.
3. **Manejo de Errores Silenciosos:** Garantizar que no se usen bloques `try-except` vacíos que oculten fallas de datos.

## 🚀 III. Sello de Calidad Final
1. **Checklist de Paso a Producción:** Verificar que los tests han pasado, el código está limpio y los contratos se cumplen.
2. **Generación de Artefacto QA:** Documento resumen de la salud técnica de la fase para el Solutions Architect.

---

> **Check de Certificación Técnica:**
> - [ ] ¿El código cumple con los estándares de linting definidos?
> - [ ] ¿Todos los módulos emiten logs rastreables?
> - [ ] ¿Se ha verificado que no existan excepciones sin manejar que puedan romper el pipeline?
