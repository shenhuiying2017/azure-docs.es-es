1. En el portal, navegue a la red virtual a la que desea conectar una puerta de enlace.

2. En la sección **Configuración** de la hoja de redes virtuales, haga clic en **Subredes** para expandir la hoja Subredes.

3. En la hoja **Subredes**, haga clic en **+ Subred de puerta de enlace** en la parte superior. Se abrirá la hoja **Agregar subred**. El **nombre** de la subred se rellenará automáticamente con el valor GatewaySubnet. Este valor es necesario para que Azure reconozca que se trata de subred de puerta de enlace.

	![Adición de la subred de puerta de enlace](./media/vpn-gateway-add-gwsubnet-rm-portal-include/addgwsubnet250.png)

4. Puede cambiar el bloque CIDR de intervalo de direcciones si es necesario. Compruebe los requisitos específicos de la configuración para confirmar el bloque CIDR recomendado.

5. Haga clic en **Aceptar** en la parte inferior de la hoja para crear la subred.

<!---HONumber=AcomDC_0810_2016-->