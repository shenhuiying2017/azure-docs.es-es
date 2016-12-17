---
title: "Cómo configurar BGP en VPN Gateway de Azure mediante Azure Resource Manager y PowerShell | Microsoft Docs"
description: "Este artículo le guiará a la hora de configurar BGP con puertas de enlace de VPN de Azure por medio de Azure Resource Manager y PowerShell."
services: vpn-gateway
documentationcenter: na
author: yushwang
manager: rossort
editor: 
tags: azure-resource-manager
ms.assetid: 905b11a7-1333-482c-820b-0fd0f44238e5
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/15/2016
ms.author: yushwang
translationtype: Human Translation
ms.sourcegitcommit: 5919c477502767a32c535ace4ae4e9dffae4f44b
ms.openlocfilehash: d6d9834bca2a7248ebec0a614a6edfbb8657e895


---
# <a name="how-to-configure-bgp-on-azure-vpn-gateways-using-azure-resource-manager-and-powershell"></a>Cómo configurar BGP en puertas de enlace de VPN de Azure mediante Azure Resource Manager y PowerShell
Este artículo le guiará por los pasos para habilitar BGP en una conexión de VPN de sitio a sitio (S2S) entre locales y una conexión de red virtual a red virtual mediante el modelo de implementación de Resource Manager y PowerShell.

**Información acerca de los modelos de implementación de Azure**

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="about-bgp"></a>Información acerca de BGP
BGP es el protocolo de enrutamiento estándar usado habitualmente en Internet para intercambiar información de enrutamiento y disponibilidad entre dos o más redes. BGP permite que las puertas de enlace de VPN de Azure y los dispositivos VPN locales, denominados vecinos o pares BGP, intercambien "rutas" que comunicarán a ambas puertas de enlace la disponibilidad y la posibilidad de que dichos prefijos pasen a través de las puertas de enlace o los enrutadores implicados. BGP también puede permitir el enrutamiento del tránsito entre varias redes mediante la propagación de las rutas que una puerta de enlace de BGP aprende de un par BGP a todos los demás pares BGP.

Para más información sobre las ventajas de BGP y para entender los requisitos técnicos y las consideraciones sobre el uso de BGP, vea [Información general de BGP con Azure VPN Gateway](vpn-gateway-bgp-overview.md).

## <a name="getting-started-with-bgp-on-azure-vpn-gateways"></a>Introducción a BGP en puertas de enlace de VPN de Azure
Este artículo le guiará a través de los pasos para realizar las tareas siguientes:

* [Parte 1: Habilitar BGP en la puerta de enlace de VPN de Azure](#enablebgp)
* [Parte 2: Establecer una conexión entre locales con BGP](#crossprembgp)
* [Parte 3: Establecer una conexión de red virtual a red virtual con BGP](#v2vbgp)

Cada parte de las instrucciones constituye un bloque de creación básico para habilitar BGP en la conectividad de red. Si completa las tres partes, podrá crear la topología tal como se muestra en el diagrama siguiente:

![Topología de BGP](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crosspremv2v.png)

Puede combinar estos elementos juntos para crear una red de tránsito más compleja y de saltos múltiples, que satisfaga sus necesidades.

## <a name="a-name-enablebgpapart-1---configure-bgp-on-the-azure-vpn-gateway"></a><a name ="enablebgp"></a>Parte 1: Configurar BGP en la puerta de enlace de VPN de Azure
Los siguientes pasos de configuración le permitirán configurar los parámetros BGP de la puerta de enlace de VPN como se muestra en el diagrama siguiente:

![Puerta de enlace de BGP](./media/vpn-gateway-bgp-resource-manager-ps/bgp-gateway.png)

### <a name="before-you-begin"></a>Antes de empezar
* Compruebe que tiene una suscripción a Azure. Si todavía no la tiene, puede activar sus [ventajas como suscriptor de MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) o registrarse para obtener una [cuenta gratuita](https://azure.microsoft.com/pricing/free-trial/).
* Necesitará instalar los cmdlets de PowerShell del Administrador de recursos de Azure. Consulte [Cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md) para más información sobre cómo instalar los cmdlets de PowerShell.

### <a name="step-1---create-and-configure-vnet1"></a>Paso 1: Creación y configuración de VNet1
#### <a name="1-declare-your-variables"></a>1. Declaración de las variables
Para este ejercicio, comenzaremos declarando las variables. En el ejemplo siguiente, se declaran las variables con los valores para este ejercicio. Asegúrese de reemplazar los valores por los suyos propios cuando realice la configuración para el entorno de producción. Puede usar estas variables si está practicando los pasos para familiarizarse con este tipo de configuración. Modifique las variables y después copie y pegue todo en la consola de PowerShell.

    $Sub1          = "Replace_With_Your_Subcription_Name"
    $RG1           = "TestBGPRG1"
    $Location1     = "East US"
    $VNetName1     = "TestVNet1"
    $FESubName1    = "FrontEnd"
    $BESubName1    = "Backend"
    $GWSubName1    = "GatewaySubnet"
    $VNetPrefix11  = "10.11.0.0/16"
    $VNetPrefix12  = "10.12.0.0/16"
    $FESubPrefix1  = "10.11.0.0/24"
    $BESubPrefix1  = "10.12.0.0/24"
    $GWSubPrefix1  = "10.12.255.0/27"
    $VNet1ASN      = 65010
    $DNS1          = "8.8.8.8"
    $GWName1       = "VNet1GW"
    $GWIPName1     = "VNet1GWIP"
    $GWIPconfName1 = "gwipconf1"
    $Connection12  = "VNet1toVNet2"
    $Connection15  = "VNet1toSite5"

#### <a name="2-connect-to-your-subscription-and-create-a-new-resource-group"></a>2. Conexión a su suscripción y creación de un nuevo grupo de recursos
Asegúrese de cambiar el modo de PowerShell para que use los cmdlets del Administrador de recursos. Para obtener más información, consulte [Uso de Windows PowerShell con el Administrador de recursos](../powershell-azure-resource-manager.md).

Abre la consola de PowerShell y conéctate a tu cuenta. Use el siguiente ejemplo para ayudarle a conectarse:

    Login-AzureRmAccount
    Select-AzureRmSubscription -SubscriptionName $Sub1
    New-AzureRmResourceGroup -Name $RG1 -Location $Location1

#### <a name="3-create-testvnet1"></a>3. Creación de TestVNet1
En el ejemplo siguiente se crea una red virtual denominada TestVNet1 y tres subredes llamadas: GatewaySubnet, FrontEnd y Backend. Al reemplazar valores, es importante que siempre asigne el nombre GatewaySubnet a la subred de la puerta de enlace. Si usa otro, se producirá un error al crear la puerta de enlace. 

    $fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
    $besub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
    $gwsub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

    New-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1

### <a name="step-2---create-the-vpn-gateway-for-testvnet1-with-bgp-parameters"></a>Paso 2: Creación de la puerta de enlace de VPN para TestVNet1 con parámetros BGP
#### <a name="1-create-the-ip-and-subnet-configurations"></a>1. Cree las configuraciones de IP y de subred
Solicite que se asigne una dirección IP pública a la puerta de enlace que creará para la red virtual. También definirá las configuraciones de subred y de IP necesarias. 

    $gwpip1    = New-AzureRmPublicIpAddress -Name $GWIPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic

    $vnet1     = Get-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
    $subnet1   = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
    $gwipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName1 -Subnet $subnet1 -PublicIpAddress $gwpip1

#### <a name="2-create-the-vpn-gateway-with-the-as-number"></a>2. Cree la puerta de enlace de VPN con el número de AS
Cree la puerta de enlace de red virtual para TestVNet1. Tenga en cuenta que BGP requiere una puerta de enlace de VPN basada en una ruta y también el parámetro de suma, - Asn, para establecer el ASN (el número de AS) para TestVNet1. Se tardan unos 30 minutos o algo más en crear una puerta de enlace.

    New-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gwipconf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku HighPerformance -Asn $VNet1ASN

#### <a name="3-obtain-the-azure-bgp-peer-ip-address"></a>3. Obtenga la dirección IP del par BGP de Azure
Una vez creada la puerta de enlace, debe obtener la dirección IP del par BGP en la puerta de enlace de VPN de Azure. Esta dirección es necesaria para configurar la puerta de enlace de VPN de Azure como par BGP para los dispositivos de VPN local.

    $vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
    $vnet1gw.BgpSettingsText

El último comando mostrará las configuraciones de BGP correspondientes en la puerta de enlace de VPN de Azure; por ejemplo:

    $vnet1gw.BgpSettingsText
    {
        "Asn": 65010,
        "BgpPeeringAddress": "10.12.255.30",
        "PeerWeight": 0
    }

Una vez creada la puerta de enlace, puede usar esta puerta de enlace para establecer conexión entre locales o conexión de red virtual a red virtual con BGP. Las siguientes secciones le guiarán por los pasos necesarios para completar el ejercicio.

## <a name="a-name-crossprembbgpapart-2---establish-a-cross-premises-connection-with-bgp"></a><a name ="crossprembbgp"></a>Parte 2: Establecer una conexión entre locales con BGP
Para establecer una conexión entre locales, debe crear una puerta de enlace de red local para representar el dispositivo VPN local y una conexión para conectarse a la puerta de enlace de VPN de Azure con la puerta de enlace de red local. La diferencia entre las instrucciones de este artículo estriba en las propiedades adicionales necesarias para especificar los parámetros de configuración de BGP.

![BGP entre locales](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crossprem.png)

Antes de continuar, asegúrese de que ha completado la [parte 1](#enablebgp) de este ejercicio.

### <a name="step-1---create-and-configure-the-local-network-gateway"></a>Paso 1: Cree y configure la puerta de enlace de red local
#### <a name="1-declare-your-variables"></a>1. Declaración de las variables
Este ejercicio continuará generando la configuración mostrada en el diagrama. Asegúrese de reemplazar los valores por los que desea usar para su configuración.

    $RG5           = "TestBGPRG5"
    $Location5     = "East US 2"
    $LNGName5      = "Site5"
    $LNGPrefix50   = "10.52.255.254/32"
    $LNGIP5        = "Your_VPN_Device_IP"
    $LNGASN5       = 65050
    $BGPPeerIP5    = "10.52.255.254"

Un par de cosas a tener en cuenta con respecto a los parámetros de la puerta de enlace de red local:

* La puerta de enlace de red local puede estar en la misma ubicación y grupo de recursos que la puerta de enlace de VPN o en otros distintos. Este ejemplo los muestra en distintos grupos de recursos en diferentes ubicaciones.
* El prefijo mínimo que debe declarar para la puerta de enlace de red local es la dirección del host de la dirección IP del par BGP en el dispositivo VPN. En este caso, es un prefijo /32 de "10.52.255.254/32".
* Como recordatorio, debe usar diferentes ASN de BGP entre las redes locales y la red virtual de Azure. Si son iguales, debe cambiar el ASN de la red virtual si el dispositivo VPN local ya utiliza el ASN para estar al mismo nivel que otros vecinos de BGP.

Antes de continuar, asegúrese de que sigue conectado a la suscripción 1.

#### <a name="2-create-the-local-network-gateway-for-site5"></a>2. Cree las puertas de enlace de red local para Site5
Asegúrese de crear el grupo de recursos si no está ya creado, antes de crear la puerta de enlace de red local. Observe los dos parámetros adicionales para la puerta de enlace de red local: Asn y BgpPeerAddress.

    New-AzureRmResourceGroup -Name $RG5 -Location $Location5

    New-AzureRmLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG5 -Location $Location5 -GatewayIpAddress $LNGIP5 -AddressPrefix $LNGPrefix50 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP5

### <a name="step-2---connect-the-vnet-gateway-and-local-network-gateway"></a>Paso 2: Conecte la puerta de enlace de red virtual y la puerta de enlace de red local
#### <a name="1-get-the-two-gateways"></a>1. Obtenga las dos puertas de enlace
        $vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
        $lng5gw  = Get-AzureRmLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG5

#### <a name="2-create-the-testvnet1-to-site5-connection"></a>2. Cree la conexión de TestVNet1 a Site5
En este paso, creará la conexión de TestVNet1 a Site5. Debe especificar "-EnableBGP True" para habilitar BGP para esta conexión. Como se explicó anteriormente, es posible tener conexiones BGP y no BGP para la misma puerta de enlace de VPN de Azure. A menos que BGP esté habilitado en la propiedad de conexión, Azure no habilitará BGP para esta conexión, aunque los parámetros BGP ya estén configurados en ambas puertas de enlace.

    New-AzureRmVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $True


En el ejemplo siguiente se enumeran los parámetros que deberá especificar en la sección de configuración de BGP en el dispositivo VPN local para este ejercicio:

    - ASN de Site5            : 65050
    - IP de BGP Site5: 10.52.255.254
    - Prefijos para anunciar: (por ejemplo), 10.51.0.0/16 y 10.52.0.0/16
    - ASN de red virtual de Azure: 65010
    - Dirección IP de BGP de red virtual de Azure: 10.12.255.30
    - Ruta estática: agregue una ruta para 10.12.255.30/32, siendo el próximo salto la interfaz de túnel VPN de su dispositivo
    - Salto múltiple de eBGP        : asegúrese de que la opción de "salto múltiple" eBGP está habilitada en el dispositivo, si es necesario

La conexión debe establecerse después de unos minutos y se iniciará la sesión de emparejamiento BGP una vez establecida la conexión IPsec.

## <a name="a-name-v2vbgpapart-3---establish-a-vnet-to-vnet-connection-with-bgp"></a><a name ="v2vbgp"></a>Parte 3: Establecer una conexión de red virtual a red virtual con BGP
En esta sección se agrega una conexión de red virtual a red virtual con BGP, tal como se muestra en el diagrama siguiente. 

![BGP para conexiones de red virtual a red virtual](./media/vpn-gateway-bgp-resource-manager-ps/bgp-vnet2vnet.png)

Las instrucciones que siguen son continuación de los pasos anteriores ya explicados. Debe completar la [Parte 1](#enablebgp) para crear y configurar TestVNet1 y la puerta de enlace de VPN con BGP. 

### <a name="step-1---create-testvnet2-and-the-vpn-gateway"></a>Paso 1: cree TestVNet2 y la puerta de enlace de VPN
Es importante asegurarse de que el espacio de direcciones IP de la red virtual nueva, TestVNet2, no se solape con ninguno de sus intervalos de red virtual.

En este ejemplo, las redes virtuales pertenecen a la misma suscripción. Puede configurar las conexiones entre dos redes virtuales entre distintas suscripciones. Para más información, vea [Configuración de una conexión de red virtual a red virtual para Resource Manager mediante PowerShell](vpn-gateway-vnet-vnet-rm-ps.md). Asegúrese de agregar "-EnableBgp True" al crear las conexiones para habilitar BGP.

#### <a name="1-declare-your-variables"></a>1. Declaración de las variables
Asegúrese de reemplazar los valores por los que desea usar para su configuración.

    $RG2           = "TestBGPRG2"
    $Location2     = "West US"
    $VNetName2     = "TestVNet2"
    $FESubName2    = "FrontEnd"
    $BESubName2    = "Backend"
    $GWSubName2    = "GatewaySubnet"
    $VNetPrefix21  = "10.21.0.0/16"
    $VNetPrefix22  = "10.22.0.0/16"
    $FESubPrefix2  = "10.21.0.0/24"
    $BESubPrefix2  = "10.22.0.0/24"
    $GWSubPrefix2  = "10.22.255.0/27"
    $VNet2ASN      = 65020
    $DNS2          = "8.8.8.8"
    $GWName2       = "VNet2GW"
    $GWIPName2     = "VNet2GWIP"
    $GWIPconfName2 = "gwipconf2"
    $Connection21  = "VNet2toVNet1"
    $Connection12  = "VNet1toVNet2"

#### <a name="2-create-testvnet2-in-the-new-resource-group"></a>2. Cree TestVNet2 en el nuevo grupo de recursos
    New-AzureRmResourceGroup -Name $RG2 -Location $Location2

    $fesub2 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName2 -AddressPrefix $FESubPrefix2
    $besub2 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName2 -AddressPrefix $BESubPrefix2
    $gwsub2 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName2 -AddressPrefix $GWSubPrefix2

    New-AzureRmVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2 -Location $Location2 -AddressPrefix $VNetPrefix21,$VNetPrefix22 -Subnet $fesub2,$besub2,$gwsub2

#### <a name="3-create-the-vpn-gateway-for-testvnet2-with-bgp-parameters"></a>3. Cree la puerta de enlace de VPN para TestVNet2 con parámetros BGP
Solicite que se asigne una dirección IP pública a la puerta de enlace que creará para la red virtual. También definirá las configuraciones de subred y de IP necesarias. 

    $gwpip2    = New-AzureRmPublicIpAddress -Name $GWIPName2 -ResourceGroupName $RG2 -Location $Location2 -AllocationMethod Dynamic

    $vnet2     = Get-AzureRmVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2
    $subnet2   = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet2
    $gwipconf2 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName2 -Subnet $subnet2 -PublicIpAddress $gwpip2

Cree la puerta de enlace de VPN con el número de AS. Tenga en cuenta que debe reemplazar el valor predeterminado del ASN en las puertas de enlace de VPN de Azure. Los ASN para las redes virtuales conectadas deben ser diferentes para habilitar el enrutamiento de tránsito y de BGP.

    New-AzureRmVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2 -Location $Location2 -IpConfigurations $gwipconf2 -GatewayType Vpn -VpnType RouteBased -GatewaySku Standard -Asn $VNet2ASN

### <a name="step-2---connect-the-testvnet1-and-testvnet2-gateways"></a>Paso 2: Conecte las puertas de enlace de TestVNet1 y TestVNet2
En este ejemplo, ambas puertas de enlace están en la misma suscripción. Puede completar este paso en la misma sesión de PowerShell.

#### <a name="1-get-both-gateways"></a>1. Obtenga ambas puertas de enlace
Asegúrese de iniciar sesión y conectarse a la suscripción 1.

    $vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
    $vnet2gw = Get-AzureRmVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2

#### <a name="2-create-both-connections"></a>2. Cree ambas conexiones
En este paso, creará la conexión de TestVNet1 a TestVNet2 y viceversa.

    New-AzureRmVirtualNetworkGatewayConnection -Name $Connection12 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet2gw -Location $Location1 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3' -EnableBgp $True

    New-AzureRmVirtualNetworkGatewayConnection -Name $Connection21 -ResourceGroupName $RG2 -VirtualNetworkGateway1 $vnet2gw -VirtualNetworkGateway2 $vnet1gw -Location $Location2 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3' -EnableBgp $True

> [!IMPORTANT]
> Asegúrese de habilitar BGP para AMBAS conexiones.
> 
> 

Después de completar estos pasos, la conexión se establecerá en unos minutos y la sesión de emparejamiento de BGP se iniciará una vez se complete la conexión de red virtual a red virtual.

Si ha completado las tres partes de este ejercicio, habrá establecido una topología de red como se muestra a continuación:

![BGP para conexiones de red virtual a red virtual](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crosspremv2v.png)

## <a name="next-steps"></a>Pasos siguientes
Una vez completada la conexión, puede agregar máquinas virtuales a las redes virtuales. Consulte [Creación de una máquina virtual que ejecuta Windows en el Portal de Azure](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) para ver los pasos.




<!--HONumber=Nov16_HO3-->


