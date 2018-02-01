---
title: "Clonación de aplicaciones web con PowerShell"
description: Aprenda a clonar sus instancias de Web Apps en nuevas aplicaciones web con PowerShell.
services: app-service\web
documentationcenter: 
author: ahmedelnably
manager: stefsch
editor: 
ms.assetid: f9a5cfa1-fbb0-41e6-95d1-75d457347a35
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/14/2016
ms.author: aelnably
ms.openlocfilehash: 30817a1a6a8079e7a896305ab0b59e48fad4d644
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/18/2018
---
# <a name="azure-app-service-app-cloning-using-powershell"></a>Clonación de aplicaciones de Azure App Service mediante PowerShell
Con el lanzamiento de Microsoft Azure PowerShell versión 1.1.0, se ha agregado una nueva opción a `New-AzureRMWebApp` que permite clonar una aplicación web existente en una aplicación recién creada de una región diferente o de la misma. Esta opción permitirá que los clientes implementen varias aplicaciones en diferentes regiones de una forma rápida y sencilla.

La clonación de aplicaciones actualmente solo se admite para los planes de Servicio de aplicaciones de nivel Premium. La nueva característica cuenta con las mismas limitaciones que la de copia de seguridad de Web Apps; consulte [Hacer copia de seguridad de una aplicación web en Azure App Service](web-sites-backup.md).

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="cloning-an-existing-app"></a>Clonación de una aplicación existente
Escenario: Una aplicación web existente de la región Centro y Sur de EE. UU.; el usuario desea clonar el contenido en una nueva aplicación web en la región Centro y norte de EE. UU. Puede realizarse mediante la versión Azure Resource Manager del cmdlet de PowerShell para crear una nueva aplicación web con la opción `-SourceWebApp`.

Conociendo el nombre del grupo de recursos que contiene la aplicación web de origen, puede usar el siguiente comando de PowerShell para obtener la información de la aplicación web de origen (en este caso, llamada "`source-webapp`"):

```PowerShell
$srcapp = Get-AzureRmWebApp -ResourceGroupName SourceAzureResourceGroup -Name source-webapp
```

Para crear un nuevo plan de App Service, puede usar el comando `New-AzureRmAppServicePlan` como en el ejemplo siguiente:

```PowerShell
New-AzureRmAppServicePlan -Location "South Central US" -ResourceGroupName DestinationAzureResourceGroup -Name NewAppServicePlan -Tier Premium
```

Mediante el comando `New-AzureRmWebApp`, puede crear la nueva aplicación web en la región Centro y norte de EE. UU. y asociarla a un plan de App Service de nivel Premium. Además, puede usar el mismo grupo de recursos que la aplicación web de origen, o bien definir un nuevo grupo de recursos, como se muestra en el siguiente comando:

```PowerShell
$destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp
```

Para clonar una aplicación web existente, incluidas todas las ranuras de implementación asociados, debe usar el parámetro `IncludeSourceWebAppSlots`. El siguiente comando de PowerShell muestra el uso de ese parámetro con el comando `New-AzureRmWebApp`:

```PowerShell
$destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -IncludeSourceWebAppSlots
```

Para clonar una aplicación web existente en la misma región, deberá crear un nuevo grupo de recursos y un nuevo plan de App Service en la misma región y después usar el siguiente comando de PowerShell para clonar la aplicación web:

```PowerShell
$destapp = New-AzureRmWebApp -ResourceGroupName NewAzureResourceGroup -Name dest-webapp -Location "South Central US" -AppServicePlan NewAppServicePlan -SourceWebApp $srcap
```

## <a name="cloning-an-existing-app-to-an-app-service-environment"></a>Clonación de una aplicación existente en un entorno de App Service
Escenario: Una aplicación web existente en la región Centro y Sur de EE. UU.; el usuario desea clonar el contenido en una nueva aplicación web en un entorno de App Service (ASE) existente.

Conociendo el nombre del grupo de recursos que contiene la aplicación web de origen, puede usar el siguiente comando de PowerShell para obtener la información de la aplicación web de origen (en este caso, llamada "`source-webapp`"):

```PowerShell
$srcapp = Get-AzureRmWebApp -ResourceGroupName SourceAzureResourceGroup -Name source-webapp
```

Conociendo el nombre del ASE y el nombre del grupo de recursos al que este pertenece, puede crear la nueva aplicación web en el ASE existente, como se muestra en el siguiente comando:

```PowerShell
$destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -ASEName DestinationASE -ASEResourceGroupName DestinationASEResourceGroupName -SourceWebApp $srcapp
```

