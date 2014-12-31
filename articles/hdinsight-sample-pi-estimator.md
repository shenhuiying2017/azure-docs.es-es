<properties urlDisplayName="Hadoop Samples in HDInsight" pageTitle="Muestra de Hadoop del estimador de Pi en HDInsight | Azure" metaKeywords="hdinsight, hdinsight sample,  hadoop, mapreduce" description="Learn how to run an Hadoop MapReduce sample on HDInsight." umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" services="hdinsight" documentationCenter="" title="The Pi estimator Hadoop sample in HDInsight" authors="bradsev" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="11/10/2014" ms.author="bradsev" />

# Muestra de Hadoop del estimador de Pi en HDInsight
 
En este tema se describe cómo ejecutar un programa de MapReduce de Hadoop que calcula el valor de la constante matemática Pi usando Azure PowerShell para ejecutar el programa en HDInsight. También proporciona el código de Java usado en el programa de MapReduce para estimar el valor de Pi.

El programa utiliza un método estadístico (quasi-Monte Carlo) para calcular el valor de Pi. Los puntos colocados en el interior aleatorio de un cuadrado unitario también entran dentro de un círculo inscrito dentro de ese cuadrado con una probabilidad igual al área del círculo, Pi/4. El valor de Pi se puede estimar a partir del valor de 4R, donde R es la proporción de la cantidad de puntos contados dentro del círculo con respecto al número total de puntos que se encuentran dentro del cuadrado. Mientras más grande sea la muestra de puntos usada, mejor resulta el valor calculado.

El código PiEstimator Java que contiene las funciones de asignador y reductor está disponible para inspección a continuación. El programa de asignador genera un número especificado de puntos ubicados en el interior aleatorio de un cuadrado unitario y, a continuación, cuenta la cantidad de esos puntos que se encuentran dentro del círculo. El programa de reductor acumula los puntos que cuentan los asignadores y, a continuación, calcula el valor de Pi a partir de la fórmula 4R, donde R es la proporción de la cantidad de puntos contados dentro del círculo con respecto al número total de puntos que se encuentran dentro del cuadrado.

El script que se proporciona para esta muestra envía un trabajo de Hadoop JAR y se configura para ejecutarse con un valor de 16 asignaciones, cada una de las cuales debe procesar 10 millones de puntos de muestra por los valores de parámetro. Estos valores de parámetro se pueden cambiar para mejorar el valor calculado de Pi. Como referencia, las primeras 10 posiciones decimales de Pi son 3,1415926535.

El archivo .jar que contiene los archivos que Hadoop necesita en Azure para implementar la aplicación es un archivo .zip disponible para descarga. Puede utilizar diversas utilidades de compresión para descomprimirlo y, a continuación, puede explorar los archivos cuando le resulte conveniente.

Las demás muestras que se encuentran disponibles para ayudarle a acelerar el proceso con HDInsight para ejecutar trabajos de MapReduce aparecen en [Ejecución de muestras de HDInsight][hdinsight-samples], además de vínculos a instrucciones sobre cómo ejecutarlas.

**Aprenderá a:**
		
* Usar Azure PowerShell para ejecutar un programa de MapReduce del estimador de Pi en HDInsight de Azure.	
* El aspecto de un programa de MapReduce escrito en Java.

**Requisitos previos**:	

