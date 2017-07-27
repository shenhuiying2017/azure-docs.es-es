---
title: "Configuración de la autenticación multifactor: Azure SQL | Microsoft Docs"
description: Use Multi-Factor Authentication con SSMS para Base de datos SQL y Almacenamiento de datos SQL.
services: sql-database
documentationcenter: 
author: BYHAM
manager: jhubbard
editor: 
tags: 
ms.assetid: 
ms.service: sql-database
ms.custom: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 06/08/2017
ms.author: rickbyh
ms.translationtype: Human Translation
ms.sourcegitcommit: 245ce9261332a3d36a36968f7c9dbc4611a019b2
ms.openlocfilehash: 1815ea909e35a02b82b4c836d7baaf6390d20bdd
ms.contentlocale: es-es
ms.lasthandoff: 06/09/2017


---
# <a name="configure-azure-sql-database-multi-factor-authentication-for-sql-server-management-studio"></a>Configure Multi-Factor Authentication de Azure SQL Database para SQL Server Management Studio

En este tema se muestra cómo configurar Multi-Factor Authentication de Azure SQL Database para SQL Server Management Studio. 

Para obtener información general de la autenticación multifactor de Azure SQL Database, vea [Autenticación universal con SQL Database y SQL Data Warehouse (compatibilidad de SSMS con MFA)](sql-database-ssms-mfa-authentication.md).

## <a name="configuration-steps"></a>Pasos de configuración

1. **Configuración de una instancia de Azure Active Directory**: para más información, consulte [Integración de las identidades locales con Azure Active Directory](../active-directory/active-directory-aadconnect.md), [Incorporación de su nombre de dominio personalizado a Azure AD](https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/), [Microsoft Azure now supports federation with Windows Server Active Directory](https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/) (Microsoft Azure ahora admite la federación con Windows Server Active Directory), [Administración de su directorio de Azure AD](https://msdn.microsoft.com/library/azure/hh967611.aspx) y [Administrar Azure AD mediante Windows PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx).
2. **Configurar MFA**: para obtener instrucciones detalladas, vea [Acceso condicional (MFA) con Azure SQL Database y Data Warehouse](sql-database-conditional-access.md). 
3. **Configuración de SQL Database o SQL Data Warehouse para Autenticación de Azure AD**: para instrucciones detalladas, consulte [Conexión a SQL Database o a SQL Data Warehouse mediante Autenticación de Azure Active Directory](sql-database-aad-authentication.md).
4. **Descarga de SSMS**: en el equipo cliente, descargue la versión más reciente de SSMS (como mínimo, de agosto de 2016), desde [Descarga de SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx).

## <a name="connecting-by-using-universal-authentication-with-ssms"></a>Conexión mediante autenticación universal con SSMS

Los pasos siguientes muestran cómo conectarse a Base de datos SQL o Almacenamiento de datos SQL con la versión más reciente de SSMS.

1. Para conectarse mediante autenticación universal, en el cuadro de diálogo **Conectar con el servidor**, seleccione **Autenticación universal de Active Directory**.

   ![1mfa-universal-connect][1]
2. Como es habitual para SQL Database y SQL Data Warehouse, debe hacer clic en **Opciones** y especificar la base de datos en el cuadro de diálogo **Opciones**. Haga clic en **Conectar**.
3. Cuando aparezca el cuadro de diálogo **Iniciar sesión con su cuenta** , escriba la cuenta y la contraseña de su identidad de Azure Active Directory.

   ![2mfa-sign-in][2]
   
   > [!NOTE]
   > Para Autenticación universal con una cuenta que no requiere MFA, conéctese en este momento. Para los usuarios que requieren MFA, continúe con los pasos siguientes:
   > 
   > 
4. Pueden aparecer dos cuadros de diálogo de configuración de MFA. Esta operación de una sola vez depende de la configuración del administrador de MFA y, por tanto, puede ser opcional. En el caso de un dominio habilitado para MFA, a veces este paso está definido previamente (por ejemplo, el dominio requiere que los usuarios usen una tarjeta inteligente y un PIN).  

   ![3mfa-setup][3]
5. El segundo cuadro de diálogo de un único uso que puede que aparezca le permite seleccionar los detalles del método de autenticación. El administrador configura las opciones posibles.

   ![4mfa-verify-1][4]
6. Azure Active Directory le envía la información de confirmación. Cuando reciba el código de comprobación, escríbalo en el cuadro **Escribir código de comprobación** y haga clic en **Iniciar sesión**.

   ![5mfa-verify-2][5]

Cuando se complete la comprobación, SSMS se conectará de manera habitual, siempre que haya acceso de firewall y credenciales válidas.

## <a name="next-steps"></a>Pasos siguientes

* Para obtener información general de la autenticación multifactor de Azure SQL Database, vea Autenticación universal con SQL Database y SQL Data Warehouse (compatibilidad de SSMS con MFA) (sql-database-ssms-mfa-authentication.md).
* Conceder a otros usuarios acceso a la base de datos. Consulte [Autorización y autenticación de SQL Database: concesión de acceso](sql-database-manage-logins.md).  
Asegúrese de que otros usuarios puedan conectarse a través del firewall. Consulte [Configuración de un firewall de nivel de servidor en Azure SQL Database mediante Azure Portal](sql-database-configure-firewall-settings.md).

[1]: ./media/sql-database-ssms-mfa-auth/1mfa-universal-connect.png
[2]: ./media/sql-database-ssms-mfa-auth/2mfa-sign-in.png
[3]: ./media/sql-database-ssms-mfa-auth/3mfa-setup.png
[4]: ./media/sql-database-ssms-mfa-auth/4mfa-verify-1.png
[5]: ./media/sql-database-ssms-mfa-auth/5mfa-verify-2.png


