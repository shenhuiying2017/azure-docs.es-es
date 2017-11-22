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
ms.date: 11/2/2017
ms.author: damaerte
ms.openlocfilehash: 59aec1cb6433e90caf683b4bce98c92e9f79fd18
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/15/2017
---
# <a name="troubleshooting-azure-cloud-shell"></a>Solución de problemas de Azure Cloud Shell

Entre las resoluciones conocidas de problemas en Azure Cloud Shell, se incluyen las siguientes:

## <a name="general-resolutions"></a>Resoluciones generales

### <a name="storage-dialog---error-403-requestdisallowedbypolicy"></a>Cuadro de diálogo de Storage. Error: 403 RequestDisallowedByPolicy
- **Detalles**: al crear una cuenta de almacenamiento a través de Cloud Shell se produce un error debido a una directiva de Azure creada por el administrador. El mensaje de error incluirá: `The resource action 'Microsoft.Storage/storageAccounts/write' is disallowed by one or more policies.`
- **Resolución**: póngase en contacto con el administrador de Azure para quitar o actualizar la directiva que deniega la creación del almacenamiento.

### <a name="storage-dialog---error-400-disallowedoperation"></a>Cuadro de diálogo de Storage. Error: 400 DisallowedOperation
 - **Detalles**: cuando se usa una suscripción de Azure Active Directory, no puede crear almacenamiento.
 - **Resolución**: use una suscripción de Azure capaz de crear recursos de almacenamiento. Las suscripciones de Azure AD no pueden crear recursos de Azure.

### <a name="terminal-output---error-failed-to-connect-terminal-websocket-cannot-be-established-press-enter-to-reconnect"></a>Salida de terminal. Error: Failed to connect terminal: websocket cannot be established (Error al conectar con el terminal: no se puede establecer el websocket). Presione `Enter` para volver a conectarse.
 - **Detalles**: Cloud Shell requiere la capacidad de establecer una conexión de websocket con la infraestructura de Cloud Shell.
 - **Resolución**: Compruebe que ha configurado las opciones de red para permitir el envío de solicitudes https y websocket para dominios en *.console.azure.com.

## <a name="bash-resolutions"></a>Resoluciones de Bash

### <a name="cannot-run-az-login"></a>No se puede ejecutar el inicio de sesión de az

- **Detalles**: la ejecución de `az login` no funcionará, ya que ya se ha autenticado en la cuenta utilizada para iniciar sesión en Cloud Shell o Azure Portal.
- **Resolución**: utilice la cuenta usada para iniciar sesión o cierre sesión y vuelva a autenticar con la cuenta de Azure prevista.

### <a name="cannot-run-the-docker-daemon"></a>No se puede ejecutar el demonio de Docker

- **Detalles**: Cloud Shell utiliza un contenedor para hospedar el entorno de shell, lo que hace que no se permita la ejecución del demonio.
- **Resolución**: utilice la [máquina de Docker](https://docs.docker.com/machine/overview/), que se instala de forma predeterminada para administrar contenedores de Docker desde un host de Docker remoto.

## <a name="powershell-resolutions"></a>Resoluciones de PowerShell

### <a name="no-home-directory-persistence"></a>El directorio $Home no persiste

- **Detalles**: todos los datos que la aplicación (por ejemplo: git, vim, etc.) escribe en `$Home` no se conservan entre las sesiones de PowerShell.
- **Resolución**: en el perfil de PowerShell, cree un vínculo simbólico a la carpeta específica de la aplicación en `clouddrive` para $Home.

### <a name="ctrlc-doesnt-exit-out-of-a-cmdlet-prompt"></a>Ctrl+C no permite salir del símbolo del sistema de un cmdlet

- **Detalles**: cuando se intenta salir del símbolo del sistema de un cmdlet, `Ctrl+C` no permite hacerlo.
- **Resolución**: para salir del símbolo del sistema, presione `Ctrl+C` y luego `Enter`.

### <a name="gui-applications-are-not-supported"></a>No se admiten las aplicaciones con GUI

- **Detalles**: si un usuario inicia una aplicación con GUI, no se devuelve el símbolo del sistema. Por ejemplo, cuando un usuario clona un repositorio GitHub privado que tiene habilitada la autenticación en dos fases, aparece un cuadro de diálogo para completar esta autenticación.
- **Resolución**: presione `Ctrl+C` para salir del comando.

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
