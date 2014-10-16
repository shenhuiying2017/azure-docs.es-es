<properties linkid="manage-services-hdinsight-provision-hadoop-clusters" urlDisplayName="HDInsight Administration" pageTitle="Provision Hadoop clusters in HDInsight | Azure" metaKeywords="hdinsight, hdinsight administration, hdinsight administration azure" description="Learn how to provision clusters for Azure HDInsight using the management portal, PowerShell, or the command line." umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" services="hdinsight" documentationCenter="" title="Provision Hadoop clusters in HDInsight" authors="jgao" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jgao"></tags>

# Aprovisionamiento de clústeres de Hadoop en HDInsight usando opciones personalizadas

En este artículo aprenderá las diferentes formas de aprovisionar un clúster de Hadoop de forma personalizada en HDInsight de Azure usando el Portal de administración de Azure, PowerShell, herramientas de la línea de comandos o el SDK. NET de HDInsight. Este artículo trata sobre el aprovisionamiento de clústeres de Hadoop. Para obtener instrucciones sobre cómo aprovisionar un clúster de HBase, consulte [Aprovisionamiento de un clúster de HBase en HDInsight][]. Consulte [¿Cuál es la diferencia entre Hadoop y HBase?][] para comprender por qué debería elegir uno en lugar del otro.

## ¿Qué es un clústeres de HDInsight?

¿Se ha preguntado alguna vez por qué mencionamos clústeres cada vez que hablamos sobre Hadoop o BigData? El motivo es porque Hadoop permite el procesamiento distribuido de datos grandes y el despliegue a través de diferentes nodos de un clúster. El clúster tiene una arquitectura maestro/esclavo con un maestro (también llamado nodo principal o nodo de nombres) y cualquier número de esclavos (también llamado nodo de datos). Para obtener más información, consulte [Apache Hadoop][].

Un clúster de HDInsight abstrae los detalles de implementación de Hadoop de manera que no tiene que preocuparse sobre cómo comunicarse con diferentes nodos de un clúster. Cuando aprovisiona un clúster de Azure, aprovisiona cursos de proceso de Azure que contienen aplicaciones de Hadoop y aplicaciones relacionadas. Para obtener más información, consulte [Introducción a Hadoop en HDInsight][].

En este artículo se proporcionan instrucciones sobre diferentes formas de aprovisionar un clúster. Si busca un enfoque rápido de aprovisionar un clúster, consulte [Introducción a HDInsight de Azure][].

**Requisitos previos:**

Antes de empezar este artículo, debe tener lo siguiente:

-   Una suscripción de Azure. Azure es una plataforma basada en suscripción. Los cmdlets de HDInsight PowerShell realizan las tareas con su suscripción. Para obtener más información acerca de cómo obtener una suscripción, consulte [Opciones de compra][], [Ofertas para miembros][] o [Evaluación gratuita][].

## En este artículo

-   [Uso del Portal de administración de Azure][]
-   [Uso de Azure PowerShell][]
-   [Uso de la línea de comandos entre plataformas][]
-   [Uso del SDK .NET de HDInsight][]
-   [Pasos siguientes][]

## <span id="portal"></span></a> Uso del Portal de administración de Azure

Los clústeres de HDInsight usan contenedores de almacenamiento de blobs de Azure como sistemas de archivos predeterminados. Es preciso tener una cuenta de almacenamiento de Azure ubicada en el mismo centro de datos antes de crear un clúster de HDInsight. Para obtener más información, consulte [Uso del almacenamiento de blobs de Azure con HDInsight][]. Para obtener información acerca de la creación de una cuenta de almacenamiento de Azure, consulte [Creación de una cuenta de almacenamiento][].

> [WACOM.NOTE] Actualmente, solo las regiones **Asia oriental**, **Sudeste de Asia**, **Norte de Europa**, **Oeste de Europa**, **Este de EE. UU.**, **Oeste de EE. UU.**, **Centro y norte de EE. UU.**, **Centro y sur de EE. UU.** pueden hospedar clústeres de HDInsight.

**Para crear un clúster de HDInsight usando la opción de creación personalizada**

1.  Inicie sesión en el [Portal de administración de Azure][].
2.  Haga clic en **+ NEW** en la parte inferior de la página, después en **DATA SERVICES**, a continuación en **HDINSIGHT** y, finalmente, en **CUSTOM CREATE**.
3.  En la página **Detalles del clúster**, escriba o seleccione los valores siguientes:

    ![ClústerCreaciónPersonalizada.HDI][]

    <table border='1'>
		<tr><th>Propiedad</th><th>Valor</th></tr>
		<tr><td>Nombre del clúster</td>
			<td><p>Dé un nombre al clúster.</p>
				<ul>
				<li>El nombre DNS debe empezar y terminar por caracteres alfanuméricos y puede contener guiones.</li>
				<li>El campo debe ser una cadena con una longitud entre 3 y 63 caracteres.</li>
				</ul></td></tr>
		<tr><td>Tipo de clúster</td>
			<td>Para el tipo de clúster, seleccione <strong>Hadoop</strong>.</td></tr>
		<tr><td>Versión de HDInsight</td>
			<td>Seleccione la versión. Para Hadoop, la versión predeterminada la versión 3.1 de HDInsight, que usa Hadoop 2.4.</td></tr>
		</table>

    Escriba o seleccione los valores mostrados en la tabla y después haga clic en la flecha derecha.

