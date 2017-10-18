---
title: Soluciones de Azure para Internet de las cosas (IoT Suite) | Microsoft Docs
description: "Información general de una arquitectura de la solución de IoT de ejemplo y cómo se relaciona con dispositivos, el servicio IoT Hub de Azure, SDK de dispositivo IoT de Azure, SDK del servicio IoT de Azure y otros servicios de Azure."
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: a859e379-dca7-42fa-bdf6-1125c86ad140
ms.service: iot-hub
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/15/2017
ms.author: dobett
ms.openlocfilehash: 417ca4b6ecc39cbdafd8e12b5360b370d0ce79fa
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
[!INCLUDE [iot-azure-and-iot](../../includes/iot-azure-and-iot.md)]

## <a name="next-steps"></a>Pasos siguientes

Azure IoT Hub es un servicio de Azure que permite la comunicación bidireccional fiable y segura entre el back-end de la solución y millones de dispositivos. Habilita al back-end de la solución para:

* Recibir telemetría a escala de los dispositivos.
* Distribuir datos de los dispositivos a un procesador de eventos de transmisión.
* Recibir cargas de archivos de los dispositivos.
* Enviar mensajes de nube a dispositivo a dispositivos concretos.

Puede usar el Centro de IoT para implementar su propio back-end de soluciones. Además, IoT Hub incluye un registro de identidades que se usa para aprovisionar dispositivos, sus credenciales de seguridad y sus derechos para conectarse al centro de IoT. Para más información acerca de IoT Hub, consulte la [introducción a IoT Hub][lnk-iot-hub].

Para más información acerca de cómo administrar los dispositivos de forma remota mediante la administración basada en estándares de Azure IoT Hub, consulte [Información general sobre la administración de dispositivos con IoT Hub][lnk-device-management].

Para implementar aplicaciones cliente que se ejecuten en una gran variedad de plataformas de hardware de dispositivo y sistemas operativos, puede usar los SDK de dispositivo IoT de Azure. Los SDK de dispositivo incluyen bibliotecas que facilitan el envío de telemetría a un IoT Hub y la recepción de mensajes de la nube al dispositivo. Al usar los SDK de dispositivo, puede elegir entre varios protocolos de red para comunicarse con IoT Hub. Para más información, consulte la [información sobre los SDK de dispositivo][lnk-device-sdks].

Para comenzar a escribir código y ejecutar algunos ejemplos, consulte el tutorial [Introducción a IoT Hub][lnk-getstarted].

También puede interesarle [Conjunto de aplicaciones de IoT de Azure][lnk-iot-suite], que es una colección de soluciones preconfiguradas. El Conjunto de aplicaciones de IoT le permite comenzar rápidamente y escalar proyectos IoT en escenarios comunes de IoT como, por ejemplo, supervisión remota, administración de activos y mantenimiento predictivo.

[lnk-getstarted]: iot-hub-csharp-csharp-getstarted.md
[lnk-device-sdks]: https://github.com/Azure/azure-iot-sdks
[lnk-iot-hub]: iot-hub-what-is-iot-hub.md
[lnk-iot-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/
[lnk-iotdev]: https://azure.microsoft.com/develop/iot/
[lnk-device-management]: iot-hub-device-management-overview.md
