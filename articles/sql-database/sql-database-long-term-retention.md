---
title: "Almacenamiento de copias de seguridad de Azure SQL Database durante diez años como máximo | Microsoft Docs"
description: "Obtenga información acerca de cómo Azure SQL Database admite almacenar copias de seguridad durante diez años como máximo."
keywords: 
services: sql-database
documentationcenter: 
author: anosov1960
manager: jhubbard
editor: 
ms.assetid: 66fdb8b8-5903-4d3a-802e-af08d204566e
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/22/2016
ms.author: sashan
translationtype: Human Translation
ms.sourcegitcommit: 2c13daf84727a500a2ea6a3dc1d4968c9824e223
ms.openlocfilehash: 6abc5c10f6116886ac5d6cbc11f251f083ee2b29
ms.lasthandoff: 02/16/2017


---
# <a name="storing-azure-sql-database-backups-for-up-to-10-years"></a>Almacenamiento de copias de seguridad de Azure SQL Database durante diez años como máximo
Muchas aplicaciones tienen finalidades normativas, de cumplimiento u otras de carácter empresarial que requieren que se conserven las copias de seguridad de las bases de datos completas automáticas más allá de los entre 7 y 35 días proporcionados por las [copias de seguridad automáticas](sql-database-automated-backups.md) de SQL Database.

La función **Long-Term Backup Retention** (Retención a largo plazo de copias de seguridad) le permite almacenar las copias de seguridad de Azure SQL Database en un almacén de Azure Recovery Services hasta 10 años. Puede almacenar hasta 1000 bases de datos por almacén. Puede seleccionar cualquier copia de seguridad del almacén para restaurarlo como una base de datos nueva.

> [!NOTE]
> Puede habilitar hasta 200 bases de datos por almacén durante 24 horas. Por lo tanto, se recomienda que use un almacén independiente para cada servidor para minimizar el impacto de este límite. 
> 
> 

## <a name="how-does-sql-database-long-term-backup-retention-work"></a>¿Cómo funciona la retención a largo plazo de copias de seguridad de SQL Database?

La retención a largo plazo de copias de seguridad permite asociar un servidor de Azure SQL Database a un almacén de Azure Recovery Services. 

* El almacén debe crearse en la misma suscripción de Azure que creó el servidor SQL Server y en la misma región geográfica y el grupo de recursos. 
* A continuación, configure una directiva de retención para cualquier base de datos. Esta directiva tiene como consecuencia que se copien las copias de seguridad completas semanales de la base de datos en el almacén de Recovery Services y que se conserven durante el periodo especificado de retención (hasta 10 años). 
* A continuación, puede restaurar desde cualquiera de estas copias de seguridad a una base de datos nueva en cualquier servidor de la suscripción. La copia se realiza mediante Azure Storage desde las copias de seguridad existentes y no afecta al rendimiento de la base de datos existente.


> [!TIP]
> Si desea ver un tutorial, consulte [Introducción a la copia de seguridad y la restauración para la protección y la recuperación de los datos mediante Azure Portal](sql-database-get-started-backup-recovery-portal.md) o [Introducción a la copia de seguridad y la restauración para la protección y la recuperación de los datos mediante PowerShell](sql-database-get-started-backup-recovery-powershell.md)

## <a name="how-do-i-enable-long-term-backup-retention"></a>¿Cómo se habilita la retención a largo plazo de copias de seguridad?

Para configurar la retención a largo plazo de copias de seguridad de una base de datos, es preciso:

1. crear un almacén de Azure Recovery Services en la misma región, suscripción y grupo de recursos que el servidor de SQL Database, 
2. registrar el servidor en el almacén.
3. crear una directiva de protección de Azure Recovery Services,
4. aplicar la directiva de protección a las bases de datos que requieren la retención a largo plazo de copias de seguridad.

