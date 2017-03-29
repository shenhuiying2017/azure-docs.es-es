---
title: "Creación de un equilibrador de carga con conexión a Internet de Azure: PowerShell | Microsoft Docs"
description: Aprenda a crear un equilibrador de carga accesible desde Internet en Resource Manager con PowerShell
services: load-balancer
documentationcenter: na
author: kumudd
manager: timlt
tags: azure-resource-manager
ms.assetid: 8257f548-7019-417f-b15f-d004a1eec826
ms.service: load-balancer
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: kumud
translationtype: Human Translation
ms.sourcegitcommit: fd5960a4488f2ecd93ba117a7d775e78272cbffd
ms.openlocfilehash: 5abd8365ed883831d4c85ebd14de31dbe45d815d
ms.lasthandoff: 01/24/2017

---

# <a name="get-started"></a>Creación de un equilibrador de carga orientado a Internet en Resource Manager mediante PowerShell

> [!div class="op_single_selector"]
> * [Portal](../load-balancer/load-balancer-get-started-internet-portal.md)
> * [PowerShell](../load-balancer/load-balancer-get-started-internet-arm-ps.md)
> * [CLI de Azure](../load-balancer/load-balancer-get-started-internet-arm-cli.md)
> * [Plantilla](../load-balancer/load-balancer-get-started-internet-arm-template.md)

