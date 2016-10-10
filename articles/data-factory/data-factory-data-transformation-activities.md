<properties 
	pageTitle="Transformación de datos: procesamiento y transformación de datos| Microsoft Azure" 
	description="Aprenda cómo transformar datos o procesar datos en Azure Data Factory mediante Hadoop, Machine Learning o Azure Data Lake Analytics." 
	keywords="transformación de datos, procesar datos, transformar datos, actividad de transformación"
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/23/2016" 
	ms.author="spelluru"/>

# Transformar datos en Azure Data Factory

## Información general 
En este artículo se explican las actividades de transformación de datos de Azure Data Factory que puede usar para transformar y procesar los datos sin procesar en predicciones e información. Una actividad de transformación se ejecuta en un entorno informático, como clúster de HDInsight de Azure o un Lote de Azure. Proporciona vínculos a artículos con información detallada sobre cada actividad de transformación.
 
Data Factory admite las siguientes actividades de transformación de datos que se pueden agregar a las [canalizaciones](data-factory-create-pipelines.md) tanto individualmente como encadenadas a otra actividad.

> [AZURE.NOTE] Para obtener un tutorial con instrucciones paso a paso, consulte el artículo [Crear una canalización con la transformación de Hive](data-factory-build-your-first-pipeline.md).

## Actividad de HDInsight Hive
La actividad de Hive de HDInsight en una canalización de Data Factory ejecuta consultas de Hive en su propio clúster de HDInsight o en uno a petición basado en Windows/Linux. Consulte el artículo [Actividad de Hive](data-factory-hive-activity.md) para obtener más información acerca de esta actividad.

## Actividad de HDInsight Pig
La actividad de Pig de HDInsight en una canalización de Data Factory ejecuta consultas de Pig en su propio clúster de HDInsight o en uno a petición basado en Windows/Linux. Consulte el artículo [Actividad de Pig](data-factory-pig-activity.md) para obtener más información acerca de esta actividad.

## Actividad de MapReduce de HDInsight
La actividad MapReduce de HDInsight en una canalización de Data Factory ejecuta programas de MapReduce en su propio clúster de HDInsight o en uno basado en Windows/Linux a petición. Consulte el artículo [Actividad de MapReduce](data-factory-map-reduce.md) para obtener más información acerca de esta actividad.

Puede usar la actividad MapReduce para ejecutar programas Spark en su clúster de HDInsight Spark. Consulte [Invoke Spark programs from Azure Data Factory](data-factory-spark.md) (Invocar programas Spark desde Data Factory de Azure) para obtener información detallada.

## Actividad de HDInsight Streaming
La actividad de streaming de HDInsight en una canalización de Data Factory ejecuta programas de streaming de Hadoop en su propio clúster de HDInsight o en uno basado en Windows/Linux a petición. Vea [Actividad de HDInsight Streaming](data-factory-hadoop-streaming-activity.md) para obtener información sobre esta actividad.

