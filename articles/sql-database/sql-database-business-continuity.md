---
title: "Continuidad del negocio en la nube: recuperación de la base de datos (SQL Database) | Microsoft Docs"
description: "Obtenga información acerca de cómo Azure SQL Database permite la continuidad del negocio en la nube y la recuperación de la base de datos, y ayuda a que las aplicaciones críticas de la nube se sigan ejecutando."
keywords: "continuidad del negocio, continuidad del negocio en la nube, recuperación de desastres de la base de datos, recuperación de la base de datos"
services: sql-database
documentationcenter: 
author: anosov1960
manager: jhubbard
editor: 
ms.assetid: 18e5d3f1-bfe5-4089-b6fd-76988ab29822
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: On Demand
ms.date: 08/25/2017
ms.author: sashan
ms.openlocfilehash: c2c5f18b736c83c281d56e0bdda977a9f8c48101
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/14/2017
---
# <a name="overview-of-business-continuity-with-azure-sql-database"></a>Introducción a la continuidad empresarial con Azure SQL Database

En este artículo de introducción se describen las funcionalidades de continuidad empresarial y recuperación ante desastres que proporciona Azure SQL Database. Conozca más información sobre opciones, recomendaciones y tutoriales para recuperarse de eventos de interrupción que podrían provocar la pérdida de información o que su base de datos y aplicación dejen de estar disponibles. Sepa qué hacer en caso de que un error del usuario o la aplicación afecte a la integridad de los datos, se produzca una interrupción en una región de Azure o su aplicación requiera mantenimiento.

## <a name="sql-database-features-that-you-can-use-to-provide-business-continuity"></a>Características de SQL Database que puede utilizar para proporcionar continuidad empresarial

SQL Database ofrece diversas funcionalidades de continuidad empresarial, incluidas las copias de seguridad automatizadas y la replicación de base de datos opcional. Cada una de ellas posee distintas características que abarcan los conceptos de tiempo de recuperación calculado (ERT) y pérdida de datos potencial de transacciones recientes. Cuando entienda estas opciones, puede elegir las que más relevantes considere y, en la mayoría de los escenarios, utilizarlas juntas con distintos objetivos. A medida que desarrolle el plan de continuidad empresarial, tendrá que saber el tiempo máximo aceptable para que la aplicación se recupere por completo tras un evento de interrupción. A esto se le denomina "objetivo de tiempo de recuperación" (RTO). También debe conocer la cantidad máxima de actualizaciones de datos recientes (intervalo de tiempo) que la aplicación puede tolerar perder al recuperarse después de un evento de interrupción. A esto se le conoce como "objetivo de punto de recuperación" (RPO).

La tabla siguiente compara los valores de ERT y RPO para los tres escenarios más comunes.

| Capacidad | Nivel Basic | Nivel Standard | Nivel Premium |
| --- | --- | --- | --- |
| Restauración a un momento dado a partir de una copia de seguridad |Cualquier punto de restauración en 7 días |Cualquier punto de restauración en 35 días |Cualquier punto de restauración en 35 días |
| Restauración geográfica de las copias de seguridad con replicación geográfica |ERT < 12h, RPO < 1 h |ERT < 12h, RPO < 1 h |ERT < 12h, RPO < 1 h |
| Restauración de datos del Almacén de Azure Backup |ERT < 12 h, RPO < 1 h |ERT < 12 h, RPO < 1 h |ERT < 12 h, RPO < 1 h |
| Replicación geográfica activa |ERT < 30 s, RPO < 5 s |ERT < 30 s, RPO < 5 s |ERT < 30 s, RPO < 5 s |

### <a name="use-database-backups-to-recover-a-database"></a>Uso de copias de seguridad para recuperar bases de datos

