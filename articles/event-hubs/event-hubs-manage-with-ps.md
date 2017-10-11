---
title: Uso de PowerShell para administrar recursos de Azure Event Hubs | Microsoft Docs
description: "Usar el módulo de PowerShell para crear y administrar Event Hubs"
services: event-hubs
documentationcenter: .NET
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/15/2017
ms.author: sethm
ms.openlocfilehash: 2b49c01153b1104612e6ebf9c88566fc40d1f635
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/18/2017
---
# <a name="use-powershell-to-manage-event-hubs-resources"></a>Uso de PowerShell para administrar recursos de Event Hubs

Microsoft Azure PowerShell es un entorno de scripting que puede usar para controlar y automatizar la implementación y la administración de sus servicios de Azure. Este artículo describe cómo utilizar el [módulo de PowerShell de Resource Manager de Event Hubs](/powershell/module/azurerm.eventhub) para aprovisionar y administrar entidades de Event Hubs (espacios de nombres, Event Hubs individuales y grupos de consumidores) mediante una consola o script de Azure PowerShell.

También puede administrar los recursos de Event Hubs mediante plantillas de Azure Resource Manager. Para más información, consulte el artículo [Creación de un espacio de nombres de Event Hubs con un centro de eventos y un grupo de consumidores mediante una plantilla de Azure Resource Manager](event-hubs-resource-manager-namespace-event-hub.md).

## <a name="prerequisites"></a>Requisitos previos

Antes de comenzar, necesitará lo siguiente:

* Una suscripción de Azure. Para obtener más información sobre cómo obtener una suscripción, consulte [Opciones de compra][purchase options], [ofertas para miembros][member offers] o [cuenta gratuita][free account].
* Un equipo con Azure PowerShell. Para obtener instrucciones, consulte [Introducción a los cmdlets de Azure PowerShell](/powershell/azure/get-started-azureps).
* Conocimientos generales sobre los scripts de PowerShell, paquetes de NuGet y .NET Framework.

## <a name="get-started"></a>Primeros pasos

El primer paso es usar PowerShell para iniciar sesión en su cuenta de Azure y su suscripción de Azure. Siga las instrucciones descritas en [Introducción a los cmdlets de Azure PowerShell](/powershell/azure/get-started-azureps) para iniciar sesión en su cuenta de Azure y recuperar y tener acceso a los recursos de la suscripción.

## <a name="provision-an-event-hubs-namespace"></a>Aprovisionamiento de un espacio de nombres de Event Hubs

Al trabajar con espacios de nombres de Event Hubs, puede usar los cmdlets [Get-AzureRmEventHubNamespace](/powershell/module/azurerm.eventhub/get-azurermeventhubnamespace), [New-AzureRmEventHubNamespace](/powershell/module/azurerm.eventhub/new-azurermeventhubnamespace), [Remove-AzureRmEventHubNamespace](/powershell/module/azurerm.eventhub/remove-azurermeventhubnamespace) y [Set-AzureRmEventHubNamespace](/powershell/module/azurerm.eventhub/set-azurermeventhubnamespace).

En este ejemplo se crean algunas variables locales en el script: `$Namespace` y `$Location`.

* `$Namespace` es el nombre del espacio de nombres de Event Hubs con el que queremos trabajar.
* `$Location` identifica el centro de datos en el que aprovisionaremos el espacio de nombres.
* `$CurrentNamespace` almacena el espacio de nombres de referencia que vamos a recuperar (o crear).

En un script real, `$Namespace` y `$Location` se pueden pasar como parámetros.

Esta parte del script hace lo siguiente:

1. Intenta recuperar un espacio de nombres de Event Hubs con el nombre especificado.
2. Si se encuentra el espacio de nombres, informa de lo que ha encontrado.
3. Si no se encuentra el espacio de nombres, lo crea y luego recupera el espacio de nombres recién creado.

    ```powershell
    # Query to see if the namespace currently exists
    $CurrentNamespace = Get-AzureRMEventHubNamespace -ResourceGroupName $ResGrpName -NamespaceName $Namespace
   
    # Check if the namespace already exists or needs to be created
    if ($CurrentNamespace)
    {
        Write-Host "The namespace $Namespace already exists in the $Location region:"
        # Report what was found
        Get-AzureRMEventHubNamespace -ResourceGroupName $ResGrpName -NamespaceName $Namespace
    }
    else
    {
        Write-Host "The $Namespace namespace does not exist."
        Write-Host "Creating the $Namespace namespace in the $Location region..."
        New-AzureRmEventHubNamespace -ResourceGroupName $ResGrpName -NamespaceName $Namespace -Location $Location
        $CurrentNamespace = Get-AzureRMEventHubNamespace -ResourceGroupName $ResGrpName -NamespaceName $Namespace
        Write-Host "The $Namespace namespace in Resource Group $ResGrpName in the $Location region has been successfully created."
    }
    ```

