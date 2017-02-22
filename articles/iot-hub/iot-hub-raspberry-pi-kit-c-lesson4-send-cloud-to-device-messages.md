---
title: "Conexión de Raspberry Pi (C) a Azure IoT: Lección 4: De la nube al dispositivo | Microsoft Docs"
description: "La aplicación de ejemplo se ejecuta en Pi y supervisa los mensajes entrantes de IoT Hub. Una nueva tarea de Gulp envía mensajes a Pi desde su centro de IoT para que parpadee el LED."
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: de la nube al dispositivo, mensaje de la nube
ms.assetid: fcbc0dd0-cae3-47b0-8e58-240e4f406f75
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/28/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 4f9feb665ac93028023df86889ca53a64ac2ec0e
ms.openlocfilehash: 3a6957ebbb239eacb4fce696b7fcfd02e690310b


---
# <a name="run-a-sample-application-to-receive-cloud-to-device-messages"></a>Ejecución de la aplicación de ejemplo para recibir mensajes de nube a dispositivo
En este artículo, implementará una aplicación de ejemplo en Raspberry Pi 3. La aplicación de ejemplo supervisa los mensajes entrantes de IoT Hub. También ejecutará una tarea de Gulp en el equipo para enviar mensajes a Pi desde IoT Hub. Cuando la aplicación de ejemplo reciba los mensajes, el LED parpadeará. Si tiene problemas, busque soluciones en la [página de solución de problemas](iot-hub-raspberry-pi-kit-c-troubleshooting.md).

## <a name="what-you-will-do"></a>Lo que hará
* Conecte la aplicación de ejemplo a su instancia de IoT Hub.
* Implemente y ejecute la aplicación de ejemplo.
* Envíe mensajes desde el centro de IoT Hub a Pi para que el LED parpadee.

## <a name="what-you-will-learn"></a>Lo qué aprenderá
En este artículo, aprenderá lo siguiente:
* Cómo supervisar los mensajes entrantes desde IoT Hub
* Envío a Pi de mensajes de la nube al dispositivo desde IoT Hub

## <a name="what-you-need"></a>Lo que necesita
* Un dispositivo Raspberry Pi 3, con la configuración lista para utilizarse. Para aprender a configurar Pi, consulte [Configure your device](iot-hub-raspberry-pi-kit-c-lesson1-configure-your-device.md) (Configuración del dispositivo).
* Una instancia de IoT Hub creada en su suscripción de Azure. Para aprender a crear la instancia de IoT Hub, consulte [Creación de un centro de IoT y registro de Raspberry Pi 3](iot-hub-raspberry-pi-kit-c-lesson2-prepare-azure-iot-hub.md).

## <a name="connect-the-sample-application-to-your-iot-hub"></a>Conexión de la aplicación de ejemplo con el centro de IoT Hub
1. Asegúrese de que se encuentra en la carpeta del repositorio `iot-hub-c-raspberrypi-getting-started`. Abra la aplicación de ejemplo en Visual Studio Code ejecutando los comandos siguientes:

   ```bash
   cd Lesson4
   code .
   ```

   Observe el archivo `app.c` en la subcarpeta `app`. El archivo `app.c` es el archivo de origen de la clave y contiene el código para supervisar los mensajes entrantes de IoT Hub. La función `blinkLED` hace parpadear el LED.

   ![Estructura del repositorio en la aplicación de ejemplo](media/iot-hub-raspberry-pi-lessons/lesson4/repo_structure_c.png)
2. Inicialice el archivo de configuración con los siguientes comandos:

   ```bash
   npm install
   gulp init
   ```

   Si completó el tutorial [Create an Azure function app and storage account](iot-hub-raspberry-pi-kit-c-lesson3-deploy-resource-manager-template.md) (Creación de una instancia de Azure Function App y una cuenta de almacenamiento) en este equipo, se heredarán todas las configuraciones, por lo que no tendrá que implementar ni ejecutar la aplicación de ejemplo. Si completó el tutorial [Create an Azure function app and storage account](iot-hub-raspberry-pi-kit-c-lesson3-deploy-resource-manager-template.md) (Creación de una instancia de Azure Function App y una cuenta de almacenamiento) en otro equipo, tendrá que reemplazar los marcadores de posición del archivo `config-raspberrypi.json`. El archivo `config-raspberrypi.json` se encuentra en la subcarpeta de la carpeta principal.

   ![Contenido del archivo config-raspberrypi.json](media/iot-hub-raspberry-pi-lessons/lesson4/config_raspberrypi.png)

* Reemplace **[device hostname or IP address]** por la dirección IP o el nombre de host de Pi que obtendrá ejecutando el comando `devdisco list --eth`.
* Sustituya **[IoT device connection string]** por la cadena de conexión del dispositivo que obtendrá al ejecutar el comando `az iot device show-connection-string --hub-name {my hub name} --device-id {device id} -g iot-sample {resource group name}`.
* Sustituya **[IoT hub connection string]** por la cadena de conexión de IoT Hub que obtendrá al ejecutar el comando `az iot hub show-connection-string --name {my hub name} -g iot-sample {resource group name}`.

> [!NOTE]
> Ejecute también **gulp install-tools** si no lo hizo en la lección 1.

## <a name="deploy-and-run-the-sample-application"></a>Implementación y ejecución de la aplicación de ejemplo
Implemente y ejecute la aplicación de ejemplo en Pi mediante el comando siguiente:

```
gulp deploy && gulp run
```

El comando gulp primero ejecuta la tarea de instalación de herramientas. A continuación, implementa la aplicación de ejemplo en Pi. Finalmente, ejecuta la aplicación en Pi. También ejecuta una tarea independiente en el equipo host que envía 20 mensajes de parpadeo a Pi desde IoT Hub.

Una vez que se ejecuta, la aplicación de ejemplo empieza a escuchar los mensajes de IoT Hub. Mientras tanto, la tarea de Gulp envía a Pi varios mensajes de parpadeo desde el centro de IoT Hub. Cada vez que Pi recibe un mensaje de parpadeo, la aplicación de ejemplo llama a la función `blinkLED` para hacer parpadear el LED.

A medida que la tarea de Gulp envía a Pi los 20 mensajes desde el centro de IoT Hub, el LED debería parpadear cada dos segundos. El último es un mensaje "stop" que indica a la aplicación que deje de ejecutarse.

![Aplicación de ejemplo con comandos de Gulp y mensajes de parpadeo](media/iot-hub-raspberry-pi-lessons/lesson4/gulp_blink_c.png)

## <a name="summary"></a>Resumen
Ha enviado correctamente mensajes desde el centro de IoT Hub a Pi para que el LED parpadee. La siguiente tarea es optativa y consiste en cambiar el comportamiento de encendido y apagado del LED.

## <a name="next-steps"></a>Pasos siguientes
[Modificación del comportamiento de encendido y apagado del LED](iot-hub-raspberry-pi-kit-c-lesson4-change-led-behavior.md)



<!--HONumber=Jan17_HO4-->


