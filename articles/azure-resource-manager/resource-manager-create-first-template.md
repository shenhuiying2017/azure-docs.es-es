---
title: "Creación de la primera plantilla de Azure Resource Manager | Microsoft Docs"
description: "Una guía paso a paso para crear su primera plantilla de Azure Resource Manager. Muestra cómo utilizar la referencia de plantilla para una cuenta de almacenamiento para crear la plantilla."
services: azure-resource-manager
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 10/02/2017
ms.topic: get-started-article
ms.author: tomfitz
ms.openlocfilehash: 7d20469aaf2dfdd7a5f3650983b59152de837837
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="create-and-deploy-your-first-azure-resource-manager-template"></a>Creación e implementación de la primera plantilla de Azure Resource Manager
Este tema lo guiará por los pasos para crear la primera plantilla de Azure Resource Manager. Las plantillas de Resource Manager son archivos JSON que definen los recursos que necesita para implementar la solución. Para entender los conceptos asociados a la implementación y administración de sus soluciones de Azure, consulte [Introducción a Azure Resource Manager](resource-group-overview.md). Si tiene recursos existentes y desea obtener una plantilla para ellos, consulte [Exportación de plantillas de Azure Resource Manager desde recursos existentes](resource-manager-export-template.md).

Para crear y revisar las plantillas, se necesita un editor de JSON. [Visual Studio Code](https://code.visualstudio.com/) es un editor de código ligero, de código abierto y multiplataforma. Se recomienda encarecidamente usar código de Visual Studio para crear plantillas de Resource Manager. En este artículo se da por supuesto que usa VS Code. Si tiene otro editor de JSON (por ejemplo, Visual Studio), puede usarlo.

## <a name="prerequisites"></a>Requisitos previos

* Código de Visual Studio. Si es necesario, instálelo desde [https://code.visualstudio.com/](https://code.visualstudio.com/).
* Una suscripción de Azure. Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

## <a name="create-template"></a>Creación de una plantilla

Puede empezar con una plantilla sencilla que implemente una cuenta de almacenamiento para su suscripción.

1. Seleccione **Archivo** > **Nuevo archivo**. 

   ![Nuevo archivo](./media/resource-manager-create-first-template/new-file.png)

2. Copie y pegue la siguiente sintaxis JSON en el archivo:

   ```json
   {
     "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
     "contentVersion": "1.0.0.0",
     "parameters": {
     },
     "variables": {
     },
     "resources": [
       {
         "name": "[concat('storage', uniqueString(resourceGroup().id))]",
         "type": "Microsoft.Storage/storageAccounts",
         "apiVersion": "2016-01-01",
         "sku": {
           "name": "Standard_LRS"
         },
         "kind": "Storage",
         "location": "South Central US",
         "tags": {},
         "properties": {}
       }
     ],
     "outputs": {  }
   }
   ```

   Los nombres de cuenta de almacenamiento tienen varias restricciones que dificultan el establecimiento. El nombre debe tener entre 3 y 24 caracteres, usar solo números y letras minúsculas, y ser único. La plantilla anterior utiliza la función [uniqueString](resource-group-template-functions-string.md#uniquestring) para generar un valor hash. Para dar más significado a este valor hash, agrega el prefijo *storage*. 

3. Guarde este archivo como **azuredeploy.json** en una carpeta local.

   ![Guardar plantilla](./media/resource-manager-create-first-template/save-template.png)

## <a name="deploy-template"></a>Implementar plantilla

Está listo para implementar esta plantilla. Use PowerShell o la CLI de Azure para crear un grupo de recursos. Después, implemente una cuenta de almacenamiento en ese grupo de recursos.

* Para PowerShell, use los siguientes comandos desde la carpeta que contenga la plantilla:

   ```powershell
   Login-AzureRmAccount
   
   New-AzureRmResourceGroup -Name examplegroup -Location "South Central US"
   New-AzureRmResourceGroupDeployment -ResourceGroupName examplegroup -TemplateFile azuredeploy.json
   ```

* Para una instalación local de la CLI de Azure, use los siguientes comandos desde la carpeta que contenga la plantilla:

   ```azurecli
   az login

   az group create --name examplegroup --location "South Central US"
   az group deployment create --resource-group examplegroup --template-file azuredeploy.json
   ```

Cuando finaliza la implementación, la cuenta de almacenamiento existe en el grupo de recursos.

[!INCLUDE [resource-manager-cloud-shell-deploy.md](../../includes/resource-manager-cloud-shell-deploy.md)]

Para la CLI de Azure, use los siguientes comandos:

```azurecli-interactive
az group create --name examplegroup --location "South Central US"
az group deployment create --resource-group examplegroup --template-file clouddrive/templates/azuredeploy.json
```

Actualmente, PowerShell está disponible en Cloud Shell como versión preliminar. Para PowerShell, use los siguientes comandos:

```powershell
New-AzureRmResourceGroup -Name examplegroup -Location "South Central US"
New-AzureRmResourceGroupDeployment -ResourceGroupName examplegroup -TemplateFile $home\CloudDrive\templates\azuredeploy.json
```

Cuando finaliza la implementación, la cuenta de almacenamiento existe en el grupo de recursos.

## <a name="customize-the-template"></a>Personalización de la plantilla

La plantilla funciona bien, pero no es flexible. Siempre se implementa un almacenamiento con redundancia local para el centro-sur de EE. UU. El nombre es siempre *storage* (almacenamiento) seguido por un valor de hash. Para habilitar el uso de la plantilla para diferentes escenarios, agregue parámetros a la plantilla.

En el ejemplo siguiente se muestra la sección de parámetros con dos parámetros. El primer parámetro `storageSKU` le permite especificar el tipo de redundancia. Limita los valores que puede pasar a los válidos para una cuenta de almacenamiento. También especifica un valor predeterminado. El segundo parámetro `storageNamePrefix` se establece para permitir 11 caracteres como máximo. Especifica un valor predeterminado.

```json
"parameters": {
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
  },
  "storageNamePrefix": {
    "type": "string",
    "maxLength": 11,
    "defaultValue": "storage",
    "metadata": {
      "description": "The value to use for starting the storage account name. Use only lowercase letters and numbers."
    }
  }
},
```

En la sección de variables, agregue una variable denominada `storageName`. Combina el valor del prefijo de los parámetros y un valor hash de la función [uniqueString](resource-group-template-functions-string.md#uniquestring). Usa la función [toLower](resource-group-template-functions-string.md#tolower) para convertir todos los caracteres a minúsculas.

```json
"variables": {
  "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
},
```

Para utilizar estos nuevos valores para la cuenta de almacenamiento, cambie la definición del recurso:

```json
"resources": [
  {
    "name": "[variables('storageName')]",
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2016-01-01",
    "sku": {
      "name": "[parameters('storageSKU')]"
    },
    "kind": "Storage",
    "location": "[resourceGroup().location]",
    "tags": {},
    "properties": {}
  }
],
```

Tenga en cuenta que el nombre de la cuenta de almacenamiento se establece ahora en la variable que ha agregado. El nombre SKU se establece en el valor del parámetro. La ubicación se establece en la misma que la del grupo de recursos.

Guarde el archivo. 

La plantilla ahora tiene el aspecto siguiente:

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
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
    },   
    "storageNamePrefix": {
      "type": "string",
      "maxLength": 11,
      "defaultValue": "storage",
      "metadata": {
        "description": "The value to use for starting the storage account name. Use only lowercase letters and numbers."
      }
    }
  },
  "variables": {
    "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
  },
  "resources": [
    {
      "name": "[variables('storageName')]",
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2016-01-01",
      "sku": {
        "name": "[parameters('storageSKU')]"
      },
      "kind": "Storage",
      "location": "[resourceGroup().location]",
      "tags": {},
      "properties": {}
    }
  ],
  "outputs": {  }
}
```

## <a name="redeploy-template"></a>Volver a implementar la plantilla

Vuelva a implementar la plantilla con valores diferentes.

Para PowerShell, use:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName examplegroup -TemplateFile azuredeploy.json -storageNamePrefix newstore -storageSKU Standard_RAGRS
```

Para la CLI de Azure, utilice:

```azurecli
az group deployment create --resource-group examplegroup --template-file azuredeploy.json --parameters storageSKU=Standard_RAGRS storageNamePrefix=newstore
```

Para Cloud Shell, cargue la plantilla modificada en el recurso compartido de archivos. Sobrescriba el archivo existente. Use el comando siguiente:

```azurecli
az group deployment create --resource-group examplegroup --template-file clouddrive/templates/azuredeploy.json --parameters storageSKU=Standard_RAGRS storageNamePrefix=newstore
```

## <a name="use-autocomplete"></a>Uso de Autocompletar

Hasta ahora, el trabajo en las plantillas ha consistido en copiar y pegar código JSON de este artículo. Sin embargo, al desarrollar sus propias plantillas, querrá buscar y especificar propiedades y valores que estén disponibles para el tipo de recurso. VS Code lee el esquema del tipo de recurso y sugiere propiedades y valores. Para ver la característica Autocompletar, vaya al elemento de propiedades de su plantilla y agregue una nueva línea. Escriba comillas y advertirá que VS Code sugiere inmediatamente nombres que están disponibles dentro del elemento de propiedades.

![Visualización de las propiedades disponibles](./media/resource-manager-create-first-template/show-properties.png)

Seleccione **Cifrado**. Escriba dos puntos (:) y VS Code sugiere que agregue un nuevo objeto.

![Adición de un objeto](./media/resource-manager-create-first-template/add-object.png)

Presione las teclas TAB o ENTRAR para agregar el objeto.

De nuevo, escriba un signo de interrogación y vea que VS Code sugiere ahora propiedades que están disponibles para cifrado.

![Visualización de las propiedades de cifrado](./media/resource-manager-create-first-template/show-encryption-properties.png)

Seleccione **Servicios** y siga agregando valores en función de las extensiones de VS Code hasta que tenga:

```json
"properties": {
    "encryption":{
        "services":{
            "blob":{
              "enabled":true
            }
        }
    }
}
```

Ya ha habilitado el cifrado de blobs para la cuenta de almacenamiento. Sin embargo, VS Code ha identificado un problema. Tenga en cuenta que el cifrado tiene una advertencia.

![Advertencia de cifrado](./media/resource-manager-create-first-template/encryption-warning.png)

Para ver la advertencia, mantenga el mouse sobre la línea verde.

![Propiedad que falta](./media/resource-manager-create-first-template/missing-property.png)

Verá que el elemento cifrado requiere una propiedad keySource. Agregue una coma después del objeto de servicio y agregue la propiedad keySource. VS Code sugiere **"Microsoft.Storage"** como valor válido. Cuando termine, el elemento de propiedades es:

```json
"properties": {
    "encryption":{
        "services":{
            "blob":{
              "enabled":true
            }
        },
        "keySource":"Microsoft.Storage"
    }
}
```

La plantilla final es:

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
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
    },   
    "storageNamePrefix": {
      "type": "string",
      "maxLength": 11,
      "defaultValue": "storage",
      "metadata": {
        "description": "The value to use for starting the storage account name. Use only lowercase letters and numbers."
      }
    }
  },
  "variables": {
    "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
  },
  "resources": [
    {
      "name": "[variables('storageName')]",
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2016-01-01",
      "sku": {
        "name": "[parameters('storageSKU')]"
      },
      "kind": "Storage",
      "location": "[resourceGroup().location]",
      "tags": {},
      "properties": {
        "encryption":{
          "services":{
            "blob":{
              "enabled":true
            }
          },
          "keySource":"Microsoft.Storage"
        }
      }
    }
  ],
  "outputs": {}
}
```

## <a name="deploy-encrypted-storage"></a>Implementación del almacenamiento cifrado

De nuevo, implemente la plantilla y proporcione un nuevo nombre de cuenta de almacenamiento.

Para PowerShell, use:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName examplegroup -TemplateFile azuredeploy.json -storageNamePrefix storesecure
```

