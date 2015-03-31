<properties
	pageTitle="Administración de Service Bus con PowerShell"
	description="Administración de Service Bus con scripts de PowerShell en lugar de .NET"
	services="service-bus"
	documentationCenter=".net"
	authors="sethmanheim"
	manager="timlt"
	editor=""/>

<tags
	ms.service="service-bus"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/26/2015"
	ms.author="sethm"/>

# Administración de Service Bus con PowerShell

## Información general

Microsoft Azure PowerShell es un eficaz entorno de scripting que puede usar para controlar y automatizar la implementación y la administración de sus cargas de trabajo en Azure. En este artículo, obtendrá información sobre cómo usar PowerShell para aprovisionar y administrar entidades de Service Bus, como espacios de nombres, colas y centros de eventos, mediante una consola local de Azure PowerShell.

## Requisitos previos

Antes de empezar este artículo, debe tener lo siguiente:

- Una suscripción de Azure. Azure es una plataforma basada en suscripción. Para obtener más
Para obtener información acerca de cómo obtener una suscripción, consulte [Opciones de compra][ azure-purchase-options],
[Ofertas de miembro][azure-member-offers] o [Evaluación gratuita de][azure-free-trial].

- Un equipo con Azure PowerShell. Para obtener más información, consulte [Instalación y configuración de Azure PowerShell][Powershell-install-configure].

- Conocimientos generales sobre los scripts de PowerShell, paquetes de NuGet y .NET Framework.

## Incluir una referencia al ensamblado .NET para Service Bus

Hay un número limitado de cmdlets de PowerShell disponibles para la administración de Service Bus. Para aprovisionar
las entidades que no se exponen a través de los cmdlets existentes, se hará uso del cliente .NET para
Service Bus al usar el [paquete NuGet de Service Bus][Service Bus NuGet package].

En primer lugar, queremos asegurarnos de que nuestro script puede encontrar el ensamblado **Microsoft.ServiceBus.dll**, que se instala con el paquete NuGet. Para ser flexible, el script llevará a cabo estos pasos:

1. El script determina la ruta de acceso en la que se invocó.
2. Recorre de forma recursiva la ruta de acceso hasta que encuentra una carpeta denominada `paquetes`. Esta es una carpeta que se crea al instalar los paquetes de NuGet.
3. Se busca de forma recursiva en la carpeta `paquetes` para un ensamblado denominado **Microsoft.ServiceBus.dll**.
4. Luego, hace referencia al ensamblado de modo que los tipos estarán disponibles para su uso posterior.

Aquí se explica cómo se implementan estos pasos en un script de PowerShell:

```powershell

try
{
    # WARNING: Make sure to reference the latest version of Microsoft.ServiceBus.dll
    Write-Output "Adding the [Microsoft.ServiceBus.dll] assembly to the script..."
    $scriptPath = Split-Path (Get-Variable MyInvocation -Scope 0).Value.MyCommand.Path
    $packagesFolder = (Split-Path $scriptPath -Parent) + "\packages"
    $assembly = Get-ChildItem $packagesFolder -Include "Microsoft.ServiceBus.dll" -Recurse
    Add-Type -Path $assembly.FullName

    Write-Output "The [Microsoft.ServiceBus.dll] assembly has been successfully added to the script."
}

catch [System.Exception]
{
    Write-Error("Could not add the Microsoft.ServiceBus.dll assembly to the script. Make sure you build the solution before running the provisioning script.")
}

```

## Aprovisionar un espacio de nombres de Service Bus

Al trabajar con espacios de nombres de Service Bus, puede usar dos cmdlets en lugar del SDK de .NET. En concreto, puede usar [Get-AzureSBNamespace][] y [New-AzureSBNamespace][].

En nuestro ejemplo, vamos a crear algunas variables locales en el script, `$Namespace`y `$Location`.
- `$Namespace`es el nombre del espacio de nombres de Service Bus con el que queremos trabajar.
- `$Location`identifica el centro de datos en el que aprovisionaremos el espacio de nombres.
- `$CurrentNamespace`almacena el espacio de nombres de referencia que vamos a recuperar (o crear).

En un script real, `$Namespace`y `$Location`se pueden pasar como parámetros.

Esta parte del script hará lo siguiente:

1. Intenta recuperar un espacio de nombres de Service Bus con el nombre proporcionado.
2. Si se encuentra el espacio de nombres, simplemente indica que se encontró.
3. Si no se encuentra el espacio de nombres, lo crea y luego recupera el espacio de nombres recién creado.

``` powershell

$Namespace = "MyServiceBusNS"
$Location = "West US"

# Query to see if the namespace currently exists
$CurrentNamespace = Get-AzureSBNamespace -Name $Namespace

# Check if the namespace already exists or needs to be created
if ($CurrentNamespace)
{
    Write-Output "The namespace [$Namespace] already exists in the [$($CurrentNamespace.Region)] region."
}
else
{
    Write-Host "The [$Namespace] namespace does not exist."
    Write-Output "Creating the [$Namespace] namespace in the [$Location] region..."
    New-AzureSBNamespace -Name $Namespace -Location $Location -CreateACSNamespace false -NamespaceType Messaging
    $CurrentNamespace = Get-AzureSBNamespace -Name $Namespace
    Write-Host "The [$Namespace] namespace in the [$Location] region has been successfully created."
}
```

