# Probabilidad de ocurrencia de incendios en Chile modelado con Machien learning (Gradient Boosted Machine) y su impacto en las reservas de carbono irrecuperable.

## Puntos para la intro

1. Chile ha apostado a mitigar sus emisiones y adaptarse al cambio climático usando Soluciones Basadas en la Naturaleza, entre als cuales esta la reforestacion, la restauración, el mejoramiento del carbono en el suelo de predios agricolas, plantaciones forestales, entre otros. Toda estas soluciones sin embargo pueden verse afectadas negativamente en su implementación y en su eficacia debido aincendios forestales.

2. La última decada ha sido particualrlemente prodiga en incendios de alto impacto no vistos en los últimos cien años. Los incendios no solo representan desastres ambientales que pueden llegar a ser severos incluso con pérdidas de vidas humanas y pueblos, como ocurrio en Chile en el 2017, sino que ademas afectan el carbon secuestrado en el suelo liebrando CO2 a la atmosfera. Este carbon es irrecuperable, pues tomaría ciento o miles de años para volver a capturarlo (Goldstein et al. 2020).

3. Forest fire regimes are expected to change,  increasing in severity and event size (e.g., Flannigan et al. 2000, Iglesias et al. 2022) as has been recorded in the past due to abrupt climate changes  ( Marlon et al. 2009)

4. In chile most fires are of human-driven and in some areas of Patagonia they increased markedly after the European colonization (Iglesias y Whitlock 2014). etc etc...

Revisar libro de biodiversidad (COP25) y tambien el las SBN (ver adjuntos)

