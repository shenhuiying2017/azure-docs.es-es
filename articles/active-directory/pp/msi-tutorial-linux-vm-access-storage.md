---
title: "Uso de una MSI asignada por el usuario en una máquina virtual Linux para acceder a Azure Storage"
description: "Tutorial que le guía mediante el proceso para usar una identidad de servicio administrada (MSI) asignada por el usuario con una máquina virtual Linux para acceder a Azure Storage."
services: active-directory
documentationcenter: 
author: bryanLa
manager: mtillman
editor: arluca
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/15/2017
ms.author: bryanla
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 91fe06825d1db586b715617241b0ca39115414c0
ms.sourcegitcommit: 48fce90a4ec357d2fb89183141610789003993d2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/12/2018
---
# <a name="use-a-user-assigned-managed-service-identity-msi-on-a-linux-vm-to-access-azure-storage"></a>Uso de una identidad de servicio administrada (MSI) asignada por el usuario en una máquina virtual Linux para acceder a Azure Storage

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

En este tutorial se muestra cómo crear y utilizar una identidad de servicio administrada (MSI) asignada por el usuario en una máquina virtual Linux y, a continuación, usar esa identidad para acceder a Azure Storage. Aprenderá a:

> [!div class="checklist"]
> * Crear una identidad de servicio administrada (MSI) asignada por el usuario de la máquina virtual
> * Asignar la MSI asignada por el usuario a una máquina virtual Linux
> * Conceder el acceso MSI a una instancia de Azure Storage
> * Obtener un token de acceso mediante la identidad de servicio administrada asignada por el usuario y usarla para acceder a Azure Storage

## <a name="prerequisites"></a>Requisitos previos

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

[!INCLUDE [msi-tut-prereqs](~/includes/active-directory-msi-tut-prereqs.md)]

Para ejecutar los ejemplos de script de la CLI de este tutorial, tiene dos opciones:

- Use [Azure Cloud Shell](~/articles/cloud-shell/overview.md) desde Azure Portal o mediante el botón "Pruébelo", situado en la esquina superior derecha de cada bloque de código.
- [Instale la versión más reciente de la CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.23 o posterior), si prefiere usar una consola de la CLI local.

## <a name="sign-in-to-azure"></a>Inicio de sesión en Azure

