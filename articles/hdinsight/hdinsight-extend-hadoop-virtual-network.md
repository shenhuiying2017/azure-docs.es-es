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
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/20/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: abdbb9a43f6f01303844677d900d11d984150df0
ms.openlocfilehash: 52038c02901d49f787226ce629a1ca5942414e4a
ms.lasthandoff: 04/21/2017


---
# <a name="extend-hdinsight-capabilities-by-using-azure-virtual-network"></a>Extensión de las funcionalidades de HDInsight con Red virtual de Azure

Descubra cómo usar las redes virtuales de Azure Virtual Network con HDInsight para habilitar los siguientes escenarios:

* Restringir el acceso a HDInsight. Por ejemplo, evitar el tráfico entrante procedente de Internet.

* Acceder directamente a servicios en HDInsight que no están expuestos en Internet. Por ejemplo, trabajar de forma directa con agentes de Kafka o utilizar la API de Java de HBase.

* Conectar servicios directamente a HDInsight. Por ejemplo, emplear Oozie para importar datos de SQL Server, o exportarlos a dicho servicio, dentro del centro de datos.

* Crear soluciones que impliquen varios clústeres de HDInsight. Por ejemplo, usar Spark o Storm para analizar datos almacenados en Kafka.

## <a name="prerequisites"></a>Requisitos previos

* CLI de Azure 2.0: para obtener más información, vea [Install and Configure Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-az-cli2) (Instalar y configurar CLI de Azure 2.0).

* Azure PowerShell: para obtener más información, vea [Install and Configure Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) (Instalar y configurar Azure PowerShell).

> [!NOTE]
> Los pasos que aparecen en este documento se han probado con la versión más reciente de Azure PowerShell y la CLI de Azure. Si está utilizando una versión anterior, los comandos pueden variar. Para obtener mejores resultados, use los vínculos anteriores para instalar las versiones más recientes.

## <a id="whatis"></a>Conceptos básicos sobre Azure Virtual Network

