---
title: "Administración de mensajería entre dispositivos y la nube de Azure IoT Hub con iothub-explorer | Microsoft Docs"
description: Aprenda a utilizar la herramienta de la CLI iothub-explorer para supervisar los mensajes de dispositivo a nube (D2C) y enviar mensajes de nube a dispositivo (C2D) en Azure IoT Hub.
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "iothub explorer, mensajería de dispositivo a nube, nube a dispositivo de iot hub, mensajería de nube a dispositivo"
ms.assetid: 04521658-35d3-4503-ae48-51d6ad3c62cc
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/24/2017
ms.author: xshi
ms.openlocfilehash: 30151b7bdc544bc36e959cc3528d37897198fc7e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="use-iothub-explorer-to-send-and-receive-messages-between-your-device-and-iot-hub"></a>Uso de iothub-explorer para enviar y recibir mensajes entre el dispositivo y la instancia de IoT Hub

![Diagrama integral](media/iot-hub-get-started-e2e-diagram/2.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

[iothub-explorer](https://github.com/azure/iothub-explorer) tiene una serie de comandos que facilitan la administración de IoT Hub. Este tutorial se centra en cómo utilizar iothub-explorer para enviar y recibir mensajes entre el dispositivo y el IoT Hub.

## <a name="what-you-will-learn"></a>Lo qué aprenderá

Aprenderá a utilizar iothub-explorer para supervisar los mensajes del dispositivo a la nube y enviar mensajes de la nube al dispositivo. Los mensajes del dispositivo a la nube podrían ser datos de sensor que el dispositivo recopila y, a continuación, envía al IoT Hub. Los mensajes de la nube al dispositivo podrían ser comandos que el IoT Hub envía al dispositivo para hacer parpadear un LED que está conectado al dispositivo.

## <a name="what-you-will-do"></a>Lo que hará

- Utilizará iothub-explorer para supervisar los mensajes del dispositivo a la nube.
- Utilizará iothub-explorer para enviar mensajes de la nube al dispositivo.

## <a name="what-you-need"></a>Lo que necesita

- Tutorial [Instalación de su dispositivo](iot-hub-raspberry-pi-kit-node-get-started.md) completado donde se abordan los siguientes requisitos:
  - Una suscripción de Azure activa.
  - Un centro de Azure IoT en su suscripción.
  - Una aplicación cliente que envía mensajes a su centro de Azure IoT.
- iothub-explorer ([instalación de iothub-explorer](https://github.com/azure/iothub-explorer)).

## <a name="monitor-device-to-cloud-messages"></a>Supervisión de mensajes de dispositivo a nube

Para supervisar los mensajes que se envían desde el dispositivo al IoT Hub, siga estos pasos:

1. Abra una ventana de la consola.
1. Ejecute el siguiente comando:

   ```bash
   iothub-explorer monitor-events <device-id> --login "<IoTHubConnectionString>"
   ```

   > [!Note]
   > Obtenga `<device-id>` y `<IoTHubConnectionString>` desde el IoT Hub. Asegúrese de haber finalizado el tutorial anterior. O bien, puede intentar usar `iothub-explorer monitor-events <device-id> --login "HostName=<my-hub>.azure-devices.net;SharedAccessKeyName=<my-policy>;SharedAccessKey=<my-policy-key>"` si tiene `HostName`, `SharedAccessKeyName` y `SharedAccessKey`.

## <a name="send-cloud-to-device-messages"></a>Envío de mensajes de nube a dispositivo

Para enviar un mensaje desde el IoT Hub al dispositivo, siga estos pasos:

1. Abra una ventana de la consola.
1. Inicie una sesión en su IoT Hub mediante la ejecución del siguiente comando:

   ```bash
   iothub-explorer login `<IoTHubConnectionString>`
   ```

1. Envíe un mensaje al dispositivo mediante la ejecución del siguiente comando:

   ```bash
   iothub-explorer send <device-id> <message>
   ```

El comando hace parpadear el LED que está conectado al dispositivo y envía el mensaje a este.

> [!Note]
> No es necesario que el dispositivo envíe un comando de confirmación independiente al IoT Hub al recibir el mensaje.

## <a name="next-steps"></a>Pasos siguientes

Ahora sabe cómo supervisar los mensajes del dispositivo a la nube y enviar mensajes de la nube al dispositivo entre el dispositivo de IoT y la instancia de Azure IoT Hub.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
