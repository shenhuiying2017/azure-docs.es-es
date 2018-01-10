---
title: "Reconocimiento de entidades biomédicas: proceso de ciencia de los datos en equipos con Azure Machine Learning | Microsoft Docs"
description: "Guía de inicio rápido sobre proyectos del proceso de ciencia de los datos en equipos que usa el aprendizaje profundo para el reconocimiento de entidades biomédicas en Azure Machine Learning Workbench."
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/10/2017
ms.author: bradsev
ms.openlocfilehash: 7de3a30e477fcec66ce703b6c3fec7d17d79d3ab
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/18/2017
---
# <a name="biomedical-entity-recognition-using-team-data-science-process-tdsp-template"></a>Reconocimiento de entidades biomédicas mediante la plantilla del proceso de ciencia de los datos en equipos (TDSP)

La extracción de la entidad es una subtarea de extracción de información (también conocida como [reconocimiento de entidades con nombre (ER)](https://en.wikipedia.org/wiki/Named-entity_recognition), identificación de la entidad y fragmentación de entidad). El objetivo de este escenario real es explicar cómo se usa Azure Machine Learning Workbench para resolver una tarea complicada de procesamiento de lenguaje natural (NLP), como la extracción de entidades contenidas en texto no estructurado:

1. Se explica cómo entrenar un modelo de incrustación de palabras neuronales en un corpus de texto de aproximadamente 18 millones de resúmenes de PubMed mediante la [implementación de Word2Vec de Spark](https://spark.apache.org/docs/latest/mllib-feature-extraction.html#word2vec).
2. Se explica cómo compilar un modelo de red neuronal recurrente profundo de memoria a corto y largo plazo (LSTM) para la extracción de entidades en una máquina virtual de ciencia de datos de Azure habilitada para GPU (GPU DS VM) en Azure.
2. Se muestra que el rendimiento del modelo de incrustaciones de palabras específicas de un dominio puede superar el de los modelos de incrustaciones de palabras genéricas en la tarea de reconocimiento de entidades. 
3. Se muestra cómo entrenar y hacer operativos los modelos de aprendizaje profundo mediante Azure Machine Learning Workbench.

4. Se muestran las capacidades siguientes de Azure Machine Learning Workbench:

    * Creación de instancias de [estructura y plantillas del proceso de ciencia de los datos en equipos (TDSP)](how-to-use-tdsp-in-azure-ml.md).
    * Administración automatizada de las dependencias del proyecto, incluidas la descarga y la instalación.
    * Ejecución de scripts de Python en distintos entornos de proceso.
    * Seguimiento del historial de ejecuciones de scripts de Python.
    * Ejecución de trabajos en un contexto de proceso remoto de Spark mediante clústeres de HDInsight Spark 2.1.
    * Ejecución de trabajos en máquinas virtuales de GPU remota en Azure.
    * Puesta en marcha sencilla de modelos de aprendizaje profundo como servicios web en Azure Container Service (ACS).

## <a name="use-case-overview"></a>Información general del caso de uso
El reconocimiento de entidades biomédicas con nombre es un paso esencial para las tareas biomédicas complejas de procesamiento de lenguaje natural, como: 
* Extracción de menciones de entidades con nombre como enfermedades, medicamentos, elementos químicos y síntomas de registros electrónicos sanitarios o médicos.
* Detección de fármacos.
* Interpretación de las interacciones entre los diferentes tipos de entidades, como la interacción entre los fármacos, la relación entre los fármacos y las enfermedades, y la relación entre los genes y las proteínas.

El escenario de nuestro caso de uso se centra en cómo se puede analizar una gran cantidad de corpus de datos no estructurados, como resúmenes de PubMed de Medline, para entrenar un modelo de incrustación de palabras. Después, las incrustaciones de salida se consideran características generadas automáticamente para entrenar un extractor de entidades neuronales.

Nuestros resultados muestran que el rendimiento del entrenamiento del modelo de extracción de entidades biomédicas en las características de incrustación de palabras específicas de un dominio supera el del modelo entrenado en el tipo de características genéricas. El modelo específico de dominio puede detectar 7012 entidades correctamente (de 9475) con una puntuación F1 de 0,73, frente a las 5274 entidades con una puntuación F1 de 0,61 en el caso del modelo genérico.

En la imagen siguiente se muestra la arquitectura que se ha usado para procesar los datos y entrenar los modelos.

![Architecture](./media/scenario-tdsp-biomedical-recognition/architecture.png)

## <a name="data-description"></a>Descripción de los datos

### <a name="1-word2vec-model-training-data"></a>1. Datos de entrenamiento del modelo de Word2Vec
En primer lugar, se descargan de [MEDLINE](https://www.nlm.nih.gov/pubs/factsheets/medline.html) los datos de resúmenes de MEDLINE sin formato. Los datos están disponibles públicamente en forma de archivos XML en su [servidor FTP](https://ftp.ncbi.nlm.nih.gov/pubmed/baseline). Hay 892 archivos XML en el servidor, y cada uno de los archivos XML contiene información de 30 000 artículos. Se proporcionan más detalles sobre el paso de recopilación de datos en la sección Estructura del proyecto. Los campos presentes en cada archivo son los siguientes: 
        
        abstract
        affiliation
        authors
        country 
        delete: boolean if False means paper got updated so you might have two XMLs for the same paper.
        file_name   
        issn_linking    
        journal 
        keywords    
        medline_ta: this is abbreviation of the journal nam 
        mesh_terms: list of MeSH terms  
        nlm_unique_id   
        other_id: Other IDs 
        pmc: Pubmed Central ID  
        pmid: Pubmed ID
        pubdate: Publication date
        title

### <a name="2-lstm-model-training-data"></a>2. Datos de entrenamiento del modelo de LSTM

El modelo de extracción de entidades neuronales se ha entrenado y evaluado con conjuntos de datos disponibles públicamente. Para obtener una descripción detallada de estos conjuntos de datos, consulte los recursos siguientes:
 * [Tarea de reconocimiento de bioentidades en BioNLP/NLPBA 2004](http://www.nactem.ac.uk/tsujii/GENIA/ERtask/report.html)
 * [Corpus de tareas CDR de BioCreative V](http://www.biocreative.org/tasks/biocreative-v/track-3-cdr/)
 * [Tarea 9.1 de Semeval 2013 (reconocimiento de fármacos)](https://www.cs.york.ac.uk/semeval-2013/task9/)

## <a name="link-to-the-azure-gallery-github-repository"></a>Vínculo al repositorio de GitHub de la galería de Azure
A continuación se muestra el vínculo al repositorio público de GitHub del escenario real que contiene el código y una descripción más detallada: 

[https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction)


## <a name="prerequisites"></a>requisitos previos 

* Una suscripción de [Azure](https://azure.microsoft.com/free/).
* Azure Machine Learning Workbench. Vea la [guía de instalación](quickstart-installation.md). Actualmente, Azure Machine Learning Workbench solo se puede instalar en los siguientes sistemas operativos: 
    * Windows 10 o Windows Server 2016
    * macOS Sierra (o una versión posterior)


### <a name="azure-services"></a>Servicios de Azure
* [Clúster de HDInsight Spark](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-jupyter-spark-sql) versión Spark 2.1 en Linux (HDI 3.6) para el cálculo de escalabilidad horizontal. Para procesar todos los resúmenes de MEDLINE indicados a continuación, se necesita la configuración mínima de: 
    * Nodo principal: tamaño [D13_V2](https://azure.microsoft.com/pricing/details/hdinsight/)
    * Nodos de trabajo: al menos 4 de [D12_V2](https://azure.microsoft.com/pricing/details/hdinsight/). En nuestro trabajo, hemos usado 11 nodos de trabajo de tamaño D12_V2.
* [Máquina virtual de ciencia de datos (DSVM) NC6](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-linux-dsvm-intro) para el cálculo de la escalabilidad vertical.

### <a name="python-packages"></a>Paquetes de Python

Todas las dependencias necesarias están definidas en el archivo aml_config/conda_dependencies.yml en la carpeta de proyecto del escenario. Las dependencias definidas en este archivo se aprovisionarán automáticamente para las ejecuciones en destinos de Docker, la máquina virtual y el clúster de HDI. Para obtener más información sobre el formato de archivo del entorno de Conda, haga clic [aquí](https://conda.io/docs/using/envs.html#create-environment-file-by-hand).

* [TensorFlow](https://www.tensorflow.org/install/)
* [CNTK 2.0](https://docs.microsoft.com/cognitive-toolkit/using-cntk-with-keras)
* [Keras](https://keras.io/#installation)
* NLTK
* Fastparquet

### <a name="basic-instructions-for-azure-machine-learning-aml-workbench"></a>Instrucciones básicas para Azure Machine Learning (AML) Workbench
* [Información general](overview-what-is-azure-ml.md)
* [Instalación](quickstart-installation.md)
* [Uso de TDSP](how-to-use-tdsp-in-azure-ml.md)
* [Cómo leer y escribir archivos](how-to-read-write-files.md)
* [Cómo usar Jupyter Notebook](how-to-use-jupyter-notebooks.md)
* [Cómo usar GPU](how-to-use-gpu.md)

## <a name="scenario-structure"></a>Estructura del escenario
Para el escenario, se usa la estructura del proyecto y las plantillas de documentación de TDSP (figura 1), que siguen el [ciclo de vida de TDSP](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/lifecycle-detail.md). El proyecto se crea según las instrucciones proporcionadas [aquí](./how-to-use-tdsp-in-azure-ml.md).


![Información del proyecto para rellenar](./media/scenario-tdsp-biomedical-recognition/instantiation-3.png) 

El flujo de trabajo detallado de ciencia de datos es el siguiente:

### <a name="1-data-acquisition-and-understanding"></a>1. Adquisición y comprensión de los datos

Vea [Data Acquisition and Understanding](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/blob/master/code/01_data_acquisition_and_understanding/ReadMe.md) (Adquisición y comprensión de los datos).

El corpus de MEDLINE sin formato tiene un total de 27 millones de resúmenes, donde aproximadamente 10 millones de artículos tienen un campo abstracto vacío. Se usa Azure HDInsight Spark para procesar macrodatos que no se pueden cargar en la memoria de un solo equipo como una [trama de datos de Pandas](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.html). En primer lugar, se descargan los datos en el clúster de Spark. Después, se ejecutan los pasos siguientes en la [trama de datos de Spark](https://spark.apache.org/docs/latest/sql-programming-guide.html): 
* Se analizan los archivos XML mediante el analizador XML de Medline.
* Se preprocesa el texto abstracto, incluida la división de oraciones, la tokenización y la normalización de mayúsculas.
* Se excluyen los artículos en los que el campo abstracto está vacío o tiene un texto breve. 
* Se crea el vocabulario a partir de los resúmenes de entrenamiento.
* Se entrena el modelo neuronal de incrustación de palabras. Para obtener más información, vea el [vínculo del código de GitHub](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/blob/master/code/01_data_acquisition_and_understanding/ReadMe.md) para empezar a trabajar.


Después de analizar los archivos XML, los datos tienen el formato siguiente: 

![Ejemplo de datos](./media/scenario-tdsp-biomedical-recognition/datasample.png)

El modelo de extracción de entidades neuronales se ha entrenado y evaluado con conjuntos de datos disponibles públicamente. Para obtener una descripción detallada de estos conjuntos de datos, consulte los recursos siguientes:
 * [Tarea de reconocimiento de bioentidades en BioNLP/NLPBA 2004](http://www.nactem.ac.uk/tsujii/GENIA/ERtask/report.html)
 * [Corpus de tareas CDR de BioCreative V](http://www.biocreative.org/tasks/biocreative-v/track-3-cdr/)
 * [Tarea 9.1 de Semeval 2013 (reconocimiento de fármacos)](https://www.cs.york.ac.uk/semeval-2013/task9/)

### <a name="2-modeling"></a>2. Modelado

Vea [Modeling](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/tree/master/code/02_modeling) (Modelado).

El modelado es la fase en la que le mostramos cómo puede usar los datos descargados en la sección anterior para entrenar su propio modelo de incrustación de palabras y usarlo para otras tareas que siguen en la cadena. Aunque usamos los datos de PubMed, la canalización para generar las incrustaciones es genérica y se puede reutilizar para entrenar incrustaciones de palabras para cualquier otro dominio. Para que las incrustaciones sean una representación precisa de los datos, es fundamental que word2vec se entrene con una gran cantidad de datos.
Cuando las incrustaciones de palabras estén listas, se puede entrenar un modelo de red neuronal profunda que use las incrustaciones aprendidas para inicializar la capa de incrustación. El nivel de incrustación se marca como no entrenable, pero esto no es obligatorio. El entrenamiento del modelo de incrustación de palabras no se supervisa y, por tanto, se pueden aprovechar las ventajas que suponen los textos sin etiquetas. Aun así, el entrenamiento del modelo de reconocimiento de entidades es una tarea de aprendizaje supervisada y su precisión depende de la cantidad y la calidad de los datos anotados manualmente. 


#### <a name="21-feature-generation"></a>2.1. Generación de características

Vea [Feature generation](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/tree/master/code/02_modeling/01_feature_engineering) (Generación de características).

Word2Vec es el algoritmo de aprendizaje sin supervisar de incrustación de palabras que entrena un modelo de red neuronal desde un corpus de entrenamiento sin etiquetas. Genera un vector continuo para cada palabra del corpus que representa su información semántica. Estos modelos son redes neuronales simples con una única capa oculta. Los vectores/incrustaciones de palabras se aprenden mediante la propagación hacia atrás y el descenso de gradiente estocástico. Hay dos tipos de modelos de word2vec, denominados Skip-Gram y Continuous Bag Of Words (haga clic [aquí](https://arxiv.org/pdf/1301.3781.pdf) para obtener más información). Dado que usamos la implementación de word2vec de MLlib, que es compatible con el modelo Skip-Gram, lo describiremos brevemente (imagen procedente de este [vínculo](https://ahmedhanibrahim.wordpress.com/2017/04/25/thesis-tutorials-i-understanding-word2vec-for-word-embedding-i/)): 

![Modelo Skip-Gram](./media/scenario-tdsp-biomedical-recognition/skip-gram.png)

El modelo usa softmax jerárquico y el muestreo negativo para optimizar el rendimiento. El softmax jerárquico (H-SoftMax) es una aproximación inspirada en árboles binarios. Básicamente, H-SoftMax reemplaza la capa de softmax sin formato por una capa jerárquica que contiene las palabras como hojas. Esto nos permite descomponer el cálculo de la probabilidad de una palabra en una secuencia de cálculos de probabilidad, lo que nos evita tener que calcular una normalización costosa en todas las palabras. Puesto que un árbol binario equilibrado tiene una profundidad de log2(|V|) (V es el vocabulario), solo se necesita evaluar a lo sumo log2(|V|) nodos para obtener la probabilidad final de una palabra. La probabilidad de una palabra p dado su contexto c es simplemente el producto de las probabilidades de dar giros a la derecha y a la izquierda respectivamente que lleven a su nodo hoja. Se puede crear un árbol de Huffman en función de la frecuencia de las palabras del conjunto de datos para asegurarse de que las palabras más frecuentes obtengan representaciones más cortas. Para obtener más información, vea [este vínculo](http://sebastianruder.com/word-embeddings-softmax/).
Imagen tomada de [aquí](https://ahmedhanibrahim.wordpress.com/2017/04/25/thesis-tutorials-i-understanding-word2vec-for-word-embedding-i/).

##### <a name="visualization"></a>Visualización

Una vez que tenemos las incrustaciones de palabras, nos gustaría visualizarlas y ver la relación que existe entre palabras similares semánticamente. 

![Similitud de W2V](./media/scenario-tdsp-biomedical-recognition/w2v-sim.png)

Hemos mostrado dos formas diferentes de visualizar las incrustaciones. La primera usa PCA para proyectar el vector con una alta dimensionalidad en un espacio de vector 2D. Esto produce una pérdida considerable de información y la visualización no es tan precisa. La segunda consiste en usar PCA con [t-SNE](https://distill.pub/2016/misread-tsne/). t-SNE es una técnica de reducción de dimensionalidad no lineal que resulta ideal para incrustar datos con una alta dimensionalidad en un espacio de dos o tres dimensiones, que después se pueden visualizar en un gráfico de dispersión.  Modela cada objeto con una alta dimensionalidad mediante un punto de dos o tres dimensiones, de tal manera que los objetos similares se modelan mediante puntos cercanos y los objetos dispares se modelan mediante puntos lejanos. Funciona en dos partes. En primer lugar, crea una distribución de probabilidad en los pares del espacio con más alta dimensionalidad, de forma que hay una gran probabilidad de que se seleccionen objetos similares y una baja probabilidad de que se seleccionen puntos dispares. En segundo lugar, define una distribución de probabilidad similar en los puntos de un mapa con baja dimensionalidad y minimiza la divergencia de KL entre las dos distribuciones con respecto a la ubicación de los puntos en el mapa. Para obtener la ubicación de los puntos en la dimensión baja, se minimiza la divergencia de KL mediante el descenso de gradiente. A pesar de todo, t-SNE podría no ser de confianza en todo momento. Encontrará [aquí](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/tree/master/code/02_modeling/01_feature_engineering) más detalles sobre la implementación. 


Como se muestra en la imagen siguiente, la visualización de t-SNE proporciona una mayor separación de los vectores de palabras y los posibles modelos de agrupación en clústeres. 


* Visualización con PCA

![PCA](./media/scenario-tdsp-biomedical-recognition/pca.png)

* Visualización con t-SNE

![t-SNE](./media/scenario-tdsp-biomedical-recognition/tsne.png)

* Puntos más cercanos a "Cáncer" (todos son subtipos de cáncer)

![Puntos más cercanos a "Cáncer"](./media/scenario-tdsp-biomedical-recognition/nearesttocancer.png)

#### <a name="22-train-the-neural-entity-extractor"></a>2.2. Entrenar el extractor de entidades neuronales

Vea [Train the neural entity extractor](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/tree/master/code/02_modeling/02_model_creation/ReadMe.md) (Entrenar el extractor de entidades neuronales).

La arquitectura de red neuronal de prealimentacion tiene un problema que consiste en tratar cada entrada y salida como independiente de las demás entradas y salidas. Esta arquitectura no puede modelar tareas de etiquetado de secuencia en secuencia, como la traducción automática y la extracción de entidades. Los modelos de red neuronal recurrente resuelven este problema al pasar la información calculada hasta el momento al nodo siguiente. Esta propiedad se denomina "tener memoria en la red", ya que puede usar la información calculada previamente como se muestra en la imagen siguiente:

![Red neuronal recurrente](./media/scenario-tdsp-biomedical-recognition/rnn-expanded.png)

Las redes neuronales recurrentes básicas sufren el [problema del gradiente evanescente](https://en.wikipedia.org/wiki/Vanishing_gradient_problem), por lo que no pueden usar toda la información que ya han visto. El problema solo se vuelve evidente cuando es necesaria una gran cantidad de contexto para realizar una predicción. Pero los modelos como LSTM no sufren este problema; de hecho, están diseñados para recordar las dependencias a largo plazo. A diferencia de las redes neuronales recurrentes básicas que tienen una sola red neuronal, las LSTM tienen interacciones entre cuatro redes neuronales para cada célula. Para obtener una explicación detallada de cómo funciona LSTM, vea [esta entrada](http://colah.github.io/posts/2015-08-Understanding-LSTMs/).

![Célula LSTM](./media/scenario-tdsp-biomedical-recognition/lstm-cell.png)

Vamos a intentar crear nuestra propia red neuronal recurrente basada en LSTM y a intentar extraer los tipos de entidades, como menciones a fármacos, enfermedades y síntomas en los datos de PubMed. Como cabría suponer, el primer paso consiste en obtener una gran cantidad de datos etiquetados, algo que no es nada sencillo. La mayoría de los datos médicos contienen gran cantidad de información confidencial sobre las personas y, por lo tanto, no están disponibles públicamente. Nos basaremos en una combinación de dos conjuntos de datos diferentes que están disponibles públicamente. El primer conjunto de datos es la tarea 9.1 de Semeval 2013 (reconocimiento de fármacos) y el segundo es la tarea CDR de BioCreative V. Vamos a combinar y etiquetar automáticamente estos dos conjuntos de datos, para poder detectar fármacos y enfermedades en textos médicos y evaluar nuestras incrustaciones de palabras. Para obtener detalles de implementación, vea el [vínculo del código de GitHub](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/tree/master/code/02_modeling/02_model_creation).

La arquitectura del modelo que hemos usado en todos los códigos y para la comparación se muestra más adelante. El parámetro que cambia para los diferentes conjuntos de datos es la longitud de secuencia máxima (en este caso, 613).

![Modelo LSTM](./media/scenario-tdsp-biomedical-recognition/d-a-d-model.png)

#### <a name="23-model-evaluation"></a>2.3. Evaluación del modelo
Vea [Evaluación del modelo](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/tree/master/code/02_modeling/03_model_evaluation/ReadMe.md).

Para evaluar la precisión, la recuperación y la puntuación F1 del modelo, se usa el script de evaluación de la tarea compartida de [reconocimiento de bioentidades en BioNLP/NLPBA 2004](http://www.nactem.ac.uk/tsujii/GENIA/ERtask/report.html). 

#### <a name="in-domain-versus-generic-word-embedding-models"></a>El modelo de incrustación de palabras genéricas frente al modelo de incrustación de palabras en dominio

A continuación se muestra una comparación entre la precisión de dos tipos de características: (1) incrustaciones de palabras entrenadas en resúmenes de PubMed e (2) incrustaciones de palabras entrenadas en Google Noticias. Se ve claramente que el rendimiento del modelo en el dominio supera el del modelo genérico. Por lo tanto, es mucho más útil tener un modelo de incrustación de palabras específico que usar uno genérico. 

* Tarea n.º 1: detección de fármacos y enfermedades

![Comparación de modelos 1](./media/scenario-tdsp-biomedical-recognition/mc1.png)

Si realizamos la evaluación de las incrustaciones de palabras en otros conjuntos de datos de forma similar, veremos que el modelo en el dominio siempre es mejor.

* Tarea n.º 2: detección de proteínas, estirpe celular, tipo de célula, ADN y ARN

![Comparación de modelos 2](./media/scenario-tdsp-biomedical-recognition/mc2.png)

* Tarea n.º 3: detección de sustancias químicas y enfermedades

![Comparación de modelos 3](./media/scenario-tdsp-biomedical-recognition/mc3.png)

* Tarea n.º 4: detección de fármacos

![Comparación de modelos 4](./media/scenario-tdsp-biomedical-recognition/mc4.png)

* Tarea n.º 5: detección de genes

![Comparación de modelos 5](./media/scenario-tdsp-biomedical-recognition/mc5.png)

#### <a name="tensorflow-versus-cntk"></a>TensorFlow frente a CNTK
Todos los modelos indicados están entrenados mediante Keras con TensorFlow como back-end. Keras con el back-end de CNTK no admitía la "inversión" en el momento en que se realizó este trabajo. Por lo tanto, para realizar la comparación, hemos entrenado un modelo LSTM unidireccional con el back-end de CNTK y lo hemos comparado con un modelo LSTM unidireccional con el back-end de TensorFlow. Instale CNTK 2.0 para Keras desde [aquí](https://docs.microsoft.com/cognitive-toolkit/using-cntk-with-keras). 

![Comparación de modelos 6](./media/scenario-tdsp-biomedical-recognition/mc6.png)

Hemos llegado a la conclusión de que CNTK tiene tan buen rendimiento como Tensorflow en lo que respecta al tiempo de entrenamiento por época (60 segundos para CNTK y 75 segundos para Tensorflow) y al número de entidades de prueba detectado. Para la evaluación, usamos las capas unidireccionales.


### <a name="3-deployment"></a>3. Implementación

Vea [Deployment](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/tree/master/code/03_deployment) (Implementación).

Hemos implementado un servicio web en un clúster en [Azure Container Service (ACS)](https://azure.microsoft.com/services/container-service/). El entorno de puesta en marcha aprovisiona Docker y Kubernetes en el clúster para administrar la implementación del servicio web. [Aquí](model-management-service-deploy.md ) encontrará más información sobre el proceso de puesta en marcha.


## <a name="conclusion"></a>Conclusión

Hemos explicado los detalles del proceso que se lleva a cabo para entrenar un modelo de incrustación de palabras mediante el algoritmo Word2Vec en Spark y, después, hemos usado las incrustaciones extraídas como características para entrenar una red neuronal profunda para la extracción de entidades. Hemos aplicado la canalización de entrenamiento en el ámbito biomédico. A pesar de ello, la canalización es lo bastante genérica para aplicarse a la detección de tipos de entidades personalizados de cualquier otro dominio. Basta con disponer de datos suficientes para adaptar fácilmente el flujo de trabajo que se presenta aquí a un dominio diferente.

## <a name="references"></a>Referencias

* Tomas Mikolov, Kai Chen, Greg Corrado y Jeffrey Dean. 2013a. Efficient estimation of word representations in vector space. En las actas de la ICLR.
* Tomas Mikolov, Ilya Sutskever, Kai Chen, Greg S Corrado y Jeff Dean. 2013b. Distributed representations of words and phrases and their compositionality. En las actas de NIPS, páginas 3111-3119.
* Billy Chiu, Gamal Crichton, Anna Korhonen y Sampo Pyysalo. 2016. [How to Train Good Word Embeddings for Biomedical NLP](http://aclweb.org/anthology/W/W16/W16-2922.pdf), en las actas del 15.º Taller de procesamiento del lenguaje natural biomédico, páginas 166-174.
* [Representaciones vectoriales de palabras](https://www.tensorflow.org/tutorials/word2vec)
* [Redes neuronales recurrentes](https://www.tensorflow.org/tutorials/recurrent)
* [Problemas habituales en Word2Vec del aprendizaje automático de Spark](https://intothedepthsofdataengineering.wordpress.com/2017/06/26/problems-encountered-with-spark-ml-word2vec/)
* [Word2Vec de Spark: lecciones aprendidas](https://intothedepthsofdataengineering.wordpress.com/2017/06/26/spark-word2vec-lessons-learned/)

