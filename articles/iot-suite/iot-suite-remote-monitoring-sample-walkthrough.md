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
ms.date: 08/17/2016
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 6338750446b33269614c404ecaad8f8192bf1ab2


---
# <a name="remote-monitoring-preconfigured-solution-walkthrough"></a>Tutorial de la solución preconfigurada de supervisión remota
## <a name="introduction"></a>Introducción
La [solución preconfigurada][lnk-preconfigured-solutions] de supervisión remota del Conjunto de aplicaciones de IoT es una implementación de una solución de supervisión de extremo a extremo para varias máquinas en ubicaciones remotas. La solución combina servicios clave de Azure para proporcionar una implementación genérica del escenario de negocio y usarla como punto de partida para su propia implementación. También puede [personalizar][lnk-customize] la solución para satisfacer sus requisitos empresariales específicos.

Este artículo le guiará a través de algunos de los elementos clave de la solución de supervisión remota para que pueda entender cómo funciona. Esta información le ayuda a:

* Solucionar problemas de la solución.
* Planear cómo personalizar la solución para satisfacer sus propios requisitos específicos. 
* Diseñar una solución de IoT propia que utilice servicios de Azure.

## <a name="logical-architecture"></a>Arquitectura lógica
El diagrama siguiente describe los componentes lógicos de la solución preconfigurada:

![Arquitectura lógica](media/iot-suite-remote-monitoring-sample-walkthrough/remote-monitoring-architecture.png)

## <a name="simulated-devices"></a>Dispositivos simulados
En la solución preconfigurada, el dispositivo simulado representa un dispositivo de refrigeración (por ejemplo, el aire acondicionado del edificio o una unidad de tratamiento de aire en instalaciones). Al implementar la solución preconfigurada, aprovisiona automáticamente cuatro dispositivos simulados que se ejecutan en un [Azure WebJob][lnk-webjobs]. Los dispositivos simulados facilitan la experimentación del comportamiento de la solución sin necesidad de implementar ningún dispositivo físico. Para implementar un dispositivo físico real, consulte el tutorial [Conectar el dispositivo a la solución preconfigurada de supervisión remota][lnk-connect-rm].

Cada dispositivo simulado puede enviar los siguientes tipos de mensajes al Centro de IoT:

| Message | Description |
| --- | --- |
| Inicio |Cuando el dispositivo se inicia, envía un mensaje de **información del dispositivo** que contiene información acerca de sí mismo al back-end. Estos datos incluyen el identificador de dispositivo, los metadatos del dispositivo, una lista de los comandos admite el dispositivo y la configuración actual del dispositivo. |
| Presencia |Un dispositivo envía periódicamente un mensaje de **presencia** para notificar si el dispositivo puede detectar o no la presencia de un sensor. |
| Telemetría |Un dispositivo envía periódicamente un mensaje de **telemetría** que informa de los valores simulados de temperatura y humedad recopilados a partir de los sensores simulados del dispositivo. |

Los dispositivos simulados envían las siguientes propiedades de dispositivo en un mensaje de **información de dispositivo** :

| Propiedad | Propósito |
| --- | --- |
| Id. de dispositivo |Identificador que se proporciona o se asigna cuando se crea un dispositivo en la solución. |
| Fabricante |Fabricante del dispositivo |
| Número de modelo |Número de modelo del dispositivo |
| Número de serie |Número de serie del dispositivo |
| Firmware |Versión actual del firmware del dispositivo |
| Plataforma |Arquitectura de la plataforma del dispositivo |
| Procesador |Procesador que ejecuta el dispositivo |
| RAM instalada |Cantidad de RAM instalada en el dispositivo |
| Estado del centro habilitado |Propiedad del estado de Centro de IoT del dispositivo |
| Hora de creación |La hora en la que se creó el dispositivo en la solución |
| Hora actualizada |Última vez que se actualizaron las propiedades en el dispositivo |
| Latitud |Ubicación de la latitud del dispositivo |
| Longitud |Ubicación de la longitud del dispositivo |

