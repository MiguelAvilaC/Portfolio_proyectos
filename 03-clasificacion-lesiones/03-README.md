# Clasificación de lesiones pretumorales

Aprendizaje Automático I, FCEN-UBA, 2025. Trabajo grupal de cuatro personas.

## El problema

Clasificación binaria de buen o mal pronóstico a partir de mediciones de ARN, sobre un
dataset con dos características incómodas: pocas instancias y muchos atributos.

## Los datos

500 muestras, 200 genes, 29.4 % de clase positiva. Separación en desarrollo (90 %) y
control (10 %) preservando la proporción de clases, para que el conjunto de control
tuviera suficientes positivos.

## Qué hice

- Validación cruzada estratificada de 5 folds, implementada primero a mano con
  `StratifiedKFold` y después con las funciones de Scikit-Learn, para comparar ambos
  caminos.
- Búsqueda de hiperparámetros con `RandomizedSearchCV` sobre árbol, KNN y SVM, con el
  rango de cada hiperparámetro justificado por el tamaño del dataset.
- Comparación contra LDA y Naive Bayes gaussiano.
- Evaluación con Accuracy, AUPRC y AUC-ROC, tanto promediando folds como calculando el
  score global sobre las predicciones concatenadas.

## Resultados

AUC-ROC en validación cruzada:

| Modelo | AUC-ROC |
|---|---|
| SVM (RBF, `class_weight='balanced'`) | **0.897** |
| KNN (k=14, pesado por distancia) | 0.869 |
| Naive Bayes gaussiano | 0.785 |
| Árbol de decisión | 0.709 |
| LDA | 0.688 |

## El resultado que descarté

Sobre el conjunto de control, el SVM dio un AUC-ROC de **0.978**, casi 9 puntos por
encima del promedio de validación cruzada y por encima del mejor fold individual. No lo
reporto como la performance del modelo: el control tenía 49 muestras y con esa cantidad
la estimación no es robusta. Los otros dos modelos mostraron el mismo salto sobre el
mismo conjunto, lo que refuerza que esas instancias resultaron más fáciles por azar.

La conclusión útil del trabajo no es el 0.978, es que con 500 muestras hay que reservar
un conjunto de test bastante más grande si se quiere estimar generalización de verdad.

## Por qué gana el SVM

Alta dimensionalidad con pocas instancias es exactamente donde el SVM rinde: usa unos
pocos vectores de soporte, no asume distribución de los datos y el kernel le permite
fronteras no lineales sin costo explícito en el espacio transformado. LDA sufre por lo
contrario: estimar una matriz de covarianza con 200 atributos y 450 muestras.

## Mi aporte

Trabajo grupal. Mi participación estuvo en la implementación de la validación cruzada, la
búsqueda de hiperparámetros y el análisis de los resultados.

**Stack:** Python (Scikit-Learn, Pandas, Seaborn).
