---
title: "Restauración de un almacén de datos de Azure: local y con redundancia geográfica | Microsoft Docs"
description: "Información general de las opciones de restauración de bases de datos para recuperar una base de datos en Almacenamiento de datos SQL de Azure."
services: sql-data-warehouse
documentationcenter: NA
author: Lakshmi1812
manager: jhubbard
editor: 
ms.assetid: 3e01c65c-6708-4fd7-82f5-4e1b5f61d304
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: backup-restore
ms.date: 10/31/2016
ms.author: lakshmir;barbkess
ms.openlocfilehash: ea42b7135d0695b66d569095e70bb3d9f8b9594b
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2017
---
# <a name="sql-data-warehouse-restore"></a>Restauración de SQL Data Warehouse
> [!div class="op_single_selector"]
> * [Información general][Overview]
> * [Portal][Portal]
> * [PowerShell][PowerShell]
> * [REST][REST]
> 
> 

SQL Data Warehouse ofrece restauraciones locales y geográficas como parte de sus funcionalidades de recuperación ante desastres de almacenamiento de datos. Use copias de seguridad de almacenamiento de datos para restaurar el almacenamiento de datos a un punto de restauración en la región primaria o use copias de seguridad con redundancia geográfica para restaurar a una región geográfica diferente. En este artículo se explican los aspectos específicos de la restauración de un almacenamiento de datos.

## <a name="what-is-a-data-warehouse-restore"></a>¿Qué es una restauración de almacenamiento de datos?
Una restauración de almacenamiento de datos es un nuevo almacenamiento de datos que se crea a partir de una copia de seguridad de un almacenamiento de datos existente o eliminado. El almacenamiento de datos restaurado vuelve a crear el de copia de seguridad en un momento determinado. Como SQL Data Warehouse es un sistema distribuido, una restauración de almacenamiento de datos se crea a partir de muchos archivos de copia de seguridad que se almacenan en blobs de Azure. 

La restauración de las bases de datos es una parte esencial de cualquier estrategia de recuperación ante desastres y continuidad empresarial, ya que vuelve a crear los datos tras daños o eliminaciones accidentales.

Para más información, consulte:

* [Copias de seguridad de SQL Data Warehouse](sql-data-warehouse-backups.md)
* [Información general acerca de la continuidad empresarial](../sql-database/sql-database-business-continuity.md)

## <a name="data-warehouse-restore-points"></a>Puntos de restauración de almacenamiento de datos
Una de las ventajas de usar Azure Premium Storage es que SQL Data Warehouse usa instantáneas de Azure Storage Blob para realizar la copia de seguridad del almacenamiento de datos principal. Cada instantánea tiene un punto de restauración que representa la hora de inicio de la instantánea. Para restaurar un almacenamiento de datos, elija un punto de restauración y emita un comando de restauración.  

SQL Data Warehouse siempre restaura la copia de seguridad en un nuevo almacenamiento de datos. Puede mantener el almacenamiento de datos restaurado y el actual, o eliminar uno de ellos. Si desea reemplazar el almacenamiento de datos actual con el almacenamiento de datos restaurado, puede cambiar su nombre.

Si tiene que restaurar un almacenamiento de datos eliminado o en pausa, puede [crear una incidencia de soporte técnico](sql-data-warehouse-get-started-create-support-ticket.md). 

<!-- 
### Can I restore a deleted data warehouse?

Yes, you can restore the last available restore point.

Yes, for the next seven calendar days. When you delete a data warehouse, SQL Data Warehouse actually keeps the data warehouse and its snapshots for seven days just in case you need the data. After seven days, you won't be able to restore to any of the restore points. -->

## <a name="geo-redundant-restore"></a>Restauración con redundancia geográfica
Puede restaurar el almacenamiento de datos en cualquier región que admita Azure SQL Data Warehouse en el nivel de rendimiento elegido. Tenga en cuenta que 9000 y 18000 DWU no se admiten en todas las regiones durante la versión preliminar.

> [!NOTE]
> Para llevar a cabo una restauración con redundancia geográfica no puede haber anulado esta característica.
> 
> 

## <a name="restore-timeline"></a>Escala de tiempo de restauración
Puede restaurar una base de datos a cualquier punto de restauración disponible de los últimos siete días. Las instantáneas comienzan cada cuatro a ocho horas y están disponibles durante siete días. Cuando una instantánea tiene una antigüedad superior a siete días, caduca y su punto de restauración ya no está disponible.

## <a name="restore-costs"></a>Costos de restauración
Los cargos de almacenamiento por el almacenamiento de datos restaurado se facturan con la tarifa de Azure Premium Storage. 

Si se pausa un almacenamiento de datos restaurado, se le cobrará por el almacenamiento según la tarifa de Azure Premium Storage. La ventaja de pausar es que no se cobra por los recursos de computación de DWU.

Para más información sobre los precios de SQL Data Warehouse, consulte [Precios de SQL Data Warehouse](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).

## <a name="uses-for-restore"></a>Usos de la restauración
El uso principal de la restauración del almacenamiento de datos es recuperar información tras las pérdidas de datos o daños accidentales.

También puede utilizar la restauración del almacenamiento de datos para conservar una copia de seguridad durante más de siete días. Una vez restaurada la copia de seguridad, tiene el almacenamiento de datos en línea y puede pausarlo indefinidamente para ahorrar costos de computación. La base de datos en pausa genera gastos de almacenamiento según la tarifa de Azure Premium Storage. 

## <a name="related-topics"></a>Temas relacionados
### <a name="scenarios"></a>Escenarios
* Para ver una introducción a la continuidad empresarial, consulte [Información general sobre la continuidad empresarial](../sql-database/sql-database-business-continuity.md)

<!-- ### Tasks -->

Para realizar una restauración del almacenamiento de datos, efectúa esta última mediante los siguientes recursos:

* Azure Portal; consulte [Restaurar un almacenamiento de datos mediante Azure Portal](sql-data-warehouse-restore-database-portal.md).
* Cmdlets de PowerShell; consulte [Restaurar un almacenamiento de datos mediante cmdlets de PowerShell](sql-data-warehouse-restore-database-powershell.md).
* Las API de REST; consulte [Restaurar un almacenamiento de datos mediante las API de REST](sql-data-warehouse-restore-database-rest-api.md).

<!-- ### Tutorials -->

<!--Image references-->

<!--Article references-->
[Azure SQL Database business continuity overview]: ../sql-database/sql-database-business-continuity.md
[Overview]: ./sql-data-warehouse-restore-database-overview.md
[Portal]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[REST]: ./sql-data-warehouse-restore-database-rest-api.md

<!--MSDN references-->


<!--Other Web references-->
