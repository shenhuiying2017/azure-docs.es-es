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
ms.date: 08/25/2017
ms.author: carlrab
ms.translationtype: HT
ms.sourcegitcommit: 48dfc0fa4c9ad28c4c64c96ae2fc8a16cd63865c
ms.openlocfilehash: df6e4bba9290c6129c9cba1440bb0c903aacc3c8
ms.contentlocale: es-es
ms.lasthandoff: 08/30/2017

---
# <a name="recover-an-azure-sql-database-using-automated-database-backups"></a>Recuperación de una Base de datos SQL de Azure mediante copias de seguridad automatizadas
SQL Database proporciona estas opciones para la recuperación de bases de datos mediante [copias de seguridad automatizadas de la base de datos](sql-database-automated-backups.md) y [copias de seguridad en retención a largo plazo](sql-database-long-term-retention.md). Puede restaurar de una copia de seguridad de base de datos a:

* Una base de datos nueva en el mismo servidor lógico recuperada en un punto especificado en el tiempo durante el período de retención. 
* Una base de datos en el mismo servidor lógico recuperada a la hora de eliminación de una base de datos eliminada.
* Una base de datos nueva en cualquier servidor lógico de cualquier región recuperada hasta las copias de seguridad diarias más recientes en el almacenamiento de blobs de replicación geográfica (RA-GRS).

> [!IMPORTANT]
> Durante la restauración, no se puede sobrescribir la base de datos existente.
>

Una base de datos restaurada incurre en un costo de almacenamiento adicional en las siguientes condiciones: 
- Restauración de P11 – P15 a S4-S12 o P1–P6 si el tamaño máximo de la base de datos es mayor de 500 GB.
- Restauración de P1–P6 o PRS1–PRS6 a S4-S12 si el tamaño máximo de la base de datos es mayor de 250 GB.

