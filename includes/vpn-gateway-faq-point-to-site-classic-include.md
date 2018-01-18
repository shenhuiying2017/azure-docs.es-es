Estas preguntas más frecuentes corresponden a las conexiones P2S mediante el modelo de implementación clásica.

### <a name="what-client-operating-systems-can-i-use-with-point-to-site"></a>¿Qué sistemas operativos de cliente puedo usar para las conexiones de punto a sitio?

Se admiten los siguientes sistemas operativos de cliente:

* Windows 7 (32 bits y 64 bits)
* Windows Server 2008 R2 (solo 64 bits)
* Windows 8 (32 bits y 64 bits)
* Windows 8.1 (32 bits y 64 bits)
* Windows Server 2012 (solo 64 bits)
* Windows Server 2012 R2 (solo 64 bits)
* Windows 10

### <a name="can-i-use-any-software-vpn-client-for-point-to-site-that-supports-sstp"></a>¿Puedo usar cualquier cliente de software VPN de punto a sitio que admita SSTP?

Nº La compatibilidad se limita solo a las versiones de sistema operativo de Windows enumeradas anteriormente.

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

Sí. Ambas soluciones funcionarán si tiene un tipo de VPN basada en enrutamiento para la puerta de enlace. Para el modelo de implementación clásica, necesita una puerta de enlace dinámica. No se admite la configuración de punto a sitio para puertas de enlace de VPN de enrutamiento estáticas o puertas de enlace que usan el cmdlet `-VpnType PolicyBased`.

### <a name="can-i-configure-a-point-to-site-client-to-connect-to-multiple-virtual-networks-at-the-same-time"></a>¿Puedo configurar un cliente de punto a sitio para conectarse a varias redes virtuales al mismo tiempo?

Sí, es posible. Pero las redes virtuales no pueden tener prefijos IP superpuestos y los espacios de dirección de punto a sitio no pueden superponerse entre las redes virtuales.

### <a name="how-much-throughput-can-i-expect-through-site-to-site-or-point-to-site-connections"></a>¿Qué rendimiento puedo esperar en las conexiones de sitio a sitio o de punto a sitio?

Es difícil de mantener el rendimiento exacto de los túneles VPN. IPsec y SSTP son protocolos VPN con mucho cifrado. El rendimiento también está limitado por la latencia y el ancho de banda entre sus instalaciones locales e Internet.
