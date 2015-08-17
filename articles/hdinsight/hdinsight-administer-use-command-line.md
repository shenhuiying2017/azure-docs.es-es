<properties
	pageTitle="Administrar clústeres de Hadoop mediante la CLI de Azure | Microsoft Azure"
	description="Cómo usar la CLI de Azure para administrar clústeres de Hadoop en HDInsight"
	services="hdinsight"
	editor="cgronlun"
	manager="paulettm"
	authors="mumian"
	documentationCenter=""/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/08/2015"
	ms.author="jgao"/>

# Administración de clústeres de Hadoop en HDInsight mediante la interfaz de la línea de comandos de Azure (CLI de Azure)

Aprenda a utilizar la CLI de Azure para administrar clústeres de Hadoop en HDInsight de Azure. La CLI de Azure se implementa en Node.js y se puede usar en cualquier plataforma compatible con Node.js, entre las que se incluyen Windows, Mac y Linux.

La CLI de Azure es código abierto. El código fuente se administra en GitHub en <a href= "https://github.com/azure/azure-xplat-cli">https://github.com/azure/azure-xplat-cli</a>.

Este artículo solo trata del uso de la CLI de Azure con HDInsight. Para una guía general de cómo usar la CLI de Azure, vea [Cómo usar la CLI de Azure][azure-command-line-tools].


##Requisitos previos

Antes de empezar este artículo, debe tener lo siguiente:

- **Una suscripción de Azure**. Vea [Obtener evaluación gratuita de Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- **CLI de Azure** - Vea [Instalar y configurar la CLI de Azure](../xplat-cli.md) para obtener información de instalación y configuración.

##Instalación

Si no lo ha hecho ya, use el documento [Instalar y configurar la CLI Azure](../xplat-cli.md) para instalar y configurar la CLI de Azure.

##Aprovisionamiento de un clúster de HDInsight

[AZURE.INCLUDE [provisioningnote](../../includes/hdinsight-provisioning.md)]


HDInsight utiliza contenedores de almacenamiento de blobs de Azure como sistemas de archivos predeterminados. Es necesario tener una cuenta de Almacenamiento de Azure antes de crear un clúster de HDInsight.

Después de importar el archivo publishsettings, puede usar el siguiente comando para crear una cuenta de Almacenamiento:

	azure account storage create [options] <StorageAccountName>


> [AZURE.NOTE]La cuenta de Almacenamiento debe colocarse con HDInsight en el mismo centro de datos.


Para obtener información sobre la creación de una cuenta de Almacenamiento de Azure a través del portal de Azure, consulte [Creación, administración o eliminación de una cuenta de Almacenamiento][azure-create-storageaccount].

Si ya tiene una cuenta de Almacenamiento pero no sabe su nombre ni su clave, puede usar los comandos siguientes para recuperar dicha información:

	-- Lists Storage accounts
	azure account storage list
	-- Shows a Storage account
	azure account storage show <StorageAccountName>
	-- Lists the keys for a Storage account
	azure account storage keys list <StorageAccountName>

Para obtener información sobre cómo obtener la información mediante el portal de Azure, vea la sección "Visualización, copia y regeneración de claves de acceso de almacenamiento" de [Creación, administración o eliminación de una cuenta de Almacenamiento][azure-create-storageaccount].


El comando **azure hdinsight cluster create** crea el contenedor si no existe. Si opta por crear el contenedor antes, puede usar el comando siguiente:

	azure storage container create --account-name <StorageAccountName> --account-key <StorageAccountKey> [ContainerName]

Una vez que tenga preparados la cuenta de Almacenamiento y el contenedor de blobs, podrá proceder con la creación de un clúster:

	azure hdinsight cluster create --clusterName <ClusterName> --storageAccountName <StorageAccountName> --storageAccountKey <storageAccountKey> --storageContainer <StorageContainer> --nodes <NumberOfNodes> --location <DataCenterLocation> --username <HDInsightClusterUsername> --clusterPassword <HDInsightClusterPassword>

![HDI.CLIClusterCreation][image-cli-clustercreation]

















##Aprovisionamiento de un clúster de HDInsight con un archivo de configuración
Normalmente, se aprovisiona un clúster de HDInsight, se ejecutan los trabajos en él y, a continuación, se elimina el clúster para reducir los costes. La interfaz de línea de comandos le da la opción de guardar las configuraciones en un archivo para que pueda volver a usarlas cada vez que aprovisione un clúster.

	azure hdinsight cluster config create <file>

	azure hdinsight cluster config set <file> --clusterName <ClusterName> --nodes <NumberOfNodes> --location "<DataCenterLocation>" --storageAccountName "<StorageAccountName>.blob.core.windows.net" --storageAccountKey "<StorageAccountKey>" --storageContainer "<BlobContainerName>" --username "<Username>" --clusterPassword "<UserPassword>"

	azure hdinsight cluster config storage add <file> --storageAccountName "<StorageAccountName>.blob.core.windows.net"
	       --storageAccountKey "<StorageAccountKey>"

	azure hdinsight cluster config metastore set <file> --type "hive" --server "<SQLDatabaseName>.database.windows.net"
	       --database "<HiveDatabaseName>" --user "<Username>" --metastorePassword "<UserPassword>"

	azure hdinsight cluster config metastore set <file> --type "oozie" --server "<SQLDatabaseName>.database.windows.net"
	       --database "<OozieDatabaseName>" --user "<SQLUsername>" --metastorePassword "<SQLPassword>"

	azure hdinsight cluster create --config <file>



![HDI.CLIClusterCreationConfig][image-cli-clustercreation-config]


##Enumeración y visualización de los detalles del clúster
Use los comandos siguientes para enumerar y mostrar los detalles del clúster:

	azure hdinsight cluster list
	azure hdinsight cluster show <ClusterName>

![HDI.CLIListCluster][image-cli-clusterlisting]


##Eliminación de un clúster
Use el comando siguiente para eliminar un clúster:

	azure hdinsight cluster delete <ClusterName>




##Pasos siguientes
En este artículo, ha aprendido a realizar diferentes tareas administrativas en clústeres de HDInsight. Para obtener más información, consulte los artículos siguientes:

* [Administración de HDInsight mediante el portal de Azure][hdinsight-admin-portal]
* [Administración de HDInsight con PowerShell de Azure][hdinsight-admin-powershell]
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
 

<!---HONumber=August15_HO6-->