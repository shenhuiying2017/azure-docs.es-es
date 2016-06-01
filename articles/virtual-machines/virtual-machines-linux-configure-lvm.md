<properties 
	pageTitle="Configuración de LVM en una máquina virtual que ejecuta Linux | Microsoft Azure" 
	description="Aprenda a configurar LVM en Linux en Azure." 
	services="virtual-machines-linux" 
	documentationCenter="na" 
	authors="szarkos"  
	manager="timlt" 
	editor="tysonn"
	tag="azure-service-management,azure-resource-manager" />

<tags 
	ms.service="virtual-machines-linux" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-linux" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/06/2016" 
	ms.author="szark"/>


# Configuración del LVM en una máquina virtual Linux en Azure

Este documento describe cómo configurar el administrador de volúmenes lógicos (LVM, Logical Volume Manager) en una máquina virtual de Azure. Aunque el LVM se puede configurar en cualquier disco conectado a la máquina virtual, de forma predeterminada, la mayoría de las imágenes de nube no tendrá el LVM configurado en el disco del sistema operativo. El motivo es evitar problemas con los grupos de volúmenes duplicados si el disco del sistema operativo se conecta en algún momento a otra máquina virtual de la misma distribución y tipo, es decir, durante un escenario de recuperación. Por lo tanto, se recomienda usar el LVM únicamente en los discos de datos.


## Volúmenes lógicos lineales frente a seccionados

El LVM se puede utilizar para combinar diversos discos físicos en un único volumen de almacenamiento. De forma predeterminada, el LVM suele crear volúmenes lógicos lineales, lo que significa que el almacenamiento físico se concatena todo junto. En este caso, las operaciones de lectura y escritura normalmente solo se enviarán a un único disco. En cambio, también podemos crear volúmenes lógicos seccionados en los que las lecturas y escrituras se distribuyan en varios discos del grupo de volúmenes (es decir, similar a RAID0). Por motivos de rendimiento, es probable que deba seccionar los volúmenes lógicos para que las lecturas y escrituras utilicen todos los discos de datos conectados.

Este documento describe cómo combinar varios discos de datos en un único grupo de volúmenes y crear un volumen lógico seccionado. Los pasos siguientes están en cierto modo generalizados para que funcionen con la mayoría de las distribuciones. En la mayoría de los casos, las utilidades y los flujos de trabajo para la administración de LVM en Azure no se diferencian en esencia de los de otros entornos. Como de costumbre, consulte a su proveedor de Linux con el fin de obtener la documentación y los procedimientos recomendados para usar el LVM en una distribución en particular.


## Conexión de discos de datos
Normalmente, cuando utilice el LVM, conviene comenzar al menos dos discos de datos vacíos. Según sus requisitos de E/S, puede decidir asociar discos que estén almacenados en nuestro almacenamiento estándar con hasta 500 E/S por segundo por disco o Almacenamiento Premium con hasta 5000 E/S por segundo por disco. En este artículo no entramaremos en detalles sobre cómo asociar discos de datos a una máquina virtual Linux. Consulte el artículo de Microsoft Azure sobre la [conexión de un disco](virtual-machines-linux-add-disk.md) para obtener instrucciones detalladas acerca de cómo conectar un disco de datos vacío a una máquina virtual con Linux en Azure.

## Instalación de las utilidades del LVM

- **Ubuntu**

		# sudo apt-get update
		# sudo apt-get install lvm2

- **RHEL, CentOS y Oracle Linux**

		# sudo yum install lvm2

- **12 SLES y openSUSE**

		# sudo zypper install lvm2

- **SLES 11**

		# sudo zypper install lvm2

	En SLES11 también debe editar /etc/sysconfig/lvm y establecer `LVM_ACTIVATED_ON_DISCOVERED` como "enable":

		LVM_ACTIVATED_ON_DISCOVERED="enable" 


## Configuración del LVM
En esta guía, se presupone la conexión de tres discos de datos, a los que se llamará `/dev/sdc`, `/dev/sdd` y `/dev/sde`. Tenga en cuenta que estos podrían no presentar siempre los mismos nombres de ruta de acceso en la máquina virtual. Puede ejecutar `sudo fdisk -l` o un comando similar para ver la lista de los discos disponibles.

1. Prepare los volúmenes físicos:

		# sudo pvcreate /dev/sd[cde]
		  Physical volume "/dev/sdc" successfully created
		  Physical volume "/dev/sdd" successfully created
		  Physical volume "/dev/sde" successfully created


2.  Cree un grupo de volúmenes. En este ejemplo, el grupo de volúmenes se llamará "data-vg01":

		# sudo vgcreate data-vg01 /dev/sd[cde]
		  Volume group "data-vg01" successfully created


3. Cree los volúmenes lógicos. El comando siguiente creará un único volumen lógico llamado "data-lv01" para abarcar todo el grupo de volúmenes, pero tenga en cuenta que también se pueden crear varios volúmenes lógicos dentro del grupo de volúmenes.

		# sudo lvcreate --extents 100%FREE --stripes 3 --name data-lv01 data-vg01
		  Logical volume "data-lv01" created.


4. Formatee el volumen lógico.

		# sudo mkfs -t ext4 /dev/data-vg01/data-lv01

  >[AZURE.NOTE] En SLES11, utilice -t ext3 en vez de ext4. SLES11 admite únicamente el acceso de solo lectura a los sistemas de archivos ext4.


## Incorporación del nuevo sistema de archivos a /etc/fstab

**Precaución:** la edición incorrecta del archivo /etc/fstab puede tener como resultado un sistema que no se pueda arrancar. Si no está seguro, consulte la documentación de distribución para obtener información sobre cómo editar correctamente ese archivo. También se recomienda realizar una copia de seguridad del archivo /etc/fstab antes de editarlo.

1. Cree el punto de montaje deseado para el nuevo sistema de archivos, por ejemplo:

		# sudo mkdir /data


2. Busque la ruta de acceso del volumen lógico.

		# lvdisplay
		--- Logical volume ---
		LV Path                /dev/data-vg01/data-lv01
		....


3. Abra /etc/fstab en un editor de texto y agregue una entrada al nuevo sistema de archivos, por ejemplo:

		/dev/data-vg01/data-lv01  /data  ext4  defaults  0  2

	A continuación, guarde y cierre /etc/fstab.


4. Pruebe que la entrada /etc/fstab sea correcta:

		# sudo mount -a

	Si este comando genera un mensaje de error, compruebe la sintaxis del archivo /etc/fstab.

	A continuación, ejecute el comando `mount` para garantizar el montaje del sistema de archivos:

		# mount
		......
		/dev/mapper/data--vg01-data--lv01 on /data type ext4 (rw)


5. Parámetros de arranque para notificaciones de error en/etc/fstab (opcional)

	Muchas distribuciones incluyen los parámetros de montaje `nobootwait` o `nofail` que pueden agregarse al archivo /etc/fstab. Estos parámetros admiten errores al montar un sistema de archivos concreto y permiten que el sistema Linux continúe iniciándose incluso aunque no pueda montar correctamente el sistema de archivos RAID. Consulte la documentación de su distribución para obtener más información sobre estos parámetros.

	Ejemplo (Ubuntu):

		/dev/data-vg01/data-lv01  /data  ext4  defaults,nobootwait  0  2

<!---HONumber=AcomDC_0518_2016-->