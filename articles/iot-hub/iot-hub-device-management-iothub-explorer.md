---
title: "Administración de dispositivos de Azure IoT con iothub-explorer | Microsoft Docs"
description: "Use la herramienta de la CLI iothub-explorer para la administración de dispositivos de Azure IoT Hub, herramienta que incluye métodos directos y opciones de administración de las propiedades deseadas de los dispositivos gemelos."
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "administración de dispositivos de azure iot, administración de dispositivos de azure iot hub, iot de administración de dispositivos, administración de dispositivos de iot hub"
ms.assetid: b34f799a-fc14-41b9-bf45-54751163fffe
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/12/2017
ms.author: xshi
ms.openlocfilehash: 5b7a5057bdfb5920fbb5759bed1f5561cfa1d7e0
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2017
---
# <a name="use-iothub-explorer-for-azure-iot-hub-device-management"></a>Uso de iothub-explorer para la administración de dispositivos de Azure IoT Hub

![Diagrama integral](media/iot-hub-get-started-e2e-diagram/2.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

[iothub-explorer](https://github.com/azure/iothub-explorer) es una herramienta de la CLI que se ejecuta en un equipo host para administrar identidades de dispositivo en el registro de IoT Hub. Incluye opciones de administración que puede usar para realizar varias tareas.

| Opción de administración          | Tarea                                                                                                                            |
|----------------------------|---------------------------------------------------------------------------------------------------------------------------------|
| Métodos directos             | Hacer que un dispositivo actúe, por ejemplo, para iniciar o detener el envío de mensajes o reiniciar el dispositivo.                                        |
| Propiedades deseadas de dispositivos gemelos    | Poner un dispositivo en determinados estados, como establecer un indicador LED en verde o establecer el intervalo de envío de telemetría en 30 minutos.         |
| Propiedades notificadas de dispositivos gemelos   | Obtener el estado notificado de un dispositivo. Por ejemplo, el dispositivo informa de que el LED está parpadeando.                                    |
| Etiquetas de dispositivos gemelos                  | Almacenar metadatos específicos de dispositivo en la nube. Por ejemplo, la ubicación de implementación de una máquina expendedora.                         |
| Mensajes de nube a dispositivo   | Enviar notificaciones a un dispositivo. Por ejemplo, "Es muy probable que llueva hoy. No olvide traerse un paraguas".              |
| Consultas de dispositivos gemelos        | Consultar todos los dispositivos gemelos para recuperar aquellos con condiciones arbitrarias como, por ejemplo, identificar los dispositivos que están disponibles para su uso. |

Para obtener una explicación más detallada acerca de las diferencias y orientación sobre el uso de estas opciones, consulte la [Guía de comunicación de dispositivo a nube](iot-hub-devguide-d2c-guidance.md) y la [Guía de comunicación de nube a dispositivo](iot-hub-devguide-c2d-guidance.md).

> [!NOTE]
> Los dispositivos gemelos son documentos JSON que almacenan información sobre el estado de los dispositivos (metadatos, configuraciones y condiciones). IoT Hub conserva un dispositivo gemelo por cada dispositivo que se conecta a él. Para más información acerca de los dispositivos gemelos, consulte [Introducción a los dispositivos gemelos](iot-hub-node-node-twin-getstarted.md).

## <a name="what-you-learn"></a>Conocimientos que adquirirá

Aprenderá a usar iothub-explorer con distintas opciones de administración en su máquina de desarrollo.

## <a name="what-you-do"></a>Qué debe hacer

Ejecute iothub-explorer con distintas opciones de administración.

## <a name="what-you-need"></a>Lo que necesita

- Tutorial [Instalación de su dispositivo](iot-hub-raspberry-pi-kit-node-get-started.md) completado donde se abordan los siguientes requisitos:
  - Una suscripción de Azure activa.
  - Un centro de Azure IoT en su suscripción.
  - Una aplicación cliente que envía mensajes a su centro de Azure IoT.
- Asegúrese de que el dispositivo se está ejecutando con la aplicación de cliente durante este tutorial.
- iothub-explorer, [instale iothub-explorer](https://github.com/azure/iothub-explorer) en la máquina de desarrollo.

## <a name="connect-to-your-iot-hub"></a>Conexión a IoT Hub

Conéctese a su instancia de IoT Hub mediante la ejecución del siguiente comando:

```bash
iothub-explorer login <your IoT hub connection string>
```

## <a name="use-iothub-explorer-with-direct-methods"></a>Uso de iothub-explorer con métodos directos

Invoque el método `start` en la aplicación de dispositivo para enviar mensajes a su instancia de IoT Hub mediante la ejecución del comando siguiente:

```bash
iothub-explorer device-method <your device Id> start
```

Invoque el método `stop` en la aplicación de dispositivo para dejar de enviar mensajes a su instancia de IoT Hub mediante la ejecución del comando siguiente:

```bash
iothub-explorer device-method <your device Id> stop
```

## <a name="use-iothub-explorer-with-twins-desired-properties"></a>Use iothub-explorer con las propiedades deseadas de los dispositivos gemelos

Establezca un intervalo de propiedad deseada = 3000 mediante la ejecución del comando siguiente:

```bash
iothub-explorer update-twin <your device id> {\"properties\":{\"desired\":{\"interval\":3000}}}
```

El dispositivo puede leer esta propiedad.

## <a name="use-iothub-explorer-with-twins-reported-properties"></a>Use iothub-explorer con las propiedades notificadas de los dispositivos gemelos

Obtenga las propiedades notificadas del dispositivo mediante la ejecución del comando siguiente:

```bash
iothub-explorer get-twin <your device id>
```

Una de las propiedades es $metadata.$lastUpdated que muestra la última vez que este dispositivo envía o recibe un mensaje.

## <a name="use-iothub-explorer-with-twins-tags"></a>Uso de iothub-explorer con etiquetas de dispositivos gemelos

Muestre las etiquetas y las propiedades del dispositivo mediante la ejecución del comando siguiente:

```bash
iothub-explorer get-twin <your device id>
```

Agregue un rol de campo = temperatura y humedad al dispositivo mediante la ejecución del comando siguiente:

```bash
iothub-explorer update-twin <your device id> "{\"tags\":{\"role\":\"temperature&humidity\"}}"

```

## <a name="use-iothub-explorer-with-cloud-to-device-messages"></a>Uso de iothub-explorer con mensajes de la nube al dispositivo

Envíe un mensaje "Hello World" al dispositivo mediante la ejecución del siguiente comando:

```bash
iothub-explorer send <device-id> "Hello World"
```

Consulte [Uso de iothub-explorer para enviar y recibir mensajes entre el dispositivo y la instancia de IoT Hub](iot-hub-explorer-cloud-device-messaging.md) para ver un escenario real de uso de este comando.

## <a name="use-iothub-explorer-with-device-twins-queries"></a>Uso de iothub-explorer con consultas de dispositivos gemelos

Consulte los dispositivos con una etiqueta de rol = "temperatura y humedad" mediante la ejecución del comando siguiente:

```bash
iothub-explorer query-twin "SELECT * FROM devices WHERE tags.role = 'temperature&humidity'"
```

Consulte todos los dispositivos excepto aquellos con una etiqueta de rol = "temperatura y humedad" mediante la ejecución del comando siguiente:

```bash
iothub-explorer query-twin "SELECT * FROM devices WHERE tags.role != 'temperature&humidity'"
```

## <a name="next-steps"></a>Pasos siguientes

Ha aprendido a usar iothub-explorer con distintas opciones de administración.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
