1. En el portal, vaya a **Nuevo** > **Redes** > **Puerta de enlace de red virtual**. Se abrirá la hoja **Crear puerta de enlace de red virtual**.

	![Puerta de enlace](./media/vpn-gateway-add-gw-rm-portal-include/creategw250.png)

2. En la hoja **Crear puerta de enlace de red virtual**, en el campo **Nombre**, dé un nombre a la puerta de enlace. Esta acción no es igual a la de asignación de un nombre a una subred de puerta de enlace. Este es el nombre del objeto de puerta de enlace que creará.

3. Ajuste el campo **Ubicación** para que apunte a la ubicación en la que se encuentra la red virtual. Si no lo hace, la red virtual no aparecerá en la lista de redes virtuales.
 
4. Después, elija la red virtual a la que quiera agregar esta puerta de enlace. Haga clic en **Red virtual** para abrir la hoja **Elegir una red virtual**. Seleccione la red virtual. Para que la red virtual aparezca en la lista, debe tener ya una subred de puerta de enlace válida.

5. Elija una dirección IP pública. Haga clic en **Dirección de IP pública** para abrir la hoja **Elegir dirección IP pública**. Haga clic en **+Crear nueva** para abrir la hoja **Crear dirección IP pública**. Escriba un nombre para la dirección IP pública. Esto creará un objeto de dirección IP pública al que se le asignará dinámicamente una dirección IP pública. <br>Haga clic en **Aceptar** para guardar los cambios.

5. En **Tipo de puerta de enlace**, seleccione el tipo de puerta de enlace que se especifica para la configuración.

6. En **Tipo de VPN**, seleccione el tipo de VPN que se especifica para la configuración.

7. En **Suscripción**, compruebe que se selecciona la suscripción correcta.

8. El **Grupo de recursos** vendrá determinado por la red virtual que seleccione.

9. No ajuste la **ubicación** después de especificar la configuración anterior.

10. En este punto, la hoja tendrá un aspecto similar al gráfico del paso 1. Compruebe que la configuración coincide con su configuración. Puede seleccionar **Anclar al panel** en la parte inferior de la hoja si quiere que la puerta de enlace aparezca en el panel.

11. Haga clic en **Crear** para comenzar a crear la puerta de enlace. Se validará la configuración y verá el icono "Implementación de la puerta de enlace de red virtual" en el panel. La creación de una puerta de enlace puede tardar hasta 45 minutos. Es posible que tenga que actualizar la página de portal para ver el estado completado.

	![Puerta de enlace](./media/vpn-gateway-add-gw-rm-portal-include/deployvnetgw150.png)

11. Una vez creada la puerta de enlace, puede ver la dirección IP que se le ha asignado consultando la red virtual en el portal. La puerta de enlace aparecerá como un dispositivo conectado. Puede hacer clic en el dispositivo conectado (la puerta de enlace de red virtual) para más información.

<!---HONumber=AcomDC_0810_2016-->