---
title: "Administración de Service Bus de Azure con PowerShell | Microsoft Docs"
description: "Administración de Bus de servicio con scripts de PowerShell"
services: service-bus-messaging
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: aff8e2ce-bc8b-489f-aca9-a18782be0375
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/12/2017
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: 61f31c8ad0463776937f366d145595f04cc42d2e
ms.openlocfilehash: 24dd8757942488aa8364cc6cf968cfc17299699f


---
# <a name="manage-service-bus-with-powershell"></a>Administración de Service Bus con PowerShell
## <a name="overview"></a>Información general
Microsoft Azure PowerShell es un eficaz entorno de scripting que puede usar para controlar y automatizar la implementación y la administración de sus cargas de trabajo en Azure. En este artículo se describe cómo usar PowerShell para aprovisionar y administrar entidades de Service Bus, como espacios de nombres, colas y centros de eventos, mediante una consola local de Azure PowerShell.

## <a name="prerequisites"></a>Requisitos previos
Antes de comenzar este tutorial, debe cumplir los siguientes requisitos previos:

* Una suscripción de Azure. Azure es una plataforma basada en suscripción. Para obtener más información sobre cómo obtener una suscripción, consulte [Opciones de compra][Purchase Options], [Ofertas para miembros][Member Offers] o [Evaluación gratuita][Free Trial].
* Un equipo con Azure PowerShell. Para obtener más información, consulte [Instalación y configuración de Azure PowerShell][Install and configure Azure PowerShell].
* Conocimientos generales sobre los scripts de PowerShell, paquetes de NuGet y .NET Framework.

## <a name="including-a-reference-to-the-net-assembly-for-service-bus"></a>Incluir una referencia al ensamblado .NET para Service Bus
Hay un número limitado de cmdlets de PowerShell disponibles para la administración de Service Bus. Para aprovisionar las entidades que no se exponen a través de los cmdlets existentes, puede usar el cliente de .NET para Service Bus en el [paquete NuGet de Service Bus][Service Bus NuGet package].

En primer lugar, asegúrese de que el script puede encontrar el ensamblado **Microsoft.ServiceBus.dll** , que se instala con el paquete NuGet. Para ser flexible, el script llevará a cabo estos pasos:

1. Determina la ruta de acceso en la que se invocó.
2. Atraviesa la ruta de acceso hasta que encuentra una carpeta denominada `packages`. Esta carpeta se crea al instalar paquetes de NuGet.
3. Se busca de forma recursiva en la carpeta `packages` un ensamblado denominado **Microsoft.ServiceBus.dll**.
4. Hace referencia al ensamblado de modo que los tipos estarán disponibles para su uso posterior.

Aquí se explica cómo se implementan estos pasos en un script de PowerShell:

```powershell
try
{
    # WARNING: Make sure to reference the latest version of Microsoft.ServiceBus.dll
    Write-Host "Adding the [Microsoft.ServiceBus.dll] assembly to the script..."
    $scriptPath = Split-Path (Get-Variable MyInvocation -Scope 0).Value.MyCommand.Path
    $packagesFolder = (Split-Path $scriptPath -Parent) + "\packages"
    $assembly = Get-ChildItem $packagesFolder -Include "Microsoft.ServiceBus.dll" -Recurse
    Add-Type -Path $assembly.FullName

    Write-Host "The [Microsoft.ServiceBus.dll] assembly has been successfully added to the script."
}

catch [System.Exception]
{
    Write-Error "Could not add the Microsoft.ServiceBus.dll assembly to the script. Make sure you build the solution before running the provisioning script."
}
```

## <a name="provision-a-service-bus-namespace"></a>Aprovisionar un espacio de nombres de Service Bus
Dos de los cmdlets de PowerShell admiten operaciones de espacio de nombres del Bus de servicio. En lugar de las API .NET SDK, puede usar [Get-AzureSBNamespace][Get-AzureSBNamespace] y New-[AzureSBNamespace][New-AzureSBNamespace].

En este ejemplo se crean algunas variables locales en el script: `$Namespace` y `$Location`.

* `$Namespace` es el nombre del espacio de nombres de Service Bus con el que queremos trabajar.
* `$Location` identifica el centro de datos en el que el script aprovisiona el espacio de nombres.
* `$CurrentNamespace` almacena el espacio de nombres de referencia que el script recupera (o crea).

