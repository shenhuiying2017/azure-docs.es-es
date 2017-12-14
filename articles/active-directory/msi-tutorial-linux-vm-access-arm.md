---
title: "Uso de MSI en una máquina virtual Linux para acceder a Azure Resource Manager"
description: "Tutorial que le guía a través del proceso para usar Managed Service Identity (MSI) con una máquina virtual Linux para acceder a Azure Resource Manager."
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
ms.openlocfilehash: 391b9297ee9cf30cded0f5cb6e91d728d196d6b5
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="use-a-linux-vm-managed-service-identity-msi-to-access-azure-resource-manager"></a>Uso de una identidad de servicio administrada con una máquina virtual Linux para acceder a Azure Resource Manager

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

Este tutorial muestra cómo habilitar Managed Service Identity (MSI) para una máquina virtual Linux y, a continuación, utilizar esa identidad para tener acceso a la API de Azure Resource Manager. Las identidades de MSI son administradas automáticamente por Azure y le permiten autenticar los servicios que admiten la autenticación de Azure AD sin necesidad de insertar credenciales en el código. Aprenderá a:

> [!div class="checklist"]
> * Habilitar MSI en una máquina virtual Linux 
> * Concesión a una máquina virtual de acceso a un grupo de recursos en Azure Resource Manager 
> * Obtención de un token de acceso mediante la identidad de máquina virtual y su uso para llamar a Azure Resource Manager 

## <a name="prerequisites"></a>Requisitos previos

[!INCLUDE [msi-qs-configure-prereqs](../../includes/active-directory-msi-qs-configure-prereqs.md)]

