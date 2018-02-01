---
title: "Solución de problemas de Azure File Sync (versión preliminar) | Microsoft Docs"
description: "Solución de problemas comunes con Azure File Sync."
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
ms.date: 12/04/2017
ms.author: wgries
ms.openlocfilehash: 7562e43f58f303ea34a08b8b9e056a0c3d0c10d0
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/17/2018
---
# <a name="troubleshoot-azure-file-sync-preview"></a>Solución de problemas de Azure File Sync (versión preliminar)
Use Azure File Sync (versión preliminar) para centralizar los recursos compartidos de archivos de su organización en Azure Files sin renunciar a la flexibilidad, el rendimiento y la compatibilidad de un servidor de archivos local. Azure File Sync transforma Windows Server en una caché rápida de los recursos compartidos de archivos de Azure. Puede usar cualquier protocolo disponible en Windows Server para acceder a sus datos localmente, como SMB, NFS y FTPS. Puede tener todas las cachés que necesite en todo el mundo.

Este artículo está diseñado para ayudarle a solucionar problemas que podrían producirse con la implementación de Azure File Sync. Se describe cómo recopilar registros importantes del sistema si es necesario investigar los problemas en mayor profundidad. Si no encuentra una respuesta a su pregunta, póngase en contacto con nosotros mediante los siguientes canales (en orden incremental):

