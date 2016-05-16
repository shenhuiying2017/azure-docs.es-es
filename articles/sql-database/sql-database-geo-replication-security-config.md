<properties
	pageTitle="Configuración de seguridad para Replicación geográfica activa o estándar"
	description="En este tema, se explican las consideraciones de seguridad para administrar escenarios de Replicación geográfica activa para Base de datos SQL."
	services="sql-database"
	documentationCenter="na"
	authors="carlrabeler"
	manager="jhubbard"
	editor="monicar" />


<tags
	ms.service="sql-database"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="data-management"
	ms.date="04/27/2016"
	ms.author="carlrab" />

# Configuración de seguridad para Replicación geográfica activa o estándar

>[AZURE.NOTE] [Active Geo-Replication](sql-database-geo-replication-overview.md) está disponible ahora para todas las bases de datos en todos los niveles de servicio.

## Información general sobre los requisitos de autenticación para Replicación geográfica activa
En este tema se describen los requisitos de autenticación para configurar y controlar la [Replicación geográfica activa para Base de datos SQL de Azure](sql-database-geo-replication-overview.md) y los pasos necesarios para configurar el acceso de usuario a la base de datos secundaria. Para obtener más información sobre el uso de la replicación geográfica, consulte [Recuperación de una base de datos SQL de Azure tras una interrupción](sql-database-disaster-recovery.md).

## Uso de la replicación geográfica activa con usuarios independientes
Con la [versión V12 de Base de datos SQL de Azure](sql-database-v12-whats-new.md), Base de datos SQL admite ahora los usuarios independientes. A diferencia de los usuarios tradicionales, que deben asignarse a inicios de sesión en la base de datos maestra, un usuario independiente se administra completamente en la base de datos, lo que ofrece dos ventajas. En el escenario de replicación geográfica, los usuarios pueden proceder a conectarse a la base de datos secundaria sin ninguna configuración adicional, ya que la base de datos administra los usuarios. También existen ventajas potenciales de escalabilidad y rendimiento con esta configuración desde la perspectiva del inicio de sesión. Para obtener más información, consulte [Usuarios de base de datos independiente: hacer que la base de datos sea portátil](https://msdn.microsoft.com/library/ff929188.aspx).

Si tiene varias bases de datos que usan el mismo inicio de sesión, el mantenimiento de las credenciales que usan los usuarios independientes en varias bases de datos puede invalidar las ventajas de los usuarios independientes. Por ejemplo, si la contraseña cambia, el cambio tendrá que hacerse por separado para el usuario independiente de cada base de datos, en lugar de cambiar la contraseña de inicio de sesión una sola vez en el nivel del servidor. Por este motivo, si tiene varias bases de datos que utilizan el mismo nombre de usuario y la misma contraseña, no se recomienda usar usuarios independientes.

## Uso de inicios de sesión y usuarios con la replicación geográfica activa
Si usa inicios de sesión y usuarios (en lugar de usuarios independientes), debe realizar pasos adicionales para asegurarse de que existan los mismos inicios de sesión en el servidor de la base de datos secundaria. En las secciones siguientes, se describen los pasos necesarios y otras consideraciones.

### Configuración del acceso de usuario a una base de datos secundaria
Para que la base de datos secundaria se pueda usar como base de datos de solo lectura secundaria o como base de datos principal viable después de una conmutación por error, secundaria debe tener la configuración de seguridad adecuada.

El administrador del servidor o los usuarios con los permisos adecuados pueden completar los pasos de configuración descritos en este tema. Los permisos específicos para cada paso se describen más adelante en este tema.

La preparación del acceso de usuario a la base de datos secundaria en línea de Replicación geográfica activa puede realizarse en cualquier momento. Consta de los tres pasos descritos a continuación:

1. Determinar los inicios de sesión con acceso a la base de datos principal;
2. Buscar al SID de estos inicios de sesión en el servidor de origen;
3. Cree los inicios de sesión en el servidor de destino con el SID correspondiente del servidor de origen.

>[AZURE.NOTE] Si los inicios de sesión en el servidor de destino no se asignan correctamente a los usuarios de la base de datos secundaria, el acceso a esta como base de datos de solo lectura o el acceso a la nueva base de datos principal después de la conmutación por error quedan limitados únicamente al administrador del servidor.

#### 1\. Determinar los inicios de sesión con acceso a la base de datos principal:
El primer paso del proceso es determinar qué inicios de sesión se deben duplicar en el servidor de destino. Esto se logra con un par de instrucciones SELECT, una en la base de datos maestra lógica del servidor de origen y otra, en la base de datos principal en sí.

El administrador del servidor o un miembro del rol de servidor **LoginManager** son los únicos que pueden determinar los inicios de sesión en el servidor de origen con la siguiente instrucción SELECT.

	SELECT [name], [sid] 
	FROM [sys].[sql_logins] 
	WHERE [type_desc] = 'SQL_Login'

Solo un miembro del rol de base de datos db\_owner, el usuario dbo o el administrador del servidor pueden determinar todas las entidades de seguridad de usuario de base de datos en la base de datos principal.

	SELECT [name], [sid]
	FROM [sys].[database_principals]
	WHERE [type_desc] = 'SQL_USER'

#### 2\. Buscar al SID de los inicios de sesión que identificó en el paso 1:
Al comparar los resultados de las consultas de la sección anterior y cotejar los SID, puede asignar el inicio de sesión de servidor a un usuario de base de datos. Los inicios de sesión que cuenten con un usuario de base de datos con un SID coincidente tienen acceso de usuario a esa base de datos como entidad de seguridad de usuario de base de datos.

La consulta siguiente se puede usar para ver todas las entidades de seguridad de usuario y sus SID en una base de datos. Solo un miembro del rol de servidor db\_owner o el administrador del servidor pueden ejecutar esta consulta.

	SELECT [name], [sid]
	FROM [sys].[database_principals]
	WHERE [type_desc] = 'SQL_USER'

>[AZURE.NOTE] Los usuarios de **sys** e **INFORMATION\_SCHEMA** tienen SID *NULL*, mientras que el SID de **guest** es **0x00**. El SID de **dbo** puede empezar por *0x01060000000001648000000000048454* si el creador de la base de datos era el administrador del servidor en lugar de un miembro de **DbManager**.

#### 3\. Crear los inicios de sesión en el servidor de destino:
El último paso consiste en ir al servidor o los servidores de destino y generar los inicios de sesión con los SID correspondientes. Esta es la sintaxis básica.

	CREATE LOGIN [<login name>]
	WITH PASSWORD = <login password>,
	SID = <desired login SID>

>[AZURE.NOTE] Si desea conceder acceso de usuario a la base de datos secundaria, pero no a la principal, puede hacerlo modificando el inicio de sesión de usuario en el servidor principal con la sintaxis siguiente.
>
>ALTER LOGIN <login name> DISABLE
>
>DISABLE no cambia la contraseña, por lo que siempre puede habilitarlo si es necesario.

## Pasos siguientes
Para más información sobre la replicación geográfica activa, vea [Replicación geográfica activa para Base de datos SQL de Azure](sql-database-geo-replication-overview.md).


## Recursos adicionales

- [Información general acerca de la continuidad del negocio](sql-database-business-continuity.md)
- [Replicación geográfica activa](sql-database-geo-replication-overview.md)
- [Diseño de aplicaciones para la recuperación ante desastres en la nube](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
- [Finalización de una base de datos SQL de Azure recuperada](sql-database-recovered-finalize.md)
- [P+F de BCDR de Base de datos SQL](sql-database-bcdr-faq.md)

<!---HONumber=AcomDC_0504_2016-->