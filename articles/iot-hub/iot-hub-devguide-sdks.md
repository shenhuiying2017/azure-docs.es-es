---
title: "Guía del desarrollador: SDK IoT de Azure | Microsoft Docs"
description: "Guía del desarrollador de Azure IoT Hub: Información y vínculos a los diversos SDK de dispositivos y servicios IoT de Azure."
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
ms.date: 09/30/2016
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: c18a1b16cb561edabd69f17ecebedf686732ac34
ms.openlocfilehash: 58dc546ee2db9a0925ecf0fc074795b531323e03


---
# <a name="azure-iot-sdks"></a>SDK IoT de Azure
## <a name="azure-iot-device-sdk"></a>SDK de dispositivos IoT de Azure
Los SDK de dispositivos IoT de Microsoft Azure contienen código que facilita la creación dispositivos y aplicaciones que se conectan a servicios del Centro de IoT de Azure y que este administra.

Los siguientes SDK de dispositivos IoT de Azure están disponibles para su descarga desde GitHub:

* [SDK de dispositivos IoT de Azure para C][lnk-c-device-sdk] escrito en ANSI C (C99) para portabilidad y amplia compatibilidad de plataformas.
* [SDK de dispositivos IoT de Azure para .NET][lnk-dotnet-device-sdk]
* [SDK de dispositivos IoT de Azure para Java][lnk-java-device-sdk]
* [SDK de dispositivos IoT de Azure para Node.js][lnk-node-device-sdk]
* [SDK de dispositivos IoT de Microsoft Azure para Python 2.7][lnk-python-device-sdk]

> [!NOTE]
> Consulte los archivos Léame en los repositorios de GitHub para obtener información sobre el uso de administradores de paquetes específicos de la plataforma y el lenguaje para instalar los archivos binarios y dependencias en el equipo de desarrollo.
> 
> 

## <a name="os-platforms-and-hardware-compatibility"></a>Compatibilidad de hardware y de plataformas de sistema operativo
Para más información sobre la compatibilidad del SDK con dispositivos de hardware específicos, consulte [Catálogo de dispositivos de Azure Certified for IoT][lnk-certified].

## <a name="azure-iot-service-sdk"></a>SDK de servicios IoT de Azure
Los SDK de servicios IoT de Azure contienen código que facilitan la creación de aplicaciones que interactúan directamente con IoT Hub para administrar los dispositivos y la seguridad.

Los siguientes SDK de servicios IoT de Azure están disponibles para su descarga desde GitHub:

* [SDK de servicios IoT de Azure para .NET][lnk-dotnet-service-sdk]
* [SDK de servicios IoT de Azure para Node.js][lnk-node-service-sdk]
* [SDK de servicios IoT de Azure para Java][lnk-java-service-sdk]

> [!NOTE]
> Consulte los archivos Léame en los repositorios de GitHub para obtener información sobre el uso de administradores de paquetes específicos de la plataforma y el lenguaje para instalar los archivos binarios y dependencias en el equipo de desarrollo.
> 
> 

## <a name="azure-iot-gateway-sdk"></a>SDK de puerta de enlace IoT de Azure
Este SDK de puerta de enlace IoT de Azure contiene la infraestructura y los módulos necesarios para crear soluciones de puerta de enlace IoT. Puede ampliar este SDK para crear puertas de enlace aptas para cualquier escenario de un extremo a otro.

Puede descargar el [SDK de puerta de enlace de IoT de Azure][lnk-gateway-sdk] desde GitHub.

## <a name="online-api-reference-documentation"></a>Documentación de referencia de la API en línea
La siguiente es una lista de vínculos a documentación en línea de referencia de API para bibliotecas de dispositivo, servicio y puerta de enlace IoT de Azure:

* [.NET de Internet de las cosas (IoT)][lnk-dotnet-ref]
* [REST de IoT Hub][lnk-rest-ref]
* [SDK de dispositivo IoT de Azure para C][lnk-c-ref]
* [SDK de dispositivo IoT de Azure para Java][lnk-java-ref]
* [SDK de servicios IoT de Azure para Java][lnk-java-service-ref]
* [SDK de dispositivo IoT de Azure para Node.js][lnk-node-ref]
* [SDK de servicios IoT de Azure para Node.js][lnk-node-service-ref]
* [SDK de puerta de enlace de IoT de Azure][lnk-gateway-ref]

## <a name="next-steps"></a>Pasos siguientes
Otros temas de referencia en la Guía del desarrollador de IoT Hub son:

* [IoT Hub endpoints][lnk-devguide-endpoints] (Puntos de conexión de IoT Hub)
* [Lenguaje de consulta de IoT Hub para dispositivos gemelos y trabajos][lnk-devguide-query]
* [Cuotas y limitación][lnk-devguide-quotas]
* [Compatibilidad con MQTT de IoT Hub][lnk-devguide-mqtt]

<!-- Links and images -->

[lnk-c-device-sdk]: https://github.com/Azure/azure-iot-sdks/blob/master/c/readme.md
[lnk-dotnet-device-sdk]: https://github.com/Azure/azure-iot-sdks/blob/master/csharp/device/readme.md
[lnk-java-device-sdk]: https://github.com/Azure/azure-iot-sdks/blob/master/java/device/readme.md
[lnk-dotnet-service-sdk]: https://github.com/Azure/azure-iot-sdks/blob/master/csharp/service/README.md
[lnk-java-service-sdk]: https://github.com/Azure/azure-iot-sdks/blob/master/java/service/readme.md
[lnk-node-device-sdk]: https://github.com/Azure/azure-iot-sdks/blob/master/node/device/readme.md
[lnk-node-service-sdk]: https://github.com/Azure/azure-iot-sdks/blob/master/node/service/README.md
[lnk-python-device-sdk]: https://github.com/Azure/azure-iot-sdks/blob/master/python/device/readme.md
[lnk-certified]: https://catalog.azureiotsuite.com/
[lnk-gateway-sdk]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/README.md

[lnk-dotnet-ref]: https://msdn.microsoft.com/library/mt488521.aspx
[lnk-c-ref]: http://azure.github.io/azure-iot-sdks/c/api_reference/index.html
[lnk-java-ref]: http://azure.github.io/azure-iot-sdks/java/device/api_reference/index.html
[lnk-node-ref]: http://azure.github.io/azure-iot-sdks/node/api_reference/azure-iot-device/1.0.15/index.html
[lnk-rest-ref]: https://msdn.microsoft.com/library/mt548492.aspx
[lnk-java-service-ref]: http://azure.github.io/azure-iot-sdks/java/service/api_reference/index.html
[lnk-node-service-ref]: http://azure.github.io/azure-iot-sdks/node/api_reference/azure-iothub/1.0.17/index.html
[lnk-gateway-ref]: http://azure.github.io/azure-iot-gateway-sdk/api_reference/c/html/

[lnk-devguide-endpoints]: iot-hub-devguide-endpoints.md
[lnk-devguide-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-devguide-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md



<!--HONumber=Nov16_HO5-->