[!INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Este artículo trata sobre el modelo de implementación del Administrador de recursos. También puede [aprender a crear un equilibrador de carga con acceso desde Internet mediante el modelo de implementación clásica](load-balancer-get-started-internet-classic-cli.md).

[!INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]

## <a name="deploying-the-solution-by-using-azure-powershell"></a>Implementación de la solución mediante Azure PowerShell

Los siguientes procedimientos explican cómo crear un equilibrador de carga accesible desde Internet mediante Azure Resource Manager con PowerShell. Con Azure Resource Manager, cada recurso se crea y configura de forma individual, y luego se juntan para crear un equilibrador de carga.

Para implementar un equilibrador de carga, debe crear y configurar los siguientes objetos:

* Configuración de direcciones IP front-end: contiene direcciones IP públicas (PIP) para el tráfico de red entrante.
* Grupo de direcciones de back-end: contiene interfaces de red (NIC) para que las máquinas virtuales reciban tráfico de red del equilibrador de carga.
* Reglas de equilibrio de carga: contiene reglas que asignan un puerto público en el equilibrador de carga a un puerto del grupo de direcciones de back-end.
* Reglas NAT de entrada: contiene reglas que asignan un puerto público en el equilibrador de carga a un puerto de una máquina virtual específica en el grupo de direcciones de back-end.
* Sondeos: contiene sondeos de estado que se usan para comprobar la disponibilidad de las instancias de máquina virtual del grupo de direcciones de back-end.

Para más información, consulte [Compatibilidad de Azure Resource Manager con el equilibrador de carga](load-balancer-arm.md).

## <a name="set-up-powershell-to-use-resource-manager"></a>Configuración de PowerShell para usar Resource Manager

Asegúrese de que tiene la última versión de producción del módulo Azure Resource Manager para PowerShell:

1. Inicie sesión en Azure.

    ```powershell
    Login-AzureRmAccount
    ```

    Escriba sus credenciales cuando se le solicite.

2. Compruebe las suscripciones para la cuenta.

    ```powershell
    Get-AzureRmSubscription
    ```

3. Elección de la suscripción de Azure que se va a usar.

    ```powershell
    Select-AzureRmSubscription -SubscriptionId 'GUID of subscription'
    ```

4. Cree un grupo de recursos. (Si utiliza un grupo de recursos existente, puede omitir este paso).

    ```powershell
    New-AzureRmResourceGroup -Name NRP-RG -location "West US"
    ```

## <a name="create-a-virtual-network-and-a-public-ip-address-for-the-front-end-ip-pool"></a>Creación de una red virtual y una dirección IP pública para el grupo de direcciones IP front-end

1. Cree una subred y una red virtual.

    ```powershell
    $backendSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name LB-Subnet-BE -AddressPrefix 10.0.2.0/24
    New-AzureRmvirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG -Location 'West US' -AddressPrefix 10.0.0.0/16 -Subnet $backendSubnet
    ```

2. Cree un recurso de dirección IP pública de Azure, llamado **PublicIP**, para que lo use un grupo de direcciones IP front-end con el nombre DNS **loadbalancernrp.westus.cloudapp.azure.com**. El siguiente comando usa el tipo de asignación estática.

    ```powershell
    $publicIP = New-AzureRmPublicIpAddress -Name PublicIp -ResourceGroupName NRP-RG -Location 'West US' -AllocationMethod Static -DomainNameLabel loadbalancernrp
    ```

   > [!IMPORTANT]
   > El equilibrador de carga usa la etiqueta de dominio de la dirección IP pública como prefijo para su FQDN. Esto es diferente del modelo de implementación clásica, que usa el servicio en la nube como FQDN del equilibrador de carga.
   > En este ejemplo, el FQDN es **loadbalancernrp.westus.cloudapp.azure.com**.

## <a name="create-a-front-end-ip-pool-and-a-back-end-address-pool"></a>Creación de un grupo de direcciones IP front-end y un grupo de direcciones back-end

1. Cree un grupo de direcciones IP front-end llamado **LB-Frontend** que use el recurso **PublicIp**.

    ```powershell
    $frontendIP = New-AzureRmLoadBalancerFrontendIpConfig -Name LB-Frontend -PublicIpAddress $publicIP
    ```

2. Cree un grupo de direcciones de back-end llamado **LB-backend**.

    ```powershell
    $beaddresspool = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name LB-backend
    ```

## <a name="create-nat-rules-a-load-balancer-rule-a-probe-and-a-load-balancer"></a>Creación de reglas NAT, una regla de equilibrador de carga, un sondeo y un equilibrador de carga

En este ejemplo se crean los siguientes elementos:

* Una regla NAT para trasladar todo el tráfico entrante del puerto 3441 al puerto 3389.
* Una regla NAT para trasladar todo el tráfico entrante del puerto 3442 al puerto 3389.
* Una regla de sondeo para comprobar el estado de mantenimiento en una página llamada **HealthProbe.aspx**
* Una regla de equilibrador de carga para equilibrar todo el tráfico entrante del puerto 80 al puerto 80 en las direcciones del grupo de back-end.
* Un equilibrador de carga que usa todos estos objetos.

Siga estos pasos:

1. Cree las reglas NAT.

    ```powershell
    $inboundNATRule1= New-AzureRmLoadBalancerInboundNatRuleConfig -Name RDP1 -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3441 -BackendPort 3389

    $inboundNATRule2= New-AzureRmLoadBalancerInboundNatRuleConfig -Name RDP2 -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3442 -BackendPort 3389
    ```

2. Cree un sondeo de estado. Hay dos formas de configurar un sondeo:

    Sondeo HTTP

    ```powershell
    $healthProbe = New-AzureRmLoadBalancerProbeConfig -Name HealthProbe -RequestPath 'HealthProbe.aspx' -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2
    ```

    Sondeo TCP

    ```powershell
    $healthProbe = New-AzureRmLoadBalancerProbeConfig -Name HealthProbe -Protocol Tcp -Port 80 -IntervalInSeconds 15 -ProbeCount 2
    ```

3. Cree una regla de equilibrador de carga.

    ```powershell
    $lbrule = New-AzureRmLoadBalancerRuleConfig -Name HTTP -FrontendIpConfiguration $frontendIP -BackendAddressPool  $beAddressPool -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80
    ```

4. Cree el equilibrador de carga mediante los objetos creados anteriormente.

    ```powershell
    $NRPLB = New-AzureRmLoadBalancer -ResourceGroupName NRP-RG -Name NRP-LB -Location 'West US' -FrontendIpConfiguration $frontendIP -InboundNatRule $inboundNATRule1,$inboundNatRule2 -LoadBalancingRule $lbrule -BackendAddressPool $beAddressPool -Probe $healthProbe
    ```

## <a name="create-nics"></a>Cree tarjetas NIC

Cree interfaces de red (o modifique las existentes) y asócielas a reglas NAT, reglas de equilibrador de carga y sondeos:

1. Obtenga la red virtual y una subred de red virtual, donde deben crearse las NIC.

    ```powershell
    $vnet = Get-AzureRmVirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG
    $backendSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name LB-Subnet-BE -VirtualNetwork $vnet
    ```

2. Cree una NIC llamada **lb-nic1-be**y asóciela con la primera regla NAT y el primer (y único) grupo de direcciones de back-end.

    ```powershell
    $backendnic1= New-AzureRmNetworkInterface -ResourceGroupName NRP-RG -Name lb-nic1-be -Location 'West US' -PrivateIpAddress 10.0.2.6 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[0]
    ```

3. Cree una NIC llamada **lb-nic2-be**y asóciela con la segunda regla NAT y el primer (y único) grupo de direcciones de back-end.

    ```powershell
    $backendnic2= New-AzureRmNetworkInterface -ResourceGroupName NRP-RG -Name lb-nic2-be -Location 'West US' -PrivateIpAddress 10.0.2.7 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[1]
    ```

4. Compruebe las tarjetas NIC.

        $backendnic1

    Resultado esperado:

        Name                 : lb-nic1-be
        ResourceGroupName    : NRP-RG
        Location             : westus
        Id                   : /subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/networkInterfaces/lb-nic1-be
        Etag                 : W/"d448256a-e1df-413a-9103-a137e07276d1"
        ResourceGuid         : 896cac4f-152a-40b9-b079-3e2201a5906e
        ProvisioningState    : Succeeded
        Tags                 :
        VirtualMachine       : null
        IpConfigurations     : [
                            {
                            "Name": "ipconfig1",
                            "Etag": "W/\"d448256a-e1df-413a-9103-a137e07276d1\"",
                            "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/networkInterfaces/lb-nic1-be/ipConfigurations/ipconfig1",
                            "PrivateIpAddress": "10.0.2.6",
                            "PrivateIpAllocationMethod": "Static",
                            "Subnet": {
                                "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/virtualNetworks/NRPVNet/subnets/LB-Subnet-BE"
                            },
                            "ProvisioningState": "Succeeded",
                            "PrivateIpAddressVersion": "IPv4",
                            "PublicIpAddress": {
                                "Id": null
                            },
                            "LoadBalancerBackendAddressPools": [
                                {
                                "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/loadBalancers/NRPlb/backendAddressPools/LB-backend"
                                }
                            ],
                            "LoadBalancerInboundNatRules": [
                                {
                                "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/loadBalancers/NRPlb/inboundNatRules/RDP1"
                                }
                            ],
                            "Primary": true,
                            "ApplicationGatewayBackendAddressPools": []
                            }
                        ]
        DnsSettings          : {
                            "DnsServers": [],
                            "AppliedDnsServers": [],
                            "InternalDomainNameSuffix": "prcwibzcuvie5hnxav0yjks2cd.dx.internal.cloudapp.net"
                        }
        EnableIPForwarding   : False
        NetworkSecurityGroup : null
        Primary              :

5. Use el cmdlet `Add-AzureRmVMNetworkInterface` para asignar las tarjetas NIC a distintas máquinas virtuales.

## <a name="create-a-virtual-machine"></a>de una máquina virtual

Para instrucciones sobre cómo crear una máquina virtual y asignar una NIC, consulte el artículo sobre cómo [crear una máquina virtual de Azure con PowerShell](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fload-balancer%2ftoc.json).

## <a name="add-the-network-interface-to-the-load-balancer"></a>Adición de la interfaz de red al equilibrador de carga

1. Recupere el equilibrador de carga de Azure.

    Cargue el recurso de equilibrador de carga en una variable (si no lo ha hecho todavía). La variable se denomina **$lb**. Use los mismos nombres del recurso de equilibrador de carga que creó anteriormente.

    ```powershell
    $lb= get-azurermloadbalancer -name NRP-LB -resourcegroupname NRP-RG
    ```

2. Cargue la configuración de back-end en una variable.

    ```powershell
    $backend=Get-AzureRmLoadBalancerBackendAddressPoolConfig -name LB-backend -LoadBalancer $lb
    ```

3. Cargue la interfaz de red ya creada en una variable. El nombre de la variable es **$nic**. El nombre de la interfaz de red es el mismo que el del ejemplo anterior.

    ```powershell
    $nic =get-azurermnetworkinterface -name lb-nic1-be -resourcegroupname NRP-RG
    ```

4. Cambie la configuración de back-end en la interfaz de red.

    ```powershell
    $nic.IpConfigurations[0].LoadBalancerBackendAddressPools=$backend
    ```

5. Guarde el objeto de interfaz de red.

    ```powershell
    Set-AzureRmNetworkInterface -NetworkInterface $nic
    ```

    Después de agregar una interfaz de red al grupo de back-end del equilibrador de carga, comienza la recepción del tráfico de red según la reglas de equilibrio de carga para ese recurso de equilibrador de carga.

## <a name="update-an-existing-load-balancer"></a>Actualización de un equilibrador de carga existente

1. Con el equilibrador de carga del ejemplo anterior, asigne un objeto de equilibrador de carga a la variable **$slb** con `Get-AzureLoadBalancer`.

    ```powershell
    $slb = get-AzureRmLoadBalancer -Name NRP-LB -ResourceGroupName NRP-RG
    ```

2. En el ejemplo siguiente, agregará una nueva regla NAT de entrada, mediante el puerto 81 en el grupo de front-end y el puerto 8181 en el grupo de back-end, a un equilibrador de carga existente.

    ```powershell
    $slb | Add-AzureRmLoadBalancerInboundNatRuleConfig -Name NewRule -FrontendIpConfiguration $slb.FrontendIpConfigurations[0] -FrontendPort 81  -BackendPort 8181 -Protocol TCP
    ```

3. Guarde la nueva configuración mediante `Set-AzureLoadBalancer`.

    ```powershell
    $slb | Set-AzureRmLoadBalancer
    ```

## <a name="remove-a-load-balancer"></a>Elimine un equilibrador de carga

Use el comando`Remove-AzureLoadBalancer` para eliminar un equilibrador de carga creado previamente denominado **NRP-LB** en un grupo de recursos llamado **NRP-RG**.

```powershell
Remove-AzureRmLoadBalancer -Name NRP-LB -ResourceGroupName NRP-RG
```

> [!NOTE]
> Puede usar el modificador opcional **-Force** para evitar la solicitud de eliminación.

## <a name="next-steps"></a>Pasos siguientes

[Introducción a la configuración de un equilibrador de carga interno](load-balancer-get-started-ilb-arm-ps.md)

[Configuración de un modo de distribución del equilibrador de carga](load-balancer-distribution-mode.md)

[Configuración de opciones de tiempo de espera de inactividad de TCP para el equilibrador de carga](load-balancer-tcp-idle-timeout.md)

