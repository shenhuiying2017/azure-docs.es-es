<properties linkid="manage-services-hdinsight-administer-hdinsight-hadoop-clusters-using-command-line" urlDisplayName="HDInsight Administration" pageTitle="Manage Hadoop clusters using Cross-Platform Command-Line | Azure" metaKeywords="hdinsight, hdinsight administration, hdinsight administration azure, hadoop, administration" description="Learn how to use the Cross-Platform Command-Line Interface to manage Hadoop clusters in HDIsight on any platform that supports Node.js, including Windows, Mac, and Linux." services="hdinsight" umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" title="Administer Hadoop clusters using the Cross-platform Command-line Interface" authors="jgao" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jgao" />

# Administración de clústeres de Hadoop mediante la interfaz de la línea de comandos entre plataformas

En este artículo, aprenderá a utilizar la interfaz de la línea de comandos entre plataformas para administrar clústeres de Hadoop en HDInsight. La herramienta de línea de comandos se implementa en Node.js y se puede usar en cualquier plataforma compatible con Node.js, entre las que se incluyen Windows, Mac y Linux.

La herramienta de línea de comandos es de código abierto. El código fuente se administra en GitHub en <https://github.com/WindowsAzure/azure-sdk-tools-xplat>.

Este artículo solo trata el uso de la interfaz de línea de comandos en Windows. Para obtener información general acerca de cómo usar la interfaz de la línea de comandos, consulte [Uso de las herramientas de línea de comandos de Azure para Mac y Linux][Uso de las herramientas de línea de comandos de Azure para Mac y Linux]. Para obtener una documentación de referencia completa, consulte [Herramienta de línea de comandos de Azure para Mac y Linux][Herramienta de línea de comandos de Azure para Mac y Linux].

**Requisitos previos:**

Antes de empezar este artículo, debe tener lo siguiente:

-   **Suscripción de Azure**. Azure es una plataforma basada en suscripción. Para obtener más información acerca de cómo obtener una suscripción, consulte [Opciones de compra][Opciones de compra], [Ofertas para miembros][Ofertas para miembros] o [Evaluación gratuita][Evaluación gratuita].

## En este artículo

-   [Instalación][Instalación]
-   [Descarga e importación de la configuración de publicación de la cuenta de Azure][Descarga e importación de la configuración de publicación de la cuenta de Azure]
-   [Aprovisionamiento de un clúster][Aprovisionamiento de un clúster]
-   [Aprovisionamiento de clústeres mediante un archivo de configuración][Aprovisionamiento de clústeres mediante un archivo de configuración]
-   [Enumeración y visualización de clústeres][Enumeración y visualización de clústeres]
-   [Eliminación de un clúster][Eliminación de un clúster]
-   [Pasos siguientes][Pasos siguientes]

## <span id="installation"></span></a> Instalación

La interfaz de línea de comandos se puede instalar usando *Node.js Package Manager (NPM)* o Windows Installer.

**Para instalar la interfaz de línea de comandos usando NPM**

1.  Vaya a **www.nodejs.org**.
2.  Haga clic en **INSTALL** y siga las instrucciones usando la configuración predeterminada.
3.  Abra **Command Prompt** (o bien *Azure Command Prompt* o *Símbolo del sistema para desarrolladores de VS2012*) desde su estación de trabajo.
4.  Ejecute el comando siguiente en la ventana del símbolo del sistema.

        npm install -g azure-cli

    > [WACOM.NOTE] Si aparece un error que indica que no se ha podido encontrar el comando NPM, compruebe que las rutas siguientes estén en la variable de entorno PATH: *C:\\Archivos de programa (x86)\\nodejs;C:\\Users[nombreusuario]\\AppData\\Roaming\\npm* o *C:\\Archivos de programa\\nodejs;C:\\Users[nombreusuario]\\AppData\\Roaming\\npm*

5.  Ejecute el siguiente comando para comprobar la instalación:

        azure hdinsight -h

    Puede usar el modificador *-h* en diferentes niveles para mostrar la información de ayuda. Por ejemplo:

        azure -h
        azure hdinsight -h
        azure hdinsight cluster -h
        azure hdinsight cluster create -h

