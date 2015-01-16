<properties title="Azure Machine Learning Sample: Network intrusion detection" pageTitle="Ejemplo de aprendizaje automático: detección de intrusión en red | Azure" description="Un experimento de aprendizaje automático de Azure de ejemplo que utiliza un modelo de clasificación para determinar qué actividades de red forman parte de la intrusión en la red." metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="garye" manager="paulettm" editor="cgronlun"  videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/23/2014" ms.author="garye" />



# Ejemplo de Aprendizaje automático de Azure: detección de intrusión en red

>[AZURE.NOTE]
>El [experimento de ejemplo] y el [conjunto de datos de ejemplo] asociado a este modelo están disponibles en Estudio de aprendizaje automático. Consulte la siguiente información para obtener más detalles.
[Experimento de ejemplo]: #sample-experiment
[Conjunto de datos de ejemplo]: #sample-dataset


## Descripción del problema ##

La demostración utiliza varias características de red para detectar cuáles de las actividades de la red forman parte de una intrusión o un ataque. Este problema de clasificación binaria no es demasiado difícil, ya que las características son bastante predictivas de una clase y las clases están bastante equilibradas.  Como la etiqueta consta de varias características, es importante quitar todas las partes de la etiqueta antes de entrenar un clasificador. 

## Datos ##

1. Los datos, tomados de la competición KDD Cup 1999, incluye conjuntos de datos tanto de entrenamiento como de prueba.  Las precisiones en la clasificación del conjunto de datos de entrenamiento son históricamente superiores a las del conjunto de datos de prueba, ya que las intrusiones de red se introducen en el conjunto de prueba.  El conjunto de datos de entrenamiento tiene aproximadamente 126.000 filas y 43 columnas, incluidas las etiquetas.  Como tres columnas forman parte de la información de etiquetas, existen 40 columnas disponibles para entrenar el modelo.  Estas 40 columnas contienen mayoritariamente valores numéricos con unas pocas características de cadena/categóricas. Los datos de prueba tienen aproximadamente 22.500 ejemplos de prueba (con las mismas 43 columnas en datos de entrenamiento). 
1. Los datos se cargaron a un blob público para facilitar el acceso a los mismos. Aprendizaje automático de Azure proporciona módulos de lectura para leer datos desde diferentes medios de almacenamiento, como blobs, con solo unos clics, sin que sea necesario escribir ningún código. 

## Modelo ##

1. El primer paso del modelo es condicionar los datos para su posterior clasificación.  La columna "Clase" contiene las etiquetas para predecir.  Estas etiquetas son "normal" (sin intrusiones) o el nombre de un ataque.  Algunos ataques no tienen muchos ejemplos en los datos de entrenamiento y hay nuevos ataques en el conjunto de datos de prueba, por lo que convertimos estas etiquetas multiclase en etiquetas de clase binaria para hacer posible el problema.  El estudio proporciona un módulo incorporado para facilitar este paso de procesamiento previo. El módulo de valor del indicador se utiliza para convertir las etiquetas de clase en valores binarios y, a continuación, el módulo de columna de proyecto se utiliza para seleccionar esta columna de etiqueta de clase binarizada (excluyendo la columna de etiqueta de clase original).  
1. Como parte del desarrollo de este experimento, vemos a través de la correlación/selección de características que muchas características guardan una elevada correlación con la etiqueta. Esto es habitual en conjuntos de datos sintéticos.  El aprendizaje automático en la nube proporciona esta visualización con solo unos clics; para hacerlo, seleccione la opción de visualizar en el resultado del módulo de proyecto y haga clic en el encabezado de cualquiera de las características para iniciar el visualizador y, a continuación, seleccione la etiqueta de la clase en la comparación para desplegarla.
1. Dado que un reducido número de características guarda una elevada correlación con la etiqueta, probaremos combinaciones de clasificadores lineales y no lineales con y sin selección de características para determinar cuál es el mejor modelo.  La comparación se lleva a cabo a través del módulo del modelo de evaluación.    

## Resultados ##

1. El clasificador no lineal (árbol de decisión ampliado) obtiene resultados ligeramente mejores que el clasificador lineal (regresión logística): 

![][1]


Ahora queremos comparar un árbol de decisión ampliado con la selección de características. Los resultados de la clasificación son bastante similares, pero el árbol de decisión ampliado en todas las características es ligeramente mejor, y se utilizará para el flujo de trabajo de puntuación.  Tenga en cuenta que esta AUC de alta clasificación es habitual para este conjunto de datos. 


![][2]

<!-- Removed until this part is fixed
## Operationalization ##


We wanted to see our model in action, to do this we wanted a request-response service around the model we just learned. Doing this is straightforward and can be achieved in few simple clicks in studio: 

1. First step is to save the learned model (by right clicking on the classifier module output) 
1. Now create a new experiment and search for saved model and drop it in the panel for new experiment 
1. Then we need to replicate the steps we did for pre-process, again we can just select the modules from experiment we need and copy and paste to the new experiment 
1. The last step is to plug in the test data and run the experiment, once the run is finished we can define the input and output points for the service 
1. Again, using one click we can publish the service end points that is deployed right away and can be tested using a friendly user interface - all within studio   
-->


## Experimento de ejemplo

Puede encontrar el siguiente experimento de ejemplo asociado a este modelo en Estudio de aprendizaje automático, en la sección **EXPERIMENTOS** bajo la pestaña **EJEMPLOS**.

> **Experimentos de ejemplo - Detección de intrusiones de red - Desarrollo**


## Conjunto de datos de ejemplo

El siguiente conjunto de datos de ejemplo que se usa en este experimento está disponible en Estudio de aprendizaje automático en la paleta de módulos bajo **Conjuntos de datos guardados**.

###network_intrusion_detection.csv
[AZURE.INCLUDE [machine-learning-sample-dataset-network-intrusion](../includes/machine-learning-sample-dataset-network-intrusion.md)]




[1]: ./media/machine-learning-sample-network-intrusion-detection/network-intrusion-detection-1.png
[2]: ./media/machine-learning-sample-network-intrusion-detection/network-intrusion-detection-2.png
