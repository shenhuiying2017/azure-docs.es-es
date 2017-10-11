---
title: "Dispositivo SensorTag y puerta de enlace de Azure IoT: Lección 1: Configuración de Intel NUC | Microsoft Docs"
description: "Configure Intel NUC para que funcione como puerta de enlace de IoT entre un sensor e IoT Hub de Azure para recopilar información del sensor y enviarla a IoT Hub."
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: puerta de enlace de iot, intel nuc, equipo nuc, DE3815TYKE
ms.assetid: 917090d6-35c2-495b-a620-ca6f9c02b317
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
ms.openlocfilehash: 1a3a92ab8d08c6ed6f047208217c46022027157e
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2017
---
# <a name="set-up-intel-nuc-as-an-iot-gateway"></a>Configuración de Intel NUC como puerta de enlace de IoT
[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

## <a name="what-you-will-do"></a>Lo que hará

- Configure Intel NUC como puerta de enlace de IoT.
- Instale el paquete de Azure IoT Edge en Intel NUC.
- Ejecute una aplicación de ejemplo "hola_mundo" en Intel NUC para comprobar la funcionalidad de la puerta de enlace.

  > Si tiene problemas, busque soluciones en la [página de solución de problemas](iot-hub-gateway-kit-c-troubleshooting.md).

## <a name="what-you-will-learn"></a>Lo qué aprenderá

En esta lección, aprenderá lo siguiente:

- Conexión de Intel NUC con periféricos.
- Instalación y actualización de los paquetes necesarios en Intel NUC mediante Smart Package Manager.
- Ejecución de una aplicación de ejemplo "hola_mundo" para comprobar la funcionalidad de la puerta de enlace.

## <a name="what-you-need"></a>Lo que necesita

- Un kit Intel NUC DE3815TYKE con Intel IoT Gateway Software Suite (Wind River Linux *7.0.0.13) preinstalado. [Haga clic aquí para comprar el producto Grove IoT Commercial Gateway Kit](https://www.seeedstudio.com/Grove-IoT-Commercial-Gateway-Kit-p-2724.html).
- Un cable Ethernet.
- Un teclado.
- Un cable HDMI o VGA.
- Un monitor con un puerto HDMI o VGA.
- Opcional: [SensorTag de Texas Instruments (CC2650STK)](http://www.ti.com/tool/cc2650stk)

![Kit de puerta de enlace](media/iot-hub-gateway-kit-lessons/lesson1/kit.png)

## <a name="connect-intel-nuc-with-the-peripherals"></a>Conexión de Intel NUC con los periféricos

La imagen siguiente es un ejemplo de Intel NUC conectado con varios periféricos:

1. Conectado a un teclado.
2. Conectado a un monitor mediante un cable VGA o HDMI.
3. Conectado a una red cableada mediante un cable Ethernet.
4. Conectado a una fuente de alimentación con un cable de alimentación.

![Intel NUC conectado a periféricos](media/iot-hub-gateway-kit-lessons/lesson1/nuc.png)

## <a name="connect-to-the-intel-nuc-system-from-host-computer-via-secure-shell-ssh"></a>Conexión al sistema Intel NUC desde el equipo host a través de Secure Shell (SSH)

Necesitará un teclado y un monitor para obtener la dirección IP del dispositivo Intel NUC. Si ya conoce la dirección IP, puede avanzar al paso 3 de esta sección.

1. Encienda Intel NUC presionando el botón de encendido y, a continuación, inicie sesión.

   El nombre de usuario y la contraseña predeterminados son `root`.

       > Hit the enter key on your keyboard if you see either of the following errors when you boot: 'A TPM error (7) occurred attempting to read a pcr value.' or 'Timeout, No TPM chip found, activating TPM-bypass!'

2. Obtenga la dirección IP de Intel NUC mediante la ejecución del comando `ifconfig` en el dispositivo Intel NUC.

   Este es un ejemplo del resultado del comando.

   ![Salida de ifconfig que muestra la dirección IP de Intel NUC](media/iot-hub-gateway-kit-lessons/lesson1/ifconfig.png)

   En este ejemplo, el valor que sigue a `inet addr:` es la dirección IP que necesita para conectarse desde un equipo host a Intel NUC.

3. Utilice uno de los siguientes clientes SSH desde el equipo host para conectarse a Intel NUC.

    - [PuTTY](http://www.putty.org/) para Windows.
    - El cliente de SSH integrado en Ubuntu o macOS.

   Resulta más eficaz y productivo operar en Intel NUC desde un equipo host. Necesitará la dirección IP, el nombre de usuario y la contraseña de NUC para conectarse a través de un cliente de SSH. Este es un ejemplo que utiliza un cliente de SSH en Mac OS.
   ![Cliente de SSH que se ejecuta en macOS](media/iot-hub-gateway-kit-lessons/lesson1/ssh.png)

## <a name="install-the-azure-iot-edge-package"></a>Instalación del paquete de Azure IoT Edge

El paquete de Azure IoT Edge contiene los archivos binarios compilados previamente de IoT Edge y sus dependencias. Estos archivos binarios forman Azure IoT Edge, el SDK de IoT de Azure y las herramientas correspondientes. El paquete también contiene una aplicación de ejemplo "hola_mundo" que se utiliza para validar la funcionalidad de la puerta de enlace. IoT Edge es la parte principal de la puerta de enlace. 

Para instalar el paquete, siga estos pasos:

1. Agregue el repositorio de la nube de IoT mediante la ejecución de los comandos siguientes en una ventana de terminal:

   ```bash
   rpm --import https://iotdk.intel.com/misc/iot_pub2.key
   smart channel --add IoT_Cloud type=rpm-md name="IoT_Cloud" baseurl=http://iotdk.intel.com/repos/iot-cloud/wrlinux7/rcpl13/ -y
   smart channel --add WR_Repo type=rpm-md baseurl=https://distro.windriver.com/release/idp-3-xt/public_feeds/WR-IDP-3-XT-Intel-Baytrail-public-repo/RCPL13/corei7_64/
   ```

   > Escriba "y" cuando se le pregunte si incluir este canal.
   
   Si recibe un error `import read failed(-1)`, use los comandos siguientes para resolver el problema:
   ```bash
   wget http://iotdk.intel.com/misc/iot_pub2.key 
   rpm --import iot_pub2.key  
   ```

   El comando `rpm` importa la clave rpm. El comando `smart channel` agrega el canal rpm a Smart Package Manager. Antes de ejecutar el comando `smart update`, verá una salida similar a la siguiente.

   ![salida de los comandos rpm y de canal inteligente](media/iot-hub-gateway-kit-lessons/lesson1/rpm_smart_channel.png)

2. Ejecute el comando de actualización inteligente:

   ```bash
   smart update
   ```

3. Instale la puerta de enlace de IoT de Azure ejecutando el siguiente comando:

   ```bash
   smart install packagegroup-cloud-azure -y
   ```

   `packagegroup-cloud-azure` es el nombre del paquete. El comando `smart install` se usa para instalar el paquete.

    > Ejecute el siguiente comando si ve este error: "la clave pública no está disponible".

    ```bash
    smart config --set rpm-check-signatures=false
    smart install packagegroup-cloud-azure -y
    ```
    > Reinicie el dispositivo Intel NUC si ve el siguiente error: "no package provides util-linux-dev" (ningún paquete proporciona util-linux-dev).

   Después de instalar el paquete, Intel NUC está listo para funcionar como puerta de enlace.

## <a name="run-the-azure-iot-edge-helloworld-sample-application"></a>Ejecución de la aplicación de ejemplo "hola_mundo" de Azure IoT Edge

La siguiente aplicación de ejemplo crea una puerta de enlace desde un archivo `hello_world.json` y utiliza los componentes fundamentales de la arquitectura de Azure IoT Edge para registrar un mensaje de "Hola mundo" en un archivo (log.txt) cada 5 segundos.

Puede ejecutar el ejemplo de "Hola mundo" mediante la ejecución de los siguientes comandos:

```bash
cd /usr/share/azureiotgatewaysdk/samples/hello_world/
./hello_world hello_world.json
```

Permita que la aplicación "Hola mundo" se ejecute durante unos minutos y, a continuación, presione la tecla ENTRAR para detenerla.
![resultado de la aplicación](media/iot-hub-gateway-kit-lessons/lesson1/hello_world.png)

> Puede omitir los errores sobre control de argumento no válido (NULL) que aparecen tras pulsar Entrar.

Para comprobar que la puerta de enlace se ejecutó correctamente, abra el archivo log.txt que se encuentra ahora en la ![vista del directorio de log.txt](media/iot-hub-gateway-kit-lessons/lesson1/logtxtdir.png) de la carpeta hola_mundo.

Abra log.txt mediante el comando siguiente:

```bash
vim log.txt
```

A continuación, verá el contenido de log.txt, que será una salida con formato JSON de los mensajes de registro que se escribieron cada 5 segundos por parte del módulo "Hola mundo" de la puerta de enlace.
![vista de directorio de log.txt](media/iot-hub-gateway-kit-lessons/lesson1/logtxtview.png)

Si tiene problemas, busque soluciones en la [página de solución de problemas](iot-hub-gateway-kit-c-troubleshooting.md).

## <a name="summary"></a>Resumen

¡Enhorabuena! Ha terminado de configurar Intel NUC como puerta de enlace. Ahora está listo para pasar a la lección siguiente, donde configurará el equipo host, creará una instancia de Azure IoT Hub y registrará el dispositivo lógico de la instancia de Azure IoT Hub.

## <a name="next-steps"></a>Pasos siguientes
[Uso de una puerta de enlace de IoT para conectar un dispositivo a Azure IoT Hub](iot-hub-gateway-kit-c-iot-gateway-connect-device-to-cloud.md)

