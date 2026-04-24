---
name: specdd-interface-definer
description: Protocolo para la definición técnica de interfaces de software (SpecDD) que garantiza la interoperabilidad modular y el cumplimiento de TDD.
user-invocable: false
agent: ai-solutions-architect
allowed-tools: [Read, Write, Edit]
---

## 🏗️ I. Definición de Contratos de Interfaz
Por cada módulo `.py` definido en el SAD, el agente debe establecer:
1. **Definición de Firmas:** Especificar nombres de funciones, parámetros (con tipos) y valores de retorno.
2. **Manejo de Excepciones:** Definir qué errores debe lanzar cada componente y cómo deben ser capturados por la capa superior.
3. **Contratos de Comunicación:** Establecer cómo se comunicará la Phase Engineering con la 3 (ej: archivos Parquet) y la 3 con la 4 (ej: JSON via FastAPI).
4. **Documentación Obligatoria:** El SpecDD debe guardarse en `docs/governance/specdd.md`.

## 📐 II. Protocolo SpecDD (Specification-Driven Development)
Antes de que un ingeniero comience el desarrollo, el Architect debe entregar el "blueprint":
1. **Interfaces de Entrada:** Datos exactos que el módulo espera recibir.
2. **Post-condiciones:** El estado garantizado del sistema tras la ejecución del módulo.
3. **Comportamiento Mock:** Instrucciones sobre cómo simular (mockear) el módulo para que otros agentes puedan trabajar en paralelo sin dependencias reales.

## 🚀 III. Estándares de Ingeniería y Calidad
1. **Linting & Formatting:** Definir las reglas de estilo (ej: PEP 8, Black, Isort).
2. **Estructura de Logs:** Establecer el formato de telemetría (mínimo: timestamp, level, module, message, metadata).
3. **Puntos de Inyección:** Diseñar cómo se inyectarán las dependencias (ej: modelos, conexiones a DB) para facilitar las pruebas unitarias.

---

> **Check de Certificación SpecDD:**
> - [ ] ¿Cada archivo .py tiene definida su interfaz antes de ser creado?
> - [ ] ¿Las definiciones permiten que el agente de QA escriba los tests sin ver el código interno?
> - [ ] ¿Se han definido tipos personalizados (TypedDict, Pydantic) para datos complejos?
