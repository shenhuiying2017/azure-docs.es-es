<properties 
	pageTitle="Uso de R en HDInsight para personalizar clústeres | Microsoft Azure" 
	description="Obtenga información acerca de cómo instalar y usar R para personalizar los clústeres de Hadoop." 
	services="hdinsight" 
	documentationCenter="" 
	authors="bradsev" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/31/2015" 
	ms.author="bradsev"/>

# Instalación y uso de R en clústeres de Hadoop para HDInsight

Puede instalar R en cualquier tipo de clúster de Hadoop en HDInsight mediante la personalización de clústeres de **acción de script**. Esto permite que los analistas y los científicos de datos usen R para implementar el eficaz marco de programación MapReduce/YARN para procesar grandes cantidades en datos en clústeres de Hadoop que están implementados en HDInsight.

La acción de script le permite ejecutar scripts para personalizar un clúster, conforme se crea el clúster. Para obtener más información, consulte [Personalización de un clúster de HDInsight mediante la acción de script][hdinsight-cluster-customize].


## <a name="whatIs"></a>¿Qué es R?

El <a href="http://www.r-project.org/" target="_blank">proyecto R para el cálculo estadístico</a> es un entorno y lenguaje de código abierto para el cálculo estadístico. R proporciona cientos de de funciones estadísticas integradas y su propio lenguaje de programación que combina aspectos de la programación funcional y orientada a objetos. También proporciona amplias capacidades gráficas. R es el entorno de programación preferido para los científicos y los estadísticos más profesionales de una amplia variedad de campos.

Los scripts de R se pueden ejecutar en clústeres de Hadoop en HDInsight que se han personalizado mediante la acción de script cuando se creó para instalar el entorno de R. R es compatible con el almacenamiento de blobs de Azure (WASB) para que los datos que se almacenan allí se puedan procesar mediante R en HDInsight.

## <a name="install"></a>¿Cómo instalo R?

Un [script de ejemplo](https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1) para instalar R en un clúster de HDInsight se encuentra disponible desde un blob de solo lectura en almacenamiento de Azure. Esta sección proporciona instrucciones sobre cómo usar el script de ejemplo durante el aprovisionamiento del clúster mediante el Portal de Azure.

> [AZURE.NOTE]El script de ejemplo se introdujo con el clúster de HDInsight versión 3.1. Para obtener más información acerca de las versiones de clústeres de HDInsight, consulte las [versiones de clústeres de HDInsight](../hdinsight-component-versioning/).

