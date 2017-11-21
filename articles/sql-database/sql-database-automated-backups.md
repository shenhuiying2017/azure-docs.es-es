---
title: "Copias de seguridad automáticas con redundancia geográfica de Azure SQL Database | Microsoft Docs"
description: "SQL Database crea automáticamente una copia de seguridad local de la base de datos cada pocos minutos y usa almacenamiento con redundancia geográfica con acceso de lectura de Azure para proporcionar redundancia geográfica."
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 3ee3d49d-16fa-47cf-a3ab-7b22aa491a8d
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: Active
ms.date: 07/05/2017
ms.author: carlrab
ms.openlocfilehash: 25fa8d03a206452c17e5d4ad38211203aeb5dd65
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2017
---
# <a name="learn-about-automatic-sql-database-backups"></a>Más información sobre copias de seguridad automáticas de SQL Database

SQL Database crea automáticamente copias de seguridad de la base de datos y usa almacenamiento con redundancia geográfica con acceso de lectura de Azure (RA-GRS) para ofrecer redundancia geográfica. Estas copias de seguridad se crean automáticamente y sin cargos adicionales. No es necesario hacer nada para que se produzcan. Las copias de seguridad de base de datos son una parte esencial de cualquier estrategia de recuperación ante desastres y continuidad del negocio, ya que protegen los datos de daños o eliminaciones accidentales. Si desea conservar las copias de seguridad en su propio contenedor de almacenamiento, puede configurar una directiva de retención de copias de seguridad a largo plazo. Para obtener más información, consulte el tema sobre la [retención a largo plazo](sql-database-long-term-retention.md).

## <a name="what-is-a-sql-database-backup"></a>¿Qué es una copia de seguridad de SQL Database?

