---
title: "Autenticación de AAD: Firewalls, autenticación y acceso a Azure SQL Database | Microsoft Docs"
description: "En esta guía de procedimientos, puede obtener información sobre cómo usar SQL Server Management Studio y Transact-SQL para trabajar con reglas de firewall de nivel de servidor y de nivel de base de datos, y la autenticación, los inicios de sesión, usuarios y roles de Azure Active Directory para conceder acceso y control a los servidores y las bases de datos de Azure SQL Database."
keywords: 
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 67797b09-f5c3-4ec2-8494-fe18883edf7f
ms.service: sql-database
ms.custom: security
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/17/2017
ms.author: carlrab
ms.translationtype: Human Translation
ms.sourcegitcommit: e851a3e1b0598345dc8bfdd4341eb1dfb9f6fb5d
ms.openlocfilehash: ca679a820eefc7acbb08eed6b8f809f46aacd3a3
ms.contentlocale: es-es
ms.lasthandoff: 04/15/2017


---
# <a name="azure-ad-authentication-access-and-database-level-firewall-rules"></a>Autenticación de Azure AD, acceso y reglas de firewall de nivel de base de datos
En esta guía de procedimientos, puede obtener información sobre cómo usar SQL Server Management Studio para trabajar con la autenticación, los inicios de sesión, los usuarios y roles de base de datos de Azure Active Directory que conceden acceso y permisos a los servidores y bases de datos de Azure SQL Database. Aprenderá a:

- Ver los permisos de usuario en la base de datos maestra y en las bases de datos de usuario
- Crear inicios de sesión y usuarios basados en la autenticación de Azure Active Directory
- Conceder permisos específicos de base de datos para todo el servidor a los usuarios
- Iniciar sesión en una base de datos de usuario como un usuario sin derechos de administrador
- Crear reglas de firewall de nivel de base de datos para los usuarios de la base de datos
- Crear reglas de firewall de nivel de servidor para los administradores de este

**Estimación del tiempo**: esta guía de procedimientos dura aproximadamente 45 minutos (suponiendo que ya se hayan cumplido los requisitos previos).

## <a name="prerequisites"></a>Requisitos previos

