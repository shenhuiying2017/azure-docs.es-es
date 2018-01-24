---
title: "Ajuste distribuido de hiperparámetros con Azure Machine Learning Workbench | Documentos de Microsoft"
description: "Este escenario muestra cómo llevar a cabo el ajuste distribuido de hiperparámetros con Azure Machine Learning Workbench"
services: machine-learning
author: pechyony
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.author: dmpechyo
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.date: 09/20/2017
ms.openlocfilehash: f0c466c433701c295bde00258d9ff7fd267b71f7
ms.sourcegitcommit: 234c397676d8d7ba3b5ab9fe4cb6724b60cb7d25
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/20/2017
---
# <a name="distributed-tuning-of-hyperparameters-using-azure-machine-learning-workbench"></a>Ajuste distribuido de hiperparámetros con Azure Machine Learning Workbench

Este escenario muestra cómo usar Azure Machine Learning Workbench para escalar horizontalmente el ajuste de hiperparámetros de algoritmos de aprendizaje automático que implementan la API scikit-learn. Se muestra cómo configurar y usar un contenedor de Docker remoto y un clúster de Spark como un back-end de ejecución para el ajuste de hiperparámetros.

## <a name="link-of-the-gallery-github-repository"></a>Vínculo al repositorio de GitHub de la galería
A continuación se muestra el vínculo al repositorio de GitHub público: 

