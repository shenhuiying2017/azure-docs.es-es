<properties urlDisplayName="Hadoop Samples in HDInsight" pageTitle="Muestra WordCount de Hadoop en HDInsight | Azure" metaKeywords="hdinsight, hdinsight sample, hadoop, mapreduce" description="Aprenda a ejecutar una muestra sencilla de MapReduce en HDInsight." umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" services="hdinsight" documentationCenter="" title="Ejemplo WordCount de Hadoop en HDInsight" authors="bradsev" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="bradsev" />

# Ejemplo WordCount de Hadoop en HDInsight

Este tema de ejemplo muestra cómo ejecutar un programa de MapReduce de Hadoop que cuenta el número de palabras en un archivo de texto usando Azure PowerShell para ejecutar el programa en HDInsight. El programa de MapReduce para WordCount está escrito en Java y se ejecuta en un clúster de Hadoop administrado por HDInsight. El archivo de texto analizado en esta muestra es la edición de Los cuadernos de Leonardo Da Vinci para el libro electrónico del proyecto Gutenberg.

El programa de MapReduce de Hadoop lee el archivo de texto y cuenta la frecuencia con que aparece cada palabra. El resultado es un archivo de texto nuevo que consta de líneas, cada una de las cuales contiene una palabra y el recuento (un par clave-valor separado por tabulaciones) de la frecuencia con que esa palabra aparece en el documento. Este proceso se hace en dos fases. El asignador utiliza cada línea del texto de entrada como una entrada y la desglosa en palabras. Emite un par clave-valor cada vez que se detecta una palabra, seguido por un 1. Posteriormente, el reductor suma estos recuentos individuales de cada palabra y emite un solo par clave-valor que contiene la palabra seguida de la suma de sus ocurrencias.

**Aprenderá a:**

-   Utilizar Azure PowerShell para ejecutar un programa de MapReduce en un clúster de HDInsight.
-   Crear programas de MapReduce en Java.

**Requisitos previos**:

-   Debe tener una cuenta de Azure. Para conocer las opciones para obtener una cuenta, consulte la página de la [prueba gratuita de Azure][prueba gratuita de Azure].

-   Debe aprovisionar un clúster de HDInsight. Para obtener instrucciones acerca de las diversas formas de creación de dichos clústeres, consulte [Introducción a HDInsight de Azure][Introducción a HDInsight de Azure] o [Aprovisionamiento de clústeres de HDInsight][Aprovisionamiento de clústeres de HDInsight].

-   Debe tener instalado Azure PowerShell y haber configurado los clústeres para utilizarlos con su cuenta. Para obtener instrucciones acerca de cómo hacerlo, consulte [Instalación y configuración de Azure PowerShell][Instalación y configuración de Azure PowerShell].

## En este artículo

En este tema se muestra cómo ejecutar la muestra, presenta el código de Java para el programa de MapReduce, resume lo que ha aprendido y se describen algunos pasos a seguir. Tiene las siguientes secciones.

1.  [Ejecución del ejemplo con Azure PowerShell][Ejecución del ejemplo con Azure PowerShell]
2.  [El código Java para el programa de MapReduce de WordCount][El código Java para el programa de MapReduce de WordCount]
3.  [Resumen][Resumen]
4.  [Pasos siguientes][Pasos siguientes]

## <span id="run-sample"></span></a>Ejecución del ejemplo con Azure PowerShell

**Para enviar el trabajo de MapReduce**

1.  Abra **Azure PowerShell**. Para obtener instrucciones acerca de cómo abrir la ventana de la consola de Azure PowerShell, consulte [Instalación y configuración de Azure PowerShell][Instalación y configuración de Azure PowerShell].

2.  Ajuste las dos variables en los comandos siguientes y, a continuación, ejecútelos:

        $subscriptionName = "<SubscriptionName>"   # Azure subscription name
        $clusterName = "<ClusterName>"             # HDInsight cluster name

3.  Ejecute el siguiente comando para crear una definición del trabajo de MapReduce:

        # Define the MapReduce job
        $wordCountJobDefinition = New-AzureHDInsightMapReduceJobDefinition -JarFile "wasb:///example/jars/hadoop-examples.jar" -ClassName "wordcount" -Arguments "wasb:///example/data/gutenberg/davinci.txt", "wasb:///example/data/WordCountOutput" 

    > [WACOM.NOTE] *hadoop-examples.jar* viene con la versión 2.1 de clústeres de HDInsight. Se cambió el nombre del archivo a *hadoop-mapreduce.jar* en la versión 3.0 de clústeres de HDInsight.

    El archivo hadoop-examples.jar se incluye con el clúster de HDInsight. Existen dos argumentos para el trabajo de MapReduce. El primero es el nombre del archivo de origen y, el segundo, la ruta de acceso del archivo de salida. El archivo de origen se incluye con el clúster de HDInsight y la ruta de acceso del archivo de salida se creará en tiempo de ejecución.

4.  Ejecute el siguiente comando para enviar el trabajo de MapReduce:

        # Submit the job
        Select-AzureSubscription $subscriptionName
        $wordCountJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $wordCountJobDefinition | Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600  

    Además de la definición de trabajo de MapReduce, también debe proporcionar el nombre del clúster de HDInsight en el que desea ejecutar el trabajo de MapReduce.