Para aprovisionar otras entidades de Service Bus, debemos crear una instancia del objeto `NamespaceManager` a partir del SDK.
Podemos usar el cmdlet [AzureSBAuthorizationRule Get][Get-AzureSBAuthorizationRule] para recuperar una regla de autorización que se usa para proporcionar una cadena de conexión. Almacenaremos una referencia a la instancia de  `NamespaceManager` en la variable `$NamespaceManager`. Usaremos `$NamespaceManager`más adelante en el script para aprovisionar otras entidades.

``` powershell
$sbr = Get-AzureSBAuthorizationRule -Namespace $Namespace
# Create the NamespaceManager object to create the event hub
Write-Output "Creating a NamespaceManager object for the [$Namespace] namespace..."
$NamespaceManager = [Microsoft.ServiceBus.NamespaceManager]::CreateFromConnectionString($sbr.ConnectionString);
Write-Output "NamespaceManager object for the [$Namespace] namespace has been successfully created."
```

## Aprovisionamiento de otras entidades de Service Bus

Para aprovisionar otras entidades, tales como colas, temas y centros de eventos, podemos usar la API [.NET para Service Bus][dotnet-servicebus-api]. En este artículo, nos centraremos solo en centros de eventos, pero los pasos para otras entidades son similares. Además, al final de este artículo se hace referencia a ejemplos más detallados, incluidas otras entidades.

En esta parte del script, creamos cuatro variables locales adicionales. Estas variables se usarán para crear una instancia de `EventHubDescription`. Haremos lo siguiente:

1. Mediante el objeto `NamespaceManager`, comprobaremos en primer lugar si existe el centro de eventos que se identifica mediante `$Path`.
2. Si no existe, creamos un `EventHubDescription` y lo pasamos al método  `NamespaceManager`class `CreateEventHubIfNotExists`.
3. Una vez que sepamos que el centro de eventos está disponible, creamos un grupo de consumidores mediante `ConsumerGroupDescription` y `NamespaceManager`.

``` powershell

$Path  = "MyEventHub"
$PartitionCount = 12
$MessageRetentionInDays = 7
$UserMetadata = $null
$ConsumerGroupName = "MyConsumerGroup"

# Check if the event hub already exists
if ($NamespaceManager.EventHubExists($Path))
{
    Write-Output "The [$Path] event hub already exists in the [$Namespace] namespace."  
}
else
{
    Write-Output "Creating the [$Path] event hub in the [$Namespace] namespace: PartitionCount=[$PartitionCount] MessageRetentionInDays=[$MessageRetentionInDays]..."
    $EventHubDescription = New-Object -TypeName Microsoft.ServiceBus.Messaging.EventHubDescription -ArgumentList $Path
    $EventHubDescription.PartitionCount = $PartitionCount
    $EventHubDescription.MessageRetentionInDays = $MessageRetentionInDays
    $EventHubDescription.UserMetadata = $UserMetadata
    $EventHubDescription.Path = $Path
    $NamespaceManager.CreateEventHubIfNotExists($EventHubDescription);
    Write-Output "The [$Path] event hub in the [$Namespace] namespace has been successfully created."
}

# Create the consumer group if it doesn't exist
Write-Output "Creating the consumer group [$ConsumerGroupName] for the [$Path] event hub..."
$ConsumerGroupDescription = New-Object -TypeName Microsoft.ServiceBus.Messaging.ConsumerGroupDescription -ArgumentList $Path, $ConsumerGroupName
$ConsumerGroupDescription.UserMetadata = $ConsumerGroupUserMetadata
$NamespaceManager.CreateConsumerGroupIfNotExists($ConsumerGroupDescription);
Write-Output "The consumer group [$ConsumerGroupName] for the [$Path] event hub has been successfully created."
```

## Pasos siguientes

En este artículo se proporciona una descripción básica para el aprovisionamiento de entidades de Service Bus mediante PowerShell. Todo lo que puede hacer con las bibliotecas de cliente .NET puede hacer en un script de PowerShell.

Hay ejemplos más detallados en los siguientes artículos de blog:

- [Cómo crear colas, temas y suscripciones de Service Bus con un script de PowerShell](http://blogs.msdn.com/b/paolos/archive/2014/12/02/how-to-create-a-service-bus-queues-topics-and-subscriptions-using-a-powershell-script.aspx)
- [Cómo crear un espacio de nombres de Service Bus y un centro de eventos mediante un script de PowerShell](http://blogs.msdn.com/b/paolos/archive/2014/12/01/how-to-create-a-service-bus-namespace-and-an-event-hub-using-a-powershell-script.aspx)

Además, puede descargar algunos scripts listos para usar:
- [Scripts de PowerShell de Service Bus](https://code.msdn.microsoft.com/windowsazure/Service-Bus-PowerShell-a46b7059)

<!--Link references-->
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[powershell-install-configure]: ../install-configure-powershell/
[Service Bus NuGet package]: http://www.nuget.org/packages/WindowsAzure.ServiceBus/
[Get-AzureSBNamespace]: https://msdn.microsoft.com/library/azure/dn495122.aspx
[New-AzureSBNamespace]: https://msdn.microsoft.com/library/azure/dn495165.aspx
[Get-AzureSBAuthorizationRule]: https://msdn.microsoft.com/library/azure/dn495113.aspx
[dotnet-servicebus-api]: https://msdn.microsoft.com/library/microsoft.servicebus.aspx

<!--HONumber=47-->
