---
title: "Uso de una identidad MSI de máquina virtual Linux para acceder a Azure Storage"
description: "Tutorial que indica cómo usar Managed Service Identity (MSI) en una máquina virtual Linux para tener acceso a Azure Storage."
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
ms.openlocfilehash: 2b264f8e2227e410caba8370d58c824d469198e4
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="use-a-linux-vm-managed-service-identity-to-access-azure-storage-via-access-key"></a>Uso de Managed Service Identity en una máquina virtual Linux para tener acceso a Azure Storage con una clave de acceso

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

Este tutorial muestra cómo habilitar Managed Service Identity (MSI) para una máquina virtual Linux y, a continuación, usar esa identidad para recuperar claves de acceso a la cuenta de almacenamiento. Puede usar una clave de acceso de almacenamiento de la forma habitual al realizar operaciones de almacenamiento, por ejemplo, al usar el SDK de Storage. En este tutorial se cargan y descargan blobs mediante la CLI de Azure. Aprenderá a:

> [!div class="checklist"]
> * Habilitar MSI en una máquina virtual Linux 
> * Conceder acceso a la máquina virtual a las claves de acceso a la cuenta de almacenamiento en Resource Manager 
> * Obtener un token de acceso mediante la identidad de la máquina virtual y utilizarlo para recuperar las claves de acceso a la cuenta de almacenamiento desde Resource Manager  

## <a name="prerequisites"></a>Requisitos previos

[!INCLUDE [msi-qs-configure-prereqs](../../includes/active-directory-msi-qs-configure-prereqs.md)]

