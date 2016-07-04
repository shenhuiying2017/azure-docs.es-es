<properties
   pageTitle="Continuidad del negocio en la nube: recuperación de la base de datos (Base de datos SQL) | Microsoft Azure"
   description="Obtenga información acerca de cómo la Base de datos SQL de Azure permite la continuidad del negocio en la nube y la recuperación de la base de datos, y ayuda a que las aplicaciones críticas de la nube se sigan ejecutando."
   keywords="continuidad del negocio, continuidad del negocio en la nube, recuperación de desastres de la base de datos, recuperación de la base de datos"
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
   ms.workload="sqldb-bcdr"
   ms.date="06/09/2016"
   ms.author="carlrab"/>

# Información general: continuidad del negocio en la nube y recuperación ante desastres con la Base de datos SQL de Azure

> [AZURE.SELECTOR]
- [Restauración a un momento dado](sql-database-point-in-time-restore.md)
- [Restaurar la base de datos eliminada](sql-database-restore-deleted-database.md)
- [Restauración geográfica](sql-database-geo-restore.md)
- [Replicación geográfica activa](sql-database-geo-replication-overview.md)
- [Escenarios de continuidad del negocio](sql-database-business-continuity-scenarios.md)

La Base de datos SQL de Azure proporciona una serie de soluciones de continuidad del negocio. La continuidad del negocio implica el diseño, la implementación y la ejecución de aplicaciones de manera que sean resistentes a los eventos de interrupción, planeados o no, que provocan la pérdida permanente o temporal de la capacidad de estas para realizar su función empresarial. Entre los eventos no planeados se incluyen desde los errores humanos hasta las interrupciones permanentes o temporales, pasando por los desastres regionales que pueden provocar la pérdida a gran escala de la instalación en una determinada región de Azure. Los eventos planeados incluyen la reimplementación de la aplicación en una región diferente y las actualizaciones de aplicaciones. El objetivo de la continuidad del negocio es que su aplicación siga funcionando durante estos eventos con un impacto mínimo en la función empresarial.

Para tratar las soluciones de continuidad del negocio en la nube de Base de datos SQL, existen varios conceptos con los que debe estar familiarizado. Dichos componentes son:

* **Recuperación ante desastres (DR):** proceso que permite restaurar la función empresarial normal de la aplicación.

* **Tiempo calculado de recuperación estimado (ERT):** tiempo estimado necesario para que la base de datos vuelva a estar completamente disponible tras una solicitud de restauración o de conmutación por error.

* **Objetivo de tiempo de recuperación (RTO)**: tiempo máximo aceptable para la recuperación total de la aplicación después del evento de interrupción. El RTO mide la pérdida máxima de disponibilidad durante los errores.

* **Objetivo de punto de recuperación (RPO)**: cantidad máxima de actualizaciones recientes (intervalo de tiempo) que la aplicación puede perder hasta que se recupera completamente tras el evento de interrupción. El RPO mide la pérdida máxima de datos durante los errores.


## Escenarios de continuidad del negocio en la nube de Base de datos SQL

A continuación, se abordan los escenarios clave que hay que tener en cuenta al planear la continuidad empresarial y la recuperación de la base de datos:

### Diseño de aplicaciones para la continuidad del negocio

La aplicación que estoy creando es fundamental para mi negocio Por ello deseo diseñarla y configurarla para que sea capaz de sobrevivir a un desastre regional provocado por un error catastrófico del servicio. Conozco cuáles son los requisitos de RPO y RTO de mi aplicación y elegiré la configuración que se ajuste a dichos requisitos.

### Recuperación de errores humanos

Tengo derechos administrativos para tener acceso a la versión de producción de la aplicación. Como parte del proceso de mantenimiento periódico, he cometido un error y he eliminado algunos datos críticos en producción. Por ello, deseo restaurar rápidamente los datos con el fin de mitigar el impacto del error.

### Recuperación tras una interrupción

Estoy ejecutando la aplicación en producción y recibo una alerta que indica que hay una interrupción importante en la región en la que se ha implementado la aplicación. Deseo iniciar el proceso de recuperación para ponerla en una región distinta para mitigar el impacto en el negocio.

### Exploración de la recuperación ante desastres

Puesto que con la recuperación de la interrupción, se reubicará el nivel de datos de la aplicación en una región distinta, necesito probar de manera periódica el proceso de recuperación y evaluar su impacto en la aplicación para estar preparado.

### Actualización de la aplicación sin tiempo de inactividad

