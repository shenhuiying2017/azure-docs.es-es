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
ms.workload: On Demand
ms.date: 12/22/2016
ms.author: sashan
ms.openlocfilehash: e44c92c3f37b3f1e3397d1c8cdb8c8f6d0f9942e
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/31/2017
---
# <a name="store-azure-sql-database-backups-for-up-to-10-years"></a>Almacenamiento de copias de seguridad de Azure SQL Database durante diez años como máximo
Muchas aplicaciones tienen finalidades normativas, de conformidad u otras de carácter empresarial que requieren que se conserven copias de seguridad de las bases de datos más allá de entre los 7 y 35 días proporcionados por las [copias de seguridad automáticas](sql-database-automated-backups.md) de Azure SQL Database. Al usar la función de retención de copias de seguridad a largo plazo, puede almacenar las copias de seguridad de las bases de datos SQL en un almacén de Azure Recovery Services hasta diez años. Puede almacenar hasta 1000 bases de datos por almacén. Puede después seleccionar cualquier copia de seguridad del almacén para restaurarla como una base de datos nueva.

> [!IMPORTANT]
> La retención de copias de seguridad a largo plazo está actualmente disponible en versión preliminar en las siguientes regiones: este de Australia, sudeste de Australia, sur de Brasil, centro de EE. UU., Asia oriental, este de EE. UU., este de EE. UU. 2, India central, sur de la India, Japón oriental, Japón Occidental, centro-norte de EE. UU., Europa del Norte, centro-sur de EE. UU., Sudeste Asiático, Europa Occidental y oeste de EE. UU.
>

> [!NOTE]
> Puede habilitar hasta 200 bases de datos por almacén durante 24 horas. Se recomienda que use un almacén independiente para cada servidor para minimizar el impacto de este límite. 
> 

## <a name="how-sql-database-long-term-backup-retention-works"></a>¿Cómo funciona la retención de copias de seguridad a largo plazo de SQL Database?

Con la retención de copias de seguridad a largo plazo, puede asociar un servidor de bases de datos SQL a un almacén de Azure Recovery Services. 

* Debe crear el almacén en la misma suscripción de Azure que creó el servidor SQL Server y en la misma región geográfica y grupo de recursos. 
* A continuación, configure una directiva de retención para cualquier base de datos. Esta directiva tiene como consecuencia que se copien las copias de seguridad completas semanales de la base de datos en el almacén de Recovery Services y que se conserven durante el periodo especificado de retención (hasta diez años). 
* Luego, puede restaurar la base de datos desde cualquiera de estas copias de seguridad a una base de datos nueva en cualquier servidor de la suscripción. Azure Storage crea una copia desde las copias de seguridad existentes, y esta copia no afecta al rendimiento de la base de datos existente.

> [!TIP]
> Para obtener una guía de procedimientos, consulte [Configuración y restauración de retención de copias de seguridad a largo plazo de Azure SQL Database](sql-database-long-term-backup-retention-configure.md).

## <a name="enable-long-term-backup-retention"></a>Habilitación de la retención de copias de seguridad a largo plazo

Para configurar la retención a largo plazo de copias de seguridad de una base de datos, es preciso:

1. Cree un almacén de Azure Recovery Services en la misma región, suscripción y grupo de recursos que el servidor de SQL Database. 
2. Registre el servidor en el almacén.
3. Cree una directiva de protección de Azure Recovery Services.
4. Aplique la directiva de protección a las bases de datos que requieren la retención de copias de seguridad a largo plazo.

Para configurar, administrar y restaurar una base de datos desde la retención de copias de seguridad automatizadas a largo plazo en un almacén de Azure Recovery Services, adopte una de las siguientes medidas:

* Desde Azure Portal: haga clic en **Retención de copias de seguridad a largo plazo**, seleccione una base de datos y, Luego, haga clic en **configurar**. 

   ![Seleccionar base de datos para retención de copias de seguridad a largo plazo](./media/sql-database-get-started-backup-recovery/select-database-for-long-term-backup-retention.png)

* Desde PowerShell: vaya a [Configuración y restauración de retención de copias de seguridad a largo plazo de Azure SQL Database](sql-database-long-term-backup-retention-configure.md).

## <a name="restore-a-database-thats-stored-with-the-long-term-backup-retention-feature"></a>Restauración de una base de datos almacenada con la función de retención de copias de seguridad a largo plazo

Para recuperar desde una copia de seguridad de retención a largo plazo, es preciso:

1. incluir en una lista el almacén donde se almacena la copia de seguridad,
2. incluir en una lista el contenedor que se asigna a su servidor lógico,
3. incluir en una lista el origen de datos en el almacén que se asigna a la base de datos,
4. incluir en una lista de los puntos de restauración disponibles para restaurar,
5. restaurar la base de datos desde el punto de recuperación en el servidor de destino en su suscripción.

Para configurar, administrar y restaurar una base de datos desde la retención de copias de seguridad automatizadas a largo plazo en un almacén de Azure Recovery Services, adopte una de las siguientes medidas:

* Desde Azure Portal: vaya a [Administración de la retención de copias de seguridad a largo plazo mediante Azure Portal](sql-database-long-term-backup-retention-configure.md). 

* Desde PowerShell: vaya a [Administración de la retención de copias de seguridad a largo plazo mediante PowerShell](sql-database-long-term-backup-retention-configure.md).

## <a name="get-pricing-for-long-term-backup-retention"></a>Obtención de precios para la retención de copias de seguridad a largo plazo

