<properties
	pageTitle="Muestra de MapReduce de Hadoop para GraySort de 10 GB | Microsoft Azure"
	description="Aprenda a ejecutar una muestra de GraySort de uso general para grandes volúmenes de datos (normalmente un mínimo de 100 TB) en Hadoop con HDInsight mediante Azure PowerShell."
	editor="cgronlun"
	manager="paulettm"
	services="hdinsight"
	documentationCenter=""
	authors="bradsev"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/30/2015" 
	ms.author="bradsev"/>

# Muestra de MapReduce de Hadoop para GraySort de 10 GB en HDInsight

En este tema de ejemplo se muestra cómo ejecutar un programa de MapReduce de Hadoop para GraySort de uso general en HDInsight de Azure con Azure PowerShell. GraySort es un tipo de banco de pruebas cuya métrica es la velocidad de ordenación (TB/minuto) que se logra después de ordenar enormes volúmenes de datos, normalmente 100 TB como mínimo.

Este ejemplo utiliza solo 10 GB de datos, para así poder ejecutarlo relativamente rápido. En él se emplean las aplicaciones de MapReduce, desarrolladas por Owen O'Malley y Arun Murthy, que ganaron el estándar de comparación anual de ordenación de terabytes de fin general ("daytona") en 2009 con una velocidad de 0,578 TB/min (100 TB en 173 minutos). Para obtener más información sobre este y otros estándares de comparación de ordenación, consulte el sitio [Sortbenchmark](http://sortbenchmark.org/).

Este ejemplo utiliza tres conjuntos de programas de MapReduce:

1. **TeraGen** es un programa de MapReduce que puede utilizarse para generar las filas de datos que se van a ordenar.
2. **TeraSort** toma una muestra de los datos de entrada y usa MapReduce para ordenar los datos de manera absoluta. TeraSort es un ordenamiento estándar de funciones de MapReduce, con la excepción de un particionador personalizado que utiliza una lista ordenada de N-1 claves de muestra que definen el rango de claves para cada reducción. En concreto, todas las claves, como esa muestra[i-1] <= clave < muestra[i] se envían a la reducción i. Esto garantiza que las salidas de la reducción i sean todas menores que la salida de la reducción i+1.
3. **TeraValidate** es un programa de MapReduce que valida que la salida se ordene de manera global. Crea una asignación por archivo en el directorio de salida y cada asignación asegura que cada clave es menor o igual que la anterior. La función de asignación también genera registros de la primera y la última clave de cada archivo, y la función de reducción garantiza que la primera clave del archivo i es mayor que la última clave de archivo i-1. Los problemas se notifican como una salida de la reducción con las claves que no están en orden.

El formato de entrada y salida, que utilizan las tres aplicaciones, lee y escribe los archivos de texto en el formato correcto. La salida de la reducción tiene la replicación definida en 1, en lugar del valor predeterminado 3, porque el concurso de estándar de comparación no requiere que los datos de salida se repliquen en varios nodos.


**Aprenderá:** * cómo usar Azure PowerShell para ejecutar una serie de programas de MapReduce en HDInsight de Azure. * El aspecto de un programa de MapReduce escrito en Java.


**Requisitos previos:**

