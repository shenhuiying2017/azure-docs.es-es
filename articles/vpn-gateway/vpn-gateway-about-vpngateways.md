<properties 
   pageTitle="Información acerca de las puertas de enlace de VPN| Microsoft Azure"
   description="Obtenga información acerca de las SKU de puertas de enlace de VPN Básica, Estándar y Alto rendimiento y la coexistencia con ExpressRoute, los tipos de enrutamiento de puerta de enlace estático y dinámico y los requisitos de las puertas de enlace para la conectividad de la red virtual."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="07/13/2015"
   ms.author="cherylmc" />

# Información acerca de las puertas de enlace de VPN

Las puertas de enlace de VPN se utilizan para enviar tráfico de red entre redes virtuales y ubicaciones locales, o bien entre varias redes virtuales (VNet a VNet). Al crear una puerta de enlace, hay una serie de factores que es necesario tener en cuenta. Necesitará saber qué SKU de puerta de enlace desea utilizar, el tipo de enrutamiento que se necesita para la configuración (dinámico o estático) y el dispositivo VPN que se va a utilizar si se necesita un dispositivo VPN para la configuración.

## SKU de puerta de enlace
Hay 3 SKU de puerta de enlace de VPN; Básica, Estándar y Alto rendimiento. La tabla siguiente muestra los tipos de puerta de enlace y el rendimiento agregado estimado. Los precios difieren entre las SKU de puerta de enlace. Para obtener información acerca de los precios, consulte [Precios de puertas de enlace de VPN](http://azure.microsoft.com/pricing/details/vpn-gateway/).

| SKU | Puerta de enlace de VPN y ExpressRoute coexisten | Rendimiento de puerta de enlace de ExpressRoute | Rendimiento de puerta de enlace de VPN | Túneles IPsec máx. de puerta de enlace de VPN |
|-------------|-----------------------------------|---------------------------------|------------------------|-------------------------------|
| Básica | No | 500 Mbps | 100 Mbps | 10 |
| Standard | Sí | 1000 Mbps | 100 Mbps | 10 |
| Alto rendimiento | Sí | 2000 Mbps | 200 Mbps | 30 |

**Nota:** el rendimiento de la VPN es una estimación aproximada basada en las medidas entre redes virtuales en la misma región de Azure. No es una garantía de lo que puede obtener para conexiones entre entornos a través de Internet, sino que debe usarse como una medida máxima posible.

## Tipos de puerta de enlace

Hay dos tipos de puerta de enlace, *enrutamiento estático* (también llamada VPN basada en directivas) y *enrutamiento dinámico* (también llamada VPN basada en enrutamiento). Algunas configuraciones solo funcionan con un tipo específico de enrutamiento, mientras que algunos dispositivos VPN solo funcionan con un determinado tipo de distribución. Cuando se crea una puerta de enlace VPN, seleccionará el tipo de puerta de enlace que se requiere para la configuración, asegurándose de que el dispositivo VPN que selecciona también admite ese tipo de enrutamiento.

Por ejemplo, si tiene previsto utilizar una configuración de sitio a sitio simultáneamente con una configuración de punto a sitio, necesitará configurar una puerta de enlace de VPN con enrutamiento dinámico. Si bien es cierto que las configuraciones de sitio a sitio funcionan con puertas de enlace con enrutamiento estático, las configuraciones de punto a sitio requieren una puerta de enlace con enrutamiento dinámico. Dado que ambas conexiones utilizarán la misma puerta de enlace, tendrá que seleccionar el tipo de puerta de enlace que admite ambas configuraciones.

Además, deberá comprobar que el dispositivo VPN es compatible con el tipo de puerta de enlace y la configuración y los parámetros de IPsec/IKE que necesita. Por ejemplo, si desea crear una puerta de enlace dinámica y el dispositivo VPN no es compatible con las VPN basadas en enrutamiento, debe volver a considerar los planes. Puede decidir adquirir un dispositivo VPN diferente que sea compatible con puertas de enlace dinámicas, o bien crear una conexión de puerta de enlace de VPN que admita una puerta de enlace con enrutamiento estático. Si más adelante adquiere un dispositivo VPN que sea capaz de admitir una puerta de enlace con enrutamiento dinámico, siempre puede volver a crear la puerta de enlace como dinámica para usar el dispositivo. En ese caso, solo necesitará volver a crear la puerta de enlace. No necesitará volver a crear la red virtual.

A continuación se muestran los dos tipos de puerta de enlace:

- **Enrutamiento estático:** las puertas de enlace con enrutamiento estático admiten **VPN basadas en directivas**. Las VPN basadas en directivas dirigen los paquetes a través de túneles de IPsec con selectores de tráfico basados en las combinaciones de prefijos de dirección entre su red local y la red virtual de Azure. Normalmente, los selectores de tráfico o las directivas se definen como una lista de acceso en la configuración de la VPN.

	>[AZURE.NOTE]No todas las configuraciones son compatibles con puertas de enlace de VPN con enrutamiento estático. Por ejemplo, las configuraciones multisitio, las configuraciones de red virtual a red virtual y las conexiones punto a sitio requieren puertas de enlace con enrutamiento dinámico. Verá los requisitos de las puertas de enlace en los artículos de cada configuración.

- **Enrutamiento dinámico:** las puertas de enlace de enrutamiento dinámico implementan **VPN basadas en enrutamiento**. Las VPN basadas en enrutamiento utilizan "rutas" en la tabla de enrutamiento o reenvío de IP para dirigir los paquetes a sus correspondientes interfaces de túnel VPN. A continuación, las interfaces de túnel cifran o descifran los paquetes dentro y fuera de los túneles. La directiva o el selector de tráfico para las VPN basadas en enrutamiento se configura como conectividad de tipo cualquiera a cualquier (o caracteres comodín).

## Requisitos de las puertas de enlace

La tabla siguiente enumera los requisitos para las puertas de enlace VPN estáticas y dinámicas.


| **Propiedad** | **Puerta de enlace de VPN con enrutamiento estático** | **Puerta de enlace de VPN con enrutamiento dinámico** | **Puerta de enlace de VPN estándar** | **Puerta de enlace de VPN de alto rendimiento** |
|-----------------------------------------|--------------------------------|-----------------------------------------------------------------------|-----------------------------------|----------------------------------|
| Conectividad de sitio a sitio (S2S) | Configuración de VPN basada en directivas | Configuración de VPN basada en enrutamiento | Configuración de VPN basada en enrutamiento | Configuración de VPN basada en enrutamiento |
| Conectividad de punto a sitio (P2S) | No compatible | Compatible (puede coexistir con S2S) | Compatible (puede coexistir con S2S) | Compatible (puede coexistir con S2S) |
| Método de autenticación | Clave precompartida | -Clave precompartida para la conectividad de S2S -Certificados para la conectividad de P2S | -Clave precompartida para la conectividad de S2S -Certificados para la conectividad de P2S | -Clave precompartida para la conectividad de S2S -Certificados para la conectividad de P2S |
| Número máximo de conexiones S2S | 1 | 10 | 10 | 30 |
| Número máximo de conexiones P2S | No compatible | 128 | 128 | 128 |
| Compatibilidad con enrutamiento activo (BGP) | No compatible | No compatible | No compatible | No compatible |


## Pasos siguientes

Seleccione el dispositivo VPN para su configuración. Consulte [Acerca de los dispositivos VPN](http://go.microsoft.com/fwlink/p/?LinkID=615934).

Configure la red virtual. Para las conexiones entre entornos, vea los siguientes artículos:

- [Configurar una conexión sitio a sitio entre entornos a una red virtual de Azure](vpn-gateway-site-to-site-create.md)
- [Configuración de una conexión VPN de punto a sitio a una red virtual de Azure](vpn-gateway-point-to-site-create.md)
- [Conexión VPN sitio a sitio en la Red virtual de Azure mediante el Servicio de enrutamiento y acceso remoto (RRAS) de Windows Server 2012](https://msdn.microsoft.com/library/dn636917.aspx)

Si desea configurar una puerta de enlace de VPN, consulte [Configuración de una puerta de enlace de red virtual](vpn-gateway-configure-vpn-gateway-mp.md).

Si desea cambiar el tipo de puerta de enlace VPN, consulte [Cambio del tipo de enrutamiento de puerta de enlace de VPN de red virtual](vpn-gateway-configure-vpn-gateway-mp.md).

Si desea conectarse a varios sitios en una red virtual, consulte [Conexión de varios sitios locales a una red virtual](http://go.microsoft.com/fwlink/p/?LinkID=615106).

 

<!---HONumber=August15_HO6-->