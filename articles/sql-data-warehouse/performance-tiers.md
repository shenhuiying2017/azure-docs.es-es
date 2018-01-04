---
title: Niveles de rendimiento de Azure SQL Data Warehouse | Microsoft Docs
description: "Introducción a los niveles de rendimiento Optimizado para Elasticity y Optimizado para Compute disponibles en Azure SQL Data Warehouse."
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: jhubbard
editor: 
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: performance
ms.date: 11/10/2017
ms.author: jrj;barbkess
ms.openlocfilehash: 03881c12faed723999e97431e4a69fdeb6bfa10d
ms.sourcegitcommit: 9ea2edae5dbb4a104322135bef957ba6e9aeecde
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/03/2018
---
# <a name="azure-sql-data-warehouse-performance-tiers-preview"></a>Niveles de rendimiento de Azure SQL Data Warehouse (versión preliminar)
SQL Data Warehouse ofrece dos niveles de rendimiento optimizados para cargas de trabajo de análisis. En este artículo se explican los conceptos de niveles de rendimiento que le ayudarán a elegir el más adecuado para su carga de trabajo. 

## <a name="what-is-a-performance-tier"></a>¿Qué es un nivel de rendimiento?
Un nivel de rendimiento es una opción que determina la configuración del almacenamiento de datos. Esta opción es una de las primeras decisiones que toma al crear un almacenamiento de datos.  