La retención de copias de seguridad a largo plazo de una base de datos SQL se cobra según las [tarifas de precios de servicios de copias de seguridad de Azure](https://azure.microsoft.com/pricing/details/backup/).

Una vez que el servidor de bases de datos SQL esté registrado en el almacén, se le cobra por el almacenamiento total utilizado por las copias de seguridad semanales guardadas en el almacén.

## <a name="view-available-backups-that-are-stored-in-long-term-backup-retention"></a>Consulta de las copias de seguridad disponibles almacenadas en retención de copias de seguridad a largo plazo

Para configurar, administrar y restaurar una base de datos desde la retención de copias de seguridad automatizadas a largo plazo en un almacén de Azure Recovery Services mediante Azure Portal, adopte una de las siguientes medidas:

* Desde Azure Portal: vaya a [Administración de la retención de copias de seguridad a largo plazo mediante Azure Portal](sql-database-long-term-backup-retention-configure.md). 

* Desde PowerShell: vaya a [Administración de la retención de copias de seguridad a largo plazo mediante PowerShell](sql-database-long-term-backup-retention-configure.md).

## <a name="disable-long-term-retention"></a>Deshabilitación de la retención a largo plazo

El servicio de recuperación gestiona automáticamente la limpieza de las copias de seguridad según la directiva de retención proporcionada. 

Para detener el envío de copias de seguridad de una base de datos específica al almacén, elimine la directiva de retención de esa base de datos.
  
```
Set-AzureRmSqlDatabaseBackupLongTermRetentionPolicy -ResourceGroupName 'RG1' -ServerName 'Server1' -DatabaseName 'DB1' -State 'Disabled' -ResourceId $policy.Id
```

> [!NOTE]
> Las copias de seguridad que ya están en el almacén no se ven afectadas. El servicio de recuperación la elimina automáticamente cuando termina el periodo de retención.

## <a name="long-term-backup-retention-faq"></a>Preguntas frecuentes sobre la retención de copias de seguridad a largo plazo

**¿Puedo eliminar manualmente copias de seguridad específicas del almacén?**

Actualmente, no. El almacén limpiará automáticamente las copias de seguridad cuando haya terminado el período de retención.

**¿Puedo registrar mi servidor para guardar las copias de seguridad en más de un almacén?**

No, actualmente solo puede guardar copias de seguridad en un almacén a la vez.

**¿Puedo tener un almacén y un servidor en suscripciones distintas?**

No, actualmente el almacén y el servidor deben estar en la misma suscripción y el grupo de recursos.

**¿Puedo usar un almacén que he creado en una región que es distinta a la de mi servidor?**

No, el almacén y el servidor deben estar en la misma región para minimizar el tiempo de copia y evitar cargos de tráfico.

**¿Cuántas bases de datos puedo guardar en un almacén?**

Actualmente se admiten hasta 1000 bases de datos por almacén. 

**¿Cuántos almacenes puedo crear por suscripción?**

Se pueden crear hasta 25 almacenes por suscripción.

**¿Cuántas bases de datos puedo configurar diariamente por almacén?**

Puede configurar 200 bases de datos diariamente por almacén.

**¿La retención de copias de seguridad a largo plazo funciona con grupos elásticos?**

Sí. Cualquier base de datos del grupo puede configurarse con la directiva de retención.

**¿Puedo elegir el momento en que se crea la copia de seguridad?**

No, SQL Database controla la programación de copias de seguridad con el fin de minimizar el impacto del rendimiento en las bases de datos.

**Si dispone de cifrado de datos transparente habilitado para la base de datos. ¿Puedo usarlo con el almacén?** 

Si, se admite el cifrado de datos transparente. Puede restaurar la base de datos desde el almacén incluso si la base de datos original ya no existe.

**¿Qué ocurre con las copias de seguridad en el almacén si se suspende mi suscripción?** 

Si la suscripción se suspende, se conservan las bases de datos y las copias de seguridad existentes. Las copias de seguridad nuevas no se copian en el almacén. Después haber reactivado la suscripción, el servicio retoma el copiado de copias de seguridad en el almacén. El almacén estará accesible para las operaciones de restauración mediante el uso de copias de seguridad que se copiaron allí antes de que se suspendiera la suscripción. 

**¿Puedo obtener acceso a los archivos de copias de seguridad de la base de datos SQL Database para descargar o restaurar al servidor SQL Server?**

Actualmente, no.

**¿Es posible tener varias programaciones (diaria, semanal, mensual, anual) dentro de una directiva de retención de SQL?**

No, actualmente solo están disponibles varias programaciones para las copias de seguridad de máquinas virtuales.

**¿Qué ocurre si establecemos la retención de copias de seguridad a largo plazo en una base de datos secundaria con replicación geográfica activa?**

Dado que no creamos copias de seguridad de réplicas, actualmente no hay ninguna opción para la retención de copias de seguridad a largo plazo en bases de datos secundarias. Sin embargo, es importante que un usuario configure una retención de copias de seguridad a largo plazo en una base de datos secundaria con replicación geográfica activa por los siguientes motivos:
* Cuando se produce una conmutación por error y la base de datos se convierte en la principal, creamos una copia de seguridad completa y esta se carga al almacén.
* No hay costo adicional si el cliente configura la retención de copias de seguridad a largo plazo en una base de datos secundaria.

## <a name="next-steps"></a>Pasos siguientes
Dado que las copias de seguridad de bases de datos protegen los datos de eliminaciones o daños accidentales, son una parte esencial de cualquier estrategia de recuperación ante desastres y de continuidad empresarial. Para descubrir otras soluciones de continuidad empresarial de SQL Database, consulte el artículo de [información general sobre la continuidad empresarial](sql-database-business-continuity.md).
