---
title: Información de los SDK de IoT de Azure | Microsoft Docs
description: 'Guía del desarrollador: Información y vínculos a los diversos SDK de dispositivos y servicios IoT de Azure que puede usar para compilar aplicaciones de back-end y de aplicaciones de dispositivo.'
services: iot-hub
documentationcenter: ''
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: c5c9a497-bb03-4301-be2d-00edfb7d308f
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/12/2018
ms.author: dobett
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2380e72083ec0c9453db991be5fbcc89310cee17
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/06/2018
---
# <a name="understand-and-use-azure-iot-hub-sdks"></a>Información y uso de los SDK de Azure IoT Hub

Hay dos categorías de kits de desarrollo de software (SDK) para trabajar con IoT Hub:

* Los **SDK de dispositivos** permiten diseñar aplicaciones que se ejecuten en sus dispositivos IoT. Estas aplicaciones envían datos de telemetría a IoT Hub y, de manera opcional, reciben mensajes, trabajos, métodos o actualizaciones gemelas de dicho servicio.

* Los **SDK de servicios** le permiten administrar IoT Hub y, opcionalmente, enviar mensajes, programar trabajos, invocar métodos directos o enviar las actualizaciones de las propiedades deseada a los dispositivos de IoT.

[Aquí][lnk-benefits-blog] obtendrá información acerca de las ventajas de realizar desarrollo con los SDK de IoT de Azure.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="azure-iot-device-sdks"></a>SDK de dispositivos IoT de Azure

Los SDK de dispositivos IoT de Microsoft Azure contienen código que facilita la creación dispositivos y aplicaciones que se conectan a servicios de Azure IoT Hub y que este administra.

SDK de dispositivos Azure IoT Hub para .NET: 
* Se instala desde [NuGet][lnk-nuget-csharp-device]
* [Código fuente][lnk-dotnet-sdk]
* [Referencia de API][lnk-dotnet-ref]

SDK de dispositivos Azure IoT Hub para C: escrito en ANSI C (C99) para facilitar la portabilidad y una compatibilidad con un gran número de plataformas
* Se instala desde [apt get, MBED, Arduino IDE o NuGet][lnk-c-package]
* [Código fuente][lnk-c-sdk]
* [Referencia de API][lnk-c-ref]

SDK de dispositivos Azure IoT Hub para Java: 
* Se agrega al proyecto [Maven][lnk-maven-device]
* [Código fuente][lnk-java-sdk]
* [Referencia de API][lnk-java-ref]

SDK de dispositivos Azure IoT Hub para Node.js: 
* Instalar desde [npm][lnk-npm-device]
* [Código fuente][lnk-node-sdk]
* [Referencia de API][lnk-node-ref]

SDK de dispositivos Azure IoT Hub para Python: 
* Se instala desde [pip][lnk-pip-device]
* [Código fuente][lnk-python-sdk]

> [!NOTE]
> Consulte los archivos Léame en los repositorios de GitHub para obtener información sobre el uso de administradores de paquetes específicos de la plataforma y el lenguaje para instalar los archivos binarios y dependencias en el equipo de desarrollo.
> 
> 

### <a name="os-platform-and-hardware-compatibility"></a>Compatibilidad de hardware y de plataformas de sistema operativo

Para más información acerca de la compatibilidad del SDK con dispositivos de hardware concretos, consulte el [catálogo de dispositivos Azure Certified for IoT][lnk-certified] o el repositorio individual.

## <a name="azure-iot-service-sdks"></a>SDK de servicios IoT de Azure

Los SDK de servicios IoT de Azure contienen código que facilitan la creación de aplicaciones que interactúan directamente con IoT Hub para administrar dispositivos y seguridad.

SDK de servicios Azure IoT Hub para .NET:
* Se descarga de [NuGet][lnk-nuget-csharp-service]
* [Código fuente][lnk-dotnet-sdk]
* [Referencia de API][lnk-dotnet-service-ref]

SDK de servicios Azure IoT Hub para Java: 
* Se agrega al proyecto [Maven][lnk-maven-service]
* [Código fuente][lnk-java-sdk]
* [Referencia de API][lnk-java-service-ref]

SDK de servicios Azure IoT Hub para Node.js: 
* Se descarga de [npm][lnk-npm-service]
* [Código fuente][lnk-node-sdk]
* [Referencia de API][lnk-node-service-ref]

SDK de servicios Azure IoT Hub para Python: 
* Se descarga de [pip][lnk-pip-service]
* [Código fuente][lnk-python-sdk]

SDK de servicios Azure IoT Hub para C: 
* Se descarga de [apt get, MBED, Arduino IDE o NuGet][lnk-c-package]
* [Código fuente][lnk-c-sdk]

> [!NOTE]
> Consulte los archivos Léame en los repositorios de GitHub para obtener información sobre el uso de administradores de paquetes específicos de la plataforma y el lenguaje para instalar los archivos binarios y dependencias en el equipo de desarrollo.


## <a name="next-steps"></a>Pasos siguientes

Otros temas de referencia en la Guía del desarrollador de IoT Hub son:

* [IoT Hub endpoints][lnk-devguide-endpoints] (Puntos de conexión de IoT Hub)
* [IoT Hub query language for device twins, jobs, and message routing][lnk-devguide-query] (Lenguaje de consulta de IoT Hub para dispositivos gemelos, trabajos y enrutamiento de mensajes)
* [Cuotas y limitación][lnk-devguide-quotas]
* [Compatibilidad con MQTT de IoT Hub][lnk-devguide-mqtt]
* [Referencia de la API REST de IoT Hub][lnk-rest-ref]

<!-- Links and images -->

[lnk-c-sdk]: https://github.com/Azure/azure-iot-sdk-c
[lnk-dotnet-sdk]: https://github.com/Azure/azure-iot-sdk-csharp
[lnk-java-sdk]: https://github.com/Azure/azure-iot-sdk-java
[lnk-node-sdk]: https://github.com/Azure/azure-iot-sdk-node
[lnk-python-sdk]: https://github.com/Azure/azure-iot-sdk-python
[lnk-certified]: https://catalog.azureiotsuite.com/

[lnk-dotnet-ref]: https://docs.microsoft.com/dotnet/api/microsoft.azure.devices?view=azure-dotnet
[lnk-dotnet-service-ref]: https://docs.microsoft.com/dotnet/api/microsoft.azure.devices
[lnk-c-ref]: https://azure.github.io/azure-iot-sdk-c/index.html
[lnk-java-ref]: https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device
[lnk-node-ref]: https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device
[lnk-rest-ref]: https://docs.microsoft.com/rest/api/iothub/
[lnk-java-service-ref]: https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.service
[lnk-node-service-ref]: https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.service

[lnk-maven-device]: https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-device-sdk
[lnk-maven-service]: https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-service-sdk
[lnk-npm-device]: https://www.npmjs.com/package/azure-iot-device
[lnk-npm-service]: https://www.npmjs.com/package/azure-iothub
[lnk-nuget-csharp-device]: https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/
[lnk-nuget-csharp-service]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[lnk-c-package]: https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/readme.md
[lnk-pip-device]: https://pypi.python.org/pypi/azure-iothub-device-client/
[lnk-pip-service]: https://pypi.python.org/pypi/azure-iothub-service-client/


[lnk-devguide-endpoints]: iot-hub-devguide-endpoints.md
[lnk-devguide-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-devguide-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-benefits-blog]: https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/
