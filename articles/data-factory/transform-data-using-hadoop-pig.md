---
title: "Transformación de datos mediante la actividad Pig de Hadoop en Azure Data Factory | Microsoft Docs"
description: "Aprenda cómo puede usar la actividad de Pig en Factoría de datos de Azure para ejecutar scripts de Pig en un clúster de HDInsight suyo propio o a petición."
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
ms.date: 09/19/2017
ms.author: shengc
ms.openlocfilehash: 7745f273283ff998b41fe59a10e705e7564a7bd3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="transform-data-using-hadoop-pig-activity-in-azure-data-factory"></a>Transformación de datos mediante la actividad Pig de Hadoop en Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versión 1: Disponibilidad general](v1/data-factory-pig-activity.md)
> * [Versión 2: Versión preliminar](transform-data-using-hadoop-pig.md)

La actividad Pig de HDInsight en una [canalización](concepts-pipelines-activities.md) de Data Factory ejecuta consultas de Pig en su [propio](compute-linked-services.md#azure-hdinsight-linked-service) clúster de HDInsight o en uno [a petición](compute-linked-services.md#azure-hdinsight-on-demand-linked-service). Este artículo se basa en el artículo sobre [actividades de transformación de datos](transform-data.md) , que presenta información general de la transformación de datos y las actividades de transformación admitidas.

> [!NOTE]
> Este artículo se aplica a la versión 2 de Data Factory, que actualmente se encuentra en versión preliminar. Si usa la versión 1 del servicio Data Factory, que está disponible con carácter general, consulte [Actividad Pig en V1](v1/data-factory-pig-activity.md).

Si no está familiarizado con Azure Data Factory, vea [Introduction to Azure Data Factory](introduction.md) (Introducción a Azure Data Factory) y siga el [tutorial de transformación de datos](tutorial-transform-data-spark-powershell.md) antes de leer este artículo. 

## <a name="syntax"></a>Sintaxis

```json
{
    "name": "Pig Activity",
    "description": "description",
    "type": "HDInsightPig",
    "linkedServiceName": {
        "referenceName": "MyHDInsightLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "scriptLinkedService": {
            "referenceName": "MyAzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "scriptPath": "MyAzureStorage\\PigScripts\\MyPigSript.pig",
        "getDebugInfo": "Failure",
        "arguments": [
            "SampleHadoopJobArgument1"
        ],
        "defines": {
            "param1": "param1Value"
        }
    }   
}
```
## <a name="syntax-details"></a>Detalles de la sintaxis

| Propiedad            | Descripción                              | Obligatorio |
| ------------------- | ---------------------------------------- | -------- |
| name                | Nombre de la actividad                     | Sí      |
| Descripción         | Texto que describe para qué se usa la actividad. | No       |
| type                | Para la actividad de Hive, el tipo de actividad es HDinsightPig | Sí      |
| linkedServiceName   | Referencia al clúster de HDInsight registrado como servicio vinculado en Data Factory. Para obtener más información sobre este servicio vinculado, vea el artículo [Compute linked services](compute-linked-services.md) (Servicios vinculados de procesos). | Sí      |
| scriptLinkedService | Referencia a un servicio vinculado de Azure Storage que se utiliza para almacenar el script de Pig que se va a ejecutar. Si no se especifica este servicio vinculado, se usará el servicio vinculado de Azure Storage definido en el servicio vinculado de HDInsight. | No       |
| scriptPath          | Proporcione la ruta de acceso al archivo de script almacenado en Azure Storage al que hace referencia scriptLinkedService. El nombre del archivo distingue mayúsculas de minúsculas. | No       |
| getDebugInfo        | Especifica si se copian los archivos de registro en el almacenamiento de Azure Storage que usa el clúster de HDInsight o que está especificado por scriptLinkedService. Valores permitidos: Ninguno, Siempre o Error. Valor predeterminado: Ninguno. | No       |
| argumentos           | Especifica una matriz de argumentos para un trabajo de Hadoop. Los argumentos se pasan a cada tarea como argumentos de la línea de comandos. | No       |
| define los campos             | Especifique los parámetros como pares de clave y valor para referencia en el script de Pig. | No       |

## <a name="next-steps"></a>Pasos siguientes
Vea los siguientes artículos, en los que se explica cómo transformar datos de otras maneras: 

* [Actividad de U-SQL](transform-data-using-data-lake-analytics.md)
* [Actividad Hive](transform-data-using-hadoop-hive.md)
* [Actividad MapReduce](transform-data-using-hadoop-map-reduce.md)
* [Actividad de streaming de Hadoop](transform-data-using-hadoop-streaming.md)
* [Actividad de Spark](transform-data-using-spark.md)
* [Actividad personalizada de .NET](transform-data-using-dotnet-custom-activity.md)
* [Actividad de ejecución de Batch de Machine Learning](transform-data-using-machine-learning.md)
* [Actividad de procedimiento almacenado](transform-data-using-stored-procedure.md)
