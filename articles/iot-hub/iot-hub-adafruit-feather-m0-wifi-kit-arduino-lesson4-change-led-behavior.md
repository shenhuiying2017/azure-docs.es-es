---
title: "Conexión de Arduino (C) a Azure IoT: Lección 4: Modificación de la aplicación | Microsoft Docs"
description: Personalice los mensajes para cambiar el comportamiento de encendido y apagado del LED.
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: control del LED con Arduino
ms.assetid: d7a25430-450e-43c4-a3ed-1eed995b8b7e
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/13/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 64e69df256404e98f6175f77357500b562d74318
ms.openlocfilehash: 3585dfbac8816140c0a62931920aff1a6bf7d540


---
# <a name="change-the-on-and-off-behavior-of-the-led"></a>Modificación del comportamiento de encendido y apagado del LED
## <a name="what-you-will-do"></a>Lo que hará
Personalice los mensajes para cambiar el comportamiento de encendido y apagado del LED. Si tiene problemas, busque soluciones en [esta página](iot-hub-adafruit-feather-m0-wifi-kit-arduino-troubleshooting.md) para la placa Adafruit Feather M0 WiFi Arduino.

## <a name="what-you-will-learn"></a>Lo qué aprenderá
Utilice las funciones adicionales de Arduino para cambiar el comportamiento de encendido y apagado del LED.

## <a name="what-you-need"></a>Lo que necesita
Para seguir este procedimiento, debe haber completado correctamente el tutorial sobre la [ejecución de una aplicación de ejemplo en la placa de Arduino para recibir mensajes de nube a dispositivo][receive-cloud-to-device-messages].

## <a name="add-functions-to-mainc-and-gulpfilejs"></a>Adición de funciones a main.c y gulpfile.js
1. Abra la aplicación de ejemplo en Visual Studio Code ejecutando los comandos siguientes:

   ```bash
   cd Lesson4
   code .
   ```
2. Abra el archivo `app.ino` y, luego, agregue las siguientes funciones después de blinkLED():

   ```arduino
   static void turnOnLED()
   {
     digitalWrite(LED_PIN, HIGH);
   }

   static void turnOffLED()
   {
     digitalWrite(LED_PIN, LOW);
   }
   ```

   ![Archivo app.js con funciones agregadas][app-ino-file]
3. Agregue las siguientes condiciones antes del bloqueo `else if` de la función `receiveMessageCallback`:

   ```arduino
   else if (strcmp((const char*)value, "\"on\"") == 0)
   {
       turnOnLED();
   }
   else if (0 == strcmp((const char*)value, "\"off\"") == 0)
   {
       turnOffLED();
   }
   ```

   Ahora ha configurado la aplicación de ejemplo para responder a más instrucciones a través de mensajes. La instrucción "on" enciende el LED, mientras que la instrucción "off" lo apaga.
4. Abra el archivo gulpfile.js y, después, agregue una función nueva antes de la función `sendMessage`:

   ```javascript
   var buildCustomMessage = function (messageId) {
     if ((messageId & 1) && (messageId < MAX_MESSAGE_COUNT)) {
       return new Message(JSON.stringify({ command: 'on', messageId: messageId }));
     } else if (messageId < MAX_MESSAGE_COUNT) {
       return new Message(JSON.stringify({ command: 'off', messageId: messageId }));
     } else {
       return new Message(JSON.stringify({ command: 'stop', messageId: messageId }));
     }
   };
   ```

   ![Archivo Gulpfile.js con una función agregada][gulp-file-js]
5. En la función `sendMessage`, reemplace la línea `var message = buildMessage(sentMessageCount);` por la nueva línea que se muestra en el siguiente fragmento:

   ```javascript
   var message = buildCustomMessage(sentMessageCount);
   ```
6. Guarde todos los cambios.

### <a name="deploy-and-run-the-sample-application"></a>Implementación y ejecución de la aplicación de ejemplo
Implemente y ejecute la aplicación de ejemplo en la placa de Arduino ejecutando el comando siguiente:

```bash
gulp run
# You can monitor the serial port by running listen task:
gulp listen

# Or you can combine above two gulp tasks into one:
gulp run --listen
```

El LED debería encenderse durante dos segundos y apagarse después otros dos segundos. El último mensaje "stop" detiene la ejecución de la aplicación de ejemplo.

![Encendido y apagado][on-and-off]

¡Enhorabuena! Ha personalizado correctamente los mensajes que se envían a la placa de Arduino desde IoT Hub.

### <a name="summary"></a>Resumen
En esta sección opcional, se explica cómo personalizar los mensajes para que la aplicación de ejemplo pueda controlar el comportamiento de encendido y apagado del LED de manera diferente.

<!-- Images and links -->

[receive-cloud-to-device-messages]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson4-send-cloud-to-device-messages.md
[app-ino-file]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson4/updated_app_ino.png
[gulp-file-js]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson4/updated_gulpfile_js.png
[on-and-off]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson4/gulp_on_and_off_arduino.png


<!--HONumber=Jan17_HO4-->


