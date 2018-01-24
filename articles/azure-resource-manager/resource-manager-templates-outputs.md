---
title: Salidas de plantilla de Azure Resource Manager | Microsoft Docs
description: "Se describe cómo definir salidas para plantillas de Azure Resource Manager mediante la sintaxis declarativa de JSON."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/14/2017
ms.author: tomfitz
ms.openlocfilehash: 485a3eb5c5d04d1540482245d088c48645704465
ms.sourcegitcommit: 3fca41d1c978d4b9165666bb2a9a1fe2a13aabb6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/15/2017
---
# <a name="outputs-section-in-azure-resource-manager-templates"></a>Sección de salidas en plantillas de Azure Resource Manager
En la sección de salidas, especifique valores que se devuelven de la implementación. Por ejemplo, podría devolver el URI para acceder a un recurso implementado.

## <a name="define-and-use-output-values"></a>Definición y uso de valores de salida

En el ejemplo siguiente se muestra cómo devolver el identificador de recurso para una dirección IP pública:

```json
"outputs": {
  "resourceID": {
    "type": "string",
    "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
  }
}
```

Después de la implementación, puede recuperar el valor con el script. Para PowerShell, use:

```powershell
(Get-AzureRmResourceGroupDeployment -ResourceGroupName <resource-group-name> -Name <deployment-name>).Outputs.resourceID.value
```

Para la CLI de Azure, utilice:

```azurecli-interactive
az group deployment show -g <resource-group-name> -n <deployment-name> --query properties.outputs.resourceID.value
```

Puede recuperar el valor de salida de una plantilla vinculada mediante la función [reference](resource-group-template-functions-resource.md#reference). Para obtener un valor de salida de una plantilla vinculada, recupere el valor de propiedad con sintaxis de esta manera: `"[reference('<name-of-deployment>').outputs.<property-name>.value]"`.

Por ejemplo, puede establecer la dirección IP en un equilibrador de carga mediante la recuperación de un valor de una plantilla vinculada.

```json
"publicIPAddress": {
    "id": "[reference('linkedTemplate').outputs.resourceID.value]"
}
```

## <a name="available-properties"></a>Propiedades disponibles

En el ejemplo siguiente se muestra la estructura de una definición de salida:

```json
"outputs": {
    "<outputName>" : {
        "type" : "<type-of-output-value>",
        "value": "<output-value-expression>"
    }
}
```

| Nombre del elemento | Obligatorio | DESCRIPCIÓN |
|:--- |:--- |:--- |
| outputName |Sí |Nombre del valor de salida. Debe ser un identificador válido de JavaScript. |
| Tipo |Sí |Tipo del valor de salida. Los valores de salida admiten los mismos tipos que los parámetros de entrada de plantilla. |
| value |Sí |Expresión de lenguaje de plantilla que se evaluará y devolverá como valor de salida. |

## <a name="recommendations"></a>Recomendaciones

Si usa una plantilla para crear direcciones IP públicas incluya una sección de salidas que devuelve detalles de la dirección IP y el nombre de dominio completo (FQDN). Puede usar valores de salida para recuperar fácilmente los detalles sobre las direcciones IP públicas y los nombres FQDN después de la implementación.

```json
"outputs": {
    "fqdn": {
        "value": "[reference(parameters('publicIPAddresses_name')).dnsSettings.fqdn]",
        "type": "string"
    },
    "ipaddress": {
        "value": "[reference(parameters('publicIPAddresses_name')).ipAddress]",
        "type": "string"
    }
}
```

## <a name="example-templates"></a>Plantillas de ejemplo


|Plantilla  |DESCRIPCIÓN  |
|---------|---------|
|[Variables de copia](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copyvariables.json) | Crea variables complejas y genera esos valores. No implementa ningún recurso. |
|[Dirección IP pública](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip.json) | Crea una dirección IP pública y genera el identificador de recurso. |
|[Equilibrador de carga](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json) | Crea vínculos a la plantilla anterior. Usa el identificador de recurso de la salida al crear el equilibrador de carga. |


## <a name="next-steps"></a>pasos siguientes
* Para ver plantillas completas de muchos tipos diferentes de soluciones, consulte [Plantillas de inicio rápido de Azure](https://azure.microsoft.com/documentation/templates/).
* Para obtener información detallada sobre las funciones que se pueden usar dentro de una plantilla, consulte [Funciones de plantilla de Azure Resource Manager](resource-group-template-functions.md).
* Para combinar varias plantillas en la implementación, consulte [Uso de plantillas vinculadas con Azure Resource Manager](resource-group-linked-templates.md).
* Puede que necesite usar los recursos que existen dentro de un grupo de recursos diferente. Este escenario es habitual al trabajar con cuentas de almacenamiento o redes virtuales que se comparten entre varios grupos de recursos. Para obtener más información, vea la [función resourceId](resource-group-template-functions-resource.md#resourceid).