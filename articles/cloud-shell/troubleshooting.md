---
title: "Solución de problemas de Azure Cloud Shell | Microsoft Docs"
description: "Solución de problemas de Azure Cloud Shell"
services: azure
documentationcenter: 
author: maertendMSFT
manager: angelc
tags: azure-resource-manager
ms.assetid: 
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/22/2018
ms.author: damaerte
ms.openlocfilehash: 52ee832b643af573d8236b266df17d36e485ead2
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/02/2018
---
# <a name="troubleshooting--limitations-of-azure-cloud-shell"></a>Solución de problemas y limitaciones de Azure Cloud Shell

Entre las resoluciones conocidas de problemas en Azure Cloud Shell se incluyen las siguientes:

## <a name="general-troubleshooting"></a>Solución general de problemas

### <a name="early-timeouts-in-firefox"></a>Tiempos de expiración tempranos en Firefox
- **Detalles**: Cloud Shell usa una conexión Websocket abierta para pasar la entrada/salida al explorador. Firefox tiene directivas preestablecidas que pueden cerrar la conexión WebSocket prematuramente, lo que genera tiempos de expiración tempranos en Cloud Shell.
- **Resolución**: abra FireFox y escriba "about:config" en el cuadro de dirección URL. Busque "network.websocket.timeout.ping.request" y cambie el valor de 0 a 10.

### <a name="storage-dialog---error-403-requestdisallowedbypolicy"></a>Cuadro de diálogo de Storage. Error: 403 RequestDisallowedByPolicy
- **Detalles**: al crear una cuenta de almacenamiento a través de Cloud Shell se produce un error debido a una directiva de Azure creada por el administrador. El mensaje de error incluirá: `The resource action 'Microsoft.Storage/storageAccounts/write' is disallowed by one or more policies.`
- **Resolución**: póngase en contacto con el administrador de Azure para quitar o actualizar la directiva que deniega la creación del almacenamiento.

### <a name="storage-dialog---error-400-disallowedoperation"></a>Cuadro de diálogo de Storage. Error: 400 DisallowedOperation
 - **Detalles**: cuando se usa una suscripción de Azure Active Directory, no puede crear almacenamiento.
 - **Resolución**: use una suscripción de Azure capaz de crear recursos de almacenamiento. Las suscripciones de Azure AD no pueden crear recursos de Azure.

### <a name="terminal-output---error-failed-to-connect-terminal-websocket-cannot-be-established-press-enter-to-reconnect"></a>Salida de terminal. Error: Failed to connect terminal: websocket cannot be established (Error al conectar con el terminal: no se puede establecer el websocket). Presione `Enter` para volver a conectarse.
 - **Detalles**: Cloud Shell requiere la capacidad de establecer una conexión de websocket con la infraestructura de Cloud Shell.
 - **Resolución**: Compruebe que ha configurado las opciones de red para permitir el envío de solicitudes https y websocket para dominios en *.console.azure.com.

## <a name="bash-troubleshooting"></a>Solución de problemas de Bash

### <a name="cannot-run-az-login"></a>No se puede ejecutar el inicio de sesión de az

- **Detalles**: la ejecución de `az login` no funcionará, ya que ya se ha autenticado en la cuenta utilizada para iniciar sesión en Cloud Shell o Azure Portal.
- **Resolución**: utilice la cuenta usada para iniciar sesión o cierre sesión y vuelva a autenticar con la cuenta de Azure prevista.

### <a name="cannot-run-the-docker-daemon"></a>No se puede ejecutar el demonio de Docker

