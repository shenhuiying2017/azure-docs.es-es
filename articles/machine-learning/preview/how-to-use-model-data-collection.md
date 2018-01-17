---
title: "Usar la función de recopilación de datos del modelo en Azure Machine Learning Workbench | Microsoft Docs"
description: "En este artículo se explica cómo usar la función de recopilación de datos del modelo en Azure Machine Learning Workbench."
services: machine-learning
author: aashishb
ms.author: aashishb
manager: neerajkh
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 09/12/2017
ms.openlocfilehash: 6f9786b75f5160ceaa4dd269a91d7f3a4b6700d5
ms.sourcegitcommit: c4cc4d76932b059f8c2657081577412e8f405478
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/11/2018
---
# <a name="collect-model-data-by-using-data-collection"></a>Recopilar datos del modelo mediante la recopilación de datos

Puede usar la función de recopilación de datos del modelo en Azure Machine Learning para archivar predicciones y entradas del modelo desde un servicio web.

## <a name="install-the-data-collection-package"></a>Instalar el paquete de recopilación de datos
Puede instalar la biblioteca de recopilación de datos de forma nativa en Linux y Windows.

### <a name="windows"></a>Windows
En Windows, instale el módulo de recopilador de datos con el siguiente comando:

    pip install azureml.datacollector

### <a name="linux"></a>Linux
En Linux, instale primero la biblioteca libxml++. Ejecute el siguiente comando, que debe emitirse en sudo:

    sudo apt-get install libxml++2.6-2v5

Luego, ejecute el siguiente comando:

    pip install azureml.datacollector

## <a name="set-environment-variables"></a>Establecimiento de variables de entorno

La recopilación de datos del modelo depende de dos variables de entorno. AML_MODEL_DC_STORAGE_ENABLED debe establecerse en **true** (en minúsculas) y AML_MODEL_DC_STORAGE debe establecerse en la cadena de conexión para la cuenta de Azure Storage en la que quiere almacenar los datos.

Estas variables de entorno ya están establecidas cuando el servicio web se ejecuta en un clúster de Azure. Cuando se ejecuta localmente, debe configurarlas. Si usa Docker, use el parámetro -e del comando de ejecución de Docker para pasar variables de entorno.

## <a name="collect-data"></a>Recopilación de datos

Para usar la recolección de datos del modelo, realice los siguientes cambios en el archivo de puntuación:

1. Agregue el código siguiente en la parte superior del archivo:
   
    ```python
    from azureml.datacollector import ModelDataCollector
    ```

2. Agregue las líneas de código siguientes en la función `init()`:
    
    ```python
    global inputs_dc, prediction_dc
    inputs_dc = ModelDataCollector('model.pkl',identifier="inputs")
    prediction_dc = ModelDataCollector('model.pkl', identifier="prediction")
    ```

3. Agregue las líneas de código siguientes en la función `run(input_df)`:
    
    ```python
    global inputs_dc, prediction_dc
    inputs_dc.collect(input_df)
    prediction_dc.collect(pred)
    ```

    Asegúrese de que se inicializan las variables `input_df` y `pred` (valor de predicción de `model.predict()`) antes de llamar la función `collect()` en ellas.

4. Use el comando `az ml service create realtime` con el conmutador `--collect-model-data true` para crear un servicio web en tiempo real. Este paso garantiza que los datos del modelo se recopilan cuando se ejecuta el servicio.

     ```batch
    c:\temp\myIris> az ml service create realtime -f iris_score.py --model-file model.pkl -s service_schema.json -n irisapp -r python --collect-model-data true 
    ```
    
5. Para probar la recopilación de datos, ejecute el comando `az ml service run realtime`:

    ```
    C:\Temp\myIris> az ml service run realtime -i irisapp -d "ADD YOUR INPUT DATA HERE!!" 
    ``` 
    
## <a name="view-the-collected-data"></a>Ver los datos recopilados
Para ver los datos recopilados en el almacenamiento de blobs:

1. Inicie sesión en el [Azure Portal](https://portal.azure.com).
2. Seleccione **Más servicios**.
3. En el cuadro de búsqueda, escriba **Cuentas de almacenamiento** y seleccione la tecla Entrar.
4. En la hoja de búsqueda **Cuentas de almacenamiento**, seleccione el recurso **Cuenta de almacenamiento**. Para determinar cuál es su cuenta de almacenamiento, lleve a cabo los pasos siguientes:

    a. Vaya a Azure Machine Learning Workbench, seleccione el proyecto en el que está trabajando y abra el símbolo del sistema en el menú **Archivo**.
    
    b. Escriba `az ml env show -v` y consulte el valor *storage_account*. Este es el nombre de la cuenta de almacenamiento.

5. Seleccione **Contenedores** en el menú de la hoja de recursos y, después, el contenedor denominado **modeldata**. Para ver cómo los datos comienzan a propagarse a la cuenta de almacenamiento, debe esperar hasta 10 minutos tras la primera solicitud de servicio web. Los datos fluyen en blobs con la siguiente ruta de acceso del contenedor:

    `/modeldata/<subscription_id>/<resource_group_name>/<model_management_account_name>/<webservice_name>/<model_id>-<model_name>-<model_version>/<identifier>/<year>/<month>/<day>/data.csv`

Los datos pueden consumirse desde los blobs de Azure de diferentes maneras y tanto con software de Microsoft como con herramientas de código abierto. Estos son algunos ejemplos:
- Azure Machine Learning Workbench: abra el archivo .csv en Azure Machine Learning Workbench mediante la adición del archivo .csv como origen de datos.
- Excel: abra los archivos .csv diariamente como una hoja de cálculo.
- [Power BI](https://powerbi.microsoft.com/en-us/documentation/powerbi-azure-and-power-bi/): cree gráficos con datos extraídos de los datos .csv en blobs.
- [Spark](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-overview): cree una trama de datos con una gran parte de datos .csv.
    ```python
    var df = spark.read.format("com.databricks.spark.csv").option("inferSchema","true").option("header","true").load("wasb://modeldata@<storageaccount>.blob.core.windows.net/<subscription_id>/<resource_group_name>/<model_management_account_name>/<webservice_name>/<model_id>-<model_name>-<model_version>/<identifier>/<year>/<month>/<date>/*")
    ```
- [Hive](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-tutorial-get-started) : cargue los datos .csv en una tabla de hive y realice las consultas SQL directamente en el blob.

