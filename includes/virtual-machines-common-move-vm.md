
Este artículo le guiará en el procedimiento para mover una máquina virtual entre suscripciones. Esto puede ser útil si originalmente creó una máquina virtual en una suscripción personal y ahora quiere moverla a la suscripción de su empresa para seguir trabajando.

> [AZURE.NOTE] Como parte de esta operación, se crean nuevos id. de recurso. Cuando la máquina virtual se ha movido, debe actualizar sus herramientas y scripts para usar los nuevos id. de recursos.

1. Abra el [Portal de Azure](https://portal.azure.com).
2. Haga clic en **Examinar** > **Máquinas virtuales** y seleccione la máquina virtual que quiere mover de la lista.
	
	![Captura de pantalla de la sección Información esencial donde hace clic en el icono de lápiz para abrir la hoja Mover recursos.](./media/virtual-machines-common-move-vm/move-button.png)
	
3. En la sección **Información esencial**, haga clic en el icono de lápiz **Cambiar suscripción** junto al nombre de la suscripción. Se abrirá la hoja **Mover recursos**.
	
	![Captura de pantalla de la hoja Mover recursos.](./media/virtual-machines-common-move-vm/move.png)
	
4. Seleccione cada uno de los recursos que quiere mover. En la mayoría de los casos, debe mover todos los recursos opcionales que se muestran.
5. Seleccione la **suscripción** adonde quiere mover la máquina virtual.
6. Seleccione un **grupo de recursos** existente o escriba un nombre para que se cree uno nuevo.
7. Cuando haya terminado, seleccione que comprende que se crearán nuevos id. de recursos y que se deberán usar con la máquina virtual cuando se mueva. A continuación, haga clic en **Aceptar**.



## Pasos siguientes

Puede mover muchos tipos diferentes de recursos entre suscripciones y grupos de recursos. Para obtener más información, consulte [Traslado de los recursos a un nuevo grupo de recursos o a una nueva suscripción](../articles/resource-group-move-resources.md).

<!---HONumber=AcomDC_0706_2016-->