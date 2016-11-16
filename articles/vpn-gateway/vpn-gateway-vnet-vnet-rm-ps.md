---
title: "Conexión de redes virtuales de Azure con VPN Gateway y PowerShell | Microsoft Docs"
description: "Este artículo le guiará para conectar redes virtuales entre sí por medio de PowerShell y el Administrador de recursos de Azure."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: carmonm
editor: 
tags: azure-resource-manager
ms.assetid: 0683c664-9c03-40a4-b198-a6529bf1ce8b
ms.service: vpn-gateway
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/31/2016
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 636606f5f5f651c10d174854de8471b5dd060dce


---
# <a name="configure-a-vnettovnet-connection-for-resource-manager-using-powershell"></a>Configuración de una conexión de red virtual a red virtual para Resource Manager mediante PowerShell
> [!div class="op_single_selector"]
> * [Resource Manager - Azure Portal](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [Resource Manager - PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)
> * [Clásico - Portal clásico](virtual-networks-configure-vnet-to-vnet-connection.md)
> 
> 

Este artículo le guiará a través de los pasos necesarios para crear una conexión entre redes virtuales con el modelo de implementación de Resource Manager mediante Puerta de enlace de VPN. Las redes virtuales pueden estar en la misma región o en distintas, así como pertenecer a una única suscripción o a varias.

![diagrama de v2v](./media/vpn-gateway-vnet-vnet-rm-ps/v2vrmps.png)

### <a name="deployment-models-and-methods-for-vnettovnet-connections"></a>Modelos de implementación y métodos para conexiones de red virtual a red virtual
[!INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)]

La siguiente tabla muestra los modelos de implementación disponibles actualmente y los métodos para las configuraciones de red virtual a red virtual. Cuando aparezca algún artículo con pasos de configuración, creamos un vínculo directo a él desde esta tabla.

[!INCLUDE [vpn-gateway-table-vnet-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)]

#### <a name="vnet-peering"></a>Emparejamiento de VNET
[!INCLUDE [vpn-gateway-vnetpeeringlink](../../includes/vpn-gateway-vnetpeeringlink-include.md)]

## <a name="about-vnettovnet-connections"></a>Acerca de conexiones de red virtual a red virtual
La conexión de una red virtual a otra es muy parecida a la conexión de una red virtual a una ubicación de un sitio local. Ambos tipos de conectividad usan una puerta de enlace de VPN de Azure para proporcionar un túnel seguro con IPsec/IKE. Las redes virtuales que se conecten pueden estar en regiones distintas. O en distintas suscripciones. Incluso puede combinar la comunicación de red virtual a red virtual con configuraciones de varios sitios. Esto permite establecer topologías de red que combinan la conectividad entre entornos locales con la conectividad entre redes virtuales, como se muestra en el diagrama siguiente:

![Acerca de las conexiones](./media/vpn-gateway-vnet-vnet-rm-ps/aboutconnections.png)

### <a name="why-connect-virtual-networks"></a>¿Por qué debería conectarse a redes virtuales?
Puede que desee conectar redes virtuales por las siguientes razones:

* **Presencia geográfica y redundancia geográfica entre regiones**
  
  * Puede configurar su propia replicación geográfica o sincronización con conectividad segura sin recurrir a los puntos de conexión a Internet.
  * Con el Equilibrador de carga y el Administrador de tráfico de Azure, puede configurar cargas de trabajo de alta disponibilidad con redundancia geográfica en varias regiones de Azure. Por ejemplo, puede configurar AlwaysOn de SQL con grupos de disponibilidad distribuidos en varias regiones de Azure.
* **Aplicaciones regionales de niveles múltiples con aislamiento o un límite administrativo**
  
  * Dentro de la misma región, se pueden configurar aplicaciones de niveles múltiples con varias redes virtuales conectadas entre sí para cumplir requisitos administrativos o de aislamiento.

### <a name="vnettovnet-faq"></a>P+F sobre conexiones de red virtual a red virtual
[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-vnet-vnet-faq-include.md)]

