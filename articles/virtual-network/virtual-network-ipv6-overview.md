<properties 
   pageTitle="Información general de IPv6 | Microsoft Azure"
   description="Más información acerca del direccionamiento IPv6 en Azure"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/04/2016"
   ms.author="telmos" />

# Compatibilidad con IPv6 en Azure

El protocolo IPv6 se presentó inicialmente en 1998 por el Grupo de Trabajo de Ingeniería de Internet (IETF) para solucionar la limitación de las direcciones IPv4 de 32 bits en la red pública de Internet. Con el incremento del número de dispositivos móviles conectados a Internet y el aumento del Internet de las cosas (IoT), las direcciones IPv6 son cada vez más frecuentes en la red pública de Internet.

>[AZURE.IMPORTANT] IPv6 en Azure se encuentra actualmente en versión preliminar privada y no está disponible para todos los clientes. Esta página se actualizará una vez que la característica esté disponible para todos los clientes.

Puede usar las direcciones IPv6 en las VM IaaS de Azure en las implementaciones de Resource Manager para la conectividad entrante y saliente de la red pública de Internet. En la lista siguiente se describen las funcionalidades de IPv6 en Azure.

- **VM laaS en Resource Manager**
	- Las VM tienen un punto de conexión IPv4 y uno IPv6.
	- Las VM pueden conectarse a Internet a través de una IP pública con equilibrador de carga para conexiones entrantes (equilibrador de carga y NAT) y para conexiones salientes (SNAT).
	- Las VM se conectan a todos los servicios de Azure usando sus puntos de conexión IPv4.
	- Las VM solo usan puntos de conexión IPv4 para la comunicación Este-Oeste
- **DNS de Azure**
	- DNS de Azure es compatible con IPv6 y proporciona los registros AAAA, junto con los registros IPv4.
	- DNS de Azure responde a las consultas con los registros A y AAAA de las búsquedas, cuando ambos existen. 
- **Office365**
	- Los servicios de Office 365 admiten IPv6.
	- Puede conectarse desde su red local a los recursos IPv6 en Office 365 mediante el uso de un circuito ExpressRoute.

## VM laaS en Resource Manager

Solo puede aprovecharse de la conectividad IPv6 con el modelo de implementación del Administrador de recursos. Aunque cada VM de una implementación de Resource Manager tiene puntos de conexión IPv4 e IPv6, el punto de conexión IPv6 solo puede usarse para conectarse a la red pública de Internet a través de un equilibrador de carga, como se muestra a continuación.

![Conectividad de IPv6](./media/virtual-network-ipv6-overview/figure1.png)

## DNS de Azure

Puede hospedar registros AAAA de IPv6 en DNS de Azure. Cuando DNS de Azure recibe una consulta de un recurso, responde con todos los registros disponibles para el nombre de host. Por ejemplo, si tiene un registro A y un registro AAAA para un nombre de host determinado (es decir, www.contoso.com), DNS de Azure envía ambas direcciones como respuesta a la consulta. El cliente DNS decidirá si usa el punto de conexión IPv4 o IPv6 para la conectividad, una vez que reciba la respuesta del DNS de Azure o de cualquier otro servidor DNS que admita registros AAAA.

>[AZURE.NOTE] Los equipos con Windows siempre intentan conectarse mediante IPv6 antes de intentar una conexión IPv4.

## Office 365

Puede usar la conectividad IPv6 entre su red local y Office 365 con un circuito ExpressRoute y el emparejamiento de Microsoft. Asegúrese de que comprende [cómo funciona ExpressRoute](../expressroute/expressroute-introduction.md) en relación con el emparejamiento de Microsoft.

## Pasos siguientes

- Obtenga información acerca de [DNS de Azure](../dns/dns-overview.md).
- Obtenga información acerca de [ExpressRoute](../expressroute/expressroute-introduction.md).

<!---HONumber=AcomDC_0309_2016-->