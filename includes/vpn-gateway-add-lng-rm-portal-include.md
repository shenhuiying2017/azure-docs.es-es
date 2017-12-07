1. En el portal, en **Todos los recursos**, haga clic en **+Agregar**.
2. En el cuadro de búsqueda de la página **Todo**, escriba **Puerta de enlace de red local** y después haga clic para devolver una lista de recursos. Haga clic en **Puerta de enlace de red local** para abrir la página y, a continuación, haga clic en **Crear** para abrir la página **Crear puerta de enlace de red local**.

  ![crear una puerta de enlace de red local](./media/vpn-gateway-add-lng-rm-portal-include/lng.png)
3. En la página **Crear puerta de enlace de red local**, especifique los valores de la puerta de enlace de red local.

  - **Nombre:** especifique el nombre del objeto de puerta de enlace de red local. Si es posible, use algo intuitivo, como **ClassicVNetLocal** o **TestVNet1Local**. Esto facilita la identificación de la puerta de enlace de red local en el portal.
  - **Dirección IP:** especifique una **dirección IP** pública válida para el dispositivo VPN o la puerta de enlace de red virtual a la que desea conectarse.

    * **Si esta red local representa una ubicación local:** especifique la dirección IP pública del dispositivo VPN al que quiere conectarse. No puede encontrarse detrás de NAT y debe estar al alcance de Azure.
    * **Si esta red local representa otra red virtual:** especifica la dirección IP pública que se asignó a la puerta de enlace de red virtual de esa red virtual.
    * **Si aún no tiene la dirección IP:** puede crear una dirección IP de marcador de posición válida y luego volver y modificar esta configuración antes de la conexión.
  - **Espacio de direcciones** hace referencia a los intervalos de direcciones de la red que representa esta red local. Puede agregar varios intervalos de espacios de direcciones. Asegúrese de que los intervalos que especifique aquí no se superpongan con los de otras redes a las que quiere conectarse.
  - **Configurar BGP:** solo se debe usar al configurar BGP. En caso contrario, no seleccione esta opción.
  - **Suscripción:** compruebe que se muestra la suscripción correcta.
  - **Grupo de recursos:** seleccione el grupo de recursos que desea utilizar. Puede crear un grupo de recursos nuevo o seleccionar uno ya creado.
  - **Ubicación:** seleccione la ubicación en la que se creará este objeto. Puede seleccionar la misma ubicación en la que reside la red, pero no es obligatorio.
4. Haga clic en **Crear** para crear la puerta de enlace de red local.