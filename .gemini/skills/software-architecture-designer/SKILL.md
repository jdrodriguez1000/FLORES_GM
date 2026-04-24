---
name: software-architecture-designer
description: Protocolo para el diseño de la topología del sistema, selección del stack tecnológico y elaboración del Software Architecture Document (SAD) para aplicaciones de IA.
user-invocable: false
agent: ai-solutions-architect
allowed-tools: [Read, Write, Edit, Bash]
---

## 🏗️ I. Diseño de Topología y Patrones
El agente debe definir la estructura del sistema basándose en la complejidad y escalabilidad requerida:
1. **Selección del Patrón:** 
   - **Monolito Modular:** Para proyectos en etapa temprana o equipos pequeños, priorizando la cohesión.
   - **Microservicios/Micro-kernels:** Para sistemas que requieren escalabilidad independiente de módulos de inferencia.
   - **Arquitectura Hexagonal / Cebolla:** Obligatorio para desacoplar la lógica de negocio (modelos) de los detalles de infraestructura (APIs, DBs).
2. **Estructura del Proyecto:** Definir la jerarquía de carpetas siguiendo estándares de industria (ej: `src/`, `tests/`, `docs/`, `infra/`).

## 📐 II. Stack Tecnológico e Infraestructura
1. **Contenedorización (Docker):** Definir la estrategia de imágenes (Multi-stage builds) para optimizar el peso de las librerías de ML.
2. **Orquestación (Kubernetes/Compose):** Diseñar el despliegue de servicios concurrentes (Frontend, Backend, Model Serving, Celery Workers).
3. **Capa de Persistencia:** Determinar el uso de bases de datos según el tipo de dato:
   - **Relacionales (PostgreSQL):** Para metadatos, usuarios y logs estructurados.
   - **NoSQL / Vector DB:** Para almacenamiento de embeddings o datos no estructurados de alta velocidad.

## 🚀 III. Elaboración del Software Architecture Document (SAD)
El documento final debe seguir el **C4 Model** o similar, incluyendo:
1. **Selección del Stack Tecnológico:** Justificar la elección de lenguajes, frameworks y bases de datos según requisitos de latencia y volumen.
2. **Diagramación C4 Model:** Crear diagramas de Contexto, Contenedor y Componentes para visualizar la solución.
3. **Definición de Infraestructura:** Diseñar la topología (Cloud, On-premise, Híbrida) y servicios necesarios.
4. **Documentación Obligatoria:** El Software Architecture Document (SAD) debe guardarse en `docs/governance/sad.md`.

---

> **Check de Certificación Arquitectónica:**
> - [ ] ¿El diseño garantiza el desacoplamiento entre la lógica de ML (Phase Modeling) y la API (Phase Delivery)?
> - [ ] ¿Se ha definido una estrategia de escalabilidad para picos de inferencia?
> - [ ] ¿El SAD especifica claramente cómo se gestionarán los estados y la caché (Redis)?
