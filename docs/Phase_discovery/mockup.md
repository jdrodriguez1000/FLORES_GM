# Documento de Aprobación de Mockup Visual
> **Proyecto:** Flores_GM (Clasificador de Iris)
> **Fase:** Phase 1 (Discovery)
> **Autor:** ai-ux-designer

## 1. Resumen de Diseño (Interactive Dashboard)
Se ha creado un prototipo visual interactivo ubicado en `mockup/index.html`. A diferencia del diseño estático anterior, este mockup incorpora sliders (controles de rango) para una entrada de datos más amigable, un menú lateral para anclaje espacial, y botones de estado en la parte superior para simular interactivamente los escenarios del BDD.

## 2. Alineación y Evolución Visual
Basado en la imagen de referencia proporcionada y las auditorías de UX, el diseño ha evolucionado para enfocarse en la **usabilidad del usuario final**:
*   **Identidad (Branding):** El proyecto se llama y se visualiza exclusivamente como "Flores_GM", eliminando enlaces irrelevantes (como Historial) para mantener un diseño "Simplicity First".
*   **Controles Deslizantes (Sliders):** Reemplazan los inputs de texto crudos, limitando físicamente el rango de entrada y reduciendo la fricción mental.
*   **Guía de Medición:** Se incorporó un diagrama visual en la barra lateral para educar al usuario sobre qué es exactamente un pétalo y un sépalo.
*   **Navegación de Estados:** Se añadió una barra superior (`Estado Inicial`, `Resultado Exitoso`, `Baja Confianza`, `Error de Validación`) para probar las UI sin necesidad de backend.
*   **Prevención de Errores (Input Locking):** Cuando el usuario está visualizando un resultado o error, los controles deslizantes se deshabilitan visual y funcionalmente (`opacity-50`, `cursor-not-allowed`) para evitar confusiones de lectura.
*   **Flujo Cíclico (State-Aware CTA):** El botón de acción es inteligente. En el estado inicial dice "Identificar Especie", pero una vez hecha la inferencia, se transforma en "Realizar nueva predicción" y reinicia el flujo limpiando la pantalla.

## 3. Validación Interactiva de Historias de Usuario (BDD)
El archivo HTML contiene lógica en JavaScript (Smoke and Mirrors) para demostrar visualmente las historias:

| User Story | Simulación en el Mockup |
| :--- | :--- |
| **US-01 (Happy Path)** | Haz clic en "Resultado exitoso". La pantalla se divide, el formulario se bloquea y el resultado verde ("Iris Setosa", 98% confianza) aparece. El botón permite regresar al inicio. |
| **US-02 (Validación de Límites)** | Haz clic en "Error de validación" (o desliza el Pétalo > 8.0cm). Aparece un mensaje rojo integrado en el formulario y los sliders se bloquean. |
| **US-03 (Confianza Softmax)** | En los estados de éxito o advertencia, se muestran las 3 barras de progreso sumando exactamente el 100%. |
| **US-04 (Alerta de Confianza)** | Haz clic en "Baja Confianza". Aparece un recuadro ámbar advirtiendo sobre el solapamiento estadístico (Virginica 65%, Versicolor 35%). |

## 4. Estado
> **Veredicto:** APROBADO.
> El diseño visual y el flujo de estados interactivo cumplen con el Design System, blindan los escenarios del Contrato de Comportamiento (BDD) y aplican fuertes principios de UX para el usuario final.