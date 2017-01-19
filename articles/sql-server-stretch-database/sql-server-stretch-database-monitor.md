---
title: "Supervisión y solución de problemas de migración de datos (Stretch Database) | Microsoft Docs"
description: "Aprenda a supervisar el estado de migración de datos."
services: sql-server-stretch-database
documentationcenter: 
author: douglaslMS
manager: jhubbard
editor: 
ms.assetid: 2341d446-9909-4694-8bb8-d288582daf54
ms.service: sql-server-stretch-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/14/2016
ms.author: douglasl
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 4efcb1b30f002d7baecaa46f9994dfbc9ad42dee


---
# <a name="monitor-and-troubleshoot-data-migration-stretch-database"></a>Supervisión y solución de problemas de migración de datos (Stretch Database)
Para supervisar la migración de datos en Stretch Database Monitor, seleccione **Tareas | Stretch | Monitor** para una base de datos en SQL Server Management Studio.

## <a name="check-the-status-of-data-migration-in-the-stretch-database-monitor"></a>Comprobación del estado de la migración de datos en Stretch Database Monitor
Seleccione **Tareas | Stretch | Monitor** para una base de datos en SQL Server Management Studio para abrir Stretch Database Monitor y supervisar la migración de datos.

* La parte superior del monitor muestra información general acerca de la base de datos de SQL Server habilitada para Stretch y la base de datos de Azure remota.
* La parte inferior del monitor muestra el estado de migración de datos para cada tabla habilitada para Stretch en la base de datos.

![Stretch Database Monitor][StretchMonitorImage1]

## <a name="a-namemigrationacheck-the-status-of-data-migration-in-a-dynamic-management-view"></a><a name="Migration"></a>Comprobación del estado de la migración de datos en una vista de administración dinámica
Abra la vista de administración dinámica **sys.dm\_db\_rda\_migration\_status** para comprobar cuántos lotes y filas de datos se han migrado. Para más información, consulte [sys.dm_db_rda_migration_status (Transact-SQL)](https://msdn.microsoft.com/library/dn935017.aspx).

## <a name="a-namefirewallatroubleshoot-data-migration"></a><a name="Firewall"></a>Solución de problemas de migración de datos
**Las filas de mi tabla habilitada para Stretch no se están migrando a Azure. ¿Cuál es el problema?**

Hay varios problemas que pueden afectar a la migración. Compruebe lo siguiente.

* Compruebe la conectividad de red para el equipo con SQL Server.
* Compruebe que el firewall de Azure no esté bloqueando la conexión de SQL Server con el punto de conexión remoto.
* Compruebe el estado del lote más reciente en la vista de administración dinámica **sys.dm\_db\_rda\_migration\_status**. Si se ha producido un error, compruebe los valores error\_number, error\_state y error\_severity para el lote.
  
  * Para más información sobre la vista, consulte [sys.dm_db_rda_migration_status (Transact-SQL)](https://msdn.microsoft.com/library/dn935017.aspx).
  * Para más información sobre el contenido de un mensaje de error de SQL Server, consulte [sys.messages (Transact-SQL)](https://msdn.microsoft.com/library/ms187382.aspx).

**El firewall de Azure bloquea las conexiones de mi servidor local.**

Es posible que tenga que agregar una regla en la configuración del Firewall de Azure para permitir que SQL Server se comunique con el servidor remoto de Azure.

## <a name="see-also"></a>Otras referencias
[Administración y solución de problemas de Stretch Database](sql-server-stretch-database-manage.md)

<!--Image references-->
[StretchMonitorImage1]: ./media/sql-server-stretch-database-monitor/StretchDBMonitor.png



<!--HONumber=Dec16_HO2-->


