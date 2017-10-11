---
title: Bloqueo de recursos de Azure para impedir cambios | Microsoft Docs
description: Impida que los usuarios actualicen o eliminen recursos de Azure esenciales aplicando un bloqueo para todos los usuarios y roles.
services: azure-resource-manager
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 53c57e8f-741c-4026-80e0-f4c02638c98b
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: tomfitz
ms.openlocfilehash: 44c87b00f4fc63dbfd45a07d9a8cddc5eaf1a65c
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="lock-resources-to-prevent-unexpected-changes"></a>Bloqueo de recursos para impedir cambios inesperados 
Como administrador, puede que tenga que bloquear una suscripción, un grupo de recursos o un recurso para impedir que otros usuarios de su organización eliminen o modifiquen accidentalmente recursos esenciales. Puede establecer el bloqueo de nivel en **CanNotDelete** o **ReadOnly**. 

* **CanNotDelete** significa que los usuarios autorizados pueden leer y modificar recursos, pero no eliminarlos. 
* **ReadOnly** significa que los usuarios autorizados solo pueden leer recursos, pero no actualizarlos ni eliminarlos. Aplicar este bloqueo es similar a restringir todos los usuarios autorizados a los permisos concedidos por el rol **Lector**. 

## <a name="how-locks-are-applied"></a>Cómo se aplican los bloqueos

Cuando se aplica un bloqueo en un ámbito primario, todos los recursos heredan el mismo bloqueo. Incluso los recursos que agregue posteriormente heredan el bloqueo del elemento primario. El bloqueo más restrictivo de toda la herencia tiene prioridad.

Al diferencia del control de acceso basado en rol, los bloqueos de administración se usan para aplicar una restricción a todos los usuarios y roles. Para obtener información sobre cómo establecer permisos para usuarios y roles, vea [Control de acceso basado en roles de Azure](../active-directory/role-based-access-control-configure.md).

Los bloqueos de Resource Manager solo se aplican a las operaciones que se producen en el plano de la administración, que consta de las operaciones enviadas a `https://management.azure.com`. Los bloqueos no restringen cómo los recursos realizan sus propias funciones. Los cambios de recursos están restringidos, pero no así las operaciones de recursos. Por ejemplo, un bloqueo de solo lectura de una base de datos SQL evita tener que eliminar o modificar dicha base de datos, pero no podrá crear, actualizar o eliminar los datos de ella. Se permiten las transacciones de datos porque esas operaciones no se envían a `https://management.azure.com`.

Si aplica **ReadOnly** , pueden producirse resultados inesperados, ya que algunas operaciones que parecen ser similares a operaciones de lectura realmente requieren acciones adicionales. Por ejemplo, al colocar un bloqueo **ReadOnly** en una cuenta de almacenamiento, evita que se muestren las claves a todos los usuarios. La operación de visualización claves se administra mediante solicitudes POST debido a que las claves devueltas están disponibles para las operaciones de escritura. Otro ejemplo: al colocar un bloqueo **ReadOnly** en un recurso de servicio del Servicio de aplicaciones, evita que el Explorador de servidores de Visual Studio muestre los archivos del recurso, ya que esa interacción requiere acceso de escritura.

## <a name="who-can-create-or-delete-locks-in-your-organization"></a>Quién puede crear o eliminar bloqueos en su organización
Para crear o eliminar bloqueos de administración, debe tener acceso a las acciones `Microsoft.Authorization/*` o `Microsoft.Authorization/locks/*`. Entre los roles integrados, solamente se conceden esas acciones al **propietario** y al **administrador de acceso de usuarios**.

## <a name="portal"></a>Portal
[!INCLUDE [resource-manager-lock-resources](../../includes/resource-manager-lock-resources.md)]

