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
 ms.date="10/02/2015"
 ms.author="elioda"/>

# Comparación del Centro de IoT y los Centros de eventos

Uno de los principales usos del Centro de IoT de Azure es recopilar telemetría de dispositivos. Por este motivo, el Centro de IoT suele compararse con los [Centros de eventos][], que son un servicio de procesamiento de eventos que ofrece entrada de telemetría y eventos en la nube a escala masiva, con una latencia baja y una fiabilidad alta.

Los servicios, sin embargo, tienen muchas diferencias que se detallan en las secciones siguientes.

| Ámbito | Centro de IoT | Centros de eventos |
| ---- | ------- | ---------- |
| Patrones de comunicación | Entrada de eventos de dispositivo a nube y mensajería de nube a dispositivo. | Solo ingreso de eventos (normalmente se consideran escenarios dispositivo a nube). |
| Seguridad | Identidad por dispositivo y control de acceso revocable. Consulte la [Guía para desarrolladores del Centro de IoT: seguridad ]. | [Directivas de acceso compartido][Event Hub - security] para todo el Centro de eventos, con soporte para revocación limitada mediante [directivas de publicador][Event Hub publisher policies]. En el contexto de las soluciones IoT, a menudo es necesario implementar una solución personalizada para admitir las credenciales por dispositivo y las medidas contra la suplantación de identidad. |
| Escala | El Centro de IoT está optimizado para admitir millones de dispositivos conectados al mismo tiempo. | Los Centros de eventos pueden admitir un número más reducido de conexiones simultáneas: hasta 5000 conexiones AMQP, según las [cuotas de Bus de servicio][]. Por otro lado, los Centros de eventos permiten especificar la partición para cada mensaje enviado. |
| SDK de dispositivo | El Centro de IoT proporciona [SDK de dispositivo][Azure IoT Hub SDKs] para una gran variedad de plataformas y lenguajes. | Los Centros de eventos son compatibles con .NET, C y proporcionan interfaces de envío HTTP y AMQP. |

En resumen, aunque el único uso sea la entrada de telemetría de dispositivo a nube, el Centro de IoT proporciona un servicio que está diseñado específicamente para la conectividad de dispositivos IoT, y continuará ampliando las propuestas de valor para estos escenarios con características específicas de IoT. Los Centros de eventos están diseñados para la entrada de eventos a gran escala, tanto en escenarios de conexión entre centros de datos como dentro del propio centro de datos.

No es raro usar el Centro de IoT y los Centros de eventos conjuntamente, y dejar que el primero controle la comunicación de dispositivo a nube y que el segundo incorpore eventos de fase posterior a los motores de procesamiento en tiempo real.

## Pasos siguientes

Siga estos vínculos para obtener más información sobre el Centro de IoT de Azure:

- [Introducción a los Centros de IoT (Tutorial)][lnk-get-started]
- [¿Qué es el Centro de IoT de Azure?][]

[Centros de eventos]: ../event-hubs/event-hubs-what-is-event-hubs.md
[Guía para desarrolladores del Centro de IoT: seguridad ]: iot-hub-devguide.md#security
[Event Hub - security]: ../event-hubs/event-hubs-authentication-and-security-model-overview.md
[Event Hub publisher policies]: ../event-hubs/event-hubs-overview.md#common-publisher-tasks
[cuotas de Bus de servicio]: ../service-bus/service-bus-quotas.md
[Azure IoT Hub SDKs]: https://github.com/Azure/azure-iot-sdks/blob/master/readme.md
[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[¿Qué es el Centro de IoT de Azure?]: iot-hub-what-is-iot-hub.md

<!---HONumber=AcomDC_1210_2015-->