# Contrato de Datos (Data Contract)
> **Proyecto:** Flores_GM (Clasificador de Iris)
> **Versión:** 1.5.0 (Extreme Data SecOps & Idempotency Review)
> **Estado:** Aprobado Definitivo
> **Responsable:** ai-data-auditor
> **Trazabilidad:** feasibility.md → specdd.md v2.5.0

---

## 1. Propósito del Contrato
Este documento establece las reglas **inquebrantables** de formato, completitud, validez biológica y consistencia multivariable que los datos deben cumplir. La premisa es **"Zero Trust" (Cero Confianza)**. Toda violación en Phase 2 (Engineering) o Phase 4 (Delivery) debe generar un *Fail-Fast* inmediato mediante excepciones claras en Python.

---

## 2. Reglas Globales de Calidad de Datos (EDQ)

1. **Ausencia Absoluta de Vacíos y Anomalías Numéricas:** 
   - **Tolerancia 0%** a valores nulos (`NULL`, `None`, cadenas vacías `""`).
   - **Prohibición explícita** de valores Not-a-Number (`NaN`) e Infinitos (`+Inf`, `-Inf`). La fila entera se rechaza.
2. **Precisión Numérica y Sanitización de Tipos:** Todas las variables predictivas ($X$) deben castearse explícitamente a `float64`. Antes del cast, se debe extraer cualquier sufijo de texto (ej. `"5.1 cm"` $\rightarrow$ `5.1`) utilizando expresiones regulares si es necesario.
3. **Estandarización Categórica y Estructural:** 
   - **Datos:** Strip de espacios en blanco (inicio y fin) y conversión a minúsculas (`lowercase`). Si la cadena resultante es vacía `""`, se considera nula y se rechaza.
   - **Esquema (Headers):** Las cabeceras del CSV Bronze deben ser forzadas a minúsculas y se les deben remover espacios antes de mapearlas al esquema Silver, para evitar fallos si la fuente envía `Sepal Length` en lugar de `sepal_length`.
4. **Clausura de Esquema (Drop Extra):** Descartar silenciosamente cualquier columna adicional no listada en el contrato Silver.
5. **Contrato de Codificación Estricta (Encoding):** Todo archivo de texto plano consumido por el pipeline debe procesarse forzosamente como `UTF-8` puro, con delimitador de coma `,`.
6. **Volumen Mínimo Vital (Minimum Viable Volume):** La limpieza estricta puede diezmar el dataset. Se exige que la Capa Silver retenga al menos el **80%** de los registros del dataset histórico original (mínimo 120 filas). Si cae por debajo, el pipeline colapsa por `Data Insufficiency Error`.

---

## 3. Resolución de Paradojas, Contradicciones e Idempotencia

La instrucción de "eliminar duplicados" en lenguajes como Python tiene fallos críticos relacionados con la precisión y la trazabilidad de los pipelines de datos modernos.

**Regla de Comparación de Flotantes:** Toda búsqueda de duplicados o contradicciones DEBE realizarse **después** de redondear temporalmente las 4 dimensiones a 1 decimal.

1. **Duplicados Exactos ($X_{redondeado}$ igual, $y$ igual):** Se mantiene una (1) sola instancia.
2. **Contradicciones Lógicas ($X_{redondeado}$ igual, $y$ distinto):** Se consideran datos corruptos y **AMBAS** filas deben ser eliminadas del dataset Silver.
3. **Idempotencia de Identificadores (UUID Hashing):** El `flower_id` **NO** puede ser un UUID4 completamente aleatorio, ya que esto rompería la idempotencia (ejecutar el pipeline dos veces generaría IDs distintos para las mismas flores). El `flower_id` DEBE generarse como un **Hash determinista (MD5 o SHA256)** de la concatenación de las 4 medidas redondeadas y la especie. Así, un mismo registro siempre tendrá el mismo ID sin importar cuántas veces se ingeste.

---

