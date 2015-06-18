<properties 
	pageTitle="Administración de clústeres de Hadoop mediante una interfaz de línea de comandos | Microsoft Azure" 
	description="Vea cómo usar la interfaz de línea de comandos multiplataforma para administrar clústeres de Hadoop en HDIsight en una plataforma que admita Node.js, incluidos Windows, Mac y Linux." 
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
	ms.date="03/31/2015" 
	ms.author="jgao"/>

# Administración de clústeres de Hadoop mediante la interfaz de la línea de comandos multiplataforma

Aprenda a usar la interfaz de la línea de comandos de Azure para Mac, Linux y Windows para administrar clústeres de Hadoop en HDInsight de Azure. La CLI de Azure se implementa en Node.js y se puede usar en cualquier plataforma compatible con Node.js, entre las que se incluyen Windows, Mac y Linux.

La CLI de Azure es código abierto. El código fuente se administra en GitHub en <a href= "https://github.com/WindowsAzure/azure-sdk-tools-xplat">https://github.com/WindowsAzure/azure-sdk-tools-xplat</a>.

Este artículo solo trata el uso de la interfaz de línea de comandos en Windows. Para obtener información general acerca de cómo usar la interfaz de la línea de comandos, consulte [Uso de las herramientas de línea de comandos de Azure para Mac y Linux][azure-command-line-tools].


##Requisitos previos

Antes de empezar este artículo, debe tener lo siguiente:

- **Suscripción de Azure**: Azure es una plataforma basada en suscripción. Para obtener más información acerca de cómo obtener una suscripción, consulte [Opciones de compra][azure-purchase-options], [Ofertas para miembros][azure-member-offers] o [Prueba gratuita][azure-free-trial].

##Instalación
La interfaz de línea de comandos se puede instalar usando *Node.js Package Manager (NPM)* o Windows Installer.

**Para instalar la interfaz de línea de comandos con NPM**

1.	Vaya a **www.nodejs.org**.
2.	Haga clic en **INSTALL** (INSTALAR) y siga las instrucciones usando la configuración predeterminada.
3.	Abra **Command Prompt** (Símbolo del sistema) (o **Símbolo del sistema de Azure** o **Símbolo del sistema para desarrolladores de VS2012**) desde su estación de trabajo.
4.	Ejecute el comando siguiente en la ventana del símbolo del sistema.

		npm install -g azure-cli

	> [AZURE.NOTE]Si recibe un error que indica que no se encuentra el comando NPM, compruebe que las rutas siguientes estén en la variable de entorno **PATH**: <i>C:\\Program Files (x86)\\nodejs;C:\\Users[username]\\AppData\\Roaming\\npm</i> or <i>C:\\Program Files\\nodejs;C:\\Users[username]\\AppData\\Roaming\\npm</i>


5.	Ejecute el siguiente comando para comprobar la instalación:

		azure hdinsight -h

	Puede usar el modificador **-h** en diferentes niveles para mostrar la información de ayuda. Por ejemplo:
		
		azure -h
		azure hdinsight -h
		azure hdinsight cluster -h
		azure hdinsight cluster create -h

**Para instalar la interfaz de línea de comandos con Windows Installer**

1.	Vaya a  **http://azure.microsoft.com/downloads/**. 
2.	Desplácese hasta la sección **Herramientas de línea de comandos** y, a continuación, haga clic en **Interfaz de la línea de comandos multiplataforma** y siga el asistente del instalador de plataforma web.

##Descarga e importación del archivo publishsettings de la cuenta de Azure

Antes de usar la interfaz de línea de comandos, debe configurar la conectividad entre su estación de trabajo y Azure. La interfaz de línea de comandos usa la información de su suscripción de Azure para conectarse a la cuenta. Esta información se puede obtener de Azure en un archivo publishsettings. El archivo publishsettings puede importarse después como un valor de configuración local permanente que la interfaz de línea de comandos usará para operaciones posteriores. Solo tiene que importar el archivo publishsettings una vez.

> [AZURE.NOTE]El archivo publishsettings contiene información confidencial. Se recomienda eliminar el archivo o tomar medidas adicionales para cifrar la carpeta del usuario que contiene el archivo. En Windows, modifique las propiedades de la carpeta o use el Cifrado de unidad BitLocker.


**Para descargar e importar el archivo publishsettings**

1.	Abra el símbolo del sistema.
2.	Ejecute el comando siguiente para descargar el archivo de configuración de publicación:

		azure account download
 
	![Interfaz de línea de comandos de descarga de la cuenta de Azure.][image-cli-account-download-import]

	El comando muestra las instrucciones para descargar el archivo, incluida una dirección URL.

3.	Abra Internet Explorer y vaya a la URL que se incluye en la ventana del símbolo del sistema.
4.	Haga clic en **Guardar** para guardar el archivo en la estación de trabajo.
5.	En la ventana del símbolo del sistema, ejecute el comando siguiente para importar el archivo publishsettings:

		azure account import <file>

	En la captura de pantalla anterior, el archivo publishsettings se guardó en la carpeta C:\\HDInsight de la estación de trabajo.


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
* [Uso de la CLI de Azure para Mac, Linux y Windows][azure-command-line-tools]


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

<!--HONumber=54--> 