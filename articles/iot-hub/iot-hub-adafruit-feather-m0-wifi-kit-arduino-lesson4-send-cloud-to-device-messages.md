---
title: "Ejecución de una aplicación de ejemplo para recibir mensajes de la nube al dispositivo desde IoT Hub de Azure | Microsoft Docs"
description: "Una aplicación de ejemplo se ejecuta en Adafruit Feather M0 WiFi y supervisa los mensajes entrantes de IoT Hub. Una nueva tarea de Gulp envía mensajes a Adafruit Feather M0 WiFi desde IoT Hub para que parpadee el LED."
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "control de led de arduino desde web, control de led de arduino a través de web"
ms.assetid: a0bf53fb-29fb-485f-ba4a-6c715057b1a2
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/13/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 9e8084fe26229ef9ed1676c0a3c34e0ee7be88b1
ms.openlocfilehash: 55eda75c1e3290cedda1c4433db67c3cf320df20


---
# <a name="run-a-sample-application-to-receive-cloud-to-device-messages"></a>Ejecución de una aplicación de ejemplo para recibir mensajes de la nube al dispositivo
En este artículo, se implementa una aplicación de ejemplo en la placa Adafruit Feather M0 WiFi Arduino.

La aplicación de ejemplo supervisa los mensajes entrantes de IoT Hub. También se ejecuta una tarea de Gulp en el equipo para enviar mensajes a la placa de Arduino desde IoT Hub. Cuando la aplicación de ejemplo reciba los mensajes, el LED parpadeará. Si tiene problemas, busque soluciones en la [página de solución de problemas][troubleshooting].

## <a name="what-you-will-do"></a>Lo que hará
* Conecte la aplicación de ejemplo a su instancia de IoT Hub.
* Implemente y ejecute la aplicación de ejemplo.
* Envíe mensajes desde su instancia de IoT Hub a la placa de Arduino para que parpadee el LED.

## <a name="what-you-will-learn"></a>Lo qué aprenderá
En este artículo, aprenderá lo siguiente:
* Cómo supervisar los mensajes entrantes desde IoT Hub
* Cómo enviar mensajes de la nube al dispositivo desde IoT Hub a su placa de Arduino.

## <a name="what-you-need"></a>Lo que necesita
* La placa de Arduino, configurada para su uso. Para obtener información sobre cómo configurar el panel de Arduino, consulte [Configure your device][configure-your-device] (Configure su dispositivo).
* Una instancia de IoT Hub creada en su suscripción de Azure. Para obtener información sobre cómo crear su instancia de IoT Hub, consulte [Creación de su instancia de IoT Hub de Azure][create-your-azure-iot-hub].

## <a name="connect-the-sample-application-to-your-iot-hub"></a>Conexión de la aplicación de ejemplo con el centro de IoT Hub

1. Asegúrese de que se encuentra en la carpeta del repositorio `iot-hub-c-feather-m0-getting-started`.

   Abra la aplicación de ejemplo en Visual Studio Code ejecutando los comandos siguientes:

   ```bash
   cd Lesson4
   code .
   ```

   Observe el archivo `app.ino` en la subcarpeta `app`. El archivo `app.ino` es el archivo de origen de la clave y contiene el código para supervisar los mensajes entrantes de IoT Hub. La función `blinkLED` hace parpadear el LED.

   ![Estructura del repositorio en la aplicación de ejemplo][repo-structure]

2. Obtenga el puerto serie del dispositivo con la CLI de detección de dispositivos:

   ```bash
   devdisco list --usb
   ```

   Debe ver un resultado similar al siguiente y localizar el puerto COM USB de la placa de Arduino:

   ![Detección de dispositivos][device-discovery]

3. Abra el archivo `config.json` de la carpeta lesson y agregue el valor del número de puerto COM encontrado:

   ```json
   {
       "device_port" : "COM1"
   }
   ```

   ![config.json][config-json]

   > [!NOTE]
   > Para el puerto COM, en la plataforma Windows tiene el formato de `COM1, COM2, ...`. En Mac OS o Ubuntu, empezará por `/dev/`.

