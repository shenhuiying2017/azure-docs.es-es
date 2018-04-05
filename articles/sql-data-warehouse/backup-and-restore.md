---
title: 'Copia de seguridad y restauración de Azure SQL Data Warehouse: instantáneas y redundancia geográfica | Microsoft Docs'
description: Obtenga información acerca de cómo funcionan la copia de seguridad y la restauración en Azure SQL Data Warehouse. Use copias de seguridad de almacenamiento de datos para restaurar el almacenamiento de datos a un punto de restauración en la región primaria o use copias de seguridad con redundancia geográfica para restaurar a una región geográfica diferente.
services: sql-data-warehouse
author: ronortloff
manager: jhubbard
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: design
ms.date: 03/28/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: 7f540bca0d2eb2c9009a386bd14a5beda2912014
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/30/2018
---
# <a name="backup-and-restore-in-azure-sql-data-warehouse"></a>Copia de seguridad y restauración en Azure SQL Data Warehouse
Obtenga información acerca de cómo funcionan la copia de seguridad y la restauración en Azure SQL Data Warehouse. Use copias de seguridad de almacenamiento de datos para restaurar el almacenamiento de datos a un punto de restauración en la región primaria o use copias de seguridad con redundancia geográfica para restaurar a una región geográfica diferente. 

## <a name="what-is-backup-and-restore"></a>¿Qué es la copia de seguridad y la restauración?
Una *copia de seguridad de almacenamiento de datos* es la copia de su base de datos, que se puede usar para restaurar un almacenamiento de datos.  Dado que SQL Data Warehouse es un sistema distribuido, una copia de seguridad de almacenamiento de datos consta de muchos archivos que se almacenan en Azure Storage. Una copia de seguridad de almacenamiento de datos incluye las instantáneas de base de datos local y geográfica de todas las bases de datos y todos los archivos que están asociados a un almacén de datos. 

Una *restauración de almacenamiento de datos* es un nuevo almacenamiento de datos que se crea a partir de una copia de seguridad de un almacenamiento de datos existente o eliminado. El almacenamiento de datos restaurado vuelve a crear el de copia de seguridad en un momento determinado. La restauración del almacenamiento de datos es una parte esencial de cualquier estrategia de recuperación ante desastres y continuidad empresarial, ya que vuelve a crear los datos tras daños o eliminaciones accidentales.

Las restauraciones locales y geográficas forman parte de las funcionalidades de recuperación ante desastres de SQL Data Warehouse. 

## <a name="local-snapshot-backups"></a>Copias de seguridad de la instantánea local
Las copias de seguridad de instantáneas locales son una característica integrada del servicio.  No es necesario habilitarlas. 

SQL Data Warehouse captura las instantáneas de su almacenamiento de datos a lo largo del día. Las instantáneas están disponibles durante siete días. SQL Data Warehouse admite un objetivo de punto de recuperación (RPO) de ocho horas. Puede restaurar su almacenamiento de datos de la región primaria a cualquiera de las instantáneas capturadas en los últimos siete días.

Para ver cuándo se inició la última instantánea, ejecute esta consulta en SQL Data Warehouse en línea. 

```sql
select   top 1 *
from     sys.pdw_loader_backup_runs 
order by run_id desc
;
```

### <a name="snapshot-retention-when-a-data-warehouse-is-paused"></a>Retención de instantáneas cuando un almacenamiento de datos está en pausa
SQL Data Warehouse no crea instantáneas y no caduca las copias de seguridad mientras un almacenamiento de datos está en pausa. Y tampoco cambia la antigüedad de la instantánea mientras el almacenamiento de datos está en pausa. La retención de instantáneas se basa en el número de días que el almacenamiento de datos está en línea, no en los días de calendario.

Por ejemplo, si una instantánea comienza el 1 de octubre a las 4 de la tarde y el almacenamiento de datos se detiene el 3 de octubre a las 4 de la tarde, las instantáneas tienen dos días de antigüedad. Cuando el almacenamiento de datos vuelve a estar en línea, la instantánea tiene dos días de antigüedad. Si el almacenamiento de datos vuelve a estar en línea el 5 de octubre a las 4 de la tarde, la instantánea tiene dos días de antigüedad y permanece cinco días más.

Cuando el almacenamiento de datos vuelve a estar en línea, SQL Data Warehouse reanuda las nuevas instantáneas y caduca aquellas que tienen más de siete días de datos.

### <a name="snapshot-retention-when-a-data-warehouse-is-dropped"></a>Retención de instantáneas cuando se quita un almacenamiento de datos
Cuando se quita un almacenamiento de datos SQL Data Warehouse crea una instantánea final y la guarda durante siete días. Puede restaurar el almacenamiento de datos en el punto de restauración final durante la eliminación. 

> [!IMPORTANT]
> Si elimina una instancia de servidor de SQL lógica, todas las bases de datos que pertenecen a la instancia también se eliminan y no se pueden recuperar. No puede restaurar un servidor eliminado.
> 

## <a name="geo-backups"></a>Copias de seguridad geográficas
SQL Data Warehouse realiza una copia de seguridad geográfica una vez al día en un [centro de datos emparejado](../best-practices-availability-paired-regions.md). El RPO para una restauración geográfica es de 24 horas. Puede restaurar la copia de seguridad geográfica en el servidor en la región con emparejamiento geográfico. Una copia de seguridad geográfica garantiza que pueda restaurar el almacenamiento de datos en caso de que no pueda acceder a las instantáneas de su región primaria.

