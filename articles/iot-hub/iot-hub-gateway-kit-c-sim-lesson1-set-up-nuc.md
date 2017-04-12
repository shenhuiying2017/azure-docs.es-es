---
title: "Dispositivo simulado y puerta de enlace de Azure IoT: Lección 1: Configuración de NUC | Microsoft Docs"
description: "Configure Intel NUC para que funcione como puerta de enlace de IoT entre un sensor e IoT Hub de Azure para recopilar información del sensor y enviarla a IoT Hub."
services: iot-hub
documentationcenter: 
author: shizn
manager: yjianfeng
tags: 
keywords: puerta de enlace de iot, intel nuc, equipo nuc, DE3815TYKE
ROBOTS: NOINDEX
redirect_url: /azure/iot-hub/iot-hub-gateway-kit-c-lesson1-set-up-nuc
ms.assetid: f41d6b2e-9b00-40df-90eb-17d824bea883
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 61e9a9fc7876094c04238c61cfc38efdd97b05f7
ms.openlocfilehash: 399ac2d571b65503da7d9cc47d2dec9aa5e4c3d7
ms.lasthandoff: 01/25/2017


---
# <a name="set-up-intel-nuc-as-an-iot-gateway"></a>Configuración de Intel NUC como puerta de enlace de IoT

## <a name="what-you-will-do"></a>Lo que hará

- Configure Intel NUC como puerta de enlace de IoT.
- Instale el paquete del SDK de puerta de enlace de IoT de Azure en Intel NUC.
- Ejecute una aplicación de ejemplo "hola_mundo" en Intel NUC para comprobar la funcionalidad de la puerta de enlace.
Si tiene problemas, busque soluciones en la [página de solución de problemas](iot-hub-gateway-kit-c-sim-troubleshooting.md).

## <a name="what-you-will-learn"></a>Lo qué aprenderá

En esta lección, aprenderá lo siguiente:

- Conexión de Intel NUC con periféricos.
- Instalación y actualización de los paquetes necesarios en Intel NUC mediante Smart Package Manager.
- Ejecución de una aplicación de ejemplo "hola_mundo" para comprobar la funcionalidad de la puerta de enlace.

## <a name="what-you-need"></a>Lo que necesita

- Un kit Intel NUC DE3815TYKE con Intel IoT Gateway Software Suite (Wind River Linux *7.0.0.13) preinstalado.
- Un cable Ethernet.
- Un teclado.
- Un cable HDMI o VGA.
- Un monitor con un puerto HDMI o VGA.

![Kit de puerta de enlace](media/iot-hub-gateway-kit-lessons/lesson1/kit_without_sensortag.png)

## <a name="connect-intel-nuc-with-the-peripherals"></a>Conexión de Intel NUC con los periféricos

La imagen siguiente es un ejemplo de Intel NUC conectado con varios periféricos:

1. Conectado a un teclado.
2. Conectado al monitor mediante un cable VGA o HDMI.
3. Conectado a una red cableada mediante un cable Ethernet.
4. Conectado a la fuente de alimentación con un cable de alimentación.

![Intel NUC conectado a periféricos](media/iot-hub-gateway-kit-lessons/lesson1/nuc.png)

## <a name="connect-to-the-intel-nuc-system-from-host-computer-via-secure-shell-ssh"></a>Conexión al sistema Intel NUC desde el equipo host a través de Secure Shell (SSH)

Aquí necesita un teclado y un monitor para obtener la dirección IP del dispositivo NUC. Si ya conoce la dirección IP, puede ir al paso 3 de esta sección.

1. Encienda Intel NUC presionando el botón de encendido e inicie sesión en el sistema.

   El nombre de usuario y la contraseña predeterminados son `root`.

2. Obtenga la dirección IP de NUC mediante la ejecución del comando `ifconfig`. Este paso se realiza en el dispositivo NUC.

   Este es un ejemplo del resultado del comando.

   ![Salida de ifconfig que muestra la dirección IP de NUC](media/iot-hub-gateway-kit-lessons/lesson1/ifconfig.png)

   En este ejemplo, el valor que sigue a `inet addr:` es la dirección IP que necesita si tiene previsto conectarse remotamente desde un equipo host a Intel NUC.

