---
title: "Dispositivo simulado y puerta de enlace de Azure IoT: Lección 2: Registro del dispositivo | Microsoft Docs"
description: 
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "azure iot hub, nube de internet de las cosas, dispositivo de creación de azure iot hub, sensortag de ti, ble de ti"
ms.assetid: 23cfbe21-22c6-4fe1-ae41-63714a897f12
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/07/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 61e9a9fc7876094c04238c61cfc38efdd97b05f7
ms.openlocfilehash: 759085dcb4993d38f79cb19efe58e1a968ddb682


---

# <a name="create-your-azure-iot-hub-and-register-your-device"></a>Creación de una instancia de Azure IoT Hub y registro del dispositivo

## <a name="what-you-will-do"></a>Lo que hará

- Crear un grupo de recursos
- Crear su primera instancia de IoT Hub
- Registrar el dispositivo en IoT Hub mediante la CLI de Azure 

Al registrar el dispositivo en IoT Hub, el servicio Azure IoT Hub genera una clave para que el dispositivo la use cuando se autentique con el servicio. 

Si tiene problemas, busque soluciones en la [página de solución de problemas](iot-hub-gateway-kit-c-sim-troubleshooting.md).

## <a name="what-you-will-learn"></a>Lo qué aprenderá

En esta lección, aprenderá lo siguiente:

- Cómo usar la CLI de Azure para crear una instancia de IoT Hub.
- Cómo registrar un dispositivo en un IoT Hub.

## <a name="what-you-need"></a>Lo que necesita

- Una suscripción de Azure activa. Si no tiene ninguna cuenta de Azure, puede crear una [cuenta de evaluación gratuita de Azure](http://azure.microsoft.com/pricing/free-trial/) en solo unos minutos.
- Debe tener instalada la CLI de Azure.

## <a name="create-an-iot-hub"></a>Crear un centro de IoT

Para crear una instancia de IoT Hub, siga estos pasos:

1. Inicie sesión en la cuenta de Azure mediante el siguiente comando:

   ```bash
   az login
   ```

   Una vez que se haya iniciado sesión correctamente, se mostrarán todas las suscripciones disponibles.

2. Establezca la suscripción predeterminada de Azure que va a usar ejecutando el comando siguiente:

   ```bash
   az account set --subscription {subscription id or name}
   ```

   El valor de `subscription ID or name` puede verse en la salida del comando `az login` o del comando `az account list`.

3. Registre el proveedor con el siguiente comando. Los proveedores de recursos son servicios que proporcionan recursos para la aplicación. Debe registrar el proveedor para poder implementar el recurso de Azure que este le ofrece.

   ```bash
   az provider register -n "Microsoft.Devices"
   ```

4. Cree un grupo de recursos denominado `iot-gateway` en la región oeste de EE. UU. ejecutando el comando siguiente:

   ```bash
   az group create --name iot-gateway --location westus
   ```
   
   `westus` es la ubicación en la que se crea el grupo de recursos. Si desea utilizar otra ubicación, puede ejecutar `az account list-locations -o table` para ver todas las ubicaciones admitidas por Azure.

5. Cree una instancia de IoT Hub en el grupo de recursos `iot-gateway` mediante la ejecución del comando siguiente:

   ```bash
   az iot hub create --name {my hub name} --resource-group iot-gateway
   ```

De forma predeterminada, la herramienta crea la instancia de IoT Hub con el plan de tarifa Gratis. Para más información, consulte los [precios de Azure IoT Hub](https://azure.microsoft.com/pricing/details/iot-hub/).

> [!NOTE]
> El nombre de su instancia de IoT Hub debe única globalmente. Solo puede crear una edición F1 de Azure IoT Hub en su suscripción de Azure.

## <a name="register-your-device-in-your-iot-hub"></a>Registro del dispositivo en IoT Hub

Todos los dispositivos que envían mensajes al centro de IoT Hub y los reciben de este deben estar registrados con un identificador único.
Registre el dispositivo en IoT Hub ejecutando el comando siguiente:

```bash
az iot device create --device-id mydevice --hub-name {my hub name} --resource-group iot-gateway
```

## <a name="summary"></a>Resumen

Ha creado una instancia de IoT Hub y ha registrado en ella el dispositivo local con una identidad de dispositivo. Está listo para aprender a configurar y ejecutar una aplicación de ejemplo de puerta de enlace para enviar datos desde el dispositivo físico a la instancia de IoT Hub en la nube.

## <a name="next-steps"></a>Pasos siguientes
[Configuración y ejecución de una aplicación de ejemplo de carga a la nube de dispositivo simulado](iot-hub-gateway-kit-c-sim-lesson3-configure-simulated-device-app.md)


<!--HONumber=Jan17_HO4-->