## <a name="template"></a>Plantilla
El ejemplo siguiente muestra una plantilla que crea un bloqueo en una cuenta de almacenamiento. La cuenta de almacenamiento en la que se va a aplicar el bloqueo se proporciona como un parámetro. El nombre del bloqueo se crea mediante la concatenación del nombre del recurso con **/Microsoft.Authorization/** y el propio nombre del bloqueo (en este caso, **myLock**).

El tipo proporcionado es específico del tipo de recurso. Para el almacenamiento, establezca este tipo en "Microsoft.Storage/storageaccounts/providers/locks".

    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "lockedResource": {
          "type": "string"
        }
      },
      "resources": [
        {
          "name": "[concat(parameters('lockedResource'), '/Microsoft.Authorization/myLock')]",
          "type": "Microsoft.Storage/storageAccounts/providers/locks",
          "apiVersion": "2015-01-01",
          "properties": {
            "level": "CannotDelete"
          }
        }
      ]
    }

## <a name="powershell"></a>PowerShell
Bloquee recursos implementados con Azure PowerShell mediante el comando [New-AzureRmResourceLock](/powershell/module/azurerm.resources/new-azurermresourcelock).

Para bloquear un recurso, proporcione el nombre del recurso, su tipo y el nombre del grupo de recursos.

```powershell
New-AzureRmResourceLock -LockLevel CanNotDelete -LockName LockSite `
  -ResourceName examplesite -ResourceType Microsoft.Web/sites `
  -ResourceGroupName exampleresourcegroup
```

Para bloquear un grupo de recursos, proporcione el nombre del grupo de recursos.

```powershell
New-AzureRmResourceLock -LockName LockGroup -LockLevel CanNotDelete `
  -ResourceGroupName exampleresourcegroup
```

Para obtener información sobre un bloqueo, use [Get-AzureRmResourceLock](/powershell/module/azurerm.resources/get-azurermresourcelock). Para obtener todos los bloqueos en su suscripción, use:

```powershell
Get-AzureRmResourceLock
```

Para obtener todos los bloqueos para un recurso, use:

```powershell
Get-AzureRmResourceLock -ResourceName examplesite -ResourceType Microsoft.Web/sites `
  -ResourceGroupName exampleresourcegroup
```

Para obtener todos los bloqueos para un grupo de recursos, use:

```powershell
Get-AzureRmResourceLock -ResourceGroupName exampleresourcegroup
```

Azure PowerShell ofrece otros comandos para trabajar con bloqueos, como [Set-AzureRmResourceLock](/powershell/module/azurerm.resources/set-azurermresourcelock) para actualizar un bloqueo y [Remove-AzureRmResourceLock](/powershell/module/azurerm.resources/remove-azurermresourcelock) para eliminarlo.

## <a name="azure-cli"></a>CLI de Azure

Bloquee recursos implementados con la CLI de Azure mediante el comando [az lock create](/cli/azure/lock#create).

Para bloquear un recurso, proporcione el nombre del recurso, su tipo y el nombre del grupo de recursos.

```azurecli
az lock create --name LockSite --lock-type CanNotDelete \
  --resource-group exampleresourcegroup --resource-name examplesite \
  --resource-type Microsoft.Web/sites
```

Para bloquear un grupo de recursos, proporcione el nombre del grupo de recursos.

```azurecli
az lock create --name LockGroup --lock-type CanNotDelete \
  --resource-group exampleresourcegroup
```

Para obtener información sobre un bloqueo, use [az lock list](/cli/azure/lock#list). Para obtener todos los bloqueos en su suscripción, use:

```azurecli
az lock list
```

Para obtener todos los bloqueos para un recurso, use:

```azurecli
az lock list --resource-group exampleresourcegroup --resource-name examplesite \
  --namespace Microsoft.Web --resource-type sites --parent ""
```

Para obtener todos los bloqueos para un grupo de recursos, use:

```azurecli
az lock list --resource-group exampleresourcegroup
```

La CLI de Azure proporciona otros comandos para trabajar con bloqueos, como [az lock update](/cli/azure/lock#update) para actualizar un bloqueo y [az lock delete](/cli/azure/lock#delete) para eliminarlo.

## <a name="rest-api"></a>API de REST
Puede bloquear los recursos implementados con la [API de REST para bloqueos de administración](https://docs.microsoft.com/rest/api/resources/managementlocks). La API de REST le permite crear y eliminar bloqueos, y recuperar información acerca de los bloqueos existentes.

Para crear un bloqueo, ejecute:

    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/locks/{lock-name}?api-version={api-version}

El ámbito puede ser una suscripción, un grupo de recursos o un recurso. El nombre del bloqueo es el nombre con el que desee llamar al bloqueo. Como versión de la API, use **2015-01-01**.

En la solicitud, incluya un objeto JSON que especifique las propiedades para el bloqueo.

    {
      "properties": {
        "level": "CanNotDelete",
        "notes": "Optional text notes."
      }
    } 

## <a name="next-steps"></a>Pasos siguientes
* Para obtener más información sobre cómo trabajar con bloqueos de recursos, consulte [Bloqueo de los recursos de Azure](http://blogs.msdn.com/b/cloud_solution_architect/archive/2015/06/18/lock-down-your-azure-resources.aspx)
* Para aprender a organizar de manera lógica los recursos, vea [Uso de etiquetas para organizar sus recursos](resource-group-using-tags.md)
* Para cambiar el grupo de recursos en que reside un recurso, vea [Traslado de los recursos a un nuevo grupo de recursos](resource-group-move-resources.md)
* Puede aplicar restricciones y convenciones a través de su suscripción con directivas personalizadas. Para más información, vea [Uso de directivas para administrar los recursos y controlar el acceso](resource-manager-policy.md).
* Para obtener instrucciones sobre cómo las empresas pueden utilizar Resource Manager para administrar eficazmente las suscripciones, vea [Scaffold empresarial de Azure: Gobierno de suscripción prescriptivo](resource-manager-subscription-governance.md).

