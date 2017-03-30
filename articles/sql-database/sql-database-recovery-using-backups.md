---
title: "Restauración de una base de datos Azure SQL Database a partir de una copia de seguridad | Microsoft Docs"
description: "Obtenga información acerca de la restauración a un momento dado, que le permite revertir una Base de datos SQL de Azure a un momento dado anterior (hasta 35 días)."
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: monicar
ms.assetid: fd1d334d-a035-4a55-9446-d1cf750d9cf7
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/20/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 07635b0eb4650f0c30898ea1600697dacb33477c
ms.openlocfilehash: 751e1eaf65da889ab5c7dc26145c017682a12a4d
ms.lasthandoff: 03/28/2017


---
# <a name="recover-an-azure-sql-database-using-automated-database-backups"></a>Recuperación de una Base de datos SQL de Azure mediante copias de seguridad automatizadas
SQL Database proporciona tres opciones para la recuperación de bases de datos mediante [copias de seguridad automáticas de la base de datos](sql-database-automated-backups.md). Puede restaurar una base de datos durante el [período de retención](sql-database-service-tiers.md) de copia de seguridad de base de datos en:

* Una base de datos nueva en el mismo servidor lógico recuperada en un punto especificado en el tiempo durante el período de retención. 
* Una base de datos en el mismo servidor lógico recuperada a la hora de eliminación de una base de datos eliminada.
* Una base de datos nueva en cualquier servidor lógico de cualquier región recuperada hasta las copias de seguridad diarias más recientes en el almacenamiento de blobs de replicación geográfica (RA-GRS).

> [!TIP]
> Para ver un tutorial, consulte [Introducción a la copia de seguridad y la restauración para la protección de datos y la recuperación mediante PowerShell](sql-database-get-started-backup-recovery-portal.md).
>

También puede usar las [copias de seguridad de base de datos automatizada](sql-database-automated-backups.md) para crear una [copia de base de datos](sql-database-copy.md) en cualquier servidor lógico de cualquier región. También puede configurar un servidor de SQL Database para almacenar copias de seguridad de Azure SQL Database en un almacén de Recovery Services durante un período de tiempo de hasta 10 años. Consulte sobre la [retención de copia de seguridad a largo plazo](sql-database-long-term-retention.md).

## <a name="recovery-time"></a>Tiempo de recuperación
El tiempo de recuperación para restaurar una base de datos mediante copias de seguridad de base de datos automatizadas se ve afectado por una serie de factores: 

