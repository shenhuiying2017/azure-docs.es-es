---
title: Uso de PowerShell para administrar recursos de Azure Service Bus | Microsoft Docs
description: "Use el módulo de PowerShell para crear y administrar recursos de Service Bus"
services: service-bus-messaging
documentationcenter: .NET
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/21/2017
ms.author: sethm
ms.openlocfilehash: 4fbc6e18565ec14a3ccb4499b24804f681026023
ms.sourcegitcommit: 6f33adc568931edf91bfa96abbccf3719aa32041
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/22/2017
---
# <a name="use-powershell-to-manage-service-bus-resources"></a>Use PowerShell para administrar recursos de Service Bus

Microsoft Azure PowerShell es un entorno de scripting que puede usar para controlar y automatizar la implementación y la administración de sus servicios de Azure. En este artículo se describe cómo utilizar el [módulo de PowerShell de Resource Manager de Service Bus](/powershell/module/azurerm.servicebus) para aprovisionar y administrar entidades de Service Bus (espacios de nombres, colas, temas y suscripciones) mediante una consola o un script de Azure PowerShell.

Las entidades de Service Bus también se pueden administrar mediante plantillas de Azure Resource Manager. Para más información, consulte el artículo [Cómo crear recursos de Service Bus mediante plantillas de Azure Resource Manager](service-bus-resource-manager-overview.md).

## <a name="prerequisites"></a>requisitos previos

Antes de empezar, tendrá que cumplir los siguientes requisitos previos:

* Una suscripción de Azure. Para obtener más información sobre cómo obtener una suscripción, consulte [Opciones de compra][purchase options], [ofertas para miembros][member offers] o [cuenta gratuita][free account].
* Un equipo con Azure PowerShell. Para obtener instrucciones, consulte [Introducción a los cmdlets de Azure PowerShell](/powershell/azure/get-started-azureps).
* Conocimientos generales sobre los scripts de PowerShell, paquetes de NuGet y .NET Framework.

## <a name="get-started"></a>Introducción

El primer paso es usar PowerShell para iniciar sesión en su cuenta de Azure y su suscripción de Azure. Siga las instrucciones descritas en [Introducción a los cmdlets de Azure PowerShell](/powershell/azure/get-started-azureps) para iniciar sesión en su cuenta de Azure y recuperar y tener acceso a los recursos de la suscripción.

## <a name="provision-a-service-bus-namespace"></a>Aprovisionar un espacio de nombres de Service Bus

Al trabajar con espacios de nombres de Service Bus, puede usar los cmdlets [Get AzureRmServiceBusNamespace](/powershell/module/azurerm.servicebus/get-azurermservicebusnamespace), [New-AzureRmServiceBusNamespace](/powershell/module/azurerm.servicebus/new-azurermservicebusnamespace), [Remove-AzureRmServiceBusNamespace](/powershell/module/azurerm.servicebus/remove-azurermservicebusnamespace) y [Set-AzureRmServiceBusNamespace](/powershell/module/azurerm.servicebus/set-azurermservicebusnamespace).

En este ejemplo se crean algunas variables locales en el script: `$Namespace` y `$Location`.

* `$Namespace` es el nombre del espacio de nombres de Service Bus con el que queremos trabajar.
* `$Location` identifica el centro de datos en el que aprovisionamos el espacio de nombres.
* `$CurrentNamespace` almacena el espacio de nombres de referencia que vamos a recuperar (o crear).

En un script real, `$Namespace` y `$Location` se pueden pasar como parámetros.

Esta parte del script hace lo siguiente:

1. Intenta recuperar un espacio de nombres de Service Bus con el nombre especificado.
2. Si se encuentra el espacio de nombres, informa de lo que ha encontrado.
3. Si no se encuentra el espacio de nombres, lo crea y luego recupera el espacio de nombres recién creado.
   
    ``` powershell
    # Query to see if the namespace currently exists
    $CurrentNamespace = Get-AzureRMServiceBusNamespace -ResourceGroup $ResGrpName -NamespaceName $Namespace
   
    # Check if the namespace already exists or needs to be created
    if ($CurrentNamespace)
    {
        Write-Host "The namespace $Namespace already exists in the $Location region:"
        # Report what was found
        Get-AzureRMServiceBusNamespace -ResourceGroup $ResGrpName -NamespaceName $Namespace
    }
    else
    {
        Write-Host "The $Namespace namespace does not exist."
        Write-Host "Creating the $Namespace namespace in the $Location region..."
        New-AzureRmServiceBusNamespace -ResourceGroup $ResGrpName -NamespaceName $Namespace -Location $Location
        $CurrentNamespace = Get-AzureRMServiceBusNamespace -ResourceGroup $ResGrpName -NamespaceName $Namespace
        Write-Host "The $Namespace namespace in Resource Group $ResGrpName in the $Location region has been successfully created."
                
    }
    ```

### <a name="create-a-namespace-authorization-rule"></a>Creación de una regla de autorización de espacios de nombres

En el ejemplo siguiente se muestra cómo administrar las reglas de autorización de espacios de nombres mediante los cmdlets [New-AzureRmServiceBusNamespaceAuthorizationRule](/powershell/module/azurerm.servicebus/new-azurermservicebusnamespaceauthorizationrule), [Get- AzureRmServiceBusNamespaceAuthorizationRule](/powershell/module/azurerm.servicebus/get-azurermservicebusnamespaceauthorizationrule), [Set-AzureRmServiceBusNamespaceAuthorizationRule](/powershell/module/azurerm.servicebus/set-azurermservicebusnamespaceauthorizationrule) y [Remove-AzureRmServiceBusNamespaceAuthorizationRule](/powershell/module/azurerm.servicebus/remove-azurermservicebusnamespaceauthorizationrule).

