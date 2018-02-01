---
title: "Información de los métodos directos de IoT Hub de Azure | Microsoft Docs"
description: "Guía del desarrollador: uso de métodos directos para invocar código en los dispositivos de una aplicación de servicio."
services: iot-hub
documentationcenter: .net
author: nberdy
manager: timlt
editor: 
ms.assetid: 9f0535f1-02e6-467a-9fc4-c0950702102d
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/19/2017
ms.author: nberdy
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 243845139c7ae0389333d7490098ef73f95dceac
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/17/2018
---
# <a name="understand-and-invoke-direct-methods-from-iot-hub"></a>Conocimiento e invocación de los métodos directos de IoT Hub
IoT Hub ofrece la posibilidad de invocar métodos directos en dispositivos desde la nube. Los métodos directos representan una interacción solicitud-respuesta con un dispositivo similar a una llamada HTTP en la cual se completan correctamente o generan un error de inmediato (tras un tiempo de espera que especifica el usuario). Este enfoque es útil para escenarios en los que la línea de acción inmediata difiere en función de si el dispositivo respondió, por ejemplo, enviando una reactivación por SMS a un dispositivo si este está sin conexión (enviar un SMS cuesta más que una llamada de método).
Cada método de dispositivo se dirige a un único dispositivo. Los [trabajos][lnk-devguide-jobs] proporcionan una manera de invocar métodos directos en varios dispositivos y de programar la invocación de métodos para los dispositivos desconectados.

Cualquier persona con permisos de **conexión de servicio** en IoT Hub pueden invocar un método en un dispositivo.

Los métodos directos siguen un patrón de solicitud-respuesta y se usan para las comunicaciones que necesitan confirmación inmediata de su resultado, normalmente control interactivo del dispositivo, por ejemplo, encender un ventilador.

Si duda entre el uso de las propiedades deseadas, los métodos directos o los mensajes de la nube al dispositivo, consulte [Cloud-to-device communication guidance][lnk-c2d-guidance] (Guía de comunicaciones de la nube al dispositivo).

## <a name="method-lifecycle"></a>Ciclo de vida de los métodos
Los métodos directos se implementan en el dispositivo y pueden requerir de ninguna entrada a varias en la carga de método para crear instancias correctamente. Se invoca un método directo mediante un URI orientado al servicio (`{iot hub}/twins/{device id}/methods/`). Un dispositivo recibe métodos directos a través de un tema MQTT específico del dispositivo (`$iothub/methods/POST/{method name}/`) o a través de vínculos AMQP (propiedades de la aplicación `IoThub-methodname` y `IoThub-status`). 

