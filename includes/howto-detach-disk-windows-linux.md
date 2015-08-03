
Cuando ya no necesite un disco de datos que se encuentra conectado a una máquina virtual, puede desconectarlo fácilmente. Esto quita el disco de la máquina virtual, pero no lo quita del almacenamiento. Si desea volver a usar los datos existentes en el disco, puede acoplarlo de nuevo a la misma máquina virtual (o a otra).

> [AZURE.NOTE]Una máquina virtual en Azure utiliza distintos tipos de discos, como un disco del sistema operativo, un disco temporal local y discos de datos opcionales. Se recomienda utilizar discos de datos para almacenar datos para una máquina virtual. Para obtener más información, vea [Acerca de los discos y discos duros virtuales para máquinas virtuales](../../virtual-machines-disks-vhds.md). No es posible desconectar un disco del sistema operativo a menos que también elimine la máquina virtual.

## Buscar el disco##

Si no conoce el nombre del disco o desea comprobarlo antes de desconectarlo, siga estos pasos.

> [AZURE.NOTE]Azure asigna automáticamente un nombre al disco en el momento de acoplarlo. El nombre consta del nombre del servicio en la nube, el nombre de la máquina virtual y un número.

1. Si aún no lo ha hecho, inicie sesión en el [Portal de Azure](http://manage.windowsazure.com).

2. Haga clic en **Máquinas virtuales**, haga clic en el nombre de máquina virtual y, a continuación, haga clic en **Panel**.

3. En **Discos**, la tabla enumera el nombre y el tipo de todos los discos acoplados. Por ejemplo, esta pantalla muestra una máquina virtual con un disco de sistema operativo y un disco de datos:

	![Buscar disco de datos](./media/howto-detach-disk-windows-linux/FindDataDisks.png)


## Desacoplar el disco##

Cuando haya encontrado el nombre del disco, podrá desconectarlo:

1. Haga clic en **Máquinas virtuales**, haga clic en el nombre de la máquina virtual que tiene el disco de datos que desea desconectar y, a continuación, haga clic en **Panel**.
2. En la barra de comandos, haga clic en **Desconectar disco**.

3. Seleccione el disco de datos y, a continuación, haga clic en la marca de verificación para desacoplarlo.

	![Detalles de desacoplamiento del disco](./media/howto-detach-disk-windows-linux/DetachDiskDetails.png)

El disco permanece en el almacenamiento pero ya no está acoplado a una máquina virtual.

<!---HONumber=July15_HO4-->