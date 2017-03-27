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
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/10/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: b084c722b75152b8a2b867f21d546abd04a96f04
ms.lasthandoff: 03/15/2017


---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-cli"></a>Implementación de recursos con plantillas de Resource Manager y la CLI de Azure
> [!div class="op_single_selector"]
> * [PowerShell](resource-group-template-deploy.md)
> * [CLI de Azure](resource-group-template-deploy-cli.md)
> * [Portal](resource-group-template-deploy-portal.md)
> * [API DE REST](resource-group-template-deploy-rest.md)
> 
> 

En este tema se explica cómo usar la [CLI de Azure 2.0](/cli/azure/install-az-cli2) con plantillas de Resource Manager para implementar recursos en Azure.  La plantilla puede ser un archivo local o en un archivo externo que está disponible a través de un identificador URI. Cuando la plantilla se encuentra en una cuenta de almacenamiento, puede restringir el acceso a la plantilla y proporcionar un token de firma de acceso compartido (SAS) durante la implementación.

## <a name="deploy"></a>Implementación

* Para empezar a trabajar en la implementación rápidamente, use los siguientes comandos para implementar una plantilla local con parámetros en línea:

  ```azurecli
  az login
  az account set --subscription {subscription-id}

  az group create --name ExampleGroup --location "Central US"
  az group deployment create \
      --name ExampleDeployment \
      --resource-group ExampleGroup \
      --template-file storage.json \
      --parameters '{"storageNamePrefix":{"value":"contoso"},"storageSKU":{"value":"Standard_GRS"}}'
  ```

  La implementación puede demorar unos minutos en completarse. Cuando termine, verá un mensaje similar al siguiente:

  ```azurecli
  "provisioningState": "Succeeded",
  ```
  
* El comando `az account set` solo se necesita si desea usar una suscripción distinta de la suscripción predeterminada. Para ver todas las suscripciones y sus identificadores, use:

  ```azurecli
  az account list
  ```

* Para implementar una plantilla externa, use el parámetro **template-uri**:
   
   ```azurecli
   az group deployment create \
       --name ExampleDeployment \
       --resource-group ExampleGroup \
       --template-uri "https://raw.githubusercontent.com/exampleuser/MyTemplates/master/storage.json" \
       --parameters '{"storageNamePrefix":{"value":"contoso"},"storageSKU":{"value":"Standard_GRS"}}'
   ```

* Para pasar los valores del parámetro en un archivo, use:

   ```azurecli
   az group deployment create \
       --name ExampleDeployment \
       --resource-group ExampleGroup \
       --template-file storage.json \
       --parameters @storage.parameters.json
   ```

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


[!INCLUDE [resource-manager-deployments](../../includes/resource-manager-deployments.md)]

Para usar el modo completo, utilice el parámetro de modo:

```azurecli
az group deployment create \
    --name ExampleDeployment \
    --mode Complete \
    --resource-group ExampleGroup \
    --template-file storage.json \
    --parameters '{"storageNamePrefix":{"value":"contoso"},"storageSKU":{"value":"Standard_GRS"}}'
```

## <a name="deploy-template-from-storage-with-sas-token"></a>Implementación de plantilla desde el almacenamiento con token de SAS
Puede agregar las plantillas a una cuenta de almacenamiento y establecer vínculos a ellas durante la implementación con un token de SAS.

> [!IMPORTANT]
> Siguiendo estos pasos, el blob que contiene la plantilla solo es accesible para el propietario de la cuenta. Sin embargo, cuando se crea un token de SAS para el blob, el blob es accesible para cualquier persona con ese URI. Si otro usuario intercepta el URI, ese usuario podrá tener acceso a la plantilla. El uso de un token de SAS supone un buen método para limitar el acceso a las plantillas, pero no debe incluir información confidencial como contraseñas directamente en estas.
> 
> 

