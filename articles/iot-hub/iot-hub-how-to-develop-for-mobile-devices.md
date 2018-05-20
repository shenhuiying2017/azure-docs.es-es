---
title: Desarrollar contenido para dispositivos móviles mediante los SDK de Azure IoT | Microsoft Docs
description: 'Guía para desarrolladores: aprenda a desarrollar contenido para dispositivos móviles mediante los SDK de Azure IoT Hub.'
services: iot-hub
documentationcenter: ''
author: yzhong94
manager: timlt
editor: ''
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/16/2018
ms.author: yizhon
ms.openlocfilehash: 824d577a4709a388bd858bdda6ef5f11f53dde58
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/01/2018
---
# <a name="develop-for-mobile-devices-using-azure-iot-sdks"></a>Desarrollar contenido para dispositivos móviles mediante los SDK de Azure IoT

El contenido que puede encontrar en Internet de las cosas puede hacer referencia a una amplia gama de dispositivos con distintas capacidades: sensores, microcontroladores, dispositivos inteligentes, puertas de enlace industriales e incluso dispositivos móviles.  Un dispositivo móvil puede ser un dispositivo de IoT, donde se envíe telemetría del dispositivo a la nube y que administre la nube.  Asimismo, también puede ser el dispositivo que se encargue de ejecutar una aplicación de servicio de back-end, para administrar otros dispositivos de IoT.  En ambos casos, los [SDK de Azure IoT Hub][lnk-sdk-overview] se pueden usar para desarrollar aplicaciones que funcionen en dispositivos móviles.  

## <a name="develop-for-native-ios-platform"></a>Desarrollar contenido para la plataforma nativa iOS

Los SDK de Azure IoT Hub proporcionan compatibilidad con la plataforma de iOS nativa a través del SDK de C de Azure IoT Hub.  Se puede considerar como un SDK de iOS que se puede incorporar en el proyecto de Swift u Objective C XCode.  Hay dos maneras de utilizar el SDK de C en iOS:
- Usar las bibliotecas de CocoaPod en el proyecto de XCode directamente.  
- Descargar el código fuente para el SDK de C y la compilación para la plataforma de iOS referentes a las [instrucciones de compilación][lnk-c-devbox] para MacOS.  

El SDK de C de Azure IoT Hub se escribe en C99 para obtener la máxima portabilidad en diferentes plataformas.  El proceso de portabilidad implica la escritura de una fina capa de adopción para los componentes específicos de la plataforma que se pueden encontrar aquí para [iOS][lnk-ios-pal].  Se pueden aprovechar las características del SDK de C en la plataforma de iOS, incluyendo las primitivas de Azure IoT Hub admitidas y las características específicas del SDK como la directiva de reintentos para la confiabilidad de la red.  La interfaz del SDK para iOS también es similar a la interfaz del SDK de C para Azure IoT Hub.  

Estos documentos le indicarán cómo desarrollar una aplicación de dispositivo o de servicio en dispositivo de iOS:
- [Quickstart: Send telemetry from a device to an IoT hub][lnk-device-ios-quickstart] (Inicio rápido: enviar telemetría desde un dispositivo a IoT Hub)  
- [Send messages from the cloud to your device with IoT hub][lnk-service-ios-quickstart] (Enviar mensajes desde la nube al dispositivo con IoT Hub)  

### <a name="develop-with-azure-iot-hub-cocoapod-libraries"></a>Desarrollar bibliotecas de CocoaPod con Azure IoT Hub

Los SDK de Azure IoT Hub liberan un conjunto de bibliotecas de CocoaPod del proyecto Objective-C para desarrollar contenido de iOS.  Para ver la lista más reciente de bibliotecas de CocoaPod, consulte [CocoaPods for Microsoft Azure IoT][lnk-cocoapod-list] (Bibliotecas de CocoaPod para Microsoft Azure IoT).  Una vez que se incorporen las bibliotecas relevantes en el proyecto de XCode, hay dos maneras de escribir el código relacionado con IoT Hub:
- Función de Objective C: si el proyecto se ha escrito en Objective-C, se pueden llamar a las API del SDK de C de Azure IoT Hub directamente.  Si el proyecto está escrito en Swift, puede llamar a ``@objc func`` antes de crear la función y escribir todas las lógicas relacionadas con Azure IoT Hub mediante el código de C u Objective-C.  Puede encontrar un conjunto de ejemplos que muestran ambos elementos en el [repositorio de ejemplos][lnk-ios-samples-repo].  
- Ejemplos de Incorporate C: si ha escrito una aplicación de dispositivo de C, puede hacer referencia a esa aplicación directamente en el proyecto de XCode:
    - Agregue el archivo sample.c al proyecto de XCode desde XCode.  
    - Agregue el archivo de encabezado a la dependencia.  Un archivo de encabezado se incluye en el [repositorio de ejemplo][lnk-ios-samples-repo] como ejemplo.  Para obtener más información, visite la página de documentación de Apple referente a [Objective-C](https://developer.apple.com/documentation/objectivec).

## <a name="next-steps"></a>Pasos siguientes
* [Referencia de la API REST de IoT Hub][lnk-rest-ref]
* [Código fuente del SDK de C para Azure IoT][lnk-c-sdk]

[lnk-sdk-overview]: https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks
[lnk-c-devbox]: https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md
[lnk-device-ios-quickstart]:https://docs.microsoft.com/azure/iot-hub/quickstart-send-telemetry-ios
[lnk-service-ios-quickstart]: https://docs.microsoft.com/azure/iot-hub/iot-hub-ios-swift-c2d
[lnk-cocoapod-list]: https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/samples/ios/CocoaPods.md
[lnk-ios-samples-repo]: https://github.com/Azure-Samples/azure-iot-samples-ios
[lnk-ios-pal]: https://github.com/Azure/azure-c-shared-utility/tree/master/pal/ios-osx
[lnk-c-sdk]: https://github.com/Azure/azure-iot-sdk-c
[lnk-rest-ref]: https://docs.microsoft.com/rest/api/iothub/
