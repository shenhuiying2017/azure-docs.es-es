---
title: "Uso de una identidad de servicio administrada (MSI) de máquina virtual Windows para acceder a Azure Resource Manager"
description: "Tutorial que indica cómo usar una identidad de servicio administrada (MSI) en una máquina virtual Windows para acceder a Azure Resource Manager."
services: active-directory
documentationcenter: 
author: bryanla
manager: mtillman
editor: bryanla
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/20/2017
ms.author: bryanla
ms.openlocfilehash: b5ba403c4e152770eeacb32d4a8d1980cf039396
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="use-a-windows-vm-managed-service-identity-msi-to-access-resource-manager"></a>Uso de una identidad de servicio administrada (MSI) en una máquina virtual Windows para acceder a Resource Manager

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

En este tutorial se muestra cómo habilitar Managed Service Identity (MSI) para una máquina virtual Windows. Luego, podrá usar esa identidad para acceder a la API de Azure Resource Manager. Las identidades de MSI son administradas automáticamente por Azure y le permiten autenticar los servicios que admiten la autenticación de Azure AD sin necesidad de insertar credenciales en el código. Aprenderá a:

> [!div class="checklist"]
> * Habilitar MSI en una máquina virtual Windows 
> * Concesión a una máquina virtual de acceso a un grupo de recursos en Azure Resource Manager 
> * Obtención de un token de acceso mediante la identidad de máquina virtual y su uso para llamar a Azure Resource Manager

## <a name="prerequisites"></a>Requisitos previos

[!INCLUDE [msi-qs-configure-prereqs](../../includes/active-directory-msi-qs-configure-prereqs.md)]

