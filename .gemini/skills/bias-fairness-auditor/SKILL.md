---
name: bias-fairness-auditor
description: Protocolo para la auditoría ética del modelo, detectando sesgos algorítmicos y garantizando la equidad (Fairness) en las predicciones.
user-invocable: false
agent: ai-model-qa-validator
allowed-tools: [Read, Write, Python-Interpreter]
---

## 🏗️ I. Identificación de Variables Protegidas
El agente debe identificar campos sensibles que no deben influir injustamente en la predicción:
1. **Atributos Sensibles:** (Género, edad, etnia, nivel socioeconómico) definidos en las políticas de ética de la empresa.
2. **Proxies de Sesgo:** Identificar variables correlacionadas con atributos protegidos (ej: código postal como proxy de nivel socioeconómico).

## 📐 II. Aplicación de Métricas de Equidad (Fairness Metrics)
1. **Demographic Parity:** Verificar si la probabilidad de una predicción positiva es la misma para diferentes grupos protegidos.
2. **Equal Opportunity:** Confirmar que los verdaderos positivos (True Positives) se distribuyen equitativamente entre los grupos.
3. **Disparate Impact Ratio:** Cuantificar la disparidad entre el grupo mayoritario y el minoritario.

## 🚀 III. Informe de Ética Algorítmica
1. **Diagnóstico de Sesgo:** Documentar si el modelo muestra favoritismo o perjuicio sistemático.
2. **Recomendación de Mitigación:** Si se detecta sesgo, proponer técnicas de re-balanceo de datos o ajuste de umbrales de decisión post-entrenamiento.

---

> **Check de Certificación de Ética:**
> - [ ] ¿Se han evaluado las métricas de equidad para todos los atributos protegidos?
> - [ ] ¿El modelo cumple con el umbral de "Fairness" del proyecto (ej: 80% Rule)?
> - [ ] ¿Se han identificado variables proxy que podrían estar introduciendo sesgo oculto?