Para la CLI de Azure, utilice:

```azurecli
az group deployment create --resource-group examplegroup --template-file azuredeploy.json --parameters storageNamePrefix=storesecure
```

Para Cloud Shell, cargue la plantilla modificada en el recurso compartido de archivos. Sobrescriba el archivo existente. Use el comando siguiente:

```azurecli
az group deployment create --resource-group examplegroup --template-file clouddrive/templates/azuredeploy.json --parameters storageNamePrefix=storesecure
```

## <a name="clean-up-resources"></a>Limpieza de recursos

Cuando ya no sean necesarios, limpie los recursos que implementó eliminando el grupo de recursos.

Para PowerShell, use:

```powershell
Remove-AzureRmResourceGroup -Name examplegroup
```

Para la CLI de Azure, utilice:

```azurecli
az group delete --name examplegroup
```

## <a name="next-steps"></a>Pasos siguientes
* Para obtener más ayuda con el desarrollo de plantillas, puede instalar una extensión de VS Code. Para más información, consulte [Use Visual Studio Code extension to create Azure Resource Manager template](resource-manager-vscode-extension.md) (Uso de la extensión de Visual Studio Code para crear una plantilla de Azure Resource Manager).
* Para aprender más sobre la estructura de una plantilla, consulte [Creación de plantillas de Azure Resource Manager](resource-group-authoring-templates.md).
* Para obtener información acerca de las propiedades de una cuenta de almacenamiento, consulte la [referencia de plantillas de cuentas de almacenamiento](/azure/templates/microsoft.storage/storageaccounts).
* Para ver plantillas completas de muchos tipos diferentes de soluciones, consulte [Plantillas de inicio rápido de Azure](https://azure.microsoft.com/documentation/templates/).
