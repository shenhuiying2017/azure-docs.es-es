---
title: "Extensión de HDInsight con Virtual Network | Microsoft Docs"
description: Aprenda a usar la Red virtual de Azure para conectar HDInsight con otros recursos en la nube o recursos en su centro de datos.
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 37b9b600-d7f8-4cb1-a04a-0b3a827c6dcc
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/08/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 5ea7095e12b6194556d3cd0baa43ccfed1e087ee
ms.openlocfilehash: b28eec9a01c45468e0cc323514d9c2e91ec88bf5
ms.lasthandoff: 02/27/2017


---
# <a name="extend-hdinsight-capabilities-by-using-azure-virtual-network"></a>Extensión de las funcionalidades de HDInsight con Red virtual de Azure
Virtual Network de Azure permite extender sus soluciones de Hadoop para incorporar recursos locales como SQL Server, combinar varios tipos de clúster de HDInsight o crear redes privadas seguras entre los recursos existentes en la nube.

## <a name="prerequisites"></a>Requisitos previos

* CLI de Azure CLI 2.0: para obtener más información, consulte [Instalación y configuración de Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-az-cli2).

* Azure PowerShell: para obtener información sobre Azure PowerShell, consulte [Instalación y configuración de Azure PowerShell](/powershell/azureps-cmdlets-docs).

> [!NOTE]
> Los pasos que aparecen en este documento se han probado con la versión más reciente de Azure PowerShell y la CLI de Azure. Si está utilizando una versión anterior, los comandos pueden variar. Para obtener mejores resultados, use los vínculos anteriores para instalar las versiones más recientes.

## <a id="whatis"></a>¿Qué es Red virtual de Azure?

