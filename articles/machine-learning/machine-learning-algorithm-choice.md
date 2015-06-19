<properties 
	title="" 
	pageTitle="Cómo elegir un algoritmo en Aprendizaje automático de Azure | Azure" 
	description="Explica cómo elegir un algoritmo en Aprendizaje automático de Azure." 
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
	ms.date="02/9/2015" 
	ms.author="bradsev" />


# Cómo elegir un algoritmo en Aprendizaje automático de Azure

En este tema se explican algunos aspectos básicos del enfoque de aprendizaje automático y se destaca, en concreto, cómo seleccionar un algoritmo apropiado para analizar un tipo determinado de datos, para responder a una pregunta planteada, llevar a cabo una tarea específica o proporcionar criterios para tomar una decisión. Cuando se utiliza el aprendizaje automático para realizar análisis, a menudo nos enfrentamos a dos preguntas: 

* ¿Qué tipo de análisis necesitamos para lograr nuestros objetivos con los datos disponibles? 
* ¿Cuál es el algoritmo o el modelo más adecuado para realizar este análisis?

Se describen tres tipos de análisis y se comparan sus casos de uso: 

* clasificación 
* regresión 
* agrupación en clústeres

También se debaten los distintos algoritmos para cada uno de estos tipos de análisis y los módulos que los contienen que están disponibles en el Aprendizaje automático de Azure. 


<a name="anchor-1"></a>
##**Aprendizaje automático**

Aprendizaje automático es una disciplina que estudia una clase de algoritmos que están controlados por datos en el sentido de que están diseñados para aprender de los datos y no imponen un modelo predeterminado específico para probar con los datos. La idea es adquirir conocimientos de manera más inductiva examinando los patrones de un conjunto de datos en lugar de mediante el uso de lo que se conoce normalmente como el método hipotético deductivo en el que primero intenta adivinar el modelo apropiado para todo el conjunto de datos y, a continuación, lo prueba empíricamente. Este tipo de aprendizaje a partir de datos tiene dos modos: aprendizaje supervisado y aprendizaje no supervisado. 

<a name="anchor-2"></a>
##**Aprendizaje supervisado**  

El aprendizaje supervisado requiere que la variable de destino esté bien definida y que se suministre un número suficiente de sus valores. 

Aprendizaje supervisado es el tipo de aprendizaje que tiene lugar cuando se conocen los resultados correctos (o variables de destino) para las instancias de entrenamiento a introducir. El objetivo de entrenar a un algoritmo de aprendizaje automático es encontrar el modelo (que es una función o una regla) que asigna las entradas a los valores de los resultados conocidos. Es como tener un supervisor que puede indicar al agente algorítmico si está asignando correctamente las entradas a las salidas. Una vez que se completa el proceso de aprendizaje y tenemos un modelo viable, se puede aplicar a los nuevos datos de entrada para predecir el resultado esperado cuando, a diferencia del conjunto de datos de entrenamiento, el valor de destino no se conoce de antemano.

La naturaleza de la variable de destino determina el tipo de modelo.

![screenshot_of_experiment](./media/machine-learning-algorithm-choice/help9.png)

Hay dos amplias categorías de análisis que emplean aprendizaje supervisado: clasificación y regresión. El aprendizaje supervisado es muy común en *classification problems* porque el objetivo es a menudo que el equipo aprenda un sistema de clasificación que hemos creado. Las respuestas normalmente son tan solo unos pocos valores (etiquetas) conocidos, como 'true' o 'false'. Los algoritmos de clasificación se aplican a los valores de respuesta no ordinales, nominales. El reconocimiento de dígitos es un ejemplo común de aprendizaje de clasificación. Por lo general, el aprendizaje de la clasificación es adecuado para cualquier problema en el que resulta útil deducir una clasificación y la clasificación es fácil de determinar.