4.  En la pestaña **Configurar clúster**, escriba o seleccione los valores siguientes:

	<table border="1">
	<tr><th>Nombre</th><th>Valor</th></tr>
	<tr><td>Nodos de datos</td><td>Número de nodos de datos que desea implementar. Para propósitos de prueba, cree un clúster de un solo nodo.<br />El límite del tamaño del clúster varía según las suscripciones a Azure. Póngase en contacto con el servicio de soporte relacionado con la facturación de Azure para aumentar el límite.</td></tr>
	<tr><td>Región/Red virtual</td><td>Seleccione la misma región que la cuenta de almacenamiento que creó en el último procedimiento. HDInsight requiere que la cuenta de almacenamiento se encuentre en la misma región. Posteriormente, en la configuración, puede seleccionar solo una cuenta de almacenamiento que se encuentre en la misma región que especificó aquí. Las regiones disponibles son las siguientes: <strong>Asia oriental</strong>, <strong>Sudeste de Asia</strong>, <strong>Norte de Europa</strong>, <strong>Oeste de Europa</strong>, <strong>Este de EE. UU.</strong>, <strong>Oeste de EE. UU.</strong>, <strong>Centro y norte de EE. UU.</strong> y <strong>Centro y sur de EE. UU..</strong></td></tr>
	</table>

5.  En la página **Configurar usuario de clúster**, proporcione los siguientes valores:

    ![HDI.CustomCreateCluster.ClusterUser][]

    <table border='1'>
		<tr><th>Propiedad</th><th>Valor</th></tr>
		<tr><td>Nombre de usuario</td>
			<td>Especifique el nombre del usuario del clúster de HDInsight.</td></tr>
		<tr><td>Contraseña/Confirmar contraseña</td>
			<td>Especifique la contraseña del usuario del clúster de HDInsight.</td></tr>
		<tr><td>Enter Hive/Oozie Metastore</td>
			<td>Active esta casilla para especificar la base de datos SQL que se encuentra en el mismo centro de datos que el clúster, que se usará como tienda de metadatos de Hive/Oozie. Esto resulta de gran utilidad si desea conservar los metadatos sobre trabajos de Hive/Oozie después de haber eliminado un clúster.</td></tr>
		<tr><td>Base de datos de tienda de metadatos</td>
			<td>Especifique la base de datos SQL de Azure que se usará como metastore para Hive/OOzie. Esta base de datos SQL debe estar en el mismo centro de datos que el clúster de HDInsight. El cuadro de lista solo incluye las bases de datos SQL del mismo centro de datos que ha especificado en la página <strong>Detalles del clúster</strong>.</td></tr>
		<tr><td>Usuario de base de datos</td>
			<td>Especifique el usuario de la base de datos SQL que se usará para conectarse a la base de datos.</td></tr>
		<tr><td>Contraseña de usuario de base de datos</td>
			<td>Especifique la contraseña del usuario de la base de datos SQL.</td></tr>
	</table>

    > [WACOM.NOTE] La base de datos SQL de Azure usada para la tienda de metadatos debe permitir conectividad con otros servicios de Azure, incluido HDInsight de Azure. En el panel de base de datos SQL de Azure, en el lado derecho, haga clic en el nombre de servidor. Este es el servidor en el que se ejecuta la instancia de base de datos SQL. Cuando se encuentre en la vista de servidor, haga clic en **Configurar** y después para **Servicios de Windows Azure**, haga clic en **Sí** y después en **Guardar**.

    Haga clic en la flecha derecha.

