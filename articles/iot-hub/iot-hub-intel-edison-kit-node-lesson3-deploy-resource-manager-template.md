---
title: "Conexión de Intel Edison (Node) a Azure IoT: Lección 3: Creación de Function App | Microsoft Docs"
description: La instancia de Azure Function App escucha los eventos de Azure IoT Hub, procesa los mensajes entrantes y los escribe en Azure Table Storage.
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: almacenar datos en la nube, datos almacenados en la nube, servicio en la nube de iot
ms.assetid: 37ee5962-95ce-40e8-8162-17e735eaec21
ms.service: iot-hub
ms.devlang: nodejs
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/8/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: adf5b10721a28432e6b37ef73c6a7e7ec9f93cdd
ms.openlocfilehash: 72d1496fcf353662fa2f7b003ee3757c45b8181e


---
# <a name="create-an-azure-function-app-and-azure-storage-account"></a>Creación de una instancia de Azure Function App y una cuenta de Azure Storage
[Azure Functions](../../articles/azure-functions/functions-overview.md) es una solución que permite ejecutar *funciones* (pequeños fragmentos de código) en la nube fácilmente. Una aplicación de Azure Function hospeda la ejecución de sus funciones en Azure.

## <a name="what-will-you-do"></a>Qué hará
Va a utilizar una plantilla de Azure Resource Manager para crear una instancia de Azure Function App y una cuenta de Azure Storage. La instancia de Azure Function App escucha los eventos de Azure IoT Hub, procesa los mensajes entrantes y los escribe en Azure Table Storage. La cuenta de almacenamiento se usa para leer las copias conservadas de mensajes de la tabla de Azure. Si tiene problemas, busque soluciones en [esta página][troubleshooting].

## <a name="what-will-you-learn"></a>Qué aprenderá
En este artículo, aprenderá lo siguiente:
* Cómo utilizar [Azure Resource Manager](../../articles/azure-resource-manager/resource-group-overview.md) para implementar recursos de Azure.
* Cómo utilizar una instancia de Azure Function App para procesar mensajes de IoT Hub y escribirlos en una tabla de Azure Table Storage.

## <a name="what-do-you-need"></a>Qué necesita
Debe haber completado correctamente los siguientes tutoriales:
- [Introducción a Intel Edison][get-started-with-your-intel-edison]
- [Creación de un centro de IoT Hub de Azure][create-your-azure-iot-hub]

## <a name="open-the-sample-app"></a>Apertura de la aplicación de ejemplo
Abra el proyecto de ejemplo en Visual Studio Code ejecutando los comandos siguientes:

```bash
cd Lesson3
code .
```

![Estructura del repositorio][repo-structure]

* El archivo de la subcarpeta `app` es el archivo de origen de la clave. Este archivo de origen contiene el código para enviar un mensaje 20 veces en su instancia de IoT Hub y hacer parpadear el LED para cada mensaje que envía.
* El archivo `arm-template.json` es una plantilla de Azure Resource Manager que contiene una instancia de Azure Function App y una cuenta de Azure Storage.
* El archivo `arm-template-param.json` es el archivo de configuración que utiliza la plantilla de Azure Resource Manager.
* La subcarpeta `ReceiveDeviceMessages` contiene el código de Node.js para la función de Azure.

## <a name="configure-azure-resource-manager-templates-and-create-resources-in-azure"></a>Configuración de las plantillas de Azure Resource Manager y creación de recursos de Azure
Actualice el archivo `arm-template-param.json` en Visual Studio Code.

![Parámetros de plantilla de Azure Resource Manager][arm-template-parameters]

* Sustituya **[su nombre de centro de IoT Hub]** por **{mi nombre de centro}** que especificó cuando [creó el centro de IoT Hub y registró Intel Edison][created-your-iot-hub-and-registered-intel-edison].
* Reemplace **[prefix string for new resources]** por el prefijo que desee. El prefijo garantiza que el nombre del recurso sea globalmente único para evitar conflictos. No utilice guiones ni números al comienzo del prefijo.

Después de actualizar el archivo `arm-template-param.json`, implemente los recursos en Azure ejecutando el comando siguiente:

```bash
az group deployment create --template-file arm-template.json --parameters @arm-template-param.json -g iot-sample
```

Este proceso tarda aproximadamente cinco minutos en crear estos recursos. Mientras espera a que se ejecute la creación de recursos, puede pasar al siguiente artículo.

## <a name="summary"></a>Resumen
Ha creado una instancia de Azure Function App para procesar mensajes de IoT Hub, así como una cuenta de Azure Storage para almacenar estos mensajes. Ahora puede implementar y ejecutar el ejemplo para enviar mensajes de dispositivo a nube en Edison.

## <a name="next-steps"></a>Pasos siguientes
[Ejecución de una aplicación de ejemplo para enviar mensajes de dispositivo a nube en Intel Edison][send-device-to-cloud-messages]
<!-- Images and links -->

[troubleshooting]: iot-hub-intel-edison-kit-node-troubleshooting.md
[get-started-with-your-intel-edison]: iot-hub-intel-edison-kit-node-get-started.md
[create-your-azure-iot-hub]: iot-hub-intel-edison-kit-node-get-started.md
[repo-structure]: media/iot-hub-intel-edison-lessons/lesson3/repo_structure.png
[arm-template-parameters]: media/iot-hub-intel-edison-lessons/lesson3/arm_para.png
[created-your-iot-hub-and-registered-intel-edison]: iot-hub-intel-edison-kit-node-lesson2-prepare-azure-iot-hub.md
[send-device-to-cloud-messages]: iot-hub-intel-edison-kit-node-lesson3-run-azure-blink.md


<!--HONumber=Jan17_HO4-->


