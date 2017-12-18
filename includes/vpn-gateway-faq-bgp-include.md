### <a name="is-bgp-supported-on-all-azure-vpn-gateway-skus"></a>¿Se admite BGP en todas las SKU de Azure VPN Gateway?
No, BGP se admite solo en las puertas de enlace VPN de Azure **VpnGw1**, **VpnGw2**, **VpnGw3**, **Standard** y **HighPerformance**. **Basic** NO se admite.

### <a name="can-i-use-bgp-with-azure-policy-based-vpn-gateways"></a>¿Puedo usar BGP con puertas de enlace de VPN basadas en directivas de Azure?
No, BGP solo es compatible con puertas de enlace de VPN basadas en enrutamiento.

### <a name="can-i-use-private-asns-autonomous-system-numbers"></a>¿Puedo usar ASN (números de sistema autónomos) privados?
Sí, puede usar sus propios ASN públicos o privados para sus redes locales y redes virtuales de Azure.

### <a name="are-there-asns-reserved-by-azure"></a>¿Hay ASN reservados por Azure?
Sí, los siguientes ASN están reservados por Azure para emparejamientos internos y externos:

* ASN públicos: 8074, 8075, 12076
* ASN privados: 65515, 65517, 65518, 65519, 65520

Estos ASN no se pueden especificar para sus dispositivos VPN locales al conectar con puertas de enlace de VPN.

