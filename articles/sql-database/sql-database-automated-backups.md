---
title: Copias de seguridad automáticas con redundancia geográfica de Azure SQL Database | Microsoft Docs
description: SQL Database crea automáticamente una copia de seguridad local de la base de datos cada pocos minutos y usa almacenamiento con redundancia geográfica con acceso de lectura de Azure para proporcionar redundancia geográfica.
services: sql-database
author: anosov1960
manager: craigg
ms.service: sql-database
ms.custom: business continuity
ms.topic: article
ms.workload: Active
ms.date: 04/04/2018
ms.author: sashan
ms.reviewer: carlrab
ms.openlocfilehash: 37bbbf8ea5a5d8439b300d0740e4f1a048e98e91
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/28/2018
ms.locfileid: "32189075"
---
# <a name="learn-about-automatic-sql-database-backups"></a>Más información sobre copias de seguridad automáticas de SQL Database

SQL Database crea automáticamente copias de seguridad de la base de datos y usa almacenamiento con redundancia geográfica con acceso de lectura de Azure (RA-GRS) para ofrecer redundancia geográfica. Estas copias de seguridad se crean automáticamente y sin cargos adicionales. No es necesario hacer nada para que se produzcan. Las copias de seguridad de base de datos son una parte esencial de cualquier estrategia de recuperación ante desastres y continuidad del negocio, ya que protegen los datos de daños o eliminaciones accidentales. Si desea conservar las copias de seguridad en su propio contenedor de almacenamiento, puede configurar una directiva de retención de copias de seguridad a largo plazo. Para más información, consulte [retención a largo plazo](sql-database-long-term-retention.md).

## <a name="what-is-a-sql-database-backup"></a>¿Qué es una copia de seguridad de SQL Database?