[Red virtual de Azure](https://azure.microsoft.com/documentation/services/virtual-network/) permite crear una red segura y persistente que contenga los recursos necesarios para una solución.

A continuación, se expone una lista de aspectos que deben tenerse en cuenta a la hora de utilizar HDInsight en una red virtual:

* __Redes virtuales clásicas y de Resource Manager__: sírvase de la siguiente tabla para determinar el tipo de red que tendría que usar en función del sistema operativo del clúster de HDInsight:

    | Sistema operativo de HDInsight | Red virtual clásica | Red virtual de Resource Manager |
    | ---- | ---- | ---- |
    | Linux | no | yes |
    | Windows | yes | no |

    Para acceder a recursos ubicados en una red virtual incompatible, combine las dos redes. Para obtener más información sobre cómo conectar redes virtuales clásicas y de Resource Manager, vea [Conexión de redes virtuales a partir de diferentes modelos de implementación con el portal](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md).

* __DNS personalizado__: Azure proporciona resolución de nombres para los servicios de Azure instalados en una red virtual de Azure Virtual Network. Esta resolución de nombres se limita únicamente a la red virtual. Si desea habilitar la resolución de nombres para recursos que se encuentren fuera de dicha red, debe emplear un servidor DNS personalizado. Para obtener más información sobre el uso de un servidor DNS personalizado, vea el documento [Resolución de nombres para las máquinas virtuales e instancias de rol](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server).

* __Tunelización forzada__: HDInsight no admite la configuración de tunelización forzada de Azure Virtual Network.

* __Restricción del tráfico de red__: HDInsight no admite el uso de grupos de seguridad de red para limitar el tráfico de red, pero precisa de acceso sin restricciones a varias direcciones IP de Azure. Para obtener más información, consulte la sección [Redes virtuales protegidas](#secured-virtual-networks).

### <a name="connect-cloud-resources-together-in-a-private-network-cloud-only"></a>Conexión de recursos de nube entre sí en una red privada (solo en la nube)

![Diagrama de la configuración solo en la nube](media/hdinsight-extend-hadoop-virtual-network/cloud-only.png)

El uso de Red virtual para vincular servicios de Azure con HDInsight de Azure permite los siguientes escenarios:

* **Invocación de servicios o trabajos de HDInsight** desde servicios o sitios web de Azure que se ejecutan en máquinas virtuales de Azure.

* **Transferencia directa de datos** entre HDInsight y SQL Server o SQL Database de Azure u otra solución de almacenamiento de datos en ejecución en una máquina virtual.

* **Combinación de varios servidores de HDInsight** en una sola solución. Hay varios tipos de clústeres de HDInsight, que corresponden a la carga de trabajo o la tecnología para los que el clúster está optimizado. No hay ningún método admitido para crear un solo clúster que combine varios tipos, como Storm y HBase. Al usar una red virtual, varios clústeres podrán comunicarse directamente entre sí.

### <a name="connect-cloud-resources-to-a-local-datacenter-network"></a>Conexión de recursos de nube a una red de centro de datos local

La configuración de sitio a sitio le permite conectar varios recursos de su centro de datos a Azure Virtual Network. La conexión puede realizarse con un dispositivo VPN de hardware o con el servicio Enrutamiento y acceso remoto.

![Diagrama de la configuración de sitio a sitio](media/hdinsight-extend-hadoop-virtual-network/site-to-site.png)

La configuración punto a sitio permite conectar un recurso específico a la Red virtual de Azure usando una VPN de software.

![Diagrama de la configuración de punto a sitio](media/hdinsight-extend-hadoop-virtual-network/point-to-site.png)

El uso de Red virtual para vincular la nube y su centro de datos posibilita escenarios similares al de la configuración solo en la nube. Pero, en lugar de limitarse a trabajar con recursos en la nube, también puede trabajar con recursos en su centro de datos.

* **Transferencia directa de datos** entre HDInsight y el centro de datos. Un ejemplo es el uso de Sqoop para transferir datos desde o hacia SQL Server o leer datos generados por una aplicación de línea de negocio (LoB).

* **Invocación de servicios o trabajos de HDInsight** desde una aplicación de línea de negocio. Un ejemplo es usar las API de Java de HBase para almacenar y recuperar datos desde un clúster de HDInsight HBase.

Para obtener más información sobre las características, las ventajas y la funcionalidad de Red virtual, consulte [Información general sobre Red virtual de Azure](../virtual-network/virtual-networks-overview.md).

> [!NOTE]
> Cree la red de Azure Virtual Network antes de aprovisionar un clúster de HDInsight, después especifique la red al crear el clúster. Para obtener más información, consulte [Tareas de configuración de Red virtual](https://azure.microsoft.com/documentation/services/virtual-network/).

## <a name="secured-virtual-networks"></a>Redes virtuales protegidas

El servicio HDInsight es un servicio administrado y requiere de acceso a los servicios de administración de Azure durante el aprovisionamiento y su ejecución. Dentro de los servicios de administración de Azure, se engloban los siguientes:

* Supervisar el mantenimiento del clúster
* Iniciar la conmutación por error de recursos de clúster
* Cambiar el número de nodos en el clúster mediante operaciones de escala
* Otras tareas de administración

> [!NOTE]
> Estas operaciones no necesitan acceso total a Internet. Cuando se restrinja el acceso a Internet, permita el acceso de entrada en el puerto 443 para las siguientes direcciones IP. Esto permite que Azure administre HDInsight:

Las direcciones IP que deben permitirse son específicas de la región en la que residen el clúster de HDInsight y la red Virtual. Busque las direcciones IP correspondientes a su región en la siguiente tabla.

| País | Region | Direcciones IP permitidas | Puerto permitido |
| ---- | ---- | ---- | ---- |
| Brasil | Sur de Brasil | 191.235.84.104</br>191.235.87.113 | 443 |
| Canadá | Este de Canadá | 52.229.127.96</br>52.229.123.172 | 443 |
| &nbsp; | Centro de Canadá | 52.228.37.66</br>52.228.45.222 | 443 |
| Alemania | Centro de Alemania | 51.4.146.68</br>51.4.146.80 | 443 |
| &nbsp; | Noreste de Alemania | 51.5.150.132</br>51.5.144.101 | 443 |
| India | India Central | 52.172.153.209</br>52.172.152.49 | 443 |
| Reino Unido | Oeste de Reino Unido | 51.141.13.110</br>51.141.7.20 | 443 |
| &nbsp; | Sur del Reino Unido 2 | 51.140.47.39</br>51.140.52.16 | 443 |
| Estados Unidos | Centro occidental de EE.UU. | 52.161.23.15</br>52.161.10.167 | 443 |
| &nbsp; | Oeste de EE. UU. 2 | 52.175.211.210</br>52.175.222.222 | 443 |

__Si su región no aparece en la tabla__, permita el tráfico en el puerto __443__ en las siguientes direcciones IP:

* 168.61.49.99
* 23.99.5.239
* 168.61.48.131
* 138.91.141.162

> [!IMPORTANT]
> HDInsight no puede restringir el tráfico saliente, solo el entrante. Cuando defina reglas del grupo de seguridad de red para la subred que contiene HDInsight, __utilice solamente reglas de entrada__.

### <a name="working-with-hdinsight-in-secured-virtual-networks"></a>Uso de HDInsight en redes virtuales protegidas

Si bloquea el acceso a Internet, no podrá utilizar los servicios de HDInsight que suelen exponerse por medio de la puerta de enlace pública de un clúster. Esto incluye Ambari y SSH. En su lugar, debe acceder a los servicios mediante la dirección IP interna de los nodos principales del clúster.

Para encontrar la dirección IP interna de los nodos principales, utilice los scripts que aparecen en la sección relativa a las [IP internas y los FQDN](#internal-ips-and-fqdns).

### <a name="example-secured-virtual-network"></a>Ejemplo: red virtual protegida

En los ejemplos siguientes, se muestra cómo crear un grupo de seguridad de red que permita el tráfico entrante en el puerto 443 desde las siguientes direcciones IP:

* 168.61.49.99
* 23.99.5.239
* 168.61.48.131
* 138.91.141.162

> [!IMPORTANT]
> Estas direcciones son para aquellas regiones que no dispongan de las direcciones IP específicas anteriormente enumeradas. Para encontrar las direcciones IP de su región, consulte la información que figura en la sección [Redes virtuales protegidas](#secured-virtual-networks).

Estos pasos dan por supuesto que ya ha creado una red virtual y una subred en la que desea instalar HDInsight. Consulte [Creación de una red virtual mediante Azure Portal](../virtual-network/virtual-networks-create-vnet-arm-pportal.md).

> [!WARNING]
> Las reglas se prueban en el tráfico de red por orden de __prioridad__. Cuando una regla cumple los criterios de prueba, se aplica y no se prueba ninguna otra para esa solicitud. Si una regla bloquea en gran medida el tráfico entrante (como la regla para **denegar todo**), __debe__ ir después de las reglas que permiten el tráfico.
>
> Para obtener más información sobre las reglas del grupo de seguridad de red, consulte el documento en el que se explica lo que es un [grupo de seguridad de red](../virtual-network/virtual-networks-nsg.md).

**Ejemplo: plantilla de Azure Resource Manager**

Usando la siguiente plantilla de administración de recursos de Azure de las [plantillas de inicio rápido de Azure](https://azure.microsoft.com/resources/templates/) para crear un clúster de HDInsight en una red virtual con las configuraciones de red segura:

[Implementación de una red virtual de Azure segura y un clúster de Hadoop de HDInsight en la red virtual](https://azure.microsoft.com/resources/templates/101-hdinsight-secure-vnet/)

**Ejemplo: Azure PowerShell**

```powershell
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
# Create a Network Security Group.
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
```

**Ejemplo: CLI de Azure**

1. Use el siguiente comando para crear un nuevo grupo de seguridad de red denominado " `hdisecure`". Reemplace **RESOURCEGROUPNAME** por el grupo de recursos que contiene la red de Azure Virtual Network. Reemplace **LOCATION** por la ubicación (región) en la que se haya creado el grupo.

    ```azurecli
    az network nsg create -g RESOURCEGROUPNAME -n hdisecure -l LOCATION
    ```

    Cuando se haya creado el grupo, recibirá información sobre el nuevo grupo.

2. Utilice lo siguiente para agregar reglas al nuevo grupo de seguridad de red que permitan la comunicación entrante en el puerto 443 desde el servicio de mantenimiento y administración de HDInsight de Azure. Reemplace **RESOURCEGROUPNAME** por el nombre del grupo de recursos que contiene la red virtual de Azure.

    ```azurecli
    az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule1 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "168.61.49.99/24" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 300 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule2 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "23.99.5.239/24" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 301 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule3 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "168.61.48.131/24" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 302 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule4 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "138.91.141.162/24" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 303 --direction "Inbound"
    ```

3. Una vez que se han creado las reglas, utilice lo siguiente para recuperar el identificador único para este grupo de seguridad de red:

    ```azurecli
    az network nsg show -g RESOURCEGROUPNAME -n hdisecure --query 'id'
    ```

    Este comando devuelve un valor similar al siguiente texto:

        "/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUPNAME/providers/Microsoft.Network/networkSecurityGroups/hdisecure"

    Utilice comillas dobles alrededor del identificador en el comando si no obtiene los resultados esperados.

4. Use el siguiente comando para aplicar el grupo de seguridad de red a una subred. Reemplace los valores de __GUID__ y __RESOURCEGROUPNAME__ por los que se devolvieron en el paso anterior. Reemplace __VNETNAME__ y __SUBNETNAME__ por el nombre de red virtual y el de subred que va a utilizar al crear un clúster de HDInsight.

    ```azurecli
    az network vnet subnet update -g RESOURCEGROUPNAME --vnet-name VNETNAME --name SUBNETNAME --set networkSecurityGroup.id="/subscriptions/GUID/resourceGroups/RESOURCEGROUPNAME/providers/Microsoft.Network/networkSecurityGroups/hdisecure"
    ```

    Cuando finalice este comando, puede instalar correctamente HDInsight en la red virtual protegida en la subred usada en estos pasos.

> [!IMPORTANT]
> Con los pasos anteriores solo se abre el acceso al servicio de administración y mantenimiento de HDInsight en la nube de Azure. Cualquier otro acceso al clúster de HDInsight desde fuera de Virtual Network está bloqueado. Para permitir el acceso desde fuera de la red virtual, debe agregar reglas adicionales del grupo de seguridad de red.
>
> En el siguiente ejemplo se muestra cómo habilitar el acceso de SSH desde Internet:
>
> ```powershell
> Add-AzureRmNetworkSecurityRuleConfig -Name "SSH" -Description "SSH" -Protocol "*" -SourcePortRange "*" -DestinationPortRange "22" -SourceAddressPrefix "*" -DestinationAddressPrefix "VirtualNetwork" -Access Allow -Priority 304 -Direction Inbound
> ```
>
> ```azurecli
> az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule5 --protocol "*" --source-port-range "*" --destination-port-range "22" --source-address-prefix "*" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 304 --direction "Inbound"
> ```

Para obtener más información sobre los grupos de seguridad de red, consulte [¿Qué es un grupo de seguridad de red?](../virtual-network/virtual-networks-nsg.md) Para obtener más información sobre el control del enrutamiento en una red de Azure Virtual Network, vea [Rutas definidas por el usuario y reenvío IP](../virtual-network/virtual-networks-udr-overview.md).

## <a name="retrieve-internal-ips-and-fqdns"></a>Recuperación de IP internas y FQDN

Al establecer una conexión con HDInsight mediante una red virtual, puede conectarse directamente a los nodos del clúster. Utilice los siguientes scripts para determinar la dirección IP interna y los nombres de dominio completos (FQDN) de los nodos del clúster:

**Azure PowerShell**

```powershell
$resourceGroupName = Read-Input -Prompt "Enter the resource group that contains the virtual network used with HDInsight"

$clusterNICs = Get-AzureRmNetworkInterface -ResourceGroupName $resourceGroupName | where-object {$_.Name -like "*node*"}

$nodes = @()
foreach($nic in $clusterNICs) {
    $node = new-object System.Object
    $node | add-member -MemberType NoteProperty -name "Type" -value $nic.Name.Split('-')[1]
    $node | add-member -MemberType NoteProperty -name "InternalIP" -value $nic.IpConfigurations.PrivateIpAddress
    $node | add-member -MemberType NoteProperty -name "InternalFQDN" -value $nic.DnsSettings.InternalFqdn
    $nodes += $node
}
$nodes | sort-object Type
```

__CLI de Azure__

```azurecli
az network nic list --resource-group <resourcegroupname> --output table --query "[?contains(name,'node')].{NICname:name,InternalIP:ipConfigurations[0].privateIpAddress,InternalFQDN:dnsSettings.internalFqdn}"
```

> [!IMPORTANT]
> En el ejemplo de la CLI de Azure 2.0, reemplace `<resourcegroupname>` por el nombre del grupo de recursos que contiene la red virtual.

Los scripts consultan las tarjetas de interfaz de red (NIC) virtual del clúster. Las NIC se encuentran en el grupo de recursos que alberga la red virtual que emplea HDInsight.

## <a id="nextsteps"></a>Pasos siguientes

Los siguientes ejemplos demuestran cómo usar HDInsight con Red virtual de Azure:

* [Clústeres de HBase en Azure Virtual Network](hdinsight-hbase-provision-vnet.md)

* [Análisis de datos de sensores con Storm y HBase en HDInsight](hdinsight-storm-sensor-data-analysis.md)

* [Aprovisionamiento de clústeres de Hadoop en HDInsight](hdinsight-hadoop-provision-linux-clusters.md)

* [Uso de Sqoop con Hadoop en HDInsight](hdinsight-use-sqoop-mac-linux.md)

Para obtener más información acerca de Redes virtuales de Azure, consulte la [Información general sobre Red virtual de Azure](../virtual-network/virtual-networks-overview.md).


