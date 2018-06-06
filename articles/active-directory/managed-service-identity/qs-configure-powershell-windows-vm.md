---
title: Configuración de MSI en una máquina virtual de Azure con PowerShell
description: Instrucciones paso a paso para configurar una identidad de servicio administrada (MSI) en una máquina virtual de Azure, mediante PowerShell.
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
ms.openlocfilehash: 6981c0f917fb7175f444ceca8c55c0df186774db
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/05/2018
ms.locfileid: "33932326"
---
# <a name="configure-a-vm-managed-service-identity-msi-using-powershell"></a>Configuración de una identidad de servicio administrada (MSI) en una máquina virtual con PowerShell

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Managed Service Identity proporciona a los servicios de Azure una identidad administrada automáticamente en Azure Active Directory. Puede usar esta identidad para autenticar a cualquier servicio que admita la autenticación de Azure AD, sin necesidad de tener credenciales en el código. 

<a name="in-this-article-you-learn-how-to-perform-the-following-managed-service-identity-operations-on-an-azure-vm-using-powershell"></a>En este artículo, aprenderá a realizar las siguientes operaciones de Managed Service Identity en una máquina virtual de Azure mediante PowerShell:
- 

## <a name="prerequisites"></a>requisitos previos

- Si no está familiarizado con Managed Service Identity, consulte la [sección de introducción](overview.md). **No olvide revisar la [diferencia entre una identidad asignada por el sistema y una asignada por el usuario](overview.md#how-does-it-work)**.
- Si aún no tiene una cuenta de Azure, [regístrese para una cuenta gratuita](https://azure.microsoft.com/free/) antes de continuar.
- Instale [la versión más reciente de Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM) si aún no lo ha hecho.

## <a name="system-assigned-identity"></a>Identidad asignada por el sistema

En esta sección, aprenderá a habilitar y deshabilitar la identidad asignada por el sistema con Azure PowerShell.

### <a name="enable-system-assigned-identity-during-creation-of-an-azure-vm"></a>Habilitación de una identidad asignada por el sistema durante la creación de una máquina virtual de Azure

Para crear una máquina virtual de Azure con la identidad asignada por el sistema habilitada:

1. Consulte una de las siguientes guías de inicio rápido sobre máquinas virtuales de Azure y finalice solo las secciones necesarias ("Iniciar sesión en Azure", "Creación de un grupo de recursos", "Creación de un grupo de red", "Creación de la máquina virtual").
    
    Cuando llegue a la sección "Creación de la máquina virtual", realice una pequeña modificación en la sintaxis del cmdlet [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvm). Asegúrese de agregar un parámetro `-AssignIdentity "SystemAssigned"` para aprovisionar la máquina virtual con la identidad asignada por el sistema habilitada, por ejemplo:
      
    ```powershell
    $vmConfig = New-AzureRmVMConfig -VMName myVM -AssignIdentity "SystemAssigned" ...
    ```

   - [Creación de una máquina virtual Windows con PowerShell](../../virtual-machines/windows/quick-create-powershell.md)
   - [Creación de una máquina virtual Linux con PowerShell](../../virtual-machines/linux/quick-create-powershell.md)

2. (Opcional) Agregue la extensión MSI de máquina virtual con el parámetro `-Type` en el cmdlet [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension). Puede pasar "ManagedIdentityExtensionForWindows" o "ManagedIdentityExtensionForLinux", en función del tipo de máquina virtual y asígnele el nombre mediante el parámetro `-Name`. El parámetro `-Settings` especifica el puerto utilizado por el punto de conexión del token de OAuth para la adquisición de tokens:

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```
    > [!NOTE]
    > Este paso es opcional, ya que puede usar el punto de conexión de identidad de Azure Instance Metadata Service (IMDS) para recuperar tokens.

### <a name="enable-system-assigned-identity-on-an-existing-azure-vm"></a>Habilitación de la identidad asignada por el sistema en una máquina virtual de Azure existente

Si tiene que habilitar la identidad asignada por el sistema en una máquina virtual existente:

1. Inicie sesión en Azure con `Login-AzureRmAccount`. Use una cuenta asociada a la suscripción de Azure que contenga la máquina virtual. Asegúrese también de que la cuenta pertenece a un rol que le conceda permisos de escritura en la máquina virtual, como "Colaborador de la máquina virtual":

   ```powershell
   Login-AzureRmAccount
   ```

2. En primer lugar, recupere las propiedades de la máquina virtual mediante el cmdlet `Get-AzureRmVM`. A continuación, para habilitar una identidad asignada por el sistema, use el modificador `-AssignIdentity` en el cmdlet [Update-AzureRmVM](/powershell/module/azurerm.compute/update-azurermvm):

   ```powershell
   $vm = Get-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM
   Update-AzureRmVM -ResourceGroupName myResourceGroup -VM $vm -AssignIdentity "SystemAssigned"
   ```

3. (Opcional) Agregue la extensión MSI de máquina virtual con el parámetro `-Type` en el cmdlet [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension). Puede pasar "ManagedIdentityExtensionForWindows" o "ManagedIdentityExtensionForLinux", en función del tipo de máquina virtual y asígnele el nombre mediante el parámetro `-Name`. El parámetro `-Settings` especifica el puerto utilizado por el punto de conexión del token OAuth para la adquisición de tokens. Asegúrese de especificar el parámetro `-Location` correcto y que coincida la ubicación de la máquina virtual existente:

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```
    > [!NOTE]
    > Este paso es opcional, ya que puede usar el punto de conexión de identidad de Azure Instance Metadata Service (IMDS) para recuperar tokens.

## <a name="disable-the-system-assigned-identity-from-an-azure-vm"></a>Deshabilitación de la identidad asignada por el sistema de una máquina virtual de Azure

> [!NOTE]
>  Actualmente no se puede deshabilitar Managed Service Identity de una máquina virtual. Mientras tanto, puede cambiar entre el uso de identidades asignadas por el sistema y por el usuario.

Si tiene una máquina virtual que ya no necesita la identidad asignada por el sistema, pero aún necesita identidades asignadas por el usuario, use el siguiente cmdlet:

1. Inicie sesión en Azure con `Login-AzureRmAccount`. Use una cuenta asociada a la suscripción de Azure que contenga la máquina virtual. Asegúrese también de que la cuenta pertenece a un rol que le conceda permisos de escritura en la máquina virtual, como "Colaborador de la máquina virtual":

   ```powershell
   Login-AzureRmAccount
   ```

2. Ejecute el siguiente cmdlet: 
    ```powershell       
    Update-AzureRmVm -ResourceGroupName myResourceGroup -Name myVm -IdentityType "UserAssigned"
    ```
Para quitar la extensión de máquina virtual de MSI, use el modificador -Name con el cmdlet [Remove-AzureRmVMExtension](/powershell/module/azurerm.compute/remove-azurermvmextension) y especifique el mismo nombre que ha usado al agregar la extensión:

   ```powershell
   Remove-AzureRmVMExtension -ResourceGroupName myResourceGroup -Name "ManagedIdentityExtensionForWindows" -VMName myVM
   ```

## <a name="user-assigned-identity"></a>Identidad asignada por el usuario

En esta sección, aprenderá a agregar y quitar una identidad asignada por el usuario de una máquina virtual con Azure PowerShell.

### <a name="assign-a-user-assigned-identity-to-a-vm-during-creation"></a>Asignación de una identidad asignada por el usuario a una máquina virtual durante la creación

Para asignar una identidad asignada por el usuario a una máquina virtual de Azure al crear la máquina virtual:

1. Consulte una de las siguientes guías de inicio rápido sobre máquinas virtuales de Azure y finalice solo las secciones necesarias ("Iniciar sesión en Azure", "Creación de un grupo de recursos", "Creación de un grupo de red", "Creación de la máquina virtual"). 
  
    Cuando llegue a la sección "Creación de la máquina virtual", realice una pequeña modificación en la sintaxis del cmdlet [`New-AzureRmVMConfig`](/powershell/module/azurerm.compute/new-azurermvm). Agregue los parámetros `-IdentityType UserAssigned` y `-IdentityID ` para aprovisionar la máquina virtual con una identidad asignada por el usuario.  Reemplace `<VM NAME>`, `<SUBSCRIPTION ID>`, `<RESROURCE GROUP>` y `<MSI NAME>` con sus propios valores.  Por ejemplo: 
    
    ```powershell 
    $vmConfig = New-AzureRmVMConfig -VMName <VM NAME> -IdentityType UserAssigned -IdentityID "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESROURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>..."
    ```
    
    - [Creación de una máquina virtual Windows con PowerShell](../../virtual-machines/windows/quick-create-powershell.md)
    - [Creación de una máquina virtual Linux con PowerShell](../../virtual-machines/linux/quick-create-powershell.md)

2. (Opcional) Agregue la extensión MSI de máquina virtual con el parámetro `-Type` en el cmdlet [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension). Puede pasar "ManagedIdentityExtensionForWindows" o "ManagedIdentityExtensionForLinux", en función del tipo de máquina virtual y asígnele el nombre mediante el parámetro `-Name`. El parámetro `-Settings` especifica el puerto utilizado por el punto de conexión del token OAuth para la adquisición de tokens. Asegúrese de especificar el parámetro `-Location` correcto y que coincida la ubicación de la máquina virtual existente:
      > [!NOTE]
    > Este paso es opcional, ya que puede usar el punto de conexión de identidad de Azure Instance Metadata Service (IMDS) para recuperar tokens.

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```

### <a name="assign-a-user-identity-to-an-existing-azure-vm"></a>Asignación de una identidad asignada por el usuario a una máquina virtual de Azure existente

Para asignar una identidad asignada por el usuario a una máquina virtual de Azure existente:

1. Inicie sesión en Azure con `Connect-AzureRmAccount`. Use una cuenta asociada a la suscripción de Azure que contenga la máquina virtual. Asegúrese también de que la cuenta pertenece a un rol que le conceda permisos de escritura en la máquina virtual, como "Colaborador de la máquina virtual":

   ```powershell
   Connect-AzureRmAccount
   ```

2. Cree una identidad asignada por el usuario mediante el cmdlet [New-AzureRmUserAssignedIdentity](/powershell/module/azurerm.managedserviceidentity/new-azurermuserassignedidentity).  Anote `Id` en la salida, porque lo necesitará en el paso siguiente.

    > [!IMPORTANT]
    > La creación de identidades asignadas por el usuario solo admite caracteres alfanuméricos y guiones (0-9, a-z, A-z, -). Además, el nombre debe limitarse a una longitud de 24 caracteres para que la asignación a VM/VMSS funcione correctamente. Compruebe si hay actualizaciones. Para obtener más información, consulte [Preguntas más frecuentes y problemas conocidos](known-issues.md).


  ```powershell
  New-AzureRmUserAssignedIdentity -ResourceGroupName <RESOURCEGROUP> -Name <USER ASSIGNED IDENTITY NAME>
  ```
3. Recupere las propiedades de la máquina virtual mediante el cmdlet `Get-AzureRmVM`. A continuación, para asignar una identidad asignada por el usuario a la máquina virtual de Azure, use los modificadores `-IdentityType` y `-IdentityID` en el cmdlet [Update-AzureRmVM](/powershell/module/azurerm.compute/update-azurermvm).  El valor del parámetro `-IdentityId` es el `Id` que ha anotado en el paso anterior.  Reemplace `<VM NAME>`, `<SUBSCRIPTION ID>`, `<RESROURCE GROUP>` y `<USER ASSIGNED IDENTITY NAME>` con sus propios valores.

   ```powershell
   $vm = Get-AzureRmVM -ResourceGroupName <RESOURCE GROUP> -Name <VM NAME>
   Update-AzureRmVM -ResourceGroupName <RESOURCE GROUP> -VM $vm -IdentityType UserAssigned -IdentityID "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESROURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>"
   ```

4. Agregue la extensión MSI de máquina virtual con el parámetro `-Type` en el cmdlet [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension). Puede pasar "ManagedIdentityExtensionForWindows" o "ManagedIdentityExtensionForLinux", en función del tipo de máquina virtual y asígnele el nombre mediante el parámetro `-Name`. El parámetro `-Settings` especifica el puerto utilizado por el punto de conexión del token OAuth para la adquisición de tokens. Especifique el parámetro `-Location` correcto, que coincide con la ubicación de la máquina virtual existente.

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-vm"></a>Eliminación de una identidad asignada por el usuario de una máquina virtual de Azure

> [!NOTE]
>  Actualmente no se pueden eliminar todas las identidades asignadas por el usuario de una máquina virtual, a menos que tenga una identidad asignada por el sistema. Compruebe si hay actualizaciones.

Si la máquina virtual tiene varias identidades asignadas por el usuario, puede quitar todas, menos la última, mediante los comandos siguientes. Asegúrese de reemplazar los valores de los parámetros `<RESOURCE GROUP>` y `<VM NAME>` con sus propios valores. `<MSI NAME>` es la propiedad de nombre de la identidad asignada por el usuario, que debe permanecer en la máquina virtual. Esta información puede encontrarse en la sección de identidad de la máquina virtual mediante `az vm show`:

```powershell
$vm = Get-AzureRmVm -ResourceGroupName myResourceGroup -Name myVm
$vm.Identity.IdentityIds = "<MSI NAME>"
Update-AzureRmVm -ResourceGroupName myResourceGroup -Name myVm -VirtualMachine $vm
```

Si la máquina virtual tiene identidades asignadas tanto por el sistema como por el usuario, puede quitar todas las identidades asignadas por el usuario si cambia para usar solo las asignadas por el sistema. Use el comando siguiente:

```powershell 
$vm = Get-AzureRmVm -ResourceGroupName myResourceGroup -Name myVm
$vm.Identity.IdentityIds = $null
Update-AzureRmVm -ResourceGroupName myResourceGroup -Name myVm -VirtualMachine $vm -IdentityType "SystemAssigned"
```

## <a name="related-content"></a>Contenido relacionado

- [Información general de Managed Service Identity](overview.md)
- Para ver guías de inicio rápido completas acerca de la creación de máquinas virtuales de Azure, consulte:
  
  - [Creación de una máquina virtual Windows con PowerShell](../../virtual-machines/windows/quick-create-powershell.md) 
  - [Creación de una máquina virtual Linux con PowerShell](../../virtual-machines/linux/quick-create-powershell.md) 
