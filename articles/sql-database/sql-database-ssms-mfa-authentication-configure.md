---
title: "Configuración de la autenticación multifactor: Azure SQL | Microsoft Docs"
description: Aprenda a usar Multi-Factor Authentication con SSMS para SQL Database y SQL Data Warehouse.
services: sql-database
documentationcenter: 
author: GithubMirek
manager: johammer
editor: 
tags: 
ms.assetid: 
ms.service: sql-database
ms.custom: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Inactive
ms.date: 09/27/2017
ms.author: mireks
ms.openlocfilehash: fe056288fbc04db0a6892c9a501c58ed85967734
ms.sourcegitcommit: 71fa59e97b01b65f25bcae318d834358fea5224a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/11/2018
---
# <a name="configure-multi-factor-authentication-for-sql-server-management-studio-and-azure-ad"></a>Configuración de la autenticación multifactor para SQL Server Management Studio y Azure AD

En este tema se muestra cómo usar la autenticación multifactor (MFA) de Azure Active Directory con SQL Server Management Studio. MFA de Azure AD puede usarse cuando se conecta SSMS o SqlPackage.exe a Azure SQL Database y Azure SQL Data Warehouse.

Para obtener información general de la autenticación multifactor de Azure SQL Database, vea [Autenticación universal con SQL Database y SQL Data Warehouse (compatibilidad de SSMS con MFA)](sql-database-ssms-mfa-authentication.md).

## <a name="configuration-steps"></a>Pasos de configuración

