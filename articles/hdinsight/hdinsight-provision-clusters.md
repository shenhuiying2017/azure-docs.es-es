<properties
   pageTitle="Creación de clústeres de Hadoop en HDInsight | Microsoft Azure"
   	description="Obtenga información sobre cómo crear clústeres para HDInsight de Azure mediante el Portal de vista previa de Azure, Azure PowerShell, una línea de comandos o .NET SDK."
   services="hdinsight"
   documentationCenter=""
   tags="azure-portal"
   authors="mumian"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="11/16/2015"
   ms.author="jgao"/>

# Creación de clústeres de Hadoop en HDInsight

Obtenga información acerca de cómo planear la creación de clústeres de HDInsight.

[AZURE.INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

* [Creación de clústeres de Hadoop en HDInsight](hdinsight-provision-clusters-v1.md)

[AZURE.INCLUDE [hdinsight-azure-preview-portal](../../includes/hdinsight-azure-preview-portal.md)]

* [Creación de clústeres de Hadoop en HDInsight](hdinsight-provision-clusters-v1.md)

**Requisitos previos:**

Antes de empezar las instrucciones de este artículo, debe tener lo siguiente:

- Una suscripción de Azure. Consulte [How to get Azure Free trial for testing Hadoop in HDInsight (Obtención de una versión de prueba gratuita de Azure para probar Hadoop en HDInsight)](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).


## Opciones de configuración básica


- **Nombre del clúster**

	El nombre del clúster se utiliza para identificar un clúster. El nombre de clúster debe seguir las siguientes directrices:

	- El campo debe ser una cadena que contenga entre 3 y 63 caracteres
	- El campo solo puede contener letras, números y guiones.

- **Nombre de la suscripción**

	Un clúster de HDInsight está asociado a una suscripción a Azure.

- **Definición de un nombre de grupo de recursos**

	Las aplicaciones normalmente se componen de muchos componentes,por ejemplo una aplicación web, base de datos, servidor de base de datos, almacenamiento y servicios de terceros. El Administrador de recursos de Azure (ARM) permite trabajar con los recursos de la aplicación como un grupo al que se hace referencia como Grupo de recursos de Azures Puede implementar, actualizar, supervisar o eliminar todos los recursos de la aplicación en una operación única y coordinada. Para la implementación se utiliza una plantilla, y esta plantilla puede trabajar en diferentes entornos, como pruebas, ensayo y producción. Puede aclarar la facturación de la organización consultando los costes acumulados de todo el grupo. Para obtener más información, consulte [Información general del Administrador de recursos de Azure](resource-group-overview.md).	
- **Sistema operativos**

	Puede crear clústeres de HDInsight en uno de los dos sistemas operativos siguientes: - **HDInsight en Windows (Windows Server 2012 R2 Datacenter)**: - **HDInsight en Linux (Ubuntu 12.04 LTS para Linux) (vista previa)**: HDInsight ofrece la opción de configurar clústeres de Linux en Azure. Configure un clúster de Linux si conoce Linux o Unix, migrando desde una solución existente de Hadoop basado en Linux, o si desea una integración fácil con componentes del ecosistema de Hadoop creados para Linux. Para obtener más información, vea [Introducción a Hadoop en Linux en HDInsight](hdinsight-hadoop-linux-get-started.md).


- **Versión de HDInsight**

	Se utiliza para determinar la versión de HDInsight que se utilizará para este clúster. Para obtener más información, consulte [Componentes y versiones de clústeres de Hadoop en HDInsight](https://go.microsoft.com/fwLink/?LinkID=320896&clcid=0x409)

- **Tipo de clúster** y **tamaño del clúster (también conocidos como nodos de datos)**

	HDInsight permite a los clientes implementar varios tipos de clúster para diferentes cargas de trabajo de análisis de datos. Los tipos de clústeres que se ofrecen hoy son:

	- Clústeres de Hadoop: para cargas de trabajo de consulta y análisis
	- Clústeres de HBase: para cargas de trabajo NoSQL
	- Clústeres de Storm: para cargas de trabajo de procesamiento de eventos en tiempo real
	- Clústeres de Spark (vista previa): para procesamiento en memoria, consultas interactivas, transmisiones y cargas de trabajo de aprendizaje automático

	![Clústeres de HDInsight](./media/hdinsight-provision-clusters/hdinsight.clusters.png)

	> [AZURE.NOTE]El *clúster de HDInsight de Azure* se llama también *clúster de Hadoop en HDInsight* o *clúster de HDInsight*. En algunas ocasiones, se usa indistintamente con el *clúster de Hadoop*. Todos estos nombres se refieren a los clústeres de Hadoop hospedados en el entorno de Microsoft Azure.

	Dentro de un tipo de clúster determinado, existen distintos roles para los diversos nodos, los que permiten que un cliente dimensione esos nodos en un rol determinado según los detalles de su carga de trabajo. Por ejemplo, un clúster de Hadoop puede crear sus nodos de trabajo con una gran cantidad de memoria si el tipo de análisis que se realiza requiere mucha memoria.

	![Roles de clúster de Hadoop en HDInsight](./media/hdinsight-provision-clusters/HDInsight.Hadoop.roles.png)

	Los clústeres de Hadoop para HDInsight se implementan con dos roles:

	- Nodo principal (2 nodos)
	- Nodo de datos (al menos 1 nodo)

	![Roles de clúster de Hadoop en HDInsight](./media/hdinsight-provision-clusters/HDInsight.HBase.roles.png)

	Los clústeres de HBase para HDInsight se implementan con tres roles: - Servidores principales (2 nodos) - Servidores regionales (al menos 1 nodo) - Nodos principales/Zookeeper (3 nodos)

	![Roles de clúster de Hadoop en HDInsight](./media/hdinsight-provision-clusters/HDInsight.Storm.roles.png)

	Los clústeres de Storm para HDInsight se implementan con tres roles: - Nodos Nimbus (2 nodos) - Servidores de supervisor (al menos 1 nodo) - Nodos Zookeeper (3 nodos)


	![Roles de clúster de Hadoop en HDInsight](./media/hdinsight-provision-clusters/HDInsight.Spark.roles.png)

	Los clústeres de Spark para HDInsight se implementan con tres roles: - Nodo principal (2 nodos) - Nodo de trabajo (al menos 1 nodo) - Nodos de Zookeeper (3 nodos) (gratis para Zookeepers A1)

	El uso de esos nodos se factura a los clientes por la duración del clúster. La facturación comienza una vez que se crea un clúster y se detiene cuando se elimina el clúster (no es posible eliminar la asignación de los clústeres ni tampoco se pueden poner en espera) El tamaño del clúster afecta el precio del mismo. Con fines de aprendizaje, se recomienda utilizar 1 nodo de datos. Para obtener más información sobre los precios de HDInsight, consulte [HDInsight Precios](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409).


	>[AZURE.NOTE]El límite de tamaño del clúster varía entre las suscripciones a Azure. Póngase en contacto con el servicio de soporte relacionado con la facturación para aumentar el límite.

- **Región/red virtual (conocida también como ubicación)**

	![Regiones de Azure](./media/hdinsight-provision-clusters/Azure.regions.png)

	Si desea obtener una lista de las regiones admitidas, haga clic en la lista desplegable **Región** en [HDInsight Precios](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409).

- **Tamaño del nodo**

	![tamaños de nodos de vm de hdinsight](./media/hdinsight-provision-clusters/hdinsight.node.sizes.png)

	Seleccione el tamaño de VM para los nodos. Para obtener más información, consulte [Tamaños de los servicios en la nube](cloud-services-sizes-specs.md).

	En función de la elección de las máquinas virtuales, el costo puede variar. HDInsight usa todas las máquinas virtuales de nivel estándar para los nodos del clúster. Para obtener información sobre cómo afectan los tamaños de máquinas virtuales a los precios, consulte <a href="http://azure.microsoft.com/pricing/details/hdinsight/" target="_blank">Precios de HDInsight</a>.


- **Usuarios de HDInsight**

	Los clústeres de HDInsight le permiten configurar dos cuentas de usuario durante el aprovisionamiento:

	- Usuario de HTTP. El nombre de usuario predeterminado es admin durante la configuración básica en el Portal de vista previa de Azure.
	- Usuario de RDP (clústeres de Windows): se usa para conectarse al clúster mediante RDP. Cuando crea la cuenta, debe establecer una fecha de caducidad que se encuentre dentro de 90 días a contar del día de hoy.
	- Usuario SSH (clústeres de Linux): se usa para conectarse al clúster mediante SSH. Puede crear cuentas de usuario SSH adicional una vez que se cree el clúster siguiendo los pasos en [Utilización de SSH con Hadoop en HDInsight basado en Linux desde Linux, Unix u OS X](hdinsight-hadoop-linux-use-ssh-unix.md).



- **Cuenta de Almacenamiento de Azure**

	El HDFS original usa muchos discos locales en el clúster. En su lugar, HDInsight utiliza el almacenamiento de blobs de Azure para el almacenamiento de datos. El almacenamiento de blobs de Azure es una solución de almacenamiento sólida y de uso general, que se integra sin problemas con HDInsight. A través de una interfaz del sistema de archivos distribuidos de Hadoop (HDFS), el conjunto completo de componentes de HDInsight puede operar directamente en datos estructurados o no estructurados en el almacenamiento de blobs. Almacenar los datos en un almacenamiento de blobs hace que elimine de forma segura los clústeres de HDInsight que se usan para los cálculos sin perder los datos del usuario.

	Durante la configuración, debe especificar una cuenta de almacenamiento de Azure y un contenedor de almacenamiento de blobs de Azure en la cuenta de almacenamiento de Azure. Algunos de los procesos de creación requieren que la cuenta de almacenamiento de Azure y el contenedor de almacenamiento de blobs se creen con antelación. El clúster utiliza el contenedor de almacenamiento de blobs como la ubicación de almacenamiento predeterminada. De manera opcional, puede especificar cuentas de almacenamiento de Azure adicionales (almacenamiento vinculado) al que podrá tener acceso el clúster. Además, el clúster también podrá tener acceso a cualquier contenedor de blobs que esté configurado con acceso de lectura público completo o con acceso de lectura público solo para blobs. Para obtener más información sobre el acceso restringido, consulte [Administración del acceso a los recursos de Almacenamiento de Azure](storage-manage-access-to-resources.md).

	![Almacenamiento de HDInsight](./media/hdinsight-provision-clusters/HDInsight.storage.png)

	>[AZURE.NOTE]Un contenedor de almacenamiento de blobs proporciona una agrupación de un conjunto de blobs, tal como se muestra en la imagen:

	![Almacenamiento de blobs de Azure](./media/hdinsight-provision-clusters/Azure.blob.storage.jpg)


	>[AZURE.WARNING]No comparta un contenedor de almacenamiento de blobs para varios clústeres, ya que no es compatible.

	Para obtener más información sobre el uso de almacenes de blobs secundarios, consulte [Uso del almacenamiento de blobs de Azure con HDInsight](hdinsight-use-blob-storage.md).

- **Tienda de metadatos Hive/Oozie**

	La tienda de metadatos contiene metadatos de Hive y Oozie, como columnas, esquemas, particiones y tablas de Hive. El uso de la tienda de metadatos le ayuda a conservar sus metadatos de Hive y Oozie, por lo que no es necesario volver a crear tablas de Hive o trabajos de Oozie al crear un nuevo clúster. De forma predeterminada, Hive utiliza una base de datos SQL de Azure incrustada para almacenar esta información. La base de datos incrustada no puede conservar los metadatos cuando se elimina el clúster. Por ejemplo, tiene un clúster creado con una tienda de metadatos de Hive. Creó algunas tablas de Hive. Después de eliminar el clúster y de volverlo a crear con la misma tienda de metadatos de Hive, podrá ver las tablas de Hive que creó en el clúster original.
    
    > [AZURE.NOTE]La configuración de la tienda de metadatos no está disponible para los tipos de clúster de HBase.

## Opciones de configuración avanzada

>[AZURE.NOTE]Esta sección se aplica actualmente solo a los clústeres de HDInsight basado en Windows.

### Personalización de clústeres mediante la personalización de clústeres de HDInsight

A veces desea configurar los archivos de configuración. Estos son algunos de ellos.

- core-site.xml
- hdfs-site.xml
- mapred-site.xml
- yarn-site.xml
- hive-site.xml
- oozie-site.xml

Los clústeres no pueden conservar los cambios debido a la recreación de imágenes. Para obtener más información, consulte [Role Instance Restarts Due to OS Upgrades](http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx). Para mantener los cambios durante la vida útil de los clústeres, puede usar la personalización de clústeres de HDInsight durante el proceso de creación.

El siguiente es un ejemplo de script de Azure PowerShell sobre cómo personalizar una configuración de Hive:

	# hive-site.xml configuration
	$hiveConfigValues = @{ "hive.metastore.client.socket.timeout"="90" }
	
	$config = New-AzureRmHDInsightClusterConfig `
		| Set-AzureRmHDInsightDefaultStorage `
			-StorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
			-StorageAccountKey $defaultStorageAccountKey `
		| Add-AzureRmHDInsightConfigValues `
			-HiveSite $hiveConfigValues 
	
	New-AzureRmHDInsightCluster `
		-ResourceGroupName $existingResourceGroupName `
		-ClusterName $clusterName `
		-Location $location `
		-ClusterSizeInNodes $clusterSizeInNodes `
		-ClusterType Hadoop `
		-OSType Windows `
		-Version "3.2" `
		-HttpCredential $httpCredential `
		-Config $config 

Otros ejemplos de cómo personalizar otros archivos de configuración:

	# hdfs-site.xml configuration
	$HdfsConfigValues = @{ "dfs.blocksize"="64m" } #default is 128MB in HDI 3.0 and 256MB in HDI 2.1

	# core-site.xml configuration
	$CoreConfigValues = @{ "ipc.client.connect.max.retries"="60" } #default 50

	# mapred-site.xml configuration
	$MapRedConfigValues = @{ "mapreduce.task.timeout"="1200000" } #default 600000

	# oozie-site.xml configuration
	$OozieConfigValues = @{ "oozie.service.coord.normal.default.timeout"="150" }  # default 120

Para más información, vea el blog de Azim Uddin titulado [Customizing HDInsight Cluster creationg](http://blogs.msdn.com/b/bigdatasupport/archive/2014/04/15/customizing-hdinsight-cluster-provisioning-via-powershell-and-net-sdk.aspx) (Personalización de la creación de clústeres de HDInsight).




### Personalización de clústeres mediante la acción de script

Puede instalar componentes adicionales o personalizar la configuración del clúster mediante el uso de scripts durante la creación. Dichos scripts se invocan mediante la **acción de script**, una opción de configuración que se puede usar en el Portal de vista previa, los cmdlets de HDInsight de Windows PowerShell o el .NET SDK de HDInsight. Para obtener más información, consulte [Personalización de un clúster de HDInsight mediante la acción de script](hdinsight-hadoop-customize-cluster.md).


### Uso de redes virtuales de Azure

[Red virtual de Azure](http://azure.microsoft.com/documentation/services/virtual-network/) permite crear una red segura y persistente que contenga los recursos necesarios para una solución. Una red virtual permite hacer lo siguiente:

* Conectar recursos en la nube en una red privada (solo en la nube)

	![Diagrama de la configuración solo en la nube](./media/hdinsight-provision-clusters/hdinsight-vnet-cloud-only.png)

* Conectar sus recursos en la nube a la red de su centro de datos local (sitio a sitio o punto a sitio) usando una red privada virtual (VPN).

	La configuración sitio a sitio permite conectar varios recursos de su centro de datos a la red virtual de Azure usando una VPN de hardware o el servicio de enrutamiento y acceso remoto.

	![Diagrama de la configuración de sitio a sitio](./media/hdinsight-provision-clusters/hdinsight-vnet-site-to-site.png)

	La configuración punto a sitio permite conectar un recurso específico a la red virtual de Azure usando una VPN de software

	![Diagrama de la configuración de punto a sitio](./media/hdinsight-provision-clusters/hdinsight-vnet-point-to-site.png)

Para obtener más información sobre las características, las ventajas y la funcionalidad de Red virtual, consulte [Información general sobre Red virtual de Azure](../virtual-network/virtual-networks-overview.md).

> [AZURE.NOTE]Debe crear la red virtual de Azure antes de aprovisionar un clúster de HDInsight. Para más información, vea [Creación de un clúster de Hadoop en una red virtual](hdinsight-hbase-provision-vnet.md#provision-an-hbase-cluster-into-a-virtual-network).
>
> HDInsight de Azure solo admite redes virtuales basadas en la ubicación y actualmente no funciona con redes virtuales basadas en grupos de afinidad. Utilice el cmdlet Get-AzureVNetConfig de Azure PowerShell para comprobar si una red virtual existente de Azure está basada en la ubicación. Si la red virtual no está basada en la ubicación, tiene las opciones siguientes:
>
> - Exporte la configuración de la red virtual existente y luego cree una red virtual nueva. De manera predeterminada, las redes virtuales nuevas están basadas en la ubicación.
> - Migre a una red virtual basada en la ubicación. Vea [Migración de los servicios existentes a un ámbito regional](http://azure.microsoft.com/blog/2014/11/26/migrating-existing-services-to-regional-scope/).
>
> Se recomienda encarecidamente designar una única subred para un clúster.

## Creación mediante el Portal de vista previa

Puede revisar la sección [opciones de configuración básica](#basic-configuration-options), y la sección [opciones de configuración avanzada](#advanced-configuration-options) para ver las explicaciones de los campos.

**Para crear un clúster de HDInsight**

1. Inicie sesión en el [portal de vista previa de Azure][azure-preview-portal].
2. Haga clic en **NUEVO**, en **Análisis de datos** y, luego, en **HDInsight**.

    ![Crear un nuevo clúster en el Portal de vista previa de Azure](./media/hdinsight-provision-clusters/HDI.CreateCluster.1.png "Crear un nuevo clúster en el Portal de vista previa de Azure")

3. Escriba o seleccione los valores siguientes:

  * **Nombre del clúster**: escriba un nombre para el clúster. Si el nombre está disponible, aparecerá una marca de verificación verde junto al nombre del clúster.
  * **Tipo de clúster**: seleccione **Hadoop**.
  * **Sistema operativo del clúster**: seleccione **Windows Server 2012 R2 Datacenter**.
  * **Suscripción**: seleccione la suscripción de Azure que se usará para crear este clúster.
  * **Grupo de recursos**: seleccione un grupo de recursos existente o cree uno nuevo. Esta entrada se establecerá de manera predeterminada en uno de sus grupos de recursos existentes, si hay alguno disponible.
  * **Credenciales**: configure el nombre de usuario y la contraseña del usuario de Hadoop (usuario HTTP). Si habilita Escritorio remoto para el clúster, deberá configurar el nombre de usuario y la contraseña del Escritorio remoto y una fecha de caducidad de la cuenta. Haga clic en **Seleccionar** en la parte inferior para guardar los cambios.

	   	![Provide cluster credentials](./media/hdinsight-provision-clusters/HDI.CreateCluster.3.png "Provide cluster credentials")

  * **Origen de datos**: cree una cuenta de Almacenamiento de Azure o seleccione una ya existente para usar como sistema de archivos predeterminado para el clúster.

   		![Data source blade](./media/hdinsight-provision-clusters/HDI.CreateCluster.4.png "Provide data source configuration")

  		* **Selection Method**: Set this to **From all subscriptions** to enable browsing of storage accounts from all your subscriptions. Set this to **Access Key** if you want to enter the **Storage Name** and **Access Key** of an existing storage account.
  		* **Select storage account / Create New**: Click **Select storage account** to browse and select an existing storage account you want to associate with the cluster. Or, click **Create New** to create a new storage account. Use the field that appears to enter the name of the storage account. A green check will appear if the name is available.
  		* **Choose Default Container**: Use this to enter the name of the default container to use for the cluster. While you can enter any name here, we recommend using the same name as the cluster so that you can easily recognize that the container is used for this specific cluster.
  		* **Location**: The geographic region that the storage account is in, or will be created in. This location will determine the cluster location.  The cluster and its default storage account must co-locate in the same Azure data center.
  	
  * **Niveles de precios de nodo**: establezca el número de nodos de trabajo que requiere para el clúster. El costo estimado del clúster se mostrará en la hoja.
  

		![Node pricing tiers blade](./media/hdinsight-provision-clusters/HDI.CreateCluster.5.png "Specify number of cluster nodes")


  * **Configuración opcional** para seleccionar la versión del clúster y configurar otros valores opcionales como, por ejemplo, unir una **Red virtual** o configurar una **Tienda de metadatos externa** para almacenar datos de Hive y Oozie, use acciones de script para personalizar un clúster con el fin de instalar componentes personalizados o usar cuentas de almacenamiento adicionales con el clúster.

  		* **HDInsight Version**: Select the version you want to use for the cluster. For more information, see [HDInsight cluster versions](hdinsight-component-versioning.md).
  		* **Virtual Network**: Select an Azure virtual network and the subnet if you want to place the cluster into a virtual network.  

			![Virtual network blade](./media/hdinsight-provision-clusters/HDI.CreateCluster.6.png "Specify virtual network details")

			>[AZURE.NOTE] Windows based HDInsight cluster can only be placed into a classical virtual network.
  

  		
		* **External Metastores**: Specify an Azure SQL database to store Hive and Oozie metadata associated with the cluster.
 
            > [AZURE.NOTE] Metastore configuration is not available for HBase cluster types.

			![Custom metastores blade](./media/hdinsight-provision-clusters/HDI.CreateCluster.7.png "Specify external metastores")


			For **Use an existing SQL DB for Hive** metadata, click **Yes**, select a SQL database, and then provide the username/password for the database. Repeat these steps if you want to **Use an existing SQL DB for Oozie metadata**. Click **Select** till you are back on the **Optional Configuration** blade.


			>[AZURE.NOTE] The Azure SQL database used for the metastore must allow connectivity to other Azure services, including Azure HDInsight. On the Azure SQL database dashboard, on the right side, click the server name. This is the server on which the SQL database instance is running. Once you are on the server view, click **Configure**, and then for **Azure Services**, click **Yes**, and then click **Save**.
		
  		* **Script Actions** if you want to use a custom script to customize a cluster, as the cluster is being created. For more information about script actions, see [Customize HDInsight clusters using Script Action](hdinsight-hadoop-customize-cluster.md). On the Script Actions blade provide the details as shown in the screen capture.
  	

			![Script action blade](./media/hdinsight-provision-clusters/HDI.CreateCluster.8.png "Specify script action")


    	* **Azure Storage Keys**: Specify additional storage accounts to associate with the cluster. In the **Azure Storage Keys** blade, click **Add a storage key**, and then select an existing storage account or create a new account.
    

			![Additional storage blade](./media/hdinsight-provision-clusters/HDI.CreateCluster.9.png "Specify additional storage accounts")


4. Haga clic en **Crear**. Si selecciona **Anclar a Panel de inicio** se agregará un icono del clúster en el Panel de inicio de su portal de vista previa. El icono indicará que el clúster se está creando y cambiará para mostrar el icono de HDInsight cuando se haya completado el proceso.


	| Al crear | Creación finalizada |
	| ------------------ | --------------------- |
	| ![Indicador de aprovisionamiento en el panel de inicio](./media/hdinsight-provision-clusters/provisioning.png) | ![Icono de clúster aprovisionado](./media/hdinsight-provision-clusters/provisioned.png) |


	
	> [AZURE.NOTE]El clúster tardará algo de tiempo en crearse, normalmente unos 15 minutos. Use el icono del Panel de inicio o la entrada **Notificaciones** de la izquierda de la página para comprobar el proceso de aprovisionamiento.
	

5. Cuando termine la creación, haga clic en el icono del clúster desde el panel de inicio para iniciar la hoja del clúster. La hoja de clúster proporciona información esencial sobre el clúster, como el nombre, el grupo de recursos al que pertenece, la ubicación, el sistema operativo, la dirección URL para el panel del clúster, etc.


	![Hoja de clúster](./media/hdinsight-provision-clusters/HDI.Cluster.Blade.png "Propiedades de clúster")


	Use la siguiente información para comprender los iconos de la parte superior de esta hoja y de la sección **Conceptos básicos**:


	* **Configuración** y **Toda la configuración**: muestra la hoja **Configuración** del clúster, que permite obtener acceso a información de configuración detallada para el clúster.
	* **Panel**, **Panel del clúster** y **URL**: se trata de formas de tener acceso al panel del clúster, que es un portal web que ejecuta trabajos en el clúster.
	* **Escritorio remoto**: permite habilitar o deshabilitar Escritorio remoto en los nodos del clúster.
	* **Escalar clúster**: permite cambiar el número de nodos de trabajo para este clúster.
	* **Eliminar**: elimina el clúster de HDInsight.
	* **Inicio rápido** (![icono de nube y rayo = inicio rápido](./media/hdinsight-provision-clusters/quickstart.png)): muestra información que le ayudará a empezar a usar HDInsight.
	* **Usuarios** (![icono de usuarios](./media/hdinsight-provision-clusters/users.png)): permite establecer permisos para la _administración del portal_ de este clúster para otros usuarios de su suscripción de Azure.
	

		> [AZURE.IMPORTANT]Esto _solo_ afecta al acceso y los permisos para este clúster en el Portal de vista previa, y no tiene ningún efecto sobre quién puede conectarse o enviar trabajos al clúster de HDInsight.
		
	* **Etiquetas** (![icono de etiqueta](./media/hdinsight-provision-clusters/tags.png)): las etiquetas permiten establecer pares de clave-valor para definir una taxonomía personalizada de sus servicios en la nube. Por ejemplo, puede crear una clave denominada __proyecto__ y luego usar un valor común para todos los servicios asociados a un proyecto específico.


## Creación mediante una plantilla ARM

La plantilla de Administrador de recursos de Azure (ARM) facilita la implementación y la reimplementación del clúster. El procedimiento siguiente crea el clúster de HDInsight basado en Linux.

**Para implementar un clúster mediante la plantilla ARM**

1. Guarde el archivo json en el [Apéndice A](#appendix-a---arm-template) de la estación de trabajo.
2. Cree los parámetros si es necesario.
3. Ejecute la plantilla con siguiente script de PowerShell:

		$subscriptionId = "<Azure Subscription ID"
		
		$newResourceGroupName = "<Azure Resource Group Name>"
		$Location = "EAST US 2" # for creating ARM group
				
		$armDeploymentName = "New-HDInsigt-Cluster-" + (Get-Date -Format MMdd)
		$newClusterName = "<HDInsight Cluster Name>"
		$clusterStorageAccountName = "<Default Storage Account Name>"
				
		# Connect to Azure
		#Login-AzureRmAccount
		#Select-AzureRmSubscription -SubscriptionId $subscriptionId
				
		# Create a resource group
		New-AzureRmResourceGroup -Name $newResourceGroupName -Location $Location
				
		# Create cluster and the dependent storage accounge
		$parameters = @{clusterName="$newClusterName";clusterStorageAccountName="$clusterStorageAccountName"}
				
		New-AzureRmResourceGroupDeployment `
			-Name $armDeploymentName `
			-ResourceGroupName $newResourceGroupName `
			-TemplateFile E:\HDITutorials-ARM\Create-clusters\hdinsight-arm-template.json `
			-TemplateParameterObject $parameters
				
		# List cluster
		Get-AzureRmHDInsightCluster -ResourceGroupName $newResourceGroupName -ClusterName $newClusterName 

	El script de PowerShell solo configura el nombre del clúster y el nombre de la cuenta de almacenamiento. Puede establecer otros valores en la plantilla ARM.
	
Para implementar una plantilla del ARM con otros métodos, vea [Implementación de una aplicación con la plantilla del Administrador de recursos de Azure](resource-group-template-deploy.md).


## Creación con Azure PowerShell
Azure PowerShell es un potente entorno de scripting que puede usar para controlar y automatizar la implementación y la administración de sus cargas de trabajo en Azure. Esta sección proporciona instrucciones sobre cómo aprovisionar un clúster de HDInsight con Azure PowerShell. Para obtener información sobre la configuración de una estación de trabajo para que ejecute cmdlets de HDInsight Windows PowerShell, consulte [Instalación y configuración de Azure PowerShell](../install-configure-powershell.md). Para obtener más información sobre el uso de Azure PowerShell con HDInsight, consulte [Administración de HDInsight con PowerShell](hdinsight-administer-use-powershell.md). Para obtener una lista de los cmdlets de HDInsight Windows PowerShell, consulte [Documentación de referencia de los cmdlets de HDInsight](https://msdn.microsoft.com/library/azure/dn858087.aspx).


Los siguientes procedimientos son necesarios para crear un clúster de HDInsight con Azure PowerShell:

- Creación de un grupo de recursos de Azure
- Crear una cuenta de Almacenamiento de Azure
- Creación de un contenedor de blobs de Azure
- Creación de un clúster de HDInsight

	$subscriptionId = "<Azure Subscription ID>"
	
	$newResourceGroupName = "<Azure Resource Group Name>" $location = "<Azure Location>" # for example, "East US 2" $newDefaultStorageAccountName = "<Azure Storage Account Name>" $newClusterName = "<Azure HDInsight Cluster Name>" $clusterSizeInNodes = 1
	
	###########################################
	# Inicio de sesión en Azure
	########################################### Login-AzureRmAccount Select-AzureRmSubscription -SubscriptionId $subscriptionId
	
	###########################################
	# Creación del grupo de recursos
	########################################### New-AzureRmResourceGroup -Name $newRresourceGroupName -Location $location
	
	###########################################
	# Preparar el contenedor y la cuenta de almacenamiento predeterminados
	########################################### New-AzureRmStorageAccount -ResourceGroupName $newResourceGroupName -Name $newDefaultStorageAccountName -Location $location
	
	$defaultStorageAccountKey = Get-AzureRmStorageAccountKey -ResourceGroupName $newResourceGroupName -Name $newDefaultStorageAccountName | %{ $\_.Key1 } $defaultStorageContext = New-AzureStorageContext -StorageAccountName $newDefaultStorageAccountName -StorageAccountKey $defaultStorageAccountKey New-AzureStorageContainer -Name $newClusterName -Context $defaultStorageContext #use the cluster name as the container name
		
	###########################################
	# Creación de clústeres
	########################################### $httpCredential =Get-Credential -Message "Escribir la credencial de cuenta HTTP:" New-AzureRmHDInsightCluster `
		-ResourceGroupName $newResourceGroupName ` -ClusterName $newClusterName `
		-Location $location ` -ClusterSizeInNodes $clusterSizeInNodes `
		-ClusterType Hadoop ` -OSType Windows `
		-Version "3.2" ` -HttpCredential $httpCredential



## Creación con .NET SDK de HDInsight
El SDK .NET de HDInsight proporciona bibliotecas de cliente .NET que facilitan el trabajo con HDInsight desde una aplicación .NET Framework. Siga las instrucciones siguientes para crear una aplicación de consola de Visual Studio y pegar el código para crear un clúster.

**Para crear una aplicación de consola de Visual Studio**

1. Cree una aplicación de consola en C# mediante Visual Studio.
2. Ejecute el siguiente comando Nuget en la Consola del administrador de paquetes NuGet:

		Install-Package Microsoft.Azure.Common.Authentication -pre
		Install-Package Microsoft.Azure.Management.HDInsight -Pre

6. En el Explorador de soluciones, haga doble clic en **Program.cs** para abrirlo, pegue el siguiente código y especifique valores para las variables:

		using System;
		using System.Security;
		using Microsoft.Azure;
		using Microsoft.Azure.Common.Authentication;
		using Microsoft.Azure.Common.Authentication.Factories;
		using Microsoft.Azure.Common.Authentication.Models;
		using Microsoft.Azure.Management.HDInsight;
		using Microsoft.Azure.Management.HDInsight.Models;
		
		namespace CreateHDInsightCluster
		{
			class Program
			{
				private static HDInsightManagementClient _hdiManagementClient;
		
				private static Guid SubscriptionId = new Guid("<Azure Subscription ID");
				private const string ExistingResourceGroupName = "<Azure Resource Group Name>";
				private const string ExistingStorageName = "<Default Storage Account Name>.blob.core.windows.net";
				private const string ExistingStorageKey = "<Default Storage Account Key>";
				private const string ExistingBlobContainer = "<Default Blob Container Name>";
				private const string NewClusterName = "<HDInsight Cluster Name>";
				private const int NewClusterNumNodes = 1;
				private const string NewClusterLocation = "EAST US 2";     // Must be the same as the default Storage account
				private const OSType NewClusterOsType = OSType.Windows;
				private const HDInsightClusterType NewClusterType = HDInsightClusterType.Hadoop;
				private const string NewClusterVersion = "3.2";
				private const string NewClusterUsername = "admin";
				private const string NewClusterPassword = "<HTTP User password";
		
				static void Main(string[] args)
				{
					System.Console.WriteLine("Running");
		
					var tokenCreds = GetTokenCloudCredentials();
					var subCloudCredentials = GetSubscriptionCloudCredentials(tokenCreds, SubscriptionId);
		
					_hdiManagementClient = new HDInsightManagementClient(subCloudCredentials);
				
					var parameters = new ClusterCreateParameters
					{
						ClusterSizeInNodes = NewClusterNumNodes,
						UserName = NewClusterUsername,
						Password = NewClusterPassword,
						Location = NewClusterLocation,
						DefaultStorageAccountName = ExistingStorageName,
						DefaultStorageAccountKey = ExistingStorageKey,
						DefaultStorageContainer = ExistingBlobContainer,
						ClusterType = NewClusterType,
						OSType = NewClusterOsType
					};
		
					_hdiManagementClient.Clusters.Create(ExistingResourceGroupName, NewClusterName, parameters);
				}
				private static void CreateCluster()
				{
					var parameters = new ClusterCreateParameters
					{
						ClusterSizeInNodes = NewClusterNumNodes,
						UserName = NewClusterUsername,
						Password = NewClusterPassword,
						Location = NewClusterLocation,
						DefaultStorageAccountName = ExistingStorageName,
						DefaultStorageAccountKey = ExistingStorageKey,
						DefaultStorageContainer = ExistingBlobContainer,
						ClusterType = NewClusterType,
						OSType = NewClusterOsType
					};
		
					_hdiManagementClient.Clusters.Create(ExistingResourceGroupName, NewClusterName, parameters);
				}
		
				public static TokenCloudCredentials GetTokenCloudCredentials(string username = null, SecureString password = null)
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
		
				public static SubscriptionCloudCredentials GetSubscriptionCloudCredentials(TokenCloudCredentials creds, Guid subId)
				{
					return new TokenCloudCredentials(subId.ToString(), creds.Token);
		
				}
			}
		}

7. Presione **F5** para ejecutar la aplicación. Una ventana de consola se abrirá y mostrará el estado de la aplicación. También se le pedirá que escriba las credenciales de la cuenta de Azure. La creación del clúster de HDInsight puede durar varios minutos.

## Creación mediante SQL Server Integration Services local

También puede usar SQL Server Integration Services (SSIS) para crear o eliminar un clúster de HDInsight. El paquete de características de Azure para SSIS proporciona los siguientes componentes que funcionan con clústeres de HDInsight.


- [Tarea de creación de un clúster de Azure HDInsight][ssisclustercreate]
- [Tarea de eliminación de un clúster de Azure HDInsight][ssisclusterdelete]
- [Administrador de conexiones de suscripción de Azure][connectionmanager]

Obtenga más información sobre el paquete de características de Azure para SSIS [aquí][ssispack].


##Pasos siguientes
En este artículo, ha aprendido varias maneras de crear un clúster de HDInsight. Para obtener más información, consulte los artículos siguientes:

* [Introducción a HDInsight de Azure](hdinsight-get-started.md): aprenda a empezar a trabajar con su clúster de HDInsight
* [Uso de Sqoop con HDInsight](hdinsight-use-sqoop.md): aprenda copiar datos entre HDInsight y Base de datos SQL o SQL Server
* [Administración de HDInsight con PowerShell](hdinsight-administer-use-powershell.md): aprenda a trabajar con HDInsight mediante PowerShell
* [Envío de trabajos de Hadoop mediante programación](hdinsight-submit-hadoop-jobs-programmatically.md): aprenda a enviar trabajos a HDInsight mediante programación
* [Documentación del SDK de HDInsight de Azure][hdinsight-sdk-documentation]\: descubra el SDK de HDInsight



##Apéndice A - plantilla ARM

La siguiente plantilla de Administrador de recursos de Azure crea un clúster de Hadoop con la cuenta de Almacenamiento de Azure dependiente.

	{
	  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
	  "contentVersion": "1.0.0.0",
	  "parameters": {
	    "location": {
	      "type": "string",
	      "defaultValue": "North Europe",
	      "allowedValues": [
	        "North Europe"
	      ],
	      "metadata": {
	        "description": "The location where all azure resources will be deployed."
	      }
	    },
	    "clusterName": {
	      "type": "string",
	      "metadata": {
	        "description": "The name of the HDInsight cluster to create."
	      }
	    },
	    "clusterLoginUserName": {
	      "type": "string",
	      "defaultValue": "admin",
	      "metadata": {
	        "description": "These credentials can be used to submit jobs to the cluster and to log into cluster dashboards."
	      }
	    },
	    "clusterLoginPassword": {
	      "type": "securestring",
	      "metadata": {
	        "description": "The password for the cluster login."
	      }
	    },
	    "sshUserName": {
	      "type": "string",
	      "defaultValue": "username",
	      "metadata": {
	        "description": "These credentials can be used to remotely access the cluster and the edge node virtual machine."
	      }
	    },
	    "sshPassword": {
	      "type": "securestring",
	      "metadata": {
	        "description": "The password for the ssh user."
	      }
	    },
	    "clusterStorageAccountName": {
	      "type": "string",
	      "metadata": {
	        "description": "The name of the storage account to be created and be used as the cluster's storage."
	      }
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
	    "clusterWorkerNodeCount": {
	      "type": "int",
	      "defaultValue": 4,
	      "metadata": {
	        "description": "The number of nodes in the HDInsight cluster."
	      }
	    }
	  },
	  "variables": {},
	  "resources": [
	    {
	      "name": "[parameters('clusterStorageAccountName')]",
	      "type": "Microsoft.Storage/storageAccounts",
	      "location": "[parameters('location')]",
	      "apiVersion": "2015-05-01-preview",
	      "dependsOn": [],
	      "tags": {},
	      "properties": {
	        "accountType": "[parameters('clusterStorageType')]"
	      }
	    },
	    {
	      "name": "[parameters('clusterName')]",
	      "type": "Microsoft.HDInsight/clusters",
	      "location": "[parameters('location')]",
	      "apiVersion": "2015-03-01-preview",
	      "dependsOn": [
	        "[concat('Microsoft.Storage/storageAccounts/',parameters('clusterStorageAccountName'))]"
	      ],
	      "tags": {},
	      "properties": {
	        "clusterVersion": "3.2",
	        "osType": "Linux",
	        "clusterDefinition": {
	          "kind": "hadoop",
	          "configurations": {
	            "gateway": {
	              "restAuthCredential.isEnabled": true,
	              "restAuthCredential.username": "[parameters('clusterLoginUserName')]",
	              "restAuthCredential.password": "[parameters('clusterLoginPassword')]"
	            }
	          }
	        },
	        "storageProfile": {
	          "storageaccounts": [
	            {
	              "name": "[concat(parameters('clusterStorageAccountName'),'.blob.core.windows.net')]",
	              "isDefault": true,
	              "container": "[parameters('clusterName')]",
	              "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('clusterStorageAccountName')), '2015-05-01-preview').key1]"
	            }
	          ]
	        },
	        "computeProfile": {
	          "roles": [
	            {
	              "name": "headnode",
	              "targetInstanceCount": "1",
	              "hardwareProfile": {
	                "vmSize": "Large"
	              },
	              "osProfile": {
	                "linuxOperatingSystemProfile": {
	                  "username": "[parameters('sshUserName')]",
	                  "password": "[parameters('sshPassword')]"
	                }
	              }
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
	              }
	            }
	          ]
	        }
	      }
	    }
	  ],
	  "outputs": {
	    "cluster": {
	      "type": "object",
	      "value": "[reference(resourceId('Microsoft.HDInsight/clusters',parameters('clusterName')))]"
	    }
	  }
	}


[hdinsight-sdk-documentation]: http://msdn.microsoft.com/library/dn479185.aspx
[azure-preview-portal]: https://manage.windowsazure.com
[connectionmanager]: http://msdn.microsoft.com/library/mt146773(v=sql.120).aspx
[ssispack]: http://msdn.microsoft.com/library/mt146770(v=sql.120).aspx
[ssisclustercreate]: http://msdn.microsoft.com/library/mt146774(v=sql.120).aspx
[ssisclusterdelete]: http://msdn.microsoft.com/library/mt146778(v=sql.120).aspx

<!---HONumber=Nov15_HO4-->