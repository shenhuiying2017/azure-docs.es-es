<properties 
   pageTitle="Uso compartido de un circuito ExpressRoute entre varias suscripciones | Microsoft Azure"
   description="En este artículo, se explica el uso compartido de un circuito ExpressRoute entre varias suscripciones a Azure."
   services="expressroute"
   documentationCenter="na"
   authors="cherylmc"
   manager="jdial"
   editor="tysonn" />
<tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="06/25/2015"
   ms.author="cherylmc" />

# Uso compartido de un circuito ExpressRoute entre varias suscripciones

Se puede compartir un solo circuito ExpressRoute entre varias suscripciones. En la **ilustración 1**, se muestra un sencillo esquema de cómo funciona el uso compartido de circuitos ExpressRoute entre varias suscripciones.

Cada una de las nubes más pequeñas dentro de la nube de gran tamaño se usa para representar las suscripciones que pertenecen a diferentes departamentos dentro de una organización. Cada departamento de la organización puede usar su propia suscripción para implementar sus servicios, pero puede compartir un único circuito ExpressRoute dedicado para volver a conectarse a la red corporativa. Un solo departamento (en este ejemplo: TI) puede ser el propietario del circuito ExpressRoute dedicado. Los cargos de conectividad y ancho de banda de un circuito dedicado se recaerán en el propietario del circuito dedicado. Otras suscripciones dentro de la organización pueden usar el circuito ExpressRoute.

**Ilustración 1**

![Uso compartido de la suscripción](./media/expressroute-share-circuit/IC766124.png)

## Administración

El *propietario del circuito* es el administrador o coadministrador de la suscripción en la que se crea el circuito ExpressRoute dedicado. El propietario del circuito puede autorizar a los administradores o coadministradores de otras suscripciones (denominados *usuario del circuito* en el diagrama de flujo de trabajo) para que usen el circuito dedicado de su propiedad. Los usuarios del circuito autorizados para usar el circuito dedicado de una organización pueden vincular la red virtual de su suscripción al circuito dedicado una vez que estén autorizados.

El propietario del circuito tiene la capacidad de modificar y revocar las autorizaciones en cualquier momento. La revocación de una autorización dará como resultado la eliminación de todos los vínculos de la suscripción cuyo acceso se haya revocado.

## Flujo de trabajo

1. El propietario del circuito autoriza a los administradores de otras suscripciones para que usen el circuito especificado. En el ejemplo siguiente, el administrador del circuito (TI de Contoso) permite que el administrador de otra suscripción (ventas de Contoso), especificando su identificador de Microsoft (Live ID), cree un vínculo de dos redes virtuales al circuito. El cmdlet no envía correo electrónico al identificador de Microsoft especificado. El propietario del circuito debe notificar de forma explícita al propietario de la otra suscripción que la autorización se ha completado.

		PS C:> New-AzureDedicatedCircuitLinkAuthorization -ServiceKey '6ed7e310-1a02-4261-915f-6ccfedc416f1' -Description 'SalesTeam' -Limit 2 -MicrosoftIds 'salesadmin@contoso.com'
		
		Description         : SalesTeam 
		Limit               : 2 
		LinkAuthorizationId : e2bc2645-6fd4-44a4-94f5-f2e43e6953ed 
		MicrosoftIds        : salesadmin@contoso.com 
		Used                : 0

1. Una vez notificado por el propietario del circuito, el administrador de la suscripción autorizada puede ejecutar el siguiente cmdlet para recuperar la clave de servicio del circuito. En este ejemplo, el administrador de ventas de Contoso debe primero iniciar sesión con el identificador de Microsoft especificado, salesadmin@contoso.com.

		PS C:> Get-AzureAuthorizedDedicatedCircuit
		
		Bandwidth                        : 100
		CircuitName                      : ContosoIT
		Location                         : Washington DC
		MaximumAllowedLinks              : 2
		ServiceKey                       : 6ed7e310-1a02-4261-915f-6ccfedc416f1
		ServiceProviderName              : ###########
		ServiceProviderProvisioningState : Provisioned
		Status                           : Enabled
		UsedLinks                        : 0

1. El administrador de la suscripción autorizada ejecuta el cmdlet siguiente para completar la operación de vínculo.

		PS C:> New-AzureDedicatedCircuitLink –servicekey 6ed7e310-1a02-4261-915f-6ccfedc416f1 –VnetName 'SalesVNET1' 
		
			State VnetName 
			----- -------- 
			Provisioned SalesVNET1

Eso es todo. Ahora, la red virtual de ventas de Contoso en Azure está vinculada a un circuito creado por ContosoIT o de su propiedad.

## Administración de la autorización

El propietario del circuito puede compartir un circuito con hasta 10 suscripciones a Azure. El propietario del circuito puede ver quién está autorizado para el circuito. El propietario puede revocar la autorización en cualquier momento. Cuando el propietario del circuito revoca una autorización, identificada por LinkAuthorizationId, todos los vínculos permitidos bajo esa autorización se eliminarán de inmediato. Las redes virtuales vinculadas perderán la conectividad a la red local a través del circuito ExpressRoute.

	PS C:> Get-AzureDedicatedCircuitLinkAuthorization -ServiceKey: 6ed7e310-1a02-4261-915f-6ccfedc416f1 
	
	Description         : EngineeringTeam 
	Limit               : 3 
	LinkAuthorizationId : cc958457-c8c1-4f16-af09-e7f099da64bf 
	MicrosoftIds        : engadmin@contoso.com 
	Used                : 1 
	
	Description         : MarketingTeam 
	Limit               : 1 
	LinkAuthorizationId : d972726f-c7b9-4658-8598-ad3208ac9348 
	MicrosoftIds        : marketingadmin@contoso.com 
	Used                : 0 
	
	Description         : SalesTeam 
	Limit               : 2 
	LinkAuthorizationId : e2bc2645-6fd4-44a4-94f5-f2e43e6953ed 
	MicrosoftIds        : salesadmin@contoso.com 
	Used                : 2 
	
	PS C:> Remove-AzureDedicatedCircuitLinkAuthorization -ServiceKey '6ed7e310-1a02-4261-915f-6ccfedc416f1' -AuthorizationId 'e2bc2645-6fd4-44a4-94f5-f2e43e6953ed'


En el diagrama siguiente se muestra el flujo de trabajo de autorización:

![Flujo de trabajo del uso compartido de la suscripción](./media/expressroute-share-circuit/IC759525.png)

## Pasos siguientes

Para obtener más información acerca de ExpressRoute, consulte [Información técnica de ExpressRoute](expressroute-introduction.md).

<!---HONumber=July15_HO2-->