1. Inicie el aprovisionamiento de un clúster con la opción **CREACIÓN PERSONALIZADA**, tal como se describe en [Aprovisionamiento de clústeres mediante opciones personalizadas](../hdinsight-provision-clusters/#portal). 
2. En la página **Acciones de script** del asistente, haga clic en **Agregar acción de script** para proporcionar detalles acerca de las acciones de script, como se muestra a continuación:

	![Uso de la acción de script para personalizar un clúster](./media/hdinsight-hadoop-r-scripts/hdi-r-script-action.png "Uso de la acción de script para personalizar un clúster")
	
	<table border='1'>
	<tr><th>Propiedad</th><th>Valor</th></tr>
	<tr><td>Nombre</td>
		<td>Especifique un nombre para la acción de script, por ejemplo, <b>Instalar R</b>.</td></tr>
	<tr><td>URI de script</td>
		<td>Especifique el URI al script que se invoca para personalizar el clúster, por ejemplo, <i>https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1</i></td></tr>
	<tr><td>Tipo de nodo</td>
		<td>Especifique los nodos en los que se ejecuta el script de personalización. Puede elegir <b>Todos los nodos</b>, <b>Solo los nodos principales</b> o <b>Solo los nodos de trabajo</b>.
	<tr><td>Parámetros</td>
		<td>Especifique los parámetros, si lo requiere el script. Sin embargo, el script para instalar R no requiere ningún parámetro, por lo que puede dejarlo en blanco.</td></tr>
</table>Puede agregar más de una acción de script para instalar varios componentes en el clúster. Una vez agregados los scripts, haga clic en la marca de verificación para iniciar el aprovisionamiento del clúster.

También puede utilizar el script para instalar R en HDInsight con Azure PowerShell o el SDK de .NET de HDInsight. Más adelante en este artículo se proporcionan instrucciones para estos procedimientos.

## <a name="useR"></a>¿Cómo ejecuto scripts de R con HDInsight?
En esta sección se describe cómo ejecutar un script de R en el clúster de Hadoop con HDInsight.

1. **Establezca una conexión de Escritorio remoto con el clúster**: en el Portal de Azure, habilite el Escritorio remoto para el clúster que creó con R instalado y, a continuación, conéctese al clúster. Para obtener instrucciones, consulte <a href="http://azure.microsoft.com/documentation/articles/hdinsight-administer-use-management-portal/#rdp" target="_blank">Conexión a los clústeres de HDInsight con RDP</a>.

2. **Abra la consola de R**: la instalación de R coloca un vínculo a la consola de R en el escritorio del nodo principal. Haga clic en él para abrir la consola de R.

3. **Ejecute el script de R**: el script de R se puede ejecutar directamente desde la consola de R pegándolo, seleccionándolo y presionando ENTRAR. Este es un script de ejemplo simple que genera los números del 1 al 100 y, a continuación, los multiplica por 2.

		library(rmr2)
		library(rhdfs)
		ints = to.dfs(1:100)
		calc = mapreduce(input = ints, map = function(k, v) cbind(v, 2*v))
		from.dfs(calc)

Las dos primeras líneas llaman a las bibliotecas de RHadoop instaladas con R. La línea final imprime los resultados en la consola. El resultado debe ser similar al siguiente:

	[1,]  1 2
	[2,]  2 4
	.
	.
	.
	[98,]  98 196
	[99,]  99 198
	[100,] 100 200

## <a name="usingPS"></a>Instalación de R en HDInsight con Azure PowerShell

En esta sección se utiliza el cmdlet **<a href = "http://msdn.microsoft.com/library/dn858088.aspx" target="_blank">Add-AzureHDInsightScriptAction</a>** para invocar scripts mediante la acción de script a fin de personalizar un clúster. Antes de continuar, asegúrese de que ha instalado y configurado PowerShell de Azure. Para obtener información acerca de la configuración de una estación de trabajo para que ejecute cmdlets de HDInsight PowerShell, consulte [Instalación y configuración de Azure PowerShell][powershell-install-configure].

Lleve a cabo los siguiente pasos:

1. Abra la consola de Azure PowerShell y declare las siguientes variables:

		# PROVIDE VALUES FOR THESE VARIABLES
		$subscriptionName = "<SubscriptionName>"		# Name of the Azure subscription
		$clusterName = "<HDInsightClusterName>"			# HDInsight cluster name
		$storageAccountName = "<StorageAccountName>"	# Azure storage account that hosts the default container
		$storageAccountKey = "<StorageAccountKey>"      # Key for the storage account
		$containerName = $clusterName
		$location = "<MicrosoftDataCenter>"				# Location of the HDInsight cluster. It must be in the same data center as the storage account.
		$clusterNodes = <ClusterSizeInNumbers>			# The number of nodes in the HDInsight cluster.
		$version = "<HDInsightClusterVersion>"          # HDInsight version, for example "3.1"
	
2. Especifique los valores de configuración (como nodos en el clúster) y el almacenamiento predeterminado que se va a usar.

		# SPECIFY THE CONFIGURATION OPTIONS
		Select-AzureSubscription $subscriptionName
		$config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes $clusterNodes
		$config.DefaultStorageAccount.StorageAccountName="$storageAccountName.blob.core.windows.net"
		$config.DefaultStorageAccount.StorageAccountKey=$storageAccountKey
		$config.DefaultStorageAccount.StorageContainerName=$containerName
	
3. Use el cmdlet **Add-AzureHDInsightScriptAction** para invocar el script de ejemplo para instalar R; por ejemplo:

		# INVOKE THE SCRIPT USING THE SCRIPT ACTION
		$config = Add-AzureHDInsightScriptAction -Config $config -Name "Install R"  -ClusterRoleCollection HeadNode,DataNode -Uri https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1


	El cmdlet **Add-AzureHDInsightScriptAction** toma los parámetros siguientes:

	<table style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse;">
<tr>
<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">Parámetro</th>
<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:550px; padding-left:5px; padding-right:5px;">Definición</th></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Configuración</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px; padding-right:5px;">El objeto de configuración al que se agrega la información de la acción de script</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Nombre</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Nombre de la acción de script</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">ClusterRoleCollection</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Especifica los nodos en los que se ejecuta el script de personalización. Los valores válidos son **HeadNode** (para instalar en el nodo principal) o **DataNode** (para instalar en todos los nodos de datos). Puede usar uno de los valores o ambos.</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Parámetros</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Parámetros requeridos por el script 
</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">URI</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Especifica el URI para el script que se ejecuta</td></tr>
</table>
	
4. Por último, aprovisione el clúster que personalizó para tener R instalado.
	
		# PROVISION A CLUSTER WITH R INSTALLED
		New-AzureHDInsightCluster -Config $config -Name $clusterName -Location $location -Version $version 

Cuando el sistema lo pida, escriba las credenciales para el clúster. El clúster puede tardar varios minutos en crearse.


## <a name="usingSDK"></a>Instalación de R en HDInsight con el SDK de .NET

El SDK .NET de HDInsight proporciona bibliotecas de cliente .NET que facilitan el trabajo con HDInsight desde una aplicación .NET.

Realice los siguientes procedimientos para aprovisionar un clúster de HDInsight con el SDK:

- [Instalación del SDK de .NET de HDInsight](#installSDK)
- [Creación de un certificado autofirmado](#createCert)
- [Creación de una aplicación .NET en Visual Studio](#createApp)
- [Ejecución de la aplicación](#runApp)

En las secciones siguientes se muestra cómo realizar estos procedimientos.

### <a name="installSDK"></a>Para instalar el SDK de .NET de HDInsight

Puede instalar la compilación publicada más reciente del SDK desde [NuGet](http://nuget.codeplex.com/wikipage?title=Getting%20Started). Las instrucciones se mostrarán en el siguiente procedimiento.

### <a name="createCert"></a>Para crear un certificado autofirmado

Cree un certificado autofirmado, instálelo en su estación de trabajo y cárguelo en su suscripción de Azure. Para obtener instrucciones, consulte [Creación de un certificado autofirmado](http://go.microsoft.com/fwlink/?LinkId=511138).


### <a name="createApp"></a>Para crear una aplicación .NET en Visual Studio

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
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">CreateRCluster</td></tr>
</table>

4. Haga clic en **Aceptar** para crear el proyecto.

5. En el menú **Herramientas**, haga clic en **Administrador de paquetes NuGet** y, a continuación, en la **Consola del administrador de paquetes**.

6. Ejecute el siguiente comando en la consola para instalar el paquete.

		Install-Package Microsoft.WindowsAzure.Management.HDInsight

	Este comando agrega las bibliotecas y referencias .NET a los paquetes desde el proyecto de Visual Studio actual.

7. En el **Explorador de soluciones**, haga doble clic en **Program.cs** para abrirlo.

8. Agregue las siguientes instrucciones **using** en la parte superior del archivo:

		using System.Security.Cryptography.X509Certificates;
		using Microsoft.WindowsAzure.Management.HDInsight;
		using Microsoft.WindowsAzure.Management.HDInsight.ClusterProvisioning;
		using Microsoft.WindowsAzure.Management.HDInsight.Framework.Logging;
	
9. En la función **Main()**, copie y pegue el siguiente código y proporcione valores para las variables:
		
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

10. Anexe el código siguiente a la función **Main()** para que use la clase [ScriptAction](http://msdn.microsoft.com/library/microsoft.windowsazure.management.hdinsight.clusterprovisioning.data.scriptaction.aspx) con el fin de invocar un script personalizado para instalar R.

		// ADD THE SCRIPT ACTION TO INSTALL R

        clusterInfo.ConfigActions.Add(new ScriptAction(
            "Install R",
            new ClusterNodeType[] { ClusterNodeType.HeadNode, ClusterNodeType.DataNode },
            new Uri("https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1"), null
            ));

11. Por último, cree el clúster:

		client.CreateCluster(clusterInfo);

11. Guarde los cambios en la aplicación y genere la solución.

### <a name="runApp"></a>Para ejecutar la aplicación

Abra la consola de Azure PowerShell, navegue a la ubicación donde guardó el proyecto, luego al directorio \\bin\\debug dentro del proyecto y, a continuación, ejecute el siguiente comando:

	.\CreateRCluster <cluster-name>

Proporcione un nombre de clúster y presione ENTRAR para aprovisionar un clúster con R instalado.

## <a name="seeAlso"></a>Consulte también

- [Instalación y uso de Spark en clústeres de HDInsight][hdinsight-install-spark] para obtener instrucciones sobre cómo usar la personalización de clústeres para instalar y usar Spark en clústeres de Hadoop para HDInsight. Spark es un marco de procesamiento paralelo de código abierto que admite el procesamiento en memoria para mejorar el rendimiento de las aplicaciones analíticas de Big Data.
- [Instalación de Giraph en clústeres de HDInsight](../hdinsight-hadoop-giraph-install). Use la personalización del clúster para instalar Giraph en clústeres de Hadoop para HDInsight. Giraph permite realizar un procesamiento gráfico con Hadoop y se puede usar con HDInsight de Azure.
- [Instalación de Solr en clústeres de HDInsight](../hdinsight-hadoop-solr-install). Use la personalización del clúster para instalar Solr en clústeres de Hadoop para HDInsight. Solr le permite realizar potentes operaciones de búsqueda en los datos almacenados.

[powershell-install-configure]: ../install-configure-powershell.md
[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-cluster-customize]: ../hdinsight-hadoop-customize-cluster
[hdinsight-install-spark]: ../hdinsight-hadoop-spark-install/
 

<!---HONumber=July15_HO2-->