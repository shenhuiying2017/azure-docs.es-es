---
title: "Creación de una cuenta de Experimentación de Azure Machine Learning con una plantilla de Azure Resource Manager | Microsoft Docs"
description: "En este artículo se proporciona un ejemplo para crear una cuenta de Experimentación de Azure Machine Learning mediante una plantilla de Azure Resource Manager."
services: machine-learning
author: ahgyger
ms.author: ahgyger
manager: haining
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 11/14/2017
ms.openlocfilehash: c7f4bf0fed35ffb2c03e8b983260c6093032fe79
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/08/2017
---
# <a name="configure-the-azure-machine-learning-experimentation-service"></a>Configuración del servicio Experimentación de Azure Machine Learning

## <a name="overview"></a>Información general
La cuenta del servicio Experimentación de Azure Machine Learning, el área de trabajo y el proyecto son recursos de Azure. Por lo tanto, se pueden implementar con plantillas de Resource Manager. Las plantillas de Resource Manager son archivos JSON que definen los recursos que necesita para implementar la solución. Para entender los conceptos asociados a la implementación y administración de sus soluciones de Azure, consulte [Introducción a Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).

## <a name="deploy-a-template"></a>Implementación de una plantilla
Para implementar una plantilla solamente es necesario realizar un par de pasos en la interfaz de la línea de comandos de Azure o en Azure Portal.

### <a name="deploy-a-template-from-the-command-line"></a>Implementación de una plantilla desde la línea de comandos
Mediante la interfaz de la línea de comandos, puede implementar una plantilla en un grupo de recursos existente con un solo comando.
Consulte la siguiente información sobre cómo crear una plantilla.

```sh
# Login and validate your are in the right subscription context
az login

# Create a new resource group (you can use an existing one)
az group create --name <resource group name> --location <supported Azure region>
az group deployment create -n testdeploy -g <resource group name> --template-file <template-file.json> --parameters <parameters.json>
```

### <a name="deploy-a-template-from-the-azure-portal"></a>Implementación de una plantilla desde Azure Portal
Si lo prefiere, también usar Azure Portal para crear e implementar una plantilla. Haga lo siguiente:

1. Navegue hasta [Azure Portal](https://portal.azure.com).
2. Seleccione **Todos los servicios** y busque "plantillas".
3. Seleccione **Plantillas**.
4. Haga clic en **+ Agregar** y copie la información de la plantilla. 
5. Seleccione la plantilla que creó en el paso 4 y haga clic en **Implementar**.


## <a name="create-a-template-from-an-existing-azure-resource-in-the-azure-portal"></a>Creación de una plantilla a partir de un recurso de Azure existente en Azure Portal
Si ya tiene una cuenta de Experimentación de Azure Machine Learning disponible, en [Azure Portal](https://portal.azure.com), puede generar una plantilla a partir de ese recurso. 

1. Navegue a una cuenta de Experimentación de Azure en [Azure Portal](https://portal.azure.com).
2. En **Configuración**, haga clic en **Script de automatización**.
3. Descargue la plantilla. 

Como alternativa, puede editar manualmente los archivos de plantilla. Vea lo siguiente para obtener un ejemplo de una plantilla y archivos de parámetros. 

### <a name="template-file-example"></a>Ejemplo de archivo de plantilla
Cree un archivo denominado "template-file.json" con el contenido siguiente. 

```json
{
    "contentVersion": "1.0.0.0",
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "parameters": {
        "accountName": {
            "type": "string",
            "metadata": {
                "description": "Name of the machine learning experimentation team account"
            }
        },
        "location": {
            "type": "string",
            "metadata": {
                "description": "Location of the machine learning experimentation account and other dependent resources."
            }
        },
        "storageAccountSku": {
            "type": "string",
            "defaultValue": "Standard_LRS",
            "metadata": {
                "description": "Sku of the storage account"
            }
        }
    },
    "variables": {
        "mlexpVersion": "2017-05-01-preview",
        "stgResourceId": "[resourceId('Microsoft.Storage/storageAccounts', parameters('accountName'))]"
    },
    "resources": [
        {
            "name": "[parameters('accountName')]",
            "type": "Microsoft.Storage/storageAccounts",
            "location": "[parameters('location')]",
            "apiVersion": "2016-12-01",
            "tags": {
                "mlteamAccount": "[parameters('accountName')]"
            },
            "sku": {
                "name": "[parameters('storageAccountSku')]"
            },
            "kind": "Storage",
            "properties": {
                "encryption": {
                    "services": {
                        "blob": {
                            "enabled": true
                        }
                    },
                    "keySource": "Microsoft.Storage"
                }
            }
        },
        {
            "apiVersion": "[variables('mlexpVersion')]",
            "type": "Microsoft.MachineLearningExperimentation/accounts",
            "name": "[parameters('accountName')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[resourceId('Microsoft.Storage/storageAccounts', parameters('accountName'))]"
            ],
            "properties": {
                "storageAccount": {
                    "storageAccountId": "[variables('stgResourceId')]",
                    "accessKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('accountName')), '2016-12-01').keys[0].value]"
                }
            }
        }
    ]
}
```

### <a name="parameters"></a>parameters 
Cree un archivo con el contenido siguiente y guárdelo como <parameters.json>. 

Hay tres valores que puede cambiar. 
* AccountName: nombre de la cuenta de experimentación.
* Location: una de las regiones de Azure admitidas.
* storageAccountSKU: Azure ML solo admite almacenamiento estándar, no premium. Para más información sobre el almacenamiento, consulte la [introducción al almacenamiento](https://docs.microsoft.com/azure/storage/common/storage-introduction). 

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
     "accountName": {
         "value": "MyExperimentationAccount"
     },
     "location": {
         "value": "eastus2"
     },
     "storageAccountSku": {
         "value": "Standard_LRS"
     }
  }
}
```

## <a name="next-steps"></a>Pasos siguientes
* [Crear e instalar Azure Machine Learning](quickstart-installation.md)
