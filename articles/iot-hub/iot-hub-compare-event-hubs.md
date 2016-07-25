<properties
 pageTitle="Comparación del Centro de IoT de Azure con los Centros de eventos de Azure | Microsoft Azure"
 description="Comparación de los servicios del Centro de IoT de Azure y los servicios de centros de eventos de Azure en la que se resaltan diferencias funcionales y casos de uso."
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
 ms.date="06/06/2016"
 ms.author="elioda"/>

# Comparación del Centro de IoT y los Centros de eventos

Uno de los principales usos del Centro de IoT de Azure es recopilar telemetría de dispositivos. Por este motivo, el Centro de IoT a menudo se compara con los [Centros de eventos de Azure][]. Al igual que el Centro de IoT, Centros de eventos de Azure es un servicio de procesamiento de eventos que ofrece entrada de telemetría y eventos en la nube a escala masiva, con una latencia baja y una confiabilidad alta.

Sin embargo, los servicios tienen muchas diferencias que se detallan en la tabla siguiente.

| Ámbito | Centro de IoT | Centros de eventos |
| ---- | ------- | ---------- |
| Patrones de comunicación | Habilita la mensajería de dispositivo a nube y de nube a dispositivo. | Solo se habilita la entrada de eventos (normalmente se consideran escenarios dispositivo a nube). |
| Compatibilidad con protocolos de dispositivo | Admite AMQP, AMQP sobre WebSockets, MQTT y HTTP/1. Además el Centro de IoT funciona con la [Puerta de enlace de protocolos de IoT de Azure][lnk-azure-protocol-gateway], una implementación de puerta de enlace de protocolos personalizable para admitir protocolos personalizados. | Admite AMQP, AMQP sobre WebSockets y HTTP/1. |
| Seguridad | Ofrece identidad por dispositivo y control de acceso revocable. Vea la [sección de seguridad de la Guía del desarrollador del Centro de IoT]. | Ofrece [directivas de acceso compartido][Event Hub - security] para todo los Centros de eventos, con soporte para revocación limitada mediante [directivas de publicador][Event Hub publisher policies]. A menudo se requiere de las soluciones de IoT que implementen una solución personalizada para admitir las credenciales por dispositivo y las medidas contra la suplantación de identidad. |
| Supervisión de operaciones | Permite a las soluciones de IoT suscribirse a un amplio conjunto de eventos de conectividad y administración de identidad del dispositivo, como errores de autenticación de dispositivos individuales, establecimientos de limitaciones y excepciones de formato incorrecto. Estos eventos permiten identificar rápidamente problemas de conectividad en los dispositivos individuales. | Muestra solo las métricas agregadas. |
| Escala | Está optimizado para admitir millones de dispositivos conectados al mismo tiempo. | Pueden admitir un número más reducido de conexiones simultáneas: hasta 5000 conexiones AMQP, según las [cuotas de Bus de servicio de Azure][]. Por otro lado, los Centros de eventos permiten especificar la partición para cada mensaje enviado. |
| SDK de dispositivo | Ofrece [SDK de dispositivo][Azure IoT Hub SDKs] para una gran variedad de plataformas y lenguajes. | Es compatible con .NET, C. También ofrece interfaces de envío HTTP y AMQP. |
| Carga de archivos | Permite que las soluciones IoT carguen en la nube archivos de los dispositivos. Incluye un punto de conexión de notificación de archivos para la integración del flujo de trabajo y una categoría de supervisión de operaciones para la compatibilidad con la depuración. | Utiliza un modelo de comprobación de notificaciones para solicitar manualmente archivos a los dispositivos y proporcionarles a estos una clave de almacenamiento para la transacción. |

En resumen, aunque el único uso sea la entrada de telemetría de dispositivo a nube, el Centro de IoT ofrece un servicio que está diseñado específicamente para la conectividad de dispositivos IoT. Continuará expandiendo las propuestas de valor para estos escenarios con características específicas de IoT. Los Centros de eventos están diseñados para la entrada de eventos a gran escala, tanto en escenarios de conexión entre centros de datos como dentro del propio centro de datos.

No es raro usar el Centro de IoT y los Centros de eventos en la misma solución, en la que el Centro de IoT controla la comunicación de dispositivo a nube y los segundos incorporan eventos de fase posterior a los motores de procesamiento en tiempo real.

## Pasos siguientes

Para más información acerca de planificación de la implementación del Centro de IoT, consulte [Escalado del Centro de IoT][lnk-scaling].

Para explorar aún más las funcionalidades de Centro de IoT, consulte:

- [Guía del desarrollador][lnk-devguide]
- [Exploración de la administración de dispositivos desde Centro de IoT de Azure con la IU de ejemplo][lnk-dmui]
- [SDK de puerta de enlace de IoT (beta): envío de mensajes del dispositivo a la nube con un dispositivo simulado usando Linux][lnk-gateway]
- [Administración de Centros de IoT a través del portal de Azure][lnk-portal]

[Centros de eventos de Azure]: ../event-hubs/event-hubs-what-is-event-hubs.md
[sección de seguridad de la Guía del desarrollador del Centro de IoT]: iot-hub-devguide.md#security
[Event Hub - security]: ../event-hubs/event-hubs-authentication-and-security-model-overview.md
[Event Hub publisher policies]: ../event-hubs/event-hubs-overview.md#common-publisher-tasks
[cuotas de Bus de servicio de Azure]: ../service-bus/service-bus-quotas.md
[Azure IoT Hub SDKs]: https://github.com/Azure/azure-iot-sdks/blob/master/readme.md
[lnk-azure-protocol-gateway]: iot-hub-protocol-gateway.md

[lnk-scaling]: iot-hub-scaling.md
[lnk-devguide]: iot-hub-devguide.md
[lnk-dmui]: iot-hub-device-management-ui-sample.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
[lnk-portal]: iot-hub-manage-through-portal.md

<!---HONumber=AcomDC_0713_2016-->