SQL Database realiza automáticamente una combinación de copias de seguridad completas semanales, copias de seguridad diferenciales cada hora y copias de seguridad del registro de transacciones cada 5 o 10 minutos con el fin de proteger su empresa contra la pérdida de datos. Estas copias de seguridad se guardan en un almacenamiento con redundancia geográfica durante 35 días en el caso de las bases de datos de los niveles de servicio Estándar y Premium, y durante siete días en el nivel Básico. Para más información, consulte el artículo sobre [niveles de servicio](sql-database-service-tiers.md). Si el período de retención del nivel de servicio no se ajusta a los requisitos de su empresa, puede ampliarlo [cambiando dicho nivel de servicio](sql-database-service-tiers.md). Las copias de seguridad completas y diferenciales de bases de datos también se replican en un [centro de datos asociado](../best-practices-availability-paired-regions.md) con el fin de brindar protección frente a interrupciones en el centro de datos. Para más información, consulte [copias de seguridad automáticas de bases de datos](sql-database-automated-backups.md).

Si el período de retención integrado no es suficiente para la aplicación, puede ampliarlo mediante la configuración de la directiva de retención a largo plazo para las bases de datos. Para más información, consulte [retención a largo plazo](sql-database-long-term-retention.md).

Puede utilizar este tipo de copia de seguridad para recuperar una base de datos después de que se produzcan diferentes eventos de interrupción tanto en su centro de datos como en otro. Con las copias de seguridad automáticas de bases de datos, el tiempo estimado de recuperación depende de varios factores, como el número total de bases de datos que se están recuperando a la vez en la misma región, el tamaño de estas, el tamaño del registro de transacciones y el ancho de banda de red. Normalmente, el tiempo de recuperación es inferior a 12 horas. Cuando se lleva a cabo un proceso de recuperación en otra región de datos, la posible pérdida de datos solo es de 1 hora gracias al almacenamiento con redundancia geográfica de las copias de seguridad diferenciales de bases de datos que se realizan cada hora.

> [!IMPORTANT]
> Para poder efectuar una recuperación con copias de seguridad automatizadas, debe ser miembro del rol de colaborador de SQL Server o propietario de la suscripción. Consulte el artículo [RBAC: Roles integrados](../active-directory/role-based-access-built-in-roles.md). Las recuperaciones se pueden realizar a través del Portal de Azure, PowerShell o la API de REST. No puede utilizar Transact-SQL.
>

Utilice las copias de seguridad automatizadas como mecanismo de recuperación y de continuidad empresarial si se cumplen los siguientes requisitos en su aplicación:

* No se considera crítica.
* No tiene un Acuerdo de Nivel de Servicio vinculante, por lo que no incurrirá en responsabilidades financieras en caso de tiempos de inactividad de 24 horas o más.
* Tiene una tasa de cambio de datos reducida (bajo número de transacciones por hora) y se tolera perder hasta una hora de datos.
* Los costos son un factor fundamental.

Si necesita recuperaciones más rápidas, utilice la [replicación geográfica activa](sql-database-geo-replication-overview.md) (se describe a continuación). Si necesita recuperar datos de un período anterior a 35 días, use la [retención de copia de seguridad a largo plazo](sql-database-long-term-retention.md). 

### <a name="use-active-geo-replication-and-auto-failover-groups-in-preview-to-reduce-recovery-time-and-limit-data-loss-associated-with-a-recovery"></a>Uso de grupos de conmutación automática por error (versión preliminar) y replicación geográfica activa para reducir el tiempo de recuperación y limitar la pérdida de datos asociada a una recuperación

Además de utilizar las copias de seguridad para recuperar bases de datos en caso de interrupciones en el negocio, puede usar la [replicación geográfica activa](sql-database-geo-replication-overview.md) para hacer que una base de datos tenga un máximo de cuatro bases de datos secundarias legibles en las regiones que prefiera. Estas bases de datos secundarias se mantienen sincronizadas con la principal a través de un mecanismo de replicación asincrónica. Esta característica se utiliza para proteger su negocio de interrupciones en el centro de datos o durante una actualización de la aplicación. La replicación geográfica activa también puede emplearse para que los usuarios repartidos por distintas ubicaciones del mundo puedan realizar consultas de solo lectura de manera más eficaz.

Para habilitar la conmutación por error automática y transparente debe organizar las bases de datos de replicación geográfica en grupos mediante la característica [de grupos de conmutación automática por error](sql-database-geo-replication-overview.md) de SQL Database (versión preliminar).

