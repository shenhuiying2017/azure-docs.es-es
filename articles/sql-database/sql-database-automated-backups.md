<properties
   pageTitle="Continuidad empresarial en la nube: copia de seguridad integrada (Base de datos SQL) | Microsoft Azure"
   description="Obtenga más información sobre las copias de seguridad integradas de Base de datos SQL que permiten revertir una Base de datos SQL de Azure a un momento dado previo o copiarla en una nueva base de datos en una región geográfica (hasta 35 días)."
   services="sql-database"
   documentationCenter=""
   authors="carlrabeler"
   manager="jhubbard"
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="06/16/2016"
   ms.author="carlrab"/>

# Información general: copias de seguridad automatizadas de Base de datos SQL

El servicio Base de datos SQL de Azure protege todas las bases de datos con una copia de seguridad automatizada que se conserva durante 7 días para el nivel Básico, 35 días para el Estándar y 35 días para el Premium. Consulte [Niveles de servicio](sql-database-service-tiers.md) para obtener más información sobre las características disponibles con cada nivel de servicio.

Las copias de seguridad de la base de datos se realizan automáticamente, sin necesidad de aceptarlo ni de asumir ningún cargo adicional. Estas copias de seguridad automatizadas y la restauración a un momento dado proporcionan una forma sin costos y sin administración para proteger las bases de datos de daños de un daño o eliminación accidental, con independencia de la causa. Puede utilizar estas copias de seguridad automatizadas para realizar restauraciones a un momento dado y restaurar una base de datos eliminada tras la eliminación o el daño de los datos por accidente.

## Costos de la copia de seguridad automatizada

La Base de datos SQL de Microsoft Azure le proporciona hasta un 200 % de almacenamiento de base de datos aprovisionado máximo, para que pueda almacenar copias de seguridad sin coste adicional. Por ejemplo, si tiene una instancia de base de datos de tipo Estándar con un tamaño de base de datos aprovisionado de 250 GB, se le proporcionarán 500 GB para almacenar sus copias de seguridad sin coste adicional. Si su base de datos excede el tamaño del almacenamiento de copias de seguridad suministrado, puede elegir reducir el período de retención poniéndose en contacto con el Servicio técnico de Azure o pagar el almacenamiento de copias de seguridad extra; si se decide por la segunda opción, esta se facturará según la tarifa de almacenamiento de redundancia geográfica con acceso de lectura (RA-GRS) estándar.

## Detalles de la copia de seguridad automatizada

Las bases de datos de niveles Básico, Estándar y Premium están protegidas por copias de seguridad automáticas. Las copias de seguridad de la base de datos se realizan cada semana, las copias de seguridad diferencial de base de datos, cada hora, y las copias de seguridad del registro de transacciones, cada cinco minutos. La primera copia de seguridad completa se programa inmediatamente después de la creación de la base de datos. Normalmente tarda 30 minutos en completarse, pero puede tardar más. Si una base de datos ya es grande, por ejemplo, si se crea como resultado de una copia de base de datos o de restauración de una base de datos grande, la primera copia de seguridad completa puede tardar más tiempo en completarse. Después de la primera copia de seguridad completa, todas las copias de seguridad adicionales se programan automáticamente y se administran silenciosamente en segundo plano. El tiempo exacto de las copias de seguridad completas y diferenciales viene determinado por el sistema para equilibrar la carga general.

## Redundancia geográfica

Los archivos de copia de seguridad se almacenan en una cuenta de almacenamiento con redundancia geográfica con acceso de lectura (RA-GRS) para garantizar la disponibilidad con fines de recuperación ante desastres. Esto garantiza que los archivos de copia de seguridad se repliquen en un [centro de datos asociado](../best-practices-availability-paired-regions.md). A continuación se muestra la replicación geográfica de las copias de seguridad semanales y diarias almacenadas en una cuenta de almacenamiento con redundancia geográfica con acceso de lectura (RA-GRS) para garantizar la disponibilidad con fines de recuperación ante desastres.

![restauración geográfica](./media/sql-database-geo-restore/geo-restore-1.png)

## Uso de copias de seguridad automatizadas

También puede [restaurar una base de datos desde las copias de seguridad automatizadas](sql-database-recovery-using-backups.md) durante el [periodo de retención](sql-database-service-tiers.md) en:

- Una base de datos nueva en el mismo servidor lógico recuperada en un punto especificado en el tiempo dentro del período de retención.
- Una base de datos en el mismo servidor lógico recuperada a la hora de eliminación de una base de datos eliminada.
- Una base de datos nueva en cualquier servidor lógico de cualquier región recuperada en las copias de seguridad diarias más recientes en el almacenamiento de blobs de replicación geográfica (RA-GRS).

También puede usar [copias de seguridad automatizadas de la Base de datos SQL](sql-database-automated-backups.md) para crear un [copia de la base de datos](sql-database-copy.md) en cualquier servidor lógico en cualquier región que sea transaccionalmente coherente con la Base de datos SQL actual. Puede usar la copia de la base de datos y [exportar a un BACPAC](sql-database-export.md) para archivar una copia transaccionalmente coherente de una base de datos durante el almacenamiento a largo plazo más allá de su período de retención o para transferir una copia de la base de datos a una instancia de máquina virtual local o de Azure de SQL Server.

## ¿Qué le ocurre a mi período de retención del punto de restauración al cambiar a una versión anterior/actualizar por nivel de servicio?

Después de cambiar a un nivel de rendimiento inferior, el período de retención del punto de restauración cambiará inmediatamente al período de retención del nivel de rendimiento de la base de datos actual. Si el nivel de servicio se actualiza, el período de retención se ampliará solo cuando se haya actualizado la base de datos. Por ejemplo, si la base de datos se degrada al nivel Básico, el período de retención cambiará inmediatamente de 35 días a 7 días, por lo que todos los puntos de restauración anteriores a 35 días dejarán de estar disponibles. Posteriormente, si se actualiza la base de datos a la versión Estándar o Premium, el período de retención comenzará a partir de 7 días y hasta 35 días.

## ¿Cuánto dura el período de retención para una base de datos que se ha quitado? 
El período de retención se determina según el nivel de servicio de la base de datos mientras esta existe o según el número de días que exista la base de datos, el menor de estos dos valores.

> [AZURE.IMPORTANT] Si elimina una instancia de servidor de Base de datos SQL de Azure, todas sus bases de datos también se eliminan y no se pueden recuperar. En estos momentos no es posible restaurar un servidor eliminado.

## Pasos siguientes

- Si quiere saber cómo utilizar las copias de seguridad automatizadas para procesos de recuperación, consulte [Recover an Azure SQL database using automated database backups](sql-database-recovery-using-backups.md) (Recuperación de una base de datos SQL de Azure mediante copias de seguridad de base de datos automatizadas).
- Para conocer las opciones de recuperación más rápidas, consulte el artículo sobre [replicación geográfica activa](sql-database-geo-replication-overview.md).
- Si quiere aprender a utilizar las copias de seguridad automatizadas para procesos de archivado, consulte el artículo de [copia de bases de datos](sql-database-copy.md).
- Para obtener una descripción general de la continuidad empresarial, consulte [Información general: continuidad del negocio en la nube y recuperación ante desastres con la Base de datos SQL de Azure](sql-database-business-continuity.md).

<!---HONumber=AcomDC_0727_2016-->