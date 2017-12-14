---
title: "Incorporación de una nueva cuenta de inquilino de Azure Stack en Azure Active Directory | Microsoft Docs"
description: "Después de implementar Microsoft Azure Stack Development Kit, tendrá que crear una cuenta de usuario de al menos un inquilino para poder explorar el portal del inquilino."
services: azure-stack
documentationcenter: 
author: heathl17
manager: byronr
editor: 
ms.assetid: a75d5c88-5b9e-4e9a-a6e3-48bbfa7069a7
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: helaw
ms.openlocfilehash: b7fd3c36825746a009c01c97fb8664e04278159f
ms.sourcegitcommit: 094061b19b0a707eace42ae47f39d7a666364d58
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/08/2017
---
*Se aplica a: Azure Stack Development Kit*

# <a name="add-a-new-azure-stack-tenant-account-in-azure-active-directory"></a>Adición de una nueva cuenta de inquilino de Azure Stack en Azure Active Directory
Después de [implementar Azure Stack Development Kit](azure-stack-run-powershell-script.md), necesitará una cuenta de usuario inquilino para explorar el portal del inquilino y probar las ofertas y los planes. Puede crear una cuenta de inquilino con [Azure Portal](#create-an-azure-stack-tenant-account-using-the-azure-portal) o con [PowerShell](#create-an-azure-stack-tenant-account-using-powershell).

## <a name="create-an-azure-stack-tenant-account-using-the-azure-portal"></a>Creación de una cuenta de inquilino de Azure Stack con el Portal de Azure+
Debe tener una suscripción de Azure para usar el Portal de Azure.

1. Inicie sesión en [Azure](https://portal.azure.com).
2. En la barra de navegación izquierda de Microsoft Azure, haga clic en **Active Directory**.
3. En la lista de directorios, haga clic en el directorio que desea usar para Azure Stack o cree uno nuevo.
4. En este directorio, haga clic en **Usuarios**.
5. Haga clic en **Agregar usuario**.
6. En el **Asistente para agregar usuario**, en la lista **Tipo de usuario**, elija **nuevo usuario de la organización**.
7. En el cuadro **Nombre de usuario** escriba un nombre para el usuario.
8. En el asistente **@** , seleccione la entrada apropiada.
9. Haga clic en la flecha siguiente.
10. En la página **Perfil de usuario** del asistente, escriba un **Nombre**, **Apellidos** y **Nombre para mostrar**.
11. En la lista **Rol**, seleccione **Usuario**.
12. Haga clic en la flecha siguiente.
13. En la página **Obtener contraseña temporal**, haga clic en **Crear**.
14. Copie la **nueva contraseña**.
15. Inicie sesión en Microsoft Azure con la nueva cuenta. Cambie la contraseña cuando se le solicite.
16. Inicie sesión en `https://portal.local.azurestack.external` con la nueva cuenta para ver el portal del inquilino.

## <a name="create-an-azure-stack-tenant-account-using-powershell"></a>Creación de una cuenta de inquilino de Azure Stack con PowerShell
Si no tiene una suscripción de Azure, no puede usar Azure Portal para agregar una cuenta de usuario de inquilino. En este caso, puede usar el módulo Azure Active Directory para Windows PowerShell.

> [!NOTE]
> Si usa una cuenta Microsoft (Live ID) para implementar Azure Stack Development Kit, no se puede usar PowerShell de AAD para crear la cuenta de inquilino. 
> 
> 

1. Instale el [Ayudante para el inicio de sesión de Microsoft Online Services para profesionales de TI (RTW)](https://www.microsoft.com/en-us/download/details.aspx?id=41950).
2. Instale el [módulo de Azure Active Directory para Windows PowerShell (versión de 64 bits)](http://go.microsoft.com/fwlink/p/?linkid=236297) y ábralo.
3. Ejecute los siguientes cmdlets:

    ```powershell
    # Provide the AAD credential you use to deploy Azure Stack Development Kit

            $msolcred = get-credential

    # Add a tenant account "Tenant Admin <username>@<yourdomainname>" with the initial password "<password>".

            connect-msolservice -credential $msolcred
            $user = new-msoluser -DisplayName "Tenant Admin" -UserPrincipalName <username>@<yourdomainname> -Password <password>
            Add-MsolRoleMember -RoleName "Company Administrator" -RoleMemberType User -RoleMemberObjectId $user.ObjectId

    ```

1. Inicie sesión en Microsoft Azure con la nueva cuenta. Cambie la contraseña cuando se le solicite.
2. Inicie sesión en `https://portal.local.azurestack.external` con la nueva cuenta para ver el portal del inquilino.

