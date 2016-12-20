---
title: "Inicio de una conmutación por error planeada o no planeada para Azure SQL Database con PowerShell | Microsoft Docs"
description: "Inicio de una conmutación por error planeada o no planeada para Base de datos SQL de Azure con PowerShell"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 5849b600-89cb-4995-ae9f-0188a17b4e1b
ms.service: sql-database
ms.custom: business continuity; how to
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: powershell
ms.workload: data-management
ms.date: 08/29/2016
ms.author: sstein
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: a16d278262f6fb645163f8d94139c86019df0cde


---
# <a name="initiate-a-planned-or-unplanned-failover-for-azure-sql-database-with-powershell"></a>Inicio de una conmutación por error planeada o no planeada para Base de datos SQL de Azure con PowerShell
> [!div class="op_single_selector"]
> * [Portal de Azure](sql-database-geo-replication-failover-portal.md)
> * [PowerShell](sql-database-geo-replication-failover-powershell.md)
> * [T-SQL](sql-database-geo-replication-failover-transact-sql.md)
> 
> 

En este artículo se muestra cómo iniciar una conmutación por error planeada o no planeada para Base de datos SQL con PowerShell. Para configurar la replicación geográfica, consulte [Configuración de la replicación geográfica para Base de datos SQL de Azure con PowerShell](sql-database-geo-replication-powershell.md).

## <a name="initiate-a-planned-failover"></a>Inicio de una conmutación por error planeada
Use el cmdlet **Set-AzureRmSqlDatabaseSecondary** con el parámetro **-Failover** para promover una base de datos secundaria a nueva base de datos principal, y degradar la base de datos principal existente a secundaria. Esta funcionalidad está diseñada para la conmutación por error planeada, por ejemplo, durante las exploraciones de recuperación ante desastres, y requiere que la base de datos principal esté disponible.

El comando ejecuta el siguiente flujo de trabajo:

1. Cambiar temporalmente la replicación a modo sincrónico. Esto hará que todas las transacciones pendientes se vacíen en la base de datos secundaria.
2. Cambia los roles de las dos bases de datos en la asociación de replicación geográfica.  

