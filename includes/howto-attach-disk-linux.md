
Para obtener más detalles acerca de los discos, consulte [Acerca de los discos y discos duros virtuales para máquinas virtuales](../articles/virtual-machines/virtual-machines-linux-about-disks-vhds.md).

<a id="attachempty"></a>
## Acoplamiento de un disco vacío
El acoplamiento de un disco vacío supone el método más sencillo de agregar un disco de datos, porque Azure crea el archivo .vhd en su lugar y lo almacena en la cuenta de almacenamiento.

1.  Abra la CLI de Azure y [conéctese a su suscripción de Azure](../articles/xplat-cli-connect.md). Asegúrese de que se encuentra en el modo de administración de servicios de Azure (`azure config mode asm`).

2.  Escriba `azure vm disk attach-new` para crear y asociar un nuevo disco, tal y como se muestra a continuación. Reemplace _ubuntuVMasm_ por el nombre de la máquina virtual Linux. Después, especifique el tamaño del disco en GB; en este ejemplo es 30 GB.

        azure vm disk attach-new ubuntuVMasm 30

3.	Una vez que se cree y asocie el disco de datos, se mostrará en la salida de `azure vm disk list
    <virtual-machine-name>`:

        $ azure vm disk list ubuntuVMasm
        info:    Executing command vm disk list
        + Fetching disk images
        + Getting virtual machines
        + Getting VM disks
        data:    Lun  Size(GB)  Blob-Name                         OS
        data:    ---  --------  --------------------------------  -----
        data:         30        ubuntuVMasm-2645b8030676c8f8.vhd  Linux
        data:    0    30        ubuntuVMasm-76f7ee1ef0f6dddc.vhd
        info:    vm disk list command OK

<a id="attachexisting"></a>
## Acoplamiento de un disco existente

El acoplamiento de un disco existente requiere que disponga de un .vhd disponible en la cuenta de almacenamiento.

1. 	Abra la CLI de Azure y [conéctese a su suscripción de Azure](../articles/xplat-cli-connect.md). Asegúrese de que se encuentra en el modo de administración de servicios de Azure (`azure config mode asm`).

2.	Compruebe si el VHD que quiere asociar ya se ha cargado en la suscripción de Azure:

        $azure vm disk list
    	info:    Executing command vm disk list
    	+ Fetching disk images
    	data:    Name                                          OS
    	data:    --------------------------------------------  -----
    	data:    myTestVhd                                     Linux
    	data:    ubuntuVMasm-ubuntuVMasm-0-201508060029150744  Linux
    	data:    ubuntuVMasm-ubuntuVMasm-0-201508060040530369
    	info:    vm disk list command OK

3.  Si no encuentras el disco que quieres usar, puedes cargar un VHD local a tu suscripción mediante `azure vm disk create` o `azure vm disk upload`. Un ejemplo de `disk create` sería:

        $azure vm disk create myTestVhd2 .\TempDisk\test.VHD -l "East US" -o Linux
		info:    Executing command vm disk create
		+ Retrieving storage accounts
		info:    VHD size : 10 GB
		info:    Uploading 10485760.5 KB
		Requested:100.0% Completed:100.0% Running:   0 Time:   25s Speed:    82 KB/s
		info:    Finishing computing MD5 hash, 16% is complete.
		info:    https://portalvhdsq1s6mc7mqf4gn.blob.core.windows.net/disks/test.VHD was
		uploaded successfully
		info:    vm disk create command OK

	También puede usar el comando `azure vm disk upload` para cargar un VHD a una cuenta de almacenamiento específica. [Aquí](virtual-machines-command-line-tools.md#commands-to-manage-your-azure-virtual-machine-data-disks) puedes obtener más información sobre los comandos para administrar los discos de datos de tu máquina virtual de Azure.

4.  Ahora queremos asociar el VHD deseado a la máquina virtual:

		$azure vm disk attach ubuntuVMasm myTestVhd
		info:    Executing command vm disk attach
		+ Getting virtual machines
		+ Adding Data-Disk
		info:    vm disk attach command OK

	Asegúrate de reemplazar _ubuntuVMasm_ por el nombre de la máquina virtual y _myTestVhd_ por el disco duro virtual que quieres.

5.	Puedes comprobar si el disco está asociado a la máquina virtual con `azure vm disk list
 	<virtual-machine-name>`:

		$azure vm disk list ubuntuVMasm
		info:    Executing command vm disk list
		+ Fetching disk images
		+ Getting virtual machines
		+ Getting VM disks
		data:    Lun  Size(GB)  Blob-Name                         OS
		data:    ---  --------  --------------------------------  -----
		data:         30        ubuntuVMasm-2645b8030676c8f8.vhd  Linux
		data:    1    10        test.VHD
		data:    0    30        ubuntuVMasm-76f7ee1ef0f6dddc.vhd
		info:    vm disk list command OK


> [AZURE.NOTE]
Después de conectar un disco de datos, tendrá que iniciar sesión en la máquina virtual e inicializar el disco para que la máquina virtual pueda usar el disco para el almacenamiento.

<!---HONumber=AcomDC_0406_2016-->