<properties
	pageTitle="Ejemplo de recuento de palabras de MapReduce de Hadoop en HDInsight | Microsoft Azure"
	description="Ejecución de un ejemplo de recuento de palabras de MapReduce en un clúster de Hadoop en HDInsight. El programa, escrito en Java, cuenta las apariciones de palabras en un archivo de texto."
	editor="cgronlun"
	manager="paulettm"
	services="hdinsight"
	documentationCenter=""
	tags="azure-portal"
	authors="mumian"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/15/2015"
	ms.author="jgao"/>

#Ejecución del programa de MapReduce de recuento de palabras en el clúster de Hadoop en HDInsight

Obtenga información acerca de cómo usar un programa de MapReduce en un clúster de Hadoop en HDInsight mediante Azure PowerShell. El programa, escrito en Java, cuenta las repeticiones de palabras en un archivo de texto y, después, genera un nuevo archivo de texto que contiene cada palabra emparejada con su frecuencia de repetición.

El programa se instala en los clústeres. El archivo de texto analizado en este tutorial es la edición para libro electrónico de Los cuadernos de Leonardo Da Vinci de Project Gutenberg.

> [AZURE.NOTE]Los pasos de este documento requieren un cliente de Windows. Para conocer los pasos sobre cómo utilizar el ejemplo de recuento de palabras de un cliente de Linux, OS X o Unix, con un clúster de HDInsight basado en Linux, consulte [Uso de MapReduce con Hadoop en HDInsight con SSH](hdinsight-hadoop-use-mapreduce-ssh.md) o [Uso de MapReduce con Hadoop en HDInsight con Curl](hdinsight-hadoop-use-mapreduce-curl.md).

**Otros artículos relacionados:**

