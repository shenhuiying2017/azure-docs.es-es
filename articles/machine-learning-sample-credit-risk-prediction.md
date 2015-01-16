<properties title="Azure Machine Learning Sample: Credit risk prediction" pageTitle="Ejemplo de aprendizaje automático: predicción de riesgo de crédito | Azure" description="Un experimento de aprendizaje automático de Azure de ejemplo para desarrollar un modelo de clasificación binaria que predice si un solicitante es un riesgo de crédito bajo o un riesgo de crédito alto." metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="garye" manager="paulettm" editor="cgronlun"  videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/23/2014" ms.author="garye" />


# Ejemplo de Aprendizaje automático de Azure: predicción de riesgo de crédito 

>[AZURE.NOTE]
>El [experimento de ejemplo] y el [conjunto de datos de ejemplo] asociado a este modelo están disponibles en Estudio de aprendizaje automático. Consulte la siguiente información para obtener más detalles.
[Experimento de ejemplo]: #sample-experiment
[Conjunto de datos de ejemplo]: #sample-dataset

*Para ver un tutorial detallado acerca de cómo crear y utilizar una versión simplificada de este experimento, consulte [Desarrollo de una solución predictiva con Aprendizaje automático de Azure](http://azure.microsoft.com/es-es/documentation/articles/machine-learning-walkthrough-develop-predictive-solution/).*

El objetivo de este experimento es predecir el riesgo de crédito de acuerdo con la información proporcionada en una aplicación de crédito. La predicción es un valor binario: bajo riesgo o alto riesgo. 


<!-- Removed until the Training and Scoring parts are fixed
This example is divided into 3 sample experiments:

- Development Experiment – for experimenting with different models
- Training Experiment – to train the one chosen model
- Scoring Experiment – to set up a web service using the trained model
-->

<!-- Removed because we added a section at the bottom describing the dataset
##Dataset Description

The experiment uses the UCI Statlog (German Credit Card) dataset which can be found here: 
<a href="http://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)">http://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)</a>. 
Vamos a usar el archivo german.data desde este sitio web.

El conjunto de datos clasifica a las personas, descritas por un conjunto de atributos, según si su riesgo de crédito es bajo o alto. Cada ejemplo representa a una persona. Hay 20 características, tanto numéricas como categóricas, y una etiqueta binaria (el valor del riesgo de crédito). Las entradas de riesgo de crédito alto tienen la etiqueta = 2; las entradas de riesgo de crédito bajo tienen la etiqueta = 1. El coste de clasificar erróneamente un ejemplo de riesgo bajo como alto es 1, mientras que el coste de clasificar erróneamente un ejemplo de riesgo alto como bajo es 5.
-->

##Experimento de desarrollo

El conjunto de datos original utiliza un formato separado por espacios en blanco. Hemos transformado el conjunto de datos a formato CSV y lo hemos cargado en Estudio de aprendizaje automático. Esta conversión se puede realizar mediante Powershell: 

	cat dataset.txt | %{$_ -replace " ",","} | sc german.csv

O utilizando el comando sed de Unix:

	sed 's/ /,/g' german.data > german.csv

Para empezar, utilizamos el **Editor de metadatos** para agregar columnas a fin de reemplazar los nombres de columna predeterminados del conjunto de datos por nombres más significativos tomados de la descripción del conjunto de datos del sitio de UCI. Los nuevos nombres de columna se separan con comas en el campo **Nuevo nombre de columna** del **Editor de metadatos**.

A continuación, generamos conjuntos de entrenamiento y prueba que se utilizan para desarrollar el modelo de predicción de riesgos. Dividimos el conjunto de datos original en conjuntos de entrenamiento y prueba del mismo tamaño utilizando el módulo **Dividir** con la opción **Proporción entre primeras filas de salida y entrada** establecida en 0,5.
 
Dado que el coste de clasificar erróneamente un ejemplo de alto riesgo como bajo es 5 veces superior al coste de clasificar erróneamente un riesgo bajo como alto, generamos un nuevo conjunto de datos que refleje la función de coste. En el nuevo conjunto de datos, cada ejemplo de alto riesgo se replica 5 veces, mientras que los ejemplos de bajo riesgo se quedan tal cual. La división de los conjuntos de datos en entrenamiento y prueba se lleva a cabo antes de esta replicación para impedir que el mismo ejemplo se encuentre tanto en el conjunto de prueba como en el de entrenamiento. 

Esta replicación se realiza mediante el siguiente código R, que se ejecuta utilizando el módulo **Ejecutar script R**:

	dataset1 <- maml.mapInputPort(1)
	data.set<-dataset1[dataset1[,21]==1,]
	pos<-dataset1[dataset1[,21]==2,]
	for (i in 1:5) data.set<-rbind(data.set,pos)
	maml.mapOutputPort("data.set")

En nuestro experimento comparamos dos enfoques para la generación de modelos: entrenamiento sobre el conjunto de datos original y entrenamiento sobre el conjunto de datos replicado. En ambos enfoques, para adaptarnos a la función de coste del problema, probamos en el conjunto de prueba con replicación. El flujo de trabajo final para la división y la replicación se describe a continuación. En este flujo de trabajo, el resultado de la izquierda del módulo **Dividir** es un conjunto de entrenamiento y el de la derecha es un conjunto de prueba. Tenga en cuenta que el conjunto de entrenamiento se utiliza posteriormente con y sin **Ejecutar script R**; esto es, con y sin replicación.

![Splitting training and test data][screen1]
 
Además de comprobar el efecto de la replicación de los ejemplos en el conjunto de entrenamiento, también comparamos el rendimiento de dos algoritmos: Máquina de vectores de soporte (SVM) y árbol de decisión ampliado. De este modo generamos de manera efectiva 4 modelos:

- SVM, entrenado con datos originales
- SVM, entrenado con datos replicados
- Árbol de decisión ampliado, entrenado con datos originales
- Árbol de decisión ampliado, entrenado con datos replicados

Los árboles de decisión ampliados funcionan bien con características de todo tipo. Sin embargo, dado que el módulo SVM genera un clasificador lineal, el modelo que genera tiene el mejor error de prueba cuando todas las características tienen la misma escala. Para convertir todas las características a la misma escala, utilizamos el módulo **Transformar datos mediante escalado** con una transformación tanh. Esta transformación transforma todas las características numéricas al rango [0,1]. Tenga en cuenta que las características de cadena se convierten por parte del módulo SVM a características categóricas y, a continuación, a características binarias 0/1; así pues, no es necesario transformar manualmente las características de cadena. 

Inicializamos el algoritmo de aprendizaje utilizando el módulo **Máquina de vectores de soporte de dos clases** o el módulo **Árbol de decisión ampliado de dos clases** y, a continuación, utilizamos el módulo **Entrenar modelo** para crear el módulo real. Estos módulos se utilizan por parte de los módulos **Puntuar modelo** para producir puntuaciones de ejemplos de prueba. A continuación se describe un ejemplo de flujo de trabajo que combina estos módulos y utiliza SVM y el conjunto de entrenamiento replicado. Tenga en cuenta que **Entrenar modelo** está conectado al conjunto de entrenamiento, mientras que **Puntuar modelo** está conectado al conjunto de prueba.

![Training and scoring a model][screen2]

En la etapa de evaluación del experimento se calcula la precisión de cada uno de los 4 modelos enumerados anteriormente. Para ello, utilizamos el módulo **Evaluar modelo**. Tenga en cuenta que este módulo solo calcula la precisión cuando todos los ejemplos tienen el mismo coste derivado de una clasificación errónea. Pero dado que anteriormente hemos replicado los ejemplos positivos, la precisión calculada por **Evaluar modelo** tiene en cuenta los costes y es 

![Accuracy computation][formula]

donde *n+* y *n-* son los números de los ejemplos positivo y negativo del conjunto de datos original, y *e+* y *e-* son los números de los ejemplos positivo y negativo mal clasificados del conjunto de datos original.

El módulo **Evaluar modelo** compara 2 modelos de puntuación; por tanto, utilizamos un módulo **Evaluar modelo** para comparar los 2 modelos SVM y otro para comparar los 2 modelos de árboles de decisión ampliado. Combinaremos ambos en una tabla para ver los 4 resultados. **Evaluar modelo** produce una tabla con una sola fila que contiene varias métricas. Utilizamos el módulo **Agregar filas** para combinar todos los resultados en una única tabla. A continuación anotamos la tabla con las precisiones de los 4 módulos utilizando un script R en el módulo **Ejecutar script R**, donde escribimos manualmente los nombres de las filas de la tabla final. Por último, quitamos las columnas con las métricas no relevantes utilizando el módulo **Columnas de proyecto**. 

Los resultados finales del experimento, obtenidos haciendo clic con el botón secundario en el resultado **Conjunto de datos de resultados** de **Columnas de proyecto** son:

![Results][results] 

donde la primera columna es el nombre del algoritmo de aprendizaje automático utilizado para generar un modelo, la segunda columna indica el tipo de conjunto de entrenamiento y la tercera columna es la precisión teniendo en cuenta los costes. En este experimento, el modelo SVM, utilizado con el conjunto de datos de entrenamiento replicado, proporciona el resultado más preciso.

<!-- Removed until the Training and Scoring parts are fixed
##Training Experiment

The sample training experiment is a simplified version of the larger experiment using just the chosen SVM training model. 

Notice that unlike the development experiment, in the training experiment we chose to load the dataset from Azure blob storage using the **Reader** module. Having the dataset stored in Azure is very common when it is generated by other programs. By reading the dataset directly from Azure we skip the step of manually uploading the dataset into ML Studio. The parameters of the **Reader** module are shown below. In this example, the storage account name is “datascience” and the dataset file “german.csv” is placed in container “sampleexperiments”. The account key is an access key of an Azure storage account. This key can be retrieved from your account in the Azure management portal.

![Azure storage parameters][screen3] 

##Scoring Experiment

The purpose of the sample scoring experiment is to set up a REST API web service that will score test examples. The trained model in this experiment (“Credit Risk model”) was created from the training experiment by right-clicking the Train Model module and selecting **Save as Trained Model**. In this scoring experiment we load test examples, normalize them, and perform scoring using this saved trained model. 

After running this experiment and verifying that it generates the right scores we prepare to publish it as a web service by defining the service input and output. We define the web service input as the input port to the **Transform Data By Scaling** module by right-clicking the port and selecting **Set as Publish Input**. The web service output is set to the output of the **Score Model** module by right-clicking the output of **Score Model** and selecting **Set as Publish Output**. 

After setting up the service input and output we need to rerun the experiment and then click **Publish Web Service**. This publishes the web service to the staging environment and takes us to the ML Studio **WEB SERVICES** page. Here we can configure and test the service with sample data.

When the service is ready to go live, go to the **CONFIGURATION** tab on the **WEB SERVICES** page and click **READY FOR PRODUCTION?**. A request will be sent to the IT administrator for Machine Learning who can promote the service to the production environment.

![Web service ready for production][screen4] 
-->

## Experimento de ejemplo

Puede encontrar el siguiente experimento de ejemplo asociado a este modelo en Estudio de aprendizaje automático, en la sección **EXPERIMENTOS** bajo la pestaña **EJEMPLOS**.

> **Experimento de ejemplo - Crédito alemán - Desarrollo**


## Conjunto de datos de ejemplo

El siguiente conjunto de datos de ejemplo que se usa en este experimento está disponible en Estudio de aprendizaje automático en la paleta de módulos bajo **Conjuntos de datos guardados**.

###Conjunto de datos UCI de tarjeta de crédito alemana
[AZURE.INCLUDE [machine-learning-sample-dataset-german-credit-card-uci-dataset](../includes/machine-learning-sample-dataset-german-credit-card-uci-dataset.md)]



[screen1]:./media/machine-learning-sample-credit-risk-prediction/screen1.jpg
[screen2]:./media/machine-learning-sample-credit-risk-prediction/screen2.jpg
[formula]:./media/machine-learning-sample-credit-risk-prediction/formula.jpg
[results]:./media/machine-learning-sample-credit-risk-prediction/results.jpg
[screen3]:./media/machine-learning-sample-credit-risk-prediction/screen3.jpg
[screen4]:./media/machine-learning-sample-credit-risk-prediction/screen4.jpg
