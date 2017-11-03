---
title: "Creación de aplicaciones lógicas a partir de plantillas de Azure Resource Manager | Microsoft Docs"
description: "Creación e implementación de flujos de trabajo de aplicaciones lógicas con plantillas de Azure Resource Manager"
services: logic-apps
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
ms.assetid: 7574cc7c-e5a1-4b7c-97f6-0cffb1a5d536
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2017
ms.author: LADocs; mandia
ms.openlocfilehash: e30ed8b1b8e2241bbebab1d7c5f337fabf37e1dd
ms.sourcegitcommit: cf4c0ad6a628dfcbf5b841896ab3c78b97d4eafd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/21/2017
---
# <a name="create-and-deploy-logic-apps-with-azure-resource-manager-templates"></a>Creación e implementación de aplicaciones lógicas con plantillas de Azure Resource Manager

Azure Logic Apps proporciona las plantillas de Azure Resource Manager que puede usar no solo para crear aplicaciones lógicas para automatizar los flujos de trabajo, sino también para definir los recursos y parámetros que se usan para la implementación. Puede usar esta plantilla para sus propios escenarios empresariales o personalizar para satisfacer sus requisitos. Obtenga más información sobre la [plantilla de Resource Manager para aplicaciones lógicas](https://github.com/Azure/azure-quickstart-templates/blob/master/101-logic-app-create/azuredeploy.json) y la [estructura y sintaxis de las plantillas de Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md).

## <a name="define-the-logic-app"></a>Definición de la aplicación lógica

Esta definición de ejemplo de una aplicación lógica se ejecuta una vez cada hora y hace ping en la ubicación especificada en el parámetro `testUri`.
La plantilla usa valores de parámetro para el nombre de la aplicación lógica (```logicAppName```) y la ubicación para hacer ping para realizar pruebas (```testUri```). Obtenga más información sobre la [definición de estos parámetros en la plantilla](#define-parameters). La plantilla también establece la ubicación de la aplicación lógica en la misma ubicación que el grupo de recursos de Azure. 

``` json
{
    "type": "Microsoft.Logic/workflows",
    "apiVersion": "2016-06-01",
    "name": "[parameters('logicAppName')]",
    "location": "[resourceGroup().location]",
    "tags": {
        "displayName": "LogicApp"
    },
    "properties": {
        "definition": {
            "$schema": "http://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
            "contentVersion": "1.0.0.0",
            "parameters": {
                "testURI": {
                "type": "string",
                "defaultValue": "[parameters('testUri')]"
                }
            },
            "triggers": {
                "Recurrence": {
                    "type": "Recurrence",
                    "recurrence": {
                        "frequency": "Hour",
                        "interval": 1
                    }
                }
            },
            "actions": {
                "Http": {
                    "type": "Http",
                    "inputs": {
                        "method": "GET",
                        "uri": "@parameters('testUri')"
                    },
                    "runAfter": {}
                }
            },
            "outputs": {}
        },
        "parameters": {}
    }
}
``` 

<a name="define-parameters"></a>

### <a name="define-parameters"></a>Definición de parámetros

[!INCLUDE [app-service-logic-deploy-parameters](../../includes/app-service-logic-deploy-parameters.md)]

Estas son las descripciones de los parámetros de la plantilla:

| Parámetro | Descripción | Ejemplo de definición JSON | 
| --------- | ----------- | ----------------------- | 
| `logicAppName` | Define el nombre de la aplicación lógica que la plantilla crea. | "logicAppName": { "type": "string", "metadata": { "description": "myExampleLogicAppName" } } |
| `testUri` | Define la ubicación para hacer ping para realizar pruebas. | "testUri": { "type": "string", "defaultValue": "http://azure.microsoft.com/status/feed/"} | 
||||

Obtenga más información sobre la [definición y las propiedades de la API de REST para el flujo de trabajo de Logic Apps](https://docs.microsoft.com/rest/api/logic/workflows) y la [creación de definiciones de aplicaciones lógicas con JSON](logic-apps-author-definitions.md).

## <a name="deploy-logic-apps-automatically"></a>Implementación automática de aplicaciones lógicas

Para crear e implementar automáticamente una aplicación lógica en Azure, elija **Implementación en Azure** aquí:

[![Implementación en Azure](./media/logic-apps-create-deploy-azure-resource-manager-templates/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-logic-app-create%2Fazuredeploy.json)

Con esta acción inicia sesión en Azure Portal, donde puede proporcionar los detalles de la aplicación lógica y hacer cualquier cambio en la plantilla o en los parámetros. Por ejemplo, Azure Portal le pedirá estos detalles:

* El nombre de la suscripción a Azure
* El grupo de recursos que desea usar
* La ubicación de la aplicación lógica
* Un nombre para la aplicación lógica
* Un URI de prueba
* La aceptación de los términos y condiciones especificados

## <a name="deploy-logic-apps-with-commands"></a>Implementación de aplicaciones lógicas con comandos

[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### <a name="powershell"></a>PowerShell

```
New-AzureRmResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json -ResourceGroupName ExampleDeployGroup
``` 

### <a name="azure-cli"></a>CLI de Azure

```
azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json -g ExampleDeployGroup
```

## <a name="get-support"></a>Obtención de soporte técnico

* Si tiene alguna duda, visite el [foro de Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Para enviar ideas sobre características o votar sobre ellas, visite el [sitio de comentarios de los usuarios de Logic Apps](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Supervisión de aplicaciones lógicas](../logic-apps/logic-apps-monitor-your-logic-apps.md)