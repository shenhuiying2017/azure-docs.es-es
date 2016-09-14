<properties
	pageTitle="Copia de una base de datos SQL de Azure| Microsoft Azure"
	description="Creación de una copia de una base de datos SQL de Azure"
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="06/16/2016"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>



# Copiar una base de datos SQL de Azure

> [AZURE.SELECTOR]
- [Información general](sql-database-copy.md)
- [Portal de Azure](sql-database-copy-portal.md)
- [PowerShell](sql-database-copy-powershell.md)
- [T-SQL](sql-database-copy-transact-sql.md)

Puede usar las [copias de seguridad automatizadas de Base de datos SQL](sql-database-automated-backups.md) para crear una copia de su base de datos SQL. Esta operación consiste en copiar el final del registro de transacciones y, posteriormente, utilizar las copias de seguridad completas y diferenciales de este que forman parte de las copias de seguridad automatizadas. El objetivo es que las transacciones tengan homogeneidad con la base de datos de origen a partir de la fecha de la copia de seguridad del registro de transacciones final.

Puede crear la copia de la base de datos en el mismo servidor o en otro distinto. El nivel de rendimiento y el nivel de servicio (nivel de precios) de la copia de base de datos son los mismos que la base de datos de origen. Cuando se complete la copia, esta se convierte en una base de datos independiente y completamente funcional. Los inicios de sesión, usuarios y permisos pueden administrarse de forma independiente.

Al copiar una base de datos en el mismo servidor lógico, los mismos inicios de sesión se pueden usar en ambas bases de datos. La entidad de seguridad que usa para copiar la base de datos se convierte en el propietario de la base de datos (DBO) en la nueva base de datos. Todos los usuarios de base de datos, sus permisos y sus identificadores de seguridad (SID) se copian en la copia de la base de datos.

Al copiar una base de datos en un servidor lógico diferente, la entidad de seguridad del nuevo servidor se convierte en el propietario de la nueva base de datos. Los usuarios de la base de datos independientes pueden utilizarse en la base de datos copiada. Sin embargo, al copiar la base de datos a un nuevo servidor, normalmente, los usuarios basados en inicios de sesión no funcionarán debido a que en el nuevo servidor no están esos inicios de sesión y, en caso de que realicen esta operación para conectarse, es posible que no coincidan sus SID. Después de que la nueva base de datos esté en línea en el servidor de destino, use la instrucción [ALTER USER](https://msdn.microsoft.com/library/ms176060.aspx) para volver a asignar los usuarios de la nueva base de datos a inicios de sesión en el servidor de destino. Para resolver los usuarios huérfanos, consulte [Solucionar problemas de usuarios huérfanos (SQL Server)](https://msdn.microsoft.com/library/ms175475.aspx).


Para copiar una base de datos SQL, necesita lo siguiente:

- Una suscripción de Azure. Si necesita una suscripción a Azure, haga clic en la opción **PRUEBA GRATUITA** situada en la parte superior de esta página y, a continuación, vuelva para finalizar este artículo.
- Una base de datos SQL para copiar. Si no tiene una base de datos SQL, cree una siguiendo los pasos de este artículo: [Creación de la primera Base de datos SQL de Azure](sql-database-get-started.md).

## Pasos siguientes

- Consulte [Copy an Azure SQL database using the Azure Portal](sql-database-copy-portal.md) (Copia de una base de datos SQL de Azure mediante el Portal de Azure) para copiar una base de datos a través del Portal de Azure.
- Consulte [Copia de una Base de datos SQL de Azure con PowerShell](sql-database-copy-powershell.md) para copiar una base de datos mediante PowerShell.
- Consulte [Copia de una Base de datos SQL de Azure con Transact-SQL](sql-database-copy-transact-sql.md) para copiar una base de datos mediante Transact-SQL.
- Consulte [Administración de la seguridad de Base de datos SQL de Azure después de la recuperación ante desastres](sql-database-geo-replication-security-config.md) para obtener información sobre cómo administrar usuarios e inicios de sesión al copiar una base de datos a un servidor lógico diferente.



## Recursos adicionales

- [Administración de inicios de sesión](sql-database-manage-logins.md)
- [Conexión a la Base de datos SQL con SQL Server Management Studio y realización de una consulta de T-SQL de ejemplo](sql-database-connect-query-ssms.md)
- [Exportar la base de datos a un BACPAC](sql-database-export.md)
- [Información general acerca de la continuidad del negocio](sql-database-business-continuity.md)
- [Documentación de Base de datos SQL](https://azure.microsoft.com/documentation/services/sql-database/)

<!---HONumber=AcomDC_0831_2016-->