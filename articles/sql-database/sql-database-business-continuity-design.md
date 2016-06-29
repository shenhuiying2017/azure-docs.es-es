<properties 
   pageTitle="Diseño de la base de datos SQL para la continuidad del negocio" 
   description="Directrices para realizar su elección En esta sección se proporcionan directrices que le ayudarán a elegir las características BCDR que deben usarse, así como el momento en que deben usarse. Esto incluye descripciones de lo que obtendrá automáticamente con el uso de la base de datos SQL."
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
   ms.workload="data-management" 
   ms.date="05/27/2016"
   ms.author="carlrab"/>

# Diseño para la continuidad del negocio

El diseño de su aplicación para la continuidad del negocio requiere que responda a las preguntas siguientes:

1. ¿Qué característica de continuidad del negocio es la más adecuada para proteger mi aplicación frente a las interrupciones?
2. ¿Qué nivel de topología de replicación y redundancia debo usar?

Para más información sobre las estrategias de recuperación cuando se utiliza un grupo elástico, consulte [Estrategias de recuperación ante desastres para aplicaciones que usan el grupo elástico de Base de datos SQL](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).

## Cuándo se debe usar la restauración geográfica

La [restauración geográfica](sql-database-geo-restore.md) proporciona la opción de recuperación predeterminada cuando una base de datos no está disponible debido a una incidencia en la región en la que se hospeda. Base de datos SQL ofrece de forma predeterminada la protección integrada básica de todas las bases de datos. Dicha protección se obtiene al realizar y almacenar [copias de seguridad de la base de datos](sql-database-automated-backups.md) en el Almacenamiento de Azure con redundancia geográfica (GRS). Si elige este método, no es necesaria ninguna configuración especial o asignación de recursos adicionales. Con estas copias de seguridad, podrá recuperar la base de datos en cualquier región mediante el comando de restauración geográfica. Vea la sección [Recuperación tras una interrupción](sql-database-disaster-recovery.md) para obtener información sobre los detalles del uso de la restauración geográfica para recuperar la aplicación.

Deberá usar la protección integrada si su aplicación cumple los criterios siguientes:

1. No se considera crítica. No tiene un SLA vinculante, por lo que no incurrirá en responsabilidades financieras en caso de tiempo de inactividad de 24 horas o más.
2. La tasa de cambio de los datos es baja (por ejemplo de transacciones por cada hora). El RPO de 1 hora no dará como resultado pérdidas de datos masivas.
3. La aplicación es sensible a los costes, por lo que el coste adicional de la replicación geográfica no está justificado. 

> [AZURE.NOTE] La restauración geográfica no asigna previamente la capacidad informática a ninguna región en particular para restaurar bases de datos activas de la copia de seguridad durante la interrupción. El servicio administrará la carga de carga de trabajo asociada a las solicitudes de restauración geográfica de forma que se minimice el impacto en las bases de datos existentes en dicha región, por lo que sus demandas de capacidad tendrán prioridad. Por lo tanto, el tiempo de recuperación de la base de datos dependerá del número de bases de datos que se recuperarán en la misma región de manera simultánea.

## Cuándo utilizar la replicación geográfica activa

La [replicación geográfica activa](sql-database-geo-replication-overview.md) permite la creación de bases de datos legibles (secundarias) en una región distinta de la principal. Esta replicación garantiza que la base de datos tendrá los datos y los recursos necesarios para admitir la carga de trabajo de la aplicación después del proceso de recuperación. Vea la sección[Recuperación tras una interrupción](sql-database-disaster-recovery.md) para obtener información sobre el uso de la conmutación por error para recuperar su aplicación.

Deberá usar la replicación geográfica si su aplicación cumple los criterios siguientes:

1. Es crítica. Tiene un SLA vinculante con unos valores de RTO y RPO agresivos. La pérdida de datos y de disponibilidad incurriría en responsabilidades financieras. 
2. La tasa de cambio de los datos es alta (por ejemplo de transacciones por cada minuto o segundo). El RPO de 1 hora asociado a la protección predeterminada probablemente tendrá como resultado una pérdida de datos inaceptable.
3. El coste asociado al uso de la replicación geográfica es significativamente menor que el de la responsabilidad financiera potencial y la pérdida de negocio.

Para habilitar la replicación geográfica activa, consulte [Configuración de replicación geográfica activa para Base de datos SQL de Azure con el Portal de Azure](sql-database-geo-replication-portal.md).

> [AZURE.NOTE] La replicación geográfica activa también admite el acceso de solo lectura a la base de datos secundaria, lo que proporciona capacidad adicional para las cargas de trabajo de solo lectura.

##Cómo elegir la configuración de conmutación por error 

Al diseñar la aplicación para la continuidad del negocio, debe tener en cuenta varias opciones de configuración. La elección dependerá de la topología de la implementación de la aplicación y de las partes de las aplicaciones que sean más vulnerables a las interrupciones. Consulte [Diseño de soluciones de nube para la recuperación ante desastres mediante la replicación geográfica](sql-database-designing-cloud-solutions-for-disaster-recovery.md), para obtener más instrucciones.

## Pasos siguientes

- [Diseño de una aplicación para la recuperación ante desastres en la nube mediante replicación geográfica activa en Base de datos SQL](sql-database-designing-cloud-solutions-for-disaster-recovery.md)

## Recursos adicionales

- [Estrategias de recuperación ante desastres para aplicaciones que usan el grupo elástico de Base de datos SQL](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md) 

<!---HONumber=AcomDC_0615_2016-->