## <a name="which-set-of-steps-should-i-use"></a>¿Qué serie de pasos debo seguir?
En este artículo, verá dos conjuntos de pasos diferentes. Un conjunto de pasos para [redes virtuales que residen en la misma suscripción](#samesub) y otro para [redes virtuales que residen en suscripciones diferentes](#difsub). La diferencia clave entre ambos conjuntos es si se pueden crear y configurar todos los recursos de red virtual y puerta de enlace en la misma sesión de PowerShell.

Los pasos de este artículo utilizan variables que se declaran al principio de cada sección. Si ya trabaja con redes virtuales existentes, modifique las variables para reflejar la configuración de su propio entorno. 

![Ambas conexiones](./media/vpn-gateway-vnet-vnet-rm-ps/differentsubscription.png)

## <a name="a-namesamesubahow-to-connect-vnets-that-are-in-the-same-subscription"></a><a name="samesub"></a>Conexión de redes virtuales que están en la misma suscripción
![diagrama de v2v](./media/vpn-gateway-vnet-vnet-rm-ps/v2vrmps.png)

### <a name="before-you-begin"></a>Antes de empezar
Antes de empezar, necesitará instalar los cmdlets de PowerShell de Azure Resource Manager. Consulte [Cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md) para más información sobre cómo instalar los cmdlets de PowerShell.

### <a name="a-namestep1astep-1-plan-your-ip-address-ranges"></a><a name="Step1"></a>Paso 1: Planeamiento de los intervalos de direcciones IP
En los pasos siguientes, se crearán dos redes virtuales junto con sus subredes de puerta de enlace y configuraciones correspondientes. A continuación crearemos una conexión VPN entre las dos redes virtuales. Es importante planear los intervalos de direcciones IP para la configuración de red. Tenga en cuenta que hay que asegurarse de que ninguno de los intervalos de VNet o intervalos de red local se solapen.

En los ejemplos usamos los siguientes valores:

**Valores para TestVNet1:**

* Nombre de red virtual: TestVNet1
* Grupo de recursos: TestRG1
* Ubicación: Este de EE. UU.
* TestVNet1: 10.11.0.0/16 y 10.12.0.0/16
* FrontEnd: 10.11.0.0/24
* Backend: 10.12.0.0/24
* GatewaySubnet: 10.12.255.0/27
* Servidor DNS: 8.8.8.8
* GatewayName: VNet1GW
* Dirección IP pública: VNet1GWIP
* VPNType: RouteBased
* Connection(1to4): VNet1toVNet4
* Connection(1to5): VNet1toVNet5
* ConnectionType: VNet2VNet

**Valores para TestVNet4:**

* Nombre de red virtual: TestVNet4
* TestVNet2: 10.41.0.0/16 y 10.42.0.0/16
* FrontEnd: 10.41.0.0/24
* Backend: 10.42.0.0/24
* GatewaySubnet: 10.42.255.0/27
* Grupo de recursos: TestRG4
* Ubicación: Oeste de EE. UU.
* Servidor DNS: 8.8.8.8
* GatewayName: VNet4GW
* Dirección IP pública: VNet4GWIP
* VPNType: RouteBased
* Conexión: VNet4toVNet1
* ConnectionType: VNet2VNet

### <a name="a-namestep2astep-2-create-and-configure-testvnet1"></a><a name="Step2"></a>Paso 2: Creación y configuración de TestVNet1
1. Declaración de las variables
   
    Comience por declarar las variables. En este ejemplo se declaran las variables con los valores para este ejercicio. En la mayoría de los casos, deberá reemplazar los valores por los suyos propios. No obstante, puede usar estas variables si está practicando los pasos para familiarizarse con este tipo de configuración. Si es necesario, modifique las variables y después cópielas y péguelas en la consola de PowerShell.
   
        $Sub1 = "Replace_With_Your_Subcription_Name"
        $RG1 = "TestRG1"
        $Location1 = "East US"
        $VNetName1 = "TestVNet1"
        $FESubName1 = "FrontEnd"
        $BESubName1 = "Backend"
        $GWSubName1 = "GatewaySubnet"
        $VNetPrefix11 = "10.11.0.0/16"
        $VNetPrefix12 = "10.12.0.0/16"
        $FESubPrefix1 = "10.11.0.0/24"
        $BESubPrefix1 = "10.12.0.0/24"
        $GWSubPrefix1 = "10.12.255.0/27"
        $DNS1 = "8.8.8.8"
        $GWName1 = "VNet1GW"
        $GWIPName1 = "VNet1GWIP"
        $GWIPconfName1 = "gwipconf1"
        $Connection14 = "VNet1toVNet4"
        $Connection15 = "VNet1toVNet5"
2. su suscripción
   
    Cambie el modo de PowerShell para que use los cmdlets de Resource Manager. Abre la consola de PowerShell y conéctate a tu cuenta. Use el siguiente ejemplo para conectarse:
   
        Login-AzureRmAccount
   
    Compruebe las suscripciones para la cuenta.
   
        Get-AzureRmSubscription 
   
    Especifique la suscripción que desea usar.
   
        Select-AzureRmSubscription -SubscriptionName $Sub1
3. Creación de un nuevo grupo de recursos
   
        New-AzureRmResourceGroup -Name $RG1 -Location $Location1
4. Creación de las configuraciones de subred para TestVNet1
   
    En este ejemplo se crea una red virtual denominada TestVNet1 y tres subredes llamadas: GatewaySubnet, FrontEnd y Backend. Al reemplazar valores, es importante que siempre asigne el nombre GatewaySubnet a la subred de la puerta de enlace. Si usa otro, se producirá un error al crear la puerta de enlace. 
   
    El siguiente ejemplo usa las variables que estableció anteriormente. En este ejemplo, la subred de la puerta de enlace está usando un /27. Aunque es posible crear una subred de puerta de enlace tan pequeña como /29, se recomienda que cree una subred mayor que incluya más direcciones seleccionando al menos /28 o /27. Esto permitirá suficientes direcciones para dar cabida a posibles configuraciones adicionales que desee en el futuro. 
   
        $fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
        $besub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
        $gwsub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1
5. Creación de TestVNet1
   
        New-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 `
        -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1
6. Solicitar una dirección IP pública
   
    Solicite que se asigne una dirección IP pública a la puerta de enlace que creará para la red virtual. Observe que AllocationMethod es dinámico. No puede especificar la dirección IP que desea usar. Se asigna dinámicamente a la puerta de enlace. 
   
        $gwpip1 = New-AzureRmPublicIpAddress -Name $GWIPName1 -ResourceGroupName $RG1 `
        -Location $Location1 -AllocationMethod Dynamic
7. Establecer la configuración de la puerta de enlace
   
    La configuración de puerta de enlace define la subred y la dirección IP pública. Use el ejemplo para crear la configuración de la puerta de enlace. 
   
        $vnet1 = Get-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
        $subnet1 = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
        $gwipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName1 `
        -Subnet $subnet1 -PublicIpAddress $gwpip1
8. Creación de la puerta de enlace para TestVNet1
   
    En este paso, creará la puerta de enlace de red virtual para TestVNet1. Las configuraciones VNet a VNet requieren un VpnType RouteBased. Se tardan unos 45 minutos o algo más en crear una puerta de enlace.
   
        New-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 `
        -Location $Location1 -IpConfigurations $gwipconf1 -GatewayType Vpn `
        -VpnType RouteBased -GatewaySku Standard

### <a name="step-3-create-and-configure-testvnet4"></a>Paso 3: Creación y configuración de TestVNet4
Una vez que haya configurado TestVNet1, cree TestVNet4. Siga los pasos a continuación y reemplace los valores por los suyos propios cuando sea necesario. Este paso puede realizarse en la misma sesión de PowerShell porque está en la misma suscripción.

1. Declaración de las variables
   
    Asegúrese de reemplazar los valores por los que desea usar para su configuración.
   
        $RG4 = "TestRG4"
        $Location4 = "West US"
        $VnetName4 = "TestVNet4"
        $FESubName4 = "FrontEnd"
        $BESubName4 = "Backend"
        $GWSubName4 = "GatewaySubnet"
        $VnetPrefix41 = "10.41.0.0/16"
        $VnetPrefix42 = "10.42.0.0/16"
        $FESubPrefix4 = "10.41.0.0/24"
        $BESubPrefix4 = "10.42.0.0/24"
        $GWSubPrefix4 = "10.42.255.0/27"
        $DNS4 = "8.8.8.8"
        $GWName4 = "VNet4GW"
        $GWIPName4 = "VNet4GWIP"
        $GWIPconfName4 = "gwipconf4"
        $Connection41 = "VNet4toVNet1"
   
    Antes de continuar, asegúrese de que sigue conectado a la suscripción 1.
2. Creación de un nuevo grupo de recursos
   
        New-AzureRmResourceGroup -Name $RG4 -Location $Location4
3. Creación de las configuraciones de subred para TestVNet4
   
        $fesub4 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName4 -AddressPrefix $FESubPrefix4
        $besub4 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName4 -AddressPrefix $BESubPrefix4
        $gwsub4 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName4 -AddressPrefix $GWSubPrefix4
4. Creación de TestVNet4
   
        New-AzureRmVirtualNetwork -Name $VnetName4 -ResourceGroupName $RG4 `
        -Location $Location4 -AddressPrefix $VnetPrefix41,$VnetPrefix42 -Subnet $fesub4,$besub4,$gwsub4
5. Solicitar una dirección IP pública
   
        $gwpip4 = New-AzureRmPublicIpAddress -Name $GWIPName4 -ResourceGroupName $RG4 `
        -Location $Location4 -AllocationMethod Dynamic
6. Establecer la configuración de la puerta de enlace
   
        $vnet4 = Get-AzureRmVirtualNetwork -Name $VnetName4 -ResourceGroupName $RG4
        $subnet4 = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet4
        $gwipconf4 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName4 -Subnet $subnet4 -PublicIpAddress $gwpip4
7. Creación de la puerta de enlace de TestVNet4
   
        New-AzureRmVirtualNetworkGateway -Name $GWName4 -ResourceGroupName $RG4 `
        -Location $Location4 -IpConfigurations $gwipconf4 -GatewayType Vpn `
        -VpnType RouteBased -GatewaySku Standard

### <a name="step-4-connect-the-gateways"></a>Paso 4: Conexión de las puertas de enlace
1. Obtención de ambas puertas de enlace de red virtual
   
    En este ejemplo, como ambas puertas de enlace están en la misma suscripción, el paso puede realizarse en la misma sesión de PowerShell.
   
        $vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
        $vnet4gw = Get-AzureRmVirtualNetworkGateway -Name $GWName4 -ResourceGroupName $RG4
2. Creación de la conexión de TestVNet1 a TestVNet4
   
    En este paso, creará la conexión de TestVNet1 a TestVNet4. Verá una clave compartida a la que se hace referencia en los ejemplos. Puede utilizar sus propios valores para la clave compartida. Lo importante es que la clave compartida coincida en ambas conexiones. Se tardará unos momentos en terminar de crear la conexión.
   
        New-AzureRmVirtualNetworkGatewayConnection -Name $Connection14 -ResourceGroupName $RG1 `
        -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet4gw -Location $Location1 `
        -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'
3. Creación de la conexión de TestVNet4 a TestVNet1
   
    Este paso es similar al anterior, salvo en que se crea la conexión de TestVNet4 a TestVNet1. Asegúrese de que coincidan las claves compartidas.
   
        New-AzureRmVirtualNetworkGatewayConnection -Name $Connection41 -ResourceGroupName $RG4 `
        -VirtualNetworkGateway1 $vnet4gw -VirtualNetworkGateway2 $vnet1gw -Location $Location4 `
        -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'
   
    Después de unos minutos, la conexión debería haberse establecido.
4. Compruebe la conexión. Consulte la sección [Comprobación de la conexión](#verify).

## <a name="a-namedifsubahow-to-connect-vnets-that-are-in-different-subscriptions"></a><a name="difsub"></a>Conexión de redes virtuales que están en suscripciones diferentes
![diagrama de v2v](./media/vpn-gateway-vnet-vnet-rm-ps/v2vdiffsub.png)

En este escenario, conectaremos TestVNet1 y TestVNet5. TestVNet1 y TestVNet5 residen en suscripciones diferentes. Los pasos para esta configuración permiten agregar una conexión de red virtual a red virtual adicional para poder conectar TestVNet1 a TestVNet5. 

La diferencia es que parte de los pasos de configuración se deben realizar en una sesión de PowerShell distinta en el contexto de la segunda suscripción. Especialmente cuando las dos suscripciones pertenecen a distintas organizaciones. 

Las instrucciones que siguen son continuación de los pasos anteriores ya explicados. Tiene que completar el [paso 1](#Step1) y el [paso 2](#Step2) para crear y configurar TestVNet1 y la puerta de enlace de VPN para TestVNet1. Cuando haya completado el paso 1 y el paso 2, continúe con el paso 5 para crear TestVNet5.

### <a name="step-5-verify-the-additional-ip-address-ranges"></a>Paso 5: Comprobación de los intervalos de direcciones IP adicionales
Es importante asegurarse de que el espacio de direcciones IP de la red virtual nueva, TestVNet5, no se solape con ninguno de los intervalos de red virtual o de puerta de enlace de red local. 

En este ejemplo, las redes virtuales pueden pertenecer a distintas organizaciones. En este ejercicio, use los siguientes valores para TestVNet5:

**Valores para TestVNet5:**

* Nombre de red virtual: TestVNet5
* Grupo de recursos: TestRG5
* Ubicación: Japón Oriental
* TestVNet5: 10.51.0.0/16 y 10.52.0.0/16
* FrontEnd: 10.51.0.0/24
* Backend: 10.52.0.0/24
* GatewaySubnet: 10.52.255.0.0/27
* Servidor DNS: 8.8.8.8
* GatewayName: VNet5GW
* Dirección IP pública: VNet5GWIP
* VPNType: RouteBased
* Conexión: VNet5toVNet1
* ConnectionType: VNet2VNet

**Valores adicionales para TestVNet1:**

* Conexión: VNet1toVNet5

### <a name="step-6-create-and-configure-testvnet5"></a>Paso 6: Creación y configuración de TestVNet5
Este paso debe realizarse en el contexto de la nueva suscripción. Es posible que esta parte la realice el administrador de otra organización que posea la suscripción.

1. Declaración de las variables
   
    Asegúrese de reemplazar los valores por los que desea usar para su configuración.
   
        $Sub5 = "Replace_With_the_New_Subcription_Name"
        $RG5 = "TestRG5"
        $Location5 = "Japan East"
        $VnetName5 = "TestVNet5"
        $FESubName5 = "FrontEnd"
        $BESubName5 = "Backend"
        $GWSubName5 = "GatewaySubnet"
        $VnetPrefix51 = "10.51.0.0/16"
        $VnetPrefix52 = "10.52.0.0/16"
        $FESubPrefix5 = "10.51.0.0/24"
        $BESubPrefix5 = "10.52.0.0/24"
        $GWSubPrefix5 = "10.52.255.0/27"
        $DNS5 = "8.8.8.8"
        $GWName5 = "VNet5GW"
        $GWIPName5 = "VNet5GWIP"
        $GWIPconfName5 = "gwipconf5"
        $Connection51 = "VNet5toVNet1"
2. Conexión con la suscripción 5
   
    Abre la consola de PowerShell y conéctate a tu cuenta. Use el siguiente ejemplo para ayudarle a conectarse:
   
        Login-AzureRmAccount
   
    Compruebe las suscripciones para la cuenta.
   
        Get-AzureRmSubscription 
   
    Especifique la suscripción que desea usar.
   
        Select-AzureRmSubscription -SubscriptionName $Sub5
3. Creación de un nuevo grupo de recursos
   
        New-AzureRmResourceGroup -Name $RG5 -Location $Location5
4. Creación de las configuraciones de subred para TestVNet4
   
        $fesub5 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName5 -AddressPrefix $FESubPrefix5
        $besub5 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName5 -AddressPrefix $BESubPrefix5
        $gwsub5 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName5 -AddressPrefix $GWSubPrefix5
5. Creación de TestVNet5
   
        New-AzureRmVirtualNetwork -Name $VnetName5 -ResourceGroupName $RG5 -Location $Location5 `
        -AddressPrefix $VnetPrefix51,$VnetPrefix52 -Subnet $fesub5,$besub5,$gwsub5
6. Solicitar una dirección IP pública
   
        $gwpip5 = New-AzureRmPublicIpAddress -Name $GWIPName5 -ResourceGroupName $RG5 `
        -Location $Location5 -AllocationMethod Dynamic
7. Establecer la configuración de la puerta de enlace
   
        $vnet5 = Get-AzureRmVirtualNetwork -Name $VnetName5 -ResourceGroupName $RG5
        $subnet5  = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet5
        $gwipconf5 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName5 -Subnet $subnet5 -PublicIpAddress $gwpip5
8. Creación de la puerta de enlace de TestVNet5
   
        New-AzureRmVirtualNetworkGateway -Name $GWName5 -ResourceGroupName $RG5 -Location $Location5 `
        -IpConfigurations $gwipconf5 -GatewayType Vpn -VpnType RouteBased -GatewaySku Standard

### <a name="step-7-connecting-the-gateways"></a>Paso 7: Conexión de las puertas de enlace
En este ejemplo, como las puertas de enlace están en suscripciones diferentes, hemos dividido el paso en dos sesiones de PowerShell marcadas como [Suscripción 1] y [Suscripción 5].

1. **[Suscripción 1]** Obtención de la puerta de enlace de red virtual para la suscripción 1
   
    Asegúrese de iniciar sesión y conectarse a la suscripción 1.
   
        $vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
   
    Copie la salida de los siguientes elementos y envíesela al administrador de la suscripción 5 por correo electrónico u otro método.
   
        $vnet1gw.Name
        $vnet1gw.Id
   
    Estos dos elementos tendrán valores similares a la salida de ejemplo siguiente:
   
        PS D:\> $vnet1gw.Name
        VNet1GW
        PS D:\> $vnet1gw.Id
        /subscriptions/b636ca99-6f88-4df4-a7c3-2f8dc4545509/resourceGroupsTestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW
2. **[Suscripción 5]** Obtención de la puerta de enlace de red virtual para la suscripción 5
   
    Asegúrese de iniciar sesión y conectarse a la suscripción 5.
   
        $vnet5gw = Get-AzureRmVirtualNetworkGateway -Name $GWName5 -ResourceGroupName $RG5
   
    Copie la salida de los siguientes elementos y envíesela al administrador de la suscripción 1 por correo electrónico u otro método.
   
        $vnet5gw.Name
        $vnet5gw.Id
   
    Estos dos elementos tendrán valores similares a la salida de ejemplo siguiente:
   
        PS C:\> $vnet5gw.Name
        VNet5GW
        PS C:\> $vnet5gw.Id
        /subscriptions/66c8e4f1-ecd6-47ed-9de7-7e530de23994/resourceGroups/TestRG5/providers/Microsoft.Network/virtualNetworkGateways/VNet5GW
3. **[Suscripción 1]** Creación de la conexión entre TestVNet1 y TestVNet5
   
    En este paso, creará la conexión de TestVNet1 a TestVNet5. La diferencia en este caso es que no se puede obtener $vnet5gw directamente porque está en una suscripción diferente. Debe crear un nuevo objeto de PowerShell con los valores que se le hayan proporcionado para la suscripción 1 en los pasos anteriores. Use el ejemplo siguiente. Reemplace el nombre (Name), el identificador (Id) y la clave compartida por sus propios valores. Lo importante es que la clave compartida coincida en ambas conexiones. Se tardará unos momentos en terminar de crear la conexión.
   
    Asegúrese de conectarse a la suscripción 1. 
   
        $vnet5gw = New-Object Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
        $vnet5gw.Name = "VNet5GW"
        $vnet5gw.Id   = "/subscriptions/66c8e4f1-ecd6-47ed-9de7-7e530de23994/resourceGroups/TestRG5/providers/Microsoft.Network/virtualNetworkGateways/VNet5GW"
        $Connection15 = "VNet1toVNet5"
        New-AzureRmVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet5gw -Location $Location1 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'
4. **[Suscripción 5]** Creación de la conexión entre TestVNet5 y TestVNet1
   
    Este paso es similar al anterior, salvo en que se crea la conexión de TestVNet5 a TestVNet1. Aquí también se aplica el mismo proceso de creación de un objeto de PowerShell basándose en los valores obtenidos de la suscripción 1. En este paso, asegúrese de que las claves compartidas coincidan.
   
    Asegúrese de conectarse a la suscripción 5.
   
        $vnet1gw = New-Object Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
        $vnet1gw.Name = "VNet1GW"
        $vnet1gw.Id = "/subscriptions/b636ca99-6f88-4df4-a7c3-2f8dc4545509/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW "
        New-AzureRmVirtualNetworkGatewayConnection -Name $Connection51 -ResourceGroupName $RG5 -VirtualNetworkGateway1 $vnet5gw -VirtualNetworkGateway2 $vnet1gw -Location $Location5 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'

## <a name="a-nameverifyahow-to-verify-a-connection"></a><a name="verify"></a>Comprobación de una conexión
[!INCLUDE [vpn-gateway-no-nsg-include](../../includes/vpn-gateway-no-nsg-include.md)]

[!INCLUDE [verify connection powershell](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

## <a name="next-steps"></a>Pasos siguientes
* Una vez completada la conexión, puede agregar máquinas virtuales a las redes virtuales. Consulte [Creación de una máquina virtual que ejecuta Windows en el Portal de Azure](../virtual-machines/virtual-machines-windows-hero-tutorial.md) para ver los pasos.
* Para más información acerca de BGP, consulte [Información general de BGP](vpn-gateway-bgp-overview.md) y [Configuración de BGP](vpn-gateway-bgp-resource-manager-ps.md). 




<!--HONumber=Nov16_HO2-->


