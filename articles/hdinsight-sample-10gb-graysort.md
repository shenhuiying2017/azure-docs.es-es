<properties linkid="manage-services-hdinsight-sample-10gb-graysort" urlDisplayName="HDInsight Samples" pageTitle="The 10GB GraySort sample | Azure" metaKeywords="hdinsight, hdinsight administration, hdinsight administration azure" description="Learn how to run a general purpose GraySort with HDInsight using Azure PowerShell." umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" services="hdinsight" documentationCenter="" title="The 10GB GraySort sample" authors="bradsev" />

El ejemplo de GraySort de 10 GB
===============================

Este tema de ejemplo muestra cómo ejecutar GraySort de uso general con HDInsight de Azure con Azure PowerShell. GraySort es un tipo de banco de pruebas cuya métrica es la velocidad de ordenación (TB/minuto) que se logra después de ordenar enormes volúmenes de datos, normalmente 100 TB como mínimo.

Este ejemplo utiliza solo 10 GB de datos, para así poder ejecutarlo relativamente rápido. En él se emplean las aplicaciones de MapReduce, desarrolladas por Owen O'Malley y Arun Murthy, que ganaron el estándar de comparación anual de ordenación de terabyte de fin general ("daytona") en 2009 con una velocidad de 0,578 TB/min (100 TB en 173 minutos). Para obtener más información sobre este y otros estándares de comparación de ordenación, consulte el sitio [Sortbenchmark](http://sortbenchmark.org/).

Este ejemplo utiliza tres conjuntos de programas de MapReduce:

1.  **TeraGen** es un programa de MapReduce que puede utilizar para generar las filas de datos que se van a ordenar.
2.  **TeraSort** toma una muestra de los datos de entrada y utiliza MapReduce para ordenar los datos de manera absoluta. TeraSort es un ordenamiento estándar de funciones de MapReduce, con la excepción de un particionador personalizado que utiliza una lista ordenada de N-1 claves de muestra que definen el rango de claves para cada reducción. En particular, todas las claves, como sample[i-1] &lt;= key &lt; sample[i], se envían a reducción de i. Con esto se garantiza que la salida de reducción de i es menor que la salida de reducción i+1.
3.  **TeraValidate** es un programa de MapReduce que valida que la salida se ordene de manera global. Crea una asignación por archivo en el directorio de salida y cada asignación asegura que cada clave es menor o igual que la anterior. La función de asignación también genera registros de la primera y la última clave de cada archivo y la función de reducción garantiza que la primera clave del archivo i es mayor que la última clave de archivo i-1. Los problemas se notifican como salida de la reducción con las claves que no están en orden.

El formato de entrada y salida, que utilizan las tres aplicaciones, lee y escribe los archivos de texto en el formato correcto. La salida de la reducción tiene la replicación definida en 1, en lugar del valor predeterminado 3, porque el concurso de estándar de comparación no requiere que los datos de salida se repliquen en varios nodos.

**Aprenderá a:** 
* Utilizar Azure PowerShell para ejecutar una serie de programas de MapReduce en HDInsight de Azure. 
* El aspecto de un programa de MapReduce escrito en Java.

**Requisitos previos**:

