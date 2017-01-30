---
title: "Creación de una instancia de Azure Function App y una cuenta de Azure Storage | Microsoft Docs"
description: La instancia de Azure Function App escucha los eventos de Azure IoT Hub, procesa los mensajes entrantes y los escribe en Azure Table Storage.
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: almacenar datos en la nube, datos almacenados en la nube, servicio en la nube de iot
ms.assetid: 9c8f4cd1-9511-4601-ad7e-51761a986753
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/13/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 946d66cc7ed1173e7b2b92dae90a1ce69ba3c165
ms.openlocfilehash: 18806dc515a9a8951134d580a2c0664ff503e17c


---

# <a name="create-an-azure-function-app-and-azure-storage-account"></a>Creación de una instancia de Azure Function App y una cuenta de Azure Storage
[Azure Functions](../../articles/azure-functions/functions-overview.md) es una solución que permite ejecutar *funciones* (pequeños fragmentos de código) en la nube fácilmente. Una aplicación de Azure Function hospeda la ejecución de sus funciones en Azure.

## <a name="what-will-you-do"></a>Qué hará
Va a utilizar una plantilla de Azure Resource Manager para crear una instancia de Azure Function App y una cuenta de Azure Storage. La instancia de Azure Function App escucha los eventos de Azure IoT Hub, procesa los mensajes entrantes y los escribe en Azure Table Storage.

Si tiene problemas, busque soluciones en [la página de solución de problemas para la placa Adafruit Feather M0 WiFi Arduino](iot-hub-adafruit-feather-m0-wifi-kit-arduino-troubleshooting.md).

## <a name="what-will-you-learn"></a>Qué aprenderá
En este artículo, aprenderá lo siguiente:
* Cómo utilizar [Azure Resource Manager](../../articles/azure-resource-manager/resource-group-overview.md) para implementar recursos de Azure.
* Cómo utilizar una instancia de Azure Function App para procesar mensajes de IoT Hub y escribirlos en una tabla de Azure Table Storage.

## <a name="what-do-you-need"></a>Qué necesita
Debe haber completado correctamente los siguientes tutoriales:
- [Get started with your Arduino board][get-started] (Introducción a la placa de Arduino)
- [Create your Azure IoT hub][create-iot-hub] (Creación de un Azure IoT Hub)

## <a name="open-the-sample-app"></a>Apertura de la aplicación de ejemplo
Abra el proyecto de ejemplo en Visual Studio Code ejecutando los comandos siguientes:

```bash
cd Lesson3
code .
```

![Estructura del repositorio][repo-structure]

* El archivo `app.ino` de la subcarpeta `app` es el archivo de origen de la clave. Este archivo de origen contiene el código para enviar un mensaje 20 veces en su instancia de IoT Hub y hacer parpadear el LED para cada mensaje que envía.
* `config.json` contiene valores de configuración necesarios.
* El archivo `arm-template.json` es una plantilla de Azure Resource Manager que contiene una instancia de Azure Function App y una cuenta de Azure Storage.
* El archivo `arm-template-param.json` es el archivo de configuración que utiliza la plantilla de Azure Resource Manager.
* La subcarpeta `ReceiveDeviceMessages` contiene el código de Node.js para la función de Azure.

## <a name="configure-azure-resource-manager-templates-and-create-resources-in-azure"></a>Configuración de las plantillas de Azure Resource Manager y creación de recursos de Azure
Actualice el archivo `arm-template-param.json` en Visual Studio Code.

![Parámetros de plantilla de Azure Resource Manager][arm-template-params]

* Sustituya **[su nombre de IoT Hub]** por el valor **{mi nombre de centro}** que especificó cuando [creó el IoT Hub y registró la placa de Arduino][created-iot-hub-and-registered-arduino-board].
* Reemplace **[prefix string for new resources]** por el prefijo que desee. El prefijo garantiza que el nombre del recurso sea globalmente único para evitar conflictos. No utilice guiones ni números al comienzo del prefijo.

Después de actualizar el archivo `arm-template-param.json`, implemente los recursos en Azure ejecutando el comando siguiente:

```bash
az group deployment create --template-file arm-template.json --parameters @arm-template-param.json -g iot-sample
```

Este proceso tarda aproximadamente cinco minutos en crear estos recursos. Mientras espera a que se ejecute la creación de recursos, puede pasar al siguiente artículo.

## <a name="summary"></a>Resumen
Ha creado una instancia de Azure Function App para procesar mensajes de IoT Hub, así como una cuenta de Azure Storage para almacenar estos mensajes. Ahora puede implementar y ejecutar el ejemplo para enviar mensajes del dispositivo a la nube en la placa de Arduino.

## <a name="next-steps"></a>Pasos siguientes
[Run a sample application to send device-to-cloud messages on your Arduino board][send-device-to-cloud-messages] (Ejecución de una aplicación de ejemplo para enviar mensajes de dispositivo a nube en la placa de Arduino)

<!-- Images and links -->

[get-started]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-get-started.md
[create-iot-hub]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson2-prepare-azure-iot-hub.md
[repo-structure]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson3/repo_structure_c.png
[arm-template-params]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson3/arm_para_arduino.png
[created-iot-hub-and-registered-arduino-board]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson2-prepare-azure-iot-hub.md
[send-device-to-cloud-messages]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson3-run-azure-blink.md


<!--HONumber=Dec16_HO2-->