6.  Proporcione el siguiente valor en la página **Cuenta de almacenamiento**:

    ![HDI.CustomCreateCluster.StorageAccount][]

	<table border='1'>
		<tr><th>Propiedad</th><th>Valor</th></tr>
		<tr><td>Cuenta de almacenamiento</td>
			<td>Especifique la cuenta de almacenamiento de Azure que se usará como sistema de archivos predeterminado para el clúster de HDInsight. Puede elegir una de las tres opciones siguientes:
			<ul>
				<li>Usar almacenamiento existente</li>
				<li>Crear nuevo almacenamiento</li>
				<li>Utilizar almacenamiento de otra suscripción</li>
			</ul>
			</td></tr>
		<tr><td>Nombre de cuenta</td>
			<td><ul>
				<li>Si optó por usar el almacenamiento existente, seleccione una cuenta de almacenamiento existente para <strong>Nombre de cuenta</strong>. En la lista desplegable solamente aparecen las cuentas de almacenamiento ubicadas en el mismo centro de datos en el que eligió aprovisionar el clúster.</li>
				<li>Si eligió <strong>Crear nuevo almacenamiento</strong> o <strong>Usar almacenamiento de otra suscripción</strong>, debe proporcionar el nombre de cuenta de almacenamiento.</li>
			</ul></td></tr>
		<tr><td>Clave de cuenta</td>
			<td>Si eligió la opción <strong>Usar almacenamiento de otra suscripción</strong>, especifique la clave de cuenta para esa cuenta de almacenamiento.</td></tr>	
		<tr><td>Contenedor predeterminado</td>
			<td><p>Especifica el contenedor predeterminado de la cuenta de almacenamiento que se usará como sistema de archivos predeterminado para el clúster de HDInsight. Si eligió <strong>Usar almacenamiento existente</strong> para el campo <strong>Cuenta de almacenamiento</strong> y no existen contenedores en esa cuenta, el contenedor se creará de forma predeterminada con el mismo nombre que el del clúster. Si ya existe un contenedor con el nombre del clúster, se anexará un número de secuencia al nombre del contenedor. Por ejemplo, mycontainer1, mycontainer2 y así sucesivamente. Sin embargo, si la cuenta de almacenamiento existente tiene un contenedor con un nombre diferente al del clúster especificado, también puede usar ese contenedor.</p>
			<p>Si eligió crear un nuevo almacenamiento o usar un almacenamiento de otra suscripción de Azure, debe especificar el nombre predeterminado del contenedor.</p>
        </td></tr>
		<tr><td>Cuentas de almacenamiento adicionales</td>
			<td>HDInsight admite varias cuentas de almacenamiento. No hay límite en el número de cuentas de almacenamiento adicionales que un clúster puede usar. No obstante, si crea un clúster mediante el Portal de administración, tendrá un límite de siete debido a las restricciones de la interfaz de usuario. Por cada cuenta de almacenamiento adicional que especifique, se agregará una página Cuenta de almacenamiento adicional al asistente donde podrá especificar la información de la cuenta. Por ejemplo, en la captura de pantalla anterior, se selecciona una cuenta de almacenamiento adicional y, por tanto, se agrega la página 5 al diálogo.</td></tr>		
	</table>

    Haga clic en la flecha derecha.

7.  En la página **Cuenta de almacenamiento**, escriba la información de la cuenta de almacenamiento adicional:

    ![HDI.CustomCreateCluster.AddOnStorage][]

    De nuevo aquí, tiene la opción de elegir entre almacenamiento existente, crear nuevo almacenamiento o usar almacenamiento de otra suscripción de Azure. El procedimiento para proporcionar los valores es similar al paso anterior.

    Haga clic en la marca de verificación para iniciar el aprovisionamiento del clúster. Cuando el aprovisionamiento se complete, la columna de estado mostrará **En ejecución**.

    > [WACOM.NOTE] Una vez que haya seleccionado una cuenta de almacenamiento de Azure para su clúster de HDInsight, no podrá eliminar la cuenta, ni cambiarla por otra diferente.

## <span id="powershell"></span></a> Uso de Azure PowerShell

Azure PowerShell es un potente entorno de scripting que puede usar para controlar y automatizar la implementación y la administración de sus cargas de trabajo en Azure. Esta sección ofrece instrucciones acerca de cómo aprovisionar un clúster HDInsight. Para obtener información acerca de la configuración de una estación de trabajo para que ejecute cmdlets de HDInsight PowerShell, consulte [Instalación y configuración de Azure PowerShell][]. Para obtener más información acerca del uso de PowerShell con HDInsight, consulte [Administración de HDInsight con PowerShell][]. Para obtener más información acerca de los cmdlets de HDInsight PowerShell, consulte [Documentación de referencia de los cmdlets de HDInsight][].

Los siguientes procedimientos son necesarios para aprovisionar un clúster de HDInsight con PowerShell:

-   Creación de una cuenta de almacenamiento de Azure
-   Creación de un contenedor de blobs de Azure
-   Creación de un clúster de HDInsight

HDInsight utiliza contenedores de almacenamiento de blobs de Azure como sistemas de archivos predeterminados. Es preciso tener una cuenta de almacenamiento de Azure y un contenedor de almacenamiento antes de crear un clúster de HDInsight. Dicha cuenta debe ubicarse en el mismo centro de datos que el clúster de HDInsight.

**Para crear una cuenta de Almacenamiento de Azure**

-   Ejecute los comandos siguientes en la ventana de la consola de Azure PowerShell:

        $storageAccountName = "<StorageAcccountName>"   # Provide a storage account name 
        $location = "<MicrosoftDataCenter>"             # For example, "West US"

        # Create an Azure storage account
        New-AzureStorageAccount -StorageAccountName $storageAccountName -Location $location

    Si ya tiene una cuenta de almacenamiento pero no sabe su nombre ni su clave, puede usar los siguientes comandos de PowerShell para recuperar dicha información:

        # List storage accounts for the current subscription
        Get-AzureStorageAccount

        # List the keys for a storage account
        Get-AzureStorageKey "<StorageAccountName>"

