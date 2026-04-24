# GEMINI.md: Protocolos de Ingeniería de IA y Ciencia de Datos

Este archivo define las convenciones y protocolos agnósticos para Gemini CLI en proyectos de **Ciencia de Datos, Machine Learning e Ingeniería de IA**, siguiendo las metodologías **SpecDD** (Specification-Driven Development) y **TDD** (Test-Driven Development).

## Directrices de Comportamiento
* **LECTURA OBLIGATORIA:** Leer y comprender el archivo **[principles.md](docs/references/principles.md)**. Todas tus acciones, sugerencias y generación de código deben cumplir estrictamente con los principios definidos en este archivo.
* **PROTOCOLO DE PENSAMIENTO:** Antes de realizar cualquier cambio (usando `write_file` o `edit_file`), debes validar internamente que tu solución respeta los pilares de "Simplicidad Primero" y "Cambios Quirúrgicos" detallados en `principles.md`.
* **AUDITORÍA:** Si detectas que una instrucción del usuario contradice los principios (por ejemplo, pide sobre-ingeniería innecesaria), debes advertirlo antes de proceder.
* **VERIFICACIÓN:** Al finalizar una tarea, confirma brevemente que la solución es la mínima necesaria para resolver el problema, evitando abstracciones prematuras.

## Configuracion del proyecto y metodologia de trabajo

Para configuración específica del proyecto actual (nombre, stack, IDs), consulta siempre **[docs/references/config.md](docs/references/config.md)**.

Seguir la metodologia de trabajo para proyectos de ciencia de datos y machine learning, qeu se encuentra en el archivo **[process.md](docs/methodology/process.md)**.


## 🌐 Fuentes de Verdad Vivas (Live Context)

Para garantizar la paridad técnica con las versiones más recientes y eliminar el "vibecoding", consulta las URLs documentadas en **[docs/references/sources.md](docs/references/sources.md)**.

**Directiva de Uso:** Accede y procesa estas URLs **ÚNICAMENTE** cuando la tarea implique escribir o modificar código relacionado con las tecnologías allí listadas. Si detectas discrepancias entre tu memoria interna y el contenido de estas URLs, la URL siempre prevalece como Fuente de Verdad.


---

## 🎯 1. Directivas Fundamentales

### Mentalidad de Auditor (Devil's Advocate)
Cuestiona proactivamente la factibilidad de los datos, la lógica de los KPIs y la arquitectura del modelo. **Rechaza tareas sin criterios de aceptación técnicos (Thresholds) definidos.** 

### Soberanía Documental (SpecDD + BDD)
El código productivo (`.py`) es un reflejo estricto de la especificación técnica. **Cada línea de código debe ser trazable hacia el BRD (Ver detalle de la jerarquía en la Sección 4).** Se prohíbe la improvisación de lógica de limpieza o modelado fuera del flujo documentado.

### Separación de Entornos (Notebook vs. Production)
*   **Notebooks (`notebooks/`):** Espacio de R&D, descubrimiento y descarte de hipótesis algorítmicas. No requieren código "grado producción" pero deben ser legibles y estar documentados.
*   **Módulos (`src/`):** Código industrializado, modular, con tipado estricto y siguiendo los contratos del SpecDD. **Solo el código en `src/` entra en el pipeline de despliegue.**

### Cadena de Confianza y Linaje
Toda transformación de datos y entrenamiento de modelos debe ser reproducible. Se debe mantener el linaje: **VERSIÓN DE DATOS (Gold) → VERSIÓN DE CÓDIGO (src) → VERSIÓN DE MODELO (models).**

### Higiene del Entorno Técnico
*   **Versión:** Uso obligatorio de **Python 3.12+**.
*   **Aislamiento:** Todo desarrollo debe realizarse dentro de un **Ambiente Virtual** (`venv` o `conda`).
*   **Dependencias:** El archivo **`requirements.txt`** es la única fuente de verdad para librerías. Debe actualizarse inmediatamente al instalar nuevas dependencias.
*   **Portabilidad:** Se prohíbe el uso de rutas absolutas. Todo enlace en documentos y toda referencia en el código debe utilizar **rutas relativas** respecto a la raíz del proyecto para garantizar la movilidad total del repositorio.

---

## 📂 2. Estándar de Almacenamiento y Gobernanza

Para garantizar la organización y trazabilidad, se sigue esta jerarquía de carpetas obligatoria:

| Directorio                | Propósito                                   | Regla de Oro                                                                                                                                                       |
| :------------------------ | :------------------------------------------ | :----------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `docs/`                   | Documentación técnica y de negocio oficial. | Segmentado por fases (`Phase_discovery` a `Phase_delivery`).                                                                                                       |
| `docs/[[design]]-system/` | Sistema de diseño del cliente (Brand).      | **Fuente de verdad de UI.** Obligatorio leer antes de generar cualquier interfaz. Contiene tokens de color, tipografía, reglas de componentes y referencia visual. |
| `src/`                    | Código fuente productivo (.py).             | Modularizado según el [[SAD]] (Ingesta, Modelado, API).                                                                                                            |
| `data/`                   | Almacenamiento de datos.                    | Estructura Bronze (crudo), Silver (limpio), Gold (features).                                                                                                       |
| `models/`                 | Artefactos de modelos serializados.         | Solo modelos certificados (ONNX, Pickle, Joblib).                                                                                                                  |
| `notebooks/`              | Investigación y experimentación.            | Archivos `.ipynb` documentados y numerados.                                                                                                                        |
| `tests/`                  | Suite de pruebas técnicas.                  | Unit, Integration, E2E y Model QA.                                                                                                                                 |
| `infra/`                  | Infraestructura como Código (IaC).          | Scripts de Docker, Terraform o K8s.                                                                                                                                |

---

## 📝 3. Documentos de Gobernanza

| Documento        | Ubicación                 | Propósito                                                                                           |
| :--------------- | :------------------------ | :-------------------------------------------------------------------------------------------------- |
| **BACKLOG**      | `docs/governance/`        | Orquestación de tareas (Fases > Iteraciones > Tareas).                                              |
| **BRD**          | `docs/governance/`        | Business Requirements Document: Objetivos y KPIs.                                                   |
| **[[SAD]]**      | `docs/governance/`        | Software Architecture Document: Stack y Diseño técnico.                                             |
| **SpecDD**       | `docs/governance/`        | Especificación de Interfaces: Contratos y firmas `.py`.                                             |
| **BEHAVIOR**     | `docs/governance/`        | Contrato de Comportamiento BDD: Escenarios Gherkin por User Story. Fuente de verdad para tests E2E. |
| **CONTRACT**     | `docs/governance/`        | Contrato de Datos: Validaciones matemáticas de variables.                                           |
| **FEASIBILITY**  | `docs/Phase_discovery/`   | Reporte de Factibilidad: Diagnóstico de salud de datos.                                             |
| **EDAs**         | `docs/Phase_engineering/` | Reportes de Ingesta, Limpieza y Análisis Estadístico.                                               |
| **MODEL QA**     | `docs/Phase_modeling/`    | Validación de Modelos: Benchmarking y Sesgo.                                                        |
| **QA SYSTEM**    | `docs/Phase_delivery/`    | Certificados E2E y Stress Testing.                                                                  |
| **[[HANDOFF]]**  | `docs/references/`        | Estado Operativo diario (sobrescribible).                                                           |
| **DECISIONS**    | `docs/references/`        | Log histórico de decisiones y lecciones (incluye referencias a CCs).                                |
| **CHANGES (CC)** | `docs/changes/`           | Fichas formales de Control de Cambios. Una ficha por CC-ID.                                         |

---

## 🧪 4. Ciclo de Desarrollo: SpecDD + BDD + TDD

La metodología opera en tres capas de especificación que se complementan:

| Capa       | Documento                     | Pregunta que responde                                       |
| :--------- | :---------------------------- | :---------------------------------------------------------- |
| **SpecDD** | `docs/governance/SpecDD.md`   | ¿Qué hace cada función? (contratos e interfaces)            |
| **BDD**    | `docs/governance/behavior.md` | ¿Cómo se comporta el sistema con ejemplos reales? (Gherkin) |
| **TDD**    | `tests/`                      | ¿El código es correcto para cumplir los dos anteriores?     |

Todo desarrollo sigue el ciclo **Red-Green-Refactor-Certificación-Validación**:

1.  **RED (Test Fallido):** El `ai-data-qa-engineer` escribe el test basado en el escenario Gherkin del `behavior.md` y el contrato del `SpecDD`. El test falla porque el módulo no existe aún.
2.  **GREEN (Funcionalidad):** Escribir código mínimo para pasar el test.
3.  **REFACTOR (Calidad):** Optimización técnica y cumplimiento del linaje.
4.  **CERTIFICACIÓN (Técnica):** Validación contra [[SAD]] & SpecDD.
5.  **VALIDACIÓN (Negocio):** Verificación final contra el BRD, behavior.md y KPIs.