- Debe tener una cuenta de Azure. Para conocer las opciones para obtener una cuenta, consulte la página de la [prueba gratuita de Azure](http://azure.microsoft.com/en-us/pricing/free-trial/) .

- Debe aprovisionar un clúster de HDInsight. Para obtener instrucciones acerca de varias formas de creación de dichos clústeres, consulte [Aprovisionamiento de clústeres de HDInsight](../hdinsight-provision-clusters/)

- Debe tener instalado Azure PowerShell y haber configurado los clústeres para utilizarlos con su cuenta. Para obtener instrucciones acerca de cómo hacerlo, consulte [Instalación y configuración de Azure PowerShell][powershell-install-configure].

##En este artículo	
En este tema se muestra cómo ejecutar la muestra, se presenta el código de Java para el programa de MapReduce del estimador de Pi, se resume lo que ha aprendido y se describen algunos de los pasos siguientes. Tiene las siguientes secciones.
	
1. [Ejecución de la muestra con Azure PowerShell](#run-sample)	
2. [El código Java para el programa de MapReduce del estimador de Pi](#java-code)
3. [Resumen](#summary)	
4. [Pasos siguientes](#next-steps)	

<h2><a id="run-sample"></a>Ejecución de la muestra con Azure PowerShell</h2>

**Para enviar el trabajo de MapReduce, siga estos pasos:**

1. Abra Azure PowerShell. Para obtener instrucciones acerca de cómo abrir la ventana de la consola de Azure PowerShell, consulte [Instalación y configuración de Azure PowerShell][powershell-install-configure].
2. Ajuste las dos variables en los comandos siguientes y, a continuación, ejecútelos:
	
		$subscriptionName = "<SubscriptionName>"   # Azure subscription name
		$clusterName = "<ClusterName>"             # HDInsight cluster name

4. Ejecute el siguiente comando para crear una definición de MapReduce:	

		$piEstimatorJobDefinition = New-AzureHDInsightMapReduceJobDefinition -JarFile "wasb:///example/jars/hadoop-mapreduce-examples.jar" -ClassName "pi" -Arguments "16", "10000000" 

	
	El primer argumento indica cuántas asignaciones se deben crear (el valor predeterminado es 16). El segundo argumento indica cuántas muestras se generan por asignación (el valor predeterminado es 10 millones). Por lo tanto, este programa utiliza 10*10 millones = 160 millones de puntos aleatorios para realizar el cálculo de Pi. El tercer argumento indica la ubicación y el nombre del archivo jar usado para ejecutar la muestra en clústeres de HDInsight 3.0 y 3.1. (Consulte a continuación el contenido de este archivo).

5. Ejecute los siguientes comandos para enviar el trabajo de MapReduce y espere a que se complete:

		# Run the Pi Estimator MapReduce job.
		Select-AzureSubscription $subscriptionName
		$piJob = $piEstimatorJobDefinition | Start-AzureHDInsightJob -Cluster $clusterName
	
		# Wait for the job to complete.  
		$piJob | Wait-AzureHDInsightJob -Subscription $subscriptionName -WaitTimeoutInSeconds 3600  

6. Ejecute el siguiente comando para recuperar el archivo de salida estándar del trabajo de MapReduce:

		# Print output and standard error file of the MapReduce job
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $piJob.JobId -StandardOutput

	Como comparación, las primeras 10 posiciones decimales de Pi son 3,1415926535.


<h2><a id="java-code"></a>El código Java para el programa de MapReduce del estimador de Pi</h2>



 	/**	
 	* Licensed to the Apache Software Foundation (ASF) under one	
 	* or more contributor license agreements. See the NOTICE file	
 	* distributed with this work for additional information	
 	* regarding copyright ownership. The ASF licenses this file	
 	* to you under the Apache License, Version 2.0 (the	
 	* "License"); you may not use this file except in compliance	
 	* with the License. You may obtain a copy of the License at	
 	*	
	* http://www.apache.org/licenses/LICENSE-2.0	
 	*	
 	* Unless required by applicable law or agreed to in writing, software	
 	* distributed under the License is distributed on an "AS IS" BASIS,	
 	* WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or 	implied.
 	* See the License for the specific language governing permissions and	
 	* limitations under the License.		
 	*/	

 	package org.apache.hadoop.examples;

 	import java.io.IOException;
 	import java.math.BigDecimal;
 	import java.util.Iterator;

 	import org.apache.hadoop.conf.Configured;
 	import org.apache.hadoop.fs.FileSystem;
 	import org.apache.hadoop.fs.Path;
 	import org.apache.hadoop.io.BooleanWritable;
 	import org.apache.hadoop.io.LongWritable;
 	import org.apache.hadoop.io.SequenceFile;
 	import org.apache.hadoop.io.Writable;
 	import org.apache.hadoop.io.WritableComparable;
 	import org.apache.hadoop.io.SequenceFile.CompressionType;
 	import org.apache.hadoop.mapred.FileInputFormat;
 	import org.apache.hadoop.mapred.FileOutputFormat;
 	import org.apache.hadoop.mapred.JobClient;
 	import org.apache.hadoop.mapred.JobConf;
 	import org.apache.hadoop.mapred.MapReduceBase;
 	import org.apache.hadoop.mapred.Mapper;
 	import org.apache.hadoop.mapred.OutputCollector;
 	import org.apache.hadoop.mapred.Reducer;
 	import org.apache.hadoop.mapred.Reporter;
 	import org.apache.hadoop.mapred.SequenceFileInputFormat;
 	import org.apache.hadoop.mapred.SequenceFileOutputFormat;
 	import org.apache.hadoop.util.Tool;
 	import org.apache.hadoop.util.ToolRunner;


	//A Map-reduce program to estimate the value of Pi	
	//using quasi-Monte Carlo method.	
	//	
	//Mapper:	
	//Generate points in a unit square	
	//and then count points inside/outside of the inscribed circle of the square.	
	//	
	//Reducer:	
	//Accumulate points inside/outside results from the mappers.	
	//Let numTotal = numInside + numOutside.	
	//The fraction numInside/numTotal is a rational approximation of	
	//the value (Area of the circle)/(Area of the square),	
	//where the area of the inscribed circle is Pi/4	
	//and the area of unit square is 1.	
	//Then, Pi is estimated value to be 4(numInside/numTotal). 	
	//	

 	public class PiEstimator extends Configured implements Tool {	
	//tmp directory for input/output	
 	static private final Path TMP_DIR = new Path(	
 	PiEstimator.class.getSimpleName() + "_TMP_3_141592654");	

	//2-dimensional Halton sequence {H(i)},		
	//where H(i) is a 2-dimensional point and i >= 1 is the index.		
	//Halton sequence is used to generate sample points for Pi estimation.	 
 	private static class HaltonSequence {	
	// Bases	
 	static final int[] P = {2, 3}; 	
	//Maximum number of digits allowed	
 	static final int[] K = {63, 40}; 	

 	private long index;	
 	private double[] x;	
 	private double[][] q;	
 	private int[][] d;	

	//Initialize to H(startindex),	
	//so the sequence begins with H(startindex+1).	
 	HaltonSequence(long startindex) {	
 	index = startindex;	
 	x = new double[K.length];	
 	q = new double[K.length][];	
 	d = new int[K.length][];	
 	for(int i = 0; i < K.length; i++) {	
 	q[i] = new double[K[i]];	
 	d[i] = new int[K[i]];	
 	}

 	for(int i = 0; i < K.length; i++) {
 	long k = index;
 	x[i] = 0;
	
 	for(int j = 0; j < K[i]; j++) {	
 	q[i][j] = (j == 0? 1.0: q[i][j-1])/P[i];	
 	d[i][j] = (int)(k % P[i]);	
 	k = (k - d[i][j])/P[i];	
 	x[i] += d[i][j] * q[i][j];	
 	}	
 	}
 	}

	//Compute next point.	
	//Assume the current point is H(index).	
	//Compute H(index+1).	
	//@return a 2-dimensional point with coordinates in [0,1)^2		
 	double[] nextPoint() {		
 	index++;	
 	for(int i = 0; i < K.length; i++) {	
 	for(int j = 0; j < K[i]; j++) {	
 	d[i][j]++;	
 	x[i] += q[i][j];	
 	if (d[i][j] < P[i]) {	
 	break;		
 	}	
 	d[i][j] = 0;	
 	x[i] -= (j == 0? 1.0: q[i][j-1]);	
 	}	
 	}	
 	return x;	
 	}	
 	}	

	//Mapper class for Pi estimation.	
	//Generate points in a unit square and then		
	//count points inside/outside of the inscribed circle of the square.	
 	public static class PiMapper extends MapReduceBase
 	implements Mapper<LongWritable, LongWritable, BooleanWritable, LongWritable> {

	//Map method.	
	//@param offset samples starting from the (offset+1)th sample.	
	//@param size the number of samples for this map	
	//@param out output {ture->numInside, false->numOutside}	
	//@param reporter	
 	public void map(LongWritable offset,
 	LongWritable size,
 	OutputCollector<BooleanWritable, LongWritable> out,
 	Reporter reporter) throws IOException {

 	final HaltonSequence haltonsequence = new HaltonSequence(offset.get());		
 	long numInside = 0L;	
 	long numOutside = 0L;	

 	for(long i = 0; i < size.get(); ) {
 	//generate points in a unit square
 	final double[] point = haltonsequence.nextPoint();

 	//count points inside/outside of the inscribed circle of the square
 	final double x = point[0] - 0.5;
 	final double y = point[1] - 0.5;
 	if (x*x + y*y > 0.25) {
 	numOutside++;
 	} else {
 	numInside++;
 	}

 	//report status
 	i++;
 	if (i % 1000 == 0) {
 	reporter.setStatus("Generated " + i + " samples.");
 	}
 	}

 	//output map results
 	out.collect(new BooleanWritable(true), new LongWritable(numInside));
 	out.collect(new BooleanWritable(false), new LongWritable(numOutside));
 	}
 	}


	//Reducer class for Pi estimation.		
	//Accumulate points inside/outside results from the mappers.		
 	public static class PiReducer extends MapReduceBase
 	implements Reducer<BooleanWritable, LongWritable, WritableComparable<?>, Writable> {

 	private long numInside = 0;
 	private long numOutside = 0;
 	private JobConf conf; //configuration for accessing the file system

	//Store job configuration.	
 	@Override	
 	public void configure(JobConf job) {
 	conf = job;
 	}


	// Accumulate number of points inside/outside results from the mappers.		
	// @param isInside Is the points inside? 	
	// @param values An iterator to a list of point counts	
	// @param output dummy, not used here.	
	// @param reporter	

 	public void reduce(BooleanWritable isInside,
 	Iterator<LongWritable> values,
 	OutputCollector<WritableComparable<?>, Writable> output,
 	Reporter reporter) throws IOException {
 	if (isInside.get()) {
 	for(; values.hasNext(); numInside += values.next().get());
 	} else {
 	for(; values.hasNext(); numOutside += values.next().get());
 	}
 	}
	
 	//Reduce task done, write output to a file.	
 	@Override	
 	public void close() throws IOException {
 	//write output to a file
 	Path outDir = new Path(TMP_DIR, "out");
 	Path outFile = new Path(outDir, "reduce-out");
 	FileSystem fileSys = FileSystem.get(conf);
 	SequenceFile.Writer writer = SequenceFile.createWriter(fileSys, conf,
 	outFile, LongWritable.class, LongWritable.class, 
 	CompressionType.NONE);
 	writer.append(new LongWritable(numInside), new LongWritable(numOutside));
 	writer.close();
 	}
 	}
	
	//Run a map/reduce job for estimating Pi.	
	//@return the estimated value of Pi.		
 	public static BigDecimal estimate(int numMaps, long numPoints, JobConf jobConf
 	) 	
 	throws IOException {
 	//setup job conf
 	jobConf.setJobName(PiEstimator.class.getSimpleName());
	
 	jobConf.setInputFormat(SequenceFileInputFormat.class);
	
 	jobConf.setOutputKeyClass(BooleanWritable.class);
 	jobConf.setOutputValueClass(LongWritable.class);
 	jobConf.setOutputFormat(SequenceFileOutputFormat.class);
	
 	jobConf.setMapperClass(PiMapper.class);
 	jobConf.setNumMapTasks(numMaps);
	
 	jobConf.setReducerClass(PiReducer.class);
 	jobConf.setNumReduceTasks(1);
	
 	// turn off speculative execution, because DFS doesn't handle		
 	// multiple writers to the same file.		
 	jobConf.setSpeculativeExecution(false);
	
 	//setup input/output directories	
 	final Path inDir = new Path(TMP_DIR, "in");	
 	final Path outDir = new Path(TMP_DIR, "out");	
 	FileInputFormat.setInputPaths(jobConf, inDir);	
 	FileOutputFormat.setOutputPath(jobConf, outDir);	
	
 	final FileSystem fs = FileSystem.get(jobConf);		
 	if (fs.exists(TMP_DIR)) {	
	 throw new IOException("Tmp directory " + fs.makeQualified(TMP_DIR)
	 + " already exists. Please remove it first.");	
	 }	
	 if (!fs.mkdirs(inDir)) {	
	 throw new IOException("Cannot create input directory " + inDir);	
	 }	

	 //generate an input file for each map task		
	 try {
	 for(int i=0; i < numMaps; ++i) {
	 final Path file = new Path(inDir, "part"+i);	
	 final LongWritable offset = new LongWritable(i * numPoints);	
	 final LongWritable size = new LongWritable(numPoints);	
	 final SequenceFile.Writer writer = SequenceFile.createWriter(
	 fs, jobConf, file,
	 LongWritable.class, LongWritable.class, CompressionType.NONE);
	 try {
	 writer.append(offset, size);
	 } finally {
	 writer.close();	
	 }
	 System.out.println("Wrote input for Map #"+i);
	 }
	
	 //start a map/reduce job		
	 System.out.println("Starting Job");
	 final long startTime = System.currentTimeMillis();
	 JobClient.runJob(jobConf);
	 final double duration = (System.currentTimeMillis() - startTime)/1000.0;
	 System.out.println("Job Finished in " + duration + " seconds");
	
	 //read outputs		
	 Path inFile = new Path(outDir, "reduce-out");
	 LongWritable numInside = new LongWritable();
	 LongWritable numOutside = new LongWritable();
	 SequenceFile.Reader reader = new SequenceFile.Reader(fs, inFile, jobConf);
	 try {
	 reader.next(numInside, numOutside);
	 } finally {
	 reader.close();
	 }
	
	 //compute estimated value
	 return BigDecimal.valueOf(4).setScale(20)
	 .multiply(BigDecimal.valueOf(numInside.get()))
	 .divide(BigDecimal.valueOf(numMaps))
	 .divide(BigDecimal.valueOf(numPoints));
	 } finally {
	 fs.delete(TMP_DIR, true);
	 }
	 }
	
	//Parse arguments and then runs a map/reduce job.	
	//Print output in standard out.		
	//@return a non-zero if there is an error. Otherwise, return 0. 	
	 public int run(String[] args) throws Exception {
	 if (args.length != 2) {
	 System.err.println("Usage: "+getClass().getName()+" <nMaps> <nSamples>");
	 ToolRunner.printGenericCommandUsage(System.err);
	 return -1;
	 }

	 final int nMaps = Integer.parseInt(args[0]);
	 final long nSamples = Long.parseLong(args[1]);
	
	 System.out.println("Number of Maps = " + nMaps);
	 System.out.println("Samples per Map = " + nSamples);
	
	 final JobConf jobConf = new JobConf(getConf(), getClass());
	 System.out.println("Estimated value of Pi is "
	 + estimate(nMaps, nSamples, jobConf));
	 return 0;
	 }
	
	 //main method for running it as a stand alone command. 		
	 public static void main(String[] argv) throws Exception {
	 System.exit(ToolRunner.run(null, new PiEstimator(), argv));
	 }
	 }
  

<h2><a id="summary"></a>Resumen</h2>

En este tutorial, ha aprendido a ejecutar un trabajo de MapReduce en HDInsight y cómo usar métodos Monte Carlo que requieren y generan grandes conjuntos de datos que este servicio puede administrar.

Aquí está el script completo usado para ejecutar esta muestra en un clúster predeterminado de HDInsight 3.1 o 3.0. 

	### Provide the Windows Azure subscription name and the HDInsight cluster name. 
	$subscriptionName = "<SubscriptionName>" 
	$clusterName = "<ClusterName>"  

	###Select the Azure subscription to use.
	Select-AzureSubscription $subscriptionName 

	### Create a MapReduce job definition. 
	$piEstimatorJobDefinition = New-AzureHDInsightMapReduceJobDefinition -ClassName "pi" -Arguments "32", "1000000000" -JarFile "wasb:///example/jars/hadoop-mapreduce-examples.jar"

	### Run the MapReduce job. 
	$piJob = $piEstimatorJobDefinition | Start-AzureHDInsightJob -Cluster $clusterName
 
	### Wait for the job to complete.  
	$piJob | Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600

	### Print the standard error file of the MapReduce job.
	Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $piJob.JobId -StandardOutput



<h2><a id="next-steps"></a>Pasos siguientes</h2>

Para ver tutoriales que ejecutan otras muestras y proporcionan instrucciones sobre el uso de Pig, Hive y los trabajos de MapReduce en HDInsight de Azure con Azure PowerShell, consulte los siguientes temas:

* [Introducción a Azure HDInsight][hdinsight-get-started]
* [Muestra: GraySort de 10 GB][hdinsight-sample-10gb-graysort]
* [Muestra: Wordcount][hdinsight-sample-wordcount]
* [Muestra: C# Steaming][hdinsight-sample-csharp-streaming]
* [Uso de Pig con HDInsight][hdinsight-use-pig]
* [Uso de Hive con HDInsight][hdinsight-use-hive]
* [Documentación del SDK de HDInsight de Azure][hdinsight-sdk-documentation]

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/en-us/library/dn479185.aspx

[Powershell-install-configure]: ../install-configure-powershell/

[hdinsight-get-started]: ../hdinsight-get-started/

[hdinsight-samples]: ../hdinsight-run-samples/
[hdinsight-sample-10gb-graysort]: ../hdinsight-sample-10gb-graysort/
[hdinsight-sample-csharp-streaming]: ../hdinsight-sample-csharp-streaming/
[hdinsight-sample-pi-estimator]: ../hdinsight-sample-pi-estimator/
[hdinsight-sample-wordcount]: ../hdinsight-sample-wordcount/

[hdinsight-use-hive]: ../hdinsight-use-hive/
[hdinsight-use-pig]: ../hdinsight-use-pig/
 


<!--HONumber=35_1-->
