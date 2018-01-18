---
title: "Previsión de carga de trabajo del servidor en terabytes de datos: Azure | Microsoft Docs"
description: "Cómo entrenar un modelo de aprendizaje automático sobre macrodatos con Azure Machine Learning Workbench."
services: machine-learning
documentationcenter: 
author: daden
manager: mithal
editor: daden
ms.assetid: 
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/15/2017
ms.author: daden
ms.openlocfilehash: f2482c7a47c72d192f26f3d8d9b9249af53da25d
ms.sourcegitcommit: 48fce90a4ec357d2fb89183141610789003993d2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/12/2018
---
# <a name="server-workload-forecasting-on-terabytes-of-data"></a>Previsión de carga de trabajo del servidor en terabytes de datos

En este artículo se explica de qué forma los científicos de datos pueden usar Azure Machine Learning Workbench para desarrollar soluciones que requieren el uso de macrodatos. Puede empezar a partir de una muestra de un conjunto de datos grande, repetir mediante la preparación de datos, la ingeniería de características y el aprendizaje automático, y luego extender el proceso a todo el conjunto de datos grande. 

Obtendrá información sobre las siguientes capacidades claves de Machine Learning Workbench:
* Cambie fácilmente entre destinos de proceso. Puede configurar diferentes destinos de proceso y usarlos en la experimentación. En este ejemplo, usamos una DSVM Ubuntu y un clúster de Azure HDInsight como destinos de proceso. También puede configurar los destinos de proceso según la disponibilidad de recursos. En concreto, después del escalado del clúster de Spark con más nodos de trabajo, puede usar los recursos con Machine Learning Workbench para acelerar las ejecuciones de los experimentos.
* Ejecute el seguimiento del historial. Puede usar Machine Learning Workbench para hacer el seguimiento del rendimiento de los modelos de aprendizaje automático y de otras métricas de interés.
* Ponga en marcha el modelo de aprendizaje automático. Puede usar las herramientas integradas en Machine Learning Workbench para implementar el modelo de aprendizaje de la máquina entrenada como un servicio web en Azure Container Service. También puede usar el servicio web para obtener predicciones en pequeños lotes mediante llamadas a la API de REST. 
* Compatibilidad con datos de terabytes.

