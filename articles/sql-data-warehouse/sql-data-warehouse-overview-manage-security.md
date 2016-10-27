<properties
   pageTitle="Proteger una base de datos en Almacenamiento de datos SQL | Microsoft Azure"
   description="Sugerencias para proteger una base de datos en Almacenamiento de datos SQL de Azure para desarrollar soluciones."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="ronortloff"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/24/2016"
   ms.author="rortloff;barbkess;sonyama"/>


# <a name="secure-a-database-in-sql-data-warehouse"></a>Proteger una base de datos en Almacenamiento de datos SQL

> [AZURE.SELECTOR]
- [Información general sobre seguridad](sql-data-warehouse-overview-manage-security.md)
- [Autenticación](sql-data-warehouse-authentication.md)
- [Cifrado (Portal)](sql-data-warehouse-encryption-tde.md)
- [Cifrado (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)

En este artículo se describen los fundamentos de la protección de una base de datos de Almacenamiento de datos SQL de Azure. En concreto, este artículo le ayudará a empezar a trabajar con los recursos para limitar el acceso, proteger los datos y supervisar las actividades en una base de datos.

## <a name="connection-security"></a>Seguridad de conexión

Seguridad de conexión hace referencia a cómo restringir y proteger las conexiones a la base de datos mediante reglas de firewall y cifrado de las conexiones.

Las reglas de firewall las usan tanto el servidor como la base de datos para rechazar los intentos de conexión desde direcciones IP que no se hayan incluido explícitamente en la lista de permitidos. Para permitir conexiones desde la dirección IP pública de la máquina cliente o de la aplicación, primero debe crear una regla de firewall de nivel de servidor mediante Azure Portal, la API de REST o PowerShell. Como práctica recomendada, debe restringir los intervalos de direcciones IP que se permite que atraviesen el firewall del servidor tanto como sea posible.  Para obtener acceso a Almacenamiento de datos SQL de Azure desde el equipo local, asegúrese de que el firewall de su red y el equipo local permiten la comunicación saliente en el puerto TCP 1433.  Para más información, consulte [Firewall de Azure SQL Database][], [sp_set_firewall_rule][] y [sp_set_database_firewall_rule][].

Las conexiones a su instancia de SQL Data Warehouse se cifran de forma predeterminada.  Se pasa por alto la modificación de la configuración de conexión para deshabilitar el cifrado.

## <a name="authentication"></a>Autenticación

La autenticación indica a cómo demostrar su identidad al conectarse a la base de datos. Actualmente, Almacenamiento de datos SQL admite la autenticación de SQL Server con un nombre de usuario y una contraseña, además de Azure Active Directory. 

Al crear el servidor lógico de la base de datos, especificó un inicio de sesión de "administrador de servidor" con un nombre de usuario y una contraseña. Con estas credenciales, puede autenticarse en cualquier base de datos de ese servidor como propietario, o "dbo" a través de la autenticación en SQL Server.

Sin embargo, como práctica recomendada, los usuarios de su organización deben usar una cuenta diferente para autenticarse. De esta manera puede limitar los permisos concedidos a la aplicación y reducir los riesgos de actividad malintencionada en caso de que el código de aplicación sea vulnerable a ataques de inyección SQL. 

Para crear un usuario autenticado de SQL Server, conéctese a la base de datos **maestra** en el servidor con su inicio de sesión de administrador de servidor y cree un nuevo inicio de sesión de servidor.  Además, es una buena idea crear un usuario en la base de datos maestra para los usuarios de Almacenamiento de datos SQL de Azure. La creación de un usuario en la base de datos maestra posibilita el inicio de sesión mediante herramientas como SSMS sin especificar un nombre de base de datos.  También permite el uso del Explorador de objetos para ver todas las bases de datos en un servidor SQL Server.

```sql
-- Connect to master database and create a login
CREATE LOGIN ApplicationLogin WITH PASSWORD = 'Str0ng_password';
CREATE USER ApplicationUser FOR LOGIN ApplicationLogin;
```

Después, conéctese a la base de datos de **Almacenamiento de datos SQL** con el inicio de sesión de administrador de servidor y cree un usuario de base de datos basado en el inicio de sesión de servidor que acaba de crear.

```sql
-- Connect to SQL DW database and create a database user
CREATE USER ApplicationUser FOR LOGIN ApplicationLogin;
```

Si un usuario va a realizar operaciones adicionales como la creación de inicios de sesión o de nuevas bases de datos, también necesitará que se le asignen los roles `Loginmanager` y `dbmanager` en la base de datos maestra. Para obtener más información sobre la autenticación en Base de datos SQL, consulte [Autorización y autenticación de Base de datos SQL: concesión de acceso][].  Para obtener más información sobre Azure AD para Almacenamiento de datos SQL, consulte [Conexión a Base de datos SQL o a Almacenamiento de datos SQL mediante autenticación de Azure Active Directory][].


## <a name="authorization"></a>Autorización

La autorización hace referencia a lo que se puede hacer en la base de datos de Almacenamiento de datos SQL de Azure, y la controlan los permisos y las pertenencias del rol de la cuenta de usuario. Como procedimiento recomendado, debe conceder a los usuarios los privilegios mínimos necesarios. Almacenamiento de datos SQL de Azure facilita la administración con roles en T-SQL:

```sql
EXEC sp_addrolemember 'db_datareader', 'ApplicationUser'; -- allows ApplicationUser to read data
EXEC sp_addrolemember 'db_datawriter', 'ApplicationUser'; -- allows ApplicationUser to write data
```

La cuenta de administrador de servidor con la que se está conectando forma parte de db_owner, que tiene autoridad para realizar cualquier acción en la base de datos. Guarde esta cuenta para implementar las actualizaciones de los esquemas y otras operaciones de administración. Utilice la cuenta "ApplicationUser" con permisos más limitados para conectarse desde la aplicación a la base de datos con los privilegios mínimos que necesita la aplicación.

Existen varias formas de limitar aún más lo que los usuarios pueden hacer con Base de datos SQL de Azure:

- Los [permisos][] granulares permiten control qué operaciones se pueden realizar en columnas individuales, tablas, vistas, procedimientos y otros objetos de la base de datos. Use los permisos granulares para tener el máximo control y conceder los permisos mínimos necesarios. El sistema de permisos granulares es complicado y requerirá un estudio para usarlo de forma eficaz.
- Los [roles de base de datos][] que no sean db_datareader y db_datawriter se pueden usar para crear cuentas de usuario de aplicación más eficaces o cuentas de administración menos eficaces. Los roles d base de datos fijos integrados proporcionan una manera fácil de conceder permisos, pero pueden dar lugar a la concesión de más permisos que son necesarios.
- [procedimientos almacenados][] puede utilizarse para limitar las acciones que se pueden realizar en la base de datos.

La administración de bases de datos y servidores lógicos desde el Portal de Azure clásico o mediante la API del Administrador de recursos de Azure la controlan las asignaciones de roles de su cuenta de usuario del portal. Para obtener más información sobre este tema, consulte [Control de acceso basado en rol en el Portal de Azure][].

## <a name="encryption"></a>Cifrado

El Cifrado de datos transparente (TDE) de Azure SQL Data Warehouse ayuda a proteger frente a las amenazas de actividad malintencionada al realizar el cifrado y el descifrado en tiempo real de los datos en reposo.  Cuando se cifra la base de datos, los archivos de registro de copias de seguridad y transacciones asociados se cifran sin necesidad de realizar ningún cambio en las aplicaciones. TDE cifra el almacenamiento de una base de datos completa mediante el uso de una clave simétrica denominada clave de cifrado de base de datos. En Base de datos SQL la clave de cifrado de base de datos está protegida por un certificado de servidor integrado. El certificado de servidor integrado es único para cada servidor de Base de datos SQL. Microsoft alterna automáticamente estos certificados al menos cada 90 días. El algoritmo de cifrado usado por Almacenamiento de datos SQL es AES-256. Para ver una descripción general de TDE, consulte [Cifrado de datos transparente (TDE)][].

Puede cifrar la base de datos mediante [Azure Portal][ifrado con el portal de] o [T-SQL][ifrado con TSQL].

## <a name="next-steps"></a>Pasos siguientes

Para obtener detalles y ejemplos sobre la conexión de Almacenamiento de datos SQL con diferentes protocolos, consulte [Conexión a Almacenamiento de datos SQL][].

<!--Image references-->

<!--Article references-->
[Conexión a Almacenamiento de datos SQL]: ./sql-data-warehouse-connect-overview.md
[Cifrado con el portal]: ./sql-data-warehouse-encryption-tde.md
[Cifrado con TSQL]: ./sql-data-warehouse-encryption-tde-tsql.md
[Conexión a Base de datos SQL o a Almacenamiento de datos SQL mediante autenticación de Azure Active Directory]: ./sql-data-warehouse-authentication.md

<!--MSDN references-->
[Firewall de Base de datos SQL de Azure]: https://msdn.microsoft.com/library/ee621782.aspx
[sp_set_firewall_rule]: https://msdn.microsoft.com/library/dn270017.aspx
[sp_set_database_firewall_rule]: https://msdn.microsoft.com/library/dn270010.aspx
[roles de base de datos]: https://msdn.microsoft.com/library/ms189121.aspx
[Administrar bases de datos e inicios de sesión en Base de datos SQL de Azure]: https://msdn.microsoft.com/library/ee336235.aspx
[permisos]: https://msdn.microsoft.com/library/ms191291.aspx
[procedimientos almacenados]: https://msdn.microsoft.com/library/ms190782.aspx
[Cifrado de datos transparente (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx
[Portal de Azure]: https://portal.azure.com/

<!--Other Web references-->
[Control de acceso basado en rol en el Portal de Azure]: https://azure.microsoft.com/documentation/articles/role-based-access-control-configure



<!--HONumber=Oct16_HO2-->