[https://github.com/Azure/MachineLearningSamples-DistributedHyperParameterTuning](https://github.com/Azure/MachineLearningSamples-DistributedHyperParameterTuning)

## <a name="use-case-overview"></a>Información general del caso de uso

Muchos algoritmos de aprendizaje automático tienen uno o varios mandos de control, llamados hiperparámetros. Estos mandos de control permiten ajustar algoritmos para optimizar su rendimiento en datos futuros, medido según métricas especificadas por el usuario (por ejemplo, precisión, AUC, RMSE). Los científicos de datos tienen que proporcionar valores para los hiperparámetros al generar un modelo con datos de entrenamiento antes de ver los datos de pruebas futuras. Basándonos en los datos de entrenamiento conocidos, ¿cómo podemos configurar los valores de los hiperparámetros de modo que el modelo tenga un buen rendimiento con datos de prueba desconocidos? 
    
Una técnica popular para el ajuste de hiperparámetros es la *búsqueda de cuadrícula* combinada con *validación cruzada*. La validación cruzada es una técnica que evalúa la predicción de un modelo entrenado en un conjunto de entrenamiento sobre el conjunto de prueba. Con esta técnica, primero se divide el conjunto de datos en K subconjuntos y luego se entrena el algoritmo K veces de un modo round robin. Esta técnica se emplea en todos los subconjuntos excepto en uno de ellos denominado "subconjunto apartado". Se calcula el valor medio de las métricas de K modelos sobre K subconjuntos apartados. Este valor promedio, denominado *estimación de rendimiento de validación cruzada*, depende de los valores de los hiperparámetros utilizados al crear K modelos. Al ajustar hiperparámetros, se busca en el espacio de valores del hiperparámetro candidato para encontrar los que optimizan la estimación de rendimiento de validación cruzada. La búsqueda de cuadrícula es una técnica de búsqueda común. En la búsqueda de cuadrícula, el espacio de valores candidatos de varios hiperparámetros es un producto cruzado de conjuntos de valores candidatos de hiperparámetros individuales. 

La búsqueda de cuadrícula con la validación cruzada puede consumir mucho tiempo. Si un algoritmo tiene cinco hiperparámetros cada uno con cinco valores candidatos, usamos K=5 subconjuntos. Después, se completa una búsqueda de cuadrícula entrenando 5<sup>6</sup>= 15 625 modelos. Afortunadamente, la búsqueda de cuadrícula con la validación cruzada es un procedimiento paralelo y se puede entrenar todos estos modelos en paralelo.

## <a name="prerequisites"></a>requisitos previos

* Una [cuenta de Azure](https://azure.microsoft.com/free/) (hay disponibles versiones gratuitas de prueba).
* Una copia instalada de [Azure Machine Learning Workbench](./overview-what-is-azure-ml.md) siguiendo la [Guía de instalación de inicio rápido](./quickstart-installation.md) para instalar el programa y crear cuentas.
* En este escenario se da por supuesto que está ejecutando Azure ML Workbench en Windows 10 o MacOS con el motor de Docker instalado localmente. 
* Para ejecutar el escenario con un contenedor de Docker remoto, aprovisione la instancia de Data Science Virtual Machine (DSVM) Ubuntu siguiendo estas [instrucciones](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-provision-vm). Se recomienda usar una máquina virtual con al menos 8 núcleos y 28 GB de memoria. Las instancias de máquinas virtuales D4 tienen esa capacidad. 
* Para ejecutar este escenario con un clúster de Spark, aprovisione el clúster de Azure HDInsight siguiendo estas [instrucciones](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-provision-linux-clusters).   
Se recomienda tener un clúster con al menos:
    - seis nodos de trabajo
    - ocho núcleos
    - 28 GB de memoria en los nodos de encabezado y de trabajo. Las instancias de máquinas virtuales D4 tienen esa capacidad.       
    - Es recomendable cambiar los parámetros siguientes para maximizar el rendimiento del clúster:
        - spark.executor.instances
        - spark.executor.cores
        - spark.executor.memory 

Puede seguir estas [instrucciones](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-resource-manager) y editar las definiciones en la sección de personalización de valores predeterminados de spark".

     **Troubleshooting**: Your Azure subscription might have a quota on the number of cores that can be used. The Azure portal does not allow the creation of cluster with the total number of cores exceeding the quota. To find you quota, go in the Azure portal to the Subscriptions section, click on the subscription used to deploy a cluster and then click on **Usage+quotas**. Usually quotas are defined per Azure region and you can choose to deploy the Spark cluster in a region where you have enough free cores. 

* Cree una cuenta de Azure Storage que se usará para almacenar el conjunto de datos. Siga las [instrucciones](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account) para crear una cuenta de almacenamiento.

## <a name="data-description"></a>Descripción de los datos

Usamos el [conjunto de datos TalkingData](https://www.kaggle.com/c/talkingdata-mobile-user-demographics/data). Este conjunto de datos contiene eventos desde las aplicaciones en teléfonos móviles. El objetivo es predecir las categorías género y edad del usuario del teléfono móvil dado el tipo de teléfono y los eventos que el usuario ha generado recientemente.  

## <a name="scenario-structure"></a>Estructura del escenario
Este escenario tiene varias carpetas en el repositorio de GitHub. Los archivos de código y configuración se encuentran en la carpeta **Code** (Código), toda la documentación se encuentra en la carpeta **Docs** (Documentos) y todas las imágenes en la carpeta **Images** (Imágenes). La carpeta raíz tiene el archivo Léame que contiene un breve resumen de este escenario.

### <a name="getting-started"></a>Introducción
Haga clic en el icono de Azure Machine Learning Workbench para ejecutar el programa y crear un proyecto a partir de la plantilla "Distributed Tuning of Hyperparameters" (Ajuste distribuido de hiperparámetros). Puede encontrar instrucciones detalladas sobre cómo crear un nuevo proyecto en [Guía de instalación de inicio rápido](quickstart-installation.md).   

### <a name="configuration-of-execution-environments"></a>Configuración de los entornos de ejecución
Se muestra cómo ejecutar el código en un contenedor de Docker remoto y en un clúster de Spark. Comenzaremos con la descripción de las configuraciones que son comunes a ambos entornos. 

Usamos los paquetes [scikit-learn](https://anaconda.org/conda-forge/scikit-learn), [xgboost](https://anaconda.org/conda-forge/xgboost) y [azure-storage](https://pypi.python.org/pypi/azure-storage), que no se proporcionan en el contenedor de Docker predeterminado de Azure Machine Learning Workbench. El paquete azure-storage requiere la instalación de los paquetes [cryptography](https://pypi.python.org/pypi/cryptography) (criptografía) y [azure](https://pypi.python.org/pypi/azure). Para instalar estos paquetes en el contenedor de Docker y en los nodos del clúster de Spark, se modifica el archivo conda_dependencies.yml:

    name: project_environment
    channels:
      - conda-forge
    dependencies:
      - python=3.5.2
      - scikit-learn
      - xgboost
      - pip:
        - cryptography
        - azure
        - azure-storage

La archivo modificado conda\_dependencies.yml se almacena en el directorio aml_config del tutorial. 

En los pasos siguientes, se conecta el entorno de ejecución con la cuenta de Azure. Haga clic en el menú Archivo situado en la esquina superior izquierda de AML Workbench. Elija "Abrir símbolo del sistema". A continuación, en la CLI, ejecute

    az login

Recibirá un mensaje

    To sign in, use a web browser to open the page https://aka.ms/devicelogin and enter the code <code> to authenticate.

Vaya a esta página web, escriba el código e inicie sesión en su cuenta de Azure. Después de este paso, en la CLI, ejecute

    az account list -o table

y busque el identificador de la suscripción de Azure que tenga su cuenta de AML Workbench Workspace. Por último, en la CLI, ejecute

    az account set -s <subscription ID>

para realizar la conexión a su suscripción de Azure.

En las dos secciones siguientes se muestra cómo completar la configuración del contenedor de Docker remoto y el clúster de Spark.

#### <a name="configuration-of-remote-docker-container"></a>Configuración del contenedor de Docker remoto

 Para configurar un contenedor de Docker remoto, en la CLI, ejecute

    az ml computetarget attach remotedocker --name dsvm --address <IP address> --username <username> --password <password> 

con la dirección IP, nombre de usuario y contraseña de la DSVM. La dirección IP de la DSVM se puede encontrar en la sección Información general de la página de la DSVM en Azure Portal:

![Dirección IP de la máquina virtual](media/scenario-distributed-tuning-of-hyperparameters/vm_ip.png)

#### <a name="configuration-of-spark-cluster"></a>Configuración del clúster de Spark

Para configurar el entorno de Spark, en la CLI, ejecute

    az ml computetarget attach cluster--name spark --address <cluster name>-ssh.azurehdinsight.net  --username <username> --password <password> 

con el nombre del clúster, el nombre de usuario SSH del clúster y la contraseña. El valor predeterminado del nombre de usuario SSH es `sshuser`, a menos que se haya cambiado durante el aprovisionamiento del clúster. El nombre del clúster se puede encontrar en la sección Propiedades de la página del clúster en Azure Portal:

![Nombre del clúster](media/scenario-distributed-tuning-of-hyperparameters/cluster_name.png)

Utilizamos el paquete spark-sklearn para usar Spark como un entorno de ejecución para el ajuste distribuido de hiperparámetros. Modificamos el archivo spark_dependencies.yml para instalar este paquete cuando se usa el entorno de ejecución de Spark:

    configuration: {}
    repositories:
      - "https://mmlspark.azureedge.net/maven"
      - "https://spark-packages.org/packages"
    packages:
      - group: "com.microsoft.ml.spark"
        artifact: "mmlspark_2.11"
        version: "0.7"
      - group: "databricks"
        artifact: "spark-sklearn"
        version: "0.2.0"

La archivo modificado spark\_dependencies.yml se almacena en el directorio aml_config del tutorial. 

### <a name="data-ingestion"></a>Ingesta de datos
En el código de este escenario se supone que los datos se almacenan en Azure Blob Storage. Inicialmente, se muestra cómo descargar datos desde el sitio de Kaggle en el equipo y cargarlos en el almacenamiento de blobs. A continuación, se muestra cómo leer los datos desde el almacenamiento de blobs. 

Para descargar datos desde Kaggle, vaya a la [página del conjunto de datos](https://www.kaggle.com/c/talkingdata-mobile-user-demographics/data) y haga clic en el botón de descarga. Se le pedirá que inicie sesión en Kaggle. Después de iniciar sesión, se le redirigirá a la página del conjunto de datos. A continuación, descargue los siete primeros archivos de la columna izquierda seleccionándolos y haciendo clic en el botón de descarga. El tamaño total de los archivos descargados es 289 Mb. Para cargar estos archivos al almacenamiento de blobs, cree un contenedor de almacenamiento de blobs llamado 'dataset' en la cuenta de almacenamiento. Para ello, vaya a la página de Azure de la cuenta de almacenamiento, haga clic en Blobs y, a continuación, haga clic en +Contenedor. Escriba 'dataset' como Nombre y haga clic en Aceptar. Las capturas de pantalla siguientes ilustran estos pasos:

![Abrir blob](media/scenario-distributed-tuning-of-hyperparameters/open_blob.png)
![Abrir contenedor](media/scenario-distributed-tuning-of-hyperparameters/open_container.png)

A continuación, seleccione el contenedor del conjunto de datos en la lista y haga clic en el botón Cargar. Azure Portal le permite cargar varios archivos al mismo tiempo. En la sección "Cargar blob", haga clic en el botón de carpeta, seleccione todos los archivos del conjunto de datos y, a continuación, haga clic en Abrir y, a continuación, haga clic en Cargar. La siguiente captura de pantalla muestra estos pasos:

![Carga de blob](media/scenario-distributed-tuning-of-hyperparameters/upload_blob.png) 

La carga de los archivos tarda varios minutos, dependiendo de la conexión a Internet. 

En el código, utilizamos el [SDK de Azure Storage](https://azure-storage.readthedocs.io/en/latest/) para descargar el conjunto de datos desde el almacenamiento de blobs al entorno de ejecución actual. La descarga se realiza en la función load\_ldata() del archivo load_data.py. Para usar este código, es necesario reemplazar <ACCOUNT_NAME> y <ACCOUNT_KEY> por el nombre y la clave principal de la cuenta de almacenamiento que hospeda el conjunto de datos. Puede ver el nombre de la cuenta en la esquina superior izquierda de la página de Azure de la cuenta de almacenamiento. Para obtener la clave de la cuenta, seleccione Claves de acceso en la página de Azure de la cuenta de almacenamiento (ver la primera captura de pantalla en la sección Ingesta de datos) y, a continuación, copie la cadena larga en la primera fila de la columna de clave:
 
![clave de acceso](media/scenario-distributed-tuning-of-hyperparameters/access_key.png)

El siguiente código de la función load_data() descarga un único archivo:

    from azure.storage.blob import BlockBlobService

    # Define storage parameters 
    ACCOUNT_NAME = "<ACCOUNT_NAME>"
    ACCOUNT_KEY = "<ACCOUNT_KEY>"
    CONTAINER_NAME = "dataset"

    # Define blob service     
    my_service = BlockBlobService(account_name=ACCOUNT_NAME, account_key=ACCOUNT_KEY)

    # Load blob
    my_service.get_blob_to_path(CONTAINER_NAME, 'app_events.csv.zip', 'app_events.csv.zip')

Tenga en cuenta que no es necesario ejecutar el archivo load_data.py manualmente. Más adelante se llama desde otros archivos.

### <a name="feature-engineering"></a>Ingeniería de características
El código para calcular todas las características en el archivo feature\_engineering.py. No es necesario ejecutar el archivo feature_engineering.py manualmente. Más adelante se llamará desde otros archivos.

Creamos varios conjuntos de características:
* Codificación "uno a n" de la marca y el modelo de teléfono móvil (función one\_hot\_brand_model)
* Fracción de los eventos generados por el usuario en cada día de la semana (función weekday\_hour_features)
* Fracción de los eventos generados por el usuario en cada hora (función weekday\_hour_features)
* Fracción de los eventos generados por el usuario en cada combinación de día de la semana y hora (función weekday\_hour_features)
* Fracción de los eventos generados por el usuario en cada aplicación (función one\_hot\_app_labels)
* Fracción de los eventos generados por el usuario en cada etiqueta de aplicación (función one\_hot\_app_labels)
* Fracción de los eventos generados por el usuario en cada categoría de aplicación (función text\_category_features)
* Características de indicador para categorías de aplicaciones que se usaron para generan eventos (función one\_hot_category)

Estas características están inspiradas en [Un modelo lineal para aplicaciones y etiquetas](https://www.kaggle.com/dvasyukova/a-linear-model-on-apps-and-labels) del kernel de Kaggle.

El cálculo de estas características requiere gran cantidad de memoria. Inicialmente se intentó calcular las características en el entorno local con 16 GB de RAM. Se pudieron calcular los cuatro primeros conjuntos de características, pero se recibió un error 'Sin memoria' al calcular el quinto conjunto de características. El cálculo de los primeros cuatro conjuntos de características se encuentra en el archivo singleVMsmall.py y se puede ejecutar en el entorno local mediante la ejecución de 

     az ml experiment submit -c local .\singleVMsmall.py   

en la ventana de la CLI.

Puesto que el entorno local es demasiado pequeño para calcular todos los conjuntos de características, cambiamos a la instancia de DSVM remota, que tiene una memoria mayor. La ejecución en la instancia de DSVM se realiza en el contenedor de Docker que se administra mediante AML Workbench. Con esta instancia de DSVM se pudieron calcular todas las características, entrenar modelos y ajustar los hiperparámetros (consulte la sección siguiente). El archivo singleVM.py tiene el cálculo completo de características y el código de modelado. En la siguiente sección, le mostraremos cómo ejecutar singleVM.py en la instancia de DSVM remota. 

### <a name="tuning-hyperparameters-using-remote-dsvm"></a>Ajuste de hiperparámetros con la instancia de DSVM remota
Usamos la implementación de [xgboost](https://anaconda.org/conda-forge/xgboost) [1] de la potenciación de árboles de gradiente. Usamos el paquete [scikit-learn](http://scikit-learn.org/) para ajustar los hiperparámetros de xgboost. Aunque xgboost no forma parte del paquete scikit-learn, implementa la API de scikit-learn, por lo que puede utilizarse junto con las funciones de ajuste de hiperparámetros de scikit-learn. 

Xgboost tiene ocho hiperparámetros:
* n_estimators
* max_depth
* reg_alpha
* reg_lambda
* colsample\_by_tree
* learning_rate
* colsample\_by_level
* subsample
* objective Puede encontrar una descripción de estos hiperparámetros en
- http://xgboost.readthedocs.io/en/latest/python/python_api.html#module-xgboost.sklearn- https://github.com/dmlc/xgboost/blob/master/doc/parameter.md). 
- 
Inicialmente, utilizamos la instancia de DSVM remota y ajustamos los hiperparámetros a partir de una cuadrícula pequeña de valores candidatos:

    tuned_parameters = [{'n_estimators': [300,400], 'max_depth': [3,4], 'objective': ['multi:softprob'], 'reg_alpha': [1], 'reg_lambda': [1], 'colsample_bytree': [1],'learning_rate': [0.1], 'colsample_bylevel': [0.1,], 'subsample': [0.5]}]  

Esta cuadrícula tiene cuatro combinaciones de valores de los hiperparámetros. Usamos validación cruzada de 5 subconjuntos, resultando 4 x 5 = 20 ejecuciones de xgboost. Para medir el rendimiento de los modelos, usamos la métrica de pérdida logarítmica negativa. El código siguiente busca los valores de hiperparámetros de la cuadrícula que maximizan la pérdida logarítmica negativa de la validación cruzada. El código también utiliza estos valores para entrenar el modelo final sobre el conjunto de entrenamiento completo:

    clf = XGBClassifier(seed=0)
    metric = 'neg_log_loss'
    
    clf_cv = GridSearchCV(clf, tuned_parameters, scoring=metric, cv=5, n_jobs=8)
    model = clf_cv.fit(X_train,y_train)

Después de crear el modelo, se guardan los resultados del ajuste del hiperparámetro. Se utiliza la API de registro de AML Workbench para guardar los mejores valores de los hiperparámetros y la correspondiente validación cruzada de la estimación de pérdida logarítmica negativa:

    from azureml.logging import get_azureml_logger

    # initialize logger
    run_logger = get_azureml_logger()

    ...

    run_logger.log(metric, float(clf_cv.best_score_))

    for key in clf_cv.best_params_.keys():
        run_logger.log(key, clf_cv.best_params_[key]) 

También creamos el archivo sweeping_results.txt con la validación cruzada de las pérdidas logarítmicas negativas de todas las combinaciones de valores de hiperparámetros de la cuadrícula.

    if not path.exists('./outputs'):
        makedirs('./outputs')
    outfile = open('./outputs/sweeping_results.txt','w')

    print("metric = ", metric, file=outfile)
    for i in range(len(model.grid_scores_)):
        print(model.grid_scores_[i], file=outfile)
    outfile.close()

Este archivo se almacena en un directorio especial ./outputs. Más adelante mostraremos cómo descargarlo.  

 Antes de ejecutar singleVM.py en la instancia de DSVM remota por primera vez, creamos un contenedor de Docker mediante la ejecución de 

    az ml experiment prepare -c dsvm

en la ventana de la CLI. La creación del contenedor de Docker puede tardar varios minutos. Después de ello, ejecutamos singleVM.py en la instancia de DSVM:

    az ml experiment submit -c dsvm .\singleVM.py

Este comando finaliza en 1 hora y 38 minutos con una instancia de DSVM con 8 núcleos y 28 GB de memoria. Los valores registrados pueden verse en la ventana Historial de ejecución de AML Workbench:

![historial de ejecución](media/scenario-distributed-tuning-of-hyperparameters/run_history.png)

De forma predeterminada, la ventana Historial de ejecución muestra valores y gráficos de los primeros 1-2 valores registrados. Para ver la lista completa de los valores elegidos de hiperparámetros, haga clic en el icono de configuración marcado con un círculo rojo en la captura de pantalla anterior. Después, seleccione los hiperparámetros que se mostrarán en la tabla. Además, para seleccionar los gráficos que se muestran en la parte superior de la ventana Historial de ejecución, haga clic en el icono de configuración marcado con un círculo azul y seleccione los gráficos de la lista. 

También se pueden examinar los valores elegidos de hiperparámetros en la ventana Propiedades de ejecución: 

![propiedades de ejecución](media/scenario-distributed-tuning-of-hyperparameters/run_properties.png)

En la esquina superior derecha de la ventana Propiedades de ejecución, hay una sección de archivos de salida con la lista de todos los archivos que se crearon en la carpeta '. \output'. El archivo sweeping\_results.txt puede descargarse desde allí seleccionándolo y haciendo clic en el botón de descarga. El archivo sweeping_results.txt debe tener la siguiente salida:

    metric =  neg_log_loss
    mean: -2.29096, std: 0.03748, params: {'colsample_bytree': 1, 'learning_rate': 0.1, 'subsample': 0.5, 'n_estimators': 300, 'reg_alpha': 1, 'objective': 'multi:softprob', 'colsample_bylevel': 0.1, 'reg_lambda': 1, 'max_depth': 3}
    mean: -2.28712, std: 0.03822, params: {'colsample_bytree': 1, 'learning_rate': 0.1, 'subsample': 0.5, 'n_estimators': 400, 'reg_alpha': 1, 'objective': 'multi:softprob', 'colsample_bylevel': 0.1, 'reg_lambda': 1, 'max_depth': 3}
    mean: -2.28706, std: 0.03863, params: {'colsample_bytree': 1, 'learning_rate': 0.1, 'subsample': 0.5, 'n_estimators': 300, 'reg_alpha': 1, 'objective': 'multi:softprob', 'colsample_bylevel': 0.1, 'reg_lambda': 1, 'max_depth': 4}
    mean: -2.28530, std: 0.03927, params: {'colsample_bytree': 1, 'learning_rate': 0.1, 'subsample': 0.5, 'n_estimators': 400, 'reg_alpha': 1, 'objective': 'multi:softprob', 'colsample_bylevel': 0.1, 'reg_lambda': 1, 'max_depth': 4}

### <a name="tuning-hyperparameters-using-spark-cluster"></a>Ajuste de hiperparámetros con el clúster de Spark
Se usa el clúster de Spark para escalar horizontalmente el ajuste de hiperparámetros y usar una cuadrícula más grande. Nuestra nueva cuadrícula es

    tuned_parameters = [{'n_estimators': [300,400], 'max_depth': [3,4], 'objective': ['multi:softprob'], 'reg_alpha': [1], 'reg_lambda': [1], 'colsample_bytree': [1], 'learning_rate': [0.1], 'colsample_bylevel': [0.01, 0.1], 'subsample': [0.5, 0.7]}]

Esta cuadrícula tiene 16 combinaciones de valores de los hiperparámetros. Dado que usamos validación cruzada de 5 subconjuntos, ejecutamos xgboost 16 x 5 = 80 veces.

El paquete scikit-learn no tiene una compatibilidad nativa con el ajuste de hiperparámetros con un clúster de Spark. Afortunadamente, el paquete [spark-sklearn](https://spark-packages.org/package/databricks/spark-sklearn) de Databricks llena este vacío. Este paquete proporciona la función GridSearchCV que tiene casi la misma API que la función GridSearchCV de scikit-learn. Para usar spark-sklearn y ajustar los hiperparámetros con Spark es necesario conectarse para crear el contexto de Spark

    from pyspark import SparkContext
    sc = SparkContext.getOrCreate()

A continuación, reemplazamos 

    from sklearn.model_selection import GridSearchCV

por 

    from spark_sklearn import GridSearchCV

También se reemplaza la llamada a GridSearchCV desde scikit-learn por la de spark-sklearn:

    clf_cv = GridSearchCV(sc = sc, param_grid = tuned_parameters, estimator = clf, scoring=metric, cv=5)

El código final para el ajuste de hiperparámetros con Spark está en el archivo distributed\_sweep.py. La diferencia entre singleVM.py y distributed_sweep.py está en la definición de la cuadrícula y cuatro líneas de código adicionales. Observe también que debido a los servicios de AML Workbench, el código de registro no cambia cuando se cambia el entorno de ejecución de la instancia de DSVM remota al clúster de Spark.

Antes de ejecutar distributed_sweep.py en el clúster de Spark por primera vez, es necesario instalar paquetes de Python en él. Esto puede realizarse mediante la ejecución de 

    az ml experiment prepare -c spark

en la ventana de la CLI. Esta instalación puede tardar varios minutos. Después, ejecutamos distributed_sweep.py en el clúster de Spark:

    az ml experiment submit -c spark .\distributed_sweep.py

Este comando finaliza en 1 hora y 6 minutos con un clúster de Spark que tiene 6 nodos de trabajo con 28 GB de memoria. Se puede acceder a los resultados de la optimización de hiperparámetros en Azure Machine Learning Workbench del mismo modo que la ejecución remota de DSVM. (es decir, registros, mejores valores de hiperparámetros y archivo sweeping_results.txt)

### <a name="architecture-diagram"></a>Diagrama de la arquitectura

El siguiente diagrama muestra el flujo de trabajo global: ![arquitectura](media/scenario-distributed-tuning-of-hyperparameters/architecture.png) 

## <a name="conclusion"></a>Conclusión 

En este escenario, hemos mostrado cómo usar Azure Machine Learning Workbench para realizar el ajuste de hiperparámetros en máquinas virtuales remotas y en clústeres de Spark. Hemos visto que Azure Machine Learning Workbench proporciona herramientas para facilitar la configuración de entornos de ejecución. También permite cambiar fácilmente entre ellos. 

## <a name="references"></a>Referencias

[1] T. Chen y C. Guestrin. [XGBoost: A Scalable Tree Boosting System](https://arxiv.org/abs/1603.02754) (XGBoost: sistema de potenciación de árboles escalable). KDD 2016.




