---
title: "Acerca de la supervisión de redes en Log Analytics | Microsoft Docs"
description: "Introducción a las soluciones de supervisión de redes, como es NPM, para administrar redes en entornos en la nube, locales e híbridos."
services: monitoring-and-diagnostics
documentationcenter: na
author: agummadi
manager: 
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2018
ms.author: ajaycode
ms.openlocfilehash: 6d93821b59e1f69a48c3d5eeda96dad2edddb188
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="network-monitoring-solutions"></a>Soluciones de supervisión de redes 

Azure ofrece una gran cantidad de soluciones para supervisar los activos de una red. Azure ofrece soluciones y utilidades para supervisar la conectividad de red y el estado de los circuitos ExpressRoute, y para analizar el tráfico de red en la nube.

## <a name="network-performance-monitor-npm"></a>Network Performance Monitor (NPM) (monitor de rendimiento de red)

Network Performance Monitor (NPM) es un conjunto de funcionalidades, cada una orientada específicamente a la supervisión del estado de una red y a la conectividad de red para las aplicaciones, además de proporcionar información detallada sobre el rendimiento de una red. NPM está basado en la nube y proporciona una solución híbrida para la supervisión de la red que controla la conectividad entre:
 
* Las implementaciones en la nube y las ubicaciones locales
* Varios centros de datos y sucursales
* Aplicaciones y microservicios esenciales de niveles múltiples
* Aplicaciones basadas en web (HTTP/HTTPs) y ubicaciones de usuario 

## <a name="performance-monitor"></a>Supervisión del rendimiento

Performance Monitor es parte de NPM y supervisa la red en entornos en la nube, locales e híbridos. Puede supervisar la conectividad de red entre oficinas locales y sucursales remotas, tiendas, centros de datos y nubes. Puede detectar problemas de red antes de que los usuarios se quejen. Las principales ventajas son:

* Supervisar la latencia y la pérdida a través de varias redes virtuales y establecer alertas
* Supervisar todas las rutas de acceso (incluidas las rutas de acceso redundantes) en la red
* Solucionar problemas de red transitorios y puntuales que resultan difíciles de replicar
* Determinar un segmento específico de la red que es responsable de la degradación del rendimiento
* Supervisar el estado de la red, sin necesidad de SNMP

![Mapa de topología NPM](./media/network-monitoring-overview/npm-topology-map.png) 

Para más información, consulte los siguientes artículos:

* [Solución Network Performance Monitor de Log Analytics](../log-analytics/log-analytics-network-performance-monitor.md) 
* [Casos de uso](https://blogs.technet.microsoft.com/msoms/2016/08/30/monitor-on-premises-cloud-iaas-and-hybrid-networks-using-oms-network-performance-monitor/)
*  Actualizaciones del producto: [febrero de 2017](https://blogs.technet.microsoft.com/msoms/2017/02/27/oms-network-performance-monitor-is-now-generally-available/), [agosto de 2017](https://blogs.technet.microsoft.com/msoms/2017/08/14/improvements-to-oms-network-performance-monitor/)

## <a name="expressroute-monitor"></a>Monitor para ExpressRoute

NPM para ExpressRoute ofrece una supervisión completa de ExpressRoute destinada a conexiones privadas del mismo nivel. Puede supervisar la conectividad E2E y el rendimiento entre las sucursales y Azure a través de ExpressRoute. Las funcionalidades principales son:

* Detección automática de circuitos ER asociados a una suscripción
* Detección de la topología de red del entorno local a las aplicaciones en la nube
* Planeamiento de la capacidad, análisis de uso
* Supervisión y alertas relacionadas con las rutas de acceso principales y secundarias
* Detección del deterioro de la conectividad con redes virtuales

Para más información, consulte los siguientes artículos.

* [Configuración de Network Performance Monitor para ExpressRoute](../expressroute/how-to-npm.md)
* [Entrada de blog](https://aka.ms/NPMExRmonitorGA)

## <a name="service-endpoint-monitor"></a>Supervisión de puntos de conexión de servicio

Con la supervisión de puntos de conexión de servicio, ahora puede probar la accesibilidad de las aplicaciones y detectar los cuellos de botella en el rendimiento de las redes locales, redes de operadores y centros de datos en la nube y privados.

* Supervisar de un extremo a otro la conectividad de red con las aplicaciones
* Poner en correlación la entrega de las aplicaciones con el rendimiento de la red, detectar la ubicación exacta de la degradación a lo largo de la ruta entre el usuario y la aplicación
* Probar la accesibilidad de las aplicaciones desde varias ubicaciones de usuario en todo el mundo
* Determinar la pérdida de paquetes y la latencia de red para la línea de negocio y las aplicaciones SaaS
* Determinar las zonas activas en la red, que puedan estar causando el rendimiento insuficiente de las aplicaciones
* Supervisar la disponibilidad para las aplicaciones de Office 365, con pruebas integradas de Microsoft Office 365, Dynamics 365, Skype Empresarial y otros servicios de Microsoft

Para más información, consulte los siguientes artículos.

* [Configurar Network Performance Monitor para la supervisión de puntos de conexión de servicio](https://aka.ms/applicationconnectivitymonitorguide)
* [Entrada de blog](https://aka.ms/svcendptmonitor)

## <a name="next-steps"></a>pasos siguientes

* [Configuración de Network Performance Monitor](https://docs.microsoft.com/azure/log-analytics/log-analytics-network-performance-monitor)
* [Configuración de Network Performance Monitor para ExpressRoute](../expressroute/how-to-npm.md)
