---
title: Actualización a la última generación de Azure SQL Data Warehouse | Microsoft Docs
description: Actualización de Azure SQL Data Warehouse a la última generación de arquitectura de almacenamiento y hardware de Azure.
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 673386ad236f596aa4c64fe2e8c885fb86afe170
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/19/2018
---
# <a name="optimize-performance-by-upgrading-sql-data-warehouse"></a>Actualización de SQL Data Warehouse para optimizar el rendimiento
Actualización de Azure SQL Data Warehouse a la última generación de arquitectura de almacenamiento y hardware de Azure.

## <a name="why-upgrade"></a>¿Por qué actualizar?
Ahora, puede actualizar sin problemas al nivel de rendimiento Optimizado para Compute en Azure Portal. Si tiene un almacenamiento de datos optimizado para elasticidad, se recomienda actualizar. Mediante la actualización, puede usar la última generación de hardware de Azure y arquitectura de almacenamiento mejorada. Puede aprovechar las ventajas del rendimiento más rápido, mayor escalabilidad y almacenamiento ilimitado en columnas. 

## <a name="applies-to"></a>Se aplica a
Esta actualización se aplica a los almacenamientos de datos del nivel de rendimiento optimizado para elasticidad.

## <a name="sign-in-to-the-azure-portal"></a>Inicie sesión en el Portal de Azure.

Inicie sesión en el [Azure Portal](https://portal.azure.com/).

## <a name="before-you-begin"></a>Antes de empezar
> [!NOTE]
> Si su almacenamiento de datos existente Optimizado para Elasticity no está en una región donde esté disponible Optimizado para Compute, puede realizar la [restauración geográfica a Optimizado para Compute](https://docs.microsoft.com/en-us/azure/sql-data-warehouse/sql-data-warehouse-restore-database-powershell#restore-from-an-azure-geographical-region) mediante PowerShell en una región admitida.
> 
>

1. Si el almacenamiento de datos optimizado para elasticidad que se va a actualizar se pausa, [reanude el almacenamiento de datos](pause-and-resume-compute-portal.md).
2. Prepárese para unos minutos de inactividad. 



## <a name="start-the-upgrade"></a>Iniciar la actualización

1. Vaya a su almacenamiento de datos Optimizado para Elasticity en Azure Portal y haga clic en **Upgrade to Optimized for Compute** (Actualizar a Optimizado para Compute): ![Upgrade_1](./media/sql-data-warehouse-upgrade-to-latest-generation/Upgrade_to_Gen2_1.png).

2. De forma predeterminada, **seleccione el nivel de rendimiento sugerido** para el almacenamiento de datos en función del nivel de rendimiento actual en Optimizado para Elasticity mediante la asignación siguiente:
    
| Optimizado para Elasticity | Optimizado para Compute |
| :----------------------: | :-------------------: |
|      DW100: DW1000      |        DW1000c        |
|          DW1200          |        DW1500c        |
|          DW1500          |        DW1500c        |
|          DW2000          |        DW2000c        |
|          DW3000          |        DW3000c        |
|          DW6000          |        DW6000c        |


3. Asegúrese de que la carga de trabajo ha terminado de ejecutarse y está en modo inactivo antes de actualizar. Unos minutos antes de que el almacenamiento de datos vuelva a estar en línea como Optimizado para Compute, experimentará tiempo de inactividad. Haga clic en **Actualizar**. Actualmente, el precio del nivel de rendimiento Optimizado para Compute está medio desactivado durante el período de versión preliminar.
    
    ![Upgrade_2](./media/sql-data-warehouse-upgrade-to-latest-generation/Upgrade_to_Gen2_2.png)

4. Para **supervisar la actualización**, compruebe el estado en Azure Portal:

   ![Upgrade3](./media/sql-data-warehouse-upgrade-to-latest-generation/Upgrade_to_Gen2_3.png)
   
   El primer paso del proceso de actualización recorre la operación de escalado ("Actualización: sin conexión") donde todas las sesiones se terminan y las conexiones se descartan. 
   
   El segundo paso del proceso de actualización es la migración de datos ("Actualización: en línea"). La migración de datos es un proceso en segundo plano lento y en línea, durante el cual se mueven lentamente los datos en columnas desde la arquitectura de almacenamiento antigua hasta la nueva arquitectura de almacenamiento mediante una caché de SSD local. Durante este tiempo, el almacenamiento de datos estará en línea para consulta y carga. Todos los datos estarán disponibles para consulta tanto si se han migrado como si no. La migración de los datos se produce a una velocidad variable, según el tamaño de los datos, el nivel de rendimiento y el número de segmentos del almacén de columnas. 

5. **Recomendación opcional:** para acelerar el proceso en segundo plano de migración de los datos, se recomienda forzar inmediatamente el movimiento de datos mediante la ejecución de [Alter Index rebuild](https://docs.microsoft.com/en-us/azure/sql-data-warehouse/sql-data-warehouse-tables-index) en todas las tablas de almacén de columnas con un SLO y una clase de recurso más grandes. Esta operación se realiza sin conexión en comparación con el proceso en segundo plano lento; sin embargo, la migración de los datos será mucho más rápida donde pueda aprovechar la nueva arquitectura de almacenamiento mejorada una vez completada con grupos de filas de alta calidad. 

La consulta siguiente genera los comandos Alter Index Rebuild necesarios para acelerar el proceso de migración de datos:

```sql
SELECT 'ALTER INDEX [' + idx.NAME + '] ON [' 
       + Schema_name(tbl.schema_id) + '].[' 
       + Object_name(idx.object_id) + '] REBUILD ' + ( CASE 
                                                         WHEN ( 
                                                     (SELECT Count(*) 
                                                      FROM   sys.partitions 
                                                             part2 
                                                      WHERE  part2.index_id 
                                                             = idx.index_id 
                                                             AND 
                                                     idx.object_id = 
                                                     part2.object_id) 
                                                     > 1 ) THEN 
              ' PARTITION = ' 
              + Cast(part.partition_number AS NVARCHAR(256)) 
              ELSE '' 
                                                       END ) + '; SELECT ''[' + 
              idx.NAME + '] ON [' + Schema_name(tbl.schema_id) + '].[' + 
              Object_name(idx.object_id) + '] ' + ( 
              CASE 
                WHEN ( (SELECT Count(*) 
                        FROM   sys.partitions 
                               part2 
                        WHERE 
                     part2.index_id = 
                     idx.index_id 
                     AND idx.object_id 
                         = part2.object_id) > 1 ) THEN 
              ' PARTITION = ' 
              + Cast(part.partition_number AS NVARCHAR(256)) 
              + ' completed'';' 
              ELSE ' completed'';' 
                                                    END ) 
FROM   sys.indexes idx 
       INNER JOIN sys.tables tbl 
               ON idx.object_id = tbl.object_id 
       LEFT OUTER JOIN sys.partitions part 
                    ON idx.index_id = part.index_id 
                       AND idx.object_id = part.object_id 
WHERE  idx.type_desc = 'CLUSTERED COLUMNSTORE'; 
```



## <a name="next-steps"></a>Pasos siguientes
El almacenamiento de datos actualizado está en línea. Para aprovechar las ventajas de la arquitectura mejorada, consulte [Clases de recursos para la administración de cargas de trabajo](resource-classes-for-workload-management.md).
 
