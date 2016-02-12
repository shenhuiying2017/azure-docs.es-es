<properties
   pageTitle="Configuración de un circuito ExpressRoute mediante el Administrador de recursos de Azure y PowerShell | Microsoft Azure"
   description="Este artículo le guiará por los pasos necesarios para crear y aprovisionar un circuito ExpressRoute. También se muestra cómo comprobar el estado, actualizar, o eliminar y desaprovisionar un circuito ExpressRoute."
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
   ms.date="01/26/2016"
   ms.author="cherylmc"/>

# Creación y modificación de un circuito ExpressRoute mediante el Administrador de recursos de Azure y PowerShell

> [AZURE.SELECTOR]
[PowerShell - Classic](expressroute-howto-circuit-classic.md)
[PowerShell - Resource Manager](expressroute-howto-circuit-arm.md)

Este artículo le guiará por los pasos necesarios para crear un circuito ExpressRoute con los cmdlets de PowerShell y el modelo de implementación del Administrador de recursos de Azure. Los siguientes pasos también le mostrarán cómo comprobar el estado, actualizar, o eliminar y desaprovisionar un circuito ExpressRoute.

[AZURE.INCLUDE [vpn-gateway-sm-rm](../../includes/vpn-gateway-sm-rm-include.md)]

## Requisitos previos de configuración

- Necesitará la versión más reciente de los módulos de Azure PowerShell, versión 1.0 o posterior. Siga las instrucciones de la página [Cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md) para obtener instrucciones detalladas sobre cómo configurar el equipo para utilizar los módulos de Azure PowerShell. 
- Asegúrese de que ha revisado la página de [requisitos previos](expressroute-prerequisites.md) y la página de [flujos de trabajo](expressroute-workflows.md) antes de comenzar la configuración.

## Para crear y aprovisionar un circuito ExpressRoute

