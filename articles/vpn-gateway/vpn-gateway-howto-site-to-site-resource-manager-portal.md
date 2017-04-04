---
title: "Conexión de la red local a una red virtual de Azure: VPN de sitio a sitio: Portal | Microsoft Docs"
description: "Pasos para crear una conexión de IPsec desde la red local a una red virtual de Azure a través de la red pública de Internet. Estos pasos le ayudarán a crear una conexión de VPN Gateway de sitio a sitio entre locales mediante el portal."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 827a4db7-7fa5-4eaf-b7e1-e1518c51c815
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/23/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 80939bb48c29ba39e2d347cb80d6169d79329cfc
ms.lasthandoff: 03/25/2017


---
# <a name="create-a-site-to-site-connection-in-the-azure-portal"></a>Creación de una conexión de sitio a sitio mediante Azure Portal
> [!div class="op_single_selector"]
> * [Resource Manager - Azure Portal](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
> * [Resource Manager - PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
> * [Clásico: Azure Portal](vpn-gateway-howto-site-to-site-classic-portal.md)
> * [Clásico - Portal clásico](vpn-gateway-site-to-site-create.md)
>
>


Una conexión de puerta de enlace de VPN de sitio a sitio (S2S) es una conexión a través de un túnel VPN IPsec/IKE (IKEv1 o IKEv2). Este tipo de conexión requiere un dispositivo VPN local que tenga una dirección IP pública asignada y que no se encuentre detrás de NAT. Se pueden utilizar conexiones de sitio a sitio para las configuraciones híbridas y entre locales.

Este artículo lo guía por la creación de una red virtual y una conexión VPN Gateway de sitio a sitio a una red local mediante el modelo de implementación de Azure Resource Manager y el Azure Portal. 

![Diagrama de la conexión entre locales de VPN Gateway de sitio a sitio](./media/vpn-gateway-howto-site-to-site-resource-manager-portal/site-to-site-diagram.png)

### <a name="deployment-models-and-methods-for-site-to-site-connections"></a>Modelos y métodos de implementación para las conexiones de sitio a sitio
[!INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)]

La siguiente tabla muestra los modelos y métodos de implementación disponibles actualmente para las configuraciones de sitio a sitio. Cuando aparezca algún artículo con pasos de configuración, creamos un vínculo directo a él desde esta tabla.

[!INCLUDE [site-to-site table](../../includes/vpn-gateway-table-site-to-site-include.md)]

#### <a name="additional-configurations"></a>Configuraciones adicionales
Si desea conectar las redes virtuales entre sí pero no está creando una conexión a una ubicación local, consulte [Configurar una conexión de red virtual a red virtual en el Portal de Azure clásico](vpn-gateway-vnet-vnet-rm-ps.md). Si desea añadir una conexión de sitio a sitio a una red virtual que ya tiene una conexión, consulte [Adición de una conexión S2S a VNet con una conexión de puerta de enlace existente](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md).

## <a name="before-you-begin"></a>Antes de empezar
Antes de comenzar con la configuración, compruebe que dispone de los elementos siguientes:

* Un dispositivo VPN compatible y alguien que pueda configurarlo. Consulte [Acerca de los dispositivos VPN para conexiones de red virtual de sitio a sitio](vpn-gateway-about-vpn-devices.md).
* Si no está familiarizado con los espacios de direcciones IP ubicados en la red local, necesita trabajar con alguien que pueda proporcionarle estos detalles. Las conexiones de sitio a sitio no pueden tener espacios de direcciones superpuestos.
* Una dirección IP pública externa para el dispositivo VPN. Esta dirección IP no puede estar detrás de un NAT.
* Una suscripción de Azure. Si todavía no la tiene, puede activar sus [ventajas como suscriptor de MSDN](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) o registrarse para obtener una [cuenta gratuita](http://azure.microsoft.com/pricing/free-trial).

### <a name="values"></a>Valores del ejemplo
Al usar estos pasos como un ejercicio, puede utilizar los siguientes valores de ejemplo:

* **Nombre de red virtual:** TestVNet1
* **Espacio de direcciones:** 
    * 10.11.0.0/16
    * 10.12.0.0/16 (opcional para este ejercicio)
* **Subredes:**
  * FrontEnd: 10.11.0.0/24
  * BackEnd: 10.12.0.0/24 (opcional para este ejercicio)
  * GatewaySubnet: 10.11.255.0/27
* **Grupo de recursos:** TestRG1
* **Ubicación:** este de EE. UU.
* **Servidor DNS**: la dirección IP del servidor DNS
* **Nombre de la puerta de enlace de red virtual:** VNet1GW
* **Dirección IP pública:** VNet1GWIP
* **Tipo de VPN:** basada en rutas
* **Tipo de conexión:** de sitio a sitio (IPsec)
* **Tipo de puerta de enlace:** VPN
* **Nombre de la puerta de enlace de red local:** Site2
* **Nombre de conexión:** VNet1toSite2

## <a name="CreatVNet"></a>1. Crear una red virtual

[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-basic-vnet-s2s-rm-portal-include.md)]

## <a name="dns"></a>2. Especificación de un servidor DNS
No se requiere la DNS para las conexiones de sitio a sitio. Sin embargo, si desea tener resolución de nombres para los recursos que se implementan en su red virtual, debe especificar un servidor DNS. Esta configuración le permite especificar el servidor DNS que desea usar para la resolución de nombres para esta red virtual. No crea un servidor DNS.

[!INCLUDE [vpn-gateway-add-dns-rm-portal](../../includes/vpn-gateway-add-dns-rm-portal-include.md)]

## <a name="gatewaysubnet"></a>3. Creación de una subred de puerta de enlace
Debe crear una subred de puerta de enlace para la puerta de enlace VPN. La subred de puerta de enlace contiene las direcciones IP que usarán los servicios de puerta de enlace de la red virtual. Si es posible, cree una subred de puerta de enlace con un bloque CIDR de /28 o /27. Esto garantizará que tiene suficientes direcciones IP para adaptarse a futuros requisitos de configuración de puerta de enlace.

[!INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-s2s-rm-portal-include.md)]

