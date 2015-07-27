<properties 
   pageTitle="Aprovisionamiento de clústeres Apache Spark en HDInsight | Azure" 
   description="Obtenga información sobre cómo aprovisionar clústeres Spark para HDInsight de Azure mediante el Portal de Azure, Azure PowerShell, una línea de comandos o el SDK de .NET de HDInsight" 
   services="hdinsight" 
   documentationCenter="" 
   authors="nitinme" 
   manager="paulettm" 
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="07/10/2015"
   ms.author="nitinme"/>

# Aprovisionamiento de clústeres Apache Spark en HDInsight mediante opciones personalizadas

Para la mayoría de los escenarios, puede aprovisionar un clúster Spark mediante el método de creación rápida como se describe en [Inicio rápido: Aprovisionamiento de un clúster Spark en HDInsight y ejecución de consultas interactivas mediante Spark SQL](hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql.md). En determinados escenarios, debería aprovisionar un clúster personalizado. Por ejemplo, si desea conectar un almacén de metadatos externo para mantener la persistencia de los metadatos de Hive más allá de la duración de un clúster o si desea usar más almacenamiento con el clúster.

Para estos y otros escenarios, en este artículo se proporcionan instrucciones sobre cómo usar el Portal de Azure, Azure PowerShell o el SDK de .NET de HDInsight para aprovisionar un clúster Spark personalizado en HDInsight.


**Requisitos previos:**

