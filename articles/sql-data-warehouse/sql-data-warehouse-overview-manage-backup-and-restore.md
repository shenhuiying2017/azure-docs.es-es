<properties
   pageTitle="Copia de seguridad y restauración de bases de datos en Almacenamiento de datos SQL de Azure (información general) | Microsoft Azure"
   description="Información general de las opciones de copia de seguridad y restauración para recuperar una base de datos en Almacenamiento de datos SQL de Azure."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="elfisher"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="05/05/2016"
   ms.author="elfish;barbkess;sonyama"/>


# Copia de seguridad y restauración de bases de datos en Almacenamiento de datos SQL de Azure (información general)

> [AZURE.SELECTOR]
- [Información general](sql-data-warehouse-overview-manage-backup-and-restore.md)
- [Portal](sql-data-warehouse-manage-backup-and-restore-tasks-portal.md)
- [PowerShell](sql-data-warehouse-manage-backup-and-restore-tasks-powershell.md)
- [REST](sql-data-warehouse-manage-backup-and-restore-tasks-rest-api.md)

En este artículo se describen los conceptos básicos de planificación de continuidad del negocio y recuperación ante desastres con el Almacenamiento de datos SQL. Los almacenamientos de datos constituyen el repositorio central de información para empresas y desempeñan un papel fundamental en las operaciones diarias de análisis e inteligencia empresarial en todos los niveles de la organización. Por lo tanto, es esencial que el almacenamiento de datos sea confiable y permita la recuperación y la operación continua. En concreto, este artículo le mostrará cómo Almacenamiento de datos SQL le permite recuperarse ante desastres y errores del usuario con Restaurar base de datos y Restauración geográfica.

## Planeación de la recuperación 

Es necesario optimizar la continuidad del negocio y los planes de recuperación ante desastres para lo siguiente:

**Objetivo de tiempo de recuperación (RTO)**: máximo tiempo aceptable antes de que la base de datos se recupere por completo tras una interrupción; es decir, máxima pérdida de disponibilidad durante los errores.

**Objetivo de punto de recuperación (RPO)**: máxima ventana de tiempo aceptable de actualizaciones perdidas cuando la base de datos se recupera tras una interrupción; es decir, máxima pérdida de datos durante los errores.

**Tiempo estimado de recuperación (ERT):** duración estimada para que la base de datos esté completamente operativa tras una solicitud de restauración.

Para más información, consulte [Información general: continuidad del negocio en la nube y recuperación ante desastres con la Base de datos SQL][].

## Escenarios de recuperación

**Recuperación de errores de infraestructura:** este escenario se refiere a la recuperación de problemas de infraestructura tales como, errores de disco, etc. Un cliente quiere garantizar la continuidad del negocio mediante una infraestructura de alta disponibilidad con tolerancia a errores.

**Recuperación de errores de usuario:** este escenario se refiere a la recuperación de datos dañados o eliminados de manera fortuita o involuntaria. Por si un usuario modificara o eliminara datos de manera fortuita o involuntaria, un cliente desea tener la opción de garantizar la continuidad del negocio mediante la restauración de la base de datos a un momento anterior en el tiempo.

**Recuperación ante desastres (DR):** este escenario se refiere a la recuperación de una catástrofe grave. Por si se diera el caso de que una interrupción, como un desastre natural o un apagón regional, hiciera que la base de datos dejase de estar disponible, un cliente desea tener la opción de recuperar la base de datos en otra región para continuar con las operaciones del negocio.


## Capacidades de copia de seguridad y restauración

Veamos cómo Almacenamiento de datos SQL mejora la confiabilidad de la base de datos y permite la recuperación y la operación continua en los escenarios anteriormente mencionados.


### Redundancia de datos

Almacenamiento de datos SQL separa el proceso y el almacenamiento. Todos los datos se escriben directamente en Almacenamiento de Azure con redundancia geográfica (RA-GRS). El almacenamiento con redundancia geográfica replica sus datos a una región secundaria que se encuentra a cientos de kilómetros de distancia de la región principal. Los datos se replican tres veces en cada región primaria y secundaria, en dominios de error y en dominios de actualización independientes. Esto garantiza que los datos perduran incluso en el caso de un apagón regional completo o un desastre que haga que una de las regiones deje de estar disponible. Para obtener más información sobre almacenamiento con redundancia geográfica con acceso de lectura, lea [Opciones de redundancia de Almacenamiento de Azure][].

### Restauración de base de datos

La restauración de base de datos está diseñada para restablecer la base de datos a un punto anterior en el tiempo. El servicio Almacenamiento de datos SQL de Azure protege todas las bases de datos con instantáneas de almacenamiento automáticas cada 8 horas como mínimo y las conserva durante 7 días para ofrecerle un conjunto discreto de puntos de restauración. Estas copias de seguridad se almacenan en el Almacenamiento de Azure RA-GRS y, por tanto, tienen redundancia geográfica de forma predeterminada. Las características de copia de seguridad y restauración automáticas permiten proteger las bases de datos de daños o eliminaciones accidentales sin esfuerzos de administración. Para más información sobre la restauración de bases de datos, consulte [Backup and restore tasks][] (Tareas de copia de seguridad y restauración).

### Restauración geográfica

La restauración geográfica se ha diseñado para recuperar la base de datos en caso de que deje de estar disponible por una interrupción. Dado que la copia de seguridad tiene redundancia geográfica, puede usarse para recuperar una base de datos aunque la base de datos sea inaccesible debido a una interrupción. La base de datos restaurada se puede crear en cualquier servidor y en cualquier región de Azure. Además de la recuperación de una interrupción, la restauración geográfica también se puede usar para otros escenarios como la migración o copia de una base de datos a un servidor o una región diferente.

**Cuándo iniciar la recuperación** La operación de recuperación requiere cambiar la cadena de conexión SQL en la recuperación y puede provocar la pérdida de datos permanente. Por lo tanto, debe realizarse solo cuando la duración de la interrupción pueda durar más que el RTO de su aplicación. Utilice los siguientes puntos de datos para reafirmar que la recuperación está garantizada:

- Error de conectividad permanente en la base de datos.
- El Portal de Azure muestra una alerta sobre una incidencia en una región con un gran impacto.


## Pasos siguientes
Para otras tareas de administración importantes, consulte [Información general de administración][].

<!--Image references-->

<!--Article references-->
[Opciones de redundancia de Almacenamiento de Azure]: ../storage/storage-redundancy.md#read-access-geo-redundant-storage
[Backup and restore tasks]: sql-data-warehouse-backup-and-restore-tasks-portal.md
[Finalize a recovered database]: ../sql-database/sql-database-recovered-finalize.md
[Información general de administración]: sql-data-warehouse-overview-management.md
[Información general: continuidad del negocio en la nube y recuperación ante desastres con la Base de datos SQL]: ../sql-database/sql-database-business-continuity.md

<!--MSDN references-->


<!--Other Web references-->

<!---HONumber=AcomDC_0518_2016-->