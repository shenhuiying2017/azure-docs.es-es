---
title: "Administración de mensajería entre dispositivos y la nube de Azure IoT Hub con iothub-explorer | Microsoft Docs"
description: Aprenda a utilizar la herramienta de la CLI iothub-explorer para supervisar los mensajes de dispositivo a nube (D2C) y enviar mensajes de nube a dispositivo (C2D) en Azure IoT Hub.
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "iothub explorer, mensajería de dispositivo a nube, nube a dispositivo de iot hub, mensajería de nube a dispositivo"
ms.assetid: 04521658-35d3-4503-ae48-51d6ad3c62cc
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 432752c895fca3721e78fb6eb17b5a3e5c4ca495
ms.openlocfilehash: 8c25b8f558aae638a95d6e7186e19e77d02edbb2
ms.lasthandoff: 03/30/2017


---
# <a name="use-iothub-explorer-to-send-and-receive-messages-between-your-device-and-iot-hub"></a>Uso de iothub-explorer para enviar y recibir mensajes entre el dispositivo y la instancia de IoT Hub

> [!NOTE]
> Antes de empezar este tutorial, asegúrese de que ha completado los pasos para [conectar ESP8266 a Azure IoT Hub](/iot-hub-arduino-huzzah-esp8266-get-started.md). En dicho artículo para [conectar ESP8266 a Azure IoT Hub](/iot-hub-arduino-huzzah-esp8266-get-started.md), se configura el dispositivo de IoT y el centro de IoT y se implementa una aplicación de ejemplo para que se ejecute en el dispositivo. La aplicación envía los datos del sensor recopilados al centro de IoT.

[iothub-explorer](https://github.com/azure/iothub-explorer) tiene una serie de comandos que facilitan la administración de IoT Hub. Este tutorial se centra en cómo utilizar iothub-explorer para enviar y recibir mensajes entre el dispositivo y el centro de IoT.

## <a name="what-you-will-learn"></a>Lo qué aprenderá

Aprenderá a utilizar iothub-explorer para supervisar los mensajes del dispositivo a la nube y enviar mensajes de la nube al dispositivo. Los mensajes del dispositivo a la nube podrían ser datos de sensor que el dispositivo recopila y, a continuación, envía al centro de IoT. Los mensajes de la nube al dispositivo podrían ser comandos que el centro de IoT envía al dispositivo para hacer parpadear un LED que está conectado al dispositivo.

## <a name="what-you-will-do"></a>Lo que hará

- Utilizará iothub-explorer para supervisar los mensajes del dispositivo a la nube.
- Utilizará iothub-explorer para enviar mensajes de la nube al dispositivo.

## <a name="what-you-need"></a>Lo que necesita

- Tiene que haber completado el tutorial para [conectar ESP8266 a Azure IoT Hub](/iot-hub-arduino-huzzah-esp8266-get-started.md), que engloba los siguientes requisitos:
  - Una suscripción de Azure activa.
  - Un centro de Azure IoT en su suscripción.
  - Una aplicación cliente que envía mensajes a su centro de Azure IoT.
- iothub-explorer ([instalación de iothub-explorer](https://github.com/azure/iothub-explorer)).

## <a name="monitor-device-to-cloud-messages"></a>Supervisión de mensajes de dispositivo a nube

Para supervisar los mensajes que se envían desde el dispositivo al centro de IoT, siga estos pasos:

1. Abra una ventana de la consola.
1. Ejecute el siguiente comando:

   ```bash
   iothub-explorer monitor-events <device-id> --login <IoTHubConnectionString>
   ```

   > [!Note]
   > Obtenga `<device-id>` y `<IoTHubConnectionString>` desde el centro de IoT. Asegúrese de haber finalizado el tutorial anterior.

## <a name="send-cloud-to-device-messages"></a>Envío de mensajes de nube a dispositivo

Para enviar un mensaje desde el centro de IoT al dispositivo, siga estos pasos:

1. Abra una ventana de la consola.
1. Inicie una sesión en su centro de IoT mediante la ejecución del siguiente comando:

   ```bash
   iothub-explorer login <IoTHubConnectionString>
   ```

1. Envíe un mensaje al dispositivo mediante la ejecución del siguiente comando:

   ```bash
   iothub-explorer send <device-id> <message>
   ```

El comando hace parpadear el LED que está conectado al dispositivo y envía el mensaje a este.

> [!Note]
> No es necesario que el dispositivo envíe un comando de confirmación independiente al centro de IoT al recibir el mensaje.

## <a name="next-steps"></a>Pasos siguientes

Ahora sabe cómo supervisar los mensajes del dispositivo a la nube y enviar mensajes de la nube al dispositivo entre el dispositivo de IoT y la instancia de Azure IoT Hub.

Para continuar la introducción a IoT Hub y explorar otros escenarios de IoT, consulte:

- [Guardado de los mensajes de IoT Hub en el almacenamiento de datos de Azure](iot-hub-store-data-in-azure-table-storage.md)
