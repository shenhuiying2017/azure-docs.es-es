---
title: "Conexión de un dispositivo mediante Node.js | Microsoft Docs"
description: "Se describe cómo conectar un dispositivo a la solución de supervisión remota preconfigurada del Conjunto de IoT de Azure mediante una aplicación creada en Node.js."
services: 
suite: iot-suite
documentationcenter: na
author: dominicbetts
manager: timlt
editor: 
ms.assetid: fc50a33f-9fb9-42d7-b1b8-eb5cff19335e
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/04/2017
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: 02247d679c9bffa01b8d2827ea57eba38894107e
ms.openlocfilehash: 698384bb5079feb937f423f9b6365dff20cfa3be


---
# <a name="connect-your-device-to-the-remote-monitoring-preconfigured-solution-nodejs"></a>Conectar el dispositivo a la solución preconfigurada de supervisión remota (Node.js)
[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

## <a name="build-and-run-the-nodejs-sample-solution"></a>Compilar y ejecutar la solución de ejemplo de node.js
1. Para clonar el repositorio de GitHub del *SDK de IoT de Microsoft Azure para Node.js* e instalarlo en su entorno de escritorio, siga las instrucciones que se describen en [Prepare your development environment][lnk-github-prepare] (Preparación del entorno de desarrollo).
2. En la copia local del repositorio [azure-iot-sdk-node][lnk-github-repo], copie los dos archivos siguientes de la carpeta device/samples a una carpeta del dispositivo:
   
   * package.json
   * remote_monitoring.js
3. Abra el archivo remote-monitoring.js y busque la siguiente variable:
   
    ```
    var connectionString = "[IoT Hub device connection string]";
    ```
4. Reemplace **[IoT Hub device connection string]** con la cadena de conexión al dispositivo. Puede encontrar los valores para su nombre de host del Centro de IoT, el id. de dispositivo y la clave de dispositivo en el panel de solución de supervisión remota. Una cadena de conexión de dispositivo tiene el siguiente formato:
   
    ```
    HostName={your IoT Hub hostname};DeviceId={your device id};SharedAccessKey={your device key}
    ```
   
    Si el nombre de host de IoT Hub es **contoso** y el identificador del dispositivo es **mydevice**, la cadena de conexión será similar a la siguiente:
   
    ```
    var connectionString = "HostName=contoso.azure-devices.net;DeviceId=mydevice;SharedAccessKey=2s ... =="
    ```
5. Guarde el archivo . Para instalar los paquetes necesarios, ejecute los siguientes comandos en un símbolo del sistema de la carpeta que contiene estos archivos y, a continuación, ejecute la aplicación de ejemplo:
   
    ```
    npm install --save
    node remote_monitoring.js
    ```

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]

[lnk-github-repo]: https://github.com/azure/azure-iot-sdk-node
[lnk-github-prepare]: https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md



<!--HONumber=Dec16_HO3-->


