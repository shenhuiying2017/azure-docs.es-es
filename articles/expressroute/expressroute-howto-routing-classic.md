---
title: "Configuración del enrutamiento (emparejamiento) de un circuito ExpressRoute: Azure (clásica) | Microsoft Docs"
description: "Este artículo le guiará por los pasos necesarios para crear y aprovisionar las configuraciones entre pares privados, públicos y de Microsoft de un circuito ExpressRoute. Este artículo también muestra cómo comprobar el estado, actualizar, o eliminar configuraciones entre pares en el circuito."
documentationcenter: na
services: expressroute
author: ganesr
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: a4bd39d2-373a-467a-8b06-36cfcc1027d2
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/21/2017
ms.author: ganesr;cherylmc
ms.openlocfilehash: 37713db70f3ae837edafc997b78b16b121d0a885
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/21/2017
---
# <a name="create-and-modify-peering-for-an-expressroute-circuit-classic"></a>Creación y modificación del emparejamiento de un circuito ExpressRoute (clásica)
> [!div class="op_single_selector"]
> * [Portal de Azure](expressroute-howto-routing-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-routing-arm.md)
> * [CLI de Azure](howto-routing-cli.md)
> * [Vídeo: pares privados](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [Vídeo: pares públicos](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-public-peering-for-your-expressroute-circuit)
> * [Vídeo: emparejamiento de Microsoft](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> * [PowerShell (clásico)](expressroute-howto-routing-classic.md)
> 

Este artículo le guiará por los pasos necesarios para crear y administrar la configuración de enrutamiento para un circuito ExpressRoute con PowerShell y el modelo de implementación clásica. Los siguientes pasos también le mostrarán cómo comprobar el estado, actualizar, o eliminar y desaprovisionar las configuraciones entre pares para un circuito ExpressRoute.

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]

**Información acerca de los modelos de implementación de Azure**

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]


## <a name="configuration-prerequisites"></a>Requisitos previos de configuración
* Tendrá que instalar la versión más reciente de los cmdlets de Azure PowerShell para Service Management (SM). Para obtener más información, consulte [Introducción a los cmdlets de Azure PowerShell](/powershell/azure/overview).  
* Antes de comenzar la configuración, asegúrese de que ha revisado la página de [requisitos previos](expressroute-prerequisites.md), la página de [requisitos de enrutamiento](expressroute-routing.md) y la página de [flujos de trabajo](expressroute-workflows.md).
* Tiene que tener un circuito ExpressRoute activo. Siga las instrucciones para [crear un circuito ExpressRoute](expressroute-howto-circuit-classic.md) y habilite el circuito mediante el proveedor de conectividad antes de continuar. El circuito ExpressRoute debe estar en un estado habilitado y aprovisionado para poder ejecutar los cmdlets que se describen a continuación.

> [!IMPORTANT]
> Estas instrucciones se aplican solo a los circuitos creados con proveedores de servicios que ofrecen servicios de conectividad de capa 2. Si usa un proveedor de servicios que ofrece servicios administrados de nivel 3 (normalmente IPVPN, como MPLS), el mismo proveedor de conectividad configurará y administrará el enrutamiento.
> 
> 

Puede configurar una, dos o las tres configuraciones entre pares (Azure privado, Azure público y Microsoft) para un circuito ExpressRoute. Puede establecer las configuraciones entre pares en cualquier orden. Pero tiene que asegurarse de que completa cada configuración entre pares de una en una.


### <a name="log-in-to-your-azure-account-and-select-a-subscription"></a>Inicie sesión en la cuenta de Azure y seleccione la suscripción.
1. Abra la consola de PowerShell con privilegios elevados y conéctela a su cuenta. Use el siguiente ejemplo para conectarse:

        Login-AzureRmAccount

2. Compruebe las suscripciones para la cuenta.

        Get-AzureRmSubscription

3. Si tiene varias suscripciones, seleccione la que quiera usar.

        Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"

4. A continuación, use el cmdlet siguiente para agregar la suscripción de Azure a PowerShell para el modelo de implementación clásica.

        Add-AzureAccount


## <a name="azure-private-peering"></a>Configuración entre pares privados de Azure
Esta sección proporciona instrucciones sobre cómo crear, obtener, actualizar y eliminar la configuración entre pares privados de Azure para un circuito ExpressRoute. 

