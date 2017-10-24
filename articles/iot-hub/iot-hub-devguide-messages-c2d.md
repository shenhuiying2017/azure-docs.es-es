---
title: "Información sobre la mensajería de nube a dispositivo de Azure IoT Hub | Microsoft Docs"
description: "Guía del desarrollador: cómo utilizar la mensajería de nube a dispositivo con IoT Hub. Incluye información sobre el ciclo de vida de los mensajes y las opciones de configuración."
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/06/2017
ms.author: dobett
ms.openlocfilehash: a3ebda292d16b2a420fb6d586f18201e34efffa7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="send-cloud-to-device-messages-from-iot-hub"></a>Envío de mensajes de nube a dispositivo desde IoT Hub

Para enviar notificaciones unidireccionales a la aplicación para dispositivo desde el back-end de la solución, envíe mensajes de nube a dispositivo desde su centro de IoT al dispositivo. Para obtener una explicación de otras opciones de nube a dispositivos compatibles con IoT Hub, consulte [Guía de comunicación de nube a dispositivo][lnk-c2d-guidance].

Se envían mensajes de la nube al dispositivo mediante un punto de conexión orientado al servicio (**/messages/devicebound**). Luego, un dispositivo recibe los mensajes mediante un punto de conexión específico del dispositivo (**/devices/{IdDeDispositivo}/messages/devicebound**).

Para dirigir cada mensaje de la nube a un único dispositivo, IoT Hub establece la propiedad **to** en **/devices/{IdDeDispositivo}/messages/devicebound**.

La cola de cada dispositivo puede contener como máximo 50 mensajes de la nube al dispositivo. Si se intenta enviar más mensajes al mismo dispositivo, se producirá un error.

## <a name="the-cloud-to-device-message-lifecycle"></a>El ciclo de vida de los mensajes de nube a dispositivo

Para garantizar la entrega de mensajes al menos una vez, IoT Hub conserva los mensajes de nube a dispositivo en colas para cada dispositivo. Los dispositivos tienen que confirmar explícitamente la *finalización* para que el Centro de IoT los quite de la cola. Esto garantiza la resistencia frente a errores de dispositivo y de conectividad.

En el diagrama siguiente se detalla el gráfico de estado del ciclo de vida de un mensaje de nube a dispositivo en IoT Hub.

![Ciclo de vida de los mensajes de nube a dispositivo][img-lifecycle]

Cuando el servicio IoT Hub envía un mensaje a un dispositivo, el servicio establece el estado del mensaje en **En cola**. Cuando un dispositivo quiere *recibir* un mensaje, IoT Hub *bloquea* dicho mensaje (estableciendo el estado en **Invisible**), lo que permite que otros subprocesos del dispositivo empiecen a recibir otros mensajes. Cuando el subproceso de un dispositivo termina de procesar un mensaje, informa al Centro de IoT *finalizando* dicho mensaje. Luego, IoT Hub establece el estado en **Completado**.

Un dispositivo también puede hacer lo siguiente:

* *Rechazar* el mensaje, lo que hace que el Centro de IoT lo establezca en estado **Deadlettered** (Procesado como devuelto). Los dispositivos que se conectan mediante el protocolo MQTT no pueden rechazar mensajes de nube a dispositivo.
* *Abandonar* el mensaje, lo que hace que el Centro de IoT vuelva a ponerlo en la cola con el estado **Enqueued**(En cola). Los dispositivos que se conectan mediante el protocolo MQTT no pueden abandonar mensajes de nube a dispositivo.

Podría producirse un error en el subproceso al procesar un mensaje sin notificar a Centro de IoT. En este caso, los mensajes pasan automáticamente del estado **Invisible** al estado **Enqueued** (En cola) después de un *tiempo de espera de visibilidad (o bloqueo)*. El valor predeterminado de este tiempo de espera es un minuto.

