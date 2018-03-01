---
title: "Eliminación de asignaciones de usuario o grupo de una aplicación empresarial en Azure Active Directory | Microsoft Docs"
description: "Cómo eliminar la asignación del acceso de un usuario o grupo de una aplicación empresarial en Azure Active Directory"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
ms.assetid: 7b2d365b-ae92-477f-9702-353cc6acc5ea
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2018
ms.author: markvi
ms.reviewer: asteen
ms.custom: it-pro
ms.openlocfilehash: 084ffcbe473290a8734b1c8b8847b517dac4f6b6
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/21/2018
---
# <a name="remove-a-user-or-group-assignment-from-an-enterprise-app-in-azure-active-directory"></a>Eliminación de asignaciones de usuario o grupo de una aplicación empresarial en Azure Active Directory
Evitar que se asigne acceso a un usuario o grupo a una de sus aplicaciones empresariales en Azure Active Directory (Azure AD) es fácil. Debe tener los permisos adecuados para administrar la aplicación de empresa y debe ser administrador global en el directorio.

> [!NOTE]
> Con aplicaciones de Microsoft (por ejemplo, aplicaciones de Office 365), use PowerShell para eliminar usuarios de una aplicación empresarial.

## <a name="how-do-i-remove-a-user-or-group-assignment-to-an-enterprise-app-in-the-azure-portal"></a>¿Cómo puedo eliminar asignaciones de usuario o grupo de una aplicación empresarial en Azure Portal?
1. Inicie sesión en [Azure Portal](https://portal.azure.com) con una cuenta que tenga el rol de administrador global en el directorio.
2. Seleccione **Más servicios**, escriba **Azure Active Directory** en el cuadro de texto y presione **ENTRAR**.
3. En la página **Azure Active Directory - *nombreDeDirectorio*** (es decir, la página de Azure AD del directorio que está administrando), seleccione **Aplicaciones empresariales**.

    ![Apertura de Enterprise apps (Aplicaciones empresariales)](./media/active-directory-coreapps-remove-assignment-user-azure-portal/open-enterprise-apps.png)
4. En la página **Aplicaciones empresariales**, seleccione **Todas las aplicaciones**. Verá una lista de las aplicaciones que puede administrar.
5. En la página **Aplicaciones empresariales - Todas las aplicaciones**, seleccione una aplicación.
6. En la página ***nombreDeAplicación*** (es decir, la página con el nombre de la aplicación seleccionada en el título), seleccione **Usuarios y grupos**.

    ![Selección de usuarios o grupos](./media/active-directory-coreapps-remove-assignment-user-azure-portal/remove-app-users.png)
7. En la página ***nombreDeAplicación*** **- User & Group Assignment** (Asignación de usuarios y grupos), seleccione uno o varios usuarios o grupos y, luego, seleccione el comando**Quitar**. Confirme la decisión en el símbolo del sistema.

    ![Selección del comando Eliminar](./media/active-directory-coreapps-remove-assignment-user-azure-portal/remove-users.png)

## <a name="how-do-i-remove-a-user-or-group-assignment-to-an-enterprise-app-using-powershell"></a>¿Cómo puedo eliminar asignaciones de usuario o grupo de una aplicación empresarial con PowerShell?
1. Abra un símbolo del sistema de Windows PowerShell con privilegios elevados.

    >[!NOTE] 
    > Debe instalar el módulo de AzureAD (use el comando `Install-Module -Name AzureAD`). Si se le pide que instale un módulo de NuGet o el nuevo módulo de PowerShell para Azure Active Directory V2, escriba S y presione ENTRAR.

2. Ejecute `Connect-AzureAD` e inicie sesión con una cuenta de usuario administrador global.
3. Use el siguiente script para asignar un usuario y un rol a una aplicación:

    ```powershell
    # Store the proper parameters
    $user = get-azureaduser -ObjectId <objectId>
    $spo = Get-AzureADServicePrincipal -ObjectId <objectId>

    #Get the ID of role assignment 
    $assignments = Get-AzureADServiceAppRoleAssignment -ObjectId $spo.ObjectId | Where {$_.PrincipalDisplayName -eq $user.DisplayName}

    #if you run the following, it will show you what is assigned what
    $assignments | Select *

    #To remove the App role assignment run the following command.
    Remove-AzureADServiceAppRoleAssignment -ObjectId $spo.ObjectId -AppRoleAssignmentId $assignments[assignment #].ObjectId
    ``` 
## <a name="next-steps"></a>pasos siguientes

- [Ver todos mis grupos](active-directory-groups-view-azure-portal.md)
- [Asignar un usuario o grupo a una aplicación empresarial](active-directory-coreapps-assign-user-azure-portal.md)
- [Deshabilitar los inicios de sesión de los usuarios de una aplicación empresarial](active-directory-coreapps-disable-app-azure-portal.md)
- [Cambio del nombre o el logotipo de una aplicación empresarial](active-directory-coreapps-change-app-logo-user-azure-portal.md)
