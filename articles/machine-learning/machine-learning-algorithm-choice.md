<properties 
	pageTitle="Cómo elegir un algoritmo en Aprendizaje automático de Azure | Microsoft Azure" 
	description="Cómo elegir algoritmos de Aprendizaje automático de Azure para el aprendizaje supervisado y no supervisado en experimentos de agrupación en clústeres, clasificación o regresión." 
	services="machine-learning"
	documentationCenter="" 
	authors="garyericson" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/01/2015" 
	ms.author="bradsev;garye" />


# Elección de algoritmos de Aprendizaje automático de Azure para la agrupación en clústeres, clasificación o regresión

En este tema se explican algunos aspectos básicos del enfoque de Aprendizaje automático. En concreto, aprenderá cómo seleccionar un algoritmo apropiado para analizar un tipo determinado de datos, para responder a una pregunta planteada, llevar a cabo una tarea específica o proporcionar criterios para tomar una decisión

> [AZURE.TIP]La [hoja de referencia rápida de algoritmos de Aprendizaje automático de Microsoft Azure](machine-learning-algorithm-cheat-sheet.md) es una práctica referencia para acompañar a este artículo.

Cuando se utiliza el aprendizaje automático para realizar análisis, a menudo nos enfrentamos a dos preguntas:

* ¿Qué tipo de análisis necesitamos para lograr nuestros objetivos con los datos disponibles? 
* ¿Cuál es el algoritmo o el modelo más adecuado para realizar este análisis?

Se describen tres tipos de análisis y se comparan sus casos de uso:

* **Agrupación en clústeres**
* **Clasificación** 
* **Regresión** 

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]


<a name="anchor-1"></a>
## Los algoritmos de Aprendizaje automático aprenden de los datos

Aprendizaje automático es una disciplina que estudia una clase de algoritmos diseñados para aprender de los datos y no imponen un modelo predeterminado específico para probar con los datos. La idea es adquirir conocimientos de manera más inductiva examinando los patrones de un conjunto de datos en lugar usar el método *hipotético deductivo* en el que primero intenta adivinar el modelo apropiado para todo el conjunto de datos y, a continuación, lo prueba empíricamente.

Hay dos modos de Aprendizaje automático, que también se conoce como aprendizaje a partir de los datos: *aprendizaje supervisado* y *aprendizaje no supervisado*.

<a name="anchor-2"></a>
## Aprendizaje supervisado  

El aprendizaje supervisado requiere que la variable de destino esté bien definida y que se suministre un número suficiente de sus valores.

El aprendizaje supervisado es el tipo de aprendizaje automático que tiene lugar cuando se conocen los resultados correctos (o variables de destino) para las instancias de aprendizaje que se van a introducir. El objetivo de entrenar a un algoritmo de aprendizaje automático es encontrar el modelo (que es una función o una regla) que asigna las entradas a los valores de los resultados conocidos. Es similar a tener un supervisor que puede indicar al agente algorítmico si está asignando correctamente las entradas a las salidas. Una vez que se completa el proceso de aprendizaje y tenemos un modelo viable, se puede aplicar a los nuevos datos de entrada para predecir el resultado esperado cuando, a diferencia del conjunto de datos de entrenamiento, el valor de destino no se conoce de antemano.

El tipo de modelo se determina por la naturaleza de la variable de destino.

![Diagrama de aprendizaje supervisado: modelado a partir de datos etiquetados y utilizado para predecir los resultados de los nuevos datos.](./media/machine-learning-algorithm-choice/supervised-learning-using-known-data-to-model-solution.png)

Hay dos amplias categorías de análisis que emplean aprendizaje supervisado: *clasificación* y *regresión*. El aprendizaje supervisado es muy común en los problemas de clasificación porque el objetivo es a menudo que el equipo aprenda un sistema de clasificación que hemos creado. Las respuestas normalmente son tan solo unos pocos valores (etiquetas) conocidos, como 'true' o 'false', o 'high', 'medium' o 'low'. Los algoritmos de clasificación se aplican a los valores nominales, no a los valores de respuesta no ordinales. El reconocimiento de dígitos es un ejemplo común de aprendizaje de clasificación. Por lo general, el aprendizaje de la clasificación es adecuado para cualquier problema en el que resulta útil y sencillo determinar la clasificación.

En el aprendizaje supervisado las variables que se están investigando se pueden dividir en dos grupos: variables explicativas (también denominadas de predicción) y variables dependientes (también denominadas variables de respuesta). El objetivo del análisis es especificar una relación entre las variables explicativas y las variables dependientes, como se hace en el *análisis de regresión*. Los valores de la variable dependiente deben ser conocidos para una parte suficientemente grande del conjunto de datos. En la regresión, las respuestas o la variable de salida toman valores continuos, como los kilómetros por litro de un determinado vehículo, la edad de una persona, etc.

