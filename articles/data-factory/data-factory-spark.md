---
title: Invocar programas Spark desde Data Factory de Azure
description: "Obtenga información sobre cómo invocar programas Spark desde Data Factory de Azure mediante la actividad MapReduce."
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: fd98931c-cab5-4d66-97cb-4c947861255c
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/21/2017
ms.author: spelluru
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: 2af5d275bb331101b370e4a12043e27b6cdf5b68
ms.lasthandoff: 03/15/2017


---
# <a name="invoke-spark-programs-from-data-factory"></a>Invocar programas Spark desde Data Factory
> [!div class="op_single_selector" title1="Transformation Activities"]
> * [Actividad de Hive](data-factory-hive-activity.md) 
> * [Actividad de Pig](data-factory-pig-activity.md)
> * [Actividad MapReduce](data-factory-map-reduce.md)
> * [Actividad de streaming de Hadoop](data-factory-hadoop-streaming-activity.md)
> * [Actividad de Spark](data-factory-spark.md)
> * [Actividad de ejecución de Batch de Machine Learning](data-factory-azure-ml-batch-execution-activity.md)
> * [Actividad Actualizar recurso de Machine Learning](data-factory-azure-ml-update-resource-activity.md)
> * [Actividad de procedimiento almacenado](data-factory-stored-proc-activity.md)
> * [Actividad U-SQL de Data Lake Analytics](data-factory-usql-activity.md)
> * [Actividad personalizada de .NET](data-factory-use-custom-activities.md)

## <a name="introduction"></a>Introducción
La actividad de Spark de HDInsight en una [canalización](data-factory-create-pipelines.md) de Data Factory ejecuta consultas de Spark en [su propio](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) clúster de HDInsight. Este artículo se basa en el artículo sobre [actividades de transformación de datos](data-factory-data-transformation-activities.md) , que presenta información general de la transformación de datos y las actividades de transformación admitidas.

## <a name="hdinsight-linked-service"></a>Servicio vinculado de HDInsight
Antes de emplear una actividad de Spark en una canalización de Data Factory, cree su servicio vinculado de HDInsight. El siguiente fragmento JSON muestra la definición de un servicio vinculado de HDInsight para que apunte a su propio clúster de Spark de Azure HDInsight.   

```json
{
    "name": "HDInsightLinkedService",
    "properties": {
        "type": "HDInsight",
        "typeProperties": {
            "clusterUri": "https://MyHdinsightSparkcluster.azurehdinsight.net/",
              "userName": "admin",
              "password": "password",
              "linkedServiceName": "MyHDInsightStoragelinkedService"
        }
    }
}
```

> [!NOTE]
> Actualmente, la actividad de Spark no admite clústeres de Spark con Data Lake Store como almacenamiento principal o a servicios vinculados de HDInsight a petición. 

Para obtener más información sobre el servicio vinculado de HDInsight y otros servicios vinculados de proceso, consulte el artículo sobre [servicios vinculados de proceso de Data Factory](data-factory-compute-linked-services.md). 

## <a name="spark-activity-json"></a>Definición JSON de actividad de Spark
Esta es la definición de JSON de ejemplo de una actividad de Spark:    

```json
{
    "name": "MySparkActivity",
    "description": "This activity invokes the Spark program",
    "type": "HDInsightSpark",
    "outputs": [
        {
            "name": "PlaceholderDataset"
        }
    ],
    "linkedServiceName": "HDInsightLinkedService",
    "typeProperties": {
        "rootPath": "mycontainer\\myfolder",
        "entryFilePath": "main.py",
        "arguments": [ "arg1", "arg2" ],
        "sparkConfig": {
              "spark.python.worker.memory": "512m"
        }
    }
}
```
En la siguiente tabla se describen las propiedades JSON que se usan en la definición de JSON: 

| Propiedad | Descripción | Obligatorio |
| -------- | ----------- | -------- |
| name | Nombre de la actividad en la canalización. | Sí |
| Descripción | Texto que describe para qué se usa la actividad. | No |
| type | Esta propiedad debe establecerse en HDInsightSpark. | Sí |
| linkedServiceName | Referencia a un servicio vinculado de HDInsight en el que se ejecuta el programa de Spark. | Sí |
| rootPath | Contenedor de blobs de Azure y la carpeta que contiene el archivo de Spark. El nombre del archivo distingue mayúsculas de minúsculas. | Sí |
| entryFilePath | Ruta de acceso relativa a la carpeta raíz del código o el paquete de Spark. | Sí |
| className | Clase principal de Spark o Java de la aplicación. | No | 
| argumentos | Lista de argumentos de línea de comandos del programa de Spark. | No | 
| proxyUser | Cuenta de usuario de suplantación para ejecutar el programa de Spark. | No | 
| sparkConfig | Propiedades de configuración de Spark. | No | 
| getDebugInfo | Especifica si se copian los archivos de registro de Spark en el almacenamiento de Azure que usa el clúster de HDInsight que especifica sparkJobLinkedService. Valores permitidos: Ninguno, Siempre o Error. Valor predeterminado: Ninguno. | No | 
| sparkJobLinkedService | El servicio vinculado de Azure Storage que contiene los registros, las dependencias y los archivos de trabajos de Spark.  Si no especifica un valor para esta propiedad, se usa el almacenamiento asociado con el clúster de HDInsight. | No |

