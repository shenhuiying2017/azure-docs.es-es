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
ms.date: 09/19/2017
ms.author: dobett
ms.openlocfilehash: dc983549aea53ed29859205102d6308a3367bec7
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/23/2018
---
# <a name="reference---iot-hub-endpoints"></a>Referencia: Puntos de conexión de IoT Hub

## <a name="iot-hub-names"></a>Nombres de IoT Hub

Puede encontrar el nombre de la instancia de IoT Hub que hospeda los puntos de conexión en el portal en la hoja **Introducción**. De forma predeterminada, el nombre DNS de una instancia de IoT Hub es similar a lo siguiente: `{your iot hub name}.azure-devices.net`.

Puede utilizar Azure DNS para crear un nombre DNS personalizado para el centro de IoT. Para más información, consulte [Usar Azure DNS para proporcionar la configuración de un dominio personalizado para un servicio de Azure](../dns/dns-custom-domain.md).

## <a name="list-of-built-in-iot-hub-endpoints"></a>Lista de puntos de conexión de IoT Hub integrados

Azure IoT Hub es un servicio multiempresa que muestra su funcionalidad a diversos actores. En el siguiente diagrama se muestran los diferentes puntos de conexión expuestos por IoT Hub.

![Puntos de conexión de IoT Hub][img-endpoints]

En la lista siguiente se describen los puntos de conexión:

* **Proveedor de recursos**. El proveedor de recursos de IoT Hub expone una interfaz de [Azure Resource Manager][lnk-arm]. Esta interfaz permite que los propietarios de suscripciones de Azure creen y eliminen instancias de IoT Hub para actualizar sus propiedades. Las propiedades de IoT Hub controlan las [directivas de seguridad a nivel de centro][lnk-accesscontrol], a diferencia del control de acceso de nivel de dispositivo y las opciones funcionales para la mensajería de nube a dispositivo y de dispositivo a nube. El proveedor de recursos también permite [exportar identidades de dispositivo][lnk-importexport].
* **Administración de identidades de dispositivo**. Cada instancia de IoT Hub muestra un conjunto de puntos de conexión HTTPS REST para administrar las identidades de dispositivo (crear, recuperar, actualizar y eliminar). Las [identidades del dispositivo][lnk-device-identities] se usan para autenticación de dispositivos y control de acceso.
* **Administración de dispositivo gemelo**. Cada instancia de IoT Hub muestra un conjunto de punto de conexión de HTTPS REST orientado a servicios para consultar y actualizar [dispositivos gemelos][lnk-twins] (actualización de etiquetas y propiedades).
* **Administración de trabajos**. Cada instancia de IoT Hub muestra un conjunto de punto de conexión de HTTPS REST orientado a servicios para consultar y administrar [trabajos][lnk-jobs].
* **Puntos de conexión de dispositivo**. Para cada dispositivo del registro de identidad, IoT Hub muestra un conjunto de puntos de conexión:

  * *Envío de mensajes de dispositivo a nube*. Un dispositivo usa este punto de conexión para [enviar mensajes de dispositivo a la nube][lnk-d2c].
  * *Recepción de mensajes de nube a dispositivo*. El dispositivo usa ese punto de conexión para recibir [mensajes de nube a dispositivos][lnk-c2d] dirigidos.
  * *Iniciar cargas de archivos*. Un dispositivo usa este punto de conexión para recibir un URI de SAS de Azure Storage de IoT Hub para [cargar un archivo][lnk-upload].
  * *Recuperación y actualización de las propiedades del dispositivo gemelo*. Un dispositivo usa este punto de conexión para tener acceso a las propiedades del [dispositivo gemelo][lnk-twins].
  * *Recepción de solicitudes de métodos directos*. Un dispositivo usa este punto de conexión para escuchar solicitudes de [métodos directos][lnk-methods].

    Estos puntos de conexión se exponen mediante los protocolos[ MQTT v3.1.1][lnk-mqtt], HTTPS 1.1 y [AMQP 1.0][lnk-amqp]. AMQP también está disponible sobre [WebSockets][lnk-websockets] en el puerto 443.

* **Puntos de conexión de servicio**. Cada instancia de IoT Hub muestra un conjunto de puntos de conexión que el back-end de solución para comunicarse con los dispositivos. Con una excepción, estos puntos de conexión solo se muestran con el protocolo [AMQP][lnk-amqp]. El punto de conexión de invocación de método se muestra en el protocolo HTTPS.
  
  * *Recepción de mensajes de dispositivo a nube*. Este punto de conexión es compatible con [Azure Event Hubs][lnk-event-hubs]. Un servicio back-end puede usarse para leer los [mensajes de dispositivo a nube][lnk-d2c] enviados por los dispositivos. Además de este punto de conexión integrado, puede crear puntos de conexión personalizados en el centro de IoT.
  * *Envío de mensajes de nube a dispositivo y recepción de confirmaciones de entrega*. Estos puntos de conexión permiten al back-end de aplicaciones enviar mensajes confiables [de nube a dispositivo][lnk-c2d] y recibir las confirmaciones de entrega o expiración correspondientes.
  * *Recepción de notificaciones de archivos*. Este punto de conexión de mensajería le permite recibir notificaciones del momento en que los dispositivos cargan correctamente un archivo. 
  * *Invocación del método director*. Este punto de conexión permite que un servicio back-end invoque un [método directo][lnk-methods] en un dispositivo.
  * *Recepción de eventos de supervisión de operaciones*. Este punto de conexión le permite recibir eventos de supervisión de operaciones si su centro de IoT se ha configurado para emitirlos. Para más información, consulte [Supervisión de operaciones de IoT Hub][lnk-operations-mon].