- Iglesias, Virginia, Jennifer K. Balch, and William R. Travis. “U.S. Fires Became Larger, More Frequent, and More Widespread in the 2000s.” Science Advances 8, no. 11 (March 18, 2022): eabc0020. [https://doi.org/10.1126/sciadv.abc0020](https://doi.org/10.1126/sciadv.abc0020)

## Objetivos

Este análasis busca identificar las variables mejor asociadas a la ocurrencia de incedios a escala regional en Chile entre la región de Valparaíso y Los Lagos (32-44$^\circle$). Para esto, se usan datos de incendios de MODIS a una resolucion de 500m el pixel, la BD de Chelsa, para  asociar variables climáticas, y el Global Human Modification index para dar cuenta del impacto antrópico. Usando un método de Machine Learning (GBM) se determina:  (i) qué variables explican mejor la variabilidad espacial de incendios; (ii) incidencia de incendios en áreas protegidas ; (iii) cómo se proyecta la ocurrencia de incendios futuros usando la calibración del modelo generado en (i). Se evalúa el intervalo 2041-2070 bajo las recientes proyecciones de escenarios RCP 4.5, 8.5 (2060-2080) y SSP (4-6 y 5-8.5). Estos últimos  incluyen el efecto de  variables socio-ambientales en escenarios similares a los RCP;   Finalmente, (iv) se evalua la pérdida de carbono originada por incendios y el rol de las áreas protegidas para mitigar dichos efectos.


## Datos 
### Incendios
La ocurrencia de incendio corresponde al producto **MCD64A1** de MODIS. Este emplea imágenes de reflectancia del espectro radiómetros combinadas con observaciones de incendios activos. El algoritmo utiliza un índice de vegetación (VI) sensible a la combustión para crear umbrales dinámicos que se aplican a los datos compuestos e identifica la fecha de quema con una resolución espacial de 500m. A partir del producto se creó una capa binarizada para el territorio comprendido entre la región de O'Higgins y la región de Los Lagos, que identifica cuándo un píxel es definido como quemado o no, entre los añsos 2001 a 2020. [MCD64A1  v006](https://lpdaac.usgs.gov/products/mcd64a1v006/)

[figure](https://github.com/Vakkhus/PN-Incendios/blob/main/Figures/270123/Occurrence_MODIS.pdf)

## Disponibilidad de carbono

Se usó los datos de [Noon 2021](https://doi.org/10.1038/s41893-021-00803-6) para representar la disponibilidad de carbono irrecuperable, la fracción de carbono que puede ser afectado por la actividad humana, que es vulnerable a la pérdida debido a cambios de uso de suelo y que si se pierde no puede ser recuperado en un periodo de tiempo determinado [(Goldstein et al. 2020)](https://doi.org/10.1038/s41558-020-0738-8). 

```
Noon, Monica L., Allie Goldstein, Juan Carlos Ledezma, Patrick R. Roehrdanz, Susan C. Cook-Patton, Seth A. Spawn-Lee, Timothy Maxwell Wright et al. "Mapping the irrecoverable carbon in Earth’s ecosystems." Nature Sustainability 5, no. 1 (2022): 37-46 DOI:10.1038/s41893-021-00803-6.

Goldstein, A., Turner, W. R., Spawn, S. A., Anderson-Teixeira, K. J., Cook-Patton, S., Fargione, J., … Hole, D. G. (2020). Protecting irrecoverable carbon in Earth’s ecosystems. Nature Climate Change, 10(4), 287–295. doi:10.1038/s41558-020-0738-8 
```

## Variables bioclimáticas

Se utilizaron los datos de alta resolución de **CHELSA** como variable climática. Las 19 variables bioclimáticas fueron usadas como predictores para la ocurrencia de incendios.

```
Dirk Nikolaus Karger; Olaf Conrad; Jürgen Böhner; Tobias Kawohl; Holger Kreft; Rodrigo Wilber Soria-Auza; Niklaus E. Zimmermann; H. Peter Linder; Michael Kessler (2021). Climatologies at high resolution for the earth’s land surface areas. EnviDat. doi: 10.16904/envidat.228.
```
### Escenarios climaticos
Se usaron los escenarios CMIP5 y CMIP6 (2061-2060 y 2041-2070 respectivamente) fueron utilizadas para los escenarios rcp 4.5, rcp 8.5, ssp 370 y 585) ![Variables bioclimáticas](https://github.com/Vakkhus/PN-Incendios/blob/main/Figures/Plots/bio.png?raw=true)

## Influencia humana
Se cree que la mayoría de los incendios en el sur de Chile son causados por humanos [(Peña-Fernandez & Valenzuela-Palma, 2008)](https://www.fs.usda.gov/psw/publications/documents/psw_gtr208es/psw_gtr208es_595-612_pena-fernandez.pdf?iframe=true&width=95%&height=95%), por lo que se incluyó la Modificación Humana Global (GHM en inglés) como variable predictora. Este índice representa una medida acumulativa de la modificación humana basada en el modelado de la extensión física de 13 factores de estrés antropogénicos y sus impactos estimados utilizando conjuntos de datos globales espacialmente explícitos: asentamientos humanos (densidad de población, áreas urbanizada, agricultura (tierras de cultivo, ganadería), transporte (carreteras principales, secundarias y de dos vías; ferrocarriles, minería y producción de energía, infraestructura eléctrica (líneas eléctricas, luces nocturnas)(Kennedy et al., 2020, [Global Human Modification, gHM](https://sedac.ciesin.columbia.edu/data/set/lulc-human-modification-terrestrial-systems)). 

```
Peña-Fernández, E., & Valenzuela-Palma, L. (2008). Incremento de los incendios forestales en bosques naturales y plantaciones forestales en Chile. In Memorias del segundo simposio internacional sobre políticas, planificación y economía de los programas de protección contra incendios forestales: Una visión global (pp. 595-612).

```
![GHM](https://github.com/Vakkhus/PN-Incendios/blob/main/Figures/Plots/ghm.png?raw=true)

## Escenarios de cambio climático
Se utilizaron distintos modelos de escenarios de cambio climático en CHELSA. Para los escenarios puramente climáticos basados en el los RCP (CMIP5), se usaron los siguientes modelos: GFDL_ESM2G, CESM1_BGC, MIROC_ESM_CHEM, MPI_ESM_LR e IPSL_CM5A_LR, seleccionados a partir de .... . Para el nuevo marco de proyección de cambio climático que incluye escenarios socio-ambientales, SSP (CMIP6), se utilizaron los modelos DFDL-ESM4, IPSL-CM6A-LR, MPI-ESM1-2-HR, MRI-ESM2-0, UKESM1-0-LL, seleccionados a partir de su disponibilidad en CHELSA.

## Análisis

### Probabilidad de ocurrencia de incendios usando Boosted Regression Trees
Se consideró una aproximación de lenguaje de màquinas mediante el algoritmo de `boosted regression trees` (BRT) para ajustar los modelos de ocurrencia de incendios, ya que ofrecen varias ventajas sobre otras técnicas de regresión. Los BRT son un tipo de técnica de aprendizaje automático que busca optimizar la precisión predictiva de los datos fuera de la muestra en un proceso iterativo mediante el uso de un conjunto de árboles de regresión. Al centrarse en la predicción, los BRT proporcionan una mejor estimación de la precisión predictiva en contraste con los modelos lineales generalizados tradicionales (por ejemplo, regresión lineal). Suelen evitar incluir variables irrelevantes, y las interacciones entre variables se incluyen de forma inherente sin necesidad de especificarlas a priori (Friedman, 2001; Friedman & Meulman, 2003). Además, los BRT pueden adaptarse a cualquier forma de respuesta y, por lo tanto, evitan la posibilidad de un desajuste. Sin embargo, debido a esta flexibilidad, la validación cruzada es necesaria para evitar el sobreajuste. Dado que los BRT dependen de métodos basados en árboles, el número de bifurcaciones de cada variable junto con la reducción del error residual en cada una de ellas se puede utilizar para calcular la contribución relativa de cada variable (Friedman & Popescu, 2008; Greenwell et al. , 2020). Realizamos una validación cruzada estructurada 10 veces para cada conjunto de entrenamiento, los cuales fueron seleccionados a partir del 80% de los datos utilizando K-means [(Stuart, 1982)](10.1109/TIT.1982.1056489) para reducir el sobreajuste y factores asociados a la autocorrelación espacial, pues se asume que esta existe en variables bioclimáticas. Así, se seleccionó el mejor modelo resultante a través de la optimización del valor de Root Mean Squared Error (RSME) (Kuhn & Johnson, 2013). Esto se hizo usando el paquete **caret** (Kuhn, 2008) y árboles de regresión potenciados a través del paquete **gbm** (Greenwell et al., 2020). Finalmente, usando un set de datos de validación apartado inicialmente (20 %) se obtuvieron las métricas de exactitud y se realizó la proyección en los distintos escenarios de cambio climático. 

![Resumen gráfico de métodos](https://github.com/Vakkhus/PN-Incendios/blob/main/Figures/Plots/diagrama_final.png?raw=true)

### Proyección

Se usaron las variables bioclimáticas de Chelsa como predictores (Karger et al., 2017), con la variable de influencia humana (GHM) incluída, ambas para la superficie comprendida entre las regiones de Valparaíso y Los Lagos. Se realizó la proyección con cada uno de los modelos de cambio climático descritos anteriormente y finalmente, se promedió el resultado de todos los modelos para obtener una predicción resumida del efecto medio del cambio climático en la ocurrencia de incendios (Fajardo et al., 2020).

### Cálculo de pérdida potencial de carbono irrecuperable

Para el cálculo de la pérdida potencial de carbono irrecuperable, se multiplicó el valor de carbono irrecuperable y su incerteza asociada [(Noon 2021)](https://doi.org/10.1038/s41893-021-00803-6) y la probabilidad de ocurrencia de incendios para cada escenario de cambio climático proyectado. Posteriormente, se calculó la pérdida potencial de carbono irrecuperable dentro de cada una de las áreas protegidas administradas por el Servicio Nacional de Áreas Protegidas del Esado (SNASPE) dentro del área de estudio. 

## RESULTADOS

Mapas de fire ocurrence prob. 
Mapas de Potential carbon loss.
Mapas de potential carbon loss uncertainty.

Mapa? de carbono perdido por AP y tabla asociada.



