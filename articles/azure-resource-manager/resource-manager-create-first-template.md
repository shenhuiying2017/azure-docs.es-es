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
ms.date: 07/27/2017
ms.topic: get-started-article
ms.author: tomfitz
ms.translationtype: HT
ms.sourcegitcommit: 6e76ac40e9da2754de1d1aa50af3cd4e04c067fe
ms.openlocfilehash: 49086b51e2db1aebed45746306ae14b6f1feb631
ms.contentlocale: es-es
ms.lasthandoff: 07/31/2017

---

# <a name="create-and-deploy-your-first-azure-resource-manager-template"></a>Creación e implementación de la primera plantilla de Azure Resource Manager
Este tema lo guiará por los pasos para crear la primera plantilla de Azure Resource Manager. Las plantillas de Resource Manager son archivos JSON que definen los recursos que necesita para implementar la solución. Para entender los conceptos asociados a la implementación y administración de sus soluciones de Azure, consulte [Introducción a Azure Resource Manager](resource-group-overview.md). Si tiene recursos existentes y desea obtener una plantilla para ellos, consulte [Exportación de plantillas de Azure Resource Manager desde recursos existentes](resource-manager-export-template.md).

Para crear y revisar las plantillas, se necesita un editor de JSON. [Visual Studio Code](https://code.visualstudio.com/) es un editor de código ligero, de código abierto y multiplataforma. Se recomienda encarecidamente usar código de Visual Studio para crear plantillas de Resource Manager. En este tema se da por supuesto que se utiliza Visual Studio Code; sin embargo, si tiene otro editor de JSON (por ejemplo, Visual Studio), puede usarlo.

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

## <a name="deploy-template-from-cloud-shell"></a>Implementación de una plantilla desde Cloud Shell

Puede usar [Cloud Shell](../cloud-shell/overview.md) para ejecutar los comandos de la CLI de Azure para implementar la plantilla. Pero primero debe cargar la plantilla en el recurso compartido de archivos de Cloud Shell. Si no ha usado Cloud Shell, vea [Introducción a Azure Cloud Shell](../cloud-shell/overview.md) para más información sobre su configuración.

1. Inicie sesión en [Azure Portal](https://portal.azure.com).   

2. Seleccione el grupo de recursos de Cloud Shell. El patrón de nombre es `cloud-shell-storage-<region>`.

   ![Selección de un grupo de recursos](./media/resource-manager-create-first-template/select-cs-resource-group.png)

3. Seleccione la cuenta de almacenamiento de Cloud Shell.

   ![Selección de la cuenta de almacenamiento](./media/resource-manager-create-first-template/select-storage.png)

4. Seleccione **Archivos**.

   ![Seleccionar archivos](./media/resource-manager-create-first-template/select-files.png)

5. Seleccione el recurso compartido de archivos de Cloud Shell. El patrón de nombre es `cs-<user>-<domain>-com-<uniqueGuid>`.

   ![Selección de recurso compartido de archivos](./media/resource-manager-create-first-template/select-file-share.png)

6. Seleccione **Agregar directorio**.

   ![Agregar directorio](./media/resource-manager-create-first-template/select-add-directory.png)

7. Asígnele el nombre **plantillas** y seleccione **Correcto**.

   ![Nombre de directorio](./media/resource-manager-create-first-template/name-templates.png)

8. Seleccione el nuevo directorio.

   ![Selección de directorio](./media/resource-manager-create-first-template/select-templates.png)

9. Seleccione **Cargar**.

   ![Selección de carga](./media/resource-manager-create-first-template/select-upload.png)

10. Busque y cargue la plantilla.

   ![Carga de archivo](./media/resource-manager-create-first-template/upload-files.png)

11. Abra el símbolo del sistema.

   ![Apertura de Cloud Shell](./media/resource-manager-create-first-template/start-cloud-shell.png)

12. En Cloud Shell, escriba los comandos siguientes:

   ```azurecli
   az group create --name examplegroup --location "South Central US"
   az group deployment create --resource-group examplegroup --template-file clouddrive/templates/azuredeploy.json
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

Después de completar los pasos descritos en este artículo, la plantilla tendrá ahora el siguiente aspecto:

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

