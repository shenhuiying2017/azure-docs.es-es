---
title: Correspondencia entre preguntas y respuestas mediante Azure Machine Learning Workbench | Microsoft Docs
description: "Uso de varios métodos eficaces de aprendizaje automático para hacer corresponder consultas abiertas con pares de preguntas/respuestas de P+F ya existentes."
services: machine-learning
documentationcenter: 
author: mezmicrosoft
editor: mezmicrosoft
ms.assetid: 
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/15/2017
ms.author: mez
manager: tihazen
ms.openlocfilehash: 33f807a4a0bbc4afd1f2fbe017f8913eccacc34b
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/18/2017
---
#  <a name="q--a-matching-using-azure-machine-learning-workbench"></a>Correspondencia entre preguntas y respuestas mediante Azure Machine Learning Workbench
Responder a preguntas abiertas es difícil y a menudo requiere un esfuerzo manual por parte de expertos en la materia (SME). Para ayudar a reducir la demanda de expertos en la materia internos, las empresas a menudo crean listas con las preguntas más frecuentes (P+F) como forma de ayudar a los usuarios. Este ejemplo muestra varios métodos eficaces de aprendizaje automático para hacer corresponder consultas abiertas con pares de preguntas/respuestas de P+F ya existentes. Este ejemplo muestra un sencillo proceso de desarrollo para compilar una solución de este tipo mediante Azure Machine Learning Workbench. 