```powershell
# Query to see if rule exists
$CurrentRule = Get-AzureRmServiceBusNamespaceAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthorizationRuleName $AuthRule

# Check if the rule already exists or needs to be created
if ($CurrentRule)
{
    Write-Host "The $AuthRule rule already exists for the namespace $Namespace."
}
else
{
    Write-Host "The $AuthRule rule does not exist."
    Write-Host "Creating the $AuthRule rule for the $Namespace namespace..."
    New-AzureRmServiceBusNamespaceAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthorizationRuleName $AuthRule -Rights @("Listen","Send")
    $CurrentRule = Get-AzureRmServiceBusNamespaceAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthorizationRuleName $AuthRule
    Write-Host "The $AuthRule rule for the $Namespace namespace has been successfully created."

    Write-Host "Setting rights on the namespace"
    $authRuleObj = Get-AzureRmServiceBusNamespaceAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthorizationRuleName $AuthRule

    Write-Host "Remove Send rights"
    $authRuleObj.Rights.Remove("Send")
    Set-AzureRmServiceBusNamespaceAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthRuleObj $authRuleObj

    Write-Host "Add Send and Manage rights to the namespace"
    $authRuleObj.Rights.Add("Send")
    Set-AzureRmServiceBusNamespaceAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthRuleObj $authRuleObj
    $authRuleObj.Rights.Add("Manage")
    Set-AzureRmServiceBusNamespaceAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthRuleObj $authRuleObj

    Write-Host "Show value of primary key"
    $CurrentKey = Get-AzureRmServiceBusNamespaceKey -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthorizationRuleName $AuthRule
        
    Write-Host "Remove this authorization rule"
    Remove-AzureRmServiceBusNamespaceAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthorizationRuleName $AuthRule
}
```

## <a name="create-a-queue"></a>Creación de una cola

Para crear una cola o un tema, realice una comprobación de espacio de nombres mediante el script de la sección anterior. Después, cree la cola:

```powershell
# Check if queue already exists
$CurrentQ = Get-AzureRmServiceBusQueue -ResourceGroup $ResGrpName -NamespaceName $Namespace -QueueName $QueueName

if($CurrentQ)
{
    Write-Host "The queue $QueueName already exists in the $Location region:"
}
else
{
    Write-Host "The $QueueName queue does not exist."
    Write-Host "Creating the $QueueName queue in the $Location region..."
    New-AzureRmServiceBusQueue -ResourceGroup $ResGrpName -NamespaceName $Namespace -QueueName $QueueName -EnablePartitioning $True
    $CurrentQ = Get-AzureRmServiceBusQueue -ResourceGroup $ResGrpName -NamespaceName $Namespace -QueueName $QueueName
    Write-Host "The $QueueName queue in Resource Group $ResGrpName in the $Location region has been successfully created."
}
```

### <a name="modify-queue-properties"></a>Modificación de las propiedades de una cola

Después de ejecutar el script de las sección anterior, puede usar el cmdlet [Set-AzureRmServiceBusQueue](/powershell/module/azurerm.servicebus/set-azurermservicebusqueue) para actualizar las propiedades de una cola, como en el ejemplo siguiente:

```powershell
$CurrentQ.DeadLetteringOnMessageExpiration = $True
$CurrentQ.MaxDeliveryCount = 7
$CurrentQ.MaxSizeInMegabytes = 2048
$CurrentQ.EnableExpress = $True

Set-AzureRmServiceBusQueue -ResourceGroup $ResGrpName -NamespaceName $Namespace -QueueName $QueueName -QueueObj $CurrentQ
```

## <a name="provisioning-other-service-bus-entities"></a>Aprovisionamiento de otras entidades de Service Bus

Puede usar el [módulo de PowerShell de Service Bus](/powershell/module/azurerm.servicebus) para aprovisionar otras entidades, como temas y suscripciones. Estos cmdlets son sintácticamente similares a los de creación de colas que se han mostrado en la sección anterior.

## <a name="next-steps"></a>pasos siguientes

- Consulte la documentación completa del módulo de PowerShell de Resource Manager de Service Bus [aquí](/powershell/module/azurerm.servicebus). Esta página enumera todos los cmdlets disponibles.
- Para obtener información acerca del uso de las plantillas de Azure Resource Manager, consulte el artículo [Cómo crear recursos de Service Bus mediante plantillas de Azure Resource Manager](service-bus-resource-manager-overview.md).
- Información sobre las [bibliotecas de administración de .NET de Service Bus](service-bus-management-libraries.md).

Hay varias formas alternativas de administrar entidades de Service Bus, tal como se describe en estas entradas de blog:

* [Cómo crear colas, temas y suscripciones de Service Bus con un script de PowerShell](http://blogs.msdn.com/b/paolos/archive/2014/12/02/how-to-create-a-service-bus-queues-topics-and-subscriptions-using-a-powershell-script.aspx)
* [Cómo crear un espacio de nombres de Service Bus y un centro de eventos mediante un script de PowerShell](http://blogs.msdn.com/b/paolos/archive/2014/12/01/how-to-create-a-service-bus-namespace-and-an-event-hub-using-a-powershell-script.aspx)
* [Scripts de PowerShell de Service Bus](https://code.msdn.microsoft.com/Service-Bus-PowerShell-a46b7059)

<!--Anchors-->

[purchase options]: http://azure.microsoft.com/pricing/purchase-options/
[member offers]: http://azure.microsoft.com/pricing/member-offers/
[free account]: http://azure.microsoft.com/pricing/free-trial/
