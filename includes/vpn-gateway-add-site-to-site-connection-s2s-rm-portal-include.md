1. Busque la puerta de enlace de red virtual.
2. Haga clic en **Conexiones**. En la parte superior de la hoja Conexiones, haga clic en **+ Agregar** para abrir la hoja **Agregar conexión**.
   
    ![Creación de una conexión de sitio a sitio](./media/vpn-gateway-add-site-to-site-connection-s2s-rm-portal-include/connection.png)
3. En la hoja **Agregar conexión**, asigne un **nombre** a la conexión. 
4. En **Tipo de conexión**, seleccione **Sitio a sitio (IPsec)**.
5. En **Puerta de enlace de red virtual**, el valor es fijo porque se conecta desde esta puerta de enlace.
6. En **Puerta de enlace de red local**, haga clic en **Elegir una puerta de enlace de red local** y seleccione la puerta de enlace de red local que desea utilizar. 
7. En **Clave compartida**, el valor que aparece aquí debe coincidir con el que está usando para el dispositivo VPN local. En el ejemplo, hemos usado “abc123” pero puede usar algo más complejo. Lo importante es que el valor que especifique aquí debe ser el mismo que el que se especificó al configurar el dispositivo VPN.
8. Los restantes valores de **Suscripción**, **Grupo de recursos** y **Ubicación** son fijos.
9. Haga clic en **Aceptar** para crear la conexión. El mensaje *Creando la conexión* aparecerá de forma intermitente en la pantalla.
10. Cuando se complete la conexión, apare en la hoja **Conexiones** de su puerta de enlace de red virtual.
    
    ![Creación de una conexión de sitio a sitio](./media/vpn-gateway-add-site-to-site-connection-s2s-rm-portal-include/connectionstatus450.png)

