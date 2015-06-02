<properties 
   pageTitle="Aprovisionamiento de clústeres de Hadoop en HDInsight | Azure" 
   description="Obtenga información sobre cómo aprovisionar clústeres de HDInsight de Azure mediante el portal de Azure, Azure PowerShell, una línea de comandos o el SDK de .NET de HDInsight" 
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
   ms.date="04/21/2015"
   ms.author="nitinme"/>

#Aprovisionar clústeres de Hadoop en HDInsight usando opciones personalizadas

Aprenda las diferentes formas de aprovisionar clústeres de Hadoop de forma personalizada en HDInsight de Azure usando el Portal de administración de Azure, Azure PowerShell, herramientas de la línea de comandos o el SDK de .NET de HDInsight. Para obtener instrucciones sobre el aprovisionamiento clústeres de HBase y clústeres de Storm, vea [Aprovisionar clúster de HBase en HDInsight](hdinsight-hbase-get-started.md) e [Introducción al uso de Storm en HDInsight](hdinsight-storm-getting-started.md). Consulte <a href="http://go.microsoft.com/fwlink/?LinkId=510237">¿Cuál es la diferencia entre Hadoop y HBase?</a> para comprender por qué debería elegir uno en lugar del otro.

## ¿Qué es un clúster de HDInsight?

¿Se ha preguntado alguna vez por qué mencionamos clústeres cada vez que hablamos de Hadoop o Big Data? El motivo es porque Hadoop permite el procesamiento distribuido de datos grandes y el despliegue a través de diferentes nodos de un clúster. El clúster tiene una arquitectura maestro/esclavo con un maestro (también llamado nodo principal o nodo de nombres) y cualquier número de esclavos (también llamado nodo de datos). Para obtener más información, consulte [Apache Hadoop][apache-hadoop].

![Clúster de HDInsight][img-hdi-cluster]

Un clúster de HDInsight abstrae los detalles de implementación de Hadoop de manera que no tiene que preocuparse sobre cómo comunicarse con diferentes nodos de un clúster. Cuando aprovisiona un clúster de Azure, aprovisiona cursos de proceso de Azure que contienen aplicaciones de Hadoop y aplicaciones relacionadas. Para obtener más información, consulte [Introducción a Hadoop en HDInsight](hdinsight-hadoop-introduction.md). Los datos que se van a renovar se almacenan en el almacenamiento de blobs de Azure. Para obtener más información, vea [Uso del almacenamiento de blobs de Azure con HDInsight](hdinsight-use-blob-storage.md).

En este artículo se proporcionan instrucciones sobre diferentes formas de aprovisionar un clúster. Si busca un enfoque rápido para aprovisionar un clúster, consulte [Introducción a HDInsight de Azure](hdinsight-get-started.md).

**Requisitos previos:**

Antes de empezar las instrucciones de este artículo, debe tener lo siguiente:

- Una suscripción de Azure. Azure es una plataforma basada en suscripción. Los cmdlets de Windows PowerShell de HDInsight realizan las tareas con su suscripción. Para obtener más información acerca de cómo obtener una suscripción, consulte [Opciones de compra][azure-purchase-options], [Ofertas para miembros][azure-member-offers] o [Prueba gratuita][azure-free-trial].

##<a id="configuration"></a>Opciones de configuración

###Clústeres en Linux

HDInsight proporciona la opción de configurar clústeres de Linux en Azure. Configure un clúster de Linux si conoce Linux o Unix, migrando desde una solución existente de Hadoop basado en Linux, o si desea una integración fácil con componentes del ecosistema de Hadoop creados para Linux. Para obtener más información, vea [Introducción a Hadoop en Linux en HDInsight](hdinsight-hadoop-linux-get-started.md).

###Almacenamiento adicional

Durante la configuración, debe especificar una cuenta de almacenamiento de blobs de Azure y un contenedor predeterminado. El clúster usa este contenedor como ubicación de almacenamiento predeterminada. Si lo desea, puede especificar una cuenta de almacenamiento de Azure adicional que también se asociará con el clúster.

>[AZURE.NOTE]No comparta un contenedor de almacenamiento de blobs para varios clústeres, ya que no es compatible.

Para obtener más información sobre el uso de almacenes de blobs secundarios, consulte [Uso del almacenamiento de blobs de Azure con HDInsight](hdinsight-use-blob-storage.md).

###Tienda de metadatos

La tienda de metadatos contiene metadatos de Hive y Oozie, como columnas, esquemas, particiones y tablas de Hive. El uso de la tienda de metadatos le ayuda a conservar sus metadatos de Hive y Oozie, por lo que no es necesario volver a crear tablas de Hive o trabajos de Oozie al aprovisionar un nuevo clúster. De forma predeterminada, Hive utiliza una base de datos integrada para almacenar esta información. La base de datos incrustada no puede conservar los metadatos cuando se elimina el clúster.

### Personalización del clúster

Puede instalar componentes adicionales o personalizar la configuración del clúster mediante secuencias de comandos durante el aprovisionamiento. Tales scripts se invocan mediante la opción de **Acción de script**, una opción de configuración que se puede usar a partir de los cmdlets de Windows PowerShell de HDInsight, en el Portal de Azure o el SDK de .NET para HDInsight. Para obtener más información, consulte [Personalizar el clúster de HDInsight mediante la acción de script](hdinsight-hadoop-customize-cluster.md).


###Redes virtuales

