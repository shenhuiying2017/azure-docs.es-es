<properties 
   pageTitle="Vinculación de una red virtual con un circuito ExpressRoute mediante el modelo de implementación de Resource Manager y el Portal de Azure | Microsoft Azure"
   description="En este documento se proporciona información general de cómo vincular redes virtuales a circuitos ExpressRoute."
   services="expressroute"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>
<tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/14/2016"
   ms.author="cherylmc" />

# Vinculación de una red virtual a un circuito ExpressRoute

> [AZURE.SELECTOR]
- [Portal de Azure: Resource Manager](expressroute-howto-linkvnet-portal-resource-manager.md)
- [PowerShell: administrador de recursos](expressroute-howto-linkvnet-arm.md)
- [PowerShell: clásico](expressroute-howto-linkvnet-classic.md)



Este artículo le ayudará a vincular redes virtuales a circuitos ExpressRoute a través del modelo de implementación de Resource Manager y el Portal de Azure. Las redes virtuales pueden estar en la misma suscripción o formar parte de otra suscripción.


**Información acerca de los modelos de implementación de Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## Requisitos previos de configuración

- Asegúrese de haber revisado la página de [requisitos previos](expressroute-prerequisites.md), la página de [requisitos del enrutamiento](expressroute-routing.md) y la página de [flujos de trabajo](expressroute-workflows.md) antes de comenzar la configuración.
- Tiene que tener un circuito ExpressRoute activo. 
	- Siga las instrucciones para [crear un circuito ExpressRoute](expressroute-howto-circuit-arm.md) y habilite el circuito mediante el proveedor de conectividad. 
	
	- Asegúrese de que dispone de un emparejamiento privado de Azure configurado para el circuito. Consulte el artículo de [configuración del enrutamiento](expressroute-howto-routing-portal-resource-manager.md) para obtener instrucciones sobre el enrutamiento.
	
	- El emparejamiento privado de Azure debe configurarse y el emparejamiento BGP entre la red y Microsoft debe estar activo para habilitar la conectividad de extremo a extremo.
	
	- Debe crear y aprovisionar totalmente una red virtual y una puerta de enlace de red virtual. Siga las instrucciones para crear una [puerta de enlace de VPN](../articles/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) (siga solo los pasos 1 a 5).

Es posible vincular hasta 10 redes virtuales a un circuito ExpressRoute. Todos los circuitos ExpressRoute deben estar en la misma región geopolítica. Puede vincular un mayor número de redes virtuales en el circuito ExpressRoute si habilitó el complemento premium de ExpressRoute. Consulte las [preguntas más frecuentes](expressroute-faqs.md) para obtener más detalles sobre el complemento premium.

## Conectar una red virtual en la misma suscripción con un circuito


### Para crear una conexión

1. Asegúrese de que el circuito ExpressRoute y el emparejamiento privado de Azure estén correctamente configurados. Siga las instrucciones que aparecen en los artículos para [configurar un circuito ExpressRoute](expressroute-howto-circuit-arm.md) y [configurar el enrutamiento](expressroute-howto-routing-arm.md). El circuito ExpressRoute debe tener un aspecto similar a la imagen que aparece a continuación.

	![](./media/expressroute-howto-linkvnet-portal-resource-manager/routing1.png)

	>[AZURE.NOTE] La información de la configuración de BGP no aparecerá si el proveedor de capa 3 configuró los emparejamientos. Si el circuito tiene un estado aprovisionado, debe ser posible crear conexiones.

2. Ahora puede iniciar el aprovisionamiento de una conexión para vincular la puerta de enlace de red virtual con el circuito ExpressRoute. Haga clic en **Conexión** **>** **Agregar** para abrir la hoja **Agregar conexión** y, luego, configure los valores. Consulte el ejemplo de referencia que aparece a continuación.
	

	![](./media/expressroute-howto-linkvnet-portal-resource-manager/samesub1.png)
 
	
3. Una vez que la conexión esté correctamente configurada, el objeto de conexión mostrará la información de la conexión.

	![](./media/expressroute-howto-linkvnet-portal-resource-manager/samesub2.png)


### Para eliminar una conexión

Puede eliminar una conexión si selecciona el icono **Eliminar** en la hoja correspondiente a su conexión.

## Conectar una red virtual en una suscripción distinta con un circuito

En este momento, no se pueden conectar redes virtuales en diferentes suscripciones a través del Portal de Azure. Sin embargo, puede usar PowerShell para hacerlo. Consulte el artículo sobre [PowerShell](expressroute-howto-linkvnet-arm.md) para obtener más información.

## Pasos siguientes

Para obtener más información acerca de ExpressRoute, consulte [P+F de ExpressRoute](expressroute-faqs.md).

<!---HONumber=AcomDC_0420_2016-->