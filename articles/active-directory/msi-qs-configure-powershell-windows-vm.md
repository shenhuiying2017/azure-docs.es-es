---
title: "Configuración de MSI en una máquina virtual de Azure con PowerShell"
description: "Instrucciones paso a paso para configurar una identidad de servicio administrada (MSI) en una máquina virtual de Azure, mediante PowerShell."
services: active-directory
documentationcenter: 
author: bryanla
manager: mtillman
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/27/2017
ms.author: bryanla
ms.openlocfilehash: 7d34e506eca609001ef6d10ce288c2531921f3d0
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="configure-a-vm-managed-service-identity-msi-using-powershell"></a>Configuración de una identidad de servicio administrada (MSI) en una máquina virtual con PowerShell

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

Managed Service Identity proporciona a los servicios de Azure una identidad administrada automáticamente en Azure Active Directory. Puede usar esta identidad para autenticar a cualquier servicio que admita la autenticación de Azure AD, sin necesidad de tener credenciales en el código. 

En este artículo, aprenderá tanto a habilitar MSI en una máquina virtual de Azure como a quitarlo de ella mediante PowerShell.

## <a name="prerequisites"></a>Requisitos previos

[!INCLUDE [msi-qs-configure-prereqs](../../includes/active-directory-msi-qs-configure-prereqs.md)]

Además, instale [la última versión de Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM) (versión 4.3.1 o posterior) si aún no lo ha hecho.

## <a name="enable-msi-during-creation-of-an-azure-vm"></a>Habilitación de MSI durante la creación de una máquina virtual de Azure

Para crear una máquina virtual con MSI habilitado:

1. Consulte una de las siguientes guías de inicio rápido sobre máquinas virtuales de Azure y finalice solo las secciones necesarias ("Iniciar sesión en Azure", "Creación de un grupo de recursos", "Creación de un grupo de red", "Creación de la máquina virtual"). 

   > [!IMPORTANT] 
   > Cuando llegue a la sección "Creación de la máquina virtual", realice una pequeña modificación en la sintaxis del cmdlet [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvm). Asegúrese de agregar un parámetro `-IdentityType "SystemAssigned"` para aprovisionar la máquina virtual con un MSI, por ejemplo:
   >  
   > `$vmConfig = New-AzureRmVMConfig -VMName myVM -IdentityType "SystemAssigned" ...`

   - [Creación de una máquina virtual Windows con PowerShell](../virtual-machines/windows/quick-create-powershell.md)
   - [Creación de una máquina virtual Linux con PowerShell](../virtual-machines/linux/quick-create-powershell.md)



2. Agregue la extensión MSI de máquina virtual con el parámetro `-Type` en el cmdlet [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension). Puede pasar "ManagedIdentityExtensionForWindows" o "ManagedIdentityExtensionForLinux", en función del tipo de máquina virtual y asígnele el nombre mediante el parámetro `-Name`. El parámetro `-Settings` especifica el puerto utilizado por el punto de conexión del token de OAuth para la adquisición de tokens:

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```

## <a name="enable-msi-on-an-existing-azure-vm"></a>Habilitación de MSI en una máquina virtual de Azure existente

Si tiene que habilitar MSI en una máquina virtual existente:

1. Inicie sesión en Azure con `Login-AzureRmAccount`. Use una cuenta asociada a la suscripción de Azure que contenga la máquina virtual. Asegúrese también de que la cuenta pertenece a un rol que le conceda permisos de escritura en la máquina virtual, como "Colaborador de la máquina virtual":

   ```powershell
   Login-AzureRmAccount
   ```

2. En primer lugar, recupere las propiedades de la máquina virtual mediante el cmdlet `Get-AzureRmVM`. A continuación, para habilitar MSI, use el parámetro `-IdentityType` en el cmdlet [Update-AzureRmVM](/powershell/module/azurerm.compute/update-azurermvm):

   ```powershell
   $vm = Get-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM
   Update-AzureRmVM -ResourceGroupName myResourceGroup -VM $vm -IdentityType "SystemAssigned"
   ```

3. Agregue la extensión MSI de máquina virtual con el parámetro `-Type` en el cmdlet [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension). Puede pasar "ManagedIdentityExtensionForWindows" o "ManagedIdentityExtensionForLinux", en función del tipo de máquina virtual y asígnele el nombre mediante el parámetro `-Name`. El parámetro `-Settings` especifica el puerto utilizado por el punto de conexión del token OAuth para la adquisición de tokens. Asegúrese de especificar el parámetro `-Location` correcto y que coincida la ubicación de la máquina virtual existente:

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```

## <a name="remove-msi-from-an-azure-vm"></a>Eliminación de MSI de una máquina virtual de Azure

Si tiene una máquina virtual que ya no necesita una identidad MSI, puede usar el cmdlet `RemoveAzureRmVMExtension` para quitarla de la máquina virtual:

1. Inicie sesión en Azure con `Login-AzureRmAccount`. Use una cuenta asociada a la suscripción de Azure que contenga la máquina virtual. Asegúrese también de que la cuenta pertenece a un rol que le conceda permisos de escritura en la máquina virtual, como "Colaborador de la máquina virtual":

   ```powershell
   Login-AzureRmAccount
   ```

2. Use el modificador `-Name` con el cmdlet [Remove-AzureRmVMExtension](/powershell/module/azurerm.compute/remove-azurermvmextension) y especifique el mismo nombre que se utilizó cuando agregó la extensión:

   ```powershell
   Remove-AzureRmVMExtension -ResourceGroupName myResourceGroup -Name "ManagedIdentityExtensionForWindows" -VMName myVM
   ```

## <a name="related-content"></a>Contenido relacionado

- [Información general de Managed Service Identity](msi-overview.md)
- Para ver guías de inicio rápido completas acerca de la creación de máquinas virtuales de Azure, consulte:
  
  - [Creación de una máquina virtual Windows con PowerShell](../virtual-machines/windows/quick-create-powershell.md) 
  - [Creación de una máquina virtual Linux con PowerShell](../virtual-machines/linux/quick-create-powershell.md) 

Use la siguiente sección de comentarios para proporcionar sus opiniones y ayudarnos a afinar y remodelar el contenido.
