-   Debe tener una cuenta de Azure. Para conocer las opciones para obtener una cuenta, consulte la página de la [prueba gratuita de Azure](http://www.windowsazure.com/en-us/pricing/free-trial/).

-   Debe aprovisionar un clúster de HDInsight. Para obtener instrucciones acerca de varias formas de creación de dichos clústeres, consulte [Aprovisionamiento de clústeres de HDInsight](/en-us/manage/services/hdinsight/provision-hdinsight-clusters/).

-   Debe tener instalado Azure PowerShell y haber configurado los clústeres para utilizarlos con su cuenta. Para obtener instrucciones acerca de cómo hacerlo, consulte [Instalación y configuración de Azure PowerShell](/en-us/documentation/articles/install-configure-powershell/).

En este artículo
----------------

En este tema se muestra cómo ejecutar la serie de programas de MapReduce que conforman el ejemplo, se presenta el código Java del programa de MapReduce, se resume todo lo que ha aprendido y se esbozan los pasos siguientes. Tiene las siguientes secciones.

1.  [Ejecución de la muestra con Azure PowerShell](#run-sample)
2.  [El código Java para el programa de MapReduce de TeraSort](#java-code)
3.  [Resumen](#summary)
4.  [Pasos siguientes](#next-steps)

Ejecución de la muestra con Azure PowerShell
--------------------------------------------

El ejemplo requiere tres tareas, cada una de las cuales corresponde a uno de los programas de MapReduce que se describen en la introducción:

1.  Ejecute el trabajo de MapReduce **TeraGen** para generar los datos que se van a ordenar.
2.  Ejecute el trabajo de MapReduce **TeraSort** para ordenar los datos.
3.  Ejecute el trabajo de MapReduce **TeraValidate** para confirmar que los datos se ordenen correctamente.

**Ejecución del programa TeraGen**

1.  Abra Azure PowerShell. Para obtener instrucciones acerca de cómo abrir la ventana de la consola de Azure PowerShell, consulte [Instalación y configuración de Azure PowerShell](/en-us/documentation/articles/install-configure-powershell/).
2.  Ajuste las dos variables en los comandos siguientes y, a continuación, ejecútelos:

         # Proporcionar el nombre de suscripción de Azure y el nombre de clúster de HDInsight.
         $subscriptionName = "myAzureSubscriptionName"   
         $clusterName = "myClusterName"

3.  Ejecute el siguiente comando para crear una definición del trabajo de MapReduce":

         # Crear una definición de trabajo de MapReduce para el programa de MapReduce de TeraGen
         $teragen = New-AzureHDInsightMapReduceJobDefinition -JarFile "/example/jars/hadoop-examples.jar" -ClassName "teragen" -Arguments "-Dmapred.map.tasks=50", "100000000", "/example/data/10GB-sort-input" 

    > [WACOM.NOTE] *hadoop-examples.jar* viene con la versión 2.1 de clústeres de HDInsight. Se cambió el nombre del archivo a *hadoop-mapreduce.jar* en la versión 3.0 de clústeres de HDInsight.

    El argumento *"-Dmapred.map.tasks=50"* especifica que se crearán 50 asignaciones para ejecutar el trabajo. El argumento *100000000* especifica la cantidad de datos que se van a generar. El argumento final, */example/data/10GB-sort-input*, especifica el directorio de salida en el que se guardarán los resultados (que contiene la entrada para la siguiente fase de ordenación).

4.  Ejecute los siguientes comandos para iniciar el trabajo, espere a que se complete e imprima el error estándar:

         # Ejecutar el trabajo de MapReduce de TeraGen.
         # Esperar a que el trabajo finalice.
         # Imprimir la salida y el archivo de error estándar del trabajo de MapReduce.
         Select-AzureSubscription $subscriptionName         
         $teragen | Start-AzureHDInsightJob -Cluster $clustername | Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 | Get-AzureHDInsightJobOutput -Cluster $clustername -StandardError 

**Ejecución del programa TeraSort**

1.  Abra Azure PowerShell.
2.  Ajuste las dos variables en los comandos siguientes y, a continuación, ejecútelos:

         # Proporcionar el nombre de suscripción de Azure y el nombre de clúster de HDInsight.
         $subscriptionName = "myAzureSubscriptionName"   
         $clusterName = "myClusterName"

3.  Ejecute el siguiente comando para definir el trabajo de MapReduce:

         # Crear una definición de trabajo de MapReduce para el programa de MapReduce de TeraSort.
         $terasort = New-AzureHDInsightMapReduceJobDefinition -JarFile "/example/jars/hadoop-examples.jar" -ClassName "terasort" -Arguments "-Dmapred.map.tasks=50", "-Dmapred.reduce.tasks=25", "/example/data/10GB-sort-input", "/example/data/10GB-sort-output" 

    El argumento *"-Dmapred.map.tasks=50"* especifica que se crearán 50 asignaciones para ejecutar el trabajo. El argumento *100000000* especifica la cantidad de datos que se van a generar. Los últimos dos especifican los directorios de entrada y salida.

4.  Ejecute el siguiente comando para iniciar el trabajo, espere a que se complete e imprima el error estándar:

         # Ejecutar el trabajo de MapReduce de TeraSort.
         # Esperar a que el trabajo finalice.
         # Imprimir la salida y el archivo de error estándar del trabajo de MapReduce. 
         Select-AzureSubscription $subscriptionName        
         $terasort | Start-AzureHDInsightJob -Cluster $clustername | Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 | Get-AzureHDInsightJobOutput -Cluster $clustername -StandardError 

**Ejecución del programa TeraValidate**

1.  Abra Azure PowerShell.
2.  Ajuste las dos variables en los comandos siguientes y, a continuación, ejecútelos:

         # Proporcionar el nombre de suscripción de Azure y el nombre de clúster de HDInsight.
         $subscriptionName = "myAzureSubscriptionName"   
         $clusterName = "myClusterName"

3.  Ejecute el siguiente comando para definir el trabajo de MapReduce:

         #   Crear una definición de trabajo de MapReduce para el programa de MapReduce de TeraValidate.
         $teravalidate = New-AzureHDInsightMapReduceJobDefinition -JarFile "/example/jars/hadoop-examples.jar" -ClassName "teravalidate" -Arguments "-Dmapred.map.tasks=50", "-Dmapred.reduce.tasks=25", "/example/data/10GB-sort-output", "/example/data/10GB-sort-validate" 

    El argumento *"-Dmapred.map.tasks=50"* especifica que se crearán 50 asignaciones para ejecutar el trabajo. El argumento *"-Dmapred.reduce.tasks=25"* especifica también que se crearán 25 tareas de reducción para ejecutar el trabajo. Los últimos dos especifican los directorios de entrada y salida.

4.  Ejecute los siguientes comandos para iniciar el trabajo de MapReduce, espere a que se complete e imprima el error estándar:

         # Ejecutar el trabajo de MapReduce de TeraSort.
         # Esperar a que el trabajo finalice.
         # Imprimir la salida y el archivo de error estándar del trabajo de MapReduce. 
         Select-AzureSubscription $subscriptionName 
         $teravalidate | Start-AzureHDInsightJob -Cluster $clustername | Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 | Get-AzureHDInsightJobOutput -Cluster $clustername -StandardError 

El código Java para el programa de MapReduce de TeraSort
--------------------------------------------------------

En esta sección se analiza el código del programa de MapReduce de TerraSort.

    /**
     * Con licencia de Apache Software Foundation (ASF) en virtud de uno    
     * o más contratos de licencia de contribuidor.  Consulte el archivo AVISO   
     * distribuido con este trabajo para obtener información adicional   
     * respecto a la propiedad de derecho de autor.  ASF otorga licencia de este archivo    
     * a usted en virtud de la licencia de Apache, versión 2.0 (la   
     * "Licencia"); no puede utilizar este archivo, salvo que sea para el cumplimiento de normas  
     * con la Licencia.  Puede obtener una copia de la licencia en  
     *  
     *     http://www.apache.org/licenses/LICENSE-2.0   
     *  
     * A menos que así lo requiera la ley aplicable o que se haya acordado por escrito, el software  
     * distribuido en virtud de la Licencia se distribuye "TAL CUAL", 
     * SIN GARANTÍAS o CONDICIONES DE NINGÚN TIPO, ni expresas ni implícitas.  
     * Consulte la licencia para obtener información sobre el lenguaje específico que rige los permisos y 
     * limitaciones de la licencia. 
     */ 

    package org.apache.hadoop.examples.terasort;

    import java.io.IOException;
    import java.io.PrintStream;
    import java.net.URI;
    import java.util.ArrayList;
    import java.util.List;

    import org.apache.commons.logging.Log;
    import org.apache.commons.logging.LogFactory;
    import org.apache.hadoop.conf.Configured;
    import org.apache.hadoop.filecache.DistributedCache;
    import org.apache.hadoop.fs.FileSystem;
    import org.apache.hadoop.fs.Path;
    import org.apache.hadoop.io.NullWritable;
    import org.apache.hadoop.io.SequenceFile;
    import org.apache.hadoop.io.Text;
    import org.apache.hadoop.mapred.FileOutputFormat;
    import org.apache.hadoop.mapred.JobClient;
    import org.apache.hadoop.mapred.JobConf;
    import org.apache.hadoop.mapred.Partitioner;
    import org.apache.hadoop.util.Tool;
    import org.apache.hadoop.util.ToolRunner;

    /** 
     * Genera los puntos de división muestreados, inicia el trabajo  
     * y espera a que finalice.     
     * <p>
     * Para ejecutar el programa:   
     * <b>bin/hadoop jar hadoop-examples-*.jar terasort in-dir out-dir</b>    
     */ 

    public class TeraSort extends Configured implements Tool {
      private static final Log LOG = LogFactory.getLog(TeraSort.class);

      /**   
       * Un particionador que divide claves de texto en particiones     
       * prácticamente iguales en un orden clasificado global.   
       */   

      static class TotalOrderPartitioner implements Partitioner<Text,Text>{
        private TrieNode trie;
        private Text[] splitPoints;

        /**
         * Un nodo trie genérico
         */
        static abstract class TrieNode {
          private int level;
          TrieNode(int level) {
            this.level = level;
          }
          abstract int findPartition(Text key);
          abstract void print(PrintStream strm) throws IOException;
          int getLevel() {
            return level;
          }
        }

        /**
         * Un nodo trie interno que contiene 256 elementos secundarios basados en el siguiente
         * carácter.
         */
        static class InnerTrieNode extends TrieNode {
          private TrieNode[] child = new TrieNode[256];
          
          InnerTrieNode(int level) {
            super(level);
          }
          int findPartition(Text key) {
            int level = getLevel();
            if (key.getLength() <= level) {
              return child[0].findPartition(key);
            }
            return child[key.getBytes()[level]].findPartition(key);
          }
          void setChild(int idx, TrieNode child) {
            this.child[idx] = child;
          }
          void print(PrintStream strm) throws IOException {
            for(int ch=0; ch < 255; ++ch) {
              for(int i = 0; i < 2*getLevel(); ++i) {
                strm.print(' ');
              }
              strm.print(ch);
              strm.println(" ->");
              if (child[ch] != null) {
                child[ch].print(strm);
              }
            }
          }
        }

        /**
         * Un nodo trie hoja que realiza comparaciones de cadena para averiguar el lugar
         * en que corresponde la clave entre inferior..superior.
         */
        static class LeafTrieNode extends TrieNode {
          int lower;
          int upper;
          Text[] splitPoints;
          LeafTrieNode(int level, Text[] splitPoints, int lower, int upper) {
            super(level);
            this.splitPoints = splitPoints;
            this.lower = lower;
            this.upper = upper;
          }
          int findPartition(Text key) {
            for(int i=lower; i<upper; ++i) {
              if (splitPoints[i].compareTo(key) >= 0) {
                return i;
              }
            }
            return upper;
          }
          void print(PrintStream strm) throws IOException {
            for(int i = 0; i < 2*getLevel(); ++i) {
              strm.print(' ');
            }
            strm.print(lower);
            strm.print(", ");
            strm.println(upper);
          }
        }


        /**
         * Leer los puntos de corte del archivo de secuencia dado.
         * @param fs the file system
         * @param p the path to read
         * @param job the job config
         * @return the strings to split the partitions on
         * @throws IOException
         */
        private static Text[] readPartitions(FileSystem fs, Path p, 
                                             JobConf job) throws IOException {
          SequenceFile.Reader reader = new SequenceFile.Reader(fs, p, job);
          List<Text> parts = new ArrayList<Text>();
          Text key = new Text();
          NullWritable value = NullWritable.get();
          while (reader.next(key, value)) {
            parts.add(key);
            key = new Text();
          }
          reader.close();
          return parts.toArray(new Text[parts.size()]);  
        }

        /**
         * Dado un conjunto ordenado de puntos de corte, construir un trie que encontrará rápidamente
         * la partición correcta.
         * @param splits the list of cut points
         * @param lower the lower bound of partitions 0..numPartitions-1
         * @param upper the upper bound of partitions 0..numPartitions-1
         * @param prefix the prefix that we have already checked against
         * @param maxDepth the maximum depth we will build a trie for
         * @return the trie node that will divide the splits correctly
         */
        private static TrieNode buildTrie(Text[] splits, int lower, int upper, 
                                          Text prefix, int maxDepth) {
          int depth = prefix.getLength();
          if (depth >= maxDepth || lower == upper) {
            return new LeafTrieNode(depth, splits, lower, upper);
          }
          InnerTrieNode result = new InnerTrieNode(depth);
          Text trial = new Text(prefix);
          // adjuntar un byte adicional en el prefijo
          trial.append(new byte[1], 0, 1);
          int currentBound = lower;
          for(int ch = 0; ch < 255; ++ch) {
            trial.getBytes()[depth] = (byte) (ch + 1);
            lower = currentBound;
            while (currentBound < upper) {
              if (splits[currentBound].compareTo(trial) >= 0) {
                break;
              }
              currentBound += 1;
            }
            trial.getBytes()[depth] = (byte) ch;
            result.child[ch] = buildTrie(splits, lower, currentBound, trial, 
                                         maxDepth);
          }
          // seleccionar el resto
          trial.getBytes()[depth] = 127;
          result.child[255] = buildTrie(splits, currentBound, upper, trial,
                                        maxDepth);
          return result;
        }

        public void configure(JobConf job) {
          try {
            FileSystem fs = FileSystem.getLocal(job);
            Path partFile = new Path(TeraInputFormat.PARTITION_FILENAME);
            splitPoints = readPartitions(fs, partFile, job);
            trie = buildTrie(splitPoints, 0, splitPoints.length, new Text(), 2);
          } catch (IOException ie) {
            throw new IllegalArgumentException("can't read paritions file", ie);
          }
        }

        public TotalOrderPartitioner() {
        }

        public int getPartition(Text key, Text value, int numPartitions) {
          return trie.findPartition(key);
        }
        
      }
      
      public int run(String[] args) throws Exception {
        LOG.info("starting");
        JobConf job = (JobConf) getConf();
        Path inputDir = new Path(args[0]);
        inputDir = inputDir.makeQualified(inputDir.getFileSystem(job));
        Path partitionFile = new Path(inputDir, TeraInputFormat.PARTITION_FILENAME);
        URI partitionUri = new URI(partitionFile.toString() +
                                   "#" + TeraInputFormat.PARTITION_FILENAME);
        TeraInputFormat.setInputPaths(job, new Path(args[0]));
        FileOutputFormat.setOutputPath(job, new Path(args[1]));
        job.setJobName("TeraSort");
        job.setJarByClass(TeraSort.class);
        job.setOutputKeyClass(Text.class);
        job.setOutputValueClass(Text.class);
        job.setInputFormat(TeraInputFormat.class);
        job.setOutputFormat(TeraOutputFormat.class);
        job.setPartitionerClass(TotalOrderPartitioner.class);
        TeraInputFormat.writePartitionFile(job, partitionFile);
        DistributedCache.addCacheFile(partitionUri, job);
        DistributedCache.createSymlink(job);
        job.setInt("dfs.replication", 1);
        TeraOutputFormat.setFinalSync(job, true);
        JobClient.runJob(job);
        LOG.info("done");
        return 0;
      }

      /**
       * @param args
       */

      public static void main(String[] args) throws Exception {
        int res = ToolRunner.run(new JobConf(), new TeraSort(), args);
        System.exit(res);
      }

    }

Resumen
-------

Este ejemplo ha demostrado cómo ejecutar una serie de trabajos de MapReduce con HDInsight de Azure, donde la salida de datos para un trabajo se convierte en la entrada para el trabajo siguiente de la serie.

Pasos siguientes
----------------

Para ver tutoriales que ejecutan otras muestras y proporcionan instrucciones sobre el uso de Pig, Hive y los trabajos de MapReduce en HDInsight de Azure con Azure PowerShell, consulte los siguientes temas:

-   [Introducción a HDInsight de Azure](/en-us/manage/services/hdinsight/get-started-hdinsight/)
-   [Muestra: Estimador de Pi](/en-us/manage/services/hdinsight/howto-run-samples/sample-pi-estimator/)
-   [Muestra: WordCount](/en-us/manage/services/hdinsight/howto-run-samples/sample-wordcount/)
-   [Muestra: C\# Steaming](/en-us/manage/services/hdinsight/howto-run-samples/sample-csharp-streaming/)
-   [Uso de Pig con HDInsight](/en-us/manage/services/hdinsight/using-pig-with-hdinsight/)
-   [Uso de Hive con HDInsight](/en-us/manage/services/hdinsight/using-hive-with-hdinsight/)
-   [Azure HDInsight SDK documentation](http://msdnstage.redmond.corp.microsoft.com/en-us/library/dn479185.aspx)

