<properties urlDisplayName="Use Hadoop Hive in HDInsight" pageTitle="Uso de Hive de Hadoop en HDInsight | Azure" metaKeywords="" description="Learn how to use Hive with HDInsight. You'll use a log file as input into an HDInsight table, and use HiveQL to query the data and report basic statistics." metaCanonical="" services="hdinsight" documentationCenter="" title="Use Hadoop Hive in HDInsight" authors="jgao" solutions="" manager="paulettm" editor="cgronlun" videoId="" scriptId="" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/25/2014" ms.author="jgao" />

# Uso de Hive con Hadoop en HDInsight

[Apache Hive][apache-hive] ofrece la posibilidad de ejecutar un trabajo de MapReduce a través de un lenguaje de scripting similar a SQL, denominado *HiveQ*. Hive es un sistema de almacén de datos para Hadoop, que permite realizar resúmenes de datos, consultas y análisis de grandes volúmenes de datos. En este artículo, utilizará HiveQL para consultar un archivo de datos de ejemplo que se incluye como parte del aprovisionamiento de clústeres de HDInsight.


**Requisitos previos:**

- Debe aprovisionar un **clúster de HDInsight**. Para saber cómo hacerlo con el Portal de Azure, consulte [Introducción a HDInsight][hdinsight-get-started]. Para obtener instrucciones acerca de otras formas de creación de dichos clústeres, consulte [Aprovisionamiento de clústeres de HDInsight][hdinsight-provision].

- Debe tener instalado **Azure PowerShell** en su estación de trabajo. Para obtener instrucciones acerca de cómo hacerlo, consulte [Instalación y configuración de Azure PowerShell][powershell-install-configure].

##En este artículo

