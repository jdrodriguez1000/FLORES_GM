---
name: backlog-orchestration
description: Protocolo para la gestión jerárquica del proyecto, atomización de especificaciones técnicas y aseguramiento del DoD.
user-invocable: false
agent: ai-backlog-manager
allowed-tools: [Read, Write, Edit, Bash]
---

# Skill: Orquestación de Backlog

Esta habilidad permite transformar el plan maestro en acciones ejecutables, asegurando la trazabilidad total del proyecto.

## Funciones

### 1. Inicialización de Roadmap (Bootstrap)
**Acción:** `initialize_roadmap`
- Crea el archivo `docs/governance/backlog.md`.
- Registra las 4 Fases con su **Entregable Principal**:
    - **Phase Discovery (Discovery):** Linea Base Documental (SAD/SpecDD).
    - **Phase Engineering (Engineering):** Feature Set Certificado (Gold Layer).
    - **Phase Modeling (Modeling):** Modelo Predictivo Certificado.
    - **Phase Delivery (Delivery):** Sistema en Producción con Monitoreo.
- Divide las fases grandes en **Iteraciones** lógicas.

### 2. Atomización de SpecDD (TDD Mapping)
**Acción:** `atomize_specs_to_tasks`
- Escanea el `SpecDD` y el `Data Contract`.
- **Regla Mandatoria RED/GREEN:** Por cada componente definido, genera obligatoriamente dos ítems vinculados en el Backlog:
    1.  **Tarea [RED]:** Desarrollo de la Suite de Pruebas (Unit/Data Quality). El DoD es el fallo controlado del test.
    2.  **Tarea [GREEN]:** Implementación de la lógica productiva. El DoD es el paso exitoso de la Tarea [RED].
- Asigna el agente responsable (ej: `ai-data-engineer` para la lógica y `ai-data-sdet` para el test).

### 3. Gestión de Tareas (Task Management)
**Acción:** `manage_task_state`
- Actualiza el estado de las tareas (Block, In Progress, Done).
- Verifica el **DoD (Definition of Done)** antes de marcar como finalizada.
- Asegura que el responsable sea único por tarea.

## Estructura de Tarea en backlog.md
```markdown
### [ID] Título de la Tarea
- **Responsable:** @agente-nombre
- **Iteración:** X.Y
- **Entregable:** Nombre del Archivo/Componente
- **Acción:** [Coding | Testing | Documentation]
- **DoD:** Criterios específicos para marcar como hecho.
- **Estado:** [TODO | IN_PROGRESS | DONE]
```

## Reglas de Validación
- **Prohibido la multifunción:** Si una tarea dice "Desarrollar y Probar", debe ser rechazada y separada en dos tareas independientes.
- **Trazabilidad:** Toda tarea técnica debe citar la sección del SpecDD o SAD que la origina.
