<properties
	pageTitle="Administrar clústeres de Hadoop mediante la CLI de Azure | Microsoft Azure"
	description="Cómo usar la CLI de Azure para administrar clústeres de Hadoop en HDInsight"
	services="hdinsight"
	editor="cgronlun"
	manager="paulettm"
	authors="mumian"
	tags="azure-portal"
	documentationCenter=""/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/16/2015"
	ms.author="jgao"/>

# Administración de clústeres de Hadoop en HDInsight mediante la CLI de Azure

[AZURE.INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Aprenda a usar la [interfaz de la línea de comandos de Azure](xplat-cli-install.md) para administrar clústeres de Hadoop en HDInsight de Azure. La CLI de Azure se implementa en Node.js y se puede usar en cualquier plataforma compatible con Node.js, entre las que se incluyen Windows, Mac y Linux.

Este artículo solo trata del uso de la CLI de Azure con HDInsight. Para información general sobre cómo usar la CLI de Azure, vea [Instalación y configuración de la CLI de Azure][azure-command-line-tools].

##Requisitos previos

Antes de empezar este artículo, debe tener lo siguiente:

- **Una suscripción de Azure**. Vea [Obtener evaluación gratuita de Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- **CLI de Azure** - Vea [Instalar y configurar la CLI de Azure](../xplat-cli-install.md) para obtener información de instalación y configuración.
- **Conectarse a Azure** mediante el comando siguiente:

		azure login

	Para obtener más información acerca de cómo autenticarse con una cuenta profesional o educativa, consulte [Conexión a una suscripción de Azure desde la CLI de Azure](xplat-cli-connect.md).
	
- **Cambiar al modo de Administrador de recursos de Azure** con el siguiente comando:

		azure config mode arm

Para obtener ayuda, use el modificador **-h**. Por ejemplo:

	azure hdinsight cluster create -h
	
##Creación de clústeres

[AZURE.INCLUDE [provisioningnote](../../includes/hdinsight-provisioning.md)]

Para poder crear un clúster de HDInsight debe tener una Administración de recursos de Azure (ARM) y una cuenta de almacenamiento de blobs de Azure. Además, debe especificar lo siguiente:

- **Grupo de recursos de Azure**: se debe crear una cuenta de Análisis de Data Lake dentro de un grupo de recursos de Azure. Administrador de recursos de Azure permite trabajar con los recursos de la aplicación como un grupo. Puede implementar, actualizar o eliminar todos los recursos de la aplicación en una operación única y coordinada. 

	Para mostrar los grupos de recursos de su suscripción:
	
		azure group list 
	
	Para crear un nuevo grupo de recursos:
	
		azure group create -n "<Resource Group Name>" -l "<Azure Location>"

- **Nombre del clúster de HDInsight**

- **Ubicación**: uno de los centros de datos de Azure que admite clústeres de HDInsight. Para obtener una lista de ubicaciones admitidas, consulte los [precios de HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

- **Cuenta de almacenamiento predeterminada**: HDInsight usa un contenedor de almacenamiento de blobs de Azure como sistema de archivos predeterminado. Es necesario tener una cuenta de Almacenamiento de Azure antes de crear un clúster de HDInsight.

	Para crear una cuenta de almacenamiento de Azure:
	
		azure storage account create "<Azure Storage Account Name>" -g "<Resource Group Name>" -l "<Azure Location>" --type LRS

	> [AZURE.NOTE]La cuenta de Almacenamiento debe colocarse con HDInsight en el mismo centro de datos. El tipo de cuenta de almacenamiento no puede ser ZRS, porque ZRS no admite tablas.

	Para obtener información sobre la creación de una cuenta de almacenamiento de Azure a través del Portal de Azure, consulte [Creación, administración o eliminación de una cuenta de almacenamiento][azure-create-storageaccount].
	
	Si ya tiene una cuenta de almacenamiento pero no sabe su nombre ni su clave, puede usar los comandos siguientes para recuperar dicha información:
	
		-- Lists Storage accounts
		azure storage account list
		-- Shows a Storage account
		azure storage account show "<Storage Account Name>"
		-- Lists the keys for a Storage account
		azure storage account keys list "<Storage Account Name>" -g "<Resource Group Name>"

	Para obtener información sobre cómo obtener la información mediante el Portal de Azure, vea la sección "Visualización, copia y regeneración de claves de acceso de almacenamiento" de [Creación, administración o eliminación de una cuenta de Almacenamiento][azure-create-storageaccount].

- **(Opcional) Contenedor de blobs predeterminado**: el comando **azure hdinsight cluster create** crea el contenedor si aún no existe. Si opta por crear el contenedor antes, puede usar el comando siguiente:

	azure storage container create --account-name "<Storage Account Name>" --account-key <Storage Account Key> [ContainerName]

Cuando tenga preparada la cuenta de almacenamiento, podrá crear un clúster.

	azure hdinsight cluster create --resource-group <Resource Group Name> --clusterName <Cluster Name> --location <Location> --osType <Windows | Linux> --version <Cluster Version> --clusterType <Hadoop | HBase | Spark | Storm> --storageAccountName <Default Storage Account Name> --storageAccountKey <Storage Account Key> --storageContainer <Default Storage Container> --username <HDInsight Cluster Username> --password <HDInsight Cluster Password> --sshUserName <SSH Username> --sshPassword <SSH User Password> --workerNodeCount <Number of Worker Nodes>


##Creación de clústeres mediante archivos de configuración
Normalmente, se crea un clúster de HDInsight, se ejecutan trabajos en él y luego se elimina el clúster para reducir los costos. La interfaz de línea de comandos le da la opción de guardar las configuraciones en un archivo para que pueda volver a usarlas cada vez que cree un clúster.

	azure hdinsight config create [options ] <Config File Path> <overwirte>
	azure hdinsight config add-config-values [options] <Config File Path>
	azure hdinsight config add-script-action [options] <Config File Path>

Ejemplo: Crear un archivo de configuración que contenga una acción de sript para ejecutarlo al crear un clúster.

	hdinsight config create "C:\myFiles\configFile.config"
	hdinsight config add-script-action --configFilePath "C:\myFiles\configFile.config" --nodeType HeadNode --uri <Script Action URI> --name myScriptAction --parameters "-param value"

##Creación de clústeres con una acción de script

Aquí tiene un ejemplo:

	azure hdinsight cluster create -g mahirg001 -l westus -y Linux --clusterType Hadoop --version 3.2 --defaultStorageAccountName mystorageaccount --defaultStorageAccountKey <defaultStorageAccountKey> --defaultStorageContainer mycontainer --userName admin --password <clusterPassword> --sshUserName sshuser --sshPassword <sshPassword> --workerNodeCount 1 –configurationPath "C:\myFiles\configFile.config" myNewCluster01
	
Para información sobre la acción de script, vea [Personalización de clústeres de HDInsight mediante la acción de scripts (Linux)](hdinsight-hadoop-customize-cluster-linux.md).

##Enumeración y visualización de los detalles del clúster
Use los comandos siguientes para enumerar y mostrar los detalles del clúster:

	azure hdinsight cluster list
	azure hdinsight cluster show <Cluster Name>

![HDI.CLIListCluster][image-cli-clusterlisting]


##Eliminación de clústeres
Use el comando siguiente para eliminar un clúster:

	azure hdinsight cluster delete <Cluster Name>

##Escalado de clústeres

Para cambiar el tamaño del clúster de Hadoop:

	azure hdinsight cluster resize [options] <clusterName> <Target Instance Count>


## Habilitar/deshabilitar el acceso HTTP para un clúster

	azure hdinsight cluster enable-http-access [options] <Cluster Name> <userName> <password>
	azure hdinsight cluster disable-http-access [options] <Cluster Name>

## Habilitar/deshabilitar el acceso RDP para un clúster

  	azure hdinsight cluster enable-rdp-access [options] <Cluster Name> <rdpUserName> <rdpPassword> <rdpExpiryDate>
  	azure hdinsight cluster disable-rdp-access [options] <Cluster Name>


##Pasos siguientes
En este artículo, ha aprendido a realizar diferentes tareas administrativas en clústeres de HDInsight. Para obtener más información, consulte los artículos siguientes:

* [Administración de HDInsight mediante el Portal de Azure][hdinsight-admin-portal]
* [Administración de HDInsight con Azure PowerShell][hdinsight-admin-powershell]
* [Introducción a HDInsight de Azure][hdinsight-get-started]
* [Cómo usar la CLI de Azure][azure-command-line-tools]


[azure-command-line-tools]: ../xplat-cli.md
[azure-create-storageaccount]: ../storage-create-storage-account.md
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/


[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-get-started]: ../hdinsight-get-started.md

[image-cli-account-download-import]: ./media/hdinsight-administer-use-command-line/HDI.CLIAccountDownloadImport.png
[image-cli-clustercreation]: ./media/hdinsight-administer-use-command-line/HDI.CLIClusterCreation.png
[image-cli-clustercreation-config]: ./media/hdinsight-administer-use-command-line/HDI.CLIClusterCreationConfig.png
[image-cli-clusterlisting]: ./media/hdinsight-administer-use-command-line/HDI.CLIListClusters.png "Enumeración y visualización de clústeres"

<!---HONumber=AcomDC_1217_2015-->