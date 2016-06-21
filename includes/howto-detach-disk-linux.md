Cuando ya no necesite un disco de datos que se encuentra conectado a una máquina virtual, puede desconectarlo fácilmente. Con esto se quita el disco de la máquina virtual, pero no se quita del almacenamiento. Si desea volver a usar los datos existentes en el disco, puede volver a conectarlo a la misma máquina virtual (o a otra).

> [AZURE.NOTE] Una máquina virtual en Azure utiliza distintos tipos de discos, como un disco del sistema operativo, un disco temporal local y discos de datos opcionales. Para obtener más información, vea [Acerca de los discos y discos duros virtuales para máquinas virtuales](../articles/virtual-machines/virtual-machines-linux-about-disks-vhds.md). No es posible desconectar un disco del sistema operativo a menos que también elimine la máquina virtual.

## Buscar el disco

Antes de poder desconectar un disco de una máquina virtual, es necesario conocer el número de unidad lógica (LUN), que es un identificador para el disco que se va a desconectar. Para ello, sigue estos pasos:

1. 	Abra la CLI de Azure y [conéctese a su suscripción de Azure](../articles/xplat-cli-connect.md). Asegúrese de que se encuentra en el modo de administración de servicios de Azure (`azure config mode asm`).

2. 	Compruebe qué discos están conectados a la máquina virtual mediante `azure vm disk list
	<virtual-machine-name>`:

		$azure vm disk list UbuntuVM
		info:    Executing command vm disk list
		+ Fetching disk images
		+ Getting virtual machines
		+ Getting VM disks
		data:    Lun  Size(GB)  Blob-Name                         OS
		data:    ---  --------  --------------------------------  -----
		data:         30        ubuntuVM-2645b8030676c8f8.vhd  Linux
		data:    1    10        test.VHD
		data:    0    30        ubuntuVM-76f7ee1ef0f6dddc.vhd
		info:    vm disk list command OK

3. 	Anota el LUN o **número de unidad lógica** para el disco que quieres desacoplar.


## Desacoplar el disco

Cuando hayas encontrado el número LUN del disco, podrás desacoplarlo:

1. 	Desasocie el disco seleccionado de la máquina virtual ejecutando el comando `azure vm disk detach
 	<virtual-machine-name> <LUN>`:

		$azure vm disk detach UbuntuVM 0
		info:    Executing command vm disk detach
		+ Getting virtual machines
		+ Removing Data-Disk
		info:    vm disk detach command OK

2. 	Puedes comprobar si se desacopló el disco ejecutando este comando:

		$azure vm disk list UbuntuVM
		info:    Executing command vm disk list
		+ Fetching disk images
		+ Getting virtual machines
		+ Getting VM disks
		data:    Lun  Size(GB)  Blob-Name                         OS
		data:    ---  --------  --------------------------------  -----
		data:         30        ubuntuVM-2645b8030676c8f8.vhd  Linux
		data:    1    10        test.VHD
		info:    vm disk list command OK

El disco desacoplado permanece en el almacenamiento pero ya no estará acoplado a una máquina virtual.

<!---HONumber=AcomDC_0608_2016-->