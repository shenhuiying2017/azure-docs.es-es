---
title: "Creación de un equilibrador de carga con conexión a Internet con IPv6: CLI de Azure | Microsoft Docs"
description: "Obtenga información sobre cómo crear un equilibrador de carga orientado a Internet con IPv6 en Azure Resource Manager con la CLI de Azure"
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
tags: azure-resource-manager
keywords: "IPv6, Azure Load Balancer, pila doble, dirección ip pública, ipv6 nativo, móvil, iot"
ms.assetid: a1957c9c-9c1d-423e-9d5c-d71449bc1f37
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 3ae62ddd350204d801012b9810aec669abe55817
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-internet-facing-load-balancer-with-ipv6-in-azure-resource-manager-using-the-azure-cli"></a>Creación de un equilibrador de carga orientado a Internet con IPv6 en Azure Resource Manager con la CLI de Azure

> [!div class="op_single_selector"]
> * [PowerShell](load-balancer-ipv6-internet-ps.md)
> * [CLI de Azure](load-balancer-ipv6-internet-cli.md)
> * [Plantilla](load-balancer-ipv6-internet-template.md)

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

Azure Load Balancer es un equilibrador de carga de nivel 4 (TCP y UDP) que distribuye proporcionando una alta disponibilidad el tráfico entrante entre las instancias de servicio correctas de los servicios en la nube o las máquinas virtuales de un conjunto de carga equilibrada. Azure Load Balancer también pueden presentar prestar servicios en varios puertos, varias direcciones IP o ambos.

## <a name="example-deployment-scenario"></a>Escenario de implementación de ejemplo

En el siguiente diagrama se ilustra la solución de equilibrio de carga que se implementa mediante la plantilla de ejemplo descrita en este artículo.

![Escenario del equilibrador de carga](./media/load-balancer-ipv6-internet-cli/lb-ipv6-scenario-cli.png)

En este escenario creará los siguientes recursos de Azure:

* dos máquinas virtuales (VM)
* una interfaz de red virtual para cada máquina virtual con las direcciones IPv4 e IPv6 asignadas
* un equilibrador de carga orientado a Internet con una dirección IP pública IPv4 e IPv6
* un conjunto de disponibilidad con las dos máquinas virtuales
* dos reglas de equilibrio de carga para asignar las VIP públicas a los puntos de conexión privados

## <a name="deploying-the-solution-using-the-azure-cli"></a>Implementación de la solución mediante la CLI de Azure

Los pasos siguientes muestran cómo crear un equilibrador de carga orientado a Internet mediante Azure Resource Manager con la CLI. Con Azure Resource Manager, cada recurso se crea y configura de forma individual, y luego se juntan para crear un recurso.

Para implementar un equilibrador de carga, debe crear y configurar los siguientes objetos:

* Configuración de direcciones IP de front-end: contiene direcciones IP públicas para el tráfico de red entrante.
* Grupo de direcciones de back-end: contiene interfaces de red (NIC) para que las máquinas virtuales reciban tráfico de red del equilibrador de carga.
* Reglas de equilibrio de carga: contiene reglas que asignan un puerto público en el equilibrador de carga al del grupo de direcciones de back-end.
* Reglas NAT de entrada: contiene reglas que asignan un puerto público en el equilibrador de carga a un puerto para una máquina virtual específica en el grupo de direcciones de back-end.
* Sondeos: contiene los sondeos de estado que se usan para comprobar la disponibilidad de las instancias de las máquinas virtuales del grupo de direcciones de back-end.

Para más información, consulte [Compatibilidad de Azure Resource Manager con el equilibrador de carga](load-balancer-arm.md).

## <a name="set-up-your-cli-environment-to-use-azure-resource-manager"></a>Configuración del entorno de CLI para usar Azure Resource Manager

En este ejemplo, ejecutamos las herramientas de la CLI en una ventana Comandos de PowerShell. No usamos los cmdlets de Azure PowerShell, pero utilizamos las capacidades de scripting de PowerShell para mejorar la legibilidad y reutilización.

