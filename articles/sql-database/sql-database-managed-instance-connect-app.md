---
title: Conexión de la aplicación a Instancia administrada de Azure SQL Database | Microsoft Docs
description: En este artículo se describe la conexión de la aplicación a Instancia administrada de Azure SQL Database.
ms.service: sql-database
author: srdjan-bozovic
manager: craigg
ms.custom: managed instance
ms.topic: article
ms.date: 04/10/2018
ms.author: srbozovi
ms.reviewer: bonova, carlrab
ms.openlocfilehash: 1eecd28d5e7043acae5cfd52edf93e8d301bd31e
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/16/2018
---
# <a name="connect-your-application-to-azure-sql-database-managed-instance"></a>Conexión de la aplicación a Instancia administrada de Azure SQL Database

Hoy en día puede elegir entre varias opciones sobre cuándo y cómo hospedar la aplicación. 
 
Puede elegir hospedar la aplicación en la nube mediante Azure App Service o algunas de las opciones de red virtual integradas de Azure, como Azure App Service Environment, Virtual Machine o Virtual Machine Scale Set. También podría elegir un enfoque de nube híbrida y mantener las aplicaciones en local. 
 
Sea cual sea su opción, se podrá realizar una conexión a Instancia administrada (versión preliminar).  

![alta disponibilidad](./media/sql-database-managed-instance/application-deployment-topologies.png)  

## <a name="connect-an-application-inside-the-same-vnet"></a>Conexión de una aplicación dentro de la misma red virtual 

Este escenario es el más sencillo. Las máquinas virtuales dentro de la red virtual pueden conectarse directamente entre sí aunque se encuentren en subredes diferentes. Esto significa que para conectar la aplicación dentro de una instancia de Azure Application Environment o de Virtual Machine solo tiene que establecer correctamente la cadena de conexión.  
 
Si no puede establecer la conexión, compruebe si hay algún grupo de seguridad de red establecido en la subred de la aplicación. Si es así, abra una conexión saliente en el puerto 1433 de SQL, así como en los puertos 11000-12000 para el redireccionamiento. 

## <a name="connect-an-application-inside-a-different-vnet"></a>Conexión de una aplicación dentro de una red virtual distinta 

Este escenario es un poco más complejo, ya que Instancia administrada tiene la dirección IP privada en su propia red virtual. Para conectarse, una aplicación necesita acceso a la red virtual donde Instancia administrada está implementada. Por lo tanto, primero debe establecer una conexión entre la aplicación y la red virtual de Instancia administrada. Las redes virtuales no tienen por qué estar en la misma suscripción para que este escenario funcione. 
 
Para conectar redes virtuales hay dos opciones: 
- [Emparejamiento de Azure Virtual Network](../virtual-network/virtual-network-peering-overview.md) 
- Puerta de enlace VPN de red virtual a red virtual ([Azure Portal](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md), [PowerShell](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md), [CLI de Azure](../vpn-gateway/vpn-gateway-howto-vnet-vnet-cli.md)) 
 
La opción de emparejamiento es la preferida, porque se usa la red troncal de Microsoft y, por lo tanto, desde la perspectiva de la conectividad, no hay diferencias notables de latencia entre máquinas virtuales de la red virtual emparejada y la propia. El emparejamiento de redes virtuales se limita a redes de la misma región, aunque el emparejamiento entre regiones está habilitado en algunas regiones como versión preliminar.  
 
> [!IMPORTANT]
> Es posible que los emparejamientos de redes virtuales entre regiones no tengan el mismo nivel de disponibilidad y confiabilidad que los de disponibilidad general. Es posible que los emparejamientos de redes virtuales tengan funcionalidades limitadas y no estén disponibles en todas las regiones de Azure. Para ver las notificaciones más recientes de disponibilidad y estado de esta característica, consulte la página de actualizaciones de  [Azure Virtual Network](https://azure.microsoft.com/updates/?product=virtual-network). 

## <a name="connect-an-on-premises-application"></a>Conexión de una aplicación local 

A Instancia administrada solo se puede acceder mediante dirección IP privada. Para acceder a la instancia desde la máquina local, debe establecer una conexión de sitio a sitio entre la aplicación y la red virtual de Instancia administrada. 
 
Hay dos opciones de conexión local a una red virtual de Azure: 
- Conexión VPN de sitio a sitio ([Azure Portal](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md), [PowerShell](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md), [CLI de Azure](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md)) 
- Conexión de [ExpressRoute](../expressroute/expressroute-introduction.md)  
 
Si ha establecido la conexión local a Azure correctamente y no puede establecer la conexión con Instancia administrada, compruebe si el firewall tiene abierta la conexión saliente en el puerto 1433 de SQL, así como los puertos 11000-12000 para el redireccionamiento. 

## <a name="connect-an-azure-app-service-hosted-application"></a>Conexión de una aplicación hospedada en Azure App Service 

A Instancia administrada se puede acceder solo mediante una dirección IP privada, por ello, para acceder desde Azure App Service, primero debe realizar una conexión entre la aplicación y la red virtual de Instancia administrada. Consulte [Integración de su aplicación con una instancia de Azure Virtual Network](../app-service/web-sites-integrate-with-vnet.md).  
 
Para la solución de problemas, consulte la sección de [solución de problemas con redes virtuales y aplicaciones](../app-service/web-sites-integrate-with-vnet.md#troubleshooting). Si no se puede establecer una conexión, intente [sincronizar la configuración de red](sql-database-managed-instance-sync-network-configuration.md). 
 
Un caso especial de conexión de Azure App Service a Instancia administrada es cuando la instancia de Azure App Service se ha integrado en una red virtual emparejada a otra red virtual de Instancia administrada. Ese caso requiere la siguiente configuración: 

- La red virtual de Instancia administrada NO debe tener puerta de enlace  
- La red virtual de Instancia administrada debe tener la opción Usar puertas de enlace remotas establecida 
- La red virtual emparejada debe tener la opción Permitir tránsito de puerta de enlace establecida 
 
Este escenario se ilustra en el diagrama siguiente:

![emparejamiento de aplicación integrada](./media/sql-database-managed-instance/integrated-app-peering.png)
 
## <a name="connect-an-application-on-the-developers-box"></a>Conexión de una aplicación en el cuadro de desarrollo 

A Instancia administrada se puede acceder solo mediante una dirección IP privada, por ello, para acceder desde el cuadro de desarrollo, primero debe realizar una conexión entre el cuadro de desarrollo y la red virtual de Instancia administrada.  
 
En la configuración de una conexión de punto a sitio a una red virtual en los artículos de autenticación con certificados de Azure nativos ([Azure Portal](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md), [PowerShell](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md), [CLI de Azure](../vpn-gateway/vpn-gateway-howto-point-to-site-classic-azure-portal.md)) se muestran los detalles de cómo se hace.  

## <a name="next-steps"></a>Pasos siguientes

- Para más información acerca de Instancia administrada, consulte [¿Qué es Instancia administrada de SQL Database (versión preliminar)?](sql-database-managed-instance.md).
- Para un tutorial que muestra cómo crear una instancia administrada, consulte el artículo de [creación de instancias administradas](sql-database-managed-instance-create-tutorial-portal.md).
