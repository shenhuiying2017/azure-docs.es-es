---
title: "Creación de una aplicación lógica a partir de una plantilla en Azure | Microsoft Docs"
description: "Use una plantilla de Azure Resource Manager para implementar una aplicación lógica vacía para definir flujos de trabajo."
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
ms.date: 01/27/2016
ms.author: LADocs; mandia
ms.openlocfilehash: 161adeacd6da2b15225c8a4ddae171e19e539967
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="create-a-logic-app-using-a-template"></a>Creación de una aplicación lógica mediante una plantilla
Las plantillas proporcionan una forma rápida de utilizar distintos conectores dentro de una aplicación lógica. Logic Apps incluye plantillas de Azure Resource Manager para crear una aplicación lógica que puede utilizarse para definir flujos de trabajo empresariales. Puede especificar qué recursos se implementan y cómo definir parámetros al implementar la aplicación lógica. Puede usar esta plantilla para sus propios escenarios empresariales o personalizarla para satisfacer sus necesidades.

Para obtener más detalles sobre las propiedades de la aplicación lógica, consulte [API de administración de flujos de trabajo de aplicaciones lógicas](https://msdn.microsoft.com/library/azure/mt643788.aspx). 

Para obtener ejemplos de la propia definición, consulte [Creación de definiciones de aplicaciones lógicas](logic-apps-author-definitions.md). 

Para obtener más información sobre la creación de plantillas, consulte [Creación de plantillas de Administrador de recursos de Azure](../azure-resource-manager/resource-group-authoring-templates.md).

Para la plantilla completa, consulte [Plantilla de aplicación lógica](https://github.com/Azure/azure-quickstart-templates/blob/master/101-logic-app-create/azuredeploy.json).

## <a name="what-you-deploy"></a>¿Qué se puede implementar?
Con esta plantilla, implementará una aplicación lógica.

Para ejecutar automáticamente la implementación, seleccione el botón siguiente:  

[![Implementación en Azure](media/logic-apps-arm-provision/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-logic-app-create%2Fazuredeploy.json)

## <a name="parameters"></a>Parámetros
[!INCLUDE [app-service-logic-deploy-parameters](../../includes/app-service-logic-deploy-parameters.md)]

### <a name="testuri"></a>testUri
     "testUri": {
        "type": "string",
        "defaultValue": "http://azure.microsoft.com/en-us/status/feed/"
      }

## <a name="resources-to-deploy"></a>Recursos para implementar
### <a name="logic-app"></a>Aplicación lógica
Crea la aplicación lógica.

Las plantillas utilizan un valor de parámetro para el nombre de aplicación lógica. Establece la ubicación de la aplicación lógica en la misma ubicación que el grupo de recursos. 

Esta definición determinada se ejecuta una vez cada hora y hace ping en la ubicación especificada en el parámetro **testUri** . 

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
            "recurrence": {
              "type": "recurrence",
              "recurrence": {
                "frequency": "Hour",
                "interval": 1
              }
            }
          },
          "actions": {
            "http": {
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


## <a name="commands-to-run-deployment"></a>Comandos para ejecutar la implementación
[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### <a name="powershell"></a>PowerShell
    New-AzureRmResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json -ResourceGroupName ExampleDeployGroup

### <a name="azure-cli"></a>Azure CLI
    azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json -g ExampleDeployGroup



