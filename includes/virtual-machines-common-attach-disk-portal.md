


## <a name="find-the-virtual-machine"></a>Búsqueda de la máquina virtual
1. Inicie sesión en el [Portal de Azure](https://portal.azure.com/).
2. En el menú del concentrador, haga clic en **Máquinas virtuales**.
3. Seleccione la máquina virtual en la lista.
4. En la hoja Virtual Machines, en **Essentials**, haga clic en **Discos**.
   
    ![Abrir configuración de disco](./media/virtual-machines-common-attach-disk-portal/find-disk-settings.png)

Continúe siguiendo las instrucciones para conectar un nuevo disco o un disco existente.

## <a name="option-1:-attach-a-new-disk"></a>Opción 1: Conectar un nuevo disco
1. En la hoja **Discos**, haga clic en **Adjuntar nuevo**.
2. Revise la configuración predeterminada, actualice según sea necesario y, después, haga clic en **Aceptar**.
   
   ![Revisar configuración de disco](./media/virtual-machines-common-attach-disk-portal/attach-new.png)
3. Una vez que Azure crea el disco y lo adjunta a la máquina virtual, el nuevo disco aparece en la configuración de disco de la máquina virtual en el apartado **Discos de datos**.

## <a name="option-2:-attach-an-existing-disk"></a>Opción 2: Conectar un disco existente
1. En la hoja **Discos**, haga clic en **Adjuntar existente**.
2. En **Adjuntar un disco existente**, haga clic en **Archivo VHD**.
   
   ![Conectar disco existente](./media/virtual-machines-common-attach-disk-portal/attach-existing.png)
3. En **Cuentas de almacenamiento**, seleccione la cuenta y el contenedor que incluyen el archivo .vhd.
   
   ![Buscar ubicación de VHD](./media/virtual-machines-common-attach-disk-portal/find-storage-container.png)
4. Seleccione el archivo .vhd.
5. En **Adjuntar un disco existente**, el archivo que acaba de seleccionar aparece en **Archivo VHD**. Haga clic en **Aceptar**.
6. Una vez que Azure adjunta el disco a la máquina virtual,este aparece en la configuración del disco de la máquina virtual en el apartado **Discos de datos**.

<!--HONumber=Oct16_HO2-->