Esta secuencia garantiza que las dos bases de datos se sincronizan antes del cambio de roles y, por tanto, no se producirá ninguna pérdida de datos. Hay un breve período durante el que ambas bases de datos no están disponibles (del orden de 0 a 25 segundos) mientras se cambian los roles. En circunstancias normales, toda la operación debería tardar menos de un minuto en completarse. Para más información, consulte [Set-AzureRmSqlDatabaseSecondary](https://msdn.microsoft.com/library/mt619393\(v=azure.300\).aspx).

Este cmdlet devolverá resultados cuando se complete el proceso de cambiar la base de datos secundaria a principal.

El siguiente comando cambia los roles de la base de datos "mydb" en el servidor "srv2" en el grupo de recursos "rg2" a principal. La base de datos principal original a la que estaba conectada "db2" cambiará a secundaria después de que las dos bases de datos estén totalmente sincronizadas.

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb" –ResourceGroupName "rg2” –ServerName "srv2”
    $database | Set-AzureRmSqlDatabaseSecondary -Failover


> [!NOTE]
> En contadas ocasiones, es posible que la operación no pueda completarse y puede parecer que no responde. En este caso, el usuario puede ejecutar el comando de conmutación por error forzada (conmutación por error no planeada) y aceptar la pérdida de datos.
> 
> 

## <a name="initiate-an-unplanned-failover-from-the-primary-database-to-the-secondary-database"></a>Inicio de una conmutación por error no planeada desde la base de datos principal a la base de datos secundaria
Puede usar el cmdlet **Set-AzureRmSqlDatabaseSecondary** con los parámetros **–Failover** y **-AllowDataLoss** para promover una base de datos secundaria a nueva base de datos principal de forma no planeada, y forzar la degradación de la base de datos principal existente a secundaria en un momento cuando la base de datos principal ya no está disponible.

Esta funcionalidad está diseñada para situaciones de recuperación ante desastres en los que resulta fundamental restaurar la disponibilidad de la base de datos y la pérdida de algunos datos resulta aceptable. Cuando se invoca la conmutación por error forzada, la base de datos secundaria especificada se convierte inmediatamente en la base de datos principal y comienza a aceptar transacciones de escritura. Tan pronto como la base de datos principal original puede volver a conectar con esta nueva base de datos principal después de la operación de conmutación por error forzada, se realiza una copia de seguridad incremental de la base de datos principal original y la base de datos principal anterior se convierte en base de datos secundaria para la nueva base de datos principal; por consiguiente, es simplemente una réplica de la nueva principal.

Como no se admite la restauración a un momento dado en bases de datos secundarias, si desea recuperar los datos confirmados en la base de datos principal anterior que no se replicaron en la nueva base de datos principal, deberá indicar a CSS que restaure la copia de seguridad de registro conocida de la base de datos.

> [!NOTE]
> Si el comando se emite cuando las bases de datos principal y secundaria están en línea, la principal anterior se convertirá inmediatamente en la nueva secundaria sin sincronización de datos. Si la principal está confirmando transacciones cuando se emite el comando, es posible que produzca alguna pérdida de datos.
> 
> 

Si la base de datos principal tiene varias bases de datos secundarias, el comando se ejecutará correctamente de forma parcial. La base de datos secundaria en la que se ejecutó el comando se convertirá en la principal. Sin embargo, la base de datos principal anterior seguirá siendo principal, es decir, las dos bases de datos principales terminarán en un estado incoherente y conectadas por un vínculo de replicación suspendido. El usuario tendrá que reparar manualmente esta configuración mediante una API "quitar secundaria" en cualquiera de estas bases de datos principales.

El siguiente comando cambia los roles de la base de datos "mydb" a principal cuando la base de datos principal no está disponible. La base de datos principal original a la que estaba conectada "mydb" cambiará a secundaria después de volver en línea de nuevo. En ese momento, la sincronización puede producir la pérdida de datos.

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb" –ResourceGroupName "rg2” –ServerName "srv2”
    $database | Set-AzureRmSqlDatabaseSecondary –Failover -AllowDataLoss




## <a name="next-steps"></a>Pasos siguientes
* Después de la conmutación por error, asegúrese de que los requisitos de autenticación para el servidor y la base de datos se configuran en el nuevo elemento principal. Para obtener más información, consulte [Administración de la seguridad de Base de datos SQL de Azure después de la recuperación ante desastres](sql-database-geo-replication-security-config.md).
* Para obtener información sobre cómo llevar a cabo tareas de recuperación después de desastres mediante la replicación geográfica activa, incluidos los pasos previos y posteriores, consulte [Obtención de detalles de la recuperación ante desastres](sql-database-disaster-recovery.md)
* Para ver una entrada de blog de Sasha Nosov sobre la replicación geográfica activa, consulte [Spotlight on new Geo-Replication capabilities](https://azure.microsoft.com/blog/spotlight-on-new-capabilities-of-azure-sql-database-geo-replication/)
* Para obtener información sobre cómo diseñar aplicaciones en la nube para usar la replicación geográfica activa, consulte [Diseño de una aplicación para la recuperación ante desastres en la nube mediante replicación geográfica activa en Base de datos SQL](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
* Para saber cómo utilizar la replicación geográfica activa con los grupos de bases de datos elásticas, consulte [Estrategias de recuperación ante desastres para aplicaciones que usan el grupo elástico de Base de datos SQL](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).
* Para ver una descripción general de la continuidad empresarial, consulte [Introducción a la continuidad empresarial con Base de datos SQL de Azure](sql-database-business-continuity.md)




<!--HONumber=Nov16_HO3-->