### <a name="to-create-azure-private-peering"></a>Creación de un emparejamiento privado de Azure
1. **Importe el módulo de PowerShell para ExpressRoute.**
   
    Tiene que importar los módulos de Azure y ExpressRoute en la sesión de PowerShell para poder usar los cmdlets de ExpressRoute. Ejecute los siguientes comandos para importar los módulos de Azure y ExpressRoute en la sesión de PowerShell.  
   
        Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Azure.psd1'
        Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\ExpressRoute\ExpressRoute.psd1'
2. **Creación de un circuito ExpressRoute.**
   
    Siga las instrucciones para crear un [circuito ExpressRoute](expressroute-howto-circuit-classic.md) y habilite su aprovisionamiento a través del proveedor de conectividad. Si su proveedor de conectividad ofrece servicios administrados de nivel 3, puede solicitarle que habilite la configuración entre pares privados de Azure. En ese caso, no necesita seguir las instrucciones que aparecen en las secciones siguientes. Por otra parte, si su proveedor de conectividad no administra este enrutamiento, una vez que cree el circuito siga las instrucciones siguientes. 
3. **Compruebe el circuito ExpressRoute para asegurarse de que está aprovisionado.**
   
    En primer lugar tiene que comprobar si el circuito ExpressRoute tiene los estados Aprovisionado y Habilitado. Observe el ejemplo siguiente.
   
        PS C:\> Get-AzureDedicatedCircuit -ServiceKey "*********************************"
   
        Bandwidth                        : 200
        CircuitName                      : MyTestCircuit
        Location                         : Silicon Valley
        ServiceKey                       : *********************************
        ServiceProviderName              : equinix
        ServiceProviderProvisioningState : Provisioned
        Sku                              : Standard
        Status                           : Enabled
   
    Asegúrese de que el circuito se muestra como Aprovisionado y Habilitado. Si no es así, colabore con su proveedor de conectividad para obtener el circuito para establecer el estado y la condición necesarios para el circuito.
   
        ServiceProviderProvisioningState : Provisioned
        Status                           : Enabled
4. **Establecimiento de la configuración entre pares privados de Azure para el circuito.**
   
    Asegúrese de que tiene los elementos siguientes antes de continuar con los siguientes pasos:
   
   * Una subred /30 para el vínculo principal. No debe ser parte de ningún espacio de direcciones reservado para redes virtuales.
   * Una subred /30 para el vínculo secundario. No debe ser parte de ningún espacio de direcciones reservado para redes virtuales.
   * Un identificador VLAN válido para establecer esta configuración entre pares. Asegúrese de que ninguna otra configuración entre pares en el circuito usa el mismo identificador de VLAN.
   * Número de sistema autónomo (AS) para la configuración entre pares. Puede usar 2 bytes o 4 bytes como números AS. Puede usar un número AS privado para esta configuración entre pares. Asegúrese de que no usa 65515.
   * Un hash MD5, en caso de que haya decidido usarlo. **Esto es opcional**.
     
    Puede ejecutar el siguiente cmdlet para realizar la configuración entre pares privados de Azure para el circuito.
     
        New-AzureBGPPeering -AccessType Private -ServiceKey "*********************************" -PrimaryPeerSubnet "10.0.0.0/30" -SecondaryPeerSubnet "10.0.0.4/30" -PeerAsn 1234 -VlanId 100
     
    Puede usar el cmdlet siguiente si decide usar un hash MD5.
     
        New-AzureBGPPeering -AccessType Private -ServiceKey "*********************************" -PrimaryPeerSubnet "10.0.0.0/30" -SecondaryPeerSubnet "10.0.0.4/30" -PeerAsn 1234 -VlanId 100 -SharedKey "A1B2C3D4"
     
     > [!IMPORTANT]
     > Asegúrese de especificar su número AS como ASN de configuración entre pares, no como cliente ASN.
     > 
     > 

