<properties linkid="manage-services-hdinsight-howto-mapreduce" urlDisplayName="MapReduce with HDInsight " pageTitle="Use MapReduce with HDInsight | Azure" metaKeywords="" description="Learn how to use HDInsight to execute a simple Hadoop MapReduce job." metaCanonical="" services="hdinsight" documentationCenter="" title="Use MapReduce with HDInsight" authors="jgao" solutions="" manager="paulettm" editor="cgronlun" />

Uso de MapReduce con HDInsight
==============================

MapReduce de Hadoop es un marco de software para escribir aplicaciones que procesan enormes cantidades de datos. En este tutorial, usará Azure PowerShell desde la estación de trabajo para enviar un programa de MapReduce que cuenta las ocurrencias de palabras en un texto para un clúster de HDInsight. El programa de recuento de palabras está escrito en Java y el programa incluye el clúster de HDInsight.

**Requisitos previos:**

Antes de empezar este tutorial, debe tener lo siguiente:

-   Un clúster de HDInsight. Para obtener instrucciones acerca de varias formas de creación de dichos clústeres, consulte [Aprovisionamiento de clústeres de HDInsight](/en-us/manage/services/hdinsight/provision-hdinsight-clusters/).

-   Una estación de trabajo con Azure PowerShell instalado y configurado. Para obtener más información, consulte [Instalación y configuración de Azure PowerShell](/en-us/manage/install-and-configure-windows-powershell/).

**Duración aproximada:** 30 minutos

Apartados de este tutorial
--------------------------

