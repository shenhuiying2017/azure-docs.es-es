---
title: "Replicación geográfica activa para Base de datos SQL de Azure"
description: "La replicación geográfica activa permite configurar cuatro réplicas de la base de datos en cualquiera de los centros de datos de Azure."
services: sql-database
documentationcenter: na
author: anosov1960
manager: jhubbard
editor: monicar
ms.assetid: 2a29f657-82fb-4283-9a83-e14a144bfd93
ms.service: sql-database
ms.custom: business continuity
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: NA
ms.date: 09/26/2016
ms.author: sashan
translationtype: Human Translation
ms.sourcegitcommit: 07635b0eb4650f0c30898ea1600697dacb33477c
ms.openlocfilehash: bd3aea04266baebbba1b953d5a2b7c4b2fb41a87
ms.lasthandoff: 03/28/2017


---
# <a name="overview-sql-database-active-geo-replication"></a>Información general: Replicación geográfica activa para Base de datos SQL de Azure
La replicación geográfica activa le permite configurar hasta cuatro bases de datos secundarias legibles en las mismas ubicaciones de centros de datos (regiones) o en otras. Las bases de datos secundarias están disponibles para la consulta y la conmutación por error en caso de una interrupción del centro de datos o de imposibilidad para conectarse a la base de datos principal. La replicación geográfica activa debe producirse entre bases de datos de la misma suscripción.

