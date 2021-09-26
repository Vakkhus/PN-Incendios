# Welcome to StackEdit!
Los modelos de distribución de probabilidad de ocurrencia de incendios 


## Datos de ocurrencia
Corresponden al producto **MCD64A1 de MODIS ** , que emplea imágenes de reflectancia de espectro radiómetros combinadas con observaciones de incendios activos. El algoritmo El algoritmo utiliza un índice de vegetación (VI) sensible a la combustión para crear umbrales dinámicos que se aplican a los datos compuestos e identifica la fecha de quema con una resolución espacial de 500m. A partir del producto se creó una capa binarizada que identifica cuándo un píxel es definido como quemado o no. [MCD64A1  v006](https://lpdaac.usgs.gov/products/mcd64a1v006/)
![enter image description here](https://github.com/Vakkhus/PN-Incendios/blob/main/Figures/Plots/Ocurrencias.png?raw=true)

## Variables bioclimáticas

Se utilizaron los datos de **CHELSA** como variable climática. Estos datos fueron usados como predictores y las variables climáticas futuras CMIP5 (2050 y 2070) fueron utilizadas para los diferentes escenarios (rcp 4.5 y 8.5) ![enter image description here](https://github.com/Vakkhus/PN-Incendios/blob/main/Figures/Plots/bio.png?raw=true)

## Escenarios de cambio climático
Los escenarios de cambio climático se compararon utilizando GCM compareR (Fajardo et al., 2020), considerando la temperatura media anual y la precipitación anual. La tabla de comparación escalada resultante entre escenarios futuros se utilizó para seleccionar los modelos que se utilizarían en el proyecto. Se usó el índice de estructura simple implementado por el paquete Vegan (Dolnicar et al., 1999; Oksanen et al., 2019) para probar y establecer el mejor número de clusters (entre dos y ocho) para representar los 32 MCG comparados. Se identificó y seleccionó, desde cada grupo hasta el GCM más cercano al centroide del grupo seleccionado. Los cinco GCMS seleccionados fueron **cesm1_bgc, ggfdl_esm2g, ipsl_cm5a_lr, miroc_esm_chem y mpi_esm_lr** y los GCM seleccionados junto con los grupos se muestran en la figura. 
![enter image description here](https://github.com/Vakkhus/PN-Incendios/blob/main/Figures/Plots/modelos.png?raw=true)



## Modelo de probabilidad de ocurrencia

Se usó las variables bioclimáticas de Chelsa como predictores (Karger et al., 2017), junto con la densidad poblacional extraída de (Columbia University, 2018). Finalmente, producimos un promedio de cada modelo para obtener una predicción resumida del efecto medio del cambio climático en la ocurrencia de incendios (Fajardo et al., 2020).
