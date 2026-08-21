# Predicción de puntuación y capitalización en texto normalizado

Aprendizaje Automático II, FCEN-UBA, 2025. Trabajo grupal de cuatro personas.

## El problema

Los sistemas de reconocimiento de habla devuelven texto plano, en minúsculas y sin
puntuación. Eso rompe el sentido de la oración y complica usar la salida en aplicaciones
como el subtitulado automático. La tarea es recuperar, para cada palabra, la puntuación
inicial, la final y el tipo de capitalización.

## Los datos

Corpus CC100 en español. Filtrado de instancias mal escritas y tokenización con
BERT-base-multilingual-cased. 8.9 M de tokens de entrenamiento y 2.2 M de test. Las
clases están naturalmente desbalanceadas: un espacio en blanco es muchísimo más frecuente
que un punto.

## Qué hice

Entrené y comparé tres modelos sobre las mismas tres tareas:

- **Random Forest** con 15 atributos construidos a mano (posición del token, distancias
  entre embeddings, etc.), uno por tarea y sin pesos compartidos.
- **RNN** con LSTM de dos capas y *multi-task learning*: una sola red para las tres
  tareas, con tres capas densas independientes a la salida.
- **Bi-LSTM**, igual que la anterior pero bidireccional.

En los tres casos el desbalance se atacó con pesos inversamente proporcionales a la
frecuencia en la función de pérdida, más *label smoothing* en las redes para evitar
sobreconfianza en la clase mayoritaria.

## Resultados

F1-macro por tarea (puntuación inicial / puntuación final / capitalización):

| Modelo | Punt. inicial | Punt. final | Capitalización | Promedio |
|---|---|---|---|---|
| Random Forest | 0.51 | 0.54 | 0.80 | 0.62 |
| RNN | 0.64 | 0.45 | 0.80 | 0.63 |
| Bi-LSTM | **0.70** | **0.73** | **0.87** | **0.77** |

## Qué explica esos números

La RNN unidireccional rinde peor que el Random Forest en puntuación final, y no es un
error de entrenamiento: la puntuación final depende de lo que viene *después* del token,
información que la red unidireccional no tiene. La Bi-LSTM la usa explícitamente y el
Random Forest la aproxima de forma indirecta, con atributos como la distancia coseno al
embedding de la palabra siguiente.

En capitalización los tres modelos rinden parecido, porque ahí pesan más las
características locales —ser la primera palabra de la oración, ser numérico— que el
contexto secuencial.

## Mi aporte

Trabajo grupal. Participé en la construcción de features, el armado y entrenamiento de
los modelos, el tuning de hiperparámetros y el análisis comparativo de resultados.

**Stack:** Python (PyTorch, Transformers, Scikit-Learn).
