---
title: Monitor Azure Functions
description: "Obtenga información acerca de cómo usar Azure Application Insights con Azure Functions para supervisar la ejecución de funciones."
services: functions
author: tdykstra
manager: cfowler
editor: 
tags: 
keywords: "Azure funciones, funciones, procesamiento de eventos, webhooks, proceso dinámico, arquitectura sin servidor"
ms.assetid: 501722c3-f2f7-4224-a220-6d59da08a320
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/15/2017
ms.author: tdykstra
ms.openlocfilehash: 6f38fe1e99c734bf09a403ea93b6487a71110cac
ms.sourcegitcommit: e19f6a1709b0fe0f898386118fbef858d430e19d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/13/2018
---
# <a name="monitor-azure-functions"></a>Monitor Azure Functions

## <a name="overview"></a>Información general 

[Azure Functions](functions-overview.md) ofrece integración incorporada con [Azure Application Insights](../application-insights/app-insights-overview.md) para las funciones de supervisión. En este artículo se muestra cómo configurar Functions para enviar datos de telemetría a Application Insights.

![Explorador de métricas de Application Insights](media/functions-monitoring/metrics-explorer.png)

Functions también dispone de supervisión incorporada que no usa Application Insights. Se recomienda Application Insights porque ofrece más datos y mejores maneras de analizar los datos. Para obtener información más información acerca de la supervisión integrada, consulte la [última sección de este artículo](#monitoring-without-application-insights).

## <a name="enable-application-insights-integration"></a>Habilitación de la integración de Application Insights

Para que una aplicación de función envíe datos a Application Insights, debe conocer la clave de instrumentación de una instancia de Application Insights. Existen dos maneras de realizar esa conexión en el [Azure Portal](https://portal.azure.com):

* [Crear una instancia de Application Insights conectada al crear la aplicación de función](#new-function-app).
* [Conectar una instancia de Application Insights a una aplicación de función existente](#existing-function-app).

### <a name="new-function-app"></a>Nueva aplicación de función

Habilite Application Insights en la página **Crear** de Function App:

1. Establezca el conmutador **Application Insights** en **Habilitado**.

2. Seleccione una **ubicación de Application Insights**.

   ![Habilitar Application Insights al crear una aplicación de función](media/functions-monitoring/enable-ai-new-function-app.png)

### <a name="existing-function-app"></a>Aplicación de función existente

Obtenga la clave de instrumentación y guárdela en una aplicación de función:

1. Cree la instancia de Application Insights. Establezca el tipo de aplicación en **General**.

   ![Crear una instancia de Application Insights habilitada de tipo General](media/functions-monitoring/ai-general.png)

2. Copie la clave de instrumentación de la página **Essentials** de la instancia de Application Insights. Mantenga el puntero al final del valor de la clave que se muestra para obtener un botón **Haga clic para copiar**.

   ![Copiar la clave de instrumentación de Application Insights](media/functions-monitoring/copy-ai-key.png)

1. En la página **Configuración de la aplicación** de la aplicación de función, [agregue una configuración de aplicación](functions-how-to-use-azure-function-app-settings.md#settings) haciendo clic en **Agregar nuevo valor**. Asigne un nombre al nuevo valor APPINSIGHTS_INSTRUMENTATIONKEY y pegue la clave de instrumentación copiada.

   ![Agregar clave de instrumentación a la configuración de la aplicación](media/functions-monitoring/add-ai-key.png)

1. Haga clic en **Save**(Guardar).

## <a name="disable-built-in-logging"></a>Deshabilitar el registro integrado

Si habilita Application Insights, es recomendable que deshabilite el [registro integrado que usa Azure Storage](#logging-to-storage). El registro integrado es útil para realizar pruebas con cargas de trabajo ligeras, pero no está diseñado para su uso en producción de carga alta. Para la supervisión de producción, se recomienda Application Insights. Si el registro integrado se usa en producción, el registro resultante podría estar incompleto debido a la limitación de Azure Storage.

Para deshabilitar el registro integrado, elimine la configuración de la aplicación `AzureWebJobsDashboard`. Para obtener información acerca de cómo eliminar la configuración de la aplicación en Azure Portal, consulte la sección **Application settings** (Configuración de la aplicación) en [How to manage a function app](functions-how-to-use-azure-function-app-settings.md#settings) (Cómo administrar una aplicación de función).

Cuando se habilita Application Insights y se deshabilita el registro integrado, la pestaña **Supervisión** de una función en Azure Portal le lleva a Application Insights.

## <a name="view-telemetry-data"></a>Visualización de datos de telemetría

Para navegar a la instancia de Application Insights conectada desde una aplicación de función en el portal, seleccione el vínculo **Application Insights** en la página **Introducción** de la aplicación de función.

Para obtener más información acerca de Application Insights, consulte [Documentación de Application Insights](https://docs.microsoft.com/azure/application-insights/). En esta sección se muestran algunos ejemplos de cómo ver los datos de Application Insights. Si ya está familiarizado con Application Insights, puede ir directamente a [las secciones acerca de cómo configurar y personalizar los datos de telemetría](#configure-categories-and-log-levels).

En [Explorador de métricas](../application-insights/app-insights-metrics-explorer.md), puede crear alertas y gráficos basados en métricas, como el número de invocaciones de función, el tiempo de ejecución y la tasa de éxito.

![Explorador de métricas](media/functions-monitoring/metrics-explorer.png)

En la pestaña [Errores](../application-insights/app-insights-asp-net-exceptions.md), puede crear alertas y gráficos basados en errores de funciones y excepciones del servidor. **Nombre de la operación** es el nombre de la función. Los errores en las dependencias no se muestran a no ser que se implemente [telemetría personalizada](#custom-telemetry-in-c-functions) para las dependencias.

![Errores](media/functions-monitoring/failures.png)

En la pestaña [Rendimiento](../application-insights/app-insights-performance-counters.md), puede analizar los problemas de rendimiento.

![Rendimiento](media/functions-monitoring/performance.png)

En la pestaña **Servidores** se muestra la utilización de recursos y el rendimiento por servidor. Estos datos pueden ser útiles para la depuración de escenarios en los que las funciones están dificultando los recursos subyacentes. Los servidores se conocen como **Instancias de rol en la nube**.

![Servidores](media/functions-monitoring/servers.png)

La pestaña [Live Metrics Stream](../application-insights/app-insights-live-stream.md) muestra datos de métricas en tiempo real.

![Live Stream](media/functions-monitoring/live-stream.png)

## <a name="query-telemetry-data"></a>Consultar datos de telemetría

[Application Insights Analytics](../application-insights/app-insights-analytics.md) proporciona acceso a todos los datos de telemetría en forma de tabla en una base de datos. Analytics ofrece un lenguaje de consulta para extraer, manipular y visualizar los datos.

![Seleccione Analytics](media/functions-monitoring/select-analytics.png)

![Ejemplo de Analytics](media/functions-monitoring/analytics-traces.png)

Este es un ejemplo de consulta que muestra la distribución de solicitudes por trabajador durante los últimos 30 minutos.

```
requests
| where timestamp > ago(30m) 
| summarize count() by cloud_RoleInstance, bin(timestamp, 1m)
| render timechart
```

Las tablas que están disponibles se muestran en la pestaña **Esquema** del panel izquierdo. Encontrará los datos generados por las invocaciones de función en las tablas siguientes:

* **traces**: registros creados por el tiempo de ejecución y por el código de la función.
* **requests**: una por cada invocación de función.
* **exceptions**: cualquier excepción que produzca el tiempo de ejecución.
* **customMetrics**: recuento de invocaciones correctas y erróneas, tasa de éxito y duración.
* **customEvents**: eventos registrados por el tiempo de ejecución; por ejemplo: solicitudes HTTP que desencadenan una función.
* **performanceCounters**: información acerca del rendimiento de los servidores en los que se ejecutan las funciones.

El resto de tablas son para pruebas de disponibilidad, y telemetría de explorador y de cliente. Puede implementar telemetría personalizada para agregarles datos.

Dentro de cada tabla, algunos de los datos específicos de Functions están en un campo `customDimensions`.  Por ejemplo, la consulta siguiente recupera todos los seguimientos que tienen el nivel de registro `Error`.

```
traces 
| where customDimensions.LogLevel == "Error"
```

El tiempo de ejecución proporciona `customDimensions.LogLevel` y `customDimensions.Category`. Puede proporcionar campos adicionales en los registros que escriba en el código de función. Consulte [Registro estructurado](#structured-logging) más adelante en este artículo.

## <a name="configure-categories-and-log-levels"></a>Configurar categorías y niveles de registro

Puede usar Application Insights sin ninguna configuración personalizada, pero la configuración predeterminada puede dar lugar a un gran volumen de datos. Si usa una suscripción de Visual Studio Azure, es posible que alcance su límite de datos para Application Insights. En el resto de este artículo se muestra cómo configurar y personalizar los datos que envían las funciones a Application Insights.

### <a name="categories"></a>Categorías

El registrador de Azure Functions incluye una *categoría* para cada registro. La categoría indica qué parte del código de tiempo de ejecución o del código de la función escribió el registro. 

El tiempo de ejecución de Functions crea registros que tienen una categoría que empieza con "Host". Por ejemplo, los registros "función iniciada", "función ejecutada" y "función concluida" tienen la categoría "Host.Executor". 

Si escribe registros en el código de función, su categoría es "Function".

### <a name="log-levels"></a>Niveles de registro

El registrador de funciones de Azure también incluye un *nivel de registro* con cada registro. [LogLevel](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.loglevel#Microsoft_Extensions_Logging_LogLevel) es una enumeración y el código entero indica la importancia relativa:

|LogLevel    |Código|
|------------|---|
|Seguimiento       | 0 |
|Depurar       | 1 |
|Información | 2 |
|Warning (Advertencia)     | 3 |
|Error       | 4 |
|Crítico    | 5 |
|None        | 6 |

El nivel de registro `None` se explica en la sección siguiente. 

### <a name="configure-logging-in-hostjson"></a>Configurar el registro en host.json

El archivo *host.json* configura la cantidad de registro que envía una aplicación de la función a Application Insights. Para cada categoría, debe indicar el nivel de registro mínimo para enviar. Este es un ejemplo:

```json
{
  "logger": {
    "categoryFilter": {
      "defaultLevel": "Information",
      "categoryLevels": {
        "Host.Results": "Error",
        "Function": "Error",
        "Host.Aggregator": "Information"
      }
    }
  }
}
```

En el ejemplo siguiente se configuran las reglas siguientes:

1. Para los registros con la categoría "Host.Results" o "Function", solo tiene que enviar el nivel `Error` y superior a Application Insights. Los registros del nivel `Warning` e inferiores se omiten.
2. Para los registros con la categoría Host. Aggregator, envíe solo el nivel `Information` y superior a Application Insights. Los registros del nivel `Debug` e inferiores se omiten.
3. Para el resto de registros, envíe solo el nivel `Information` y superior a Application Insights.

El valor de categoría de *host.json* controla el registro de todas las categorías que comienzan con el mismo valor. Por ejemplo, "Host" en *host.json* controla el registro de "Host.General", "Host.Executor", "Host.Results" y así sucesivamente.

Si *host.json* incluye varias categorías que comienzan con la misma cadena, las más largas se asignan primero. Por ejemplo, imagine que quiere todo del tiempo de ejecución, menos que "Host.Aggregator" registre en el nivel `Error`, y que "Host.Aggregator" registre en el nivel `Information`:

```json
{
  "logger": {
    "categoryFilter": {
      "defaultLevel": "Information",
      "categoryLevels": {
        "Host": "Error",
        "Function": "Error",
        "Host.Aggregator": "Information"
      }
    }
  }
}
```

Para eliminar todos los registros de una categoría, puede usar el nivel de registro `None`. No hay ningún registro escrito con esa categoría y no existe ningún nivel de registro por encima de él.

En las secciones siguientes se describen las categorías principales de registros que crea el tiempo de ejecución. 

### <a name="category-hostresults"></a>Categoría Host.Results

Estos registros se muestran como "requests" en Application Insights, e indican el éxito o fracaso de una función.

![Gráfico de solicitudes](media/functions-monitoring/requests-chart.png)

Todos estos registros se escriben en el nivel `Information`, por lo que si se filtra por `Warning` o superior no verá ninguno de estos datos.

### <a name="category-hostaggregator"></a>Categoría Host.Aggregator

Estos registros proporcionan los recuentos y promedios de las invocaciones de función en un período de tiempo [configurable](#configure-the-aggregator). El período predeterminado es 30 segundos o 1000 resultados, lo que ocurra primero. 

Los registros están disponibles en la tabla **customMetrics** de Application Insights. Algunos ejemplos son el número de ejecuciones, la tasa de éxito y la duración.

![Consulta customMetrics](media/functions-monitoring/custom-metrics-query.png)

Todos estos registros se escriben en el nivel `Information`, por lo que si se filtra por `Warning` o superior no verá ninguno de estos datos.

### <a name="other-categories"></a>Otras categorías

Todos los registros de categorías diferentes a las que ya se muestran están disponibles en la tabla **traces** en Application Insights.

![Consulta traces](media/functions-monitoring/analytics-traces.png)

Todos los registros con categorías que comienzan por "Host" los escribe el tiempo de ejecución de Functions. Los registros "Función iniciada" y "Función concluida" tienen la categoría "Host.Executor". Para las ejecuciones correctas, estos registros están en el nivel `Information`; las excepciones se registran en el nivel `Error`. El tiempo de ejecución también crea registros del nivel `Warning`; por ejemplo, poner en cola los mensajes enviados a la cola de dudosos.

Los registros escritos por el código de función tienen la categoría "Function" y pueden tener cualquier nivel de registro.

## <a name="configure-the-aggregator"></a>Configurar el agregador

Como se indicó en la sección anterior, el tiempo de ejecución agrega datos acerca de las ejecuciones de la función durante un período de tiempo. El período predeterminado es 30 segundos o 1000 ejecuciones, lo que ocurra primero. Puede configurar este valor en el archivo *host.json*.  Este es un ejemplo:

```json
{
    "aggregator": {
      "batchSize": 1000,
      "flushTimeout": "00:00:30"
    }
}
```

## <a name="configure-sampling"></a>Configurar el muestreo

Application Insights tiene una característica de [muestreo](../application-insights/app-insights-sampling.md) que le puede ayudar a impedir que se recopilen demasiados datos de telemetría en los momentos de picos de carga. Cuando el número de elementos de telemetría supera una tasa especificada, Application Insights empieza a omitir aleatoriamente algunos de los elementos entrantes. Puede configurar el muestreo en *host.json*.  Este es un ejemplo:

```json
{
  "applicationInsights": {
    "sampling": {
      "isEnabled": true,
      "maxTelemetryItemsPerSecond" : 5
    }
  }
}
```

## <a name="write-logs-in-c-functions"></a>Escribir registros en funciones de C#

Puede escribir registros en el código de función que aparecen como seguimientos de Application Insights.

### <a name="ilogger"></a>ILogger

Use un parámetro [ILogger](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.ilogger) en las funciones en lugar de un parámetro `TraceWriter`. Los registros creados mediante el uso de `TraceWriter` se dirigen a Application Insights, pero `ILogger` permite hacer un [registro estructurado](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).

Con un objeto `ILogger` llama a [métodos de extensión `Log<level>` en ILogger](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.loggerextensions#Methods_) para crear registros. Por ejemplo, el código siguiente escribe registros `Information` con la categoría "Function".

```cs
public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, ILogger logger)
{
    logger.LogInformation("Request for item with key={itemKey}.", id);
```

### <a name="structured-logging"></a>Registro estructurado

El orden de los marcadores de posición, no sus nombres, determina qué parámetros se usan en el mensaje del registro. Por ejemplo, supongamos que tiene el siguiente código:

```csharp
string partitionKey = "partitionKey";
string rowKey = "rowKey";
logger.LogInformation("partitionKey={partitionKey}, rowKey={rowKey}", partitionKey, rowKey);
```

Si mantiene la misma cadena de mensaje e invierte el orden de los parámetros, el texto del mensaje resultante tendrá los valores en los lugares incorrectos.

Los marcadores de posición se controlan de esta forma para que pueda hacer el registro estructurado. Application Insights almacena los pares de nombre-valor del parámetro además de la cadena de mensaje. Como resultado, los argumentos del mensaje se convierten en campos que puede consultar.

Por ejemplo, si la llamada de método del registrador tiene un aspecto similar al ejemplo anterior, puede consultar el campo `customDimensions.prop__rowKey`. Se agrega el prefijo `prop__` para asegurar que no hay ninguna colisión entre los campos que agrega el tiempo de ejecución y los campos que agrega el código de la función.

También puede consultar la cadena del mensaje original haciendo referencia al campo `customDimensions.prop__{OriginalFormat}`.  

Esta es una representación de JSON de ejemplo de los datos `customDimensions`:

```json
{
  customDimensions: {
    "prop__{OriginalFormat}":"C# Queue trigger function processed: {message}",
    "Category":"Function",
    "LogLevel":"Information",
    "prop__message":"c9519cbf-b1e6-4b9b-bf24-cb7d10b1bb89"
  }
}
```

### <a name="logging-custom-metrics"></a>Registro de métricas personalizadas  

En funciones de secuencia de comandos de C#, puede usar el método de extensión `LogMetric` en `ILogger` para crear métricas personalizadas en Application Insights. Este es un ejemplo de una llamada de método:

```csharp
logger.LogMetric("TestMetric", 1234); 
```

Este código es una alternativa a llamar a `TrackMetric` con [la API de Application Insights para .NET](#custom-telemetry-in-c-functions).

## <a name="write-logs-in-javascript-functions"></a>Escribir registros en las funciones de JavaScript

En las funciones de Node.js, use `context.log` para escribir los registros. El registro estructurado no está habilitado.

```
context.log('JavaScript HTTP trigger function processed a request.' + context.invocationId);
```

### <a name="logging-custom-metrics"></a>Registro de métricas personalizadas  

En funciones de Node.js, puede usar el método `context.log.metric` para crear métricas personalizadas en Application Insights. Este es un ejemplo de una llamada de método:

```javascript
context.log.metric("TestMetric", 1234); 
```

Este código es una alternativa a llamar a `trackMetric` con [el SDK de Node.js de Application Insights](#custom-telemetry-in-javascript-functions).

## <a name="custom-telemetry-in-c-functions"></a>Telemetría personalizada en funciones de C#

Puede usar el paquete NuGet [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) para enviar datos de telemetría personalizada a Application Insights.

Este es un ejemplo de código C# que utiliza la [API de telemetría personalizada](../application-insights/app-insights-api-custom-events-metrics.md). El ejemplo es para una biblioteca de clases. NET, pero el código de Application Insights es el mismo para la secuencia de comandos de C#.

```cs
using System;
using System.Net;
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.Azure.WebJobs;
using System.Net.Http;
using System.Threading.Tasks;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Extensions.Logging;
using System.Linq;

namespace functionapp0915
{
    public static class HttpTrigger2
    {
        private static string key = TelemetryConfiguration.Active.InstrumentationKey = 
            System.Environment.GetEnvironmentVariable(
                "APPINSIGHTS_INSTRUMENTATIONKEY", EnvironmentVariableTarget.Process);

        private static TelemetryClient telemetry = 
            new TelemetryClient() { InstrumentationKey = key };

        [FunctionName("HttpTrigger2")]
        public static async Task<HttpResponseMessage> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]
            HttpRequestMessage req, ExecutionContext context, ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");
            DateTime start = DateTime.UtcNow;

            // parse query parameter
            string name = req.GetQueryNameValuePairs()
                .FirstOrDefault(q => string.Compare(q.Key, "name", true) == 0)
                .Value;

            // Get request body
            dynamic data = await req.Content.ReadAsAsync<object>();

            // Set name to query string or body data
            name = name ?? data?.name;

            telemetry.Context.Operation.Id = context.InvocationId.ToString();
            telemetry.Context.Operation.Name = "cs-http";
            if (!String.IsNullOrEmpty(name))
            {
                telemetry.Context.User.Id = name;
            }
            telemetry.TrackEvent("Function called");
            telemetry.TrackMetric("Test Metric", DateTime.Now.Millisecond);
            telemetry.TrackDependency("Test Dependency", 
                "swapi.co/api/planets/1/", 
                start, DateTime.UtcNow - start, true);

            return name == null
                ? req.CreateResponse(HttpStatusCode.BadRequest, 
                    "Please pass a name on the query string or in the request body")
                : req.CreateResponse(HttpStatusCode.OK, "Hello " + name);
        }
    }
}
```

No llame a `TrackRequest` ni a `StartOperation<RequestTelemetry>`, ya que verá solicitudes duplicadas de invocaciones de función.  El tiempo de ejecución de Functions realiza un seguimiento automático de las solicitudes.

Establezca `telemetry.Context.Operation.Id` en el identificador de la invocación cada vez que se inicie la función. Esto permite la correlación de todos los elementos de telemetría de una determinada invocación de función.

```cs
telemetry.Context.Operation.Id = context.InvocationId.ToString();
```

## <a name="custom-telemetry-in-javascript-functions"></a>Telemetría personalizada en funciones de JavaScript

El [SDK de Application Insights Node.js](https://www.npmjs.com/package/applicationinsights) está actualmente en versión beta. Presentamos ejemplos de código que envía la telemetría personalizada para Application Insights:

```javascript
const appInsights = require("applicationinsights");
appInsights.setup();
const client = appInsights.defaultClient;

module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    client.trackEvent({name: "my custom event", tagOverrides:{"ai.operation.id": context.invocationId}, properties: {customProperty2: "custom property value"}});
    client.trackException({exception: new Error("handled exceptions can be logged with this method"), tagOverrides:{"ai.operation.id": context.invocationId}});
    client.trackMetric({name: "custom metric", value: 3, tagOverrides:{"ai.operation.id": context.invocationId}});
    client.trackTrace({message: "trace message", tagOverrides:{"ai.operation.id": context.invocationId}});
    client.trackDependency({target:"http://dbname", name:"select customers proc", data:"SELECT * FROM Customers", duration:231, resultCode:0, success: true, dependencyTypeName: "ZSQL", tagOverrides:{"ai.operation.id": context.invocationId}});
    client.trackRequest({name:"GET /customers", url:"http://myserver/customers", duration:309, resultCode:200, success:true, tagOverrides:{"ai.operation.id": context.invocationId}});

    if (req.query.name || (req.body && req.body.name)) {
        context.res = {
            // status: 200, /* Defaults to 200 */
            body: "Hello " + (req.query.name || req.body.name)
        };
    }
    else {
        context.res = {
            status: 400,
            body: "Please pass a name on the query string or in the request body"
        };
    }
    context.done();
};
```

El parámetro `tagOverrides` establece `operation_Id` en el identificador de invocación de función. Esta configuración le permite correlacionar toda la telemetría personalizada y generada automáticamente para una determinada invocación de función.

## <a name="known-issues"></a>Problemas conocidos

### <a name="dependencies"></a>Dependencias

Las dependencias que la función tiene a otros servicios no se muestran automáticamente, pero puede escribir código personalizado para mostrarlas. El código de ejemplo en la [sección de telemetría personalizada de C#](#custom-telemetry-in-c-functions) muestra cómo hacerlo. El código de ejemplo da como resultado un *mapa de aplicación* de Application Insights, que tiene este aspecto:

![Mapa de aplicación](media/functions-monitoring/app-map.png)

### <a name="report-issues"></a>Informar de problemas

Para notificar un problema con la integración de Application Insights en Functions o para realizar una solicitud o sugerencia [cree un problema en GitHub](https://github.com/Azure/Azure-Functions/issues/new).

## <a name="monitoring-without-application-insights"></a>Supervisión sin Application Insights

Se recomienda Application Insights para supervisar funciones porque ofrece más datos y mejores maneras de analizarlos. No obstante, también puede encontrar registros y datos de telemetría en las páginas de Azure Portal de una instancia de Function App.

### <a name="logging-to-storage"></a>Registro en el almacenamiento

El registro integrado usa la cuenta de almacenamiento especificada por la cadena de conexión en la configuración de la aplicación `AzureWebJobsDashboard`. Si la configuración de la aplicación está establecida, puede ver los datos del registro en Azure Portal. En la página de una aplicación de función, seleccione una función y elija la pestaña **Supervisión**. Obtendrá una lista de las ejecuciones de la función. Seleccione una ejecución de la función para revisar la duración, los datos de entrada, los errores y los archivos de registro asociados.

Si usa Application Insights y tiene el [registro integrado deshabilitado](#disable-built-in-logging), la pestaña **Supervisión** le lleva a Application Insights.

### <a name="real-time-monitoring"></a>Supervisión en tiempo real

Puede transmitir archivos de registro a una sesión de línea de comandos en una estación de trabajo local mediante la [interfaz de la línea de comandos (CLI) de Azure 2.0](/cli/azure/install-azure-cli) o [Azure PowerShell](/powershell/azure/overview).  

Para la CLI de Azure 2.0, use los siguientes comandos para iniciar sesión, elija su suscripción y transmita los archivos de registro:

```
az login
az account list
az account set <subscriptionNameOrId>
az appservice web log tail --resource-group <resource group name> --name <function app name>
```

Para Azure PowerShell, use los siguientes comandos para agregar su cuenta de Azure, elija su suscripción y transmita los archivos de registro:

```
PS C:\> Add-AzureAccount
PS C:\> Get-AzureSubscription
PS C:\> Get-AzureSubscription -SubscriptionName "<subscription name>" | Select-AzureSubscription
PS C:\> Get-AzureWebSiteLog -Name <function app name> -Tail
```

Para más información, vea [How to: Stream logs](../app-service/web-sites-enable-diagnostic-log.md#streamlogs) (Cómo: Transmitir registros).

## <a name="next-steps"></a>pasos siguientes

Para obtener más información, consulte los siguientes recursos:

* [Application Insights](/azure/application-insights/)
* [Registro de ASP.NET Core](/aspnet/core/fundamentals/logging/)
