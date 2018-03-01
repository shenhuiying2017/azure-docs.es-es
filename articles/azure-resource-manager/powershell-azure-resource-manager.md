---
title: "Administración de soluciones de Azure con PowerShell | Microsoft Docs"
description: Uso de Azure PowerShell y Resource Manager para administrar recursos.
services: azure-resource-manager
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: b33b7303-3330-4af8-8329-c80ac7e9bc7f
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: powershell
ms.devlang: na
ms.topic: article
ms.date: 02/16/2018
ms.author: tomfitz
ms.openlocfilehash: 7e2f988fd62753e1ebed702728dee7ede65c72c4
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/21/2018
---
# <a name="manage-resources-with-azure-powershell"></a>Administración de recursos con Azure PowerShell

[!include[Resource Manager governance introduction](../../includes/resource-manager-governance-intro.md)]

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Si decide instalar y usar PowerShell de forma local, consulte el [módulo sobre la instalación de Azure PowerShell](/powershell/azure/install-azurerm-ps). Si PowerShell se ejecuta localmente, también debe ejecutar `Login-AzureRmAccount` para crear una conexión con Azure.

## <a name="understand-scope"></a>Descripción del ámbito

[!include[Resource Manager governance scope](../../includes/resource-manager-governance-scope.md)]

En este artículo, aplica toda la configuración de administración a un grupo de recursos para que pueda quitar fácilmente esos valores cuando haya finalizado.

Vamos a crear el grupo de recursos.

```azurepowershell-interactive
Set-AzureRmContext -Subscription <subscription-name>
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

Actualmente, el grupo de recursos está vacío.

## <a name="role-based-access-control"></a>Control de acceso basado en rol

[!include[Resource Manager governance policy](../../includes/resource-manager-governance-rbac.md)]

### <a name="assign-a-role"></a>Asignar un rol

En este artículo, implementará una máquina virtual y su red virtual relacionada. Para administrar las soluciones de máquina virtual, hay tres roles específicos a los recursos, que normalmente proporcionan el acceso necesario:

* [Colaborador de la máquina virtual](../active-directory/role-based-access-built-in-roles.md#virtual-machine-contributor)
* [Colaborador de la red](../active-directory/role-based-access-built-in-roles.md#network-contributor)
* [Colaborador de la cuenta de almacenamiento](../active-directory/role-based-access-built-in-roles.md#storage-account-contributor)

En lugar de asignar roles a usuarios individuales, a menudo resulta más fácil [crear un grupo de Azure Active Directory](../active-directory/active-directory-groups-create-azure-portal.md) para los usuarios que tienen que realizar acciones similares. A continuación, asigne a ese grupo el rol apropiado. Para simplificar este artículo, cree un grupo de Azure Active Directory sin miembros. Todavía puede asignar a este grupo un rol para un ámbito. 

En el ejemplo siguiente, se crea un grupo y se le asigna el rol Colaborador de la máquina virtual para el grupo de recursos. Para ejecutar el comando `New-AzureAdGroup`, debe usar [Azure Cloud Shell](/azure/cloud-shell/overview) o [descargar el módulo de Azure AD PowerShell](https://www.powershellgallery.com/packages/AzureAD/).

```azurepowershell-interactive
$adgroup = New-AzureADGroup -DisplayName VMDemoContributors `
  -MailNickName vmDemoGroup `
  -MailEnabled $false `
  -SecurityEnabled $true
New-AzureRmRoleAssignment -ObjectId $adgroup.ObjectId `
  -ResourceGroupName myResourceGroup `
  -RoleDefinitionName "Virtual Machine Contributor"
```

Por lo general, repetirá el proceso para **Colaborador de la red** y **Colaborador de la cuenta de almacenamiento** con el objetivo de asegurarse de que los usuarios se asignan para administrar los recursos implementados. En este artículo, puede omitir esos pasos.

## <a name="azure-policies"></a>Directivas de Azure

[!include[Resource Manager governance policy](../../includes/resource-manager-governance-policy.md)]

### <a name="apply-policies"></a>Aplicación de directivas

La suscripción ya tiene varias definiciones de directiva. Para ver las definiciones de directiva disponibles, use:

```azurepowershell-interactive
(Get-AzureRmPolicyDefinition).Properties | Format-Table displayName, policyType
```

Ve las definiciones de directiva existentes. El tipo de directiva es **BuiltIn** o **Custom**. Examine las definiciones para buscar las que describan una condición que quiera asignar. En este artículo, puede asignar directivas que:

* limitan las ubicaciones para todos los recursos.
* limitan las SKU para máquinas virtuales.
* realizan auditorías de máquinas virtuales que no usan discos administrados.

```azurepowershell-interactive
$locations ="eastus", "eastus2"
$skus = "Standard_DS1_v2", "Standard_E2s_v2"

$rg = Get-AzureRmResourceGroup -Name myResourceGroup

$locationDefinition = Get-AzureRmPolicyDefinition | where-object {$_.properties.displayname -eq "Allowed locations"}
$skuDefinition = Get-AzureRmPolicyDefinition | where-object {$_.properties.displayname -eq "Allowed virtual machine SKUs"}
$auditDefinition = Get-AzureRmPolicyDefinition | where-object {$_.properties.displayname -eq "Audit VMs that do not use managed disks"}

New-AzureRMPolicyAssignment -Name "Set permitted locations" `
  -Scope $rg.ResourceId `
  -PolicyDefinition $locationDefinition `
  -listOfAllowedLocations $locations
