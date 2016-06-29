<properties
   pageTitle="Información general de BGP con puertas de enlace de VPN de Azure | Microsoft Azure"
   description="Este artículo proporciona información general de BGP con puertas de enlace de VPN de Azure."
   services="vpn-gateway"
   documentationCenter="na"
   authors="yushwang"
   manager="rossort"
   editor=""
   tags=""/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="06/16/2016"
   ms.author="yushwang"/>

# Información general de BGP con puertas de enlace de VPN de Azure

Este artículo proporciona información general de compatibilidad de BGP (Border Gateway Protocol) en las puertas de enlace de VPN de Azure.

## Información acerca de BGP

BGP es el protocolo de enrutamiento estándar usado habitualmente en Internet para intercambiar información de enrutamiento y disponibilidad entre dos o más redes. Cuando se utiliza en el contexto de redes virtuales de Azure, BGP permite que las puertas de enlace de VPN de Azure y los dispositivos VPN locales, denominados vecinos o pares BGP, intercambien "rutas" que comunicarán a ambas puertas de enlace la disponibilidad y la posibilidad de que dichos prefijos pasen a través de las puertas de enlace o los enrutadores implicados. BGP también puede permitir el enrutamiento del tránsito entre varias redes mediante la propagación de las rutas que una puerta de enlace de BGP aprende de un par BGP a todos los demás pares BGP.
 
### ¿Por qué usar BGP?

BGP es una característica opcional que puede utilizar con puertas de enlace de VPN basadas en enrutamiento de Azure. También debe asegurarse de que los dispositivos VPN locales admiten BGP antes de habilitar la característica. Puede seguir usando las puertas de enlace de VPN de Azure y los dispositivos VPN locales sin BGP. Es el equivalente a utilizar rutas estáticas (sin BGP) *frente a* usar el enrutamiento dinámico con BGP entre sus redes y Azure.

Hay varias ventajas y nuevas funcionalidades con BGP:

#### Uso de actualizaciones de prefijo flexible y automáticas

Con BGP, solo necesita declarar un prefijo mínimo para un par BGP específico a través del túnel VPN S2S de IPsec. Puede ser tan pequeño como un prefijo de host (/32) de la dirección IP del par BGP del dispositivo VPN local. Puede controlar que prefijos de red local desea anunciar a Azure para permitir el acceso de la red virtual de Azure.
	
También puede anunciar prefijos mayores que pueden incluir algunos de los prefijos de dirección de red virtual, como un espacio de direcciones IP privado grande (por ejemplo, 10.0.0.0/8). Sin embargo, tenga en cuenta que los prefijos no pueden ser idénticos a uno de sus prefijos de red virtual. Se rechazarán dichas rutas idénticas a sus prefijos de red virtual.

>[AZURE.IMPORTANT] Actualmente, el anuncio de la ruta predeterminada (0.0.0.0/0) para puertas de enlace de VPN de Azure estará bloqueado. Cuando esté habilitada esta funcionalidad, se proporcionarán más actualizaciones.

#### Uso de varios túneles entre una red virtual y un sitio local con la conmutación por error automática basada en BGP

Puede establecer varias conexiones entre la red virtual de Azure y los dispositivos VPN locales en la misma ubicación. Esta funcionalidad proporciona varios túneles (rutas) entre las dos redes en una configuración activa-activa. Si uno de los túneles está desconectado, se retiran las rutas correspondientes a través de BGP y el tráfico se cambiará automáticamente a los túneles restantes.
	
El siguiente diagrama muestra un ejemplo sencillo de esta configuración de alta disponibilidad:
	
![Varias rutas de acceso activas](./media/vpn-gateway-bgp-overview/multiple-active-tunnels.png)

#### Uso del enrutamiento de tránsito entre las redes locales y varias redes virtuales de Azure

BGP permite que varias puertas de enlace obtengan y propaguen los prefijos de diferentes redes, si están directa o indirectamente conectadas. Esto puede permitir el enrutamiento de tránsito con puertas de enlace de VPN de Azure entre sitios locales o en varias redes virtuales de Azure.
	
El siguiente diagrama muestra un ejemplo de una topología de múltiples saltos con varias rutas que pueden enrutar el tráfico entre las dos redes locales a través de las puertas de enlace de VPN de Azure en las redes de Microsoft:

![Tránsito de múltiples saltos](./media/vpn-gateway-bgp-overview/full-mesh-transit.png)

## Preguntas más frecuentes de BGP


[AZURE.INCLUDE [vpn-gateway-bgp-faq-include](../../includes/vpn-gateway-bpg-faq-include.md)]




## Pasos siguientes

Consulte [Cómo configurar BGP en puertas de enlace de VPN de Azure mediante Azure Resource Manager y PowerShell](./vpn-gateway-bgp-resource-manager-ps.md) para conocer los pasos necesarios para configurar BGP en las conexiones entre sistemas locales y de red virtual a red virtual.

<!---HONumber=AcomDC_0622_2016-->