---
title: "Conexión de Raspberry Pi al Conjunto de aplicaciones de IoT de Azure mediante C con sensores reales | Microsoft Docs"
description: "Use el Starter Kit de IoT de Microsoft Azure para Raspberry Pi 3 y el Conjunto de aplicaciones de IoT de Azure. Use C para conectar su Raspberry Pi a la solución de supervisión remota, enviar telemetría desde sensores a la nube y responder a los métodos invocados desde el panel de la solución."
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.service: iot-suite
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: dobett
ms.openlocfilehash: 635eb9d4e85eaf43dc83f2bd5a0d0f7c32620d98
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/06/2017
---
# <a name="connect-your-raspberry-pi-3-to-the-remote-monitoring-solution-and-send-telemetry-from-a-real-sensor-using-c"></a>Conexión de Raspberry Pi 3 a la solución de supervisión remota y envío de telemetría desde un sensor real mediante C

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-selector](../../includes/iot-suite-v1-raspberry-pi-kit-selector.md)]

Este tutorial muestra cómo usar el Starter Kit de IoT de Microsoft Azure para Raspberry Pi 3 para desarrollar un lector de temperatura y humedad que pueda comunicarse con la nube. El tutorial usa:

- El SO Raspbian, el lenguaje de programación C y el SDK de IoT de Microsoft Azure para C para implementar un dispositivo de ejemplo.
- La solución preconfigurada de supervisión remota del Conjunto de aplicaciones de IoT como el back-end basado en la nube.

## <a name="overview"></a>Información general

En este tutorial, va a completar los siguientes pasos:

- Implemente una instancia de la solución preconfigurada de supervisión remota en su suscripción de Azure. Este paso implementa y configura varios servicios de Azure automáticamente.
- Configure el dispositivo y los sensores para que se comunique con el equipo y la solución de supervisión remota.
- Actualice el código del dispositivo de ejemplo para que se conecte a la solución de supervisión remota y envíe telemetría que aparezca en el panel de soluciones.

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-prerequisites](../../includes/iot-suite-v1-raspberry-pi-kit-prerequisites.md)]

[!INCLUDE [iot-suite-v1-provision-remote-monitoring](../../includes/iot-suite-v1-provision-remote-monitoring.md)]

> [!WARNING]
> La solución de supervisión remota proporciona un conjunto de servicios de Azure de la suscripción de Azure. La implementación refleja una arquitectura empresarial real. Para evitar cobros de consumo innecesarios de Azure, elimine la instancia de la solución preconfigurada en azureiotsuite.com cuando haya terminado con ella. Si necesita la solución preconfigurada de nuevo, puede crearla fácilmente. Para más información sobre cómo reducir el consumo mientras se ejecuta la solución de supervisión remota, consulte [Configuring Azure IoT Suite preconfigured solutions for demo purposes][lnk-demo-config] (Configuración de soluciones preconfiguradas del Conjunto de aplicaciones de IoT de Azure para fines de demostración).

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-view-solution](../../includes/iot-suite-v1-raspberry-pi-kit-view-solution.md)]

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-prepare-pi](../../includes/iot-suite-v1-raspberry-pi-kit-prepare-pi.md)]

## <a name="download-and-configure-the-sample"></a>Descarga y configuración del ejemplo

Ahora puede descargar y configurar la aplicación de cliente de supervisión remota en su Raspberry Pi.

### <a name="clone-the-repositories"></a>Clonación de repositorios

Si aún no lo ha hecho, clone los repositorios necesarios mediante la ejecución de los siguientes comandos en un terminal en su Pi:

```sh
cd ~
git clone --recursive https://github.com/Azure-Samples/iot-remote-monitoring-c-raspberrypi-getstartedkit.git
git clone --recursive https://github.com/WiringPi/WiringPi.git
```

### <a name="update-the-device-connection-string"></a>Actualización de la cadena de conexión de dispositivo

Abra el archivo de código fuente de ejemplo en el editor **nano** con el comando siguiente:

```sh
nano ~/iot-remote-monitoring-c-raspberrypi-getstartedkit/basic/remote_monitoring/remote_monitoring.c
```

Busque las líneas siguientes:

```c
static const char* deviceId = "[Device Id]";
static const char* connectionString = "HostName=[IoTHub Name].azure-devices.net;DeviceId=[Device Id];SharedAccessKey=[Device Key]";
```

Reemplace los valores de marcador de posición por el dispositivo y la información de IoT Hub que creó y guardó al principio de este tutorial. Guarde los cambios (**Ctrl-O**, **ENTRAR**) y salga del editor (**Ctrl-X**).

## <a name="build-the-sample"></a>Compilación del ejemplo

Instale los paquetes de requisitos previos para el SDK de dispositivo IoT de Microsoft Azure para C ejecutando los comandos siguientes en un terminal en Raspberry Pi:

```sh
sudo apt-get update
sudo apt-get install g++ make cmake git libcurl4-openssl-dev libssl-dev uuid-dev
```

Ahora puede compilar la solución de ejemplo actualizada en Raspberry Pi:

```sh
chmod +x ~/iot-remote-monitoring-c-raspberrypi-getstartedkit/basic/build.sh
~/iot-remote-monitoring-c-raspberrypi-getstartedkit/basic/build.sh
```

Ahora puede ejecutar el programa de ejemplo en Raspberry Pi. Escriba el comando:

```sh
sudo ~/cmake/remote_monitoring/remote_monitoring
```

La salida de ejemplo siguiente es un ejemplo de la salida que se ve en el símbolo del sistema en Raspberry Pi:

![Salida de la aplicación Raspberry Pi][img-raspberry-output]

Presione **Ctrl-C** para salir del programa en cualquier momento.

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-view-telemetry](../../includes/iot-suite-v1-raspberry-pi-kit-view-telemetry.md)]

## <a name="next-steps"></a>Pasos siguientes

Visite el [Centro para desarrolladores de IoT de Azure](https://azure.microsoft.com/develop/iot/) para obtener más ejemplos y la documentación de IoT de Azure.

[img-raspberry-output]: ./media/iot-suite-v1-raspberry-pi-kit-c-get-started-basic/appoutput.png

[lnk-demo-config]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/configure-preconfigured-demo.md
