---
title: "Creación de una instancia de Azure Network Watcher | Microsoft Docs"
description: "En esta página se proporcionan los pasos necesarios para crear una instancia de Network Watcher mediante el portal y la API de REST de Azure"
services: network-watcher
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
ms.assetid: b1314119-0b87-4f4d-b44c-2c4d0547fb76
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 6e0ad6b5bec11c5197dd7bded64168a1b8cc2fdd
ms.openlocfilehash: a39ce143796408f8e44b0d2c877e631e92473462
ms.lasthandoff: 03/28/2017

---

# <a name="create-an-azure-network-watcher-instance"></a>Creación de una instancia de Azure Network Watcher

Network Watcher es un servicio regional que permite supervisar y diagnosticar problemas en un nivel de escenario de red mediante Azure. La supervisión del nivel de escenario permite diagnosticar problemas en una vista de nivel de red de un extremo a otro. Las herramientas de visualización y diagnóstico de red que incluye Network Watcher le ayudan a conocer, diagnosticar y obtener información acerca de cualquier red de Azure.

[!INCLUDE [network-watcher-preview](../../includes/network-watcher-public-preview-notice.md)]

## <a name="register-the-preview-capability"></a>Registro de la funcionalidad de versión preliminar

Actualmente, Network Watcher se encuentra en versión preliminar y, para usar las características de Network Watcher, debe registrarse. Para ello, ejecute el siguiente ejemplo de PowerShell:

```powershell
Register-AzureRmProviderFeature -FeatureName AllowNetworkWatcher -ProviderNamespace Microsoft.Network
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
```

Para comprobar que el registro se realizó correctamente, ejecute el siguiente ejemplo de PowerShell:

```powershell
Get-AzureRmProviderFeature -FeatureName AllowNetworkWatcher -ProviderNamespace  Microsoft.Network
```

Si la característica se registró correctamente, la salida debería coincidir con lo siguiente:

```
FeatureName         ProviderName      RegistrationState
-----------         ------------      -----------------
AllowNetworkWatcher Microsoft.Network Registered
```

### <a name="instructions-for-cli-10"></a>Instrucciones para la CLI 1.0

Para registrarla

```CLI
azure feature register  Microsoft.Network AllowNetworkWatcher
azure provider register Microsoft.Network
```

Para comprobar que el registro se realizó correctamente, ejecute el siguiente comando de la CLI:

```CLI
azure feature show Microsoft.Network AllowNetworkWatcher
```

Si la característica se registró correctamente, la salida debería coincidir con lo siguiente:

```CLI
info:    Executing command feature show
data:    Feature Name:       AllowNetworkWatcher
data:    Provider Name:      Microsoft.Network
data:    Registration State: Registered
info:    feature show command OK
```

> [!NOTE]
> Debido a que, por el momento, Network Watcher solo es compatible con CLI 1.0, las instrucciones para crear una nueva instancia de Network Watcher son las mismas que para CLI 1.0. Para registrar el proveedor con CLI 2.0, utilice `az feature register -n AllowNetworkWatcher --namespace Microsoft.Network`.

## <a name="create-a-network-watcher-in-the-portal"></a>Creación de una instancia de Network Watcher en el portal

Navegue a **Más servicios** > **Redes** > **Network Watcher**. Puede seleccionar todas las suscripciones para las que desee habilitar Network Watcher. Esta acción crea una instancia de Network Watcher en todas las regiones que estén disponibles.

![crear una instancia de network watcher][1]

## <a name="create-a-network-watcher-with-powershell"></a>Creación de una instancia de Network Watcher con PowerShell

Para crear una instancia de Network Watcher, ejecute el siguiente ejemplo:

```powershell
New-AzureRmNetworkWatcher -Name "NetworkWatcher_westcentralus" -ResourceGroupName "NetworkWatcherRG" -Location "West Central US"
```

## <a name="create-a-network-watcher-with-the-rest-api"></a>Creación de una instancia de Network Watcher con la API de REST

ARMclient se usa para llamar a la API de REST con PowerShell. ARMClient se encuentra en Chocolatey en [ARMClient en Chocolatey](https://chocolatey.org/packages/ARMClient)

### <a name="log-in-with-armclient"></a>Inicio de sesión con ARMClient

```powerShell
armclient login
```

### <a name="create-the-network-watcher"></a>Creación de Network Watcher

```powershell
$subscriptionId = '<subscription id>'
$networkWatcherName = '<name of network watcher>'
$resourceGroupName = '<resource group name>'
$apiversion = "2016-09-01"
$requestBody = @"
{
'location': 'West Central US'
}
"@

armclient put "https://management.azure.com/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}?api-version=${api-version}" $requestBody
```

## <a name="next-steps"></a>Pasos siguientes

Ahora que tiene una instancia de Network Watcher, obtenga información acerca de las características disponibles:

* [Topología](network-watcher-topology-overview.md)
* [Captura de paquetes](network-watcher-packet-capture-overview.md)
* [Comprobación de flujo de IP](network-watcher-ip-flow-verify-overview.md)
* [Próximo salto](network-watcher-next-hop-overview.md)
* [Vista de grupo de seguridad](network-watcher-security-group-view-overview.md)
* [Registro de flujo de NSG](network-watcher-nsg-flow-logging-overview.md)
* [Solución de problemas de una puerta de enlace de Virtual Network](network-watcher-troubleshoot-overview.md)

Una vez creada una instancia de Network Watcher, para configurar la captura de paquetes vea el artículo [Create an alert triggered packet capture](network-watcher-alert-triggered-packet-capture.md) (Crear una captura de paquetes desencadenada por alertas).

[1]: ./media/network-watcher-create/figure1.png












