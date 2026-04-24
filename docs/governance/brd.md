# Business Requirements Document (BRD)
> **Proyecto:** Flores_GM (Clasificador de Iris)
> **Fase:** Phase 1 (Discovery)
> **Autor:** ai-business-strategist

## 1. Resumen Ejecutivo (Executive Summary)
El proyecto **Flores_GM** busca automatizar la clasificación de la especie de la flor de Iris (Setosa, Versicolor, Virginica) mediante el uso de un modelo predictivo de Machine Learning y una interfaz web intuitiva. Actualmente, la identificación botánica depende de inspecciones manuales que consumen tiempo y requieren un nivel de especialización. La aplicación web reducirá el tiempo de inferencia a milisegundos, democratizando y agilizando la catalogación para botánicos, investigadores y estudiantes.

## 2. Encuadre Técnico (ML Framing)
*   **Taxonomía de la Tarea:** Aprendizaje Supervisado - Clasificación Multi-clase.
*   **Variable Objetivo ($y$):** Especie de la flor (Categórica: *Setosa*, *Versicolor*, *Virginica*).
*   **Variables Predictivas ($X$):** 4 Dimensiones físicas continuas en centímetros (Longitud del sépalo, Ancho del sépalo, Longitud del pétalo, Ancho del pétalo).
*   **Disponibilidad de Datos:** Se cuenta con un dataset histórico estructurado y etiquetado (Gold Data) suficiente para el entrenamiento inicial del algoritmo.
*   **Frecuencia de Actualización (Data Drift):** El modelo es estático. No se requiere reentrenamiento en línea ni ingesta de datos en tiempo real, ya que la morfología de las especies de Iris no cambia a corto plazo.

## 3. Análisis de Valor y Retorno de Inversión (ROI)
*   **Baseline Actual:** Clasificación manual por expertos. Costo estimado de $2 por clasificación; precisión humana en torno al 90%.
*   **Impacto Esperado ("The Money Metric"):** 
    *   **Aumento de Productividad:** Ahorro proyectado de 20 horas mensuales de personal especializado.
    *   **Valor Monetario:** Asumiendo una tasa de $50/hora del experto, el ahorro es de ~$1,000 USD/mes. 
    *   **Restricción de Rentabilidad:** Para mantener un ROI positivo, el costo de infraestructura de la aplicación (hosting/inferencia) **no debe superar los $50 USD/mes**.

## 4. Arquitectura de Métricas y Criterios de Aceptación
Dado que las tres especies de Iris tienen la misma importancia en el catálogo botánico, los errores de clasificación tienen el mismo peso. Sin embargo, una clasificación incorrecta con *alta confianza* es más peligrosa que una duda informada.

| Métrica Técnica | Umbral de Aceptación (Threshold) | Justificación de Negocio |
| :--- | :--- | :--- |
| **Accuracy (Exactitud)** | $\geq 95\%$ | El modelo debe superar holgadamente el baseline humano (~90%) para justificar la adopción tecnológica. |
| **Macro F1-Score** | $\geq 0.94$ | Asegura que el modelo no tenga un sesgo oculto hacia una especie en particular. |
| **Latencia de Inferencia** | $< 200 \text{ ms}$ | Requerimiento de UX para la web. La predicción debe ser percibida como instantánea. |
| **Manejo de Incertidumbre** | Alerta si Confianza $< 70\%$ | El modelo debe retornar la distribución de probabilidad (Softmax). Si la probabilidad máxima es menor al 70%, el sistema debe sugerir "Revisión Manual" para mitigar el riesgo de error en casos ambiguos. |
| **Eficiencia Computacional** | Inferencia en CPU | El modelo debe ser lo suficientemente ligero para ejecutarse en CPUs estándar, cumpliendo la restricción de rentabilidad ($<50 USD/mes de infraestructura). |

## 5. Restricciones y Fuera de Alcance (Out-of-Scope)
*   **Exclusividad Botánica:** El modelo *solo* está entrenado para discernir entre las 3 especies de Iris. Si se introducen medidas de otra familia de plantas, el modelo forzará una predicción errónea.
*   **Tipo de Input:** El sistema no procesará imágenes (Computer Vision); depende estrictamente de la medición física manual (Tabular Data).
*   **Límites Biológicos:** El frontend debe rechazar entradas con medidas $\leq 0$ cm o $> 15$ cm (atípicas para el género Iris).
*   **Inferencia Parcial:** El sistema no realizará imputación de datos en tiempo real. Se requieren las 4 medidas obligatorias exactas para ejecutar la predicción.
*   **Almacenamiento de Predicciones:** La aplicación web será *stateless* (sin estado). No se guardará un historial de las predicciones realizadas por los usuarios en una base de datos para cumplir con normativas de simplicidad arquitectónica y costos.

## 6. Historias de Usuario (User Stories) Principales
1.  **Como** botánico o aficionado, **quiero** ingresar las medidas exactas del sépalo y pétalo en la web, **para** obtener instantáneamente la especie de Iris.
2.  **Como** administrador del sistema, **quiero** que la aplicación rechace dimensiones biológicamente imposibles (ej. negativas o nulas), **para** evitar predicciones basura (Garbage In - Garbage Out).
3.  **Como** usuario final, **quiero** visualizar el porcentaje de confianza de la predicción, **para** poder tomar una decisión informada en casos donde las características físicas se solapen.
4.  **Como** supervisor de calidad, **quiero** que el sistema me alerte si la predicción tiene una confianza baja (<70%), **para** poder derivar ese espécimen a una revisión botánica manual.

---
> **Estado de Aprobación:** Aprobado para avanzar a la redacción del *Contrato de Comportamiento (behavior.md)* y el *Análisis de Factibilidad*.