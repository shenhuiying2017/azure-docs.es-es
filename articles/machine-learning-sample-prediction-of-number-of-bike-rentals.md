<properties title="Azure Machine Learning Sample: Prediction of the number of bike rentals" pageTitle="Machine Learning Sample: Prediction of bike rentals | Azure" description="A sample Azure Machine Learning experiment to develop a regression model that predicts the number of bike rentals hourly." metaKeywords="" services="" solutions="" documentationCenter="" authors="garye" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="garye"></tags>

# Ejemplo de Aprendizaje automático de Azure: predicción del número de alquileres de bicicletas

*Puede ver el experimento de ejemplo asociado a este modelo en Estudio de aprendizaje automático, en la sección **EXPERIMENTOS** que se encuentra bajo la pestaña **EJEMPLOS**. El nombre del experimento es:*

    Sample Experiment - Demand Forecasting of Bikes

## Descripción del problema

Predicción del número de bicicletas que se alquilarán en cada una de las horas del día actual, dado el historial previo de alquiler, las condiciones meteorológicas medidas cada hora y la indicación de si se trata un día festivo/laboral/del fin de semana. Las predicciones son diferentes para cada hora (por ejemplo
 hay muchos alquileres por la mañana y prácticamente ninguno por la noche).

## Datos

Conjunto de datos UCI Bike Rental basados en datos reales tomados de la empresa Capital Bikeshare, que mantiene una red de alquiler de bicicletas en Washington DC. El conjunto de datos tiene una fila para cada hora de cada día en 2011 y 2012, totalizando 17.379 filas. El rango de alquileres de bicicletas por horas va de 1 a 977.

## Modelo

Utilizamos datos de 2011 como conjunto de entrenamiento y datos de 2012 como conjunto de prueba. Comparamos 4 conjuntos de características:

1.  características de condiciones meteorológicas + vacaciones + día laborable + fin de semana para el día de la predicción
2.  número de bicicletas que se alquilaron en cada una de las 12 horas anteriores
3.  número de bicicletas que se alquilaron en cada uno de los 12 días anteriores a la misma hora
4.  número de bicicletas que se alquilaron en cada una de las 12 semanas anteriores a la misma hora y en el mismo día

Las características B capturan la demanda muy reciente de bicicletas. Las características C capturan la demanda de bicicletas a una hora determinada. Las características D capturan la demanda de bicicletas a una hora determinada y en un día concreto de la semana.

Dado que la etiqueta (número de alquileres) contiene valores reales, tenemos una configuración de regresión. Además, como el número de características es relativamente pequeño (inferior a 100) y estas no están dispersas, la frontera de decisión es probablemente no lineal. De acuerdo con esto, decidimos utilizar el algoritmo de regresión del árbol de decisión ampliado.

## Resultados

| Características | Error medio absoluto | Error cuadrático medio |
|-----------------|----------------------|------------------------|
| A               | 89.7                 | 124.9                  |
| A+B             | 51.2                 | 86.7                   |
| A+B+C           | 48.5                 | 83.7                   |
| A+B+C+D         | 48.8                 | 83.2                   |

</table>
Los mejores resultados se obtienen por medio de las características A+B+C y A+B+C+D. Sorprendentemente, las características D no mejoran significativamente la combinación de A+B+C.

