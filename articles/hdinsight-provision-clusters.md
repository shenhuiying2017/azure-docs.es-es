<properties urlDisplayName="HDInsight Administration" pageTitle="Aprovisionamiento de clústeres de Hadoop en HDInsight | Azure" metaKeywords="hdinsight, hdinsight administration, hdinsight administration azure" description="Learn how to provision clusters for Azure HDInsight using the management portal, PowerShell, or the command line." umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" services="hdinsight" documentationCenter="" title="Provision Hadoop clusters in HDInsight" authors="jgao" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="11/14/2014" ms.author="jgao" />

#Aprovisionamiento de clústeres de Hadoop en HDInsight usando opciones personalizadas

En este artículo aprenderá las diferentes formas de aprovisionar un clúster de Hadoop de forma personalizada en HDInsight de Azure usando el Portal de administración de Azure, PowerShell, herramientas de la línea de comandos o el SDK. NET de HDInsight. Este artículo trata sobre el aprovisionamiento de clústeres de Hadoop. Para obtener instrucciones sobre cómo aprovisionar un clúster de HBase, consulte [Aprovisionamiento de un clúster de HBase en HDInsight][hdinsight-hbase-custom-provision]. Consulte <a href="http://go.microsoft.com/fwlink/?LinkId=510237">¿Cuál es la diferencia entre Hadoop y HBase?</a> para comprender lo que le puede llevar a elegir uno sobre otro.

## ¿Qué es un clústeres de HDInsight?

¿Se ha preguntado alguna vez por qué mencionamos clústeres cada vez que hablamos sobre Hadoop o BigData? El motivo es porque Hadoop permite el procesamiento distribuido de datos grandes y el despliegue a través de diferentes nodos de un clúster. El clúster tiene una arquitectura maestro/esclavo con un maestro (también llamado nodo principal o nodo de nombres) y cualquier número de esclavos (también llamado nodo de datos). Para obtener más información, consulte [Apache Hadoop][apache-hadoop].

![HDInsight Cluster][img-hdi-cluster]

Un clúster de HDInsight abstrae los detalles de implementación de Hadoop de manera que no tiene que preocuparse sobre cómo comunicarse con diferentes nodos de un clúster. Cuando aprovisiona un clúster de Azure, aprovisiona cursos de proceso de Azure que contienen aplicaciones de Hadoop y aplicaciones relacionadas. Para obtener más información, consulte [Introducción a Hadoop en HDInsight][hadoop-hdinsight-intro]. Los datos que se van a renovar se guardan en el almacenamiento de blobs de Azure, también denominado *Almacenamiento de Azure - Blob* (o WASB) en el contexto de HDInsight. Para obtener más información, consulte [Uso del almacenamiento de blobs de Azure con HDInsight][hdinsight-storage].

En este artículo se proporcionan instrucciones sobre diferentes formas de aprovisionar un clúster. Si busca un enfoque rápido de aprovisionar un clúster, consulte [Introducción a HDInsight de Azure][hdinsight-get-started].

**Requisitos previos:**

Antes de empezar este artículo, debe tener lo siguiente:

- Una suscripción de Azure. Azure es una plataforma basada en suscripción. Los cmdlets de HDInsight PowerShell realizan las tareas con su suscripción. Para obtener más información acerca de cómo obtener una suscripción, consulte [Opciones de compra][azure-purchase-options], [Ofertas para miembros][azure-member-offers] o [Evaluación gratuita][azure-free-trial].

##En este artículo

