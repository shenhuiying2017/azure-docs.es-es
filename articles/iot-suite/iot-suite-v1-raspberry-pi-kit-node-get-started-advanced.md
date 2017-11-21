---
title: "Conexión de Raspberry Pi al Conjunto de aplicaciones de IoT de Azure mediante Node.js para admitir las actualizaciones de firmware | Microsoft Docs"
description: "Use el Starter Kit de IoT de Microsoft Azure para Raspberry Pi 3 y el Conjunto de aplicaciones de IoT de Azure. Utilice Node.js para conectar su Raspberry Pi a la solución de supervisión remota, enviar telemetría desde sensores a la nube y realizar una actualización de firmware remota."
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.service: iot-suite
ms.devlang: nodejs
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: dobett
ms.openlocfilehash: 31bbeff8049c6005671b991f965fae7316e3adf6
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/06/2017
---
# <a name="connect-your-raspberry-pi-3-to-the-remote-monitoring-solution-and-enable-remote-firmware-updates-using-nodejs"></a>Conexión de Raspberry Pi 3 a la solución de supervisión remota y habilitación de las actualizaciones de firmware remotas mediante Node.js

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-selector](../../includes/iot-suite-v1-raspberry-pi-kit-selector.md)]

Este tutorial muestra cómo usar el Starter Kit de IoT de Microsoft Azure para Raspberry Pi 3 para:

* Desarrollar un lector de temperatura y humedad que pueda comunicarse con la nube.
* Habilitar y realizar una actualización de firmware remota para actualizar la aplicación cliente en Raspberry Pi.

El tutorial usa:

- El SO Raspbian, el lenguaje de programación Node.js y el SDK de IoT de Microsoft Azure para Node.js para implementar un dispositivo de ejemplo.
- La solución preconfigurada de supervisión remota del Conjunto de aplicaciones de IoT como el back-end basado en la nube.

## <a name="overview"></a>Información general

En este tutorial, va a completar los siguientes pasos:

- Implemente una instancia de la solución preconfigurada de supervisión remota en su suscripción de Azure. Este paso implementa y configura varios servicios de Azure automáticamente.
- Configure el dispositivo y los sensores para que se comunique con el equipo y la solución de supervisión remota.
- Actualice el código del dispositivo de ejemplo para que se conecte a la solución de supervisión remota y envíe telemetría que aparezca en el panel de soluciones.
- Use el código de dispositivo de ejemplo para actualizar la aplicación cliente.

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-prerequisites](../../includes/iot-suite-v1-raspberry-pi-kit-prerequisites.md)]

[!INCLUDE [iot-suite-v1-provision-remote-monitoring](../../includes/iot-suite-v1-provision-remote-monitoring.md)]

> [!WARNING]
> La solución de supervisión remota proporciona un conjunto de servicios de Azure de la suscripción de Azure. La implementación refleja una arquitectura empresarial real. Para evitar cobros de consumo innecesarios de Azure, elimine la instancia de la solución preconfigurada en azureiotsuite.com cuando haya terminado con ella. Si necesita la solución preconfigurada de nuevo, puede crearla fácilmente. Para más información sobre cómo reducir el consumo mientras se ejecuta la solución de supervisión remota, consulte [Configuring Azure IoT Suite preconfigured solutions for demo purposes][lnk-demo-config] (Configuración de soluciones preconfiguradas del Conjunto de aplicaciones de IoT de Azure para fines de demostración).

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-view-solution](../../includes/iot-suite-v1-raspberry-pi-kit-view-solution.md)]

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-prepare-pi](../../includes/iot-suite-v1-raspberry-pi-kit-prepare-pi.md)]

## <a name="download-and-configure-the-sample"></a>Descarga y configuración del ejemplo

Ahora puede descargar y configurar la aplicación de cliente de supervisión remota en su Raspberry Pi.

### <a name="install-nodejs"></a>Instalación de Node.js

Si aún no lo ha hecho, instale Node.js en su Raspberry Pi. El SDK de IoT para Node.js requiere la versión 0.11.5 de Node.js o una versión posterior. Los pasos siguientes muestran cómo instalar Node.js v6.10.2 en Raspberry Pi:

1. Use el siguiente comando para actualizar Raspberry Pi:

    ```sh
    sudo apt-get update
    ```

1. Use el siguiente comando para descargar los archivos binarios de Node.js en Raspberry Pi:

    ```sh
    wget https://nodejs.org/dist/v6.10.2/node-v6.10.2-linux-armv7l.tar.gz
    ```

1. Use el siguiente comando para los binarios:

    ```sh
    sudo tar -C /usr/local --strip-components 1 -xzf node-v6.10.2-linux-armv7l.tar.gz
    ```

1. Use el siguiente comando para comprobar que ha instalado Node.js v6.10.2 correctamente:

    ```sh
    node --version
    ```

### <a name="clone-the-repositories"></a>Clonación de repositorios

Si aún no lo ha hecho, clone los repositorios necesarios mediante la ejecución de los siguientes comandos en Pi:

```sh
cd ~
git clone --recursive https://github.com/Azure-Samples/iot-remote-monitoring-node-raspberrypi-getstartedkit.git
```

