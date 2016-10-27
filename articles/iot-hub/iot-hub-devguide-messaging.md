<properties
 pageTitle="Guía del desarrollador: mensajería | Microsoft Azure"
 description="Guía del desarrollador de Azure IoT Hub: mensajería del dispositivo a la nube y de la nube al dispositivo"
 services="iot-hub"
 documentationCenter=".net"
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="multiple"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="09/30/2016" 
 ms.author="dobett"/>


# <a name="send-and-receive-messages-with-iot-hub"></a>Envío y recepción de mensajes con IoT Hub

## <a name="overview"></a>Información general

IoT Hub proporciona los siguientes primitivos de mensajería para comunicarse con un dispositivo:

- [Del dispositivo a la nube][lnk-d2c] desde un dispositivo al back-end de una aplicación.
- [De la nube al dispositivo][lnk-c2d] desde el back-end de una aplicación (*servicio* o *nube*).

Las propiedades básicas de la funcionalidad de mensajería del Centro de IoT son la confiabilidad y durabilidad de los mensajes. Estas propiedades permiten la resistencia a la conectividad intermitente en el dispositivo y a los picos de carga del procesamiento de eventos en la nube. El Centro de IoT implementa *al menos una vez* garantías de entrega para la mensajería del dispositivo a la nube y de la nube al dispositivo.

IoT Hub admite varios [protocolos orientados al dispositivo][lnk-protocols] (por ejemplo, MQTT, AMQP y HTTP). Para admitir la interoperabilidad sin problemas entre protocolos, IoT Hub define un [formato de mensaje común][lnk-message-format] compatible con todos los protocolos orientados al dispositivo.

IoT Hub expone un [punto de conexión compatible con Event Hubs][lnk-compatible-endpoint] para permitir que las aplicaciones de back-end lean los mensajes del dispositivo a la nube recibidos por el centro.

### <a name="when-to-use"></a>Cuándo se debe usar

La mensajería es una funcionalidad fundamental de IoT Hub. Úsela siempre que necesite enviar mensajes desde el dispositivo a su back-end o desde el back-end a un dispositivo.

Para ver una comparación de los servicios de IoT Hub y Event Hubs, consulte [Comparación de IoT Hub y Event Hubs][lnk-compare].

## <a name="device-to-cloud-messages"></a>Mensajes de dispositivo a nube

Los mensajes del dispositivo a la nube se envían a través de un punto de conexión orientado al dispositivo (**/devices/{IdDeDispositivo}/messages/events**). El servicio de back-end recibe mensajes del dispositivo a la nube a través de un punto de conexión orientado al servicio (**/messages/events**) que es compatible con [Event Hubs][lnk-event-hubs]. Por lo tanto, puede usar la [integración de Event Hubs estándar y los SDK][lnk-compatible-endpoint] para recibir mensajes del dispositivo a la nube.

IoT Hub implementa la mensajería del dispositivo a la nube de forma similar a [Event Hubs][lnk-event-hubs]. Los mensajes del dispositivo a la nube de IoT Hub son más parecidos a los *eventos* de Event Hubs que a los *mensajes* de [Service Bus][lnk-servicebus].

Esta implementación tiene las implicaciones siguientes:

* Como sucede con los eventos de Event Hubs, los mensajes del dispositivo a la nube son duraderos y se conservan en un centro de IoT hasta siete días (consulte [Opciones de configuración del dispositivo a la nube][lnk-d2c-configuration]).
* Los mensajes del dispositivo a la nube se fragmentan entre un conjunto fijo de particiones que se establece en el momento de la creación (consulte [Opciones de configuración del dispositivo a la nube][lnk-d2c-configuration]).
* De igual forma que los centros de eventos, los clientes que leen mensajes de dispositivo a nube tienen que administrar particiones y puntos de control. Consulte el artículo sobre el [consumo de eventos en Event Hubs][lnk-event-hubs-consuming-events].
* Como los eventos de Event Hubs, los mensajes del dispositivo a la nube pueden tener como máximo 256 kB y se pueden agrupar en lotes para optimizar los envíos. Los lotes pueden tener un tamaño máximo de 256 KB y como máximo 500 mensajes.