## <a name="VNetGateway"></a>4. Creación de una puerta de enlace de red virtual

[!INCLUDE [vpn-gateway-add-gw-s2s-rm-portal](../../includes/vpn-gateway-add-gw-s2s-rm-portal-include.md)]

## <a name="LocalNetworkGateway"></a>5. Creación de una puerta de enlace de red local
La puerta de enlace de red local hace referencia a la ubicación local. La configuración que especifique para la puerta de enlace de red local determina los espacios de direcciones que se enrutan al dispositivo VPN local.

[!INCLUDE [vpn-gateway-add-lng-s2s-rm-portal](../../includes/vpn-gateway-add-lng-s2s-rm-portal-include.md)]

## <a name="VPNDevice"></a>6. Configurar el dispositivo VPN
[!INCLUDE [vpn-gateway-configure-vpn-device-rm](../../includes/vpn-gateway-configure-vpn-device-rm-include.md)]

## <a name="CreateConnection"></a>7. Creación de una conexión VPN de sitio a sitio.

En este paso, creará la conexión VPN de sitio a sitio entre la puerta de enlace de red virtual y el dispositivo VPN local. Antes de comenzar esta sección, compruebe que la puerta de enlace de red virtual y las puertas de enlace de red local se han terminado de crear.

[!INCLUDE [vpn-gateway-add-site-to-site-connection-rm-portal](../../includes/vpn-gateway-add-site-to-site-connection-s2s-rm-portal-include.md)]

## <a name="VerifyConnection"></a>8. Comprobación de la conexión VPN

[!INCLUDE [Azure portal](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]

## <a name="next-steps"></a>Pasos siguientes
*  Una vez completada la conexión, puede agregar máquinas virtuales a las redes virtuales. Consulte [Virtual Machines](https://docs.microsoft.com/azure/#pivot=services&panel=Compute) para más información.
*  Para más información acerca de BGP, consulte [Información general de BGP](vpn-gateway-bgp-overview.md) y [Configuración de BGP](vpn-gateway-bgp-resource-manager-ps.md).


