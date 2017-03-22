---
title: "Conexión de Arduino (C) a Azure IoT: Lección 3: Ejecución del ejemplo | Microsoft Docs"
description: "Implemente y ejecute una aplicación de ejemplo para Adafruit Feather M0 WiFi que envíe mensajes a su instancia de IoT Hub y haga parpadear el LED."
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "servicio en la nube de IoT, envío de datos de Arduino a la nube"
ms.assetid: 92cce319-2b17-4c9b-889d-deac959e3e7c
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 64e69df256404e98f6175f77357500b562d74318
ms.openlocfilehash: 631a6677e4801b1c3475b9e8f2726219dd101132
ms.lasthandoff: 01/24/2017


---
# <a name="run-a-sample-application-to-send-device-to-cloud-messages"></a>Ejecución de una aplicación de ejemplo para enviar mensajes de dispositivo a nube
## <a name="what-you-will-do"></a>Lo que hará
En este artículo se explica cómo implementar y ejecutar una aplicación de ejemplo en el panel de Adafruit Feather M0 WiFi Arduino que envía mensajes a su instancia de IoT Hub.

Si tiene problemas, busque soluciones en [esta página][troubleshooting].

## <a name="what-you-will-learn"></a>Lo qué aprenderá
Aprenderá a usar la herramienta de Gulp para implementar y ejecutar la aplicación de ejemplo de Arduino en el panel de Arduino.

## <a name="what-you-need"></a>Lo que necesita
* Para comenzar esta tarea, debe haber completado correctamente el tutorial [Creación de una instancia de Azure Function App y una cuenta de Azure Storage para procesar y guardar mensajes de IoT Hub][process-and-store-iot-hub-messages].

## <a name="get-your-iot-hub-and-device-connection-strings"></a>Obtención de las cadenas de conexión de IoT Hub y del dispositivo
La cadena de conexión del dispositivo se usa para conectar el panel de Arduino con su instancia de IoT Hub. La cadena de conexión de IoT Hub se usa para conectar su instancia de IoT Hub con la identidad de dispositivo que representa el panel de Arduino en la instancia de IoT Hub.

* Enumere todos los centros de IoT del grupo de recursos ejecutando el siguiente comando de la CLI de Azure:

```bash
az iot hub list -g iot-sample --query [].name
```

Use `iot-sample` como valor de `{resource group name}`, si no lo modificó previamente.

* Obtenga la cadena de conexión de IoT Hub ejecutando el siguiente comando de la CLI de Azure:

```bash
az iot hub show-connection-string --name {my hub name}
```

`{my hub name}` es el nombre que especificó cuando creó su instancia de IoT Hub y registró el panel de Arduino.

* Obtenga la cadena de conexión de dispositivos ejecutando el siguiente comando:

```bash
az iot device show-connection-string --hub-name {my hub name} --device-id mym0wifi
```

Use `mym0wifi` como valor de `{device id}`, si no lo modificó previamente.
## <a name="configure-the-device-connection"></a>Configuración de la conexión de dispositivos
Para configurar la conexión de dispositivos, siga estos pasos:

1. Obtenga el puerto serie del dispositivo con la CLI de detección de dispositivos:

   ```bash
   devdisco list --usb
   ```

   Debe ver un resultado similar al siguiente y localizar el puerto COM USB de la placa de Arduino:

   ![Detección de dispositivos][device-discovery]

2. Abra el archivo `config.json` en la carpeta de lecciones y agregue el valor del número de puerto COM encontrado:

   ```json
   {
       "device_port" : "COM1"
   }
   ```

   ![config.json][config-json]

   > [!NOTE]
   > Para el puerto COM, en la plataforma Windows tiene el formato de `COM1, COM2, ...`. En macOS o Ubuntu, empieza por `/dev/`.

3. Inicialice el archivo de configuración con los siguientes comandos:

   ```bash
   npm install
   gulp init
   gulp install-tools
   ```
4. Abra el archivo de configuración de dispositivos `config-arduino.json` en Visual Studio Code ejecutando el comando siguiente:

   ```bash
   # For Windows command prompt
   code %USERPROFILE%\.iot-hub-getting-started\config-arduino.json

   # For MacOS or Ubuntu
   code ~/.iot-hub-getting-started/config-arduino.json
   ```

   ![config-arduino.json][config-arduino-json]

5. Realice las sustituciones siguientes en el archivo `config-arduino.json`:

   * Reemplace **[Wi-Fi SSID]** por el SSID Wi-Fi que se conectó a Internet.
   * Reemplace **[Wi-Fi password]** por su contraseña de Wi-Fi. Quite la cadena si su Wi-Fi no necesita contraseña.
   * Reemplace **[IoT device connection string]** con el valor de `device connection string` que ha obtenido.
   * Reemplace **[IoT hub connection string]** con el valor de `iot hub connection string` que ha obtenido.

   > [!NOTE]
   > `azure_storage_connection_string` no es necesario en este artículo. Déjelo como está.

## <a name="deploy-and-run-the-sample-application"></a>Implementación y ejecución de la aplicación de ejemplo
Implemente y ejecute la aplicación de ejemplo en la placa de Arduino ejecutando el comando siguiente:

```bash
gulp run
# You can monitor the serial port by running listen task:
gulp listen

# Or you can combine above two gulp tasks into one:
gulp run --listen
```

> [!NOTE]
> La tarea de Gulp ejecuta las tareas `install-tools` y `run` de forma secuencial. Cuando [implementó la aplicación de intermitencia][deployed-the-blink-app], ejecutó estas tareas por separado.

## <a name="verify-that-the-sample-application-works"></a>Comprobación del funcionamiento de la aplicación de ejemplo
Debería ver que el LED de GPIO 0 en el panel parpadea cada dos segundos. Cada vez que el LED parpadea, la aplicación de ejemplo envía un mensaje a IoT Hub y comprueba que el mensaje se envió correctamente a IoT Hub. Además, todos los mensajes que reciba IoT Hub se imprimirán en la ventana de consola. La aplicación de ejemplo se finaliza automáticamente después de enviar 20 mensajes.

![Aplicación de ejemplo con mensajes enviados y recibidos][sample-application-with-sent-and-received-messages]

## <a name="summary"></a>Resumen
Implementó y ejecutó la nueva aplicación de ejemplo de intermitencia en el panel de Arduino para enviar mensajes de dispositivo a nube a su instancia de IoT Hub. Ahora, puede supervisar los mensajes a medida que se escriben en la cuenta de almacenamiento.

## <a name="next-steps"></a>Pasos siguientes
[Lectura de los mensajes que se conservan en Azure Storage][read-messages-persisted-in-azure-storage]
<!-- Images and links -->

[troubleshooting]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-troubleshooting.md
[process-and-store-iot-hub-messages]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson3-deploy-resource-manager-template.md
[device-discovery]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson1/device_discovery.png
[config-json]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson1/vscode-config-mac.png
[config-arduino-json]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson3/config-arduino.png
[deployed-the-blink-app]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson1-deploy-blink-app.md
[sample-application-with-sent-and-received-messages]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson3/gulp_run_arduino.png
[read-messages-persisted-in-azure-storage]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson3-read-table-storage.md