Voy a publicar una actualización importante de mi aplicación. Dicha actualización implica cambios en el esquema de la base de datos, implementación de procedimientos almacenados adicionales, etc. Este proceso requiere detener el acceso de los usuarios a la base de datos. Al mismo tiempo, deseo asegurarme de que la actualización no provoque una interrupción importante en las operaciones de negocio.

## Características de continuidad del negocio de Base de datos SQL

En la siguiente tabla se enumeran las características de continuidad empresarial de Base de datos SQL y se muestran sus diferencias entre los distintos [niveles de servicio](sql-database-service-tiers.md):

| Capacidad | Nivel Basic | Nivel Standard |Nivel Premium
| --- | --- | --- | ---
| Restauración a un momento dado | Cualquier punto de restauración en 7 días | Cualquier punto de restauración en 14 días | Cualquier punto de restauración en 35 días
| Restauración geográfica | ERT < 12h, RPO < 1 h | ERT < 12h, RPO < 1 h | ERT < 12h, RPO < 1 h
| Replicación geográfica activa | ERT < 30 s, RPO < 5 s | ERT < 30 s, RPO < 5 s | ERT < 30 s, RPO < 5 s

Estas características se proporcionan para abordar los escenarios mencionados anteriormente. Vea la sección [Diseño para la continuidad del negocio](sql-database-business-continuity-design.md) sección para obtener instrucciones sobre cómo seleccionar características específicas.

