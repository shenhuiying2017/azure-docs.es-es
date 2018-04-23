---
title: Administración de administradores de servidor en Azure Analysis Services | Microsoft Docs
description: Aprenda a administrar administradores de servidor de Analysis Services en Azure.
author: minewiskan
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/12/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: def09f2853f761f3fefca80f341e6cc0557bac86
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/16/2018
---
# <a name="manage-server-administrators"></a>Administración de administradores de servidor
Los administradores del servidor deben ser un usuario o grupo válido en Azure Active Directory (Azure AD) para el inquilino en el que reside el servidor. Puede usar **Administradores de Analysis Services** en el servidor de Azure Portal o Propiedades del servidor en SSMS para administrar los administradores del servidor. 

## <a name="to-add-server-administrators-by-using-azure-portal"></a>Incorporación de administradores de servidor mediante Azure Portal
1. En el portal, en su servidor, haga clic en **Administradores de Analysis Services**.
2. En **\<nombre de servidor> - Administradores de Analysis Services**, haga clic en **Agregar**.
3. En **Agregar administradores de servidor**, seleccione las cuentas de usuario de Azure AD o invite usuarios externos por dirección de correo electrónico.

    ![Administradores del servidor en Azure Portal](./media/analysis-services-server-admins/aas-manage-users-admins.png)

## <a name="to-add-server-administrators-by-using-ssms"></a>Incorporación de administradores de servidor mediante SSMS
1. Haga clic con el botón derecho en el servidor > **Propiedades**.
2. En **Propiedades de Analysis Server**, haga clic en **Seguridad**.
3. Haga clic en **Agregar** y escriba la dirección de correo electrónico de un usuario o grupo de Azure AD.
   
    ![Incorporación de administradores de servidor en SSMS](./media/analysis-services-server-admins/aas-manage-users-ssms.png)

## <a name="next-steps"></a>Pasos siguientes 
[Permisos de usuario y autenticación](analysis-services-manage-users.md)  
[Manage database roles and users](analysis-services-database-users.md) (Administración de roles y usuarios de una base de datos)  
[Control de acceso basado en roles](../role-based-access-control/overview.md)  

