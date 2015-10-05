<properties
	pageTitle="Personalizar los clústeres de HDInsight mediante acciones de script | Microsoft Azure"
	description="Obtenga información acerca de cómo personalizar clústeres de HDInsight mediante la acción de script."
	services="hdinsight"
	documentationCenter=""
	authors="Blackmist"
	manager="paulettm"
	editor="cgronlun"
	tags="azure-portal"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/21/2015"
	ms.author="larryfr"/>

# Personalizar los clústeres de HDInsight mediante la acción de script

HDInsight proporciona una opción de configuración denominada **Acción de script** que invoca scripts personalizados, que definen la personalización que se realizará en el clúster en el proceso de aprovisionamiento. Estos scripts pueden utilizarse para instalar software adicional en un clúster o para cambiar la configuración de las aplicaciones de un clúster.

> [AZURE.NOTE]La información de este artículo es específica de los clústeres de HDInsight basados en Linux. Para obtener una versión de este artículo específica para clústeres basados en Windows, vea [Personalización de clústeres de HDInsight mediante la acción de script (Windows)](hdinsight-hadoop-customize-cluster.md)

## Acción de script en el proceso de aprovisionamiento de clústeres

Acción de script sólo se utiliza mientras un clúster está en proceso de creación. El siguiente diagrama ilustra el momento en que acción de script se ejecuta durante el proceso de aprovisionamiento:

![Fases y personalización de clústeres de HDInsight durante el aprovisionamiento de los clústeres][img-hdi-cluster-states]

Se ejecuta el script durante la configuración de HDInsight. En esta fase, el script se ejecuta de forma paralela en todos los nodos especificados en el clúster con privilegios raíz en los nodos.

> [AZURE.NOTE]Puesto que dispone de privilegios raíz en los nodos del clúster cuando se ejecuta el script, puede realizar operaciones como la detención o el inicio de servicios, incluidos los servicios de Hadoop. Si detiene los servicios, tiene que asegurarse de que los servicios de Ambari y los demás servicios de Hadoop estén en funcionamiento antes de que finalice la ejecución del script. Estos servicios son necesarios para determinar correctamente el estado del clúster durante la creación.

Cada clúster puede aceptar varias acciones de script, que se invocan en el orden en que se hayan especificado. Un script se puede ejecutar en los nodos principales, los nodos de trabajo, o en ambos.

> [AZURE.IMPORTANT]Las acciones de script se tienen que completar dentro de un periodo de 15 minutos o superarán el tiempo de espera.

## Ejemplo de scripts de acción de script

Los scripts de acción de script pueden usarse desde el Portal de vista previa de Azure, Azure PowerShell o el SDK de .NET de HDInsight. En este artículo se muestra cómo usar la acción de script desde el portal. Para obtener información sobre cómo usar PowerShell y el SDK de .NET para usar la acción de script, examine los ejemplos enumerados en la tabla siguiente.

HDInsight proporciona varios scripts para instalar los siguientes componentes en clústeres de HDInsight:

Nombre | Script
----- | -----
**Instalación de Hue** | https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv01/install-hue-uber-v01.sh. Consulte [Instalación y uso de Hue en clústeres de HDInsight](hdinsight-hadoop-hue-linux.md)
**Instalar Spark** | https://hdiconfigactions.blob.core.windows.net/linuxsparkconfigactionv01/spark-installer-v01.sh. Consulte [Instalación y uso de Spark en clústeres de HDInsight](hdinsight-hadoop-spark-install-linux.md).
**Instalar R** | https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh. Consulte [Instalación y uso de R en clústeres de HDInsight](hdinsight-hadoop-r-scripts-linux.md).
**Instalar Solr** | https://hdiconfigactions.blob.core.windows.net/linuxsolrconfigactionv01/solr-installer-v01.sh. Consulte [Instalación y uso de Solr en clústeres de HDInsight](hdinsight-hadoop-solr-install-linux.md).
**Instalación de Giraph** | https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh. Consulte [Instalación y uso de Giraph en clústeres de HDInsight](hdinsight-hadoop-giraph-install-linux.md).