* [Caso de uso de Hive](#usage)
* [Carga de datos en tablas de Hive](#uploaddata)
* [Ejecución de las consultas de Hive usando PowerShell](#runhivequeries)
* [Ejecución de las consultas de Hive usando HDInsight Tools for Visual Studio](#runhivefromvs)
* [Uso de Tez para un mejor rendimiento](#usetez)
* [Pasos siguientes](#nextsteps)

##<a id="usage"></a>Caso de uso de Hive

![HDI.HIVE.Architecture][image-hdi-hive-architecture]

Los proyectos de Hive se estructuran en datos en gran parte no estructurados que luego permiten realizar consultas en esos datos. Hive proporciona una capa de abstracción sobre el marco de MapReduce basado en Java, que permite a los usuarios consultar datos sin conocimiento de Java o de MapReduce. HiveQL, el idioma de consultas de Hive, permite escribir consultas con instrucciones similares a T-SQL. Las consultas de HiveQL se recopilan en MapReduce mediante HDInsight y se ejecutan en el clúster. Otras ventajas de Hive son las siguientes:

- Hive permite que los programadores familiarizados con el marco de MapReduce conecten asignadores y reductores personalizados para realizar un análisis más sofisticado que podría no ser compatible con las capacidades integradas del lenguaje HiveQL.
- Hive está destinado al procesamiento de lotes de grandes cantidades de datos inmutables (como blogs). No es adecuado para aplicaciones a transacciones que necesitan tiempos de respuesta rápidos, como los sistemas de administración de bases de datos.
- Hive se ha optimizado para la escalabilidad (se pueden añadir dinámicamente más máquinas al clúster de Hadoop), la extensibilidad (dentro del marco de MapReduce y con otras interfaces de programación) y la tolerancia a errores. La latencia no es una consideración clave del diseño.

##<a id="uploaddata"></a>Carga de datos en tablas de Hive

HDInsight utiliza un contenedor de almacenamiento de blobs de Azure como sistema de archivos predeterminado para clústeres de Hadoop. Se agregan algunos archivos de datos de ejemplo al almacenamiento de blobs como parte del aprovisionamiento de clústeres. En este artículo se usa un archivo de ejemplo *log4j* que se distribuye el clúster de HDInsight y se almacena en **/example/data/sample.log** en el contenedor de almacenamiento de blobs. Cada registro del archivo consta de una línea de campos que contiene uno llamado `[LOG LEVEL]` que muestra el tipo y la gravedad. Por ejemplo:

	2012-02-03 20:26:41 SampleClass3 [ERROR] verbose detail for id 1527353937

En el ejemplo anterior, el nivel de registro es ERROR.

> [AZURE.NOTE] También puede generar su propios archivos log4j con la utilidad de registro [Apache Log4j][apache-log4j] y luego cargarlos en el contenedor de blobs. Consulte [Carga de datos en HDInsight][hdinsight-upload-data] para obtener instrucciones. Para obtener más información sobre el uso del almacenamiento de blobs de Azure con HDInsight, consulte [Uso del almacenamiento de blobs de Azure con HDInsight][hdinsight-storage]..

HDInsight puede obtener acceso a archivos almacenados en el almacenamiento de blobs mediante el prefijo **wasb**. Por ejemplo, para tener acceso al archivo sample.log, use la siguiente sintaxis:

	wasb:///example/data/sample.log

Dado que WASB es el almacenamiento predeterminado para HDInsight, también puede obtener acceso al archivo mediante **/example/data/sample.log**.

> [AZURE.NOTE] La sintaxis anterior, **wasb:///**, se usa para obtener acceso a archivos almacenados en el contenedor de almacenamiento predeterminado para el clúster de HDInsight. Si especificó cuentas de almacenamiento adicionales cuando aprovisionó el clúster y desea obtener acceso a los archivos almacenados en esas cuentas, puede obtener acceso a los datos especificando el nombre de contenedor y la dirección de las cuentas de almacenamiento. Por ejemplo, **wasb://mycontainer@mystorage.blob.core.windows.net/example/data/sample.log**.

##<a id="runhivequeries"></a> Ejecución de las consultas de Hive usando PowerShell

Las consultas de Hive se pueden ejecutar en PowerShell usando el cmdlet **Start-AzureHDInsightJob** o el cmdlet **Invoke-Hive**.

* **Start-AzureHDInsightJob** es un ejecutor de trabajos genérico, usado para iniciar trabajos de Hive, Pig y MapReduce en un clúster de HDInsight. **Start-AzureHDInsightJob** es asincrónico y se devuelve antes de que el trabajo se complete. Se devuelve información acerca del trabajo que se puede usar con cmdlets, como por ejemplo **Wait-AzureHDInsightJob**, **Stop-AzureHDInsightJob** y **Get-AzureHDInsightJobOutput**.  **Get-AzureHDInsightJobOutput** se debe usar para recuperar información escrita en **STDOUT** o **STDERR** por el trabajo.

* **Invoke-Hive** ejecuta una consulta de Hive, espera en ella hasta completarse y recupera la salida de la consulta como una acción.

1. Abra una ventana de la consola de Azure PowerShell. Las instrucciones se encuentran en [Instalación y configuración de Azure PowerShell][powershell-install-configure].
2. Ejecute el comando siguiente para conectarse a su suscripción de Azure:

		Add-AzureAccount

	Se le pedirá que escriba las credenciales de la cuenta de Azure.

2. Configure variables del script siguiente y ejecútelo:

		# Provide Azure subscription name, and the Azure Storage account and container that is used for the default HDInsight file system.
		$subscriptionName = "<SubscriptionName>"
		$storageAccountName = "<AzureStorageAccountName>"
		$containerName = "<AzureStorageContainerName>"

		# Provide HDInsight cluster name Where you want to run the Hive job
		$clusterName = "<HDInsightClusterName>"

3. Ejecute el siguiente script para crear una nueva tabla llamada **log4jLogs** mediante los datos del ejemplo.

		# HiveQL
		# Create an EXTERNAL table
		$queryString = "DROP TABLE log4jLogs;" +
		               "CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' STORED AS TEXTFILE LOCATION 'wasb:///example/data/';" +
		               "SELECT t4 AS sev, COUNT(*) AS cnt FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4;"

	Las instrucciones de HiveQL realizan las acciones siguientes

	* **DROP TABLE** - Elimina la tabla y el archivo de datos si la tabla ya existe.
	* **CREATE EXTERNAL TABLE** - Crea una tabla 'externa' en Hive. Las tablas externas solamente almacenan la definición de tabla en Hive (los datos se dejan en la ubicación original).
	* **ROW FORMAT** - Indica a Hive cómo se da formato los datos. En este caso, los campos de cada registro se separan mediante un espacio.
	* **STORED AS TEXTFILE LOCATION** - Indica a Hive dónde se almacenan los datos (el directorio example/data) y que se almacenen como texto.
	* **SELECT** - Permite seleccionar un número de todas las filas donde la columna **t4** contiene el valor **[ERROR]**. Esto debe devolver un valor de **3** ya que hay tres filas que contienen este valor.

	> [AZURE.NOTE] Las tablas externas se deben usar cuando espera que un origen externo, como por ejemplo un proceso de carga de datos automático, u otra operación MapReduce, actualice los datos subyacentes, pero siempre desea que las consultas de Hive usen los datos más recientes.
	>
	> La eliminación de una tabla externa **no** elimina los datos, solamente la definición de tabla.


4. Ejecute el script siguiente para crear una definición de trabajo de Hive.

		# Create a Hive job definition
		$hiveJobDefinition = New-AzureHDInsightHiveJobDefinition -Query $queryString

	También usará el conmutador -File para especificar un archivo de script de HiveQL en HDFS.

5. Ejecute el script siguiente para enviar un trabajo de Hive:

		# Submit the job to the cluster
		Select-AzureSubscription $subscriptionName
		$hiveJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $hiveJobDefinition

6. Ejecute el script siguiente para esperar a que el trabajo de Hive termine:

		# Wait for the Hive job to complete
		Wait-AzureHDInsightJob -Job $hiveJob -WaitTimeoutInSeconds 3600

7. Ejecute el script siguiente para imprimir la salida estándar:

		# Print the standard error and the standard output of the Hive job.
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $hiveJob.JobId -StandardOutput


 	![HDI.HIVE.PowerShell][image-hdi-hive-powershell]

	El resultado es:

		[ERROR] 3

	lo que indica que habían tres instancias de registros de ERROR en el archivo *sample.log*.

4. Para usar **Invoke-Hive**, primero debe establecer el clúster que desea usar.

		# Connect to the cluster
		Use-AzureHDInsightCluster $clusterName

4. Use el siguiente script para crear una nueva tabla 'interna' llamada **errorLogs** usando el cmdlet **Invoke-Hive**.

		# Run a query to create an 'internal' Hive table
		$response = Invoke-Hive -Query @"
		CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
		INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]';
		"@
		# print the output on the console
		Write-Host $response

	Estas instrucciones realizan las acciones siguientes.

	* **CREATE TABLE IF NOT EXISTS** - Crea una tabla sea todavía no existe. Dado que la palabra clave **EXTERNAL** no se usa, se trata de una tabla 'interna', que se almacena en el almacén de datos de Hive y es administrada por Hive
	* **STORED AS ORC** - Almacena los datos en el formato Optimized Row Columnar (ORC). Se trata de un formato altamente optimizado y eficiente para almacenar datos de Hive
	* **INSERT OVERWRITE ... SELECT** - Selecciona filas de la tabla **log4jLogs** que contienen **[ERROR]** y, a continuación, se insertan los datos en la tabla **errorLogs**.

	> [AZURE.NOTE] A diferencia de las tablas **EXTERNAL**, la eliminación de una tabla interna también eliminará los datos subyacentes.

	El resultado tendrá un aspecto similar al siguiente.

	![PowerShell Invoke-Hive output][img-hdi-hive-powershell-output]

	> [AZURE.NOTE] Para consultas de HiveQL más extensas, puede usar PowerShell Here-Strings o archivos de script de HiveQL. El siguiente fragmento indica cómo usar el cmdlet *Invoke-Hive* para ejecutar un archivo de script de HiveQL. El archivo de script de HiveQL debe cargarse en WASB.
	>
	> `Invoke-Hive -File "wasb://<ContainerName>@<StorageAccountName>/<Path>/query.hql"`
	>
	> Para obtener más información acerca de Here-Strings, consulte [Uso de Windows PowerShell Here-Strings][powershell-here-strings].

5. Para comprobar que solamente las filas que contienen **[ERROR]** en la columna t4 se almacenaron en la tabla **errorLogs**, use la siguiente instrucción para devolver todas las filas de **errorLogs**.

		#Select all rows
		$response = Invoke-Hive -Query "SELECT * from errorLogs;"
		Write-Host $response

	Se deben devolver tres filas de datos y todas ellas deben contener **[ERROR]** en la columna t4.


> [AZURE.NOTE] Si es necesario, también puede importar el resultado de las consultas a Microsoft Excel para un análisis adicional. Para obtener instrucciones, consulte [Conexión de Excel a Hadoop con Power Query][import-to-excel]..

##<a id="runhivefromvs"></a>Ejecución de las consultas de Hive usando Visual Studio
HDInsight Tools for Visual Studio incluye el SDK de Azure para la versión 2.5, o posterior, de .NET.  Mediante las herramientas de Visual Studio puede conectarse al clúster de HDInsight, crear tablas de Hive y ejecutar consultas de Hive.  Para obtener más información, consulte [Introducción al uso de HDInsight Hadoop Tools for Visual Studio][1].



##<a id="usetez"></a>Uso de Tez para un mejor rendimiento

[Apache Tez][apache-tez] es un marco que permite que aplicaciones con uso intensivo de datos como Hive se ejecuten con mucha más eficacia a escala. En la última versión de HDInsight, Hive ya admite la ejecución en Tez.  Esta opción está actualmente desactivada de forma predeterminada y debe habilitarse.  En versiones de clúster futuras, estará activada de manera predeterminada. Con objeto de aprovechar Tez, debe establecerse el siguiente valor en una consulta de Hive:

		set hive.execution.engine=tez;

Este valor puede enviarse por consulta insertándolo al comienzo de la consulta.  También se puede establecer para que se active de manera predeterminada en un clúster estableciendo el valor de configuración en el momento de la creación del clúster.  Puede encontrar más detalles en [Aprovisionamiento de clústeres de HDInsight][hdinsight-provision].

Los [documentos de diseño de Hive en Tez][hive-on-tez-wiki] incluyen varios detalles sobre las opciones de implementación y las configuraciones de ajuste.


##<a id="nextsteps"></a>Pasos siguientes

Aunque Hive facilita la realización de las consultas de datos usando un lenguaje de consultas de tipo SQL, otros componentes disponibles con HDInsight proporcionan funciones complementarias, como el movimiento y la transformación de los datos. Para obtener más información, consulte los artículos siguientes:

* [Introducción al uso de HDInsight Hadoop Tools for Visual Studio][1]
* [Uso de Oozie con HDInsight][hdinsight-use-oozie]
* [Envío de trabajos de Hadoop mediante programación][hdinsight-submit-jobs]
* [Uso de Pig con HDInsight](../hdinsight-use-pig/)
* [Análisis de la información de retraso de vuelos usando HDInsight][hdinsight-analyze-flight-data]
* [Documentación del SDK de Azure HDInsight][hdinsight-sdk-documentation]
* [Carga de datos en HDInsight][hdinsight-upload-data]
* [Introducción a Azure HDInsight](../hdinsight-get-started/)


[1]: ../hdinsight-hadoop-visual-studio-tools-get-started/

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/en-us/library/dn479185.aspx

[azure-purchase-options]: http://azure.microsoft.com/en-us/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/en-us/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/en-us/pricing/free-trial/

[apache-tez]: http://tez.apache.org
[apache-hive]: http://hive.apache.org/
[apache-log4j]: http://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: http://azure.microsoft.com/en-us/documentation/articles/hdinsight-connect-excel-power-query/


[hdinsight-use-oozie]: ../hdinsight-use-oozie/
[hdinsight-analyze-flight-data]: ../hdinsight-analyze-flight-delay-data/



[hdinsight-storage]: ../hdinsight-use-blob-storage

[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-submit-jobs]: ../hdinsight-submit-hadoop-jobs-programmatically/
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-get-started]: ../hdinsight-get-started/

[Powershell-install-configure]: ../install-configure-powershell/
[powershell-here-strings]: http://technet.microsoft.com/en-us/library/ee692792.aspx

[image-hdi-hive-powershell]: ./media/hdinsight-use-hive/HDI.HIVE.PowerShell.png
[img-hdi-hive-powershell-output]: ./media/hdinsight-use-hive/HDI.Hive.PowerShell.Output.png
[image-hdi-hive-architecture]: ./media/hdinsight-use-hive/HDI.Hive.Architecture.png

<!--HONumber=35_1-->
