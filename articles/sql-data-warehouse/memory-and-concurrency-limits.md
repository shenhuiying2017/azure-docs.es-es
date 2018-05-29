---
title: 'Límites de memoria y simultaneidad: Azure SQL Data Warehouse | Microsoft Docs'
description: Vea los límites de simultaneidad y memoria asignados a los distintos niveles de rendimiento y las clases de recursos en Azure SQL Data Warehouse.
services: sql-data-warehouse
author: ronortloff
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 05/07/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: 46d41e3ee85deb20f189bc9c82a255178f3d7eee
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/10/2018
ms.locfileid: "33942272"
---
# <a name="memory-and-concurrency-limits-for-azure-sql-data-warehouse"></a>Límites de memoria y simultaneidad para Azure SQL Data Warehouse
Vea los límites de simultaneidad y memoria asignados a los distintos niveles de rendimiento y las clases de recursos en Azure SQL Data Warehouse. Para obtener más información y para aplicar estas capacidades a un plan de administración de cargas de trabajo, consulte [Clases de recursos para la administración de cargas de trabajo](resource-classes-for-workload-management.md). 

Actualmente hay disponibles dos generaciones con SQL Data Warehouse: Gen1 y Gen2. Le recomendamos que aproveche Gen2 de SQL Data Warehouse para obtener el mejor rendimiento para la carga de trabajo de almacenamiento de datos. Gen2 presenta una nueva caché de disco de estado sólido de NVMe que mantiene los datos a los que se accede con más frecuencia cerca de las CPU. Esto quita la E/S remota de las cargas de trabajo más intensivas y exigentes. Además de rendimiento, Gen2 ofrece el mayor nivel de escala, lo que le permite escalar verticalmente hasta 30 000 unidades de almacenamiento de datos y proporcionar almacenamiento en columnas ilimitado. Aunque seguiremos admitiendo la generación anterior (Gen1) de SQL Data Warehouse y conservaremos las mismas características, le recomendamos que [actualice a Gen2](upgrade-to-latest-generation.md) lo antes posible. 

## <a name="data-warehouse-capacity-settings"></a>Configuración de la capacidad del almacenamiento de datos
Las siguientes tablas muestran la capacidad máxima para el almacenamiento de datos en diferentes niveles de rendimiento. Para cambiar el nivel de rendimiento, consulte [Escalabilidad horizontal de un proceso en Azure SQL Data Warehouse en Azure Portal](quickstart-scale-compute-portal.md).

### <a name="gen2"></a>Gen2

Gen2 proporciona 2,5 veces más memoria por consulta que Gen1. Esta memoria adicional ayuda a Gen2 a ofrecer un rendimiento rápido.  Los niveles de rendimiento de Gen2 oscilan entre DW1000c y DW30000c. 

| Nivel de rendimiento | Nodos de proceso | Distribuciones por nodo de proceso | Memoria por almacenamiento de datos (GB) |
|:-----------------:|:-------------:|:------------------------------:|:------------------------------:|
| DW1000c           | 2             | 30                             |   600                          |
| DW1500c           | 3             | 20                             |   900                          |
| DW2000c           | 4             | 15                             |  1200                          |
| DW2500c           | 5             | 12                             |  1.500                          |
| DW3000c           | 6             | 10                             |  1.800                          |
| DW5000c           | 10            | 6                              |  3000                          |
| DW6000c           | 12            | 5                              |  3600                          |
| DW7500c           | 15            | 4                              |  4500                          |
| DW10000c          | 20            | 3                              |  6000                          |
| DW15000c          | 30            | 2                              |  9000                          |
| DW30000c          | 60            | 1                              | 18000                          |

El valor máximo de DWU de Gen2 es DW30000c, que tiene 60 nodos de proceso y una distribución por nodo de proceso. Por ejemplo, un almacenamiento de datos de 600 TB con DW30000c procesa, aproximadamente, 10 TB por nodo de proceso.

### <a name="gen1"></a>Gen1

Los niveles de servicio de Gen1 oscilan entre DW100 y DW6000. 

| Nivel de rendimiento | Nodos de proceso | Distribuciones por nodo de proceso | Memoria por almacenamiento de datos (GB) |
|:-----------------:|:-------------:|:------------------------------:|:------------------------------:|
| DW100             | 1             | 60                             |  24                            |
| DW200             | 2             | 30                             |  48                            |
| DW300             | 3             | 20                             |  72                            |
| DW400             | 4             | 15                             |  96                            |
| DW500             | 5             | 12                             | 120                            |
| DW600             | 6             | 10                             | 144                            |
| DW1000            | 10            | 6                              | 240                            |
| DW1200            | 12            | 5                              | 288                            |
| DW1500            | 15            | 4                              | 360                            |
| DW2000            | 20            | 3                              | 480                            |
| DW3000            | 30            | 2                              | 720                            |
| DW6000            | 60            | 1                              | 1440                           |

