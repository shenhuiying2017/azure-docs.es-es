---
title: "Creación de su primera función en Linux con la CLI de Azure (versión preliminar) | Microsoft Docs"
description: "Obtenga información sobre cómo crear su primera función de Azure para ejecutarse en una imagen de Linux predeterminada con la CLI de Azure."
services: functions
keywords: 
author: ggailey777
ms.author: glenga
ms.date: 11/15/2017
ms.topic: quickstart
ms.service: functions
ms.custom: mvc
ms.devlang: azure-cli
manager: cfowler
ms.openlocfilehash: d04e2000f2043e8bb11e15f6b9d7fd06ef5b9da3
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/16/2017
---
# <a name="create-your-first-function-running-on-linux-using-the-azure-cli-preview"></a>Creación de su primera función para ejecutarse en Linux con la CLI de Azure (versión preliminar)

Azure Functions permite hospedar sus funciones en Linux en un contenedor de Azure App Service predeterminado. Actualmente, la versión de esta funcionalidad es una versión preliminar. También puede [traer su propio contenedor personalizado](functions-create-function-linux-custom-image.md). 

En este tema de inicio rápido se explica cómo utilizar Azure Functions con la CLI de Azure para crear su primera aplicación de función en Linux hospedada en el contenedor de App Service predeterminado. El propio código de la función se implementa en la imagen desde un repositorio de ejemplo de GitHub.    

Los pasos siguientes se admiten en equipos Mac, Windows o Linux. 

## <a name="prerequisites"></a>Requisitos previos 

Para completar este inicio rápido necesita instalar:

+ Una suscripción de Azure activa.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Si decide instalar y usar la CLI de forma local, para este tema es preciso la CLI de Azure versión 2.0.21 o posterior. Ejecute `az --version` para encontrar la versión que tiene. Si necesita instalarla o actualizarla, consulte [Instalación de la CLI de Azure 2.0]( /cli/azure/install-azure-cli). 

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-linux-app-service-plan"></a>Creación de un plan de App Service de Linux

El hospedaje de Linux para Functions solo se admite actualmente en un plan de App Service. El hospedaje del plan de consumo todavía no es compatible. Para obtener más información sobre el hospedaje, consulte [Comparación de los planes de hospedaje de Azure Functions](functions-scale.md). 

[!INCLUDE [app-service-plan-no-h](../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

## <a name="create-a-function-app-on-linux"></a>Creación de aplicación de función en Linux

Debe tener una aplicación de función para hospedar la ejecución de las funciones en Linux. La aplicación de función proporciona un entorno para la ejecución del código de su aplicación. Le permite agrupar funciones como una unidad lógica para facilitar la administración, la implementación y el uso compartido de recursos. Cree una aplicación de función usando el comando [az functionapp create](/cli/azure/functionapp#create) con un plan de App Service de Linux. 

En el siguiente comando, sustituya un nombre de aplicación de función único donde vea el marcador de posición `<app_name>` y el nombre de la cuenta de almacenamiento para `<storage_name>`. El `<app_name>` se usa como el dominio DNS predeterminado para la Function App y, por ello, el nombre debe ser único en todas las aplicaciones de Azure. El parámetro _deployment-source-url_ es un repositorio de ejemplo de GitHub que contiene una función desencadenada mediante HTTP de tipo "Hola mundo".

```azurecli-interactive
az functionapp create --name <app_name> --storage-account  <storage_name>  --resource-group myResourceGroup \
--plan myAppServicePlan --deployment-source-url https://github.com/Azure-Samples/functions-quickstart-linux
```
Después de que se haya creado la aplicación de función, la CLI de Azure muestra información similar al siguiente ejemplo:

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 1536,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "quickstart.azurewebsites.net",
  "enabled": true,
  "enabledHostNames": [
    "quickstart.azurewebsites.net",
    "quickstart.scm.azurewebsites.net"
  ],
   ....
    // Remaining output has been truncated for readability.
}
```

Dado que `myAppServicePlan` es un plan de Linux, la imagen de Docker integrada se utiliza para crear el contenedor que ejecuta la aplicación de función en Linux. 

>[!NOTE]  
>Actualmente, el repositorio de ejemplo incluye dos archivos de script, [deploy.sh](https://github.com/Azure-Samples/functions-quickstart-linux/blob/master/deploy.sh) y [.deployment](https://github.com/Azure-Samples/functions-quickstart-linux/blob/master/.deployment). El archivo .deployment indica al proceso de implementación para utilizar deploy.sh como el [script de implementación personalizado](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script). En la versión preliminar actual, los scripts son necesarios para implementar la aplicación de función en una imagen de Linux.  

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

[!INCLUDE [functions-quickstart-next-steps-cli](../../includes/functions-quickstart-next-steps-cli.md)]
