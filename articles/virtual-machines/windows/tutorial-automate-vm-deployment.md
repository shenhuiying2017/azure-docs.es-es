---
title: "Personalizar una máquina virtual de Windows en Azure | Microsoft Docs"
description: "Aprenda a utilizar la extensión de script personalizada para automatizar la instalación de aplicaciones en las máquinas virtuales Windows de Azure"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 02/09/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 63858da0a4a47d67ec659e922ab10f9f7bc97938
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/14/2018
---
# <a name="how-to-customize-a-windows-virtual-machine-in-azure"></a>Cómo personalizar una máquina virtual de Windows en Azure
Para configurar las máquinas virtuales de una manera rápida y coherente, normalmente se desea alguna forma de automatización. Un enfoque común para personalizar una máquina virtual de Windows consiste en usar la [Extensión de la secuencia de comandos personalizada para Windows](extensions-customscript.md). En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Usar la extensión de script personalizada para instalar IIS
> * Crear una máquina virtual que use la extensión de script personalizada
> * Ver un sitio IIS en funcionamiento después de aplicar la extensión

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Si decide instalar y usar PowerShell de forma local, para este tutorial se requiere la versión 5.3 del módulo de Azure PowerShell, o cualquier versión posterior. Ejecute `Get-Module -ListAvailable AzureRM` para encontrar la versión. Si necesita actualizarla, consulte [Instalación del módulo de Azure PowerShell](/powershell/azure/install-azurerm-ps). Si PowerShell se ejecuta localmente, también debe ejecutar `Login-AzureRmAccount` para crear una conexión con Azure. 


## <a name="custom-script-extension-overview"></a>Información general de la extensión de script personalizado
La extensión de script personalizado descarga y ejecuta scripts en máquinas virtuales de Azure. Esta extensión es útil para la configuración posterior a la implementación, la instalación de software o cualquier otra tarea de configuración o administración. Los scripts se pueden descargar desde Azure Storage o GitHub, o se pueden proporcionar a Azure Portal en el tiempo de ejecución de la extensión.

La extensión de script personalizado se integra con las plantillas de Azure Resource Manager y también se puede ejecutar mediante la CLI de Azure, PowerShell, Azure Portal o la API de REST de máquina virtual de Azure.

Se puede usar la extensión de script personalizado con máquinas virtuales de Linux y Windows.


## <a name="create-virtual-machine"></a>Create virtual machine
En primer lugar, establezca un nombre de usuario de administrador y una contraseña para la máquina virtual con [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```azurepowershell-interactive
$cred = Get-Credential
```

Ahora puede crear la máquina virtual con [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). En el ejemplo siguiente se crea una máquina virtual denominada *myVM* en la ubicación *EastUS*. Si aún no existen, se crean el grupo de recursos *myResourceGroupAutomate* y los recursos de red de soporte. Para permitir el tráfico de web, el cmdlet también abre el puerto *80*.

```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroupAutomate" `
    -Name "myVM" `
    -Location "East US" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -OpenPorts 80 `
    -Credential $cred
```

Los recursos y la máquina virtual tardan unos minutos en crearse.


## <a name="automate-iis-install"></a>Automatizar la instalación de IIS
Use [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension) para instalar la extensión de script personalizado. La extensión ejecuta `powershell Add-WindowsFeature Web-Server` para instalar el servidor web de IIS y después actualiza la página *Default.htm* para mostrar el nombre de host de la máquina virtual:

```azurepowershell-interactive
Set-AzureRmVMExtension -ResourceGroupName "myResourceGroupAutomate" `
    -ExtensionName "IIS" `
    -VMName "myVM" `
    -Location "EastUS" `
    -Publisher Microsoft.Compute `
    -ExtensionType CustomScriptExtension `
    -TypeHandlerVersion 1.8 `
    -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}'
```


## <a name="test-web-site"></a>Sitio web de prueba
Obtenga la dirección IP pública del equilibrador de carga con [Get-AzureRmPublicIPAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress). En el ejemplo siguiente se obtiene la dirección IP de *myPublicIPAddress* que se ha creado anteriormente:

```azurepowershell-interactive
Get-AzureRmPublicIPAddress `
    -ResourceGroupName "myResourceGroupAutomate" `
    -Name "myPublicIPAddress" | select IpAddress
```

A continuación, puede escribir la dirección IP pública en un explorador web. Se muestra el sitio web, incluido el nombre de host de la máquina virtual a la que el equilibrador de carga distribuye el tráfico como en el ejemplo siguiente:

![Ejecución del sitio web de IIS](./media/tutorial-automate-vm-deployment/running-iis-website.png)


## <a name="next-steps"></a>pasos siguientes

En este tutorial, automatizó la instalación IIS en una máquina virtual. Ha aprendido a:

> [!div class="checklist"]
> * Usar la extensión de script personalizada para instalar IIS
> * Crear una máquina virtual que use la extensión de script personalizada
> * Ver un sitio IIS en funcionamiento después de aplicar la extensión

Avanzar al siguiente tutorial para aprender a crear imágenes de máquina virtual personalizadas.

> [!div class="nextstepaction"]
> [Creación de imágenes personalizadas de máquinas virtuales](./tutorial-custom-images.md)
