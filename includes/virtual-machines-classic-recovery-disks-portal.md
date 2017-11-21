Si la máquina virtual de Azure se encuentra un error de disco o de arranque, deberá realizar los pasos para solucionar problemas en el propio disco duro virtual. Un ejemplo habitual sería una actualización de aplicación con error que impide que la máquina virtual pueda arrancar correctamente. En este artículo se describe cómo utilizar Azure Portal para conectar el disco duro virtual a otra máquina virtual para solucionar errores y, posteriormente, volver a crear la máquina virtual original.


## <a name="recovery-process-overview"></a>Introducción al proceso de recuperación
El proceso de solución de problemas es el siguiente:

1. Elimine la máquina virtual que tiene problemas pero conserve los discos duros virtuales.
2. Asocie y monte el disco duro virtual en otra máquina virtual con el fin de solucionar problemas.
3. Conéctese a la máquina virtual de solución de problemas. Edite los archivos o ejecute las herramientas necesarias para solucionar los problemas del disco duro virtual original.
4. Desmonte y desconecte el disco duro virtual de la máquina virtual de solución de problemas.
5. Cree una máquina virtual mediante el disco duro virtual original.

## <a name="delete-the-original-vm"></a>Eliminación de la máquina virtual original
Los discos duros virtuales y las máquinas virtuales son dos recursos diferentes de Azure. Un disco duro virtual es el recurso donde se almacenan el sistema operativo, las aplicaciones y las configuraciones. La máquina virtual consiste solo en metadatos que definen el tamaño o la ubicación y que hacen referencia a recursos tales como un disco duro virtual o una tarjeta de interfaz de red virtual (NIC). Cada disco duro virtual obtiene una concesión que se le asigna cuando se asocia a una máquina virtual. Aunque los discos de datos se pueden conectar y desconectar incluso mientras se está ejecutando la máquina virtual, no se puede desasociar el disco del sistema operativo, a menos que se elimine el recurso de máquina virtual. La concesión continúa para asociar el disco del sistema operativo a una máquina virtual incluso cuando esa máquina virtual está en un estado detenido o desasignado.

El primer paso para recuperar la máquina virtual es eliminar el propio recurso de máquina virtual. Al eliminar la máquina virtual, los discos duros virtuales se dejan en su cuenta de almacenamiento. Después de eliminar la máquina virtual, puede asociar el disco duro virtual a otra máquina virtual para localizar y solucionar los errores. 

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com). 
2. En el menú de la izquierda, haga clic en **Virtual Machines**.
3. Seleccione la máquina virtual que tiene el problema, haga clic en **Discos** y, a continuación, identifique el nombre del disco duro virtual. 
4. Seleccione el disco duro virtual del sistema operativo y compruebe la **Ubicación** para identificar la cuenta de almacenamiento que contiene este disco duro virtual. En el ejemplo siguiente, la cadena inmediatamente antes de ".blob.core.windows.net" es el nombre de la cuenta de almacenamiento.

    ```
    https://portalvhds73fmhrw5xkp43.blob.core.windows.net/vhds/SCCM2012-2015-08-28.vhd
    ```

    ![Imagen sobre la ubicación de la máquina virtual](./media/virtual-machines-classic-recovery-disks-portal/vm-location.png)

5. Haga clic con el botón derecho en la máquina virtual y, a continuación, seleccione **Eliminar**. Asegúrese de que los discos no estén seleccionados cuando elimine la máquina virtual.
6. Cree una nueva máquina virtual de recuperación. Esta máquina virtual debe estar en la misma región y grupo de recursos (servicio en la nube) que la máquina virtual que tiene problemas.
7. Seleccione la máquina virtual de recuperación y, a continuación, seleccione **Discos** > **Asociar existente**.
8. Para seleccionar el disco duro virtual existente, haga clic en **Archivo VHD**:

    ![Busque el disco duro virtual existente.](./media/virtual-machines-classic-recovery-disks-portal/select-vhd-location.png)

9. Seleccione la cuenta de almacenamiento, el contenedor de discos duros virtuales y el disco duro virtual, y haga clic en el botón **Seleccionar** para confirmar su elección.

    ![Seleccione el disco duro virtual existente](./media/virtual-machines-classic-recovery-disks-portal/select-vhd.png)

10. Con el disco duro virtual ya seleccionado, haga clic en **Aceptar** para asociar el disco duro virtual existente.
11. Después de unos segundos, el panel **Discos** de la máquina virtual mostrará el disco duro virtual existente conectado como un disco de datos:

    ![Disco duro virtual existente conectado como disco de datos](./media/virtual-machines-classic-recovery-disks-portal/attached-disk.png)

## <a name="fix-issues-on-the-original-virtual-hard-disk"></a>Solución de problemas en el disco duro virtual original
Con el disco duro virtual existente montado, ahora puede realizar todos los pasos de mantenimiento y solución de problemas según sea necesario. Una vez que se han resuelto los problemas, continúe con los pasos siguientes.

## <a name="unmount-and-detach-the-original-virtual-hard-disk"></a>Desmontaje y desasociación del disco duro virtual original
Una vez resueltos los errores, desmonte y desasocie el disco duro virtual existente de la máquina virtual de solución de problemas. No se podrá usar el disco duro virtual con ninguna otra máquina virtual hasta que se libere la concesión que asocia el disco duro virtual a la máquina virtual de solución de problemas.  

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com). 
2. En el menú de la izquierda, haga clic en **Virtual Machines (clásico)**.
3. Busque la máquina virtual de recuperación. Seleccione Discos, haga clic con el botón derecho en el disco y, a continuación, seleccione **Desasociar**.

## <a name="create-a-vm-from-the-original-hard-disk"></a>Creación de una máquina virtual a partir del disco duro original

Para crear una máquina virtual a partir del disco duro virtual original, use [Azure Portal](https://portal.azure.com).

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
2. En la parte superior izquierda del portal, seleccione **Nuevo** > **Proceso** > **Máquina virtual** > **De la galería**.
3. En la sección **Elija una imagen**, seleccione **Mis discos** y seleccione el disco duro virtual original. Compruebe la información de ubicación. Esta es la región donde se debe implementar la máquina virtual. Seleccione el botón Siguiente.
4. En la sección **Configuración de la máquina virtual**, escriba el nombre de la máquina virtual y seleccione un tamaño.