* [Opciones de configuración](#configuration)
* [Uso del Portal de administración de Azure](#portal)
* [Uso de Azure PowerShell](#powershell)
* [Uso de la línea de comandos entre plataformas](#cli)
* [Uso del SDK .NET de HDInsight](#sdk)
* [Pasos siguientes](#nextsteps)

##<a id="configuration"></a>Opciones de configuración

###Almacenamiento adicional

Durante la configuración, debe especificar una cuenta de almacenamiento de blobs de Azure y un contenedor predeterminado. El clúster usa este contenedor como ubicación de almacenamiento predeterminada. Si lo desea, puede especificar otros blobs que se asociarán también con el clúster.

Para obtener más información sobre el uso de almacenes de blobs secundarios, consulte [Uso del almacenamiento de blobs de Azure con HDInsight](http://azure.microsoft.com/en-us/documentation/articles/hdinsight-use-blob-storage/).

###Tienda de metadatos

La tienda de metadatos contiene información sobre las tablas, particiones, esquemas, columnas, etc. de Hive, que usa esta información para saber dónde están almacenados los datos en HDFS (o WASB para HDInsight). De forma predeterminada, Hive utiliza una base de datos integrada para almacenar esta información.

Cuando se aprovisiona un clúster de HDInsight, puede especificar una base de datos SQL que contendrá la tienda de metadatos para Hive. Esto permite conservar la información de metadatos cuando se elimina un clúster, porque se almacena externamente en Base de datos SQL.

### Personalización del clúster

Puede instalar componentes adicionales o personalizar la configuración del clúster mientras se crea un clúster de HDInsight. Los clústeres se pueden personalizar escribiendo scripts que se ejecutan durante la creación del clúster. Tales scripts se invocan mediante la opción de**acción de script**, que es una opción de configuración que se puede usar a partir de los cmdlets de PowerShell de HDInsight o el SDK de .NET para HDInsight. Para obtener más información, consulte [Personalización de un clúster de HDInsight mediante la acción de script][hdinsight-customize-cluster].


###Redes virtuales

[Red virtual de Azure](http://azure.microsoft.com/en-us/documentation/services/virtual-network/) permite crear una red segura y persistente que contenga los recursos que necesita para su solución. Una red virtual permite hacer lo siguiente:

* Conectar recursos en la nube en una red privada (solo en la nube)

	![diagram of cloud-only configuration](.\media\hdinsight-provision-clusters\cloud-only.png)

* Conectar sus recursos en la nube a la red de su centro de datos local (sitio a sitio o punto a sitio) usando una red privada virtual (VPN)

	La configuración sitio a sitio permite conectar varios recursos de su centro de datos a la Red virtual de Azure usando una VPN de hardware o el servicio de enrutamiento y acceso remoto.

	![diagram of site-to-site configuration](.\media\hdinsight-provision-clusters\site-to-site.png)

	La configuración punto a sitio permite conectar un recurso específico a la Red virtual de Azure usando una VPN de software

	![diagram of point-to-site configuration](.\media\hdinsight-provision-clusters\point-to-site.png)

Para obtener más información sobre las características, ventajas y funcionalidad de Red virtual, consulte [Información general sobre Red virtual de Azure](http://msdn.microsoft.com/library/azure/jj156007.aspx).

> [WACOM.NOTE] Debe crear la red virtual de Azure antes de aprovisionar un clúster de HDInsight. Para obtener más información, consulte [Tareas de configuración de Red virtual de Azure](http://msdn.microsoft.com/en-us/library/azure/jj156206.aspx).
>
> HDInsight de Azure solo admite redes virtuales basadas en la ubicación y actualmente no funciona con redes virtuales basadas en grupos de afinidad.
>
> Se recomienda encarecidamente designar una única subred para un clúster.

##<a id="portal"></a> Uso del Portal de administración de Azure

Los clústeres de HDInsight usan contenedores de almacenamiento de blobs de Azure como sistemas de archivos predeterminados. Es preciso tener una cuenta de almacenamiento de Azure ubicada en el mismo centro de datos antes de crear un clúster de HDInsight. Para obtener más información, consulte [Uso del almacenamiento de blobs de Azure con HDInsight][hdinsight-storage]. Para obtener información acerca de la creación de una cuenta de almacenamiento de Azure, consulte [Creación de una cuenta de almacenamiento][azure-create-storageaccount].


> [WACOM.NOTE] Actualmente, solo las regiones de **Asia Oriental**, **Sudeste asiático**, **Norte de Europa**, **Europa Occidental**, **Este de EE. UU.**, **Oeste de EE. UU.**, **Centro-Norte de EE. UU** y **Centro-Sur de EE. UU.** pueden hospedar clústeres de HDInsight.

**Para crear un clúster de HDInsight usando la opción de creación personalizada, siga estos pasos:**

1. Inicie sesión en el [Portal de administración de Azure][azure-management-portal].
2. Haga clic en **+ NUEVO** en la parte inferior de la página, haga clic en **SERVICIOS DE DATOS**, en **HDINSIGHT** y luego en **CREACIÓN PERSONALIZADA**.
3. En la página**Detalles del clúster**, escriba o elija los valores siguientes:

	![HDI.CustomCreateCluster][image-hdi-customcreatecluster]

    <table border='1'>
		<tr><th>Propiedad</th><th>Valor</th></tr>
		<tr><td>Nombre del clúster</td>
			<td><p>Dé un nombre al clúster. </p>
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

4. En la pestaña **Configurar clúster** escriba o seleccione los valores siguientes:

	<table border="1">
	<tr><th>Nombre</th><th>Valor</th></tr>
	<tr><td>Nodos de datos</td><td>Número de nodos de datos que desea implementar. Para propósitos de prueba, cree un clúster de un solo nodo. <br />El límite del tamaño del clúster varía según las suscripciones a Azure. Póngase en contacto con el servicio de soporte relacionado con la facturación de Azure para aumentar el límite.</td></tr>
	<tr><td>Región/Red virtual</td><td><p>Seleccione la misma región que la cuenta de almacenamiento que creó en el último procedimiento. HDInsight requiere que la cuenta de almacenamiento se encuentre en la misma región. Posteriormente, en la configuración, puede seleccionar solo una cuenta de almacenamiento que se encuentre en la misma región que especificó aquí.</p><p>Las regiones disponibles son las siguientes: <strong>Asia oriental</strong>, <strong>Sudeste asiático</strong>, <strong>Europa del Norte</strong>, <strong>Europa occidental</strong>, <strong>Este de EE. UU.</strong>, <strong>Oeste de EE. UU.</strong>, <strong>Centro-Norte de EE. UU</strong>, <strong>Centro-Sur de EE. UU</strong><br/>Si ha creado una red virtual de Azure, puede seleccionar la red que configurará para su uso por parte del clúster de HDInsight.</p><p>Para obtener más información sobre la creación de una red virtual de Azure, consulte <a href="http://msdn.microsoft.com/en-us/library/azure/jj156206.aspx">Tareas de configuración de Red virtual</a>.</p></td></tr>
	</table>


5. En la página **Configurar usuario de clúster**, proporcione los siguientes valores:

    ![HDI.CustomCreateCluster.ClusterUser][image-hdi-customcreatecluster-clusteruser]

    <table border='1'>
		<tr><th>Propiedad</th><th>Valor</th></tr>
		<tr><td>Nombre de usuario</td>
			<td>Especifique el nombre del usuario del clúster de HDInsight.</td></tr>
		<tr><td>Contraseña/Confirmar contraseña</td>
			<td>Especifique la contraseña del usuario del clúster de HDInsight.</td></tr>
		<tr><td>Enter Hive/Oozie Metastore</td>
			<td>Active esta casilla para especificar la base de datos SQL que se encuentra en el mismo centro de datos que el clúster, que se usará como tienda de metadatos de Hive/Oozie. Esto resulta de gran utilidad si desea conservar los metadatos sobre trabajos de Hive/Oozie después de haber eliminado un clúster.</td></tr>
		<tr><td>Base de datos de tienda de metadatos</td>
			<td>Especifique la base de datos SQL de Azure que se usará como metastore para Hive/OOzie. Esta base de datos SQL debe estar en el mismo centro de datos que el clúster de HDInsight. El cuadro de lista solo incluye las bases de datos SQL del mismo centro de datos que ha especificado <strong>en la página Detalles del clúster</strong> .</td></tr>
		<tr><td>Usuario de base de datos</td>
			<td>Especifique el usuario de la base de datos SQL que se usará para conectarse a la base de datos.</td></tr>
		<tr><td>Contraseña de usuario de base de datos</td>
			<td>Especifique la contraseña del usuario de la base de datos SQL.</td></tr>
	</table>

	>[WACOM.NOTE] La base de datos SQL de Azure usada para la tienda de metadatos debe permitir la conectividad con otros servicios de Azure, incluido HDInsight de Azure. En el panel de base de datos SQL de Azure, en el lado derecho, haga clic en el nombre de servidor. Este es el servidor en el que se ejecuta la instancia de base de datos SQL. Cuando se encuentre en la vista de servidor, haga clic en **Configurar** y luego en **Servicios de Windows Azure**, haga clic en **Sí** y luego en **Guardar**.

    Haga clic en la flecha derecha.


6. Proporcione el siguiente valor en la página **Cuenta de almacenamiento**:

    ![HDI.CustomCreateCluster.StorageAccount][image-hdi-customcreatecluster-storageaccount]

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
				<li>Si ha elegido usar el almacenamiento existente, en <strong>Nombre de cuenta</strong>, seleccione una cuenta de almacenamiento existente. En la lista desplegable solamente aparecen las cuentas de almacenamiento ubicadas en el mismo centro de datos en el que eligió aprovisionar el clúster.</li>
				<li>Si ha elegido la opción <strong>Crear nuevo almacenamiento</strong> o <strong>Utilizar almacenamiento de otra suscripción</strong> , debe proporcionar el nombre de la cuenta de almacenamiento.</li>
			</ul></td></tr>
		<tr><td>Clave de cuenta</td>
			<td>Si ha elegido la opción <strong>Utilizar almacenamiento de otra suscripción</strong> , especifique la clave de cuenta de esa cuenta de almacenamiento.</td></tr>
		<tr><td>Contenedor predeterminado</td>
			<td><p>Especifica el contenedor predeterminado de la cuenta de almacenamiento que se usará como sistema de archivos predeterminado para el clúster de HDInsight. Si ha elegido la opción <strong>Usar almacenamiento existente</strong> en el campo <strong>Cuenta de almacenamiento</strong>  y no existen contenedores en esa cuenta, el contenedor se creará de forma predeterminada con el mismo nombre que el del clúster. Si ya existe un contenedor con el nombre del clúster, se anexará un número de secuencia al nombre del contenedor. Por ejemplo, mycontainer1, mycontainer2 y así sucesivamente. Sin embargo, si la cuenta de almacenamiento existente tiene un contenedor con un nombre diferente al del clúster especificado, también puede usar ese contenedor.</p>
            <p>Si eligió crear un nuevo almacenamiento o usar un almacenamiento de otra suscripción de Azure, debe especificar el nombre predeterminado del contenedor.</p>
        </td></tr>
		<tr><td>Cuentas de almacenamiento adicionales</td>
			<td>HDInsight admite varias cuentas de almacenamiento. No hay límite en el número de cuentas de almacenamiento adicionales que un clúster puede usar. No obstante, si crea un clúster mediante el Portal de administración, tendrá un límite de siete debido a las restricciones de la interfaz de usuario. Por cada cuenta de almacenamiento adicional que especifique, se agregará una página Cuenta de almacenamiento adicional al asistente donde podrá especificar la información de la cuenta. Por ejemplo, en la captura de pantalla anterior, se selecciona una cuenta de almacenamiento adicional y, por tanto, se agrega la página 5 al diálogo.</td></tr>
	</table>

	Haga clic en la flecha derecha.

7. En la página **Cuenta de almacenamiento**, escriba la información de la cuenta de almacenamiento adicional:

	![HDI.CustomCreateCluster.AddOnStorage][image-hdi-customcreatecluster-addonstorage]

    De nuevo aquí, tiene la opción de elegir entre almacenamiento existente, crear nuevo almacenamiento o usar almacenamiento de otra suscripción de Azure. El procedimiento para proporcionar los valores es similar al paso anterior.

    Haga clic en la marca de verificación para iniciar el aprovisionamiento del clúster. Cuando el aprovisionamiento se complete, la columna de estado mostrará **En ejecución**.

	> [WACOM.NOTE] Una vez que haya seleccionado una cuenta de almacenamiento de Azure para su clúster de HDInsight, no podrá eliminar la cuenta, ni cambiarla por otra diferente.

##<a id="powershell"></a> Uso de Azure PowerShell
Azure PowerShell es un potente entorno de scripting que puede usar para controlar y automatizar la implementación y la administración de sus cargas de trabajo en Azure. Esta sección ofrece instrucciones acerca de cómo aprovisionar un clúster HDInsight. Para obtener información acerca de la configuración de una estación de trabajo para que ejecute cmdlets de HDInsight PowerShell, consulte [Instalación y configuración de Azure PowerShell][powershell-install-configure]. Para obtener más información acerca del uso de PowerShell con HDInsight, consulte [Administración de HDInsight mediante PowerShell][hdinsight-admin-powershell]. Para obtener más información acerca de los cmdlets de HDInsight PowerShell, consulte [Referencia de los cmdlets de HDInsight][hdinsight-powershell-reference].

> [WACOM.NOTE] Si bien los scripts de esta sección se pueden usar para configurar un clúster de HDInsight en una red virtual de Azure, no crearán una red virtual de Azure. Para obtener información sobre la creación de una red virtual de Azure, consulte [Tareas de configuración de una red virtual](http://msdn.microsoft.com/en-us/library/azure/jj156206.aspx).

Los siguientes procedimientos son necesarios para aprovisionar un clúster de HDInsight con PowerShell:

- Creación de una cuenta de almacenamiento de Azure
- Creación de un contenedor de blobs de Azure
- Creación de un clúster de HDInsight

HDInsight utiliza contenedores de almacenamiento de blobs de Azure como sistemas de archivos predeterminados. Es preciso tener una cuenta de almacenamiento de Azure y un contenedor de almacenamiento antes de crear un clúster de HDInsight. Dicha cuenta debe ubicarse en el mismo centro de datos que el clúster de HDInsight.


**Para crear una cuenta de almacenamiento de Azure, siga estos pasos:**

- Ejecute los comandos siguientes en la ventana de la consola de Azure PowerShell:

		$storageAccountName = "<StorageAcccountName>"	# Provide a storage account name
		$location = "<MicrosoftDataCenter>"				# For example, "West US"

		# Create an Azure storage account
		New-AzureStorageAccount -StorageAccountName $storageAccountName -Location $location

	Si ya tiene una cuenta de almacenamiento pero no sabe su nombre ni su clave, puede usar los siguientes comandos de PowerShell para recuperar dicha información:

		# List storage accounts for the current subscription
		Get-AzureStorageAccount

		# List the keys for a storage account
		Get-AzureStorageKey "<StorageAccountName>"

**Para crear un contenedor de almacenamiento de blobs de Azure, siga estos pasos:**

- Ejecute los comandos siguientes en la ventana de la consola de Azure PowerShell:

		$storageAccountName = "<StorageAccountName>"	# Provide the storage account name
		$storageAccountKey = "<StorageAccountKey>"		# Provide either primary or secondary key
		$containerName="<ContainerName>"				# Provide a container name

		# Create a storage context object
		$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName
		                                       -StorageAccountKey $storageAccountKey  

		# Create a Blob storage container
		New-AzureStorageContainer -Name $containerName -Context $destContext

Una vez que tenga preparados la cuenta de almacenamiento y el contenedor de blobs, podrá proceder con la creación de un clúster.

**Para aprovisionar un clúster de HDInsight, siga estos pasos:**

> [WACOM.NOTE] Los cmdlets de PowerShell son la única forma recomendada para cambiar variables de configuración en un clúster de HDInsight.  Los cambios que se realicen en los archivos de configuración de Hadoop mientras esté conectado al clúster a través de Escritorio remoto se pueden sobrescribir si se aplica una revisión al clúster.  Los valores de configuración establecidos con PowerShell sí se mantienen si se aplica una revisión al clúster.

- Ejecute los comandos siguientes en la ventana de la consola de Azure PowerShell:

		$subscriptionName = "<SubscriptionName>"		# Name of the Azure subscription.
		$storageAccountName = "<StorageAccountName>"	# Azure storage account that hosts the default container.
		$containerName = "<ContainerName>"				# Azure Blob container that is used as the default file system for the HDInsight cluster.

		$clusterName = "<HDInsightClusterName>"			# The name you will name your HDInsight cluster.
		$location = "<MicrosoftDataCenter>"				# The location of the HDInsight cluster. It must in the same data center as the storage account.
		$clusterNodes = <ClusterSizeInNodes>			# The number of nodes in the HDInsight cluster.

		# Get the storage primary key based on the account name
		Select-AzureSubscription $subscriptionName
		$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }

		# Create a new HDInsight cluster
		New-AzureHDInsightCluster -Name $clusterName -Location $location -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainerName $containerName  -ClusterSizeInNodes $clusterNodes

	Cuando el sistema lo pida, escriba las credenciales para el clúster. El aprovisionamiento del clúster puede durar varios minutos en completarse.

	![HDI.CLI.Provision][image-hdi-ps-provision]



**Para aprovisionar un clúster de HDInsight usando opciones de configuración personalizada, siga estos pasos:**

Durante el aprovisionamiento de un clúster, puede usar las otras opciones de configuración; por ejemplo, conectarse a más de un almacenamiento de blobs de Azure, usar una red virtual o utilizar una base de datos SQL de Azure para tiendas de metadatos de Hive y Oozie. Esto le permite separar la vigencia de sus datos y metadatos de la vigencia del clúster.

> [WACOM.NOTE] Los cmdlets de PowerShell son la única forma recomendada para cambiar variables de configuración en un clúster de HDInsight.  Los cambios que se realicen en los archivos de configuración de Hadoop mientras esté conectado al clúster a través de Escritorio remoto se pueden sobrescribir si se aplica una revisión al clúster.  Los valores de configuración establecidos con PowerShell sí se mantienen si se aplica una revisión al clúster.

- Ejecute los comandos siguientes en la ventana de Windows PowerShell:

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

		# Get the virtual network ID and subnet name
		$vnetID = "<AzureVirtualNetworkID>"
		$subNetName = "<AzureVirtualNetworkSubNetName>"

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
		        New-AzureHDInsightCluster -Name $clusterName -Location $location -VirtualNetworkId $vnetID -SubnetName $subNetName

	>[WACOM.NOTE] La base de datos SQL de Azure usada para la tienda de metadatos debe permitir la conectividad con otros servicios de Azure, incluido HDInsight de Azure. En el panel de base de datos SQL de Azure, en el lado derecho, haga clic en el nombre de servidor. Este es el servidor en el que se ejecuta la instancia de base de datos SQL. Cuando se encuentre en la vista de servidor, haga clic en **Configurar** y luego en **Servicios de Windows Azure**, haga clic en **Sí** y luego en **Guardar**.

**Para enumerar los clústeres de HDInsight, siga estos pasos:**

- Ejecute los siguientes comandos desde una ventana de consola de Azure PowerShell para enumerar el clúster de HDInsight y comprobar que dicho clúster se aprovisionó correctamente:

		Get-AzureHDInsightCluster -Name <ClusterName>


##<a id="cli"></a> Uso de la línea de comandos entre plataformas

> [WACOM.NOTE] Desde el 29/8/2014, no se puede usar la interfaz de línea de comandos multiplataforma para asociar un clúster con una red virtual de Azure.

Otra opción para aprovisionar un clúster de HDInsight es la interfaz de línea de comandos entre plataformas. La herramienta de línea de comandos se implementa en Node.js, y se puede usar en cualquier plataforma compatible con Node.js, entre las que se incluyen Windows, Mac y Linux. La herramienta de línea de comandos es de código abierto.  El código fuente se administra en GitHub en <a href= "https://github.com/Azure/azure-sdk-tools-xplat">https://github.com/Azure/azure-sdk-tools-xplat</a>. Para obtener información general acerca de cómo usar la interfaz de la línea de comandos, consulte [Uso de las herramientas de línea de comandos de Azure para Mac y Linux][azure-command-line-tools]. Para obtener una documentación de referencia completa, consulte [Herramienta de línea de comandos de Azure para Mac y Linux][azure-command-line-tool]. Este artículo solo trata el uso de la interfaz de línea de comandos en Windows.

Los procedimientos siguientes son necesarios para aprovisionar un clúster de HDInsight con la línea de comandos entre varias plataformas:

- Instalación de la línea de comandos entre varias plataformas
- Descarga e importación de la configuración de publicación de la cuenta de Azure
- Creación de una cuenta de almacenamiento de Azure
- Aprovisionamiento de un clúster

La interfaz de línea de comandos se puede instalar usando *Node.js Package Manager (NPM)* o Windows Installer. Microsoft recomienda realizar la instalación usando solamente una de las dos opciones.

**Para instalar la interfaz de línea de comandos usando NPM, siga estos pasos:**

1.	Vaya a **www.nodejs.org**.
2.	Haga clic en **INSTALAR** y siga las instrucciones usando la configuración predeterminada.
3.	Abra **Command Prompt** (Símbolo del sistema) (o *símbolo del sistema de Azure*, o *símbolo del sistema de desarrollador para VS2012*) en la estación de trabajo.
4.	Ejecute el comando siguiente en la ventana del símbolo del sistema.

		npm install -g azure-cli

	> [WACOM.NOTE] Si aparece un error que indica que no se ha podido encontrar el comando NPM, compruebe que las rutas siguientes estén en la variable de entorno de PATH: <i>C:\Program Files (x86)\nodejs;C:\Users\[username]\AppData\Roaming\npm</i> o <i>C:\Program Files\nodejs;C:\Users\[username]\AppData\Roaming\npm</i>

5.	Ejecute el siguiente comando para comprobar la instalación:

		azure hdinsight -h

	Puede usar el modificador *-h* en diferentes niveles para mostrar la información de ayuda. Por ejemplo:

		azure -h
		azure hdinsight -h
		azure hdinsight cluster -h
		azure hdinsight cluster create -h

**Para instalar la interfaz de línea de comandos usando Windows Installer, siga estos pasos:**

1.	Vaya a **http://azure.microsoft.com/en-us/downloads/**.
2.	Desplácese hasta la sección **Command line tools** (Herramientas de línea de comandos) y luego haga clic en **Cross-platform Command Line Interface** (Interfaz de línea de comandos multiplataforma) y siga el asistente del instalador de plataforma web.

**Para descargar e importar la configuración de publicación, siga estos pasos:**

Antes de usar la interfaz de línea de comandos, debe configurar la conectividad entre su estación de trabajo y Azure. La interfaz de línea de comandos usa la información de su suscripción de Azure para conectarse a la cuenta. Esta información se puede obtener de Azure en un archivo de configuración de publicación. El archivo de configuración de publicación puede importarse después como un valor de configuración local permanente que la interfaz de línea de comandos usará para operaciones posteriores. Solo tiene que importar la configuración de publicación una vez.

> [WACOM.NOTE] El archivo de configuración de publicación contiene información confidencial. Microsoft recomienda eliminar el archivo o tomar medidas adicionales para cifrar la carpeta del usuario que contiene el archivo. En Windows, modifique las propiedades de la carpeta o use BitLocker.


1.	Abra un **símbolo del sistema**.
2.	Ejecute el comando siguiente para descargar el archivo de configuración de publicación.

		azure account download

	![HDI.CLIAccountDownloadImport][image-cli-account-download-import]

	El comando inicia la página web para descargar el archivo de configuración de publicación desde ella.

3.	En el símbolo del sistema para guardar el archivo, haga clic en **Guardar** y proporcione una ubicación en la que se debe guardar el archivo.
5.	En la ventana del símbolo del sistema, ejecute el comando siguiente para importar el archivo de configuración de publicación:

		azure account import <file>

	En la captura de pantalla anterior, el archivo de configuración de publicación se guardó en la carpeta C:\HDInsight de la estación de trabajo.

**Para crear una cuenta de almacenamiento de Azure, siga estos pasos:**

HDInsight utiliza contenedores de almacenamiento de blobs de Azure como sistemas de archivos predeterminados. Es preciso tener una cuenta de almacenamiento de Azure antes de crear un clúster de HDInsight. La cuenta de almacenamiento debe ubicarse en el mismo centro de datos.

- Desde la ventana del símbolo del sistema, ejecute el siguiente comando para crear una cuenta de almacenamiento de Azure:

		azure storage account create [options] <StorageAccountName>

	Cuando se le pida una ubicación, seleccione aquella en la que se puede aprovisionar el clúster de HDInsight. El almacenamiento debe encontrarse en la misma ubicación que el clúster de HDInsight. Actualmente, solo las regiones de **Asia Oriental**, **Sudeste asiático**, **Norte de Europa**, **Europa Occidental**, **Este de EE. UU.**, **Oeste de EE. UU.**, **Centro-Norte de EE. UU** y **Centro-Sur de EE. UU.** pueden hospedar clústeres de HDInsight.  

Para obtener información acerca de la creación de una cuenta de almacenamiento de Azure usando el Portal de administración de Azure, consulte [Creación, administración o eliminación de una cuenta de almacenamiento][azure-create-storageaccount].

Si ya tiene una cuenta de almacenamiento pero no sabe su nombre ni su clave, puede usar los comandos siguientes para recuperar dicha información:

	-- lists storage accounts
	azure storage account list

	-- Shows information for a storage account
	azure storage account show <StorageAccountName>

	-- Lists the keys for a storage account
	azure storage account keys list <StorageAccountName>

Para obtener información acerca de cómo conseguir la información usando el portal de administración, consulte la sección *Visualización, copia y generación de claves de acceso al almacenamiento* de [Creación, administración o eliminación de una cuenta de almacenamiento][azure-create-storageaccount].

Un clúster de HDInsight también requiere un contenedor dentro de una cuenta de almacenamiento. Si la cuenta de almacenamiento que proporciona todavía no tiene un contenedor, el comando *azure hdinsight cluster create* le pide un nombre de contenedor y también lo crea. Sin embargo, si opta por crear el contenedor antes, puede usar el comando siguiente:

	azure storage container create --account-name <StorageAccountName> --account-key <StorageAccountKey> [ContainerName]

Una vez que tenga preparados la cuenta de almacenamiento y el contenedor de blobs, podrá proceder con la creación de un clúster.

**Para aprovisionar un clúster de HDInsight, siga estos pasos:**

- En la ventana del símbolo del sistema, ejecute el comando siguiente:

		azure hdinsight cluster create --clusterName <ClusterName> --storageAccountName "<StorageAccountName>.blob.core.windows.net" --storageAccountKey <storageAccountKey> --storageContainer <SorageContainerName> --nodes <NumberOfNodes> --location <DataCenterLocation> --username <HDInsightClusterUsername> --clusterPassword <HDInsightClusterPassword>

	![HDI.CLIClusterCreation][image-cli-clustercreation]


**Para aprovisionar un clúster de HDInsight usando un archivo de configuración, siga estos pasos:**

Normalmente, se aprovisiona un clúster de HDInsight, se ejecutan los trabajos y, a continuación, se elimina el clúster para reducir los costes. La interfaz de línea de comandos le da la opción de guardar las configuraciones en un archivo para que pueda volver a usarlas cada vez que aprovisione un clúster.

- En la ventana del símbolo del sistema, ejecute los comandos siguientes:


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

	>[WACOM.NOTE] La base de datos SQL de Azure usada para la tienda de metadatos debe permitir la conectividad con otros servicios de Azure, incluido HDInsight de Azure. En el panel de base de datos SQL de Azure, en el lado derecho, haga clic en el nombre de servidor. Este es el servidor en el que se ejecuta la instancia de base de datos SQL. Cuando se encuentre en la vista de servidor, haga clic en **Configurar** y luego en **Servicios de Windows Azure**, haga clic en **Sí** y luego en **Guardar**.


	![HDI.CLIClusterCreationConfig][image-cli-clustercreation-config]


**Para enumerar y mostrar los detalles del clúster, siga estos pasos:**

- Use los comandos siguientes para enumerar y mostrar los detalles del clúster:

		azure hdinsight cluster list
		azure hdinsight cluster show <ClusterName>

	![HDI.CLIListCluster][image-cli-clusterlisting]


**Para eliminar un clúster, siga estos pasos:**

- Use el comando siguiente para eliminar un clúster:

		azure hdinsight cluster delete <ClusterName>



##<a id="sdk"></a> Uso del SDK .NET de HDInsight
El SDK .NET de HDInsight proporciona bibliotecas de cliente .NET que facilitan el trabajo con HDInsight desde una aplicación .NET.

Los siguientes procedimientos se deben realizar para aprovisionar un clúster de HDInsight con el SDK:

- Instalación del SDK .NET de HDInsight
- Creación de un certificado autofirmado
- Creación de una aplicación de consola
- Ejecución de la aplicación


**Para instalar el SDK .NET de HDInsight, siga estos pasos:**

Puede instalar la compilación publicada más reciente del SDK en [NuGet](http://nuget.codeplex.com/wikipage?title=Getting%20Started). Las instrucciones se mostrarán en el siguiente procedimiento.

**Para crear un certificado autofirmado, siga estos pasos:**

Cree un certificado autofirmado, instálelo en su estación de trabajo y cárguelo en su suscripción de Azure. Para obtener instrucciones, consulte [Creación de un certificado autofirmado](http://go.microsoft.com/fwlink/?LinkId=511138).


**Para crear una aplicación de consola de Visual Studio, siga estos pasos:**

1. Abra Visual Studio 2013.

2. En el menú Archivo, haga clic en **Nuevo** y luego en **Proyecto**.

3. En Nuevo proyecto, escriba o seleccione los siguientes valores:

	<table style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse;">
	<tr>
	<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">Property</th>
	<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">Value</th></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Category</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px; padding-right:5px;">Templates/Visual C#/Windows</td></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Template</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Console Application</td></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Name</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">CreateHDICluster</td></tr>
	</table>

4. Haga clic en **Aceptar** para crear el proyecto.

5. En el menú **Herramientas**, haga clic en **Administrador de paquetes Nuget** y luego en **Consola del Administrador de paquetes**.

6. Ejecute los siguientes comandos en la consola para instalar los paquetes.

		Install-Package Microsoft.WindowsAzure.Management.HDInsight

	Este comando agrega las bibliotecas y referencias .NET a los paquetes para el proyecto de Visual Studio actual.

7. En el Explorador de soluciones, haga doble clic en **Program.cs** para abrirlo.

8. Agregue la siguiente instrucción en la parte superior del archivo:

		using System.Security.Cryptography.X509Certificates;
		using Microsoft.WindowsAzure.Management.HDInsight;
		using Microsoft.WindowsAzure.Management.HDInsight.ClusterProvisioning;


9. En la función Main(), copie y pegue el siguiente código:

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

**Para ejecutar la aplicación, siga estos pasos:**

Mientras la aplicación está abierta en Visual Studio, presione **F5** para ejecutarla. Una ventana de consola se abrirá y mostrará el estado de la aplicación. La creación del clúster de HDInsight puede durar varios minutos.



##<a id="nextsteps"></a> Pasos siguientes
En este artículo, ha aprendido a aprovisionar un clúster de HDInsight de varias formas. Para obtener más información, consulte los artículos siguientes:

* [Introducción a HDInsight de Azure][hdinsight-get-started]: aprenda a empezar a trabajar con su clúster de HDInsight
* [Uso de Sqoop con HDInsight][89e2276a]: aprenda copiar datos entre HDInsight y Base de datos SQL o SQL Server
* [Administración de HDInsight mediante PowerShell][hdinsight-admin-powershell]: aprenda a trabajar con HDInsight mediante PowerShell
* [Envío de trabajos de Hadoop mediante programación][hdinsight-submit-jobs]: aprenda a enviar trabajos a HDInsight mediante programación
* [Documentación del SDK de HDInsight de Azure][hdinsight-sdk-documentation]: descubra el SDK de HDInsight

[hdinsight-sdk-documentation]: http://msdn.microsoft.com/en-us/library/dn479185.aspx
[hdinsight-hbase-custom-provision]: http://azure.microsoft.com/en-us/documentation/articles/hdinsight-hbase-get-started/

[hdinsight-customize-cluster]: ../hdinsight-hadoop-customize-cluster/
[hdinsight-get-started]: ../hdinsight-get-started/
[hdinsight-storage]: ../hdinsight-use-blob-storage/
[hdinsight-admin-powershell]: ../hdinsight-administer-use-powershell/
[hadoop-hdinsight-intro]: ../hdinsight-hadoop-introduction/
[hdinsight-submit-jobs]: ../hdinsight-submit-hadoop-jobs-programmatically/
[hdinsight-powershell-reference]: http://msdn.microsoft.com/en-us/library/windowsazure/dn479228.aspx

[azure-management-portal]: https://manage.windowsazure.com/

[azure-command-line-tools]: ../xplat-cli/
[azure-command-line-tool]: ../command-line-tools/
[azure-create-storageaccount]: ../storage-create-storage-account/

[apache-hadoop]: http://go.microsoft.com/fwlink/?LinkId=510084
[azure-purchase-options]: http://azure.microsoft.com/en-us/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/en-us/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/en-us/pricing/free-trial/
[hdi-remote]: http://azure.microsoft.com/en-us/documentation/articles/hdinsight-administer-use-management-portal/#rdp


[Powershell-install-configure]: ../install-configure-powershell/

[image-hdi-customcreatecluster]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.png
[image-hdi-customcreatecluster-clusteruser]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.ClusterUser.png
[image-hdi-customcreatecluster-storageaccount]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.StorageAccount.png
[image-hdi-customcreatecluster-addonstorage]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.AddOnStorage.png

[image-customprovision-page1]: ./media/hdinsight-provision-clusters/HDI.CustomProvision.Page1.png
[image-customprovision-page2]: ./media/hdinsight-provision-clusters/HDI.CustomProvision.Page2.png
[image-customprovision-page3]: ./media/hdinsight-provision-clusters/HDI.CustomProvision.Page3.png
[image-customprovision-page4]: ./media/hdinsight-provision-clusters/HDI.CustomProvision.Page4.png

[image-cli-account-download-import]: ./media/hdinsight-provision-clusters/HDI.CLIAccountDownloadImport.png
[image-cli-clustercreation]: ./media/hdinsight-provision-clusters/HDI.CLIClusterCreation.png
[image-cli-clustercreation-config]: ./media/hdinsight-provision-clusters/HDI.CLIClusterCreationConfig.png
[image-cli-clusterlisting]: ./media/hdinsight-provision-clusters/HDI.CLIListClusters.png "List and show clusters"

[image-hdi-ps-provision]: ./media/hdinsight-provision-clusters/HDI.ps.provision.png

[img-hdi-cluster]: ./media/hdinsight-provision-clusters/HDI.Cluster.png

  [89e2276a]: /en-us/documentation/articles/hdinsight-use-sqoop/ "Use Sqoop with HDInsight"

<!--HONumber=35_1-->
