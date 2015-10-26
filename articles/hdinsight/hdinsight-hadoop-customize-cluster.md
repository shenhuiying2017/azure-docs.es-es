<properties
	pageTitle="Personalizar los clústeres de HDInsight mediante acciones de script | Microsoft Azure"
	description="Obtenga información acerca de cómo personalizar clústeres de HDInsight mediante la acción de script."
	services="hdinsight"
	documentationCenter=""
	authors="nitinme"
	manager="paulettm"
	editor="cgronlun"
	tags="azure-portal"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/02/2015"
	ms.author="nitinme"/>

# Personalización de clústeres de HDInsight mediante la acción de scripts (Windows)

HDInsight proporciona una opción de configuración denominada **Acción de script** que invoca scripts personalizados, que definen la personalización que se realizará en el clúster en el proceso de aprovisionamiento. Estos scripts pueden utilizarse para instalar software adicional en un clúster o para cambiar la configuración de las aplicaciones de un clúster.

[AZURE.INCLUDE [hdinsight-azure-preview-portal](../../includes/hdinsight-azure-preview-portal.md)]

* [Personalizar los clústeres de HDInsight mediante la acción de script](hdinsight-hadoop-customize-cluster-v1.md)

> [AZURE.NOTE]La información de este artículo es específica de los clústeres de HDInsight basados en Windows. Para obtener una versión de este artículo específica para clústeres basados en Linux, vea [Personalización de clústeres de HDInsight mediante la acción de script (Linux)](hdinsight-hadoop-customize-cluster-linux.md)

Los clústeres de HDInsight se pueden personalizar de muchas maneras distintas, por ejemplo, con la inclusión de cuentas de almacenamiento de Azure adicionales, mediante el cambio de archivos de configuración de Hadoop (core-site.xml, hive-site.xml, etc.) o mediante la adición de bibliotecas compartidas (por ejemplo, Oozie o Hive) en ubicaciones comunes en el clúster. Estas personalizaciones pueden realizarse a través de Azure PowerShell, el SDK de .NET de HDInsight de Azure o el Portal de vista previa de Azure. Para obtener más información, consulte [Creación de clústeres de Hadoop en HDInsight][hdinsight-provision-cluster].

## Acción de script en el proceso de aprovisionamiento de clústeres

Acción de script sólo se utiliza mientras un clúster está en proceso de creación. El siguiente diagrama ilustra el momento en que acción de script se ejecuta durante el proceso de aprovisionamiento:

![Fases y personalización de clústeres de HDInsight durante la creación de clústeres][img-hdi-cluster-states]

Cuando se ejecuta el script, el clúster entra en la fase **ClusterCustomization**. En esta fase, el script se ejecuta bajo la cuenta de administrador del sistema, de forma paralela a todos los nodos especificados en el clúster. Asimismo, proporciona privilegios completos de administrador en los nodos.

> [AZURE.NOTE]Puesto que dispone de privilegios de administrador en los nodos del clúster durante la fase **ClusterCustomization**, podrá usar el script para realizar operaciones como la detención o el inicio de servicios, incluidos los servicios de Hadoop. Por lo tanto, como parte del script, debe asegurarse de que los servicios de Ambari y los demás servicios de Hadoop estén en funcionamiento antes de que finalice el script. Estos servicios son necesarios para determinar correctamente el estado del clúster durante la creación. Si modifica cualquier configuración en el clúster que afecte a estos servicios, deberá usar las funciones auxiliares proporcionadas. Para obtener más información sobre las funciones auxiliares, vea [Desarrollo de scripts de acción de script con HDInsight][hdinsight-write-script].

Los registros de errores y resultados del script se almacenan en la cuenta de almacenamiento predeterminada especificada para el clúster. Los registros se almacenan en una tabla con el nombre **u<\\cluster-name-fragment><\\time-stamp>setuplog**. Estos son registros agregados desde la ejecución del script en todos los nodos (nodos principal y de trabajo) del clúster.

