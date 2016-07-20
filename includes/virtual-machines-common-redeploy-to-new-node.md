## Uso del Portal de Azure

1. Seleccione la máquina virtual que quiere volver a implementar y haga clic en el botón "Volver a implementar" en la hoja "Configuración":

	![Hoja Máquina virtual de Azure](./media/virtual-machines-common-redeploy-to-new-node/vmoverview.png)

2. Haga clic en el botón "Volver a implementar" para confirmar la operación:

	![Hoja Volver a implementar una máquina virtual](./media/virtual-machines-common-redeploy-to-new-node/redeployvm.png)

3. Verá que el **estado** de la máquina virtual cambia a *Actualizando* puesto que la máquina virtual se prepara para implementarse de nuevo:

	![Máquina virtual actualizando](./media/virtual-machines-common-redeploy-to-new-node/vmupdating.png)

4. El **estado** pasa luego a *Iniciando* cuando arranque la máquina virtual en un nuevo host de Azure:

	![Máquina virtual iniciando](./media/virtual-machines-common-redeploy-to-new-node/vmstarting.png)

5. Cuando la máquina virtual finaliza el proceso de arranque, el **estado** vuelve a *En ejecución*, lo que indica que la máquina virtual se ha vuelto a implementar correctamente:

	![Máquina virtual en ejecución](./media/virtual-machines-common-redeploy-to-new-node/vmrunning.png)

<!---HONumber=AcomDC_0706_2016-->