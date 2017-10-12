---
title: "Copias de seguridad de Azure SQL Data Warehouse: instantáneas, redundancia geográfica | Microsoft Docs"
description: "Aprenda sobre las copias de seguridad de base de datos integradas de SQL Data Warehouse que permiten restaurar una instancia de Azure SQL Data Warehouse a un punto de restauración o una región geográfica distinta."
services: sql-data-warehouse
documentationcenter: 
author: Lakshmi1812
manager: jhubbard
editor: 
ms.assetid: b5aff094-05b2-4578-acf3-ec456656febd
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.custom: backup-restore
ms.date: 10/31/2016
ms.author: lakshmir;barbkess
ms.openlocfilehash: 54c0149a769e654139bbdf709802d49127f041ac
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="sql-data-warehouse-backups"></a>Copias de seguridad de SQL Data Warehouse
SQL Data Warehouse ofrece copias de seguridad locales y geográficas como parte de sus funcionalidades de copia de seguridad de almacenamiento de datos. Aquí se incluyen instantáneas de Azure Storage Blob y almacenamiento con redundancia geográfica. Use copias de seguridad de almacenamiento de datos para restaurar el almacenamiento de datos a un punto de restauración en la región primaria o a una región geográfica diferente. En este artículo se explican los aspectos específicos de las copias de seguridad en SQL Data Warehouse.

## <a name="what-is-a-data-warehouse-backup"></a>¿Qué es una copia de seguridad de almacenamiento de datos?
Una copia de seguridad de almacenamiento de datos son los datos que se pueden usar para restaurar un almacenamiento de datos a un momento específico.  Como SQL Data Warehouse es un sistema distribuido, una copia de seguridad de almacenamiento de datos consta de muchos archivos que se almacenan en blobs de Azure. 

Las copias de seguridad de base de datos son una parte esencial de cualquier estrategia de recuperación ante desastres y continuidad del negocio, ya que protegen los datos de daños o eliminaciones accidentales. Para más información, consulte [Información general sobre la continuidad empresarial](../sql-database/sql-database-business-continuity.md).

## <a name="data-redundancy"></a>Redundancia de datos
SQL Data Warehouse protege los datos al almacenarlos en Azure Premium Storage con redundancia local (LRS). Esta característica de Azure Storage almacena varias copias sincrónicas de los datos en el centro de datos local para garantizar la protección transparente de los datos en caso de errores localizados. Gracias a la redundancia de datos, se garantiza que estos puedan sobrevivir a problemas de infraestructura, por ejemplo, errores de disco. Asimismo, asegura la continuidad empresarial mediante una infraestructura de alta disponibilidad con tolerancia a errores.

Para más información sobre:

