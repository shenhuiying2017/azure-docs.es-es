<properties
	pageTitle="Ejemplo de recuento de palabras de MapReduce de Hadoop en HDInsight | Microsoft Azure"
	description="Ejecución de un ejemplo de recuento de palabras de MapReduce en un clúster de Hadoop en HDInsight. El programa, escrito en Java, cuenta las apariciones de palabras en un archivo de texto."
	editor="cgronlun"
	manager="paulettm"
	services="hdinsight"
	documentationCenter=""
	authors="mumian"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/09/2015" 
	ms.author="jgao"/>

#Ejecución de un ejemplo de recuento de palabras de MapReduce escrito en Java en un clúster de Hadoop en HDInsight

En este tutorial se muestra cómo ejecutar un ejemplo de recuento de palabras de MapReduce en un clúster de Hadoop en HDInsight. El programa está escrito en Java. Cuenta las repeticiones de palabras en un archivo de texto y, después, genera un nuevo archivo de texto que contiene cada palabra emparejada con su frecuencia de repetición. El archivo de texto analizado en esta muestra es la edición de Los cuadernos de Leonardo Da Vinci para el libro electrónico del proyecto Gutenberg.

> [AZURE.NOTE]Los pasos de este documento requieren un cliente de Windows. Para conocer los pasos sobre cómo utilizar el ejemplo de recuento de palabras de un cliente de Linux, OS X o Unix, con un clúster de HDInsight basado en Linux, consulte [Uso de MapReduce con Hadoop en HDInsight con SSH](hdinsight-hadoop-use-mapreduce-ssh.md) o [Uso de MapReduce con Hadoop en HDInsight con Curl](hdinsight-hadoop-use-mapreduce-curl.md).

**Aprenderá a:**

* Utilizar Azure PowerShell para ejecutar un programa de MapReduce en un clúster de HDInsight.
* Crear programas de MapReduce en Java.


**Requisitos previos**:

