---
title: "Cómo usar la función de recopilación de datos de Administración de modelos en Azure Machine Learning Workbench | Microsoft Docs"
description: "En este documento se habla de cómo utilizar la función de recopilación de datos de Administración de modelos en Azure Machine Learning Workbench"
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
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: e751e2f0ec812de43a03749e04ff165fa62ec649
ms.contentlocale: es-es
ms.lasthandoff: 09/25/2017

---

# <a name="how-to-collect-model-data-using-data-collection"></a>Cómo recopilar datos del modelo con recopilación de datos

La función de recopilación de datos del modelo permite archivar entradas del modelo y predicciones de un servicio web de aprendizaje automático. En este documento se habla de los siguientes aspectos de recopilación de datos del modelo:

- Cómo instalar el paquete de recopilación de datos
- Cómo usar la recopilación de datos
- Cómo ver y utilizar los datos recopilados

## <a name="how-to-install-the-data-collection-package"></a>Cómo instalar el paquete de recopilación de datos
La biblioteca de recopilación de datos puede instalarse de forma nativa en Windows y Linux.

### <a name="windows"></a>Windows
En Windows, se puede instalar el módulo de recopilador de datos con el siguiente comando:

    pip install azureml.datacollector

### <a name="linux"></a>Linux
En Linux, debe instalarse primero la biblioteca libxml++. Ejecute el siguiente comando, que debe emitirse en sudo:

    sudo apt-get install libxml++2.6-2v5

A continuación, emita el comando siguiente:

    pip install azureml.datacollector
## <a name="how-to-use-data-collection"></a>Cómo usar la recopilación de datos

Para usar la recolección de datos del modelo, debe realizar los siguientes cambios en el archivo de puntuación:

1. Agregue el código siguiente en la parte superior del archivo:
   
    ```python
    from azureml.datacollector import ModelDataCollector
    ```

2. Agregue las siguientes líneas de código en la función `init()`:
    
    ```python
    global inputs_dc, prediction_dc
    inputs_dc = ModelDataCollector('model.pkl',identifier="inputs")
    prediction_dc = ModelDataCollector('model.pkl', identifier="prediction")
    ```

3. Agregue las siguientes líneas de código en la función `run(input_df)`:
    
    ```python
    global inputs_dc, prediction_dc
    inputs_dc.collect(input_df)
    prediction_dc.collect(pred)
    ```

    Asegúrese de que las variables `input_df` y `pred` (valor de predicción de `model.predict()`) se inicializan antes de llamar la función `collect()` en ellas.

4. Use el comando `az ml service create realtime` con el conmutador `--collect-model-data true` para crear un servicio web en tiempo real. Esto garantiza que los datos del modelo se recopilan cuando se ejecuta el servicio.

     ```batch
    c:\temp\myIris> az ml service create realtime -f iris_score.py --model-file model.pkl -s service_schema.json -n irisapp -r python --collect-model-data true 
    ```
    
5. Para probar la recopilación de datos, ejecute `az ml service run realtime`.

    ```
    C:\Temp\myIris> az ml service run realtime -i irisapp -d "ADD YOUR INPUT DATA HERE!!" 
    ``` 
    
## <a name="how-to-view-and-consume-the-collected-data"></a>Cómo ver y utilizar los datos recopilados
Para ver los datos recopilados en el almacenamiento de blobs:

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).
2. Haga clic en **Más servicios**.
3. En el cuadro de búsqueda, escriba `Storage accounts` y presione **ENTRAR**.
4. En la hoja de búsqueda **Cuentas de almacenamiento**, seleccione el recurso **Cuenta de almacenamiento**. Para determinar la cuenta de almacenamiento, use los pasos siguientes:

    a. Vaya a Azure Machine Learning Workbench, seleccione el proyecto en el que está trabajando y abra el símbolo de la línea de comandos del menú **Archivo**.
    
    b. Escriba `az ml env show -v` y compruebe el valor *storage_account*. Este es el nombre de la cuenta de almacenamiento.

5. Haga clic en **Contenedores** en el menú de la hoja de recursos y, después, en el contenedor denominado **modeldata**. Debe esperar hasta 10 minutos tras la primera solicitud de servicio web para poder ver cómo los datos comienzan a propagarse a la cuenta de almacenamiento. Los datos fluyen en blobs con la siguiente ruta de acceso del contenedor:

    `/modeldata/<subscription_id>/<resource_group_name>/<model_management_account_name>/<webservice_name>/<model_id>-<model_name>-<model_version>/<identifier>/<year>/<month>/<day>/data.csv`

Los datos pueden consumirse desde los blobs de Azure de diferentes maneras y tanto con software de Microsoft como con herramientas de código abierto. Estos son algunos ejemplos:
 - Azure Machine Learning Workbench: abra el archivo cvs en Azure Machine Learning Workbench mediante la adición del archivo csv como origen de datos.
 - Excel: abra los archivos csv diariamente como una hoja de cálculo.
 - [Power BI](https://powerbi.microsoft.com/en-us/documentation/powerbi-azure-and-power-bi/): cree gráficos con datos extraídos de los datos csv en blobs.
 - [Spark](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-apache-spark-overview): cree una trama de datos con una gran parte de datos csv.
    ```python
    var df = spark.read.format("com.databricks.spark.csv").option("inferSchema","true").option("header","true").load("wasb://modeldata@<storageaccount>.blob.core.windows.net/<subscription_id>/<resource_group_name>/<model_management_account_name>/<webservice_name>/<model_id>-<model_name>-<model_version>/<identifier>/<year>/<month>/<date>/*")
    ```
- [Hive](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-linux-tutorial-get-started) : cargue los datos csv en una tabla de Hive y realice las consultas SQL directamente en el blob.