* Azure Premium Storage, consulte [Introducción a Azure Premium Storage](../storage/common/storage-premium-storage.md).
* Almacenamiento con redundancia local, consulte [Replicación de Azure Storage](../storage/common/storage-redundancy.md#locally-redundant-storage).

## <a name="azure-storage-blob-snapshots"></a>Instantáneas de Azure Storage Blob
Una de las ventajas de usar Azure Premium Storage, es que SQL Data Warehouse usa instantáneas de Azure Storage Blob para realizar la copia de seguridad del almacenamiento de datos local. Puede restaurar un almacén de datos a un punto de restauración de instantáneas. Las instantáneas se inician como mínimo cada ocho horas y están disponibles durante siete días.  

Para más información sobre:

* Instantáneas de blobs de Azure, consulte [Creación de una instantánea de un blob](../storage/blobs/storage-blob-snapshots.md).

## <a name="geo-redundant-backups"></a>Copias de seguridad con redundancia geográfica
Cada 24 horas, SQL Data Warehouse almacena el almacenamiento de datos completo en almacenamiento Estándar. El almacenamiento de datos completo se crea para que coincida con la hora de la última instantánea. El almacenamiento estándar pertenece a una cuenta de almacenamiento con redundancia geográfica con acceso de lectura (RA-GRS). La característica RA-GRS de Azure Storage replica los archivos de copias de seguridad en un [centro de datos emparejado](../best-practices-availability-paired-regions.md). Esta replicación geográfica garantiza que pueda restaurar el almacenamiento de datos en caso de que no se pueda acceder a las instantáneas de la región primaria. 

Esta característica está activada de forma predeterminada. Si no desea utilizar copias de seguridad con redundancia geográfica, puede [descartar] (https://docs.microsoft.com/powershell/resourcemanager/Azurerm.sql/v2.1.0/Set-AzureRmSqlDatabaseGeoBackupPolicy?redirectedfrom=msdn). 

> [!NOTE]
> En Azure Storage, el término *replicación* hace referencia a la copia de archivos desde una ubicación a otra. La *replicación de base de datos* de SQL hace referencia a mantener varias bases de datos secundarias sincronizadas con una base de datos principal. 
> 
> 

> [!NOTE]
> No se pueden rechazar las copias de seguridad con redundancia geográfica con DWU 9000 y DWU 18000. 
>
> 

Para más información sobre:

* Almacenamiento con redundancia geográfica, consulte [Replicación de Azure Storage](../storage/common/storage-redundancy.md).
* Almacenamiento de RA-GRS, consulte [Almacenamiento con redundancia geográfica con acceso de lectura](../storage/common/storage-redundancy.md#read-access-geo-redundant-storage).

## <a name="data-warehouse-backup-schedule-and-retention-period"></a>Programación de copia de seguridad de almacenamiento de datos y período de retención
SQL Data Warehouse crea instantáneas en los almacenamientos de datos en línea entre cada cuatro y ocho horas y guarda cada instantánea durante siete días. Puede restaurar la base de datos en línea a uno de los puntos de restauración de los últimos siete días. 

Para ver cuándo se inició la última instantánea, ejecute esta consulta en SQL Data Warehouse en línea. 

```sql
select top 1 *
from sys.pdw_loader_backup_runs 
order by run_id desc;
```

Si necesita conservar una instantánea durante más de siete días, puede restaurar un punto de restauración a un nuevo almacenamiento de datos. Cuando finaliza la restauración, SQL Data Warehouse comienza a crear instantáneas en el nuevo almacenamiento de datos. Si no realiza cambios en el nuevo almacenamiento de datos, las instantáneas permanecen vacías y, por tanto, el costo de la instantánea es mínimo. También puede pausar la base de datos para evitar que SQL Data Warehouse cree instantáneas.

### <a name="what-happens-to-my-backup-retention-while-my-data-warehouse-is-paused"></a>¿Qué pasa con la retención de mi copia de seguridad mientras el almacenamiento de datos está pausado?
SQL Data Warehouse no crea instantáneas y no caduca las copias de seguridad mientras un almacenamiento de datos está en pausa. Y tampoco cambia la antigüedad de la instantánea mientras el almacenamiento de datos está en pausa. La retención de instantáneas se basa en el número de días que el almacenamiento de datos está en línea, no en los días de calendario.

Por ejemplo, si una instantánea comienza el 1 de octubre a las 4 de la tarde y el almacenamiento de datos se detiene el 3 de octubre a kas 4 de la tarde, la instantánea tiene dos días de antigüedad. Cada vez que el almacenamiento de datos vuelve a estar en línea, la instantánea tiene dos días de antigüedad. Si el almacenamiento de datos vuelve a estar en línea el 5 de octubre a las 4 de la tarde, la instantánea tiene dos días de antigüedad y permanece cinco días más.

Cuando el almacenamiento de datos vuelve a estar en línea, SQL Data Warehouse reanuda las nuevas instantáneas y caduca aquellas que tienen más de siete días de datos.

### <a name="how-long-is-the-retention-period-for-a-dropped-data-warehouse"></a>¿Cuánto dura el período de retención para un almacenamiento de datos quitado?
Cuando se quita un almacenamiento de datos, este y las instantáneas se guardan durante siete días y luego se quitan. Puede restaurar el almacenamiento de datos a cualquiera de los puntos de restauración guardados.

> [!IMPORTANT]
> Si elimina una instancia de servidor de SQL lógica, todas las bases de datos que pertenecen a la instancia también se eliminan y no se pueden recuperar. No puede restaurar un servidor eliminado.
> 
> 

## <a name="data-warehouse-backup-costs"></a>Costos de la copia de seguridad del almacenamiento de datos
El costo total del almacenamiento de datos principal y de los siete días de instantáneas de Azure Blob se redondea al TB más cercano. Por ejemplo, si el almacenamiento de datos es de 1,5 TB y las instantáneas usan 100 GB, se le facturan 2 TB de datos según las tarifas de Azure Premium Storage. 

> [!NOTE]
> Cada instantánea está vacía inicialmente y crece a medida que se realizan cambios en el almacenamiento de datos principal. Todas las instantáneas aumentan de tamaño cuando cambia el almacenamiento de datos. Por lo tanto, los costos de almacenamiento de las instantáneas crecen según el porcentaje de cambio.
> 
> 

Si usa almacenamiento con redundancia geográfica, recibirá un cargo de almacenamiento por separado. El almacenamiento con redundancia geográfica se factura según la tarifa estándar de almacenamiento geográficamente redundante con acceso de lectura (RA-GRS).

Para más información sobre los precios de SQL Data Warehouse, consulte [Precios de SQL Data Warehouse](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).

## <a name="using-database-backups"></a>Uso de copias de seguridad de base de datos
El uso principal de copias de seguridad de almacenamiento de datos SQL consiste en restaurar el almacenamiento de datos a uno de los puntos de restauración dentro del período de retención.  

* Para restaurar un almacenamiento de datos SQL, consulte [Restauración de un almacenamiento de datos SQL](sql-data-warehouse-restore-database-overview.md).

## <a name="related-topics"></a>Temas relacionados
### <a name="scenarios"></a>Escenarios
* Para ver una introducción a la continuidad empresarial, consulte [Información general sobre la continuidad empresarial](../sql-database/sql-database-business-continuity.md)

<!-- ### Tasks -->

* Para restaurar un almacenamiento de datos, consulte [Restauración de un almacenamiento de datos SQL](sql-data-warehouse-restore-database-overview.md).

<!-- ### Tutorials -->

