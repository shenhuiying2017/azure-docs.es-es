---
title: Ejemplos de integración de Azure Service Bus en Event Grid | Microsoft Docs
description: Este artículo proporciona ejemplos de integración de la mensajería de Service Bus y Event Grid.
services: service-bus-messaging
documentationcenter: .net
author: ChristianWolf42
manager: timlt
editor: ''
ms.assetid: f99766cb-8f4b-4baf-b061-4b1e2ae570e4
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: get-started-article
ms.date: 02/15/2018
ms.author: chwolf
ms.openlocfilehash: 5d0ab8cf9e87fc13b78b00dbe77ec6f9fb38c4b9
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/28/2018
---
# <a name="azure-service-bus-to-azure-event-grid-integration-examples"></a>Ejemplos de integración de Azure Service Bus en Azure Event Grid

En este artículo, aprenderá a configurar una función de Azure y una aplicación lógica que reciben mensajes basados en la recepción de un evento de Azure Event Grid. Puede realizar las siguientes acciones:
 
* Crear una [sencilla función de Azure de prueba](#test-function-setup) para depurar y ver el flujo inicial de eventos de Event Grid. Realice este paso, independientemente de si va a realizar o no los demás.
* Crear una [función de Azure para recibir y procesar los mensajes de Azure Service Bus](#receive-messages-using-azure-function) basados en eventos de Event Grid.
* Utilizar la [característica Logic Apps de Azure App Service](#receive-messages-using-azure-logic-app).

En el ejemplo que cree se da por supuesto que el tema de Service Bus tiene dos suscripciones. El ejemplo también supone que la suscripción de Event Grid se creó para enviar eventos para una sola suscripción de Service Bus. 

En el ejemplo, se envían mensajes al tema de Service Bus y, a continuación, se comprueba que se ha generado el evento para esta suscripción de Service Bus. La función o aplicación lógica recibe los mensajes de la suscripción de Service Bus y la completa.

## <a name="prerequisites"></a>requisitos previos
Antes de comenzar, asegúrese de que ha completado los pasos descritos en las dos secciones siguientes.

### <a name="create-a-service-bus-namespace"></a>Creación de un espacio de nombres de Service Bus

Cree un espacio de nombres Premium de Service Bus y cree un tema de Service Bus que tenga dos suscripciones.

### <a name="send-a-message-to-the-service-bus-topic"></a>Envío de un mensaje al tema de Service Bus

Puede usar cualquier método para enviar un mensaje al tema de Service Bus. El código de ejemplo al final de este procedimiento presupone que usa Visual Studio 2017.

1. Clone [el repositorio azure-service-bus de GitHub](https://github.com/Azure/azure-service-bus/).

2. En Visual Studio,acuda a la carpeta *\samples\DotNet\Microsoft.ServiceBus.Messaging\ServiceBusEventGridIntegration* y abra el archivo *SBEventGridIntegration.sln*.

3. Vaya al proyecto **MessageSender** y, a continuación, seleccione **Program.cs**.

   ![8][]

4. Rellene el nombre del tema y la cadena de conexión y luego, ejecute el siguiente código de aplicación de consola:

    ```CSharp
    const string ServiceBusConnectionString = "YOUR CONNECTION STRING";
    const string TopicName = "YOUR TOPIC NAME";
    ```

## <a name="set-up-a-test-function"></a>Configuración de una función de prueba

Antes de trabajar en todo el escenario, configure por lo menos una pequeña función de prueba que puede utilizar para depurar y ver qué eventos fluyen.

1. En Azure Portal cree una nueva aplicación de Azure Functions. Para más información sobre los conceptos básicos de Azure Functions, consulte [Documentación de Azure Functions](https://docs.microsoft.com/azure/azure-functions/).

2. En la función recién creada, haga clic en el signo más (+) para agregar una función de desencadenador de HTTP:

    ![2][]
    
    Se abre la ventana **Empiece a trabajar rápidamente con una función predefinida**.

    ![3][]

3. Seleccione el botón **Webhook y API**, **CSharp** y seleccione **Crear esta función**.
 
4. Pegue el código siguiente en la función:

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

5. Seleccione **Guardar y ejecutar**.

## <a name="connect-the-function-and-namespace-via-event-grid"></a>Conexión de la función y el espacio de nombres mediante Event Grid

En esta sección va a asociar la función y el espacio de nombres de Service Bus. En este ejemplo, use Azure Portal. Para entender cómo usar PowerShell o la CLI de Azure para llevar a cabo este procedimiento, consulte [Introducción a la integración de Azure Service Bus en Azure Event Grid](service-bus-to-event-grid-integration-concept.md).

Para crear una suscripción de Azure Event Grid, haga lo siguiente:
1. En Azure Portal, vaya al espacio de nombres y, a continuación, en el panel izquierdo, seleccione **Event Grid**.  
    Se abre la ventana del espacio de nombres, con dos suscripciones de Event Grid que se muestran en el panel derecho.

    ![20][]

2. Seleccione **Suscripción de eventos**.  
    Se abre la ventana **Suscripción de eventos**. La siguiente imagen muestra un formulario para suscribirse a una función de Azure o un webhook sin aplicar los filtros.

    ![21][]

3. Rellene el formulario tal y como se muestra y, en el cuadro **Filtro de sufijo**, no olvide especificar el filtro apropiado.

4. Seleccione **Crear**.

5. Envíe un mensaje al tema de Service Bus como se mencionó en la sección "Requisitos previos" y compruebe que los eventos fluyen a través de la característica de Supervisión de Azure Functions.

El paso siguiente consiste en asociar la función y el espacio de nombres de Service Bus. En este ejemplo, use Azure Portal. Para aprender a usar PowerShell o la CLI de Azure para realizar este paso, consulte [Introducción a la integración de Azure Service Bus en Azure Event Grid](service-bus-to-event-grid-integration-concept.md).

![9][]

### <a name="receive-messages-by-using-azure-functions"></a>Recepción de mensajes mediante Azure Functions

En la sección anterior pudo observar un escenario de prueba y depuración sencillo, y se aseguró de que los eventos fluían. 

En esta sección aprenderá cómo recibir y procesar los mensajes una vez que reciba un evento.

Agregará una función de Azure, como se muestra en el siguiente ejemplo, porque las funciones de Service Bus dentro de Azure Functions aún no admiten de forma nativa la nueva integración con Event Grid.

1. En la misma solución de Visual Studio que abrió en los requisitos previos, seleccione **ReceiveMessagesOnEvent.cs**. 

    ![10][]

2. Especifique la cadena de conexión en el código siguiente:

    ```Csharp
    const string ServiceBusConnectionString = "YOUR CONNECTION STRING";
    ```

3. En Azure Portal, descargue el perfil de publicación para la función de Azure que creó en la sección "Configuración de una función de prueba".

    ![11][]

4. En Visual Studio, haga clic con el botón derecho en **SBEventGridIntegration** y seleccione **Publicar**. 

5. En el panel **Publicar** para el perfil de publicación que descargó anteriormente, seleccione **Importar perfil** y, a continuación, seleccione **Publicar**.

    ![12][]

6. Una vez publicada la nueva función de Azure, cree una nueva suscripción de Azure Event Grid que apunte a ella.  
    Asegúrese de que en el cuadro **Ends with** (Termina con) aplica el filtro correcto, que debe ser el nombre de la suscripción a Service Bus.

7. Envíe un mensaje al tema de Azure Service Bus que creó anteriormente, y examine el registro de Azure Functions de Azure Portal para asegurarse de que los eventos fluyen y los mensajes se reciben.

    ![12-1][]

### <a name="receive-messages-by-using-logic-apps"></a>Recepción de mensajes mediante Logic Apps

Conecte una aplicación lógica con Azure Service Bus y Azure Event Grid haciendo lo siguiente:

1. Cree una nueva aplicación lógica en Azure Portal y seleccione **Event Grid** como acción de inicio.

    ![13][]

    Se abre la ventana del diseñador de Logic Apps.

    ![14][]

2. Agregue su información haciendo lo siguiente:

    a. En el cuadro **Nombre de recurso**, escriba el nombre de su espacio de nombres. 

    b. En **Opciones avanzadas**, en el cuadro **Filtro de sufijo**, escriba el filtro para la suscripción.

3. Agregue una acción de recepción de Service Bus para recibir de una suscripción al tema.  
    La acción final se muestra en la imagen siguiente:

    ![15][]

4. Agregue un evento completo tal como se muestra en la imagen siguiente:

    ![16][]

5. Guarde la aplicación lógica y envíe un mensaje al tema de Service Bus como se mencionó en la sección "Requisitos previos".  
    Observe la ejecución de la aplicación lógica. Para ver más datos para la ejecución, seleccione **Introducción** y, a continuación, vea los datos en **Historial de ejecuciones**.

    ![17][]

    ![18][]

## <a name="next-steps"></a>Pasos siguientes

* Más información sobre [Azure Event Grid](https://docs.microsoft.com/azure/event-grid/).
* Más información acerca de [Azure Functions](https://docs.microsoft.com/azure/azure-functions/).
* Más información acerca de la [característica Logic Apps de Azure App Service](https://docs.microsoft.com/azure/logic-apps/).
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
