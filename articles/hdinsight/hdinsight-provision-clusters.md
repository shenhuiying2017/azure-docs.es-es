<properties
   pageTitle="Aprovisionamiento personalizado de clústeres de Hadoop en HDInsight | Microsoft Azure"
   	description="Obtenga información sobre cómo aprovisionar clústeres de manera personalizada para HDInsight de Azure mediante el Portal de vista previa de Azure, Azure PowerShell, una línea de comandos o el SDK de .NET."
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
   ms.date="09/03/2015"
   ms.author="jgao"/>

# Aprovisionamiento de clústeres de Hadoop en HDInsight

Aprenda a planear el aprovisionamiento de los clústeres de HDInsight.

[AZURE.INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

* [Aprovisionamiento de clústeres de Hadoop en HDInsight](hdinsight-provision-clusters-v1.md)

[AZURE.INCLUDE [hdinsight-azure-preview-portal](../../includes/hdinsight-azure-preview-portal.md)]

* [Aprovisionamiento de clústeres de Hadoop en HDInsight](hdinsight-provision-clusters-v1.md)

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

	Puede aprovisionar clústeres de HDInsight en uno de los dos sistemas operativos siguientes: - **HDInsight en Windows (Windows Server 2012 R2 Datacenter)**: - **HDInsight en Linux (Ubuntu 12.04 LTS para Linux) (vista previa)**: HDInsight ofrece la opción de configurar clústeres de Linux en Azure. Configure un clúster de Linux si conoce Linux o Unix, migrando desde una solución existente de Hadoop basado en Linux, o si desea una integración fácil con componentes del ecosistema de Hadoop creados para Linux. Para obtener más información, vea [Introducción a Hadoop en Linux en HDInsight](hdinsight-hadoop-linux-get-started.md).


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

	Dentro de un tipo de clúster determinado, existen distintos roles para los diversos nodos, los que permiten que un cliente dimensione esos nodos en un rol determinado según los detalles de su carga de trabajo. Por ejemplo, un clúster de Hadoop puede aprovisionar sus nodos de trabajo con una gran cantidad de memoria si el tipo de análisis que se realiza requiere mucha memoria.

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

	Durante la configuración, debe especificar una cuenta de almacenamiento de Azure y un contenedor de almacenamiento de blobs de Azure en la cuenta de almacenamiento de Azure. Algunos de los procesos de aprovisionamiento requieren que la cuenta de almacenamiento de Azure y el contenedor de almacenamiento de blobs se creen con antelación. El clúster utiliza el contenedor de almacenamiento de blobs como la ubicación de almacenamiento predeterminada. De manera opcional, puede especificar cuentas de almacenamiento de Azure adicionales (almacenamiento vinculado) al que podrá tener acceso el clúster. Además, el clúster también podrá tener acceso a cualquier contenedor de blobs que esté configurado con acceso de lectura público completo o con acceso de lectura público solo para blobs. Para obtener más información sobre el acceso restringido, consulte [Administración del acceso a los recursos de Almacenamiento de Azure](storage-manage-access-to-resources.md).

	![Almacenamiento de HDInsight](./media/hdinsight-provision-clusters/HDInsight.storage.png)

	>[AZURE.NOTE]Un contenedor de almacenamiento de blobs proporciona una agrupación de un conjunto de blobs, tal como se muestra en la imagen:

	![Almacenamiento de blobs de Azure](./media/hdinsight-provision-clusters/Azure.blob.storage.jpg)


	>[AZURE.WARNING]No comparta un contenedor de almacenamiento de blobs para varios clústeres, ya que no es compatible.

	Para obtener más información sobre el uso de almacenes de blobs secundarios, consulte [Uso del almacenamiento de blobs de Azure con HDInsight](hdinsight-use-blob-storage.md).

- **Tienda de metadatos Hive/Oozie**

	La tienda de metadatos contiene metadatos de Hive y Oozie, como columnas, esquemas, particiones y tablas de Hive. El uso de la tienda de metadatos le ayuda a conservar sus metadatos de Hive y Oozie, por lo que no es necesario volver a crear tablas de Hive o trabajos de Oozie al aprovisionar un nuevo clúster. De forma predeterminada, Hive utiliza una base de datos SQL de Azure incrustada para almacenar esta información. La base de datos incrustada no puede conservar los metadatos cuando se elimina el clúster. Por ejemplo, tiene un clúster aprovisionado con una tienda de metadatos de Hive. Creó algunas tablas de Hive. Después de eliminar el clúster y de volverlo a crear con la misma tienda de metadatos de Hive, podrá ver las tablas de Hive que creó en el clúster original.

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

Los clústeres no pueden conservar los cambios debido a la recreación de imágenes. Para obtener más información, consulte [Role Instance Restarts Due to OS Upgrades](http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx). Para mantener los cambios por toda la duración de los clústeres, puede utilizar la personalización de clústeres de HDInsight durante el proceso de aprovisionamiento.

El siguiente es un ejemplo de script de Azure PowerShell sobre cómo personalizar una configuración de Hive:

	# hive-site.xml configuration
	$hiveConfigValues = new-object 'Microsoft.WindowsAzure.Management.HDInsight.Cmdlet.DataObjects.AzureHDInsightHiveConfiguration'
	$hiveConfigValues.Configuration = @{ "hive.metastore.client.socket.timeout"="90" } #default 60

	$config = New-AzureHDInsightClusterConfig `
	            -ClusterSizeInNodes $clusterSizeInNodes `
	            -ClusterType $clusterType `
	          | Set-AzureHDInsightDefaultStorage `
	            -StorageAccountName $defaultStorageAccount `
	            -StorageAccountKey $defaultStorageAccountKey `
	            -StorageContainerName $defaultBlobContainer `
	          | Add-AzureHDInsightConfigValues `
	            -Hive $hiveConfigValues

	New-AzureHDInsightCluster -Name $clusterName -Location $location -Credential $credential -OSType Windows -Config $config

Otros ejemplos de cómo personalizar otros archivos de configuración:

	# hdfs-site.xml configuration
	$HdfsConfigValues = @{ "dfs.blocksize"="64m" } #default is 128MB in HDI 3.0 and 256MB in HDI 2.1

	# core-site.xml configuration
	$CoreConfigValues = @{ "ipc.client.connect.max.retries"="60" } #default 50

	# mapred-site.xml configuration
	$MapRedConfigValues = new-object 'Microsoft.WindowsAzure.Management.HDInsight.Cmdlet.DataObjects.AzureHDInsightMapReduceConfiguration'
	$MapRedConfigValues.Configuration = @{ "mapreduce.task.timeout"="1200000" } #default 600000

	# oozie-site.xml configuration
	$OozieConfigValues = new-object 'Microsoft.WindowsAzure.Management.HDInsight.Cmdlet.DataObjects.AzureHDInsightOozieConfiguration'
	$OozieConfigValues.Configuration = @{ "oozie.service.coord.normal.default.timeout"="150" }  # default 120

Para obtener más información, consulte el blog de Azim Uddin titulado [Personalización del aprovisionamiento de clústeres de HDInsight](http://blogs.msdn.com/b/bigdatasupport/archive/2014/04/15/customizing-hdinsight-cluster-provisioning-via-powershell-and-net-sdk.aspx).




### Personalización de clústeres mediante la acción de script

Puede instalar componentes adicionales o personalizar la configuración del clúster mediante secuencias de comandos durante el aprovisionamiento. Dichos scripts se invocan mediante la **acción de script**, una opción de configuración que se puede usar en el Portal de vista previa, los cmdlets de HDInsight de Windows PowerShell o el .NET SDK de HDInsight. Para obtener más información, consulte [Personalización de un clúster de HDInsight mediante la acción de script](hdinsight-hadoop-customize-cluster.md).


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

> [AZURE.NOTE]Debe crear la red virtual de Azure antes de aprovisionar un clúster de HDInsight. Para obtener más información, consulte [Aprovisionar un clúster de Hadoop en una red virtual](hdinsight-hbase-provision-vnet.md#provision-an-hbase-cluster-into-a-virtual-network).
>
> HDInsight de Azure solo admite redes virtuales basadas en la ubicación y actualmente no funciona con redes virtuales basadas en grupos de afinidad. Utilice el cmdlet Get-AzureVNetConfig de Azure PowerShell para comprobar si una red virtual existente de Azure está basada en la ubicación. Si la red virtual no está basada en la ubicación, tiene las opciones siguientes:
>
> - Exporte la configuración de la red virtual existente y luego cree una red virtual nueva. De manera predeterminada, las redes virtuales nuevas están basadas en la ubicación.
> - Migre a una red virtual basada en la ubicación. Consulte [Migración de los servicios existentes a un ámbito regional](http://azure.microsoft.com/blog/2014/11/26/migrating-existing-services-to-regional-scope/).
>
> Se recomienda encarecidamente designar una única subred para un clúster.

## Aprovisionamiento mediante el Portal de vista previa

Puede revisar la sección [opciones de configuración básica] y la sección [opciones de configuración avanzada] para ver las explicaciones de los campos.

**Para crear un clúster de HDInsight**

1. Inicie sesión en el [Portal de vista previa de Azure][azure-preview-portal].
2. Haga clic en **NUEVO**, en **Análisis de datos** y, luego, en **HDInsight**.

    ![Crear un nuevo clúster en el Portal de vista previa de Azure](./media/hdinsight-provision-clusters/HDI.CreateCluster.1.png "Crear un nuevo clúster en el Portal de vista previa de Azure")

3. Escriba o seleccione los valores siguientes:

  * **Nombre del clúster**: escriba un nombre para el clúster. Si el nombre está disponible, aparecerá una marca de verificación verde junto al nombre del clúster.
  * **Tipo de clúster**: seleccione **Hadoop**.
  * **Sistema operativo del clúster**: seleccione **Windows Server 2012 R2 Datacenter**.
  * **Suscripción**: seleccione la suscripción de Azure que se usará para aprovisionar este clúster.
  * **Grupo de recursos**: seleccione un grupo de recursos existente o cree uno nuevo. Esta entrada se establecerá de manera predeterminada en uno de sus grupos de recursos existentes, si hay alguno disponible.
  * **Credenciales**: configure el nombre de usuario y la contraseña del usuario de Hadoop (usuario HTTP). Si habilita Escritorio remoto para el clúster, deberá configurar el nombre de usuario y la contraseña del Escritorio remoto y una fecha de caducidad de la cuenta. Haga clic en **Seleccionar** en la parte inferior para guardar los cambios.

	   	![Provide cluster credentials](./media/hdinsight-provision-clusters/HDI.CreateCluster.3.png "Provide cluster credentials")

  * **Origen de datos**: cree una cuenta de Almacenamiento de Azure o seleccione una ya existente para usar como sistema de archivos predeterminado para el clúster.

   		![Data source blade](./media/hdinsight-provision-clusters/HDI.CreateCluster.4.png "Provide data source configuration")

  		* **Método de selección**: establézcalo en **De todas las suscripciones** para habilitar la exploración de cuentas de almacenamiento en todas sus suscripciones. Establézcalo en **Clave de acceso** si desea especificar el **Nombre de almacenamiento** y la **Clave de acceso** de una cuenta de almacenamiento existente.
  		* **Seleccionar cuenta de almacenamiento/Crear nueva**: haga clic en **Seleccionar cuenta de almacenamiento** para examinar y seleccionar una cuenta de almacenamiento existente que desee asociar al clúster. O bien, haga clic en **Crear nueva** para crear otra cuenta de almacenamiento. Use el campo que aparece para especificar el nombre de la cuenta de almacenamiento. Si el nombre está disponible, aparecerá una marca de verificación verde.
  		* **Elegir contenedor predeterminado**: usa esta opción para escribir el nombre del contenedor predeterminado que se usará para el clúster. Aunque se puede escribir cualquier nombre aquí, se recomienda usar el mismo nombre que el del clúster para que pueda reconocer fácilmente que el contenedor se usa para este clúster concreto.
  		* **Ubicación**: región geográfica en la que se encuentra o en la que se creará la cuenta de almacenamiento. Esta ubicación determinará la ubicación del clúster. El clúster y su cuenta de almacenamiento predeterminada debe colocarse en el mismo centro de datos Azure.
  	
  * **Niveles de precios de nodo**: establezca el número de nodos de trabajo que requiere para el clúster. El costo estimado del clúster se mostrará en la hoja.
  

		![Node pricing tiers blade](./media/hdinsight-provision-clusters/HDI.CreateCluster.5.png "Specify number of cluster nodes")


  * **Configuración opcional** para seleccionar la versión del clúster y configurar otros valores opcionales como, por ejemplo, unir una **Red virtual** o configurar una **Tienda de metadatos externa** para almacenar datos de Hive y Oozie, use acciones de script para personalizar un clúster con el fin de instalar componentes personalizados o usar cuentas de almacenamiento adicionales con el clúster.

  		* **Versión de HDInsight**: seleccione la versión que desee usar para el clúster. Para obtener más información, consulte [Versiones del clúster de HDInsight](hdinsight-component-versioning.md).
  		* **Red virtual**: seleccione una red virtual de Azure y la subred si desea colocar el clúster en una red virtual.  

			![Virtual network blade](./media/hdinsight-provision-clusters/HDI.CreateCluster.6.png "Specify virtual network details")

			>[AZURE.NOTE] El clúster de HDInsight basado en Windows solo se puede colocar en una red virtual clásica.
  

  		
		* **Tiendas de metadatos externas**: especifique una Base de datos SQL de Azure para guardar los metadatos de Hive y Oozie asociados con el clúster.
 

			![Custom metastores blade](./media/hdinsight-provision-clusters/HDI.CreateCluster.7.png "Specify external metastores")


			Para **Usar una Base de datos SQL existente para metadatos de Hive**, haga clic en **Sí**, seleccione una Base de datos SQL y, a continuación, proporcione el nombre de usuario y la contraseña para la base de datos. Repita estos pasos si desea **Usar una Base de datos SQL existente para los metadatos de Oozie**. Haga clic en **Seleccionar** hasta volver a la hoja **Configuración opcional**.


			>[AZURE.NOTE] La base de datos SQL de Azure usada para la tienda de metadatos debe permitir la conectividad con otros servicios de Azure, incluido HDInsight de Azure. En el panel de la base de datos SQL de Azure, en el lado derecho, haga clic en el nombre de servidor. Este es el servidor en el que se ejecuta la instancia de base de datos SQL. Cuando se encuentre en la vista de servidor, haga clic en **Configurar** y luego, en **Servicios de Azure**, haga clic en **Sí** y en **Guardar**.
		
  		* Seleccione **Acciones de script** si desea usar un script personalizado para personalizar un clúster, conforme se crea el clúster. Para obtener más información acerca de acciones de script, consulte [Personalización de un clúster de HDInsight mediante la acción de script](hdinsight-hadoop-customize-cluster.md). En la hoja Acciones de script, proporcione los detalles como se muestra en la captura de pantalla.
  	

			![Script action blade](./media/hdinsight-provision-clusters/HDI.CreateCluster.8.png "Specify script action")


    	* **Claves de Almacenamiento de Azure**: especifique cuentas de almacenamiento adicionales para asociar con el clúster. En la hoja **Claves de almacenamiento de Azure**, haga clic en **Agregar una clave de almacenamiento** y, a continuación, seleccione una cuenta de almacenamiento existente o cree una nueva cuenta.
    

			![Additional storage blade](./media/hdinsight-provision-clusters/HDI.CreateCluster.9.png "Specify additional storage accounts")


4. Haga clic en **Crear**. Si selecciona **Anclar a Panel de inicio** se agregará un icono del clúster en el Panel de inicio de su portal de vista previa. El icono indicará que el clúster está aprovisionando y cambiará para mostrar el icono de HDInsight cuando se haya completado el aprovisionamiento.


	| Durante el aprovisionamiento | Aprovisionamiento completado |
	| ------------------ | --------------------- |
	| ![Indicador de aprovisionamiento en el panel de inicio](./media/hdinsight-provision-clusters/provisioning.png) | ![Icono de clúster aprovisionado](./media/hdinsight-provision-clusters/provisioned.png) |


	
	> [AZURE.NOTE]El clúster tardará algo de tiempo en crearse, normalmente unos 15 minutos. Use el icono del Panel de inicio o la entrada **Notificaciones** de la izquierda de la página para comprobar el proceso de aprovisionamiento.
	

5. Una vez que termine el aprovisionamiento, haga clic en el icono del clúster desde el panel de inicio para iniciar la hoja del clúster. La hoja de clúster proporciona información esencial sobre el clúster, como el nombre, el grupo de recursos al que pertenece, la ubicación, el sistema operativo, la dirección URL para el panel del clúster, etc.


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


## Aprovisionamiento con la plantilla del Administrador de recursos de Azure

La plantilla de Administrador de recursos de Azure (ARM) facilita la implementación y la reimplementación del clúster. El procedimiento siguiente crea un clúster de Hadoop en el sistema operativo de Linux en el centro de datos de Europa del Norte con 4 nodos de trabajo.

**Para implementar un clúster mediante la plantilla ARM**

1. Guarde el archivo json en el apéndice A de la estación de trabajo.
2. Cree los parámetros si es necesario.
3. Ejecute la plantilla con siguiente script de PowerShell:

		$resourceGroupName = "<ResourceGroupName>"
		$Location = "<ResourceGroupLocation>"
		
		$armDeploymentName = "<ARMDeploymentName>"
		$clusterName = "<ClusterName>"
		$clusterStorageAccountName = "<DefaultStorageAccountName>"
		
		# Connect to Azure
		Switch-AzureMode -Name AzureResourceManager
		Add-AzureAccount
		
		# Create a resource group
		New-AzureResourceGroup -Name $resourceGroupName -Location $Location
		
		# Provision cluster and the dependent storage accounge
		$parameters = @{clusterName="$clusterName";clusterStorageAccountName="$clusterStorageAccountName"}
		
		New-AzureResourceGroupDeployment `
		    -Name $armDeploymentName `
		    -ResourceGroupName $resourceGroupName `
		    -TemplateFile E:\Tutorials\HDIARMTemplates\ARMTemplate-create-hadoop-cluster-with-storage.json `
		    -TemplateParameterObject $parameters
		
		# List cluster
		Get-AzureHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $clusterName

Para implementar una plantilla del ARM con otros métodos, vea [Implementación de una aplicación con la plantilla del Administrador de recursos de Azure](resource-group-template-deploy.md).


## Aprovisionamiento con Azure PowerShell
Azure PowerShell es un potente entorno de scripting que puede usar para controlar y automatizar la implementación y la administración de sus cargas de trabajo en Azure. Esta sección proporciona instrucciones sobre cómo aprovisionar un clúster de HDInsight con Azure PowerShell. Para obtener información sobre la configuración de una estación de trabajo para que ejecute cmdlets de HDInsight Windows PowerShell, consulte [Instalación y configuración de Azure PowerShell](../install-configure-powershell.md). Para obtener más información sobre el uso de Azure PowerShell con HDInsight, consulte [Administración de HDInsight con PowerShell](hdinsight-administer-use-powershell.md). Para obtener una lista de los cmdlets de HDInsight Windows PowerShell, consulte [Documentación de referencia de los cmdlets de HDInsight](https://msdn.microsoft.com/library/azure/dn858087.aspx).


Los siguientes procedimientos son necesarios para aprovisionar un clúster de HDInsight con Azure PowerShell:

- Creación de un grupo de recursos de Azure
- Crear una cuenta de Almacenamiento de Azure
- Creación de un contenedor de blobs de Azure
- Creación de un clúster de HDInsight


		# Use the new Azure Resource Manager mode
		Switch-AzureMode AzureResourceManager

		###########################################
		# Create required items, if none exist
		###########################################

		# Sign in
		Add-AzureAccount

		# Select the subscription to use
		$subscriptionName = "<SubscriptionName>"        # Provide your Subscription Name
		Select-AzureSubscription -SubscriptionName $subscriptionName

		# Register your subscription to use HDInsight
		Register-AzureProvider -ProviderNamespace "Microsoft.HDInsight" -Force

		# Create an Azure Resource Group
		$resourceGroupName = "<ResourceGroupName>"      # Provide a Resource Group name
		$location = "<Location>"                        # For example, "West US"
		New-AzureResourceGroup -Name $resourceGroupName -Location $location

		# Create an Azure Storage account
		$storageAccountName = "<StorageAcccountName>"   # Provide a Storage account name
		New-AzureStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName -Location $location -Type Standard_GRS

		# Create an Azure Blob Storage container
		$containerName = "<ContainerName>"              # Provide a container name
		$storageAccountKey = Get-AzureStorageAccountKey -Name $storageAccountName -ResourceGroupName $resourceGroupName | %{ $_.Key1 }
		$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
		New-AzureStorageContainer -Name $containerName -Context $destContext

		###########################################
		# Create an HDInsight Cluster
		###########################################

		# Skip these variables if you just created them
		$resourceGroupName = "<ResourceGroupName>"      # Provide the Resource Group name
		$storageAccountName = "<StorageAcccountName>"   # Provide the Storage account name
		$containerName = "<ContainerName>"              # Provide the container name
		$storageAccountKey = Get-AzureStorageAccountKey -Name $storageAccountName -ResourceGroupName $resourceGroupName | %{ $_.Key1 }

		# Set these variables
		$clusterName = $containerName           		# As a best practice, have the same name for the cluster and container
		$clusterNodes = <ClusterSizeInNodes>    		# The number of nodes in the HDInsight cluster
		$credentials = Get-Credential

		# The location of the HDInsight cluster. It must be in the same data center as the Storage account.
		$location = Get-AzureStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName | %{$_.Location}

		# Create a new HDInsight cluster
		New-AzureHDInsightCluster -ClusterName $clusterName -ResourceGroupName $resourceGroupName -HttpCredential $credentials -Location $location -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainer $containerName  -ClusterSizeInNodes $clusterNodes -ClusterType Hadoop


	![HDI.CLI.Provision](./media/hdinsight-provision-clusters/HDI.ps.provision.png)

## Aprovisionamiento con .NET SDK de HDInsight
El SDK .NET de HDInsight proporciona bibliotecas de cliente .NET que facilitan el trabajo con HDInsight desde una aplicación .NET Framework. Siga las instrucciones siguientes para crear una aplicación de consola de Visual Studio y pegar el código para crear un clúster.

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

5. En el menú **Herramientas**, haga clic en **Administrador de paquetes de NuGet** y luego en **Administrar paquetes de NuGet para soluciones**. En el cuadro de texto de búsqueda del cuadro de diálogo, busque **HDInsight**. En los resultados que se muestran, instale lo siguiente:

	 * Microsoft.Azure.Management.HDInsight
	 * Microsoft.Azure.Management.HDInsight.Job

	Busque Autenticación de Azure y, de los resultados que se muestran, instale **Microsoft.Azure.Common.Authentication**.

6. En el Explorador de soluciones, haga doble clic en **Program.cs** para abrirlo, pegue el siguiente código y especifique valores para las variables:


        using System;
		using System.Collections.Generic;
		using System.Diagnostics;
		using System.Linq;
		using System.Security;
		using System.Text;
		using System.Threading.Tasks;
		using Hyak.Common;
		using Microsoft.Azure;
		using Microsoft.Azure.Common.Authentication;
		using Microsoft.Azure.Common.Authentication.Models;
		using Microsoft.Azure.Management.HDInsight;
		using Microsoft.Azure.Management.HDInsight.Job;
		using Microsoft.Azure.Management.HDInsight.Job.Models;
		using Microsoft.Azure.Management.HDInsight.Models;
		using Newtonsoft.Json;


		namespace CreateHDICluster
		{
		    internal class Program
		    {
		        private static ProfileClient _profileClient;
		        private static SubscriptionCloudCredentials _cloudCredentials;
		        private static HDInsightManagementClient _hdiManagementClient;

		        private static Guid SubscriptionId = new Guid("<SubscriptionID>");
		        private const string ResourceGroupName = "<ResourceGroupName>";
		        private const string ExistingStorageName = "<storageaccountname>.blob.core.windows.net";
		        private const string ExistingStorageKey = "<account key>";
		        private const string ExistingContainer = "<container name>";
		        private const string NewClusterName = "<cluster name>";
		        private const int NewClusterNumNodes = <number of nodes>;
		        private const string NewClusterLocation = "<location>";		//should be same as the storage account
		        private const OSType NewClusterOsType = OSType.Windows;
		        private const HDInsightClusterType NewClusterType = HDInsightClusterType.Hadoop;
		        private const string NewClusterVersion = "3.2";
		        private const string NewClusterUsername = "admin";
		        private const string NewClusterPassword = "<password>";

		        private static void Main(string[] args)
		        {
		            System.Console.WriteLine("Start cluster provisioning");

		            _profileClient = GetProfile();
		            _cloudCredentials = GetCloudCredentials();
		            _hdiManagementClient = new HDInsightManagementClient(_cloudCredentials);

		            System.Console.WriteLine(String.Format("Creating the cluster {0}...", NewClusterName));
		            CreateCluster();
		            System.Console.WriteLine("Done. Press any key to continue.");
		            System.Console.ReadKey(true);
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
		                DefaultStorageContainer = ExistingContainer,
		                ClusterType = NewClusterType,
		                OSType = NewClusterOsType
		            };

		            _hdiManagementClient.Clusters.Create(ResourceGroupName, NewClusterName, parameters);
		        }

		        private static ProfileClient GetProfile(string username = null, SecureString password = null)
		        {
		            var profileClient = new ProfileClient(new AzureProfile());
		            var env = profileClient.GetEnvironmentOrDefault(EnvironmentName.AzureCloud);
		            var acct = new AzureAccount { Type = AzureAccount.AccountType.User };

		            if (username != null && password != null)
		                acct.Id = username;

		            profileClient.AddAccountAndLoadSubscriptions(acct, env, password);

		            return profileClient;
		        }

		        private static SubscriptionCloudCredentials GetCloudCredentials()
		        {
		            var sub = _profileClient.Profile.Subscriptions.Values.FirstOrDefault(s => s.Id.Equals(SubscriptionId));

		            Debug.Assert(sub != null, "subscription != null");
		            _profileClient.SetSubscriptionAsDefault(sub.Id, sub.Account);

		            return AzureSession.AuthenticationFactory.GetSubscriptionCloudCredentials(_profileClient.Profile.Context);
		        }

		    }
		}

7. Presione **F5** para ejecutar la aplicación. Una ventana de consola se abrirá y mostrará el estado de la aplicación. También se le pedirá que escriba las credenciales de la cuenta de Azure. La creación del clúster de HDInsight puede durar varios minutos.


## Creación de un clúster de HDInsight mediante SQL Server Integration Services local

También puede usar SQL Server Integration Services (SSIS) para crear o eliminar un clúster de HDInsight. El paquete de características de Azure para SSIS proporciona los siguientes componentes que funcionan con clústeres de HDInsight.


- [Tarea de creación de un clúster de Azure HDInsight][ssisclustercreate]
- [Tarea de eliminación de un clúster de Azure HDInsight][ssisclusterdelete]
- [Administrador de conexiones de suscripción de Azure][connectionmanager]

Obtenga más información sobre el paquete de características de Azure para SSIS [aquí][ssispack].


##<a id="nextsteps"></a> Pasos siguientes
En este artículo, ha aprendido a aprovisionar un clúster de HDInsight de varias formas. Para obtener más información, consulte los artículos siguientes:

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
	      "defaultValue": "hdiuser",
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
[connectionmanager]: http://msdn.microsoft.com/es-ES/library/mt146773(v=sql.120).aspx
[ssispack]: http://msdn.microsoft.com/es-ES/library/mt146770(v=sql.120).aspx
[ssisclustercreate]: http://msdn.microsoft.com/es-ES/library/mt146774(v=sql.120).aspx
[ssisclusterdelete]: http://msdn.microsoft.com/es-ES/library/mt146778(v=sql.120).aspx

<!----HONumber=Sept15_HO2-->