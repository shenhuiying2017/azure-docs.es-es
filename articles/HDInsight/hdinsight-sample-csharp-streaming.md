<properties
	pageTitle="Ejemplo de Hadoop de recuento de palabras por streaming de C# en HDInsight | Azure"
	description="Cómo escribir programas de MapReduce en C # que usen la interfaz de streaming de Hadoop y cómo ejecutarlos en HDInsight con cmdlets de PowerShell."
	editor="cgronlun"
	manager="paulettm"
	services="hdinsight"
	documentationCenter=""
	authors="bradsev"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="03/30/2014" 
	ms.author="bradsev"/>

# Ejemplo de MapReduce de contar palabras por streaming de C# en Hadoop en HDInsight

Hadoop ofrece una API de streaming para MapReduce que le permite escribir mapas y reducir funciones en lenguajes distintos de Java. En este tutorial se muestra cómo escribir programas de MapReduce en C # mediante la interfaz de streaming de Hadoop y cómo ejecutar dichos programas en HDInsight de Azure con cmdlets de Azure PowerShell.

> [AZURE.NOTE]Los pasos de este tutorial solo se aplican a los clústeres de HDInsight basados en Windows. Para obtener un ejemplo de streaming para clústeres de HDInsight basados en Linux, consulte [Desarrollo de programas de streaming en Python para HDInsight](hdinsight-hadoop-streaming-python.md).

En el ejemplo, el asignador y el reductor son ejecutables que leen la entrada desde [stdin][stdin-stdout-stderr] (línea a línea) y emiten la salida en [stdout][stdin-stdout-stderr]. El programa cuenta todas las palabras del texto.

Cuando se especifica un archivo ejecutable para los **asignadores**, cada tarea del asignador inicia el ejecutable como un proceso aparte al inicializar el asignador. A medida que se ejecuta la tarea del asignador, convierte sus entradas en líneas y proporciona las líneas al elemento [stdin][stdin-stdout-stderr] del proceso.

Mientras tanto, el asignador recopila la salida orientada a líneas desde el elemento stdout del proceso. Convierte cada línea en un par de clave y valor, que se recopila como la salida del asignador. De manera predeterminada, el prefijo de una línea hasta el primer carácter de tabulación es la clave y el resto de la línea (sin incluir el carácter de tabulación) es el valor. Si no hay un carácter de tabulación en la línea, se considera que toda la línea es la clave y el valor es nulo.

Cuando se especifica un archivo ejecutable para los **reductores**, cada tarea del reductor inicia el ejecutable como un proceso aparte al inicializar el reductor. Mientras se ejecuta la tarea del reductor, convierte sus pares clave-valor de entrada en líneas y proporciona las líneas al elemento [stdin][stdin-stdout-stderr] del proceso.

Mientras tanto, el reductor recopila la salida orientada a líneas desde el elemento [stdout][stdin-stdout-stderr] del proceso. Convierte cada línea en un par de clave y valor, que se recopila como la salida del reductor. De manera predeterminada, el prefijo de una línea hasta el primer carácter de tabulación es la clave y el resto de la línea (sin incluir el carácter de tabulación) es el valor.

Para obtener más información sobre la interfaz de streaming de Hadoop, consulte [Streaming de Hadoop][hadoop-streaming].

**En este tutorial, aprenderá a:**

* Usar Azure PowerShell para ejecutar un programa de streaming en C# para analizar los datos contenidos en un archivo en HDInsight.
* Escribir código C# que usa la interfaz de streaming de Hadoop.


**Requisitos previos**:

Antes de empezar, debe disponer de lo siguiente:

- Una cuenta de Azure. Si desea conocer las opciones para obtener una cuenta, consulte la página de la [prueba gratuita de Azure](http://azure.microsoft.com/pricing/free-trial/).

- Un clúster de HDInsight aprovisionado. Para obtener instrucciones sobre las distintas formas de creación de dichos clústeres, consulte [Aprovisionamiento de clústeres de HDInsight](hdinsight-provision-clusters.md).

- Azure PowerShell. Debe configurarse para su uso con su cuenta. Para obtener instrucciones sobre cómo hacerlo, consulte [Instalación y configuración de Azure PowerShell][powershell-install-configure].


## <a id="run-sample"></a>Ejecución de la muestra con Azure PowerShell

**Para ejecutar el trabajo de MapReduce**

1.	Abra **Azure PowerShell**. Para obtener instrucciones sobre cómo abrir la ventana de la consola de Azure PowerShell, consulte [Instalación y configuración de Azure PowerShell][powershell-install-configure].

3. Ajuste las dos variables en los comandos siguientes y, a continuación, ejecútelos:

		$subscriptionName = "<SubscriptionName>"   # Azure subscription name
		$clusterName = "<ClusterName>"             # HDInsight cluster name


2. Ejecute el siguiente comando para definir el trabajo de MapReduce:

		# Create a MapReduce job definition for the streaming job.
		$streamingWC = New-AzureHDInsightStreamingMapReduceJobDefinition -Files "/example/apps/wc.exe", "/example/apps/cat.exe" -InputPath "/example/data/gutenberg/davinci.txt" -OutputPath "/example/data/StreamingOutput/wc.txt" -Mapper "cat.exe" -Reducer "wc.exe"

	Los parámetros especifican las funciones del asignador y el reductor, además de los archivos de entrada y salida.

5. Ejecute los siguientes comandos para ejecutar el trabajo de MapReduce, espere a que el trabajo finalice y, a continuación, imprima el mensaje de error estándar:

		# Run the C# Streaming MapReduce job.
		# Wait for the job to complete.
		# Print output and standard error file of the MapReduce job
		Select-AzureSubscription $subscriptionName
		$streamingWC | Start-AzureHDInsightJob -Cluster $clustername | Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 | Get-AzureHDInsightJobOutput -Cluster $clustername -StandardError

6. Ejecute los siguientes comandos para mostrar los resultados del recuento de palabras:

		$subscriptionName = "<SubscriptionName>"
		$storageAccountName = "<StorageAccountName>"
		$containerName = "<ContainerName>"

		# Select the current subscription
		Select-AzureSubscription $subscriptionName

		# Blob storage container and account name
      $storageAccountKey = Get-AzureStorageKey -StorageAccountName $storageAccountName | %{ $_.Primary } $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey

		# Retrieve the output
		Get-AzureStorageBlobContent -Container $containerName -Blob "example/data/StreamingOutput/wc.txt/part-00000" -Context $storageContext -Force

		# The number of words in the text is:
		cat ./example/data/StreamingOutput/wc.txt/part-00000

	Tenga en cuenta que los archivos de salida de un trabajo de MapReduce son inmutables. Por lo tanto, si vuelve a ejecutar esta muestra, debe cambiar el nombre del archivo de salida.

##<a id="java-code"></a>El código de C# para streaming de Hadoop

El programa de MapReduce utiliza la aplicación cat.exe como una interfaz de asignación para transmitir el texto en la consola y la aplicación wc.exe como la interfaz de reducción para contar el número de palabras que se transmiten desde un documento. Tanto el asignador como el reductor leen caracteres, línea por línea, a partir de la secuencia de entrada estándar (stdin) y escriben en la secuencia de salida estándar (stdout).



	// The source code for the cat.exe (Mapper).

	using System;
	using System.IO;

	namespace cat
	{
	    class cat
	    {
	        static void Main(string[] args)
	        {
	            if (args.Length > 0)
	            {
	                Console.SetIn(new StreamReader(args[0]));
	            }

	            string line;
	            while ((line = Console.ReadLine()) != null)
	            {
	                Console.WriteLine(line);
	            }
	        }
	    }
	}



El código del asignador en el archivo cat.cs usa un objeto [StreamReader][streamreader] para leer los caracteres de la transmisión entrante en la consola, la cual escribe a continuación la transmisión en la secuencia de salida estándar con el método [Console.Writeline][console-writeline] estático.


	// The source code for wc.exe (Reducer) is:

	using System;
	using System.IO;
	using System.Linq;

	namespace wc
	{
	    class wc
	    {
	        static void Main(string[] args)
	        {
	            string line;
	            var count = 0;

	            if (args.Length > 0){
	                Console.SetIn(new StreamReader(args[0]));
	            }

	            while ((line = Console.ReadLine()) != null) {
	                count += line.Count(cr => (cr == ' ' || cr == '\n'));
	            }
	            Console.WriteLine(count);
	        }
	    }
	}


El código reductor en el archivo wc.cs usa un objeto [StreamReader][streamreader] para leer caracteres de la secuencia de entrada estándar que han sido de salida por el asignador cat.exe. A medida que lee los caracteres con el método [Console.Writeline][console-writeline], cuenta las palabras contando espacios y caracteres de fin de línea al final de cada palabra. A continuación, escribe el total en la secuencia de salida estándar con el método [Console.Writeline][console-writeline].

##<a id="summary"></a>Resumen

En este tutorial, ha visto la manera de implementar un trabajo de MapReduce en HDInsight con la ayuda del streaming de Hadoop.

##<a id="next-steps"></a>Pasos siguientes

Para ver tutoriales que ejecutan otras muestras y ofrecen instrucciones sobre la ejecución de Pig, Hive y los trabajos de MapReduce en HDInsight de Azure con Azure PowerShell, consulte los siguientes artículos:

* [Introducción a HDInsight de Azure][hdinsight-get-started]
* [Muestra: estimador de pi][hdinsight-sample-pi-estimator]
* [Muestra: contar palabras][hdinsight-sample-wordcount]
* [Muestra: GraySort de 10 GB][hdinsight-sample-10gb-graysort]
* [Uso de Pig con HDInsight][hdinsight-use-pig]
* [Uso de Hive con HDInsight][hdinsight-use-hive]
* [Documentación de SDK de HDInsight de Azure][hdinsight-sdk-documentation]

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx

[hadoop-streaming]: http://wiki.apache.org/hadoop/HadoopStreaming
[streamreader]: http://msdn.microsoft.com/library/system.io.streamreader.aspx
[console-writeline]: http://msdn.microsoft.com/library/system.console.writeline
[stdin-stdout-stderr]: http://msdn.microsoft.com/library/3x292kth(v=vs.110).aspx

[powershell-install-configure]: ../install-configure-powershell.md

[hdinsight-get-started]: ../hdinsight-get-started.md

[hdinsight-samples]: hdinsight-run-samples.md
[hdinsight-sample-10gb-graysort]: hdinsight-sample-10gb-graysort.md
[hdinsight-sample-csharp-streaming]: hdinsight-sample-csharp-streaming.md
[hdinsight-sample-pi-estimator]: hdinsight-sample-pi-estimator.md
[hdinsight-sample-wordcount]: hdinsight-sample-wordcount.md

[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md

<!--HONumber=54--> 