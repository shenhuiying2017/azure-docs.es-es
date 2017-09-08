---
title: Conceptos de trabajos de IoT Hub de Azure | Microsoft Docs
description: "Guía de desarrollador: programación de trabajos para su ejecución en varios dispositivos conectados al centro de IoT Hub. Trabajos pueden actualizar las etiquetas y propiedades que desee y llamar a métodos directas en varios dispositivos."
services: iot-hub
documentationcenter: .net
author: juanjperez
manager: timlt
editor: 
ms.assetid: fe78458f-4f14-4358-ac83-4f7bd14ee8da
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/29/2017
ms.author: juanpere
ms.translationtype: HT
ms.sourcegitcommit: 8351217a29af20a10c64feba8ccd015702ff1b4e
ms.openlocfilehash: 6e4ca8ad0c444930f5e45eed0a024412de82dbb1
ms.contentlocale: es-es
ms.lasthandoff: 08/29/2017

---
# <a name="schedule-jobs-on-multiple-devices"></a>Programación de trabajos en varios dispositivos
## <a name="overview"></a>Información general
Como se describe en artículos anteriores, Azure IoT Hub permite un número de bloques de creación ([etiquetas y propiedades de dispositivos gemelos][lnk-twin-devguide] y [métodos directos][lnk-dev-methods]).  Normalmente, las aplicaciones de back-end permiten a los administradores y operadores de dispositivos actualizar e interactuar con dispositivos de IoT de forma masiva y a la hora programada.  Los trabajos encapsulan la ejecución de actualizaciones del dispositivo gemelo y métodos directos con un conjunto de dispositivos a la hora programada.  Por ejemplo, un operador usaría una aplicación de back-end que se iniciaría y realizaría un seguimiento de un trabajo para reiniciar un conjunto de dispositivos en la creación del piso 43 y 3 en un momento en que no sería problemático para las operaciones del edificio.

### <a name="when-to-use"></a>Cuándo se deben usar
Considere la posibilidad de usar trabajos cuando una solución back end necesite programar y realizar un seguimiento del progreso de cualquiera de las siguientes actividades en un conjunto de dispositivos:

* Actualizar las propiedades deseadas
* Actualizar etiquetas
* Invocar métodos directos

## <a name="job-lifecycle"></a>Ciclo de vida de trabajo
Los trabajos se inician mediante el back-end de solución y se mantienen mediante IoT Hub.  Puede iniciar un trabajo a través de un URI orientado a servicios (`{iot hub}/jobs/v2/{device id}/methods/<jobID>?api-version=2016-11-14`) y una consulta para el progreso de un trabajo en ejecución a través de un URI orientado a servicios (`{iot hub}/jobs/v2/<jobId>?api-version=2016-11-14`).  Una vez que se inicia un trabajo, la consulta de trabajos permitirá a la aplicación back-end actualizar el estado de trabajos en ejecución.

