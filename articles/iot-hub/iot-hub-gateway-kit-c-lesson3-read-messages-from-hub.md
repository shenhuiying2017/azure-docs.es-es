---
title: Lectura de mensajes en IoT Hub de Azure | Microsoft Docs
description: "Ejecute el código de ejemplo en el equipo host para leer los mensajes en IoT Hub."
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "datos en la nube, recopilación de datos en la nube, servicio en la nube de iot, datos de iot"
ms.assetid: cc88be24-b5c0-4ef2-ba21-4e8f77f3e167
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 65a25dd7a2f6a8d518217512f9e10fc9008ee728
ms.openlocfilehash: 09b0e0321b08a11622633e727cf8be54c1707436


---

# <a name="read-messages-from-your-iot-hub"></a>Lectura de mensajes en IoT Hub

## <a name="what-you-will-do"></a>Lo que hará

- Ejecute el código de ejemplo en el equipo host para leer los mensajes en IoT Hub.

Si tiene problemas, busque soluciones en la [página de solución de problemas](iot-hub-gateway-kit-c-troubleshooting.md).

## <a name="what-you-will-learn"></a>Lo qué aprenderá

Uso de la herramienta Gulp para leer mensajes en IoT Hub.

## <a name="what-you-need"></a>Lo que necesita

- La aplicación de ejemplo BLE que se ejecutó correctamente en la lección 3.

## <a name="get-your-iot-hub-and-device-connection-strings"></a>Obtención de las cadenas de conexión de IoT Hub y del dispositivo

El dispositivo (SensorTag de TI o dispositivo simulado) utiliza la cadena de conexión del dispositivo para conectarse a la instancia de IoT Hub. La cadena de conexión de IoT Hub se utiliza para conectar con el registro de identidades en IoT Hub para administrar los dispositivos que pueden conectarse con su instancia de IoT Hub.

- Enumere todas las instancias de IoT Hub del grupo de recursos mediante la ejecución del siguiente comando:

   ```bash
   az iot hub list -g iot-gateway --query [].name
   ```

   Use `iot-gateway` como valor de `{resource group name}`, si no lo modificó previamente.
- Obtenga la cadena de conexión de IoT Hub mediante la ejecución del siguiente comando:

   ```bash
   az iot hub show-connection-string --name {my hub name} -g iot-gateway
   ```

   `{my hub name}` es el nombre que especificó en la lección 2.

## <a name="configure-the-device-connection-for-the-sample-code"></a>Configuración de la conexión de dispositivos para el código de ejemplo

Actualice el archivo de configuración de dispositivos `config-azure.json` para que pueda leer mensajes desde su instancia de IoT Hub en el equipo host. Para ello, siga estos pasos.

1. Abra `config-azure.json` en Visual Studio Code mediante la ejecución del siguiente comando en una ventana de consola:

   ```bash
   # For Windows command prompt
   code %USERPROFILE%\.iot-hub-getting-started\config-azure.json
   # For MacOS or Ubuntu
   code ~/.iot-hub-getting-started/config-azure.json
   ```

2. Realice las sustituciones siguientes en el archivo `config-azure.json`:

   ![captura de pantalla de configuración de azure](media/iot-hub-gateway-kit-lessons/lesson3/config_azure.png)

   Reemplace `[IoT hub connection string]` con la cadena de conexión de IoT Hub obtenida.

## <a name="read-messages-from-your-iot-hub"></a>Lectura de mensajes en IoT Hub

Si tiene un SensorTag de TI, asegúrese de que ya ha encendido su SensorTag. Ejecute la aplicación de ejemplo de la puerta de enlace y lea los mensajes de IoT Hub mediante el siguiente comando:

```bash
gulp run --iot-hub
```

El comando ejecuta la aplicación de ejemplo BLE que lee y empaqueta los datos de temperatura del dispositivo simulado o SensorTag, y envía el mensaje a su instancia de IoT Hub cada 2 segundos. También genera un proceso secundario para recibir el mensaje.

Todos los mensajes que se envían y se reciben se muestran al instante en la misma ventana de consola del equipo host. La instancia de la aplicación de ejemplo se cerrará automáticamente en 40 segundos.

![Aplicación de ejemplo BLE con mensajes enviados y recibidos](media/iot-hub-gateway-kit-lessons/lesson3/gulp_run_read_hub.png)

## <a name="summary"></a>Resumen

Ha ejecutado un código de ejemplo para leer los mensajes de su instancia de IoT Hub. Está listo para leer los mensajes que se almacenan en Azure Table Storage.

## <a name="next-steps"></a>Pasos siguientes
[Creación de una instancia de Azure Function App y una cuenta de Azure Storage](iot-hub-gateway-kit-c-lesson4-deploy-resource-manager-template.md)





<!--HONumber=Dec16_HO3-->


