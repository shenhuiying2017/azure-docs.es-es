

## Uso del portal para mover una máquina virtual a otra suscripción

Puede mover una máquina virtual y sus recursos asociados a otra suscripción mediante el portal.

1. Abra el [Portal de Azure](https://portal.azure.com).
2. Haga clic en **Examinar** > **Máquinas virtuales** y seleccione la máquina virtual que quiere mover de la lista.
	
	![Captura de pantalla de la sección Información esencial donde hace clic en el icono de lápiz para abrir la hoja Mover recursos.](./media/virtual-machines-common-move-vm/move-button.png)
	
3. En la sección **Información esencial**, haga clic en el icono de lápiz **Cambiar suscripción** junto al nombre de la suscripción. Se abrirá la hoja **Mover recursos**.
	
	![Captura de pantalla de la hoja Mover recursos.](./media/virtual-machines-common-move-vm/move.png)
	
4. Seleccione cada uno de los recursos que quiere mover. En la mayoría de los casos, debe mover todos los recursos opcionales que se muestran.
5. Seleccione la **suscripción** adonde quiere mover la máquina virtual.
6. Seleccione un **grupo de recursos** existente o escriba un nombre para que se cree uno nuevo.
7. Cuando haya terminado, seleccione que comprende que se crearán nuevos id. de recursos y que se deberán usar con la máquina virtual cuando se mueva. Después, haga clic en **Aceptar**.

## Uso del portal para mover una máquina virtual a otro grupo de recursos

Puede mover una máquina virtual y sus recursos asociados a otro grupo de recursos mediante el portal.

1. Abra el [Portal de Azure](https://portal.azure.com).
2. Haga clic en **Examinar** > **Grupos de recursos** y seleccione el grupo de recursos que contiene la máquina virtual.
3. En la hoja **Grupo de recursos**, seleccione la opción **Mover** del menú.
	
	![Captura de pantalla del botón Mover del menú Grupos de recursos.](./media/virtual-machines-common-move-vm/move-rg.png)
	
3. En la hoja **Mover recursos**, seleccione los recursos que quiere mover y, luego, escriba el nombre de un grupo de recursos existente o elija crear uno. Cuando haya terminado, seleccione que comprende que se crearán nuevos id. de recursos y que se deberán usar con la máquina virtual cuando se mueva. Después, haga clic en **Aceptar**.
	
	![Captura de pantalla de la hoja Mover recursos.](./media/virtual-machines-common-move-vm/move-rg-list.png)

<!---HONumber=AcomDC_0810_2016-->