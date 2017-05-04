---
title: "Implementación de recursos en Azure | Microsoft Docs"
description: Use Azure PowerShell o la CLI de Azure para implementar recursos en Azure. Los recursos se definen en una plantilla de Resource Manager.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/16/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 3b3162c335be43c9bf2a3d1c14b86cdd9b5d46b9
ms.lasthandoff: 04/27/2017


---
# <a name="deploy-resources-to-azure"></a>Implementación de recursos en Azure

En este tema se muestra cómo implementar recursos en la suscripción de Azure. Puede usar Azure PowerShell o la CLI de Azure para implementar una plantilla de Resource Manager que define la infraestructura de la solución.

Para ver una introducción de los conceptos de Resource Manager, consulte [Información general sobre Azure Resource Manager](resource-group-overview.md).

## <a name="steps-for-deployment"></a>Pasos para la implementación

En este tema se da por supuesto que implementa la [plantilla de almacenamiento de ejemplo](#example-storage-template) que aparece aquí. Puede usar una plantilla distinta, pero los parámetros que escriba serán diferentes a los que se muestran en este tema.

Después de crear una plantilla, los pasos generales para implementar la plantilla son los siguientes:

1. Inicie sesión en su cuenta.
2. Seleccione la suscripción que se va a usar (solo es necesario si tiene varias suscripciones y quiere usar una distinta a la suscripción predeterminada).
3. Crear un grupo de recursos
4. Implementación de la plantilla
5. Compruebe el estado de la implementación.

Las secciones siguientes muestran cómo llevar a cabo esos pasos con [PowerShell](#powershell) o la [CLI de Azure](#azure-cli).

## <a name="powershell"></a>PowerShell

1. Para instalar Azure PowerShell, consulte [Introducción a los cmdlets de Azure PowerShell](/powershell/azure/overview).

2. Para empezar rápidamente con la implementación, use los siguientes cmdlets:

  ```powershell
  Login-AzureRmAccount
  Set-AzureRmContext -SubscriptionID {subscription-id}

  New-AzureRmResourceGroup -Name ExampleGroup -Location "Central US"
  New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleGroup -TemplateFile c:\MyTemplates\azuredeploy.json 
  ```

  El cmdlet `Set-AzureRmContext` solo se necesita si desea usar una suscripción distinta de la suscripción predeterminada. Para ver todas las suscripciones y sus identificadores, use:

  ```powershell
  Get-AzureRmSubscription
  ```

3. La implementación puede demorar unos minutos en completarse. Cuando termine, verá un mensaje similar al siguiente:

  ```powershell
  DeploymentName          : ExampleDeployment
  CorrelationId           : 07b3b233-8367-4a0b-b9bc-7aa189065665
  ResourceGroupName       : ExampleGroup
  ProvisioningState       : Succeeded
  ...
  ```

4. Para ver el grupo de recursos y la cuenta de almacenamiento que se implementaron en la suscripción, use:

  ```powershell
  Get-AzureRmResourceGroup -Name ExampleGroup
  Find-AzureRmResource -ResourceGroupNameEquals ExampleGroup
  ```

5. Puede especificar parámetros de plantilla como parámetros de PowerShell cuando implemente una plantilla. El ejemplo anterior no incluía ningún parámetro de plantilla, por lo que se usaron los valores predeterminados en la plantilla. Para implementar otra cuenta de almacenamiento y proporcionar valores de parámetro para el prefijo de nombre del almacenamiento y la SKU de la cuenta de almacenamiento, use:

  ```powershell
  New-AzureRmResourceGroupDeployment -Name ExampleDeployment2 -ResourceGroupName ExampleGroup -TemplateFile c:\MyTemplates\azuredeploy.json -storageNamePrefix "contoso" -storageSKU "Standard_GRS"
  ```

  Ahora tiene dos cuentas de almacenamiento en el grupo de recursos. 

## <a name="azure-cli"></a>Azure CLI

1. Para instalar la CLI de Azure, consulte [Instalación de la CLI de Azure 2.0](/cli/azure/install-az-cli2).

2. Para empezar a trabajar rápidamente con la implementación, use los siguientes comandos:

  ```azurecli
  az login
  az account set --subscription {subscription-id}

  az group create --name ExampleGroup --location "Central US"
  az group deployment create --name ExampleDeployment --resource-group ExampleGroup --template-file c:\MyTemplates\azuredeploy.json
  ```

  El comando `az account set` solo se necesita si desea usar una suscripción distinta de la suscripción predeterminada. Para ver todas las suscripciones y sus identificadores, use:

  ```azurecli
  az account list
  ```

3. La implementación puede demorar unos minutos en completarse. Cuando termine, verá un mensaje similar al siguiente:

  ```azurecli
  "provisioningState": "Succeeded",
  ```

4. Para ver el grupo de recursos y la cuenta de almacenamiento que se implementaron en la suscripción, use:

  ```azurecli
  az group show --name ExampleGroup
  az resource list --resource-group ExampleGroup
  ```

5. Puede especificar parámetros de plantilla como parámetros de PowerShell cuando implemente una plantilla. El ejemplo anterior no incluía ningún parámetro de plantilla, por lo que se usaron los valores predeterminados en la plantilla. Para implementar otra cuenta de almacenamiento y proporcionar valores de parámetro para el prefijo de nombre del almacenamiento y la SKU de la cuenta de almacenamiento, use:

  ```azurecli
  az group deployment create --name ExampleDeployment2 --resource-group ExampleGroup --template-file c:\MyTemplates\azuredeploy.json --parameters '{"storageNamePrefix":{"value":"contoso"},"storageSKU":{"value":"Standard_GRS"}}'
  ```

  Ahora tiene dos cuentas de almacenamiento en el grupo de recursos. 

## <a name="example-storage-template"></a>Plantilla de almacenamiento de ejemplo

Use la plantilla de ejemplo siguiente para implementar una cuenta de almacenamiento en la suscripción:

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageNamePrefix": {
      "type": "string",
      "maxLength": 11,
      "defaultValue": "storage",
      "metadata": {
        "description": "The value to use for starting the storage account name."
      }
    },
    "storageSKU": {
      "type": "string",
      "allowedValues": [
        "Standard_LRS",
        "Standard_ZRS",
        "Standard_GRS",
        "Standard_RAGRS",
        "Premium_LRS"
      ],
      "defaultValue": "Standard_LRS",
      "metadata": {
        "description": "The type of replication to use for the storage account."
      }
    }
  },
  "variables": {
    "storageName": "[concat(parameters('storageNamePrefix'), uniqueString(resourceGroup().id))]"
  },
  "resources": [
    {
      "name": "[variables('storageName')]",
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2016-05-01",
      "sku": {
        "name": "[parameters('storageSKU')]"
      },
      "kind": "Storage",
      "location": "[resourceGroup().location]",
      "tags": {},
      "properties": {
      }
    }
  ],
  "outputs": {  }
}
```

## <a name="next-steps"></a>Pasos siguientes

* Para información detallada sobre cómo usar PowerShell para implementar plantillas, consulte [Implementación de recursos con las plantillas de Resource Manager y Azure PowerShell](/azure/azure-resource-manager/resource-group-template-deploy).
* Para información detallada sobre cómo usar la CLI de Azure para implementar plantillas, consulte [Implementación de recursos con plantillas de Resource Manager y la CLI de Azure](/azure/azure-resource-manager/resource-group-template-deploy-cli).




