---
title: "Etiquetado de recursos de Azure para organización lógica | Microsoft Docs"
description: "Muestra cómo aplicar etiquetas para organizar los recursos de Azure para la facturación y administración."
services: azure-resource-manager
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 003a78e5-2ff8-4685-93b4-e94d6fb8ed5b
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: AzurePortal
ms.devlang: na
ms.topic: article
ms.date: 01/19/2018
ms.author: tomfitz
ms.openlocfilehash: 7ad53c7cfc49958abbe6200a892ba4e0c24c434c
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/09/2018
---
# <a name="use-tags-to-organize-your-azure-resources"></a>Uso de etiquetas para organizar los recursos de Azure

[!INCLUDE [resource-manager-tag-introduction](../../includes/resource-manager-tag-introduction.md)]

## <a name="powershell"></a>PowerShell

Los ejemplos de este artículo requieren la versión 3.0 o posterior de Azure PowerShell. Si no tiene la versión 3.0 o posterior, [actualice su versión](/powershell/azureps-cmdlets-docs/) utilizando la Galería de PowerShell o el Instalador de plataforma web.

Para ver las etiquetas existentes de un *grupo de recursos*, use:

```powershell
(Get-AzureRmResourceGroup -Name examplegroup).Tags
```

Ese script devuelve el siguiente formato:

```powershell
Name                           Value
----                           -----
Dept                           IT
Environment                    Test
```

Para ver las etiquetas existentes de un *recurso que tiene un identificador de recurso especificado*, use:

```powershell
(Get-AzureRmResource -ResourceId {resource-id}).Tags
```

O bien, para ver las etiquetas existentes para un *recurso que tiene un nombre y un grupo de recursos especificados*, use:

```powershell
(Get-AzureRmResource -ResourceName examplevnet -ResourceGroupName examplegroup).Tags
```

Para obtener *grupos de recursos que tengan una etiqueta específica*, use:

```powershell
(Find-AzureRmResourceGroup -Tag @{ Dept="Finance" }).Name
```

Para obtener *recursos que tengan una etiqueta específica*, use:

```powershell
(Find-AzureRmResource -TagName Dept -TagValue Finance).Name
```

Cada vez que aplique etiquetas a un recurso o grupo de recursos, sobrescribirá las etiquetas existentes en ese recurso o grupo de recursos. Por lo tanto, tiene que utilizar un enfoque diferente en función de si el recurso o grupo de recursos tienen etiquetas existentes.

Para agregar etiquetas a un *grupo de recursos sin etiquetas existentes*, use:

```powershell
Set-AzureRmResourceGroup -Name examplegroup -Tag @{ Dept="IT"; Environment="Test" }
```

Para agregar etiquetas a un *grupo de recursos que ya tiene etiquetas*, recupere las etiquetas existentes, agregue la nueva y vuelva a aplicar todas:

```powershell
$tags = (Get-AzureRmResourceGroup -Name examplegroup).Tags
$tags += @{Status="Approved"}
Set-AzureRmResourceGroup -Tag $tags -Name examplegroup
```

Para agregar etiquetas a un *recurso sin etiquetas*, use:

```powershell
$r = Get-AzureRmResource -ResourceName examplevnet -ResourceGroupName examplegroup
Set-AzureRmResource -Tag @{ Dept="IT"; Environment="Test" } -ResourceId $r.ResourceId -Force
```

Para agregar etiquetas a un *recurso que ya tiene etiquetas*, use:

```powershell
$r = Get-AzureRmResource -ResourceName examplevnet -ResourceGroupName examplegroup
$r.tags += @{Status="Approved"}
Set-AzureRmResource -Tag $r.Tags -ResourceId $r.ResourceId -Force
```

Para aplicar todas las etiquetas de un grupo de recursos a sus recursos y *no conservar ninguna de las etiquetas existentes en los recursos*, use el siguiente script:

```powershell
$groups = Get-AzureRmResourceGroup
foreach ($g in $groups)
{
    Find-AzureRmResource -ResourceGroupNameEquals $g.ResourceGroupName | ForEach-Object {Set-AzureRmResource -ResourceId $_.ResourceId -Tag $g.Tags -Force }
}
```