Cada clúster puede aceptar varias acciones de script, que se invocan en el orden en que se hayan especificado. Los scripts se pueden ejecutar en el nodo principal, los nodos de trabajo, o ambos.

HDInsight proporciona varios scripts para instalar los siguientes componentes en clústeres de HDInsight:

Nombre | Script
----- | -----
**Instalar Spark** | https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1. Consulte [Instalación y uso de Spark en clústeres de HDInsight][hdinsight-install-spark].
**Instalar R** | https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1. Consulte [Instalación y uso de R en clústeres de HDInsight][hdinsight-install-r].
**Instalar Solr** | https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1. Consulte [Instalación y uso de Solr en clústeres de HDInsight](hdinsight-hadoop-solr-install.md).
**Instalación de Giraph** | https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1. Consulte [Instalación y uso de Giraph en clústeres de HDInsight](hdinsight-hadoop-giraph-install.md).



## Llamada a scripts con el Portal de vista previa de Azure

**En el Portal de vista previa de Azure**

1. Comience a crear un clúster, tal como se describe en [Creación de clústeres de Hadoop en HDInsight](hdinsight-provision-clusters.md#portal).
2. En Configuración opcional, de la hoja **Acciones de script**, haga clic en **agregar acción de script** para proporcionar detalles sobre la acción de script, tal como se muestra a continuación:

	![Uso de la acción de script para personalizar un clúster](./media/hdinsight-hadoop-customize-cluster/HDI.CreateCluster.8.png "Uso de la acción de script para personalizar un clúster")

	<table border='1'>
	<tr><th>Propiedad</th><th>Valor</th></tr>
	<tr><td>Nombre</td>
		<td>Especifique un nombre para la acción de script.</td></tr>
	<tr><td>URI de script</td>
		<td>Especifique el URI al script invocado para personalizar el clúster.</td></tr>
	<tr><td>Head/Worker</td>
		<td>Especifique los nodos (**Head** o **Worker**) en los que se ejecuta el script de personalización.</b>.
	<tr><td>Parámetros</td>
		<td>Especifique los parámetros, si lo requiere el script.</td></tr>
</table>Presione ENTRAR para agregar más de una acción de script para instalar varios componentes en el clúster.

3. Haga clic en **Seleccionar** para guardar la configuración de la acción de script y continúe con la creación del clúster.

## Llamadas a scripts con Azure PowerShell

Este script de PowerShell muestra cómo instalar Spark en un clúster de HDInsight basado en Windows. Para instalar otro software, deberá reemplazar el archivo de script en el script:

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

4. Por último, comience a crear un clúster personalizado con Spark instalado.

		# Start creating a cluster with Spark installed
		New-AzureHDInsightCluster -Config $config -Name $clusterName -Location $location -Version $version

Cuando el sistema lo pida, escriba las credenciales para el clúster. El clúster puede tardar varios minutos en crearse.

## Llamada a scripts mediante .NET SDK 

En el ejemplo siguiente se muestra cómo instalar Spark en un clúster de HDInsight basado en Windows. Para instalar otro software, deberá reemplazar el archivo de script en el código.

**Para crear un clúster de HDInsight con Spark**

1. Cree una aplicación de consola en C# mediante Visual Studio.
2. En la Consola del administrador de paquetes Nuget, ejecute el siguiente comando.

		Install-Package Microsoft.Azure.Management.HDInsight -Pre
		Install-Package Microsoft.Azure.Common.Authentication -Pre

2. Use las siguientes instrucciones using en el archivo Program.cs:

		using System;
		using System.Security;
		using Microsoft.Azure.Management.HDInsight;
		using Microsoft.Azure.Management.HDInsight.Models;
		
		using Microsoft.Azure;
		using Microsoft.Azure.Common.Authentication;
		using Microsoft.Azure.Common.Authentication.Factories;
		using Microsoft.Azure.Common.Authentication.Models;

3. Coloque el código de la clase con lo siguiente:

        private static HDInsightManagementClient _hdiManagementClient;

        private static Guid SubscriptionId = new Guid("<YourAzureSubscriptionID>");
        private const string ResourceGroupName = "<ExistingAzureResourceGroupName>";
        private const string NewClusterName = "<NewAzureHDInsightClusterName>";
        private const int NewClusterNumNodes = <NumberOfClusterNodes>;
        private const string NewClusterLocation = "East US";
        private const string NewClusterVersion = "3.2";
        private const string ExistingStorageName = "<ExistingAzureStorageAccountName>";
        private const string ExistingStorageKey = "<ExistingAzureStorageAccountKey>";
        private const string ExistingContainer = "<ExistingAzureBlobStorageContainer>";
        private const HDInsightClusterType NewClusterType = HDInsightClusterType.Hadoop;
        private const OSType NewClusterOSType = OSType.Windows;
        private const string NewClusterUsername = "<HttpUserName>";
        private const string NewClusterPassword = "<HttpUserPassword>";

        static void Main(string[] args)
        {
            System.Console.WriteLine("Running");

            var tokenCreds = GetTokenCloudCredentials();
            var subCloudCredentials = GetSubscriptionCloudCredentials(tokenCreds, SubscriptionId);

            _hdiManagementClient = new HDInsightManagementClient(subCloudCredentials);

            CreateCluster();

        }

        private static void CreateCluster()
        {
            var parameters = new ClusterCreateParameters
            {
                ClusterSizeInNodes = NewClusterNumNodes,
                Location = NewClusterLocation,
                ClusterType = NewClusterType,
                OSType = NewClusterOSType,
                Version = NewClusterVersion,

                DefaultStorageAccountName = ExistingStorageName,
                DefaultStorageAccountKey = ExistingStorageKey,
                DefaultStorageContainer = ExistingContainer,

                UserName = NewClusterUsername,
                Password = NewClusterPassword,
            };

            ScriptAction sparkScriptAction = new ScriptAction("Install Spark",
                new Uri("https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1"), "");

            parameters.ScriptActions.Add(ClusterNodeType.HeadNode, new System.Collections.Generic.List<ScriptAction> { sparkScriptAction });
            parameters.ScriptActions.Add(ClusterNodeType.WorkerNode, new System.Collections.Generic.List<ScriptAction> { sparkScriptAction });

            _hdiManagementClient.Clusters.Create(ResourceGroupName, NewClusterName, parameters);
        }


        public static SubscriptionCloudCredentials GetTokenCloudCredentials(string username = null, SecureString password = null)
        {
            var authFactory = new AuthenticationFactory();

            var account = new AzureAccount { Type = AzureAccount.AccountType.User };

            if (username != null && password != null)
                account.Id = username;

            var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];

            var accessToken =
                authFactory.Authenticate(account, env, AuthenticationFactory.CommonAdTenant, password, ShowDialog.Auto)
                    .AccessToken;

            return new TokenCloudCredentials(accessToken);
        }

        public static SubscriptionCloudCredentials GetSubscriptionCloudCredentials(SubscriptionCloudCredentials creds, Guid subId)
        {
            return new TokenCloudCredentials(subId.ToString(), ((TokenCloudCredentials)creds).Token);
        }

