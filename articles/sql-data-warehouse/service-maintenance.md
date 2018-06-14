---
title: 'Mantenimiento planeado: Azure SQL Data Warehouse | Microsoft Docs'
description: Aprenda a prepararse para los eventos de mantenimiento planeado en Azure SQL Data Warehouse.
services: sql-data-warehouse
author: antvgski
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 04/19/2018
ms.author: anvang
ms.reviewer: igorstan
ms.openlocfilehash: a273ad4c256267865d3af324f0ef755a6cb75c5c
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2018
ms.locfileid: "31799896"
---
# <a name="planning-for-maintenance-on-your-azure-sql-data-warehouse"></a>Planeación de mantenimiento en Azure SQL Data Warehouse

Aprenda a prepararse para los eventos de mantenimiento planeado en Azure SQL Data Warehouse.

## <a name="what-is-a-planned-maintenance-event"></a>¿Qué es un evento de mantenimiento planeado?
Un evento de mantenimiento planeado es un período de tiempo durante el que la instancia de Data Warehouse está sin conexión para una operación de mantenimiento. Estos eventos son el momento de aplicar actualizaciones de servicio, características o revisiones. 

## <a name="frequency"></a>Frecuencia
De media, se produce un evento de mantenimiento planeado al mes. 

## <a name="notifications-and-downtime"></a>Notificaciones y tiempo de inactividad
Antes de cada evento de mantenimiento planeado recibirá una notificación. El evento de mantenimiento cancela todas las consultas en ejecución y desconecta la instancia de Data Warehouse. El tiempo de inactividad previsto para cada instancia de Data Warehouse es de unos 30 minutos. Recibirá una notificación cuando se complete el mantenimiento. 

## <a name="setting-up-alerts"></a>Configuración de las alertas

Se recomienda usar [Azure Monitor](../monitoring-and-diagnostics/monitoring-activity-log-alerts-on-service-notifications.md) para configurar las alertas de registro del mantenimiento planeado. Las alertas ayudan a planear que el mantenimiento necesario afecte lo mínimo al negocio. 

Para configurar las notificaciones, utilice estas [instrucciones de las alertas de registro](../monitoring-and-diagnostics/monitoring-activity-log-alerts-on-service-notifications.md). 

## <a name="next-steps"></a>Pasos siguientes
Para más información sobre la supervisión, consulte [Supervisión de la carga de trabajo](sql-data-warehouse-manage-monitor.md).