### <a name="to-view-azure-private-peering-details"></a>Visualización de los detalles del emparejamiento privado
Puede obtener detalles de configuración mediante el siguiente cmdlet

    Get-AzureBGPPeering -AccessType Private -ServiceKey "*********************************"

    AdvertisedPublicPrefixes       : 
    AdvertisedPublicPrefixesState  : Configured
    AzureAsn                       : 12076
    CustomerAutonomousSystemNumber : 
    PeerAsn                        : 1234
    PrimaryAzurePort               : 
    PrimaryPeerSubnet              : 10.0.0.0/30
    RoutingRegistryName            : 
    SecondaryAzurePort             : 
    SecondaryPeerSubnet            : 10.0.0.4/30
    State                          : Enabled
    VlanId                         : 100


### <a name="to-update-azure-private-peering-configuration"></a>Actualización del establecimiento de configuración del emparejamiento privado de Azure
Puede actualizar cualquier parte de la configuración mediante el siguiente cmdlet. En el ejemplo siguiente, se está actualizando el identificador de VLAN del circuito de 100 a 500.

    Set-AzureBGPPeering -AccessType Private -ServiceKey "*********************************" -PrimaryPeerSubnet "10.0.0.0/30" -SecondaryPeerSubnet "10.0.0.4/30" -PeerAsn 1234 -VlanId 500 -SharedKey "A1B2C3D4"

### <a name="to-delete-azure-private-peering"></a>Eliminación del emparejamiento privado de Azure
Puede quitar el establecimiento de configuración entre pares ejecutando el siguiente cmdlet.

> [!WARNING]
> Tiene que asegurarse de que todas las redes virtuales se desvinculan del circuito ExpressRoute antes de ejecutar este cmdlet. 
> 
> 

    Remove-AzureBGPPeering -AccessType Private -ServiceKey "*********************************"


## <a name="azure-public-peering"></a>Configuración entre pares públicos de Azure
Esta sección proporciona instrucciones sobre cómo crear, obtener, actualizar y eliminar la configuración entre pares públicos de Azure para un circuito ExpressRoute.

### <a name="to-create-azure-public-peering"></a>Creación de un emparejamiento público de Azure
1. **Importe el módulo de PowerShell para ExpressRoute.**
   
    Tiene que importar los módulos de Azure y ExpressRoute en la sesión de PowerShell para poder usar los cmdlets de ExpressRoute. Ejecute los siguientes comandos para importar los módulos de Azure y ExpressRoute en la sesión de PowerShell. 
   
        Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Azure.psd1'
        Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\ExpressRoute\ExpressRoute.psd1'
2. **Creación de un circuito ExpressRoute**
   
    Siga las instrucciones para crear un [circuito ExpressRoute](expressroute-howto-circuit-classic.md) y habilite su aprovisionamiento a través del proveedor de conectividad. Si el proveedor de conectividad ofrece servicios administrados de nivel 3, puede solicitarle que habilite la configuración entre pares públicos de Azure. En ese caso, no necesita seguir las instrucciones que aparecen en las secciones siguientes. Por otra parte, si su proveedor de conectividad no administra este enrutamiento, una vez que cree el circuito siga las instrucciones siguientes.
3. **Compruebe el circuito ExpressRoute para asegurarse de que está aprovisionado**
   
    En primer lugar tiene que comprobar si el circuito ExpressRoute tiene los estados Aprovisionado y Habilitado. Observe el ejemplo siguiente.
   
        PS C:\> Get-AzureDedicatedCircuit -ServiceKey "*********************************"
   
        Bandwidth                        : 200
        CircuitName                      : MyTestCircuit
        Location                         : Silicon Valley
        ServiceKey                       : *********************************
        ServiceProviderName              : equinix
        ServiceProviderProvisioningState : Provisioned
        Sku                              : Standard
        Status                           : Enabled
   
    Asegúrese de que el circuito se muestra como Aprovisionado y Habilitado. Si no es así, colabore con su proveedor de conectividad para obtener el circuito para establecer el estado y la condición necesarios para el circuito.
   
        ServiceProviderProvisioningState : Provisioned
        Status                           : Enabled
