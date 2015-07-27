<properties 
	pageTitle="Hoja de referencia rápida de algoritmos de aprendizaje automático | Microsoft Azure" 
	description="Una hoja de referencia rápida de algoritmos de aprendizaje automático que puede imprimirse le ayudará a elegir el algoritmo correcto para el modelo de predicción en Estudio de aprendizaje automático de Azure."
	services="machine-learning"
	documentationCenter="" 
	authors="brohrer" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/18/2015" 
	ms.author="brohrer;garye" />


# Hoja de referencia rápida de algoritmos de aprendizaje automático de Estudio de aprendizaje automático de Microsoft Azure

La **Hoja de referencia rápida de algoritmos de aprendizaje automático de Microsoft Azure** ayuda a elegir el algoritmo de aprendizaje automático adecuado para sus soluciones de análisis predictivos de la biblioteca de algoritmos de aprendizaje automático de Microsoft Azure. La hoja de referencia rápida le hace preguntas sobre la naturaleza de sus datos y el problema que está intentando solucionar, y luego le sugiere que pruebe un algoritmo.

[Estudio de aprendizaje automático de Azure](https://studio.azureml.net/) incluye un gran número de algoritmos de aprendizaje automático para sus soluciones de análisis predictivo. Estos algoritmos se incluyen en las categorías generales de aprendizaje automático de ***regresión***, ***clasificación***, ***agrupación en clústeres*** y ***detección de anomalías***. Cada una de ellas está diseñada para resolver un tipo de problema de aprendizaje automático diferente.



## Descargue la hoja de referencia rápida de algoritmos de aprendizaje automático

Descargue la hoja de referencia rápida de algoritmos de aprendizaje automático para obtener ayuda para comprender cómo elegir un algoritmo de aprendizaje automático para su solución. Para mantenerla cerca, puede imprimir la hoja de referencia en tamaño tabloide (11 x 17 pulg.).

**Descargue aquí la hoja de referencia rápida: [Hoja de referencia rápida de algoritmos de Aprendizaje automático de Microsoft Azure](http://download.microsoft.com/download/A/6/1/A613E11E-8F9C-424A-B99D-65344785C288/microsoft-machine-learning-algorithm-cheat-sheet-v2.pdf)**

![Hoja de referencia rápida de algoritmos de aprendizaje automático: elección de un algoritmo de aprendizaje automático.][cheat-sheet]

[cheat-sheet]: ./media/machine-learning-algorithm-cheat-sheet/machine-learning-algorithm-cheat-sheet-microsoft-azure.png



<!-- This is now covered in the first footnote below
[Azure Machine Learning Studio](https://studio.azureml.net/) gives you the flexibility to experiment - 
try one algorithm, and if you're not satisfied with the results, try another.
Here's an example from the [Azure Machine Learning Gallery](http://gallery.azureml.net) of an experiment that tries several algorithms against the same data and compares the results:
[Compare Multi-class Classifiers: Letter recognition](http://gallery.azureml.net/Details/a635502fc98b402a890efe21cec65b92).
-->

## Más ayuda con los algoritmos 

* Para obtener un examen más profundo de los distintos tipos de algoritmos de aprendizaje automático y cómo se usan, consulte [Selección de un algoritmo de Aprendizaje automático de Azure](machine-learning-algorithm-choice.md).
* Para ver una lista por categoría de todos los algoritmos disponibles de aprendizaje automático en Estudio de aprendizaje automático, consulte [Inicializar modelo][initialize-model] en la Ayuda de módulos y algoritmos de Estudio de aprendizaje automático.
* Para ver una lista completa de todos los algoritmos de Estudio de aprendizaje automático, consulte [Lista de la A a la Z de módulos de Estudio de aprendizaje automático](https://msdn.microsoft.com/library/azure/dn906033.aspx) en la Ayuda de módulos y algoritmos de Estudio de aprendizaje automático.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## Notas y definiciones de terminología de la hoja de referencia rápida de algoritmos de aprendizaje automático

* Las sugerencias ofrecidas en esta hoja de referencia rápida de algoritmos son reglas generales aproximadas. Algunas se pueden ignorar y otras se pueden infringir de forma fragante. Su finalidad es sugerir un punto de partida. No tema ejecutar una competición directa entre varios algoritmos en sus datos. Realmente no hay sustituto para comprender los principios de cada algoritmo y el sistema que generó los datos. 

* Cada algoritmo de aprendizaje automático tiene su propio estilo o *sesgo inductivo*. Para un problema específico, varios algoritmos pueden ser apropiados y un algoritmo puede servir mejor que otros. Pero saber cuál funcionará mejor de antemano no siempre es posible. En estos casos, varios algoritmos se muestran juntos en la hoja de referencia rápida. Una estrategia adecuada sería probar un algoritmo y, si los resultados no resultan satisfactorios, probar los demás. Este es un ejemplo de la [Galería de aprendizaje automático de Azure](http://gallery.azureml.net/) de un experimento que prueba varios algoritmos con los mismos datos y compara los resultados: [Comparación de clasificadores multiclase: reconocimiento de letra](http://gallery.azureml.net/Details/a635502fc98b402a890efe21cec65b92).

* Existen tres categorías principales de aprendizaje automático: **aprendizaje supervisado**, **aprendizaje sin supervisar** y **aprendizaje de refuerzo**.

  * En el **aprendizaje supervisado**, cada punto de datos está etiquetado o asociado con una categoría o valor de interés. Un ejemplo de una etiqueta de categoría es asignar una imagen como un 'gato' o 'perro'. Un ejemplo de una etiqueta de valor es el precio de venta asociado a un coche usado. El objetivo del aprendizaje supervisado es estudiar muchos ejemplos etiquetados como estos y luego poder realizar predicciones sobre puntos de datos futuros, por ejemplo, para identificar nuevas fotos con el animal correcto o asignar precios de venta precisos a otros coches usados. Este es un tipo conocido y útil del aprendizaje automático. Todos los módulos de Aprendizaje automático de Azure son algoritmos de aprendizaje supervisado, excepto [agrupación en clústeres k-means][k-means-clustering].

  * En el **aprendizaje sin supervisar**, los puntos de datos no tienen etiquetas asociadas a ellos. En su lugar, el objetivo de un algoritmo de aprendizaje sin supervisar es organizar los datos de alguna manera o describir su estructura. Esto puede significar agruparlos en clústeres, como hace K-means, o buscar diferentes maneras de examinar datos complejos para que parezcan más simples.

  * En el **aprendizaje de refuerzo**, el algoritmo elige una acción en respuesta a cada punto de datos. Se trata de un enfoque común en robótica, donde el conjunto de lecturas del sensor en un punto en el tiempo es un punto de datos, y el algoritmo debe elegir la siguiente acción del robot. También resulta perfecto para las aplicaciones de Internet de las cosas. El algoritmo de aprendizaje también recibe una señal de recompensa un poco más adelante, que indica cómo de buena fue la decisión. Según esto, el algoritmo modifica su estrategia para lograr la mayor recompensa. Actualmente no hay ningún módulo de algoritmo de aprendizaje de refuerzo en Aprendizaje automático de Azure.

* Los **métodos bayesianos** se basan en el supuesto de puntos de datos estadísticamente independientes. Esto significa que la variabilidad sin modelar en un punto de datos no se correlaciona con otras, es decir, no se puede predecir. Por ejemplo, si los datos que se registran son el número de minutos hasta que llega el próximo tren, dos mediciones tomadas con una diferencia de un día son estadísticamente independientes. Sin embargo, dos mediciones tomadas con una diferencia de un minuto no son estadísticamente independientes: el valor de una es altamente predictivo del valor de la otra.

* La **regresión del árbol de decisión incrementado** aprovecha la superposición de características o la interacción entre ellas. Esto significa que, en cualquier punto de datos dado, el valor de una característica es en cierto modo predictiva del valor de otra. Por ejemplo, en datos de temperatura diaria alta o baja, saber la temperatura baja del día permite realizar una estimación razonable de la alta. La información contenida en las dos características es en cierto modo redundante.

* Se pueden clasificar los datos en más de una categoría, bien mediante un clasificador multiclase por naturaleza, o combinando un conjunto de clasificadores de dos clases en un **conjunto**. En el enfoque de conjunto, hay un clasificador de dos clases distinto para cada clase y cada uno separa los datos en dos categorías: “esta clase” y “esta clase no”. Luego, estos clasificadores votan la asignación correcta del punto de datos. Este es el principio operativo que subyace a [Multiclase uno contra todos][one-vs-all-multiclass].

* Varios métodos, incluida la regresión logística y la máquina del punto de Bayes, se basan en el supuesto de **límites de clase lineal**, es decir, que los límites entre las clases son aproximadamente líneas rectas (o hiperplanos en la mayoría de los casos). Con frecuencia es una característica de los datos que no conoce hasta que intenta separarlos, pero es algo que normalmente se puede aprender visualizándolos de antemano. Si los límites de clase son muy irregulares, quédese con árboles de decisión, selvas de decisión, máquinas de vectores de soporte o redes neurales.

* Las redes neurales pueden usarse con variables de categorías creando una **variable ficticia** para cada categoría y estableciéndola en 1 en aquellos casos en los que se aplique la categoría, o en 0 cuando no sea así.


<!-- This is how you can add a link to the image in HTML. Don't know how to do this in markdown.
<a href="http://download.microsoft.com/download/A/6/1/A613E11E-8F9C-424A-B99D-65344785C288/microsoft-machine-learning-algorithm-cheat-sheet.pdf">
<img src="C:\Users\garye\azure-content-pr\articles\media\machine-learning-algorithm-cheat-sheet\cheat-sheet-small.png">
</a>
-->

<!-- Module References -->
[initialize-model]: https://msdn.microsoft.com/library/azure/0c67013c-bfbc-428b-87f3-f552d8dd41f6/
[k-means-clustering]: https://msdn.microsoft.com/library/azure/5049a09b-bd90-4c4e-9b46-7c87e3a36810/
[one-vs-all-multiclass]: https://msdn.microsoft.com/library/azure/7191efae-b4b1-4d03-a6f8-7205f87be664/
 

<!---HONumber=July15_HO2-->