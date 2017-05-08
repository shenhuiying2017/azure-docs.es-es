---
title: "Creación y modificación de un circuito ExpressRoute mediante Powershell y Azure clásico | Microsoft Docs"
description: "Este artículo le guiará por los pasos necesarios para crear y aprovisionar un circuito ExpressRoute. También se muestra cómo comprobar el estado, actualizar, o eliminar y desaprovisionar un circuito ExpressRoute."
documentationcenter: na
services: expressroute
author: ganesr
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 0134d242-6459-4dec-a2f1-4657c3bc8b23
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/21/2017
ms.author: ganesr;cherylmc
translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: a0ce5cf6a48d3dfd68e1696d93aac7c55a442900
ms.lasthandoff: 04/27/2017


---
# <a name="create-and-modify-an-expressroute-circuit-using-powershell-classic"></a>Creación y modificación de un circuito ExpressRoute mediante PowerShell (clásica)
> [!div class="op_single_selector"]
> * [Resource Manager - Azure Portal](expressroute-howto-circuit-portal-resource-manager.md)
> * [Resource Manager - PowerShell](expressroute-howto-circuit-arm.md)
> * [Clásico: PowerShell](expressroute-howto-circuit-classic.md)
> * [Vídeo: Azure Portal](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> 
>

Este artículo le guiará por los pasos necesarios para crear un circuito ExpressRoute de Azure con los cmdlets de PowerShell y el modelo clásico de implementación. En este artículo, también se muestra cómo comprobar el estado de un circuito ExpressRoute, así como el modo de actualizarlo o eliminarlo y desaprovisionarlo.

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]


**Información acerca de los modelos de implementación de Azure**

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="before-you-begin"></a>Antes de empezar
### <a name="step-1-review-the-prerequisites-and-workflow-articles"></a>Paso 1. Revise los requisitos previos y los artículos de flujo de trabajo.
Asegúrese de haber revisado los [requisitos previos](expressroute-prerequisites.md) y los [flujos de trabajo](expressroute-workflows.md) antes de comenzar la configuración.  

### <a name="step-2-install-the-latest-versions-of-the-azure-service-management-sm-powershell-modules"></a>Paso 2: Instale las versiones más recientes de los módulos de Azure PowerShell para Service Management (SM).
Para obtener instrucciones detalladas sobre cómo configurar el equipo para usar los módulos de Azure PowerShell, siga las indicaciones que aparecen en [Introducción a los cmdlets de PowerShell para Azure](/powershell/azure/overview).

### <a name="step-3-log-in-to-your-azure-account-and-select-a-subscription"></a>Paso 3: Inicie sesión en la cuenta de Azure y seleccione la suscripción.
1. Abra la consola de PowerShell con privilegios elevados y conéctela a su cuenta. Use el siguiente ejemplo para conectarse:

        Login-AzureRmAccount

2. Compruebe las suscripciones para la cuenta.

        Get-AzureRmSubscription

3. Si tiene varias suscripciones, seleccione la que quiera usar.

        Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"

4. A continuación, use el cmdlet siguiente para agregar la suscripción de Azure a PowerShell para el modelo de implementación clásica.

        Add-AzureAccount

## <a name="create-and-provision-an-expressroute-circuit"></a>Creación y aprovisionamiento de un circuito ExpressRoute
### <a name="step-1-import-the-powershell-modules-for-expressroute"></a>Paso 1. Importación del módulo de PowerShell para ExpressRoute
 Si aún no lo ha hecho, tiene que importar los módulos de Azure y ExpressRoute en la sesión de PowerShell para poder usar los cmdlets de ExpressRoute. Importe los módulos de la ubicación donde estaban instaladas en el equipo local. Según el método utilizado para instalar los módulos, la ubicación puede ser diferente a la que se muestra en el ejemplo siguiente. Si es necesario, modifique el ejemplo.  

    Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Azure.psd1'
    Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\ExpressRoute\ExpressRoute.psd1'

### <a name="step-2-get-the-list-of-supported-providers-locations-and-bandwidths"></a>Paso 2: Obtención de la lista de proveedores, ubicaciones y anchos de banda admitidos
Para crear un circuito ExpressRoute, necesita la lista de proveedores de conectividad, ubicaciones y opciones de ancho de banda admitidas.

El cmdlet de PowerShell `Get-AzureDedicatedCircuitServiceProvider` devuelve esta información, que se usará en pasos posteriores:

    Get-AzureDedicatedCircuitServiceProvider

Compruebe si aparece su proveedor de conectividad. Tome nota de la siguiente información, porque la necesitará más adelante cuando cree un circuito:

* Nombre
* PeeringLocations
* BandwidthsOffered

Ahora está listo para crear un circuito ExpressRoute.         

### <a name="step-3-create-an-expressroute-circuit"></a>Paso 3: Creación de un circuito ExpressRoute
En el ejemplo siguiente se muestra cómo crear un circuito ExpressRoute de 200 Mbps a través de Equinix en Silicon Valley. Si usa otro proveedor y otra configuración, sustituya esa información al realizar la solicitud.

> [!IMPORTANT]
> El circuito ExpressRoute se facturará a partir del momento en que se emita una clave de servicio. Asegúrese de realizar esta operación cuando el proveedor de conectividad esté listo para aprovisionar el circuito.
> 
> 

A continuación se muestra un ejemplo de solicitud para una nueva clave de servicio:

    $Bandwidth = 200
    $CircuitName = "MyTestCircuit"
    $ServiceProvider = "Equinix"
    $Location = "Silicon Valley"

    New-AzureDedicatedCircuit -CircuitName $CircuitName -ServiceProviderName $ServiceProvider -Bandwidth $Bandwidth -Location $Location -sku Standard -BillingType MeteredData

O bien, si desea crear un circuito ExpressRoute con el complemento Premium, use el siguiente ejemplo. Consulte la página [P+F de ExpressRoute](expressroute-faqs.md) para más información sobre el complemento Premium.

    New-AzureDedicatedCircuit -CircuitName $CircuitName -ServiceProviderName $ServiceProvider -Bandwidth $Bandwidth -Location $Location -sku Premium - BillingType MeteredData


La respuesta contendrá la clave del servicio. Puede obtener una descripción detallada de todos los parámetros ejecutando lo siguiente:

    get-help new-azurededicatedcircuit -detailed

### <a name="step-4-list-all-the-expressroute-circuits"></a>Paso 4 Lista de todos los circuitos ExpressRoute
Para obtener una lista de todos los circuitos ExpressRoute que haya creado, ejecute el comando `Get-AzureDedicatedCircuit`:

    Get-AzureDedicatedCircuit

La respuesta será similar al siguiente ejemplo:

    Bandwidth                        : 200
    CircuitName                      : MyTestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : NotProvisioned
    Sku                              : Standard
    Status                           : Enabled

Esta información se puede recuperar en cualquier momento con el cmdlet `Get-AzureDedicatedCircuit` . Si se realiza la llamada sin parámetros, se obtendrá una lista de todos los circuitos. La clave de servicio se mostrará en el campo *ServiceKey* .

    Get-AzureDedicatedCircuit

    Bandwidth                        : 200
    CircuitName                      : MyTestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : NotProvisioned
    Sku                              : Standard
    Status                           : Enabled

Puede obtener una descripción detallada de todos los parámetros ejecutando lo siguiente:

    get-help get-azurededicatedcircuit -detailed

