---
title: "Implementación de recursos con la plantilla y la CLI de Azure | Microsoft Docs"
description: Use Azure Resource Manager y la CLI de Azure para implementar recursos en Azure. Los recursos se definen en una plantilla de Resource Manager.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 493b7932-8d1e-4499-912c-26098282ec95
ms.service: azure-resource-manager
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/19/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: abdbb9a43f6f01303844677d900d11d984150df0
ms.openlocfilehash: c889a609b8d49474216fe1dcfba69a881edb4133
ms.lasthandoff: 04/21/2017


---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-cli"></a>Implementación de recursos con plantillas de Resource Manager y la CLI de Azure

En este tema se explica cómo usar la [CLI de Azure 2.0](/cli/azure/install-az-cli2) con plantillas de Resource Manager para implementar recursos en Azure.  La plantilla puede ser un archivo local o en un archivo externo que está disponible a través de un identificador URI.

Puede obtener la plantilla (storage.json) que se usa en estos ejemplos en el artículo [Creación de la primera plantilla de Azure Resource Manager](resource-manager-create-first-template.md#final-template). Para usar la plantilla con estos ejemplos, cree un archivo JSON y agregue el contenido copiado.

## <a name="deploy-local-template"></a>Implementar una plantilla local

Para empezar a trabajar en la implementación rápidamente, use los siguientes comandos para implementar una plantilla local con parámetros en línea:

```azurecli
az login

az group create --name ExampleGroup --location "Central US"
az group deployment create \
    --name ExampleDeployment \
    --resource-group ExampleGroup \
    --template-file storage.json \
    --parameters "{\"storageNamePrefix\":{\"value\":\"contoso\"},\"storageSKU\":{\"value\":\"Standard_GRS\"}}"
```

La implementación puede demorar unos minutos en completarse. Cuando termine, verá un mensaje que incluye el resultado:

```azurecli
"provisioningState": "Succeeded",
```

En el ejemplo anterior se ha creado el grupo de recursos en la suscripción predeterminada. Para usar otra suscripción, agregue el comando [az account set](/cli/azure/account#set) después de iniciar sesión.

## <a name="deploy-external-template"></a>Implementar una plantilla externa

Para implementar una plantilla externa, use el parámetro **template-uri**. La plantilla puede estar en cualquier URI accesible públicamente (como un archivo de la cuenta de almacenamiento).
   
```azurecli
az group deployment create \
    --name ExampleDeployment \
    --resource-group ExampleGroup \
    --template-uri "https://raw.githubusercontent.com/exampleuser/MyTemplates/master/storage.json" \
    --parameters "{\"storageNamePrefix\":{\"value\":\"contoso\"},\"storageSKU\":{\"value\":\"Standard_GRS\"}}"
```

Puede proteger la plantilla si exige un token de firma de acceso compartido (SAS) para el acceso. Para más información sobre la implementación de una plantilla que requiere un token de SAS, vea [Implementación de una plantilla privada con el token de SAS](resource-manager-cli-sas-token.md).

## <a name="parameter-files"></a>Archivos de parámetros

En los ejemplos anteriores se ha mostrado cómo pasar parámetros como valores en línea. Puede especificar valores de parámetro en un archivo y pasarlo durante la implementación. 

El archivo de parámetros debe estar en el siguiente formato:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
     "storageNamePrefix": {
         "value": "contoso"
     },
     "storageSKU": {
         "value": "Standard_GRS"
     }
  }
}
```

Para pasar un archivo de parámetros local, use:

```azurecli
az group deployment create \
    --name ExampleDeployment \
    --resource-group ExampleGroup \
    --template-file storage.json \
    --parameters @storage.parameters.json
```

## <a name="test-a-deployment"></a>Probar una implementación

Para probar los valores de parámetro y de plantilla sin implementar realmente ningún recurso, use [az group deployment validate](/cli/azure/group/deployment#validate). Tiene las mismas opciones para el uso de archivos locales o remotos.

```azurecli
az group deployment validate \
    --resource-group ExampleGroup \
    --template-file storage.json \
    --parameters @storage.parameters.json
```

## <a name="debug"></a>Depurar

Para obtener información acerca de las operaciones de una implementación incorrecta, use:
   
```azurecli
az group deployment operation list --resource-group ExampleGroup --name vmlinux --query "[*].[properties.statusMessage]"
```

Para obtener sugerencias para resolver los errores de implementación más comunes, consulte [Solución de errores comunes de implementación de Azure con Azure Resource Manager](resource-manager-common-deployment-errors.md).


## <a name="export-resource-manager-template"></a>Exportación de plantillas de Resource Manager
En el caso de un grupo de recursos existente (que se implementa mediante CLI de Azure o uno de los otros métodos, como el portal), puede ver la plantilla de Resource Manager para el grupo de recursos. Exportar la plantilla ofrece dos ventajas:

1. Puede automatizar fácilmente las futuras implementaciones de la solución porque toda la infraestructura está definida en la plantilla.
2. Para familiarizarse con la sintaxis de la plantilla, consulte la notación de objetos JavaScript (JSON) que representa la solución.

Para ver la plantilla de un grupo de recursos, ejecute el comando [az group export](/cli/azure/group#export).

```azurecli
az group export --name ExampleGroup
```

Para más información, consulte [Exportación de plantillas de Azure Resource Manager desde recursos existentes](resource-manager-export-template.md).


[!INCLUDE [resource-manager-deployments](../../includes/resource-manager-deployments.md)]

Para usar el modo completo, emplee el parámetro `mode`:

```azurecli
az group deployment create \
    --name ExampleDeployment \
    --mode Complete \
    --resource-group ExampleGroup \
    --template-file storage.json \
    --parameters "{\"storageNamePrefix\":{\"value\":\"contoso\"},\"storageSKU\":{\"value\":\"Standard_GRS\"}}"
```


## <a name="next-steps"></a>Pasos siguientes
* Para obtener un script de ejemplo completo que implementa una plantilla, vea [Resource Manager template deployment script](resource-manager-samples-cli-deploy.md) (Script de implementación de plantilla de Resource Manager).
* Para definir parámetros de plantilla, consulte [Creación de plantillas](resource-group-authoring-templates.md#parameters).
* Para obtener sugerencias para resolver los errores de implementación más comunes, consulte [Solución de errores comunes de implementación de Azure con Azure Resource Manager](resource-manager-common-deployment-errors.md).
* Para más información sobre la implementación de una plantilla que requiere un token de SAS, vea [Implementación de una plantilla privada con el token de SAS](resource-manager-cli-sas-token.md).
* Para obtener instrucciones sobre cómo las empresas pueden utilizar Resource Manager para administrar eficazmente las suscripciones, vea [Scaffold empresarial de Azure: Gobierno de suscripción prescriptivo](resource-manager-subscription-governance.md).

