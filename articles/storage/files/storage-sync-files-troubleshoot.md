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
ms.openlocfilehash: 6bc5f2da2b8628671037b9257db746e73cd3afad
ms.sourcegitcommit: 76a3cbac40337ce88f41f9c21a388e21bbd9c13f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2017
---
# <a name="troubleshoot-azure-file-sync-preview"></a>Solución de problemas de Azure File Sync (versión preliminar)
Azure File Sync (versión preliminar) permite centralizar los recursos compartidos de archivos de su organización en Azure Files sin renunciar a la flexibilidad, el rendimiento y la compatibilidad de un servidor de archivos local. Para ello la transformación de los servidores Windows Server en una caché rápida de los recursos compartidos de Azure Files. Puede usar cualquier protocolo disponible en Windows Server para tener acceso a los datos localmente (incluidos SMB, NFS y FTPS) y puede tener tantas cachés según sea necesario en todo el mundo.

Este artículo está diseñado para ayudarle a solucionar problemas que pueden producirse con la implementación de Azure File Sync. En su defecto, esta guía muestra cómo recopilar registros importantes del sistema para ayudar a una investigación más profunda de los problemas. Si no encuentra una respuesta que se ajuste a su pregunta, no dude en ponerse en contacto con nosotros mediante los siguientes canales (en orden incremental):