En un script real, `$Namespace` y `$Location` se pueden pasar como parámetros.

Esta parte del script realiza las siguientes tareas:

1. Intenta recuperar un espacio de nombres de Service Bus con el nombre proporcionado.
2. Si se encuentra el espacio de nombres, informa de lo que ha encontrado.
3. Si no se encuentra el espacio de nombres, lo crea y luego recupera el espacio de nombres recién creado.
   
    ```powershell
    $Namespace = "MyServiceBusNS"
    $Location = "West US"
   
    # Query to see if the namespace currently exists
    $CurrentNamespace = Get-AzureSBNamespace -Name $Namespace
   
    # Check if the namespace already exists or needs to be created
    if ($CurrentNamespace)
    {
        Write-Host "The namespace [$Namespace] already exists in the [$($CurrentNamespace.Region)] region."
    }
    else
    {
        Write-Host "The [$Namespace] namespace does not exist."
        Write-Host "Creating the [$Namespace] namespace in the [$Location] region..."
        New-AzureSBNamespace -Name $Namespace -Location $Location -CreateACSNamespace $false -NamespaceType Messaging
        $CurrentNamespace = Get-AzureSBNamespace -Name $Namespace
        Write-Host "The [$Namespace] namespace in the [$Location] region has been successfully created."
    }
    ```

Para aprovisionar otras entidades de Service Bus, cree una instancia de la clase [NamespaceManager][NamespaceManager] a partir del SDK.
Puede usar el cmdlet [Get-AzureSBAuthorizationRule][Get-AzureSBAuthorizationRule] para recuperar una regla de autorización que se usa para proporcionar una cadena de conexión. Almacenaremos una referencia a la instancia de `NamespaceManager` en la variable `$NamespaceManager`. Usaremos `$NamespaceManager` más adelante en el script para aprovisionar otras entidades.

```powershell
$sbr = Get-AzureSBAuthorizationRule -Namespace $Namespace
# Create the NamespaceManager object to create the event hub
Write-Host "Creating a NamespaceManager object for the [$Namespace] namespace..."
$NamespaceManager = [Microsoft.ServiceBus.NamespaceManager]::CreateFromConnectionString($sbr.ConnectionString);
Write-Host "NamespaceManager object for the [$Namespace] namespace has been successfully created."
```

## <a name="provisioning-other-service-bus-entities"></a>Aprovisionamiento de otras entidades de Service Bus
Para aprovisionar otras entidades, como colas, temas y centros de eventos, podemos usar la [API de .NET para Service Bus][API de .NET para Service Bus]. En este artículo se tratan solo los centros de eventos, pero los pasos para otras entidades son similares. Además, al final de este artículo se hace referencia a ejemplos más detallados, incluidas otras entidades.

En esta parte del script se crean cuatro variables locales adicionales. Estas variables se usan para crear una instancia de un objeto `EventHubDescription` . Este script realiza las siguientes tareas:

1. Mediante el objeto `NamespaceManager`, compruebe si existe el centro de eventos que se identifica mediante `$Path`.
2. Si no existe, creamos un `EventHubDescription` y lo pasamos al método `CreateEventHubIfNotExists` de la clase `NamespaceManager`.
3. Cuando sepamos que el centro de eventos está disponible, cree un grupo de consumidores mediante `ConsumerGroupDescription` y `NamespaceManager`.
   
    ```powershell
    $Path  = "MyEventHub"
    $PartitionCount = 12
    $MessageRetentionInDays = 7
    $UserMetadata = $null
    $ConsumerGroupName = "MyConsumerGroup"
   
    # Check to see if the Event Hub already exists
    if ($NamespaceManager.EventHubExists($Path))
    {
        Write-Host "The [$Path] event hub already exists in the [$Namespace] namespace."  
    }
    else
    {
        Write-Host "Creating the [$Path] event hub in the [$Namespace] namespace: PartitionCount=[$PartitionCount] MessageRetentionInDays=[$MessageRetentionInDays]..."
        $EventHubDescription = New-Object -TypeName Microsoft.ServiceBus.Messaging.EventHubDescription -ArgumentList $Path
        $EventHubDescription.PartitionCount = $PartitionCount
        $EventHubDescription.MessageRetentionInDays = $MessageRetentionInDays
        $EventHubDescription.UserMetadata = $UserMetadata
        $EventHubDescription.Path = $Path
        $NamespaceManager.CreateEventHubIfNotExists($EventHubDescription)
        Write-Host "The [$Path] event hub in the [$Namespace] namespace has been successfully created."
    }
   
    # Create the consumer group if it doesn't exist
    Write-Host "Creating the consumer group [$ConsumerGroupName] for the [$Path] event hub..."
    $ConsumerGroupDescription = New-Object -TypeName Microsoft.ServiceBus.Messaging.ConsumerGroupDescription -ArgumentList $Path, $ConsumerGroupName
    $ConsumerGroupDescription.UserMetadata = $ConsumerGroupUserMetadata
    $NamespaceManager.CreateConsumerGroupIfNotExists($ConsumerGroupDescription)
    Write-Host "The consumer group [$ConsumerGroupName] for the [$Path] event hub has been successfully created."
    ```

