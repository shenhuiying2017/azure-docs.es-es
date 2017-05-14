---
title: "Creación de la primera plantilla de Azure Resource Manager | Microsoft Docs"
description: "Una guía paso a paso para crear su primera plantilla de Azure Resource Manager. Muestra cómo utilizar la referencia de plantilla para una cuenta de almacenamiento para crear la plantilla."
services: azure-resource-manager
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 04/18/2017
ms.topic: get-started-article
ms.author: tomfitz
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: 745e115409a5b1e5e4f343ca0a5bb922272d3020
ms.contentlocale: es-es
ms.lasthandoff: 05/11/2017

---

# <a name="create-your-first-azure-resource-manager-template"></a>Creación de la primera plantilla de Azure Resource Manager
Este tema lo guiará por los pasos para crear la primera plantilla de Azure Resource Manager. Las plantillas de Resource Manager son archivos JSON que definen los recursos que necesita para implementar la solución. Para entender los conceptos asociados a la implementación y administración de sus soluciones de Azure, consulte [Introducción a Azure Resource Manager](resource-group-overview.md). Si tiene recursos existentes y desea obtener una plantilla para ellos, consulte [Exportación de plantillas de Azure Resource Manager desde recursos existentes](resource-manager-export-template.md).

Para crear y revisar las plantillas, se necesita un editor de JSON. [Visual Studio Code](https://code.visualstudio.com/) es un editor de código ligero, de código abierto y multiplataforma. Permite crear y editar plantillas de Resource Manager mediante una extensión. En este tema se da por supuesto que se utiliza Visual Studio Code; sin embargo, si tiene otro editor de JSON (por ejemplo, Visual Studio), puede usarlo.

## <a name="get-vs-code-and-extension"></a>Obtención de Visual Studio Code y extensión
1. Si es necesario, instale Visual Studio Code desde [https://code.visualstudio.com/](https://code.visualstudio.com/).

2. Instale la extensión [Herramientas de Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools) accediendo a Quick Open (Ctrl + P) y ejecutando: 

   ```
   ext install msazurermtools.azurerm-vscode-tools
   ```

3. Reinicie VS Code cuando se le solicite para habilitar la extensión.

## <a name="create-blank-template"></a>Creación de la plantilla en blanco

Comencemos con una plantilla en blanco que incluya solo las secciones básicas de una plantilla.

1. Cree un archivo. 

2. Copie y pegue la siguiente sintaxis JSON en el archivo:

   ```json
   {
     "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
     "contentVersion": "1.0.0.0",
     "parameters": {  },
     "variables": {  },
     "resources": [  ],
     "outputs": {  }
   }
   ```

3. Guarde este archivo como **azuredeploy.json**. 

## <a name="add-storage-account"></a>Agregar cuenta de almacenamiento
1. Para definir una cuenta de almacenamiento para la implementación, agregue esa cuenta de almacenamiento a la sección **resources** de la plantilla. Para buscar los valores que están disponibles para la cuenta de almacenamiento, examine la [referencia de la plantilla de cuentas de almacenamiento](/azure/templates/microsoft.storage/storageaccounts). Copie el JSON que se muestra para la cuenta de almacenamiento. 

3. Pegue dicho JSON en la sección **resources** de la plantilla, tal como se muestra en el ejemplo siguiente: 

   ```json
   {
     "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
     "contentVersion": "1.0.0.0",
     "parameters": {  },
     "variables": {  },
     "resources": [
       {
         "name": "string",
         "type": "Microsoft.Storage/storageAccounts",
         "apiVersion": "2016-05-01",
         "sku": {
           "name": "string"
         },
         "kind": "string",
         "location": "string",
         "tags": {},
         "properties": {
           "customDomain": {
             "name": "string",
             "useSubDomain": boolean
           },
           "encryption": {
             "services": {
               "blob": {
                 "enabled": boolean
               }
             },
             "keySource": "Microsoft.Storage"
           },
           "accessTier": "string"
         }
       }
     ],
     "outputs": {  }
   }
   ```

  En el ejemplo anterior se incluyen muchos de los valores de marcador de posición y algunas propiedades que quizá no necesite en su cuenta de almacenamiento.

## <a name="set-values-for-storage-account"></a>Establecimiento de los valores de la cuenta de almacenamiento

Ahora, está listo para establecer los valores de la cuenta de almacenamiento. 

1. Vuelva a consultar la [referencia de la plantilla de cuentas de almacenamiento](/azure/templates/microsoft.storage/storageaccounts) donde copió el JSON. Hay varias tablas que describen las propiedades y proporcionan los valores disponibles. 

2. Tenga en cuenta que en el elemento **properties**, se enumeran **customDomain**, **encryption** y **accessTier** como no necesarios. Estos valores pueden ser importantes para sus escenarios, pero para simplificar este ejemplo, vamos a quitarlos.

   ```json
   "resources": [
     {
       "name": "string",
       "type": "Microsoft.Storage/storageAccounts",
       "apiVersion": "2016-05-01",
       "sku": {
         "name": "string"
       },
       "kind": "string",
       "location": "string",
       "tags": {},
       "properties": {
       }
     }
   ],
   ```

3. Actualmente, el elemento **kind** está establecido en un valor de marcador de posición ("string"). Visual Studio Code incluye muchas características que le ayudarán a comprender los valores que se usarán en la plantilla. Tenga en cuenta que Visual Studio Code indica que este valor no es válido. Si mantiene el puntero sobre "string", Visual Studio Code sugiere que los valores válidos para **kind** son `Storage` o `BlobStorage`. 

   ![mostrar valores sugeridos de Visual Studio Code](./media/resource-manager-create-first-template/vs-code-show-values.png)

   Para ver los valores disponibles, elimine los caracteres entre las comillas dobles y seleccione **Ctrl+barra espaciadora**. Seleccione **Storage** entre las opciones disponibles.
  
   ![mostrar IntelliSense](./media/resource-manager-create-first-template/intellisense.png)

   Si no se utiliza Visual Studio Code, examine la página de referencia de la plantilla de cuentas de almacenamiento. Observe que la descripción muestra los mismos valores válidos. Establezca el elemento en **Storage**.

   ```json
   "kind": "Storage",
   ```

La plantilla ahora tiene el aspecto siguiente:

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {  },
  "variables": {  },
  "resources": [
    {
      "name": "string",
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2016-05-01",
      "sku": {
        "name": "string"
      },
      "kind": "Storage",
      "location": "string",
      "tags": {},
      "properties": {
      }
    }
  ],
  "outputs": {  }
}
```

## <a name="add-template-function"></a>Adición de la función de plantilla

Puede usar las funciones dentro de la plantilla para simplificar la sintaxis de la plantilla y para recuperar los valores que solo están disponibles cuando se implementa la plantilla. Para obtener la lista completa de las funciones de plantilla, consulte [Funciones de la plantilla de Azure Resource Manager](resource-group-template-functions.md).

Para especificar que la cuenta de almacenamiento se implementa en la misma ubicación que el grupo de recursos, establezca la propiedad **location** en:

```json
"location": "[resourceGroup().location]",
```

Una vez más, Visual Studio Code le ayuda al sugerir las funciones disponibles. 

![mostrar funciones](./media/resource-manager-create-first-template/show-functions.png)

Tenga en cuenta que la función va entre corchetes. La función [resourceGroup](resource-group-template-functions-resource.md#resourcegroup) devuelve un objeto con una propiedad denominada `location`. El grupo de recursos contiene todos los recursos relacionados para la solución. Se puede codificar la propiedad location en un valor como "Central US" (Centro de EE. UU.), pero tendría que cambiar manualmente la plantilla para volver a implementarla en una ubicación diferente. Mediante la función `resourceGroup`, facilita el proceso de volver a implementar esta plantilla en otro grupo de recursos en una ubicación diferente.

La plantilla ahora tiene el aspecto siguiente:

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {  },
  "variables": {  },
  "resources": [
    {
      "name": "string",
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2016-05-01",
      "sku": {
        "name": "string"
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

## <a name="add-parameters-and-variables"></a>Adición de parámetros y variables
Solo quedan dos valores para establecer en la plantilla - **name** y **sku.name**. Para estas propiedades, puede agregar parámetros que le permiten personalizar estos valores durante la implementación. 

Los nombres de cuenta de almacenamiento tienen varias restricciones que dificultan el establecimiento. El nombre debe tener entre 3 y 24 caracteres, usar solo números y letras minúsculas, y ser único. En lugar de tratar de adivinar un valor único que coincida con las restricciones, utilice la función [uniqueString](resource-group-template-functions-string.md#uniquestring) para generar un valor de hash. Para dar más significado a este valor de hash, agregue un prefijo que le ayude a identificarla como una cuenta de almacenamiento después de la implementación. 

1. Para pasar un prefijo para el nombre que coincide con las convenciones de nomenclatura, vaya a la sección **parameters** de la plantilla. Agregue un parámetro a la plantilla que acepte un prefijo para el nombre de cuenta de almacenamiento:

   ```json
   "parameters": {
     "storageNamePrefix": {
       "type": "string",
       "maxLength": 11,
       "defaultValue": "storage",
       "metadata": {
         "description": "The value to use for starting the storage account name."
       }
     }
   },
   ```

  El prefijo se limita a un máximo de 11 caracteres porque `uniqueString` devuelve 13 caracteres y el nombre no puede superar los 24 caracteres. Si no pasa un valor para el parámetro durante la implementación, se utiliza el valor predeterminado.

2. Vaya a la sección **variables** de la plantilla. Para construir el nombre del prefijo y de la cadena única, agregue la siguiente variable:

   ```json
   "variables": {
     "storageName": "[concat(parameters('storageNamePrefix'), uniqueString(resourceGroup().id))]"
   },
   ```

3. En la sección **resources**, establezca el nombre de cuenta de almacenamiento a esa variable.

   ```json
   "name": "[variables('storageName')]",
   ```

3. Para permitir pasar SKU diferentes a la cuenta de almacenamiento, vaya a la sección **parameters**. Después del parámetro para el prefijo de nombre de almacenamiento, agregue un parámetro que especifique los valores de SKU permitidos y un valor predeterminado. Puede encontrar los valores permitidos en la página de referencia de la plantilla o en Visual Studio Code. En el ejemplo siguiente, se incluyen todos los valores válidos para SKU. Sin embargo, puede limitar los valores permitidos a aquellos tipos de SKU que se van a implementar a través de esta plantilla.

   ```json
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
   ```

3. Cambie la propiedad SKU para que utilice el valor del parámetro:

   ```json
   "sku": {
     "name": "[parameters('storageSKU')]"
   },
   ```    

4. Guarde el archivo.

## <a name="final-template"></a>Plantilla final

Después de completar los pasos descritos en este artículo, la plantilla tendrá ahora el siguiente aspecto:

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
* La plantilla está completa y ya está listo para implementarla en su suscripción. Para realizar la implementación, consulte [Implementación de recursos en Azure](resource-manager-quickstart-deploy.md).
* Para aprender más sobre la estructura de una plantilla, consulte [Creación de plantillas de Azure Resource Manager](resource-group-authoring-templates.md).
* Para ver plantillas completas de muchos tipos diferentes de soluciones, consulte [Plantillas de inicio rápido de Azure](https://azure.microsoft.com/documentation/templates/).

