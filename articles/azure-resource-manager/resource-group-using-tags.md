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
ms.date: 07/17/2017
ms.author: tomfitz
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 9345971b8dff683c7d079c29168437f601633eb1
ms.contentlocale: es-es
ms.lasthandoff: 07/21/2017

---
# <a name="use-tags-to-organize-your-azure-resources"></a>Uso de etiquetas para organizar los recursos de Azure
[!INCLUDE [resource-manager-tag-introduction](../../includes/resource-manager-tag-introduction.md)]

> [!NOTE]
> Solo puede aplicar etiquetas a recursos que admiten operaciones del Administrador de recursos. Si creó una máquina virtual, una red virtual o un almacenamiento mediante el modelo de implementación clásica (por ejemplo, a través del Portal clásico), no podrá aplicar una etiqueta a ese recurso. Para admitir el etiquetado, vuelva a implementar estos recursos mediante Resource Manager. Todos los demás recursos admiten el etiquetado.
> 
> 

## <a name="ensure-tag-consistency-with-policies"></a>Garantía de consistencia de etiquetas con directivas

Las directivas de recursos le permiten crear reglas estándar para su organización. Puede crear directivas que se aseguren de que los recursos están etiquetados con los valores adecuados. Para más información, consulte [Aplicación de directivas de recursos para etiquetas](resource-manager-policy-tags.md).

## <a name="powershell"></a>PowerShell
[!INCLUDE [resource-manager-tag-resources-powershell](../../includes/resource-manager-tag-resources-powershell.md)]

## <a name="azure-cli"></a>CLI de Azure

Para ver las etiquetas existentes de un **grupo de recursos**, use:

```azurecli
az group show -n examplegroup --query tags
```

Que devuelve el siguiente formato:

```json
{
  "Dept"        : "IT",
  "Environment" : "Test"
}
```

Para ver las etiquetas existentes de un **recurso con un ID de recurso especificado**, use:

```azurecli
az resource show --id {resource-id} --query tags
```

O bien, para ver las etiquetas existentes de un **recurso con un nombre, tipo y grupo de recursos especificado**, use:

```azurecli
az resource show -n examplevnet -g examplegroup --resource-type "Microsoft.Network/virtualNetworks" --query tags
```

Para obtener grupos de recursos con una etiqueta específica, use `az group list`.

```azurecli
az group list --tag Dept=IT
```

Para obtener todos los recursos con una etiqueta y un valor concretos, use `az resource list`.

```azurecli
az resource list --tag Dept=Finance
```

Cada vez que aplique etiquetas a un recurso o grupo de recursos, sobrescribirá las etiquetas existentes en ese recurso o grupo de recursos. Por lo tanto, tiene que utilizar un enfoque diferente en función de si el recurso o grupo de recursos tiene etiquetas existentes. 

Para agregar etiquetas a un **grupo de recursos sin etiquetas existentes**, use:

```azurecli
az group update -n examplegroup --set tags.Environment=Test tags.Dept=IT
```

Para agregar etiquetas a un **recurso sin etiquetas existentes**, use:

```azurecli
az resource tag --tags Dept=IT Environment=Test -g examplegroup -n examplevnet --resource-type "Microsoft.Network/virtualNetworks"
``` 

Para agregar etiquetas a un recurso que ya tiene etiquetas, recupere las etiquetas existentes, vuelva a formatear el valor y vuelva a aplicar esas etiquetas con etiquetas nuevas: 

```azurecli
jsonrtag=$(az resource show -g examplegroup -n examplevnet --resource-type "Microsoft.Network/virtualNetworks" --query tags)
rt=$(echo $jsonrtag | tr -d '"{},' | sed 's/: /=/g')
az resource tag --tags $rt Project=Redesign -g examplegroup -n examplevnet --resource-type "Microsoft.Network/virtualNetworks"
```

Para aplicar todas las etiquetas de un grupo de recursos a sus recursos y **no conservar ninguna de las etiquetas existentes en los recursos**, use el siguiente script:

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

Para aplicar todas las etiquetas de un grupo de recursos a sus recursos y **conservar las etiquetas existentes en los recursos**, use el siguiente script:

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


## <a name="rest-api"></a>API de REST
Tanto el portal como PowerShell usan la [API de REST del Administrador de recursos](https://docs.microsoft.com/rest/api/resources/) en segundo plano. Si necesita integrar el etiquetado en otro entorno, puede obtener etiquetas con un comando GET en el identificador de recurso y actualizar el conjunto de etiquetas con una llamada PATCH.

## <a name="tags-and-billing"></a>Etiquetas y facturación
Las etiquetas le permiten agrupar los datos de facturación. Por ejemplo, si va a ejecutar varias máquinas virtuales para organizaciones diferentes, use etiquetas para agrupar el uso por centro de costo. También puede usar etiquetas para clasificar los costos por entorno de tiempo de ejecución; por ejemplo, el uso de facturación en máquinas virtuales que se ejecutan en el entorno de producción.


Puede recuperar información sobre las etiquetas a través de las [API de RateCard y de uso de recursos de Azure](../billing/billing-usage-rate-card-overview.md) o mediante el archivo de valores separados por coma (CSV). Puede descargar el archivo de uso en el [Portal de cuentas de Azure](https://account.windowsazure.com/) o el [portal EA](https://ea.azure.com). Para obtener más información sobre el acceso a información de facturación mediante programación, vea [Obtención de información sobre el consumo de recursos de Microsoft Azure](../billing/billing-usage-rate-card-overview.md). Para las operaciones de API de REST, vea [Referencia de API de REST de facturación de Azure](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c).


Al descargar el CSV de uso correspondiente a los servicios que admiten etiquetas con facturación, las etiquetas aparecen en la columna **Etiquetas** . Para más información, consulte [Comprender la factura de Microsoft Azure](../billing/billing-understand-your-bill.md).

![Ver etiquetas en la facturación](./media/resource-group-using-tags/billing_csv.png)

## <a name="next-steps"></a>Pasos siguientes
* Puede aplicar restricciones y convenciones a través de su suscripción con directivas personalizadas. La directiva que define podría requerir que todos los recursos tengan un valor para una etiqueta determinada. Para más información, vea [Uso de directivas para administrar los recursos y controlar el acceso](resource-manager-policy.md).
* Para obtener información sobre cómo usar Azure PowerShell al implementar recursos, consulte [Uso de Azure PowerShell con el Administrador de recursos de Azure](powershell-azure-resource-manager.md).
* Para obtener información sobre cómo usar la interfaz de la línea de comandos (CLI) de Azure al implementar recursos, consulte [Uso de la CLI de Azure para Mac, Linux y Windows con el Administrador de recursos de Azure](xplat-cli-azure-resource-manager.md).
* Para obtener información sobre cómo usar el portal, consulte [Uso del Portal de Azure para administrar los recursos de Azure](resource-group-portal.md)  
* Para obtener instrucciones sobre cómo las empresas pueden utilizar Resource Manager para administrar eficazmente las suscripciones, vea [Scaffold empresarial de Azure: Gobierno de suscripción prescriptivo](resource-manager-subscription-governance.md).