Las copias de seguridad geográficas están activadas de manera predeterminada. Si el almacenamiento de datos está optimizado para ofrecer elasticidad, lo puede [rechazar](/powershell/module/azurerm.sql/set-azurermsqldatabasegeobackuppolicy) si lo desea. No se pueden rechazar las copias de seguridad geográficas con el nivel de rendimiento Optimizado para Compute.

## <a name="backup-costs"></a>Costos de la copia de seguridad
Observará que la factura de Azure tiene un elemento de línea para Premium Storage de Azure y un elemento de línea para el almacenamiento con redundancia geográfica. El cargo de Premium Storage es el costo total del almacenamiento de sus datos en la región primaria, que incluye las instantáneas.  El cargo con redundancia geográfica abarca el costo de almacenamiento de las copias de seguridad geográficas.  

El costo total del almacenamiento de datos principal y de los siete días de instantáneas de Azure Blob se redondea al TB más cercano. Por ejemplo, si el almacenamiento de datos es de 1,5 TB y las instantáneas usan 100 GB, se le facturan 2 TB de datos según las tarifas de Azure Premium Storage. 

> [!NOTE]
> Cada instantánea está vacía inicialmente y crece a medida que se realizan cambios en el almacenamiento de datos principal. Todas las instantáneas aumentan de tamaño cuando cambia el almacenamiento de datos. Por lo tanto, los costos de almacenamiento de las instantáneas crecen según el porcentaje de cambio.
> 
> 

Si usa almacenamiento con redundancia geográfica, recibirá un cargo de almacenamiento por separado. El almacenamiento con redundancia geográfica se factura según la tarifa estándar de almacenamiento geográficamente redundante con acceso de lectura (RA-GRS).

Para más información sobre los precios de SQL Data Warehouse, consulte [Precios de SQL Data Warehouse](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).

## <a name="restoring-from-restore-points"></a>Restauración a partir de puntos de restauración
Cada instantánea tiene un punto de restauración que representa la hora de inicio de la instantánea. Para restaurar un almacenamiento de datos, elija un punto de restauración y emita un comando de restauración.  

SQL Data Warehouse siempre restaura la copia de seguridad en un nuevo almacenamiento de datos. Puede mantener el almacenamiento de datos restaurado y el actual, o eliminar uno de ellos. Si quiere reemplazar el almacenamiento de datos actual por el restaurado, puede cambiarle el nombre mediante [ALTER DATABASE (Azure SQL Data Warehouse)](/sql/t-sql/statements/alter-database-azure-sql-data-warehouse) con la opción MODIFY NAME. 

Para restaurar un almacenamiento de datos, consulte [Restauración de un almacenamiento de datos mediante Azure Portal](sql-data-warehouse-restore-database-portal.md), [Restauración de un almacenamiento de datos mediante PowerShell](sql-data-warehouse-restore-database-powershell.md) o [Restauración de un almacenamiento de datos mediante T-SQL](sql-data-warehouse-restore-database-rest-api.md).

Para restaurar un almacenamiento de datos eliminado o en pausa, puede [crear una incidencia de soporte técnico](sql-data-warehouse-get-started-create-support-ticket.md). 


## <a name="geo-redundant-restore"></a>Restauración con redundancia geográfica
Puede restaurar el almacenamiento de datos en cualquier región que admita Azure SQL Data Warehouse en el nivel de rendimiento elegido. 

> [!NOTE]
> Para llevar a cabo una restauración con redundancia geográfica no puede haber anulado esta característica.
> 
> 

## <a name="restore-timeline"></a>Escala de tiempo de restauración
Puede restaurar una base de datos a cualquier punto de restauración disponible de los últimos siete días. Las instantáneas se inician entre cada cuatro y ocho horas y están disponibles durante siete días. Cuando una instantánea tiene una antigüedad superior a siete días, caduca y su punto de restauración ya no está disponible. 

Las copias de seguridad no se realizan en un almacenamiento de datos en pausa. Si su almacenamiento de datos está en pausa durante más de siete días, no tendrá ningún punto de restauración. 

## <a name="restore-costs"></a>Costos de restauración
Los cargos de almacenamiento por el almacenamiento de datos restaurado se facturan con la tarifa de Azure Premium Storage. 

Si se pausa un almacenamiento de datos restaurado, se le cobrará por el almacenamiento según la tarifa de Azure Premium Storage. La ventaja de pausar es que no se le cobra nada por los recursos de computación.

Para más información sobre los precios de SQL Data Warehouse, consulte [Precios de SQL Data Warehouse](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).

## <a name="restore-use-cases"></a>Casos de uso de restauración
El uso principal de la restauración del almacenamiento de datos es recuperar información tras las pérdidas de datos o daños accidentales. También puede utilizar la restauración del almacenamiento de datos para conservar una copia de seguridad durante más de siete días. Una vez restaurada la copia de seguridad, tiene el almacenamiento de datos en línea y puede pausarlo indefinidamente para ahorrar costos de computación. La base de datos en pausa genera gastos de almacenamiento según la tarifa de Azure Premium Storage. 

## <a name="next-steps"></a>Pasos siguientes
Para más información acerca de la planificación ante desastres, consulte [Información general sobre la continuidad empresarial](../sql-database/sql-database-business-continuity.md).
