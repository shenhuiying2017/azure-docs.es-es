---
title: Vinculación de una red virtual a un circuito ExpressRoute a través de PowerShell | Microsoft Docs
description: Este documento proporciona información general sobre cómo vincular redes virtuales a circuitos ExpressRoute mediante el modelo de implementación de Resource Manager y PowerShell.
services: expressroute
documentationcenter: na
author: ganesr
manager: carmonm
editor: ''
tags: azure-resource-manager

ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/10/2016
ms.author: ganesr

---
# <a name="link-a-virtual-network-to-an-expressroute-circuit"></a>Vinculación de una red virtual a un circuito ExpressRoute
> [!div class="op_single_selector"]
> * [Portal de Azure: Resource Manager](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [PowerShell: administrador de recursos](expressroute-howto-linkvnet-arm.md)
> * [PowerShell: clásico](expressroute-howto-linkvnet-classic.md)
> 
> 

Este artículo le ayudará a vincular redes virtuales a circuitos ExpressRoute de Azure a través del modelo de implementación de Resource Manager y PowerShell. Las redes virtuales pueden estar en la misma suscripción o formar parte de otra suscripción.

**Información sobre los modelos de implementación de Azure**

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="configuration-prerequisites"></a>Requisitos previos de configuración
* Necesitará la versión más reciente de los módulos de Azure PowerShell (como mínimo, la versión 1.0). Consulte [Cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md) para más información sobre cómo instalar los cmdlets de PowerShell.
* Debe revisar los [requisitos previos](expressroute-prerequisites.md), los [requisitos de enrutamiento](expressroute-routing.md) y los [flujos de trabajo](expressroute-workflows.md) antes de comenzar la configuración.
* Tiene que tener un circuito ExpressRoute activo. 
  * Siga las instrucciones para [crear un circuito ExpressRoute](expressroute-howto-circuit-arm.md) y habilite el circuito mediante el proveedor de conectividad. 
  * Asegúrese de que dispone de un emparejamiento privado de Azure configurado para el circuito. Consulte el artículo de [configuración del enrutamiento](expressroute-howto-routing-arm.md) para obtener instrucciones sobre el enrutamiento. 
  * Asegúrese de que el emparejamiento privado de Azure está configurado y el emparejamiento BGP entre la red y Microsoft está activo para habilitar la conectividad de extremo a extremo.
  * Asegúrese de que ha creado y aprovisionado totalmente una red virtual y una puerta de enlace de red virtual. Siga las instrucciones para crear una [puerta de enlace de VPN`-GatewayType ExpressRoute`, pero asegúrese de usar ](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md).

Es posible vincular hasta 10 redes virtuales a un circuito ExpressRoute estándar. Todas las redes virtuales deben pertenecer a la misma región geopolítica al utilizar un circuito de ExpressRoute estándar. 

Puede vincular una red virtual fuera de la región geopolítica del circuito de ExpressRoute, o bien conectar un mayor número de redes virtuales a este si habilitó el complemento premium de ExpressRoute. Consulte las [preguntas más frecuentes](expressroute-faqs.md) para obtener más detalles sobre el complemento premium.

## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>Conexión de una red virtual en la misma suscripción a un circuito
Puede conectar una puerta de enlace de red virtual a un circuito ExpressRoute mediante el siguiente cmdlet. Asegúrese de que la puerta de enlace de red virtual se crea y está lista para vincular antes de ejecutar el cmdlet:

    $circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
    $gw = Get-AzureRmVirtualNetworkGateway -Name "ExpressRouteGw" -ResourceGroupName "MyRG"
    $connection = New-AzureRmVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName "MyRG" -Location "East US" -VirtualNetworkGateway1 $gw -PeerId $circuit.Id -ConnectionType ExpressRoute

## <a name="connect-a-virtual-network-in-a-different-subscription-to-a-circuit"></a>Conexión de una red virtual en una suscripción diferente a un circuito
Puede compartir un circuito ExpressRoute entre varias suscripciones. En la ilustración siguiente se muestra un sencillo esquema de cómo funciona el uso compartido de circuitos ExpressRoute entre varias suscripciones.

Cada una de las nubes más pequeñas dentro de la nube de gran tamaño se usa para representar las suscripciones que pertenecen a diferentes departamentos dentro de una organización. Cada departamento de la organización puede usar su propia suscripción para implementar sus servicios, pero puede compartir un único circuito ExpressRoute para volver a conectarse a la red local. Un solo departamento (en este ejemplo: TI) puede ser el propietario del circuito ExpressRoute. Otras suscripciones dentro de la organización pueden usar el circuito ExpressRoute.

> [!NOTE]
> Los cargos de conectividad y ancho de banda de un circuito dedicado recaerán en el propietario del circuito ExpressRoute. Todas las redes virtuales comparten el mismo ancho de banda.
> 
> 

![Conectividad entre suscripciones](./media/expressroute-howto-linkvnet-classic/cross-subscription.png)

### <a name="administration"></a>Administración
El *propietario del circuito* es un usuario avanzado autorizado del recurso de circuito ExpressRoute. Puede crear autorizaciones que los *usuarios del circuito*, a su vez, pueden canjear. *Circuit users* son propietarios de puertas de enlace de red virtual (que no se incluyen en la misma suscripción que el circuito ExpressRoute). *Circuit users* pueden canjear autorizaciones (una autorización por red virtual).

El *propietario del circuito* tiene la capacidad de modificar y revocar las autorizaciones en cualquier momento. La revocación de una autorización dará como resultado la eliminación de todas las conexiones de vínculos de la suscripción cuyo acceso se haya revocado.

### <a name="circuit-owner-operations"></a>Operaciones del propietario del circuito
#### <a name="creating-an-authorization"></a>Creación de una autorización
El propietario del circuito crea una autorización. Esto da lugar a la creación de una clave de autorización que puede usar un usuario del circuito para conectar sus puertas de enlace de red virtual al circuito ExpressRoute. Una autorización solo es válida para una conexión.

El fragmento de código del cmdlet siguiente muestra cómo crear una autorización:

    $circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
    Add-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "MyAuthorization1"
    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $circuit

        $circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
    $auth1 = Get-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "MyAuthorization1"


La respuesta a esta contendrá la clave y el estado de la autorización:

    Name                   : MyAuthorization1
    Id                     : /subscriptions/&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&/resourceGroups/ERCrossSubTestRG/providers/Microsoft.Network/expressRouteCircuits/CrossSubTest/authorizations/MyAuthorization1
    Etag                   : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&& 
    AuthorizationKey       : ####################################
    AuthorizationUseStatus : Available
    ProvisioningState      : Succeeded



#### <a name="reviewing-authorizations"></a>Revisión de las autorizaciones
El propietario del circuito puede revisar todas las autorizaciones emitidas en un circuito concreto ejecutando el siguiente cmdlet:

    $circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
    $authorizations = Get-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit


#### <a name="adding-authorizations"></a>Adición de autorizaciones
El propietario del circuito puede agregar las autorizaciones mediante el siguiente cmdlet:

    $circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
    Add-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "MyAuthorization2"
    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $circuit

    $circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
    $authorizations = Get-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit


#### <a name="deleting-authorizations"></a>Eliminación de autorizaciones
El propietario del circuito puede revocar o eliminar las autorizaciones al usuario ejecutando el siguiente cmdlet:

    Remove-AzureRmExpressRouteCircuitAuthorization -Name "MyAuthorization2" -ExpressRouteCircuit $circuit
    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $circuit    

### <a name="circuit-user-operations"></a>Operaciones del usuario del circuito
El usuario del circuito necesita el Id. de mismo nivel y una clave de autorización del propietario del circuito. la clave de autorización es un GUID.

El id. de mismo nivel se puede comprobar con el siguiente comando.

    Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"

#### <a name="redeeming-connection-authorizations"></a>Canjear autorizaciones de conexión
El usuario del circuito puede ejecutar el siguiente cmdlet para canjear una autorización de vínculo:

    $id = "/subscriptions/********************************/resourceGroups/ERCrossSubTestRG/providers/Microsoft.Network/expressRouteCircuits/MyCircuit"  
    $gw = Get-AzureRmVirtualNetworkGateway -Name "ExpressRouteGw" -ResourceGroupName "MyRG"
    $connection = New-AzureRmVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName "RemoteResourceGroup" -Location "East US" -VirtualNetworkGateway1 $gw -PeerId $id -ConnectionType ExpressRoute -AuthorizationKey "^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^"

#### <a name="releasing-connection-authorizations"></a>Liberación de autorizaciones de conexión
Puede liberar una autorización eliminando la conexión que vincula el circuito ExpressRoute a la red virtual.

## <a name="next-steps"></a>Pasos siguientes
Para obtener más información acerca de ExpressRoute, consulte [P+F de ExpressRoute](expressroute-faqs.md).

<!--HONumber=Oct16_HO2-->


