---
title: "Extensión de script personalizada en una máquina virtual Windows | Microsoft Docs"
description: "Automatizar tareas de configuración de máquina virtual de Azure mediante la extensión de script personalizada para ejecutar scripts de PowerShell en una máquina virtual remota de Windows"
services: virtual-machines-windows
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: ebb7340a-8f61-4d3c-a290-d7bf8de2d0bd
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 12/15/2016
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: 862b19b334b0a4da6e61983f428e3e6732189870
ms.openlocfilehash: a5e3fc8a1160fc3eef3c98320840992f86b67d14


---
# <a name="custom-script-extension-for-windows-virtual-machines"></a>Extensión de script personalizada para máquinas virtuales Windows

Este artículo ofrece información general del uso de la extensión de script personalizada en máquinas virtuales Windows mediante los cmdlets de Azure PowerShell con las API de Azure Service Management.

Las extensiones de máquina virtual (VM) se crean por Microsoft y editores de confianza de terceros para extender la funcionalidad de la máquina virtual. Para obtener información general de las extensiones de máquina virtual, vea [Características y extensiones de máquina virtual de Azure](virtual-machines-windows-extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

> [!IMPORTANT] 
> Azure tiene dos modelos de implementación diferentes para crear recursos y trabajar con ellos: [Resource Manager y el clásico](../azure-resource-manager/resource-manager-deployment-model.md). En este artículo se trata el modelo de implementación clásico. Microsoft recomienda que las implementaciones más recientes usen el modelo del Administrador de recursos. Aprenda a [realizar estos pasos con el modelo de Resource Manager](virtual-machines-windows-extensions-customscript.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="custom-script-extension-overview"></a>Introducción a la extensión de script personalizada

La extensión de script personalizada para Windows le permite ejecutar scripts de PowerShell en una VM remota, sin iniciar sesión en ella. Puede ejecutar los scripts después de aprovisionar la VM o en cualquier momento durante el ciclo de vida de la VM sin necesidad de abrir los puertos adicionales. Los casos de uso más comunes para la ejecución de la extensión de script personalizada incluyen la ejecución, la instalación y la configuración de software adicional en la VM después de su aprovisionamiento.

### <a name="prerequisites-for-running-the-custom-script-extension"></a>Requisitos previos para ejecutar la extensión de script personalizada

1. Instale <a href="http://azure.microsoft.com/downloads" target="_blank">cmdlets de Azure PowerShell</a>, versión 0.8.0 o superior.
2. Si desea que los scripts se ejecuten en una VM existente, asegúrese de que el Agente de VM está habilitado en la VM. Si no está instalado, siga estos [pasos](virtual-machines-windows-classic-agents-and-extensions.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json). Si la VM se crea en el portal de Azure, el agente de VM se instala de forma predeterminada.
3. Cargue las secuencias de comandos que desea ejecutar en la máquina virtual para el almacenamiento de Azure. Las secuencias de comandos pueden proceder de un único contenedor de almacenamiento o de varios.
4. El script debe crearse de forma tal que el script de entrada que inicia la extensión inicie, a su vez, otros scripts.

## <a name="custom-script-extension-scenarios"></a>Escenarios de la extensión de script personalizada

### <a name="upload-files-to-the-default-container"></a>Cargar archivos en el contenedor predeterminado

El siguiente ejemplo le muestra cómo puede ejecutar los scripts en la VM si están en el contenedor de almacenamiento de la cuenta predeterminada de su suscripción. Cargue los scripts en ContainerName. Puede comprobar la cuenta de almacenamiento predeterminada mediante el comando **Get-AzureSubscription –Default** .

En el ejemplo siguiente se crea una nueva VM, pero puede ejecutar el mismo escenario en una VM existente.

```powershell
# Create a new VM in Azure.
$vm = New-AzureVMConfig -Name $name -InstanceSize Small -ImageName $imagename
$vm = Add-AzureProvisioningConfig -VM $vm -Windows -AdminUsername $username -Password $password

# Add Custom Script extension to the VM. 
# The container name refers to the storage container that contains the file.
$vm = Set-AzureVMCustomScriptExtension -VM $vm -ContainerName $container -FileName 'start.ps1'
New-AzureVM -ServiceName $servicename -Location $location -VMs $vm

# Viewing the  script execution output.
$vm = Get-AzureVM -ServiceName $servicename -Name $name
# Use the position of the extension in the output as index.
$vm.ResourceExtensionStatusList[i].ExtensionSettingStatus.SubStatusList
```

### <a name="upload-files-to-a-non-default-storage-container"></a>Cargar archivos a un contenedor de almacenamiento no predeterminado

Este escenario muestra cómo usar un contenedor de almacenamiento no predeterminado en la misma suscripción o en una suscripción diferente para cargar archivos y scripts. Este ejemplo muestra una VM existente, pero se pueden realizar las mismas operaciones al crear una VM.

```powershell
Get-AzureVM -Name $name -ServiceName $servicename | ` 
Set-AzureVMCustomScriptExtension -StorageAccountName $storageaccount -StorageAccountKey $storagekey ` 
-ContainerName $container -FileName 'file1.ps1','file2.ps1' -Run 'file.ps1' | ` 
Update-AzureVM
```

### <a name="upload-scripts-to-multiple-containers-across-different-storage-accounts"></a>Carga de secuencias de comandos en varios contenedores en diferentes cuentas de almacenamiento

Si los archivos de scripts se almacenan en varios contenedores, debe ofrecer la dirección URL de firmas de acceso compartido (SAS) completas para que los archivos ejecuten los scripts.

```powershell
Get-AzureVM -Name $name -ServiceName $servicename | ` 
Set-AzureVMCustomScriptExtension -StorageAccountName $storageaccount -StorageAccountKey $storagekey ` 
-ContainerName $container -FileUri $fileUrl1, $fileUrl2 -Run 'file.ps1' | ` 
Update-AzureVM
```

### <a name="add-the-custom-script-extension-from-the-azure-portal"></a>Agregar la extensión de script personalizada desde el Portal de Azure

Vaya a la VM en el <a href="https://portal.azure.com/ " target="_blank">Portal de Azure </a> y especifique el archivo de scripts que se debe ejecutar para agregar la extensión.

![Especifique el archivo de script][5]

### <a name="uninstall-the-custom-script-extension"></a>Desinstalar la extensión de script personalizada

Puede desinstalar la extensión del script personalizado de la VM con el siguiente comando.

```powershell
get-azureVM -ServiceName KPTRDemo -Name KPTRDemo | ` 
Set-AzureVMCustomScriptExtension -Uninstall | Update-AzureVM
```

### <a name="use-the-custom-script-extension-with-templates"></a>Usar la extensión de scripts personalizada con plantillas

Para más información sobre cómo usar la extensión de script personalizado con plantillas de Azure Resource Manager, consulte [Uso de las extensiones de script personalizadas para máquinas virtuales Windows con plantillas de Azure Resource Manager](virtual-machines-windows-extensions-customscript.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).



<!--HONumber=Dec16_HO3-->


