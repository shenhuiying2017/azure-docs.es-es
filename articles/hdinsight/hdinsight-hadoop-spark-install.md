<properties 
	pageTitle="Uso de la acción de script para instalar Spark en el clúster de Hadoop| Microsoft Azure" 
	description="Obtenga información para personalizar un clúster de HDInsight con Spark. Va a usar una opción de configuración de la acción de script para usar un script para instalar Spark" 
	services="hdinsight" 
	documentationCenter="" 
	authors="nitinme" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/11/2015" 
	ms.author="nitinme"/>

# Instalación y uso de Spark en clústeres Hadoop de HDInsight

Puede instalar Spark en cualquier tipo de clúster de Hadoop en HDInsight de Azure mediante la personalización de clústeres de **acción de script**. La acción de script le permite ejecutar scripts para personalizar un clúster, conforme se crea el clúster. Para obtener más información, consulte [Personalización de un clúster de HDInsight mediante la acción de script][hdinsight-cluster-customize].

En este tema aprenderá a instalar Spark con la acción de script. Una vez haya instalado Spark, aprenderá cómo ejecutar una consulta de Spark en clústeres de HDInsight.

> [AZURE.NOTE]HDInsight proporciona Spark como tipo de clúster de primera clase, lo que significa que es posible aprovisionar directamente un clúster Spark sin modificar un clúster de Hadoop. Con el tipo de clúster Spark, se obtiene un clúster de HDInsight versión 3.2 con Spark versión 1.3.1. Para obtener más información, consulte [Empezar a trabajar con Apache Spark en HDInsight](hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql.md).


## <a name="whatis"></a>¿Qué es Spark?

<a href="http://spark.apache.org/docs/latest/index.html" target="_blank">Apache Spark</a> es un marco de procesamiento paralelo de código abierto que admite el procesamiento en memoria para mejorar el rendimiento de las aplicaciones analíticas de Big Data. Las capacidades de cálculo en memoria de Spark lo convierten en una buena opción para algoritmos iterativos en los cálculos de gráficos y aprendizaje automático.

Spark también se puede usar para llevar a cabo el procesamiento de datos convencional basado en disco. Spark mejora el marco de MapReduce tradicional evitando escrituras en disco en las etapas intermedias. Además, Spark es compatible con el Sistema de archivos distribuido de Hadoop (HDFS) y el almacenamiento de blobs de Azure, por lo que se pueden procesar los datos existentes fácilmente a través de Spark.

Este tema proporciona instrucciones sobre cómo personalizar un clúster de HDInsight para instalar Spark.

## <a name="whatis"></a>¿Qué versión de Spark puedo instalar?

En este tema, se usa un script personalizado de acción de script para instalar Spark en un clúster de HDInsight. Este script puede instalar Spark 1.2.0 o Spark 1.0.2 según la versión del clúster de HDInsight que aprovisione.

- Si utiliza el script al aprovisionar un clúster de **HDInsight 3.2**, instala **Spark 1.2.0**.
- Si utiliza el script al aprovisionar un clúster de **HDInsight 3.1**, instala **Spark 1.0.2**. 

Puede modificar este script o crear su propio script para instalar otras versiones de Spark.


## <a name="install"></a>¿Cómo instalo Spark?

Hay un script de ejemplo para instalar Spark en un clúster de HDInsight disponible desde un blob de almacenamiento de Azure de solo lectura en [https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1](https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1). Esta sección proporciona instrucciones sobre cómo utilizar el script de ejemplo durante el aprovisionamiento del clúster mediante el Portal de Azure.

> [AZURE.NOTE]El script de ejemplo solo funciona con clústeres de HDInsight 3.1 y 3.2. Para obtener más información acerca de las versiones de clústeres de HDInsight, consulte las [versiones de clústeres de HDInsight](hdinsight-component-versioning.md).

