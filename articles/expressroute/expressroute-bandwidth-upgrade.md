<properties 
   pageTitle="Actualizar dinámicamente el ancho de banda de ExpressRoute | Microsoft Azure"
	description="Cómo aumentar dinámicamente el tamaño del ancho de banda de un circuito de ExpressRoute sin tiempo de inactividad."
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

# Actualizar el ancho de banda del circuito de ExpressRoute dinámicamente sin tiempo de inactividad

Puede aumentar el tamaño de un circuito de ExpressRoute sin tiempo de inactividad. Estas instrucciones le ayudarán a actualizar el ancho de banda de un circuito de ExpressRoute mediante PowerShell. Consulte la página [Preguntas más frecuentes sobre ExpressRoute](expressroute-faqs.md) para obtener más información sobre los límites y limitaciones.

##  Requisitos previos de configuración

Antes de comenzar la configuración, compruebe que dispone de los requisitos previos siguientes:

- Una suscripción de Azure
- La versión más reciente de PowerShell de Azure
- Un circuito de ExpressRoute activo que se haya configurado y esté en funcionamiento


##  Configuración de opciones mediante PowerShell

Windows PowerShell es un potente entorno de scripting que puede usar para controlar y automatizar la implementación y la administración de sus cargas de trabajo en Azure. Para obtener más información, consulte la documentación de PowerShell en [MSDN](https://msdn.microsoft.com/library/windowsazure/jj156055.aspx).

1. **Importe el módulo de PowerShell para ExpressRoute.**

	    Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Azure.psd1'
	    Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\ExpressRoute\ExpressRoute.psd1'

2. **Obtener los detalles del circuito de ExpressRoute**

	Puede obtener los detalles de su circuito de ExpressRoute mediante el siguiente Commandlet de PowerShell:
		

    	PS C:\> Get-AzureDedicatedCircuit
	
	Este comando devuelve una lista de todos los circuitos que haya creado en la suscripción. Puede usar el siguiente comando para obtener detalles de un circuito de ExpressRoute concreto si dispone de la clave de servicio:

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


3. **Aumentar el ancho de banda del circuito de ExpressRoute en el lado de Microsoft**
	
	Consulte la página [Preguntas más frecuentes sobre ExpressRoute](expressroute-faqs.md) para conocer las opciones de ancho de banda compatibles con su proveedor. Puede elegir cualquier tamaño mayor que el tamaño de su circuito existente. Cuando haya decidido el tamaño que necesita, puede usar el comando siguiente para cambiar el tamaño del circuito.

		PS C:\> Set-AzureDedicatedCircuitProperties -ServiceKey ********************************* -Bandwidth 1000
		
		Bandwidth                        : 1000
		CircuitName                      : TestCircuit
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : Provisioned
		Sku                              : Standard
		Status                           : Enabled

	El circuito habrá cambiado de tamaño en el lado de Microsoft. Tenga en cuenta que le facturará la opción de ancho de banda actualizada desde este momento.

4. **Aumentar el ancho de banda del circuito de ExpressRoute en el lado del proveedor de servicios**

	Póngase en contacto con su proveedor de conectividad (NSP / EXP) y facilítele la información sobre el ancho de banda actualizado. Siga el proceso de actualización de pedido que le indique su proveedor de servicios para completar la tarea.

 

<!---HONumber=August15_HO9-->