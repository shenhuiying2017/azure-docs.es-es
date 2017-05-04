---
title: "Información sobre la mensajería de IoT Hub de Azure | Microsoft Docs"
description: "Guía del desarrollador: mensajería de dispositivo a nube y de nube a dispositivo con IoT Hub Incluye información sobre los formatos de mensaje y protocolos de comunicación compatibles."
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 3fc5f1a3-3711-4611-9897-d4db079b4250
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/04/2017
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: ac3f4d2220c1902f00049ce237468ddee992209d
ms.lasthandoff: 04/03/2017


---
# <a name="send-and-receive-messages-with-iot-hub"></a>Envío y recepción de mensajes con IoT Hub
## <a name="overview"></a>Información general
IoT Hub proporciona los siguientes primitivos de mensajería para comunicarse con un dispositivo:

* [De dispositivo a nube][lnk-d2c] desde un dispositivo hasta un back-end de aplicación.
* [de nube a dispositivo][lnk-c2d] desde un back-end de aplicación (*servicio* o *nube*).

Las propiedades básicas de la funcionalidad de mensajería del Centro de IoT son la confiabilidad y durabilidad de los mensajes. Estas propiedades permiten la resistencia a la conectividad intermitente en el dispositivo y a los picos de carga del procesamiento de eventos en la nube. El Centro de IoT implementa *al menos una vez* garantías de entrega para la mensajería del dispositivo a la nube y de la nube al dispositivo.

IoT Hub admite varios [protocolos accesibles desde los dispositivos][lnk-protocols] (por ejemplo, MQTT, AMQP y HTTP). Para admitir la interoperabilidad sin problemas entre protocolos, IoT Hub define un [formato de mensaje común][lnk-message-format] compatible con todos los protocolos accesibles desde el dispositivo.

IoT Hub expone un [punto de conexión compatible con el Centro de eventos][lnk-compatible-endpoint] integrado para permitir que las aplicaciones de back-end lean los mensajes de dispositivo a nube recibidos por el centro. También puede crear puntos de conexión personalizados en su IoT Hub vinculando otros servicios en su suscripción al centro.

### <a name="when-to-use"></a>Cuándo se deben usar
Los mensajes del dispositivo a la nube se utilizan para enviar telemetría y alertas de series temporales desde la aplicación para dispositivo y los mensajes de la nube al dispositivo, para las notificaciones unidireccionales a la aplicación para dispositivo.

Si duda entre el uso de propiedades notificadas, mensajes de dispositivo a nube o carga de archivos, consulte [Device-to-cloud communication guidance][lnk-d2c-guidance] (Guía de comunicación de dispositivo a nube).
Si duda entre el uso de las propiedades deseadas, los métodos directos o los mensajes de la nube al dispositivo, consulte [Cloud-to-device communication guidance][lnk-c2d-guidance] (Guía de comunicaciones de la nube al dispositivo).

Para ver una comparación de los servicios IoT Hub y Event Hubs, consulte [Comparación de IoT Hub y Event Hubs][lnk-compare].

## <a name="device-to-cloud-messages"></a>Mensajes de dispositivo a nube
Los mensajes del dispositivo a la nube se envían a través de un punto de conexión orientado al dispositivo (**/devices/{IdDeDispositivo}/messages/events**). Después, las reglas de enrutamiento enrutan los mensajes a uno de los puntos de conexión orientado al servicio en su IoT Hub. Las reglas de enrutamiento utilizan las propiedades de los mensajes de dispositivo a nube que fluyen a través de su centro para determinar dónde enrutarlos. De forma predeterminada, los mensajes se enrutan al punto de conexión orientado al servicio integrado (/messages/events), que es compatible con [Event Hubs][lnk-event-hubs]. Por lo tanto, puede usar la [integración de Event Hubs estándar y SDK][lnk-compatible-endpoint] para recibir mensajes del dispositivo a la nube.

IoT Hub implementa mensajería de dispositivo a nube mediante un patrón de mensajería de streaming. Los mensajes de dispositivo a nube de IoT Hub son más parecidos a *eventos* de [Event Hubs][lnk-event-hubs] que a *mensajes* de [Service Bus][lnk-servicebus] en que hay un gran volumen de eventos que se pasan a través del servicio que pueden leer varios lectores.

Esta implementación tiene las implicaciones siguientes:

* Como sucede con los eventos de Event Hubs, los mensajes de dispositivo a nube son duraderos y se conservan en el punto de conexión **messages/events** predeterminado de un IoT Hub hasta siete días.
* Como los eventos de Event Hubs, los mensajes del dispositivo a la nube pueden tener como máximo 256 kB y se pueden agrupar en lotes para optimizar los envíos. Los lotes pueden tener un tamaño máximo de 256 KB.

De todas formas, hay algunas diferencias importantes entre los mensajes de dispositivo a nube de Centro de IoT y los Centros de eventos:

* Como se explica en la sección [Control del acceso a IoT Hub][lnk-devguide-security], IoT Hub permite la autenticación y el control de acceso por dispositivo.
* IoT Hub le permite crear hasta 10 puntos de conexión personalizados. Los mensajes se entregan a los puntos de conexión según las rutas configuradas en su IoT Hub.
* IoT Hub permite millones de dispositivos conectados simultáneamente consulte [Cuotas y limitación][lnk-quotas], mientras que Event Hubs se limita a 5000 conexiones AMQP por espacio de nombres.
* El Centro de IoT no permite el particionamiento arbitrario mediante una **PartitionKey**. Los mensajes de dispositivo a nube se dividen en particiones en función de su valor de **deviceId**de origen.
* El escalado de un Centro de IoT es ligeramente diferente al de los centros de eventos. Para más información, consulte [Escalado de IoT Hub][lnk-guidance-scale].

Para más información sobre cómo usar la mensajería del dispositivo a la nube, consulte [SDK de IoT de Azure][lnk-sdks].

Para más información sobre cómo configurar el enrutamiento de mensajes, consulte [Reglas de enrutamiento](#routing-rules).

> [!NOTE]
> Cuando se usa HTTP para enviar mensajes de dispositivo a nube, los valores y los nombres de propiedad solo pueden contener caracteres alfanuméricos ASCII y ``{'!', '#', '$', '%, '&', "'", '*', '*', '+', '-', '.', '^', '_', '`', '|', '~'}``.
> 
> 

### <a name="non-telemetry-traffic"></a>Tráfico sin telemetría
A menudo, además de los puntos de datos de telemetría, los dispositivos envían mensajes y solicitudes que requieren la ejecución y el control independientes de la capa de lógica de negocio de la aplicación. Por ejemplo, las alertas críticas que deben desencadenar una acción específica en el back-end. Puede escribir fácilmente una regla de enrutamiento para enviar estos tipos de mensajes a un punto de conexión dedicado para su procesamiento.

Para más información sobre la mejor manera de procesar este tipo de mensaje, consulte [Tutorial: procesamiento de mensajes del dispositivo a la nube de IoT Hub][lnk-d2c-tutorial].

### <a name="routing-rules"></a>Reglas de enrutamiento

IoT Hub le permite enrutar mensajes a puntos de conexión de IoT Hub según las propiedades de mensaje. Las reglas de enrutamiento proporcionan la flexibilidad necesaria para enviar los mensajes donde deben ir sin la necesidad de crear servicios adicionales para procesar mensajes o escribir código adicional. Cada regla de enrutamiento que configure tiene las siguientes propiedades:

* **Nombre**. Nombre único que identifica la regla.
* **Origen**. El origen del flujo de datos sobre el que se debe actuar. Por ejemplo, telemetría de dispositivo.
* **Condición**. La expresión de consulta para la regla de enrutamiento que se ejecuta en las propiedades del mensaje y se usa para determinar si se trata de una coincidencia para el punto de conexión. Para más información sobre cómo crear una condición de ruta, consulte [Referencia: Lenguaje de consulta de IoT Hub para dispositivos gemelos y trabajos][lnk-devguide-query-language].
* **Punto de conexión**. El nombre del punto de conexión donde IoT Hub envía mensajes que cumplen la condición. Los puntos de conexión deben estar en la misma región que IoT Hub, ya que, de lo caso contrario, se puede cobrar por escrituras entre regiones.

Un solo mensaje puede cumplir la condición en varias reglas de enrutamiento, en cuyo caso IoT Hub entrega el mensaje al punto de conexión asociado a cada regla coincidente. IoT Hub también desduplica automáticamente la entrega de mensajes, por lo que si un mensaje cumple varias reglas que tienen el mismo destino, solo se escribe en ese destino una vez.

Para más información sobre cómo crear puntos de conexión personalizados en IoT Hub, consulte [Referencia: Puntos de conexión de IoT Hub][lnk-devguide-endpoints].

### <a name="built-in-endpoint-messagesevents"></a>Punto de conexión integrado: messages/events

IoT Hub muestra las propiedades siguientes para permitirle controlar el punto de conexión de mensajería integrado compatible con Event Hubs **messages/events**.

* **Número de particiones**. Establezca esta propiedad durante la creación para definir el número de [particiones][lnk-event-hub-partitions] para ingesta de eventos de dispositivo a nube.
* **Tiempo de retención**. Esta propiedad especifica cuánto tiempo, en días, IoT Hub conserva los mensajes. El valor predeterminado es un día, pero se puede aumentar a siete días.

IoT Hub también le permite administrar los grupos de consumidores en el punto de conexión de recepción de dispositivo a nube integrado.

De forma predeterminada, todos los mensajes que no cumplen de forma explícita una regla de enrutamiento de mensajes se escriben en el punto de conexión integrado. Si deshabilita esta ruta de reserva, los mensajes que no cumplen explícitamente ninguna reglas de enrutamiento de mensajes se quitan.

Puede modificar el tiempo de retención mediante programación con las[API de REST del proveedor de recursos de IoT Hub][lnk-resource-provider-apis] o usando [Azure Portal][lnk-management-portal].

### <a name="anti-spoofing-properties"></a>Propiedades contra la suplantación
Para evitar la suplantación de dispositivos en los mensajes de dispositivo a nube, el Centro de IoT marca todos los mensajes con las siguientes propiedades:

* **ConnectionDeviceId**
* **ConnectionDeviceGenerationId**
* **ConnectionAuthMethod**

Las dos primeras contienen los valores **deviceId** y **generationId** del dispositivo de origen, tal como se indicó en [Propiedades de identidad del dispositivo][lnk-device-properties].

La propiedad **ConnectionAuthMethod** contiene un objeto JSON serializado con las siguientes propiedades:

```
{
  "scope": "{ hub | device}",
  "type": "{ symkey | sas}",
  "issuer": "iothub"
}
```

## <a name="cloud-to-device-messages"></a>Mensajes de nube a dispositivo
Se envían mensajes de la nube al dispositivo mediante un punto de conexión orientado al servicio (**/messages/devicebound**). Un dispositivo los recibe mediante un punto de conexión específico del dispositivo (**/devices/{IdDeDispositivo}/messages/devicebound**).

Cada mensaje de la nube al dispositivo se dirige a un único dispositivo al establecer la propiedad **to** en **/devices/{IdDeDispositivo}/messages/devicebound**.

> [!IMPORTANT]
> La cola de cada dispositivo puede contener como máximo 50 mensajes de la nube al dispositivo. Si se intenta enviar más mensajes al mismo dispositivo, se producirá un error.
> 
> [!NOTE]
> Al enviar mensajes de la nube al dispositivo, los valores y los nombres de propiedad solo pueden contener caracteres alfanuméricos ASCII y ``{'!', '#', '$', '%, '&', "'", '*', '*', '+', '-', '.', '^', '_', '`', '|', '~'}``.
> 
> 

### <a name="message-lifecycle"></a>Ciclo de vida de los mensajes
Para garantizar la entrega de mensajes al menos una vez, IoT Hub conserva los mensajes de nube a dispositivo en colas para cada dispositivo. Los dispositivos tienen que confirmar explícitamente la *finalización* para que el Centro de IoT los quite de la cola. Esto garantiza la resistencia frente a errores de dispositivo y de conectividad.

En el diagrama siguiente se detalla el gráfico de estado del ciclo de vida de un mensaje de nube a dispositivo.

![Ciclo de vida de los mensajes de nube a dispositivo][img-lifecycle]

Cuando el servicio envía un mensaje, se considera que está *Enqueued*(En cola). Cuando un dispositivo quiere *recibir* un mensaje, IoT Hub *bloquea* dicho mensaje (establece el estado en **Invisible**) para permitir que otros subprocesos del mismo dispositivo empiecen a recibir otros mensajes. Cuando el subproceso de un dispositivo termina de procesar un mensaje, informa al Centro de IoT *finalizando* dicho mensaje.

Un dispositivo también puede:

* *Rechazar* el mensaje, lo que hace que el Centro de IoT lo establezca en estado **Deadlettered** (Procesado como devuelto). Nota: Los dispositivos que se conectan con MQTT no pueden rechazar mensajes de la nube al dispositivo.
* *Abandonar* el mensaje, lo que hace que el Centro de IoT vuelva a ponerlo en la cola con el estado **Enqueued**(En cola).

Podría producirse un error en el subproceso al procesar un mensaje sin notificar a Centro de IoT. En este caso, los mensajes pasan automáticamente del estado **Invisible** al estado **Enqueued** (En cola) después de un *tiempo de espera de visibilidad (o bloqueo)*. El valor predeterminado de este tiempo de espera es un minuto.

Un mensaje puede cambiar entre los estados **Enqueued** (En cola) e **Invisible**, como mucho, el número de veces especificado en la propiedad **Número máximo de entregas** en IoT Hub. Después de ese número de transiciones, el Centro de IoT establece el estado del mensaje en **Deadlettered**(Procesado como devuelto). De igual forma, IoT Hub establece el estado de un mensaje en **Deadlettered** (Procesado como devuelto) después de su fecha de caducidad [consulte [Expiración de mensajes (periodo de vida)][lnk-ttl]].

Para ver un tutorial sobre los mensajes de la nube al dispositivo, consulte [Tutorial: cómo enviar mensajes de la nube a un dispositivo con IoT Hub][lnk-c2d-tutorial]. Para consultar temas de referencia sobre cómo exponen los diferentes SDK IoT de Azure la funcionalidad de la nube al dispositivo, consulte [SDK de IoT de Azure][lnk-sdks].

> [!NOTE]
> Normalmente los mensajes de nube a dispositivo se completan siempre que la pérdida del mensaje no afecte a la lógica de aplicación. Por ejemplo, el contenido del mensaje se ha guardado correctamente en el almacenamiento local, o se ha ejecutado correctamente una operación. El mensaje también puede llevar información transitoria, cuya pérdida no afectaría a la funcionalidad de la aplicación. A veces, para tareas de larga duración, puede completar el mensaje de nube a dispositivo después de guardar la descripción de la tarea en el almacenamiento local. A continuación, se puede notificar al back-end de solución con uno o más mensajes de dispositivo a nube en distintas fases de progreso de la tarea.
> 
> 

### <a name="message-expiration-time-to-live"></a>Expiración de mensajes (período de vida)
Cada mensaje de nube a dispositivo tiene una fecha de expiración. La puede establecer el servicio (en la propiedad **ExpiryTimeUtc**), o bien IoT Hub mediante el *período de vida* predeterminado especificado como propiedad de IoT Hub. Consulte [Opciones de configuración de la nube al dispositivo][lnk-c2d-configuration].

> [!NOTE]
> Una forma habitual de aprovechar la expiración de los mensajes y evitar enviarlos a dispositivos desconectados consiste en establecer valores cortos de período de vida. Así se consigue el mismo resultado que al mantener el estado de la conexión de los dispositivos, con la diferencia de que la primera opción resulta más eficiente. Cuando solicita confirmaciones de mensajes, IoT Hub le notificará qué dispositivos pueden recibir mensajes y qué dispositivos no están conectados o presentan errores.
> 
> 

### <a name="message-feedback"></a>Comentarios de mensajes
Cuando envía un mensaje de nube a dispositivo, el servicio puede solicitar la entrega de los comentarios de cada mensaje en relación con el estado final de ese mensaje.

* Si establece la propiedad **Ack** en **positive**, IoT Hub genera un mensaje de comentarios únicamente si el mensaje de la nube al dispositivo alcanza el estado **Completed** (Completado).
* Si establece la propiedad **Ack** en **negative**, IoT Hub genera un mensaje de comentarios únicamente si el mensaje de la nube al dispositivo alcanza el estado **Deadlettered** (Procesado como devuelto).
* Al establecer la propiedad **Ack** en **full**, IoT Hub genera un mensaje de comentarios en ambos casos.

> [!NOTE]
> Si **Ack** es **full** y no recibe un mensaje de comentarios, significa que este ha expirado. El servicio no puede saber qué ha ocurrido con el mensaje original. En la práctica, un servicio debe asegurarse de que puede procesar el comentario antes de que expire. El tiempo de expiración máximo es de dos días, lo que permite tener tiempo suficiente para poner de nuevo en funcionamiento el servicio en caso de error.
> 
> 

Como se explica en [Puntos de conexión][lnk-endpoints], IoT Hub envía los comentarios a través de un punto de conexión accesible desde el servicio (**/messages/servicebound/feedback**) en forma de mensajes. La semántica de recepción de los comentarios es la misma que para los mensajes de la nube al dispositivo; además, tienen el mismo [ciclo de vida de los mensajes][lnk-lifecycle]. Siempre que sea posible, los comentarios de mensajes se agrupan en un único mensaje, con el formato siguiente:

| Propiedad | Description |
| --- | --- |
| EnqueuedTime |Marca de tiempo que indica cuándo se creó el mensaje. |
| UserId |`{iot hub name}` |
| ContentType |`application/vnd.microsoft.iothub.feedback.json` |

El cuerpo es una matriz serializada de JSON de registros, cada uno con las siguientes propiedades:

| Propiedad | Description |
| --- | --- |
| EnqueuedTimeUtc |Marca de tiempo que indica cuándo se produjo el resultado del mensaje. Por ejemplo, el dispositivo completado o el mensaje expirado. |
| OriginalMessageId |**MessageId** del mensaje de nube a dispositivo al que pertenece esta información de comentarios. |
| StatusCode |Cadena necesaria. Se utiliza en los mensajes de comentarios generados por el Centro de IoT. <br/> "Success" <br/> "Expired" <br/> 'DeliveryCountExceeded' <br/> "Rejected" <br/> 'Purged' |
| Descripción |Valores de cadena para **StatusCode**. |
| deviceId |**DeviceId** del dispositivo de destino del mensaje de nube a dispositivo al que pertenece este elemento de comentarios. |
| DeviceGenerationId |**DeviceGenerationId** del dispositivo de destino del mensaje de nube a dispositivo al que pertenece este elemento de comentarios. |

> [!IMPORTANT]
> El servicio tiene que especificar un valor de **MessageId** para el mensaje de nube a dispositivo para poder correlacionar sus comentarios con el mensaje original.
> 
> 

En el ejemplo siguiente se muestra el cuerpo de un mensaje de comentarios.

```
[
  {
    "OriginalMessageId": "0987654321",
    "EnqueuedTimeUtc": "2015-07-28T16:24:48.789Z",
    "StatusCode": 0
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

### <a name="cloud-to-device-configuration-options"></a>Opciones de configuración de la nube al dispositivo
Cada Centro de IoT expone las siguientes opciones de configuración para la mensajería de nube a dispositivo.

| Propiedad | Description | Intervalo y valor predeterminado |
| --- | --- | --- |
| defaultTtlAsIso8601 |TTL predeterminado para los mensajes de nube a dispositivo. |Intervalo de ISO_8601 hasta 2D (1 minuto como mínimo). Valor predeterminado: 1 hora. |
| maxDeliveryCount |Número máximo de entregas para las colas de nube a dispositivo por dispositivo. |De 1 a 100. Valor predeterminado: 10 |
| feedback.ttlAsIso8601 |Retención de mensajes de comentarios del límite de servicio. |Intervalo de ISO_8601 hasta 2D (1 minuto como mínimo). Valor predeterminado: 1 hora. |
| feedback.maxDeliveryCount |Número máximo de entregas para la cola de comentarios. |De 1 a 100. Valor predeterminado: 100. |

Para más información, consulte el artículo sobre la [creación de centros de IoT][lnk-portal].

## <a name="read-device-to-cloud-messages"></a>Lectura de mensajes del dispositivo a la nube
IoT Hub expone el punto de conexión integrado **messages/events** para los servicios de back-end con el fin de leer los mensajes de dispositivo a nube recibidos por el centro. Este punto de conexión es compatible con Event Hubs, lo que permite usar cualquiera de los mecanismos del servicio Event Hubs para leer mensajes.

También puede crear puntos de conexión personalizados en IoT Hub. IoT Hub admite actualmente Event Hubs, colas de Service Bus y temas de Service Bus como puntos de conexión personalizados. Para más información sobre la lectura desde esos servicios, consulte lo siguiente: lectura desde [Event Hubs][lnk-getstarted-eh], lectura desde [colas de Service Bus][lnk-getstarted-queue] y lectura desde [temas de Service Bus][lnk-getstarted-topic].

### <a name="reading-from-the-built-in-endpoint"></a>Lectura desde el punto de conexión integrado

Al usar el [SDK de Azure Service Bus para .NET][lnk-servicebus-sdk] o [Event Hubs - host del procesador de eventos][lnk-eventprocessorhost], puede usar cualquier cadena de conexión del centro de IoT con los permisos correctos. A continuación, utilice **messages/events** como el nombre del Centro de eventos.

Cuando use SDK (o integraciones de productos) que no detecten IoT Hub, tiene que recuperar un punto de conexión y un nombre compatibles con Event Hub de la configuración de IoT Hub en [Azure Portal][lnk-management-portal]:

1. En la hoja IoT Hub, haga clic en **Puntos de conexión**.
2. En la sección **Built-in endpoints** (Puntos de conexión integrados), haga clic en **Eventos**. La hoja contiene los siguientes valores: **Event Hub-compatible endpoint** (Punto de conexión compatible con el Centro de eventos), **Event Hub-compatible name** (Nombre compatible con el Centro de eventos), **Particiones**, **Tiempo de retención** y **Grupos de consumidores**.
   
    ![Configuración de dispositivo a nube][img-eventhubcompatible]

> [!NOTE]
> El SDK de IoT Hub requiere el nombre del punto de conexión de IoT Hub, que es **messages/events** tal y como se muestra en la hoja **Puntos de conexión**.
>
>

> [!NOTE]
> Si el SDK que utiliza requiere un valor de **Nombre de host** o **Espacio de nombres**, quite el esquema de **Event Hub-compatible endpoint** (Punto de conexión compatible con el Centro de eventos). Por ejemplo, si el punto de conexión compatible con el Centro de eventos es **sb://iothub-ns-myiothub-1234.servicebus.windows.net/**, el **nombre de host** sería **iothub-ns-myiothub-1234.servicebus.windows.net** y el **espacio de nombres** sería **iothub-ns-myiothub-1234**.
> 
>

A continuación, puede usar cualquier directiva de acceso compartido que tenga permisos **ServiceConnect** para conectarse a la instancia de Event Hub especificada.

En caso de que tenga que crear una cadena de conexión del Centro de eventos con la información anterior, use el modelo siguiente:

```
Endpoint={Event Hub-compatible endpoint};SharedAccessKeyName={iot hub policy name};SharedAccessKey={iot hub policy key}
```

La siguiente es una lista de los SDK e integraciones que puede usar con los puntos de conexión compatibles con el Centro de eventos que muestra Centro de IoT:

* [Cliente de Centros de eventos de Java](https://github.com/hdinsight/eventhubs-client)
* [Spout de Apache Storm](../hdinsight/hdinsight-storm-develop-csharp-event-hub-topology.md). Puede ver el [origen de spout](https://github.com/apache/storm/tree/master/external/storm-eventhubs) en GitHub.
* [Integración de Apache Spark](../hdinsight/hdinsight-apache-spark-eventhub-streaming.md)

## <a name="reference-topics"></a>Temas de referencia:
Los siguientes temas de referencia proporcionan más información sobre el intercambio de mensajes con IoT Hub.

## <a name="message-format"></a>Formato de los mensajes
Los mensajes del Centro de IoT constan de:

* Un conjunto de *propiedades del sistema*. Propiedades que IoT Hub interpreta o establece. Este conjunto es el predeterminado.
* Un conjunto de *propiedades de la aplicación*. Diccionario de propiedades de cadena que la aplicación puede definir y a las que puede acceder sin necesidad de deserializar el cuerpo del mensaje. Centro de IoT nunca modifica estas propiedades.
* Un cuerpo binario opaco.

Para más información sobre cómo se codifica el mensaje en distintos protocolos, consulte [SDK de IoT de Azure][lnk-sdks].

En la siguiente tabla, aparece el conjunto de propiedades del sistema en los mensajes de IoT Hub.

| Propiedad | Description |
| --- | --- |
| MessageId |Un identificador configurable por el usuario para el mensaje, utilizado para patrones de solicitud y respuesta. Formato: una cadena que distingue mayúsculas y minúsculas (de hasta 128 caracteres) de caracteres alfanuméricos ASCII de 7 bits + `{'-', ':',’.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '$', '''}`. |
| Número de secuencia |Un número (exclusivo para cada cola de dispositivo) asignado por Centro de IoT a cada mensaje de nube a dispositivo. |
| Para |Un destino especificado en los mensajes [de la nube al dispositivo][lnk-c2d]. |
| ExpiryTimeUtc |Fecha y hora de la expiración del mensaje. |
| EnqueuedTime |Fecha y hora en la que IoT Hub recibió el mensaje de [nube a dispositivo][lnk-c2d]. |
| CorrelationId |Cadena de propiedad en un mensaje de respuesta que normalmente contiene el identificador del mensaje de la solicitud en los patrones de solicitud y respuesta. |
| UserId |Un identificador que se utiliza para especificar el origen de los mensajes. Cuando el Centro de IoT genera mensajes, se establece en `{iot hub name}`. |
| Ack |Un generador de mensajes de comentarios. Esta propiedad se usa en los mensajes de nube a dispositivo para solicitar a Centro de IoT que genere mensajes de comentarios debido al consumo del mensaje por el dispositivo. Valores posibles: **none** (valor predeterminado): no se genera ningún mensaje de comentarios, **positive**: recibe un mensaje de comentarios si el mensaje se completó, **negative**: recibe un mensaje de comentarios si el mensaje expiró (o si se alcanzó el número máximo de entregas) sin que se complete en el dispositivo, y **full**: comentarios positivos y negativos. Para más información, consulte [Comentarios de mensajes][lnk-feedback]. |
| ConnectionDeviceId |Un identificador establecido por Centro de IoT en los mensajes de dispositivo a nube. Contiene el **deviceId** del dispositivo que envió el mensaje. |
| ConnectionDeviceGenerationId |Un identificador establecido por Centro de IoT en los mensajes de dispositivo a nube. Contiene el valor **generationId** (como se indica en [Propiedades de identidad del dispositivo][lnk-device-properties]) del dispositivo que envió el mensaje. |
| ConnectionAuthMethod |Un método de autenticación establecido por Centro de IoT en los mensajes de dispositivo a nube. Esta propiedad contiene información sobre el método de autenticación usado para autenticar el dispositivo que envía el mensaje. Para más información, consulte la sección [Propiedades contra la suplantación][lnk-antispoofing]. |

## <a name="message-size"></a>Tamaño del mensaje

IoT Hub mide el tamaño de los mensajes de una manera independiente del protocolo, teniendo en cuenta solo la carga real. El tamaño en bytes se calcula como la suma de los siguientes elementos:

* El tamaño del cuerpo en bytes, además de
* El tamaño en bytes de todos los valores de las propiedades del sistema del mensaje, además de
* El tamaño en bytes de todos los valores y nombres de propiedades del usuario.

Tenga en cuenta solamente se pueden usar caracteres ASCII para valores y los nombres de propiedades, por lo que la longitud de las cadenas es igual al tamaño en bytes.

## <a name="communication-protocols"></a>Protocolos de comunicación
IoT Hub permite a los dispositivos usar protocolos [MQTT][lnk-mqtt], MQTT sobre WebSockets, [AMQP][lnk-amqp], AMQP sobre WebSockets y HTTP para comunicaciones del lado del dispositivo. La tabla siguiente proporciona recomendaciones generales para la elección del protocolo:

| Protocol | Cuándo elegir este protocolo |
| --- | --- |
| MQTT <br> MQTT sobre WebSocket |Utilice todos los dispositivos que no requieren conexión a varios dispositivos (cada uno con sus propias credenciales por servicio) sobre la misma conexión TLS. |
| AMQP <br> AMQP sobre WebSocket |Usar en puertas de enlace de campo y en la nube para aprovechar las ventajas de la multiplexación de la conexión entre dispositivos. |
| HTTP |Usar con dispositivos que no admiten otros protocolos. |

Considere los siguientes aspectos a la hora de elegir el protocolo para las comunicaciones del dispositivo:

* **Patrón de nube a dispositivo**. HTTP no cuenta con una forma eficaz de implementar la inserción de servidor. Por lo tanto, cuando se usa HTTP, los dispositivos sondean los mensajes de nube a dispositivo en IoT Hub. Este enfoque es ineficaz tanto para el dispositivo como para IoT Hub. Según las directrices actuales de HTTP, cada dispositivo sondeará si hay mensajes cada 25 minutos o más. Por otro lado, AMQP y MQTT admiten la inserción de servidor cuando se reciben mensajes de nube a dispositivo. Permiten inserciones inmediatas de mensajes desde Centro de IoT en el dispositivo. Si le preocupa la latencia de entrega, es mucho mejor usar los protocolos MQTT o AMQP. Para dispositivos conectados en raras ocasiones, HTTP funciona bien.
* **Puertas de enlace de campo**. Cuando se utiliza MQTT y HTTP, no puede conectar varios dispositivos (cada uno con sus propias credenciales por dispositivo) con la misma conexión TLS. Por lo tanto, en los [escenarios de puerta de enlace de campo][lnk-azure-gateway-guidance], estos protocolos no son óptimos, ya que requieren una conexión TLS entre la puerta de enlace e IoT Hub para cada dispositivo conectado a ella.
* **Dispositivos con bajos recursos**. Las bibliotecas de MQTT y HTTP tienen una huella menor que las bibliotecas de AMQP. Por ello, si el dispositivo tiene recursos limitados (por ejemplo, menos de 1 MB de RAM), estos protocolos podrían ser la única opción disponible.
* **Cruce seguro de red**. El protocolo AMQP estándar usa el puerto 5671, mientras que el MQTT realiza la escucha en el puerto 8883, lo que podría producir problemas en las redes cerradas para los protocolos que no sean HTTP. MQTT sobre WebSockets, AMQP sobre WebSockets y HTTP están disponibles para usarse en este escenario.
* **Tamaño de carga**. MQTT y AMQP son protocolos binarios que producen cargas más compactas que HTTP.

> [!NOTE]
> Cuando se usa HTTP, cada dispositivo sondeará si hay mensajes de la nube a dispositivo cada 25 minutos o más. Sin embargo, durante el desarrollo, es aceptable sondear con una frecuencia mayor de 25 minutos.
> 
> 

## <a name="port-numbers"></a>Números de puerto
Los dispositivos pueden comunicarse con el Centro de IoT en Azure mediante diversos protocolos. Normalmente, la elección del protocolo se basa en los requisitos específicos de la solución. En la tabla siguiente se indican los puertos de salida que deben estar abiertos para que un dispositivo pueda usar un protocolo concreto:

| Protocol | Port |
| --- | --- |
| MQTT |8883 |
| MQTT sobre WebSockets |443 |
| AMQP |5671 |
| AMQP sobre WebSockets |443 |
| HTTP |443 |
| LWM2M (Administración de dispositivos) |5684 |

Una vez creado un centro de IoT en una región de Azure, el centro de IoT mantiene la misma dirección IP durante toda su existencia. Sin embargo, para mantener la calidad de servicio, si Microsoft mueve el Centro de IoT a una unidad de escalado diferente, se le asigna una nueva dirección IP.

## <a name="notes-on-mqtt-support"></a>Notas sobre la compatibilidad con MQTT
Centro de IoT implementa el protocolo MQTT v3.1.1 con las siguientes limitaciones y comportamiento específico:

* **No se admite QoS 2**. Cuando un cliente de dispositivo publica un mensaje con **QoS 2**, IoT Hub cierra la conexión de red. Cuando una aplicación de dispositivo se suscribe a un tema con **QoS 2**, IoT Hub concede el QoS de nivel 1 (el máximo) en el paquete **SUBACK**.
* **Los mensajes Retain no se conservan**. Si una aplicación de dispositivo publica un mensaje con la marca RETAIN establecida en 1, IoT Hub agrega la propiedad de aplicación **x-opt-retain** al mensaje. Esto significa que IoT Hub no conserva el mensaje, sino que lo pasa a la aplicación de back-end.

Para más información, consulte [Compatibilidad con MQTT de IoT Hub][lnk-devguide-mqtt].

Como consideración final, debe revisarse la sección [Puerta de enlace de protocolos de IoT de Azure][lnk-azure-protocol-gateway], donde se explica cómo implementar una puerta de enlace de protocolo personalizado de alto rendimiento que interactúe directamente con IoT Hub. La puerta de enlace de protocolos de IoT de Azure le permite personalizar el protocolo del dispositivo para dar cabida a las implementaciones de MQTT de Brownfield u otros protocolos personalizados. Sin embargo, este enfoque requiere que se ejecute y se ponga en funcionamiento una puerta de enlace de protocolo personalizado.

## <a name="additional-reference-material"></a>Material de referencia adicional
Otros temas de referencia en la guía del desarrollador de IoT Hub son los siguientes:

* En [Puntos de conexión de IoT Hub][lnk-endpoints], se describen los diferentes puntos de conexión que expone cada centro de IoT Hub para las operaciones en tiempo de ejecución y de administración.
* En [Cuotas y limitación][lnk-quotas], se describen las cuotas que se aplican al servicio IoT Hub y el comportamiento de limitación que se espera al usar el servicio.
* En [SDK de dispositivo y servicio IoT de Azure][lnk-sdks] se muestran los diversos SDK de lenguaje que puede usar para desarrollar aplicaciones para dispositivo y de servicio que interactúen con IoT Hub.
* En [Lenguaje de consulta de IoT Hub para gemelos y trabajos][lnk-query], se describe el lenguaje de consulta de IoT Hub que se puede usar para recuperar información de IoT Hub sobre los dispositivos gemelos y trabajos.
* En [Compatibilidad con MQTT de IoT Hub][lnk-devguide-mqtt], se proporciona más información sobre la compatibilidad de IoT Hub con el protocolo MQTT.

## <a name="next-steps"></a>Pasos siguientes
Ahora que ha aprendido a enviar y recibir mensajes con IoT Hub, pueden interesarle los siguientes temas de la Guía del desarrollador de IoT Hub:

* [Upload files from a device][lnk-devguide-upload] (Carga de archivos desde un dispositivo)
* [Administrar identidades del dispositivo en IoT Hub][lnk-devguide-identities]
* [Control del acceso a IoT Hub][lnk-devguide-security]
* [Uso de dispositivos gemelos para sincronizar el estado y las configuraciones][lnk-devguide-device-twins]
* [Invocación de un método directo en un dispositivo][lnk-devguide-directmethods]
* [Programación de trabajos en varios dispositivos][lnk-devguide-jobs]

Si desea probar algunos de los conceptos descritos en este artículo, puede interesarle los siguientes tutoriales de IoT Hub:

* [Introducción a Azure IoT Hub][lnk-getstarted-tutorial]
* [Cómo enviar mensajes de la nube a un dispositivo con IoT Hub][lnk-c2d-tutorial]
* [Procesamiento de mensajes del dispositivo a la nube de IoT Hub][lnk-d2c-tutorial]

[img-lifecycle]: ./media/iot-hub-devguide-messaging/lifecycle.png
[img-eventhubcompatible]: ./media/iot-hub-devguide-messaging/eventhubcompatible.png

[lnk-resource-provider-apis]: https://docs.microsoft.com/rest/api/iothub/iothubresource
[lnk-azure-gateway-guidance]: iot-hub-devguide-endpoints.md#field-gateways
[lnk-guidance-scale]: iot-hub-scaling.md
[lnk-azure-protocol-gateway]: iot-hub-protocol-gateway.md
[lnk-amqp]: http://docs.oasis-open.org/amqp/core/v1.0/os/amqp-core-complete-v1.0-os.pdf
[lnk-mqtt]: http://docs.oasis-open.org/mqtt/mqtt/v3.1.1/mqtt-v3.1.1.pdf
[lnk-event-hubs]: http://azure.microsoft.com/documentation/services/event-hubs/
[lnk-event-hubs-consuming-events]: ../event-hubs/event-hubs-programming-guide.md#event-consumers
[lnk-management-portal]: https://portal.azure.com
[lnk-servicebus]: http://azure.microsoft.com/documentation/services/service-bus/
[lnk-eventhub-partitions]: ../event-hubs/event-hubs-overview.md#partitions
[lnk-portal]: iot-hub-create-through-portal.md
[lnk-getstarted-eh]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-getstarted-queue]: ../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md
[lnk-getstarted-topic]: ../service-bus-messaging/service-bus-dotnet-how-to-use-topics-subscriptions.md

[lnk-c2d-guidance]: iot-hub-devguide-c2d-guidance.md
[lnk-d2c-guidance]: iot-hub-devguide-d2c-guidance.md

[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-d2c]: iot-hub-devguide-messaging.md#device-to-cloud-messages
[lnk-c2d]: iot-hub-devguide-messaging.md#cloud-to-device-messages
[lnk-compatible-endpoint]: iot-hub-devguide-messaging.md#read-device-to-cloud-messages
[lnk-protocols]: iot-hub-devguide-messaging.md#communication-protocols
[lnk-message-format]: iot-hub-devguide-messaging.md#message-format
[lnk-device-properties]: iot-hub-devguide-identity-registry.md#device-identity-properties
[lnk-ttl]: iot-hub-devguide-messaging.md#message-expiration-time-to-live
[lnk-c2d-configuration]: iot-hub-devguide-messaging.md#cloud-to-device-configuration-options
[lnk-lifecycle]: iot-hub-devguide-messaging.md#message-lifecycle
[lnk-feedback]: iot-hub-devguide-messaging.md#message-feedback
[lnk-antispoofing]: iot-hub-devguide-messaging.md#anti-spoofing-properties
[lnk-compare]: iot-hub-compare-event-hubs.md

[lnk-devguide-upload]: iot-hub-devguide-file-upload.md
[lnk-devguide-identities]: iot-hub-devguide-identity-registry.md
[lnk-devguide-security]: iot-hub-devguide-security.md
[lnk-devguide-device-twins]: iot-hub-devguide-device-twins.md
[lnk-devguide-directmethods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-servicebus-sdk]: https://www.nuget.org/packages/WindowsAzure.ServiceBus
[lnk-eventprocessorhost]: http://blogs.msdn.com/b/servicebus/archive/2015/01/16/event-processor-host-best-practices-part-1.aspx
[lnk-devguide-query-language]: iot-hub-devguide-query-language.md
[lnk-devguide-endpoints]: iot-hub-devguide-endpoints.md

[lnk-getstarted-tutorial]: iot-hub-csharp-csharp-getstarted.md
[lnk-c2d-tutorial]: iot-hub-csharp-csharp-c2d.md
[lnk-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md
[lnk-event-hub-partitions]: ../event-hubs/event-hubs-what-is-event-hubs.md#partitions

