---
title: "Implementación de una aplicación web vinculada a un repositorio de GitHub | Microsoft Docs"
description: "Use una plantilla de Administrador de recursos de Azure para implementar una aplicación web que contenga un proyecto de un repositorio de GitHub."
services: app-service
documentationcenter: 
author: cephalin
manager: erikre
editor: 
ms.assetid: 32739607-85fe-43c8-a4dc-1feb46d93a4d
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/27/2016
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 0921b01bc930f633f39aba07b7899ad60bd6a234
ms.openlocfilehash: 92408d6358516cdb2cea068553757c036143e955
ms.lasthandoff: 03/01/2017


---
# <a name="deploy-a-web-app-linked-to-a-github-repository"></a>Implementación de una aplicación web vinculada a un repositorio de GitHub
En este tema, aprenderá a crear una plantilla de Administrador de recursos de Azure que implementa una aplicación web que está vinculada a un proyecto en un repositorio de GitHub. Aprenderá a definir los recursos que se implementan y los parámetros que se especifican cuando se ejecuta la implementación. Puede usar esta plantilla para sus propias implementaciones o personalizarla para satisfacer sus necesidades.

Para obtener más información sobre la creación de plantillas, consulte [Creación de plantillas de Administrador de recursos de Azure](../azure-resource-manager/resource-group-authoring-templates.md).

Para la plantilla completa, consulte [Aplicación web vinculada a la plantilla de GitHub](https://github.com/Azure/azure-quickstart-templates/blob/master/201-web-app-github-deploy/azuredeploy.json).

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="what-you-will-deploy"></a>Lo que implementará
Con esta plantilla, implementará una aplicación web que contiene el código de un proyecto en GitHub.

Para ejecutar automáticamente la implementación, haga clic en el botón siguiente:

[![Implementación en Azure](./media/app-service-web-arm-from-github-provision/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-web-app-github-deploy%2Fazuredeploy.json)

## <a name="parameters"></a>Parámetros
[!INCLUDE [app-service-web-deploy-web-parameters](../../includes/app-service-web-deploy-web-parameters.md)]

### <a name="repourl"></a>repoURL
La dirección URL del repositorio GitHub que contiene el proyecto que se va a implementar. Este parámetro contiene un valor predeterminado, pero la finalidad de este valor es únicamente mostrarle cómo proporcionar la dirección URL del repositorio. Puede usar este valor cuando pruebe la plantilla, pero lo más seguro es que quiera proporcionar la dirección URL de su propio repositorio cuando trabaje con la plantilla.

    "repoURL": {
        "type": "string",
        "defaultValue": "https://github.com/davidebbo-test/Mvc52Application.git"
    }

### <a name="branch"></a>branch
La rama del repositorio que se va a usar al implementar la aplicación. El valor predeterminado es maestro, pero puede proporcionar el nombre de cualquier rama del repositorio que desee implementar.

    "branch": {
        "type": "string",
        "defaultValue": "master"
    }

## <a name="resources-to-deploy"></a>Recursos para implementar
[!INCLUDE [app-service-web-deploy-web-host](../../includes/app-service-web-deploy-web-host.md)]

### <a name="web-app"></a>Aplicación web
Crea la aplicación web que está vinculada al proyecto en GitHub. 

Especifique el nombre de la aplicación web a través del parámetro **siteName** y la ubicación de la aplicación web a través del parámetro **siteLocation**. En el elemento **dependsOn** , la plantilla define la aplicación web como dependiente del plan de hospedaje de servicio. Puesto que depende del plan de hospedaje, la aplicación web no se crea hasta que ha terminado de crearse el plan de hospedaje. El elemento **dependsOn** solo se usa para especificar el orden de implementación. Si no marca la aplicación web como dependientes en el plan de hospedaje, el Administrador de recursos de Azure intentará crear ambos recursos al mismo tiempo y puede recibir un error si la aplicación web se crea antes que el plan de hospedaje.

La aplicación web también tiene un recurso secundario que se define en la sección sobre **recursos** posterior. Este recurso secundario define el control de código fuente para el proyecto implementado con la aplicación web. En esta plantilla, el control de código fuente está vinculado a un repositorio de GitHub determinado. El repositorio de GitHub se define con el código **"RepoUrl": "https://github.com/davidebbo-test/Mvc52Application.git"** Podría codificar de forma rígida la dirección URL del repositorio cuando desee crear una plantilla que implemente de forma repetida un único proyecto y se requiera el número mínimo de parámetros.
En lugar de codificar de forma rígida la dirección URL del repositorio, puede agregar un parámetro para ella y usar ese valor para la propiedad **RepoUrl**.

    {
      "apiVersion": "2015-08-01",
      "name": "[parameters('siteName')]",
      "type": "Microsoft.Web/sites",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
      ],
      "properties": {
        "serverFarmId": "[parameters('hostingPlanName')]"
      },
      "resources": [
        {
          "apiVersion": "2015-08-01",
          "name": "web",
          "type": "sourcecontrols",
          "dependsOn": [
            "[resourceId('Microsoft.Web/Sites', parameters('siteName'))]"
          ],
          "properties": {
            "RepoUrl": "[parameters('repoURL')]",
            "branch": "[parameters('branch')]",
            "IsManualIntegration": true
          }
        }
      ]
    }

## <a name="commands-to-run-deployment"></a>Comandos para ejecutar la implementación
[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### <a name="powershell"></a>PowerShell
    New-AzureRmResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-web-app-github-deploy/azuredeploy.json -siteName ExampleSite -hostingPlanName ExamplePlan -siteLocation "West US" -ResourceGroupName ExampleDeployGroup

### <a name="azure-cli"></a>Azure CLI

    azure group deployment create -g {resource-group-name} --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-web-app-github-deploy/azuredeploy.json

### <a name="azure-cli-20"></a>CLI de Azure 2.0

    az group deployment create -g {resource-group-name} --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-web-app-github-deploy/azuredeploy.json --parameters '@azuredeploy.parameters.json'

> [!NOTE] 
> Para el contenido del archivo JSON de parámetros, consulte [azuredeploy.parameters.json](https://github.com/Azure/azure-quickstart-templates/blob/master/201-web-app-github-deploy/azuredeploy.parameters.json).
>
>