### <a name="step-5-send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>Paso 5. Envío de la clave de servicio al proveedor de conectividad para el aprovisionamiento
*ServiceProviderProvisioningState* da información sobre el estado actual del aprovisionamiento en el lado del proveedor de servicios. *Status* proporciona el estado relativo al lado de Microsoft. Para más información sobre los estados de aprovisionamiento del circuito, consulte el artículo [Flujos de trabajo de ExpressRoute para aprovisionamiento de circuitos y estados de circuitos de ExpressRoute](expressroute-workflows.md#expressroute-circuit-provisioning-states) .

Cuando se crea un nuevo circuito ExpressRoute, dicho circuito estará en el siguiente estado:

    ServiceProviderProvisioningState : NotProvisioned
    Status                           : Enabled


El circuito pasará al estado siguiente cuando el proveedor de conectividad se encuentre en el proceso de habilitarlo:

    ServiceProviderProvisioningState : Provisioning
    Status                           : Enabled

Un circuito ExpressRoute tiene que estar en el siguiente estado para poder usarlo:

    ServiceProviderProvisioningState : Provisioned
    Status                           : Enabled


### <a name="step-6-periodically-check-the-status-and-the-state-of-the-circuit-key"></a>Paso 6. Comprobación periódica del estado y la condición de la clave del circuito
Esto le permitirá saber cuándo ha habilitado el circuito el proveedor. Después de configurar el circuito, *ServiceProviderProvisioningState* aparece como *Provisioned*, tal como se muestra en el ejemplo siguiente:

    Get-AzureDedicatedCircuit

    Bandwidth                        : 200
    CircuitName                      : MyTestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Standard
    Status                           : Enabled

### <a name="step-7-create-your-routing-configuration"></a>Paso 7. Creación de la configuración de enrutamiento
Consulte el artículo [Configuración de enrutamiento de circuitos ExpressRoute (crear y modificar emparejamientos de circuito)](expressroute-howto-routing-classic.md) para obtener instrucciones paso a paso.

> [!IMPORTANT]
> Estas instrucciones se aplican solo a los circuitos creados con proveedores de servicios que ofrecen servicios de conectividad de nivel 2. Si usa un proveedor de servicios que ofrece servicios administrados de nivel 3 (normalmente VPN IP, como MPLS), el mismo proveedor de conectividad configurará y administrará el enrutamiento.
> 
> 

### <a name="step-8-link-a-virtual-network-to-an-expressroute-circuit"></a>Paso 8. Vinculación de una red virtual a un circuito ExpressRoute
A continuación, vincule una red virtual a su circuito ExpressRoute. Consulte [Vinculación de una red virtual a un circuito ExpressRoute](expressroute-howto-linkvnet-classic.md) para obtener instrucciones paso a paso. Si necesita crear una red virtual con el modelo de implementación clásica de ExpressRoute, consulte [Creación de una red virtual para ExpressRoute](expressroute-howto-vnet-portal-classic.md).

## <a name="getting-the-status-of-an-expressroute-circuit"></a>Obtención del estado de un circuito ExpressRoute
Esta información se puede recuperar en cualquier momento con el cmdlet `Get-AzureCircuit` . Si se realiza la llamada sin parámetros, se obtendrá una lista de todos los circuitos.

    Get-AzureDedicatedCircuit

    Bandwidth                        : 200
    CircuitName                      : MyTestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Standard
    Status                           : Enabled

    Bandwidth                        : 1000
    CircuitName                      : MyAsiaCircuit
    Location                         : Singapore
    ServiceKey                       : #################################
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Standard
    Status                           : Enabled

Puede obtener información sobre un circuito ExpressRoute específico, pasando la clave de servicio como un parámetro a la llamada.

    Get-AzureDedicatedCircuit -ServiceKey "*********************************"

    Bandwidth                        : 200
    CircuitName                      : MyTestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Standard
    Status                           : Enabled


Puede obtener una descripción detallada de todos los parámetros ejecutando el siguiente ejemplo:

    get-help get-azurededicatedcircuit -detailed

## <a name="modifying-an-expressroute-circuit"></a>Modificación de un circuito ExpressRoute
Puede modificar determinadas propiedades de un circuito ExpressRoute sin afectar a la conectividad.

Puede hacer lo siguiente sin experimentar tiempo de inactividad:

* Habilitar o deshabilitar el complemento ExpressRoute Premium en su circuito ExpressRoute.
* Aumente el ancho de banda del circuito ExpressRoute, siempre que haya capacidad disponible en el puerto. Tenga en cuenta que no se admite la degradación del ancho de banda de un circuito. 
* Cambio del plan de medición de datos limitados a datos ilimitados. Tenga en cuenta que no es posible cambiar el plan de medición de datos ilimitados a datos limitados.
* Puede habilitar y deshabilitar *Allow Classic Operations*(Permitir operaciones clásicas).

Consulte la página [P+F de ExpressRoute](expressroute-faqs.md) para más información sobre los límites y las limitaciones.

### <a name="to-enable-the-expressroute-premium-add-on"></a>Para habilitar el complemento ExpressRoute Premium
Puede habilitar el complemento ExpressRoute Premium en el circuito existente mediante el siguiente cmdlet de PowerShell:

    Set-AzureDedicatedCircuitProperties -ServiceKey "*********************************" -Sku Premium

    Bandwidth                        : 1000
    CircuitName                      : TestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Premium
    Status                           : Enabled

El circuito tendrá ahora las características del complemento ExpressRoute Premium habilitadas. Tenga en cuenta que la facturación de la capacidad del complemento Premium comienza en cuanto el comando se ejecuta correctamente.

### <a name="to-disable-the-expressroute-premium-add-on"></a>Para deshabilitar el complemento ExpressRoute Premium
> [!IMPORTANT]
> Esta operación puede producir un error si usa recursos que son más grandes de lo que está permitido para el circuito estándar.
> 
> 

#### <a name="considerations"></a>Consideraciones

* Debe asegurarse de que el número de redes virtuales vinculadas al circuito es inferior a 10 antes de realizar la degradación de Premium a estándar. Si no lo hace, se producirá un error en la solicitud de actualización y se le facturará con las tarifas Premium.
* Tiene que desvincular todas las redes virtuales en otras regiones geopolíticas. Si no lo hace, se producirá un error en la solicitud de actualización y se le facturará con las tarifas Premium.
* La tabla de enrutamiento tiene que tener menos de 4.000 rutas para el emparejamiento entre pares privados. Si el tamaño de la tabla de ruta sobrepasa las 4.000 rutas, la sesión BGP se anulará y no se volverá a habilitar hasta que el número de prefijos anunciados esté por debajo de 4.000.

#### <a name="disable-the-premium-add-on"></a>Deshabilitamiento del complemento de premium
Puede deshabilitar el complemento ExpressRoute Premium en el circuito existente mediante el siguiente cmdlet de PowerShell:

    Set-AzureDedicatedCircuitProperties -ServiceKey "*********************************" -Sku Standard

    Bandwidth                        : 1000
    CircuitName                      : TestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Standard
    Status                           : Enabled



### <a name="to-update-the-expressroute-circuit-bandwidth"></a>Para actualizar el ancho de banda del circuito ExpressRoute
Consulte la página [P+F de ExpressRoute](expressroute-faqs.md) para conocer las opciones de ancho de banda compatibles con su proveedor. Puede elegir cualquier tamaño que sea mayor que el de su circuito existente siempre que lo permita el puerto físico (en el que se creó el circuito).

> [!IMPORTANT]
> Si el puerto existente no tiene la capacidad adecuada, tendrá que volver a crear el circuito ExpressRoute. El circuito no se puede actualizar si no hay más capacidad disponible en la ubicación.
>
> No podrá reducir el ancho de banda de un circuito ExpressRoute sin interrupciones. Para degradar un ancho de banda, es necesario desaprovisionar el circuito ExpressRoute y luego volver a aprovisionar un nuevo circuito ExpressRoute.
> 
> 

#### <a name="resize-a-circuit"></a>Cambio del tamaño de un circuito

Después de decidir el tamaño que necesita, puede usar el comando siguiente para cambiar el tamaño del circuito:

    Set-AzureDedicatedCircuitProperties -ServiceKey ********************************* -Bandwidth 1000

    Bandwidth                        : 1000
    CircuitName                      : TestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Standard
    Status                           : Enabled

El circuito habrá cambiado de tamaño en el lado de Microsoft. Debe ponerse en contacto con su proveedor de conectividad para actualizar las configuraciones de su parte para que coincidan con este cambio. Tenga en cuenta que le facturará la opción de ancho de banda actualizada desde este momento.

Si ve el siguiente error al aumentar el ancho de banda de circuito, significa que no queda suficiente ancho de banda en el puerto físico donde se creó el circuito existente. Tendrá que eliminar el circuito y crear uno nuevo del tamaño que necesite. 

    Set-AzureDedicatedCircuitProperties : InvalidOperation : Insufficient bandwidth available to perform this circuit
    update operation
    At line:1 char:1
    + Set-AzureDedicatedCircuitProperties -ServiceKey ********************* ...
    + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
        + CategoryInfo          : CloseError: (:) [Set-AzureDedicatedCircuitProperties], CloudException
        + FullyQualifiedErrorId : Microsoft.WindowsAzure.Commands.ExpressRoute.SetAzureDedicatedCircuitPropertiesCommand


## <a name="deprovisioning-and-deleting-an-expressroute-circuit"></a>Desaprovisionamiento y eliminación de un circuito ExpressRoute

### <a name="considerations"></a>Consideraciones

* Para realizar esta operación correctamente, tiene que desvincular todas las redes virtuales del circuito ExpressRoute. Si se produce un error en esta operación compruebe si tiene alguna red virtual vinculada al circuito.
* Si el estado de aprovisionamiento del proveedor de servicios del circuito ExpressRoute es **Aprovisionando** o **Aprovisionado**, debe colaborar con su proveedor de servicios para que desaprovisionen el circuito. Se le continuará reservando recursos y facturándole por ello hasta que el proveedor de servicios complete el desaprovisionamiento del circuito y nos lo notifique.
* Si el proveedor de servicios ha desaprovisionado el circuito (el estado de aprovisionamiento del proveedor de servicios está establecido en **No aprovisionado**), puede eliminar el circuito. Esto detendrá la facturación del circuito.

#### <a name="delete-a-circuit"></a>Eliminación de un circuito

Puede eliminar el circuito ExpressRout con la ejecución del siguiente comando:

    Remove-AzureDedicatedCircuit -ServiceKey "*********************************"



## <a name="next-steps"></a>Pasos siguientes
Después de crear el circuito, asegúrese de hacer lo siguiente:

* [Crear y modificar el enrutamiento para el circuito ExpressRoute](expressroute-howto-routing-classic.md)
* [Vincular la red virtual a su circuito ExpressRoute](expressroute-howto-linkvnet-classic.md)


