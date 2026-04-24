# Backlog del Proyecto

> Generado automáticamente en el Bootstrap del repositorio.
> Fuente de verdad: GEMINI.md | Metodología: process.md

---

## FASE 1: Discovery — Línea Base Documental

**Entregable Principal:** Documentación de Gobernanza completa (config + BRD + Factibilidad + Mockup + SAD + SpecDD + Contract).

### Iteración 1.1: Configuración e Identidad del Proyecto

#### [F1-T01] Crear config.md
- **Responsable:** @config-manager
- **Iteración:** 1.1
- **Entregable:** `docs/references/config.md`
- **Acción:** Documentation
- **DoD:** El archivo existe con las 4 secciones mandatorias (Definición, Identidad, Estado, Fuentes). Todos los campos obligatorios tienen valor real (no placeholder).
- **Estado:** DONE

### Iteración 1.2: Documentación de Negocio

#### [F1-T02] Crear BRD (Business Requirements Document)
- **Responsable:** @ai-business-strategist
- **Iteración:** 1.2
- **Entregable:** `docs/governance/brd.md`
- **Acción:** Documentation
- **DoD:** BRD contiene objetivos de negocio, KPIs con thresholds definidos y criterios de aceptación verificables.
- **Estado:** TODO

### Iteración 1.3: Factibilidad y Diseño de Experiencia

#### [F1-T03] Ejecutar Análisis de Factibilidad
- **Responsable:** @ai-data-auditor
- **Iteración:** 1.3
- **Entregable:** `docs/Phase_discovery/feasibility.md`
- **Acción:** Documentation
- **DoD:** Reporte incluye diagnóstico de calidad de datos (completitud, distribución, outliers) y veredicto GO/NO-GO para continuar a Phase Engineering.
- **Estado:** TODO

#### [F1-T04] Crear Mockup de Interfaz
- **Responsable:** @ai-ux-designer
- **Iteración:** 1.3
- **Entregable:** `docs/Phase_discovery/mockup.md`
- **Acción:** Documentation
- **DoD:** Mockup aprobado por el Stakeholder principal. Cubre flujos principales de la aplicación.
- **Estado:** TODO

### Iteración 1.4: Arquitectura y Especificaciones Técnicas

#### [F1-T05] Crear SAD (Software Architecture Document)
- **Responsable:** @ai-solutions-architect
- **Iteración:** 1.4
- **Entregable:** `docs/governance/sad.md`
- **Acción:** Documentation
- **DoD:** SAD define el stack tecnológico, diagrama de arquitectura de 4 capas (Bronze/Silver/Gold/Model) y las interfaces entre componentes.
- **Estado:** TODO

#### [F1-T06] Crear SpecDD (Specification-Driven Development)
- **Responsable:** @ai-solutions-architect
- **Iteración:** 1.4
- **Entregable:** `docs/governance/specdd.md`
- **Acción:** Documentation
- **DoD:** SpecDD contiene las firmas de todas las funciones `.py` del pipeline, contratos de entrada/salida y criterios de aceptación técnicos por módulo.
- **Estado:** TODO

### Iteración 1.5: Contrato de Datos

#### [F1-T07] Crear Data Contract
- **Responsable:** @ai-data-auditor
- **Iteración:** 1.5
- **Entregable:** `docs/governance/contract.md`
- **Acción:** Documentation
- **DoD:** Contract define esquema de variables (tipo, rango, cardinalidad), reglas de validación matemáticas y criterios de rechazo de datos.
- **Estado:** TODO

---

## FASE 2: Engineering — Feature Set Certificado
> Tareas pendientes de atomización. Se poblará al completar Phase Discovery.

## FASE 3: Modeling — Modelo Predictivo Certificado
> Tareas pendientes de atomización. Se poblará al completar Phase Engineering.

## FASE 4: Delivery — Sistema en Producción
> Tareas pendientes de atomización. Se poblará al completar Phase Modeling.
