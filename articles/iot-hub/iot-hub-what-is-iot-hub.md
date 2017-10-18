---
title: "Información general del Azure IoT Hub | Microsoft Docs"
description: "Información general del servicio IoT Hub de Azure: qué es IoT Hub, la conectividad de dispositivos, los patrones de comunicación de Internet de las cosas, puertas de enlace y el patrón de comunicación asistida por servicios"
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 24376318-5344-4a81-a1e6-0003ed587d53
ms.service: iot-hub
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/14/2017
ms.author: dobett
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b5f44d2ae42ffc6f75887a64c9ef988fe6d8fd69
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="overview-of-the-azure-iot-hub-service"></a>Introducción al servicio Azure IoT Hub

Bienvenido al Centro de IoT de Azure. En este artículo se ofrece información general sobre Azure IoT Hub y se describe por qué debería usar este servicio para implementar una solución de Internet de las cosas (IoT). El Centro de IoT de Azure es un servicio totalmente administrado que permite la comunicación bidireccional fiable y segura entre millones de dispositivos IoT y un back-end de soluciones. Centro de IoT de Azure:

* Proporciona varias opciones de comunicación de dispositivo a nube y de nube a dispositivo. Dichas opciones incluyen la mensajería unidireccional, la transferencia de archivos y los métodos de solicitud y respuesta.
* Proporciona enrutamiento de mensajes declarativos integrado a otros servicios de Azure.
* Proporciona almacenamiento consultable para metadatos del dispositivo e información de estado sincronizada.
* Habilita las comunicaciones seguras y el control de acceso con claves de seguridad por dispositivo o certificados X.509.
* Proporciona una supervisión exhaustiva para la conectividad de dispositivos y los eventos de administración de identidad de dispositivos.
* Incluye bibliotecas de dispositivos para las plataformas y los lenguajes más populares.

En el artículo [Comparación de IoT Hub y Event Hubs][lnk-compare] se describen las diferencias clave entre estos dos servicios y se resaltan las ventajas del uso de IoT Hub en las soluciones de IoT.

Para más información sobre cómo Azure y IoT Hub ayudan a proteger la solución de IoT, consulte [Seguridad de Internet de las cosas desde el principio][lnk-security-ground-up].

![Centro de IoT de Azure como solución de puerta de enlace de nube en Internet de las cosas][img-architecture]

> [!NOTE]
> Para ver un análisis detallado de la arquitectura de IoT, consulte el PDF [Microsoft Azure IoT Reference Architecture][lnk-refarch] (Arquitectura de referencia de IoT de Microsoft Azure).

## <a name="iot-device-connectivity-challenges"></a>Problemas de conectividad de dispositivos IoT

El Centro de IoT y las bibliotecas de dispositivo le ayudan a superar los desafíos derivados de la conexión confiable y segura de dispositivos al back-end de soluciones. Dispositivos IoT:

* A menudo son sistemas insertados sin operador humano.
* Pueden encontrarse en ubicaciones remotas, donde el acceso físico resulta costoso.
* Es posible que solo sean accesibles a través del back-end de soluciones.
* Es posible que tengan limitaciones de recursos de procesamiento y alimentación.
* Es posible que tengan conectividad de red intermitente, lenta o costosa.
* Es posible que necesiten usar protocolos de aplicación propios, personalizados o específicos de determinados sectores.
* Pueden crearse mediante un amplio conjunto de plataformas populares de hardware y software.

Además de los requisitos anteriores, toda solución de IoT debe ser capaz de ofrecer escalabilidad, seguridad y fiabilidad. Esto da lugar a un conjunto de requisitos de conectividad cuya implementación resulta compleja y lenta si se realiza con tecnologías tradicionales como, por ejemplo, los contenedores web y los agentes de mensajería.

## <a name="why-use-azure-iot-hub"></a>¿Por qué usar Azure IoT Hub?

Azure IoT Hub ofrece un gran conjunto de opciones de comunicación de [dispositivo a nube][lnk-d2c-guidance] y de [nube a dispositivo][lnk-c2d-guidance]. Además, Azure IoT Hub afronta los problemas que pueden aparecer al conectar de forma confiable y segura a los dispositivos de las siguientes formas:

* **Dispositivos gemelos**. Con los [dispositivos gemelos][lnk-twins] puede almacenar, sincronizar y consultar metadatos e información de estado de los dispositivos. Los dispositivos gemelos son documentos JSON que almacenan información acerca del estado del dispositivo como metadatos, configuraciones y condiciones. IoT Hub mantiene un dispositivo gemelo por cada dispositivo que se conecta a IoT Hub.