Para configurar, administrar y restaurar desde la retención a largo plazo de copias de seguridad automatizadas en un almacén de Azure Recovery Services mediante Azure Portal, consulte [Administración de la retención de copia de seguridad a largo plazo mediante Azure Portal](sql-database-manage-long-term-backup-retention-portal.md). Para configurar, administrar y restaurar desde la retención a largo plazo de copias de seguridad automatizadas en un almacén de Azure Recovery Services mediante PowerShell, consulte [Administración de la retención de copia de seguridad a largo plazo mediante PowerShell](sql-database-manage-long-term-backup-retention-powershell.md).

## <a name="how-do-i-restore-a-database-stored-with-the-long-term-backup-retention-feature"></a>¿Cómo se restaura una base de datos almacenada con la función de retención a largo plazo de copias de seguridad?

Para recuperar desde una copia de seguridad de retención a largo plazo, es preciso:

1. incluir en una lista el almacén donde se almacena la copia de seguridad,
2. incluir en una lista el contenedor que se asigna a su servidor lógico,
3. incluir en una lista el origen de datos en el almacén que se asigna a la base de datos,
4. incluir en una lista de los puntos de restauración disponibles para restaurar,
5. restaurar desde el punto de recuperación en el servidor objetivo en su suscripción.

Para configurar, administrar y restaurar desde la retención a largo plazo de copias de seguridad automatizadas en un almacén de Azure Recovery Services mediante Azure Portal, consulte [Administración de la retención de copia de seguridad a largo plazo mediante Azure Portal](sql-database-manage-long-term-backup-retention-portal.md). Para configurar, administrar y restaurar desde la retención a largo plazo de copias de seguridad automatizadas en un almacén de Azure Recovery Services mediante PowerShell, consulte [Administración de la retención de copia de seguridad a largo plazo mediante PowerShell](sql-database-manage-long-term-backup-retention-powershell.md).

## <a name="how-much-does-long-term-backup-retention-cost"></a>¿Cuánto cuesta la retención a largo plazo de copias de seguridad?

