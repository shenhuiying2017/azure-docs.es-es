---
title: "Cómo elegir algoritmos de aprendizaje automático| Microsoft Docs"
description: "Cómo elegir algoritmos de Azure Machine Learning para el aprendizaje supervisado y no supervisado en experimentos de agrupación en clústeres, clasificación o regresión."
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
tags: 
ms.assetid: a3b23d7f-f083-49c4-b6b1-3911cd69f1b4
ms.service: machine-learning
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 12/18/2017
ms.author: garye
ms.openlocfilehash: 1b30e4dbf20cac653c323720de779aa5f8edba68
ms.sourcegitcommit: 9a8b9a24d67ba7b779fa34e67d7f2b45c941785e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/08/2018
---
# <a name="how-to-choose-algorithms-for-microsoft-azure-machine-learning"></a>Cómo elegir algoritmos para Microsoft Azure Machine Learning
La respuesta a la pregunta "¿qué algoritmo de aprendizaje automático debería usar?" siempre es "Depende". Depende del tamaño, la calidad y la naturaleza de los datos. Depende de qué desea hacer con la respuesta. Depende de cómo se hayan traducido los cálculos del algoritmo en instrucciones para el equipo que está usando. Y también depende del tiempo de que disponga. Ni siquiera los científicos con más experiencia en datos pueden determinar qué algoritmo funcionará mejor antes de probarlos.

## <a name="the-machine-learning-algorithm-cheat-sheet"></a>Hoja de referencia rápida de algoritmos de Machine Learning
La **Hoja de referencia rápida de algoritmos de Microsoft Azure Machine Learning** ayuda a elegir el algoritmo de aprendizaje automático adecuado para sus soluciones de análisis predictivos de la biblioteca de algoritmos de Microsoft Azure Machine Learning.
En este artículo se explica cómo usarla.

> [!NOTE]
> Para descargar la hoja de referencia rápida y usarla con las instrucciones de este artículo, consulte la [hoja de referencia rápida de algoritmos de aprendizaje automático de Microsoft Azure Machine Learning Studio](algorithm-cheat-sheet.md).
> 
> 

Esta hoja de referencia rápida está pensada para un público muy específico: científicos de datos principiantes con conocimientos de aprendizaje automático de nivel universitario que intentan elegir un algoritmo para empezar en Azure Machine Learning Studio. Eso significa que en ella se hacen algunas generalizaciones y simplificaciones exageradas, pero le sirve para orientarse bien. También significa que hay muchos algoritmos que no están incluidos aquí. A medida que Azure Machine Learning vaya creciendo y abarcando un conjunto más completo de métodos disponibles, iremos agregándolos.

Estas recomendaciones son una recopilación de los comentarios y las sugerencias de muchos científicos de datos y expertos en aprendizaje automático. No estuvimos de acuerdo en todo, pero intentamos combinar las opiniones para llegar a un consenso general. La mayoría de los argumentos de desacuerdo comienzan con "Depende...".

### <a name="how-to-use-the-cheat-sheet"></a>Cómo usar la hoja de referencia rápida
Lea las etiquetas de ruta de acceso y algoritmo del gráfico con el siguiente formato: "Para *&lt;etiqueta de ruta de acceso&gt;*, use *&lt;algoritmo&gt;*". Por ejemplo, "Para *velocidad*, use la *regresión logística de dos clases*". Ciertas veces, se aplica más de una rama.
Otras, ninguna de ellas es la ideal. Tienen la finalidad de ser recomendaciones generales, así que no se preocupe si no son exactas.
Varios de los científicos de datos con los que hablé dijeron que la única forma de encontrar el mejor algoritmo es probarlos todos.