De todas formas, hay algunas diferencias importantes entre los mensajes de dispositivo a nube de Centro de IoT y los Centros de eventos:

* Como se explica en la sección [Control access to IoT Hub][lnk-devguide-security] (Control del acceso a IoT Hub), IoT Hub permite la autenticación y el control de acceso por dispositivo.///
* IoT Hub permite millones de dispositivos conectados simultáneamente (consulte [Quotas and throttling][lnk-quotas] [Cuotas y limitación]), mientras que Event Hubs se limita a 5000 conexiones AMQP por espacio de nombres.///
* El Centro de IoT no permite el particionamiento arbitrario mediante una **PartitionKey**. Los mensajes de dispositivo a nube se dividen en particiones en función de su valor de **deviceId**de origen.
* El escalado de un Centro de IoT es ligeramente diferente al de los centros de eventos. Para más información, consulte [Escalado de IoT Hub][lnk-guidance-scale].

> [AZURE.NOTE] No puede sustituir IoT Hub por Event Hubs en todos los escenarios. Por ejemplo, en algunos cálculos de procesamiento de eventos, podría ser necesario volver a crear particiones de eventos con respecto a un campo o propiedad diferentes antes de analizar los flujos de datos. En esta situación, puede usar un Centro de eventos para desacoplar las dos partes de la canalización de procesamiento de la transmisión. Para más información, consulte la sección sobre *particiones* del artículo [Información general de Azure Event Hubs][lnk-eventhub-partitions].

Para detalles sobre cómo usar la mensajería del dispositivo a la nube, consulte [IoT Hub APIs and SDKs][lnk-sdks] (API y SDK de IoT Hub).///