1. **Importe el módulo de PowerShell para ExpressRoute.**

 	Para comenzar a usar los cmdlets de ExpressRoute, debe instalar el programa de instalación de PowerShell más reciente desde la [Galería de PowerShell](http://www.powershellgallery.com/) e importar los módulos del Administrador de recursos de Azure en la sesión de PowerShell. Deberá ejecutar PowerShell como administrador.

	    Install-Module AzureRM

		Install-AzureRM

	Importe todos los módulos de AzureRM.* dentro del intervalo de versiones semánticas conocidas.

		Import-AzureRM

	También puede importar un módulo determinado en dicho intervalo.
		
		Import-Module AzureRM.Network 

	Inicie sesión en su cuenta.

		Login-AzureRmAccount

	Seleccione la suscripción en la que desea crear un circuito ExpressRoute.
		
		Select-AzureRmSubscription -SubscriptionId "<subscription ID>"
			


2. **Obtenga la lista de proveedores, ubicaciones y anchos de banda admitidos.**

	Antes de crear un circuito ExpressRoute, necesitará una lista de proveedores de conectividad, ubicaciones admitidas y opciones de ancho de banda. El cmdlet de PowerShell *Get-AzureRmExpressRouteServiceProvider* devuelve esta información, que más tarde se usará en otros pasos.

		Get-AzureRmExpressRouteServiceProvider

	Compruebe si aparece su proveedor de conectividad. Tome nota de la siguiente información, la necesitará para crear los circuitos.
	
	- Nombre
	- PeeringLocations
	- BandwidthsOffered

	Ahora está listo para crear un circuito ExpressRoute.

		
3. **Creación de un circuito ExpressRoute.**

	Antes de crear el circuito de ExpressRoute, primero debe crear un grupo de recursos si no tiene ya uno. Para ello, ejecute el siguiente comando.

		New-AzureRmResourceGroup -Name “ExpressRouteResourceGroup” -Location "West US"

	En el ejemplo siguiente se muestra cómo crear un circuito ExpressRoute de 200 Mbps a través de Equinix en Silicon Valley. Si usa otro proveedor y otra configuración, sustituya la información al realizar la solicitud.

	A continuación se muestra un ejemplo de solicitud para una nueva clave de servicio:

		New-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup" -Location "West US" -SkuTier Standard -SkuFamily MeteredData -ServiceProviderName "Equinix" -PeeringLocation "Silicon Valley" -BandwidthInMbps 200

	Asegúrese de que especifica el nivel y la familia adecuados de SKU.
 
	 - El nivel SKU determina si está habilitado el complemento estándar o premium de ExpressRoute. Puede especificar *estándar* para obtener la SKU estándar o *premium* para el complemento de premium.
	 - La familia de SKU determina el tipo de facturación. Puede seleccionar *metereddata* para el plan de datos limitado y *unlimiteddata" para el plan de datos ilimitado. **Nota:** no podrá cambiar el tipo de facturación después de crear el circuito.

	La respuesta contendrá la clave del servicio. Puede obtener una descripción detallada de todos los parámetros ejecutando lo siguiente:

		Get-Help New-AzureRmExpressRouteCircuit -detailed 

4. **Obtenga una lista de todos los circuitos ExpressRoute.**

	Puede ejecutar el comando *Get-AzureRmExpressRouteCircuit* para obtener una lista de todos los circuitos ExpressRoute que haya creado.

		
		Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

	La respuesta tendrá un aspecto similar al siguiente ejemplo:

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


	Puede recuperar esta información en cualquier momento con el cmdlet *Get-AzureRmExpressRouteCircuit*. Si se realiza la llamada sin parámetros, se obtendrá una lista de todos los circuitos. La clave de servicio se mostrará en el campo *ServiceKey*.

		Get-AzureRmExpressRouteCircuit

	La respuesta tendrá un aspecto similar al siguiente ejemplo:

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



	Puede obtener una descripción detallada de todos los parámetros ejecutando lo siguiente:

		Get-Help Get-AzureRmExpressRouteCircuit -detailed 

5. **Envíe la clave de servicio a su proveedor de conectividad para su aprovisionamiento.**

	Cuando se crea un nuevo circuito ExpressRoute, el circuito estará en el siguiente estado:
	
		ServiceProviderProvisioningState : NotProvisioned
		
		CircuitProvisioningState         : Enabled

	*ServiceProviderProvisioningState* proporciona información sobre el estado actual de aprovisionamiento en la parte del proveedor del servicios ,y en Estado se proporciona el estado en Microsoft. Un circuito ExpressRoute tiene que estar en el siguiente estado para poder usarlo.

		ServiceProviderProvisioningState : Provisioned
		
		CircuitProvisioningState         : Enabled

	El circuito pasará al estado siguiente cuando el proveedor de conectividad se encuentre en el proceso de habilitarlo.

		ServiceProviderProvisioningState : Provisioned
		
		Status                           : Enabled



6. **Compruebe periódicamente el estado y la condición de la clave del circuito.**

	Esto le permitirá saber cuándo ha habilitado el circuito el proveedor. Una vez configurado el circuito, el parámetro *ServiceProviderProvisioningState* aparecerá como *Aprovisionado*, tal como se muestra en el ejemplo siguiente.

		Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

	La respuesta tendrá un aspecto similar al siguiente ejemplo:

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

7. **Configurar el enrutamiento y vincular una red virtual**

	a. **Cree la configuración de enrutamiento.** Consulte [Creación y modificación del enrutamiento de un circuito ExpressRoute](expressroute-howto-routing-arm.md) para obtener instrucciones paso a paso. Tenga en cuenta que estas instrucciones para el enrutamiento se aplican solo a circuitos creados con proveedores de servicios que ofrecen servicios de conectividad de capa 2. Si usa un proveedor de servicios que ofrece servicios administrados de nivel 3 (normalmente IPVPN, como MPLS), el mismo proveedor de conectividad configurará y administrará el enrutamiento. En estos casos no podrá crear ni administrar las configuraciones entre pares.
	
	b. **Vincule una red virtual a un circuito ExpressRoute.** Después de comprobar que se ha configurado un enrutamiento, debe vincular la red virtual a su circuito ExpressRoute. Consulte [Vinculación de la red virtual a circuitos ExpressRoute](expressroute-howto-linkvnet-arm.md) para obtener instrucciones paso a paso.

##  Obtención del estado de un circuito ExpressRoute

Puede recuperar esta información en cualquier momento con el cmdlet *Get-AzureRmExpressRouteCircuit*. Si se realiza la llamada sin parámetros, se obtendrá una lista de todos los circuitos.

		Get-AzureRmExpressRouteCircuit

La respuesta será similar al siguiente ejemplo:

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

Se puede obtener información sobre un circuito ExpressRoute específico si se pasa el nombre del grupo de recursos y el nombre del circuito como parámetro a la llamada.

		Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"


La respuesta tendrá un aspecto similar al siguiente ejemplo:

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

Puede obtener una descripción detallada de todos los parámetros ejecutando lo siguiente:

		Get-Help Get-azurededicatedcircuit -detailed 

## Modificación de un circuito ExpressRoute

Puede modificar determinadas propiedades de un circuito ExpressRoute sin afectar a la conectividad. Consulte la página [Preguntas más frecuentes sobre ExpressRoute](expressroute-faqs.md) para obtener más información sobre los límites y limitaciones.

Puede modificar la siguiente configuración sin incurrir en tiempo de inactividad:

- Habilitar o deshabilitar el complemento ExpressRoute Premium en un circuito ExpressRoute sin tiempo de inactividad.
- Aumentar el ancho de banda de su circuito ExpressRoute sin tiempo de inactividad.



### Cómo habilitar el complemento ExpressRoute Premium

Puede habilitar el complemento premium de ExpressRoute para el circuito existente mediante el siguiente fragmento de PowerShell:

		$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

		$ckt.Sku.Tier = "Premium"
		$ckt.sku.Name = "Premium_MeteredData"

		Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
	
		
El circuito tendrá ahora las características del complemento ExpressRoute Premium habilitadas. Tenga en cuenta que la facturación de la capacidad del complemento Premium comienza en cuanto el comando se ejecuta correctamente.

### Cómo deshabilitar el complemento ExpressRoute Premium

Puede deshabilitar el complemento de ExpressRoute Premium en el circuito existente. Al deshabilitar el complemento Premium de ExpressRoute, tenga en cuenta las consideraciones siguientes:

- Debe asegurarse de que el número de redes virtuales vinculadas al circuito es inferior a 10 antes de realizar la degradación de Premium a estándar. Si no lo hace, se producirá un error en la solicitud de actualización y se le facturarán las tarifas Premium.
- Tiene que desvincular todas las redes virtuales en otras regiones geopolíticas. Si no lo hace, se producirá un error en la solicitud de actualización y se le factura con las tarifas Premium.
- La tabla de enrutamiento tiene que tener menos de 4.000 rutas para la configuración entre pares privados. Si el tamaño de la tabla de ruta sobrepasa las 4000 rutas, la sesión BGP se anulará y no se volverá a habilitar hasta que el número de prefijos anunciados esté por debajo de 4.000.

Para deshabilitar el complemento Premium, utilice el siguiente ejemplo de cmdlet de PowerShell. Esta operación puede producir un error si está utilizando recursos superiores a lo que está permitido para el circuito estándar.
	
		$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
		
		$ckt.Sku.Tier = "Standard"
		$ckt.sku.Name = "Standard_MeteredData"
		
		Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

### Cómo actualizar el ancho de banda de circuito ExpressRoute

Consulte la página [Preguntas más frecuentes sobre ExpressRoute](expressroute-faqs.md) para conocer las opciones de ancho de banda compatibles con su proveedor. Puede elegir cualquier tamaño **mayor** que el tamaño de su circuito existente sin incurrir en tiempo de inactividad.

>[AZURE.IMPORTANT] No podrá reducir el ancho de banda de un circuito ExpressRoute sin interrupciones. Degradar de ancho de banda requiere que cancele el aprovisionamiento del circuito ExpressRoute y, a continuación, vuelva a aprovisionar un nuevo circuito ExpressRoute.

Cuando haya decidido el tamaño que necesita, puede usar el ejemplo siguiente para cambiar el tamaño del circuito. Después de ejecutar los cmdlets, el circuito habrá cambiado de tamaño en el lado de Microsoft. Debe ponerse en contacto con su proveedor de conectividad para actualizar las configuraciones de su parte para que coincidan con este cambio. Tenga en cuenta que le facturará la opción de ancho de banda actualizada desde este momento.

		$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

		$ckt.ServiceProviderProperties.BandwidthInMbps = 1000

		Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

## Eliminación y desaprovisionamiento de un circuito ExpressRoute

Puede eliminar el circuito ExpressRoute. Al eliminar un circuito ExpressRoute, tenga en cuenta las consideraciones siguientes:

- Para realizar esta operación correctamente, tiene que desvincular todas las redes virtuales de ExpressRoute. Si se produce un error en esta operación compruebe si tiene alguna red virtual vinculada al circuito.

- Si está habilitado el estado de aprovisionamiento del proveedor de servicios del circuito ExpressRoute, el estado cambiará de habilitado a *deshabilitado*. Tiene que cooperar con su proveedor de servicios para desaprovisionar el circuito en su lado. Hasta que el proveedor de servicios finalice la cancelación del aprovisionamiento del circuito y nos envíe una notificación continuaremos reservando recursos y facturándole por ello.

- Si el proveedor de servicios ha desaprovisionado el circuito (el estado de aprovisionamiento del proveedor de servicios está establecido en *no aprovisionado*) antes de ejecutar el cmdlet, cancelaremos el aprovisionamiento del circuito y dejaremos de facturarle.

Para eliminar el circuito ExpressRoute, utilice el siguiente ejemplo de cmdlet de PowerShell.

		Remove-AzureRmExpressRouteCircuit -ResourceGroupName "ExpressRouteResourceGroup" -Name "ExpressRouteARMCircuit"

## Pasos siguientes

Después de haber creado el circuito, asegúrese de hacer lo siguiente:

1.  [Crear y modificar el enrutamiento para el circuito ExpressRoute](expressroute-howto-routing-arm.md)
2.  [Vincular la red virtual a su circuito ExpressRoute](expressroute-howto-linkvnet-arm.md)

<!---HONumber=AcomDC_0204_2016-->