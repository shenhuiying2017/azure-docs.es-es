<properties 
   pageTitle="Requisitos previos para la adopción de ExpressRoute"
   description="Esta página proporciona una lista de requisitos que deben cumplirse para poder solicitar un circuito ExpressRoute."
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="adinah"
   editor="tysonn"/> <tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="05/06/2015"
   ms.author="cherylmc"/>


# Requisitos previos de ExpressRoute  

Para conectarse a Servicios en la nube de Microsoft con ExpressRoute, deberá comprobar que se han cumplido los requisitos previos siguientes:

## Requisitos previos para la conectividad

- Una cuenta de Microsoft Azure válida y activa
- Una relación con un proveedor de servicios de red (NSP) o un proveedor de Exchange (EXP) en la [lista de compatibilidad](expressroute-locations.md) a través de la que debe facilitarse la conectividad. Debe tener una relación comercial existente con el proveedor de servicio de red o el proveedor de Exchange. Deberá asegurarse de que el servicio que utiliza es compatible con ExpressRoute. 
- Si desea utilizar un proveedor de servicios de red y su proveedor de servicios de red no está en la lista anterior, puede crear una conexión a Azure. 
	- Consulte con su proveedor de red para ver si están presentes en cualquiera de las ubicaciones de Exchange enumeradas anteriormente.
	- Haga que el proveedor de red amplíe su red a la ubicación de Exchange que elija.
	- Solicite un circuito ExpressRoute a través del proveedor de Exchange para conectarse a Azure.
- Conectividad con la infraestructura del proveedor de servicio. Debe cumplir los criterios para al menos uno de los elementos que se indican:
	- Es un cliente VPN del proveedor de servicios de red y tiene al menos un sitio local conectado a la infraestructura VPN del proveedor de servicio de red. Compruebe con el proveedor de servicios de red si su servicio VPN cumple los requisitos de ExpressRoute.
	- La infraestructura cuenta con una coubicación en el centro de datos del proveedor de Exchange.
	- Dispone de una conectividad Ethernet para la infraestructura de Exchange de Ethernet del proveedor de Exchange.	
- Las direcciones IP y los números de AS para configuración de enrutamiento. 
	- Puede usar números de AS privados para conectarse al dominio de enrutamiento de emparejamiento privado Azure. Si decide hacerlo, debe ser > 65.000. Para obtener más información acerca de los números de AS, consulte [Números de sistema autónomo (AS)](http://www.iana.org/assignments/as-numbers/as-numbers.xhtml).
	- Direcciones IP para configurar rutas. Es necesaria una subred /28. Esto no debe solaparse con los intervalos de direcciones IP en Azure o de forma local.
	- Debe utilizar sus propios números de AS públicos para la configuración de sesiones de BGP con servicios públicos de Azure.

## Pasos siguientes

- Para obtener más información acerca de ExpressRoute, consulte [P+F de ExpressRoute](expressroute-faqs.md).
- Para obtener información acerca de cómo configurar la conexión ExpressRoute, vea 
	- [Configuración de una conexión ExpressRoute a través de un proveedor de servicios de red](expressroute-configuring-nsps.md)
	- [Configuración de una conexión ExpressRoute a través de un proveedor de Exchange](expressroute-configuring-exps.md)
<!--HONumber=54-->