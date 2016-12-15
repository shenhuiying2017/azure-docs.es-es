---
title: Preguntas frecuentes sobre VPN Gateway de Virtual Network | Microsoft Docs
description: "Preguntas más frecuentes sobre la puerta de enlace de VPN Preguntas más frecuentes sobre las conexiones entre locales de Virtual Network de Microsoft Azure, las conexiones híbridas de configuración y puertas de enlace VPN"
services: vpn-gateway
documentationcenter: na
author: yushwang
manager: rossort
editor: 
ms.assetid: 6ce36765-250e-444b-bfc7-5f9ec7ce0742
ms.service: vpn-gateway
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/10/2016
ms.author: yushwang
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: e7d0fa43001268fc4747bbf40d3dc209aa037a67


---
# <a name="vpn-gateway-faq"></a>Preguntas más frecuentes sobre la puerta de enlace de VPN
## <a name="connecting-to-virtual-networks"></a>Conexión a redes virtuales
### <a name="can-i-connect-virtual-networks-in-different-azure-regions"></a>¿Puedo conectar redes virtuales en diferentes regiones de Azure?
Sí. De hecho, no hay ninguna restricción de región. Puede conectar una red virtual a otra red virtual en la misma región o en una región distinta de Azure.

### <a name="can-i-connect-virtual-networks-in-different-subscriptions"></a>¿Puedo conectar redes virtuales en diferentes suscripciones?
Sí.