En el artículo [SDK IoT de Azure][lnk-sdks] se describen las distintas formas con las que se puede acceder a estos puntos de conexión.

Todos los puntos de conexión de IoT Hub usan el protocolo [TLS][lnk-tls] y ningún punto de conexión se expone en canales sin cifrar o no seguros.

## <a name="custom-endpoints"></a>Puntos de conexión personalizados

Puede vincular los servicios de Azure existentes en su centro de IoT para usarlos como puntos de conexión para el enrutamiento de mensajes. Estos puntos de conexión hacen de puntos de conexión de servicio y se usan como receptores para las rutas de los mensajes. Los dispositivos no pueden escribir directamente en los puntos de conexión adicionales. Para obtener más información sobre las rutas de mensajes, vea la entrada de la guía para desarrolladores sobre [enviar y recibir mensajes con IoT Hub][lnk-devguide-messaging].

IoT Hub admite actualmente los siguientes servicios de Azure como puntos de conexión adicionales:

* Contenedores de Azure Storage
* Event Hubs
* Colas de Service Bus
* Temas de Service Bus

IoT Hub necesita acceso de escritura a estos puntos de conexión de servicio para que el enrutamiento de mensajes funcione. Si configura los puntos de conexión a través de Azure Portal, se agregan los permisos necesarios automáticamente. Asegúrese de configurar los servicios para admitir el rendimiento esperado. Al configurar la solución de IoT por primera vez, es posible que deba supervisar los puntos de conexión adicionales y realizar los ajustes necesarios para la carga real.

Si un mensaje coincide con varias rutas que señalan al mismo punto de conexión, IoT Hub entrega el mensaje a ese punto de conexión solo una vez. Por lo tanto, no es necesario configurar la desduplicación en la cola o tema de Service Bus. En las colas con particiones, la afinidad de partición garantiza el orden de los mensajes.

Para conocer los límites del número de puntos de conexión que se pueden agregar, consulte [Cuotas y limitación][lnk-devguide-quotas].

### <a name="when-using-azure-storage-containers"></a>Cuándo usar los contenedores de Azure Storage

IoT Hub solo admite la escritura de datos en contenedores de Azure Storage como blobs con el formato [Apache Avro](http://avro.apache.org/). IoT Hub agrupa los mensajes por lotes y escribe los datos en un blob cuando llega a cierto tamaño o después de cierta cantidad de tiempo, lo que ocurra en primer lugar. IoT Hub no escribirá en un blob vacío si no hay datos que escribir.

IoT Hub asume como valor predeterminado la convención de nomenclatura de archivos siguiente:

```
{iothub}/{partition}/{YYYY}/{MM}/{DD}/{HH}/{mm}
```

Puede usar la convención de nomenclatura de archivos de su preferencia, aunque debe usar todos los tokens de la lista.

### <a name="when-using-service-bus-queues-and-topics"></a>Cuándo usar colas y temas de Service Bus

Las colas y los temas de Service Bus usados como puntos de conexión de IoT Hub no deben tener habilitadas las opciones **Sesiones** o **Detección de duplicados**. Si cualquiera de estas opciones está habilitada, el punto de conexión aparece como **Inaccesible** en Azure Portal.

## <a name="field-gateways"></a>Puertas de enlace de campo

En una solución de IoT, un *puerta de enlace de campo* se encuentra entre los dispositivos y los puntos de conexión de IoT Hub. Suele encontrarse cerca de los dispositivos. Los dispositivos se comunican directamente con la puerta de enlace de campo mediante un protocolo compatible con los dispositivos. La puerta de enlace de campo se conecta al punto de conexión de IoT Hub con un protocolo que es compatible con IoT Hub. Una puerta de enlace de campo puede ser un dispositivo de hardware dedicado o un equipo de bajo consumo de energía que ejecuta el software de puerta de enlace personalizado.

Puede usar [Azure IoT Edge][lnk-iot-edge] para implementar una puerta de enlace de campo. IoT Edge ofrece la funcionalidad de multiplexar la comunicación desde varios dispositivos en la misma conexión de IoT Hub.

## <a name="next-steps"></a>pasos siguientes

Otros temas de referencia en la Guía del desarrollador de IoT Hub son:

* [Referencia: Lenguaje de consulta de IoT Hub para dispositivos gemelos y trabajos][lnk-devguide-query]
* [Cuotas y limitación][lnk-devguide-quotas]
* [Compatibilidad con MQTT de IoT Hub][lnk-devguide-mqtt]

[lnk-iot-edge]: https://github.com/Azure/iot-edge

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
[lnk-d2c]: iot-hub-devguide-messages-d2c.md
[lnk-device-identities]: iot-hub-devguide-identity-registry.md
[lnk-upload]: iot-hub-devguide-file-upload.md
[lnk-c2d]: iot-hub-devguide-messages-c2d.md
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-jobs]: iot-hub-devguide-jobs.md

[lnk-devguide-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-devguide-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-devguide-messaging]: iot-hub-devguide-messaging.md
[lnk-operations-mon]: iot-hub-operations-monitoring.md