[!INCLUDE [msi-tut-prereqs](../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="sign-in-to-azure"></a>Inicio de sesión en Azure
Inicie sesión en Azure Portal en [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-windows-virtual-machine-in-a-new-resource-group"></a>Creación de una máquina virtual Windows en un nuevo grupo de recursos

En este tutorial, se crea una nueva máquina virtual Windows.  También puede habilitar MSI en una máquina virtual existente.

1.  Haga clic en el botón **Nuevo** de la esquina superior izquierda de Azure Portal.
2.  Seleccione **Compute**y, después, seleccione **Windows Server 2016 Datacenter**. 
3.  Escriba la información de la máquina virtual. El **nombre de usuario** y la **contraseña** creados aquí son las credenciales que se usan para iniciar sesión en la máquina virtual.
4.  Elija la **suscripción** adecuada de la máquina virtual en la lista desplegable.
5.  Para seleccionar un nuevo **grupo de recursos** en el que crear la máquina virtual, elija **Crear nuevo**. Cuando haya terminado, haga clic en **Aceptar**.
6.  Seleccione el tamaño de la máquina virtual. Para ver más tamaños, seleccione **Ver todo** o cambie el filtro **Supported disk type** (Tipo de disco admitido). En la página de configuración, conserve los valores predeterminados y haga clic en **Aceptar**.

    ![Texto alternativo de imagen](media/msi-tutorial-windows-vm-access-arm/msi-windows-vm.png)

## <a name="enable-msi-on-your-vm"></a>Habilitación de MSI en la máquina virtual 

Una identidad MSI de máquina virtual le permite obtener tokens de acceso de Azure AD sin tener que poner las credenciales en el código. Al habilitar MSI se indica a Azure que cree una identidad administrada para la máquina virtual. Al habilitar MSI suceden dos cosas en segundo plano: se instala la extensión MSI en la máquina virtual y se habilita MSI en Azure Resource Manager.

1.  Seleccione la **máquina virtual** en la que quiera habilitar MSI.  
2.  En la barra de navegación de la izquierda, haga clic en **Configuración**. 
3.  Verá **Managed Service Identity**. Para registrar y habilitar MSI, seleccione **Sí**; si desea deshabilitarla, elija No. 
4.  No olvide hacer clic en **Guardar** para guardar la configuración.  
    ![Texto alternativo de imagen](media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

5. Si desea comprobar las extensiones que están en esta máquina virtual, haga clic en **Extensiones**. Si MSI está habilitada, en la lista aparece **ManagedIdentityExtensionforWindows**.

    ![Texto alternativo de imagen](media/msi-tutorial-windows-vm-access-arm/msi-windows-extension.png)

## <a name="grant-your-vm-access-to-a-resource-group-in-resource-manager"></a>Concesión de acceso a la máquina virtual a un grupo de recursos en Resource Manager
Con MSI, el código puede obtener tokens de acceso para autenticarse en los recursos que admitan la autenticación de Azure AD.  Azure Resource Manager admite la autenticación de Azure AD.  En primer lugar, es necesario conceder acceso a la identidad de esta máquina virtual a un recurso de Resource Manager, en este caso, al grupo de recursos que contiene la máquina virtual.  

1.  Vaya a la pestaña de **Grupos de recursos**. 
2.  Seleccione el **grupo de recursos** específico que creó para su **máquina virtual Windows**. 
3.  Vaya a **Control de acceso (IAM)** en el panel izquierdo. 
4.  A continuación, **agregue** una nueva asignación de rol para su **máquina virtual Windows**.  En **Rol**, elija **Lector**. 
5.  En la lista desplegable siguiente, **asigne acceso** al recurso **Máquina virtual**. 
6.  A continuación, asegúrese de que la suscripción adecuada aparece en la lista desplegable **Suscripción**. Y en **Grupo de recursos**, seleccione **Todos los grupos de recursos**. 
7.  Por último, en **Seleccionar**, elija la máquina virtual Windows en la lista desplegable y haga clic en **Guardar**.

    ![Texto alternativo de imagen](media/msi-tutorial-windows-vm-access-arm/msi-windows-permissions.png)

## <a name="get-an-access-token-using-the-vm-identity-and-use-it-to-call-azure-resource-manager"></a>Obtención de un token de acceso mediante la identidad de máquina virtual y su uso para llamar a Azure Resource Manager 

Deberá usar **PowerShell** en esta parte.  Si no lo ha instalado, descárguelo [aquí](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-4.3.1). 

1.  En el portal, vaya a **Virtual Machines** y diríjase a la máquina virtual Windows. A continuación, en **Introducción**, haga clic en **Conectar**. 
2.  Escriba su **nombre de usuario** y **contraseña** que agregó cuando creó la máquina virtual Windows. 
3.  Ahora que ha creado una **conexión a Escritorio remoto** con la máquina virtual, abra **PowerShell** en la sesión remota. 
4.  Mediante Invoke-WebRequest de PowerShell, realice una solicitud al punto de conexión de MSI local para obtener un token de acceso para Azure Resource Manager.

    ```powershell
       $response = Invoke-WebRequest -Uri http://localhost:50342/oauth2/token -Method GET -Body @{resource="https://management.azure.com/"} -Headers @{Metadata="true"}
    ```
    
    > [!NOTE]
    > El valor del parámetro "resource" debe coincidir exactamente con el que Azure AD espera. Al usar el id. de recurso de Azure Resource Manager, debe incluir la barra diagonal final en el URI.
    
    A continuación, extraiga la respuesta completa, que se almacena como una cadena con formato de notación de objetos JavaScript (JSON) en el objeto $response. 
    
    ```powershell
    $content = $response.Content | ConvertFrom-Json
    ```
    Luego, extraiga el token de acceso de la respuesta.
    
    ```powershell
    $ArmToken = $content.access_token
    ```
    
    Por último, llame a Azure Resource Manager mediante el token de acceso. En este ejemplo, se usará también Invoke-WebRequest de PowerShell para realizar la llamada a Azure Resource Manager y se incluirá el token de acceso en el encabezado de autorización.
    
    ```powershell
    (Invoke-WebRequest -Uri https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>?api-version=2016-06-01 -Method GET -ContentType "application/json" -Headers @{ Authorization ="Bearer $ArmToken"}).content
    ```
    > [!NOTE] 
    > La dirección URL distingue mayúsculas de minúsculas, por lo tanto, asegúrese de que usa las mismas mayúsculas y minúsculas que al asignar el nombre al grupo de recursos, así como la "G" mayúscula de "resourceGroups".
        
    El siguiente comando devuelve los detalles del grupo de recursos:

    ```powershell
    {"id":"/subscriptions/98f51385-2edc-4b79-bed9-7718de4cb861/resourceGroups/DevTest","name":"DevTest","location":"westus","properties":{"provisioningState":"Succeeded"}}
    ```

## <a name="related-content"></a>Contenido relacionado

- Para obtener información general sobre MSI, consulte [Managed Service Identity overview](../active-directory/msi-overview.md) (Introducción a Managed Service Identity).

Use la siguiente sección de comentarios para proporcionar sus opiniones y ayudarnos a afinar y remodelar el contenido.

