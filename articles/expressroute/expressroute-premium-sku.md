<properties 
   pageTitle="Cómo habilitar o deshabilitar el complemento ExpressRoute Premium | Microsoft Azure"
	description="Cómo habilitar o deshabilitar el complemento ExpressRoute Premium en un circuito de ExpressRoute. ExpressRoute Premium le permite agregar hasta 10.000 rutas para emparejamientos públicos y privados, y hasta 10 redes virtuales a su circuito de ExpressRoute. También puede vincular una red virtual de una región a un circuito de ExpressRoute de otra."
	services="expressroute"
	documentationCenter="na"
	authors="cherylmc"
	manager="carolz"
	editor=""/>
<tags 
   ms.service="expressroute"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="infrastructure-services"
	ms.date="08/25/2015"
	ms.author="cherylmc"/>

# Configurar el complemento ExpressRoute Premium en el circuito de ExpressRoute

ExpressRoute Premium es una colección de características que se enumera a continuación:

 - Límite de tabla de enrutamiento aumentado de 4000 rutas a 10.000 rutas para el intercambio público y privado.
 - Mayor número de redes virtuales (VNets) que puede conectarse al circuito ExpressRoute (el valor predeterminado es 10). 
 - Conectividad global a través de la red principal de Microsoft. Ahora podrá vincular una red virtual en una región geopolítica con un circuito ExpressRoute en otra región. **Ejemplo:** puede vincular una red virtual creada en Europa occidental a un circuito ExpressRoute creado en Silicon Valley.