4. **Establecimiento de la configuración entre pares públicos de Azure para el circuito.**
   
    Asegúrese de que tiene la siguiente información antes de continuar:
   
   * Una subred /30 para el vínculo principal. Tiene que ser un prefijo IPv4 público válido.
   * Una subred /30 para el vínculo secundario. Tiene que ser un prefijo IPv4 público válido.
   * Un identificador VLAN válido para establecer esta configuración entre pares. Asegúrese de que ninguna otra configuración entre pares en el circuito usa el mismo identificador de VLAN.
   * Número de sistema autónomo (AS) para la configuración entre pares. Puede usar 2 bytes o 4 bytes como números AS.
   * Un hash MD5, en caso de que haya decidido usarlo. **Esto es opcional**.
     
    Puede ejecutar el siguiente cmdlet para realizar la configuración entre pares públicos de Azure para el circuito.
     
        New-AzureBGPPeering -AccessType Public -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -PeerAsn 1234 -VlanId 200
     
    Puede usar el cmdlet siguiente si decide usar un hash MD5
     
        New-AzureBGPPeering -AccessType Public -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -PeerAsn 1234 -VlanId 200 -SharedKey "A1B2C3D4"
     
     > [!IMPORTANT]
     > Asegúrese de especificar su número AS como ASN de configuración entre pares y no como cliente ASN.
     > 
     > 

### <a name="to-view-azure-public-peering-details"></a>Visualización de detalles de un emparejamiento público de Azure
Puede obtener detalles de configuración mediante el siguiente cmdlet

    Get-AzureBGPPeering -AccessType Public -ServiceKey "*********************************"

    AdvertisedPublicPrefixes       : 
    AdvertisedPublicPrefixesState  : Configured
    AzureAsn                       : 12076
    CustomerAutonomousSystemNumber : 
    PeerAsn                        : 1234
    PrimaryAzurePort               : 
    PrimaryPeerSubnet              : 131.107.0.0/30
    RoutingRegistryName            : 
    SecondaryAzurePort             : 
    SecondaryPeerSubnet            : 131.107.0.4/30
    State                          : Enabled
    VlanId                         : 200


### <a name="to-update-azure-public-peering-configuration"></a>Actualización del establecimiento de configuración del emparejamiento público de Azure
Puede actualizar cualquier parte de la configuración mediante el siguiente cmdlet

    Set-AzureBGPPeering -AccessType Public -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -PeerAsn 1234 -VlanId 600 -SharedKey "A1B2C3D4"

En el ejemplo anterior se está actualizando el identificador de VLAN del circuito de 200 a 600.

### <a name="to-delete-azure-public-peering"></a>Eliminación del emparejamiento público de Azure
Puede quitar el establecimiento de configuración entre pares ejecutando el siguiente cmdlet

    Remove-AzureBGPPeering -AccessType Public -ServiceKey "*********************************"

## <a name="microsoft-peering"></a>Emparejamiento de Microsoft
Esta sección proporciona instrucciones sobre cómo crear, obtener, actualizar y eliminar el establecimiento de configuración entre pares de Microsoft para un circuito ExpressRoute. 

### <a name="to-create-microsoft-peering"></a>Creación del emparejamiento de Microsoft
1. **Importe el módulo de PowerShell para ExpressRoute.**
   
    Tiene que importar los módulos de Azure y ExpressRoute en la sesión de PowerShell para poder usar los cmdlets de ExpressRoute. Ejecute los siguientes comandos para importar los módulos de Azure y ExpressRoute en la sesión de PowerShell.  
   
        Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Azure.psd1'
        Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\ExpressRoute\ExpressRoute.psd1'
2. **Creación de un circuito ExpressRoute**
   
    Siga las instrucciones para crear un [circuito ExpressRoute](expressroute-howto-circuit-classic.md) y habilite su aprovisionamiento a través del proveedor de conectividad. Si su proveedor de conectividad ofrece servicios administrados de nivel 3, puede solicitarle que habilite la configuración entre pares privados de Azure. En ese caso, no necesita seguir las instrucciones que aparecen en las secciones siguientes. Por otra parte, si su proveedor de conectividad no administra este enrutamiento, una vez que cree el circuito siga las instrucciones siguientes.
3. **Compruebe el circuito ExpressRoute para asegurarse de que está aprovisionado**
   
    En primer lugar tiene que comprobar si el circuito ExpressRoute tiene los estados Aprovisionado y Habilitado.
   
        PS C:\> Get-AzureDedicatedCircuit -ServiceKey "*********************************"
   
        Bandwidth                        : 200
        CircuitName                      : MyTestCircuit
        Location                         : Silicon Valley
        ServiceKey                       : *********************************
        ServiceProviderName              : equinix
        ServiceProviderProvisioningState : Provisioned
        Sku                              : Standard
        Status                           : Enabled
   
    Asegúrese de que el circuito se muestra como Aprovisionado y Habilitado. Si no es así, colabore con su proveedor de conectividad para obtener el circuito para establecer el estado y la condición necesarios para el circuito.
   
        ServiceProviderProvisioningState : Provisioned
        Status                           : Enabled