**Para instalar la interfaz de línea de comandos usando Windows Installer**

1.  Vaya a **[http://azure.microsoft.com/es-es/downloads/][http://azure.microsoft.com/es-es/downloads/]**.
2.  Desplácese hasta la sección **Command line tools** y, a continuación, haga clic en **Cross-platform Command Line Interface** y siga el asistente del instalador de plataforma web.

## <span id="importsettings"></span></a> Descarga e importación de la configuración de publicación de la cuenta de Azure

Antes de usar la interfaz de línea de comandos, debe configurar la conectividad entre su estación de trabajo y Azure. La interfaz de línea de comandos usa la información de su suscripción de Azure para conectarse a la cuenta. Esta información se puede obtener de Azure en un archivo publishsettings. El archivo publishsettings puede importarse después como un valor de configuración local permanente que la interfaz de línea de comandos usará para operaciones posteriores. Solo tiene que importar la configuración de publicación una vez.

> [WACOM.NOTE] El archivo publishsettings contiene información confidencial. Se recomienda eliminar el archivo o tomar medidas adicionales para cifrar la carpeta del usuario que contiene el archivo. En Windows, modifique las propiedades de la carpeta o use BitLocker.

**Para descargar e importar la configuración de publicación**

1.  Abra un **símbolo del sistema**.
2.  Ejecute el comando siguiente para descargar el archivo publishsettings.

        azure account download

    ![HDI.CLIAccountDownloadImport][HDI.CLIAccountDownloadImport]

    El comando muestra las instrucciones para descargar el archivo, incluyendo una URL.

3.  Abra **Internet Explorer** y vaya a la URL que se incluye en la ventana del símbolo del sistema.
4.  Haga clic en **Save** para guardar el archivo en la estación de trabajo.
5.  En la ventana del símbolo del sistema, ejecute el comando siguiente para importar el archivo publishsettings:

        azure account import <file>

    En la captura de pantalla anterior, el archivo publishsettings se guardó en la carpeta C:\\HDInsight de la estación de trabajo.

## <span id="provision"></span></a>Aprovisionamiento de un clúster de HDInsight

HDInsight utiliza contenedores de almacenamiento de blobs de Azure como sistemas de archivos predeterminados. Es preciso tener una cuenta de almacenamiento de Azure antes de crear un clúster de HDInsight.

Después de importar el archivo publishsettings, puede usar el siguiente comando para crear una cuenta de almacenamiento:

    azure account storage create [options] <StorageAccountName>

> [WACOM.NOTE] La cuenta de almacenamiento debe ubicarse en el mismo centro de datos. En la actualidad, solo pueden aprovisionarse clústeres de HDInsight en los siguientes centros de datos:

> -   Sudeste asiático
> -   Europa del Norte
> -   Europa occidental
> -   Este de EE. UU.
> -   Oeste de EE. UU.

Para obtener información acerca de la creación de una cuenta de almacenamiento de Azure usando el Portal de administración de Azure, consulte [Creación de una cuenta de almacenamiento][Creación de una cuenta de almacenamiento].

Si ya tiene una cuenta de almacenamiento pero no sabe su nombre ni su clave, puede usar los comandos siguientes para recuperar dicha información:

    -- lists storage accounts
    azure account storage list
    -- Shows a storage account
    azure account storage show <StorageAccountName>
    -- Lists the keys for a storage account
    azure account storage keys list <StorageAccountName>

Para obtener información acerca de cómo conseguir la información usando el portal de administración, consulte la sección *Visualización, copia y regeneración de claves de acceso al almacenamiento* de [Administración de cuentas de almacenamiento][Administración de cuentas de almacenamiento].

El comando *azure hdinsight cluster create* crea el contenedor si no existe. Si opta por crear el contenedor antes, puede usar el comando siguiente:

    azure storage container create --account-name <StorageAccountName> --account-key <StorageAccountKey> [ContainerName]
        

Una vez que tenga preparados la cuenta de almacenamiento y el contenedor de blobs, podrá proceder con la creación de un clúster:

    azure hdinsight cluster create --clusterName <ClusterName> --storageAccountName <StorageAccountName> --storageAccountKey <storageAccountKey> --storageContainer <StorageContainer> --nodes <NumberOfNodes> --location <DataCenterLocation> --username <HDInsightClusterUsername> --clusterPassword <HDInsightClusterPassword>

![HDI.CLIClusterCreation][HDI.CLIClusterCreation]

## <span id="provisionconfigfile"></span></a> Aprovisionamiento de un clúster de HDInsight usando un archivo de configuración

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
         

![HDI.CLIClusterCreationConfig][HDI.CLIClusterCreationConfig]

## <span id="listshow"></span></a> Enumeración y visualización de los detalles del clúster

Use los comandos siguientes para enumerar y mostrar los detalles del clúster:

    azure hdinsight cluster list
    azure hdinsight cluster show <ClusterName>

![HDI.CLIListCluster][HDI.CLIListCluster]

## <span id="delete"></span></a> Eliminación de un clúster

Use el comando siguiente para eliminar un clúster:

    azure hdinsight cluster delete <ClusterName>

## <span id="nextsteps"></span></a>Pasos siguientes

En este artículo, ha aprendido a realizar diferentes tareas administrativas en clústeres de HDInsight. Para obtener más información, consulte los artículos siguientes:

-   [Administración de HDInsight con el portal de administración][Administración de HDInsight con el portal de administración]
-   [Administración de HDInsight con PowerShell][Administración de HDInsight con PowerShell]
-   [Introducción a HDInsight de Azure][Introducción a HDInsight de Azure]
-   [Uso de las herramientas de línea de comandos de Azure para Mac y Linux][Uso de las herramientas de línea de comandos de Azure para Mac y Linux]
-   [Herramienta de línea de comandos de Azure para Mac y Linux][Herramienta de línea de comandos de Azure para Mac y Linux]

  [Uso de las herramientas de línea de comandos de Azure para Mac y Linux]: ../xplat-cli/
  [Herramienta de línea de comandos de Azure para Mac y Linux]: ../command-line-tools/
  [Opciones de compra]: http://azure.microsoft.com/es-es/pricing/purchase-options/
  [Ofertas para miembros]: http://azure.microsoft.com/es-es/pricing/member-offers/
  [Evaluación gratuita]: http://azure.microsoft.com/es-es/pricing/free-trial/
  [Instalación]: #installation
  [Descarga e importación de la configuración de publicación de la cuenta de Azure]: #importsettings
  [Aprovisionamiento de un clúster]: #provision
  [Aprovisionamiento de clústeres mediante un archivo de configuración]: #provisionconfigfile
  [Enumeración y visualización de clústeres]: #listshow
  [Eliminación de un clúster]: #delete
  [Pasos siguientes]: #nextsteps
  [http://azure.microsoft.com/es-es/downloads/]: http://azure.microsoft.com/es-es/downloads/
  [HDI.CLIAccountDownloadImport]: ./media/hdinsight-administer-use-command-line/HDI.CLIAccountDownloadImport.png
  [Creación de una cuenta de almacenamiento]: ../storage-create-storage-account/
  [Administración de cuentas de almacenamiento]: ../storage-manage-storage-account/
  [HDI.CLIClusterCreation]: ./media/hdinsight-administer-use-command-line/HDI.CLIClusterCreation.png
  [HDI.CLIClusterCreationConfig]: ./media/hdinsight-administer-use-command-line/HDI.CLIClusterCreationConfig.png
  [HDI.CLIListCluster]: ./media/hdinsight-administer-use-command-line/HDI.CLIListClusters.png "Enumeración y visualización de clústeres"
  [Administración de HDInsight con el portal de administración]: ../hdinsight-administer-use-management-portal/
  [Administración de HDInsight con PowerShell]: ../hdinsight-administer-use-powershell/
  [Introducción a HDInsight de Azure]: ../hdinsight-get-started/
