---
title: "Transformación de datos mediante la actividad de Spark en Azure Data Factory | Microsoft Docs"
description: "Obtenga información sobre cómo transformar datos mediante la ejecución de programas de Spark desde una canalización de Azure Data Factory mediante la actividad de Spark."
services: data-factory
documentationcenter: 
author: shengcmsft
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2018
ms.author: shengc
ms.openlocfilehash: 4aed91696b5853b56ab17d69753d20081c79cdf7
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/23/2018
---
# <a name="transform-data-using-spark-activity-in-azure-data-factory"></a>Transformación de datos mediante la actividad de Spark en Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versión 1: Disponibilidad general](v1/data-factory-spark.md)
> * [Versión 2: versión preliminar](transform-data-using-spark.md)

La actividad de Spark en una [canalización](concepts-pipelines-activities.md) de Data Factory ejecuta un programa de Spark en su clúster de HDInsight [propio](compute-linked-services.md#azure-hdinsight-linked-service) o [a petición](compute-linked-services.md#azure-hdinsight-on-demand-linked-service). Este artículo se basa en el artículo sobre [actividades de transformación de datos](transform-data.md) , que presenta información general de la transformación de datos y las actividades de transformación admitidas. Cuando se usa un servicio vinculado a Spark a petición, Data Factory crea automáticamente un clúster Just-in-Time para procesar los datos y, luego, lo elimina una vez finalizado el procesamiento. 

> [!NOTE]
> Este artículo se aplica a la versión 2 de Data Factory, que actualmente se encuentra en versión preliminar. Si usa la versión 1 del servicio Data Factory, que está disponible con carácter general, vea [Actividad de Spark en V1](v1/data-factory-spark.md).

> [!IMPORTANT]
> La actividad de Spark no admite clústeres de HDInsight Spark que usan una instancia de Azure Data Lake Store como almacenamiento principal.

## <a name="spark-activity-properties"></a>Propiedades de la actividad de Spark
Esta es la definición de JSON de ejemplo de una actividad de Spark:    

```json
{
    "name": "Spark Activity",
    "description": "Description",
    "type": "HDInsightSpark",
    "linkedServiceName": {
        "referenceName": "MyHDInsightLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "sparkJobLinkedService": {
            "referenceName": "MyAzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "rootPath": "adfspark\\pyFiles",
        "entryFilePath": "test.py",
        "arguments": [ "arg1", "arg2" ],
        "sparkConfig": {
            "ConfigItem1": "Value"
        },
        "getDebugInfo": "Failure",
        "arguments": [
            "SampleHadoopJobArgument1"
        ]
    }
}
```

En la siguiente tabla se describen las propiedades JSON que se usan en la definición de JSON:

| Propiedad              | DESCRIPCIÓN                              | Obligatorio |
| --------------------- | ---------------------------------------- | -------- |
| Nombre                  | Nombre de la actividad en la canalización.    | Sí      |
| Descripción           | Texto que describe para qué se usa la actividad.  | Sin        |
| Tipo                  | Para la actividad de Spark, el tipo de actividad es HDInsightSpark. | Sí      |
| linkedServiceName     | Nombre del servicio vinculado de HDInsight Spark en el que se ejecuta el programa de Spark. Para obtener más información sobre este servicio vinculado, vea el artículo [Compute linked services](compute-linked-services.md) (Servicios vinculados de procesos). | Sí      |
| SparkJobLinkedService | El servicio vinculado de Azure Storage que contiene los registros, las dependencias y los archivos de trabajos de Spark.  Si no especifica un valor para esta propiedad, se usa el almacenamiento asociado con el clúster de HDInsight. | Sin        |
| rootPath              | Contenedor de blobs de Azure y la carpeta que contiene el archivo de Spark. El nombre del archivo distingue mayúsculas de minúsculas. Vea la sección sobre la estructura de carpetas (sección siguiente) para obtener más información sobre la estructura de esta carpeta. | Sí      |
| entryFilePath         | Ruta de acceso relativa a la carpeta raíz del código o el paquete de Spark. | Sí      |
| className             | Clase principal de Spark o Java de la aplicación.      | Sin        |
| argumentos             | Lista de argumentos de línea de comandos del programa de Spark. | Sin        |
| proxyUser             | Cuenta de usuario de suplantación para ejecutar el programa de Spark. | Sin        |
| sparkConfig           | Especifique valores para propiedades de configuración de Spark indicados en el tema [Spark Configuration: Application properties](https://spark.apache.org/docs/latest/configuration.html#available-properties) (Configuración de Spark: Propiedades de aplicación). | Sin        |
| getDebugInfo          | Especifica si se copian los archivos de registro de Spark en el almacenamiento de Azure que usa el clúster de HDInsight que especifica sparkJobLinkedService. Valores permitidos: Ninguno, Siempre o Error. Valor predeterminado: Ninguno. | Sin        |

## <a name="folder-structure"></a>Estructura de carpetas
Los trabajos de Spark son más extensibles que los de Pig y Hive. En los trabajos de Spark, puede proporcionar varias dependencias como paquetes JAR (ubicados en la CLASSPATH de Java), archivos de Python (ubicados en la ruta PYTHONPATH) y cualquier otro archivo.

Cree la siguiente estructura de carpetas en la instancia de Azure Blob Storage a la que hace referencia el servicio vinculado de HDInsight. Luego, cargue los archivos dependientes en las subcarpetas adecuadas de la carpeta raíz que representa **entryFilePath**. Por ejemplo, cargue los archivos de Python en la subcarpeta pyFiles y los archivos JAR en la subcarpeta jars de la carpeta raíz. En el entorno de tiempo de ejecución, el servicio Data Factory espera la siguiente estructura de carpetas en Azure Blob Storage:     

| Ruta de acceso                  | DESCRIPCIÓN                              | Obligatorio | type   |
| --------------------- | ---------------------------------------- | -------- | ------ |
| `.` (raíz)            | Ruta de acceso raíz del trabajo de Spark en el servicio vinculado de almacenamiento. | Sí      | Carpeta |
| &lt;Definida por el usuario&gt; | Ruta de acceso que apunta al archivo de entrada del trabajo de Spark. | Sí      | Archivo   |
| ./jars                | Todos los archivos de esta carpeta se cargan y se colocan en la ruta CLASSPATH de Java del clúster. | Sin        | Carpeta |
| ./pyFiles             | Todos los archivos de esta carpeta se cargan y se colocan en la ruta PYTHONPATH del clúster. | Sin        | Carpeta |
| ./files               | Todos los archivos de esta carpeta se cargan y se colocan en el directorio de trabajo del ejecutor. | Sin        | Carpeta |
| ./archives            | Todos los archivos de esta carpeta están sin comprimir. | Sin        | Carpeta |
| ./logs                | Carpeta que contiene los registros del clúster de Spark. | Sin        | Carpeta |

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
## <a name="next-steps"></a>pasos siguientes
Vea los siguientes artículos, en los que se explica cómo transformar datos de otras maneras: 

* [Actividad de U-SQL](transform-data-using-data-lake-analytics.md)
* [Actividad de Hive](transform-data-using-hadoop-hive.md)
* [Actividad de Pig](transform-data-using-hadoop-pig.md)
* [Actividad de MapReduce](transform-data-using-hadoop-map-reduce.md)
* [Actividad de streaming de Hadoop](transform-data-using-hadoop-streaming.md)
* [Actividad de Spark](transform-data-using-spark.md)
* [Actividad personalizada de .NET](transform-data-using-dotnet-custom-activity.md)
* [Actividad de ejecución de Batch de Machine Learning](transform-data-using-machine-learning.md)
* [Actividad de procedimiento almacenado](transform-data-using-stored-procedure.md)