- **Detalles**: Cloud Shell utiliza un contenedor para hospedar el entorno de shell, lo que hace que no se permita la ejecución del demonio.
- **Resolución**: utilice la [máquina de Docker](https://docs.docker.com/machine/overview/), que se instala de forma predeterminada para administrar contenedores de Docker desde un host de Docker remoto.

## <a name="powershell-troubleshooting"></a>Solución de problemas de PowerShell

### <a name="no-home-directory-persistence"></a>El directorio $Home no persiste

- **Detalles**: todos los datos que la aplicación (por ejemplo: git, vim, etc.) escribe en `$Home` no se conservan entre las sesiones de PowerShell.
- **Resolución**: en el perfil de PowerShell, cree un vínculo simbólico a la carpeta específica de la aplicación en `clouddrive` para $Home.

### <a name="ctrlc-doesnt-exit-out-of-a-cmdlet-prompt"></a>Ctrl+C no permite salir del símbolo del sistema de un cmdlet

- **Detalles**: cuando se intenta salir del símbolo del sistema de un cmdlet, `Ctrl+C` no permite hacerlo.
- **Resolución**: para salir del símbolo del sistema, presione `Ctrl+C` y luego `Enter`.

### <a name="gui-applications-are-not-supported"></a>No se admiten las aplicaciones con GUI

- **Detalles**: si un usuario inicia una aplicación con GUI, no se devuelve el símbolo del sistema. Por ejemplo, cuando un usuario clona un repositorio GitHub privado que tiene habilitada la autenticación en dos fases, aparece un cuadro de diálogo para completar esta autenticación.  
- **Resolución**: cierre y vuelva a abrir el shell.

### <a name="get-help--online-does-not-open-the-help-page"></a>Get-Help -online no abre la página de ayuda

- **Detalles**: si un usuario escriba `Get-Help Find-Module -online`, aparece un mensaje de error similar a: `Starting a browser to display online Help failed. No program or browser is associated to open the URI http://go.microsoft.com/fwlink/?LinkID=398574.`
- **Resolución**: copie la dirección URL y ábrala manualmente en el explorador.

### <a name="troubleshooting-remote-management-of-azure-vms"></a>Solución de problemas de la administración remota de máquinas virtuales de Azure

- **Detalles**: debido a la configuración predeterminada del Firewall de Windows para WinRM, es posible que el usuario vea el error siguiente: `Ensure the WinRM service is running. Remote Desktop into the VM for the first time and ensure it can be discovered.`
- **Resolución**: asegúrese de que la máquina virtual está en ejecución. Puede ejecutar `Get-AzureRmVM -Status` para saber el estado de la máquina virtual.  A continuación, agregue una nueva regla de firewall en la máquina virtual remota para permitir conexiones de WinRM desde cualquier subred, por ejemplo,

 ``` Powershell
 New-NetFirewallRule -Name 'WINRM-HTTP-In-TCP-PSCloudShell' -Group 'Windows Remote Management' -Enabled True -Protocol TCP -LocalPort 5985 -Direction Inbound -Action Allow -DisplayName 'Windows Remote Management - PSCloud (HTTP-In)' -Profile Public
 ```
 Puede usar [extensión de script personalizado de Azure](https://docs.microsoft.com/azure/virtual-machines/windows/extensions-customscript) para evitar iniciar sesión en la máquina virtual remota para agregar la nueva regla de firewall.
 Puede guardar el script anterior en un archivo, por ejemplo `addfirerule.ps1`, y cargarlo en el contenedor de almacenamiento de Azure.
 Luego, pruebe con el comando siguiente:

 ``` Powershell
 Get-AzureRmVM -Name MyVM1 -ResourceGroupName MyResourceGroup | Set-AzureRmVMCustomScriptExtension -VMName MyVM1 -FileUri https://mystorageaccount.blob.core.windows.net/mycontainer/addfirerule.ps1 -Run 'addfirerule.ps1' -Name myextension
 ```

### <a name="dir-caches-the-result-in-azure-drive"></a>`dir` almacena en caché el resultado en la unidad de Azure.

- **Detalles**: el resultado de `dir` se almacena en caché en la unidad de Azure.
- **Resolución**: después de crear o quitar un recurso en la vista de la unidad de Azure, ejecute `dir -force` para actualizar.

## <a name="general-limitations"></a>Limitaciones generales
Azure Cloud Shell tiene las limitaciones conocidas siguientes:

### <a name="system-state-and-persistence"></a>Persistencia y estado del sistema

La máquina que proporciona la sesión de Cloud Shell es temporal y se recicla después de que la sesión esté inactiva durante 20 minutos. Cloud Shell requiere montar un recurso compartido de archivos de Azure. Como resultado, la suscripción debe poder configurar los recursos de almacenamiento para tener acceso a Cloud Shell. Otras consideraciones:

* Con el almacenamiento montado, solo se conservan las modificaciones dentro del directorio `clouddrive`. En Bash, también se conserva el directorio `$Home`.
* Solo se pueden montar recursos compartidos de archivos de Azure desde la [región asignada](persisting-shell-storage.md#mount-a-new-clouddrive).
  * En Bash, ejecute `env` para buscar la región establecida como `ACC_LOCATION`.
* Azure Files solo admite cuentas de almacenamiento con redundancia local o de almacenamiento con redundancia geográfica.

### <a name="browser-support"></a>Compatibilidad con exploradores

Cloud Shell es compatible con las versiones más recientes de Microsoft Edge, Microsoft Internet Explorer, Google Chrome, Mozilla Firefox y Safari de Apple. Safari en modo privado no es compatible.

### <a name="copy-and-paste"></a>Copiar y pegar

[!include [copy-paste](../../includes/cloud-shell-copy-paste.md)]

### <a name="for-a-given-user-only-one-shell-can-be-active"></a>Solo puede haber un shell activo para un usuario determinado

Los usuarios solo pueden iniciar un tipo de shell a la vez, ya sea **Bash** o **PowerShell**. Pero puede haber varias instancias de Bash o PowerShell en ejecución al mismo tiempo. Los cambios entre Bash y PowerShell reinician Cloud Shell, lo que finaliza las sesiones existentes.

### <a name="usage-limits"></a>Límites de uso

Cloud Shell está pensado para casos de uso interactivos. Por tanto, todas las sesiones que no sean de este tipo y que se prolonguen durante mucho tiempo se finalizarán sin previo aviso.

## <a name="bash-limitations"></a>Limitaciones de Bash

### <a name="user-permissions"></a>Permisos de usuario

Los permisos se establecen como usuarios normales sin acceso a sudo. No se conserva cualquier instalación fuera del directorio `$Home`.

### <a name="editing-bashrc"></a>Editar .bashrc

Tenga cuidado al editar .bashrc, ya que puede producir errores inesperados en Cloud Shell.

## <a name="powershell-limitations"></a>Limitaciones de PowerShell

### <a name="slow-startup-time"></a>Tiempo de inicio lento

PowerShell en Azure Cloud Shell (versión preliminar) podría tardar hasta 60 segundos en inicializarse durante la versión preliminar.

### <a name="default-file-location-when-created-from-azure-drive"></a>Ubicación del archivo predeterminada cuando se crea a partir de la unidad de Azure:

Con los cmdlets de PowerShell, los usuarios no pueden crear archivos en la unidad de Azure. Cuando los usuarios crean nuevos archivos con otras herramientas, como vim o nano, los archivos se guardan de forma predeterminada en la carpeta C:\Users. 

### <a name="gui-applications-are-not-supported"></a>No se admiten las aplicaciones con GUI

Si el usuario ejecuta un comando que crearía un cuadro de diálogo de Windows, como `Connect-AzureAD` o `Login-AzureRMAccount`, se ve un mensaje de error como el siguiente: `Unable to load DLL 'IEFRAME.dll': The specified module could not be found. (Exception from HRESULT: 0x8007007E)`.