##Use una acción de script desde el Portal de vista previa de Azure

1. Inicie el aprovisionamiento de un clúster tal como se describe en [Aprovisionamiento de clústeres mediante opciones personalizadas](hdinsight-provision-clusters.md#portal).

2. En __Configuración opcional__, para la hoja **Acciones de script**, haga clic en **agregar acción de script** para proporcionar detalles acerca de la acción de script, tal como se muestra a continuación:

	![Uso de la acción de script para personalizar un clúster](./media/hdinsight-hadoop-customize-cluster-linux/HDI.CreateCluster.8.png "Uso de la acción de script para personalizar un clúster")

	| Propiedad | Valor |
	| -------- | ----- |
	| Nombre | Especifique un nombre para la acción de script. |
	| URI de script | Especifique el URI al script que se ha invocado para personalizar el clúster. |
	| Head/Worker | Especifique los nodos (**Principal** o **Trabajo** o **Zookeeper**) en los que se ejecuta el script de personalización. |
	| Parámetros | Especifique los parámetros, si lo requiere el script. |

	Presione ENTRAR para agregar más de una acción de script para instalar varios componentes en el clúster.

3. Haga clic en **Seleccionar** para guardar la configuración de la acción de script y continúe con el aprovisionamiento del clúster.

##Use una acción de script de las plantillas de Administrador de recursos de Azure

En esta sección, usamos plantillas de Administrador de recursos de Azure (ARM) para aprovisionar un clúster de HDInsight y usar una acción de script para instalar componentes personalizados (en este ejemplo, R) en el clúster. En esta sección se proporciona una plantilla ARM de ejemplo para aprovisionar un clúster mediante la acción de script.

### Antes de empezar

* Para obtener información acerca de la configuración de una estación de trabajo para que ejecute cmdlets de HDInsight PowerShell, consulte [Instalación y configuración de Azure PowerShell](../powershell-install-configure.md).
* Para obtener instrucciones sobre cómo crear plantillas ARM, consulte [Creación de plantillas del Administrador de recursos de Azure](resource-group-authoring-templates.md).
* Si todavía no ha utilizado Azure PowerShell con el Administrador de recursos, consulte [Uso de Azure PowerShell con el Administrador de recursos de Azure](powershell-azure-resource-manager)

### Aprovisionamiento de clúster mediante acción de script

1. Copie la plantilla siguiente en una ubicación en el equipo. Esta plantilla instala R en el nodo principal, así como en los nodos de trabajo en el clúster. También puede comprobar si la plantilla JSON es válida. Pegue la plantilla de contenido en [JSONLint](http://jsonlint.com/), una herramienta de validador JSON en línea.

			{
		    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
		    "contentVersion": "1.0.0.0",
		    "parameters": {
		        "clusterLocation": {
		            "type": "string",
		            "defaultValue": "West US",
		            "allowedValues": [ "West US" ]
		        },
		        "clusterName": {
		            "type": "string"
		        },
		        "clusterUserName": {
		            "type": "string",
		            "defaultValue": "admin"
		        },
		        "clusterUserPassword": {
		            "type": "securestring"
		        },
		        "sshUserName": {
		            "type": "string",
		            "defaultValue": "hdiuser"
		        },
		        "sshPassword": {
		            "type": "securestring"
		        },
		        "clusterStorageAccountName": {
		            "type": "string"
		        },
		        "clusterStorageAccountResourceGroup": {
		            "type": "string"
		        },
		        "clusterStorageType": {
		            "type": "string",
		            "defaultValue": "Standard_LRS",
		            "allowedValues": [
		                "Standard_LRS",
		                "Standard_GRS",
		                "Standard_ZRS"
		            ]
		        },
		        "clusterStorageAccountContainer": {
		            "type": "string"
		        },
		        "clusterHeadNodeCount": {
		            "type": "int",
		            "defaultValue": 1
		        },
		        "clusterWorkerNodeCount": {
		            "type": "int",
		            "defaultValue": 2
		        }
		    },
		    "variables": {
		    },
		    "resources": [
		        {
		            "name": "[parameters('clusterStorageAccountName')]",
		            "type": "Microsoft.Storage/storageAccounts",
		            "location": "[parameters('clusterLocation')]",
		            "apiVersion": "2015-05-01-preview",
		            "dependsOn": [ ],
		            "tags": { },
		            "properties": {
		                "accountType": "[parameters('clusterStorageType')]"
		            }
		        },
		        {
		            "name": "[parameters('clusterName')]",
		            "type": "Microsoft.HDInsight/clusters",
		            "location": "[parameters('clusterLocation')]",
		            "apiVersion": "2015-03-01-preview",
		            "dependsOn": [
		                "[concat('Microsoft.Storage/storageAccounts/', parameters('clusterStorageAccountName'))]"
		            ],
		            "tags": { },
		            "properties": {
		                "clusterVersion": "3.2",
		                "osType": "Linux",
		                "clusterDefinition": {
		                    "kind": "hadoop",

		                    "configurations": {
		                        "gateway": {
		                            "restAuthCredential.isEnabled": true,
		                            "restAuthCredential.username": "[parameters('clusterUserName')]",
		                            "restAuthCredential.password": "[parameters('clusterUserPassword')]"
		                        }
		                    }
		                },
		                "storageProfile": {
		                    "storageaccounts": [
		                        {
		                            "name": "[concat(parameters('clusterStorageAccountName'),'.blob.core.windows.net')]",
		                            "isDefault": true,
		                            "container": "[parameters('clusterStorageAccountContainer')]",
		                            "key": "[listKeys(resourceId(parameters('clusterStorageAccountResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('clusterStorageAccountName')), providers('Microsoft.Storage', 'storageAccounts').apiVersions[0]).key1]"
		                        }
		                    ]
		                },
		                "computeProfile": {
		                    "roles": [
		                        {
		                            "name": "headnode",
		                            "targetInstanceCount": "[parameters('clusterHeadNodeCount')]",
		                            "hardwareProfile": {
		                                "vmSize": "Large"
		                            },
		                            "osProfile": {
		                                "linuxOperatingSystemProfile": {
		                                    "username": "[parameters('sshUserName')]",
		                                    "password": "[parameters('sshPassword')]"
		                                }
		                            },
		                            "scriptActions": [
		                                {
		                                    "name": "installR",
		                                    "uri": "https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh",
		                                    "parameters": ""
		                                }
		                            ]
		                        },
		                        {
		                            "name": "workernode",
		                            "targetInstanceCount": "[parameters('clusterWorkerNodeCount')]",
		                            "hardwareProfile": {
		                                "vmSize": "Large"
		                            },
		                            "osProfile": {
		                                "linuxOperatingSystemProfile": {
		                                    "username": "[parameters('sshUserName')]",
		                                    "password": "[parameters('sshPassword')]"
		                                }
		                            },
		                            "scriptActions": [
		                                {
		                                    "name": "installR",
		                                    "uri": "https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh",
		                                    "parameters": ""
		                                }
		                            ]
		                        }
		                    ]
		                }
		            }
		        }
		    ],
		    "outputs": {
		        "cluster":{
		            "type" : "object",
		            "value" : "[reference(resourceId('Microsoft.HDInsight/clusters',parameters('clusterName')))]"
		        }
		    }
		}



2. Inicie Azure PowerShell e inicie sesión en su cuenta de Azure. Después de proporcionar sus credenciales, el comando devuelve información acerca de su cuenta.

		Add-AzureAccount

		Id                             Type       ...
		--                             ----
		someone@example.com            User       ...

3. Si tiene varias suscripciones, proporcione el identificador de suscripción que desea usar para la implementación.

		Select-AzureSubscription -SubscriptionID <YourSubscriptionId>

4. Cambie al módulo Administrador de recursos de Azure.

		Switch-AzureMode AzureResourceManager

5. Si no tiene un grupo de recursos existente, cree uno nuevo. Proporcione el nombre del grupo de recursos y la ubicación que necesita para la solución. Se devuelve un resumen del grupo de recursos nuevo.

		New-AzureResourceGroup -Name myresourcegroup -Location "West US"

		ResourceGroupName : myresourcegroup
		Location          : westus
		ProvisioningState : Succeeded
		Tags              :
		Permissions       :
		            		Actions  NotActions
		            		=======  ==========
		            		*
		ResourceId        : /subscriptions/######/resourceGroups/ExampleResourceGroup


6. Para crear una implementación nueva para el grupo de recursos, ejecute el comando **New-AzureResourceGroupDeployment** y proporcione los parámetros necesarios. Los parámetros incluirán un nombre para la implementación, el nombre del grupo de recursos y la ruta de acceso o dirección URL a la plantilla que creó. Si la plantilla requiere parámetros, tiene que pasar también esos parámetros. En este caso, la acción de script para instalar R en el clúster no requiere ningún parámetro.


		New-AzureResourceGroupDeployment -Name mydeployment -ResourceGroupName myresourcegroup -TemplateFile <PathOrLinkToTemplate>


	Se le pedirá que proporcione valores para los parámetros definidos en la plantilla.

7. Una vez implementado el grupo de recursos, verá un resumen de la implementación.

		  DeploymentName    : mydeployment
		  ResourceGroupName : myresourcegroup
		  ProvisioningState : Succeeded
		  Timestamp         : 8/17/2015 7:00:27 PM
		  Mode              : Incremental
		  ...

8. Si se produce un error en la implementación, puede usar los cmdlets siguientes para obtener información sobre los errores.

		Get-AzureResourceGroupLog -ResourceGroup myresourcegroup -Status Failed

	Para obtener información detallada acerca de los errores de implementación, utilice el siguiente cmdlet.

		Get-AzureResourceGroupLog -ResourceGroup myresourcegroup -Status Failed -DetailedOutput

##Use una acción de script de PowerShell de Azure

En esta sección, usamos el cmdlet **<a href = "http://msdn.microsoft.com/library/dn858088.aspx" target="_blank">Add-AzureHDInsightScriptAction</a>** para invocar scripts usando la acción de script para personalizar un clúster. Antes de continuar, asegúrese de que ha instalado y configurado PowerShell de Azure. Para obtener información acerca de la configuración de una estación de trabajo para que ejecute cmdlets de HDInsight PowerShell, consulte [Instalación y configuración de Azure PowerShell](../powershell-install-configure.md).

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

3. Use el cmdlet **Add-AzureHDInsightScriptAction** para invocar el script. En el ejemplo siguiente se usa el script para instalar R en el clúster:

		# INVOKE THE SCRIPT USING THE SCRIPT ACTION
		$config = Add-AzureHDInsightScriptAction -Config $config -Name "Install R"  -ClusterRoleCollection HeadNode,WorkerNode,ZookeeperNode -Uri https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh


	El cmdlet **Add-AzureHDInsightScriptAction** toma los siguientes parámetros:

	| Parámetro | Definición |
	| --------- | ---------- |
	| Configuración | Objeto de configuración al que se agrega la información de la acción de script. |
	| Nombre | Nombre de la acción de script. |
	| ClusterRoleCollection | Especifica los nodos en los que se ejecuta el script de personalización. Los valores válidos son **HeadNode** (para instalar en el nodo principal), **WorkerNode** (para instalar en todos los nodos de datos) o **ZookeeperNode** (para instalar en el nodo zookeeper). Puede usar uno de los valores o todos. |
	| Parámetros | Parámetros requeridos por el script. |
	| URI | Especifica el URI para el script que se ejecuta. |

4. Finalmente, aprovisione el clúster

		New-AzureHDInsightCluster -Config $config -Name $clusterName -Location $location -Version $version

Cuando el sistema lo pida, escriba las credenciales para el clúster. El clúster puede tardar varios minutos en crearse.

##Use una acción de script desde .NET SDK de HDInsight

El .NET SDK de HDInsight proporciona bibliotecas de cliente que facilitan el trabajo con HDInsight desde una aplicación .NET. Los pasos siguientes muestran cómo usar un script para personalizar un clúster desde .NET SDK de HDInsight.


> [AZURE.IMPORTANT]Tiene que crear primero un certificado autofirmado, instálelo en su estación de trabajo y cárguelo en su suscripción de Azure. Para obtener instrucciones, consulte [Creación de un certificado autofirmado](http://go.microsoft.com/fwlink/?LinkId=511138).


###Creación de un proyecto de Visual Studio

1. Abra Visual Studio 2013 o 2015

2. En el menú **Archivo**, haga clic en **Nuevo** y, después, en **Proyecto**.

3. En **Nuevo proyecto**, escriba o seleccione los siguientes valores:

	| Propiedad | Valor |
	| -------- | ----- |
	| Categoría | Plantillas/Visual C#/Windows |
	| Plantilla | Aplicación de consola |
	| Nombre | ScriptActionCluster |

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

10. Anexe el código siguiente a la función **Main()** Este código invoca una acción de script, en este ejemplo se trata del script para instalar R en el clúster:

		// ADD THE SCRIPT ACTION TO INSTALL R

        clusterInfo.ConfigActions.Add(new ScriptAction(
            "Install R",
            new ClusterNodeType[] { ClusterNodeType.HeadNode, ClusterNodeType.DataNode },
            new Uri("https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh"), null
            ));

11. Por último, cree el clúster:

		client.CreateCluster(clusterInfo);

11. Guarde los cambios en la aplicación y genere la solución.

###Ejecución de la aplicación

Abra la consola de Azure PowerShell, navegue a la ubicación donde guardó el proyecto, luego al directorio \\bin\\debug dentro del proyecto y, a continuación, ejecute el siguiente comando:

	.\ScriptActionCluster <cluster-name>

Proporcione un nombre de clúster y presione ENTRAR para aprovisionar un clúster.

## Soporte técnico para el software de código abierto utilizado en clústeres de HDInsight

El servicio Microsoft Azure HDInsight es una plataforma flexible que permite compilar aplicaciones con grandes volúmenes de datos en la nube mediante el ecosistema de tecnologías de código abierto formadas en torno a Hadoop. Microsoft Azure proporciona el nivel general de soporte técnico para las tecnologías de código abierto, tal como se describe en la sección **Ámbito de soporte técnico** del [sitio web de Preguntas más frecuentes de soporte técnico de Azure](http://azure.microsoft.com/support/faq/). Además, el servicio de HDInsight ofrece un nivel adicional de soporte técnico para algunos de los componentes, como se describe a continuación.

Hay dos tipos de componentes de código abierto que están disponibles en el servicio de HDInsight:

- **Componentes integrados**: estos componentes están instalados previamente en clústeres de HDInsight y proporcionan la funcionalidad básica del clúster. Por ejemplo, el administrador de recursos de YARN, el lenguaje de consulta Hive (HiveQL) y la biblioteca Mahout pertenecen a esta categoría. Una lista completa de los componentes del clúster está disponible en [Novedades en las versiones de clústeres de Hadoop proporcionadas por HDInsight](hdinsight-component-versioning.md).

- **Componentes personalizados**: el usuario del clúster puede instalar o usar en la carga de trabajo cualquier componente que esté disponible en la comunidad o que haya creado personalmente.

> [AZURE.WARNING]Los componentes ofrecidos con HDInsight son totalmente compatibles. Además, el soporte técnico de Microsoft le ayudará a aislar y resolver problemas relacionados con estos componentes.
>
> Los componentes personalizados reciben soporte técnico comercialmente razonable para ayudarle a solucionar el problema. Esto podría resolver el problema o pedirle que forme parte de los canales disponibles para las tecnologías de código abierto donde se encuentra la más amplia experiencia para esa tecnología. Por ejemplo, hay diversos sitios de la comunidad que se pueden usar, como el [foro de MSDN para HDInsight](https://social.msdn.microsoft.com/Forums/azure/es-ES/home?forum=hdinsight), [http://stackoverflow.com](http://stackoverflow.com). Además, los proyectos de Apache tienen sitios del proyecto en [http://apache.org](http://apache.org), por ejemplo, [Hadoop](http://hadoop.apache.org/) y [Spark](http://spark.apache.org/).

El servicio HDInsight proporciona varias maneras de utilizar los componentes personalizados. Independientemente de cómo se use el componente o cómo se instale en el clúster, se aplica el mismo nivel de soporte técnico. A continuación, se muestra una lista de las maneras más comunes que se pueden usar los componentes personalizados en los clústeres de HDInsight:

1. Envío de trabajos: se pueden enviar al clúster trabajos de Hadoop o de otros tipos que ejecuten o usen componentes personalizados.

2. Personalización del clúster: durante la creación del clúster puede especificar una configuración adicional y componentes personalizados que se instalarán en los nodos del clúster.

3. Muestras: para los componentes personalizados más populares, Microsoft y otros pueden proporcionar muestras de cómo estos componentes se pueden utilizar en los clústeres de HDInsight. Estas muestras se proporcionan sin soporte técnico.

##Solución de problemas

Puede usar la interfaz de usuario web de Ambari para ver la información registrada por los scripts durante el aprovisionamiento del clúster.

1. Abra el explorador web y navegue a https://CLUSTERNAME.azurehdinsight.net. Reemplace CLUSTERNAME por el nombre del clúster de HDInsight.

	Cuando se le solicite, escriba el nombre de cuenta de administrador (admin) y la contraseña de administrador para el clúster. Tendrá que volver a escribir las credenciales de administrador en un formulario web.

2. En los vínculos de la parte superior de la página, seleccione la entrada __ops__. Esto mostrará una lista de las operaciones actuales y anteriores realizadas en el clúster a través de Ambari.

	![Barra de interfaz de usuario web de Ambari con ops seleccionado](./media/hdinsight-hadoop-customize-cluster-linux/ambari-nav.png)

3. Busque las entradas que tienen __run\_customscriptaction__ en la columna __Operaciones__. Estas se crean al ejecutarse las acciones de script.

	![Captura de pantalla de operaciones](./media/hdinsight-hadoop-customize-cluster-linux/ambariscriptaction.png)

	Seleccione esta entrada y profundice a través de los vínculos para ver el resultado STDOUT y STDERR que se genera cuando el script se ejecutó en el clúster.

## Pasos siguientes

Consulte la siguiente información y ejemplos sobre la creación y uso de scripts para personalizar un clúster:

- [Desarrollo de la acción de script con HDInsight](hdinsight-hadoop-script-actions-linux.md)
- [Instalación y uso de Spark en clústeres de HDInsight](hdinsight-hadoop-spark-install-linux.md)
- [Instalación y uso de R en clústeres de Hadoop de HDInsight](hdinsight-hadoop-r-scripts-linux.md)
- [Instalación y uso de Solr en clústeres de HDInsight](hdinsight-hadoop-solr-install-linux.md)
- [Instalación y uso de Giraph en clústeres de HDInsight](hdinsight-hadoop-giraph-install-linux.md)



[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster-linux/HDI-Cluster-state.png "Fases durante el aprovisionamiento del clúster"

<!---HONumber=Sept15_HO4-->