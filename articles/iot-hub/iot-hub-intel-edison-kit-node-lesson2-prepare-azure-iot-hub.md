---
title: "Conexión de Intel Edison (Node) a Azure IoT: Lección 2: Registro del dispositivo | Microsoft Docs"
description: Cree un grupo de recursos, cree una instancia de IoT Hub de Azure y registre Edison en IoT Hub de Azure mediante la CLI de Azure.
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: 
ms.assetid: c1465cc2-d0d9-4326-967c-64d95b61dd54
ms.service: iot-hub
ms.devlang: nodejs
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: adf5b10721a28432e6b37ef73c6a7e7ec9f93cdd
ms.openlocfilehash: 384724ccf78dc08b215ef602ff4366dd54801ab5
ms.lasthandoff: 01/25/2017


---
# <a name="create-your-iot-hub-and-register-intel-edison"></a>Creación de un centro de IoT Hub y registro de Intel Edison
## <a name="what-you-will-do"></a>Lo que hará
* Cree un grupo de recursos.
* Cree una instancia de IoT Hub de Azure en el grupo de recursos.
* Agregue Intel Edison al centro de IoT Hub de Azure mediante la interfaz de la línea de comandos de Azure (CLI de Azure).

Cuando utiliza la CLI de Azure para agregar Edison a IoT Hub, el servicio genera una clave para que Edison se autentique con el servicio. Si tiene problemas, busque soluciones en [esta página][troubleshooting].

## <a name="what-you-will-learn"></a>Lo qué aprenderá
En este artículo, aprenderá lo siguiente:
* Cómo usar la CLI de Azure para crear un centro de IoT Hub
* Cómo crear una identidad de dispositivos para Edison en IoT Hub

## <a name="what-you-need"></a>Lo que necesita
* Una cuenta de Azure. Si no tiene ninguna cuenta de Azure, cree una [cuenta de evaluación gratuita de Azure](http://azure.microsoft.com/pricing/free-trial/) en solo unos minutos.
* Debe tener instalada la CLI de Azure.

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


## <a name="register-edison-in-your-iot-hub"></a>Registro de Edison en un centro de IoT Hub
Todos los dispositivos que envían mensajes al centro de IoT Hub y los reciben de este deben estar registrados con un identificador único.

Registre Edison en su centro de IoT Hub ejecutando el comando siguiente:

```bash
az iot device create --device-id myinteledison --hub-name {my hub name}
```

## <a name="summary"></a>Resumen
Ha creado un centro de IoT Hub y ha registrado Edison con una identidad de dispositivo en este centro. Ya está preparado para aprender a enviar mensajes desde Edison a IoT Hub.

## <a name="next-steps"></a>Pasos siguientes
[Creación de una instancia de Azure Function App y una cuenta de Azure Storage para procesar y almacenar mensajes de IoT Hub][process-and-store-iot-hub-messages]


<!-- Images and links -->

[troubleshooting]: iot-hub-intel-edison-kit-node-troubleshooting.md
[process-and-store-iot-hub-messages]: iot-hub-intel-edison-kit-node-lesson3-deploy-resource-manager-template.md