Consulte la página [Preguntas más frecuentes sobre ExpressRoute](expressroute-faqs.md) para obtener más información sobre el complemento ExpressRoute Premium. Consulte la página [Información de precios](http://azure.microsoft.com/pricing/details/expressroute/) para conocer el costo.

Las instrucciones siguientes le ayudarán a realizar lo siguiente:

- Crear un circuito ExpressRoute con el complemento Premium habilitado.
- Actualizar un circuito ExpressRoute existente para habilitar el complemento Premium.
- Deshabilitar el complemento ExpressRoute Premium en un circuito.


## Para crear un circuito ExpressRoute con las características del complemento Premium habilitadas

###  Antes de empezar

Antes de comenzar la configuración, compruebe que dispone de los requisitos previos siguientes:

- Una suscripción de Azure
- La versión más reciente de PowerShell de Azure

###  1\. Importar el módulo de PowerShell para ExpressRoute

Windows PowerShell es un potente entorno de scripting que puede usar para controlar y automatizar la implementación y la administración de sus cargas de trabajo en Azure. Para obtener más información, consulte la documentación de PowerShell en [MSDN](https://msdn.microsoft.com/library/windowsazure/jj156055.aspx).

Use los cmdlets siguientes para importar el módulo de PowerShell en ExpressRoute:


	    Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Azure.psd1'
	    Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\ExpressRoute\ExpressRoute.psd1'


### 2\. Configurar un circuito ExpressRoute con las características del complemento Premium habilitadas

Puede crear un nuevo circuito de ExpressRoute con el complemento Premium habilitado en el momento de la creación. Siga las instrucciones sobre cómo crear circuitos ExpressRoute con [NSP](expressroute-configuring-nsps.md) o [EXP](expressroute-configuring-exps.md). Tenemos un nuevo parámetro opcional en el cmdlet New-AzureDedicatedCircuit que le permite especificar la SKU. La SKU puede ser Standard o Premium. El valor predeterminado es standard. Si se pasa la SKU como Premium, se habilitará el circuito con las características del complemento Premium.


		New-AzureDedicatedCircuit -CircuitName $CircuitName -ServiceProviderName $ServiceProvider -Bandwidth $Bandwidth -Location $Location -Sku Premium


### 3\. Comprobar que el complemento ExpressRoute Premium está habilitado
Puede comprobar si el complemento de ExpressRoute Premium está habilitado en el circuito. En el ejemplo siguiente, el circuito de ExpressRoute no tiene las características del complemento ExpressRoute Premium habilitadas. La SKU aparecerá como ***Premium*** si el complemento está habilitado.

		PS C:\> Get-AzureDedicatedCircuit -ServiceKey *********************************

		Bandwidth                        : 200
		CircuitName                      : TestCircuit
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : Provisioned
		Sku                              : Standard
		Status                           : Enabled




## Para habilitar el complemento ExpressRoute Premium en un circuito de ExpressRoute existente
Puede habilitar las características del complemento ExpressRoute Premium en cualquier circuito ExpressRoute que ya haya creado.


1. **Obtener detalles del circuito de ExpressRoute**

	Puede obtener detalles de su circuito de ExpressRoute mediante el siguiente cmdlet de PowerShell:
		

    	PS C:\> Get-AzureDedicatedCircuit
	
	Este comando devuelve una lista de todos los circuitos que haya creado en la suscripción. Puede usar el siguiente comando para obtener detalles de un circuito de ExpressRoute concreto si dispone de la clave de servicio

		 PS C:\> Get-AzureDedicatedCircuit -ServiceKey <skey>

	Reemplace <skey> por la clave de servicio real.
	
		PS C:\> Get-AzureDedicatedCircuit -ServiceKey *********************************

		Bandwidth                        : 200
		CircuitName                      : TestCircuit
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : Provisioned
		Sku                              : Standard
		Status                           : Enabled


2. **Habilitar el complemento ExpressRoute Premium en el circuito**


	Puede habilitar el complemento ExpressRoute Premium en el circuito existente mediante el siguiente cmdlet de PowerShell:
	
		PS C:\> Set-AzureDedicatedCircuitProperties -ServiceKey "*********************************" -Sku Premium
		
		Bandwidth                        : 1000
		CircuitName                      : TestCircuit
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : Provisioned
		Sku                              : Premium
		Status                           : Enabled

	El circuito tendrá ahora las características del complemento ExpressRoute Premium habilitadas. Tenga en cuenta que la facturación de la capacidad del complemento Premium comienza en cuanto el comando se ejecuta correctamente.


## Para deshabilitar el complemento ExpressRoute Premium en un circuito de ExpressRoute

Puede deshabilitar el complemento ExpressRoute Premium en un circuito de ExpressRoute que tenga el complemento habilitado.

**Nota**: Debe asegurarse de que se hayan dejado de usar las características enumeradas en la lista de características del complemento Premium antes de hacerlo. La solicitud para deshabilitar el complemento Premium no se podrá realizar si tiene más de 10 redes virtuales vinculadas al circuito. También notará que anularemos nuestras sesiones BGP si nos anuncia más de 5000 rutas cuando complemento Premium esté deshabilitado.

1. **Obtener los detalles del circuito de ExpressRoute**

	Puede obtener detalles de su circuito de ExpressRoute mediante el siguiente cmdlet de PowerShell:
		

    	PS C:\> Get-AzureDedicatedCircuit
	
	Este comando devuelve una lista de todos los circuitos que haya creado en la suscripción. Puede usar el siguiente comando para obtener detalles de un circuito de ExpressRoute concreto si dispone de la clave de servicio

		 PS C:\> Get-AzureDedicatedCircuit -ServiceKey <skey>

	Reemplace <skey> por la clave de servicio real.
	
		PS C:\> Get-AzureDedicatedCircuit -ServiceKey *********************************

		Bandwidth                        : 200
		CircuitName                      : TestCircuit
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : Provisioned
		Sku                              : Premium
		Status                           : Enabled


3. **Deshabilitar el complemento ExpressRoute Premium en el circuito**


	Puede deshabilitar el complemento ExpressRoute Premium en el circuito existente mediante el siguiente cmdlet de PowerShell:
	
		PS C:\> Set-AzureDedicatedCircuitProperties -ServiceKey "*********************************" -Sku Standard
		
		Bandwidth                        : 1000
		CircuitName                      : TestCircuit
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : Provisioned
		Sku                              : Premium
		Status                           : Standard

	El complemento Premium ahora está deshabilitado para el circuito.


 

<!---HONumber=August15_HO9-->