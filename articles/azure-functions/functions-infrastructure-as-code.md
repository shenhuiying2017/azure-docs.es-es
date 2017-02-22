---
title: "Automatización de la implementación de recursos para una aplicación de Azure Functions | Microsoft Docs"
description: Aprenda a crear una plantilla de Azure Resource Manager que implemente sus aplicaciones de Azure Functions.
services: Functions
documtationcenter: na
author: mattchenderson
manager: erikre
editor: 
tags: 
keywords: "azure functions, funciones, arquitectura sin servidor, infraestructura como código, azure resource manager"
ms.assetid: 
ms.server: functions
ms.devlang: multiple
ms.topic: 
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 01/23/2017
ms.author: cfowler;glenga
translationtype: Human Translation
ms.sourcegitcommit: 360abaa575e473e18e55d0784730f4bd5635f3eb
ms.openlocfilehash: 979537bfe6b0e14a9208871fc9862661d2fb2e6c


---

# <a name="automate-resource-deployment-for-your-azure-functions-app"></a>Automatización de la implementación de recursos para la aplicación de Azure Functions

Puede usar una plantilla de Azure Resource Manager para implementar una aplicación de Azure Functions. Aprenderá a definir la línea de base de los recursos que necesita una aplicación de Azure Functions y los parámetros que se especifican durante la implementación. Según los [desencadenadores y enlaces](functions-triggers-bindings.md) de la aplicación de Functions, puede que necesite implementar recursos adicionales para conseguir una configuración correcta de la infraestructura como código de la aplicación.

Para más información sobre la creación de plantillas, consulte [Creación de plantillas de Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md).

