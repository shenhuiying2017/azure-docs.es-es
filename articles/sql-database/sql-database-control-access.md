---
title: Control de acceso a Azure SQL Database | Microsoft Docs
description: "Obtenga información acerca de cómo controlar el acceso a Microsoft Azure SQL Database."
services: sql-database
documentationcenter: 
author: BYHAM
manager: jhubbard
editor: 
tags: 
ms.assetid: 8e71b04c-bc38-4153-8f83-f2b14faa31d9
ms.service: sql-database
ms.custom: overview
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 10/18/2016
ms.author: rickbyh
translationtype: Human Translation
ms.sourcegitcommit: 356cc4c6d8e25d36880e4b12bf471326e61990c3
ms.openlocfilehash: f12ed9d76e7c6db5e14ed3c00d7d4087dbd4069c


---
# <a name="azure-sql-database-access-control"></a>Control de acceso a Azure SQL Database
Para proporcionar seguridad, SQL Database controla el acceso con reglas de firewall que limitan la conectividad por dirección IP, con mecanismos de autenticación que requieren a los usuarios que demuestren su identidad y con mecanismos de autorización que limitan a los usuarios el acceso a datos y acciones específicos. 

> [!IMPORTANT]
> Para obtener información general de las características de seguridad de SQL Database, consulte [SQL security overview](sql-database-security-overview.md) (Información general acerca de la seguridad de SQL). Para ver un tutorial que use la autenticación de SQL Server, consulte [Tutorial de SQL Database: Autenticación, inicios de sesión y cuentas de usuario de SQL Server, roles de base de datos, permisos, reglas de firewall de nivel de servidor y de nivel de base de datos](sql-database-control-access-sql-authentication-get-started.md). Para ver un tutorial que use la autenticación de Azure Active Directory, consulte [Tutorial de SQL Database: Autenticación, inicios de sesión y cuentas de usuario de AAD, roles de base de datos, permisos, reglas de firewall de nivel de servidor y de nivel de base de datos](sql-database-control-access-aad-authentication-get-started.md).

>

## <a name="firewall-and-firewall-rules"></a>Firewall y reglas de firewall
Base de datos SQL de Microsoft Azure ofrece un servicio de base de datos relacional para Azure y otras aplicaciones basadas en Internet. Para ayudar a proteger los datos, los firewalls impiden todo acceso al servidor de bases de datos, excepto a aquellos equipos a los que haya concedido permiso. Asimismo, otorgan acceso a las bases de datos según la dirección IP de origen de cada solicitud. Para más información, consulte [Introducción a las reglas de firewall de Azure SQL Database](sql-database-firewall-configure.md).

El servicio de Base de datos SQL de Azure solo está disponible a través del puerto TCP 1433. Para tener acceso a una Base de datos SQL desde el equipo, asegúrese de que el firewall de equipos cliente permite la comunicación TCP saliente en el puerto TCP 1433. Si no es necesario para otras aplicaciones, bloquee las conexiones entrantes en el puerto TCP 1433. 

Como parte del proceso de conexión, las conexiones de máquinas virtuales de Azure se redirigen a una dirección IP y un puerto diferente, único para cada rol de trabajo. El número de puerto está comprendido en el rango del 11000 al 11999. Para más información sobre los puertos TCP, consulte [Puertos más allá de 1433 para ADO.NET 4.5 y Base de datos SQL V12](sql-database-develop-direct-route-ports-adonet-v12.md).

## <a name="authentication"></a>Autenticación

Base de datos SQL admite dos tipos de autenticación:

* **Autenticación de SQL**, que usa un nombre de usuario y una contraseña. Al crear el servidor lógico de la base de datos, especificó un inicio de sesión de "administrador de servidor" con un nombre de usuario y una contraseña. Con estas credenciales, puede autenticarse en cualquier base de datos en ese servidor como propietario de la base de datos, o "dbo". 
* **Autenticación de Azure Active Directory**, que usa las identidades administradas por Azure Active Directory y es compatible con dominios administrados e integrados. Use la autenticación de Active Directory (seguridad integrada) [siempre que sea posible](https://msdn.microsoft.com/library/ms144284.aspx). Si desea usar la autenticación de Azure Active Directory, debe crear otro administrador de servidor llamado "administrador de Azure AD" con permiso para administrar usuarios y grupos de Azure AD. Este administrador también puede realizar todas las operaciones de un administrador de servidor normal. Consulte el tutorial [Conectar a la Base de datos SQL mediante la autenticación de Azure Active Directory](sql-database-aad-authentication.md) , para obtener información acerca de cómo crear un administrador de Azure AD y así habilitar la autenticación de Azure Active Directory.

El motor de la base de datos cierra las conexiones que permanecen inactivas durante más de 30 minutos. La conexión debe volver a iniciar sesión antes de poder usarse. Las conexiones con SQL Database que están constantemente activas requieren volver a ser autorizadas (acción realizada por el motor de la base de datos) al menos cada 10 horas. El motor de la base de datos intenta repetir la autorización con la contraseña que se envió inicialmente y no se requiere ninguna acción del usuario. Por motivos de rendimiento, cuando se restablece una contraseña en SQL Database, la conexión no vuelve a autenticar, incluso si esta se restablece debido a la agrupación de conexiones. Esto difiere del comportamiento del SQL Server local. Si se ha cambiado la contraseña desde que se autorizó la conexión inicialmente, deberá terminarse la conexión y establecerse una nueva conexión con la nueva contraseña. Los usuarios con el permiso KILL DATABASE CONNECTION pueden terminar explícitamente una conexión con la Base de datos SQL mediante el uso del comando [KILL](https://msdn.microsoft.com/library/ms173730.aspx) .

Las cuentas de usuario se pueden crear en la base de datos maestra y se pueden conceder permisos en todas las bases de datos del servidor, o se pueden crear en la misma base de datos (se denominan usuarios de bases de datos independientes). Para más información sobre cómo crear y administrar inicios de sesión, consulte [Administración de inicios de sesión](sql-database-manage-logins.md). Para mejorar la portabilidad y escalabilidad utilice usuarios de bases de datos independientes. Para más información sobre los usuarios de bases de datos independientes, consulte [Usuarios de bases de datos independientes: llévense sus bases de datos donde quieran](https://msdn.microsoft.com/library/ff929188.aspx), [CREATE USER (Transact-SQL)](https://technet.microsoft.com/library/ms173463.aspx) y [Bases de datos independientes](https://technet.microsoft.com/library/ff929071.aspx).

Como procedimiento recomendado, la aplicación debe usar una cuenta distinta para autenticar. De esta forma, puede limitar los permisos concedidos a la aplicación y reducir los riesgos de actividad malintencionada en caso de que el código de la aplicación sea vulnerable a ataques por inyección de código SQL. Se recomienda crear un [usuario de base de datos independiente](https://msdn.microsoft.com/library/ff929188), ya que esto permitirá que la aplicación se autentique directamente en la base de datos. Para crear un usuario de base de datos independiente que utilice la autenticación de SQL, ejecute el comando de T-SQL siguiente cuando haya iniciado sesión como administrador de servidor en la base de datos de usuario a la que está conectado:

## <a name="authorization"></a>Autorización

Autorización indica las acciones que pueden realizar los usuarios en Azure SQL Database, algo que controlan las [pertenencias a roles](https://msdn.microsoft.com/library/ms189121) y los [permisos de nivel de objeto](https://msdn.microsoft.com/library/ms191291.aspx) de las bases de datos de la cuenta de usuario. Como procedimiento recomendado, debe conceder a los usuarios los privilegios mínimos necesarios. La cuenta de administrador de servidor con la que se está conectando forma parte de db_owner, que tiene autoridad para realizar cualquier acción en la base de datos. Guarde esta cuenta para implementar las actualizaciones de los esquemas y otras operaciones de administración. Utilice la cuenta "ApplicationUser" con permisos más limitados para conectarse desde la aplicación a la base de datos con los privilegios mínimos que necesita la aplicación. Para más información, consulte [Administración de inicios de sesión](sql-database-manage-logins.md).

Normalmente, solo los administradores necesitan tener acceso a la base de datos maestra. El acceso rutinario a cada base de datos de usuario debe llevarse a cabo a través de los usuarios de la base de datos independiente que no son administradores creados en cada base de datos. Al utilizar los usuarios de la base de datos independiente, no es necesario que cree inicios de sesión en la base de datos maestra. Para obtener más información, vea [Usuarios de base de datos independiente - Conversión de la base de datos en portátil](https://msdn.microsoft.com/library/ff929188.aspx).

Además, estas características se pueden utilizar para limitar o elevar los permisos.

* La [suplantación](https://msdn.microsoft.com/library/vstudio/bb669087) y la [firma de módulos](https://msdn.microsoft.com/library/bb669102) se pueden usar para elevar los permisos temporalmente de forma segura.
* [seguridad del nivel de fila](https://msdn.microsoft.com/library/dn765131) permite filtrar las filas que puede ver el usuario.
* [enmascaramiento de datos](sql-database-dynamic-data-masking-get-started.md) puede utilizarse para limitar la exposición de información confidencial.
* [procedimientos almacenados](https://msdn.microsoft.com/library/ms190782) puede utilizarse para limitar las acciones que se pueden realizar en la base de datos.

## <a name="next-steps"></a>Pasos siguientes

- Para obtener información general de todas los características de seguridad de SQL Database, consulte [SQL security overview](sql-database-security-overview.md) (Información general acerca de la seguridad de SQL).
- Para más información acerca de las reglas de firewall, consulte [Firewall de Azure SQL Database](sql-database-firewall-configure.md).
- Para obtener información acerca de los usuarios e inicios de sesión, consulte [Administración de inicios de sesión](sql-database-manage-logins.md). 
- Para información sobre el uso de las características de protección de datos en SQL Database, consulte [Protecting data within your SQL Database](sql-database-protect-data.md) (Protección de datos en SQL Database).
- Para obtener información acerca de la supervisión proactiva, consulte [Introducción a SQL Database Auditing](sql-database-auditing-get-started.md) e [Introducción a la detección de amenazas de SQL Database](sql-database-threat-detection-get-started.md).
- Para ver un tutorial que use la autenticación de SQL Server, consulte [Tutorial de SQL Database: Autenticación, inicios de sesión y cuentas de usuario de SQL Server, roles de base de datos, permisos, reglas de firewall de nivel de servidor y de nivel de base de datos](sql-database-control-access-sql-authentication-get-started.md).
- Para ver un tutorial que use la autenticación de Azure Active Directory, consulte [Tutorial de SQL Database: Autenticación, inicios de sesión y cuentas de usuario de AAD, roles de base de datos, permisos, reglas de firewall de nivel de servidor y de nivel de base de datos](sql-database-control-access-aad-authentication-get-started.md).



<!--HONumber=Jan17_HO3-->


