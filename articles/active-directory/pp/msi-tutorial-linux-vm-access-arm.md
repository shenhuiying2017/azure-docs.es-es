---
title: "Uso de MSI asignada por el usuario en una máquina virtual Linux para acceder a Azure Resource Manager"
description: "Tutorial que le guía a través del proceso para usar una identidad de servicio administrada (MSI) asignada por el usuario con una máquina virtual Linux para acceder a Azure Resource Manager."
services: active-directory
documentationcenter: 
author: bryanLa
manager: mtillman
editor: bryanla
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/22/2017
ms.author: arluca
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: bebdccb616a4677fdf36ac257ac36f1827958af7
ms.sourcegitcommit: 176c575aea7602682afd6214880aad0be6167c52
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/09/2018
---
# <a name="use-a-user-assigned-managed-service-identity-msi-on-a-linux-vm-to-access-azure-resource-manager"></a>Uso de una identidad de servicio administrada (MSI) asignada por el usuario en una máquina virtual Linux para acceder a Azure Resource Manager

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Este tutorial explica cómo crear una identidad de servicio administrada (MSI) asignada por el usuario, asignarla a una máquina virtual Linux y, a continuación, utilizar esa identidad para acceder a la API de Azure Resource Manager. 

Azure administra automáticamente las identidades de servicio administradas. Estas identidades permiten autenticarse en servicios que admiten la autenticación de Azure AD, sin necesidad de incluir credenciales en el código.

Aprenderá a:

> [!div class="checklist"]
> * Creación de una MSI asignada por el usuario
> * Asignar la MSI a una máquina virtual Linux 
> * Conceder acceso a la MSI a un grupo de recursos en Azure Resource Manager 
> * Obtener un token de acceso mediante la MSI y usarlo para llamar a Azure Resource Manager 

## <a name="prerequisites"></a>Requisitos previos

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

[!INCLUDE [msi-tut-prereqs](~/includes/active-directory-msi-tut-prereqs.md)]

Para ejecutar los ejemplos de script de la CLI de este tutorial, tiene dos opciones:

- Use [Azure Cloud Shell](~/articles/cloud-shell/overview.md) desde Azure Portal o mediante el botón "Pruébelo", situado en la esquina superior derecha de cada bloque de código.
- [Instale la versión más reciente de la CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.23 o posterior), si prefiere usar una consola de la CLI local.

## <a name="sign-in-to-azure"></a>Inicio de sesión en Azure