En el aprendizaje supervisado las variables que se están investigando se pueden dividir en dos grupos: variables explicativas (también denominadas de predicción) y variables dependientes (también denominadas variables de respuesta). El objetivo del análisis es especificar una relación entre las variables explicativas y las variables dependientes, como se hace en *regression analysis*. Los valores de la variable dependiente deben ser conocidos para una parte suficientemente grande del conjunto de datos. En la regresión, las respuestas o la variable de salida toman valores continuos, como los kilómetros por litro de un determinado vehículo, la edad de una persona, etc.

El aprendizaje supervisado también es la técnica más común para redes neuronales de formación y árboles de decisión. Ambas técnicas son altamente dependientes de la información facilitada por las clasificaciones predeterminadas. 

* En el caso de las redes neuronales, la clasificación se utiliza para determinar el error de la red y, a continuación, ajustar la red para minimizarla. 
* En el caso de los árboles de decisión, las clasificaciones se utilizan para determinar qué atributos proporcionan la mayoría de la información que puede utilizarse para resolver el rompecabezas de clasificación.  

Ambos ejemplos requieren cierta "supervisión" en el sentido de que dependen de que se proporcionen con clasificaciones predeterminadas. 

El reconocimiento de voz mediante modelos de Markov ocultos y redes bayesianas también depende de los elementos de la supervisión para ajustar parámetros para minimizar los errores en las entradas indicadas. 

<a name="anchor-3"></a>
##**Aprendizaje no supervisado**

En el aprendizaje automático, el problema del aprendizaje sin supervisión es buscar patrones o estructuras ocultas en los datos sin etiquetar. El modelo no se proporciona con "resultados correctos" para un conjunto de datos en el que se va a entrenar. Debido a que los ejemplos proporcionados al aprendiz están sin etiquetar, no hay ningún error ni señal de recompensa para evaluar una posible solución. El objetivo es que el equipo aprenda a hacer algo que no le indiquemos explícitamente cómo hacer. En situaciones de aprendizaje sin supervisión, todas las variables se tratan de la misma manera. No hay ninguna distinción entre las variables dependientes y explicativas. Sin embargo, todavía hay algunos objetivos de lograr. Este objetivo podría ser tan general como una reducción de datos o más específica como encontrar clústeres. 

En Aprendizaje automático de Azure podemos efectuar aprendizaje no supervisado y supervisado a través de la **Clasificación**, la **Agrupación en clústeres** y la **Regresión**.

   ![screenshot_of_experiment](./media/machine-learning-algorithm-choice/help2.png)

<a name="anchor-4"></a>
##**Agrupación en clústeres**  
Agrupación en clústeres es un ejemplo de aprendizaje no supervisado. En este tipo de aprendizaje, el objetivo es buscar similitudes en los datos de entrenamiento y particionar el conjunto de datos en subconjuntos que están delimitados por estas similitudes. A menudo, pero no siempre, se satisface la expectativa de que los clústeres más significativos detectados por estos procedimientos controlados por los datos son coherentes con la clasificación intuitiva. 

Aunque el algoritmo no asigna nombres adecuados a estos clústeres, puede producirlos y, a continuación, usarlos para anticipar similitudes esperadas en nuevos ejemplos mediante su clasificación en el clúster más adecuado. Se trata de un enfoque basado en datos que puede funcionar bien cuando hay datos suficientes. Por ejemplo, los algoritmos de filtrado de información social, como los utilizados por Amazon.com para recomendar libros, se basan en buscar grupos similares de personas y, a continuación, asignar nuevos usuarios a estos grupos con el fin de realizar recomendaciones.

El algoritmo de agrupación en clústeres disponible en Aprendizaje automático de Azure es la agrupación en clústeres K-means.

![screenshot_of_experiment](./media/machine-learning-algorithm-choice/help10.png)