[!INCLUDE [msi-tut-prereqs](../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="sign-in-to-azure"></a>Inicio de sesión en Azure
Inicie sesión en Azure Portal en [https://portal.azure.com](https://portal.azure.com).


## <a name="create-a-linux-virtual-machine-in-a-new-resource-group"></a>Crear una máquina virtual Linux en un nuevo grupo de recursos

En este tutorial, vamos a crear una nueva máquina virtual Linux. También puede habilitar MSI en una máquina virtual existente.

1. Haga clic en el botón **+/Crear nuevo servicio** de la esquina superior izquierda de Azure Portal.
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

1. Haga clic en el botón **+/Crear nuevo servicio** de la esquina superior izquierda de Azure Portal.
2. Haga clic en **Storage**, a continuación, en **Cuenta de almacenamiento** y se mostrará un nuevo panel "Crear cuenta de almacenamiento".
3. Escriba un **Nombre** para la cuenta de almacenamiento, que utilizará más adelante.  
4. **Modelo de implementación** y **Clase de cuenta** debe establecerse en "Resource Manager" y "Uso General", respectivamente. 
5. Asegúrese de que **Suscripción** y **Grupo de recursos** coinciden con los que especificó cuando creó la máquina virtual en el paso anterior.
6. Haga clic en **Crear**.

    ![Creación de una nueva cuenta de almacenamiento](media/msi-tutorial-linux-vm-access-storage/msi-storage-create.png)

## <a name="create-a-blob-container-in-the-storage-account"></a>Creación de un contenedor de blobs en la cuenta de almacenamiento

Más adelante se cargará y descargará un archivo a la nueva cuenta de almacenamiento. Dado que los archivos requieren almacenamiento de blobs, es necesario crear un contenedor de blobs en el que almacenar el archivo.

1. Vuelva a la cuenta de almacenamiento recién creada.
2. Haga clic en el vínculo **Contenedores** a la izquierda, en "Blob service".
3. Haga clic en **+ Contenedor** en la parte superior de la página y se abrirá un panel "Nuevo contenedor".
4. Asigne un nombre al contenedor, seleccione un nivel de acceso y, a continuación, haga clic en **Aceptar**. El nombre especificado se utilizará más adelante en el tutorial. 

    ![Creación de contenedores de almacenamiento](media/msi-tutorial-linux-vm-access-storage/create-blob-container.png)

## <a name="grant-your-vms-msi-access-to-use-storage-account-access-keys"></a>Concesión de acceso MSI a la máquina virtual para usar las claves de acceso de la cuenta de almacenamiento

Azure Storage no admite la autenticación de Azure AD de forma nativa.  No obstante, puede usar una instancia de MSI para recuperar las claves de acceso a la cuenta de almacenamiento desde Resource Manager y usar una clave para acceder al almacenamiento.  En este paso, se concede a la instancia de MSI en la máquina virtual acceso a las claves de la cuenta de almacenamiento.   

1. Vuelva a la cuenta de almacenamiento recién creada.
2. Haga clic en el vínculo **Control de acceso (IAM)** en el panel izquierdo.  
3. Haga clic en **+ Agregar** en la parte superior de la página para agregar una nueva asignación de roles para la máquina virtual
4. Establezca **Rol** en "Rol de servicio del operador de claves de cuentas de almacenamiento", en el lado derecho de la página. 
5. En el menú desplegable siguiente, establezca **Asignar acceso a** en el recurso "Máquina virtual".  
6. A continuación, asegúrese de que la suscripción adecuada aparece en el menú desplegable **Suscripción** y después, establezca **Grupo de recursos** en "Todos los grupos de recursos".  
7. Por último, en **Seleccionar**, elija la máquina virtual Linux en el menú desplegable y haga clic en **Guardar**. 

    ![Texto alternativo de imagen](media/msi-tutorial-linux-vm-access-storage/msi-storage-role.png)

## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-call-azure-resource-manager"></a>Obtención de un token de acceso utilizando la identidad de la máquina virtual y su uso para llamar a Azure Resource Manager

En el resto del tutorial, vamos a trabajar desde la máquina virtual que se creó anteriormente.

Para completar estos pasos, necesitará un cliente SSH. Si usa Windows, puede usar el cliente SSH en el [Subsistema de Windows para Linux](https://msdn.microsoft.com/commandline/wsl/install_guide). Si necesita ayuda para configurar las claves del cliente de SSH, consulte [Uso de SSH con Windows en Azure](../virtual-machines/linux/ssh-from-windows.md) o [Creación y uso de un par de claves SSH pública y privada para máquinas virtuales Linux en Azure](../virtual-machines/linux/mac-create-ssh-keys.md).

1. En Azure Portal, vaya a **Máquinas virtuales**, vaya a la máquina virtual Linux y, a continuación, desde la página **Información general**, haga clic en **Conectar** en la parte superior. Copie la cadena para conectarse a la máquina virtual. 
2. Conéctese a la máquina virtual mediante un cliente SSH.  
3. A continuación, se le pedirá que escriba la **contraseña** que agregó al crear la **máquina virtual Linux**. Debe haber iniciado sesión correctamente.  
4. Utilice CURL para obtener un token de acceso para Azure Resource Manager.  

    La solicitud CURL y la respuesta para el token de acceso están a continuación:
    
    ```bash
    curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true    
    ```
    
    > [!NOTE]
    > En la solicitud anterior, el valor del parámetro "resource" tiene que coincidir exactamente con el que Azure AD espera. Al usar el id. de recurso de Azure Resource Manager, debe incluir la barra diagonal final en el URI.
    > En la siguiente respuesta, el elemento access_token se ha abreviado.
    
    ```bash
    {"access_token":"eyJ0eXAiOiJ...",
    "refresh_token":"",
    "expires_in":"3599",
    "expires_on":"1504130527",
    "not_before":"1504126627",
    "resource":"https://management.azure.com",
    "token_type":"Bearer"} 
     ```
    
## <a name="get-storage-account-access-keys-from-azure-resource-manager-to-make-storage-calls"></a>Obtención de las claves de almacenamiento desde Azure Resource Manager para realizar llamadas al almacenamiento  

Ahora utilice CURL para llamar a Resource Manager mediante el token de acceso que se recuperó en la sección anterior, para recuperar la clave de acceso de almacenamiento. Una vez que tenemos la clave de acceso de almacenamiento, podemos hacer llamadas a operaciones de carga y descarga del almacenamiento. Asegúrese de reemplazar los valores de los parámetros `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>` y `<STORAGE ACCOUNT NAME>` con sus propios valores. Reemplace el valor de `<ACCESS TOKEN>` con el token de acceso que se recuperó anteriormente:

```bash 
curl https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Storage/storageAccounts/<STORAGE ACCOUNT NAME>/listKeys?api-version=2016-12-01 –-request POST -d "" -H "Authorization: Bearer <ACCESS TOKEN>" 
```

> [!NOTE]
> El texto de la dirección URL anterior distingue mayúsculas de minúsculas, por lo que tiene que fijarse en si usa mayúsculas o minúsculas para los grupos de recursos para reflejarlo adecuadamente según corresponda. Además, es importante saber que se trata de una solicitud POST, no una solicitud GET, y asegurarse de que pasa un valor para capturar un límite de longitud con -d que puede ser NULL.  

La respuesta CURL le ofrece la lista de claves:  

```bash 
{"keys":[{"keyName":"key1","permissions":"Full","value":"iqDPNt..."},{"keyName":"key2","permissions":"Full","value":"U+uI0B..."}]} 
```
Cree un archivo blob de ejemplo para cargar en el contenedor de almacenamiento de blobs. En una máquina virtual Linux puede hacer esto con el siguiente comando. 

```bash
echo "This is a test file." > test.txt
```

A continuación, autentíquese con el comando `az storage` de la CLI con la clave de acceso al almacenamiento y cargue el archivo en el contenedor de blobs. Para este paso, necesitará [instalar la CLI de Azure más reciente](https://docs.microsoft.com/cli/azure/install-azure-cli) en su máquina virtual, si no lo ha hecho ya.
 

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

Además, puede cargar el archivo usando la CLI de Azure y autenticando con la clave de acceso al almacenamiento. 

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

## <a name="next-steps"></a>Pasos siguientes

- Para obtener información general sobre MSI, consulte [Managed Service Identity overview](../active-directory/msi-overview.md) (Introducción a Managed Service Identity).
- Para aprender a hacer lo que se realiza en este tutorial con una credencial SAS de almacenamiento, consulte [Uso de Managed Service Identity en una máquina virtual Linux para tener acceso a Azure Storage a través de una credencial SAS](msi-tutorial-linux-vm-access-storage-sas.md)
- Para más información sobre la característica SAS de una cuenta de Azure Storage, consulte:
  - [Uso de Firmas de acceso compartido (SAS)](/azure/storage/common/storage-dotnet-shared-access-signature-part-1.md)
  - [Creación de una SAS de servicio](/rest/api/storageservices/Constructing-a-Service-SAS.md)

Use la siguiente sección de comentarios para proporcionar sus opiniones y ayudarnos a afinar y remodelar el contenido.
