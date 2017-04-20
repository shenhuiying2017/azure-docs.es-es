---
title: "Conexión de Intel Edison (Node) a Azure IoT: Lección 4: Parpadeo del LED | Microsoft Docs"
description: Personalice los mensajes para cambiar el comportamiento de encendido y apagado del LED.
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: control del LED con Arduino
ROBOTS: NOINDEX
redirect_url: /azure/iot-hub/iot-hub-intel-edison-kit-node-get-started
ms.assetid: 387cd97e-b05e-43c4-b252-f68ad45d524a
ms.service: iot-hub
ms.devlang: nodejs
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: adf5b10721a28432e6b37ef73c6a7e7ec9f93cdd
ms.openlocfilehash: 9a2c6dfe5d85a4da9714f9b8942cb2baf1a98466
ms.lasthandoff: 01/25/2017


---
# <a name="change-the-on-and-off-behavior-of-the-led"></a>Modificación del comportamiento de encendido y apagado del LED
## <a name="what-you-will-do"></a>Lo que hará
Personalice los mensajes para cambiar el comportamiento de encendido y apagado del LED. Si tiene problemas, busque soluciones en [esta página][troubleshooting].

## <a name="what-you-will-learn"></a>Lo qué aprenderá
Utilice las funciones adicionales para cambiar el comportamiento de encendido y apagado del LED.

## <a name="what-you-need"></a>Lo que necesita
Debe haber completado correctamente el tutorial sobre la [ejecución de una aplicación de ejemplo en Intel Edison para recibir mensajes de la nube al dispositivo][receive-cloud-to-device-messages].

## <a name="add-functions-to-appjs-and-gulpfilejs"></a>Adición de funciones a app.js y gulpfile.js
1. Abra la aplicación de ejemplo en Visual Studio Code ejecutando los comandos siguientes:

   ```bash
   cd Lesson4
   code .
   ```
2. Abra el archivo `app.js` y, luego, agregue las siguientes funciones después de la función blinkLED():

   ```javascript
   function turnOnLED() {
     myLed.write(1);
   }

   function turnOffLED() {
     myLed.write(0);
   }
   ```

   ![Archivo app.js con funciones agregadas](media/iot-hub-intel-edison-lessons/lesson4/updated_app_node.png)
3. Agregue las siguientes condiciones antes del case "blink" en el bloque switch-case de la función `receiveMessageCallback`:

   ```javascript
   case 'on':
     turnOnLED();
     break;
   case 'off':
     turnOffLED();
     break;
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

[troubleshooting]: iot-hub-intel-edison-kit-node-troubleshooting.md
[receive-cloud-to-device-messages]: iot-hub-intel-edison-kit-node-lesson4-send-cloud-to-device-messages.md
[gulpfile]: media/iot-hub-intel-edison-lessons/lesson4/updated_gulpfile_node.png
[on-and-off]: media/iot-hub-intel-edison-lessons/lesson4/gulp_on_and_off_node.png

