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
ms.date: 01/03/2018
ms.author: tomfitz
ms.openlocfilehash: e25de0366126ceee988eb253b66d18c9b8b62e1f
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/04/2018
---
# <a name="lock-resources-to-prevent-unexpected-changes"></a>Bloqueo de recursos para impedir cambios inesperados 

Como administrador, puede que tenga que bloquear una suscripción, un grupo de recursos o un recurso para impedir que otros usuarios de su organización eliminen o modifiquen accidentalmente recursos esenciales. Puede establecer el bloqueo de nivel en **CanNotDelete** o **ReadOnly**. 

* **CanNotDelete** significa que los usuarios autorizados pueden leer y modificar recursos, pero no eliminarlos. 
* **ReadOnly** significa que los usuarios autorizados solo pueden leer recursos, pero no actualizarlos ni eliminarlos. Aplicar este bloqueo es similar a restringir todos los usuarios autorizados a los permisos concedidos por el rol **Lector**. 

## <a name="how-locks-are-applied"></a>Cómo se aplican los bloqueos

Cuando se aplica un bloqueo en un ámbito primario, todos los recursos heredan el mismo bloqueo. Incluso los recursos que agregue posteriormente heredan el bloqueo del elemento primario. El bloqueo más restrictivo de toda la herencia tiene prioridad.

Al diferencia del control de acceso basado en rol, los bloqueos de administración se usan para aplicar una restricción a todos los usuarios y roles. Para obtener información sobre cómo establecer permisos para usuarios y roles, vea [Control de acceso basado en roles de Azure](../active-directory/role-based-access-control-configure.md).

Los bloqueos de Resource Manager solo se aplican a las operaciones que se producen en el plano de la administración, que consta de las operaciones enviadas a `https://management.azure.com`. Los bloqueos no restringen cómo los recursos realizan sus propias funciones. Los cambios de recursos están restringidos, pero no así las operaciones de recursos. Por ejemplo, un bloqueo de solo lectura de SQL Database evita tener que eliminar o modificar dicha base de datos, pero no podrá crear, actualizar o eliminar los datos de ella. Se permiten las transacciones de datos porque esas operaciones no se envían a `https://management.azure.com`.

Si aplica **ReadOnly** , pueden producirse resultados inesperados, ya que algunas operaciones que parecen ser similares a operaciones de lectura realmente requieren acciones adicionales. Por ejemplo, al colocar un bloqueo **ReadOnly** en una cuenta de almacenamiento, evita que se muestren las claves a todos los usuarios. La operación de visualización claves se administra mediante solicitudes POST debido a que las claves devueltas están disponibles para las operaciones de escritura. Otro ejemplo: al colocar un bloqueo **ReadOnly** en un recurso de servicio de App Service, evita que el Explorador de servidores de Visual Studio muestre los archivos del recurso, ya que esa interacción requiere acceso de escritura.

## <a name="who-can-create-or-delete-locks-in-your-organization"></a>Quién puede crear o eliminar bloqueos en su organización
Para crear o eliminar bloqueos de administración, debe tener acceso a las acciones `Microsoft.Authorization/*` o `Microsoft.Authorization/locks/*`. Entre los roles integrados, solamente se conceden esas acciones al **propietario** y al **administrador de acceso de usuarios**.

## <a name="portal"></a>Portal
[!INCLUDE [resource-manager-lock-resources](../../includes/resource-manager-lock-resources.md)]

