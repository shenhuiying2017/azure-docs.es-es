### <a name="gwipnoconnection"></a>Para modificar la dirección IP de la puerta de enlace de red local (no hay conexión con la puerta de enlace)

Use el ejemplo para modificar una puerta de enlace de red local que no tenga una conexión de puerta de enlace. Al modificar este valor, también puede modificar al mismo tiempo los prefijos de dirección.

1. En el recurso de la puerta de enlace de red local, en la sección **Configuración**, haga clic en **Configuración**.
2. En el cuadro **Dirección IP**, modifique la dirección IP.
3. Haga clic en **Guardar** para guardar la configuración.

### <a name="gwipwithconnection"></a>Para modificar la dirección IP de la puerta de enlace de red local (conexión de puerta de enlace existente)

Para modificar una puerta de enlace de red local que tenga una conexión, primero es preciso quitar dicha conexión. Después de quitar la conexión, puede modificar la dirección IP de la puerta de enlace y volver a crear una nueva conexión. También puede modificar los prefijos de dirección al mismo tiempo. Esto tendrá como resultado un tiempo de inactividad para la conexión VPN. Al modificar la dirección IP de puerta de enlace, no es necesario eliminar la puerta de enlace VPN. Basta con quitar la conexión.
 
#### <a name="1-remove-the-connection"></a>1. Cierre la conexión.

1. En el recurso de la puerta de enlace de red local, en la sección **Configuración**, haga clic en **Conexiones**.
2. Haga clic en **...** en la línea de la conexión y, después, haga clic en **Eliminar**.
3. Haga clic en **Guardar** para guardar la configuración.

#### <a name="2-modify-the-ip-address"></a>2. Modifique la dirección IP.

También puede modificar los prefijos de dirección al mismo tiempo.

1. En el cuadro **Dirección IP**, modifique la dirección IP.
2. Haga clic en **Guardar** para guardar la configuración.

#### <a name="3-recreate-the-connection"></a>3. Vuelva a crear la conexión.

1. Navegue hasta la puerta de enlace de red virtual de la red virtual. (no la puerta de enlace de red local).
2. En la puerta de enlace de red virtual, en la sección **Configuración**, haga clic en **Conexiones**.
3. Haga clic en **+ Agregar** para abrir la hoja **Agregar conexión**.
4. Vuelva a crear la conexión.
5. Haga clic en **Aceptar** para crear la conexión.