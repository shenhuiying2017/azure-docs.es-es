---
title: Servicio Azure SQL Database | Microsoft Docs
description: Obtenga información acerca de los niveles de servicio para las bases de datos de grupo y únicas a fin de proporcionar niveles de rendimiento y tamaños de almacenamiento.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: article
ms.date: 03/15/2018
ms.author: carlrab
ms.openlocfilehash: 6153616de763eee1b20fff40d38816eca8b455de
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/17/2018
---
# <a name="what-are-azure-sql-database-service-tiers"></a>¿Qué son los niveles de servicio de Azure SQL Database?

[Azure SQL Database](sql-database-technical-overview.md) ofrece los niveles de servicio **Básico**, **Estándar** y **Premium** tanto para [bases de datos únicas](sql-database-single-database-resources.md) como para [grupos de bases de datos elásticas](sql-database-elastic-pool.md). SQL Database ofrece un nivel de servicio de uso general para [Instancia administrada de Azure SQL Database](sql-database-managed-instance.md#managed-instance-service-tier). Los niveles de servicio se diferencian sobre todo en un rango de opciones relativas al nivel de rendimiento y al tamaño de almacenamiento, así como de precio.  Todos los niveles de servicio proporcionan flexibilidad para cambiar el tamaño de almacenamiento y el nivel de rendimiento.  Las bases de datos únicas y los grupos elásticos se facturan por horas en función de nivel de servicio, el nivel de rendimiento y el tamaño de almacenamiento.   

> [!IMPORTANT]
> La Instancia administrada de SQL Database, actualmente en versión preliminar pública, ofrece un único nivel de servicio de uso general. Para más información, consulte [Instancia administrada de Azure SQL Database](sql-database-managed-instance.md). El resto de este artículo no se aplica a Instancia administrada.

## <a name="choosing-a-service-tier"></a>Selección de un nivel de servicio

La selección de un nivel de servicio depende sobre todo de los requisitos de continuidad del negocio, de almacenamiento y de rendimiento.
| | **Básico** | **Estándar** |**Premium**  |
| :-- | --: |--:| --:| --:| 
|Carga de trabajo de destino|Desarrollo y producción|Desarrollo y producción|Desarrollo y producción||
|Acuerdo de Nivel de Servicio de tiempo de actividad|99,99%|99,99%|99,99%|N/D en versión preliminar|
|Retención de copias de seguridad|7 días|35 días|35 días|
|CPU|Bajo|Bajo, medio, alto|Medio, alto|
|Rendimiento de E/S (aproximado) |2,5 IOPS por DTU  | 2,5 IOPS por DTU | 48 IOPS por DTU|
|Latencia de E/S (aproximada)|5 ms (lectura), 10 ms (escritura)|5 ms (lectura), 10 ms (escritura)|2 ms (lectura/escritura)|
|Indexación de almacén de columnas y OLTP en memoria|N/D|N/D|Compatible|
|||||

## <a name="performance-level-and-storage-size-limits"></a>Límites de nivel de rendimiento y de tamaño de almacenamiento

Los niveles de rendimiento se expresan como unidades de transmisión de datos (DTU) para las bases de datos únicas y como unidades de transmisión de datos elásticas (eDTU) para los grupos de bases de datos elásticas. Para más información sobre las DTU y las eDTU, consulte [¿Qué son las DTU y las eDTU?](sql-database-what-is-a-dtu.md)

### <a name="single-databases"></a>Bases de datos únicas

|  | **Básico** | **Estándar** | **Premium** | 
| :-- | --: | --: | --: | --: |
| Tamaño máximo de almacenamiento* | 2 GB | 1 TB | 4 TB  | 
| Cantidad máxima de DTU | 5 | 3000 | 4000 | |
||||||

### <a name="elastic-pools"></a>Grupos elásticos

| | **Básico** | **Estándar** | **Premium** | 
| :-- | --: | --: | --: | --: |
| Tamaño máximo de almacenamiento por base de datos*  | 2 GB | 1 TB | 1 TB | 
| Tamaño máximo de almacenamiento por grupo* | 156 GB | 4 TB | 4 TB | 
| Cantidad máxima de eDTU por base de datos | 5 | 3000 | 4000 | 
| Cantidad máxima de eDTU por grupo | 1600 | 3000 | 4000 | 
| Cantidad máxima de bases de datos por grupo | 500  | 500 | 100 | 
||||||

> [!IMPORTANT]
> \* Los tamaños de almacenamiento mayores que la cantidad de almacenamiento incluida están en su versión preliminar y pueden generar costos adicionales. Para obtener información detallada, vea [Precios de SQL Database](https://azure.microsoft.com/pricing/details/sql-database/). 
>
> \* En el nivel Premium, más de 1 TB de almacenamiento se encuentra actualmente disponible en las siguientes regiones: Este de Australia, Sudeste de Australia, Sur de Brasil, Centro de Canadá, Este de Canadá, Centro de EE. UU., Centro de Francia, Centro de Alemania, Este de Japón, Oeste de Japón, Centro de Corea, Centro y Norte de EE. UU., Europa del Norte, Centro y Sur de EE. UU., Sudeste Asiático, Sur de Reino Unido, Oeste de Reino Unido, Este de EE. UU. 2, Oeste de EE. UU., Virginia Gob. EE. UU. y Europa Occidental. Consulte [Limitaciones actuales P11-P15](sql-database-resource-limits.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb).  
> 

Para más información sobre las opciones específicas de niveles de rendimiento y de tamaño de almacenamiento disponibles, consulte [Límites de recursos de SQL Database](sql-database-resource-limits.md).


## <a name="next-steps"></a>Pasos siguientes

- Más información sobre [recursos de bases de datos únicas](sql-database-single-database-resources.md).
- Más información sobre grupos elásticos en [este artículo](sql-database-elastic-pool.md).
- Más información sobre [límites, cuotas y restricciones de suscripción y servicios de Azure](../azure-subscription-service-limits.md).
* Más información sobre [DTU y eDTU](sql-database-what-is-a-dtu.md).
* Más información sobre la supervisión del uso de DTU en [Supervisión y optimización del rendimiento](sql-database-troubleshoot-performance.md).