El aprendizaje supervisado también es la técnica más común para redes neuronales de formación y árboles de decisión.

> Ambas técnicas son altamente dependientes de la información facilitada por las clasificaciones predeterminadas. En el caso de las redes neuronales, la clasificación se utiliza para determinar el error de la red y, a continuación, ajustar la red para minimizarla. En el caso de los árboles de decisión, las clasificaciones se utilizan para determinar qué atributos proporcionan la mayoría de la información que puede utilizarse para resolver el rompecabezas de clasificación. Ambos ejemplos requieren cierta "supervisión" en el sentido de que dependen de que se proporcionen con clasificaciones predeterminadas.

>  El reconocimiento de voz mediante modelos de Markov ocultos y redes bayesianas también depende de los elementos de la supervisión para ajustar parámetros para minimizar los errores en las entradas indicadas. [[Machine Learning, Part II: Supervised and Unsupervised Learning](http://www.aihorizon.com/essays/generalai/supervised_unsupervised_machine_learning.htm) (Aprendizaje automático, parte II: Aprendizaje supervisado y sin supervisión), [AI Horizon](http://www.aihorizon.com/)]


<a name="anchor-3"></a>
##Aprendizaje no supervisado

En Aprendizaje automático, el problema del aprendizaje sin supervisión es determinar si hay patrones o estructuras ocultas en los datos sin etiquetar. El modelo no se proporciona con "resultados correctos" para un conjunto de datos en el que se va a entrenar. Debido a que los ejemplos proporcionados al aprendiz están sin etiquetar, no hay ningún error ni señal de recompensa para evaluar una posible solución. El objetivo es que el equipo aprenda a hacer algo aunque no le indiquemos explícitamente cómo llevar a cabo esa tarea. En situaciones de aprendizaje sin supervisión, todas las variables se tratan de la misma manera. No hay ninguna distinción entre las variables dependientes y las explicativas. Sin embargo, todavía hay algunos objetivos de lograr. Este objetivo podría ser tan general como una reducción de datos o más específica como encontrar clústeres.

En Aprendizaje automático de Azure podemos efectuar aprendizaje no supervisado y supervisado a través de la **Agrupación en clústeres**, la **Clasificación** y la **Regresión**.

   ![screenshot_of_experiment](./media/machine-learning-algorithm-choice/help2.png)

<a name="anchor-4"></a>
##Agrupación en clústeres
Agrupación en clústeres es un ejemplo de aprendizaje no supervisado. En este tipo de aprendizaje, el objetivo es buscar similitudes en los datos de entrenamiento y particionar el conjunto de datos en subconjuntos que están delimitados por estas similitudes. A menudo, pero no siempre, se satisface la expectativa de que los clústeres más significativos detectados por estos procedimientos controlados por los datos son coherentes con la clasificación intuitiva.

Aunque el algoritmo de agrupación en clústeres no asigna nombres adecuados a estos clústeres, puede producirlos y, a continuación, usarlos para anticipar similitudes esperadas en nuevos ejemplos mediante su clasificación en el clúster más adecuado. Se trata de un enfoque basado en datos que puede funcionar bien cuando hay datos suficientes disponibles. Por ejemplo, los algoritmos de filtrado de información social, como los utilizados por Amazon.com para recomendar libros, se basan en buscar grupos similares de personas y, a continuación, asignar nuevos usuarios a estos grupos con el fin de realizar recomendaciones.

El algoritmo de agrupación en clústeres disponible en Aprendizaje automático de Azure es la [agrupación en clústeres K-means][k-means-clustering].

![Experimento de algoritmo de agrupación en clústeres K-Means: captura de pantalla](./media/machine-learning-algorithm-choice/k-means-clustering-algorithm-menu.png)

K-means es uno de los algoritmos de agrupación en clústeres sin supervisión más sencillos que solucionan el conocido problema de agrupación en clústeres. El algoritmo de KMeans agrupa en clústeres los datos intentando separar los ejemplos en N grupos de varianza equitativa, minimizando el criterio de «inercia» o «suma de cuadrados en clúster». Este algoritmo requiere el número de clústeres a especificar. K-means puede escalar perfectamente hasta un gran número de muestras y se ha utilizado en una amplia gama de áreas de la aplicación en varios campos distintos.

El módulo del algoritmo de [agrupación en clústeres K-Means][k-means-clustering] devuelve un modelo de agrupación en clústeres K-means no entrenado que puede pasarse al modelo de [agrupación en clústeres de entrenamiento][train-clustering-model] para efectuar el aprendizaje.

![screenshot_of_experiment](./media/machine-learning-algorithm-choice/k4.png)

En esta figura se muestra que hay opciones para configurar cuando se utiliza la agrupación en clústeres K-Means. El método K-means busca un número especificado de clústeres para un conjunto de puntos de datos de D dimensiones. Comenzando con un *conjunto inicial de centroides K*, el método usa el algoritmo de Lloyd para refinar las ubicaciones de los centroides de forma iterativa. El algoritmo termina cuando los centroides se estabilizan o cuando se completa un *número especificado de iteraciones*. El módulo inicializa una matriz de K por D con los centroides finales que definen los clústeres K encontrados en los puntos de datos de N. El algoritmo también utiliza un vector de longitud N con la asignación de cada punto de datos a uno de los clústeres K. Si se especifica un número específico de clústeres (K) para buscar, el módulo asigna los primeros puntos de datos K en orden a los clústeres K.


Este módulo también acepta o genera puntos iniciales para definir su configuración inicial del clúster. La *métrica* define el método utilizado para medir la distancia entre un punto de datos y el centroide. Cada punto de datos se asigna al clúster que tiene el centroide más cercano al punto de datos. De forma predeterminada, el método usa la *métrica euclidiana*. Puede especificar la *métrica de coseno* como una métrica alternativa para el método. Tenga en cuenta que es posible que el método K-means solo encuentre una configuración de clúster localmente óptima para un conjunto de datos. El método podría encontrar una configuración diferente, quizás superior, si se facilita una configuración inicial diferente.

<a name="anchor-5"></a>
##Clasificación 
En el análisis de clasificación se dividen los ejemplos en clases y se utiliza un conjunto entrenado de datos etiquetados previamente. La técnica se usa para predecir la pertenencia al grupo de las instancias de datos. En Aprendizaje automático de Azure están disponibles los siguientes algoritmos de clasificación.

![screenshot_of_experiment](./media/machine-learning-algorithm-choice/help3.png)

Los *algoritmos de dos clases* se usan para las variables de respuesta binaria (Sí o no, 0 o 1, etc. true o false, etc.), mientras que los *algoritmos multiclase* se utilizan para cualquier variable de respuesta nominal que clasifica las instancias en más de dos clases

### Directrices para seleccionar un algoritmo de clasificación
Esta larga lista de algoritmos da lugar a una serie de preguntas:

* ¿Cómo sé cuál de estos clasificadores es el que mejor se puede utilizar en un determinado conjunto de datos? 
* ¿Hay casos en los que un clasificador sea una opción "natural"? 
* ¿Cuáles son los principios que determinan su elección?

Un enfoque recomendable es probar varios clasificadores diferentes, así como conjuntos de parámetros diferentes dentro de cada algoritmo y, a continuación, seleccionar el mejor mediante el uso de la validación cruzada.

> [AZURE.TIP] [Azure Machine Learning Studio](https://studio.azureml.net/) le permite probar varios algoritmos en paralelo con los mismos datos y comparar los resultados. Este es un ejemplo de la [Galería de Aprendizaje automático de Azure](http://gallery.azureml.net/): [comparación de clasificadores multiclase: reconocimiento de letras](http://gallery.azureml.net/Details/a635502fc98b402a890efe21cec65b92).

A continuación presentamos algunas directrices generales que pueden proporcionar un lugar para iniciar esta deliberación. Tenga en cuenta los siguientes problemas a la hora de elegir el algoritmo a utilizar: [esquema sugerido en el blog de Edwin Chen [Choosing a Machine Learning Classifier](http://blog.echen.me/2011/04/27/choosing-a-machine-learning-classifier/) (Elección de un clasificador de Aprendizaje automático)]

**¿Qué tamaño tiene los datos de aprendizaje?** Si el conjunto de entrenamiento es pequeño y va a entrenar a un clasificador supervisado, la teoría de aprendizaje automático indica que debemos ceñirnos a un clasificador con gran variación de sesgo o con desviación baja como Naive Bayes. Estos tienen una ventaja sobre clasificadores de poca variación de sesgo y desviación alta como kNN, ya que los segundos tienden a sobreajustarse. Pero los clasificadores de variación de sesgo baja y desviación alta comienzan a ganar a medida que crece el conjunto de aprendizaje (tienen un error asintótico más pequeño), ya que los clasificadores de variación de sesgo alto no son lo suficientemente eficaces para proporcionar modelos precisos. Hay resultados teóricos y empíricos que indican que Bayes Naive se comporta correctamente en tales circunstancias. Pero tenga en cuenta que los mejores datos normalmente superan a los mejores algoritmos y que las buenas funciones de diseño proporcionan una ventaja considerable. Si tiene un gran conjunto de datos, a continuación, el algoritmo de clasificación que utiliza podría no ser de gran importancia para el rendimiento de clasificación. Por ello, es posible que resulte mejor elegir el algoritmo en función de su buena escalabilidad, velocidad o facilidad de uso en su lugar.

**¿Necesita entrenar gradualmente o en un modo por lotes?** Si tiene una gran cantidad de datos o los datos se actualizan con frecuencia, probablemente deba usar algoritmos bayesianos que realizan buenas actualizaciones. Las redes neuronales y SVM necesitan trabajar en los datos de aprendizaje en el modo por lotes.

**¿Son sus datos exclusivamente pertenecientes a categorías, numéricos o una mezcla de ambos tipos?** El bayesiano funciona mejor con datos de categorías/binomiales. Los árboles de decisión no pueden predecir valores numéricos.

**¿Necesita usted o su público entender cómo funciona el clasificador?** Utilice árboles bayesianos o de decisión, ya que estos pueden explicarse fácilmente. Es mucho más difícil ver o explicar cómo clasifican los datos las redes neuronales y SVM.

**¿Con qué rapidez es necesario generar su clasificación?** Árboles de decisión pueden ser lentos, especialmente cuando el árbol es complejo. Por otro lado, las SVM son rápidas en clasificar porque solo necesitan determinar en qué lado de la "línea" se encuentran los datos.

**¿Qué grado de complejidad presenta o requiere el problema?** Las redes neuronales y las SVM admiten la clasificación compleja no lineal.

### Ventajas y desventajas de los algoritmos de clasificación
Cada uno de estos algoritmos de clasificación tiene algunas ventajas y algunas desventajas:

<a name="anchor-5a"></a> **Ventajas y desventajas de regresión la logística:** «El análisis de regresión logística se basa en calcular las probabilidades del resultado como la proporción de la probabilidad de tener el resultado dividido por la probabilidad de no tenerlo». [[Logistic Regression and Linear Discriminant Analyses in Evaluating Factors Associated with Asthma Prevalence among 10- to 12-Years-Old Children: Divergence and Similarity of the Two Statistical Methods](http://www.hindawi.com/journals/ijpedi/2009/952042/), George Antonogeorgos, et al (International Journal of Pediatrics, 2009) Article ID 952042]
 
El modelo logístico es paramétrico y por lo tanto, tiene la ventaja de que proporciona información sobre el impacto de cada variable de elemento de predicción en la variable de respuesta.


Con las interpretaciones probabilísticas naturales disponibles (a diferencia de los árboles de decisión o SVM), se puede actualizar fácilmente el modelo para incorporar nuevos datos. Hay varias maneras de regularizar el modelo y, como ocurre con Naive Bayes, no tiene que preocuparse tanto acerca de las funciones que se correlacionan. La regresión logística es útil si se desea:

* un marco probabilístico para ajustar los umbrales de clasificación
* incorporar rápidamente datos de aprendizaje adicionales  

La regresión logística funciona mejor que los árboles de decisión para datos de dimensión elevados. Por ejemplo, en la clasificación del texto puede tener más de 100.000 documentos con 500.000 palabras distintas (funciones). Es mejor una regla sencilla como el aprendizaje de un hiperplano lineal, puesto que los árboles de decisión tienen demasiados grados de libertad y son propensos al sobreajuste. Puede llevar a cabo la selección de funciones para usar un árbol de decisión en los datos de texto, pero se perderá mucha información valiosa para la clasificación del texto al elegir un subconjunto muy reducido de las funciones. Cuando se usan modelos de aprendizaje con datos muy dimensionales, es muy fácil que los errores basados en desviación aumenten de tamaño. En ese caso es preferible usar modelos sencillos con diferencia superior.

Una desventaja de la regresión logística es que es inestable cuando un predictor casi podría explicar la variable de la respuesta, ya que el coeficiente de esta variable será muy elevado.

<a name="anchor-5b"></a> **Ventajas y desventajas de los árboles de decisión:** los [árboles de decisión](http://research.microsoft.com/pubs/155552/decisionForests_MSR_TR_2011_114.pdf) son fáciles de interpretar y explicar.

> [Los árboles de decisión] controlan fácilmente las interacciones de funciones y son no paramétricos para no tener que preocuparse por los valores atípicos o de si los datos son linealmente separables (por ejemplo, los árboles de decisión atienden fácilmente casos en los que dispone de la clase A en el extremo inferior de una función x, clase B en el rango medio de la función x y A de nuevo en el extremo superior). Una desventaja es que no son compatibles con el aprendizaje en línea, por lo que debe volver a generar el árbol cuando lleguen nuevos ejemplos. Otra desventaja es que se sobreajustan fácilmente, pero aquí es donde pueden ayudar los métodos de conjunto como bosques aleatorios (o árboles aumentados). Además, los bosques aleatorios son a menudo los ganadores para muchos problemas en la clasificación. Suelen hacerlo un poco mejor que las SVM: son rápidos y escalables, y no tiene que preocuparse acerca del ajuste de un conjunto de parámetros como lo haría con las SVM. [[Choosing a Machine Learning Classifier](http://blog.echen.me/2011/04/27/choosing-a-machine-learning-classifier/) (Elección de un clasificador de Aprendizaje automático), Edwin Chen]

Los árboles de decisión generarán el resultado como reglas junto con métricas como el *soporte técnico*, la *confianza* y la *elevación*.


<a name="anchor-5c"></a> **Ventajas y desventajas de SVM**: Las máquinas de vectores de soporte (SVM) son eficaces en espacios dimensionales altos. Incluso en casos en los que el número de dimensiones es mayor que el número de muestras continúa siendo eficaz. Sin embargo, si el número de funciones es mucho mayor que el número de muestras, es probable que el método no funcione correctamente. También permite ahorrar memoria, ya que usa un subconjunto de puntos de aprendizaje en la función de decisión (conocidos como vectores de soporte). Es muy versátil: se pueden especificar diferentes funciones de Kernel, tanto comunes como personalizadas, para la función de decisión. La función del kernel se utiliza para transformar los ejemplos de aprendizaje dimensional baja a dimensiones superiores, lo cual resulta útil para los problemas de la cláusula de divisibilidad lineal.

No obstante, las SVM no proporcionan directamente las estimaciones de probabilidad. Estas se calculan mediante una costosa validación cruzada repetida cinco veces.

>[Con] alta precisión, las garantías teóricas sólidas relacionadas con el sobreajuste y un kernel adecuado, pueden funcionar bien incluso si los datos no son separables linealmente en el espacio de la función de base. [Las SVM son] especialmente populares en problemas de clasificación de texto donde los espacios muy altamente dimensionales son la norma. [[Choosing a Machine Learning Classifier](http://blog.echen.me/2011/04/27/choosing-a-machine-learning-classifier/) (Elección de un clasificador de Aprendizaje automático), Edwin Chen]

A diferencia de los bosques, las SVM se originaron como clasificadores de dos clases, aunque recientemente se han adaptado para funcionar con varias clases. En ocasiones podemos usar algo como la formación de "uno contra los demás" para crear un clasificador de varias clases que puede ser menos óptimo. Puesto que las SVM solo pueden controlar las salidas de dos clases (es decir, una variable de salida categórica con variedad 2), con N clases, aprende N SVM (SVM 1 aprende "salida == 1" frente a "salida != 1", SVM 2 aprende "salida==2" frente a "salida != 2",..., SVM N aprende "salida== N" frente a "salida ! = N"). A continuación, para predecir el resultado de una nueva entrada, simplemente predice con cada SVM y busca cuál coloca la predicción más alejada en la región positiva. [[Support Vector Machines](http://www.astro.caltech.edu/~george/aybi199/AMooreTutorials/svm.ppt) (Máquinas de vectores de soporte), Andrew W. Moore (Carnegie Mellon University 2001)]

<a name="anchor-5d"></a> **Ventajas y desventajas de Bayes Naive**: Los clasificadores [Naive Bayes (NB)](http://www.aaai.org/Papers/FLAIRS/2004/Flairs04-097.pdf) son una opción popular para los problemas de clasificación. Suponen que las funciones son independientes, esto es lo que hace la técnica ’naive’.

> Si realmente se mantiene la suposición de independencia condicional de NB, un clasificador de Naive Bayes convergerá más rápidamente que los modelos discriminativos como la regresión logística, por tanto necesitan menos datos de aprendizaje. Y aunque la suposición NB no se mantenga, un clasificador NB suelen hace un gran trabajo en la práctica. Su principal inconveniente es que no aprenda de las interacciones entre funciones (por ejemplo, no puede aprender que aunque le encanten las películas con Brad Pitt y Tom Cruise, odie las películas donde aparecen juntos). [[Choosing a Machine Learning Classifier](http://blog.echen.me/2011/04/27/choosing-a-machine-learning-classifier/) (Elección de un clasificador de Aprendizaje automático), Edwin Chen]


<a name="anchor-5e"></a> **Uno frente todos**: Uno frente todos es una estrategia para reducir el problema de la clasificación de varias clases a un conjunto de varios problemas de clasificación binaria. Esta estrategia implica formar a un clasificador único por clase, con los ejemplos de esa clase como ejemplos positivos y el resto de ejemplos como negativos. Esta estrategia requiere los clasificadores base para producir una puntuación de confianza con valores reales para su decisión, en lugar de simplemente una etiqueta de clase. Las etiquetas de clase discreta solas pueden producir ambigüedades, donde se predicen varias clases para un único ejemplo. [[Clasificación multiclass](http://en.wikipedia.org/wiki/Multiclass_classification) (Clasificación multiclase) (Wikipedia 2006)]


<a name="anchor-6"></a>
##Regresión
 
En el análisis de regresión, predecimos nuevos valores basados en la inferencia pasada. Los nuevos valores de una variable dependiente se calculan basándose en el valor de uno o más atributos medidos. Los distintos algoritmos de regresión disponibles en Aprendizaje automático de Azure son:

![screenshot_of_experiment](./media/machine-learning-algorithm-choice/help4.png)

Según el caso de uso y los datos en cuestión, se elige un algoritmo sobre otro. A continuación, describiremos algunos algoritmos de regresión y sus casos de uso principales.

<a name="anchor-6b"></a> **[Regresión lineal bayesiana][bayesian-linear-regression]**: La regresión lineal de bayesiana es un enfoque para la regresión lineal en el que el análisis estadístico se lleva a cabo en el contexto de la inferencia bayesiana. Hay disponibles resultados explícitos para las distribuciones de probabilidad posteriores de los parámetros del modelo cuando el modelo de regresión tiene errores distribuidos normales y se puede suponer una forma especial de distribución anterior. [[Regresión lineal](http://en.wikipedia.org/wiki/Bayesian_linear_regression) ([Wikipedia](http://en.wikipedia.org))]

<a name="anchor-6f"></a> **[Regresión del árbol de decisión aumentada][boosted-decision-tree-regression]** La regresión calcula una relación entre predictor y variables de respuesta. La estructura de árbol de regresión es similar a un árbol de clasificación. Los nodos terminales son valores de función predichos (modelo). Los valores predichos están limitados a los valores de los nodos terminales. Algunas de las ventajas de utilizar los árboles de decisión son:

* es fácil de interpretar las reglas de decisión 
* son no paramétricos, por lo que resulta fácil usar una gama de capas de datos categóricos o numéricos y no son necesario los datos de aprendizaje unimodal
* son sólidos en comparación con los valores atípicos de datos de formación 
* la clasificación es rápida una vez que se desarrollan las reglas 

Sin embargo, hay algunas desventajas de utilizar árboles de decisión:

* tienden a sobreajustar los datos de entrenamiento, lo cual puede provocar la obtención de resultados inexactos cuando se aplica a todo el conjunto de datos
* no es posible predecir los límites mínimo y máximo de la variable de respuesta en los datos de aprendizaje


<a name="anchor-6g"></a> **[Regresión del bosque de decisión][decision-forest-regression]** Los bosques de decisión pueden utilizarse para aplicaciones de clasificación (variables de categorías) o de regresión (variables continuas). Los bosques de regresión pueden utilizarse para la regresión no lineal de variables dependientes a los que se otorga una entrada independiente, y ambas entradas y salidas pueden ser multidimensionales. Los árboles de regresión no se usan tanto como sus homólogos de clasificación. La principal diferencia es que la etiqueta de salida de los bosques de decisión a asociar con los datos de entrada (y por las etiquetas de aprendizaje) debe ser continua. Esto significa que la función objetivo debe adaptarse adecuadamente. Los bosques de regresión comparten muchas de las ventajas de los bosques de clasificación, como la eficacia y la flexibilidad.

<a name="anchor-6a"></a> **[Regresión lineal][linear-regression]** La regresión lineal se usa ampliamente para modelar la relación entre una variable Y dependiente de escalar y una o más variables explicativas X. Puede, y normalmente se aplica, para la predicción, previsión o reducción. Se puede usar para ajustarse a un modelo de predicción para un conjunto de datos observado de valores X e Y. La regresión lineal supone que la estructura subyacente de Y es una combinación de lineal de las variables de X. Si, a continuación, se proporciona un valor adicional de X sin su correspondiente valor y, el modelo de regresión lineal ajustado puede utilizarse para predecir ese valor Y. Los modelos de regresión lineal normalmente están equipados con el enfoque de menos cuadrados, pero existen otras opciones también para medir qué opción es la mejor. [[Regresión lineal](http://en.wikipedia.org/wiki/Bayesian_linear_regression) ([Wikipedia](http://en.wikipedia.org))]

<a name="anchor-6c"></a> **[Regresión de red neuronal][neural-network-regression]** Las redes neuronales son una herramienta estadística útil de regresión no paramétrica. La regresión no paramétrica trata el problema de tratar de ajustar un modelo para una variable Y en un conjunto de posibles variables explicativas X1; : : : ;Xp, y donde la relación entre X e Y pueden ser más complicada que una relación lineal simple. [[A Framework for Nonparametric Regression Using Neural Networks](http://ftp.isds.duke.edu/WorkingPapers/00-32.pdf) (Un marco para la regresión no paramétrica mediante redes neuronales), Herbert K. H. Lee (ISDS, Duke University)]

<a name="anchor-6d"></a> **[Regresión ordinal][ordinal-regression]** La regresión ordinal es un tipo de análisis de regresión utilizado para modelar o predecir una variable dependiente de un ordinal. Para variables dependientes ordinales, puede clasificar los valores, pero se desconoce la distancia real entre categorías. Solo es significativo el orden relativo entre los distintos valores. Puesto que las etiquetas o valores de destino tienen un orden clasificación natural, puede usarse cualquier columna numérica como destino de ordinal. El orden natural de los números se utiliza para clasificarlos. Por ejemplo, las enfermedades se clasifican en escalas desde menos grave a más grave. Los encuestados eligen respuestas en escalas que van desde «muy de acuerdo» a «muy en desacuerdo». Los alumnos son evaluados en escalas que van desde la A a la F. No obstante, esencialmente, la regresión ordinal es una extensión de regresión logística que se basa en el modelo de *probabilidades proporcional*.


<a name="anchor-6e"></a> **[Regresión de Poisson][poisson-regression]** La regresión de Poisson se suele usar para el modelado de datos del contador. La regresión Poisson supone que la variable de respuesta tiene una distribución de Poisson. Los datos distribuidos mediante Poisson son intrínsecamente de enteros (positivos y discretos), lo cual tiene sentido para los datos del contador. Dado un conjunto de datos de entrenamiento, la regresión de Poisson intenta encontrar los valores óptimos mediante la maximización de la probabilidad del registro de los parámetros dadas las entradas. La probabilidad de los parámetros es la probabilidad de que los datos de entrenamiento se hayan tomado de una distribución con estos parámetros. Por ejemplo, la regresión de Poisson sería útil para:

* Modelar el número de los resfriados asociados a los vuelos en avión 
* Obtener una estimación del número de llamadas relacionadas con un evento o promoción 
* Crear tablas de contingencia

## Referencias

Para ver una lista completa de los tipos de algoritmos de aprendizaje automático disponibles en Estudio de aprendizaje automático, consulte [Inicializar modelo](https://msdn.microsoft.com/library/azure/0c67013c-bfbc-428b-87f3-f552d8dd41f6/) en [Estudio de aprendizaje automático: ayuda para algoritmos y módulos](https://msdn.microsoft.com/library/azure/dn905974.aspx).

Puede encontrar información más detallada acerca de todos los tipos de algoritmos de aprendizaje automático en las siguientes referencias, muchos de los cuales se han usado al redactar este artículo.

* [Choosing a Machine Learning Classifier](http://blog.echen.me/2011/04/27/choosing-a-machine-learning-classifier/) (Elección de un clasificador de Aprendizaje automático), Edwin Chen.

* [Decision Forests for Classification, Regression, Density Estimation, Manifold Learning and Semi-Supervised Learning](http://research.microsoft.com/pubs/155552/decisionForests_MSR_TR_2011_114.pdf) (Bosques de decisión para la clasificación, regresión, estimación de densidad, aprendizaje múltiple y aprendizaje semisupervisado), A. Criminisi1, J. Shotton2 and E. Konukoglu (Microsoft Research, 2011) informe técnico TR-2011-114.

* [A Framework for Nonparametric Regression Using Neural Networks](http://ftp.isds.duke.edu/WorkingPapers/00-32.pdf) (Un marco para la regresión no paramétrica mediante redes neuronales), Herbert K. H. Lee (ISDS, Duke University).

* Handbook of Computational Statistics: Concepts and Methods (Manual de estadísticas computacionales: conceptos y métodos), editado por James E. Gentle, Wolfgang Karl Härdle, Yuichi Mori (Springer-Verlag Berlin Heidelberg New York, 2004).

* [Logistic Regression and Linear Discriminant Analyses in Evaluating Factors Associated with Asthma Prevalence among 10- to 12-Years-Old Children: Divergence and Similarity of the Two Statistical Methods](http://www.hindawi.com/journals/ijpedi/2009/952042/) (Regresión logística y análisis discriminante lineal al evaluar factores asociados con la prevalencia del asma en niños de 10 a 12 años: Divergencia y similitud entre los dos métodos estadísticos), George Antonogeorgos, Demosthenes B. Panagiotakos, Kostas N. Priftis y Anastasia Tzonou (International Journal of Pediatrics, 2009) Article ID 952042.

* [The Optimality of Naive Bayes](http://www.aaai.org/Papers/FLAIRS/2004/Flairs04-097.pdf) (El estado óptimo de Naive Bayes) (University of New Brunswick 2004) Harry Zhang.

* [Support Vector Machines](http://www.astro.caltech.edu/~george/aybi199/AMooreTutorials/svm.ppt) (Máquinas de vectores de soporte), Andrew W. Moore (Carnegie Mellon University 2001).

* [Machine Learning, Part II: Supervised and Unsupervised Learning](http://www.aihorizon.com/essays/generalai/supervised_unsupervised_machine_learning.htm) (Aprendizaje automático, parte II: Aprendizaje supervisado y sin supervisión), [AI Horizon](http://www.aihorizon.com/).

* [What are the advantages of logistic regression over decision trees?](http://www.quora.com/What-are-the-advantages-of-logistic-regression-over-decision-trees) (¿Cuáles son las ventajas de regresión logística sobre árboles de decisión?) ([Quora](http://www.quora.com/)).

* [What is the difference between supervised learning and unsupervised learning?](http://stackoverflow.com/questions/1832076/what-is-the-difference-between-supervised-learning-and-unsupervised-learning) (¿Cuál es la diferencia entre aprendizaje supervisado y aprendizaje sin supervisión?) ([Stackoverflow](http://stackoverflow.com/)).

* [When to choose which machine learning classifier?](http://stackoverflow.com/questions/2595176/when-to-choose-which-machine-learning-classifier) (¿Cuándo elegir el clasificador de aprendizaje del equipo?) ([Stackoverflow](http://stackoverflow.com/)).

* [Wikipedia](http://en.wikipedia.org):
	* [Bayesian linear regression](http://en.wikipedia.org/wiki/Bayesian_linear_regression) (Regresión lineal bayesiana)
	* [Regresión lineal](http://en.wikipedia.org/wiki/Linear_regression)
	* [Multiclass classification](http://en.wikipedia.org/wiki/Multiclass_classification) (Clasificación multiclase)
	* [Aprendizaje no supervisado](http://en.wikipedia.org/wiki/Unsupervised_learning)

También:

* [Hoja de referencia rápida de algoritmos de Aprendizaje automático de Microsoft Azure](machine-learning-algorithm-cheat-sheet.md) (Microsoft).

* [Choosing the right estimator](http://scikit-learn.org/stable/tutorial/machine_learning_map/) (Elección del estimador correcto) ([scikit-learn](http://scikit-learn.org/stable/index.html)).


<!-- Module References -->
[k-means-clustering]: https://msdn.microsoft.com/library/azure/5049a09b-bd90-4c4e-9b46-7c87e3a36810/
[train-clustering-model]: https://msdn.microsoft.com/library/azure/bb43c744-f7fa-41d0-ae67-74ae75da3ffd/
[bayesian-linear-regression]: https://msdn.microsoft.com/library/azure/ee12de50-2b34-4145-aec0-23e0485da308/
[boosted-decision-tree-regression]: https://msdn.microsoft.com/library/azure/0207d252-6c41-4c77-84c3-73bdf1ac5960/
[decision-forest-regression]: https://msdn.microsoft.com/library/azure/562988b2-e740-4e3a-8131-358391bad755/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[neural-network-regression]: https://msdn.microsoft.com/library/azure/d7ee222c-669f-4200-a576-a761a9c1a928/
[ordinal-regression]: https://msdn.microsoft.com/library/azure/ffb557f8-dc7f-44bd-8fd0-b25666dd23f1/
[poisson-regression]: https://msdn.microsoft.com/library/azure/80e21b9d-3827-40d8-b733-b53148becbc2/

 

<!---HONumber=July15_HO4-->