## <a name="link-to-the-gallery-github-repository"></a>Vínculo al repositorio de GitHub de la galería
[https://github.com/Azure/MachineLearningSamples-QnAMatching](https://github.com/Azure/MachineLearningSamples-QnAMatching)

## <a name="overview"></a>Información general

En este ejemplo se aborda el problema de la asignación de preguntas de usuarios a pares de preguntas y respuestas ya existentes tal y como aparecen normalmente en una lista de preguntas más frecuentes (es decir, en una sección de P+F) o en los pares de preguntas y respuestas presentes en sitios web como el de [Stack Overflow](https://stackoverflow.com/). Existen muchos enfoques para hacer corresponder una pregunta con su respuesta correcta como, por ejemplo, encontrar la respuesta que es más parecida a la pregunta. Sin embargo, en este ejemplo se hace corresponder preguntas abiertas con preguntas anteriormente formuladas suponiendo que cada respuesta de la sección de P+F puede responder a varias preguntas semánticamente equivalentes.

Los principales pasos necesarios para ofrecer esta solución son los siguientes:

1. Limpiar y procesar los datos de texto.
2. Aprender frases informativas, que son secuencias de varias palabras que proporcionan más información cuando se ven como una secuencia que cuando se tratan de forma individual.
3. Extraer características de los datos de texto.
4. Entrenar modelos de clasificación de texto y evaluar el rendimiento del modelo.


## <a name="prerequisites"></a>requisitos previos

Los requisitos previos para ejecutar este ejemplo son los siguientes:

1. Una [cuenta de Azure](https://azure.microsoft.com/free/) (hay disponibles versiones gratuitas de prueba).
2. Una copia instalada de [Azure Machine Learning Workbench](./overview-what-is-azure-ml.md) siguiendo la [Guía de instalación de inicio rápido](./quickstart-installation.md) para instalar el programa y crear un área de trabajo.
3. En este ejemplo se podría ejecutar en cualquier contexto de proceso. Sin embargo, se recomienda que se ejecute en una máquina de varios núcleos con al menos 16 GB de memoria y 5 GB de espacio en disco.

## <a name="create-a-new-workbench-project"></a>Creación de un nuevo proyecto de Workbench

Cree un nuevo proyecto usando este ejemplo como plantilla:
1.  Abra Azure Machine Learning Workbench
2.  En la página **Proyectos**, haga clic en el signo **+** y seleccione **Nuevo proyecto**
3.  En el panel **Crear nuevo proyecto**, rellene la información del proyecto nuevo
4.  En el cuadro de búsqueda **Buscar plantillas de proyecto**, escriba "Q & A Matching" (Correspondencia entre preguntas y respuestas) y seleccione la plantilla
5.  Haga clic en **Crear**

## <a name="data-description"></a>Descripción de los datos

El conjunto de datos utilizado en este ejemplo es un volcado de datos de Stack Exchange almacenado en [archive.org](https://archive.org/details/stackexchange). Se trata de un volcado de memoria anonimizado de todo el contenido proporcionado por los usuarios en la [red Stack Exchange](https://stackexchange.com/). Cada sitio de la red tiene el formato de un archivo independiente compuesto por archivos XML comprimidos mediante 7-zip con compresión bzip2. Cada archivo del sitio incluye publicaciones, usuarios, votos, comentarios, PostHistory y PostLinks. 

### <a name="data-source"></a>Origen de datos

En este ejemplo se utilizan los [datos de Posts (10 GB)](https://archive.org/download/stackexchange/stackoverflow.com-Posts.7z) y los [datos de PostLinks (515 MB)](https://archive.org/download/stackexchange/stackoverflow.com-PostLinks.7z) del sitio de Stack Overflow. Para obtener una información de esquema completa, consulte el archivo [readme.txt](https://ia800500.us.archive.org/22/items/stackexchange/readme.txt). 

El campo `PostTypeId` de los datos de Posts indica si una publicación es una `Question` o una `Answer`. El campo `PostLinkTypeId` de los datos de PostsLinks indica si dos publicaciones están vinculadas o duplicadas. Las entradas de preguntas incluyen normalmente algunas etiquetas que son palabras clave que permiten clasificar una pregunta con otras preguntas similares o duplicadas. Hay algunas etiquetas que son más frecuentes, como `javascript`, `java`, `c#`, `php` etc., que constan de un mayor número de entradas de preguntas. En este ejemplo, se extrae un subconjunto de pares de preguntas y respuestas con la etiqueta `javascript`.

Additionionally, una entrada de pregunta puede estar relacionada con varias entradas de respuesta o entradas de pregunta duplicadas. Para generar una lista de preguntas más frecuentes a partir de estos dos conjuntos de datos se han tenido en cuenta algunos criterios de recopilación de datos. Los tres conjuntos de datos compilados se seleccionan mediante un script de SQL que no se incluye en este ejemplo. La descripción de los datos resultantes es la siguiente:

- `Original Questions (Q)`: las entradas de preguntas se formulan y responden en el sitio de Stack Overflow.
- `Duplications (D)`: las entradas de preguntas duplican otras preguntas ya existentes (`PostLinkTypeId = 3`) que son las preguntas originales. Los duplicados se consideran como semánticamente equivalentes a las preguntas originales en el sentido de que la respuesta proporcionada a la pregunta original también responde a la nueva pregunta duplicada.
- `Answers (A)`: cada pregunta original y sus duplicados se pueden vincular a más de una entrada de respuesta. En este ejemplo solo se selecciona la entrada de respuesta que ya está aceptada por el autor original (filtrado por `AcceptedAnswerId`) o que tiene la máxima puntuación (clasificada según `Score`) de entre las preguntas originales. 

La combinación de estos tres conjuntos de datos crea pares de preguntas y respuestas en los que una respuesta (R) se ha asignado a una pregunta original (P) y a varias preguntas duplicadas (D) como se muestra en el siguiente diagrama de datos:

<table><tr><td><img id ="data_diagram" src="media/scenario-qna-matching/data_diagram.png"></td></tr></table>

### <a name="data-structure"></a>Estructura de datos

El esquema de datos y los vínculos de descarga directa de los tres conjuntos de datos se pueden encontrar en la tabla siguiente:

| Dataset | Campo | type | DESCRIPCIÓN
| ----------|------------|------------|--------
| [questions](https://bostondata.blob.core.windows.net/stackoverflow/orig-q.tsv.gz) | Id | string | El identificador único de pregunta (clave principal)
|  | AnswerId | string | El identificador único de respuesta por pregunta
|  | Text0 | string | Los datos de texto sin procesar, incluido el título y el cuerpo de la pregunta
|  | CreationDate | Timestamp | La marca de tiempo de cuando se formuló la pregunta
| [dupes](https://bostondata.blob.core.windows.net/stackoverflow/dup-q.tsv.gz) | Id | string | El identificador único de la duplicación (clave principal)
|  | AnswerId | string | El identificador de respuesta asociado a la duplicación
|  | Text0 | string | Los datos de texto sin procesar, incluido el título y el cuerpo de la duplicación
|  | CreationDate | Timestamp | La marca de tiempo de cuando se formuló la duplicación
| [answers](https://bostondata.blob.core.windows.net/stackoverflow/ans.tsv.gz)  | Id | string | El identificador único de la respuesta (clave principal)
|  | text0 | string | Los datos de texto sin procesar de la respuesta


## <a name="scenario-structure"></a>Estructura del escenario

El ejemplo de correspondencia entre preguntas y respuestas se presenta a través de tres tipos de archivos. El primer tipo es una serie de instancias de Jupyter Notebook que muestran las descripciones detalladas del flujo de trabajo completo. El segundo tipo es un conjunto de archivos de Python que contienen módulos personalizados de Python para el aprendizaje de frases y la extracción de características. Estos módulos de Python son lo suficientemente genéricos como para que no solo sirvan para este ejemplo, sino también para otros casos de uso. El tercer tipo es un conjunto de archivos de Python para ajustar los hiperparámetros y realizar un seguimiento del rendimiento del modelo mediante Azure Machine Learning Workbench.

Los archivos de este ejemplo se organizan del siguiente modo.

| Nombre de archivo | type | DESCRIPCIÓN
| ----------|------------|--------
| `Image` | Carpeta | La carpeta se utiliza para guardar las imágenes para el archivo Léame
| `notebooks` | Carpeta | La carpeta de Jupyter Notebooks
| `modules` | Carpeta | La carpeta de módulos de Python
| `scripts` | Carpeta | La carpeta de archivos de Python
| `notebooks/Part_1_Data_Preparation.ipynb` | Jupyter Notebook | Permite acceder a los datos de ejemplo, preprocesar el texto y preparar el entrenamiento y los conjuntos de datos de prueba
| `notebooks/Part_2_Phrase_Learning.ipynb` | Jupyter Notebook | Permite aprender frases informativas y acortar texto en representaciones de bolsas de palabras (BOW)
| `notebooks/Part_3_Model_Training_and_Evaluation.ipynb` | Jupyter Notebook | Extrae características, entrena modelos de clasificación de texto y evalúa el rendimiento del modelo
| `modules/__init__.py` | Archivo Python | El archivo de inicialización del paquete de Python
| `modules/phrase_learning.py` | Archivo Python | Los módulos de Python se usan para transformar los datos sin procesar y aprender frases informativas
| `modules/feature_extractor.py` | Archivo Python | Los módulos de Python extraen características para el entrenamiento del modelo
| `scripts/naive_bayes.py` | Archivo Python | El archivo de Python para ajustar hiperparámetros en el modelo de Naive Bayes
| `scripts/random_forest.py` | Archivo Python | El archivo de Python para ajustar los hiperparámetros en el modelo de Naive Bayes
| `README.md` | Archivo Markdown | Archivo Léame de Markdown

> [!NOTE]
> La serie de cuadernos se compila en Python 3.5.
> 

### <a name="data-ingestion-and-transformation"></a>Ingesta y transformación de datos

Los tres conjuntos de datos compilados se almacenan en una instancia de Blob Storage y se recuperan en el cuaderno `Part_1_Data_Preparation.ipynb`.  

Antes de entrenar los modelos de clasificación de texto, el texto de las preguntas se limpia y se preprocesa para excluir los fragmentos de código. Se aplica un aprendizaje de frases sin supervisar sobre el material de entrenamiento para aprender secuencias informativas de varias palabras. Estas frases se representan como unidades de palabras compuestas individuales en las características de bolsa de palabras descendente que usan los modelos de clasificación de texto.

Las descripciones detalladas paso a paso del preprocesamiento de texto y el aprendizaje de frases se pueden encontrar en los cuadernos `Part_1_Data_Preparation.ipynb` y `Part_2_Phrase_Learning.ipynb`, respectivamente.

### <a name="modeling"></a>Modelado

Este ejemplo está diseñado para puntuar nuevas preguntas en comparación con pares de preguntas y respuestas ya existentes mediante el entrenamiento de modelos de clasificación de texto en los que cada par de pregunta y respuesta es una clase única de un subconjunto de las preguntas duplicadas de cada par de pregunta y respuesta disponible como material de entrenamiento. En el ejemplo, se conservan las preguntas originales y el 75% de las preguntas duplicadas para el entrenamiento y el 25% de las preguntas duplicadas publicadas más recientemente se aparta como datos de evaluación.

El modelo de clasificación usa un método de conjuntos para agregar tres clasificadores base que incluyen **Naive Bayes**, **Máquina de vectores de soporte** y **Bosque aleatorio**. En cada clasificador base, el `AnswerId` se usa como la etiqueta de clase y las representaciones de las bolsas de palabras se utilizan como las características.

El proceso de entrenamiento del modelo se muestra en `Part_3_Model_Training_and_Evaluation.ipynb`.

### <a name="evaluation"></a>Evaluación

Se utilizan dos métricas de evaluación diferentes para evaluar el rendimiento. 
1. `Average Rank (AR)`: indica la posición media donde se encuentra la respuesta correcta en la lista de pares de preguntas y respuestas recuperados (fuera del conjunto completo de 103 clases de respuesta). 
2. `Top 3 Percentage`: indica el porcentaje de preguntas de prueba cuya respuesta correcta se puede recuperar de entre las tres opciones principales de la lista clasificada que se devuelve. 

La evaluación se muestra en `Part_3_Model_Training_and_Evaluation.ipynb`.


## <a name="conclusion"></a>Conclusión

En este ejemplo se explica cómo utilizar técnicas conocidas de análisis de texto, como el aprendizaje de frases y la clasificación de texto para generar un modelo sólido. También explica cómo puede ayudar Azure Machine Learning Workbench con el desarrollo de soluciones interactivas y el seguimiento del rendimiento de los modelos. 

Algunos aspectos destacados de este ejemplo son:

- El problema de la correspondencia entre preguntas y respuestas se puede solucionar eficazmente mediante los modelos de aprendizaje de frases y clasificación de texto.
- La demostración del desarrollo de modelos interactivos con Azure Machine Learning Workbench y Jupyter Notebook.
- Azure Machine Learning Workbench administra el historial de ejecución y los modelos aprendidos con el registro de las métricas de evaluación con fines de comparación. Estas características permiten el ajuste rápido de los hiperparámetros y ayuda a identificar los modelos con el mejor rendimiento.


## <a name="references"></a>Referencias

Timothy J. Hazen, Fred Richardson, [_Modeling Multiword Phrases with Constrained Phrases Tree for Improved Topic Modeling of Conversational Speech_](http://people.csail.mit.edu/hazen/publications/Hazen-SLT-2012.pdf) (Modelado de frases de varias palabras con árbol de frases restringido para mejorar el modelado de temas de conversación). Taller de tecnología del lenguaje hablado (SLT), IEEE de 2012. IEEE, 2012.

Timothy J. Hazen, [_MCE Training Techniques for Topic Identification of Spoken Audio Documents_](http://ieeexplore.ieee.org/abstract/document/5742980/) (Técnicas de entrenamiento de MCE para la identificación de temas en documentos de audio hablados) en la biblioteca de transacciones de IEEE sobre audio, voz y procesamiento del lenguaje), vol. 19, núm. 8, pp 2451-2460, noviembre de 2011.
