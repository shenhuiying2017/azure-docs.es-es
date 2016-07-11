<properties
	pageTitle="Escenarios de continuidad empresarial de Base de datos SQL de Azure | Microsoft Azure"
	description="Escenarios de continuidad empresarial de Base de datos SQL de Azure"
	services="sql-database"
	documentationCenter=""
	authors="carlrabeler"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="06/16/2016"
	ms.author="carlrab"
   ms.workload="sqldb-bcdr"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>



# Escenarios de continuidad empresarial de Base de datos SQL de Microsoft Azure

Este artículo presenta varios escenarios de recuperación ante desastres y varios escenarios de diseño de aplicaciones para la continuidad empresarial.

## Recuperación tras una interrupción

En el caso de una interrupción, [Restauración de una base de datos SQL de Azure o una conmutación por error en una secundaria](sql-database-disaster-recovery.md) describe cómo utilizar las siguientes soluciones de continuidad empresarial para recuperarse de la interrupción:

- [Replicación geográfica activa](sql-database-geo-replication-overview.md)
- [Restauración geográfica](sql-database-recovery-using.backups.md#geo-restore)

Los pasos y el período de tiempo específicos que se requieren para recuperarse de una interrupción variarán dependiendo de la solución de continuidad empresarial que elija. Sin embargo, independientemente de su solución de continuidad empresarial, necesita saber cuándo iniciar la recuperación, los pasos de recuperación de base de datos para cada solución de continuidad empresarial, cómo configurar la base de datos después de la recuperación y cómo configurar la aplicación después de la recuperación para completar la recuperación tras una interrupción.

## Recuperación tras un error

Si se produce un error de usuario u otro error de modificación de datos no deseado, [Recuperar una base de datos SQL de Azure de un error de usuario](sql-database-user-error-recovery.md) describe cómo utilizar las siguientes soluciones de continuidad empresarial para recuperarse del error:

- [Restauración a un momento dado](sql-database-recovery-using-backups.md#point-in-time-restore)
- [Restaurar la base de datos eliminada](sql-database-recovery-using-backups.md#deleted-database-restore)

Los pasos y el período de tiempo específicos que se requieren para recuperarse de una interrupción variarán dependiendo de la solución de continuidad empresarial que elija. Sin embargo, independientemente de su solución de continuidad empresarial, necesita saber cómo recuperarse de un error para cada solución de continuidad empresarial.

## Obtención de detalles de la recuperación ante desastres para prepararse para una interrupción

Para que la solución de continuidad empresarial sea eficaz, se recomienda validar periódicamente el flujo de trabajo de preparación de la aplicación para la recuperación. Comprobar el comportamiento de la aplicación y las implicaciones de las pérdidas de datos o de las interrupciones que conlleva la conmutación por error es una buena práctica de ingeniería. También es un requisito de la mayoría de estándares del sector como parte de la certificación de continuidad del negocio.

Obtener los detalles de una recuperación ante desastres implica lo siguiente:

- Simular la interrupción del nivel de datos.
- Realizar la recuperación.
- Validar la integridad de la aplicación tras la recuperación.

En el artículo [Obtención de detalles de la recuperación ante desastres](sql-database-disaster-recovery-drills.md) se describe cómo realizar maniobras de recuperación ante desastres empleando las siguientes soluciones de continuidad empresarial:

- [Replicación geográfica activa](sql-database-geo-replication-overview.md)
- [Restauración geográfica](sql-database-recovery-using-backups.md#geo-restore)

## Administración de actualizaciones graduales de aplicaciones en la nube mediante la replicación geográfica activa

La actualización de una aplicación en la nube con una Base de datos SQL puede interrumpir los procesos en curso y, por lo tanto, debe incluir este escenario como parte del diseño y la planeación de la continuidad empresarial. En el artículo sobre la [administración de actualizaciones de aplicaciones](sql-database-manage-application-rolling-upgrade.md) se describe cómo usar la [replicación geográfica](sql-database-geo-replication-overview.md) en Base de datos SQL para permitir actualizaciones graduales de la aplicación en la nube. En este artículo veremos dos métodos diferentes de organizar el proceso de actualización y comentaremos las ventajas y desventajas de cada opción.

## Diseño de una aplicación para la recuperación ante desastres en la nube mediante la funcionalidad de replicación geográfica activa

En el artículo [Diseño de una aplicación para la recuperación ante desastres en la nube mediante replicación geográfica activa en Base de datos SQL](sql-database-designing-cloud-solutions-for-disaster-recovery.md) se describe cómo usar la [replicación geográfica activa](sql-database-geo-replication-overview.md) de Base de datos SQL para diseñar aplicaciones de base de datos resistentes a errores regionales e interrupciones graves. En este artículo se tiene en cuenta la topología de implementación de aplicaciones, el Acuerdo de Nivel de Servicio objetivo, la latencia de tráfico y los costos; después, se analizan los patrones comunes de aplicaciones (cada uno con sus ventajas y desventajas).

## Estrategias de recuperación ante desastres para aplicaciones que usan grupos de bases de datos elásticas

En el artículo [Estrategias de recuperación ante desastres para aplicaciones que usan el grupo elástico de Base de datos SQL](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md) se describen los escenarios de recuperación ante desastres mediante [grupos de bases de datos elásticas](sql-database-elastic-pool.md).

## Pasos siguientes

- Para obtener una descripción general de la continuidad empresarial, consulte [Información general: continuidad del negocio en la nube y recuperación ante desastres con la Base de datos SQL de Azure](sql-database-business-continuity.md).
- Para obtener información sobre las copias de seguridad automatizadas de Base de datos SQL de Azure, consulte [Información general: copias de seguridad automatizadas de Base de datos SQL](sql-database-automated-backups.md).
- Para obtener información sobre los escenarios de recuperación y diseño de la continuidad empresarial, consulte [Business continuity scenarios for Azure SQL Database](sql-database-business-continuity-scenarios.md) (Escenarios de continuidad empresarial para Base de datos SQL de Azure).
- Si quiere saber cómo utilizar las copias de seguridad automatizadas para procesos de recuperación, consulte [Recover an Azure SQL database using automated database backups](sql-database-recovery-using-backups.md) (Recuperación de una base de datos SQL de Azure mediante copias de seguridad de base de datos automatizadas).
- Para conocer las opciones de recuperación más rápidas, consulte el artículo sobre [replicación geográfica activa](sql-database-geo-replication-overview.md).
- Si quiere aprender a utilizar las copias de seguridad automatizadas para procesos de archivado, consulte el artículo de [copia de bases de datos](sql-database-copy.md).

<!---HONumber=AcomDC_0629_2016-->