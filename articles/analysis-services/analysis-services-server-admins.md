---
title: "Administración de administradores de servidor en Azure Analysis Services | Microsoft Docs"
description: Aprenda a administrar administradores de servidor de Analysis Services en Azure.
services: analysis-services
documentationcenter: 
author: minewiskan
manager: kfile
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 11/01/2017
ms.author: owend
ms.openlocfilehash: 6a42baf9fd880264e4130c009f27935a4743de21
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2017
---
# <a name="manage-server-administrators"></a>Administración de administradores de servidor
Los administradores del servidor deben ser un usuario o grupo válido en Azure Active Directory (Azure AD) para el inquilino en el que reside el servidor. Puede usar **Administradores de Analysis Services** en la hoja de control del servidor en Azure Portal o Propiedades del servidor en SSMS para administrar los administradores de servidor. 

## <a name="to-add-server-administrators-by-using-azure-portal"></a>Incorporación de administradores de servidor mediante Azure Portal
1. En la hoja de control del servidor, haga clic en **Administradores de Analysis Services**.
2. En la hoja **\<nombreDeServidor > Administradores de Analysis Services**, haga clic en **Agregar**.
3. En la hoja **Agregar administradores de servidor**, seleccione las cuentas de usuario de Azure AD o invite usuarios externos por correo electrónico.

    ![Administradores del servidor en Azure Portal](./media/analysis-services-server-admins/aas-manage-users-admins.png)

## <a name="to-add-server-administrators-by-using-ssms"></a>Incorporación de administradores de servidor mediante SSMS
1. Haga clic con el botón derecho en el servidor > **Propiedades**.
2. En **Propiedades de Analysis Server**, haga clic en **Seguridad**.
3. Haga clic en **Agregar** y escriba la dirección de correo electrónico de un usuario o grupo de Azure AD.
   
    ![Incorporación de administradores de servidor en SSMS](./media/analysis-services-server-admins/aas-manage-users-ssms.png)

## <a name="next-steps"></a>Pasos siguientes 
[Permisos de usuario y autenticación](analysis-services-manage-users.md)  
[Manage database roles and users](analysis-services-database-users.md) (Administración de roles y usuarios de una base de datos)  
[Control de acceso basado en roles](../active-directory/role-based-access-control-what-is.md)  

