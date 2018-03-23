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
ms.openlocfilehash: 892cff02b5b70f09236bb37ae786f180ddca9316
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/08/2018
---
# <a name="network-topologies-for-azure-sql-db-managed-instance-migrations-using-the-azure-database-migration-service"></a>Topologías de red para migraciones a Instancia administrada de Azure SQL Database con Azure Database Migration Service
En este artículo obtendrá información sobre diversas topologías de red con las que Azure Database Migration Service puede trabajar para proporcionar una experiencia de migración sin problemas a Instancia administrada de Azure SQL Database desde los servidores SQL Server locales.

## <a name="azure-sql-database-managed-instance-configured-for-hybrid-workloads"></a>Instancia administrada de Azure SQL Database configurada para cargas de trabajo híbridas 
Use esta topología si Instancia administrada de Azure SQL Database está conectada a la red local. Este enfoque proporciona el enrutamiento de red más simplificado y genera el máximo rendimiento de los datos durante la migración.

![Topología de red para cargas de trabajo híbridas](media\resource-network-topologies\hybrid-workloads.png)

**Requisitos**
- En este escenario, Instancia administrada de Azure SQL Database y la instancia de Azure Database Migration Service se crean en la misma red virtual de Azure pero usan diferentes subredes.  
- La red virtual que se utiliza en este escenario también está conectada a la red local mediante ExpressRoute o VPN.

## <a name="azure-sql-database-managed-instance-isolated-from-the-on-premises-network"></a>Instancia administrada de Azure SQL Database aislada de la red local
Use esta topología de red si su entorno requiere uno o varios de los siguientes escenarios:
- Instancia administrada de Azure SQL Database está aislada de la conectividad local pero la instancia de Azure Database Migration Service está conectada a la red local.
- Se han implementado directivas de control de acceso basado en roles (RBAC) y limita el acceso de usuario a la misma suscripción que hospeda Instancia administrada de Azure SQL Database.
- Las redes virtuales que se usan para Instancia administrada de Azure SQL Database y Azure Database Migration Service se encuentran en suscripciones distintas.

![Topología de red para Instancia administrada aislada de la red local](media\resource-network-topologies\mi-isolated-workload.png)

**Requisitos**
- La red virtual que utiliza Azure Database Migration Service para este escenario también debe estar conectada a la red local mediante ExpressRoute o VPN.
- Cree un emparejamiento de red virtual entre la red virtual que se usa para Instancia administrada de Azure SQL Database y Azure Database Migration Service.


## <a name="cloud-to-cloud-migrations"></a>Migraciones de nube a nube
Use esta topología si el servidor SQL Server de origen se hospeda en una máquina virtual de Azure.

![Topología de red para migraciones de nube a nube](media\resource-network-topologies\cloud-to-cloud.png)

**Requisitos**
- Cree un emparejamiento de red virtual entre la red virtual que se usa para Instancia administrada de Azure SQL Database y Azure Database Migration Service.

## <a name="see-also"></a>Otras referencias
- [Migración de SQL Server a Instancia administrada de Azure SQL Database](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-managed-instance)
- [Información general sobre los requisitos previos para usar Azure Database Migration Service](https://docs.microsoft.com/azure/dms/pre-reqs)
- [Creación de una red virtual mediante Azure Portal](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

## <a name="next-steps"></a>Pasos siguientes
Para información general sobre Azure Database Migration Service y la disponibilidad regional durante la versión preliminar pública, consulte el artículo [¿Qué es la versión preliminar de Azure Database Migration Service?](dms-overview.md) 