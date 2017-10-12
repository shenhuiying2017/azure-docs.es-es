---
title: "Vinculación de una red virtual a un circuito ExpressRoute mediante PowerShell y Azure clásico | Microsoft Docs"
description: "Este documento proporciona información general sobre cómo vincular redes virtuales a circuitos ExpressRoute mediante el modelo de implementación clásica y PowerShell."
services: expressroute
documentationcenter: na
author: ganesr
manager: carmonm
editor: 
tags: azure-service-management
ms.assetid: 9b53fd72-9b6b-4844-80b9-4e1d54fd0c17
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/28/2017
ms.author: ganesr
ms.openlocfilehash: 8df8a4c6ff0897c821e13248e0494b17e1a4992d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="connect-a-virtual-network-to-an-expressroute-circuit-using-powershell-classic"></a>Conexión de una red virtual a un circuito ExpressRoute mediante PowerShell (clásica)
> [!div class="op_single_selector"]
> * [Portal de Azure](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-linkvnet-arm.md)
> * [CLI de Azure](howto-linkvnet-cli.md)
> * [Vídeo: Azure Portal](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (clásico)](expressroute-howto-linkvnet-classic.md)
>

Este artículo le ayudará a vincular redes virtuales a circuitos ExpressRoute de Azure a través del modelo de implementación clásica y PowerShell. Las redes virtuales pueden estar en la misma suscripción o formar parte de otra suscripción.

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]