## <a name="folder-structure"></a>Estructura de carpetas
La actividad de Spark no es compatible con un script en línea, al contrario que las actividades de Pig y Hive. Los trabajos de Spark también son más ampliable que los de Pig y Hive. En los trabajos de Spark, puede proporcionar varias dependencias como paquetes JAR (ubicados en la CLASSPATH de Java), archivos de Python (ubicados en la ruta PYTHONPATH) y cualquier otro archivo.

Cree la siguiente estructura de carpetas en la instancia de Azure Blob Storage a la que hace referencia el servicio vinculado de HDInsight. Luego, cargue los archivos dependientes en las subcarpetas adecuadas de la carpeta raíz que representa **entryFilePath**. Por ejemplo, cargue los archivos de Python en la subcarpeta pyFiles y los archivos JAR en la subcarpeta jars de la carpeta raíz. En el entorno de tiempo de ejecución, el servicio Data Factory espera la siguiente estructura de carpetas en Azure Blob Storage:     

| Ruta de acceso | Descripción | Obligatorio | Escriba |
| ---- | ----------- | -------- | ---- | 
| .    | Ruta de acceso raíz del trabajo de Spark en el servicio vinculado de almacenamiento.    | Sí | Carpeta |
| &lt;Definida por el usuario&gt; | Ruta de acceso que apunta al archivo de entrada del trabajo de Spark. | Sí | Archivo | 
| ./jars | Todos los archivos de esta carpeta se cargan y se colocan en la ruta CLASSPATH de Java del clúster. | No | Carpeta |
| ./pyFiles | Todos los archivos de esta carpeta se cargan y se colocan en la ruta PYTHONPATH del clúster. | No | Carpeta |
| ./files | Todos los archivos de esta carpeta se cargan y se colocan en el directorio de trabajo del ejecutor. | No | Carpeta |
| ./archives | Todos los archivos de esta carpeta están sin comprimir. | No | Carpeta |
| ./logs | Carpeta donde se almacenan los registros del clúster de Spark.| No | Carpeta |

Este es un ejemplo de un almacenamiento que contiene dos archivos de trabajos de Spark en la instancia de Azure Blob Storage a la que hace referencia el servicio vinculado de HDInsight. 

```
SparkJob1
    main.jar
    files
        input1.txt
        input2.txt
    jars
        package1.jar
        package2.jar
    logs

SparkJob2
    main.py
    pyFiles
        scrip1.py
        script2.py
    logs    
```

> [!IMPORTANT]
> Para ver un tutorial completo sobre cómo crear una canalización con una actividad de transformación, lea el artículo [Creación de una canalización para transformar datos](data-factory-build-your-first-pipeline-using-editor.md). 

## <a name="spark-sample-on-github"></a>Ejemplo de Spark en GitHub
Antes de admitir la actividad de Spark, la solución para ejecutar programas de Spark desde la canalización de Data Factory consistía en emplear una actividad de MapReduce. Puede seguir usando la [actividad de MapReduce](data-factory-map-reduce.md) en una canalización de Data Factory para ejecutar programas de Spark en su clúster de Spark de HDInsight. Se recomienda utilizar la actividad de Spark en lugar de la de MapReduce. 

[Spark - Data Factory sample on GitHub](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/Spark) (Spark: ejemplo de Data Factory en GitHub) muestra cómo usar la actividad MapReduce para invocar un programa Spark. El programa Spark se limita a copiar datos de un contenedor de blobs de Azure a otro. 

## <a name="see-also"></a>Otras referencias
* [Actividad de Hive](data-factory-hive-activity.md)
* [Actividad de Pig](data-factory-pig-activity.md)
* [Actividad MapReduce](data-factory-map-reduce.md)
* [Actividad de streaming de Hadoop](data-factory-hadoop-streaming-activity.md)
* [Invocar scripts de R](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)


