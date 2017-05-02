---
title: "Conexión de la red local a una red virtual de Azure: VPN de sitio a sitio: CLI | Microsoft Docs"
description: "Pasos para crear una conexión de IPsec desde la red local a una red virtual de Azure a través de la red pública de Internet. Estos pasos le ayudarán a crear una conexión de VPN Gateway de sitio a sitio entre locales mediante la CLI."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/21/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 1cc1ee946d8eb2214fd05701b495bbce6d471a49
ms.openlocfilehash: c3563f3a3fa46d40ba02fe97b3b0ebe3c45caddd
ms.lasthandoff: 04/26/2017


---
# <a name="create-a-virtual-network-with-a-site-to-site-vpn-connection-using-cli"></a>Creación de una red virtual con una conexión VPN de sitio a sitio mediante la CLI

Este artículo muestra cómo utilizar la CLI de Azure para crear una conexión de puerta de enlace VPN de sitio a sitio desde la red local a la red virtual. Los pasos descritos en este artículo se aplican al modelo de implementación de Resource Manager. También se puede crear esta configuración con una herramienta o modelo de implementación distintos, mediante la selección de una opción diferente en la lista siguiente:

> [!div class="op_single_selector"]
> * [Resource Manager - Azure Portal](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
> * [Resource Manager - PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
> * [Resource Manager - CLI](vpn-gateway-howto-site-to-site-resource-manager-cli.md)
> * [Clásico - Azure Portal](vpn-gateway-howto-site-to-site-classic-portal.md)
> * [Clásico - Portal clásico](vpn-gateway-site-to-site-create.md)
> 
>


![Diagrama de la conexión entre locales de VPN Gateway de sitio a sitio](./media/vpn-gateway-howto-site-to-site-resource-manager-cli/site-to-site-connection-diagram.png)

Se utiliza una conexión de puerta de enlace VPN de sitio a sitio para conectar su red local a una red virtual de Azure a través de un túnel VPN de IPsec/IKE (IKEv1 o IKEv2). Este tipo de conexión requiere un dispositivo VPN local que tenga una dirección IP pública asignada. Para más información acerca de las puertas de enlace VPN, consulte [Acerca de VPN Gateway](vpn-gateway-about-vpngateways.md).

## <a name="before-you-begin"></a>Antes de empezar

Antes de comenzar con la configuración, compruebe que se cumplen los criterios siguientes:

* Compruebe que desea trabajar con el modelo de implementación de Resource Manager. [!INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)] 
* Un dispositivo VPN compatible y alguien que pueda configurarlo. Para más información acerca de los dispositivos VPN compatibles y su configuración, consulte [Acerca de los dispositivos VPN](vpn-gateway-about-vpn-devices.md).
* Una dirección IPv4 pública externa para el dispositivo VPN. Esta dirección IP no puede estar detrás de un NAT.
* Si no está familiarizado con los intervalos de direcciones IP ubicados en la red local, necesita trabajar con alguien que pueda proporcionarle estos detalles. Al crear esta configuración, debe especificar los prefijos del intervalo de direcciones IP al que Azure enrutará la ubicación local. Ninguna de las subredes de la red local puede superponerse con las subredes de la red virtual a la que desea conectarse. 
* La versión más reciente de los comandos de la CLI (2.0 o posteriores). Para más información sobre la instalación de los comandos de la CLI, consulte [Instalación de la CLI de Azure 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli).

### <a name="example-values"></a>Valores de ejemplo

Puede usar los siguientes valores para crear un entorno de prueba o hacer referencia a ellos para comprender mejor los ejemplos de este artículo:

```
#Example values

VnetName                = TestVNet1 
ResourceGroup           = TestRG1 
Location                = eastus 
AddressSpace            = 10.12.0.0/16 
SubnetName              = Subnet1 
Subnet                  = 10.12.0.0/24 
GatewaySubnet           = 10.12.255.0/27 
LocalNetworkGatewayName = Site2 
LNG Public IP           = <VPN device IP address>
LocalAddrPrefix1        = 10.0.0.0/24 
LocalAddrPrefix2        = 20.0.0.0/24   
GatewayName             = VNet1GW 
PublicIP                = VNet1GWIP 
VPNType                 = RouteBased 
GatewayType             = Vpn 
ConnectionName          = VNet1toSite2
```

## <a name="Login"></a>1. Inicie sesión en Azure.

Inicie sesión en la suscripción de Azure con el comando [az login](/cli/azure/#login) y siga las instrucciones de la pantalla.

```azurecli
az login
```

Si tiene más de una suscripción de Azure, enumere las suscripciones de la cuenta.

```azurecli
Az account list --all
```

Especifique la suscripción que desea usar.

```azurecli
Az account set --subscription <replace_with_your_subscription_id>
```

## <a name="2-create-a-resource-group"></a>2. Crear un grupo de recursos

En el ejemplo siguiente se crea un grupo de recursos con el nombre 'TestRG1' en la ubicación 'eastus'. Si ya tiene un grupo de recursos en la región que desea crear la red virtual, puede utilizarlo.

```azurecli
az group create -n TestRG1 -l eastus
```

## <a name="VNet"></a>3. Crear una red virtual

Si no tiene una red virtual, créela. Al crear una red virtual, compruebe que los espacios de direcciones especificados no se superponen con los espacios de direcciones que existen en la red local. 

El siguiente ejemplo crea una red virtual denominada 'TestVNet1' y una subred llamada 'Subnet1'.

```azurecli
az network vnet create -n TestVNet1 -g TestRG1 --address-prefix 10.12.0.0/16 -l eastus --subnet-name Subnet1 --subnet-prefix 10.12.0.0/24
```

## 4. <a name="gwsub"></a>Creación de la subred de la puerta de enlace

[!INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

Para esta configuración, también es necesaria una subred de la puerta de enlace. La puerta de enlace de la red virtual usa una subred de la puerta de enlace que contiene las direcciones IP que usan los servicios de la puerta de enlace VPN. Cuando se crea una subred de la puerta de enlace, debe tener el nombre 'GatewaySubnet'. Si le asigna otro nombre, crea una subred, pero Azure no la tratará como subred de puerta de enlace.

El tamaño de la subred de la puerta de enlace que especifique depende de la configuración de la puerta de enlace VPN que desea crear. Aunque es posible crear una subred de puerta de enlace tan pequeña como /29, se recomienda que cree una subred mayor que incluya más direcciones seleccionando al menos /27 o /28. El uso de una subred de la puerta de enlace mayor permite suficientes direcciones IP para dar cabida a posibles configuraciones futuras.


```azurecli
az network vnet subnet create --address-prefix 10.12.255.0/27 -n GatewaySubnet -g TestRG1 --vnet-name TestVNet1
```

## <a name="localnet"></a>5. Creación de la puerta de enlace de red local

La puerta de enlace de red local suele hacer referencia a la ubicación local. Asigne al sitio un nombre al que Azure pueda hacer referencia y, luego, especifique la dirección IP del dispositivo VPN local con la que creará una conexión. Especifique también los prefijos de dirección IP que se enrutarán a través de la puerta de enlace VPN al dispositivo VPN. Los prefijos de dirección que especifique son los prefijos que se encuentran en la red local. Puede actualizar fácilmente estos prefijos si cambia su red local.

Use los valores siguientes:

* *--gateway-ip-address* es la dirección IP del dispositivo VPN local. El dispositivo VPN no se encuentra detrás de un NAT.
* *--local-address-prefixes* son los espacios de direcciones locales.

En el ejemplo siguiente se muestra cómo agregar una puerta de enlace de red local con varios prefijos de direcciones:

```azurecli
az network local-gateway create --gateway-ip-address 23.99.221.164 -n Site2 -g TestRG1 --local-address-prefixes 10.0.0.0/24 20.0.0.0/24
```

## <a name="PublicIP"></a>6. Solicitar una dirección IP pública

Solicite una dirección IP pública que se asignará a la puerta de enlace VPN de la red virtual. Es la dirección IP que configurará para que el dispositivo VPN se conecte a ella.

La puerta de enlace de red virtual para el modelo de implementación de Resource Manager actualmente solo admite direcciones IP públicas mediante el método de asignación dinámica. Sin embargo, esto no significa que la dirección IP vaya a cambiar. La única vez que cambia la dirección IP de la puerta de enlace de VPN es cuando se elimina y se vuelve a crear la puerta de enlace. La dirección IP pública de la puerta de enlace de la red virtual no cambia aunque se cambie el tamaño, se restablezca o se lleven a cabo operaciones de actualización o mantenimiento interno en ella. 

```azurecli
az network public-ip create -n VNet1GWIP -g TestRG1 --allocation-method Dynamic
```

## <a name="CreateGateway"></a>7. Creación de la puerta de enlace VPN

Cree la puerta de enlace VPN de la red virtual. Crear una puerta de enlace VPN puede tardar 45 minutos o más en completarse.

Use los valores siguientes:

* El valor *---gateway-type* para una configuración de sitio a sitio es *Vpn*. El tipo de puerta de enlace siempre es específico de la configuración que se va a implementar. Para más información, consulte [Tipos de puerta de enlace](vpn-gateway-about-vpn-gateway-settings.md#gwtype)
* El valor de *--vpn-type* puede ser *RouteBased* (denominada puerta de enlace dinámica en algunos documentos) o *PolicyBased* (denominada puerta de enlace estática en algunos documentos). La configuración es específica según los requisitos del dispositivo al que se va a conectar. Para más información sobre los tipos de puertas de enlace de VPN, consulte [Acerca de la información de VPN Gateway](vpn-gateway-about-vpn-gateway-settings.md#vpntype).
* El valor de *--sku* puede ser Basic, Standard, o HighPerformance. Hay limitaciones de configuración para determinadas SKU. Consulte [SKU de puertas de enlace](vpn-gateway-about-vpngateways.md#gateway-skus) para más información.

Después de ejecutar este comando, no verá ningún comentario ni salida. Tarda aproximadamente 45 minutos en crear una puerta de enlace.

```azurecli
az network vnet-gateway create -n VNet1GW --public-ip-address VNet1GWIP -g TestRG1 --vnet TestVNet1 --gateway-type Vpn --vpn-type RouteBased --sku Standard --no-wait 
```

## <a name="VPNDevice"></a>8. Configurar el dispositivo VPN

[!INCLUDE [vpn-gateway-configure-vpn-device-rm](../../includes/vpn-gateway-configure-vpn-device-rm-include.md)]
 Para buscar la dirección IP púbica de la puerta de enlace de la red virtual, utilice el ejemplo siguiente reemplazando los valores por los suyos propios. Para facilitar la lectura, la salida muestra la lista de direcciones IP públicas en formato de tabla.

  ```azurecli
  az network public-ip list -g TestRG1 -o table
  ```

## <a name="CreateConnection"></a>9. Creación de la conexión VPN

Creación de la conexión VPN de sitio a sitio entre la puerta de enlace de la red virtual y el dispositivo VPN local. Preste especial atención al valor de la clave compartida, que debe coincidir con el valor de la clave compartida configurada para el dispositivo VPN.

```azurecli
az network vpn-connection create -n VNet1toSite2 -g TestRG1 --vnet-gateway1 VNet1GW -l eastus --shared-key abc123 --local-gateway2 Site2
```

Pasado un momento, se establecerá la conexión.

## <a name="toverify"></a>10. Comprobación de la conexión VPN

[!INCLUDE [verify connection](../../includes/vpn-gateway-verify-connection-cli-rm-include.md)] 

Si desea usar otro método para comprobar la conexión, consulte [Comprobación de una conexión de VPN Gateway](vpn-gateway-verify-connection-resource-manager.md).

## <a name="common-tasks"></a>Tareas comunes

### <a name="to-view-local-network-gateways"></a>Para ver las puertas de enlace de la red local

```azurecli
az network local-gateway list --resource-group TestRG1
```

### <a name="modify"></a>Para modificar los prefijos de dirección IP de una puerta de enlace de red local
Si tiene que cambiar los prefijos de la puerta de enlace de red local, siga estas instrucciones. Cada vez que se realiza un cambio, debe especificarse toda la lista de prefijos, no solo los prefijos que se desea cambiar.

- **Si ha especificado una conexión**, use el ejemplo siguiente. Especifique toda la lista de prefijos que se compone de prefijos existentes y los elementos que desee agregar. En este ejemplo, 10.0.0.0/24 y 20.0.0.0/24 ya están presentes. Agregamos los prefijos 30.0.0.0/24 y 40.0.0.0/24.

  ```azurecli
  az network local-gateway update --local-address-prefixes 10.0.0.0/24 20.0.0.0/24 30.0.0.0/24 40.0.0.0/24 -n VNet1toSite2 -g TestRG1
  ```

- **Si no ha especificado una conexión**, use el mismo comando que utiliza para crear una puerta de enlace de la red local. También puede usar este comando para actualizar la dirección IP de la puerta de enlace para el dispositivo VPN. Solo puede usar este comando si aún no tiene una conexión. En este ejemplo, 10.0.0.0/24, 20.0.0.0/24, 30.0.0.0/24 y 40.0.0.0/24 están presentes. Especificamos solo los prefijos que se van a conservar. En este caso, 10.0.0.0/24 y 20.0.0.0/24.

  ```azurecli
  az network local-gateway create --gateway-ip-address 23.99.221.164 -n Site2 -g TestRG1 --local-address-prefixes 10.0.0.0/24 20.0.0.0/24
  ```

### <a name="modifygwipaddress"></a>Para modificar la dirección IP de una puerta de enlace de red local

La dirección IP en esta configuración es la dirección IP del dispositivo VPN con el que se va a crear una conexión. Si cambia la dirección IP del dispositivo VPN, puede modificar el valor. Incluso si hay una conexión de puerta de enlace, se puede cambiar la dirección IP.

```azurecli
az network local-gateway update --gateway-ip-address 23.99.222.170 -n Site2 -g TestRG1
```

Al ver el resultado, compruebe que se incluyan los prefijos de las direcciones IP.

  ```azurecli
  "localNetworkAddressSpace": { 
    "addressPrefixes": [ 
      "10.0.0.0/24", 
      "20.0.0.0/24", 
      "30.0.0.0/24" 
    ] 
  }, 
  "location": "eastus", 
  "name": "Site2", 
  "provisioningState": "Succeeded",  
  ```

### <a name="to-view-the-virtual-network-gateway-public-ip-address"></a>Para ver la dirección IP pública de la puerta de enlace de la red virtual

Para buscar la dirección IP pública de la puerta de enlace de la red virtual, use el siguiente ejemplo. Para facilitar la lectura, la salida muestra la lista de direcciones IP públicas en formato de tabla.

```azurecli
az network public-ip list -g TestRG1 -o table
```

### <a name="to-verify-the-shared-key-values"></a>Para comprobar los valores de la clave compartida

Compruebe que el valor de la clave compartida es el mismo valor que utilizó para la configuración del dispositivo VPN. Si no es así, ejecute de nuevo la conexión con el valor del dispositivo, o actualice el dispositivo con el valor devuelto. Los valores deben coincidir.

```azurecli
az network vpn-connection shared-key show --connection-name VNet1toSite2 -g TestRG1
```

## <a name="next-steps"></a>Pasos siguientes

*  Una vez completada la conexión, puede agregar máquinas virtuales a las redes virtuales. Consulte [Virtual Machines](https://docs.microsoft.com/azure/#pivot=services&panel=Compute) para más información.
* Para más información acerca de BGP, consulte [Información general de BGP](vpn-gateway-bgp-overview.md) y [Configuración de BGP](vpn-gateway-bgp-resource-manager-ps.md).
* Para más información acerca de la tunelización forzada, consulte [Configuración de la tunelización forzada](vpn-gateway-forced-tunneling-rm.md).
* Para obtener una lista de comandos de red de la CLI de Azure, consulte [Networking - az network](https://docs.microsoft.com/cli/azure/network) (Redes: az network).
