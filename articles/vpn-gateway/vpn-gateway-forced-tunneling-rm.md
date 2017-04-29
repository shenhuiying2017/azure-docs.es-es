---
title: "Configuración de la tunelización forzada para conexiones de sitio a sitio de Azure: Resource Manager | Microsoft Docs"
description: "Cómo redirigir o forzar todo el tráfico vinculado a Internet a la ubicación local."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: cbe58db8-b598-4c9f-ac88-62c865eb8721
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/11/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 0d9afb1554158a4d88b7f161c62fa51c1bf61a7d
ms.openlocfilehash: aae7271e62552d8393ed3b0c5b31f104fae4dbbc
ms.lasthandoff: 04/12/2017


---
# <a name="configure-forced-tunneling-using-the-azure-resource-manager-deployment-model"></a>Configuración de la tunelización forzada mediante el modelo de implementación de Azure Resource Manager

La tunelización forzada permite redirigir o forzar todo el tráfico vinculado a Internet de vuelta a su ubicación local a través de un túnel VPN de sitio a sitio para inspección y auditoría. Se trata de un requisito de seguridad crítico en la mayoría de las directivas de las empresas de TI. Sin la tunelización forzada, el tráfico vinculado a Internet desde las máquinas virtuales en Azure siempre atravesará desde la infraestructura de red de Azure directamente a Internet, sin la opción que permite inspeccionar o auditar el tráfico. Un acceso no autorizado a Internet puede provocar la divulgación de información u otros tipos de infracciones de seguridad.

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

Este artículo lo guiará a través del proceso de configuración de la tunelización forzada para redes virtuales creadas mediante el modelo de implementación de Resource Manager. La tunelización forzada puede configurarse mediante el uso de PowerShell, no a través del portal. Si desea configurar la tunelización forzada para el modelo de implementación clásica, seleccione el artículo clásico de la lista desplegable siguiente:

> [!div class="op_single_selector"]
> * [PowerShell: clásico](vpn-gateway-about-forced-tunneling.md)
> * [PowerShell: administrador de recursos](vpn-gateway-forced-tunneling-rm.md)
> 
> 


## <a name="about-forced-tunneling"></a>Información acerca de la tunelización forzada
El siguiente diagrama ilustra cómo funciona la tunelización forzada. 

![Tunelización forzada](./media/vpn-gateway-forced-tunneling-rm/forced-tunnel.png)

En el ejemplo anterior, la subred Frontend no usa la tunelización forzada. Las cargas de trabajo de la subred Frontend pueden continuar para aceptar y responder a las solicitudes de los clientes directamente desde Internet. Las subredes Mid-tier y Backend usan la tunelización forzada. Las conexiones salientes desde estas dos subredes a Internet se fuerzan o redirigen a un sitio local a través de uno de los túneles VPN de sitio a sitio.

Esto permite restringir e inspeccionar el acceso a Internet desde sus máquinas virtuales o servicios en la nube en Azure, al tiempo que continúa posibilitando la arquitectura de varios niveles de servicio necesaria. También tiene la opción de aplicar la tunelización forzada a redes virtuales completas si no hay ninguna carga de trabajo a través de Internet en las redes virtuales.

## <a name="requirements-and-considerations"></a>Requisitos y consideraciones
La tunelización forzada en Azure se configura a través de rutas definidas por el usuario de redes virtuales. La redirección del tráfico a un sitio local se expresa como una ruta predeterminada a la puerta de enlace de VPN de Azure. Para obtener más información sobre las redes virtuales y las rutas definidas por el usuario, consulte [¿Qué son las rutas definidas por el usuario y el reenvío IP?](../virtual-network/virtual-networks-udr-overview.md)

* Cada subred de la red virtual tiene una tabla de enrutamiento del sistema integrada. La tabla de enrutamiento del sistema tiene los siguientes tres grupos de rutas:
  
  * **Rutas de red virtual local** : directamente a las máquinas virtuales de destino en la misma red virtual.
  * **Rutas locales** : a la puerta de enlace de VPN de Azure.
  * **Ruta predeterminada** : directamente a Internet. Los paquetes destinados a las direcciones IP privadas que no están cubiertos por las dos rutas anteriores se anularán.
