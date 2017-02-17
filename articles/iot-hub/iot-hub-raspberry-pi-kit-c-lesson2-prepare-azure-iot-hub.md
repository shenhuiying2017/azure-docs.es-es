---
title: "Conexión de Raspberry Pi (C) a Azure IoT: Lección 2: Registro del dispositivo | Microsoft Docs"
description: Cree un grupo de recursos, cree una instancia de IoT Hub de Azure y registre Pi en IoT Hub de Azure mediante la CLI de Azure.
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "nube de raspberry pi, conexión a la nube de pi"
ms.assetid: 4bcfb071-b3ae-48cc-8ea5-7e7434732287
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/28/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 64e69df256404e98f6175f77357500b562d74318
ms.openlocfilehash: d7bc143b509e84d50f3ce0164d31ae51a26f75d4


---
# <a name="create-your-iot-hub-and-register-raspberry-pi-3"></a>Creación de un centro de IoT y registro de Raspberry Pi 3
## <a name="what-you-will-do"></a>Lo que hará
* Cree un grupo de recursos.
* Cree una instancia de IoT Hub de Azure en el grupo de recursos.
* Agregue Raspberry Pi 3 al centro de Azure IoT Hub mediante la interfaz de la línea de comandos de Azure (CLI de Azure).

Si utiliza la CLI de Azure para agregar Pi al centro de IoT Hub, el servicio generará una clave para autenticar Pi en el servicio. Si tiene problemas, busque soluciones en la [página de solución de problemas](iot-hub-raspberry-pi-kit-c-troubleshooting.md).

## <a name="what-you-will-learn"></a>Lo qué aprenderá
En este artículo, aprenderá lo siguiente:
* Cómo usar la CLI de Azure para crear un centro de IoT Hub
* Cómo crear una identidad de dispositivos para Pi en IoT Hub

## <a name="what-you-need"></a>Lo que necesita
* Una cuenta de Azure
* Un equipo Mac o un equipo Windows con la CLI de Azure instalada

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
> El nombre de su instancia de IoT Hub debe única globalmente. Solamente puede crear una edición F1 de Azure IoT Hub en la suscripción de Azure.

## <a name="register-pi-in-your-iot-hub"></a>Registro de Pi en el centro de IoT hub
Todos los dispositivos que envían mensajes al centro de IoT Hub y los reciben de este deben estar registrados con un identificador único.

Registre Pi en el centro de ejecutando el comando siguiente:

```bash
az iot device create --device-id myraspberrypi --hub {my hub name} --resource-group iot-sample
```

## <a name="summary"></a>Resumen
Ha creado un centro de IoT Hub y ha registrado Pi con una identidad de dispositivo en este centro. Ya está preparado para aprender a enviar mensajes desde Pi a IoT Hub.

## <a name="next-steps"></a>Pasos siguientes
[Creación de una instancia de Azure Function App y una cuenta de Azure Storage para procesar y almacenar mensajes de IoT Hub](iot-hub-raspberry-pi-kit-c-lesson3-deploy-resource-manager-template.md)




<!--HONumber=Jan17_HO4-->


