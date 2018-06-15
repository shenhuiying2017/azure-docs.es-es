---
title: Topologías de red para migraciones a Instancia administrada de Azure SQL Database mediante Azure Database Migration Service | Microsoft Docs
description: Más información sobre las configuraciones de origen y de destino para Database Migration Service.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: ''
ms.reviewer: ''
ms.service: database-migration
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 03/06/2018
ms.openlocfilehash: 5904864ffba656dab17e1549ed9832be4258a67f
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/28/2018
ms.locfileid: "30235407"
---
# <a name="network-topologies-for-azure-sql-db-managed-instance-migrations-using-the-azure-database-migration-service"></a>Topologías de red para migraciones a Instancia administrada de Azure SQL Database con Azure Database Migration Service
En este artículo obtendrá información sobre diversas topologías de red con las que Azure Database Migration Service puede trabajar para proporcionar una experiencia de migración sin problemas desde instancias locales de SQL Server a Instancia administrada de SQL Database.

## <a name="azure-sql-database-managed-instance-configured-for-hybrid-workloads"></a>Instancia administrada de Azure SQL Database configurada para cargas de trabajo híbridas 
Use esta topología si Instancia administrada de Azure SQL Database está conectada a la red local. Este enfoque proporciona el enrutamiento de red más simplificado y genera el máximo rendimiento de los datos durante la migración.

![Topología de red para cargas de trabajo híbridas](media\resource-network-topologies\hybrid-workloads.png)

**Requisitos**
- En este escenario, Instancia administrada de Azure SQL Database y la instancia de Azure Database Migration Service se crean en la misma red virtual de Azure pero usan diferentes subredes.  
- La red virtual que se utiliza en este escenario también está conectada a la red local mediante [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) o [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).

## <a name="azure-sql-database-managed-instance-isolated-from-the-on-premises-network"></a>Instancia administrada de Azure SQL Database aislada de la red local
Use esta topología de red si su entorno requiere uno o varios de los siguientes escenarios:
- Instancia administrada de Azure SQL Database está aislada de la conectividad local pero la instancia de Azure Database Migration Service está conectada a la red local.
- Si se han implementado directivas de control de acceso basado en roles (RBAC) y tiene que limitar el acceso de los usuarios a la misma suscripción que hospeda Instancia administrada de Azure SQL Database.
- Las redes virtuales que se usan para Instancia administrada de Azure SQL Database y Azure Database Migration Service se encuentran en suscripciones distintas.

![Topología de red para Instancia administrada aislada de la red local](media\resource-network-topologies\mi-isolated-workload.png)

**Requisitos**
- La red virtual que utiliza Azure Database Migration Service para este escenario también debe estar conectada a la red local mediante (https://docs.microsoft.com/azure/expressroute/expressroute-introduction) o [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
- Debe configurar un [emparejamiento de red virtual](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) entre la red virtual que se usa para Instancia administrada de Azure SQL Database y Azure Database Migration Service.


## <a name="cloud-to-cloud-migrations-shared-vnet"></a>Migraciones de nube a nube: red virtual compartida

Use esta topología si el origen de SQL Server se hospeda en una máquina virtual de Azure y comparte la misma red virtual con Instancia administrada de Azure SQL Database y Azure Database Migration Service.

![Topología de red para migraciones de nube a nube con una red virtual compartida](media\resource-network-topologies\cloud-to-cloud.png)

**Requisitos**
- No hay requisitos adicionales.

## <a name="cloud-to-cloud-migrations-isolated-vnet"></a>Migraciones de nube a nube: red virtual aislada

Use esta topología de red si su entorno requiere uno o varios de los siguientes escenarios:
- La Instancia administrada de Azure SQL DataBase está aprovisionada en una red virtual aislada.
- Si se han implementado directivas de control de acceso basado en roles (RBAC) y tiene que limitar el acceso de los usuarios a la misma suscripción que hospeda Instancia administrada de Azure SQL Database.
- Las redes virtuales que se usan para Instancia administrada de Azure SQL Database y Azure Database Migration Service se encuentran en suscripciones distintas.

![Topología de red para migraciones de nube a nube con una red virtual aislada](media\resource-network-topologies\cloud-to-cloud-isolated.png)

**Requisitos**
- Debe configurar un [emparejamiento de red virtual](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) entre la red virtual que se usa para Instancia administrada de Azure SQL Database y Azure Database Migration Service.


## <a name="see-also"></a>Otras referencias
- [Migración de SQL Server a Instancia administrada de Azure SQL Database](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-managed-instance)
- [Información general sobre los requisitos previos para usar Azure Database Migration Service](https://docs.microsoft.com/azure/dms/pre-reqs)
- [Creación de una red virtual mediante Azure Portal](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

## <a name="next-steps"></a>Pasos siguientes
Para información general sobre Azure Database Migration Service y la disponibilidad regional durante la versión preliminar pública, consulte el artículo [¿Qué es la versión preliminar de Azure Database Migration Service?](dms-overview.md) 