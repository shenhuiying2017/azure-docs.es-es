---
title: "Automatización de la implementación de recursos para Azure Functions | Microsoft Docs"
description: "Aprenda a crear una plantilla de Azure Resource Manager que implemente sus aplicaciones de función en Microsoft Azure."
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
ms.sourcegitcommit: d11ef8865d21dfe4e56bcb6fa08ce58c53f3d309
ms.openlocfilehash: 7c55a1d34df71c2c958f42f43810fbce7bd74e5d


---

# <a name="automate-the-deployment-of-resources-for-your-azure-functions-app"></a>Automatización de la implementación de recursos para la aplicación de Azure Functions

En este tema, aprenderá a crear una plantilla de Azure Resource Manager que implementa una aplicación de función. Aprenderá a definir la línea de base de los recursos necesarios para una función de Azure y los parámetros que se especifican cuando se ejecuta la implementación. Según los [desencadenadores y enlaces](functions-triggers-bindings.md) que se usen en la función, puede que sea necesario implementar recursos adicionales para incluir toda la aplicación como infraestructura como código.

Para más información sobre la creación de plantillas, consulte [Authoring Azure Resource Manager Templates](../azure-resource-manager/resource-group-authoring-templates.md) (Creación de plantillas de Azure Resource Manager).

Para ver ejemplos de una plantilla completa, consulte [Create consumption-based Azure Function](https://github.com/Azure/azure-quickstart-templates/blob/052db5feeba11f85d57f170d8202123511f72044/101-function-app-create-dynamic/azuredeploy.json) (Creación de una función de Azure basada en consumo) y/o [Create an App Service Plan based Azure Function](https://github.com/Azure/azure-quickstart-templates/blob/master/101-function-app-create-dedicated/azuredeploy.json) (Creación de una función de Azure basada en un plan de App Service).

## <a name="what-is-deployed"></a>Lo que se implementa

Con los ejemplos siguientes, creará una instancia de Azure Function App de línea de base. Los recursos necesarios para una instancia de Function App son los siguientes:

* Cuenta de [Azure Storage](../storage/index.md)
* Plan de hospedaje (plan de consumo o plan de App Service)
* Function App (Microsoft.Web/sitio de clase **functionapp**)

## <a name="parameters"></a>Parámetros

Con el Administrador de recursos de Azure, se definen los parámetros de los valores que desea especificar al implementar la plantilla. La plantilla incluye una sección denominada Parameters que contiene todos los valores de los parámetros. Debe definir parámetros para aquellos valores que varíen según el proyecto que vaya a implementar o según el entorno en el que vaya a realizar la implementación.

Las [variables](../azure-resource-manager/resource-group-authoring-templates.md#variables) son útiles para valores que no cambian en función de una implementación individual, o para parámetros que requieren transformación antes de usarse en una plantilla (por ejemplo, para pasar reglas de validación).

Al definir parámetros, use el campo **allowedValues** para especificar los valores que un usuario puede proporcionar durante la implementación. Use el campo **defaultValue** para asignar un valor al parámetro, si no se proporciona ningún valor durante la implementación.

Vamos a describir los parámetros de nuestra plantilla.

### <a name="appname"></a>appName

El nombre de la función que quiere crear.

```json
"appName": {
    "type": "string"
}
```

### <a name="location"></a>location

La ubicación en la que se va a implementar Function App.

> [!NOTE]
> El parámetro **defaultValue** se usa para heredar la ubicación del grupo de recursos o cuando no se especifica un valor de parámetro durante una implementación con Powershell o la CLI. Si la implementación es desde el portal, se proporciona un cuadro de lista desplegable para seleccionar de **allowedValues**.

> [!TIP]
> Para obtener una lista actualizada de las regiones en las que está disponible Azure Functions, visite la página [Productos disponibles por región](https://azure.microsoft.com/regions/services/).

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
        "description": "Sourcecode Repo branch"
      }
    }
```

### <a name="sourcecodemanualintegration-optional"></a>sourceCodeManualIntegration (opcional)

```json
"sourceCodeManualIntegration": {
    "type": "bool",
    "defaultValue": false,
    "metadata": {
        "description": "Use 'true' if you are deploying from the base repo, 'false' if you are deploying from your own fork. If you're using 'false', make sure you have admin permissions to the repo. If you get an error, you should add GitHub integration to another web app manually, so that you get a GitHub access token associated with your Azure Subscription."
    }
}
```

## <a name="variables"></a>Variables

Además de parámetros, las plantillas de Azure Resource Manager también tienen un concepto de variables que pueden incorporar parámetros para generar valores más específicos para su uso en la plantilla.

En este ejemplo, puede ver que estamos aprovechando variables para aplicar [funciones de plantilla de Azure Resource Manager](../azure-resource-manager/resource-group-template-functions.md) a fin de tomar el elemento appName proporcionado y convertirlo en minúsculas para garantizar que se cumplen los [requisitos de nomenclatura](../storage/storage-create-storage-account.md#create-a-storage-account) de las cuentas de Azure Storage.

```json
"variables": {
    "lowerSiteName": "[toLower(parameters('appName'))]",
    "storageAccountName": "[concat(variables('lowerSiteName'))]"
}
```

## <a name="resources-to-deploy"></a>Recursos para implementar

### <a name="storage-account"></a>Cuenta de almacenamiento

Una cuenta de almacenamiento de Azure Storage es un recurso necesario en Azure Functions.

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

### <a name="hosting-plan-consumption-vs-app-service-plan"></a>Plan de hospedaje: plan de consumo frente a plan de App Service

Al crear funciones hay escenarios en los que quizás quiera que las funciones se escalen de forma completamente administrada, es decir, a petición por la plataforma (consumo). Como alternativa, puede elegir el escalado administrado por el usuario en el que las funciones se ejecutan 24 horas al día, los 7 días de la semana, en hardware dedicado (plan de App Service) donde el número de instancias se puede configurar automática o manualmente. La decisión de usar un plan u otro podría basarse en las características disponibles en el plan o en el diseño según el coste. Para más información sobre los distintos planes de hospedaje, lea el artículo [Escalado de Azure Functions](functions-scale.md).

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

### <a name="function-app-site"></a>Aplicación de función (sitio)

Cuando haya seleccionado la opción de escalado, es el momento de crear el contenedor, que contendrá todas las funciones, y esto se conoce como Function App.

Una instancia de Function App tiene muchos recursos secundarios en los que puede aprovechar las ventajas de incluir **configuración de aplicación** y **opciones de control del código fuente**. Puede optar por quitar el recurso secundario **sourcecontrols** en favor de otra [opción de implementación](functions-continuous-deployment.md).

> [!IMPORTANT]
> Es importante comprender cómo se implementan los recursos en Azure para asegurarse de que crea una configuración correcta de infraestructura como código de la aplicación al usar Azure Resource Manager. En este ejemplo, observará que existen configuraciones de nivel superior que se aplican mediante **siteConfig**; es importante establecerlas en un nivel superior dado que transmiten significado al entorno de tiempo de ejecución y al motor de implementación de Azure Functions, los cuales son necesarios antes de aplicar el recurso secundario **sourcecontrols/web**. Aunque estas opciones se pueden configurar en el recurso de nivel de secundario **config/appSettings**, hay escenarios en los que Function App y Functions se deben implementar *antes* de aplicar **config/appsettings** dado que las funciones son una dependencia de otro recurso, por ejemplo una [aplicación lógica](../logic-apps/index.md).

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
> En esta plantilla usa la configuración de aplicación [Project](https://github.com/projectkudu/kudu/wiki/Customizing-deployments#using-app-settings-instead-of-a-deployment-file), que determina el directorio base en el que el motor de implementación de funciones (Kudu) va a buscar código que se pueda implementar. En este ejemplo, hemos configurado este valor en `src` ya que nuestro repositorio de GitHub contiene una carpeta `src` en la que nuestras funciones son un elemento secundario. Si tiene un repositorio en el que las funciones están directamente en su raíz o no va a implementar desde el control de código fuente, se puede quitar esta configuración de aplicación.

## <a name="deploying-your-template"></a>Implementación de la plantilla

* [PowerShell](../azure-resource-manager/resource-group-template-deploy.md)
* [CLI](../azure-resource-manager/resource-group-template-deploy-cli.md)
* [Portal](../azure-resource-manager/resource-group-template-deploy-portal.md)
* [API DE REST](../azure-resource-manager/resource-group-template-deploy-rest.md)

### <a name="deploy-to-azure-button"></a>Botón de implementación en Azure

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

Ahora que tiene la posibilidad de implementar una aplicación de función a partir de código, aproveche la oportunidad de aprender más sobre cómo desarrollar y configurar Azure Functions:

* [Referencia para desarrolladores de Funciones de Azure](functions-reference.md)
* [Configuración de Azure Function App](functions-how-to-use-azure-function-app-settings.md)
* [Creación de su primera función de Azure](functions-create-first-azure-function.md)


<!--HONumber=Jan17_HO4-->


