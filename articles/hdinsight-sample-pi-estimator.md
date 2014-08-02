<properties linkid="manage-services-hdinsight-sample-pi-estimator" urlDisplayName="HDInsight Samples" pageTitle="The HDInsight Pi estimator sample | Azure" metaKeywords="hdinsight, hdinsight sample, mapreduce" description="Learn how to run a simple MapReduce sample on HDInsight." umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" services="hdinsight" documentationCenter="" title="The HDInsight Pi estimator sample" authors="bradsev" />

La muestra del estimador de Pi para HDInsight
=============================================

En este tema se describe cómo ejecutar un programa de MapReduce simple que calcula el valor de la constante matemática Pi con HDInsight de Azure mediante el uso de Azure PowerShell.

El programa utiliza un método estadístico (quasi-Monte Carlo) para calcular el valor de Pi. Los puntos ubicados en el interior aleatorio de un cuadrado unitario también caerá dentro de un círculo inscrito dentro de ese cuadrado con una probabilidad igual al área del círculo, Pi/4. El valor de Pi se puede calcular a partir del valor de 4R, donde R es la proporción de la cantidad de puntos que se encuentran dentro del círculo con respecto a la cantidad total de puntos que se encuentran dentro del cuadrado. Mientras más grande sea la muestra de puntos usada, mejor resulta el valor calculado.

El código PiEstimator Java que contiene las funciones de asignador y reductor está disponible para inspección a continuación. El programa de asignador genera un número especificado de puntos ubicados en el interior aleatorio de un cuadrado unitario y, a continuación, cuenta la cantidad de esos puntos que se encuentran dentro del círculo. El programa de reductor acumula los puntos que cuentan los asignadores y, a continuación, calcula el valor de Pi a partir de la fórmula 4R, donde R es la proporción de la cantidad de puntos contados dentro del círculo con respecto al número total de puntos que se encuentran dentro del cuadrado.

El script que se proporciona para esta muestra envía un trabajo de Hadoop JAR y se configura para ejecutarse con un valor de 16 asignaciones, cada una de las cuales debe procesar 10 millones de puntos de muestra por los valores de parámetro. Estos valores de parámetro se pueden cambiar para mejorar el valor calculado de Pi. Como referencia, las primeras 10 posiciones decimales de Pi son 3,1415926535.

El archivo .jar que contiene los archivos que Hadoop necesita en Azure para implementar la aplicación es un archivo .zip disponible para descarga. Puede utilizar diversas utilidades de compresión para descomprimirlo y, a continuación, puede explorar los archivos cuando le resulte conveniente.

Las demás muestras que se encuentran disponibles para ayudarle a acelerar el proceso con HDInsight para ejecutar trabajos de MapReduce aparecen en [Ejecución de muestras de HDInsight](/en-us/manage/services/hdinsight/howto-run-samples), además de vínculos a instrucciones sobre cómo ejecutarlas.

**Aprenderá a:**

-   Usar Azure PowerShell para ejecutar un programa de MapReduce del estimador de Pi en HDInsight de Azure.
-   El aspecto de un programa de MapReduce escrito en Java.

**Requisitos previos**:

