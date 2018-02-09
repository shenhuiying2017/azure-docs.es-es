---
title: "Información general sobre los requisitos previos para usar Azure Database Migration Service | Microsoft Docs"
description: "Obtenga información general sobre los requisitos previos para usar Azure Database Migration Service para realizar migraciones de bases de datos."
services: database-migration
author: HJToland3
ms.author: jtoland
manager: 
ms.reviewer: 
ms.service: database-migration
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 01/25/2018
ms.openlocfilehash: a103bb4802bc4a20b6d82f0c6bb427133d9e5643
ms.sourcegitcommit: 99d29d0aa8ec15ec96b3b057629d00c70d30cfec
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/25/2018
---
# <a name="overview-of-prerequisites-for-using-the-azure-database-migration-service"></a>Información general sobre los requisitos previos para usar Azure Database Migration Service
Hay varios requisitos previos necesarios para garantizar que Azure Database Migration Service se ejecute sin problemas al realizar migraciones de bases de datos. Algunos de los requisitos previos se aplican en todos los escenarios (pares origen-destino) compatibles con el servicio, mientras que otros son exclusivos para un escenario específico.

Los requisitos previos asociados con el uso de Azure Database Migration Service se muestran en las secciones siguientes.

## <a name="prerequisites-common-across-migration-scenarios"></a>Requisitos previos comunes en los distintos escenarios de migración
Los requisitos de Azure Database Migration Service que son comunes en todos los escenarios de migración compatibles incluyen la necesidad de:
- Crear una red virtual para Azure Database Migration Service mediante el modelo de implementación de Azure Resource Manager, que proporciona conectividad de sitio a sitio a los servidores de origen local utilizando [ExpressRoute](https://docs.microsoft.com/en-us/azure/expressroute/expressroute-introduction) o [VPN](https://docs.microsoft.com/en-us/azure/vpn-gateway/vpn-gateway-about-vpngateways).
- Asegúrese de que el grupo de seguridad de red de Azure Virtual Network (VNET) no bloquea los puertos de comunicación 443, 53, 9354, 445 y 12000. Para obtener información más detallada sobre el filtrado de tráfico con NSG de Azure VNET, vea el artículo [Filtrado del tráfico de red con grupos de seguridad de red](https://docs.microsoft.com/en-us/azure/virtual-network/virtual-networks-nsg).
- Cuando se usa un dispositivo de firewall frente a las bases de datos de origen, puede que sea necesario agregar reglas de firewall para permitir que Azure Database Migration Service acceda a las bases de datos de origen para realizar la migración.

## <a name="prerequisites-for-migrating-sql-server-to-azure-sql-database"></a>Requisitos previos para migrar SQL Server a Azure SQL Database 
Además de los requisitos previos de Azure Database Migration Service que son comunes para todos los escenarios de migración, también hay requisitos previos que se aplican específicamente a un escenario o a otro.

Cuando se usa Azure Database Migration Service para realizar migraciones de SQL Server a Azure SQL Database, además de los requisitos previos comunes a todos los escenarios de migración, asegúrese de cumplir con estos requisitos previos adicionales:
- Configurar su [Firewall de Windows para acceder al motor de base de datos](https://docs.microsoft.com/en-us/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Habilitar el protocolo TCP/IP, que se deshabilita de forma predeterminada durante la instalación de SQL Server Express, siguiendo las instrucciones del artículo [Habilitar o deshabilitar un protocolo de red de servidor](https://docs.microsoft.com/en-us/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure).
- Crear una instancia de Azure SQL Database siguiendo la información del artículo sobre cómo [crear una instancia de Azure SQL Database en Azure Portal](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-get-started-portal).
- Descargar e instalar [Data Migration Assistant](https://www.microsoft.com/en-us/download/details.aspx?id=53595) versión 3.3 o posterior.
- Abrir Firewall de Windows para permitir que Azure Database Migration Service acceda a las bases de datos de origen.
- Crear una [regla de firewall](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-firewall-configure) a nivel de servidor para que el servidor de Azure SQL Database permita a Azure Database Migration Service acceder a las bases de datos de destino. Proporcionar el intervalo de subred de la red virtual usada para Azure Database Migration Service.
- Asegurarse de que las credenciales usadas para conectarse a la instancia de SQL Server de origen tenga permisos [CONTROL SERVER](https://docs.microsoft.com/en-us/sql/t-sql/statements/grant-server-permissions-transact-sql).
- Asegurarse de que las credenciales usadas para conectarse a la instancia de Azure SQL Database de destino tengan permisos CONTROL DATABASE en las bases de datos SQL de Azure de destino.

   > [!NOTE]
   > Para una lista completa de los requisitos previos necesarios para usar Azure Database Migration Service para realizar migraciones de SQL Server a Azure SQL Database, consulte el tutorial [Migración de SQL Server a Azure SQL Database](https://docs.microsoft.com/en-us/azure/dms/tutorial-sql-server-to-azure-sql).
   > 

## <a name="next-steps"></a>pasos siguientes
Para información general sobre Azure Database Migration Service y la disponibilidad regional durante la versión preliminar pública, consulte el artículo [¿Qué es la versión preliminar de Azure Database Migration Service?](dms-overview.md) 