* **Una cuenta de Azure**. Necesitará una cuenta de Azure. Puede [abrir una cuenta gratuita de Azure](https://azure.microsoft.com/free/) o [activar las ventajas que disfrutan los suscriptores de Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits/). 

* **Permisos de creación de Azure**. Debe poder conectarse a Azure Portal mediante una cuenta que sea miembro del rol de colaborador o propietario de la suscripción. Para más información sobre el acceso basado en roles (RBAC), consulte [Introducción a la administración de acceso en Azure Portal](../active-directory/role-based-access-control-what-is.md).

* **SQL Server Management Studio**. Puede descargar e instalar la versión de SQL Server Management Studio (SSMS) más reciente de [Descarga de SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx). Utilice siempre la versión más reciente de SSMS al conectarse a Azure SQL Database, ya que se lanzan funcionalidades nuevas constantemente.

* **Servidor de bases y bases de datos**. Para instalar y configurar un servidor y las dos bases de datos que se usan en esta guía de procedimientos, haga clic en el botón **Implementar en Azure**. Al hacer clic en el botón se abre la hoja **Deploy from a template** (Implementar desde una plantilla); cree un nuevo grupo de recursos y proporcione la **Admin Login Password** (Contraseña de inicio de sesión del administrador) para el nuevo servidor que va a crear:

   [![descarga](http://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fsqldbtutorial.blob.core.windows.net%2Ftemplates%2Fsqldbgetstarted.json)

   > [!NOTE]
   > La aplicación de la guía de procedimientos relacionada para la autenticación de SQL Server, [SQL authentication, logins and user accounts, database roles, permissions, server-level firewall rules, and database-level firewall rules](sql-database-control-access-sql-authentication-get-started.md) (Autenticación, inicios de sesión y cuentas de usuario de SQL, roles de base de datos, permisos, reglas de firewall de nivel de servidor y de nivel de base de datos), es opcional. Sin embargo, hay conceptos que se tratan en esa guía y que no se repiten aquí. Los procedimientos de esta guía relacionados con las reglas de firewall de nivel de servidor y de nivel de base de datos no son obligatorios si ya ha completado esta guía de procedimientos relacionada en los mismos equipos (con las mismas direcciones IP) y están marcados como opcionales por esa razón. Igualmente, en las capturas de pantalla de esta guía de procedimientos se supone que ya ha completado esta guía de procedimientos relacionada. 
   >

* Ha creado y rellenado una instancia de Azure Active Directory. Para obtener más información, consulte [Integrating your on-premises identities with Azure Active Directory](../active-directory/active-directory-aadconnect.md) (Integración de sus identidades locales con Azure Active Directory), [Incorporación de su nombre de dominio personalizado a Azure Active Directory](../active-directory/active-directory-add-domain.md), [Microsoft Azure now supports federation with Windows Server Active Directory](https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/) (Microsoft Azure admite ahora Windows Server Active Directory), [Administración de su directorio de Azure AD](https://msdn.microsoft.com/library/azure/hh967611.aspx), [Administrar Azure AD mediante Windows PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx) y [Hybrid Identity Required Ports and Protocols](../active-directory/active-directory-aadconnect-ports.md) (Puertos y protocolos requeridos para la identidad híbrida).

> [!NOTE]
> Esta guía de procedimientos le servirá para conocer el contenido de estos temas: [Control de acceso a Azure SQL Database](sql-database-control-access.md), [Control y concesión de acceso a bases de datos](sql-database-manage-logins.md), [Entidades de seguridad](https://msdn.microsoft.com/library/ms181127.aspx), [Roles de nivel de base de datos](https://msdn.microsoft.com/library/ms189121.aspx), [Reglas de firewall de Azure SQL Database](sql-database-firewall-configure.md) y [Autenticación de Azure Active Directory](sql-database-aad-authentication.md). 
>  

## <a name="sign-in-to-the-azure-portal-using-your-azure-account"></a>Inicie sesión en Azure Portal con su cuenta.
Con una [suscripción existente](https://account.windowsazure.com/Home/Index), siga estos pasos para conectarse al portal de Azure.

1. Abra el explorador que prefiera y conéctese al [Portal de Azure](https://portal.azure.com/).
2. Inicie sesión en el [Portal de Azure](https://portal.azure.com/).
3. En la página **Iniciar sesión** , proporcione las credenciales de la suscripción.
   
## <a name="provision-an-azure-active-directory-admin-for-your-sql-logical-server"></a>Aprovisionamiento de un administrador de Azure Active Directory para el servidor lógico de SQL

En esta sección de la guía de procedimientos, puede ver información sobre la configuración de seguridad del servidor lógico en Azure Portal.

1. Abra la hoja **SQL Server** del servidor lógico y lea la información de la página de **introducción**. Tenga en cuenta que no se ha configurado un administrador de Azure Active Directory.

   ![Cuenta de administrador del servidor en Azure Portal](./media/sql-database-control-access-aad-authentication-get-started/sql_admin_portal.png)

2. Haga clic en **No configurado** en el panel **Información esencial** para abrir la hoja **Administrador de Active Directory**.

   ![Hoja de AAD](./media/sql-database-control-access-aad-authentication-get-started/aad_blade.png)

3. Haga clic en **Establecer administrador** para que se abra la hoja **Agregar administrador** y, a continuación, seleccione una cuenta de usuario o grupo de Active Directory como administrador de Active Directory para el servidor.

   ![Seleccione la cuenta de administrador de AAD](./media/sql-database-control-access-aad-authentication-get-started/aad_admin.png)

4. Haga clic en **Seleccionar** y después en **Guardar**.

   ![Guardar la cuenta de administrador de AAD seleccionada](./media/sql-database-control-access-aad-authentication-get-started/aad_admin_save.png)

> [!NOTE]
> Para revisar la información de conexión de este servidor, vaya a [Conexión con SSMS](sql-database-connect-query-ssms.md). Para esta serie de la guía de procedimientos, el nombre completo del servidor es "sqldbtutorialserver.database.windows.net".
>

## <a name="connect-to-sql-server-using-sql-server-management-studio-ssms"></a>Conexión con SQL Server mediante SQL Server Management Studio (SSMS)

1. Si aún no lo ha hecho descargue e instale la versión más reciente de SSMS en [Descarga de SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx). Para estar siempre actualizado, la versión más reciente de SSMS le avisará cuando haya una nueva versión disponible para su descarga.

2. Después de la instalación, escriba **Microsoft SQL Server Management Studio** en el cuadro de búsqueda de Windows y haga clic en **Entrar** para abrir SSMS.

   ![SQL Server Management Studio](./media/sql-database-connect-query-ssms/ssms.png)

3. En el cuadro de diálogo **Conectar al servidor**, seleccione uno de los métodos de autenticación de Active Directory y, a continuación, proporcione la información de autenticación adecuada. Para obtener información sobre cómo elegir un método, consulte [Autenticación con Azure Active Directory](sql-database-aad-authentication.md) y [SSMS support for Azure AD MFA](sql-database-ssms-mfa-authentication.md) (Compatibilidad con SSMS de Azure AD MFA).

   ![conectarse al servidor con aad](./media/sql-database-control-access-aad-authentication-get-started/connect_to_server_with_aad.png)

4. Escriba la información necesaria para conectarse a su servidor SQL Server mediante la autenticación de SQL Server y la cuenta de administrador del servidor.

5. Haga clic en **Conectar**.

   ![conectado al servidor con aad](./media/sql-database-control-access-aad-authentication-get-started/connected_to_server_with_aad.png)

## <a name="view-the-server-admin-account-and-its-permissions"></a>Visualización de la cuenta de administrador del servidor y sus permisos 
En esta sección de la guía de procedimientos, puede ver información sobre la cuenta de administrador del servidor y sus permisos en la base de datos maestra y en las bases de datos de usuario.

1. En el Explorador de objetos, expanda **Bases de datos**, expanda **Bases de datos de sistema**, expanda **Maestra**, expanda **Seguridad** y, finalmente, expanda **Usuarios**. Tenga en cuenta que se ha creado una cuenta de usuario en la base de datos maestra para el administrador de Active Directory. Observe también que no se ha creado ningún inicio de sesión para la cuenta de usuario de administrador de Active Directory.

   ![cuenta de usuario de base de datos maestra para el administrador de AAD](./media/sql-database-control-access-aad-authentication-get-started/master_database_user_account_for_AAD_admin.png)

   > [!NOTE]
   > Para más información acerca de las cuentas de usuario que aparecen, consulte [Entidades de seguridad](https://msdn.microsoft.com/library/ms181127.aspx).
   >

2. En el Explorador de objetos, haga clic con el botón derecho en **Maestra** y, a continuación, haga clic en **Nueva consulta** para abrir una ventana de consulta conectada a la base de datos maestra.
3. En la ventana de consulta, ejecute la siguiente consulta para que devuelva información sobre el usuario que ejecuta la consulta. Tenga en cuenta que se devuelve user@microsoft.com para la cuenta de usuario que ejecuta esta consulta (se obtendrá un resultado diferente cuando se consulte una base de datos de usuario más adelante en este procedimiento).

   ```
   SELECT USER;
   ```

   ![seleccionar consulta de usuario en la base de datos maestra](./media/sql-database-control-access-aad-authentication-get-started/select_user_query_in_master_database.png)

4. En la ventana de consulta, ejecute la siguiente consulta para que devuelva información sobre los permisos del usuario administrador de Active Directory. Observe que el usuario administrador de Active Directory tiene permisos para conectarse a la base de datos maestra, crear inicios de sesión y usuarios, seleccionar la información de la tabla sys.sql_logins y agregar usuarios a los roles de base de datos dbmanager y dbcreator. Estos permisos se suman a los permisos concedidos al rol público desde el que todos los usuarios heredan permisos (como los permisos para seleccionar información de determinadas tablas). Para más información, consulte [Permisos](https://msdn.microsoft.com/library/ms191291.aspx).

   ```
   SELECT prm.permission_name
      , prm.class_desc
      , prm.state_desc
      , p2.name as 'Database role'
      , p3.name as 'Additional database role' 
   FROM sys.database_principals p
   JOIN sys.database_permissions prm
      ON p.principal_id = prm.grantee_principal_id
      LEFT JOIN sys.database_principals p2
      ON prm.major_id = p2.principal_id
      LEFT JOIN sys.database_role_members r
      ON p.principal_id = r.member_principal_id
      LEFT JOIN sys.database_principals p3
      ON r.role_principal_id = p3.principal_id
   WHERE p.name = 'user@microsoft.com';
   ```

   ![permisos de administrador de AAD en la base de datos maestra](./media/sql-database-control-access-aad-authentication-get-started/aad_admin_permissions_in_master_database.png)

6. En el Explorador de objetos, expanda **blankdb**, expanda **Seguridad** y, finalmente, expanda **Usuarios**. Observe que no hay ninguna cuenta de usuario denominada user@microsoft.com en esta base de datos.

   ![cuentas de usuario en blankdb](./media/sql-database-control-access-aad-authentication-get-started/user_accounts_in_blankdb.png)

7. En el Explorador de objetos, haga clic con el botón derecho en **blankdb** y, después, haga clic en **Nueva consulta**.

8. En la ventana de consulta, ejecute la siguiente consulta para que devuelva información sobre el usuario que ejecuta la consulta. Observe que se devuelve dbo para la cuenta de usuario que ejecuta esta consulta (de forma predeterminada, el inicio de sesión de administrador del servidor se asigna a la cuenta de usuario de dbo de cada base de datos de usuario).

   ```
   SELECT USER;
   ```

   ![seleccionar consulta de usuario en la base de datos blankdb](./media/sql-database-control-access-aad-authentication-get-started/select_user_query_in_blankdb_database.png)

9. En la ventana de consulta, ejecute la siguiente consulta para que devuelva información sobre los permisos del usuario dbo. Observe que dbo es un miembro del rol público y también un miembro del rol fijo de base de datos db_owner. Consulte [Roles de nivel de base de datos](https://msdn.microsoft.com/library/ms189121.aspx) para más información.

   ```
   SELECT prm.permission_name
      , prm.class_desc
      , prm.state_desc
      , p2.name as 'Database role'
      , p3.name as 'Additional database role' 
   FROM sys.database_principals AS p
   JOIN sys.database_permissions AS prm
      ON p.principal_id = prm.grantee_principal_id
      LEFT JOIN sys.database_principals AS p2
      ON prm.major_id = p2.principal_id
      LEFT JOIN sys.database_role_members r
      ON p.principal_id = r.member_principal_id
      LEFT JOIN sys.database_principals AS p3
      ON r.role_principal_id = p3.principal_id
   WHERE p.name = 'dbo';
   ```

   ![permisos de administrador de servidor en la base de datos blankdb](./media/sql-database-control-access-aad-authentication-get-started/aad_admin_permissions_in_blankdb_database.png)

10. Si lo desea, repita los tres pasos anteriores para la base de datos de usuario AdventureWorksLT.

## <a name="create-a-new-user-in-the-adventureworkslt-database-with-select-permissions"></a>Creación de un nuevo usuario en la base de datos AdventureWorksLT con permisos SELECT

En esta sección de la guía de procedimientos, va a crear una cuenta de usuario en la base de datos AdventureWorksLT basada en un nombre principal de un usuario de Azure AD o en el nombre para mostrar de un grupo de Azure AD, va a probar los permisos de este usuario como miembro del rol público, conceder permisos SELECT a este usuario y, finalmente, probar de nuevo los permisos de este.

> [!NOTE]
> Los usuarios de nivel de base de datos ([usuarios contenidos](https://msdn.microsoft.com/library/ff929188.aspx)) aumentan la portabilidad de la base de datos, una funcionalidad que se explorará en guías de procedimientos posteriores.
>

1. En el Explorador de objetos, haga clic con el botón derecho en **AdventureWorksLT** y, a continuación, haga clic en **Nueva consulta** para abrir una ventana de consulta conectada a la base de datos AdventureWorksLT.
2. Ejecute la instrucción siguiente para crear una cuenta de usuario en la base de datos AdventureWorksLT para un usuario del dominio de Microsoft denominado aaduser1.

   ```
   CREATE USER [aaduser1@microsoft.com]
   FROM EXTERNAL PROVIDER;
   ```
   ![nuevo usuario aaduser1@microsoft.com AdventureWorksLT](./media/sql-database-control-access-aad-authentication-get-started/new_user_aaduser1@microsoft.com_aw.png)

3. En la ventana de consulta, ejecute la siguiente consulta para que devuelva información sobre los permisos del usuario user1. Observe que los únicos permisos que tiene user1 son los permisos heredados del rol público.

   ```
   SELECT prm.permission_name
      , prm.class_desc
      , prm.state_desc
      , p2.name as 'Database role'
      , p3.name as 'Additional database role' 
   FROM sys.database_principals AS p
   JOIN sys.database_permissions AS prm
      ON p.principal_id = prm.grantee_principal_id
      LEFT JOIN sys.database_principals AS p2
      ON prm.major_id = p2.principal_id
      LEFT JOIN sys.database_role_members r
      ON p.principal_id = r.member_principal_id
      LEFT JOIN sys.database_principals AS p3
      ON r.role_principal_id = p3.principal_id
   WHERE p.name = 'aaduser1@microsoft.com';
   ```

   ![nuevos permisos de usuario en una base de datos de usuario](./media/sql-database-control-access-aad-authentication-get-started/new_user_permissions_in_user_database.png)

4. Ejecute las siguientes consultas para intentar consultar una tabla en la base de datos AdventureWorksLT como user1.

   ```
   EXECUTE AS USER = 'aaduser1@microsoft.com';  
   SELECT * FROM [SalesLT].[ProductCategory];
   REVERT;
   ```

   ![sin permisos select](./media/sql-database-control-access-aad-authentication-get-started/no_select_permissions.png)

5. Ejecute la siguiente instrucción para conceder a user1 permisos SELECT en la tabla ProductCategory del esquema SalesLT.

   ```
   GRANT SELECT ON OBJECT::[SalesLT].[ProductCategory] to [aaduser1@microsoft.com];
   ```

   ![conceder permisos select](./media/sql-database-control-access-aad-authentication-get-started/grant_select_permissions.png)

6. Ejecute las siguientes consultas para intentar consultar una tabla en la base de datos AdventureWorksLT como user1.

   ```
   EXECUTE AS USER = 'aaduser1@microsoft.com';  
   SELECT * FROM [SalesLT].[ProductCategory];
   REVERT;
   ```

   ![permisos select](./media/sql-database-control-access-aad-authentication-get-started/select_permissions.png)

## <a name="create-a-database-level-firewall-rule-for-adventureworkslt-database-users"></a>Creación de una regla de firewall de nivel de base de datos para los usuarios de la base de datos AdventureWorksLT

> [!NOTE]
> No es necesario completar este procedimiento si ya ha completado el procedimiento equivalente de la guía de procedimientos relacionada sobre la autenticación de SQL Server, [Autenticación y autorización de SQL](sql-database-control-access-sql-authentication-get-started.md) y está aprendiendo a usar el mismo equipo con la misma dirección IP.
>

En esta sección de la guía de procedimientos, va a iniciar sesión con la nueva cuenta de usuario desde un equipo con una dirección IP diferente, va a crear una regla de firewall de nivel de base de datos como administrador del servidor y, finalmente, iniciará sesión correctamente mediante esta nueva regla de firewall de nivel de base de datos. 

> [!NOTE]
> [Las reglas de firewall de nivel de base de datos](sql-database-firewall-configure.md) aumentan la portabilidad de la base de datos, una funcionalidad que se explorará en guías de procedimientos posteriores.
>

1. En otro equipo para el que aún no se ha creado una regla de firewall de nivel de servidor, abra SQL Server Management Studio.

   > [!IMPORTANT]
   > Use siempre la versión más reciente de SSMS, que puede encontrar en [Descarga de SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx). 
   >

2. En la ventana **Conectar con el servidor**, escriba el nombre del servidor y la información de autenticación para conectarse mediante la autenticación de SQL Server con la cuenta aaduser1@microsoft.com. 
    
   ![Conéctese como aaduser1@microsoft.com sin la regla de firewall rule1](./media/sql-database-control-access-aad-authentication-get-started/connect_aaduser1_no_rule1.png)

3. Haga clic en **Opciones** en el cuadro de diálogo **Conectar a servidor** para especificar la base de datos a la que desea conectarse y después escriba **AdventureWorksLT** en el cuadro desplegable **Conectar con base de datos** de la pestaña **Propiedades de conexión**.
   
   ![Conéctese como aaduser1 sin la regla de firewall rule2](./media/sql-database-control-access-aad-authentication-get-started/connect_aaduser1_no_rule2.png)

4. Haga clic en **Conectar**. Aparece un cuadro de diálogo que le informa de que el equipo desde el que está intentando conectarse a SQL Database no tiene una regla de firewall que permita el acceso a la base de datos. El cuadro de diálogo que recibe tendrá dos variantes según los pasos que haya realizado anteriormente con los firewalls, pero normalmente se obtiene el primer cuadro de diálogo mostrado.

   ![Conectarse como user1 sin la regla de firewall rule3](./media/sql-database-control-access-aad-authentication-get-started/connect_aaduser1_no_rule3.png)

   ![Conectarse como user1 sin la regla de firewall rule4](./media/sql-database-control-access-aad-authentication-get-started/connect_aaduser1_no_rule4.png)

   > [!NOTE]
   > Las versiones más recientes de SSMS incluyen la funcionalidad que permite a los propietarios de suscripciones y colaboradores iniciar sesión en Microsoft Azure y crear una regla de firewall de nivel de servidor.
   > 

4. Copie la dirección IP del cliente de este cuadro de diálogo para su uso en el paso 7.
5. Haga clic en **Cancelar** pero no cierre el cuadro de diálogo **Conectar con el servidor**.
6. Vuelva a cambiar a un equipo para el que ya se ha creado una regla de firewall de nivel de servidor y conéctese al servidor con la cuenta de administrador del servidor.
7. En una nueva ventana de consulta conectada a la base de datos AdventureWorksLT como administrador del servidor, ejecute la instrucción siguiente para crear un firewall de nivel de base de datos mediante la ejecución de [sp_set_database_firewall_rule](https://msdn.microsoft.com/library/dn270010.aspx) y utilizando la dirección IP del paso 4:

   ```
   EXEC sp_set_database_firewall_rule @name = N'AdventureWorksLTFirewallRule', 
     @start_ip_address = 'x.x.x.x', @end_ip_address = 'x.x.x.x';
   ```

   ![agregar regla rule4 de firewall de nivel de base de datos](./media/sql-database-control-access-aad-authentication-get-started/aaduser1_add_rule_aw.png)

8. Cambie los equipos de nuevo y haga clic en **Conectar** en el cuadro de diálogo **Conectar con el servidor** para conectarse a AdventureWorksLT como aaduser1. 

9. En el Explorador de objetos, expanda **Bases de datos**, expanda **AdventureWorksLT**y, a continuación, expanda **Tablas**. Observe que user1 solo tiene permiso para ver una sola tabla, la denominada **SalesLT.ProductCategory**. 

10. En el Explorador de objetos, haga clic con el botón derecho en **SalesLT.ProductCategory** y haga clic en **Select Top 1000 Rows** (Seleccionar las 1000 primeras filas).   

## <a name="next-steps"></a>Pasos siguientes
- Para obtener información general de acceso y control en SQL Database, consulte [Control de acceso a Azure SQL Database](sql-database-control-access.md).
- Para obtener información general de los inicios de sesión, usuarios y roles de base de datos de SQL Database, consulte [Control y concesión de acceso a bases de datos](sql-database-manage-logins.md).
- Para más información acerca de las entidades de seguridad de bases de datos, consulte [Entidades de seguridad](https://msdn.microsoft.com/library/ms181127.aspx).
- Para más información acerca de los roles de base de datos, consulte [Roles de nivel de base de datos](https://msdn.microsoft.com/library/ms189121.aspx).
- Para más información general acerca de las reglas de firewall de SQL Database, consulte [Introducción a las reglas de firewall de Azure SQL Database](sql-database-firewall-configure.md).


