[!INCLUDE [P2S FAQ All](vpn-gateway-faq-p2s-all-include.md)]

### <a name="is-radius-authentication-supported-on-all-azure-vpn-gateway-skus"></a>¿Se admite la autenticación RADIUS en todas las SKU de Azure VPN Gateway?

La autenticación RADIUS es compatible con las SKU VpnGw1, VpnGw2 y VpnGw3. Si usa SKU heredadas, la autenticación RADIUS se admite en SKU estándar y de alto rendimiento. Esta autenticación no es compatible con la SKU de nivel Básico. 
 
### <a name="is-radius-authentication-supported-for-the-classic-deployment-model"></a>¿Es compatible la autenticación RADIUS con el modelo de implementación clásica?
 
No. La autenticación RADIUS no es compatible con el modelo de implementación clásica.
 
### <a name="are-3rd-party-radius-servers-supported"></a>¿Se admiten servidores RADIUS de terceros?

Sí, se admiten.
 
### <a name="what-are-the-connectivity-requirements-to-ensure-that-the-azure-gateway-is-able-to-reach-an-on-premises-radius-server"></a>¿Cuáles son los requisitos de conectividad para garantizar que la puerta de enlace de Azure pueda comunicarse con un servidor RADIUS local?

Se necesita una conexión VPN de sitio a sitio en el sitio local, con las rutas apropiadas configuradas.  
 
### <a name="can-traffic-to-an-on-premises-radius-server-from-the-azure-vpn-gateway-be-routed-over-an-expressroute-connection"></a>¿Puede enrutarse el tráfico a un servidor RADIUS local (desde la instancia de Azure VPN Gateway) a través de una conexión ExpressRoute?

No. Solo puede enrutarse a través de una conexión de sitio a sitio.
 
### <a name="is-there-a-change-in-the-number-of-sstp-connections-supported-with-radius-authentication-what-is-the-maximum-number-of-sstp-and-ikev2-connections-supported"></a>¿Ha cambiado el número de conexiones SSTP admitidas con autenticación RADIUS? ¿Cuál es el número máximo de conexiones SSTP e IKEv2 admitidas?

El número máximo de conexiones SSTP admitidas en una puerta de enlace con autenticación RADIUS no ha cambiado. Sigue siendo 128. El número máximo de conexiones admitidas es 128, independientemente de si la puerta de enlace está configurada para SSTP, IKEv2 o ambas opciones.
 
### <a name="what-is-the-difference-between-doing-certificate-authentication-using-a-radius-server-vs-using-azure-native-certificate-authentication-by-uploading-a-trusted-certificate-to-azure"></a>¿En qué se diferencia la autenticación de certificados con un servidor RADIUS de la realizada con la autenticación mediante certificados nativos de Azure (al cargar un certificado de confianza en Azure)?

En la autenticación de certificados RADIUS, la solicitud de autenticación se reenvía a un servidor RADIUS que realiza la validación del certificado real. Esta opción es útil si desea integrarla con una infraestructura de autenticación de certificados a través de RADIUS de la que ya disponga.
  
Cuando se utiliza Azure para la autenticación de certificados, la instancia de Azure VPN Gateway realiza la validación del certificado. Debe cargar la clave pública del certificado en la puerta de enlace. También puede especificar una lista de certificados revocados que no podrán conectarse.

### <a name="does-radius-authentication-work-with-both-ikev2-and-sstp-vpn"></a>¿La autenticación RADIUS funciona con IKEv2 y SSTP VPN?

Sí, la autenticación RADIUS es compatible con IKEv2 y SSTP VPN.  