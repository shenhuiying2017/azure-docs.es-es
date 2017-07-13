---
title: "Recuperación ante desastres de SQL Database | Microsoft Docs"
description: "Obtenga información sobre cómo recuperar una base de datos tras un error o una interrupción de un centro de datos regional con las funcionalidades de replicación geográfica activa y restauración geográfica de Azure SQL Database."
services: sql-database
documentationcenter: 
author: anosov1960
manager: jhubbard
editor: monicar
ms.assetid: 4800960e-3f9d-40ce-9e55-fb7f2784c067
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/14/2017
ms.author: sashan
ms.translationtype: Human Translation
ms.sourcegitcommit: bb794ba3b78881c967f0bb8687b1f70e5dd69c71
ms.openlocfilehash: e33f69bf04b32a31aae3c311c41aa44e4da5016a
ms.contentlocale: es-es
ms.lasthandoff: 07/06/2017


---
<a id="restore-an-azure-sql-database-or-failover-to-a-secondary" class="xliff"></a>

# Restauración de una base de datos SQL de Azure o una conmutación por error en una secundaria
Base de datos SQL de Azure ofrece las siguientes capacidades para recuperarse de un corte en el suministro eléctrico:

* [Replicación geográfica activa](sql-database-geo-replication-overview.md)
* [Restauración geográfica](sql-database-recovery-using-backups.md#point-in-time-restore)

Para obtener información sobre los escenarios de continuidad empresarial y sus características, consulte el artículo sobre [continuidad empresarial](sql-database-business-continuity.md).

<a id="prepare-for-the-event-of-an-outage" class="xliff"></a>

### Preparación ante interrupciones
Para que el proceso de recuperación pueda realizarse sin problemas en otra región de datos mediante la replicación geográfica activa o las copias de seguridad con redundancia geográfica, debe preparar un servidor en otra interrupción de un centro de datos para convertirlo en el nuevo servidor principal si lo considera necesario. También hay que contar con pasos bien definidos que se hayan documentado y probado para garantizar que la recuperación se lleve a cabo correctamente. Estos son algunos de los pasos correspondientes a la fase de preparación:

* Identificar el servidor lógico en otra región para convertirse en el nuevo servidor principal. Con la replicación geográfica activa, será, como mínimo, uno de los servidores secundarios (y probablemente todos). Para la restauración geográfica, normalmente suele ser un servidor de la [región asociada](../best-practices-availability-paired-regions.md) a aquella en donde se encuentre la base de datos.
* Identificar y, de manera opcional, definir las reglas de firewall de nivel de servidor que deben estar activas para que los usuarios accedan la nueva base de datos principal.
* Determinar cómo va a redirigir a los usuarios al nuevo servidor principal; por ejemplo, cambiar las cadenas de conexión o las entradas DNS.
* Identificar y, de manera opcional, crear los inicios de sesión que deben estar presentes en la base de datos maestra del nuevo servidor principal, así como asegurarse de que tienen los permisos adecuados en la base de datos maestra, si procede. Para obtener más información, consulte [Administración de la seguridad de Base de datos SQL de Azure después de la recuperación ante desastres](sql-database-geo-replication-security-config.md)
* Identificar las reglas de alerta que deben actualizarse para que se asignen a la nueva base de datos principal.
* Documentar la configuración de auditoría de la base de datos principal actual.
* Realice [maniobras de recuperación ante desastres](sql-database-disaster-recovery-drills.md). Para simular una interrupción con la restauración geográfica, puede eliminar o cambiar el nombre de la base de datos de origen para provocar el error de conectividad de la aplicación. Para realizar el mismo proceso con la replicación geográfica activa, puede deshabilitar la aplicación web o la máquina virtual conectadas a la base de datos, o bien llevar a cabo una conmutación por error en la base de datos para provocar errores de conectividad en la aplicación.

<a id="when-to-initiate-recovery" class="xliff"></a>

## Cuándo iniciar la recuperación
La operación de recuperación repercute en la aplicación. Este proceso requiere cambiar el redireccionamiento o la cadena de conexión SQL mediante DNS, y podría provocar la pérdida de datos permanente. Por lo tanto, debe realizarse solo cuando la duración de la interrupción pueda durar más que el objetivo de tiempo de recuperación de la aplicación. Cuando se implementa la aplicación en producción, deberá supervisar con frecuencia el estado de la aplicación. Para ello, use los siguientes puntos de datos para garantizar la recuperación:

1. Error de conectividad permanente de la capa de aplicación en la base de datos.
2. El Portal de Azure muestra una alerta sobre una incidencia en una región con un gran impacto.
3. El servidor de Base de datos SQL de Azure está marcado como degradado.

En función de la tolerancia de la aplicación al tiempo de inactividad y de la posible responsabilidad civil, puede considerar las siguientes opciones de recuperación.

Use la opción [Get Recoverable Database](https://msdn.microsoft.com/library/dn800985.aspx) (Obtener base de datos recuperable) (*LastAvailableBackupDate*) para obtener el último punto de restauración de replicación geográfica.

<a id="wait-for-service-recovery" class="xliff"></a>

## Espera para la recuperación del servicio
Los equipos de Azure trabajan diligentemente para restaurar la disponibilidad del servicio lo antes posible, pero dependiendo de la causa principal pueden tardar horas, o incluso días.  Si la aplicación puede tolerar un tiempo de inactividad considerable, puede esperar hasta que se complete la recuperación. En este caso, no se requieren acciones por su parte. El estado actual del servicio se puede ver en el [panel de estado de los servicios de Azure](https://azure.microsoft.com/status/). Después de la recuperación de la región se restaurará la disponibilidad de su aplicación.

<a id="fail-over-to-geo-replicated-secondary-database" class="xliff"></a>

## Conmutación por error de la base de datos secundaria de replicación geográfica
Si el tiempo de inactividad de la aplicación puede dar lugar a responsabilidades civiles, debería utilizar bases de datos con replicación geográfica en la aplicación. Esto permitirá que la aplicación restaure rápidamente la disponibilidad en una región diferente en caso de interrupción. Obtenga información sobre cómo [configurar la replicación geográfica](sql-database-geo-replication-portal.md).

Para restaurar la disponibilidad de las bases de datos, es preciso iniciar la conmutación por error en la secundaria con replicación geográfica mediante uno de los métodos admitidos.

Utilice una de las siguientes guías para realizar la conmutación por error en una base de datos secundaria con replicación geográfica:

* [Configuración de replicación geográfica con Azure Portal](sql-database-geo-replication-portal.md)
* [Configuración de la replicación geográfica con PowerShell](scripts/sql-database-setup-geodr-and-failover-database-powershell.md)
* [Conmutación por error a una base de datos secundaria con replicación geográfica mediante Transact-SQL](sql-database-geo-replication-transact-sql.md)

<a id="recover-using-geo-restore" class="xliff"></a>

## Recuperación mediante la restauración geográfica
Si el tiempo de inactividad de la aplicación no da lugar a responsabilidades civiles, puede usar la [restauración geográfica](sql-database-recovery-using-backups.md) como método para recuperar las bases de datos de la aplicación. Dicho método crea una copia de la base de datos a partir de la última copia de seguridad con redundancia geográfica.

<a id="configure-your-database-after-recovery" class="xliff"></a>

## Configuración de la base de datos después de realizar la recuperación
Si se usa la conmutación por error con replicación geográfica o la funcionalidad de restauración geográfica para recuperar la base de datos tras una interrupción, hay que asegurarse de que la conectividad con las bases de datos nuevas está configurada correctamente para que se pueda reanudar el funcionamiento normal de la aplicación. Esta es una lista de comprobación de las tareas necesarias para que la producción de la base de datos recuperada esté lista.

<a id="update-connection-strings" class="xliff"></a>

### Actualización de cadenas de conexión
Dado que la base de datos recuperada residirá en otro servidor, es preciso que actualice la cadena de conexión de la aplicación para que apunte a dicho servidor.

Para obtener más información sobre cómo cambiar las cadenas de conexión, consulte el lenguaje de desarrollo adecuado para la [biblioteca de conexiones](sql-database-libraries.md).

<a id="configure-firewall-rules" class="xliff"></a>

### Configuración de las reglas del firewall
Es preciso que se asegure de que las reglas del firewall configuradas tanto en el servidor como en la base de datos coinciden con las configuradas en el servidor principal y en la base de datos principal. Para obtener más información, consulte [Configuración del firewall (Base de datos SQL de Azure)](sql-database-configure-firewall-settings.md).

<a id="configure-logins-and-database-users" class="xliff"></a>

### Configuración de inicios de sesión de y de usuarios de la base de datos
Es preciso asegurarse de que todos los inicios de sesión que usa la aplicación existen en el servidor que hospeda la base de datos recuperada. Para obtener más información, consulte [Configuración de seguridad para Replicación geográfica activa o estándar](sql-database-geo-replication-security-config.md).

> [!NOTE]
> Debe configurar y probar las reglas de firewall del servidor y los inicios de sesión (y sus permisos) durante una maniobra de recuperación ante desastres. Es posible que estos objetos de nivel de servidor y su configuración no estén disponibles durante la interrupción.
> 
> 

<a id="setup-telemetry-alerts" class="xliff"></a>

### Configuración de alertas de telemetría
Debe asegurarse de que la configuración actual de la regla de alertas está actualizada para asignarla a la base de datos recuperada y al otro servidor.

Para obtener más información sobre las reglas de alerta de las bases de datos, consulte [Recibir notificaciones de alerta](../monitoring-and-diagnostics/insights-receive-alert-notifications.md) y [Realización de seguimiento del estado](../monitoring-and-diagnostics/insights-service-health.md).

<a id="enable-auditing" class="xliff"></a>

### Habilitar auditoría
Si se requiere una auditoría para tener acceso a una base de datos, será preciso habilitar Auditoría tras la recuperación de la base de datos. Para obtener más información, consulte [este artículo sobre la realización de auditorías en las bases de datos](sql-database-auditing.md).

<a id="next-steps" class="xliff"></a>

## Pasos siguientes
* Para obtener información sobre las copias de seguridad automatizadas de Base de datos SQL de Azure, consulte [Información general: copias de seguridad automatizadas de Base de datos SQL](sql-database-automated-backups.md)
* Para obtener información sobre los escenarios de recuperación y diseño de la continuidad empresarial, consulte el artículo sobre [escenarios de continuidad](sql-database-business-continuity.md)
* Si quiere saber cómo utilizar las copias de seguridad automatizadas para procesos de recuperación, consulte [Recover an Azure SQL database using automated database backups](sql-database-recovery-using-backups.md)


