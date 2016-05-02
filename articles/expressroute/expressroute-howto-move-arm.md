<properties
   pageTitle="Transición de circuitos ExpressRoute desde el modelo de implementación clásica al modelo de implementación de Resource Manager | Microsoft Azure"
   description="En esta página se describe cómo mover un circuito clásico al modelo de implementación de Resource Manager"
   documentationCenter="na"
   services="expressroute"
   authors="ganesr"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/01/2016"
   ms.author="ganesr"/>


# Transición de los circuitos ExpressRoute desde el modelo de implementación clásica al modelo de implementación de Resource Manager

## Requisitos previos de configuración

- Necesitará la versión más reciente de los módulos de Azure PowerShell (como mínimo, la versión 1.0).
- Asegúrese de haber revisado los [requisitos previos](expressroute-prerequisites.md), los [requisitos de enrutamiento](expressroute-routing.md) y los [flujos de trabajo](expressroute-workflows.md) antes de comenzar la configuración.
- Antes de continuar, revise la información que se proporciona en [Transición de un circuito ExpressRoute desde la implementación clásica a la implementación de Resource Manager](expressroute-move.md). Asegúrese de entender completamente los límites y las limitaciones.
- Si desea mover un circuito de Azure ExpressRoute desde el modelo de implementación clásica al modelo de implementación de Azure Resource Manager, debe tener completamente configurado y en funcionamiento el circuito en el modelo de implementación clásica.
- Asegúrese de tener un grupo de recursos creado en el modelo de implementación de Resource Manager.

## Transición del circuito ExpressRoute al modelo de implementación de Resource Manager

Debe mover un circuito ExpressRoute al modelo de implementación de Resource Manager para poder usarlo en ambos modelos de implementación. Para hacerlo, ejecute los siguientes comandos de PowerShell.

### Paso 1: Recopile detalles del circuito desde el modelo de implementación clásica

Debe recopilar primero información sobre su circuito ExpressRoute.

Inicie sesión en el entorno clásico de Azure y recopile la clave de servicio. Puede usar el siguiente fragmento de código de PowerShell para recopilar la información:

	# Sign in to your Azure account
	Add-AzureAccount

	# Select the appropriate Azure subscription
	Select-AzureSubscription "<Enter Subscription Name here>"

	# Import the PowerShell modules for Azure and ExpressRoute
	Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Azure.psd1'
	Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\ExpressRoute\ExpressRoute.psd1'

	# Get the service keys of all your ExpressRoute circuits
	Get-AzureDedicatedCircuit

Copie la **clave de servicio** del circuito que desea mover al modelo de implementación de Resource Manager.

### Paso 2: Inicie sesión en el entorno de Resource Manager y cree un grupo de recursos nuevo

Puede crear un grupo de recursos nuevo mediante el siguiente fragmento de código:

	# Sign in to your Azure Resource Manager environment
	Login-AzureRmAccount

	# Select the appropriate Azure subscription
	Get-AzureRmSubscription -SubscriptionName "<Enter Subscription Name here>" | Select-AzureRmSubscription

	#Create a new resource group if you don't already have one
	New-AzureRmResourceGroup -Name "DemoRG" -Location "West US"

También puede usar un grupo de recursos existente si lo tiene.

### Paso 3: Mover el circuito ExpressRoute al modelo de implementación de Resource Manager

Ya está listo para mover el circuito ExpressRoute desde el modelo de implementación clásica al modelo de implementación de Resource Manager. Revise la información que se proporciona en [Transición de un circuito ExpressRoute desde el modelo de implementación clásica al modelo de implementación de Resource Manager](expressroute-move.md) antes de continuar.

Para ello, ejecute el siguiente fragmento de código:

	Move-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "DemoRG" -Location "West US" -ServiceKey "<Service-key>"

>[AZURE.NOTE] Una vez que se termine la transición, se usará el nombre nuevo que aparece en el cmdlet anterior para referirse al recurso. Básicamente, se cambiará el nombre del circuito.

## Habilitar un circuito ExpressRoute para ambos modelos de implementación

Debe mover el circuito ExpressRoute al modelo de implementación de Resource Manager antes de controlar el acceso al modelo de implementación.

Ejecute el siguiente cmdlet para habilitar el acceso a ambos modelos de implementación:

    # Get details of the ExpressRoute circuit
    $ckt = Get-AzureRmExpressRouteCircuit -Name "DemoCkt" -ResourceGroupName "DemoRG"

    #Set "Allow Classic Operations" to TRUE
    $ckt.AllowClassicOperations = $true

    # Update circuit
    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

Una vez que esta operación finalice correctamente, podrá ver el circuito en el modelo de implementación clásica.

Ejecute lo siguiente para obtener los detalles del circuito ExpressRoute:

    get-azurededicatedcircuit

También debe poder ver la clave de servicio. Ahora puede administrar los vínculos al circuito ExpressRoute. Los artículos siguientes le guiarán para administrar vínculos al circuito ExpressRoute:

- [Vinculación de redes virtuales a circuitos ExpressRoute en el modelo de implementación de Resource Manager](expressroute-howto-linkvnet-arm.md)
- [Vinculación de redes virtuales a circuitos ExpressRoute en el modelo de implementación clásica](expressroute-howto-linkvnet-classic.md)


## Deshabilitar el circuito ExpressRoute en el modelo de implementación clásica

Ejecute el siguiente cmdlet para deshabilitar el acceso al modelo de implementación clásica:

    # Get details of the ExpressRoute circuit
    $ckt = Get-AzureRmExpressRouteCircuit -Name "DemoCkt" -ResourceGroupName "DemoRG"

    #Set "Allow Classic Operations" to FALSE
    $ckt.AllowClassicOperations = $false

    # Update circuit
    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

Una vez que esta operación finalice correctamente, no podrá ver el circuito en el modelo de implementación clásica.

## Pasos siguientes

Después de crear el circuito, asegúrese de hacer lo siguiente:

- [Crear y modificar el enrutamiento para el circuito ExpressRoute](expressroute-howto-routing-arm.md)
- [Vincular la red virtual a su circuito ExpressRoute](expressroute-howto-linkvnet-arm.md)

<!---HONumber=AcomDC_0420_2016-->