* Este procedimiento usa las rutas definidas por el usuario para crear una tabla de enrutamiento para agregar una ruta predeterminada y, a continuación, asociar la tabla de enrutamiento a las subredes de la red virtual para habilitar la tunelización forzada en esas subredes.
* La tunelización forzada debe asociarse a una red virtual que tenga una puerta de enlace de VPN basada en rutas. Deberá establecer un "sitio predeterminado" entre los sitios locales entre entornos conectados a la red virtual.
* La tunelización forzada ExpressRoute no se configura mediante este mecanismo, sino que se habilita mediante el anuncio de una ruta predeterminada a través de las sesiones de emparejamiento BGP de ExpressRoute. Consulte la [Documentación de ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/) para obtener más información.

## <a name="configuration-overview"></a>Información general sobre la configuración
El procedimiento siguiente lo ayudará a crear un grupo de recursos y una red virtual. Después, creará una puerta de enlace de VPN y configurará la tunelización forzada. En el procedimiento, la red virtual "MultiTier-VNet" tiene tres subredes: *Frontend*, *Midtier* y *Backend*, con cuatro conexiones entre locales: *DefaultSiteHQ* y tres *ramas*.

Los pasos del procedimiento establecerán *DefaultSiteHQ* como la conexión de sitio predeterminada para la tunelización forzada y configurarán las subredes Midtier y Backend para que usen dicha tunelización.

## <a name="before-you-begin"></a>Antes de empezar
Antes de comenzar con la configuración, compruebe que dispone de los elementos siguientes.