El simulador propaga estas propiedades en los dispositivos simulados con valores de ejemplo.  Cada vez que el simulador inicializa un dispositivo simulado, el dispositivo envía los metadatos previamente definidos al Centro de IoT. Tenga en cuenta que esto sobrescribe cualquier actualización de metadatos realizada en el portal de dispositivo.

Los dispositivos simulados también pueden manejar los siguientes comandos enviados desde el panel de soluciones a través del Centro de IoT:

| Comando | Description |
| --- | --- |
| PingDevice |Envía un *ping* al dispositivo para comprobar si está activo |
| StartTelemetry |Inicia el dispositivo que envía la telemetría. |
| StopTelemetry |Detiene el dispositivo que envía la telemetría. |
| ChangeSetPointTemp |Cambia el valor del punto fijo alrededor del cual se generan los datos aleatorios. |
| DiagnosticTelemetry |Desencadena el simulador de dispositivos para enviar un valor de telemetría adicional (externalTemp). |
| ChangeDeviceState |Cambia una propiedad de estado extendido del dispositivo y envía el mensaje de información de dispositivo desde el dispositivo. |

La confirmación de comandos del dispositivo al back-end de la solución se proporciona a través del Centro de IoT.

## <a name="iot-hub"></a>Centro de IoT
El [IoT Hub][lnk-iothub] ingiere los datos enviados desde los dispositivos en la nube y los pone a disposición de los trabajos de Stream Analytics de Azure (ASA). El Centro de IoT también envía comandos a sus dispositivos en nombre del portal de dispositivos. Cada trabajo de transmisión de ASA utiliza un grupo de consumidores independiente del Centro de IoT para leer la transmisión de mensajes de los dispositivos.

## <a name="azure-stream-analytics"></a>Análisis de transmisiones de Azure
En la solución de supervisión remota, [Stream Analytics de Azure][lnk-asa] (ASA) envía los mensajes de dispositivos recibidos por el IoT Hub a otros componentes back-end para su procesamiento o almacenamiento. Diferentes trabajos de ASA realizan funciones específicas en función del contenido de los mensajes.

**Trabajo 1: información de dispositivo** filtra los mensajes de información del dispositivo desde la transmisión de mensajes entrantes y los envía a un punto de conexión de Centro de eventos. Un dispositivo envía mensajes de información de dispositivo al inicio y como respuesta a un comando **SendDeviceInfo** . Este trabajo utiliza la siguiente definición de consulta para identificar mensajes de **información de dispositivo** :

```
SELECT * FROM DeviceDataStream Partition By PartitionId WHERE  ObjectType = 'DeviceInfo'
```

Este trabajo envía su resultado a un centro de eventos para su posterior procesamiento.

**Trabajo 2: reglas** evalúa los valores de telemetría de temperatura y humedad entrantes según los umbrales por dispositivo. Los valores de umbral se establecen en el editor de reglas disponible en el panel de soluciones. Cada par de valor/dispositivo se almacena en la marca de tiempo de un blob que se lee en Análisis de transmisiones como **datos de referencia**. El trabajo compara cualquier valor no vacío con el umbral establecido para el dispositivo. Si supera la condición '>', el trabajo tendrá como resultado un evento de **alarma** que indica que se superó el umbral y proporciona los valores de dispositivo, valor y marca de tiempo. Este trabajo utiliza la definición de la consulta siguiente para identificar los mensajes de telemetría que desencadenan una alarma:

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

El trabajo envía su resultado a un centro de eventos para su posterior procesamiento y guarda los detalles de cada alerta en el almacenamiento de blobs, en donde el panel de soluciones puede leer la información de la alerta.

**Trabajo 3: Telemetría** opera en el flujo de la telemetría del dispositivo entrante de dos formas distintas. La primera envía todos los mensajes de telemetría desde los dispositivos al almacenamiento de blobs persistente para su almacenamiento a largo plazo. La segunda calcula los valores de humedad medios, mínimos y máximos sobre una ventana deslizante de cinco minutos y envía estos datos al almacenamiento de blobs. El panel de soluciones lee los datos de telemetría desde el almacenamiento de blobs para rellenar los gráficos. Este trabajo utiliza la siguiente definición de consulta:

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
La solución utiliza el almacenamiento de blobs de Azure para conservar todos los datos de telemetría resumidos y sin procesar de los dispositivos de la solución. El panel lee los datos de telemetría desde el almacenamiento de blobs para rellenar los gráficos. Para mostrar las alertas, el panel lee los datos del almacenamiento de blobs que registra cuándo los valores de telemetría superan los valores de umbral configurados. La solución también utiliza el almacenamiento de blobs para registrar los valores de umbral establecidos en el panel.