**Para crear un contenedor de almacenamiento de blobs de Azure**

-   Ejecute los comandos siguientes en la ventana de la consola de Azure PowerShell:

        $storageAccountName = "<StorageAccountName>"    # Provide the storage account name 
        $storageAccountKey = "<StorageAccountKey>"      # Provide either primary or secondary key
        $containerName="<ContainerName>"                # Provide a container name

        # Create a storage context object
        $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName 
                                               -StorageAccountKey $storageAccountKey  

        # Create a Blob storage container
        New-AzureStorageContainer -Name $containerName -Context $destContext

Una vez que tenga preparados la cuenta de almacenamiento y el contenedor de blobs, podrá proceder con la creación de un clúster.

**Para aprovisionar un clúster de HDInsight**

-   Ejecute los comandos siguientes en la ventana de la consola de Azure PowerShell:

        $subscriptionName = "<SubscriptionName>"        # Name of the Azure subscription.
        $storageAccountName = "<StorageAccountName>"    # Azure storage account that hosts the default container. 
        $containerName = "<ContainerName>"              # Azure Blob container that is used as the default file system for the HDInsight cluster.

        $clusterName = "<HDInsightClusterName>"         # The name you will name your HDInsight cluster.
        $location = "<MicrosoftDataCenter>"             # The location of the HDInsight cluster. It must in the same data center as the storage account.
        $clusterNodes = <ClusterSizeInNodes>            # The number of nodes in the HDInsight cluster.

        # Get the storage primary key based on the account name
        Select-AzureSubscription $subscriptionName
        $storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }

        # Create a new HDInsight cluster
        New-AzureHDInsightCluster -Name $clusterName -Location $location -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainerName $containerName  -ClusterSizeInNodes $clusterNodes

    Cuando el sistema lo pida, escriba las credenciales para el clúster. El aprovisionamiento del clúster puede durar varios minutos en completarse.

    ![HDI.CLI.Provision][]

**Para aprovisionar un clúster de HDInsight usando opciones de configuración personalizada**

Durante el aprovisionamiento de un clúster, puede usar las otras opciones de configuración, como por ejemplo conectarse a más de un almacenamiento de blobs de Azure o usar una base de datos SQL de Azure para tiendas de metadatos de Hive y Oozie. Esto le permite separar la vigencia de sus datos y metadatos de la vigencia del clúster.

-   Ejecute los comandos siguientes en la ventana de Windows PowerShell:

        $subscriptionName = "<SubscriptionName>"
        $clusterName = "<ClusterName>"
        $location = "<MicrosoftDataCenter>"
        $clusterNodes = <ClusterSizeInNodes>

        $storageAccountName_Default = "<DefaultFileSystemStorageAccountName>"
        $containerName_Default = "<DefaultFileSystemContainerName>"

        $storageAccountName_Add1 = "<AdditionalStorageAccountName>"

        $hiveSQLDatabaseServerName = "<SQLDatabaseServerNameForHiveMetastore>"
        $hiveSQLDatabaseName = "<SQLDatabaseDatabaseNameForHiveMetastore>"
        $oozieSQLDatabaseServerName = "<SQLDatabaseServerNameForOozieMetastore>"
        $oozieSQLDatabaseName = "<SQLDatabaseDatabaseNameForOozieMetastore>"

        # Get the storage account keys
        Select-AzureSubscription $subscriptionName
        $storageAccountKey_Default = Get-AzureStorageKey $storageAccountName_Default | %{ $_.Primary }
        $storageAccountKey_Add1 = Get-AzureStorageKey $storageAccountName_Add1 | %{ $_.Primary }

        $oozieCreds = Get-Credential -Message "Oozie metastore"
        $hiveCreds = Get-Credential -Message "Hive metastore"

        # Create a Blob storage container
        $dest1Context = New-AzureStorageContext -StorageAccountName $storageAccountName_Default -StorageAccountKey $storageAccountKey_Default  
        New-AzureStorageContainer -Name $containerName_Default -Context $dest1Context

        # Create a new HDInsight cluster
        $config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes $clusterNodes |
            Set-AzureHDInsightDefaultStorage -StorageAccountName "$storageAccountName_Default.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Default -StorageContainerName $containerName_Default |
            Add-AzureHDInsightStorage -StorageAccountName "$storageAccountName_Add1.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Add1 |
            Add-AzureHDInsightMetastore -SqlAzureServerName "$hiveSQLDatabaseServerName.database.windows.net" -DatabaseName $hiveSQLDatabaseName -Credential $hiveCreds -MetastoreType HiveMetastore |
            Add-AzureHDInsightMetastore -SqlAzureServerName "$oozieSQLDatabaseServerName.database.windows.net" -DatabaseName $oozieSQLDatabaseName -Credential $oozieCreds -MetastoreType OozieMetastore |
                New-AzureHDInsightCluster -Name $clusterName -Location $location

    > [WACOM.NOTE] La base de datos SQL de Azure usada para la tienda de metadatos debe permitir conectividad con otros servicios de Azure, incluido HDInsight de Azure. En el panel de base de datos SQL de Azure, en el lado derecho, haga clic en el nombre de servidor. Este es el servidor en el que se ejecuta la instancia de base de datos SQL. Cuando se encuentre en la vista de servidor, haga clic en **Configurar** y después para **Servicios de Windows Azure**, haga clic en **Sí** y después en **Guardar**.

