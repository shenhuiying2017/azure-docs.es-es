---
title: "Conexión de Intel Edison (Node) a Azure IoT: Lección 3: Supervisión de mensajes | Microsoft Docs"
description: Supervise los mensajes del dispositivo a la nube a medida que se escriben en Azure Table Storage.
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "datos en la nube, recopilación de datos en la nube, servicio en la nube de IoT, datos de IoT"
ms.assetid: fa2c7efe-7e34-4e39-bb70-015c15ac69ed
ms.service: iot-hub
ms.devlang: nodejs
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/8/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: adf5b10721a28432e6b37ef73c6a7e7ec9f93cdd
ms.openlocfilehash: 5100e6dbd74095f419c1017a3784fac547bf200b


---
# <a name="read-messages-persisted-in-azure-storage"></a>Lectura de los mensajes que se conservan en Azure Storage
## <a name="what-you-will-do"></a>Lo que hará
Va a supervisar los mensajes de dispositivo a nube que se envían desde Intel Edison a IoT Hub a medida que se escriben en Azure Table Storage. Si tiene problemas, busque soluciones en [esta página][troubleshooting].

## <a name="what-you-will-learn"></a>Lo qué aprenderá
En este artículo, aprenderá a utilizar la tarea read-message de Gulp para leer los mensajes guardados en Azure Table Storage.

## <a name="what-you-need"></a>Lo que necesita
Antes de comenzar este procedimiento, debe de haber completado correctamente el tutorial sobre la [ejecución de una aplicación de ejemplo de Azure para activar el parpadeo en Intel Edison][run-the-azure-blink-sample-application-on-intel-edison].

## <a name="read-new-messages-from-your-storage-account"></a>Lectura de mensajes nuevos de la cuenta de almacenamiento
En el artículo anterior, ejecutó una aplicación de ejemplo en Edison. La aplicación de ejemplo envío mensajes a IoT Hub de Azure. Los mensajes enviados a IoT Hub se almacenan en Azure Table Storage a través de Azure Function App. Para poder leer los mensajes de Azure Table Storage, necesita la cadena de conexión de Azure Storage.

Para leer los mensajes almacenados en Azure Table Storage, siga estos pasos:

1. Obtenga la cadena de conexión ejecutando el siguiente comando:

   ```bash
   az storage account list -g iot-sample --query [].name
   az storage account show-connection-string -g iot-sample -n {storage name}
   ```

   El primer comando recupera el elemento `storage name` que se utiliza en el segundo comando para obtener la cadena de conexión. Use `iot-sample` como valor de `{resource group name}`, si no lo modificó previamente.
2. Abra el archivo de configuración `config-edison.json` en Visual Studio Code con este comando:

   ```bash
   # For Windows command prompt
   code %USERPROFILE%\.iot-hub-getting-started\config-edison.json

   # For MacOS or Ubuntu
   code ~/.iot-hub-getting-started/config-edison.json
   ```
3. Reemplace `[Azure storage connection string]` por la cadena de conexión que obtuvo en el paso 1.
4. Guarde el archivo `config-edison.json`.
5. Vuelva a enviar los mensajes y léalos en Azure Table Storage mediante el siguiente comando:

   ```bash
   gulp run --read-storage
   ```

   La lógica para leer mensajes de Azure Table Storage se encuentra en el archivo `azure-table.js`.

   ![Ejecución de Gulp: almacenamiento de lectura][gulp run]

## <a name="summary"></a>Resumen
Ha conseguido conectar Edison a IoT Hub en la nube y utilizar la aplicación de ejemplo de activación del parpadeo para enviar mensajes de dispositivo a nube. También ha utilizado Azure Function App para almacenar los mensajes entrantes de IoT Hub en Azure Table Storage. Ahora, puede enviar a Edison mensajes de dispositivo a nube desde IoT Hub.

## <a name="next-steps"></a>Pasos siguientes
[Ejecución de la aplicación de ejemplo para recibir mensajes de nube a dispositivo][receive-cloud-to-device-messages]
<!-- Images and links -->

[troubleshooting]: iot-hub-intel-edison-kit-node-troubleshooting.md
[run-the-azure-blink-sample-application-on-intel-edison]: iot-hub-intel-edison-kit-node-lesson3-run-azure-blink.md
[gulp run]: media/iot-hub-intel-edison-lessons/lesson3/gulp_read_message.png
[receive-cloud-to-device-messages]: iot-hub-intel-edison-kit-node-lesson4-send-cloud-to-device-messages.md


<!--HONumber=Jan17_HO4-->