**Información sobre los modelos de implementación de Azure**

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="configuration-prerequisites"></a>Requisitos previos de configuración
1. Necesitará la versión más reciente de los módulos de Azure PowerShell. Puede descargar los módulos de PowerShell más recientes desde la sección de PowerShell en la [página de descargas de Azure](https://azure.microsoft.com/downloads/). Para obtener instrucciones detalladas sobre cómo configurar el equipo para usar los módulos de Azure PowerShell, siga las instrucciones de la página [Cómo instalar y configurar Azure PowerShell](/powershell/azure/overview) .
2. Debe revisar los [requisitos previos](expressroute-prerequisites.md), los [requisitos de enrutamiento](expressroute-routing.md) y los [flujos de trabajo](expressroute-workflows.md) antes de comenzar la configuración.
3. Tiene que tener un circuito ExpressRoute activo.
   * Siga las instrucciones para [crear un circuito ExpressRoute](expressroute-howto-circuit-classic.md) y haga que el proveedor de conectividad habilite el circuito.
   * Asegúrese de que dispone de un emparejamiento privado de Azure configurado para el circuito. Consulte el artículo de [configuración del enrutamiento](expressroute-howto-routing-classic.md) para obtener instrucciones sobre el enrutamiento.
   * Asegúrese de que el emparejamiento privado de Azure está configurado y el emparejamiento BGP entre la red y Microsoft está activo para habilitar la conectividad de extremo a extremo.
   * Debe crear y aprovisionar totalmente una red virtual y una puerta de enlace de red virtual. Siga las instrucciones para [configurar una red virtual en ExpressRoute](expressroute-howto-vnet-portal-classic.md).

Es posible vincular hasta 10 redes virtuales a un circuito ExpressRoute. Todas las redes virtuales deben estar en la misma región geopolítica. Puede vincular un mayor número de redes virtuales en el circuito ExpressRoute, o redes virtuales que se encuentren en otras regiones geopolíticas, si habilitó el complemento premium de ExpressRoute. Consulte las [preguntas más frecuentes](expressroute-faqs.md) para obtener más detalles sobre el complemento premium.

## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>Conexión de una red virtual en la misma suscripción a un circuito
Puede vincular una red virtual a un circuito ExpressRoute mediante el siguiente cmdlet. Asegúrese de que la puerta de enlace de red virtual se crea y está lista para vincular antes de ejecutar el cmdlet.

    New-AzureDedicatedCircuitLink -ServiceKey "*****************************" -VNetName "MyVNet"
    Provisioned

## <a name="connect-a-virtual-network-in-a-different-subscription-to-a-circuit"></a>Conexión de una red virtual en una suscripción diferente a un circuito
Puede compartir un circuito ExpressRoute entre varias suscripciones. En la ilustración siguiente se muestra un sencillo esquema de cómo funciona el uso compartido de circuitos ExpressRoute entre varias suscripciones.

Cada una de las nubes más pequeñas dentro de la nube de gran tamaño se usa para representar las suscripciones que pertenecen a diferentes departamentos dentro de una organización. Cada departamento de la organización puede usar su propia suscripción para implementar sus servicios, pero los departamentos pueden compartir un único circuito ExpressRoute para volver a conectarse a la red local. Un solo departamento (en este ejemplo: TI) puede ser el propietario del circuito ExpressRoute. Otras suscripciones dentro de la organización pueden usar el circuito ExpressRoute.

> [!NOTE]
> Los cargos de conectividad y ancho de banda de un circuito dedicado recaerán en el propietario del circuito ExpressRoute. Todas las redes virtuales comparten el mismo ancho de banda.
> 
> 

![Conectividad entre suscripciones](./media/expressroute-howto-linkvnet-classic/cross-subscription.png)

### <a name="administration"></a>Administración
El *propietario del circuito* es el administrador o coadministrador de la suscripción en la que se crea el circuito ExpressRoute. El propietario del circuito puede autorizar a los administradores o coadministradores de otras suscripciones (denominados *usuarios del circuito*) para que usen el circuito dedicado de su propiedad. Los usuarios del circuito autorizados para usar el circuito ExpressRoute de la organización pueden vincular la red virtual de su suscripción al circuito ExpressRoute una vez que estén autorizados.

El propietario del circuito tiene la capacidad de modificar y revocar las autorizaciones en cualquier momento. La revocación de una autorización dará como resultado la eliminación de todos los vínculos de la suscripción cuyo acceso se haya revocado.

### <a name="circuit-owner-operations"></a>Operaciones del propietario del circuito

**Creación de una autorización**

El propietario del circuito autoriza a los administradores de otras suscripciones para que usen el circuito especificado. En el ejemplo siguiente, el administrador del circuito (TI de Contoso) permite que el administrador de otra suscripción (Dev-Test), cree un vínculo de hasta 2 redes virtuales al circuito. El administrador de TI de Contoso lo permite especificando el identificador de Microsoft de Dev-Test. El cmdlet no envía correo electrónico al identificador de Microsoft especificado. El propietario del circuito debe notificar de forma explícita al propietario de la otra suscripción que la autorización se ha completado.

    New-AzureDedicatedCircuitLinkAuthorization -ServiceKey "**************************" -Description "Dev-Test Links" -Limit 2 -MicrosoftIds 'devtest@contoso.com'

    Description         : Dev-Test Links
    Limit               : 2
    LinkAuthorizationId : **********************************
    MicrosoftIds        : devtest@contoso.com
    Used                : 0

**Revisión de las autorizaciones**

El propietario del circuito puede revisar todas las autorizaciones emitidas en un circuito concreto ejecutando el siguiente cmdlet:

    Get-AzureDedicatedCircuitLinkAuthorization -ServiceKey: "**************************"

    Description         : EngineeringTeam
    Limit               : 3
    LinkAuthorizationId : ####################################
    MicrosoftIds        : engadmin@contoso.com
    Used                : 1

    Description         : MarketingTeam
    Limit               : 1
    LinkAuthorizationId : @@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
    MicrosoftIds        : marketingadmin@contoso.com
    Used                : 0

    Description         : Dev-Test Links
    Limit               : 2
    LinkAuthorizationId : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&
    MicrosoftIds        : salesadmin@contoso.com
    Used                : 2


**Actualización de autorizaciones**

El propietario del circuito puede modificar las autorizaciones mediante el siguiente cmdlet:

    Set-AzureDedicatedCircuitLinkAuthorization -ServiceKey "**************************" -AuthorizationId "&&&&&&&&&&&&&&&&&&&&&&&&&&&&"-Limit 5

    Description         : Dev-Test Links
    Limit               : 5
    LinkAuthorizationId : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&
    MicrosoftIds        : devtest@contoso.com
    Used                : 0


**Eliminación de autorizaciones**

El propietario del circuito puede revocar o eliminar las autorizaciones al usuario ejecutando el siguiente cmdlet:

    Remove-AzureDedicatedCircuitLinkAuthorization -ServiceKey "*****************************" -AuthorizationId "###############################"


### <a name="circuit-user-operations"></a>Operaciones del usuario del circuito

**Revisión de las autorizaciones**

El usuario del circuito puede revisar las autorizaciones mediante el siguiente cmdlet:

    Get-AzureAuthorizedDedicatedCircuit

    Bandwidth                        : 200
    CircuitName                      : ContosoIT
    Location                         : Washington DC
    MaximumAllowedLinks              : 2
    ServiceKey                       : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Status                           : Enabled
    UsedLinks                        : 0

**Canjear autorizaciones de vínculo**

El usuario del circuito puede ejecutar el siguiente cmdlet para canjear una autorización de vínculo:

    New-AzureDedicatedCircuitLink –servicekey "&&&&&&&&&&&&&&&&&&&&&&&&&&" –VnetName 'SalesVNET1'

    State VnetName
    ----- --------
    Provisioned SalesVNET1

Ejecute este comando en la suscripción recién vinculada para la red virtual:

    New-AzureDedicatedCircuitLink -ServiceKey "*****************************" -VNetName "MyVNet"

## <a name="next-steps"></a>Pasos siguientes
Para obtener más información acerca de ExpressRoute, consulte [P+F de ExpressRoute](expressroute-faqs.md).