Inicie sesión en Azure Portal en [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-linux-virtual-machine-in-a-new-resource-group"></a>Crear una máquina virtual Linux en un nuevo grupo de recursos

En primer lugar, cree una nueva máquina virtual Linux. También puede habilitar MSI en una máquina virtual existente, si lo prefiere.

1. Haga clic en el botón **+/Crear nuevo servicio** de la esquina superior izquierda de Azure Portal.
2. Seleccione **Compute**y, después, seleccione **Ubuntu Server 16.04 LTS**.
3. Escriba la información de la máquina virtual. En **Tipo de autenticación**, seleccione **Clave pública SSH** o **Contraseña**. Las credenciales creadas le permiten iniciar sesión en la máquina virtual.

    ![Texto alternativo de imagen](~/articles/active-directory/media/msi-tutorial-linux-vm-access-arm/msi-linux-vm.png)

4. Elija una **Suscripción** para la máquina virtual en la lista desplegable.
5. Para seleccionar un nuevo **Grupo de recursos** en el que desearía crear la máquina virtual, elija **Crear nuevo**. Cuando haya terminado, haga clic en **Aceptar**.
6. Seleccione el tamaño de la máquina virtual. Para ver más tamaños, seleccione **Ver todo** o cambie el filtro de tipo de disco admitido. En la hoja de configuración, conserve los valores predeterminados y haga clic en **Aceptar**.

## <a name="create-a-user-assigned-msi"></a>Creación de una MSI asignada por el usuario

1. Si usa la consola de la CLI (en lugar de una sesión de Azure Cloud Shell), primero inicie sesión en Azure. Use una cuenta asociada a la suscripción de Azure en la que desee crear la nueva MSI:

    ```azurecli
    az login
    ```

2. Cree una MSI asignada por el usuario mediante el cmdlet [az identity create](/cli/azure/identity#az_identity_create). El parámetro `-g` especifica el grupo de recursos donde se creó la MSI, mientras que el parámetro `-n` especifica su nombre. Asegúrese de reemplazar los valores de los parámetros `<RESOURCE GROUP>` y `<MSI NAME>` por sus propios valores:

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <MSI NAME>
    ```

    La respuesta contiene detalles de la MSI asignada por el usuario que se ha creado, de forma similar al ejemplo siguiente. Anote el valor `id` para la MSI, ya que se usará en el paso siguiente:

    ```json
    {
    "clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
    "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>/credentials?tid=5678&oid=9012&aid=123444643-8088-4d70-9532-c3a0fdc190fz",
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

Asigne la MSI asignada por el usuario a la máquina virtual Linux con el cmdlet [az vm assign-identity](/cli/azure/vm#az_vm_assign_identity). Asegúrese de reemplazar los valores de los parámetros `<RESOURCE GROUP>` y `<VM NAME>` por sus propios valores. Use la propiedad `id` devuelta en el paso anterior para el valor del parámetro `--identities`:

```azurecli-interactive
az vm assign-identity -g <RESOURCE GROUP> -n <VM NAME> --identities "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>"
```

## <a name="create-a-storage-account"></a>Crear una cuenta de almacenamiento 

Si aún no tiene una, creará ahora una cuenta de almacenamiento. También puede omitir este paso y usar una cuenta de almacenamiento existente, si lo prefiere. 

1. Haga clic en el botón **+/Crear nuevo servicio** de la esquina superior izquierda de Azure Portal.
2. Haga clic en **Storage**, a continuación, en **Cuenta de almacenamiento** y se mostrará un nuevo panel "Crear cuenta de almacenamiento".
3. Escriba un **nombre** para la cuenta de almacenamiento, que va a utilizar más adelante.  
4. **Modelo de implementación** y **Clase de cuenta** debe establecerse en "Resource Manager" y "Uso General", respectivamente. 
5. Asegúrese de que **Suscripción** y **Grupo de recursos** coinciden con los que especificó cuando creó la máquina virtual en el paso anterior.
6. Haga clic en **Create**(Crear).

    ![Creación de una nueva cuenta de almacenamiento](~/articles/active-directory/media/msi-tutorial-linux-vm-access-storage/msi-storage-create.png)

## <a name="create-a-blob-container-in-the-storage-account"></a>Creación de un contenedor de blobs en la cuenta de almacenamiento

Dado que los archivos requieren almacenamiento de blobs, necesita crear un contenedor de blobs en el que almacenar el archivo. Después va a cargar y descargar un archivo en el contenedor de blobs, en la nueva cuenta de almacenamiento.

1. Vuelva a la cuenta de almacenamiento recién creada.
2. Haga clic en el vínculo **Contenedores** a la izquierda, en "Blob service".
3. Haga clic en **+ Contenedor** en la parte superior de la página y se abrirá un panel "Nuevo contenedor".
4. Asigne un nombre al contenedor, seleccione un nivel de acceso y, a continuación, haga clic en **Aceptar**. El nombre especificado también se utilizará más adelante en el tutorial. 

    ![Creación de contenedores de almacenamiento](~/articles/active-directory/media/msi-tutorial-linux-vm-access-storage/create-blob-container.png)

5. Cargue un archivo en el contenedor que se acaba de crear haciendo clic en el nombre del contenedor, después, en **Cargar**, seleccione un archivo y haga clic en **Cargar**.

    ![Carga de un archivo de texto](~/articles/active-directory/media/msi-tutorial-linux-vm-access-storage/upload-text-file.png)

## <a name="grant-your-user-assigned-msi-access-to-an-azure-storage-container"></a>Concesión de acceso de MSI asignado por el usuario a un contenedor de Azure Storage

Al usar una MSI, el código puede obtener tokens de acceso para autenticarse en los recursos que admitan la autenticación de Azure AD. En este tutorial, usará Azure Storage.

En primer lugar conceda el acceso a la identidad de MSI a un contenedor de Azure Storage. En este caso, va a utilizar el contenedor que creó anteriormente. Actualice los valores de `<MSI PRINCIPALID>`, `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>`, `<STORAGE ACCOUNT NAME>` y `<CONTAINER NAME>`, según corresponda en su entorno. Reemplace `<CLIENT ID>` por la propiedad `clientId` devuelta por el comando `az identity create` en [Creación de una MSI asignada por el usuario](#create-a-user-assigned-msi):

```azurecli-interactive
az role assignment create --assignee <MSI PRINCIPALID> --role ‘Reader’ --scope "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.Storage/storageAccounts/<STORAGE ACCOUNT NAME>/blobServices/default/containers/<CONTAINER NAME>"
```

La respuesta incluye los detalles de la asignación de roles que se ha creado:

```
{
  "id": "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Authorization/roleAssignments/b402bd74-157f-425c-bf7d-zed3a3a581ll",
  "name": "b402bd74-157f-425c-bf7d-zed3a3a581ll",
  "properties": {
    "principalId": "f5fdfdc1-ed84-4d48-8551-999fb9dedfbl",
    "roleDefinitionId": "/subscriptions/<SUBSCRIPTION ID>/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7",
    "scope": "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.storage/storageAccounts/<STORAGE ACCOUNT NAME>/blogServices/default/<CONTAINER NAME>"
  },
  "resourceGroup": "<RESOURCE GROUP>",
  "type": "Microsoft.Authorization/roleAssignments"
}
```

## <a name="get-an-access-token-using-the-user-assigned-msis-identity-and-use-it-to-call-azure-storage"></a>Obtención de un token de acceso mediante la identidad de MSI asignada por el usuario y su uso para llamar a Azure Storage

En el resto del tutorial, tiene que trabajar desde la máquina virtual que creó anteriormente.

Para completar estos pasos, necesitará un cliente SSH. Si usa Windows, puede usar el cliente SSH en el [Subsistema de Windows para Linux](https://msdn.microsoft.com/commandline/wsl/about). Si necesita ayuda para configurar las claves del cliente de SSH, consulte [Uso de SSH con Windows en Azure](~/articles/virtual-machines/linux/ssh-from-windows.md) o [Creación y uso de un par de claves SSH pública y privada para máquinas virtuales Linux en Azure](~/articles/virtual-machines/linux/mac-create-ssh-keys.md).

1. En Azure Portal, vaya a **Máquinas virtuales**, vaya a la máquina virtual Linux y, a continuación, desde la página **Información general**, haga clic en **Conectar** en la parte superior. Copie la cadena para conectarse a la máquina virtual.
2. **Conéctese** a la máquina virtual con el cliente SSH que elija. 
3. En la ventana del terminal, con CURL, realice una solicitud al punto de conexión local de MSI para obtener un token de acceso para Azure Storage.

   En el ejemplo siguiente, se muestra la solicitud CURL para adquirir un token de acceso. Asegúrese de reemplazar `<CLIENT ID>` por la propiedad `clientId` devuelta por el comando `az identity create` en [Creación de una MSI asignada por el usuario](#create-a-user-assigned-msi):

   ```bash
   curl -H Metadata:true "http://localhost:50342/oauth2/token?resource=https%3A%2F%2Fstorage.azure.com/&client_id=<CLIENT ID>"
   ```

   > [!NOTE]
   > En la solicitud anterior, el valor del parámetro `resource` tiene que coincidir exactamente con el que Azure AD espera. Al usar el identificador de recurso de Azure Storage, debe incluir la barra diagonal final en el URI.
   > En la siguiente respuesta, el elemento access_token se ha abreviado.

   ```bash
   {"access_token":"eyJ0eXAiOiJ...",
   "refresh_token":"",
   "expires_in":"3599",
   "expires_on":"1504130527",
   "not_before":"1504126627",
   "resource":"https://storage.azure.com",
   "token_type":"Bearer"}
   ```

4. Ahora utilice el token de acceso para acceder a Azure Storage, por ejemplo para leer el contenido del archivo de muestra que ha cargado previamente en el contenedor. Reemplace los valores `<STORAGE ACCOUNT>`, `<CONTAINER NAME>` y `<FILE NAME>` por los valores especificados anteriormente, y `<ACCESS TOKEN>` por el token devuelto en el paso anterior.

   ```bash
   curl https://<STORAGE ACCOUNT>.blob.core.windows.net/<CONTAINER NAME>/<FILE NAME>?api-version=2017-11-09 -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   La respuesta incluye el contenido del archivo:

   ```bash
   Hello world! :)
   ```

## <a name="next-steps"></a>pasos siguientes

- Para obtener información general sobre MSI, consulte [Managed Service Identity overview](msi-overview.md) (Introducción a Managed Service Identity).
- Para aprender a hacer lo que se realiza en este tutorial con una credencial SAS de almacenamiento, consulte [Uso de Managed Service Identity en una máquina virtual Linux para tener acceso a Azure Storage a través de una credencial SAS](msi-tutorial-linux-vm-access-storage-sas.md)
- Para más información sobre la característica SAS de una cuenta de Azure Storage, consulte:
  - [Uso de Firmas de acceso compartido (SAS)](~/articles/storage/common/storage-dotnet-shared-access-signature-part-1.md)
  - [Creación de una SAS de servicio](/rest/api/storageservices/Constructing-a-Service-SAS.md)

Use la siguiente sección de comentarios para proporcionar sus opiniones y ayudarnos a afinar y remodelar el contenido.





