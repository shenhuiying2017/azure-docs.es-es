---
title: "Creación de una red virtual con una conexión VPN de sitio a sitio mediante Azure Resource Manager y Azure Portal | Microsoft Docs"
description: "Cómo crear una red virtual con el modelo de implementación de Resource Manager y conectarla a la red local mediante una conexión de puerta de enlace de VPN de sitio a sitio."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: carmonm
editor: 
tags: azure-resource-manager
ms.assetid: 827a4db7-7fa5-4eaf-b7e1-e1518c51c815
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/14/2016
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: d269d9a76ff4ccd973eee70d2d5b54a7262383ef
ms.openlocfilehash: f0491df77418c4d7c79beff87302b64ddc3fa9be


---
# <a name="create-a-vnet-with-a-site-to-site-connection-using-the-azure-portal"></a>Creación de una red virtual con una conexión de sitio a sitio mediante el Portal de Azure
> [!div class="op_single_selector"]
> * [Resource Manager - Azure Portal](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
> * [Resource Manager - PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
> * [Clásico - Portal clásico](vpn-gateway-site-to-site-create.md)
> 
> 

Este artículo lo guía por la creación de una red virtual y una conexión VPN Gateway de sitio a sitio a una red local mediante el modelo de implementación de Azure Resource Manager y el Azure Portal. Se pueden utilizar conexiones de sitio a sitio para las configuraciones híbridas y entre locales.

![Diagrama](./media/vpn-gateway-howto-site-to-site-resource-manager-portal/s2srmportal.png)

### <a name="deployment-models-and-methods-for-site-to-site-connections"></a>Modelos y métodos de implementación para las conexiones de sitio a sitio
[!INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)]

La siguiente tabla muestra los modelos y métodos de implementación disponibles actualmente para las configuraciones de sitio a sitio. Cuando aparezca algún artículo con pasos de configuración, creamos un vínculo directo a él desde esta tabla.

[!INCLUDE [site-to-site table](../../includes/vpn-gateway-table-site-to-site-include.md)]

#### <a name="additional-configurations"></a>Configuraciones adicionales
Si desea conectar las redes virtuales entre sí pero no está creando una conexión a una ubicación local, consulte [Configurar una conexión de red virtual a red virtual en el Portal de Azure clásico](vpn-gateway-vnet-vnet-rm-ps.md). Si desea añadir una conexión de sitio a sitio a una red virtual que ya tiene una conexión, consulte [Adición de una conexión S2S a VNet con una conexión de puerta de enlace existente](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md).

## <a name="before-you-begin"></a>Antes de empezar
Antes de comenzar con la configuración, compruebe que dispone de los elementos siguientes:

* Un dispositivo VPN compatible y alguien que pueda configurarlo. Consulte [Acerca de los dispositivos VPN para conexiones de red virtual de sitio a sitio](vpn-gateway-about-vpn-devices.md). Si no conoce la configuración de su dispositivo VPN o los intervalos de direcciones IP ubicados en la configuración de la red local, necesita trabajar con alguien que pueda proporcionarle estos detalles.
* Una dirección IP pública externa para el dispositivo VPN. Esta dirección IP no puede estar detrás de un NAT.
* Una suscripción de Azure. Si todavía no la tiene, puede activar sus [ventajas como suscriptor de MSDN](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) o registrarse para obtener una [cuenta gratuita](http://azure.microsoft.com/pricing/free-trial).

### <a name="a-namevaluesasample-configuration-values-for-this-exercise"></a><a name="values"></a>Valores de configuración de ejemplo para este ejercicio
Si utiliza este procedimiento para practicar, puede usar también estos valores de configuración de ejemplo:

* **Nombre de red virtual:** TestVNet1
* **Espacio de direcciones:** 10.11.0.0/16 y 10.12.0.0/16
* **Subredes:**
  * FrontEnd: 10.11.0.0/24
  * Backend: 10.12.0.0/24
  * GatewaySubnet: 10.12.255.0/27
* **Grupo de recursos:** TestRG1
* **Ubicación:** este de EE. UU.
* **Servidor DNS:** 8.8.8.8
* **Nombre de puerta de enlace:** VNet1GW
* **Dirección IP pública:** VNet1GWIP
* **Tipo de VPN:** basada en rutas
* **Tipo de conexión:** de sitio a sitio (IPsec)
* **Tipo de puerta de enlace:** VPN
* **Nombre de la puerta de enlace de red local:** Site2
* **Nombre de conexión:** VNet1toSite2

## <a name="a-namecreatvneta1-create-a-virtual-network"></a><a name="CreatVNet"></a>1. Crear una red virtual
Si ya dispone de una red virtual, compruebe que la configuración sea compatible con el diseño de la puerta de enlace de VPN. Preste especial atención a las subredes que se pueden superponer con otras redes. Si tiene subredes superpuestas, la conexión no funcionará correctamente. Si la red virtual está configurada correctamente, puede comenzar con los pasos descritos en la sección [Especificación de un servidor DNS](#dns) .

### <a name="to-create-a-virtual-network"></a>Creación de una red virtual
[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="a-namesubnetsa2-add-additional-address-space-and-subnets"></a><a name="subnets"></a>2. Incorporación de espacio de direcciones y subredes adicionales
Puede agregar espacio de direcciones y subredes adicionales a la red virtual una vez que se ha creado.

[!INCLUDE [vpn-gateway-additional-address-space](../../includes/vpn-gateway-additional-address-space-include.md)]

## <a name="a-namednsa3-specify-a-dns-server"></a><a name="dns"></a>3. Especificación de un servidor DNS
### <a name="to-specify-a-dns-server"></a>Especificación de un servidor DNS
[!INCLUDE [vpn-gateway-add-dns-rm-portal](../../includes/vpn-gateway-add-dns-rm-portal-include.md)]

## <a name="a-namegatewaysubneta4-create-a-gateway-subnet"></a><a name="gatewaysubnet"></a>4. Creación de una subred de puerta de enlace
Antes de conectar la red virtual a una puerta de enlace, es preciso crear la subred de la puerta de enlace de la red virtual a la que desea conectarse. Si es posible, es preferible crear una subred de puerta de enlace con un bloque CIDR de /28 o /27 para proporcionar suficientes direcciones IP para adaptarse a nuevos requisitos de configuración que puedan surgir en el futuro.

Si va a crear esta configuración como ejercicio, haga referencia a estos [valores](#values) al crear la subred de puerta de enlace.

### <a name="to-create-a-gateway-subnet"></a>Creación de una subred de puerta de enlace
[!INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-rm-portal-include.md)]

## <a name="a-namevnetgatewaya5-create-a-virtual-network-gateway"></a><a name="VNetGateway"></a>5. Creación de una puerta de enlace de red virtual
Si va a crear esta configuración como ejercicio, puede consultar los [valores de configuración ejemplo](#values).

### <a name="to-create-a-virtual-network-gateway"></a>Creación de una puerta de enlace de red virtual
[!INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

## <a name="a-namelocalnetworkgatewaya6-create-a-local-network-gateway"></a><a name="LocalNetworkGateway"></a>6. Creación de una puerta de enlace de red local
La "puerta de enlace de red local" hace referencia a la ubicación local. Asigne a la puerta de enlace de la red local un nombre a través del que Azure pueda hacer referencia a ella. 

Si va a crear esta configuración como ejercicio, puede consultar los [valores de configuración ejemplo](#values).

### <a name="to-create-a-local-network-gateway"></a>Creación de una puerta de enlace de red local
[!INCLUDE [vpn-gateway-add-lng-rm-portal](../../includes/vpn-gateway-add-lng-rm-portal-include.md)]

## <a name="a-namevpndevicea7-configure-your-vpn-device"></a><a name="VPNDevice"></a>7. Configurar el dispositivo VPN
[!INCLUDE [vpn-gateway-configure-vpn-device-rm](../../includes/vpn-gateway-configure-vpn-device-rm-include.md)]

## <a name="a-namecreateconnectiona8-create-a-site-to-site-vpn-connection"></a><a name="CreateConnection"></a>8. Creación de una conexión VPN de sitio a sitio.
Va a crear la conexión VPN de sitio a sitio entre la puerta de enlace de red virtual y el dispositivo VPN. Asegúrese de reemplazar los valores por los suyos. La clave compartida debe coincidir con el valor usado para la configuración del dispositivo VPN. 

Antes de comenzar esta sección, compruebe que la puerta de enlace de red virtual y las puertas de enlace de red local se han terminado de crear. Si va a crear esta configuración como ejercicio, haga referencia a estos [valores](#values) al crear la conexión.

### <a name="to-create-the-vpn-connection"></a>Creación de la conexión VPN
[!INCLUDE [vpn-gateway-add-site-to-site-connection-rm-portal](../../includes/vpn-gateway-add-site-to-site-connection-rm-portal-include.md)]

## <a name="a-nameverifyconnectiona9-verify-the-vpn-connection"></a><a name="VerifyConnection"></a>9. Comprobación de la conexión VPN
Puede comprobar la conexión de VPN en el portal, o bien mediante el uso de PowerShell.

[!INCLUDE [vpn-gateway-verify-connection-rm](../../includes/vpn-gateway-verify-connection-rm-include.md)]

## <a name="next-steps"></a>Pasos siguientes
*  Una vez completada la conexión, puede agregar máquinas virtuales a las redes virtuales. Consulte [Virtual Machines](https://docs.microsoft.com/azure/#pivot=services&panel=Compute) para más información.
*  Para más información acerca de BGP, consulte [Información general de BGP](vpn-gateway-bgp-overview.md) y [Configuración de BGP](vpn-gateway-bgp-resource-manager-ps.md).




<!--HONumber=Nov16_HO4-->


