---
title: "Envío de eventos al entorno de Azure Time Series Insights | Microsoft Docs"
description: "Este tutorial describe cómo se insertan eventos en el entorno de Time Series Insights"
keywords: 
services: time-series-insights
documentationcenter: 
author: venkatgct
manager: almineev
editor: cgronlun
ms.assetid: 
ms.service: time-series-insights
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/21/2017
ms.author: venkatja
translationtype: Human Translation
ms.sourcegitcommit: 1cc1ee946d8eb2214fd05701b495bbce6d471a49
ms.openlocfilehash: 92e3e64f235e165a6a1772b6e1724789f3ec3049
ms.lasthandoff: 04/26/2017

---
# <a name="send-events-to-a-time-series-insights-environment-via-event-hub"></a>Envío de eventos a un entorno de Time Series Insights mediante un centro de eventos

Este tutorial explica cómo crear y configurar el centro de eventos y ejecutar una aplicación de ejemplo para insertar eventos. Si tiene un centro de eventos existente que ya tiene eventos en formato JSON, puede omitir este tutorial y ver su entorno en el [explorador de Time Series Insights](https://insights.timeseries.azure.com).

## <a name="configure-an-event-hub"></a>Configuración de un centro de eventos
1. Para crear un centro de eventos, siga las instrucciones de la [documentación](https://docs.microsoft.com/azure/event-hubs/event-hubs-create) de Event Hubs.

2. Asegúrese de crear un grupo de consumidores que sea utilizado exclusivamente por el origen de eventos de Time Series Insights.

  > [!IMPORTANT]
  > Asegúrese de que el grupo de consumidores no es utilizado por ningún otro servicio (como un trabajo de Stream Analytics u otro entorno de Time Series Insights). Si otros servicios utilizan el grupo de consumidores, la operación de lectura se ve afectada negativamente en este entorno y en los otros servicios. Utilizar “$Default” como grupo de consumidores podría provocar que otros lectores lo reutilicen.

  ![Selección del grupo de consumidores del centro de eventos](media/send-events/consumer-group.png)

3. En el centro de eventos, cree "MySendPolicy", que se usa para enviar eventos en el ejemplo siguiente.

  ![Seleccione Directivas de acceso compartido y haga clic en el botón Agregar](media/send-events/shared-access-policy.png)  

  ![Agregar nueva directiva de acceso compartido](media/send-events/shared-access-policy-2.png)  

## <a name="create-time-series-insights-event-source"></a>Creación de un origen de eventos de Time Series Insights
1. Si no ha creado el origen de eventos, siga las instrucciones que se especifican [aquí](time-series-insights-add-event-source.md) para crear un origen de eventos.

2. Especifique "deviceTimestamp" como nombre de la propiedad de marca de tiempo: esta propiedad se utiliza como la marca de tiempo real en el ejemplo siguiente. El nombre de la propiedad de marca de tiempo distingue mayúsculas de minúsculas y los valores deben tener el formato __yyyy-MM-ddTHH:mm:ss.FFFFFFFK__ cuando se envían como JSON al centro de eventos. Si la propiedad no existe en el evento, se utiliza la hora en la que el evento estuvo en la cola del centro de eventos.

  ![Creación de un origen de eventos](media/send-events/event-source-1.png)

## <a name="run-sample-code-to-push-events"></a>Ejecución del código de ejemplo para insertar eventos
1. Vaya a la directiva del centro de eventos "MySendPolicy" y copie la cadena de conexión con la clave de directiva.

  ![Copia de la cadena de conexión de MySendPolicy](media/send-events/sample-code-connection-string.png)

2. Ejecute el código siguiente que va a enviar 600 eventos a cada uno de los tres dispositivos. Reemplace `eventHubConnectionString` por su cadena de conexión.

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
    "deviceId":"device1",
    "deviceTimestamp":"2016-01-08T01:08:00Z"
}
```
#### <a name="output---1-event"></a>Salida: 1 evento

|deviceId|deviceTimestamp|
|--------|---------------|
|device1|2016-01-08T01:08:00Z|

### <a name="sample-2"></a>Ejemplo 2

#### <a name="input"></a>Entrada
Una matriz JSON con dos objetos JSON. Cada objeto JSON se convertirá en un evento.
```json
[
    {
        "deviceId":"device1",
        "deviceTimestamp":"2016-01-08T01:08:00Z"
    },
    {
        "deviceId":"device2",
        "deviceTimestamp":"2016-01-17T01:17:00Z"
    }
]
```
#### <a name="output---2-events"></a>Salida: 2 eventos

|deviceId|deviceTimestamp|
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
            "deviceId":"device1",
            "deviceTimestamp":"2016-01-08T01:08:00Z"
        },
        {
            "deviceId":"device2",
            "deviceTimestamp":"2016-01-17T01:17:00Z"
        }
    ]
}

```
#### <a name="output---2-events"></a>Salida: 2 eventos
Tenga en cuenta que la propiedad "location" se copia en cada uno de los eventos.

|location|events.deviceId|events.deviceTimestamp|
|--------|---------------|----------------------|
|WestUs|device1|2016-01-08T01:08:00Z|
|WestUs|device2|2016-01-08T01:17:00Z|

### <a name="sample-4"></a>Ejemplo 4

#### <a name="input"></a>Entrada

```json
{
    "location":"WestUs",
    "manufacturerInfo":{
        "name":"manufacturer1",
        "location":"EastUs"
    },
    "events":[
        {
            "deviceId":"device1",
            "deviceTimestamp":"2016-01-08T01:08:00Z",
            "deviceData":{
                "type":"pressure",
                "units":"psi",
                "value":108.09
            }
        },
        {
            "deviceId":"device2",
            "deviceTimestamp":"2016-01-17T01:17:00Z",
            "deviceData":{
                "type":"vibration",
                "units":"abs G",
                "value":217.09
            }
        }
    ]
}
```
#### <a name="output---2-events"></a>Salida: 2 eventos

|location|manufacturerInfo.name|manufacturerInfo.location|events.deviceId|events.deviceTimestamp|events.deviceData.type|events.deviceData.units|events.deviceData.value|
|---|---|---|---|---|---|---|---|
|WestUs|manufacturer1|EastUs|device1|2016-01-08T01:08:00Z|pressure|psi|108.09|
|WestUs|manufacturer1|EastUs|device1|2016-01-08T01:17:00Z|vibration|abs G|217.09|

## <a name="next-steps"></a>Pasos siguientes

* Vea el entorno en el [portal de Time Series Insights](https://insights.timeseries.azure.com)