1.  [Comprensión del escenario](#scenario)
2.  [Ejecución de la muestra con Azure PowerShell](#run-sample)
3.  [El código Java para el programa de recuento de palabras de MapReduce](#java-code)
4.  [Pasos siguientes](#next-steps)

Comprensión del escenario
-------------------------

En el siguiente diagrama se ilustra cómo funciona MapReduce para el escenario de recuento de palabras:

![HDI.ProgramaRecuentoPalabras](./media/hdinsight-get-started/HDI.WordCountDiagram.gif)

La salida del trabajo de MapReduce es un conjunto de pares clave-valor. La clave es una cadena que especifica una palabra y el valor es un entero que especifica el número total de ocurrencias de esa palabra en el texto. Esto se hace en dos etapas:

-   El asignador utiliza cada línea del texto de entrada como una entrada y la desglosa en palabras. Emite un par clave-valor cada vez que se detecta una palabra, seguido por un 1. La salida se ordenará antes de enviarla al reductor.

-   Posteriormente, el reductor suma estos recuentos individuales de cada palabra y emite un solo par clave-valor que contiene la palabra seguido de la suma de sus ocurrencias.

Para ejecutar un trabajo de MapReduce se requieren los siguientes elementos:

-   Un programa de MapReduce. En este tutorial, usará la muestra de recuento de palabras incluida con los clústeres de HDInsight, por lo que no tendrá que escribir una propia. Se encuentra en */example/jars/hadoop-examples.jar*. El nombre del archivo es *hadoop-mapreduce-examples.jar* en la versión 3.0 de clústeres de HDInsight. Para obtener instrucciones acerca de cómo escribir su propio trabajo de MapReduce, consulte [Desarrollo de programas MapReduce de Java para HDInsight](/en-us/documentation/articles/hdinsight-develop-deploy-java-mapreduce/).
-   Un archivo de entrada. Se usará */example/data/gutenberg/davinci.txt* como este tipo de archivo. Para obtener información acerca de cómo cargar archivos, consulte [Carga de datos en HDInsight](/en-us/documentation/articles/hdinsight-upload-data/).
-   Una carpeta de archivo de salida. Se usará */example/data/WordCountOutput* como la carpeta mencionada. El sistema creará la carpeta en caso de que esta no exista. El trabajo de MapReduce producirá un error si la carpeta existe. Si desea ejecutar el trabajo de MapReduce por segunda vez, asegúrese de eliminar la carpeta de salida o especifique otra carpeta de salida.

Ejecución de la muestra con Azure PowerShell
--------------------------------------------

1.  Abra **Azure PowerShell**. Para obtener instrucciones acerca de cómo abrir la ventana de la consola de Azure PowerShell, consulte [Instalación y configuración de Azure PowerShell](/en-us/manage/install-and-configure-windows-powershell/).

2.  Ajuste las dos variables en los comandos siguientes y, a continuación, ejecútelos:

         $subscriptionName = "<SubscriptionName>"   # Nombre de la suscripción a Azure
         $clusterName = "<ClusterName>"             # Nombre del clúster de HDInsight

3.  Ejecute el siguiente comando y proporcione información de la cuenta de Azure:

         Add-AzureAccount

4.  Ejecute los siguientes comandos para crear una definición del trabajo de MapReduce:

         # Defina el trabajo de MapReduce
         $wordCountJobDefinition = New-AzureHDInsightMapReduceJobDefinition -JarFile "wasb:///example/jars/hadoop-examples.jar" -ClassName "wordcount" -Arguments "wasb:///example/data/gutenberg/davinci.txt", "wasb:///example/data/WordCountOutput" 

    > [WACOM.NOTE] *hadoop-examples.jar* viene con la versión 2.1 de clústeres de HDInsight. Se cambió el nombre del archivo a *hadoop-mapreduce.jar* en la versión 3.0 de clústeres de HDInsight.

    El archivo hadoop-examples.jar se incluye con la distribución del clúster de HDInsight. Existen dos argumentos para el trabajo de MapReduce. El primero es el nombre del archivo de origen y, el segundo, la ruta de acceso del archivo de salida. El archivo de origen se incluye con la distribución del clúster de HDInsight y la ruta de acceso del archivo de salida se creará en tiempo de ejecución.

5.  Ejecute el siguiente comando para enviar el trabajo de MapReduce:

         # Envíe el trabajo
         Select-AzureSubscription $subscriptionName
         $wordCountJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $wordCountJobDefinition | Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600  

    Además de la definición de trabajo de MapReduce, también debe proporcionar el nombre del clúster de HDInsight en el que desea ejecutar el trabajo de MapReduce y las credenciales. Start-AzureHDInsightJob es una llamada no sincronizada. Para comprobar la finalización del trabajo, use el cmdlet *Wait-AzureHDInsightJob*.

6.  Ejecute el siguiente comando para comprobar la finalización del trabajo de MapReduce:

         Wait-AzureHDInsightJob -Job $wordCountJob -WaitTimeoutInSeconds 3600 

7.  Ejecute el siguiente comando para comprobar posibles errores al ejecutar el trabajo de MapReduce:

         # Obtenga la salida del trabajo
         Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $wordCountJob.JobId -StandardError 

**Para recuperar los resultados del trabajo de MapReduce**

1.  Abra **Azure PowerShell**.
2.  Ejecute el siguiente comando para cambiar al directorio a c:\\root:

         cd \

    El directorio predeterminado de Azure Powershell es *C:\\Windows\\System32\\WindowsPowerShell\\v1.0*. De forma predeterminada, no tiene permiso de escritura en esta carpeta. Debe cambiar el directorio al directorio raíz C:\\ o a una carpeta en la que tiene permiso de escritura.

3.  Establezca tres variables en los comandos siguientes y, a continuación, ejecútelos:

         $subscriptionName = "<SubscriptionName>"       # Nombre de la suscripción a Azure
            
         $storageAccountName = "<StorageAccountName>"   # Nombre de la cuenta de almacenamiento de Azure
         $containerName = "<ContainerName>"              # Nombre del contenedor de almacenamiento de blobs

         La cuenta de almacenamiento de Azure es la misma que se creó anteriormente en el tutorial. Esta se usa para hospedar el contenedor de blobs utilizado como sistema de archivos predeterminado del clúster de HDInsight.  El nombre del contenedor de almacenamiento de blobs suele coincidir con el del clúster de HDInsight, a menos que se especifique un nombre distinto durante el aprovisionamiento del clúster.

4.  Ejecute los siguientes comandos para crear un objeto de contexto de almacenamiento de Azure:

         # Seleccione la suscripción actual
         Select-AzureSubscription $subscriptionName

         # Crear el objeto de contexto de la cuenta de almacenamiento
         $storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
         $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  

    *Select-AzureSubscription* se usa para establecer la suscripción actual en caso de tener varias y no usar la suscripción predeterminada.

5.  Ejecute el siguiente comando para descargar el resultado del trabajo de MapReduce del contenedor de blobs a la estación de trabajo:

         # Download the job output to the workstation
         Get-AzureStorageBlobContent -Container $ContainerName -Blob example/data/WordCountOutput/part-r-00000 -Context $storageContext -Force

    La carpeta */example/data/WordCountOutput* es la carpeta de salida especificada cuando se ejecuta el trabajo de MapReduce. *part-r-00000* es el nombre de archivo predeterminado para la salida de trabajos de MapReduce. El archivo se descargará en la misma estructura de carpetas de la carpeta local. Por ejemplo, en la captura de pantalla siguiente, la carpeta actual es la carpeta raíz C. El archivo se descargará en la carpeta *C:\\example\\data\\WordCountOutput*.

6.  Ejecute el siguiente comando para imprimir el archivo de salida del trabajo de MapReduce:

         cat ./example/data/WordCountOutput/part-r-00000 | findstr "there"

    El trabajo de MapReduce genera un archivo denominado *part-r-00000* con las palabras y los recuentos. El script usa el comando findstr para enumerar todas las palabras que contienen *"there"*.

Tenga en cuenta que los archivos de salida de un trabajo de MapReduce son inmutables. Por lo tanto, si vuelve a ejecutar esta muestra tendrá que cambiar el nombre del archivo de salida.

El código Java para el programa de recuento de palabras de MapReduce
--------------------------------------------------------------------

El siguiente es el código fuente del programa MapReduce de Java de recuento de palabras:

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

Pasos siguientes
----------------

Mientras MapReduce ofrece potentes capacidades de diagnóstico, puede ser un poco difícil de dominar. Otros lenguajes como Pig y Hive proporcionan una manera más fácil de trabajar con datos almacenados en HDInsight. Para obtener más información, consulte los artículos siguientes:

-   [Introducción a HDInsight de Azure](/en-us/manage/services/hdinsight/get-started-hdinsight/)
-   [Desarrollo de programas MapReduce de Java para HDInsight](/en-us/documentation/articles/hdinsight-develop-deploy-java-mapreduce/)
-   [Desarrollo de programas de MapReduce de streaming de Hadoop C\# para HDInsight](/en-us/documentation/articles/hdinsight-hadoop-develop-deploy-streaming-jobs/)
-   [Uso de Hive con HDInsight](/en-us/documentation/articles/hdinsight-use-hive/)
-   [Uso de Pig con HDInsight](/en-us/documentation/articles/hdinsight-use-pig/)
-   [Ejecución de muestras de HDInsight](/en-us/documentation/articles/hdinsight-run-samples/)

