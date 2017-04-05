---
title: "Migración de circuitos ExpressRoute y las redes virtuales asociadas del modelo clásico a Resource Manager: Azure | Microsoft Docs"
description: "En esta página se describe cómo migrar un circuito clásico y las redes virtuales asociadas a Resource Manager."
documentationcenter: na
services: expressroute
author: ganesr
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 08152836-23e7-42d1-9a56-8306b341cd91
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2017
ms.author: ganesr;cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: 9a3ad5be7cc12f1c82eab4a8b3089276a434c234
ms.lasthandoff: 03/29/2017


---
# <a name="migrate-expressroute-circuits-and-associated-virtual-networks-from-the-classic-to-the-resource-manager-deployment-model"></a>Migración de circuitos ExpressRoute y las redes virtuales asociadas del modelo de implementación clásica a Resource Manager

En este artículo se explica cómo migrar circuitos ExpressRoute de Azure y redes virtuales asociadas desde el modelo de implementación clásica al modelo de implementación de Azure Resource Manager. 


## <a name="before-you-begin"></a>Antes de empezar
* Compruebe que dispone de la versión más reciente de los módulos de Azure PowerShell. Para más información, vea [Instalación y configuración de Azure PowerShell](/powershell/azureps-cmdlets-docs).
* Asegúrese de haber revisado los [requisitos previos](expressroute-prerequisites.md), los [requisitos de enrutamiento](expressroute-routing.md) y los [flujos de trabajo](expressroute-workflows.md) antes de comenzar la configuración.
* Revise la información que se proporciona en [Transición de un circuito ExpressRoute desde la implementación clásica a la implementación de Resource Manager](expressroute-move.md). Asegúrese de que comprende perfectamente los límites y restricciones.
* Compruebe que el circuito está totalmente operativo en el modelo de implementación clásica.
* Asegúrese de tener un grupo de recursos creado en el modelo de implementación de Resource Manager.
* Revise la siguiente documentación de migración de recursos:

    * [Migración compatible con la plataforma de recursos de IaaS del modelo clásico al de Azure Resource Manager](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
    * [Profundización técnica en la migración compatible con la plataforma de la implementación clásica a la de Azure Resource Manager](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
    * [FAQs: Platform-supported migration of IaaS resources from classic to Azure Resource Manager](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md) (Preguntas frecuentes: Migración compatible con la plataforma de recursos de IaaS desde el modelo de implementación clásica a Azure Resource Manager)
    * [Common errors during classic to Azure Resource Manager migration](../virtual-machines/virtual-machines-migration-errors.md) (Errores comunes durante la migración del modelo de implementación clásica a Azure Resource Manager)

## <a name="supported-and-unsupported-scenarios"></a>Escenarios admitidos y no admitidos

* Un circuito ExpressRoute se puede migrar desde el modelo de implementación clásica al entorno de Resource Manager sin tiempo de inactividad. Puede mover un circuito ExpressRoute del entorno clásico al entorno de Resource Manager sin tiempo de inactividad. Siga las instrucciones sobre [cómo mover circuitos ExpressRoute del modelo de implementación clásica a Resource Manager mediante PowerShell](expressroute-howto-move-arm.md). Este es un requisito previo para mover recursos conectados a la red virtual.
* Las redes virtuales, las puertas de enlace y las implementaciones asociadas dentro de la red virtual que están conectadas a un circuito ExpressRoute de la misma suscripción se pueden migrar al entorno de Resource Manager sin tiempo de inactividad. Puede seguir los pasos descritos más adelante para migrar recursos, como redes virtuales, puertas de enlace y máquinas virtuales implementadas dentro de la red virtual. Debe asegurarse de que las redes virtuales estén configuradas correctamente antes de la migración. 
* Las redes virtuales, las puertas de enlace y las implementaciones asociadas dentro de la red virtual que no estén en la misma suscripción que el circuito ExpressRoute requerirán algún tiempo de inactividad para completar la migración. En la última sección del documento se describen los pasos que se deben seguir para migrar los recursos.

## <a name="move-an-expressroute-circuit-from-classic-to-resource-manager"></a>Movimiento de un circuito ExpressRoute del modelo clásico a Resource Manager
Deberá mover un circuito ExpressRoute del entorno clásico a Resource Manager antes de intentar migrar los recursos que están conectados al circuito ExpressRoute. Para realizar esta tarea, consulte los siguientes artículos:

* Revise la información que se proporciona en [Transición de un circuito ExpressRoute desde la implementación clásica a la implementación de Resource Manager](expressroute-move.md).
* [Transición de los circuitos ExpressRoute desde el modelo de implementación clásica al modelo de implementación de Resource Manager mediante Azure PowerShell](expressroute-howto-move-arm.md).
* Use el portal de Azure Service Management. Puede seguir el flujo de trabajo para [crear un nuevo circuito ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md) y seleccionar la opción de importación. 

Esta operación no requiere tiempo de inactividad. Puede continuar transfiriendo datos entre el entorno local y Microsoft mientras la migración está en curso.

## <a name="prepare-your-virtual-network-for-migration"></a>Preparación de la red virtual para la migración
Debe asegurarse de que la red de la red virtual que se va a migrar no tenga artefactos innecesarios. Para descargar la configuración de la red virtual y actualizarla si es necesario, ejecute el siguiente cmdlet de PowerShell:

    Add-AzureAccount
    Select-AzureSubscription -SubscriptionName <VNET Subscription>
    Get-AzureVNetConfig -ExportToFile C:\virtualnetworkconfig.xml
      
Debe asegurarse de que todas las referencias a <ConnectionsToLocalNetwork> se hayan quitado de las redes virtuales que se van a migrar. En el fragmento de código siguiente se muestra una configuración de red de ejemplo:

    <VirtualNetworkSite name="MyVNet" Location="East US">
        <AddressSpace>
            <AddressPrefix>10.0.0.0/8</AddressPrefix>
        </AddressSpace>
        <Subnets>
            <Subnet name="Subnet-1">
                <AddressPrefix>10.0.0.0/11</AddressPrefix>
            </Subnet>
            <Subnet name="GatewaySubnet">
                <AddressPrefix>10.32.0.0/28</AddressPrefix>
            </Subnet>
        </Subnets>
        <Gateway>
            <ConnectionsToLocalNetwork>
            </ConnectionsToLocalNetwork>
        </Gateway>
    </VirtualNetworkSite>
 
Si <ConnectionsToLocalNetwork> no está vacío, elimine las referencias que contiene y vuelva a enviar su configuración de red. Para ello, ejecute el siguiente cmdlet de PowerShell:

    Set-AzureVNetConfig -ConfigurationPath c:\virtualnetworkconfig.xml

## <a name="migrate-virtual-networks-gateways-and-associated-deployments-in-the-same-subscription-as-the-expressroute-circuit"></a>Migración de redes virtuales, puertas de enlace e implementaciones asociadas en la misma suscripción que el circuito ExpressRoute
En esta sección se describen los pasos que se deben seguir para migrar una red virtual, una puerta de enlace y las implementaciones asociadas en la misma suscripción que el circuito ExpressRoute. No hay tiempo de inactividad asociado a esta migración. Puede seguir usando todos los recursos a través del proceso de migración. El plano de administración se bloquea mientras la migración está en curso. 

1. Asegúrese de que el circuito ExpressRoute se ha movido del entorno clásico al entorno de Resource Manager.
2. Asegúrese de que la red virtual se ha preparado correctamente para la migración.
3. Registre la suscripción para la migración de recursos. Para registrar la suscripción para la migración de recursos, use el siguiente fragmento de código de PowerShell: 
    ```
    Select-AzureRmSubscription -SubscriptionName <Your Subscription Name>
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
    Get-AzureRmResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
    ```
4. Valide, prepare y migre. Para mover la red virtual, use el siguiente fragmento de código de PowerShell:
    ```
    Move-AzureVirtualNetwork -Prepare $vnetName  
    Move-AzureVirtualNetwork -Commit $vnetName
    ```
    También puede cancelar la migración mediante la ejecución del siguiente cmdlet de PowerShell:
    ```
    Move-AzureVirtualNetwork -Abort $vnetName
    ``` 
## <a name="migrate-virtual-networks-gateways-and-associated-deployments-in-a-different-subscription-from-that-of-the-expressroute-circuit"></a>Migración de redes virtuales, puertas de enlace e implementaciones asociadas en una suscripción diferente a la del circuito ExpressRoute

1. Asegúrese de que el circuito ExpressRoute se ha movido del entorno clásico al entorno de Resource Manager.
2. Asegúrese de que la red virtual se ha preparado correctamente para la migración.
3. Asegúrese de que el circuito ExpressRoute puede funcionar en el entorno clásico y en el de Resource Manager. Para permitir que el circuito pueda usarse en los entornos clásico y de Resource Manager, use el siguiente script de PowerShell: 
    ```
    Login-AzureRmAccount
    Select-AzureRmSubscription -SubscriptionName <My subscription>
    $circuit = Get-AzureRmExpressRouteCircuit -Name <CircuitName> -ResourceGroupName <ResourceGroup Name> 
    $circuit.AllowClassicOperations = $true
    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $circuit
    ```
4. Cree autorizaciones en el entorno de Resource Manager. Para aprender a crear autorizaciones, consulte [cómo vincular redes virtuales a circuitos ExpressRoute](expressroute-howto-linkvnet-arm.md). Para crear una autorización, use el siguiente fragmento de código de PowerShell:
    ```
    circuit = Get-AzureRmExpressRouteCircuit -Name <CircuitName> -ResourceGroupName <ResourceGroup Name> 
    Add-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "AuthorizationForMigration"
    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $circuit
    $circuit = Get-AzureRmExpressRouteCircuit -Name MigrateCircuit -ResourceGroupName MigrateRGWest

    $id = $circuit.id 
    $auth1 = Get-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "AuthorizationForMigration"

    $key=$auth1.AuthorizationKey 
    ```
    Anote el id. de circuito y la clave de autorización. Estos elementos se usan para conectar el circuito a la red virtual una vez completada la migración.
  
5. Elimine el vínculo de circuito dedicado que está asociado con la red virtual. Para quitar el vínculo de circuito en el entorno clásico, use el siguiente cmdlet: 
    ```
    $skey = Get-AzureDedicatedCircuit | select ServiceKey
    Remove-AzureDedicatedCircuitLink -ServiceKey $skey -VNetName $vnetName
    ```  

6. Registre la suscripción para la migración de recursos. Para registrar la suscripción para la migración de recursos, use el siguiente fragmento de código de PowerShell: 
    ```
    Select-AzureRmSubscription -SubscriptionName <Your Subscription Name>
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
    Get-AzureRmResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
    ```
7. Valide, prepare y migre. Para mover la red virtual, use el siguiente fragmento de código de PowerShell:
    ```
    Move-AzureVirtualNetwork -Prepare $vnetName  
    Move-AzureVirtualNetwork -Commit $vnetName
    ```
    También puede cancelar la migración mediante la ejecución del siguiente cmdlet de PowerShell:
    ```
    Move-AzureVirtualNetwork -Abort $vnetName
    ```
8. Conecte de nuevo la red virtual al circuito ExpressRoute. El siguiente fragmento de código de PowerShell se ejecuta en el contexto de la suscripción en la que se crea la red virtual. No lo debe ejecutar en la suscripción donde se crea el circuito. Use el id. de circuito como PeerID y la clave de autorización que anotó en el paso 4.
    ```
    Select-AzureRMSubscription –SubscriptionName <customer subscription>  
    $gw = Get-AzureRmVirtualNetworkGateway -Name $vnetName-Default-Gateway -ResourceGroupName ($vnetName + "-Migrated")
    $vnet = Get-AzureRmVirtualNetwork -Name $vnetName -ResourceGroup  ($vnetName + "-Migrated")  

    New-AzureRmVirtualNetworkGatewayConnection -Name  ($vnetName + "-GwConn") -ResourceGroupName ($vnetName + "-Migrated")  -Location $vnet.Location -VirtualNetworkGateway1 $gw -PeerId $id -ConnectionType ExpressRoute -AuthorizationKey $key
    ```
## <a name="next-steps"></a>Pasos siguientes
* [Migración compatible con la plataforma de recursos de IaaS del modelo clásico al de Azure Resource Manager](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
* [Profundización técnica en la migración compatible con la plataforma de la implementación clásica a la de Azure Resource Manager](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
* [FAQs: Platform-supported migration of IaaS resources from classic to Azure Resource Manager](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md) (Preguntas frecuentes: Migración compatible con la plataforma de recursos de IaaS desde el modelo de implementación clásica a Azure Resource Manager)
* [Common errors during classic to Azure Resource Manager migration](../virtual-machines/virtual-machines-migration-errors.md) (Errores comunes durante la migración del modelo de implementación clásica a Azure Resource Manager)

