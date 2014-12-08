<properties title="Azure Machine Learning Sample: Prediction of the number of bike rentals" pageTitle="Ejemplo de aprendizaje automático: Predicción de alquileres de bicicletas | Azure" description="A sample Azure Machine Learning experiment to develop a regression model that predicts the number of bike rentals hourly." metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="garye" manager="paulettm" editor="cgronlun"  videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/23/2014" ms.author="garye" />


# Ejemplo de Aprendizaje automático de Azure: predicción del número de alquileres de bicicletas

>[AZURE.NOTE]
>El [experimento de ejemplo] y el [conjunto de datos de ejemplo] asociado a este modelo están disponibles en Estudio de aprendizaje automático. Consulte la siguiente información para obtener más detalles.
[Experimento de ejemplo]: #sample-experiment
[Conjunto de datos de ejemplo]: #sample-dataset


## Descripción del problema ##
Predicción del número de bicicletas que se alquilarán en cada una de las horas del día actual, dado el historial previo de alquiler, las condiciones meteorológicas medidas cada hora y la indicación de si se trata un día festivo/laboral/del fin de semana. Las predicciones son diferentes para cada hora (por ejemplo
 hay una gran cantidad de alquileres por la mañana y apenas alquileres por la noche). 

## Datos ##
Conjunto de datos UCI Bike Rental basados en datos reales tomados de la empresa Capital Bikeshare, que mantiene una red de alquiler de bicicletas en Washington DC. El conjunto de datos tiene una fila para cada hora de cada día en 2011 y 2012, totalizando 17.379 filas. El rango de alquileres de bicicletas por horas va de 1 a 977.

## Modelo ##
Utilizamos datos de 2011 como conjunto de entrenamiento y datos de 2012 como conjunto de prueba. Comparamos 4 conjuntos de características:

1. características de condiciones meteorológicas + vacaciones + día laborable + fin de semana para el día de la predicción
1. número de bicicletas que se alquilaron en cada una de las 12 horas anteriores
1. número de bicicletas que se alquilaron en cada uno de los 12 días anteriores a la misma hora
1. número de bicicletas que se alquilaron en cada una de las 12 semanas anteriores a la misma hora y en el mismo día

Las características B capturan la demanda muy reciente de bicicletas. Las características C capturan la demanda de bicicletas a una hora determinada. Las características D capturan la demanda de bicicletas a una hora determinada y en un día concreto de la semana.

Dado que la etiqueta (número de alquileres) contiene valores reales, tenemos una configuración de regresión. Además, como el número de características es relativamente pequeño (inferior a 100) y estas no están dispersas, la frontera de decisión es probablemente no lineal. De acuerdo con esto, decidimos utilizar el algoritmo de regresión del árbol de decisión ampliado.

## Resultados ##

<table border="1">
<tr><th>Características</th><th>Error absoluto medio</th> <th>Raíz cuadrada del error cuadrático medio</th> </tr>
<tr style="background-color: #fff"><td>A</td> <td> 89,7</td> <td>124,9 </td> </tr>
<tr style="background-color: #fff"><td>A+B</td><td>51,2 </td> <td>86,7 </td></tr>
<tr style="background-color: #fff"><td>A+B+C</td><td> 48,5</td> <td> 83,7 </td></tr>
<tr style="background-color: #fff"><td>A+B+C+D</td><td> 48,8 </td> <td>83,2 </td></tr>
</table>

</table>

Los mejores resultados se obtienen por medio de las características A+B+C y A+B+C+D. Sorprendentemente, las características D no mejoran significativamente la combinación de A+B+C. 

## Experimento de ejemplo

Puede encontrar el siguiente experimento de ejemplo asociado a este modelo en Estudio de aprendizaje automático, en la sección **EXPERIMENTOS** bajo la pestaña **EJEMPLOS**.

> **Experimento de ejemplo - Previsión de la demanda de bicicletas**


## Conjunto de datos de ejemplo

El siguiente conjunto de datos de ejemplo que se usa en este experimento está disponible en Estudio de aprendizaje automático en la paleta de módulos bajo **Conjuntos de datos guardados**.

###Conjunto de datos UCI de alquiler de bicicletas
[AZURE.INCLUDE [machine-learning-sample-dataset-bike-rental-uci-dataset](../includes/machine-learning-sample-dataset-bike-rental-uci-dataset.md)]


