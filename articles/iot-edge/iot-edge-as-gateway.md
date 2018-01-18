---
title: Uso de un dispositivo de Azure IoT Edge como puerta de enlace para otros dispositivos | Microsoft Docs
description: "Use Azure IoT Edge para crear un dispositivo de puerta de enlace transparente, opaco o proxy que envíe datos desde varios dispositivos de nivel inferior a la nube y los procese localmente."
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 11/27/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 3f2f9258b97d4886f41a2b991ff4de7e16379245
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/04/2018
---
# <a name="how-an-iot-edge-device-can-be-used-as-a-gateway---preview"></a>Uso de un dispositivo IoT Edge como puerta de enlace (versión preliminar)

La finalidad de las puertas de enlace en las soluciones IoT es específica de la solución y combina la conectividad de los dispositivos con el análisis perimetral. Azure IoT Edge se puede usar para satisfacer todas las necesidades de una puerta de enlace de IoT con independencia de si están relacionadas con la conectividad, la identidad o los análisis perimetrales. Los patrones de puerta de enlace de este artículo únicamente hacen referencia a las características de la conectividad de los dispositivos de nivel inferior y la identidad de los dispositivos, y no a cómo los datos del dispositivo se procesan en la puerta de enlace.

## <a name="patterns"></a>Patrones
Existen tres patrones para usar un dispositivo IoT Edge como puerta de enlace: transparente, traducción del protocolo y traducción de la identidad:
* **Transparente**: los dispositivos que podrían conectarse teóricamente a IoT Hub pueden conectarse en su lugar a un dispositivo de puerta de enlace. Esto implica que los dispositivos de nivel inferior tienen sus propias identidades de IoT Hub y usan cualquiera de los protocolos MQTT, AMQP o HTTP. La puerta de enlace simplemente pasa las comunicaciones entre los dispositivos e IoT Hub. Los dispositivos no saben que se están comunicando con la nube a través de una puerta de enlace y el usuario que interactúa con los dispositivos en IoT Hub no sabe que hay un dispositivo de puerta de enlace intermedio. Por lo tanto, la puerta de enlace es transparente. Consulte el procedimiento [Creación de una puerta de enlace transparente][lnk-iot-edge-as-transparent-gateway] para obtener información específica sobre como usar un dispositivo IoT Edge como puerta de enlace transparente.
* **Traducción del protocolo**: los dispositivos que no admiten MQTT, AMQP o HTTP usan un dispositivo de puerta de enlace para enviar datos a IoT Hub. La puerta de enlace es lo suficientemente inteligente como para comprender ese protocolo utilizado por los dispositivos de nivel inferior; sin embargo, es el único dispositivo que tiene identidad en IoT Hub. Toda la información parece proceder de un dispositivo, la puerta de enlace. Como consecuencia, los dispositivos de nivel inferior deben insertar información de identificación adicional en sus mensajes si las aplicaciones de nube quieren razonar sobre los datos de cada dispositivo. Además, los primitivos de IoT Hub, como gemelos y métodos, solo están disponibles para el dispositivo de puerta de enlace, no para los dispositivos de nivel inferior.
* **Traducción de la identidad**: los dispositivos que no se pueden conectar a IoT Hub se conectan a un dispositivo de puerta de enlace que proporciona la traducción de la identidad y el protocolo de IoT Hub en nombre de los dispositivos de nivel inferior. La puerta de enlace es lo suficientemente inteligente como para comprender el protocolo utilizado por los dispositivos de nivel inferior, proporcionarles una identidad y traducir los primitivos de IoT Hub. Los dispositivos de nivel inferior aparecen en IoT Hub como dispositivos de primera clase con gemelos y métodos. Un usuario puede interactuar con los dispositivos IoT Hub sin saber que hay un dispositivo de puerta de enlace intermedio.

![Diagramas de patrones de puerta de enlace][1]

