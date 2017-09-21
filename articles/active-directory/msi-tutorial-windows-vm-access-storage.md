---
title: "Uso de una identidad de servicio administrara (MSI) de máquina virtual Windows para acceder a Azure Storage"
description: "Tutorial que indica cómo usar una identidad de servicio administrada (MSI) en una máquina virtual Windows para acceder a Azure Storage."
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
ms.date: 09/14/2017
ms.author: elkuzmen
ms.translationtype: HT
ms.sourcegitcommit: 47ba7c7004ecf68f4a112ddf391eb645851ca1fb
ms.openlocfilehash: 86d88e3d44f442171f69d0baea5e7d689b963277
ms.contentlocale: es-es
ms.lasthandoff: 09/14/2017

---

# <a name="use-a-windows-vm-managed-service-identity-msi-to-access-azure-storage"></a>Uso de una identidad de servicio administrada (MSI) en una máquina virtual Windows para acceder a Azure Storage

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

Este tutorial muestra cómo habilitar Managed Service Identity (MSI) para una máquina virtual Linux y, a continuación, usar esa identidad para acceder a las claves de almacenamiento. Puede usar las claves de almacenamiento de la forma habitual al realizar operaciones de almacenamiento, por ejemplo, al usar Storage SDK. En este tutorial, se cargarán y descargarán blobs mediante la CLI de Azure. Aprenderá a:


> [!div class="checklist"]
> * Habilitar MSI en una máquina virtual Windows 
> * Creación de una cuenta de Storage
> * Conceder a la máquina virtual acceso a Storage 
> * Obtener un token de acceso para la cuenta de Storage mediante la identidad de máquina virtual 


Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

## <a name="sign-in-to-azure"></a>Inicio de sesión en Azure

