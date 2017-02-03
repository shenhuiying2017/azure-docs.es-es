---
title: Depurar el modelo en Azure Machine Learning | Microsoft Docs
description: "Explica cómo depurar el modelo en Aprendizaje automático de Azure."
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: 629dc45e-ac1e-4b7d-b120-08813dc448be
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/12/2016
ms.author: bradsev;garye
translationtype: Human Translation
ms.sourcegitcommit: 8c2fb72de830a699c96f3593e9f07a871614267b
ms.openlocfilehash: 58e6cc28a624b0d873e3eabeba971cc8e909267a


---
# <a name="debug-your-model-in-azure-machine-learning"></a>Depurar el modelo en Aprendizaje automático de Azure
En este artículo se explica cómo depurar los modelos en Aprendizaje automático de Microsoft Azure. En concreto, se tratan las posibles razones por las cuales podría encontrar uno de los dos siguientes errores al ejecutar un modelo:

* el módulo [Entrenar modelo][train-model] produce un error 
* el módulo [Puntuar modelo][score-model] produce resultados incorrectos 

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## <a name="train-model-module-produces-an-error"></a>El módulo Entrenar modelo produce un error
![imagen1](./media/machine-learning-debug-models/train_model-1.png)

El módulo [Entrenar modelo][train-model] espera las dos entradas siguientes:

1. El tipo de modelo de clasificación o regresión de la colección de modelos que proporciona Aprendizaje automático de Azure
2. Los datos de entrenamiento con una columna de etiqueta especificada. La columna de etiqueta especifica la variable que se va a predecir. Se supone que el resto de columnas incluidas son características.

Este módulo produce un error en los siguientes casos:

1. Si la columna de etiqueta se especificó incorrectamente, porque se seleccionó más de una columna como de etiqueta o se seleccionó un índice de columna incorrecto. Por ejemplo, el segundo caso se aplicaría si se usara un índice de columna de 30 con un conjunto de datos de entrada que solo tuviese 25 columnas.
2. Si el conjunto de datos no contiene ninguna columna de característica. Por ejemplo, si el conjunto de datos de entrada tiene solo una columna, que se marca como la columna de etiqueta, no habría ninguna característica con la que se pudiera generar el modelo. En este caso, el módulo [Entrenar modelo][train-model] produce un error.
3. El conjunto de datos de entrada (características o de etiqueta) contiene infinito como valor.

## <a name="score-model-module-does-not-produce-correct-results"></a>El módulo Puntuar modelo no produce resultados correctos
![imagen2](./media/machine-learning-debug-models/train_test-2.png)

En un gráfico típico de entrenamiento y pruebas de aprendizaje supervisado, el módulo de [División de datos][split] separa el conjunto de datos original en dos partes: la parte que se utiliza para entrenar el modelo y la parte que se reserva para puntuar el rendimiento del modelo entrenado con los datos sobre los que no se entrenó. A continuación, se usa el modelo entrenado para puntuar los datos de prueba y, luego, se evalúan los resultados para determinar la precisión del modelo.

El módulo [Puntuar modelo][score-model] requiere dos entradas:

1. Una salida de modelo entrenado del módulo de [Entrenar modelo][train-model]
2. Un conjunto de datos de puntuación que sea diferente del conjunto de datos utilizado para entrenar el modelo

Puede ocurrir que, aunque el experimento se realice correctamente, el módulo de [Puntuar modelo][score-model] produzca resultados incorrectos. Varios escenarios pueden provocar que esto ocurra:

1. Si la etiqueta especificada es de categoría y se entrena un modelo de regresión con los datos, módulo de [Puntuar modelo][score-model] genera una salida incorrecta. Esto es debido a que la regresión requiere una variable de respuesta continua. En este caso, sería más adecuado usar un modelo de clasificación. 
2. De forma similar, si un modelo de clasificación se entrena con un conjunto de datos con números de punto flotante en la columna de etiqueta, se podrían producir resultados no deseados. Esto es debido a que la clasificación requiere una variable de respuesta discreta que solo permite valores que abarcan un conjunto finito y generalmente bastante pequeño de clases.
3. Si el conjunto de datos de puntuación no contiene todas las características que se usan para entrenar el modelo, [Puntuar modelo][score-model] generará un error.
4. Si una fila del conjunto de datos de puntuación contiene un valor que falte o un valor infinito de cualquiera de sus características, [Puntuar modelo][score-model] no producirá ningún resultado correspondiente a esa fila.
5. [Puntuar modelo][score-model] puede producir resultados idénticos para todas las filas del conjunto de datos de puntuación. Esto puede ocurrir, por ejemplo, cuando se intenta la clasificación mediante bosques de decisión, si se elige que el número mínimo de muestras por cada nodo de hoja sea mayor que el número de ejemplos de aprendizaje disponibles.

<!-- Module References -->
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/




<!--HONumber=Dec16_HO2-->


