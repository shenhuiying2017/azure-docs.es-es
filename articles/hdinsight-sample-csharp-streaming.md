<properties urlDisplayName="Hadoop Samples in HDInsight" pageTitle="Ejemplo de Hadoop de recuento de palabras por streaming de C# en HDInsight | Azure" metaKeywords="hadoop, hdinsight, hdinsight administration, hdinsight administration azure" description="Vea cómo ejecutar un ejemplo TBD." umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" services="hdinsight" documentationCenter="" title="The C# streaming wordcount Hadoop sample in HDInsight" authors="bradsev" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="11/10/2014" ms.author="bradsev" />

# Ejemplo de Hadoop de recuento de palabras por streaming de C# en HDInsight
 
Hadoop proporciona una API de streaming para MapReduce que le permite escribir mapas y reducir funciones en lenguajes distintos de Java. En este tutorial se muestra cómo escribir programas de MapReduce en C # que usa la interfaz de streaming de Hadoop y cómo ejecutar los programas en HDInsight de Azure con cmdlets de Azure PowerShell. 

En el ejemplo, tanto el asignador como el reductor son ejecutables que leen la entrada desde [stdin][stdin-stdout-stderr] (línea a línea) y emiten la salida en [stdout][stdin-stdout-stderr]. El programa cuenta todas las palabras del texto.

Cuando se especifica un archivo ejecutable para los **asignadores**, cada tarea del asignador inicia el ejecutable como un proceso aparte al inicializar el asignador. Mientras se ejecuta la tarea del asignador, convierte sus entradas en líneas y alimenta las líneas a [stdin][stdin-stdout-stderr] del proceso. Mientras tanto, el asignador recopila las salidas orientadas por las líneas desde el stdout del proceso y convierte cada línea en un par clave-valor, que se recopila como la salida del asignador. De manera predeterminada, el prefijo de una línea hasta el primer carácter de tabulación es la clave y el resto de la línea (sin incluir el carácter de tabulación) es el valor. Si no hay un carácter de tabulación en la línea, entonces se considera toda la línea como una clave y el valor es nulo. 

Cuando se especifica un archivo ejecutable para los **reductores**, cada tarea del reductor inicia el ejecutable como un proceso aparte al inicializar el reductor. Mientras se ejecuta la tarea del reductor, convierte sus pares clave-valor de entrada en líneas y alimenta las líneas a [stdin][stdin-stdout-stderr] del proceso. Mientras tanto, el reductor recopila las salidas orientadas por las líneas desde el [stdout][stdin-stdout-stderr] del proceso y convierte cada línea en un par clave-valor, que se recopila como la salida del reductor. De manera predeterminada, el prefijo de una línea hasta el primer carácter de tabulación es la clave y el resto de la línea (sin incluir el carácter de tabulación) es el valor. 

Para obtener más información acerca de la interfaz de streaming de Hadoop, consulte [Hadoop Streaming][hadoop-streaming]. 
 
**Aprenderá a:**	
	
* Usar Azure PowerShell para ejecutar un programa de streaming en C# para analizar los datos contenidos en un archivo en HDInsight.		
* Escribir código C# que usa la interfaz de streaming de Hadoop.


**Requisitos previos**:	

