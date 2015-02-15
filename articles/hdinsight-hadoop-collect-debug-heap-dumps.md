<properties 
	pageTitle="Recopilación de volcados de memoria para la depuración y el análisis | Azure" 
	description="Recopilación de volcados de memoria para la depuración y el análisis" 
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
	ms.date="12/08/2014" 
	ms.author="bradsev"/>

# Recopilación de volcados de memoria para la depuración y el análisis

Los volcados de memoria pueden recopilarse automáticamente para servicios Hadoop y se colocan en la cuenta de almacenamiento blob de un usuario en HDInsightHeapDumps /.  Los archivos de volcado de memoria de un servicio contienen una instantánea de la memoria de la aplicación. Esto incluye los valores de las variables en el momento en que se creó el volcado de memoria.

La recopilación de volcados de memoria para los distintos servicios debe habilitarse para los servicios en clústeres individuales. De forma predeterminada, esta característica está desactivada para un clúster. Los volcados de memoria pueden ser de gran tamaño, por lo que se recomienda montón pueden ser grandes de tamaño por lo que se recomienda supervisar la cuenta de almacenamiento blob en la que se van a guardar tras habilitar la recopilación.

## En este artículo

- [¿Para qué servicios se pueden habilitar los volcados de memoria?](#whichServices)
- [Elementos de configuración que permiten los volcados de memoria](#configuration)
- [Cómo habilitar los volcados de memoria con el PowerShell de Azure HDInsight](#powershell)
- [Cómo habilitar los volcados de memoria con el SDK de HDInsight .NET](#sdk)


## <a name="whichServices"></a>¿Para qué servicios se pueden habilitar los volcados de memoria?

Los servicios que pueden tener habilitados los volcados de memoria son los siguientes: 

*  **hcatalog**: tempelton
*  **hive**: hiveserver2, metastore, derbyserver 
*  **mapreduce**: jobhistoryserver 
*  **yarn**: resourcemanager, nodemanager, timelineserver 
*  **hdfs**: datanode, secondarynamenode, namenode

## <a name="configuration"></a>Elementos de configuración que permiten los volcados de memoria

Para activar el volcado de memoria para un servicio, el usuario deberá establecer los elementos de configuración adecuados en la sección de dicho servicio, que se especifica mediante el service_name.

	"javaargs.<service_name>.XX:+HeapDumpOnOutOfMemoryError" = "-XX:+HeapDumpOnOutOfMemoryError",
	"javaargs.<service_name>.XX:HeapDumpPath" = "-XX:HeapDumpPath=c:\Dumps\<service_name>_%date:~4,2%%date:~7,2%%date:~10,2%%time:~0,2%%time:~3,2%%time:~6,2%.hprof" 

El valor <service_name> puede ser cualquiera de los servicios enumerados anteriormente: tempelton, hiveserver2, metastore, derbyserver, jobhistoryserver, resourcemanager, nodemanager, timelineserver, datanode, secondarynamenode o namenode.

## <a name="powershell"></a>Cómo habilitar los volcados de memoria con el PowerShell de Azure HDInsight

Por ejemplo, para activar el volcado de memoria mediante PowerShell para el servicio jobhistoryserver, el usuario debe hacer lo siguiente:

Con el sdk de PowerShell:

	$MapRedConfigValues = new-object 'Microsoft.WindowsAzure.Management.HDInsight.Cmdlet.DataObjects.AzureHDInsightMapReduceConfiguration'

	$MapRedConfigValues.Configuration = @{ "javaargs.jobhistoryserver.XX:+HeapDumpOnOutOfMemoryError"="-XX:+HeapDumpOnOutOfMemoryError" ; "javaargs.jobhistoryserver.XX:HeapDumpPath" = "-XX:HeapDumpPath=c:\\Dumps\\jobhistoryserver_%date:~4,2%_%date:~7,2%_%date:~10,2%_%time:~0,2%_%time:~3,2%_%time:~6,2%.hprof" }

## <a name="sdk"></a>Cómo habilitar los volcados de memoria con el SDK de Azure HDInsight .NET

Por ejemplo, para activar el volcado de memoria con el SDK .NET para el servicio jobhistoryserver, el usuario debe hacer lo siguiente:

Con el sdk de c#:

	clusterInfo.MapReduceConfiguration.ConfigurationCollection.Add(new KeyValuePair<string, string>("javaargs.jobhistoryserver.XX:+HeapDumpOnOutOfMemoryError", "-XX:+HeapDumpOnOutOfMemoryError"));

	clusterInfo.MapReduceConfiguration.ConfigurationCollection.Add(new KeyValuePair<string, string>("javaargs.jobhistoryserver.XX:HeapDumpPath", "-XX:HeapDumpPath=c:\\Dumps\\jobhistoryserver_%date:~4,2%_%date:~7,2%_%date:~10,2%_%time:~0,2%_%time:~3,2%_%time:~6,2%.hprof"));




<!--HONumber=42-->
