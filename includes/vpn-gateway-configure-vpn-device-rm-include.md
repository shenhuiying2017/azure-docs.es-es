Las conexiones de sitio a sitio a una red local requieren un dispositivo VPN. Aunque no se proporcionan todos los pasos de configuración para todos los dispositivos VPN, se puede encontrar información en los siguientes vínculos útiles:

- Para obtener más información sobre dispositivos VPN compatibles, vea [Dispositivos VPN](../articles/vpn-gateway/vpn-gateway-about-vpn-devices.md). 
- Para obtener vínculos a los valores de configuración de dispositivo, vea [Dispositivos VPN validados](../articles/vpn-gateway/vpn-gateway-about-vpn-devices.md#devicetable). Los vínculos de la configuración de dispositivo se proporcionan dentro de lo posible. Siempre es mejor ponerse en contacto con el fabricante del dispositivo para obtener la información de configuración más reciente.
- Para obtener información sobre cómo modificar los ejemplos de configuración de dispositivo, vea [Edición de ejemplos](../articles/vpn-gateway/vpn-gateway-about-vpn-devices.md#editing).
- Para los parámetros de IPsec/IKE, vea [Parámetros](../articles/vpn-gateway/vpn-gateway-about-vpn-devices.md#ipsec).
- Antes de configurar el dispositivo VPN, compruebe si hay [problemas conocidos de compatibilidad de dispositivos](../articles/vpn-gateway/vpn-gateway-about-vpn-devices.md#known) para el dispositivo VPN que desea usar.

Al configurar el dispositivo VPN, necesita lo siguiente:

- Una clave compartida. Se trata de la misma clave compartida que se especifica al crear la conexión VPN de sitio a sitio. En estos ejemplos se utiliza una clave compartida básica. Se recomienda que genere y utilice una clave más compleja.
- La dirección IP pública de la puerta de enlace de red virtual. Puede ver la dirección IP pública mediante Azure Portal, PowerShell o la CLI.