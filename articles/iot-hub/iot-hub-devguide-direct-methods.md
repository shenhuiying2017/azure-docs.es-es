<properties
 pageTitle="Guía del desarrollador: métodos directos | Microsoft Azure"
 description="Guía del desarrollador de Azure IoT Hub: uso de métodos directos para invocar código en los dispositivos"
 services="iot-hub"
 documentationCenter=".net"
 authors="nberdy"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="multiple"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="09/30/2016" 
 ms.author="nberdy"/>


# <a name="invoke-a-direct-method-on-a-device-(preview)"></a>Invocación de un método directo en un dispositivo (versión preliminar)

## <a name="overview"></a>Información general

IoT Hub ofrece la capacidad de invocar métodos en dispositivos desde la nube. Los métodos representan una interacción solicitud-respuesta con un dispositivo similar a una llamada HTTP en la cual se completan correctamente o generan un error de inmediato (tras un tiempo de espera que especifica el usuario) para informar al usuario sobre el estado de la llamada. Esto es útil para escenarios en los que la línea de acción inmediata difiere en función de si el dispositivo respondió, por ejemplo, enviando una reactivación por SMS a un dispositivo si este está sin conexión (enviar un SMS cuesta más que una llamada de método).

Un método se puede considerar como una llamada a procedimiento remoto directamente al dispositivo. Solo se puede llamar desde la nube a aquellos métodos que se hayan implementado en un dispositivo. Si la nube intenta invocar un método en un dispositivo que no lo tiene definido, se produce un error en la llamada de método.

Cada método de dispositivo se dirige a un único dispositivo. Los [trabajos][lnk-devguide-jobs] proporcionan una manera de invocar métodos en varios dispositivos y de poner en cola la invocación de métodos para los dispositivos desconectados.

Cualquier persona con permisos de **conexión de servicio** en IoT Hub pueden invocar un método en un dispositivo.

### <a name="when-to-use"></a>Cuándo se debe usar

Los métodos de dispositivo se parecen a los [mensajes de la nube al dispositivo][lnk-devguide-messages] en que permiten que el back-end en la nube pase información a un dispositivo, pero difieren en aspectos fundamentales. Conceptualmente, los métodos son sincrónicos y no duraderos, mientras que los mensajes de la nube al dispositivo son asincrónicos y pueden durar hasta 48 horas.

Los métodos siguen un patrón de solicitud-respuesta y no son duraderos. La falta de durabilidad ofrece dos ventajas inmediatas al enviar comandos a dispositivos:

- La **información inmediata sobre la ejecución del método** significa que no es necesario que administre la correlación entre solicitud y respuesta.
- El **mayor rendimiento** supone que las operaciones se pueden realizar más rápido porque IoT Hub no proporciona durabilidad. IoT Hub permite más llamadas de método por unidad que mensajes de la nube al dispositivo.

Los mensajes del dispositivo a la nube no son necesariamente comandos para el dispositivo, sino que representan un servicio en la nube que pasa algo de información al dispositivo para que la reciba cuando pueda, y a la que responderá o no. Los mensajes de la nube al dispositivo tienen un tiempo de espera más largo (hasta 48 horas), mientras que los métodos expiran mucho antes.

Use métodos de dispositivo para invocar comandos de inmediato en un dispositivo y trabajos para invocar de forma programada un comando en un dispositivo.

## <a name="method-lifecycle"></a>Ciclo de vida de los métodos

Los métodos se implementan en el dispositivo y pueden requerir de ninguna entrada a varias en la carga de método para crear instancias correctamente. Se invoca un método directo mediante un URI orientado al servicio (`{iot hub}/twins/{device id}/methods/`). Un dispositivo recibe métodos directos a través de un tema MQTT específico del dispositivo (`$iothub/methods/POST/{method name}/`). Es posible que más adelante se admitan métodos en otros protocolos de red del dispositivo.