1. **Configuración de una instancia de Azure Active Directory**: para más información, consulte [Administración de su directorio de Azure AD](https://msdn.microsoft.com/library/azure/hh967611.aspx), [Integración de identidades locales con Azure Active Directory](../active-directory/active-directory-aadconnect.md), [Add your own domain name to Azure AD](https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/) (Incorporación de su nombre de dominio a Azure AD), [Microsoft Azure now supports federation with Windows Server Active Directory](https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/) (Windows ahora admite la federación con Windows Server Active Directory) y [Administrar Azure AD mediante Windows PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx).
2. **Configurar MFA**: para obtener instrucciones detalladas, consulte [¿Qué es Azure Multi-Factor Authentication?](../multi-factor-authentication/multi-factor-authentication.md), [Acceso condicional (MFA) con Azure SQL Database y Data Warehouse](sql-database-conditional-access.md). (El acceso condicional completo requiere una versión Premium de Azure Active Directory (Azure AD). MFA limitado está disponible con la versión estándar de Azure AD).
3. **Configuración de SQL Database o SQL Data Warehouse para Autenticación de Azure AD**: para instrucciones detalladas, consulte [Conexión a SQL Database o a SQL Data Warehouse mediante Autenticación de Azure Active Directory](sql-database-aad-authentication.md).
4. **Descarga de SSMS**: en el equipo cliente, descargue la versión más reciente de SSMS, desde [Descarga de SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx). Para todas las características de este tema, utilice al menos la versión 17.2 de julio de 2017.  

## <a name="connecting-by-using-universal-authentication-with-ssms"></a>Conexión mediante autenticación universal con SSMS

Los pasos siguientes muestran cómo conectarse a SQL Database o SQL Data Warehouse con la versión más reciente de SSMS.

1. Para conectarse con Autenticación universal, en el cuadro de diálogo **Conectar con el servidor**, seleccione **Active Directory - Universal compatible con MFA**. (Si ve **Autenticación universal de Active Directory**, no tiene la versión más reciente de SSMS).  
   ![1mfa-universal-connect][1]  
2. Rellene el cuadro **Nombre de usuario** con las credenciales de Azure Active Directory, con el formato `user_name@domain.com`.  
   ![1mfa-universal-connect-user](./media/sql-database-ssms-mfa-auth/1mfa-universal-connect-user.png)   
3. Si va a conectarse como un usuario invitado, debe hacer clic en **Opciones** y, en el cuadro d diálogo **Propiedad de conexión**, rellenar el cuadro **Nombre de dominio o ID de inquilino de AD**. Para más información, consulte [Autenticación universal con SQL Database y SQL Data Warehouse (compatibilidad de SSMS con MFA)](sql-database-ssms-mfa-authentication.md).
   ![mfa-tenant-ssms](./media/sql-database-ssms-mfa-auth/mfa-tenant-ssms.png)   
4. Como es habitual para SQL Database y SQL Data Warehouse, debe hacer clic en **Opciones** y especificar la base de datos en el cuadro de diálogo **Opciones**. (Si el usuario conectado es un invitado (es decir, joe@outlook.com), debe activar la casilla y agregar el nombre de dominio de AD actual o el identificador de inquilino como parte de las opciones). Consulte [Autenticación universal con SQL Database y SQL Data Warehouse (compatibilidad de SSMS con MFA)]()(sql-database-ssms-mfa-authentication.md. Haga clic en **Conectar**.  
5. Cuando aparezca el cuadro de diálogo **Iniciar sesión con su cuenta** , escriba la cuenta y la contraseña de su identidad de Azure Active Directory. Si un usuario pertenece a un dominio federado con Azure AD, no se requiere contraseña.  
   ![2mfa-sign-in][2]  

   > [!NOTE]
   > Para Autenticación universal con una cuenta que no requiere MFA, conéctese en este momento. Para los usuarios que requieren MFA, continúe con los pasos siguientes:
   >  
   
6. Pueden aparecer dos cuadros de diálogo de configuración de MFA. Esta operación de una sola vez depende de la configuración del administrador de MFA y, por tanto, puede ser opcional. En el caso de un dominio habilitado para MFA, a veces este paso está definido previamente (por ejemplo, el dominio requiere que los usuarios usen una tarjeta inteligente y un PIN).  
   ![3mfa-setup][3]  
7. El segundo cuadro de diálogo de un único uso que puede que aparezca le permite seleccionar los detalles del método de autenticación. El administrador configura las opciones posibles.  
   ![4mfa-verify-1][4]  
8. Azure Active Directory le envía la información de confirmación. Cuando reciba el código de verificación, escríbalo en el cuadro **Escribir código de comprobación** y haga clic en **Iniciar sesión**.  
   ![5mfa-verify-2][5]  

Cuando se complete la comprobación, SSMS se conectará de manera habitual, siempre que haya acceso de firewall y credenciales válidas.

## <a name="next-steps"></a>pasos siguientes

- Para obtener información general de la autenticación multifactor de Azure SQL Database, consulte [Autenticación universal con SQL Database y SQL Data Warehouse (compatibilidad de SSMS con MFA)](sql-database-ssms-mfa-authentication.md).  
- Conceder a otros usuarios acceso a la base de datos. Consulte [Autorización y autenticación de SQL Database: concesión de acceso](sql-database-manage-logins.md).  
- Asegúrese de que otros usuarios puedan conectarse a través del firewall. Consulte [Configuración de un firewall de nivel de servidor en Azure SQL Database mediante Azure Portal](sql-database-configure-firewall-settings.md).  
- Cuando se usa la autenticación de **Active Directory - Universal compatible con MFA**, el seguimiento de ADAL está disponible a partir de [SSMS 17.3](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms). Desactivado de forma predeterminada, puede activar el seguimiento de ADAL mediante el menú **Herramientas**, **Opciones**, en **Azure Services**, **Azure Cloud**, **Nivel de seguimiento de la ventana de salida de ADAL**, y habilitar seguidamente **Salida** en el menú **Ver**. Los seguimientos están disponibles en la ventana de salida al seleccionar la opción **Azure Active Directory**.   


[1]: ./media/sql-database-ssms-mfa-auth/1mfa-universal-connect.png
[2]: ./media/sql-database-ssms-mfa-auth/2mfa-sign-in.png
[3]: ./media/sql-database-ssms-mfa-auth/3mfa-setup.png
[4]: ./media/sql-database-ssms-mfa-auth/4mfa-verify-1.png
[5]: ./media/sql-database-ssms-mfa-auth/5mfa-verify-2.png

