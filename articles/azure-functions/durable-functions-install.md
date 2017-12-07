---
title: "Instalación de la extensión Durable Functions y ejemplos: Azure"
description: "Obtenga información acerca de cómo instalar la extensión Durable Functions para Azure Functions, para el desarrollo de Azure Portal o para el desarrollo de Visual Studio."
services: functions
author: cgillum
manager: cfowler
editor: 
tags: 
keywords: 
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/29/2017
ms.author: azfuncdf
ms.openlocfilehash: 03326304e438f3b716c43d268f07d8e22cd15ea3
ms.sourcegitcommit: cf42a5fc01e19c46d24b3206c09ba3b01348966f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/29/2017
---
# <a name="install-the-durable-functions-extension-and-samples-azure-functions"></a>Instalación de la extensión Durable Functions y ejemplos (Azure Functions)

La extensión [Durable Functions](durable-functions-overview.md) para Azure Functions se proporciona en el paquete NuGet [Microsoft.Azure.WebJobs.Extensions.DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask). Este artículo muestra cómo instalar el paquete y un conjunto de ejemplos para los entornos de desarrollo siguientes:

* Visual Studio 2017 (recomendado) 
* Azure Portal

## <a name="visual-studio-2017"></a>Visual Studio 2017

Actualmente, Visual Studio proporciona la mejor experiencia para el desarrollo de aplicaciones que usan Durable Functions.  Las funciones se pueden ejecutar localmente y también se pueden publicar en Azure. Puede empezar con un proyecto vacío o con un conjunto de funciones de ejemplo.

### <a name="prerequisites"></a>Requisitos previos

* Instale la [versión más reciente de Visual Studio](https://www.visualstudio.com/downloads/) (15.3 o posterior). Incluya la carga de trabajo del **desarrollo Azure** en las opciones de configuración.

### <a name="start-with-sample-functions"></a>Empezar con funciones de ejemplo

1. Descargue el [archivo .zip de la aplicación de ejemplo para Visual Studio](https://azure.github.io/azure-functions-durable-extension/files/VSDFSampleApp.zip). No es necesario agregar la referencia de NuGet porque el proyecto de ejemplo ya la tiene.
2. Instale y ejecute la versión 5.2 o posterior del [Emulador de Azure Storage](https://docs.microsoft.com/azure/storage/storage-use-emulator). Como alternativa, puede actualizar el archivo *local.appsettings.json* con cadenas de conexión reales de Azure Storage.
3. Abra el proyecto en Visual Studio 2017. 
4. Para obtener instrucciones sobre cómo ejecutar el ejemplo, empiece con [Encadenamiento de funciones: Hello Sequence de ejemplo](durable-functions-sequence.md). El ejemplo se puede ejecutar localmente o publicarse en Azure.

### <a name="start-with-an-empty-project"></a>Empezar con un proyecto vacío
 
Siga las mismas instrucciones que para empezar con el ejemplo, pero, en lugar de descargar el archivo *.zip*, realice estos pasos:

1. Cree un proyecto de aplicación de función.
2. Agregue la siguiente referencia de paquete de NuGet a su archivo *.csproj*:

   ```xml
   <PackageReference Include="Microsoft.Azure.WebJobs.Extensions.DurableTask" Version="1.0.0-beta" />
   ```

## <a name="azure-portal"></a>Azure Portal

Si lo prefiere, puede usar Azure Portal para el desarrollo con Durable Functions.

### <a name="create-an-orchestrator-function"></a>Creación de una función de orquestador

1. Cree una nueva aplicación de función en [functions.azure.com](https://functions.azure.com/signin).
2. Configure la aplicación de la función para [usar la versión de tiempo de ejecución 2.0](functions-versions.md).
3. Cree una nueva función, seleccionando "C#" para el idioma y "Todo" para el escenario. Luego, seleccione la plantilla **Durable Functions Orchestrator - C#**.
4. En **Extensiones no instaladas**, haga clic en **Instalar** para descargar la extensión de NuGet.org.

### <a name="copy-sample-code-to-the-function-app"></a>Copia de código de ejemplo a la aplicación de función

1. Descargue el archivo [DFSampleApp.zip](https://github.com/Azure/azure-functions-durable-extension/raw/master/docfx/files/DFSampleApp.zip).
2. Descomprima el archivo de ejemplo en `D:\home\site\wwwroot` en la aplicación de función, mediante Kudu o FTP.

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Ejecutar el ejemplo de encadenamiento de funciones](durable-functions-sequence.md)
