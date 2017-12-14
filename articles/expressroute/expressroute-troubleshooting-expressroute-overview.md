---
title: "Comprobación de la conectividad: Guía de solución de problemas de Azure ExpressRoute | Microsoft Docs"
description: "Esta página proporciona instrucciones para la solución de problemas y para validar la conectividad de extremo a extremo de un circuito ExpressRoute."
documentationcenter: na
services: expressroute
author: rambk
manager: tracsman
editor: 
ms.assetid: 
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/26/2017
ms.author: cherylmc
ms.openlocfilehash: e52e53255a1462522f297d8918eb1c347a460f77
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/12/2017
---
# <a name="verifying-expressroute-connectivity"></a>Comprobación de la conectividad de ExpressRoute
ExpressRoute amplía una red local en Microsoft Cloud por medio de una conexión privada que se realiza mediante un proveedor de conectividad, y abarca las siguientes tres zonas de red distintas:

-   Red del cliente
-   Red del proveedor
-   Centro de datos de Microsoft

El propósito de este documento es ayudar a los usuarios a identificar dónde (o incluso si) existe un problema de conectividad y la zona en la que se encuentra, con lo que podrá buscar ayuda del equipo adecuado para solucionar el problema. Si se necesita soporte técnico de Microsoft para resolver un problema, abra una incidencia de soporte técnico con el [soporte técnico de Microsoft][Support].

> [!IMPORTANT]
> Este documento está pensado para ayudarle a diagnosticar y corregir problemas sencillos. No pretende sustituir al soporte técnico de Microsoft. Abra una incidencia de soporte técnico dirigida al [soporte técnico de Microsoft][Support] si no puede resolver el problema siguiendo estas instrucciones.
>
>

## <a name="overview"></a>Información general
El siguiente diagrama muestra la conectividad lógica de una red de cliente a la red de Microsoft mediante ExpressRoute.
[![1]][1]

En el diagrama anterior, los números indican los puntos de red clave. En este artículo a menudo se hace referencia a los puntos de red según su número asociado.

Según el modelo de conectividad de ExpressRoute (ubicación compartida de Exchange en la nube, conexión Ethernet de punto a punto o conectividad universal [IPVPN]), los puntos de red 3 y 4 pueden conmutadores (dispositivos de capa 2). Los puntos de red clave son los siguientes:

1.  Dispositivo de proceso del cliente (por ejemplo, un servidor o equipo)
2.  CE: enrutadores en el lado del cliente 
3.  PE (hacia los CE): enrutadores o conmutadores del lado del proveedor orientados hacia los enrutadores del lado del cliente. Se hace referencia como PE-CE en este documento.
4.  PE (hacia los MSEE): enrutadores o conmutadores del lado del proveedor orientados hacia los MSEE. Se hace referencia como PE-MSEE en este documento.
5.  MSEEs: enrutadores de Microsoft Enterprise Edge (MSEE) ExpressRoute
6.  Puerta de enlace de Virtual Network (VNet)
7.  Dispositivo de proceso de Azure Virtual Network

Si se utilizan los modelos de conectividad de colocación de Exchange para la nube o conexión Ethernet de punto a punto, el enrutador del lado del cliente (2) establecerá un emparejamiento BGP con los MSEE (5). Los puntos de red 3 y 4 todavía existirán, pero serán transparentes de forma parecida a los dispositivos de capa 2.

Si se usa el modelo de conectividad universal (IPVPN), los PE (orientados hacia los MSEE) (4) podrían establecer un emparejamiento BGP con los MSEE (5). Entonces las rutas se propagarían de nuevo a la red del cliente a través de la red del proveedor de servicios IPVPN.

>[!NOTE]
>Para lograr la alta disponibilidad de ExpressRoute, Microsoft requiere un par redundante de sesiones BGP entre los MSEE (5) y los PE-MSEE (4). También se recomienda establecer un par redundante de rutas de red entre la red del cliente y los PE-CE. Sin embargo, en el modelo de conexión universal (IPVPN), solo puede haber un único dispositivo CE (2) conectado a uno o varios PE (3).
>
>

