---
title: "Uso de una identidad MSI de máquina virtual Linux para acceder a Azure Storage"
description: "Tutorial que indica cómo usar una identidad de servicio administrada (MSI) en una máquina virtual Linux para acceder a Azure Storage."
services: active-directory
documentationcenter: 
author: elkuzmen
manager: mbaldwin
editor: bryanla
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/19/2017
ms.author: elkuzmen
ms.openlocfilehash: 16d96494c2e97604ed2116c6b1a28e099b4e61bd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="use-a-linux-vm-managed-service-identity-to-access-azure-storage"></a>Uso de Managed Service Identity en una máquina virtual Linux para tener acceso a Azure Storage

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

Este tutorial muestra cómo habilitar Managed Service Identity (MSI) para una máquina virtual Linux y, a continuación, usar esa identidad para acceder a las claves de almacenamiento. Puede usar las claves de almacenamiento de la forma habitual al realizar operaciones de almacenamiento, por ejemplo, al usar Storage SDK. En este tutorial se cargan y descargan blobs mediante la CLI de Azure. Aprenderá a:


> [!div class="checklist"]
> * Habilitar MSI en una máquina virtual Linux 
> * Conceder acceso a la máquina virtual a las claves de almacenamiento en Resource Manager
> * Obtener un token de acceso mediante la identidad de la máquina virtual y utilizarlo para recuperar claves de almacenamiento de Resource Manager 


Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