* Una suscripción de Azure. Si todavía no la tiene, puede activar sus [ventajas como suscriptor de MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) o registrarse para obtener una [cuenta gratuita](https://azure.microsoft.com/pricing/free-trial/).
* Necesitará instalar la versión más reciente de los cmdlets de PowerShell de Azure Resource Manager (1.0 o posterior). Consulte [Cómo instalar y configurar Azure PowerShell](/powershell/azureps-cmdlets-docs) para obtener más información sobre cómo instalar los cmdlets de PowerShell.

## <a name="configure-forced-tunneling"></a>Configuración de la tunelización forzada
1. En la consola de PowerShell, inicie sesión en su cuenta de Azure. El cmdlet le pedirá las credenciales de inicio de sesión para la cuenta de Azure. Después de iniciar la sesión, se descarga la configuración de la cuenta a fin de que esté disponible para Azure PowerShell.

  ```powershell
  Login-AzureRmAccount
  ```
2. Obtenga una lista de las suscripciones de Azure.

 ```powershell
 Get-AzureRmSubscription
 ```
3. Especifique la suscripción que desea usar.

  ```powershell
  Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
  ```
4. Cree un grupo de recursos.

  ```powershell
  New-AzureRmResourceGroup -Name "ForcedTunneling" -Location "North Europe"
  ```
5. Cree una red virtual y especifique las subredes.

  ```powershell 
  $s1 = New-AzureRmVirtualNetworkSubnetConfig -Name "Frontend" -AddressPrefix "10.1.0.0/24"
  $s2 = New-AzureRmVirtualNetworkSubnetConfig -Name "Midtier" -AddressPrefix "10.1.1.0/24"
  $s3 = New-AzureRmVirtualNetworkSubnetConfig -Name "Backend" -AddressPrefix "10.1.2.0/24"
  $s4 = New-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix "10.1.200.0/28"
  $vnet = New-AzureRmVirtualNetwork -Name "MultiTier-VNet" -Location "North Europe" -ResourceGroupName "ForcedTunneling" -AddressPrefix "10.1.0.0/16" -Subnet $s1,$s2,$s3,$s4
  ```
6. Cree las puertas de enlace de red local.

  ```powershell
  $lng1 = New-AzureRmLocalNetworkGateway -Name "DefaultSiteHQ" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.111" -AddressPrefix "192.168.1.0/24"
  $lng2 = New-AzureRmLocalNetworkGateway -Name "Branch1" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.112" -AddressPrefix "192.168.2.0/24"
  $lng3 = New-AzureRmLocalNetworkGateway -Name "Branch2" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.113" -AddressPrefix "192.168.3.0/24"
  $lng4 = New-AzureRmLocalNetworkGateway -Name "Branch3" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.114" -AddressPrefix "192.168.4.0/24"
  ```
7. Cree la tabla de enrutamiento y la regla de enrutamiento.

  ```powershell
  New-AzureRmRouteTable –Name "MyRouteTable" -ResourceGroupName "ForcedTunneling" –Location "North Europe"
  $rt = Get-AzureRmRouteTable –Name "MyRouteTable" -ResourceGroupName "ForcedTunneling" 
  Add-AzureRmRouteConfig -Name "DefaultRoute" -AddressPrefix "0.0.0.0/0" -NextHopType VirtualNetworkGateway -RouteTable $rt
  Set-AzureRmRouteTable -RouteTable $rt
  ```
8. Asocie la tabla de enrutamiento creada anteriormente a las subredes Midtier y Back-end.

  ```powershell
  $vnet = Get-AzureRmVirtualNetwork -Name "MultiTier-Vnet" -ResourceGroupName "ForcedTunneling"
  Set-AzureRmVirtualNetworkSubnetConfig -Name "MidTier" -VirtualNetwork $vnet -AddressPrefix "10.1.1.0/24" -RouteTable $rt
  Set-AzureRmVirtualNetworkSubnetConfig -Name "Backend" -VirtualNetwork $vnet -AddressPrefix "10.1.2.0/24" -RouteTable $rt
  Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
  ```
9. Cree la puerta de enlace con un sitio predeterminado. Este paso tarda algún tiempo en completarse, a veces 45 minutos o más, dado que va a crear y configurar la puerta de enlace.<br> **-GatewayDefaultSite** es el parámetro de cmdlet que permite que funcione la configuración de enrutamiento forzado, así que tenga cuidado al configurar este valor correctamente. Este parámetro solo está disponible en PowerShell 1.0 o posterior.

  ```powershell
  $pip = New-AzureRmPublicIpAddress -Name "GatewayIP" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -AllocationMethod Dynamic
  $gwsubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
  $ipconfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name "gwIpConfig" -SubnetId $gwsubnet.Id -PublicIpAddressId $pip.Id
  New-AzureRmVirtualNetworkGateway -Name "Gateway1" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -IpConfigurations $ipconfig -GatewayType Vpn -VpnType RouteBased -GatewayDefaultSite $lng1 -EnableBgp $false
  ```
10. Establezca las conexiones VPN de sitio a sitio.

  ```powershell
  $gateway = Get-AzureRmVirtualNetworkGateway -Name "Gateway1" -ResourceGroupName "ForcedTunneling"
  $lng1 = Get-AzureRmLocalNetworkGateway -Name "DefaultSiteHQ" -ResourceGroupName "ForcedTunneling" 
  $lng2 = Get-AzureRmLocalNetworkGateway -Name "Branch1" -ResourceGroupName "ForcedTunneling" 
  $lng3 = Get-AzureRmLocalNetworkGateway -Name "Branch2" -ResourceGroupName "ForcedTunneling" 
  $lng4 = Get-AzureRmLocalNetworkGateway -Name "Branch3" -ResourceGroupName "ForcedTunneling" 
    
  New-AzureRmVirtualNetworkGatewayConnection -Name "Connection1" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng1 -ConnectionType IPsec -SharedKey "preSharedKey"
  New-AzureRmVirtualNetworkGatewayConnection -Name "Connection2" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng2 -ConnectionType IPsec -SharedKey "preSharedKey"
  New-AzureRmVirtualNetworkGatewayConnection -Name "Connection3" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng3 -ConnectionType IPsec -SharedKey "preSharedKey"
  New-AzureRmVirtualNetworkGatewayConnection -Name "Connection4" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng4 -ConnectionType IPsec -SharedKey "preSharedKey"
    
  Get-AzureRmVirtualNetworkGatewayConnection -Name "Connection1" -ResourceGroupName "ForcedTunneling"
  ```