1. Si nunca ha usado la CLI de Azure, consulte [Instalación y configuración de la CLI de Azure](../cli-install-nodejs.md) y siga las instrucciones hasta el punto donde deba seleccionar su cuenta y suscripción de Azure.
2. Ejecute el comando **azure config mode** para cambiar al modo de Resource Manager.

    ```azurecli
    azure config mode arm
    ```

    Resultado esperado:

        info:    New mode is arm

3. Inicie sesión en Azure y obtenga una lista de suscripciones.

    ```azurecli
    azure login
    ```

    Escriba sus credenciales de Azure cuando se le solicite.

    ```azurecli
    azure account list
    ```

    Seleccione la suscripción que desea usar. Tome nota del Id. de suscripción para el siguiente paso.

4. Configure variables de PowerShell para usarlas con los comandos de la CLI.

    ```powershell
    $subscriptionid = "########-####-####-####-############"  # enter subscription id
    $location = "southcentralus"
    $rgName = "pscontosorg1southctrlus09152016"
    $vnetName = "contosoIPv4Vnet"
    $vnetPrefix = "10.0.0.0/16"
    $subnet1Name = "clicontosoIPv4Subnet1"
    $subnet1Prefix = "10.0.0.0/24"
    $subnet2Name = "clicontosoIPv4Subnet2"
    $subnet2Prefix = "10.0.1.0/24"
    $dnsLabel = "contoso09152016"
    $lbName = "myIPv4IPv6Lb"
    ```

## <a name="create-a-resource-group-a-load-balancer-a-virtual-network-and-subnets"></a>Creación de un grupo de recursos, un equilibrador de carga, una red virtual y subredes

1. Crear un grupo de recursos

    ```azurecli
    azure group create $rgName $location
    ```

2. Crear un equilibrador de carga

    ```azurecli
    $lb = azure network lb create --resource-group $rgname --location $location --name $lbName
    ```

3. Cree una red virtual (VNet)

    ```azurecli
    $vnet = azure network vnet create  --resource-group $rgname --name $vnetName --location $location --address-prefixes $vnetPrefix
    ```

    Cree dos subredes en esta red virtual

    ```azurecli
    $subnet1 = azure network vnet subnet create --resource-group $rgname --name $subnet1Name --address-prefix $subnet1Prefix --vnet-name $vnetName
    $subnet2 = azure network vnet subnet create --resource-group $rgname --name $subnet2Name --address-prefix $subnet2Prefix --vnet-name $vnetName
    ```

## <a name="create-public-ip-addresses-for-the-front-end-pool"></a>Creación de direcciones IP públicas para el grupo de servidores front-end

1. Configure las variables de PowerShell

    ```powershell
    $publicIpv4Name = "myIPv4Vip"
    $publicIpv6Name = "myIPv6Vip"
    ```

2. Cree una dirección IP pública para el grupo de servidores front-end

    ```azurecli
    $publicipV4 = azure network public-ip create --resource-group $rgname --name $publicIpv4Name --location $location --ip-version IPv4 --allocation-method Dynamic --domain-name-label $dnsLabel
    $publicipV6 = azure network public-ip create --resource-group $rgname --name $publicIpv6Name --location $location --ip-version IPv6 --allocation-method Dynamic --domain-name-label $dnsLabel
    ```

    > [!IMPORTANT]
    > El equilibrador de carga usa la etiqueta de dominio de la dirección IP pública como su FQDN. Esto representa un cambio en la implementación clásica, que usa el nombre del servicio en la nube como el FQDN del equilibrador de carga.
    > En este ejemplo, el FQDN es *contoso09152016.southcentralus.cloudapp.azure.com*.

## <a name="create-front-end-and-back-end-pools"></a>Creación de grupos de servidores front-end y back-end

En este ejemplo se crea el grupo de direcciones IP de front-end que recibe el tráfico de red que entra al equilibrador de carga y el grupo de direcciones IP de back-end donde el grupo de servidores front-end envía el tráfico de red con equilibrio de carga.

1. Configure las variables de PowerShell

    ```powershell
    $frontendV4Name = "FrontendVipIPv4"
    $frontendV6Name = "FrontendVipIPv6"
    $backendAddressPoolV4Name = "BackendPoolIPv4"
    $backendAddressPoolV6Name = "BackendPoolIPv6"
    ```

