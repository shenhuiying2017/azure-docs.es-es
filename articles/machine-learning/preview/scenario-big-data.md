---
title: "Previsión de carga de trabajo del servidor en terabytes de datos: Azure | Microsoft Docs"
description: "Cómo entrenar un modelo de aprendizaje automático sobre macrodatos con Azure ML Workbench."
services: machine-learning
documentationcenter: 
author: daden
manager: mithal
editor: daden
ms.assetid: 
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/15/2017
ms.author: daden
ms.openlocfilehash: b76253fad43be231591023c4d4466bf6e3f329a0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="server-workload-forecasting-on-terabytes-data"></a>Previsión de carga de trabajo del servidor en terabytes de datos

Este ejemplo muestra cómo pueden usar los científicos de datos Azure ML Workbench para desarrollar soluciones que requieren el uso de macrodatos. Se muestra cómo un usuario, mediante el uso de Azure ML Workbench, puede seguir una ruta que comienza a partir de una muestra de un conjunto de datos de gran tamaño, realiza la preparación de los datos en iteraciones, lleva a cabo ingeniería y aprendizaje automático y, eventualmente, extiende el proceso al conjunto completo de datos de gran tamaño. 

Al hacerlo, se muestran las siguientes funcionalidades claves de Azure ML Workbench:
* Cambiar fácilmente entre destinos de proceso: mostramos cómo el usuario puede configurar diferentes destinos de proceso y usarlos en experimentación. En este ejemplo, utilizamos una DSVM Ubuntu y un clúster de HDInsight como destinos de proceso. También se muestra al usuario cómo configurar los destinos de proceso según la disponibilidad de recursos. En concreto, después del escalado horizontal del clúster de Spark (es decir, incluir más nodos de trabajo en el clúster de Spark), se muestra cómo puede el usuario utilizar los recursos con Azure ML Workbench para acelerar las ejecuciones de los experimentos.
* Seguimiento del historial de ejecución: se muestran al usuario cómo usar Azure ML Workbench para realizar el seguimiento del rendimiento de los modelos de aprendizaje automático y otras métricas de interés.
* Operacionalización del modelo de aprendizaje automático: se muestra el uso de las herramientas integradas en Azure ML Workbench para implementar el modelo de aprendizaje automático entrenado como un servicio web en Azure Container Service (ACS). También se muestra cómo utilizar el servicio web para obtener predicciones en pequeños lotes mediante llamadas a la API de REST. 
* Compatibilidad con datos de terabytes.



## <a name="link-to-the-gallery-github-repository"></a>Vínculo al repositorio de GitHub de la galería

El repositorio de GitHub público para este ejemplo contiene todos los materiales, incluidos ejemplos de código: 
 