K-means es uno de los algoritmos de aprendizaje sin supervisión más sencillos que solucionan el conocido problema de agrupación en clústeres. El algoritmo de KMeans agrupa en clústeres los datos intentando separar los ejemplos en N grupos de varianza equitativa, minimizando un criterio conocido como la inercia o suma de cuadrados en clúster. Este algoritmo requiere el número de clústeres a especificar. Se puede escalar perfectamente hasta un gran número de muestras y se ha utilizado en una gran gama de áreas de la aplicación en varios campos distintos.

El módulo de agrupación en clústeres **K-Means** que implementa el algoritmo K-means devuelve un modelo de agrupación en clústeres K-means no entrenado que puede pasarse al **modelo de agrupación en clústeres de entrenamiento** para efectuar el entrenamiento.

![screenshot_of_experiment](./media/machine-learning-algorithm-choice/k4.png)

En esta figura se muestra que hay opciones para configurar cuando se utiliza la agrupación en clústeres K-Means. El método K-means busca un número especificado de clústeres para un conjunto de puntos de datos de D dimensiones. Comenzando con un *initial set of K centroids*, el método usa el algoritmo de Lloyd para refinar las ubicaciones de los centroides de forma iterativa. El algoritmo termina cuando los centroides se estabilizan o cuando se completa un *specified number of iterations*.
El módulo inicializa una matriz de K por D con los centroides finales que definen los clústeres K encontrados en los puntos de datos de N. El algoritmo también utiliza un vector de longitud N con la asignación de cada punto de datos a uno de los clústeres K.
Si se especifica un número específico de clústeres (K) para buscar, el módulo asigna los primeros puntos de datos K en orden a los clústeres K.
Este módulo también acepta o genera puntos iniciales para definir su configuración inicial del clúster.
*Métrica* define el método utilizado para medir la distancia entre un punto de datos y el centroide.
Cada punto de datos se asigna al clúster que tiene el centroide más cercano al punto de datos. De forma predeterminada, el método usa el *Euclidean metric*. Puede especificar  *cosine metric* como una métrica alternativa para el método.
Tenga en cuenta que es posible que el método K-means solo encuentre una configuración de clúster localmente óptima para un conjunto de datos. El método podría encontrar una configuración diferente, quizás superior, si se facilita una configuración inicial diferente.

<a name="anchor-5"></a>
##**Clasificación**  
En el análisis de clasificación se dividen los ejemplos en clases y se utiliza un conjunto entrenado de datos etiquetados previamente. La técnica se usa para predecir la pertenencia al grupo de las instancias de datos. 
En Aprendizaje automático de Azure están disponibles los siguientes algoritmos de clasificación.

![screenshot_of_experiment](./media/machine-learning-algorithm-choice/help3.png)

 *Two-Class algorithms* se usan para las variables de respuesta binaria (Sí o no, 0 o 1, etc. true o false, etc.), mientras que *Multiclass algorithms* se utilizan para cualquier variable de respuesta nominal que clasifica las instancias en más de dos clases

### Directrices para seleccionar un algoritmo de clasificación
Esta larga lista de algoritmos da lugar a una serie de preguntas: 

* ¿Cómo sé cuál de estos clasificadores es el que mejor se puede utilizar en un determinado conjunto de datos? 
* ¿Hay casos en los que uno de ellos sea una opción "natural"? 
* ¿Cuáles son los principios que determinan su elección?

Un enfoque recomendable es probar varios clasificadores diferentes, así como conjuntos de parámetros diferentes dentro de cada algoritmo y, a continuación, seleccionar el mejor mediante el uso de la validación cruzada. A continuación presentamos algunas directrices generales que pueden proporcionar un lugar para iniciar esta deliberación. Tenga en cuenta los siguientes problemas a la hora de elegir el algoritmo a utilizar:

**¿Qué tamaño tiene los datos de entrenamiento?**
Si el conjunto de entrenamiento es pequeño y va a entrenar a un clasificador supervisado, la teoría de aprendizaje automático indica que debemos ceñirnos a un clasificador con gran variación de sesgo o a clasificadores con desviación baja como Naive Bayes. Estos tienen una ventaja sobre clasificadores de poca variación de sesgo y desviación alta como kNN, ya que los segundos tienden a sobreajustarse. Pero los clasificadores de variación de sesgo baja y desviación alta comienzan a ganar a medida que crece el conjunto de entrenamiento (tienen un error asintótico más pequeño), ya que los clasificadores de variación de sesgo alto no son lo suficientemente eficaces para proporcionar modelos precisos. Hay resultados teóricos y empíricos que indican que Bayes Naive se comporta correctamente en tales circunstancias. Pero tenga en cuenta que los mejores datos normalmente superan a los mejores algoritmos y que las buenas funciones de diseño proporcionan una ventaja considerable. Si tiene un gran conjunto de datos, a continuación, el algoritmo de clasificación que utiliza podría no ser de gran importancia para el rendimiento de clasificación. Por ello, es posible que resulte mejor elegir el algoritmo en función de  su buena escalabilidad, velocidad o facilidad de uso en su lugar.

**¿Necesita entrenar gradualmente o en un modo por lotes?** 
Si necesita actualizar con frecuencia el clasificador con nuevos datos (o si tiene una gran cantidad de datos), probablemente deba usar algoritmos bayesianos que realizan buenas actualizaciones. Las redes neuronales y SVM necesitan trabajar en los datos de entrenamiento en el modo por lotes.

**¿Son sus datos exclusivamente pertenecientes a categorías, numéricos o una mezcla de ambos tipos?** 
El bayesiano funciona mejor con datos de categorías/binomiales. Los árboles de decisión no pueden predecir valores numéricos.

**¿Necesita usted o su público entender cómo funciona el clasificador?**  Utilice árboles bayesianos o de decisión, ya que estos pueden explicarse fácilmente a la mayoría de las personas. Las redes neuronales y SVM son "cajas negras" en el sentido de que realmente no puede ver cómo están clasificando los datos.

**¿Con qué rapidez es necesario generar su clasificación?** 
Las SVM son rápidas en cuanto a la clasificación porque solo necesitan determinar en qué lado de la "línea" se encuentran los datos. Los árboles de decisión pueden ser lentos, especialmente cuando son complejos (por ejemplo, cuando presentan muchas bifurcaciones).

**¿Qué grado de complejidad presenta o requiere el problema?** Las redes neuronales y las SVM admiten la clasificación compleja no lineal.

### Ventajas y desventajas de los algoritmos de clasificación
Cada uno de estos algoritmos de clasificación tiene algunas ventajas y  algunas desventajas:

<a name="anchor-5a"></a>
**Ventajas y desventajas de la regresión logística:**   
El análisis de regresión logística se basa en calcular las probabilidades del resultado como la proporción de la probabilidad de tener el resultado dividido por la probabilidad de no tenerlo. El modelo de logístico  es paramétrico y por lo tanto, tiene la ventaja de que proporciona información sobre el impacto de cada variable de elemento de predicción en la variable de respuesta. Con las interpretaciones probabilísticas naturales disponibles (a diferencia de los árboles de decisión o SVM), podemos actualizar fácilmente nuestro modelo para aprovechar nuevos datos. Hay muchas maneras de regularizar nuestro modelo y no tiene que preocuparse tanto acerca de las funciones que se correlacionan, como ocurre con Naive Bayes. La regresión logística es útil si se desea 

* un marco probabilístico que facilite la tarea de ajustar los umbrales de clasificación a decir cuando no estemos seguros u obtener intervalos de confianza 
* si se espera recibir más datos de entrenamiento en el futuro que queremos ser capaces de incorporar rápidamente en nuestro modelo. 