> [AZURE.NOTE] Cuando se usa HTTP para enviar mensajes de dispositivo a nube, los valores y los nombres de propiedad solo pueden contener caracteres alfanuméricos ASCII y ``{'!', '#', '$', '%, '&', "'", '*', '*', '+', '-', '.', '^', '_', '`', '|', '~'}``.

### <a name="non-telemetry-traffic"></a>Tráfico sin telemetría

A menudo, además de los puntos de datos de telemetría, los dispositivos también envían mensajes y solicitudes que requieren la ejecución y el control de la capa de lógica de negocio de la aplicación. Por ejemplo, alertas críticas que deben desencadenar una acción específica en el back-end o respuestas de dispositivo a los comandos enviados desde el back-end.

Para más información sobre la mejor manera de procesar este tipo de mensaje, consulte [Tutorial: procesamiento de mensajes de dispositivo a la nube de IoT Hub][lnk-d2c-tutorial].

### <a name="device-to-cloud-configuration-options"></a>Opciones de configuración del dispositivo a la nube

Centro de IoT muestra las propiedades siguientes para permitirle controlar la mensajería de dispositivo a la nube.

* **Número de particiones**. Establezca esta propiedad durante la creación para definir el número de particiones para ingesta de eventos de dispositivo a la nube.
* **Tiempo de retención**. Esta propiedad especifica el tiempo de retención para los mensajes de dispositivo a nube. El valor predeterminado es un día, pero se puede aumentar a siete días.

Además, de forma análoga a los Centros de eventos, el Centro de IoT permite administrar los grupos de consumidores en el punto de conexión de recepción de dispositivo a nube.

Puede modificar todas estas propiedades mediante programación con las [API del proveedor de recursos de Azure IoT Hub][lnk-resource-provider-apis] o usando [Azure Portal][lnk-management-portal].

### <a name="anti-spoofing-properties"></a>Propiedades contra la suplantación

Para evitar la suplantación de dispositivos en los mensajes de dispositivo a nube, el Centro de IoT marca todos los mensajes con las siguientes propiedades:

* **ConnectionDeviceId**
* **ConnectionDeviceGenerationId**
* **ConnectionAuthMethod**

Las dos primeras contienen los valores **deviceId** y **generationId** del dispositivo de origen, tal como se indicó en [Device identity properties][lnk-device-properties] (Propiedades de identidad de dispositivos).///

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

>[AZURE.IMPORTANT] La cola de cada dispositivo puede contener como máximo 50 mensajes de la nube al dispositivo. Si se intenta enviar más mensajes al mismo dispositivo, se producirá un error.

> [AZURE.NOTE] Al enviar mensajes de la nube al dispositivo, los valores y los nombres de propiedad solo pueden contener caracteres alfanuméricos ASCII y ``{'!', '#', '$', '%, '&', "'", '*', '*', '+', '-', '.', '^', '_', '`', '|', '~'}``.

### <a name="message-lifecycle"></a>Ciclo de vida de los mensajes

Para garantizar la entrega de mensajes al menos una vez, IoT Hub conserva los mensajes de la nube al dispositivo en colas para cada dispositivo. Los dispositivos tienen que confirmar explícitamente la *finalización* para que el Centro de IoT los quite de la cola. Esto garantiza la resistencia frente a errores de dispositivo y de conectividad.

En el diagrama siguiente se detalla el gráfico de estado del ciclo de vida de un mensaje de nube a dispositivo.

![Ciclo de vida de los mensajes de nube a dispositivo][img-lifecycle]

Cuando el servicio envía un mensaje, se considera que está *Enqueued*(En cola). Cuando un dispositivo quiere *recibir* un mensaje, IoT Hub *bloquea* dicho mensaje (establece el estado en **Invisible**) para permitir que otros subprocesos del mismo dispositivo empiecen a recibir otros mensajes. Cuando el subproceso de un dispositivo termina de procesar un mensaje, informa al Centro de IoT *finalizando* dicho mensaje.

Un dispositivo también puede:

- *Rechazar* el mensaje, lo que hace que el Centro de IoT lo establezca en estado **Deadlettered** (Procesado como devuelto). Nota: Los dispositivos que se conectan con MQTT no pueden rechazar mensajes de la nube al dispositivo.
- *Abandonar* el mensaje, lo que hace que el Centro de IoT vuelva a ponerlo en la cola con el estado **Enqueued**(En cola).

Podría producirse un error en el subproceso al procesar un mensaje sin notificar a Centro de IoT. En este caso, los mensajes pasan automáticamente del estado **Invisible** al estado **Enqueued** (En cola) después de un *tiempo de espera de visibilidad (o bloqueo)*. El valor predeterminado de este tiempo de espera es un minuto.

Un mensaje puede cambiar entre los estados **Enqueued** (En cola) e **Invisible**, como mucho, el número de veces especificado en la propiedad **Número máximo de entregas** en IoT Hub. Después de ese número de transiciones, el Centro de IoT establece el estado del mensaje en **Deadlettered**(Procesado como devuelto). De igual forma, IoT Hub establece el estado de un mensaje en **Deadlettered** (Procesado como devuelto) después de su fecha de expiración (consulte [Expiración de mensajes [período de vida]][lnk-ttl]).

Para ver un tutorial sobre los mensajes de la nube al dispositivo, consulte [Tutorial: cómo enviar mensajes de la nube a un dispositivo con IoT Hub][lnk-c2d-tutorial]. Para ver temas de referencia sobre cómo las diferentes API y SDK exponen la funcionalidad de la nube al dispositivo, consulte [IoT Hub APIs and SDKs][lnk-sdks] (API y SDK de IoT Hub).///

> [AZURE.NOTE] Normalmente los mensajes de nube a dispositivo se completan siempre que la pérdida del mensaje no afecte a la lógica de aplicación. Por ejemplo, el contenido del mensaje se ha guardado correctamente en el almacenamiento local, o se ha ejecutado correctamente una operación. El mensaje también puede llevar información transitoria, cuya pérdida no afectaría a la funcionalidad de la aplicación. A veces, para tareas de larga duración, puede completar el mensaje de nube a dispositivo después de guardar la descripción de la tarea en el almacenamiento local. A continuación, se puede notificar al back-end de aplicación con uno o más mensajes de dispositivo a nube en distintas fases de progreso de la tarea.

### <a name="message-expiration-(time-to-live)"></a>Expiración de mensajes (período de vida)

Cada mensaje de nube a dispositivo tiene una fecha de expiración. La puede establecer el servicio (en la propiedad **ExpiryTimeUtc**), o bien IoT Hub mediante el *período de vida* predeterminado especificado como propiedad de IoT Hub. Consulte [Opciones de configuración de la nube al dispositivo][lnk-c2d-configuration].

> [AZURE.NOTE] Una forma habitual de aprovechar la expiración de los mensajes y evitar enviarlos a dispositivos desconectados consiste en establecer valores cortos de período de vida. Así se consigue el mismo resultado que al mantener el estado de la conexión de los dispositivos, con la diferencia de que la primera opción resulta más eficiente. Cuando solicita confirmaciones de mensajes, IoT Hub le notificará qué dispositivos pueden recibir mensajes y qué dispositivos no están conectados o presentan errores.

### <a name="message-feedback"></a>Comentarios de mensajes

Cuando envía un mensaje de nube a dispositivo, el servicio puede solicitar la entrega de los comentarios de cada mensaje en relación con el estado final de ese mensaje.

- Si establece la propiedad **Ack** en **positive**, IoT Hub genera un mensaje de comentarios únicamente si el mensaje de la nube al dispositivo alcanza el estado **Completed** (Completado).
- Si establece la propiedad **Ack** en **negative**, IoT Hub genera un mensaje de comentarios únicamente si el mensaje de la nube al dispositivo alcanza el estado **Deadlettered** (Procesado como devuelto).
- Al establecer la propiedad **Ack** en **full**, IoT Hub genera un mensaje de comentarios en ambos casos.

> [AZURE.NOTE] Si **Ack** es **full** y no recibe un mensaje de comentarios, significa que este ha expirado. El servicio no puede saber qué ha ocurrido con el mensaje original. En la práctica, un servicio debe asegurarse de que puede procesar el comentario antes de que expire. El tiempo de expiración máximo es de dos días, lo que permite tener tiempo suficiente para poner de nuevo en funcionamiento el servicio en caso de error.

Como se explica en el tema sobre [puntos de conexión][lnk-endpoints], IoT Hub envía los comentarios a través de un punto de conexión orientado al servicio (**/messages/servicebound/feedback**) en forma de mensajes. La semántica de recepción de los comentarios es la misma que para los mensajes de la nube al dispositivo; además, tienen el mismo [ciclo de vida de los mensajes][lnk-lifecycle]. Siempre que sea posible, los comentarios de mensajes se agrupan en un único mensaje, con el formato siguiente:

| Propiedad | Description |
| -------- | ----------- |
| EnqueuedTime | Marca de tiempo que indica cuándo se creó el mensaje. |
| UserId | `{iot hub name}` |
| ContentType | `application/vnd.microsoft.iothub.feedback.json` |

El cuerpo es una matriz serializada de JSON de registros, cada uno con las siguientes propiedades:

| Propiedad | Description |
| -------- | ----------- |
| EnqueuedTimeUtc | Marca de tiempo que indica cuándo se produjo el resultado del mensaje. Por ejemplo, el dispositivo completado o el mensaje expirado. |
| OriginalMessageId | **MessageId** del mensaje de nube a dispositivo al que pertenece esta información de comentarios. |
| StatusCode | Entero requerido. Se utiliza en los mensajes de comentarios generados por el Centro de IoT. <br/> 0 = correcto <br/> 1 = mensaje expirado <br/> 2 = el número máximo de entregas excedido <br/>  3 = mensaje rechazado |
| Description | Valores de cadena para **StatusCode**. |
| deviceId | **DeviceId** del dispositivo de destino del mensaje de nube a dispositivo al que pertenece este elemento de comentarios. |
| DeviceGenerationId | **DeviceGenerationId** del dispositivo de destino del mensaje de nube a dispositivo al que pertenece este elemento de comentarios. |


>[AZURE.IMPORTANT] El servicio tiene que especificar un valor de **MessageId** para el mensaje de nube a dispositivo para poder correlacionar sus comentarios con el mensaje original.

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
| -------- | ----------- | ----------------- |
| defaultTtlAsIso8601 | TTL predeterminado para los mensajes de nube a dispositivo. | Intervalo de ISO_8601 hasta 2D (1 minuto como mínimo). Valor predeterminado: 1 hora. |
| maxDeliveryCount | Número máximo de entregas para las colas de nube a dispositivo por dispositivo. | De 1 a 100. Valor predeterminado: 10 |
| feedback.ttlAsIso8601 | Retención de mensajes de comentarios del límite de servicio. | Intervalo de ISO_8601 hasta 2D (1 minuto como mínimo). Valor predeterminado: 1 hora. |
| feedback.maxDeliveryCount | Número máximo de entregas para la cola de comentarios. | De 1 a 100. Valor predeterminado: 100. |

Para más información, consulte el artículo sobre la [creación de centros de IoT][lnk-portal].

## <a name="read-device-to-cloud-messages"></a>Lectura de mensajes del dispositivo a la nube

IoT Hub expone un punto de conexión para los servicios de back-end con el fin de leer los mensajes del dispositivo a la nube recibidos por el centro. El punto de conexión es compatible con Event Hubs, lo que permite usar cualquiera de los mecanismos del servicio Event Hubs para leer mensajes.

Cuando use el [SDK de Azure Service Bus para .NET][lnk-servicebus-sdk] o el [host del procesador de eventos de Event Hubs][lnk-eventprocessorhost], puede utilizar cualquier cadena de conexión de IoT Hub con los permisos correctos. A continuación, utilice **messages/events** como el nombre del Centro de eventos.

Cuando use SDK (o integraciones de productos) que no detecten IoT Hub, tiene que recuperar un punto de conexión compatible con Event Hubs y un nombre de Centro de eventos de la configuración de IoT Hub en [Azure Portal][lnk-management-portal]:

1. En la hoja Centro de IoT, haga clic en **Mensajería**.
2. En la sección **Configuración del dispositivo a la nube**, encontrará los valores **Extremo compatible con el Centro de eventos**, **Nombre compatible con el Centro de eventos** y **Particiones**.

    ![Configuración de dispositivo a nube][img-eventhubcompatible]

> [AZURE.NOTE] Si el SDK requiere un valor de **Nombre de host** o **Espacio de nombres**, quite el esquema del **Extremo compatible con el Centro de eventos**. Por ejemplo, si el punto de conexión compatible con el Centro de eventos es **sb://iothub-ns-myiothub-1234.servicebus.windows.net/**, el **nombre de host** sería **iothub-ns-myiothub-1234.servicebus.windows.net** y el **espacio de nombres** sería **iothub-ns-myiothub-1234**.

A continuación, puede usar cualquier directiva de seguridad de acceso compartido que tenga permisos **ServiceConnect** para conectarse al Centro de eventos especificado.

En caso de que tenga que crear una cadena de conexión del Centro de eventos con la información anterior, use el modelo siguiente:

```
Endpoint={Event Hub-compatible endpoint};SharedAccessKeyName={iot hub policy name};SharedAccessKey={iot hub policy key}
```

La siguiente es una lista de los SDK e integraciones que puede usar con los puntos de conexión compatibles con el Centro de eventos que muestra Centro de IoT:

* [Cliente de Centros de eventos de Java](https://github.com/hdinsight/eventhubs-client)
* [Spout de Apache Storm](../hdinsight/hdinsight-storm-develop-csharp-event-hub-topology.md). Puede ver el [origen de spout](https://github.com/apache/storm/tree/master/external/storm-eventhubs) en GitHub.
* [Integración de Apache Spark](../hdinsight/hdinsight-apache-spark-eventhub-streaming.md)

## <a name="reference"></a>Referencia

### <a name="message-format"></a>Formato de los mensajes

Los mensajes del Centro de IoT constan de:

* Un conjunto de *propiedades del sistema*. Propiedades que IoT Hub interpreta o establece. Este conjunto es el predeterminado.
* Un conjunto de *propiedades de la aplicación*. Diccionario de propiedades de cadena que la aplicación puede definir y a las que puede acceder sin necesidad de deserializar el cuerpo del mensaje. Centro de IoT nunca modifica estas propiedades.
* Un cuerpo binario opaco.

Para más información sobre cómo se codifica el mensaje en distintos protocolos, consulte [IoT Hub APIs and SDKs][lnk-sdks] (API y SDK de IoT Hub).///

En la siguiente tabla, aparece el conjunto de propiedades del sistema en los mensajes de IoT Hub.

| Propiedad | Description |
| -------- | ----------- |
| MessageId | Un identificador configurable por el usuario para el mensaje, utilizado para patrones de solicitud y respuesta. Formato: una cadena que distingue mayúsculas y minúsculas (de hasta 128 caracteres) de caracteres alfanuméricos ASCII de 7 bits + `{'-', ':',’.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '$', '''}`. |
| Número de secuencia | Un número (exclusivo para cada cola de dispositivo) asignado por Centro de IoT a cada mensaje de nube a dispositivo. |
| Para | Un destino especificado en los mensajes [de la nube al dispositivo][lnk-c2d]. |
| ExpiryTimeUtc | Fecha y hora de la expiración del mensaje. |
| EnqueuedTime | Fecha y hora en la que el Centro de IoT recibió el mensaje. |
| CorrelationId | Cadena de propiedad en un mensaje de respuesta que normalmente contiene el identificador del mensaje de la solicitud en los patrones de solicitud y respuesta. |
| UserId | Un identificador que se utiliza para especificar el origen de los mensajes. Cuando el Centro de IoT genera mensajes, se establece en `{iot hub name}`. |
| Ack | Un generador de mensajes de comentarios. Esta propiedad se usa en los mensajes de nube a dispositivo para solicitar a Centro de IoT que genere mensajes de comentarios debido al consumo del mensaje por el dispositivo. Valores posibles: **none** (valor predeterminado): no se genera ningún mensaje de comentarios, **positive**: recibe un mensaje de comentarios si el mensaje se completó, **negative**: recibe un mensaje de comentarios si el mensaje expiró (o si se alcanzó el número máximo de entregas) sin que se complete en el dispositivo, y **full**: comentarios positivos y negativos. Para más información, consulte [Comentarios de mensajes][lnk-feedback]. |
| ConnectionDeviceId | Un identificador establecido por Centro de IoT en los mensajes de dispositivo a nube. Contiene el **deviceId** del dispositivo que envió el mensaje. |
| ConnectionDeviceGenerationId | Un identificador establecido por Centro de IoT en los mensajes de dispositivo a nube. Contiene el valor **generationId** (como se indica en [Device identity properties][lnk-device-properties] [Propiedades de identidad de dispositivos]) del dispositivo que envió el mensaje./// |
| ConnectionAuthMethod | Un método de autenticación establecido por Centro de IoT en los mensajes de dispositivo a nube. Esta propiedad contiene información sobre el método de autenticación usado para autenticar el dispositivo que envía el mensaje. Para más información, consulte [Propiedades contra la suplantación][lnk-antispoofing].|

### <a name="communication-protocols"></a>Protocolos de comunicación

IoT Hub admite los protocolos MQTT, [AMQP][lnk-amqp], AMQP sobre WebSockets y HTTP/1 para las comunicaciones del dispositivo. La tabla siguiente proporciona recomendaciones generales para la elección del protocolo:

| Protocol | Cuándo elegir este protocolo |
| -------- | ------------------------------------ |
| MQTT     | Usar en todos los dispositivos que no requieren el uso de WebSockets. |
| AMQPS    | Usar en puertas de enlace de campo y en la nube para aprovechar las ventajas de la multiplexación de la conexión entre dispositivos. <br/>  Usar cuando se necesita conectar en el puerto 443. |
| HTTPS    | Usar con dispositivos que no admiten otros protocolos. |

Considere los siguientes aspectos a la hora de elegir el protocolo para las comunicaciones del dispositivo:

* **Patrón de nube a dispositivo**. HTTP/1 no cuenta con una forma eficaz de implementar la inserción de servidor. Por lo tanto, cuando se usa HTTP/1, los dispositivos sondean los mensajes de nube a dispositivo en Centro de IoT. Este enfoque es ineficaz tanto para el dispositivo como para IoT Hub. Según las directrices actuales de HTTP/1, cada dispositivo sondeará si hay mensajes cada 25 minutos o más. Por otro lado, AMQP y MQTT admiten la inserción de servidor cuando se reciben mensajes de nube a dispositivo. Permiten inserciones inmediatas de mensajes desde Centro de IoT en el dispositivo. Si le preocupa la latencia de entrega, es mucho mejor usar los protocolos AMQP o MQTT. Para dispositivos conectados en raras ocasiones, HTTP/1 funciona bien.
* **Puertas de enlace de campo**. Cuando se utiliza HTTP/1 y MQTT, no puede conectar varios dispositivos (cada uno con sus propias credenciales por dispositivo) con la misma conexión TLS. Por lo tanto, en los [escenarios de puerta de enlace de campo][lnk-azure-gateway-guidance], estos protocolos no son óptimos, ya que requieren una conexión TLS entre la puerta de enlace de campo y IoT Hub para cada dispositivo conectado a ella.
* **Dispositivos con bajos recursos**. Las bibliotecas de MQTT y HTTP/1 tienen una huella menor que las bibliotecas de AMQP. Por ello, si el dispositivo tiene recursos limitados (por ejemplo, menos de 1 MB de RAM), estos protocolos podrían ser la única opción disponible.
* **Cruce seguro de red**. El estándar MQTT escucha en el puerto 8883, lo cual podría producir problemas en las redes cerradas para los protocolos que no sean HTTP. HTTP y AMQP (sobre WebSockets) están disponibles para usarse en este escenario.
* **Tamaño de carga**. AMQP y MQTT son protocolos binarios que producen cargas más compactas que HTTP/1.

> [AZURE.NOTE] Cuando se usa HTTP/1, cada dispositivo sondeará si hay mensajes de la nube a dispositivo cada 25 minutos o más. Sin embargo, durante el desarrollo, es aceptable sondear con una frecuencia mayor de 25 minutos.

### <a name="port-numbers"></a>Números de puerto

Los dispositivos pueden comunicarse con el Centro de IoT en Azure mediante diversos protocolos. Normalmente, la elección del protocolo se basa en los requisitos específicos de la solución. En la tabla siguiente se indican los puertos de salida que deben estar abiertos para que un dispositivo pueda usar un protocolo concreto:

| Protocol | Puertos |
| -------- | ------- |
| MQTT     | 8883    |
| AMQP     | 5671    |
| AMQP sobre WebSockets | 443    |
| HTTPS    | 443     |
| LWM2M (Administración de dispositivos) | 5684 |

Una vez creado un Centro de IoT en una región de Azure, el centro mantiene la misma dirección IP durante toda su existencia. Sin embargo, para mantener la calidad de servicio, si Microsoft mueve el Centro de IoT a una unidad de escalado diferente, se le asigna una nueva dirección IP.

### <a name="notes-on-mqtt-support"></a>Notas sobre la compatibilidad con MQTT

Centro de IoT implementa el protocolo MQTT v3.1.1 con las siguientes limitaciones y comportamiento específico:

  * **No se admite QoS 2**. Cuando un cliente de dispositivo publica un mensaje con **QoS 2**, el Centro de IoT cierra la conexión de red. Cuando un cliente de dispositivo se suscribe a un tema con **QoS 2**, IoT Hub concede el QoS de nivel 1 (el máximo) en el paquete **SUBACK**.
  * **Los mensajes Retain no se conservan**. Si un cliente de dispositivo publica un mensaje con la marca RETAIN establecida en 1, el Centro de IoT agrega la propiedad de aplicación **x-opt-retain** al mensaje. Esto significa que el Centro de IoT no conserva el mensaje, sino que lo pasa a la aplicación de back-end.

Para más información, consulte [Compatibilidad con MQTT de IoT Hub][lnk-devguide-mqtt].

Como consideración final, debe revisarse la sección [Puerta de enlace de protocolos de IoT de Azure][lnk-azure-protocol-gateway], donde se explica cómo implementar una puerta de enlace de protocolo personalizado de alto rendimiento que interactúe directamente con IoT Hub. La puerta de enlace de protocolos de IoT de Azure le permite personalizar el protocolo del dispositivo para dar cabida a las implementaciones de MQTT de Brownfield u otros protocolos personalizados. De todas formas, este enfoque requiere autohospedar y operar una puerta de enlace de protocolo personalizado.

### <a name="additional-reference-material"></a>Material de referencia adicional

Otros temas de referencia en la Guía para desarrolladores son:

- En [IoT Hub endpoints][lnk-endpoints] (Puntos de conexión de IoT Hub), se describen los diferentes puntos de conexión que expone cada centro de IoT para las operaciones en tiempo de ejecución y de administración.///
- En [Throttling and quotas][lnk-quotas] (Limitación y cuotas), se describen las cuotas que se aplican al servicio de IoT Hub y el comportamiento de limitación que se espera al usar el servicio.
- En [IoT Hub device and service SDKs][lnk-sdks] (SDK de servicio y de dispositivo de IoT Hub) se muestran los diversos SDK de lenguaje que puede usar para desarrollar aplicaciones de dispositivo y de servicio que interactúen con IoT Hub.///
- En [Lenguaje de consulta para dispositivos gemelos, métodos y trabajos][lnk-query], se describe el lenguaje de consulta que se puede usar para recuperar información desde IoT Hub sobre sus dispositivos gemelos, métodos y trabajos.
- En [Compatibilidad con MQTT de IoT Hub][lnk-devguide-mqtt], se proporciona más información acerca de la compatibilidad de IoT Hub con el protocolo MQTT.

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha aprendido a enviar y recibir mensajes con IoT Hub, pueden interesarle los siguientes temas de la Guía del desarrollador:

- [Upload files from a device][lnk-devguide-upload] (Carga de archivos desde un dispositivo)///
- [Manage device identities in IoT Hub][lnk-devguide-identities] (Administración de identidades de dispositivos en IoT Hub)///
- [Control access to IoT Hub][lnk-devguide-security] (Control del acceso a IoT Hub)///
- [Uso de dispositivos gemelos para sincronizar el estado y las configuraciones][lnk-devguide-device-twins]
- [Invocación de un método directo en un dispositivo][lnk-devguide-directmethods]
- [Schedule jobs on multiple devices][lnk-devguide-jobs] (Programación de trabajos en varios dispositivos)

Si desea probar algunos de los conceptos descritos en este artículo, pueden interesarle los siguientes tutoriales de IoT Hub:

- [Introducción a Azure IoT Hub][lnk-getstarted-tutorial]
- [Tutorial: cómo enviar mensajes de la nube a un dispositivo con IoT Hub][lnk-c2d-tutorial]
- [Tutorial: procesamiento de mensajes de dispositivo a la nube de IoT Hub][lnk-d2c-tutorial]


[img-lifecycle]: ./media/iot-hub-devguide-messaging/lifecycle.png
[img-eventhubcompatible]: ./media/iot-hub-devguide-messaging/eventhubcompatible.png

[lnk-resource-provider-apis]: https://msdn.microsoft.com/library/mt548492.aspx
[lnk-azure-gateway-guidance]: iot-hub-devguide-endpoints.md#field-gateways
[lnk-guidance-scale]: iot-hub-scaling.md
[lnk-azure-protocol-gateway]: iot-hub-protocol-gateway.md
[lnk-amqp]: https://www.amqp.org/
[lnk-event-hubs]: http://azure.microsoft.com/documentation/services/event-hubs/
[lnk-event-hubs-consuming-events]: ../event-hubs/event-hubs-programming-guide.md#event-consumers
[lnk-management-portal]: https://portal.azure.com
[lnk-servicebus]: http://azure.microsoft.com/documentation/services/service-bus/
[lnk-eventhub-partitions]: ../event-hubs/event-hubs-overview.md#partitions
[lnk-portal]: iot-hub-create-through-portal.md

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
[lnk-d2c-configuration]: iot-hub-devguide-messaging.md#device-to-cloud-configuration-options
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


[lnk-getstarted-tutorial]: iot-hub-csharp-csharp-getstarted.md
[lnk-c2d-tutorial]: iot-hub-csharp-csharp-c2d.md
[lnk-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md


<!--HONumber=Oct16_HO2-->


