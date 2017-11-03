---
title: "Registro y cancelación del registro de un servidor con Azure File Sync (versión preliminar) | Microsoft Docs"
description: "Aprenda a registrar y anular el registro de una instancia de Windows Server con un servicio de sincronización de almacenamiento de Azure File Sync."
services: storage
documentationcenter: 
author: wmgries
manager: klaasl
editor: jgerend
ms.assetid: 297f3a14-6b3a-48b0-9da4-db5907827fb5
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/08/2017
ms.author: wgries
ms.openlocfilehash: 13a75d5cafd94435346660614721399f2d77919b
ms.sourcegitcommit: b723436807176e17e54f226fe00e7e977aba36d5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/19/2017
---
# <a name="registerunregister-a-server-with-azure-file-sync-preview"></a>Registro y cancelación del registro de un servidor con Azure File Sync (versión preliminar)
Azure File Sync (versión preliminar) permite centralizar los recursos compartidos de archivos de su organización en Azure Files sin renunciar a la flexibilidad, el rendimiento y la compatibilidad de un servidor de archivos local. Para ello la transformación de los servidores Windows Server en una caché rápida de los recursos compartidos de Azure Files. Puede usar cualquier protocolo disponible en Windows Server para tener acceso a los datos localmente (incluidos SMB, NFS y FTPS) y puede tener tantas cachés según sea necesario en todo el mundo.

En el artículo siguiente se ilustra cómo registrar y anular el registro de un servidor con un servicio de sincronización de almacenamiento. Puede que esto sea necesario si un servidor se da de baja o si se quiere tener un nuevo punto de conexión en un grupo de sincronización. Consulte [How to deploy Azure File Sync (preview)](storage-sync-files-deployment-guide.md) (Implementación de Azure File Sync [versión preliminar]).

## <a name="prerequisites"></a>Requisitos previos
Para registrar una instancia de Windows Server en el servicio de sincronización de almacenamiento, debe preparar una instancia de Windows Server con los requisitos previos necesarios:

* Asegúrese de que se ha implementado un servicio de sincronización de almacenamiento. Para más información sobre cómo implementar un servicio de sincronización de almacenamiento, consulte [How to deploy Azure File Sync (preview)](storage-sync-files-deployment-guide.md) (Implementación de Azure File Sync [versión preliminar]).
* Asegúrese de que el servidor está conectado a Internet y que se puede acceder a Azure.
* Deshabilite la configuración de seguridad mejorada de IE para administradores con la interfaz de usuario del Administrador del servidor.
    
    ![Interfaz de usuario del Administrador del servidor con la configuración de seguridad mejorada de IE resaltada](media/storage-sync-files-server-registration/server-manager-ie-config.png)

* Asegúrese de que el módulo AzureRM de PowerShell esté instalado en el servidor. Si el servidor es miembro de un clúster de conmutación por error, todos los nodos del clúster necesitarán el módulo de AzureRM. Pueden encontrar más detalles sobre cómo instalar el módulo AzureRM en [Install and configure Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) (Instalación y configuración de Azure PowerShell).

    > [!Note]  
    > Se recomienda usar la versión más reciente del módulo AzureRM de PowerShell para registrar o anular el registro de un servidor. Si el paquete de AzureRM se ha instalado anteriormente en este servidor (y la versión de PowerShell en este servidor es 5.* o superior), puede usar el cmdlet `Update-Module` para actualizar este paquete. 

## <a name="register-a-server-with-storage-sync-service"></a>Registro de un servidor con el servicio de sincronización de almacenamiento
Antes de usar una instancia de Windows Server como *punto de conexión del servidor* en un *grupo de sincronización* de Azure File Sync, se debe registrar en un *servicio de sincronización de almacenamiento*. Un servidor solo se puede registrar en un *servicio de sincronización de almacenamiento* al mismo tiempo.