**Para enumerar los clústeres de HDInsight**

-   Ejecute los siguientes comandos desde una ventana de consola de Azure PowerShell para enumerar el clúster de HDInsight y comprobar que dicho clúster se aprovisionó correctamente:

        Get-AzureHDInsightCluster -Name <ClusterName>

## <span id="cli"></span></a> Uso de la línea de comandos entre plataformas

Otra opción para aprovisionar un clúster de HDInsight es la interfaz de línea de comandos entre plataformas. La herramienta de línea de comandos se implementa en Node.js y se puede usar en cualquier plataforma compatible con Node.js, entre las que se incluyen Windows, Mac y Linux. La herramienta de línea de comandos es de código abierto. El código fuente se administra en GitHub en <https://github.com/Azure/azure-sdk-tools-xplat>. Para obtener información general acerca de cómo usar la interfaz de la línea de comandos, consulte [Uso de las herramientas de línea de comandos de Azure para Mac y Linux][]. Para obtener una documentación de referencia completa, consulte [Herramienta de línea de comandos de Azure para Mac y Linux][]. Este artículo solo trata el uso de la interfaz de línea de comandos en Windows.

Los procedimientos siguientes son necesarios para aprovisionar un clúster de HDInsight con la línea de comandos entre varias plataformas:

-   Instalación de la línea de comandos entre varias plataformas
-   Descarga e importación de la configuración de publicación de la cuenta de Azure
-   Creación de una cuenta de almacenamiento de Azure
-   Aprovisionamiento de un clúster

La interfaz de línea de comandos se puede instalar usando *Node.js Package Manager (NPM)* o Windows Installer. Microsoft recomienda realizar la instalación usando solamente una de las dos opciones.

**Para instalar la interfaz de línea de comandos usando NPM**

1.  Vaya a **www.nodejs.org**.
2.  Haga clic en **INSTALL** y siga las instrucciones usando la configuración predeterminada.
3.  Abra **Command Prompt** (o bien *Azure Command Prompt* o *Símbolo del sistema para desarrolladores de VS2012*) desde su estación de trabajo.
4.  Ejecute el comando siguiente en la ventana del símbolo del sistema.

        npm install -g azure-cli

    > [WACOM.NOTE] Si aparece un error que indica que no se ha podido encontrar el comando NPM, compruebe que las rutas siguientes estén en la variable de entorno de PATH: *C:\\Archivos de programa (x86)\\nodejs;C:\\Users[nombreusuario]\\AppData\\Roaming\\npm* o *C:\\Archivos de programa\\nodejs;C:\\Users[nombreusuario]\\AppData\\Roaming\\npm*

5.  Ejecute el siguiente comando para comprobar la instalación:

        azure hdinsight -h

    Puede usar el modificador *-h* en diferentes niveles para mostrar la información de ayuda. Por ejemplo:

        azure -h
        azure hdinsight -h
        azure hdinsight cluster -h
        azure hdinsight cluster create -h

**Para instalar la interfaz de línea de comandos usando Windows Installer**

