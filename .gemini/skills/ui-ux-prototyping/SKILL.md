---
name: ui-ux-prototyping
description: Protocolo para la creación de interfaces premium no funcionales y validación de flujos de usuario (Mockups).
user-invocable: false
agent: ai-ux-designer
allowed-tools: [generate_image, Read, Write, Edit, Bash]
---

# Skill: Prototipado UI/UX

Esta habilidad permite al equipo validar la "cara" del proyecto antes de construir el "cerebro" (IA) utilizando técnicas de prototipado rápido.

## Funciones

### 1. Concepción de Interfaz (Visual Concept)
**Acción:** `generate_visual_concept`
- Utiliza la herramienta `generate_image` para crear conceptos estéticos rápidamente. No es necesario que sean finales, solo deben transmitir la "vibra" del producto.

### 2. Construcción de Prototipo "Smoke and Mirrors"
**Acción:** `build_rapid_prototype`
- Crea archivos HTML/CSS ultra-ligeros.
- **Regla del Dato Quemado:** Todos los gráficos, tablas y textos deben ser estáticos (Hardcoded). Prohibido intentar conectar con archivos JSON o CSV externos para ahorrar tiempo.
- Usa placeholders de alta calidad si es necesario.

### 3. Mapeo de UI a Requerimientos
**Acción:** `map_ui_to_backlog`
- Identifica qué elementos visuales aprobados requieren lógica compleja en las fases siguientes y notifica al **Backlog Manager**.

## Reglas Técnicas
- **Design System First:** Antes de generar cualquier HTML, verificar si existe `docs/design-system/`:
  - **Existe:** Leer `DESIGN.md` y extraer el bloque `tailwind.config` de `code.html`. Aplicar como restricciones absolutas.
  - **No existe:** Preguntar al usuario — *"¿Deseas definir colores y fuente corporativa antes de prototipar? (color primario, fondo, texto, fuente). Si no, aplico estética premium por defecto."* Crear `docs/design-system/DESIGN.md` si el usuario responde, o continuar con defaults si omite.
- **Tokens son Ley:** Los colores, tipografías y border-radius del `code.html` son restricciones absolutas. No sustituir por valores propios.
- **Referencia Visual:** Si existe `docs/design-system/screen.png`, usarla como criterio de aceptación visual.
- **Velocidad sobre Perfección:** Un mockup al 80% visualmente atractivo hoy es mejor que uno al 100% perfecto la próxima semana.
- **Interactividad Simulada:** Usa animaciones CSS simples para dar sensación de vida sin escribir JavaScript pesado.
- **Aislamiento:** El código del Mockup reside solo en `mockup/` (raíz del proyecto).
