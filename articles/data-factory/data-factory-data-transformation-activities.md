<properties 
	pageTitle="Transformación de datos: procesamiento y transformación de datos| Microsoft Azure" 
	description="Obtenga más información sobre la transformación de datos en Data Factory de Azure. Transforme y procese los datos en el clúster de HDInsight de Azure o en un Lote de Azure." 
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
	ms.date="09/12/2016" 
	ms.author="spelluru"/>

# Más información sobre la transformación y el análisis de datos en Data Factory de Azure
En este artículo se explican las actividades de transformación de datos de Data Factory de Azure para saber cómo ADF transforma y procesa los datos sin procesar en predicciones e información. La actividad de transformación se ejecuta en un entorno informático, como clúster de HDInsight de Azure o un Lote de Azure. También se incluyen vínculos a algunos artículos que muestran cómo usar actividades de transformación concretas.
 
La Factoría de datos de Azure admite las siguientes actividades de transformación que se pueden agregar a[canalizaciones](data-factory-create-pipelines.md) ya sea individualmente o encadenadas a otra actividad.

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
   
> [AZURE.NOTE] 
Puede usar la actividad MapReduce para ejecutar programas Spark en su clúster de HDInsight Spark. Consulte [Invocar programas Spark desde Data Factory de Azure](data-factory-spark.md) para obtener más información. Puede crear una actividad personalizada para ejecutar scripts de R en su clúster de HDInsight con R instalado. Consulte [Run R Script using Azure Data Factory](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample) (Ejecutar script de R con Data Factory de Azure).
 

Deberá crear un servicio vinculado para el entorno de proceso y después usar el servicio vinculado al definir una actividad de transformación. La Factoría de datos admite dos tipos de entornos de proceso.

1. **A petición**: en este caso, el entorno informático es completamente administrado por la Factoría de datos. El servicio Factoría de datos lo crea automáticamente antes de que se envíe un trabajo para procesar los datos y que se quite cuando finalice el trabajo. Los usuarios pueden configurar y controlar la configuración granular del entorno de proceso a petición para la ejecución del trabajo, la administración del clúster y las acciones de arranque.
2. **Traiga el suyo propio**: en este caso, puede registrar su propio entorno informático (por ejemplo, clúster de HDInsight) como servicio vinculado en la Factoría de datos. El usuario administra el entorno de procesos y el servicio Factoría de datos lo usa para ejecutar las actividades.

Vea el artículo [Servicios vinculados de procesos](data-factory-compute-linked-services.md) para obtener información sobre los servicios vinculados de proceso compatibles con Data Factory.

<!---HONumber=AcomDC_0914_2016-->