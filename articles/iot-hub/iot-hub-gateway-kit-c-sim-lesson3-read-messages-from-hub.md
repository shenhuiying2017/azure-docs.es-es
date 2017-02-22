---
title: "Dispositivo simulado y puerta de enlace de Azure IoT: Lección 3: Lectura de mensajes | Microsoft Docs"
description: "Ejecute el código de ejemplo en el equipo host para leer los mensajes en IoT Hub."
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "datos en la nube, recopilación de datos en la nube, servicio en la nube de iot, datos de iot"
ms.assetid: 5a6ec9c1-d83c-41c1-beaf-7c0d3395d77f
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 61e9a9fc7876094c04238c61cfc38efdd97b05f7
ms.openlocfilehash: 08ccd35c6e6a8e77f0fd4637f14a1f27730560d6


---

# <a name="read-messages-from-your-iot-hub"></a>Lectura de mensajes en IoT Hub

## <a name="what-you-will-do"></a>Lo que hará

- Ejecute el código de ejemplo en el equipo host para leer los mensajes en IoT Hub.

Si tiene problemas, busque soluciones en la [página de solución de problemas](iot-hub-gateway-kit-c-sim-troubleshooting.md).

## <a name="what-you-will-learn"></a>Lo qué aprenderá

Uso de la herramienta Gulp para leer mensajes en IoT Hub.

## <a name="what-you-need"></a>Lo que necesita

- El ejemplo de dispositivo simulado en [Configuración y ejecución de una aplicación de ejemplo de carga en la nube del dispositivo simulado](iot-hub-gateway-kit-c-sim-lesson3-configure-simulated-device-app.md).

## <a name="get-your-iot-hub-and-device-connection-strings"></a>Obtención de las cadenas de conexión de IoT Hub y del dispositivo

El dispositivo simulado utiliza la cadena de conexión del dispositivo para conectarse a IoT Hub. La cadena de conexión de IoT Hub se utiliza para conectar con el registro de identidad en IoT Hub para administrar los dispositivos que pueden conectarse con IoT Hub.

- Enumere todas las instancias de IoT Hub del grupo de recursos mediante la ejecución del siguiente comando:

   ```bash
   az iot hub list -g iot-gateway --query [].name
   ```

   Use `iot-gateway` como valor de `{resource group name}`, en caso de que no lo haya modificado.
- Obtenga la cadena de conexión de IoT Hub mediante la ejecución del siguiente comando:

   ```bash
   az iot hub show-connection-string --name {my hub name} -g iot-gateway
   ```

   `{my hub name}` es el nombre que especificó en la lección 2.

## <a name="configure-the-device-connection-for-the-sample-code"></a>Configuración de la conexión de dispositivos para el código de ejemplo

Actualice las configuraciones de IoT Hub y de la conexión de dispositivos en `config-azure.json` mediante estos pasos:

1. Abra `config-azure.json` en Visual Studio Code mediante la ejecución del siguiente comando en una ventana de consola:

   ```bash
   # For Windows command prompt
   code %USERPROFILE%\.iot-hub-getting-started\config-azure.json
   # For MacOS or Ubuntu
   code ~/.iot-hub-getting-started/config-azure.json
   ```

2. Realice las sustituciones siguientes en el archivo `config-azure.json`:

   ![captura de pantalla de configuración de azure](media/iot-hub-gateway-kit-lessons/lesson3/config_azure.png)

   Reemplace `[IoT hub connection string]` con la cadena de conexión de IoT Hub.

## <a name="read-messages-from-your-iot-hub"></a>Lectura de mensajes en IoT Hub

Ejecute la aplicación de ejemplo del dispositivo simulado y lea los mensajes de IoT Hub mediante el siguiente comando:

```bash
gulp run --iot-hub
```

El comando ejecuta la aplicación que envía mensajes a IoT Hub cada 2 segundos. También genera un proceso secundario para recibir el mensaje.

Todos los mensajes que se envían y reciben se muestran al instante en la misma ventana de consola del equipo host. La aplicación se cerrará en 40 segundos.

![Aplicación de ejemplo simulada con mensajes enviados y recibidos](media/iot-hub-gateway-kit-lessons/lesson3/gulp_run_read_hub_simudev.png)

## <a name="summary"></a>Resumen

Ha ejecutado correctamente la aplicación de ejemplo para enviar datos a IoT Hub con el dispositivo simulado. También ha leído los mensajes que se han enviado a IoT Hub.

## <a name="next-steps"></a>Pasos siguientes
[Creación de una instancia de Azure Function App y una cuenta de Azure Storage](iot-hub-gateway-kit-c-sim-lesson4-deploy-resource-manager-template.md)





<!--HONumber=Jan17_HO4-->