### <a name="add-private-template-to-storage-account"></a>Adición de plantilla privada a la cuenta de almacenamiento
Los siguientes comandos configuran un contenedor de una cuenta de almacenamiento privado y carga una plantilla:
   
```azurecli
az group create --name "ManageGroup" --location "South Central US"
az storage account create \
    --resource-group ManageGroup \
    --location "South Central US" \
    --sku Standard_LRS \
    --kind Storage \
    --name {your-unique-name}
connection=$(az storage account show-connection-string \
    --resource-group ManageGroup \
    --name {your-unique-name} \
    --query connectionString)
az storage container create \
    --name templates \
    --public-access Off \
    --connection-string $connection
az storage blob upload \
    --container-name templates \
    --file vmlinux.json \
    --name vmlinux.json \
    --connection-string $connection
```

### <a name="provide-sas-token-during-deployment"></a>Provisión del token de SAS durante la implementación
Para implementar una plantilla privada en una cuenta de almacenamiento, genere un token de SAS e inclúyalo en el identificador URI de la plantilla. Establezca el tiempo de expiración con un margen suficiente para completar la implementación.
   
```azurecli
seconds='@'$(( $(date +%s) + 1800 ))
expiretime=$(date +%Y-%m-%dT%H:%MZ --date=$seconds)
connection=$(az storage account show-connection-string \
    --resource-group ManageGroup \
    --name {your-unique-name} \
    --query connectionString)
token=$(az storage blob generate-sas \
    --container-name templates \
    --name vmlinux.json \
    --expiry $expiretime \
    --permissions r \
    --output tsv \
    --connection-string $connection)
url=$(az storage blob url \
    --container-name templates \
    --name vmlinux.json \
    --output tsv \
    --connection-string $connection)
az group deployment create --resource-group ExampleGroup --template-uri $url?$token
```

Para obtener un ejemplo del uso de un token de SAS con plantillas vinculadas, consulte [Uso de plantillas vinculadas con Azure Resource Manager](resource-group-linked-templates.md).

## <a name="debug"></a>Depurar

Para obtener información acerca de las operaciones de una implementación incorrecta, use:
   
```azurecli
az group deployment operation list --resource-group ExampleGroup --name vmlinux --query "[*].[properties.statusMessage]"
```

Para obtener sugerencias para resolver los errores de implementación más comunes, consulte [Solución de errores comunes de implementación de Azure con Azure Resource Manager](resource-manager-common-deployment-errors.md).

## <a name="next-steps"></a>Pasos siguientes
* Para ver un ejemplo de cómo implementar los recursos mediante la biblioteca cliente de .NET, consulte [Deploy Azure resources using .NET libraries and a template](../virtual-machines/virtual-machines-windows-csharp-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)(Implementación de recursos de Azure mediante bibliotecas de .NET y una plantilla).
* Para definir parámetros de plantilla, consulte [Creación de plantillas](resource-group-authoring-templates.md#parameters).
* Para obtener instrucciones sobre cómo implementar la solución en diferentes entornos, vea [Entornos de desarrollo y pruebas en Microsoft Azure](solution-dev-test-environments.md).
* Para más información sobre el uso de una referencia KeyVault para pasar valores seguros, consulte [Paso de valores seguros durante la implementación](resource-manager-keyvault-parameter.md).
* Para obtener instrucciones sobre cómo las empresas pueden utilizar Resource Manager para administrar eficazmente las suscripciones, vea [Scaffold empresarial de Azure: Gobierno de suscripción prescriptivo](resource-manager-subscription-governance.md).
* Para obtener una serie de cuatro partes acerca de cómo automatizar la implementación, vea [Automatización de implementaciones de aplicaciones en Azure Virtual Machines](../virtual-machines/virtual-machines-windows-dotnet-core-1-landing.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Esta serie cubre la arquitectura de la aplicación, el acceso y la seguridad, la disponibilidad y la escala, y la implementación de aplicaciones.