---

## 🏗️ 5. Protocolo de Control de Cambios (CC)

Obligatorio cuando se detecta una desviación de los documentos de gobernanza. Se ejecuta a través del agente **`ai-change-manager`**.

### Paso 1 — `evaluate_drift` (Evaluación de Deriva)
Parada inmediata del código. Comparar la intención del cambio contra el **[[SAD]]** y el **SpecDD**. Identificar qué secciones exactas de la documentación quedarían obsoletas.

### Paso 2 — `generate_cc_proposal` (Ficha de CC)
Generar la propuesta en el chat con los siguientes campos:
- **CC-ID:** Identificador único correlativo.
- **Cambio:** Descripción técnica clara de lo que se modifica.
- **Justificación:** Por qué es necesario o mejor el cambio.
- **Impacto:** Lista de documentos afectados con sección exacta.

### Paso 3 — Autorización
Esperar **"APROBADO"** explícito del usuario. Sin esta confirmación, no se ejecuta ningún cambio.

### Paso 4 — `execute_approved_change` (Efecto Cascada)
1. Actualizar los archivos de gobernanza (`.md`) afectados.
2. Crear la ficha formal en **`docs/changes/CC-<ID>.md`**.
3. Registrar una referencia al CC-ID en **`docs/references/decisions.md`**.
4. Emitir un **Token de Continuidad** al agente original para que retome el trabajo con la nueva especificación vigente.

### Reglas Técnicas
- **Atomicidad:** Un CC trata un único cambio o grupo de cambios altamente relacionados. No mezclar cambios de negocio con refactores técnicos.
- **Rollback Mental:** Si el usuario rechaza el CC, sugerir la alternativa más cercana a la documentación original sin romper el sistema.

---

## 🌳 6. Protocolo de Gestión de Versiones (Git)

### Soberanía de Ramas
*   `main` / `dev` protegidas. PR obligatorio.
*   `feat/F[1-4]-<nombre>`: Nuevas funcionalidades.
*   `fix/F[1-4]-<nombre>`: Correcciones técnicos/datos.

### Commits Semánticos (Foco DS/ML)
*   `feat(data):` Transformaciones en Bronze/Silver/Gold.
*   `feat(model):` Ajustes en entrenamiento o arquitectura.
*   `test(qa):` Validaciones de datos y tests unitarios.
*   `docs(f-X):` Cambios en documentación de la fase X.

---

## ⚙️ 7. Escuadrón de Agentes Especializados

El proyecto opera con un escuadrón de agentes especializados por fase y rol. El catálogo completo — incluyendo triggers, skills asignados y cuándo invocar cada agente — se encuentra en **[agents.md](docs/references/agents.md)**.

---

## 🕒 8. Rituales de Sesión

### Ritual de Apertura (Session Kickoff)
1. **Contexto y Alineación Obligatoria:** 
   - **Leer** `[[handoff]].md`, `decisions.md` y `backlog.md` para reconstruir el contexto completo de la sesión anterior.
   - **Alineación Mental:** Configurar el comportamiento y APLICAR ESTRICTAMENTE los lineamientos de `docs/references/principles.md` (según se exige en las Directrices de Comportamiento).
2.  **Priorización:** Seleccionar la siguiente tarea atómica pendiente del Backlog.

> **Nota:** NotebookLM no se consulta en apertura. Es una base de conocimiento profundo que se consulta **a demanda**, cuando los documentos operativos no son suficientes para responder una pregunta específica.

### Ritual de Cierre (Session Wrap-up)
1.  **Validación:** Asegurar que todos los tests estén en verde antes de cerrar.
2.  **Documentación (`ai-session-steward`):** Actualizar `handoff.md` (Logros, Pendientes, Bloqueos) y registrar en `decisions.md` (Decisiones, Lecciones aprendidas).
3.  **Sincronización del Cerebro (`notebooklm`):** Llevar al cerebro del proyecto en NotebookLM: `[[handoff]].md`, `decisions.md` y las fichas nuevas o modificadas en `docs/changes/`.
4.  **Versionado (`ai-repository-governor`):** Hacer commit semántico y subir los cambios al repositorio actual.
