---
title: 'Multi-Factor Authentication: Azure SQL | Microsoft Docs'
description: Use Multi-Factor Authentication con SSMS para Base de datos SQL y Almacenamiento de datos SQL.
services: sql-database
documentationcenter: 
author: BYHAM
manager: jhubbard
editor: 
tags: 
ms.assetid: fbd6e644-0520-439c-8304-2e4fb6d6eb91
ms.service: sql-database
ms.custom: authentication and authorization
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 01/23/2017
ms.author: rickbyh
translationtype: Human Translation
ms.sourcegitcommit: 6e68ff56eaab3de8166711c872bf1b5707dbdf56
ms.openlocfilehash: ce77a059a05f83d74e80c71a87a22de6755b2dbb
ms.lasthandoff: 02/16/2017


---
# <a name="ssms-support-for-azure-ad-mfa-with-sql-database-and-sql-data-warehouse"></a>Compatibilidad de SSMS con Azure AD MFA con Base de datos SQL y Almacenamiento de datos SQL
Base de datos SQL de Azure y Almacenamiento de datos SQL de Azure ahora admiten conexiones provenientes de SQL Server Management Studio (SSMS) con *Autenticación universal de Active Directory*. Autenticación universal de Active Directory es un flujo de trabajo interactivo que admite *Azure Multi-Factor Authentication* (MFA). Azure MFA ayuda a proteger el acceso a los datos y las aplicaciones, al tiempo que satisface la demanda de los usuarios de un proceso de inicio de sesión simple. Ofrece una autenticación segura gracias a una gran variedad de opciones sencillas de verificación, como llamadas telefónicas, mensajes de texto, tarjetas inteligentes con PIN o notificaciones de aplicaciones móviles, lo que permite a los usuarios elegir el mecanismo que prefieran. 

* Para una descripción de Multi-Factor Authentication, consulte [Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication.md).
* Para ver los pasos de configuración, consulte [Configure Azure SQL Database multi-factor authentication for SQL Server Management Studio](sql-database-ssms-mfa-authentication-configure.md) (Configuración de la autenticación multifactor de Azure SQL Database para SQL Server Management Studio).

## <a name="multi-factor-options"></a>Opciones de multifactor

SSMS ahora admite:

* MFA interactivo con Azure AD con el potencial de una validación en cuadros de diálogo emergentes.
* Los métodos no interactivos Contraseña de Active Directory y Autenticación integrada de Active Directory que se pueden usar en muchas aplicaciones distintas (ADO.NET, JDBC, ODBC, etc.). Estos dos métodos nunca generan cuadros de diálogo emergentes.

Cuando la cuenta de usuario está configurada para MFA, el flujo de trabajo de autenticación interactivo requiere la interacción adicional del usuario a través de cuadros de diálogo emergentes, el uso de tarjetas inteligentes, etc. Cuando la cuenta de usuario está configurada para MFA, el usuario debe seleccionar Autenticación universal de Azure para conectarse. Si la cuenta de usuario no requiere MFA, de todos modos puede usar las otras dos opciones de Autenticación de Azure Active Directory.

## <a name="universal-authentication-limitations-for-sql-database-and-sql-data-warehouse"></a>Limitaciones de Autenticación universal para Base de datos SQL y Almacenamiento de datos SQL
* SSMS es la única herramienta que actualmente está habilitada para MFA a través de Autenticación universal de Active Directory.
* Solo una cuenta única de Azure Active Directory puede iniciar sesión en una instancia de SSMS con Autenticación universal. Para iniciar sesión con otra cuenta de Azure AD, debe usar otra instancia de SSMS. (Esta restricción se limita a Autenticación universal de Active Directory. Puede iniciar sesión en distintos servidores con Autenticación de contraseña de Active Directory, Autenticación integrada de Active Directory o Autenticación de SQL Server).
* SSMS admite Autenticación universal de Active Directory para la visualización de Explorador de objetos, Editor de consultas y Almacén de consultas.
* Ni DacFx ni el Diseñador de esquemas admiten Autenticación universal.
* Las cuentas de MSA no se admiten para Autenticación universal de Active Directory.
* Autenticación universal de Active Directory no se admite en SSMS para los usuarios que se importan a la instancia actual de Active Directory desde otros directorios de Active Directory. Estos usuarios no se admiten, porque haría falta un id. de inquilino para validar las cuentas y no hay ningún mecanismo que lo proporcione.
* No hay ningún requisito de software adicional para Autenticación universal de Active Directory, excepto el uso de una versión compatible de SSMS.



## <a name="next-steps"></a>Pasos siguientes

* Para ver los pasos de configuración, consulte [Configure Azure SQL Database multi-factor authentication for SQL Server Management Studio](sql-database-ssms-mfa-authentication-configure.md) (Configuración de la autenticación multifactor de Azure SQL Database para SQL Server Management Studio).
* Conceder a otros usuarios acceso a la base de datos. Consulte [Autorización y autenticación de Base de datos SQL: concesión de acceso](sql-database-manage-logins.md).  
Asegúrese de que otros usuarios puedan conectarse a través del firewall. Consulte [Configuración de un firewall de nivel de servidor en Azure SQL Database mediante Azure Portal](sql-database-configure-firewall-settings.md).



