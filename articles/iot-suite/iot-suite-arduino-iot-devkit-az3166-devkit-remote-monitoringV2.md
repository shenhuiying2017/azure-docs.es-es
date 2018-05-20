---
title: 'IoT DevKit en la nube: conexión de IoT DevKit AZ3166 a Azure IoT Suite Remote Monitoring v2 | Microsoft Docs'
description: En este tutorial aprenderá a enviar el estado de los sensores de IoT DevKit AZ3166 a Remote Monitoring v2 desde Azure IoT Suite para su supervisión y visualización.
services: iot-hub
documentationcenter: ''
author: isabelcabezasm
manager: ''
tags: ''
keywords: ''
ms.service: iot-suite
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/03/2018
ms.author: isacabe
ms.openlocfilehash: 1b7c913b394bd89b9045c6b8fe9ac84c3cf09b91
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/14/2018
---
# <a name="connect-mxchip-iot-devkit-az3166-to-azure-iot-suite-for-remote-monitoring-v2"></a>Conexión de MXChip IoT DevKit AZ3166 a Azure IoT Suite para Remote Monitoring v2


[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

En este tutorial, aprenderá a ejecutar una aplicación de ejemplo en DevKit para enviar datos de sensor a Azure IoT Suite.

[MXChip IoT DevKit](https://aka.ms/iot-devkit) es una placa integral compatible de Arduino con periféricos y sensores varios. Se puede desarrollar para ella con la [extensión de Visual Studio Code para Arduino](https://aka.ms/arduino). Incluye un [catálogo de proyectos](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) cada vez mayor que sirve de guía para crear prototipos de soluciones de Internet de las cosas (IoT) que aprovechen los servicios de Microsoft Azure.

## <a name="what-you-need"></a>Lo que necesita

Siga la [Guía de introducción](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started) para:

* Conexión de DevKit con Wi-Fi
* Preparación del entorno de desarrollo


## <a name="open-the-remotemonitoring-sample"></a>Apertura del ejemplo de RemoteMonitoring

1. Desconecte el DevKit del equipo, si está conectado.

2. Inicie VS Code.

3. Conecte el DevKit al equipo. Código de VS detecta automáticamente el DevKit y abre las páginas siguientes:
  * Página de introducción del DevKit.
  * Ejemplos de Arduino: ejemplos prácticos para empezar a trabajar con DevKit.

4. Expanda la sección de la izquierda **ARDUINO EXAMPLES** (EJEMPLOS DE ARDUINO), vaya a **Examples for MXCHIP AZ3166 > AzureIoT** (Ejemplos para MXCHIP AZ3166 > AzureIoT) y seleccione **RemoteMonitoringv2**. Se abre una nueva ventana de VS Code que contiene la carpeta de proyecto.

  ![Abrir proyecto de supervisión remota](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-arduino-examples.png)


  > [!NOTE]
  > Si cierra el panel, puede volver a abrirlo. Utilice `Ctrl+Shift+P` (macOS: `Cmd+Shift+P`) para abrir la paleta de comandos, escriba **Arduino** y, a continuación, busque y seleccione **Arduino: ejemplos**.

## <a name="add-a-new-physical-device"></a>Incorporación de un dispositivo físico nuevo

En el portal, vaya a la sección **Devices** (Dispositivos) y allí, haga clic en el botón **+ New Device** (+Nuevo dispositivo). 

![Añadir un dispositivo nuevo](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-add-device.png)

Hay que rellenar el *formulario de dispositivo nuevo*.
1. Haga clic en **Physical** (Físico) en la sección *Device type* (Tipo de dispositivo).
2. Defina el identificador de su dispositivo (por ejemplo, *MXChip* o *AZ3166*).
3. Elija **Auto generate keys** (Generar claves automáticamente) en la sección *Authentication key* (Clave de autenticación).
4. Haga clic en el botón *Apply* (Aplicar).

![Añadir un nuevo formulario de dispositivo](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-add-new-device-form.png)

Espere hasta que el portal termine el aprovisionamiento del dispositivo nuevo.

![Aprovisionar un dispositivo nuevo ](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-add-device-provisioning.png)


A continuación, se mostrará la configuración del dispositivo nuevo.
Copie la **cadena de conexión** generada.

![Cadena de conexión de dispositivo](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-new-device-connstring.png)


Esta cadena de conexión se usará en la sección siguiente.





## <a name="build-and-upload-the-device-code"></a>Creación y carga del código del dispositivo

Vuelva a Visual Studio Code: 

1. Use `Ctrl+P` (macOS: `Cmd + P`) y escriba **task config-device-connection**.

  ![Elegir la suscripción de Azure y la instancia de IoT Hub](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/iot-suite-task-config-device-conexion.png)

2. El terminal le pregunta si desea usar la cadena de conexión del dispositivo IoT que desea usar. Seleccione *crea nueva*y péguela.

  ![pegar cadena de conexión](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/iot-suite-task-config-device-conexion-choose-iot-hub-press-button-A.png)

3. El terminal a veces le pide que especifique el modo de configuración. Para ello, mantenga presionado el botón A y pulse y suelte el botón de restablecimiento y libere el botón A. La pantalla muestra el identificador de DevKit y "Configuration" (Configuración).

  ![Pantalla de DevKit del dispositivo](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-devkit-screen.png)

  > [!NOTE]
  > Si ha seguido la última sección de este tutorial, la cadena de conexión se debe de haber guardado en el Portapapeles. Si no es así, debe ir a Azure Portal y buscar la instancia de IoT HUB del grupo de recursos Supervisión remota. Ahí puede ver los dispositivos conectados de IoT Hub y copiar la cadena de conexión del dispositivo.

  ![buscar la cadena de conexión](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-connection-string-of-a-device.png)


Ya puede ver el nuevo dispositivo físico en la sección de VS Code "Dispositivos de Azure IoT Hub":

![Observe el nuevo dispositivo de IoT Hub](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/iot-suite-new-iot-hub-device.png)

## <a name="test-the-project"></a>Prueba del proyecto

Cuando se ejecuta la aplicación de ejemplo, DevKit envía datos de sensor vía Wi-Fi a la instancia de Azure IoT Suite. Para ver el resultado, siga estos pasos:

1. Vaya a Azure IoT Suite y haga clic en **PANEL**.

2. En la consola de solución de Azure IoT Suite, verá el estado del sensor de DevKit. 

![Datos del sensor de Azure IoT Suite](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-dashboard.png)

Si hace clic en el nombre del sensor (AZ3166), se abre una pestaña en el lado derecho del panel, donde puede ver el gráfico de sensores MX Chip en tiempo real.


## <a name="send-a-c2d-message"></a>Envío de un mensaje C2D
Remote Monitoring v2 permite invocar un método remoto en el dispositivo.
El código de ejemplo de MX Chip publica tres métodos que se pueden ver en la sección Method (Método) cuando se selecciona el sensor.

![Métodos MX Chip](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-methods.png)

Para cambiar el color de uno de los LED de Chip MX, utilice el método "LedColor". Para ello, active la casilla del dispositivo y haga clic en el botón Schedule (Programar). 

![Métodos MX Chip](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-schedule.png)

En la lista desplegable donde en la que aparecen todos los métodos aparecen, elija el llamado ChangeColor, escriba un nombre y pulse Apply (Aplicar).

![Lista desplegable MX Chip](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/iot-suite-change-color.png)

En unos segundos, el LED RGB del dispositivo físico MX Chip debería cambiar de color (debajo del botón A)

![LED MX Chip](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-devkit-led.png)


## <a name="change-device-id"></a>Cambio del identificador del dispositivo

Puede cambiar el identificador del dispositivo en IoT Hub siguiendo [esta guía](https://microsoft.github.io/azure-iot-developer-kit/docs/customize-device-id/).


## <a name="problems-and-feedback"></a>Problemas y comentarios

Si tiene algún problema, acuda a [P+F](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/), o también puede ponerse en contacto con nosotros a través de los siguientes canales:

* [Gitter.im](http://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stackoverflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha aprendido cómo conectar un dispositivo de DevKit a Azure IoT Suite y visualizar los datos de sensor, aquí tiene los siguientes pasos sugeridos:

* [Información general de Azure IoT Suite](https://docs.microsoft.com/azure/iot-suite/)
* [Connect an MXChip IoT DevKit device to your Microsoft IoT Central application](https://docs.microsoft.com/en-us/microsoft-iot-central/howto-connect-devkit) (Conectar un dispositivo MXChip IoT DevKit a una aplicación de Microsoft IoT Central)
