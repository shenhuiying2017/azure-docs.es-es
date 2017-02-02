---
title: Infraestructura y conectividad con SAP HANA en Azure (Instancias grandes) | Microsoft Docs
description: "Configuración de la infraestructura de conectividad necesaria para usar SAP HANA en Azure (Instancias grandes)."
services: virtual-machines-linux
documentationcenter: 
author: RicksterCDN
manager: timlt
editor: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/01/2016
ms.author: rclaus
translationtype: Human Translation
ms.sourcegitcommit: a3e99cf86cbe6ae18366b13047d8c2994ff398f4
ms.openlocfilehash: 9adf67b706f866fbb41c2b82970eb0e44aada5c6


---
# <a name="infrastructure-and-connectivity-to-sap-hana-on-azure-large-instances"></a>Infraestructura y conectividad con SAP HANA en Azure (Instancias grandes)

Una vez finalizada la compra de SAP HANA en Azure (Instancias grandes) entre el usuario y el equipo de cuentas de empresa de Microsoft, es necesaria la siguiente información:

- Nombre del cliente
- Información del contacto de la empresa (incluida la dirección de correo electrónico y el número de teléfono)
- Información del contacto técnico (incluida la dirección de correo electrónico y el número de teléfono)
- Información del contacto técnico de red (incluida la dirección de correo electrónico y el número de teléfono)
- Región de implementación de Azure (oeste de EE. UU. o este de EE. UU. a partir de septiembre de 2016)
- Confirmación de la SKU de SAP HANA en Azure (Instancias grandes) (configuración)
- Para cada región de Azure que se va a implementar:
  - Un intervalo de direcciones IP /29 para las conexiones P2P
  - Un bloque CIDR (usado para el grupo NAT de Instancias grandes de HANA; se recomienda /24)
- Para cada red virtual de Azure conectada a Instancias grandes de HANA, independientemente de la región de Azure:
  - Uno o más intervalos de direcciones IP /28s o /27 (para la subred de puerta de enlace de la red virtual del cliente)
  - Uno o más bloques CIDR (para la subred de inquilino de la red virtual del cliente; se recomienda /24)
- Datos para cada sistema Instancias grandes de HANA:
  - Nombre de host deseado
  - Dirección IP deseada del grupo NAT
- Número de suscripción de Azure para la suscripción de Azure a la que SAP HANA en Azure (Instancias grandes) estará directamente conectado
- Nombre del SID de SAP HANA para la instancia de SAP HANA (necesario para crear los volúmenes de disco necesarios relacionados con SAP HANA)

Después de proporcionar la información, Microsoft aprovisiona SAP HANA en Azure (Instancias grandes).

A continuación, se le proporciona información de configuración de la red para:

- Conectar sus redes virtuales de Azure al circuito de ExpressRoute que conecta dichas redes a Instancias grandes de HANA
  - Para Azure Resource Manager:
     - Claves de autorización
     - PeerID de ExpressRoute
- Acceder a Instancias grandes de HANA con el circuito de ExpressRoute establecido y la red virtual de Azure

## <a name="creating-an-azure-vnet"></a>Creación de una red virtual de Azure

Esta red virtual de Azure debe crearse mediante el modelo de implementación de Azure Resource Manager. Esta solución no admite el modelo de implementación de Azure antiguo, normalmente conocido como ASM.

La red virtual de Azure que se crea debe tener al menos una subred de inquilino y una subred de puerta de enlace. Los intervalos de direcciones IP deben asignarse a dichas subredes según se especifica y enviarse a Microsoft.

> [!IMPORTANT] 
> Solo se deben asignar bloques de direcciones de puerta de enlace y de inquilinos a la red virtual en la suscripción de Azure. Los bloques de direcciones del grupo NAT y P2P deben ser independientes de la red virtual y de los espacios de direcciones de subred, ya que están fuera de la suscripción de Azure.

Se pueden usar varias subredes de inquilino (incluso utilizando intervalos de direcciones no contiguas), pero como ya se mencionó, estos intervalos de direcciones deben enviarse antes a Microsoft.

