---
title: Compatibilidad de MQTT con el Centro de IoT | Microsoft Docs
description: Descripción de la compatibilidad de MQTT a nivel de Centro de IoT
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: ''

ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/05/2016
ms.author: dobett

---
# <a name="iot-hub-mqtt-support"></a>Compatibilidad con MQTT del Centro de IoT
IoT Hub permite a los dispositivos comunicarse con los puntos de conexión de dispositivos de IoT Hub utilizando el protocolo [MQTT v3.1.1][ lnk-mqtt-org] en el puerto 8883. El Centro de IoT requiere que toda la comunicación de los dispositivos se proteja mediante TLS/SSL.

## <a name="connecting-to-iot-hub"></a>Conexión al Centro de IoT
Un dispositivo puede conectarse a un centro IoT mediante el protocolo MQTT, ya sea mediante las bibliotecas de los [SDK de Microsoft Azure IoT][lnk-device-sdks] o directamente mediante el protocolo MQTT.

## <a name="using-the-device-client-sdks"></a>Uso de los SDK de cliente de dispositivo
Los [SDK de cliente de dispositivo][lnk-device-sdks] compatibles con el protocolo MQTT están disponibles para Java, Node.js, C y C#. El SDK de cliente de dispositivo utiliza la cadena de conexión de Centro de IoT estándar para establecer una conexión a un Centro de IoT. Para utilizar el protocolo MQTT, el parámetro de protocolo de cliente debe establecerse en **MQTT**. De forma predeterminada, los SDK de cliente de dispositivo se conectan a un centro IoT con la marca **CleanSession** establecida en **0** y usan **QoS 1** para el intercambio de mensajes con el centro IoT.

Cuando un dispositivo se conecta a un Centro de IoT, los SDK de cliente de dispositivo proporcionan métodos que permiten al dispositivo enviar y recibir mensajes desde un Centro de IoT.

La tabla siguiente contiene vínculos a ejemplos de código para cada idioma admitido y especifica el parámetro que se debe utilizar para establecer una conexión con el Centro de IoT mediante el protocolo MQTT.

