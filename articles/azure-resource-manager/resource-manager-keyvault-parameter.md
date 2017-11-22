---
title: Secreto de Key Vault con la plantilla de Azure Resource Manager | Microsoft Docs
description: "Muestra cómo pasar un secreto de un almacén de claves como un parámetro durante la implementación."
services: azure-resource-manager,key-vault
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2017
ms.author: tomfitz
ms.openlocfilehash: e789a234979be877d990665902fd6219ae7ec40b
ms.sourcegitcommit: dcf5f175454a5a6a26965482965ae1f2bf6dca0a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/10/2017
---
# <a name="use-azure-key-vault-to-pass-secure-parameter-value-during-deployment"></a>Uso de Azure Key Vault para pasar el valor de parámetro seguro durante la implementación

Cuando tiene que pasar un valor seguro (por ejemplo, una contraseña) como un parámetro durante la implementación, puede recuperar el valor de [Azure Key Vault](../key-vault/key-vault-whatis.md). El valor se recupera haciendo referencia a Key Vault y al secreto del archivo de parámetros. El valor nunca se expone debido a que solo hace referencia a su identificador de almacén de claves. No es necesario especificar manualmente el valor del secreto cada vez que implementan los recursos. Key Vault puede existir en una suscripción diferente en la que está implementando el grupo de recursos. Cuando hace referencia a Key Vault, incluye el identificador de suscripción.

Al crear el almacén de claves, establezca la propiedad *enabledForTemplateDeployment* en *true*. Al establecer este valor en true, permite el acceso desde las plantillas de Resource Manager durante la implementación.

## <a name="deploy-a-key-vault-and-secret"></a>Implementación de un almacén de claves y un secreto

Para crear un almacén de claves y un secreto, use la CLI de Azure o PowerShell. Tenga en cuenta que el almacén de claves está habilitado para la implementación de plantillas. 

Para la CLI de Azure, utilice:

```azurecli-interactive
vaultname={your-unique-vault-name}
password={password-value}

az group create --name examplegroup --location 'South Central US'
az keyvault create \
  --name $vaultname \
  --resource-group examplegroup \
  --location 'South Central US' \
  --enabled-for-template-deployment true
az keyvault secret set --vault-name $vaultname --name examplesecret --value $password
```

Para PowerShell, use:

```powershell
$vaultname = "{your-unique-vault-name}"
$password = "{password-value}"

New-AzureRmResourceGroup -Name examplegroup -Location "South Central US"
New-AzureRmKeyVault `
  -VaultName $vaultname `
  -ResourceGroupName examplegroup `
  -Location "South Central US" `
  -EnabledForTemplateDeployment
$secretvalue = ConvertTo-SecureString $password -AsPlainText -Force
Set-AzureKeyVaultSecret -VaultName $vaultname -Name "examplesecret" -SecretValue $secretvalue
```

## <a name="enable-access-to-the-secret"></a>Habilitación del acceso al secreto

Si usa un nuevo Key Vault o uno ya existente, asegúrese de que el usuario que implementa la plantilla puede acceder al secreto. El usuario que implementa una plantilla que hace referencia a un secreto debe tener el permiso `Microsoft.KeyVault/vaults/deploy/action` para Key Vault. Los roles [Propietario](../active-directory/role-based-access-built-in-roles.md#owner) y [Colaborador](../active-directory/role-based-access-built-in-roles.md#contributor) conceden este acceso.

## <a name="reference-a-secret-with-static-id"></a>Referencia a un secreto con identificador estático

La plantilla que recibe un secreto de Key Vault es como cualquier otra plantilla. Esto se debe a que **se hace referencia al almacén de claves del archivo de parámetros, no de la plantilla**. Por ejemplo, la siguiente plantilla implementa una base de datos SQL que incluye una contraseña de administrador. El parámetro de contraseña se establece en una cadena segura. Pero la plantilla no especifica de dónde procede ese valor.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "adminLogin": {
      "type": "string"
    },
    "adminPassword": {
      "type": "securestring"
    },
    "sqlServerName": {
      "type": "string"
    }
  },
  "resources": [
    {
      "name": "[parameters('sqlServerName')]",
      "type": "Microsoft.Sql/servers",
      "apiVersion": "2015-05-01-preview",
      "location": "[resourceGroup().location]",
      "tags": {},
      "properties": {
        "administratorLogin": "[parameters('adminLogin')]",
        "administratorLoginPassword": "[parameters('adminPassword')]",
        "version": "12.0"
      }
    }
  ],
  "outputs": {
  }
}
```

Ahora, cree un archivo de parámetros para la plantilla anterior. En el archivo de parámetros, especifique un parámetro que coincida con el nombre del parámetro de la plantilla. Para el valor del parámetro, haga referencia al secreto del almacén de claves. Se hace referencia al secreto pasando el identificador de recurso de almacén de claves y el nombre del secreto. En este ejemplo, ya debe existir el secreto del almacén de claves y se utiliza un valor estático para el mismo identificador de recurso.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "adminLogin": {
            "value": "exampleadmin"
        },
        "adminPassword": {
            "reference": {
              "keyVault": {
                "id": "/subscriptions/<subscription-id>/resourceGroups/examplegroup/providers/Microsoft.KeyVault/vaults/<vault-name>"
              },
              "secretName": "examplesecret"
            }
        },
        "sqlServerName": {
            "value": "<your-server-name>"
        }
    }
}
```

