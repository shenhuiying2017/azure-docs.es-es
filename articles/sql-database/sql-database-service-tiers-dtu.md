---
title: 'Servicio Azure SQL Database: DTU | Microsoft Docs'
description: Obtenga información acerca de los niveles de servicio para las bases de datos de grupo y únicas a fin de proporcionar niveles de rendimiento y tamaños de almacenamiento.
services: sql-database
author: CarlRabeler
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: article
ms.date: 04/09/2018
manager: craigg
ms.author: carlrab
ms.openlocfilehash: 6e282291a6e6e219bb275dd4da91f3815590adc1
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/28/2018
ms.locfileid: "32196008"
---
# <a name="dtu-based-purchasing-model-for-azure-sql-database"></a>Modelo de compra basado en DTU de Azure SQL Database 


[Azure SQL Database](sql-database-technical-overview.md) ofrece dos modelos de compra para recursos de proceso, almacenamiento y E/S: uno basado en DTU y el otro en núcleos virtuales (que se encuentra en versión preliminar). En la tabla y el gráfico siguientes se comparan y contrastan estos dos modelos de compra.

> [!IMPORTANT]
> Consulte este artículo sobre el [modelo de compra basado en núcleos virtuales (versión preliminar)](sql-database-service-tiers-vcore.md) si desea obtener más información al respecto.


|**Modelo de compra**|**Descripción**|**Más adecuado para**|
|---|---|---|
|Modelo basado en DTU|Este modelo se basa en una medida agrupada de recursos de proceso, almacenamiento y E/S. Los niveles de rendimiento se expresan como unidades de transmisión de datos (DTU) para las bases de datos únicas y como unidades de transmisión de datos elásticas (eDTU) para los grupos de bases de datos elásticas. Para más información sobre las DTU y las eDTU, consulte [¿Qué son las DTU y las eDTU?](sql-database-what-is-a-dtu.md)|Recomendado para los clientes que desean opciones de recursos simples y configuradas previamente.| 
|Modelo basado en núcleos virtuales|Este modelo le permite escalar los recursos de proceso y almacenamiento de manera independiente. También permite usar Ventaja híbrida de Azure para SQL Server para ahorrar en los costos.|Recomendado para los clientes que valoran la flexibilidad, el control y la transparencia.|
||||  

![Modelo de precios](./media/sql-database-service-tiers/pricing-model.png)

## <a name="dtu-based-purchasing-model"></a>Modelo de compra basado en DTU

La unidad de rendimiento de base de datos (DTU) representa una medida combinada de CPU, memoria, lecturas y escrituras. El modelo de compra basado en DTU ofrece un conjunto de agrupaciones preconfiguradas de recursos de proceso y almacenamiento incluido para impulsar diferentes niveles de rendimiento de la aplicación. Los clientes que prefieren la simplicidad de una agrupación preconfigurada y pagos fijos cada mes pueden encontrar el modelo basado en DTU más adecuado para sus necesidades. En el modelo de compra basado en DTU, los clientes pueden elegir entre los niveles de servicio **Básico**, **Estándar** y **Premium** tanto para [bases de datos únicas](sql-database-single-database-resources.md) como para [grupos elásticos](sql-database-elastic-pool.md). Los niveles de servicio se diferencian por una variedad de niveles de rendimiento con una cantidad fija de almacenamiento incluido, un período de retención fijo para copias de seguridad y un precio fijo. Todos los niveles de servicio proporcionan la flexibilidad de cambiar los niveles de rendimiento sin tiempo de inactividad. Las bases de datos únicas y los grupos elásticos se facturan por horas en función del nivel de servicio y el nivel de rendimiento.

> [!IMPORTANT]
> Instancia administrada de Azure SQL Database, actualmente en versión preliminar pública, no admite un modelo de compra basado en DTU. Para más información, consulte [Instancia administrada de Azure SQL Database](sql-database-managed-instance.md). 

## <a name="choosing-a-service-tier-in-the-dtu-based-purchasing-model"></a>Selección de un nivel de servicio en el modelo de compra basado en DTU