5.  Ejecute el siguiente comando para comprobar posibles errores al ejecutar el trabajo de MapReduce:

        # Get the job output
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $wordCountJob.JobId -StandardError 

**Para recuperar los resultados del trabajo de MapReduce**

1.  Abra **Azure PowerShell**.
2.  Establezca tres variables en los comandos siguientes y, a continuación, ejecútelos:

        $subscriptionName = "<SubscriptionName>"       # Azure subscription name

        $storageAccountName = "<StorageAccountName>"   # Azure storage account name
        $containerName = "<ContainerName>"             # Blob storage container name

    La cuenta de almacenamiento de Azure es la misma que se creó anteriormente en el tutorial. Esta se usa para hospedar el contenedor de blobs utilizado como sistema de archivos predeterminado del clúster de HDInsight. El nombre del contenedor de almacenamiento de blobs suele coincidir con el del clúster de HDInsight, a menos que se especifique un nombre distinto durante el aprovisionamiento del clúster.

3.  Ejecute los siguientes comandos para crear un objeto de contexto de almacenamiento de Azure:

        # Select the current subscription
        Select-AzureSubscription $subscriptionName

        # Create the storage account context object
        $storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
        $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  

    *Select-AzureSubscription* se usa para establecer la suscripción actual en caso de tener varias y no usar la suscripción predeterminada.

4.  Ejecute el siguiente comando para descargar el resultado del trabajo de MapReduce del contenedor de blobs a la estación de trabajo:

        # Download the job output to the workstation
        Get-AzureStorageBlobContent -Container $ContainerName -Blob example/data/WordCountOutput/part-r-00000 -Context $storageContext -Force

    *example/data/WordCountOutput* es la carpeta de salida especificada al ejecutar el trabajo de MapReduce. *part-r-00000* es el nombre de archivo predeterminado para el resultado del trabajo de MapReduce. El archivo se descargará en la misma estructura de carpetas de la carpeta local. Por ejemplo, en la captura de pantalla siguiente, la carpeta actual es la carpeta raíz C. El archivo se descargará en la carpeta *C:\\example\\data\\WordCountOutput*.

5.  Ejecute el siguiente comando para imprimir el archivo de salida del trabajo de MapReduce:

        cat ./example/data/WordCountOutput/part-r-00000 | findstr "there"

    El trabajo de MapReduce genera un archivo denominado *part-r-00000* con las palabras y los recuentos. El script usa el comando findstr para enumerar todas las palabras que contienen *"there"*.

La salida del script de WordCount debe aparecer en la ventana cmd:

![HDI.Sample.WordCount.Output][HDI.Sample.WordCount.Output]

Tenga en cuenta que los archivos de salida de un trabajo de MapReduce son inmutables. Por lo tanto, si vuelve a ejecutar esta muestra tendrá que cambiar el nombre del archivo de salida.

## <span id="java-code"></span></a>El código Java para el programa de MapReduce de WordCount

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

## <span id="summary"></span></a>Resumen

En este tutorial, ha visto cómo ejecutar un programa de MapReduce que cuenta las ocurrencias de palabras en un archivo de texto con HDInsight mediante el uso de Azure PowerShell.

## <span id="next-steps"></span></a>Pasos siguientes

Para ver tutoriales sobre la ejecución de otros ejemplos y que proporcionan instrucciones sobre el uso de Pig, Hive y los trabajos de MapReduce en HDInsight de Azure con Azure PowerShell, consulte los siguientes temas:

-   [Introducción a HDInsight de Azure][Introducción a HDInsight de Azure]
-   [Muestra: GraySort de 10 GB][Muestra: GraySort de 10 GB]
-   [Muestra: Estimador de Pi][Muestra: Estimador de Pi]
-   [Muestra: C# Steaming][Muestra: C# Steaming]
-   [Uso de Pig con HDInsight][Uso de Pig con HDInsight]
-   [Uso de Hive con HDInsight][Uso de Hive con HDInsight]
-   [Azure HDInsight SDK documentation][Azure HDInsight SDK documentation]

  [prueba gratuita de Azure]: http://azure.microsoft.com/es-es/pricing/free-trial/
  [Introducción a HDInsight de Azure]: ../hdinsight-get-started/
  [Aprovisionamiento de clústeres de HDInsight]: ../hdinsight-provision-clusters/
  [Instalación y configuración de Azure PowerShell]: ../install-configure-powershell/
  [Ejecución del ejemplo con Azure PowerShell]: #run-sample
  [El código Java para el programa de MapReduce de WordCount]: #java-code
  [Resumen]: #summary
  [Pasos siguientes]: #next-steps
  [HDI.Sample.WordCount.Output]: ./media/hdinsight-sample-wordcount/HDI.Sample.WordCount.Output.png
  [Muestra: GraySort de 10 GB]: ../hdinsight-sample-10gb-graysort/
  [Muestra: Estimador de Pi]: ../hdinsight-sample-pi-estimator/
  [Muestra: C# Steaming]: ../hdinsight-sample-csharp-streaming/
  [Uso de Pig con HDInsight]: ../hdinsight-use-pig/
  [Uso de Hive con HDInsight]: ../hdinsight-use-hive/
  [Azure HDInsight SDK documentation]: http://msdnstage.redmond.corp.microsoft.com/es-es/library/dn479185.aspx
