1. En el portal, en **Todos los recursos**, haga clic en **+Agregar**. En el cuadro de búsqueda de la hoja **Todo**, escriba **Puerta de enlace de red local** y, a continuación, haga clic para buscar. Esto devolverá una lista. Haga clic en **Puerta de enlace de red local** para abrir la hoja, a continuación, haga clic en **Crear** para abrir la hoja **Crear puerta de enlace de red local**.
   
    ![crear una puerta de enlace de red local](./media/vpn-gateway-add-lng-rm-portal-include/newlng.png)

2. En la hoja **Crear puerta de enlace de red local**, especifique un **Nombre** para el objeto de puerta de enlace de red local.
3. Especifique una **dirección IP** pública válida para el dispositivo VPN o la puerta de enlace de red virtual a la que desea conectarse.<br>Si esta red local representa una ubicación local, será dirección IP pública del dispositivo VPN al que quiere conectarse. No puede encontrarse detrás de NAT y debe estar al alcance de Azure.<br>Si esta red local representa otra red virtual, especificará la dirección IP pública que se asignó a la puerta de enlace de red virtual de esa red virtual.<br>
4. **Espacio de direcciones** hace referencia a los intervalos de direcciones de la red que representa esta red local. Puede agregar varios intervalos de espacios de direcciones. Asegúrese de que los intervalos que especifique aquí no se superpongan con los de otras redes a las que quiera conectarse.
5. En **Suscripción**, compruebe que se muestra la suscripción correcta.
6. En **Grupo de recursos**, seleccione el grupo de recursos que desea utilizar. Puede crear un grupo de recursos nuevo o seleccionar uno ya creado.
7. En **Ubicación**, seleccione la ubicación en la que se creará este objeto. Puede seleccionar la misma ubicación en la que reside la red, pero no es obligatorio.
8. Haga clic en **Crear** para crear la puerta de enlace de red local.



<!--HONumber=Jan17_HO3-->


