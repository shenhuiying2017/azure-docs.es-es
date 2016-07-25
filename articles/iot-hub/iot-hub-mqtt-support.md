<properties
 pageTitle="Compatibilidad de MQTT con el Centro de IoT | Microsoft Azure"
 description="Descripción de la compatibilidad de MQTT a nivel de Centro de IoT"
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
 ms.date="04/29/2016"
 ms.author="dobett"/>

# Compatibilidad con MQTT del Centro de IoT

El Centro de IoT permite a los dispositivos comunicarse con los puntos de conexión de dispositivos del Centro de IoT utilizando el protocolo [MQTT v3.1.1][lnk-mqtt-org] en el puerto 8883. El Centro de IoT requiere que toda la comunicación de los dispositivos se proteja mediante TLS/SSL.

## Conexión al Centro de IoT

Un dispositivo puede conectarse a un Centro de IoT mediante el protocolo MQTT, ya sea mediante las bibliotecas en los [SDK de IoT de Microsoft Azure][lnk-device-sdks] o mediante el protocolo MQTT directamente.

## Uso de los SDK de cliente de dispositivo

Los [SDK de cliente de dispositivo][lnk-device-sdks] que admiten el protocolo MQTT están disponibles para Java, Node.js, C y C#. El SDK de cliente de dispositivo utiliza la cadena de conexión de Centro de IoT estándar para establecer una conexión a un Centro de IoT. Para utilizar el protocolo MQTT, el parámetro de protocolo de cliente debe establecerse en **MQTT**. De forma predeterminada, los SDK de cliente de dispositivo se conectan a un Centro de IoT con la marca **CleanSession** establecida en **0** y usan **QoS 1** para el intercambio de mensajes con el Centro de IoT.

Cuando un dispositivo se conecta a un Centro de IoT, los SDK de cliente de dispositivo proporcionan métodos que permiten al dispositivo enviar y recibir mensajes desde un Centro de IoT.

La tabla siguiente contiene vínculos a ejemplos de código para cada idioma admitido y especifica el parámetro que se debe utilizar para establecer una conexión con el Centro de IoT mediante el protocolo MQTT.

