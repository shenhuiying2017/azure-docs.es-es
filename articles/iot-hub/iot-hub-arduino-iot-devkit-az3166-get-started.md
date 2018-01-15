---
title: "IoT DevKit en la nube: conexión de IoT DevKit AZ3166 a Azure IoT Hub | Microsoft Docs"
description: "Con este tutorial aprenderá a configurar y conectar IoT DevKit AZ3166 a Azure IoT Hub para que envíe datos a la plataforma en la nube de Azure."
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: 
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/20/2017
ms.author: xshi
ms.openlocfilehash: 1ad9200376bc4959e7572ae8d5be11b278a42cc0
ms.sourcegitcommit: 176c575aea7602682afd6214880aad0be6167c52
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/09/2018
---
# <a name="connect-iot-devkit-az3166-to-azure-iot-hub-in-the-cloud"></a>Conexión de IoT DevKit AZ3166 a Azure IoT Hub en la nube

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

Se puede usar [MXChip IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/) para desarrollar y crear prototipos de soluciones de Internet de las cosas (IoT) que aprovechen los servicios de Microsoft Azure. Incluye una placa compatible con Arduino con periféricos y sensores avanzados, un paquete de placa de código abierto y un [catálogo de proyectos](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) en aumento.

## <a name="what-you-do"></a>Qué debe hacer
Conecte el [DevKit](https://microsoft.github.io/azure-iot-developer-kit/) a la instancia de Azure IoT Hub en la que creará y recopilará los datos de temperatura y humedad de los sensores para enviarlos al centro de IoT.

¿Aún no tiene un DevKit? [Pruebe el simulador de DevKit](https://azure-samples.github.io/iot-devkit-web-simulator/) u [obtenga uno](https://aka.ms/iot-devkit-purchase).

## <a name="what-you-learn"></a>Conocimientos que adquirirá

* Cómo conectar el IoT DevKit a un punto de acceso inalámbrico y preparar el entorno de desarrollo.
* Cómo crear un centro de IoT y registrar un dispositivo para el MXChip IoT DevKit.
* Cómo recopilar datos del sensor mediante la ejecución de una aplicación de ejemplo en el MXChip IoT DevKit.
* Cómo enviar los datos del sensor al centro de IoT.

## <a name="what-you-need"></a>Lo que necesita

* Una placa MXChip IoT DevKit con un cable microUSB. [Obténgalo ahora](https://aka.ms/iot-devkit-purchase).
* Un equipo con Windows 10 o macOS 10.10 o versiones posteriores.
* Una suscripción de Azure activa. [Active una cuenta de Microsoft Azure de prueba de 30 días gratis](https://azureinfo.microsoft.com/us-freetrial.html).
  

## <a name="prepare-your-hardware"></a>Preparar el hardware

Conecte el hardware al equipo.

Necesita este hardware:

* Placa DevKit
* Cable microUSB

![Requisitos de hardware](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/hardware.jpg)

Para conectar el DevKit en el equipo:

1. Conecte el extremo USB en el equipo.
2. Conecte el extremo microUSB al DevKit.
3. El LED verde de alimentación confirma la conexión.

![Conexiones de hardware](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connect.jpg)

## <a name="configure-wi-fi"></a>Configuración de Wi-Fi

Los proyectos de IoT se basan en la conectividad a Internet. Siga estas instrucciones para configurar el DevKit para conectarse a la red Wi-Fi.

### <a name="enter-ap-mode"></a>Entrada al modo AP

Mantenga presionado el botón B, presione y suelte el botón de restablecimiento y suelte el botón B. El DevKit pasa al modo AP para la configuración de la red Wi-Fi. La pantalla muestra el identificador de red SSID del DevKit y la dirección IP del portal de configuración.

![Botón de restablecimiento, botón B y SSID](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/wifi-ap.jpg)

### <a name="connect-to-devkit-ap"></a>Conexión a DevKit AP

Ahora, use otro dispositivo con la red Wi-Fi habilitada (un equipo o teléfono móvil) para conectarse al SSID del DevKit (resaltado en la imagen anterior). Deje la contraseña en blanco.

![Información de red y botón Conectar](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connect-ssid.png)

### <a name="configure-wi-fi-for-the-devkit"></a>Configuración de la red Wi-Fi para el DevKit

Abra la dirección IP que aparece en la pantalla del DevKit en el explorador del equipo o del teléfono móvil, seleccione la red Wi-Fi a la que quiera que se conecte el DevKit y escriba la contraseña. Seleccione **Conectar**.

![Cuadro de contraseña y botón Conectar](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/wifi-portal.png)

Cuando la conexión se realiza correctamente, el DevKit se reinicia en unos segundos. Verá el nombre de la red Wi-Fi y la dirección IP en la pantalla:

![Nombre de la red Wi-Fi y dirección IP](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/wifi-ip.jpg)

> [!NOTE] 
> La dirección IP mostrada en la imagen puede no coincidir con la dirección IP real asignada que se muestre en la pantalla del DevKit. Es normal, ya que la red Wi-Fi usa DHCP para asignar las direcciones IP dinámicamente.

Una vez configurada la red Wi-Fi, las credenciales se conservarán en el dispositivo para esa conexión, aunque se desconecte el dispositivo. Por ejemplo, si configura el DevKit para la red Wi-Fi del hogar y se lo lleva a la oficina, debe volver a configurar el modo AP (comenzando por el paso "Entrada al modo AP") para conectar el DevKit a la red Wi-Fi de la oficina. 

## <a name="start-using-the-devkit"></a>Empezar a usar el DevKit

La aplicación predeterminada que se ejecuta en el DevKit comprueba la versión más reciente del firmware y muestra algunos datos de diagnóstico del sensor.

### <a name="upgrade-to-the-latest-firmware"></a>Actualización al firmware más reciente

> [!NOTE] 
> A partir de la versión 1.1, DevKit permite ST-SAFE en el cargador de arranque. Es probable que deba actualizar el firmware si está ejecutando la versión 1.1 para que funcione.

Si necesita una actualización de firmware, la pantalla mostrará las versiones de firmware más reciente y la actual. Para la actualización, siga la guía de [Actualización de firmware](https://microsoft.github.io/azure-iot-developer-kit/docs/firmware-upgrading/).

![Presentación de las versiones de firmware más reciente y actual](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/firmware.jpg)

> [!NOTE] 
> Esta tarea solo se realiza una vez. Cuando comience el desarrollo en el DevKit y cargue la aplicación, tendrá el firmware más reciente, que viene con la aplicación.

### <a name="test-various-sensors"></a>Prueba de varios sensores

Presione el botón B para probar los sensores. Continúe presionando y soltando el botón B para recorrer en iteración cada sensor.

![Mostrar botón B y sensor](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/sensors.jpg)

## <a name="prepare-the-development-environment"></a>Preparación del entorno de desarrollo

Ahora es el momento de configurar el entorno de desarrollo: herramientas y paquetes para compilar aplicaciones de IoT sorprendentes. Puede elegir la versión de Windows o macOS según el sistema operativo.

### <a name="windows"></a>Windows

Se recomienda usar el paquete de instalación para preparar el entorno de desarrollo. Si tiene algún problema, puede seguir los [pasos del manual](https://microsoft.github.io/azure-iot-developer-kit/docs/installation/) para hacerlo.

#### <a name="download-the-latest-package"></a>Descarga del paquete más reciente

El archivo .zip que descarga contiene todos los paquetes y herramientas necesarios para el desarrollo en el DevKit.

> [!div class="button"]
[Descargar](https://aka.ms/devkit/prod/installpackage/latest)

El archivo .zip contiene las siguientes herramientas y paquetes. Si ya tiene algunos componentes instalados, el script los detectará y los omitirá.

* Node.js y Yarn: sistema de tiempo de ejecución del script de configuración y tareas automatizadas.
* [MSI de la CLI de Azure 2.0](https://docs.microsoft.com//cli/azure/install-azure-cli#windows): experiencia de línea de comandos multiplataforma para administrar recursos de Azure. El archivo MSI contiene los dependientes Python y pip.
* [Visual Studio Code](https://code.visualstudio.com/) (VS Code): editor de código ligero para el desarrollo de DevKit.
* [Extensión de Visual Studio Code para Arduino](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-arduino): extensión que habilita el desarrollo para Arduino en Visual Studio Code.
* [IDE de Arduino](https://www.arduino.cc/en/Main/Software): la extensión para Arduino se basa en esta herramienta.
* Paquete de la placa DevKit: cadenas de herramientas, bibliotecas y proyectos para el DevKit.
* Utilidad ST-Link: controladores y utilidades esenciales.

#### <a name="run-the-installation-script"></a>Ejecución del script de instalación

En el Explorador de archivos de Windows, busque el archivo .zip y extráigalo. Busque `install.cmd`, haga clic con el botón derecho en él y seleccione **Ejecutar como administrador**.

![Explorador de archivos](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/run-admin.png)

Durante la instalación verá el progreso de cada herramienta o paquete.

![Progreso de la instalación](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install.png)

> [!NOTE] 
> Dependiendo de su entorno, puede ser que se produzca un error al instalar Arduino IDE. Si esto sucede, puede intentar [instalar Arduino IDE por separado](https://microsoft.github.io/azure-iot-developer-kit/docs/installation/#windows) y volver a ejecutar install.cmd. Si no es así, siga los [pasos que encontrará en el manual](https://microsoft.github.io/azure-iot-developer-kit/docs/installation/#windows) para instalar todas las herramientas y todos los paquetes necesarios.

#### <a name="install-drivers"></a>Instalación de controladores

La extensión VS Code para Arduino se basa en el IDE de Arduino. Si se trata de la primera vez que se va a instalar el IDE de Arduino, se le pedirá que instale los controladores correspondientes:

![getting-started-driver](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/driver.png)

Tardará unos 10 minutos en completarse la instalación, en función de la velocidad de Internet. Una vez terminada, debería ver los accesos directos a Visual Studio Code y al IDE de Arduino en el escritorio.

> [!NOTE] 
> En ocasiones, al iniciar VS Code, aparece un error que indica que no se encuentra el IDE de Arduino o un paquete de la placa. Para solucionarlo, cierre VS Code y reinicie el IDE de Arduino. Ahora VS Code debe encontrar la ruta de acceso del IDE de Arduino correctamente.

### <a name="macos"></a>macOS

Se recomienda usar la experiencia de instalación con un solo clic para preparar el entorno de desarrollo. Si tiene algún problema, puede seguir los [pasos del manual](https://microsoft.github.io/azure-iot-developer-kit/docs/installation/) para hacerlo.

#### <a name="install-homebrew"></a>Instalación de Homebrew

> [!NOTE] 
> Si ha instalado Homebrew, puede omitir este paso.

Siga las [instrucciones de instalación de Homebrew](https://docs.brew.sh/Installation.html) para realizar la instalación.

#### <a name="download-the-latest-package"></a>Descarga del paquete más reciente
El archivo .zip que descarga contiene todos los paquetes y herramientas necesarios para el desarrollo en el DevKit.

> [!div class="button"]
[Descargar](https://aka.ms/devkit/prod/installpackage/mac/latest)

El archivo .zip contiene las siguientes herramientas y paquetes. Si ya tiene algunos componentes instalados, el script los detectará y los omitirá.

* Node.js y Yarn: sistema de tiempo de ejecución del script de configuración y tareas automatizadas.
* [CLI de Azure 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest#a-namemacosinstall-on-macos): experiencia de línea de comandos multiplataforma para administrar recursos de Azure.
* [Visual Studio Code](https://code.visualstudio.com/) (VS Code): editor de código ligero para el desarrollo de DevKit.
* [Extensión de Visual Studio Code para Arduino](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-arduino): extensión que habilita el desarrollo para Arduino en Visual Studio Code.
* [IDE de Arduino](https://www.arduino.cc/en/Main/Software): la extensión para Arduino se basa en esta herramienta.
* Paquete de la placa DevKit: cadenas de herramientas, bibliotecas y proyectos para el DevKit.
* Utilidad ST-Link: controladores y utilidades esenciales.

#### <a name="run-the-installation-script"></a>Ejecución del script de instalación

En el Buscador, busque el .zip y extráigalo:

![macOS finder](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/mac-finder.png)

Inicie la aplicación Terminal, busque la carpeta en la que extrajo el archivo .zip y ejecute:

```bash
./install.sh
```

![instalación en macOS](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/mac-install-sh.png)

> [!NOTE] 
> Si se produce un error de permiso de Homebrew, ejecute `brew doctor` para corregirlo. Consulte la sección [Preguntas más frecuentes](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#homebrew-permission-error-on-macos) para obtener más información.

Ahora tiene todas las herramientas y los paquetes necesarios instalados para macOS.


## <a name="open-the-project-folder"></a>Apertura de la carpeta del proyecto

### <a name="start-vs-code"></a>Inicio de VS Code

Asegúrese de que el DevKit no está conectado. Primero, inicie VS Code y conecte el DevKit al equipo. VS Code busca automáticamente el DevKit y abre una página de introducción:

![Página de introducción](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/vscode_start.png)

> [!NOTE] 
> En ocasiones, al iniciar VS Code, aparece un error que indica que no se encuentra el IDE de Arduino o un paquete de la placa. Cierre VS Code y reinicie el IDE de Arduino. Ahora VS Code debe encontrar la ruta de acceso del IDE de Arduino correctamente.


### <a name="open-the-arduino-examples-folder"></a>Apertura de la carpeta de ejemplos de Arduino

En la pestaña **Ejemplos de Arduino**, vaya a **Ejemplos para MXCHIP AZ3166** > **AzureIoT** y seleccione **GetStarted**.

![Pestaña Ejemplos de Arduino](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/vscode_start.png)

Si cierra el panel, puede volver a abrirlo. Utilice `Ctrl+Shift+P` (macOS: `Cmd+Shift+P`) para abrir la paleta de comandos, escriba **Arduino** y, a continuación, busque y seleccione **Arduino: ejemplos** .

## <a name="provision-azure-services"></a>Aprovisionamiento de los servicios de Azure

En la ventana de la solución, ejecute la tarea mediante `Ctrl+P` (macOS: `Cmd+P`) escribiendo `task cloud-provision`.

En el terminal de VS Code, una línea de comandos interactiva le guía durante el aprovisionamiento de los servicios de Azure necesarios:

![Línea de comandos interactiva](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/cloud-provision.png)

## <a name="build-and-upload-the-arduino-sketch"></a>Creación y carga del boceto de Arduino

### <a name="windows"></a>Windows

1. Use `Ctrl+P` para ejecutar `task device-upload`.
2. El terminal le pide que especifique el modo de configuración. Para ello, mantenga presionado el botón A, y presione y suelte el botón de restablecimiento. La pantalla muestra el identificador de DevKit y "Configuration" (Configuración).

Esto sirve para establecer la cadena de conexión que se recupera del paso `task cloud-provision`.

A continuación, VS Code inicia la comprobación y la carga del boceto de Arduino:

![Comprobación y carga del boceto de Arduino](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/device-upload.png)

El DevKit se reinicia y comienza a ejecutar el código.

> [!NOTE] 
> En ocasiones, obtendrá el error "Error: AZ3166: Paquete desconocido". Esto se debe a que el índice del panel no está actualizado. Active esta casilla [FAQ steps](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) (Pasos de preguntas más frecuentes) para resolverlo.

### <a name="macos"></a>macOS

1. Ponga DevKit en modo de configuración: mantenga presionado el botón A y presione y suelte el botón de restablecimiento. La pantalla muestra "Configuration" (Configuración).
2. Use `Cmd+P` para ejecutar `task device-upload`.

Esto sirve para establecer la cadena de conexión que se recupera del paso `task cloud-provision`.

A continuación, VS Code inicia la comprobación y la carga del boceto de Arduino:

![carga-dispositivo](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/device-upload.png)

El DevKit se reinicia y comienza a ejecutar el código.

> [!NOTE] 
> En ocasiones, obtendrá el error "Error: AZ3166: Paquete desconocido". Esto se debe a que el índice del panel no está actualizado. Active esta casilla [FAQ steps](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) (Pasos de preguntas más frecuentes) para resolverlo.


## <a name="test-the-project"></a>Prueba del proyecto

En VS Code, siga estos pasos para abrir y configurar Serial Monitor (Monitor de serie):

1. Haga clic en la palabra `COM[X]` de la barra de estado para establecer el puerto COM correcto con `STMicroelectronics`: ![com-port](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/com-port.png).

2. Haga clic en el icono de interruptor de alimentación de la barra de estado para abrir Serial Monitor (Monitor de serie): ![monitor-serie](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution//connect-iothub/serial-monitor.png).

3. En la barra de estado, haga clic en el número que representa la velocidad en baudios y configúrelo en `115200`: ![velocidad-baudios](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/baud-rate.png)

La aplicación de ejemplo se ejecuta correctamente si ve los siguientes resultados:

* El monitor de serie muestra la misma información de la captura de pantalla siguiente.
* El LED del MXChip IoT DevKit parpadea.

![Salida final en VS Code](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/result-serial-output.png)

## <a name="problems-and-feedback"></a>Problemas y comentarios

Si tiene problemas, puede buscar en las [preguntas más frecuentes](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/). Se pueden también enviar comentarios al dejar un comentario en esta página.

## <a name="next-steps"></a>pasos siguientes

Ha conectado correctamente un MXChip IoT DevKit a IoT Hub y ha enviado los datos del sensor capturados a IoT Hub.

Para continuar la introducción a Azure IoT Hub y explorar otros escenarios de IoT, consulte:

- [Administración de la mensajería de dispositivos en la nube con iothub-explorer](https://docs.microsoft.com/azure/iot-hub/iot-hub-explorer-cloud-device-messaging)
- [Guardado de los mensajes de IoT Hub en el almacenamiento de datos de Azure](https://docs.microsoft.com//azure/iot-hub/iot-hub-store-data-in-azure-table-storage)
- [Uso de Power BI para visualizar datos de sensor en tiempo real desde Azure IoT Hub](https://docs.microsoft.com//azure/iot-hub/iot-hub-live-data-visualization-in-power-bi)
- [Uso de Web Apps para visualizar datos del sensor en tiempo real desde Azure IoT Hub](https://docs.microsoft.com//azure/iot-hub/iot-hub-live-data-visualization-in-web-apps)
- [Pronóstico meteorológico con los datos del sensor de IoT Hub en Azure Machine Learning](https://docs.microsoft.com/azure/iot-hub/iot-hub-weather-forecast-machine-learning)
- [Administración de dispositivos con iothub-explorer](https://docs.microsoft.com/azure/iot-hub/iot-hub-device-management-iothub-explorer)
- [Supervisión remota y notificaciones con Logic Apps](https://docs.microsoft.com/azure/iot-hub/iot-hub-monitoring-notifications-with-azure-logic-apps)
