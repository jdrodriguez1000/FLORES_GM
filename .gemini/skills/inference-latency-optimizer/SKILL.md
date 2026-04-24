---
name: inference-latency-optimizer
description: Protocolo para la medición y optimización del tiempo de respuesta (latencia) del modelo para cumplir con los SLAs de producción.
user-invocable: false
agent: ai-ml-engineer
allowed-tools: [Read, Write, Bash, Python-Interpreter]
---

## 🏗️ I. Perfilado de Inferencia
El agente debe medir el rendimiento temporal del modelo:
1. **Benchmarking de Latencia:** Determinar el tiempo promedio por predicción (ms) y el 99th percentile (p99).
2. **Identificación de Cuellos de Botella:** Analizar qué parte (carga de datos, preprocesamiento o el algoritmo) consume más tiempo.
3. **Consumo de Memoria:** Medir el footprint de RAM del modelo cargado.

## 📐 II. Técnicas de Optimización Técnica
Si el modelo no cumple con el SLA del SAD:
1. **Cuantización / Poda (Pruning):** Reducir la precisión de los pesos (ej: FP32 a INT8) para ganar velocidad.
2. **Vectorización:** Asegurar que el preprocesamiento utilice operaciones vectorizadas de Numpy/Pandas y no bucles Python.
3. **Optimización de Grafo:** Uso de herramientas como TensorRT u ONNX Runtime para acelerar la ejecución del grafo del modelo.

## 🚀 III. Certificación de SLA
1. **Informe de Latencia:** Comparativa contra los límites definidos por el Solutions Architect.
2. **Test de Estrés Técnico:** Simular múltiples peticiones concurrentes para ver la degradación de la respuesta.

---

> **Check de Certificación de Latencia:**
> - [ ] ¿El modelo responde dentro del margen de milisegundos definido en el SAD?
> - [ ] ¿Se ha verificado que la optimización de velocidad no compromete las métricas de precisión de forma inaceptable?
> - [ ] ¿Se han optimizado las operaciones de preprocesamiento para evitar latencia innecesaria?