New-AzureRMPolicyAssignment -Name "Set permitted VM SKUs" `
  -Scope $rg.ResourceId `
  -PolicyDefinition $skuDefinition `
  -listOfAllowedSKUs $skus
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

[!include[Resource Manager governance locks](../../includes/resource-manager-governance-locks.md)]

### <a name="lock-a-resource"></a>Bloqueo de un recurso

Para bloquear la máquina virtual y el grupo de seguridad de red, use:

```azurepowershell-interactive
New-AzureRmResourceLock -LockLevel CanNotDelete `
  -LockName LockVM `
  -ResourceName myVM `
  -ResourceType Microsoft.Compute/virtualMachines `
  -ResourceGroupName myResourceGroup
New-AzureRmResourceLock -LockLevel CanNotDelete `
  -LockName LockNSG `
  -ResourceName myNetworkSecurityGroup `
  -ResourceType Microsoft.Network/networkSecurityGroups `
  -ResourceGroupName myResourceGroup
```

Solo se puede eliminar la máquina virtual si quita el bloqueo específicamente. Ese paso se muestra en [Limpieza de recursos](#clean-up-resources).

## <a name="tag-resources"></a>Etiquetado de recursos

[!include[Resource Manager governance tags](../../includes/resource-manager-governance-tags.md)]

### <a name="tag-resources"></a>Etiquetado de recursos

[!include[Resource Manager governance tags Powershell](../../includes/resource-manager-governance-tags-powershell.md)]

Para aplicar etiquetas a una máquina virtual, use:

```azurepowershell-interactive
$r = Get-AzureRmResource -ResourceName myVM `
  -ResourceGroupName myResourceGroup `
  -ResourceType Microsoft.Compute/virtualMachines
Set-AzureRmResource -Tag @{ Dept="IT"; Environment="Test"; Project="Documentation" } -ResourceId $r.ResourceId -Force
```

### <a name="find-resources-by-tag"></a>Búsqueda de recursos por etiqueta

Para buscar recursos con un nombre y valor de etiqueta, use:

```azurepowershell-interactive
(Find-AzureRmResource -TagName Environment -TagValue Test).Name
```

Puede utilizar los valores devueltos para las tareas de administración, como detener todas las máquinas virtuales con un valor de etiqueta.

```azurepowershell-interactive
Find-AzureRmResource -TagName Environment -TagValue Test | Where-Object {$_.ResourceType -eq "Microsoft.Compute/virtualMachines"} | Stop-AzureRmVM
```

### <a name="view-costs-by-tag-values"></a>Visualización de los costos por valores de etiqueta

Después de aplicar etiquetas a los recursos, puede ver los costos de los recursos con esas etiquetas. El análisis de costos tarda un tiempo en mostrar el uso más reciente, por lo que quizás no pueda ver los costos todavía. Cuando los costos están disponibles, puede ver los costos de los recursos a través de los grupos de recursos en su suscripción. Los usuarios deben tener [acceso de nivel de suscripción a la información de facturación](../billing/billing-manage-access.md) para ver los costos.

Para ver los costos por etiqueta en el portal, seleccione su suscripción y seleccione **Análisis de costos**.

![Análisis de costos](./media/powershell-azure-resource-manager/select-cost-analysis.png)

A continuación, filtre por el valor de etiqueta y seleccione **Aplicar**.

![Ver costo por etiqueta](./media/powershell-azure-resource-manager/view-costs-by-tag.png)

También puede usar las [API de facturación de Azure](../billing/billing-usage-rate-card-overview.md) para ver los costos mediante programación.

## <a name="clean-up-resources"></a>Limpieza de recursos

El grupo de seguridad de red bloqueado no se puede eliminar hasta que se quite el bloqueo. Para quitar el bloqueo, use:

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

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>pasos siguientes
* Para obtener información acerca de la supervisión de las máquinas virtuales, consulte [Supervisar y actualizar una máquina virtual Windows con Azure PowerShell](../virtual-machines/windows/tutorial-monitoring.md).
* Para obtener información acerca del uso de Azure Security Center para implementar prácticas recomendadas de seguridad,consulte [Supervisión de la seguridad de máquinas virtuales mediante Azure Security Center](../virtual-machines/windows/tutorial-azure-security.md).
* Puede mover recursos existentes a un nuevo grupo de recursos. Para consultar ejemplos, vea [Traslado de los recursos a un nuevo grupo de recursos o a una nueva suscripción](resource-group-move-resources.md).
* Para obtener instrucciones sobre cómo las empresas pueden utilizar Resource Manager para administrar eficazmente las suscripciones, vea [Scaffold empresarial de Azure: Gobierno de suscripción prescriptivo](resource-manager-subscription-governance.md).
