### Siga estos pasos para crear una puerta de enlace de red local:

1. En el Portal de Azure, vaya a **Nuevo** **>** **Redes** **>** **Puerta de enlace de red local**.

	![crear una puerta de enlace de red local](./media/vpn-gateway-add-lng-rm-portal-include/addlng250.png)

2. En la hoja **Crear puertas de enlace de red local**, especifique un **nombre** al objeto de puerta de enlace de red local.
 
3. Especifique una **dirección IP** para la puerta de enlace. Esta es la dirección IP del dispositivo VPN externo al que desea conectarse. No puede encontrarse detrás de NAT y debe estar al alcance de Azure.

4. El **espacio de direcciones** se refiere a los intervalos de direcciones de la red local. Puede agregar varios intervalos de espacios de direcciones. Los intervalos que ingrese aquí no se pueden superponer a ninguno de los intervalos de espacios de direcciones que utiliza para las redes virtuales que se comunicarán a través de la puerta de enlace. Deberá coordinar con la configuración local, además de los espacios de direcciones de la red virtual de Azure.
 
5. En **Suscripción**, compruebe que se muestra la suscripción correcta.

6. En **Grupo de recursos**, seleccione el grupo de recursos que desea utilizar. Puede crear un grupo de recursos nuevo o seleccionar uno ya creado. Para crear un grupo de recursos, escriba el nombre en el cuadro. Si desea seleccionar un grupo de recursos ya creado, haga clic en **Grupo de recursos** para abrir la hoja **Grupo de recursos** y, luego, seleccione el grupo de recursos que desea utilizar.

7. Para **Ubicación**, si está creando una nueva puerta de enlace de red local, puede utilizar la misma ubicación que la puerta de enlace de red virtual. Sin embargo, no es necesario. La puerta de enlace de red local puede estar en otra ubicación.

8. Deje seleccionada la opción "Anclar al panel" si desea acceder fácilmente a esta puerta de enlace de red local desde el panel.

9. Haga clic en **Crear** para crear la puerta de enlace de red local. En el panel, verá "Implementación de la puerta de enlace de red local".

10. Cuando se haya creado la puerta de enlace de red local, se abrirá en el portal para que se pueda ver.

	

<!---HONumber=AcomDC_0406_2016-->