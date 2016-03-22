<properties
   pageTitle="Creación y modificación de un circuito ExpressRoute mediante Resource Manager y PowerShell | Microsoft Azure"
   description="Este artículo describe cómo crear y aprovisionar un circuito ExpressRoute. También muestra cómo comprobar el circuito, actualizarlo, o eliminarlo y desaprovisionarlo."
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="carolz"
   editor=""
   tags="azure-resource-manager"/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/03/2016"
   ms.author="cherylmc"/>

# Creación y modificación de un circuito ExpressRoute mediante Resource Manager y PowerShell

   > [AZURE.SELECTOR]
   [PowerShell - Classic](expressroute-howto-circuit-classic.md)
   [PowerShell - Resource Manager](expressroute-howto-circuit-arm.md)

En este artículo se describe cómo crear un circuito Azure ExpressRoute mediante los cmdlets de Windows PowerShell y el modelo de implementación de Azure Resource Manager. Los siguientes pasos también le mostrarán cómo comprobar el estado del circuito, actualizarlo, o eliminarlo y desaprovisionarlo.

   [AZURE.INCLUDE [vpn-gateway-sm-rm](../../includes/vpn-gateway-sm-rm-include.md)]

## Requisitos previos de configuración

Para crear un circuito ExpressRoute, necesita:

- Obtener la versión más reciente de los módulos de Azure PowerShell, versión 1.0 o posterior. Para obtener instrucciones detalladas sobre cómo configurar el equipo para usar los módulos de Azure PowerShell, siga las instrucciones de la página [Cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md).
- Revise la página [Requisitos previos](expressroute-prerequisites.md) y la página [Flujos de trabajo](expressroute-workflows.md) antes de comenzar la configuración.

## Creación y aprovisionamiento de un circuito ExpressRoute

**Paso 1: Importar el módulo de PowerShell para ExpressRoute.**

