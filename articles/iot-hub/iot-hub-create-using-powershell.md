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
ms.date: 03/23/2017
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 5c6ab3a8fdacb43546801518b76a15314d0b4b0f
ms.lasthandoff: 03/21/2017


---
# <a name="create-an-iot-hub-using-the-new-azurermiothub-cmdlet"></a>Creación de una instancia de IoT Hub mediante el cmdlet New-AzureRmIotHub

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## <a name="introduction"></a>Introducción

Puede usar cmdlets de Azure PowerShell para crear y administrar instancias de Azure IoT Hub. En este tutorial se muestra cómo crear una instancia de IoT Hub con PowerShell.

> [!NOTE]
> Azure tiene dos modelos de implementación diferentes para crear y trabajar con recursos: [Azure Resource Manager y clásico](../azure-resource-manager/resource-manager-deployment-model.md).  Este artículo trata sobre el uso del modelo de implementación de Azure Resource Manager.

Para completar este tutorial, necesitará lo siguiente:

* Una cuenta de Azure activa. <br/>Si no tiene ninguna, puede crear una [cuenta gratuita][lnk-free-trial] en tan solo unos minutos.
* [Azure PowerShell 1.0][lnk-powershell-install] o posterior.
* [Cmdlets de Azure Resource Manager][lnk-rm-install].

## <a name="connect-to-your-azure-subscription"></a>Conexión a su suscripción de Azure
En un símbolo del sistema de PowerShell, escriba el siguiente comando para iniciar sesión en su suscripción de Azure:

```powershell
Login-AzureRmAccount
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

> [!NOTE]
> El nombre de la instancia de IoT Hub debe ser único.

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

Para explorar aún más las funcionalidades de Centro de IoT, consulte:

* [Simulación de un dispositivo con el SDK de puerta de enlace de IoT][lnk-gateway]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-powershell-install]: /powershell/azureps-cmdlets-docs
[lnk-iothub-cmdlets]: /powershell/resourcemanager/azurerm.iothub/v1.3.0/azurerm.iothub
[lnk-rm-install]: /powershell/resourcemanager/
[lnk-rest-api]: https://msdn.microsoft.com/library/mt589014.aspx

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md

