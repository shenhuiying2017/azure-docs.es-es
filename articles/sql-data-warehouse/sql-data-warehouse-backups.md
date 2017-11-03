---
title: "Copias de seguridad de Azure SQL Data Warehouse: instantáneas, redundancia geográfica | Microsoft Docs"
description: "Aprenda sobre las copias de seguridad de base de datos integradas de SQL Data Warehouse que permiten restaurar una instancia de Azure SQL Data Warehouse a un punto de restauración o una región geográfica distinta."
services: sql-data-warehouse
documentationcenter: 
author: barbkess
manager: jhubbard
editor: 
ms.assetid: b5aff094-05b2-4578-acf3-ec456656febd
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.custom: backup-restore
ms.date: 10/23/2017
ms.author: jrj;barbkess
ms.openlocfilehash: e76349ef7a2afa02d4f9e5295f299bb8084d1e08
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2017
---
# <a name="backup-and-restore-in-sql-data-warehouse"></a>Copia de seguridad y restauración en SQL Data Warehouse
En este artículo se explican los aspectos específicos de las copias de seguridad en SQL Data Warehouse. Use copias de seguridad de almacenamiento de datos para restaurar una instantánea de base de datos en la región primaria, o bien restaure una copia de seguridad para su región con emparejamiento geográfico. 

## <a name="what-is-a-data-warehouse-backup"></a>¿Qué es una copia de seguridad de almacenamiento de datos?
Una copia de seguridad de almacenamiento de datos es la copia de su base de datos, que se puede usar para restaurar un almacenamiento de datos.  Dado que SQL Data Warehouse es un sistema distribuido, una copia de seguridad de almacenamiento de datos consta de muchos archivos que se almacenan en Azure Storage. Una copia de seguridad de almacenamiento de datos incluye las instantáneas de base de datos local y geográfica de todas las bases de datos y todos los archivos que están asociados a un almacén de datos. 

## <a name="local-snapshot-backups"></a>Copias de seguridad de la instantánea local
SQL Data Warehouse captura las instantáneas de su almacenamiento de datos a lo largo del día. Las instantáneas están disponibles durante siete días. SQL Data Warehouse admite un objetivo de punto de recuperación (RPO) de ocho horas. Puede restaurar su almacenamiento de datos de la región primaria a cualquiera de las instantáneas capturadas en los últimos siete días.

Para ver cuándo se inició la última instantánea, ejecute esta consulta en SQL Data Warehouse en línea. 

```sql
select   top 1 *
from     sys.pdw_loader_backup_runs 
order by run_id desc
;
```

## <a name="geo-backups"></a>Copias de seguridad geográficas
SQL Data Warehouse realiza una copia de seguridad geográfica una vez al día en un [centro de datos emparejado](../best-practices-availability-paired-regions.md). El RPO para una restauración geográfica es de 24 horas. Puede restaurar la copia de seguridad geográfica en el servidor en la región con emparejamiento geográfico. La copia de seguridad geográfica garantiza que pueda restaurar el almacenamiento de datos en caso de que no pueda acceder a las instantáneas de su región primaria.

Las copias de seguridad geográficas están activadas de manera predeterminada. Si el almacenamiento de datos está optimizado para ofrecer elasticidad, lo puede [rechazar](https://docs.microsoft.com/powershell/resourcemanager/Azurerm.sql/v2.1.0/Set-AzureRmSqlDatabaseGeoBackupPolicyredirectedfrom=msdn) si lo desea. No se pueden rechazar las copias de seguridad geográficas con el nivel de rendimiento Optimizado para Compute.

## <a name="backup-costs"></a>Costos de la copia de seguridad
Observará que la factura de Azure tiene un elemento de línea para Premium Storage de Azure y un elemento de línea para el almacenamiento con redundancia geográfica. El cargo de Premium Storage es el costo total del almacenamiento de sus datos en la región primaria, que incluye las instantáneas.  El cargo con redundancia geográfica abarca el costo de almacenamiento de las copias de seguridad geográficas.  

El costo total del almacenamiento de datos principal y de los siete días de instantáneas de Azure Blob se redondea al TB más cercano. Por ejemplo, si el almacenamiento de datos es de 1,5 TB y las instantáneas usan 100 GB, se le facturan 2 TB de datos según las tarifas de Azure Premium Storage. 

> [!NOTE]
> Cada instantánea está vacía inicialmente y crece a medida que se realizan cambios en el almacenamiento de datos principal. Todas las instantáneas aumentan de tamaño cuando cambia el almacenamiento de datos. Por lo tanto, los costos de almacenamiento de las instantáneas crecen según el porcentaje de cambio.
> 
> 

Si usa almacenamiento con redundancia geográfica, recibirá un cargo de almacenamiento por separado. El almacenamiento con redundancia geográfica se factura según la tarifa estándar de almacenamiento geográficamente redundante con acceso de lectura (RA-GRS).

Para más información sobre los precios de SQL Data Warehouse, consulte [Precios de SQL Data Warehouse](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).

## <a name="snapshot-retention-when-a-data-warehouse-is-paused"></a>Retención de instantáneas cuando un almacenamiento de datos está en pausa
SQL Data Warehouse no crea instantáneas y no caduca las copias de seguridad mientras un almacenamiento de datos está en pausa. Y tampoco cambia la antigüedad de la instantánea mientras el almacenamiento de datos está en pausa. La retención de instantáneas se basa en el número de días que el almacenamiento de datos está en línea, no en los días de calendario.

Por ejemplo, si una instantánea comienza el 1 de octubre a las 4 de la tarde y el almacenamiento de datos se detiene el 3 de octubre a las 4 de la tarde, las instantáneas tienen dos días de antigüedad. Cuando el almacenamiento de datos vuelve a estar en línea, la instantánea tiene dos días de antigüedad. Si el almacenamiento de datos vuelve a estar en línea el 5 de octubre a las 4 de la tarde, la instantánea tiene dos días de antigüedad y permanece cinco días más.

Cuando el almacenamiento de datos vuelve a estar en línea, SQL Data Warehouse reanuda las nuevas instantáneas y caduca aquellas que tienen más de siete días de datos.

## <a name="can-i-restore-a-dropped-data-warehouse"></a>¿Puedo restaurar un almacenamiento de datos en pausa?
Cuando se quita un almacenamiento de datos SQL Data Warehouse crea una instantánea final y la guarda durante siete días. Puede restaurar el almacenamiento de datos en el punto de restauración final durante la eliminación. 

> [!IMPORTANT]
> Si elimina una instancia de servidor de SQL lógica, todas las bases de datos que pertenecen a la instancia también se eliminan y no se pueden recuperar. No puede restaurar un servidor eliminado.
> 

## <a name="next-steps"></a>Pasos siguientes
Para restaurar un almacenamiento de datos SQL, consulte [Restauración de un almacenamiento de datos SQL](sql-data-warehouse-restore-database-overview.md).

Para ver una introducción a la continuidad empresarial, consulte [Información general sobre la continuidad empresarial](../sql-database/sql-database-business-continuity.md)
