<properties
 pageTitle="Soluciones de Azure para EL Internet de las cosas | Microsoft Azure"
 description="Descripción general de IoT en Azure que incluye la arquitectura de una solución de ejemplo y cómo se relaciona con el Centro de IoT de Azure, los SDK de dispositivos y las soluciones preconfiguradas"
 services="iot-hub"
 documentationCenter=""
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="11/05/2015"
 ms.author="dobett"/>

[AZURE.INCLUDE [iot-azure-and-iot](../../includes/iot-azure-and-iot.md)]

## Pasos siguientes

El Centro de IoT es un servicio de Azure que recibe datos telemetría a escala desde sus dispositivos y enruta esos datos a un procesador de eventos de transmisión. Puede usar el centro de IoT para implementar su propio back-end de solución. El Centro de IoT puede enviar también comandos de nube a dispositivo a dispositivos concretos. Además, el Centro de IoT incluye un registro de identidades de dispositivo que puede usar para aprovisionar dispositivos y administrar aquellos que pueden conectarse al Centro. Para obtener más información, consulte:

- [¿Qué es el Centro de IoT?][lnk-iot-hub]
- [Introducción al centro de IoT][lnk-getstarted]

Para implementar aplicaciones cliente que se ejecuten en una gran variedad de plataformas de hardware de dispositivos y sistemas operativos, puede usar los SDK de dispositivos IoT. Los SDK de dispositivos IoT incluyen bibliotecas que facilitan el envío de telemetría a un Centro de IoT y la recepción de comandos de nube a dispositivo. Al usar los SDK, puede elegir entre una serie de protocolos de red para comunicarse con el Centro de IoT. Para más información, vea la [información sobre los SDK de dispositivo][lnk-device-sdks].

También puede interesarle el [Conjunto de aplicaciones de IoT de Azure][lnk-iot-suite], que es una colección de soluciones preconfiguradas. El Conjunto de aplicaciones de IoT le permite comenzar rápidamente y escalar proyectos IoT en escenarios comunes de IoT como, por ejemplo, supervisión remota, administración de activos y mantenimiento predictivo.

[lnk-getstarted]: iot-hub-csharp-csharp-getstarted.md
[lnk-device-sdks]: https://github.com/Azure/azure-iot-sdks/blob/master/readme.md
[lnk-iot-hub]: iot-hub-what-is-iot-hub.md
[lnk-iot-suite]: http://azure.microsoft.com/solutions/iot/
[lnk-iotdev]: https://azure.microsoft.com/develop/iot/

<!---HONumber=AcomDC_1223_2015-->