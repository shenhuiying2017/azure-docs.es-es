---
title: 'Multi-Factor Authentication: Azure SQL | Microsoft Docs'
description: Use Multi-Factor Authentication con SSMS para SQL Database y SQL Data Warehouse.
services: sql-database
documentationcenter: 
author: BYHAM
manager: jhubbard
editor: 
tags: 
ms.assetid: fbd6e644-0520-439c-8304-2e4fb6d6eb91
ms.service: sql-database
ms.custom: security-access
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 04/07/2017
ms.author: rickbyh
ms.translationtype: Human Translation
ms.sourcegitcommit: a3ca1527eee068e952f81f6629d7160803b3f45a
ms.openlocfilehash: 33282f1ce0c31056524359aeb42399be550334e2
ms.contentlocale: es-es
ms.lasthandoff: 04/27/2017


---
# <a name="universal-authentication-with-sql-database-and-sql-data-warehouse-ssms-support-for-mfa"></a>Autenticación universal con SQL Database y SQL Data Warehouse (compatibilidad de SSMS con MFA)
Azure SQL Database y Azure SQL Data Warehouse admiten conexiones procedentes de SQL Server Management Studio (SSMS) mediante *Autenticación universal de Active Directory*. 

- Autenticación Universal de Active Directory admite los dos métodos de autenticación no interactiva (Autenticación de contraseña de Active Directory y Autenticación integrada de Active Directory). Los métodos no interactivos Contraseña de Active Directory y Autenticación integrada de Active Directory se pueden usar en muchas aplicaciones (ADO.NET, JDBC, ODBC, etc.). Estos dos métodos nunca generan cuadros de diálogo emergentes.

- Autenticación universal de Active Directory es un método de trabajo interactivo que admite *Azure Multi-Factor Authentication* (MFA). Azure MFA ayuda a proteger el acceso a los datos y las aplicaciones, al tiempo que satisface la demanda de los usuarios de un proceso de inicio de sesión simple. Ofrece una autenticación segura gracias a una gran variedad de opciones sencillas de verificación, como llamadas telefónicas, mensajes de texto, tarjetas inteligentes con PIN o notificaciones de aplicaciones móviles, lo que permite a los usuarios elegir el mecanismo que prefieran. MFA interactivo con Azure AD puede generar un cuadro de diálogo emergente para la validación.

Para una descripción de Multi-Factor Authentication, consulte [Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication.md).
Para ver los pasos de configuración, consulte [Configure Azure SQL Database multi-factor authentication for SQL Server Management Studio](sql-database-ssms-mfa-authentication-configure.md) (Configuración de la autenticación multifactor de Azure SQL Database para SQL Server Management Studio).

### <a name="azure-ad-domain-name-or-tenant-id-parameter"></a>Nombre de dominio de Azure AD o parámetro de identificador de inquilino   

A partir de [la versión 17 de SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms), los usuarios que se importan en la instancia de Active Directory actual de otras instancias puede proporcionar el nombre de dominio de Azure AD o el identificador de inquilino cuando se conectan. Esto permite que **Autenticación universal de Active Directory** identifique la autoridad de autenticación correcta. Esta opción también se requiere para admitir cuentas de Microsoft (MSA), como outlook.com, hotmail.com o live.com. Todos los usuarios que deseen que se emplee en ellos la autenticación universal deben escribir su nombre de dominio o identificador de inquilino de Azure AD. Este parámetro representa el nombre de dominio o identificador de inquilino actuales de Azure AD al que el servidor Azure está vinculado. Por ejemplo, si el servidor Azure está asociado al dominio de Azure AD `contosotest.onmicrosoft.com` en el que el usuario `joe@contosodev.onmicrosoft.com` está hospedado como un usuario importado del dominio de Azure AD `contosodev.onmicrosoft.com`, el nombre de dominio requerido para autenticar este usuario es `contosotest.onmicrosoft.com`. Cuando el usuario es un usuario nativo de Azure AD vinculado al servidor Azure y no es una cuenta MSA, no se requieren el identificador de inquilino ni el nombre de dominio. Para especificar el parámetro (a partir de la versión 17 de SSMS), en el cuadro de diálogo **Conectar con base de datos**, complete el cuadro de diálogo, para lo que debe seleccionar **Autenticación universal de Active Directory**, haga clic en **Opciones**, haga clic en la pestaña **Propiedades de conexión**, marque el cuadro **Nombre de dominio o ID de inquilino de AD:** e indique la autoridad de autenticación, como el nombre de dominio (**contosotest.onmicrosoft.com**) o el GUID del identificador del inquilino.

## <a name="universal-authentication-limitations-for-sql-database-and-sql-data-warehouse"></a>Limitaciones de Autenticación universal para SQL Database y SQL Data Warehouse
* SSMS es la única herramienta que actualmente está habilitada para MFA a través de Autenticación universal de Active Directory.
* Solo una cuenta única de Azure Active Directory puede iniciar sesión en una instancia de SSMS con Autenticación universal. Para iniciar sesión con otra cuenta de Azure AD, debe usar otra instancia de SSMS. (Esta restricción se limita a Autenticación universal de Active Directory. Puede iniciar sesión en distintos servidores con Autenticación de contraseña de Active Directory, Autenticación integrada de Active Directory o Autenticación de SQL Server).
* SSMS admite Autenticación universal de Active Directory para la visualización de Explorador de objetos, Editor de consultas y Almacén de consultas.
* Ni DacFx ni el Diseñador de esquemas admiten Autenticación universal.
* No hay ningún requisito de software adicional para Autenticación universal de Active Directory, excepto el uso de una versión compatible de SSMS.


## <a name="next-steps"></a>Pasos siguientes

* Para ver los pasos de configuración, consulte [Configure Azure SQL Database multi-factor authentication for SQL Server Management Studio](sql-database-ssms-mfa-authentication-configure.md) (Configuración de la autenticación multifactor de Azure SQL Database para SQL Server Management Studio).
* Conceder a otros usuarios acceso a la base de datos. Consulte [Autorización y autenticación de SQL Database: concesión de acceso](sql-database-manage-logins.md).  
Asegúrese de que otros usuarios puedan conectarse a través del firewall. Consulte [Configuración de un firewall de nivel de servidor en Azure SQL Database mediante Azure Portal](sql-database-configure-firewall-settings.md).
* Para la configuración y administración de Azure AD, vea [Configuración y administración de la autenticación de Azure Active Directory con SQL Database o SQL Data Warehouse](sql-database-aad-authentication-configure.md).



