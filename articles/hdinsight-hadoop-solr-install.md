<properties 
	pageTitle="Usar la acción de script en HDInsight para instalar Solr en el clúster de Hadoop| Azure" 
	description="Obtenga información acerca de cómo personalizar el clúster de HDInsight para instalar Solr. Va a utilizar una opción de configuración de la acción de script para usar un script para instalar Solr." 
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

# Instalación y uso de Solr en clústeres de Hadoop de HDInsight

Puede instalar Solr en cualquier tipo de clúster de Hadoop en HDInsight mediante la personalización de clústeres de **acción de script**. La acción de script le permite ejecutar scripts para personalizar un clúster, conforme se crea el clúster. Para obtener más información, consulte [Personalización de un clúster de HDInsight mediante la acción de script][hdinsight-cluster-customize].

En este tema aprenderá a instalar Solr con la acción de script. Solr es una eficaz plataforma de búsqueda eficaz y proporciona capacidades de búsqueda de nivel empresarial en datos administrados por Hadoop. Una vez haya instalado Solr en un clúster de HDInsight, también aprenderá cómo buscar datos mediante Solr.

> [AZURE.NOTE] El script de ejemplo que se utiliza en este tema crea un clúster de Solr con una configuración concreta. Si desea configurar el clúster de Solr con distintas colecciones, particiones, esquemas o réplicas, entre otras, debe modificar el script y los archivos binarios de Solr en consecuencia.


## En este artículo

