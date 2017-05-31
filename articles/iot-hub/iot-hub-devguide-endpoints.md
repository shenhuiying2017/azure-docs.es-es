---
title: "Información de los puntos de conexión de Azure IoT Hub | Microsoft Docs"
description: "Guía para desarrolladores: información de referencia sobre los puntos de conexión orientados a dispositivos y servicios IoT Hub."
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 57ba52ae-19c6-43e4-bc6c-d8a5c2476e95
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/22/2017
ms.author: dobett
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: b0b2e8f375fa0b80e4f933515e1e8a689478483e
ms.contentlocale: es-es
ms.lasthandoff: 05/10/2017


---
# <a name="reference---iot-hub-endpoints"></a>Referencia: Puntos de conexión de IoT Hub
## <a name="list-of-built-in-iot-hub-endpoints"></a>Lista de puntos de conexión de IoT Hub integrados
Centro de IoT de Azure es un servicio multiempresa que muestra su funcionalidad a diversos actores. En el siguiente diagrama se muestran los diferentes puntos de conexión expuestos por Centro de IoT.

![Puntos de conexión del Centro de IoT][img-endpoints]

Esta es una descripción de los puntos de conexión:

* **Proveedor de recursos**. El proveedor de recursos de IoT Hub muestra una interfaz de [Azure Resource Manager][lnk-arm] que permite a los propietarios de suscripciones de Azure crear y eliminar centros de IoT, así como actualizar las propiedades de IoT Hub. Las propiedades de IoT Hub controlan las [directivas de seguridad a nivel de centro][lnk-accesscontrol], a diferencia del control de acceso de nivel de dispositivo y las opciones funcionales para la mensajería de nube a dispositivo y de dispositivo a nube. El proveedor de recursos también permite [exportar identidades de dispositivo][lnk-importexport].
* **Administración de identidades de dispositivo**. Cada Centro de IoT muestra un conjunto de puntos de conexión HTTP REST para administrar identidades de dispositivo (crear, recuperar, actualizar y eliminar). Las [identidades del dispositivo][lnk-device-identities] se usan para autenticación de dispositivos y control de acceso.
* **Administración de dispositivo gemelo**. Cada instancia de IoT Hub muestra un conjunto de punto de conexión de HTTP REST orientado a servicios para consultar y actualizar [dispositivos gemelos][lnk-twins] (actualización de etiquetas y propiedades).
* **Administración de trabajos**. Cada instancia de IoT Hub muestra un conjunto de punto de conexión de HTTP REST orientado a servicios para consultar y administrar [trabajos][lnk-jobs].
* **Puntos de conexión de dispositivo**. Para cada dispositivo aprovisionado en el registro de identidad del dispositivos, IoT Hub muestra un conjunto de puntos de conexión que el dispositivo puede usar para enviar y recibir mensajes:
  
  * *Envío de mensajes de dispositivo a nube*. Use este punto de conexión para [enviar mensajes de dispositivo a la nube][lnk-d2c].
  * *Recepción de mensajes de nube a dispositivo*. El dispositivo usa ese punto de conexión para recibir [mensajes de nube a dispositivos][lnk-c2d] dirigidos.
  * *Iniciar cargas de archivos*. Un dispositivo usa este punto de conexión para recibir un URI de SAS de Azure Storage de IoT Hub para [cargar un archivo][lnk-upload].
  * *Recuperación y actualización de las propiedades del dispositivo gemelo*. Un dispositivo usa este punto de conexión para tener acceso a las propiedades del [dispositivo gemelo][lnk-twins].
  * *Recepción de solicitudes de métodos directos*. Un dispositivo usa este punto de conexión para escuchar solicitudes de [métodos directos][lnk-methods].
    
    Estos puntos de conexión se exponen mediante los protocolos[ MQTT v3.1.1][lnk-mqtt], HTTP 1.1 y [AMQP 1.0][lnk-amqp]. Tenga en cuenta que AMQP también está disponible sobre [WebSockets][lnk-websockets] en el puerto 443.
    
    Los puntos de conexión de métodos y dispositivos gemelos están disponibles solo mediante [MQTT v3.1.1][lnk-mqtt].
* **Puntos de conexión de servicio**. Cada centro de IoT Hub muestra un conjunto de puntos de conexión que el back-end de solución puede usar para comunicarse con los dispositivos. Estos puntos de conexión se exponen solo actualmente mediante el protocolo [AMQP][lnk-amqp], excepto para el punto de conexión de la invocación de método que se expone a través de HTTP 1.1.
  
  * *Recepción de mensajes de dispositivo a nube*. Este punto de conexión es compatible con [Azure Event Hubs][lnk-event-hubs]. Un servicio back-end puede usarse para leer los [mensajes de dispositivo a nube][lnk-d2c] enviados por los dispositivos. Además de este punto de conexión integrado, puede crear puntos de conexión personalizados en el centro de IoT.
  * *Envío de mensajes de nube a dispositivo y recepción de confirmaciones de entrega*. Estos puntos de conexión permiten al back-end de aplicaciones enviar mensajes confiables [de nube a dispositivo][lnk-c2d] y recibir las confirmaciones de entrega o expiración correspondientes.
  * *Recepción de notificaciones de archivos*. Este punto de conexión de mensajería le permite recibir notificaciones del momento en que los dispositivos cargan correctamente un archivo. 
  * *Invocación del método director*. Este punto de conexión permite que un servicio back-end invoque un [método directo][lnk-methods] en un dispositivo.
  * *Recepción de eventos de supervisión de operaciones*. Este punto de conexión le permite recibir eventos de supervisión de operaciones si su centro de IoT se ha configurado para emitirlos. Consulte [Supervisión de operaciones de IoT Hub][lnk-operations-mon] para más información.

