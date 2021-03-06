# Probabilidad de ocurrencia de incendios con GBM

## Datos de ocurrencia
Corresponden al producto **MCD64A1 de MODIS ** , que emplea imágenes de reflectancia de espectro radiómetros combinadas con observaciones de incendios activos. El algoritmo El algoritmo utiliza un índice de vegetación (VI) sensible a la combustión para crear umbrales dinámicos que se aplican a los datos compuestos e identifica la fecha de quema con una resolución espacial de 500m. A partir del producto se creó una capa binarizada que identifica cuándo un píxel es definido como quemado o no. [MCD64A1  v006](https://lpdaac.usgs.gov/products/mcd64a1v006/)
![enter image description here](https://github.com/Vakkhus/PN-Incendios/blob/main/Figures/Plots/Ocurrencias.png?raw=true)

## Variables bioclimáticas

Se utilizaron los datos de **CHELSA** como variable climática. Estos datos fueron usados como predictores y las variables climáticas futuras CMIP5 y CMIP6 (2061-2060 y 2041-2070 respectivamente) fueron utilizadas para los escenarios rcp 4.5, rcp 8.5, ssp 370 y 585) ![enter image description here](https://github.com/Vakkhus/PN-Incendios/blob/main/Figures/Plots/bio.png?raw=true)

## Influencia humana
Dos variables fueron testeadas para incorporar el efecto producido por la influencia humana en la ocurrencia de incendios: La densidad poblacional (Columbia University, 2018) y la Modificación Humana Global (Kennedy et al., 2020, [Global Human Modification, gHM](https://sedac.ciesin.columbia.edu/data/set/lulc-human-modification-terrestrial-systems))  por separado. 

Se cree que la mayoría de los incendios en el sur de Chile son causados por humanos, por usamos población como predictor de incendios.  
![enter image description here](https://github.com/Vakkhus/PN-Incendios/blob/main/Figures/Plots/poblacion.png?raw=true)

Sin embargo, una variable más completa para medir el impacto de la actividad humana podría ser gHM, una medida acumulativa de la modificación humana de las tierras terrestres basada en el modelado de la extensión física de 13 factores de estrés antropogénicos y sus impactos estimados utilizando conjuntos de datos globales espacialmente explícitos. 

 - asentamientos humanos (densidad de población, áreas urbanizadas)
-  agricultura (tierras de cultivo, ganadería)
-  transporte (carreteras principales, secundarias y de dos vías; ferrocarriles)
-  minería y producción de energía
-  infraestructura eléctrica (líneas eléctricas, luces nocturnas)  

![enter image description here](https://github.com/Vakkhus/PN-Incendios/blob/main/Figures/Plots/ghm.png?raw=true)

Ambas variables fueron comparadas e incorporadas en la construcción del modelo por separado. La correlación de Pearson entre las variables fue de un 0.705.  
![enter image description here](https://github.com/Vakkhus/PN-Incendios/blob/main/Figures/Plots/ghm%20vs%20density.png?raw=true)

Por otro lado, se calculó la correlación de Pearson entre cada par de proyecciones realizadas, incluyendo la población en el modelo e incluyendo la modificación humana.  
![enter image description here](https://github.com/Vakkhus/PN-Incendios/blob/main/Figures/Plots/corr_pearson_(2).png?raw=true)

## Escenarios de cambio climático
Para el caso de los escenarios de cambio climático CMIP6, se utilizaron los disponibles en CHELSA: DFDL-ESM4, IPSL-CM6A-LR, MPI-ESM1-2-HR, MRI-ESM2-0, UKESM1-0-LL

## Probabilidad de ocurrencia de incendios
### Boosted Regression Trees
Se usó el algoritmo boosted regression trees (BRT) para ajustar los modelos, ya que ofrecen varias ventajas sobre otras técnicas de regresión. Los BRT son un tipo de técnica de aprendizaje automático que busca optimizar la precisión predictiva de los datos fuera de la muestra en un proceso iterativo mediante el uso de un conjunto de árboles de regresión. Al centrarse en la predicción, los BRT proporcionan una mejor estimación de la precisión predictiva en contraste con los modelos lineales generalizados tradicionales (por ejemplo, regresión lineal). Suelen evitar incluir variables irrelevantes, y las interacciones entre variables se incluyen de forma inherente sin necesidad de especificarlas a priori (Friedman, 2001; Friedman & Meulman, 2003). Además, los BRT pueden adaptarse a cualquier forma de respuesta y, por lo tanto, evitan la posibilidad de un desajuste. Sin embargo, debido a esta flexibilidad, la validación cruzada es necesaria para evitar el sobreajuste. Dado que los BRT dependen de métodos basados en árboles, el número de bifurcaciones de cada variable junto con la reducción del error residual en cada una de ellas se puede utilizar para calcular la contribución relativa de cada variable (Friedman & Popescu, 2008; Greenwell et al. , 2020). Realizamos una validación cruzada estructurada 10 veces para cada conjunto de entrenamiento para reducir el sobreajuste, seleccionando el mejor modelo optimizando el valor de Root Mean Squared Error (RSME) (Kuhn & Johnson, 2013). Esto se hizo usando el paquete **caret** (Kuhn, 2008) y árboles de regresión potenciados a través del paquete **gbm** (Greenwell et al., 2020).

### Modelo generado
Los datos de área quemada, variables bioclimáticas y de población fueron agrupados y divididos aleatoriamente en un set de datos de entrenamiento (20%) y uno de testeo o validación (80%). Los datos de entrenamiento fueron divididos en 10 fold distribuídos espacialmente mediante la utilización de K-means para así controlar el sobreajuste. 

```R
df <- downSample(x = df[, -ncol(df)],y = df$ocurrencia)
index=createDataPartition(df$Class,list=FALSE,p=0.8)
dftrain=df[index,]
dftest=df[-index,]
kmeans <- kmeans(data.frame(dftrain[3:21]), 10)
```
![enter image description here](https://github.com/Vakkhus/PN-Incendios/blob/main/Figures/Plots/kmeansmap.png?raw=true)

El modelo GBM fue ajustado utilizando como variable respuesta la ocurrencia binarizada de incendios, y realizando un cross-validation entre los k-fold generados, optimizando el valor de RMSE.
```R
fitControl= trainControl(method='cv', 
			 index=indin, 
			 indexOut = indout) 
#indin e indout son los índices de cada fold usado para el cross-validation
model = train(Class ~ ., 
	      data=dftrain[3:23], 
	      method="gbm", 
	      trControl=fitControl, 
	      tuneGrid=gbmGrid)
```
 Finalmente, usando el set de datos de validación apartado inicialmente se obtuvieron las métricas de exactitud y se procede a la proyección en los distintos escenarios de cambio climático. 

![enter image description here](https://github.com/Vakkhus/PN-Incendios/blob/main/Figures/Plots/diagrama_final.png?raw=true)

### Proyección

Se usaron las variables bioclimáticas de Chelsa como predictores (Karger et al., 2017), con y sin las variables de influencia humana incluídas. Se realizó la proyección con cada uno de los modelos de cambio climático descritos anteriormente y finalmente, se promedió el resultado de todos los modelos para obtener una predicción resumida del efecto medio del cambio climático en la ocurrencia de incendios (Fajardo et al., 2020).

# [Resultados](https://github.com/Vakkhus/PN-Incendios/tree/main/Results/Raster)
