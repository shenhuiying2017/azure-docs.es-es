Las conexiones de sitio a sitio a una red local requieren un dispositivo VPN. Hay muchos dispositivos VPN diferentes que funcionan con Azure. Para obtener información acerca de los dispositivos VPN y de la configuración, consulte [Dispositivos VPN](../articles/vpn-gateway/vpn-gateway-about-vpn-devices.md). Antes de configurar el dispositivo VPN, compruebe si hay [problemas conocidos de compatibilidad de dispositivos](../articles/vpn-gateway/vpn-gateway-about-vpn-devices.md#known) para el dispositivo VPN que desea usar. Para más información sobre la configuración del dispositivo VPN específica, trabaje con el fabricante del dispositivo.

Al configurar el dispositivo VPN, necesitará los elementos siguientes:

- **La dirección IP pública** de la puerta de enlace de red virtual.

    -  Para buscar la dirección IP pública mediante Azure Portal, vaya a **Puertas de enlace de red virtual** y haga clic en el nombre de la puerta de enlace. 

    - Para buscar la dirección IP pública de la puerta de enlace de red virtual con PowerShell, utilice el ejemplo siguiente, reemplazando los valores por los suyos propios.

            Get-AzureRmPublicIpAddress -Name GW1PublicIP -ResourceGroupName TestRG
- **Una clave compartida**. Se trata de la misma clave compartida que se va a especificar al crear la conexión VPN de sitio a sitio. En nuestros ejemplos, se utiliza una clave compartida muy básica. Debe generar una clave más compleja para su uso.




