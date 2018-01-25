---
title: "Información general del agente de máquina virtual de Azure | Microsoft Docs"
description: "Información general del agente de máquina virtual de Azure"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: danielsollondon
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 0a1f212e-053e-4a39-9910-8d622959f594
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 11/17/2016
ms.author: danis
ms.openlocfilehash: 3be001c2ad63c5872af4740f136438034e1e406b
ms.sourcegitcommit: 28178ca0364e498318e2630f51ba6158e4a09a89
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/24/2018
---
# <a name="azure-virtual-machine-agent-overview"></a>Información general del agente de máquina virtual de Azure

El agente de máquina virtual de Microsoft Azure (agente VM) es un proceso ligero y seguro que administra la interacción de VM con el controlador de tejido de Azure. El agente de VM tiene un rol principal que consiste en habilitar y ejecutar extensiones de máquina virtual de Azure. Las extensiones de VM habilitan la configuración posterior a la implementación de máquinas virtuales, como la instalación y configuración de software. Las extensiones de máquina virtual también habilitan características de recuperación, como el restablecimiento de la contraseña administrativa de una máquina virtual. Sin el agente de VM de Azure, no se pueden ejecutar las extensiones de máquina virtual.

En este documento se describe la instalación, detección y eliminación del agente de máquina virtual de Azure.

## <a name="install-the-vm-agent"></a>Instalar el agente de VM

### <a name="azure-gallery-image"></a>Imagen de la galería de Azure

El agente de VM de Azure se instala de forma predeterminada en cualquier máquina virtual Windows implementada a partir de una imagen de la galería de Azure. Al implementar una imagen de la galería de Azure desde el portal, PowerShell, la interfaz de la línea de comandos o una plantilla de Azure Resource Manager, también se instalará el agente de VM de Azure. 

### <a name="manual-installation"></a>Instalación manual

El agente de VM de Windows puede instalarse manualmente mediante un paquete de Windows Installer. La instalación manual puede ser necesaria cuando se crea una imagen de máquina virtual personalizada que se implementará en Azure. Para instalar manualmente el agente de VM de Windows, descargue el instalador del agente de VM desde esta ubicación de [descarga del agente de VM de Microsoft Azure](http://go.microsoft.com/fwlink/?LinkID=394789). 

El agente de VM se puede instalar haciendo doble clic en el archivo de Windows Installer. Para una instalación automatizada o desatendida del agente de VM, ejecute el siguiente comando.

```cmd
msiexec.exe /i WindowsAzureVmAgent.2.7.1198.778.rd_art_stable.160617-1120.fre /quiet
```

## <a name="detect-the-vm-agent"></a>Detección del agente de VM

### <a name="powershell"></a>PowerShell

El módulo de PowerShell de Azure Resource Manager puede utilizarse para recuperar información sobre Azure Virtual Machines. La ejecución de `Get-AzureRmVM` devuelve una gran cantidad de información que incluye el estado de aprovisionamiento para el agente de VM de Azure.

```PowerShell
Get-AzureRmVM
```

Lo siguiente es solo un subconjunto de la salida `Get-AzureRmVM`. Observe la propiedad `ProvisionVMAgent` anidada dentro de `OSProfile`. Esta propiedad se puede utilizar para determinar si el agente de VM se ha implementado en la máquina virtual.

```PowerShell
OSProfile                  :
  ComputerName             : myVM
  AdminUsername            : muUserName
  WindowsConfiguration     :
    ProvisionVMAgent       : True
    EnableAutomaticUpdates : True
```

El siguiente script se puede utilizar para devolver una lista concisa de nombres de máquina virtual y el estado del agente de VM.

```PowerShell
$vms = Get-AzureRmVM

foreach ($vm in $vms) {
    $agent = $vm | Select -ExpandProperty OSProfile | Select -ExpandProperty Windowsconfiguration | Select ProvisionVMAgent
    Write-Host $vm.Name $agent.ProvisionVMAgent
}
```

### <a name="manual-detection"></a>Detección manual

Cuando inicia sesión en una VM de Windows Azure, el administrador de tareas se puede usar para examinar los procesos en ejecución. Para comprobar el agente de VM de Azure, abra el Administrador de tareas > haga clic en la pestaña Detalles y busque un nombre de proceso `WindowsAzureGuestAgent.exe`. La presencia de este proceso indica que el agente de VM está instalado.

## <a name="upgrade-the-vm-agent"></a>Actualización del agente de VM

El agente de VM de Azure para Windows se actualiza automáticamente. A medida que se implementan nuevas máquinas virtuales en Azure, reciben el agente de VM más reciente. Las imágenes de VM personalizadas se deben actualizar manualmente para que incluya el nuevo agente de VM.
