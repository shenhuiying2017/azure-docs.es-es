---
title: 'Procedimiento para ejecutar Durable Functions como WebJobs: Azure'
description: Aprenda a programar y configurar Durable Functions para ejecutarlas en WebJobs mediante el SDK de WebJobs.
services: functions
author: tdykstra
manager: cfowler
editor: ''
tags: ''
keywords: ''
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 04/25/2018
ms.author: azfuncdf
ms.openlocfilehash: 3fc84d1492d2855ffa3bb5538226da049a928339
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
ms.locfileid: "33766697"
---
# <a name="how-to-run-durable-functions-as-webjobs"></a>Procedimiento para ejecutar Durable Functions como WebJobs

[Azure Functions](functions-overview.md) y la extensión [Durable Functions](durable-functions-overview.md) están construidos sobre el [SDK de WebJobs](../app-service/web-sites-create-web-jobs.md). `JobHost` en el SDK de WebJobs es el runtime en Azure Functions. Si necesita controlar el comportamiento de `JobHost` de maneras que no son posibles en Azure Functions, puede desarrollar y ejecutar funciones duraderas mediante el SDK de WebJobs. A continuación, puede ejecutar las funciones duraderas en un WebJob de Azure o en cualquier lugar en que se ejecute una aplicación de consola.

