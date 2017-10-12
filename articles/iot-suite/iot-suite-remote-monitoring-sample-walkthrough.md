---
title: "Tutorial de la solución preconfigurada de supervisión remota | Microsoft Docs"
description: "Una descripción de la supervisión remota de la solución preconfigurada de IoT de Azure y su arquitectura."
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 31fe13af-0482-47be-b4c8-e98e36625855
ms.service: iot-suite
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/24/2017
ms.author: dobett
ms.openlocfilehash: b28105f300723b542fa6d1aebc569439d5c73dc4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="remote-monitoring-preconfigured-solution-walkthrough"></a>Tutorial de la solución preconfigurada de supervisión remota

La [solución preconfigurada][lnk-preconfigured-solutions] de supervisión remota del Conjunto de aplicaciones de IoT es una implementación de una solución de supervisión integral para varias máquinas en ubicaciones remotas. La solución combina servicios clave de Azure para proporcionar una implementación genérica del escenario de negocio. Puede usar la solución como punto de partida para su propia implementación, así como [personalizarla][lnk-customize] para cubrir sus requisitos empresariales específicos.

Este artículo le guiará a través de algunos de los elementos clave de la solución de supervisión remota para que pueda entender cómo funciona. Esta información le ayuda a:

* Solucionar problemas de la solución.
* Planear cómo personalizar la solución para satisfacer sus propios requisitos específicos. 
* Diseñar una solución de IoT propia que utilice servicios de Azure.

## <a name="logical-architecture"></a>Arquitectura lógica

El diagrama siguiente describe los componentes lógicos de la solución preconfigurada:

![Arquitectura lógica](media/iot-suite-remote-monitoring-sample-walkthrough/remote-monitoring-architecture.png)

## <a name="simulated-devices"></a>Dispositivos simulados

En la solución preconfigurada, el dispositivo simulado representa un dispositivo de refrigeración (por ejemplo, el aire acondicionado del edificio o una unidad de tratamiento de aire en instalaciones). Al implementar la solución preconfigurada, también aprovisiona automáticamente cuatro dispositivos simulados que se ejecutan en [Azure WebJob][lnk-webjobs]. Los dispositivos simulados facilitan la experimentación del comportamiento de la solución sin necesidad de implementar ningún dispositivo físico. Para implementar un dispositivo físico real, consulte el tutorial [Conectar el dispositivo a la solución preconfigurada de supervisión remota (Windows)][lnk-connect-rm].

### <a name="device-to-cloud-messages"></a>Mensajes de dispositivo a nube

Cada dispositivo simulado puede enviar los siguientes tipos de mensajes al Centro de IoT:

| Message | Description |
| --- | --- |
| Inicio |Cuando el dispositivo se inicia, envía un mensaje de **información del dispositivo** que contiene información acerca de sí mismo al back-end. Estos datos incluyen el identificador de dispositivo y una lista de los comandos y los métodos que admite. |
| Presencia |Un dispositivo envía periódicamente un mensaje de **presencia** para notificar si el dispositivo puede detectar o no la presencia de un sensor. |
| Telemetría |Un dispositivo envía periódicamente un mensaje de **telemetría** que informa de los valores simulados de temperatura y humedad recopilados a partir de los sensores simulados del dispositivo. |

> [!NOTE]
> La solución almacena la lista de comandos admitidos por el dispositivo en una base de datos de Cosmos DB, no en el dispositivo gemelo.

### <a name="properties-and-device-twins"></a>Propiedades y dispositivos gemelos

Los dispositivos simulados envían las siguientes propiedades del dispositivo al [gemelo][lnk-device-twins] en IoT Hub como *propiedades notificadas*. El dispositivo envía propiedades notificadas al iniciarse y en respuesta a un comando o un método **Change Device State**.

| Propiedad | Propósito |
| --- | --- |
| Config.TelemetryInterval | Frecuencia (segundos) con la que el dispositivo envía la telemetría |
| Config.TemperatureMeanValue | Especifica el valor medio de la telemetría de temperatura simulada |
| Device.DeviceID |Identificador que se proporciona o asigna cuando se crea un dispositivo en la solución |
| Device.DeviceState | Estado notificado por el dispositivo |
| Device.CreatedTime |La hora en la que se creó el dispositivo en la solución |
| Device.StartupTime |Hora en que se inició el dispositivo |
| Device.LastDesiredPropertyChange |El número de versión del último cambio de propiedad deseado |
| Device.Location.Latitude |Ubicación de la latitud del dispositivo |
| Device.Location.Longitude |Ubicación de la longitud del dispositivo |
| System.Manufacturer |Fabricante del dispositivo |
| System.ModelNumber |Número de modelo del dispositivo |
| System.SerialNumber |Número de serie del dispositivo |
| System.FirmwareVersion |Versión actual del firmware del dispositivo |
| System.Platform |Arquitectura de la plataforma del dispositivo |
| System.Processor |Procesador que ejecuta el dispositivo |
| System.InstalledRAM |Cantidad de RAM instalada en el dispositivo |