[https://github.com/Azure/MachineLearningSamples-BigData](https://github.com/Azure/MachineLearningSamples-BigData)



## <a name="use-case-overview"></a>Información general del caso de uso


La previsión de la carga de trabajo en servidores es una necesidad empresarial común para las empresas de tecnología que administran su propia infraestructura. Para reducir los costos de infraestructura, los servicios que se ejecutan en servidores infrautilizados deben agruparse para ejecutarse en un menor número de equipos y los servicios que se ejecutan en servidores con una carga intensa deberían contar con más máquinas para su ejecución. Este escenario se centra en la predicción de carga de trabajo para cada máquina (o servidor). En concreto, los datos de sesión de cada servidor se utilizan para predecir la clase de carga de trabajo del servidor en el futuro. Se clasifica la carga de cada servidor en clases baja, media y alta mediante el uso del clasificador de bosque aleatorio de [Apache Spark ML](https://spark.apache.org/docs/2.1.1/ml-guide.html). La técnicas de aprendizaje automático y el flujo de trabajo de este ejemplo se pueden extender con facilidad a otros problemas similares. 


## <a name="prerequisites"></a>Requisitos previos

Los requisitos previos para ejecutar este ejemplo son los siguientes:

* Una [cuenta de Azure](https://azure.microsoft.com/free/) (hay disponibles versiones gratuitas de prueba).
* Una copia instalada de [Azure Machine Learning Workbench](./overview-what-is-azure-ml.md) siguiendo la [Guía de instalación de inicio rápido](./quickstart-installation.md) para instalar el programa y crear un área de trabajo.
* En este escenario se supone que está ejecutando Azure Machine Learning (ML) Workbench en Windows 10. Si usa macOS, las instrucciones son en gran medida las mismas.
* Una instancia de Data Science Virtual Machine (DSVM) para Linux (Ubuntu). Puede aprovisionar una DSVM Ubuntu siguiendo estas [instrucciones](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-provision-vm). Haga clic [aquí](https://ms.portal.azure.com/#create/microsoft-ads.linux-data-science-vm-ubuntulinuxdsvmubuntu) para una guía de inicio rápido. Se recomienda usar una máquina virtual con al menos 8 núcleos y 32 GB de memoria.  Se necesitan la dirección IP de la DSVM, el nombre de usuario y la contraseña para probar este ejemplo. Guarde la tabla siguiente con información de la DSVM para pasos posteriores:

 Nombre del campo| Valor |  
 |------------|------|
Dirección IP de la DSVM | xxx|
 Nombre de usuario  | xxx|
 Password   | xxx|

 Se puede usar cualquier máquina virtual (VM) con [Docker Engine](https://docs.docker.com/engine/) instalado.

* Un clúster de Spark de HDInsight con HDP versión 3.6 y Spark 2.1.x. Consulte [Creación de un clúster de Apache Spark en Azure HDInsight] (https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-jupyter-spark-sql) para más información sobre cómo crear clústeres de HDInsight. Se recomienda usar un clúster de tres nodos de trabajo con 16 núcleos y 112 GB de memoria cada uno. O elija simplemente el tipo de máquina virtual "`D12 V2`" para el nodo principal y "`D14 V2`" para el nodo de trabajo. La implementación del clúster tarda unos 20 minutos. Se necesitan el nombre del clúster, el nombre de usuario de SSH y la contraseña para probar este ejemplo. Guarde la tabla siguiente con información del clúster de Azure HDInsight para pasos posteriores:

 Nombre del campo| Valor |  
 |------------|------|
 Nombre del clúster| xxx|
 Nombre de usuario  | xxx (de forma predeterminada, es sshuser)|
 Password   | xxx|


* Una cuenta de almacenamiento de Azure. Puede seguir estas [instrucciones](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account) para crear una cuenta de Azure Storage. Además, cree dos contenedores de Blob privados llamados "`fullmodel`" y "`onemonthmodel`" en esta cuenta de almacenamiento. La cuenta de almacenamiento se utiliza para guardar resultados intermedios de proceso y modelos de aprendizaje automático. Necesitará la clave de acceso y nombre de la cuenta de almacenamiento para probar este ejemplo. Guarde la tabla siguiente con información de la cuenta de Azure Storage para pasos posteriores:

 Nombre del campo| Valor |  
 |------------|------|
 Nombre de la cuenta de almacenamiento| xxx|
 Clave de acceso  | xxx|


La DSVM Ubuntu y el clúster de Azure HDInsight creado en la lista de requisitos previos son destinos de proceso. Los destinos de proceso son el recurso de proceso en el contexto de Azure ML Workbench, que puede ser diferente del equipo donde se ejecuta Azure ML Workbench.   

## <a name="create-a-new-workbench-project"></a>Creación de un nuevo proyecto de Workbench

Cree un nuevo proyecto usando este ejemplo como plantilla:
1.  Abra Azure Machine Learning Workbench
2.  En la página **Proyectos**, haga clic en el signo **+** y seleccione **Nuevo proyecto**
3.  En el panel **Crear nuevo proyecto**, rellene la información del proyecto nuevo
4.  En el cuadro de búsqueda **Buscar plantillas de proyecto**, escriba "Workload Forecasting on Terabytes Data" (Previsión de carga de trabajo en terabytes de datos) y seleccione la plantilla
5.  Haga clic en **Crear**

Puede crear un proyecto de Azure ML Workbench con un repositorio de Git creado previamente siguiendo estas [instrucciones](./tutorial-classifying-iris-part-1.md).  
Ejecute git status (estado de git) para inspeccionar el estado de los archivos para seguimiento de versiones.

## <a name="data-description"></a>Descripción de los datos

Los datos utilizados en el escenario son datos de carga de trabajo del servidor sintetizados y se hospedan en una cuenta de Azure Blob Storage que es accesible públicamente. La información de la cuenta de almacenamiento específica puede encontrarse en el campo `dataFile` de [`Config/storageconfig.json`](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Config/fulldata_storageconfig.json). Puede usar los datos directamente desde Azure Blob Storage. En caso de que el almacenamiento sea utilizado por muchos usuarios al mismo tiempo, puede optar por usar [azcopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-linux) para descargar los datos en su propio almacenamiento. 

El tamaño total de los datos es aproximadamente de 1 TB. Cada archivo ocupa aproximadamente de 1 a 3 GB y está en formato de archivo CSV sin encabezado. Cada fila de datos representa la carga de una transacción en un servidor determinado.  La información detallada del esquema de datos es la siguiente:

Número de columna | Nombre del campo| Tipo | Descripción |  
|------------|------|-------------|---------------|
1  | `SessionStart` | Datetime |    Hora de inicio de sesión
2  |`SessionEnd`    | Datetime | Hora de fin de sesión
3 |`ConcurrentConnectionCounts` | Entero | Número de conexiones simultáneas
4 | `MbytesTransferred` | Double | Datos normalizados transferidos en Megabytes
5 | `ServiceGrade` | Entero |  Grado de servicio para la sesión
6 | `HTTP1` | Entero|  Indica si la sesión utiliza HTTP1 o HTTP2
7 |`ServerType` | Entero   |Tipo de servidor
8 |`SubService_1_Load` | Double |   Carga subservicio 1
9 | `SubService_1_Load` | Double |  Carga subservicio 2
10 | `SubService_1_Load` | Double |     Carga subservicio 3
11 |`SubService_1_Load` | Double |  Carga subservicio 4
12 | `SubService_1_Load`| Double |      Carga subservicio 5
13 |`SecureBytes_Load`  | Double | Carga de bytes seguros
14 |`TotalLoad` | Double | Carga total en el servidor
15 |`ClientIP` | String|    Dirección IP de cliente
16 |`ServerIP` | String|    Dirección IP del servidor



Tenga en cuenta que aunque se enumeran los tipos de datos esperados en la tabla anterior, debido a valores que faltan y problemas de datos con errores, no hay ninguna garantía de que el tipos de datos sea el esperado y el procesamiento de los datos debe tener esto en consideración. 


## <a name="scenario-structure"></a>Estructura del escenario

Los archivos de este ejemplo se organizan del siguiente modo.

| Nombre de archivo | Tipo | Descripción |
|-----------|------|-------------|
| `Code` | Carpeta | La carpeta contiene todo el código del ejemplo |
| `Config` | Carpeta | La carpeta contiene los archivos de configuración |
| `Image` | Carpeta | La carpeta se utiliza para guardar las imágenes para el archivo Léame |
| `Model` | Carpeta | La carpeta se utiliza para guardar los archivos del modelo que se descargan desde Azure Blob Storage |
| `Code/etl.py` | Archivo Python | Archivo Python utilizado para la preparación de los datos y la ingeniería de características |
| `Code/train.py` | Archivo Python | Archivo Python utilizado para entrenar un modelo multi-clasificación de tres clases  |
| `Code/webservice.py` | Archivo Python | Archivo Python utilizado para la operacionalización  |
| `Code/scoring_webservice.py` | Archivo Python |  Archivo Python usado para la transformación de datos y llamar al servicio web |
| `Code/O16Npreprocessing.py` | Archivo Python | Archivo Python usado para preprocesar los datos para scoring_webservice.py.  |
| `Code/util.py` | Archivo Python | Archivo Python que contiene código para leer y escribir blobs de Azure.  
| `Config/storageconfig.json` | Archivo JSON | Archivo de configuración para el contenedor de blobs de Azure que almacena los resultados intermedios y el modelo para el procesamiento y entrenamiento sobre los datos de un mes |
| `Config/fulldata_storageconfig.json` | Archivo JSON |  Archivo de configuración para el contenedor de blobs de Azure que almacena los resultados intermedios y el modelo para el procesamiento y entrenamiento sobre el conjunto de datos completo|
| `Config/webservice.json` | Archivo JSON | Archivo de configuración para scoring_webservice.py|
| `Config/conda_dependencies.yml` | Archivo YAML | Archivo de dependencia de Conda |
| `Config/conda_dependencies_webservice.yml` | Archivo YAML | Archivo de dependencia de Conda para el servicio web|
| `Config/dsvm_spark_dependencies.yml` | Archivo YAML | Archivo de dependencia de Spark para la DSVM Ubuntu |
| `Config/hdi_spark_dependencies.yml` | Archivo YAML | Archivo de dependencia de Spark para el clúster de Spark de HDInsight |
| `README.md` | Archivo de marcado | Archivo de marcado Léame |
| `Code/download_model.py` | Archivo Python | Archivo Python que se utiliza para descargar los archivos del modelo desde Azure Blob al disco local |
| `Docs/DownloadModelsFromBlob.md` | Archivo de marcado | Archivo de marcado que contiene la instrucción para la ejecución de `Code/download_model.py` |



### <a name="data-flow"></a>flujo de datos

El código en [`Code/etl.py`](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Code/etl.py) carga datos desde el contenedor con acceso público (campo `dataFile` de [`Config/storageconfig.json`](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Config/fulldata_storageconfig.json)). Incluye la preparación de los datos y la ingeniería de características y guarda los resultados intermedios del proceso y los modelos en su propio contenedor privado. El código en [`Code/train.py`](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Code/train.py) carga los resultados intermedios de proceso desde el contenedor privado, entrena el modelo de clasificación multiclase y, por último, escribe el modelo de aprendizaje automático entrenado en el contenedor privado. Se recomienda que el usuario utilice un contenedor para la experimentación en el conjunto de datos de un mes y, a continuación, otro para la experimentación en el conjunto de datos completo. Puesto que los datos y los modelos se guardan como archivo Parquet, cada archivo es realmente una carpeta en el contenedor, que contiene varios blobs. El contenedor resultante tiene el siguiente aspecto:

| Nombre de prefijo de BLOB | Tipo | Descripción |
|-----------|------|-------------|
| featureScaleModel | Parquet | Modelo de escala estándar para características numéricas |
| stringIndexModel | Parquet | Modelo de indexador de cadena para características no numéricas|
| oneHotEncoderModel|Parquet | Modelo de codificador "uno a n" para características de categorías |
| mlModel | Parquet | Modelo de aprendizaje automático entrenado |
| info| Archivo de inclusión Python | Información acerca de los datos transformados, como el inicio del entrenamiento, el final del entrenamiento, la duración, la marca de tiempo para dividir la prueba del entrenamiento y las columnas para la indexación y la codificación "uno a n".

Todos los archivos y blobs de la tabla anterior se usan para la operacionalización.


### <a name="model-development"></a>Desarrollo del modelo

#### <a name="architecture-diagram"></a>Diagrama de la arquitectura


El siguiente diagrama muestra el flujo de trabajo de un extremo a otro del uso de Azure ML Workbench para desarrollar el modelo: ![arquitectura](media/scenario-big-data/architecture.PNG)



En la siguiente tabla, se muestra el desarrollo de modelos mediante la funcionalidad de destino de proceso remoto de Azure ML workbench. Se cargar primero una pequeña muestra datos y se ejecuta el script [`Code/etl.py`](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Code/etl.py) en la DSVM Ubuntu para una iteración rápida. Se puede restringir aún más el trabajo a realizar en [`Code/etl.py`](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Code/etl.py) pasando un argumento adicional para una iteración más rápida. Finalmente, usamos un clúster de HDInsight para entrenar con el conjunto de datos completos.     

El archivo [`Code/etl.py`](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Code/etl.py) realiza la carga de datos, la preparación de los datos y la ingeniería de características. Acepta dos argumentos: (1) un archivo de configuración para el contenedor de Azure Blob Storage para almacenar los resultados intermedios de proceso y los modelos, (2) configuración de depuración para iteración rápida.

El primer argumento, `configFilename`, es un archivo de configuración local en el que se almacena la información de Azure Blob Storage y se especifica dónde se deben cargar los datos. De forma predeterminada, es [`Config/storageconfig.json`](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Config/storageconfig.json) y se utilizará en la ejecución con los datos de mes. También incluimos [`Config/fulldata_storageconfig.json`](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Config/fulldatastorageconfig.json), que debe usar en la ejecución con el conjunto de datos completo. El contenido de la configuración es el siguiente: 

| Campo | Tipo | Descripción |
|-----------|------|-------------|
| StorageAccount | String | Nombre de la cuenta de Azure Storage |
| storageContainer | String | Contenedor de la cuenta de Azure Storage para almacenar resultados intermedios |
| storageKey | String |Clave de acceso de la cuenta de Azure Storage |
| dataFile|String | Archivos de origen de datos  |
| duration| String | Duración de los datos de los archivos de origen de datos|

Modifique `Config/storageconfig.json` y `Config/fulldata_storageconfig.json` para configurar la cuenta de almacenamiento, la clave de almacenamiento y el contenedor de blobs para almacenar los resultados intermedios. De forma predeterminada, el contenedor de blobs para la ejecución con datos de un mes es "`onemonthmodel`" y el contenedor de blobs para la ejecución con el conjunto de datos completo es "`fullmodel`." Asegúrese de crear estos dos contenedores en la cuenta de almacenamiento. El campo `"dataFile"` en [`Config/fulldata_storageconfig.json`](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Config/fulldatastorageconfig.json) configura qué datos se cargan en [`Code/etl.py`](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Code/etl.py) y `"duration"` configura el intervalo que incluyen los datos. Si la duración se establece en 'ONE_MONTH', los datos cargados deben ser un solo archivo CSV entre los siete archivos de datos de junio de 2016. Si la duración es 'FULL', se carga el conjunto de datos completo, que ocupa 1 TB. No es necesario cambiar `"dataFile"` y `"duration"` en estos dos archivos de configuración.

El segundo argumento es DEBUG (depuración). Si se establece en 'FILTER_IP', permite una iteración más rápida. El uso de este parámetro resulta útil para depurar el script.

> [!NOTE]
> Reemplace cualquier argumento variable en todos los comandos siguientes por su valor real.
> 


#### <a name="model-development-on-the-docker-of-ubuntu-dsvm"></a>Desarrollo del modelo en Docker de la instancia de DSVM Ubuntu

#####  <a name="1-setting-up-the-compute-target-for-docker-on-ubuntu-dsvm"></a>1. Configurar el destino de proceso para Docker en la DSVM Ubuntu

Para iniciar la línea de comandos desde Azure ML Workbench, haga clic en el menú "Archivo" en la esquina superior izquierda de Azure ML Workbench, elija "Abrir el símbolo del sistema" y, a continuación, ejecute 

```az ml computetarget attach --name dockerdsvm --address $DSVMIPaddress  --username $user --password $password --type remotedocker```

Una vez finalizada correctamente la ejecución de la línea de comandos, verá los dos archivos siguientes creados en la carpeta aml_config del proyecto:

    dockerdsvm.compute: contains the connection and configuration information for a remote execution target
    dockerdsvm.runconfig: set of run options used when executing within the Azure ML Workbench application

Vaya a dockerdsvm.runconfig y cambie la configuración de los siguientes campos, tal y como se muestra a continuación:

    PrepareEnvironment: true 
    CondaDependenciesFile: Config/conda_dependencies.yml 
    SparkDependenciesFile: Config/dsvm_spark_dependencies.yml

Prepare el entorno del proyecto mediante la ejecución de:

```az ml experiment prepare -c dockerdsvm```


Al establecer "PrepareEnvironment" en true, permite a Azure ML Workbench crear el entorno en tiempo de ejecución cada vez que envíe un trabajo. `Config/conda_dependencies.yml` y `Config/dsvm_spark_dependencies.yml` contienen la personalización del entorno en tiempo de ejecución. Siempre puede modificar las dependencias de Conda, la configuración de Spark y las dependencias de Spark mediante la edición de estos dos archivos YAML. En este ejemplo, hemos agregado `azure-storage` y `azure-ml-api-sdk` como paquetes adicionales de Python en `Config/conda_dependencies.yml` y se agregan "`spark.default.parallelism`", "`spark.executor.instances`" y "`spark.executor.cores`", etc. en `Config/dsvm_spark_dependencies.yml`. 

#####  <a name="2-data-preparation-and-feature-engineering-on-dsvm-docker"></a>2. Preparación de los datos e ingeniería de características en Docker de la DSVM

Ejecute el script `etl.py` en Docker de la DSVM con el parámetro de depuración que filtra los datos cargados por direcciones IP de servidor específicas:

```az ml experiment submit -t dockerdsvm -c dockerdsvm ./Code/etl.py ./Config/storageconfig.json FILTER_IP```

Vaya al panel lateral y haga clic en "Ejecutar" para ver el historial de ejecución de `etl.py`. Tenga en cuenta que el tiempo de ejecución es aproximadamente de dos minutos. Si piensa cambiar el código para incluir nuevas características, proporcione FILTER_IP como segundo argumento para obtener una iteración más rápida. Deberá ejecutar este paso varias veces cuando trabaje con sus propios problemas de aprendizaje automático para explorar el conjunto de datos o crear nuevas características. Con la restricción personalizada de los datos que se van a cargar y el filtrado de los datos que se van a procesar, puede acelerar el proceso de iteración en el desarrollo del modelo. Mientras experimenta, debe guardar periódicamente los cambios en el código en el repositorio de git.  Tenga en cuenta que se ha utilizado el siguiente código en `etl.py` para habilitar el acceso al contenedor privado:

```python
def attach_storage_container(spark, account, key):
    config = spark._sc._jsc.hadoopConfiguration()
    setting = "fs.azure.account.key." + account + ".blob.core.windows.net"
    if not config.get(setting):
        config.set(setting, key)

# attach the blob storage to the spark cluster or VM so that the storage can be accessed by the cluster or VM        
attach_storage_container(spark, storageAccount, storageKey)
```


A continuación, ejecute el script `etl.py` en Docker de la DSVM sin el parámetro de depuración FILTER_IP

```az ml experiment submit -t dockerdsvm -c dockerdsvm ./Code/etl.py ./Config/storageconfig.json FALSE```

Vaya al panel lateral y haga clic en "Ejecutar" para ver el historial de ejecución de `etl.py`. Tenga en cuenta que el tiempo de ejecución es aproximadamente de cuatro minutos. El resultado procesado de este paso se guarda en el contenedor y se carga para el entrenamiento en train.py. Además, los indexadores de cadena, las canalizaciones de codificador y los escalados estándar también se guardan en el contenedor privado y se usan en la operacionalización (O16N). 


##### <a name="3-model-training-on-dsvm-docker"></a>3. Entrenamiento del modelo en Docker de la DSVM

Ejecute el script `train.py` en Docker de la DSVM:

```az ml experiment submit -t dockerdsvm -c dockerdsvm ./Code/train.py ./Config/storageconfig.json```

Este paso carga los resultados intermedios de proceso de la ejecución de `etl.py` y entrena un modelo de aprendizaje automático. Este paso tarda aproximadamente dos minutos.

Una vez que ha finalizado correctamente la experimentación con los datos pequeños, puede continuar con la ejecución de la experimentación con el conjunto de datos completo. Puede empezar con el mismo código y, a continuación, experimentar con cambios en el argumento y el destino de proceso.  

####  <a name="model-development-on-the-hdinsight-cluster"></a>Desarrollo del modelo en el clúster de HDInsight

##### <a name="1-create-compute-target-in-azure-ml-workbench-for-the-hdinsight-cluster"></a>1. Creación del destino de proceso en Azure ML Workbench para el clúster de HDInsight

```az ml computetarget attach --name myhdi --address $clustername-ssh.azurehdinsight.net --username $username --password $password --type cluster```

Una vez finalizada correctamente la ejecución de la línea de comandos, verá los dos archivos siguientes creados en la carpeta aml_config:
    
    myhdo.compute: contains connection and configuration information for a remote execution target
    myhdi.runconfig: set of run options used when executing within the Azure ML Workbench application


Vaya a myhdi.runconfig y cambie la configuración de los siguientes campos, tal y como se muestra a continuación:

    PrepareEnvironment: true 
    CondaDependenciesFile: Config/conda_dependencies.yml 
    SparkDependenciesFile: Config/hdi_spark_dependencies.yml

Prepare el entorno del proyecto mediante la ejecución de:

```az ml experiment prepare -c myhdi```

Este paso puede tardar hasta siete minutos.

##### <a name="2-data-preparation-and-feature-engineering-on-hdinsight-cluster"></a>2. Preparación de los datos e ingeniería de características en el clúster de HDInsight

Ejecute el script `etl.py` con los datos completos en el clúster de HDInsight

```az ml experiment submit -a -t myhdi -c myhdi ./Code/etl.py Config/fulldata_storageconfig.json FALSE```

Puesto que este trabajo tarda un tiempo relativamente largo (aproximadamente dos horas), podemos usar "-a" para deshabilitar el streaming de salida. Una vez que se realiza el trabajo, en el "Historial de ejecución", puede buscar en los registros de controladores. Si tiene un clúster mayor, siempre puede volver a configurar las configuraciones de `Config/hdi_spark_dependencies.yml` para usar varias instancias o más núcleos. Por ejemplo, si tiene un clúster de cuatro nodos de trabajo, puede aumentar el valor de "`spark.executor.instances`" de 5 a 7. Puede ver la salida de este paso en el contenedor "fullmodel" en la cuenta de almacenamiento. 


##### <a name="3-model-training-on-hdinsight-cluster"></a>3. Entrenamiento del modelo en el clúster de HDInsight

Ejecute el script `train.py` en el clúster de HDInsight:

```az ml experiment submit -a -t myhdi -c myhdi ./Code/train.py Config/fulldata_storageconfig.json```

Puesto que este trabajo tarda un tiempo relativamente largo (aproximadamente media hora), podemos usar "-a" para deshabilitar el streaming de salida.

#### <a name="run-history-exploration"></a>Exploración del historial de ejecución

El Historial de ejecución es una característica que permite el seguimiento de la experimentación en Azure ML Workbench. De forma predeterminada, realiza un seguimiento de la duración de la experimentación. En nuestro ejemplo concreto, cuando trabajamos con el conjunto de datos completo para "`Code/etl.py`" en la experimentación, observamos que la duración aumenta significativamente. También puede registrar métricas específicas con fines de seguimiento. Para habilitar el seguimiento de la métrica, agregue las siguientes líneas de código en el encabezado del archivo Python:
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

Vaya a "Ejecuciones" en la barra lateral derecha de Azure ML Workbench para ver el historial de ejecución de cada archivo Python. Además, vaya al repositorio de github y se habrá creado una nueva bifurcación con un nombre que comienza por "AMLHistory" para realizar el seguimiento de los cambios realizados en el script en cada ejecución. 


### <a name="operationalization"></a>Operacionalización

En esta sección, se operacionalizaliza el modelo que hemos creado en los pasos anteriores como un servicio web y se muestra cómo podemos usar el servicio web para predecir la carga de trabajo. Se usan interfaces de línea de comandos (CLI) de operacionalización de Azure ML para empaquetar el código y las dependencias como imágenes de Docker y publicar el modelo como un servicio web en contenedores. Puede consultar la [Introducción a la operacionalización](https://github.com/Azure/Machine-Learning-Operationalization/blob/master/documentation/operationalization-overview.md) para más detalles. Puede utilizar el símbolo del sistema de línea de comandos en Azure ML Workbench para ejecutar las CLI de operacionalización de Azure ML.  También puede ejecutar las CLI de operacionalización de Azure ML en Ubuntu Linux siguiendo la [Guía de instalación](https://github.com/Azure/Machine-Learning-Operationalization/blob/master/documentation/install-on-ubuntu-linux.md). 

> [!NOTE]
> Reemplace cualquier argumento variable en todos los comandos siguientes por su valor real. Esta sección tarda aproximadamente 40 minutos en realizarse.
> 


Elija una cadena única como el entorno de operacionalización y usaremos la cadena "[unique]" para representar la cadena que elija.

1. Cree el entorno de operacionalización y cree el grupo de recursos.

        az ml env setup -c -n [unique] --location eastus2 --cluster -z 5 --yes

   Observe que elegimos usar Azure Container Service como entorno mediante el uso de `--cluster` en el comando `az ml env setup`. Elegimos la operacionalización del modelo de aprendizaje automático en [Azure Container Service](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-intro-kubernetes) porque usa [Kubernetes](https://kubernetes.io/) para automatizar la implementación, el escalado y la administración de aplicaciones en contenedores. Este comando tarda aproximadamente 20 minutos en ejecutarse. Uso 

        az ml env show -g [unique]rg -n [unique]

   para comprobar si la implementación ha finalizado correctamente.

   Establezca el entorno de implementación que acaba de crear mediante la ejecución de

        az ml env set -g [unique]rg -n [unique]

2. Cree una cuenta de administración del modelo y use la cuenta de administración del modelo.

   Cree de una cuenta de administración del modelo mediante la ejecución de

    az ml account modelmanagement create --location eastus2 -n [unique]acc -g [unique]rg --sku-instances 4 --sku-name S3 

   Utilice la cuenta de administración del modelo para la operacionalización mediante la ejecución de

        az ml account modelmanagement set  -n [unique]acc -g [unique]rg  

   La cuenta de administración del modelo se usa para administrar los modelos y los servicios web. Desde Azure Portal, puede ver que se ha creado una nueva cuenta de administración del modelo y se puede usar para ver los modelos, manifiestos, imágenes de Docker y servicios que se crean mediante el uso de esta cuenta de administración del modelo.

3. Descargue y registre los modelos.

   Descargue los modelos en el contenedor "fullmodel" en el equipo local en el directorio de código. No descargue el archivo de datos Parquet llamado "vmlSource.parquet" ya que no es un archivo de modelo sino un resultado intermedio de proceso. También puede reutilizar los archivos de modelo que hemos incluido en el repositorio de git. Visite [DownloadModelsFromBlob.md](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Docs/DownloadModelsFromBlob.md) para más información sobre la descarga de los archivos Parquet. 

   Vaya a la carpeta `Model` en la CLI y registre los modelos con los siguientes comandos:

        az ml model register -m  mlModel -n vmlModel -t fullmodel
        az ml model register -m  featureScaleModel -n featureScaleModel -t fullmodel
        az ml model register -m  oneHotEncoderModel -n  oneHotEncoderModel -t fullmodel
        az ml model register -m  stringIndexModel -n stringIndexModel -t fullmodel
        az ml model register -m  info -n info -t fullmodel

   La salida de cada comando proporciona un identificador de modelo que se necesita en el paso siguiente. Guárdelos en un archivo de texto para un uso futuro.

4. Cree el manifiesto para el servicio web.

   El manifiesto es la receta que se usa para crear la imagen de Docker para contenedores de servicios web. Incluye el código del servicio web, todos los modelos de aprendizaje automático y las dependencias del entorno de tiempo de ejecución.  Vaya a la carpeta `Code` en la CLI y ejecute la línea de comandos:

        az ml manifest create -n $webserviceName -f webservice.py -r spark-py -c ../Config/conda_dependencies_webservice.yml -i $modelID1 -i $modelID2 -i $modelID3 -i $modelID4 -i $modelID5

   La salida incluye un identificador de manifiesto para el paso siguiente. 

   Permanezca en el directorio `Code` y puede probar webservice.py ejecutando 

        az ml experiment submit -t dockerdsvm -c dockerdsvm webservice.py

5. Cree una imagen de Docker. 

        az ml image create -n [unique]image --manifest-id $manifestID

   La salida incluye un identificador de imagen para el siguiente paso, ya que esta imagen de Docker se usa en ACS. 

6. Implemente el servicio web en el clúster de ACS

        az ml service create realtime -n [unique] --image-id $imageID --cpu 0.5 --memory 2G

   La salida incluye un identificador de servicio que debe usar para obtener la clave de autorización y la dirección URL del servicio.

7. Llame al servicio Web en el código de Python para puntuar en lotes de pequeño tamaño.

   Use el siguiente comando para obtener la clave de autorización

         az ml service keys realtime -i $ServiceID 

   y use el comando siguiente para obtener la dirección URL del servicio de puntuación

        az ml service usage realtime -i $ServiceID

   Modifique el contenido del archivo `./Config/webservice.json` con la dirección URL del servicio de puntuación adecuada y la clave de autorización (mantenga el "Bearer" del archivo original y reemplace la parte "xxx"). 
   
   Vaya al directorio raíz del proyecto y pruebe el servicio web de puntuación para pequeños lotes mediante

        az ml experiment submit -t dockerdsvm -c dockerdsvm ./Code/scoring_webservice.py ./Config/webservice.json

8. Escale el servicio web. 

   Consulte [Cómo escalar la operacionalización en el clúster de ACS](https://github.com/Azure/Machine-Learning-Operationalization/blob/master/documentation/how-to-scale.md) para escalar el servicio web.
 

## <a name="conclusion"></a>Conclusión

Este ejemplo describe cómo usar Azure ML Workbench para entrenar un modelo de aprendizaje automático sobre macrodatos y operacionalizar el modelo entrenado. En concreto, se ha mostrado cómo:

* Configurar y usar diferentes destinos de proceso.

* Ver el historial de ejecución de métricas de seguimiento y diferentes ejecuciones.

* Operacionalización.

Los usuarios pueden extender el código para explorar la validación cruzada y el ajuste de hiperparámetros. Para más información sobre la validación cruzada y el ajuste de hiperparámetros, consulte https://github.com/Azure/MachineLearningSamples-DistributedHyperParameterTuning.  
Para más información acerca de la previsión de series temporales, consulte https://github.com/Azure/MachineLearningSamples-EnergyDemandTimeSeriesForecasting.
