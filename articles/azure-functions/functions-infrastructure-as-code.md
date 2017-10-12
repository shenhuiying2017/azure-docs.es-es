---
title: "Automatización de la implementación de recursos para una aplicación de función en Azure Functions | Microsoft Docs"
description: "Obtenga información sobre cómo crear una plantilla de Azure Resource Manager que implemente su aplicación de función."
services: Functions
documtationcenter: na
author: lindydonna
manager: cfowler
editor: 
tags: 
keywords: "azure functions, funciones, arquitectura sin servidor, infraestructura como código, azure resource manager"
ms.assetid: d20743e3-aab6-442c-a836-9bcea09bfd32
ms.server: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/25/2017
ms.author: glenga
ms.openlocfilehash: 2fee04ee11210b9081fc7edb64da1f0210f1bd06
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="automate-resource-deployment-for-your-function-app-in-azure-functions"></a>Automatización de la implementación de recursos para una aplicación de función en Azure Functions

Puede usar una plantilla de Azure Resource Manager para implementar una aplicación de función. En este artículo se describen los recursos y los parámetros necesarios para hacerlo. Es posible que tenga que implementar recursos adicionales, dependiendo de los [desencadenadores y enlaces](functions-triggers-bindings.md) de la aplicación de función.

Para más información sobre la creación de plantillas, consulte [Creación de plantillas de Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md).

Para obtener las plantillas de ejemplo, vea:
- [Aplicación de función en el plan de consumo]
- [Aplicación de función en el plan de App Service]

## <a name="required-resources"></a>Recursos necesarios

Una aplicación de función requiere estos recursos:

* Una cuenta de [Azure Storage](../storage/index.yml).
* Un plan de hospedaje (plan de consumo o plan de App Service).
* Una aplicación de función. 

### <a name="storage-account"></a>Cuenta de almacenamiento