1.  Vaya a **[http://azure.microsoft.com/es-es/downloads/][]**.
2.  Desplácese hasta la sección **Command line tools** y, a continuación, haga clic en **Cross-platform Command Line Interface** y siga el asistente del instalador de plataforma web.

**Para descargar e importar la configuración de publicación**

Antes de usar la interfaz de línea de comandos, debe configurar la conectividad entre su estación de trabajo y Azure. La interfaz de línea de comandos usa la información de su suscripción de Azure para conectarse a la cuenta. Esta información se puede obtener de Azure en un archivo de configuración de publicación. El archivo de configuración de publicación puede importarse después como un valor de configuración local permanente que la interfaz de línea de comandos usará para operaciones posteriores. Solo tiene que importar la configuración de publicación una vez.

> [WACOM.NOTE] El archivo de configuración de publicación contiene información confidencial. Microsoft recomienda eliminar el archivo o tomar medidas adicionales para cifrar la carpeta del usuario que contiene el archivo. En Windows, modifique las propiedades de la carpeta o use BitLocker.

1.  Abra un **símbolo del sistema**.
2.  Ejecute el comando siguiente para descargar el archivo de configuración de publicación.

        azure account download

    ![HDI.CLIAccountDownloadImport][]

    El comando inicia la página web para descargar el archivo de configuración de publicación desde ella.

3.  En el símbolo del sistema para guardar el archivo, haga clic en **Guardar** y proporcione una ubicación en la que se debe guardar el archivo.
4.  En la ventana del símbolo del sistema, ejecute el comando siguiente para importar el archivo de configuración de publicación:

        azure account import <file>

    En la captura de pantalla anterior, el archivo de configuración de publicación se guardó en la carpeta C:\\HDInsight de la estación de trabajo.

**Para crear una cuenta de Almacenamiento de Azure**

HDInsight utiliza contenedores de almacenamiento de blobs de Azure como sistemas de archivos predeterminados. Es preciso tener una cuenta de almacenamiento de Azure antes de crear un clúster de HDInsight. La cuenta de almacenamiento debe ubicarse en el mismo centro de datos.

-   Desde la ventana del símbolo del sistema, ejecute el siguiente comando para crear una cuenta de almacenamiento de Azure:

        azure storage account create [options] <StorageAccountName>

    Cuando se le pida una ubicación, seleccione aquella en la que se puede aprovisionar el clúster de HDInsight. El almacenamiento debe encontrarse en la misma ubicación que el clúster de HDInsight. Actualmente, solo las regiones **Asia oriental**, **Sudeste de Asia**, **Norte de Europa**, **Oeste de Europa**, **Este de EE. UU.**, **Oeste de EE. UU.**, **Centro y norte de EE. UU.**, **Centro y sur de EE. UU.** pueden hospedar clústeres de HDInsight.

Para obtener información acerca de la creación de una cuenta de almacenamiento de Azure usando el Portal de administración de Azure, consulte [Creación de una cuenta de almacenamiento][].

Si ya tiene una cuenta de almacenamiento pero no sabe su nombre ni su clave, puede usar los comandos siguientes para recuperar dicha información:

    -- lists storage accounts
    azure storage account list

    -- Shows information for a storage account
    azure storage account show <StorageAccountName>

    -- Lists the keys for a storage account
    azure storage account keys list <StorageAccountName>

Para obtener información acerca de cómo conseguir la información usando el portal de administración, consulte la sección *Visualización, copia y regeneración de claves de acceso al almacenamiento* de [Administración de cuentas de almacenamiento][].

Un clúster de HDInsight también requiere un contenedor dentro de una cuenta de almacenamiento. Si la cuenta de almacenamiento que proporciona todavía no tiene un contenedor, el comando *azure hdinsight cluster create* le pide un nombre de contenedor y también lo crea. Sin embargo, si opta por crear el contenedor antes, puede usar el comando siguiente:

    azure storage container create --account-name <StorageAccountName> --account-key <StorageAccountKey> [ContainerName]
        

Una vez que tenga preparados la cuenta de almacenamiento y el contenedor de blobs, podrá proceder con la creación de un clúster.

**Para aprovisionar un clúster de HDInsight**

-   En la ventana del símbolo del sistema, ejecute el comando siguiente:

        azure hdinsight cluster create --clusterName <ClusterName> --storageAccountName "<StorageAccountName>.blob.core.windows.net" --storageAccountKey <storageAccountKey> --storageContainer <SorageContainerName> --nodes <NumberOfNodes> --location <DataCenterLocation> --username <HDInsightClusterUsername> --clusterPassword <HDInsightClusterPassword>

    ![HDI.CLIClusterCreation][]

**Para aprovisionar un clúster de HDInsight usando un archivo de configuración**

Normalmente, se aprovisiona un clúster de HDInsight, se ejecutan los trabajos y, a continuación, se elimina el clúster para reducir los costes. La interfaz de línea de comandos le da la opción de guardar las configuraciones en un archivo para que pueda volver a usarlas cada vez que aprovisione un clúster.

-   En la ventana del símbolo del sistema, ejecute los comandos siguientes:

        #Create the config file
        azure hdinsight cluster config create <file> 

        #Add commands to create a basic cluster
        azure hdinsight cluster config set <file> --clusterName <ClusterName> --nodes <NumberOfNodes> --location "<DataCenterLocation>" --storageAccountName "<StorageAccountName>.blob.core.windows.net" --storageAccountKey "<StorageAccountKey>" --storageContainer "<BlobContainerName>" --username "<Username>" --clusterPassword "<UserPassword>"

        #If requred, include commands to use additional blob storage with the cluster
        azure hdinsight cluster config storage add <file> --storageAccountName "<StorageAccountName>.blob.core.windows.net"
               --storageAccountKey "<StorageAccountKey>"

        #If required, include commands to use a SQL database as a Hive metastore
        azure hdinsight cluster config metastore set <file> --type "hive" --server "<SQLDatabaseName>.database.windows.net"
               --database "<HiveDatabaseName>" --user "<Username>" --metastorePassword "<UserPassword>"

        #If required, include commands to use a SQL database as an Oozie metastore 
        azure hdinsight cluster config metastore set <file> --type "oozie" --server "<SQLDatabaseName>.database.windows.net"
               --database "<OozieDatabaseName>" --user "<SQLUsername>" --metastorePassword "<SQLPassword>"

        #Run this command to create a cluster using the config file     
        azure hdinsight cluster create --config <file>

    > [WACOM.NOTE] La base de datos SQL de Azure usada para la tienda de metadatos debe permitir conectividad con otros servicios de Azure, incluido HDInsight de Azure. En el panel de base de datos SQL de Azure, en el lado derecho, haga clic en el nombre de servidor. Este es el servidor en el que se ejecuta la instancia de base de datos SQL. Cuando se encuentre en la vista de servidor, haga clic en **Configurar** y después para **Servicios de Windows Azure**, haga clic en **Sí** y después en **Guardar**.

    ![HDI.CLIClusterCreationConfig][]

**Para enumerar y mostrar los detalles del clúster**

-   Use los comandos siguientes para enumerar y mostrar los detalles del clúster:

        azure hdinsight cluster list
        azure hdinsight cluster show <ClusterName>

    ![HDI.CLIListCluster][]

**Para eliminar un clúster**

-   Use el comando siguiente para eliminar un clúster:

        azure hdinsight cluster delete <ClusterName>

## <span id="sdk"></span></a> Uso del SDK .NET de HDInsight

El SDK .NET de HDInsight proporciona bibliotecas de cliente .NET que facilitan el trabajo con HDInsight desde una aplicación .NET.

Los siguientes procedimientos se deben realizar para aprovisionar un clúster de HDInsight con el SDK:

-   Instalación del SDK .NET de HDInsight
-   Creación de un certificado autofirmado
-   Creación de una aplicación de consola
-   Ejecución de la aplicación

**Para instalar el SDK .NET de HDInsight**

Puede instalar la compilación publicada más reciente del SDK en [NuGet][]. Las instrucciones se mostrarán en el siguiente procedimiento.

**Para crear un certificado autofirmado**

1.  Cree el certificado autofirmado que se usa para autentica las solicitudes. Puede usar IIS o [makecert][] para crear el certificado.

2.  Busque la ubicación del certificado, haga clic con el botón secundario en él, después haga clic en **Instalar certificado** y, por último, instale el certificado en la tienda personal del equipo. Edite las propiedades del certificado para asignarle un nombre descriptivo.

3.  Importe el certificado en el Portal de administración de Azure. Desde el portal, haga clic en **Configuración** en la parte inferior izquierda de la página y después haga clic en **Certificados de administración**. En la parte inferior de la página, haga clic en **Cargar** y siga las instrucciones para cargar el archivo .cer que creó en el paso anterior.

    ![HDI.ClusterCreate.UploadCert][]

**Para crear una aplicación de consola de Visual Studio**

1.  Abra Visual Studio 2013.

2.  En el menú Archivo, haga clic en **Nuevo** y, a continuación, haga clic en **Proyecto**.

3.  En Nuevo proyecto, escriba o seleccione los siguientes valores:

	<table style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse;">
	<tr>
	<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">Propiedad</th>
	<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">Valor</th></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Category</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px; padding-right:5px;">Plantillas/Visual C#/Windows</td></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Template</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Aplicación de consola</td></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Nombre</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">CrearClústerHDI</td></tr>
	</table>

4.  Haga clic en **Aceptar** para crear el proyecto.

5.  En el menú **Herramientas**, haga clic en **Administrador de paquetes NuGet** y, a continuación, en **Consola del Administrador de paquetes**.

6.  Ejecute los siguientes comandos en la consola para instalar los paquetes.

        Install-Package Microsoft.WindowsAzure.Management.HDInsight

    Este comando agrega las bibliotecas y referencias .NET a los paquetes para el proyecto de Visual Studio actual.

7.  En el Explorador de soluciones, haga doble clic en **Program.cs** para abrirlo.

8.  Agregue la siguiente instrucción en la parte superior del archivo:

        using System.Security.Cryptography.X509Certificates;
        using Microsoft.WindowsAzure.Management.HDInsight;
        using Microsoft.WindowsAzure.Management.HDInsight.ClusterProvisioning;

9.  En la función Main(), copie y pegue el siguiente código:

        string certfriendlyname = "<CertificateFriendlyName>";     // Friendly name for the certificate your created earlier  
        string subscriptionid = "<AzureSubscriptionID>";
        string clustername = "<HDInsightClusterName>";
        string location = "<MicrosoftDataCenter>";
        string storageaccountname = "<AzureStorageAccountName>.blob.core.windows.net";
        string storageaccountkey = "<AzureStorageAccountKey>";
        string containername = "<HDInsightDefaultContainerName>";
        string username = "<HDInsightUsername>";
        string password = "<HDInsightUserPassword>";
        int clustersize = <NumberOfNodesInTheCluster>;

        // Get the certificate object from certificate store using the friendly name to identify it
        X509Store store = new X509Store();
        store.Open(OpenFlags.ReadOnly);
        X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.FriendlyName == certfriendlyname);

        // Create the storage account if it doesn't exist.

        // Create the container if it doesn't exist.

        // Create an HDInsightClient object
        HDInsightCertificateCredential creds = new HDInsightCertificateCredential(new Guid(subscriptionid), cert);
        var client = HDInsightClient.Connect(creds);

        // Supply th cluster information
        ClusterCreateParameters clusterInfo = new ClusterCreateParameters()
        {
            Name = clustername,
            Location = location,
            DefaultStorageAccountName = storageaccountname,
            DefaultStorageAccountKey = storageaccountkey,
            DefaultStorageContainer = containername,
            UserName = username,
            Password = password,
            ClusterSizeInNodes = clustersize
        };

        // Create the cluster
        Console.WriteLine("Creating the HDInsight cluster ...");

        ClusterDetails cluster = client.CreateCluster(clusterInfo);

        Console.WriteLine("Created cluster: {0}.", cluster.ConnectionUrl);
        Console.WriteLine("Press ENTER to continue.");
        Console.ReadKey();

