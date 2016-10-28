
Para obtener más detalles acerca de los discos, consulte [Acerca de los discos y discos duros virtuales para máquinas virtuales](../articles/virtual-machines/virtual-machines-linux-about-disks-vhds.md).

<a id="attachempty"></a>
## Conectar un disco vacío

1.  Abra la CLI de Azure y [conéctese a su suscripción de Azure](../articles/xplat-cli-connect.md). Procure estar en el modo de administración de servicios de Azure (`azure config mode asm`).

2.  Escriba `azure vm disk attach-new` para crear y asociar un nuevo disco, tal y como se muestra aquí. Reemplace _TestVM_ por el nombre de la máquina virtual con Linux y especifique el tamaño del disco en GB (100 GB en este ejemplo):

        azure vm disk attach-new TestVM 100

3.	Una vez creado y conectado el disco de datos, este aparece en la salida de `azure vm disk list <virtual-machine-name>` de esta manera:

        $ azure vm disk list TestVM
        info:    Executing command vm disk list
        + Fetching disk images
        + Getting virtual machines
        + Getting VM disks
        data:    Lun  Size(GB)  Blob-Name                         OS
        data:    ---  --------  --------------------------------  -----
        data:         30        TestVM-2645b8030676c8f8.vhd  Linux
        data:    0    100       TestVM-76f7ee1ef0f6dddc.vhd
        info:    vm disk list command OK

<a id="attachexisting"></a>
## un disco existente

El acoplamiento de un disco existente requiere que disponga de un .vhd disponible en la cuenta de almacenamiento.

1. 	Abra la CLI de Azure y [conéctese a su suscripción de Azure](../articles/xplat-cli-connect.md). Procure estar en el modo de administración de servicios de Azure (`azure config mode asm`).

2.	Compruebe si el VHD que quiere asociar ya se ha cargado en la suscripción de Azure:

        $azure vm disk list
    	info:    Executing command vm disk list
    	+ Fetching disk images
    	data:    Name                                          OS
    	data:    --------------------------------------------  -----
    	data:    myTestVhd                                     Linux
    	data:    TestVM-ubuntuVMasm-0-201508060029150744  Linux
    	data:    TestVM-ubuntuVMasm-0-201508060040530369
    	info:    vm disk list command OK

3.  Si no encuentras el disco que quieres usar, puedes cargar un VHD local a tu suscripción mediante `azure vm disk create` o `azure vm disk upload`. Un ejemplo de `disk create` sería el siguiente:

        $azure vm disk create myTestVhd2 .\TempDisk\test.VHD -l "East US" -o Linux
		info:    Executing command vm disk create
		+ Retrieving storage accounts
		info:    VHD size : 10 GB
		info:    Uploading 10485760.5 KB
		Requested:100.0% Completed:100.0% Running:   0 Time:   25s Speed:    82 KB/s
		info:    Finishing computing MD5 hash, 16% is complete.
		info:    https://mystorageaccount.blob.core.windows.net/disks/test.VHD was
		uploaded successfully
		info:    vm disk create command OK

	También puede usar el comando `azure vm disk upload` para cargar un VHD a una cuenta de almacenamiento específica. [Aquí](virtual-machines-command-line-tools.md#commands-to-manage-your-azure-virtual-machine-data-disks) puedes obtener más información sobre los comandos para administrar los discos de datos de tu máquina virtual de Azure.

4.  Ahora conectamos el VHD que queramos a la máquina virtual:

		$azure vm disk attach TestVM myTestVhd
		info:    Executing command vm disk attach
		+ Getting virtual machines
		+ Adding Data-Disk
		info:    vm disk attach command OK

	Procure reemplazar _TestVM_ por el nombre de su máquina virtual y _myTestVhd_ por el VHD que quiera.

5.	Puede comprobar si el disco está conectado a la máquina virtual con `azure vm disk list <virtual-machine-name>`:

		$azure vm disk list TestVM
		info:    Executing command vm disk list
		+ Fetching disk images
		+ Getting virtual machines
		+ Getting VM disks
		data:    Lun  Size(GB)  Blob-Name                         OS
		data:    ---  --------  --------------------------------  -----
		data:         30        TestVM-2645b8030676c8f8.vhd  Linux
		data:    1    10        test.VHD
		data:    0    100        TestVM-76f7ee1ef0f6dddc.vhd
		info:    vm disk list command OK


> [AZURE.NOTE]
Después de agregar un disco de datos, tendrá que iniciar sesión en la máquina virtual e inicializar el disco para que la máquina virtual pueda usar dicho disco para el almacenamiento (consulte los pasos descritos aquí para obtener más información sobre cómo llevar esto a cabo).
