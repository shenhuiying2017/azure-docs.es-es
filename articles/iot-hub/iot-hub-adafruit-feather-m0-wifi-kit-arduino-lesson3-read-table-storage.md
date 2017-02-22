---
title: "Conexión de Arduino (C) a Azure IoT: Lección 3: Table Storage | Microsoft Docs"
description: Supervise los mensajes del dispositivo a la nube a medida que se escriben en Azure Table Storage.
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "datos en la nube, recopilación de datos en la nube, servicio en la nube de IoT, datos de IoT"
ms.assetid: 386083e0-0dbb-48c0-9ac2-4f8fb4590772
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/13/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 64e69df256404e98f6175f77357500b562d74318
ms.openlocfilehash: 7a49a15b00bf25c9416235f41da8cc86e54d0062


---
# <a name="read-messages-persisted-in-azure-storage"></a>Lectura de los mensajes que se conservan en Azure Storage
## <a name="what-you-will-do"></a>Lo que hará
Supervise los mensajes de dispositivo a nube que se envían desde la placa Adafruit Feather M0 WiFi Arduino a IoT Hub a medida que se escriben en Azure Table Storage.

Si tiene problemas, busque soluciones en [esta página][troubleshooting].

## <a name="what-you-will-learn"></a>Lo qué aprenderá
En este artículo, aprenderá a utilizar la tarea read-message de Gulp para leer los mensajes guardados en Azure Table Storage.

## <a name="what-you-need"></a>Lo que necesita
Antes de comenzar este procedimiento, debe de haber completado correctamente el tutorial sobre la [ejecución de una aplicación de ejemplo de Azure para activar el parpadeo en la placa de Arduino][run-blink-application].

## <a name="read-new-messages-from-your-storage-account"></a>Lectura de mensajes nuevos de la cuenta de almacenamiento
En el artículo anterior, ejecutó una aplicación de ejemplo en la placa de Arduino. La aplicación de ejemplo envío mensajes a IoT Hub de Azure. Los mensajes enviados a IoT Hub se almacenan en Azure Table Storage a través de Azure Function App. Para poder leer los mensajes de Azure Table Storage, necesita la cadena de conexión de Azure Storage.

Para leer los mensajes almacenados en Azure Table Storage, siga estos pasos:

1. Obtenga la cadena de conexión ejecutando el siguiente comando:

   ```bash
   az storage account list -g iot-sample --query [].name
   az storage account show-connection-string -g iot-sample -n {storage name}
   ```

   El primer comando recupera el elemento `storage name` que se utiliza en el segundo comando para obtener la cadena de conexión. Use `iot-sample` como valor de `{resource group name}`, si no lo modificó previamente.
2. Abra el archivo de configuración `config-arduino.json` en Visual Studio Code con este comando:

   ```bash
   # For Windows command prompt
   code %USERPROFILE%\.iot-hub-getting-started\config-arduino.json

   # For MacOS or Ubuntu
   code ~/.iot-hub-getting-started/config-arduino.json
   ```
3. Reemplace `[Azure storage connection string]` por la cadena de conexión que obtuvo en el paso 1.
4. Guarde el archivo `config-arduino.json`.
5. Vuelva a enviar los mensajes y léalos en Azure Table Storage mediante el siguiente comando:

   ```bash
   gulp run --read-storage

   # You can monitor the serial port by running listen task:
   gulp listen

   # Or you can combine above two gulp tasks into one:
   gulp run --read-storage --listen
   ```

   La lógica para leer mensajes de Azure Table Storage se encuentra en el archivo `azure-table.js`.

   ![Ejecución de Gulp: almacenamiento de lectura][gulp-run]

## <a name="summary"></a>Resumen
Ha conectado la placa de Arduino a IoT Hub en la nube y utilizado la aplicación de ejemplo de intermitencia correctamente para enviar mensajes de dispositivo a nube. También ha utilizado Azure Function App para almacenar los mensajes entrantes de IoT Hub en Azure Table Storage. Ahora, puede enviar a la placa de Arduino mensajes de dispositivo a nube desde IoT Hub.

## <a name="next-steps"></a>Pasos siguientes
[Envío de mensajes de nube a dispositivo][send-cloud-to-device-messages]
<!-- Images and links -->

[troubleshooting]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-troubleshooting.md
[run-blink-application]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson3-run-azure-blink.md
[gulp-run]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson3/gulp_read_message_arduino.png
[send-cloud-to-device-messages]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson4-send-cloud-to-device-messages.md


<!--HONumber=Jan17_HO4-->