El simulador propaga estas propiedades en los dispositivos simulados con valores de ejemplo. Cada vez que el simulador inicializa un dispositivo simulado, el dispositivo envía los metadatos previamente definidos a IoT Hub como propiedades notificadas. Solo el dispositivo puede actualizar las propiedades notificadas. Para cambiar una propiedad notificada, establezca una propiedad deseada en el portal de la solución. Es responsabilidad del dispositivo:

1. Recuperar de forma periódica las propiedades deseadas desde IoT Hub.
2. Actualizar su configuración con el valor de la propiedad deseada.
3. Devolver el nuevo valor al concentrador como una propiedad notificada.

Desde el panel de la solución, puede usar las *propiedades deseadas* para establecer propiedades en un dispositivo mediante el [dispositivo gemelo][lnk-device-twins]. Normalmente, un dispositivo lee un valor de la propiedad deseada desde el concentrador para actualizar su estado interno y notificar el cambio de vuelta como una propiedad notificada.

> [!NOTE]
> El código del dispositivo simulado solo usa las propiedades deseadas **Desired.Config.TemperatureMeanValue** y **Desired.Config.TelemetryInterval** para actualizar las propiedades notificadas devueltas a IoT Hub. Todas las demás solicitudes de cambio de la propiedad deseada se omiten en el dispositivo simulado.

### <a name="methods"></a>Métodos

Los dispositivos simulados pueden controlar los siguientes métodos ([métodos directos][lnk-direct-methods]) invocados desde el portal de la solución a través de IoT Hub:

| Método | Descripción |
| --- | --- |
| InitiateFirmwareUpdate |Indica al dispositivo que realice una actualización del firmware |
| Reboot |Indica al dispositivo que se reinicie |
| FactoryReset |Indica al dispositivo que realice un restablecimiento de fábrica |

Algunos métodos utilizan las propiedades notificadas para informar sobre el progreso. Por ejemplo, el método **InitiateFirmwareUpdate** simula la ejecución de la actualización de forma asincrónica en el dispositivo. El método vuelve inmediatamente al dispositivo, mientras la tarea asincrónica continúa devolviendo las actualizaciones de estado al panel de la solución con las propiedades notificadas.

### <a name="commands"></a>Comandos:

Los dispositivos simulados pueden controlar los siguientes comandos (mensajes de la nube al dispositivo) enviados desde el portal de la solución a través de IoT Hub:

| Comando | Description |
| --- | --- |
| PingDevice |Envía un *ping* al dispositivo para comprobar si está activo |
| StartTelemetry |Inicia el dispositivo que envía la telemetría. |
| StopTelemetry |Detiene el dispositivo que envía la telemetría. |
| ChangeSetPointTemp |Cambia el valor del punto fijo alrededor del cual se generan los datos aleatorios. |
| DiagnosticTelemetry |Desencadena el simulador de dispositivos para enviar un valor de telemetría adicional (externalTemp). |
| ChangeDeviceState |Cambia una propiedad de estado extendido del dispositivo y envía el mensaje de información de dispositivo desde el dispositivo. |

> [!NOTE]
> Para ver una comparación de estos comandos (mensajes de la nube al dispositivo) y métodos (métodos directos), consulte [Guía de comunicación de nube a dispositivo][lnk-c2d-guidance].

## <a name="iot-hub"></a>IoT Hub

[IoT hub][lnk-iothub] ingiere los datos enviados desde los dispositivos en la nube y los pone a disposición de los trabajos de Azure Stream Analytics (ASA). Cada trabajo de transmisión de ASA utiliza un grupo de consumidores independiente del Centro de IoT para leer la transmisión de mensajes de los dispositivos.

La instancia de IoT Hub en la solución también:

- Mantiene un registro de identidades que almacena los identificadores y las claves de autenticación de todos los dispositivos que se pueden conectar al portal. Puede habilitar y deshabilitar dispositivos a través del registro de identidades.
- Envía comandos a los dispositivos en nombre del portal de la solución.
- Invoca los métodos de los dispositivos en nombre del portal de la solución.
- Mantiene los dispositivos gemelos para todos los dispositivos registrados. Un dispositivo gemelo almacena los valores de la propiedad notificados por un dispositivo. Un dispositivo gemelo también almacena las propiedades deseadas, establecidas en el portal de la solución, para que el dispositivo las recupere la siguiente vez que se conecte.
- Programa trabajos para establecer las propiedades de varios dispositivos o invocar métodos en varios dispositivos.