La regresión logística funciona mejor que los árboles de decisión para datos de dimensión elevados. Por ejemplo, en la clasificación del texto puede tener más de 100.000 documentos con 500.000 palabras distintas (funciones). Es mejor una regla sencilla como el aprendizaje de un hiperplano lineal, puesto que los árboles de decisión tienen demasiados grados de libertad y son propensos al sobreajuste. Puede llevar a cabo la selección de funciones para usar un árbol de decisión en los datos de texto, pero se perderá mucha información valiosa para la clasificación del texto al elegir un subconjunto muy reducido de las funciones. Cuando se usan modelos de aprendizaje con datos muy dimensionales, es muy fácil que los errores basados en desviación aumenten de tamaño, por lo que los modelos sencillos con diferencia superior son más útiles. 

Una desventaja de la regresión logística es que es inestable cuando un predictor casi podría explicar la variable de la respuesta, ya que el coeficiente de esta variable será muy elevado.

<a name="anchor-5b"></a>
**Ventajas y desventajas de los árboles de decisión:**   
[Los árboles de decisión](http://research.microsoft.com/pubs/155552/decisionForests_MSR_TR_2011_114.pdf) son fáciles de interpretar y explicar. Controlan fácilmente las interacciones de funciones y son no paramétricos para no tener que preocuparse por los valores atípicos o de si los datos son linealmente separables (por ejemplo, los árboles de decisión atienden fácilmente casos en los que dispone de la clase A en el extremo inferior de una función x, clase B en el rango medio de la función x y A de nuevo en el extremo superior). Los árboles de decisión generarán el resultado como reglas junto con métricas como el soporte técnico, la confianza y la elevación. 

Una desventaja es que los árboles de decisión no son compatibles con el aprendizaje en línea, por lo que debe volver a generar el árbol cuando lleguen nuevos ejemplos. Otra desventaja es que se sobreajustan fácilmente, pero aquí es donde pueden ayudar los métodos de conjunto como bosques aleatorios (o árboles aumentados). Además, los bosques aleatorios son a menudo los ganadores para muchos problemas en la clasificación. Suelen hacerlo un poco mejor que las SVM: son rápidos y escalables, y no tiene que preocuparse acerca del ajuste de un conjunto de parámetros como lo haría con las SVM.


<a name="anchor-5c"></a>
**Ventajas y desventajas de SVM:**   
Las máquinas de vectores de soporte (SVM) son eficaces en espacios dimensionales altos. Incluso en casos en los que el número de dimensiones es mayor que el número de muestras continúa siendo eficaz. Sin embargo, si el número de funciones es mucho mayor que el número de muestras, es probable que el método no funcione correctamente. También permite ahorrar memoria, ya que usa un subconjunto de puntos de entrenamiento en la función de decisión (conocidos como vectores de soporte).  Es muy versátil: se pueden especificar diferentes funciones de Kernel para la función de decisión. Se proporcionan los kernels comunes, pero también es posible especificar los kernels personalizados. La función del kernel se utiliza para transformar los ejemplos de formación dimensional baja a dimensiones superiores, lo cual resulta útil para el problema de la cláusula de divisibilidad lineal. 

No obstante, las SVM no proporcionan directamente las estimaciones de probabilidad. Estas se calculan mediante una costosa validación cruzada repetida cinco veces. Con alta precisión, las garantías teóricas sólidas relacionadas con el sobreajuste y un kernel adecuado, pueden funcionar bien incluso si los datos no son separables linealmente en el espacio de la función de base. Es especialmente popular en problemas de clasificación de texto donde los espacios muy altamente dimensionales son la norma. A diferencia de los bosques, las SVM se originaron como clasificadores de dos clases, aunque recientemente se han adaptado para funcionar con varias clases. En ocasiones podemos usar algo como la formación de "uno contra los demás" para crear un clasificador de varias clases que puede ser menos óptimo. Puesto que las SVM solo pueden controlar las salidas de dos clases (es decir, una variable de salida categórica con variedad 2), con N clases, aprende N SVM (SVM 1 aprende "salida == 1" frente a "salida != 1", SVM 2 aprende "salida==2" frente a "salida != 2",..., SVM N aprende "salida== N" frente a "salida ! = N"). A continuación, para predecir el resultado de una nueva entrada, simplemente predice con cada SVM y busca cuál coloca la predicción más alejada en la región positiva.

<a name="anchor-5d"></a>
**Ventajas y desventajas de Bayes Naive:**   
Los clasificadores [Naive Bayes](http://www.aaai.org/Papers/FLAIRS/2004/Flairs04-097.pdf) (NB) son una opción popular para los problemas de clasificación. Suponen que las funciones son independientes, esto es lo que hace la técnica 'naive'. Si realmente se mantiene la suposición de independencia condicional de NB, un clasificador de Naive Bayes convergerá más rápidamente que los modelos discriminativos como la regresión logística. Por lo tanto, en estos casos, necesitará menos datos de entrenamiento. 

Aunque la suposición NB no se mantenga, un clasificador NB suelen hace un gran trabajo en la práctica.  Resulta que Bayes es buena no solo cuando las funciones son independientes, sino también cuando las dependencias de las funciones entre sí son similares entre las funciones. Por ello, NB es una buena apuesta si desea algo rápido y sencillo que actúe de manera respectiva.

Su principal inconveniente es que no aprenda de las interacciones entre funciones (por ejemplo, no puede aprender que aunque le encanten las películas con Brad Pitt y Tom Cruise, odie las películas donde aparecen juntos).


<a name="anchor-5e"></a>
**Uno frente todos:**  
Uno frente todos es una estrategia para reducir el problema de la clasificación de varias clases a un conjunto de varios problemas de clasificación binaria. La estrategia implica formar a un clasificador único por clase, con los ejemplos de esa clase como ejemplos positivos y el resto de ejemplos como negativos. Esta estrategia requiere los clasificadores base para producir una puntuación de confianza con valores reales para su decisión, en lugar de simplemente una etiqueta de clase. Las etiquetas de clase discreta solas pueden producir ambigüedades, donde se predicen varias clases para un único ejemplo.	


<a name="anchor-6"></a>
##**Regresión**  
En el análisis de regresión, predecimos nuevos valores basados en la inferencia pasada. Los nuevos valores de una variable dependiente se calculan basándose en el valor de uno o más atributos medidos. Los distintos algoritmos de regresión disponibles en Aprendizaje automático de Azure son:

![screenshot_of_experiment](./media/machine-learning-algorithm-choice/help4.png)

Según el caso de uso y los datos en cuestión, se elige un algoritmo o módulo sobre otro. A continuación, describiremos algunos algoritmos de regresión y sus casos de uso principales.

<a name="anchor-6b"></a>
**Regresión lineal bayesiana**                      
La regresión lineal de bayesiana es un enfoque para la regresión lineal en el que el análisis estadístico se lleva a cabo en el contexto de la inferencia bayesiana. Hay disponibles resultados explícitos para las distribuciones de probabilidad posteriores de los parámetros del modelo cuando el modelo de regresión tiene errores distribuidos normales y se puede suponer una forma especial de distribución anterior.

<a name="anchor-6f"></a>
**Regresión del árbol de decisión aumentada**  
La regresión calcula una relación entre predictor y variables de respuesta. La estructura de árbol de regresión es similar a un árbol de clasificación. Los nodos terminales son valores de función predichos (modelo). Los valores predichos están limitados a los valores de los nodos terminales. Algunas de las ventajas de utilizar los árboles de decisión son: 

* es fácil de interpretar las reglas de decisión 
* son no paramétricos, por lo que resulta fácil incorporar una gama de capas de datos categóricos o numéricos y no es necesario seleccionar los datos de formación unimodal 
* son sólidos en comparación con los valores atípicos de datos de formación 
* la clasificación es rápida una vez que se desarrollan las reglas 

Algunas desventajas de utilizar árboles de decisión es que tienden a sobreajustar los datos de entrenamiento, lo cual puede provocar la obtención de resultados inexactos cuando se aplica a todo el conjunto de datos y no es posible predecir los límites mínimo y máximo de la variable de respuesta en los datos de entrenamiento.

<a name="anchor-6g"></a>
**Regresión del bosque de decisión**  
Los bosques de decisión pueden utilizarse para aplicaciones de clasificación (variables de categorías) o de regresión (variables continuas). Los bosques de regresión pueden utilizarse para la regresión no lineal de variables dependientes a los que se otorga una entrada independiente. Las entradas y salidas pueden ser multidimensionales. Los árboles de regresión no se usan tanto como sus homólogos de clasificación. La principal diferencia es que la etiqueta de salida de los bosques de decisión a asociar con los datos de una entrada y por las etiquetas de entrenamiento debe ser continua. Por lo tanto, la función objetivo debe adaptarse adecuadamente. Los bosques de regresión comparten muchas de las ventajas de los bosques de clasificación como la eficacia y la flexibilidad.

<a name="anchor-6a"></a>
**Regresión lineal**  
Regresión lineal se usa ampliamente para modelar la relación entre una variable Y dependiente de escalar y una o más variables explicativas X. Puede, y normalmente se aplica, para la predicción, previsión o reducción. Se puede usar para ajustarse a un modelo de predicción para un conjunto de datos observado de valores X e Y. La regresión lineal supone que la estructura subyacente de Y es una combinación de lineal de las variables de X. Si, a continuación, se proporciona un valor adicional de X sin su correspondiente valor y, el modelo de regresión lineal ajustado puede utilizarse para predecir ese valor Y. Los modelos de regresión lineal normalmente están equipados con el enfoque de menos cuadrados, pero existen otras opciones también para medir qué opción es la mejor.

<a name="anchor-6c"></a>
**Regresión de red neuronal**  
Las redes neuronales son una herramienta estadística útil de regresión no paramétrica. La regresión no paramétrica trata el problema de tratar de ajustar un modelo para una variable Y en un conjunto de posibles variables explicativas X1; : : : ;Xp, y donde la relación entre X e Y pueden ser más complicada que una relación lineal simple.

<a name="anchor-6d"></a>
**Regresión ordinal**   
La regresión ordinal es un tipo de análisis de regresión utilizado para modelar o predecir una variable dependiente de un ordinal. Para variables dependientes ordinales, puede clasificar los valores, pero se desconoce la distancia real entre categorías. Solo es significativo el orden relativo entre los distintos valores. Puesto que las etiquetas o valores de destino tienen un orden clasificación natural, puede usarse cualquier columna numérica como destino de ordinal. El orden natural de los números se utiliza para clasificarlos. Las enfermedades se clasifican en escalas desde menos grave a más grave. Los encuestados eligen respuestas en escalas que van desde muy de acuerdo a muy en desacuerdo. Los alumnos son evaluados en escalas que van desde la A a la F. No obstante, esencialmente, la regresión ordinal es una extensión de regresión logística que se basa en el modelo de probabilidades proporcional.


<a name="anchor-6e"></a>
**Regresión de Poisson**  
La regresión POISSON se suele usar para el modelado de datos del contador. La regresión Poisson supone que la variable de respuesta tiene una distribución de Poisson. Los datos distribuidos mediante Poisson son intrínsecamente de enteros (positivos y discretos), lo cual tiene sentido para los datos del contador. Dado un conjunto de datos de entrenamiento, la regresión de Poisson intenta encontrar los valores óptimos mediante la maximización de la probabilidad del registro de los parámetros dadas las entradas. La probabilidad de los parámetros es la probabilidad de que los datos de entrenamiento se hayan tomado de una distribución con estos parámetros. Por ejemplo, la regresión de Poisson sería útil para: 

* Modelar el número de los resfriados asociados a los vuelos en avión 
* Obtener una estimación del número de llamadas relacionadas con un evento o promoción 
* Crear tablas de contingencia



<!--HONumber=49--> 