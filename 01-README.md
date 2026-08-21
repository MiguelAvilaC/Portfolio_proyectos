# Optimización de packaging y logística

Data Challenge de AlixPartners, julio 2026. Trabajo individual.

## El problema

Bonsai Corp gestionaba 427 SKUs con 204 tipos de caja distintos, un catálogo fragmentado
por años de crear un molde nuevo con cada lanzamiento. El objetivo era unificarlo
minimizando el costo total de packaging más flete, respetando todas las restricciones
físicas y logísticas de la consigna: fit, headspace, resistencia a compresión, apilado
en pallet y un único grosor homologado.

## Los datos

Cuatro data sets de áreas distintas de la empresa, con discrepancias entre sí:
separadores decimales mezclados, unidades escritas dentro de celdas numéricas, descuentos
con notación inconsistente y valores faltantes o cargados como "ERROR". Todos los
faltantes se reconstruyeron a partir de columnas cruzadas que sí eran consistentes.

## Qué hice

- Reconcilié las cuatro fuentes y verifiqué toda relación calculable entre columnas en
  lugar de asumirla.
- En el EDA deduje que toda la operación es intra-regional, que el costo de packaging
  depende solo del grosor de la caja y que los descuentos se aplican por planta y no
  sobre el volumen global.
- Reduje el espacio de búsqueda: como el flete solo salta cuando una dimensión cruza el
  umbral en que entra una caja más o menos por pallet, dentro de cada ventana de
  tolerancia hay un puñado de medidas distinguibles. Eso dejó 2,785 configuraciones
  admisibles y 27 valores posibles de unidades por pallet.
- Sobre ese universo resolví la asignación de los 427 SKUs como un problema de partición
  de conjuntos con costos por bandas de descuento, con MILP y gap 0 %.

## Resultados

- Catálogo de 204 a 53 tipos de caja, todas de un único grosor de 3.0 mm.
- Costo anual de USD 209.2 M a USD 188.1 M: **−10.11 %**, USD 21.2 M de ahorro.
- Utilización media de pallet de 82 % a 95 %.
- Como la enumeración cubre todo el espacio admisible, el óptimo del modelo es el óptimo
  del problema. No es una buena aproximación: no existe una asignación mejor bajo estas
  restricciones.

## El hallazgo que quedó fuera de la función objetivo

Comparando cajas compradas contra consumidas, se habían adquirido 276 millones de cajas
para usar 62 millones: **USD 96.4 M de capital inmovilizado**, cerca del 77 % sin usar.
Es más plata que todo el ahorro de la optimización y no formaba parte de lo que pedía el
challenge. Al homologarse los grosores, ese stock además es prácticamente irrecuperable.

## Sobre el código

Está en `codigo-exploratorio/`. Es código de competencia, escrito contra reloj y sin
refactorizar. El informe es la pieza que cuenta el trabajo completo.

Los datos originales no se publican por las bases del challenge.

**Stack:** Python (PuLP/CBC, SciPy-HiGHS, Pandas, Matplotlib).