2. Cree un grupo de direcciones IP de front-end mediante la asociación de la dirección IP pública creada en el paso anterior y el equilibrador de carga.

    ```azurecli
    $frontendV4 = azure network lb frontend-ip create --resource-group $rgname --name $frontendV4Name --public-ip-name $publicIpv4Name --lb-name $lbName
    $frontendV6 = azure network lb frontend-ip create --resource-group $rgname --name $frontendV6Name --public-ip-name $publicIpv6Name --lb-name $lbName
    $backendAddressPoolV4 = azure network lb address-pool create --resource-group $rgname --name $backendAddressPoolV4Name --lb-name $lbName
    $backendAddressPoolV6 = azure network lb address-pool create --resource-group $rgname --name $backendAddressPoolV6Name --lb-name $lbName
    ```

## <a name="create-the-probe-nat-rules-and-lb-rules"></a>Creación del sondeo, las reglas NAT y las reglas del equilibrador de carga

En este ejemplo se crean los siguientes elementos:

* una regla de sondeo para comprobar la conectividad con el puerto TCP 80
* una regla NAT para trasladar todo el tráfico entrante del puerto 3389 al puerto 3389 para RDP<sup>1</sup>
* una regla NAT para trasladar todo el tráfico entrante del puerto 3391 al puerto 3389 para RDP<sup>1</sup>
* Una regla de equilibrador de carga para equilibrar todo el tráfico entrante del puerto 80 al puerto 80 en las direcciones del grupo de back-end.

<sup>1</sup> Las reglas NAT están asociadas a una instancia de máquina virtual específica detrás del equilibrador de carga. El tráfico de red que llega al puerto 3389 se envía a la máquina virtual específica y el puerto asociado a la regla NAT. Debe especificar un protocolo (UDP o TCP) para una regla NAT. Los dos protocolos no se pueden asignar al mismo puerto.

1. Configure las variables de PowerShell

    ```powershell
    $probeV4V6Name = "ProbeForIPv4AndIPv6"
    $natRule1V4Name = "NatRule-For-Rdp-VM1"
    $natRule2V4Name = "NatRule-For-Rdp-VM2"
    $lbRule1V4Name = "LBRuleForIPv4-Port80"
    $lbRule1V6Name = "LBRuleForIPv6-Port80"
    ```

2. Elaboración del sondeo

    En el siguiente ejemplo se crea un sondeo TCP que comprueba la conectividad con el puerto TCP 80 de back-end cada 15 segundos. Marcará el recurso de back-end no disponible tras dos errores consecutivos.

    ```azurecli
    $probeV4V6 = azure network lb probe create --resource-group $rgname --name $probeV4V6Name --protocol tcp --port 80 --interval 15 --count 2 --lb-name $lbName
    ```

3. Cree reglas NAT de entrada que permitan conexiones RDP a los recursos de back-end

    ```azurecli
    $inboundNatRuleRdp1 = azure network lb inbound-nat-rule create --resource-group $rgname --name $natRule1V4Name --frontend-ip-name $frontendV4Name --protocol Tcp --frontend-port 3389 --backend-port 3389 --lb-name $lbName
    $inboundNatRuleRdp2 = azure network lb inbound-nat-rule create --resource-group $rgname --name $natRule2V4Name --frontend-ip-name $frontendV4Name --protocol Tcp --frontend-port 3391 --backend-port 3389 --lb-name $lbName
    ```

4. Cree reglas de equilibrador de carga que envíen tráfico a distintos puertos de back-end dependiendo de qué front-end recibió la solicitud

    ```azurecli
    $lbruleIPv4 = azure network lb rule create --resource-group $rgname --name $lbRule1V4Name --frontend-ip-name $frontendV4Name --backend-address-pool-name $backendAddressPoolV4Name --probe-name $probeV4V6Name --protocol Tcp --frontend-port 80 --backend-port 80 --lb-name $lbName
    $lbruleIPv6 = azure network lb rule create --resource-group $rgname --name $lbRule1V6Name --frontend-ip-name $frontendV6Name --backend-address-pool-name $backendAddressPoolV6Name --probe-name $probeV4V6Name --protocol Tcp --frontend-port 80 --backend-port 8080 --lb-name $lbName
    ```