Para aplicar todas las etiquetas de un grupo de recursos a sus recursos y *conservar las etiquetas existentes en los recursos que no son duplicados*, use el siguiente script:

```powershell
$group = Get-AzureRmResourceGroup "examplegroup"
if ($group.Tags -ne $null) {
    $resources = $group | Find-AzureRmResource
    foreach ($r in $resources)
    {
        $resourcetags = (Get-AzureRmResource -ResourceId $r.ResourceId).Tags
        foreach ($key in $group.Tags.Keys)
        {
            if (($resourcetags) -AND ($resourcetags.ContainsKey($key))) { $resourcetags.Remove($key) }
        }
        $resourcetags += $group.Tags
        Set-AzureRmResource -Tag $resourcetags -ResourceId $r.ResourceId -Force
    }
}
```

Para quitar todas las etiquetas, pase una tabla hash vacía:

```powershell
Set-AzureRmResourceGroup -Tag @{} -Name examplegroup
```


## <a name="azure-cli"></a>Azure CLI

Para ver las etiquetas existentes de un *grupo de recursos*, use:

```azurecli
az group show -n examplegroup --query tags
```

Ese script devuelve el siguiente formato:

```json
{
  "Dept"        : "IT",
  "Environment" : "Test"
}
```

O bien, para ver las etiquetas existentes para un *recurso que tiene un nombre, un tipo y un grupo de recursos especificados*, use:

```azurecli
az resource show -n examplevnet -g examplegroup --resource-type "Microsoft.Network/virtualNetworks" --query tags
```

Al recorrer en iteración una colección de recursos, puede mostrar el identificador de recurso por recurso. Más adelante en este artículo se muestra un ejemplo completo. Para ver las etiquetas existentes de un *recurso que tiene un identificador de recurso especificado*, use:

```azurecli
az resource show --id <resource-id> --query tags
```

Para obtener grupos de recursos que tengan una etiqueta específica, use `az group list`:

```azurecli
az group list --tag Dept=IT
```

Para obtener todos los recursos que tengan una etiqueta y un valor particulares, use `az resource list`:

```azurecli
az resource list --tag Dept=Finance
```

Cada vez que aplique etiquetas a un recurso o grupo de recursos, sobrescribirá las etiquetas existentes en ese recurso o grupo de recursos. Por lo tanto, tiene que utilizar un enfoque diferente en función de si el recurso o grupo de recursos tienen etiquetas existentes.

Para agregar etiquetas a un *grupo de recursos sin etiquetas existentes*, use:

```azurecli
az group update -n examplegroup --set tags.Environment=Test tags.Dept=IT
```

Para agregar etiquetas a un *recurso sin etiquetas*, use:

```azurecli
az resource tag --tags Dept=IT Environment=Test -g examplegroup -n examplevnet --resource-type "Microsoft.Network/virtualNetworks"
```

Para agregar etiquetas a un recurso que ya tiene etiquetas, recupere las etiquetas existentes, vuelva a formatear el valor y vuelva a aplicar las etiquetas existentes y nuevas: 

```azurecli
jsonrtag=$(az resource show -g examplegroup -n examplevnet --resource-type "Microsoft.Network/virtualNetworks" --query tags)
rt=$(echo $jsonrtag | tr -d '"{},' | sed 's/: /=/g')
az resource tag --tags $rt Project=Redesign -g examplegroup -n examplevnet --resource-type "Microsoft.Network/virtualNetworks"
```

Para aplicar todas las etiquetas de un grupo de recursos a sus recursos y *no conservar ninguna de las etiquetas existentes en los recursos*, use el siguiente script:

```azurecli
groups=$(az group list --query [].name --output tsv)
for rg in $groups
do
  jsontag=$(az group show -n $rg --query tags)
  t=$(echo $jsontag | tr -d '"{},' | sed 's/: /=/g')
  r=$(az resource list -g $rg --query [].id --output tsv)
  for resid in $r
  do
    az resource tag --tags $t --id $resid
  done
done
```