Inicie sesión en Azure Portal en [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-linux-virtual-machine-in-a-new-resource-group"></a>Creación de una máquina virtual Linux en un nuevo grupo de recursos

En este tutorial, creará primero una nueva máquina virtual Linux. También puede usar una máquina virtual existente.

1. Haga clic en el botón **Nuevo** de la esquina superior izquierda de Azure Portal.
2. Seleccione **Compute**y, después, seleccione **Ubuntu Server 16.04 LTS**.
3. Escriba la información de la máquina virtual. En **Tipo de autenticación**, seleccione **Clave pública SSH** o **Contraseña**. Las credenciales creadas le permiten iniciar sesión en la máquina virtual.

    ![Creación de máquinas virtuales Linux](~/articles/active-directory/media/msi-tutorial-linux-vm-access-arm/msi-linux-vm.png)

4. Elija una **Suscripción** para la máquina virtual en la lista desplegable.
5. Para seleccionar un nuevo **Grupo de recursos** en el que desearía crear la máquina virtual, elija **Crear nuevo**. Cuando haya terminado, haga clic en **Aceptar**.
6. Seleccione el tamaño de la máquina virtual. Para ver más tamaños, seleccione **Ver todo** o cambie el filtro de tipo de disco admitido. En la hoja de configuración, conserve los valores predeterminados y haga clic en **Aceptar**.

## <a name="create-a-user-assigned-msi"></a>Creación de una MSI asignada por el usuario

1. Si usa la consola de la CLI (en lugar de una sesión de Azure Cloud Shell), primero inicie sesión en Azure. Use una cuenta asociada a la suscripción de Azure en la que desee crear la nueva MSI:

    ```azurecli
    az login
    ```

2. Cree una MSI asignada por el usuario mediante [az identity create](/cli/azure/identity#az_identity_create). El parámetro `-g` especifica el grupo de recursos donde se creó la MSI, mientras que el parámetro `-n` especifica su nombre. Asegúrese de reemplazar los valores de los parámetros `<RESOURCE GROUP>` y `<MSI NAME>` con sus propios valores:

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <MSI NAME>
    ```

    La respuesta contiene detalles de la MSI asignada por el usuario que se ha creado, de forma similar al ejemplo siguiente. Anote el valor `id` para la MSI, ya que se usará en el paso siguiente:

    ```json
    {
    "clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
    "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>/credentials?tid=5678&oid=9012&aid=12344643-8088-4d70-9532-c3a0fdc190fz",
    "id": "/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>",
    "location": "westcentralus",
    "name": "<MSI NAME>",
    "principalId": "9012",
    "resourceGroup": "<RESOURCE GROUP>",
    "tags": {},
    "tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533bl",
    "type": "Microsoft.ManagedIdentity/userAssignedIdentities"
    }
    ```

## <a name="assign-your-user-assigned-msi-to-your-linux-vm"></a>Asignación de la MSI asignada por el usuario a la máquina virtual Linux

A diferencia de una MSI asignada por el sistema, los clientes pueden usar una MSI asignada por el usuario en varios recursos de Azure. En este tutorial, la asignará a una sola máquina virtual. También puede asignarla a más de una máquina virtual.

Asigne la MSI asignada por el usuario a la máquina virtual Linux con [az vm assign-identity](/cli/azure/vm#az_vm_assign_identity). Asegúrese de reemplazar los valores de los parámetros `<RESOURCE GROUP>` y `<VM NAME>` con sus propios valores. Use la propiedad `id` devuelta en el paso anterior para el valor del parámetro `--identities`:

```azurecli-interactive
az vm assign-identity -g <RESOURCE GROUP> -n <VM NAME> --identities "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>"
```

## <a name="grant-your-user-assigned-msi-access-to-a-resource-group-in-azure-resource-manager"></a>Concesión a la MSI asignada por el usuario de acceso a un grupo de recursos en Azure Resource Manager 

La MSI proporciona el código con un token de acceso para autenticarse en API de recursos que admitan la autenticación de Azure AD. En este tutorial, el código accede a la API de Azure Resource Manager. 

No obstante, antes de que el código pueda acceder a la API, debe conceder a la identidad de MSI acceso a un recurso en Azure Resource Manager. En este caso, se trata del grupo de recursos que contiene la máquina virtual. Asegúrese de reemplazar los valores de los parámetros `<CLIENT ID>`, `<SUBSCRIPTION ID>` y `<RESOURCE GROUP>` con sus propios valores. Reemplace `<CLIENT ID>` por la propiedad `clientId` devuelta por el comando `az identity create` en [Creación de una MSI asignada por el usuario](#create-a-user-assigned-msi): 

```azurecli-interactive
az role assignment create --assignee <CLIENT ID> --role ‘Reader’ --scope "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESOURCE GROUP> "
```

La respuesta contiene detalles de la asignación de roles que se ha creado, de forma similar al ejemplo siguiente:

```json
{
  "id": "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Authorization/roleAssignments/b402bd74-157f-425c-bf7d-zed3a3a581ll",
  "name": "b402bd74-157f-425c-bf7d-zed3a3a581ll",
  "properties": {
    "principalId": "f5fdfdc1-ed84-4d48-8551-999fb9dedfbl",
    "roleDefinitionId": "/subscriptions/<SUBSCRIPTION ID>/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7",
    "scope": "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>"
  },
  "resourceGroup": "<RESOURCE GROUP>",
  "type": "Microsoft.Authorization/roleAssignments"
}

```

## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-call-resource-manager"></a>Obtenga un token de acceso con la identidad de la máquina virtual y úselo para llamar a Resource Manager 

En el resto del tutorial, vamos a trabajar desde la máquina virtual que se creó anteriormente.

Para completar estos pasos, necesitará un cliente SSH. Si usa Windows, puede usar el cliente SSH en el [Subsistema de Windows para Linux](https://msdn.microsoft.com/commandline/wsl/about). Si necesita ayuda para configurar las claves del cliente de SSH, consulte [Uso de SSH con Windows en Azure](~/articles/virtual-machines/linux/ssh-from-windows.md) o [Creación y uso de un par de claves SSH pública y privada para máquinas virtuales Linux en Azure](~/articles/virtual-machines/linux/mac-create-ssh-keys.md).

1. En Azure Portal, vaya a **Máquinas virtuales**, vaya a la máquina virtual Linux y, a continuación, desde la página **Información general**, haga clic en **Conectar** en la parte superior. Copie la cadena para conectarse a la máquina virtual.
2. **Conéctese** a la máquina virtual con el cliente SSH que elija.  
3. En la ventana del terminal, con CURL, realice una solicitud con el punto de conexión local de MSI para obtener un token de acceso para Azure Resource Manager.  

   En el ejemplo siguiente, se muestra la solicitud CURL para adquirir un token de acceso. Asegúrese de reemplazar `<CLIENT ID>` por la propiedad `clientId` devuelta por el comando `az identity create` en [Creación de una MSI asignada por el usuario](#create-a-user-assigned-msi): 
    
   ```bash
   curl -H Metadata:true "http://localhost:50342/oauth2/token?resource=https%3A%2F%2Fmanagement.azure.com/&client_id=<CLIENT ID>"   
   ```
    
    > [!NOTE]
    > El valor del parámetro `resource` debe coincidir exactamente con el que Azure AD espera. Al usar el identificador de recurso de Azure Resource Manager, debe incluir la barra diagonal final en el URI. 
    
    La respuesta incluye el token de acceso que necesita para acceder a Azure Resource Manager. 
    
    Ejemplo de respuesta:  

    ```bash
    {
    "access_token":"eyJ0eXAiOi...",
    "refresh_token":"",
    "expires_in":"3599",
    "expires_on":"1504130527",
    "not_before":"1504126627",
    "resource":"https://management.azure.com",
    "token_type":"Bearer"
    } 
    ```

4. Ahora use este token de acceso para acceder a Azure Resource Manager y lea las propiedades del grupo de recursos al que previamente concedió acceso a la MSI asignada por el usuario. No olvide reemplazar `<SUBSCRIPTION ID>` y `<RESOURCE GROUP>` por los valores especificados anteriormente, y `<ACCESS TOKEN>` por el token devuelto en el paso anterior.

    > [!NOTE]
    > La dirección URL distingue mayúsculas de minúsculas, por lo tanto, asegúrese de que usa las mismas mayúsculas y minúsculas que al asignar el nombre al grupo de recursos, así como la "G" mayúscula de `resourceGroups`.  

    ```bash 
    curl https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>?api-version=2016-09-01 -H "Authorization: Bearer <ACCESS TOKEN>" 
    ```

    La respuesta contiene la información específica del grupo de recursos, de forma similar al ejemplo siguiente: 

    ```bash
    {
    "id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/DevTest",
    "name":"DevTest",
    "location":"westus",
    "properties":{"provisioningState":"Succeeded"}
    } 
    ```
    
## <a name="next-steps"></a>pasos siguientes

- Para obtener información general sobre MSI, consulte [Managed Service Identity overview](msi-overview.md) (Introducción a Managed Service Identity).

Use la siguiente sección de comentarios para proporcionar sus opiniones y ayudarnos a afinar y remodelar el contenido.
