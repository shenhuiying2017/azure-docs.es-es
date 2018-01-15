---
title: 'Multi-Factor Authentication: Azure SQL | Microsoft Docs'
description: "Azure SQL Database y Azure SQL Data Warehouse admiten conexiones procedentes de SQL Server Management Studio (SSMS) mediante la Autenticación universal de Active Directory."
services: sql-database
documentationcenter: 
author: GithubMirek
manager: johammer
editor: 
tags: 
ms.assetid: fbd6e644-0520-439c-8304-2e4fb6d6eb91
ms.service: sql-database
ms.custom: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: On Demand
ms.date: 09/29/2017
ms.author: mireks
ms.openlocfilehash: 3fd6532fb3dc6af9b3ccdac37201552ad0475e7b
ms.sourcegitcommit: 71fa59e97b01b65f25bcae318d834358fea5224a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/11/2018
---
# <a name="universal-authentication-with-sql-database-and-sql-data-warehouse-ssms-support-for-mfa"></a>Autenticación universal con SQL Database y SQL Data Warehouse (compatibilidad de SSMS con MFA)
Azure SQL Database y Azure SQL Data Warehouse admiten conexiones procedentes de SQL Server Management Studio (SSMS) mediante *Autenticación universal de Active Directory*. 
**Descarga de la versión de SSMS más reciente**: en el equipo cliente, descargue la versión más reciente de SSMS desde [Descarga de SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx). Para todas las características de este tema, utilice al menos la versión 17.2 de julio de 2017.  El cuadro de diálogo de conexión más reciente tiene la apariencia siguiente: ![1mfa-universal-connect](./media/sql-database-ssms-mfa-auth/1mfa-universal-connect.png "Rellena el cuadro Nombre de usuario.")  

## <a name="the-five-authentication-options"></a>Las cinco opciones de autenticación  
- La Autenticación Universal de Active Directory admite los dos métodos de autenticación no interactiva (`Active Directory - Password` y `Active Directory - Integrated`). Los métodos de autenticación no interactivos `Active Directory - Password` y `Active Directory - Integrated` se pueden usar en muchas aplicaciones (ADO.NET, JDBC, ODBC, etc.). Estos dos métodos nunca generan cuadros de diálogo emergentes.

- La autenticación `Active Directory - Universal with MFA` es un método interactivo que también admite *Azure Multi-Factor Authentication* (MFA). Azure MFA ayuda a proteger el acceso a los datos y las aplicaciones, al tiempo que satisface la demanda de los usuarios de un proceso de inicio de sesión simple. Ofrece una autenticación segura con una gran variedad de opciones sencillas de verificación, como llamadas telefónicas, mensajes de texto, tarjetas inteligentes con PIN o notificaciones de aplicaciones móviles, lo que permite a los usuarios elegir el mecanismo que prefieran. MFA interactivo con Azure AD puede generar un cuadro de diálogo emergente para la validación.

Para una descripción de Multi-Factor Authentication, consulte [Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication.md).
Para ver los pasos de configuración, consulte [Configure Azure SQL Database multi-factor authentication for SQL Server Management Studio](sql-database-ssms-mfa-authentication-configure.md) (Configuración de la autenticación multifactor de Azure SQL Database para SQL Server Management Studio).

### <a name="azure-ad-domain-name-or-tenant-id-parameter"></a>Nombre de dominio de Azure AD o parámetro de identificador de inquilino   

A partir de la [versión 17 de SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms), los usuarios que se importan como invitados en la instancia actual de Active Directory desde otras instancias, pueden proporcionar el nombre de dominio de Azure AD o el identificador de inquilino cuando se conectan. Los usuarios invitados incluyen los que lo son desde otras instancias de Azure AD, cuentas de Microsoft como outlook.com, hotmail.com o live.com, u otras cuentas como gmail.com. Esta información permite que **Active Directory universal con MFA Authentication** identifique la entidad de autenticación correcta. Esta opción también se requiere para admitir cuentas de Microsoft (MSA), como outlook.com, hotmail.com o live.com, y otras cuentas que no son MSA. Todos los usuarios que deseen que se emplee en ellos la autenticación universal deben escribir su nombre de dominio o identificador de inquilino de Azure AD. Este parámetro representa el nombre de dominio o identificador de inquilino actuales de Azure AD al que el servidor Azure está vinculado. Por ejemplo, si el servidor Azure está asociado al dominio de Azure AD `contosotest.onmicrosoft.com` en el que el usuario `joe@contosodev.onmicrosoft.com` está hospedado como un usuario importado del dominio de Azure AD `contosodev.onmicrosoft.com`, el nombre de dominio requerido para autenticar este usuario es `contosotest.onmicrosoft.com`. Cuando el usuario es un usuario nativo de Azure AD vinculado al servidor Azure y no es una cuenta MSA, no se requieren el identificador de inquilino ni el nombre de dominio. Para especificar el parámetro (a partir de la versión 17.2 de SSMS), en el cuadro de diálogo **Conectar con base de datos**, rellene el cuadro de diálogo, seleccione la autenticación **Active Directory: Universal con MFA**, haga clic en **Opciones**, rellene el cuadro **Nombre de usuario** y, a continuación, haga clic en la pestaña **Propiedades de conexión**. Active la casilla **Nombre de dominio o ID de inquilino de AD** y proporcione la entidad de autenticación, por ejemplo, el nombre de dominio (**contosoprueba.enmicrosoft.com**) o el GUID del identificador del inquilino.  
   ![mfa-tenant-ssms](./media/sql-database-ssms-mfa-auth/mfa-tenant-ssms.png)   

