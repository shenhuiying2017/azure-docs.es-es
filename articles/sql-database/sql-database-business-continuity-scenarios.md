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
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>



# Escenarios de continuidad empresarial de Base de datos SQL de Microsoft Azure

> [AZURE.SELECTOR]
- [Continuidad del negocio](sql-database-business-continuity.md)
- [Escenarios](sql-database-business-continuity-scenarios.md)
- [Restauración a un momento dado](sql-database-point-in-time-retore.md)
- [Restaurar la base de datos eliminada](sql-database-restore-deleted-database.md)
- [Restauración geográfica](sql-database-geo-restore.md)
- [Replicación geográfica activa](sql-database-geo-replication)

En este artículo, conocerá los diferentes escenarios de continuidad empresarial de Base de datos SQL de Microsoft Azure.

## Recuperación tras una interrupción

En el artículo [Restauración de una base de datos SQL de Azure o una conmutación por error en una secundaria](sql-database-disaster-recovery.md) se describe cómo recuperarse ante una interrupción empleando las siguientes funcionalidades:

- [Replicación geográfica activa](sql-database-geo-replication-overview.md)
- [Restauración geográfica](sql-database-geo-restore.md)

En este artículo se describe cuándo iniciar la recuperación, cómo realizar el proceso de restauración con cada funcionalidad, además de cómo configurar la base de datos y la aplicación después de la recuperación.

## Recuperación ante un error de usuario

En el artículo [Recuperar una base de datos SQL de Azure de un error de usuario](sql-database-user-error-recovery.md) se describe cómo recuperar bases de datos de errores de usuario o modificaciones de datos no intencionadas empleando las siguientes funcionalidades:

- [Restauración a un momento dado](sql-database-point-in-time-restore.md) 
- [Restaurar la base de datos eliminada](sql-database-restore-deleted-database.md)

## Realización de maniobras de recuperación ante desastres

En el artículo [Realización de maniobras de recuperación ante desastres](sql-database-disaster-recovery-drills.md) se describe cómo realizar maniobras de recuperación ante desastres empleando las siguientes funcionalidades:

- [Replicación geográfica activa](sql-database-geo-replication-overview.md)
- [Restauración geográfica](sql-database-geo-restore.md)

Se recomienda validar periódicamente el flujo de trabajo de preparación de la aplicación para la recuperación. Comprobar el comportamiento de la aplicación y las implicaciones de las pérdidas de datos o de las interrupciones que conlleva la conmutación por error es una buena práctica de ingeniería. También es un requisito de la mayoría de estándares del sector como parte de la certificación de continuidad del negocio.

Obtener los detalles de una recuperación ante desastres implica lo siguiente:

- Simular la interrupción del nivel de datos.
- Realizar la recuperación. 
- Validar la integridad de la aplicación tras la recuperación.

## Administración de la seguridad después de la recuperación ante desastres

En el artículo [Administración de la seguridad después de la recuperación ante desastres](sql-database-geo-replication-security-config.md) se describen los requisitos de autenticación para configurar y controlar la [replicación geográfica activa](sql-database-geo-replication-overview.md) y los pasos necesarios para configurar el acceso de usuario a la base de datos secundaria. También se explica cómo habilitar el acceso a la base de datos recuperada después de utilizar la funcionalidad de [georrestauración](sql-database-geo-restore.md).

## Administración de actualizaciones graduales de aplicaciones en la nube mediante la replicación geográfica activa

En el artículo [Administración de actualizaciones graduales de aplicaciones en la nube mediante la replicación geográfica activa de Base de datos SQL](sql-database-manage-application-rolling-upgrade.md) se describe cómo usar la funcionalidad de [georreplicación](sql-database-geo-replication-overview.md) de Base de datos SQL para habilitar las actualizaciones graduales de su aplicación en la nube. Como la actualización es una operación problemática, pues debe ser parte del diseño y planeamiento de continuidad. En este artículo veremos dos métodos diferentes de organizar el proceso de actualización y comentaremos las ventajas y desventajas de cada opción.

## Diseño de una aplicación para la recuperación ante desastres en la nube mediante la funcionalidad de replicación geográfica activa

En el artículo [Diseño de una aplicación para la recuperación ante desastres en la nube mediante replicación geográfica activa en Base de datos SQL](sql-database-designing-cloud-solutions-for-disaster-recovery.md) se describe cómo usar la funcionalidad de [replicación geográfica activa](sql-database-geo-replication-overview.md) de Base de datos SQL para diseñar aplicaciones de base de datos diseño resistentes a errores regionales e interrupciones graves. En este artículo se tiene en cuenta la topología de implementación de aplicaciones, el Acuerdo de Nivel de Servicio objetivo, la latencia de tráfico y los costos; después, se analizan los patrones comunes de aplicaciones (cada uno con sus ventajas y desventajas).

## Estrategias de recuperación ante desastres para aplicaciones que usan grupos de bases de datos elásticas

En el artículo [Estrategias de recuperación ante desastres para aplicaciones que usan el grupo elástico de Base de datos SQL](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md) se describen los escenarios de recuperación ante desastres mediante [grupos de bases de datos elásticas](sql-database-elastic-pool.md).

## Pasos siguientes

- Para obtener información sobre cómo usar y configurar la funcionalidad de replicación geográfica activa para realizar el proceso recuperación ante desastres, consulte [Replicación geográfica activa](sql-database-geo-replication-overview.md).
- Para obtener información sobre cómo utilizar la funcionalidad de replicación geográfica activa para realizar el proceso recuperación ante desastres, consulte [Restauración geográfica](sql-database-geo-restore.md).

## Recursos adicionales

- [Información general: continuidad del negocio en la nube y recuperación ante desastres con la Base de datos SQL](sql-database-business-continuity.md)
- [Overview: SQL Database Point-in-Time Restore (Información general: Restauración a un momento dado de Base de datos SQL)](sql-database-point-in-time-restore.md)
- [Restauración geográfica](sql-database-geo-restore.md)
- [Replicación geográfica activa](sql-database-geo-replication-overview.md)
- [Diseño de aplicaciones para la recuperación ante desastres en la nube](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
- [Finalización de una base de datos SQL de Azure recuperada](sql-database-recovered-finalize.md)
- [Configuración de seguridad para Replicación geográfica activa o estándar](sql-database-geo-replication-security-config.md)
- [P+F de BCDR de Base de datos SQL](sql-database-bcdr-faq.md)

<!---HONumber=AcomDC_0622_2016-->