El ejemplo de encadenamiento de Durable Functions está disponible en una versión del SDK de WebJobs: descargue o clone el [repositorio de Durable Functions](https://github.com/azure/azure-functions-durable-extension/) y vaya a la carpeta *samples\\webjobssdk\\chaining*.

## <a name="prerequisites"></a>requisitos previos

En este artículo se supone que está familiarizado con los conceptos básicos del SDK de WebJobs, el desarrollo de bibliotecas de clase de C# para Azure Functions y Durable Functions. Si necesita una presentación de estos temas, consulte los siguientes recursos:

* [Introducción al SDK de WebJobs](../app-service/webjobs-sdk-get-started.md)
* [Creación de la primera función mediante Visual Studio](functions-create-your-first-function-visual-studio.md)
* [Durable Functions](durable-functions-sequence.md)

Para completar los pasos de este artículo:

* Instale [Visual Studio 2017](https://docs.microsoft.com/visualstudio/install/) versión 15.6 o posterior con la carga de trabajo **Desarrollo de Azure**.

  Si ya tiene Visual Studio pero no tiene esa carga de trabajo, agréguela mediante la selección de **Herramientas > Obtener herramientas y características**. 

  (Puede usar [Visual Studio Code](https://code.visualstudio.com/) en su lugar, pero algunas de las instrucciones son específicas para Visual Studio).

* Instale y ejecute la versión 5.2 o posterior del [Emulador de Azure Storage](../storage/common/storage-use-emulator.md). Una alternativa consiste en actualizar el archivo *App.config* con una cadena de conexión de Azure Storage.

## <a name="webjobs-sdk-versions"></a>Versiones del SDK de WebJobs

Este artículo explica cómo desarrollar un proyecto del SDK de WebJobs 2.x (equivalente a la versión 1.x de Azure Functions). Para información sobre la versión 3.x, consulte [SDK de WebJobs 3.x](#webjobs-sdk-3x) más adelante en este artículo. 

## <a name="create-console-app"></a>Creación de una aplicación de consola

Un proyecto del SDK de WebJobs es simplemente un proyecto de aplicación de consola con los paquetes de NuGet adecuados instalados.

En el cuadro de diálogo **Nuevo proyecto** de Visual Studio, seleccione **Escritorio clásico de Windows > Aplicación de consola (.NET Framework)**. En el archivo del proyecto, el elemento `TargetFrameworkVersion` debe ser `v4.6.1`.

Visual Studio también tiene una plantilla de proyecto de WebJob, que se puede usar seleccionando **En la nube > WebJob de Azure (.NET Framework)**. Esta plantilla instala varios paquetes, algunos de los cuales puede que no necesite.

## <a name="install-nuget-packages"></a>Instalación de paquetes NuGet

Necesita los paquetes de NuGet para el SDK de WebJobs, los enlaces de núcleo, la plataforma de registro y la extensión Durable Task. Estos son los comandos de la **Consola del administrador de paquetes** para esos paquetes, con los números de versión estable más reciente en la fecha en que se escribió este artículo:

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions -version 2.2.0
Install-Package Microsoft.Extensions.Logging -version 2.0.1
Install-Package Microsoft.Azure.WebJobs.Extensions.DurableTask -version 1.4.0
```

También necesita proveedores de registro. El siguiente comando instala el proveedor de Application Insights y `ConfigurationManager`. `ConfigurationManager` le permite obtener la clave de instrumentación de Application Insights de la configuración de la aplicación.

```powershell
Install-Package Microsoft.Azure.WebJobs.Logging.ApplicationInsights -version 2.2.0
Install-Package System.Configuration.ConfigurationManager -version 4.4.1
```

El comando siguiente instala el proveedor de consola:

```powershell
Install-Package Microsoft.Extensions.Logging.Console -version 2.0.1
```

## <a name="jobhost-code"></a>Código de JobHost

Para utilizar la extensión Durable Functions, llame a `UseDurableTask` en el objeto `JobHostConfiguration` en el método `Main`:

```cs
var config = new JobHostConfiguration();
config.UseDurableTask(new DurableTaskExtension
{
    HubName = "MyTaskHub",
};
```

Para obtener una lista de propiedades que se pueden establecer en el objeto `DurableTaskExtension`, consulte [host.json](functions-host-json.md#durabletask).

El método `Main` también es el lugar donde se configuran los proveedores de registro. En el ejemplo siguiente se configuran la consola y los proveedores de Application Insights.

```cs
static void Main(string[] args)
{
    using (var loggerFactory = new LoggerFactory())
    {
        var config = new JobHostConfiguration();

        config.DashboardConnectionString = "";

        var instrumentationKey =
            ConfigurationManager.AppSettings["APPINSIGHTS_INSTRUMENTATIONKEY"];

        config.LoggerFactory = loggerFactory
            .AddApplicationInsights(instrumentationKey, null)
            .AddConsole();

        config.UseTimers();
        config.UseDurableTask(new DurableTaskExtension
        {
            HubName = "MyTaskHub",
        });
        var host = new JobHost(config);
        host.RunAndBlock();
    }
}
```

## <a name="functions"></a>Functions

Hay algunas diferencias en el código que se escribe para las funciones del SDK de WebJobs en comparación al que se escribe para el servicio Azure Functions.

El SDK de WebJobs no es compatible con las siguientes características de Azure Functions:

* [Atributo FunctionName](#functionname-attribute)
* [Desencadenador HTTP](#http-trigger)
* [API de administración HTTP de Durable Functions](#http-management-api)

### <a name="functionname-attribute"></a>Atributo FunctionName

En un proyecto del SDK de WebJobs, el nombre del método de una función es el nombre de la función. El atributo `FunctionName` solo se usa en Azure Functions.

### <a name="http-trigger"></a>Desencadenador HTTP

El SDK de WebJobs no tiene un desencadenador HTTP. El cliente de orquestación del proyecto de ejemplo utiliza un desencadenador de temporizador:

```cs
public static async Task CronJob(
    [TimerTrigger("0 */2 * * * *")] TimerInfo timer,
    [OrchestrationClient] DurableOrchestrationClient client,
    ILogger logger)
{
  ...
}
```

### <a name="http-management-api"></a>API de administración HTTP

Dado que no tiene desencadenador HTTP, el SDK de WebJobs no tiene [API de administración HTTP](durable-functions-http-api.md).

En un proyecto del SDK de WebJobs, puede llamar a métodos del objeto del cliente de orquestación en lugar de enviar solicitudes HTTP. Los métodos siguientes se corresponden a las tres tareas que puede realizar con la API de administración HTTP:

* `GetStatusAsync`
* `RaiseEventAsync`
* `TerminateAsync`

La función del cliente de orquestación en el proyecto de ejemplo inicia la función del orquestador y, a continuación, entra en un bucle que llama a `GetStatusAsync` cada 2 segundos:

```cs
string instanceId = await client.StartNewAsync(nameof(HelloSequence), input: null);
logger.LogInformation($"Started new instance with ID = {instanceId}.");

DurableOrchestrationStatus status;
while (true)
{
    status = await client.GetStatusAsync(instanceId);
    logger.LogInformation($"Status: {status.RuntimeStatus}, Last update: {status.LastUpdatedTime}.");

    if (status.RuntimeStatus == OrchestrationRuntimeStatus.Completed ||
        status.RuntimeStatus == OrchestrationRuntimeStatus.Failed ||
        status.RuntimeStatus == OrchestrationRuntimeStatus.Terminated)
    {
        break;
    }

    await Task.Delay(TimeSpan.FromSeconds(2));
}
```

## <a name="run-the-sample"></a>Ejecución del ejemplo

Esta sección proporciona información general sobre cómo ejecutar el [proyecto de ejemplo](https://github.com/Azure/azure-functions-durable-extension/tree/master/samples/webjobssdk/chaining). Para obtener instrucciones detalladas que explican cómo ejecutar un proyecto del SDK de WebJobs localmente e implementarlo en un WebJob de Azure, consulte [Introducción al SDK de WebJobs](../app-service/webjobs-sdk-get-started.md#deploy-as-a-webjob).

### <a name="run-locally"></a>Ejecución en modo local

1. Asegúrese de que está ejecutando el emulador de almacenamiento (consulte los [requisitos previos](#prerequisites)).

1. Si desea ver los registros de Application Insights cuando se ejecuta localmente:

  a. Cree un recurso de Application Insights del tipo de aplicación **General**.

  b. Guarde la clave de instrumentación en el archivo *App.config*.

1. Ejecute el proyecto.

### <a name="run-in-azure"></a>Ejecución en Azure

1. Cree una aplicación web y una cuenta de almacenamiento.

1. En la aplicación web, guardar la cadena de conexión de almacenamiento en una configuración de aplicación llamada AzureWebJobsStorage.

1. Cree un recurso de Application Insights del tipo de aplicación **General**.

1. Guarde la clave de instrumentación en una configuración de aplicación llamada APPINSIGHTS_INSTRUMENTATIONKEY.

1. Realice la implementación como un WebJob.

## <a name="webjobs-sdk-3x"></a>SDK de WebJobs 3.x

El cambio principal que presenta la versión 3.x es el uso de .NET Core en lugar de .NET Framework. Para crear un proyecto 3.x, las instrucciones son las mismas con las siguientes excepciones:

1. Cree una aplicación de consola de .NET Core. En el cuadro de diálogo **Nuevo proyecto** de Visual Studio, seleccione **.NET Core > Aplicación de consola (.NET Core)**. El archivo de proyecto especifica que `TargetFramework` es `netcoreapp2.0`.

1. Elija la versión preliminar 3.x de los siguientes paquetes:

  * `Microsoft.Azure.WebJobs.Extensions`
  * `Microsoft.Azure.WebJobs.Logging.ApplicationInsights`

2. Cambie el código del método `Main` para obtener la cadena de conexión de almacenamiento y la clave de instrumentación de Application Insights de un archivo *appSettings.json*, utilizando la plataforma de configuración de .NET Core.  Este es un ejemplo:

   ```cs
   static void Main(string[] args)
   {
       var builder = new ConfigurationBuilder()
           .SetBasePath(Directory.GetCurrentDirectory())
           .AddJsonFile("appsettings.json");

       var appSettingsConfig = builder.Build();

       using (var loggerFactory = new LoggerFactory())
       {
           var config = new JobHostConfiguration();

           config.DashboardConnectionString = "";
           config.StorageConnectionString = 
               appSettingsConfig.GetConnectionString("AzureWebJobsStorage");
           var instrumentationKey =
               appSettingsConfig["APPINSIGHTS_INSTRUMENTATIONKEY"];

           config.LoggerFactory = loggerFactory
               .AddApplicationInsights(instrumentationKey, null)
               .AddConsole();

           config.UseTimers();
           config.UseDurableTask(new DurableTaskExtension
           {
               HubName = "MyTaskHub",
           });
           var host = new JobHost(config);
           host.RunAndBlock();
       }
   }
   ```

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre el SDK de WebJobs, consulte [Cómo usar el SDK de WebJobs](../app-service/webjobs-sdk-how-to.md).

