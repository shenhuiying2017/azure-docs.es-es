---
title: Invocar programas Spark desde Data Factory de Azure
description: Obtenga información sobre cómo invocar programas Spark desde Data Factory de Azure mediante la actividad MapReduce.
services: data-factory
documentationcenter: ''
author: spelluru
manager: jhubbard
editor: monicar

ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/25/2016
ms.author: spelluru

---
# Invocar programas Spark desde Data Factory
## Introducción
Puede usar la actividad MapReduce en una canalización de Data Factory para ejecutar programas Spark en su clúster de HDInsight Spark. Consulte el artículo [Actividad MapReduce](data-factory-map-reduce.md) para obtener información detallada sobre el uso de la actividad antes de leer este artículo.

## Ejemplo de Spark en GitHub
[Spark - Data Factory sample on GitHub](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/Spark) (Spark: ejemplo de Data Factory en GitHub) muestra cómo usar la actividad MapReduce para invocar un programa Spark. El programa Spark se limita a copiar datos de un contenedor de blobs de Azure a otro.

## Entidades de Factoría de datos
La carpeta **Spark-ADF/src/ADFJsons** contiene archivos para entidades de Data Factory (servicios vinculados, conjuntos de datos, canalización).

Hay dos **servicios vinculados** en este ejemplo: Almacenamiento de Azure y Azure HDInsight. Especifique sus valores clave y nombre de Almacenamiento de Azure en **StorageLinkedService.json** y clusterUri, userName y contraseña en **HDInsightLinkedService.json**.

Hay dos **conjuntos de datos** en este ejemplo: **input.json** y **output.json**. Estos archivos se encuentran en la carpeta **Conjuntos de datos**. Estos archivos representan conjuntos de datos de entrada y salida para la actividad MapReduce

Las canalizaciones de ejemplo se buscan en la carpeta **ADFJsons/Pipeline**. Revise una canalización para entender cómo invocar un programa Spark mediante la actividad MapReduce.

L actividad MapReduce está configurada para invocar **com.adf.sparklauncher.jar** en el contenedor **adflibs** de Almacenamiento de Azure (especificado en StorageLinkedService.json). El código fuente para este programa está en la carpeta Spark-ADF/src/main/java/com/adf/, llama a spark-submit y ejecuta trabajos Spark.

> [!IMPORTANT]
> Lea [README.TXT](https://github.com/Azure/Azure-DataFactory/blob/master/Samples/Spark/README.txt) para ver la información adicional y más reciente antes de utilizar el ejemplo.
> 
> Use su propio clúster de HDInsight Spark con este enfoque para invocar programas Spark mediante la actividad MapReduce. No se admite el uso de un clúster de HDInsight a petición.
> 
> 

## Otras referencias
* [Actividad de Hive](data-factory-hive-activity.md)
* [Actividad de Pig](data-factory-pig-activity.md)
* [Actividad MapReduce](data-factory-map-reduce.md)
* [Actividad de streaming de Hadoop](data-factory-hadoop-streaming-activity.md)
* [Invocar scripts de R](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)

<!---HONumber=AcomDC_0831_2016-->