Si la base de datos principal se desconecta de forma inesperada o debido a actividades de mantenimiento, puede convertir rápidamente una base de datos secundaria en principal (a este proceso también se le denomina "conmutación por error") y configurar que las aplicaciones se conecten a esta principal. Si la aplicación se conecta a las bases de datos mediante el agente de escucha del grupo de conmutación por error, no es necesario cambiar la configuración de la cadena de conexión de SQL después de la conmutación por error. Con las conmutaciones por error planeadas no se pierden datos. Sin embargo, con las no planeadas, se puede perder una pequeña cantidad de información en las transacciones muy recientes debido a la naturaleza de la replicación asincrónica. Con el uso de grupos de conmutación automática por error (versión preliminar), puede personalizar la directiva de conmutación por error para minimizar la posible pérdida de datos. Tras una conmutación por error, puede realizar una conmutación por recuperación según un plan o cuando el centro de datos vuelva a estar en línea. En todos los casos, los usuarios experimentan un tiempo de inactividad reducido y tienen que volver a conectarse.

> [!IMPORTANT]
> Para usar grupos de conmutación automática por error (versión preliminar) y la replicación geográfica activa, debe ser el propietario de la suscripción o tener permisos administrativos en SQL Server. Puede realizar tareas de configuración y conmutación por error mediante Azure Portal, PowerShell o la API de REST con los permisos de la suscripción de Azure, o bien mediante Transact-SQL con los permisos de SQL Server.
> 

Use la replicación geográfica activa y los grupos de conmutación automática por error (versión preliminar) si su aplicación cumple alguno de estos criterios:

* Es crítica.
* Tiene un SLA que no se permite que se produzcan tiempos de inactividad de más de 24 horas.
* El tiempo de inactividad incurrirá en responsabilidades financieras.
* Tiene una tasa de cambio de datos elevada y no se acepta perder una hora de datos.
* El costo adicional por utilizar la replicación geográfica activa es menor que el de la posible responsabilidad financiera y la pérdida de negocio asociada que habría que asumir.

>
> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-protecting-important-DBs-from-regional-disasters-is-easy/player]
>

## <a name="recover-a-database-after-a-user-or-application-error"></a>Recuperación de bases de datos tras un error del usuario o la aplicación
Nadie es perfecto. Un usuario podría eliminar de forma involuntaria algunos datos, una tabla importante o, incluso, una base de datos entera. O bien, una aplicación podría sobrescribir accidentalmente datos correctos por otros no válidos debido a un defecto en esta.

Estas son las opciones de recuperación que tiene para este caso.

### <a name="perform-a-point-in-time-restore"></a>Realización de una restauración a un momento dado
Puede utilizar las copias de seguridad automatizadas para restaurar la base de datos a un momento dado conocido y sin problemas, siempre que sea dentro del periodo de retención de la base de datos. Después de restaurar la base de datos, puede reemplazar la original por la restaurada o copiar la información que necesite de los datos restaurados en la base de datos original. Si la base de datos utiliza la replicación geográfica activa, se recomienda copiar los datos que precise de la copia restaurada en la original. Si reemplaza la base de datos original por la restaurada, debe volver a configurar y sincronizar la replicación geográfica activa (proceso que puede llevar bastante tiempo en bases de datos de gran tamaño). Aunque esto permite restaurar una base de datos hasta el último punto disponible en el tiempo, la restauración de una base de datos geográfica secundaria a cualquier punto en el tiempo no se admite actualmente.

