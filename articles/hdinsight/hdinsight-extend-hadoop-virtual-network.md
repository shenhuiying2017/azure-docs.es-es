<properties
	pageTitle="Extensión de HDInsight con Red virtual | Microsoft Azure"  
	description="Aprenda a usar la Red virtual de Azure para conectar HDInsight con otros recursos en la nube o recursos en su centro de datos."
	services="hdinsight"
	documentationCenter=""
	authors="Blackmist"
	manager="paulettm"
	editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="06/20/2016"
   ms.author="larryfr"/>


#Extensión de las funcionalidades de HDInsight con Red virtual de Azure

La Red virtual de Azure permite extender sus soluciones de Hadoop para incorporar recursos locales como SQL Server o para crear redes privadas seguras entre los recursos existentes en la nube.

[AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell-and-cli.md)]


##<a id="whatis"></a>¿Qué es Red virtual de Azure?

[Red virtual de Azure](https://azure.microsoft.com/documentation/services/virtual-network/) permite crear una red segura y persistente que contenga los recursos necesarios para una solución. Una red virtual permite hacer lo siguiente:

* Conectar recursos en la nube en una red privada (solo en la nube)

	![Diagrama de la configuración solo en la nube](media/hdinsight-extend-hadoop-virtual-network/cloud-only.png)

	El uso de Red virtual para vincular servicios de Azure con HDInsight de Azure permite los siguientes escenarios:

	* **Invocación de servicios o trabajos de HDInsight** desde servicios o sitios web de Azure que se ejecutan en máquinas virtuales de Azure.

	* **Transferencia directa de datos** entre HDInsight y SQL Server o SQL Database de Azure u otra solución de almacenamiento de datos en ejecución en una máquina virtual.

	* **Combinación de varios servidores de HDInsight** en una sola solución. Un ejemplo es usar un servidor de HDInsight Storm para consumir datos entrantes y, a continuación, almacenar los datos procesados en un servidor de HDInsight HBase. Los datos sin procesar también podrían almacenarse en un servidor de HDInsight Hadoop para un análisis posterior con MapReduce.

* Conecte sus recursos en la nube a la red de su centro de datos local (sitio a sitio o punto a sitio) usando una red privada virtual (VPN).

	La configuración sitio a sitio permite conectar varios recursos de su centro de datos a la Red virtual de Azure usando una VPN de hardware o el servicio de enrutamiento y acceso remoto.

	![Diagrama de la configuración de sitio a sitio](media/hdinsight-extend-hadoop-virtual-network/site-to-site.png)

	La configuración punto a sitio permite conectar un recurso específico a la Red virtual de Azure usando una VPN de software.

	![Diagrama de la configuración de punto a sitio](media/hdinsight-extend-hadoop-virtual-network/point-to-site.png)

	El uso de Red virtual para vincular la nube y su centro de datos posibilita escenarios similares al de la configuración solo en la nube. Pero, en lugar de limitarse a trabajar con recursos en la nube, también puede trabajar con recursos en su centro de datos.

	* **Transferencia directa de datos** entre HDInsight y el centro de datos. Un ejemplo es el uso de Sqoop para transferir datos desde o hacia SQL Server o leer datos generados por una aplicación de línea de negocio (LoB).

	* **Invocación de servicios o trabajos de HDInsight** desde una aplicación de línea de negocio. Un ejemplo es usar las API de Java de HBase para almacenar y recuperar datos desde un clúster de HDInsight HBase.

Para obtener más información sobre las características, las ventajas y la funcionalidad de Red virtual, consulte [Información general sobre Red virtual de Azure](../virtual-network/virtual-networks-overview.md).

> [AZURE.NOTE] Debe crear la red virtual de Azure antes de aprovisionar un clúster de HDInsight. Para obtener más información, consulte [Tareas de configuración de Red virtual](https://azure.microsoft.com/documentation/services/virtual-network/).

## Requisitos de red virtual

> [AZURE.IMPORTANT] La creación de un clúster de HDInsight en una red virtual requiere configuraciones de red virtual específicas, que se describen en esta sección.

###Redes virtuales basadas en la ubicación

HDInsight de Azure solo admite redes virtuales basadas en la ubicación. Actualmente, no funciona con redes virtuales basadas en grupos de afinidad.

###Red virtual clásica o v2

Los clústeres basados en Windows requieren una red virtual v1 (clásica), mientras que los clústeres basados en Linux requieren una red virtual v2 (Administrador de recursos de Azure). Si no dispone del tipo correcto de red, no se podrá usar cuando cree el clúster.

Si dispone de recursos en una red virtual que no se puede usar por el clúster que planea crear, puede crear una nueva red virtual que se pueda usar por el clúster y conectarla a la red virtual incompatible. Luego puede crear el clúster en la versión de la red que requiere y podrá tener acceso a los recursos de la otra red puesto que los dos están combinadas. Para más información sobre cómo conectar redes virtuales clásicas y nuevas, vea [Conexión de redes virtuales clásicas a redes virtuales nuevas](../virtual-network/virtual-networks-arm-asm-s2s.md).

###DNS personalizado

Al crear una red virtual, Azure proporciona la funcionalidad de resolución de nombres predeterminada para los servicios de Azure instalados en la red, como HDInsight. Sin embargo, quizás tenga que utilizar su propio Sistema de nombres de dominio (DNS) en situaciones como la resolución de nombres de dominio de distintas redes, por ejemplo, cuando se establece comunicación entre servicios que se encuentran en dos redes virtuales unidas. HDInsight admite tanto la resolución de nombres predeterminada de Azure como un DNS personalizado cuando se utiliza con la red virtual de Azure.

Para más información sobre cómo utilizar su propio servidor DNS con la red virtual de Azure, consulte la sección __Resolución de nombres mediante su propio servidor DNS__ del documento [Resolución de nombres para las máquinas virtuales e instancias de rol](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server).

###Redes virtuales protegidas

El servicio HDInsight es un servicio administrado y requiere acceso a Internet durante el aprovisionamiento y mientras se está ejecutando. De esta forma, Azure puede supervisar el estado del clúster, iniciar la conmutación por error de los recursos del clúster, cambiar el número de nodos del clúster mediante operaciones de escalado y otras tareas de administración.

Si necesita instalar HDInsight en una red virtual protegida, debe permitir el acceso entrante en el puerto 443 para las siguientes direcciones IP, que permiten a Azure administrar el clúster de HDInsight.

* 168\.61.49.99
* 23\.99.5.239
* 168\.61.48.131
* 138\.91.141.162

Si permite el acceso de entrada a estas direcciones en el puerto 443, podrá instalar HDInsight correctamente en una red virtual protegida.

Los siguientes ejemplos demuestran cómo crear un nuevo grupo de seguridad de red que permita las direcciones necesarias y aplique el grupo de seguridad a una subred de la red virtual. Estos pasos dan por supuesto que ya ha creado una red virtual y una subred en la que desea instalar HDInsight.

__Uso de Azure PowerShell__

    $vnetName = "Replace with your virtual network name"
    $resourceGroupName = "Replace with the resource group the virtual network is in"
    $subnetName = "Replace with the name of the subnet that HDInsight will be installed into"
    # Get the Virtual Network object
    $vnet = Get-AzureRmVirtualNetwork `
        -Name $vnetName `
        -ResourceGroupName $resourceGroupName
    # Get the region the Virtual network is in.
    $location = $vnet.Location
    # Get the subnet object
    $subnet = $vnet.Subnets | Where-Object Name -eq $subnetName
    # Create a new Network Security Group.
    # And add exemptions for the HDInsight health and management services.
    $nsg = New-AzureRmNetworkSecurityGroup `
        -Name "hdisecure" `
        -ResourceGroupName $resourceGroupName `
        -Location $location `
        | Add-AzureRmNetworkSecurityRuleConfig `
            -name "hdirule1" `
            -Description "HDI health and management address 168.61.49.99" `
            -Protocol "*" `
            -SourcePortRange "*" `
            -DestinationPortRange "443" `
            -SourceAddressPrefix "168.61.49.99" `
            -DestinationAddressPrefix "VirtualNetwork" `
            -Access Allow `
            -Priority 300 `
            -Direction Inbound `
        | Add-AzureRmNetworkSecurityRuleConfig `
            -Name "hdirule2" `
            -Description "HDI health and management 23.99.5.239" `
            -Protocol "*" `
            -SourcePortRange "*" `
            -DestinationPortRange "443" `
            -SourceAddressPrefix "23.99.5.239" `
            -DestinationAddressPrefix "VirtualNetwork" `
            -Access Allow `
            -Priority 301 `
            -Direction Inbound `
        | Add-AzureRmNetworkSecurityRuleConfig `
            -Name "hdirule3" `
            -Description "HDI health and management 168.61.48.131" `
            -Protocol "*" `
            -SourcePortRange "*" `
            -DestinationPortRange "443" `
            -SourceAddressPrefix "168.61.48.131" `
            -DestinationAddressPrefix "VirtualNetwork" `
            -Access Allow `
            -Priority 302 `
            -Direction Inbound `
        | Add-AzureRmNetworkSecurityRuleConfig `
            -Name "hdirule4" `
            -Description "HDI health and management 138.91.141.162" `
            -Protocol "*" `
            -SourcePortRange "*" `
            -DestinationPortRange "443" `
            -SourceAddressPrefix "138.91.141.162" `
            -DestinationAddressPrefix "VirtualNetwork" `
            -Access Allow `
            -Priority 303 `
            -Direction Inbound
    # Set the changes to the security group
    Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg
    # Apply the NSG to the subnet
    Set-AzureRmVirtualNetworkSubnetConfig `
        -VirtualNetwork $vnet `
        -Name $subnetName `
        -AddressPrefix $subnet.AddressPrefix `
        -NetworkSecurityGroupId $nsg

__Uso de la CLI de Azure__

1. Use el siguiente comando para crear un nuevo grupo de seguridad de red denominado `hdisecure`. Reemplace __RESOURCEGROUPNAME__ y __LOCATION__ por el grupo de recursos que contiene la red virtual de Azure y la ubicación (región) en la que se creó el grupo.

        azure network nsg create RESOURCEGROUPNAME hdisecure LOCATION
    
    Cuando se haya creado el grupo, recibirá información sobre el nuevo grupo. Busque una línea similar a la siguiente y guarde la información de `/subscriptions/GUID/resourceGroups/RESOURCEGROUPNAME/providers/Microsoft.Network/networkSecurityGroups/hdisecure`. Se utilizará en un paso más adelante.
    
        data:    Id                              : /subscriptions/GUID/resourceGroups/RESOURCEGROUPNAME/providers/Microsoft.Network/networkSecurityGroups/hdisecure

2. Utilice lo siguiente para agregar reglas al nuevo grupo de seguridad de red que permitan la comunicación entrante en el puerto 443 desde el servicio de mantenimiento y administración de HDInsight de Azure. Reemplace __RESOURCEGROUPNAME__ por el nombre del grupo de recursos que contiene la red virtual de Azure.

        azure network nsg rule create RESOURCEGROUPNAME hdisecure hdirule1 -p "*" -o "*" -u "443" -f "168.61.49.99" -e "VirtualNetwork" -c "Allow" -y 300 -r "Inbound"
        azure network nsg rule create RESOURCEGROUPNAME hdisecure hdirule2 -p "*" -o "*" -u "443" -f "23.99.5.239" -e "VirtualNetwork" -c "Allow" -y 301 -r "Inbound"
        azure network nsg rule create RESOURCEGROUPNAME hdisecure hdirule3 -p "*" -o "*" -u "443" -f "168.61.48.131" -e "VirtualNetwork" -c "Allow" -y 302 -r "Inbound"
        azure network nsg rule create RESOURCEGROUPNAME hdisecure hdirule4 -p "*" -o "*" -u "443" -f "138.91.141.162" -e "VirtualNetwork" -c "Allow" -y 303 -r "Inbound"

3. Cuando se hayan creado las reglas, utilice lo siguiente para aplicar el nuevo grupo de seguridad de red a una subred. Reemplace __RESOURCEGROUPNAME__ por el nombre del grupo de recursos que contiene la red virtual de Azure. Reemplace __VNETNAME__ y __SUBNETNAME__ por el nombre de la red virtual de Azure y la subred que usará al instalar HDInsight.

        azure network vnet subnet set RESOURCEGROUPNAME VNETNAME SUBNETNAME -w "/subscriptions/GUID/resourceGroups/RESOURCEGROUPNAME/providers/Microsoft.Network/networkSecurityGroups/hdisecure"
    
    Cuando finalice este comando, puede instalar correctamente HDInsight en la red virtual protegida en la subred usada en estos pasos.

> [AZURE.IMPORTANT] Con los pasos anteriores solo se abre el acceso al servicio de administración y mantenimiento de HDInsight en la nube de Azure. Esto le permite instalar correctamente un clúster de HDInsight en la subred. Sin embargo, se bloquea de forma predeterminada el acceso al clúster de HDInsight desde fuera de la red virtual. Tendrá que agregar reglas adicionales del grupo de seguridad de red si desea permitir el acceso desde fuera de la red virtual.
>
> Por ejemplo, para permitir el acceso SSH desde Internet, deberá agregar una regla similar al siguiente:
>
> * Azure PowerShell: ```Add-AzureRmNetworkSecurityRuleConfig -Name "SSSH" -Description "SSH" -Protocol "*" -SourcePortRange "*" -DestinationPortRange "22" -SourceAddressPrefix "*" -DestinationAddressPrefix "VirtualNetwork" -Access Allow -Priority 304 -Direction Inbound```
> * CLI de Azure: ```azure network nsg rule create RESOURCEGROUPNAME hdisecure hdirule4 -p "*" -o "*" -u "22" -f "*" -e "VirtualNetwork" -c "Allow" -y 304 -r "Inbound"```

Para más información sobre los grupos de seguridad de red, consulte [¿Qué es un grupo de seguridad de red?](../virtual-network/virtual-networks-nsg.md) Para más información sobre el control del enrutamiento en una red virtual de Azure, consulte [¿Qué son las rutas definidas por el usuario y el reenvío IP?](../virtual-network/virtual-networks-udr-overview.md)

##<a id="tasks"></a>Tareas e información

En esta sección, encontrará información y tareas comunes que probablemente necesite al usar HDInsight con una red virtual.

###Determinación del nombre FQDN

El clúster de HDInsight tendrá asignado un nombre de dominio completo (FQDN) específico para la interfaz de la red virtual. Esta es la dirección que debe usar al conectarse al clúster desde otros recursos en la red virtual. Para determinar el nombre de dominio completo, use la siguiente dirección URL para consultar el servicio de administración de Ambari:

	https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/services/<servicename>/components/<componentname>

> [AZURE.NOTE] Para obtener más información sobre el uso de Ambari con HDInsight, consulte [Supervisión de clústeres de Hadoop en HDInsight con la API de Ambari](hdinsight-monitor-use-ambari-api.md).

Debe especificar el nombre del clúster y un servicio y componente en ejecución en el clúster, como el administrador de recursos de YARN.

> [AZURE.NOTE] Los datos devueltos son un documento de notación de objetos JavaScript (JSON) que contiene gran cantidad de información acerca del componente. Para extraer únicamente el nombre de dominio completo, debe usar un analizador JSON para recuperar el valor de `host_components[0].HostRoles.host_name`.

Por ejemplo, para devolver el nombre de dominio completo desde un clúster de HDInsight Hadoop, puede usar uno de los siguientes métodos a fin de recuperar los datos para el administrador de recursos de YARN:

* [Azure PowerShell](../powershell-install-configure.md)

		$ClusterDnsName = <clustername>
		$Username = <cluster admin username>
		$Password = <cluster admin password>
		$DnsSuffix = ".azurehdinsight.net"
		$ClusterFQDN = $ClusterDnsName + $DnsSuffix

		$webclient = new-object System.Net.WebClient
		$webclient.Credentials = new-object System.Net.NetworkCredential($Username, $Password)

		$Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/services/yarn/		components/resourcemanager"
		$Response = $webclient.DownloadString($Url)
		$JsonObject = $Response | ConvertFrom-Json
		$FQDN = $JsonObject.host_components[0].HostRoles.host_name
		Write-host $FQDN

* [cURL](http://curl.haxx.se/) y [jq](http://stedolan.github.io/jq/)

		curl -G -u <username>:<password> https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/services/yarn/components/resourcemanager | jq .host_components[0].HostRoles.host_name

###Conexión a HBase

Para conectarse remotamente a HBase mediante la API de Java, debe determinar las direcciones de cuórum de ZooKeeper para el clúster de HBase y especificar esto en su aplicación.

Para obtener la dirección de cuórum de ZooKeeper, use uno de los siguientes métodos para consultar el servicio de administración de Ambari:

* [Azure PowerShell](../powershell-install-configure.md)

		$ClusterDnsName = <clustername>
		$Username = <cluster admin username>
		$Password = <cluster admin password>
		$DnsSuffix = ".azurehdinsight.net"
		$ClusterFQDN = $ClusterDnsName + $DnsSuffix

		$webclient = new-object System.Net.WebClient
		$webclient.Credentials = new-object System.Net.NetworkCredential($Username, $Password)

		$Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.zookeeper.quorum"
        $Response = $webclient.DownloadString($Url)
        $JsonObject = $Response | ConvertFrom-Json
        Write-host $JsonObject.items[0].properties.'hbase.zookeeper.quorum'

* [cURL](http://curl.haxx.se/) y [jq](http://stedolan.github.io/jq/)

		curl -G -u <username>:<password> "https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.zookeeper.quorum" | jq .items[0].properties[]

> [AZURE.NOTE] Para obtener más información sobre el uso de Ambari con HDInsight, consulte [Supervisión de clústeres de Hadoop en HDInsight con la API de Ambari](hdinsight-monitor-use-ambari-api.md).

Una vez que tenga la información de cuórum, úsela en su aplicación cliente.

Por ejemplo, en una aplicación Java que usa la API de HBase, agregue un archivo **hbase-site.xml** al proyecto y especifique la información de cuórum en el archivo de la siguiente manera:

```
<configuration>
  <property>
    <name>hbase.cluster.distributed</name>
    <value>true</value>
  </property>
  <property>
    <name>hbase.zookeeper.quorum</name>
    <value>zookeeper0.address,zookeeper1.address,zookeeper2.address</value>
  </property>
  <property>
    <name>hbase.zookeeper.property.clientPort</name>
    <value>2181</value>
  </property>
</configuration>
```

###Comprobación de la conectividad de la red

Algunos servicios, como SQL Server, pueden limitar las conexiones de red entrantes. Esto impedirá que HDInsight trabaje correctamente con estos servicios.

Si tiene problemas para obtener acceso a un servicio desde HDInsight, consulte la documentación del servicio para asegurarse de que ha habilitado el acceso a la red. También puede comprobar el acceso a la red creando una máquina virtual de Azure en la misma red virtual. Use utilidades cliente para comprobar que la máquina virtual puede conectarse al servicio a través de la red virtual.

##<a id="nextsteps"></a>Pasos siguientes

Los siguientes ejemplos demuestran cómo usar HDInsight con Red virtual de Azure:

* [Análisis de los datos de sensor con Storm y HBase en HDInsight](hdinsight-storm-sensor-data-analysis.md): describe cómo configurar un clúster de Storm y HBase en una red virtual, así como escribir datos de manera remota en HBase desde Storm.

* [Aprovisionamiento de clústeres de Hadoop en HDInsight](hdinsight-hadoop-provision-linux-clusters.md): proporciona información sobre el aprovisionamiento de clústeres de Hadoop, incluida información sobre cómo usar Red virtual de Azure.

* [Uso de Sqoop con Hadoop en HDInsight](hdinsight-use-sqoop-mac-linux.md): proporciona información sobre el uso de Sqoop para transferir datos con SQL Server a través de una red virtual.

Para obtener más información acerca de Redes virtuales de Azure, consulte la [Información general sobre Red virtual de Azure](../virtual-network/virtual-networks-overview.md).

<!---HONumber=AcomDC_0622_2016-->