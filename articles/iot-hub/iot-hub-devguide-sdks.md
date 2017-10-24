---
title: "Información de los SDK de IoT de Azure | Microsoft Docs"
description: "Guía del desarrollador: Información y vínculos a los diversos SDK de dispositivos y servicios IoT de Azure que puede usar para compilar aplicaciones de back-end y de aplicaciones de dispositivo."
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: c5c9a497-bb03-4301-be2d-00edfb7d308f
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/15/2017
ms.author: dobett
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e299de0953cefac925b0015a15983d25d456576f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="understand-and-use-azure-iot-sdks"></a>SDK de IoT de Azure

Hay tres categorías de kits de desarrollo de software (SDK) para trabajar con IoT Hub:

* Los **SDK de dispositivos** permiten diseñar aplicaciones que se ejecuten en sus dispositivos IoT. Estas aplicaciones envían telemetría a IoT Hub y, de manera opcional, reciben mensajes de dicho servicio.

* Los **SDK de servicios** permiten administrar IoT Hub y, si se desea, enviar mensajes a los dispositivos IoT.

* **Azure IoT Edge** permite crear puertas de enlace para dispositivos que no utilizan uno de los protocolos admitidos. Las puertas de enlace también pueden procesar mensajes en el borde.

Los SDK se incluyen para admitir varios lenguajes de programación.

## <a name="azure-iot-device-sdks"></a>SDK de dispositivos IoT de Azure

Los SDK de dispositivos IoT de Microsoft Azure contienen código que facilita la creación dispositivos y aplicaciones que se conectan a servicios del Centro de IoT de Azure y que este administra.

Los siguientes SDK de dispositivos IoT de Azure están disponibles para su descarga desde GitHub:

* [SDK de dispositivos IoT de Azure para .NET][lnk-dotnet-device-sdk]
* [SDK de dispositivos IoT de Azure para Java][lnk-java-device-sdk]
* [SDK de dispositivo IoT de Azure para Node.js][lnk-node-device-sdk]
* [SDK de dispositivo IoT de Azure para Python][lnk-python-device-sdk]
* [SDK de dispositivos IoT de Azure para C][lnk-c-device-sdk] escrito en ANSI C (C99) para portabilidad y amplia compatibilidad de plataformas. Hay dos bibliotecas de cliente de dispositivo para C, **iothub_client**de bako nivel y **serializer**.

> [!NOTE]
> Consulte los archivos Léame en los repositorios de GitHub para obtener información sobre el uso de administradores de paquetes específicos de la plataforma y el lenguaje para instalar los archivos binarios y dependencias en el equipo de desarrollo.
> 
> 

### <a name="os-platform-and-hardware-compatibility"></a>Compatibilidad de hardware y de plataformas de sistema operativo

Para más información sobre la compatibilidad del SDK con dispositivos de hardware específicos, consulte [Catálogo de dispositivos de Azure Certified for IoT][lnk-certified].

## <a name="azure-iot-service-sdks"></a>SDK de servicios IoT de Azure

Los SDK de servicios IoT de Azure contienen código que facilitan la creación de aplicaciones que interactúan directamente con IoT Hub para administrar dispositivos y seguridad.

Los siguientes SDK de servicios IoT de Azure están disponibles para su descarga desde GitHub:

* [SDK de servicios IoT de Azure para .NET][lnk-dotnet-service-sdk]
* [SDK de servicios IoT de Azure para Java][lnk-java-service-sdk]
* [SDK de servicios IoT de Azure para Node.js][lnk-node-service-sdk]
* [SDK de servicios IoT de Azure para Python][lnk-python-service-sdk]
* [SDK de servicios de Azure IoT para C][lnk-c-service-sdk]

> [!NOTE]
> Consulte los archivos Léame en los repositorios de GitHub para obtener información sobre el uso de administradores de paquetes específicos de la plataforma y el lenguaje para instalar los archivos binarios y dependencias en el equipo de desarrollo.

## <a name="azure-iot-edge"></a>Azure IoT Edge

Azure IoT Edge contiene la infraestructura y los módulos necesarios para crear soluciones de puerta de enlace IoT. Puede ampliar IoT Edge para crear puertas de enlace aptas para cualquier escenario de un extremo a otro.

Puede descargar [Azure IoT Edge][lnk-iot-edge] desde GitHub.

## <a name="online-api-reference-documentation"></a>Documentación de referencia de la API en línea

La siguiente lista contiene vínculos a documentación en línea de referencia de API para bibliotecas de dispositivo, servicio y puerta de enlace IoT de Azure:

* [.NET de Internet de las cosas (IoT)][lnk-dotnet-ref]
* [SDK de dispositivos IoT de Azure para Java][lnk-java-ref]
* [SDK de servicios IoT de Azure para Java][lnk-java-service-ref]
* [SDK de dispositivo IoT de Azure para Node.js][lnk-node-ref]
* [SDK de servicios IoT de Azure para Node.js][lnk-node-service-ref]
* [SDK de dispositivo IoT de Azure para C][lnk-c-ref]
* [REST de IoT Hub][lnk-rest-ref]
* [Azure IoT Edge][lnk-gateway-ref]

## <a name="next-steps"></a>Pasos siguientes

Otros temas de referencia en la Guía del desarrollador de IoT Hub son:

* [IoT Hub endpoints][lnk-devguide-endpoints] (Puntos de conexión de IoT Hub)
* [Referencia: Lenguaje de consulta de IoT Hub para dispositivos gemelos y trabajos][lnk-devguide-query]
* [Cuotas y limitación][lnk-devguide-quotas]
* [Compatibilidad con MQTT de IoT Hub][lnk-devguide-mqtt]

<!-- Links and images -->

[lnk-c-device-sdk]: https://github.com/Azure/azure-iot-sdk-c
[lnk-c-service-sdk]: https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_service_client
[lnk-dotnet-device-sdk]: https://github.com/Azure/azure-iot-sdk-csharp/tree/master/device
[lnk-java-device-sdk]: https://github.com/Azure/azure-iot-sdk-java/tree/master/device
[lnk-dotnet-service-sdk]: https://github.com/Azure/azure-iot-sdk-csharp/tree/master/service
[lnk-java-service-sdk]: https://github.com/Azure/azure-iot-sdk-java/tree/master/service
[lnk-node-device-sdk]: https://github.com/Azure/azure-iot-sdk-node/tree/master/device
[lnk-node-service-sdk]: https://github.com/Azure/azure-iot-sdk-node/tree/master/service
[lnk-python-device-sdk]: https://github.com/Azure/azure-iot-sdk-python/tree/master/device
[lnk-python-service-sdk]: https://github.com/Azure/azure-iot-sdk-python/tree/master/service
[lnk-certified]: https://catalog.azureiotsuite.com/
[lnk-iot-edge]: https://github.com/Azure/iot-edge

[lnk-dotnet-ref]: https://docs.microsoft.com/dotnet/api/microsoft.azure.devices
[lnk-c-ref]: https://azure.github.io/azure-iot-sdk-c/index.html
[lnk-java-ref]: https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device
[lnk-node-ref]: https://azure.github.io/azure-iot-sdk-node/
[lnk-rest-ref]: https://docs.microsoft.com/rest/api/iothub/
[lnk-java-service-ref]: https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.service.auth
[lnk-node-service-ref]: https://azure.github.io/azure-iot-sdk-node/
[lnk-gateway-ref]: http://azure.github.io/iot-edge/api_reference/c/html/

[lnk-devguide-endpoints]: iot-hub-devguide-endpoints.md
[lnk-devguide-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-devguide-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