* El tamaño de la base de datos
* El nivel de rendimiento de la base de datos
* El número de registros de transacciones implicados
* La cantidad de actividad que debe reproducirse para la recuperación hasta el punto de restauración
* El ancho de banda de red si la restauración es a una región diferente 
* El número de solicitudes de restauración simultáneas que se están procesando en la región de destino. 
  
  En bases de datos muy grandes o activas, la restauración puede tardar varias horas. Si hay una interrupción prolongada en una región, es posible que haya un gran número de solicitudes de restauración geográfica que se procesan en otras regiones. Si hay muchas solicitudes, puede aumentar el tiempo de recuperación de las bases de datos en esa región. La mayoría de las restauraciones de bases de datos dura unas 12 horas.
  
  No existe ninguna funcionalidad integrada para restaurar de forma masiva. El script denominado [Base de datos SQL de Azure: recuperación completa del servidor](https://gallery.technet.microsoft.com/Azure-SQL-Database-Full-82941666) , es un ejemplo de uno de los diferentes modos de realizar esta tarea.

> [!IMPORTANT]
> Para poder efectuar una recuperación con copias de seguridad automatizadas, debe ser miembro del rol de colaborador de SQL Server en la suscripción o ser el propietario de la suscripción. Las recuperaciones se pueden realizar a través del Portal de Azure, PowerShell o la API de REST. No puede utilizar Transact-SQL. 
> 

## <a name="point-in-time-restore"></a>Restauración a un momento dado

Puede restaurar una base de datos existente a un momento anterior en el tiempo como una nueva base de datos en el mismo servidor lógico con [Azure Portal](sql-database-point-in-time-restore-portal.md), [PowerShell](scripts/sql-database-restore-database-powershell.md) o la [API de REST](https://msdn.microsoft.com/library/azure/mt163685.aspx). 

> [!IMPORTANT]
> Durante la restauración, no se puede sobrescribir la base de datos existente.
>

La base de datos se puede restaurar a cualquier nivel de rendimiento o de servicio y como una única base de datos o en un grupo elástico. Asegúrese de que haya suficientes recursos en el servidor lógico o en el grupo elástico en los que se va a restaurar la base de datos. Una vez finalizada, la base de datos restaurada es una base de datos normal, totalmente accesible en línea. La base de datos restaurada se cobra según la tarifa normal en función de su nivel de rendimiento y servicio. No se incurre en gastos hasta que finaliza la restauración de la base de datos.

Por lo general, una base de datos se restaura a un punto anterior para fines de recuperación. Cuando lo haga, puede tratar la base de datos restaurada como sustituto de la base de datos original o utilizarla para recuperar datos y actualizar después la base de datos original. 

* ***Sustituto de la base de datos:*** si la base de datos restaurada está pensada como sustituto de la base de datos original, debe comprobar que el nivel de rendimiento y el de servicio sean adecuados y escalar la base de datos si es necesario. Puede cambiar el nombre de la base de datos original y después asignar a la base de datos restaurada el nombre original mediante el comando ALTER DATABASE en T-SQL. 
* ***Recuperación de datos:*** si va a recuperar datos de la base de datos restaurada para recuperarse de un error de usuario o de aplicación, debe escribir y ejecutar cualquier script de recuperación de datos necesario para extraer datos de la base de datos restaurada a la base de datos original. Aunque la operación de restauración puede tardar mucho tiempo en finalizar, la base de datos restaurada será visible en la lista de bases de datos en todo el proceso de restauración. Si elimina la base de datos durante la restauración, la operación de restauración se cancela y no se le cobra por la base de datos que no finalizó la restauración. 

## <a name="deleted-database-restore"></a>Restauración de la base de datos eliminada
Puede restaurar una base de datos eliminada a la hora de eliminación de una base de datos eliminada en el mismo servidor lógico con [Azure Portal](sql-database-restore-deleted-database-portal.md), [PowerShell](scripts/sql-database-restore-database-powershell.md) o la [API de REST (createMode=Restore)](https://msdn.microsoft.com/library/azure/mt163685.aspx). 

> [!IMPORTANT]
> Si elimina una instancia de servidor de Azure SQL Database, todas sus bases de datos también se eliminan y no se pueden recuperar. En estos momentos no es posible restaurar un servidor eliminado.
> 

## <a name="geo-restore"></a>Restauración geográfica
Una base de datos SQL situada en cualquier servidor de cualquier región de Azure se puede restaurar de las copias de seguridad diferenciales y completas con replicación geográfica más recientes. La funcionalidad de restauración geográfica usa una copia de seguridad con redundancia geográfica como origen y se puede usar para recuperar una base de datos, aunque no se pueda acceder a dicha base de datos o al centro de datos debido a una interrupción. Puede usar [Azure Portal](sql-database-geo-restore-portal.md), [PowerShell](sql-database-geo-restore-powershell.md) o [REST (createMode=Recovery)](https://msdn.microsoft.com/library/azure/mt163685.aspx) 

La funcionalidad de restauración geográfica proporciona la opción de recuperación predeterminada cuando la base de datos no está disponible debido a una incidencia en la región en la que se hospeda la base de datos. Si un incidente a gran escala en una región provoca la falta de disponibilidad de una aplicación de base de datos, puede restaurar una base de datos de las copias de seguridad con replicación geográfica en un servidor de cualquier otra región. Hay un retraso entre momento en que se realiza una copia de seguridad diferencial y el momento en que se replica geográficamente en un blob de Azure de una región diferente. Este retraso puede ser de hasta una hora; por lo tanto, si se produce un desastre, puede haber una pérdida de datos de hasta una hora. En la siguiente ilustración se muestra la restauración de la base de datos a partir de la última copia de seguridad disponible en otra región.

![Restauración geográfica](./media/sql-database-geo-restore/geo-restore-2.png)

Para obtener información detallada sobre cómo usar la restauración geográfica a fin de recuperarse de una interrupción, consulte [Restauración de una base de datos SQL de Azure o una conmutación por error en una secundaria](sql-database-disaster-recovery.md)

> [!IMPORTANT]
> La recuperación de copias de seguridad es la más básica de las soluciones de recuperación ante desastres disponibles en SQL Database con el RPO y el tiempo de recuperación estimado (ERT) más largos. En las bases de datos básicas con un tamaño máximo de 2 GB, la funcionalidad de restauración geográfica proporciona una solución de recuperación ante desastres razonable con un ERT de 12 horas. En bases de datos Standard o Premium más grandes, si se desean tiempos de recuperación más cortos o reducir la probabilidad de pérdida de datos, sería conveniente considerar la posibilidad de usar la replicación geográfica activa. La replicación geográfica activa ofrece RPO y ERT mucho menores, ya que solo es necesario iniciar una conmutación por error en un elemento secundario replicado continuamente. Para más información, consulte [Información general: Replicación geográfica activa para Base de datos SQL de Azure](sql-database-geo-replication-overview.md).
> 

## <a name="programmatically-performing-recovery-using-automated-backups"></a>Recuperación mediante programación con copias de seguridad automatizadas
Como se dijo anteriormente, además de Azure Portal, la recuperación de una base de datos se puede realizar mediante programación con Azure PowerShell o la API de REST. En las tablas siguientes se describe el conjunto de comandos disponibles.

### <a name="powershell"></a>PowerShell
| Cmdlet | Descripción |
| --- | --- |
| [Get-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt603648.aspx) |Obtiene una o más bases de datos. |
| [Get-AzureRMSqlDeletedDatabaseBackup](https://msdn.microsoft.com/library/azure/mt693387.aspx) |Obtiene una base de datos eliminada que se puede restaurar. |
| [Get-AzureRmSqlDatabaseGeoBackup](https://msdn.microsoft.com/library/azure/mt693388.aspx) |Obtiene una copia de seguridad con redundancia geográfica de una base de datos. |
| [Restore-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt693390.aspx) |Restaura una Base de datos SQL. |
|  | |

### <a name="rest-api"></a>API de REST
| API | Descripción |
| --- | --- |
| [REST (createMode=Recovery)](https://msdn.microsoft.com/library/azure/mt163685.aspx) |Restaura una base de datos. |
| [Obtener el estado de creación o actualización de la base de datos](https://msdn.microsoft.com/library/azure/mt643934.aspx) |Devuelve el estado durante una operación de restauración. |
|  | |

## <a name="summary"></a>Resumen
Las copias de seguridad automáticas protegen las bases de datos de los errores de usuario y de aplicación, la eliminación accidental de la base de datos y las interrupciones prolongadas. Esta funcionalidad integrada está disponible para todos los niveles de servicio y niveles de rendimiento. 

## <a name="next-steps"></a>Pasos siguientes
* Para obtener una descripción general y los escenarios de la continuidad empresarial, consulte [Información general sobre la continuidad empresarial](sql-database-business-continuity.md)
* Para saber en qué consisten las copias de seguridad automatizadas de Base de datos SQL de Azure, consulte [Información general: copias de seguridad automatizadas de Base de datos SQL](sql-database-automated-backups.md)
* Para más información sobre la retención de copia de seguridad a largo plazo, consulte sobre la [retención de copia de seguridad a largo plazo](sql-database-long-term-retention.md).
* Para configurar, administrar y restaurar desde la retención a largo plazo de copias de seguridad automatizadas en un almacén de Azure Recovery Services mediante Azure Portal, consulte [Administración de la retención de copia de seguridad a largo plazo mediante Azure Portal](sql-database-manage-long-term-backup-retention-portal.md). 
* Para configurar, administrar y restaurar desde la retención a largo plazo de copias de seguridad automatizadas en un almacén de Azure Recovery Services mediante PowerShell, consulte [Administración de la retención de copia de seguridad a largo plazo mediante PowerShell](sql-database-manage-long-term-backup-retention-powershell.md).
* Para conocer las opciones de recuperación más rápidas, consulte [Replicación geográfica activa](sql-database-geo-replication-overview.md)  
* Si quiere aprender a utilizar las copias de seguridad automatizadas para procesos de archivado, consulte el procedimiento para [copiar una base de datos](sql-database-copy.md)

