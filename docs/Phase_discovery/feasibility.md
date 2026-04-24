# Reporte de Factibilidad de Datos (Data Feasibility Report)
> **Proyecto:** Flores_GM (Clasificador de Iris)
> **Fase:** Phase 1 (Discovery)
> **Autor:** ai-data-auditor
> **Dataset:** Iris Botánico (Gold Data)

## 1. Inventario de Fuentes y Conectividad
*   **Fuente Principal:** Archivo estático / Dataset estructurado (CSV/DB Tabular).
*   **Origen Específico:** *Pendiente de ingesta física.* Se requiere materializar el dataset canónico de Fisher (1936) en `data/Bronze/` antes de iniciar la Phase Engineering.
*   **Volumetría Histórica:** 150 registros históricos.
*   **Frescura (Lineage):** Datos estáticos recolectados en 1936. No requieren actualización en tiempo real.

## 2. Análisis Exploratorio de Calidad (EDQ)

| Variable | Tipo Técnico | Estado de Salud (EDQ) | Nulos / Faltantes |
| :--- | :--- | :--- | :--- |
| `sepal_length` | Float (cm) | Válido. Distribución normal (aprox $4.3$ a $7.9$ cm). | 0% |
| `sepal_width` | Float (cm) | Válido. Distribución normal (aprox $2.0$ a $4.4$ cm). | 0% |
| `petal_length` | Float (cm) | Válido. Distribución bimodal (aprox $1.0$ a $6.9$ cm). | 0% |
| `petal_width` | Float (cm) | Válido. Distribución bimodal (aprox $0.1$ a $2.5$ cm). | 0% |
| `species` ($y$) | String | Válido. Balance perfecto (50 muestras por clase). | 0% |

### ⚠️ Hallazgos Críticos y Banderas Rojas (Red Flags):
1.  **Riesgo Crítico de Volumetría (Overfitting):** **150 registros es un volumen peligrosamente bajo** para un entorno industrial. Con un split estándar de 80/20, el set de prueba será de apenas 30 registros. Un (1) solo error baja el accuracy al $96.6\%$, y dos (2) errores lo hunden al $93.3\%$, fallando el BRD ($\geq 95\%$).
2.  **Riesgo de Sesgo de Medición (Measurement Drift):** El dataset original fue medido por expertos botánicos en 1936 utilizando instrumentos de alta precisión. Los usuarios de la aplicación web en 2026 serán "aficionados" (según US-01) midiendo con reglas caseras. Existe un riesgo gravísimo de **Data Drift por error humano**. El modelo se entrenará con datos de laboratorio pero inferirá sobre datos ruidosos.
3.  **Riesgo de Sesgo de Selección (Representatividad):** Las 150 flores originales fueron recolectadas en condiciones geográficas y climáticas específicas (Península de Gaspé, Canadá). No hay garantía estadística de que la distribución morfológica sea idéntica para especies de Iris cultivadas en invernaderos modernos o en otros continentes en 2026.
4.  **Riesgo de Solapamiento:** Existe un solapamiento estadístico natural entre *Versicolor* y *Virginica* en sus medidas de pétalo.
5.  **Integridad Estructural:** El dataset carece de un `id` único por flor, lo que rompe la trazabilidad.

## 3. Scorecard de Salud y Veredicto
*   **Scorecard de Calidad Estructural:** 10/10 (Cero nulos).
*   **Scorecard de Robustez (Volumen y Sesgo):** 2/10 (Riesgo altísimo de overfitting y sesgo de medición).
*   **Veredicto Final:** **GO CONDICIONADO**. 

*   **Condiciones Obligatorias para Continuar (Mitigación):**
    1.  **Stratified K-Fold CV:** El `ai-data-scientist` no puede usar *train_test_split* simple; la validación cruzada es obligatoria.
    2.  **Inyección de Ruido (Data Augmentation):** Durante la Phase Engineering, el `ai-feature-store-architect` deberá inyectar ruido gaussiano sintético a los datos de entrenamiento (simulando errores de medición de regla casera) para robustecer el modelo contra el "Measurement Drift" esperado en producción.
    3.  **Arquitecturas Simples:** Prohibido el uso de Deep Learning. Solo algoritmos con fuerte regularización.

## 4. Insumo para Contrato de Datos (SpecDD)
El `ai-solutions-architect` deberá configurar el validador estricto bajo estas reglas:
*   `rango_valido`: $> 0.0$ cm y $\leq 15.0$ cm.
*   `precision_decimal`: La UI debe forzar la entrada a máximo 1 decimal (ej. $5.1$), ya que así fue entrenado el modelo histórico. Entradas como $5.143$ deben ser redondeadas antes de la inferencia.
*   `imputacion`: Deshabilitada (Nulls = Reject).
*   `id`: Generar UUID sintético en capa Silver.

---
> **Acción Siguiente:** Con el Veredicto "GO CONDICIONADO", se habilita el diseño del Mockup Visual por parte del `ai-ux-designer` y la creación del SAD (Arquitectura), asumiendo la responsabilidad de implementar las mitigaciones de ruido en las fases posteriores.