---
title: Envío de eventos al entorno de Azure Time Series Insights | Microsoft Docs
description: En este tutorial se explica cómo crear y configurar el centro de eventos, y cómo ejecutar una aplicación de ejemplo para insertar eventos que se muestren en Azure Time Series Insights.
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
ms.date: 04/09/2018
ms.openlocfilehash: b418d1114cf6b906dcdee46bbf7e094cbc4a0521
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/28/2018
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

## <a name="add-time-series-insights-reference-data-set"></a>Incorporación del conjunto de datos de referencia de Time Series Insights 
Los datos de referencia en TSI contextualizan los datos de telemetría.  Ese contexto agrega significado a los datos y facilita el filtrado y el agregado.  TSI reúne los datos de referencia en el momento de la entrada y no los puede recopilar de manera retroactiva.  Por lo tanto, es fundamental agregar los datos de referencia antes de incorporar un origen de eventos con datos.  Datos como el tipo de ubicación o de sensor son dimensiones útiles que quizá quiera unir a un identificador de dispositivo/etiqueta/sensor para facilitar la segmentación y el filtrado.  

> [!IMPORTANT]
> Es fundamental que el conjunto de datos de referencia esté configurado al cargar datos históricos.

Asegúrese de que tiene datos de referencia en su lugar al cargar datos históricos en TSI de forma masiva.  Tenga en cuenta que TSI comienza la lectura del origen de eventos combinado inmediatamente cuando este tiene datos.  Es útil esperar a unir un origen de eventos a TSI cuando tenga los datos de referencia en su lugar, particularmente si el origen contiene datos. Como alternativa, puede esperar a insertar los datos en ese origen de eventos cuando el conjunto de datos de referencia se encuentre en su lugar.

Puede administrar los datos de referencia con la interfaz de usuario basada en web del Explorador de TSI o con la API de programación de C#. El Explorador de TSI tiene una experiencia de usuario visual para cargar archivos o pegar conjuntos de datos de referencia existentes en formato JSON o CSV. Con la API, puede compilar una aplicación personalizada cuando la necesite.

Para más información sobre la administración de datos de referencia en Time Series Insights, consulte el [artículo de datos de referencia](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-add-reference-data-set).

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
#### <a name="output---one-event"></a>Salida: un evento

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
#### <a name="output---two-events"></a>Salida: dos eventos

|id|timestamp|
|--------|---------------|
|device1|2016-01-08T01:08:00Z|
|device2|2016-01-08T01:17:00Z|

### <a name="sample-3"></a>Ejemplo 3

#### <a name="input"></a>Entrada

Un objeto JSON con una matriz JSON anidada que contiene dos objetos JSON:
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
#### <a name="output---two-events"></a>Salida: dos eventos
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
#### <a name="output---two-events"></a>Salida: dos eventos

|location|manufacturer.name|manufacturer.location|events.id|events.timestamp|events.data.type|events.data.units|events.data.value|
|---|---|---|---|---|---|---|---|
|WestUs|manufacturer1|EastUs|device1|2016-01-08T01:08:00Z|pressure|psi|108.09|
|WestUs|manufacturer1|EastUs|device2|2016-01-08T01:17:00Z|vibration|abs G|217.09|

### <a name="json-shaping-strategies"></a>Estrategias de moldeado de JSON
Vamos a usar el siguiente ejemplo de evento como punto de partida y comentaremos los problemas relacionados con él y estrategias para solucionarlos.

#### <a name="payload-1"></a>Carga útil 1:
```json
[{
            "messageId": "LINE_DATA",
            "deviceId": "FXXX",
            "timestamp": 1522355650620,
            "series": [{
                        "chId": 3,
                        "value": -3750.0
                  }, {
                        "chId": 13,
                        "value": 0.58015072345733643
                  }, {
                        "chId": 11,
                        "value": 800.0
                  }, {
                        "chId": 21,
                        "value": 0.0
                  }, {
                        "chId": 14,
                        "value": -999.0
                  }, {
                        "chId": 37,
                        "value": 2.445906400680542
                  }, {
                        "chId": 39,
                        "value": 0.0
                  }, {
                        "chId": 40,
                        "value": 1.0
                  }, {
                        "chId": 1,
                        "value": 1.0172575712203979
                  }
            ],
            "EventProcessedUtcTime": "2018-03-29T20:36:21.3245900Z",
            "PartitionId": 2,
            "EventEnqueuedUtcTime": "2018-03-29T20:34:11.0830000Z",
            "IoTHub": {
                  "MessageId": "<17xxx2xx-36x0-4875-9x1x-x428x41x1x68>",
                  "CorrelationId": "<x253x5xx-7xxx-4xx3-91x4-xxx3bx2xx0x3>",
                  "ConnectionDeviceId": "AAAA-ZZ-001",
                  "ConnectionDeviceGenerationId": "<123456789012345678>",
                  "EnqueuedTime": "2018-03-29T20:34:10.7990000Z",
                  "StreamId": null
            }
      }
]
 ```