> [AZURE.NOTE] Cuando se invoca un método directo en un dispositivo, los valores y los nombres de propiedad solo pueden contener caracteres alfanuméricos US-ASCII imprimibles, excepto los del siguiente conjunto: ``{'$', '(', ')', '<', '>', '@', ',', ';', ':', '\', '"', '/', '[', ']', '?', '=', '{', '}', SP, HT}``.

Los métodos son sincrónicos y se completan correctamente o producen un error tras el período de tiempo de espera (valor predeterminado: 30 segundos; valor máximo: 3600 segundos). Los métodos son útiles en escenarios interactivos en los que desee que un dispositivo actúe únicamente si está conectado y recibiendo comandos, como encender una luz desde un teléfono. En estos escenarios, quiere saber de inmediato si la acción se ha completado o no para que el servicio en la nube pueda actuar lo antes posible en función del resultado. El dispositivo puede devolver un cuerpo de mensaje como resultado del método, pero no es necesario que el método lo haga. No hay ninguna garantía respecto al orden o la semántica de simultaneidad en las llamadas de método.

Las llamadas de método de dispositivo son solo HTTP desde el lado de la nube y solo MQTT desde el lado del dispositivo.

## <a name="reference"></a>Referencia

### <a name="service-facing"></a>Orientado al servicio

#### <a name="method-invocation"></a>Invocación de método

Las invocaciones de método directo en un dispositivo son llamadas HTTP compuestas por:

- El *URI* específico del dispositivo (`{iot hub}/twins/{device id}/methods/`)
- El *método* POST
- *Encabezados* que contienen la autorización, el id. de solicitud, el tipo de contenido y la codificación del contenido
- Un *cuerpo* JSON transparente en el formato siguiente:

```
{
    "methodName": "reboot",
    "timeoutInSeconds": 200,
    "payload": {
        "input1": "someInput",
        "input2": "anotherInput"
    }
}
```

  El tiempo de espera se expresa en segundos. Si no se establece el tiempo de espera, el valor predeterminado es 30 segundos.
  
#### <a name="response"></a>Response

El back-end recibe una respuesta que consta de:

- *Código de estado HTTP*, que se usa para errores procedentes de IoT Hub, incluido el error 404 para los dispositivos que no estén conectados
- *Encabezados* que contienen la etiqueta ETag, el id. de solicitud, el tipo de contenido y la codificación del contenido
- Un *cuerpo* JSON en el formato siguiente:

```
{
    "status" : "OK",
    "payload" : {...}
}
```
  
   El dispositivo proporciona tanto `status` como `body`, que se utilizan para responder con el código de estado o la descripción propios del dispositivo.

### <a name="device-facing"></a>Orientada al dispositivo

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

 - La propiedad `status` es el estado de la ejecución del método proporcionado por el dispositivo.
 - La propiedad `$rid` es el identificador de solicitud de la invocación de método recibido de IoT Hub.

El dispositivo establece el cuerpo y puede tener cualquier estado.

### <a name="additional-reference-material"></a>Material de referencia adicional

Otros temas de referencia en la Guía para desarrolladores son:

- En [IoT Hub endpoints][lnk-endpoints] (Puntos de conexión de IoT Hub), se describen los diferentes puntos de conexión que expone cada centro de IoT para las operaciones en tiempo de ejecución y de administración.///
- En [Throttling and quotas][lnk-quotas] (Limitación y cuotas), se describen las cuotas que se aplican al servicio de IoT Hub y el comportamiento de limitación que se espera al usar el servicio.
- En [IoT Hub device and service SDKs][lnk-sdks] (SDK de servicio y de dispositivo de IoT Hub), se muestran los diversos SDK de lenguaje que puede usar para desarrollar aplicaciones de dispositivo y de servicio que interactúen con IoT Hub.///
- En [Lenguaje de consulta para dispositivos gemelos, métodos y trabajos][lnk-query], se describe el lenguaje de consulta que se puede usar para recuperar información desde IoT Hub sobre sus dispositivos gemelos, métodos y trabajos.
- En [Compatibilidad con MQTT de IoT Hub][lnk-devguide-mqtt], se proporciona más información acerca de la compatibilidad de IoT Hub con el protocolo MQTT.

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha aprendido a usar métodos directos, puede interesarle el siguiente tema de la Guía del desarrollador:

- [Schedule jobs on multiple devices][lnk-devguide-jobs] (Programación de trabajos en varios dispositivos)

Si desea probar algunos de los conceptos descritos en este artículo, puede interesarle el siguiente tutorial de IoT Hub:

- [Uso de métodos de la nube al dispositivo][lnk-methods-tutorial]

<!-- links and images -->

[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md

[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-methods-tutorial]: iot-hub-c2d-methods.md
[lnk-devguide-messages]: iot-hub-devguide-messaging.md



<!--HONumber=Oct16_HO2-->


