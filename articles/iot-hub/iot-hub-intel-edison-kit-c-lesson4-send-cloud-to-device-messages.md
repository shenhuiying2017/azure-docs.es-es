---
title: "Ejecución de una aplicación de ejemplo para recibir mensajes de la nube al dispositivo de IoT Hub de Azure | Microsoft Docs"
description: "Una aplicación de ejemplo se ejecuta en Edison y supervisa los mensajes entrantes provenientes desde su instancia de IoT Hub. Una nueva tarea de Gulp envía mensajes a Edison desde su instancia de IoT Hub para que parpadee el LED."
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "control de led de arduino desde web, control de led de arduino a través de web"
ms.assetid: 820d38f3-d3b8-4249-9e2b-f1b9b771e62f
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/8/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: d492fa295237f6ff333aefb2181975e8af948723
ms.openlocfilehash: 48e5c46d53ce412384e8667904e84b9ebd454cff


---
# <a name="run-a-sample-application-to-receive-cloud-to-device-messages"></a>Ejecución de una aplicación de ejemplo para recibir mensajes de la nube al dispositivo
En este artículo se implementará una aplicación de ejemplo en Intel Edison. La aplicación de ejemplo supervisa los mensajes entrantes de IoT Hub. También ejecutará una tarea de Gulp en el equipo para enviar mensajes a Edison desde su instancia de IoT Hub. Cuando la aplicación de ejemplo reciba los mensajes, el LED parpadeará. Si tiene problemas, busque soluciones en la [página de solución de problemas][troubleshooting].

## <a name="what-you-will-do"></a>Lo que hará
* Conecte la aplicación de ejemplo a su instancia de IoT Hub.
* Implemente y ejecute la aplicación de ejemplo.
* Envíe mensajes desde su instancia de IoT Hub a Edison para que el LED parpadee.

## <a name="what-you-will-learn"></a>Lo qué aprenderá
En este artículo, aprenderá lo siguiente:
* Cómo supervisar los mensajes entrantes desde IoT Hub
* Envío a Edison de mensajes de la nube al dispositivo desde su instancia de IoT Hub.

## <a name="what-you-need"></a>Lo que necesita
* Intel Edison, configurado para su uso. Para obtener información sobre cómo configurar Edison, consulte [Configuración del dispositivo][configure-your-device].
* Una instancia de IoT Hub creada en su suscripción de Azure. Para obtener información sobre cómo crear su instancia de IoT Hub, consulte [Creación de su instancia de IoT Hub de Azure][create-your-azure-iot-hub].

## <a name="connect-the-sample-application-to-your-iot-hub"></a>Conexión de la aplicación de ejemplo con el centro de IoT Hub
1. Asegúrese de que se encuentra en la carpeta del repositorio `iot-hub-c-edison-getting-started`. Abra la aplicación de ejemplo en Visual Studio Code ejecutando los comandos siguientes:

   ```bash
   cd Lesson4
   code .
   ```

   El archivo que se encuentra en la carpeta `app` es el archivo de origen de la clave que contiene el código para supervisar los mensajes entrantes desde la instancia de IoT Hub. La función `blinkLED` hace parpadear el LED.

   ![Estructura del repositorio en la aplicación de ejemplo][repo-structure]
2. Inicialice el archivo de configuración con los siguientes comandos:

   ```bash
   npm install
   gulp init
   ```

   Si completó los pasos que aparecen en [Creación de una instancia de Azure Function App y una cuenta de Azure Storage][create-an-azure-function-app-and-storage-account] en este equipo, se heredarán todas las configuraciones, por lo que no tendrá que implementar ni ejecutar la aplicación de ejemplo. Si completó los pasos que aparecen en [Creación de una instancia de Azure Function App y una cuenta de Azure Storage][create-an-azure-function-app-and-storage-account] en otro equipo, debe reemplazar los marcadores de posición en el archivo `config-edison.json`. El archivo `config-edison.json` se encuentra en la subcarpeta de la carpeta principal.

   ![Contenido del archivo config-edison.json](media/iot-hub-intel-edison-lessons/lesson4/config-edison.png)

   * Sustituya **[device hostname or IP address]** por la dirección IP del dispositivo que anotó cuando configuró el dispositivo.
   * Sustituya **[IoT device connection string]** por la cadena de conexión del dispositivo que obtendrá al ejecutar el comando `az iot device show-connection-string --hub-name {my hub name} --device-id {device id}`.
   * Sustituya **[IoT hub connection string]** por la cadena de conexión de IoT Hub que obtendrá al ejecutar el comando `az iot hub show-connection-string --name {my hub name}`.

   > [!NOTE]
   > Ejecute también **gulp install-tools** si no lo hizo en la lección 1.

## <a name="deploy-and-run-the-sample-application"></a>Implementación y ejecución de la aplicación de ejemplo
Implemente y ejecute la aplicación de ejemplo en Edison usando los comandos siguientes:

```bash
gulp deploy && gulp run
```

El comando de Gulp implementa la aplicación de ejemplo en Edison. Luego, ejecuta la aplicación en Edison y una tarea independiente en el equipo host para enviar 20 mensajes de parpadeo a Edison desde su instancia de IoT Hub.

Una vez que se ejecuta, la aplicación de ejemplo empieza a escuchar los mensajes de IoT Hub. Mientras tanto, la tarea de Gulp envía varios mensajes de parpadeo desde su instancia de IoT Hub a Edison. Cada vez que Edison recibe un mensaje de parpadeo, la aplicación de ejemplo llama a la función `blinkLED` para hacer parpadear el LED.

A medida que la tarea de Gulp envía 20 mensajes a Edison desde su instancia de IoT Hub, el LED debería parpadear cada dos segundos. El último es un mensaje "stop" que indica a la aplicación que deje de ejecutarse.

![Aplicación de ejemplo con comandos de Gulp y mensajes de parpadeo][gulp-command-and-blink-messages]

## <a name="summary"></a>Resumen
Envió correctamente mensajes desde su instancia de IoT Hub a Edison para que el LED parpadee. La siguiente tarea es optativa y consiste en cambiar el comportamiento de encendido y apagado del LED.

## <a name="next-steps"></a>Pasos siguientes
[Modificación del comportamiento de encendido y apagado del LED][change-the-on-and-off-behavior-of-the-led]

<!-- Images and links -->

[troubleshooting]: iot-hub-intel-edison-kit-c-troubleshooting.md
[configure-your-device]: iot-hub-intel-edison-kit-c-lesson1-configure-your-device.md
[create-your-azure-iot-hub]: iot-hub-intel-edison-kit-c-lesson2-prepare-azure-iot-hub.md
[repo-structure]: media/iot-hub-intel-edison-lessons/lesson4/repo_structure_c.png
[create-an-azure-function-app-and-storage-account]: iot-hub-intel-edison-kit-c-lesson3-deploy-resource-manager-template.md
[gulp-command-and-blink-messages]: media/iot-hub-intel-edison-lessons/lesson4/gulp_blink_c.png
[change-the-on-and-off-behavior-of-the-led]: iot-hub-intel-edison-kit-c-lesson4-change-led-behavior.md


<!--HONumber=Dec16_HO2-->