1. En la sección Comentarios de este artículo.
2. [Foro de Azure Storage](https://social.msdn.microsoft.com/Forums/home?forum=windowsazuredata)
3. [UserVoice de Azure Files](https://feedback.azure.com/forums/217298-storage/category/180670-files) 
4. Soporte técnico de Microsoft: para crear un nuevo caso de soporte técnico, vaya a la pestaña "Ayuda y soporte técnico" en Azure Portal y haga clic en "Nueva solicitud de soporte técnico".

## <a name="agent-installation-and-server-registration"></a>Instalación del agente y registro del servidor
<a id="agent-installation-failures"></a>**Solución de problemas de errores de instalación del agente**  
Si la instalación del agente de Azure File Sync no se produce correctamente, ejecute el siguiente comando desde un símbolo del sistema con privilegios elevados para habilitar el registro durante la instalación del agente:

```
StorageSyncAgent.msi /l*v Installer.log
```

Si la instalación produce error, revise el archivo installer.log para determinar la causa. 

> [!Note]  
> Se producirá un error en la instalación del agente si utiliza Microsoft Update y no se está ejecutando el servicio Windows Update.

<a id="server-registration-missing"></a>**El servidor no aparece en los servidores registrados de Azure Portal**  
Si un servidor no aparece en los servidores registrados de un servicio de sincronización de almacenamiento, siga los pasos siguientes:
1. Inicie sesión en el servidor que desea registrar.
2. Abra el Explorador de archivos y vaya al directorio de instalación del agente de sincronización de almacenamiento (la ubicación predeterminada es `C:\Program Files\Azure\StorageSyncAgent`). 
3. Ejecute ServerRegistration.exe y siga el asistente para registrar el servidor en un servicio de sincronización de almacenamiento.

<a id="server-already-registered"></a>**El registro de servidor muestra un mensaje después de instalar el agente de Azure File Sync que indica que el servidor ya está registrado**  
![Captura de pantalla del cuadro de diálogo Registro de servidor con el mensaje de error que indica que el servidor ya está registrado](media/storage-sync-files-troubleshoot/server-registration-1.png)

Este mensaje aparece si el servidor se registró anteriormente en un servicio de sincronización de almacenamiento. Para anular el registro del servidor del servicio de sincronización de almacenamiento actual y registrarlo en un servicio de sincronización de almacenamiento nuevo, siga los pasos para [anular el registro de un servidor de Azure File Sync](storage-sync-files-server-registration.md#unregister-the-server-with-storage-sync-service).

Si el servidor no aparece en la lista de servidores registrados en el servicio de sincronización de Storage, ejecute los siguientes comandos de PowerShell en el servidor cuyo registro desea eliminar:

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Reset-StorageSyncServer
```

> [!Note]  
> Si el servidor forma parte de un clúster, hay un parámetro opcional `Reset-StorageSyncServer -CleanClusterRegistration` que también quitará el registro de clúster.

<a id="web-site-not-trusted"></a>**Al registrar un servidor, obtengo numerosas respuestas de tipo "el sitio web no es de confianza", ¿por qué ocurre?**  
Este error se produce porque la directiva **Seguridad mejorada de Internet Explorer** está habilitada durante el registro de servidor. Para más información sobre cómo deshabilitar de manera apropiada la política **Seguridad mejorada de Internet Explorer**, consulte la sección [Preparación de los servidores de Windows Server para su uso con Azure File Sync](storage-sync-files-deployment-guide.md#prepare-windows-servers-for-use-with-azure-file-sync) del artículo [Cómo implementar Azure File Sync (versión preliminar)](storage-sync-files-deployment-guide.md).

## <a name="sync-group-management"></a>Administración de grupos de sincronización
<a id="cloud-endpoint-using-share"></a>**Se produce un error al crear el punto de conexión de nube con el siguiente error: "The specified Azure FileShare is already in use by a different CloudEndpoint" (El recurso compartido de archivos de Azure especificado ya está en uso por un punto de conexión de nube diferente)**  
Este error se produce si el recurso compartido de Azure Files ya está en uso por otro punto de conexión de nube. 

Si recibe este error y el recurso compartido de Azure Files no está actualmente en uso por un punto de conexión de nube, realice los siguientes pasos para borrar los metadatos de Azure File Sync en el recurso compartido de Azure Files:

> [!Warning]  
> Si se eliminan los metadatos en un recurso compartido de Azure Files que está actualmente en uso por un punto de conexión de nube, las operaciones de Azure File Sync producirán un error. 

1. Navegue al recurso compartido de Azure Files en Azure Portal.  
2. Haga clic con el botón derecho en el recurso compartido de Azure File y seleccione **Editar metadatos**
3. Haga clic con el botón derecho en SyncService y seleccione **Eliminar**.

<a id="cloud-endpoint-authfailed"></a>**Se produce el error AuthorizationFailed al crear el punto de conexión de nube**  
Este problema se produce si la cuenta de usuario no tiene derechos suficientes para crear un punto de conexión de nube. 

Para crear un punto de conexión de nube, la cuenta de usuario debe tener los siguientes permisos de autorización de Microsoft:  
* Lectura: obtener definición de roles
* Escritura: crear o actualizar una definición de rol personalizada
* Lectura: obtener asignación de roles
* Escritura: crear asignación de roles

Los siguientes roles integrados tienen los permisos adecuados de autorización de Microsoft:  
* Propietario
* Administrador de acceso de usuario

Para determinar si el rol de la cuenta de usuario tiene los permisos adecuados, haga lo siguiente:  
* Haga clic en **Grupos de recursos** en Azure Portal.
* Seleccione el grupo de recursos donde se encuentra la cuenta de almacenamiento y haga clic en **Control de acceso (IAM)**.
* Haga clic en el **rol** (por ejemplo, Propietario, Colaborador) de su cuenta de usuario.
* En la lista **Proveedor de recursos**, seleccione **Autorización de Microsoft**. 
* **Asignación de roles** debe tener permisos de **lectura** y **escritura**.
* **Definición de roles** debe tener permisos de **lectura** y **escritura**.

<a id="cloud-endpoint-deleteinternalerror"></a>**Se produce el error MgmtInternalError al eliminar el punto de conexión**  
Este problema puede producirse si el recurso compartido de archivos de Azure o la cuenta de almacenamiento se han eliminado antes de eliminar el punto de conexión de la nube. El problema se corregirá en una actualización futura y el punto de conexión de nube se podrá eliminar.

Para evitar que esto se produzca, elimine el punto de conexión de nube antes de eliminar el recurso compartido de archivos de Azure o la cuenta de almacenamiento.

## <a name="sync"></a>Sync
<a id="afs-change-detection"></a>**He creado un archivo directamente en el recurso compartido de archivos de Azure a través de SMB o a través del portal. ¿Cuánto tiempo tarda la sincronización del archivo con los servidores del grupo de sincronización?**  
[!INCLUDE [storage-sync-files-change-detection](../../../includes/storage-sync-files-change-detection.md)]

<a id="broken-sync"></a>**Solución de problemas cuando la sincronización no funciona en un servidor**  
Si la sincronización no funciona en un servidor, realice estos pasos:
- Compruebe que exista un punto de conexión de servidor en Azure Portal para el directorio que quiere sincronizar con un recurso compartido de archivos de Azure:
    
    ![Captura de pantalla de un grupo de sincronización y un punto de conexión de servidor en Azure Portal](media/storage-sync-files-troubleshoot/sync-troubleshoot-1.png)

- Revise los registros de eventos operativos y de diagnóstico con el Visor de eventos, que se encuentra en `Applications and Services\Microsoft\FileSync\Agent`.
- Confirme que el servidor tiene conectividad a Internet.
- Compruebe que el servicio Azure File Sync se ejecuta en el servidor; para ello, abra el complemento MMC de Servicios y compruebe que el servicio del agente de sincronización de almacenamiento (FileSyncSvc) se esté ejecutando.

<a id="replica-not-ready"></a>**Se produce el siguiente error en la sincronización: 0x80c8300f: La réplica no está lista para realizar la operación necesaria**  
Este error puede producirse si crea un punto de conexión de nube y usa un recurso compartido de archivos de Azure que contiene los datos. Una vez que se haya completado la detección de cambios en el recurso compartido de archivos de Azure (puede tardar hasta 24 horas), la sincronización debería empezar a funcionar correctamente.

<a id="broken-sync-files"></a>**Solución de problemas de archivos individuales que no se pueden sincronizar**  
Si no se pueden sincronizar archivos individuales, realice lo siguiente:
- Revise los registros de eventos operativos y de diagnóstico en `Applications and Services\Microsoft\FileSync\Agent` en el Visor de eventos.
- Compruebe que no haya ningún identificador abierto en el archivo.
    - Nota: Azure File Sync toma instantáneas de VSS periódicamente para sincronizar los archivos con identificadores abiertos.

## <a name="cloud-tiering"></a>Niveles de nube 
<a id="files-fail-tiering"></a>**Solución de problemas de archivos que no se apilan**  
Si los archivos no se apilan en Azure Files, realice los pasos siguientes:

- Compruebe que los archivos existen en el recurso compartido de archivos de Azure.
    - Nota: Un archivo debe sincronizarse con un recurso compartido de archivos de Azure antes de que se puedan apilar.
- Revise los registros de eventos operativos y de diagnóstico, ubicados en `Applications and Services\Microsoft\FileSync\Agent` en el Visor de eventos.
- Confirme que el servidor tiene conectividad a Internet. 
- Compruebe que los controladores de filtro de Azure File Sync (StorageSync.sys y StorageSyncGuard.sys) se están ejecutando.
    - Abra un símbolo del sistema con privilegios elevados, ejecute `fltmc` y compruebe que se muestran los controladores de filtro de sistema de archivos StorageSync.sys y StorageSyncGuard.sys.

<a id="files-fail-recall"></a>**Solución de problemas de archivos que no se pueden recuperar**  
Si no se pueden recuperar los archivos, realice los pasos siguientes:
- Revise los registros de eventos operativos y de diagnóstico, ubicados en `Applications and Services\Microsoft\FileSync\Agent` en el Visor de eventos.
- Compruebe que los archivos existen en el recurso compartido de archivos de Azure.
- Confirme que el servidor tiene conectividad a Internet. 
- Compruebe que los controladores de filtro de Azure File Sync (StorageSync.sys y StorageSyncGuard.sys) se están ejecutando.
    - Abra un símbolo del sistema con privilegios elevados, ejecute `fltmc` y compruebe que se muestran los controladores de filtro de sistema de archivos StorageSync.sys y StorageSyncGuard.sys.

<a id="files-unexpectedly-recalled"></a>**Solución de problemas de archivos que se recuperan de manera inesperada en un servidor**  
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

## <a name="see-also"></a>Otras referencias
- [Preguntas más frecuentes de Azure Files](storage-files-faq.md)
- [Solucione problemas de Azure Files en Windows](storage-troubleshoot-windows-file-connection-problems.md)
- [Solución de problemas de Azure Files en Linux](storage-troubleshoot-linux-file-connection-problems.md)
