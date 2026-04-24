# Log de Decisiones y Aprendizajes

## [2026-04-24] — Sesión de Inicialización (Bootstrap)

### 🧩 Contexto
Arranque formal del proyecto Flores_GM y migración de protocolos desde el entorno anterior a Gemini CLI.

### 💡 Decisiones Clave
1. **Migración de Identidad:** Se reemplazaron todas las referencias a `.claude` y `CLAUDE.md` por `.gemini` y `GEMINI.md` para alinear el repositorio con el nuevo asistente de CLI.
2. **Estructura Industrial:** Se adoptó la estructura de 4 fases (Discovery, Engineering, Modeling, Delivery) para garantizar la trazabilidad del ciclo de vida de datos y ML.
3. **CI Temprano:** Se decidió implementar la GitHub Action de CI desde el día 0 para forzar la calidad del código desde el primer commit.

### 🎓 Aprendizajes (Learnings)
- **PowerShell vs Bash:** Se ajustaron los comandos de creación de directorios para compatibilidad con el entorno Win32 (`New-Item` en lugar de `mkdir -p`).
- **Gobernanza Automatizada:** El agente `repository-governor` es crítico para evitar "shaky structures" desde el inicio.

## [2026-04-24] — Cierre de Fase Discovery (Discovery Iteration 1.2-1.3)

### 💡 Decisiones Clave
1. **Identidad:** El sistema se llama oficialmente "Flores_GM". Se elimina cualquier referencia a "Flores AI" o funciones no solicitadas (Historial).
2. **UX Clínica:** Adoptado el diseño "The Clinical Sanctuary" (No bordes sólidos, Surface Nesting).
3. **Restricción de Infraestructura:** El costo operativo del sistema debe ser $< $50 USD/mes. Esto limita la arquitectura a inferencia en CPU y modelos ligeros (prohibido Deep Learning innecesario).
4. **Data Factibilidad:** Veredicto "GO CONDICIONADO". Obligatoriedad de Stratified K-Fold CV y Data Augmentation con ruido gaussiano para robustecer el modelo ante errores de medición humana en 2026.
5. **Arquitectura Web:** El frontend debe ser "stateless" (sin DB). Toda inferencia es inmediata, sin persistencia de historial.

