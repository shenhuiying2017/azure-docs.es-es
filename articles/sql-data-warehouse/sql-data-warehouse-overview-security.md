<properties
   pageTitle="Proteger una base de datos en Almacenamiento de datos SQL | Microsoft Azure"
   description="Sugerencias para proteger una base de datos en Almacenamiento de datos SQL de Azure para desarrollar soluciones."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sahaj08"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/22/2015"
   ms.author="sahajs"/>

# Proteger una base de datos en Almacenamiento de datos SQL

En este artículo se describen los fundamentos de la protección de una base de datos de Almacenamiento de datos SQL de Azure. En concreto, este artículo le ayudará a empezar a trabajar con los recursos para limitar el acceso, proteger los datos y supervisar las actividades en una base de datos.

## Seguridad de conexión

Seguridad de conexión hace referencia a cómo restringir y proteger las conexiones a la base de datos mediante reglas de firewall y cifrado de las conexiones.

Las reglas de firewall las usan tanto el servidor como la base de datos para rechazar los intentos de conexión desde direcciones IP que no se hayan incluido explícitamente en la lista blanca. Para permitir que la aplicación o la dirección IP pública del equipo cliente intente conectarse a una nueva base de datos, primero debe crear una regla de firewall de nivel de servidor mediante el Portal de administración de Azure, API de REST o PowerShell. Como práctica recomendada, debe restringir los intervalos de direcciones IP que se permite que atraviesen el firewall del servidor tanto como sea posible. Para obtener más información, vea [Firewall de Base de datos SQL de Azure][].


## Autenticación

La autenticación indica a cómo demostrar su identidad al conectarse a la base de datos. Actualmente, Almacenamiento de datos SQL admite la autenticación de SQL con un nombre de usuario y una contraseña.

Al crear el servidor lógico de la base de datos, especificó un inicio de sesión de "administrador de servidor" con un nombre de usuario y una contraseña. Con estas credenciales, puede autenticarse en cualquier base de datos en ese servidor como propietario de la base de datos, o "dbo".

Sin embargo, como práctica recomendada los usuarios de la organización deben usar una cuenta diferente para autenticarse; de este modo, se pueden limitar los permisos concedidos a la aplicación y reducir los riesgos de actividad malintencionada en caso de que el código de la aplicación sea vulnerable a ataques de inyección SQL. El enfoque recomendado es crear un usuario de base de datos contenido, lo que permite a la aplicación autenticarse directamente en una única base de datos con un nombre de usuario y una contraseña. Puede crear un usuario de base de datos contenido mediante la ejecución del siguiente comando T-SQL mientras está conectado a su base de datos de usuario con el inicio de sesión de administrador de servidor:

```
CREATE USER ApplicationUser WITH PASSWORD = 'strong_password';
```

Para obtener más información sobre la autenticación en Base de datos SQL, consulte [Administrar bases de datos e inicios de sesión en Base de datos SQL de Azure][].


## Autorización

La autorización hace referencia a lo que se puede hacer en la base de datos de Almacenamiento de datos SQL de Azure, y la controlan los permisos y las pertenencias del rol de la cuenta de usuario. Como procedimiento recomendado, debe conceder a los usuarios los privilegios mínimos necesarios. Almacenamiento de datos SQL de Azure facilita la administración con roles en T-SQL:

```
ALTER ROLE db_datareader ADD MEMBER ApplicationUser; -- allows ApplicationUser to read data
ALTER ROLE db_datawriter ADD MEMBER ApplicationUser; -- allows ApplicationUser to write data
```

La cuenta de administrador de servidor con la que se está conectando forma parte de db\_owner, que tiene autoridad para realizar cualquier acción en la base de datos. Guarde esta cuenta para implementar las actualizaciones de los esquemas y otras operaciones de administración. Utilice la cuenta "ApplicationUser" con permisos más limitados para conectarse desde la aplicación a la base de datos con los privilegios mínimos que necesita la aplicación.

Existen formas para limitar aún más lo que un usuario puede hacer con Base de datos SQL de Azure:- pueden utilizarse [roles de base de datos][] distintos de db\_datareader y db\_datawriter para crear cuentas de usuario de aplicaciones más eficaces o cuentas de administración menos eficaces. - Granular los [permisos][] permite controlar qué operaciones se pueden realizar en columnas, tablas, vistas, procedimientos y otros objetos de la base de datos. - Los [procedimientos almacenados][] se pueden usar para limitar las acciones que se pueden realizar en la base de datos.

La administración bases de datos y servidores lógicos desde el Portal de administración de Azure o mediante la API del Administrador de recursos de Azure la controlan las asignaciones de roles de su cuenta de usuario del portal. Para obtener más información sobre este tema, consulte [Control de acceso basado en roles en el Portal de vista previa de Azure][].


## Pasos siguientes
Para obtener más sugerencias sobre desarrollo, consulte la [información general sobre desarrollo][].

<!--Image references-->

<!--Article references-->
[información general sobre desarrollo]: sql-data-warehouse-overview-develop.md


<!--MSDN references-->
[Firewall de Base de datos SQL de Azure]: https://msdn.microsoft.com/library/ee621782.aspx
[roles de base de datos]: https://msdn.microsoft.com/library/ms189121.aspx
[Administrar bases de datos e inicios de sesión en Base de datos SQL de Azure]: https://msdn.microsoft.com/library/ee336235.aspx
[permisos]: https://msdn.microsoft.com/library/ms191291.aspx
[procedimientos almacenados]: https://msdn.microsoft.com/library/ms190782.aspx
[Transparent Data Encryption]: http://go.microsoft.com/fwlink/?linkid=526242&clcid=0x409

<!--Other Web references-->
[Control de acceso basado en roles en el Portal de vista previa de Azure]: http://azure.microsoft.com/documentation/articles/role-based-access-control-configure.aspx

<!---HONumber=August15_HO6-->