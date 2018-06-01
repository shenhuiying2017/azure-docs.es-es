---
title: Conectividad de dispositivos en Azure IoT Central | Microsoft Docs
description: En este artículo se presentan conceptos clave relacionados con la conectividad de dispositivos en Azure IoT Central
services: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 11/30/2017
ms.topic: conceptual
ms.prod: microsoft-iot-central
manager: timlt
ms.openlocfilehash: 00b621a4635ef1ceda26772ac5876fa2599b56f8
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/16/2018
ms.locfileid: "34202652"
---
# <a name="device-connectivity-in-azure-iot-central"></a>Conectividad de dispositivos en Azure IoT Central

En este artículo se presentan conceptos clave relacionados con la conectividad de dispositivos en Microsoft Azure IoT Central.

Todos los dispositivos que se conectan a la aplicación Azure IoT Central, se conectan a [puntos de conexión](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-endpoints) que expone la instancia de IoT Hub que utiliza Azure IoT Central. IoT Hub permite conexiones escalables, seguras y confiables de los dispositivos conectados.

## <a name="sdk-support"></a>Compatibilidad con SDK

Los SDK de dispositivos Azure ofrecen la forma más fácil de implementar el código en los dispositivos que se conecta a la aplicación Azure IoT Central. En la actualidad, están disponibles los siguientes SDK:

- [SDK de Azure IoT para C](https://github.com/azure/azure-iot-sdk-c)
- [SDK de Azure IoT para Python](https://github.com/azure/azure-iot-sdk-python)
- [SDK de Azure IoT para Node.js](https://github.com/azure/azure-iot-sdk-node)
- [SDK de Azure IoT para Java](https://github.com/azure/azure-iot-sdk-java)
- [SDK de Azure IoT para .NET](https://github.com/azure/azure-iot-sdk-csharp)

Cada dispositivo se conecta mediante una cadena de conexión única que identifica al dispositivo. Un dispositivo solo puede conectarse al centro de IoT donde está registrado. Cuando se crea un dispositivo real en la aplicación Azure IoT Central, esta genera una cadena de conexión para que se utilice.

## <a name="sdk-features-and-iot-hub-connectivity"></a>Características del SDK y la conectividad de IoT Hub

Toda comunicación del dispositivo con la instancia de IoT Hub utiliza las siguientes opciones de conectividad:

- [Mensajería del dispositivo a la nube](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c)
- [Dispositivos gemelos](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-device-twins)

En la tabla siguiente se resume cómo las características de los dispositivos de Azure IoT Central se asignan a las características de IoT Hub:

| Azure IoT Central | Azure IoT Hub |
| ----------- | ------- |
| Medida: telemetría | Mensajería de un dispositivo a la nube |
| Propiedades del dispositivo | Propiedades notificadas de dispositivos gemelos |
| Settings | Propiedades deseadas y notificadas de dispositivos gemelos |

Para más información sobre el uso de los SDK de dispositivos, consulte cualquiera de los siguientes artículos para ver código de ejemplo:

- [Conexión de un cliente de Node.js genérico a una aplicación de Azure IoT Central](howto-connect-nodejs.md)
- [Conexión de un dispositivo Raspberry Pi a su aplicación de Azure IoT Central](howto-connect-raspberry-pi-python.md)
- [Conexión de un dispositivo de DevKit a su aplicación de Azure IoT Central](howto-connect-devkit.md)

El siguiente vídeo ofrece información general sobre cómo conectar un dispositivo real a Azure IoT Central:

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Connect-real-devices-to-Microsoft-IoT-Central/Player]

## <a name="protocols"></a>Protocolos

Los SDK de dispositivo admiten los siguientes protocolos de red para conectarse a un centro de IoT:

- MQTT
- AMQP
- HTTPS

Para obtener información sobre estos protocolos de diferencia y guía sobre cómo elegir uno, consulte [Elección de un protocolo de comunicación](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-protocols).

Si el dispositivo no puede usar ninguno de los protocolos admitidos, utilice Azure IoT Edge para realizar la conversión de protocolos. IoT Edge es compatible con otros escenarios de inteligencia perimetral para descargar el procesamiento al perímetro desde la aplicación Azure IoT Central.

## <a name="security"></a>Seguridad

Todos los datos intercambiados entre los dispositivos y la instancia de Azure IoT Central están cifrados. IoT Hub autentica todas las solicitudes de un dispositivo que se conecta a cualquiera de los puntos de conexión de IoT Hub orientados al dispositivo. Para evitar el intercambio de credenciales por la red, el dispositivo utiliza tokens firmados para autenticarse. Para más información, consulte [Control del acceso a IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security).

> [!NOTE]
> Actualmente, los dispositivos que se conectan a Azure IoT Central deben usar tokens SAS. Los certificados X.509 no son compatibles con los dispositivos que se conectan a Azure IoT Central.

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha aprendido sobre la conectividad de dispositivos en Azure IoT Central, estos son los siguientes pasos sugeridos:

- [Preparación y conexión de un dispositivo DevKit](howto-connect-devkit.md)
- [Preparación y conexión de una instancia de Raspberry Pi](howto-connect-raspberry-pi-python.md)
- [Conexión de un cliente de Node.js genérico a una aplicación de Azure IoT Central](howto-connect-nodejs.md)
