<properties 
	pageTitle="Depurar el modelo en Aprendizaje automático de Azure | Azure" 
	description="Explica cómo depurar el modelo en Aprendizaje automático de Azure." 
	services="machine-learning"
	documentationCenter="" 
	authors="bradsev" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/13/2015" 
	ms.author="bradsev" />

# Depurar el modelo en Aprendizaje automático de Azure

En este artículo se explica cómo depurar los modelos en Aprendizaje automático de Microsoft Azure. En concreto, se tratan las posibles razones por las cuales podría encontrar uno de los dos siguientes escenarios de error al ejecutar un modelo:

* el módulo Entrenar modelo produce un error 
* el módulo Puntuar modelo produce resultados incorrectos 

## El módulo Entrenar modelo produce un error

![image1](./media/machine-learning-debug-model/train_model-1.png)

El módulo Entrenar modelo espera las dos entradas siguientes:

1. El tipo de modelo de clasificación o regresión de la colección de modelos que proporciona Aprendizaje automático de Azure
2. Los datos de entrenamiento con una columna de etiqueta especificada. La columna de etiqueta especifica la variable que se va a predecir. Se supone que el resto de columnas incluidas son características.

Este módulo produce un error en los siguientes casos:

1. Si la columna de etiqueta se especificó incorrectamente, porque se seleccionó más de una columna como de etiqueta o se seleccionó un índice de columna incorrecto. Por ejemplo, el segundo caso se aplicaría si se usara un índice de columna de 30 con un conjunto de datos de entrada que solo tuviese 25 columnas.

2. Si el conjunto de datos no contiene ninguna columna de característica. Por ejemplo, si el conjunto de datos de entrada tiene solo 1 columna, que se marca como la columna de etiqueta, no habría ninguna característica con la que se pudiera generar el modelo. En este caso, el módulo Entrenar modelo producirá un error.

3. Si el conjunto de datos de entrada (características o de etiqueta) contienen infinito como valor.


## El módulo Puntuar modelo no produce resultados correctos

![image2](./media/machine-learning-debug-model/train_test-2.png)

En un gráfico típico de entrenamiento y pruebas de aprendizaje supervisado, el módulo de división separa el conjunto de datos original en dos partes: la parte que se utiliza para entrenar el modelo y la parte que se reserva para puntuar el rendimiento del modelo entrenado con los datos que no entrenó. A continuación, se usa el modelo entrenado para puntuar los datos de prueba después de los cuales se evalúan los resultados para determinar la precisión del modelo.

El módulo Puntuar modelo requiere dos entradas:

1. Una salida de modelo entrenado del módulo Entrenar modelo
2. Un conjunto de datos de puntuación que no se hayan usado para entrenar el modelo

Puede ocurrir que, aunque el experimento se realice correctamente, el módulo Puntuar modelo produzca resultados incorrectos. Varios escenarios pueden provocar que esto ocurra:

1. Si la etiqueta especificada es de categoría y se entrena un modelo de regresión con los datos, se genera una salida incorrecta producida por el módulo Puntuar modelo. Esto es debido a que la regresión requiere una variable de respuesta continua. En este caso, sería más adecuado usar un modelo de clasificación. 
2. De forma similar, si un modelo de clasificación se entrena con un conjunto de datos con números de punto flotante en la columna de etiqueta, se podrían producir resultados no deseados. Esto es debido a que la clasificación requiere una variable de respuesta discreta que solo permite valores que abarcan un conjunto finito y generalmente bastante pequeño de clases.
3. Si el conjunto de datos de puntuación no contiene todas las características que se usan para entrenar el modelo, el modelo de puntuación generará un error.
4. El modelo de puntuación no produciría ningún resultado correspondiente a una fila del conjunto de datos de puntuación que contenga un valor que falte o un valor infinito de cualquiera de sus características.
5. El modelo de puntuación puede producir resultados idénticos para todas las filas del conjunto de datos de puntuación. Esto puede ocurrir, por ejemplo, cuando se intenta la clasificación mediante bosques de decisión, si se elige que el número mínimo de muestras por cada nodo de hoja sea mayor que el número de ejemplos de aprendizaje disponibles.



<!--HONumber=49-->