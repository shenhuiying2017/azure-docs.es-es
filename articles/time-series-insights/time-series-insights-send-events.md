---
title: "Envío de eventos al entorno de Azure Time Series Insights | Microsoft Docs"
description: "En este tutorial se explica cómo crear y configurar el centro de eventos, y cómo ejecutar una aplicación de ejemplo para insertar eventos que se muestren en Azure Time Series Insights."
services: time-series-insights
ms.service: time-series-insights
author: venkatgct
ms.author: venkatja
manager: jhubbard
editor: MarkMcGeeAtAquent
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.devlang: csharp
ms.workload: big-data
ms.topic: article
ms.date: 11/15/2017
ms.openlocfilehash: 2c1b91fb87857eee8ca938be193b61e01bbdb886
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/16/2017
---
# <a name="send-events-to-a-time-series-insights-environment-using-event-hub"></a>Envío de eventos a un entorno de Time Series Insights mediante un centro de eventos
En este artículo se explica cómo crear y configurar el centro de eventos, y cómo ejecutar una aplicación de ejemplo para insertar eventos. Si tiene un centro de eventos con eventos en formato JSON, pase por alto este tutorial y vea su entorno en [Time Series Insights](https://insights.timeseries.azure.com).

## <a name="configure-an-event-hub"></a>Configuración de un centro de eventos
1. Para crear un centro de eventos, siga las instrucciones de la [documentación](../event-hubs/event-hubs-create.md) de Event Hubs.

2. Busque el **centro de eventos** en la barra de búsqueda. Haga clic en **Event Hubs** en la lista devuelta.

3. Haga clic en el nombre del centro de eventos para seleccionarlo.

4. En **Entidades**, en la ventana de configuración central, haga clic en **Event Hubs** de nuevo.

5. Seleccione el nombre del centro de eventos para configurarlo.

  ![Selección del grupo de consumidores del centro de eventos](media/send-events/consumer-group.png)

6. En **Entidades**, seleccione **Grupos de consumidores**.
 
7. Asegúrese de crear un grupo de consumidores que sea utilizado exclusivamente por el origen de eventos de Time Series Insights.

   > [!IMPORTANT]
   > Asegúrese de que el grupo de consumidores no es utilizado por ningún otro servicio (como un trabajo de Stream Analytics u otro entorno de Time Series Insights). Si otros servicios utilizan el grupo de consumidores, la operación de lectura se ve afectada negativamente en este entorno y en los otros servicios. Utilizar “$Default” como grupo de consumidores podría provocar que otros lectores lo reutilicen.

8. En el encabezado **Configuración**, seleccione **Directivas de acceso compartido**.

9. En el centro de eventos, cree **MySendPolicy**, que se usa para enviar eventos en el ejemplo de csharp.

  ![Seleccione Directivas de acceso compartido y haga clic en el botón Agregar](media/send-events/shared-access-policy.png)  

  ![Agregar nueva directiva de acceso compartido](media/send-events/shared-access-policy-2.png)  

## <a name="create-time-series-insights-event-source"></a>Creación de un origen de eventos de Time Series Insights
1. Si no ha creado un origen de eventos, siga [estas instrucciones](time-series-insights-how-to-add-an-event-source-eventhub.md) para crear un origen de eventos.

2. Especifique **deviceTimestamp** como nombre de la propiedad de marca de tiempo: esta propiedad se utiliza como marca de tiempo real en el ejemplo de C#. El nombre de la propiedad timestamp distingue mayúsculas de minúsculas y los valores deben tener el formato __yyyy-MM-ddTHH:mm:ss.FFFFFFFK__ cuando se envían como JSON al centro de eventos. Si la propiedad no existe en el evento, se utiliza la hora de puesta en la cola del centro de eventos.

  ![Creación de un origen de eventos](media/send-events/event-source-1.png)

## <a name="sample-code-to-push-events"></a>Código de ejemplo para insertar eventos
1. Vaya a la directiva del centro de eventos denominada **MySendPolicy**. Copie la **cadena de conexión** con la clave de la directiva.

  ![Copia de la cadena de conexión de MySendPolicy](media/send-events/sample-code-connection-string.png)

2. Ejecute el código siguiente para enviar 600 eventos para cada uno de los tres dispositivos. Reemplace `eventHubConnectionString` por su cadena de conexión.

```csharp
using System;
using System.Collections.Generic;
using System.Globalization;
using System.IO;
using Microsoft.ServiceBus.Messaging;

namespace Microsoft.Rdx.DataGenerator
{
    internal class Program
    {
        private static void Main(string[] args)
        {
            var from = new DateTime(2017, 4, 20, 15, 0, 0, DateTimeKind.Utc);
            Random r = new Random();
            const int numberOfEvents = 600;

            var deviceIds = new[] { "device1", "device2", "device3" };

            var events = new List<string>();
            for (int i = 0; i < numberOfEvents; ++i)
            {
                for (int device = 0; device < deviceIds.Length; ++device)
                {
                    // Generate event and serialize as JSON object:
                    // { "deviceTimestamp": "utc timestamp", "deviceId": "guid", "value": 123.456 }
                    events.Add(
                        String.Format(
                            CultureInfo.InvariantCulture,
                            @"{{ ""deviceTimestamp"": ""{0}"", ""deviceId"": ""{1}"", ""value"": {2} }}",
                            (from + TimeSpan.FromSeconds(i * 30)).ToString("o"),
                            deviceIds[device],
                            r.NextDouble()));
                }
            }

            // Create event hub connection.
            var eventHubConnectionString = @"Endpoint=sb://...";
            var eventHubClient = EventHubClient.CreateFromConnectionString(eventHubConnectionString);

            using (var ms = new MemoryStream())
            using (var sw = new StreamWriter(ms))
            {
                // Wrap events into JSON array:
                sw.Write("[");
                for (int i = 0; i < events.Count; ++i)
                {
                    if (i > 0)
                    {
                        sw.Write(',');
                    }
                    sw.Write(events[i]);
                }
                sw.Write("]");

                sw.Flush();
                ms.Position = 0;

                // Send JSON to event hub.
                EventData eventData = new EventData(ms);
                eventHubClient.Send(eventData);
            }
        }
    }
}

```
## <a name="supported-json-shapes"></a>Formas de JSON admitidas
### <a name="sample-1"></a>Ejemplo 1

#### <a name="input"></a>Entrada

Un objeto JSON simple.

```json
{
    "id":"device1",
    "timestamp":"2016-01-08T01:08:00Z"
}
```
#### <a name="output---1-event"></a>Salida: 1 evento

|id|timestamp|
|--------|---------------|
|device1|2016-01-08T01:08:00Z|

### <a name="sample-2"></a>Ejemplo 2

#### <a name="input"></a>Entrada
Una matriz JSON con dos objetos JSON. Cada objeto JSON se convertirá en un evento.
```json
[
    {
        "id":"device1",
        "timestamp":"2016-01-08T01:08:00Z"
    },
    {
        "id":"device2",
        "timestamp":"2016-01-17T01:17:00Z"
    }
]
```
#### <a name="output---2-events"></a>Salida: 2 eventos

|id|timestamp|
|--------|---------------|
|device1|2016-01-08T01:08:00Z|
|device2|2016-01-08T01:17:00Z|

### <a name="sample-3"></a>Ejemplo 3

#### <a name="input"></a>Entrada

Un objeto JSON con una matriz JSON anidada que contiene dos objetos JSON.
```json
{
    "location":"WestUs",
    "events":[
        {
            "id":"device1",
            "timestamp":"2016-01-08T01:08:00Z"
        },
        {
            "id":"device2",
            "timestamp":"2016-01-17T01:17:00Z"
        }
    ]
}

```
#### <a name="output---2-events"></a>Salida: 2 eventos
Tenga en cuenta que la propiedad "location" se copia en cada uno de los eventos.

|location|events.id|events.timestamp|
|--------|---------------|----------------------|
|WestUs|device1|2016-01-08T01:08:00Z|
|WestUs|device2|2016-01-08T01:17:00Z|

### <a name="sample-4"></a>Ejemplo 4

#### <a name="input"></a>Entrada

Un objeto JSON con una matriz JSON anidada que contiene dos objetos JSON. Esta entrada muestra que las propiedades globales se pueden representar mediante el objeto JSON complejo.

```json
{
    "location":"WestUs",
    "manufacturer":{
        "name":"manufacturer1",
        "location":"EastUs"
    },
    "events":[
        {
            "id":"device1",
            "timestamp":"2016-01-08T01:08:00Z",
            "data":{
                "type":"pressure",
                "units":"psi",
                "value":108.09
            }
        },
        {
            "id":"device2",
            "timestamp":"2016-01-17T01:17:00Z",
            "data":{
                "type":"vibration",
                "units":"abs G",
                "value":217.09
            }
        }
    ]
}
```
#### <a name="output---2-events"></a>Salida: 2 eventos

|location|manufacturer.name|manufacturer.location|events.id|events.timestamp|events.data.type|events.data.units|events.data.value|
|---|---|---|---|---|---|---|---|
|WestUs|manufacturer1|EastUs|device1|2016-01-08T01:08:00Z|pressure|psi|108.09|
|WestUs|manufacturer1|EastUs|device2|2016-01-08T01:17:00Z|vibration|abs G|217.09|

## <a name="next-steps"></a>Pasos siguientes
> [!div class="nextstepaction"]
> [Vea el entorno en el explorador de Time Series Insights](https://insights.timeseries.azure.com).