La retención a largo plazo de copias de seguridad de una base de datos Azure SQL Database se cobra según las [tarifas de precios de servicios de copias de seguridad de Azure](https://azure.microsoft.com/pricing/details/backup/).

Una vez que el servidor de Azure SQL Database esté registrado en el almacén, se le cobra por el almacenamiento total utilizado por las copias de seguridad semanales guardadas en el almacén.

## <a name="view-available-backups-stored-in-long-term-backup-retention"></a>Consulta de las copias de seguridad disponibles almacenadas en retención de copia de seguridad a largo plazo

Para configurar, administrar y restaurar desde la retención a largo plazo de copias de seguridad automatizadas en un almacén de Azure Recovery Services mediante Azure Portal, consulte [Administración de la retención de copia de seguridad a largo plazo mediante Azure Portal](sql-database-manage-long-term-backup-retention-portal.md). Para configurar, administrar y restaurar desde la retención a largo plazo de copias de seguridad automatizadas en un almacén de Azure Recovery Services mediante PowerShell, consulte [Administración de la retención de copia de seguridad a largo plazo mediante PowerShell](sql-database-manage-long-term-backup-retention-powershell.md).

## <a name="disabling-long-term-retention"></a>Inhabilitación de la retención a largo plazo

Recovery Service controla automáticamente la limpieza de las copias de seguridad según la directiva de retención proporcionada. 

* Para detener el envío de copias de seguridad de una base de datos específica al almacén, elimine la directiva de retención de esa base de datos.
  
    ```
    Set-AzureRmSqlDatabaseBackupLongTermRetentionPolicy -ResourceGroupName 'RG1' -ServerName 'Server1' -DatabaseName 'DB1' -State 'Disabled' -ResourceId $policy.Id
    ```

> [!NOTE]
> Las copias de seguridad que ya están en el almacén no resultan afectadas. Recovery Service los elimina automáticamente cuando termina el período de retención.


## <a name="removing-long-term-backup-retention-backups-from-the-azure-recovery-services-vault"></a>Eliminación de copias de seguridad de retención a largo plazo desde el almacén de Azure Recovery Services

Para quitar las copias de seguridad de retención a largo plazo del almacén, consulte [Eliminación de copias de seguridad de retención a largo plazo](sql-database-manage-long-term-backup-retention-powershell.md).

## <a name="long-term-backup-retention-faq"></a>Preguntas frecuentes sobre la retención a largo plazo de copias de seguridad:

1. P: ¿puedo eliminar manualmente las copias de seguridad específicas en el almacén?

    R.: no en este momento, el almacén limpiará automáticamente las copias de seguridad cuando haya terminado el periodo de retención.
2. P: ¿puedo registrar mi servidor para guardar las copias de seguridad en más de un almacén?

    R.: no, actualmente solo puede guardar copias de seguridad en un almacén a la vez.
3. P: ¿puedo tener un almacén y un servidor en suscripciones distintas?

    R: no, actualmente el almacén y el servidor deben estar en la misma suscripción y el grupo de recursos.
4. P: ¿puedo usar un almacén que he creado en una región distinta a la de mi servidor?

    R: no, el almacén y el servidor deben estar en la misma región para minimizar el tiempo de copia y evitar los cargos de tráfico.
5. P.: ¿cuántas bases de datos puedo guardar en un almacén?

    R: actualmente solo se admiten hasta 1000 bases de datos por almacén. 
6. P: ¿Cuántos almacenes puedo crear por suscripción?

    A. Se pueden crear hasta 25 almacenes por suscripción.
7. P: ¿cuántas bases de datos puedo configurar diariamente por almacén?

    A. Solo puede configurar 200 bases de datos diariamente por almacén.
8. P.: ¿la retención a largo plazo de copias de seguridad funciona con grupos elásticos?

    R: Sí. Cualquier base de datos del grupo puede configurarse con la directiva de retención.
9. P: ¿puedo elegir el momento en que se crea la copia de seguridad?

    R.: no, la base de datos SQL Database controla la programación de copias de seguridad con el fin de minimizar el impacto del rendimiento en las bases de datos.
10. P: he habilitado TDE en mi base de datos. ¿Puedo usar TDE con el almacén? 

    A. Sí, es compatible con TDE. Puede restaurar la base de datos desde el almacén incluso si la base de datos original ya no existe.
11. P: ¿Qué ocurre con las copias de seguridad en el almacén si se suspende mi suscripción? 

    A. Si la suscripción se suspende, se conservan las bases de datos y las copias de seguridad existentes, pero las copias de seguridad nuevas no se copian en el almacén. Después haber reactivado la suscripción, el servicio retoma el copiado de copias de seguridad en el almacén. El almacén estará accesible para las operaciones de restauración mediante el uso de copias de seguridad que se copiaron allí antes de que se suspendiera la suscripción. 
12. P.: ¿puedo obtener acceso a los archivos de copias de seguridad de la base de datos SQL Database para descargar o restaurar a SQL Server?

    R.: actualmente, no.
13. P.: ¿es posible tener varias programaciones (diaria, semanal, mensual, anual) dentro de una directiva de retención de SQL.

    R: no, en este momento esto solo está disponible para las copias de seguridad de máquinas virtuales.
14. P: ¿Qué ocurre si establecemos la retención de copia de seguridad a largo plazo en una base de datos secundaria con replicación geográfica activa?

    R: En estos momentos no creamos copias de seguridad de réplicas y, por lo tanto, no hay ninguna opción para la retención de copia de seguridad a largo plazo en bases de datos secundarias. Sin embargo, es importante que un cliente configure una retención de copia de seguridad a largo plazo en una base de datos secundaria con replicación geográfica activa por estos motivos:
    - Cuando se produce una conmutación por error y la base de datos se convierte en la principal, crearemos una copia de seguridad completa y esta se cargará al almacén.
    - No hay costo adicional si el cliente configura la retención de copia de seguridad a largo plazo en una base de datos secundaria.



## <a name="next-steps"></a>Pasos siguientes
Las copias de seguridad de base de datos son una parte esencial de cualquier estrategia de recuperación ante desastres y continuidad del negocio, ya que protegen los datos de daños o eliminaciones accidentales. Para descubrir otras soluciones de continuidad empresarial de Azure SQL Database, consulte el artículo de [información general sobre la continuidad empresarial](sql-database-business-continuity.md).


