---
title: "Ejemplos de integración de Azure Service Bus en Event Grid | Microsoft Docs"
description: "Ejemplos de integración de la mensajería de Service Bus y Event Grid"
services: service-bus-messaging
documentationcenter: .net
author: ChristianWolf42
manager: timlt
editor: 
ms.assetid: f99766cb-8f4b-4baf-b061-4b1e2ae570e4
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: get-started-article
ms.date: 02/15/2018
ms.author: chwolf
ms.openlocfilehash: 3819a274696762861fbe76a9684b8495f1724f6a
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/12/2018
---
# <a name="azure-service-bus-to-azure-event-grid-examples"></a>Ejemplos de Azure Service Bus en Azure Event Grid

En este documento, aprenderá a configurar Azure Functions y una aplicación lógica que recibe mensajes basándose en la recepción de un evento de Event Grid. En el ejemplo se presupone un tema de Service Bus con dos suscripciones y que la suscripción de Event Grid se ha creado de una forma que permite enviar eventos solo a una suscripción de Service Bus. Posteriormente se envían mensajes al tema de Service Bus y se comprueba que el evento se ha generado para esta suscripción de Service Bus y, finalmente, la función o aplicación lógica recibe los mensajes desde esa suscripción de Service Bus y la completa.

* Primero, asegúrese de cumplir todos los [requisitos previos](#prerequisites).
* Cree una [instancia sencilla de Azure Function de prueba](#test-function-setup) para depurar y ver el flujo inicial de eventos de Event Grid.  **Este paso debe realizarse independientemente de la ejecución de los pasos 3. o 4.**
* Cree una instancia de [Azure Function que reciba y procese los mensajes de Service Bus](#receive-messages-using-azure-function) basados en eventos de Event Grid.
* Use [Logic Apps](#receive-messages-using-azure-logic-app).

## <a name="prerequisites"></a>requisitos previos

### <a name="service-bus-namespace"></a>Espacio de nombres de Service Bus

Cree un espacio de nombres premium de Service Bus. Cree un tema de Service Bus con dos suscripciones.

### <a name="code-to-send-message-to-the-service-bus-topic"></a>Código para enviar mensajes al tema de Service Bus

Puede usar cualquier medio para enviar un mensaje al tema de Service Bus. Como alternativa, puede usar el ejemplo de abajo. El ejemplo de código presupone que usa Visual Studio 2017.

Clone [este repositorio GitHub](https://github.com/Azure/azure-service-bus/).

Vaya a la siguiente carpeta:

\samples\DotNet\Microsoft.ServiceBus.Messaging\ServiceBusEventGridIntegration y abra el archivo: SBEventGridIntegration.sln.

A continuación, vaya al proyecto MessageSender y abra Program.cs.

![8][]

Rellene el nombre del tema y también la cadena de conexión y ejecute la aplicación de consola:

```CSharp
const string ServiceBusConnectionString = "YOUR CONNECTION STRING";
const string TopicName = "YOUR TOPIC NAME";
```

## <a name="test-function-setup"></a>Configuración de la función de prueba

Antes de trabajar en todo el escenario, puede que desee realizar una pequeña función de prueba para depurar y ver qué eventos fluyen.

En el portal, cree una nueva aplicación de Azure Functions. Siga este [vínculo](https://docs.microsoft.com/en-us/azure/azure-functions/) para obtener información sobre los conceptos básicos de Azure Functions.

En la función recién creada, haga clic en el signo más para agregar una función de desencadenador de http:

![2][]

![3][]

A continuación, copie el código siguiente en la función:

```CSharp
#r "Newtonsoft.Json"
using System.Net;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
{
    log.Info("C# HTTP trigger function processed a request.");
    // parse query parameter
    var content = req.Content;

    string jsonContent = await content.ReadAsStringAsync(); 
    log.Info($"Received Event with payload: {jsonContent}");

IEnumerable<string> headerValues;
if (req.Headers.TryGetValues("Aeg-Event-Type", out headerValues))
{
var validationHeaderValue = headerValues.FirstOrDefault();
if(validationHeaderValue == "SubscriptionValidation")
{
var events = JsonConvert.DeserializeObject<GridEvent[]>(jsonContent);
     var code = events[0].Data["validationCode"];
     return req.CreateResponse(HttpStatusCode.OK,
     new { validationResponse = code });
}
}

    return jsonContent == null
    ? req.CreateResponse(HttpStatusCode.BadRequest, "Pass a name on the query string or in the request body")
    : req.CreateResponse(HttpStatusCode.OK, "Hello " + jsonContent);
}

public class GridEvent
{
    public string Id { get; set; }
    public string EventType { get; set; }
    public string Subject { get; set; }
    public DateTime EventTime { get; set; }
    public Dictionary<string, string> Data { get; set; }
    public string Topic { get; set; }
}
```

Haga clic en Guardar y ejecutar.

## <a name="connect-function-and-namespace-via-event-grid"></a>Conexión de la función y el espacio de nombres mediante Event Grid

El paso siguiente consiste en asociar la función y el espacio de nombres de Service Bus. En este ejemplo, use Azure Portal. Consulte la página de [conceptos](service-bus-to-event-grid-integration-concept.md) para saber cómo se usa PowerShell o la CLI de Azure para lograr el mismo resultado.

Para crear una nueva suscripción a Azure Event Grid, vaya al espacio de nombres en Azure Portal y seleccione la hoja Event Grid. Haga clic en "+ Suscripción de eventos".

La siguiente captura de pantalla muestra un espacio de nombres que ya tiene varias suscripciones de Event Grid.

![20 ][]

La siguiente captura de pantalla muestra cómo suscribirse a una instancia de Azure Functions o a un webhook sin ningún filtro concreto. No olvide agregar el filtro relacionado para su suscripción de Service Bus como "Filtro de sufijo":

![21][]

Envíe un mensaje al tema de Service Bus como se mencionó en los requisitos previos y compruebe que los eventos fluyen mediante la característica de supervisión de Azure Functions.

![9][]

### <a name="receive-messages-using-azure-function"></a>Recepción de mensajes mediante Azure Functions

En la sección anterior, pudo observar un escenario de prueba y depuración sencillo y se aseguró de que los eventos fluían. En esta parte de la documentación podrá ver cómo recibir y procesar mensajes después de recibir un evento.

La razón para agregar una instancia de Azure Functions de la siguiente manera es que las funciones de Service Bus dentro del propio Azure Functions aún no admiten de forma nativa la nueva integración con Event Grid.

En la misma solución de Visual Studio que abrió en los requisitos previos, seleccione ReceiveMessagesOnEvent.cs. Especifique la cadena de conexión en el código:

![10][]

```Csharp
const string ServiceBusConnectionString = "YOUR CONNECTION STRING";
```

A continuación, vaya a Azure Portal y descargue el perfil de publicación de la instancia de Azure Functions que creó antes en [2. Configuración de la función de prueba](#2-test-function-setup).

![11][]

En Visual Studio, haga clic con el botón derecho en SBEventGridIntegration y seleccione Publicar. Use el perfil de publicación que descargó antes, seleccione Importar perfil y haga clic en Publicar.

![12][]

Una vez publicada la nueva instancia de Azure Functions, cree una nueva suscripción a Azure Event Grid que apunte a ella. Asegúrese de que aplica el filtro correcto "Termina con", que debe ser el nombre de la suscripción a Service Bus.

Después, envíe un mensaje al tema de Azure Service Bus que creó anteriormente y examine en el registro de Azure Functions del portal que los eventos fluyen y los mensajes se reciben.

![12-1][]

### <a name="receive-messages-using-azure-logic-app"></a>Recepción de mensajes mediante Azure Logic Apps

Las instrucciones siguientes muestran cómo conectar Azure Logic Apps junto con Azure Service Bus y Azure Event Grid:

En primer lugar, cree una nueva aplicación lógica en Azure Portal y seleccione Event Grid como acción de inicio.

![13][]

La vista inicial del diseñador de Logic Apps debería ser parecida a la de la siguiente captura de pantalla, aunque deberá reemplazar "Nombre del recurso" por su propio nombre del espacio de nombres. Asegúrese también de expandir las opciones avanzadas y agregar el filtro de sufijo de la suscripción:

![14][]

A continuación, agregue una acción de recepción para recibir de una suscripción de tema. La acción final debe ser similar a la siguiente captura de pantalla.

![15][]

A continuación, agregue un evento completo que debería ser parecido a este.

![16][]

Guarde la aplicación lógica y envíe un mensaje al tema de Service Bus como se mencionó en los requisitos previos. A continuación, observe la ejecución de la aplicación lógica. Si hace clic en "Información general" y, a continuación, en "Historial de ejecuciones", también puede obtener más datos acerca de la ejecución.

![17][]

![18][]

## <a name="next-steps"></a>Pasos siguientes

* Más información sobre [Azure Event Grid](https://docs.microsoft.com/azure/event-grid/).
* Más información acerca de [Azure Functions](https://docs.microsoft.com/azure/azure-functions/).
* Obtenga más información sobre [Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/).
* Aprenda más sobre [Azure Service Bus](https://docs.microsoft.com/azure/service-bus/).

[2]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid2.png
[3]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid3.png
[7]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid7.png
[8]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid8.png
[9]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid9.png
[10]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid10.png
[11]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid11.png
[12]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid12.png
[12-1]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid12-1.png
[12-2]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid12-2.png
[13]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid13.png
[14]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid14.png
[15]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid15.png
[16]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid16.png
[17]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid17.png
[18]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid18.png
[20]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal.png
[21]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal2.png
