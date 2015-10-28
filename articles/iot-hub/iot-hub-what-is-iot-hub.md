<properties
 pageTitle="Información general del Centro de IoT de Azure | Microsoft Azure"
 description="Información general del servicio del centro de IoT de Azure que aborda la conectividad de dispositivos, los patrones de comunicación y el patrón de comunicación asistida por servicios"
 services="iot-hub"
 documentationCenter=""
 authors="fsautomata"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="09/29/2015"
 ms.author="elioda"/>

# ¿Qué es el centro de IoT de Azure?

El centro de IoT de Azure es un servicio totalmente administrado que permite la comunicación bidireccional fiable y segura entre millones de dispositivos IoT y una aplicación back-end. El centro de IoT de Azure permite el envío a gran escala de mensajes de dispositivo a nube y viceversa. Asimismo, permite establecer comunicaciones seguras mediante el uso de credenciales de seguridad por cada dispositivo y funciones de control de acceso; además, incluye bibliotecas de dispositivos para las plataformas y los lenguajes más populares.

![El centro de IoT como puerta de enlace de nube][img-architecture]

## Conectividad de dispositivos de IoT

Uno de los mayores desafíos relacionados con los proyectos de IoT consiste en conectar dispositivos al servidor de aplicaciones de manera segura y fiable. Los dispositivos de IoT a menudo tienen características diferentes en comparación con otros clientes como, por ejemplo, exploradores, otros servidores y aplicaciones móviles:

-   A menudo son sistemas incrustados sin operador humano.

-   Pueden encontrarse en ubicaciones remotas, donde el acceso físico resulta muy costoso.

-   La conectividad con el back-end de aplicaciones puede ser la única manera de tener acceso al dispositivo.

-   También pueden limitaciones de recursos de procesamiento y/o alimentación.

-   La conectividad de red puede ser intermitente, costosa o insuficiente para el dispositivo.

-   Puede que necesite usar protocolos de aplicación propios, personalizados o específicos de determinados sectores.

-   Existe un amplio conjunto de plataformas de hardware y software para la creación dispositivos.

Además de los requisitos anteriores, toda solución de IoT debe ser capaz de ofrecer escalabilidad, seguridad y fiabilidad. Todos estos aspectos se combinan en un conjunto de requisitos de conectividad cuya implementación resulta compleja y lenta si se realiza con tecnologías tradicionales como, por ejemplo, los contenedores web y los agentes de mensajería.

## ¿Por qué usar el centro de IoT de Azure?

El centro de IoT de Azure aborda los requisitos de conectividad del dispositivo de las maneras siguientes:

-   **Autenticación por dispositivo y conectividad segura**. Cada dispositivo puede usar su propia clave de seguridad para conectarse al centro de IoT. El back-end de aplicaciones, a continuación, puede individualmente crear listas blancas y listas negras de cada dispositivo, lo que permite tener un control completo sobre el acceso a los dispositivos.

-   **Amplio conjunto de bibliotecas de dispositivos**. Los SDK de dispositivos de IoT de Azure están disponibles y son compatibles con una amplia gama de lenguajes y plataformas: C para muchas distribuciones de Linux, Windows y RTOS. Los SDK de dispositivos IoT de Azure admiten lenguajes administrados como C#, Java y JavaScript.

-   **Extensibilidad y protocolos de IoT**. Si la solución no puede hacer uso de las bibliotecas de dispositivos, el Centro de IoT de Azure expone un protocolo público que permite a los dispositivos usar los protocolos HTTP 1.1 y AMQP 1.0 de forma nativa. También puede ampliar el Centro de IoT para ofrecer compatibilidad con MQTT v3.1.1 con el componente de código abierto de puerta de enlace de protocolo de IoT de Azure. Es posible ejecutar la puerta de enlace de protocolo de IoT de Azure en la nube o de manera local y ampliarla para admitir protocolos personalizados.

-   **Escalabilidad.** El centro de IoT de Azure se puede escalar a millones de dispositivos conectados de manera simultánea y a millones de eventos por segundo.

Además de estas ventajas, que son genéricas en distintos patrones de comunicación, el Centro de IoT actualmente permite implementar los siguientes patrones de comunicación:

-   **Ingestión de dispositivos a nube basada en eventos.** Los dispositivos pueden enviar de manera fiable millones de eventos por segundo para que los procese el motor del procesador de eventos de ruta de acceso activa, o bien para que los almacene para realizar análisis de ruta de acceso inactiva. El Centro de IoT conserva los datos de eventos hasta 7 días para garantizar un procesamiento fiable y absorber picos de carga.