### <a name="install-the-azure-file-sync-agent"></a>Instalación del agente de Azure File Sync
1. [Descargue el agente de Azure File Sync](https://go.microsoft.com/fwlink/?linkid=858257).
2. Inicie el instalador del agente de Azure File Sync.
    
    ![El primer panel del instalador del agente de Azure File Sync](media/storage-sync-files-server-registration/install-afs-agent-1.png)

3. Asegúrese de habilitar las actualizaciones del agente de Azure File Sync mediante Microsoft Update. Esto es importante porque las correcciones críticas de seguridad y las mejoras de características del paquete de servidor se envían a través de Microsoft Update.

    ![Asegúrese de que Microsoft Update está habilitado en el panel de Microsoft Update del instalador del agente de Azure File Sync.](media/storage-sync-files-server-registration/install-afs-agent-2.png)

4. Si el servidor no se ha registrado anteriormente, se abre inmediatamente la interfaz de usuario de registro del servidor tras finalizar la instalación.

> [!Important]  
> Si el servidor es miembro de un clúster de conmutación por error, el agente de Azure File Sync se deberá instalar en cada nodo del clúster.

### <a name="register-the-server-using-the-server-registration-ui"></a>Registro del servidor mediante la interfaz de usuario de registro del servidor

> [!Important]  
> Las suscripciones del Proveedor de soluciones en la nube no pueden utilizar la interfaz de usuario de registro del servidor. En su lugar, use PowerShell (debajo de esta sección).

1. Si la interfaz de usuario de registro del servidor no se inició inmediatamente después de finalizar la instalación del agente de Azure File Sync, se puede iniciar manualmente ejecutando `C:\Program Files\Azure\StorageSyncAgent\ServerRegistration.exe`.
2. Haga clic en *Iniciar sesión* para acceder a la suscripción de Azure. 

    ![Apertura del cuadro de diálogo de la interfaz de usuario de registro de servidor](media/storage-sync-files-server-registration/server-registration-ui-1.png)

3. En el cuadro de diálogo, seleccione la suscripción correcta, el grupo de recursos y el servicio de sincronización de almacenamiento.

    ![Información del servicio de sincronización de almacenamiento](media/storage-sync-files-server-registration/server-registration-ui-2.png)

4. En la versión preliminar, se necesita otro inicio de sesión más para completar el proceso. 

    ![Cuadro de diálogo de inicio de sesión](media/storage-sync-files-server-registration/server-registration-ui-3.png)

> [!Important]  
> Si el servidor es miembro de un clúster de conmutación por error, cada servidor debe ejecutar el registro de servidor. Cuando ve los servidores registrados en Azure Portal, Azure File Sync reconoce automáticamente cada nodo como miembro del mismo clúster de conmutación por error y los agrupa de la manera adecuada.

### <a name="register-the-server-with-powershell"></a>Registro del servidor con PowerShell
También puede realizar el registro del servidor a través de PowerShell. Esta es la única forma compatible de registrar el servidor para las suscripciones del Proveedor de soluciones en la nube:

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Login-AzureRmStorageSync -SubscriptionID "<your-subscription-id>" -TenantID "<your-tenant-id>"
Register-AzureRmStorageSyncServer -SubscriptionId "<your-subscription-id>" - ResourceGroupName "<your-resource-group-name>" - StorageSyncService "<your-storage-sync-service-name>"
```

## <a name="unregister-the-server-with-storage-sync-service"></a>Cancelación del registro del servidor del servicio de sincronización de almacenamiento
Hay varios pasos que son necesarios para anular el registro de un servidor del servicio de sincronización de almacenamiento. A continuación se indica cómo anular el registro correctamente de un servidor.

### <a name="optional-recall-all-tiered-data"></a>(Opcional) Recuperación de todos los datos con niveles
Cuando la característica de niveles de nube está habilitada para un punto de conexión de servidor, los archivos se *apilan* a los recursos compartidos de Azure Files. De esta forma, los recursos compartidos de archivos locales funcionan como una caché, en lugar de como una copia completa del conjunto de datos, para realizar un uso eficiente del espacio del servidor de archivos. Sin embargo, si se quita un punto de conexión de servidor con archivos con niveles que aún se encuentran localmente en el servidor, esos archivos se volverán inaccesibles. Por tanto, si sigue queriendo acceder a los archivos, debe recuperar todos los archivos con niveles de Azure Files antes de continuar con la cancelación del registro. 

Para ello, se puede usar el cmdlet de PowerShell, tal y como se muestra a continuación:

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <path-to-to-your-server-endpoint>
```

> [!Warning]  
> Si el volumen local que hospeda el servidor no tiene suficiente espacio disponible para recuperar todos los datos con niveles, el cmdlet `Invoke-StorageSyncFileRecall` dará error.  

### <a name="remove-the-server-from-all-sync-groups"></a>Eliminación del servidor de todos los grupos de sincronización
Antes de anular el registro del servidor en el servicio de sincronización de almacenamiento, se deben quitar todos los puntos de conexión de servidor de ese servidor. Esta acción también se puede hacer mediante el Portal.

1. Vaya al servicio de sincronización de almacenamiento donde está registrado el servidor.
2. Quite todos los puntos de conexión de servidor de este servidor en cada grupo de sincronización del servicio de sincronización de almacenamiento. Para ello, haga clic con el botón derecho en el punto de conexión de servidor que le interese en el panel Grupo de sincronización.

    ![Eliminación de un punto de conexión de servidor de un grupo de sincronización](media/storage-sync-files-server-registration/sync-group-server-endpoint-remove-1.png)

Esta acción también se puede realizar con un script simple de PowerShell:

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.PowerShell.Cmdlets.dll"

$accountInfo = Login-AzureRmAccount
Login-AzureRmStorageSync -SubscriptionId $accountInfo.Context.Subscription.Id -TenantId $accountInfo.Context.Tenant.Id -ResourceGroupName "<your-resource-group>"

$StorageSyncService = "<your-storage-sync-service>"

Get-AzureRmStorageSyncGroup -StorageSyncServiceName $StorageSyncService | ForEach-Object { 
    $SyncGroup = $_; 
    Get-AzureRmStorageSyncServerEndpoint -StorageSyncServiceName $StorageSyncService -SyncGroupName $SyncGroup.Name | Where-Object { $_.DisplayName -eq $env:ComputerName } | ForEach-Object { 
        Remove-AzureRmStorageSyncServerEndpoint -StorageSyncServiceName $StorageSyncService -SyncGroupName $SyncGroup.Name -ServerEndpointName $_.Name 
    } 
}
```

### <a name="unregister-the-server"></a>Anulación del registro del servidor
Ahora que todos los datos se han recuperado y que el servidor se ha quitado de todos los grupos de sincronización, se puede anular el registro del servidor. 

1. En Azure Portal, vaya a la sección "Servidores registrados" del servicio de sincronización de almacenamiento.
2. Haga clic con el botón derecho en el servidor cuyo registro desea anular y haga clic en "Cancelar registro de servidor".

    ![Anulación del registro del servidor](media/storage-sync-files-server-registration/unregister-server-1.png)