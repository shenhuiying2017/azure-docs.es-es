---
title: "Selección de características en el proceso de ciencia de datos en equipos | Microsoft Docs"
description: "Aquí se explica el propósito de la selección de características y ofrece ejemplos de su rol en el proceso de mejora de los datos del aprendizaje automático."
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 878541f5-1df8-4368-889a-ced6852aba47
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/21/2017
ms.author: zhangya;bradsev
ms.openlocfilehash: c69e7a7e86ccea2073209eabe123049e59a2b8e3
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/23/2017
---
# <a name="feature-selection-in-the-team-data-science-process-tdsp"></a>Selección de características en el proceso de ciencia de datos en equipos (TDSP)
En este artículo se explican los propósitos de la selección de características y ofrece ejemplos de su rol en el proceso de mejora de los datos del aprendizaje automático. Estos ejemplos se extraen de Azure Machine Learning Studio. 

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

La ingeniería y la selección de características forman parte del proceso de ciencia de datos en equipos TDSP descrito en el artículo [¿Cuál es el ciclo de vida del TDSP?](overview.md). La selección y la ingeniería de características son partes del paso del **desarrollo de características** del TDSP.

* **Diseño de características**: este proceso intenta crear características pertinentes adicionales a partir de características existentes sin procesar en los datos y mejorar la eficacia predictiva del algoritmo de aprendizaje.
* **selección de características**: este proceso selecciona el subconjunto de claves de las características de datos originales en un intento por reducir la dimensionalidad del problema de entrenamiento.

Normalmente, la **ingeniería de características** se aplica primero para generar características adicionales y, a continuación, se realiza el paso de **selección de características** para eliminar características irrelevantes, redundantes o altamente correlacionadas.

## <a name="filter-features-from-your-data---feature-selection"></a>Filtrado de características desde sus datos: selección de características
La selección de características es un proceso que normalmente se aplica para la construcción de conjuntos de datos de entrenamiento para tareas de modelado predictivo, como las tareas de clasificación o de regresión. El objetivo es seleccionar un subconjunto de las características a partir del conjunto de datos original que reduce sus dimensiones al usar un conjunto de características mínimo para que represente la cantidad máxima de varianza en los datos. Este subconjunto de características se utiliza para entrenar el modelo. La selección de características tiene dos propósitos principales.

* En primer lugar, la selección de características a menudo aumenta la precisión de la clasificación a través de la eliminación de características irrelevantes, redundantes o altamente correlacionadas.
* En segundo lugar, disminuye la cantidad de características, lo que hace que el proceso de entrenamiento del modelo sea más eficiente. Esto resulta especialmente importante cuando se trata de sistemas aprendices que son costosos de entrenar, como las máquinas de vectores de soporte.

A pesar de que la selección de características sí busca disminuir la cantidad de características en el conjunto de datos que se usa para entrenar el modelo, no nos referiremos a ella con el término "reducción de la dimensionalidad". Los métodos de selección de características extraen un subconjunto de las características originales de los datos sin cambiarlas.  Los métodos de reducción de dimensionalidad emplean características diseñadas que pueden transformar las características originales y, de ese modo, modificarlas. Algunos ejemplos de los métodos de reducción de dimensionalidad incluyen el análisis del componente principal, el análisis de correlación canónica y la descomposición en valores singulares.

Entre otros aspectos, una categoría ampliamente aplicada de los métodos de selección de categorías en un contexto supervisado se llama "selección de características basada en filtro". Mediante la evaluación de la correlación entre cada característica y el atributo de destino, estos métodos aplican una medida estadística para asignar una puntuación a cada característica. A continuación, las características se clasifican según la puntuación, lo que se puede usar para definir el umbral para conservar o eliminar una característica específica. Algunos ejemplos de las medidas estadísticas que se usan en estos métodos incluyen la correlación de Pearson, la información mutua y la prueba de Chi-cuadrado.

En Azure Machine Learning Studio, estos son los módulos proporcionados para la selección de características. Tal como se muestra en la figura siguiente, entre estos módulos se incluyen la [selección de características basada en filtro][filter-based-feature-selection] y el [análisis discriminante lineal de Fisher][fisher-linear-discriminant-analysis].

![Ejemplo de selección de características](./media/select-features/feature-Selection.png)

Por ejemplo, considere el uso del módulo de [selección de características basada en filtro][filter-based-feature-selection]. Para mayor comodidad, continúe con el ejemplo de minería de datos de texto. Suponga que desea crear un modelo de regresión una vez creado un conjunto de 256 características mediante el módulo de [hash de características][feature-hashing] y que la variable de respuesta es la "Col1" y contiene una clasificación de las reseñas de un libro, que van desde 1 a 5. Defina el "Método de puntuación de características" en "Correlación de Pearson", la "Columna de destino" en "Col1" y el "Número de características deseadas" en 50. Seguidamente, el módulo de [Selección de características basada en filtro][filter-based-feature-selection] generará un conjunto de datos con 50 características, junto con el atributo de destino "Col1". La figura siguiente muestra el flujo de este experimento y los parámetros de entrada:

![Ejemplo de selección de características](./media/select-features/feature-Selection1.png)

La figura siguiente muestra los conjuntos de datos resultantes:

![Ejemplo de selección de características](./media/select-features/feature-Selection2.png)

Cada características recibe una puntuación según la correlación de Pearson entre sí misma y el atributo de destino "Col1". Las características con las mayores puntuaciones se conservan.

La figura siguiente muestra las puntuaciones correspondientes de las características seleccionadas:

![Ejemplo de selección de características](./media/select-features/feature-Selection3.png)

A través de la aplicación de este módulo de [selección de características basada en filtro][filter-based-feature-selection], se seleccionan 50 de las 256 características, debido a que tienen las características más correlacionadas con la variable de destino "Col1", según el método de puntuación "Correlación de Pearson".

## <a name="conclusion"></a>Conclusión
La selección de características y la ingeniería de características son dos características  diseñadas y seleccionadas frecuentemente que aumentan la eficiencia del proceso de entrenamiento en el que se intenta extraer la información clave que contienen los datos. También mejoran la eficacia de estos modelos para clasificar los datos de entrada de manera precisa y para predecir resultados de interés de manera más sólida. El diseño y la selección de características también se pueden combinar para que sea posible hacer un mejor seguimiento computacional del aprendizaje. Para ello, mejora y luego reduce el número de características que se necesitan para calibrar o entrenar un modelo. Matemáticamente hablando, las características seleccionadas para entrenar el modelo son un conjunto mínimo de variables independientes que explican los patrones existentes en los datos y, a continuación, predicen correctamente los resultados.

No siempre es necesario realizar el diseño o la selección de funciones. Esta necesidad depende de los datos que se hayan recopilado, del algoritmo seleccionado y del objetivo del experimento.

<!-- Module References -->
[feature-hashing]: https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/
[filter-based-feature-selection]: https://msdn.microsoft.com/library/azure/918b356b-045c-412b-aa12-94a1d2dad90f/
[fisher-linear-discriminant-analysis]: https://msdn.microsoft.com/library/azure/dcaab0b2-59ca-4bec-bb66-79fd23540080/