Antes de comenzar con las instrucciones de este artículo, debe tener una suscripción a Azure. Consulte [How to get Azure Free trial for testing Hadoop in HDInsight (Obtención de una versión de prueba gratuita de Azure para probar Hadoop en HDInsight)](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

##<a id="configuration"></a>¿Cuáles son las diferentes opciones de configuración?

###Almacenamiento adicional

Durante la configuración, debe especificar una cuenta de almacenamiento de blobs de Azure y un contenedor predeterminado. El clúster usa este contenedor como ubicación de almacenamiento predeterminada. Si lo desea, puede especificar una cuenta de almacenamiento de Azure adicional que también se asociará con el clúster.

>[AZURE.NOTE]No comparta un contenedor de almacenamiento de blobs para varios clústeres, ya que no es compatible.

Para obtener más información sobre el uso de almacenes de blobs secundarios, consulte [Uso del almacenamiento de blobs de Azure con HDInsight](hdinsight-use-blob-storage.md).

###Tienda de metadatos

Spark permite definir esquemas y tablas de Hive con datos sin procesar. Puede guardar estos esquemas y metadatos de las tablas en las tiendas de metadatos externas. El uso de la tienda de metadatos lo ayuda a conservar sus metadatos de Hive, por lo que no es necesario volver a crear tablas de Hive al aprovisionar un nuevo clúster. De forma predeterminada, Hive utiliza una base de datos integrada para almacenar esta información. La base de datos incrustada no puede conservar los metadatos cuando se elimina el clúster.

### Personalización del clúster

Puede instalar componentes adicionales o personalizar la configuración del clúster mediante secuencias de comandos durante el aprovisionamiento. Tales scripts se invocan mediante la opción de **Acción de script**, una opción de configuración que se puede usar a partir de los cmdlets de Windows PowerShell de HDInsight, en el Portal de Azure o el SDK de .NET para HDInsight. Para obtener más información, consulte [Personalizar el clúster de HDInsight mediante la acción de script][hdinsight-customize-cluster].


###Redes virtuales

[Red virtual de Azure](http://azure.microsoft.com/documentation/services/virtual-network/) permite crear una red segura y persistente que contenga los recursos que necesita para la solución. Una red virtual permite hacer lo siguiente:

* Conectar recursos en la nube en una red privada (solo en la nube)

	![Diagrama de la configuración solo en la nube](./media/hdinsight-apache-spark-provision-clusters/hdinsight-vnet-cloud-only.png)

* Conectar sus recursos en la nube a la red de su centro de datos local (sitio a sitio o punto a sitio) usando una red privada virtual (VPN).

	La configuración sitio a sitio permite conectar varios recursos de su centro de datos a la red virtual de Azure usando una VPN de hardware o el servicio de enrutamiento y acceso remoto.

	![Diagrama de la configuración de sitio a sitio](./media/hdinsight-apache-spark-provision-clusters/hdinsight-vnet-site-to-site.png)

	La configuración punto a sitio permite conectar un recurso específico a la red virtual de Azure usando una VPN de software

	![Diagrama de la configuración de punto a sitio](./media/hdinsight-apache-spark-provision-clusters/hdinsight-vnet-point-to-site.png)

Para obtener más información sobre las características, las ventajas y la funcionalidad de Red virtual, consulte [Información general sobre Red virtual de Azure](http://msdn.microsoft.com/library/azure/jj156007.aspx).

> [AZURE.NOTE]Debe crear la red virtual de Azure antes de aprovisionar un clúster. Para obtener más información, consulte [Tareas de configuración de Red virtual](http://msdn.microsoft.com/library/azure/jj156206.aspx).
>
> Azure HDInsight solo admite redes virtuales basadas en la ubicación y actualmente no funciona con redes virtuales basadas en grupos de afinidad.
>
> Se recomienda encarecidamente designar una única subred para un clúster.

##<a id="portal"></a> Uso del portal de Azure

Los clústeres Spark en HDInsight usan un contenedor de almacenamiento de blobs de Azure como sistema de archivos predeterminado. Es preciso tener una cuenta de almacenamiento de Azure ubicada en el mismo centro de datos antes de crear un clúster de HDInsight. Para obtener más información, consulte [Uso de Almacenamiento de blobs de Azure con HDInsight](hdinsight-hadoop-use-blob-storage.md). Para obtener información acerca de la creación de una cuenta de almacenamiento de Azure, consulte [Creación de una cuenta de almacenamiento][azure-create-storageaccount].

**Para crear un clúster de HDInsight con la opción de creación personalizada**

1. Inicie sesión en el [Portal de Azure][azure-management-portal].
2. Haga clic en **+ NUEVO** en la parte inferior de la página y después en **SERVICIOS DE DATOS**, **HDINSIGHT** y **CREACIÓN PERSONALIZADA**.
3. En la página **Detalles del clúster**, escriba o elija los valores siguientes:

	![Proporcionar detalles del clúster Spark en HDInsight](./media/hdinsight-apache-spark-provision-clusters/HDI.CustomProvision.Page1.png "Proporcionar detalles del clúster Spark en HDInsight")

    <table border='1'>
	<tr><th>Propiedad</th><th>Valor</th></tr>
	<tr><td>Cluster Name</td>
		<td><p>Dé un nombre al clúster. </p>
			<ul>
			<li>El nombre del Sistema de nombres de dominio (DNS) debe empezar y terminar con un carácter alfanumérico y puede contener guiones.</li>
			<li>El campo debe ser una cadena con una longitud comprendida entre 3 y 63 caracteres.</li>
			</ul></td></tr>
	<tr><td>Tipo de clúster</td>
		<td>Para el tipo de clúster, seleccione<strong>Spark</strong>.</td></tr>
	<tr><td>Sistema operativo</td>
		<td>Seleccione <b>Windows Server 2012 R2 Data Center</b> para aprovisionar un clúster Spark que se ejecute en Windows. </td></tr>
	<tr><td>Versión de HDInsight</td>
		<td>Seleccione la versión. Para Spark, la única opción de versión disponible es <b>HDInsight 3.2</b>, que usa Spark 1.3.1.</td></tr>
	</table>Escriba o seleccione los valores mostrados en la tabla y después haga clic en la flecha derecha.

4. En la página **Configurar clúster** escriba o seleccione los valores siguientes:

	![Proporcionar el número y los tipos de nodos para el clúster](./media/hdinsight-apache-spark-provision-clusters/HDI.CustomProvision.Page2.png "Proporcionar el número y los tipos de nodos para el clúster")

	<table border="1">
<tr><th>Nombre</th><th>Valor</th></tr>
<tr><td>Nodos de datos</td><td>Número de nodos de datos que desea implementar. Para propósitos de prueba, cree un clúster de un solo nodo. <br />El límite de tamaño del clúster varía según las suscripciones a Azure. Póngase en contacto con el servicio de soporte relacionado con la facturación de Azure para aumentar el límite.</td></tr>
<tr><td>Región/red virtual</td><td><p>Elija la misma región que la cuenta de almacenamiento que creó en el último procedimiento. HDInsight requiere que la cuenta de almacenamiento se encuentre en la misma región. Posteriormente, en la configuración, puede seleccionar solo una cuenta de almacenamiento que se encuentre en la misma región que especificó aquí.</p>.<br/>Si ha creado una red virtual de Azure, puede seleccionar la red que configurará para su uso por parte del clúster de HDInsight.</p><p>Para obtener más información sobre la creación de una red virtual de Azure, consulte <a href="http://msdn.microsoft.com/library/azure/jj156206.aspx">Tareas de configuración de la red virtual</a>.</p></td></tr>
<tr><td>Tamaño de nodo principal</td><td><p>Seleccione un tamaño de máquina virtual (VM) para el nodo principal.</p></td></tr>
<tr><td>Tamaño de nodo de datos</td><td><p>Seleccione un tamaño de máquina virtual para los nodos de datos.</p></td></tr>
</table>>[AZURE.NOTE]En función de la elección de las máquinas virtuales, el costo puede variar. HDInsight usa todas las máquinas virtuales de nivel estándar para los nodos del clúster. Para obtener información sobre cómo afectan los tamaños de máquinas virtuales a los precios, consulte <a href="http://azure.microsoft.com/pricing/details/hdinsight/" target="_blank">Precios de HDInsight</a>.


5. En la página **Configurar usuario de clúster** proporcione los siguientes valores:

    ![Proporcionar detalles del usuario administrador y el usuario de RDP](./media/hdinsight-apache-spark-provision-clusters/HDI.CustomProvision.Page3.png "Proporcionar detalles del usuario administrador y el usuario de RDP")

    <table border='1'>
	<tr><th>Propiedad</th><th>Valor</th></tr>
	<tr><td>Nombre de usuario HTTP</td>
		<td>Especifique el nombre del usuario del clúster de HDInsight.</td></tr>
	<tr><td>Contraseña HTTP/Confirmar contraseña</td>
		<td>Especifique la contraseña del usuario del clúster de HDInsight.</td></tr>
	<tr><td>Habilitar Escritorio remoto para un clúster</td>
		<td>Active esta casilla para especificar un nombre de usuario, una contraseña y una fecha de caducidad para un usuario de Escritorio remoto que puede conectarse en remoto a los nodos del clúster, cuando esté aprovisionado. También puede habilitar Escritorio remoto más adelante, cuando el clúster esté aprovisionado. Para obtener instrucciones, vea <a href="hdinsight-administer-use-management-portal/#rdp" target="_blank">Conexión a los clústeres de HDInsight con RDP</a>.</td></tr>
	<tr><td>Especificar la tienda de metadatos de Hive/Oozie</td>
		<td>Active esta casilla para especificar una base de datos SQL que se encuentra en el mismo centro de datos que el clúster, que se usará como tienda de metadatos de Hive/Oozie. Si activa esta casilla, debe especificar detalles acerca de la base de datos SQL de Azure en las páginas siguientes del asistente. Esto resulta de gran utilidad si desea conservar los metadatos sobre trabajos de Hive/Oozie después de haber eliminado un clúster. Para obtener instrucciones sobre cómo crear una tienda de metadatos, consulte <a href="https://azure.microsoft.com/es-es/documentation/articles/sql-database-get-started/" target="_blank">Creación de la primera Base de datos SQL de Azure</a>.</td></tr>
	</td></tr>
</table>>[AZURE.NOTE]La base de datos SQL de Azure usada para la tienda de metadatos debe permitir la conectividad con otros servicios de Azure, incluido HDInsight de Azure. En el panel de la base de datos SQL de Azure, en el lado derecho, haga clic en el nombre de servidor. Este es el servidor en el que se ejecuta la instancia de base de datos SQL. Cuando se encuentre en la vista de servidor, haga clic en **Configurar** y luego, en **Servicios de** **Microsoft Azure**, haga clic en **Sí** y en **Guardar**.

    Haga clic en la flecha derecha.

6. En la página **Configurar la tienda de metadatos de Hive/Oozie** proporcione los siguientes valores:

    ![Proporcionar detalles de la base de datos de la tienda de metadatos de Hive/Oozie](./media/hdinsight-apache-spark-provision-clusters/HDI.CustomProvision.Page4.png "Proporcionar detalles de la base de datos de la tienda de metadatos de Hive/Oozie")

	Especifique una base de datos SQL de Azure que se usará como tienda de metadatos para Hive/Oozie. Puede especificar la misma base de datos para las tiendas de metadatos de Hive y de Oozie. Esta base de datos SQL debe estar en el mismo centro de datos que el clúster de HDInsight. El cuadro de lista solo incluye las bases de datos SQL del mismo centro de datos que ha especificado en la página <strong>Detalles del clúster</strong>. Especifique también el nombre de usuario y la contraseña para conectarse a la base de datos SQL de Azure seleccionada.

    >[AZURE.NOTE]La base de datos SQL de Azure usada para la tienda de metadatos debe permitir la conectividad con otros servicios de Azure, incluido HDInsight de Azure. En el panel de la base de datos SQL de Azure, en el lado derecho, haga clic en el nombre de servidor. Este es el servidor en el que se ejecuta la instancia de base de datos SQL. Cuando se encuentre en la vista de servidor, haga clic en **Configurar** y luego, en **Servicios de Azure**, haga clic en **Sí** y en **Guardar**.

    Haga clic en la flecha derecha.

7. En la página **Cuenta de almacenamiento**, proporcione los siguientes valores:

    ![Escribir los detalles de la cuenta de almacenamiento](./media/hdinsight-apache-spark-provision-clusters/HDI.CustomProvision.Page5.png "Escribir los detalles de la cuenta de almacenamiento")

	<table border='1'>
	<tr><th>Propiedad</th><th>Valor</th></tr>
	<tr><td>Cuenta de almacenamiento</td>
		<td>Especifique la cuenta de almacenamiento de Azure que se usará como sistema de archivos predeterminado para el clúster de HDInsight. Puede elegir una de las tres opciones siguientes:
		<ul>
			<li><strong>Usar almacenamiento existente</strong></li>
			<li><strong>Crear nuevo almacenamiento</strong></li>
			<li><strong>Utilizar almacenamiento de otra suscripción</strong></li>
		</ul>
		</td></tr>
	<tr><td>Nombre de cuenta</td>
		<td><ul>
			<li>Si decidió utilizar almacenamiento existente, en <strong>Nombre de cuenta</strong>, seleccione una cuenta de almacenamiento existente. En la lista desplegable solamente aparecen las cuentas de almacenamiento ubicadas en el mismo centro de datos en el que eligió aprovisionar el clúster.</li>
			<li>Si eligió la opción <strong>Crear nuevo almacenamiento</strong> o <strong>Usar almacenamiento de otra suscripción</strong>, debe proporcionar el nombre de la cuenta de almacenamiento.</li>
		</ul></td></tr>
	<tr><td>Clave de cuenta</td>
		<td>Si eligió la opción <strong>Usar almacenamiento de otra suscripción</strong>, especifique la clave de cuenta para esa cuenta de almacenamiento.</td></tr>
	<tr><td>Contenedor predeterminado</td>
		<td><p>Especifique el contenedor predeterminado en la cuenta de almacenamiento que se utiliza como sistema de archivos predeterminado para el clúster de HDInsight. Si eligió <strong>Usar almacenamiento existente</strong> para el campo <strong>Cuenta de almacenamiento</strong> y no existen contenedores en esa cuenta, el contenedor se creará de forma predeterminada con el mismo nombre que el del clúster. Si ya existe un contenedor con el nombre del clúster, se anexará un número de secuencia al nombre del contenedor. Por ejemplo, mycontainer1, mycontainer2 y así sucesivamente. Sin embargo, si la cuenta de almacenamiento existente tiene un contenedor con un nombre diferente al del clúster especificado, también puede usar ese contenedor.</p>
        <p>Si eligió crear un almacenamiento nuevo o usar almacenamiento de otra suscripción de Azure, debe especificar el nombre del contenedor predeterminado.</p>
    </td></tr>
	<tr><td>Cuentas de almacenamiento adicionales</td>
		<td>HDInsight admite varias cuentas de almacenamiento. No hay límite en el número de cuentas de almacenamiento adicionales que un clúster puede usar. No obstante, si crea un clúster mediante el portal de Azure, tendrá un límite de siete debido a las restricciones de la interfaz de usuario. Por cada cuenta de almacenamiento adicional que especifique, se agregará una página **Cuenta de almacenamiento** adicional al asistente donde podrá especificar la información de la cuenta. Por ejemplo, en la captura de pantalla anterior, se selecciona una cuenta de almacenamiento adicional y, por tanto, se agrega la página 5 al diálogo.</td></tr>
</table>Haga clic en la flecha derecha.

8. Si ha elegido configurar almacenamiento adicional para el clúster, escriba la información de la cuenta de almacenamiento adicional en la página **Cuenta de almacenamiento**:

	![Proporcionar más detalles de la cuenta de almacenamiento](./media/hdinsight-apache-spark-provision-clusters/HDI.CustomProvision.Page6.png "Proporcionar más detalles de la cuenta de almacenamiento")

    De nuevo aquí, tiene la opción de elegir entre almacenamiento existente, crear nuevo almacenamiento o usar almacenamiento de otra suscripción de Azure. El procedimiento para proporcionar los valores es similar al paso anterior.

    > [AZURE.NOTE]Cuando haya elegido una cuenta de almacenamiento de Azure para su clúster de HDInsight, no podrá eliminar la cuenta, ni cambiarla por otra diferente.

8. En la página **Acciones de script**, haga clic en **agregar acción de script** y proporcione información detallada sobre el script personalizado que desea ejecutar para personalizar un clúster durante su creación. Para obtener más información, consulte [Personalización de un clúster de HDInsight mediante la acción de script][hdinsight-customize-cluster].

	Si necesita usar una acción de script, debe proporcionar los siguientes datos.
	
	<table border='1'>
	<tr><th>Propiedad</th><th>Valor</th></tr>
	<tr><td>Nombre</td>
		<td>Especifique un nombre para la acción de script.</td></tr>
	<tr><td>URI de script</td>
		<td>Especifique el Identificador uniforme de recursos (URI) al script que se ha invocado para personalizar el clúster.</td></tr>
	<tr><td>Tipo de nodo</td>
		<td>Especifique los nodos en los que se ejecuta el script de personalización. Puede elegir <b>Todos los nodos</b>, <b>Solo nodos principales</b> o <b>Solo nodos de trabajo</b>.
	<tr><td>Parámetros</td>
		<td>Especifique los parámetros, si lo requiere el script.</td></tr>
</table>Puede agregar más de una acción de script para instalar varios componentes en el clúster. Una vez agregados los scripts, haga clic en la marca de verificación para iniciar el aprovisionamiento del clúster.



##<a id="powershell"></a> Uso de Azure PowerShell

En esta sección, se proporcionan instrucciones sobre cómo aprovisionar un clúster Apache Spark en HDInsight de Azure con Azure PowerShell. Para obtener información sobre la configuración de una estación de trabajo para que ejecute cmdlets de HDInsight Windows PowerShell, consulte [Instalación y configuración de Azure PowerShell][powershell-install-configure]. Para obtener más información sobre el uso de Azure PowerShell con HDInsight, consulte [Administración de HDInsight con PowerShell][hdinsight-admin-powershell]. Para obtener una lista de los cmdlets de HDInsight Windows PowerShell, consulte [Documentación de referencia de los cmdlets de HDInsight][hdinsight-powershell-reference].

> [AZURE.NOTE]Si bien los scripts de esta sección se pueden usar para configurar un clúster de HDInsight en una red virtual de Azure, no crearán una red virtual de Azure. Para obtener información sobre la creación de una red virtual de Azure, consulte [Tareas de configuración de la red virtual](http://msdn.microsoft.com/library/azure/jj156206.aspx).

Los siguientes procedimientos son necesarios para aprovisionar un clúster de HDInsight con Azure PowerShell:

- Crear una cuenta de Almacenamiento de Azure
- Creación de un contenedor de blobs de Azure
- Creación de un clúster de HDInsight

Puede usar la consola de Windows PowerShell o el entorno de scripting integrado (ISE) de Windows PowerShell para ejecutar los scripts.
 
HDInsight utiliza contenedores de almacenamiento de blobs de Azure como sistemas de archivos predeterminados. Es preciso tener una cuenta de almacenamiento de Azure y un contenedor de almacenamiento para crear un clúster de HDInsight. Dicha cuenta debe ubicarse en el mismo centro de datos que el clúster de HDInsight.

**Para conectarse a su cuenta de Azure**

		Add-AzureAccount 

Se le pedirá que escriba las credenciales de la cuenta de Azure.

**Para crear una cuenta de almacenamiento de Azure**

		$storageAccountName = "<StorageAcccountName>"	# Provide a Storage account name
		$location = "<MicrosoftDataCenter>"				# For example, "West US"

		# Create an Azure Storage account
		New-AzureStorageAccount -StorageAccountName $storageAccountName -Location $location

Si ya tiene una cuenta de almacenamiento pero no sabe su nombre ni su clave, puede usar los siguientes comandos de Windows PowerShell para recuperar dicha información:

		# List Storage accounts for the current subscription
		Get-AzureStorageAccount

		# List the keys for a Storage account
		Get-AzureStorageKey "<StorageAccountName>"

**Para crear un contenedor de almacenamiento de blobs de Azure**

		$storageAccountName = "<StorageAccountName>"	# Provide the Storage account name
		$containerName="<ContainerName>"				# Provide a container name

		# Create a storage context object
		$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
		$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName
		                                       -StorageAccountKey $storageAccountKey  

		# Create a Blob storage container
		New-AzureStorageContainer -Name $containerName -Context $destContext

Cuando tenga preparados la cuenta de almacenamiento y el contenedor de blobs, podrá proceder con la creación de un clúster.

**Para aprovisionar un clúster de HDInsight**

- Ejecute los comandos siguientes en la ventana de la consola de Azure PowerShell:

		$subscriptionName = "<AzureSubscriptionName>"	  # The Azure subscription used for the HDInsight cluster to be created

		$storageAccountName = "<AzureStorageAccountName>" # HDInsight cluster requires an existing Azure Storage account to be used as the default file system

		$clusterName = "<HDInsightClusterName>"			  # The name for the HDInsight cluster to be created
		$clusterNodes = <ClusterSizeInNodes>              # The number of nodes in the HDInsight cluster
        $credentials = Get-Credential		              # User name and password for the Hadoop account. You will use this account to connect to the cluster and run jobs.
           
        # Get the storage primary key based on the account name
		Select-AzureSubscription $subscriptionName
		$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
		$containerName = $clusterName				# Azure Blob container that is used as the default file system for the HDInsight cluster

        # The location of the HDInsight cluster. It must be in the same data center as the Storage account.
        $location = Get-AzureStorageAccount -StorageAccountName $storageAccountName | %{$_.Location} 

		# Create a new HDInsight cluster
		New-AzureHDInsightCluster -Name $clusterName -Credential $credentials -Location $location -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainerName $containerName -ClusterSizeInNodes $clusterNodes -Version "3.2" -ClusterType Spark

	>[AZURE.NOTE]Las credenciales especificadas se usan para crear la cuenta de usuario de Hadoop para el clúster. Esta cuenta se usará para conectarse al clúster y ejecutar trabajos. Si usa la opción Creación rápida en el portal de Azure para aprovisionar un clúster, el nombre de usuario de Hadoop predeterminado es "admin". No confunda esta cuenta con la cuenta de usuario del Protocolo de escritorio remoto (RDP). La cuenta de usuario RDP tiene que ser diferente de la cuenta de usuario de Hadoop. Para obtener más información, vea [Administración de clústeres de Hadoop en HDInsight mediante el Portal de administración de Azure][hdinsight-admin-portal].

	El aprovisionamiento del clúster puede durar varios minutos en completarse.

	![HDI.PS.Provision](./media/hdinsight-apache-spark-provision-clusters/hdi-spark-ps-provisioning.png "Aprovisionamiento de clúster Spark con la configuración básica en PowerShell")


**Para aprovisionar un clúster de HDInsight usando opciones de configuración personalizada**

Durante el aprovisionamiento de un clúster, puede usar las otras opciones de configuración, como por ejemplo conectarse a más de un contenedor de almacenamiento de blobs de Azure o usar una base de datos SQL de Azure para tiendas de metadatos de Hive y Oozie. Esto le permite separar la vigencia de sus datos y metadatos de la vigencia del clúster.

> [AZURE.NOTE]Los cmdlets de Windows PowerShell son la única forma recomendada de cambiar variables de configuración en un clúster de HDInsight. Los cambios que se realicen en los archivos de configuración de Hadoop mientras esté conectado al clúster a través de Escritorio remoto se pueden sobrescribir si se aplica una revisión al clúster. Los valores de configuración establecidos con Azure PowerShell sí se mantienen si se aplica una revisión al clúster.

- Ejecute los comandos siguientes en la ventana de Windows PowerShell:

		$subscriptionName = "<Azure subscription>"
		$clusterName = "<cluster name>"
		$clusterNodes = <cluster size in nodes>
		                
		# Get credentials for Hadoop user and Hive/Oozie metastores
		$clusterCredentials = Get-Credential
		$oozieCreds = Get-Credential -Message "Oozie metastore"
		$hiveCreds = Get-Credential -Message "Hive metastore"
		
		# Get default storage account, storage container, and add-on storage account
		$storageAccountName_Default = "<Default storage account name>"
		$containerName_Default = $clusterName
		$storageAccountName_Add1 = "<Add-on storage account name>"
		
		# Get information about Hive and Oozie metastores
		$hiveSQLDatabaseServerName = "<SQLDatabaseServerNameForHiveMetastore>"
		$hiveSQLDatabaseName = "<SQLDatabaseNameForHiveMetastore>"
		$oozieSQLDatabaseServerName = "<SQLDatabaseServerNameForOozieMetastore>""
		$oozieSQLDatabaseName = "<SQLDatabaseNameForOozieMetastore>"

		# Get the virtual network ID and subnet name
		$vnetID = "<AzureVirtualNetworkID>"
		$subNetName = "<AzureVirtualNetworkSubNetName>"

		# Retrieve storage account keys and storage account location
		Select-AzureSubscription $subscriptionName
		$storageAccountKey_Default = Get-AzureStorageKey $storageAccountName_Default | %{ $_.Primary }
		$storageAccountKey_Add1 = Get-AzureStorageKey $storageAccountName_Add1 | %{ $_.Primary }
		$location = Get-AzureStorageAccount -StorageAccountName $storageAccountName_Default | %{$_.Location}
		
		# Specify custom configuration and cluster type
		$config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes $clusterNodes -ClusterType Spark -VirtualNetworkId $vnetID -SubnetName $subNetName | Set-AzureHDInsightDefaultStorage -StorageAccountName "$storageAccountName_Default.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Default -StorageContainerName $containerName_Default | Add-AzureHDInsightStorage -StorageAccountName "$storageAccountName_Add1.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Add1 | Add-AzureHDInsightMetastore -SqlAzureServerName "$hiveSQLDatabaseServerName.database.windows.net" -DatabaseName $hiveSQLDatabaseName -Credential $hiveCreds -MetastoreType HiveMetastore | Add-AzureHDInsightMetastore -SqlAzureServerName "$oozieSQLDatabaseServerName.database.windows.net" -DatabaseName $oozieSQLDatabaseName -Credential $oozieCreds -MetastoreType OozieMetastore
		
		# Provision the cluster with custom configuration                
		New-AzureHDInsightCluster -Name $clusterName -Config $config -Credential $clusterCredentials -Location $location -Version "3.2"

	>[AZURE.NOTE]La base de datos SQL de Azure usada para la tienda de metadatos debe permitir la conectividad con otros servicios de Azure, incluido HDInsight de Azure. En el panel de la base de datos SQL de Azure, en el lado derecho, haga clic en el nombre de servidor. Este es el servidor en el que se ejecuta la instancia de base de datos SQL. Cuando se encuentre en la vista de servidor, haga clic en **Configurar** y luego, en **Servicios de** **Microsoft Azure**, haga clic en **Sí** y en **Guardar**.

	El aprovisionamiento del clúster puede durar varios minutos en completarse.

**Para enumerar los clústeres de HDInsight**

- Ejecute el siguiente comando en una ventana de consola de Azure PowerShell para enumerar el clúster de HDInsight y comprobar que dicho clúster se aprovisionó correctamente:

		Get-AzureHDInsightCluster -Name <ClusterName>

##<a id="sdk"></a> Uso del SDK de .NET de HDInsight
El SDK .NET de HDInsight proporciona bibliotecas de cliente .NET que facilitan el trabajo con HDInsight desde una aplicación .NET Framework.

Los siguientes procedimientos se deben realizar para aprovisionar un clúster de HDInsight con el SDK:

- Instalación del SDK de .NET de HDInsight
- Creación de un certificado autofirmado
- Creación de una aplicación de consola
- Ejecución de la aplicación


**Para instalar el SDK de .NET de HDInsight**

Puede instalar la compilación publicada más reciente del SDK desde [NuGet](http://nuget.codeplex.com/wikipage?title=Getting%20Started). Las instrucciones se mostrarán en el siguiente procedimiento.

**Para crear un certificado autofirmado**

Cree un certificado autofirmado, instálelo en su estación de trabajo y cárguelo en su suscripción de Azure. Para obtener instrucciones, consulte [Creación de un certificado autofirmado](http://go.microsoft.com/fwlink/?LinkId=511138).


**Para crear una aplicación de consola de Visual Studio**

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
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">CrearClústerHDI</td></tr>
</table>

4. Haga clic en **Aceptar** para crear el proyecto.

5. En el menú **Herramientas**, haga clic en **Administrador de paquetes NuGet** y, a continuación, en la **Consola del administrador de paquetes**.

6. Ejecute el siguiente comando en la consola para instalar los paquetes:

		Install-Package Microsoft.WindowsAzure.Management.HDInsight

	Este comando agrega las bibliotecas y referencias .NET a los paquetes para el proyecto de Visual Studio actual.

7. En el Explorador de soluciones, haga doble clic en **Program.cs** para abrirlo.

8. Agregue la siguiente instrucción en la parte superior del archivo:

		using System.Security.Cryptography.X509Certificates;
		using Microsoft.WindowsAzure.Management.HDInsight;
		using Microsoft.WindowsAzure.Management.HDInsight.ClusterProvisioning;
		using Microsoft.WindowsAzure.Management.HDInsight.ClusterProvisioning.Data;


9. En la función main(), copie y pegue el código siguiente:

        string thumbprint = "<CertificateFriendlyName>";  
        string subscriptionid = "<AzureSubscriptionID>";
        string clustername = "<HDInsightClusterName>";
        string location = "<MicrosoftDataCenter>";
        string storageaccountname = "<AzureStorageAccountName>.blob.core.windows.net";
        string storageaccountkey = "<AzureStorageAccountKey>";
        string containername = "<HDInsightDefaultContainerName>";
        string username = "<HDInsightUsername>";
        string password = "<HDInsightUserPassword>";
        int clustersize = <NumberOfNodesInTheCluster>;

        // Get the certificate object from certificate store by using the friendly name to identify it
        X509Store store = new X509Store();
        store.Open(OpenFlags.ReadOnly);
        X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.Thumbprint == thumbprint);

        // Create the Storage account if it doesn't exist

        // Create the container if it doesn't exist.

		// Create an HDInsightClient object
        HDInsightCertificateCredential creds = new HDInsightCertificateCredential(new Guid(subscriptionid), cert);
        var client = HDInsightClient.Connect(creds);

		// Supply the cluster information
        ClusterCreateParametersV2 clusterInfo = new ClusterCreateParametersV2()
		{
		    Name = clustername,
		    Location = location,
		    DefaultStorageAccountName = storageaccountname,
		    DefaultStorageAccountKey = storageaccountkey,
		    DefaultStorageContainer = containername,
		    UserName = username,
		    Password = password,
		    ClusterSizeInNodes = clustersize,
		    Version = "3.2",
		    OSType = OSType.Windows,
			ClusterType = ClusterType.Spark		    
		};


		// Create the cluster
        Console.WriteLine("Creating the HDInsight cluster ...");

        ClusterDetails cluster = client.CreateCluster(clusterInfo);

        Console.WriteLine("Created cluster: {0}.", cluster.ConnectionUrl);
        Console.WriteLine("Press ENTER to continue ...");
        Console.ReadKey();

10. Reemplace las variables al principio de la función main().

**Para ejecutar la aplicación**

Mientras la aplicación está abierta en Visual Studio, presione **F5** para ejecutarla. Una ventana de consola se abrirá y mostrará el estado de la aplicación. La creación del clúster de HDInsight puede durar varios minutos.


##<a id="nextsteps"></a> Pasos siguientes

* Consulte [Uso de herramientas de BI con Apache Spark en HDInsight de Azure](hdinsight-apache-spark-use-bi-tools.md) para aprender a usar Apache Spark en HDInsight con herramientas de BI como Power BI y Tableau. 
* Consulte [Uso de Spark en HDInsight para crear aplicaciones de aprendizaje automático](hdinsight-apache-spark-ipython-notebook-machine-learning.md) para aprender a crear aplicaciones de aprendizaje automático con Apache Spark en HDInsight.
* Consulte [Streaming con Spark: Procesamiento de eventos desde el Centro de eventos de Azure con Apache Spark en HDInsight](hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming.md) para aprender a crear aplicaciones de streaming con Apache Spark en HDInsight.
* Consulte [Administración de recursos para el clúster Apache Spark en HDInsight de Azure](hdinsight-apache-spark-resource-manager.md) para aprender a usar el Administrador de recursos para administrar los recursos asignados al clúster Spark.


[hdinsight-sdk-documentation]: http://msdn.microsoft.com/library/dn479185.aspx
[hdinsight-hbase-custom-provision]: http://azure.microsoft.com/documentation/articles/hdinsight-hbase-get-started/

[hdinsight-customize-cluster]: ../hdinsight-hadoop-customize-cluster/
[hdinsight-get-started]: ../hdinsight-get-started/

[hdinsight-admin-powershell]: ../hdinsight-administer-use-powershell/
[hdinsight-admin-portal]: ../hdinsight-administer-use-management-portal/
[hadoop-hdinsight-intro]: ../hdinsight-hadoop-introduction/
[hdinsight-submit-jobs]: ../hdinsight-submit-hadoop-jobs-programmatically/
[hdinsight-powershell-reference]: https://msdn.microsoft.com/library/dn858087.aspx
[hdinsight-storm-get-started]: ../hdinsight-storm-getting-started/

[azure-management-portal]: https://manage.windowsazure.com/

[azure-command-line-tools]: ../xplat-cli/

[azure-create-storageaccount]: ../storage-create-storage-account/

[apache-hadoop]: http://go.microsoft.com/fwlink/?LinkId=510084
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[hdi-remote]: http://azure.microsoft.com/documentation/articles/hdinsight-administer-use-management-portal/#rdp


[powershell-install-configure]: ../install-configure-powershell/




[89e2276a]: /documentation/articles/hdinsight-use-sqoop/ "Uso de Sqoop con HDInsight"

<!---HONumber=July15_HO3-->