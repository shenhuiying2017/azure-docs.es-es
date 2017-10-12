---
title: Transformar datos mediante Azure Data Factory | Microsoft Docs
description: "Aprenda cómo transformar datos o procesar datos en Azure Data Factory mediante Hadoop, Machine Learning o Azure Data Lake Analytics."
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: monicar
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: shlo
ms.openlocfilehash: b224f5b32c9a46f797e78644c731a9cd9341da2b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="transform-data-in-azure-data-factory"></a>Transformar datos en Azure Data Factory
> [!div class="op_single_selector"]
> * [Hive](transform-data-using-hadoop-hive.md)  
> * [Pig](transform-data-using-hadoop-pig.md)  
> * [MapReduce](transform-data-using-hadoop-map-reduce.md)  
> * [Hadoop Streaming](transform-data-using-hadoop-streaming.md)
> * [Spark](transform-data-using-spark.md)
> * [Machine Learning](transform-data-using-machine-learning.md) 
> * [Procedimiento almacenado](transform-data-using-stored-procedure.md)
> * [U-SQL de análisis con Data Lake](transform-data-using-data-lake-analytics.md)
> * [.NET personalizado](transform-data-using-dotnet-custom-activity.md)

## <a name="overview"></a>Información general
En este artículo se explican las actividades de transformación de datos de Azure Data Factory que puede usar para transformar y procesar los datos sin procesar en predicciones e información. Una actividad de transformación se ejecuta en un entorno informático, como clúster de HDInsight de Azure o un Lote de Azure. Proporciona vínculos a artículos con información detallada sobre cada actividad de transformación.

Data Factory admite las siguientes actividades de transformación de datos que se pueden agregar a las [canalizaciones](concepts-pipelines-activities.md) tanto individualmente como encadenadas a otra actividad.
 

## <a name="hdinsight-hive-activity"></a>Actividad de HDInsight Hive
La actividad de Hive de HDInsight en una canalización de Data Factory ejecuta consultas de Hive en su propio clúster de HDInsight o en uno a petición basado en Windows/Linux. Vea el artículo [Actividad de Hive](transform-data-using-hadoop-hive.md) para más información sobre esta actividad. 

## <a name="hdinsight-pig-activity"></a>Actividad de HDInsight Pig
La actividad de Pig de HDInsight en una canalización de Data Factory ejecuta consultas de Pig en su propio clúster de HDInsight o en uno a petición basado en Windows/Linux. Vea el artículo [Actividad de Pig](transform-data-using-hadoop-pig.md) para más información sobre esta actividad. 

## <a name="hdinsight-mapreduce-activity"></a>Actividad de MapReduce de HDInsight
La actividad MapReduce de HDInsight en una canalización de Data Factory ejecuta programas de MapReduce en su propio clúster de HDInsight o en uno basado en Windows/Linux a petición. Vea el artículo [Actividad de MapReduce](transform-data-using-hadoop-map-reduce.md) para más información sobre esta actividad.

## <a name="hdinsight-streaming-activity"></a>Actividad de HDInsight Streaming
La actividad de streaming de HDInsight en una canalización de Data Factory ejecuta programas de streaming de Hadoop en su propio clúster de HDInsight o en uno basado en Windows/Linux a petición. Vea [Actividad de HDInsight Streaming](transform-data-using-hadoop-streaming.md) para obtener información sobre esta actividad.

## <a name="hdinsight-spark-activity"></a>Actividad de HDInsight Spark
La actividad de Spark de HDInsight en una canalización de Data Factory ejecuta consultas de Spark en su propio clúster de HDInsight. Consulte [Invoke Spark programs from Azure Data Factory](transform-data-using-spark.md) (Invocar programas Spark desde Data Factory de Azure) para obtener información detallada. 

## <a name="machine-learning-activities"></a>Actividades de Machine Learning
Azure Data Factory permite crear fácilmente canalizaciones que utilizan un servicio web de Azure Machine Learning publicado para realizar análisis predictivos. Con la [actividad de ejecución de lotes](transform-data-using-machine-learning.md) en una canalización de Azure Data Factory puede invocar un servicio web Machine Learning para realizar predicciones sobre los datos en el lote.

Pasado algún tiempo, los modelos predictivos en los experimentos de puntuación de Machine Learning tienen que volver a entrenarse con nuevos conjuntos de datos de entrada. Después de terminar con el nuevo entrenamiento, tendrá que actualizar el servicio web de puntuación con el modelo de Machine Learning que volvió a entrenar. Puede usar la [actividad de recursos de actualización](update-machine-learning-models.md) para actualizar el servicio web con el modelo recién entrenado.  

Consulte [Usar actividades de Machine Learning](transform-data-using-machine-learning.md) para obtener más información sobre estas actividades de Machine Learning. 

## <a name="stored-procedure-activity"></a>Actividad de procedimiento almacenado
Puede usar la actividad de procedimiento almacenado de SQL Server en una canalización de Data Factory para invocar un procedimiento almacenado en uno de los siguientes almacenes de datos: Azure SQL Database, Azure SQL Data Warehouse y Base de datos de SQL Server en su empresa o una VM de Azure. Vea el artículo [Actividad de procedimiento almacenado](transform-data-using-stored-procedure.md) para más información.  

## <a name="data-lake-analytics-u-sql-activity"></a>Actividad de U-SQL de Data Lake Analytics 
La actividad de U-SQL de Data Lake Analytics ejecuta un script de U-SQL en un clúster de Azure Data Lake Analytics. Vea el artículo [Actividad de U-SQL de Data Analytics](transform-data-using-data-lake-analytics.md) para más información. 

## <a name="net-custom-activity"></a>Actividad personalizada de .NET
Si necesita transformar datos de algún modo no compatible con Data Factory, puede crear una actividad personalizada con su propia lógica de procesamiento de datos y usarla en la canalización. Puede configurar una actividad de .NET personalizada para ejecutarse mediante un servicio Azure Batch o un clúster de Azure HDInsight. Consulte el artículo [Utilizar actividades personalizadas](transform-data-using-dotnet-custom-activity.md) para obtener más información. 

Puede crear una actividad personalizada para ejecutar scripts de R en su clúster de HDInsight con R instalado. Consulte [Run R Script using Azure Data Factory](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)(Ejecutar script de R con Data Factory de Azure). 

## <a name="compute-environments"></a>Entornos de proceso
Deberá crear un servicio vinculado para el entorno de proceso y después usar el servicio vinculado al definir una actividad de transformación. La Factoría de datos admite dos tipos de entornos de proceso. 

- **A petición**: en este caso, el entorno informático es completamente administrado por Data Factory. El servicio Factoría de datos lo crea automáticamente antes de que se envíe un trabajo para procesar los datos y que se quite cuando finalice el trabajo. Los usuarios pueden configurar y controlar la configuración granular del entorno de proceso a petición para la ejecución del trabajo, la administración del clúster y las acciones de arranque. 
- **Traiga el suyo propio**: en este caso, puede registrar su propio entorno informático (por ejemplo, clúster de HDInsight) como servicio vinculado en la Factoría de datos. El usuario administra el entorno de procesos y el servicio Factoría de datos lo usa para ejecutar las actividades. 

Vea el artículo [Servicios vinculados de procesos](compute-linked-services.md) para obtener información sobre los servicios vinculados de proceso compatibles con Data Factory. 

## <a name="next-steps"></a>Pasos siguientes
Vea el siguiente tutorial para ver un ejemplo del uso de una actividad de transformación: [Tutorial: Transformación de datos mediante Spark](tutorial-transform-data-spark-powershell.md)