El costo adicional es debido a que el tamaño máximo de la base de datos restaurada es mayor que la cantidad de almacenamiento incluido para el nivel de rendimiento, y se aplicarán cargos adicionales a cualquier almacenamiento adicional aprovisionado por encima del importe incluido.  Para más información sobre los precios del almacenamiento adicional, consulte la [página de precios de SQL Database](https://azure.microsoft.com/pricing/details/sql-database/).  Si la cantidad de espacio real utilizado es menor que la cantidad de almacenamiento incluido, este costo adicional puede evitarse si se reduce el tamaño máximo de la base de datos a la cantidad incluida. Para más información sobre los tamaños de almacenamiento de las bases de datos y cómo cambiar el tamaño máximo de la base de datos, consulte [Límites de recursos de base de datos únicas](sql-database-resource-limits.md#single-database-storage-sizes-and-performance-levels).  

> [!NOTE]
> Las [copias de seguridad de base de datos automatizadas](sql-database-automated-backups.md) se usan cuando se crea una [copia de la base de datos](sql-database-copy.md). 
>


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

Puede restaurar una base de datos existente a un momento dado como una nueva base de datos en el mismo servidor lógico con Azure Portal, [PowerShell](https://docs.microsoft.com/en-us/powershell/module/azurerm.sql/restore-azurermsqldatabase) o la [API de REST](https://msdn.microsoft.com/library/azure/mt163685.aspx). 

> [!TIP]
> Para obtener un script de PowerShell de ejemplo que muestre cómo realizar una restauración a un momento dado, vea [Restauración de una instancia de SQL Database con PowerShell](scripts/sql-database-restore-database-powershell.md).
>

La base de datos se puede restaurar a cualquier nivel de rendimiento o de servicio y como una única base de datos o en un grupo elástico. Asegúrese de que haya suficientes recursos en el servidor lógico o en el grupo elástico en los que se va a restaurar la base de datos. Una vez finalizada, la base de datos restaurada es una base de datos normal, totalmente accesible en línea. La base de datos restaurada se cobra según la tarifa normal en función de su nivel de rendimiento y servicio. No se incurre en gastos hasta que finaliza la restauración de la base de datos.

Por lo general, una base de datos se restaura a un punto anterior para fines de recuperación. Cuando lo haga, puede tratar la base de datos restaurada como sustituto de la base de datos original o utilizarla para recuperar datos y actualizar después la base de datos original. 

* ***Sustituto de la base de datos:*** si la base de datos restaurada está pensada como sustituto de la base de datos original, debe comprobar que el nivel de rendimiento y el de servicio sean adecuados y escalar la base de datos si es necesario. Puede cambiar el nombre de la base de datos original y después asignar a la base de datos restaurada el nombre original mediante el comando ALTER DATABASE en T-SQL. 
* ***Recuperación de datos:*** si va a recuperar datos de la base de datos restaurada para recuperarse de un error de usuario o de aplicación, debe escribir y ejecutar cualquier script de recuperación de datos necesario para extraer datos de la base de datos restaurada a la base de datos original. Aunque la operación de restauración puede tardar mucho tiempo en finalizar, la base de datos restaurada será visible en la lista de bases de datos en todo el proceso de restauración. Si elimina la base de datos durante la restauración, la operación de restauración se cancela y no se le cobra por la base de datos que no finalizó la restauración. 

### <a name="azure-portal"></a>Portal de Azure

Para recuperar a un momento dado mediante Azure Portal, abra la página de la base de datos y haga clic en **Restaurar** en la barra de herramientas.

![restauración a un momento dado](./media/sql-database-recovery-using-backups/point-in-time-recovery.png)

## <a name="deleted-database-restore"></a>Restauración de la base de datos eliminada
Puede restaurar una base de datos eliminada a la hora de eliminación de una base de datos eliminada en el mismo servidor lógico con Azure Portal, [PowerShell](https://docs.microsoft.com/en-us/powershell/module/azurerm.sql/restore-azurermsqldatabase) o la [REST (createMode=Restore)](https://msdn.microsoft.com/library/azure/mt163685.aspx). 

> [!TIP]
> Para obtener un script de PowerShell de ejemplo que muestre cómo realizar una restauración en una base de datos eliminada, vea [Restauración de una instancia de SQL Database con PowerShell](scripts/sql-database-restore-database-powershell.md).
>

> [!IMPORTANT]
> Si elimina una instancia de servidor de Azure SQL Database, todas sus bases de datos también se eliminan y no se pueden recuperar. En estos momentos no es posible restaurar un servidor eliminado.
> 

### <a name="azure-portal"></a>Portal de Azure

Para recuperar una base de datos eliminada durante su [período de retención](sql-database-service-tiers.md) mediante Azure Portal, abra la página del servidor y, en el área de operaciones, haga clic en **Bases de datos eliminadas**.

![restauración-base de datos-eliminada-1](./media/sql-database-recovery-using-backups/deleted-database-restore-1.png)


![restauración-base de datos-eliminada-2](./media/sql-database-recovery-using-backups/deleted-database-restore-2.png)

## <a name="geo-restore"></a>Restauración geográfica
Una base de datos SQL situada en cualquier servidor de cualquier región de Azure se puede restaurar de las copias de seguridad diferenciales y completas con replicación geográfica más recientes. La funcionalidad de restauración geográfica utiliza una copia de seguridad con redundancia geográfica como origen y se puede usar para recuperar una base de datos, aunque no se pueda acceder a dicha base de datos o al centro de datos debido a una interrupción. 

La funcionalidad de restauración geográfica proporciona la opción de recuperación predeterminada cuando la base de datos no está disponible debido a una incidencia en la región en la que se hospeda la base de datos. Si un incidente a gran escala en una región provoca la falta de disponibilidad de una aplicación de base de datos, puede restaurar una base de datos de las copias de seguridad con replicación geográfica en un servidor de cualquier otra región. Hay un retraso entre momento en que se realiza una copia de seguridad diferencial y el momento en que se replica geográficamente en un blob de Azure de una región diferente. Este retraso puede ser de hasta una hora; por lo tanto, si se produce un desastre, puede haber una pérdida de datos de hasta una hora. En la siguiente ilustración se muestra la restauración de la base de datos a partir de la última copia de seguridad disponible en otra región.

![Restauración geográfica](./media/sql-database-geo-restore/geo-restore-2.png)

> [!TIP]
> Para obtener un script de PowerShell de ejemplo que muestre cómo realizar una georrestauración, vea [Restauración de una instancia de SQL Database con PowerShell](scripts/sql-database-restore-database-powershell.md).
> 

Actualmente no se admite la restauración a un momento dad en una base de datos geográfica secundaria. La restauración a un momento dado solo puede realizarse en una base de datos principal. Para obtener información detallada sobre cómo usar la restauración geográfica a fin de recuperarse de una interrupción, consulte [Restauración de una base de datos SQL de Azure o una conmutación por error en una secundaria](sql-database-disaster-recovery.md).

> [!IMPORTANT]
> La recuperación de copias de seguridad es la más básica de las soluciones de recuperación ante desastres disponibles en SQL Database con el RPO y el tiempo de recuperación estimado (ERT) más largos. Para las soluciones que emplean bases de datos Básico, la restauración geográfica suele ser una solución de recuperación ante desastres razonable con un ERT de 12 horas. En lo que respecta a las soluciones que utilizan bases de datos Estándar o Premium más grandes que precisan tiempos de recuperación más cortos, sería conveniente considerar la posibilidad de usar la [replicación geográfica activa](sql-database-geo-replication-overview.md). La replicación geográfica activa ofrece RPO y ERT mucho menores, ya que solo es necesario iniciar una conmutación por error en un elemento secundario replicado continuamente. Para obtener más información sobre las opciones de continuidad empresarial, vea [este artículo](sql-database-business-continuity.md).
> 

### <a name="azure-portal"></a>Portal de Azure

Para realizar una restauración geográfica de una base de datos durante su [período de retención](sql-database-service-tiers.md) mediante Azure Portal, abra la página de bases de datos SQL y, luego, haga clic en **Agregar**. En el cuadro de texto **Seleccionar origen** cuadro de texto, seleccione **Copia de seguridad**. Especifique la copia de seguridad desde la que se va a recuperar en la región y en el servidor de su elección. 

## <a name="programmatically-performing-recovery-using-automated-backups"></a>Recuperación mediante programación con copias de seguridad automatizadas
Como se dijo anteriormente, además de Azure Portal, la recuperación de una base de datos se puede realizar mediante programación con Azure PowerShell o la API de REST. En las tablas siguientes se describe el conjunto de comandos disponibles.

### <a name="powershell"></a>PowerShell
| Cmdlet | Descripción |
| --- | --- |
| [Get-AzureRmSqlDatabase](/powershell/module/azurerm.sql/get-azurermsqldatabase) |Obtiene una o más bases de datos. |
| [Get-AzureRMSqlDeletedDatabaseBackup](/powershell/module/azurerm.sql/get-azurermsqldeleteddatabasebackup) | Obtiene una base de datos eliminada que se puede restaurar. |
| [Get-AzureRmSqlDatabaseGeoBackup](/powershell/module/azurerm.sql/get-azurermsqldatabasegeobackup) |Obtiene una copia de seguridad con redundancia geográfica de una base de datos. |
| [Restore-AzureRmSqlDatabase](/powershell/module/azurerm.sql/restore-azurermsqldatabase) |Restaura una Base de datos SQL. |
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
* Para configurar, administrar y restaurar desde la retención a largo plazo de copias de seguridad automatizadas en un almacén de Azure Recovery Services mediante Azure Portal, consulte [Configure and use long-term backup retention](sql-database-long-term-backup-retention-configure.md) (Configuración y uso de retención a largo plazo de copias de seguridad). 
* Para conocer las opciones de recuperación más rápidas, consulte [Replicación geográfica activa](sql-database-geo-replication-overview.md)  