### <a name="can-i-connect-to-multiple-sites-from-a-single-virtual-network"></a>¿Puedo conectar a varios sitios desde una única red virtual?
Puede conectarse a varios sitios mediante el uso de Windows PowerShell y las API de REST de Azure. Consulte la sección de P+F [Conectividad de red virtual a red virtual y de multisitio](#multi-site-and-vnet-to-vnet-connectivity) .

## <a name="what-are-my-cross-premises-connection-options"></a>¿Cuáles son mis opciones de conexión entre locales?
Se admiten las siguientes conexiones entre locales:

* [Sitio a sitio](vpn-gateway-site-to-site-create.md) : conexión VPN sobre IPsec (IKE v1 e IKE v2). Este tipo de conexión requiere un dispositivo VPN o RRAS.
* [Punto a sitio](vpn-gateway-point-to-site-create.md) : conexión VPN sobre SSTP (Protocolo de túnel de sockets seguros). Esta conexión no requiere un dispositivo VPN.
* [Red virtual a red virtual](virtual-networks-configure-vnet-to-vnet-connection.md) : este tipo de conexión es el mismo que el de la configuración de sitio a sitio. La conexión de red virtual a red virtual es una conexión VPN sobre IPsec (IKE v1 e IKE v2). No requiere un dispositivo VPN.
* [Varios sitios](vpn-gateway-multi-site.md) : esta es una variación de una configuración de sitio a sitio que le permite conectar varios sitios locales a una red virtual.
* [ExpressRoute](../expressroute/expressroute-introduction.md) : ExpressRoute es una conexión directa a Azure desde la WAN, no a través de la red Internet pública. Si desea más información consulte la [Información técnica de ExpressRoute](../expressroute/expressroute-introduction.md) y [P+F de ExpressRoute](../expressroute/expressroute-faqs.md).

Para más información acerca de las conexiones, consulte [Acerca de Puerta de enlace de VPN](vpn-gateway-about-vpngateways.md).

### <a name="what-is-the-difference-between-a-site-to-site-connection-and-point-to-site"></a>¿Cuál es la diferencia entre una conexión de sitio a sitio y una de punto a sitio?
**Sitio a sitio** le permiten conectar cualquiera de los equipos ubicados en sus instalaciones locales con cualquier máquina virtual o instancia de rol dentro de la red virtual, dependiendo de cómo elija configurar el enrutamiento. Es ideal para una conexión entre locales que esté siempre disponible y una opción que se adapta bien a las configuraciones híbridas. Este tipo de conexión se basa en un dispositivo (de hardware o de software) VPN sobre IPsec, que se tiene que implementar en el perímetro de la red. Para crear este tipo de conexión, debe tener el hardware de VPN necesario y una dirección IPv4 externa.

**Punto a sitio** le permiten conectarse desde un único equipo y desde cualquier lugar con cualquier dispositivo ubicado en la red virtual. Usa el cliente VPN incluido en Windows. Como parte de la configuración de punto a sitio, instala un certificado y un paquete de configuración de cliente VPN, que contiene la configuración que permite al equipo conectarse a cualquier máquina virtual o instancia de rol dentro de la red virtual. Es ideal si desea conectarse a una red virtual pero no se encuentra en una ubicación local. También es una buena opción cuando no tenga acceso a un hardware VPN o una dirección IPv4 con orientación externa, ya que ambos son necesarios para una conexión de sitio a sitio.

Puede configurar la red virtual para utilizar las conexiones de sitio a sitio y de punto a sitio simultáneamente, siempre que cree la conexión de sitio a sitio mediante un tipo de VPN basada en enrutamiento para la puerta de enlace. Los tipos de VPN basada en enrutamiento se denominan puertas de enlace dinámicas en el modelo de implementación clásico.

### <a name="what-is-expressroute"></a>¿Qué es ExpressRoute?
ExpressRoute le permite crear conexiones privadas entre los centros de datos de Microsoft y la infraestructura local o en un entorno de coubicación. Con ExpressRoute, se pueden establecer conexiones con servicios en la nube de Microsoft, como Microsoft Azure y Office 365 en una instalación de coubicación de asociado de ExpressRoute o se pueden conectar directamente desde la red WAN existente (como una VPN MPLS de un proveedor de servicios de red).

Las conexiones ExpressRoute ofrecen mejor confiabilidad y seguridad, mayor ancho de banda y una menor latencia que las conexiones a Internet típicas. En algunos casos, el uso de conexiones ExpressRoute para transferir datos entre la red local y Azure también puede aportar beneficios económicos importantes. Si ya ha creado una conexión entre instalaciones desde la red local, puede migrar a una conexión ExpressRoute manteniendo intacta la red virtual.

Consulte [P+F de ExpressRoute](../expressroute/expressroute-faqs.md) para obtener más detalles.

## <a name="site-to-site-connections-and-vpn-devices"></a>Conexiones de sitio a sitio y dispositivos VPN
### <a name="what-should-i-consider-when-selecting-a-vpn-device"></a>¿Qué tengo que tener en cuenta al seleccionar un dispositivo VPN?
Hemos validado un conjunto de dispositivos estándar VPN de sitio a sitio en colaboración con proveedores de dispositivos. [Aquí](vpn-gateway-about-vpn-devices.md)puede encontrar una lista de dispositivos VPN de compatibilidad conocida y sus correspondientes instrucciones de configuración o ejemplos, así como especificaciones del dispositivo. Todos los dispositivos dentro de las familias de dispositivos que aparecen como de compatibilidad conocida, deben funcionar con Red virtual. Con el fin de configurar el dispositivo VPN, consulte el ejemplo de configuración de dispositivo o el vínculo que corresponde a la familia de dispositivos adecuada.

### <a name="what-do-i-do-if-i-have-a-vpn-device-that-isnt-in-the-known-compatible-device-list"></a>¿Qué hago si tengo un dispositivo VPN que no está en la lista de dispositivos de compatibilidad conocida?
Si el dispositivo no aparece como un dispositivo VPN de compatibilidad conocida y desea utilizarlo para la conexión VPN, tendrá que comprobar que tiene las opciones de configuración admitidas de IPsec/IKE y los parámetros que aparecen [aquí](vpn-gateway-about-vpn-devices.md). Los dispositivos que cumplen los requisitos mínimos deberían funcionar correctamente con las puertas de enlace de VPN. Póngase en contacto con el fabricante del dispositivo para obtener instrucciones adicionales de soporte técnico y configuración.

### <a name="why-does-my-policy-based-vpn-tunnel-go-down-when-traffic-is-idle"></a>¿Por qué mi túnel de VPN basado en directivas deja de funcionar cuando el tráfico está inactivo?
Este es el comportamiento esperado para puertas de enlace de VPN basadas en directivas (también conocido como enrutamiento estático). Cuando el tráfico a través del túnel está inactivo durante más de 5 minutos, este túnel se cancelará. Cuanto el tráfico comience a fluir en cualquier dirección, el túnel se restablecerá inmediatamente.

### <a name="can-i-use-software-vpns-to-connect-to-azure"></a>¿Puedo usar una VPN de software para conectarme a Azure?
Para la configuración de sitio a sitio entre locales se admiten los servidores de Windows Server 2012 con servicio de enrutamiento y acceso remoto (RRAS).

Otras soluciones VPN de software deben funcionar con nuestra puerta de enlace siempre que se ajusten a las implementaciones IPsec estándar de la industria. Póngase en contacto con el proveedor del software para obtener instrucciones de configuración y soporte técnico.

## <a name="point-to-site-connections"></a>Conexiones de punto a sitio
### <a name="what-operating-systems-can-i-use-with-point-to-site"></a>¿Qué sistemas operativos puedo usar para las conexiones de punto a sitio?
Se admiten los siguientes sistemas operativos:

* Windows 7 (32 bits y 64 bits)
* Windows Server 2008 R2 (solo 64 bits)
* Windows 8 (32 bits y 64 bits)
* Windows 8.1 (32 bits y 64 bits)
* Windows Server 2012 (solo 64 bits)
* Windows Server 2012 R2 (solo 64 bits)
* Windows 10

### <a name="can-i-use-any-software-vpn-client-for-point-to-site-that-supports-sstp"></a>¿Puedo usar cualquier cliente de software VPN de punto a sitio que admita SSTP?
No. La compatibilidad se limita solo a las versiones de sistema operativo de Windows enumeradas anteriormente.

### <a name="how-many-vpn-client-endpoints-can-i-have-in-my-point-to-site-configuration"></a>¿Cuántos puntos de conexión de cliente VPN puedo tener en mi configuración punto a sitio?
Se admiten hasta 128 clientes VPN para poder conectarse al mismo tiempo a una red virtual.

### <a name="can-i-use-my-own-internal-pki-root-ca-for-point-to-site-connectivity"></a>¿Puedo usar mi propio CA raíz de PKI interna para la conectividad de punto a sitio?
Sí. Anteriormente, solo podían usarse certificados raíz autofirmados. Todavía puede cargar 20 certificados raíz.

### <a name="can-i-traverse-proxies-and-firewalls-using-point-to-site-capability"></a>¿Puedo atravesar servidores proxy y firewalls con la funcionalidad de punto a sitio?
Sí. Usamos SSTP (Protocolo de túnel de sockets de seguros) para el túnel a través de firewalls. Este túnel aparecerá como una conexión HTTPs.

### <a name="if-i-restart-a-client-computer-configured-for-point-to-site-will-the-vpn-automatically-reconnect"></a>¿Si reinicio un equipo cliente con configuración de punto a sitio, se volverá a conectar la VPN de forma automática?
De forma predeterminada, el equipo cliente no volverá a establecer la conexión VPN automáticamente.

### <a name="does-point-to-site-support-auto-reconnect-and-ddns-on-the-vpn-clients"></a>¿Admite la configuración de punto a sitio la reconexión automática y el DDNS en los clientes VPN?
Las VPN de punto a sitio no admiten la reconexión automática y el DDNS.

### <a name="can-i-have-site-to-site-and-point-to-site-configurations-coexist-for-the-same-virtual-network"></a>¿Puedo tener configuraciones de sitio a sitio y de punto a sitio coexistiendo en la misma red virtual?
Sí. Ambas soluciones funcionarán si tiene un tipo de VPN basada en enrutamiento para la puerta de enlace. Para el modelo de implementación clásica, necesita una puerta de enlace dinámica. No se admite la configuración de punto a sitio para puertas de enlace de VPN de enrutamiento estáticas o puertas de enlace que usan -VpnType PolicyBased.

### <a name="can-i-configure-a-point-to-site-client-to-connect-to-multiple-virtual-networks-at-the-same-time"></a>¿Puedo configurar un cliente de punto a sitio para conectarse a varias redes virtuales al mismo tiempo?
Sí, es posible. Pero las redes virtuales no pueden tener prefijos IP superpuestos y los espacios de dirección de punto a sitio no pueden superponerse entre las redes virtuales.

### <a name="how-much-throughput-can-i-expect-through-site-to-site-or-point-to-site-connections"></a>¿Qué rendimiento puedo esperar en las conexiones de sitio a sitio o de punto a sitio?
Es difícil de mantener el rendimiento exacto de los túneles VPN. IPsec y SSTP son protocolos VPN con mucho cifrado. El rendimiento también está limitado por la latencia y el ancho de banda entre sus instalaciones locales e Internet.

## <a name="gateways"></a>Puertas de enlace
### <a name="what-is-a-policy-based-static-routing-gateway"></a>¿Qué es una puerta de enlace basada en directivas (de enrutamiento estático)?
Las puertas de enlace basadas en directivas implementan VPN basadas en directivas. Las VPN basadas en directivas cifran y dirigen los paquetes a través de túneles de IPsec basados en las combinaciones de prefijos de dirección entre su red local y la red virtual de Azure. Normalmente, la directiva (o selector de tráfico) se define como una lista de acceso en la configuración de la VPN.

### <a name="what-is-a-route-based-dynamic-routing-gateway"></a>¿Qué es una puerta de enlace basada en enrutamiento (de enrutamiento dinámico)?
Las puertas de enlace basadas en enrutamiento implementan VPN basadas en enrutamiento. Las VPN basadas en enrutamiento utilizan "rutas" en la dirección IP de reenvío o en la tabla de enrutamiento para dirigir los paquetes a sus correspondientes interfaces de túnel. A continuación, las interfaces de túnel cifran o descifran los paquetes dentro y fuera de los túneles. La directiva o selector de tráfico para las VPN basadas en enrutamiento se configura como conectividad de tipo any-to-any (o caracteres comodín).

### <a name="can-i-get-my-vpn-gateway-ip-address-before-i-create-it"></a>¿Puedo obtener mi dirección IP de puerta de enlace VPN antes de crearla?
No. Tiene que crear primero la puerta de enlace para obtener la dirección IP. Si elimina y vuelve a crear la puerta de enlace VPN, la dirección IP cambiará.

### <a name="how-does-my-vpn-tunnel-get-authenticated"></a>¿Cómo se autentica mi túnel VPN?
VPN de Azure usa autenticación PSK (clave previamente compartida). Se genera una clave previamente compartida (PSK) cuando se crea el túnel VPN. Puede cambiar la clave PSK generada automáticamente con la suya propia a través del cmdlet de PowerShell o la API de REST para establecer la clave precompartida.

### <a name="can-i-use-the-set-pre-shared-key-api-to-configure-my-policy-based-static-routing-gateway-vpn"></a>¿Puedo usar la API para establecer la clave precompartida y configurar mi VPN basada en directivas (enrutamiento estático) de la puerta de enlace?
Sí, el cmdlet de PowerShell y la API para establecer la clave precompartida se pueden utilizar para configurar tanto las VPN basadas en directivas (enrutamiento estático) de Azure como las VPN basadas en enrutamiento (enrutamiento dinámico).

### <a name="can-i-use-other-authentication-options"></a>¿Puedo usar otras opciones de autenticación?
Las opciones están limitadas al uso de claves precompartidas (PSK) para la autenticación.

### <a name="what-is-the-gateway-subnet-and-why-is-it-needed"></a>¿Qué es la "subred de puerta de enlace" y por qué es necesaria?
Existe un servicio de puerta de enlace que se ejecuta para habilitar la conectividad entre locales.

Deberá crear una subred de puerta de enlace para la red virtual para configurar una puerta de enlace de VPN. Para que funcionen correctamente, todas las subredes de puerta de enlace se deben llamar GatewaySubnet. No denomine de ninguna otra forma la subred de puerta de enlace. Y no implemente máquinas virtuales ni nada más en la subred de puerta de enlace.

El tamaño mínimo de la subred de puerta de enlace depende por completo de la configuración que desee crear. Aunque es posible crear una puerta de enlace tan pequeña como /29 para algunas configuraciones, le recomendamos que cree una puerta de enlace de /28 o mayor (/28, /27, /26 etc.).

### <a name="can-i-deploy-virtual-machines-or-role-instances-to-my-gateway-subnet"></a>¿Puedo implementar máquinas virtuales o instancias de rol en mi subred de puerta de enlace?
No.

### <a name="how-do-i-specify-which-traffic-goes-through-the-vpn-gateway"></a>¿Cómo especifico qué tráfico pasa a través de la puerta de enlace VPN?
Si está usando el Portal de Azure clásico, agregue cada uno de los intervalos que quiera enviar a través de la puerta de enlace de la red virtual a la página Redes en Redes locales.

### <a name="can-i-configure-forced-tunneling"></a>¿Puedo configurar una tunelización forzada?
Sí. Consulte [Configurar una tunelización forzada](vpn-gateway-about-forced-tunneling.md).

### <a name="can-i-set-up-my-own-vpn-server-in-azure-and-use-it-to-connect-to-my-on-premises-network"></a>¿Puedo configurar mi propio servidor VPN en Azure y usarlo para conectar a mi red local?
Sí, puede implementar sus propias puertas de enlace o servidores VPN en Azure bien desde Azure Marketplace o creando sus propios enrutadores VPN. Tendrá que configurar las rutas definidas por el usuario en la red virtual para asegurarse de que el tráfico se enruta correctamente entre las redes locales y las subredes de la red virtual.

### <a name="why-are-certain-ports-opened-on-my-vpn-gateway"></a>¿Por qué hay algunos puertos abiertos en mi puerta de enlace de VPN?
Son necesarios para la comunicación de la infraestructura de Azure. Están protegidos (bloqueados) mediante certificados de Azure. Sin los certificados apropiados, las entidades externas, incluidos los clientes de esas puertas de enlace, no podrán tener ningún efecto en esos puntos de conexión.

Una puerta de enlace de VPN es básicamente un dispositivo de hosts múltiples con una NIC que accede a la red privada del cliente y una NIC accesible desde la red pública. Las entidades de la infraestructura de Azure no pueden acceder a redes privadas de clientes por motivos de conformidad, por lo que necesitan usar puntos de conexión públicos para la comunicación de infraestructura. Los puntos de conexión públicos se analizan periódicamente mediante auditoría de seguridad de Azure.

### <a name="more-information-about-gateway-types-requirements-and-throughput"></a>Más información acerca de los tipos de puerta de enlace, los requisitos y el rendimiento
Para más información, vea [Acerca de la configuración de VPN Gateway](vpn-gateway-about-vpn-gateway-settings.md).

## <a name="multi-site-and-vnet-to-vnet-connectivity"></a>Conectividad de red virtual a red virtual y de multisitio
### <a name="which-type-of-gateways-can-support-multi-site-and-vnet-to-vnet-connectivity"></a>¿Qué tipo de puertas de enlace admiten la conectividad de red virtual a red virtual y de multisitio?
Solo VPN basadas en enrutamiento (enrutamiento dinámico).

### <a name="can-i-connect-a-vnet-with-a-routebased-vpn-type-to-another-vnet-with-a-policybased-vpn-type"></a>¿Puedo conectar una red virtual con un tipo de VPN RouteBased a otra red virtual con un tipo de VPN PolicyBased?
No, ambas redes virtuales TIENEN QUE usar VPN basadas en enrutamiento (enrutamiento dinámico).

### <a name="is-the-vnet-to-vnet-traffic-secure"></a>¿Es seguro el tráfico de red virtual a red virtual?
Sí, se protege mediante cifrado IPsec/IKE.

### <a name="does-vnet-to-vnet-traffic-travel-over-the-azure-backbone"></a>¿El tráfico de red virtual a red virtual viaja a través de la red troncal de Azure?
Sí.

### <a name="how-many-on-premises-sites-and-virtual-networks-can-one-virtual-network-connect-to"></a>¿A cuántos sitios locales y redes virtuales se puede conectar una red virtual?
Máx. 10 combinados para las puertas de enrutamiento dinámico de nivel Básico y Estándar y 30 para las puertas de enlace VPN de alto rendimiento.

### <a name="can-i-use-point-to-site-vpns-with-my-virtual-network-with-multiple-vpn-tunnels"></a>¿Puedo usar VPN de punto a sitio con mi red virtual con varios túneles VPN?
Sí, las VPN de punto a sitio (P2S) se pueden usar con las puertas de enlace de VPN para conectarse a varios sitios locales y a otras redes virtuales.

### <a name="can-i-configure-multiple-tunnels-between-my-virtual-network-and-my-on-premises-site-using-multi-site-vpn"></a>¿Puedo configurar varios túneles entre mi red virtual y mi sitio local utilizando VPN multisitio?
No, no se admiten túneles redundantes entre una red virtual de Azure y un sitio local.

### <a name="can-there-be-overlapping-address-spaces-among-the-connected-virtual-networks-and-on-premises-local-sites"></a>¿Puede haber espacios de direcciones superpuestos entre las redes virtuales conectadas y los sitios locales?
No. Los espacios de direcciones superpuestos harían que fallara la carga del archivo de configuración de red o la creación de una red virtual.

### <a name="do-i-get-more-bandwidth-with-more-site-to-site-vpns-than-for-a-single-virtual-network"></a>¿Tengo más ancho de banda con más VPN de sitio a sitio que si tengo una única red virtual?
No, todos los túneles VPN, incluidas las VPN de punto a sitio, comparten la misma puerta de enlace de VPN de Azure y el ancho de banda disponible.

### <a name="can-i-use-azure-vpn-gateway-to-transit-traffic-between-my-on-premises-sites-or-to-another-virtual-network"></a>¿Puedo usar la puerta de enlace de VPN de Azure para el tráfico en tránsito entre mis sitios locales o a otra red virtual?
**Modelo de implementación clásica**<br>
 el tráfico en tránsito a través de Puerta de enlace de VPN de Azure es posible mediante el modelo de implementación clásica, pero se basa en espacios de direcciones definidos estáticamente en el archivo de configuración de red. BGP aún no se admite con instancias de Red virtual de Azure y Puerta de enlace de VPN mediante el modelo de implementación clásica. Sin BGP, definir manualmente los espacios de direcciones de tránsito es difícil de hacer sin errores y no se recomienda.<br>
**Modelo de implementación de Resource Manager**<br>
Si usa el modelo de implementación de Resource Manager, consulte la sección [BGP](#bgp) para más información.

### <a name="does-azure-generate-the-same-ipsecike-pre-shared-key-for-all-my-vpn-connections-for-the-same-virtual-network"></a>¿Azure genera la misma clave precompartida de IPsec/IKE para todas mis conexiones VPN para la misma red virtual?
No, Azure de forma predeterminada genera distintas claves precompartidas para distintas conexiones VPN. Sin embargo, puede utilizar la API de REST para establecer la clave de la puerta de enlace VPN o el cmdlet PowerShell para establecer el valor de clave que prefiera. La clave TIENE QUE ser una cadena alfanumérica con una longitud de entre 1 y 128 caracteres.

### <a name="does-azure-charge-for-traffic-between-virtual-networks"></a>¿Azure cobra por el tráfico entre redes virtuales?
Para el tráfico entre distintas redes virtuales de Azure, se cobra solo por el tráfico que atraviesa de una región de Azure a otra. Las tarifas se encuentran en la página [Precios de la puerta de enlace de VPN](https://azure.microsoft.com/pricing/details/vpn-gateway/) de Azure.

### <a name="can-i-connect-a-virtual-network-with-ipsec-vpns-to-my-expressroute-circuit"></a>¿Puedo conectar una red virtual con VPN sobre IPsec a mi circuito de ExpressRoute?
Sí, este procedimiento se admite. Para más información, consulte [Configurar conexiones VPN ExpressRoute y sitio a sitio que coexistan](../expressroute/expressroute-howto-coexist-classic.md).

## <a name="a-namebgpabgp"></a><a name="bgp"></a>BGP
[!INCLUDE [vpn-gateway-bgp-faq-include](../../includes/vpn-gateway-bpg-faq-include.md)]

## <a name="cross-premises-connectivity-and-vms"></a>Conectividad entre locales y máquinas virtuales
### <a name="if-my-virtual-machine-is-in-a-virtual-network-and-i-have-a-cross-premises-connection-how-should-i-connect-to-the-vm"></a>Si mi máquina virtual está en una red virtual y tengo una conexión entre locales, ¿cómo debo conectar a la máquina virtual?
Dispone de varias opciones. Si tiene RDP habilitado y ha creado un extremo, puede conectarse a la máquina virtual mediante la VIP. En ese caso, especificaría la VIP y el puerto al que desea conectarse. Tendrá que configurar el puerto en la máquina virtual para el tráfico. Normalmente, tendrá que ir al Portal de Azure clásico y guardar la configuración de la conexión RDP en el equipo. La configuración contiene la información de conexión necesaria.

Si tiene configurada una red virtual con conectividad entre locales, puede conectarse a la máquina virtual mediante la DIP interna o una dirección IP privada. También puede conectarse a la máquina virtual mediante la DIP interna desde otra máquina virtual que se encuentre en la misma red virtual. Si se conecta desde una ubicación fuera de la red virtual no podrá usar RDP en la máquina virtual mediante la DIP. Por ejemplo, si tiene configurada una red virtual de punto a sitio y no establece una conexión desde su equipo, no podrá conectarse a la máquina virtual mediante la DIP.

### <a name="if-my-virtual-machine-is-in-a-virtual-network-with-cross-premises-connectivity-does-all-the-traffic-from-my-vm-go-through-that-connection"></a>¿Si mi máquina virtual está en una red virtual con conectividad entre locales, todo el tráfico de mi máquina virtual pasa a través de esa conexión?
No. Únicamente el tráfico que tiene como destino una IP que se encuentra en los intervalos de direcciones IP de red local de la red virtual que haya especificado pasará a través de la puerta de enlace de red virtual. El tráfico que tenga una IP de destino ubicada dentro de la red virtual permanecerá en la red virtual. El resto del tráfico se envía a través del equilibrador de carga a las redes públicas, o si se usa la tunelización forzada, se envía a través de la puerta de enlace VPN de Azure. Si está solucionando problemas, es importante asegurarse de que en la red local tiene especificados todos los intervalos que desee enviar a través de la puerta de enlace. Compruebe que los intervalos de direcciones de red local no se superponen con ninguno de los intervalos de direcciones de la red virtual. Además, debe comprobar que el servidor DNS que está utilizando está resolviendo el nombre a la dirección IP correcta.

## <a name="virtual-network-faq"></a>P+F de Red virtual
Consulte información adicional de redes virtuales adicionales en las [Preguntas frecuentes sobre redes virtuales](../virtual-network/virtual-networks-faq.md).



<!--HONumber=Dec16_HO2-->