### <a name="update-the-device-connection-string"></a>Actualización de la cadena de conexión de dispositivo

Abra el archivo de configuración de ejemplo en el editor **nano** con el comando siguiente:

```sh
nano ~/iot-remote-monitoring-node-raspberrypi-getstartedkit/advanced/config/deviceinfo
```

Reemplace los valores de marcador de posición por el identificador de dispositivo y la información de IoT Hub que creó y guardó al principio de este tutorial.

Cuando haya terminado, el contenido del archivo e información del dispositivo debe ser similar al ejemplo siguiente:

```conf
yourdeviceid
HostName=youriothubname.azure-devices.net;DeviceId=yourdeviceid;SharedAccessKey=yourdevicekey
```

Guarde los cambios (**Ctrl-O**, **ENTRAR**) y salga del editor (**Ctrl-X**).

## <a name="run-the-sample"></a>Ejecución del ejemplo

Ejecute los comandos siguientes para instalar los paquetes de requisitos previos para el ejemplo:

```sh
cd ~/iot-remote-monitoring-node-raspberrypi-getstartedkit/advance/1.0
npm install
```

Ahora puede ejecutar el programa de ejemplo en Raspberry Pi. Escriba el comando:

```sh
sudo node ~/iot-remote-monitoring-node-raspberrypi-getstartedkit/advanced/1.0/remote_monitoring.js
```

La salida de ejemplo siguiente es un ejemplo de la salida que se ve en el símbolo del sistema en Raspberry Pi:

![Salida de la aplicación Raspberry Pi][img-raspberry-output]

Presione **Ctrl-C** para salir del programa en cualquier momento.

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-view-telemetry-advanced](../../includes/iot-suite-v1-raspberry-pi-kit-view-telemetry-advanced.md)]

1. En el panel de soluciones, haga clic en **Dispositivos** para visitar la página **Dispositivos**. Seleccione su Raspberry Pi en la **lista de dispositivos**. A continuación, elija **Métodos**:

    ![Lista de dispositivos en el panel][img-list-devices]

1. En la página **Invocar método**, elija **InitiateFirmwareUpdate** en la lista desplegable **Método**.

1. En el campo **FWPackageURI**, escriba **https://raw.githubusercontent.com/Azure-Samples/iot-remote-monitoring-node-raspberrypi-getstartedkit/master/advanced/2.0/raspberry.js**. Este archivo contiene la implementación de la versión 2.0 del firmware.

1. Elija **Invocar método**. La aplicación en Raspberry Pi envía una confirmación de vuelta al panel de soluciones. Después, inicia el proceso de actualización de firmware mediante la descarga de la nueva versión del firmware:

    ![Mostrar el historial de métodos][img-method-history]

## <a name="observe-the-firmware-update-process"></a>Observar el proceso de actualización de firmware

Puede observar el proceso de actualización del firmware a medida que se ejecuta en el dispositivo y ver las propiedades notificadas en el panel de la solución:

1. Puede ver el progreso en el proceso de actualización en Raspberry Pi:

    ![Mostrar el progreso de la actualización][img-update-progress]

    > [!NOTE]
    > La aplicación de supervisión remota se reinicia automáticamente cuando finaliza la actualización. Use el comando `ps -ef` para comprobar que se está ejecutando. Si desea terminar el proceso, use el comando `kill` con el identificador de proceso.

1. Puede ver el estado de la actualización del firmware, tal y como lo notifica el dispositivo, en el portal de solución. La captura de pantalla siguiente muestra el estado y la duración de cada fase del proceso de actualización y la nueva versión de firmware:

    ![Mostrar estado del trabajo][img-job-status]

    Si retrocede al panel, puede comprobar que el dispositivo todavía está enviando telemetría tras la actualización del firmware.

> [!WARNING]
> Si deja la solución de supervisión remota ejecutándose en su cuenta de Azure, se le cobra por el tiempo que se ejecute. Para más información sobre cómo reducir el consumo mientras se ejecuta la solución de supervisión remota, consulte [Configuring Azure IoT Suite preconfigured solutions for demo purposes][lnk-demo-config] (Configuración de soluciones preconfiguradas del Conjunto de aplicaciones de IoT de Azure para fines de demostración). Elimine la solución preconfigurada de su cuenta de Azure cuando haya terminado de usarla.

## <a name="next-steps"></a>Pasos siguientes

Visite el [Centro para desarrolladores de IoT de Azure](https://azure.microsoft.com/develop/iot/) para obtener más ejemplos y la documentación de IoT de Azure.


[img-raspberry-output]: ./media/iot-suite-v1-raspberry-pi-kit-node-get-started-advanced/app-output.png
[img-update-progress]: ./media/iot-suite-v1-raspberry-pi-kit-node-get-started-advanced/updateprogress.png
[img-job-status]: ./media/iot-suite-v1-raspberry-pi-kit-node-get-started-advanced/jobstatus.png
[img-list-devices]: ./media/iot-suite-v1-raspberry-pi-kit-node-get-started-advanced/listdevices.png
[img-method-history]: ./media/iot-suite-v1-raspberry-pi-kit-node-get-started-advanced/methodhistory.png

[lnk-demo-config]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/configure-preconfigured-demo.md