1. La sección Comentarios de este artículo.
2. [Foro de Azure Storage](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata).
3. [UserVoice de Azure Files](https://feedback.azure.com/forums/217298-storage/category/180670-files). 
4. Soporte técnico de Microsoft. Para crear una solicitud de soporte técnico, en Azure Portal, vaya a la pestaña **Ayuda**, seleccione el botón **Ayuda y soporte técnico** y elija **Nueva solicitud de soporte técnico**.

## <a name="agent-installation-and-server-registration"></a>Instalación del agente y registro del servidor
<a id="agent-installation-failures"></a>**Solución de problemas de errores de instalación del agente**  
Si la instalación del agente de Azure File Sync no se produce correctamente, ejecute el siguiente comando desde un símbolo del sistema con privilegios elevados para activar el registro durante la instalación del agente:

```
StorageSyncAgent.msi /l*v Installer.log
```

Si la instalación produce error, revise el archivo installer.log para determinar la causa. 

> [!Note]  
> Se producirá un error en la instalación del agente si utiliza Microsoft Update y no se está ejecutando el servicio Windows Update.

<a id="agent-installation-websitename-failure"></a>**Error de instalación del agente: "El agente de sincronización de Azure Storage finalizó antes de tiempo"**  
Este problema puede producirse si se cambia el nombre predeterminado del sitio web de IIS. Para solucionar este problema, cambie el nombre del sitio web predeterminado de IIS por "Default Web Site" y vuelva a intentar la instalación. El problema se solucionará en una actualización futura del agente. 

<a id="server-registration-missing"></a>**El servidor no aparece en los servidores registrados de Azure Portal**  
Si un servidor no aparece en los **servidores registrados** de un servicio de sincronización de almacenamiento:
1. Inicie sesión en el servidor que desea registrar.
2. Abra el Explorador de archivos y, a continuación, vaya al directorio de instalación del agente de sincronización de almacenamiento (la ubicación predeterminada es C:\Program Files\Azure\StorageSyncAgent). 
3. Ejecute ServerRegistration.exe y complete el asistente para registrar el servidor en un servicio de sincronización de almacenamiento.

<a id="server-already-registered"></a>**El registro de servidor muestra un mensaje durante la instalación del agente de Azure File Sync que indica que el servidor ya está registrado** 

![Captura de pantalla del cuadro de diálogo Registro de servidor con el mensaje que indica que el servidor ya está registrado](media/storage-sync-files-troubleshoot/server-registration-1.png)

Este mensaje aparece si el servidor se registró anteriormente en un servicio de sincronización de almacenamiento. Para anular el registro del servidor del servicio de sincronización de almacenamiento actual y registrarlo en un servicio de sincronización de almacenamiento nuevo, complete los pasos que se describen en [Anular el registro de un servidor de Azure File Sync](storage-sync-files-server-registration.md#unregister-the-server-with-storage-sync-service).

Si el servidor no aparece en la lista de **servidores registrados** en el servicio de sincronización de almacenamiento, ejecute los siguientes comandos de PowerShell en el servidor cuyo registro desea eliminar:

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Reset-StorageSyncServer
```

> [!Note]  
> Si el servidor forma parte de un clúster, puede utilizar el parámetro opcional *Reset-StorageSyncServer -CleanClusterRegistration* para quitar también el registro del clúster.

<a id="web-site-not-trusted"></a>**Al registrar un servidor, obtengo numerosas respuestas de tipo "el sitio web no es de confianza". ¿Por qué?**  
Este problema se produce cuando la directiva **Seguridad mejorada de Internet Explorer** está habilitada durante el registro de servidor. Para más información sobre cómo deshabilitar de manera apropiada la directiva **Seguridad mejorada de Internet Explorer**, consulte la sección [Preparación de los servidores de Windows Server para su uso con Azure File Sync](storage-sync-files-deployment-guide.md#prepare-windows-server-to-use-with-azure-file-sync) del artículo [Cómo implementar Azure File Sync (versión preliminar)](storage-sync-files-deployment-guide.md).

## <a name="sync-group-management"></a>Administración de grupos de sincronización
<a id="cloud-endpoint-using-share"></a>**Se produce un error al crear el punto de conexión de nube con el mensaje: "The specified Azure FileShare is already in use by a different CloudEndpoint"** (El recurso compartido de archivos de Azure especificado ya está siendo usado por un punto de conexión de nube diferente)  
Este error se produce si el recurso compartido de archivos de Azure ya está siendo utilizado por otro punto de conexión de nube. 

Si ve este mensaje y el recurso compartido de archivos de Azure no está siendo utilizado actualmente por un punto de conexión de nube, complete los siguientes pasos para borrar los metadatos de Azure File Sync en el recurso compartido de archivos de Azure:

> [!Warning]  
> Si se eliminan los metadatos en un recurso compartido de archivos de Azure que está siendo utilizado por un punto de conexión de nube, las operaciones de Azure File Sync producirán un error. 

1. Vaya al recurso compartido de archivos de Azure en Azure Portal.  
2. Haga clic con el botón derecho en el recurso compartido de archivos de Azure y seleccione **Editar metadatos**.
3. Haga clic con el botón derecho en **SyncService** y, a continuación, seleccione **Eliminar**.

<a id="cloud-endpoint-authfailed"></a>**Se produce el error "AuthorizationFailed" al crear el punto de conexión de nube**  
Este problema se produce si la cuenta de usuario no tiene derechos suficientes para crear un punto de conexión de nube. 

Para crear un punto de conexión de nube, la cuenta de usuario debe tener los siguientes permisos de autorización de Microsoft:  
* Lectura: obtener definición de roles
* Escritura: crear o actualizar una definición de rol personalizada
* Lectura: obtener asignación de roles
* Escritura: crear asignación de roles

Los siguientes roles integrados tienen los permisos necesarios de autorización de Microsoft:  
* Propietario
* Administrador de acceso de usuario

Para determinar si su rol de la cuenta de usuario tiene los permisos necesarios:  
1. En Azure Portal, seleccione **Grupos de recursos**.
2. Seleccione el grupo de recursos donde se encuentra la cuenta de almacenamiento y seleccione en **Control de acceso (IAM)**.
3. Seleccione el **rol** (por ejemplo, propietario o colaborador) para su cuenta de usuario.
4. En la lista **Proveedor de recursos**, seleccione **Autorización de Microsoft**. 
    * **Asignación de roles** debe tener permisos de **lectura** y **escritura**.
    * **Definición de roles** debe tener permisos de **lectura** y **escritura**.

<a id="server-endpoint-createjobfailed"></a>**Error de creación del punto de punto de conexión de servidor: "MgmtServerJobFailed" (código de Error: -2134375898)**                                                                                                                           
Este problema se produce si la ruta de acceso del punto de conexión de servidor se encuentra en el volumen del sistema y los niveles de la nube están habilitados. Los niveles de nube no se admiten en el volumen del sistema. Para crear un punto de conexión de servidor en el volumen del sistema, deshabilite los niveles de la nube al crear el punto de conexión de servidor.

<a id="server-endpoint-deletejobexpired"></a>**Error del punto de conexión de servidor: "MgmtServerJobExpired"**                
Este problema se produce si el servidor está sin conexión o no tiene conectividad de red. Si el servidor ya no está disponible, anule el registro del servidor en el portal, lo que eliminará los puntos de conexión de servidor. Para eliminar los puntos de conexión de servidor, siga los pasos que se describen en [Anular el registro de un servidor de Azure File Sync](storage-sync-files-server-registration.md#unregister-the-server-with-storage-sync-service).

## <a name="sync"></a>Sync
<a id="afs-change-detection"></a>**Si creé un archivo directamente en el recurso compartido de archivos de Azure mediante SMB o el portal, ¿cuánto tiempo tarda el archivo en sincronizarse con los servidores del grupo de sincronización?**  
[!INCLUDE [storage-sync-files-change-detection](../../../includes/storage-sync-files-change-detection.md)]

<a id="broken-sync"></a>**Se produce un error en la sincronización en un servidor**  
Si se produce un error en la sincronización en un servidor:
1. Compruebe que existe un punto de conexión de servidor en Azure Portal para el directorio que quiere sincronizar con un recurso compartido de archivos de Azure:
    
    ![Una captura de pantalla de un grupo de sincronización con un punto de conexión de nube y un punto de conexión de servidor en Azure Portal](media/storage-sync-files-troubleshoot/sync-troubleshoot-1.png)

2. En el Visor de eventos, revise los registros de eventos operativos y de diagnósticos, ubicados en Applications and Services\Microsoft\FileSync\Agent.
    1. Compruebe que el servidor tiene conectividad a Internet.
    2. Compruebe que el servicio Azure File Sync se está ejecutando en el servidor. Para hacer esto, abra el complemento MMC de Servicios y compruebe que el servicio del agente de sincronización de almacenamiento (FileSyncSvc) se está ejecutando.

<a id="replica-not-ready"></a>**Se produce este error en la sincronización: "0x80c8300f: La réplica no está lista para realizar la operación necesaria"**  
Este problema puede producirse si crea un punto de conexión de nube y usa un recurso compartido de archivos de Azure que contiene los datos. Cuando el trabajo de detección de cambios termine de ejecutarse en el recurso compartido de archivos de Azure (puede tardar hasta 24 horas), la sincronización debería comenzar a funcionar correctamente.

<a id="broken-sync-files"></a>**Solución de problemas de archivos individuales que no se pueden sincronizar**  
Si los archivos individuales no se podrán sincronizar:
1. En el Visor de eventos, revise los registros de eventos operativos y de diagnósticos, ubicados en Applications and Services\Microsoft\FileSync\Agent.
2. Compruebe que no haya ningún identificador abierto en el archivo.

    > [!NOTE]
    > Azure File Sync toma instantáneas de VSS periódicamente para sincronizar los archivos que tienen identificadores abiertos.

Actualmente no se admite el movimiento de recursos a otra suscripción ni a un inquilino de Azure AD distinto.  Si la suscripción se mueve a un inquilino diferente, el recurso compartido de archivos de Azure deja de estar accesible a nuestro servicio debido al cambio de propiedad. Si se cambia el inquilino, debe eliminar los puntos de conexión del servidor y el punto de conexión en la nube (consulte la sección Administración de grupos de sincronización para obtener instrucciones sobre cómo limpiar el recurso compartido de archivos de Azure para su reutilización) y volver a crear el grupo de sincronización.

## <a name="cloud-tiering"></a>Niveles de nube 
Los errores en la organización en niveles en la nube pueden producirse de dos formas:

- Los archivos pueden dar error al organizarse en niveles, lo que significa que Azure File Sync lo intenta sin éxito con un archivo en Azure Files.
- Los archivos pueden producir un error en la recuperación, lo que significa que el filtro del sistema de archivos de Azure File Sync (StorageSync.sys) da un error al descargar los datos cuando un usuario intenta tener acceso a un archivo que ha sido organizado en niveles.

Hay dos clases principales de errores que pueden producirse de alguno de esos modos:

- Errores de almacenamiento en la nube
    - *Problemas transitorios de disponibilidad del servicio de almacenamiento*. Vea [Acuerdo de Nivel de Servicio (SLA) para Azure Storage](https://azure.microsoft.com/support/legal/sla/storage/v1_2/) para más información.
    - *Recurso compartido de archivos de Azure inaccesible*. Este error normalmente ocurre al eliminarse el recurso compartido de archivos de Azure cuando sigue siendo un punto de conexión en la nube en un grupo de sincronización.
    - *Cuenta de almacenamiento inaccesible*. Este error suele ocurrir cuando se elimina la cuenta de almacenamiento mientras todavía tiene un recurso compartido de archivos de Azure que es un punto de conexión en la nube en un grupo de sincronización. 
- Errores del servidor 
    - *No está cargado el filtro de sistema de archivos de Azure File Sync (StorageSync.sys)*. Para responder a solicitudes de organización por niveles o recuperación, debe haber cargado el filtro del sistema de archivos de Azure File Sync. El filtro puede no cargarse por varios motivos, pero el más común es que un administrador lo descargue manualmente. El filtro del sistema de archivos de Azure File Sync se debe cargar en todo momento para que este funcione de forma adecuada.
    - *El punto de reanálisis falta, está dañado o ha sufrido algún otro problema*. Un punto de reanálisis es una estructura de datos especial en un archivo que consta de dos partes:
        1. Una etiqueta de reanálisis, que indica al sistema operativo que el filtro del sistema de archivos de Azure File Sync (StorageSync.sys) puede necesitar realizar alguna acción de E/S en el archivo. 
        2. Repita el análisis de los datos, lo que indica al sistema de archivos que filtre el URI del archivo en el punto de conexión en la nube asociado (el recurso compartido de archivos de Azure). 
        
        La manera más común de que un punto de reanálisis pueda resultar dañado es que un administrador intente modificar la etiqueta o sus datos. 
    - *Problemas de conectividad de red*. Con el fin organizar en niveles un archivo o de recuperarlo, el servidor debe tener conectividad a Internet.

En las secciones siguientes se indica cómo solucionar problemas de los niveles en la nube y determinar si se trata de un problema de almacenamiento en la nube o del servidor.

<a id="files-fail-tiering"></a>**Solución de problemas de archivos que no se apilan**  
Si no se pueden apilar archivos en Azure Files:

1. Compruebe que los archivos existen en el recurso compartido de los archivos de Azure.

    > [!NOTE]
    > Un archivo debe sincronizarse con un recurso compartido de archivos de Azure antes de que se puedan apilar.
2. En el Visor de eventos, revise los registros de eventos operativos y de diagnósticos, ubicados en Applications and Services\Microsoft\FileSync\Agent.
    1. Compruebe que el servidor tiene conectividad a Internet. 
    2. Compruebe que los controladores de filtro de Azure File Sync (StorageSync.sys y StorageSyncGuard.sys) se están ejecutando:
        - En un símbolo del sistema con privilegios elevados, ejecute `fltmc`. Compruebe que aparecen los controladores de filtro del sistema de archivos StorageSync.sys y StorageSyncGuard.sys.

<a id="files-fail-recall"></a>**Solución de problemas de archivos que no se pueden recuperar**  
Si no se pueden recuperar archivos:
1. En el Visor de eventos, revise los registros de eventos operativos y de diagnósticos, ubicados en Applications and Services\Microsoft\FileSync\Agent.
    1. Compruebe que los archivos existen en el recurso compartido de los archivos de Azure.
    2. Compruebe que el servidor tiene conectividad a Internet. 
    3. Compruebe que los controladores de filtro de Azure File Sync (StorageSync.sys y StorageSyncGuard.sys) se están ejecutando:
        - En un símbolo del sistema con privilegios elevados, ejecute `fltmc`. Compruebe que aparecen los controladores de filtro del sistema de archivos StorageSync.sys y StorageSyncGuard.sys.

<a id="files-unexpectedly-recalled"></a>**Solución de problemas de archivos que se recuperan de manera inesperada en un servidor**  
Las aplicaciones antivirus, de backup y de otro tipo que leen gran cantidad de archivos provocan recuperaciones no previstas salvo que respeten la omisión del atributo sin conexión y pasen por alto la lectura del contenido de esos archivos. Omitir archivos sin conexión para los productos que admiten esta opción ayuda a evitar recuperaciones imprevistas durante las operaciones, como análisis antivirus o trabajos de backup.

Consulte con el proveedor de software para aprender cómo configurar su solución para omitir la lectura de archivos sin conexión.

Las recuperaciones imprevistas también se podrían producir en otros escenarios, como al buscar archivos en el Explorador de archivos. Abrir una carpeta que tiene archivos de niveles en la nube en el Explorador de archivos en el servidor podría producir recuperaciones imprevistas. Esto es incluso más probable si una solución antivirus está habilitada en el servidor.

## <a name="general-troubleshooting"></a>Solución general de problemas
Si tiene problemas con Azure File Sync en un servidor, empiece por los pasos siguientes:
1. Revise los registros de eventos operativos y de diagnóstico en el Visor de eventos.
    - Los problemas de sincronización, de niveles y de recuperación se registran en los registros operativos y de diagnóstico en Applications and Services\Microsoft\FileSync\Agent.
    - Los problemas relacionados con la administración de un servidor (por ejemplo, los valores de configuración) se registran en los registros de eventos operativos y de diagnósticos en Applications and Services\Microsoft\FileSync\Management.
2. Compruebe que el servicio Azure File Sync se está ejecutando en el servidor:
    - Abra el complemento MMC de Servicios y compruebe que el servicio del agente de sincronización de almacenamiento (FileSyncSvc) se está ejecutando.
3. Compruebe que los controladores de filtro de Azure File Sync (StorageSync.sys y StorageSyncGuard.sys) se están ejecutando:
    - En un símbolo del sistema con privilegios elevados, ejecute `fltmc`. Compruebe que aparecen los controladores de filtro del sistema de archivos StorageSync.sys y StorageSyncGuard.sys.

Si no se resuelve el problema, ejecute la herramienta AFSDiag:
1. Cree un directorio donde se guardará la salida de AFSDiag (por ejemplo, C:\Output).
2. Abra una ventana de PowerShell con privilegios elevados y ejecute los comandos siguientes (presione Entrar después de cada comando):

    ```PowerShell
    cd "c:\Program Files\Azure\StorageSyncAgent"
    Import-Module .\afsdiag.ps1
    Debug-Afs c:\output # Note: Use the path created in step 1.
    ```

3. Para el nivel de seguimiento del modo de kernel de Azure File Sync, escriba **1** (a menos que se especifique lo contrario, para crear seguimientos más detallados) y presione Entrar.
4. Para el nivel de seguimiento del modo de usuario de Azure File Sync, escriba **1** (a menos que se especifique lo contrario, para crear seguimientos más detallados) y presione Entrar.
5. Reproduzca el problema. Cuando haya terminado, escriba **D**.
6. Un archivo .zip que contiene los archivos de seguimiento y registros se guarda en el directorio de salida que especificó.

## <a name="see-also"></a>Otras referencias
- [Preguntas más frecuentes de Azure Files](storage-files-faq.md)
- [Solucione problemas de Azure Files en Windows](storage-troubleshoot-windows-file-connection-problems.md)
- [Solución de problemas de Azure Files en Linux](storage-troubleshoot-linux-file-connection-problems.md)