## 4. Esquemas por Capa (Layer Schemas)

### 4.1 Capa Bronze (Raw Data)
* **Ruta Esperada:** `data/Bronze/Iris.csv`
* **Formato Esperado:** CSV con cabeceras, delimitado por comas (`,`), codificación `UTF-8`. (Prohibido inferir separadores).

### 4.2 Capa Silver (Trusted Data)
Datos limpios, sin contradicciones lógicas y con IDs idempotentes.

* **Ruta:** `data/Silver/Iris_silver.parquet`
* **Formato:** Apache Parquet (Validación por `pyarrow.schema` obligatoria).
* **Esquema Unidimensional Estricto:**
    * `flower_id`: `string` (Hash MD5/SHA256 determinista).
    * `sepal_length`: `float64` ($0.1 \le x \le 15.0$).
    * `sepal_width`: `float64` ($0.1 \le x \le 15.0$).
    * `petal_length`: `float64` ($0.1 \le x \le 15.0$).
    * `petal_width`: `float64` ($0.1 \le x \le 15.0$).
    * `species`: `string` (Solo `"setosa"`, `"versicolor"`, `"virginica"`).
* **Restricciones Multivariables (Integridad Biológica Extrema):**
    * **Regla 1 (Pétalo):** `petal_length >= petal_width`. Es imposible un pétalo más ancho que largo.
    * **Regla 2 (Sépalo vs Pétalo):** `sepal_length >= petal_length`. En la especie Iris, el sépalo siempre es más largo o igual que el pétalo.
    * Filas que violen estas proporciones biológicas son errores de medición (ej. inputs invertidos) y DEBEN descartarse.

### 4.3 Capa Gold (Feature Store)
Datos robustecidos y listos para ML.

* **Ruta:** `data/Gold/Iris_gold.parquet`
* **Formato:** Apache Parquet.
* **Prohibición de Normalización (Anti-Leakage Contract):** Queda **ESTRICTAMENTE PROHIBIDO** aplicar Feature Scaling (Normalización/Estandarización) en la Capa Gold. El escalamiento debe ocurrir *exclusivamente* dentro del objeto `Pipeline` de Scikit-learn en la Fase 3, para evitar el *Training/Serving Skew*.
* **Esquema Estricto:**
    * Mantener campos de Silver, pero `species` (string) **DEBE** transformarse a `target` (integer).
* **Mapeo Inmutable del Target (Label Encoding Contract):**
    * `"setosa"` $\rightarrow$ `0`
    * `"versicolor"` $\rightarrow$ `1`
    * `"virginica"` $\rightarrow$ `2`
* **Regla de Augmentation (Determinista):** 
    1. Inyectar ruido Gaussiano ($\mu=0$, $\sigma$ de `config.py`) usando `RANDOM_SEED`.
    2. Recortar al rango biológico ($0.1 \le x \le 15.0$).
    3. Re-validar proporciones (`petal_length >= petal_width`, `sepal_length >= petal_length`).
    4. Redondear a 1 decimal y re-asegurar `float64`.
* **Guardia de Desbalance (Class Imbalance Guard):**
    * Tras todo el proceso, contar muestras por clase. Si alguna clase representa **menos del $30\%$**, lanzar `RuntimeError("Class Imbalance Detected")`.

---

## 5. Contrato de Inferencia (API Vector Contract)

* **Orden Inmutable del Tensor Numérico ($X$):**
  ```python
  X_inference = [[ sepal_length, sepal_width, petal_length, petal_width ]]
  ```
  La salida del modelo será un `integer` (`0, 1, 2`). La API usará el Mapeo Inmutable (Sección 4.3) a la inversa para devolver el nombre de la especie.

---
> **Validación Final:** Este contrato protege la integridad relacional de la base de datos (idempotencia) y aplica reglas de biología botánica para evitar que valores numéricamente "válidos" pero anatómicamente imposibles contaminen el entrenamiento y la inferencia.