## <a name="azure-stream-analytics"></a>Análisis de transmisiones de Azure

En la solución de supervisión remota, [Azure Stream Analytics][lnk-asa] (ASA) envía los mensajes de los dispositivos recibidos por IoT Hub a otros componentes back-end para su procesamiento o almacenamiento. Diferentes trabajos de ASA realizan funciones específicas en función del contenido de los mensajes.

**Trabajo 1: información de dispositivo** filtra los mensajes de información del dispositivo desde la transmisión de mensajes entrantes y los envía a un punto de conexión de Centro de eventos. Un dispositivo envía mensajes de información de dispositivo al inicio y como respuesta a un comando **SendDeviceInfo** . Este trabajo utiliza la siguiente definición de consulta para identificar mensajes de **información de dispositivo** :

```
SELECT * FROM DeviceDataStream Partition By PartitionId WHERE  ObjectType = 'DeviceInfo'
```

Este trabajo envía su resultado a un centro de eventos para su posterior procesamiento.

**Trabajo 2: reglas** evalúa los valores de telemetría de temperatura y humedad entrantes según los umbrales por dispositivo. Los valores de umbral se establecen en el editor de reglas disponible en el panel de la solución. Cada par de valor/dispositivo se almacena en la marca de tiempo de un blob que se lee en Análisis de transmisiones como **datos de referencia**. El trabajo compara cualquier valor no vacío con el umbral establecido para el dispositivo. Si supera la condición '>', el trabajo tendrá como resultado un evento de **alarma** que indica que se superó el umbral y proporciona los valores de dispositivo, valor y marca de tiempo. Este trabajo utiliza la definición de la consulta siguiente para identificar los mensajes de telemetría que desencadenan una alarma:

```
WITH AlarmsData AS 
(
SELECT
     Stream.IoTHub.ConnectionDeviceId AS DeviceId,
     'Temperature' as ReadingType,
     Stream.Temperature as Reading,
     Ref.Temperature as Threshold,
     Ref.TemperatureRuleOutput as RuleOutput,
     Stream.EventEnqueuedUtcTime AS [Time]
FROM IoTTelemetryStream Stream
JOIN DeviceRulesBlob Ref ON Stream.IoTHub.ConnectionDeviceId = Ref.DeviceID
WHERE
     Ref.Temperature IS NOT null AND Stream.Temperature > Ref.Temperature

UNION ALL

SELECT
     Stream.IoTHub.ConnectionDeviceId AS DeviceId,
     'Humidity' as ReadingType,
     Stream.Humidity as Reading,
     Ref.Humidity as Threshold,
     Ref.HumidityRuleOutput as RuleOutput,
     Stream.EventEnqueuedUtcTime AS [Time]
FROM IoTTelemetryStream Stream
JOIN DeviceRulesBlob Ref ON Stream.IoTHub.ConnectionDeviceId = Ref.DeviceID
WHERE
     Ref.Humidity IS NOT null AND Stream.Humidity > Ref.Humidity
)

SELECT *
INTO DeviceRulesMonitoring
FROM AlarmsData

SELECT *
INTO DeviceRulesHub
FROM AlarmsData
```

El trabajo envía su resultado a un centro de eventos para su posterior procesamiento y guarda los detalles de cada alerta en Blob Storage, en donde el portal de la solución puede leer la información de la alerta.

**Trabajo 3: Telemetría** opera en el flujo de la telemetría del dispositivo entrante de dos formas distintas. La primera envía todos los mensajes de telemetría desde los dispositivos al almacenamiento de blobs persistente para su almacenamiento a largo plazo. La segunda calcula los valores de humedad medios, mínimos y máximos sobre una ventana deslizante de cinco minutos y envía estos datos al almacenamiento de blobs. El portal de la solución lee los datos de telemetría de Blob Storage para rellenar los gráficos. Este trabajo utiliza la siguiente definición de consulta:

```
WITH 
    [StreamData]
AS (
    SELECT
        *
    FROM [IoTHubStream]
    WHERE
        [ObjectType] IS NULL -- Filter out device info and command responses
) 

SELECT
    IoTHub.ConnectionDeviceId AS DeviceId,
    Temperature,
    Humidity,
    ExternalTemperature,
    EventProcessedUtcTime,
    PartitionId,
    EventEnqueuedUtcTime,
    * 
INTO
    [Telemetry]
FROM
    [StreamData]

SELECT
    IoTHub.ConnectionDeviceId AS DeviceId,
    AVG (Humidity) AS [AverageHumidity],
    MIN(Humidity) AS [MinimumHumidity],
    MAX(Humidity) AS [MaxHumidity],
    5.0 AS TimeframeMinutes 
INTO
    [TelemetrySummary]
FROM [StreamData]
WHERE
    [Humidity] IS NOT NULL
GROUP BY
    IoTHub.ConnectionDeviceId,
    SlidingWindow (mi, 5)
```

