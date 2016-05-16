<properties
	pageTitle="Adición de un disco a una máquina virtual de Linux | Microsoft Azure"
	description="Vea cómo agregar un disco persistente a la máquina virtual Linux"
	keywords="máquina virtual de Linux, agregar disco de recursos"
	services="virtual-machines-linux"
	documentationCenter=""
	authors="rickstercdn"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager" />

<tags
	ms.service="virtual-machines-linux"
	ms.topic="article"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.date="04/29/2016"
	ms.author="rclaus"/>

# Adición de un disco a una máquina virtual de Linux

En este artículo se muestra cómo conectar un disco persistente a la máquina virtual para que se pueden conservar los datos: incluso si la máquina virtual se vuelve a aprovisionar por mantenimiento o cambio de tamaño. Para agregar un disco, necesitará configurar la [CLI de Azure](../xplat-cli-install.md) en modo Resource Manager (`azure config mode arm`).

## Comandos rápidos

En los siguientes ejemplos de comandos, reemplace los valores entre &lt; y &gt; con los valores de su propio entorno.

```bash
azure vm disk attach-new <myuniquegroupname> <myuniquevmname> <size-in-GB>
```

## Conexión de un disco

La asociación de un nuevo disco es algo que se hace rápidamente. Escriba simplemente `azure vm disk attach-new <myuniquegroupname> <myuniquevmname> <size-in-GB>` para crear y vincular un nuevo disco GB para la máquina virtual. Si no identifica explícitamente una cuenta de almacenamiento, cualquier disco que cree se coloca en la misma cuenta de almacenamiento donde reside el disco del sistema operativo. Debe tener un aspecto similar al siguiente:

```bash
azure vm disk attach-new myuniquegroupname myuniquevmname 5
```

Salida

```bash
info:    Executing command vm disk attach-new
+ Looking up the VM "myuniquevmname"
info:    New data disk location: https://cliexxx.blob.core.windows.net/vhds/myuniquevmname-20150526-0xxxxxxx43.vhd
+ Updating VM "myuniquevmname"
info:    vm disk attach-new command OK
```

## Conexión a la máquina virtual con Linux para montar el nuevo disco

> [AZURE.NOTE] En este tema se conecta a una máquina virtual mediante nombres de usuario y contraseñas. Para utilizar pares de claves públicas y privadas para comunicarse con la máquina virtual, consulte [Utilización de SSH con Linux en Azure](virtual-machines-linux-ssh-from-linux.md). Puede modificar la conectividad de **SSH** de las máquinas virtuales creadas con el comando `azure vm quick-create` mediante el comando `azure vm reset-access` para restablecer completamente el acceso de **SSH**, agregar o quitar usuarios o agregar archivos de claves públicas para proteger el acceso.

Necesitará SSH en la máquina virtual de Azure para particionar, formatear y montar el disco nuevo para que la máquina virtual con Linux pueda usarlo. Si no está familiarizado con la conexión con **ssh**, el comando toma la forma `ssh <username>@<FQDNofAzureVM> -p <the ssh port>` y tiene el aspecto siguiente:

```bash
ssh ops@myuni-westu-1432328437727-pip.westus.cloudapp.azure.com -p 22
```

Salida

```bash
The authenticity of host 'myuni-westu-1432328437727-pip.westus.cloudapp.azure.com (191.239.51.1)' can't be established.
ECDSA key fingerprint is bx:xx:xx:xx:xx:xx:xx:xx:xx:x:x:x:x:x:x:xx.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'myuni-westu-1432328437727-pip.westus.cloudapp.azure.com,191.239.51.1' (ECDSA) to the list of known hosts.
ops@myuni-westu-1432328437727-pip.westus.cloudapp.azure.com's password:
Welcome to Ubuntu 14.04.2 LTS (GNU/Linux 3.16.0-37-generic x86_64)

* Documentation:  https://help.ubuntu.com/

System information as of Fri May 22 21:02:32 UTC 2015

System load: 0.37              Memory usage: 2%   Processes:       207
Usage of /:  41.4% of 1.94GB   Swap usage:   0%   Users logged in: 0

Graph this data and manage this system at:
  https://landscape.canonical.com/

Get cloud support with Ubuntu Advantage Cloud Guest:
  http://www.ubuntu.com/business/services/cloud

0 packages can be updated.
0 updates are security updates.

The programs included with the Ubuntu system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
applicable law.

ops@myuniquevmname:~$
```