* **Autenticación por dispositivo y conectividad segura**. Puede aprovisionar cada dispositivo con su propia [clave de seguridad][lnk-devguide-security] para permitirle conectarse a IoT Hub. El [registro de identidades de IoT Hub][lnk-devguide-identityregistry] almacena identidades y claves en una solución. Un back-end de soluciones puede agregar dispositivos individuales para permitir o denegar listas que permitan controlar por completo el acceso a los dispositivos.

* **Enrutamiento de mensajes de dispositivo a nube a servicios de Azure según reglas declarativas**. IoT Hub permite definir rutas de mensajes basadas en reglas de enrutamiento para controlar dónde el centro envía mensajes de dispositivo a nube. Las reglas de enrutamiento no requieren que se escriba ningún código y pueden sustituir a los distribuidores de mensajes personalizados posteriores a la ingesta.

* **Supervisión de operaciones de conectividad del dispositivo**. Puede recibir registros de operación detallados sobre operaciones de administración de identidad de dispositivos y eventos de conectividad de dispositivos. Esta funcionalidad de supervisión permite a una solución de IoT identificar problemas de conectividad. Estos registros se usan para identificar los dispositivos que proporcionan credenciales incorrectas, envían mensajes con demasiada frecuencia o rechazan todos los mensajes de la nube al dispositivo.

* **Amplio conjunto de bibliotecas de dispositivos**. Los [SDK de dispositivo IoT de Azure][lnk-device-sdks] están disponibles y son compatibles con varios lenguajes y plataformas: C para muchas distribuciones de Linux, Windows y sistemas operativos en tiempo real. Los SDK de dispositivos IoT de Azure admiten lenguajes administrados como C#, Java y JavaScript.

* **Extensibilidad y protocolos de IoT**. Si la solución no puede usar las bibliotecas de dispositivos, IoT Hub expone un protocolo público que permite a los dispositivos usar los protocolos MQTT v3.1.1, HTTPS 1.1 o AMQP 1.0 de forma nativa. IoT Hub también se puede ampliar para que admita protocolos personalizados mediante la:

  * Creación de una puerta de enlace de campo con [Azure IoT Edge][lnk-iot-edge] que convierte su protocolo personalizado en uno de los tres protocolos compatibles con IoT Hub.
  * Personalización de la [puerta de enlace de protocolo de IoT de Azure][protocol-gateway], un componente de código abierto que se ejecuta en la nube.

* **Escala**. El centro de IoT de Azure se puede escalar a millones de dispositivos conectados de manera simultánea y a millones de eventos por segundo.

## <a name="gateways"></a>Puertas de enlace

En una solución de IoT, una puerta de enlace es normalmente una [puerta de enlace de protocolo][lnk-iotedge] implementada en la nube o una [puerta de enlace de campo][lnk-field-gateway] implementada localmente con los dispositivos. Una puerta de enlace de protocolo realiza la traducción de protocolos, por ejemplo, de MQTT a AMQP. Una puerta de enlace de campo puede ejecutar análisis en el perímetro, tomar decisiones sujetas a limitaciones temporales para reducir la latencia, proporcionar servicios de administración del dispositivo, aplicar restricciones de privacidad y seguridad y realizar la traducción de protocolos. Ambos tipos de puerta de enlace actúan como intermediarios entre los dispositivos e IoT Hub.

Una puerta de enlace de campo es diferente de un dispositivo de enrutamiento de tráfico simple (como un firewall o un dispositivo de traducción de direcciones de red) porque normalmente desempeña un rol activo en la administración del acceso y del flujo de la información en su solución.

Una solución puede incluir tanto puertas de enlace de protocolo como de campo.

## <a name="how-does-iot-hub-work"></a>¿Cómo funciona el Centro de IoT?

Azure IoT Hub implementa el modelo de [comunicación asistida por servicio][lnk-service-assisted-pattern] para mediar en las interacciones entre los dispositivos y su back-end de soluciones. El objetivo de la comunicación asistida por servicio es establecer rutas de acceso de comunicación bidireccional de confianza entre un sistema de control, como IoT Hub, y los dispositivos de uso especial en un espacio físico que no es de confianza. El patrón establece los principios siguientes:

* La seguridad tiene prioridad sobre el resto de las funciones.

