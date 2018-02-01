### <a name="supportedclientos"></a>¿Qué sistemas operativos de cliente puedo usar para las conexiones de punto a sitio?

Se admiten los siguientes sistemas operativos de cliente:

* Windows 7 (32 bits y 64 bits)
* Windows Server 2008 R2 (solo 64 bits)
* Windows 8 (32 bits y 64 bits)
* Windows 8.1 (32 bits y 64 bits)
* Windows Server 2012 (solo 64 bits)
* Windows Server 2012 R2 (solo 64 bits)
* Windows Server 2016 (solo 64 bits)
* Windows 10
* OSX versión 10.11 para Mac (El Capitan)
* macOS versión 10.12 para Mac (Sierra)

### <a name="how-many-vpn-client-endpoints-can-i-have-in-my-point-to-site-configuration"></a>¿Cuántos puntos de conexión de cliente VPN puedo tener en mi configuración punto a sitio?

Se admiten hasta 128 clientes VPN para poder conectarse al mismo tiempo a una red virtual.

### <a name="can-i-traverse-proxies-and-firewalls-using-point-to-site-capability"></a>¿Puedo atravesar servidores proxy y firewalls con la funcionalidad de punto a sitio?

Azure admite dos tipos de opciones de VPN de punto a sitio:

* Protocolo de túnel de sockets seguros (SSTP). SSTP es una solución basada en SSL propietaria de Microsoft que puede atravesar firewalls, puesto que la mayoría de los firewalls abren el puerto TCP 443 que utiliza SSL.

* VPN IKEv2. VPN IKEv2 es una solución de VPN con IPsec basada en estándares que utiliza los puertos UDP 500 y 4500 y protocolo IP. 50. Los firewalls no siempre abren estos puertos, por lo que hay una posibilidad de que la VPN IKEv2 no pueda atravesar servidores proxy y firewalls.

### <a name="if-i-restart-a-client-computer-configured-for-point-to-site-will-the-vpn-automatically-reconnect"></a>¿Si reinicio un equipo cliente con configuración de punto a sitio, se volverá a conectar la VPN de forma automática?

De forma predeterminada, el equipo cliente no volverá a establecer la conexión VPN automáticamente.

### <a name="does-point-to-site-support-auto-reconnect-and-ddns-on-the-vpn-clients"></a>¿Admite la configuración de punto a sitio la reconexión automática y el DDNS en los clientes VPN?

Las VPN de punto a sitio no admiten la reconexión automática y el DDNS.

### <a name="can-i-have-site-to-site-and-point-to-site-configurations-coexist-for-the-same-virtual-network"></a>¿Puedo tener configuraciones de sitio a sitio y de punto a sitio coexistiendo en la misma red virtual?

Sí. Para el modelo de implementación de Resource Manager, debe tener un tipo de VPN basada en ruta para la puerta de enlace. Para el modelo de implementación clásica, necesita una puerta de enlace dinámica. No se admite la configuración de punto a sitio para puertas de enlace de VPN de enrutamiento estático o puertas de enlace de VPN basadas en directivas.

### <a name="can-i-configure-a-point-to-site-client-to-connect-to-multiple-virtual-networks-at-the-same-time"></a>¿Puedo configurar un cliente de punto a sitio para conectarse a varias redes virtuales al mismo tiempo?

Nº Un cliente de punto a sitio solo puede conectarse a los recursos de la red virtual en la que reside la puerta de enlace de red virtual.

### <a name="how-much-throughput-can-i-expect-through-site-to-site-or-point-to-site-connections"></a>¿Qué rendimiento puedo esperar en las conexiones de sitio a sitio o de punto a sitio?

Es difícil de mantener el rendimiento exacto de los túneles VPN. IPsec y SSTP son protocolos VPN con mucho cifrado. El rendimiento también está limitado por la latencia y el ancho de banda entre sus instalaciones locales e Internet. Para una puerta de enlace de VPN únicamente con conexiones VPN de punto a sitio IKEv2, el rendimiento total que puede esperar depende de la SKU de puerta de enlace. Consulte [SKU de puerta de enlace](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku) para más información sobre el rendimiento.

### <a name="can-i-use-any-software-vpn-client-for-point-to-site-that-supports-sstp-andor-ikev2"></a>¿Puedo usar cualquier software de cliente VPN para punto a sitio que admita SSTP o IKEv2?

Nº Solo puede usar el cliente VPN nativo en Windows para SSTP y el cliente VPN nativo en Mac para IKEv2. Consulte la lista de sistemas operativos cliente compatibles.

### <a name="does-azure-support-ikev2-vpn-with-windows"></a>¿Azure admite VPN IKEv2 con Windows?

IKEv2 se admite en Windows 10 y Server 2016. Sin embargo, para poder usar IKEv2, debe instalar las actualizaciones y establecer un valor de clave del Registro localmente. No se admiten las versiones de sistemas operativos anteriores a Windows 10 y solo pueden usar SSTP.

Para preparar Windows 10 o Server 2016 para IKEv2:

1. Instale la actualización.

  | Versión del SO | Date | Número/vínculo |
  |---|---|---|---|
  | Windows Server 2016<br>Windows 10 Versión 1607 | 17 de enero de 2018 | [KB4057142](https://support.microsoft.com/help/4057142/windows-10-update-kb4057142) |
  | Windows 10 Versión 1703 | 17 de enero de 2018 | [KB4057144](https://support.microsoft.com/help/4057144/windows-10-update-kb4057144) |
  |  |  |  |  |

2. Establezca el valor de clave del Registro. Cree o establezca la clave REG_DWORD “HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\RasMan\ IKEv2\DisableCertReqPayload” del Registro en 1.

### <a name="what-happens-when-i-configure-both-sstp-and-ikev2-for-p2s-vpn-connections"></a>¿Qué ocurre cuando se configuran SSTP e IKEv2 para conexiones VPN de P2S?

Cuando configure tanto SSTP como IKEv2 en un entorno mixto (que consiste en dispositivos Windows y Mac), el cliente VPN de Windows siempre probará primero el túnel de IKEv2, pero volverá a SSTP si la conexión con IKEv2 no se realiza correctamente. MacOSX solo se conectará a través de IKEv2.

### <a name="other-than-windows-and-mac-which-other-platforms-does-azure-support-for-p2s-vpn"></a>Además de Windows y Mac, ¿qué otras plataformas Azure admite para VPN de P2S?

Azure solo admite Windows y Mac para VPN de P2S.

### <a name="i-already-have-an-azure-vpn-gateway-deployed-can-i-enable-radius-andor-ikev2-vpn-on-it"></a>Ya tengo implementada una instancia de Azure VPN Gateway. ¿Puedo habilitar VPN de IKEv2 o RADIUS en ella?

Sí, puede habilitar estas características nuevas en puertas de enlace ya implementadas mediante Powershell o Azure Portal, siempre que la SKU de la puerta de enlace que use admita RADIUS o IKEv2. Por ejemplo, la SKU de nivel Básico de VPN Gateway no admite RADIUS ni IKEv2.