| Idioma | Parámetro de protocolo |
| --- | --- |
| [Node.js][lnk-sample-node] |azure-iot-device-mqtt |
| [Java][lnk-sample-java] |IotHubClientProtocol.MQTT |
| [C][lnk-sample-c] |MQTT_Protocol |
| [C#][lnk-sample-csharp] |TransportType.Mqtt |
| [Python][lnk-sample-python] |IoTHubTransportProvider.MQTT |

### <a name="migrate-a-device-app-from-amqp-to-mqtt"></a>Migrar una aplicación de dispositivo de AMQP a MQTT
Si está utilizando el [SDK de cliente de dispositivo][lnk-device-sdks], cambiar de usar AMQP a MQTT requiere cambiar el parámetro de protocolo en la inicialización del cliente como se indicó anteriormente.

Al hacerlo, asegúrese de comprobar los elementos siguientes:

* AMQP devuelve errores para muchas condiciones, mientras que MQTT finaliza la conexión. Como resultado, la lógica de control de excepciones puede requerir algunos cambios.
* MQTT no admite las operaciones *rechazar* al recibir [mensajes C2D][lnk-messaging]. Si su back-end tiene que recibir una respuesta de la aplicación del dispositivo, considere utilizar [métodos directos][lnk-methods].
* En este momento, MQTT no está disponible a través de WebSockets.

## <a name="using-the-mqtt-protocol-directly"></a>Uso del protocolo MQTT directamente
Si un dispositivo no puede usar los SDK de cliente de dispositivo, tendrá la posibilidad de conectarse a los puntos de conexión públicos del dispositivo mediante el protocolo MQTT. En el paquete **CONNECT** el dispositivo debe usar los siguientes valores:

* Para el campo **ClientId**, use **deviceId**. 
* Para el campo **Nombre de usuario** use `{iothubhostname}/{device_id}`, donde {iothubhostname} es el CName completo de IoT Hub.
  
    Por ejemplo, si el nombre de su centro IoT es **contoso.azure-devices.net** y si el nombre del dispositivo es **MyDevice01**, el campo **Nombre de usuario** completo debe contener `contoso.azure-devices.net/MyDevice01`.
* Para el campo **Contraseña** , use un token SAS. El formato del token de SAS es el mismo que para los protocolos HTTP y AMQP:<br/>`SharedAccessSignature sig={signature-string}&se={expiry}&sr={URL-encoded-resourceURI}`.
  
    Para más información sobre cómo generar tokens de SAS, consulte la sección sobre [uso de tokens de seguridad de IoT Hub][lnk-sas-tokens].
  
    Al realizar la prueba, también puede utilizar la herramienta [Explorador de dispositivos][lnk-device-explorer] para generar rápidamente un token SAS que puede copiar y pegar en su propio código:
  
  1. Vaya a la pestaña **Administración** del Explorador de dispositivos.
  2. Haga clic en **Token de SAS** (parte superior derecha).
  3. En **SASTokenForm**, seleccione el dispositivo en el menú desplegable **DeviceID**. Establezca el valor para **TTL**.
  4. Haga clic en **Generar** para crear el token.
     
     El token de SAS que ha generado tiene esta estructura:   `HostName={your hub name}.azure-devices.net;DeviceId=javadevice;SharedAccessSignature=SharedAccessSignature sr={your hub name}.azure-devices.net%2fdevices%2fMyDevice01&sig=vSgHBMUG.....Ntg%3d&se=1456481802`.
     
     La parte de este token que se debe usar como el campo **Password** para conectarse con MQTT es:   `SharedAccessSignature sr={your hub name}.azure-devices.net%2fdevices%2fyDevice01&sig=vSgHBMUG.....Ntg%3d&se=1456481802g%3d&se=1456481802`.

Para que MQTT conecte y desconecte paquetes, el Centro de IoT emite un evento en el canal **Supervisión de operaciones** .

### <a name="sending-messages-to-iot-hub"></a>Envío de mensajes al Centro de IoT
Después de realizar con éxito una conexión, un dispositivo puede enviar mensajes a un centro IoT mediante `devices/{device_id}/messages/events/` o `devices/{device_id}/messages/events/{property_bag}` como un **Nombre del tema**. El elemento `{property_bag}` permite al dispositivo enviar mensajes con propiedades adicionales en un formato con codificación URL. Por ejemplo:

```
RFC 2396-encoded(<PropertyName1>)=RFC 2396-encoded(<PropertyValue1>)&RFC 2396-encoded(<PropertyName2>)=RFC 2396-encoded(<PropertyValue2>)…
```

> [!NOTE]
> Este elemento `{property_bag}` usa la misma codificación que se utiliza para las cadenas de consulta en el protocolo HTTP.
> 
> 

La aplicación cliente del dispositivo también puede utilizar `devices/{device_id}/messages/events/{property_bag}` como el **nombre del tema Will** para definir los *mensajes Will* que se reenviarán como un mensaje de telemetría.

### <a name="receiving-messages"></a>Recepción de mensajes
Para recibir mensajes de IoT Hub, un dispositivo debe suscribirse usando `devices/{device_id}/messages/devicebound/#”` como un **filtro de tema**. IoT Hub entrega los mensajes con el **Nombre del tema** `devices/{device_id}/messages/devicebound/`, o `devices/{device_id}/messages/devicebound/{property_bag}` si hay alguna propiedad de mensaje. `{property_bag}` contiene pares clave-valor con codificación URL de propiedades del mensaje. Las propiedades de la aplicación y del sistema configuradas por el usuario (como **messageId** o **correlationId**) son las únicas que se incluyen en el paquete de propiedades. Los nombres de propiedad del sistema tienen el prefijo **$**; las propiedades de aplicaciones utilizan el nombre de propiedad original sin prefijo.

## <a name="next-steps"></a>Pasos siguientes
Para más información, consulte la sección ///[Notes on MQTT support][lnk-mqtt-devguide] (Notas sobre la compatibilidad con MQTT) en la Guía del desarrollador de Azure IoT Hub.

Para más información sobre el protocolo MQTT, consulte la [documentación de MQTT][lnk-mqtt-docs].

Para más información acerca de planificación de la implementación del Centro de IoT, consulte:

* [Dispositivos compatibles][lnk-devices]
* [Compatibilidad con protocolos adicionales][lnk-protocols]
* [Comparación con Event Hubs][lnk-compare]
* [Escalado, alta disponibilidad y recuperación ante desastres][lnk-scaling]

Para explorar aún más las funcionalidades de Centro de IoT, consulte:

* [Guía para desarrolladores][lnk-devguide]
* [Simulación de un dispositivo con el SDK de puerta de enlace][lnk-gateway]

[lnk-device-sdks]: https://github.com/Azure/azure-iot-sdks/blob/master/readme.md
[lnk-mqtt-org]: http://mqtt.org/
[lnk-mqtt-docs]: http://mqtt.org/documentation
[lnk-sample-node]: https://github.com/Azure/azure-iot-sdks/blob/develop/node/device/samples/simple_sample_device.js
[lnk-sample-java]: https://github.com/Azure/azure-iot-sdks/blob/develop/java/device/samples/send-receive-sample/src/main/java/samples/com/microsoft/azure/iothub/SendReceive.java
[lnk-sample-c]: https://github.com/Azure/azure-iot-sdks/tree/master/c/iothub_client/samples/iothub_client_sample_mqtt
[lnk-sample-csharp]: https://github.com/Azure/azure-iot-sdks/tree/master/csharp/device/samples
[lnk-sample-python]: https://github.com/Azure/azure-iot-sdks/tree/master/python/device/samples
[lnk-device-explorer]: https://github.com/Azure/azure-iot-sdks/blob/master/tools/DeviceExplorer/readme.md
[lnk-sas-tokens]: iot-hub-devguide-security.md#using-sas-tokens-as-a-device
[lnk-mqtt-devguide]: iot-hub-devguide-messaging.md#notes-on-mqtt-support

[lnk-devices]: iot-hub-tested-configurations.md
[lnk-protocols]: iot-hub-protocol-gateway.md
[lnk-compare]: iot-hub-compare-event-hubs.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md

[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-messaging]: iot-hub-devguide-messaging.md



<!--HONumber=Oct16_HO2-->


