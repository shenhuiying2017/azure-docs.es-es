---
title: "Clasificación de imágenes con CNTK en Azure Machine Learning Workbench | Microsoft Docs"
description: "Use Azure ML Workbench para probar, evaluar e implementar un modelo personalizado de clasificación de imágenes."
services: machine-learning
documentationcenter: 
author: PatrickBue
ms.author: pabuehle
manager: mwinkle
ms.reviewer: mawah, marhamil, mldocs, garyericson, jasonwhowell
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 10/17/2017
ms.openlocfilehash: 53d182d84c8f28c7b4055780a5b41df00fdc8583
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/18/2017
---
# <a name="image-classification-using-azure-machine-learning-workbench"></a>Clasificación de imágenes con Azure Machine Learning Workbench

Se pueden usar distintos métodos de clasificación de imágenes para resolver un gran número de problemas de Computer Vision.
Esto incluye la creación de modelos que respondan preguntas del tipo *¿Hay un OBJETO en la imagen?*, donde OBJETO puede ser, por ejemplo, un *perro*, un *coche* o un *barco*. O a preguntas más complejas, como *¿Qué gravedad de enfermedad ocular revela el escáner de retina de este paciente?*

En este tutorial se abordan estos problemas. En él explicamos cómo entrenar, evaluar e implementar su propio modelo de clasificación de imágenes usando [Microsoft Cognitive Toolkit (CNTK)](https://docs.microsoft.com/cognitive-toolkit/) para el aprendizaje profundo.
Se incluyen imágenes de ejemplo, si bien el lector puede aportar su propio conjunto de datos y entrenar modelos personalizados de su cosecha.

Las soluciones de Computer Vision solían requerir un conocimiento de experto para identificar e implementar manualmente las llamadas *características*, que resaltan la información deseada en imágenes.
Este método manual cambió en 2012 con el famoso artículo de [AlexNet](https://papers.nips.cc/paper/4824-imagenet-classification-with-deep-convolutional-neural-networks.pdf) [1] sobre aprendizaje profundo y, en la actualidad, se usan redes neuronales profundas (DNN) para encontrar estas características automáticamente.
Las DNN supusieron una notable mejora en el campo no ya de la clasificación de imágenes, sino también para acabar con diversos problemas de Computer Vision como la detección de objetos y la similitud de imágenes.


## <a name="link-to-the-gallery-github-repository"></a>Vínculo al repositorio de GitHub de la galería
[https://github.com/Azure/MachineLearningSamples-ImageClassificationUsingCNTK](https://github.com/Azure/MachineLearningSamples-ImageClassificationUsingCNTK)

## <a name="overview"></a>Información general

Este tutorial se divide en tres partes:

- En la parte 1 se muestra cómo entrenar, evaluar e implementar un sistema de clasificación de imágenes, usando para ello una DNN ya entrenada para la captura de características y probando un SVM en la salida.
- En la parte 2 siguiente se indica cómo mejorar la precisión, por ejemplo, perfeccionando la DNN en lugar de usarla para la captura de características.
- En la parte 3 se describe cómo puede usar un conjunto de datos de su cosecha en lugar de las imágenes de ejemplo suministradas y, si es necesario, cómo generar su propio conjunto de datos extrayendo imágenes de Internet.

No se requiere experiencia previa de aprendizaje automático y CNTK, pero sí resulta útil para comprender los principios subyacentes. La precisión de los números, la duración del entrenamiento y otras cuestiones reflejadas en el tutorial se incluyen exclusivamente a modo de referencia; de igual modo, los valores reales que se obtengan al ejecutar el código variarán casi con toda seguridad.


## <a name="prerequisites"></a>requisitos previos

Los requisitos previos para ejecutar este ejemplo son los siguientes:

1. Una [cuenta de Azure](https://azure.microsoft.com/free/) (hay disponibles versiones gratuitas de prueba).
2. Una instalación de [Azure Machine Learning Workbench](./overview-what-is-azure-ml.md) siguiendo la [Guía de instalación de inicio rápido](./quickstart-installation.md) para instalar el programa y crear un área de trabajo.  
3. Un equipo Windows. Se necesita un sistema operativo Windows porque Workbench admite únicamente Windows y Mac OS, mientras que Microsoft Cognitive Toolkit (que usaremos como biblioteca de aprendizaje profundo) solo es compatible con Windows y Linux.
4. No se necesita una GPU dedicada para ejecutar el entrenamiento de SVM en la parte 1, pero sí para el proceso de perfeccionamiento de la DNN descrito en la parte 2. Si no tiene una GPU en condiciones, quiere entrenar en varias GPU o no tiene un equipo con Windows, considere la posibilidad de usar máquina de virtual de aprendizaje profundo de Azure con un sistema operativo Windows. Vaya [aquí](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.dsvm-deep-learning) para obtener una guía de implementación con un solo clic. Una vez implementada, conéctese a la máquina virtual a través de una conexión de Escritorio remoto, instale Workbench y ejecute el código localmente en ella.
5. Es necesario instalar varias bibliotecas de Python, como OpenCV. Haga clic en *Abrir símbolo del sistema* en el menú *Archivo* de Workbench y ejecute los siguientes comandos para instalar estas dependencias:  
    - `pip install https://cntk.ai/PythonWheel/GPU/cntk-2.2-cp35-cp35m-win_amd64.whl`  
    - `pip install opencv_python-3.3.1-cp35-cp35m-win_amd64.whl` después de descargar OpenCV de http://www.lfd.uci.edu/~gohlke/pythonlibs/ (la versión y el nombre de archivo exactos pueden variar)
    - `conda install pillow`
    - `pip install -U numpy`
    - `pip install bqplot`
    - `jupyter nbextension enable --py --sys-prefix bqplot`

### <a name="troubleshooting--known-bugs"></a>Solución de problemas/Errores conocidos
- Se necesita una GPU para la parte 2. Si no hay una, cuando trate de perfeccionar la DNN aparecerá un error que indica que el aprendizaje de normalización por lotes todavía no se ha implementado.
- Los errores de memoria insuficiente durante el entrenamiento de DNN se pueden soslayar si se reduce el tamaño del minilote (variable `cntk_mb_size` en `PARAMETERS.py`).
- El código se probó con CNTK 2.2 y debería poder ejecutarse tanto en versiones anteriores (hasta 2.0) como en versiones más recientes sin cambios (o solo con cambios mínimos).
- En el momento de redactar este artículo, Azure Machine Learning Workbench generaba problemas a la hora de mostrar blocs de notas superiores a 5 MB. Puede haber blocs de notas con este tamaño si se guardan mostrando todas las salidas de celda. Si este error se produce, abra un símbolo del sistema en el menú Archivo de Workbench, ejecute `jupyter notebook`, abra el bloc de notas, borre todas las salidas y guárdelo. Una vez hecho esto, el bloc de notas volverá a abrirse correctamente en Azure Machine Learning Workbench.
- Todos los scripts que se proporcionan en este ejemplo tienen que ejecutarse de forma local y no en entornos como, por ejemplo, un entorno remoto de docker. Todos los blocs de notas tienen que ejecutarse con el kernel establecido como un kernel del proyecto local denominado "PROJECTNAME local" (por ejemplo, "myImgClassUsingCNTK local").

    
## <a name="create-a-new-workbench-project"></a>Creación de un nuevo proyecto de Workbench

Para crear un proyecto usando este ejemplo como plantilla:
1.  Abra Azure Machine Learning Workbench.
2.  En la página **Proyectos**, haga clic en el signo **+** y seleccione **Nuevo proyecto**.
3.  En el panel **Crear nuevo proyecto**, rellene la información del proyecto nuevo.
4.  En el cuadro de búsqueda **Buscar plantillas de proyecto**, escriba "Image Classification" (Clasificación de imágenes) y seleccione la plantilla.
5.  Haga clic en **Create**(Crear).

Cuando realice estos pasos, se creará la estructura del proyecto descrita abajo. El directorio del proyecto tiene una limitación de tamaño máximo de 25 MB, ya que Azure Machine Learning Workbench crea una copia de esta carpeta después de cada ejecución (para habilitar el historial de ejecución). Por lo tanto, todos los archivos temporales y de imagen se guardan en el directorio *~/Desktop/imgClassificationUsingCntk_data* (en este documento lo llamaremos *DATA_DIR*).

  Carpeta| DESCRIPCIÓN
  ---|---
  aml_config/|                           Directorio que contiene los archivos de configuración de Azure Machine Learning Workbench
  libraries/|                              Directorio que contiene todas las funciones auxiliares de Python y Jupyter
  notebooks/|                              Directorio que contiene todos los blocs de notas
  resources/|                              Directorio que contiene todos los recursos (por ejemplo, direcciones URL de imágenes de moda)
  scripts/|                              Directorio que contiene todos los scripts
  PARAMETERS.py|                       Script de Python donde se especifican todos los parámetros
  readme.md|                           El presente documento Léame


## <a name="data-description"></a>Descripción de los datos

En este tutorial se usa como ejemplo un conjunto de datos con 428 imágenes de texturas de partes de arriba de ropa. Cada imagen está anotada como perteneciente a una de tres posibles texturas distintas (lunares, rayas o leopardo). Hemos usado un número de imágenes reducido para que este tutorial sea rápido de ejecutar, pero el código está totalmente probado y funciona con miles de imágenes o más. Todas las imágenes se extrajeron con búsquedas de imágenes de Bing y se anotaron a mano tal y como se explica en la [parte 3](#using-a-custom-dataset). Las direcciones URL de las imágenes aparecen en el archivo */resources/fashionTextureUrls.tsv* junto con sus correspondientes atributos.

El script `0_downloadData.py` descarga todas las imágenes en el directorio *DATA_DIR/images/fashionTexture/*. Es probable que los vínculos de algunas de las 428 direcciones URL estén rotos. Esto no supone un problema y simplemente significa que dispondremos de menos imágenes con las que entrenar y probar. Todos los scripts que se proporcionan en este ejemplo tienen que ejecutarse de forma local y no en entornos como, por ejemplo, un entorno remoto de docker.

En la siguiente ilustración se muestran ejemplos de los atributos de lunares (izquierda), a rayas (centro) y leopardo (derecha). Se realizaron las anotaciones pertinentes en función de la prenda de ropa.

<p align="center">
<img src="media/scenario-image-classification-using-cntk/examples_all.jpg"  alt="alt text" width="700">
</p>


## <a name="part-1---model-training-and-evaluation"></a>Parte 1: Entrenamiento y evaluación del modelo

En la primera parte de este tutorial, entrenaremos un sistema que usa (pero no modifica) una red neuronal profunda ya entrenada. Esta DNN ya entrenada se usa para la captura de características, y se entrena una SVM lineal para predecir el atributo (esto es, lunares, rayas o leopardo) de una imagen determinada.

Ahora pasaremos a describir este método detalladamente y a indicar los scripts que hay que ejecutar. Es recomendable que, después de cada paso, se inspeccione qué archivos se han escrito y dónde se han escrito.

Todos los parámetros importantes (además de una breve explicación) se especifican en un único lugar: el archivo `PARAMETERS.py`.




### <a name="step-1-data-preparation"></a>Paso 1: Preparación de los datos
`Script: 1_prepareData.py. Notebook: showImages.ipynb`

El bloc de notas `showImages.ipynb` se puede usar para ver las imágenes y para corregir su anotación según sea necesario. Para ejecutar el bloc de notas, ábralo en Azure Machine Learning Workbench y haga clic en "Start Notebook Server" (Iniciar el servidor del bloc de notas) si esta opción aparece; a continuación, cambie al kernel del proyecto local denominado "PROJECTNAME local" (por ejemplo,. "myImgClassUsingCNTK local") y ejecute todas las celdas del bloc de notas. Vea la sección Solución de problemas de este documento si se produce un error que indica que el bloc de notas es demasiado grande para mostrarse.
<p align="center">
<img src="media/scenario-image-classification-using-cntk/notebook_showImages.jpg" alt="alt text" width="700"/>
</p>

Ahora, ejecute el script `1_prepareData.py`, que asigna todas las imágenes al conjunto de entrenamiento o al conjunto de prueba. Estas asignaciones son excluyentes entre sí; es decir, ninguna imagen de entrenamiento se usa para las pruebas y viceversa. Un 75 % de las imágenes de cada tipo se asigna de forma predeterminada y aleatoria al entrenamiento y el 25 por ciento restante, a las pruebas. Todos los datos generados por el script se guardan en la carpeta *DATA_DIR/proc/fashionTexture/*.

<p align="center">
<img src="media/scenario-image-classification-using-cntk/output_script_1_white.jpg" alt="alt text" width="700"/>
</p>



### <a name="step-2-refining-the-deep-neural-network"></a>Paso 2: Perfeccionar la red neuronal profunda
`Script: 2_refineDNN.py`

Como ya hemos explicado en la parte 1 de este tutorial, la DNN ya entrenada se mantiene inamovible (es decir, no se perfecciona). Pero el script `2_refineDNN.py` se ejecuta en la parte 1, dado que carga un modelo de [ResNet](https://www.cv-foundation.org/openaccess/content_cvpr_2016/papers/He_Deep_Residual_Learning_CVPR_2016_paper.pdf) ya entrenado [2] y lo modifica (para, por ejemplo, permitir mayores resoluciones de imagen de entrada). Este paso es rápido (tarda apenas segundos) y no requiere una GPU.

En la parte 2 del tutorial, una modificación en el archivo PARAMETERS.py hace que el script `2_refineDNN.py` también perfeccione la DNN ya entrenada. Durante este perfeccionamiento, se ejecutan 45 tandas de entrenamiento.

En uno y otro caso, el modelo final se escribe en el archivo *DATA_DIR/proc/fashionTexture/cntk_fixed.model*.

### <a name="step-3-evaluate-dnn-for-all-images"></a>Paso 3: Evaluar la DNN para todas las imágenes
`Script: 3_runDNN.py`

Ahora, podemos usar la DNN (posiblemente mejorada) del último paso para caracterizar nuestras imágenes. Dada una imagen como entrada de la DNN, la salida es el vector de 512 flotantes de la penúltima capa del modelo. Este vector tiene unas dimensiones mucho más pequeñas que la propia imagen. A pesar de ello, debe contener (y resaltar incluso) toda la información de la imagen que sea relevante para identificar el atributo de la imagen (esto es, si la prenda de ropa es de lunares, a rayas o con estampado de leopardo).

Todas las representaciones de imagen de la DNN se guardan en el archivo *DATA_DIR/proc/fashionTexture/cntkFiles/features.pickle*.

<p align="center">
<img src="media/scenario-image-classification-using-cntk/output_script_4_white.jpg" alt="alt text" width="700"/>
</p>


### <a name="step-4-support-vector-machine-training"></a>Paso 4: Entrenamiento de una máquina de vectores de soporte
`Script: 4_trainSVM.py`

La representación de 512 flotantes calculada en el último paso se usa para entrenar un clasificador de SVM: dada una imagen como entrada, la SVM genera una puntuación para cada atributo que esté presente. En nuestro conjunto de datos de ejemplo, esto significa una puntuación para "rayas", para "lunares" y para "leopardo".

El script `4_trainSVM.py` carga las imágenes de entrenamiento, entrena una SVM para los distintos valores del parámetro de regularización (margen de demora) C y mantiene la SVM con la máxima precisión. La precisión de la clasificación se imprime en la consola y se traza en Workbench. En el caso de los datos de textura proporcionados, estos valores deben rondar el 100 % y el 88 % respectivamente. Por último, la SVM entrenada se escribe en el archivo *DATA_DIR/proc/fashionTexture/cntkFiles/svm.np*.

<p align="center">
<img src="media/scenario-image-classification-using-cntk/vienna_svm_log_zoom.jpg" alt="alt text" width="700"/>
</p>



### <a name="step-5-evaluation-and-visualization"></a>Paso 5: Evaluación y visualización
`Script: 5_evaluate.py. Notebook: showResults.ipynb`

La precisión del clasificador de imágenes entrenado se puede medir con el script `5_evaluate.py`. El script puntúa todas las imágenes de prueba con el clasificador de SVM entrenado, asigna a cada imagen el atributo con la puntuación más alta y compara los atributos de predicción con anotaciones precisas.

Aquí mostramos la salida del script `5_evaluate.py`. Se calcula la precisión de la clasificación de cada clase individual, así como la precisión del conjunto de prueba completo ("precisión total") y el promedio de las precisiones individuales ("promedio total de precisión de clases"). 100 % corresponde a la mejor precisión posible y 0 %, a la peor. Una estimación aleatoria generaría un promedio total de precisión de clases de 1 sobre el número de atributos: en nuestro caso, esta precisión sería pues del 33,33 %. Estos resultados mejoran significativamente cuando se usa una mayor resolución de entrada, como `rf_inputResoluton = 1000`, si bien esto conlleva tiempos de cálculo de DNN más prolongados.

<p align="center">
<img src="media/scenario-image-classification-using-cntk/output_script_6_white.jpg" alt="alt text" width="700"/>
</p>

Además de la precisión, la curva ROC se traza con la correspondiente área bajo la curva (izquierda). También mostramos la matriz de confusión (derecha):

<p align="center">
<img src="media/scenario-image-classification-using-cntk/roc_confMat.jpg" alt="alt text" width="700"/>
</p>

Por último, se proporciona el bloc de notas `showResults.py` para desplazarse por las imágenes de prueba y ver las correspondientes puntuaciones de clasificación. Tal como se explica en el paso 1, cada bloc de notas de este ejemplo debe usar el kernel del proyecto local denominado "PROJECTNAME local":
<p align="center">
<img src="media/scenario-image-classification-using-cntk/notebook_showResults.jpg" alt="alt text" width="700"/>
</p>





### <a name="step-6-deployment"></a>Paso 6: Implementación
`Scripts: 6_callWebservice.py, deploymain.py. Notebook: deploy.ipynb`

El sistema entrenado ya se puede publicar como una API de REST. La implementación se explica en el bloc de notas `deploy.ipynb` y se basa en la funcionalidad de Azure Machine Learning Workbench (recuerde establecer como kernel el kernel del proyecto local denominado "PROJECTNAME local"). Si quiere obtener más información detallada de la implementación, consulte también la excelente sección de implementación del [tutorial de IRIS](https://docs.microsoft.com/azure/machine-learning/preview/tutorial-classifying-iris-part-3).

Una vez implementado, el servicio web se puede llamar con el script `6_callWebservice.py`. Cabe mencionar que la dirección IP (ya sea local o en la nube) del servicio web debe establecerse antes en el script. En el bloc de notas `deploy.ipynb` se explica cómo encontrar esta dirección IP.








## <a name="part-2---accuracy-improvements"></a>Parte 2: Mejoras en la precisión

En la parte 1 hemos visto cómo clasificar una imagen, entrenando para ello una máquina de vectores de soporte lineal en la salida de 512 flotantes de una red neuronal profunda. Esta DNN se entrenó previamente con millones de imágenes y la penúltima capa se devolvió como un vector de características. Este método es rápido, porque la DNN se usa tal cual y, con todo, da buenos resultados con frecuencia.

Ahora nos centraremos en diversas maneras de mejorar la precisión del modelo de la parte 1. En concreto, vamos a perfeccionar la DNN en lugar mantenerla inamovible.

### <a name="dnn-refinement"></a>Perfeccionamiento de la DNN

En lugar de en una SVM, la clasificación se puede realizar directamente en la red neuronal. Esto se logra agregando una nueva última capa a la DNN ya entrenada, que toma los 512 flotantes de la penúltima capa como entrada. La ventaja de realizar la clasificación en la DNN es que ahora la red entera se puede volver a entrenar con una nueva propagación. Con este método se suele obtener una precisión de la clasificación mucho mejor a si usáramos la DNN ya entrenada tal cual, pero conlleva unos tiempos de cálculo de DNN más prolongados (incluso con una GPU).

Entrenar la red neuronal en lugar de una SVM se efectúa cambiando la variable `classifier` en `PARAMETERS.py` de `svm` a `dnn`. Luego, tal y como se describe en la parte 1, hay que volver a ejecutar todos los scripts, excepto el de preparación de los datos (paso 1) y el de entrenamiento de SVM (paso 3). El perfeccionamiento de la DNN requiere una GPU. Si no existe ninguna GPU o si la GPU está bloqueada (por ejemplo, porque haya una ejecución de CNTK anterior), el script `2_refineDNN.py` generará un error. En algunas GPU, el entrenamiento de la DNN puede producir un error de memoria insuficiente, que se puede soslayar si se reduce el tamaño del minilote (variable `cntk_mb_size` en `PARAMETERS.py`).

Una vez completado el entrenamiento, el modelo perfeccionado se guarda en *DATA_DIR/proc/fashionTexture/cntk_refined.model* y se traza un gráfico que muestra cómo varían los errores de clasificación de entrenamiento y de prueba durante el entrenamiento. Observe en ese gráfico que el error del conjunto de entrenamiento es mucho menor que el del conjunto de prueba. Este comportamiento, conocido como sobreajuste, se puede minimizar, por ejemplo, usando un valor más alto como tasa de eliminación (`rf_dropoutRate`).
<p align="center">
<img src="media/scenario-image-classification-using-cntk/output_script_3_plot.png" alt="alt text" height="300"/>
</p>

Como se aprecia en el siguiente gráfico, la precisión del conjunto de datos proporcionado usando el perfeccionamiento de la DNN es del 92,35 % en comparación con el 88,92 % previo (parte 1). En concreto, las imágenes relativas a los "lunares" (dotted) mejoran considerablemente y logran un área bajo la curva de ROC de 0,98 con el perfeccionamiento, frente al 0,94 anterior. Estamos usando un conjunto de datos pequeño y, por lo tanto, las precisiones reales al ejecutar el código son diferentes. Esta discrepancia obedece a efectos estocásticos como la división aleatoria de las imágenes en los conjuntos de pruebas de entrenamiento y de prueba.
<p align="center">
<img src="media/scenario-image-classification-using-cntk/roc_confMat_dnn.jpg" alt="alt text" width="700"/>
</p>

### <a name="run-history-tracking"></a>Ejecutar un seguimiento del historial

Azure Machine Learning Workbench almacena el historial de cada ejecución en Azure para, así, permitir que se puedan comparar dos o más ejecuciones con incluso semanas de diferencia. Esto se explica con más detalle en el [tutorial de Iris](https://docs.microsoft.com/azure/machine-learning/preview/tutorial-classifying-iris-part-2). También se ilustra en las siguientes capturas de pantalla, donde comparamos dos ejecuciones del script `5_evaluate.py`, ya sea por medio del perfeccionamiento de la DNN, es decir, `classifier = "dnn"` (número de ejecución 148) o del entrenamiento de la SVM, es decir, `classifier = "svm"` (número de ejecución 150).

En la primera captura de pantalla, el perfeccionamiento de la DNN lleva a unas mejores precisiones que con el entrenamiento de SVM en todas las clases. La segunda captura de pantalla muestra todas las métricas de las que se hace un seguimiento, incluido el clasificador que se usó. Este seguimiento se realiza en el script `5_evaluate.py`, llamando al registrador de Azure Machine Learning Workbench. El script también guarda la curva de ROC y la matriz de confusión en la carpeta *outputs*. Esta carpeta *outputs* es especial, en el sentido de que también se realiza un seguimiento de su contenido por medio de la característica de historial de Workbench. Por lo tanto, se puede tener acceso a sus archivos siempre que se quiera, independientemente de si las copias locales se han sobrescrito.

<p align="center">
<img src="media/scenario-image-classification-using-cntk/run_comparison1.jpg" alt="alt text" width="700"/>  
</p>

<p align="center">
<img src="media/scenario-image-classification-using-cntk/run_comparison2b.jpg" alt="alt text" width="700"/>
</p>


### <a name="parameter-tuning"></a>Ajuste de parámetros
Como sucede en la mayoría de los proyectos de aprendizaje automático, para obtener buenos resultados en un conjunto de datos nuevo, es necesario ajustar los parámetros con mucho cuidado, así como sopesar detenidamente diversas decisiones de diseño. Para hacer estas tareas más sencillas, todos los parámetros importantes (además de una breve explicación) se especifican en un único lugar: el archivo `PARAMETERS.py`.

Estos son algunos aspectos que sin duda se traducirán en mejoras:

- Calidad de los datos: asegúrese de que la calidad de los conjuntos de entrenamiento y de prueba es alta. Es decir, procure que las imágenes estén correctamente anotadas, que ha quitado las imágenes ambiguas (por ejemplo, prendas de ropa que tienen tanto rayas como lunares) y que los atributos son excluyentes entre sí (dicho de otro modo, que los ha elegido de tal forma que cada imagen pertenece a exactamente un atributo).
- Hay constancia de que, si el objeto de interés es pequeño en la imagen, los métodos de clasificación de imágenes no funcionan bien. En tales casos, considere el uso de un método de detección de objetos, tal y como se describe en este [tutorial](https://github.com/Azure/ObjectDetectionUsingCntk).
- Perfeccionamiento de la DNN: seguramente, el parámetro más importante para realizar esta tarea correctamente sea la velocidad de aprendizaje `rf_lrPerMb`. Si la precisión del conjunto de entrenamiento (primera figura de la parte 2) no ronda el 0-5 %, probablemente se deba a un error en la velocidad de aprendizaje. Los demás parámetros que comienzan por `rf_` no son tan importantes. Normalmente, el error de entrenamiento debería disminuir exponencialmente y aproximarse a 0 % después del entrenamiento.
- Resolución de entrada: la resolución de imagen predeterminada es 224 x 224 píxeles. Si usa una resolución de imagen mayor (parámetro: `rf_inputResoluton`), por ejemplo, 448 x 448 o 896 x 896 píxeles, con frecuencia obtendrá una mejora significativa, pero el perfeccionamiento de la DNN tardará más en completarse. **Usar una mayor resolución es inocuo y casi siempre aumenta la precisión**.
- Sobreajuste de la DNN: procure que no haya mucha diferencia entre la precisión del conjunto de entrenamiento y la del conjunto de prueba durante el perfeccionamiento de la DNN (primera figura de la parte 2). Esta diferencia se puede reducir usando tasas de eliminación (`rf_dropoutRate`) de 0,5 o más, así como aumentando el peso del regularizador `rf_l2RegWeight`. Usar una tasa de eliminación elevada puede ser especialmente útil si la resolución de imagen de entrada de DNN es alta.
- Pruebe a usar DNN un poco más profundas, cambiando para ello `rf_pretrainedModelFilename` de `ResNet_18.model` a `ResNet_34.model` o a `ResNet_50.model`. El modelo ResNet-50 no solo es más profundo, sino que, además, su salida de la penúltima capa tiene un tamaño de 2048 flotantes (frente a los 512 flotantes de los modelos ResNet-18 y ResNet 34). Esta mayor dimensión puede resultar especialmente útil al entrenar un clasificador de SVM.

## <a name="part-3---custom-dataset"></a>Parte 3: Conjunto de datos personalizado

En las partes 1 y 2, hemos entrenado y evaluado un modelo de clasificación de imágenes usando las imágenes de texturas de partes de arriba de ropa proporcionadas. Ahora, describiremos cómo usar un conjunto de datos personalizado proporcionado por el usuario, en lugar de esas imágenes. O bien, si no hay un conjunto disponible, cómo generar y anotar uno por medio de la búsqueda de imágenes de Bing.

### <a name="using-a-custom-dataset"></a>Usar un conjunto de datos personalizado

En primer lugar, echemos un vistazo a la estructura de carpetas de los datos de texturas de prendas de ropa. Observe cómo todas las imágenes relativas a cada atributo se encuentran en las subcarpetas correspondientes (*dotted*, *leopard y *striped*) dentro de *DATA_DIR/images/fashionTexture/*. Observe también cómo el nombre de la carpeta de imágenes también aparece reflejado en el archivo `PARAMETERS.py`:
```python
datasetName = "fashionTexture"
```

Usar un conjunto de datos personalizado es tan sencillo como reproducir esta misma estructura de carpetas donde están todas las imágenes en subcarpetas según sus atributos y, luego, copiar estas subcarpetas en un nuevo directorio especificado por el usuario, *DATA_DIR/images/newDataSetName/*. El único cambio de código necesario consiste en establecer la variable `datasetName` en *newDataSetName*. Tras ello, los scripts 1-5 se pueden ejecutar en orden y todos los archivos intermedios se escriben en *DATA_DIR/proc/newDataSetName/*. No se requiere ningún cambio de código más.

Es importante que cada imagen pueda asignarse a exactamente un solo atributo. Por ejemplo, sería incorrecto usar atributos para "animal" y para "leopardo", dado que "leopardo" también pertenecería a la clasificación "animal". De igual modo, conviene eliminar las imágenes ambiguas (y, por tanto, difíciles de anotar).



### <a name="image-scraping-and-annotation"></a>Extracción y anotación de imágenes

Recopilar un número suficientemente grande de imágenes anotadas para el entrenamiento y las pruebas puede resultar complicado. Una forma de solucionar este problema consiste en extraer imágenes de Internet. Por ejemplo, eche un vistazo a los siguientes resultados de una búsqueda de imágenes de Bing a raíz de la consulta *t-shirt striped* para encontrar camisetas a rayas. Como se esperaba, la mayoría de las imágenes son verdaderamente camisetas a rayas. Las pocas imágenes incorrectas o ambiguas (por ejemplo, la de la columna 1, fila 1, o la de la columna 3, fila 2) se pueden identificar y quitar fácilmente:
<p align="center">
<img src="media/scenario-image-classification-using-cntk/bing_search_striped.jpg" alt="alt text" width="600"/>
</p>

Para generar un conjunto de datos voluminoso y diverso, hay que realizar varias consultas. Por ejemplo, si tenemos 7 \* 3 = 21 consultas, se pueden sintetizar automáticamente usando todas las combinaciones de prendas de ropa {blusa, sudadera, sudadera con capucha, jersey, camisa, camiseta, chaleco} y atributos {rayas, lunares, leopardo}. Descargar las primeras 50 imágenes de cada consulta nos llevaría a tener un máximo de 21 x 50 = 1050 imágenes.

En lugar de descargarlas manualmente desde la búsqueda de imágenes de Bing, es mucho más fácil usar en su lugar la [Bing Image Search API de Cognitive Services](https://www.microsoft.com/cognitive-services/bing-image-search-api), que devuelve un conjunto de direcciones URL de imágenes correspondientes a una cadena de consulta determinada.

Algunas de las imágenes descargadas son duplicados exactos o casi exactos (por ejemplo, solo se diferencian por la resolución de imagen o por anomalías del formato jpg). Estos duplicados se deben quitar para que los conjuntos de entrenamiento y de prueba no contengan las mismas imágenes. La eliminación de imágenes duplicadas puede lograrse con un método basado en hash, consistente en dos pasos: (1) en primer lugar, se calcula la cadena hash de todas las imágenes y (2) en un segundo procesamiento de las imágenes, solamente se conservan aquellas que tengan una cadena hash que aún no se haya visto. Todas las demás imágenes se descartan. Hemos constatado que el método `dhash` de la biblioteca de Python `imagehash` (descrito en este [blog](http://www.hackerfactor.com/blog/index.php?/archives/529-Kind-of-Like-That.html)) funciona bien con el parámetro `hash_size` establecido en 16. No pasa nada si se quitan algunas imágenes no duplicadas por error, siempre y cuando se quiten la mayoría de los duplicados reales.





## <a name="conclusion"></a>Conclusión

Algunos aspectos destacados de este ejemplo son:
- El código usado para entrenar, evaluar e implementar un modelo personalizado de clasificación de imágenes.
- El uso de las imágenes de demostración suministradas, aunque esto es fácilmente adaptable (solo habría que cambiar una línea) para usar un conjunto de datos de imágenes propio.
- Las características modernas y de nivel de experto implementadas para entrenar modelos de alta precisión basados en el aprendizaje de transferencia.
- El desarrollo de modelos interactivos con Azure Machine Learning Workbench y Jupyter Notebook.


## <a name="references"></a>Referencias

[1] Alex Krizhevsky, Ilya Sutskever y Geoffrey E. Hinton. [_ImageNet Classification with Deep Convolutional Neural Networks_](https://papers.nips.cc/paper/4824-imagenet-classification-with-deep-convolutional-neural-networks.pdf) (Clasificación de ImageNet con redes neuronales profundas de convolución). NIPS 2012.  
[2] Kaiming He, Xiangyu Zhang, Shaoqing Ren y Jian Sun. [_Deep Residual Learning for Image Recognition_](https://www.cv-foundation.org/openaccess/content_cvpr_2016/papers/He_Deep_Residual_Learning_CVPR_2016_paper.pdf) (Aprendizaje residual profundo para el reconocimiento de imágenes). CVPR 2016.
