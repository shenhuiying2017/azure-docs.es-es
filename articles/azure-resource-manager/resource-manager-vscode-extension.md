---
title: "Plantilla de Azure Resource Manager con la extensión VS Code | Microsoft Docs"
description: "Use la extensión de herramientas de Azure Resource Manager para trabajar con plantillas de Resource Manager."
services: azure-resource-manager
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 09/06/2017
ms.topic: get-started-article
ms.author: tomfitz
ms.openlocfilehash: 0dd48146624052fbd00093066d79067b75e33c92
ms.sourcegitcommit: 094061b19b0a707eace42ae47f39d7a666364d58
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/08/2017
---
# <a name="use-visual-studio-code-extension-to-create-azure-resource-manager-template"></a>Uso de la extensión Visual Studio Code para crear plantillas de Azure Resource Manager
En este artículo se muestran las ventajas de instalar y usar la extensión de herramientas de Azure Resource Manager en Visual Studio Code. Puede crear plantillas de Resource Manager en VS Code sin la extensión, pero la extensión proporciona opciones de autocompletar que simplifican el desarrollo de la plantilla. Sugiere funciones de plantilla, parámetros y variables que están disponibles en la plantilla.

Para completar este artículo, necesita [Visual Studio Code](https://code.visualstudio.com/).

Para entender los conceptos asociados a la implementación y administración de sus soluciones de Azure, consulte [Introducción a Azure Resource Manager](resource-group-overview.md).

## <a name="create-the-template"></a>Creación de la plantilla

Este artículo se basa en la plantilla que creó en [Creación e implementación de la primera plantilla de Azure Resource Manager](resource-manager-create-first-template.md). Si ya tiene esa plantilla, puede omitir esta sección.

1. Si tiene que crear la plantilla, inicie VS Code. Seleccione **Archivo** > **Nuevo archivo**. 

   ![Nuevo archivo](./media/resource-manager-vscode-extension/new-file.png)

2. Copie y pegue la siguiente sintaxis JSON en el archivo:

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
     "outputs": {  }
   }
   ```

3. Guarde este archivo como **azuredeploy.json** en una carpeta local.

   ![Guardar plantilla](./media/resource-manager-vscode-extension/save-template.png)

## <a name="install-the-extension"></a>Instalación de la extensión

1. En VS Code, seleccione **Extensiones**.

   ![Seleccione extensiones](./media/resource-manager-vscode-extension/select-extensions.png)

2. Busque **Herramientas de Azure Resource Manager** y seleccione **Instalar**.

   ![Instalación de la extensión](./media/resource-manager-vscode-extension/install-extension.png)

3. Para finalizar la instalación de la extensión, seleccione **Volver a cargar**.

## <a name="edit-the-template"></a>Edición de la plantilla

1. Abra el archivo azuredeploy.json.

2. La extensión recupera todas las [funciones de plantilla](resource-group-template-functions.md) disponibles. También lee los parámetros y variables definidas en la plantilla. Para ver esta funcionalidad, agregará dos valores a la sección de salida. En la plantilla, reemplace la sección de salida por:

   ```json
   "outputs": { 
       "groupLocation": {
         "type": "string",
         "value": ""
       },
       "storageUri": {
         "type": "string",
         "value": ""
       }
   }
   ```

3. Coloque el cursor dentro de las marcas de expresiones de código delimitadas en **groupLocation**. Escriba el corchete de apertura (`[`). Observe que la extensión sugiere inmediatamente las funciones disponibles de la plantilla.

   ![Mostrar funciones disponibles](./media/resource-manager-vscode-extension/available-functions.png)

4. Comience a escribir **resourceGroup**. Cuando se muestre la función `resourceGroup()`, presione las teclas TAB o ENTRAR.

   ![Selección de las funciones de resourceGroup](./media/resource-manager-vscode-extension/show-resourcegroup.png)

5. La extensión rellena la sintaxis de la función. La función [resourceGroup](resource-group-template-functions-resource.md#resourcegroup) no acepta parámetros. Agregue un punto después del cierre de paréntesis. La extensión proporciona las propiedades que están disponibles para el objeto devuelto por la función `resourceGroup()`. Seleccione `location`.

   ![Seleccionar propiedades](./media/resource-manager-vscode-extension/resourcegroup-properties.png)

6. Después de **location**, agregue el corchete de cierre.

   ```json
   "outputs": { 
       "groupLocation": {
         "type": "string",
         "value": "[resourceGroup().location]"
       },
       "storageUri": {
         "type": "string",
         "value": ""
       }
   }
   ```

7. A continuación, coloque el cursor dentro de las marcas de expresiones de código delimitadas en **storageUri**. De nuevo, escriba el corchete de apertura. Comience a escribir **reference**. Cuando se seleccione esa función, presione las teclas TAB o ENTRAR.

   ![Selección de reference](./media/resource-manager-vscode-extension/add-reference.png)

8. La función [reference](resource-group-template-functions-resource.md#reference) acepta el identificador de recurso o el nombre del recurso como un parámetro. Ya tiene el nombre de la cuenta de almacenamiento en una variable. Escriba **var** y, a continuación, seleccione Ctrl + espacio. La extensión sugiere la función variables.

   ![Selección de la variable](./media/resource-manager-vscode-extension/add-variable.png)

   Presione las teclas TAB o ENTRAR.

9. La función [variables](resource-group-template-functions-deployment.md#variables) requiere el nombre de la variable. Dentro de los paréntesis, agregue una comilla simple. La extensión proporciona los nombres de las variables definidas en la plantilla.

   ![Mostrar variables](./media/resource-manager-vscode-extension/show-variables.png) 

10. Seleccione la variable **storageName**. Agregue el corchete de cierre. En el siguiente ejemplo se muestran la sección de salida:

   ```json
   "outputs": { 
       "groupLocation": {
         "type": "string",
         "value": "[resourceGroup().location]"
       },
       "storageUri": {
         "type": "string",
         "value": "[reference(concat('Microsoft.Storage/storageAccounts/',variables('storageName'))).primaryEndpoints.blob]"
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
  "outputs": { 
    "groupLocation": {
      "type": "string",
      "value": "[resourceGroup().location]"
    },
    "storageUri": {
      "type": "string",
      "value": "[reference(concat('Microsoft.Storage/storageAccounts/',variables('storageName'))).primaryEndpoints.blob]"
    }
  }
}
```

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

Cuando finalice la implementación, se devuelven los valores de salida.

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
* Para aprender más sobre la estructura de una plantilla, consulte [Creación de plantillas de Azure Resource Manager](resource-group-authoring-templates.md).
* Para obtener información acerca de las propiedades de una cuenta de almacenamiento, consulte la [referencia de plantillas de cuentas de almacenamiento](/azure/templates/microsoft.storage/storageaccounts).
* Para ver plantillas completas de muchos tipos diferentes de soluciones, consulte [Plantillas de inicio rápido de Azure](https://azure.microsoft.com/documentation/templates/).
