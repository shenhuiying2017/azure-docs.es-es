---
title: "Creación e implementación de la aplicación de intermitencia en el kit de inicio de IoT de Azure | Microsoft Docs"
description: Configure Intel Edison para usarlo por primera vez.
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "configuración de Arduino, conexión de Arduino al equipo, configuración de Arduino, placa Arduino"
ms.assetid: 372c9b6d-e701-4ff6-8151-d262aa76aa55
ms.service: iot-hub
ms.devlang: nodejs
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/7/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: f45b3bf00d619376ac07418f0c02eca5f3241939
ms.openlocfilehash: 4511f88bc6a3e71fe3ea9375711dade15fd4ef70


---
# <a name="configure-your-intel-edison"></a>Configuración de Intel Edison
## <a name="what-you-will-do"></a>Lo que hará
Configure Intel Edison para usarlo por primera vez ensamblando la placa, encendiéndola e instalando la herramienta de configuración en el sistema operativo de escritorio. El objetivo es actualizar el firmware de Edison, establecer su contraseña y conectarlo a una red Wi-Fi. Si tiene problemas, busque soluciones en [esta página][troubleshooting].

## <a name="what-you-will-learn"></a>Lo qué aprenderá
En este artículo, aprenderá lo siguiente:

* Cómo ensamblar la placa Edison y encenderla.
* Cómo actualizar el firmware de Edison, establecer la contraseña y conectarse a una red Wi-Fi.

## <a name="what-you-need"></a>Lo que necesita
Para completar esta operación, necesita las siguientes piezas del kit de inicio de Intel Edison:

* El módulo Intel® Edison.
* La placa de expansión de Arduino.
* Cualquiera de las piezas intermedias o los tornillos incluidos en el paquete, por ejemplo, los dos tornillos para fijar el módulo a la placa de expansión y los cuatro conjuntos de tornillos y separadores de plástico.
* Un cable USB Micro B a Tipo A.
* Una fuente de alimentación de corriente continua (CC). La fuente de alimentación debe ser del siguiente tipo:
  - 7-15 V CC.
  - Un mínimo de 1500 mA.
  - La clavija central o interna debe ser el polo positivo de la fuente de alimentación.

  ![Los elementos del kit de inicio](media/iot-hub-intel-edison-lessons/lesson1/kit.png)

También necesita lo siguiente:

* Un equipo con Windows, Mac o Linux.
* Una conexión inalámbrica para establecer conexión entre Edison y el equipo.
* Una conexión a Internet para descargar la herramienta de configuración.

## <a name="assemble-your-board"></a>Ensamblaje de la placa

En esta sección encontrará los pasos necesarios para conectar el módulo Intel® Edison a la placa de expansión.

1. Coloque el módulo Intel® Edison dentro del contorno blanco de la placa de expansión, de forma que los agujeros del módulo queden alineados con los tornillos de la placa expansión.

2. Presione hacia abajo en el módulo, justo debajo de la frase `What will you make?`, hasta que oiga un chasquido.

   ![Ensamblado de la placa 2](media/iot-hub-intel-edison-lessons/lesson1/assemble_board2.jpg)

3. Utilice las dos tuercas hexagonales (incluidas en el paquete) para fijar el módulo a la placa de expansión.

   ![Ensamblado de la placa 3](media/iot-hub-intel-edison-lessons/lesson1/assemble_board3.jpg)

4. Inserte un tornillo en uno de los cuatro agujeros de las esquinas de la placa de expansión. Gire y apriete uno de los separadores de plástico blancos sobre el tornillo.

   ![Ensamblado de la placa 4](media/iot-hub-intel-edison-lessons/lesson1/assemble_board4.jpg)

5. Repita el paso anterior con los otros tres separadores de las esquinas.

   ![Ensamblado de la placa 5](media/iot-hub-intel-edison-lessons/lesson1/assemble_board5.jpg)

Ya ha ensamblado la placa.

   ![Placa ensamblada](media/iot-hub-intel-edison-lessons/lesson1/assembled_board.jpg)

## <a name="power-up-edison"></a>Encendido de Edison

1. Conecte la fuente de alimentación.

   ![Conexión de la fuente de alimentación](media/iot-hub-intel-edison-lessons/lesson1/plug_power.jpg)

2. Un LED verde (con la etiqueta DS1 en la placa de expansión de Arduino*) debería encenderse y permanecer encendido.

