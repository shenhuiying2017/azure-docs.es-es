---
title: "Solución de problemas de Azure File Sync (versión preliminar) | Microsoft Docs"
description: "Solución de problemas comunes con Azure File Sync"
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
ms.openlocfilehash: 1ea7956e92dbc85f62383e4b041c4c830599f765
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-azure-file-sync-preview"></a>Solución de problemas de Azure File Sync (versión preliminar)
Azure File Sync (versión preliminar) permite centralizar los recursos compartidos de archivos de su organización en Azure Files sin renunciar a la flexibilidad, el rendimiento y la compatibilidad de un servidor de archivos local. Para ello la transformación de los servidores Windows Server en una caché rápida de los recursos compartidos de Azure Files. Puede usar cualquier protocolo disponible en Windows Server para tener acceso a los datos localmente (incluidos SMB, NFS y FTPS) y puede tener tantas cachés según sea necesario en todo el mundo.

Este artículo está diseñado para ayudarle a solucionar problemas que pueden producirse con la implementación de Azure File Sync. En su defecto, esta guía muestra cómo recopilar registros importantes del sistema para ayudar a una investigación más profunda de los problemas. Las siguientes opciones están disponibles para obtener soporte técnico para Azure File Sync:

- Soporte técnico de Microsoft: para crear un nuevo caso de soporte técnico, vaya a la pestaña "Ayuda y soporte técnico" en Azure Portal y haga clic en "Nueva solicitud de soporte técnico".
- [Foro de Azure Storage](https://social.msdn.microsoft.com/Forums/en-US/home?forum=windowsazuredata)

## <a name="how-to-troubleshoot-agent-installation-failures"></a>Solución de problemas de errores de instalación del agente
Si la instalación del agente de Azure File Sync no se produce correctamente, ejecute el siguiente comando desde un símbolo del sistema con privilegios elevados para habilitar el registro durante la instalación del agente:

```
StorageSyncAgent.msi /l*v Installer.log
```

Si la instalación produce error, revise el archivo installer.log para determinar la causa. 

> [!Note]  
> Se producirá un error en la instalación del agente si utiliza Microsoft Update y no se está ejecutando el servicio Windows Update.

## <a name="cloud-endpoint-creation-fails-with-the-following-error-the-specified-azure-fileshare-is-already-in-use-by-a-different-cloudendpoint"></a>Se produce un error al crear el punto de conexión de nube con el siguiente error: "The specified Azure FileShare is already in use by a different CloudEndpoint" (El recurso compartido de Azure Files especificado ya está en uso por un punto de conexión de nube diferente).
Este error se produce si el recurso compartido de Azure Files ya está en uso por otro punto de conexión de nube. 

Si recibe este error y el recurso compartido de Azure Files no está actualmente en uso por un punto de conexión de nube, realice los siguientes pasos para borrar los metadatos de Azure File Sync en el recurso compartido de Azure Files:

> [!Warning]  
> Si se eliminan los metadatos en un recurso compartido de Azure Files que está actualmente en uso por un punto de conexión de nube, las operaciones de Azure File Sync producirán un error. 

1. Navegue al recurso compartido de Azure Files en Azure Portal.  
2. Haga clic con el botón derecho en el recurso compartido de Azure File y seleccione **Editar metadatos**
3. Haga clic con el botón derecho en SyncService y seleccione **Eliminar**.

## <a name="server-is-not-listed-under-registered-servers-in-the-azure-portal"></a>El servidor no aparece en los servidores registrados de Azure Portal
Si un servidor no aparece en los servidores registrados de un servicio de sincronización de almacenamiento, siga los pasos siguientes:
1. Inicie sesión en el servidor que desea registrar.
2. Abra el Explorador de archivos y vaya al directorio de instalación del agente de sincronización de almacenamiento (la ubicación predeterminada es `C:\Program Files\Azure\StorageSyncAgent`). 
3. Ejecute ServerRegistration.exe y siga el asistente para registrar el servidor en un servicio de sincronización de almacenamiento.

## <a name="server-registration-displays-the-following-message-after-installing-the-azure-file-sync-agent-this-server-is-already-registered"></a>El registro de servidor muestra un mensaje para indicar que el servidor ya está registrado.
![Captura de pantalla del cuadro de diálogo Registro de servidor con el mensaje que indica que el servidor ya está registrado](media/storage-sync-files-troubleshoot/server-registration-1.png)

Este mensaje aparece si el servidor se registró anteriormente en un servicio de sincronización de almacenamiento. Para anular el registro del servidor del servicio de sincronización de almacenamiento actual y registrarlo en un servicio de sincronización de almacenamiento nuevo, siga los pasos para [anular el registro de un servidor de Azure File Sync](storage-sync-files-server-registration.md#unregister-the-server-with-storage-sync-service).

Si el servidor no aparece en la lista de servidores registrados en el servicio de sincronización de Storage, ejecute los siguientes comandos de PowerShell en el servidor cuyo registro desea eliminar:

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Reset-StorageSyncServer
```

> [!Note]  
> Si el servidor forma parte de un clúster, hay un parámetro opcional `Reset-StorageSyncServer -CleanClusterRegistration` que también quitará el registro de clúster. Este modificador debe utilizarse cuando el último nodo del clúster no esté registrado.

## <a name="how-to-troubleshoot-sync-not-working-on-a-server"></a>Solución de problemas cuando la sincronización no funciona en un servidor
Si la sincronización no funciona en un servidor, realice estos pasos:
- Compruebe que exista un punto de conexión de servidor en Azure Portal para el directorio que quiere sincronizar con un recurso compartido de archivos de Azure:
    
    ![Captura de pantalla de un grupo de sincronización y un punto de conexión de servidor en Azure Portal](media/storage-sync-files-troubleshoot/sync-troubleshoot-1.png)

- Revise los registros de eventos operativos y de diagnóstico con el Visor de eventos, que se encuentra en `Applications and Services\Microsoft\FileSync\Agent`.
- Confirme que el servidor tiene conectividad a Internet.
- Compruebe que el servicio Azure File Sync se ejecuta en el servidor; para ello, abra el complemento MMC de Servicios y compruebe que el servicio del agente de sincronización de almacenamiento (FileSyncSvc) se esté ejecutando.

## <a name="how-to-troubleshoot-individual-files-failing-to-sync"></a>Solución de problemas de archivos individuales que no se pueden sincronizar 
Si no se pueden sincronizar archivos individuales, realice lo siguiente:
- Revise los registros de eventos operativos y de diagnóstico en `Applications and Services\Microsoft\FileSync\Agent` en el Visor de eventos.
- Compruebe que no haya ningún identificador abierto en el archivo.
    - Nota: Azure File Sync toma instantáneas de VSS periódicamente para sincronizar los archivos con identificadores abiertos.

## <a name="how-to-troubleshoot-files-that-fail-to-tier"></a>Solución de problemas de archivos que no se apilan
Si los archivos no se apilan en Azure Files, realice los pasos siguientes:

- Compruebe que los archivos existen en el recurso compartido de archivos de Azure.
    - Nota: Un archivo debe sincronizarse con un recurso compartido de archivos de Azure antes de que se puedan apilar.
- Revise los registros de eventos operativos y de diagnóstico, ubicados en `Applications and Services\Microsoft\FileSync\Agent` en el Visor de eventos.
- Confirme que el servidor tiene conectividad a Internet. 
- Compruebe que los controladores de filtro de Azure File Sync (StorageSync.sys y StorageSyncGuard.sys) se están ejecutando.
    - Abra un símbolo del sistema con privilegios elevados, ejecute `fltmc` y compruebe que se muestran los controladores de filtro de sistema de archivos StorageSync.sys y StorageSyncGuard.sys.

## <a name="how-to-troubleshoot-files-that-fail-to-be-recalled"></a>Solución de problemas de archivos que no se pueden recuperar
Si no se pueden recuperar los archivos, realice los pasos siguientes:
- Revise los registros de eventos operativos y de diagnóstico, ubicados en `Applications and Services\Microsoft\FileSync\Agent` en el Visor de eventos.
- Compruebe que los archivos existen en el recurso compartido de archivos de Azure.
- Confirme que el servidor tiene conectividad a Internet. 
- Compruebe que los controladores de filtro de Azure File Sync (StorageSync.sys y StorageSyncGuard.sys) se están ejecutando.
    - Abra un símbolo del sistema con privilegios elevados, ejecute `fltmc` y compruebe que se muestran los controladores de filtro de sistema de archivos StorageSync.sys y StorageSyncGuard.sys.

## <a name="how-to-troubleshoot-files-being-unexpectedly-recalled-on-a-server"></a>Solución de problemas de archivos que se recuperan de manera inesperada en un servidor
Las aplicaciones antivirus, de copia de seguridad y de otro tipo que leen gran cantidad de archivos provocarán recuperaciones no deseadas salvo que respeten el atributo sin conexión y pasen por alto la lectura del contenido de esos archivos. La omisión de los archivos sin conexión en esos productos ayuda a evitar recuperaciones no deseadas al realizar operaciones como exámenes antivirus o trabajos de copia de seguridad.

Consulte con el proveedor de software respecto a cómo configurar su solución para omitir la lectura de archivos sin conexión.

Otras recuperaciones no deseadas pueden suceder en otros escenarios, como la exploración de archivos en el Explorador de archivos. Al abrir una carpeta que contiene archivos con niveles en la nube en el Explorador de archivos en el servidor, pueden producirse recuperaciones no deseadas, y más si hay una solución antivirus habilitada en el servidor.

## <a name="general-troubleshooting"></a>Solución general de problemas
Si tiene problemas con Azure File Sync en un servidor, empiece por los pasos siguientes:
- Revise los registros de eventos de diagnóstico y operativos en el Visor de eventos.
    - Los problemas de sincronización, de niveles y de recuperación se registran en los registros operativos y de diagnóstico en `Applications and Services\Microsoft\FileSync\Agent`.
    - Los problemas de administración de un servidor (por ejemplo, la configuración) se registran en los registros de eventos operativos y de diagnóstico en `Applications and Services\Microsoft\FileSync\Management`.
- Compruebe que el servicio Azure File Sync se ejecuta en el servidor.
    - Abra el complemento MMC de Servicios y compruebe que el servicio del agente de sincronización de almacenamiento (FileSyncSvc) se está ejecutando.
- Compruebe que los controladores de filtro de Azure File Sync (StorageSync.sys y StorageSyncGuard.sys) se están ejecutando.
    - Abra un símbolo del sistema con privilegios elevados, ejecute fltmc y compruebe que se muestran los controladores de filtro de sistema de archivos StorageSync.sys y StorageSyncGuard.sys.

Si el problema no se resuelve después de realizar los pasos anteriores, ejecute la herramienta AFSDiag mediante los pasos siguientes:
1. Cree un directorio que se usará para guardar la salida de AFSDiag (por ejemplo, c:\output).
2. Abra una ventana de PowerShell con privilegios elevados y ejecute los comandos siguientes (presione Entrar después de cada uno):

    ```PowerShell
    cd "c:\Program Files\Azure\StorageSyncAgent"
    Import-Module .\afsdiag.ps1
    Debug-Afs c:\output # Note: use the path created in step 1
    ```

3. Para el nivel de seguimiento del modo de kernel de Azure File Sync, escriba 1 (a menos que especifique para crear seguimientos más detallados) y presione Entrar.
4. Para el nivel de seguimiento de modo de usuario de Azure File Sync, escriba 1 (a menos que especifique para crear seguimientos más detallados) y presione Entrar.
5. Reproduzca el problema y presione D cuando haya finalizado.
6. Un archivo .zip que contiene archivos de registro y seguimiento estará en el directorio de salida especificado.