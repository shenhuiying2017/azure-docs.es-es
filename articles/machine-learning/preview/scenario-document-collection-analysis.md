---
title: "Análisis de una colección de documentos - Azure | Microsoft Docs"
description: "Cómo resumir y analizar una extensa colección de documentos, incluidas técnicas como el aprendizaje de frase, el modelado de tema y el análisis de modelo de tema, con Azure ML Workbench."
services: machine-learning
author: kehuan
ms.author: kehuan
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, MicrosoftDocs/mlreview, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/20/2017
ms.openlocfilehash: a6034652f27765bb20db4dbbb4c25741b261e50a
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/18/2017
---
# <a name="document-collection-analysis"></a>Análisis de una colección de documentos

Este escenario muestra cómo resumir y analizar una extensa colección de documentos, incluidas técnicas como el aprendizaje de frase, el modelado de tema y el análisis de modelo de tema con Azure ML Workbench. Azure Machine Learning Workbench proporciona un escalado vertical para una colección de documentos muy extensa y proporciona mecanismos para entrenar y ajustar modelos en una variedad de contextos de proceso, que abarcan desde el proceso local hasta instancias de Data Science Virtual Machines o un clúster de Spark. Se proporciona un desarrollo sencillo con Jupyter Notebook de Azure Machine Learning Workbench.

## <a name="link-to-the-gallery-github-repository"></a>Vínculo al repositorio de GitHub de la galería

El repositorio de GitHub público para este escenario real contiene todos los materiales, incluidos ejemplos de código, necesarios para este ejemplo:

[https://github.com/Azure/MachineLearningSamples-DocumentCollectionAnalysis](https://github.com/Azure/MachineLearningSamples-DocumentCollectionAnalysis)

## <a name="overview"></a>Información general

Con una gran cantidad de datos (datos de texto no estructurado especialmente) recopilada todos los días, supone un desafío importante organizar, buscar y comprender grandes cantidades de estos textos. Este escenario de análisis de una colección de documentos muestra un flujo de trabajo eficaz, automatizado y de un extremo a otro para analizar una colección de documentos de gran tamaño y habilitar tareas de procesamiento de lenguaje natural descedentes.

Los elementos clave que se entregan con este escenario son:

1. Aprendizaje de frases de múltiples palabras más destacadas de los documentos.

1. Detección de temas subyacentes que se presentan en la colección de documentos.

1. Representación de los documentos por distribución temática.

1. Presentación de métodos para organizar, buscar y resumir los documentos en función del contenido temático.

Los métodos que se presentan en este escenario pueden permitir una variedad de cargas de trabajo industriales esenciales, como la detección de anomalías de tendencias de tema, el resumen de la colección de documentos y la búsqueda de documentos similares. Se puede aplicar a muchos tipos diferentes de análisis de documentos, como legislación gubernamental, noticias, reseñas de productos, comentarios de clientes y artículos de investigación científica.

Las técnicas de aprendizaje automático y los algoritmos usados en este escenario incluyen:

1. Procesamiento y limpieza de texto

1. Aprendizaje de frases

1. Modelado de tema

1. Resumen de corpus

1. Detección de tendencias de tema y anomalías

## <a name="prerequisites"></a>requisitos previos

Los requisitos previos para ejecutar este ejemplo son los siguientes:

* Asegúrese de que ha instalado correctamente [Azure Machine Learning Workbench](./overview-what-is-azure-ml.md) siguiendo la [Guía de instalación de inicio rápido](quickstart-installation.md).

* Este ejemplo se podría ejecutar en cualquier contexto de proceso. Sin embargo, se recomienda que se ejecute en una máquina de varios núcleos con al menos 16 GB de memoria y 5 GB de espacio en disco.

## <a name="create-a-new-workbench-project"></a>Creación de un nuevo proyecto de Workbench

Cree un nuevo proyecto usando este ejemplo como plantilla:
1.  Abra Azure Machine Learning Workbench
2.  En la página **Proyectos**, haga clic en el signo **+** y seleccione **Nuevo proyecto**
3.  En el panel **Crear nuevo proyecto**, rellene la información del proyecto nuevo
4.  En el cuadro de búsqueda **Buscar plantillas de proyecto**, escriba "Document Collection Analysis" (Análisis de una colección de documentos) y seleccione la plantilla
5.  Haga clic en **Crear**

## <a name="data-description"></a>Descripción de los datos

El conjunto de datos usado en este escenario contiene los resúmenes de texto y los metadatos asociados para cada acción legislativa tomada por el Congreso de EE. UU. Los datos se recopilan de [GovTrack.us](https://www.govtrack.us/), que realiza un seguimiento de las actividades del Congreso de Estados Unidos y ayuda a los norteamericanos a participar en su proceso legislativo nacional. Se pueden descargar los datos de forma masiva a través de [este vínculo](https://www.govtrack.us/data/congress/) mediante un script manual, que no se incluye en este escenario. Los detalles sobre cómo descargar los datos se pueden encontrar en la [documentación de la API de GovTrack](https://www.govtrack.us/developers/api).

### <a name="data-source"></a>Origen de datos

En este escenario, los datos sin procesar recopilados son una serie de acciones legislativas introducidas por el Congreso de EE. UU. (proposiciones y resoluciones) desde 1973 hasta junio de 2017 (los Congresos 93 a 115). Los datos recopilados están en formato JSON y contienen un amplio conjunto de información sobre las acciones legislativas. Puede hacer referencia a [este vínculo GitHub](https://github.com/unitedstates/congress/wiki/bills) para una descripción detallada de los campos de datos. Para el propósito de demostración dentro de este escenario, solo se extrajo un subconjunto de los campos de datos de los archivos JSON. En este escenario, se proporciona el archivo TSV precompilado `CongressionalDataAll_Jun_2017.tsv` que contiene registros de dichas acciones legislativas. El archivo TSV puede descargarse automáticamente mediante el notebook `1_Preprocess_Text.ipynb` de la carpeta notebook o `preprocessText.py` del paquete de Python.

### <a name="data-structure"></a>Estructura de datos

Hay nueve campos de datos en el archivo de datos. Los nombres y descripciones de los campos de datos se enumeran a continuación.

| Nombre del campo | type | DESCRIPCIÓN | Faltan valores en el contenido |
|------------|------|-------------|---------------|
| `ID` | string | El identificador de la propuesta o resolución. El formato de este campo es [tipo_de_propuesta][número]-[congreso]. Por ejemplo, "hconres1-93" significa que el tipo de propuesta es "hconres" (abreviatura de House Concurrent Resolution, puede consultar [este documento](https://github.com/unitedstates/congress/wiki/bills#basic-information)), el número de propuesta es '1' y el número de congreso es ' 93'. | Sin  |
| `Text` | string | El contenido de la propuesta o resolución. | Sin  |
| `Date` | string | La fecha de la propuesta o resolución propuesta inicialmente. En un formato de "aaaa-mm-dd". | Sin  |
| `SponsorName` | string | El nombre del patrocinador principal que presenta la propuesta o resolución. | Sí |
| `Type` | string | El tipo de título del patrocinador principal, puede ser 'rep' (representante) o 'sen' (senador). | Sí |
| `State` | string | El Estado del patrocinador principal. | Sí |
| `District` | Entero | El número de distrito del patrocinador principal si el título del patrocinador es representante. | Sí |
| `Party` | string | El partido del patrocinador principal. | Sí |
| `Subjects` | string | Los términos del asunto agregados de manera acumulativa por la biblioteca del Congreso a la propuesta. Los términos se concatenan con comas. Estos términos han sido escritos por una persona de la biblioteca del Congreso y normalmente no están presentes cuando se publica la información de la propuesta por primera vez. Se pueden agregar en cualquier momento. Por lo tanto, al final del ciclo de vida de una propuesta, algunos asuntos pueden no resultar relevantes. | Sí |

## <a name="scenario-structure"></a>Estructura del escenario

El ejemplo de análisis de una colección de documentos se divide en dos tipos de entregas. El primer tipo es una serie de instancias de iPython Notebook que muestran las descripciones detalladas del flujo de trabajo completo. El segundo tipo es un paquete de Python, así como algunos ejemplos de código sobre cómo usar el paquete. El paquete de Python es lo suficientemente genérico para servir en muchos casos de uso.

Los archivos de este ejemplo se organizan del siguiente modo.

| Nombre de archivo | type | DESCRIPCIÓN |
|-----------|------|-------------|
| `aml_config` | Carpeta | Carpeta de configuración de Azure Machine Learning Workbench, consulte [esta documentación](./experimentation-service-configuration-reference.md) para la configuración de la ejecución del experimento detallada |
| `Code` | Carpeta | La carpeta de código usada para guardar el paquete de Python y los scripts de Python |
| `Data` | Carpeta | La carpeta de datos usada para guardar los archivos intermedios |
| `notebooks` | Carpeta | La carpeta de Jupyter Notebooks |
| `Code/documentAnalysis/__init__.py` | Archivo Python | El archivo de inicialización del paquete de Python |
| `Code/documentAnalysis/configs.py` | Archivo Python | El archivo de configuración usado por el paquete de Python de análisis de documentos, incluidas las constantes predefinidas |
| `Code/documentAnalysis/preprocessText.py` | Archivo Python | El archivo Python usado para preprocesar los datos para las tareas descendentes |
| `Code/documentAnalysis/phraseLearning.py` | Archivo Python | El archivo Python utilizado para aprender frases de los datos y transformar los datos sin procesar |
| `Code/documentAnalysis/topicModeling.py` | Archivo Python | El archivo Python utilizado para entrenar un modelo de temas Latent Dirichlet Allocation (LDA) |
| `Code/step1.py` | Archivo Python | Paso 1 del análisis de la colección de documentos: preprocesamiento del texto |
| `Code/step2.py` | Archivo Python | Paso 2 del análisis de la colección de documentos: aprendizaje de frases |
| `Code/step3.py` | Archivo Python | Paso 3 del análisis de la colección de documentos: entrenar y evaluar el modelo de temas LDA |
| `Code/runme.py` | Archivo Python | Ejemplo de ejecución de todos los pasos en un archivo |
| `notebooks/1_Preprocess_Text.ipynb` | iPython Notebook | Preprocesamiento de texto y transformación de los datos sin procesar |
| `notebooks/2_Phrase_Learning.ipynb` | iPython Notebook | Aprendizaje de frases de los datos de texto (después de la transformación de datos) |
| `notebooks/3_Topic_Model_Training.ipynb` | iPython Notebook | Entrenamiento del modelo de temas LDA |
| `notebooks/4_Topic_Model_Summarization.ipynb` | iPython Notebook | Resume el contenido de la colección de documentos en función de un modelo de temas LDA entrenado |
| `notebooks/5_Topic_Model_Analysis.ipynb` | iPython Notebook | Analiza el contenido temático de una colección de documentos de texto y correlaciona la información temática con otros metadatos asociados a la colección de documentos |
| `notebooks/6_Interactive_Visualization.ipynb` | iPython Notebook | Visualización interactiva del modelo del tema aprendido |
| `notebooks/winprocess.py` | Archivo Python | El script de Python para multiproceso utilizado por los Notebooks |
| `README.md` | Archivo de marcado | Archivo Léame de Markdown |

### <a name="data-ingestion-and-transformation"></a>Ingesta y transformación de datos

El conjunto de datos compilado `CongressionalDataAll_Jun_2017.tsv` se guarda en Blob Storage y es accesible desde los Notebooks y los scripts de Python. Hay dos pasos para la ingesta y transformación de datos: el preprocesamiento de los datos de texto y el aprendizaje de frases.

#### <a name="preprocess-text-data"></a>Preprocesamiento de los datos de texto

El paso de preprocesamiento aplica técnicas de procesamiento de lenguaje natural para limpiar y preparar los datos de texto sin formato. Sirve como precursor para el aprendizaje de frases no supervisado y el modelado de tema latente. La descripción detallada paso a paso puede encontrarse en el Notebook `1_Preprocess_Text.ipynb`. También hay un script de Python `step1.py` que corresponde a este Notebook.

En este paso, el archivo de datos TSV se descarga de Azure Blob Storage y se importa como una trama de datos de Pandas. Cada elemento de fila en la trama de datos es una única cadena larga coherente de texto o un 'documento'. Cada documento, a continuación, se divide en fragmentos de texto que suelen ser frases o subfrases. La división está diseñada para evitar que el proceso de aprendizaje de frases utilice cadenas de palabras de diferentes frases al aprender frases.

Hay varias funciones definidas para el paso de preprocesamiento en el Notebook y el paquete de Python. La mayoría del trabajo se puede realizar mediante una llamada a estas dos líneas de código.

```python
# Read raw data into a Pandas DataFrame
textDF = getData()

# Write data frame with preprocessed text out to TSV file
cleanedDataFrame = CleanAndSplitText(textDF, saveDF=True)
```

#### <a name="phrase-learning"></a>Aprendizaje de frases

La fase de aprendizaje de frases implementa una plataforma básica para aprender frases clave entre una extensa colección de documentos. Se describe en el documento titulado "[Modeling Multiword Phrases with Constrained Phrases Tree for Improved Topic Modeling of Conversational Speech](http://people.csail.mit.edu/hazen/publications/Hazen-SLT-2012.pdf)", que se presentó originalmente en el taller de IEEE 2012 sobre Tecnología en el lenguaje hablado. La implementación detallada del paso de aprendizaje de frases se muestra en el iPython Notebook `2_Phrase_Learning.ipynb` y el script de Python `step2.py`.

Este paso toma el texto limpio como entrada y aprende las frases más importantes que se presentan en una colección extensa de documentos. El aprendizaje de frases es un proceso iterativo que se puede dividir en tres tareas: contar los n-gramas, clasificar las posibles frases en función de la información mutua de puntos ponderada de sus palabras constituyentes y volver a escribir la frase en texto. Las tres tareas se ejecutan secuencialmente en varias iteraciones hasta que se han aprendido las frases especificadas.

En el paquete de Python de análisis de documentos, la clase de Python `PhraseLearner` se define en el archivo `phraseLearning.py`. A continuación se muestra el fragmento de código que se utiliza para aprender frases.

```python
# Instantiate a PhraseLearner and run a configuration
phraseLearner = PhraseLearner(cleanedDataFrame, "CleanedText", numPhrase,
                        maxPhrasePerIter, maxPhraseLength, minInstanceCount)

# The chunks of text in a list
textData = list(phraseLearner.textFrame['LowercaseText'])

# Learn most salient phrases present in a large collection of documents
phraseLearner.RunConfiguration(textData,
            phraseLearner.learnedPhrases,
            addSpace=True,
            writeFile=True,
            num_workers=cpu_count()-1)
```

> [!NOTE]
> La fase de aprendizaje de frases se implementa con multiproceso. Sin embargo, tener más núcleos de CPU **NO** significa un tiempo de ejecución más rápido. En nuestras pruebas, el rendimiento no ha mejorado con más de ocho núcleos debido a la sobrecarga del multiproceso. Se tardó aproximadamente 2,5 horas en aprender 25.000 frases en una máquina con ocho núcleos (3,6 GHz).
>

### <a name="topic-modeling"></a>Modelado de tema

Aprender un modelo de temas latente con LDA es el tercer paso en este escenario. El paquete de Python [gensim](https://radimrehurek.com/gensim/) es necesario en este paso para aprender un [modelo de temas LDA](https://en.wikipedia.org/wiki/Latent_Dirichlet_allocation). El Notebook correspondiente para este paso es `3_Topic_Model_Training.ipynb`. También puede hacer referencia a `step3.py` para conocer el uso del paquete de análisis de documentos.

En este paso, la tarea principal consiste en aprender un modelo de temas LDA y ajustar los hiperparámetros. Hay varios parámetros que se deben ajustar al entrenar un modelo LDA, pero el parámetro más importante es el número de temas. Con pocos temas podría no aportar detalle sobre la colección de documentos; mientras que elegir demasiados temas dará como resultado el exceso de división de un corpus en muchos temas pequeños y muy similares. El propósito de este escenario es mostrar cómo ajustar el número de temas. Azure Machine Learning Workbench proporciona libertad para ejecutar los experimentos con diferentes parámetros de configuración en contextos de proceso diferentes.

En el paquete de Python de análisis de documentos, se han definido algunas funciones para ayudar a los usuarios a determinar el mejor número de temas. El primer enfoque es mediante la evaluación de la coherencia del modelo de temas. Hay cuatro matrices de evaluación compatibles: `u_mass`, `c_v`, `c_uci` y `c_npmi`. Los detalles de esas cuatro métricas se describen en [este documento](http://svn.aksw.org/papers/2015/WSDM_Topic_Evaluation/public.pdf). El segundo enfoque consiste en evaluar la perplejidad en un corpus apartado.

Para la evaluación de la perplejidad, se espera una curva con forma de 'U' para averiguar el mejor número de temas. Y la posición del codo es la mejor opción. Se recomienda evaluar varias veces con diferentes valores de inicialización aleatorios y obtener el promedio. La evaluación de la coherencia se espera que tenga forma de 'n', lo que significa que la coherencia aumenta con el aumento del número de temas para luego disminuir. A continuación se muestra un gráfico de ejemplo de perplejidad y coherencia `c_v`.

![Perplejidad](./media/scenario-document-collection-analysis/Perplexity_Value.png)

![Coherencia c_v](./media/scenario-document-collection-analysis/c_v_Coherence.png)

En este escenario, la perplejidad aumenta considerablemente después de 200 temas, mientras que se reduce significativamente el valor de la coherencia después de 200 temas también. Basándose en los gráficos y en el deseo de temas más generales frente a temas más compartimentados, elegir 200 temas debería ser una buena opción.

Puede entrenar un modelo de temas LDA en una ejecución del experimento o entrenar y evaluar varios modelos LDA con diferentes configuraciones de número de temas en una única ejecución del experimento. Se recomienda ejecutar varias veces cada configuración y, a continuación, obtener el promedio de las evaluaciones de coherencia y perplejidad. Los detalles de cómo usar el paquete de análisis de documentos se pueden encontrar en el archivo `step3.py`. El siguiente fragmento de código es un ejemplo.

```python
topicmodeler = TopicModeler(docs,
        stopWordFile=FUNCTION_WORDS_FILE,
        minWordCount=MIN_WORD_COUNT,
        minDocCount=MIN_DOC_COUNT,
        maxDocFreq=MAX_DOC_FREQ,
        workers=cpu_count()-1,
        numTopics=NUM_TOPICS,
        numIterations=NUM_ITERATIONS,
        passes=NUM_PASSES,
        chunksize=CHUNK_SIZE,
        random_state=RANDOM_STATE,
        test_ratio=test_ratio)

# Train an LDA topic model
lda = topicmodeler.TrainLDA(saveModel=saveModel)

# Evaluate coherence metrics
coherence = topicmodeler.EvaluateCoherence(lda, coherence_types)

# Evaluate perplexity on a held-out corpus
perplex = topicmodeler.EvaluatePerplexity(lda)
```

> [!NOTE]
> El tiempo de ejecución para entrenar un modelo de temas LDA depende de varios factores, como el tamaño de corpus, la configuración de hiperparámetros y el número de núcleos en el equipo. Mediante el uso de varios núcleos de CPU se entrena un modelo más rápido. Sin embargo, con la misma configuración de hiperparámetros, más núcleos significa menos actualizaciones durante el entrenamiento. Es recomendable que haya **al menos 100 actualizaciones para entrenar un modelo LDA convergente**. La relación entre el número de actualizaciones y los hiperparámetros se describe en [esta entrada](https://groups.google.com/forum/#!topic/gensim/ojySenxQHi4) y [esta entrada](http://miningthedetails.com/blog/python/lda/GensimLDA/). En nuestras pruebas, se tardó aproximadamente 3 horas para entrenar un modelo LDA con 200 temas con la configuración de `workers=15`, `passes=10`, `chunksize=1000` en un equipo con 16 núcleos (2,0 GHz).
>

### <a name="topic-summarization-and-analysis"></a>Resumen y análisis de temas

El resumen y análisis de temas consta de dos Notebooks y no hay ninguna función correspondiente en el paquete de análisis de documentos.

En `4_Topic_Model_Summarization.ipynb`, se muestra cómo resumir el contenido del documento en función de un modelo de temas LDA entrenado. El resumen se aplica al modelo de temas LDA que se aprendió en el paso 3. Muestra cómo medir la importancia o la calidad de un tema mediante el uso de temas para medir la pureza del documento. Esta medida de pureza supone que los temas latentes que dominan los documentos en los que aparecen son semánticamente más importantes que los temas latentes débilmente repartidos en muchos documentos. Este concepto se presentó en el documento "[Latent Topic Modeling for Audio Corpus Summarization](http://people.csail.mit.edu/hazen/publications/Hazen-Interspeech11.pdf)" (Modelado de temas latentes para resumen de corpus de audio).

El Notebook `5_Topic_Model_Analysis.ipynb` muestra cómo analizar el contenido temático de una colección de documentos y correlacionar la información temática con otros metadatos asociados a la colección de documentos. Se presentan algunos gráficos en este Notebook para ayudar a los usuarios a comprender mejor el tema aprendido y la colección de documentos.

Notebook `6_Interactive_Visualization.ipynb` muestra cómo visualizar de forma interactiva modelo del tema aprendido. Incluye cuatro tareas de visualización interactiva.

## <a name="conclusion"></a>Conclusión

Este escenario real destaca cómo utilizar técnicas de análisis de texto conocidas (en este caso, el aprendizaje de frases y el modelado de temas LDA) para generar un modelo robusto y cómo puede ayudar Azure Machine Learning Workbench a realizar un seguimiento del rendimiento de los modelos y ejecutar sin problemas procesos de aprendizaje a una escala mayor. Con más detalle:

* Usar aprendizaje de frases y modelado de temas para procesar una colección de documentos y generar un modelo robusto. Si la colección de documentos es muy grande, Azure Machine Learning Workbench puede escalar fácilmente tanto vertical como horizontalmente. Además, los usuarios tienen la libertad de ejecutar experimentos en contextos de proceso diferentes fácilmente desde Azure Machine Learning Workbench.

* Azure Machine Learning Workbench proporciona ambas opciones para ejecutar Notebooks paso a paso y un script de Python para ejecutar un experimento completo.

* Ajustar los hiperparámetros con Azure Machine Learning Workbench para encontrar el mejor número de temas que se necesita aprender. Azure Machine Learning Workbench puede ayudar a realizar un seguimiento del rendimiento de los modelos y ejecutar sin problemas procesos de aprendizaje diferentes a una escala mayor.

* Azure Machine Learning Workbench puede administrar el historial de ejecución y los modelos aprendidos. Permite a los científicos de datos identificar rápidamente los modelos de mejor rendimiento y buscar los scripts y los datos utilizados para generarlos.

## <a name="references"></a>Referencias

* **Timothy J. Hazen, Fred Richardson**, [ _Modeling Multiword Phrases with Constrained Phrases Tree for Improved Topic Modeling of Conversational Speech_](http://people.csail.mit.edu/hazen/publications/Hazen-SLT-2012.pdf) (Modelado de frases de varias palabras con árbol de frases restringido para mejorar el modelado de temas de conversación). Taller de tecnología del lenguaje hablado (SLT), IEEE de 2012. IEEE, 2012.

* **Timothy J. Hazen**, [ _Latent Topic Modeling for Audio Corpus Summarization_](http://people.csail.mit.edu/hazen/publications/Hazen-Interspeech11.pdf) (Modelado de temas latentes para resumen de corpus de audio). 12ª Conferencia anual de la Asociación de comunicación de voz internacional. 2011.

* **Michael Roder, Andreas ambos, Alexander Hinneburg**, [ _Exploring the Space of Topic Coherence Measures_](http://svn.aksw.org/papers/2015/WSDM_Topic_Evaluation/public.pdf) (Explorar el espacio de las medidas de coherencia de temas). Procedente de la octava conferencia internacional de ACM sobre búsqueda en la Web y minería de datos. ACM, 2015.