## <a name="sign-in-to-azure"></a>Inicio de sesión en Azure
Inicie sesión en Azure Portal en [https://portal.azure.com](https://portal.azure.com).


## <a name="create-a-linux-virtual-machine-in-a-new-resource-group"></a>Crear una máquina virtual Linux en un nuevo grupo de recursos

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

1. Desplácese hasta el grupo de recursos de la nueva máquina virtual y seleccione la máquina virtual que creó en el paso anterior.
2. En la configuración de máquina virtual, a la izquierda, haga clic en **Configuración**.
3. Para registrar y habilitar MSI, seleccione **Sí**; si desea deshabilitarla, elija No.
4. No olvide hacer clic en **Guardar** para guardar la configuración.

    ![Texto alternativo de imagen](media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

5. Si desea comprobar las extensiones que hay en la máquina virtual, haga clic en **Extensiones**. Si MSI está habilitado, **ManagedIdentityExtensionforLinux** aparece en la lista.

    ![Texto alternativo de imagen](media/msi-tutorial-linux-vm-access-arm/msi-extension-value.png)

## <a name="create-a-storage-account"></a>Crear una cuenta de almacenamiento 

Si aún no tiene una, creará ahora una cuenta de almacenamiento.  También puede omitir este paso y conceder acceso para MSI en la máquina virtual a las claves de una cuenta de almacenamiento existente. 

1. Haga clic en el botón **Nuevo** de la esquina superior izquierda de Azure Portal.
2. Haga clic en **Storage**, a continuación, en **Cuenta de almacenamiento** y se mostrará un nuevo panel "Crear cuenta de almacenamiento".
3. Escriba un **Nombre** para la cuenta de almacenamiento, que utilizará más adelante.  
4. **Modelo de implementación** y **Clase de cuenta** debe establecerse en "Resource Manager" y "Uso General", respectivamente. 
5. Asegúrese de que **Suscripción** y **Grupo de recursos** coinciden con los que especificó cuando creó la máquina virtual en el paso anterior.
6. Haga clic en **Crear**.

    ![Creación de una nueva cuenta de almacenamiento](media/msi-tutorial-linux-vm-access-storage/msi-storage-create.png)

## <a name="create-a-blob-container-in-the-storage-account"></a>Creación de un contenedor de blobs en la cuenta de almacenamiento

Más adelante se cargará y descargará un archivo a la nueva cuenta de almacenamiento. Dado que los archivos requieren almacenamiento de blobs, es necesario crear un contenedor de blobs en el que almacenar el archivo.

1. Vuelva a la cuenta de almacenamiento recién creada.
2. Haga clic en el vínculo **Contenedores** en la barra de navegación izquierda, en "Blob service".
3. Haga clic en **+ Contenedor** en la parte superior de la página y se abrirá un panel "Nuevo contenedor".
4. Asigne un nombre al contenedor, seleccione un nivel de acceso y, a continuación, haga clic en **Aceptar**. El nombre especificado se utilizará más adelante en el tutorial. 

    ![Creación de contenedores de almacenamiento](media/msi-tutorial-linux-vm-access-storage/create-blob-container.png)

## <a name="grant-your-vms-identity-access-to-use-storage-keys"></a>Concesión de acceso a la identidad de la máquina virtual para usar claves de almacenamiento 

Azure Storage no admite la autenticación de Azure AD de forma nativa.  No obstante, puede usar una instancia de MSI para recuperar las claves de almacenamiento de Resource Manager y usar dichas claves para tener acceso al almacenamiento.  En este paso, se concede a la instancia de MSI en la máquina virtual acceso a las claves para la cuenta de almacenamiento.   

1. Vaya a la pestaña de **Storage**.  
2. Seleccione la **cuenta de Storage** específica que creó anteriormente.   
3. Vaya a **Control de acceso (IAM)** en el panel izquierdo.  
4. A continuación, haga clic en **Agregar** para agregar una nueva asignación de rol para su máquina virtual y, en **Rol**, seleccione **Rol de servicio del operador de claves de cuentas de almacenamiento**.  
5. En la lista desplegable siguiente, **asigne acceso** al recurso **Máquina virtual**.  
6. A continuación, asegúrese de que la suscripción adecuada aparece en la lista desplegable **Suscripción**. Y en **Grupo de recursos**, seleccione **Todos los grupos de recursos**.  
7. Por último, en **Seleccionar**, elija la máquina virtual Linux en la lista desplegable y haga clic en **Guardar**. 
    ![Texto alternativo de imagen](media/msi-tutorial-linux-vm-access-storage/msi-storage-role.png)

## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-call-azure-resource-manager"></a>Obtención de un token de acceso utilizando la identidad de la máquina virtual y su uso para llamar a Azure Resource Manager

Para completar estos pasos, necesitará un cliente SSH. Si usa Windows, puede usar el cliente SSH en el [Subsistema de Windows para Linux](https://msdn.microsoft.com/commandline/wsl/install_guide).

1. En el portal, vaya a la máquina virtual Linux y, en **Información general**, haga clic en **Conectar**. Se le pedirá que use Bash, tome nota de su SSH y IP de máquina virtual en la alerta. 
2. Abra y conéctese a Bash.  
3. En el terminal, escriba en su **SSH** y **máquina virtual** a la que desea conectarse, por ejemplo, "ssh admin@12.61.219.35 ".  
4. A continuación, se le pedirá que escriba la **contraseña** que agregó al crear la **máquina virtual Linux**. Debe haber iniciado sesión correctamente.  
5. Utilice CURL para obtener un token de acceso para Azure Resource Manager.  

    La solicitud CURL para el token de acceso está debajo:
    
    ```bash
    curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true    
    ```
    
    > [!NOTE]
    > El valor del parámetro "resource" debe coincidir exactamente con el que Azure AD espera. Al usar el id. de recurso de Azure Resource Manager, debe incluir la barra diagonal final en el URI.
    
    ```bash
    {"access_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IkhIQnlLVS0wRHFBcU1aaDZaRlBkMlZXYU90ZyIsImtpZCI6IkhIQnlLVS0wRHFBcU1aaDZaRlBkMlZXYU90ZyJ9.eyJhdWQiOiJodHRwczovL21hbmFnZW1lbnQuYXp1cmUuY29tIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3LyIsImlhdCI6MTUwNDEyNjYyNywibmJmIjoxNTA0MTI2NjI3LCJleHAiOjE1MDQxMzA1MjcsImFpbyI6IlkyRmdZTGg2dENWSzRkSDlGWGtuZzgyQ21ZNVdBZ0E9IiwiYXBwaWQiOiI2ZjJmNmU2OS04MGExLTQ3NmEtOGRjZi1mOTgzZDZkMjUxYjgiLCJhcHBpZGFjciI6IjIiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwib2lkIjoiMTEyODJiZDgtMDNlMi00NGVhLTlmYjctZTQ1YjVmM2JmNzJlIiwic3ViIjoiMTEyODJiZDgtMDNlMi00NGVhLTlmYjctZTQ1YjVmM2JmNzJlIiwidGlkIjoiNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3IiwidXRpIjoib0U5T3JVZFJMMHVKSEw4UFdvOEJBQSIsInZlciI6IjEuMCJ9.J6KS7b9kFgDkegJ-Vfff19LMnu3Cfps4dL2uNGucb5M76rgDM5f73VO-19wZSRhQPxWmZLETzN3SljnIMQMkYWncp79MVdBud_xqXYyLdQpGkNinpKVJhTo1j1dY27U_Cjl4yvvpBTrtH3OX9gG0GtQs7PBFTTLznqcH3JR9f-bTSEN4wUhalaIPHPciVDtJI9I24_vvMfVqxkXOo6gkL0mEP"}
     ```
    
## <a name="use-curl-to-get-storage-keys-from-azure-resource-manager"></a>Uso de CURL para obtener las claves de almacenamiento de Azure Resource Manager  

Ahora vamos a utilizar CURL para realizar una llamada a Resource Manager mediante el token de acceso que se recuperó en la sección anterior, para recuperar la clave de acceso de almacenamiento. Una vez que tenemos la clave de acceso de almacenamiento, podemos hacer llamadas a operaciones de carga y descarga del almacenamiento.

> [!NOTE]
> El texto en la dirección URL distingue mayúsculas de minúsculas, por lo que tiene que fijarse si usa mayúsculas o minúsculas para los grupos de recursos para reflejarlo adecuadamente según corresponda. Además, es importante saber que se trata de una solicitud POST, no una solicitud GET, y asegurarse de que pasa un valor para capturar un límite de longitud con -d que puede ser NULL.  

```bash 
curl https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Storage/storageAccounts/<STORAGE ACCOUNT NAME>/listKeys?api-version=2016-12-01 –-request POST -d "" -H "Authorization: Bearer <ACCESS TOKEN>" 
```

La respuesta CURL le ofrece la lista de claves:  

```bash 
{"keys":[{"keyName":"key1","permissions":"Full","value":"iqDPNtXcluBiFSV2Fs+JTj6c+VK5wWB4RWUVYw2Y5BkRQu21cRpAztt3S19bAowhGPqJrAfMr3gn89Ui9VPbtA=="},{"keyName":"key2","permissions":"Full","value":"U+uI0B1Iygw1bDK6Ic1VWKMMY77ac0VV3kqYaCW7FcUFiuw4UkLfEKbnF2sZpIfFR9zUWniLBhlt+eUn/TeSJg=="}]} 
```


Cree un archivo blob de ejemplo para cargar en el contenedor de almacenamiento de blobs. En una máquina virtual Linux puede hacer esto con el siguiente comando. 

```bash
echo "This is a test file." > test.txt
```

A continuación, cargue el archivo usando la CLI de Azure y realice la autenticación con la clave de almacenamiento.
 

```azurecli-interactive
az storage blob upload -c <CONTAINER NAME> -n test.txt -f test.txt --account-name <STORAGE ACCOUNT NAME> --account-key <STORAGE ACCOUNT KEY>
```

Respuesta: 

```JSON
Finished[#############################################################]  100.0000%
{
  "etag": "\"0x8D4F9929765C139\"",
  "lastModified": "2017-09-12T03:58:56+00:00"
}
```

Además, puede cargar el archivo usando la CLI de Azure y autenticando con la clave de almacenamiento. 

Solicitud: 

```azurecli-interactive
az storage blob download -c <CONTAINER NAME> -n test.txt -f test-download.txt --account-name <STORAGE ACCOUNT NAME> --account-key <STORAGE ACCOUNT KEY>
```

Respuesta: 

```JSON
{
  "content": null,
  "metadata": {},
  "name": "test.txt",
  "properties": {
    "appendBlobCommittedBlockCount": null,
    "blobType": "BlockBlob",
    "contentLength": 21,
    "contentRange": "bytes 0-20/21",
    "contentSettings": {
      "cacheControl": null,
      "contentDisposition": null,
      "contentEncoding": null,
      "contentLanguage": null,
      "contentMd5": "LSghAvpnElYyfUdn7CO8aw==",
      "contentType": "text/plain"
    },
    "copy": {
      "completionTime": null,
      "id": null,
      "progress": null,
      "source": null,
      "status": null,
      "statusDescription": null
    },
    "etag": "\"0x8D5067F30D0C283\"",
    "lastModified": "2017-09-28T14:42:49+00:00",
    "lease": {
      "duration": null,
      "state": "available",
      "status": "unlocked"
    },
    "pageBlobSequenceNumber": null,
    "serverEncrypted": false
  },
  "snapshot": null
}
```

## <a name="related-content"></a>Contenido relacionado

Para obtener información general sobre MSI, consulte [Managed Service Identity overview](../active-directory/msi-overview.md) (Introducción a Managed Service Identity).

Use la siguiente sección de comentarios para proporcionar sus opiniones y ayudarnos a afinar y remodelar el contenido.

