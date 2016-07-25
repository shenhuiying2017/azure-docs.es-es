<properties 
   pageTitle="Recuperación ante desastres de Base de datos SQL | Microsoft Azure" 
   description="Obtenga información acerca de cómo recuperar una base de datos tras un error o una interrupción de un centro de datos regional con las funcionalidades de replicación geográfica activa y restauración geográfica de Base de datos SQL de Azure." 
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
   ms.date="06/16/2016"
   ms.author="carlrab"/>

# Restauración de una base de datos SQL de Azure o una conmutación por error en una secundaria

Base de datos SQL de Azure ofrece las siguientes capacidades para recuperarse de un corte en el suministro eléctrico:

- [Replicación geográfica activa](sql-database-geo-replication-overview.md)
- [Restauración geográfica](sql-database-recovery-using-backups.md#point-in-time-restore)

Para obtener información sobre cómo prepararse ante escenarios de desastres y sobre cuándo se debe recuperar la base de datos, consulte [Información general: continuidad del negocio en la nube y recuperación ante desastres con la Base de datos SQL de Azure](sql-database-business-continuity.md) y [Restauración de una base de datos SQL de Azure o una conmutación por error en una secundaria]().

## Cuándo iniciar la recuperación

La operación de recuperación repercute en la aplicación. Este proceso requiere cambiar la cadena de conexión SQL y puede provocar la pérdida de datos permanente. Por lo tanto, debe realizarse solo cuando la duración de la interrupción pueda durar más que el RTO de su aplicación. Cuando se implementa la aplicación en producción, deberá supervisar con frecuencia el estado de la aplicación. Para ello, use los siguientes puntos de datos para garantizar la recuperación:

1.	Error de conectividad permanente de la capa de aplicación en la base de datos.
2.	El Portal de Azure muestra una alerta acerca de una incidencia en una región con un gran impacto.
3.	El servidor de base de datos de SQL Azure está marcado como degradado.

En función de la tolerancia de la aplicación al tiempo de inactividad y de la posible responsabilidad civil, puede considerar las siguientes opciones de recuperación.

Use la opción [Obtener base de datos recuperable](https://msdn.microsoft.com/library/dn800985.aspx) (*LastAvailableBackupDate*) para obtener el último punto de restauración de replicación geográfica.

## Espera para la recuperación del servicio

Los equipos de Azure trabajan diligentemente para restaurar la disponibilidad del servicio lo antes posible, pero dependiendo de la causa principal pueden tardar horas, o incluso días. Si la aplicación puede tolerar un tiempo de inactividad considerable, puede esperar hasta que se complete la recuperación. En este caso, no se requieren acciones por su parte. El estado actual del servicio se puede ver en el [panel de estado de los servicios de Azure](https://azure.microsoft.com/status/). Después de la recuperación de la región se restaurará la disponibilidad de su aplicación.

## Conmutación por error de la base de datos secundaria de replicación geográfica

Si el tiempo de inactividad de la aplicación puede dar lugar a responsabilidades civiles, debería utilizar bases de datos con replicación geográfica en la aplicación. Esto permitirá que la aplicación restaure rápidamente la disponibilidad en una región diferente en caso de interrupción. Obtenga información sobre cómo [configurar la replicación geográfica](sql-database-geo-replication-portal.md).

Para restaurar la disponibilidad de las bases de datos, es preciso iniciar la conmutación por error en la secundaria con replicación geográfica mediante uno de los métodos admitidos.


Utilice una de las siguientes guías para realizar la conmutación por error en una base de datos secundaria con replicación geográfica:

- [Configuración de replicación geográfica para Base de datos SQL de Azure con el Portal de Azure](sql-database-geo-replication-portal.md)
- [Configuración de la replicación geográfica para Base de datos SQL de Azure con PowerShell](sql-database-geo-replication-powershell.md)
- [Configuración de la replicación geográfica para una base de datos SQL de Azure con Transact-SQL](sql-database-geo-replication-transact-sql.md)



## Recuperación mediante la restauración geográfica

Si el tiempo de inactividad de la aplicación no da lugar a responsabilidades civiles, puede usar la restauración geográfica como método para recuperar las bases de datos de la aplicación. Dicho método crea una copia de la base de datos a partir de la última copia de seguridad con redundancia geográfica.

Utilice una de las siguientes guías para restaurar geográficamente una base de datos en una región nueva:

- [Geo-Restore an Azure SQL Database from a geo-redundant backup using the Azure Portal (Restauración geográfica de una Base de datos SQL de Azure a partir de una copia de seguridad con redundancia geográfica con el Portal de Azure)](sql-database-geo-restore-portal.md)
- [Restore an Azure SQL Database from a geo-redundant backup using PowerShell (Restauración de una Base de datos SQL de Azure a una región nueva con Powershell)](sql-database-geo-restore-powershell.md)


## Configuración de la base de datos después de realizar la recuperación

Si se usa la conmutación por error con replicación geográfica o la funcionalidad de restauración geográfica para recuperar la base de datos tras una interrupción, hay que asegurarse de que la conectividad con las bases de datos nuevas está configurada correctamente para que se pueda reanudar el funcionamiento normal de la aplicación. Esta es una lista de comprobación de las tareas necesarias para que la producción de la base de datos recuperada esté lista.

### Actualización de cadenas de conexión

Dado que la base de datos recuperada residirá en otro servidor, es preciso que actualice la cadena de conexión de la aplicación para que apunte a dicho servidor.

Para obtener más información sobre cómo cambiar las cadenas de conexión, consulte el lenguaje de desarrollo adecuado para la [biblioteca de conexiones](sql-database-libraries.md).

### Configuración de las reglas del firewall

Es preciso que se asegure de que las reglas del firewall configuradas tanto en el servidor como en la base de datos coinciden con las configuradas en el servidor principal y en la base de datos principal. Para obtener más información, consulte [Configuración del firewall (Base de datos SQL de Azure)](sql-database-configure-firewall-settings.md).


### Configuración de inicios de sesión de y de usuarios de la base de datos

Es preciso asegurarse de que todos los inicios de sesión que usa la aplicación existen en el servidor que hospeda la base de datos recuperada. Para obtener más información, consulte [Configuración de seguridad para Replicación geográfica activa o estándar](sql-database-geo-replication-security-config.md).

>[AZURE.NOTE] Debe configurar y probar las reglas de firewall del servidor y los inicios de sesión (y sus permisos) durante una maniobra de recuperación ante desastres. Es posible que estos objetos de nivel de servidor y su configuración no estén disponibles durante la interrupción. Para obtener más información, consulte [Maniobras de recuperación ante desastres](sql-database-disaster-recovery-drills.md).

### Configuración de alertas de telemetría

Debe asegurarse de que la configuración actual de la regla de alertas está actualizada para asignarla a la base de datos recuperada y al otro servidor.

Para obtener más información sobre las reglas de alerta de las bases de datos, consulte [Recepción de notificaciones de alerta](../azure-portal/insights-receive-alert-notifications.md) y [Estado del servicio de seguimiento](../azure-portal/insights-service-health.md).

### Habilitar auditoría

Si se requiere una auditoría para tener acceso a una base de datos, será preciso habilitar Auditoría tras la recuperación de la base de datos. Para obtener más información, consulte [Introducción a la auditoría de la Base de datos SQL](sql-database-auditing-get-started.md). Además, para "clientes de nivel inferior", consulte [Base de datos SQL: compatibilidad con clientes de nivel inferior y cambios de punto de conexión IP para auditoría](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md).


## Pasos siguientes

- Para saber en qué consisten las copias de seguridad automatizadas de Base de datos SQL de Azure, consulte [Información general: copias de seguridad automatizadas de Base de datos SQL](sql-database-automated-backups.md)
- Para más información sobre los escenarios de recuperación y diseño de la continuidad empresarial, consulte [Escenarios de continuidad empresarial de Base de datos SQL de Microsoft Azure](sql-database-business-continuity-scenarios.md)
- Si quiere saber cómo utilizar las copias de seguridad automatizadas para procesos de recuperación, consulte [Recuperación de una Base de datos SQL de Azure mediante copias de seguridad automatizadas](sql-database-recovery-using-backups.md)
- Para conocer las opciones de recuperación más rápidas, consulte [Información general: Replicación geográfica activa para Base de datos SQL de Azure](sql-database-geo-replication-overview.md)
- Si quiere aprender a utilizar las copias de seguridad automatizadas para procesos de archivado, consulte el artículo sobre [Copia de una Base de datos SQL de Azure](sql-database-copy.md)

<!---HONumber=AcomDC_0713_2016-->