10. Reemplace las variables al principio de la función main().

**Para ejecutar la aplicación**

Mientras la aplicación está abierta en Visual Studio, presione **F5** para ejecutarla. Una ventana de consola se abrirá y mostrará el estado de la aplicación. La creación del clúster de HDInsight puede durar varios minutos.

## <span id="nextsteps"></span></a>Pasos siguientes

En este artículo, ha aprendido a aprovisionar un clúster de HDInsight de varias formas. Para obtener más información, consulte los artículos siguientes:

-   [Introducción a HDInsight de Azure][]
-   [Administración de HDInsight con PowerShell][]
-   [Envío de trabajos de Hadoop mediante programación][]
-   [Azure HDInsight SDK documentation][]

  [Aprovisionamiento de un clúster de HBase en HDInsight]: http://azure.microsoft.com/es-es/documentation/articles/hdinsight-hbase-get-started/
  [¿Cuál es la diferencia entre Hadoop y HBase?]: http://go.microsoft.com/fwlink/?LinkId=510237
  [Apache Hadoop]: http://go.microsoft.com/fwlink/?LinkId=510084
  [Introducción a Hadoop en HDInsight]: ../hdinsight-introduction/
  [Introducción a HDInsight de Azure]: ../hdinsight-get-started/
  [Opciones de compra]: http://azure.microsoft.com/en-us/pricing/purchase-options/
  [Ofertas para miembros]: http://azure.microsoft.com/en-us/pricing/member-offers/
  [Evaluación gratuita]: http://azure.microsoft.com/en-us/pricing/free-trial/
  [Uso del Portal de administración de Azure]: #portal
  [Uso de Azure PowerShell]: #powershell
  [Uso de la línea de comandos entre plataformas]: #cli
  [Uso del SDK .NET de HDInsight]: #sdk
  [Pasos siguientes]: #nextsteps
  [Uso del almacenamiento de blobs de Azure con HDInsight]: ../hdinsight-use-blob-storage/
  [Creación de una cuenta de almacenamiento]: ../storage-create-storage-account/
  [Portal de administración de Azure]: https://manage.windowsazure.com/
  [ClústerCreaciónPersonalizada.HDI]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.png
  [HDI.CustomCreateCluster.ClusterUser]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.ClusterUser.png
  [HDI.CustomCreateCluster.StorageAccount]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.StorageAccount.png
  [HDI.CustomCreateCluster.AddOnStorage]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.AddOnStorage.png
  [Instalación y configuración de Azure PowerShell]: ../install-configure-powershell/
  [Administración de HDInsight con PowerShell]: ../hdinsight-administer-use-powershell/
  [Documentación de referencia de los cmdlets de HDInsight]: http://msdn.microsoft.com/en-us/library/windowsazure/dn479228.aspx
  [HDI.CLI.Provision]: ./media/hdinsight-provision-clusters/HDI.ps.provision.png
  [Uso de las herramientas de línea de comandos de Azure para Mac y Linux]: ../xplat-cli/
  [Herramienta de línea de comandos de Azure para Mac y Linux]: ../command-line-tools/
  [http://azure.microsoft.com/es-es/downloads/]: http://azure.microsoft.com/en-us/downloads/
  [HDI.CLIAccountDownloadImport]: ./media/hdinsight-provision-clusters/HDI.CLIAccountDownloadImport.png
  [Administración de cuentas de almacenamiento]: ../storage-manage-storage-account/
  [HDI.CLIClusterCreation]: ./media/hdinsight-provision-clusters/HDI.CLIClusterCreation.png
  [HDI.CLIClusterCreationConfig]: ./media/hdinsight-provision-clusters/HDI.CLIClusterCreationConfig.png
  [HDI.CLIListCluster]: ./media/hdinsight-provision-clusters/HDI.CLIListClusters.png "Enumeración y visualización de clústeres"
  [NuGet]: http://nuget.codeplex.com/wikipage?title=Getting%20Started
  [makecert]: http://msdn.microsoft.com/en-us/library/bfsktky3(v=vs.110).aspx
  [HDI.ClusterCreate.UploadCert]: ./media/hdinsight-get-started/HDI.ClusterCreate.UploadCert.png
  [Envío de trabajos de Hadoop mediante programación]: ../hdinsight-submit-hadoop-jobs-programmatically/
  [Azure HDInsight SDK documentation]: http://msdnstage.redmond.corp.microsoft.com/en-us/library/dn479185.aspx