Inicie sesión en Azure Portal en [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-windows-virtual-machine-in-a-new-resource-group"></a>Creación de una máquina virtual Windows en un nuevo grupo de recursos

En este tutorial, se crea una nueva máquina virtual Windows. También puede habilitar MSI en una máquina virtual existente.

1.  Haga clic en el botón **Nuevo** de la esquina superior izquierda de Azure Portal.
2.  Seleccione **Compute**y, después, seleccione **Windows Server 2016 Datacenter**. 
3.  Escriba la información de la máquina virtual. El **nombre de usuario** y la **contraseña** creados aquí son las credenciales que se usan para iniciar sesión en la máquina virtual.
4.  Elija la **suscripción** adecuada de la máquina virtual en la lista desplegable.
5.  Para seleccionar un nuevo **grupo de recursos** en el que quiere crear la máquina virtual, elija **Crear nuevo**. Cuando haya terminado, haga clic en **Aceptar**.
6.  Seleccione el tamaño de la máquina virtual. Para ver más tamaños, seleccione **Ver todo** o cambie el filtro **Supported disk type** (Tipo de disco admitido). En la hoja de configuración, conserve los valores predeterminados y haga clic en **Aceptar**.

    ![Texto alternativo de imagen](media/msi-tutorial-windows-vm-access-arm/msi-windows-vm.png)

## <a name="enable-msi-on-your-vm"></a>Habilitación de MSI en la máquina virtual

Una identidad MSI de máquina virtual le permite obtener tokens de acceso de Azure AD sin tener que incluir las credenciales en el código. Al habilitar MSI suceden dos cosas en segundo plano: se instala la extensión MSI en la máquina virtual y se habilita MSI para la máquina virtual.  

1. Seleccione la **máquina virtual** en la que quiera habilitar MSI.
2. En la barra de navegación de la izquierda, haga clic en **Configuración**.
3. Verá **Managed Service Identity**. Para registrar y habilitar MSI, seleccione **Sí**; si desea deshabilitarla, elija No.
4. No olvide hacer clic en **Guardar** para guardar la configuración.

    ![Texto alternativo de imagen](media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

5. Si desea comprobar las extensiones que están en esta **máquina virtual Windows**, haga clic en **Extensiones**. Si MSI está habilitada, en la lista aparecerá **ManagedIdentityExtensionforWindows**.

    ![Texto alternativo de imagen](media/msi-tutorial-linux-vm-access-arm/msi-extension-value.png)

## <a name="create-a-new-storage-account"></a>Creación de una cuenta de Storage 

Puede usar las claves de almacenamiento de la manera habitual al realizar operaciones de Storage; este ejemplo se centra en la carga y la descarga de blobs mediante la CLI de Azure. 

1. Vaya a la barra lateral y seleccione **Storage**.  
2. Cree una nueva **cuenta de Storage**.  
3. En **Modelo de implementación**, escriba **Resource Manager** y en **Tipo de cuenta**, seleccione **Uso general**.  
4. Asegúrese de que los valores de **Suscripción** y **Grupo de recursos** sean los que usó cuando creó su **máquina virtual Linux** en el paso anterior.

    ![Texto alternativo de imagen](media/msi-tutorial-linux-vm-access-storage/msi-storage-create.png)

## <a name="grant-your-vm-identity-access-to-use-storage-keys"></a>Concesión de acceso a la identidad de máquina virtual para usar claves de almacenamiento 

Con MSI, el código puede obtener tokens de acceso para autenticarse en los recursos que admitan la autenticación de Azure AD.   

1. Vaya a la pestaña de **Storage**.  
2. Seleccione la **cuenta de Storage** específica que creó anteriormente.   
3. Vaya a **Control de acceso (IAM)** en el panel izquierdo.  
4. A continuación, haga clic en **Agregar** para agregar una nueva asignación de rol para su máquina virtual y, en **Rol**, seleccione **Rol de servicio del operador de claves de cuentas de almacenamiento**.  
5. En la lista desplegable siguiente, **asigne acceso** al recurso **Máquina virtual**.  
6. A continuación, asegúrese de que la suscripción adecuada aparece en la lista desplegable **Suscripción**. Y en **Grupo de recursos**, seleccione **Todos los grupos de recursos**.  
7. Por último, en **Seleccionar**, elija la máquina virtual Windows de la lista desplegable y haga clic en **Guardar**. 

    ![Texto alternativo de imagen](media/msi-tutorial-linux-vm-access-storage/msi-storage-role.png)

## <a name="get-an-access-token-using-the-vm-identity-and-use-it-to-call-azure-resource-manager"></a>Obtención de un token de acceso mediante la identidad de máquina virtual y su uso para llamar a Azure Resource Manager 

Deberá usar **PowerShell** en esta parte.  Si no lo ha instalado, descárguelo [aquí](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-4.3.1). 

1. En el portal, vaya a **Virtual Machines** y diríjase a la máquina virtual Windows. A continuación, en **Introducción**, haga clic en **Conectar**. 
2. Escriba su **nombre de usuario** y **contraseña** que agregó cuando creó la máquina virtual Windows. 
3. Ahora que ha creado una **conexión a Escritorio remoto** con la máquina virtual, abra **PowerShell** en la sesión remota. 
4. Mediante Invoke-WebRequest de PowerShell, realice una solicitud al punto de conexión de MSI local para obtener un token de acceso para Azure Resource Manager.

    ```powershell
       $response = Invoke-WebRequest -Uri http://localhost/50342/oauth2/token -Method GET -Body @resource="https://management.azure.com/"} -Headers @{Metadata="true"}
    ```
    
    > [!NOTE]
    > El valor del parámetro "resource" debe coincidir exactamente con el que Azure AD espera. Al usar el id. de recurso de Azure Resource Manager, debe incluir la barra diagonal final en el URI.
    
    A continuación, extraiga la respuesta completa, que se almacena como una cadena con formato de notación de objetos JavaScript (JSON) en el objeto $response. 
    
    ```powershell
    $content = $repsonse.Content | ConvertFrom-Json
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
    > La dirección URL distingue mayúsculas de minúsculas, por lo tanto, asegúrese de que usa las mismas mayúsculas y minúsculas que al asignar el nombre al grupo de recursos, así como la "G" mayúscula de "resourceGrop".
    
## <a name="get-the-storage-keys-from-azure-resource-manager"></a>Obtención de las claves de almacenamiento de Azure Resource Manager 

```powershell
PS C:\> $keysResponse = Invoke-WebRequest -Uri https://management.azure.com/subscriptions/97f51385-2edc-4b69-bed8-7778dd4cb761/resourceGroups/SKwan_Test/providers/Microsoft.Storage/storageAccounts/skwanteststorage/listKeys/?api-version=2016-12-01 -Method POST$ -Headers @{Authorization="Bearer $ARMToken"}
```

```powershell
PS C:\> $keysContent = $keysResponse.Content | ConvertFrom-Json
```

```powershell
PS C:\> $key = $keysContent.keys[0].value
```

**Creación de un archivo que se cargará mediante la CLI de Azure**

```bash
echo "This is a test text file." > test.txt
```

**Carga del archivo mediante la CLI de Azure y realice la autenticación con la clave de almacenamiento**

> [!NOTE]
> Primero, no olvide instalar los cmdlets de Azure Storage "Install-Module Azure.Storage". 

Solicitud de PowerShell:


```powershell
PS C:\> $ctx = New-AzureStorageContext -StorageAccountName skwanteststorage -StorageAccountKey $key
PS C:\> Set-AzureStorageBlobContent -File test.txt -Container testcontainer -Blob testblob -Context $ctx
```

Respuesta:

```powershell
ICloudBlob        : Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob
BlobType          : BlockBlob
Length            : 56
ContentType       : application/octet-stream
LastModified      : 9/13/2017 6:14:25 PM +00:00
SnapshotTime      :
ContinuationToken :
Context           : Microsoft.WindowsAzure.Commands.Storage.AzureStorageContext
Name              : testblob
```

**Descarga del archivo mediante la CLI de Azure y realice la autenticación con la clave de almacenamiento**

Solicitud de PowerShell:

```powershell
PS C:\> Get-AzureStorageBlobContent -Blob <blob name> -Container <container name> -Destination <file> -Context $ctx
```

Respuesta:

```powershell
ICloudBlob        : Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob
BlobType          : BlockBlob
Length            : 56
ContentType       : application/octet-stream
LastModified      : 9/13/2017 6:14:25 PM +00:00
SnapshotTime      :
ContinuationToken :
Context           : Microsoft.WindowsAzure.Commands.Storage.AzureStorageContext
Name              : testblob
```