> [!NOTE]
> La replicación geográfica activa (bases de datos secundarias legibles) está ahora disponible para todas las bases de datos en todos los niveles de servicio. En abril de 2017 se retirará el tipo secundario no legible y las bases de datos no legibles existentes se actualizarán automáticamente a secundarias legibles.
>  

 Puede configurar la replicación geográfica activa mediante [Azure Portal](sql-database-geo-replication-portal.md), [PowerShell](sql-database-geo-replication-powershell.md), [Transact-SQL](sql-database-geo-replication-transact-sql.md) o la [API de REST: creación o actualización de la base de datos](https://msdn.microsoft.com/library/azure/mt163685.aspx).

Si, por cualquier motivo, se produce un error en la base de datos principal o, simplemente, debe desconectarse, puede *conmutar por error* a cualquiera de las secundarias. Cuando se activa la conmutación por error a una de las bases de datos secundarias, las demás bases de datos secundarias se vinculan automáticamente a la nueva base de datos principal.

Puede conmutar por error a una base de datos secundaria mediante [Azure Portal](sql-database-geo-replication-failover-portal.md), [PowerShell](scripts/sql-database-setup-geodr-and-failover-database-powershell.md), [Transact-SQL](sql-database-geo-replication-failover-transact-sql.md), la [API de REST: conmutación por error planeada](https://msdn.microsoft.com/library/mt575007.aspx) o la [API de REST: conmutación por error no planeada](https://msdn.microsoft.com/library/mt582027.aspx).

Después de la conmutación por error, asegúrese de que los requisitos de autenticación para el servidor y la base de datos se configuran en el nuevo elemento principal. Para obtener más información, consulte [Administración de la seguridad de Base de datos SQL de Azure después de la recuperación ante desastres](sql-database-geo-replication-security-config.md).

La característica Replicación geográfica activa implementa un mecanismo para proporcionar redundancia de base de datos en la misma región de Microsoft Azure o en distintas regiones (redundancia geográfica). Replicación geográfica activa replica de forma asincrónica las transacciones confirmadas desde una base de datos en hasta cuatro copias de la base de datos en servidores diferentes mediante el aislamiento instantáneo de lectura confirmada (RCSI). Cuando se configura la replicación geográfica activa, se crea una base de datos secundaria en el servidor especificado y la base de datos original se convierte en la principal. La base de datos principal replica de forma asincrónica las transacciones confirmadas en cada una de las bases de datos secundarias. Solo se replican las transacciones completas. Mientras que, en cualquier momento dado, la base de datos secundaria puede ir ligeramente por detrás de la base de datos principal, se garantiza que los datos secundarios nunca tengan transacciones parciales. Los datos específicos de RPO se encuentran en [Introducción a la continuidad empresarial](sql-database-business-continuity.md).

Una de las principales ventajas de la replicación geográfica activa es que ofrece una solución de recuperación ante desastres en el nivel de base de datos con un tiempo de recuperación reducido. Cuando se coloca la base de datos secundaria en un servidor de una región distinta, se agrega la máxima resistencia a la aplicación. La redundancia entre regiones permite que las aplicaciones se recuperen de la pérdida permanente de todo un centro de datos, o de partes de él, causada por desastres naturales, errores humanos catastróficos o actos malintencionados. En la siguiente ilustración, se muestra un ejemplo de replicación geográfica activa configurada con una principal en la región centro-norte de EE. UU. y una secundaria en la región centro-sur de EE. UU.

![Relación de replicación geográfica](./media/sql-database-active-geo-replication/geo-replication-relationship.png)

Otra ventaja clave es que las bases de datos secundarias son legibles y se pueden usar para descargar cargas de trabajo de solo lectura como trabajos de informes. Si solo va a usar la base de datos secundaria para el equilibrio de carga, puede crearla en la misma región que la principal. La creación de una base de datos secundaria en la misma región no aumenta la resistencia de la aplicación ante errores catastróficos.  

Algunos otros escenarios donde se puede usar Replicación geográfica activa son:

* **Migración de base de datos**: puede usar Replicación geográfica activa para migrar una base de datos de un servidor a otro en línea con un tiempo de inactividad mínimo.
* **Actualizaciones de aplicaciones**: puede crear una base de datos secundaria adicional como copia de conmutación por recuperación durante las actualizaciones de la aplicación.

Para lograr una verdadera continuidad empresarial, agregar redundancia de base de datos entre centros de datos es solo parte de la solución. Para recuperar una aplicación (un servicio) de un extremo a otro tras un error catastrófico, es necesario recuperar todos los componentes que constituyen el servicio y cualquier servicio dependiente. Algunos ejemplos de estos componentes son el software cliente (por ejemplo, un explorador con JavaScript personalizado), los front-end web, el almacenamiento y DNS. Es fundamental que todos los componentes sean resistentes a los mismos errores y que estén disponibles en el plazo del objetivo de tiempo de recuperación (RTO) de la aplicación. Por lo tanto, debe identificar todos los servicios dependientes y comprender las garantías y capacidades que ofrecen. A continuación, debe seguir los pasos adecuados para asegurarse de que el servicio funcione durante la conmutación por error de los servicios de los que depende. Para obtener más información sobre el diseño de soluciones para la recuperación ante desastres, consulte [Diseño de aplicaciones de nube para la continuidad de negocio mediante replicación geográfica](sql-database-designing-cloud-solutions-for-disaster-recovery.md).

## <a name="active-geo-replication-capabilities"></a>Funcionalidad de replicación geográfica activa
La característica Replicación geográfica activa ofrece la funcionalidad esencial siguiente:

* **Replicación asincrónica automática**: solo se puede crear una base de datos secundaria mediante la adición a una existente. La base de datos secundaria solo se puede crear en un servidor de Base de datos SQL de Azure diferente. Una vez creada, la base de datos secundaria se rellena con los datos copiados de la base de datos principal. Este proceso se conoce como propagación. Después de crear y propagar la base de datos secundaria, las actualizaciones de la base de datos principal se replican asincrónicamente en la base de datos secundaria de forma automática. La replicación asincrónica significa que las transacciones se confirman en la base de datos principal antes de replicarse en la secundaria. 
* **Varias bases de datos secundarias**: dos o más bases de datos secundarias aumentan la redundancia y el nivel de protección de la base de datos principal y la aplicación. Si existen varias bases de datos secundarias, la aplicación sigue estando protegida incluso si se produce un error en una de ellas. Si solo hay una base de datos secundaria y se produce un error, la aplicación está expuesta a un mayor riesgo hasta que se crea una nueva base de datos secundaria.
* **Bases de datos secundarias legibles**: una aplicación puede acceder a una base de datos secundaria para operaciones de solo lectura con las mismas entidades de seguridad que las usadas para tener acceso a la base de datos principal u otras diferentes. Las bases de datos secundarias funcionan en el modo de aislamiento de instantánea para asegurarse de que no se retrasa la replicación de las actualizaciones de la base de datos principal (reproducción de registro) por las consultas ejecutadas en la secundaria.

> [!NOTE]
> La reproducción de registros se retrasa en la base de datos secundaria si las actualizaciones de esquema que está recibiendo desde la principal requieren que se bloquee el esquema en la base de datos secundaria.
> 
> 

* **Replicación geográfica activa de bases de datos de grupos elásticos**: se puede configurar la replicación geográfica activa para cualquier base de datos en un grupo elástico. La base de datos secundaria puede estar en otro grupo elástico. Para las bases de datos normales, la base de datos secundaria puede ser un grupo elástico y viceversa, siempre que los niveles de servicio sean los mismos. 
* **Nivel de rendimiento configurable de la base de datos secundaria**: se puede crear una base de datos secundaria con un nivel de rendimiento inferior al de la principal. Es necesario que tanto la base de datos principal como las secundarias tengan el mismo nivel de servicio. No se recomienda esta opción para las aplicaciones con elevada actividad de escritura en la base de datos porque el mayor retraso de replicación aumenta el riesgo de perder una cantidad considerable de datos después de una conmutación por error. Además, después de la conmutación por error, el rendimiento de la aplicación se ve afectado hasta que la nueva base de datos principal se actualice a un nivel de rendimiento mayor. El gráfico de porcentaje de E/S de registro en Azure Portal proporciona un buen método para estimar el nivel mínimo de rendimiento de la base de datos secundaria que es necesario para mantener la carga de replicación. Por ejemplo, si la base de datos principal es P6 (1000 DTU) y su porcentaje de E/S de registro es 50 %, la base de datos secundaria debe ser al menos P4 (500 DTU). También puede recuperar los datos de E/S de registro mediante las vistas de base de datos [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) o [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx).  Para más información sobre los niveles de rendimiento de Base de datos SQL, consulte [Opciones y rendimiento de Base de datos SQL: comprender lo que está disponible en cada nivel de servicio](sql-database-service-tiers.md). 
* **Conmutación por error y conmutación por recuperación controladas por el usuario**: la aplicación o el usuario puede cambiar explícitamente una base de datos secundaria al rol principal en cualquier momento. Durante una interrupción real, debe utilizarse la opción "no planeada", que promueve inmediatamente una base de datos secundaria a principal. Cuando la base de datos principal que generó el error se recupera y vuelve a estar disponible, el sistema la marca automáticamente como secundaria y la pone al día con la nueva base de datos principal. Por la naturaleza asincrónica de la replicación, se puede perder una pequeña cantidad de datos durante las conmutaciones por error no planeadas antes de que se repliquen los cambios más recientes a la base de datos secundaria. Cuando una base de datos principal con varias secundarias conmuta por error, el sistema vuelve a configurar automáticamente las relaciones de replicación y vincula las bases de datos secundarias restantes a la principal recién promovida sin necesidad de que intervenga el usuario. Una vez que se mitiga la interrupción que causó la conmutación por error, sería conveniente devolver la aplicación a la región primaria. Para hacer esto, se debe invocar el comando de conmutación por error con la opción "planeada". 
* **Mantenimiento de las credenciales y las reglas de firewall sincronizadas**: se recomienda usar las [reglas de firewall de base de datos](sql-database-firewall-configure.md) para las bases de datos con replicación geográfica. Así, estas reglas se pueden replicar con la base de datos para asegurarse de que todas las bases de datos secundarias tengan las mismas reglas de firewall que la principal. Este enfoque elimina la necesidad de que los clientes configuren y mantengan manualmente las reglas de firewall en los servidores que hospedan tanto la base de datos principal como las secundarias. Igualmente, la utilización de [usuarios de base de datos independiente](sql-database-manage-logins.md) para el acceso a los datos garantiza que la base de datos principal y las secundarias tengan siempre las mismas credenciales de usuario. Por tanto, durante una conmutación por error, no hay interrupciones debidas a discrepancias en los inicios de sesión y las contraseñas. Con la adición de [Azure Active Directory](../active-directory/active-directory-whatis.md), los clientes pueden administrar el acceso de usuarios a la base de datos principal y a las secundarias, por lo que ya no es necesario administrar credenciales en las bases de datos.

## <a name="upgrading-or-downgrading-a-primary-database"></a>Actualización o degradación de una base de datos principal
Puede actualizar o degradar una base de datos principal a un nivel de rendimiento diferente (en el mismo nivel de servicio) sin desconectar las bases de datos secundarias. Al actualizar, se recomienda que actualice la base de datos secundaria en primer lugar y, a continuación, la principal. Al degradar, invierta el orden: degrade primero la base de datos principal y, después, la secundaria. 

La base de datos secundaria debe estar en el mismo nivel de servicio que la principal, por lo que para migrar la base de datos principal a un nivel de servicio distinto debe terminar el vínculo de replicación geográfica y cambiar el nombre de la base de datos secundaria o simplemente quitarlo. A continuación, migre la base de datos principal al nuevo nivel de servicio y vuelva a configurar la replicación geográfica. La nueva base de datos secundaria se creará automáticamente con el mismo nivel de rendimiento que la principal de forma predeterminada.

## <a name="preventing-the-loss-of-critical-data"></a>Evitar la pérdida de datos críticos
Debido a la elevada latencia de las redes de área extensa, la copia continua usa un mecanismo de replicación asincrónica. La replicación asincrónica hace inevitable la pérdida de cierta cantidad datos si se produce un error. Sin embargo, es posible que algunas aplicaciones exijan que no se pierdan datos. Para proteger estas actualizaciones críticas, un desarrollador de aplicaciones puede llamar al procedimiento del sistema [sp_wait_for_database_copy_sync](https://msdn.microsoft.com/library/dn467644.aspx) inmediatamente después de confirmar la transacción. La llamada a **sp_wait_for_database_copy_sync** bloquea el subproceso de llamada hasta que se replica la última transacción confirmada en la base de datos secundaria. El procedimiento esperará hasta que la base de datos secundaria confirme todas las transacciones en cola. **sp_wait_for_database_copy_sync** está dirigida a un vínculo de copia continua específico. Cualquier usuario con derechos de conexión para la base de datos principal puede llamar a este procedimiento.

> [!NOTE]
> El retraso causado por una llamada al procedimiento **sp_wait_for_database_copy_sync** puede ser considerable. El retraso depende de la longitud del registro de transacciones en ese momento y esta llamada no devuelve resultados hasta que se replique el registro completo. Evite llamar a este procedimiento a menos que sea absolutamente necesario.
> 
> 

## <a name="programmatically-managing-active-geo-replication"></a>Administración mediante programación de la replicación geográfica activa
Como se dijo antes, la replicación geográfica activa también puede administrarse mediante programación con Azure PowerShell y la API de REST. En las tablas siguientes se describe el conjunto de comandos disponibles.

* **API de Azure Resource Manager y seguridad basada en roles**: la replicación geográfica activa incluye un conjunto de [API de Azure Resource Manager](https://msdn.microsoft.com/library/azure/mt163571.aspx) para la administración, en el que se incluyen [cmdlets de PowerShell basados en Azure Resource Manager](sql-database-geo-replication-powershell.md). Estas API requieren que se usen grupos de recursos y admiten la seguridad basada en roles (RBAC). Para más información sobre cómo implementar los roles de acceso, consulte [Control de acceso basado en roles de Azure](../active-directory/role-based-access-control-configure.md).

> [!NOTE]
> Muchas de las nuevas características de la replicación geográfica activa solo se admiten con [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md), que se basa en la [API de REST de Azure SQL](https://msdn.microsoft.com/library/azure/mt163571.aspx) y los [cmdlets de PowerShell de Azure SQL Database](https://msdn.microsoft.com/library/azure/mt574084.aspx). La [API de REST (clásica)](https://msdn.microsoft.com/library/azure/dn505719.aspx) y los [cmdlets de Azure SQL Database (clásicos)](https://msdn.microsoft.com/library/azure/dn546723.aspx) se admiten por compatibilidad con versiones anteriores, por lo que se recomienda usar las API basadas en Azure Resource Manager. 
> 
> 

### <a name="transact-sql"></a>Transact-SQL
| Comando | Description |
| --- | --- |
| [ALTER DATABASE (Base de datos SQL de Azure)](https://msdn.microsoft.com/library/mt574871.aspx) |Se utiliza el argumento ADD SECONDARY ON SERVER a fin de crear una base de datos secundaria para una base de datos existente e iniciar la replicación de datos |
| [ALTER DATABASE (Base de datos SQL de Azure)](https://msdn.microsoft.com/library/mt574871.aspx) |Se utiliza FAILOVER o FORCE_FAILOVER_ALLOW_DATA_LOSS para cambiar una base de datos de secundaria a principal e iniciar la conmutación por error. |
| [ALTER DATABASE (Base de datos SQL de Azure)](https://msdn.microsoft.com/library/mt574871.aspx) |Se utiliza REMOVE SECONDARY ON SERVER para finalizar una replicación de datos entre una Base de datos SQL y la base de datos secundaria especificada. |
| [sys.geo_replication_links (Azure SQL Database)](https://msdn.microsoft.com/library/mt575501.aspx) |Devuelve información sobre todos los vínculos de replicación existentes para cada base de datos del servidor lógico de Azure SQL Database. |
| [sys.dm_geo_replication_link_status (Azure SQL Database)](https://msdn.microsoft.com/library/mt575504.aspx) |Obtiene la hora de la última replicación, el retraso de la última replicación y otro tipo de información sobre el vínculo de replicación para una base de datos SQL determinada. |
| [sys.dm_operation_status (Azure SQL Database)](https://msdn.microsoft.com/library/dn270022.aspx) |Muestra el estado de todas las operaciones de base de datos, incluido el estado de los vínculos de replicación. |
| [sp_wait_for_database_copy_sync (Azure SQL Database)](https://msdn.microsoft.com/library/dn467644.aspx) |Hace que la aplicación espere a que se repliquen todas las transacciones confirmadas y a que las reconozca la base de datos secundaria activa. |
|  | |

### <a name="powershell"></a>PowerShell
| Cmdlet | Descripción |
| --- | --- |
| [Get-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt603648.aspx) |Obtiene una o más bases de datos. |
| [New-AzureRmSqlDatabaseSecondary](https://msdn.microsoft.com/library/mt603689.aspx) |Crea una base de datos secundaria para una base de datos existente e inicia la replicación de datos. |
| [Set-AzureRmSqlDatabaseSecondary](https://msdn.microsoft.com/library/mt619393.aspx) |Convierte una base de datos secundaria en principal para iniciar la conmutación por error. |
| [Remove-AzureRmSqlDatabaseSecondary](https://msdn.microsoft.com/library/mt603457.aspx) |Finaliza una replicación de datos entre una base de datos SQL y la base de datos secundaria especificada. |
| [Get-AzureRmSqlDatabaseReplicationLink](https://msdn.microsoft.com/library/mt619330.aspx) |Obtiene los vínculos de replicación geográfica entre una Base de datos SQL de Azure y un grupo de recursos o SQL Server. |
|  | |

### <a name="rest-api"></a>API de REST
| API | Description |
| --- | --- |
| [Crear o actualizar base de datos (createMode=Restore)](https://msdn.microsoft.com/library/azure/mt163685.aspx) |Crea, actualiza o restaura una base de datos principal o secundaria. |
| [Obtener el estado de creación o actualización de la base de datos](https://msdn.microsoft.com/library/azure/mt643934.aspx) |Devuelve el estado durante una operación de creación. |
| [Establecer la base de datos secundaria como principal (conmutación por error planeada)](https://msdn.microsoft.com/library/azure/mt575007.aspx) |Promueve una base de datos secundaria en una asociación de replicación geográfica para convertirla en la nueva base de datos principal. |
| [Establecer la base de datos secundaria como principal (conmutación por error no planeada)](https://msdn.microsoft.com/library/azure/mt582027.aspx) |Forzar una conmutación por error a la base de datos secundaria y establecer la base de datos secundaria como principal. |
| [Obtener vínculos de replicación](https://msdn.microsoft.com/library/azure/mt600929.aspx) |Obtiene todos los vínculos de replicación para una Base de datos SQL determinada en una asociación de replicación geográfica. Recupera la información visible en la vista de catálogo sys.geo_replication_links. |
| [Obtener vínculo de replicación](https://msdn.microsoft.com/library/azure/mt600778.aspx) |Obtiene un vínculo de replicación específico para una Base de datos SQL determinada en una asociación de replicación geográfica. Recupera la información visible en la vista de catálogo sys.geo_replication_links. |
|  | |

## <a name="next-steps"></a>Pasos siguientes
* Para obtener una descripción general y los escenarios de la continuidad empresarial, consulte [Información general sobre la continuidad empresarial](sql-database-business-continuity.md)
* Para saber en qué consisten las copias de seguridad automatizadas de Base de datos SQL de Azure, consulte [Información general: copias de seguridad automatizadas de Base de datos SQL](sql-database-automated-backups.md).
* Si quiere saber cómo usar las copias de seguridad automatizadas para procesos de recuperación, consulte [Recuperación de una base de datos a partir de copias de seguridad iniciadas por un servicio](sql-database-recovery-using-backups.md).
* Si quiere aprender a utilizar las copias de seguridad automatizadas para procesos de archivado, consulte el procedimiento para [copiar una base de datos](sql-database-copy.md).
* Para obtener información acerca de los requisitos de autenticación para un nuevo servidor principal y la base de datos, consulte [Administración de la seguridad de Base de datos SQL de Azure después de la recuperación ante desastres](sql-database-geo-replication-security-config.md).


