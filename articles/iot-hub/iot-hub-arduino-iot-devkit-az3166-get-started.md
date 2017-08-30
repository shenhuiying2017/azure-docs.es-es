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
ms.date: 08/11/2017
ms.author: xshi
ms.translationtype: HT
ms.sourcegitcommit: 1e6fb68d239ee3a66899f520a91702419461c02b
ms.openlocfilehash: 122fac584ac5b954ef7b33a3121bee2c502ebc63
ms.contentlocale: es-es
ms.lasthandoff: 08/16/2017

---
# <a name="connect-iot-devkit-az3166-to-azure-iot-hub-in-the-cloud"></a>Conexión de IoT DevKit AZ3166 a Azure IoT Hub en la nube

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

El [MXChip IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/) puede usarse para desarrollar y crear prototipos de soluciones de Internet de las cosas (IoT) que aprovechen los servicios de Microsoft Azure. Incluye una placa compatible con Arduino con periféricos y sensores enriquecidos, un paquete de panel de código abierto y un [catálogo proyectos](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) en aumento.

## <a name="what-you-do"></a>Qué debe hacer
Conecte [DevKit](https://microsoft.github.io/azure-iot-developer-kit/) a una instancia de Azure IoT Hub en la que creará y recopilará datos de temperatura y humedad de sensores para enviarlos al centro de IoT.

¿Aún no tiene un DevKit? Obtenga uno nuevo [aquí](https://aka.ms/iot-devkit-purchase).

## <a name="what-you-learn"></a>Conocimientos que adquirirá

* Cómo conectar IoT DevKit al punto de acceso inalámbrico y preparar el entorno de desarrollo.
* Cómo crear un centro de IoT y registrar un dispositivo para MXChip IoT DevKit.
* Cómo recopilar datos del sensor mediante la ejecución de una aplicación de ejemplo en MXChip IoT DevKit.
* Cómo enviar los datos del sensor al centro de IoT.

## <a name="what-you-need"></a>Lo que necesita

* Una placa MXChip IoT DevKit con un cable microUSB. [Obténgalo ahora](https://aka.ms/iot-devkit-purchase)
* Un equipo con Windows 10 o macOS 10.10+
* Una suscripción de Azure activa.
  * Active una [cuenta de Microsoft Azure de prueba de 30 días gratis](https://azureinfo.microsoft.com/us-freetrial.html)

## <a name="prepare-your-hardware"></a>Preparar el hardware

Conecte el hardware al equipo.

### <a name="hardware-you-need"></a>Hardware que necesita

* Placa DevKit
* Cable microUSB

![getting-started-hardware](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/hardware.jpg)

### <a name="connect-devkit-to-your-computer"></a>Conexión de DevKit al equipo

1. Conecte el extremo USB al equipo
2. Conecte el extremo microUSB al DevKit
3. El LED verde situado junto a la alimentación confirma la conexión

![getting-started-connect](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connect.jpg)

## <a name="configure-wifi"></a>Configuración de la red WiFi

Los proyectos de IoT se basan en la conectividad a Internet. Siga estas instrucciones para configurar el DevKit para conectarse a la red WiFi.

### <a name="enter-ap-mode"></a>Entrada al modo AP

Mantenga presionado el botón B, pulse y suelte el botón de restablecimiento y suelte el botón B. El DevKit pasará al modo AP para la configuración de la red WiFi. La pantalla mostrará el identificador de red SSID del DevKit, así como la dirección IP del portal de configuración:

![getting-started-wifi-ap](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/wifi-ap.jpg)

### <a name="connect-to-devkit-ap"></a>Conexión a DevKit AP

Ahora, use otro dispositivo con la red WiFi habilitada (PC o teléfono móvil) para conectarse al SSID del DevKit (resaltado en la captura de pantalla anterior), deje la contraseña en blanco.

![getting-started-ssid](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connect-ssid.png)

### <a name="configure-wifi-for-devkit"></a>Configuración de la red WiFi para DevKit

Abra la dirección IP que aparece en la pantalla del DevKit en el explorador del PC o teléfono móvil, seleccione la red WiFi a la que quiera que se conecte el DevKit y escriba la contraseña. Haga clic en **Conectar** para terminar:

![getting-started-wifi-portal](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/wifi-portal.png)

Una vez conectado, el DevKit se reinicia en unos segundos. Si el proceso se realizó correctamente, verá el nombre de la red WiFi y la dirección IP en la pantalla:

![getting-started-wifi-ip](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/wifi-ip.jpg)

> [!NOTE] 
La dirección IP mostrada en la imagen puede no coincidir con la dirección IP real asignada que se muestre en la pantalla del DevKit. Es normal, ya que la red WiFi usa DHCP para asignar las direcciones IP dinámicamente.

Una vez configurada la red WiFi, sus credenciales se conservarán en el dispositivo para esa conexión, aunque se desconecte. Por ejemplo, si configura el DevKit para la red WiFi del hogar y se lleva el DevKit a la oficina, debe volver a configurar el modo AP (comenzando por el paso **Entrada al modo AP**) para conectar el DevKit a la red WiFi de la oficina. 

## <a name="start-using-devkit"></a>Uso inicial del DevKit

La aplicación predeterminada que se ejecuta en el DevKit comprobará la versión más reciente del firmware y mostrará algunos datos de diagnóstico del sensor.

### <a name="upgrade-to-the-latest-firmware"></a>Actualización al firmware más reciente

En la pantalla se le pedirá que especifique la versión actual y la más reciente del firmware (si es necesaria la actualización). Para la actualización, siga la guía de [actualización de firmware](https://microsoft.github.io/azure-iot-developer-kit/docs/upgrading/).

![getting-started-firmware](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/firmware.jpg)

> [!NOTE] 
Esto solo se realiza una vez, cuando comience el desarrollo en el DevKit y cargue la aplicación, tendrá el firmware más reciente, que viene con la aplicación.

### <a name="test-various-sensors"></a>Prueba de varios sensores

Presione el botón B probar sensores, continúe presionando y suelte el botón B para recorrer en iteración cada sensor.

![getting-started-sensors](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/sensors.jpg)

## <a name="prepare-development-environment"></a>Preparación del entorno de desarrollo

Ahora es el momento de configurar el entorno de desarrollo: herramientas y paquetes para compilar aplicaciones de IoT sorprendentes. Puede elegir la versión de Windows o macOS según el sistema operativo.


### <a name="windows"></a>Windows

Se recomienda usar el paquete de instalación para preparar el entorno de desarrollo. Si tiene algún problema, puede seguir los [pasos manuales](https://microsoft.github.io/azure-iot-developer-kit/docs/installation/).

#### <a name="download-latest-package"></a>Descarga del paquete más reciente

El archivo `.zip` que descargue contiene todos los paquetes y herramientas necesarios para el desarrollo del DevKit.

> [!div class="button"]
[Descargar](https://azureboard.azureedge.net/prod/installpackage/devkit_install_1.0.2.zip)


> [!NOTE] 
> El archivo `.zip` contiene las siguientes herramientas y paquetes. Si ya tiene algunos componentes instalados, el script los detectará y los omitirá.
> * Node.js y Yarn: tiempo de ejecución de la configuración del script y tareas automatizadas
> * [MSI para la CLI de Azure 2.0](https://docs.microsoft.com//cli/azure/install-azure-cli#windows): experiencia de la línea de comandos multiplataforma para administrar recursos de Azure, el archivo MSI contiene Python y pip asociados.
> * [Visual Studio Code](https://code.visualstudio.com/): editor de código ligero para el desarrollo de DevKit
> * [Extensión de Visual Studio Code para Arduino](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-arduino): habilita el desarrollo de Arduino en Visual Studio Code
> * [Entorno de desarrollo integrado de Arduino](https://www.arduino.cc/en/Main/Software): la extensión para Arduino se basa en esta herramienta
> * Paquete de la placa DevKit: cadenas de herramientas, bibliotecas y proyectos para el DevKit
> * Utilidad ST-Link: controladores y utilidades esenciales

#### <a name="run-installation-script"></a>Ejecución del script de instalación

En el Explorador de archivos de Windows, busque el archivo `.zip` y extráigalo, busque `install.cmd`, pulse el botón derecho y seleccione **"Ejecutar como administrador"** para empezar.

![getting-started-run-admin](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/run-admin.png)

Durante la instalación verá el progreso de cada herramienta o paquete.

![getting-started-install](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install.png)

#### <a name="confirm-to-install-drivers"></a>Confirmación de instalación de los controladores

La extensión VS Code para Arduino se basa en el IDE de Arduino. Si se trata de la primera vez que se va a instalar el IDE de Arduino, se le pedirá que instale los controladores correspondientes:

![getting-started-driver](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/driver.png)

Tardará unos 10 minutos en completarse la instalación, en función de la velocidad de Internet. Una vez terminada, debería ver los accesos directos a Visual Studio Code y al IDE de Arduino en el escritorio.

> [!NOTE] 
En ocasiones, al iniciar VS Code, aparece un error que indica que no se encuentra el IDE de Arduino o un paquete de la placa. Para resolverlo, cierre VS Code, inicie el IDE de Arduino de nuevo y VS Code debería encontrar la ruta de acceso al IDE de Arduino correctamente.


### <a name="macos-preview"></a>macOS (versión preliminar)

Siga estos pasos para preparar el entorno de desarrollo en MacOS.

#### <a name="install-azure-cli-20"></a>Instalación de la CLI de Azure 2.0

Siga la [guía oficial](https://docs.microsoft.com//cli/azure/install-azure-cli) para instalar la CLI de Azure 2.0:

Instale la CLI de Azure 2.0 con un comando `curl`:

```bash
curl -L https://aka.ms/InstallAzureCli | bash
```

Y reinicie el shell de comandos para que surtan efecto los cambios:

```bash
exec -l $SHELL
```

#### <a name="install-arduino-ide"></a>Instalación del IDE de Arduino

La extensión Visual Studio Code para Arduino se basa en el IDE de Arduino. Descargue e instale el [IDE de Arduino para macOS](https://www.arduino.cc/en/Main/Software).

#### <a name="install-visual-studio-code"></a>Instalación de Visual Studio Code

Descargue e instale [Visual Studio Code para macOS](https://code.visualstudio.com/). Se trata de la herramienta de desarrollo principal para compilar aplicaciones de IoT de DevKit.

####  <a name="download-latest-package"></a>Descarga del paquete más reciente

1. Instale Node.js. Para la instalación puede usar el administrador de paquetes populares de macOS [Homebrew](https://brew.sh/) o el [instalador incorporado](https://nodejs.org/en/download/).

2. Descargue el archivo `.zip` con los scripts de tarea necesarios para el desarrollo del DevKit en VS Code.

   > [!div class="button"]
   [Descargar](https://azureboard.azureedge.net/installpackage/devkit_tasks_1.0.2.zip)

   Busque el archivo `.zip` y extráigalo. A continuación, inicie la aplicación **Terminal** y ejecute los siguientes comandos de configuración:

   Migre la carpeta extraída a la carpeta de usuario de MacOS:
   ```bash
   mv [.zip extracted folder]/azure-board-cli ~/. ; cd ~/azure-board-cli
   ```
  
   Instale los paquetes `npm`:
   ```
   npm install
   ```

#### <a name="install-vs-code-extension-for-arduino"></a>Instalación de la extensión de VS Code para Arduino

Visual Studio Code le permite instalar las extensiones de Marketplace directamente en la herramienta, solo tiene que hacer clic en el icono de las extensiones del panel izquierdo del menú y buscar `Arduino` para la instalación:

![installation-extensions](media/iot-hub-arduino-devkit-az3166-get-started/installation-extensions-mac.png)

#### <a name="install-devkit-board-package"></a>Instalación del paquete de la placa DevKit

Debe agregar la placa DevKit mediante el administrador de placas de Visual Studio Code.

1. Use `Cmd+Shift+P` para invocar la paleta de comandos y escriba **Arduino**, busque y seleccione **Arduino: Board Manager** (Arduino: administrador de placas).

2. Haga clic en **"Additional URLs"** (Direcciones URL adicionales) en la parte inferior derecha.
   ![instalación-url-adicionales](media/iot-hub-arduino-devkit-az3166-get-started/installation-additional-urls-mac.png)

3. En el archivo `settings.json`, agregue una línea en la parte inferior del panel `USER SETTINGS` y guárdelo.
   ```json
   "arduino.additionalUrls": "https://raw.githubusercontent.com/VSChina/azureiotdevkit_tools/master/package_azureboard_index.json"
   ```
   ![installation-settings-json](media/iot-hub-arduino-devkit-az3166-get-started/installation-settings-json-mac.png)

4. Ahora, en el administrador de placas, busque "az3166" e instale la versión más reciente.
   ![instalación-az3166](media/iot-hub-arduino-devkit-az3166-get-started/installation-az3166-mac.png)

Ahora tiene todas las herramientas y los paquetes necesarios instalados para macOS.


## <a name="open-project-folder"></a>Apertura de la carpeta del proyecto

### <a name="launch-vs-code"></a>Ejecutar VS Code

Asegúrese de que el DevKit no está conectado. Primero, inicie VS Code y conecte el DevKit al equipo. VS Code lo encontrará automáticamente y abrirá una página de introducción emergente:

![mini-solution-vscode](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution-vscode.png)

> [!NOTE] 
En ocasiones, al iniciar VS Code, aparece un error que indica que no se encuentra el IDE de Arduino o un paquete de la placa. Para resolverlo, cierre VS Code, inicie el IDE de Arduino de nuevo y VS Code debería encontrar la ruta de acceso al IDE de Arduino correctamente.

### <a name="open-arduino-examples-folder"></a>Apertura de la carpeta de ejemplos de Arduino

Cambie a la pestaña **"Arduino Examples"** (Ejemplos de Arduino), vaya a `Examples for MXCHIP AZ3166 > AzureIoT` y haga clic en `GetStarted`.

![mini-solution-examples](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution-examples.png)

Si se cierra el panel, para volver a cargarlo, use `Ctrl+Shift+P` (macOS: `Cmd+Shift+P`) para invocar la paleta de comandos y escriba **Arduino** para buscar y seleccionar **Arduino: Examples** (Ejemplos de Arduino).

## <a name="provision-azure-services"></a>Aprovisionamiento de los servicios de Azure

En la ventana de la solución, escriba "task cloud-provision" para ejecutar la tarea a través de `Ctrl+P` (macOS: `Cmd+P`):

En el terminal de VS Code, una línea de comandos interactiva le guiará durante el aprovisionamiento de los servicios de Azure necesarios:

![mini-solution-cloud-provision](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/cloud-provision.png)

## <a name="build-and-upload-arduino-sketch"></a>Creación y carga del boceto de Arduino

### <a name="install-required-library"></a>Instalación de la biblioteca necesaria

1. Presione `F1` o `Ctrl+Shift+P` (macOS: `Cmd+Shift+P`) para invocar la paleta de comandos y escriba **Arduino**, busque y seleccione **Arduino: Library Manager** (Arduino: administrador de bibliotecas).

2. Busque la biblioteca `ArduinoJson` y haga clic en **Install** (Instalar)

### <a name="build-and-upload-the-device-code"></a>Creación y carga del código del dispositivo

Use `Ctrl+P` (macOS: `Cmd+P`) para ejecutar la tarea "device-upload". El terminal le pedirá que especifique el modo de configuración. Para ello, mantenga presionado el botón A, y presione y suelte el botón de restablecimiento. En la pantalla aparecerá "Configuration" (Configuración). Esto sirve para establecer la cadena de conexión que se recupera del paso "task cloud-provision".

A continuación, se iniciará la comprobación y la carga del boceto de Arduino:

![mini-solution-device-upload](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/device-upload.png)

El DevKit se reiniciará y empezará a ejecutar el código.

## <a name="test-the-project"></a>Prueba del proyecto

En VS Code, haga clic en el icono de interruptor de alimentación de la barra de estado para abrir el monitor serie.

La aplicación de ejemplo se ejecuta correctamente si ve los siguientes resultados:

* El monitor de serie muestra la misma información de la captura de pantalla siguiente.
* El LED del MXChip IoT DevKit parpadea.

![Salida final en VS Code](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/result-serial-output.png)

## <a name="problems-and-feedback"></a>Problemas y comentarios

Si tiene algún problema, tiene [P+F](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) a su disposición; pero también puede ponerse en contacto con nosotros a través de los canales que aparecen a continuación.

## <a name="next-steps"></a>Pasos siguientes

Ha conectado correctamente un MXChip IoT DevKit a IoT Hub y ha enviado los datos del sensor capturados a IoT Hub.

Para continuar la introducción a IoT Hub y explorar otros escenarios de IoT, consulte:

- [Administración de la mensajería de dispositivos en la nube con iothub-explorer](https://docs.microsoft.com/azure/iot-hub/iot-hub-explorer-cloud-device-messaging)
- [Guardado de los mensajes de IoT Hub en el almacenamiento de datos de Azure](https://docs.microsoft.com//azure/iot-hub/iot-hub-store-data-in-azure-table-storage)
- [Uso de Power BI para visualizar datos de sensor en tiempo real desde Azure IoT Hub](https://docs.microsoft.com//azure/iot-hub/iot-hub-live-data-visualization-in-power-bi)
- [Uso de Azure Web Apps para visualizar datos del sensor en tiempo real desde Azure IoT Hub](https://docs.microsoft.com//azure/iot-hub/iot-hub-live-data-visualization-in-web-apps)
- [Pronóstico meteorológico con los datos del sensor de IoT Hub en Azure Machine Learning](https://docs.microsoft.com/azure/iot-hub/iot-hub-weather-forecast-machine-learning)
- [Administración de dispositivos con iothub-explorer](https://docs.microsoft.com/azure/iot-hub/iot-hub-device-management-iothub-explorer)
- [Supervisión remota y notificaciones con Logic Apps](https://docs.microsoft.com/azure/iot-hub/iot-hub-monitoring-notifications-with-azure-logic-apps)
