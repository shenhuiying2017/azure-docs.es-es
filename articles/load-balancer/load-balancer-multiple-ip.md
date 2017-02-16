---
title: Equilibrio de carga en varias configuraciones de IP | Microsoft Docs
description: Equilibrio de carga entre las configuraciones de IP principales y secundarias.
services: load-balancer
documentationcenter: na
author: anavinahar
manager: narayan
editor: na
ms.assetid: 244907cd-b275-4494-aaf7-dcfc4d93edfe
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/28/2016
ms.author: annahar
translationtype: Human Translation
ms.sourcegitcommit: 8761e32f22471e00e33605916fc145881c8fe378
ms.openlocfilehash: fed40bd97114a2f4eafedc5587b4744d73b2a0ff

---

# <a name="load-balancing-on-multiple-ip-configurations"></a>Equilibrio de carga en varias configuraciones de IP

> [!div class="op_single_selector"]
> * [PowerShell](load-balancer-multiple-ip.md)
> * [CLI](load-balancer-multiple-ip-cli.md)
>

En este artículo, se explica cómo se utiliza Azure Load Balancer cuando hay varias direcciones IP en cada interfaz de red (NIC) virtual. La compatibilidad con varias direcciones IP en una NIC es una característica que, en este momento, está incluida en versión preliminar. Para más información, consulte la sección [Limitaciones](#limitations) de este artículo. En el siguiente escenario se ilustra cómo funciona esta característica con Load Balancer.

En este escenario, tenemos dos máquinas virtuales que ejecutan Windows. Cada una de ellas cuenta con una sola NIC. Cada NIC, a su vez, tiene varias configuraciones de IP. Cada una de las máquinas virtuales hospeda dos sitios web: contoso.com y fabrikam.com. Cada uno de los sitios web está enlazado a una de las configuraciones de IP de la NIC. Usamos Load Balancer para exponer dos direcciones IP front-end, una por cada sitio web, que van a distribuir el tráfico a la configuración de IP correspondiente del sitio web. En este escenario, se utiliza el mismo número de puerto en los dos front-end, así como en las dos direcciones IP del grupo de back-end.

![Imagen del escenario de equilibrio de carga](./media/load-balancer-multiple-ip/lb-multi-ip.PNG)

## <a name="limitations"></a>Limitaciones

En este momento, la configuración del equilibrio de carga en las configuraciones de IP secundarias solo es posible mediante la CLI de Azure y Azure PowerShell. Esta limitación es temporal y puede cambiar en cualquier momento. Vuelva a visitar esta página para comprobar si hay actualizaciones.

[!INCLUDE [virtual-network-preview](../../includes/virtual-network-preview.md)]

Para registrarse para la versión preliminar, envíe un correo electrónico a [Multiple IPs](mailto:MultipleIPsPreview@microsoft.com?subject=Request%20to%20enable%20subscription%20%3csubscription%20id%3e) con el identificador de suscripción y el uso previsto.

## <a name="steps-to-load-balance-on-multiple-ip-configurations"></a>Pasos para equilibrar la carga en varias configuraciones de IP

Siga estos pasos para reproducir el escenario que se describe en este artículo:

1. Instale Azure PowerShell. Consulte [Cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md) para obtener más información sobre cómo instalar la versión más reciente de Azure PowerShell, seleccionar la suscripción que quiere usar e iniciar sesión en su cuenta.
2. Cree un grupo de recursos con el siguiente comando:

    ```powershell
    $location = "westcentralus".
    $myResourceGroup = "contosofabrikam"
    ```

    Para más información, consulte el [Paso 2: Creación de un grupo de recursos](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fload-balancer%2ftoc.json).

3. [Cree un conjunto de disponibilidad](../virtual-machines/virtual-machines-windows-create-availability-set.md?toc=%2fazure%2fload-balancer%2ftoc.json) que contenga sus máquinas virtuales. En este caso, utilice el siguiente comando:

    ```powershell
    New-AzureRmAvailabilitySet -ResourceGroupName "contosofabrikam" -Name "myAvailset" -Location "West Central US"
    ```

4. Siga las instrucciones de los pasos 3 a 5 del artículo [Creación de una máquina virtual Windows](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fload-balancer%2ftoc.json) para preparar la creación de una máquina virtual con una sola NIC. Realice el paso 6.1 y use el siguiente código en lugar del paso 6.2:

    ```powershell
    $availset = Get-AzureRmAvailabilitySet -ResourceGroupName "contosofabrikam" -Name "myAvailset"
    New-AzureRmVMConfig -VMName "VM1" -VMSize "Standard_DS1_v2" -AvailabilitySetId $availset.Id
    ```

    Después, complete los pasos 6.3 a 6.8 de [Creación de una máquina virtual Windows](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fload-balancer%2ftoc.json).

5. Agregue una segunda configuración de IP a cada una de las máquinas virtuales. Siga las instrucciones del artículo [Asignación de varias direcciones IP a máquinas virtuales](../virtual-network/virtual-network-multiple-ip-addresses-powershell.md#add). Use las opciones de configuración siguientes:

    ```powershell
    $NicName = "VM1-NIC"
    $RgName = "contosofabrikam"
    $NicLocation = "West Central US"
    $IPConfigName4 = "VM1-ipconfig2"
    $Subnet1 = Get-AzureRmVirtualNetworkSubnetConfig -Name "mySubnet" -VirtualNetwork $myVnet
    ```

    Para los fines de este tutorial, no es necesario asociar las configuraciones de IP secundarias a direcciones IP públicas. Edite el comando para quitar la parte pública de la asociación de IP.

6. Complete de nuevo los pasos 4 a 6 de este artículo para VM2. Al hacerlo, asegúrese de reemplazar el nombre de la máquina virtual por VM2. Tenga en cuenta que no es necesario crear una red virtual para la segunda máquina virtual. Según sus necesidades de uso, puede crear o no una nueva subred.

7. Cree dos direcciones IP públicas y almacénelas en las variables correspondientes, tal y como se muestra:

    ```powershell
    $publicIP1 = New-AzureRmPublicIpAddress -Name PublicIp1 -ResourceGroupName contosofabrikam -Location 'West Central US' -AllocationMethod Dynamic -DomainNameLabel contoso
    $publicIP2 = New-AzureRmPublicIpAddress -Name PublicIp2 -ResourceGroupName contosofabrikam -Location 'West Central US' -AllocationMethod Dynamic -DomainNameLabel fabrikam

    $publicIP1 = Get-AzureRmPublicIpAddress -Name PublicIp1 -ResourceGroupName contosofabrikam
    $publicIP2 = Get-AzureRmPublicIpAddress -Name PublicIp2 -ResourceGroupName contosofabrikam
    ```

8. Cree dos configuraciones de IP de front-end:

    ```powershell
    $frontendIP1 = New-AzureRmLoadBalancerFrontendIpConfig -Name contosofe -PublicIpAddress $publicIP1
    $frontendIP2 = New-AzureRmLoadBalancerFrontendIpConfig -Name fabrikamfe -PublicIpAddress $publicIP2
    ```

9. Cree los grupos de direcciones de back-end, un sondeo y las reglas de equilibrio de carga:

    ```powershell
    $beaddresspool1 = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name contosopool
    $beaddresspool2 = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name fabrikampool

    $healthProbe = New-AzureRmLoadBalancerProbeConfig -Name HTTP -RequestPath 'index.html' -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2

    $lbrule1 = New-AzureRmLoadBalancerRuleConfig -Name HTTPc -FrontendIpConfiguration $frontendIP1 -BackendAddressPool $beaddresspool1 -Probe $healthprobe -Protocol Tcp -FrontendPort 80 -BackendPort 80
    $lbrule2 = New-AzureRmLoadBalancerRuleConfig -Name HTTPf -FrontendIpConfiguration $frontendIP2 -BackendAddressPool $beaddresspool2 -Probe $healthprobe -Protocol Tcp -FrontendPort 80 -BackendPort 80
    ```

10. Una vez que tenga estos recursos creados, cree el equilibrador de carga:

    ```powershell
    $mylb = New-AzureRmLoadBalancer -ResourceGroupName contosofabrikam -Name mylb -Location 'West Central US' -FrontendIpConfiguration $frontendIP1 -LoadBalancingRule $lbrule -BackendAddressPool $beAddressPool -Probe $healthProbe
    ```

11. Agregue el segundo grupo de direcciones de back-end y la configuración de IP de front-end al equilibrador de carga recién creado:

    ```powershell
    $mylb = Get-AzureRmLoadBalancer -Name "mylb" -ResourceGroupName $myResourceGroup | Add-AzureRmLoadBalancerBackendAddressPoolConfig -Name fabrikampool | Set-AzureRmLoadBalancer

    $mylb | Add-AzureRmLoadBalancerFrontendIpConfig -Name fabrikamfe -PublicIpAddress $publicIP2 | Set-AzureRmLoadBalancer
    
    Add-AzureRmLoadBalancerRuleConfig -Name HTTP -LoadBalancer $mylb -FrontendIpConfiguration $frontendIP2 -BackendAddressPool $beaddresspool2 -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80 | Set-AzureRmLoadBalancer
    ```

12. Los siguientes comandos obtienen las NIC y agregan ambas configuraciones de IP de cada NIC al grupo de direcciones de back-end del equilibrador de carga:

    ```powershell
    $nic1 = Get-AzureRmNetworkInterface -Name "VM1-NIC" -ResourceGroupName "MyResourcegroup";
    $nic2 = Get-AzureRmNetworkInterface -Name "VM2-NIC" -ResourceGroupName "MyResourcegroup";

    $nic1.IpConfigurations[0].LoadBalancerBackendAddressPools.Add($mylb.BackendAddressPools[0]);
    $nic1.IpConfigurations[1].LoadBalancerBackendAddressPools.Add($mylb.BackendAddressPools[1]);
    $nic2.IpConfigurations[0].LoadBalancerBackendAddressPools.Add($mylb.BackendAddressPools[0]);
    $nic2.IpConfigurations[1].LoadBalancerBackendAddressPools.Add($mylb.BackendAddressPools[1]);

    $mylb = $mylb | Set-AzureRmLoadBalancer

    $nic1 | Set-AzureRmNetworkInterface
    $nic2 | Set-AzureRmNetworkInterface
    ```

13. Por último, debe configurar los registros de recursos DNS para que apunten a la dirección IP de front-end correspondiente del equilibrador de carga. Puede hospedar los dominios en Azure DNS. Para más información sobre el uso de Azure DNS con Load Balancer, consulte [Uso de Azure DNS con otros servicios de Azure](../dns/dns-for-azure-services.md).



<!--HONumber=Nov16_HO5-->