Se necesita una cuenta de Azure Storage para una aplicación de función. Se necesita una cuenta de uso general que admita blobs, tablas, colas y archivos. Para más información, vea [Requisitos de la cuenta de almacenamiento de Azure Functions](functions-create-function-app-portal.md#storage-account-requirements).

```json
{
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2015-06-15",
    "location": "[resourceGroup().location]",
    "properties": {
        "accountType": "[parameters('storageAccountType')]"
    }
}
```

Además, las propiedades `AzureWebJobsStorage` y `AzureWebJobsDashboard` deben especificarse como valores de la aplicación en la configuración del sitio. El tiempo de ejecución de Azure Functions usa la cadena de conexión `AzureWebJobsStorage` para crear colas internas. La cadena de conexión `AzureWebJobsDashboard` se usa para registrar en el almacenamiento de tablas de Azure y alimentar la pestaña **Monitor** en el portal.

Estas propiedades se especifican en la colección `appSettings` del objeto `siteConfig`:

```json
"appSettings": [
    {
        "name": "AzureWebJobsStorage",
        "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
    },
    {
        "name": "AzureWebJobsDashboard",
        "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
    }
```    

### <a name="hosting-plan"></a>Plan de hospedaje

La definición del plan de hospedaje varía, dependiendo de si se usa un plan de consumo o un plan de App Service. Vea [Implementar una aplicación de función en el plan de consumo](#consumption) e [Implementar una aplicación de función en el plan de App Service](#app-service-plan).

### <a name="function-app"></a>Aplicación de función

El recurso de aplicación de función se define mediante un recurso de tipo **Microsoft.Web/Site** y tipo **functionapp**:

```json
{
    "apiVersion": "2015-08-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",            
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ]
```

<a name="consumption"></a>

## <a name="deploy-a-function-app-on-the-consumption-plan"></a>Implementar una aplicación de función en el plan de consumo

Puede ejecutar una aplicación de función en dos modos diferentes: el plan de consumo y el plan de App Service. El plan de consumo asigna automáticamente capacidad de proceso cuando se ejecuta el código, se amplía horizontalmente cuando es necesario para gestionar la carga y se reduce horizontalmente cuando no se ejecuta código. Por tanto, no tiene que pagar por máquinas virtuales inactivas y no tiene que reservar capacidad de antemano. Para obtener más información sobre planes de hospedaje, vea [Plan de consumo y plan de App Service de Azure Functions](functions-scale.md).

Para obtener una plantilla de Azure Resource Manager de ejemplo, vea [Aplicación de función en el plan de consumo].

### <a name="create-a-consumption-plan"></a>Crear un plan de consumo

Un plan de consumo es un tipo especial de recurso de "granja de servidores". Se especifica mediante el valor `Dynamic` para las propiedades `computeMode` y `sku`:

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

### <a name="create-a-function-app"></a>Creación de una aplicación de función

Además, un plan de consumo requiere dos configuraciones adicionales en la configuración del sitio: `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` y `WEBSITE_CONTENTSHARE`. Estas propiedades configuran la cuenta de almacenamiento y la ruta de acceso del archivo donde se almacenan el código y la configuración de la aplicación de función.

```json
{
    "apiVersion": "2015-08-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",            
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ],
    "properties": {
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "siteConfig": {
            "appSettings": [
                {
                    "name": "AzureWebJobsDashboard",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
                },
                {
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
                },
                {
                    "name": "WEBSITE_CONTENTAZUREFILECONNECTIONSTRING",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
                },
                {
                    "name": "WEBSITE_CONTENTSHARE",
                    "value": "[toLower(variables('functionAppName'))]"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~1"
                }
            ]
        }
    }
}
```                    

<a name="app-service-plan"></a> 

## <a name="deploy-a-function-app-on-the-app-service-plan"></a>Implementar una aplicación de función en el plan de App Service

En el plan de App Service, la aplicación de función se ejecuta en máquinas virtuales dedicadas en las SKU de los niveles Básico, Estándar y Premium, de un modo similar a las aplicaciones web. Para más información acerca del funcionamiento del plan de App Service, consulte [Introducción detallada sobre los planes de Azure App Service](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md). 

Para obtener una plantilla de Azure Resource Manager de ejemplo, vea [Aplicación de función en el plan de App Service].

### <a name="create-an-app-service-plan"></a>Creación de un plan del Servicio de aplicaciones

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

### <a name="create-a-function-app"></a>Creación de una aplicación de función 

Después de seleccionar una opción de escalado, cree una aplicación de función. La aplicación es el contenedor que contiene todas las funciones.

Una aplicación de función tiene muchos recursos secundarios que puede usar en la implementación, incluidas la configuración de la aplicación y las opciones de control del código fuente. Es posible que también elija quitar el recurso secundario **sourcecontrols** y usar otra [opción de implementación](functions-continuous-deployment.md) en su lugar.

> [!IMPORTANT]
> Para implementar la aplicación de forma correcta mediante Azure Resource Manager, es importante comprender cómo se implementan los recursos en Azure. En el siguiente ejemplo, se aplican configuraciones de nivel superior mediante **siteConfig**. Es importante establecer estas configuraciones en un nivel superior porque transmiten información al motor de implementación y en tiempo de ejecución de Functions. Se necesita información de nivel superior antes de aplicar el recurso secundario **sourcecontrols/web**. Aunque es posible configurar estas opciones en el recurso de nivel secundario **config/appSettings**, en algunos casos debe implementar la aplicación de función *antes de* que se aplique **config/appSettings**. Por ejemplo, cuando se usan funciones con [Logic Apps](../logic-apps/index.md), las funciones son una dependencia de otro recurso.

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
> En esta plantilla se usa el valor de configuración de aplicación [Project](https://github.com/projectkudu/kudu/wiki/Customizing-deployments#using-app-settings-instead-of-a-deployment-file), que establece el directorio base en el que el motor de implementación de Functions (Kudu) busca código que se pueda implementar. En nuestro repositorio, nuestras funciones están en una subcarpeta de la carpeta **src**. Por tanto, en el ejemplo anterior, se establece el valor de configuración de la aplicación en `src`. Si las funciones se encuentran en la raíz del repositorio, o si no va a implementar desde el control de código fuente, puede quitar este valor de configuración de la aplicación.

## <a name="deploy-your-template"></a>Implementación de la plantilla

Puede usar cualquiera de los siguientes métodos para implementar la plantilla:

* [PowerShell](../azure-resource-manager/resource-group-template-deploy.md)
* [CLI de Azure](../azure-resource-manager/resource-group-template-deploy-cli.md)
* [Portal de Azure](../azure-resource-manager/resource-group-template-deploy-portal.md)
* [API DE REST](../azure-resource-manager/resource-group-template-deploy-rest.md)

### <a name="deploy-to-azure-button"></a>Botón Implementación en Azure

Reemplace ```<url-encoded-path-to-azuredeploy-json>``` por una versión [codificada de la URL](https://www.bing.com/search?q=url+encode) de la ruta de acceso sin formato del archivo `azuredeploy.json` en GitHub.

A continuación se muestra un ejemplo que usa Markdown:

```markdown
[![Deploy to Azure](http://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/<url-encoded-path-to-azuredeploy-json>)
```

A continuación se muestra un ejemplo que usa HTML:

```html
<a href="https://portal.azure.com/#create/Microsoft.Template/uri/<url-encoded-path-to-azuredeploy-json>" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"></a>
```

## <a name="next-steps"></a>Pasos siguientes

Aprenda a desarrollar y configurar Azure Functions.

* [Referencia para desarrolladores de Funciones de Azure](functions-reference.md)
* [Configuración de aplicaciones de función de Azure](functions-how-to-use-azure-function-app-settings.md)
* [Creación de su primera función de Azure](functions-create-first-azure-function.md)

<!-- LINKS -->

[Aplicación de función en el plan de consumo]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-function-app-create-dynamic/azuredeploy.json
[Aplicación de función en el plan de App Service]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-function-app-create-dedicated/azuredeploy.json