> [!VIDEO https://channel9.msdn.com/Events/Connect/2017/T140/player]

- El **nivel de rendimiento Optimizado para Elasticity** separa las capas de proceso y almacenamiento en la arquitectura. Esta opción resulta muy útil para las cargas de trabajo que pueden aprovechar al máximo la separación entre proceso y almacenamiento, ya que se escala frecuentemente para admitir periodos breves de máxima actividad. Este nivel de proceso tiene el precio de venta al público de entrada más bajo y se puede escalar para admitir la mayoría de las cargas de trabajo del cliente.

- El **nivel de rendimiento Optimizado para Compute** usa el hardware de Azure más reciente para introducir una nueva memoria caché de disco de estado sólido NVMe que mantiene los datos a los que se accede con mayor frecuencia cerca de las CPU, es decir, exactamente donde deben estar. Al organizar automáticamente el almacenamiento en niveles, este nivel de rendimiento resulta muy útil para las consultas complejas, ya que todas las E/S se mantienen de forma local en la capa de proceso. Además, el almacén de columnas se ha mejorado para almacenar una cantidad ilimitada de datos en SQL Data Warehouse. El nivel de rendimiento Optimizado para Compute proporciona un mayor nivel de escalabilidad y permite escalar hasta 30,000 unidades de almacenamiento de datos de proceso (cDWU). Elija este nivel para las cargas de trabajo que requieren un rendimiento continuo e increíblemente rápido.

## <a name="service-levels"></a>Niveles de servicio
El objetivo de nivel de servicio (SLO) es la opción de escalabilidad que determina el nivel de costo y el rendimiento del almacenamiento de datos. Los niveles de servicio para la escala del nivel de rendimiento Optimizado para Compute se miden en unidades de almacenamiento de datos de proceso (cDWU); por ejemplo, DW2000c. Los niveles de servicio del nivel Optimizado para Elasticity se miden en DWU; por ejemplo, DW2000. Para obtener más información, consulte [qué es una unidad de almacenamiento de datos](what-is-a-data-warehouse-unit-dwu-cdwu.md).

En T-SQL, el valor de SERVICE_OBJECTIVE determina el nivel de servicio y el nivel de rendimiento del almacenamiento de datos.

```sql
--Optimized for Elasticity
CREATE DATABASE myElasticSQLDW
WITH
(    SERVICE_OBJECTIVE = 'DW1000'
)
;

--Optimized for Compute
CREATE DATABASE myComputeSQLDW
WITH
(    SERVICE_OBJECTIVE = 'DW1000c'
)
;
```

## <a name="memory-maximums"></a>Valores máximos de memoria
Los niveles de rendimiento tienen perfiles de memoria distintos, lo que se traduce en una cantidad de memoria por consulta diferente. El nivel de rendimiento Optimizado para Compute proporciona 2.5 veces más memoria por consulta que el nivel de rendimiento Optimizado para Elasticity. Esta memoria adicional ayuda al nivel de rendimiento Optimizado para Compute a ofrecer su rendimiento de alta velocidad. La memoria adicional por consulta también permite ejecutar más consultas al mismo tiempo, ya que las consultas pueden usar [clases de recursos](resource-classes-for-workload-management.md) inferiores. 

### <a name="optimized-for-elasticity"></a>Optimizado para Elasticity

Los niveles de servicio para el nivel de rendimiento Optimizado para Elasticity oscilan entre DW100 y DW6000. 

| Nivel de servicio | N.º máximo de consultas simultáneas | Nodos de proceso | Distribuciones por nodo de proceso | Memoria máxima por distribución (MB) | Memoria máxima por almacenamiento de datos (GB) |
|:-------------:|:----------------------:|:-------------:|:------------------------------:|:--------------------------------:|:----------------------------------:|
| DW100         | 4                      | 1             | 60                             | 400                              |  24                                |
| DW200         | 8                      | 2             | 30                             | 800                              |  48                                |
| DW300         | 12                     | 3             | 20                              | 1,200                            |  72                                |
| DW400         | 16                     | 4             | 15                             | 1600                            |  96                                |
| DW500         | 20                      | 5             | 12                             | 2.000                            | 120                                |
| DW600         | 24                     | 6             | 10                             | 2,400                            | 144                                |
| DW1000        | 32                     | 10            | 6                              | 4.000                            | 240                                |
| DW1200        | 32                     | 12            | 5                              | 4,800                            | 288                                |
| DW1500        | 32                     | 15            | 4                              | 6,000                            | 360                                |
| DW2000        | 32                     | 20             | 3                              | 8.000                            | 480                                |
| DW3000        | 32                     | 30            | 2                              | 12,000                           | 720                                |
| DW6000        | 32                     | 60            | 1                              | 24,000                           | 1440                               |

### <a name="optimized-for-compute"></a>Optimizado para Compute

Los niveles de servicio para el nivel de rendimiento Optimizado para Compute oscilan entre DW1000c y DW30000c. 

| Nivel de servicio | N.º máximo de consultas simultáneas | Nodos de proceso | Distribuciones por nodo de proceso | Memoria máxima por distribución (GB) | Memoria máxima por almacenamiento de datos (GB) |
|:-------------:|:----------------------:|:-------------:|:------------------------------:|:--------------------------------:|:----------------------------------:|
| DW1000c       | 32                     | 2             | 30                             |  10                              |   600                              |
| DW1500c       | 32                     | 3             | 20                              |  15                              |   900                              |
| DW2000c       | 32                     | 4             | 15                             |  20                               |  1200                              |
| DW2500c       | 32                     | 5             | 12                             |  25                              |  1.500                              |
| DW3000c       | 32                     | 6             | 10                             |  30                              |  1.800                              |
| DW5000c       | 32                     | 10            | 6                              |  50                              |  3000                              |
| DW6000c       | 32                     | 12            | 5                              |  60                              |  3600                              |
| DW7500c       | 32                     | 15            | 4                              |  75                              |  4500                              |
| DW10000c      | 32                     | 20             | 3                              | 100                              |  6000                              |
| DW15000c      | 32                     | 30            | 2                              | 150                              |  9000                              |
| DW30000c      | 32                     | 60            | 1                              | 300                              | 18000                              |

El valor máximo de cDWU es DW30000c, que tiene 60 nodos de proceso y una distribución por nodo de proceso. Por ejemplo, un almacenamiento de datos de 600 TB con DW30000c procesa, aproximadamente, 10 TB por nodo de proceso.

## <a name="concurrency-maximums"></a>Valores máximos de simultaneidad
SQL Data Warehouse proporciona una simultaneidad líder en el sector en un único almacenamiento de datos. Para asegurarse de que cada consulta tenga recursos suficientes para ejecutarse de forma eficaz, el sistema realiza un seguimiento del uso de los recursos de proceso. Para hacerlo, asigna espacios de simultaneidad a cada consulta. El sistema coloca las consultas en una cola en la que esperan hasta que haya suficientes espacios de simultaneidad disponibles. 

Los espacios de simultaneidad también determinan la asignación de prioridades de CPU. Para obtener más información, consulte [Analyze your workload](analyze-your-workload.md) (Análisis de la carga de trabajo).

### <a name="concurrency-slots"></a>Espacios de simultaneidad
Los espacios de simultaneidad son una manera cómoda de realizar un seguimiento de los recursos disponibles para la ejecución de la consulta. Son como las entradas que compra para reservar un asiento en un concierto porque estos son limitados. Del mismo modo, SQL Data Warehouse tiene una cantidad limitada de recursos de proceso. Para reservar recursos de proceso, las consultas adquieren espacios de simultaneidad. Para que una consulta se pueda ejecutar, debe poder reservar suficientes espacios de simultaneidad. Cuando una consulta finaliza, libera sus espacios de simultaneidad. 

* El nivel de rendimiento Optimizado para Elasticity permite escalar hasta 240 espacios de simultaneidad.
* El nivel de rendimiento Optimizado para Compute permite escalar hasta 1200 espacios de simultaneidad.

Cada consulta consume uno o varios espacios de simultaneidad, o ninguno. Las consultas del sistema y algunas consultas sencillas no consumen ningún espacio. De forma predeterminada, las consultas que se rigen por clases de recursos requieren un espacio de simultaneidad. Las consultas más complejas pueden requerir espacios de simultaneidad adicionales.  

- Una consulta que se ejecute con 10 espacios de simultaneidad puede tener acceso a 5 veces más recursos de proceso que una consulta que se ejecute con 2 espacios de simultaneidad.
- Si cada consulta requiere 10 espacios de simultaneidad y hay 40, solo se pueden ejecutar 4 consultas simultáneamente.
 
Solo las consultas regidas por recursos consumen espacios de simultaneidad. El número exacto de espacios de simultaneidad que se usa está determinado por la [clase de recurso](resource-classes-for-workload-management.md) de la consulta.

### <a name="optimized-for-compute"></a>Optimizado para Compute
En la tabla siguiente se muestra el número máximo de consultas simultáneas y espacios de simultaneidad para cada [clase de recurso dinámico](resource-classes-for-workload-management.md).  Se aplican al nivel de rendimiento Optimizado para Compute.

**Clases de recursos dinámicos**
| Nivel de servicios | N.º máximo de consultas simultáneas | Espacios de simultaneidad disponibles | Ranuras utilizadas por smallrc | Ranuras utilizadas por mediumrc | Ranuras utilizadas por largerc | Ranuras utilizadas por xlargerc |
|:-------------:|:--------------------------:|:---------------------------:|:---------------------:|:----------------------:|:---------------------:|:----------------------:|
| DW1000c       | 32                         |   40                        | 1                     |  8                     |  16                   |  32                    |
| DW1500c       | 32                         |   60                        | 1                     |  8                     |  16                   |  32                    |
| DW2000c       | 32                         |   80                        | 1                     | 16                     |  32                   |  64                    |
| DW2500c       | 32                         |  100                        | 1                     | 16                     |  32                   |  64                    |
| DW3000c       | 32                         |  120                        | 1                     | 16                     |  32                   |  64                    |
| DW5000c       | 32                         |  200                        | 1                     | 32                     |  64                   | 128                    |
| DW6000c       | 32                         |  240                        | 1                     | 32                     |  64                   | 128                    |
| DW7500c       | 32                         |  300                        | 1                     | 64                     | 128                   | 128                    |
| DW10000c      | 32                         |  400                        | 1                     | 64                     | 128                   | 256                    |
| DW15000c      | 32                         |  600                        | 1                     | 64                     | 128                   | 256                    |
| DW30000c      | 32                         | 1200                        | 1                     | 64                     | 128                   | 256                    |

**Clases de recursos estáticos**

En la tabla siguiente se muestra el número máximo de consultas simultáneas y espacios de simultaneidad para cada [clase de recurso estático](resource-classes-for-workload-management.md).  

| Nivel de servicios | N.º máximo de consultas simultáneas | Espacios de simultaneidad disponibles |staticrc10 | staticrc20 | staticrc30 | staticrc40 | staticrc50 | staticrc60 | staticrc70 | staticrc80 |
|:-------------:|:--------------------------:|:---------------------------:|:---------:|:----------:|:----------:|:----------:|:----------:|:----------:|:----------:|:----------:|
| DW1000c       | 32                         |   40                        | 1         | 2          | 4          | 8          | 16         | 32         | 32         |  32        |
| DW1500c       | 32                         |   60                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         |  64        |
| DW2000c       | 32                         |   80                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         |  64        |
| DW2500c       | 32                         |  100                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         |  64        |
| DW3000c       | 32                         |  120                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW5000c       | 32                         |  200                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW6000c       | 32                         |  240                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW7500c       | 32                         |  300                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW10000c      | 32                         |  400                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW15000c      | 32                         |  600                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW30000c      | 32                         | 1200                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |

### <a name="optimized-for-elasticity"></a>Optimizado para Elasticity
En la tabla siguiente se muestra el número máximo de consultas simultáneas y espacios de simultaneidad para cada [clase de recurso dinámico](resource-classes-for-workload-management.md).  Se aplican al nivel de rendimiento Optimizado para Elasticity.

**Clases de recursos dinámicos**

| Nivel de servicio | N.º máximo de consultas simultáneas | Espacios de simultaneidad disponibles | smallrc | mediumrc | largerc | xlargerc |
|:-------------:|:--------------------------:|:---------------------------:|:-------:|:--------:|:-------:|:--------:|
| DW100         |  4                         |   4                         | 1       |  1       |  2      |   4      |
| DW200         |  8                         |   8                         | 1       |  2       |  4      |   8      |
| DW300         | 12                         |  12                         | 1       |  2       |  4      |   8      |
| DW400         | 16                         |  16                         | 1       |  4       |  8      |  16      |
| DW500         | 20                          |  20                          | 1       |  4       |  8      |  16      |
| DW600         | 24                         |  24                         | 1       |  4       |  8      |  16      |
| DW1000        | 32                         |  40                         | 1       |  8       | 16      |  32      |
| DW1200        | 32                         |  48                         | 1       |  8       | 16      |  32      |
| DW1500        | 32                         |  60                         | 1       |  8       | 16      |  32      |
| DW2000        | 32                         |  80                         | 1       | 16       | 32      |  64      |
| DW3000        | 32                         | 120                         | 1       | 16       | 32      |  64      |
| DW6000        | 32                         | 240                         | 1       | 32       | 64      | 128      |

**Clases de recursos estáticos** En la tabla siguiente se muestra el número máximo de consultas simultáneas y espacios de simultaneidad para cada [clase de recurso estático](resource-classes-for-workload-management.md).  Se aplican al nivel de rendimiento Optimizado para Elasticity.

| Nivel de servicio | N.º máximo de consultas simultáneas | Espacios de simultaneidad máximos |staticrc10 | staticrc20 | staticrc30 | staticrc40 | staticrc50 | staticrc60 | staticrc70 | staticrc80 |
|:-------------:|:--------------------------:|:-------------------------:|:---------:|:----------:|:----------:|:----------:|:----------:|:----------:|:----------:|:----------:|
| DW100         | 4                          |   4                       | 1         | 2          | 4          | 4          |  4         |  4         |  4         |   4        |
| DW200         | 8                          |   8                       | 1         | 2          | 4          | 8          |  8         |  8         |  8         |   8        |
| DW300         | 12                         |  12                       | 1         | 2          | 4          | 8          |  8         |  8         |  8         |   8        |
| DW400         | 16                         |  16                       | 1         | 2          | 4          | 8          | 16         | 16         | 16         |  16        |
| DW500         | 20                          |  20                        | 1         | 2          | 4          | 8          | 16         | 16         | 16         |  16        |
| DW600         | 24                         |  24                       | 1         | 2          | 4          | 8          | 16         | 16         | 16         |  16        |
| DW1000        | 32                         |  40                       | 1         | 2          | 4          | 8          | 16         | 32         | 32         |  32        |
| DW1200        | 32                         |  48                       | 1         | 2          | 4          | 8          | 16         | 32         | 32         |  32        |
| DW1500        | 32                         |  60                       | 1         | 2          | 4          | 8          | 16         | 32         | 32         |  32        |
| DW2000        | 32                         |  80                       | 1         | 2          | 4          | 8          | 16         | 32         | 64         |  64        |
| DW3000        | 32                         | 120                       | 1         | 2          | 4          | 8          | 16         | 32         | 64         |  64        |
| DW6000        | 32                         | 240                       | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |

Cuando se alcanza alguno de estos umbrales, se ponen a la cola nuevas consultas y se ejecutan según la regla "primero en entrar, primero en salir".  A medida que las consultas finalizan y el número de consultas y espacios desciende por debajo de los límites, SQL Data Warehouse libera las consultas en cola. 

## <a name="next-steps"></a>pasos siguientes

Para obtener más información sobre cómo aprovechar las clases de recursos para optimizar aún más la carga de trabajo, revise los artículos siguientes:
* [Resource classes for workload management](resource-classes-for-workload-management.md) (Clases de recursos para la administración de cargas de trabajo)
* [Analyzing your workload](analyze-your-workload.md) (Análisis de la carga de trabajo)

