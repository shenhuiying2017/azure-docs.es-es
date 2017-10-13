---
title: "Conversión de datos en la puerta de enlace de IoT con Azure IoT Edge | Microsoft Docs"
description: "Use la puerta de enlace de IoT para convertir el formato de los datos del sensor mediante un módulo personalizado desde Azure IoT Edge."
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "conversión de datos de puerta de enlace de iot, transformación de datos de puerta de enlace de iot"
ms.assetid: 75f2573d-500b-4405-bff7-61021c4c3500
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/25/2017
ms.author: xshi
ms.openlocfilehash: d5c735a4adbc59e9526ec4fd40720c5ec136d63d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="use-iot-gateway-for-sensor-data-transformation-with-azure-iot-edge"></a>Uso de la puerta de enlace de IoT para la transformación de datos de sensor con Azure IoT Edge

> [!NOTE]
> Antes de empezar este tutorial, asegúrese de que ha completado las siguientes lecciones en orden:
> * [Configuración de Intel NUC como una puerta de enlace de IoT](iot-hub-gateway-kit-c-lesson1-set-up-nuc.md)
> * [Uso de una puerta de enlace de IoT para conectar dispositivos a la nube: SensorTag a Azure IoT Hub](iot-hub-gateway-kit-c-iot-gateway-connect-device-to-cloud.md)

Un objetivo de una puerta de enlace de IoT es procesar los datos recopilados antes de enviarlos a la nube. Azure IoT Edge introduce módulos que se pueden crear y ensamblar para formar el flujo de trabajo de procesamiento de datos. Un módulo recibe un mensaje, realiza alguna acción en él y, a continuación, lo mueve a otros módulos para su procesamiento.

## <a name="what-you-learn"></a>Conocimientos que adquirirá

Aprenderá a crear un módulo para convertir mensajes del formato SensorTag a otro formato diferente.

## <a name="what-you-do"></a>Qué debe hacer

* Cree un módulo para convertir un mensaje recibido al formato .json.
* Compile el módulo.
* Agregue el módulo a la aplicación de ejemplo BLE desde Azure IoT Edge.
* Ejecute la aplicación de ejemplo.

## <a name="what-you-need"></a>Lo que necesita

* Los siguientes tutoriales completados en orden:
  * [Configuración de Intel NUC como una puerta de enlace de IoT](iot-hub-gateway-kit-c-lesson1-set-up-nuc.md)
  * [Uso de una puerta de enlace de IoT para conectar dispositivos a la nube: SensorTag a Azure IoT Hub](iot-hub-gateway-kit-c-iot-gateway-connect-device-to-cloud.md)
* Un cliente de SSH que se ejecute en el equipo host. En Windows se recomienda PuTTY. Linux y macOS ya vienen con un cliente de SSH.
* La dirección IP y el nombre de usuario y la contraseña para acceder a la puerta de enlace desde el cliente de SSH.
* Una conexión a Internet.

## <a name="create-a-module"></a>Creación de un módulo

1. En el equipo host, ejecute el cliente de SSH y conéctelo a la puerta de enlace de IoT.
1. Clone los archivos de origen del módulo de conversión desde GitHub al directorio principal de la puerta de enlace de IoT mediante la ejecución de los comandos siguientes:

   ```bash
   cd ~
   git clone https://github.com/Azure-Samples/iot-hub-c-intel-nuc-gateway-customized-module.git
   ```

   Se trata de un módulo nativo de Azure Edge escrito en el lenguaje de programación C. El módulo convierte el formato de los mensajes recibidos en el siguiente:

   ```json
   {"deviceId": "Intel NUC Gateway", "messageId": 0, "temperature": 0.0}
   ```

## <a name="compile-the-module"></a>Compilación del módulo

Para compilar el módulo, ejecute los comandos siguientes:

```bash
cd iot-hub-c-intel-nuc-gateway-customized-module/my_module
# change the build script runnable
chmod 777 build.sh
# remove the invalid windows character
sed -i -e "s/\r$//" build.sh
# run the build shell script
./build.sh
```

Obtendrá un archivo `libmy_module.so` una vez completada la compilación. Tome nota de la ruta de acceso absoluta de este archivo.

## <a name="add-the-module-to-the-ble-sample-application"></a>Incorporación del módulo a la aplicación de ejemplo BLE

1. Vaya a la carpeta de ejemplos. Para ello, ejecute el siguiente comando:

   ```bash
   cd /usr/share/azureiotgatewaysdk/samples
   ```

1. Abra el archivo de configuración al ejecutar el siguiente comando:

   ```bash
   vi ble_gateway.json
   ```

1. Agregue un módulo insertando el código siguiente en la sección `modules`.

   ```json
   {
       "name": "MyModule",
       "loader": {
           "name": "native",
           "entrypoint":{
               "module.path": "[Your libmy_module.so path]"
               }
            },
        "args": null
    },
    ```

1. Reemplace `[Your libmy_module.so path]` en el código por la ruta de acceso absoluta del archivo libmy_module.so'.
1. Reemplace el código de la sección `links` por el código siguiente:

   ```json
   {
       "source": "SensorTag",
       "sink": "MyModule"
   },
   {
       "source": "MyModule",
       "sink": "mapping"
   }
   ```

1. Presione `ESC` y, a continuación, escriba `:wq` para guardar el archivo.

## <a name="run-the-sample-application"></a>Ejecutar la aplicación de ejemplo

1. Encienda el SensorTag.
1. Establezca la variable de entorno SSL_CERT_FILE mediante la ejecución del comando siguiente:

   ```bash
   export SSL_CERT_FILE=/etc/ssl/certs/ca-certificates.crt
   ```

1. Ejecute la aplicación de ejemplo con el módulo agregado mediante la ejecución del siguiente comando:

   ```bash
   ./ble_gateway ble_gateway.json
   ```

## <a name="next-steps"></a>Pasos siguientes

Ha usado correctamente la puerta de enlace de IoT para convertir el mensaje de SensorTag en el formato .json.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