## <a name="event-hubs"></a>Centros de eventos

Los trabajos de **información del dispositivo** y **reglas** de ASA, envían sus datos a los centros de eventos para que los reenvíe de forma confiable al **procesador de eventos** que se ejecuta en WebJob.

## <a name="azure-storage"></a>Almacenamiento de Azure

La solución utiliza el almacenamiento de blobs de Azure para conservar todos los datos de telemetría resumidos y sin procesar de los dispositivos de la solución. El portal lee los datos de telemetría de Blob Storage para rellenar los gráficos. Para mostrar las alertas, el portal de la solución lee los datos de Blob Storage que registran cuándo los valores de telemetría superan los valores de umbral configurados. La solución también utiliza Blob Storage para registrar los valores de umbral establecidos en el portal de la solución.

## <a name="webjobs"></a>Trabajos web

Además de hospedar los simuladores de dispositivos, las instancias de WebJobs de la solución también hospedan el **procesador de eventos** que se ejecuta en una instancia de Azure WebJob que controla las respuestas de los comandos. Utiliza mensajes de respuesta de los comandos para actualizar el historial de los comandos de dispositivo (almacenado en la base de datos de Cosmos DB).

## <a name="cosmos-db"></a>Cosmos DB

La solución utiliza una base de datos de Cosmos DB para almacenar información sobre los dispositivos conectados a la solución. Esta información incluye el historial de los comandos enviados a los dispositivos desde el portal de la solución y de los métodos invocados desde el portal de la solución.

## <a name="solution-portal"></a>Portal de solución

El portal de la solución es una aplicación web que se implementa como parte de la solución preconfigurada. Las páginas principales del portal de la solución son el panel y la lista de dispositivos.

### <a name="dashboard"></a>Panel

Esta página de la aplicación web usa controles javascript de PowerBI (consulte el [repositorio de imágenes de PowerBI](https://www.github.com/Microsoft/PowerBI-visuals)) para ver los datos de telemetría de los dispositivos. La solución utiliza el trabajo de telemetría de ASA para escribir estos datos en el almacenamiento de blobs.

### <a name="device-list"></a>Lista de dispositivos

Desde esta página del portal de la solución puede hacer lo siguiente:

* Aprovisionar un dispositivo nuevo. Esta acción establece el identificador único del dispositivo y genera la clave de autenticación. Escribe la información sobre el dispositivo en el registro de identidades de IoT Hub y en la base de datos de Cosmos DB específica para la solución.
* Administrar las propiedades del dispositivo. Esta acción incluye las propiedades de visualización existentes y la actualización con nuevas propiedades.
* Enviar comandos a un dispositivo.
* Ver el historial de comandos de un dispositivo.
* Habilitar y deshabilitar dispositivos.

## <a name="next-steps"></a>Pasos siguientes

Las siguientes entradas de blog de TechNet proporcionan detalles adicionales acerca de la solución preconfigurada de supervisión remota:

* [IoT Suite - Under The Hood - Remote Monitoring: (Conjunto de aplicaciones de IoT: Supervisión remota interna)](http://social.technet.microsoft.com/wiki/contents/articles/32941.iot-suite-under-the-hood-remote-monitoring.aspx)
* [IoT Suite - Remote Monitoring - Adding Live and Simulated Devices (Conjunto de aplicaciones de IoT, Supervisión remota: Incorporación de dispositivos activos y simulados)](http://social.technet.microsoft.com/wiki/contents/articles/32975.iot-suite-remote-monitoring-adding-live-and-simulated-devices.aspx)

Puede continuar su introducción al Conjunto de aplicaciones de IoT con la lectura de los siguientes artículos:

* [Conectar el dispositivo a la solución preconfigurada de supervisión remota][lnk-connect-rm]
* [Permisos en el sitio azureiotsuite.com][lnk-permissions]

[lnk-preconfigured-solutions]: iot-suite-what-are-preconfigured-solutions.md
[lnk-customize]: iot-suite-guidance-on-customizing-preconfigured-solutions.md
[lnk-iothub]: https://azure.microsoft.com/documentation/services/iot-hub/
[lnk-asa]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-webjobs]: https://azure.microsoft.com/documentation/articles/websites-webjobs-resources/
[lnk-connect-rm]: iot-suite-connecting-devices.md
[lnk-permissions]: iot-suite-permissions.md
[lnk-c2d-guidance]: ../iot-hub/iot-hub-devguide-c2d-guidance.md
[lnk-device-twins]:  ../iot-hub/iot-hub-devguide-device-twins.md
[lnk-direct-methods]: ../iot-hub/iot-hub-devguide-direct-methods.md