[Red virtual de Azure](http://azure.microsoft.com/documentation/services/virtual-network/) permite crear una red segura y persistente que contenga los recursos que necesita para la solución. Una red virtual permite hacer lo siguiente:

* Conectar recursos en la nube en una red privada (solo en la nube).

	![Diagrama de la configuración solo en la nube](./media/hdinsight-provision-clusters/hdinsight-vnet-cloud-only.png)

* Conectar sus recursos en la nube a la red de su centro de datos local (sitio a sitio o punto a sitio) usando una red privada virtual (VPN).

	La configuración sitio a sitio permite conectar varios recursos de su centro de datos a la red virtual de Azure usando una VPN de hardware o el servicio de enrutamiento y acceso remoto.

	![Diagrama de la configuración de sitio a sitio](./media/hdinsight-provision-clusters/hdinsight-vnet-site-to-site.png)

	La configuración punto a sitio permite conectar un recurso específico a la red virtual de Azure usando una VPN de software

	![Diagrama de la configuración de punto a sitio](./media/hdinsight-provision-clusters/hdinsight-vnet-point-to-site.png)

Para obtener más información sobre las características, las ventajas y la funcionalidad de Red virtual, consulte [Información general sobre Red virtual de Azure](http://msdn.microsoft.com/library/azure/jj156007.aspx).

> [AZURE.NOTE]Debe crear la red virtual de Azure antes de aprovisionar un clúster de HDInsight. Para obtener más información, consulte [Tareas de configuración de la red virtual](http://msdn.microsoft.com/library/azure/jj156206.aspx).
>
> Azure HDInsight solo admite redes virtuales basadas en la ubicación y actualmente no funciona con redes virtuales basadas en grupos de afinidad.
>
> Se recomienda encarecidamente designar una única subred para un clúster.

##<a id="portal"></a> Uso del portal de Azure

Los clústeres de HDInsight usan un contenedor de almacenamiento de blobs de Azure como el sistema de archivos predeterminado. Es preciso tener una cuenta de almacenamiento de Azure ubicada en el mismo centro de datos antes de crear un clúster de HDInsight. Para obtener más información, consulte [Uso de Almacenamiento de blobs de Azure con HDInsight](hdinsight-use-blob-storage.md). Para obtener información acerca de la creación de una cuenta de almacenamiento de Azure, consulte [Creación de una cuenta de almacenamiento](storage-create-storage-account.md).


> [AZURE.NOTE]Actualmente, solo las regiones **Asia oriental**, **Sudeste de Asia**, **Norte de Europa**, **Oeste de Europa**, **Este de EE. UU.**, **Oeste de EE. UU.**, **Centro y norte de EE. UU.** y **Centro y sur de EE. UU.** pueden hospedar clústeres de HDInsight.

**Para crear un clúster de HDInsight con la opción de creación personalizada**

1. Inicie sesión en el [Portal de Azure][azure-management-portal].
2. Haga clic en **+ NUEVO** en la parte inferior de la página y después en **SERVICIOS DE DATOS**, **HDINSIGHT** y **CREACIÓN PERSONALIZADA**.
3. En la página **Detalles del clúster**, escriba o elija los valores siguientes:

	![Proporcionar detalles del clúster de HDInsight de Hadoop][image-customprovision-page1]

    <table border='1'>
	<tr><th>Propiedad</th><th>Valor</th></tr>
	<tr><td>Cluster Name</td>
		<td><p>Dé un nombre al clúster. </p>
			<ul>
			<li>El nombre del Sistema de nombres de dominio (DNS) debe empezar y terminar con un carácter alfanumérico y puede contener guiones.</li>
			<li>El campo debe ser una cadena con una longitud comprendida entre 3 y 63 caracteres.</li>
			</ul></td></tr>
	<tr><td>Tipo de clúster</td>
		<td>Para el tipo de clúster, seleccione <strong>Hadoop</strong>.</td></tr>
	<tr><td>Sistema operativo</td>
		<td>Seleccione <b>Windows Server 2012 R2 Data Center</b> para aprovisionar un clúster de Windows. Para aprovisionar un clúster de Linux, consulte <a href="http://azure.microsoft.com/documentation/articles/hdinsight-hadoop-provision-linux-clusters/" target="_blank">Aprovisionamiento de clústeres de Hadoop en Linux en HDInsight</a>.</td></tr>
	<tr><td>Versión de HDInsight</td>
		<td>Seleccione la versión. Para Hadoop, la versión predeterminada la versión 3.1 de HDInsight, que usa Hadoop 2.4.</td></tr>
	</table>Escriba o seleccione los valores mostrados en la tabla y después haga clic en la flecha derecha.

4. En la página **Configurar clúster** escriba o seleccione los valores siguientes:

	![Proporcionar detalles del clúster de HDInsight de Hadoop](./media/hdinsight-provision-clusters/HDI.CustomProvision.Page2.png)

	<table border="1">
<tr><th>Nombre</th><th>Valor</th></tr>
<tr><td>Nodos de datos</td><td>Número de nodos de datos que desea implementar. Para propósitos de prueba, cree un clúster de un solo nodo. <br />El límite de tamaño del clúster varía según las suscripciones a Azure. Póngase en contacto con el servicio de soporte relacionado con la facturación de Azure para aumentar el límite.</td></tr>
<tr><td>Región/red virtual</td><td><p>Elija la misma región que la cuenta de almacenamiento que creó en el último procedimiento. HDInsight requiere que la cuenta de almacenamiento se encuentre en la misma región. Posteriormente, en la configuración, puede seleccionar solo una cuenta de almacenamiento que se encuentre en la misma región que especificó aquí.</p><p>Las regiones disponibles son: <strong>Asia oriental</strong>, <strong>sudeste de Asia</strong>, <strong>norte de Europa</strong>, <strong>oeste de Europa</strong>, <strong>este de EE.&#160;UU.</strong>, <strong>oeste de EE.&#160;UU.</strong>, <strong>centro y norte de EE.&#160;UU.</strong>, <strong>centro y sur de EE.&#160;UU</strong>.<br/>Si ha creado una red virtual de Azure, puede seleccionar la red que configurará para su uso por parte del clúster de HDInsight.</p><p>Para obtener más información sobre la creación de una red virtual de Azure, consulte <a href="http://msdn.microsoft.com/library/azure/jj156206.aspx">Tareas de configuración de la red virtual</a>.</p></td></tr>
<tr><td>Tamaño de nodo principal</td><td><p>Seleccione un tamaño de máquina virtual (VM) para el nodo principal.</p></td></tr>
<tr><td>Tamaño de nodo de datos</td><td><p>Seleccione un tamaño de máquina virtual para los nodos de datos.</p></td></tr>
</table>

	>[AZURE.NOTE]En función de la elección de las máquinas virtuales, su costo puede variar. HDInsight usa todas las máquinas virtuales de nivel estándar para los nodos del clúster. Para obtener información sobre cómo afectan los tamaños de máquinas virtuales a los precios, consulte <a href="http://azure.microsoft.com/pricing/details/hdinsight/" target="_blank">Precios de HDInsight</a>.


5. En la página **Configurar usuario de clúster** proporcione los siguientes valores:

    ![Proporcionar detalles de la tienda de metadatos y de usuario de clúster de HDInsight de Hadoop](./media/hdinsight-provision-clusters/HDI.CustomProvision.Page3.png)

    <table border='1'>
	<tr><th>Propiedad</th><th>Valor</th></tr>
	<tr><td>Nombre de usuario HTTP</td>
		<td>Especifique el nombre del usuario del clúster de HDInsight.</td></tr>
	<tr><td>Contraseña HTTP/Confirmar contraseña</td>
		<td>Especifique la contraseña del usuario del clúster de HDInsight.</td></tr>
	<tr><td>Habilitar Escritorio remoto para clúster</td>
		<td>Active esta casilla para especificar un nombre de usuario, una contraseña y una fecha de caducidad para un usuario de escritorio remoto que puede conectarse en remoto a los nodos del clúster, cuando esté aprovisionado. También puede habilitar el escritorio remoto más adelante, cuando el clúster esté aprovisionado. Para obtener instrucciones, vea <a href="hdinsight-administer-use-management-portal/#rdp" target="_blank">Conexión a los clústeres de HDInsight con RDP</a>.</td></tr>
	<tr><td>Especificar la tienda de metadatos de Hive/Oozie</td>
		<td>Active esta casilla para especificar la base de datos SQL que se encuentra en el mismo centro de datos que el clúster, que se usará como tienda de metadatos de Hive/Oozie. Si activa esta casilla, debe especificar detalles acerca de la base de datos SQL de Azure en las páginas siguientes del asistente. Esto resulta de gran utilidad si desea conservar los metadatos sobre trabajos de Hive/Oozie después de haber eliminado un clúster.</td></tr>
	</td></tr>		
</table>Haga clic en la flecha derecha.

6. En la página **Configurar la tienda de metadatos de Hive/Oozie** proporcione los siguientes valores:

    ![Proporcionar usuario del clúster de HDInsight de Hadoop](./media/hdinsight-provision-clusters/HDI.CustomProvision.Page4.png)

	Especifique una base de datos SQL de Azure que se usará como tienda de metadatos para Hive/Oozie. Puede especificar la misma base de datos para las tiendas de metadatos de Hive y Oozie. Esta base de datos SQL debe estar en el mismo centro de datos que el clúster de HDInsight. El cuadro de lista solo incluye las bases de datos SQL del mismo centro de datos que ha especificado en la página <strong>Detalles del clúster</strong>. Especifique también el nombre de usuario y la contraseña para conectarse a la base de datos SQL de Azure seleccionada.

    >[AZURE.NOTE]La base de datos SQL de Azure usada para la tienda de metadatos debe permitir la conectividad con otros servicios de Azure, incluido HDInsight de Azure. En el panel de base de datos SQL de Azure, en el lado derecho, haga clic en el nombre de servidor. Este es el servidor en el que se ejecuta la instancia de base de datos SQL. Cuando se encuentre en la vista de servidor, haga clic en **Configurar** y luego, en **Servicios de Azure**, haga clic en **Sí** y en **Guardar**.

    Haga clic en la flecha derecha.


7. Proporcione los siguientes valores en la página **Cuenta de almacenamiento**:

    ![Proporcionar la cuenta de almacenamiento del clúster de HDInsight de Hadoop](./media/hdinsight-provision-clusters/HDI.CustomProvision.Page5.png)

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
		<td><p>Especifica el contenedor predeterminado de la cuenta de almacenamiento que se usará como sistema de archivos predeterminado para el clúster de HDInsight. Si eligió <strong>Usar almacenamiento existente</strong> para el campo <strong>Cuenta de almacenamiento</strong> y no existen contenedores en esa cuenta, el contenedor se creará de forma predeterminada con el mismo nombre que el del clúster. Si ya existe un contenedor con el nombre del clúster, se anexará un número de secuencia al nombre del contenedor. Por ejemplo, mycontainer1, mycontainer2 y así sucesivamente. Sin embargo, si la cuenta de almacenamiento existente tiene un contenedor con un nombre diferente al del clúster especificado, también puede usar ese contenedor.</p>
        <p>Si eligió crear un almacenamiento nuevo o usar almacenamiento de otra suscripción de Azure, debe especificar el nombre del contenedor predeterminado.</p>
    </td></tr>
	<tr><td>Cuentas de almacenamiento adicionales</td>
		<td>HDInsight admite varias cuentas de almacenamiento. No hay límite en el número de cuentas de almacenamiento adicionales que un clúster puede usar. No obstante, si crea un clúster mediante el portal de Azure, tendrá un límite de siete debido a las restricciones de la interfaz de usuario. Por cada cuenta de almacenamiento adicional que especifique, se agregará una página **Cuenta de almacenamiento** adicional al asistente donde podrá especificar la información de la cuenta. Por ejemplo, en la captura de pantalla anterior, se selecciona una cuenta de almacenamiento adicional y, por tanto, se agrega la página 5 al diálogo.</td></tr>
</table>Haga clic en la flecha derecha.

7. Si ha elegido configurar almacenamiento adicional para el clúster, escriba la información de la cuenta de almacenamiento adicional en la página **Cuenta de almacenamiento**:

	![Proporcionar detalles de almacenamiento adicionales del clúster de HDInsight](./media/hdinsight-provision-clusters/HDI.CustomProvision.Page6.png)

    De nuevo aquí, tiene la opción de elegir entre almacenamiento existente, crear nuevo almacenamiento o usar almacenamiento de otra suscripción de Azure. El procedimiento para proporcionar los valores es similar al paso anterior.

    > [AZURE.NOTE]Cuando haya elegido una cuenta de almacenamiento de Azure para su clúster de HDInsight, no podrá eliminar la cuenta, ni cambiarla por otra diferente.

8. En la página **Acciones de scripts**, haga clic en **agregar acción de script** para proporcionar información detallada sobre el script personalizado que desea ejecutar para personalizar un clúster durante su creación. Para obtener más información, consulte [Personalización de un clúster de HDInsight mediante la acción de script](hdinsight-hadoop-customize-cluster.md).
	
	![Configurar la acción de script para personalizar un clúster de HDInsight](./media/hdinsight-provision-clusters/HDI.CustomProvision.Page7.png)

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
Azure PowerShell es un potente entorno de scripting que puede usar para controlar y automatizar la implementación y la administración de sus cargas de trabajo en Azure. Esta sección proporciona instrucciones sobre cómo aprovisionar un clúster de HDInsight con Azure PowerShell. Para obtener información sobre la configuración de una estación de trabajo para que ejecute cmdlets de HDInsight Windows PowerShell, consulte [Instalación y configuración de Azure PowerShell](install-configure-powershell.md). Para obtener más información sobre el uso de Azure PowerShell con HDInsight, consulte [Administración de HDInsight con PowerShell](hdinsight-administer-use-powershell.md). Para obtener una lista de los cmdlets de HDInsight Windows PowerShell, consulte [Documentación de referencia de los cmdlets de HDInsight][hdinsight-powershell-reference].

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

> [AZURE.NOTE]Los cmdlets de Azure PowerShell son la única forma recomendada de cambiar variables de configuración en un clúster de HDInsight. Los cambios que se realicen en los archivos de configuración de Hadoop mientras esté conectado al clúster a través de Escritorio remoto se pueden sobrescribir si se aplica una revisión al clúster. Los valores de configuración establecidos con Azure PowerShell sí se mantienen si se aplica una revisión al clúster.

- Ejecute los comandos siguientes en la ventana de la consola de Azure PowerShell:

		$subscriptionName = "<AzureSubscriptionName>"	  # The Azure subscription used for the HDInsight cluster to be created

		$storageAccountName = "<AzureStorageAccountName>" # HDInsight cluster requires an existing Azure Storage account to be used as the default file system

		$clusterName = "<HDInsightClusterName>"			  # The name for the HDInsight cluster to be created
		$clusterNodes = <ClusterSizeInNodes>              # The number of nodes in the HDInsight cluster
        $hadoopUserName = "<HadoopUserName>"              # User name for the Hadoop user. You will use this account to connect to the cluster and run jobs.
        $hadoopUserPassword = "<HadoopUserPassword>"    

        $secPassword = ConvertTo-SecureString $hadoopUserPassword -AsPlainText -Force
        $credential = New-Object System.Management.Automation.PSCredential($hadoopUserName,$secPassword)            

		# Get the storage primary key based on the account name
		Select-AzureSubscription $subscriptionName
		$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
		$containerName = $clusterName				# Azure Blob container that is used as the default file system for the HDInsight cluster

        # The location of the HDInsight cluster. It must be in the same data center as the Storage account.
        $location = Get-AzureStorageAccount -StorageAccountName $storageAccountName | %{$_.Location} 

		# Create a new HDInsight cluster
		New-AzureHDInsightCluster -Name $clusterName -Credential $credential -Location $location -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainerName $containerName  -ClusterSizeInNodes $clusterNodes -ClusterType Hadoop

	>[AZURE.NOTE]Los comandos $hadoopUserName y $hadoopUserPassword se usan para crear la cuenta de usuario de Hadoop para el clúster. Esta cuenta se usará para conectarse al clúster y ejecutar trabajos. Si usa la opción Creación rápida en el portal de Azure para aprovisionar un clúster, el nombre de usuario de Hadoop predeterminado es "admin". No confunda esta cuenta con la cuenta de usuario del Protocolo de escritorio remoto (RDP). La cuenta de usuario RDP tiene que ser diferente de la cuenta de usuario de Hadoop. Para obtener más información, vea [Administración de clústeres de Hadoop en HDInsight mediante el Portal de administración de Azure][hdinsight-admin-portal].

	El aprovisionamiento del clúster puede durar varios minutos en completarse.

	![HDI.CLI.Provision][image-hdi-ps-provision]



**Para aprovisionar un clúster de HDInsight usando opciones de configuración personalizada**

Durante el aprovisionamiento de un clúster, puede usar las demás opciones de configuración; por ejemplo, conectarse a más de un contenedor de almacenamiento de blobs de Azure, usar una red virtual o utilizar una base de datos SQL de Azure para tiendas de metadatos de Hive y Oozie. Esto le permite separar la vigencia de sus datos y metadatos de la vigencia del clúster.

> [AZURE.NOTE]Los cmdlets de Windows PowerShell son la única forma recomendada de cambiar variables de configuración en un clúster de HDInsight. Los cambios que se realicen en los archivos de configuración de Hadoop mientras esté conectado al clúster a través de Escritorio remoto se pueden sobrescribir si se aplica una revisión al clúster. Los valores de configuración establecidos con Azure PowerShell sí se mantienen si se aplica una revisión al clúster.

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

		# Get the Storage account keys
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

	>[AZURE.NOTE]La base de datos SQL de Azure usada para la tienda de metadatos debe permitir la conectividad con otros servicios de Azure, incluido HDInsight de Azure. En el panel de base de datos SQL de Azure, en el lado derecho, haga clic en el nombre de servidor. Este es el servidor en el que se ejecuta la instancia de base de datos SQL. Cuando se encuentre en la vista de servidor, haga clic en **Configurar** y luego, en **Servicios de Azure**, haga clic en **Sí** y en **Guardar**.

**Para enumerar los clústeres de HDInsight**

- Ejecute el siguiente comando en una ventana de consola de Azure PowerShell para enumerar el clúster de HDInsight y comprobar que dicho clúster se aprovisionó correctamente:

		Get-AzureHDInsightCluster -Name <ClusterName>


##<a id="cli"></a> Uso de la línea de comandos multiplataforma

> [AZURE.NOTE]Desde el 29/8/2014, no se puede usar la interfaz de línea de comandos multiplataforma (CLI) para asociar un clúster con una red virtual de Azure.

Otra opción para aprovisionar un clúster de HDInsight es la interfaz de línea de comandos entre plataformas. La herramienta de línea de comandos se implementa en Node.js. Se puede usar en cualquier plataforma compatible con Node.js, entre las que se incluyen Windows, Mac y Linux. Puede instalar la CLI desde las siguientes ubicaciones:

- **SDK de Node.js**: <a href="https://www.npmjs.com/package/azure-mgmt-hdinsight" target="_blank">https://www.npmjs.com/package/azure-mgmt-hdinsight</a>
- **CLI entre plataformas**: <a href="https://github.com/Azure/azure-xplat-cli/archive/hdinsight-February-18-2015.tar.gz" target="_blank">https://github.com/Azure/azure-xplat-cli/archive/hdinsight-February-18-2015.tar.gz</a>  

Para obtener información general acerca de cómo usar la interfaz de la línea de comandos, consulte [CLI de Azure para Mac, Linux y Windows](xplat-cli.md).

Las instrucciones que aparecen a continuación le guían en la instalación de la línea de comandos multiplataforma en Linux y Windows y, a continuación, sobre cómo usar la línea de comandos para aprovisionar un clúster.

- [Configurar la CLI de Azure para Linux](#clilin)
- [Configurar la CLI de Azure para Windows](#cliwin)
- [Aprovisionamiento de clústeres de HDInsight mediante la CLI de Azure](#cliprovision)

#### <a id="clilin"></a>Configurar una la línea de comandos multiplataforma para Linux

Realice los siguientes procedimientos para configurar su equipo con Linux para que use las herramientas de la línea de comandos de Azure:

- Instalar una línea de comandos entre plataformas mediante el Administrador de paquetes de Node.js (NPM)
- Conexión a su suscripción de Azure

**Para instalar la interfaz de línea de comandos con NPM**

1.	Abra una ventana del terminal en el equipo con Linux y ejecute el siguiente comando:

		sudo npm install -g https://github.com/Azure/azure-xplat-cli/archive/hdinsight-February-18-2015.tar.gz

2.	Ejecute el siguiente comando para comprobar la instalación:

		azure hdinsight -h

	Puede usar el modificador *-h* en diferentes niveles para mostrar la información de ayuda. Por ejemplo:

		azure -h
		azure hdinsight -h
		azure hdinsight cluster -h
		azure hdinsight cluster create -h

**Para conectarse a su suscripción de Azure**

Antes de usar la interfaz de línea de comandos, debe configurar la conectividad entre su estación de trabajo y Azure. La interfaz de línea de comandos usa la información de su suscripción de Azure para conectarse a la cuenta. Esta información se puede obtener de Azure en un archivo de configuración de publicación. El archivo de configuración de publicación puede importarse después como un valor de configuración local permanente que la interfaz de línea de comandos usará para operaciones posteriores. Solo tiene que importar la configuración de publicación una vez.

> [AZURE.NOTE]El archivo de configuración de publicación contiene información confidencial. Microsoft recomienda eliminar el archivo o tomar medidas adicionales para cifrar la carpeta del usuario que contiene el archivo. En Windows, modifique las propiedades de la carpeta o use el Cifrado de unidad BitLocker.


1.	Abra una ventana del terminal.
2.	Ejecute el siguiente comando para iniciar sesión en su suscripción de Azure.

		azure account download

	![HDI.Linux.CLIAccountDownloadImport](./media/hdinsight-provision-clusters/HDI.Linux.CLIAccountDownloadImport.png)

	El comando inicia la página web para descargar el archivo de configuración de publicación desde ella. Si la página web no se abre, haga clic en el vínculo que aparece en la ventana del terminal para abrir la página del explorador e iniciar sesión en el portal.

3.	Descargue el archivo de configuración de publicación en el equipo.
5.	En la ventana del símbolo del sistema, ejecute el comando siguiente para importar el archivo de configuración de publicación:

		azure account import <path/to/the/file>


#### <a id="cliwin"></a>Configuración de una línea de comandos multiplataforma para Windows

Realice los siguientes procedimientos para configurar su equipo con Windows para que use las herramientas de la línea de comandos de Azure:

- Instale la línea de comandos multiplataforma (con NPM o Windows Installer).
- Descarga e importación de la configuración de publicación de la cuenta de Azure


La interfaz de línea de comandos se puede instalar mediante NPM o Windows Installer. Microsoft recomienda realizar la instalación usando solamente una de las dos opciones.

**Para instalar la interfaz de línea de comandos con NPM**

1.	Vaya a **www.nodejs.org**.
2.	Haga clic en **INSTALL** y siga las instrucciones usando la configuración predeterminada.
3.	Abra **Command Prompt** (Símbolo del sistema) (o *símbolo del sistema de Azure*, o *símbolo del sistema de desarrollador para VS2012*) en la estación de trabajo.
4.	Ejecute el comando siguiente en la ventana del símbolo del sistema:

		npm install -g https://github.com/Azure/azure-xplat-cli/archive/hdinsight-February-18-2015.tar.gz

	> [AZURE.NOTE]Si recibe un error que indica que no se encuentra el comando NPM, compruebe que las rutas siguientes estén en la variable de entorno PATH: <i>C:\\Program Files (x86)\\nodejs;C:\\Users[username]\\AppData\\Roaming\\npm</i> o <i>C:\\Program Files\\nodejs;C:\\Users[username]\\AppData\\Roaming\\npm</i>

5.	Ejecute el siguiente comando para comprobar la instalación:

		azure hdinsight -h

	Puede usar el modificador *-h* en diferentes niveles para mostrar la información de ayuda. Por ejemplo:

		azure -h
		azure hdinsight -h
		azure hdinsight cluster -h
		azure hdinsight cluster create -h

**Para instalar la interfaz de línea de comandos con Windows Installer**

1.	Vaya a **http://azure.microsoft.com/downloads/**. 2.	Desplácese hasta la sección **Herramientas de línea de comandos** y, a continuación, haga clic en **Interfaz de la línea de comandos multiplataforma** y siga el asistente del instalador de plataforma web.

**Para descargar e importar la configuración de publicación**

Antes de usar la interfaz de línea de comandos, debe configurar la conectividad entre su estación de trabajo y Azure. La interfaz de línea de comandos usa la información de su suscripción de Azure para conectarse a la cuenta. Esta información se puede obtener de Azure en un archivo de configuración de publicación. El archivo de configuración de publicación puede importarse después como un valor de configuración local permanente que la interfaz de línea de comandos usará para operaciones posteriores. Solo tiene que importar la configuración de publicación una vez.

> [AZURE.NOTE]El archivo de configuración de publicación contiene información confidencial. Microsoft recomienda eliminar el archivo o tomar medidas adicionales para cifrar la carpeta del usuario que contiene el archivo. En Windows, modifique las propiedades de la carpeta o use BitLocker.


1.	Abra el **símbolo del sistema**.
2.	Ejecute el comando siguiente para descargar el archivo de configuración de publicación:

		azure account download

	![HDI.CLIAccountDownloadImport][image-cli-account-download-import]

	El comando inicia la página web para descargar el archivo de configuración de publicación desde ella.

3.	En el símbolo del sistema para guardar el archivo, haga clic en **Guardar** y proporcione una ubicación en la que se deba guardar el archivo.
5.	En la ventana del símbolo del sistema, ejecute el comando siguiente para importar el archivo de configuración de publicación:

		azure account import <path/to/the/file>

#### <a id="cliprovision"></a>Aprovisionamiento de clústeres de HDInsight mediante una CLI de Azure

Los siguientes procedimientos son necesarios para aprovisionar un clúster de HDInsight con la CLI de Azure:

- Crear una cuenta de Almacenamiento de Azure
- Aprovisionamiento de un clúster

**Para crear una cuenta de almacenamiento de Azure**

HDInsight utiliza contenedores de almacenamiento de blobs de Azure como sistemas de archivos predeterminados. Es preciso tener una cuenta de almacenamiento de Azure antes de crear un clúster de HDInsight. La cuenta de almacenamiento debe ubicarse en el mismo centro de datos.

- En la ventana del símbolo del sistema, ejecute el siguiente comando para crear una cuenta de almacenamiento de Azure:

		azure storage account create [options] <StorageAccountName>

	Cuando se le pida una ubicación, seleccione aquella en la que se puede aprovisionar el clúster de HDInsight. El almacenamiento debe encontrarse en la misma ubicación que el clúster de HDInsight. Actualmente, solo las regiones **Asia oriental**, **Sudeste de Asia**, **Norte de Europa**, **Oeste de Europa**, **Este de EE. UU.**, **Oeste de EE. UU.**, **Centro y norte de EE. UU.** y **Centro y sur de EE. UU.** pueden hospedar clústeres de HDInsight.

Para obtener información sobre la creación de una cuenta de almacenamiento de Azure a través del portal de Azure, consulte [Creación, administración o eliminación de una cuenta de almacenamiento](storage-create-storage-account.md).

Si ya tiene una cuenta de almacenamiento pero no sabe su nombre ni su clave, puede usar los comandos siguientes para recuperar dicha información:

	-- Lists Storage accounts
	azure storage account list

	-- Shows information for a Storage account
	azure storage account show <StorageAccountName>

	-- Lists the keys for a Storage account
	azure storage account keys list <StorageAccountName>

Para obtener información sobre cómo obtener la información mediante el portal de Azure, vea la sección *Cómo: ver, copiar y regenerar claves de acceso de almacenamiento* de [Crear, administrar o eliminar una cuenta de almacenamiento](storage-create-storage-account.md).

Un clúster de HDInsight también requiere un contenedor dentro de una cuenta de almacenamiento. Si la cuenta de almacenamiento que proporciona todavía no tiene un contenedor, el comando *azure hdinsight cluster create* le pide un nombre de contenedor y también lo crea. Sin embargo, si opta por crear el contenedor antes, puede usar el comando siguiente:

	azure storage container create --account-name <StorageAccountName> --account-key <StorageAccountKey> [ContainerName]

Cuando tenga preparados la cuenta de almacenamiento y el contenedor de blobs, podrá proceder con la creación de un clúster.

**Para aprovisionar un clúster de HDInsight**

- En la ventana del símbolo del sistema, ejecute el comando siguiente:

		azure hdinsight cluster create --clusterName <ClusterName> --storageAccountName "<StorageAccountName>.blob.core.windows.net" --storageAccountKey <storageAccountKey> --storageContainer <StorageContainerName> --dataNodeCount <NumberOfNodes> --location <DataCenterLocation> --userName <HDInsightClusterUsername> --password <HDInsightClusterPassword> --osType windows

	![HDI.CLIClusterCreation][image-cli-clustercreation]


**Para aprovisionar un clúster de HDInsight con un archivo de configuración**

Normalmente, se aprovisiona un clúster de HDInsight, se ejecutan los trabajos y, a continuación, se elimina el clúster para reducir los costes. La interfaz de línea de comandos le da la opción de guardar las configuraciones en un archivo para que pueda volver a usarlas cada vez que aprovisione un clúster.

- En la ventana del símbolo del sistema, ejecute los comandos siguientes:


		#Create the config file
		azure hdinsight cluster config create <file>

		#Add commands to create a basic cluster
		azure hdinsight cluster config set <file> --clusterName <ClusterName> --dataNodeCount <NumberOfNodes> --location "<DataCenterLocation>" --storageAccountName "<StorageAccountName>.blob.core.windows.net" --storageAccountKey "<StorageAccountKey>" --storageContainer "<BlobContainerName>" --userName "<Username>" --password "<UserPassword>" --osType windows

		#If required, include commands to use additional Blob storage with the cluster
		azure hdinsight cluster config storage add <file> --storageAccountName "<StorageAccountName>.blob.core.windows.net"
		       --storageAccountKey "<StorageAccountKey>"

		#If required, include commands to use a SQL database as a Hive metastore
		azure hdinsight cluster config metastore set <file> --type "hive" --server "<SQLDatabaseName>.database.windows.net"
		       --database "<HiveDatabaseName>" --user "<Username>" --metastorePassword "<UserPassword>"

		#If required, include commands to use a SQL database as an Oozie metastore
		azure hdinsight cluster config metastore set <file> --type "oozie" --server "<SQLDatabaseName>.database.windows.net"
		       --database "<OozieDatabaseName>" --user "<SQLUsername>" --metastorePassword "<SQLPassword>"

		#Run this command to create a cluster by using the config file
		azure hdinsight cluster create --config <file>

	>[AZURE.NOTE]La base de datos SQL de Azure usada para la tienda de metadatos debe permitir la conectividad con otros servicios de Azure, incluido HDInsight de Azure. En el panel de base de datos SQL de Azure, en el lado derecho, haga clic en el nombre de servidor. Este es el servidor en el que se ejecuta la instancia de base de datos SQL. Cuando se encuentre en la vista de servidor, haga clic en **Configurar** y luego, en **Servicios de Azure**, haga clic en **Sí** y en **Guardar**.


	![HDI.CLIClusterCreationConfig][image-cli-clustercreation-config]


**Para enumerar y mostrar los detalles del clúster**

- Use los comandos siguientes para enumerar y mostrar los detalles del clúster:

		azure hdinsight cluster list
		azure hdinsight cluster show <ClusterName>

	![HDI.CLIListCluster][image-cli-clusterlisting]


**Para eliminar un clúster**

- Use el comando siguiente para eliminar un clúster:

		azure hdinsight cluster delete <ClusterName>



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

5. En el menú **Herramientas**, haga clic en **Administrador de paquetes NuGet** y, a continuación, haga clic en la **Consola del administrador de paquetes**.

6. Ejecute el siguiente comando en la consola para instalar los paquetes:

		Install-Package Microsoft.WindowsAzure.Management.HDInsight

	Este comando agrega las bibliotecas y referencias .NET a los paquetes para el proyecto de Visual Studio actual.

7. En el Explorador de soluciones, haga doble clic en **Program.cs** para abrirlo.

8. Agregue la siguiente instrucción en la parte superior del archivo:

		using System.Security.Cryptography.X509Certificates;
		using Microsoft.WindowsAzure.Management.HDInsight;
		using Microsoft.WindowsAzure.Management.HDInsight.ClusterProvisioning;


9. En la función Main(), copie y pegue el siguiente código:

        string certfriendlyname = "<CertificateFriendlyName>";     // Friendly name for the certificate you created earlier  
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
        X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.FriendlyName == certfriendlyname);

        // Create the Storage account if it doesn't exist

        // Create the container if it doesn't exist.

		// Create an HDInsightClient object
        HDInsightCertificateCredential creds = new HDInsightCertificateCredential(new Guid(subscriptionid), cert);
        var client = HDInsightClient.Connect(creds);

		// Supply the cluster information
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

10. Reemplace las variables al principio de la función Main().

**Para ejecutar la aplicación**

Mientras la aplicación está abierta en Visual Studio, presione **F5** para ejecutarla. Una ventana de consola se abrirá y mostrará el estado de la aplicación. La creación del clúster de HDInsight puede durar varios minutos.



##<a id="nextsteps"></a> Pasos siguientes
En este artículo, ha aprendido a aprovisionar un clúster de HDInsight de varias formas. Para obtener más información, consulte los artículos siguientes:

* [Introducción a HDInsight de Azure](hdinsight-get-started.md): aprenda a empezar a trabajar con su clúster de HDInsight
* [Uso de Sqoop con HDInsight](hdinsight-use-sqoop.md): aprenda copiar datos entre HDInsight y Base de datos SQL o SQL Server
* [Administración de HDInsight con PowerShell](hdinsight-administer-use-powershell.md): aprenda a trabajar con HDInsight mediante PowerShell
* [Envío de trabajos de Hadoop mediante programación](hdinsight-submit-hadoop-jobs-programmatically.md): aprenda a enviar trabajos a HDInsight mediante programación
* [Documentación del SDK de HDInsight de Azure][hdinsight-sdk-documentation]\: descubra el SDK de HDInsight


[hdinsight-sdk-documentation]: http://msdn.microsoft.com/library/dn479185.aspx
[hdinsight-hbase-custom-provision]: http://azure.microsoft.com/documentation/articles/hdinsight-hbase-get-started/

[hdinsight-customize-cluster]: hdinsight-hadoop-customize-cluster.md
[hdinsight-get-started]: hdinsight-get-started.md
[hdinsight-storage]: hdinsight-use-blob-storage.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md
[hadoop-hdinsight-intro]: hdinsight-hadoop-introduction.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-powershell-reference]: http://msdn.microsoft.com/library/windowsazure/dn479228.aspx
[hdinsight-storm-get-started]: hdinsight-storm-getting-started.md

[azure-management-portal]: https://manage.windowsazure.com/

[azure-command-line-tools]: xplat-cli.md

[azure-create-storageaccount]: storage-create-storage-account.md

[apache-hadoop]: http://go.microsoft.com/fwlink/?LinkId=510084
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[hdi-remote]: http://azure.microsoft.com/documentation/articles/hdinsight-administer-use-management-portal/#rdp


[Powershell-install-configure]: install-configure-powershell.md

[image-hdi-customcreatecluster]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.png
[image-hdi-customcreatecluster-clusteruser]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.ClusterUser.png
[image-hdi-customcreatecluster-storageaccount]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.StorageAccount.png
[image-hdi-customcreatecluster-addonstorage]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.AddOnStorage.png

[image-customprovision-page1]: ./media/hdinsight-provision-clusters/HDI.CustomProvision.Page1.png "Proporcionar detalles del clúster de HDInsight de Hadoop"
[image-customprovision-page3]: ./media/hdinsight-provision-clusters/HDI.CustomProvision.Page3.png "Proporcionar usuarios del clúster de HDInsight de Hadoop"
[image-customprovision-page4]: ./media/hdinsight-provision-clusters/HDI.CustomProvision.Page4.png "Proporcionar detalles de la cuenta de almacenamiento del clúster de HDInsight de Hadoop"
[image-customprovision-page5]: ./media/hdinsight-provision-clusters/HDI.CustomProvision.Page5.png "Proporcionar detalles de la cuenta de almacenamiento adicionales del clúster de HDInsight de Hadoop"
[image-customprovision-page6]: ./media/hdinsight-provision-clusters/HDI.CustomProvision.Page6.png "Configurar la acción de script para personalizar un clúster de HDInsight"


[image-cli-account-download-import]: ./media/hdinsight-provision-clusters/HDI.CLIAccountDownloadImport.png
[image-cli-clustercreation]: ./media/hdinsight-provision-clusters/HDI.CLIClusterCreation.png
[image-cli-clustercreation-config]: ./media/hdinsight-provision-clusters/HDI.CLIClusterCreationConfig.png
[image-cli-clusterlisting]: ./media/hdinsight-provision-clusters/HDI.CLIListClusters.png "Enumeración y visualización de clústeres"

[image-hdi-ps-provision]: ./media/hdinsight-provision-clusters/HDI.ps.provision.png

[img-hdi-cluster]: ./media/hdinsight-provision-clusters/HDI.Cluster.png

[89e2276a]: hdinsight-use-sqoop.md "Uso de Sqoop con HDInsight"

<!--HONumber=54-->