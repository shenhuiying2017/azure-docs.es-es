---
title: "Conexión de Raspberry Pi (C) a Azure IoT: Lección 3: Table Storage | Microsoft Docs"
description: Supervise los mensajes del dispositivo a la nube a medida que se escriben en Azure Table Storage.
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: recuperar datos de la nube, servicio en la nube de iot
ms.assetid: 8c5558bb-3c31-4445-90e6-b1a978738545
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/28/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 64e69df256404e98f6175f77357500b562d74318
ms.openlocfilehash: 41e89f524bf9ae67489ab3a0eb09ceda6b211f15


---
# <a name="read-messages-persisted-in-azure-storage"></a>Lectura de los mensajes que se conservan en Azure Storage
## <a name="what-you-will-do"></a>Lo que hará
Va a supervisar los mensajes del dispositivo a la nube que se envían desde Raspberry Pi 3 a IoT Hub a medida que se escriben en Azure Table Storage. Si tiene problemas, busque soluciones en la [página de solución de problemas](iot-hub-raspberry-pi-kit-c-troubleshooting.md).

## <a name="what-you-will-learn"></a>Lo qué aprenderá
En este artículo, aprenderá a utilizar la tarea read-message de Gulp para leer los mensajes guardados en Azure Table Storage.

## <a name="what-you-need"></a>Lo que necesita
Antes de comenzar este procedimiento, debe de haber completado correctamente el tutorial sobre la [ejecución de una aplicación de ejemplo de Azure para activar el parpadeo en Raspberry Pi 3](iot-hub-raspberry-pi-kit-c-lesson3-run-azure-blink.md).

## <a name="read-new-messages-from-your-storage-account"></a>Lectura de mensajes nuevos de la cuenta de almacenamiento
En el artículo anterior, ejecutó una aplicación de ejemplo en Pi. La aplicación de ejemplo envío mensajes a IoT Hub de Azure. Los mensajes enviados a IoT Hub se almacenan en Azure Table Storage a través de Azure Function App. Para poder leer los mensajes de Azure Table Storage, necesita la cadena de conexión de Azure Storage.

Para leer los mensajes almacenados en Azure Table Storage, siga estos pasos:

1. Obtenga la cadena de conexión ejecutando el siguiente comando:

   ```bash
   az storage account list -g iot-sample --query [].name
   az storage account show-connection-string -g iot-sample -n {storage name}
   ```

   El primer comando recupera el elemento `storage name` que se utiliza en el segundo comando para obtener la cadena de conexión. Use `iot-sample` como valor de `{resource group name}`, si no lo modificó previamente.
2. Abra el archivo de configuración `config-raspberrypi.json` en Visual Studio Code con este comando:

   ```bash
   # For Windows command prompt
   code %USERPROFILE%\.iot-hub-getting-started\config-raspberrypi.json
   
   # For MacOS or Ubuntu
   code ~/.iot-hub-getting-started/config-raspberrypi.json
   ```
3. Reemplace `[Azure storage connection string]` por la cadena de conexión que obtuvo en el paso 1.
4. Guarde el archivo `config-raspberrypi.json`.
5. Vuelva a enviar los mensajes y léalos en Azure Table Storage mediante el siguiente comando:
   
   ```bash
   gulp run --read-storage
   ```
   
   La lógica para leer mensajes de Azure Table Storage se encuentra en el archivo `azure-table.js`.
   
   ![Ejecución de Gulp: almacenamiento de lectura](media/iot-hub-raspberry-pi-lessons/lesson3/gulp_read_message_c.png)

## <a name="summary"></a>Resumen
Ha conseguido conectar Pi a IoT Hub en la nube y utilizar la aplicación de ejemplo de activación del parpadeo para enviar mensajes del dispositivo a la nube. También ha utilizado Azure Function App para almacenar los mensajes entrantes de IoT Hub en Azure Table Storage. Ahora, puede enviar a Pi mensajes del dispositivo a la nube desde IoT Hub.

## <a name="next-steps"></a>Pasos siguientes
[Ejecución de una aplicación de ejemplo para recibir mensajes de nube a dispositivo](iot-hub-raspberry-pi-kit-c-lesson4-send-cloud-to-device-messages.md)




<!--HONumber=Jan17_HO4-->