[Red virtual de Azure](https://azure.microsoft.com/documentation/services/virtual-network/) permite crear una red segura y persistente que contenga los recursos necesarios para una solución. Una red virtual permite hacer lo siguiente:

* Conectar recursos en la nube en una red privada (solo en la nube)
  
    ![Diagrama de la configuración solo en la nube](media/hdinsight-extend-hadoop-virtual-network/cloud-only.png)
  
    El uso de Red virtual para vincular servicios de Azure con HDInsight de Azure permite los siguientes escenarios:
  
    * **Invocación de servicios o trabajos de HDInsight** desde servicios o sitios web de Azure que se ejecutan en máquinas virtuales de Azure.
    * **Transferencia directa de datos** entre HDInsight y SQL Server o SQL Database de Azure u otra solución de almacenamiento de datos en ejecución en una máquina virtual.
    * **Combinación de varios servidores de HDInsight** en una sola solución. Los clústeres de HDInsight incluyen diversos tipos, que corresponden a la carga de trabajo o la tecnología para los que el clúster está optimizado. No hay ningún método admitido para crear un solo clúster que combine varios tipos, como Storm y HBase. Al usar una red virtual, varios clústeres podrán comunicarse directamente entre sí.

* Conecte sus recursos en la nube a la red de su centro de datos local (sitio a sitio o punto a sitio) usando una red privada virtual (VPN).
  
    La configuración sitio a sitio permite conectar varios recursos de su centro de datos a la Red virtual de Azure usando una VPN de hardware o el servicio de enrutamiento y acceso remoto.
  
    ![Diagrama de la configuración de sitio a sitio](media/hdinsight-extend-hadoop-virtual-network/site-to-site.png)
  
    La configuración punto a sitio permite conectar un recurso específico a la Red virtual de Azure usando una VPN de software.
  
    ![Diagrama de la configuración de punto a sitio](media/hdinsight-extend-hadoop-virtual-network/point-to-site.png)
  
    El uso de Red virtual para vincular la nube y su centro de datos posibilita escenarios similares al de la configuración solo en la nube. Pero, en lugar de limitarse a trabajar con recursos en la nube, también puede trabajar con recursos en su centro de datos.
  
    * **Transferencia directa de datos** entre HDInsight y el centro de datos. Un ejemplo es el uso de Sqoop para transferir datos desde o hacia SQL Server o leer datos generados por una aplicación de línea de negocio (LoB).
    * **Invocación de servicios o trabajos de HDInsight** desde una aplicación de línea de negocio. Un ejemplo es usar las API de Java de HBase para almacenar y recuperar datos desde un clúster de HDInsight HBase.

Para obtener más información sobre las características, las ventajas y la funcionalidad de Red virtual, consulte [Información general sobre Red virtual de Azure](../virtual-network/virtual-networks-overview.md).

> [!NOTE]
> Debe crear la red virtual de Azure antes de aprovisionar un clúster de HDInsight. Para obtener más información, consulte [Tareas de configuración de Red virtual](https://azure.microsoft.com/documentation/services/virtual-network/).

## <a name="virtual-network-requirements"></a>Requisitos de red virtual

> [!IMPORTANT]
> La creación de un clúster de HDInsight en una red virtual requiere configuraciones de red virtual específicas, que se describen en esta sección.

### <a name="location-based-virtual-networks"></a>Redes virtuales basadas en la ubicación

HDInsight de Azure solo admite redes virtuales basadas en la ubicación. Actualmente, no funciona con redes virtuales basadas en grupos de afinidad.

### <a name="classic-or-v2-virtual-network"></a>Red virtual clásica o v2

Los clústeres basados en Windows requieren una red virtual clásica mientras que los basados en Linux requieren una red virtual de Azure Resource Manager. Si no dispone del tipo correcto de red, no se podrá usar cuando cree el clúster.

Si dispone de recursos en una red virtual que no se puede usar por el clúster que planea crear, puede crear una nueva red virtual que se pueda usar por el clúster y conectarla a la red virtual incompatible. Luego puede crear el clúster en la versión de la red que requiere y podrá tener acceso a los recursos de la otra red puesto que los dos están combinadas. Para más información sobre cómo conectar redes virtuales clásicas y nuevas, vea [Conexión de redes virtuales clásicas a redes virtuales nuevas](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md).

### <a name="custom-dns"></a>DNS personalizado

Al crear una red virtual, Azure proporciona la funcionalidad de resolución de nombres predeterminada para los servicios de Azure instalados en la red, como HDInsight. Sin embargo, quizás tenga que utilizar su propio Sistema de nombres de dominio (DNS) en situaciones como la resolución de nombres de dominio de distintas redes, por ejemplo, cuando se establece comunicación entre servicios que se encuentran en dos redes virtuales unidas. HDInsight admite tanto la resolución de nombres predeterminada de Azure como un DNS personalizado cuando se utiliza con la red virtual de Azure.

Para más información sobre cómo utilizar su propio servidor DNS con la red virtual de Azure, consulte la sección **Resolución de nombres mediante su propio servidor DNS** del documento [Resolución de nombres para las máquinas virtuales e instancias de rol](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server) .

### <a name="secured-virtual-networks"></a>Redes virtuales protegidas

El servicio HDInsight es un servicio administrado y requiere acceso a Internet durante el aprovisionamiento y mientras se está ejecutando. De esta forma, Azure puede supervisar el estado del clúster, iniciar la conmutación por error de los recursos del clúster, cambiar el número de nodos del clúster mediante operaciones de escalado y otras tareas de administración.

Si necesita instalar HDInsight en una red virtual protegida, debe permitir el acceso entrante en el puerto 443 para las siguientes direcciones IP, que permiten a Azure administrar el clúster de HDInsight.

> [!IMPORTANT]
> Las direcciones IP que deben permitirse son específicas de la región en la que residen el clúster de HDInsight y la red Virtual. Use lo siguiente para encontrar la dirección IP correspondiente a su región.

Región __Sur de Brasil__:

* 191.235.84.104
* 191.235.87.113

Región __Este de Canadá__:

* 52.229.127.96
* 52.229.123.172

Región __Centro de Canadá__:

* 52.228.37.66
* 52.228.45.222

Región __Centro occidental de EE.UU.__:

* 52.161.23.15
* 52.161.10.167

Región __Oeste de EE. UU. 2__:

* 52.175.211.210
* 52.175.222.222

__Todas las demás regiones__:

* 168.61.49.99
* 23.99.5.239
* 168.61.48.131
* 138.91.141.162

Si permite el acceso de entrada a estas direcciones en el puerto 443, podrá instalar HDInsight correctamente en una red virtual protegida.

> [!IMPORTANT]
> HDInsight no puede restringir el tráfico saliente, solo el entrante. Al definir reglas de grupo de seguridad de red de la subred que contiene HDInsight, utilice solamente reglas de entrada.

Los siguientes ejemplos demuestran cómo crear un nuevo grupo de seguridad de red que permita las direcciones necesarias y aplique el grupo de seguridad a una subred de la red virtual. Las direcciones usadas en este ejemplo son de la lista __Todas las demás regiones__ indicada anteriormente. Si se encuentra en una de las regiones indicadas específicamente, como __Centro occidental de EE.UU.__, modifique el script para que use las direcciones IP de su región.

Estos pasos dan por supuesto que ya ha creado una red virtual y una subred en la que desea instalar HDInsight. Consulte [Creación de una red virtual mediante Azure Portal](../virtual-network/virtual-networks-create-vnet-arm-pportal.md).

> [!IMPORTANT]
> Anote el valor de `priority` usado en estos ejemplos; las reglas se prueban en el tráfico de red por orden de prioridad. Cuando una regla coincide con los criterios de prueba y se aplica, no se prueba ninguna otra regla.
> 
> Si tiene reglas personalizadas que bloquean el tráfico entrante (como una regla **Denegar todo**), puede que necesite ajustar los valores de prioridad de estos ejemplos o personalizar las reglas de los ejemplos se produzcan antes de las reglas que bloquean el acceso. De lo contrario, la regla **Denegar todo** se probará en primer lugar y nunca se aplicarán las reglas de este ejemplo. También debe tener cuidado de no bloquear las reglas predeterminadas de una red de Azure Virtual Network. Por ejemplo, no debería crear una regla **Denegar todo** que se aplique antes que la regla predeterminada **PERMITIR RED VIRTUAL ENTRANTE** (que tiene una prioridad de 65000).
> 
> Para más información sobre cómo aplicar las reglas y cuáles son las reglas predeterminadas de entrada y salida, consulte [¿Qué es un grupo de seguridad de red?](../virtual-network/virtual-networks-nsg.md).

**Uso de Azure PowerShell**

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
        -NetworkSecurityGroup $nsg

**Uso de la CLI de Azure**

1. Use el siguiente comando para crear un nuevo grupo de seguridad de red denominado " `hdisecure`". Reemplace **RESOURCEGROUPNAME** y **LOCATION** por el grupo de recursos que contiene la red virtual de Azure y la ubicación (región) en la que se creó el grupo.
   
        az network nsg create -g RESOURCEGROUPNAME -n hdisecure -l LOCATION

    Cuando se haya creado el grupo, recibirá información sobre el nuevo grupo.

2. Utilice lo siguiente para agregar reglas al nuevo grupo de seguridad de red que permitan la comunicación entrante en el puerto 443 desde el servicio de mantenimiento y administración de HDInsight de Azure. Reemplace **RESOURCEGROUPNAME** por el nombre del grupo de recursos que contiene la red virtual de Azure.
    
        az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule1 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "168.61.49.99/24" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 300 --direction "Inbound"
        az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule2 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "23.99.5.239/24" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 301 --direction "Inbound"
        az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule3 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "168.61.48.131/24" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 302 --direction "Inbound"
        az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule4 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "138.91.141.162/24" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 303 --direction "Inbound"

3. Una vez que se han creado las reglas, utilice lo siguiente para recuperar el identificador único para este grupo de seguridad de red:

        az network nsg show -g RESOURCEGROUPNAME -n hdisecure --query 'id'

    Se devuelve un valor similar al siguiente texto:

        "/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUPNAME/providers/Microsoft.Network/networkSecurityGroups/hdisecure"

    Utilice comillas dobles alrededor del identificador en el comando si no obtiene los resultados esperados.

4. Use el siguiente comando para aplicar el grupo de seguridad de red a una subred. Reemplace los valores de __GUID__ y __RESOURCEGROUPNAME__ por los que se devolvieron en el paso anterior. Reemplace __VNETNAME__ y __SUBNETNAME__ por el nombre de red virtual y el de subred que va a utilizar al crear un clúster de HDInsight.
   
        az network vnet subnet update -g RESOURCEGROUPNAME --vnet-name VNETNAME --name SUBNETNAME --set networkSecurityGroup.id="/subscriptions/GUID/resourceGroups/RESOURCEGROUPNAME/providers/Microsoft.Network/networkSecurityGroups/hdisecure"
   
    Cuando finalice este comando, puede instalar correctamente HDInsight en la red virtual protegida en la subred usada en estos pasos.

> [!IMPORTANT]
> Con los pasos anteriores solo se abre el acceso al servicio de administración y mantenimiento de HDInsight en la nube de Azure. Esto le permite instalar correctamente un clúster de HDInsight en la subred. Sin embargo, se bloquea de forma predeterminada el acceso al clúster de HDInsight desde fuera de la red virtual. Tendrá que agregar reglas adicionales del grupo de seguridad de red si desea permitir el acceso desde fuera de la red virtual.
> 
> Por ejemplo, para permitir el acceso SSH desde Internet, deberá agregar una regla similar al siguiente: 
> 
> * Azure PowerShell: ```Add-AzureRmNetworkSecurityRuleConfig -Name "SSSH" -Description "SSH" -Protocol "*" -SourcePortRange "*" -DestinationPortRange "22" -SourceAddressPrefix "*" -DestinationAddressPrefix "VirtualNetwork" -Access Allow -Priority 304 -Direction Inbound```
> * CLI de Azure: ```az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule5 --protocol "*" --source-port-range "*" --destination-port-range "22" --source-address-prefix "*" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 304 --direction "Inbound"```

Para obtener más información sobre los grupos de seguridad de red, consulte [¿Qué es un grupo de seguridad de red?](../virtual-network/virtual-networks-nsg.md) Para obtener más información sobre el control del enrutamiento en una red virtual de Azure, consulte [¿Qué son las rutas definidas por el usuario y el reenvío IP?](../virtual-network/virtual-networks-udr-overview.md)

## <a id="tasks"></a>Tareas e información

En esta sección, encontrará información y tareas comunes que probablemente necesite al usar HDInsight con una red virtual.

### <a name="determine-the-fqdn"></a>Determinación del nombre FQDN

El clúster de HDInsight tendrá asignado un nombre de dominio completo (FQDN) específico para la interfaz de la red virtual. Esta es la dirección que debe usar al conectarse al clúster desde otros recursos en la red virtual. Para determinar el nombre de dominio completo, use la siguiente dirección URL para consultar el servicio de administración de Ambari:

    https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/services/<servicename>/components/<componentname>

> [!NOTE]
> Para obtener más información sobre el uso de Ambari con HDInsight, consulte [Supervisión de clústeres de Hadoop en HDInsight con la API de Ambari](hdinsight-monitor-use-ambari-api.md).

Debe especificar el nombre del clúster y un servicio y componente en ejecución en el clúster, como el administrador de recursos de YARN.

> [!NOTE]
> Los datos devueltos son un documento de notación de objetos JavaScript (JSON) que contiene gran cantidad de información acerca del componente. Para extraer únicamente el nombre de dominio completo, debe usar un analizador JSON para recuperar el valor de `host_components[0].HostRoles.host_name` .

Por ejemplo, para devolver el nombre de dominio completo desde un clúster de HDInsight Hadoop, puede usar uno de los siguientes métodos a fin de recuperar los datos para el administrador de recursos de YARN:

* [Azure PowerShell](/powershell/azureps-cmdlets-docs)
  
        $ClusterDnsName = <clustername>
        $Username = <cluster admin username>
        $Password = <cluster admin password>
        $DnsSuffix = ".azurehdinsight.net"
        $ClusterFQDN = $ClusterDnsName + $DnsSuffix
  
        $webclient = new-object System.Net.WebClient
        $webclient.Credentials = new-object System.Net.NetworkCredential($Username, $Password)
  
        $Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/services/yarn/        components/resourcemanager"
        $Response = $webclient.DownloadString($Url)
        $JsonObject = $Response | ConvertFrom-Json
        $FQDN = $JsonObject.host_components[0].HostRoles.host_name
        Write-host $FQDN

* [cURL](http://curl.haxx.se/) y [jq](http://stedolan.github.io/jq/)
  
        curl -G -u <username>:<password> https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/services/yarn/components/resourcemanager | jq .host_components[0].HostRoles.host_name

### <a name="connecting-to-hbase"></a>Conexión a HBase

Para conectarse remotamente a HBase mediante la API de Java, debe determinar las direcciones de cuórum de ZooKeeper para el clúster de HBase y especificar esto en su aplicación.

Para obtener la dirección de cuórum de ZooKeeper, use uno de los siguientes métodos para consultar el servicio de administración de Ambari:

* [Azure PowerShell](/powershell/azureps-cmdlets-docs)
  
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

> [!NOTE]
> Para obtener más información sobre el uso de Ambari con HDInsight, consulte [Supervisión de clústeres de Hadoop en HDInsight con la API de Ambari](hdinsight-monitor-use-ambari-api.md).

Una vez que tenga la información de cuórum, úsela en su aplicación cliente.

Por ejemplo, en una aplicación Java que usa la API de HBase, agregue un archivo **hbase-site.xml** al proyecto y especifique la información de cuórum en el archivo de la siguiente manera:

```xml
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

### <a name="verify-network-connectivity"></a>Comprobación de la conectividad de la red

Algunos servicios, como SQL Server, pueden limitar las conexiones de red entrantes. Esto impedirá que HDInsight trabaje correctamente con estos servicios.

Si tiene problemas para obtener acceso a un servicio desde HDInsight, consulte la documentación del servicio para asegurarse de que ha habilitado el acceso a la red. También puede comprobar el acceso a la red creando una máquina virtual de Azure en la misma red virtual. Use utilidades cliente para comprobar que la máquina virtual puede conectarse al servicio a través de la red virtual.

## <a id="nextsteps"></a>Pasos siguientes

Los siguientes ejemplos demuestran cómo usar HDInsight con Red virtual de Azure:

* [Análisis de los datos de sensor con Storm y HBase en HDInsight](hdinsight-storm-sensor-data-analysis.md) : describe cómo configurar un clúster de Storm y HBase en una red virtual, así como escribir datos de manera remota en HBase desde Storm.
* [Aprovisionamiento de clústeres de Hadoop en HDInsight](hdinsight-hadoop-provision-linux-clusters.md) : proporciona información sobre el aprovisionamiento de clústeres de Hadoop, incluida información sobre cómo usar Red virtual de Azure.
* [Uso de Sqoop con Hadoop en HDInsight](hdinsight-use-sqoop-mac-linux.md) : proporciona información sobre el uso de Sqoop para transferir datos con SQL Server a través de una red virtual.

Para obtener más información acerca de Redes virtuales de Azure, consulte la [Información general sobre Red virtual de Azure](../virtual-network/virtual-networks-overview.md).


