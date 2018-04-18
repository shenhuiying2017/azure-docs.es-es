---
title: Actualización a la última generación de Azure SQL Data Warehouse | Microsoft Docs
description: Pasos para actualizar Azure SQL Data Warehouse a la última generación de arquitectura de almacenamiento y hardware de Azure.
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.services: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 04/02/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 6ea45398b0bf7fca43c75797313b7e683972b1ab
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/06/2018
---
# <a name="optimize-performance-by-upgrading-sql-data-warehouse"></a>Actualización de SQL Data Warehouse para optimizar el rendimiento

Ahora, puede actualizar sin problemas al nivel de rendimiento Optimizado para Compute en Azure Portal. Si tiene un almacenamiento de datos Optimizado para Elasticity, se recomienda actualizar a la última generación de hardware de Azure y a una arquitectura de almacenamiento mejorada. Podrá aprovechar las ventajas de rendimiento más rápido, mayor escalabilidad y almacenamiento ilimitado en columnas. 

## <a name="applies-to"></a>Se aplica a
Esta actualización se aplica a los almacenamientos de datos del nivel de rendimiento optimizado para elasticidad.

## <a name="sign-in-to-the-azure-portal"></a>Inicie sesión en el Portal de Azure.

Inicie sesión en el [Azure Portal](https://portal.azure.com/).

## <a name="before-you-begin"></a>Antes de empezar

> [!NOTE]
> A partir del 30/3, debe tener desactivada la [auditoría de nivel de servidor](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-auditing#subheading-8) antes de iniciar la actualización.
> 
>

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
   
   El segundo paso del proceso de actualización es la migración de datos ("Actualización: en línea"). La migración de datos es un proceso en segundo plano lento y en línea, durante el cual se mueven lentamente los datos en columnas desde la arquitectura de almacenamiento antigua Gen1 a la nueva arquitectura de almacenamiento Gen2 para aprovechar la caché de SSD local de Gen2. Durante este tiempo, el almacenamiento de datos estará en línea para consulta y carga. Todos los datos estarán disponibles para consulta tanto si se han migrado como si no. La migración de los datos se produce a una velocidad variable, según el tamaño de los datos, el nivel de rendimiento y el número de segmentos del almacén de columnas. 

5. **Recomendación opcional:** para acelerar el proceso en segundo plano de migración de los datos, se recomienda forzar inmediatamente el movimiento de datos mediante la ejecución de [Alter Index rebuild](https://docs.microsoft.com/en-us/azure/sql-data-warehouse/sql-data-warehouse-tables-index) en todas las tablas de almacén de columnas con un SLO y una clase de recurso más grandes. Esta operación se realiza sin conexión en comparación con el proceso en segundo plano lento; sin embargo, la migración de los datos será mucho más rápida donde pueda aprovechar la arquitectura de almacenamiento de Gen2 una vez completada con grupos de filas de alta calidad. 

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
 
