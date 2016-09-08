Cuando ya no necesite un disco de datos que se encuentra conectado a una máquina virtual, puede desconectarlo fácilmente. Con esto se quita el disco de la máquina virtual, pero no se quita del almacenamiento. Si desea volver a usar los datos existentes en el disco, puede volver a conectarlo a la misma máquina virtual (o a otra).

> [AZURE.NOTE] Una máquina virtual en Azure utiliza distintos tipos de discos, como un disco del sistema operativo, un disco temporal local y discos de datos opcionales. Para obtener más información, vea [Acerca de los discos y discos duros virtuales para máquinas virtuales](../articles/virtual-machines/virtual-machines-linux-about-disks-vhds.md). No puede desconectar un disco del sistema operativo a menos que también elimine la máquina virtual.


## Buscar el disco

Antes de poder desconectar un disco de una máquina virtual, es necesario conocer el número de unidad lógica (LUN), que es un identificador para el disco que se va a desconectar. Para ello, sigue estos pasos:

1. 	Abra la CLI de Azure y [conéctese a su suscripción de Azure](../articles/xplat-cli-connect.md). Asegúrese de que se encuentra en el modo de administración de servicios de Azure (`azure config mode asm`).

2. 	Compruebe qué discos están conectados a la máquina virtual mediante `azure vm disk list <virtual-machine-name>`:

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

## Supresión de las referencias al sistema operativo en el disco

Antes de desconectar el disco del invitado de Linux, debe asegurarse de que todas las particiones del disco no están en uso. Asegúrese de que el sistema operativo no intenta volver a montarlas después de un reinicio. Estos pasos deshacen la configuración que probablemente se creó al [asociar](../articles/virtual-machines/virtual-machines-linux-classic-attach-disk.md) el disco.

1. Use el comando `lsscsi` para detectar el identificador de dispositivo. `lsscsi` puede instalarse mediante `yum install lsscsi` (en distribuciones basadas en Red Hat) o mediante `apt-get install lsscsi` (en distribuciones basadas en Debian). Puede encontrar el identificador de disco que está buscando utilizando el número de LUN. El último número de la tupla en cada fila es el LUN. En el ejemplo siguiente LUN 0 se asigna a _/dev/sdc_.

			ops@TestVM:~$ lsscsi
			[1:0:0:0]    cd/dvd  Msft     Virtual CD/ROM   1.0   /dev/sr0
			[2:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sda
			[3:0:1:0]    disk    Msft     Virtual Disk     1.0   /dev/sdb
			[5:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sdc
			[5:0:0:1]    disk    Msft     Virtual Disk     1.0   /dev/sdd

2. Use `fdisk -l <disk>` para la detección de las particiones asociadas con el disco que se va a desvincular.
3. 
			$ sudo fdisk -l /dev/sdc
			Disk /dev/sdc: 1098.4 GB, 1098437885952 bytes, 2145386496 sectors
			Units = sectors of 1 * 512 = 512 bytes
			Sector size (logical/physical): 512 bytes / 512 bytes
			I/O size (minimum/optimal): 512 bytes / 512 bytes
			Disk label type: dos
			Disk identifier: 0x5a1d2a1a

			   Device Boot      Start         End      Blocks   Id  System
			/dev/sdc1            2048  2145386495  1072692224   83  Linux

3. Desmonte cada partición enumerada para el disco. En este ejemplo: `$ sudo umount /dev/sdc1`
4. Utilice el comando `blkid` para la detección del UUID de todas las particiones.

			$ sudo blkid
			/dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"
			/dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"
			/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"
			/dev/sdd1: UUID="44444444-4b4b-4c4c-4d4d-4e4e4e4e4e4e" TYPE="ext4
			
5. Quite las entradas del archivo **/etc/fstab** asociado a las rutas de acceso del dispositivo o los UUID de todas las particiones del disco que se va a desvincular. Las entradas de este ejemplo podrían ser:

		UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults   1   2
o

		/dev/sdc1   /datadrive   ext4   defaults   1   2


## Desacoplar el disco

Después de encontrar el número LUN del disco y de quitar las referencias del sistema operativo, está listo para desvincularlo:

1. 	Desasocie el disco seleccionado de la máquina virtual mediante la ejecución del comando `azure vm disk detach
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

<!---HONumber=AcomDC_0824_2016-->