## <a name="create-an-event-hub"></a>Creación de un centro de eventos

Para crear un centro de eventos, realice una comprobación de espacio de nombres mediante el script de la sección anterior. A continuación, use el cmdlet [New-AzureRmEventHub](/powershell/module/azurerm.eventhub/new-azurermeventhub) para crear el centro de eventos:

```powershell
# Check if event hub already exists
$CurrentEH = Get-AzureRMEventHub -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName

if($CurrentEH)
{
    Write-Host "The event hub $EventHubName already exists in the $Location region:"
    # Report what was found
    Get-AzureRmEventHub -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName
}
else
{
    Write-Host "The $EventHubName event hub does not exist."
    Write-Host "Creating the $EventHubName event hub in the $Location region..."
    New-AzureRmEventHub -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName -Location $Location -MessageRetentionInDays 3
    $CurrentEH = Get-AzureRmEventHub -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName
    Write-Host "The $EventHubName event hub in Resource Group $ResGrpName in the $Location region has been successfully created."
}
```

### <a name="create-a-consumer-group"></a>Creación de un grupo de consumidores

Para crear un grupo de consumidores dentro de un centro de eventos, realice las comprobaciones de espacio de nombres y centro de eventos mediante los scripts de la sección anterior. A continuación, use el cmdlet [New-AzureRmEventHubConsumerGroup](/powershell/module/azurerm.eventhub/new-azurermeventhubconsumergroup) para crear el grupo de consumidores en el centro de eventos. Por ejemplo:

```powershell
# Check if consumer group already exists
$CurrentCG = Get-AzureRmEventHubConsumerGroup -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName -ConsumerGroupName $ConsumerGroupName -ErrorAction Ignore

if($CurrentCG)
{
    Write-Host "The consumer group $ConsumerGroupName in event hub $EventHubName already exists in the $Location region:"
    # Report what was found
    Get-AzureRmEventHubConsumerGroup -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName
}
else
{
    Write-Host "The $ConsumerGroupName consumer group does not exist."
    Write-Host "Creating the $ConsumerGroupName consumer group in the $Location region..."
    New-AzureRmEventHubConsumerGroup -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName -ConsumerGroupName $ConsumerGroupName
    $CurrentCG = Get-AzureRmEventHubConsumerGroup -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName
    Write-Host "The $ConsumerGroupName consumer group in event hub $EventHubName in Resource Group $ResGrpName in the $Location region has been successfully created."
}
```

#### <a name="set-user-metadata"></a>Establecimiento de los metadatos del usuario

Después de ejecutar los scripts de las secciones anteriores, puede usar el cmdlet [Set-AzureRmEventHubConsumerGroup](/powershell/module/azurerm.eventhub/set-azurermeventhubconsumergroup) para actualizar las propiedades de un grupo de consumidores, como en el ejemplo siguiente:

```powershell
# Set some user metadata on the CG
Write-Host "Setting the UserMetadata field to 'Testing'"
Set-AzureRmEventHubConsumerGroup -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName -ConsumerGroupName $ConsumerGroupName -UserMetadata "Testing"
# Show result
Get-AzureRmEventHubConsumerGroup -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName -ConsumerGroupName $ConsumerGroupName
```

## <a name="remove-event-hub"></a>Eliminación de un centro de eventos

Para quitar las instancias de Event Hubs que creó, puede usar el cmdlet `Remove-*` como en el ejemplo siguiente:

```powershell
# Clean up
Remove-AzureRmEventHubConsumerGroup -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName -ConsumerGroupName $ConsumerGroupName
Remove-AzureRmEventHub -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName
Remove-AzureRmEventHubNamespace -ResourceGroupName $ResGrpName -NamespaceName $Namespace
```

## <a name="next-steps"></a>Pasos siguientes

- Consulte la documentación completa del módulo de PowerShell de Resource Manager de Event Hubs [aquí](/powershell/module/azurerm.eventhub). Esta página enumera todos los cmdlets disponibles.
- Para más información sobre el uso de plantillas de Azure Resource Manager, consulte el artículo [Creación de un espacio de nombres de Event Hubs con un centro de eventos y un grupo de consumidores mediante una plantilla de Azure Resource Manager](event-hubs-resource-manager-namespace-event-hub.md).
- Información sobre las [bibliotecas de administración de .NET de Event Hubs](event-hubs-management-libraries.md).

[purchase options]: http://azure.microsoft.com/pricing/purchase-options/
[member offers]: http://azure.microsoft.com/pricing/member-offers/
[free account]: http://azure.microsoft.com/pricing/free-trial/