1. Inicie el aprovisionamiento de un clúster con la opción **CREACIÓN PERSONALIZADA**, tal como se describe en [Aprovisionamiento de clústeres usando opciones personalizadas](hdinsight-provision-clusters.md#portal). Elija la versión de clúster según lo siguiente:

	- Si desea instalar **Spark 1.2.0**, aprovisione un clúster de HDInsight 3.2.
	- Si desea instalar **Spark 1.0.2**, aprovisione un clúster de HDInsight 3.1.


2. En la página **Acciones de script** del asistente, haga clic en **Agregar acción de script** para proporcionar detalles acerca de la acción de script, tal como se muestra a continuación:

	![Uso de la acción de script para personalizar un clúster](./media/hdinsight-hadoop-spark-install/HDI.CustomProvision.Page6.png "Uso de la acción de script para personalizar un clúster")
	
	<table border='1'>
	<tr><th>Propiedad</th><th>Valor</th></tr>
	<tr><td>Nombre</td>
		<td>Especifique un nombre para la acción de script. Por ejemplo, <b>Instalar Spark</b>.</td></tr>
	<tr><td>URI de script</td>
		<td>Especifique el Identificador uniforme de recursos (URI) al script que se ha invocado para personalizar el clúster. Por ejemplo, <i>https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1</i></td></tr>
	<tr><td>Tipo de nodo</td>
		<td>Especifique los nodos en los que se ejecuta el script de personalización. Puede elegir <b>Todos los nodos</b>, <b>Solo nodos principales</b> o <b>Solo nodos de trabajo</b>.
	<tr><td>Parámetros</td>
		<td>Especifique los parámetros, si lo requiere el script. El script para instalar Spark no requiere ningún parámetro, por lo que puede dejarlo en blanco.</td></tr>
</table>Puede agregar más de una acción de script para instalar varios componentes en el clúster. Después de haber agregado los scripts, haga clic en la marca de verificación para iniciar el aprovisionamiento del clúster.

También puede utilizar el script para instalar Spark en HDInsight con PowerShell de Azure o el SDK de .NET de HDInsight. Más adelante en este tema se proporcionan instrucciones para estos procedimientos.

## <a name="usespark"></a>¿Cómo uso Spark en HDInsight?
Spark proporciona las API en Scala, Python y Java. También puede usar el shell de Spark interactivo para ejecutar consultas de Spark. Esta sección proporciona instrucciones sobre cómo usar los diferentes enfoques para trabajar con Spark:

- [Uso del shell de Spark para ejecutar consultas interactivas](#sparkshell)
- [Uso del shell de Spark para ejecutar consultas de Spark SQL](#sparksql) 
- [Uso de un programa de Scala independiente](#standalone)

###<a name="sparkshell"></a>Uso del shell de Spark para ejecutar consultas interactivas
Realice los pasos siguientes para ejecutar consultas de Spark desde un shell de Spark interactivo. En esta sección, se ejecutará una consulta de Spark en un archivo de datos de ejemplo (/example/data/gutenberg/davinci.txt) que está disponible, de manera predeterminada, en los clústeres de HDInsight.

1. En el portal de Azure, habilite el Escritorio remoto para el clúster que ha creado con Spark instalado y, a continuación, remoto en el clúster. Para obtener instrucciones, vea <a href="http://azure.microsoft.com/documentation/articles/hdinsight-administer-use-management-portal/#rdp" target="_blank">Conexión a los clústeres de HDInsight con RDP</a>.

2. En la sesión del Protocolo de escritorio remoto (RDP), desde el escritorio, abra la línea de comandos de Hadoop (desde un acceso directo del escritorio) y navegue hasta la ubicación donde está instalado Spark; por ejemplo, **C:\apps\dist\spark-1.2.0**.


3. Ejecute el siguiente comando para iniciar el shell de Spark:

		 .\bin\spark-shell --master yarn

	Cuando finalice la ejecución del comando, debe obtener un símbolo de sistema de Scala:

		 scala>

5. En el símbolo de sistema de Scala, escriba la consulta de Spark que se muestra a continuación. Esta consulta cuenta la aparición de cada palabra en el archivo davinci.txt que está disponible en la ubicación /example/data/gutenberg/ en el almacenamiento de blobs de Azure asociado al clúster.

		val file = sc.textFile("/example/data/gutenberg/davinci.txt")
		val counts = file.flatMap(line => line.split(" ")).map(word => (word, 1)).reduceByKey(_ + _)
		counts.toArray().foreach(println)

6. La salida debe ser similar a la siguiente:

	![Resultado de la ejecución del shell interactivo de Scala en un clúster de HDInsight](./media/hdinsight-hadoop-spark-install/hdi-scala-interactive.png)
		

7. Escriba: q para salir del símbolo del sistema de Scala.

		:q

###<a name="sparksql"></a>Uso del shell de Spark para ejecutar consultas de Spark SQL

Spark SQL le permite usar Spark para ejecutar consultas relacionales expresadas en Lenguaje de consulta estructurado (SQL), HiveQL o Scala. En esta sección, veremos el uso de Spark para ejecutar una consulta de Hive en una tabla de Hive de ejemplo. La tabla de Hive usada en esta sección (llamada **hivesampletable**) está disponible de forma predeterminada cuando se aprovisiona un clúster.

>[AZURE.NOTE]El ejemplo siguiente se creó con **Spark 1.2.0**, que se instala si ejecuta la acción de script durante el aprovisionamiento del clúster de HDInsight 3.2.

1. En el portal de Azure, habilite el Escritorio remoto para el clúster que ha creado con Spark instalado y, a continuación, remoto en el clúster. Para obtener instrucciones, vea <a href="http://azure.microsoft.com/documentation/articles/hdinsight-administer-use-management-portal/#rdp" target="_blank">Conexión a los clústeres de HDInsight con RDP</a>.

2. En la sesión de RDP, desde el escritorio, abra la línea de comandos de Hadoop (desde un acceso directo del escritorio) y navegue hasta la ubicación donde está instalado Spark; por ejemplo, **C:\apps\dist\spark-1.2.0**.


3. Ejecute el siguiente comando para iniciar el shell de Spark:

		 .\bin\spark-shell --master yarn

	Cuando finalice la ejecución del comando, debe obtener un símbolo de sistema de Scala:

		 scala>

4. En el símbolo del sistema de Scala, establezca el contexto de Hive. Esto es necesario para trabajar con consultas de Hive mediante Spark.

		val hiveContext = new org.apache.spark.sql.hive.HiveContext(sc)

	Tenga en cuenta que **sc** es contexto de Spark predeterminado que se establece cuando se inicia el shell de Spark.

5. Ejecute una consulta de Hive mediante el contexto de Hive e imprima la salida en la consola. La consulta recupera los datos en dispositivos de una marca y limita el número de registros recuperados a 20.

		hiveContext.sql("""SELECT * FROM hivesampletable WHERE devicemake LIKE "HTC%" LIMIT 20""").collect().foreach(println)

6. Debe ver algo parecido a lo siguiente:

	![Resultado de la ejecución de Spark SQL en un clúster de HDInsight](./media/hdinsight-hadoop-spark-install/hdi-spark-sql.png)

7. Escriba: q para salir del símbolo del sistema de Scala.

		:q

### <a name="standalone"></a>Uso de un programa de Scala independiente

En esta sección se escribe una aplicación de Scala que cuenta el número de líneas que contienen las letras 'a' y 'b' en el archivo de datos de ejemplo (/example/data/gutenberg/davinci.txt) que está disponible, de manera predeterminada en los clústeres de HDInsight. Para escribir y usar un programa de Scala independiente con un clúster personalizado con la instalación de Spark, debe realizar los pasos siguientes:

- Escribir un programa de Scala
- Crear el programa de Scala para obtener el archivo .jar
- Ejecutar el trabajo en el clúster

#### Escribir un programa de Scala
En esta sección, escribe una aplicación de Scala que cuenta el número de líneas que contienen 'a' y 'b' en el archivo de datos de ejemplo.

1. Abra un editor de texto y pegue el código siguiente:


		/* SimpleApp.scala */
		import org.apache.spark.SparkContext
		import org.apache.spark.SparkContext._
		import org.apache.spark.SparkConf
		
		object SimpleApp {
		  def main(args: Array[String]) {
		    val logFile = "/example/data/gutenberg/davinci.txt"			//Location of the sample data file on Azure Blob storage
		    val conf = new SparkConf().setAppName("SimpleApplication")
		    val sc = new SparkContext(conf)
		    val logData = sc.textFile(logFile, 2).cache()
		    val numAs = logData.filter(line => line.contains("a")).count()
		    val numBs = logData.filter(line => line.contains("b")).count()
		    println("Lines with a: %s, Lines with b: %s".format(numAs, numBs))
		  }
		}

2. Guarde el archivo con el nombre **SimpleApp.scala**.

#### Compilar el programa de Scala
En esta sección, debe usar la herramienta <a href="http://www.scala-sbt.org/0.13/docs/index.html" target="_blank">Simple Build Tool</a> (o sbt) para compilar el programa de Scala .sbt requiere Java 1.6 o posterior; por tanto, asegúrese de que tiene la versión correcta de Java instalada antes de continuar con esta sección.

1. Instale sbt desde http://www.scala-sbt.org/0.13/tutorial/Installing-sbt-on-Windows.html.
2. Cree una carpeta llamada **SimpleScalaApp** y, dentro de esta carpeta, cree un archivo denominado **simple.sbt**. Se trata de un archivo de configuración que contiene información sobre la versión de Scala, dependencias de biblioteca, etc. Pegue lo siguiente en el archivo simple.sbt y guárdelo:


		name := "SimpleApp"
	
		version := "1.0"
	
		scalaVersion := "2.10.4"
	
		libraryDependencies += "org.apache.spark" %% "spark-core" % "1.2.0"



	>[AZURE.NOTE]Asegúrese de que conserva las líneas vacías en el archivo.

	
3. En la carpeta **SimpleScalaApp**, cree una estructura de directorios **\src\main\scala** y pegue el programa de Scala (**SimpleApp.scala**) que creó anteriormente en la carpeta \src\main\scala.
4. Abra un símbolo del sistema, navegue hasta el directorio SimpleScalaApp y escriba el siguiente comando:


		sbt package


	Una vez se compile la aplicación, verá un archivo **simpleapp_2.10 1.0.jar** creado en el directorio **\target\scala-2.10** dentro de la carpeta raíz de SimpleScalaApp.


#### Ejecutar el trabajo en el clúster
En esta sección, trabajará en remoto en el clúster que tiene Spark instalado y, a continuación, copiará la carpeta de destino del proyecto de SimpleScalaApp. A continuación, utilizará el comando **spark-submit** para enviar el trabajo del clúster.

1. Trabaje en remoto en el clúster que tiene Spark instalado. En el equipo donde escribió y creó el programa SimpleApp.scala, copie la carpeta **SimpleScalaApp\target** y péguela en una ubicación del clúster.
2. En la sesión RDP, en el escritorio, abra la línea de comandos de Hadoop y desplácese hasta la ubicación en la que ha pegado la carpeta de **destino**.
3. Escriba el siguiente comando para ejecutar el programa de SimpleApp.scala:


		C:\apps\dist\spark-1.2.0\bin\spark-submit --class "SimpleApp" --master local target/scala-2.10/simpleapp_2.10-1.0.jar

4. Cuando el programa termine de ejecutarse, el resultado aparecerá en la consola.


		Lines with a: 21374, Lines with b: 11430

## <a name="usingPS"></a>Instalar Spark en clústeres de Hadoop de HDInsight mediante Azure PowerShell

En esta sección, usamos el cmdlet **<a href = "http://msdn.microsoft.com/library/dn858088.aspx" target="_blank">Add-AzureHDInsightScriptAction</a>** para invocar scripts usando la acción de script para personalizar un clúster. Antes de continuar, asegúrese de que ha instalado y configurado PowerShell de Azure. Para obtener información acerca de la configuración de una estación de trabajo para que ejecute cmdlets de Azure PowerShell para HDInsight, consulte [Instalación y configuración de Azure PowerShell][powershell-install-configure].

Lleve a cabo los siguiente pasos:

1. Abra una ventana de PowerShell de Azure y declare las siguientes variables:

		# Provide values for these variables
		$subscriptionName = "<SubscriptionName>"		# Name of the Azure subscription
		$clusterName = "<HDInsightClusterName>"			# HDInsight cluster name
		$storageAccountName = "<StorageAccountName>"	# Azure Storage account that hosts the default container
		$storageAccountKey = "<StorageAccountKey>"      # Key for the Storage account
		$containerName = $clusterName
		$location = "<MicrosoftDataCenter>"				# Location of the HDInsight cluster. It must be in the same data center as the Storage account.
		$clusterNodes = <ClusterSizeInNumbers>			# Number of nodes in the HDInsight cluster
		$version = "<HDInsightClusterVersion>"          # For example, "3.2"
	
2. Especifique los valores de configuración como nodos del clúster y el almacenamiento predeterminado que se va a usar.

		# Specify the configuration options
		Select-AzureSubscription $subscriptionName
		$config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes $clusterNodes
		$config.DefaultStorageAccount.StorageAccountName="$storageAccountName.blob.core.windows.net"
		$config.DefaultStorageAccount.StorageAccountKey=$storageAccountKey
		$config.DefaultStorageAccount.StorageContainerName=$containerName
	
3. Utilice el cmdlet **Add-AzureHDInsightScriptAction** para agregar una acción de script a la configuración de clúster. Más adelante, cuando se cree el clúster, se ejecutará la acción de script.

		# Add a script action to the cluster configuration
		$config = Add-AzureHDInsightScriptAction -Config $config -Name "Install Spark" -ClusterRoleCollection HeadNode -Uri https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1

	El cmdlet **Add-AzureHDInsightScriptAction** toma los siguientes parámetros:

	<table style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse;">
<tr>
<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">Parámetro</th>
<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:550px; padding-left:5px; padding-right:5px;">Definición</th></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Configuración</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px; padding-right:5px;">El objeto de configuración al que se agrega la información de la acción del script</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Nombre</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Nombre de la acción de script.</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">ClusterRoleCollection</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Especifica los nodos en los que se ejecuta el script de personalización. Los valores válidos son HeadNode (para instalar en el nodo principal) o DataNode (para instalar en todos los nodos de datos). Puede usar uno de los valores o ambos.</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">URI</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Especifica el URI para el script que se ejecuta.</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Parámetros</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Parámetros requeridos por el script. El script de ejemplo que se usa en este tema no requiere ningún parámetro y, por tanto, no ve este parámetro en el fragmento de código anterior.
</td></tr>
</table>
	
4. Por último, inicie el aprovisionamiento de un clúster personalizado con Spark instalado.
	
		# Start provisioning a cluster with Spark installed
		New-AzureHDInsightCluster -Config $config -Name $clusterName -Location $location -Version $version 

Cuando el sistema lo pida, escriba las credenciales para el clúster. El clúster puede tardar varios minutos en crearse.


## <a name="usingSDK"></a>Instalar Spark en clústeres de Hadoop de HDInsight mediante el SDK de .NET

El SDK .NET de HDInsight proporciona bibliotecas de cliente .NET que facilitan el trabajo con HDInsight desde una aplicación .NET Framework. En esta sección se proporcionan instrucciones sobre cómo utilizar la acción de script del SDK para aprovisionar un clúster que tiene Spark instalado. Se deben llevar a cabo los siguiente procedimientos:

- Instalación del SDK de .NET de HDInsight
- Creación de un certificado autofirmado
- Creación de una aplicación de consola
- Ejecución de la aplicación


**Para instalar el SDK de .NET de HDInsight**

Puede instalar la compilación publicada más reciente del SDK desde [NuGet](http://nuget.codeplex.com/wikipage?title=Getting%20Started). Las instrucciones se mostrarán en el siguiente procedimiento.

**Para crear un certificado autofirmado**

Cree un certificado autofirmado, instálelo en su estación de trabajo y cárguelo en su suscripción de Azure. Para obtener instrucciones, consulte [Creación de un certificado autofirmado](http://go.microsoft.com/fwlink/?LinkId=511138).


**Para crear una aplicación de Visual Studio**

1. Abra Visual Studio 2013.

2. En el menú **Archivo**, haga clic en **Nuevo** y, a continuación, en **Proyecto**.

3. En **Nuevo proyecto**, escriba o seleccione los siguientes valores:
	
	<table style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse;">
<tr>
<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">Propiedad</th>
<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">Valor</th></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Categoría</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px; padding-right:5px;">Plantillas/Visual C#/Windows</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Plantilla</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Aplicación de consola</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Nombre</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">CreateSparkCluster</td></tr>
</table>

4. Haga clic en **Aceptar** para crear el proyecto.

5. En el menú **Herramientas**, haga clic en **Administrador de paquetes NuGet** y, a continuación, en la **Consola del administrador de paquetes**.

6. Ejecute el siguiente comando en la consola para instalar el paquete:

		Install-Package Microsoft.WindowsAzure.Management.HDInsight

	Este comando agrega las bibliotecas y referencias .NET a los paquetes desde el proyecto de Visual Studio actual.

7. En el Explorador de soluciones, haga doble clic en **Program.cs** para abrirlo.

8. Agregue la siguiente instrucción en la parte superior del archivo:

		using System.Security.Cryptography.X509Certificates;
		using Microsoft.WindowsAzure.Management.HDInsight;
		using Microsoft.WindowsAzure.Management.HDInsight.ClusterProvisioning;
		using Microsoft.WindowsAzure.Management.HDInsight.Framework.Logging;
	
9. En la función Main(), copie y pegue el siguiente código, y proporcione valores para las variables:
		
        var clusterName = args[0];

        // Provide values for the variables
        string thumbprint = "<CertificateThumbprint>";  
        string subscriptionId = "<AzureSubscriptionID>";
        string location = "<MicrosoftDataCenterLocation>";
        string storageaccountname = "<AzureStorageAccountName>.blob.core.windows.net";
        string storageaccountkey = "<AzureStorageAccountKey>";
        string username = "<HDInsightUsername>";
        string password = "<HDInsightUserPassword>";
        int clustersize = <NumberOfNodesInTheCluster>;

        // Provide the certificate thumbprint to retrieve the certificate from the certificate store 
        X509Store store = new X509Store();
        store.Open(OpenFlags.ReadOnly);
        X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.Thumbprint == thumbprint);

        // Create an HDInsight client object
        HDInsightCertificateCredential creds = new HDInsightCertificateCredential(new Guid(subscriptionId), cert);
        var client = HDInsightClient.Connect(creds);
		client.IgnoreSslErrors = true;
        
        // Provide the cluster information
		var clusterInfo = new ClusterCreateParameters()
        {
            Name = clusterName,
            Location = location,
            DefaultStorageAccountName = storageaccountname,
            DefaultStorageAccountKey = storageaccountkey,
            DefaultStorageContainer = clusterName,
            UserName = username,
            Password = password,
            ClusterSizeInNodes = clustersize,
            Version = "3.2"
        };        

10. Anexe el código siguiente a la función Main() para que use la clase [ScriptAction](http://msdn.microsoft.com/library/microsoft.windowsazure.management.hdinsight.clusterprovisioning.data.scriptaction.aspx) para invocar un script personalizado para instalar Spark.

		// Add the script action to install Spark
        clusterInfo.ConfigActions.Add(new ScriptAction(
          "Install Spark", // Name of the config action
          new ClusterNodeType[] { ClusterNodeType.HeadNode }, // List of nodes to install Spark on
          new Uri("https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1"), // Location of the script to install Spark.
		  null //Because the script used does not require any parameters
        ));

11. Por último, cree el clúster.

		client.CreateCluster(clusterInfo);

11. Guarde los cambios en la aplicación y genere la solución.

**Para ejecutar la aplicación**

Abra una consola de PowerShell de Azure, navegue hasta la ubicación donde ha guardado el proyecto de Visual Studio, luego hasta el directorio \bin\debug dentro del proyecto y, a continuación, ejecute el siguiente comando:

	.\CreateSparkCluster <cluster-name>

Proporcione un nombre de clúster y presione ENTRAR para aprovisionar un clúster con Spark instalado.


## Consulte también##
- [Instalación de R en clústeres de HDInsight][hdinsight-install-r] proporciona instrucciones acerca de cómo usar la personalización del clúster para instalar y usar R en clústeres de Hadoop de HDInsight. R es un entorno y lenguaje de código abierto para computación estadística. Proporciona cientos de de funciones estadísticas integradas y su propio lenguaje de programación que combina aspectos de la programación funcional y orientada a objetos. También proporciona amplias capacidades gráficas.
- [Instalación de Giraph en clústeres de HDInsight](hdinsight-hadoop-giraph-install.md). Use la personalización del clúster para instalar Giraph en clústeres de Hadoop para HDInsight. Giraph permite realizar un procesamiento gráfico mediante Hadoop, y se puede usar con HDInsight de Azure.
- [Instalación de Solr en clústeres de HDInsight](hdinsight-hadoop-solr-install.md). Use la personalización del clúster para instalar Solr en clústeres de Hadoop para HDInsight. Solr le permite realizar potentes operaciones de búsqueda en los datos almacenados.





[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-install-r]: hdinsight-hadoop-r-scripts.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster.md
[powershell-install-configure]: ../install-configure-powershell.md
 

<!---HONumber=July15_HO4-->