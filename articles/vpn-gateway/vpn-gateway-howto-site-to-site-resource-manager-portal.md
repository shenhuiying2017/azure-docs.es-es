<properties
   pageTitle="Creación de una red virtual con una conexión VPN de sitio a sitio mediante Azure Resource Manager y el Portal de Azure | Microsoft Azure"
   description="Este artículo le guiará a través de la creación de una red virtual mediante el modelo de Administrador de recursos y su conexión a la red local mediante una conexión de Puerta de enlace de VPN S2S."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/13/2016"
   ms.author="cherylmc"/>

# Creación de una red virtual con una conexión VPN de sitio a sitio mediante el Portal de Azure y Azure Resource Manager

> [AZURE.SELECTOR]
- [Portal de Azure](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
- [Portal de Azure clásico](vpn-gateway-site-to-site-create.md)
- [PowerShell: administrador de recursos](vpn-gateway-create-site-to-site-rm-powershell.md)


Este artículo le guiará a través de la creación de una red virtual y una conexión VPN de sitio a sitio en una red local mediante el modelo de implementación de Azure Resource Manager y el Portal de Azure. En los pasos siguientes, creará una red virtual, agregará una subred de puerta de enlace, una puerta de enlace, un sitio local y una conexión. Además, también necesitará configurar el dispositivo VPN.



**Información sobre los modelos de implementación de Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## Diagrama de conexión

![De sitio a sitio](./media/vpn-gateway-howto-site-to-site-resource-manager-portal/site2site.png)

**Modelos de implementación y herramientas para las conexiones de sitio a sitio**

[AZURE.INCLUDE [vpn-gateway-table-site-to-site-table](../../includes/vpn-gateway-table-site-to-site-include.md)]

Si desea conectar las redes virtuales entre sí pero no está creando una conexión a una ubicación local, consulte [Configurar una conexión de red virtual a red virtual en el Portal de Azure clásico](vpn-gateway-vnet-vnet-rm-ps.md). Si busca un tipo diferente de configuración de la conexión, consulte el artículo [Topologías de conexión de la puerta de enlace de VPN de Azure](vpn-gateway-topology.md).

## Antes de empezar

Antes de comenzar con la configuración, compruebe que dispone de los elementos siguientes:

- Un dispositivo VPN compatible y alguien que pueda configurarlo. Consulte [Acerca de los dispositivos VPN para conexiones de red virtual de sitio a sitio](vpn-gateway-about-vpn-devices.md). Si no estás familiarizado con la configuración de tu dispositivo VPN o con los intervalos de direcciones IP, ubicados en la configuración de la red local, tendrás que trabajar con alguien que pueda proporcionar estos detalles por ti.

- Una dirección IP pública externa para el dispositivo VPN. Esta dirección IP no puede estar detrás de un NAT.
	
- Una suscripción de Azure. Si todavía no la tiene, puede activar sus [ventajas como suscriptor de MSDN](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) o registrarse para obtener una [cuenta gratis](http://azure.microsoft.com/pricing/free-trial/).

### <a name="values"></a>Valores de configuración de ejemplo para este ejercicio


Si utiliza este procedimiento para practicar, puede usar también estos valores de configuración de ejemplo:

- Nombre de red virtual: TestVNet1
- Espacio de direcciones: 10.11.0.0/16 y 10.12.0.0/16
- Subredes: 
	- FrontEnd: 10.11.0.0/24
	- Backend: 10.12.0.0/24
	- GatewaySubnet: 10.12.255.0/27
- Grupo de recursos: TestRG1
- Ubicación: Este de EE. UU.
- Servidor DNS: 8.8.8.8
- Nombre de puerta de enlace: VNet1GW
- Dirección IP pública: VNet1GWIP
- Tipo de VPN: basada en enrutamiento
- Tipo de conexión: de sitio a sitio (IPsec)
- Tipo de puerta de enlace: VPN
- Nombre de la puerta de enlace de red local: Site2
- Nombre de conexión: VNet1toSite2



## 1\. Crear una red virtual 

Si ya ha creado una red virtual, compruebe que la configuración sea compatible con el diseño de la puerta de enlace de VPN, prestando especial atención a las subredes que se pueden superponer con otras redes. Si tiene subredes superpuestas, la conexión no funcionará correctamente. Si ha comprobado que la red virtual está configurada correctamente, puede comenzar con los pasos descritos en la sección [Especificación de un servidor DNS](#dns).

### Creación de una red virtual

[AZURE.INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## 2\. Incorporación de espacio de direcciones y subredes adicionales

Puede agregar espacio de direcciones y subredes adicionales a la red virtual una vez que se ha creado.

[AZURE.INCLUDE [vpn-gateway-additional-address-space](../../includes/vpn-gateway-additional-address-space-include.md)]

## <a name="dns"></a>3. Especificación de un servidor DNS

Si va a crear esta configuración como un ejercicio, haga referencia a estos [valores](#values) cuando especifique el servidor DNS.

### Especificación de un servidor DNS

[AZURE.INCLUDE [vpn-gateway-add-dns-rm-portal](../../includes/vpn-gateway-add-dns-rm-portal-include.md)]

## 4\. Creación de una subred de puerta de enlace

Antes de conectar la red virtual a una puerta de enlace, primero deberá crear la subred de la puerta de enlace de la red virtual a la que desea conectarse. La subred de puerta de enlace que cree se debe denominar *GatewaySubnet* o no funcionará correctamente.

El prefijo de la subred de puerta de enlace para algunas configuraciones requiere una subred de /28 o superior para alojar el número de direcciones IP necesarias en el grupo. Esto significa que el prefijo de la subred de puerta de enlace debe ser /28, /27, /26 etc. Puede que desee crear una subred mayor aquí para dar cabida a posibles adiciones futuras en la configuración.

Si va a crear esta configuración como un ejercicio, haga referencia a estos [valores](#values) al crear la subred de puerta de enlace.

### Creación de una subred de puerta de enlace

[AZURE.INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

[AZURE.INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-rm-portal-include.md)]

## 5\. Creación de una puerta de enlace de red virtual

Si va a crear esta configuración como un ejercicio, haga referencia a estos [valores](#values) al crear la puerta de enlace.

### Creación de una puerta de enlace de red virtual

[AZURE.INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

## 6\. Creación de una puerta de enlace de red local

La *puerta de enlace de red local* hace referencia a la ubicación local. Asigne a la puerta de enlace de la red local un nombre por el que Azure pueda hacer referencia a ella.

Si va a crear esta configuración como un ejercicio, haga referencia a estos [valores](#values) al agregar el sitio local.

### Creación de una puerta de enlace de red local

[AZURE.INCLUDE [vpn-gateway-add-lng-rm-portal](../../includes/vpn-gateway-add-lng-rm-portal-include.md)]

## 7\. Configurar el dispositivo VPN

[AZURE.INCLUDE [vpn-gateway-configure-vpn-device-rm](../../includes/vpn-gateway-configure-vpn-device-rm-include.md)]

## 8\. Creación de una conexión VPN de sitio a sitio.

A continuación, creará la conexión VPN de sitio a sitio entre la puerta de enlace de red virtual y el dispositivo VPN. Asegúrese de reemplazar los valores por los suyos. La clave compartida debe coincidir con el valor usado para la configuración del dispositivo VPN.

Antes de comenzar esta sección, compruebe que la puerta de enlace de red virtual y las puertas de enlace de red local se han terminado de crear. Si va a crear esta configuración como un ejercicio, haga referencia a estos [valores](#values) al crear la conexión.

### Creación de la conexión VPN


[AZURE.INCLUDE [vpn-gateway-add-site-to-site-connection-rm-portal](../../includes/vpn-gateway-add-site-to-site-connection-rm-portal-include.md)]

## 9\. Comprobación de la conexión VPN

Puede comprobar la conexión de VPN en el portal, o bien mediante el uso de PowerShell.

[AZURE.INCLUDE [vpn-gateway-verify-connection-rm](../../includes/vpn-gateway-verify-connection-rm-include.md)]

## Pasos siguientes

- Una vez completada la conexión, puede agregar máquinas virtuales a las redes virtuales. Consulte la [ruta de aprendizaje](https://azure.microsoft.com/documentation/learning-paths/virtual-machines) de las máquinas virtuales para más información.

- Para más información acerca de BGP, consulte [Información acerca de BGP](vpn-gateway-bgp-overview.md) y [Cómo configurar BGP en puertas de enlace de VPN de Azure mediante Azure Resource Manager y PowerShell](vpn-gateway-bgp-resource-manager-ps.md).

<!---HONumber=AcomDC_0525_2016-->