## <a name="webjobs"></a>Trabajos web
Además de hospedar los simuladores de dispositivos, los WebJobs de la solución también hospedan el **procesador de eventos** que se ejecuta en un WebJob de Azure que controla los mensajes de información de dispositivo y las respuestas de comandos. Usa:

* Los mensajes de información de dispositivo para actualizar el registro del dispositivo (almacenado en la base de datos de DocumentDB) con la información actual del dispositivo.
* Mensajes de respuesta de comando para actualizar el historial de comandos de dispositivo (almacenado en la base de datos de DocumentDB).

## <a name="documentdb"></a>DocumentDB
La solución utiliza una base de datos de DocumentDB para almacenar información sobre los dispositivos conectados a la solución. Esta información incluye los metadatos de dispositivo y el historial de comandos que se envió a los dispositivos desde el panel.

## <a name="web-apps"></a>Aplicaciones web
### <a name="remote-monitoring-dashboard"></a>Panel de supervisión remota
Esta página de la aplicación web usa controles javascript de PowerBI (consulte [Repositorio de imágenes de PowerBI](https://www.github.com/Microsoft/PowerBI-visuals)) para visualizar los datos de telemetría de los dispositivos. La solución utiliza el trabajo de telemetría de ASA para escribir estos datos en el almacenamiento de blobs.

### <a name="device-administration-portal"></a>Portal de administración de dispositivos
Esta aplicación web le permite:

* Aprovisionar un dispositivo nuevo. Esta acción establece el identificador único del dispositivo y genera la clave de autenticación. Escribe la información sobre el dispositivo en el registro de identidades del Centro de IoT y en la base de datos de DocumentDB específica para la solución.
* Administrar las propiedades del dispositivo. Esta acción incluye las propiedades de visualización existentes y la actualización con nuevas propiedades.
* Enviar comandos a un dispositivo.
* Ver el historial de comandos de un dispositivo.
* Habilitar y deshabilitar dispositivos.

## <a name="next-steps"></a>Pasos siguientes
Las siguientes entradas de blog de TechNet proporcionan detalles adicionales acerca de la solución preconfigurada de supervisión remota:

* [IoT Suite - Under The Hood - Remote Monitoring: (Conjunto de aplicaciones de IoT: Supervisión remota interna)](http://social.technet.microsoft.com/wiki/contents/articles/32941.iot-suite-under-the-hood-remote-monitoring.aspx)
* [IoT Suite - Remote Monitoring - Adding Live and Simulated Devices (Conjunto de aplicaciones de IoT, Supervisión remota: Incorporación de dispositivos activos y simulados)](http://social.technet.microsoft.com/wiki/contents/articles/32975.iot-suite-remote-monitoring-adding-live-and-simulated-devices.aspx)

Puede continuar su introducción al Conjunto de aplicaciones de IoT con la lectura de los siguientes artículos:

* [Conectar el dispositivo a la solución preconfigurada de supervisión remota (Windows)][lnk-connect-rm]
* [Permisos en el sitio azureiotsuite.com][lnk-permissions]

[lnk-preconfigured-solutions]: iot-suite-what-are-preconfigured-solutions.md
[lnk-customize]: iot-suite-guidance-on-customizing-preconfigured-solutions.md
[lnk-iothub]: https://azure.microsoft.com/documentation/services/iot-hub/
[lnk-asa]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-webjobs]: https://azure.microsoft.com/documentation/articles/websites-webjobs-resources/
[lnk-connect-rm]: iot-suite-connecting-devices.md
[lnk-permissions]: iot-suite-permissions.md



<!--HONumber=Nov16_HO2-->