La propiedad **max delivery count** en IoT Hub determina el número máximo de veces que un mensaje puede cambiar entre los estados **Enqueued** (en cola) e **Invisible**. Después de ese número de transiciones, el Centro de IoT establece el estado del mensaje en **Deadlettered**(Procesado como devuelto). De igual forma, IoT Hub establece el estado de un mensaje en **Deadlettered** (Procesado como devuelto) después de su fecha de caducidad [consulte [Expiración de mensajes (periodo de vida)][lnk-ttl]].

El tutorial [Envío de mensajes desde la nube al dispositivo con IoT Hub][lnk-c2d-tutorial] muestra cómo enviar mensajes de nube a dispositivo y cómo recibir estos mensajes en un dispositivo.

Normalmente, un dispositivo completa un mensaje de nube a dispositivo cuando la pérdida del mensaje no afecta a la lógica de aplicación; por ejemplo, cuando el dispositivo conserva el contenido del mensaje localmente o ha ejecutado correctamente una operación. El mensaje también puede llevar información transitoria, cuya pérdida no afectaría a la funcionalidad de la aplicación. A veces, para tareas de ejecución prolongada, puede:

* Completar el mensaje de la nube a dispositivo después de guardar la descripción de la tarea en el almacenamiento local.
* Notificar al back-end de la solución con uno o más mensajes de dispositivo a la nube en distintas fases de progreso de la tarea.

## <a name="message-expiration-time-to-live"></a>Expiración de mensajes (período de vida)

Cada mensaje de nube a dispositivo tiene una fecha de expiración. Este tiempo se configura mediante una de estas opciones:

* La propiedad **ExpiryTimeUtc** en el servicio.
* IoT Hub, con el valor predeterminado de *time to live* especificado como una propiedad de IoT Hub.

Consulte [Opciones de configuración de la nube al dispositivo][lnk-c2d-configuration].

Una forma habitual de aprovechar la expiración de los mensajes y evitar enviarlos a dispositivos desconectados consiste en establecer valores cortos de período de vida. Así se consigue el mismo resultado que al mantener el estado de la conexión de los dispositivos, con la diferencia de que la primera opción resulta más eficiente. Cuando solicita confirmación de mensajes, IoT Hub notifica qué dispositivos cumplen las siguientes condiciones:

* Son capaces de recibir mensajes.
* No están en línea o se ha producido un error.

## <a name="message-feedback"></a>Comentarios de mensajes

Cuando envía un mensaje de nube a dispositivo, el servicio puede solicitar la entrega de los comentarios de cada mensaje en relación con el estado final de ese mensaje.

| Propiedad Ack | Comportamiento |
| ------------ | -------- |
| **positive** | IoT Hub genera un mensaje de comentarios si el mensaje de la nube a dispositivo alcanza el estado **Completed** (completado). |
| **negative** | IoT Hub genera un mensaje de comentarios si el mensaje de la nube a dispositivo alcanza el estado **Deadlettered** (procesado como devuelto). |
| **full**     | IoT Hub genera un mensaje de comentarios en cualquiera de los casos. |

Si **Ack** es **full** y no recibe un mensaje de comentarios, significa que este ha expirado. El servicio no puede saber qué ha ocurrido con el mensaje original. En la práctica, un servicio debe asegurarse de que puede procesar el comentario antes de que expire. El tiempo de expiración máximo es de dos días, lo que permite tener tiempo suficiente para poner de nuevo en funcionamiento el servicio en caso de error.

Como se explica en [Puntos de conexión][lnk-endpoints], IoT Hub envía los comentarios a través de un punto de conexión accesible desde el servicio (**/messages/servicebound/feedback**) en forma de mensajes. La semántica de recepción de los comentarios es la misma que para los mensajes de la nube al dispositivo; además, tienen el mismo [ciclo de vida de los mensajes][lnk-lifecycle]. Siempre que sea posible, los comentarios de mensajes se agrupan en un único mensaje, con el formato siguiente:

| Propiedad     | Description |
| ------------ | ----------- |
| EnqueuedTime | Marca de tiempo que indica cuándo se creó el mensaje. |
| UserId       | `{iot hub name}` |
| ContentType  | `application/vnd.microsoft.iothub.feedback.json` |

El cuerpo es una matriz serializada de JSON de registros, cada uno con las siguientes propiedades:

| Propiedad           | Description |
| ------------------ | ----------- |
| EnqueuedTimeUtc    | Marca de tiempo que indica cuándo se produjo el resultado del mensaje. Por ejemplo, el dispositivo completado o el mensaje expirado. |
| OriginalMessageId  | **MessageId** del mensaje de nube a dispositivo con el que está relacionada esta información de comentarios. |
| StatusCode         | Cadena necesaria. Se utiliza en los mensajes de comentarios generados por el Centro de IoT. <br/> "Success" <br/> "Expired" <br/> 'DeliveryCountExceeded' <br/> "Rejected" <br/> 'Purged' |
| Descripción        | Valores de cadena para **StatusCode**. |
| deviceId           | **DeviceId** del dispositivo de destino del mensaje de nube a dispositivo con el que está relacionado este elemento de comentarios. |
| DeviceGenerationId | **DeviceGenerationId** del dispositivo de destino del mensaje de nube a dispositivo con el que está relacionado este elemento de comentarios. |

El servicio tiene que especificar un valor de **MessageId** para el mensaje de nube a dispositivo para poder correlacionar sus comentarios con el mensaje original.

En el ejemplo siguiente se muestra el cuerpo de un mensaje de comentarios.

```json
[
  {
    "OriginalMessageId": "0987654321",
    "EnqueuedTimeUtc": "2015-07-28T16:24:48.789Z",
    "StatusCode": 0,
    "Description": "Success",
    "DeviceId": "123",
    "DeviceGenerationId": "abcdefghijklmnopqrstuvwxyz"
  },
  {
    ...
  },
  ...
]
```

## <a name="cloud-to-device-configuration-options"></a>Opciones de configuración de la nube al dispositivo

Cada centro de IoT expone las siguientes opciones de configuración para la mensajería de nube a dispositivo:

| Propiedad                  | Description | Intervalo y valor predeterminado |
| ------------------------- | ----------- | ----------------- |
| defaultTtlAsIso8601       | TTL predeterminado para los mensajes de nube a dispositivo. | Intervalo de ISO_8601 hasta 2D (1 minuto como mínimo). Valor predeterminado: 1 hora. |
| maxDeliveryCount          | Número máximo de entregas para las colas de nube a dispositivo por dispositivo. | De 1 a 100. Valor predeterminado: 10 |
| feedback.ttlAsIso8601     | Retención de mensajes de comentarios del límite de servicio. | Intervalo de ISO_8601 hasta 2D (1 minuto como mínimo). Valor predeterminado: 1 hora. |
| feedback.maxDeliveryCount |Número máximo de entregas para la cola de comentarios. | De 1 a 100. Valor predeterminado: 100. |

Para obtener más información sobre cómo establecer estas opciones de configuración, consulte [Crear instancias de IoT Hub][lnk-portal].

## <a name="next-steps"></a>Pasos siguientes

Para obtener información sobre los SDK que puede utilizar para recibir mensajes de nube a dispositivo, consulte [SDK de Azure IoT][lnk-sdks].

Para probar la recepción de mensajes de nube a dispositivo, consulte el tutorial [Envío de mensajes desde la nube al dispositivo][lnk-c2d-tutorial].

[img-lifecycle]: ./media/iot-hub-devguide-messages-c2d/lifecycle.png

[lnk-portal]: iot-hub-create-through-portal.md
[lnk-c2d-guidance]: iot-hub-devguide-c2d-guidance.md
[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-ttl]: #message-expiration-time-to-live
[lnk-c2d-configuration]: #cloud-to-device-configuration-options
[lnk-lifecycle]: #message-lifecycle
[lnk-c2d-tutorial]: iot-hub-csharp-csharp-c2d.md