SQL Database emplea tecnología de SQL Server para crear copias de seguridad [completas](https://msdn.microsoft.com/library/ms186289.aspx), [diferenciales](https://msdn.microsoft.com/library/ms175526.aspx) y del [registro de transacciones](https://msdn.microsoft.com/library/ms191429.aspx). Por lo general, las copias de seguridad del registro de transacciones se producen cada 5-10 minutos, y la frecuencia depende del nivel de rendimiento y de la cantidad de actividad de la base de datos. Las copias del registro de transacciones, junto con las completas y diferenciales, permiten restaurar una base de datos a un momento específico en el mismo servidor que hospede la base de datos. Cuando se restaura una base de datos, el servicio calcula qué copia de seguridad completa, diferencial o del registro de transacciones es necesario restaurar.


Puede utilizar estas copias de seguridad para realizar lo siguiente:

* Restaurar una base de datos a un momento dado dentro del período de retención. Esta operación creará una base de datos nueva en el mismo servidor de la base de datos original.
* Restaurar una base de datos eliminada al momento en que se eliminó o a cualquier otro punto comprendido en el periodo de retención. La base de datos eliminada solo se puede restaurar en el mismo servidor donde se creara la original.
* Restaurar una base de datos en otra región geográfica. Esta opción permite recuperarse de un desastre en una región geográfica cuando no puede acceder a su servidor y base de datos. Crea una nueva base de datos en cualquier servidor existente del mundo. 
* Restaurar una base de datos desde una copia de seguridad específica guardada en el almacén de Azure Recovery Services. Esta opción permite restaurar una versión antigua de la base de datos para respetar una solicitud de cumplimiento o ejecutar una versión antigua de la aplicación. Consulte el tema sobre la [retención a largo plazo](sql-database-long-term-retention.md).
* Para llevar a cabo una restauración, consulte el artículo sobre la [restauración de bases de datos a partir de copias de seguridad](sql-database-recovery-using-backups.md).

> [!NOTE]
> En Azure Storage, el término *replicación* hace referencia a la copia de archivos desde una ubicación a otra. La *replicación de base de datos* de SQL hace referencia a mantener varias bases de datos secundarias sincronizadas con una base de datos principal. 
> 

## <a name="how-much-backup-storage-is-included-at-no-cost"></a>¿Cuánto almacenamiento de copia de seguridad se incluye sin costo?
SQL Database proporciona hasta un 200 % de almacenamiento de base de datos aprovisionado máximo, para que pueda almacenar copias de seguridad, sin costos adicionales. Por ejemplo, si tiene una instancia de base de datos de tipo Estándar con un tamaño de base de datos aprovisionado de 250 GB, tendrá 500 GB para almacenar copias de seguridad sin costos adicionales. 

## <a name="how-often-do-backups-happen"></a>¿Con qué frecuencia se producen las copias de seguridad?
Las copias de seguridad de bases de datos completas se producen con una frecuencia semanal, las diferenciales tienden a llevarse a cabo en intervalos de pocas horas y las de registro de transacciones suelen realizarse cada 5-10 minutos. La primera copia de seguridad completa se programa inmediatamente después de la creación de la base de datos. Normalmente, se completa en 30 minutos pero puede tardar más si la base de datos tiene un tamaño considerable. Por ejemplo, la copia de seguridad inicial puede tardar más en una base de datos restaurada o una copia de la base de datos. Después de la primera copia de seguridad completa, todas las copias de seguridad adicionales se programan automáticamente y se administran silenciosamente en segundo plano. El servicio SQL Database determina el momento exacto en el que se producen todas las copias de seguridad de la base de datos a medida que equilibra la carga de trabajo global del sistema. 

La replicación geográfica del almacenamiento de copias de seguridad se produce según la programación de replicación de Azure Storage.

## <a name="how-long-do-you-keep-my-backups"></a>¿Cuánto tiempo se mantienen las copias de seguridad?
Cada copia de seguridad de SQL Database tiene un período de retención que se basa en el [nivel de servicio](sql-database-service-tiers.md) de la base de datos. El período de retención de una base de datos es el siguiente:


* Nivel de servicio Básico: 7 días.
* Nivel de servicio Estándar: 35 días
* Nivel de servicio Premium: 35 días.

Si cambia la base de datos de los niveles de servicio Estándar o Premium a Básico, las copias de seguridad se guardan durante siete días. Todas las copias de seguridad existentes con una antigüedad de más de siete días ya no estarán disponibles. 

Si actualiza la base de datos del nivel de servicio Básico a Estándar o Premium, SQL Database mantiene las copias de seguridad existentes hasta que tienen 35 días de antigüedad. Además, mantiene nuevas copias de seguridad que se producen durante 35 días.

Si elimina una base de datos, SQL Database mantiene las copias de seguridad de la misma manera que para una base de datos en línea. Por ejemplo, suponga que elimina una base de datos básica que tiene un período de retención de siete días. Una copia de seguridad con cuatro días de antigüedad se guarda durante tres días más.

> [!IMPORTANT]
> Si elimina el servidor de Azure SQL que hospeda las instancias de SQL Database, todas las bases de datos que pertenecen al servidor también se eliminan y no se pueden recuperar. No puede restaurar un servidor eliminado.
> 

## <a name="how-to-extend-the-backup-retention-period"></a>¿Cómo se puede ampliar el periodo de retención de las copias de seguridad?
Si la aplicación requiere que las copias de seguridad estén disponibles durante un periodo mayor, puede ampliar el periodo de retención integrado configurando la directiva de retención de copias de seguridad a largo plazo para bases de datos individuales (directiva de LTR). De este modo, podrá ampliar el periodo de retención integrado de 35 días a un máximo de 10 años. Para más información, consulte [retención a largo plazo](sql-database-long-term-retention.md).

Una vez que agregue la directiva de LTR a una base de datos mediante Azure Portal o la API, las copias de seguridad completas de la base de datos semanales se copiarán de forma automática en su almacén del servicio Azure Backup. Si la base de datos se cifra con TDE, las copias de seguridad se cifran automáticamente en reposo.  El almacén de Recovery Services eliminará de forma automática sus copias de seguridad caducadas según su fecha y hora y la directiva de LTR.  Por ello, no tiene que administrar la programación de copias de seguridad ni preocuparse por la limpieza de archivos antiguos. La API de restauración admite las copias de seguridad almacenadas en el almacén siempre que este se encuentre en la misma suscripción que la base de datos SQL. Puede usar Azure Portal o PowerShell para acceder a estas copias de seguridad.

> [!TIP]
> Para obtener una guía de procedimientos, vea [Configuración de la retención de copias de seguridad a largo plazo de Azure SQL Database](sql-database-long-term-backup-retention-configure.md).
>

## <a name="are-backups-encrypted"></a>¿Se cifran las copias de seguridad?

Cuando TDE está habilitado para una instancia de Azure SQL Database, también se cifran las copias de seguridad. Todas las instancias nuevas de Azure SQL Database están configuradas con TDE habilitado de forma predeterminada. Para más información, vea [Cifrado de datos transparente con Azure SQL Database](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql).

## <a name="next-steps"></a>Pasos siguientes

- Las copias de seguridad de base de datos son una parte esencial de cualquier estrategia de recuperación ante desastres y continuidad del negocio, ya que protegen los datos de daños o eliminaciones accidentales. Para descubrir otras soluciones de continuidad empresarial de Azure SQL Database, consulte el artículo de [información general sobre la continuidad empresarial](sql-database-business-continuity.md).
- Para restaurar a un momento dado mediante Azure Portal, consulte [Restauración de una base de datos SQL de Azure a un momento dado anterior con Azure Portal](sql-database-recovery-using-backups.md).
- Para restaurar a un momento dado mediante PowerShell, consulte [Restauración de una base de datos SQL de Azure a un momento dado anterior con PowerShell](scripts/sql-database-restore-database-powershell.md).
- Para configurar, administrar y restaurar desde la retención a largo plazo de copias de seguridad automatizadas en un almacén de Azure Recovery Services mediante Azure Portal, consulte [Administración de la retención de copia de seguridad a largo plazo mediante Azure Portal](sql-database-long-term-backup-retention-configure.md).
- Para configurar, administrar y restaurar desde la retención a largo plazo de copias de seguridad automatizadas en un almacén de Azure Recovery Services mediante PowerShell, consulte [Administración de la retención de copia de seguridad a largo plazo mediante PowerShell](sql-database-long-term-backup-retention-configure.md).