El parámetro `Location` es necesario por el motivo anterior, pero se omite cuando se crea la aplicación en un ASE. 

## <a name="cloning-an-existing-app-slot"></a>Clonación de una ranura de aplicación existente
Escenario: El usuario desea clonar una ranura de aplicación web existente en una nueva aplicación web o una nueva ranura de aplicación web. La nueva aplicación web puede estar en la misma región que la ranura de aplicación web original o en una distinta.

Conociendo el nombre del grupo de recursos que contiene la aplicación web de origen, podemos usar el siguiente comando de PowerShell para obtener la información de la ranura de la aplicación web de origen (en este caso, llamada "`source-webappslot`") vinculada a la aplicación web `source-webapp`:

```PowerShell
$srcappslot = Get-AzureRmWebAppSlot -ResourceGroupName SourceAzureResourceGroup -Name source-webapp -Slot source-webappslot
```

El siguiente comando muestra la creación de un clon de la aplicación web de origen en una nueva aplicación web:

```PowerShell
$destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcappslot
```

## <a name="configuring-traffic-manager-while-cloning-an-app"></a>Configuración de Traffic Manager durante la clonación de una aplicación
Crear aplicaciones web de varias regiones y configurar Azure Traffic Manager para enrutar el tráfico a todas estas aplicaciones web es un escenario importante para asegurarse de que las aplicaciones de los clientes tengan una alta disponibilidad. Al clonar una aplicación web existente, tiene la opción de conectar las aplicaciones web a un nuevo perfil de Traffic Manager o a uno existente. Solo se admite la versión de Traffic Manager de Azure Resource Manager.

### <a name="creating-a-new-traffic-manager-profile-while-cloning-an-app"></a>Creación de un nuevo perfil de Traffic Manager durante la clonación de una aplicación
Escenario: Desea clonar una aplicación web en otra región, al mismo tiempo que configura un perfil de Traffic Manager de Azure Resource Manager que incluya ambas aplicaciones web. El siguiente comando demuestra la creación de un clon de la aplicación web de origen en una nueva aplicación web al tiempo que se configura un nuevo perfil de Traffic Manager:

```PowerShell
$destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "South Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -TrafficManagerProfileName newTrafficManagerProfile
```

### <a name="adding-new-cloned-web-app-to-an-existing-traffic-manager-profile"></a>Incorporación de una nueva aplicación web clonada a un perfil del Administrador de tráfico existente
Escenario: Ya tiene un perfil de Traffic Manager de Azure Resource Manager al que quiere agregar tanto aplicaciones web como puntos de conexión. Para ello, primero debe ensamblar el identificador de perfil existente de Traffic Manager. Necesita el identificador de suscripción, el nombre del grupo de recursos y el nombre del perfil existente de Traffic Manager.

```PowerShell
$TMProfileID = "/subscriptions/<Your subscription ID goes here>/resourceGroups/<Your resource group name goes here>/providers/Microsoft.TrafficManagerProfiles/ExistingTrafficManagerProfileName"
```

Después de obtener el identificador de Traffic Manager, el siguiente comando muestra cómo crear un clon de la aplicación web de origen en una nueva aplicación web al mismo tiempo que se agregan ambas a un perfil de Traffic Manager existente:

```PowerShell
$destapp = New-AzureRmWebApp -ResourceGroupName <Resource group name> -Name dest-webapp -Location "South Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -TrafficManagerProfileId $TMProfileID
```

## <a name="current-restrictions"></a>Restricciones actuales
Esta característica está actualmente en versión preliminar; con el tiempo, se agregarán nuevas funcionalidades. Estas son las restricciones conocidas de la versión actual de la clonación de aplicaciones:

* No se clona la configuración de escalado automático.
* No se clona la configuración de programación de copia de seguridad.
* No se clona la configuración de red virtual.
* No se instala automáticamente App Insights en la aplicación web de destino.
* No se clona la configuración de Easy Auth.
* No se clona la extensión Kudu.
* No se clonan las reglas de TiP.
* No se clona el contenido de la base de datos.
* Las direcciones IP de salida cambiarán si la clonación se realiza en una unidad de escalado distinta.

### <a name="references"></a>Referencias
* [Clonación de aplicaciones web](app-service-web-app-cloning.md)
* [Hacer copia de seguridad de una aplicación web en Azure App Service](web-sites-backup.md)
* [Compatibilidad de Azure Resource Manager con Traffic Manager (versión preliminar)](../traffic-manager/traffic-manager-powershell-arm.md)
* [Introducción al entorno de App Service](environment/intro.md)
* [Uso de Azure PowerShell con Azure Resource Manager](../azure-resource-manager/powershell-azure-resource-manager.md)

