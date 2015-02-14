<properties 
	pageTitle="Aprovisionamiento de clústeres de HBase en Red virtual de Azure | Azure" 
	description="Obtenga información sobre cómo crear clústeres de HDInsight en Red virtual de Azure." 
	services="hdinsight" 
	documentationCenter="" 
	authors="mumian" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/19/2014" 
	ms.author="jgao"/>

# Aprovisionamiento de clústeres de HBase en Red virtual de Azure

Aprenda a crear clústeres de Hbase HDInsight en [Red virtual de Azure][1]. 

Con la integración de red virtual, los clústeres de HBase se pueden implementar en la misma red virtual como sus aplicaciones para que estas puedan comunicarse directamente con HBase. Entre las ventajas se incluye lo siguiente:

- Conectividad directa entre la aplicación web y los nodos del clúster de HBase, que permite la comunicación mediante las API RPC de Java de HBase.
- Mejora del rendimiento, ya que el tráfico no pasa por varias puertas de enlace y equilibradores de carga. 
- Procesamiento más seguro de la información confidencial sin exponer un extremo público.


##En este artículo

- [Requisitos previos](#prerequisites)
- [Aprovisionamiento de clústeres de HBase en una red virtual](#hbaseprovision)
- [Conexión al clúster de HBase aprovisionado en la red virtual mediante las API RPC de Java de HBase](#connect)
- [Aprovisionamiento de un clúster de HBase usando PowerShell](#powershell)
- [Pasos siguientes](#nextsteps)

##<a id="prerequisites"></a>Requisitos previos
Antes de empezar este tutorial, debe contar con lo siguiente:

- **Una suscripción de Azure**. Azure es una plataforma basada en suscripción. Para obtener más información acerca de cómo obtener una suscripción, consulte [Opciones de compra][azure-purchase-options], [Ofertas para miembros][azure-member-offers] o [Prueba gratuita][azure-free-trial].

- **Una estación de trabajo con Azure PowerShell instalado y configurado**. Para obtener más información, consulte [Instalación y configuración de Azure PowerShell][powershell-install]. Para ejecutar scripts de PowerShell, debe ejecutar Azure PowerShell como administrador y establecer la directiva de ejecución en *RemoteSigned*. Consulte [Uso del cmdlet Set-ExecutionPolicy][2].

	Antes de ejecutar scripts de PowerShell, asegúrese de estar conectado a su suscripción de Azure mediante el siguiente cmdlet:

		Add-AzureAccount

	Si tiene varias suscripciones a Azure, utilice el siguiente cmdlet para definir la suscripción actual:

		Select-AzureSubscription <AzureSubscriptionName>


##<a id="hbaseprovision"></a>Aprovisionamiento de clústeres de HBase en una red virtual 

**Para crear una red virtual usando el portal de administración:**

1. Inicie sesión en el [Portal de administración de Azure][azure-portal].
2. En la esquina inferior izquierda, haga clic en **NUEVO**, **SERVICIOS DE RED**, **RED VIRTUAL** y, a continuación, en **CREACIÓN RÁPIDA**.
3. Escriba o seleccione los valores siguientes:

	- **Nombre**: El nombre de la red virtual.
	- **Espacio de direcciones**: elija un espacio de direcciones para la red virtual lo bastante grande para proporcionar direcciones para todos los nodos del clúster. De lo contrario, se producirá un error en el aprovisionamiento. Para completar este tutorial, puede elegir cualquiera de las tres opciones. 
	- **Número máximo de VM**: elija uno de los recuentos de VM máximos. Este valor determina el número de hosts (VM) posibles que se pueden crear en el espacio de direcciones. Para completar este tutorial, es suficiente con **4096 [CIDR: /20]**. 
	- **Ubicación**: la ubicación debe ser la misma que el clúster de HBase que creará.
	- **Servidor DNS**: en este artículo se usa un servidor DNS interno proporcionado por Azure, de modo que puede elegir **Ninguno**. Asimismo, se admite una configuración de red más avanzada con servidores DNS personalizados. Para obtener instrucciones detalladas, consulte [Resolución de nombres (DNS)](http://msdn.microsoft.com/library/azure/jj156088.aspx).
4. Haga clic en **CREAR UNA RED VIRTUAL**. El nombre de la nueva red virtual aparecerá en la lista. Espere hasta que aparezca **Creado** en la columna de estado.
5. En el panel principal, haga clic en la red virtual que acaba de crear.
6. En la parte superior de la página, haga clic en **PANEL**.
7. En **vista rápida**, tome nota del **ID. DE RED VIRTUAL**. Lo necesitará al aprovisionar el clúster de HBase.
8. En la parte superior de la página, haga clic en **CONFIGURAR**.
9. En la parte inferior de la página, el nombre de subred predeterminado es **Subred-1**. Opcionalmente, puede cambiar el nombre de la subred o agregar una nueva subred para el clúster de HBase. Anote el nombre de subred, lo necesitará al aprovisionar el clúster.
10. Compruebe **CIDR(RECUENTO DE DIRECCIONES) ** para la subred que se usará para el clúster. El recuento de direcciones debe ser mayor que el número de nodos de trabajo más siete (puerta de enlace: 2, nodo principal: 2, Zookeeper: 3). Por ejemplo, si necesita un clúster de HBase de 10 nodos, el recuento de direcciones para la subred debe ser mayor que 17 (10+7). De lo contrario, se producirá un error en la implementación.

	> [AZURE.NOTE] Se recomienda encarecidamente designar una única subred para un clúster. 

11. Haga clic en **Guardar** en la parte inferior de la página, si ha actualizado los valores de subred.



> [AZURE.NOTE] Los clústeres de HDInsight usan el almacenamiento de blobs de Azure para almacenar datos. Para obtener más información, consulte [Uso del almacenamiento de blobs de Azure con Hadoop en HDInsight][hdinsight-storage]. Necesitará una cuenta de almacenamiento y un contenedor de almacenamiento de blobs. La ubicación de la cuenta de almacenamiento debe coincidir con la ubicación de la red virtual y del clúster.

**Para crear una cuenta de almacenamiento de Azure y un contenedor de almacenamiento de blobs:**

1. Inicie sesión en el [Portal de administración de Azure][azure-portal].
2. Haga clic en **NUEVO** en la esquina inferior izquierda, seleccione **SERVICIOS DE DATOS**, seleccione **ALMACENAMIENTO** y después haga clic en **CREACIÓN RÁPIDA**.

3. Escriba o seleccione los valores siguientes:

	- **URL**: el nombre de la cuenta de almacenamiento.
	- **UBICACIÓN**: la ubicación de la cuenta de almacenamiento. Asegúrese de que coincida con la ubicación de la red virtual. No se admiten grupos de afinidad.
	- **REPLICACIÓN**: con fines de evaluación, use **Localmente redundante** para reducir el coste.

4. Haga clic en **CREAR CUENTA DE ALMACENAMIENTO**.  La nueva cuenta de almacenamiento aparecerá en la lista de almacenamiento. 
5. Espere hasta que la característica de **estado** de la nueva cuenta de almacenamiento cambie a **En línea**.
6. Haga clic en la nueva cuenta de almacenamiento en la lista para seleccionarla.
7. Haga clic en **ADMINISTRAR CLAVES DE ACCESO** en la parte inferior de la página.
8. Tome nota de los valores de los campos **NOMBRE DE CUENTA DE ALMACENAMIENTO** y **CLAVE DE ACCESO PRIMARIA** (o **CLAVE DE ACCESO SECUNDARIA**.  Cualquiera de las claves funciona).  Los necesitará más adelante en el tutorial.
9. En la parte superior de la página, haga clic en **CONTENEDOR**.
10. En la parte inferior de la página, haga clic en **AGREGAR**.
11. Escriba el nombre del contenedor.  Este contenedor se usará como contenedor predeterminado del clúster de HBase. De forma predeterminada, el nombre del contenedor predeterminado coincide con el nombre del clúster. Conserve el campo **ACCESO** como **Privado**.  
12. Haga clic en el icono de verificación para crear el contenedor.

**Para aprovisionar un clúster de HBase usando el portal de Azure:**

> [AZURE.NOTE] Para obtener más información acerca del aprovisionamiento de un clúster de HBase nuevo usando PowerShell, consulte [Aprovisionamiento de un clúster de HBase usando PowerShell](#powershell).

1. Inicie sesión en el [Portal de administración de Azure][azure-portal].

2. Haga clic en **NUEVO** en la esquina inferior izquierda, elija **SERVICIOS DE DATOS**, **HDINSIGHT** y haga clic en **CREACIÓN PERSONALIZADA**.

3. Escriba un NOMBRE DE CLÚSTER, seleccione HBase como TIPO DE CLÚSTER y elija la VERSIÓN DE HDINSIGHT.

	![Provide details for the HBase cluster][img-provision-cluster-page1]

	Haga clic en el botón derecho.

4. Escriba o seleccione lo siguiente:

	- **NODOS DE DATOS**: especifique el número de nodos de datos para el clúster de HBase. 
	- **REGIÓN/RED VIRTUAL**: seleccione la red virtual de Azure que ha creado.
	- **SUBREDES DE LA RED VIRTUAL**: seleccione una subred. El nombre predeterminado es **Subred-1**.

	Haga clic en el botón derecho.

5. Escriba el **NOMBRE DE USUARIO** y la **CONTRASEÑA** del usuario de Hadoop que se va a usar para este clúster y, a continuación, haga clic en el botón derecho.
6. Seleccione si se va a usar una cuenta de almacenamiento nueva o existente como cuenta de almacenamiento predeterminada del clúster y, a continuación, haga clic en el botón derecho.

7. En la página **Acciones de scripts**, haga clic en **agregar acción de script** para proporcionar información detallada sobre el script personalizado que desea ejecutar para personalizar un clúster durante su creación. Por ejemplo, puede usar una acción de script a fin de personalizar un clúster para instalar <a href="http://spark.apache.org/docs/latest/index.html" target="_blank">Spark de Apache</a>. Para obtener más información, consulte [Personalización de un clúster de HDInsight mediante la acción de script][hdinsight-customize-cluster]. 
	
	![Configure Script Action to customize an HDInsight HBase cluster][img-provision-cluster-page5]

	<table border='1'>
		<tr><th>Propiedad</th><th>Valor</th></tr>
		<tr><td>Nombre</td>
			<td>Especifique un nombre para la acción de script.</td></tr>
		<tr><td>URI de script</td>
			<td>Especifique el URI al script que se ha invocado para personalizar el clúster.</td></tr>
		<tr><td>Tipo de nodo</td>
			<td>Especifica los nodos en los que se ejecuta el script de personalización. Puede elegir <b>Todos los nodos</b>, <b>Solo nodos principales</b>, o <b>Solo nodos de trabajo</b>.
		<tr><td>Parámetros</td>
			<td>Especifique los parámetros, si lo requiere el script.</td></tr>
	</table>

	Puede agregar más de una acción de script para instalar varios componentes en el clúster. Una vez agregados los scripts, haga clic en la marca de verificación para iniciar el aprovisionamiento del clúster.


Para comenzar a trabajar con el nuevo clúster de HBase, utilice los procedimientos que encontrará en [Introducción al uso de HBase con Hadoop en HDInsight][hbase-get-started].

##<a id="connect"></a>Conexión al clúster de HBase aprovisionado en la red virtual mediante las API RPC de Java de HBase

1.	Aprovisiona una máquina virtual IaaS en la misma red virtual de Azure y la misma subred. De este modo, tanto la máquina virtual como el clúster de HBase usan el mismo servidor DNS interno para resolver nombres de host. Para ello, debe elegir la opción de desde la galería y seleccionar la red virtual en lugar de un centro de datos. Para obtener instrucciones, consulte [Creación de una máquina virtual que ejecuta Windows Server][vm-create]. Una imagen de Windows Server 2012 estándar con una VM pequeña es suficiente.
	
2.	Cuando use una aplicación Java para conectarse a HBase en modo remoto, debe usar el nombre de dominio completo (FQDN). Para determinarlo, debe obtener el sufijo DNS específico de la conexión del clúster de HBase. Para ello, use Curl para consultar Ambari o Escritorio remoto para conectarse al clúster.

	* **Curl**: use el comando siguiente:

			curl -u <username>:<password> -k https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/services/hbase/components/hbrest

		En los datos de JSON devueltos, busque la entrada "host_name". Esta entrada contendrá el nombre de dominio completo (FQDN) de los nodos del clúster. Por ejemplo:

			...
			"host_name": "wordkernode0.<clustername>.b1.cloudapp.net
			...

		La parte del nombre de dominio que comienza con el nombre del clúster es el sufijo DNS. Por ejemplo, mycluster.b1.cloudapp.net.

	* **PowerShell**: use el siguiente script de PowerShell para registrar la función  **Get-ClusterDetail**, que se puede usar para devolver el sufijo DNS.

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
			     Displays information to facilitate HDInsight cluster to cluster scinario within same virtual network.
				.Description
				 This command shows following 4 properties of an HDInsight cluster.
				 1. ZookeeperQuorum (only support HBase type cluster)
					Shows the value of hbase property "hbase.zookeeper.quorum".
				 2. ZookeeperClientPort (only support HBase type cluster)
					Shows the value of hbase property "hbase.zookeeper.property.clientPort".
				 3. HBaseRestServers (only support HBase type cluster)
					Shows a list of host FQDNs which run HBase rest server.
				 4. FQDNSuffix (support all type cluster)
					Shows FQDN suffix of hosts in the cluster.
			    .EXAMPLE
			     Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName ZookeeperQuorum
			     This command shows the value of hbase property "hbase.zookeeper.quorum".
			    .EXAMPLE
			     Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName ZookeeperClientPort
			     This command shows the value of hbase property "hbase.zookeeper.property.clientPort".
			    .EXAMPLE
			     Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName HBaseRestServers
			     This command shows a list of host FQDNs which run HBase rest server.
			    .EXAMPLE
			     Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName FQDNSuffix
			     This command shows FQDN suffix of hosts in the cluster.
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

		Después de ejecutar el script de PowerShell, use el siguiente comando para devolver el sufijo DNS usando la función Get-ClusterDetail. Especifique el nombre del clúster de HBase de HDInsight, el nombre y la contraseña de administrador cuando use este comando.

			Get-ClusterDetail -ClusterDnsName <yourclustername> -PropertyName FQDNSuffix -Username <clusteradmin> -Password <clusteradminpassword>

		Este código devolverá el sufijo DNS. Por ejemplo, **yourclustername.b4.internal.cloudapp.net**.

	> [AZURE.NOTE] Puede usar también Escritorio remoto para conectarse al clúster de HBase (se conectará al nodo principal) y ejecutar **ipconfig** desde el símbolo del sistema para obtener el sufijo DNS. Para obtener instrucciones sobre cómo habilitar RDP y cómo conectarse al clúster con RDP, consulte [Administración de clústeres de Hadoop en HDInsight mediante el Portal de administración de Azure][hdinsight-admin-portal].
	>
	> ![hdinsight.hbase.dns.surffix][img-dns-surffix]


<!-- 
3.	Cambie la configuración del sufijo DNS principal de la máquina virtual. Esto permite que la máquina virtual resuelva automáticamente el nombre de host del clúster de HBase sin la especificación explícita del sufijo. Por ejemplo, el nombre de host *workernode0* se resolverá correctamente en workernode0 del clúster de HBase. 

	Para cambiar la configuración:

	1. RDP en la máquina virtual. 
	2. Abra **Editor de directivas de grupo local**. gpedit.msc se puede ejecutar.
	3. Expanda **Configuración del equipo**, **Plantillas administrativas**, **Red** y, a continuación, haga clic en **Cliente DNS**. 
	- Establezca **Sufijo DNS primario** en el valor obtenido en el paso 2: 

		![hdinsight.hbase.primary.dns.suffix][img-primary-dns-suffix]
	4. Haga clic en **OK**. 
	5. Reinicio de la máquina virtual.
-->

Para comprobar que la máquina virtual puede comunicarse con el clúster de HBase, use el comando `ping headnode0.<dns suffix>` desde la máquina virtual. Por ejemplo, ping headnode0.mycluster.b1.cloudapp.net

Para usar esta información en una aplicación Java, puede seguir los pasos que se indican en [Uso de Maven para crear aplicaciones Java que utilicen HBase con HDInsight (Hadoop)](azure.microsoft.com/es-es/documentation/articles/hdinsight-hbase-build-java-maven/) para crear una aplicación. Para que la aplicación se conecte a un servidor HBase remoto, modifique el archivo **hbase-site.xml** de este ejemplo para que use el FQDN de ZooKeeper. Por ejemplo:

	<property>
    	<name>hbase.zookeeper.quorum</name>
    	<value>zookeeper0.<dns suffix>,zookeeper1.<dns suffix>,zookeeper2.<dns suffix></value>
	</property>

> [AZURE.NOTE] Para obtener más información sobre la resolución de nombres en redes virtuales de Azure, incluido el uso de su propio servidor DNS, consulte [Resolución de nombres (DNS)](http://msdn.microsoft.com/es-es/library/azure/jj156088.aspx).

##<a id="powershell"></a>Aprovisionamiento de un clúster de HBase usando Azure PowerShell

**Para aprovisionar un clúster de HBase usando Azure PowerShell**

1. Abra PowerShell ISE.
2. Copie y pegue la siguiente copia en el panel de scripts.

		$hbaseClusterName = "<HBaseClusterName>"
		$hadoopUserName = "<HBaseClusterUsername>"
		$hadoopUserPassword = "<HBaseClusterUserPassword>"
		$location = "<HBaseClusterLocation>"  #i.e. "West US"
		$clusterSize = <HBaseClusterSize>  
		$vnetID = "<AzureVirtualNetworkID>"
		$subNetName = "<AzureVirtualNetworkSubNetName>"
		$storageAccountName = "<AzureStorageAccountName>" # Do not use the full name here
		$storageAccountKey = "<AzureStorageAccountKey>"
		$storageContainerName = "<AzureBlobStorageContainer>"
		
		$password = ConvertTo-SecureString $hadoopUserPassword -AsPlainText -Force
		$creds = New-Object System.Management.Automation.PSCredential ($hadoopUserName, $password) 
		
		New-AzureHDInsightCluster -Name $hbaseClusterName `
		                          -ClusterType HBase `
		                          -Version 3.1 `
		                          -Location $location `
		                          -ClusterSizeInNodes $clusterSize `
		                          -Credential $creds `
		                          -VirtualNetworkId $vnetID `
		                          -SubnetName $subNetName `
		                          -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" `
		                          -DefaultStorageAccountKey $storageAccountKey `
		                          -DefaultStorageContainerName $storageContainerName


3. Haga clic en **Ejecutar script** o presione **F5**.
4. Para validar el clúster, puede comprobar el clúster del portal de administración o ejecutar el siguiente cmdlet de PowerShell en el panel inferior:

	Get-AzureHDInsightCluster 

##<a id="nextsteps"></a>Pasos siguientes

En este tutorial, hemos aprendido a aprovisionar un clúster de HBase. Para obtener más información, consulte:

- [Introducción a HDInsight][hdinsight-get-started]
- [Aprovisionamiento de clústeres de Hadoop en HDInsight][hdinsight-provision] 
- [Introducción al uso de HBase con Hadoop en HDInsight][hbase-get-started]
- [Análisis de opiniones de Twitter con HBase en HDInsight][hbase-twitter-sentiment]
- [Información general sobre redes virtuales][vnet-overview].


[1]: http://azure.microsoft.com/es-es/services/virtual-network/
[2]: http://technet.microsoft.com/es-es/library/ee176961.aspx
[3]: http://technet.microsoft.com/es-es/library/hh847889.aspx

[hbase-get-started]: ../hdinsight-hbase-get-started/
[hbase-twitter-sentiment]: ../hdinsight-hbase-twitter-sentiment/
[vnet-overview]: http://msdn.microsoft.com/library/azure/jj156007.aspx
[vm-create]: ../virtual-machines-windows-tutorial/

[azure-portal]: https://manage.windowsazure.com
[azure-create-storageaccount]: ../storage-create-storage-account/ 
[azure-purchase-options]: http://azure.microsoft.com/es-es/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/es-es/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/es-es/pricing/free-trial/

[hdinsight-admin-powershell]: ../hdinsight-administer-use-powershell/
[hdinsight-admin-portal]: ../hdinsight-administer-use-management-portal/#rdp

[hdinsight-powershell-reference]: http://msdn.microsoft.com/es-es/library/windowsazure/dn479228.aspx


[twitter-streaming-api]: https://dev.twitter.com/docs/streaming-apis
[twitter-statuses-filter]: https://dev.twitter.com/docs/api/1.1/post/statuses/filter


[powershell-install]: ../install-configure-powershell


[hdinsight-customize-cluster]: ../hdinsight-hadoop-customize-cluster/
[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-get-started]: ../hdinsight-get-started/
[hdinsight-storage-powershell]: ../hdinsight-use-blob-storage/#powershell
[hdinsight-analyze-flight-delay-data]: ../hdinsight-analyze-flight-delay-data/
[hdinsight-storage]: ../hdinsight-use-blob-storage/
[hdinsight-use-sqoop]: ../hdinsight-use-sqoop/
[hdinsight-power-query]: ../hdinsight-connect-excel-power-query/
[hdinsight-hive-odbc]: ../hdinsight-connect-excel-hive-ODBC-driver/

[img-dns-surffix]: ./media/hdinsight-hbase-provision-vnet/DNSSuffix.png
[img-primary-dns-suffix]: ./media/hdinsight-hbase-provision-vnet/PrimaryDNSSuffix.png
[img-provision-cluster-page1]: ./media/hdinsight-hbase-provision-vnet/hbasewizard1.png "Provision details for the new HBase cluster"
[img-provision-cluster-page5]: ./media/hdinsight-hbase-provision-vnet/hbasewizard5.png "Use Script Action to customize an HBase cluster"
<!--HONumber=42-->