4. **Establecimiento de la configuración entre pares de Microsoft para el circuito**
   
    Asegúrese de que tiene la siguiente información antes de empezar:
   
   * Una subred /30 para el vínculo principal. Debe ser un prefijo de IPv4 público válido que sea de su propiedad y esté registrado en un Registro regional de Internet (RIR) o un Registro de enrutamiento de Internet (IRR).
   * Una subred /30 para el vínculo secundario. Debe ser un prefijo de IPv4 público válido que sea de su propiedad y esté registrado en un Registro regional de Internet (RIR) o un Registro de enrutamiento de Internet (IRR).
   * Un identificador VLAN válido para establecer esta configuración entre pares. Asegúrese de que ninguna otra configuración entre pares en el circuito usa el mismo identificador de VLAN.
   * Número de sistema autónomo (AS) para la configuración entre pares. Puede usar 2 bytes o 4 bytes como números AS.
   * Prefijos anunciados: tiene que proporcionar una lista de todos los prefijos que planea anunciar en la sesión BGP. Se aceptan solo prefijos de direcciones IP públicas. Puede enviar una lista separada por comas si tiene pensado enviar un conjunto de prefijos. Estos prefijos tienen que estar registrados a su nombre en un Registro regional de Internet (RIR) o un Registro de enrutamiento de Internet (IRR).
   * Cliente ASN: si los prefijos anunciados están registrados en el número AS de configuración entre pares, puede especificar el número de AS en el que están registrados. **Esto es opcional**.
   * Nombre del enrutamiento del Registro: puede especificar el RIR o TIR en el que están registrados el número AS y los prefijos.
   * Un hash MD5, en caso de que haya decidido usarlo. **Esto es opcional.**
     
    Puede ejecutar el siguiente cmdlet para establecer la configuración entre pares de Microsoft para el circuito.
     
        New-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -VlanId 300 -PeerAsn 1234 -CustomerAsn 2245 -AdvertisedPublicPrefixes "123.0.0.0/30" -RoutingRegistryName "ARIN" -SharedKey "A1B2C3D4"

### <a name="to-view-microsoft-peering-details"></a>Visualización de detalles del emparejamiento de Microsoft
Puede obtener detalles sobre la configuración mediante el siguiente cmdlet.

    Get-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************"

    AdvertisedPublicPrefixes       : 123.0.0.0/30
    AdvertisedPublicPrefixesState  : Configured
    AzureAsn                       : 12076
    CustomerAutonomousSystemNumber : 2245
    PeerAsn                        : 1234
    PrimaryAzurePort               : 
    PrimaryPeerSubnet              : 10.0.0.0/30
    RoutingRegistryName            : ARIN
    SecondaryAzurePort             : 
    SecondaryPeerSubnet            : 10.0.0.4/30
    State                          : Enabled
    VlanId                         : 300


### <a name="to-update-microsoft-peering-configuration"></a>Actualización de la configuración de emparejamiento de Microsoft
Puede actualizar cualquier parte de la configuración mediante el siguiente cmdlet.

    Set-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -VlanId 300 -PeerAsn 1234 -CustomerAsn 2245 -AdvertisedPublicPrefixes "123.0.0.0/30" -RoutingRegistryName "ARIN" -SharedKey "A1B2C3D4"

### <a name="to-delete-microsoft-peering"></a>Eliminación del emparejamiento de Microsoft
Puede quitar el establecimiento de configuración entre pares ejecutando el siguiente cmdlet.

    Remove-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************"

## <a name="next-steps"></a>Pasos siguientes
A continuación, [Vinculación de una red virtual a un circuito ExpressRoute](expressroute-howto-linkvnet-classic.md)

* Para obtener más información sobre los flujos de trabajo, consulte [Flujos de trabajo de ExpressRoute](expressroute-workflows.md).
* Para más información sobre el emparejamiento de circuitos, vea [Circuitos y dominios de enrutamiento de ExpressRoute](expressroute-circuit-peerings.md).

