<properties 
   pageTitle="Vinculación de una red virtual a un circuito ExpressRoute a través del modelo de implementación clásica y PowerShell | Microsoft Azure"
   description="Este documento proporciona información general sobre cómo vincular redes virtuales a circuitos ExpressRoute mediante el modelo de implementación clásica y PowerShell."
   services="expressroute"
   documentationCenter="na"
   authors="ganesr"
   manager="carmonm"
   editor=""
   tags="azure-service-management"/>
<tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/14/2016"
   ms.author="ganesr" />

# Vinculación de una red virtual a un circuito ExpressRoute

> [AZURE.SELECTOR]
- [Portal de Azure: Resource Manager](expressroute-howto-linkvnet-portal-resource-manager.md)
- [PowerShell: administrador de recursos](expressroute-howto-linkvnet-arm.md)
- [PowerShell: clásico](expressroute-howto-linkvnet-classic.md)



Este artículo le ayudará a vincular redes virtuales a circuitos ExpressRoute a través del modelo de implementación clásica y PowerShell. Las redes virtuales pueden estar en la misma suscripción o formar parte de otra suscripción.

**Información acerca de los modelos de implementación de Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## Requisitos previos de configuración

1. Necesitará la versión más reciente de los cmdlets de Azure PowerShell. Puede descargar el módulo de PowerShell más reciente desde la sección de PowerShell en la [página de descargas de Azure](https://azure.microsoft.com/downloads/). Siga las instrucciones de la página [Instalación y configuración de Azure PowerShell](../powershell-install-configure.md) para obtener instrucciones detalladas sobre cómo configurar el equipo para usar los módulos de Azure PowerShell. 
2. Asegúrese de haber revisado la página de [requisitos previos](expressroute-prerequisites.md), la página de [requisitos del enrutamiento](expressroute-routing.md) y la página de [flujos de trabajo](expressroute-workflows.md) antes de comenzar la configuración.
3. Tiene que tener un circuito ExpressRoute activo. 
	- Siga las instrucciones para [crear un circuito ExpressRoute](expressroute-howto-circuit-classic.md) y habilite el circuito mediante el proveedor de conectividad. 
	- Asegúrese de que dispone de un emparejamiento privado de Azure configurado para el circuito. Consulte el artículo de [configuración del enrutamiento](expressroute-howto-routing-classic.md) para obtener instrucciones sobre el enrutamiento. 
	- El emparejamiento privado de Azure debe configurarse y el emparejamiento BGP entre la red y Microsoft debe estar activo para habilitar la conectividad de extremo a extremo.

Es posible vincular hasta 10 redes virtuales a un circuito ExpressRoute. Todos los circuitos ExpressRoute deben estar en la misma región geopolítica. Puede vincular un mayor número de redes virtuales en el circuito ExpressRoute si habilitó el complemento premium de ExpressRoute. Consulte las [preguntas más frecuentes](expressroute-faqs.md) para obtener más detalles sobre el complemento premium.

## Conectar una red virtual en la misma suscripción con un circuito

Puede vincular una red virtual a un circuito ExpressRoute mediante el siguiente cmdlet. Asegúrese de que la puerta de enlace de red virtual se crea y está lista para vincular antes de ejecutar el cmdlet.

	New-AzureDedicatedCircuitLink -ServiceKey "*****************************" -VNetName "MyVNet"
	Provisioned

## Conectar una red virtual en una suscripción distinta con un circuito

Se puede compartir un circuito ExpressRoute entre varias suscripciones. En la ilustración siguiente se muestra un sencillo esquema de cómo funciona el uso compartido de circuitos ExpressRoute entre varias suscripciones.

Cada una de las nubes más pequeñas dentro de la nube de gran tamaño se usa para representar las suscripciones que pertenecen a diferentes departamentos dentro de una organización. Cada departamento de la organización puede usar su propia suscripción para implementar sus servicios, pero puede compartir un único circuito ExpressRoute para volver a conectarse a la red local. Un solo departamento (en este ejemplo: TI) puede ser el propietario del circuito ExpressRoute. Otras suscripciones dentro de la organización pueden usar el circuito ExpressRoute.

>[AZURE.NOTE] Los cargos de conectividad y ancho de banda de un circuito dedicado recaerán en el propietario del circuito ExpressRoute. Todas las redes virtuales comparten el mismo ancho de banda.

![Conectividad entre suscripciones](./media/expressroute-howto-linkvnet-classic/cross-subscription.png)

### Administración

El *propietario del circuito* es el administrador o coadministrador de la suscripción en la que se crea el circuito ExpressRoute. El propietario del circuito puede autorizar a los administradores o coadministradores de otras suscripciones (denominados *usuario del circuito* en el diagrama de flujo de trabajo) para que usen el circuito dedicado de su propiedad. Los usuarios del circuito autorizados para usar el circuito ExpressRoute de una organización pueden vincular la red virtual de su suscripción al circuito ExpressRoute una vez que estén autorizados.

El propietario del circuito tiene la capacidad de modificar y revocar las autorizaciones en cualquier momento. La revocación de una autorización dará como resultado la eliminación de todos los vínculos de la suscripción cuyo acceso se haya revocado.

### Operaciones del propietario del circuito 

#### Creación de una autorización
	
El propietario del circuito autoriza a los administradores de otras suscripciones para que usen el circuito especificado. En el ejemplo siguiente, el administrador del circuito (TI de Contoso) permite que el administrador de otra suscripción (Dev-Test), especificando su identificador de Microsoft, cree un vínculo de 2 redes virtuales al circuito. El cmdlet no envía correo electrónico al identificador de Microsoft especificado. El propietario del circuito debe notificar de forma explícita al propietario de la otra suscripción que la autorización se ha completado.

	New-AzureDedicatedCircuitLinkAuthorization -ServiceKey "**************************" -Description "Dev-Test Links" -Limit 2 -MicrosoftIds 'devtest@contoso.com'
		
	Description         : Dev-Test Links 
	Limit               : 2 
	LinkAuthorizationId : ********************************** 
	MicrosoftIds        : devtest@contoso.com 
	Used                : 0

#### Revisión de las autorizaciones

El propietario del circuito puede revisar todas las autorizaciones emitidas en un circuito concreto ejecutando el siguiente cmdlet.

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
	

#### Actualización de autorizaciones

El propietario del circuito puede modificar las autorizaciones mediante el siguiente cmdlet.

	Set-AzureDedicatedCircuitLinkAuthorization -ServiceKey "**************************" -AuthorizationId "&&&&&&&&&&&&&&&&&&&&&&&&&&&&"-Limit 5
		
	Description         : Dev-Test Links 
	Limit               : 5 
	LinkAuthorizationId : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&& 
	MicrosoftIds        : devtest@contoso.com 
	Used                : 0


#### Eliminación de autorizaciones

El propietario del circuito puede revocar o eliminar las autorizaciones al usuario ejecutando el siguiente cmdlet.

	Remove-AzureDedicatedCircuitLinkAuthorization -ServiceKey "*****************************" -AuthorizationId "###############################"


### Operaciones del usuario del circuito

#### Revisión de las autorizaciones

El usuario del circuito puede revisar las autorizaciones mediante el siguiente cmdlet.

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

#### Canjear autorizaciones de vínculo

El usuario del circuito puede ejecutar el siguiente cmdlet para canjear una autorización de vínculo.

	New-AzureDedicatedCircuitLink –servicekey "&&&&&&&&&&&&&&&&&&&&&&&&&&" –VnetName 'SalesVNET1' 
		
	State VnetName 
	----- -------- 
	Provisioned SalesVNET1

## Pasos siguientes

Para obtener más información acerca de ExpressRoute, consulte [P+F de ExpressRoute](expressroute-faqs.md).

<!---HONumber=AcomDC_0427_2016-->