- **Una suscripción de Azure**. Vea [Obtener evaluación gratuita de Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- **un clúster de HDInsight**. Para obtener instrucciones acerca de varias formas de creación de dichos clústeres, consulte [Aprovisionamiento de clústeres de HDInsight](hdinsight-provision-clusters.md).
- **Una estación de trabajo con Azure PowerShell**. Consulte [Instalación y uso de Azure PowerShell](http://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/).



##Ejecución de la muestra con Azure PowerShell

El ejemplo requiere tres tareas, cada una de las cuales corresponde a uno de los programas de MapReduce que se describen en la introducción:

1. Ejecute el trabajo de MapReduce **TeraGen** para generar los datos que se van a ordenar.
2. Ejecute el trabajo de MapReduce **TeraSort** para ordenar los datos.
3. Ejecute el trabajo de MapReduce **TeraValidate** para confirmar que los datos se han ordenado correctamente.


Para ejecutar el programa **TeraGen**, siga estos pasos:

1. Abra Azure PowerShell. Para obtener instrucciones acerca de cómo abrir la ventana de la consola de Azure PowerShell, consulte [Instalación y configuración de Azure PowerShell][powershell-install-configure].
2. Ajuste las dos variables en los comandos siguientes y, a continuación, ejecútelos:

		# Provide the Azure subscription name and the HDInsight cluster name
		$subscriptionName = "myAzureSubscriptionName"
		$clusterName = "myClusterName"

4. Ejecute el siguiente comando para crear una definición del trabajo de MapReduce:

		# Create a MapReduce job definition for the TeraGen MapReduce program
		$teragen = New-AzureHDInsightMapReduceJobDefinition -JarFile "/example/jars/hadoop-mapreduce-examples.jar" -ClassName "teragen" -Arguments "-Dmapred.map.tasks=50", "100000000", "/example/data/10GB-sort-input"

	El argumento *"-Dmapred.map.tasks=50"* especifica que se crearán 50 asignaciones para ejecutar el trabajo. El argumento *100000000* especifica la cantidad de datos que se van a generar. El argumento final, */example/data/10GB-sort-input*, especifica el directorio de salida en el que se guardarán los resultados (y que contiene la entrada para la siguiente fase de ordenación).

5. Ejecute los siguientes comandos para iniciar el trabajo, espere a que finalice e imprima el error estándar:

		# Run the TeraGen MapReduce job
		# Wait for the job to finish
		# Print output and standard error file of the MapReduce job
		Select-AzureSubscription $subscriptionName
		$teragen | Start-AzureHDInsightJob -Cluster $clustername | Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 | Get-AzureHDInsightJobOutput -Cluster $clustername -StandardError


**Para ejecutar el programa TeraSort, siga estos pasos**

1. Abra Azure PowerShell.
2. Ajuste las dos variables en los comandos siguientes y, a continuación, ejecútelos:

		# Provide the Azure subscription name and the HDInsight cluster name
		$subscriptionName = "myAzureSubscriptionName"
		$clusterName = "myClusterName"

3. Ejecute el siguiente comando para definir el trabajo de MapReduce:

		# Create a MapReduce job definition for the TeraSort MapReduce program
		$terasort = New-AzureHDInsightMapReduceJobDefinition -JarFile "/example/jars/hadoop-mapreduce-examples.jar" -ClassName "terasort" -Arguments "-Dmapred.map.tasks=50", "-Dmapred.reduce.tasks=25", "/example/data/10GB-sort-input", "/example/data/10GB-sort-output"

	El argumento *"-Dmapred.map.tasks=50"* especifica que se crearán 50 asignaciones para ejecutar el trabajo. El argumento *100000000* especifica la cantidad de datos que se van a generar. Los últimos dos especifican los directorios de entrada y salida.

4. Ejecute el siguiente comando para iniciar el trabajo, espere a que finalice e imprima el error estándar:

		# Run the TeraSort MapReduce job
		# Wait for the job to finish
		# Print output and standard error file of the MapReduce job
		Select-AzureSubscription $subscriptionName
		$terasort | Start-AzureHDInsightJob -Cluster $clustername | Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 | Get-AzureHDInsightJobOutput -Cluster $clustername -StandardError


**Para ejecutar el programa TeraValidate, siga estos pasos**

1. Abra Azure PowerShell.
2. Ajuste las dos variables en los comandos siguientes y, a continuación, ejecútelos:

		# Provide the Azure subscription name and the HDInsight cluster name
		$subscriptionName = "myAzureSubscriptionName"
		$clusterName = "myClusterName"

3. Ejecute el siguiente comando para definir el trabajo de MapReduce:

		#	Create a MapReduce job definition for the TeraValidate MapReduce program
		$teravalidate = New-AzureHDInsightMapReduceJobDefinition -JarFile "/example/jars/hadoop-mapreduce-examples.jar" -ClassName "teravalidate" -Arguments "-Dmapred.map.tasks=50", "-Dmapred.reduce.tasks=25", "/example/data/10GB-sort-output", "/example/data/10GB-sort-validate"

	El argumento *"-Dmapred.map.tasks=50"* especifica que se crearán 50 asignaciones para ejecutar el trabajo. El argumento *"-Dmapred.reduce.tasks=25"* especifica que se crearán 25 tareas de reducción para ejecutar el trabajo. Los últimos dos especifican los directorios de entrada y salida.


4. Ejecute los siguientes comandos para iniciar el trabajo de MapReduce, espere a que finalice e imprima el error estándar:

		# Run the TeraSort MapReduce job
		# Wait for the job to finish
		# Print output and standard error file of the MapReduce job
		Select-AzureSubscription $subscriptionName
		$teravalidate | Start-AzureHDInsightJob -Cluster $clustername | Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 | Get-AzureHDInsightJobOutput -Cluster $clustername -StandardError


##El código Java para el programa de MapReduce de TeraSort

En esta sección se analiza el código del programa de MapReduce de TeraSort.


	/**
	 * Licensed to the Apache Software Foundation (ASF) under one
	 * or more contributor license agreements.  See the NOTICE file
	 * distributed with this work for additional information
	 * regarding copyright ownership.  The ASF licenses this file
	 * to you under the Apache License, Version 2.0 (the
	 * "License"); you may not use this file except in compliance
	 * with the License.  You may obtain a copy of the License at
	 *
	 *     http://www.apache.org/licenses/LICENSE-2.0
	 *
	 * Unless required by applicable law or agreed to in writing, software
	 * distributed under the License is distributed on an "AS IS" BASIS,
	 * WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
	 * See the License for the specific language governing permissions and
	 * limitations under the License.
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
	 * Generates the sampled split points, launches the job,
	 * and waits for it to finish.
	 * <p>
	 * To run the program:
	 * <b>bin/hadoop jar hadoop-examples-*.jar terasort in-dir out-dir</b>
	 */

	public class TeraSort extends Configured implements Tool {
	  private static final Log LOG = LogFactory.getLog(TeraSort.class);

	  /**
	   * A partitioner that splits text keys into roughly equal
	   * partitions in a global sorted order.
	   */

	  static class TotalOrderPartitioner implements Partitioner<Text,Text>{
	    private TrieNode trie;
	    private Text[] splitPoints;

	    /**
	     * A generic trie node
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
	     * An inner trie node that contains 256 children based on the next
	     * character.
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
	     * A leaf trie node that does string compares to figure out where the given
	     * key belongs between lower..upper.
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
	     * Read the cut points from the given sequence file.
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
	     * Given a sorted set of cut points, build a trie that will find the correct
	     * partition quickly.
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
	      // append an extra byte on to the prefix
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
	      // pick up the rest
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


##Resumen

En este ejemplo se ha mostrado cómo ejecutar una serie de trabajos de MapReduce con HDInsight de Azure, donde la salida de datos para un trabajo se convierte en la entrada para el trabajo siguiente de la serie.

##Pasos siguientes

Para ver tutoriales que le guíen en la ejecución de otras muestras y ofrezcan instrucciones sobre el uso de Pig, Hive y los trabajos de MapReduce en HDInsight de Azure con Azure PowerShell, consulte los siguientes temas:

* [Introducción a HDInsight de Azure][hdinsight-get-started]
* [Muestra: estimador de pi][hdinsight-sample-pi-estimator]
* [Muestra: contar palabras][hdinsight-sample-wordcount]
* [Muestra: streaming en C#][hdinsight-sample-csharp-streaming]
* [Uso de Pig con HDInsight][hdinsight-use-pig]
* [Uso de Hive con HDInsight][hdinsight-use-hive]
* [Documentación de SDK de HDInsight de Azure][hdinsight-sdk-documentation]

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx


[powershell-install-configure]: ../install-configure-powershell.md

[hdinsight-get-started]: ../hdinsight-get-started.md

[hdinsight-samples]: hdinsight-run-samples.md
[hdinsight-sample-10gb-graysort]: hdinsight-sample-10gb-graysort.md
[hdinsight-sample-csharp-streaming]: hdinsight-sample-csharp-streaming.md
[hdinsight-sample-pi-estimator]: hdinsight-sample-pi-estimator.md
[hdinsight-sample-wordcount]: hdinsight-sample-wordcount.md

[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
 

<!---HONumber=62-->