## <a name="use-cases"></a>Casos de uso
Todos los patrones de puerta de enlace proporcionan las siguientes ventajas:
* **Análisis perimetral**: usa los servicios de AI localmente para procesar los datos que proceden de dispositivos de nivel inferior sin enviar telemetría con total fidelidad a la nube. Busca y reacciona a la información localmente y solo envía un subconjunto de datos a IoT Hub. 
* **Aislamiento de dispositivos de nivel inferior**: el dispositivo de puerta de enlace puede proteger todos los dispositivos de nivel inferior de la exposición a Internet. Se puede situar entre una red de tecnología operativa (OT) que no tiene conectividad y una red de tecnología de la información (TI) que proporciona acceso a Internet. 
* **Multiplexación de conexiones**: todos los dispositivos que se conectan a IoT Hub a través de un dispositivo IoT Edge utilizarán la misma conexión subyacente.
* **Suavizado de tráfico**: el dispositivo IoT Edge implementará automáticamente un retroceso exponencial en caso de limitación de IoT Hub, al tiempo que se conservan los mensajes localmente. Esto hará que la solución sea resistente a los picos de tráfico.
* **Compatibilidad sin conexión limitada**: el dispositivo de puerta de enlace almacenará localmente los mensajes y las actualizaciones gemelas que no se puedan entregar a IoT Hub.

Una puerta de enlace que realiza la traducción del protocolo también puede realizar el análisis perimetral, el aislamiento de dispositivos, el suavizado de tráfico y la compatibilidad sin conexión en dispositivos nuevos y existentes con restricciones de recursos. Muchos dispositivos existentes producen datos que pueden favorecer el conocimiento del negocio; sin embargo, no están diseñados teniendo en mente la conectividad a la nube. Las puertas de enlace opacas permiten que estos datos se desbloqueen y utilicen en una solución IoT de un extremo a otro.

Una puerta de enlace que realiza la traducción de la identidad proporciona las ventajas de la traducción del protocolo y además permite una manejabilidad completa de los dispositivos de nivel inferior desde la nube. Todos los dispositivos de su solución IoT se muestran en IoT Hub con independencia del protocolo con el que se comunican.

## <a name="cheat-sheet"></a>Hoja de referencia rápida
Se trata de una hoja de referencia rápida que compara los primitivos de IoT Hub cuando se usan puertas de enlace transparentes, opacas y proxy.

| &nbsp; | Puerta de enlace transparente | Traducción del protocolo | Traducción de la identidad |
|--------|-------------|--------|--------|
| Identidades almacenadas en el registro de identidades de IoT Hub | Identidades de todos los dispositivos conectados | Solo la identidad del dispositivo de puerta de enlace | Identidades de todos los dispositivos conectados |
| Dispositivo gemelo | Cada dispositivo conectado tiene su propio dispositivo gemelo | Solo la puerta de enlace tiene dispositivo y un módulo gemelos | Cada dispositivo conectado tiene su propio dispositivo gemelo |
| Métodos directos y mensajes de nube a dispositivo | La nube puede tratar individualmente cada dispositivo conectado | La nube solo puede tratar el dispositivo de puerta de enlace | La nube puede tratar individualmente cada dispositivo conectado |
| [Limitaciones y cuotas de IoT Hub][lnk-iothub-throttles-quotas] | Aplicación a cada dispositivo | Aplicación al dispositivo de puerta de enlace | Aplicación a cada dispositivo |

Cuando se usa un patrón de puerta de enlace opaca (traducción de protocolo), todos los dispositivos que se conectan a través de esa puerta de enlace comparten la misma cola de nube a dispositivo, que puede contener 50 mensajes como máximo. De ello se desprende que el patrón de puerta de enlace opaca debe usarse solo cuando muy pocos dispositivos están conectados a través de cada puerta de enlace de campo y su tráfico de nube a dispositivo sea bajo.

## <a name="next-steps"></a>pasos siguientes
Usar un dispositivo IoT Edge como [puerta de enlace transparente][lnk-iot-edge-as-transparent-gateway] 

[lnk-iot-edge-as-transparent-gateway]: ./how-to-create-transparent-gateway.md
[lnk-iothub-throttles-quotas]: ../iot-hub/iot-hub-devguide-quotas-throttling.md

[1]: ./media/iot-edge-as-gateway/edge-as-gateway.png