Al insertar esta matriz de eventos como carga útil en TSI, se almacenará como un evento para cada medida. De esta manera se pueden crear eventos en exceso, lo cual no es recomendable. Tenga en cuenta que en TSI puede utilizar datos de referencia para agregar nombres descriptivos como propiedades.  Por ejemplo, puede crear conjuntos de datos de referencia con la propiedad clave = chId:  

chId  Medida               Unidad 24    Presión del aceite del motor   PSI 25    CALC Velocidad de la bomba        bbl/min

Para más información sobre la administración de datos de referencia en Time Series Insights, consulte el [artículo de datos de referencia](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-add-reference-data-set).

Otro problema con la primera carga útil es que la marca de tiempo se muestra en milisegundos. TSI acepta solo las marcas de tiempo con formato ISO. Una solución consiste en dejar el comportamiento predeterminado de la marca de tiempo en TSI, que consiste en usar la que se encuentra en cola.

Como alternativa a la carga útil anterior, echemos un vistazo a otro ejemplo.  

#### <a name="payload-2"></a>Carga útil 2:
```json
{
      "line": "Line01",
      "station": "Station 11",
      "gatewayid": "AAAA-ZZ-001",
      "deviceid": "F12XX",
      "timestamp": "2018-03-29T20:34:15.0000000Z",
      "STATE Engine State": 1,
      "unit": "NONE"
}, {
      "line": "Line01",
      "station": "Station 11",
      "gatewayid": "AAAA-ZZ-001",
      "deviceid": "MPC_AAAA-ZZ-001",
      "timestamp": "2018-03-29T20:34:15.0000000Z",
      "Well Head Px 1": -494162.8515625,
      "unit": "psi"
}, {
      "line": "Line01",
      "station": "Station 11",
      "gatewayid": "AAAA-ZZ-001",
      "deviceid": "F12XX",
      "timestamp": "2018-03-29T20:34:15.0000000Z",
      "CALC Pump Rate": 0,
      "unit": "bbl/min"
}, {
      "line": "Line01",
      "station": "Station 11",
      "gatewayid": "AAAA-ZZ-001",
      "deviceid": "F12XX",
      "timestamp": "2018-03-29T20:34:15.0000000Z",
      "Engine Fuel Pressure": 0,
      "unit": "psi"
}, {
      "line": "Line01",
      "station": "Station 11",
      "gatewayid": "AAAA-ZZ-001",
      "deviceid": "F12XX",
      "timestamp": "2018-03-29T20:34:15.0000000Z",
      "Engine Oil Pressure": 0.58015072345733643,
      "unit": "psi"
}
```

Al igual que la carga útil 1, TSI almacenará cada medida como evento único.  La diferencia notable es que TSI lee la *marca de tiempo* aquí correctamente, en formato ISO.  

Si necesita reducir el número de eventos enviados, puede enviar la información de la siguiente manera.  

#### <a name="payload-3"></a>Carga útil 3:
```json
{
      "line": "Line01",
      "station": "Station 11",
      "gatewayid": "AAAA-ZZ-001",
      "deviceid": "F12XX",
      "timestamp": "2018-03-29T20:34:15.0000000Z",
      "CALC Pump Rate": 0,
      "CALC Pump Rate.unit": "bbl/min"
      "Engine Oil Pressure": 0.58015072345733643,
      "Engine Oil Pressure.unit": "psi"
      "Engine Fuel Pressure": 0,
      "Engine Fuel Pressure.unit": "psi"
}
```
A continuación se muestra una última sugerencia.

#### <a name="payload-4"></a>Carga útil 4:
```json
{
              "line": "Line01",
              "station": "Station 11",
              "gatewayid": "AAAA-ZZ-001",
              "deviceid": "F12XX",
              "timestamp": "2018-03-29T20:34:15.0000000Z",
              "CALC Pump Rate": {
                           "value": 0,
                           "unit": "bbl/min"
              },
              "Engine Oil Pressure": {
                           "value": 0.58015072345733643,
                           "unit": "psi"
              },
              "Engine Fuel Pressure": {
                           "value": 0,
                           "unit": "psi"
              }
}
```

En este ejemplo se muestra la salida después de eliminar la estructura de JSON:

```json
{
      "line": "Line01",
      "station": "Station 11",,
      "gatewayid": "AAAA-ZZ-001",
      "deviceid": "F12XX",
      "timestamp": "2018-03-29T20:34:15.0000000Z",
      "CALC Pump Rate.value": 0,
      "CALC Pump Rate.unit": "bbl/min"
      "Engine Oil Pressure.value": 0.58015072345733643,
      "Engine Oil Pressure.unit": "psi"
      "Engine Fuel Pressure.value": 0,
      "Engine Fuel Pressure.unit": "psi"
}
```

Puede definir libremente propiedades diferentes para los distintos canales del objeto json propio y mantener al mismo tiempo un número de eventos reducido. Este enfoque de eliminación de estructura ocupa más espacio, es importante tenerlo en cuenta. La capacidad de TSI se basa tanto en los eventos como en el tamaño, lo que se cumpla primero.

## <a name="next-steps"></a>Pasos siguientes
> [!div class="nextstepaction"]
> [Vea el entorno en el explorador de Time Series Insights](https://insights.timeseries.azure.com).
