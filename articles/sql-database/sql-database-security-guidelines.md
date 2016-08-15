<properties
   pageTitle="Instrucciones y limitaciones de seguridad de Base de datos SQL de Azure | Microsoft Azure"
   description="Obtenga más información acerca de las instrucciones y limitaciones de la Base de datos SQL de Microsoft Azure relacionadas con la seguridad."
   services="sql-database"
   documentationCenter=""
   authors="BYHAM"
   manager="jhubbard"
   editor=""
   tags=""/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="08/02/2016"
   ms.author="rickbyh"/>

# Instrucciones y limitaciones de seguridad de Base de datos SQL de Azure

En este tema se describen las instrucciones y limitaciones de la Base de datos SQL de Microsoft Azure relacionadas con la seguridad. Tenga en cuenta los siguientes puntos al administrar la seguridad de las Bases de datos SQL de Azure.

## Acceso a la base de datos maestra virtual

Normalmente, solo los administradores necesitan tener acceso a la base de datos maestra. El acceso rutinario a cada base de datos de usuario debe llevarse a cabo a través de los usuarios de la base de datos independiente que no son administradores creados en cada base de datos. Al utilizar los usuarios de la base de datos independiente, no es necesario que cree inicios de sesión en la base de datos maestra. Para obtener más información, vea [Usuarios de base de datos independiente - Conversión de la base de datos en portátil](https://msdn.microsoft.com/library/ff929188.aspx).


## Firewall

El firewall del servidor SQL limitado para Azure SQL Server en su totalidad se suele configurar a través del portal y debe admitir únicamente las direcciones IP que usan los administradores. Conéctese a una base de datos de usuario y use después la instrucción Transact-SQL [sp\_set\_database\_firewall\_rule](https://msdn.microsoft.com/library/dn270010.aspx) para crear una regla de firewall de ámbito de base de datos que abrirá el intervalo necesario de direcciones IP para cada base de datos.

El servicio de Base de datos SQL de Azure solo está disponible a través del puerto TCP 1433. Para tener acceso a una Base de datos SQL desde el equipo, asegúrese de que el firewall de equipos cliente permite la comunicación TCP saliente en el puerto TCP 1433. Si no es necesario para otras aplicaciones, bloquee las conexiones entrantes en el puerto TCP 1433.

Como parte del proceso de conexión, las conexiones de máquinas virtuales de Azure se redirigen a una dirección IP y un puerto diferente, único para cada rol de trabajo. El número de puerto estará comprendido en el rango del 11000 al 11999. Para más información sobre los puertos TCP, consulte [Puertos más allá de 1433 para ADO.NET 4.5 y Base de datos SQL V12](sql-database-develop-direct-route-ports-adonet-v12.md).


## Autenticación

Use la autenticación de Active Directory (seguridad integrada) siempre que sea posible. Para obtener información sobre cómo configurar la autenticación de AD, consulte [Conexión a Base de datos SQL mediante autenticación de Azure Active Directory](sql-database-aad-authentication.md) y [Elegir un modo de autenticación](https://msdn.microsoft.com/library/ms144284.aspx) en los Libros en pantalla de SQL Server.

Use los usuarios de la base de datos independiente para mejorar la escalabilidad. Para obtener más información, consulte [Usuarios de bases de datos independientes - Conversión de su base de datos en portátil](https://msdn.microsoft.com/library/ff929188.aspx), [CREATE USER (Transact-SQL)](https://technet.microsoft.com/library/ms173463.aspx) y [Bases de datos independientes](https://technet.microsoft.com/library/ff929071.aspx).

El motor de la base de datos cierra las conexiones que permanecen inactivas durante más de 30 minutos. La conexión debe volver a iniciar sesión para poder usarse.

Las conexiones con la Base de datos SQL que están constantemente activas requieren volver a ser autorizadas (acción realizada por el motor de la base de datos) al menos cada 10 horas. El motor de la base de datos intenta repetir la autorización con la contraseña que se envió inicialmente y no se requiere ninguna acción del usuario. Por motivos de rendimiento, cuando se restablece una contraseña en Base de datos SQL, la conexión no se volverá a autenticar, incluso si esta se restablece debido a la agrupación de conexiones. Esto difiere del comportamiento del SQL Server local. Si se ha cambiado la contraseña desde que se autorizó la conexión inicialmente, deberá terminarse la conexión y establecerse una nueva conexión con la nueva contraseña. Los usuarios con el permiso KILL DATABASE CONNECTION pueden terminar explícitamente una conexión con la Base de datos SQL mediante el uso del comando [KILL](https://msdn.microsoft.com/library/ms173730.aspx).

## Inicios de sesión y usuarios

Al administrar los inicios de sesión y los usuarios de la Base de datos SQL, hay restricciones.


- Debe conectarse a la base de datos **maestra** al ejecutar las instrucciones ``CREATE/ALTER/DROP DATABASE``. - El usuario de la base de datos principal correspondiente al inicio de sesión principal de nivel de servidor no se puede alterar ni quitar.
- El inglés de Estados Unidos es el idioma predeterminado del inicio de sesión principal de nivel de servidor.
- Para tener acceso a la base de datos **maestra**, cada inicio de sesión debe estar asignado a una cuenta de usuario en la base de datos **maestra**. La base de datos **maestra** no admite usuarios de base de datos independiente.
- Solamente el inicio de sesión principal y los miembros del nivel de servidor del rol de la base de datos **dbmanager** de la base de datos **maestra** tienen permiso para ejecutar las instrucciones ``CREATE DATABASE`` y ``DROP DATABASE``.
- Debe estar conectado a la base de datos maestra al ejecutar las instrucciones ``CREATE/ALTER/DROP LOGIN``. Sin embargo, el uso de inicios de sesión no es recomendable. En su lugar, utilice los usuarios de la base de datos independiente.
- Para conectarse a una base de datos de usuario, debe proporcionar el nombre de la base de datos de la cadena de conexión.
- Solamente el inicio de sesión principal del nivel de servidor y los miembros del rol de base de datos **loginmanager** de la base de datos **maestra** tienen permiso para ejecutar las instrucciones ``CREATE LOGIN``, ``ALTER LOGIN`` y ``DROP LOGIN``.
- Al ejecutar las instrucciones ``CREATE/ALTER/DROP LOGIN`` y ``CREATE/ALTER/DROP DATABASE`` en una aplicación ADO.NET, no se permite el uso de comandos con parámetros. Para obtener más información, consulte [Comandos y parámetros](https://msdn.microsoft.com/library/ms254953.aspx).
- Al ejecutar las instrucciones ``CREATE/ALTER/DROP DATABASE`` y ``CREATE/ALTER/DROP LOGIN``, cada una de ellas deben ser la única instrucción de un lote de Transact-SQL. De lo contrario, se produce un error. Por ejemplo, la instrucción Transact-SQL siguiente comprueba si existe la base de datos. Si existe, se llama a una instrucción ``DROP DATABASE`` para quitar la base de datos. Dado que la instrucción ``DROP DATABASE`` no es la única instrucción del lote, la ejecución de la siguiente instrucción Transact-SQL producirá un error.

```
IF EXISTS (SELECT [name]
           FROM   [sys].[databases]
           WHERE  [name] = N'database_name')
     DROP DATABASE [database_name];
GO
```

- Cuando se ejecuta la instrucción ``CREATE USER`` con la opción ``FOR/FROM LOGIN``, debe ser la única instrucción de un lote de Transact-SQL.
- Cuando se ejecuta la instrucción ``ALTER USER`` con la opción ``WITH LOGIN``, debe ser la única instrucción de un lote de Transact-SQL.
- Para ``CREATE/ALTER/DROP`` un usuario requiere el permiso ``ALTER ANY USER`` de la base de datos.
- Cuando el propietario de un rol de base de datos intenta agregar otro usuario de base de datos a ese rol o bien quitarlo, puede producirse el siguiente error: **El usuario o el rol “Nombre” no existe en la base de datos.** Este error se produce porque el usuario no es visible para el propietario. Para resolver este problema, conceda al propietario del rol el permiso ``VIEW DEFINITION`` del usuario.

Para obtener más información sobre los inicios de sesión y los usuarios, consulte [Administrar bases de datos e inicios de sesión en Base de datos SQL de Azure](sql-database-manage-logins.md).


## Consulte también

[Firewall de Base de datos SQL de Azure](sql-database-firewall-configure.md)

[Configuración del firewall (Base de datos SQL de Azure)](sql-database-configure-firewall-settings.md)

[Administrar bases de datos e inicios de sesión en Base de datos SQL de Azure](sql-database-manage-logins.md)

[Centro de seguridad para el Motor de base de datos de SQL Server y Base de datos SQL Azure](https://msdn.microsoft.com/library/bb510589)

<!---HONumber=AcomDC_0803_2016-->