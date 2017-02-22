---
title: "Dispositivo SensorTag y puerta de enlace de Azure IoT: Lección 4: Creación de Function App | Microsoft Docs"
description: "Guarde mensajes de Intel NUC en su IoT Hub, escríbalos en una tabla de Azure Table Storage y, después, léalos desde la nube."
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: almacenar datos en la nube, datos almacenados en la nube, servicio en la nube de iot
ms.assetid: f84f9a85-e2c4-4a92-8969-f65eb34c194e
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 61e9a9fc7876094c04238c61cfc38efdd97b05f7
ms.openlocfilehash: 3672804218dbbe3a545536b8c4baccb2b9171cdc


---
# <a name="create-an-azure-function-app-and-storage-account"></a>Creación de una cuenta de Azure Storage y una aplicación de Azure Function

Azure Functions es una solución que permite ejecutar _funciones_ (pequeños fragmentos de código) en la nube fácilmente. Una aplicación de Azure Function hospeda la ejecución de sus funciones en Azure. 

## <a name="what-you-will-do"></a>Lo que hará

- Va a utilizar una plantilla de Azure Resource Manager para crear una instancia de Azure Function App y una cuenta de Azure Storage. La instancia de Azure Function App escucha los eventos de Azure IoT Hub, procesa los mensajes entrantes y los escribe en Azure Table Storage.

Si tiene problemas, busque soluciones en la [página de solución de problemas](iot-hub-gateway-kit-c-troubleshooting.md).


## <a name="what-you-will-learn"></a>Lo qué aprenderá

En esta lección, aprenderá lo siguiente:

- Uso de Azure Resource Manager para implementar recursos de Azure.
- Uso de una instancia de Azure Function App para procesar mensajes de IoT Hub y escribirlos en una tabla de Azure Table Storage.

## <a name="what-you-need"></a>Lo que necesita

Debe haber completado correctamente las lecciones anteriores:

- [Lección 1: Configuración de Intel NUC como puerta de enlace de IoT](iot-hub-gateway-kit-c-lesson1-set-up-nuc.md)
- [Lección 2: Preparación del equipo host y de IoT Hub de Azure](iot-hub-gateway-kit-c-lesson2-get-the-tools-win32.md)
- [Lección 3: Recepción de mensajes de SensorTag y lectura en IoT Hub](iot-hub-gateway-kit-c-lesson3-configure-ble-app.md)

## <a name="open-a-sample-app"></a>Apertura de una aplicación de ejemplo

Vaya a su carpeta del repositorio `iot-hub-c-intel-nuc-gateway-getting-started`, inicialice los archivos de configuración y, después, abra el proyecto de ejemplo en Visual Studio Code ejecutando el siguiente comando:

```bash
cd Lesson4
npm install
gulp init
code .
```

![estructura del repositorio](media/iot-hub-gateway-kit-lessons/lesson4/arm_template.png)

- El archivo `arm-template.json` es una plantilla de Azure Resource Manager que contiene una instancia de Azure Function App y una cuenta de Azure Storage.
- El archivo `arm-template-param.json` es el archivo de configuración que utiliza la plantilla de Azure Resource Manager.
- La subcarpeta `ReceiveDeviceMessages` contiene el código de Node.js para la función de Azure.

## <a name="configure-azure-resource-manager-templates-and-create-resources-in-azure"></a>Configuración de las plantillas de Azure Resource Manager y creación de recursos de Azure

Actualice el archivo `arm-template-param.json` en Visual Studio Code.

![arm template json](media/iot-hub-gateway-kit-lessons/lesson4/arm_template_param.png)

- Sustituya `[your IoT Hub name]` por el valor `{my hub name}` que especificó en la lección 2.

Después de actualizar el archivo `arm-template-param.json`, implemente los recursos en Azure ejecutando el comando siguiente:

```bash
az group deployment create --template-file arm-template.json --parameters @arm-template-param.json -g iot-gateway
```

Use `iot-gateway` como el valor de `{resource group name}` si no lo cambió en la lección 2.

## <a name="summary"></a>Resumen

Ha creado una instancia de Azure Function App para procesar mensajes de IoT Hub, así como una cuenta de Azure Storage para almacenar estos mensajes. Ahora puede leer los mensajes que envía su puerta de enlace al IoT Hub.

## <a name="next-steps"></a>Pasos siguientes
[Read messages persisted in Azure Storage](iot-hub-gateway-kit-c-lesson4-read-table-storage.md) (Lectura de los mensajes que se conservan en Azure Storage).



<!--HONumber=Jan17_HO4-->


