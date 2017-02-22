---
title: "Conexión de Raspberry Pi (Node) a Azure IoT: Lección 4: Modificación de la aplicación | Microsoft Docs"
description: Personalice los mensajes para cambiar el comportamiento de encendido y apagado del LED.
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: controlar el led con raspberry pi, control del led de raspberry pi, led de control de raspberry pi
ms.assetid: 3b42a4ad-0197-42f6-8ca9-04c883e879e8
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/28/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 64e69df256404e98f6175f77357500b562d74318
ms.openlocfilehash: 53c9408bedc6c61cdb0e755b46b3090e8723e271


---
# <a name="change-the-on-and-off-behavior-of-the-led"></a>Modificación del comportamiento de encendido y apagado del LED
## <a name="what-you-will-do"></a>Lo que hará
Personalice los mensajes para cambiar el comportamiento de encendido y apagado del LED. Si tiene problemas, puede encontrar soluciones en la [página de solución de problemas](iot-hub-raspberry-pi-kit-node-troubleshooting.md).

## <a name="what-you-will-learn"></a>Lo qué aprenderá
Utilice las funciones adicionales de Node.js para cambiar el comportamiento de encendido y apagado del LED.

## <a name="what-you-need"></a>Lo que necesita
Para seguir este procedimiento, debe haber completado correctamente el tutorial sobre la [ejecución de una aplicación de ejemplo en Raspberry Pi para recibir mensajes de la nube al dispositivo](iot-hub-raspberry-pi-kit-node-lesson4-send-cloud-to-device-messages.md).

## <a name="add-nodejs-functions"></a>Adición de funciones de Node.js
1. Abra la aplicación de ejemplo en Visual Studio Code ejecutando los comandos siguientes:
   
   ```bash
   cd Lesson4
   code .
   ```
2. Abra el archivo `app.js` y, después, agregue las siguientes funciones al final:
   
   ```javascript
   function turnOnLED() {
     wpi.digitalWrite(CONFIG_PIN, 1);
   }
   
   function turnOffLED() {
     wpi.digitalWrite(CONFIG_PIN, 0);
   }
   ```
   
   ![Archivo App.js con funciones agregadas](media/iot-hub-raspberry-pi-lessons/lesson4/updated_app_js.png)
3. Agregue las siguientes condiciones antes de la predeterminada en el bloque switch-case de la función `receiveMessageCallback`:
   
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
   
   ![Archivo Gulpfile.js con una función agregada](media/iot-hub-raspberry-pi-lessons/lesson4/updated_gulpfile.png)
5. En la función `sendMessage`, reemplace la línea `var message = buildMessage(sentMessageCount);` por la nueva línea que se muestra en el siguiente fragmento:
   
   ```javascript
   var message = buildCustomMessage(sentMessageCount);
   ```
6. Guarde todos los cambios.

### <a name="deploy-and-run-the-sample-application"></a>Implementación y ejecución de la aplicación de ejemplo
Implemente y ejecute la aplicación de ejemplo en PI mediante el comando siguiente:

```bash
gulp deploy && gulp run
```

El LED debería encenderse durante dos segundos y apagarse después otros dos segundos. El último mensaje "stop" detiene la ejecución de la aplicación de ejemplo.

![Aplicación de ejemplo con mensajes de encendido y apagado](media/iot-hub-raspberry-pi-lessons/lesson4/gulp_on_and_off.png)

¡Enhorabuena! Ha personalizado correctamente los mensajes que se envían a Pi desde IoT Hub.

### <a name="summary"></a>Resumen
En esta sección opcional, se explica cómo personalizar los mensajes para que la aplicación de ejemplo pueda controlar el comportamiento de encendido y apagado del LED de manera diferente.




<!--HONumber=Jan17_HO4-->