Puede usar el estándar de asignación de nombres que desee para estas subredes de inquilino. Sin embargo, **siempre debe haber una (solo una) subred de puerta de enlace para cada red virtual** que se conecta al circuito de ExpressRoute de SAP HANA en Azure (Instancias grandes), y **dicha subred siempre debe llamarse &quot;GatewaySubnet&quot; ** para garantizar la correcta ubicación de la puerta de enlace de ExpressRoute.

> [!WARNING] 
> Es fundamental que la subred de puerta de enlace siempre se llame &quot;GatewaySubnet&quot;.

La red virtual puede crearse mediante Azure Portal, PowerShell, una plantilla de Azure o la CLI de Azure (consulte [Creación de una red virtual mediante Azure Portal](../../virtual-network/virtual-networks-create-vnet-arm-pportal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)).

## <a name="creating-a-gateway-subnet"></a>Creación de una subred de puerta de enlace

Una vez creada la red virtual de Azure, es necesario crear una puerta de enlace de ExpressRoute en la red virtual para vincular la red al circuito de ExpressRoute que conecta con el inquilino del cliente en la marca Instancias grandes.

> [!NOTE] 
> Este paso puede tardar hasta 30 minutos en completarse, ya que la nueva puerta de enlace se crea en la suscripción de Azure designada y, después, se conecta a la red virtual de Azure especificada.

Si ya existe una puerta de enlace, compruebe si se trata de una puerta de enlace de ExpressRoute o no. Si no lo es, debe eliminarla y volver a crearla como una puerta de enlace de ExpressRoute. Si ya hay una puerta de enlace de ExpressRoute establecida, la red virtual de Azure ya está conectada al circuito de ExpressRoute para la conectividad local; por tanto, continúe con la sección Vinculación de redes virtuales, que aparece a continuación.

