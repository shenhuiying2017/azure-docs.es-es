<properties
	pageTitle="Depurar y analizar servicios de Hadoop con volcados de memoria | Microsoft Azure"
	description="Recopile automáticamente volcados de memoria para servicios de Hadoop y coloque dentro de la cuenta de almacenamiento de blobs de Azure para depuración y análisis."
	services="hdinsight"
	documentationCenter=""
	authors="bradsev"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/31/2015"
	ms.author="bradsev"/>


# Recopilar volcados de memoria en el almacenamiento de blobs para depurar y analizar servicios de Hadoop

[AZURE.INCLUDE [selector de volcado de memoria](../../includes/hdinsight-selector-heap-dump.md)]

Los volcados de memoria pueden recopilarse automáticamente para servicios Hadoop y se colocan en la cuenta de almacenamiento de blobs de Azure de un usuario en HDInsightHeapDumps/. Los archivos de volcado de memoria de un servicio contienen una instantánea de la memoria de la aplicación. Esto incluye los valores de las variables en el momento en que se creó el volcado de memoria.

La recopilación de volcados de memoria para los distintos servicios debe habilitarse para los servicios en clústeres individuales. De forma predeterminada, esta característica está desactivada para un clúster. Los volcados de memoria pueden ser de gran tamaño, por lo que se recomienda supervisar la cuenta de almacenamiento de blobs en la que se van a guardar tras habilitar la recopilación.

> [AZURE.NOTE]La información de este artículo solo se aplica al HDInsight basado en Windows. Para obtener más información sobre el HDInsight basado en Linux, consulte [Habilitar volcados de memoria para servicios de Hadoop en el HDInsight basado en Linux](hdinsight-hadoop-collect-debug-heap-dump-linux.md)

## <a name="whichServices"></a>Servicios de volcados de memoria aptos

Los servicios que pueden tener habilitados los volcados de memoria, si se solicitan, son los siguientes:

*  **hcatalog** - tempelton
*  **hive** - hiveserver2, metastore, derbyserver
*  **mapreduce** - jobhistoryserver
*  **yarn** - resourcemanager, nodemanager, timelineserver
*  **hdfs** - datanode, secondarynamenode, namenode

## <a name="configuration"></a>Elementos de configuración que habilitan los volcados de memoria

Para activar el volcado de memoria para un servicio, el usuario deberá definir los elementos de configuración adecuados en la sección de dicho servicio, que se especifica mediante **nombre_servicio**.

	"javaargs.<service_name>.XX:+HeapDumpOnOutOfMemoryError" = "-XX:+HeapDumpOnOutOfMemoryError",
	"javaargs.<service_name>.XX:HeapDumpPath" = "-XX:HeapDumpPath=c:\Dumps<service_name>_%date:~4,2%%date:~7,2%%date:~10,2%%time:~0,2%%time:~3,2%%time:~6,2%.hprof"

El valor de **nombre_servicio** puede ser cualquiera de los servicios enumerados anteriormente: tempelton, hiveserver2, metastore, derbyserver, jobhistoryserver, resourcemanager, nodemanager, timelineserver, datanode, secondarynamenode o namenode.

## <a name="powershell"></a>Cómo habilitar los volcados de memoria con Azure PowerShell

Por ejemplo, para activar los volcados de memoria mediante Azure PowerShell para jobhistoryserver, el usuario debe hacer lo siguiente:

	$MapRedConfigValues = new-object 'Microsoft.WindowsAzure.Management.HDInsight.Cmdlet.DataObjects.AzureHDInsightMapReduceConfiguration'

	$MapRedConfigValues.Configuration = @{ "javaargs.jobhistoryserver.XX:+HeapDumpOnOutOfMemoryError"="-XX:+HeapDumpOnOutOfMemoryError" ; "javaargs.jobhistoryserver.XX:HeapDumpPath" = "-XX:HeapDumpPath=c:\\Dumps\\jobhistoryserver_%date:~4,2%_%date:~7,2%_%date:~10,2%_%time:~0,2%_%time:~3,2%_%time:~6,2%.hprof" }

## <a name="sdk"></a>Habilitar los volcados de memoria usando el SDK de .NET de HDInsight de Azure

Por ejemplo, para activar los volcados de memoria mediante el SDK de .NET de HDInsight de Azure para jobhistoryserver, el usuario debe hacer lo siguiente:

	clusterInfo.MapReduceConfiguration.ConfigurationCollection.Add(new KeyValuePair<string, string>("javaargs.jobhistoryserver.XX:+HeapDumpOnOutOfMemoryError", "-XX:+HeapDumpOnOutOfMemoryError"));

	clusterInfo.MapReduceConfiguration.ConfigurationCollection.Add(new KeyValuePair<string, string>("javaargs.jobhistoryserver.XX:HeapDumpPath", "-XX:HeapDumpPath=c:\\Dumps\\jobhistoryserver_%date:~4,2%_%date:~7,2%_%date:~10,2%_%time:~0,2%_%time:~3,2%_%time:~6,2%.hprof"));

<!---HONumber=July15_HO2-->