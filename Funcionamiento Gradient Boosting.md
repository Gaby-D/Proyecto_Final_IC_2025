# Cómo Funciona Gradient Boosting para Predecir Series Temporales

## 1. Transformación de Serie Temporal a Problema de Regresión Supervisada

Al igual que Random Forest, Gradient Boosting no maneja series temporales directamente. Requiere transformar el problema:

- **Se crean variables de rezago (lags)**: Se generan features que representan los valores anteriores de la serie para capturar la dependencia temporal.

- Se convierte la serie temporal en un problema de regresión supervisada donde cada fila tiene:
  - **Features (X)**: Los valores de los lags (lag_1, lag_2, ..., lag_n) y variables exógenas
  - **Target (y)**: El valor actual que queremos predecir

## 2. Funcionamiento de Gradient Boosting

Gradient Boosting es un método de **ensemble secuencial** que construye árboles uno después de otro, donde cada árbol corrige los errores del anterior:

### Proceso Iterativo:

1. **Primer árbol**: Predice el valor inicial (puede ser la media o un modelo simple)
2. **Cálculo de residuos**: Se calcula el error (residuo) para cada muestra
3. **Nuevo árbol**: Se entrena para predecir los residuos del árbol anterior
4. **Combinación**: Se suma la predicción del nuevo árbol (con un factor de aprendizaje) a la predicción acumulada
5. **Iteración**: Se repite hasta completar el número de árboles especificado

### Fórmula Conceptual:

```
Predicción final = Árbol_1 + η × Árbol_2 + η × Árbol_3 + ... + η × Árbol_n
```

Donde η (eta) es el `learning_rate` que controla qué tan agresivamente cada nuevo árbol corrige los errores.

## 3. Parámetros Clave en tu Implementación

- **`n_estimators=100`**: Número de árboles (iteraciones) a construir
- **`learning_rate=0.1`**: Factor de aprendizaje (0.1 = correcciones pequeñas y conservadoras)
- **`max_depth=5`**: Profundidad máxima de cada árbol individual
- **`min_samples_split=5`**: Mínimo de muestras necesarias para dividir un nodo
- **`min_samples_leaf=2`**: Mínimo de muestras que debe tener una hoja final
- **`loss='squared_error'`**: Función de pérdida (error cuadrático medio)

## 4. Ventajas para Series Temporales

- **Alta precisión**: Puede capturar patrones complejos y no lineales con gran precisión
- **Corrección iterativa de errores**: Cada árbol se enfoca en corregir los errores de los anteriores
- **Flexibilidad**: Maneja relaciones complejas entre los lags y el valor futuro
- **Manejo de features múltiples**: Puede incorporar fácilmente variables exógenas además de los lags
- **Control de overfitting**: El `learning_rate` bajo y la `max_depth` limitada ayudan a prevenir sobreajuste

## 5. Diferencias Clave con Random Forest

| Aspecto | Random Forest | Gradient Boosting |
|---------|---------------|-------------------|
| **Construcción** | Paralela (árboles independientes) | Secuencial (árboles dependientes) |
| **Corrección de errores** | No explícita | Sí, cada árbol corrige errores |
| **Velocidad de entrenamiento** | Rápida | Más lenta (secuencial) |
| **Predicción** | Promedio de árboles | Suma ponderada de árboles |
| **Robustez** | Alta (menos sensible a outliers) | Media (puede sobreajustar) |
| **Interpretabilidad** | Importancia de features | Importancia de features |

## 6. Limitaciones y Consideraciones

- **Entrenamiento secuencial**: No se puede paralelizar fácilmente, por lo que es más lento que Random Forest
- **Sensibilidad a hiperparámetros**: Requiere ajuste cuidadoso de los parámetros
- **Riesgo de overfitting**: Si se usan muchos árboles o un `learning_rate` muy alto, puede sobreajustar
- **No modela tendencias o estacionalidad automáticamente**: Si hay patrones estacionales, deben agregarse como features explícitas
- **Dependencias de largo plazo limitadas**: Solo puede capturar patrones dentro de la ventana de lags que se definan

## 7. Proceso de Entrenamiento

1. **Inicialización**: Se crea una predicción inicial (puede ser la media de los valores objetivo)
2. **Iteración**: Para cada árbol:
   - Se calculan los residuos (error actual)
   - Se entrena un árbol para predecir esos residuos
   - Se actualiza la predicción acumulada
3. **Regularización**: Los parámetros como `max_depth` y `learning_rate` ayudan a controlar el crecimiento
4. **Validación**: Se monitorea el error en un conjunto de validación para evitar overfitting

## 8. Predicción a Múltiples Pasos (Multi-step Forecasting)

Para predecir varios días hacia el futuro:

1. **Día 1**: Usa los últimos valores conocidos de la serie como lags para predecir el primer día futuro
2. **Día 2**: Usa la predicción del día 1 como parte de los lags para predecir el día 2
3. **Días siguientes**: Continúa el proceso, actualizando la ventana de lags con las predicciones anteriores

**Nota importante**: El error se acumula en cada paso porque se usan predicciones anteriores (que pueden tener error) como input para las siguientes predicciones.

## 9. Comparación con Otros Modelos

| Aspecto | Gradient Boosting | Random Forest | MLP (Red Neuronal) | LSTM (Red Recurrente) |
|---------|-------------------|---------------|---------------------|----------------------|
| **Precisión** | ✅ Muy alta | Alta | Alta | Alta |
| **Velocidad entrenamiento** | Media | ✅ Rápida | Media | Lenta |
| **Captura no linealidad** | ✅ Excelente | ✅ Excelente | ✅ Excelente | ✅ Excelente |
| **Orden temporal** | Implícito (lags) | Implícito (lags) | Implícito (lags) | ✅ Explícito |
| **Manejo de features exógenas** | ✅ Excelente | ✅ Excelente | ✅ Excelente | Media |
| **Interpretabilidad** | Media-Alta | ✅ Alta | ❌ Baja | ❌ Baja |
| **Robustez** | Media | ✅ Alta | Media | Media |

## 10. Resumen

Gradient Boosting es efectivo para series temporales cuando:

- Se busca alta precisión en las predicciones
- Se tiene tiempo disponible para entrenar (el proceso es secuencial)
- Se pueden ajustar hiperparámetros cuidadosamente
- Hay relaciones no lineales complejas entre los valores pasados y futuros
- Se tienen variables exógenas adicionales (clima, eventos, etc.)

Es especialmente útil cuando los patrones temporales son complejos y se necesita un modelo muy potente que capture relaciones sutiles entre los valores pasados y futuros. La construcción secuencial permite que el modelo aprenda patrones cada vez más refinados, corrigiendo errores de forma iterativa.

La principal diferencia con Random Forest es que Gradient Boosting construye árboles de forma **secuencial y dependiente**, enfocándose en corregir los errores de los árboles anteriores, mientras que Random Forest construye árboles de forma **paralela e independiente** y simplemente promedia sus predicciones.