## <a name="concurrency-maximums"></a>Valores máximos de simultaneidad
Para asegurarse de que cada consulta tenga recursos suficientes para ejecutarse de forma eficaz, SQL Data Warehouse realiza un seguimiento del uso de los recursos. Para ello, asigna espacios de simultaneidad a cada consulta. El sistema coloca las consultas en una cola en la que esperan hasta que haya suficientes [espacios de simultaneidad](resource-classes-for-workload-management.md#concurrency-slots) disponibles. Los espacios de simultaneidad también determinan la asignación de prioridades de CPU. Para obtener más información, consulte [Analyze your workload](analyze-your-workload.md) (Análisis de la carga de trabajo).

### <a name="gen2"></a>Gen2
 
**Clases de recursos estáticos**

En la tabla siguiente se muestra el número máximo de consultas simultáneas y espacios de simultaneidad para cada [clase de recurso estático](resource-classes-for-workload-management.md).  

| Nivel de servicios | N.º máximo de consultas simultáneas | Espacios de simultaneidad disponibles |staticrc10 | staticrc20 | staticrc30 | staticrc40 | staticrc50 | staticrc60 | staticrc70 | staticrc80 |
|:-------------:|:--------------------------:|:---------------------------:|:---------:|:----------:|:----------:|:----------:|:----------:|:----------:|:----------:|:----------:|
| DW1000c       | 32                         |   40                        | 1         | 2          | 4          | 8          | 16         | 32         | 32         |  32        |
| DW1500c       | 32                         |   60                        | 1         | 2          | 4          | 8          | 16         | 32         | 32         |  32        |
| DW2000c       | 48                         |   80                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         |  64        |
| DW2500c       | 48                         |  100                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         |  64        |
| DW3000c       | 64                         |  120                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW5000c       | 64                         |  200                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW6000c       | 128                        |  240                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW7500c       | 128                        |  300                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW10000c      | 128                        |  400                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW15000c      | 128                        |  600                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW30000c      | 128                        | 1200                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |

**Clases de recursos dinámicos**

> [!NOTE]
> La clase de recurso smallrc en Gen2 agrega memoria de forma dinámica conforme aumenta el nivel de servicio y solo admite un máximo de 32 consultas simultáneas.  Los espacios de simultaneidad y la memoria que usa smallrc aumentan conforme aumenta el nivel de servicio. 
>
>

En la tabla siguiente se muestra el número máximo de consultas simultáneas y espacios de simultaneidad para cada [clase de recurso dinámico](resource-classes-for-workload-management.md). A diferencia de Gen1, las clases de recursos dinámicos en Gen2 son realmente dinámicas.  Gen2 usa una asignación de porcentaje de memoria de 3-10-22-70 para las clases de recursos small-medium-large-xlarge en todos los niveles de servicio.

| Nivel de servicios | N.º máximo de consultas simultáneas | Espacios de simultaneidad disponibles | Ranuras utilizadas por smallrc | Ranuras utilizadas por mediumrc | Ranuras utilizadas por largerc | Ranuras utilizadas por xlargerc |
|:-------------:|:--------------------------:|:---------------------------:|:---------------------:|:----------------------:|:---------------------:|:----------------------:|
| DW1000c       | 32                         |   40                        | 1                     |  4                     |  8                    |  28                    |
| DW1500c       | 32                         |   60                        | 1                     |  6                     |  13                   |  42                    |
| DW2000c       | 32                         |   80                        | 2                     |  8                     |  17                   |  56                    |
| DW2500c       | 32                         |  100                        | 3                     | 10                     |  22                   |  70                    |
| DW3000c       | 32                         |  120                        | 3                     | 12                     |  26                   |  84                    |
| DW5000c       | 32                         |  200                        | 6                     | 20                     |  44                   | 140                    |
| DW6000c       | 32                         |  240                        | 7                     | 24                     |  52                   | 168                    |
| DW7500c       | 32                         |  300                        | 9                     | 30                     |  66                   | 210                    |
| DW10000c      | 32                         |  400                        | 12                    | 40                     |  88                   | 280                    |
| DW15000c      | 32                         |  600                        | 18                    | 60                     | 132                   | 420                    |
| DW30000c      | 32                         | 1200                        | 36                    | 120                    | 264                   | 840                    |



#### <a name="gen1"></a>Gen1

Clases de recursos estáticos

En la tabla siguiente, se muestra el número máximo de consultas simultáneas y espacios de simultaneidad para cada [clase de recurso estático](resource-classes-for-workload-management.md) en **Gen1**.

| Nivel de servicio | N.º máximo de consultas simultáneas | Espacios de simultaneidad máximos |staticrc10 | staticrc20 | staticrc30 | staticrc40 | staticrc50 | staticrc60 | staticrc70 | staticrc80 |
|:-------------:|:--------------------------:|:-------------------------:|:---------:|:----------:|:----------:|:----------:|:----------:|:----------:|:----------:|:----------:|
| DW100         | 4                          |   4                       | 1         | 2          | 4          | 4          |  4         |  4         |  4         |   4        |
| DW200         | 8                          |   8                       | 1         | 2          | 4          | 8          |  8         |  8         |  8         |   8        |
| DW300         | 12                         |  12                       | 1         | 2          | 4          | 8          |  8         |  8         |  8         |   8        |
| DW400         | 16                         |  16                       | 1         | 2          | 4          | 8          | 16         | 16         | 16         |  16        |
| DW500         | 20                         |  20                       | 1         | 2          | 4          | 8          | 16         | 16         | 16         |  16        |
| DW600         | 24                         |  24                       | 1         | 2          | 4          | 8          | 16         | 16         | 16         |  16        |
| DW1000        | 32                         |  40                       | 1         | 2          | 4          | 8          | 16         | 32         | 32         |  32        |
| DW1200        | 32                         |  48                       | 1         | 2          | 4          | 8          | 16         | 32         | 32         |  32        |
| DW1500        | 32                         |  60                       | 1         | 2          | 4          | 8          | 16         | 32         | 32         |  32        |
| DW2000        | 48                         |  80                       | 1         | 2          | 4          | 8          | 16         | 32         | 64         |  64        |
| DW3000        | 64                         | 120                       | 1         | 2          | 4          | 8          | 16         | 32         | 64         |  64        |
| DW6000        | 128                        | 240                       | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |

Clases de recursos dinámicos
> [!NOTE]
> La clase de recurso smallrc en Gen1 asigna una cantidad fija de memoria por consulta, de forma similar a la clase de recurso estático staticrc10.  Dado que smallrc es estático, tiene la capacidad de escalar hasta 128 consultas simultáneas. 
>
>

En la tabla siguiente, se muestra el número máximo de consultas simultáneas y espacios de simultaneidad para cada [clase de recurso dinámico](resource-classes-for-workload-management.md) en **Gen1**.

| Nivel de servicio | N.º máximo de consultas simultáneas | Espacios de simultaneidad disponibles | smallrc | mediumrc | largerc | xlargerc |
|:-------------:|:--------------------------:|:---------------------------:|:-------:|:--------:|:-------:|:--------:|
| DW100         |  4                         |   4                         | 1       |  1       |  2      |   4      |
| DW200         |  8                         |   8                         | 1       |  2       |  4      |   8      |
| DW300         | 12                         |  12                         | 1       |  2       |  4      |   8      |
| DW400         | 16                         |  16                         | 1       |  4       |  8      |  16      |
| DW500         | 20                         |  20                         | 1       |  4       |  8      |  16      |
| DW600         | 24                         |  24                         | 1       |  4       |  8      |  16      |
| DW1000        | 32                         |  40                         | 1       |  8       | 16      |  32      |
| DW1200        | 32                         |  48                         | 1       |  8       | 16      |  32      |
| DW1500        | 32                         |  60                         | 1       |  8       | 16      |  32      |
| DW2000        | 48                         |  80                         | 1       | 16       | 32      |  64      |
| DW3000        | 64                         | 120                         | 1       | 16       | 32      |  64      |
| DW6000        | 128                        | 240                         | 1       | 32       | 64      | 128      |


Cuando se alcanza alguno de estos umbrales, se ponen a la cola nuevas consultas y se ejecutan según la regla "primero en entrar, primero en salir".  A medida que las consultas finalizan y el número de consultas y espacios desciende por debajo de los límites, SQL Data Warehouse libera las consultas en cola. 

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre cómo aprovechar las clases de recursos para optimizar aún más la carga de trabajo, revise los artículos siguientes:
* [Resource classes for workload management](resource-classes-for-workload-management.md) (Clases de recursos para la administración de cargas de trabajo)
* [Analyzing your workload](analyze-your-workload.md) (Análisis de la carga de trabajo)