5. Compruebe la configuración

    ```azurecli
    azure network lb show --resource-group $rgName --name $lbName
    ```

    Resultado esperado:

        info:    Executing command network lb show
        info:    Looking up the load balancer "myIPv4IPv6Lb"
        data:    Id                              : /subscriptions/########-####-####-####-############/resourceGroups/pscontosorg1southctrlus09152016/providers/Microsoft.Network/loadBalancers/myIPv4IPv6Lb
        data:    Name                            : myIPv4IPv6Lb
        data:    Type                            : Microsoft.Network/loadBalancers
        data:    Location                        : southcentralus
        data:    Provisioning state              : Succeeded
        data:
        data:    Frontend IP configurations:
        data:    Name             Provisioning state  Private IP allocation  Private IP   Subnet  Public IP
        data:    ---------------  ------------------  ---------------------  -----------  ------  ---------
        data:    FrontendVipIPv4  Succeeded           Dynamic                                     myIPv4Vip
        data:    FrontendVipIPv6  Succeeded           Dynamic                                     myIPv6Vip
        data:
        data:    Probes:
        data:    Name                 Provisioning state  Protocol  Port  Path  Interval  Count
        data:    -------------------  ------------------  --------  ----  ----  --------  -----
        data:    ProbeForIPv4AndIPv6  Succeeded           Tcp       80          15        2
        data:
        data:    Backend Address Pools:
        data:    Name             Provisioning state
        data:    ---------------  ------------------
        data:    BackendPoolIPv4  Succeeded
        data:    BackendPoolIPv6  Succeeded
        data:
        data:    Load Balancing Rules:
        data:    Name                  Provisioning state  Load distribution  Protocol  Frontend port  Backend port  Enable floating IP  Idle timeout in minutes
        data:    --------------------  ------------------  -----------------  --------  -------------  ------------  ------------------  -----------------------
        data:    LBRuleForIPv4-Port80  Succeeded           Default            Tcp       80             80            false               4
        data:    LBRuleForIPv6-Port80  Succeeded           Default            Tcp       80             8080          false               4
        data:
        data:    Inbound NAT Rules:
        data:    Name                 Provisioning state  Protocol  Frontend port  Backend port  Enable floating IP  Idle timeout in minutes
        data:    -------------------  ------------------  --------  -------------  ------------  ------------------  -----------------------
        data:    NatRule-For-Rdp-VM1  Succeeded           Tcp       3389           3389          false               4
        data:    NatRule-For-Rdp-VM2  Succeeded           Tcp       3391           3389          false               4
        info:    network lb show

## <a name="create-nics"></a>Cree tarjetas NIC

Cree tarjetas NIC y asócielas a reglas NAT, reglas de equilibrador de carga y sondeos.

1. Configure las variables de PowerShell

    ```powershell
    $nic1Name = "myIPv4IPv6Nic1"
    $nic2Name = "myIPv4IPv6Nic2"
    $subnet1Id = "/subscriptions/$subscriptionid/resourceGroups/$rgName/providers/Microsoft.Network/VirtualNetworks/$vnetName/subnets/$subnet1Name"
    $subnet2Id = "/subscriptions/$subscriptionid/resourceGroups/$rgName/providers/Microsoft.Network/VirtualNetworks/$vnetName/subnets/$subnet2Name"
    $backendAddressPoolV4Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/loadbalancers/$lbName/backendAddressPools/$backendAddressPoolV4Name"
    $backendAddressPoolV6Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/loadbalancers/$lbName/backendAddressPools/$backendAddressPoolV6Name"
    $natRule1V4Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/loadbalancers/$lbName/inboundNatRules/$natRule1V4Name"
    $natRule2V4Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/loadbalancers/$lbName/inboundNatRules/$natRule2V4Name"
    ```