- **Una suscripción de Azure**. Vea [Obtener evaluación gratuita de Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- **Un clúster de HDInsight**. Para obtener instrucciones sobre las diversas formas de creación de dichos clústeres, consulte [Introducción a Azure HDInsight][hdinsight-get-started] o [Aprovisionamiento de clústeres de HDInsight](hdinsight-provision-clusters.md).

- **Una estación de trabajo con Azure PowerShell**. Consulte [Instalación y uso de Azure PowerShell](http://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/).



## <a id="run-sample"></a>Ejecución del ejemplo con Azure PowerShell</h2>

**Para enviar el trabajo de MapReduce**

1.	Abra la consola de **Azure PowerShell**. Para obtener más información, consulte [Instalación y configuración de Azure PowerShell][powershell-install-configure].

3. Ajuste las dos variables en los comandos siguientes y, a continuación, ejecútelos:

		$subscriptionName = "<SubscriptionName>"   # Azure subscription name
		$clusterName = "<ClusterName>"             # HDInsight cluster name

5. Ejecute el siguiente comando para crear una definición del trabajo de MapReduce:

		# Define the MapReduce job
		$wordCountJobDefinition = New-AzureHDInsightMapReduceJobDefinition -JarFile "wasb:///example/jars/hadoop-mapreduce-examples.jar" -ClassName "wordcount" -Arguments "wasb:///example/data/gutenberg/davinci.txt", "wasb:///example/data/WordCountOutput"

	> [AZURE.NOTE]*hadoop-examples.jar* viene con clústeres de versión 2.1 de HDInsight. Se cambió el nombre del archivo *hadoop-mapreduce.jar* en los clústeres de la versión 3.0 de HDInsight.

	El archivo hadoop-mapreduce-examples.jar se incluye con el clúster de HDInsight. Existen dos argumentos para el trabajo de MapReduce. El primero es el nombre del archivo de origen y, el segundo, la ruta de acceso del archivo de salida. El archivo de origen se incluye con el clúster de HDInsight y la ruta de acceso del archivo de salida se creará en tiempo de ejecución.

6. Ejecute el siguiente comando para enviar el trabajo de MapReduce:

		# Submit the job
		Select-AzureSubscription $subscriptionName
		$wordCountJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $wordCountJobDefinition | Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600  

	Además de la definición de trabajo de MapReduce, también debe proporcionar el nombre del clúster de HDInsight en el que desea ejecutar el trabajo de MapReduce.

8. Ejecute el siguiente comando para comprobar posibles errores al ejecutar el trabajo de MapReduce:

		# Get the job output
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $wordCountJob.JobId -StandardError

**Para recuperar los resultados del trabajo de MapReduce**

1. Abra la consola de **Azure PowerShell**.
2. Establezca tres variables en los comandos siguientes y, a continuación, ejecútelos:

		$subscriptionName = "<SubscriptionName>"       # Azure subscription name
		$storageAccountName = "<StorageAccountName>"   # Azure storage account name
		$containerName = "<ContainerName>"			   # Blob storage container name

	La cuenta de almacenamiento de Azure es la misma que se creó anteriormente en el tutorial. Se usa para hospedar el blob utilizado como sistema de archivos predeterminado del clúster de HDInsight. El nombre del contenedor del almacenamiento de blobs de Azure suele coincidir con el del clúster de HDInsight, a menos que se especifique un nombre distinto durante el aprovisionamiento del clúster.

3. Ejecute los siguientes comandos para crear un objeto de contexto de almacenamiento de Azure:

		# Select the current subscription
		Select-AzureSubscription $subscriptionName

		# Create the storage account context object
		$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
		$storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  

	**Select-AzureSubscription** se usa para establecer la suscripción actual en caso de tener varias y no es la suscripción predeterminada la que quiere usar.

4. Ejecute el siguiente comando para descargar el resultado del trabajo de MapReduce del blob a la estación de trabajo:

		# Download the job output to the workstation
		Get-AzureStorageBlobContent -Container $ContainerName -Blob example/data/WordCountOutput/part-r-00000 -Context $storageContext -Force

	La carpeta *example/data/WordCountOutput* es la carpeta de salida especificada al ejecutar el trabajo de MapReduce. *part-r-00000* es el nombre de archivo predeterminado para el resultado del trabajo de MapReduce. El archivo se descargará en la misma estructura de carpetas en la carpeta local. Por ejemplo, en la captura de pantalla siguiente, la carpeta actual es la carpeta raíz C. El archivo se descargará en la carpeta *C:\\example\\data\\WordCountOutput*.

5. Ejecute el siguiente comando para imprimir el archivo de salida del trabajo de MapReduce:

		cat ./example/data/WordCountOutput/part-r-00000 | findstr "there"


	El trabajo de MapReduce genera un archivo denominado *part-r-00000* con las palabras y los recuentos. El script usa el comando **findstr** para enumerar todas las palabras que contienen *"there*".

La salida del script de WordCount debe aparecer en la ventana Comandos:

![Resultados de frecuencia de palabras en PowerShell del ejemplo de recuento de palabras de MapReduce de Hadoop en HDInsight.][image-hdi-sample-wordcount-output]

Tenga en cuenta que los archivos de salida de un trabajo de MapReduce son inmutables. Por lo tanto, si vuelve a ejecutar este ejemplo, debe cambiar el nombre del archivo de salida.

## <a id="java-code"></a>El código Java del programa de MapReduce de WordCount</h2>



	package org.apache.hadoop.examples;
	import java.io.IOException;
	import java.util.StringTokenizer;
	import org.apache.hadoop.conf.Configuration;
	import org.apache.hadoop.fs.Path;
	import org.apache.hadoop.io.IntWritable;
	import org.apache.hadoop.io.Text;
	import org.apache.hadoop.mapreduce.Job;
	import org.apache.hadoop.mapreduce.Mapper;
	import org.apache.hadoop.mapreduce.Reducer;
	import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
	import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;
	import org.apache.hadoop.util.GenericOptionsParser;

	public class WordCount {

  	public static class TokenizerMapper
       extends Mapper<Object, Text, Text, IntWritable>{

    private final static IntWritable one = new IntWritable(1);
    private Text word = new Text();

    public void map(Object key, Text value, Context context
                    ) throws IOException, InterruptedException {
      StringTokenizer itr = new StringTokenizer(value.toString());
      while (itr.hasMoreTokens()) {
        word.set(itr.nextToken());
        context.write(word, one);
      	}
      }
  	}

  	public static class IntSumReducer
       extends Reducer<Text,IntWritable,Text,IntWritable> {
    private IntWritable result = new IntWritable();

    public void reduce(Text key, Iterable<IntWritable> values,
                       Context context
                       ) throws IOException, InterruptedException {
      int sum = 0;
      for (IntWritable val : values) {
        sum += val.get();
      }
      result.set(sum);
      context.write(key, result);
      }
  	}

  	public static void main(String[] args) throws Exception {
    Configuration conf = new Configuration();
    String[] otherArgs = new GenericOptionsParser(conf, args).getRemainingArgs();
    if (otherArgs.length != 2) {
      System.err.println("Usage: wordcount <in> <out>");
      System.exit(2);
    	}
    Job job = new Job(conf, "word count");
    job.setJarByClass(WordCount.class);
    job.setMapperClass(TokenizerMapper.class);
    job.setCombinerClass(IntSumReducer.class);
    job.setReducerClass(IntSumReducer.class);
    job.setOutputKeyClass(Text.class);
    job.setOutputValueClass(IntWritable.class);
    FileInputFormat.addInputPath(job, new Path(otherArgs[0]));
    FileOutputFormat.setOutputPath(job, new Path(otherArgs[1]));
    System.exit(job.waitForCompletion(true) ? 0 : 1);
  	}
  	}



En este tutorial, ha visto cómo ejecutar un programa de MapReduce que cuenta las ocurrencias de palabras en un archivo de texto con HDInsight mediante el uso de Azure PowerShell.

## <a id="next-steps"></a>Pasos siguientes</h2>

Para ver tutoriales que ejecutan otras muestras y ofrecen instrucciones sobre el uso de Pig, Hive y trabajos de MapReduce en HDInsight de Azure con Azure PowerShell, consulte:

* [Introducción a HDInsight de Azure][hdinsight-get-started]
* [Muestra: GraySort de 10 GB][hdinsight-sample-10gb-graysort]
* [Muestra: estimador de pi][hdinsight-sample-pi-estimator]
* [Muestra: streaming en C#][hdinsight-sample-cs-streaming]
* [Uso de Pig con HDInsight][hdinsight-use-pig]
* [Uso de Hive con HDInsight][hdinsight-use-hive]
* [Documentación de SDK de HDInsight de Azure][hdinsight-sdk-documentation]

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx

[hdinsight-sample-10gb-graysort]: hdinsight-sample-10gb-graysort.md
[hdinsight-sample-pi-estimator]: hdinsight-sample-pi-estimator.md
[hdinsight-sample-cs-streaming]: hdinsight-sample-csharp-streaming.md


[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md

[hdinsight-get-started]: ../hdinsight-get-started.md

[powershell-install-configure]: ../install-configure-powershell.md

[image-hdi-sample-wordcount-output]: ./media/hdinsight-sample-wordcount/HDI.Sample.WordCount.Output.png
 

<!---HONumber=August15_HO6-->