Este es un ejemplo de la [Galería de Azure AI](http://gallery.cortanaintelligence.com/) de un experimento en el que se prueban varios algoritmos con los mismos datos y se comparan los resultados: [Compare Multi-class Classifiers: Letter recognition](http://gallery.cortanaintelligence.com/Details/a635502fc98b402a890efe21cec65b92) (Comparación de clasificadores multiclase: reconocimiento de letras).

> [!TIP]
> Para descargar e imprimir un diagrama con información general de las funcionalidades de Machine Learning Studio, vea [Diagrama de información general de las funcionalidades de Azure Machine Learning Studio](studio-overview-diagram.md).
> 
> 

## <a name="flavors-of-machine-learning"></a>Variantes del aprendizaje automático
### <a name="supervised"></a>Supervisado
Los algoritmos de aprendizaje supervisado hacen predicciones basadas en un conjunto de ejemplos. Por ejemplo, los precios históricos de las acciones pueden usarse para hacer estimaciones aventuradas de los precios futuros. Cada ejemplo usado para el entrenamiento se etiqueta con el valor de interés; en este caso, el precio de las acciones. Un algoritmo de aprendizaje supervisado busca patrones en esas etiquetas de valor. Puede usar cualquier información que pueda ser relevante, como el día de la semana, la temporada, datos financieros de la empresa, el tipo de sector o la presencia de eventos geopolíticos perjudiciales, y cada algoritmo busca tipos diferentes de patrones. Una vez que el algoritmo encuentra el mejor patrón posible, lo usa para hacer predicciones de datos de prueba sin etiquetar; en este caso, los precios futuros.

El aprendizaje supervisado es un tipo conocido y útil de aprendizaje automático. Con una única excepción: todos los módulos de Azure Machine Learning son algoritmos de aprendizaje supervisado. Hay varios tipos específicos de aprendizaje supervisado representados en Azure Machine Learning: la clasificación, la regresión y la detección de anomalías.

* **Clasificación**. Cuando los datos se usan para predecir una categoría, el aprendizaje supervisado también se denomina clasificación. Esto ocurre cuando se asigna una imagen, como una foto de un 'gato' o un 'perro'. Cuando hay solo dos opciones, se denomina clasificación **de dos clases** o **binomial**. Cuando hay más categorías, como cuando se predice el ganador del torneo March Madness de la NCAA, este problema se conoce como **clasificación multiclase**.
* **Regresión**. Cuando se predice un valor, como el precio de las acciones, el aprendizaje supervisado se denomina regresión.
* **Detección de anomalías**. A veces, el objetivo es identificar puntos de datos que simplemente no son habituales. En la detección de fraudes, por ejemplo, los patrones de gasto de tarjeta de crédito muy poco habituales son sospechosos. Las posibles variaciones son tan numerosas y los ejemplos de formación son tan pocos, que no es posible saber de qué actividad fraudulenta se trata. El enfoque que toma la detección de anomalías es simplemente aprender qué puede considerarse como actividad normal (haciendo uso de las transacciones no fraudulentas del historial) e identificar todo lo que sea significativamente diferente.

### <a name="unsupervised"></a>Sin supervisar
En el aprendizaje sin supervisar, los puntos de datos no tienen etiquetas asociadas a ellos. En su lugar, el objetivo de un algoritmo de aprendizaje sin supervisar es organizar los datos de alguna manera o describir su estructura. Esto puede significar agruparlos en clústeres o buscar diferentes maneras de examinar datos complejos para que parezcan más simples o más organizados.

### <a name="reinforcement-learning"></a>Aprendizaje de refuerzo
En el aprendizaje de refuerzo, el algoritmo elige una acción en respuesta a cada punto de datos. El algoritmo de aprendizaje también recibe una señal de recompensa un poco más adelante, que indica cómo de buena fue la decisión.
Según esto, el algoritmo modifica su estrategia para lograr la mayor recompensa. Actualmente no hay ningún módulo de algoritmo de aprendizaje de refuerzo en Azure Machine Learning. El aprendizaje de refuerzo es común en robótica, donde el conjunto de lecturas del sensor en un punto en el tiempo es un punto de datos, y el algoritmo debe elegir la siguiente acción del robot. También resulta perfecto para las aplicaciones de Internet de las cosas.

## <a name="considerations-when-choosing-an-algorithm"></a>Consideraciones al elegir un algoritmo
### <a name="accuracy"></a>Precisión
No siempre es necesario obtener la respuesta más precisa posible.
A veces, una aproximación ya es útil, según para qué se la desee usar. Si es así, puede reducir el tiempo de procesamiento de forma considerable al usar métodos más aproximados. Otra ventaja de los métodos más aproximados es que tienden naturalmente a evitar el [sobreajuste](https://youtu.be/DQWI1kvmwRg).

### <a name="training-time"></a>Tiempo de entrenamiento
La cantidad de minutos u horas necesarios para entrenar un modelo varía mucho según el algoritmo. A menudo, el tiempo de entrenamiento depende de la precisión (generalmente, uno determina al otro). Además, algunos algoritmos son más sensibles a la cantidad de puntos de datos que otros.
Si el tiempo es limitado, esto puede determinar la elección del algoritmo, especialmente cuando el conjunto de datos es grande.

### <a name="linearity"></a>Linealidad
Muchos algoritmos de aprendizaje automático hacen uso de la linealidad. Los algoritmos de clasificación lineal suponen que las clases pueden estar separadas mediante una línea recta (o su análogo de mayores dimensiones). Entre ellos, se encuentran la regresión logística y las máquinas de vectores de soporte (como las que se implementan en Azure Machine Learning).
Los algoritmos de regresión lineal suponen que las tendencias de datos siguen una línea recta. Estas suposiciones no son incorrectas para algunos problemas, pero en otros disminuyen la precisión.

![Límite de clase no lineal][1]

***Límite de clase no lineal****: la dependencia de un algoritmo de clasificación lineal se traduciría en baja precisión*.

![Datos con tendencia no lineal][2]

***Datos con tendencia no lineal****: el uso de un método de regresión lineal generaría errores mucho mayores que los necesarios*

A pesar de los riesgos, los algoritmos lineales son muy populares como primera línea de ataque. Tienden a ser algorítmicamente sencillos y rápidos para entrenar.

### <a name="number-of-parameters"></a>Cantidad de parámetros
Los parámetros son los botones que un científico de datos activa al configurar un algoritmo. Son números que afectan al comportamiento del algoritmo, como la tolerancia a errores o la cantidad de iteraciones, o bien opciones de variantes de comportamiento del algoritmo. El tiempo de entrenamiento y la precisión del algoritmo a veces pueden ser muy sensibles y requerir solo la configuración correcta. Normalmente, los algoritmos con parámetros de números grandes requieren la mayor cantidad de pruebas y errores posible para encontrar una buena combinación.

También puede haber un bloque de módulos de [barrido de parámetros](algorithm-parameters-optimize.md) en Azure Machine Learning que prueba automáticamente todas las combinaciones de parámetros en cualquier granularidad que se elija. Aunque esta es una excelente manera de asegurarse de que se ha distribuido el espacio de parámetros, el tiempo necesario para entrenar un modelo aumenta de manera exponencial con la cantidad de parámetros.

La ventaja es que tener muchos parámetros normalmente indica que un algoritmo tiene mayor flexibilidad. A menudo, puede lograr una precisión muy alta. Siempre y cuando se encuentre la combinación correcta de configuraciones de parámetros.

### <a name="number-of-features"></a>Cantidad de características
Para ciertos tipos de datos, la cantidad de características puede ser muy grande en comparación con la cantidad de puntos de datos. Este suele ser el caso de la genética o los datos textuales. Una gran cantidad de características puede trabar algunos algoritmos de aprendizaje y provocar que el tiempo de entrenamiento sea demasiado largo. Las máquinas de vectores de soporte son especialmente adecuadas para este caso (ver abajo).

### <a name="special-cases"></a>Casos especiales
Algunos algoritmos de aprendizaje hacen determinadas suposiciones sobre la estructura de los datos o los resultados deseados. Si encuentra uno que se ajuste a sus necesidades, este puede ofrecerle resultados más útiles, predicciones más precisas o tiempos de entrenamiento más cortos.

| **Algoritmo** | **Precisión** | **Tiempo de entrenamiento** | **Linealidad** | **Parámetros** | **Notas** |
| --- |:---:|:---:|:---:|:---:| --- |
| **Clasificación multiclase** | | | | | |
| [regresión logística](https://msdn.microsoft.com/library/azure/dn905994.aspx) | |● |● |5 | |
| [bosque de decisión](https://msdn.microsoft.com/library/azure/dn906008.aspx) |● |○ | |6 | |
| [selva de decisión](https://msdn.microsoft.com/library/azure/dn905976.aspx) |● |○ | |6 |Uso de memoria bajo |
| [árbol de decisión impulsado](https://msdn.microsoft.com/library/azure/dn906025.aspx) |● |○ | |6 |Uso de memoria grande |
| [red neuronal](https://msdn.microsoft.com/library/azure/dn905947.aspx) |● | | |9 |[La personalización adicional es posible](http://go.microsoft.com/fwlink/?LinkId=402867) |
| [perceptrón promedio](https://msdn.microsoft.com/library/azure/dn906036.aspx) |○ |○ |● |4 | |
| [máquina de vectores de soporte](https://msdn.microsoft.com/library/azure/dn905835.aspx) | |○ |● |5 |Útil para conjuntos de características de gran tamaño |
| [máquina de vectores de soporte localmente profunda](https://msdn.microsoft.com/library/azure/dn913070.aspx) |○ | | |8 |Útil para conjuntos de características de gran tamaño |
| [máquina del punto de Bayes](https://msdn.microsoft.com/library/azure/dn905930.aspx) | |○ |● |3 | |
| **Clasificación multiclase** | | | | | |
| [regresión logística](https://msdn.microsoft.com/library/azure/dn905853.aspx) | |● |● |5 | |
| [bosque de decisión](https://msdn.microsoft.com/library/azure/dn906015.aspx) |● |○ | |6 | |
| [selva de decisión ](https://msdn.microsoft.com/library/azure/dn905963.aspx) |● |○ | |6 |Uso de memoria bajo |
| [red neuronal](https://msdn.microsoft.com/library/azure/dn906030.aspx) |● | | |9 |[La personalización adicional es posible](http://go.microsoft.com/fwlink/?LinkId=402867) |
| [uno frente a todos](https://msdn.microsoft.com/library/azure/dn905887.aspx) |- |- |- |- |Vea las propiedades del método de dos clases seleccionado |
| **Regresión** | | | | | |
| [lineal](https://msdn.microsoft.com/library/azure/dn905978.aspx) | |● |● |4 | |
| [Bayesiano lineal](https://msdn.microsoft.com/library/azure/dn906022.aspx) | |○ |● |2 | |
| [bosque de decisión](https://msdn.microsoft.com/library/azure/dn905862.aspx) |● |○ | |6 | |
| [árbol de decisión impulsado](https://msdn.microsoft.com/library/azure/dn905801.aspx) |● |○ | |5 |Uso de memoria grande |
| [cuantil de bosque rápido](https://msdn.microsoft.com/library/azure/dn913093.aspx) |● |○ | |9 |Distribuciones en lugar de predicciones de puntos |
| [red neuronal](https://msdn.microsoft.com/library/azure/dn905924.aspx) |● | | |9 |[La personalización adicional es posible](http://go.microsoft.com/fwlink/?LinkId=402867) |
| [Poisson](https://msdn.microsoft.com/library/azure/dn905988.aspx) | | |● |5 |Logarítmico lineal técnicamente. Para la predicción de recuentos |
| [ordinal](https://msdn.microsoft.com/library/azure/dn906029.aspx) | | | |0 |Para la predicción de ordenación de clasificaciones |
| **Detección de anomalías** | | | | | |
| [máquina de vectores de soporte](https://msdn.microsoft.com/library/azure/dn913103.aspx) |○ |○ | |2 |Especialmente útil para conjuntos de características de gran tamaño |
| [Detección de anomalías basada en PCA](https://msdn.microsoft.com/library/azure/dn913102.aspx) | |○ |● |3 | |
| [K-Means](https://msdn.microsoft.com/library/azure/5049a09b-bd90-4c4e-9b46-7c87e3a36810/) | |○ |● |4 |Un algoritmo de agrupación en clústeres |

**Propiedades de algoritmo:**

**●** : muestra una precisión excelente, tiempos de entrenamiento breves y uso de linealidad

**○** : muestra buena precisión y tiempos de entrenamiento moderados

## <a name="algorithm-notes"></a>Notas sobre los algoritmos
### <a name="linear-regression"></a>Regresión lineal
Como se mencionó anteriormente, la [regresión lineal](https://msdn.microsoft.com/library/azure/dn905978.aspx) ajusta una línea (o plano o hiperplano) al conjunto de datos. Es potente, simple y rápida, pero puede ser demasiado simple para algunos problemas.
Entre aquí para ver un [tutorial sobre regresión lineal](linear-regression-in-azure.md).

![Datos con tendencia lineal][3]

***Datos con tendencia lineal***

### <a name="logistic-regression"></a>Regresión logística
Aunque la inclusión de la palabra “regresión” en el nombre se preste a confusión, la regresión logística es en realidad una herramienta eficaz para la clasificación [de dos clases](https://msdn.microsoft.com/library/azure/dn905994.aspx) y [multiclase](https://msdn.microsoft.com/library/azure/dn905853.aspx). Es rápida y sencilla. El hecho de que use una curva con forma de S en lugar de una línea recta la hace ideal para dividir los datos en grupos. La regresión logística proporciona límites de clase lineal. Por lo tanto, cuando la use, asegúrese de que la aproximación lineal le es útil.

![Regresión logística a datos de dos clases con una sola característica][4]

***Regresión logística a datos de dos clases con una sola característica****: el límite de clase es el punto en el que la curva logística tiene la misma distancia hacia ambas clases*

### <a name="trees-forests-and-jungles"></a>Árboles, bosques y selvas
Los bosques de decisión ([regresión](https://msdn.microsoft.com/library/azure/dn905862.aspx), [dos clases](https://msdn.microsoft.com/library/azure/dn906008.aspx) y [multiclase](https://msdn.microsoft.com/library/azure/dn906015.aspx)), las selvas de decisión ([dos clases](https://msdn.microsoft.com/library/azure/dn905976.aspx) y [multiclase](https://msdn.microsoft.com/library/azure/dn905963.aspx)) y los árboles de decisión impulsados ([regresión](https://msdn.microsoft.com/library/azure/dn905801.aspx) y [dos clases](https://msdn.microsoft.com/library/azure/dn906025.aspx)) se basan en árboles de decisión, un concepto fundamental del aprendizaje automático. Hay muchas variantes de árboles de decisión, pero todas ellas hacen lo mismo: subdividir el espacio de características en regiones con casi siempre la misma etiqueta. Estas pueden ser regiones de la misma categoría o de un valor constante, según si se está realizando una clasificación o una regresión.

![El árbol de decisión subdivide un espacio de características][5]

***Un árbol de decisión subdivide un espacio de características en regiones de valores más o menos uniformes***

Ya que un espacio de características se puede subdividir en regiones arbitrariamente pequeñas, es fácil imaginar dividirlo lo suficiente como para tener un punto de datos por región. Se trata de un ejemplo extremo de sobreajuste. Para evitar esto, se genera un conjunto grande de árboles con precisión matemática para que los árboles no se correlacionen. El resultado promedio de este "bosque de decisión" es un árbol que evita el sobreajuste. Los bosques de decisión pueden llegar a usar demasiada memoria. Las selvas de decisión son una variante que consume menos memoria a costa de un tiempo de entrenamiento ligeramente más prolongado.

Los árboles de decisión impulsados evitan el sobreajuste al limitar la cantidad de veces que pueden subdividir y la cantidad de puntos de datos que se permiten en cada región. El algoritmo genera una secuencia de árboles, cada uno de los cuales aprende a compensar el error que deja el árbol anterior. El resultado es un lector muy preciso que tiende a usar una gran cantidad de memoria. Para obtener la descripción técnica completa, vea el [documento original de Friedman](http://www-stat.stanford.edu/~jhf/ftp/trebst.pdf).

[La regresión rápida de bosque por cuantiles](https://msdn.microsoft.com/library/azure/dn913093.aspx) es una variación de los árboles de decisión para los casos especiales en los que se desea conocer no solo el valor típico (medio) de los datos dentro de una región, sino también su distribución en forma de cuantiles.

### <a name="neural-networks-and-perceptrons"></a>Redes neuronales y perceptrones
Las redes neuronales son algoritmos de aprendizaje inspirados en el cerebro que abarcan problemas [multiclase](https://msdn.microsoft.com/library/azure/dn906030.aspx), [de dos clases](https://msdn.microsoft.com/library/azure/dn905947.aspx) y [de regresión](https://msdn.microsoft.com/library/azure/dn905924.aspx). Vienen en una variedad infinita, pero las redes neuronales de Azure Machine Learning tienen todas la forma de gráficos acíclicos dirigidos. Esto significa que las características de entrada se adelantan (nunca se atrasan) en una secuencia de capas antes convertirse en salidas. En cada capa, las entradas se ponderan en varias combinaciones, se suman y se pasan a la siguiente capa. Esta combinación de cálculos sencillos da como resultado la capacidad de aprender límites de clase y tendencias de datos sofisticados, casi como por arte de magia. Las redes de varias capas de este tipo realizan el "aprendizaje profundo" que hace posibles los informes de tecnología y la ciencia ficción.

Sin embargo, el alto rendimiento no es gratuito. Las redes neuronales pueden tardar mucho tiempo para entrenarse, especialmente para grandes conjuntos de datos con muchas características. También tienen más parámetros que la mayoría de los algoritmos, lo que implica que el barrido de parámetros alargue mucho el tiempo de entrenamiento.
Para los que quieren obtener resultados óptimos y [especificar su propia estructura de red](http://go.microsoft.com/fwlink/?LinkId=402867), las posibilidades son inagotables.

![Límites que aprenden las redes neuronales][6]
***Los límites que aprenden las redes neuronales pueden ser complejos e irregulares***

El [perceptrón promedio de dos clases](https://msdn.microsoft.com/library/azure/dn906036.aspx) es la respuesta de las redes neuronales para acortar en gran medida los tiempos de entrenamiento. Este perceptrón usa una estructura de red que proporciona límites de clase lineal. Es casi primitivo según los estándares actuales, pero tiene un largo historial de trabajo sólido y es lo suficientemente pequeño como para aprender rápidamente.

### <a name="svms"></a>SVM
Las máquinas de vectores de soporte (SVM) buscan el límite que separa las clases con el mayor margen posible. Cuando no se pueden separar bien las dos clases, los algoritmos buscan el mejor límite que pueden. Tal como está escrito en Azure Machine Learning, la [SVM de dos clases](https://msdn.microsoft.com/library/azure/dn905835.aspx) hace esto solo con una línea recta. (En el idioma de SVM, usa un kernel lineal). Gracias a que hace esta aproximación lineal, se puede ejecutar con bastante rapidez. Donde realmente se destaca, es con datos de muchas características, como texto o genómica. En estos casos, las SVM pueden separar las clases más rápidamente y con menos sobreajuste que con la mayoría de los otros algoritmos, además de que requieren solo una pequeña cantidad de memoria.

![Límite de clase de máquina de vectores de soporte][7]

***Un límite de clase de máquina de vectores de soporte maximiza el margen que separa las dos clases***

Como otro producto de Microsoft Research, la [SVM de dos clases localmente profunda](https://msdn.microsoft.com/library/azure/dn913070.aspx) es una variante no lineal de SVM que mantiene casi toda la velocidad y la eficiencia de memoria de la versión lineal. Es ideal para los casos en los que el enfoque lineal no proporciona respuestas suficientemente precisas. Para mantener su velocidad, los desarrolladores dividieron el problema en un grupo de pequeños problemas de SVM lineales. Lea la [descripción completa](http://research.microsoft.com/um/people/manik/pubs/Jose13.pdf) para obtener detalles sobre cómo lograron este truco.

Mediante una extensión inteligente de SVM no lineales, la [SVM de una clase](https://msdn.microsoft.com/library/azure/dn913103.aspx) dibuja un límite que demarca con precisión todo el conjunto de datos. Es útil para la detección de anomalías. Los puntos de datos nuevos que quedan fuera de ese límite son tan poco habituales que requieren atención.

### <a name="bayesian-methods"></a>Métodos bayesianos
Los métodos bayesianos tienen una calidad muy deseable: evitan el sobreajuste. Para ello, hacen algunas suposiciones anticipadas sobre la posible distribución de la respuesta. Otra característica de este enfoque es que tienen muy pocos parámetros. Azure Machine Learning tiene dos algoritmos bayesianos tanto para la clasificación ([automática de puntos de Bayes de dos clases](https://msdn.microsoft.com/library/azure/dn905930.aspx)) como para la regresión ([regresión lineal bayesiana](https://msdn.microsoft.com/library/azure/dn906022.aspx)).
Tenga en cuenta que se asume que los datos se pueden dividir o encajan en una línea recta.

Como nota histórica, se desarrollaron las máquinas de puntos de Bayes en Microsoft Research. Presentan un trabajo teórico excepcional. Al alumno interesado se le dirige al [artículo original de JMLR](http://jmlr.org/papers/volume1/herbrich01a/herbrich01a.pdf) y a un [blog revelador de Chris Bishop](http://blogs.technet.com/b/machinelearning/archive/2014/10/30/embracing-uncertainty-probabilistic-inference.aspx).

### <a name="specialized-algorithms"></a>Algoritmos especializados
Si tiene un objetivo muy específico, puede que sea su día de suerte. Dentro de la colección de Azure Machine Learning, existen algoritmos que se especializan en:

- predicción de rangos ([regresión ordinal](https://msdn.microsoft.com/library/azure/dn906029.aspx)),
- predicción de totales ([regresión de Poisson](https://msdn.microsoft.com/library/azure/dn905988.aspx)),
- detección de anomalías (uno basado en el [análisis de componentes principales](https://msdn.microsoft.com/library/azure/dn913102.aspx) y otro en [máquinas de vectores de soporte](https://msdn.microsoft.com/library/azure/dn913103.aspx))
- agrupación en clústeres ([K-Means](https://msdn.microsoft.com/library/azure/5049a09b-bd90-4c4e-9b46-7c87e3a36810/))

![Detección de anomalías basada en PCA][8]

***Detección de anomalías basada en PCA****: la inmensa mayoría de los datos entra en una distribución típica; los puntos que se desvían mucho de esa distribución son sospechosos*

![Conjunto de datos agrupados mediante K-Means][9]

***Un conjunto de datos se agrupa en 5 clústeres mediante K-Means***

También hay un [clasificador multiclase uno contra todos](https://msdn.microsoft.com/library/azure/dn905887.aspx)que divide el problema de clasificación de clase N en problemas de clasificación de dos clases N-1. La precisión, el tiempo de entrenamiento y las propiedades de linealidad se determinan con los clasificadores de dos clases que se usan.

![Clasificadores de dos clases combinados para formar un clasificador de tres clases][10]

***Un par de clasificadores de dos clases se combinan para formar un clasificador de tres clases***

Azure Machine Learning también incluye acceso a un marco de Machine Learning eficaz con el título [Vowpal Wabbit](https://msdn.microsoft.com/library/azure/8383eb49-c0a3-45db-95c8-eb56a1fef5bf).
VW es un reto a la categorización, ya que puede aprender problemas tanto de clasificación como de regresión, e incluso puede aprender a partir de datos parcialmente etiquetados. Puede configurarlo para usar algoritmos de aprendizaje, funciones de pérdida y algoritmos de optimización. Está totalmente diseñado para ser eficiente, paralelo y extremadamente rápido. Administra enormes conjuntos de características con mucha facilidad.
VW, iniciado y liderado por el propio John Langford de Microsoft Research, es una entrada de Fórmula Uno en un campo de algoritmos de coches de línea. No todos los problemas se adaptan a VW, pero si el suyo lo hace, es posible que valga la pena que aumente la curva de aprendizaje en esa interfaz. También está disponible como [código fuente abierto independiente](https://github.com/JohnLangford/vowpal_wabbit) en varios idiomas.

## <a name="more-help-with-algorithms"></a>Más ayuda con los algoritmos
* Para una infografía descargable que describe los algoritmos y proporciona ejemplos, consulte [Infografía descargable: Conceptos básicos de aprendizaje automático con ejemplos de algoritmos](basics-infographic-with-algorithm-examples.md).
* Para ver una lista por categoría de todos los algoritmos disponibles de aprendizaje automático en Machine Learning Studio de Azure, consulte [Initialize Model][initialize-model] en la Ayuda de módulos y algoritmos de Machine Learning Studio.
* Para ver una lista completa de todos los algoritmos de Machine Learning Studio, consulte [A-Z list of Machine Learning Studio modules][a-z-list] (Lista de la A a la Z de módulos de Machine Learning Studio) en la Ayuda de módulos y algoritmos de Machine Learning Studio.
* Para descargar e imprimir un diagrama con información general de las funcionalidades de Azure Machine Learning Studio, consulte [Diagrama de información general de las funcionalidades de Azure Machine Learning Studio](studio-overview-diagram.md).


<!-- Reference links -->
[initialize-model]: https://msdn.microsoft.com/library/azure/dn905812.aspx
[a-z-list]: https://msdn.microsoft.com/library/azure/dn906033.aspx

<!-- Media -->

[1]: ./media/algorithm-choice/image1.png
[2]: ./media/algorithm-choice/image2.png
[3]: ./media/algorithm-choice/image3.png
[4]: ./media/algorithm-choice/image4.png
[5]: ./media/algorithm-choice/image5.png
[6]: ./media/algorithm-choice/image6.png
[7]: ./media/algorithm-choice/image7.png
[8]: ./media/algorithm-choice/image8.png
[9]: ./media/algorithm-choice/image9.png
[10]: ./media/algorithm-choice/image10.png