4. Presione **F5** para ejecutar la aplicación.


## Soporte técnico para el software de código abierto utilizado en clústeres de HDInsight
El servicio Microsoft Azure HDInsight es una plataforma flexible que permite compilar aplicaciones con grandes volúmenes de datos en la nube mediante el ecosistema de tecnologías de código abierto formadas en torno a Hadoop. Microsoft Azure proporciona el nivel general de soporte técnico para las tecnologías de código abierto, tal como se describe en la sección **Ámbito de soporte técnico** del <a href="http://azure.microsoft.com/support/faq/" target="_blank">sitio web de Preguntas más frecuentes de soporte técnico de Azure</a>. Además, el servicio de HDInsight ofrece un nivel adicional de soporte técnico para algunos de los componentes, como se describe a continuación.

Hay dos tipos de componentes de código abierto que están disponibles en el servicio de HDInsight:

- **Componentes integrados**: estos componentes están instalados previamente en clústeres de HDInsight y proporcionan la funcionalidad básica del clúster. Por ejemplo, el administrador de recursos de YARN, el lenguaje de consulta Hive (HiveQL) y la biblioteca Mahout pertenecen a esta categoría. Una lista completa de los componentes del clúster está disponible en <a href="http://azure.microsoft.com/documentation/articles/hdinsight-component-versioning/" target="_blank">Novedades en las versiones de clústeres de Hadoop proporcionadas por HDInsight</a>.
- **Componentes personalizados**: el usuario del clúster puede instalar o usar en la carga de trabajo cualquier componente que esté disponible en la comunidad o que haya creado personalmente.