La selección de un nivel de servicio depende sobre todo de los requisitos de continuidad del negocio, de almacenamiento y de rendimiento.
||Básica|Estándar|Premium|
| :-- | --: |--:| --:| --:| 
|Carga de trabajo de destino|Desarrollo y producción|Desarrollo y producción|Desarrollo y producción||
|Acuerdo de Nivel de Servicio de tiempo de actividad|99,99%|99,99%|99,99%|N/D en versión preliminar|
|Retención de copias de seguridad|7 días|35 días|35 días|
|CPU|Bajo|Bajo, medio, alto|Medio, alto|
|Rendimiento de E/S (aproximado) |2,5 IOPS por DTU| 2,5 IOPS por DTU | 48 IOPS por DTU|
|Latencia de E/S (aproximada)|5 ms (lectura), 10 ms (escritura)|5 ms (lectura), 10 ms (escritura)|2 ms (lectura/escritura)|
|Índice de almacén de columnas |N/D|S3 y versiones posteriores|Compatible|
|OLTP en memoria (optimización en memoria|N/D|N/D|Compatible|
|||||

## <a name="performance-level-and-storage-size-limits-in-the-dtu-based-purchasing-model"></a>Nivel de rendimiento y límites de tamaño de almacenamiento en el modelo de compra basado en DTU

Los niveles de rendimiento se expresan como unidades de transmisión de datos (DTU) para las bases de datos únicas y como unidades de transmisión de datos elásticas (eDTU) para los grupos de bases de datos elásticas. Para más información sobre las DTU y las eDTU, consulte [¿Qué son las DTU y las eDTU?](sql-database-what-is-a-dtu.md)

### <a name="single-databases"></a>Bases de datos únicas

||Básica|Estándar|Premium|
| :-- | --: | --: | --: | --: |
| Tamaño máximo de almacenamiento* | 2 GB | 1 TB | 4 TB  | 
| Cantidad máxima de DTU | 5 | 3000 | 4000 | |
||||||

Para más información sobre niveles de rendimiento y opciones de tamaño de almacenamiento específicos disponibles para bases de datos únicas, consulte [SQL Database DTU-based resource limits for single databases](sql-database-dtu-resource-limits.md#single-database-storage-sizes-and-performance-levels) (Límites de recursos basados en DTU de SQL Database para bases de datos únicas).

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
> -  Los tamaños de almacenamiento mayores que la cantidad de almacenamiento incluida están en su versión preliminar y pueden generar costos adicionales. Para obtener información detallada, vea [Precios de SQL Database](https://azure.microsoft.com/pricing/details/sql-database/). 
> -  En el nivel Premium, más de 1 TB de almacenamiento se encuentra actualmente disponible en las siguientes regiones: Este de Australia, Sudeste de Australia, Sur de Brasil, Centro de Canadá, Este de Canadá, Centro de EE. UU., Centro de Francia, Centro de Alemania, Japón Oriental, Japón Occidental, Centro de Corea del Sur, Centro y Norte de EE. UU., Europa del Norte, Centro y Sur de EE. UU., Asia Suroriental, Sur de Reino Unido, Oeste de Reino Unido, Este de EE. UU. 2, Oeste de EE. UU., US Gov Virginia. y Europa Occidental. Consulte [Limitaciones actuales P11-P15](sql-database-dtu-resource-limits.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb).  

Para más información sobre niveles de rendimiento y opciones de tamaño de almacenamiento específicos disponibles para grupos elásticos, consulte [SQL Database DTU-based resource limits](sql-database-dtu-resource-limits.md#elastic-pool-storage-sizes-and-performance-levels) (Límites de recursos basados en DTU de SQL Database).



## <a name="next-steps"></a>Pasos siguientes

- Para más información sobre niveles de rendimiento y opciones de tamaño de almacenamiento específicos disponibles, consulte [SQL Database DTU-based resource limits](sql-database-dtu-resource-limits.md) (Límites de recursos basados en DTU de SQL Database para bases de datos únicas) y [SQL Database vCore-based resource limits](sql-database-vcore-resource-limits.md) (Límites de recursos basados en núcleos virtuales de SQL Database).
- Consulte [Preguntas más frecuentes sobre SQL Database](sql-database-faq.md) para conocer las respuestas a las preguntas más frecuentes.
- Información sobre [límites, cuotas y restricciones de suscripción y servicios de Azure](../azure-subscription-service-limits.md).