4. Inicialice el archivo de configuración con los siguientes comandos:

   ```bash
   # For Windows command prompt
   npm install
   gulp init
   gulp install-tools
   ```

5. Realice las sustituciones siguientes en el archivo `config-arduino.json`:

   Si completó los pasos que aparecen en [Creación de una instancia de Azure Function App y una cuenta de Azure Storage][create-an-azure-function-app-and-storage-account] en este equipo, se heredarán todas las configuraciones, por lo que no tendrá que implementar ni ejecutar la aplicación de ejemplo. Si completó los pasos que aparecen en [Creación de una instancia de Azure Function App y una cuenta de Azure Storage][create-an-azure-function-app-and-storage-account] en otro equipo, debe reemplazar los marcadores de posición en el archivo `config-arduino.json`. El archivo `config-arduino.json` se encuentra en la subcarpeta de la carpeta principal.

   ![Contenido del archivo config-arduino.json][config-arduino-json]

   * Reemplace **[Wi-Fi SSID]** por el SSID Wi-Fi que se conectó a Internet.
   * Reemplace **[Wi-Fi password]** por su contraseña de Wi-Fi. Quite la cadena si su Wi-Fi no necesita contraseña.
   * Sustituya **[IoT device connection string]** por la cadena de conexión del dispositivo que obtendrá al ejecutar el comando `az iot device show-connection-string --hub-name {my hub name} --device-id {device id}`.
   * Sustituya **[IoT hub connection string]** por la cadena de conexión de IoT Hub que obtendrá al ejecutar el comando `az iot hub show-connection-string --name {my hub name}`.

## <a name="deploy-and-run-the-sample-application"></a>Implementación y ejecución de la aplicación de ejemplo
Implemente y ejecute la aplicación de ejemplo en la placa de Arduino ejecutando los comandos siguientes:

```bash
gulp run
# You can monitor the serial port by running listen task:
gulp listen

# Or you can combine above two gulp tasks into one:
gulp run --listen
```

El comando de Gulp implementa la aplicación de ejemplo en la placa de Arduino. Luego, ejecuta la aplicación en la placa de Arduino y una tarea independiente en el equipo host para enviar 20 mensajes de intermitencia a la placa de Arduino desde IoT Hub.

Una vez que se ejecuta, la aplicación de ejemplo empieza a escuchar los mensajes de IoT Hub. Mientras tanto, la tarea de Gulp envía varios mensajes de intermitencia desde IoT Hub a la placa de Arduino. Cada vez que la placa recibe un mensaje de intermitencia, la aplicación de ejemplo llama a la función `blinkLED` para hacer parpadear el LED.

A medida que la tarea de Gulp envía a la placa de Arduino los 20 mensajes desde el centro de IoT Hub, el LED debería parpadear cada dos segundos. El último es un mensaje "stop" que indica a la aplicación que deje de ejecutarse.

![Aplicación de ejemplo con comandos de Gulp y mensajes de parpadeo][sample-application]

## <a name="summary"></a>Resumen
Ya ha enviado correctamente mensajes desde su instancia de IoT Hub para que la placa de Arduino haga parpadear el LED. La siguiente tarea es optativa y consiste en cambiar el comportamiento de encendido y apagado del LED.

## <a name="next-steps"></a>Pasos siguientes
[Modificación del comportamiento de encendido y apagado del LED][change-the-on-and-off-led-behavior]


<!-- Images and links -->

[troubleshooting]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-troubleshooting.md
[configure-your-device]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson1-configure-your-device.md
[create-your-azure-iot-hub]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson2-prepare-azure-iot-hub.md
[repo-structure]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson4/repo_structure_arduino.png
[device-discovery]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson1/device_discovery.png
[config-json]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson1/vscode-config-mac.png
[create-an-azure-function-app-and-storage-account]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson3-deploy-resource-manager-template.md
[config-arduino-json]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson4/config-arduino.png
[sample-application]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson4/gulp_blink_arduino.png
[change-the-on-and-off-led-behavior]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson4-change-led-behavior.md


<!--HONumber=Dec16_HO2-->