Los componentes integrados son totalmente compatibles, y el soporte técnico de Microsoft le ayudará a aislar y resolver problemas relacionados con estos componentes.

> [AZURE.WARNING]Los componentes ofrecidos con HDInsight son totalmente compatibles. Además, el soporte técnico de Microsoft le ayudará a aislar y resolver problemas relacionados con estos componentes.
>
> Los componentes personalizados reciben soporte técnico comercialmente razonable para ayudarle a solucionar el problema. Esto podría resolver el problema o pedirle que forme parte de los canales disponibles para las tecnologías de código abierto donde se encuentra la más amplia experiencia para esa tecnología. Por ejemplo, hay diversos sitios de la comunidad que se pueden usar, como el [foro de MSDN para HDInsight](https://social.msdn.microsoft.com/Forums/azure/es-ES/home?forum=hdinsight), [http://stackoverflow.com](http://stackoverflow.com). Además, los proyectos de Apache tienen sitios del proyecto en [http://apache.org](http://apache.org), por ejemplo, [Hadoop](http://hadoop.apache.org/) y [Spark](http://spark.apache.org/).

El servicio HDInsight proporciona varias maneras de utilizar los componentes personalizados. Independientemente de cómo se use el componente o cómo se instale en el clúster, se aplica el mismo nivel de soporte técnico. A continuación, se muestra una lista de las maneras más comunes que se pueden usar los componentes personalizados en los clústeres de HDInsight:

1. Envío de trabajos: se pueden enviar al clúster trabajos de Hadoop o de otros tipos que ejecuten o usen componentes personalizados.
2. Personalización del clúster: durante la creación del clúster puede especificar una configuración adicional y componentes personalizados que se instalarán en los nodos del clúster.
3. Muestras: para los componentes personalizados más populares, Microsoft y otros pueden proporcionar muestras de cómo estos componentes se pueden utilizar en los clústeres de HDInsight. Estas muestras se proporcionan sin soporte técnico.

## Desarrollo de scripts de acción de script

Consulte [Desarrollo de scripts de acción de script con HDInsight][hdinsight-write-script].


## Consulte también

- En [Creación de clústeres de Hadoop en HDInsight][hdinsight-provision-cluster] se proporcionan instrucciones sobre cómo crear un clúster de HDInsight con otras opciones personalizadas.
- [Desarrollo de la acción de script con HDInsight][hdinsight-write-script]
- [Instalación y uso de Spark en clústeres de HDInsight][hdinsight-install-spark]
- [Instalación y uso de R en clústeres de Hadoop de HDInsight][hdinsight-install-r]
- [Instalación y uso de Solr en clústeres de HDInsight](hdinsight-hadoop-solr-install.md)
- [Instalación y uso de Giraph en clústeres de HDInsight](hdinsight-hadoop-giraph-install.md)

[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-install-r]: hdinsight-hadoop-r-scripts.md
[hdinsight-write-script]: hdinsight-hadoop-script-actions.md
[hdinsight-provision-cluster]: hdinsight-provision-clusters.md
[powershell-install-configure]: ../install-configure-powershell.md


[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster/HDI-Cluster-state.png "Fases durante la creación del clúster"

<!---HONumber=Oct15_HO3-->