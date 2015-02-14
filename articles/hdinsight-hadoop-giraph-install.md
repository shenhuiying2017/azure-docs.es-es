<properties 
	pageTitle="Usar la acción de script en HDInsight para instalar Giraph en el clúster de Hadoop| Azure" 
	description="Obtenga información acerca de cómo personalizar el clúster de HDInsight para instalar Giraph. Va a utilizar una opción de configuración de la acción de script para usar un script para instalar Giraph" 
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
	ms.date="12/19/2014" 
	ms.author="nitinme"/>

# Instalación y uso de Giraph en clústeres de Hadoop de HDInsight

Puede instalar Giraph en cualquier tipo de clúster de Hadoop en HDInsight mediante la personalización de clústeres de **acción de script**. La acción de script le permite ejecutar scripts para personalizar un clúster, conforme se crea el clúster. Para obtener más información, consulte [Personalización de un clúster de HDInsight mediante la acción de script][hdinsight-cluster-customize].

En este tema aprenderá a instalar Giraph con la acción de script. Una vez haya instalado Giraph, aprenderá cómo utilizar Giraph en las aplicaciones más habituales, como procesar gráficos de gran escala.


## En este artículo

- [¿Qué es Giraph?](#whatis)
- [¿Cómo instalo Giraph?](#install)
- [¿Cómo uso Giraph en HDInsight?](#usegiraph)
- [Instalar Giraph en clústeres de Hadoop de HDInsight mediante PowerShell](#usingPS)
- [Instalar Giraph en clústeres de Hadoop de HDInsight mediante el SDK de .NET](#usingSDK) 


## <a name="whatis"></a>¿Qué es Giraph?

<a href="http://giraph.apache.org/" target="_blank">Apache Giraph</a> permite realizar un procesamiento gráfico mediante Hadoop, y se puede usar con Azure HDInsight. Los gráficos modelan las relaciones entre los objetos, como las conexiones entre los enrutadores en una red de gran tamaño como Internet, o las relaciones entre personas de las redes sociales (conocidas en ocasiones como gráficos sociales). El procesamiento gráfico le permite razonar sobre las relaciones entre los objetos de un gráfico. En este sentido, le ayuda por ejemplo a:

- Identificar los posibles amigos según las relaciones actuales
- Identificar la ruta más corta entre dos equipos de una red
- Calcular la posición de las páginas web.

   
## <a name="install"></a>¿Cómo instalo Giraph?

Hay un script de ejemplo para instalar Giraph en un clúster de HDInsight disponible desde un blob de almacenamiento de Azure de solo lectura en [https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1](https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1). Esta sección proporciona instrucciones sobre cómo utilizar el script de ejemplo durante el aprovisionamiento del clúster mediante el Portal de administración de Azure. 

> [AZURE.NOTE] El script de ejemplo solo funciona con el clúster de HDInsight versión 3.1.  Para obtener más información acerca de las versiones de clústeres de HDInsight, consulte las[versiones de clústeres de HDInsight](http://azure.microsoft.com/es-es/documentation/articles/hdinsight-component-versioning/).

1. Inicie el aprovisionamiento de un clúster con la opción **CREACIÓN PERSONALIZADA**, tal como se describe en [Aprovisionamiento de clústeres mediante opciones personalizadas](http://azure.microsoft.com/es-es/documentation/articles/hdinsight-provision-clusters/#portal). 
2. En la página **Acciones de script** del asistente, haga clic en **Agregar acción de script** para proporcionar detalles acerca de la acción de script, tal como se muestra a continuación:

	![Use Script Action to customize a cluster](./media/hdinsight-hadoop-giraph-install/hdi-script-action-giraph.png "Use Script Action to customize a cluster")
	
	<table border='1'>
		<tr><th>Propiedad</th><th>Valor</th></tr>
		<tr><td>Nombre</td>
			<td>Especifique un nombre para la acción de script. Por ejemplo, <b>Instalar Giraph</b>.</td></tr>
		<tr><td>URI de script</td>
			<td>Especifique el URI al script que se ha invocado para personalizar el clúster. Por ejemplo: <i>https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1</i></td></tr>
		<tr><td>Tipo de nodo</td>
			<td>Especifica los nodos en los que se ejecuta el script de personalización. Puede elegir <b>Todos los nodos</b>, <b>Solo nodos principales</b>, o <b>Solo nodos de trabajo</b>.
		<tr><td>Parámetros</td>
			<td>Especifique los parámetros, si lo requiere el script. El script para instalar Giraph no requiere ningún parámetro, por lo que puede dejarlo en blanco.</td></tr>
	</table>	

	Puede agregar más de una acción de script para instalar varios componentes en el clúster. Después de haber agregado los scripts, haga clic en la marca de verificación para iniciar el aprovisionamiento del clúster.

También puede utilizar el script para instalar Giraph en HDInsight con PowerShell o el SDK de .NET de HDInsight. Más adelante en este tema se proporcionan instrucciones para estos procedimientos.

## <a name="usegiraph"></a>¿Cómo uso Giraph en HDInsight?

Usamos el SimpleShortestPathsComputation para mostrar la implementación básica de  <a href = "http://people.apache.org/~edwardyoon/documents/pregel.pdf">Pregel</a> para encontrar la ruta más corta entre objetos en un gráfico. Use los siguientes pasos para cargar los datos de ejemplo y el jar de muestra, ejecutar un trabajo mediante el ejemplo SimpleShortestPathsComputation y luego ver los resultados.

1. **Cargar un archivo de datos de ejemplo en WASB**. En la estación de trabajo local, cree un nuevo archivo denominado **tiny_graph.txt**. Debe contener las siguientes líneas.

		[0,0,[[1,1],[3,3]]]
		[1,0,[[0,1],[2,2],[3,1]]]
		[2,0,[[1,2],[4,4]]]
		[3,0,[[0,3],[1,1],[4,4]]]
		[4,0,[[3,4],[2,4]]]

	Cargue el archivo tiny_graph.txt en el almacenamiento principal del clúster de HDInsight. Para obtener instrucciones sobre cómo cargar datos, consulte [Carga de datos para trabajos de Hadoop en HDInsight](../hdinsight-upload-data/)

	Estos datos describen una relación entre los objetos de un gráfico dirigido, con el formato [source\_id, source\_value,[[dest\_id], [edge\_value],...]]. Cada línea representa una relación entre un **source\_id** y uno o varios objetos **dest\_id**. El **edge\_value** (o peso) se puede considerar como la fuerza o la distancia de la conexión entre **source_id** y **dest\_id**.

	Extrapolados, y usando el valor (o peso) como la distancia entre los objetos, los datos anteriores podrían parecerse a estos.

	![tiny_graph.txt drawn as circles with lines of varying distance between](.\media\hdinsight-hadoop-giraph-install\giraph-graph.png)

	

4. Ejecute el ejemplo **SimpleShortstPathsComputation**. Utilice los cmdlets de PowerShell siguientes para ejecutar el ejemplo mediante el archivo tiny_graph.txt como entrada. Para ello, será necesario que haya instalado y configurado [Azure PowerShell][powershell-install-configure].

		$clusterName = "clustername"
		# Giraph examples JAR
		$jarFile = "wasb:///example/jars/giraph-examples.jar"
		# Arguments for this job
		$jobArguments = "org.apache.giraph.examples.SimpleShortestPathsComputation",
		                "-ca", "mapred.job.tracker=headnodehost:9010",
		                "-vif", "org.apache.giraph.io.formats.JsonLongDoubleFloatDoubleVertexInputFormat",
		                "-vip", "wasb:///example/data/tiny_graph.txt",
		                "-vof", "org.apache.giraph.io.formats.IdWithValueTextOutputFormat",
		                "-op",  "wasb:///example/output/shortestpaths",
		                "-w", "2"
		# Create the definition
		$jobDefinition = New-AzureHDInsightMapReduceJobDefinition
		  -JarFile $jarFile
		  -ClassName "org.apache.giraph.GiraphRunner"
		  -Arguments $jobArguments
		
		# Run the job, write output to the PowerShell window
		$job = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $jobDefinition
		Write-Host "Wait for the job to complete ..." -ForegroundColor Green
		Wait-AzureHDInsightJob -Job $job
		Write-Host "STDERR"
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $job.JobId -StandardError
		Write-Host "Display the standard output ..." -ForegroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $job.JobId -StandardOutput

	En el ejemplo anterior, sustituya **clustername** por el nombre del clúster de HDInsight que ha instalado Giraph.

5. **Ver los resultados**.  Una vez finalizado el trabajo, los resultados se almacenarán en dos archivos de salida en la carpeta __wasb:///example/out/shotestpaths__. Los archivos se denominan archivos __parte-m-00001__ y __part-m-00002__. Realice los pasos siguientes para descargar y ver el resultado.

		$subscriptionName = "<SubscriptionName>"       # Azure subscription name
		$storageAccountName = "<StorageAccountName>"   # Azure storage account name
		$containerName = "<ContainerName>"             # Blob storage container name

		# Select the current subscription
		Select-AzureSubscription $subscriptionName
		
		# Create the storage account context object
		$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
		$storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey

		# Download the job output to the workstation
		Get-AzureStorageBlobContent -Container $containerName -Blob example/output/shortestpaths/part-m-00001 -Context $storageContext -Force
		Get-AzureStorageBlobContent -Container $containerName -Blob example/output/shortestpaths/part-m-00002 -Context $storageContext -Force

	De esta forma se creará la estructura de directorios __example/output/shortestpaths__ en el directorio actual de la estación de trabajo y se descargarán los dos archivos de salida a esa ubicación.

	Utilice el cmdlet __Cat__ para mostrar el contenido de los archivos: 

		Cat example/output/shortestpaths/part*

	La salida debe ser similar a la siguiente:


		0	1.0
		4	5.0
		2	2.0
		1	0.0
		3	1.0

	El ejemplo SimpleShortestPathComputation se ha codificado de forma rígida para comenzar por el Id. de objeto 1 y encontrar la ruta más corta a los demás objetos. Así que la salida se debe leer como  `destination_id distance`, donde la distancia es el valor (o peso) de los bordes recorridos entre el identificador de objeto 1 y el identificador de destino.
	
	Visualizando esto, puede verificar los resultados recorriendo las rutas más cortas entre el Id. 1 y todos los demás objetos. Tenga en cuenta que la ruta más corta entre el Id. 1 y el Id. 4 es 5. Esta es la distancia total entre <span style="color:orange">Id. 1 y 3</span>y después entre <span style="color:red">Id. 3 y 4</span>.

	![Drawing of objects as circles with shortest paths drawn between](.\media\hdinsight-hadoop-giraph-install\giraph-graph-out.png) 


## <a name="usingPS"></a>Instalar Giraph en clústeres de Hadoop de HDInsight mediante PowerShell

En esta sección se utiliza el cmdlet **<a href = "http://msdn.microsoft.com/es-es/library/dn858088.aspx" target="_blank">Add-AzureHDInsightScriptAction</a>** para invocar scripts que usan la acción de script para personalizar un clúster. Antes de continuar, asegúrese de que ha instalado y configurado PowerShell. Para obtener información acerca de la configuración de una estación de trabajo para que ejecute cmdlets de HDInsight PowerShell, consulte [Instalación y configuración de Azure PowerShell][powershell-install-configure].

Lleve a cabo los siguiente pasos:

1. Abra una ventana de PowerShell de Azure y declare las siguientes variables:

		# PROVIDE VALUES FOR THESE VARIABLES
		$subscriptionName = "<SubscriptionName>"		# Name of the Azure subscription
		$clusterName = "<HDInsightClusterName>"			# The HDInsight cluster name
		$storageAccountName = "<StorageAccountName>"	# Azure storage account that hosts the default container
		$storageAccountKey = "<StorageAccountKey>"      # Key for the storage account
		$containerName = $clusterName
		$location = "<MicrosoftDataCenter>"				# The location of the HDInsight cluster. It must in the same data center as the storage account.
		$clusterNodes = <ClusterSizeInNumbers>			# The number of nodes in the HDInsight cluster.
		$version = "<HDInsightClusterVersion>"          # For example "3.1"
	
2. Especifique los valores de configuración como nodos del clúster y el almacenamiento predeterminado que se va a usar.

		# SPECIFY THE CONFIGURATION OPTIONS
		Select-AzureSubscription $subscriptionName
		$config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes $clusterNodes
		$config.DefaultStorageAccount.StorageAccountName="$storageAccountName.blob.core.windows.net"
		$config.DefaultStorageAccount.StorageAccountKey=$storageAccountKey
		$config.DefaultStorageAccount.StorageContainerName=$containerName
	
3. Utilice el cmdlet **Add-AzureHDInsightScriptAction** para agregar la acción de script a la configuración de clúster. Más adelante, cuando se crea el clúster, se ejecuta la acción de script. 

		# ADD SCRIPT ACTION TO CLUSTER CONFIGURATION
		$config = Add-AzureHDInsightScriptAction -Config $config -Name "Install Giraph" -ClusterRoleCollection HeadNode -Uri https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1

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
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Nombre de la acción de script</td></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">ClusterRoleCollection</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Especifica los nodos en los que se ejecuta el script de personalización. Los valores válidos son HeadNode (para instalar en el nodo principal) o DataNode (para instalar en todos los nodos de datos). Puede usar uno de los valores o ambos.</td></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">URI</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Especifica el URI para el script que se ejecuta</td></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Parámetros</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Parámetros requeridos por el script. El script de ejemplo que se usa en este tema no requiere ningún parámetro y, por tanto, no ve este parámetro en el fragmento de código anterior.
	</td></tr>
	</table>
	
4. Por último, inicie el aprovisionamiento de un clúster personalizado con Giraph instalado.  
	
		# START PROVISIONING A CLUSTER WITH GIRAPH INSTALLED
		New-AzureHDInsightCluster -Config $config -Name $clusterName -Location $location -Version $version 

Cuando el sistema lo pida, escriba las credenciales para el clúster. Puede tardar varios minutos en crearse el clúster.


## <a name="usingSDK"></a>Instalar Giraph en clústeres de Hadoop de HDInsight mediante el SDK de .NET

El SDK .NET de HDInsight proporciona bibliotecas de cliente .NET que facilitan el trabajo con HDInsight desde una aplicación .NET. En esta sección se proporcionan instrucciones sobre cómo utilizar la acción de script del SDK para aprovisionar un clúster que tiene Giraph instalado. Se deben llevar a cabo los siguiente procedimientos:

- Instalación del SDK .NET de HDInsight
- Creación de un certificado autofirmado
- Creación de una aplicación de consola
- Ejecución de la aplicación


**Para instalar el SDK .NET de HDInsight**

Puede instalar la compilación publicada más reciente del SDK desde [NuGet](http://nuget.codeplex.0com/wikipage?title=Getting%20Started). Las instrucciones se mostrarán en el siguiente procedimiento.

**Para crear un certificado autofirmado**

Cree un certificado autofirmado, instálelo en su estación de trabajo y cárguelo en su suscripción de Azure. Para obtener instrucciones, consulte [Creación de un certificado autofirmado](http://go.microsoft.com/fwlink/?LinkId=511138). 


**Para crear una aplicación de Visual Studio**

1. Abra Visual Studio 2013.

2. En el menú Archivo, haga clic en **Nuevo** y, a continuación, haga clic en **Proyecto**.

3. En Nuevo proyecto, escriba o seleccione los siguientes valores:
	
	<table style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse;">
	<tr>
	<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">Propiedad</th>
	<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">Valor</th></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Categoría</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px; padding-right:5px;">Templates/Visual C#/Windows</td></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Plantilla</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Aplicación de consola</td></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Nombre</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">CreateGiraphCluster</td></tr>
	</table>

4. Haga clic en **Aceptar** para crear el proyecto.

5. En el menú **Herramientas**, haga clic en **Administrador de paquetes NuGet** y, a continuación, en **Consola del Administrador de paquetes**.

6. Ejecute los siguientes comandos en la consola para instalar el paquete.

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

        // PROVIDE VALUES FOR THE VARIABLES
        string thumbprint = "<CertificateThumbprint>";  
        string subscriptionId = "<AzureSubscriptionID>";
        string location = "<MicrosoftDataCenterLocation>";
        string storageaccountname = "<AzureStorageAccountName>.blob.core.windows.net";
        string storageaccountkey = "<AzureStorageAccountKey>";
        string username = "<HDInsightUsername>";
        string password = "<HDInsightUserPassword>";
        int clustersize = <NumberOfNodesInTheCluster>;

        // PROVIDE THE CERTIFICATE THUMBPRINT TO RETRIEVE THE CERTIFICATE FROM THE CERTIFICATE STORE 
        X509Store store = new X509Store();
        store.Open(OpenFlags.ReadOnly);
        X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.Thumbprint == thumbprint);

        // CREATE AN HDINSIGHT CLIENT OBJECT
        HDInsightCertificateCredential creds = new HDInsightCertificateCredential(new Guid(subscriptionId), cert);
        var client = HDInsightClient.Connect(creds);
		client.IgnoreSslErrors = true;
        
        // PROVIDE THE CLUSTER INFORMATION
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
            Version = "3.1"
        };        

10. Anexe el código siguiente a la función Main() para que use la clase [ScriptAction](http://msdn.microsoft.com/es-es/library/microsoft.windowsazure.management.hdinsight.clusterprovisioning.data.scriptaction.aspx) para invocar un script personalizado para instalar Giraph.

		// ADD THE SCRIPT ACTION TO INSTALL GIRAPH
        clusterInfo.ConfigActions.Add(new ScriptAction(
          "Install Giraph", // Name of the config action
          new ClusterNodeType[] { ClusterNodeType.HeadNode }, // List of nodes to install Giraph on
          new Uri("https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1"), // Location of the script to install Giraph
		  null //because the script used does not require any parameters.
        ));

11. Por último, cree el clúster.

		client.CreateCluster(clusterInfo);

11. Guarde los cambios en la aplicación y genere la solución. 

**Para ejecutar la aplicación**

Abra una consola de PowerShell, navegue hasta la ubicación donde ha guardado el proyecto de Visual Studio, navegue hasta el directorio \bin\debug dentro del proyecto y, a continuación, ejecute el siguiente comando:

	.\CreateGiraphCluster <cluster-name>

Proporcione un nombre de clúster y presione ENTRAR para aprovisionar un clúster con Giraph instalado.


## Consulte también##
- [Instale y use Spark en clústeres de HDInsight][hdinsight-install-spark] para obtener instrucciones acerca de cómo usar la personalización del clúster para instalar y usar Spark en clústeres de Hadoop de HDInsight. Spark es un marco de procesamiento paralelo de código abierto que admite el procesamiento en memoria para mejorar el rendimiento de las aplicaciones analíticas de Big Data.
- [Instalar R en clústeres de HDInsight][hdinsight-install-r] proporciona instrucciones acerca de cómo usar la personalización del clúster para instalar y usar R en clústeres de Hadoop para HDInsight. R es un lenguaje de código fuente abierto y el entorno para la computación estadística y proporciona cientos de de funciones estadísticas integradas y su propio lenguaje de programación que combina aspectos de la programación funcional y orientada a objetos. También proporciona amplias capacidades gráficas.
- [Instale Solr en clústeres de HDInsight](../hdinsight-hadoop-solr-install). Use la personalización del clúster para instalar Solr en clústeres de Hadoop para HDInsight. Solr le permite realizar potentes operaciones de búsqueda en los datos almacenados.



[tools]: https://github.com/Blackmist/hdinsight-tools
[aps]: http://azure.microsoft.com/es-es/documentation/articles/install-configure-powershell/

[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-install-r]: ../hdinsight-hadoop-r-scripts/
[hdinsight-install-spark]: ../hdinsight-hadoop-spark-install/
[hdinsight-cluster-customize]: ../hdinsight-hadoop-customize-cluster<!--HONumber=42-->
