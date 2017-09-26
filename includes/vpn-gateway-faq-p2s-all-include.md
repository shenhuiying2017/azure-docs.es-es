### <a name="supportedclientos"></a>¿Qué sistemas operativos de cliente puedo usar para las conexiones de punto a sitio?

Se admiten los siguientes sistemas operativos de cliente:

* Windows 7 (32 bits y 64 bits)
* Windows Server 2008 R2 (solo 64 bits)
* Windows 8 (32 bits y 64 bits)
* Windows 8.1 (32 bits y 64 bits)
* Windows Server 2012 (solo 64 bits)
* Windows Server 2012 R2 (solo 64 bits)
* Windows 10
* OSX versión 10.11 para Mac (El Capitan)
* macOS versión 10.12 para Mac (Sierra)

### <a name="how-many-vpn-client-endpoints-can-i-have-in-my-point-to-site-configuration"></a>¿Cuántos puntos de conexión de cliente VPN puedo tener en mi configuración punto a sitio?

Se admiten hasta 128 clientes VPN para poder conectarse al mismo tiempo a una red virtual.

### <a name="can-i-traverse-proxies-and-firewalls-using-point-to-site-capability"></a>¿Puedo atravesar servidores proxy y firewalls con la funcionalidad de punto a sitio?

Azure admite dos tipos de opciones de VPN de punto a sitio:

* Protocolo de túnel de sockets seguros (SSTP)

  SSTP es una solución basada en SSL propietaria de Microsoft que puede atravesar firewalls, puesto que la mayoría de los firewalls abren el puerto TCP 443 que utiliza SSL.

* VPN IKEv2

  VPN IKEv2 es una solución de VPN con IPsec basada en estándares que utiliza los puertos UDP 500 y 4500 y protocolo IP. 50. Los firewalls no siempre abren estos puertos, por lo que hay una posibilidad de que la VPN IKEv2 no pueda atravesar servidores proxy y firewalls.

### <a name="if-i-restart-a-client-computer-configured-for-point-to-site-will-the-vpn-automatically-reconnect"></a>¿Si reinicio un equipo cliente con configuración de punto a sitio, se volverá a conectar la VPN de forma automática?

De forma predeterminada, el equipo cliente no volverá a establecer la conexión VPN automáticamente.

### <a name="does-point-to-site-support-auto-reconnect-and-ddns-on-the-vpn-clients"></a>¿Admite la configuración de punto a sitio la reconexión automática y el DDNS en los clientes VPN?

Las VPN de punto a sitio no admiten la reconexión automática y el DDNS.

### <a name="can-i-have-site-to-site-and-point-to-site-configurations-coexist-for-the-same-virtual-network"></a>¿Puedo tener configuraciones de sitio a sitio y de punto a sitio coexistiendo en la misma red virtual?

Sí. Para el modelo de implementación de Resource Manager, debe tener un tipo de VPN basada en ruta para la puerta de enlace. Para el modelo de implementación clásica, necesita una puerta de enlace dinámica. No se admite la configuración de punto a sitio para puertas de enlace de VPN de enrutamiento estático o puertas de enlace de VPN basadas en directivas.

### <a name="can-i-configure-a-point-to-site-client-to-connect-to-multiple-virtual-networks-at-the-same-time"></a>¿Puedo configurar un cliente de punto a sitio para conectarse a varias redes virtuales al mismo tiempo?

No. Un cliente de punto a sitio solo puede conectarse a los recursos de la red virtual en la que reside la puerta de enlace de red virtual.

### <a name="how-much-throughput-can-i-expect-through-site-to-site-or-point-to-site-connections"></a>¿Qué rendimiento puedo esperar en las conexiones de sitio a sitio o de punto a sitio?

Es difícil de mantener el rendimiento exacto de los túneles VPN. IPsec y SSTP son protocolos VPN con mucho cifrado. El rendimiento también está limitado por la latencia y el ancho de banda entre sus instalaciones locales e Internet. Para una puerta de enlace de VPN únicamente con conexiones VPN de punto a sitio IKEv2, el rendimiento total que puede esperar depende de la SKU de puerta de enlace. Consulte [SKU de puerta de enlace](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku) para más información sobre el rendimiento.

### <a name="can-i-use-any-software-vpn-client-for-point-to-site-that-supports-sstp-andor-ikev2"></a>¿Puedo usar cualquier software de cliente VPN para punto a sitio que admita SSTP o IKEv2?

No. Solo puede usar el cliente VPN nativo en Windows para SSTP y el cliente VPN nativo en Mac para IKEv2. Consulte la lista de sistemas operativos cliente compatibles.