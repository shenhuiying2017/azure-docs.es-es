---
title: "Optimización de algoritmos de Azure Machine Learning | Microsoft Docs"
description: "Explica cómo elegir el parámetro óptimo establecido para un algoritmo de Azure Machine Learning."
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 6717e30e-b8d8-4cc1-ad0b-1d4727928d32
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2017
ms.author: bradsev;garye
ms.openlocfilehash: 664ab97cdfb663d9c8a4cc6c7b748eebfbdf580c
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/01/2017
---
# <a name="choose-parameters-to-optimize-your-algorithms-in-azure-machine-learning"></a>Elegir parámetros para optimizar los algoritmos de Azure Machine Learning
En este tema se describe cómo elegir el hiperparámetro adecuado establecido para un algoritmo en Azure Machine Learning. La mayoría de los algoritmos de aprendizaje automático tienen parámetros para configurar. Cuando entrena un modelo, tiene que especificar valores para esos parámetros. La eficacia del modelo entrenado depende de los parámetros del modelo que elija. El proceso de encontrar el conjunto óptimo de parámetros se conoce como *selección del modelo*.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

Existen varias maneras de realizar la selección del modelo. En Machine Learning, la validación cruzada es uno de los métodos más usados para la selección del modelo, y es el mecanismo de selección de modelo predeterminado en Azure Machine Learning. Como R y Python son compatibles con Azure Machine Learning, siempre puede implementar sus propios mecanismos de selección de modelo mediante R o Python.

Hay cuatro pasos en el proceso de encontrar el mejor conjunto de parámetros:

1. **Definir el espacio de parámetro**: para el algoritmo, primero decida los valores de parámetro exactos que quiere tener en cuenta.
2. **Definir la configuración de validación cruzada**: para el conjunto de datos, decida cómo elegir los plegamientos de validación cruzada.
3. **Definir la métrica**: decida qué métrica se va a usar para determinar el mejor conjunto de parámetros, por ejemplo, la precisión, el error cuadrático medio raíz, la precisión, la recuperación o la puntuación f.
4. **Formar, evaluar y comparar**: para cada combinación única de los valores de parámetro, se lleva a cabo la validación cruzada y se basa en la métrica de error que defina. Después de la evaluación y la comparación, puede elegir el modelo de máximo rendimiento.

En la imagen siguiente se muestra cómo se puede lograr esto en Azure Machine Learning.

![Encontrar el mejor conjunto de parámetros](./media/algorithm-parameters-optimize/fig1.png)

## <a name="define-the-parameter-space"></a>Definir el espacio de parámetros
Puede definir el conjunto de parámetros en el paso de inicialización del modelo. El panel de parámetros de todos los algoritmos de Machine Learning tiene dos modos de aprendizaje: de *único parámetro* y de *intervalo de parámetros*. Elija el modo del intervalo de parámetros. En el modo del intervalo de parámetros, puede escribir varios valores para cada parámetro. Puede escribir valores separados por comas en el cuadro de texto.

![Árbol de decisión ampliado de dos clases, único parámetro](./media/algorithm-parameters-optimize/fig2.png)

 De manera alternativa, puede definir los puntos máximos y mínimos de la cuadrícula y el número total de puntos que se va a generar con **Usar generador de intervalo**. De manera predeterminada, se generan los valores de parámetro en una escala lineal. Pero si el cuadro **Escala logarítmica** está activado, los valores se generan en la escala logarítmica (es decir, la relación de los puntos adyacentes es constante en lugar de su diferencia). Para los parámetros de número entero, puede definir un intervalo con un guión. Por ejemplo, “1-10” significa que todos los valores enteros entre 1 y 10 (ambos inclusive) forman el conjunto de parámetros. También se admite un modo mixto. Por ejemplo, el conjunto de parámetros “1-10, 20, 50” incluirá números enteros de 1-10, 20 y 50.

![Árbol de decisión ampliado de dos clases, intervalo de parámetros](./media/algorithm-parameters-optimize/fig3.png)

## <a name="define-cross-validation-folds"></a>Definir los plegamientos de validación cruzada
El módulo de [partición y ejemplo][partition-and-sample] se puede usar para asignar pliegues a los datos de forma aleatoria. En la siguiente configuración de ejemplo del módulo, definimos cinco plegamientos y asignamos aleatoriamente un número de plegamientos a las instancias de ejemplo.

![Partición y ejemplo](./media/algorithm-parameters-optimize/fig4.png)

## <a name="define-the-metric"></a>Definir la métrica
El módulo [Optimizar el modelo Hiperparámetros][tune-model-hyperparameters] proporciona compatibilidad para elegir empíricamente el mejor conjunto de parámetros para un algoritmo determinado y el conjunto de datos. El panel **Propiedades** de este módulo incluye, además de otra información referente a entrenar el modelo, la métrica para determinar el mejor conjunto de parámetros. Tiene dos cuadros de lista desplegables diferentes para los algoritmos de clasificación y regresión, respectivamente. Si el algoritmo en cuestión es un algoritmo de clasificación, se ignorará la métrica de regresión y viceversa. En este ejemplo concreto, la métrica es **Precisión**.   

![Limpiar parámetros](./media/algorithm-parameters-optimize/fig5.png)

## <a name="train-evaluate-and-compare"></a>Formar, evaluar y comparar
El mismo módulo [Optimizar los hiperparámetros de modelo][tune-model-hyperparameters] entrena todos los modelos correspondientes al conjunto de parámetros, evalúa varias métricas y, después, genera el mejor modelo entrenado en función de la métrica que haya elegido. Este módulo contiene dos entradas obligatorias:

* El aprendiz no formado
* El conjunto de datos

El módulo también tiene una entrada de conjunto de datos opcional. Conecte el conjunto de datos con la información de plegamientos a la entrada del conjunto de datos obligatorio. Si el conjunto de datos no se asigna a ninguna información de plegamientos, se ejecuta automáticamente una validación cruzada 10 veces más rápida de manera predeterminada. Si no se realiza la asignación de plegamientos y se proporciona un conjunto de datos de validación en el puerto opcional del conjunto de datos, se usa un modo de prueba de aprendizaje elegido y el primer conjunto de datos para entrenar el modelo para cada combinación de parámetros.

![Clasificador del árbol de decisión ampliado](./media/algorithm-parameters-optimize/fig6a.png)

Después, el modelo se evalúa en el conjunto de datos de validación. El puerto de salida izquierdo del módulo muestra métricas diferentes como funciones de los valores del parámetro. El puerto de salida derecho proporciona el modelo entrenado correspondiente al modelo de máximo rendimiento según la métrica elegida (**Precisión** en este caso).  

![Conjunto de datos de validación](./media/algorithm-parameters-optimize/fig6b.png)

Puede ver los parámetros exactos elegidos mediante la visualización del puerto de salida derecho. Este modelo puede utilizarse para determinar la puntuación de un conjunto de pruebas o en un servicio web de operaciones después de guardarlo como un modelo formado.

<!-- Module References -->
[partition-and-sample]: https://msdn.microsoft.com/library/azure/a8726e34-1b3e-4515-b59a-3e4a475654b8/
[tune-model-hyperparameters]: https://msdn.microsoft.com/library/azure/038d91b6-c2f2-42a1-9215-1f2c20ed1b40/
