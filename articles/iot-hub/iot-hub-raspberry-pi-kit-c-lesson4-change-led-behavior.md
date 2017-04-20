---
title: "Conexión de Raspberry Pi (C) a Azure IoT: Lección 4: Modificación de la aplicación | Microsoft Docs"
description: Personalice los mensajes para cambiar el comportamiento de encendido y apagado del LED.
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: controlar el led con raspberry pi, control del led de raspberry pi, led de control de raspberry pi
ROBOTS: NOINDEX
redirect_url: /azure/iot-hub/iot-hub-raspberry-pi-kit-c-get-started
ms.assetid: 0201b8ed-d5e6-4445-9a4d-1305003d1eff
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 64e69df256404e98f6175f77357500b562d74318
ms.openlocfilehash: c1c999e40ecc92a1018067822be66f14b61a8c36
ms.lasthandoff: 01/24/2017


---
# <a name="change-the-on-and-off-behavior-of-the-led"></a>Modificación del comportamiento de encendido y apagado del LED
## <a name="what-you-will-do"></a>Lo que hará
Personalice los mensajes para cambiar el comportamiento de encendido y apagado del LED. Si tiene problemas, busque soluciones en la [página de solución de problemas](iot-hub-raspberry-pi-kit-c-troubleshooting.md).

## <a name="what-you-will-learn"></a>Lo qué aprenderá
Utilice las funciones adicionales de Node.js para cambiar el comportamiento de encendido y apagado del LED.

## <a name="what-you-need"></a>Lo que necesita
Para seguir este procedimiento, debe haber completado correctamente el tutorial sobre la [ejecución de una aplicación de ejemplo en Raspberry Pi para recibir mensajes de nube a dispositivo](iot-hub-raspberry-pi-kit-c-lesson4-send-cloud-to-device-messages.md).

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
     digitalWrite(LED_PIN, HIGH);
   }

   static void turnOffLED()
   {
     digitalWrite(LED_PIN, LOW);
   }
   ```

   ![Archivo main.c con funciones agregadas](media/iot-hub-raspberry-pi-lessons/lesson4/updated_app_c.png)
3. Agregue las siguientes condiciones antes de la predeterminada en el bloque `if` de la función `receiveMessageCallback`:

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

   ![Archivo Gulpfile.js con una función agregada](media/iot-hub-raspberry-pi-lessons/lesson4/updated_gulpfile_c.png)
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

![Aplicación de ejemplo con mensajes de encendido y apagado](media/iot-hub-raspberry-pi-lessons/lesson4/gulp_on_and_off_c.png)

¡Enhorabuena! Ha personalizado correctamente los mensajes que se envían a Pi desde IoT Hub.

### <a name="summary"></a>Resumen
En esta sección opcional, se explica cómo personalizar los mensajes para que la aplicación de ejemplo pueda controlar el comportamiento de encendido y apagado del LED de manera diferente.