> [AZURE.NOTE] Los valores ERT y RPO son objetivos de ingeniería que únicamente proporcionan orientación. No forman parte del [SLA de Base de datos SQL](https://azure.microsoft.com/support/legal/sla/sql-database/v1_0/)


###Restauración a un momento dado

La [restauración a un momento dado](sql-database-point-in-time-restore.md) está diseñada para restablecer la base de datos a un punto anterior en el tiempo. Esta característica usa las copias de seguridad de la base de datos, las copias de seguridad incrementales y las copias de seguridad de registros de transacciones que el servicio mantiene automáticamente para cada base de datos de usuario. Esta capacidad está disponible para todos los niveles de servicio. Puede recuperar la base de datos de hace 7 días, con el nivel Basic, 14 días, con el nivel Standard, y 35 días, con el nivel Premium.

### Restauración geográfica

La [restauración geográfica](sql-database-geo-restore.md) también está disponible en las bases de datos básicas, estándares y premium. Esta restauración proporciona la opción de recuperación predeterminada cuando la base de datos tampoco está disponible debido a una incidencia en la región en la que se hospeda la base de datos. Al igual que la restauración a un momento dado, la restauración geográfica se basa en copias de seguridad de la base de datos en el almacenamiento de Azure con redundancia geográfica. Esta opción permite restaurar la base de datos desde la copia de seguridad de replicación geográfica. Por lo tanto es resistente a las interrupciones de almacenamiento que tienen lugar en la región principal.

### Replicación geográfica activa

La funcionalidad de [replicación geográfica activa](sql-database-geo-replication-overview.md) está disponible para todos los niveles de base de datos. Está diseñada para aplicaciones que tienen unos requisitos de recuperación más exigentes que los que puede ofrecer la restauración geográfica. Con la replicación geográfica activa, puede crear hasta cuatro bases de datos secundarias legibles en servidores situados en regiones diferentes. Puede iniciar la conmutación por error a cualquiera de las bases de datos secundarias. Además, la replicación geográfica activa puede utilizarse para los [escenarios de actualización o reubicación de la aplicación](sql-database-manage-application-rolling-upgrade.md), así como para el equilibrio de cargas de trabajo de solo lectura.

## Selección de características de continuidad empresarial

El diseño de su aplicación para la continuidad del negocio requiere que responda a las preguntas siguientes:

1. ¿Qué característica de continuidad del negocio es la más adecuada para proteger mi aplicación frente a las interrupciones?
2. ¿Qué nivel de topología de replicación y redundancia debo usar?

Para obtener más información sobre las estrategias de recuperación cuando se utiliza un grupo elástico, consulte [Estrategias de recuperación ante desastres para aplicaciones que usan el grupo elástico de Base de datos SQL](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).

### Cuándo se debe usar la restauración geográfica

La funcionalidad [restauración geográfica](sql-database-geo-restore.md) proporciona la opción de recuperación predeterminada cuando una base de datos no está disponible debido a una incidencia en la región en la que se hospeda. Base de datos SQL ofrece de forma predeterminada la protección integrada básica de todas las bases de datos. Dicha protección se obtiene al realizar y almacenar [copias de seguridad de la base de datos](sql-database-automated-backups.md) en el Almacenamiento de Azure con redundancia geográfica (GRS). Si elige este método, no es necesaria ninguna configuración especial o asignación de recursos adicionales. Puede recuperar la base de datos en cualquier región restaurando estas copias de seguridad automatizadas con redundancia geográfica a una nueva base de datos.

Deberá usar la protección integrada si su aplicación cumple los criterios siguientes:

1. No se considera crítica. No tiene un SLA vinculante, por lo que no incurrirá en responsabilidades financieras en caso de tiempo de inactividad de 24 horas o más.
2. La tasa de cambio de los datos es baja (por ejemplo de transacciones por cada hora). El RPO de 1 hora no dará como resultado pérdidas de datos masivas.
3. La aplicación es sensible a los costes, por lo que el coste adicional de la replicación geográfica no está justificado. 

> [AZURE.NOTE] La restauración geográfica no asigna previamente la capacidad informática a ninguna región en particular para restaurar bases de datos activas de la copia de seguridad durante la interrupción. El servicio administrará la carga de carga de trabajo asociada a las solicitudes de restauración geográfica de forma que se minimice el impacto en las bases de datos existentes en dicha región, por lo que sus demandas de capacidad tendrán prioridad. Por lo tanto, el tiempo de recuperación de la base de datos dependerá del número de bases de datos que se recuperarán en la misma región de manera simultánea, además del tamaño de la base de datos, el número de transacciones por registro, al ancho de banda de red, etc.

### Cuándo utilizar la replicación geográfica activa

La funcionalidad de [replicación geográfica activa](sql-database-geo-replication-overview.md) permite crear y mantener bases de datos legibles (secundarias) en una región distinta de la primaria, con lo que se seguirá usando un mecanismo de replicación asíncrona. Esta replicación garantiza que la base de datos tendrá los datos y los recursos necesarios para admitir la carga de trabajo de la aplicación después del proceso de recuperación.

Deberá usar la funcionalidad de replicación geográfica activa si su aplicación cumple los criterios siguientes:

1. Es crítica. Tiene un SLA vinculante con unos valores de RTO y RPO agresivos. La pérdida de datos y de disponibilidad incurriría en responsabilidades financieras. 
2. La tasa de cambio de los datos es alta (por ejemplo de transacciones por cada minuto o segundo). El RPO de 1 hora asociado a la protección predeterminada probablemente tendrá como resultado una pérdida de datos inaceptable.
3. El coste asociado al uso de la replicación geográfica es significativamente menor que el de la responsabilidad financiera potencial y la pérdida de negocio.


## Pasos siguientes

- Para obtener información sobre las copias de seguridad automatizadas de Base de datos SQL de Azure, consulte [Información general: copias de seguridad automatizadas de Base de datos SQL](sql-database-automated-backups.md).
- Para aprender a utilizar las copias de seguridad automatizadas de Base de datos SQL de Azure para realizar una restauración de una base de datos a un momento dado, consulte [Restauración a un momento dado](sql-database-point-in-time-restore.md).
- Para saber cómo utilizar las copias de seguridad automatizadas de Base de datos SQL de Azure para restaurar una base de datos eliminada, consulte [Restaurar la base de datos eliminada](sql-database-restore-deleted-database.md).
- Para aprender a utilizar las copias de seguridad automatizadas de Base de datos SQL de Azure para realizar una restauración geográfica de una base de datos a un momento dado, consulte [Restauración geográfica](sql-database-geo-restore.md).
- Para obtener información sobre cómo configurar y usar la funcionalidad de replicación geográfica activa para la continuidad empresarial, consulte [Replicación geográfica activa](sql-database-geo-replication-overview.md).

## Recursos adicionales

- [Recuperación tras una interrupción](sql-database-disaster-recovery.md)
- [Recuperación ante un error de usuario](sql-database-user-error-recovery.md)
- [Realización de maniobras de recuperación ante desastres](sql-database-disaster-recovery-drills.md)
- [Administración de la seguridad después de la recuperación ante desastres](sql-database-geo-replication-security-config.md)
- [Administración de actualizaciones graduales de aplicaciones en la nube mediante la replicación geográfica activa de Base de datos SQL](sql-database-manage-application-rolling-upgrade.md)
- [Diseño de soluciones en la nube de recuperación ante desastres](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
- [Estrategias de recuperación ante desastres para aplicaciones que usan el grupo elástico de Base de datos SQL](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)
- [Diseño de una aplicación para la recuperación ante desastres en la nube mediante replicación geográfica activa en Base de datos SQL](sql-database-designing-cloud-solutions-for-disaster-recovery.md)

<!---HONumber=AcomDC_0622_2016-->