3. Espere un minuto para que termine de inicializarse la placa.

   > [!NOTE]
   > Si no tiene una fuente de alimentación de CC, puede encenderla usando un puerto USB. Consulte la sección `Connect Edison to your computer` para más información. La placa puede presentar un comportamiento imprevisible si se enciende de este modo, especialmente cuando se utilizan redes Wi-Fi o motores de accionamiento.

## <a name="connect-edison-to-your-computer"></a>Conexión de Edison a un equipo

1. Mueva hacia abajo el microconmutador (es decir, hacia los dos puertos microUSB) para activar el modo de dispositivo de Edison. Para ver las diferencias entre los modos host y dispositivo, consulte [esta página](https://software.intel.com/en-us/node/628233#usb-device-mode-vs-usb-host-mode).

   ![Desplazamiento hacia abajo del microconmutador](media/iot-hub-intel-edison-lessons/lesson1/toggle_down_microswitch.jpg)

2. Conecte el cable microUSB al puerto microUSB superior.

   ![Puerto microUSB superior](media/iot-hub-intel-edison-lessons/lesson1/top_usbport.jpg)

3. Conecte el otro extremo del cable USB al equipo.

   ![Cable USB conectado a un equipo](media/iot-hub-intel-edison-lessons/lesson1/computer_usb.jpg)

4. Sabrá que la placa se ha terminado de inicializar cuando en el equipo aparezca una unidad nueva (es muy parecido a cuando se inserta una tarjeta SD en el equipo).

## <a name="download-and-run-the-configuration-tool"></a>Descarga y ejecución de la herramienta de configuración
Obtenga la herramienta de configuración más reciente haciendo clic [aquí](https://software.intel.com/en-us/iot/hardware/edison/downloads) (los vínculos se encuentran bajo el título `Installers`). Ejecute la herramienta y siga las instrucciones que aparecen en pantalla. Vaya haciendo clic en Next (Siguiente) según proceda.

### <a name="flash-firmware"></a>Actualización del firmware
1. En la página `Set up options`, haga clic en `Flash Firmware`.
2. Seleccione la imagen que se instalará en la placa realizando una de las siguientes acciones:
   - Para descargar e instalar en la placa la imagen del firmware más reciente de Intel, seleccione `Download the latest image version xxxx`.
   - Para actualizar el firmware de la placa con una imagen que ya ha guardado en el equipo, seleccione `Select the local image`. Busque y seleccione la imagen con la que va a actualizar el firmware de la placa.
3. La herramienta de instalación tratará de actualizar el firmware de la placa. La duración del proceso puede ser de hasta 10 minutos.

### <a name="set-password"></a>Establecimiento de la contraseña
1. En la página `Set up options`, haga clic en `Enable Security`.
2. Puede establecer un nombre personalizado para la placa Intel® Edison. Esto es opcional.
3. Escriba una contraseña para la placa y, luego, haga clic en `Set password`.
4. Anote la contraseña, ya que la usará más adelante.

### <a name="connect-wi-fi"></a>Conexión a una red Wi-Fi
1. En la página `Set up options`, haga clic en `Connect Wi-Fi`. Espere, como máximo, un minuto, ya que el equipo buscará las redes Wi-Fi disponibles.
2. En la lista desplegable `Detected Networks`, seleccione su red.
3. En la lista desplegable `Security`, seleccione el tipo de seguridad de la red.
4. Proporcione los datos de inicio de sesión y contraseña. Después, haga clic en `Configure Wi-Fi`.
5. Anote la dirección IP, ya que la usará más adelante.

> [!NOTE]
> Asegúrese de que Edison se conecta a la misma red que el equipo. El equipo se conectará a Edison mediante la dirección IP.

¡Enhorabuena! Ha configurado correctamente Edison.

## <a name="summary"></a>Resumen
En este artículo ha aprendido a ensamblar la placa Edison, actualizar su firmware, configurar la contraseña y conectar la placa a una red Wi-Fi mediante la herramienta de configuración. Tenga en cuenta que todavía el LED no se encenderá. En la siguiente tarea, instalará las herramientas y el software necesarios como preparativo para ejecutar una aplicación de ejemplo en Edison.

## <a name="next-steps"></a>Pasos siguientes
[Obtención de las herramientas][get-the-tools]
<!-- Images and links -->

[troubleshooting]: iot-hub-intel-edison-kit-node-troubleshooting.md
[get-the-tools]: iot-hub-intel-edison-kit-node-lesson1-get-the-tools-win32.md


<!--HONumber=Dec16_HO2-->


