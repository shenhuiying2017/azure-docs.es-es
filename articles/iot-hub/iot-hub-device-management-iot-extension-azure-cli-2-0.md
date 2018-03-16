---
title: "Administración de dispositivos IoT de Azure con la extensión de IoT de la CLI de Azure 2.0 | Microsoft Docs"
description: "Use la extensión de IoT de la CLI de Azure 2.0 para la administración de dispositivos de Azure IoT Hub, herramienta que incluye métodos directos y opciones de administración de las propiedades deseadas de los dispositivos gemelos."
services: iot-hub
documentationcenter: 
author: chrissie926
manager: timlt
tags: 
keywords: "administración de dispositivos de azure iot, administración de dispositivos de azure iot hub, iot de administración de dispositivos, administración de dispositivos de iot hub"
ms.assetid: b34f799a-fc14-41b9-bf45-54751163fffe
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/16/2018
ms.author: menchi
ms.openlocfilehash: 07b9f14048b6618863efd5bd8eb8bcc8f52ec735
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/09/2018
---
# <a name="use-the-iot-extension-for-azure-cli-20-for-azure-iot-hub-device-management"></a>Extensión de IoT de la CLI de Azure 2.0 para la administración de dispositivos de Azure IoT Hub

![Diagrama integral](media/iot-hub-get-started-e2e-diagram/2.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

[La extensión de IoT de la CLI de Azure 2.0](https://github.com/Azure/azure-iot-cli-extension) es una nueva extensión de IoT de código abierto que se agrega a las funcionalidades de la [CLI de Azure 2.0](https://docs.microsoft.com/en-us/cli/azure?view=azure-cli-latest) e incluye comandos para interactuar con Azure Resource Manager y los puntos de conexión de administración. La CLI de Azure 2.0 incluye comandos para interactuar con Azure Resource Manager y los puntos de conexión de administración. Por ejemplo, la puede utilizar para crear una máquina virtual de Azure o una instancia de IoT Hub. Una extensión de la CLI permite que un servicio de Azure aumente la CLI de Azure para proporcionar al usuario acceso a funcionalidades adicionales específicas de un servicio. La extensión de IoT proporciona a los desarrolladores de IoT acceso con la línea de comandos a todas las funcionalidades de IoT Hub, IoT Edge y del servicio IoT Hub Device Provisioning.

| Opción de administración          | Task                                                                                                                            |
|----------------------------|---------------------------------------------------------------------------------------------------------------------------------|
| Métodos directos             | Hacer que un dispositivo actúe, por ejemplo, para iniciar o detener el envío de mensajes o reiniciar el dispositivo.                                        |
| Propiedades deseadas de dispositivos gemelos    | Poner un dispositivo en determinados estados, como establecer un indicador LED en verde o establecer el intervalo de envío de telemetría en 30 minutos.         |
| Propiedades notificadas de dispositivos gemelos   | Obtener el estado notificado de un dispositivo. Por ejemplo, el dispositivo informa de que el LED está parpadeando.                                    |
| Etiquetas de dispositivos gemelos                  | Almacenar metadatos específicos de dispositivo en la nube. Por ejemplo, la ubicación de implementación de una máquina expendedora.                         |
| Consultas de dispositivos gemelos        | Consultar todos los dispositivos gemelos para recuperar aquellos con condiciones arbitrarias como, por ejemplo, identificar los dispositivos que están disponibles para su uso. |

Para obtener una explicación más detallada acerca de las diferencias y orientación sobre el uso de estas opciones, consulte la [Guía de comunicación de dispositivo a nube](iot-hub-devguide-d2c-guidance.md) y la [Guía de comunicación de nube a dispositivo](iot-hub-devguide-c2d-guidance.md).

> [!NOTE]
> Los dispositivos gemelos son documentos JSON que almacenan información sobre el estado de los dispositivos (metadatos, configuraciones y condiciones). IoT Hub conserva un dispositivo gemelo por cada dispositivo que se conecta a él. Para más información acerca de los dispositivos gemelos, consulte [Introducción a los dispositivos gemelos](iot-hub-node-node-twin-getstarted.md).

## <a name="what-you-learn"></a>Conocimientos que adquirirá

Aprenderá a usar la extensión de IoT de la CLI de Azure 2.0 con distintas opciones de administración en la máquina de desarrollo.

## <a name="what-you-do"></a>Qué debe hacer

Deberá ejecutar la CLI de Azure 2.0 y la extensión de IoT de la CLI de Azure 2.0 con varias opciones de administración.

## <a name="what-you-need"></a>Lo que necesita

- Tutorial [Instalación de su dispositivo](iot-hub-raspberry-pi-kit-node-get-started.md) completado donde se abordan los siguientes requisitos:
  - Una suscripción de Azure activa.
  - Un centro de Azure IoT en su suscripción.
  - Una aplicación cliente que envía mensajes a su centro de Azure IoT.

- Asegúrese de que el dispositivo se está ejecutando con la aplicación de cliente durante este tutorial.

- [Python 2.7x o Python 3.x](https://www.python.org/downloads/)

- Instale la CLI de Azure 2.0. Una manera sencilla de instalarla en Windows es descargar e instalar [MSI](https://aka.ms/InstallAzureCliWindows). También puede seguir las instrucciones de instalación de [Microsoft Docs](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest) para instalar la CLI de Azure 2.0 en su entorno. La versión mínima de la CLI de Azure 2.0 debe ser la 2.0.24. Use `az –version` para asegurarse. 

- Instale la extensión de IoT. La manera más sencilla es ejecutar `az extension add --name azure-cli-iot-ext`. En el [archivo Léame de la extensión de IoT](https://github.com/Azure/azure-iot-cli-extension/blob/master/README.md) se describen varias maneras de instalarla.


## <a name="log-in-to-your-azure-account"></a>Inicie sesión en la cuenta de Azure.

Inicie sesión en la cuenta de Azure mediante el siguiente comando:

```bash
az login
```

## <a name="direct-methods"></a>Métodos directos

```bash
az iot hub invoke-device-method --device-id <your device id> --hub-name <your hub name> --method-name <the method name> --method-payload <the method payload>
```

## <a name="device-twin-desired-properties"></a>Propiedades deseadas de dispositivos gemelos

Establezca un intervalo de propiedad deseada = 3000 mediante la ejecución del comando siguiente:

```bash
az iot hub device-twin update -n <your hub name> -d <your device id> --set properties.desired.interval = 3000
```

Esta propiedad se puede leer desde el dispositivo.

## <a name="device-twin-reported-properties"></a>Propiedades notificadas de dispositivos gemelos

Obtenga las propiedades notificadas del dispositivo mediante la ejecución del comando siguiente:

```bash
az iot hub device-twin update -n <your hub name> -d <your device id> --set properties.reported.interval = 3000
```

Una de las propiedades es $metadata.$lastUpdated que muestra la última vez que este dispositivo envía o recibe un mensaje.

## <a name="device-twin-tags"></a>Etiquetas de dispositivos gemelos

Muestre las etiquetas y las propiedades del dispositivo mediante la ejecución del comando siguiente:

```bash
az iot hub device-twin show --hub-name <your hub name> --device-id <your device id>
```

Agregue un rol de campo = temperatura y humedad al dispositivo mediante la ejecución del comando siguiente:

```bash
az iot hub device-twin update --hub-name <your hub name> --device-id <your device id> --set tags = '{"role":"temperature&humidity"}}'
```

## <a name="device-twin-queries"></a>Consultas de dispositivos gemelos

Consulte los dispositivos con una etiqueta de rol = "temperatura y humedad" mediante la ejecución del comando siguiente:

```bash
az iot hub query --hub-name <your hub name> --query-command "SELECT * FROM devices WHERE tags.role = 'temperature&humidity'"
```

Consulte todos los dispositivos excepto aquellos con una etiqueta de rol = "temperatura y humedad" mediante la ejecución del comando siguiente:

```bash
az iot hub query --hub-name <your hub name> --query-command "SELECT * FROM devices WHERE tags.role != 'temperature&humidity'"
```

## <a name="next-steps"></a>Pasos siguientes

Ahora sabe cómo supervisar los mensajes del dispositivo a la nube y enviar mensajes de la nube al dispositivo entre el dispositivo de IoT y la instancia de Azure IoT Hub.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]