* Los dispositivos no aceptan información de redes no solicitadas. Un dispositivo establece todas las conexiones y las rutas en modo de solo salida. Para que un dispositivo reciba comandos desde el back-end de la solución, dicho dispositivo debe iniciar una conexión con regularidad para comprobar si hay comandos pendientes de procesar.

* Los dispositivos solo deben conectarse o establecer rutas a servicios conocidos a los que están emparejados, como un Centro de IoT.

* La ruta de comunicación entre el dispositivo y el servicio o el dispositivo y puerta de enlace se protege en el nivel del protocolo de aplicaciones.

* La autenticación y la autorización de nivel de sistema se basan en las identidades de cada dispositivo. Hacen los permisos y las credenciales de acceso revocables casi al instante.

* En el caso de los dispositivos que se conectan esporádicamente debido a problemas de alimentación o de conectividad, la comunicación bidireccional funciona manteniendo los comandos y las notificaciones hasta que un dispositivo se conecta para recibirlos. El Centro de IoT mantiene colas específicas de dispositivos para los comandos que envía.

* Los datos de carga de aplicaciones se protegen por separado para proteger el tránsito a través de las puertas de enlace a un servicio determinado.

El sector de la telefonía móvil ha usado el patrón de comunicación asistida por servicio para implementar servicios de notificación push como, por ejemplo, [Windows Push Notification Services][lnk-wns], [Google Cloud Messaging][lnk-google-messaging] y [Apple Push Notification Service][lnk-apple-push].

IoT Hub es compatible con la ruta de acceso de emparejamiento público de ExpressRoute.

## <a name="next-steps"></a>Pasos siguientes

Para aprender a enviar mensajes desde un dispositivo y recibirlos desde IoT Hub, y para saber cómo configurar rutas de mensajes, consulte [Envío y recepción de mensajes con IoT Hub][lnk-send-messages].

Para saber cómo IoT Hub posibilita la administración de dispositivos basada en estándares para administrar, configurar y actualizar sus dispositivos de forma remota, consulte [Información general sobre la administración de dispositivos con IoT Hub][lnk-device-management].

Para implementar aplicaciones cliente que se ejecuten en una gran variedad de plataformas de hardware de dispositivo y sistemas operativos, puede usar los SDK de dispositivo IoT de Azure. Los SDK de dispositivo incluyen bibliotecas que facilitan el envío de telemetría a un IoT Hub y la recepción de mensajes de la nube al dispositivo. Al usar los SDK de dispositivo, puede elegir entre varios protocolos de red para comunicarse con IoT Hub. Para más información, consulte la [información sobre los SDK de dispositivo][lnk-device-sdks].

Para comenzar a escribir código y ejecutar algunos ejemplos, consulte el tutorial [Introducción a IoT Hub][lnk-get-started].

[img-architecture]: media/iot-hub-what-is-iot-hub/hubarchitecture.png

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[protocol-gateway]: https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md
[lnk-service-assisted-pattern]: http://blogs.msdn.com/b/clemensv/archive/2014/02/10/service-assisted-communication-for-connected-devices.aspx "Comunicación asistida por servicios, publicación de blog de Clemens Vasters"
[lnk-compare]: iot-hub-compare-event-hubs.md
[lnk-iotedge]: iot-hub-protocol-gateway.md
[lnk-field-gateway]: iot-hub-devguide-endpoints.md#field-gateways
[lnk-devguide-identityregistry]: iot-hub-devguide-identity-registry.md
[lnk-devguide-security]: iot-hub-devguide-security.md
[lnk-wns]: https://msdn.microsoft.com/library/windows/apps/mt187203.aspx
[lnk-google-messaging]: https://developers.google.com/cloud-messaging/
[lnk-apple-push]: https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW9
[lnk-device-sdks]: https://github.com/Azure/azure-iot-sdks
[lnk-refarch]: http://download.microsoft.com/download/A/4/D/A4DAD253-BC21-41D3-B9D9-87D2AE6F0719/Microsoft_Azure_IoT_Reference_Architecture.pdf
[lnk-iot-edge]: https://github.com/Azure/iot-edge
[lnk-send-messages]: iot-hub-devguide-messaging.md
[lnk-device-management]: iot-hub-device-management-overview.md

[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-c2d-guidance]: iot-hub-devguide-c2d-guidance.md
[lnk-d2c-guidance]: iot-hub-devguide-d2c-guidance.md

[lnk-security-ground-up]: iot-hub-security-ground-up.md