SQL Database emplea tecnología de SQL Server para crear copias de seguridad [completas](https://msdn.microsoft.com/library/ms186289.aspx), [diferenciales](https://msdn.microsoft.com/library/ms175526.aspx) y de [registro de transacciones](https://msdn.microsoft.com/library/ms191429.aspx) para la restauración a un momento dado. Por lo general, las copias de seguridad del registro de transacciones se producen cada 5-10 minutos, y la frecuencia depende del nivel de rendimiento y de la cantidad de actividad de la base de datos. Las copias del registro de transacciones, junto con las completas y diferenciales, permiten restaurar una base de datos a un momento específico en el mismo servidor que hospede la base de datos. Cuando se restaura una base de datos, el servicio calcula qué copia de seguridad completa, diferencial o del registro de transacciones es necesario restaurar.


Puede utilizar estas copias de seguridad para realizar lo siguiente:

* Restaurar una base de datos a un momento dado dentro del período de retención. Esta operación creará una base de datos nueva en el mismo servidor de la base de datos original.
* Restaurar una base de datos eliminada al momento en que se eliminó o a cualquier otro punto comprendido en el periodo de retención. La base de datos eliminada solo se puede restaurar en el mismo servidor donde se creara la original.
* Restaurar una base de datos en otra región geográfica. Esta opción permite recuperarse de un desastre en una región geográfica cuando no puede acceder a su servidor y base de datos. Crea una nueva base de datos en cualquier servidor existente del mundo. 
* Restaure una base de datos desde una copia de seguridad a largo plazo específica si la base de datos se ha configurado con una directiva de retención a largo plazo. Esta opción permite restaurar una versión antigua de la base de datos para respetar una solicitud de cumplimiento o ejecutar una versión antigua de la aplicación. Consulte el tema sobre la [retención a largo plazo](sql-database-long-term-retention.md).
* Para llevar a cabo una restauración, consulte el artículo sobre la [restauración de bases de datos a partir de copias de seguridad](sql-database-recovery-using-backups.md).

> [!NOTE]
> En Azure Storage, el término *replicación* hace referencia a la copia de archivos desde una ubicación a otra. La *replicación de base de datos* de SQL hace referencia a mantener varias bases de datos secundarias sincronizadas con una base de datos principal. 
> 

## <a name="how-often-do-backups-happen"></a>¿Con qué frecuencia se producen las copias de seguridad?
Las copias de seguridad de bases de datos completas se producen con una frecuencia semanal, las diferenciales tienden a llevarse a cabo en intervalos de pocas horas y las de registro de transacciones suelen realizarse cada 5-10 minutos. La primera copia de seguridad completa se programa inmediatamente después de la creación de la base de datos. Normalmente, se completa en 30 minutos pero puede tardar más si la base de datos tiene un tamaño considerable. Por ejemplo, la copia de seguridad inicial puede tardar más en una base de datos restaurada o una copia de la base de datos. Después de la primera copia de seguridad completa, todas las copias de seguridad adicionales se programan automáticamente y se administran silenciosamente en segundo plano. El servicio SQL Database determina el momento exacto en el que se producen todas las copias de seguridad de la base de datos a medida que equilibra la carga de trabajo global del sistema. 

La replicación geográfica del almacenamiento de copias de seguridad se produce según la programación de replicación de Azure Storage.

## <a name="how-long-do-you-keep-my-backups"></a>¿Cuánto tiempo se mantienen las copias de seguridad?
Cada copia de seguridad de SQL Database tiene un período de retención que se basa en el nivel de servicio de la base de datos y es diferente al [modelo de compra basado en DTU](sql-database-service-tiers-dtu.md) y al [modelo de compra basado en núcleos virtuales (versión preliminar)](sql-database-service-tiers-vcore.md). 


### <a name="database-retention-for-dtu-based-purchasing-model"></a>Retención de la base de datos en el modelo de compra basado en DTU
El período de retención para una base de datos en el modelo de compra basado en DTU depende del nivel de servicio. El período de retención de una base de datos es el siguiente:

* Nivel de servicio Básico: 7 días.
* Nivel de servicio Estándar: 35 días
* Nivel de servicio Premium: 35 días.
* El nivel de uso general es configurable con un máximo de 35 días (7 días de forma predeterminada)*
* El nivel de crítico para la empresa es configurable con un máximo de 35 días (7 días de forma predeterminada)*

\* Durante la versión preliminar, el período de retención de copias de seguridad no es configurable y permanece fijo en 7 días.

Si convierte una base de datos con una retención de copias de seguridad más larga a una base de datos con una retención más corta, dejarán de estar disponibles todas las copias de seguridad existentes más antiguas que el período de retención de nivel de destino.

Si actualiza una base de datos con un período de retención más corto a una base de datos con un período más largo, SQL Database conserva las copias de seguridad existentes hasta que se alcance el período de retención. 

Si elimina una base de datos, SQL Database mantiene las copias de seguridad de la misma manera que para una base de datos en línea. Por ejemplo, suponga que elimina una base de datos básica que tiene un período de retención de siete días. Una copia de seguridad con cuatro días de antigüedad se guarda durante tres días más.

> [!IMPORTANT]
> Si elimina el servidor de Azure SQL que hospeda las instancias de SQL Database, todas las bases de datos que pertenecen al servidor también se eliminan y no se pueden recuperar. No puede restaurar un servidor eliminado.

### <a name="database-retention-for-the-vcore-based-purchasing-model-preview"></a>Retención de la base de datos para el modelo compra basado en núcleos virtuales (versión preliminar)

Para admitir las funcionalidades Restauración a un momento dado (PITR) y Retención a largo plazo (LTR) de SQL Database, se asigna almacenamiento a las copias de seguridad de base de datos. Este almacenamiento se asigna por separado para cada base de datos y se factura como dos cargos independientes por base de datos. 

- **PITR**: las copias de seguridad de base de datos individuales se copian en el almacenamiento de RA-GRS automáticamente. El tamaño de almacenamiento aumenta dinámicamente a medida que se crean las nuevas copias de seguridad.  El almacenamiento se usa para realizar cada cinco minutos copias de seguridad completas semanales, copias de seguridad diferenciales diarias y copias de seguridad de registros de transacciones. El consumo de almacenamiento depende de la tasa de cambio de la base de datos y del período de retención. Puede configurar un período de retención diferente para cada base de datos de entre 7 y 35 días. Se ofrece una cantidad de almacenamiento mínimo igual al tamaño de los datos sin costo adicional. En la mayoría de las bases de datos, esta cantidad es suficiente para almacenar copias de seguridad durante 7 días. Para más información, consulte [Restauración a un momento dado](sql-database-recovery-using-backups.md#point-in-time-restore).
- **LTR**: SQL Database ofrece la opción de configurar la retención a largo plazo de copias de seguridad completas durante un período máximo de 10 años. Si la directiva de LTR está habilitada, estas copias de seguridad se almacenan en almacenamiento RA-GRS automáticamente, pero puede controlar la frecuencia con que se realizan las copias de seguridad. Para satisfacer los distintos requisitos de cumplimiento, puede seleccionar distintos períodos de retención para copias de seguridad semanales, mensuales o anuales. Esta configuración definirá la cantidad de almacenamiento que se usará para las copias de seguridad de LTR. Para estimar el costo del almacenamiento de LTR, se puede usar la calculadora de precios de LTR. Para más información, consulte [retención a largo plazo](sql-database-long-term-retention.md).

## <a name="how-to-extend-the-backup-retention-period"></a>¿Cómo se puede ampliar el periodo de retención de las copias de seguridad?

Si la aplicación requiere que las copias de seguridad estén disponibles durante un periodo mayor al periodo de retención de copias de seguridad de PITR, puede configurar una directiva de retención de copias de seguridad a largo plazo para bases de datos individuales (directiva de LTR). De este modo, podrá ampliar el periodo de retención integrado del máximo de 35 días a 10 años. Para más información, consulte [retención a largo plazo](sql-database-long-term-retention.md).

Una vez que agregue la directiva de LTR a una base de datos mediante Azure Portal o la API, las copias de seguridad completas de la base de datos semanales se copiarán de forma automática a un contenedor de almacenamiento de RA-GRS independiente para la retención a largo plazo (almacenamiento LTR). Si la base de datos se cifra con TDE, las copias de seguridad se cifran automáticamente en reposo. SQL Database eliminará de forma automática sus copias de seguridad caducadas según su fecha y hora y la directiva de LTR. Una vez configurada la directiva, no tiene que administrar la programación de copias de seguridad ni preocuparse por la limpieza de archivos antiguos. Puede usar Azure Portal o PowerShell para ver, restaurar o eliminar estas copias de seguridad.

## <a name="are-backups-encrypted"></a>¿Se cifran las copias de seguridad?

Cuando TDE está habilitado para una instancia de Azure SQL Database, también se cifran las copias de seguridad. Todas las instancias nuevas de Azure SQL Database están configuradas con TDE habilitado de forma predeterminada. Para más información, vea [Cifrado de datos transparente con Azure SQL Database](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql).

## <a name="are-the-automatic-backups-compliant-with-gdpr"></a>¿Las copias de seguridad automáticas son compatibles con el RGPD?
Si la copia de seguridad contiene datos personales, que están sujetos a Reglamento general de protección de datos (RGPD), debe aplicar medidas de seguridad mejoradas para proteger los datos contra el acceso no autorizado. Para cumplir con el RGPD, necesita una manera de administrar las solicitudes de datos de los propietarios de datos sin necesidad de acceder a las copias de seguridad.  En cuanto a las copias de seguridad a corto plazo, una solución puede ser acortar la ventana de la copia de seguridad a menos de 30 días, que es el tiempo permitido para completar las solicitudes de acceso de datos.  Si se requieren copias de seguridad a más largo plazo, se recomienda almacenar solo los datos en formato anónimo en las copias de seguridad. Por ejemplo, si es necesario eliminar o actualizar la información sobre una persona, no será necesario eliminar o actualizar las copias de seguridad existentes. Puede encontrar más información acerca de las prácticas recomendadas de RGPD en [Data Governance for GDPR Compliance](https://info.microsoft.com/DataGovernanceforGDPRCompliancePrinciplesProcessesandPractices-Registration.html) (Gobernanza de datos para el cumplimiento de RGPD).

## <a name="next-steps"></a>Pasos siguientes

- Las copias de seguridad de base de datos son una parte esencial de cualquier estrategia de recuperación ante desastres y continuidad del negocio, ya que protegen los datos de daños o eliminaciones accidentales. Para descubrir otras soluciones de continuidad empresarial de Azure SQL Database, consulte el artículo de [información general sobre la continuidad empresarial](sql-database-business-continuity.md).
- Para restaurar a un momento dado mediante Azure Portal, consulte [Restauración de una base de datos SQL de Azure a un momento dado anterior con Azure Portal](sql-database-recovery-using-backups.md).
- Para restaurar a un momento dado mediante PowerShell, consulte [Restauración de una base de datos SQL de Azure a un momento dado anterior con PowerShell](scripts/sql-database-restore-database-powershell.md).
- Para configurar, administrar y restaurar desde la retención a largo plazo de copias de seguridad automatizadas en un almacén de Azure Recovery Services mediante Azure Portal, consulte [Administración de la retención de copia de seguridad a largo plazo mediante Azure Portal](sql-database-long-term-backup-retention-configure.md).
- Para configurar, administrar y restaurar desde la retención a largo plazo de copias de seguridad automatizadas en un almacén de Azure Recovery Services mediante PowerShell, consulte [Administración de la retención de copia de seguridad a largo plazo mediante PowerShell](sql-database-long-term-backup-retention-configure.md).