- [¿Qué es Solr?](#whatis)
- [¿Cómo instalo Solr?](#install)
- [¿Cómo uso Solr en HDInsight?](#usesolr)
- [Instalar Solr en clústeres de Hadoop de HDInsight mediante PowerShell](#usingPS)
- [Instalar Solr en clústeres de Hadoop de HDInsight mediante el SDK de .NET](#usingSDK) 


## <a name="whatis"></a>¿Qué es Solr?

<a href="http://lucene.apache.org/solr/features.html" target="_blank">Apache Solr</a> es una plataforma de búsqueda empresarial que permite una eficaz búsqueda de texto completo en los datos. Si bien Hadoop permite almacenar y administrar grandes cantidades de datos, Apache Solr proporciona las capacidades de búsqueda para recuperar rápidamente los datos. Este tema proporciona instrucciones sobre cómo personalizar un clúster de HDInsight para instalar Solr.   

## <a name="install"></a>¿Cómo instalo Solr?

Hay un script de ejemplo para instalar Solr en un clúster de HDInsight disponible desde un blob de almacenamiento de Azure de solo lectura en [https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1](https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1). Esta sección proporciona instrucciones sobre cómo utilizar el script de ejemplo durante el aprovisionamiento del clúster mediante el Portal de administración de Azure. 


> [AZURE.NOTE] El script de ejemplo solo funciona con el clúster de HDInsight versión 3.1.  Para obtener más información acerca de las versiones de clústeres de HDInsight, consulte las[versiones de clústeres de HDInsight](http://azure.microsoft.com/documentation/articles/hdinsight-component-versioning/).


1. Inicie el aprovisionamiento de un clúster con la opción **CREACIÓN PERSONALIZADA**, tal como se describe en [Aprovisionamiento de clústeres mediante opciones personalizadas](http://azure.microsoft.com/documentation/articles/hdinsight-provision-clusters/#portal). 
2. En la página **Acciones de script** del asistente, haga clic en **Agregar acción de script** para proporcionar detalles acerca de la acción de script, tal como se muestra a continuación:

	![Use Script Action to customize a cluster](./media/hdinsight-hadoop-solr-install/hdi-script-action-solr.png "Use Script Action to customize a cluster")
	
	<table border='1'>
		<tr><th>Propiedad</th><th>Valor</th></tr>
		<tr><td>Nombre</td>
			<td>Especifique un nombre para la acción de script. Por ejemplo, <b>Instalar Solr</b>.</td></tr>
		<tr><td>URI de script</td>
			<td>Especifique el URI al script que se ha invocado para personalizar el clúster. Por ejemplo: <i>https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1</i></td></tr>
		<tr><td>Tipo de nodo</td>
			<td>Especifica los nodos en los que se ejecuta el script de personalización. Puede elegir <b>Todos los nodos</b>, <b>Solo nodos principales</b>, o <b>Solo nodos de trabajo</b>.
		<tr><td>Parámetros</td>
			<td>Especifique los parámetros, si lo requiere el script. El script para instalar Solr no requiere ningún parámetro, por lo que puede dejarlo en blanco.</td></tr>
	</table>	

	Puede agregar más de una acción de script para instalar varios componentes en el clúster. Después de haber agregado los scripts, haga clic en la marca de verificación para iniciar el aprovisionamiento del clúster.

También puede utilizar el script para instalar Solr en HDInsight con PowerShell o el SDK de .NET de HDInsight. Más adelante en este tema se proporcionan instrucciones para estos procedimientos.

## <a name="usesolr"></a>¿Cómo uso Solr en HDInsight?

Debe comenzar con la indización de Solr con algunos archivos de datos. A continuación, puede utilizar Solr para ejecutar consultas de búsqueda en los datos indizados. Realice los pasos siguientes para utilizar Solr en un clúster de HDInsight:

1. **RDP en el clúster de HDInsight con Solr instalado**. En el Portal de administración de Azure, habilite el Escritorio remoto para el clúster que ha creado con Solr instalado y, a continuación, remoto en el clúster. Para obtener instrucciones, vea <a href="http://azure.microsoft.com/documentation/articles/hdinsight-administer-use-management-portal/#rdp" target="_blank">Conexión a los clústeres de HDInsight con RDP</a>.

2. **Indice Solr mediante la carga de archivos de datos**. Al indizar Solr, colocar en él aquellos documentos que tenga que buscar. Para indizar Solr, RDP en el clúster, desplácese al escritorio, abra la línea de comandos de Hadoop y desplácese a **C:\apps\dist\solr-4.7.2\example\exampledocs**. Ejecute el siguiente comando: 
	
		java -jar post.jar solr.xml monitor.xml

	Verá estos resultados en la consola.

		POSTing file solr.xml
		POSTing file monitor.xml
		2 files indexed.
		COMMITting Solr index changes to http://localhost:8983/solr/update..
		Time spent: 0:00:01.624

	La utilidad post.jar indiza Solr con dos documentos de muestra, **solr.xml** y **monitor.xml**. La utilidad post.jar y los documentos de muestra están disponibles con la instalación de Solr.

3. **Utilice el panel de Solr para buscar dentro de los documentos indizados**. En la sesión RDP al clúster de HDInsight, abra Internet Explorer e inicie el panel de Solr en **http://headnodehost:8983/solr/#/**. En el panel izquierdo, en la lista desplegable Selector principal, seleccione **collection1** y, dentro de ella, haga clic en **Consulta**. Por ejemplo, para seleccionar y devolver a todos los documentos de Solr, indique los valores siguientes:
	1. En el cuadro de texto **q**, escriba **\*:**\*. Se devolverán todos los documentos indizados en Solr. Si desea buscar una cadena específica dentro de los documentos, puede especificar esa cadena aquí.
	2. En el cuadro de texto **wt**, seleccione el formato de salida. El valor predeterminado es **json**. Haga clic en **Ejecutar consulta**.

		![Use Script Action to customize a cluster](./media/hdinsight-hadoop-solr-install/hdi-solr-dashboard-query.png "Run a query on Solr dashboard")
	
	La salida devuelve los dos documentos utilizados para indizar el Solr. La salida debe ser similar a la siguiente:

			"response": {
			    "numFound": 2,
			    "start": 0,
			    "maxScore": 1,
			    "docs": [
			      {
			        "id": "SOLR1000",
			        "name": "Solr, the Enterprise Search Server",
			        "manu": "Apache Software Foundation",
			        "cat": [
			          "software",
			          "search"
			        ],
			        "features": [
			          "Advanced Full-Text Search Capabilities using Lucene",
			          "Optimized for High Volume Web Traffic",
			          "Standards Based Open Interfaces - XML and HTTP",
			          "Comprehensive HTML Administration Interfaces",
			          "Scalability - Efficient Replication to other Solr Search Servers",
			          "Flexible and Adaptable with XML configuration and Schema",
			          "Good unicode support: héllo (hello with an accent over the e)"
			        ],
			        "price": 0,
			        "price_c": "0,USD",
			        "popularity": 10,
			        "inStock": true,
			        "incubationdate_dt": "2006-01-17T00:00:00Z",
			        "_version_": 1486960636996878300
			      },
			      {
			        "id": "3007WFP",
			        "name": "Dell Widescreen UltraSharp 3007WFP",
			        "manu": "Dell, Inc.",
			        "manu_id_s": "dell",
			        "cat": [
			          "electronics and computer1"
			        ],
			        "features": [
			          "30\" TFT active matrix LCD, 2560 x 1600, .25mm dot pitch, 700:1 contrast"
			        ],
			        "includes": "USB cable",
			        "weight": 401.6,
			        "price": 2199,
			        "price_c": "2199,USD",
			        "popularity": 6,
			        "inStock": true,
			        "store": "43.17614,-90.57341",
			        "_version_": 1486960637584081000
			      }
			    ]
			  }
   

4. **Se recomienda: Haga una copia de seguridad de los datos indizados desde Solr a blobs de almacenamiento de Azure (WASB) asociados con el clúster de HDInsight**. Una práctica recomendada realice una copia los datos indizados desde los nodos del clúster de Solr en WASB. Realice los pasos siguientes para ello:

	1. Desde la sesión RDP, abra Internet Explorer y apunte a la dirección URL siguiente:

			http://localhost:8983/solr/replication?command=backup

		Debería obtener una respuesta parecida a esta.

			<?xml version="1.0" encoding="UTF-8"?>
			<response>
			  <lst name="responseHeader">
			    <int name="status">0</int>
			    <int name="QTime">9</int>
			  </lst>
			  <str name="status">OK</str>
			</response>

	2. En la sesión remota, vaya a {SOLR_HOME}\{Collection}\data. Para el clúster creado con el script de muestra, debe ser **C:\apps\dist\solr-4.7.2\example\solr\collection1\data**. En esta ubicación, verá una carpeta de instantáneas que se crea con un nombre similar a **snapshot.*timestamp***.
	
	3. Comprima la carpeta de instantáneas y cárguela a WASB. Desde la línea de comandos de Hadoop, desplácese a la ubicación de la carpeta de instantáneas mediante el comando siguiente:

			  hadoop fs -CopyFromLocal snapshot._timestamp_.zip /example/data

		Este comando copia la instantánea a /example/data/ bajo el contenedor dentro de la cuenta de almacenamiento predeterminada asociada al clúster.

## <a name="usingPS"></a>Instalar Solr en clústeres de Hadoop de HDInsight mediante PowerShell

En esta sección se utiliza el cmdlet **<a href = "http://msdn.microsoft.com/library/dn858088.aspx" target="_blank">Add-AzureHDInsightScriptAction</a>** para invocar scripts que usan la acción de script para personalizar un clúster. Antes de continuar, asegúrese de que ha instalado y configurado PowerShell. Para obtener información acerca de la configuración de una estación de trabajo para que ejecute cmdlets de HDInsight PowerShell, consulte [Instalación y configuración de Azure PowerShell][powershell-install-configure].

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
		$config = Add-AzureHDInsightScriptAction -Config $config -Name "Install Solr" -ClusterRoleCollection HeadNode,DataNode -Uri https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1

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
	
4. Por último, inicie el aprovisionamiento de un clúster personalizado con Solr instalado.  
	
		# START PROVISIONING A CLUSTER WITH SOLR INSTALLED
		New-AzureHDInsightCluster -Config $config -Name $clusterName -Location $location -Version $version 

Cuando el sistema lo pida, escriba las credenciales para el clúster. Puede tardar varios minutos en crearse el clúster.


## <a name="usingSDK"></a>Instalar Solr en clústeres de Hadoop de HDInsight mediante el SDK de .NET

El SDK .NET de HDInsight proporciona bibliotecas de cliente .NET que facilitan el trabajo con HDInsight desde una aplicación .NET. En esta sección se proporcionan instrucciones sobre cómo utilizar la acción de script del SDK para aprovisionar un clúster que tiene Solr instalado. Se deben llevar a cabo los siguiente procedimientos:

- Instalación del SDK .NET de HDInsight
- Creación de un certificado autofirmado
- Creación de una aplicación de consola
- Ejecución de la aplicación


**Para instalar el SDK .NET de HDInsight**

Puede instalar la compilación publicada más reciente del SDK desde [NuGet](http://nuget.codeplex.com/wikipage?title=Getting%20Started). Las instrucciones se mostrarán en el siguiente procedimiento.

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
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Consola Application</td></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Nombre</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">CreateSolrCluster</td></tr>
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

10. Anexe el código siguiente a la función Main() para que use la clase [ScriptAction](http://msdn.microsoft.com/library/microsoft.windowsazure.management.hdinsight.clusterprovisioning.data.scriptaction.aspx) para invocar un script personalizado para instalar Solr.

		// ADD THE SCRIPT ACTION TO INSTALL Solr
        clusterInfo.ConfigActions.Add(new ScriptAction(
          "Install Solr", // Name of the config action
          new ClusterNodeType[] { ClusterNodeType.HeadNode, ClusterNodeType.DataNode }, // List of nodes to install Solr on
          new Uri("https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1"), // Location of the script to install Solr
		  null //because the script used does not require any parameters.
        ));

11. Por último, cree el clúster.

		client.CreateCluster(clusterInfo);

11. Guarde los cambios en la aplicación y genere la solución. 

**Para ejecutar la aplicación**

Abra una consola de PowerShell, navegue hasta la ubicación donde ha guardado el proyecto de Visual Studio, navegue hasta el directorio \bin\debug dentro del proyecto y, a continuación, ejecute el siguiente comando:

	.\CreateSolrCluster <cluster-name>

Proporcione un nombre de clúster y presione ENTRAR para aprovisionar un clúster con Solr instalado.


## Consulte también##
- [Instale y use Spark en clústeres de HDInsight][hdinsight-install-spark]. Use la personalización del clúster para instalar Spark en clústeres de Hadoop para HDInsight. Spark es un marco de procesamiento paralelo de código abierto que admite el procesamiento en memoria para mejorar el rendimiento de las aplicaciones analíticas de Big Data.
- [Instale R en clústeres de HDInsight][hdinsight-install-r]. Use la personalización del clúster para instalar R en clústeres de Hadoop para HDInsight. R es un lenguaje de código fuente abierto y el entorno para la computación estadística y proporciona cientos de de funciones estadísticas integradas y su propio lenguaje de programación que combina aspectos de la programación funcional y orientada a objetos. También proporciona amplias capacidades gráficas.
- [Instale Giraph en clústeres de HDInsight](hdinsight-hadoop-giraph-install.md). Use la personalización del clúster para instalar Giraph en clústeres de Hadoop para HDInsight. Giraph permite realizar un procesamiento gráfico mediante Hadoop, y se puede usar con Azure HDInsight.




[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-install-r]: ../hdinsight-hadoop-r-scripts/
[hdinsight-install-spark]: ../hdinsight-hadoop-spark-install/
[hdinsight-cluster-customize]: ../hdinsight-hadoop-customize-cluster

<!--HONumber=42-->