Ahora, implemente la plantilla y pase el archivo de parámetros. Para la CLI de Azure, utilice:

```azurecli-interactive
az group create --name datagroup --location "Central US"
az group deployment create \
    --name exampledeployment \
    --resource-group datagroup \
    --template-file sqlserver.json \
    --parameters @sqlserver.parameters.json
```

Para PowerShell, use:

```powershell
New-AzureRmResourceGroup -Name datagroup -Location "Central US"
New-AzureRmResourceGroupDeployment `
  -Name exampledeployment `
  -ResourceGroupName datagroup `
  -TemplateFile sqlserver.json `
  -TemplateParameterFile sqlserver.parameters.json
```

## <a name="reference-a-secret-with-dynamic-id"></a>Referencia a un secreto con identificador dinámico

En la sección anterior se mostró cómo pasar un identificador de recurso estático para el secreto del almacén de claves. Sin embargo, en algunos escenarios, debe hacer referencia a un secreto del Almacén de claves que varía en función de la implementación actual. En ese caso, no se puede codificar el identificador de recurso en el archivo de parámetros. Desafortunadamente, no se puede generar dinámicamente el identificador de recurso en el archivo de parámetros, ya que no se permiten expresiones de plantilla en este tipo de archivos.

Para generar dinámicamente el identificador de recurso de un secreto del almacén de claves, debe mover los recursos que necesite el secreto a una plantilla anidada. En la plantilla principal, agregue la plantilla anidada y pase un parámetro que contenga el identificador de recurso generado dinámicamente. La plantilla anidada debe estar disponible a través de un URI externo. En el resto de este artículo se da por supuesto que ha agregado la plantilla anterior a una cuenta de almacenamiento y que está disponible a través del URI: `https://<storage-name>.blob.core.windows.net/templatecontainer/sqlserver.json`.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "vaultName": {
        "type": "string"
      },
      "vaultResourceGroup": {
        "type": "string"
      },
      "secretName": {
        "type": "string"
      },
      "adminLogin": {
        "type": "string"
      },
      "sqlServerName": {
        "type": "string"
      }
    },
    "resources": [
    {
      "apiVersion": "2015-01-01",
      "name": "nestedTemplate",
      "type": "Microsoft.Resources/deployments",
      "properties": {
        "mode": "incremental",
        "templateLink": {
          "uri": "https://<storage-name>.blob.core.windows.net/templatecontainer/sqlserver.json",
          "contentVersion": "1.0.0.0"
        },
        "parameters": {
          "adminPassword": {
            "reference": {
              "keyVault": {
                "id": "[resourceId(subscription().subscriptionId,  parameters('vaultResourceGroup'), 'Microsoft.KeyVault/vaults', parameters('vaultName'))]"
              },
              "secretName": "[parameters('secretName')]"
            }
          },
          "adminLogin": { "value": "[parameters('adminLogin')]" },
          "sqlServerName": {"value": "[parameters('sqlServerName')]"}
        }
      }
    }],
    "outputs": {}
}
```

Implemente la plantilla anterior y proporcione valores para los parámetros.

## <a name="next-steps"></a>Pasos siguientes
* Para obtener información general sobre almacenes de claves, consulte el artículo de [introducción a Azure Key Vault](../key-vault/key-vault-get-started.md).
* Para obtener ejemplos completos de secretos de clave de referencia, consulte [Ejemplos de Key Vault](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples).
