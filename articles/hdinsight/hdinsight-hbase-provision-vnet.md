<properties
	pageTitle="Aprovisionamiento de clústeres de HBase en una red virtual | Microsoft Azure"
	description="Introducción al uso de HBase en HDInsight de Azure Aprenda a crear clústeres de HBase de HDInsight en Red virtual de Azure."
	keywords=""
	services="hdinsight,virtual-network"
	documentationCenter=""
	authors="mumian"
	manager="paulettm"
	editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="11/18/2015"
   ms.author="jgao"/>

# Aprovisionamiento de clústeres de HBase en Red virtual de Azure 

Aprenda a crear clústeres de HBase en Azure HDInsight en una [red virtual de Azure][1].

[AZURE.INCLUDE [hdinsight-azure-preview-portal](../../includes/hdinsight-azure-preview-portal.md)]

* [Aprovisionamiento de clústeres de HBase en Red virtual de Azure](hdinsight-hbase-provision-vnet-v1.md)

Con la integración de red virtual, los clústeres de HBase se pueden implementar en la misma red que sus aplicaciones para que estas puedan comunicarse directamente con HBase. Entre las ventajas se incluye lo siguiente:

- Conectividad directa entre la aplicación web y los nodos del clúster de HBase, lo cual permite la comunicación mediante las API de llamada a procedimiento remoto (RPC) de Java de HBase.
- Rendimiento mejorado gracias a que el tráfico ya no tiene que examinar varias puertas de enlace y equilibradores de carga.
- La posibilidad de procesar información confidencial de una manera segura sin exponer un extremo público.

##Requisitos previos
Antes de empezar este tutorial, debe contar con lo siguiente:

- **Una suscripción de Azure**. Consulte [Obtención de una versión de evaluación gratuita](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- **Una estación de trabajo con Azure PowerShell**. Consulte [Install and use Azure PowerShell (Instalación y uso de Azure PowerShell)](http://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/). Para obtener más información, consulte [Instalación y configuración de Azure PowerShell](../install-configure-powershell.md). Para ejecutar scripts de Azure PowerShell, debe ejecutar Azure PowerShell como administrador y establecer la directiva de ejecución en *RemoteSigned*. Consulte [Uso del cmdlet Set-ExecutionPolicy][2].

	Antes de ejecutar scripts de Azure PowerShell, asegúrese de estar conectado a su suscripción de Azure mediante el siguiente cmdlet:

		Add-AzureAccount

	Si tiene varias suscripciones a Azure, utilice el siguiente cmdlet para definir la suscripción actual:

		Select-AzureSubscription <AzureSubscriptionName>


## Aprovisionamiento de clústeres de HBase en una red virtual

Las aplicaciones normalmente están formadas por varios componentes. En este tutorial, dispondrá de:

- una red virtual de Azure
- una cuenta de almacenamiento de Azure
- un clúster de HBase de HDInsight de Azure
- (opcional) una máquina virtual de Azure que actúa como servidor DNS

Administrador de recursos de Azure permite trabajar con los recursos de la aplicación como un grupo. Puede implementar, actualizar o eliminar todos los recursos de la aplicación en una operación única y coordinada. Para la implementación se utiliza una plantilla, y esta plantilla puede trabajar en diferentes entornos, como pruebas, ensayo y producción. Puede aclarar la facturación de la organización consultando los costes acumulados de todo el grupo.

**Para crear un grupo de recursos**

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).
2. Haga clic en **NUEVO**, en **Administración** y luego en **Grupo de recursos**.
3. Escriba o seleccione los valores siguientes:

	- **Nombre del grupo de recursos**: escriba un nombre para el grupo de recursos.
	- **Suscripción**: seleccione la suscripción de Azure usada para este grupo de recursos.
	- **Ubicación del grupo de recursos**: seleccione un centro de datos de Azure. Esta ubicación no tiene que coincidir con la ubicación del clúster de HDInsight.

4. Haga clic en **Crear**.

Antes de aprovisionar un clúster de HBase, debe tener una red virtual de Azure.

**Para crear una red virtual usando el portal de Azure, siga estos pasos:**

1. Inicie sesión en el [Portal](https://portal.azure.com).
2. Haga clic en **NUEVO**, en **Red** y luego en **Red virtual**.
3. En **Seleccionar un modelo de implementación**, seleccione **Clásico** si va a usar un clúster de HDInsight basado en Windows, seleccione **Administrador de recursos** si va a usar un clúster de HDInsight basado en Linux. Por último, haga clic en **Crear**.

    > [AZURE.NOTE]Los clústeres basados en Windows requieren una red virtual v1 (clásica), mientras que los clústeres basados en Linux requieren una red virtual v2 (Administrador de recursos de Azure). Si no dispone del tipo correcto de red, no se podrá usar cuando cree el clúster.
    >
    > Si dispone de recursos en una red virtual que no se puede usar por el clúster que planea crear, puede crear una nueva red virtual que se pueda usar por el clúster y conectarla a la red virtual incompatible. Luego puede crear el clúster en la versión de la red que requiere y podrá tener acceso a los recursos de la otra red puesto que los dos están combinadas. Para más información sobre cómo conectar redes virtuales clásicas y nuevas, vea [Conexión de redes virtuales clásicas a redes virtuales nuevas](../virtual-network/virtual-networks-arm-asm-s2s.md).
    
4. Escriba o seleccione los valores siguientes:

	- **Nombre**: el nombre de la red virtual.
	- **Espacio de direcciones**: elija un espacio de direcciones para la red virtual que sea lo bastante grande para proporcionar direcciones para todos los nodos del clúster. De lo contrario, se producirá un error en el aprovisionamiento. Para recorrer este tutorial, puede usar los valores predeterminados. Haga clic en **Aceptar** para guardar los cambios.
    
        > [AZURE.NOTE]Si va a usar esta red virtual con varios clústeres de HDInsight, se recomienda designar una subred única para cada clúster.
         
	- **Grupo de recursos**: seleccione el grupo de recursos que creó anteriormente en el tutorial.
	- **Suscripción**: seleccione la suscripción de Azure que quiera usar para esta red virtual.
	- **Ubicación**: la ubicación debe ser la misma que el clúster de HBase que va a crear
    
        > [AZURE.NOTE]HDInsight de Azure solo admite redes virtuales basadas en la ubicación. Actualmente, no funciona con redes virtuales basadas en grupos de afinidad.
        
    Para obtener información sobre el uso de HDInsight con una red virtual, como por ejemplo, los requisitos de configuración específicos de la red virtual, consulte [Extend HDInsight capabilities by using an Azure Virtual Network](hdinsight-extend-hadoop-virtual-network.md) (Ampliar las capacidades de HDInsight con una red virtual de Azure).

5. Haga clic en **Crear**.

De forma predeterminada, la red virtual usa un servidor de sistema de nombres de dominio (DNS) interno proporcionado por Azure. Asimismo, se admiten configuraciones de red más avanzadas con servidores DNS personalizados. Para obtener instrucciones detalladas, consulte [Resolución de nombres (DNS)](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

**(Opcional) Para agregar una máquina virtual de servidor DNS a la red virtual**

Un servidor DNS es opcional, pero es necesario en algunos casos. El procedimiento se ha documentado en [Configuración de DNS entre dos redes virtuales de Azure][hdinsight-hbase-replication-dns]. Básicamente, será necesario llevar a cabo estos pasos:

1. Agregar una máquina virtual de Azure a una red virtual
2. Configurar una dirección IP estática para la máquina virtual
3. Agregar el rol de servidor DNS a la máquina virtual
4. Asignar el servidor DNS a la red virtual

**Para aprovisionar un clúster de HBase usando el Portal de Azure, siga estos pasos:**

> [AZURE.NOTE]Para obtener más información acerca del aprovisionamiento de un nuevo clúster de HBase usando Azure PowerShell, consulte [Aprovisionamiento de un clúster de HBase usando Azure PowerShell](#powershell).


**Para crear un clúster de HDInsight**

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).
2. Haga clic en **NUEVO**, en **Análisis de datos** y luego en **HDInsight**.

    ![Creación de un clúster nuevo en el Portal de Azure](./media/hdinsight-provision-clusters/HDI.CreateCluster.1.png "Creación de un clúster nuevo en el Portal de Azure")

3. Escriba o seleccione los valores siguientes:

  - **Nombre del clúster**: escriba un nombre para el clúster. Si el nombre está disponible, aparecerá una marca de verificación verde junto al nombre del clúster.
  - **Tipo de clúster**: seleccione **HBase**.
  - **Sistema operativo del clúster**: seleccione **Windows Server 2012 R2 Datacenter**.
  - **Suscripción**: seleccione la suscripción de Azure que se usará para aprovisionar este clúster.
  - **Grupo de recursos**: seleccione el grupo de recursos que creó anteriormente en el tutorial.
  - **Credenciales**: configure el nombre de usuario y la contraseña del usuario de Hadoop (usuario HTTP). Si habilita Escritorio remoto para el clúster, deberá configurar el nombre de usuario y la contraseña del Escritorio remoto y una fecha de caducidad de la cuenta. Haga clic en **Seleccionar** en la parte inferior para guardar los cambios.
  - **Origen de datos**: seleccione una cuenta de almacenamiento de Azure existente o cree una nueva para usar como sistema de archivos predeterminado para el clúster. El nombre predeterminado para el contenedor predeterminado es el nombre del clúster. La ubicación de la cuenta de almacenamiento también determina la ubicación del clúster.
  - **Nivel de tarifa del nodo**: para fines de aprendizaje o evaluación, seleccione 1 nodo de región para minimizar el costo.

  	- **Método de selección**: establézcalo en **De todas las suscripciones** para habilitar la exploración de cuentas de almacenamiento en todas sus suscripciones. Establézcalo en **Clave de acceso** si desea especificar el **Nombre de almacenamiento** y la **Clave de acceso** de una cuenta de almacenamiento existente.
  	- **Seleccionar cuenta de almacenamiento o Crear nueva**: haga clic en **Seleccionar cuenta de almacenamiento** para buscar y seleccionar una cuenta de almacenamiento existente que desee asociar con el clúster. O bien, haga clic en **Crear nueva** para crear una nueva cuenta de almacenamiento. Use el campo que aparece para especificar el nombre de la cuenta de almacenamiento. Si el nombre está disponible, aparecerá una marca de verificación verde.
    - **Elegir contenedor predeterminado**: use esta opción para escribir el nombre del contenedor predeterminado que se usará para el clúster. Aunque se puede escribir cualquier nombre aquí, se recomienda usar el mismo nombre que el del clúster para que pueda reconocer fácilmente que el contenedor se usa para este clúster concreto.
  	- **Ubicación**: región geográfica en la que se encuentra la cuenta de almacenamiento o donde se creará. Esta ubicación determinará la ubicación del clúster. El clúster y su cuenta de almacenamiento predeterminada debe colocarse en el mismo centro de datos Azure.

  - **Niveles de precio de nodo**: establezca el número de nodos de trabajo que necesita para el clúster. El costo estimado del clúster se mostrará en la hoja.
	- **Configuración opcional**: para este tutorial, solo tendrá que configurar la **red virtual**. Seleccione la red virtual que creó anteriormente en el tutorial. Asegúrese de seleccionar también una subred.

4. Haga clic en **Crear**.


Para comenzar a trabajar con el nuevo clúster de HBase, utilice los procedimientos que encontrará en [Introducción al uso de HBase con Hadoop en HDInsight](../hdinsight-hbase-get-started.md).

##Conexión al clúster de HBase aprovisionado en la red virtual mediante las API RPC de Java de HBase

1.	Aprovisione una máquina virtual de infraestructura como servicio (IaaS) en la misma red virtual de Azure y la misma subred. De este modo, tanto la máquina virtual como el clúster de HBase usan el mismo servidor DNS interno para resolver nombres de host. Para ello, debe elegir la opción **Desde la galería** y seleccionar la red virtual en lugar de un centro de datos. Para obtener instrucciones, consulte [Creación de una máquina virtual que ejecuta Windows Server](../virtual-machines-windows-tutorial.md). Una imagen de Windows Server 2012 estándar con una VM pequeña es suficiente.

2.	Cuando use una aplicación Java para conectarse a HBase en modo remoto, debe usar el nombre de dominio completo (FQDN). Para determinarlo, debe obtener el sufijo DNS específico de la conexión del clúster de HBase. Para ello, use Curl para consultar Ambari o Escritorio remoto para conectarse al clúster.

	* **Curl**: use el comando siguiente:

			curl -u <username>:<password> -k https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/services/hbase/components/hbrest

		En los datos de notación de objetos JavaScript (JSON) devueltos, busque la entrada "host\_name". Esta entrada contendrá el nombre de dominio completo (FQDN) de los nodos del clúster. Por ejemplo:

			...
			"host_name": "wordkernode0.<clustername>.b1.cloudapp.net
			...

		La parte del nombre de dominio que comienza con el nombre del clúster es el sufijo DNS. Por ejemplo, mycluster.b1.cloudapp.net.

	* **Azure PowerShell**: use el siguiente script de Azure PowerShell para registrar la función **Get-ClusterDetail**, que se puede usar para devolver el sufijo DNS:

			function Get-ClusterDetail(
			    [String]
			    [Parameter( Position=0, Mandatory=$true )]
			    $ClusterDnsName,
				[String]
			    [Parameter( Position=1, Mandatory=$true )]
			    $Username,
				[String]
			    [Parameter( Position=2, Mandatory=$true )]
			    $Password,
			    [String]
			    [Parameter( Position=3, Mandatory=$true )]
			    $PropertyName
				)
			{
			<#
			    .SYNOPSIS
			     Displays information to facilitate an HDInsight cluster-to-cluster scenario within the same virtual network.
				.Description
				 This command shows the following 4 properties of an HDInsight cluster:
				 1. ZookeeperQuorum (supports only HBase type cluster)
					Shows the value of HBase property "hbase.zookeeper.quorum".
				 2. ZookeeperClientPort (supports only HBase type cluster)
					Shows the value of HBase property "hbase.zookeeper.property.clientPort".
				 3. HBaseRestServers (supports only HBase type cluster)
					Shows a list of host FQDNs that run the HBase REST server.
				 4. FQDNSuffix (supports all cluster types)
					Shows the FQDN suffix of hosts in the cluster.
			    .EXAMPLE
			     Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName ZookeeperQuorum
			     This command shows the value of HBase property "hbase.zookeeper.quorum".
			    .EXAMPLE
			     Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName ZookeeperClientPort
			     This command shows the value of HBase property "hbase.zookeeper.property.clientPort".
			    .EXAMPLE
			     Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName HBaseRestServers
			     This command shows a list of host FQDNs that run the HBase REST server.
			    .EXAMPLE
			     Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName FQDNSuffix
			     This command shows the FQDN suffix of hosts in the cluster.
			#>

				$DnsSuffix = ".azurehdinsight.net"

				$ClusterFQDN = $ClusterDnsName + $DnsSuffix
				$webclient = new-object System.Net.WebClient
				$webclient.Credentials = new-object System.Net.NetworkCredential($Username, $Password)

				if($PropertyName -eq "ZookeeperQuorum")
				{
					$Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.zookeeper.quorum"
					$Response = $webclient.DownloadString($Url)
					$JsonObject = $Response | ConvertFrom-Json
					Write-host $JsonObject.items[0].properties.'hbase.zookeeper.quorum'
				}
				if($PropertyName -eq "ZookeeperClientPort")
				{
					$Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.zookeeper.property.clientPort"
					$Response = $webclient.DownloadString($Url)
					$JsonObject = $Response | ConvertFrom-Json
					Write-host $JsonObject.items[0].properties.'hbase.zookeeper.property.clientPort'
				}
				if($PropertyName -eq "HBaseRestServers")
				{
					$Url1 = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.rest.port"
					$Response1 = $webclient.DownloadString($Url1)
					$JsonObject1 = $Response1 | ConvertFrom-Json
					$PortNumber = $JsonObject1.items[0].properties.'hbase.rest.port'

					$Url2 = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/services/hbase/components/hbrest"
					$Response2 = $webclient.DownloadString($Url2)
					$JsonObject2 = $Response2 | ConvertFrom-Json
					foreach ($host_component in $JsonObject2.host_components)
					{
						$ConnectionString = $host_component.HostRoles.host_name + ":" + $PortNumber
						Write-host $ConnectionString
					}
				}
				if($PropertyName -eq "FQDNSuffix")
				{
					$Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/services/yarn/components/resourcemanager"
					$Response = $webclient.DownloadString($Url)
					$JsonObject = $Response | ConvertFrom-Json
					$FQDN = $JsonObject.host_components[0].HostRoles.host_name
					$pos = $FQDN.IndexOf(".")
					$Suffix = $FQDN.Substring($pos + 1)
					Write-host $Suffix
				}
			}

		Después de ejecutar el script de Azure PowerShell, use el siguiente comando para devolver el sufijo DNS usando la función **Get-ClusterDetail**. Especifique el nombre del clúster de HBase de HDInsight, el nombre y la contraseña de administrador cuando use este comando.

			Get-ClusterDetail -ClusterDnsName <yourclustername> -PropertyName FQDNSuffix -Username <clusteradmin> -Password <clusteradminpassword>

		Este código devolverá el sufijo DNS. Por ejemplo, **yourclustername.b4.internal.cloudapp.net**.

	> [AZURE.NOTE]Puede usar también Escritorio remoto para conectarse al clúster de HBase (se conectará al nodo principal) y ejecutar **ipconfig** desde el símbolo del sistema para obtener el sufijo DNS. Para obtener instrucciones acerca de cómo habilitar el Protocolo de escritorio remoto (RDP) y conectarse al clúster mediante RDP, consulte [Administración de clústeres de Hadoop en HDInsight mediante el Portal de Azure][hdinsight-admin-portal].
	>
	> ![hdinsight.hbase.dns.surffix][img-dns-surffix]


<!--
3.	Change the primary DNS suffix configuration of the virtual machine. This enables the virtual machine to automatically resolve the host name of the HBase cluster without explicit specification of the suffix. For example, the *workernode0* host name will be correctly resolved to workernode0 of the HBase cluster.

	To make the configuration change:

	1. RDP into the virtual machine.
	2. Open **Local Group Policy Editor**. The executable is gpedit.msc.
	3. Expand **Computer Configuration**, expand **Administrative Templates**, expand **Network**, and then click **DNS Client**.
	- Set **Primary DNS Suffix** to the value obtained in step 2:

		![hdinsight.hbase.primary.dns.suffix][img-primary-dns-suffix]
	4. Click **OK**.
	5. Reboot the virtual machine.
-->

Para comprobar que la máquina virtual puede comunicarse con el clúster de HBase, use el comando `ping headnode0.<dns suffix>` desde la máquina virtual. Por ejemplo, haga ping a headnode0.mycluster.b1.cloudapp.net

Para usar esta información en una aplicación Java, puede seguir los pasos que se indican en [Uso de Maven para crear aplicaciones Java que utilicen HBase con HDInsight (Hadoop)](hdinsight-hbase-build-java-maven.md) para crear una aplicación. Para que la aplicación se conecte a un servidor HBase remoto, modifique el archivo **hbase-site.xml** de este ejemplo para que use el FQDN de ZooKeeper. Por ejemplo:

	<property>
    	<name>hbase.zookeeper.quorum</name>
    	<value>zookeeper0.<dns suffix>,zookeeper1.<dns suffix>,zookeeper2.<dns suffix></value>
	</property>

> [AZURE.NOTE]Para obtener más información sobre la resolución de nombres en redes virtuales de Azure, incluido el uso de su propio servidor DNS, consulte [Resolución de nombres (DNS)](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

##Aprovisionamiento de un clúster de HBase mediante Azure PowerShell

**Para aprovisionar un clúster de HBase mediante Azure PowerShell, siga estos pasos:**

1. Abra el entorno de script integrado de Azure PowerShell (ISE):
2. Copie y pegue lo siguiente en el panel de scripts:

		$hbaseClusterName = "<HBaseClusterName>"
		$hadoopUserName = "<HBaseClusterUsername>"
		$hadoopUserPassword = "<HBaseClusterUserPassword>"
		$location = "<HBaseClusterLocation>"  #i.e. "West US"
		$clusterSize = <HBaseClusterSize>  
		$resourceGroup = "<AzureResourceGroupName>"
		$vnetID = "<AzureVirtualNetworkID>"
		$subNetName = "<AzureVirtualNetworkSubNetName>"
		$storageAccountName = "<AzureStorageAccountName>" # Do not use the full name here
		$storageAccountKey = "<AzureStorageAccountKey>"
		$storageContainerName = "<AzureBlobStorageContainer>"

		$password = ConvertTo-SecureString $hadoopUserPassword -AsPlainText -Force
		$creds = New-Object System.Management.Automation.PSCredential ($hadoopUserName, $password)

		New-AzureHDInsightCluster -ResourceGroupName $resourceGroup `
		                          -ClusterName $hbaseClusterName `
				                    	-ClusterType HBase `
				                    	-Location $location `
				                    	-ClusterSizeInNodes $clusterSize `
		                          -HttpCredential $creds `
				                    	-VirtualNetworkId $vnetID `
				                    	-SubnetName $subNetName `
				                    	-DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" `
				                    	-DefaultStorageAccountKey $storageAccountKey `
		                          -DefaultStorageContainer $storageContainerName


3. Haga clic en **Ejecutar script** o pulse o **F5**.
4. Para validar el clúster, puede comprobar el clúster desde el Portal de Azure o ejecutar el siguiente cmdlet de Azure PowerShell en el panel inferior:

	Get-AzureHDInsightCluster

##Pasos siguientes

En este tutorial, ha aprendido a aprovisionar un clúster de HBase. Para obtener más información, consulte:

- [Introducción a HDInsight](../hdinsight-get-started.md)
- [Configuración de la replicación de HBase en HDInsight](hdinsight-hbase-geo-replication.md)
- [Aprovisionamiento de clústeres de Hadoop en HDInsight](hdinsight-provision-clusters.md)
- [Introducción al uso de HBase con Hadoop en HDInsight](../hdinsight-hbase-get-started.md)
- [Análisis de opiniones de Twitter con HBase en HDInsight](../hdinsight-hbase-twitter-sentiment.md)
- [Información general sobre redes virtuales][vnet-overview]


[1]: http://azure.microsoft.com/services/virtual-network/
[2]: http://technet.microsoft.com/library/ee176961.aspx
[3]: http://technet.microsoft.com/library/hh847889.aspx

[hbase-get-started]: ../hdinsight-hbase-get-started.md
[hbase-twitter-sentiment]: ../hdinsight-hbase-twitter-sentiment.md
[vnet-overview]: ../virtual-network/virtual-networks-overview.md
[vm-create]: ../virtual-machines-windows-tutorial.md

[azure-portal]: https://portal.azure.com
[azure-create-storageaccount]: ../storage-create-storage-account.md
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md#rdp

[hdinsight-powershell-reference]: https://msdn.microsoft.com/library/dn858087.aspx


[twitter-streaming-api]: https://dev.twitter.com/docs/streaming-apis
[twitter-statuses-filter]: https://dev.twitter.com/docs/api/1.1/post/statuses/filter


[powershell-install]: ../install-configure-powershell.md


[hdinsight-customize-cluster]: hdinsight-hadoop-customize-cluster.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-get-started]: ../hdinsight-get-started.md
[hdinsight-storage-powershell]: ../hdinsight-use-blob-storage.md#powershell
[hdinsight-analyze-flight-delay-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-storage]: ../hdinsight-use-blob-storage.md
[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-power-query]: hdinsight-connect-excel-power-query.md
[hdinsight-hive-odbc]: hdinsight-connect-excel-hive-ODBC-driver.md
[hdinsight-hbase-replication-dns]: hdinsight-hbase-geo-replication-configure-DNS.md

[img-dns-surffix]: ./media/hdinsight-hbase-provision-vnet/DNSSuffix.png
[img-primary-dns-suffix]: ./media/hdinsight-hbase-provision-vnet/PrimaryDNSSuffix.png
[img-provision-cluster-page1]: ./media/hdinsight-hbase-provision-vnet/hbasewizard1.png "Detalles de aprovisionamiento para el nuevo clúster de HBase"
[img-provision-cluster-page5]: ./media/hdinsight-hbase-provision-vnet/hbasewizard5.png "Uso de la acción de script para personalizar un clúster de HBase"

[azure-preview-portal]: https://portal.azure.com

<!---HONumber=AcomDC_0114_2016-->