## Actividades de Machine Learning
Azure Data Factory permite crear fácilmente canalizaciones que utilizan un servicio web de Azure Machine Learning publicado para realizar análisis predictivos. Mediante la [actividad de ejecución de Batch](data-factory-azure-ml-batch-execution-activity.md#invoking-a-web-service-using-batch-execution-activity) en una canalización de Azure Data Factory, puede invocar un servicio web de Machine Learning para realizar predicciones sobre los datos en el lote.

Pasado algún tiempo, los modelos predictivos en los experimentos de puntuación de Machine Learning tienen que volver a entrenarse con nuevos conjuntos de datos de entrada. Después de terminar con el nuevo entrenamiento, tendrá que actualizar el servicio web de puntuación con el modelo de Machine Learning que volvió a entrenar. Puede usar la [Actividad de recursos de actualización](data-factory-azure-ml-batch-execution-activity.md#updating-models-using-update-resource-activity) para actualizar el servicio web con el modelo recién entrenado.

Consulte [Usar actividades de Machine Learning](data-factory-azure-ml-batch-execution-activity.md) para obtener más información sobre estas actividades de Machine Learning.

## Actividad de procedimiento almacenado
Puede usar la actividad de procedimiento almacenado de SQL Server en una canalización de Data Factory para invocar un procedimiento almacenado en uno de los siguientes almacenes de datos: Azure SQL Database, Azure SQL Data Warehouse y Base de datos de SQL Server en su empresa o una VM de Azure. Consulte el artículo [Actividad de procedimiento almacenado](data-factory-stored-proc-activity.md) para obtener más información.

## Actividad de U-SQL de Data Lake Analytics
La actividad de U-SQL de Data Lake Analytics ejecuta un script de U-SQL en un clúster de Azure Data Lake Analytics. Consulte el artículo [Actividad de U-SQL de Data Analytics](data-factory-usql-activity.md) para obtener más información.

## Actividad personalizada de .NET
Si necesita transformar datos de algún modo no compatible con Data Factory, puede crear una actividad personalizada con su propia lógica de procesamiento de datos y usarla en la canalización. Puede configurar una actividad de .NET personalizada para ejecutarse mediante un servicio Azure Batch o un clúster de Azure HDInsight. Consulte el artículo [Utilizar actividades personalizadas](data-factory-use-custom-activities.md) para obtener más información.

Puede crear una actividad personalizada para ejecutar scripts de R en su clúster de HDInsight con R instalado. Consulte [Run R Script using Azure Data Factory](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample) (Ejecutar script de R con Data Factory de Azure).

## Entornos de proceso
Deberá crear un servicio vinculado para el entorno de proceso y después usar el servicio vinculado al definir una actividad de transformación. La Factoría de datos admite dos tipos de entornos de proceso.

1. **A petición**: en este caso, el entorno informático es completamente administrado por la Factoría de datos. El servicio Factoría de datos lo crea automáticamente antes de que se envíe un trabajo para procesar los datos y que se quite cuando finalice el trabajo. Los usuarios pueden configurar y controlar la configuración granular del entorno de proceso a petición para la ejecución del trabajo, la administración del clúster y las acciones de arranque.
2. **Traiga el suyo propio**: en este caso, puede registrar su propio entorno informático (por ejemplo, clúster de HDInsight) como servicio vinculado en la Factoría de datos. El usuario administra el entorno de procesos y el servicio Factoría de datos lo usa para ejecutar las actividades.

Vea el artículo [Servicios vinculados de procesos](data-factory-compute-linked-services.md) para obtener información sobre los servicios vinculados de proceso compatibles con Data Factory.


## Resumen
Azure Data Factory admite las siguientes actividades de transformación de datos y los entornos de proceso para las actividades. Las actividades de transformación se pueden agregar a las canalizaciones tanto individualmente como encadenadas a otra actividad.

Actividad de transformación de datos | Entorno de procesos 
:----------------------- | :--------------------
[Hive](data-factory-hive-activity.md) | HDInsight [Hadoop]
[Pig](data-factory-pig-activity.md) | HDInsight [Hadoop]
[MapReduce](data-factory-map-reduce.md) | HDInsight [Hadoop]
[Hadoop Streaming](data-factory-hadoop-streaming-activity.md) | HDInsight [Hadoop]
[Machine Learning activities: Batch Execution and Update Resource](data-factory-azure-ml-batch-execution-activity.md) | MV de Azure 
[Procedimiento almacenado](data-factory-stored-proc-activity.md) | SQL Azure, Almacenamiento de datos SQL de Azure o SQL Server |
[U-SQL de análisis con Data Lake](data-factory-usql-activity.md) | Análisis con Azure Data Lake 
[DotNet](data-factory-use-custom-activities.md) | HDInsight [Hadoop] o Lote de Azure
   

<!---HONumber=AcomDC_0928_2016-->