---
title: "Conexión de Intel Edison (C) a Azure IoT: Lección 4: Parpadeo del LED | Microsoft Docs"
description: Personalice los mensajes para cambiar el comportamiento de encendido y apagado del LED.
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: control del LED con Arduino
ms.assetid: 9826c55a-0e24-4296-ae54-29b7fe66436a
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 475b25f02715a60493e79ecd2170854019dfc4ac
ms.openlocfilehash: 278bdf74e2fa8f7074bb8f5ed8eae2d47402b299
ms.lasthandoff: 01/25/2017


---
# <a name="change-the-on-and-off-behavior-of-the-led"></a>Modificación del comportamiento de encendido y apagado del LED
## <a name="what-you-will-do"></a>Lo que hará
Personalice los mensajes para cambiar el comportamiento de encendido y apagado del LED. Si tiene problemas, busque soluciones en [esta página][troubleshooting].

## <a name="what-you-will-learn"></a>Lo qué aprenderá
Utilice las funciones adicionales para cambiar el comportamiento de encendido y apagado del LED.

## <a name="what-you-need"></a>Lo que necesita
Para seguir este procedimiento, debe haber completado correctamente el tutorial sobre la [ejecución de una aplicación de ejemplo en Intel Edison para recibir mensajes de nube a dispositivo][receive-cloud-to-device-messages].

## <a name="add-functions-to-mainc-and-gulpfilejs"></a>Adición de funciones a main.c y gulpfile.js
1. Abra la aplicación de ejemplo en Visual Studio Code ejecutando los comandos siguientes:

   ```bash
   cd Lesson4
   code .
   ```
2. Abra el archivo `main.c` y, luego, agregue las siguientes funciones después de blinkLED():

   ```c
   static void turnOnLED()
   {
     mraa_gpio_write(context, 1);
   }

   static void turnOffLED()
   {
     mraa_gpio_write(context, 0);
   }
   ```

   ![Archivo main.c con funciones agregadas](media/iot-hub-intel-edison-lessons/lesson4/updated_app_c.png)

3. Agregue las siguientes condiciones antes del bloqueo `else if` de la función `receiveMessageCallback`:

   ```c
   else if (0 == strcmp((const char*)value, "\"on\""))
   {
       turnOnLED();
   }
   else if (0 == strcmp((const char*)value, "\"off\""))
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
   }
   ```

   ![Archivo Gulpfile.js con una función agregada][gulpfile]
5. En la función `sendMessage`, reemplace la línea `var message = buildMessage(sentMessageCount);` por la nueva línea que se muestra en el siguiente fragmento:

   ```javascript
   var message = buildCustomMessage(sentMessageCount);
   ```
6. Guarde todos los cambios.

### <a name="deploy-and-run-the-sample-application"></a>Implementación y ejecución de la aplicación de ejemplo
Implemente y ejecute la aplicación de ejemplo en Edison usando el comando siguiente:

```bash
gulp deploy && gulp run
```

El LED debería encenderse durante dos segundos y apagarse después otros dos segundos. El último mensaje "stop" detiene la ejecución de la aplicación de ejemplo.

![Encendido y apagado][on-and-off]

¡Enhorabuena! Ha personalizado correctamente los mensajes que se envían a Edison desde IoT Hub.

### <a name="summary"></a>Resumen
En esta sección opcional, se explica cómo personalizar los mensajes para que la aplicación de ejemplo pueda controlar el comportamiento de encendido y apagado del LED de manera diferente.

<!-- Images and links -->

[troubleshooting]: iot-hub-intel-edison-kit-c-troubleshooting.md
[receive-cloud-to-device-messages]: iot-hub-intel-edison-kit-c-lesson4-send-cloud-to-device-messages.md
[gulpfile]: media/iot-hub-intel-edison-lessons/lesson4/updated_gulpfile_c.png
[on-and-off]: media/iot-hub-intel-edison-lessons/lesson4/gulp_on_and_off_c.png