- Debe tener una cuenta de Azure. Para conocer las opciones para obtener una cuenta, consulte la página de la [prueba gratuita de Azure](http://azure.microsoft.com/en-us/pricing/free-trial/) .

- Debe aprovisionar un clúster de HDInsight. Para obtener instrucciones acerca de varias formas de creación de dichos clústeres, consulte [Aprovisionamiento de clústeres de HDInsight](../hdinsight-provision-clusters/)

- Debe tener instalado Azure PowerShell y haber configurado los clústeres para utilizarlos con su cuenta. Para obtener instrucciones acerca de cómo hacerlo, consulte [Instalación y configuración de Azure PowerShell][powershell-install-configure].


##En este artículo
En este tema se muestra cómo ejecutar la muestra, presenta el código de Java para el programa de MapReduce, resume lo que ha aprendido y se describen algunos pasos a seguir. Tiene las siguientes secciones.
	
1. [Ejecución de la muestra con Azure PowerShell](#run-sample)	
2. [El código de C# para Hadoop Streaming](#java-code)
3. [Resumen](#summary)	
4. [Pasos siguientes](#next-steps)	

<h2><a id="run-sample"></a>Ejecución de la muestra con Azure PowerShell</h2>

**Para ejecutar el trabajo de MapReduce, siga estos pasos:**

1.	Abra **Azure PowerShell**. Para obtener instrucciones acerca de cómo abrir la ventana de la consola de Azure PowerShell, consulte [Instalación y configuración de Azure PowerShell][powershell-install-configure].

3. Ajuste las dos variables en los comandos siguientes y, a continuación, ejecútelos:
		
		$subscriptionName = "<SubscriptionName>"   # Azure subscription name
		$clusterName = "<ClusterName>"             # HDInsight cluster name


2. Ejecute el siguiente comando para definir el trabajo de MapReduce.
 
		# Create a MapReduce job definition for the streaming job.
		$streamingWC = New-AzureHDInsightStreamingMapReduceJobDefinition -Files "/example/apps/wc.exe", "/example/apps/cat.exe" -InputPath "/example/data/gutenberg/davinci.txt" -OutputPath "/example/data/StreamingOutput/wc.txt" -Mapper "cat.exe" -Reducer "wc.exe" 

	Los parámetros especifican las funciones del asignador y el reductor, además de los archivos de entrada y salida.
                 
5. Ejecute los siguientes comandos para ejecutar el trabajo de MapReduce, espere a que el trabajo finalice y, a continuación, imprima el error estándar:

		# Run the C# Streaming MapReduce job.
		# Wait for the job to complete.
		# Print output and standard error file of the MapReduce job
		Select-AzureSubscription $subscriptionName
		$streamingWC | Start-AzureHDInsightJob -Cluster $clustername | Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 | Get-AzureHDInsightJobOutput -Cluster $clustername -StandardError 

6. Ejecute los siguientes comandos para mostrar los resultados del recuento de palabras.

		$subscriptionName = "<SubscriptionName>"   
		$storageAccountName = "<StorageAccountName>" 
		$containerName = "<ContainerName>"

		# Select the current subscription
		Select-AzureSubscription $subscriptionName
              
		# Blob storage container and account name
      $storageAccountKey = Get-AzureStorageKey -StorageAccountName $storageAccountName | %{ $_.Primary }
      $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
 
		# Retrieve the output
		Get-AzureStorageBlobContent -Container $containerName -Blob "example/data/StreamingOutput/wc.txt/part-00000" -Context $storageContext -Force 

		# The number of words in the text is:
		cat ./example/data/StreamingOutput/wc.txt/part-00000

	Tenga en cuenta que los archivos de salida de un trabajo de MapReduce son inmutables. Por lo tanto, si vuelve a ejecutar esta muestra tendrá que cambiar el nombre del archivo de salida.
	
<h2><a id="java-code"></a>El código de C# para streaming de Hadoop</h2>

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

 

El código del asignador en el archivo cat.cs usa un objeto StreamReader para leer los caracteres de la transmisión entrante en la consola, la cual a su vez escribe la transmisión en la secuencia de salida estándar con el método Console.Writeline estático.


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


El código reductor en el archivo wc.cs usa un objeto [StreamReader][streamreader]   para leer caracteres de la secuencia de entrada estándar que han sido de salida por el asignador cat.exe. A medida que lee los caracteres con el método [Console.Writeline][console-writeline], cuenta las palabras contando el espacio y los caracteres de fin de línea al final de cada palabra y, a continuación, escribe el total de la secuencia de salida estándar con el método [Console.Writeline][console-writeline]. 

<h2><a id="summary"></a>Resumen</h2>

En este tutorial, ha observado la manera de implementar un trabajo de MapReduce en HDInsight con la ayuda del streaming de Hadoop.

<h2><a id="next-steps"></a>Pasos siguientes</h2>

Para ver tutoriales que ejecutan otras muestras y proporcionan instrucciones sobre el uso de Pig, Hive y los trabajos de MapReduce en HDInsight de Azure con Azure PowerShell, consulte los siguientes temas:

* [Introducción a Azure HDInsight][hdinsight-get-started]
* [Muestra: Estimador de Pi][hdinsight-sample-pi-estimator]
* [Muestra: Wordcount][hdinsight-sample-wordcount]
* [Muestra: GraySort de 10 GB][hdinsight-sample-10gb-graysort]
* [Uso de Pig con HDInsight][hdinsight-use-pig]
* [Uso de Hive con HDInsight][hdinsight-use-hive]
* [Documentación del SDK de HDInsight de Azure][hdinsight-sdk-documentation]

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/en-us/library/dn479185.aspx

[hadoop-streaming]: http://wiki.apache.org/hadoop/HadoopStreaming
[streamreader]: http://msdn.microsoft.com/en-us/library/system.io.streamreader.aspx
[console-writeline]: http://msdn.microsoft.com/en-us/library/system.console.writeline
[stdin-stdout-stderr]: http://msdn.microsoft.com/en-us/library/3x292kth(v=vs.110).aspx

[Powershell-install-configure]: ../install-configure-powershell/

[hdinsight-get-started]: ../hdinsight-get-started/

[hdinsight-samples]: ../hdinsight-run-samples/
[hdinsight-sample-10gb-graysort]: ../hdinsight-sample-10gb-graysort/
[hdinsight-sample-csharp-streaming]: ../hdinsight-sample-csharp-streaming/
[hdinsight-sample-pi-estimator]: ../hdinsight-sample-pi-estimator/
[hdinsight-sample-wordcount]: ../hdinsight-sample-wordcount/

[hdinsight-use-hive]: ../hdinsight-use-hive/
[hdinsight-use-pig]: ../hdinsight-use-pig/



<!--HONumber=35.1-->
