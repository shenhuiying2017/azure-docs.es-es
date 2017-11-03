---
title: Servicio Azure SQL Database | Microsoft Docs
description: "Obtenga información acerca de los niveles de servicio para las bases de datos de grupo y únicas a fin de proporcionar niveles de rendimiento y tamaños de almacenamiento."
keywords: 
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: f5c5c596-cd1e-451f-92a7-b70d4916e974
ms.service: sql-database
ms.custom: DBs & servers
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Active
ms.date: 08/20/2017
ms.author: carlrab
ms.openlocfilehash: 55f59fddee008eb42b7252d6368a56873a6abd16
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/31/2017
---
# <a name="what-are-azure-sql-database-service-tiers"></a>Qué son los niveles de servicio de Azure SQL Database

[Azure SQL Database](sql-database-technical-overview.md) ofrece los niveles de servicio **Básico**, **Estándar**, **Premium** y **Premium RS** tanto para [bases de datos únicas](sql-database-single-database-resources.md) como para [grupos de bases de datos elásticas](sql-database-elastic-pool.md). Los niveles de servicio se diferencian sobre todo en un rango de opciones relativas al nivel de rendimiento y al tamaño de almacenamiento, así como de precio.  Todos los niveles de servicio proporcionan flexibilidad para cambiar el tamaño de almacenamiento y el nivel de rendimiento.  Las bases de datos únicas y los grupos elásticos se facturan por horas en función de nivel de servicio, el nivel de rendimiento y el tamaño de almacenamiento.   

## <a name="choosing-a-service-tier"></a>Selección de un nivel de servicio

La selección de un nivel de servicio depende sobre todo de los requisitos de continuidad del negocio, de almacenamiento y de rendimiento.
| | **Básico** | **Standard** |**Premium** |**Premium RS** |
| :-- | --: |--:| --:| --:| 
|Carga de trabajo de destino|Desarrollo y producción|Desarrollo y producción|Desarrollo y producción|Carga de trabajo que puede tolerar una pérdida de datos de hasta 5 minutos debido a errores en el servicio|
|Acuerdo de Nivel de Servicio de tiempo de actividad|99,99%|99,99%|99,99%|N/D en versión preliminar|
|Retención de copias de seguridad|7 días|35 días|35 días|35 días|
|CPU|Bajo|Bajo, medio, alto|Medio, alto|Mediano|
|Rendimiento de E/S|Bajo  | Mediano | Orden de magnitud mayor que Estándar|Igual que Premium|
|Latencia de E/S|Mayor que Premium|Mayor que Premium|Menor que Básico y Estándar|Igual que Premium|
|Indexación de almacén de columnas y OLTP en memoria|N/D|N/D|Compatible|Compatible|
|||||

## <a name="performance-level-and-storage-size-limits"></a>Límites de nivel de rendimiento y de tamaño de almacenamiento

Los niveles de rendimiento se expresan como unidades de transmisión de datos (DTU) para las bases de datos únicas y como unidades de transmisión de datos elásticas (eDTU) para los grupos de bases de datos elásticas. Para más información sobre las DTU y las eDTU, consulte [¿Qué son las DTU y las eDTU?](sql-database-what-is-a-dtu.md)

### <a name="single-databases"></a>Bases de datos únicas

|  | **Básico** | **Standard** | **Premium** | **Premium RS**|
| :-- | --: | --: | --: | --: |
| Tamaño máximo de almacenamiento* | 2 GB | 1 TB | 4 TB  | 1 TB  |
| Cantidad máxima de DTU | 5 | 3000 | 4000 | 1000 |
||||||

### <a name="elastic-pools"></a>Grupos elásticos

| | **Básico** | **Standard** | **Premium** | **Premium RS**|
| :-- | --: | --: | --: | --: |
| Tamaño máximo de almacenamiento por base de datos*  | 2 GB | 1 TB | 1 TB | 1 TB |
| Tamaño máximo de almacenamiento por grupo* | 156 GB | 4 TB | 4 TB | 1 TB |
| Cantidad máxima de eDTU por base de datos | 5 | 3000 | 4000 | 1000 |
| Cantidad máxima de eDTU por grupo | 1600 | 3000 | 4000 | 1000 |
| Cantidad máxima de bases de datos por grupo | 500  | 500 | 100 | 100 |
||||||

> [!IMPORTANT]
> \* Los tamaños de almacenamiento mayores que la cantidad de almacenamiento incluida están en su versión preliminar y pueden generar costos adicionales. Para obtener información detallada, vea [Precios de bases de datos SQL](https://azure.microsoft.com/pricing/details/sql-database/). 
>
> \* En el nivel Premium, más de 1 TB de almacenamiento se encuentra actualmente disponible en las siguientes regiones: este de EE. UU. 2, oeste de EE. UU., Virginia Gob. EE. UU., Europa Occidental, centro de Alemania, Asia Suroriental, Japón Oriental, este de Australia, centro de Canadá y este de Canadá. Consulte [Limitaciones actuales P11-P15](sql-database-resource-limits.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb).  
> 

Para más información sobre las opciones específicas de niveles de rendimiento y de tamaño de almacenamiento disponibles, consulte [Límites de recursos de SQL Database](sql-database-resource-limits.md).


## <a name="next-steps"></a>Pasos siguientes

- Más información sobre [recursos de bases de datos únicas](sql-database-single-database-resources.md).
- Más información sobre grupos elásticos en [este artículo](sql-database-elastic-pool.md).
- Más información sobre [límites, cuotas y restricciones de suscripción y servicios de Azure](../azure-subscription-service-limits.md).
* Más información sobre [DTU y eDTU](sql-database-what-is-a-dtu.md).
* Más información sobre la supervisión del uso de DTU en [Supervisión y optimización del rendimiento](sql-database-troubleshoot-performance.md).

