<properties
 pageTitle="Centro de IoT de Microsoft Azure e Internet de las cosas (IoT) | Microsoft Azure"
 description="Descripción general de IoT en Azure que incluye la arquitectura de una solución de ejemplo y cómo se relaciona con el Centro de IoT de Azure, los SDK de dispositivos y las soluciones preconfiguradas"
 services="iot-hub"
 documentationCenter=".net"
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="tbd"
 ms.date="09/04/2015"
 ms.author="dobett"/>

[AZURE.INCLUDE [iot-azure-and-iot](../../includes/iot-azure-and-iot.md)]

## Ámbito de esta documentación
Estos artículos de Azure y IoT se centran en dos conjuntos de recursos que pueden ayudarle a implementar su propia solución IoT basada en la plataforma IoT de Microsoft.

- Centro de IoT de Azure
- SDK de dispositivos IoT de Azure

Puede que también le interese [Azure IoT Suite][lnk-iot-suite], un conjunto de soluciones preconfiguradas que le permiten comenzar rápidamente y escalar proyectos IoT en escenarios comunes de IoT como, por ejemplo, supervisión remota, administración de activos y mantenimiento predictivo.

### Centro de IoT de Azure
Centro de IoT es un servicio de Azure que le permite recibir datos de dispositivo a nube a escala desde los dispositivos y enrutar esos datos a un procesador de eventos de transmisión. El Centro de IoT puede enviar comandos de nube a dispositivo a dispositivos concretos mediante colas específicas del dispositivo.

Además, el servicio Centro de IoT incluye un registro de identidades de dispositivo que le ayuda a aprovisionar dispositivos y administrar los dispositivos que pueden conectarse a un Centro de IoT.

### SDK de dispositivos IoT de Azure
Microsoft proporciona SDK de dispositivos IoT que permiten implementar aplicaciones cliente que se ejecuten en una gran variedad de plataformas de hardware de dispositivos y sistemas operativos. Los SDK de dispositivos IoT incluyen bibliotecas que facilitan el envío de datos de telemetría de dispositivo a nube al Centro de IoT y la recepción de comandos de nube a dispositivo desde el Centro de IoT. Estos SDK de dispositivos IoT permiten elegir entre una serie de protocolos de red diferentes para comunicarse con el Centro de IoT de Azure.

## Pasos siguientes
Para comenzar a usar IoT en Azure, explore estos recursos:

- [Introducción al Centro de IoT][lnk-getstarted]
- [Centro de IoT de Azure][lnk-iot-hub]
- [Azure IoT Suite][lnk-iot-suite].  


[lnk-getstarted]: iot-hub-csharp-csharp-getstarted.md
[lnk-iot-hub]: iot-hub-what-is-iot-hub.md
[lnk-iot-suite]: http://azure.microsoft.com/solutions/iot/

<!---HONumber=Oct15_HO1-->