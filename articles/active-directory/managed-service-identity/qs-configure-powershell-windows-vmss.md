---
title: Configuración de MSI en un VMSS de Azure con PowerShell
description: Instrucciones paso a paso para configurar identidades asignadas por el sistema y por el usuario en un VMSS de Azure, mediante PowerShell.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/27/2017
ms.author: daveba
ms.openlocfilehash: 97c5e2dde3faeaad13317597bef4f70455d22102
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/10/2018
---
# <a name="configure-a-vmss-managed-service-identity-msi-using-powershell"></a>Configuración de Managed Service Identity (MSI) en un VMSS con PowerShell

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Managed Service Identity proporciona a los servicios de Azure una identidad administrada automáticamente en Azure Active Directory. Puede usar esta identidad para autenticar a cualquier servicio que admita la autenticación de Azure AD, sin necesidad de tener credenciales en el código. 

En este artículo, aprenderá a realizar las siguientes operaciones de Managed Service Identity en un conjunto de escalado de máquinas virtuales (VMSS) de Azure mediante PowerShell:
- Habilitar y deshabilitar la identidad asignada por el sistema en un VMSS de Azure
- Agregar y quitar una identidad asignada por el usuario en un VMSS de Azure

## <a name="prerequisites"></a>requisitos previos

- Si no está familiarizado con Managed Service Identity, consulte la [sección de introducción](overview.md). **No olvide revisar la [diferencia entre una identidad asignada por el sistema y una asignada por el usuario](overview.md#how-does-it-work)**.
- Si aún no tiene una cuenta de Azure, [regístrese para una cuenta gratuita](https://azure.microsoft.com/free/) antes de continuar.
- Instale [la versión más reciente de Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM) si aún no lo ha hecho. 

## <a name="system-assigned-managed-identity"></a>Identidad administrada asignada por el sistema

En esta sección, aprenderá a habilitar y quitar una identidad asignada por el sistema con Azure PowerShell.

### <a name="enable-system-assigned-identity-during-the-creation-of-an-azure-vmss"></a>Habilitación de una identidad asignada por el sistema durante la creación de un VMSS de Azure

Para crear un VMSS con la identidad asignada por el sistema habilitada:

1. Consulte el *ejemplo 1* en el artículo de referencia del cmdlet [New-AzureRmVmssConfig](/powershell/module/azurerm.compute/new-azurermvmssconfig) para crear un VMSS con una identidad asignada por el sistema.  Agregue el parámetro `-IdentityType SystemAssigned` al cmdlet `New-AzureRmVmssConfig`:

    ```powershell
    $VMSS = New-AzureRmVmssConfig -Location $Loc -SkuCapacity 2 -SkuName "Standard_A0" -UpgradePolicyMode "Automatic" -NetworkInterfaceConfiguration $NetCfg -IdentityType SystemAssigned`
    ```

2. (Opcional) Agregue la extensión VMSS de MSI mediante los parámetros `-Name` y `-Type` en el cmdlet [Add-AzureRmVmssExtension](/powershell/module/azurerm.compute/add-azurermvmssextension). Puede pasar "ManagedIdentityExtensionForWindows" o "ManagedIdentityExtensionForLinux", en función del tipo de máquina virtual y asígnele el nombre mediante el parámetro `-Name`. El parámetro `-Settings` especifica el puerto utilizado por el punto de conexión del token de OAuth para la adquisición de tokens:

    > [!NOTE]
    > Este paso es opcional, ya que puede utilizar el punto de conexión de Azure Instance Metadata Service (IMDS) para recuperar tokens.

   ```powershell
   $setting = @{ "port" = 50342 }
   $vmss = Get-AzureRmVmss
   Add-AzureRmVmssExtension -VirtualMachineScaleSet $vmss -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Setting $settings 
   ```

## <a name="enable-system-assigned-identity-on-an-existing-azure-vmss"></a>Habilitación de la identidad asignada por el sistema en un VMSS de Azure existente

Si tiene que habilitar la identidad asignada por el sistema en un VMSS de Azure existente:

1. Inicie sesión en Azure con `Login-AzureRmAccount`. Use una cuenta asociada a la suscripción de Azure que contenga la máquina virtual. Asegúrese también de que la cuenta pertenece a un rol que le conceda permisos de escritura en la máquina virtual, como "Colaborador de la máquina virtual":

   ```powershell
   Login-AzureRmAccount
   ```

2. En primer lugar, recupere las propiedades del VMSS mediante el cmdlet [`Get-AzureRmVmss`](/powershell/module/azurerm.compute/get-azurermvmss). A continuación, para habilitar la identidad asignada por el sistema, use el parámetro `-IdentityType` en el cmdlet [Update-AzureRmVM](/powershell/module/azurerm.compute/update-azurermvm):

   ```powershell
   $vm = Get-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVM
   Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name -myVM -IdentityType "SystemAssigned"
   ```

3. Agregue la extensión VMSS de MSI mediante los parámetros `-Name` y `-Type` en el cmdlet [Add-AzureRmVmssExtension](/powershell/module/azurerm.compute/add-azurermvmssextension). Puede pasar "ManagedIdentityExtensionForWindows" o "ManagedIdentityExtensionForLinux", en función del tipo de máquina virtual y asígnele el nombre mediante el parámetro `-Name`. El parámetro `-Settings` especifica el puerto utilizado por el punto de conexión del token de OAuth para la adquisición de tokens:

   ```powershell
   $setting = @{ "port" = 50342 }
   $vmss = Get-AzureRmVmss
   Add-AzureRmVmssExtension -VirtualMachineScaleSet $vmss -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Setting $settings 
   ```

### <a name="disable-the-system-assigned-identity-from-an-azure-vmss"></a>Deshabilitación de la identidad asignada por el sistema de un VMSS de Azure

> [!NOTE]
> Actualmente, no se admite deshabilitar Managed Service Identity de un conjunto de escalado de máquinas virtuales. Mientras tanto, puede cambiar entre el uso de identidades asignadas por el sistema y por el usuario.

Si tiene un conjunto de escalado de máquinas virtuales que ya no necesita la identidad asignada por el sistema, pero aún necesita identidades asignadas por el usuario, use el siguiente cmdlet:

1. Inicie sesión en Azure con `Login-AzureRmAccount`. Use una cuenta asociada a la suscripción de Azure que contenga la máquina virtual. Asegúrese también de que la cuenta pertenece a un rol que le conceda permisos de escritura en la máquina virtual, como "Colaborador de la máquina virtual":

2. Ejecute el siguiente cmdlet:

    ```powershell
    Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType "UserAssigned"
    ```

## <a name="user-assigned-identity"></a>Identidad asignada por el usuario

En esta sección, aprenderá a habilitar y quitar una identidad asignada por el usuario de un VMSS con Azure PowerShell.

### <a name="assign-a-user-assigned-identity-during-creation-of-an-azure-vmss"></a>Asignación de una identidad asignada por el usuario durante la creación de un VMSS de Azure

Actualmente, no se admite crear un nuevo VMSS con una identidad asignada por el usuario mediante PowerShell. Consulte la sección siguiente sobre cómo agregar una identidad asignada por el usuario a un VMSS existente. Compruebe si hay actualizaciones.

### <a name="assign-a-user-identity-to-an-existing-azure-vmss"></a>Asignación de una identidad asignada por el usuario a un VMSS de Azure existente

Para asignar una identidad asignada por el usuario a un VMSS de Azure existente:

1. Inicie sesión en Azure con `Connect-AzureRmAccount`. Use una cuenta asociada a la suscripción de Azure que contenga la máquina virtual. Asegúrese también de que la cuenta pertenece a un rol que le conceda permisos de escritura en la máquina virtual, como "Colaborador de la máquina virtual":

   ```powershell
   Connect-AzureRmAccount
   ```

2. En primer lugar, recupere las propiedades de la máquina virtual mediante el cmdlet `Get-AzureRmVM`. A continuación, para asignar una identidad asignada por el usuario al VMSS de Azure, use los parámetros `-IdentityType` y `-IdentityID` en el cmdlet [Update-AzureRmVM](/powershell/module/azurerm.compute/update-azurermvm). Reemplace `<VM NAME>`, `<SUBSCRIPTION ID>`, `<RESROURCE GROUP>`, `<USER ASSIGNED ID1>`, `USER ASSIGNED ID2` con sus propios valores.

   > [!IMPORTANT]
   > La creación de identidades asignadas por el usuario solo admite caracteres alfanuméricos y guiones (0-9, a-z, A-z, -). Además, el nombre debe limitarse a una longitud de 24 caracteres para que la asignación a VM/VMSS funcione correctamente. Compruebe si hay actualizaciones. Para más información, consulte [Preguntas más frecuentes y problemas conocidos](known-issues.md).


   ```powershell
   $vmss = Get-AzureRmVmss -ResourceGroupName <RESOURCE GROUP> -Name <VMSS NAME>
   Update-AzureRmVmss -ResourceGroupName <RESOURCE GROUP> -VM $vmss -IdentityType UserAssigned -IdentityID "<USER ASSIGNED ID1>","<USER ASSIGNED ID2>"
   ```

### <a name="remove-a-user-assigned-identity-from-an-azure-vmss"></a>Eliminación de una identidad asignada por el usuario de un VMSS de Azure

> [!NOTE]
> Actualmente, no se admite la eliminación de todas las identidades asignadas por el usuario de un conjunto de escalado de máquinas virtuales, a menos que tenga una identidad asignada por el sistema. Compruebe si hay actualizaciones.

Si el VMSS tiene varias identidades asignadas por el usuario, puede quitarlas todas, menos la última, utilizando los comandos siguientes. Asegúrese de reemplazar los valores de los parámetros `<RESOURCE GROUP>` y `<VMSS NAME>` con sus propios valores. `<MSI NAME>` es la propiedad de nombre de la identidad asignada por el usuario, que debe permanecer en el VMSS. Esta información puede encontrarse en la sección de identidad del VMSS mediante `az vmss show`:

```powershell
$vmss = Get-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss
$vmss.Identity.IdentityIds = "<MSI NAME>"
Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss -VirtualMachineScaleSet $vmss
```

Si el VMSS tiene identidades asignadas tanto por el sistema como por el usuario, puede quitar todas las identidades asignadas por el usuario para usar solo las asignadas por el sistema. Use el comando siguiente:

```powershell
$vmss = Get-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss
$vmss.Identity.IdentityIds = $null
Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss -VirtualMachine $vmss -IdentityType "SystemAssigned"
```

## <a name="related-content"></a>Contenido relacionado

- [Información general de Managed Service Identity](overview.md)
- Para ver guías de inicio rápido completas acerca de la creación de máquinas virtuales de Azure, consulte:
  
  - [Creación de una máquina virtual Windows con PowerShell](../../virtual-machines/windows/quick-create-powershell.md) 
  - [Creación de una máquina virtual Linux con PowerShell](../../virtual-machines/linux/quick-create-powershell.md) 

















