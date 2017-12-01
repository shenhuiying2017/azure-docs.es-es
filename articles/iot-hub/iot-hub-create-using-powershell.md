---
title: "Creación de una instancia de Azure IoT Hub mediante un cmdlet de PowerShell | Microsoft Docs"
description: "Cómo usar un cmdlet de PowerShell para crear una instancia de IoT Hub."
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/08/2017
ms.author: dobett
ms.openlocfilehash: 1d22e1a20d18e06686f893b1f4ae22e6373633b3
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/18/2017
---
# <a name="create-an-iot-hub-using-the-new-azurermiothub-cmdlet"></a>Creación de una instancia de IoT Hub mediante el cmdlet New-AzureRmIotHub

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## <a name="introduction"></a>Introducción

Puede usar cmdlets de Azure PowerShell para crear y administrar instancias de Azure IoT Hub. En este tutorial se muestra cómo crear una instancia de IoT Hub con PowerShell.

> [!NOTE]
> Azure tiene dos modelos de implementación diferentes para crear y trabajar con recursos: [Azure Resource Manager y el modelo clásico](../azure-resource-manager/resource-manager-deployment-model.md). Este artículo trata sobre el uso del modelo de implementación de Azure Resource Manager.

Para completar este tutorial, necesitará lo siguiente:

* Una cuenta de Azure activa. <br/>Si no tiene ninguna, puede crear una [cuenta gratuita][lnk-free-trial] en tan solo unos minutos.
* [Cmdlets de Azure PowerShell][lnk-powershell-install].

## <a name="connect-to-your-azure-subscription"></a>Conexión a su suscripción de Azure
En un símbolo del sistema de PowerShell, escriba el siguiente comando para iniciar sesión en su suscripción de Azure:

```powershell
Login-AzureRmAccount
```

Si tiene varias suscripciones de Azure, el inicio de sesión en Azure le concede acceso a todas las suscripciones de Azure asociadas a sus credenciales. Use el siguiente comando para mostrar las suscripciones de Azure que están disponibles para su uso:

```powershell
Get-AzureRMSubscription
```

Use el siguiente comando para seleccionar la suscripción que desea usar para ejecutar los comandos que crearán la instancia de IoT Hub. Puede usar el nombre de la suscripción o el identificador de la salida del comando anterior:

```powershell
Select-AzureRMSubscription `
    -SubscriptionName "{your subscription name}"
```

## <a name="create-resource-group"></a>Creación de un grupo de recursos

Necesita un grupo de recursos para implementar una instancia de IoT Hub. Puede usar un grupo de recursos existente o crear uno nuevo.

Puede usar el comando siguiente para detectar las ubicaciones en las que puede implementar una instancia de IoT Hub:

```powershell
((Get-AzureRmResourceProvider `
  -ProviderNamespace Microsoft.Devices).ResourceTypes `
  | Where-Object ResourceTypeName -eq IoTHubs).Locations
```

Para crear un grupo de recursos para la instancia de IoT Hub en una de las ubicaciones compatibles con IoT Hub, use el comando siguiente. En este ejemplo se crea un grupo de recursos denominado **MyIoTRG1** en la región **Este de EE. UU.**:

```powershell
New-AzureRmResourceGroup -Name MyIoTRG1 -Location "East US"
```

## <a name="create-an-iot-hub"></a>Crear un centro de IoT

Para crear una instancia de IoT Hub en el grupo de recursos que creó en el paso anterior, use el comando siguiente. Este ejemplo crea un centro **S1** llamado **MyTestIoTHub** en la región **Este de EE. UU.**:

```powershell
New-AzureRmIotHub `
    -ResourceGroupName MyIoTRG1 `
    -Name MyTestIoTHub `
    -SkuName S1 -Units 1 `
    -Location "East US"
```

El nombre de la instancia de IoT Hub debe ser único.

[!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]


Para ver todas las instancias de IoT Hub de su suscripción, use el comando siguiente:

```powershell
Get-AzureRmIotHub
```

El ejemplo anterior agrega una instancia de IoT Hub S1 estándar que se le cobrará. Puede eliminar la instancia de IoT Hub con el comando siguiente:

```powershell
Remove-AzureRmIotHub `
    -ResourceGroupName MyIoTRG1 `
    -Name MyTestIoTHub
```

También puede quitar un grupo de recursos y todos los recursos que contiene con el comando siguiente:

```powershell
Remove-AzureRmResourceGroup -Name MyIoTRG1
```

## <a name="next-steps"></a>Pasos siguientes

Ahora que implementó una instancia de IoT Hub mediante un cmdlet de PowerShell, quizás desee seguir explorando:

* Descubra otros [cmdlets de PowerShell para trabajar con la instancia de IoT Hub][lnk-iothub-cmdlets].
* Consulte las funcionalidades de la [API de REST del proveedor de recursos de IoT Hub][lnk-rest-api].

Para obtener más información sobre cómo desarrollar para IoT Hub, consulte los siguientes artículos:

* [Introducción al SDK de C][lnk-c-sdk]
* [SDK de IoT de Azure][lnk-sdks]

Para explorar aún más las funcionalidades de IoT Hub, consulte:

* [Implementación de Azure IoT Edge en un dispositivo simulado en Linux: versión preliminar][lnk-iotedge]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-powershell-install]: https://docs.microsoft.com/powershell/azure/install-azurerm-ps
[lnk-iothub-cmdlets]: https://docs.microsoft.com/powershell/module/azurerm.iothub/
[lnk-rest-api]: https://docs.microsoft.com/rest/api/iothub/iothubresource

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