2. Cree una NIC para cada back-end y agregue una configuración de IPv6

    ```azurecli
    $nic1 = azure network nic create --name $nic1Name --resource-group $rgname --location $location --private-ip-version "IPv4" --subnet-id $subnet1Id --lb-address-pool-ids $backendAddressPoolV4Id --lb-inbound-nat-rule-ids $natRule1V4Id
    $nic1IPv6 = azure network nic ip-config create --resource-group $rgname --name "IPv6IPConfig" --private-ip-version "IPv6" --lb-address-pool-ids $backendAddressPoolV6Id --nic-name $nic1Name

    $nic2 = azure network nic create --name $nic2Name --resource-group $rgname --location $location --subnet-id $subnet1Id --lb-address-pool-ids $backendAddressPoolV4Id --lb-inbound-nat-rule-ids $natRule2V4Id
    $nic2IPv6 = azure network nic ip-config create --resource-group $rgname --name "IPv6IPConfig" --private-ip-version "IPv6" --lb-address-pool-ids $backendAddressPoolV6Id --nic-name $nic2Name
    ```

## <a name="create-the-back-end-vm-resources-and-attach-each-nic"></a>Creación de los recursos de la máquina virtual de back-end y conexión de cada NIC

Para crear máquinas virtuales, debe tener una cuenta de almacenamiento. Para el equilibrio de carga, es necesario que las máquinas virtuales formen parte de un conjunto de disponibilidad. Para obtener más información sobre cómo crear máquinas virtuales, consulte [Creación de una máquina virtual de Azure con PowerShell](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fload-balancer%2ftoc.json)

1. Configure las variables de PowerShell

    ```powershell
    $storageAccountName = "ps08092016v6sa0"
    $availabilitySetName = "myIPv4IPv6AvailabilitySet"
    $vm1Name = "myIPv4IPv6VM1"
    $vm2Name = "myIPv4IPv6VM2"
    $nic1Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/networkInterfaces/$nic1Name"
    $nic2Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/networkInterfaces/$nic2Name"
    $disk1Name = "WindowsVMosDisk1"
    $disk2Name = "WindowsVMosDisk2"
    $osDisk1Uri = "https://$storageAccountName.blob.core.windows.net/vhds/$disk1Name.vhd"
    $osDisk2Uri = "https://$storageAccountName.blob.core.windows.net/vhds/$disk2Name.vhd"
    $imageurn "MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest"
    $vmUserName = "vmUser"
    $mySecurePassword = "PlainTextPassword*1"
    ```

    > [!WARNING]
    > En este ejemplo se usan el nombre de usuario y contraseña de las máquinas virtuales en texto no cifrado. Al usar credenciales sin cifrar, debe prestarse la atención adecuada. Para ver un método más seguro de administración de credenciales en PowerShell, consulte el cmdlet [Get-Credential](https://technet.microsoft.com/library/hh849815.aspx) .

2. Creación de la cuenta de almacenamiento y el conjunto de disponibilidad

    Puede usar una cuenta de almacenamiento existente al crear las máquinas virtuales. El siguiente comando crea una nueva cuenta de almacenamiento.

    ```azurecli
    $storageAcc = azure storage account create $storageAccountName --resource-group $rgName --location $location --sku-name "LRS" --kind "Storage"
    ```

    A continuación, cree el conjunto de disponibilidad.

    ```azurecli
    $availabilitySet = azure availset create --name $availabilitySetName --resource-group $rgName --location $location
    ```

3. Creación de las máquinas virtuales con las NIC asociadas

    ```azurecli
    $vm1 = azure vm create --resource-group $rgname --location $location --availset-name $availabilitySetName --name $vm1Name --nic-id $nic1Id --os-disk-vhd $osDisk1Uri --os-type "Windows" --admin-username $vmUserName --admin-password $mySecurePassword --vm-size "Standard_A1" --image-urn $imageurn --storage-account-name $storageAccountName --disable-bginfo-extension

    $vm2 = azure vm create --resource-group $rgname --location $location --availset-name $availabilitySetName --name $vm2Name --nic-id $nic2Id --os-disk-vhd $osDisk2Uri --os-type "Windows" --admin-username $vmUserName --admin-password $mySecurePassword --vm-size "Standard_A1" --image-urn $imageurn --storage-account-name $storageAccountName --disable-bginfo-extension
    ```

## <a name="next-steps"></a>Pasos siguientes

[Introducción a la configuración de un equilibrador de carga interno](load-balancer-get-started-ilb-arm-cli.md)

[Configuración de un modo de distribución del equilibrador de carga](load-balancer-distribution-mode.md)

[Configuración de opciones de tiempo de espera de inactividad de TCP para el equilibrador de carga](load-balancer-tcp-idle-timeout.md)
