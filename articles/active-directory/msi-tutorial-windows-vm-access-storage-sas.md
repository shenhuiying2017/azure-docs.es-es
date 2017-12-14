---
title: "Uso de una identidad MSI de máquina virtual Windows para acceder a Azure Storage con una credencial SAS"
description: "Tutorial que muestra cómo utilizar Managed Service Identity (MSI) en una máquina virtual Windows para acceder a Azure Storage, mediante una credencial SAS en lugar de una clave de acceso de la cuenta de almacenamiento."
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
ms.openlocfilehash: fbe46b952b72d3379ba3eb0ff8c84b9d036752e3
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="use-a-windows-vm-managed-service-identity-to-access-azure-storage-via-a-sas-credential"></a>Uso de Managed Service Identity en una máquina virtual Windows para tener acceso a Azure Storage a través de una credencial SAS

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

Este tutorial muestra cómo habilitar Managed Service Identity (MSI) para una máquina virtual Windows y, a continuación, usar MSI para obtener una credencial de Firma de acceso compartido (SAS) del almacenamiento. En concreto, una [credencial SAS de servicio](/azure/storage/common/storage-dotnet-shared-access-signature-part-1?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#types-of-shared-access-signatures). 

Una credencial SAS de servicio permite conceder acceso limitado a los objetos de una cuenta de almacenamiento, durante un período de tiempo limitado y con un servicio específico (en nuestro caso, el servicio blob), sin exponer una clave de acceso a la cuenta. Puede usar una credencial SAS de la forma habitual al realizar operaciones de almacenamiento, por ejemplo, al usar el SDK de Storage. En este tutorial, mostramos cómo cargar y descargar un blob mediante PowerShell de Azure Storage. Aprenderá a:


> [!div class="checklist"]
> * Habilitar MSI en una máquina virtual Windows 
> * Conceder acceso a la máquina virtual a una SAS de cuenta de almacenamiento en Resource Manager 
> * Obtener un token de acceso mediante la identidad de la máquina virtual y utilizarlo para recuperar la credencial SAS desde Resource Manager 

## <a name="prerequisites"></a>Requisitos previos

[!INCLUDE [msi-qs-configure-prereqs](../../includes/active-directory-msi-qs-configure-prereqs.md)]

[!INCLUDE [msi-tut-prereqs](../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="sign-in-to-azure"></a>Inicio de sesión en Azure

Inicie sesión en Azure Portal en [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-windows-virtual-machine-in-a-new-resource-group"></a>Creación de una máquina virtual Windows en un nuevo grupo de recursos

En este tutorial, se crea una nueva máquina virtual Windows. También puede habilitar MSI en una máquina virtual existente.

1.  Haga clic en el botón **+/Crear nuevo servicio** de la esquina superior izquierda de Azure Portal.
2.  Seleccione **Compute**y, después, seleccione **Windows Server 2016 Datacenter**. 
3.  Escriba la información de la máquina virtual. El **nombre de usuario** y la **contraseña** creados aquí son las credenciales que se usan para iniciar sesión en la máquina virtual.
4.  Elija la **suscripción** adecuada de la máquina virtual en la lista desplegable.
5.  Para seleccionar un nuevo **grupo de recursos** en el que quiere crear la máquina virtual, elija **Crear nuevo**. Cuando haya terminado, haga clic en **Aceptar**.
6.  Seleccione el tamaño de la máquina virtual. Para ver más tamaños, seleccione **Ver todo** o cambie el filtro **Supported disk type** (Tipo de disco admitido). En la hoja de configuración, conserve los valores predeterminados y haga clic en **Aceptar**.

    ![Texto alternativo de imagen](media/msi-tutorial-windows-vm-access-arm/msi-windows-vm.png)

## <a name="enable-msi-on-your-vm"></a>Habilitación de MSI en la máquina virtual

Una identidad MSI de máquina virtual le permite obtener tokens de acceso de Azure AD sin tener que incluir las credenciales en el código. Al habilitar MSI suceden dos cosas en segundo plano: se instala la extensión MSI en la máquina virtual y se habilita MSI para la máquina virtual.  

1. Desplácese hasta el grupo de recursos de la nueva máquina virtual y seleccione la máquina virtual que creó en el paso anterior.
2. En la configuración de máquina virtual, en el panel de la izquierda, haga clic en **Configuración**.
3. Para registrar y habilitar MSI, seleccione **Sí**; si desea deshabilitarla, elija No.
4. No olvide hacer clic en **Guardar** para guardar la configuración.

    ![Texto alternativo de imagen](media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

5. Si desea comprobar las extensiones que hay en la máquina virtual, haga clic en **Extensiones**. Si MSI está habilitado, **ManagedIdentityExtensionforWindows** aparece en la lista.

    ![Texto alternativo de imagen](media/msi-tutorial-linux-vm-access-arm/msi-extension-value.png)

## <a name="create-a-storage-account"></a>Crear una cuenta de almacenamiento 

Si aún no tiene una, creará ahora una cuenta de almacenamiento. También puede omitir este paso y conceder acceso para MSI en la máquina virtual a la credencial de SAS de una cuenta de almacenamiento existente. 

1. Haga clic en el botón **+/Crear nuevo servicio** de la esquina superior izquierda de Azure Portal.
2. Haga clic en **Storage**, a continuación, en **Cuenta de almacenamiento** y se mostrará un nuevo panel "Crear cuenta de almacenamiento".
3. Escriba un nombre para la cuenta de almacenamiento, que utilizará más adelante.  
4. **Modelo de implementación** y **Clase de cuenta** debe establecerse en "Resource Manager" y "Uso General", respectivamente. 
5. Asegúrese de que **Suscripción** y **Grupo de recursos** coinciden con los que especificó cuando creó la máquina virtual en el paso anterior.
6. Haga clic en **Crear**.

    ![Creación de una nueva cuenta de almacenamiento](media/msi-tutorial-linux-vm-access-storage/msi-storage-create.png)

## <a name="create-a-blob-container-in-the-storage-account"></a>Creación de un contenedor de blobs en la cuenta de almacenamiento

Más adelante se cargará y descargará un archivo a la nueva cuenta de almacenamiento. Dado que los archivos requieren almacenamiento de blobs, es necesario crear un contenedor de blobs en el que almacenar el archivo.

1. Vuelva a la cuenta de almacenamiento recién creada.
2. Haga clic en el vínculo **Contenedores** en el panel de la izquierda, en "Blob service".
3. Haga clic en **+ Contenedor** en la parte superior de la página y se abrirá un panel "Nuevo contenedor".
4. Asigne un nombre al contenedor, seleccione un nivel de acceso y, a continuación, haga clic en **Aceptar**. El nombre especificado se utilizará más adelante en el tutorial. 

    ![Creación de contenedores de almacenamiento](media/msi-tutorial-linux-vm-access-storage/create-blob-container.png)

## <a name="grant-your-vms-msi-access-to-use-a-storage-sas"></a>Concesión de acceso MSI a la máquina virtual para usar una credencial SAS de almacenamiento 

Azure Storage no admite la autenticación de Azure AD de forma nativa.  No obstante, puede usar una instancia de MSI para recuperar una credencial SAS de almacenamiento desde Resource Manager y usarla a fin de acceder al almacenamiento.  En este paso, se concede a la instancia de MSI en la máquina virtual acceso a la credencial SAS de la cuenta de almacenamiento.   

1. Vuelva a la cuenta de almacenamiento recién creada.   
2. Haga clic en el vínculo **Control de acceso (IAM)** en el panel izquierdo.  
3. Haga clic en **+ Agregar** en la parte superior de la página para agregar una nueva asignación de roles para la máquina virtual.
4. Establezca **Rol** en "Colaborador de la cuenta de almacenamiento", en el lado derecho de la página.  
5. En el menú desplegable siguiente, establezca **Asignar acceso a** en el recurso "Máquina virtual".  
6. A continuación, asegúrese de que la suscripción adecuada aparece en el menú desplegable **Suscripción** y después, establezca **Grupo de recursos** en "Todos los grupos de recursos".  
7. Por último, en **Seleccionar**, elija la máquina virtual Windows en el menú desplegable y haga clic en **Guardar**. 

    ![Texto alternativo de imagen](media/msi-tutorial-linux-vm-access-storage/msi-storage-role-sas.png)

## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-call-azure-resource-manager"></a>Obtención de un token de acceso utilizando la identidad de la máquina virtual y su uso para llamar a Azure Resource Manager 

En el resto del tutorial, vamos a trabajar desde la máquina virtual que se creó anteriormente.

En esta parte tendrá que usar los cmdlets de PowerShell de Azure Resource Manager.  Si no lo tiene instalado, [descargue la versión más reciente](https://docs.microsoft.com/powershell/azure/overview) antes de continuar.

1. En Azure Portal, vaya a **Máquinas virtuales**, vaya a la máquina virtual Windows y, a continuación, desde la página **Información general**, haga clic en **Conectar** en la parte superior.
2. Escriba su **nombre de usuario** y **contraseña** que agregó cuando creó la máquina virtual Windows. 
3. Ahora que ha creado una **conexión a Escritorio remoto** con la máquina virtual, abra PowerShell en la sesión remota. 
4. Mediante Invoke-WebRequest de PowerShell, realice una solicitud al punto de conexión de MSI local para obtener un token de acceso para Azure Resource Manager.

    ```powershell
       $response = Invoke-WebRequest -Uri http://localhost:50342/oauth2/token -Method GET -Body @{resource="https://management.azure.com/"} -Headers @{Metadata="true"}
    ```
    
    > [!NOTE]
    > El valor del parámetro "resource" debe coincidir exactamente con el que Azure AD espera. Al usar el id. de recurso de Azure Resource Manager, debe incluir la barra diagonal final en el URI.
    
    A continuación, extraiga el elemento "Content", que se almacena como una cadena con formato de notación de objetos JavaScript (JSON) en el objeto $response. 
    
    ```powershell
    $content = $response.Content | ConvertFrom-Json
    ```
    Luego, extraiga el token de acceso de la respuesta.
    
    ```powershell
    $ArmToken = $content.access_token
    ```

## <a name="get-a-sas-credential-from-azure-resource-manager-to-make-storage-calls"></a>Obtención de una credencial SAS desde Azure Resource Manager para realizar llamadas de almacenamiento 

Ahora vamos a utilizar PowerShell para realizar una llamada a Resource Manager mediante el token de acceso que se recuperó en la sección anterior, a fin de crear una credencial SAS de almacenamiento. Una vez que tenemos la credencial SAS de almacenamiento, podemos hacer llamadas de almacenamiento.

Para esta solicitud, vamos a usar los parámetros de solicitud HTTP de seguimiento para crear la credencial SAS:

```JSON
{
    "canonicalizedResource":"/blob/<STORAGE ACCOUNT NAME>/<CONTAINER NAME>",
    "signedResource":"c",              // The kind of resource accessible with the SAS, in this case a container (c).
    "signedPermission":"rcw",          // Permissions for this SAS, in this case (r)ead, (c)reate, and (w)rite. Order is important.
    "signedProtocol":"https",          // Require the SAS be used on https protocol.
    "signedExpiry":"<EXPIRATION TIME>" // UTC expiration time for SAS in ISO 8601 format, for example 2017-09-22T00:06:00Z.
}
```

Estos parámetros se incluyen en el cuerpo POST de la solicitud para la credencial SAS. Para más información sobre los parámetros para crear una credencial SAS, consulte la [referencia de REST de SAS del servicio de lista](/rest/api/storagerp/storageaccounts/listservicesas).

En primer lugar, convierta los parámetros a JSON y después llame al punto de conexión `listServiceSas` del almacenamiento para crear las credenciales SAS:

```powershell
$params = @{canonicalizedResource="/blob/<STORAGE-ACCOUNT-NAME>/<CONTAINER-NAME>";signedResource="c";signedPermission="rcw";signedProtocol="https";signedExpiry="2017-09-23T00:00:00Z"}
$jsonParams = $params | ConvertTo-Json
```

```powershell
$sasResponse = Invoke-WebRequest -Uri https://management.azure.com/subscriptions/<SUBSCRIPTION-ID>/resourceGroups/<RESOURCE-GROUP>/providers/Microsoft.Storage/storageAccounts/<STORAGE-ACCOUNT-NAME>/listServiceSas/?api-version=2017-06-01 -Method POST -Body $jsonParams -Headers @{Authorization="Bearer $ArmToken"}
```
> [!NOTE] 
> La dirección URL distingue mayúsculas de minúsculas, por lo tanto, asegúrese de que usa las mismas mayúsculas y minúsculas que al asignar el nombre al grupo de recursos, así como la "G" mayúscula de "resourceGroups". 

Ahora podemos extraer la credencial SAS de la respuesta:

```powershell
$sasContent = $sasResponse.Content | ConvertFrom-Json
$sasCred = $sasContent.serviceSasToken
```

Si examina la credencial SAS, verá algo parecido a esto:

```powershell
PS C:\> $sasCred
sv=2015-04-05&sr=c&spr=https&se=2017-09-23T00%3A00%3A00Z&sp=rcw&sig=JVhIWG48nmxqhTIuN0uiFBppdzhwHdehdYan1W%2F4O0E%3D
```

A continuación, creamos un archivo llamado "test.txt". Seguidamente, use la credencial SAS para autenticarse con el cmdlet `New-AzureStorageContent`, cargue el archivo en el contenedor de blobs y, después, descargue el archivo.

```bash
echo "This is a test text file." > test.txt
```

Asegúrese de instalar en primer lugar los cmdlets de Azure Storage, mediante `Install-Module Azure.Storage`. Después, cargue el blob recién creado utilizando el cmdlet `Set-AzureStorageBlobContent` de PowerShell:

```powershell
$ctx = New-AzureStorageContext -StorageAccountName <STORAGE-ACCOUNT-NAME> -SasToken $sasCred
Set-AzureStorageBlobContent -File test.txt -Container <CONTAINER-NAME> -Blob testblob -Context $ctx
```

Respuesta:

```powershell
ICloudBlob        : Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob
BlobType          : BlockBlob
Length            : 56
ContentType       : application/octet-stream
LastModified      : 9/21/2017 6:14:25 PM +00:00
SnapshotTime      :
ContinuationToken :
Context           : Microsoft.WindowsAzure.Commands.Storage.AzureStorageContext
Name              : testblob
```

También puede descargar el blob recién creado utilizando el cmdlet `Get-AzureStorageBlobContent` de PowerShell:

```powershell
Get-AzureStorageBlobContent -Blob testblob -Container <CONTAINER-NAME> -Destination test2.txt -Context $ctx
```

Respuesta:

```powershell
ICloudBlob        : Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob
BlobType          : BlockBlob
Length            : 56
ContentType       : application/octet-stream
LastModified      : 9/21/2017 6:14:25 PM +00:00
SnapshotTime      :
ContinuationToken :
Context           : Microsoft.WindowsAzure.Commands.Storage.AzureStorageContext
Name              : testblob
```


## <a name="related-content"></a>Contenido relacionado

- Para obtener información general sobre MSI, consulte [Managed Service Identity overview](../active-directory/msi-overview.md) (Introducción a Managed Service Identity).
- Para aprender a hacer lo que se realiza en este tutorial con una clave de cuenta de almacenamiento, consulte [Uso de Managed Service Identity en una máquina virtual Windows para tener acceso a Azure Storage](msi-tutorial-windows-vm-access-storage.md)
- Para más información sobre la característica SAS de una cuenta de Azure Storage, consulte:
  - [Uso de Firmas de acceso compartido (SAS)](/azure/storage/common/storage-dotnet-shared-access-signature-part-1.md)
  - [Creación de una SAS de servicio](/rest/api/storageservices/Constructing-a-Service-SAS.md)

Use la siguiente sección de comentarios para proporcionar sus opiniones y ayudarnos a afinar y remodelar el contenido.