> [!NOTE]
> Para consultar ejemplos de código y otros materiales relacionados con este ejemplo, vea [GitHub](https://github.com/Azure/MachineLearningSamples-BigData).
> 

## <a name="use-case-overview"></a>Información general del caso de uso

La previsión de la carga de trabajo en servidores es una necesidad empresarial común para las empresas de tecnología que administran su propia infraestructura. Para reducir los costos de infraestructura, los servicios que se ejecutan en servidores infrautilizados se deben agrupar para ejecutarse en un número más pequeño de máquinas. A los servicios que se ejecutan en servidores sobreutilizados se les debería ofrecer más máquinas para ejecutarse. 

En este escenario, se centra en la predicción de carga de trabajo para cada máquina (o servidor). En concreto, los datos de sesión de cada servidor se usan para predecir la clase de carga de trabajo del servidor en el futuro. Deberá clasificar la carga de cada servidor en clases baja, media y alta mediante el uso del clasificador de bosque aleatorio de [Apache Spark ML](https://spark.apache.org/docs/2.1.1/ml-guide.html). La técnicas de aprendizaje automático y el flujo de trabajo de este ejemplo se pueden extender con facilidad a otros problemas similares. 


## <a name="prerequisites"></a>Requisitos previos

Los requisitos previos para ejecutar este ejemplo son los siguientes:

* Una [cuenta de Azure](https://azure.microsoft.com/free/) (hay disponibles versiones gratuitas de prueba).
* Una copia instalada de [Azure Machine Learning Workbench](./overview-what-is-azure-ml.md). Para instalar el programa y crear un área de trabajo, consulte la [Guía de instalación rápida](./quickstart-installation.md). Si tiene varias suscripciones, puede [establecer la suscripción deseada para que sea la suscripción activa actual](https://docs.microsoft.com/cli/azure/account?view=azure-cli-latest#az_account_set).
* Windows 10 (las instrucciones que aparecen en este ejemplo suelen ser las mismas para los sistemas macOS).
* Una instancia de Data Science Virtual Machine (DSVM) para Linux (Ubuntu), preferiblemente en la región Este de EE. UU. donde se ubican los datos. Puede aprovisionar una DSVM Ubuntu siguiendo [estas instrucciones](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro). También puede ver [este tutorial de inicio rápido](https://ms.portal.azure.com/#create/microsoft-ads.linux-data-science-vm-ubuntulinuxdsvmubuntu). Se recomienda usar una máquina virtual con al menos 8 núcleos y 32 GB de memoria. 

Siga la [instrucción](https://docs.microsoft.com/azure/machine-learning/preview/known-issues-and-troubleshooting-guide#remove-vm-execution-error-no-tty-present) para habilitar el acceso de sudoer sin contraseña en la máquina virtual para AML Workbench.  Puede elegir utilizar la [autenticación basada en claves SSH para crear y utilizar la máquina virtual en AML Workbench](https://docs.microsoft.com/azure/machine-learning/preview/experimentation-service-configuration#using-ssh-key-based-authentication-for-creating-and-using-compute-targets). En este ejemplo, vamos a utilizar la contraseña para acceder a la máquina virtual.  Guarde la tabla siguiente con información de la DSVM para pasos posteriores:

 Nombre del campo| Valor |  
 |------------|------|
Dirección IP de la DSVM | xxx|
 Nombre de usuario  | xxx|
 Password   | xxx|


 Puede usar cualquier máquina virtual que tenga [Docker Engine](https://docs.docker.com/engine/) instalado.

* Un clúster de Spark de HDInsight con la plataforma de datos Hortonworks versión 3.6 y Spark versión 2.1.x, preferiblemente en la región Este de EE. UU. donde se localizan los datos. Visite [Creación de un clúster de Apache Spark en Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-provision-linux-clusters) para obtener información sobre cómo crear clústeres de HDInsight. Se recomienda usar un clúster de tres nodos de trabajo con 16 núcleos y 112 GB de memoria cada uno. O elija simplemente el tipo de máquina virtual `D12 V2` para el nodo principal y `D14 V2` para el nodo de trabajo. La implementación del clúster tarda unos 20 minutos. Se necesitan el nombre del clúster, el nombre de usuario de SSH y la contraseña para probar este ejemplo. Guarde la tabla siguiente con información del clúster de Azure HDInsight para pasos posteriores:

 Nombre del campo| Valor |  
 |------------|------|
 Nombre del clúster| xxx|
 Nombre de usuario  | xxx (sshuser de forma predeterminada)|
 Password   | xxx|


* Una cuenta de Azure Storage. Para crear una, siga [estas instrucciones](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account). Además, cree dos contenedores de blobs privados llamados `fullmodel` y `onemonthmodel` en esta cuenta de almacenamiento. La cuenta de almacenamiento se utiliza para guardar resultados intermedios de proceso y modelos de aprendizaje automático. Necesitará la clave de acceso y nombre de la cuenta de almacenamiento para probar este ejemplo. Guarde la tabla siguiente con información de la cuenta de Azure Storage para pasos posteriores:

 Nombre del campo| Valor |  
 |------------|------|
 Nombre de la cuenta de almacenamiento| xxx|
 Clave de acceso  | xxx|


La DSVM Ubuntu y el clúster de Azure HDInsight creado en la lista de requisitos previos son destinos de proceso. Los destinos de proceso son el recurso de proceso en el contexto de Machine Learning Workbench, que puede ser diferente del equipo donde se ejecuta Workbench.   

## <a name="create-a-new-workbench-project"></a>Creación de un nuevo proyecto de Workbench

Cree un proyecto usando este ejemplo como plantilla:
1.  Abra Machine Learning Workbench.
2.  En la página **Proyectos**, seleccione el signo **+** y **Nuevo proyecto**.
3.  En el panel **Crear nuevo proyecto**, rellene la información del proyecto nuevo.
4.  En el cuadro de búsqueda **Buscar plantillas de proyecto**, escriba **Workload Forecasting on Terabytes Data** (Previsión de carga de trabajo en terabytes de datos) y seleccione la plantilla.
5.  Seleccione **Crear**.

Puede crear un proyecto de Workbench con un repositorio de Git creado previamente siguiendo estas [instrucciones](./tutorial-classifying-iris-part-1.md).  
Ejecute `git status` para inspeccionar el estado de los archivos para el seguimiento de versiones.

## <a name="data-description"></a>Descripción de los datos

Los datos usados en este ejemplo son datos de carga de trabajo de servidor sintetizados. Se hospedan en una cuenta de Azure Blob Storage a la que se puede acceder públicamente en la región Este de EE. UU. La información de la cuenta de almacenamiento específica puede encontrarse en el campo `dataFile` de [`Config/storageconfig.json`](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Config/fulldata_storageconfig.json) en el formato "wasb://<BlobStorageContainerName>@<StorageAccountName>.blob.core.windows.net/<path>". Puede usar los datos directamente desde Blob Storage. Si muchos usuarios usan el almacenamiento al mismo tiempo, puede usar [azcopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-linux) para descargar los datos en su propio almacenamiento para una mejor experiencia de experimentación. 

El tamaño total de los datos es aproximadamente de 1 TB. Cada archivo ocupa aproximadamente de 1 a 3 GB y está en formato de archivo CSV sin encabezado. Cada fila de datos representa la carga de una transacción en un servidor determinado. La información detallada del esquema de datos es la siguiente:

Número de columna | Nombre del campo| type | DESCRIPCIÓN |  
|------------|------|-------------|---------------|
1  | `SessionStart` | DateTime |    Hora de inicio de sesión
2  |`SessionEnd`    | DateTime | Hora de fin de sesión
3 |`ConcurrentConnectionCounts` | Entero | Número de conexiones simultáneas
4 | `MbytesTransferred` | Doble | Datos normalizados transferidos en megabytes
5 | `ServiceGrade` | Entero |  Grado de servicio para la sesión
6 | `HTTP1` | Entero|  La sesión usa HTTP1 o HTTP2
7 |`ServerType` | Entero   |Tipo de servidor
8 |`SubService_1_Load` | Doble |   Carga subservicio 1
9 | `SubService_2_Load` | Doble |  Carga subservicio 2
10 | `SubService_3_Load` | Doble |     Carga subservicio 3
11 |`SubService_4_Load` | Doble |  Carga subservicio 4
12 | `SubService_5_Load`| Doble |      Carga subservicio 5
13 |`SecureBytes_Load`  | Doble | Carga de bytes seguros
14 |`TotalLoad` | Doble | Carga total en el servidor
15 |`ClientIP` | string|    Dirección IP de cliente
16 |`ServerIP` | string|    Dirección IP del servidor



En la tabla anterior se recogen los tipos de datos esperados. Puesto que faltan valores y hay problemas relacionados con datos modificados, no hay ninguna garantía de que los tipos de datos sean realmente los esperados. Hay que tener esto en cuenta para el procesamiento de datos. 


## <a name="scenario-structure"></a>Estructura del escenario

Los archivos de este ejemplo se organizan del siguiente modo.

| Nombre de archivo | type | DESCRIPCIÓN |
|-----------|------|-------------|
| `Code` | Carpeta | La carpeta contiene todo el código del ejemplo. |
| `Config` | Carpeta | La carpeta contiene los archivos de configuración. |
| `Image` | Carpeta | La carpeta se usa para guardar las imágenes para el archivo Léame. |
| `Model` | Carpeta | La carpeta se usa para guardar los archivos del modelo que se descargan desde Blob Storage. |
| `Code/etl.py` | Archivo Python | El archivo Python usado para la preparación de los datos y la ingeniería de características. |
| `Code/train.py` | Archivo Python | El archivo Python usado para entrenar un modelo multiclasificación de tres clases.  |
| `Code/webservice.py` | Archivo Python | El archivo Python usado para la operacionalización.  |
| `Code/scoring_webservice.py` | Archivo Python |  El archivo Python usado para la transformación de datos y para llamar al servicio web. |
| `Code/O16Npreprocessing.py` | Archivo Python | Archivo Python usado para preprocesar los datos para scoring_webservice.py.  |
| `Code/util.py` | Archivo Python | El archivo Python que contiene código para leer y escribir blobs de Azure.  
| `Config/storageconfig.json` | Archivo JSON | El archivo de configuración para el contenedor de blobs de Azure que almacena los resultados intermedios y el modelo para el procesamiento y entrenamiento sobre los datos de un mes. |
| `Config/fulldata_storageconfig.json` | Archivo JSON | El archivo de configuración para el contenedor de blobs de Azure que almacena los resultados intermedios y el modelo para el procesamiento y entrenamiento sobre el conjunto de datos completo.|
| `Config/webservice.json` | Archivo JSON | El archivo de configuración para scoring_webservice.py.|
| `Config/conda_dependencies.yml` | Archivo YAML | El archivo de dependencia de Conda. |
| `Config/conda_dependencies_webservice.yml` | Archivo YAML | El archivo de dependencia de Conda para el servicio web.|
| `Config/dsvm_spark_dependencies.yml` | Archivo YAML | El archivo de dependencia de Spark para la DSVM Ubuntu. |
| `Config/hdi_spark_dependencies.yml` | Archivo YAML | El archivo de dependencia de Spark para el clúster de Spark de HDInsight. |
| `README.md` | Archivo de marcado | El archivo Léame de Markdown. |
| `Code/download_model.py` | Archivo Python | El archivo Python que se usa para descargar los archivos del modelo desde Azure Blob al disco local. |
| `Docs/DownloadModelsFromBlob.md` | Archivo de marcado | El archivo Markdown que contiene instrucciones sobre cómo ejecutar `Code/download_model.py`. |



### <a name="data-flow"></a>flujo de datos

El código en [`Code/etl.py`](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Code/etl.py) carga datos desde el contenedor con acceso público (campo `dataFile` de [`Config/storageconfig.json`](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Config/fulldata_storageconfig.json)). Incluye la preparación de los datos y la ingeniería de características y guarda los resultados intermedios del proceso y los modelos en su propio contenedor privado. El código en [`Code/train.py`](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Code/train.py) carga los resultados intermedios de proceso desde el contenedor privado, entrena el modelo de clasificación multiclase y escribe el modelo de aprendizaje automático entrenado en el contenedor privado. 

Debe usar un contenedor para la experimentación en el conjunto de datos de un mes y otro para la experimentación en el conjunto de datos completo. Puesto que los datos y los modelos se guardan como archivo Parquet, cada archivo es realmente una carpeta en el contenedor, que contiene varios blobs. El contenedor resultante tiene el siguiente aspecto:

| Nombre de prefijo de blob | type | DESCRIPCIÓN |
|-----------|------|-------------|
| featureScaleModel | Parquet | El modelo de escala estándar para características numéricas. |
| stringIndexModel | Parquet | El modelo de indexador de cadena para características no numéricas.|
| oneHotEncoderModel|Parquet | El modelo de codificador "uno a n" para características de categorías. |
| mlModel | Parquet | El modelo de aprendizaje automático entrenado. |
| info| Archivo de inclusión Python | Información sobre los datos transformados, como el inicio del entrenamiento, el final del entrenamiento, la duración, la marca de tiempo para dividir la prueba del entrenamiento y las columnas para la indexación y la codificación "uno a n".

Todos los archivos y blobs de la tabla anterior se usan para la puesta en marcha.


### <a name="model-development"></a>Desarrollo del modelo

#### <a name="architecture-diagram"></a>Diagrama de la arquitectura


El siguiente diagrama muestra el flujo de trabajo de un extremo a otro del uso de Machine Learning Workbench para desarrollar el modelo: ![arquitectura](media/scenario-big-data/architecture.PNG).

En las siguientes secciones, se muestra el desarrollo de modelos mediante la funcionalidad de destino de proceso remoto de Machine Learning Workbench. Se carga primero una pequeña muestra de datos y se ejecuta el script [`Code/etl.py`](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Code/etl.py) en la DSVM Ubuntu para una iteración rápida. Se puede restringir aún más el trabajo a realizar en [`Code/etl.py`](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Code/etl.py) pasando un argumento adicional para una iteración más rápida. Finalmente, usamos un clúster de HDInsight para entrenar con el conjunto de datos completos.     

El archivo [`Code/etl.py`](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Code/etl.py) carga y prepara los datos, y realiza la ingeniería de características. Acepta dos argumentos:
* Un archivo de configuración para el contenedor de almacenamiento de blobs, para almacenar los modelos y los resultados intermedios de proceso.
* Un argumento de configuración de depuración para una iteración más rápida.

El primer argumento, `configFilename`, es un archivo de configuración local en el que se almacena la información de Blob Storage y se especifica dónde se deben cargar los datos. De forma predeterminada, es [`Config/storageconfig.json`](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Config/storageconfig.json) y se usará en la ejecución de los datos de un mes. También incluimos [`Config/fulldata_storageconfig.json`](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Config/fulldatastorageconfig.json), que debe usar en la ejecución con el conjunto de datos completo. El contenido de la configuración es el siguiente: 

| Campo | type | DESCRIPCIÓN |
|-----------|------|-------------|
| StorageAccount | string | Nombre de la cuenta de Azure Storage |
| storageContainer | string | Contenedor de la cuenta de Azure Storage para almacenar resultados intermedios |
| storageKey | string |Clave de acceso de la cuenta de Azure Storage |
| dataFile|string | Archivos de origen de datos  |
| duration| string | Duración de los datos de los archivos de origen de datos|

Modifique `Config/storageconfig.json` y `Config/fulldata_storageconfig.json` para configurar la cuenta de almacenamiento, la clave de almacenamiento y el contenedor de blobs para almacenar los resultados intermedios. De forma predeterminada, el contenedor de blobs para la ejecución con datos de un mes es `onemonthmodel` y el contenedor de blobs para la ejecución con el conjunto de datos completo es `fullmodel`. Asegúrese de crear estos dos contenedores en la cuenta de almacenamiento. El campo `dataFile` en [`Config/fulldata_storageconfig.json`](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Config/fulldatastorageconfig.json) configura qué datos se cargan en [`Code/etl.py`](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Code/etl.py). El campo `duration` configura el intervalo que incluyen los datos. Si la duración se establece en ONE_MONTH, los datos cargados deben ser un solo archivo CSV entre los siete archivos de datos de junio de 2016. Si la duración es FULL, se carga el conjunto de datos completo (1 TB). No es necesario cambiar `dataFile` y `duration` en estos dos archivos de configuración.

El segundo argumento es DEBUG (depuración). Si se establece en FILTER_IP, permite una iteración más rápida. El uso de este parámetro resulta útil para depurar el script.

> [!NOTE]
> En todos los comandos siguientes, reemplace cualquier argumento variable por su valor real.
> 


#### <a name="model-development-on-the-docker-of-ubuntu-dsvm"></a>Desarrollo del modelo en Docker de la instancia de DSVM Ubuntu

#####  <a name="1-set-up-the-compute-target"></a>1. Configuración del destino de proceso

Inicie la línea de comandos desde Machine Learning Workbench seleccionando **Archivo** > **Abrir símbolo del sistema**. A continuación, ejecute: 

```az ml computetarget attach remotedocker --name dockerdsvm --address $DSVMIPaddress  --username $user --password $password ```

Se crearán los dos archivos siguientes en la carpeta aml_config del proyecto:

-  dockerdsvm.compute: este archivo contiene la información de conexión y configuración para un destino de ejecución remoto.
-  dockerdsvm.runconfig: este archivo es un conjunto de opciones de ejecución usadas en la aplicación del área de trabajo.

Vaya a dockerdsvm.runconfig y cambie la configuración de estos campos por los siguientes:

    PrepareEnvironment: true 
    CondaDependenciesFile: Config/conda_dependencies.yml 
    SparkDependenciesFile: Config/dsvm_spark_dependencies.yml

Prepare el entorno del proyecto mediante la ejecución de:

```az ml experiment prepare -c dockerdsvm```


Con `PrepareEnvironment` establecido en true, Machine Learning Workbench crea el entorno en tiempo de ejecución cada vez que envíe un trabajo. `Config/conda_dependencies.yml` y `Config/dsvm_spark_dependencies.yml` contienen la personalización del entorno en tiempo de ejecución. Siempre puede modificar las dependencias de Conda, la configuración de Spark y las dependencias de Spark mediante la edición de estos dos archivos YAML. En este ejemplo, hemos agregado `azure-storage` y `azure-ml-api-sdk` como paquetes adicionales de Python en `Config/conda_dependencies.yml`. También hemos agregado `spark.default.parallelism`, `spark.executor.instances` y `spark.executor.cores` en `Config/dsvm_spark_dependencies.yml`. 

#####  <a name="2-data-preparation-and-feature-engineering-on-dsvm-docker"></a>2. Preparación de los datos e ingeniería de características en Docker de la DSVM

Ejecute el script `etl.py` en el Docker de la DSVM. Use un parámetro de depuración que filtre los datos cargados por direcciones IP de servidor específicas:

```az ml experiment submit -t dockerdsvm -c dockerdsvm ./Code/etl.py ./Config/storageconfig.json FILTER_IP```

Vaya al panel lateral y seleccione **Ejecutar** para ver el historial de ejecución de `etl.py`. Tenga en cuenta que el tiempo de ejecución es aproximadamente de dos minutos. Si piensa cambiar el código para incluir nuevas características, proporcione FILTER_IP como segundo argumento para obtener una iteración más rápida. Deberá ejecutar este paso varias veces cuando trabaje con sus propios problemas de aprendizaje automático para explorar el conjunto de datos o crear características. 

Con la restricción personalizada de los datos que se van a cargar y el filtrado de los datos que se van a procesar, puede acelerar el proceso de iteración en el desarrollo del modelo. Mientras experimenta, debe guardar periódicamente los cambios en el código en el repositorio de Git. Tenga en cuenta que se ha utilizado el siguiente código en `etl.py` para habilitar el acceso al contenedor privado:

```python
def attach_storage_container(spark, account, key):
    config = spark._sc._jsc.hadoopConfiguration()
    setting = "fs.azure.account.key." + account + ".blob.core.windows.net"
    if not config.get(setting):
        config.set(setting, key)

# attach the blob storage to the spark cluster or VM so that the storage can be accessed by the cluster or VM        
attach_storage_container(spark, storageAccount, storageKey)
```


Después, ejecute el script `etl.py` en el Docker de la DSVM sin el parámetro de depuración FILTER_IP:

```az ml experiment submit -t dockerdsvm -c dockerdsvm ./Code/etl.py ./Config/storageconfig.json FALSE```

Vaya al panel lateral y seleccione **Ejecutar** para ver el historial de ejecución de `etl.py`. Tenga en cuenta que el tiempo de ejecución es aproximadamente de cuatro minutos. El resultado procesado de este paso se guarda en el contenedor y se carga para el entrenamiento en train.py. Además, los indexadores de cadena, las canalizaciones de codificador y los escalados estándar se guardan en el contenedor privado y se usan en la puesta en marcha. 


##### <a name="3-model-training-on-dsvm-docker"></a>3. Entrenamiento del modelo en Docker de la DSVM

Ejecute el script `train.py` en Docker de la DSVM:

```az ml experiment submit -t dockerdsvm -c dockerdsvm ./Code/train.py ./Config/storageconfig.json```

Este paso carga los resultados intermedios de proceso de la ejecución de `etl.py` y entrena un modelo de aprendizaje automático. Este paso tarda aproximadamente dos minutos.

Una vez que ha finalizado correctamente la experimentación con los datos pequeños, puede continuar con la ejecución de la experimentación con el conjunto de datos completo. Puede empezar con el mismo código y después experimentar con cambios en el argumento y el destino de proceso.  

####  <a name="model-development-on-the-hdinsight-cluster"></a>Desarrollo del modelo en el clúster de HDInsight

##### <a name="1-create-the-compute-target-in-machine-learning-workbench-for-the-hdinsight-cluster"></a>1. Creación del destino de proceso en Machine Learning Workbench para el clúster de HDInsight

```az ml computetarget attach cluster --name myhdi --address $clustername-ssh.azurehdinsight.net --username $username --password $password```

Se crearán los dos archivos siguientes en la carpeta aml_config:
    
-  myhdi.compute: este archivo contiene la información de conexión y configuración para un destino de ejecución remoto.
-  myhdi.runconfig: este archivo es un conjunto de opciones de ejecución usadas en la aplicación del área de trabajo.


Vaya a myhdi.runconfig y cambie la configuración de estos campos por los siguientes:

    PrepareEnvironment: true 
    CondaDependenciesFile: Config/conda_dependencies.yml 
    SparkDependenciesFile: Config/hdi_spark_dependencies.yml

Prepare el entorno del proyecto mediante la ejecución de:

```az ml experiment prepare -c myhdi```

Este paso puede tardar hasta siete minutos.

##### <a name="2-data-preparation-and-feature-engineering-on-hdinsight-cluster"></a>2. Preparación de los datos e ingeniería de características en el clúster de HDInsight

Ejecute el script `etl.py` con los datos completos en el clúster de HDInsight:

```az ml experiment submit -a -t myhdi -c myhdi ./Code/etl.py Config/fulldata_storageconfig.json FALSE```

Puesto que este trabajo tarda un tiempo relativamente largo (aproximadamente dos horas), podemos usar `-a` para deshabilitar el streaming de salida. Cuando el trabajo esté terminado, en **Historial de ejecución** podrá ver el controlador y los registros de controlador. Si tiene un clúster mayor, siempre puede volver a configurar las opciones de `Config/hdi_spark_dependencies.yml` para usar más instancias o núcleos. Por ejemplo, si tiene un clúster de cuatro nodos de trabajo, puede aumentar el valor de `spark.executor.instances` de 5 a 7. Puede ver la salida de este paso en el contenedor **fullmodel** en la cuenta de almacenamiento. 


##### <a name="3-model-training-on-hdinsight-cluster"></a>3. Entrenamiento del modelo en el clúster de HDInsight

Ejecute el script `train.py` en el clúster de HDInsight:

```az ml experiment submit -a -t myhdi -c myhdi ./Code/train.py Config/fulldata_storageconfig.json```

Puesto que este trabajo tarda un tiempo relativamente largo (aproximadamente 30 minutos), podemos usar `-a` para deshabilitar el streaming de salida.

#### <a name="run-history-exploration"></a>Exploración del historial de ejecución

El Historial de ejecución es una característica que permite el seguimiento de la experimentación en Machine Learning Workbench. De forma predeterminada, realiza un seguimiento de la duración de la experimentación. En nuestro ejemplo concreto, cuando trabajamos con el conjunto de datos completo para `Code/etl.py` en la experimentación, observamos que la duración aumenta significativamente. También puede registrar métricas específicas con fines de seguimiento. Para habilitar el seguimiento de la métrica, agregue las siguientes líneas de código en el encabezado del archivo Python:
```python
# import logger
from azureml.logging import get_azureml_logger

# initialize logger
run_logger = get_azureml_logger()
```
Este es un ejemplo para realizar el seguimiento de una métrica específica:

```python
run_logger.log("Test Accuracy", testAccuracy)
```

En la barra lateral derecha de Workbench, vaya a **Ejecuciones** para ver el historial de ejecución de cada archivo Python. También puede ir al repositorio de GitHub. Se habrá creado una rama, con un nombre que comienza por "AMLHistory", para realizar el seguimiento de los cambios realizados en el script en cada ejecución. 


### <a name="operationalize-the-model"></a>Puesta en marcha del modelo

En esta sección, pondrá en marcha el modelo que ha creado en los pasos anteriores como un servicio web. También aprenderá a usar el servicio web para predecir la carga de trabajo. Use interfaces de la línea de comandos (CLI) para la puesta en marcha de Machine Learning para empaquetar el código y las dependencias como imágenes de Docker y publicar el modelo como un servicio web en contenedores.

Puede usar el símbolo de la línea de comandos en Machine Learning Workbench para ejecutar las CLI.  También puede ejecutar las CLI en Ubuntu Linux siguiendo la [Guía de instalación](https://github.com/Azure/Machine-Learning-Operationalization/blob/master/documentation/install-on-ubuntu-linux.md). 

> [!NOTE]
> En todos los comandos siguientes, reemplace cualquier argumento variable por su valor real. Esta sección tarda aproximadamente 40 minutos en realizarse.
> 

Elija una cadena única como el entorno para la puesta en marcha. En este caso, usamos la cadena "[unique]" para representar la cadena que elija.

1. Cree el entorno de puesta en marcha y cree el grupo de recursos.

        az ml env setup -c -n [unique] --location eastus2 --cluster -z 5 --yes

   Tenga en cuenta que puede usar Container Service como entorno al usar `--cluster` en el comando `az ml env setup`. Puede poner en marcha el modelo de aprendizaje automático en [Azure Container Service](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-intro-kubernetes). Usa [Kubernetes](https://kubernetes.io/) para automatizar la implementación, el escalado y la administración de aplicaciones en contenedor. Este comando tarda unos 20 minutos en ejecutarse. Use el comando siguiente para comprobar si la implementación ha finalizado correctamente: 

        az ml env show -g [unique]rg -n [unique]

   Establezca el entorno de implementación que acaba de crear mediante la ejecución del comando siguiente:

        az ml env set -g [unique]rg -n [unique]

2. Cree y use una cuenta de administración de modelos. Para crear una cuenta de administración de modelos, use el comando siguiente:

        az ml account modelmanagement create --location  eastus2 -n [unique]acc -g [unique]rg --sku-instances 4 --sku-name S3 

   Use la administración de modelos para la puesta en marcha mediante la ejecución del comando siguiente:

        az ml account modelmanagement set  -n [unique]acc -g [unique]rg  

   La cuenta de administración de modelos se usa para administrar los modelos y los servicios web. En Azure Portal, podrá ver que se ha creado una cuenta de administración de modelos. Puede ver los modelos, los manifiestos, las imágenes de Docker y los servicios que se han creado usando esta cuenta de administración de modelos.

3. Descargue y registre los modelos.

   Descargue los modelos en el contenedor **fullmodel** en el equipo local en el directorio de código. No descargue el archivo de datos en formato parquet con el nombre "vmlSource.parquet". No es un archivo de modelo; es un resultado de proceso intermedio. También puede reutilizar los archivos de modelo que hemos incluido en el repositorio de Git. Para obtener más información, consulte [GitHub](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Docs/DownloadModelsFromBlob.md). 

   Vaya a la carpeta `Model` en la CLI y registre los modelos con los siguientes comandos:

        az ml model register -m  mlModel -n vmlModel -t fullmodel
        az ml model register -m  featureScaleModel -n featureScaleModel -t fullmodel
        az ml model register -m  oneHotEncoderModel -n  oneHotEncoderModel -t fullmodel
        az ml model register -m  stringIndexModel -n stringIndexModel -t fullmodel
        az ml model register -m  info -n info -t fullmodel

   La salida de cada comando proporciona un identificador de modelo que se necesita en el paso siguiente. Guárdelos en un archivo de texto para un uso futuro.

4. Cree un manifiesto para el servicio web.

   Un manifiesto incluye el código del servicio web, todos los modelos de aprendizaje automático y las dependencias del entorno de tiempo de ejecución. Vaya a la carpeta `Code` en la CLI y ejecute el comando siguiente:

        az ml manifest create -n $webserviceName -f webservice.py -r spark-py -c ../Config/conda_dependencies_webservice.yml -i $modelID1 -i $modelID2 -i $modelID3 -i $modelID4 -i $modelID5

   La salida incluye un identificador de manifiesto para el paso siguiente. 

   Desde el directorio `Code`, puede probar webservice.py ejecutando el comando siguiente: 

        az ml experiment submit -t dockerdsvm -c dockerdsvm webservice.py

5. Cree una imagen de Docker. 

        az ml image create -n [unique]image --manifest-id $manifestID

   La salida incluye un identificador de imagen para el siguiente paso, ya que esta imagen de Docker se usa en Container Service. 

6. Implemente el servicio web en el clúster de Container Service.

        az ml service create realtime -n [unique] --image-id $imageID --cpu 0.5 --memory 2G

   La salida incluye un identificador de servicio. Lo necesitará para obtener la clave de autorización y la dirección URL del servicio.

7. Llame al servicio web en el código de Python para puntuar en lotes de pequeño tamaño.

   Use el comando siguiente para obtener la clave de autorización:

         az ml service keys realtime -i $ServiceID 

   Use el comando siguiente para obtener la dirección URL del servicio de puntuación:

        az ml service usage realtime -i $ServiceID

   Modifique el contenido de `./Config/webservice.json` con la dirección URL del servicio de puntuación y la clave de autorización adecuadas. Conserve el "Portador" en el archivo original y reemplace la parte "xxx". 
   
   Vaya al directorio raíz del proyecto y pruebe el servicio web de puntuación para pequeños lotes usando el comando siguiente:

        az ml experiment submit -t dockerdsvm -c dockerdsvm ./Code/scoring_webservice.py ./Config/webservice.json

8. Escale el servicio web. 

   Para obtener más información, consulte [Cómo escalar la puesta en marcha en el clúster de Azure Container Service](https://github.com/Azure/Machine-Learning-Operationalization/blob/master/documentation/how-to-scale.md).
 

## <a name="next-steps"></a>pasos siguientes

Este ejemplo describe cómo usar Machine Learning Workbench para entrenar un modelo de aprendizaje automático sobre macrodatos y poner en marcha el modelo entrenado. En concreto, ha aprendido a configurar y usar destinos de proceso diferentes, a ejecutar el historial de métricas de seguimiento y a usar distintas ejecuciones.

Puede extender el código para explorar la validación cruzada y el ajuste de hiperparámetros. Para obtener más información sobre la validación cruzada y el ajuste de hiperparámetros, consulte [este recurso de GitHub](https://github.com/Azure/MachineLearningSamples-DistributedHyperParameterTuning).  

Para obtener más información sobre la previsión de series temporales, consulte [este recurso de GitHub](https://github.com/Azure/MachineLearningSamples-EnergyDemandTimeSeriesForecasting).