Para aplicar todas las etiquetas de un grupo de recursos a sus recursos y *conservar las etiquetas existentes en los recursos*, use el siguiente script:

```azurecli
groups=$(az group list --query [].name --output tsv)
for rg in $groups
do
  jsontag=$(az group show -n $rg --query tags)
  t=$(echo $jsontag | tr -d '"{},' | sed 's/: /=/g')
  r=$(az resource list -g $rg --query [].id --output tsv)
  for resid in $r
  do
    jsonrtag=$(az resource show --id $resid --query tags)
    rt=$(echo $jsonrtag | tr -d '"{},' | sed 's/: /=/g')
    az resource tag --tags $t$rt --id $resid
  done
done
```

## <a name="templates"></a>Plantillas

[!INCLUDE [resource-manager-tags-in-templates](../../includes/resource-manager-tags-in-templates.md)]

## <a name="portal"></a>Portal

[!INCLUDE [resource-manager-tag-resource](../../includes/resource-manager-tag-resources.md)]

## <a name="rest-api"></a>API DE REST

Tanto Azure Portal como PowerShell usan la [API de REST de Resource Manager](https://docs.microsoft.com/rest/api/resources/) en segundo plano. Si necesita integrar el etiquetado en otro entorno, puede obtener etiquetas con un comando **GET** en el identificador de recurso y actualizar el conjunto de etiquetas con una llamada **PATCH**.

## <a name="tags-and-billing"></a>Etiquetas y facturación

Puede usar etiquetas a fin de agrupar los datos de facturación. Por ejemplo, si va a ejecutar varias máquinas virtuales para organizaciones diferentes, use etiquetas para agrupar el uso por centro de costo. También puede usar etiquetas para clasificar los costos por entorno de tiempo de ejecución; por ejemplo, el uso de facturación en máquinas virtuales que se ejecutan en el entorno de producción.

Puede recuperar información sobre las etiquetas a través de las [API de RateCard y de uso de recursos de Azure](../billing/billing-usage-rate-card-overview.md) o mediante el archivo de valores separados por coma (CSV). Puede descargar el archivo de uso en el [Portal de cuentas de Azure](https://account.windowsazure.com/) o el [portal EA](https://ea.azure.com). Para obtener más información sobre el acceso a información de facturación mediante programación, vea [Obtención de información sobre el consumo de recursos de Microsoft Azure](../billing/billing-usage-rate-card-overview.md). Para las operaciones de API de REST, vea [Referencia de API de REST de facturación de Azure](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c).

Al descargar el CSV de uso correspondiente a los servicios que admiten etiquetas con facturación, las etiquetas aparecen en la columna **Etiquetas** . Para más información, consulte [Comprender la factura de Microsoft Azure](../billing/billing-understand-your-bill.md).

![Ver etiquetas en la facturación](./media/resource-group-using-tags/billing_csv.png)

## <a name="next-steps"></a>pasos siguientes

* Puede aplicar restricciones y convenciones a través de su suscripción con directivas personalizadas. La directiva que define podría requerir que todos los recursos tengan un valor para una etiqueta determinada. Para obtener más información, consulte [¿Qué es Azure Policy?](../azure-policy/azure-policy-introduction.md)
* Para obtener información sobre cómo usar Azure PowerShell al implementar recursos, consulte [Uso de Azure PowerShell con Azure Resource Manager](powershell-azure-resource-manager.md).
* Para obtener información sobre cómo usar la interfaz de la línea de comandos (CLI) de Azure al implementar recursos, consulte [Uso de la CLI de Azure para Mac, Linux y Windows con Azure Resource Manager](xplat-cli-azure-resource-manager.md).
* Para obtener información sobre cómo usar el portal, consulte [Uso de Azure Portal para administrar los recursos de Azure](resource-group-portal.md).  
* Para obtener instrucciones sobre cómo las empresas pueden utilizar Resource Manager para administrar eficazmente las suscripciones, vea [Scaffold empresarial de Azure: Gobierno de suscripción prescriptivo](resource-manager-subscription-governance.md).