-   **Mensajería fiable de nube a dispositivo (o *comandos*).** El back-end de aplicaciones puede enviar mensajes fiables (es decir, con garantía de entrega al menos una vez) a dispositivos individuales. Cada mensaje tiene una configuración de período de vida individual y el back-end puede solicitar confirmación de entrega y vencimiento para garantizar la visibilidad completa del ciclo de vida de los mensajes de nube a dispositivo.

Además de los patrones de comunicación anteriores, puede implementar otros patrones conocidos como, por ejemplo, la carga y descarga de archivos aprovechando las ventajas de las características específicas de IoT del Centro de IoT de Azure como la administración de identidades de dispositivo coherente, la supervisión de conectividad y la escalabilidad.

## Patrón de comunicación asistida por servicio

El centro de IoT de Azure media en las interacciones entre los dispositivos y su aplicación back-end en una implementación del patrón de [comunicación asistida por servicio][lnk-service-assisted-pattern]. El objetivo de la comunicación asistida por servicio es establecer rutas de acceso de comunicación bidireccional de confianza entre sistemas de control (como el centro de IoT) y los dispositivos con una finalidad específica implementados en espacios físicos que no son de confianza. Para ello, el patrón establece los principios siguientes:

- La seguridad prima sobre el resto de las funciones.
- Los dispositivos no aceptan información de redes no solicitadas. Todas las conexiones y las rutas se establecen desde un dispositivo en modo de solo salida. Para que un dispositivo reciba comandos desde el back-end, dicho dispositivo debe iniciar una conexión con regularidad para comprobar si hay comandos pendientes de procesar.
- Los dispositivos generalmente solo se conectan o establecen rutas a servicios conocidos emparejados, como por ejemplo, una instancia de servicio del Centro de IoT.
- La ruta de comunicación entre el dispositivo y el servicio o el dispositivo y puerta de enlace se protege en el nivel del protocolo de aplicaciones.
- La autenticación y autorización de nivel de sistema deben estar basadas en las identidades de cada dispositivo y las credenciales de acceso y permisos deben poder revocarse casi de manera instantánea.
- La comunicación bidireccional de dispositivos con conexión esporádica debido a problemas de alimentación o de conectividad puede realizarse mediante el mantenimiento de comandos y notificaciones en los dispositivos hasta que estos se conecten para su recuperación. El Centro de IoT mantiene colas específicas de dispositivos de los comandos que se envían a los dispositivos.
- Los datos de carga de aplicaciones pueden protegerse por separado para, de este modo, proteger el tránsito en las puertas de enlace a un servicio determinado.

El patrón de comunicación asistida por servicio se ha usado de manera satisfactoria en el sector móvil a grandes escalas para implementar servicios de notificación push como, por ejemplo, el [Servicio de notificación de Windows](https://msdn.microsoft.com/library/windows/apps/mt187203.aspx), el [Servicio de mensajería en la nube de Google](https://developers.google.com/cloud-messaging/) y el [Servicio de notificaciones push de Apple](http://go.microsoft.com/fwlink/p/?linkid=272584&clcid=0x409).

## Pasos siguientes

Para obtener más información sobre el Centro de IoT de Azure, consulte estos vínculos:

* [Guía para desarrolladores del Centro de IoT]
* [Directrices sobre el Centro de IoT]
* [Lenguajes y plataformas de dispositivos compatibles]
* [Centro para desarrolladores de IoT de Azure]
* [Introducción al centro de IoT]

[IoT Hub Overview]: iot-hub-what-is-iot-hub.md
[Directrices sobre el Centro de IoT]: iot-hub-guidance.md
[Guía para desarrolladores del Centro de IoT]: iot-hub-devguide.md
[IoT Hub Supported Devices]: iot-hub-supported-devices.md
[Introducción al centro de IoT]: iot-hub-csharp-csharp-getstarted.md
[Lenguajes y plataformas de dispositivos compatibles]: iot-hub-sdks-summary.md#os-platforms-and-hardware-compatibility
[Centro para desarrolladores de IoT de Azure]: https://azure.microsoft.com/develop/iot/

[img-why-use]: media/iot-hub-what-is-iot-hub/image1.png
[img-architecture]: media/iot-hub-what-is-iot-hub/hubarchitecture.png

[lnk-service-assisted-pattern]: http://blogs.msdn.com/b/clemensv/archive/2014/02/10/service-assisted-communication-for-connected-devices.aspx "Comunicación asistida por servicios, publicación de blog de Clemens Vasters"

<!---HONumber=Oct15_HO3-->