### <a name="azure-ad-business-to-business-support"></a>Compatibilidad con aplicaciones empresariales de Azure AD   
Los usuarios de Azure AD admitidos en escenarios empresariales (B2B) de Azure AD como invitados (consulte [¿Qué es la colaboración B2B de Azure AD](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md)) pueden conectarse a SQL Database y a SQL Data Warehouse únicamente como miembros de un grupo creado en Azure AD actual y asignado manualmente mediante la instrucción `CREATE USER` de Transact-SQL en una base de datos determinada. Por ejemplo, si `steve@gmail.com` es invitado a `contosotest` de Azure AD (con el dominio de Azure AD `contosotest.onmicrosoft.com`), debe crearse en Azure AD un grupo de Azure AD, como `usergroup`, que contenga el miembro `steve@gmail.com`. Después, el administrador de Azure AD SQL o un administrador de base de datos de Azure AD debe crear este grupo para una base de datos específica (por ejemplo, MyDatabase) ejecutando una instrucción `CREATE USER [usergroup] FROM EXTERNAL PROVIDER` de Transact-SQL. Después de crear el usuario de base de datos, el usuario `steve@gmail.com` puede iniciar sesión en `MyDatabase` con la opción de autenticación de SSMS `Active Directory – Universal with MFA support`. De forma predeterminada, el grupo de usuarios tiene el permiso de conexión y cualquier acceso adicional a los datos tendrá que concederse de la manera normal. Tenga en cuenta que el usuario `steve@gmail.com` como invitado debe activar la casilla y agregar el nombre de dominio de AD `contosotest.onmicrosoft.com` en el cuadro de diálogo **Propiedad de conexión** de SSMS. La opción **Nombre de dominio o ID de inquilino de AD** solo se admite para las opciones Universal con conexión MFA, en caso contrario se atenúa.

## <a name="universal-authentication-limitations-for-sql-database-and-sql-data-warehouse"></a>Limitaciones de Autenticación universal para SQL Database y SQL Data Warehouse
- SSMS y SqlPackage.exe son las únicas herramientas que actualmente están habilitadas para MFA a través de Autenticación universal de Active Directory.
- La versión 17.2 de SSMS admite el acceso simultáneo de varios usuarios mediante Autenticación universal con MFA. Las versiones 17.0 y 17.1, limitadas a un registro para una instancia de SSMS con Autenticación universal para una sola cuenta de Azure Active Directory. Para iniciar sesión con otra cuenta de Azure AD, debe usar otra instancia de SSMS. (Esta restricción se limita a Autenticación universal de Active Directory. Puede iniciar sesión en distintos servidores con Autenticación de contraseña de Active Directory, Autenticación integrada de Active Directory o Autenticación de SQL Server).
- SSMS admite Autenticación universal de Active Directory para la visualización de Explorador de objetos, Editor de consultas y Almacén de consultas.
- La versión 17.2 de SSMS proporciona compatibilidad con el asistente de DacFx para la base de datos de exportación, extracción e implementación. Una vez que se autentica un usuario específico mediante el cuadro de diálogo de autenticación inicial con Autenticación universal, el asistente de DacFx funciona del mismo modo que con todos los demás métodos de autenticación.
- El Diseñador de tablas de SSMS no admite Autenticación universal.
- No hay ningún requisito de software adicional para Autenticación universal de Active Directory, excepto el uso de una versión compatible de SSMS.  
- Se actualizó la versión de la Biblioteca de autenticación de Active Directory (ADAL) para Autenticación universal a su versión de lanzamiento disponible más reciente ADAL.dll 3.13.9. Consulte [Biblioteca de autenticación de Active Directory 3.14.1](http://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/).  


## <a name="next-steps"></a>pasos siguientes

- Para ver los pasos de configuración, consulte [Configure Azure SQL Database multi-factor authentication for SQL Server Management Studio](sql-database-ssms-mfa-authentication-configure.md) (Configuración de la autenticación multifactor de Azure SQL Database para SQL Server Management Studio).
- Conceder a otros usuarios acceso a la base de datos. Consulte [Autorización y autenticación de SQL Database: concesión de acceso](sql-database-manage-logins.md).  
- Asegúrese de que otros usuarios puedan conectarse a través del firewall. Consulte [Configuración de un firewall de nivel de servidor en Azure SQL Database mediante Azure Portal](sql-database-configure-firewall-settings.md).  
- [Configuración y administración de la autenticación de Azure Active Directory con SQL Database o SQL Data Warehouse](sql-database-aad-authentication-configure.md)  
- [Microsoft SQL Server Data-Tier Application Framework (17.0.0 GA)](https://www.microsoft.com/download/details.aspx?id=55088)  
- [SQLPackage.exe](https://msdn.microsoft.com/library/hh550080.aspx)  
- [Importación de un archivo BACPAC en una nueva instancia de Azure SQL Database](../sql-database/sql-database-import.md)  
- [Exportación de una base de datos SQL de Azure a un archivo BACPAC](../sql-database/sql-database-export.md)  
- Interfaz de C# [Interfaz IUniversalAuthProvider](https://msdn.microsoft.com/library/microsoft.sqlserver.dac.iuniversalauthprovider.aspx)  
- Cuando se usa la autenticación de **Active Directory - Universal compatible con MFA**, el seguimiento de ADAL está disponible a partir de [SSMS 17.3](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms). Desactivado de forma predeterminada, puede activar el seguimiento de ADAL mediante el menú **Herramientas**, **Opciones**, en **Azure Services**, **Azure Cloud**, **Nivel de seguimiento de la ventana de salida de ADAL**, y habilitar seguidamente **Salida** en el menú **Ver**. Los seguimientos están disponibles en la ventana de salida al seleccionar la opción **Azure Active Directory**.  