[!INCLUDE [msi-tut-prereqs](../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="sign-in-to-azure"></a>Inicio de sesión en Azure

Inicie sesión en Azure Portal en [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-linux-virtual-machine-in-a-new-resource-group"></a>Creación de una máquina virtual Linux en un nuevo grupo de recursos

En este tutorial, vamos a crear una nueva máquina virtual Linux. También puede habilitar MSI en una máquina virtual existente.

1. Haga clic en el botón **Nuevo** de la esquina superior izquierda de Azure Portal.
2. Seleccione **Compute**y, después, seleccione **Ubuntu Server 16.04 LTS**.
3. Escriba la información de la máquina virtual. En **Tipo de autenticación**, seleccione **Clave pública SSH** o **Contraseña**. Las credenciales creadas le permiten iniciar sesión en la máquina virtual.

    ![Texto alternativo de imagen](media/msi-tutorial-linux-vm-access-arm/msi-linux-vm.png)

4. Elija una **Suscripción** para la máquina virtual en la lista desplegable.
5. Para seleccionar un nuevo **Grupo de recursos** en el que desearía crear la máquina virtual, elija **Crear nuevo**. Cuando haya terminado, haga clic en **Aceptar**.
6. Seleccione el tamaño de la máquina virtual. Para ver más tamaños, seleccione **Ver todo** o cambie el filtro de tipo de disco admitido. En la hoja de configuración, conserve los valores predeterminados y haga clic en **Aceptar**.

## <a name="enable-msi-on-your-vm"></a>Habilitación de MSI en la máquina virtual

Una identidad MSI de máquina virtual le permite obtener tokens de acceso de Azure AD sin tener que incluir las credenciales en el código. Al habilitar MSI, se hacen dos cosas en segundo plano: se instala la extensión MSI en la máquina virtual y se habilita Managed Service Identity para la máquina virtual.  

1. Seleccione la **máquina virtual** en la que desee habilitar MSI.
2. En la barra de navegación de la izquierda, haga clic en **Configuración**.
3. Verá **Managed Service Identity**. Para registrar y habilitar MSI, seleccione **Sí**; si desea deshabilitarla, elija No.
4. No olvide hacer clic en **Guardar** para guardar la configuración.

    ![Texto alternativo de imagen](media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

5. Si desea comprobar las extensiones que están en esta **máquina virtual Linux**, haga clic en **Extensiones**. Si MSI está habilitado, **ManagedIdentityExtensionforLinux** aparece en la lista.

    ![Texto alternativo de imagen](media/msi-tutorial-linux-vm-access-arm/msi-extension-value.png)

## <a name="grant-your-vm-access-to-a-resource-group-in-azure-resource-manager"></a>Concesión a una máquina virtual de acceso a un grupo de recursos en Azure Resource Manager 

Con MSI, el código puede obtener tokens de acceso para autenticarse en los recursos que admitan la autenticación de Azure AD. La API de Azure Resource Manager es compatible con la autenticación de Azure AD. En primer lugar, es necesario conceder acceso a la identidad de esta máquina virtual a un recurso en Azure Resource Manager, en este caso, el grupo de recursos que contiene la máquina virtual.  

1. Vaya a la pestaña de **Grupos de recursos**.
2. Seleccione el **grupo de recursos** concreto que creó anteriormente.
3. Vaya a **Control de acceso (IAM)** en el panel izquierdo.
4. Haga clic para **Agregar** una nueva asignación de roles para la máquina virtual. En **Rol**, elija **Lector**.
5. En la lista desplegable siguiente, **Asigne acceso** al recurso **Máquina Virtual**.
6. A continuación, asegúrese de que la suscripción adecuada aparece en la lista desplegable **Suscripción**. Y en **Grupo de recursos**, seleccione **Todos los grupos de recursos**.
7. Por último, en **Seleccionar**, elija la máquina virtual Linux en la lista desplegable y haga clic en **Guardar**.

    ![Texto alternativo de imagen](media/msi-tutorial-linux-vm-access-arm/msi-permission-linux.png)

## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-call-resource-manager"></a>Obtenga un token de acceso con la identidad de la máquina virtual y úselo para llamar a Resource Manager 

Para completar estos pasos, necesitará un cliente SSH. Si usa Windows, puede usar el cliente SSH en el [Subsistema de Windows para Linux](https://msdn.microsoft.com/commandline/wsl/about). Si necesita ayuda para configurar las claves del cliente de SSH, consulte [Uso de SSH con Windows en Azure](../virtual-machines/linux/ssh-from-windows.md) o [Creación y uso de un par de claves SSH pública y privada para máquinas virtuales Linux en Azure](../virtual-machines/linux/mac-create-ssh-keys.md).

1. En el portal, vaya a la máquina virtual Linux y, en **Información general**, haga clic en **Conectar**.  
2. **Conéctese** a la máquina virtual con el cliente SSH que elija. 
3. En la ventana del terminal, con CURL, realice una solicitud con el punto de conexión local de MSI para obtener un token de acceso para Azure Resource Manager.  
 
    La solicitud CURL para el token de acceso está debajo.  
    
    ```bash
    curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true   
    ```
    
    > [!NOTE]
    > El valor del parámetro "resource" (parámetro) debe coincidir exactamente con el que Azure AD espera.  En el caso del identificador de recurso de Resource Manager, debe incluir la barra diagonal final en el URI. 
    
    La respuesta incluye el token de acceso que necesita para acceder a Azure Resource Manager. 
    
    Respuesta:  

    ```bash
    {"access_token":"eyJ0eXAiOi...",
    "refresh_token":"",
    "expires_in":"3599",
    "expires_on":"1504130527",
    "not_before":"1504126627",
    "resource":"https://management.azure.com",
    "token_type":"Bearer"} 
    ```
    
    Puede usar este token de acceso para acceder a Azure Resource Manager, por ejemplo, para leer los detalles del grupo de recursos al que previamente concedió acceso a la máquina virtual. Reemplace los valores de \<Identificador de suscripción\>, \<Grupo de recursos\> y \<Token de acceso\> por los que ha creado anteriormente. 
    
    > [!NOTE]
    > La dirección URL distingue mayúsculas de minúsculas, por lo tanto, asegúrese de que usa las mismas mayúsculas y minúsculas que al asignar el nombre al grupo de recursos, así como la "G" mayúscula de "resourceGroups".  
    
    ```bash 
    curl https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>?api-version=2016-09-01 -H "Authorization: Bearer <ACCESS TOKEN>" 
    ```
    
    La respuesta devuelta con la información específica del grupo de recursos: 
     
    ```bash
    {"id":"/subscriptions/98f51385-2edc-4b79-bed9-7718de4cb861/resourceGroups/DevTest","name":"DevTest","location":"westus","properties":{"provisioningState":"Succeeded"}} 
    ```
     
## <a name="related-content"></a>Contenido relacionado

- Para obtener información general sobre MSI, consulte [Managed Service Identity overview](../active-directory/msi-overview.md) (Introducción a Managed Service Identity).

Use la siguiente sección de comentarios para proporcionar sus opiniones y ayudarnos a afinar y remodelar el contenido.

