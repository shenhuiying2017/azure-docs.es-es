## <a name="use-the-azure-portal"></a>Uso del Portal de Azure
1. Seleccione la máquina virtual que quiera volver a implementar y el botón *Volver a implementar* en la hoja *Configuración*. Es posible que deba bajar para ver la sección **Soporte y solución de problemas** que contiene el botón Volver a implementar, como en el ejemplo siguiente:
   
    ![Hoja Máquina virtual de Azure](./media/virtual-machines-common-redeploy-to-new-node/vmoverview.png)
2. Para confirmar la operación, haga clic en el botón *Volver a implementar*:
   
    ![Hoja Volver a implementar una máquina virtual](./media/virtual-machines-common-redeploy-to-new-node/redeployvm.png)
3. El valor de **Estado** de la máquina virtual cambia a *Actualizando*, puesto que la máquina virtual se prepara para implementarse de nuevo, como se muestra en el siguiente ejemplo:
   
    ![Máquina virtual actualizando](./media/virtual-machines-common-redeploy-to-new-node/vmupdating.png)
4. El valor de **Estado** pasa luego a *Iniciando* cuando la máquina virtual se inicia en un nuevo host de Azure, como se muestra en el ejemplo siguiente:
   
    ![Máquina virtual iniciando](./media/virtual-machines-common-redeploy-to-new-node/vmstarting.png)
5. Cuando la máquina virtual finaliza el proceso de arranque, el **estado** vuelve a *En ejecución*, lo que indica que la máquina virtual se ha vuelto a implementar correctamente:
   
    ![Máquina virtual en ejecución](./media/virtual-machines-common-redeploy-to-new-node/vmrunning.png)