Ahora que está conectado a la máquina virtual, está listo para conectar un disco. Primero, busquemos el disco con `dmesg | grep SCSI` (el método usado para detectar el nuevo disco puede variar). En este caso, es similar a lo siguiente:

```bash
dmesg | grep SCSI
```

Salida

```bash
[    0.294784] SCSI subsystem initialized
[    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
[    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
[    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
[ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
```

y en el caso de este tema, el disco `sdc` es el que queremos. Ahora, particionamos el disco con `sudo fdisk /dev/sdc`, suponiendo en su caso que el disco estaba `sdc`, lo convertimos en un disco principal en la partición 1 y aceptamos los demás valores predeterminados.

```bash
sudo fdisk /dev/sdc
```

Salida

```bash
Device contains neither a valid DOS partition table, nor Sun, SGI or OSF disklabel
Building a new DOS disklabel with disk identifier 0x2a59b123.
Changes will remain in memory only, until you decide to write them.
After that, of course, the previous content won't be recoverable.

Warning: invalid flag 0x0000 of partition table 4 will be corrected by w(rite)

Command (m for help): n
Partition type:
   p   primary (0 primary, 0 extended, 4 free)
   e   extended
Select (default p): p
Partition number (1-4, default 1): 1
First sector (2048-10485759, default 2048):
Using default value 2048
Last sector, +sectors or +size{K,M,G} (2048-10485759, default 10485759):
Using default value 10485759
```

Para crear la partición, escribimos `p` en el símbolo del sistema:

```bash
Command (m for help): p

Disk /dev/sdc: 5368 MB, 5368709120 bytes
255 heads, 63 sectors/track, 652 cylinders, total 10485760 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk identifier: 0x2a59b123

   Device Boot      Start         End      Blocks   Id  System
/dev/sdc1            2048    10485759     5241856   83  Linux

Command (m for help): w
The partition table has been altered!

Calling ioctl() to re-read partition table.
Syncing disks.
```

Y escribimos un sistema de archivos en la partición mediante el comando **mkfs**, especificando el tipo de sistema de archivos y el nombre del dispositivo. En este tema, estamos usando `ext4` y `/dev/sdc1` mostrados anteriormente:

```bash
sudo mkfs -t ext4 /dev/sdc1
```

Salida

```bash
mke2fs 1.42.9 (4-Feb-2014)
Discarding device blocks: done
Filesystem label=
OS type: Linux
Block size=4096 (log=2)
Fragment size=4096 (log=2)
Stride=0 blocks, Stripe width=0 blocks
327680 inodes, 1310464 blocks
65523 blocks (5.00%) reserved for the super user
First data block=0
Maximum filesystem blocks=1342177280
40 block groups
32768 blocks per group, 32768 fragments per group
8192 inodes per group
Superblock backups stored on blocks:
	32768, 98304, 163840, 229376, 294912, 819200, 884736
Allocating group tables: done
Writing inode tables: done
Creating journal (32768 blocks): done
Writing superblocks and filesystem accounting information: done
```

Cree un directorio para montar el nuevo sistema de archivos mediante `mkdir`:

```bash
sudo mkdir /datadrive
```

Y monte el directorio con `mount`:

```bash
sudo mount /dev/sdc1 /datadrive
```

El disco de datos está ahora listo para usarse como `/datadrive`.

```bash
ls
```

Salida

```bash
bin   datadrive  etc   initrd.img  lib64       media  opt   root  sbin  sys  usr  vmlinuz
boot  dev        home  lib         lost+found  mnt    proc  run   srv   tmp  var
```

> [AZURE.NOTE] También se puede conectar a la máquina virtual de Linux mediante una clave SSH para la identificación. Para obtener más información, consulte [Utilización de SSH con Linux en Azure](virtual-machines-linux-ssh-from-linux.md).

## Pasos siguientes

- Recuerde que el nuevo disco no estará disponible normalmente en la máquina virtual si esta se reinicia a menos que escriba dicha información en su archivo [fstab](http://en.wikipedia.org/wiki/Fstab).
- Revise las recomendaciones para [optimizar el rendimiento de la máquina Linux](virtual-machines-linux-optimization.md) para asegurarse de que la máquina virtual Linux está configurada correctamente.
- Amplíe la capacidad de almacenamiento mediante la adición de discos adicionales y [configure RAID](virtual-machines-linux-configure-raid.md) para obtener un mayor rendimiento.

<!---HONumber=AcomDC_0504_2016-->