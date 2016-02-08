<properties 
   pageTitle="Acerca de las puertas de enlace de VPN para la conectividad entre entornos locales en redes virtuales | Microsoft Azure"
   description="Obtenga información sobre las puertas de enlace de VPN, que se pueden utilizar para conexiones entre entornos locales para configuraciones híbridas. Este artículo trata sobre las SKU de puerta de enlace (básica, estándar y de alto rendimiento), las configuraciones de la puerta de enlace de VPN y de ExpressRoute que coexisten, los tipos de enrutamiento de puerta de enlace (estática, dinámica, basada en directivas y basada en enrutamiento) y los requisitos de puerta de enlace para la conectividad de redes virtuales."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carolz"
   editor="tysonn" />
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/15/2015"
   ms.author="cherylmc" />

# Información acerca de las puertas de enlace de VPN

Las puertas de enlace de VPN se utilizan para enviar tráfico de red entre redes virtuales y ubicaciones locales. También se usan para enviar el tráfico entre varias redes virtuales dentro de Azure. Al crear una puerta de enlace, es necesario tener en cuenta algunos factores.
 
Tenga en cuenta lo siguiente realizar la planificación:

- La SKU de puerta de enlace que desea utilizar
- El tipo de enrutamiento de puerta de enlace para la conexión
- El dispositivo VPN, si es necesario para la conexión

## SKU de puerta de enlace

Hay tres SKU de puerta de enlace de VPN:

- Básica
- Standard
- Alto rendimiento

La tabla siguiente muestra los tipos de puerta de enlace y el rendimiento agregado estimado. Los precios difieren entre las SKU de puerta de enlace. Para obtener información acerca de los precios, consulte [Precios de puertas de enlace de VPN](https://azure.microsoft.com/pricing/details/vpn-gateway/).

| SKU | Puerta de enlace de VPN y ExpressRoute coexisten | Rendimiento de puerta de enlace de ExpressRoute | Rendimiento de puerta de enlace de VPN | Túneles IPsec máx. de puerta de enlace de VPN |
|-------------|-----------------------------------|---------------------------------|------------------------|-------------------------------|
| Básica | No | 500 Mbps | 100 Mbps | 10 |
| Standard | Sí | 1000 Mbps | 100 Mbps | 10 |
| Alto rendimiento | Sí | 2000 Mbps | 200 Mbps | 30 |

**Nota:** el rendimiento de la VPN es una estimación aproximada basada en las medidas entre redes virtuales en la misma región de Azure. No es una garantía de lo que puede obtener para conexiones entre entornos a través de Internet, sino que debe usarse como una medida máxima posible.

## Tipos de enrutamiento de puerta de enlace

Hay dos tipos de enrutamiento de puerta de enlace:

- **Basada en directivas:** puertas de enlace basadas en directivas denominadas anteriormente *puertas de enlace estáticas*. La funcionalidad de una puerta de enlace estática no ha cambiado, aun cuando haya cambiado el nombre. Este tipo de puerta de enlace admite las VPN basadas en directivas. Las VPN basadas en directivas dirigen los paquetes a través de túneles de IPsec con selectores de tráfico basados en las combinaciones de prefijos de dirección entre su red local y la red virtual de Azure. Normalmente, los selectores de tráfico o las directivas se definen como una lista de acceso en la configuración de la VPN.
 
- **Basada en enrutamiento:** puertas de enlace basadas en enrutamiento denominadas anteriormente *puertas de enlace dinámicas*. La funcionalidad de una puerta de enlace dinámica no ha cambiado, aun cuando haya cambiado el nombre. Las puertas de enlace basadas en enrutamiento implementan VPN basadas en enrutamiento. Las VPN basadas en enrutamiento utilizan "rutas" en la tabla de enrutamiento o reenvío de IP para dirigir los paquetes a sus correspondientes interfaces de túnel VPN. A continuación, las interfaces de túnel cifran o descifran los paquetes dentro y fuera de los túneles. La directiva o el selector de tráfico para las VPN basadas en enrutamiento se configura como conectividad de tipo cualquiera a cualquier (o caracteres comodín).

Algunas conexiones (como las de sitio a sitio y las de red virtual a red virtual) solo funcionarán con un tipo de enrutamiento de puerta de enlace específico. Verá los requisitos de puerta de enlace enumerados en el artículo que se corresponde con el escenario de conexión que desea crear.

Los dispositivos VPN también tienen limitaciones de configuración. Cuando se crea una puerta de enlace de VPN, seleccionará el tipo de enrutamiento de puerta de enlace que se requiere para la conexión, asegurándose de que el dispositivo VPN que prevé utilizar también admite dicho tipo de enrutamiento. Consulte [Acerca de los dispositivos VPN](vpn-gateway-about-vpn-devices.md) para obtener más información.

Por ejemplo, si tiene previsto utilizar una conexión de sitio a sitio simultáneamente con una conexión de punto a sitio, necesitará configurar una puerta de enlace de VPN basada en enrutamiento. Si bien es cierto que las conexiones de sitio a sitio funcionarán con puertas de enlace basadas en directivas, las conexiones de punto a sitio requieren un tipo de puerta de enlace basada en enrutamiento. Dado que ambas conexiones utilizarán la misma puerta de enlace, tendrá que seleccionar el tipo de puerta de enlace que admite ambas conexiones. Además, el dispositivo VPN que utiliza también debe admitir las configuraciones basadas en enrutamiento.


## Requisitos de las puertas de enlace

La tabla siguiente enumera los requisitos para las puertas de enlace VPN estáticas y dinámicas.


| **Propiedad** | **Puerta de enlace de VPN basada en directivas** | **Puerta de enlace de VPN basada en enrutamiento** | **Puerta de enlace de VPN estándar** | **Puerta de enlace de VPN de alto rendimiento** |
|-----------------------------------------|--------------------------------|-----------------------------------------------------------------------|-----------------------------------|----------------------------------|
| Conectividad de sitio a sitio (S2S) | Configuración de VPN basada en directivas | Configuración de VPN basada en enrutamiento | Configuración de VPN basada en enrutamiento | Configuración de VPN basada en enrutamiento |
| Conectividad de punto a sitio (P2S) | No compatible | Compatible (puede coexistir con S2S) | Compatible (puede coexistir con S2S) | Compatible (puede coexistir con S2S) |
| Método de autenticación | Clave precompartida | -Clave precompartida para la conectividad de S2S -Certificados para la conectividad de P2S | -Clave precompartida para la conectividad de S2S -Certificados para la conectividad de P2S | -Clave precompartida para la conectividad de S2S -Certificados para la conectividad de P2S |
| Número máximo de conexiones S2S | 1 | 10 | 10 | 30 |
| Número máximo de conexiones P2S | No compatible | 128 | 128 | 128 |
| Compatibilidad con enrutamiento activo (BGP) | No compatible | No compatible | No compatible | No compatible |


## Pasos siguientes

Seleccione el dispositivo VPN para su configuración. Consulte [Acerca de los dispositivos VPN](vpn-gateway-about-vpn-devices.md).





 

<!---HONumber=AcomDC_0128_2016-->