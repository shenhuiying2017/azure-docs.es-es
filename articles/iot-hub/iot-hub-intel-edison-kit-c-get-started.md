---
title: 'Intel Edison en la nube (C): Conectar Intel Edison a Azure IoT Hub | Microsoft Docs'
description: "Con este tutorial aprenderá a configurar y conectar Intel Edison a Azure IoT Hub para que envíe datos a la plataforma en la nube de Azure."
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: azure iot intel edison, intel edison iot hub, intel edison enviar datos a la nube, intel edison en la nube
ms.assetid: 4885fa2c-c2ee-4253-b37f-ccd55f92b006
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 4/17/2017
ms.author: xshi
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: edbdbe0230f742cd7228f04a4a83c9bd567527e8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="connect-intel-edison-to-azure-iot-hub-c"></a>Conectar Intel Edison a Azure IoT Hub (C)

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

En este tutorial, empiece por aprender los conceptos básicos sobre cómo trabajar con Intel Edison. A continuación, aprenderá a conectar sin problemas los dispositivos en la nube con [Azure IoT Hub](iot-hub-what-is-iot-hub.md).

¿Aún no tiene un kit? Comience [aquí](https://azure.microsoft.com/develop/iot/starter-kits)

## <a name="what-you-do"></a>Qué debe hacer

* Configure Intel Edison y los módulos de Grove.
* Cree un IoT Hub.
* Registre un dispositivo para Edison en IoT Hub.
* Ejecute una aplicación de ejemplo en Edison para enviar datos de sensor a IoT Hub.

Conecte Intel Edison al IoT Hub que ha creado. Luego ejecute una aplicación de ejemplo en Edison para recopilar datos de temperatura y humedad de un sensor de temperatura Grove. Por último, envíe los datos del sensor a IoT Hub.

## <a name="what-you-learn"></a>Conocimientos que adquirirá

* Cómo crear Azure IoT Hub y obtener la cadena de conexión del nuevo dispositivo.
* Cómo conectar Edison a un sensor de temperatura Grove.
* Cómo recopilar datos del sensor al ejecutar una aplicación de ejemplo en Edison.
* Cómo enviar los datos del sensor a IoT Hub.

## <a name="what-you-need"></a>Lo que necesita

![Lo que necesita](media/iot-hub-intel-edison-kit-c-get-started/0_kit.png)

* La placa de Intel Edison
* La placa de expansión de Arduino.
* Una suscripción de Azure activa. Si no tiene ninguna cuenta de Azure, [cree una cuenta de evaluación gratuita de Azure](https://azure.microsoft.com/free/) en solo unos minutos.
* Un equipo PC o Mac con Windows o Linux.
* Una conexión a Internet.
* Un cable USB Micro B a Tipo A.
* Una fuente de alimentación de corriente continua (CC). La fuente de alimentación debe ser del siguiente tipo:
  - 7-15 V CC.
  - Un mínimo de 1500 mA.
  - La clavija central o interna debe ser el polo positivo de la fuente de alimentación.

Los elementos siguientes son opcionales:

* Grove Base Shield V2
* Grove: sensor de temperatura
* Cable de Grove
* Cualquiera de las piezas intermedias o los tornillos incluidos en el paquete, por ejemplo, los dos tornillos para fijar el módulo a la placa de expansión y los cuatro conjuntos de tornillos y separadores de plástico.

> [!NOTE] 
Estos elementos son opcionales porque el ejemplo de código simula los datos del sensor.

[!INCLUDE [iot-hub-get-started-create-hub-and-device](../../includes/iot-hub-get-started-create-hub-and-device.md)]

## <a name="setup-intel-edison"></a>Configurar Intel Edison

### <a name="assemble-your-board"></a>Ensamblaje de la placa

En esta sección encontrará los pasos necesarios para conectar el módulo Intel® Edison a la placa de expansión.

1. Coloque el módulo Intel® Edison dentro del contorno blanco de la placa de expansión, de forma que los agujeros del módulo queden alineados con los tornillos de la placa expansión.

2. Presione hacia abajo en el módulo, justo debajo de la frase `What will you make?`, hasta que oiga un chasquido.

   ![Ensamblado de la placa 2](media/iot-hub-intel-edison-kit-c-get-started/1_assemble_board2.jpg)

3. Utilice las dos tuercas hexagonales (incluidas en el paquete) para fijar el módulo a la placa de expansión.

   ![Ensamblado de la placa 3](media/iot-hub-intel-edison-kit-c-get-started/2_assemble_board3.jpg)

4. Inserte un tornillo en uno de los cuatro agujeros de las esquinas de la placa de expansión. Gire y apriete uno de los separadores de plástico blancos sobre el tornillo.

   ![Ensamblado de la placa 4](media/iot-hub-intel-edison-kit-c-get-started/3_assemble_board4.jpg)

5. Repita el paso anterior con los otros tres separadores de las esquinas.

   ![Ensamblado de la placa 5](media/iot-hub-intel-edison-kit-c-get-started/4_assemble_board5.jpg)

Ya ha ensamblado la placa.

   ![Placa ensamblada](media/iot-hub-intel-edison-kit-c-get-started/5_assembled_board.jpg)

### <a name="connect-the-grove-base-shield-and-the-temperature-sensor"></a>Conectar Grove Base Shield y el sensor de temperatura

1. Coloque Grove Base Shield en la placa. Asegúrese de que todas las patillas estén bien conectadas a la placa.
   
   ![Grove Base Shield](media/iot-hub-intel-edison-kit-c-get-started/6_grove_base_sheild.jpg)

2. Use el cable de Grove para conectar el sensor de temperatura Grove al puerto **A0** de Grove Base Shield.

   ![Conectar al sensor de temperatura](media/iot-hub-intel-edison-kit-c-get-started/7_temperature_sensor.jpg)
   
   ![Conexión de Edison y el sensor](media/iot-hub-intel-edison-kit-c-get-started/16_edion_sensor.png)

Ahora el sensor está listo.

### <a name="power-up-edison"></a>Encendido de Edison

1. Conecte la fuente de alimentación.

   ![Conexión de la fuente de alimentación](media/iot-hub-intel-edison-kit-c-get-started/8_plug_power.jpg)

2. Un LED verde (con la etiqueta DS1 en la placa de expansión de Arduino*) debería encenderse y permanecer encendido.

3. Espere un minuto para que termine de inicializarse la placa.

   > [!NOTE]
   > Si no tiene una fuente de alimentación de CC, puede encenderla usando un puerto USB. Consulte la sección `Connect Edison to your computer` para más información. La placa puede presentar un comportamiento imprevisible si se enciende de este modo, especialmente cuando se utilizan redes Wi-Fi o motores de accionamiento.

### <a name="connect-edison-to-your-computer"></a>Conexión de Edison a un equipo

1. Mueva hacia abajo el microconmutador (es decir, hacia los dos puertos microUSB) para activar el modo de dispositivo de Edison. Para ver las diferencias entre los modos host y dispositivo, consulte [esta página](https://software.intel.com/en-us/node/628233#usb-device-mode-vs-usb-host-mode).

   ![Desplazamiento hacia abajo del microconmutador](media/iot-hub-intel-edison-kit-c-get-started/9_toggle_down_microswitch.jpg)

2. Conecte el cable microUSB al puerto microUSB superior.

   ![Puerto microUSB superior](media/iot-hub-intel-edison-kit-c-get-started/10_top_usbport.jpg)

3. Conecte el otro extremo del cable USB al equipo.

   ![Cable USB conectado a un equipo](media/iot-hub-intel-edison-kit-c-get-started/11_computer_usb.jpg)

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

   ![Conectar al sensor de temperatura](media/iot-hub-intel-edison-kit-c-get-started/12_configuration_tool.png)

¡Enhorabuena! Ha configurado correctamente Edison.

## <a name="run-a-sample-application-on-intel-edison"></a>Ejecutar una aplicación de ejemplo en Intel Edison

### <a name="prepare-the-azure-iot-device-sdk"></a>Preparar el SDK de dispositivo IoT de Azure

1. Use uno de los siguientes clientes SSH del equipo host para conectar con Intel Edison. La dirección IP es la de la herramienta de configuración y la contraseña es la que ha establecido en esa herramienta.
    - [PuTTY](http://www.putty.org/) para Windows.
    - El cliente de SSH integrado en Ubuntu o macOS (ejecute `ssh root@"the IP address"`).

2. Clone la aplicación cliente de ejemplo en el dispositivo. 
   
   ```bash
   git clone https://github.com/Azure-Samples/iot-hub-c-intel-edison-client-app.git
   ```

3. Luego vaya a la carpeta de repositorio para ejecutar el comando siguiente a fin de compilar el SDK IoT de Azure.

   ```bash
   cd iot-hub-c-intel-edison-client-app
   sed -i -e 's/\r$//' buildSDK.sh
   chmod 755 buildSDK.sh
   ./buildSDK.sh
   ```

### <a name="configure-the-sample-application"></a>Configurar la aplicación de ejemplo

1. Abra el archivo config mediante la ejecución de los comandos siguientes:

   ```bash
   nano config.h
   ```

   ![Archivo config](media/iot-hub-intel-edison-kit-c-get-started/13_configure_file.png)

   Hay dos macros en este archivo que se pueden configurar. La primera es `INTERVAL`, que define el intervalo de tiempo entre dos mensajes que se envían a la nube. La segunda es `SIMULATED_DATA`, un valor booleano que indica si se usan los datos de sensor simulados o no.

   Si **no tiene el sensor**, establezca el valor `SIMULATED_DATA` en `1` para que la aplicación de ejemplo cree y use datos de sensor simulados.

2. Guarde y salga al presionar Control-O > Entrar > Control-X.

### <a name="build-and-run-the-sample-application"></a>Compilar y ejecutar la aplicación de ejemplo

1. Compile la aplicación de ejemplo al ejecutar el comando siguiente:

   ```bash
   cmake . && make
   ```
   ![Resultado de la compilación](media/iot-hub-intel-edison-kit-c-get-started/14_build_output.png)

1. Ejecute la aplicación de ejemplo mediante el comando siguiente:

   ```bash
   sudo ./app '<your Azure IoT hub device connection string>'
   ```

   > [!NOTE] 
   Asegúrese de que copia y pega la cadena de conexión del dispositivo entre las comillas simples.

Debería ver el resultado siguiente, que muestra los datos del sensor y los mensajes que se envían a IoT Hub.

![Resultado: datos de sensor enviados desde Intel Edison a IoT Hub](media/iot-hub-intel-edison-kit-c-get-started/15_message_sent.png)

## <a name="next-steps"></a>Pasos siguientes

Ha ejecutado una aplicación de ejemplo para recopilar datos de sensor y enviarlos a IoT Hub.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
