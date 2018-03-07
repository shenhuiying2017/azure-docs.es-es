---
title: "Control de máquinas virtuales de Azure con Azure PowerShell | Microsoft Docs"
description: "Tutorial: Administración de máquinas virtuales de Azure aplicando RBAC, directivas, bloqueos y etiquetas con Azure PowerShell"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: virtual-machines-windows
ms.workload: infrastructure
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 02/21/2018
ms.author: tomfitz
ms.openlocfilehash: 9952660dc177b542d8477dc85c62d76d16e8c54e
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="virtual-machine-governance-with-azure-powershell"></a>Control de máquinas virtuales con Azure PowerShell

[!include[Resource Manager governance introduction](../../../includes/resource-manager-governance-intro.md)]

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Si decide instalar y usar PowerShell de forma local, consulte el [módulo sobre la instalación de Azure PowerShell](/powershell/azure/install-azurerm-ps). Si PowerShell se ejecuta localmente, también debe ejecutar `Login-AzureRmAccount` para crear una conexión con Azure. En las instalaciones locales, también debe [descargar el módulo PowerShell de Azure AD](https://www.powershellgallery.com/packages/AzureAD/) para crear un nuevo grupo de Azure Active Directory.

## <a name="understand-scope"></a>Descripción del ámbito

[!include[Resource Manager governance scope](../../../includes/resource-manager-governance-scope.md)]

En este tutorial, se aplica toda la configuración de administración a un grupo de recursos para poder quitar fácilmente esas opciones al finalizar.

Vamos a crear el grupo de recursos.

```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

Actualmente, el grupo de recursos está vacío.

## <a name="role-based-access-control"></a>Control de acceso basado en rol

Desea asegurarse de que los usuarios de una organización tengan el nivel adecuado de acceso a estos recursos. No desea conceder acceso ilimitado a los usuarios, pero también es necesario garantizar que puedan realizar su trabajo. [El control de acceso basado en rol](../../active-directory/role-based-access-control-what-is.md) le permite administrar los usuarios con permiso para completar acciones específicas en un ámbito.

Para crear y quitar las asignaciones de rol, los usuarios deben tener acceso a `Microsoft.Authorization/roleAssignments/*`. Esta acción se concede mediante los roles Propietario o Administrador de acceso de usuario.

Para administrar las soluciones de máquina virtual, hay tres roles específicos a los recursos, que normalmente proporcionan el acceso necesario:

* [Colaborador de la máquina virtual](../../active-directory/role-based-access-built-in-roles.md#virtual-machine-contributor)
* [Colaborador de la red](../../active-directory/role-based-access-built-in-roles.md#network-contributor)
* [Colaborador de la cuenta de almacenamiento](../../active-directory/role-based-access-built-in-roles.md#storage-account-contributor)

En lugar de asignar roles a usuarios individuales, a menudo resulta más fácil [crear un grupo de Azure Active Directory](../../active-directory/active-directory-groups-create-azure-portal.md) para los usuarios que tienen que realizar acciones similares. A continuación, asigne a ese grupo el rol apropiado. Para simplificar este artículo, cree un grupo de Azure Active Directory sin miembros. Todavía puede asignar a este grupo un rol para un ámbito. 

En el siguiente ejemplo se crea un grupo de Azure Active Directory denominado *VMDemoContributors* con un alias de correo de *vmDemoGroup*. El alias de correo hace las veces de alias del grupo.

```azurepowershell-interactive
$adgroup = New-AzureADGroup -DisplayName VMDemoContributors `
  -MailNickName vmDemoGroup `
  -MailEnabled $false `
  -SecurityEnabled $true
```

Tras volver el símbolo del sistema, el grupo tardará un momento en propagarse por todo Azure Active Directory. Después de esperar durante 20 o 30 segundos, use el comando [New-AzureRmRoleAssignment](/powershell/module/azurerm.resources/new-azurermroleassignment) para asignar el nuevo grupo de Azure Active Directory al rol Colaborador de la máquina virtual para el grupo de recursos.  Si ejecuta el siguiente comando antes de que se propague, recibirá un error en el que se indica que la **entidad de seguridad<guid> no existe en el directorio**. Intente ejecutar el comando de nuevo.

```azurepowershell-interactive
New-AzureRmRoleAssignment -ObjectId $adgroup.ObjectId `
  -ResourceGroupName myResourceGroup `
  -RoleDefinitionName "Virtual Machine Contributor"
```

Por lo general, repetirá el proceso para *Colaborador de la red* y *Colaborador de la cuenta de almacenamiento* con el objetivo de asegurarse de que los usuarios se asignan para administrar los recursos implementados. En este artículo, puede omitir esos pasos.

## <a name="azure-policies"></a>Directivas de Azure

[!include[Resource Manager governance policy](../../../includes/resource-manager-governance-policy.md)]

### <a name="apply-policies"></a>Aplicación de directivas

La suscripción ya tiene varias definiciones de directiva. Para ver las definiciones de directivas disponibles, use el comando [Get-AzureRmPolicyDefinition](/powershell/module/AzureRM.Resources/Get-AzureRmPolicyDefinition):

```azurepowershell-interactive
(Get-AzureRmPolicyDefinition).Properties | Format-Table displayName, policyType
```

Ve las definiciones de directiva existentes. El tipo de directiva es **BuiltIn** o **Custom**. Examine las definiciones para buscar las que describan una condición que quiera asignar. En este artículo, puede asignar directivas que:

* Limite las ubicaciones para todos los recursos.
* Limite las SKU para las máquinas virtuales.
* Realice auditorías de las máquinas virtuales que no usen discos administrados.

En el siguiente ejemplo, se recuperan tres definiciones de directivas en función del nombre para mostrar. Puede usar el comando [New-AzureRMPolicyAssignment](/powershell/module/azurerm.resources/new-azurermpolicyassignment) para asignar esas definiciones al grupo de recursos. En algunas directivas, puede proporcionar los valores de los parámetros para especificar los valores permitidos.

```azurepowershell-interactive
# Values to use for parameters
$locations ="eastus", "eastus2"
$skus = "Standard_DS1_v2", "Standard_E2s_v2"

# Get the resource group
$rg = Get-AzureRmResourceGroup -Name myResourceGroup

# Get policy definitions for allowed locations, allowed SKUs, and auditing VMs that don't use managed disks
$locationDefinition = Get-AzureRmPolicyDefinition | where-object {$_.properties.displayname -eq "Allowed locations"}
$skuDefinition = Get-AzureRmPolicyDefinition | where-object {$_.properties.displayname -eq "Allowed virtual machine SKUs"}
$auditDefinition = Get-AzureRmPolicyDefinition | where-object {$_.properties.displayname -eq "Audit VMs that do not use managed disks"}

# Assign policy for allowed locations
New-AzureRMPolicyAssignment -Name "Set permitted locations" `
  -Scope $rg.ResourceId `
  -PolicyDefinition $locationDefinition `
  -listOfAllowedLocations $locations

# Assign policy for allowed SKUs
New-AzureRMPolicyAssignment -Name "Set permitted VM SKUs" `
  -Scope $rg.ResourceId `
  -PolicyDefinition $skuDefinition `
  -listOfAllowedSKUs $skus

# Assign policy for auditing unmanaged disks
New-AzureRMPolicyAssignment -Name "Audit unmanaged disks" `
  -Scope $rg.ResourceId `
  -PolicyDefinition $auditDefinition
```

## <a name="deploy-the-virtual-machine"></a>Implementación de la máquina virtual

Ha asignado roles y directivas, por lo que está listo para implementar la solución. El tamaño predeterminado es Standard_DS1_v2, que es una de las SKU permitidas. Cuando se realiza este paso, se le solicitará las credenciales. Los valores que especifique se configuran como el nombre de usuario y la contraseña de la máquina virtual.

```azurepowershell-interactive
New-AzureRmVm -ResourceGroupName "myResourceGroup" `
     -Name "myVM" `
     -Location "East US" `
     -VirtualNetworkName "myVnet" `
     -SubnetName "mySubnet" `
     -SecurityGroupName "myNetworkSecurityGroup" `
     -PublicIpAddressName "myPublicIpAddress" `
     -OpenPorts 80,3389
```

Una vez finalizada la implementación, puede aplicar más opciones de configuración de administración a la solución.

## <a name="lock-resources"></a>Bloqueo de recursos

Los [bloqueos de recursos](../../azure-resource-manager/resource-group-lock-resources.md) impiden que los usuarios de una organización eliminen o modifiquen de forma accidental recursos críticos. A diferencia del control de acceso basado en rol, los bloqueos de recursos aplican una restricción a todos los usuarios y roles. Puede establecer el bloqueo de nivel en *CanNotDelete* o *ReadOnly*.

Para bloquear la máquina virtual y el grupo de seguridad de red, use el comando [New-AzureRmResourceLock](/powershell/module/azurerm.resources/new-azurermresourcelock):

```azurepowershell-interactive
# Add CanNotDelete lock to the VM
New-AzureRmResourceLock -LockLevel CanNotDelete `
  -LockName LockVM `
  -ResourceName myVM `
  -ResourceType Microsoft.Compute/virtualMachines `
  -ResourceGroupName myResourceGroup

# Add CanNotDelete lock to the network security group
New-AzureRmResourceLock -LockLevel CanNotDelete `
  -LockName LockNSG `
  -ResourceName myNetworkSecurityGroup `
  -ResourceType Microsoft.Network/networkSecurityGroups `
  -ResourceGroupName myResourceGroup
```

Para probar los bloqueos, intente ejecutar el siguiente comando:

```azurepowershell-interactive 
Remove-AzureRmResourceGroup -Name myResourceGroup
```

Verá un error en el que se indica que la operación de eliminación no se puede realizar debido a un bloqueo. Solo se puede eliminar el grupo de recursos si quita los bloqueos específicamente. Ese paso se muestra en [Limpieza de recursos](#clean-up-resources).

## <a name="tag-resources"></a>Etiquetado de recursos

Se aplican [etiquetas](../../azure-resource-manager/resource-group-using-tags.md) a los recursos de Azure para organizarlos de forma lógica por categorías. Cada etiqueta consta de un nombre y un valor. Por ejemplo, puede aplicar el nombre "Environment" y el valor "Production" a todos los recursos en producción.

[!include[Resource Manager governance tags Powershell](../../../includes/resource-manager-governance-tags-powershell.md)]

Para aplicar etiquetas a una máquina virtual, use el comando [Set-AzureRmResource](/powershell/module/azurerm.resources/set-azurermresource):

```azurepowershell-interactive
# Get the virtual machine
$r = Get-AzureRmResource -ResourceName myVM `
  -ResourceGroupName myResourceGroup `
  -ResourceType Microsoft.Compute/virtualMachines

# Apply tags to the virtual machine
Set-AzureRmResource -Tag @{ Dept="IT"; Environment="Test"; Project="Documentation" } -ResourceId $r.ResourceId -Force
```

### <a name="find-resources-by-tag"></a>Búsqueda de recursos por etiqueta

Para buscar recursos con un nombre y un valor de etiqueta, use el comando [Find-AzureRmResource](/powershell/module/azurerm.resources/find-azurermresource):

```azurepowershell-interactive
(Find-AzureRmResource -TagName Environment -TagValue Test).Name
```

Puede utilizar los valores devueltos para las tareas de administración, como detener todas las máquinas virtuales con un valor de etiqueta.

```azurepowershell-interactive
Find-AzureRmResource -TagName Environment -TagValue Test | Where-Object {$_.ResourceType -eq "Microsoft.Compute/virtualMachines"} | Stop-AzureRmVM
```

### <a name="view-costs-by-tag-values"></a>Visualización de los costos por valores de etiqueta

[!include[Resource Manager governance tags billing](../../../includes/resource-manager-governance-tags-billing.md)]

## <a name="clean-up-resources"></a>Limpieza de recursos

El grupo de seguridad de red bloqueado no se puede eliminar hasta que se quite el bloqueo. Para quitar el bloqueo, utilice el comando [Remove-AzureRmResourceLock](/powershell/module/azurerm.resources/remove-azurermresourcelock):

```azurepowershell-interactive
Remove-AzureRmResourceLock -LockName LockVM `
  -ResourceName myVM `
  -ResourceType Microsoft.Compute/virtualMachines `
  -ResourceGroupName myResourceGroup
Remove-AzureRmResourceLock -LockName LockNSG `
  -ResourceName myNetworkSecurityGroup `
  -ResourceType Microsoft.Network/networkSecurityGroups `
  -ResourceGroupName myResourceGroup
```

Cuando ya no se necesiten, puede usar el comando [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) para quitar el grupo de recursos, la máquina virtual y todos los recursos relacionados.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>pasos siguientes

En este tutorial, ha creado una imagen de máquina virtual personalizada. Ha aprendido a:

> [!div class="checklist"]
> * Asignar un rol a los usuarios
> * Aplicar directivas que hagan cumplir los estándares
> * Proteger los recursos críticos con bloqueos
> * Etiquetar recursos para la facturación y la administración

Avance al siguiente tutorial para obtener información sobre máquinas virtuales de alta disponibilidad.

> [!div class="nextstepaction"]
> [Supervisión de máquinas virtuales](tutorial-monitoring.md)

