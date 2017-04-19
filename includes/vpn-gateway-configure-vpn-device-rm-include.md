Las conexiones de sitio a sitio a una red local requieren un dispositivo VPN. Aunque no se proporcionan todos los pasos de configuración para todos los dispositivos VPN, se puede encontrar información en los siguientes vínculos útiles:

- Para obtener más información sobre dispositivos VPN compatibles, vea [Dispositivos VPN](../articles/vpn-gateway/vpn-gateway-about-vpn-devices.md). 
- Para obtener vínculos a los valores de configuración de dispositivo, vea [Dispositivos VPN validados](../articles/vpn-gateway/vpn-gateway-about-vpn-devices.md#devicetable). Los vínculos de la configuración de dispositivo se proporcionan dentro de lo posible. Siempre es mejor ponerse en contacto con el fabricante del dispositivo para obtener la información de configuración más reciente.
- Para obtener información sobre cómo modificar los ejemplos de configuración de dispositivo, vea [Edición de ejemplos](../articles/vpn-gateway/vpn-gateway-about-vpn-devices.md#editing).
- Para los parámetros de IPsec/IKE, vea [Parámetros](../articles/vpn-gateway/vpn-gateway-about-vpn-devices.md#ipsec).
- Antes de configurar el dispositivo VPN, compruebe si hay [problemas conocidos de compatibilidad de dispositivos](../articles/vpn-gateway/vpn-gateway-about-vpn-devices.md#known) para el dispositivo VPN que desea usar.

Al configurar el dispositivo VPN, necesitará los elementos siguientes:

- La dirección IP pública de la puerta de enlace de red virtual.

    -  Para buscar la dirección IP pública mediante Azure Portal, vaya a **Puertas de enlace de red virtual** y haga clic en el nombre de la puerta de enlace. 
    - Para buscar la dirección IP pública de la puerta de enlace de red virtual con PowerShell, utilice el ejemplo siguiente, reemplazando los valores por los suyos propios.

            Get-AzureRmPublicIpAddress -Name GW1PublicIP -ResourceGroupName TestRG
- Una clave compartida. Se trata de la misma clave compartida que se especifica al crear la conexión VPN de sitio a sitio. En nuestros ejemplos, se utiliza una clave compartida muy básica. Debe generar una clave más compleja para su uso.