Para más información y ver los pasos detallados de cómo restaurar una base de datos a un momento dado mediante el Portal de Azure o PowerShell, consulte el artículo sobre [restauración a un momento dado](sql-database-recovery-using-backups.md#point-in-time-restore). Transact-SQL no se puede utilizar para este fin.

### <a name="restore-a-deleted-database"></a>Restauración de una base de datos eliminada
Si se elimina la base de datos, pero no el servidor lógico, puede restaurar la base de datos eliminada al momento en que se suprimió. De este modo, se restaura una copia de seguridad de la base de datos en el mismo servidor SQL lógico desde el que se eliminó. Puede restaurarla usando el nombre original o proporcionando un nuevo nombre a la base de datos restaurada.

Para más información y ver los pasos detallados de cómo restaurar una base de datos eliminada mediante el Portal de Azure o PowerShell, consulte el artículo sobre [restauración de bases de datos eliminadas](sql-database-recovery-using-backups.md#deleted-database-restore). Transact-SQL no se puede utilizar para este fin.

> [!IMPORTANT]
> Si se elimina el servidor lógico, no se podrá recuperar una base de datos eliminada.
>
>

### <a name="restore-from-azure-backup-vault"></a>Restauración de datos del Almacén de Azure Backup
Si se ha producido la pérdida de datos fuera del período de retención actual para copias de seguridad automatizadas y la base de datos está configurada para una retención a largo plazo, puede restaurar a partir de una copia de seguridad semanal del almacén de Azure Backup a una nueva base de datos. Llegados a este punto, puede reemplazar la base de datos original por la restaurada o copiar la información que necesite de los datos restaurados en la base de datos original. Si tiene que recuperar una versión anterior de la base de datos antes de una actualización de la aplicación principal, satisfacer una solicitud de auditores o una orden judicial, puede crear una base de datos mediante una copia de seguridad completa guardada en el almacén de Azure Backup.  Para más información, consulte [retención a largo plazo](sql-database-long-term-retention.md).

## <a name="recover-a-database-to-another-region-from-an-azure-regional-data-center-outage"></a>Recuperación de una base de datos en otra región tras una interrupción en el centro de datos regional de Azure
<!-- Explain this scenario -->

Aunque es poco habitual, en los centros de datos de Azure pueden producirse interrupciones. Cuando esto ocurre, provoca también una interrupción en el negocio que podría extenderse solo unos pocos minutos o, incluso, horas.

* Una opción consiste en esperar a que la base de datos vuelva a estar en línea cuando termine la interrupción del centro de datos. Esto puede hacerse con las aplicaciones que pueden permitirse que la base de datos esté desconectada. Por ejemplo, los proyectos de desarrollo o las pruebas gratuitas no tienen que estar en funcionamiento constantemente. Cuando se produce una interrupción en un centro de datos, no sabe cuánto durará, por lo que esta opción solo es útil si no necesita utilizar la base de datos durante un tiempo.
* Otra opción consiste en realizar una conmutación por error a otra región de datos si utiliza la replicación geográfica activa o la recuperación mediante copias de seguridad de bases de datos con redundancia geográfica (restauración geográfica). La conmutación por error solo lleva unos segundos, mientras que la recuperación de una base de datos a partir de copias de seguridad dura horas.

Cuando pase a la acción, el tiempo que se tarde en recuperar las bases de datos y la cantidad de información que se pierde en el caso de una interrupción en el centro de datos dependerá de cómo decida usar las características de continuidad empresarial de la aplicación. De hecho, puede decidir usar una combinación de copias de seguridad de bases de datos y la replicación geográfica activa según los requisitos de la aplicación. Para ver una explicación de las consideraciones de diseño de las aplicaciones para bases de datos independientes y grupos elásticos con estas características de continuidad empresarial, consulte [Diseño de una aplicación para la recuperación ante desastres](sql-database-designing-cloud-solutions-for-disaster-recovery.md) y [Estrategias de recuperación ante desastres para aplicaciones que usan el grupo elástico](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).

En las siguientes secciones se ofrece información general de los pasos para realizar tareas de recuperación mediante copias de seguridad de bases de datos o la replicación geográfica activa. Para ver los pasos detallados, como el planeamiento de los requisitos, los pasos posteriores a la recuperación y los detalles sobre cómo simular una interrupción para llevar a cabo tareas de recuperación ante desastres, consulte el artículo [Recuperación de una instancia de SQL Database después de una interrupción](sql-database-disaster-recovery.md).

### <a name="prepare-for-an-outage"></a>Preparativos para interrupciones
Con independencia de la característica de continuidad empresarial que use, debe hacer lo siguiente:

* Identificar y preparar el servidor de destino, incluidas las reglas de firewall de nivel de servidor, los inicios de sesión y los permisos de nivel de base de datos maestra.
* Determinar cómo se redirigirán los clientes y las aplicaciones cliente al nuevo servidor
* Documentar otras dependencias, como las alertas y la configuración de auditoría

Si no se prepara correctamente, el proceso de conectar las aplicaciones después de una conmutación por error o una recuperación de base de datos llevará más tiempo y, probablemente, también haya que solucionar problemas en momentos de estrés, por lo que no es nada recomendable.

### <a name="fail-over-to-a-geo-replicated-secondary-database"></a>Conmutación por error de la base de datos secundaria de replicación geográfica
Si usa grupos de conmutación automática por error (versión preliminar) y la replicación geográfica activa como el mecanismo de recuperación, puede configurar una directiva de conmutación automática por error o usar la [conmutación por error manual](sql-database-disaster-recovery.md#fail-over-to-geo-replicated-secondary-server-in-the-failover-group). Tras iniciar la conmutación por error, la base de datos secundaria pasa a ser la principal y está lista para registrar nuevas transacciones y responder a consultas, con una pérdida mínima de los datos que aún no se han replicado. Para obtener información sobre el diseño del proceso de conmutación por error, vea [Diseño de aplicaciones para la recuperación ante desastres en la nube](sql-database-designing-cloud-solutions-for-disaster-recovery.md).

> [!NOTE]
> Cuando el centro de datos vuelve a estar en línea, las bases de datos principales anteriores se vuelven a conectar automáticamente con la nueva base de datos principal y se convierten en bases de datos secundarias. Si necesita reubicar la base de datos principal de nuevo en la región original, puede iniciar una conmutación por error manual planificada (conmutación por recuperación). 
> 

### <a name="perform-a-geo-restore"></a>Realización de restauraciones geográficas
Si utiliza copias de seguridad automatizadas con replicación de almacenamiento con redundancia geográfica como mecanismo de recuperación, [inicie una recuperación de base de datos mediante la restauración geográfica](sql-database-disaster-recovery.md#recover-using-geo-restore). El proceso de recuperación suele durar 12 horas y hay una pérdida de datos de hasta una hora en función de cuándo se realizó y replicó la última copia de seguridad diferencial que se ejecuta cada hora. Hasta que no se complete la recuperación, la base de datos no puede registrar transacciones ni responder a las consultas. Aunque esto permite restaurar una base de datos hasta el último punto disponible en el tiempo, la restauración de una base de datos geográfica secundaria a cualquier punto en el tiempo no se admite actualmente.

> [!NOTE]
> Si el centro de datos vuelve a estar en línea antes de cambiar la aplicación en la base de datos recuperada, puede cancelar el proceso de recuperación.  
>
>

### <a name="perform-post-failover--recovery-tasks"></a>Realización de tareas posteriores a la recuperación y conmutación por error
Cuando efectúe la recuperación con cualquiera de los mecanismos para llevarla a cabo, debe realizar las siguientes tareas adicionales antes de que los usuarios y las aplicaciones vuelvan a conectarse:

* Redirija los clientes y las aplicaciones cliente al nuevo servidor y a la base de datos restaurada.
* Asegúrese de aplicar reglas de firewall de nivel de servidor adecuadas para que se conecten los usuarios (o use [firewalls de nivel de base de datos](sql-database-firewall-configure.md#creating-and-managing-firewall-rules)).
* No se olvide de emplear permisos de nivel de base de datos maestra e inicios de sesión apropiados (o use [usuarios contenidos](https://msdn.microsoft.com/library/ff929188.aspx)).
* Configure la auditoría según corresponda.
* Configure las alertas según corresponda.

## <a name="upgrade-an-application-with-minimal-downtime"></a>Actualice una aplicación con el mínimo tiempo de inactividad posible.
A veces, debe desconectar una aplicación debido a un mantenimiento planeado, por ejemplo, para actualizarla. En el artículo [Administración de actualizaciones graduales de aplicaciones](sql-database-manage-application-rolling-upgrade.md) se explica cómo utilizar la replicación geográfica activa para poder actualizar gradualmente su aplicación en la nube con el objetivo de minimizar el tiempo de inactividad durante este proceso. Además, se describe una ruta de recuperación para cuando se empiece a detectar errores. 

## <a name="next-steps"></a>pasos siguientes
Para ver una explicación de las consideraciones de diseño de las aplicaciones para bases de datos independientes y grupos elásticos, consulte [Diseño de una aplicación para la recuperación ante desastres en la nube](sql-database-designing-cloud-solutions-for-disaster-recovery.md) y [Estrategias de recuperación ante desastres para aplicaciones que usan el grupo elástico](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).
