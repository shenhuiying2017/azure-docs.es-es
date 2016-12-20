---
title: "Pausa y reanudación de la migración de datos (Stretch Database) | Microsoft Docs"
description: "Aprenda a pausar o reanudar la migración de datos en Azure."
services: sql-server-stretch-database
documentationcenter: 
author: douglaslMS
manager: jhubbard
editor: 
ms.assetid: d45c05ad-254e-4950-a652-3d5cc40ed967
ms.service: sql-server-stretch-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/14/2016
ms.author: douglasl
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: f8b5f4461a20c1407ba2ce834ec6dd970f4e7544


---
# <a name="pause-and-resume-data-migration-stretch-database"></a>Pausa y reanudación de la migración de datos (Stretch Database)
Para pausar o reanudar la migración de datos en Azure, seleccione **Stretch** para una tabla en SQL Server Management Studio y después seleccione **Pausar** para pausar la migración de datos o **Reanudar** para reanudarla. También puede usar Transact\-SQL para pausarla o reanudarla.

Pause la migración de datos en tablas individuales cuando desee solucionar problemas en el servidor local o para maximizar el ancho de banda de red disponible.

## <a name="pause-data-migration"></a>Pausa de migración de datos
### <a name="use-sql-server-management-studio-to-pause-data-migration"></a>Uso de SQL Server Management Studio para pausar la migración de datos
1. En SQL Server Management Studio, en el Explorador de objetos, seleccione la tabla habilitada para Stretch en la que desee pausar la migración de datos.
2. Haga clic con el botón derecho, seleccione **Stretch** y, después, **Pausar**.

### <a name="use-transact-sql-to-pause-data-migration"></a>Uso de Transact\-SQL para pausar la migración de datos
Ejecute el siguiente comando.

```tsql
USE <Stretch-enabled database name>;
GO
ALTER TABLE <Stretch-enabled table name>  
    SET ( REMOTE_DATA_ARCHIVE ( MIGRATION_STATE = PAUSED ) ) ;  
GO
```

## <a name="resume-data-migration"></a>Reanudación de la migración de datos
### <a name="use-sql-server-management-studio-to-resume-data-migration"></a>Uso de SQL Server Management Studio para reanudar la migración de datos
1. En SQL Server Management Studio, en el Explorador de objetos, seleccione la tabla habilitada para Stretch en la que desee reanudar la migración de datos.
2. Haga clic con el botón derecho, seleccione **Stretch** y, después, **Reanudar**.

### <a name="use-transact-sql-to-resume-data-migration"></a>Uso de Transact\-SQL para reanudar la migración de datos
Ejecute el siguiente comando.

```tsql
USE <Stretch-enabled database name>;
GO
ALTER TABLE <Stretch-enabled table name>   
    SET ( REMOTE_DATA_ARCHIVE ( MIGRATION_STATE = OUTBOUND ) ) ;  
 GO
```

## <a name="check-whether-migration-is-active-or-paused"></a>Comprobación de si la migración está activa o en pausa
### <a name="use-sql-server-management-studio-to-check-whether-migration-is-active-or-paused"></a>Uso de SQL Server Management Studio para comprobar si la migración está activa o en pausa
En SQL Server Management Studio, abra **Stretch Database Monitor** y compruebe el valor de la columna **Migration State**. Para más información, consulte [Supervisión y solución de problemas de migración de datos (Stretch Database)](sql-server-stretch-database-monitor.md).

### <a name="use-transact-sql-to-check-whether-migration-is-active-or-paused"></a>Uso de Transact-SQL para comprobar si la migración está activa o en pausa
Abra la vista de catálogo **sys.remote_data_archive_tables** y compruebe el valor de la columna **is_migration_paused**. Para más información, consulte [sys.remote_data_archive_tables](https://msdn.microsoft.com/library/dn935003.aspx).

## <a name="see-also"></a>Consulte también
[ALTER TABLE (Transact-SQL)](https://msdn.microsoft.com/library/ms190273.aspx)
[Supervisión y solución de problemas de migración de datos (Stretch Database)](sql-server-stretch-database-monitor.md)




<!--HONumber=Nov16_HO3-->