Para validar un circuito ExpressRoute, se realizan los pasos siguientes (con el punto de red indicado por el número asociado):
1. [Validación del aprovisionamiento y el estado del circuito (5)](#validate-circuit-provisioning-and-state)
2. [Validación de que hay al menos un emparejamiento de ExpressRoute configurado (5)](#validate-peering-configuration)
3. [Validación de ARP entre Microsoft y el proveedor de servicios (vínculo entre 4 y 5)](#validate-arp-between-microsoft-and-the-service-provider)
4. [Validación del BGP y las rutas del MSEE (BGP entre 4 y 5, y entre 5 y 6 si hay una red virtual conectada)](#validate-bgp-and-routes-on-the-msee)
5. [Comprobación de las estadísticas de tráfico (tráfico que pasa a través de 5)](#check-the-traffic-statistics)

En el futuro se agregarán más validaciones y comprobaciones, por lo que recomienda realizar una comprobación mensual.

##<a name="validate-circuit-provisioning-and-state"></a>Validación del aprovisionamiento y el estado del circuito
Independientemente del modelo de conectividad, debe crearse un circuito ExpressRoute y, por tanto, una clave de servicio generada para el aprovisionamiento del circuito. El aprovisionamiento de un circuito ExpressRoute establece conexiones redundantes de capa 2 entre los PE-MSEE (4) y los MSEE (5). Para obtener más información acerca de cómo crear, modificar, aprovisionar y comprobar un circuito ExpressRoute, consulte el artículo [Creación y modificación de un circuito ExpressRoute][CreateCircuit].

>[!TIP]
>Una clave de servicio identifica de forma única un circuito ExpressRoute. Esta clave es necesaria para la mayoría de los comandos de Powershell que se mencionan en este documento. Además, si necesita asistencia de Microsoft o de un asociado de ExpressRoute para solucionar un problema de ExpressRoute, especifique la clave de servicio para identificar inmediatamente el circuito.
>
>

###<a name="verification-via-the-azure-portal"></a>Comprobación a través de Azure Portal
En Azure Portal, se puede comprobar el estado de un circuito ExpressRoute seleccionando ![2][2] en el menú de barra de la izquierda y seleccionando después el circuito ExpressRoute. Si selecciona un circuito ExpressRoute que aparezca bajo "Todos los recursos" se abre la hoja de circuito ExpressRoute. En la sección ![3][3] de la hoja se enumera la información esencial de ExpressRoute como se muestra en la siguiente captura de pantalla:

![4][4]    

En Essentials (Fundamentos) de ExpressRoute, *Circuit status* (Estado del circuito) indica el estado del circuito en el lado de Microsoft. *Provider status* (Estado del proveedor) indica si el circuito ha sido aprovisionado o no (*Provisioned/Not provisioned*) en el lado del proveedor de servicios. 

Para que un circuito ExpressRoute sea operativo, el valor de *Circuit status* (Estado del circuito) debe ser *Enabled* (Habilitado) y *Provider status* (Estado del proveedor) debe ser *Provisioned* (Aprovisionado)

>[!NOTE]
>Si el valor de *Circuit status* (Estado del circuito) no es Enabled (Habilitado), póngase en contacto con el [soporte técnico de Microsoft][Support]. Si el valor de *Provider status*(Estado del proveedor) no es Provisioned (Aprovisionado), póngase en contacto con su proveedor de servicios.
>
>

###<a name="verification-via-powershell"></a>Comprobación a través de PowerShell
Para obtener una lista de todos los circuitos ExpressRoute en un grupo de recursos, utilice el siguiente comando:

    Get-AzureRmExpressRouteCircuit -ResourceGroupName "Test-ER-RG"

>[!TIP]
>El nombre del grupo de recursos se puede obtener a través de Azure Portal. Consulte el apartado anterior de este documento y tenga en cuenta que el nombre del grupo de recursos se muestra en la captura de pantalla de ejemplo.
>
>

Para seleccionar un circuito ExpressRoute concreto en un grupo de recursos, utilice el siguiente comando:

    Get-AzureRmExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"

Una respuesta de ejemplo es:

    Name                             : Test-ER-Ckt
    ResourceGroupName                : Test-ER-RG
    Location                         : westus2
    Id                               : /subscriptions/***************************/resourceGroups/Test-ER-RG/providers/***********/expressRouteCircuits/Test-ER-Ckt
    Etag                             : W/"################################"
    ProvisioningState                : Succeeded
    Sku                              : {
                                        "Name": "Standard_UnlimitedData",
                                        "Tier": "Standard",
                                        "Family": "UnlimitedData"
                                        }
    CircuitProvisioningState         : Enabled
    ServiceProviderProvisioningState : Provisioned
    ServiceProviderNotes             : 
    ServiceProviderProperties        : {
                                        "ServiceProviderName": "****",
                                        "PeeringLocation": "******",
                                        "BandwidthInMbps": 100
                                        }
    ServiceKey                       : **************************************
    Peerings                         : []
    Authorizations                   : []

Para confirmar si un circuito ExpressRoute está operativo, preste especial atención a los campos siguientes:

    CircuitProvisioningState         : Enabled
    ServiceProviderProvisioningState : Provisioned

>[!NOTE]
>Si el valor de *CircuitProvisioningState* no es Enabled (Habilitado), póngase en contacto con el [soporte técnico de Microsoft][Support]. Si el valor de *ServiceProviderProvisioningState* no es Provisioned (Aprovisionado), póngase en contacto con su proveedor de servicios.
>
>

###<a name="verification-via-powershell-classic"></a>Comprobación a través de PowerShell (Clásico)
Para obtener una lista de todos los circuitos ExpressRoute de una suscripción, utilice el siguiente comando:

    Get-AzureDedicatedCircuit

Para seleccionar un circuito ExpressRoute concreto, utilice el siguiente comando:

    Get-AzureDedicatedCircuit -ServiceKey **************************************

Una respuesta de ejemplo es:

    andwidth                         : 100
    BillingType                      : UnlimitedData
    CircuitName                      : Test-ER-Ckt
    Location                         : westus2
    ServiceKey                       : **************************************
    ServiceProviderName              : ****
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Standard
    Status                           : Enabled

Para confirmar si un circuito ExpressRoute está operativo, preste especial atención a los campos siguientes: ServiceProviderProvisioningState : Provisioned Status                           : Enabled

>[!NOTE]
>Si el valor de *Status* (Estado) no es Enabled (Habilitado), póngase en contacto con el [soporte técnico de Microsoft][Support]. Si el valor de *ServiceProviderProvisioningState* no es Provisioned (Aprovisionado), póngase en contacto con su proveedor de servicios.
>
>

##<a name="validate-peering-configuration"></a>Validación de la configuración de emparejamiento
Cuando el proveedor de servicios haya completado el aprovisionamiento del circuito ExpressRoute, puede crearse una configuración de enrutamiento a través del circuito ExpressRoute entre los MSEE-PR (4) y los MSEE (5). Cada circuito ExpressRoute puede tener uno, dos o tres contextos de enrutamiento habilitados: emparejamiento privado de Azure (tráfico a las redes privadas virtuales de Azure), emparejamiento público de Azure (tráfico a direcciones IP públicas de Azure) y emparejamiento de Microsoft (tráfico a Office 365 y Dynamics 365). Para obtener más información sobre cómo crear y modificar la configuración de enrutamiento, consulte el artículo [Creación y modificación del enrutamiento de un circuito ExpressRoute][CreatePeering].

###<a name="verification-via-the-azure-portal"></a>Comprobación a través de Azure Portal

>[!NOTE]
>Si el proveedor de servicio proporciona la capa 3 y los emparejamientos están en blanco en el portal, actualice la configuración del circuito mediante el botón de actualización del portal. Esta operación aplicará la configuración de enrutamiento correcta a su circuito. 
>
>

En Azure Portal, puede comprobar el estado de un circuito ExpressRoute seleccionando ![2][2] en el menú de barra de la izquierda y, a continuación, seleccionando el circuito ExpressRoute. Si selecciona un circuito ExpressRoute que aparezca bajo "Todos los recursos" se abre la hoja de circuito ExpressRoute. En la sección ![3][3] de la hoja se enumera la información esencial de ExpressRoute como se muestra en la siguiente captura de pantalla:

![5][5]

Como se indicó en el ejemplo anterior, el contexto de enrutamiento de emparejamiento privado de Azure está habilitado, mientras que los contextos de enrutamiento de emparejamientos públicos de Azure y de emparejamiento de Microsoft no lo están. Un contexto de emparejamiento correctamente habilitado también mostrará las subredes de punto a punto principales y secundarias (necesario para BGP). Las subredes /30 se usan para la dirección IP de interfaz de los MSEE y PE-MSEE. 

>[!NOTE]
>Si no está habilitado un emparejamiento, compruebe si las subredes principales y secundarias asignadas coinciden con la configuración de los PE-MSEE. Si no es así, para cambiar la configuración en los enrutadores MSEE, consulte [Creación y modificación del enrutamiento de un circuito ExpressRoute][CreatePeering]
>
>

###<a name="verification-via-powershell"></a>Comprobación a través de PowerShell
Para obtener los detalles de configuración del emparejamiento privado de Azure, use los siguientes comandos:

    $ckt = Get-AzureRmExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
    Get-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt

Una respuesta de ejemplo para un emparejamiento privado configurado correctamente es:

    Name                       : AzurePrivatePeering
    Id                         : /subscriptions/***************************/resourceGroups/Test-ER-RG/providers/***********/expressRouteCircuits/Test-ER-Ckt/peerings/AzurePrivatePeering
    Etag                       : W/"################################"
    PeeringType                : AzurePrivatePeering
    AzureASN                   : 12076
    PeerASN                    : ####
    PrimaryPeerAddressPrefix   : 172.16.0.0/30
    SecondaryPeerAddressPrefix : 172.16.0.4/30
    PrimaryAzurePort           : 
    SecondaryAzurePort         : 
    SharedKey                  : 
    VlanId                     : 300
    MicrosoftPeeringConfig     : null
    ProvisioningState          : Succeeded

 Un contexto de emparejamiento habilitado correctamente mostrará los prefijos de dirección principal y secundaria en la lista. Las subredes /30 se usan para la dirección IP de interfaz de los MSEE y PE-MSEE.

Para obtener los detalles de configuración del emparejamiento público de Azure, use los siguientes comandos:

    $ckt = Get-AzureRmExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
    Get-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -Circuit $ckt

Para obtener los detalles de configuración del emparejamiento de Microsoft, use los siguientes comandos:

    $ckt = Get-AzureRmExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
    Get-AzureRmExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -Circuit $ckt

Si no se configura un emparejamiento, aparecerá un mensaje de error. Esta es una respuesta de ejemplo de cuando el emparejamiento indicado (emparejamiento público de Azure en este ejemplo) no está configurado en el circuito:

    Get-AzureRmExpressRouteCircuitPeeringConfig : Sequence contains no matching element
    At line:1 char:1
        + Get-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering ...
        + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
            + CategoryInfo          : CloseError: (:) [Get-AzureRmExpr...itPeeringConfig], InvalidOperationException
            + FullyQualifiedErrorId : Microsoft.Azure.Commands.Network.GetAzureExpressRouteCircuitPeeringConfigCommand


<p/>
>[!NOTE]
>Si un emparejamiento no está habilitado, compruebe si las subredes principales y secundarias asignadas coinciden con la configuración del PE-MSEE vinculado. Compruebe también si se usan los *VlanId*, *AzureASN* y *PeerASN* correctos en los MSEE, y si estos valores se asignan a los que se usan en el PE-MSEE vinculado. Si se elige el hash MD5, la clave compartida debe coincidir en el par MSEE y PE-MSEE. Para cambiar la configuración en los enrutadores MSEE, consulte [Creación y modificación del enrutamiento de un circuito ExpressRoute][CreatePeering].  
>
>

### <a name="verification-via-powershell-classic"></a>Comprobación a través de PowerShell (Clásico)
Para obtener los detalles de configuración del emparejamiento privado de Azure, use el siguiente comando:

    Get-AzureBGPPeering -AccessType Private -ServiceKey "*********************************"

Una respuesta de ejemplo de un emparejamiento privado configurado correctamente es:

    AdvertisedPublicPrefixes       : 
    AdvertisedPublicPrefixesState  : Configured
    AzureAsn                       : 12076
    CustomerAutonomousSystemNumber : 
    PeerAsn                        : ####
    PrimaryAzurePort               : 
    PrimaryPeerSubnet              : 10.0.0.0/30
    RoutingRegistryName            : 
    SecondaryAzurePort             : 
    SecondaryPeerSubnet            : 10.0.0.4/30
    State                          : Enabled
    VlanId                         : 100

Un contexto de emparejamiento habilitado correctamente mostrará las subredes de pares principales y secundarias en la lista. Las subredes /30 se usan para la dirección IP de interfaz de los MSEE y PE-MSEE.

Para obtener los detalles de configuración del emparejamiento público de Azure, use los siguientes comandos:

    Get-AzureBGPPeering -AccessType Public -ServiceKey "*********************************"

Para obtener los detalles de configuración del emparejamiento de Microsoft, use los siguientes comandos:

    Get-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************"

>[!IMPORTANT]
>Si el proveedor de servicios ha establecido los emparejamientos de capa 3, esa configuración se sobrescribirá cuando se configuren los emparejamientos de ExpressRoute a través del portal o de PowerShell. Necesitará la asistencia de su proveedor de servicios para restablecer la configuración de emparejamiento del lado de proveedor. Modifique los emparejamientos de ExpressRoute solo si se tiene la certeza de que el proveedor de servicios proporciona únicamente servicios de nivel 2.
>
>

<p/>
>[!NOTE]
>Si un emparejamiento no está habilitado, compruebe si las subredes de pares principales y secundarias asignadas coinciden con la configuración del PE-MSEE vinculado. Compruebe también si se usan los *VlanId*, *AzureAsn* y *PeerAsn* correctos en los MSEE, y si estos valores se asignan a los que se usan en el PE-MSEE vinculado. Para cambiar la configuración en los enrutadores MSEE, consulte [Creación y modificación del enrutamiento de un circuito ExpressRoute][CreatePeering].
>
>

## <a name="validate-arp-between-microsoft-and-the-service-provider"></a>Validación de ARP entre Microsoft y el proveedor de servicios
Esta sección utiliza comandos de PowerShell (clásico). Si ha estado utilizando los comandos de PowerShell de Azure Resource Manager, asegúrese de que tiene acceso de administrador o coadministrador a la suscripción. Para solucionar problemas con los comandos de Azure Resource Manager, vea el documento [Getting ARP tables in the Resource Manager deployment model][ARP] (Obtención de tablas ARP en el modelo de implementación de Resource Manager).

>[!NOTE]
>Para obtener ARP, se pueden usar los comandos de PowerShell tanto de Azure Portal como de Azure Resource Manager. Si se producen errores con los comandos de PowerShell de Azure Resource Manager, los comandos de PowerShell clásico deben funcionar también con circuitos ExpressRoute de Azure Resource Manager.
>
>

Para obtener la tabla ARP del enrutador MSEE principal para el emparejamiento privado, utilice el siguiente comando:

    Get-AzureDedicatedCircuitPeeringArpInfo -AccessType Private -Path Primary -ServiceKey "*********************************"

Ejemplo de respuesta del comando, en un escenario correcto:

    ARP Info:

                 Age           Interface           IpAddress          MacAddress
                 113             On-Prem       10.0.0.1           e8ed.f335.4ca9
                   0           Microsoft       10.0.0.2           7c0e.ce85.4fc9

De forma similar, puede comprobar la tabla ARP desde el MSEE en la ruta de acceso *principal*/*secundaria*, para los emparejamientos *privados*/*públicos*/*de Microsoft*.

En el ejemplo siguiente se muestra que no hay respuesta del comando para un emparejamiento.

    ARP Info:
       
>[!NOTE]
>Si la tabla ARP no tiene direcciones IP de las interfaces que se asignan a direcciones MAC, revise la información siguiente:
>1. Si la primera dirección IP de la subred /30 asignada para el vínculo entre el MSEE-PR y MSEE se utiliza en la interfaz de MSEE-PR. Azure usa siempre la segunda dirección IP para los MSEE.
>2. Compruebe si las etiquetas VLAN de servicio (S-Tag) y el cliente (C-Tag) coinciden en el par MSEE PR y MSEE.
>
>

## <a name="validate-bgp-and-routes-on-the-msee"></a>Validación de BGP y las rutas en el MSEE
Esta sección utiliza comandos de PowerShell (clásico). Si ha estado utilizando los comandos de PowerShell de Azure Resource Manager, asegúrese de que tiene acceso de administrador o coadministrador a la suscripción.

>[!NOTE]
>Para obtener información de BGP, se pueden usar los comandos de PowerShell tanto de Azure Portal como de Azure Resource Manager. Si se producen errores con los comandos de PowerShell de Azure Resource Manager, los comandos de PowerShell clásico deben funcionar también con circuitos ExpressRoute de Azure Resource Manager.
>
>

Para obtener el resumen de la tabla de enrutamiento (vecino BGP) para un determinado contexto de enrutamiento, use el comando siguiente:

    Get-AzureDedicatedCircuitPeeringRouteTableSummary -AccessType Private -Path Primary -ServiceKey "*********************************"

Ejemplo de respuesta:

    Route Table Summary:

            Neighbor                   V                  AS              UpDown         StatePfxRcd
            10.0.0.1                   4                ####                8w4d                  50

Como se muestra en el ejemplo anterior, el comando es útil para determinar durante cuánto tiempo se ha establecido el contexto de enrutamiento. También indica el número de prefijos de rutas que anuncia el enrutador de emparejamiento.

>[!NOTE]
>Si el estado es activo o en espera, compruebe si las subredes de pares principales y secundarias asignadas coinciden con la configuración de los PE-MSEE vinculados. Compruebe también si se usan los *VlanId*, *AzureAsn* y *PeerAsn* correctos en los MSEE, y si estos valores se asignan a los que se usan en el PE-MSEE vinculado. Si se elige el hash MD5, la clave compartida debe coincidir en el par MSEE y PE-MSEE. Para cambiar la configuración en los enrutadores MSEE, consulte [Creación y modificación del enrutamiento de un circuito ExpressRoute][CreatePeering].
>
>

<p/>
>[!NOTE]
>Si determinados destinos no son accesibles a través de un emparejamiento determinado, compruebe la tabla de rutas de los MSEE que pertenecen a ese contexto de emparejamiento concreto. Si se encuentra un prefijo coincidente (podría ser IP en NAT) en la tabla de enrutamiento, compruebe si hay firewall/NSG/ACL en la ruta de acceso y si permiten el tráfico.
>
>

Para obtener la tabla de enrutamiento completa de MSEE en la ruta de acceso *principal* de ese contexto de enrutamiento *privado*, use el siguiente comando:

    Get-AzureDedicatedCircuitPeeringRouteTableInfo -AccessType Private -Path Primary -ServiceKey "*********************************"

Ejemplo de un resultado correcto del comando:

    Route Table Info:

             Network             NextHop              LocPrf              Weight                Path
         10.1.0.0/16            10.0.0.1                                       0    #### ##### #####     
         10.2.0.0/16            10.0.0.1                                       0    #### ##### #####
    ...

De forma similar, puede comprobar la tabla de enrutamiento desde el MSEE en la ruta de acceso *principal*/*secundaria*, para el contexto de emparejamientos *privados*/*públicos*/*de Microsoft*.

En el ejemplo siguiente se muestra que no hay respuesta del comando para un emparejamiento:

    Route Table Info:

##<a name="check-the-traffic-statistics"></a>Comprobación de las estadísticas de tráfico
Para obtener las estadísticas del tráfico de la ruta de acceso principal y secundaria combinados (bytes de entrada y salida) de un contexto de emparejamiento, use el siguiente comando:

    Get-AzureDedicatedCircuitStats -ServiceKey 97f85950-01dd-4d30-a73c-bf683b3a6e5c -AccessType Private

Ejemplo de resultado del comando:

    PrimaryBytesIn PrimaryBytesOut SecondaryBytesIn SecondaryBytesOut
    -------------- --------------- ---------------- -----------------
         240780020       239863857        240565035         239628474

Ejemplo de resultado del comando para un emparejamiento inexistente:

    Get-AzureDedicatedCircuitStats : ResourceNotFound: Can not find any subinterface for peering type 'Public' for circuit '97f85950-01dd-4d30-a73c-bf683b3a6e5c' .
    At line:1 char:1
    + Get-AzureDedicatedCircuitStats -ServiceKey 97f85950-01dd-4d30-a73c-bf ...
    + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
        + CategoryInfo          : CloseError: (:) [Get-AzureDedicatedCircuitStats], CloudException
        + FullyQualifiedErrorId : Microsoft.WindowsAzure.Commands.ExpressRoute.GetAzureDedicatedCircuitPeeringStatsCommand

## <a name="next-steps"></a>Pasos siguientes
Para obtener más información, consulte los siguientes artículos:

- [Ayuda y soporte técnico de Microsoft][Support]
- [Creación y modificación de un circuito ExpressRoute][CreateCircuit]
- [Creación y modificación del enrutamiento de un circuito ExpressRoute][CreatePeering]

<!--Image References-->
[1]: ./media/expressroute-troubleshooting-expressroute-overview/expressroute-logical-diagram.png "Logical Express Route Connectivity" (Conectividad lógica de Expressroute)
[2]: ./media/expressroute-troubleshooting-expressroute-overview/portal-all-resources.png "Icono All resources"
[3]: ./media/expressroute-troubleshooting-expressroute-overview/portal-overview.png "Icono Overview"
[4]: ./media/expressroute-troubleshooting-expressroute-overview/portal-circuit-status.png "Captura de pantalla de ejemplo de Essentials de ExpressRoute"
[5]: ./media/expressroute-troubleshooting-expressroute-overview/portal-private-peering.png "Captura de pantalla de ejemplo de Essentials de ExpressRoute"

<!--Link References-->
[Support]: https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[CreateCircuit]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-circuit-portal-resource-manager 
[CreatePeering]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-routing-portal-resource-manager
[ARP]: https://docs.microsoft.com/azure/expressroute/expressroute-troubleshooting-arp-resource-manager






