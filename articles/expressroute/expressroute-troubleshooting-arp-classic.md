<properties
   pageTitle="Guía de solución de problemas de ExpressRoute: obtención de tablas ARP | Microsoft Azure"
   description="En esta página se proporcionan instrucciones para obtener tablas ARP para un circuito ExpressRoute."
   documentationCenter="na"
   services="expressroute"
   authors="ganesr"
   manager="carolz"
   editor="tysonn"/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="06/06/2016"
   ms.author="ganesr"/>

# Guía de solución de problemas de ExpressRoute: obtención de tablas ARP en el modelo de implementación clásica

> [AZURE.SELECTOR]
[PowerShell - Resource Manager](expressroute-troubleshooting-arp-resourcemanager.md)
[PowerShell - Classic](expressroute-troubleshooting-arp-classic.md)

Este artículo le guiará a través de los pasos para obtener las tablas del Protocolo de resolución de direcciones (ARP) para su circuito Azure ExpressRoute.

>[AZURE.IMPORTANT] Este documento está pensado para ayudarle a diagnosticar y corregir problemas sencillos. No pretende sustituir al soporte técnico de Microsoft. Si no puede solucionar el problema mediante las siguientes instrucciones, abra una solicitud de soporte técnico con [Ayuda y soporte técnico de Microsoft Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## Protocolo de resolución de direcciones (ARP) y tablas ARP
ARP es un protocolo de nivel 2 que se define en [RFC 826](https://tools.ietf.org/html/rfc826). ARP se usa para asignar una dirección Ethernet (dirección MAC) a una dirección IP.

Una tabla ARP proporciona una asignación de la dirección IPv4 y la dirección MAC para un emparejamiento determinado. La tabla ARP de un emparejamiento de circuito ExpressRoute proporciona la siguiente información para cada interfaz (principal y secundaria):

1. Asignación de una dirección IP de la interfaz del enrutador local a la dirección MAC
2. Asignación de la dirección IP de la interfaz del enrutador de ExpressRoute a la dirección MAC
3. La antigüedad de la asignación

Las tablas ARP pueden ayudar a validar la configuración de capa 2 y a solucionar los problemas básicos de conectividad de nivel 2.

A continuación, se muestra un ejemplo de una tabla ARP:

		Age InterfaceProperty IpAddress  MacAddress    
		--- ----------------- ---------  ----------    
		 10 On-Prem           10.0.0.1 ffff.eeee.dddd
		  0 Microsoft         10.0.0.2 aaaa.bbbb.cccc


En la siguiente sección se proporciona información sobre cómo puede ver las tablas ARP que ven los enrutadores de borde de ExpressRoute.

## Requisitos previos para usar las tablas ARP

Asegúrese de que tiene lo siguiente antes de continuar:

 - Un circuito ExpressRoute válido configurado con al menos un emparejamiento. El circuito debe estar completamente configurado por el proveedor de conectividad. Usted (o su proveedor de conectividad) debe configurar al menos uno de los emparejamientos (privado de Azure, público de Azure o Microsoft) en este circuito.

 - Intervalos de direcciones IP que se usan para configurar los emparejamientos (privado de Azure, público de Azure y Microsoft). Revise los ejemplos de asignación de dirección IP en [Requisitos de enrutamiento de ExpressRoute](expressroute-routing.md) para comprender cómo se asignan las direcciones IP a las interfaces en su sitio y el sitio de ExpressRoute. Puede obtener información sobre la configuración de emparejamiento en la página [Creación y modificación del enrutamiento de un circuito ExpressRoute](expressroute-howto-routing-classic.md).

 - Información del equipo de red o del proveedor de conectividad sobre las direcciones MAC de las interfaces que se usan con estas direcciones IP.

 - El módulo más reciente de Windows PowerShell para Azure (versión 1.50 o superior).

## Tablas ARP para el circuito ExpressRoute
En esta sección se proporcionan instrucciones sobre cómo ver las tablas ARP para cada tipo de emparejamiento mediante PowerShell. Antes de continuar, usted o su proveedor de conectividad necesita configurar el emparejamiento. Cada circuito tiene dos rutas de acceso (principal y secundaria). Puede comprobar en la tabla ARP cada ruta de acceso de forma independiente.

### Tablas ARP para el emparejamiento privado de Azure
El siguiente cmdlet proporciona el emparejamiento privado de Azure para las tablas ARP:

		# Required variables
		$ckt = "<your Service Key here>

		# ARP table for Azure private peering--primary path
		Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Private -Path Primary

		# ARP table for Azure private peering--secondary path
		Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Private -Path Secondary

A continuación, se muestra el resultado de ejemplo de una de las rutas de acceso:

		Age InterfaceProperty IpAddress  MacAddress    
		--- ----------------- ---------  ----------    
		 10 On-Prem           10.0.0.1 ffff.eeee.dddd
		  0 Microsoft         10.0.0.2 aaaa.bbbb.cccc


### Tablas ARP para el emparejamiento público de Azure:
El siguiente cmdlet proporciona las tablas ARP para el emparejamiento público de Azure:

		# Required variables
		$ckt = "<your Service Key here>

		# ARP table for Azure public peering--primary path
		Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Public -Path Primary

		# ARP table for Azure public peering--secondary path
		Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Public -Path Secondary

A continuación, se muestra el resultado de ejemplo de una de las rutas de acceso:

		Age InterfaceProperty IpAddress  MacAddress    
		--- ----------------- ---------  ----------    
		 10 On-Prem           10.0.0.1 ffff.eeee.dddd
		  0 Microsoft         10.0.0.2 aaaa.bbbb.cccc


A continuación, se muestra el resultado de ejemplo de una de las rutas de acceso:

		Age InterfaceProperty IpAddress  MacAddress    
		--- ----------------- ---------  ----------    
		 10 On-Prem           64.0.0.1 ffff.eeee.dddd
		  0 Microsoft         64.0.0.2 aaaa.bbbb.cccc


### Tablas ARP para el emparejamiento de Microsoft
El siguiente cmdlet proporciona las tablas ARP para el emparejamiento de Microsoft:

    # ARP table for Microsoft peering--primary path
    Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Microsoft -Path Primary

    # ARP table for Microsoft peering--secondary path
    Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Microsoft -Path Secondary


A continuación se muestra el resultado de ejemplo de una de las rutas de acceso:

		Age InterfaceProperty IpAddress  MacAddress    
		--- ----------------- ---------  ----------    
		 10 On-Prem           65.0.0.1 ffff.eeee.dddd
		  0 Microsoft         65.0.0.2 aaaa.bbbb.cccc


## Uso de esta información
La tabla ARP de un emparejamiento se puede usar para validar la configuración y la conectividad de nivel 2. En esta sección se proporciona información general sobre la apariencia de las tablas ARP en distintos escenarios.

### Tabla ARP cuando un circuito está en un estado operativo (esperado)

 - La tabla ARP tiene una entrada para el lado local con una dirección IP válida y la dirección MAC, y una entrada similar para el lado de Microsoft.
 - El último octeto de la dirección IP local siempre será un número impar.
 - El último octeto de la dirección IP de Microsoft siempre será un número par.
 - La misma dirección MAC aparece en el lado de Microsoft para los tres emparejamientos (principales o secundarios).


		Age InterfaceProperty IpAddress  MacAddress    
		--- ----------------- ---------  ----------    
		 10 On-Prem           65.0.0.1 ffff.eeee.dddd
		  0 Microsoft         65.0.0.2 aaaa.bbbb.cccc

### Tabla ARP cuando es local o cuando el lado del proveedor de conectividad tiene problemas

 Solo aparece una entrada en la tabla ARP. Muestra la asignación entre la dirección MAC y la dirección IP que se usa en el lado de Microsoft.

		Age InterfaceProperty IpAddress  MacAddress    
		--- ----------------- ---------  ----------    
		  0 Microsoft         65.0.0.2 aaaa.bbbb.cccc

>[AZURE.NOTE] Si experimenta un problema como este, abra una solicitud de soporte con su proveedor de conectividad para resolverlo.


### Tabla ARP cuando el lado de Microsoft tiene problemas

 - Cuando hay problemas en el lado de Microsoft, no verá una tabla ARP para un emparejamiento.
 -  Abra una solicitud de soporte con [Ayuda y soporte técnico de Microsoft Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Especifique que tiene un problema con la conectividad de nivel 2.

## Pasos siguientes

 - Validar las configuraciones de nivel 3 para el circuito ExpressRoute:
	 - Obtenga un resumen de ruta para determinar el estado de las sesiones BGP.
	 - Obtenga una tabla de rutas para determinar qué prefijos se anuncian a través de ExpressRoute.
 - Validar la transferencia de datos revisando los bytes de entrada y de salida.
 - Si sigue teniendo problemas, abra una solicitud de soporte técnico en [Ayuda y soporte técnico de Microsoft Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

<!---HONumber=AcomDC_0713_2016-->