- Utilice [Azure Portal](https://portal.azure.com/) (nuevo) o PowerShell para crear una puerta de enlace de red virtual de ExpressRoute conectada a la red virtual.
  - Si usa Azure Portal, agregue una nueva **puerta de enlace de red virtual** y seleccione **ExpressRoute** como tipo de puerta de enlace.
  - Si prefiere PowerShell, descargue y use la versión más reciente [SDK de Azure PowerShell](https://azure.microsoft.com/downloads/) para garantizar una experiencia óptima. Los siguientes comandos crean una puerta de enlace de ExpressRoute. Los textos precedidos por un signo _$_ son variables definidas por el usuario que es necesario actualizar con su información específica.

```
# These Values should already exist, update to match your environment
$myAzureRegion = "eastus"
$myGroupName = "SAP-East-Coast"
$myVNetName = "VNet01"

# These values are used to create the gateway, update for how you wish the GW components to be named
$myGWName = "VNet01GW"
$myGWConfig = "VNet01GWConfig"
$myGWPIPName = "VNet01GWPIP"
$myGWSku = "HighPerformance" # Supported values for HANA Large Instances are: HighPerformance or UltraPerformance

# These Commands create the Public IP and ExpressRoute Gateway
$vnet = Get-AzureRmVirtualNetwork -Name $myVNetName -ResourceGroupName $myGroupName
$subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
New-AzureRmPublicIpAddress -Name $myPIPName -ResourceGroupName $myGroupName `
-Location $myAzureRegion -AllocationMethod Dynamic
$gwpip = Get-AzureRmPublicIpAddress -Name $myPIPName -ResourceGroupName $myGroupName
$gwipconfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name $myGWConfig -SubnetId $subnet.Id `
-PublicIpAddressId $gwpip.Id

New-AzureRmVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName -Location $myAzureRegion `
-IpConfigurations $gwipconfig -GatewayType ExpressRoute `
-GatewaySku $myGWSku -VpnType PolicyBased -EnableBgp $true
```

En este ejemplo, se usó la SKU de puerta de enlace HighPerformance. Puede elegir HighPerformance o UltraPerformance, las únicas SKU de puerta de enlace que son compatibles con SAP HANA en Azure (Instancias grandes).

## <a name="linking-vnets"></a>Vinculación de redes virtuales

Ahora que la red virtual de Azure tiene una puerta de enlace de ExpressRoute, utilice la información de autorización proporcionada por Microsoft para conectar la puerta de enlace de ExpressRoute al circuito de ExpressRoute de SAP HANA en Azure (Instancias grandes) creado para esta conectividad. Esto solo puede realizarse con PowerShell (actualmente no se puede hacer con Azure Portal).

- Haga lo siguiente para cada puerta de enlace de red virtual, con un AuthGUID diferente para cada conexión. Las dos primeras entradas mostradas a continuación proceden de la información proporcionada por Microsoft. Además, el AuthGUID es específico de cada red virtual y su puerta de enlace.

```
# Populate with information provided by Microsoft Onboarding team
$PeerID = "/subscriptions/9cb43037-9195-4420-a798-f87681a0e380/resourceGroups/Customer-USE-Circuits/providers/Microsoft.Network/expressRouteCircuits/Customer-USE01-5Gb"
$AuthGUID = "76d40466-c458-4d14-adcf-3d1b56d1cd61"

# Your ExpressRoute Gateway Information
$myGroupName = "SAP-East-Coast"
$myGWName = "VNet01GW"
$myGWLocation = "East US"

# Define the name for your connection
$myConnectionName = "VNet01GWConnection"

# Create a new connection between the ER Circuit and your Gateway using the Authorization
$gw = Get-AzureRmVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName
    
New-AzureRmVirtualNetworkGatewayConnection -Name $myConnectionName `
-ResourceGroupName $myGroupName -Location $myGWLocation -VirtualNetworkGateway1 $gw `
-PeerId $PeerID -ConnectionType ExpressRoute -AuthorizationKey $AuthGUID
```

Debe realizar este paso más de una vez si desea conectar la puerta de enlace a varios circuitos de ExpressRoute asociados a su suscripción.

## <a name="adding-more-ip-addresses-or-subnets"></a>incorporación de más direcciones IP o subredes

Use Azure Portal, PowerShell o la CLI al agregar más IP direcciones o subredes.

Si aún no ha declarado el intervalo de espacios de direcciones IP adicional con SAP HANA en Azure Service Management, abra una solicitud de soporte técnico de Azure para obtenerlo agregado. Después de recibir la confirmación, realice los pasos siguientes.

Para crear una subred adicional desde Azure Portal, consulte el artículo [Creación de una red virtual mediante Azure Portal](../../virtual-network/virtual-networks-create-vnet-arm-pportal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) y para crearla desde PowerShell, consulte [Creación de una red virtual mediante PowerShell](../../virtual-network/virtual-networks-create-vnet-arm-ps.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="adding-vnets"></a>Incorporación de redes virtuales

Después de conectarse inicialmente a una o varias redes virtuales de Azure, puede agregar otras que tengan acceso a SAP HANA en Azure (Instancias grandes). En primer lugar, envíe una solicitud de soporte técnico de Azure con información específica que identifique la implementación de Azure específica, así como los intervalos de espacios de direcciones IP para las subredes de inquilino y las subredes de puerta de enlace de las redes virtuales de Azure adicionales. A continuación, SAP HANA en Azure Service Management, proporciona la información necesaria para conectar las redes virtuales y ExpressRoute adicionales.

Pasos para agregar una nueva red virtual de Azure:

1. Complete el primer paso del proceso de incorporación; consulte la sección anterior, _Creación de una red virtual de Azure_.
2. Complete el segundo paso del proceso de incorporación; consulte la sección anterior, _Creación de una subred de puerta de enlace_.
3. Abra una solicitud de soporte técnico de Azure con información sobre la nueva red virtual y solicite una nueva clave de autorización para conectar sus redes virtuales adicionales al circuito de ExpressRoute de Instancias grandes de HANA.
4. Una vez que se le notifique que la autorización está completa, utilice la información de autorización proporcionada por Microsoft para completar el tercer paso del proceso de incorporación; consulte la sección anterior, _Vinculación de redes virtuales_.

## <a name="increasing-expressroute-circuit-bandwidth"></a>Aumento del ancho de banda del circuito de ExpressRoute

Consulte con SAP HANA en Azure Service Management. Si se le recomienda aumentar el ancho de banda del circuito de ExpressRoute de SAP HANA en Azure (Instancias grandes), cree una solicitud de soporte técnico de Azure. Puede solicitar un aumento del ancho de banda de un solo circuito de hasta 10 Gbps. Recibirá una notificación cuando la operación se complete; no es necesaria ninguna acción adicional para habilitar esta velocidad superior en Azure.

## <a name="adding-an-additional-expressroute-circuit"></a>Incorporación de un circuito de ExpressRoute adicional

Consulte con SAP HANA en Azure Service Management; si se le recomienda agregar otro circuito de ExpressRoute, abra una solicitud de soporte técnico de Azure para crear un circuito de ExpressRoute y obtener información de autorización para conectarse a él. Para que SAP HANA en Azure Service Management proporcione la autorización, es necesario definir el espacio de direcciones que se utilizará en las redes virtuales antes de realizar la solicitud.

Una vez creado el circuito y terminada la configuración en SAP HANA en Azure Service Management, recibirá una notificación con la información que necesita para continuar. Siga los pasos antes indicados para crear y conectar la nueva red virtual para este circuito adicional. No podrá conectar redes virtuales de Azure a este circuito adicional si ya están conectadas a otro circuito de ExpressRoute.

## <a name="deleting-a-subnet"></a>Eliminación de una subred

Para quitar una subred de red virtual, se puede usar Azure Portal, PowerShell o CLI. Si se quita un espacio de direcciones, SAP HANA en Azure Service Management debe notificar el cambio para quitarlo de los intervalos con los que se puede comunicar SAP HANA en Azure (Instancias grandes).

Aunque aún no existe ninguna guía en Azure.com específica sobre cómo quitar subredes, el proceso para hacerlo es el inverso del proceso para agregarlas. Consulte el artículo [Creación de una red virtual mediante Azure Portal](../../virtual-network/virtual-networks-create-vnet-arm-pportal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) para más información sobre la creación de subredes.

## <a name="deleting-a-vnet"></a>Eliminación de una red virtual

Utilice Azure Portal, PowerShell o CLI para eliminar una red virtual. SAP HANA en Azure Service Management quita las autorizaciones existentes en el circuito de ExpressRoute de SAP HANA en Azure (Instancias grandes) y elimina los intervalos de direcciones IP (intervalos de inquilino y de puerta de enlace) para la comunicación con Instancias grandes de HANA.

Una vez quitada la red virtual, abra una solicitud de soporte técnico de Azure para proporcionar el intervalo de espacios de direcciones IP que se va a quitar.

Aunque aún no existe ninguna guía en Azure.com específica sobre cómo quitar redes virtuales, el proceso para hacerlo es el inverso del proceso para agregarlas, que se ha descrito antes. Consulte los artículos [Creación de una red virtual mediante Azure Portal](../../virtual-network/virtual-networks-create-vnet-arm-pportal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) y [Creación de una red virtual mediante PowerShell](../../virtual-network/virtual-networks-create-vnet-arm-ps.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) para más información sobre la creación de redes virtuales.

Para asegurarse de que se quita todo, elimine los elementos siguientes:

- **Para Azure Resource Manager:** la conexión de ExpressRoute, la puerta de enlace de red virtual, la IP pública de la puerta de enlace de red virtual y la red virtual
- **Para una máquina virtual clásica:** la puerta de enlace de red virtual y la red virtual

## <a name="deleting-an-expressroute-circuit"></a>Eliminación de un circuito de ExpressRoute

Para quitar un circuito de ExpressRoute adicional de SAP HANA en Azure (Instancias grandes), abra una solicitud de soporte técnico de Azure en SAP HANA en Azure Service Management y solicite que se elimine. En la suscripción de Azure, puede eliminar o mantener la red virtual, según sea necesario. Sin embargo, debe eliminar (en el caso de Azure Resource Manager) o desvincular (en el caso de clásico) la conexión entre el circuito de ExpressRoute de Instancias grandes de HANA y la puerta de enlace de red virtual vinculada.

Si también desea quitar una red virtual, siga las instrucciones de la sección anterior, Eliminación de una red virtual.





<!--HONumber=Dec16_HO2-->


