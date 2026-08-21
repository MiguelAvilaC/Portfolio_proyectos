# Pares de fotones correlacionados — test de hipótesis

Métodos Estadísticos en Física Experimental, FCEN-UBA. Trabajo individual.

## El problema

Un cristal birrefringente genera pares de fotones correlacionados, pero la medición viene
contaminada por un fondo de fotones no correlacionados. La pregunta es si se puede
rechazar la hipótesis de que dos muestras no están correlacionadas, y con qué confianza.

## Qué hice

- Simulé señal y fondo con distribuciones de Poisson y construí las muestras
  contaminadas.
- Armé por Monte Carlo la distribución nula del coeficiente de correlación de Pearson,
  con N = 1000 realizaciones bajo H₀.
- Definí la zona de rechazo para α = 0.05, tomando solo la cola derecha porque la
  hipótesis alternativa admite únicamente correlaciones positivas.
- Cuantifiqué los errores de tipo I y II y calculé la potencia del test para diez valores
  distintos de intensidad de fondo.
- Verifiqué que bajo H₀ verdadera la distribución del p-valor es uniforme en [0,1], que
  es la comprobación de que el test está bien construido.

## Resultados

- **Error tipo I: 0.042.** Al ser discreta la distribución, no se alcanza exactamente
  0.05: se toma el bin más cercano por debajo.
- **Potencia 1.00 (β = 0)** en el régimen del experimento. Las distribuciones bajo H₀ y
  H₁ no se solapan, así que el test discrimina perfecto ahí.
- **p-valor < 10⁻³** sobre el dato observado. Ninguna de las realizaciones bajo H₀ llegó
  al valor observado, así que el p-valor no se integra: solo se acota por 1/N. Aumentando
  a N = 50000 la cota baja a 2×10⁻⁵ y el valor observado sigue sin ser alcanzado.

## Dónde deja de funcionar

La potencia cae cuando el fondo crece: con μ₂ = 400 baja a 0.74, con μ₂ = 1000 a 0.30 y
con μ₂ = 5000 a 0.14. El motivo es que el término correlacionado se vuelve despreciable
frente al fondo, y a efectos prácticos las dos muestras terminan siendo dos generaciones
independientes de la misma distribución. Es el límite físico del experimento, no del
test.

**Stack:** Python (SciPy, NumPy, Pandas, Matplotlib).
