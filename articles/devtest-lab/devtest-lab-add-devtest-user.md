---
title: "Adición de propietarios y usuarios en Azure DevTest Labs | Microsoft Docs"
description: Agregar propietarios y usuarios en Azure DevTest Labs mediante Azure Portal o PowerShell
services: devtest-lab,virtual-machines
documentationcenter: na
author: craigcaseyMSFT
manager: douge
editor: 
ms.assetid: 4f51d9a5-2702-45f0-a2d5-a3635b58c416
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/11/2017
ms.author: v-craic
ms.openlocfilehash: 2921356f848739d602807236006f9867eaa2a4e4
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/02/2018
---
# <a name="add-owners-and-users-in-azure-devtest-labs"></a>Adición de propietarios y usuarios en Azure DevTest Labs
> [!VIDEO https://channel9.msdn.com/Blogs/Azure/How-to-set-security-in-your-DevTest-Lab/player]
> 
> 

El acceso a Azure DevTest Labs se controla mediante el [control de acceso basado en rol (RBAC) de Azure](../active-directory/role-based-access-control-what-is.md). Mediante RBAC, puede repartir las tareas del equipo en *roles* y conceder a los usuarios únicamente el nivel de acceso que necesitan para realizar su trabajo. Tres de estos roles RBAC son *Propietario*, *Usuario de DevTest Labs* y *Colaborador*. En este artículo, aprenderá qué acciones se pueden realizar en cada uno de los tres roles principales de RBAC. A partir de ahí, aprenderá cómo agregar usuarios a un laboratorio a través del portal o a través de un script de PowerShell y cómo agregar los usuarios en el nivel de suscripción.

## <a name="actions-that-can-be-performed-in-each-role"></a>Acciones que se pueden realizar en cada rol
Hay tres roles principales que puede asignar a un usuario:

* Propietario
* Usuario de DevTest Labs
* Colaborador

En la tabla siguiente se muestran las acciones que pueden realizar los usuarios pertenecientes a cada uno de estos roles:

| **Acciones que pueden realizar los usuarios de este rol** | **Usuario de DevTest Labs** | **Propietario** | **Colaborador** |
| --- | --- | --- | --- |
| **Tareas de laboratorio** | | | |
| Agregar usuarios a un laboratorio |Sin  |Sí |Sin  |
| Actualizar la configuración de costo |Sin  |Sí |Sí |
| **Tareas base de máquina virtual** | | | |
| Agregar y quitar imágenes personalizadas |Sin  |Sí |Sí |
| Agregar, actualizar y eliminar las fórmulas |Sí |Sí |Sí |
| Incluir en la lista de permitidos imágenes de Azure Marketplace |Sin  |Sí |Sí |
| **Tareas de la máquina virtual** | | | |
| Crear máquinas virtuales |Sí |Sí |Sí |
| Iniciar, detener y eliminar máquinas virtuales |Solo las máquinas virtuales creadas por el usuario |Sí |Sí |
| Actualizar directivas de máquinas virtuales |Sin  |Sí |Sí |
| Agregar discos de datos o quitarlos en máquinas virtuales |Solo las máquinas virtuales creadas por el usuario |Sí |Sí |
| **Tareas de artefacto** | | | |
| Agregar y quitar repositorios de artefacto |Sin  |Sí |Sí |
| Aplicar artefactos |Sí |Sí |Sí |

> [!NOTE]
> Cuando un usuario crea una máquina virtual, a ese usuario se le asigna automáticamente el rol **Propietario** de la máquina virtual creada.
> 
> 

## <a name="add-an-owner-or-user-at-the-lab-level"></a>Agregar un propietario o usuario en el nivel de laboratorio
Los propietarios y los usuarios se pueden agregar en el nivel de laboratorio a través de Azure Portal. Esto incluye a los usuarios externos con una [cuenta Microsoft (MSA)](devtest-lab-faq.md#what-is-a-microsoft-account)válida.
Los siguientes pasos le guiarán a través del proceso de agregación de un propietario o usuario a un laboratorio de Azure DevTest Labs:

1. Inicie sesión en el [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Seleccione **Más servicios** y, luego, **DevTest Labs** en la lista.
3. En la lista de laboratorios, seleccione el laboratorio que desee.
4. En la hoja del laboratorio, seleccione **Configuración**. 
5. En la hoja **Configuración**, seleccione **Usuarios**.
6. En la hoja **Usuarios**, seleccione **+Agregar**.
   
    ![Agregar usuario](./media/devtest-lab-add-devtest-user/devtest-users-blade.png)
7. En la hoja **Seleccionar un rol** , seleccione el rol que desee. En la sección [Acciones que se pueden realizar en cada rol](#actions-that-can-be-performed-in-each-role) se enumeran las distintas acciones que pueden realizar los usuarios de los roles de Propietario, Usuario de DevTest y Colaborador.
8. En la hoja **Agregar usuarios** , escriba la dirección de correo electrónico o el nombre del usuario que desea agregar en el rol especificado. Si no se encuentra el usuario, aparecerá un mensaje de error que explica el problema. Si se encuentra el usuario, ese usuario se muestra y se selecciona. 
9. Elija **Seleccionar**.
10. Seleccione **Aceptar** para cerrar la hoja **Agregar acceso**.
11. Cuando vuelva a la hoja **Usuarios** , el usuario ya se habrá agregado.  

## <a name="add-an-external-user-to-a-lab-using-powershell"></a>Incorporación de un usuario externo a un laboratorio mediante PowerShell
Además de agregar usuarios en Azure Portal, puede agregar un usuario externo al laboratorio mediante un script de PowerShell. En el ejemplo siguiente, basta con modificar los valores de parámetro en el comentario **Values to change** (Valores para cambiar).
Puede recuperar los valores `subscriptionId`, `labResourceGroup` y `labName` de la hoja de laboratorio en Azure Portal.

> [!NOTE]
> El script de ejemplo supone que el usuario especificado se ha agregado como invitado a Active Directory y se producirá un error si este no es el caso. Para agregar un usuario que no está en Active Directory a un laboratorio, use Azure Portal para asignar el usuario a un rol, tal como se muestra en la sección [Agregar un propietario o usuario en el nivel de laboratorio](#add-an-owner-or-user-at-the-lab-level).   
> 
> 

    # Add an external user in DevTest Labs user role to a lab
    # Ensure that guest users can be added to the Azure Active directory:
    # https://azure.microsoft.com/en-us/documentation/articles/active-directory-create-users/#set-guest-user-access-policies

    # Values to change
    $subscriptionId = "<Enter Azure subscription ID here>"
    $labResourceGroup = "<Enter lab's resource name here>"
    $labName = "<Enter lab name here>"
    $userDisplayName = "<Enter user's display name here>"

    # Log into your Azure account
    Login-AzureRmAccount

    # Select the Azure subscription that contains the lab. 
    # This step is optional if you have only one subscription.
    Select-AzureRmSubscription -SubscriptionId $subscriptionId

    # Retrieve the user object
    $adObject = Get-AzureRmADUser -SearchString $userDisplayName

    # Create the role assignment. 
    $labId = ('subscriptions/' + $subscriptionId + '/resourceGroups/' + $labResourceGroup + '/providers/Microsoft.DevTestLab/labs/' + $labName)
    New-AzureRmRoleAssignment -ObjectId $adObject.Id -RoleDefinitionName 'DevTest Labs User' -Scope $labId

## <a name="add-an-owner-or-user-at-the-subscription-level"></a>Agregar un propietario o usuario en el nivel de suscripción
Los permisos de Azure se propagan desde el ámbito primario al secundario en Azure. Por lo tanto, los propietarios de una suscripción de Azure que contiene laboratorios, automáticamente son propietarios de estos. También serán propietarios de las máquinas virtuales y de los demás recursos creados por los usuarios del laboratorio y el servicio Azure DevTest Labs. 

Puede agregar propietarios adicionales a un laboratorio a través de la hoja del laboratorio en [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040). Sin embargo, el ámbito de administración del propietario agregado será más limitado que el ámbito del propietario de la suscripción. Por ejemplo, los propietarios agregados no tienen acceso completo a algunos de los recursos que el servicio DevTest Labs crea en la suscripción. 

Para agregar un propietario a una suscripción de Azure, siga estos pasos:

1. Inicie sesión en el [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Seleccione **Más servicios** y, después, seleccione **Suscripciones** en la lista.
3. Seleccione la suscripción deseada.
4. Seleccione el icono **Acceder** . 
   
    ![Usuarios de acceso](./media/devtest-lab-add-devtest-user/access-users.png)
5. En la hoja **Usuarios**, seleccione **Agregar**.
   
    ![Agregar usuario](./media/devtest-lab-add-devtest-user/devtest-users-blade.png)
6. En la hoja **Seleccionar rol**, seleccione **Propietario**.
7. En la hoja **Agregar usuarios** , escriba la dirección de correo electrónico o el nombre del usuario que desea agregar como propietario. Si no se encuentra el usuario, aparecerá un mensaje de error que explica el problema. Si se encuentra el usuario, dicho usuario se mostrará en el cuadro de texto **Usuario** .
8. Seleccione el nombre de usuario encontrado.
9. Elija **Seleccionar**.
10. Seleccione **Aceptar** para cerrar la hoja **Agregar acceso**.
11. Cuando vuelva a la hoja **Usuarios** , el usuario ya se habrá agregado como propietario. Este usuario será ya propietario de cualquier laboratorio creado con esta suscripción y, por lo tanto, podrá realizar tareas de propietario. 

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

