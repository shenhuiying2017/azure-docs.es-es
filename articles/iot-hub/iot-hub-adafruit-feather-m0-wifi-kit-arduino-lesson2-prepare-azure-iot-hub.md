---
title: "Creación de una instancia de IoT Hub de Azure y registro de Adafruit Feather M0 WiFi | Microsoft Docs"
description: Cree un grupo de recursos, una instancia de IoT Hub de Azure y registre Adafruit Feather M0 WiFi en IoT Hub de Azure mediante la CLI de Azure.
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "conexión de Arduino a la nube, IoT Hub de Azure, nube de Internet de las cosas, creación de dispositivos en IoT Hub de Azure, nube de Arduino"
ms.assetid: 5edc690b-7a1d-4ebc-b011-ff27bfffe6e8
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/13/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 9e8084fe26229ef9ed1676c0a3c34e0ee7be88b1
ms.openlocfilehash: 232c391e61e7e7ec053d2e7170f9abe4c79d9528


---
# <a name="create-your-iot-hub-and-register-your-adafruit-feather-m0-wifi-arduino-board"></a>Creación de una instancia de IoT Hub de Azure y registro de Adafruit Feather M0 WiFi

## <a name="what-you-will-do"></a>Lo que hará
* Cree un grupo de recursos.
* Cree una instancia de IoT Hub de Azure en el grupo de recursos.
* Agregue la placa de Arduino al centro de IoT Hub de Azure mediante la interfaz de la línea de comandos de Azure (CLI de Azure).

Cuando utiliza la CLI de Azure para agregar la placa de Arduino a IoT Hub, el servicio genera una clave para que la placa de Arduino se autentique con el servicio. Si tiene problemas, busque soluciones en [esta página][troubleshoot].

## <a name="what-you-will-learn"></a>Lo qué aprenderá
En este artículo, aprenderá lo siguiente:
* Cómo usar la CLI de Azure para crear un centro de IoT Hub
* Cómo crear una identidad de dispositivos para la placa de Arduino en el centro de IoT Hub

## <a name="what-you-need"></a>Lo que necesita
* Una cuenta de Azure
* Un equipo con la CLI de Azure instalada

## <a name="create-your-iot-hub"></a>Creación de un centro de IoT Hub
IoT Hub de Azure ayuda a conectar, supervisar y administrar millones de activos de IoT. Para crear el centro de IoT Hub, siga estos pasos:

1. Inicie sesión en la cuenta de Azure mediante el siguiente comando:

   ```bash
   az login
   ```

   Una vez que la sesión se ha iniciado correctamente, aparecen todas las suscripciones disponibles.

2. Establezca la suscripción predeterminada que desea usar mediante el siguiente comando:

   ```bash
   az account set --subscription {subscription id or name}
   ```

   El valor de `subscription ID or name` puede verse en la salida del comando `az login` o del comando `az account list`.

3. Registre el proveedor con el siguiente comando. Los proveedores de recursos son servicios que proporcionan recursos para la aplicación. Debe registrar el proveedor para poder implementar el recurso de Azure que este le ofrece.

   ```bash
   az provider register -n "Microsoft.Devices"
   ```
4. Cree un grupo de recursos denominado "iot-sample" en la región oeste de EE. UU. ejecutando el comando siguiente:

   ```bash
   az group create --name iot-sample --location westus
   ```

   `westus` es la ubicación en la que se crea el grupo de recursos. Si desea utilizar otra ubicación, puede ejecutar `az account list-locations -o table` para ver todas las ubicaciones admitidas por Azure.

5. Cree una instancia de IoT Hub en el grupo de recursos iot-sample ejecutando el comando siguiente:

   ```bash
   az iot hub create --name {my hub name} --resource-group iot-sample
   ```

De forma predeterminada, la herramienta crea la instancia de IoT Hub con el plan de tarifa Gratis. Para más información, consulte los [precios de Azure IoT Hub](https://azure.microsoft.com/pricing/details/iot-hub/).

> [!NOTE]
> El nombre de su instancia de IoT Hub debe única globalmente.
> Solamente puede crear una edición F1 de Azure IoT Hub en la suscripción de Azure.

## <a name="register-your-arduino-board-in-your-iot-hub"></a>Registro de la placa de Arduino en el centro de IoT Hub
Todos los dispositivos que envían mensajes al centro de IoT Hub y los reciben de este deben estar registrados con un identificador único.

Registre la placa de Arduino en su instancia de IoT Hub ejecutando el comando siguiente:

```bash
az iot device create --device-id mym0wifi --hub-name {my hub name}
```

## <a name="summary"></a>Resumen
Ha creado un centro de IoT Hub y ha registrado la placa de Arduino con una identidad de dispositivo en este centro de IoT Hub. Ya está preparado para aprender a enviar mensajes desde la placa de Arduino a IoT Hub.

## <a name="next-steps"></a>Pasos siguientes
[Creación de una instancia de Azure Function App y una cuenta de Azure Storage para procesar y almacenar mensajes de IoT Hub][process-and-store-iot-hub-messages]


<!-- Images and links -->

[troubleshoot]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-troubleshooting.md
[process-and-store-iot-hub-messages]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson3-deploy-resource-manager-template.md


<!--HONumber=Dec16_HO2-->


