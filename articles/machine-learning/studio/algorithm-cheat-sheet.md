---
title: "Hoja de referencia rápida de algoritmos de aprendizaje automático | Microsoft Docs"
description: "Una hoja de referencia rápida de algoritmos de aprendizaje automático que puede imprimirse le ayudará a elegir el algoritmo correcto para el modelo de predicción en  Azure Machine Learning Studio."
keywords: algorithm cheat sheet,cheat sheet,machine learning algorithm
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: e1dc31ec-1acb-463f-ba77-de565d4ddf4d
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/18/2017
ms.author: garye
ms.openlocfilehash: 5ecd569f68c2eada921193f2b7b88fe788120a9b
ms.sourcegitcommit: 9a8b9a24d67ba7b779fa34e67d7f2b45c941785e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/08/2018
---
# <a name="machine-learning-algorithm-cheat-sheet-for-microsoft-azure-machine-learning-studio"></a>Hoja de referencia rápida de algoritmos de aprendizaje automático de Microsoft Azure Machine Learning Studio
La **hoja de referencia rápida de algoritmos de Microsoft Azure Machine Learning** le ayuda a elegir el algoritmo correcto para un modelo de análisis predictivo.

[Azure Machine Learning Studio](https://studio.azureml.net/) tiene una gran biblioteca de algoritmos de las familias de ***regresión***, ***clasificación***, ***agrupación en clústeres*** y ***detección de anomalías***. Cada uno de ellos está diseñado para resolver un tipo de problema de aprendizaje automático diferente.

## <a name="download-machine-learning-algorithm-cheat-sheet"></a>Descarga: Hoja de referencia rápida de algoritmos de aprendizaje automático
**Descargue la hoja de referencia rápida aquí: [Hoja de referencia rápida de algoritmos de Machine Learning](http://download.microsoft.com/download/A/6/1/A613E11E-8F9C-424A-B99D-65344785C288/microsoft-machine-learning-algorithm-cheat-sheet-v6.pdf)**

![Hoja de referencia rápida de algoritmos de Machine Learning: elección de un algoritmo de Machine Learning.][cheat-sheet]

[cheat-sheet]: ./media/algorithm-cheat-sheet/machine-learning-algorithm-cheat-sheet-small_v_0_6-01.png

Descargue e imprima la hoja de referencia rápida de algoritmos de Machine Learning en tamaño tabloide para tenerlo a mano y que le sirva de ayuda al elegir un algoritmo.

> [!NOTE]
> Para obtener una guía detallada del uso de esta hoja de referencia rápida, consulte el artículo [Selección de algoritmos para Microsoft Azure Machine Learning](algorithm-choice.md).
> 
> 

## <a name="more-help-with-algorithms"></a>Más ayuda con los algoritmos
* Para obtener ayuda para utilizar esta hoja de referencia rápida para elegir el algoritmo correcto y conocer más sobre los distintos tipos de algoritmos de aprendizaje automático y cómo se usan, consulte [Cómo elegir algoritmos para Microsoft Azure Machine Learning](algorithm-choice.md).
* Para obtener una infografía descargable que describe los algoritmos y proporciona ejemplos, vea [Infografía descargable: Conceptos básicos de aprendizaje automático con ejemplos de algoritmos](basics-infographic-with-algorithm-examples.md).
* Para ver una lista por categoría de todos los algoritmos disponibles de aprendizaje automático en Machine Learning Studio, consulte [Inicializar modelo][initialize-model] en la Ayuda de módulos y algoritmos de Machine Learning Studio.
* Para ver una lista completa de todos los algoritmos de Machine Learning Studio, consulte [Lista de la A a la Z de módulos de Machine Learning Studio][a-z-list] (Lista de la A a la Z de módulos de Machine Learning Studio) en la Ayuda de módulos y algoritmos de Machine Learning Studio.
* Para descargar e imprimir un diagrama con información general de las funcionalidades de Machine Learning Studio, vea [Diagrama de información general de las funcionalidades de Azure Machine Learning Studio](studio-overview-diagram.md).

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="notes-and-terminology-definitions-for-the-machine-learning-algorithm-cheat-sheet"></a>Notas y definiciones de terminología de la hoja de referencia rápida de algoritmos de aprendizaje automático

* Las sugerencias ofrecidas en esta hoja de referencia rápida de algoritmos son reglas generales aproximadas. Algunas se pueden ignorar y otras se pueden infringir de forma fragante. Su finalidad es sugerir un punto de partida. No tema ejecutar una competición directa entre varios algoritmos en sus datos. Realmente no hay sustituto para comprender los principios de cada algoritmo y el sistema que generó los datos.

* Cada algoritmo de aprendizaje automático tiene su propio estilo o *sesgo inductivo*. Para un problema específico, varios algoritmos pueden ser apropiados y un algoritmo puede servir mejor que otros. Pero no siempre es posible saber de antemano cuál es la mejor opción. En estos casos, varios algoritmos se muestran juntos en la hoja de referencia rápida. Una estrategia adecuada sería probar un algoritmo y, si los resultados no resultan satisfactorios, probar los demás. Este es un ejemplo de la [Galería de Azure AI](http://gallery.cortanaintelligence.com/) de un experimento en el que se prueban varios algoritmos con los mismos datos y se comparan los resultados: [Compare Multi-class Classifiers: Letter recognition](http://gallery.cortanaintelligence.com/Details/a635502fc98b402a890efe21cec65b92) (Comparación de clasificadores multiclase: reconocimiento de letras).

* Existen tres categorías principales de aprendizaje automático: **aprendizaje supervisado**, **aprendizaje sin supervisar** y **aprendizaje de refuerzo**.

  * En el **aprendizaje supervisado**, cada punto de datos está etiquetado o asociado con una categoría o valor de interés.  Un ejemplo de una etiqueta de categoría es asignar una imagen como un 'gato' o 'perro'.  Un ejemplo de una etiqueta de valor es el precio de venta asociado a un coche usado. El objetivo del aprendizaje supervisado es estudiar muchos ejemplos etiquetados como los anteriores y, luego, poder realizar predicciones sobre los puntos de datos futuros. Por ejemplo, identificar nuevas fotografías con el animal correcto o asignar precios de venta precisos a otros coches usados. Este es un tipo conocido y útil del aprendizaje automático. Todos los módulos de Azure Machine Learning son algoritmos de aprendizaje supervisado, excepto [agrupación en clústeres k-means][k-means-clustering].

  * En el **aprendizaje sin supervisar**, los puntos de datos no tienen etiquetas asociadas a ellos. En su lugar, el objetivo de un algoritmo de aprendizaje sin supervisar es organizar los datos de alguna manera o describir su estructura. Esto puede significar agruparlos en clústeres, como hace K-means, o buscar diferentes maneras de examinar datos complejos para que parezcan más simples.

  * En el **aprendizaje de refuerzo**, el algoritmo elige una acción en respuesta a cada punto de datos. Se trata de un enfoque común en robótica, donde el conjunto de lecturas del sensor en un punto en el tiempo es un punto de datos, y el algoritmo debe elegir la siguiente acción del robot. También resulta perfecto para las aplicaciones de Internet de las cosas. El algoritmo de aprendizaje también recibe una señal de recompensa un poco más adelante, que indica cómo de buena fue la decisión. Según esto, el algoritmo modifica su estrategia para lograr la mayor recompensa. Actualmente no hay ningún módulo de algoritmo de aprendizaje de refuerzo en Aprendizaje automático de Azure.

* Los **métodos bayesianos** se basan en el supuesto de puntos de datos estadísticamente independientes. Esto significa que la variabilidad sin modelar en un punto de datos no se correlaciona con otras, es decir, no se puede predecir. Por ejemplo, si los datos que se registran son el número de minutos hasta que llega el próximo tren, dos mediciones tomadas con una diferencia de un día son estadísticamente independientes. Sin embargo, dos mediciones tomadas con una diferencia de un minuto no son estadísticamente independientes: el valor de una es altamente predictivo del valor de la otra.

* La **regresión del árbol de decisión incrementado** aprovecha la superposición de características o la interacción entre ellas. Esto significa que, en cualquier punto de datos dado, el valor de una característica es en cierto modo predictiva del valor de otra.  Por ejemplo, en datos de temperatura diaria alta o baja, saber la temperatura baja del día permite realizar una estimación razonable de la alta. La información contenida en las dos características es en cierto modo redundante.

* Se pueden clasificar los datos en más de una categoría, bien mediante un clasificador multiclase por naturaleza, o combinando un conjunto de clasificadores de dos clases en un **conjunto**. En el enfoque de conjunto, hay un clasificador de dos clases distinto para cada clase y cada uno separa los datos en dos categorías: "esta clase" y "esta clase no". Luego, estos clasificadores votan la asignación correcta del punto de datos. Este es el principio operativo que subyace a [Multiclase uno contra todos][one-vs-all-multiclass].

* Varios métodos, como la regresión logística y la máquina del punto de Bayes, suponen **límites de clase lineal**. Es decir, dan por hecho que los límites entre las clases son aproximadamente rectas (o hiperplanas en el caso más general). Con frecuencia es una característica de los datos que no conoce hasta que intenta separarlos, pero es algo que normalmente se puede aprender visualizándolos de antemano. Si los límites de clase son muy irregulares, quédese con árboles de decisión, selvas de decisión, máquinas de vectores de soporte o redes neurales.

* Las redes neurales pueden usarse con variables de categorías creando una **variable ficticia** para cada categoría y estableciéndola en 1 en aquellos casos en los que se aplique la categoría, o en 0 cuando no sea así.


<!-- This is how you can embed a link in an image in HTML. Don't know how to do this in markdown.
<a href="http://download.microsoft.com/download/A/6/1/A613E11E-8F9C-424A-B99D-65344785C288/microsoft-machine-learning-algorithm-cheat-sheet.pdf">
<img src="C:\Users\garye\azure-docs-pr\articles\media\machine-learning-algorithm-cheat-sheet\cheat-sheet-small.png">
</a>
-->

<!-- Module References -->
[a-z-list]: https://msdn.microsoft.com/library/azure/dn906033.aspx
[initialize-model]: https://msdn.microsoft.com/library/azure/0c67013c-bfbc-428b-87f3-f552d8dd41f6/
[k-means-clustering]: https://msdn.microsoft.com/library/azure/5049a09b-bd90-4c4e-9b46-7c87e3a36810/
[one-vs-all-multiclass]: https://msdn.microsoft.com/library/azure/7191efae-b4b1-4d03-a6f8-7205f87be664/
