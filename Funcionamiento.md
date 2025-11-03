# Random Forest
## Componentes del Pipeline 
1. StandardScaler (preprocesador)
- Normaliza las variables a media 0 y varianza 1.
- Mejora la eficiencia y la estabilidad numérica.
- Se ajusta con fit() y transforma con transform().

2. RandomForestRegressor (modelo)
- Bosque aleatorio de 100 árboles para regresión.
    - n_estimators=100: número de árboles.
    - max_depth=10: profundidad máxima.
    - min_samples_split=5 y min_samples_leaf=2: controlan el crecimiento.
    - random_state=42: reproducibilidad.
    - n_jobs=-1: paralelización en todos los cores.

Funcionamiento: Los datos pasan automáticamente por:datos_originales → StandardScaler → RandomForest → predicciones

## Ventajas de usar Pipeline
Automatiza el flujo de datos.
Evita fuga de información entre train/test.
Simplifica fit() y predict().
Reproduce el procesamiento en producción.
En resumen: rf_pipeline combina normalización y bosque aleatorio para predecir el número de pacientes hospitalizados.

# Gradient Boosting
## Componentes del Pipeline 
1. StandardScaler (preprocesamiento)
- Estandariza: media 0 y desviación 1.
- Mejora estabilidad y convergencia; no afecta las predicciones.
2. GradientBoostingRegressor (modelo)
- Entrenamiento secuencial en 100 iteraciones con error cuadrático.
- Parámetros principales: learning_rate=0.1, max_depth=5, n_estimators=100.
    - residual = verdadero - predicción_anteriorpredicción_nueva = predicción_anterior + learning_rate * (predicción_residual)
- Flujo: se ajusta un árbol a los residuos y se combina con un paso learning_rate.
    - learning_rate=0.1: paso conservador.
    - max_depth=5: árboles menos complejos.
    - min_samples_split=5 y min_samples_leaf=2: mínimos por rama/hoja.
    - random_state=42: reproducibilidad.
    - loss='squared_error': función de pérdida (MSE).