* [Introducción a HDInsight de Azure][hdinsight-get-started]
* [Desarrollo de programas MapReduce de Java para Hadoop en HDInsight](hdinsight-develop-deploy-java-mapreduce.md)
* [Envío de trabajos de Hadoop en HDInsight](hdinsight-submit-hadoop-jobs-programmatically.md)
* [Muestra: GraySort de 10 GB][hdinsight-sample-10gb-graysort]
* [Muestra: estimador de pi][hdinsight-sample-pi-estimator]
* [Muestra: streaming en C#][hdinsight-sample-cs-streaming]

**Requisitos previos**:

- **Un clúster de HDInsight**. Para obtener instrucciones sobre las diversas formas de creación de dichos clústeres, consulte [Introducción a Azure HDInsight][hdinsight-get-started] o [Aprovisionamiento de clústeres de HDInsight](hdinsight-provision-clusters.md).
- **Una estación de trabajo con Azure PowerShell**. Vea [Instalar y usar Azure PowerShell](http://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/).

## Ejecución del ejemplo con Azure PowerShell

**Para enviar el trabajo de MapReduce**

1. Abra **Windows PowerShell ISE**. Para obtener más información, consulte [Instalación y configuración de Azure PowerShell][powershell-install-configure].
2. Pegue el siguiente script de PowerShell:

		$subscriptionName = "<Azure Subscription Name>"
		$resourceGroupName = "<Resource Group Name>"
		$clusterName = "<HDInsight cluster name>"             # HDInsight cluster name
		
		Select-AzureRmSubscription $subscriptionName
		
		# Define the MapReduce job
		$wordCountJobDefinition = New-AzureRmHDInsightMapReduceJobDefinition `
									-JarFile "wasb:///example/jars/hadoop-mapreduce-examples.jar" `
									-ClassName "wordcount" `
									-Arguments "wasb:///example/data/gutenberg/davinci.txt", "wasb:///example/data/WordCountOutput1"
		
		# Submit the job and wait for job completion
		$cred = Get-Credential -Message "Enter the HDInsight cluster HTTP user credential:" 
		$wordCountJob = Start-AzureRmHDInsightJob `
							-ResourceGroupName $resourceGroupName `
							-ClusterName $clusterName `
							-HttpCredential $cred `
							-JobDefinition $wordCountJobDefinition 
		
		Wait-AzureRmHDInsightJob `
			-ResourceGroupName $resourceGroupName `
			-ClusterName $clusterName `
			-HttpCredential $cred `
			-JobId $wordCountJob.JobId 
		
		# Get the job output
		$cluster = Get-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $clusterName
		$defaultStorageAccount = $cluster.DefaultStorageAccount -replace '.blob.core.windows.net'
		$defaultStorageAccountKey = Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccount |  %{ $_.Key1 }
		$defaultStorageContainer = $cluster.DefaultStorageContainer
		
		Get-AzureRmHDInsightJobOutput `
			-ResourceGroupName $resourceGroupName `
			-ClusterName $clusterName `
			-HttpCredential $cred `
			-DefaultStorageAccountName $defaultStorageAccount `
			-DefaultStorageAccountKey $defaultStorageAccountKey `
			-DefaultContainer $defaultStorageContainer  `
			-JobId $wordCountJob.JobId `
			-DisplayOutputType StandardError

3. Establezca las 3 variables y ejecute el script.
		
**Para recuperar los resultados del trabajo de MapReduce**

1. Abra **Windows PowerShell ISE**. Para obtener más información, consulte [Instalación y configuración de Azure PowerShell][powershell-install-configure].
2. Pegue el siguiente script de PowerShell:

		$subscriptionName = "<Azure Subscription Name>"
		$resourceGroupName = "<Resource Group Name>"
		$clusterName = "<HDInsight cluster name>"             # HDInsight cluster name

		# Select the current subscription
		Select-AzureSubscription $subscriptionName
		
		# Get the cluster properties
		$cluster = Get-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $clusterName
		$defaultStorageAccount = $cluster.DefaultStorageAccount -replace '.blob.core.windows.net'
		$defaultStorageAccountKey = Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccount |  %{ $_.Key1 }
		$defaultStorageContainer = $cluster.DefaultStorageContainer
		
		# Download the job output to the workstation
		$storageContext = New-AzureStorageContext -StorageAccountName $defaultStorageAccount -StorageAccountKey $defaultStorageAccountKey 
		Get-AzureStorageBlobContent -Container $defaultStorageContainer -Blob example/data/WordCountOutput/part-r-00000 -Context $storageContext -Force
		
		# Display the output file
		cat ./example/data/WordCountOutput/part-r-00000 | findstr "there"

	El trabajo de MapReduce genera un archivo denominado *part-r-00000* con las palabras y los recuentos. El script usa el comando **findstr** para enumerar todas las palabras que contienen *"there*".

La salida del script de WordCount debe aparecer en la ventana Comandos:

![Resultados de frecuencia de palabras en PowerShell del ejemplo de recuento de palabras de MapReduce de Hadoop en HDInsight.][image-hdi-sample-wordcount-output]

Tenga en cuenta que los archivos de salida de un trabajo de MapReduce son inmutables. Por lo tanto, si vuelve a ejecutar esta muestra, debe cambiar el nombre del archivo de salida.

##Código fuente de Java

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

## Pasos siguientes

* [Introducción a HDInsight de Azure][hdinsight-get-started]
* [Desarrollo de programas MapReduce de Java para Hadoop en HDInsight](hdinsight-develop-deploy-java-mapreduce.md)
* [Envío de trabajos de Hadoop en HDInsight](hdinsight-submit-hadoop-jobs-programmatically.md)
* [Muestra: GraySort de 10 GB][hdinsight-sample-10gb-graysort]
* [Muestra: estimador de pi][hdinsight-sample-pi-estimator]
* [Muestra: streaming en C#][hdinsight-sample-cs-streaming]

[hdinsight-sample-10gb-graysort]: hdinsight-sample-10gb-graysort.md
[hdinsight-sample-pi-estimator]: hdinsight-sample-pi-estimator.md
[hdinsight-sample-cs-streaming]: hdinsight-sample-csharp-streaming.md


[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md

[hdinsight-get-started]: ../hdinsight-get-started.md

[powershell-install-configure]: ../install-configure-powershell.md

[image-hdi-sample-wordcount-output]: ./media/hdinsight-sample-wordcount/HDI.Sample.WordCount.Output.png

<!---HONumber=Oct15_HO4-->