-   Debe tener una cuenta de Azure. Para conocer las opciones para obtener una cuenta, consulte la página de la [prueba gratuita de Azure](http://www.windowsazure.com/en-us/pricing/free-trial/).

-   Debe aprovisionar un clúster de HDInsight. Para obtener instrucciones acerca de varias formas de creación de dichos clústeres, consulte [Aprovisionamiento de clústeres de HDInsight](/en-us/manage/services/hdinsight/provision-hdinsight-clusters/).

-   Debe tener instalado Azure PowerShell y haber configurado los clústeres para utilizarlos con su cuenta. Para obtener instrucciones acerca de cómo hacerlo, consulte [Instalación y configuración de Azure PowerShell](/en-us/documentation/articles/install-configure-powershell/).

En este artículo
----------------

En este tema se muestra cómo ejecutar la muestra, se presenta el código de Java para el programa de MapReduce del estimador de Pi, se resume lo que ha aprendido y se describen algunos de los pasos siguientes. Tiene las siguientes secciones.

1.  [Ejecución de la muestra con Azure PowerShell](#run-sample)
2.  [El código Java para el programa de MapReduce del estimador de Pi](#java-code)
3.  [Resumen](#summary)
4.  [Pasos siguientes](#next-steps)

Ejecución de la muestra con Azure PowerShell
--------------------------------------------

**Para enviar el trabajo de MapReduce**

1.  Abra Azure PowerShell. Para obtener instrucciones acerca de cómo abrir la ventana de la consola de Azure PowerShell, consulte [Instalación y configuración de Azure PowerShell](/en-us/documentation/articles/install-configure-powershell/).
2.  Ajuste las dos variables en los comandos siguientes y, a continuación, ejecútelos:

         $subscriptionName = "<SubscriptionName>"       # Nombre de la suscripción a Azure
         $clusterName = "<ClusterName>"             # Nombre del clúster de HDInsight

3.  Ejecute el siguiente comando para crear una definición de MapReduce:

         $piEstimatorJobDefinition = New-AzureHDInsightMapReduceJobDefinition -JarFile "wasb:///example/jars/hadoop-examples.jar" -ClassName "pi" -Arguments "16", "10000000" 

    > [WACOM.NOTE] *hadoop-examples.jar* viene con la versión 2.1 de clústeres de HDInsight. Se cambió el nombre del archivo a *hadoop-mapreduce.jar* en la versión 3.0 de clústeres de HDInsight.

    El primer argumento indica cuántas asignaciones se deben crear (el valor predeterminado es 16). El segundo argumento indica cuántas muestras se generan por asignación (el valor predeterminado es 10 millones). Por lo tanto, este programa utiliza 10\*10 millones = 160 millones de puntos aleatorios para realizar el cálculo de Pi.

4.  Ejecute los siguientes comandos para enviar el trabajo de MapReduce y espere a que se complete:

         # Ejecutar el trabajo de MapReduce del estimador de Pi.
         Select-AzureSubscription $subscriptionName
         $piJob = $piEstimatorJobDefinition | Start-AzureHDInsightJob -Cluster $clusterName

         # Esperar a que el trabajo finalice.  
         $piJob | Wait-AzureHDInsightJob -Subscription $subscriptionName -WaitTimeoutInSeconds 3600  

5.  Ejecute el siguiente comando para recuperar el archivo de salida estándar del trabajo de MapReduce:

         # Imprimir la salida y el archivo de error estándar del trabajo de MapReduce.
         Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $piJob.JobId -StandardOutput

    Como comparación, las primeras 10 posiciones decimales de Pi son 3,1415926535.

El código Java para el programa de MapReduce del estimador de Pi
----------------------------------------------------------------

	\**
	* Con licencia de Apache Software Foundation (ASF) en virtud de uno 
	* o más contratos de licencia de colaborador. Consulte el archivo AVISO 
	* distribuido con este trabajo para obtener información adicional
	* con relación a la propiedad de derecho de autor. ASF le otorga la licencia de este archivo 
	* en virtud de la licencia de Apache, versión 2.0 (la 
	* "Licencia"); no puede utilizar este archivo, salvo que sea a efectos de conformidad 
	* con la Licencia. Puede obtener una copia de la Licencia en 
	* 

	* http://www.apache.org/licenses/LICENSE-2.0 

	* A menos que así lo requiera la ley aplicable o que se haya acordado por escrito, el software
	* distribuido en virtud de la Licencia se distribuye "TAL CUAL", 
	* SIN GARANTÍAS O CONDICIONES DE CUALQUIER TIPO, ni directa ni indirectamente. 
	* Consulte la Licencia para el lenguaje específico que rige los permisos y 
	* las limitaciones en el marco de la Licencia. 
	* /
	
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

    //Un programa de MapReduce para calcular el valor de Pi  
    //con el método quasi-Monte Carlo.   
    //  
    //Asignador:    
    //Generar puntos en un cuadrado unitario    
    //y, a continuación, contar los puntos dentro/fuera del círculo inscrito en el cuadrado.  
    //  
    //Reductor: 
    //Acumular los resultados de puntos dentro/fuera a partir de los asignadores.   
    //Dejar numTotal = numInside + numOutside.  
    //La fracción numInside/numTotal es una aproximación racional del 
    //valor (Área del círculo)/(Área del cuadrado),    
    //donde el área del círculo inscrito es Pi/4  
    //y el área del cuadrado unitario es 1.  
    //Por lo tanto, el valor calculado de Pi debería ser 4(numInside/numTotal).  
    //  

	public class PiEstimator extends Configured implements Tool {
    //directorio temporal para entrada/salida    
	static private final Path TMP_DIR = new Path( 
	PiEstimator.class.getSimpleName() + "_TMP_3_141592654");

    //secuencia Halton bidimensional {H(i)},     
    //donde H(i) es un punto bidimensional e i >= 1 es el índice.     
    //La secuencia Halton se utiliza para generar puntos de muestra para la estimación de Pi.     
	private static class HaltonSequence {
    // Bases 
	static final int[] P = {2, 3};
    //Número máximo de dígitos permitidos 
	static final int[] K = {63, 40}; 

	private long index; 
	private double[] x; 
	private double[][] q; \
	private int[][] d;

    //Inicializar a H(startindex),   
    //para que la secuencia comience con H(startindex+1).   
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
	x[i] += d[i][j] \* q[i][j]; 
	} 
	} 
	}

    //Procesar el punto siguiente.   
    //Suponer que el punto actual es H(index).  
    //Procesar H(index+1).  
    //@return un punto bidimensional con coordenadas en [0,1)^2     
	double[] nextPoint() { 
	index++; 
	for(int i = 0; i < K.length; i++) { 
	for(int j = 0; j <; K[i]; j++) { 
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

    //Clase de asignador para estimación de Pi.   
    //Generar puntos en un cuadrado unitario y, a continuación,      
    //contar los puntos dentro/fuera del círculo inscrito en el cuadrado.    
	public static class PiMapper extends MapReduceBase 
	implements Mapper<LongWritable, LongWritable, BooleanWritable, LongWritable\> {

    //Método de asignación.    
    //@param desplazar muestras a partir de la muestra (offset+1)th.    
    //@param número de muestras de esta asignación    
    //@param salida {ture->numInside, false->numOutside}  
    //@param informador 
	public void map(LongWritable offset, 
	LongWritable size, 
	OutputCollector<BooleanWritable, LongWritable> out, 
	Reporter reporter) throws IOException { 

	final HaltonSequence haltonsequence = new HaltonSequence(offset.get()); 
	long numInside = 0L; 
	long numOutside = 0L; 

	for(long i = 0; i < size.get(); ) { 
	//generar puntos en un cuadro unitario 
	final double[] point = haltonsequence.nextPoint(); 

	//contar los puntos dentro/fuera del círculo inscrito en el cuadrado 
	final double x = point[0] - 0.5; 
	final double y = point[1] - 0.5; 
	if (x*x + y*y \> 0.25) { 
	numOutside++; 
	} else { 
	numInside++; 
	} 

	//informar del estado 
	i++; 
	if (i % 1000 == 0) { 
	reporter.setStatus("Generated " + i + " samples."); 
	} 
	} 

	
	//generar resultados de asignación 
	out.collect(new BooleanWritable(true), new LongWritable(numInside)); 
	out.collect(new BooleanWritable(false), new LongWritable(numOutside)); 
	} 
	}

    //Clase de reductor para estimación de Pi.        
    //Acumular los resultados de puntos dentro/fuera a partir de los asignadores.       
	public static class PiReducer extends MapReduceBase 
	implements Reducer<BooleanWritable, LongWritable, WritableComparable<?>, Writable> { 

	private long numInside = 0; 
	private long numOutside = 0; 
	private JobConf conf; //configuración para obtener acceso al sistema de archivos

    //Almacenar la configuración del trabajo. 
	@Override 
	public void configure(JobConf job) { 
	conf = job; 
	}

    // Acumular los resultados de la cantidad de puntos dentro/fuera de los asignadores.     
    // @param isInside ¿Los puntos están dentro
    // valores @param Un iterador para una lista de recuento de puntos  
    // @param resultado ficticio, no usado aquí. 
    // @param informador    

	public void reduce(BooleanWritable isInside, 
	Iterator <LongWritable> values, 
	OutputCollector<WritableComparable<?>, Writable> output, 
	Reporter reporter) throws IOException { 
	if (isInside.get()) { 
	for(; values.hasNext(); numInside += values.next().get()); 
	} else { 
	for(; values.hasNext(); numOutside += values.next().get()); 
	} 
	} 

	//Reducir la tarea realizada; escribir el resultado en un archivo. 
	@Override 
	public void close() throws IOException { 
	//escribir el resultado en un archivo 
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

    //Ejecutar un trabajo de asignación/reducción para estimar Pi. 
    //@return el valor estimado de Pi.      
	public static BigDecimal estimate(int numMaps, long numPoints, JobConf jobConf 
	) 
	throws IOException { 
	//configurar el trabajo 
	jobConf.setJobName(PiEstimator.class.getSimpleName()); 

	jobConf.setInputFormat(SequenceFileInputFormat.class); 

	jobConf.setOutputKeyClass(BooleanWritable.class); 
	jobConf.setOutputValueClass(LongWritable.class); 
	jobConf.setOutputFormat(SequenceFileOutputFormat.class); 

	jobConf.setMapperClass(PiMapper.class); 
	jobConf.setNumMapTasks(numMaps); 

	jobConf.setReducerClass(PiReducer.class); 
	jobConf.setNumReduceTasks(1); 

	// desactivar la ejecución especulativa, porque DFS no controla 
	// a varios redactores en el mismo archivo. 
	jobConf.setSpeculativeExecution(false); 

	//configurar los directorios de entrada/salida 
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

     //generar un archivo de entrada para cada tarea de asignación       
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

     //iniciar un trabajo de asignación/reducción     
     System.out.println("Starting Job");
     final long startTime = System.currentTimeMillis();
     JobClient.runJob(jobConf);
     final double duration = (System.currentTimeMillis() - startTime)/1000.0;
     System.out.println("Job Finished in " + duration + " seconds");

     //leer resultados      
     Path inFile = new Path(outDir, "reduce-out");
     LongWritable numInside = new LongWritable();
     LongWritable numOutside = new LongWritable();
     SequenceFile.Reader reader = new SequenceFile.Reader(fs, inFile, jobConf);
     try {
     reader.next(numInside, numOutside);
     } finally {
     reader.close();
     }

     //procesar valor estimado
     return BigDecimal.valueOf(4).setScale(20)
     .multiply(BigDecimal.valueOf(numInside.get()))
     .divide(BigDecimal.valueOf(numMaps))
     .divide(BigDecimal.valueOf(numPoints));
     } finally {
     fs.delete(TMP_DIR, true);
     }
     }

    //Analizar los argumentos y, a continuación, ejecutar un trabajo de asignación/reducción.  
    //Imprimir salida estándar.      
    //@return un valor distinto de cero si hay un error. De lo contrario, se devuelve 0.    
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

     //método principal para ejecutarlo como comando independiente.      
     public static void main(String[] argv) throws Exception {
     System.exit(ToolRunner.run(null, new PiEstimator(), argv));
     }
     }

Resumen
-------

En este tutorial, ha aprendido a ejecutar un trabajo de MapReduce en HDInsight y cómo usar métodos Monte Carlo que requieren y generan grandes conjuntos de datos que este servicio puede administrar.

Pasos siguientes
----------------

Para ver tutoriales que ejecutan otras muestras y proporcionan instrucciones sobre el uso de Pig, Hive y los trabajos de MapReduce en HDInsight de Azure con Azure PowerShell, consulte los siguientes temas:

-   [Introducción a HDInsight de Azure](/en-us/manage/services/hdinsight/get-started-hdinsight/)
-   [Muestra: GraySort de 10 GB](/en-us/manage/services/hdinsight/howto-run-samples/sample-10gb-graysort/)
-   [Muestra: WordCount](/en-us/manage/services/hdinsight/howto-run-samples/sample-wordcount/)
-   [Muestra: C\# Steaming](/en-us/manage/services/hdinsight/howto-run-samples/sample-csharp-streaming/)
-   [Uso de Pig con HDInsight](/en-us/manage/services/hdinsight/using-pig-with-hdinsight/)
-   [Uso de Hive con HDInsight](/en-us/manage/services/hdinsight/using-hive-with-hdinsight/)
-   [Azure HDInsight SDK documentation](http://msdnstage.redmond.corp.microsoft.com/en-us/library/dn479185.aspx)