## <a name="template"></a>Plantilla
En el ejemplo siguiente se muestra una plantilla que crea un plan de App Service, un sitio web y un bloqueo en el sitio web. El tipo de recurso del bloqueo es el tipo de recurso del recurso que se bloqueará y **/providers/locks**. El nombre del bloqueo se crea mediante la concatenación del nombre del recurso con **/Microsoft.Authorization/** y el propio nombre del bloqueo.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "hostingPlanName": {
            "type": "string"
        }
    },
    "variables": {
        "siteName": "[concat('ExampleSite', uniqueString(resourceGroup().id))]"
    },
    "resources": [
        {
            "apiVersion": "2016-09-01",
            "type": "Microsoft.Web/serverfarms",
            "name": "[parameters('hostingPlanName')]",
            "location": "[resourceGroup().location]",
            "sku": {
                "tier": "Free",
                "name": "f1",
                "capacity": 0
            },
            "properties": {
                "targetWorkerCount": 1
            }
        },
        {
            "apiVersion": "2016-08-01",
            "name": "[variables('siteName')]",
            "type": "Microsoft.Web/sites",
            "location": "[resourceGroup().location]",
            "dependsOn": [
                "[resourceId('Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
            ],
            "properties": {
                "serverFarmId": "[parameters('hostingPlanName')]"
            }
        },
        {
            "type": "Microsoft.Web/sites/providers/locks",
            "apiVersion": "2016-09-01",
            "name": "[concat(variables('siteName'), '/Microsoft.Authorization/siteLock')]",
            "dependsOn": [
                "[resourceId('Microsoft.Web/sites', variables('siteName'))]"
            ],
            "properties": {
                "level": "CanNotDelete",
                "notes": "Site should not be deleted."
            }
        }
    ]
}
```

Para implementar esta plantilla de ejemplo con PowerShell, use:

```powershell
New-AzureRmResourceGroup -Name sitegroup -Location southcentralus
New-AzureRmResourceGroupDeployment -ResourceGroupName sitegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/lock.json -hostingPlanName plan0103
```

Para implementar esta plantilla de ejemplo con la CLI de Azure, use:

```azurecli
az group create --name sitegroup --location southcentralus
az group deployment create --resource-group sitegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/lock.json --parameters hostingPlanName=plan0103
```

## <a name="powershell"></a>PowerShell
Bloquee recursos implementados con Azure PowerShell mediante el comando [New-AzureRmResourceLock](/powershell/module/azurerm.resources/new-azurermresourcelock).

Para bloquear un recurso, proporcione el nombre del recurso, su tipo y el nombre del grupo de recursos.

```powershell
New-AzureRmResourceLock -LockLevel CanNotDelete -LockName LockSite -ResourceName examplesite -ResourceType Microsoft.Web/sites -ResourceGroupName exampleresourcegroup
```

Para bloquear un grupo de recursos, proporcione el nombre del grupo de recursos.

```powershell
New-AzureRmResourceLock -LockName LockGroup -LockLevel CanNotDelete -ResourceGroupName exampleresourcegroup
```

Para obtener información sobre un bloqueo, use [Get-AzureRmResourceLock](/powershell/module/azurerm.resources/get-azurermresourcelock). Para obtener todos los bloqueos en su suscripción, use:

```powershell
Get-AzureRmResourceLock
```

Para obtener todos los bloqueos para un recurso, use:

```powershell
Get-AzureRmResourceLock -ResourceName examplesite -ResourceType Microsoft.Web/sites -ResourceGroupName exampleresourcegroup
```

Para obtener todos los bloqueos para un grupo de recursos, use:

```powershell
Get-AzureRmResourceLock -ResourceGroupName exampleresourcegroup
```

Para eliminar un bloqueo, use:

```powershell
$lockId = (Get-AzureRmResourceLock -ResourceGroupName exampleresourcegroup -ResourceName examplesite -ResourceType Microsoft.Web/sites).LockId
Remove-AzureRmResourceLock -LockId $lockId
```

## <a name="azure-cli"></a>CLI de Azure

Bloquee recursos implementados con la CLI de Azure mediante el comando [az lock create](/cli/azure/lock#create).

Para bloquear un recurso, proporcione el nombre del recurso, su tipo y el nombre del grupo de recursos.

```azurecli
az lock create --name LockSite --lock-type CanNotDelete --resource-group exampleresourcegroup --resource-name examplesite --resource-type Microsoft.Web/sites
```

Para bloquear un grupo de recursos, proporcione el nombre del grupo de recursos.

```azurecli
az lock create --name LockGroup --lock-type CanNotDelete --resource-group exampleresourcegroup
```

Para obtener información sobre un bloqueo, use [az lock list](/cli/azure/lock#list). Para obtener todos los bloqueos en su suscripción, use:

```azurecli
az lock list
```

Para obtener todos los bloqueos para un recurso, use:

```azurecli
az lock list --resource-group exampleresourcegroup --resource-name examplesite --namespace Microsoft.Web --resource-type sites --parent ""
```

Para obtener todos los bloqueos para un grupo de recursos, use:

```azurecli
az lock list --resource-group exampleresourcegroup
```

Para eliminar un bloqueo, use:

```azurecli
lockid=$(az lock show --name LockSite --resource-group exampleresourcegroup --resource-type Microsoft.Web/sites --resource-name examplesite --output tsv --query id)
az lock delete --ids $lockid
```

## <a name="rest-api"></a>API DE REST
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

## <a name="next-steps"></a>pasos siguientes
* Para aprender a organizar de manera lógica los recursos, vea [Uso de etiquetas para organizar sus recursos](resource-group-using-tags.md)
* Para cambiar el grupo de recursos en que reside un recurso, vea [Traslado de los recursos a un nuevo grupo de recursos](resource-group-move-resources.md)
* Puede aplicar restricciones y convenciones a través de su suscripción con directivas personalizadas. Para obtener más información, consulte [¿Qué es Azure Policy?](../azure-policy/azure-policy-introduction.md)
* Para obtener instrucciones sobre cómo las empresas pueden utilizar Resource Manager para administrar eficazmente las suscripciones, vea [Scaffold empresarial de Azure: Gobierno de suscripción prescriptivo](resource-manager-subscription-governance.md).