En el artículo [SDK IoT de Azure][lnk-sdks] se describen las distintas formas con las que se puede acceder a estos puntos de conexión.

Finalmente, es importante tener en cuenta que todos los puntos de conexión de IoT Hub usan el protocolo [TLS][lnk-tls] y ningún punto de conexión se expone en canales sin cifrar o no seguros.

## <a name="custom-endpoints"></a>Puntos de conexión personalizados
Puede vincular los servicios de Azure existentes en su centro de IoT para usarlos como puntos de conexión para el enrutamiento de mensajes. Estos puntos de conexión hacen de puntos de conexión de servicio y se usan como receptores para las rutas de los mensajes. Los dispositivos no pueden escribir directamente en los puntos de conexión adicionales. Para obtener más información sobre las rutas de mensajes, vea la entrada de la guía para desarrolladores sobre [enviar y recibir mensajes con IoT Hub][lnk-devguide-messaging].

IoT Hub admite actualmente los siguientes servicios de Azure como puntos de conexión adicionales:

* Event Hubs
* Colas del Bus de servicio
* Temas de Bus de servicio

IoT Hub necesita acceso de escritura a estos puntos de conexión de servicio para que el enrutamiento de mensajes funcione. Si configura los puntos de conexión a través de Azure Portal, se agregan los permisos necesarios automáticamente. Asegúrese de configurar los servicios para admitir el rendimiento esperado. Es posible que deba supervisar los puntos de conexión adicionales al configurar la solución de IoT por primera vez y, a continuación, realizar los ajustes necesarios para la carga real.

Si un mensaje coincide con varias rutas que señalan al mismo punto de conexión, IoT Hub entrega el mensaje a ese punto de conexión solo una vez. Por lo tanto, no es necesario configurar la desduplicación en la cola o tema de Service Bus. En las colas con particiones, la afinidad de partición garantiza el orden de los mensajes. No se admiten las colas con sesiones habilitadas como puntos de conexión. Las colas y temas con particiones que tengan habilitada la desduplicación tampoco se admiten.

Para conocer los límites del número de puntos de conexión que se pueden agregar, consulte [Cuotas y limitación][lnk-devguide-quotas].

## <a name="field-gateways"></a>Puertas de enlace de campo
En una solución de IoT, un *puerta de enlace de campo* se encuentra entre los dispositivos y los puntos de conexión de IoT Hub. Suele encontrarse cerca de los dispositivos. Los dispositivos se comunican directamente con la puerta de enlace de campo mediante un protocolo compatible con los dispositivos. La puerta de enlace de campo se conecta al punto de conexión de IoT Hub con un protocolo que es compatible con IoT Hub. Una puerta de enlace de campo puede ser hardware muy especializado o un equipo de bajo consumo que ejecuta software que lleva a cabo el escenario de extremo a extremo al que va destinada la puerta de enlace.

Puede usar [Azure IoT Edge][lnk-gateway-sdk] para implementar una puerta de enlace de campo. IoT Edge ofrece funcionalidad específica, como la posibilidad de multiplexar la comunicación desde varios dispositivos en la misma conexión de IoT Hub.

## <a name="next-steps"></a>Pasos siguientes
Otros temas de referencia en la Guía del desarrollador de IoT Hub son:

* [Lenguaje de consulta de IoT Hub para dispositivos gemelos y trabajos][lnk-devguide-query]
* [Cuotas y limitación][lnk-devguide-quotas]
* [Compatibilidad con MQTT de IoT Hub][lnk-devguide-mqtt]

[lnk-gateway-sdk]: https://github.com/Azure/iot-edge

[img-endpoints]: ./media/iot-hub-devguide-endpoints/endpoints.png
[lnk-amqp]: https://www.amqp.org/
[lnk-mqtt]: http://mqtt.org/
[lnk-websockets]: https://tools.ietf.org/html/rfc6455
[lnk-arm]: ../azure-resource-manager/resource-group-overview.md
[lnk-event-hubs]: http://azure.microsoft.com/documentation/services/event-hubs/

[lnk-tls]: https://tools.ietf.org/html/rfc5246


[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-accesscontrol]: iot-hub-devguide-security.md#access-control-and-permissions
[lnk-importexport]: iot-hub-devguide-identity-registry.md#import-and-export-device-identities
[lnk-d2c]: iot-hub-devguide-messaging.md#device-to-cloud-messages
[lnk-device-identities]: iot-hub-devguide-identity-registry.md
[lnk-upload]: iot-hub-devguide-file-upload.md
[lnk-c2d]: iot-hub-devguide-messaging.md#cloud-to-device-messages
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-jobs]: iot-hub-devguide-jobs.md

[lnk-devguide-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-devguide-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-devguide-messaging]: iot-hub-devguide-messaging.md
[lnk-operations-mon]: iot-hub-operations-monitoring.md