> [!NOTE]
> Cuando se inicia un trabajo, los valores y los nombres de propiedad solo pueden contener caracteres alfanuméricos US-ASCII imprimibles, excepto los del siguiente conjunto: ``{'$', '(', ')', '<', '>', '@', ',', ';', ':', '\', '"', '/', '[', ']', '?', '=', '{', '}', SP, HT}``.
> 
> 

## <a name="reference-topics"></a>Temas de referencia:
Los siguientes temas de referencia proporcionan más información sobre el uso de trabajos.

## <a name="jobs-to-execute-direct-methods"></a>Trabajos para ejecutar métodos directos
A continuación se muestran los detalles de la solicitud HTTP 1.1 para ejecutar un [método directo][lnk-dev-methods] en un conjunto de dispositivos mediante un trabajo:

    ```
    PUT /jobs/v2/<jobId>?api-version=2016-11-14

    Authorization: <config.sharedAccessSignature>
    Content-Type: application/json; charset=utf-8
    Request-Id: <guid>
    User-Agent: <sdk-name>/<sdk-version>

    {
        jobId: '<jobId>',
        type: 'scheduleDirectRequest', 
        cloudToDeviceMethod: {
            methodName: '<methodName>',
            payload: <payload>,                 
            responseTimeoutInSeconds: methodTimeoutInSeconds 
        },
        queryCondition: '<queryOrDevices>', // query condition
        startTime: <jobStartTime>,          // as an ISO-8601 date string
        maxExecutionTimeInSeconds: <maxExecutionTimeInSeconds>        
    }
    ```
La condición de consulta puede estar también en un solo identificador de dispositivo o en una lista de identificadores de dispositivos, como se muestra a continuación.

**Ejemplos**
```
queryCondition = "deviceId = 'MyDevice1'"
queryCondition = "deviceId IN ['MyDevice1','MyDevice2']"
queryCondition = "deviceId IN ['MyDevice1']
```
[Lenguaje de consulta de IoT Hub][lnk-query] aborda el lenguaje de consulta de IoT Hub con más detalle.

## <a name="jobs-to-update-device-twin-properties"></a>Trabajos para actualizar las propiedades del dispositivo gemelo
A continuación se muestran los detalles de la solicitud HTTP 1.1 para la actualización de las propiedades del dispositivo gemelo con un trabajo:

    ```
    PUT /jobs/v2/<jobId>?api-version=2016-11-14
    Authorization: <config.sharedAccessSignature>
    Content-Type: application/json; charset=utf-8
    Request-Id: <guid>
    User-Agent: <sdk-name>/<sdk-version>

    {
        jobId: '<jobId>',
        type: 'scheduleTwinUpdate', 
        updateTwin: <patch>                 // Valid JSON object
        queryCondition: '<queryOrDevices>', // query condition
        startTime: <jobStartTime>,          // as an ISO-8601 date string
        maxExecutionTimeInSeconds: <maxExecutionTimeInSeconds>        // format TBD
    }
    ```

## <a name="querying-for-progress-on-jobs"></a>Consulta del progreso de los trabajos
A continuación se muestran los detalles de la solicitud HTTP 1.1 para la [consulta de trabajos][lnk-query]:

    ```
    GET /jobs/v2/query?api-version=2016-11-14[&jobType=<jobType>][&jobStatus=<jobStatus>][&pageSize=<pageSize>][&continuationToken=<continuationToken>]

    Authorization: <config.sharedAccessSignature>
    Content-Type: application/json; charset=utf-8
    Request-Id: <guid>
    User-Agent: <sdk-name>/<sdk-version>
    ```

Se proporciona el continuationToken de la respuesta.  

## <a name="jobs-properties"></a>Propiedades de trabajos
La siguiente es una lista de propiedades y las descripciones correspondientes, que se pueden utilizar al consultar trabajos o resultados de trabajos.

| Propiedad | Descripción |
| --- | --- |
| **jobId** |Id. proporcionado de la aplicación para el trabajo. |
| **startTime** |Hora de inicio proporcionada de la aplicación (ISO 8601) para el trabajo. |
| **endTime** |Fecha proporcionada de IoT Hub (ISO 8601) cuando hay un trabajo completado. Válido solo después de que el trabajo alcance el estado 'completado'. |
| **type** |Tipos de trabajos: |
| **scheduledUpdateTwin**: un trabajo que se usa para actualizar un conjunto de propiedades deseadas o etiquetas. | |
| **scheduledDeviceMethod**: un trabajo que se utiliza para invocar un método de dispositivo en un conjunto de dispositivos gemelos. | |
| **estado** |Estado actual del trabajo. Posibles valores para el estado: |
| **pending**: programado y en espera para que lo recopile el servicio de trabajo. | |
| **scheduled**: programado para una hora futura. | |
| **running**: trabajo activo actualmente. | |
| **cancelled**: el trabajo se ha cancelado. | |
| **failed**: trabajo erróneo. | |
| **completed**: el trabajo se ha completado. | |
| **deviceJobStatistics** |Estadísticas sobre la ejecución del trabajo. |

Propiedades **deviceJobStatistics**.

| Propiedad | Descripción |
| --- | --- |
| **deviceJobStatistics.deviceCount** |Número de dispositivos en el trabajo. |
| **deviceJobStatistics.failedCount** |Número de dispositivos en los que se ha producido un error del trabajo. |
| **deviceJobStatistics.succeededCount** |Número de dispositivos en los que se ha realizado correctamente el trabajo. |
| **deviceJobStatistics.runningCount** |Número de dispositivos que ejecutan actualmente el trabajo. |
| **deviceJobStatistics.pendingCount** |Número de dispositivos pendientes de ejecutar el trabajo. |

### <a name="additional-reference-material"></a>Material de referencia adicional
Otros temas de referencia en la guía del desarrollador de IoT Hub son los siguientes:

* En [Puntos de conexión de IoT Hub][lnk-endpoints], se describen los diferentes puntos de conexión que expone cada centro de IoT Hub para las operaciones en tiempo de ejecución y de administración.
* En [Cuotas y limitación][lnk-quotas], se describen las cuotas que se aplican al servicio IoT Hub y el comportamiento de limitación que se espera al usar el servicio.
* En [SDK de dispositivo y servicio IoT de Azure][lnk-sdks] se muestran los diversos SDK de lenguaje que puede usar para desarrollar aplicaciones para dispositivo y de servicio que interactúen con IoT Hub.
* En [Referencia: Lenguaje de consulta de IoT Hub para dispositivos gemelos, trabajos y enrutamiento de mensajes][lnk-query], se describe el lenguaje de consulta de IoT Hub que se puede usar para recuperar información de IoT Hub sobre los dispositivos gemelos y trabajos.
* En [Compatibilidad con MQTT de IoT Hub][lnk-devguide-mqtt], se proporciona más información sobre la compatibilidad de IoT Hub con el protocolo MQTT.

## <a name="next-steps"></a>Pasos siguientes
Si desea probar algunos de los conceptos descritos en este artículo, puede interesarle el siguiente tutorial de IoT Hub:

* [Schedule and broadcast jobs][lnk-jobs-tutorial] (Programación y difusión de trabajos)

<!-- links and images -->

[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-jobs-tutorial]: iot-hub-node-node-schedule-jobs.md
[lnk-c2d-methods]: iot-hub-node-node-direct-methods.md
[lnk-dev-methods]: iot-hub-devguide-direct-methods.md
[lnk-get-started-twin]: iot-hub-node-node-twin-getstarted.md
[lnk-twin-devguide]: iot-hub-devguide-device-twins.md

