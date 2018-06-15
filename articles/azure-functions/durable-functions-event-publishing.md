---
title: Publicación de Durable Functions en Azure Event Grid (versión preliminar)
description: Aprenda a configurar la publicación automática en Azure Event Grid de Durable Functions.
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
ms.date: 04/20/2018
ms.author: tdykstra
ms.openlocfilehash: 50e517e5719fb102fd91072abe59d3908176278e
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
ms.locfileid: "33762469"
---
# <a name="durable-functions-publishing-to-azure-event-grid-preview"></a>Publicación de Durable Functions en Azure Event Grid (versión preliminar)

En este artículo se muestra cómo configurar Azure Durable Functions para publicar eventos de ciclo de vida de orquestación (como los creados, los completados y los que contienen errores) en un [tema de Azure Event Grid](https://docs.microsoft.com/en-us/azure/event-grid/overview) personalizado. 

A continuación se muestran algunos escenarios donde esta característica resulta útil:

* **Escenarios de DevOps como las implementaciones Azul/Verde**: por si desea saber si hay tareas en ejecución antes de implementar la [estrategia de implementación en paralelo](https://docs.microsoft.com/en-us/azure/azure-functions/durable-functions-versioning#side-by-side-deployments).

* **Compatibilidad con la supervisión y el diagnóstico avanzados**: puede realizar el seguimiento de la información de estado de la orquestación en un almacén externo optimizado para las consultas, como SQL Database o CosmosDB.

* **Actividad de ejecución en segundo plano de larga duración**: si se utiliza Durable Functions para una actividad de ejecución en segundo plano de larga duración, esta característica le ayuda a conocer el estado actual.

## <a name="prerequisites"></a>requisitos previos

* Instale [Microsoft.Azure.WebJobs.Extensions.DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask) 1.3.0-rc o una versión posterior en el proyecto de Durable Functions.
* Instale el [Emulador de Azure Storage](https://docs.microsoft.com/en-us/azure/storage/common/storage-use-emulator).
* Instale la [CLI de Azure 2.0](https://docs.microsoft.com/en-us/cli/azure/?view=azure-cli-latest) o use [Azure Cloud Shell](https://docs.microsoft.com/en-us/azure/cloud-shell/overview).

## <a name="create-a-custom-event-grid-topic"></a>Creación de un tema de Event Grid personalizado

Cree un tema de Event Grid para enviar eventos desde Durable Functions. Las instrucciones siguientes muestran cómo crear un tema con la CLI de Azure. Para información acerca de cómo hacerlo con PowerShell o Azure Portal, consulte los artículos siguientes:

* [Inicio rápido en EventGrid: creación de eventos personalizados con PowerShell](https://docs.microsoft.com/en-us/azure/event-grid/custom-event-quickstart-powershell)
* [Inicio rápido en EventGrid: creación de eventos personalizados con Azure Portal](https://docs.microsoft.com/en-us/azure/event-grid/custom-event-quickstart-portal)

### <a name="create-a-resource-group"></a>Crear un grupo de recursos

Cree un grupo de recursos con el comando `az group create`. Actualmente, Event Grid no se admite en todas las regiones. Para información sobre las regiones donde se admite, consulte la [introducción a Event Grid](https://docs.microsoft.com/en-us/azure/event-grid/overview). 

```bash
az group create --name eventResourceGroup --location westus2
```

### <a name="create-a-custom-topic"></a>Creación de un tema personalizado

Un tema de Event Grid proporciona un punto de conexión definido por el usuario en el que se publicar eventos. Reemplace `<topic_name>` por un nombre único para el tema. El nombre del tema debe ser único, ya que se convierte en entrada DNS.

```bash
az eventgrid topic create --name <topic_name> -l westus2 -g eventResourceGroup 
```

## <a name="get-the-endpoint-and-key"></a>Obtención del punto de conexión y la clave

Obtenga el punto de conexión del tema. Reemplace `<topic_name>` por el nombre que haya elegido.

```bash
az eventgrid topic show --name <topic_name> -g eventResourceGroup --query "endpoint" --output tsv
```

Obtenga la clave del tema. Reemplace `<topic_name>` por el nombre que haya elegido.

```bash
az eventgrid topic key list --name <topic_name> -g eventResourceGroup --query "key1" --output tsv
```

Ahora puede enviar eventos al tema.

## <a name="configure-azure-event-grid-publishing"></a>Configuración de la publicación en Azure Event Grid

En su proyecto de Durable Functions, busque el archivo `host.json`.

Agregue `EventGridTopicEndpoint` y `EventGridKeySettingName` en una propiedad `durableTask`.

```json
{
    "durableTask": {
        "EventGridTopicEndpoint": "https://<topic_name>.westus2-1.eventgrid.azure.net/api/events",
        "EventGridKeySettingName": "EventGridKey"
    }
}
```

* **EventGridTopicEndpoint**: punto de conexión del tema de Event Grid.
* **EventGridKeySettingName**: clave de la configuración de la aplicación en la función de Azure. Durable Functions obtendrá la clave del tema de Event Grid a partir de este valor.

Una vez configurado el archivo `host.json`, el proyecto de Durable Functions empieza a enviar eventos de ciclo de vida al tema de Event Grid. Esto funciona tanto con la ejecución en la aplicación de función como local.

Establezca la configuración de la aplicación para la clave del tema en la aplicación de función y en `local.setting.json`. El siguiente JSON es un ejemplo de `local.settings.json` para la depuración local. Reemplace `<topic_key>` por la clave del tema.  

```json
{
    "IsEncrypted": false,
    "Values": {
        "AzureWebJobsStorage": "UseDevelopmentStorage=true",
        "AzureWebJobsDashboard": "UseDevelopmentStorage=true",
        "EventGridKey": "<topic_key>"
    }
}
```

Asegúrese de que el [emulador de Storage](https://docs.microsoft.com/en-us/azure/storage/common/storage-use-emulator) funciona. Es conveniente ejecutar el comando `AzureStorageEmulator.exe clear all` antes de la ejecución.

## <a name="create-functions-that-listen-for-events"></a>Creación de funciones que realicen escuchas para los eventos

Cree una aplicación de función. Se recomienda ubicarla en la misma región que el tema de Event Grid.

### <a name="create-an-event-grid-trigger-function"></a>Creación de una función de desencadenador de Event Grid

Cree una función que reciba los eventos de ciclo de vida. Seleccione **Función personalizada**. 

![Selección de creación de una función personalizada](media/durable-functions-event-publishing/functions-portal.png)

Elija Desencadenador de Event Grid y seleccione `C#`.

![Selección de Desencadenador de Event Grid](media/durable-functions-event-publishing/eventgrid-trigger.png)

Escriba el nombre de la función y seleccione `Create`.

![Creación de Desencadenador de Event Grid](media/durable-functions-event-publishing/eventgrid-trigger-creation.png)

Se creará una función con el código siguiente: 

```csharp
#r "Newtonsoft.Json"
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
public static void Run(JObject eventGridEvent, TraceWriter log)
{
    log.Info(eventGridEvent.ToString(Formatting.Indented));
}
```

Seleccione `Add Event Grid Subscription`. Esta operación agrega una suscripción a Event Grid para el tema que ha creado. Para más información, consulte [Conceptos de Azure Event Grid](https://docs.microsoft.com/en-us/azure/event-grid/concepts).

![Selección del vínculo Desencadenador de Event Grid](media/durable-functions-event-publishing/eventgrid-trigger-link.png)

Seleccione `Event Grid Topics` como **Tipo de tema**. Seleccione el grupo de recursos que ha creado para el tema de Event Grid. A continuación, seleccione la instancia del tema de Event Grid. Presione `Create`.

![Cree una suscripción de Event Grid.](media/durable-functions-event-publishing/eventsubscription.png)

Ahora está preparado para recibir eventos de ciclo de vida. 

## <a name="create-durable-functions-to-send-the-events"></a>Creación de una instancia de Durable Functions para enviar eventos

En el proyecto de Durable Functions, inicie la depuración en la máquina local.  El código siguiente es el mismo que el de la plantilla de Durable Functions. Ya se han configurado `host.json` y `local.settings.json` en la máquina local. 

```csharp
using System.Collections.Generic;
using System.Net.Http;
using System.Threading.Tasks;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;

namespace LifeCycleEventSpike
{
    public static class Sample
    {
    {
        [FunctionName("Sample")]
        public static async Task<List<string>> RunOrchestrator(
            [OrchestrationTrigger] DurableOrchestrationContext context)
        {
            var outputs = new List<string>();

            // Replace "hello" with the name of your Durable Activity Function.
            outputs.Add(await context.CallActivityAsync<string>("Sample_Hello", "Tokyo"));
            outputs.Add(await context.CallActivityAsync<string>("Sample_Hello", "Seattle"));
            outputs.Add(await context.CallActivityAsync<string>("Sample_Hello", "London"));

            // returns ["Hello Tokyo!", "Hello Seattle!", "Hello London!"]
            return outputs;
        }

        [FunctionName("Sample_Hello")]
        public static string SayHello([ActivityTrigger] string name, TraceWriter log)
        {
            log.Info($"Saying hello to {name}.");
            return $"Hello {name}!";
        }

        [FunctionName("Sample_HttpStart")]
        public static async Task<HttpResponseMessage> HttpStart(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")]HttpRequestMessage req,
            [OrchestrationClient]DurableOrchestrationClient starter,
            TraceWriter log)
        {
            // Function input comes from the request content.
            string instanceId = await starter.StartNewAsync("Sample", null);
            log.Info($"Started orchestration with ID = '{instanceId}'.");
            return starter.CreateCheckStatusResponse(req, instanceId);
        }
    }
}
```

Si se llama a `Sample_HttpStart` con Postman o el explorador, la instancia de Durable Functions comienza a enviar eventos de ciclo de vida. El punto de conexión suele ser `http://localhost:7071/api/Sample_HttpStart` para la depuración local.

Consulte los registros de la función que ha creado en Azure Portal.

```
2018-04-20T09:28:21.041 [Info] Function started (Id=3301c3ef-625f-40ce-ad4c-9ba2916b162d)
2018-04-20T09:28:21.104 [Info] {
    "id": "054fe385-c017-4ce3-b38a-052ac970c39d",    
    "subject": "durable/orchestrator/Running",
    "data": {
        "hubName": "DurableFunctionsHub",
        "functionName": "Sample",
        "instanceId": "055d045b1c8a415b94f7671d8df693a6",
        "reason": "",
        "runtimeStatus": "Running"
    },
    "eventType": "orchestratorEvent",
    "eventTime": "2018-04-20T09:28:19.6492068Z",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "topic": "/subscriptions/<your_subscription_id>/resourceGroups/eventResourceGroup/providers/Microsoft.EventGrid/topics/durableTopic"
}

2018-04-20T09:28:21.104 [Info] Function completed (Success, Id=3301c3ef-625f-40ce-ad4c-9ba2916b162d, Duration=65ms)
2018-04-20T09:28:37.098 [Info] Function started (Id=36fadea5-198b-4345-bb8e-2837febb89a2)
2018-04-20T09:28:37.098 [Info] {
    "id": "8cf17246-fa9c-4dad-b32a-5a868104f17b",
    "subject": "durable/orchestrator/Completed",
    "data": {
        "hubName": "DurableFunctionsHub",
        "functionName": "Sample",
        "instanceId": "055d045b1c8a415b94f7671d8df693a6",
        "reason": "",
        "runtimeStatus": "Completed"
    },
    "eventType": "orchestratorEvent",
    "eventTime": "2018-04-20T09:28:36.5061317Z",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "topic": "/subscriptions/<your_subscription_id>/resourceGroups/eventResourceGroup/providers/Microsoft.EventGrid/topics/durableTopic"
}
2018-04-20T09:28:37.098 [Info] Function completed (Success, Id=36fadea5-198b-4345-bb8e-2837febb89a2, Duration=0ms)
```

## <a name="event-schema"></a>Esquema de eventos

En la lista siguiente se explica el esquema de los eventos de ciclo de vida:

* **id**: identificador único del evento de Event Grid.
* **subject**: ruta de acceso al asunto del evento. `durable/orchestrator/{orchestrationRuntimeStatus}`. `{orchestrationRuntimeStatus}` será `Running`, `Completed`, `Failed` y `Terminated`.  
* **data**: parámetros específicos de Durable Functions.
    * **hubName**: nombre de [TaskHub](https://docs.microsoft.com/en-us/azure/azure-functions/durable-functions-task-hubs).
    * **functionName**: nombre de la función de orquestador.
    * **instanceId**: identificador de la instancia de Durable Functions.
    * **reason**: información adicional asociada al evento de seguimiento. Para más información, consulte [Diagnóstico con Durable Functions (Azure Functions)](https://docs.microsoft.com/en-us/azure/azure-functions/durable-functions-diagnostics)
    * **runtimeStatus**: estado del tiempo de ejecución de la orquestación. Running, Completed, Failed, Canceled. 
* **eventType**: "orchestratorEvent".
* **eventTime**: hora del evento (UTC).
* **dataVersion**: versión del esquema de los eventos de ciclo de vida.
* **metadataVersion**: versión de los metadatos.
* **topic**: recurso del tema de Event Grid.

## <a name="how-to-test-locally"></a>Prueba local

Para la prueba local, use [ngrok](functions-bindings-event-grid.md#local-testing-with-ngrok).

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Aprenda a administrar instancias en Durable Functions](durable-functions-instance-management.md)

> [!div class="nextstepaction"]
> [Aprenda sobre el control de versiones en Durable Functions](durable-functions-versioning.md)
