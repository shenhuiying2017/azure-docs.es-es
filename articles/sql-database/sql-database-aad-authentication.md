---
title: "Autenticación de Azure Active Directory: Azure SQL (Introducción) | Microsoft Docs"
description: "Aprenda a usar Azure Active Directory para la autenticación con SQL Database y SQL Data Warehouse"
services: sql-database
documentationcenter: 
author: BYHAM
manager: jhubbard
editor: 
tags: 
ms.assetid: 7e2508a1-347e-4f15-b060-d46602c5ce7e
ms.service: sql-database
ms.custom: authentication and authorization
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 01/23/2017
ms.author: rickbyh
translationtype: Human Translation
ms.sourcegitcommit: 2c13daf84727a500a2ea6a3dc1d4968c9824e223
ms.openlocfilehash: 9f29b451ef76a318a7778a8d20e292e311191298
ms.lasthandoff: 02/16/2017


---
# <a name="use-azure-active-directory-authentication-for-authentication-with-sql-database-or-sql-data-warehouse"></a>Uso de la autenticación de Azure Active Directory con SQL Database o SQL Data Warehouse
La autenticación de Azure Active Directory es un mecanismo de conexión a la Base de datos SQL de Microsoft Azure y [Almacenamiento de datos SQL](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) mediante identidades de Azure Active Directory (Azure AD). Con la autenticación de Azure AD puede administrar centralmente las identidades de los usuarios de la base de datos y otros servicios de Microsoft en una ubicación central. La administración de identificadores central ofrece una ubicación única para administrar usuarios de base de datos y simplifica la administración de permisos. Entre las ventajas se incluyen las siguientes:

* Ofrece una alternativa a la autenticación de SQL Server.
* Ayuda a detener la proliferación de identidades de usuario en los servidores de base de datos.
* Permite la rotación de contraseñas en un solo lugar
* Los clientes pueden administrar los permisos de la base de datos con grupos externos (AAD).
* Puede eliminar el almacenamiento de contraseñas mediante la habilitación de la autenticación integrada de Windows y otras formas de autenticación compatibles con Azure Active Directory.
* La autenticación de Azure AD utiliza usuarios de base de datos independiente para autenticar las identidades en el nivel de base de datos.
* Azure AD admite la autenticación basada en token para las aplicaciones que se conectan a SQL Database.
* La autenticación de Azure AD es compatible con ADFS (federación de dominio) o la autenticación nativa de usuario y contraseña para una instancia de Azure Active Directory local sin sincronización de dominios.  
* Azure AD admite conexiones de SQL Server Management Studio que usan la autenticación universal de Active Directory, lo cual incluye Multi-Factor Authentication (MFA).  MFA incluye una sólida autenticación con una gama de sencillas opciones de comprobación: llamada de teléfono, mensaje de texto, tarjetas inteligentes con PIN o notificación de aplicación móvil. Para obtener más información, consulte [Compatibilidad de SSMS con Azure AD MFA con Base de datos SQL y Almacenamiento de datos SQL](sql-database-ssms-mfa-authentication.md).

En los pasos de configuración se incluyen los siguientes procedimientos para configurar y usar la autenticación de Azure Active Directory.