Para ver ejemplos de plantillas completas, consulte [Create a Consumption plan-based Azure Functions app](https://github.com/Azure/azure-quickstart-templates/blob/052db5feeba11f85d57f170d8202123511f72044/101-function-app-create-dynamic/azuredeploy.json) (Creación de una aplicación de Azure Functions basada en un plan de consumo) y [Create an App Service plan-based Azure Functions app](https://github.com/Azure/azure-quickstart-templates/blob/master/101-function-app-create-dedicated/azuredeploy.json) (Creación de una aplicación de Azure Functions basada en un plan de App Service).

## <a name="required-resources"></a>Recursos necesarios

Puede usar los ejemplos de este artículo para crear una aplicación de Azure Functions de línea de base. Necesitará estos recursos para la aplicación:

* Cuenta de [Azure Storage](../storage/index.md)
* Plan de hospedaje (plan de consumo o plan de Azure App Service)
* Aplicación de Functions (`type`: **Microsoft.Web/Site**, `kind`: **functionapp**)

## <a name="parameters"></a>Parámetros

Puede usar Azure Resource Manager para definir los parámetros de los valores que desea especificar al implementar la plantilla. La sección de **parámetros** de una plantilla contiene todos los valores del parámetro. Defina parámetros para los valores que varíen según el proyecto que vaya a implementar o según el entorno en el que vaya a realizar la implementación.

Las [variables](../azure-resource-manager/resource-group-authoring-templates.md#variables) son útiles para los valores que no cambian en función de una implementación individual, o para parámetros que requieren transformación antes de usarse en una plantilla (por ejemplo, para pasar reglas de validación).

Al definir parámetros, use el campo **allowedValues** para especificar los valores que un usuario puede proporcionar durante la implementación. Use el campo **defaultValue** para asignar un valor al parámetro, si no se proporciona ningún valor durante la implementación.

Una plantilla de Azure Resource Manager usa los siguientes parámetros.

### <a name="appname"></a>appName

El nombre de la aplicación de Azure Functions que desea crear.

```json
"appName": {
    "type": "string"
}
```

### <a name="location"></a>location

Dónde desea implementar la aplicación de Functions.

> [!NOTE]
> El parámetro **defaultValue** se usa para heredar la ubicación del grupo de recursos o cuando no se especifica un valor de parámetro durante una implementación con Powershell o la CLI. Si implementa la aplicación desde Azure Portal, seleccione un valor en el cuadro de lista desplegable del parámetro **allowedValues**.

> [!TIP]
> Para obtener una lista actualizada de las regiones en las que puede usar Azure Functions, consulte [Productos disponibles por región](https://azure.microsoft.com/regions/services/).

```json
"location": {
    "type": "string",
    "allowedValues": [
        "Brazil South",
        "East US",
        "East US 2",
        "Central US",
        "North Central US",
        "South Central US",
        "West US",
        "West US 2"
    ],
    "defaultValue": "[resourceGroup().location]"
}
```

### <a name="sourcecoderepositoryurl-optional"></a>sourceCodeRepositoryURL (opcional)

```json
"sourceCodeRepositoryURL": {
    "type": "string",
    "defaultValue": "",
    "metadata": {
    "description": "Source code repository URL"
}
```

### <a name="sourcecodebranch-optional"></a>sourceCodeBranch (opcional)

```json
    "sourceCodeBranch": {
      "type": "string",
      "defaultValue": "master",
      "metadata": {
        "description": "Source code repository branch"
      }
    }
```

### <a name="sourcecodemanualintegration-optional"></a>sourceCodeManualIntegration (opcional)

```json
"sourceCodeManualIntegration": {
    "type": "bool",
    "defaultValue": false,
    "metadata": {
        "description": "Use 'true' if you are deploying from the base repo. Use 'false' if you are deploying from your own fork. If you use 'false', make sure that you have Administrator rights in the repo. If you get an error, manually add GitHub integration to another web app, to associate a GitHub access token with your Azure subscription."
    }
}
```

## <a name="variables"></a>Variables

Las plantillas de Azure Resource Manager usan variables para incorporar parámetros, por lo que puede usar una configuración más específica en la plantilla.

En el ejemplo siguiente, para cumplir con los [requisitos de nomenclatura](../storage/storage-create-storage-account.md#create-a-storage-account) de la cuenta de Azure Storage, se usan variables para aplicar [funciones de plantilla de Azure Resource Manager](../azure-resource-manager/resource-group-template-functions.md) para convertir el valor **appName** que hemos escrito a minúsculas.

```json
"variables": {
    "lowerSiteName": "[toLower(parameters('appName'))]",
    "storageAccountName": "[concat(variables('lowerSiteName'))]"
}
```

## <a name="resources-to-deploy"></a>Recursos para implementar

### <a name="storage-account"></a>Cuenta de almacenamiento

Se necesita una cuenta de Azure Storage para una aplicación de Azure Functions.

```json
{
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2015-05-01-preview",
    "location": "[variables('storageLocation')]",
    "properties": {
        "accountType": "[variables('storageAccountType')]"
    }
}
```

### <a name="hosting-plan-consumption-vs-app-service"></a>Plan de hospedaje: plan de consumo frente a plan de App Service

En algunos escenarios, es posible que desee que la plataforma escale las funciones a petición, lo que también se denomina escalado de forma completamente administrada (mediante el uso de un plan de hospedaje de consumo). O bien, puede elegir el escalado administrado por el usuario para sus funciones. En el escalado administrado por el usuario, las funciones se ejecutan de forma ininterrumpida en hardware dedicado (mediante el uso de un plan de hospedaje de App Service). El número de instancias se puede establecer de forma manual o automática. La elección del plan de hospedaje podría basarse en las características disponibles en el plan o en el diseño según el coste. Para más información sobre planes de hospedaje, consulte [Escalado de Azure Functions](functions-scale.md).

#### <a name="consumption-plan"></a>Plan de consumo

```json
{
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2015-04-01",
    "name": "[variables('hostingPlanName')]",
    "location": "[resourceGroup().location]",
    "properties": {
        "name": "[variables('hostingPlanName')]",
        "computeMode": "Dynamic",
        "sku": "Dynamic"
    }
}
```

#### <a name="app-service-plan"></a>Plan del Servicio de aplicaciones

```json
{
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2015-04-01",
    "name": "[variables('hostingPlanName')]",
    "location": "[resourceGroup().location]",
    "properties": {
        "name": "[variables('hostingPlanName')]",
        "sku": "[parameters('sku')]",
        "workerSize": "[parameters('workerSize')]",
        "hostingEnvironment": "",
        "numberOfWorkers": 1
    }
}
```

### <a name="functions-app-a-site"></a>Aplicación de Functions (un sitio)

Después de seleccionar una opción de escalado, cree una aplicación de Functions. La aplicación es el contenedor que contiene todas las funciones.

Una aplicación de Functions tiene muchos recursos secundarios que puede usar en la implementación, incluido la configuración de la aplicación y las opciones de control del código fuente. También puede decidir quitar el recurso secundario **sourcecontrols** y usar otra [opción de implementación](functions-continuous-deployment.md) en su lugar.

> [!IMPORTANT]
> Es importante comprender cómo se implementan los recursos en Azure para asegurarse de que crea una configuración correcta de infraestructura como código de la aplicación al usar Azure Resource Manager. En el siguiente ejemplo, se aplican configuraciones de nivel superior mediante **siteConfig**. Es importante establecer estas configuraciones en un nivel superior porque transmiten información al motor de implementación y en tiempo de ejecución de Azure Functions. Se necesita información de nivel superior antes de aplicar el recurso secundario **sourcecontrols/web**. Aunque es posible configurar estas opciones en el recurso de nivel secundario **config/appSettings**, en algunos escenarios, debe implementar las funciones y aplicaciones de Functions *antes de* que se aplique **config/appSettings**. En esos casos, por ejemplo, en [Logic Apps](../logic-apps/index.md), las funciones son una dependencia de otro recurso.

```json
{
  "apiVersion": "2015-08-01",
  "name": "[parameters('appName')]",
  "type": "Microsoft.Web/sites",
  "kind": "functionapp",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Web/serverfarms', parameters('appName'))]"
  ],
  "properties": {
     "serverFarmId": "[variables('appServicePlanName')]",
     "siteConfig": {
        "alwaysOn": true,
        "appSettings": [
            { "name": "FUNCTIONS_EXTENSION_VERSION", "value": "~1" },
            { "name": "Project", "value": "src" }
        ]
     }
  },
  "resources": [
     {
        "apiVersion": "2015-08-01",
        "name": "appsettings",
        "type": "config",
        "dependsOn": [
          "[resourceId('Microsoft.Web/Sites', parameters('appName'))]",
          "[resourceId('Microsoft.Web/Sites/sourcecontrols', parameters('appName'), 'web')]",
          "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
        ],
        "properties": {
          "AzureWebJobsStorage": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]",
          "AzureWebJobsDashboard": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
        }
     },
     {
          "apiVersion": "2015-08-01",
          "name": "web",
          "type": "sourcecontrols",
          "dependsOn": [
            "[resourceId('Microsoft.Web/sites/', parameters('appName'))]"
          ],
          "properties": {
            "RepoUrl": "[parameters('sourceCodeRepositoryURL')]",
            "branch": "[parameters('sourceCodeBranch')]",
            "IsManualIntegration": "[parameters('sourceCodeManualIntegration')]"
          }
     }
  ]
}
```
> [!TIP]
> Esta plantilla usa los valores de configuración de aplicación [Project](https://github.com/projectkudu/kudu/wiki/Customizing-deployments#using-app-settings-instead-of-a-deployment-file), que determina el directorio base en el que el motor de implementación de Functions (Kudu) va a buscar código que se pueda implementar. En nuestro repositorio, nuestras funciones están en una subcarpeta de la carpeta **src**. Por tanto, en el ejemplo anterior, se establece el valor de configuración de la aplicación en `src`. Si las funciones se encuentran en la raíz del repositorio, o si no va a implementar desde el control de código fuente, puede quitar este valor de configuración de la aplicación.

## <a name="deploy-your-template"></a>Implementación de la plantilla

* [PowerShell](../azure-resource-manager/resource-group-template-deploy.md)
* [CLI de Azure](../azure-resource-manager/resource-group-template-deploy-cli.md)
* [Portal de Azure](../azure-resource-manager/resource-group-template-deploy-portal.md)
* [API DE REST](../azure-resource-manager/resource-group-template-deploy-rest.md)

### <a name="deploy-to-azure-button"></a>Botón Implementación en Azure

Reemplace ```<url-encoded-path-to-azuredeploy-json>``` por una versión [codificada de la URL](https://www.bing.com/search?q=url+encode) de la ruta de acceso sin formato del archivo `azuredeploy.json` en GitHub.

#### <a name="markdown"></a>Markdown

```markdown
[![Deploy to Azure](http://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/<url-encoded-path-to-azuredeploy-json>)
```

#### <a name="html"></a>HTML

```html
<a href="https://portal.azure.com/#create/Microsoft.Template/uri/<url-encoded-path-to-azuredeploy-json>" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"></a>
```

## <a name="next-steps"></a>Pasos siguientes

Aprenda a desarrollar y configurar Azure Functions.

* [Azure Functions developer reference](functions-reference.md)
* [Configuración de Azure Function App](functions-how-to-use-azure-function-app-settings.md)
* [Creación de su primera función de Azure](functions-create-first-azure-function.md)



<!--HONumber=Feb17_HO1-->


