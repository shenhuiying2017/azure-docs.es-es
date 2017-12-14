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
ms.openlocfilehash: 58f23406f20f6b0e75ec65197e4cebb6e4e788ac
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/08/2017
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
   
## <a name="visual-studio-code"></a>Visual Studio Code

Visual Studio Code proporciona una experiencia de desarrollo local que abarca todas las principales plataformas: Windows, Mac OS y Linux.  Las funciones se pueden ejecutar localmente y también se pueden publicar en Azure. Puede empezar con un proyecto vacío o con un conjunto de funciones de ejemplo.

### <a name="prerequisites"></a>Requisitos previos

* Instale la [versión más reciente de Visual Studio Code](https://code.visualstudio.com/Download). 

* Siga las instrucciones que aparecen en "Instalación de Azure Functions Core Tools" en [Codificación y comprobación de las funciones de Azure Functions en un entorno local](https://docs.microsoft.com/azure/azure-functions/functions-run-local).

    >[!IMPORTANT]
    > Si ya tiene las herramientas multiplataforma de Azure Functions, actualícelas a la última versión disponible.

*  Instale y ejecute la versión 5.2 o posterior del [Emulador de Azure Storage](https://docs.microsoft.com/azure/storage/storage-use-emulator). Como alternativa, puede actualizar el archivo *local.appsettings.json* con cadenas de conexión reales de Azure Storage. 


### <a name="start-with-sample-functions"></a>Empezar con funciones de ejemplo

1. Clone el [repositorio de Durable Functions](https://github.com/Azure/azure-functions-durable-extension.git).
2. Navegue hasta el equipo en la [carpeta de ejemplos de scripts de C#](https://github.com/Azure/azure-functions-durable-extension/tree/master/samples/csx). 
3. Instale la extensión Durable Functions de Azure ejecutando lo siguiente en una ventana del terminal o del símbolo del sistema:

    ```bash
    func extensions install -p Microsoft.Azure.WebJobs.Extensions.DurableTask -v 1.1.0-beta2
    ```
4. Ejecute Emulador de Azure Storage o actualice el archivo *local.appsettings.json* con cadenas de conexión reales de Azure Storage.
3. Abra el proyecto en Visual Studio Code. 
5. Para obtener instrucciones sobre cómo ejecutar el ejemplo, empiece con [Encadenamiento de funciones: Hello Sequence de ejemplo](durable-functions-sequence.md). El ejemplo se puede ejecutar localmente o publicarse en Azure.
6. Inicie el proyecto ejecutando en el terminal o el símbolo del sistema el siguiente comando:
    ```bash
    func host start
    ```

### <a name="start-with-an-empty-project"></a>Empezar con un proyecto vacío
 
1. En el terminal o el símbolo del sistema, navegue hasta la carpeta que va a hospedar la aplicación de función.
2. Instale la extensión Durable Functions de Azure ejecutando lo siguiente en una ventana del terminal o del símbolo del sistema:

    ```bash
    func extensions install -p Microsoft.Azure.WebJobs.Extensions.DurableTask -v 1.1.0-beta2
    ```
3. Para ir al proyecto de Function App, ejecute el siguiente comando:

    ```bash
    func init
    ``` 
4. Ejecute Emulador de Azure Storage o actualice el archivo *local.appsettings.json* con cadenas de conexión reales de Azure Storage.
5. Después, cree una nueva función ejecutando el comando siguiente y siga los pasos del asistente:

    ```bash
    func new
    ```
    >[!IMPORTANT]
    > Actualmente no está disponible la plantilla de función duradera, pero puede empezar con una de las opciones admitidas y, luego, modificar el código. Use para la referencia los ejemplos de [cliente de orquestación](https://github.com/Azure/azure-functions-durable-extension/tree/master/samples/csx/HttpStart), [desencadenador de orquestación](https://github.com/Azure/azure-functions-durable-extension/tree/master/samples/csx/E1_HelloSequence) y [desencadenador de actividad](https://github.com/Azure/azure-functions-durable-extension/tree/master/samples/csx/E1_HelloSequence).

6. Abra la carpeta de proyecto de Visual Studio Code y continúe modificando el código de plantilla. 
7. Inicie el proyecto ejecutando en el terminal o el símbolo del sistema el siguiente comando:
    ```bash
    func host start
    ```

## <a name="azure-portal"></a>Azure Portal

Si lo prefiere, puede usar Azure Portal para el desarrollo con Durable Functions.

### <a name="create-an-orchestrator-function"></a>Creación de una función de orquestador

1. Cree una nueva aplicación de función en [functions.azure.com](https://functions.azure.com/signin).

2. Configure la aplicación de la función para [usar la versión de tiempo de ejecución 2.0](functions-versions.md).

3. Cree una nueva función seleccionando **"Crear su propia función personalizada"**.

4. Cambie el **lenguaje** a **C#**, el **escenario** a **Durable Functions** y seleccione la plantilla **Durable Functions Http Starter - C#**.

5. En **Extensiones no instaladas**, haga clic en **Instalar** para descargar la extensión de NuGet.org. 

6. Una vez completada la instalación, continúe con la creación de una función de cliente de orquestación, **"HttpStart"**, que se crea mediante la plantilla **Durable Functions Http Starter - C#**.

7. Después, cree una función de orquestación **"HelloSequence"** a partir de la plantilla **Durable Functions Orchestrator - C#**.

8. Y se llamará a la última función **"Hello"** de la plantilla **Durable Functions Activity - C#**.

9. Vaya a la función **"HttpStart"** y copie su dirección URL.

10. Use Postman o CURL para llamar a la función duradera. Antes de realizar la prueba, reemplace en la dirección URL **{functionName}** con el nombre de la función de orquestación **HelloSequence**.  No se requiere ningún dato; use el verbo POST. 

    ```bash
    curl -X POST https://{your function app name}.azurewebsites.net/api/orchestrators/HelloSequence
    ```

11. Después, llame al punto de conexión **"statusQueryGetUri"** y vea el estado actual de la función duradera.

    ```json
        {
            "runtimeStatus": "Running",
            "input": null,
            "output": null,
            "createdTime": "2017-12-01T05:37:33Z",
            "lastUpdatedTime": "2017-12-01T05:37:36Z"
        }
    ```

12. Continuar con la llamada al punto de conexión **"statusQueryGetUri"** hasta que el estado cambie a **"Completado"**. 

    ```json
    {
            "runtimeStatus": "Completed",
            "input": null,
            "output": [
                "Hello Tokyo!",
                "Hello Seattle!",
                "Hello London!"
            ],
            "createdTime": "2017-12-01T05:38:22Z",
            "lastUpdatedTime": "2017-12-01T05:38:28Z"
        }
    ```

¡Enhorabuena! La primera función duradera está en funcionamiento en Azure Portal.

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Ejecutar el ejemplo de encadenamiento de funciones](durable-functions-sequence.md)