1. Cree y rellene una instancia de Azure AD.
2. Asegúrese de que la base de datos está en Base de datos SQL de Azure V12. (No es necesario para Almacenamiento de datos SQL).
3. Opcional: asocie o cambie la instancia de Active Directory que está asociada a la suscripción de Azure.
4. Cree un administrador de Azure Active Directory para Azure SQL Server o para [Azure SQL Data Warehouse](https://azure.microsoft.com/services/sql-data-warehouse/).
5. Configure los equipos cliente.
6. Cree usuarios de bases de datos independientes en la base de datos y asignados a identidades de Azure AD.
7. Conéctese a la base de datos mediante identidades de Azure AD.

> [!NOTE]
> Para aprender a crear y rellenar Azure AD y, posteriormente, configurarlo con Azure SQL Database y SQL Data Warehouse, consulte [Configure Azure AD with Azure SQL Database](sql-database-aad-authentication-configure.md) (Configuración de Azure AD con Azure SQL Database).
>

## <a name="trust-architecture"></a>Arquitectura de confianza
En el siguiente diagrama de alto nivel se resume la arquitectura de la solución del uso de la autenticación de Azure AD con la Base de datos SQL de Azure. Los mismos conceptos se aplican a Almacenamiento de datos SQL. Para admitir la contraseña de usuario nativa de Azure AD, se considera solo la parte de la nube y SQL Database de Azure o Azure AD. Para admitir la autenticación federada (o el usuario o contraseña para las credenciales de Windows), se requiere la comunicación con el bloque ADFS. Las flechas indican las rutas de comunicación.

![diagrama de autenticación de aad][1]

En el diagrama siguiente se indica la federación, la confianza y las relaciones de hospedaje que permiten que un cliente se conecte a una base de datos mediante el envío de un token que una instancia de Azure AD autenticó y que es de confianza para la base de datos. El cliente 1 puede representar una instancia de Azure Active Directory con usuarios nativos o una instancia de AD con usuarios federados. El cliente 2 representa una posible solución, incluidos los usuarios importados; en este ejemplo proceden de una instancia de Azure Active Directory federada con ADFS sincronizado con Azure Active Directory. Es importante comprender que el acceso a una base de datos mediante la autenticación de Azure AD requiere que la suscripción de hospedaje esté asociada a Azure AD. Debe utilizarse la misma suscripción para crear el servidor SQL Server que hospeda la Base de datos SQL de Azure o el Almacenamiento de datos SQL.

![relación de suscripción][2]

## <a name="administrator-structure"></a>Estructura del administrador
Cuando se usa la autenticación de Azure AD, existen dos cuentas de administrador para el servidor de Base de datos SQL: el administrador del servidor SQL Server original y el administrador de Azure AD. Los mismos conceptos se aplican a Almacenamiento de datos SQL. Solo el administrador basado en una cuenta de Azure AD puede crear el primer usuario de base de datos independiente en Azure AD en una base de datos de usuario. El inicio de sesión del administrador de Azure AD puede ser un usuario de Azure AD o un grupo de Azure AD. Cuando el administrador es una cuenta de grupo, cualquier miembro del grupo lo puede usar, lo que permite varios administradores de Azure AD para la instancia de SQL Server. Mediante el uso de la cuenta de grupo como un administrador, se mejora la administración al permitir agregar y quitar miembros de grupo de forma centralizada en Azure AD sin cambiar los usuarios ni los permisos de Base de datos SQL. Solo un administrador de Azure AD (un usuario o grupo) se puede configurar en cualquier momento.

![estructura de administración][3]

## <a name="permissions"></a>Permisos
Para crear nuevos usuarios, debe tener el permiso `ALTER ANY USER` en la base de datos. El permiso `ALTER ANY USER` se puede conceder a cualquier usuario de la base de datos. Las cuentas de administrador del servidor también disponen del permiso `ALTER ANY USER`, así como los usuarios de la base de datos con los permisos `CONTROL ON DATABASE` o `ALTER ON DATABASE` para esa base de datos y los miembros del rol de la base de datos `db_owner`.

Para crear un usuario de base de datos independiente en la Base de datos SQL de Azure o en Almacenamiento de datos SQL, debe conectarse a la base de datos con una identidad de Azure AD. Para crear el primer usuario de base de datos independiente , debe conectarse a la base de datos con un administrador de Azure AD (que es el propietario de la base de datos). Esto se muestra a continuación, en los pasos 4 y 5. La autenticación de Azure AD solo es posible si se creó el administrador de Azure AD para Azure SQL Database o para el servidor de SQL Data Warehouse. Si se ha quitado el administrador de Azure Active Directory desde el servidor, los usuarios de Azure Active Directory creados previamente en SQL Server ya no podrán conectarse a la base de datos utilizando sus credenciales de Azure Active Directory.

## <a name="azure-ad-features-and-limitations"></a>Características y limitaciones de Azure AD
Los siguientes miembros de Azure AD se pueden aprovisionar en Azure SQL Server o en SQL Data Warehouse:

* Miembros nativos: un miembro creado en Azure AD en el dominio administrado o en un dominio personalizado. Para más información, consulte [Incorporación de su nombre de dominio personalizado a Azure Active Directory](../active-directory/active-directory-add-domain.md).
* Miembros de dominio federado: un miembro creado en Azure AD con un dominio federado. Para más información, consulte [Microsoft Azure now supports federation with Windows Server Active Directory](https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/)(Microsoft Azure ya admite la federación con Windows Server Active Directory).
* Miembros importados de otros directorios de Azure AD que son miembros nativos o miembros de dominio federado.
* Grupos de Active Directory creados como grupos de seguridad.

No se admiten las cuentas de Microsoft (por ejemplo, outlook.com, hotmail.com, live.com) ni otras cuentas de invitado (por ejemplo, gmail.com, yahoo.com). Si puede iniciar sesión en [https://login.live.com](https://login.live.com) con la cuenta y la contraseña, está usando una cuenta Microsoft que no se admite para la autenticación de Azure AD para Base de datos SQL de Azure o Almacenamiento de datos SQL de Azure.

## <a name="connecting-using-azure-ad-identities"></a>Conexión mediante identidades de Azure AD

La autenticación de Azure Active Directory admite los siguientes métodos de conexión a una base de datos mediante identidades de Azure AD:

* Mediante la autenticación integrada de Windows.
* Mediante el nombre y contraseña de una entidad de seguridad de Azure AD
* Mediante la autenticación de token de aplicación

### <a name="additional-considerations"></a>Consideraciones adicionales

* Para mejorar la capacidad de administración, se recomienda que aprovisione un grupo dedicado de Azure AD como administrador.   
* Solo un administrador de Azure AD (un usuario o grupo) se puede configurar en un servidor de Azure SQL Server o Azure SQL Data Warehouse en cualquier momento.   
* Inicialmente, solo un administrador de Azure AD para SQL Server puede conectarse al servidor de Azure SQL Server o Azure SQL Data Warehouse con una cuenta de Azure Active Directory. El administrador de Active Directory puede configurar los usuarios de la base de datos de Azure AD sucesivos.   
* Se recomienda establecer el tiempo de espera de conexión a 30 segundos.   
* SQL Server 2016 Management Studio y SQL Server Data Tools para Visual Studio 2015 (versión 14.0.60311.1 abril de 2016 o posterior) admiten la autenticación de Azure Active Directory. (La autenticación de Azure AD es compatible con el **proveedor de datos .NET Framework para SqlServer**; al menos la versión 4.6 de .NET Framework). Por lo tanto, las versiones más recientes de estas herramientas y aplicaciones de capa de datos (DAC y .bacpac) pueden usar la autenticación de Azure AD.   
* [La versión 13.1 de ODBC](https://www.microsoft.com/download/details.aspx?id=53339) es compatible con la autenticación de Azure Active Directory; sin embargo, `bcp.exe` no se puede conectar mediante la autenticación de Azure Active Directory porque usa un proveedor de ODBC anterior.   
* `sqlcmd` admite la autenticación de Azure Active Directory a partir de la versión 13.1, disponible en el [Centro de descarga](http://go.microsoft.com/fwlink/?LinkID=825643).   
* SQL Server Data Tools para Visual Studio 2015 requiere al menos la versión de abril de 2016 de Data Tools (versión 14.0.60311.1). Actualmente, los usuarios de Azure AD no se muestran en el explorador de objetos de SSDT. Como solución alternativa, vea los usuarios de [sys.database_principals](https://msdn.microsoft.com/library/ms187328.aspx).   
* [Microsoft JDBC Driver 6.0 para SQL Server](https://www.microsoft.com/download/details.aspx?id=11774) es compatible con la autenticación de Azure AD. Consulte también [Configurar las propiedades de conexión](https://msdn.microsoft.com/library/ms378988.aspx).   
* PolyBase no se puede autenticar mediante la autenticación de Azure AD.   
* No se admiten algunas herramientas como BI y Excel.   
* La autenticación de Azure AD se admite para SQL Database con las hojas **Importar base de datos** y **Exportar base de datos de Azure Portal**. La importación y exportación mediante la autenticación de Azure AD también se admite desde el comando de PowerShell.   

## <a name="next-steps"></a>Pasos siguientes
- Para aprender a crear y rellenar Azure AD y, posteriormente, configurarlo con Azure SQL Database, consulte [Configure Azure AD with Azure SQL Database](sql-database-aad-authentication-configure.md) (Configuración de Azure AD con Azure SQL Database).
- Para obtener información general de acceso y control en SQL Database, consulte [Control de acceso a Azure SQL Database](sql-database-control-access.md).
- Para obtener información general de los inicios de sesión, usuarios y roles de base de datos de SQL Database, consulte [Control y concesión de acceso a bases de datos](sql-database-manage-logins.md).
- Para más información acerca de las entidades de seguridad de bases de datos, consulte [Entidades de seguridad](https://msdn.microsoft.com/library/ms181127.aspx).
- Para más información acerca de los roles de base de datos, consulte [Roles de nivel de base de datos](https://msdn.microsoft.com/library/ms189121.aspx).
- Para más información general acerca de las reglas de firewall de SQL Database, consulte [Introducción a las reglas de firewall de Azure SQL Database](sql-database-firewall-configure.md).
- Para ver un tutorial sobre el uso de la autenticación de SQL Server, consulte [Autenticación y autorización de SQL Server](sql-database-control-access-sql-authentication-get-started.md).
- Para ver un tutorial sobre el uso de la autenticación de Azure Active Directory, consulte [Autenticación y autorización de Azure AD](sql-database-control-access-aad-authentication-get-started.md).

<!--Image references-->

[1]: ./media/sql-database-aad-authentication/1aad-auth-diagram.png
[2]: ./media/sql-database-aad-authentication/2subscription-relationship.png
[3]: ./media/sql-database-aad-authentication/3admin-structure.png
[4]: ./media/sql-database-aad-authentication/4select-subscription.png
[5]: ./media/sql-database-aad-authentication/5ad-settings-portal.png
[6]: ./media/sql-database-aad-authentication/6edit-directory-select.png
[7]: ./media/sql-database-aad-authentication/7edit-directory-confirm.png
[8]: ./media/sql-database-aad-authentication/8choose-ad.png
[9]: ./media/sql-database-aad-authentication/9ad-settings.png
[10]: ./media/sql-database-aad-authentication/10choose-admin.png
[11]: ./media/sql-database-aad-authentication/11connect-using-int-auth.png
[12]: ./media/sql-database-aad-authentication/12connect-using-pw-auth.png
[13]: ./media/sql-database-aad-authentication/13connect-to-db.png


