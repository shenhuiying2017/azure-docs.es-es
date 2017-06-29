1. A la izquierda de la página del portal, haga clic en **+** y escriba "Puerta de enlace de red virtual" en la búsqueda. En **Resultados**, busque y haga clic en **Puerta de enlace de red virtual**.
2. En la parte inferior de la hoja "Puerta de enlace de red virtual", haga clic en **Crear**. Se abrirá la hoja **Crear puerta de enlace de red virtual**.

    ![Campos de la hoja Crear puerta de enlace de red virtual](./media/vpn-gateway-add-gw-s2s-rm-portal-include/vnet_gw.png "Nueva puerta de enlace")

3. En la hoja **Crear puerta de enlace de red virtual**, especifique los valores de la puerta de enlace de red virtual.

  - **Nombre**: asigne un nombre a la puerta de enlace. Esta acción no es igual a la de asignación de un nombre a una subred de puerta de enlace. Este es el nombre del objeto de puerta de enlace que va a crear.
  - **Tipo de puerta de enlace**: seleccione **VPN**. Las puertas de enlace VPN usan el tipo de puerta de enlace de red virtual **VPN**. 
  - **Tipo de VPN**: seleccione el tipo de VPN que se especifica para la configuración. La mayoría de las configuraciones requieren un tipo de VPN basada en enrutamiento.
  - **SKU**: seleccione la SKU de puerta de enlace en la lista desplegable. Las SKU que aparecen en la lista desplegable dependen del tipo de VPN que seleccione. Para más información acerca de las SKU de puerta de enlace, consulte [SKU de puerta de enlace](../articles/vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#gwsku).
  - **Ubicación**: puede que necesite desplazarse para ver la ubicación. Ajuste el campo **Ubicación** para que apunte a la ubicación en la que se encuentra la red virtual. Si la ubicación no apunta a la región en que reside la red virtual, esta no aparecerá en la lista desplegable "Elegir una red virtual" del paso siguiente.
  - **Red virtual**: elija la red virtual a la que quiera agregar esta puerta de enlace. Haga clic en **Red virtual** para abrir la hoja "Elegir una red virtual". Seleccione la red virtual. Si no ve la red virtual, asegúrese de que el campo Ubicación apunta a la región en la que esta se encuentra.
  - **Dirección IP pública**: la hoja "Crear dirección IP pública" crea un objeto de dirección IP pública. La dirección IP pública se asigna dinámicamente cuando se crea la puerta de enlace de VPN. Actualmente, VPN Gateway solo admite la asignación de direcciones IP públicas *dinámicas*. Sin embargo, esto no significa que la dirección IP cambia después de que se ha asignado a una puerta de enlace VPN. La única vez que la dirección IP pública cambia es cuando la puerta de enlace se elimina y se vuelve a crear. No cambia cuando se cambia el tamaño, se restablece o se realizan actualizaciones u otras operaciones de mantenimiento interno de una puerta de enlace VPN.

    - En primer lugar, haga clic en **Dirección IP pública** para abrir la hoja "Elegir dirección IP pública" y, después, haga clic en **+Crear nueva** para abrir la hoja "Crear dirección IP pública".
    - A continuación, escriba el **nombre** de la dirección IP pública y haga clic en **Aceptar** en la parte inferior de esta hoja para guardar los cambios.

      ![Crear dirección IP pública](./media/vpn-gateway-add-gw-s2s-rm-portal-include/pip.png "Crear PIP")

4. Compruebe la configuración. Puede seleccionar **Anclar al panel** en la parte inferior de la hoja si quiere que la puerta de enlace aparezca en el panel. 
5. Haga clic en **Crear** para empezar a crear la puerta de enlace de VPN. Se validará la configuración y verá el icono "Implementación de la puerta de enlace de red virtual" en el panel. La creación de una puerta de enlace puede tardar hasta 45 minutos. Es posible que tenga que actualizar la página de portal para ver el estado completado.

Una vez creada la puerta de enlace, puede ver la dirección IP que se le ha asignado consultando la red virtual en el portal. La puerta de enlace aparecerá como un dispositivo conectado. Puede hacer clic en el dispositivo conectado (la puerta de enlace de red virtual) para más información.