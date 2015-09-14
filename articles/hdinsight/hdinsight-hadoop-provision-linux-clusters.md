<properties
   	pageTitle="Aprovisionamiento de clústeres de Hadoop, HBase o Storm en Linux en HDInsight | Microsoft Azure"
	description="Aprenda a aprovisionar clústeres de Hadoop en Linux para HDInsight usando el portal de administración, la línea de comandos y el SDK de .NET."
	services="hdinsight"
	documentationCenter=""
	authors="nitinme"
	manager="paulettm"
	editor="cgronlun"
	tags="azure-portal"/>

<tags
   	ms.service="hdinsight"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="big-data"
	ms.date="08/21/2015"
	ms.author="nitinme"/>


#Aprovisionamiento de clústeres de Hadoop Linux en HDInsight usando opciones personalizadas.

En este artículo aprenderá las diferentes maneras de aprovisionar un clúster de Hadoop Linux de forma personalizada en HDInsight de Azure, mediante el uso del Portal de Azure, Azure PowerShell, CLI de Azure o el SDK de .NET de HDInsight.

## ¿Qué es un clúster de HDInsight?

¿Se ha preguntado alguna vez por qué mencionamos los clústeres cada vez que hablamos de Hadoop o Big Data? El motivo es porque Hadoop permite el procesamiento distribuido de datos grandes y el despliegue a través de diferentes nodos de un clúster. El clúster tiene una arquitectura de maestro/trabajo con un nodo maestro (también llamado nodo principal o nodo de nombre) y cualquier número de nodos de trabajo (también llamado nodo de datos). Para obtener más información, consulte <a href="http://go.microsoft.com/fwlink/?LinkId=510084" target="_blank">Apache Hadoop</a>.

![Clúster de HDInsight][img-hdi-cluster]


Un clúster de HDInsight abstrae los detalles de implementación de Hadoop de manera que no tiene que preocuparse sobre cómo comunicarse con diferentes nodos de un clúster. Cuando aprovisiona un clúster de Azure, aprovisiona cursos de proceso de Azure que contienen aplicaciones de Hadoop y aplicaciones relacionadas. Para obtener más información, consulte [Introducción a Hadoop en HDInsight](hdinsight-hadoop-introduction.md). Los datos que se van a renovar se almacenan en el almacenamiento de blobs de Azure. Para obtener más información, consulte [Uso de almacenamiento de blobs de Azure con HDInsight](../hdinsight-use-blob-storage.md).


En este artículo se proporcionan instrucciones sobre diferentes formas de aprovisionar un clúster. Si busca un enfoque rápido para aprovisionar un clúster, consulte [Introducción a HDInsight de Azure en Linux](../hdinsight-hadoop-linux-get-started.md).

**Requisitos previos**

Antes de empezar este artículo, debe tener lo siguiente:

- **Una suscripción de Azure**. Consulte [Obtención de una versión de evaluación gratuita](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- **Claves de Shell seguro (SSH)**. Si desea tener acceso remoto a un clúster de Linux mediante SSH con una clave en lugar de una contraseña. Usar una clave es el método recomendado, puesto que es más seguro. Para obtener instrucciones sobre cómo generar claves SSH, consulte los siguientes artículos:
	-  Desde un equipo con Linux: [Utilización de SSH con HDInsight basado en Linux (Hadoop) desde Linux, Unix u OS X](hdinsight-hadoop-linux-use-ssh-unix.md).
	-  Desde un equipo con Windows: [Utilización de SSH con HDInsight basado en Linux (Hadoop) desde Windows](hdinsight-hadoop-linux-use-ssh-windows.md).

## <a id="configuration"></a>Opciones de configuración

### Almacenamiento adicional

Durante la configuración, debe especificar una cuenta de almacenamiento de blobs de Azure y un contenedor predeterminado. El clúster usa este contenedor como ubicación de almacenamiento predeterminada. Si lo desea, puede especificar otros blobs que se asociarán también con el clúster.


Para obtener más información sobre el uso de almacenes de blobs secundarios, consulte [Uso del almacenamiento de blobs de Azure con HDInsight](../hdinsight-use-blob-storage.md).


### Tienda de metadatos

La tienda de metadatos contiene información acerca de las tablas, las particiones, los esquemas y las columnas de Hive, entre otros elementos. Hive usa esta información para localizar dónde se almacenan los datos en Sistema de archivos distribuido de Hadoop (HDFS) o almacenamiento de blobs de Azure para HDInsight. De forma predeterminada, Hive utiliza una base de datos integrada para almacenar esta información.

Cuando se aprovisiona un clúster de HDInsight, puede especificar una base de datos SQL que contendrá la tienda de metadatos para Hive. Esto permite conservar la información de metadatos cuando se elimina un clúster, porque se almacena externamente en la base de datos SQL. Para obtener instrucciones sobre cómo crear una base de datos SQL en Azure, consulte [Creación de la primera base de datos SQL de Azure](sql-database-get-started.md).

### Personalización de clústeres mediante la acción de script

Puede instalar componentes adicionales o personalizar la configuración del clúster mediante secuencias de comandos durante el aprovisionamiento. Tales scripts se invocan mediante la **acción de script**, una opción de configuración que se puede usar en el Portal de vista previa, los cmdlets de HDInsight de Windows PowerShell o el SDK de .NET para HDInsight. Para obtener más información, consulte [Personalización de un clúster de HDInsight mediante la acción de script](hdinsight-hadoop-customize-cluster-linux.md).


### Uso de redes virtuales de Azure

[Red virtual de Azure](http://azure.microsoft.com/documentation/services/virtual-network/) permite crear una red segura y persistente que contenga los recursos necesarios para una solución. Una red virtual permite hacer lo siguiente:

* Conectar recursos en la nube en una red privada (solo en la nube)

	![Diagrama de la configuración solo en la nube](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-vnet-cloud-only.png)

* Conectar sus recursos en la nube a la red de su centro de datos local (sitio a sitio o punto a sitio) usando una red privada virtual (VPN).

	La configuración sitio a sitio permite conectar varios recursos de su centro de datos a la red virtual de Azure usando una VPN de hardware o el servicio de enrutamiento y acceso remoto.

	![Diagrama de la configuración de sitio a sitio](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-vnet-site-to-site.png)

	La configuración punto a sitio permite conectar un recurso específico a la red virtual de Azure usando una VPN de software

	![Diagrama de la configuración de punto a sitio](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-vnet-point-to-site.png)

Para obtener más información sobre las características, las ventajas y la funcionalidad de Red virtual, consulte [Información general sobre Red virtual de Azure](http://msdn.microsoft.com/library/azure/jj156007.aspx).

> [AZURE.NOTE]Debe crear la red virtual de Azure antes de aprovisionar un clúster. Para obtener más información, consulte [Creación de una red virtual](virtual-networks-create-vnet.md).
>
> HDInsight de Azure solo admite redes virtuales basadas en la ubicación y actualmente no funciona con redes virtuales basadas en grupos de afinidad. Utilice el cmdlet Get-AzureVNetConfig de Azure PowerShell para comprobar si una red virtual existente de Azure está basada en la ubicación. Si la red virtual no está basada en la ubicación, tiene las opciones siguientes:
>
> - Exporte la configuración de la red virtual existente y luego cree una red virtual nueva. De manera predeterminada, las redes virtuales nuevas se basan en la ubicación.
> - Migre a una red virtual basada en la ubicación. Vea [Migración de los servicios existentes a un ámbito regional](http://azure.microsoft.com/blog/2014/11/26/migrating-existing-services-to-regional-scope/).
>
> Se recomienda encarecidamente designar una única subred para un clúster.
>
> Actualmente (25/08/2015), solo puede aprovisionar un clúster basado en Linux en una red virtual de Azure.


## <a id="options"></a> Opciones para aprovisionar un clúster de HDInsight Linux

Puede aprovisionar un clúster de HDInsight Hadoop Linux desde un equipo con Linux, así como desde un equipo con Windows. La siguiente tabla proporciona información sobre las opciones de aprovisionamiento disponibles en los distintos sistemas operativos, además de vínculos a instrucciones para cada una de estas opciones.

Aprovisionamiento de clústeres de Linux desde un equipo que ejecuta este SO| Uso del portal de Azure | Uso de Azure PowerShell | Uso del SDK de .NET
-----------------| ------------------------| -------------------| ---------- | ---------
Linux| Haga clic [aquí](#portal) | No aplicable | No aplicable
Windows | Haga clic [aquí](#portal) | Haga clic [aquí](#powershell) | Haga clic [aquí](#sdk)

### <a id="portal"></a> Uso del portal de Azure

Los clústeres de HDInsight usan un contenedor de almacenamiento de blobs de Azure como el sistema de archivos predeterminado. Es preciso tener una cuenta de almacenamiento de Azure ubicada en el mismo centro de datos antes de poder crear un clúster de HDInsight. Para obtener más información, consulte [Uso de almacenamiento de blobs de Azure con HDInsight](../hdinsight-use-blob-storage.md). Para obtener información acerca de la creación de una cuenta de almacenamiento de Azure, consulte [Creación de una cuenta de almacenamiento](../storage-create-storage-account.md).


> [AZURE.NOTE]Actualmente, solo las regiones del **sudeste de Asia**, **norte de Europa**, **este de EE. UU.** y **centro y sur de EE. UU.** puede hospedar clústeres de HDInsight Linux.

**Para crear un clúster de HDInsight**

1. Inicie sesión en el [Portal de vista previa de Azure](https://portal.azure.com).
2. Haga clic en **NUEVO**, en **Análisis de datos** y, luego, en **HDInsight**.

    ![Crear un nuevo clúster en el Portal de vista previa de Azure](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CreateCluster.1.png "Crear un nuevo clúster en el Portal de vista previa de Azure")

3. Escriba un **Nombre de clúster**, seleccione **Hadoop** para el **Tipo de clúster** y, en el menú desplegable **Sistema operativo de clústeres**, seleccione **Ubuntu**. Si está disponible, aparecerá una marca de verificación verde junto al Nombre de clúster.


	> [AZURE.NOTE]Para aprovisionar clústeres de HBase o Storm, seleccione el valor adecuado en la lista desplegable **Tipo de clúster**.


	![Especifique el tipo y el nombre del clúster](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CreateCluster.2.png "Especifique el tipo y el nombre del clúster")

4. Si tiene más de una suscripción, haga clic en la entrada **Suscripción** para seleccionar la suscripción de Azure que se usará para el clúster.

5. Haga clic en **Grupo de recursos** para ver una lista de grupos de recursos existentes y, luego, seleccione aquel en el que desea crear el clúster. También puede hacer clic en **Crear nuevo** y, luego, escribir el nombre del nuevo grupo de recursos. Aparecerá una marca de verificación verde para indicar si el nuevo nombre de grupo está disponible.

	> [AZURE.NOTE]Esta entrada se establecerá de manera predeterminada en uno de sus grupos de recursos existentes, si hay alguno disponible.

6. Haga clic en **Credenciales** y, luego, escriba una contraseña para el usuario administrador. También debe especificar un **Nombre de usuario de SSH** y una **CONTRASEÑA** o una **CLAVE PÚBLICA**, que se usará para autenticar al usuario de SSH. Es recomendable usar una clave pública. Haga clic en **Seleccionar** en la parte inferior para guardar la configuración de las credenciales.

	![Proporcione las credenciales del clúster](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CreateCluster.3.png "Proporcione las credenciales del clúster")

	Para obtener más información sobre el uso de SSH con HDInsight, vea uno de los siguientes artículos:

	* [Utilización de SSH con Hadoop en HDInsight basado en Linux desde Linux, Unix u OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
	* [Utilización de SSH con Hadoop en HDInsight basado en Linux desde Windows](hdinsight-hadoop-linux-use-ssh-windows.md)


7. Haga clic en **Origen de datos** para elegir un origen de datos existente para el clúster, o cree uno nuevo.

	![Hoja Origen de datos](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CreateCluster.4.png "Proporcione la configuración del origen de datos")

	Actualmente puede seleccionar una cuenta de almacenamiento de Azure como origen de datos para un clúster de HDInsight. Use los siguientes elementos para comprender las entradas de la hoja **Origen de datos**.

	- **Método de selección**: establezca esta opción en **De todas las suscripciones** para permitir la exploración de cuentas de almacenamiento en todas sus suscripciones. Establezca esta opción en **Clave de acceso** si desea especificar el **Nombre de almacenamiento** y la **Clave de acceso** de una cuenta de almacenamiento existente.

	- **Seleccionar cuenta de almacenamiento/Crear nueva**: haga clic en **Seleccionar cuenta de almacenamiento** para examinar y seleccionar una cuenta de almacenamiento existente que desee asociar con el clúster. O bien, haga clic en **Crear nueva** para crear una nueva cuenta de almacenamiento. Use el campo que aparece para especificar el nombre de la cuenta de almacenamiento. Si el nombre está disponible, aparecerá una marca de verificación verde.

	- **Elegir contenedor predeterminado**: use esta opción para escribir el nombre del contenedor predeterminado que se usará para el clúster. Aunque se puede escribir cualquier nombre aquí, se recomienda usar el mismo nombre que el del clúster para que pueda reconocer fácilmente que el contenedor se usa para este clúster concreto.

	- **Ubicación**: región geográfica en la que se encuentra, o donde se creará, la cuenta de almacenamiento.

		> [AZURE.IMPORTANT]Seleccionar la ubicación del origen de datos predeterminado también establecerá la ubicación del clúster de HDInsight. El origen de datos del clúster y predeterminado deben encontrarse en la misma región.

	Haga clic en **Seleccionar** para guardar la configuración del origen de datos.

8. Haga clic en **Nivel de precios del nodo** para mostrar información acerca de los nodos que se crearán para este clúster. Establezca el número de nodos de trabajo que necesita para el clúster. El costo estimado del clúster se mostrará en la hoja.

	![Hoja Niveles de precios de nodo](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CreateCluster.5.png "Especifique el número de nodos de clúster")

	Haga clic en **Seleccionar** para guardar la configuración de los precios del nodo.

9. Haga clic en **Configuración opcional** para seleccionar la versión del clúster y configurar otros valores opcionales, como unirse a una **Red virtual**, configurar una **tienda de metadatos externos** para almacenar datos de Hive y Oozie, usar acciones de script para personalizar un clúster para instalar componentes personalizados o usar cuentas de almacenamiento adicionales con el clúster.

	* Haga clic en el menú desplegable **Versión de HDInsight** y seleccione la versión que desea usar para el clúster. Para obtener más información, consulte [Versiones del clúster de HDInsight](hdinsight-component-versioning.md).


	* **Red virtual**: seleccione una red virtual de Azure y la subred si desea colocar el clúster en una red virtual.

		![Hoja de red virtual](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CreateCluster.6.png "Especifique detalles de red virtual")

    	>[AZURE.NOTE]El clúster de HDInsight basado en Windows solo se puede colocar en una red virtual clásica.


	* Haga clic en **Tiendas de metadatos externas** para especificar la base de datos SQL que desea usar para guardar los metadatos de Hive y Oozie asociados con el clúster.

		![Hoja de tiendas de metadatos personalizados](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CreateCluster.7.png "Especificar tiendas de metadatos externas")

		En **Usar una base de datos SQL existente para los metadatos de Hive**, haga clic en **Sí**, seleccione una base de datos SQL y, luego, proporcione el nombre de usuario y la contraseña para la base de datos. Repita estos pasos si desea **Usar una base de datos SQL existente para los metadatos de Oozie**. Haga clic en **Seleccionar** hasta volver a la hoja **Configuración opcional**.

		>[AZURE.NOTE]La base de datos SQL de Azure usada para la tienda de metadatos debe permitir la conectividad con otros servicios de Azure, incluido HDInsight de Azure. En el panel de la base de datos SQL de Azure, en el lado derecho, haga clic en el nombre de servidor. Este es el servidor en el que se ejecuta la instancia de base de datos SQL. Cuando se encuentre en la vista de servidor, haga clic en **Configurar** y luego, en **Servicios de Azure**, haga clic en **Sí** y en **Guardar**.


	* Seleccione **Acciones de script** si desea usar un script personalizado para personalizar un clúster, conforme se crea el clúster. Para obtener más información sobre las acciones de script, vea [Personalización de un clúster de HDInsight mediante la acción de script](hdinsight-hadoop-customize-cluster-linux.md). En la hoja Acciones de script, proporcione los detalles como se muestra en la captura de pantalla.

		![Hoja de acción de script](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CreateCluster.8.png "Especifique una acción de script")


	* Haga clic en **Claves de almacenamiento de Azure** para especificar cuentas de almacenamiento adicionales para asociar con el clúster. En la hoja **Claves de almacenamiento de Azure**, haga clic en **Agregar una clave de almacenamiento** y, luego, seleccione una cuenta de almacenamiento existente o cree una nueva.

		![Hoja de almacenamiento adicional](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CreateCluster.9.png "Especificar cuentas de almacenamiento adicionales")

		Haga clic en **Seleccionar** hasta volver a la hoja **Nuevo clúster de HDInsight**.

10. En la hoja **Nuevo clúster de HDInsight**, asegúrese de que **Anclar a Panel de inicio** está seleccionado y, a luego, haga clic en **Crear**. Esto creará el clúster y agregará un icono para él en el panel de inicio de su Portal de Azure. El icono indicará que el clúster está aprovisionando y cambiará para mostrar el icono de HDInsight cuando se haya completado el proceso.

	| Durante el aprovisionamiento | Aprovisionamiento completado |
	| ------------------ | --------------------- |
	| ![Indicador de aprovisionamiento en el panel de inicio](./media/hdinsight-hadoop-provision-linux-clusters/provisioning.png) | ![Icono de clúster aprovisionado](./media/hdinsight-hadoop-provision-linux-clusters/provisioned.png) |

	> [AZURE.NOTE]El clúster tardará algo de tiempo en crearse, normalmente unos 15 minutos. Use el icono del panel de inicio o la entrada **Notificaciones** de la izquierda de la página para comprobar el proceso de aprovisionamiento.

11. Una vez que termine el aprovisionamiento, haga clic en el icono del clúster desde el panel de inicio para iniciar la hoja del clúster. La hoja de clúster proporciona información esencial sobre el clúster, como el nombre, el grupo de recursos al que pertenece, la ubicación, el sistema operativo, la dirección URL para el panel del clúster, etc.

	![Hoja de clúster](./media/hdinsight-hadoop-provision-linux-clusters/HDI.Cluster.Blade.png "Propiedades de clúster")

	Use las siguientes opciones para comprender los iconos de la parte superior de esta hoja y, en la sección **Conceptos básicos**:

	* **Configuración** y **Toda la configuración**: muestra la hoja **Configuración** del clúster, que permite obtener acceso a información de configuración detallada para el clúster.

	* **Panel**, **Panel del clúster** y **URL**: se trata de formas de tener acceso al panel del clúster, que es un portal web que ejecuta trabajos en el clúster.

	* **Shell seguro**: información necesaria para tener acceso al clúster mediante SSH.

	* **Eliminar**: elimina el clúster de HDInsight.

	* **Inicio rápido** (![icono de nube y rayo = inicio rápido](./media/hdinsight-hadoop-provision-linux-clusters/quickstart.png)): muestra información que le ayudará a empezar a usar HDInsight.

	* **Usuarios** (![icono de usuarios](./media/hdinsight-hadoop-provision-linux-clusters/users.png)): permite establecer permisos para la _administración del portal_ de este clúster para otros usuarios de su suscripción de Azure.

		> [AZURE.IMPORTANT]Esto _solo_ afecta al acceso y los permisos para este clúster en el Portal de vista previa de Azure, y no tiene ningún efecto sobre quién puede conectarse o enviar trabajos al clúster de HDInsight.

	* **Etiquetas** (![icono de etiqueta](./media/hdinsight-hadoop-provision-linux-clusters/tags.png)): las etiquetas permiten establecer pares de clave/valor para definir una taxonomía personalizada de sus servicios en la nube. Por ejemplo, puede crear una clave denominada __proyecto__ y, luego, usar un valor común para todos los servicios asociados a un proyecto específico.



### <a id="powershell"></a> Uso de Azure PowerShell
Azure PowerShell es un potente entorno de scripting que puede usar para controlar y automatizar la implementación y la administración de sus cargas de trabajo en Azure. Esta sección proporciona instrucciones sobre cómo aprovisionar un clúster de HDInsight con Azure PowerShell. Para obtener información sobre la configuración de una estación de trabajo para que ejecute cmdlets de HDInsight Windows PowerShell, consulte [Instalación y configuración de Azure PowerShell](../install-configure-powershell.md). Para obtener más información sobre el uso de Azure PowerShell con HDInsight, consulte [Administración de HDInsight con PowerShell](hdinsight-administer-use-powershell.md). Para obtener una lista de los cmdlets de HDInsight Windows PowerShell, consulte [Documentación de referencia de los cmdlets de HDInsight](https://msdn.microsoft.com/library/azure/dn858087.aspx).


Los siguientes procedimientos son necesarios para aprovisionar un clúster de HDInsight con Azure PowerShell:

- Creación de un grupo de recursos de Azure
- Crear una cuenta de Almacenamiento de Azure
- Creación de un contenedor de blobs de Azure
- Creación de un clúster de HDInsight

Los dos parámetros más importantes que debe establecer para aprovisionar clústeres de Linux son donde especifica el tipo de sistema operativo y los detalles del usuario de SSH:

- Asegúrese de especificar el parámetro **-OSType** como **Linux**.
- Para usar SSH para las sesiones remotas en los clústeres, puede especificar la contraseña del usuario de SSH o la clave pública SSH. Si se especifica tanto la contraseña del usuario de SSH como la clave pública SSH, se omitirá la clave. Si desea usar la clave SSH en las sesiones remotas, debe especificar una contraseña SSH en blanco cuando se le solicite. Para obtener más información sobre el uso de SSH con HDInsight, vea uno de los siguientes artículos:

	* [Utilización de SSH con Hadoop en HDInsight basado en Linux desde Linux, Unix u OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
	* [Utilización de SSH con Hadoop en HDInsight basado en Linux desde Windows](hdinsight-hadoop-linux-use-ssh-windows.md)


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
			$sshCredentials = Get-Credential

			# The location of the HDInsight cluster. It must be in the same data center as the Storage account.
			$location = Get-AzureStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName | %{$_.Location}

			# Create a new HDInsight cluster
			New-AzureHDInsightCluster -ClusterName $clusterName -ResourceGroupName $resourceGroupName -HttpCredential $credentials -Location $location -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainer $containerName  -ClusterSizeInNodes $clusterNodes -ClusterType Hadoop -OSType Linux -Version "3.2" -SshCredential $sshCredentials


>[AZURE.NOTE]Los valores que se especifican para **$clusterCredentials** se usan para crear la cuenta de usuario de Hadoop para el clúster. Esta cuenta se usará para conectarse al clúster. Los valores que se especifican para **$sshCredentials** se usan para crear la cuenta de usuario de SSH para el clúster. Esta cuenta se usa para iniciar una sesión remota de SSH en el clúster y ejecutar trabajos. Si usa la opción de creación rápida del Portal de Azure para aprovisionar un clúster, el nombre de usuario de Hadoop predeterminado es "admin" y el nombre de usuario de SSH predeterminado es "hdiuser".

El aprovisionamiento del clúster puede durar varios minutos en completarse.

![HDI.CLI.Provision][image-hdi-ps-provision]

###<a id="sdk"></a> Uso del SDK de .NET de HDInsight
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

5. En el menú **Herramientas**, haga clic en **Administrador de paquetes de la biblioteca** y, luego, haga clic en **Administrar paquetes de NuGet para soluciones**. En el cuadro de texto Buscar del cuadro de diálogo, busque **HDInsight**. En los resultados que se muestran, instale lo siguiente:

	 * Microsoft.Azure.Management.HDInsight
	 * Microsoft.Azure.Management.HDInsight.Job

	Busque Autenticación de Azure y, de los resultados que se muestran, instale **Microsoft.Azure.Common.Authentication**.

6. En el Explorador de soluciones, haga doble clic en **Program.cs** para abrirlo, pegue el siguiente código y proporcione valores para las variables:


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
		        private const OSType NewClusterOsType = OSType.Linux;
		        private const HDInsightClusterType NewClusterType = HDInsightClusterType.Hadoop;
		        private const string NewClusterVersion = "3.2";
		        private const string NewClusterUsername = "admin";
		        private const string NewClusterPassword = "<password>";
				private const string NewClusterSshUserName = "sshuser";
        		private const string NewClusterSshPublicKey = "<ssh public key>";

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
		                OSType = NewClusterOsType,
						SshUserName = NewClusterSshUserName,
                		SshPublicKey = NewClusterSshPublicKey
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



##<a id="nextsteps"></a> Pasos siguientes
En este artículo, ha aprendido varias maneras de aprovisionar un clúster de HDInsight en Linux. Para obtener más información, consulte los artículos siguientes:

- [Trabajo con HDInsight en Linux](hdinsight-hadoop-linux-information.md): conozca los matices que implica trabajar con un clúster de HDInsight en Linux.
- [Administración de clústeres de HDInsight con Ambari](hdinsight-hadoop-manage-ambari.md): aprenda a supervisar y administrar su clúster de Hadoop en HDInsight basado en Linux con Ambari Web o la API de REST de Ambari.
- [Uso de MapReduce con HDInsight](hdinsight-use-mapreduce.md): aprenda sobre las distintas formas de ejecutar trabajos de MapReduce en un clúster.
- [Uso de Hive con HDInsight](hdinsight-use-hive.md): obtenga información acerca de las distintas maneras de ejecutar una consulta de Hive en un clúster.
- [Uso de Pig con HDInsight](hdinsight-use-pig.md): obtenga información acerca de las distintas maneras de ejecutar un trabajo de Pig en un clúster.
- [Uso de almacenamiento de blobs de Azure con HDInsight](../hdinsight-use-blob-storage.md): sepa cómo HDInsight usa almacenamiento de blobs de Azure para almacenar datos para clústeres de HDInsight.
- [Carga de datos a HDInsight](hdinsight-upload-data.md): sepa cómo trabajar con datos almacenados en un almacenamiento de blobs de Azure para un clúster de HDInsight.


[hdinsight-use-mapreduce]: ../hdinsight-use-mapreduce/
[hdinsight-use-hive]: ../hdinsight-use-hive/
[hdinsight-use-pig]: ../hdinsight-use-pig/
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-sdk-documentation]: http://msdn.microsoft.com/library/dn479185.aspx


[hdinsight-customize-cluster]: ../hdinsight-hadoop-customize-cluster/
[hdinsight-get-started]: ../hdinsight-get-started/
[hdinsight-admin-powershell]: ../hdinsight-administer-use-powershell/

[hdinsight-submit-jobs]: ../hdinsight-submit-hadoop-jobs-programmatically/
[hdinsight-powershell-reference]: https://msdn.microsoft.com/library/dn858087.aspx

[azure-management-portal]: https://manage.windowsazure.com/

[azure-command-line-tools]: ../xplat-cli/
[azure-create-storageaccount]: ../storage-create-storage-account/

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[hdi-remote]: http://azure.microsoft.com/documentation/articles/hdinsight-administer-use-management-portal/#rdp


[Powershell-install-configure]: ../install-configure-powershell/

[image-hdi-customcreatecluster]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.png
[image-hdi-customcreatecluster-clusteruser]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.ClusterUser.png
[image-hdi-customcreatecluster-storageaccount]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.StorageAccount.png
[image-hdi-customcreatecluster-addonstorage]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.AddOnStorage.png

[azure-preview-portal]: https://portal.azure.com


[image-cli-account-download-import]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.CLIAccountDownloadImport.png
[image-cli-clustercreation]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.CLIClusterCreation.png
[image-cli-clustercreation-config]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.CLIClusterCreationConfig.png
[image-cli-clusterlisting]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.CLIListClusters.png "Enumeración y visualización de clústeres"

[image-hdi-ps-provision]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.ps.provision.png
[image-hdi-ps-config-provision]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.ps.config.provision.png

[img-hdi-cluster]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.Cluster.png

  [89e2276a]: /documentation/articles/hdinsight-use-sqoop/ "Uso de Sqoop con HDInsight"

<!---HONumber=September15_HO1-->