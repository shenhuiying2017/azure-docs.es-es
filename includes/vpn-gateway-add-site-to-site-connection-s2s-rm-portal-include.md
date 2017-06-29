1. Navegue hasta la hoja de la puerta de enlace de la red virtual y ábrala. Hay varias formas de navegar. En nuestro ejemplo, navegamos hasta la puerta de enlace "VNet1GW", para lo que vamos a **TestVNet1 -> Introducción -> Dispositivos conectados -> VNet1GW**.
2. En la hoja de VNet1GW, haga clic en **Conexiones**. En la parte superior de la hoja Conexiones, haga clic en **+ Agregar** para abrir la hoja **Agregar conexión**.

    ![Creación de una conexión de sitio a sitio](./media/vpn-gateway-add-site-to-site-connection-s2s-rm-portal-include/connection1.png)

3. En la hoja **Agregar conexión**, rellene los valores necesarios para crear la conexión.

  - **Nombre**: asigne un nombre a la conexión. En nuestro ejemplo usamos **VNet1toSite2**.
  - **Tipo de conexión:** seleccione **Sitio a sitio (IPSec)**.
  - **Puerta de enlace de red virtual:**  el valor es fijo porque se conecta desde esta puerta de enlace.
  - **Puerta de enlace de red local:** haga clic en **Elegir una puerta de enlace de red local** y seleccione la puerta de enlace de red local que desea utilizar. En el ejemplo, se usa **Site2**.
  - **Clave compartida:** este valor debe ser el mismo que el que usa para el dispositivo VPN local. En el ejemplo, hemos usado “abc123” pero puede usar algo más complejo. Lo importante es que el valor que especifique aquí debe ser el mismo que el que se especificó al configurar el dispositivo VPN.
  - Los restantes valores de **Suscripción**, **Grupo de recursos** y **Ubicación** son fijos.

4. Haga clic en **Aceptar** para crear la conexión. El mensaje *Creando la conexión* aparecerá de forma intermitente en la pantalla.
5. La conexión se puede ver en la hoja **Conexiones** de la puerta de enlace de red virtual. El valor de Estado pasará de *Desconocido* a *Conectando* y luego a *Correcto*.