Para comenzar a usar los cmdlets de ExpressRoute, debe instalar el programa de instalación de PowerShell más reciente desde la [Galería de PowerShell](http://www.powershellgallery.com/) e importar los módulos de Resource Manager en la sesión de PowerShell. Deberá ejecutar PowerShell como administrador.

```
Install-Module AzureRM

Install-AzureRM
```

Importe todos los módulos de AzureRM dentro del intervalo de versiones semánticas conocidas:

```
Import-AzureRM
```

También puede importar un módulo determinado dentro del intervalo de versiones semánticas conocidas:

```
Import-Module AzureRM.Network
```

Inicie sesión en su cuenta:

```
Login-AzureRmAccount
```

Seleccione la suscripción en la que desea crear un circuito ExpressRoute:

```
Select-AzureRmSubscription -SubscriptionId "<subscription ID>"   			
```

**Paso 2. Obtener la lista de proveedores, ubicaciones y anchos de banda admitidos.**

Antes de crear un circuito ExpressRoute, necesita una lista de proveedores de conectividad, ubicaciones admitidas y opciones de ancho de banda. El cmdlet *Get-AzureRmExpressRouteServiceProvider* de PowerShell devuelve esta información, que más tarde se usará en otros pasos.

```
PS C:\> Get-AzureRmExpressRouteServiceProvider
```

Compruebe si aparece su proveedor de conectividad. Tome nota de lo siguiente, porque lo necesitará más adelante cuando cree un circuito:

- Nombre
- PeeringLocations
- BandwidthsOffered

Ahora está listo para crear un circuito ExpressRoute.

**Paso 3. Crear un circuito ExpressRoute.**

Si todavía no tiene un grupo de recursos, debe crear uno antes de crear ExpressRoute. Para ello, ejecute el siguiente comando:

```
New-AzureRmResourceGroup -Name "ExpressRouteResourceGroup" -Location "West US"
```

En el ejemplo siguiente se muestra cómo crear un circuito ExpressRoute de 200 Mbps a través de Equinix en Silicon Valley. Si usa otro proveedor y otra configuración, sustituya esa información al realizar la solicitud. A continuación se muestra un ejemplo de solicitud para una nueva clave de servicio:

```
New-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup" -Location "West US" -SkuTier Standard -SkuFamily MeteredData -ServiceProviderName "Equinix" -PeeringLocation "Silicon Valley" -BandwidthInMbps 200
```

Asegúrese de que especifica el nivel y la familia correctos de SKU.

- El nivel de SKU determina si está habilitado un complemento estándar o premium de ExpressRoute. Puede especificar *estándar* para obtener la SKU estándar o *premium* para el complemento premium.
- La familia de SKU determina el tipo de facturación. Puede seleccionar *metereddata* para el plan de datos limitado y *unlimiteddata* para el plan de datos ilimitado. **Nota:** Después de crear un circuito, no podrá cambiar el tipo de facturación.

La respuesta contiene la clave del servicio. Puede obtener una descripción detallada de todos los parámetros ejecutando lo siguiente:

```
get-help New-AzureRmExpressRouteCircuit -detailed
```

**Paso 4: Obtener una lista de todos los circuitos ExpressRoute.**

Para obtener una lista de todos los circuitos ExpressRoute creados, ejecute el comando *Get-AzureRmExpressRouteCircuit*:

```
#Getting service key
Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
```

La respuesta será similar al ejemplo siguiente:

```
Name                             : ExpressRouteARMCircuit
ResourceGroupName                : ExpressRouteResourceGroup
Location                         : westus
Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
Etag                             : W/"################################"
ProvisioningState                : Succeeded
Sku                              : {
                                     "Name": "Standard_MeteredData",
                                     "Tier": "Standard",
                                     "Family": "MeteredData"
   		                           }
CircuitProvisioningState          : Enabled
ServiceProviderProvisioningState  : NotProvisioned
ServiceProviderNotes              :
ServiceProviderProperties         : {
                                      "ServiceProviderName": "Equinix",
                                      "PeeringLocation": "Silicon Valley",
                                      "BandwidthInMbps": 200
                                    }
ServiceKey                        : **************************************
Peerings                          : []
```

Puede recuperar esta información en cualquier momento mediante el cmdlet *Get-AzureRmExpressRouteCircuit*. Si se realiza la llamada sin parámetros, se obtendrá una lista de todos los circuitos. La clave de servicio se mostrará en el campo *ServiceKey*.

```
Get-AzureRmExpressRouteCircuit
```

La respuesta será similar al ejemplo siguiente:

```
Name                             : ExpressRouteARMCircuit
ResourceGroupName                : ExpressRouteResourceGroup
Location                         : westus
Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
Etag                             : W/"################################"
ProvisioningState                : Succeeded
Sku                              : {
                                     "Name": "Standard_MeteredData",
                                     "Tier": "Standard",
                                     "Family": "MeteredData"
   		                           }
CircuitProvisioningState         : Enabled
ServiceProviderProvisioningState : NotProvisioned
ServiceProviderNotes             :
ServiceProviderProperties        : {
                                     "ServiceProviderName": "Equinix",
                                     "PeeringLocation": "Silicon Valley",
                                     "BandwidthInMbps": 200
   		                           }
ServiceKey                       : **************************************
Peerings                         : []
```

Puede obtener una descripción detallada de todos los parámetros ejecutando lo siguiente:

```
get-help Get-AzureRmExpressRouteCircuit -detailed
```

**Paso 5: Enviar la clave de servicio al proveedor de conectividad para aprovisionamiento.**

Cuando se crea un nuevo circuito ExpressRoute, dicho circuito estará en el siguiente estado:

```
ServiceProviderProvisioningState : NotProvisioned

CircuitProvisioningState         : Enabled
```

*ServiceProviderProvisioningState* proporciona información sobre el estado actual de aprovisionamiento en el lado del proveedor de servicios y Status proporciona el estado en el lado de Microsoft. Para poder usar un circuito ExpressRoute, dicho circuito tiene que estar en el siguiente estado.

```
ServiceProviderProvisioningState : Provisioned

CircuitProvisioningState         : Enabled
```

El circuito cambiará al estado siguiente cuando el proveedor de conectividad se encuentre en el proceso de habilitarlo:

```
ServiceProviderProvisioningState : Provisioned

Status                           : Enabled
```

**Paso 6. Comprobar periódicamente el estado y la condición de la clave del circuito.**

La comprobación del estado y la condición de la clave de circuito le informa cuando el proveedor ha habilitado el circuito. Después de configurar el circuito, *ServiceProviderProvisioningState* aparece como *Provisioned* (aprovisionado), tal como se muestra en el ejemplo siguiente.

```
Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
```

La respuesta será similar al ejemplo siguiente:

```
Name                             : ExpressRouteARMCircuit
ResourceGroupName                : ExpressRouteResourceGroup
Location                         : westus
Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
Etag                             : W/"################################"
ProvisioningState                : Succeeded
Sku                              : {
                                     "Name": "Standard_MeteredData",
                                     "Tier": "Standard",
                                     "Family": "MeteredData"
                                   }
CircuitProvisioningState         : Enabled
ServiceProviderProvisioningState : Provisioned
ServiceProviderNotes             :
ServiceProviderProperties        : {
                                     "ServiceProviderName": "Equinix",
                                     "PeeringLocation": "Silicon Valley",
                                     "BandwidthInMbps": 200
   	                            }
ServiceKey                       : **************************************
Peerings                         : []

```

**Paso 7. Cree la configuración de enrutamiento.**

Consulte la página [Creación y modificación del enrutamiento de un circuito ExpressRoute mediante PowerShell](expressroute-howto-routing-arm.md) para obtener instrucciones paso a paso.

**Paso 8. Vincule una red virtual a un circuito ExpressRoute.**

A continuación, vincule una red virtual a su circuito ExpressRoute. Puede usar [esta plantilla](https://github.com/Azure/azure-quickstart-templates/tree/ecad62c231848ace2fbdc36cbe3dc04a96edd58c/301-expressroute-circuit-vnet-connection) cuando se trabaja con el modo de implementación del Administrador de recursos. Actualmente estamos trabajando en los pasos para completar esta tarea con PowerShell.

## Obtención del estado de un circuito ExpressRoute

Puede recuperar esta información en cualquier momento mediante el cmdlet *Get-AzureRmExpressRouteCircuit*. Si se realiza la llamada sin parámetros, se obtendrá una lista de todos los circuitos.

```
Get-AzureRmExpressRouteCircuit
```

La respuesta será similar al siguiente ejemplo:

```
Name                             : ExpressRouteARMCircuit
ResourceGroupName                : ExpressRouteResourceGroup
Location                         : westus
Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
Etag                             : W/"################################"
ProvisioningState                : Succeeded
Sku                              : {
                                     "Name": "Standard_MeteredData",
                                     "Tier": "Standard",
                                     "Family": "MeteredData"
                                   }
CircuitProvisioningState         : Enabled
ServiceProviderProvisioningState : Provisioned
ServiceProviderNotes             :
ServiceProviderProperties        : {
   		                             "ServiceProviderName": "Equinix",
   		                             "PeeringLocation": "Silicon Valley",
   		                             "BandwidthInMbps": 200
   		                           }
ServiceKey                       : **************************************
Peerings                         : []
```

Se puede obtener información sobre un circuito ExpressRoute específico si se pasa el nombre del grupo de recursos y el nombre del circuito como parámetro a la llamada:

```
Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
```

La respuesta será similar al ejemplo siguiente:

```
Name                             : ExpressRouteARMCircuit
ResourceGroupName                : ExpressRouteResourceGroup
Location                         : westus
Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
Etag                             : W/"################################"
ProvisioningState                : Succeeded
Sku                              : {
                                     "Name": "Standard_MeteredData",
   		                             "Tier": "Standard",
   		                             "Family": "MeteredData"
   		                           }
CircuitProvisioningState         : Enabled
ServiceProviderProvisioningState : Provisioned
ServiceProviderNotes             :
ServiceProviderProperties        : {
                                     "ServiceProviderName": "Equinix",
                                     "PeeringLocation": "Silicon Valley",
                                     "BandwidthInMbps": 200
   		                           }
ServiceKey                       : **************************************
Peerings                         : []
```

Puede obtener una descripción detallada de todos los parámetros ejecutando lo siguiente:

```
get-help get-azurededicatedcircuit -detailed
```

## Modificación de un circuito ExpressRoute

Puede modificar determinadas propiedades de un circuito ExpressRoute sin afectar a la conectividad.

Puede hacer lo siguiente sin experimentar tiempo de inactividad:

- Habilitar o deshabilitar el complemento ExpressRoute Premium en su circuito ExpressRoute.
- Aumentar el ancho de banda de su circuito ExpressRoute.

Consulte la página [P+F de ExpressRoute](expressroute-faqs.md) para obtener más información sobre los límites y las limitaciones.

### Habilitación del complemento ExpressRoute Premium

Puede habilitar el complemento ExpressRoute Premium para el circuito existente mediante el siguiente fragmento de PowerShell:

```
$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.Sku.Tier = "Premium"
$ckt.sku.Name = "Premium_MeteredData"

Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

El circuito tendrá ahora las características del complemento ExpressRoute Premium habilitadas. Tenga en cuenta que Microsoft comenzará a facturarle por la funcionalidad del complemento Premium en cuanto el comando se ejecute correctamente.

### Deshabilitación del complemento ExpressRoute Premium

Puede deshabilitar el complemento ExpressRoute Premium en el circuito existente mediante el siguiente cmdlet de PowerShell:

```
$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.Sku.Tier = "Standard"
$ckt.sku.Name = "Standard_MeteredData"

Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

El complemento Premium ahora está deshabilitado para el circuito.

Tenga en cuenta que esta operación puede producir un error si está usando recursos superiores a lo que está permitido para el circuito estándar.

- Debe asegurarse de que el número de redes virtuales vinculadas al circuito es inferior a 10 antes de realizar la degradación de Premium a estándar. Si no lo hace, se producirá un error en la solicitud de actualización y Microsoft le facturará con las tarifas de nivel Premium.
- Tiene que desvincular todas las redes virtuales en otras regiones geopolíticas. Si no lo hace, se producirá un error en la solicitud de actualización y Microsoft le facturará con las tarifas de nivel Premium.
- La tabla de enrutamiento tiene que tener menos de 4.000 rutas para el emparejamiento entre pares privados. Si el tamaño de la tabla de ruta sobrepasa las 4.000 rutas, la sesión BGP se anulará y no se volverá a habilitar hasta que el número de prefijos anunciados esté por debajo de 4.000.

### Actualización del ancho de banda del circuito ExpressRoute

Consulte la página [P+F de ExpressRoute](expressroute-faqs.md) para conocer las opciones de ancho de banda compatibles con su proveedor. Puede elegir cualquier tamaño mayor que el tamaño de su circuito existente. Cuando haya decidido el tamaño que necesita, use el comando siguiente para cambiar el tamaño del circuito:

```
$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.ServiceProviderProperties.BandwidthInMbps = 1000

Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

El circuito se cambiará de tamaño en el lado de Microsoft. Así pues, debe ponerse en contacto con su proveedor de conectividad para actualizar las configuraciones de su parte para que coincidan con este cambio. Después de realizar esta notificación, Microsoft comenzará a facturarle por la opción de ancho de banda actualizada.

**Importante**: No podrá reducir el ancho de banda de un circuito ExpressRoute sin interrupciones. Para degradar un ancho de banda, es necesario desaprovisionar el circuito ExpressRoute y luego volver a aprovisionar un nuevo circuito ExpressRoute.

## Eliminación y desaprovisionamiento de un circuito ExpressRoute

Puede eliminar el circuito ExpressRout con la ejecución del siguiente comando:

```
Remove-AzureRmExpressRouteCircuit -ResourceGroupName "ExpressRouteResourceGroup" -Name "ExpressRouteARMCircuit"
```

Tenga en cuenta que para realizar esta operación correctamente tiene que desvincular todas las redes virtuales del circuito ExpressRoute. Si se produce un error en esta operación, compruebe si hay alguna red virtual vinculada al circuito.

Si el estado de aprovisionamiento del proveedor de servicios del circuito ExpressRoute está habilitado, el estado cambiará de habilitado a *deshabilitado*. Tiene que cooperar con su proveedor de servicios para desaprovisionar el circuito en su lado. Microsoft continuará reservando recursos y facturándole por ello hasta que el proveedor de servicios complete el desaprovisionamiento del circuito y nos lo notifique.

Si el proveedor de servicios ha desaprovisionado el circuito (el estado de aprovisionamiento del proveedor de servicios está establecido en *no aprovisionado*) antes de ejecutar el cmdlet anterior, Microsoft cancelará el aprovisionamiento del circuito y dejaremos de facturarle.

## Pasos siguientes

Después de crear el circuito, asegúrese de hacer lo siguiente:

- [Crear y modificar el enrutamiento para el circuito ExpressRoute](expressroute-howto-routing-arm.md)
- [Vincular la red virtual a su circuito ExpressRoute](expressroute-howto-linkvnet-arm.md)

<!---HONumber=AcomDC_0309_2016-->