### <a name="are-there-any-other-asns-that-i-cant-use"></a>¿Hay algún otro ASN que no puedo usar?
Sí, los siguientes ASN están [reservados por IANA](http://www.iana.org/assignments/iana-as-numbers-special-registry/iana-as-numbers-special-registry.xhtml) y no se pueden configurar en Azure VPN Gateway:

23456, 64496-64511, 65535-65551 y 429496729

### <a name="can-i-use-the-same-asn-for-both-on-premises-vpn-networks-and-azure-vnets"></a>¿Puedo usar el mismo ASN para redes VPN locales y redes virtuales de Azure?
No, debe asignar ASN diferentes entre las redes locales y sus redes virtuales de Azure si las está conectando con BGP. Azure VPN Gateway tiene un ASN de 65515 predeterminado asignado, independientemente de que BGP esté habilitado, o no, para la conectividad entre locales. Para invalidar este valor predeterminado, asigne otro ASN al crear la puerta de enlace de VPN o cambie el ASN después de crearla. Debe asignar los ASN locales a las puertas de enlace de red local de Azure correspondientes.

### <a name="what-address-prefixes-will-azure-vpn-gateways-advertise-to-me"></a>¿Qué prefijos de direcciones de puertas de enlace de VPN de Azure se me anunciarán?
La puerta de enlace de VPN de Azure anunciará las siguientes rutas a sus dispositivos BGP locales:

* Sus prefijos de dirección de red virtual
* Los prefijos de dirección de todas las puertas de enlace de red local conectadas a la puerta de enlace de VPN de Azure
* Las rutas aprendidas en otras sesión de intercambio de tráfico (peering) de BGP conectadas a la puerta de enlace de VPN de Azure, **excepto la ruta predeterminada o las rutas superpuestas con cualquier prefijo de red virtual**.

### <a name="can-i-advertise-default-route-00000-to-azure-vpn-gateways"></a>¿Puedo anunciar la ruta predeterminada (0.0.0.0/0) para puertas de enlace de VPN de Azure?
Sí.

Tenga en cuenta que esto conducirá todo el tráfico de salida de la red virtual hacia su sitio local e impedirá que las máquinas virtuales de la red virtual acepten comunicaciones públicas desde Internet directamente como, por ejemplo, RDP o SSH desde Internet a las máquinas virtuales.

### <a name="can-i-advertise-the-exact-prefixes-as-my-virtual-network-prefixes"></a>¿Puedo anunciar los mismos prefijos que los de mis prefijos de red virtual?

No, la plataforma de Azure bloqueará o filtrará el anuncio de los mismos prefijos que los de cualquiera de sus otros prefijos de dirección de red virtual. Sin embargo, puede anunciar un prefijo que sea un superconjunto de lo que contenga su red virtual. 

Por ejemplo, si una red virtual usara el espacio de direcciones 10.0.0.0/16, se podría anunciar 10.0.0.0/8. Sin embargo, no se pueden anunciar 10.0.0.0/16 o 10.0.0.0/24.

### <a name="can-i-use-bgp-with-my-vnet-to-vnet-connections"></a>¿Puedo usar BGP con mis conexiones de red virtual a red virtual?
Sí, puede utilizar BGP para conexiones entre locales y conexiones de red virtual a red virtual.

### <a name="can-i-mix-bgp-with-non-bgp-connections-for-my-azure-vpn-gateways"></a>¿Puedo combinar BGP con conexiones no de BGP para mi puertas de enlace de VPN de Azure?
Sí, puede mezclar conexiones de BGP y no de BGP para la misma puerta de enlace de VPN de Azure.

### <a name="does-azure-vpn-gateway-support-bgp-transit-routing"></a>¿Azure VPN Gateway admite el enrutamiento del tránsito de BGP?
Sí, se admite el enrutamiento del tránsito de BGP, con la excepción de que las puertas de enlace de VPN de Azure **NO** anunciarán las rutas predeterminadas a otros pares de BGP. Para habilitar el enrutamiento del tránsito a través de varias puertas de enlace de VPN de Azure, debe habilitar BGP en todas las conexiones de red virtual a red virtual intermedias.

### <a name="can-i-have-more-than-one-tunnel-between-azure-vpn-gateway-and-my-on-premises-network"></a>¿Puedo tener más de un túnel entre mi red local y Azure VPN Gateway?
Sí, puede establecer más de un túnel VPN S2S entre Azure VPN Gateway y la red local. Tenga en cuenta que todos estos túneles se incluirán en el recuento total de túneles de las puertas de enlace de la VPN de Azure y que BGP debe habilitarse en ambos.

Por ejemplo, si tiene dos túneles redundantes entre la instancia de Azure VPN Gateway y una de las redes locales, consumirán 2 túneles de la cuota total para la puerta de enlace de VPN (10 para Standard y 30 para HighPerformance).

### <a name="can-i-have-multiple-tunnels-between-two-azure-vnets-with-bgp"></a>¿Puedo tener varios túneles entre dos redes virtuales de Azure con BGP?
Sí, pero al menos una de las puertas de enlace de la red virtual debe estar en una configuración activa-activa.

### <a name="can-i-use-bgp-for-s2s-vpn-in-an-expressroutes2s-vpn-co-existence-configuration"></a>¿Se puede usar BGP para VPN de sitio a sitio en que coexisten VPN de sitio a sitio y ExpressRoute?
Sí. 

### <a name="what-address-does-azure-vpn-gateway-use-for-bgp-peer-ip"></a>¿Qué dirección utiliza la puerta de enlace de VPN de Azure para la dirección IP del par BGP?
La puerta de enlace de VPN de Azure asignará una sola dirección IP del intervalo de GatewaySubnet definido para la red virtual. De forma predeterminada, es la penúltima dirección del intervalo. Por ejemplo, si el valor de GatewaySubnet es 10.12.255.0/27, y oscila entre 10.12.255.0 y 10.12.255.31, la dirección IP del par BGP de la puerta de enlace de la VPN de Azure será 10.12.255.30. Puede encontrar esta información al mostrar la información de la puerta de enlace de VPN de Azure.

### <a name="what-are-the-requirements-for-the-bgp-peer-ip-addresses-on-my-vpn-device"></a>¿Cuáles son los requisitos para las direcciones IP del par BGP en mi dispositivo VPN?
Su dirección del par BGP local **NO DEBE** ser la misma que la dirección IP pública del dispositivo VPN. Utilice otra dirección IP en el dispositivo VPN para la dirección IP del par BGP. Puede ser una dirección asignada a la interfaz de bucle invertido en el dispositivo. Especifique esta dirección en la puerta de enlace de red local correspondiente que representa la ubicación.

### <a name="what-should-i-specify-as-my-address-prefixes-for-the-local-network-gateway-when-i-use-bgp"></a>¿Qué debo especificar como mis prefijos de dirección para la puerta de enlace de red local al utilizar BGP?
La puerta de enlace de red local de Azure especifica los prefijos de dirección iniciales para la red local. Con BGP, debe asignar el prefijo de host (prefijo /32) de la dirección IP del par BGP como espacio de direcciones para esa red local. Si su dirección IP del par BGP es 10.52.255.254, debe especificar "10.52.255.254/32" como localNetworkAddressSpace de la puerta de enlace de red local que representa esta red local. Esto es para asegurarse de que la puerta de enlace de VPN de Azure establece la sesión de BGP a través del túnel VPN S2S.

### <a name="what-should-i-add-to-my-on-premises-vpn-device-for-the-bgp-peering-session"></a>¿Qué debo agregar a mi dispositivo VPN local para la sesión de emparejamiento BGP?
Debe agregar una ruta de host de la dirección IP del par BGP de Azure en el dispositivo VPN que apunta al túnel VPN S2S de IPsec. Por ejemplo, si la dirección IP del par VPN de Azure es "10.12.255.30", debe agregar una ruta de host para "10.12.255.30" con una interfaz de próximo salto de la interfaz de túnel IPsec coincidente en el dispositivo VPN.