3. Utilice uno de los siguientes clientes SSH desde el equipo host para conectarse a Intel NUC.

   - [PuTTY](http://www.putty.org/) para Windows.
   - El cliente de SSH integrado en Ubuntu o macOS.

   Resulta más eficaz y productivo operar en Intel NUC desde un equipo host. Necesita la dirección IP, el nombre de usuario y la contraseña para conectarse a NUC a través del cliente de SSH. Este es el ejemplo de cliente de SSH que se usa en macOS.
   ![Cliente de SSH que se ejecuta en macOS](media/iot-hub-gateway-kit-lessons/lesson1/ssh.png)

## <a name="install-the-azure-iot-gateway-sdk-package"></a>Instalación del paquete del SDK de puerta de enlace de IoT de Azure

El paquete del SDK de puerta de enlace de IoT de Azure contiene los archivos binarios compilados previamente del SDK y sus dependencias. Estos archivos binarios forman el SDK de puerta de enlace de IoT de Azure, el SDK de IoT de Azure y las herramientas correspondientes. El paquete también contiene una aplicación de ejemplo "hello_world" que se utiliza para validar la funcionalidad de la puerta de enlace. El SDK es la parte principal de la puerta de enlace. Para instalar el paquete, siga estos pasos:

1. Agregue el repositorio de la nube de IoT mediante la ejecución de los comandos siguientes en una ventana de terminal:

   ```bash
   rpm --import http://iotdk.intel.com/misc/iot_pub.key
   smart channel --add IoT_Cloud type=rpm-md name="IoT_Cloud" baseurl=http://iotdk.intel.com/repos/iot-cloud/wrlinux7/rcpl13/ -y
   ```

   El comando `rpm` importa la clave rpm. El comando `smart channel` agrega el canal rpm a Smart Package Manager. Antes de ejecutar el comando `smart update`, verá una salida similar a la siguiente.

   ![salida de los comandos rpm y de canal inteligente](media/iot-hub-gateway-kit-lessons/lesson1/rpm_smart_channel.png)

   ```bash
   smart update
   ```

2. Instale el paquete ejecutando el comando siguiente:

   ```bash
   smart install packagegroup-cloud-azure -y
   ```

   `packagegroup-cloud-azure` es el nombre del paquete. El comando `smart install` se usa para instalar el paquete.

   Después de instalar el paquete, Intel NUC debería funcionar como puerta de enlace.

## <a name="run-the-azure-iot-gateway-sdk-helloworld-sample-application"></a>Ejecución de la aplicación de ejemplo "hola_mundo" del SDK de puerta de enlace de IoT de Azure

Vaya a `azureiotgatewaysdk/samples` y ejecute la aplicación de ejemplo "hola_mundo". Esta aplicación de ejemplo crea una puerta de enlace del archivo `hello_world.json` y utiliza los componentes fundamentales de la arquitectura del SDK de puerta de enlace de IoT de Azure para registrar un mensaje de "Hola mundo" en un archivo cada 5 segundos.

Puede ejecutar la aplicación de ejemplo "hola_mundo" ejecutando el comando siguiente:

```bash
cd /usr/share/azureiotgatewaysdk/samples/hello_world/
./hello_world hello_world.json
```

La aplicación de ejemplo produce el siguiente resultado si la funcionalidad de puerta de enlace funciona correctamente:

![resultado de la aplicación](media/iot-hub-gateway-kit-lessons/lesson1/hello_world.png)

Si tiene problemas, busque soluciones en la [página de solución de problemas](iot-hub-gateway-kit-c-troubleshooting.md).

## <a name="summary"></a>Resumen

¡Enhorabuena! Ha terminado de configurar Intel NUC como puerta de enlace. Ahora está listo para pasar a la lección siguiente donde configurará el equipo host, creará una instancia de IoT hub de Azure y registrará el dispositivo lógico de la instancia de IoT Hub de Azure.

## <a name="next-steps"></a>Pasos siguientes
[Preparación del equipo host y de IoT Hub de Azure](iot-hub-gateway-kit-c-sim-lesson2-get-the-tools-win32.md)

