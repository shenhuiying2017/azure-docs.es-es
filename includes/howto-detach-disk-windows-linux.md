Cuando ya no necesite un disco de datos que se encuentra conectado a una máquina virtual, puede desconectarlo fácilmente. Al desasociar un disco se quita de la máquina virtual, pero no se elimina de la cuenta de Azure Storage.

Si desea volver a usar los datos existentes en el disco, puede acoplarlo de nuevo a la misma máquina virtual (o a otra).  

> [!NOTE]
> Para desasociar un disco del sistema operativo, primero debe eliminar la máquina virtual.
>

## <a name="find-the-disk"></a>Buscar el disco
Si no conoce el nombre del disco o desea comprobarlo antes de desconectarlo, siga estos pasos.

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).

2. Haga clic en **Máquinas virtuales**y, a continuación, seleccione la VM correspondiente.

3. Haga clic en **Discos** a lo largo del borde izquierdo del panel de la máquina virtual en **Configuración**.

 El panel de la máquina virtual muestra el nombre y el tipo de todos los discos conectados. Por ejemplo, esta pantalla muestra una máquina virtual con un disco de sistema operativo y un disco de datos:

    ![Buscar disco de datos](./media/howto-detach-disk-windows-linux/vmwithdisklist.png)

## <a name="detach-the-disk"></a>Desacoplar el disco
1. En el portal de Azure, haga clic en **Virtual Machines** y luego en el nombre de la máquina virtual que tiene el disco de datos que desea desasociar.

2. Haga clic en **Discos** a lo largo del borde izquierdo del panel de la máquina virtual en **Configuración**.

3. Haga clic en el disco que quiere desasociar.

  ![Identificación del disco que se va a desasociar](./media/howto-detach-disk-windows-linux/disklist.png)

4. En la barra de comandos, haga clic en **Desasociar**.

  ![Búsqueda del comando detach](./media/howto-detach-disk-windows-linux/diskdetachcommand.png)

5. En la ventana de confirmación, haga clic en **Sí** para desasociar el disco.

  ![Confirmación para desasociar el disco](./media/howto-detach-disk-windows-linux/confirmdetach.png)

El disco permanece en el almacenamiento pero ya no está acoplado a una máquina virtual.
