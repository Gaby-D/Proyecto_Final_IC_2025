# Cómo Funciona Random Forest para Predecir Series Temporales

## 1. Transformación de Serie Temporal a Problema de Regresión Supervisada

Random Forest es un algoritmo de aprendizaje supervisado diseñado para problemas de regresión y clasificación, no para series temporales directamente. Para usarlo en series temporales, necesitamos transformar el problema:

- **Se crean variables de rezago (lags)**: Se generan features que representan los valores anteriores de la serie para capturar la dependencia temporal.

Por ejemplo, si queremos predecir el valor de hoy (t), creamos variables como:
- `lag_1`: valor de hace 1 día (t-1)
- `lag_2`: valor de hace 2 días (t-2)
- `lag_3`: valor de hace 3 días (t-3)
- ... y así sucesivamente

Esto transforma la serie temporal en un problema de regresión supervisada donde cada fila tiene:
- **Features (X)**: Los valores de los lags (lag_1, lag_2, ..., lag_n)
- **Target (y)**: El valor actual que queremos predecir

## 2. Funcionamiento de Random Forest

Random Forest es un **ensemble** (combinación) de múltiples árboles de decisión:

- **Cada árbol** se entrena con un subconjunto aleatorio de los datos (método bootstrap)
- **En cada nodo** del árbol, selecciona aleatoriamente un subconjunto de features y elige el mejor umbral para dividir los datos
- **El crecimiento** se controla con parámetros como `max_depth`, `min_samples_split`, etc.
- **La predicción final** es el promedio (o votación) de todas las predicciones de los árboles individuales

## 3. Ventajas para Series Temporales

- **Captura relaciones no lineales**: Puede aprender patrones complejos entre los lags y el valor futuro
- **Maneja múltiples features**: Puede incorporar fácilmente variables exógenas (clima, eventos, etc.) además de los lags
- **Robusto al overfitting**: La aleatorización y el promedio de múltiples árboles reduce el riesgo de sobreajuste
- **No requiere normalización estricta**: Aunque se puede usar escalado, no es tan crítico como en otros modelos

## 4. Limitaciones y Consideraciones

- **No modela tendencias o estacionalidad automáticamente**: Si hay patrones estacionales, deben agregarse como features explícitas
- **No garantiza el orden temporal**: Aunque se usa división temporal para train/test, el modelo no "entiende" el concepto de tiempo
- **Dependencias de largo plazo limitadas**: Solo puede capturar patrones dentro de la ventana de lags que se definan

## 5. Proceso de Entrenamiento

1. **Preparación de datos**: Se crean las variables de rezago (lags) y se eliminan las filas con valores faltantes
2. **División temporal**: Se divide en conjunto de entrenamiento y prueba, manteniendo el orden temporal (no se mezclan aleatoriamente)
3. **Entrenamiento**: Cada árbol del bosque aprende patrones diferentes usando subconjuntos aleatorios de datos y features
4. **Predicción**: Para un nuevo punto, todos los árboles votan y se promedia su predicción

## 6. Predicción a Múltiples Pasos (Multi-step Forecasting)

Para predecir varios días hacia el futuro:

1. **Día 1**: Usa los últimos valores conocidos de la serie como lags para predecir el primer día futuro
2. **Día 2**: Usa la predicción del día 1 como parte de los lags para predecir el día 2
3. **Días siguientes**: Continúa el proceso, actualizando la ventana de lags con las predicciones anteriores

**Nota importante**: El error se acumula en cada paso porque se usan predicciones anteriores (que pueden tener error) como input para las siguientes predicciones.

## 7. Comparación con Otros Modelos

| Aspecto | Random Forest | MLP (Red Neuronal) | LSTM (Red Recurrente) |
|---------|---------------|---------------------|----------------------|
| Captura no linealidad | ✅ Sí | ✅ Sí | ✅ Sí |
| Orden temporal | Implícito (lags) | Implícito (lags) | Explícito (memoria) |
| Memoria de largo plazo | Limitada (ventana) | Limitada (ventana) | ✅ Mayor capacidad |
| Interpretabilidad | ✅ Alta (importancia) | ❌ Baja | ❌ Baja |
| Velocidad entrenamiento | ✅ Rápida | Media | Lenta |
| Manejo de features exógenas | ✅ Fácil | ✅ Fácil | Media |

## 8. Resumen

Random Forest es efectivo para series temporales cuando:

- Se transforma el problema usando variables de rezago (lags)
- Se tienen relaciones no lineales entre los valores pasados y futuros
- Se necesita interpretabilidad (importancia de features)
- Se requiere un modelo rápido y robusto
- Se dispone de variables exógenas adicionales (clima, eventos, etc.)

Es especialmente útil cuando los patrones temporales pueden capturarse mediante relaciones entre los valores recientes y el valor futuro, más que mediante dependencias de largo plazo o patrones estacionales complejos.

