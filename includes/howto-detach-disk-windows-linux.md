Cuando ya no necesite un disco de datos que se encuentra conectado a una máquina virtual, puede desconectarlo fácilmente. Esto quita el disco de la máquina virtual, pero no lo quita del almacenamiento. 

Si desea volver a usar los datos existentes en el disco, puede acoplarlo de nuevo a la misma máquina virtual (o a otra).  

> [!NOTE]
> Para desasociar un disco de sistema operativo, primero debe eliminar la máquina virtual.
> 
> 

## <a name="find-the-disk"></a>Buscar el disco
Si no conoce el nombre del disco o desea comprobarlo antes de desconectarlo, siga estos pasos.

1. Inicie sesión en el [Portal de Azure clásico](http://manage.windowsazure.com).
2. Haga clic en **Virtual Machines**, luego en el nombre de una máquina virtual y, después, haga en **Panel**.
3. En **Discos**, la tabla enumera el nombre y el tipo de todos los discos acoplados. Por ejemplo, esta pantalla muestra una máquina virtual con un disco de sistema operativo y un disco de datos:
   
    ![Buscar disco de datos](./media/howto-detach-disk-windows-linux/FindDataDisks.png)

## <a name="detach-the-disk"></a>Desacoplar el disco
1. Haga clic en **Virtual Machines**, luego en el nombre de la máquina virtual que tiene el disco de datos que desea desconectar y, después, en **Panel**.
2. En la barra de comandos, haga clic en **Desconectar disco**.
3. Seleccione el disco de datos y, a continuación, haga clic en la marca de verificación para desacoplarlo.
   
    ![Detalles de desacoplamiento del disco](./media/howto-detach-disk-windows-linux/DetachDiskDetails.png)

El disco permanece en el almacenamiento pero ya no está acoplado a una máquina virtual.



<!--HONumber=Feb17_HO3-->