| Idioma | Parámetro de protocolo |
| -------------------------- | ------------------------- |
| [Node.js][lnk-sample-node] | azure-iot-device-mqtt |
| [Java][lnk-sample-java] | IotHubClientProtocol.MQTT |
| [C][lnk-sample-c] | MQTT\_Protocol |
| [C#][lnk-sample-csharp] | TransportType.Mqtt |

## Uso del protocolo MQTT directamente

Si un dispositivo no puede usar los SDK de cliente de dispositivo, tendrá la posibilidad de conectarse a los puntos de conexión públicos del dispositivo mediante el protocolo MQTT. En el paquete **CONNECT** el dispositivo debe usar los siguientes valores:

- Para el campo **ClientId**, use **deviceId**.
- Para el campo **Nombre de usuario** use `{iothubhostname}/{device_id}`, donde {iothubhostname} es el CName completo del Centro de IoT.

    Por ejemplo, si el nombre de su Centro de IoT es **contoso.azure devices.net** y si el nombre del dispositivo es **MyDevice01**, el campo **Nombre de usuario** completo debe contener `contoso.azure-devices.net/MyDevice01`.

- Para el campo **Contraseña**, use un token SAS. El formato del token de SAS es el mismo que para los protocolos HTTP y AMQP:<br/>`SharedAccessSignature sig={signature-string}&se={expiry}&sr={URL-encoded-resourceURI}`.

    Para más información sobre cómo generar tokens de SAS, consulte [Uso de tokens de seguridad del Centro de IoT][lnk-sas-tokens].
    
    Al realizar la prueba, también puede utilizar la herramienta [Explorador de dispositivos][lnk-device-explorer] para generar rápidamente un token SAS que puede copiar y pegar en su propio código:
    
    1. Vaya a la pestaña **Administración** en el Explorador de dispositivos.
    2. Haga clic en **Token de SAS** (parte superior derecha).
    3. En **SASTokenForm**, seleccione el dispositivo en el menú desplegable **DeviceID**. Establezca el valor para **TTL**.
    4. Haga clic en **Generar** para crear el token.
    
    El token de SAS que ha generado es similar al siguiente: `HostName={your hub name}.azure-devices.net;DeviceId=javadevice;SharedAccessSignature=SharedAccessSignature sr={your hub name}.azure-devices.net%2fdevices%2fMyDevice01&sig=vSgHBMUG.....Ntg%3d&se=1456481802`.

    La parte de esta opción que se debe usar como en el campo **Contraseña** para conectarse con MQTT es: `SharedAccessSignature sr={your hub name}.azure-devices.net%2fdevices%2fyDevice01&sig=vSgHBMUG.....Ntg%3d&se=1456481802g%3d&se=1456481802`.

Para que MQTT conecte y desconecte paquetes, el Centro de IoT emite un evento en el canal **Supervisión de operaciones**.

### Envío de mensajes al Centro de IoT

Una vez conectado correctamente, un dispositivo puede enviar mensajes al Centro de IoT mediante `devices/{device_id}/messages/events/` o `devices/{device_id}/messages/events/{property_bag}` como un **nombre de tema**. El elemento `{property_bag}` permite al dispositivo enviar mensajes con propiedades adicionales en un formato con codificación url. Por ejemplo:

```
RFC 2396-encoded(<PropertyName1>)=RFC 2396-encoded(<PropertyValue1>)&RFC 2396-encoded(<PropertyName2>)=RFC 2396-encoded(<PropertyValue2>)…
```

> [AZURE.NOTE] Se trata de la misma codificación que se utiliza para las cadenas de consulta en el protocolo HTTP.

La aplicación cliente del dispositivo también puede utilizar `devices/{device_id}/messages/events/{property_bag}` como el **nombre del tema Will** para definir los *mensajes Will* que se reenviarán como un mensaje de telemetría.

### Recepción de mensajes

Para recibir mensajes de un Centro de IoT, un dispositivo debe suscribirse mediante `devices/{device_id}/messages/devicebound/#”` como un **filtro de tema**. El Centro de IoT entrega los mensajes con el **nombre de tema** `devices/{device_id}/messages/devicebound/`, o `devices/{device_id}/messages/devicebound/{property_bag}` si hay propiedades de mensaje. `{property_bag}` contiene pares clave-valor con codificación URL de propiedades del mensaje. Las propiedades de la aplicación y del sistema configuradas por el usuario (como **messageId** o **correlationId**) son las únicas que se incluyen en el paquete de propiedades. Los nombres de propiedad del sistema tienen el prefijo **$**, las propiedades de la aplicación utilizan el nombre de propiedad original sin prefijo.

## Pasos siguientes

Para más información sobre la compatibilidad con MQTT con los SDK de dispositivo IoT, consulte la sección [Notas sobre la compatibilidad con MQTT][lnk-mqtt-devguide] en la Guía del desarrollador del Centro de IoT de Azure.

Para más información sobre el protocolo MQTT, consulte la [documentación de MQTT][lnk-mqtt-docs].

Para más información acerca de planificación de la implementación del Centro de IoT, consulte:

- [Dispositivos compatibles][lnk-devices]
- [Compatibilidad con protocolos adicionales para Centro de IoT][lnk-protocols]
- [Comparación del Centro de IoT y los Centros de eventos][lnk-compare]
- [Escalado del Centro de IoT][lnk-scaling]

Para explorar aún más las funcionalidades de Centro de IoT, consulte:

- [Guía del desarrollador][lnk-devguide]
- [Exploración de la administración de dispositivos desde Centro de IoT de Azure con la IU de ejemplo][lnk-dmui]
- [SDK de puerta de enlace de IoT (beta): envío de mensajes del dispositivo a la nube con un dispositivo simulado usando Linux][lnk-gateway]
- [Administración de Centros de IoT a través del portal de Azure][lnk-portal]

[lnk-device-sdks]: https://github.com/Azure/azure-iot-sdks/blob/master/readme.md
[lnk-mqtt-org]: http://mqtt.org/
[lnk-mqtt-docs]: http://mqtt.org/documentation
[lnk-sample-node]: https://github.com/Azure/azure-iot-sdks/blob/develop/node/device/samples/simple_sample_device.js
[lnk-sample-java]: https://github.com/Azure/azure-iot-sdks/blob/develop/java/device/samples/send-receive-sample/src/main/java/samples/com/microsoft/azure/iothub/SendReceive.java
[lnk-sample-c]: https://github.com/Azure/azure-iot-sdks/tree/master/c/iothub_client/samples/iothub_client_sample_mqtt
[lnk-sample-csharp]: https://github.com/Azure/azure-iot-sdks/tree/master/csharp/device/samples
[lnk-device-explorer]: https://github.com/Azure/azure-iot-sdks/blob/master/tools/DeviceExplorer/readme.md
[lnk-sas-tokens]: iot-hub-sas-tokens.md#using-sas-tokens-as-a-device
[lnk-mqtt-devguide]: iot-hub-devguide.md#mqtt-support

[lnk-devices]: iot-hub-tested-configurations.md
[lnk-protocols]: iot-hub-protocol-gateway.md
[lnk-compare]: iot-hub-compare-event-hubs.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-devguide]: iot-hub-devguide.md
[lnk-dmui]: iot-hub-device-management-ui-sample.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
[lnk-portal]: iot-hub-manage-through-portal.md

<!---HONumber=AcomDC_0713_2016-->