## <a name="migrate-a-namespace-to-another-azure-subscription"></a>Migrar un espacio de nombres a otra suscripción de Azure
La siguiente secuencia de comandos mueve un espacio de nombres de una suscripción de Azure a otra. Para ejecutar esta operación, el espacio de nombres ya debe estar activo y el usuario que ejecuta los comandos de PowerShell debe ser administrador en las suscripciones de origen y destino.

```powershell
# Create a new resource group in target subscription
Select-AzureRmSubscription -SubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff'
New-AzureRmResourceGroup -Name 'targetRG' -Location 'East US'

# Move namespace from source subscription to target subscription
Select-AzureRmSubscription -SubscriptionId 'aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa'
$res = Find-AzureRmResource -ResourceNameContains mynamespace -ResourceType 'Microsoft.ServiceBus/namespaces'
Move-AzureRmResource -DestinationResourceGroupName 'targetRG' -DestinationSubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff' -ResourceId $res.ResourceId
```

## <a name="next-steps"></a>Pasos siguientes
En este artículo se proporciona una descripción básica para el aprovisionamiento de entidades del Bus de servicio mediante PowerShell. Todo lo que puede hacer con las bibliotecas de cliente .NET puede hacer en un script de PowerShell.

Hay ejemplos más detallados disponibles en estas publicaciones de blog:

* [Cómo crear colas, temas y suscripciones de Service Bus con un script de PowerShell](http://blogs.msdn.com/b/paolos/archive/2014/12/02/how-to-create-a-service-bus-queues-topics-and-subscriptions-using-a-powershell-script.aspx)
* [Cómo crear un espacio de nombres de Service Bus y un centro de eventos mediante un script de PowerShell](http://blogs.msdn.com/b/paolos/archive/2014/12/01/how-to-create-a-service-bus-namespace-and-an-event-hub-using-a-powershell-script.aspx)

Además, puede descargar algunos scripts listos para usar:

* [Scripts de PowerShell de Service Bus](https://code.msdn.microsoft.com/Service-Bus-PowerShell-a46b7059)

<!--Link references-->
[Purchase Options]: http://azure.microsoft.com/pricing/purchase-options/
[Member Offers]: http://azure.microsoft.com/pricing/member-offers/
[Free Trial]: http://azure.microsoft.com/pricing/free-trial/
[Install and configure Azure PowerShell]: /powershell/azureps-cmdlets-docs
[Service Bus NuGet package]: http://www.nuget.org/packages/WindowsAzure.ServiceBus/
[Get-AzureSBNamespace]: https://docs.microsoft.com/powershell/servicemanagement/azure.compute/v1.6.1/Get-AzureSBNamespace
[New-AzureSBNamespace]: https://docs.microsoft.com/powershell/servicemanagement/azure.compute/v1.6.1/new-azuresbnamespace
[Get-AzureSBAuthorizationRule]: https://docs.microsoft.com/powershell/servicemanagement/azure.compute/v1.6.1/get-azuresbauthorizationrule
[NamespaceManager]: https://docs.microsoft.com/dotnet/api/microsoft.servicebus.namespacemanager



<!--HONumber=Jan17_HO2-->


