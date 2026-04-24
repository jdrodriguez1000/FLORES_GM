# Behavior Specifications (BDD Contract)
## Proyecto: Flores_GM (Clasificador de Iris)

> **Documento:** Contrato de Comportamiento BDD
> **Version:** 1.0.0
> **Estado:** Aprobado
> **Fecha:** 2026-04-24
> **Autor:** ai-business-strategist
> **Trazabilidad:** BRD v1 → behavior.md v1 → SpecDD v1
> **Fuente de verdad para:** ai-data-qa-engineer (Phase Engineering) · ai-full-stack-sdet (Phase Delivery)

---

## Jerarquía de Especificación

BRD (Intención) → BDD (Comportamiento) → TDD (Corrección)

---

## Feature: Predicción de Especies de Iris mediante Web UI

### US-01: Obtención de predicción instantánea
**Como** botánico o aficionado, **quiero** ingresar las medidas exactas del sépalo y pétalo en la web, **para** obtener instantáneamente la especie de Iris.

```gherkin
Escenario: Clasificación exitosa de una especie Iris Setosa (Happy Path)
  Dado que el usuario navega a la página de predicción
  Y el modelo está cargado en el backend
  Cuando el usuario ingresa "Longitud Sépalo" = 5.1
  Y el usuario ingresa "Ancho Sépalo" = 3.5
  Y el usuario ingresa "Longitud Pétalo" = 1.4
  Y el usuario ingresa "Ancho Pétalo" = 0.2
  Y hace clic en el botón "Clasificar"
  Entonces la interfaz debe mostrar la especie "Setosa"
  Y el tiempo de respuesta debe ser menor a 200 ms
```

### US-02: Rechazo de dimensiones biológicamente imposibles
**Como** administrador del sistema, **quiero** que la aplicación rechace dimensiones biológicamente imposibles (ej. negativas o nulas), **para** evitar predicciones basura (Garbage In - Garbage Out).

```gherkin
Escenario: Rechazo de medidas negativas o nulas (Límite Inferior)
  Dado que el usuario navega a la página de predicción
  Cuando el usuario ingresa "Longitud Sépalo" = 0.0
  Y el usuario ingresa el resto de medidas válidas
  Y hace clic en el botón "Clasificar"
  Entonces el sistema no debe llamar al modelo predictivo
  Y la interfaz debe mostrar un mensaje de error indicando "La medida debe ser estrictamente mayor a 0 cm y menor o igual a 15 cm"
  Y el formulario debe permanecer habilitado para permitir la corrección del dato

Escenario: Rechazo de medidas biológicamente gigantes (Límite Superior)
  Dado que el usuario navega a la página de predicción
  Cuando el usuario ingresa "Longitud Pétalo" = 15.1
  Y el usuario ingresa el resto de medidas válidas
  Y hace clic en el botón "Clasificar"
  Entonces el sistema no debe llamar al modelo predictivo
  Y la interfaz debe mostrar un mensaje de error indicando "La medida debe ser estrictamente mayor a 0 cm y menor o igual a 15 cm"
  Y el formulario debe permanecer habilitado para permitir la corrección del dato

Escenario: Prevención de Inferencia Parcial (Campos Vacíos)
  Dado que el usuario navega a la página de predicción
  Cuando el usuario ingresa solo 3 de las 4 medidas requeridas
  Y el campo "Ancho Pétalo" queda vacío
  Y hace clic en el botón "Clasificar"
  Entonces el botón debe estar deshabilitado o el sistema no debe llamar al modelo
  Y la interfaz debe marcar el campo "Ancho Pétalo" como obligatorio
```

### US-03: Visualización del porcentaje de confianza
**Como** usuario final, **quiero** visualizar el porcentaje de confianza de la predicción, **para** poder tomar una decisión informada en casos donde las características físicas se solapen.

```gherkin
Escenario: Presentación de la distribución de probabilidad
  Dado que el modelo ha clasificado exitosamente un espécimen
  Cuando el sistema retorna la predicción principal "Virginica"
  Entonces la interfaz debe mostrar una tabla o gráfico de confianza
  Y la tabla debe mostrar "Virginica: 92%", "Versicolor: 7%", "Setosa: 1%"
  Y la suma de todas las probabilidades mostradas debe ser exactamente 100%
```

### US-04: Alerta de confianza baja para revisión botánica
**Como** supervisor de calidad, **quiero** que el sistema me alerte si la predicción tiene una confianza baja (<70%), **para** poder derivar ese espécimen a una revisión botánica manual.

```gherkin
Escenario: Predicción ambigua requiere revisión manual (Baja Confianza)
  Dado que el usuario ingresa medidas que se solapan biológicamente
  Cuando el modelo retorna la predicción principal con una confianza de "65%"
  Entonces la interfaz debe mostrar la predicción principal
  Y debe mostrar una advertencia visual destacada (ej. color amarillo/rojo) que diga "Baja Confianza: Se sugiere Revisión Manual"
```

---

## Trazabilidad BDD → Criterios de Aceptación

| Escenario | US Origen | CA Relacionados |
| :--- | :--- | :--- |
| Clasificación exitosa de una especie Iris Setosa | US-01 | Accuracy, Latencia de Inferencia |
| Rechazo de medidas negativas o nulas (Límite Inferior) | US-02 | Input Constraints (0 a 15 cm) |
| Rechazo de medidas biológicamente gigantes (Límite Superior) | US-02 | Input Constraints (0 a 15 cm) |
| Prevención de Inferencia Parcial (Campos Vacíos) | US-02 | Input Constraints (No imputación) |
| Presentación de la distribución de probabilidad | US-03 | Output de Confianza (Softmax) |
| Predicción ambigua requiere revisión manual | US-04 | Manejo de Incertidumbre (<70%) |

---

## Nota de Alineación con SpecDD
Pendiente de elaboración del documento `SpecDD.md` por parte del **AI Solutions Architect**. El tipo de retorno de la función predictiva *deberá* obligatoriamente incluir un diccionario o array con las probabilidades de las tres clases para poder cumplir con US-03 y US-04.