> [!NOTE]
> Cuando se invoca un método directo en un dispositivo, los valores y los nombres de propiedad solo pueden contener caracteres alfanuméricos US-ASCII imprimibles, excepto los del siguiente conjunto: ``{'$', '(', ')', '<', '>', '@', ',', ';', ':', '\', '"', '/', '[', ']', '?', '=', '{', '}', SP, HT}``.
> 
> 

Los métodos directos son sincrónicos y se completan correctamente o producen un error tras el período de tiempo de espera (valor predeterminado: 30 segundos; valor máximo: 3600 segundos). Los métodos directos son útiles en escenarios interactivos en los que quiere que un dispositivo actúe únicamente si está conectado y recibiendo comandos, como encender una luz desde un teléfono. En estos escenarios, quiere saber de inmediato si la acción se ha completado o no para que el servicio en la nube pueda actuar lo antes posible en función del resultado. El dispositivo puede devolver un cuerpo de mensaje como resultado del método, pero no es necesario que el método lo haga. No hay ninguna garantía respecto al orden o la semántica de simultaneidad en las llamadas de método.

Los métodos directos son solo HTTP desde el lado de la nube y MQTT o AMQP desde el lado del dispositivo.

La carga útil de solicitudes y respuestas del método es un documento JSON de hasta 128 KB.

## <a name="invoke-a-direct-method-from-a-back-end-app"></a>Invocación de un método directo desde una aplicación de back-end
### <a name="method-invocation"></a>Invocación de método
Las invocaciones de método directo en un dispositivo son llamadas HTTP compuestas por:

* El *URI* específico del dispositivo (`{iot hub}/twins/{device id}/methods/`)
* El *método* POST
* *Encabezados* que contienen la autorización, el id. de solicitud, el tipo de contenido y la codificación del contenido
* Un *cuerpo* JSON transparente en el formato siguiente:

   ```
   {
       "methodName": "reboot",
       "responseTimeoutInSeconds": 200,
       "payload": {
           "input1": "someInput",
           "input2": "anotherInput"
       }
   }
   ```

El tiempo de espera se expresa en segundos. Si no se establece el tiempo de espera, el valor predeterminado es 30 segundos.

### <a name="response"></a>Response
La aplicación de back-end recibe una respuesta que consta de lo siguiente:

* *Código de estado HTTP*, que se usa para errores procedentes de IoT Hub, incluido el error 404 para los dispositivos que no estén conectados
* *Encabezados* que contienen la etiqueta de identidad, el id. de solicitud, el tipo de contenido y la codificación del contenido
* Un *cuerpo* JSON en el formato siguiente:

   ```   {
       "status" : 201,
       "payload" : {...}
   }
   ```

   El dispositivo proporciona tanto `status` como `body`, que se utilizan para responder con el código de estado o la descripción propios del dispositivo.

## <a name="handle-a-direct-method-on-a-device"></a>Control de un método directo en un dispositivo
### <a name="mqtt"></a>MQTT
#### <a name="method-invocation"></a>Invocación de método
Los dispositivos reciben las solicitudes de método directo en el tema MQTT: `$iothub/methods/POST/{method name}/?$rid={request id}`.

El cuerpo que recibe el dispositivo está en el formato siguiente:

```
{
    "input1": "someInput",
    "input2": "anotherInput"
}
```

Las solicitudes de método son QoS 0.

#### <a name="response"></a>Response
El dispositivo envía las respuestas a `$iothub/methods/res/{status}/?$rid={request id}`, donde:

* La propiedad `status` es el estado de la ejecución del método proporcionado por el dispositivo.
* La propiedad `$rid` es el identificador de solicitud de la invocación de método recibido de IoT Hub.

El dispositivo establece el cuerpo y puede tener cualquier estado.

### <a name="amqp"></a>AMQP
#### <a name="method-invocation"></a>Invocación de método
El dispositivo recibe solicitudes de método directo mediante la creación de un vínculo de recepción en la dirección `amqps://{hostname}:5671/devices/{deviceId}/methods/deviceBound`.

Llega el mensaje AMQP en el vínculo de recepción que representa la solicitud del método. Contiene lo siguiente:
* La propiedad del identificador de correlación, que contiene un identificador de solicitud que debe volver a pasarse con la respuesta del método correspondiente.
* Una propiedad de la aplicación denominada `IoThub-methodname`, que contiene el nombre del método que se invoca.
* El cuerpo del mensaje AMQP que contiene la carga del método como JSON.

#### <a name="response"></a>Response
El dispositivo crea un vínculo para devolver la respuesta del método en la dirección `amqps://{hostname}:5671/devices/{deviceId}/methods/deviceBound`.

La respuesta del método se devuelve en el vínculo de envío y presenta la estructura siguiente:
* La propiedad del identificador de correlación, que contiene el identificador de solicitud pasado en el mensaje de solicitud del método.
* Una propiedad de la aplicación denominada `IoThub-status`, que contiene el estado del método proporcionado por el usuario.
* El cuerpo del mensaje AMQP que contiene la respuesta del método como JSON.

## <a name="additional-reference-material"></a>Material de referencia adicional
Otros temas de referencia en la guía del desarrollador de IoT Hub son los siguientes:

* En [Puntos de conexión de IoT Hub][lnk-endpoints], se describen los diferentes puntos de conexión que expone cada centro de IoT Hub para las operaciones en tiempo de ejecución y de administración.
* En [Cuotas y limitación][lnk-quotas], se describen las cuotas que se aplican al servicio IoT Hub y el comportamiento de limitación que se espera al usar el servicio.
* En [SDK de dispositivo y servicio IoT de Azure][lnk-sdks] se muestran los diversos SDK de lenguaje que puede usar para desarrollar aplicaciones para dispositivo y de servicio que interactúen con IoT Hub.
* En [Referencia: Lenguaje de consulta de IoT Hub para dispositivos gemelos, trabajos y enrutamiento de mensajes][lnk-query], se describe el lenguaje de consulta de IoT Hub que se puede usar para recuperar información de IoT Hub sobre los dispositivos gemelos y trabajos.
* En [Compatibilidad con MQTT de IoT Hub][lnk-devguide-mqtt], se proporciona más información sobre la compatibilidad de IoT Hub con el protocolo MQTT.

## <a name="next-steps"></a>pasos siguientes
Ahora que ha aprendido a usar métodos directos, puede interesarle el siguiente artículo sobre la Guía del desarrollador de IoT Hub:

* [Programación de trabajos en varios dispositivos][lnk-devguide-jobs]

Si desea probar algunos de los conceptos descritos en este artículo, puede interesarle el siguiente tutorial de IoT Hub:

* [Use direct methods][lnk-methods-tutorial] (Uso de métodos directos)

<!-- links and images -->

[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md

[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-methods-tutorial]: iot-hub-node-node-direct-methods.md
[lnk-devguide-messages]: iot-hub-devguide-messaging.md
[lnk-c2d-guidance]: iot-hub-devguide-c2d-guidance.md
