<properties
	pageTitle="Preparar un disco duro virtual en Debian Linux| Microsoft Azure"
	description="Aprenda a crear archivos de VHD en Debian 7 y 8 para implementarlos en Azure."
	services="virtual-machines-linux"
	documentationCenter=""
	authors="szarkos"
	manager="timlt"
	editor=""
    tags="azure-resource-manager,azure-service-management"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/24/2016"
	ms.author="szark"/>



# Preparar VHD en Debian de Azure

## Requisitos previos
En esta sección, se supone que ya instaló en un sistema operativo Debian Linux desde un archivo .iso que obtuvo del [sitio web Debian](https://www.debian.org/distrib/) y que descargó a un disco duro virtual. Existen varias herramientas para crear archivos .vhd; Hyper-V es solo un ejemplo. Para obtener instrucciones acerca de cómo usar Hyper-V, consulte [Instalación del rol de Hyper-V y configuración de una máquina virtual](https://technet.microsoft.com/library/hh846766.aspx).


## Notas de instalación

- Consulte también [Notas generales sobre la instalación de Linux](virtual-machines-linux-create-upload-generic.md#general-linux-installation-notes) para obtener más consejos sobre la preparación de Linux para Azure.
- el reciente formato VHDX no se admite en Azure. Puede convertir el disco al formato VHD con el Administrador de Hyper-V o el cmdlet **convert-vhd**.
- Al instalar el sistema Linux se recomienda utilizar las particiones estándar en lugar de un LVM (que a menudo viene de forma predeterminada en muchas instalaciones). De este modo se impedirá que el nombre del LVM entre en conflicto con las máquinas virtuales clonadas, especialmente si en algún momento hace falta adjuntar un disco de SO a otra máquina virtual para solucionar problemas. Se pueden utilizar [LVM](virtual-machines-linux-configure-lvm.md) o [RAID](virtual-machines-linux-configure-raid.md) en discos de datos si así se prefiere.
- No cree una partición de intercambio en el disco del SO. El agente de Linux de Azure se puede configurar para crear un archivo de intercambio en el disco de recursos temporal. Puede encontrar más información al respecto en los pasos que vienen a continuación.
- El tamaño de todos los archivos VHD debe ser múltiplo de 1 MB.


## Uso de Azure-Manage para crear VHD Debian

Hay herramientas disponibles para generar VHD Debian para Azure, como los scripts [azure-manage](https://github.com/credativ/azure-manage) de [credativ](http://www.credativ.com/). Este es el enfoque recomendado, en lugar de crear una imagen desde el principio. Por ejemplo, para crear un VHD Debian 8, ejecute los comandos siguientes para descargar azure-manage (y sus dependencias) y ejecute el script azure\_build\_image:

	# sudo apt-get update
	# sudo apt-get install git qemu-utils mbr kpartx debootstrap

	# sudo apt-get install python3-pip
	# sudo pip3 install azure-storage azure-servicemanagement-legacy pytest pyyaml
	# git clone https://github.com/credativ/azure-manage.git
	# cd azure-manage
	# sudo pip3 install .

	# sudo azure_build_image --option release=jessie --option image_size_gb=30 --option image_prefix=debian-jessie-azure section


## Preparación manual de un VHD Debian

1. En el Administrador de Hyper-V, seleccione la máquina virtual.

2. Haga clic en **Conectar** para abrir una ventana de consola de la máquina virtual.

3. Comente la línea de **deb cdrom** en `/etc/apt/source.list`, si configura la máquina virtual con un archivo ISO.

4. Edite el archivo `/etc/default/grub` y modifique el parámetro **GRUB\_CMDLINE\_LINUX** tal y como se muestra a continuación para incluir parámetros de kernel adicionales de Azure.

        GRUB_CMDLINE_LINUX="console=tty0 console=ttyS0,115200n8 earlyprintk=ttyS0,115200 rootdelay=30"

5. Recompile el sistema GRUB y ejecute:

        # sudo update-grub

6. Agregue repositorios de Azure de Debian a /etc/apt/sources.list (Debian 7 u 8):

	**Debian 7.x "Wheezy"**

		deb http://debian-archive.trafficmanager.net/debian wheezy-backports main
		deb-src http://debian-archive.trafficmanager.net/debian wheezy-backports main
		deb http://debian-archive.trafficmanager.net/debian-azure wheezy main
		deb-src http://debian-archive.trafficmanager.net/debian-azure wheezy main


	**Debian 8.x "Jessie"**

		deb http://debian-archive.trafficmanager.net/debian jessie-backports main
		deb-src http://debian-archive.trafficmanager.net/debian jessie-backports main
		deb http://debian-archive.trafficmanager.net/debian-azure jessie main
		deb-src http://debian-archive.trafficmanager.net/debian-azure jessie main


7. Instale el Agente de Linux de Azure:

		# sudo apt-get update
		# sudo apt-get install waagent

8. En el caso de Debian 7, se requiere que se ejecute el kernel basado en 3.16 desde el repositorio wheezy-backports. En primer lugar, cree un archivo llamado /etc/apt/preferences.d/linux.pref con el siguiente contenido:

		Package: linux-image-amd64 initramfs-tools
		Pin: release n=wheezy-backports
		Pin-Priority: 500

	A continuación, ejecute "sudo apt-get install linux-image-amd64" para instalar el nuevo kernel.

8. Desaprovisione la máquina virtual, prepárela para aprovisionarla en Azure y ejecute:

        # sudo waagent –force -deprovision
        # export HISTSIZE=0
        # logout

9. Haga clic en** Acción -> Apagar** en el Administrador de Hyper-V. El VHD de Linux ya está listo para cargarse en Azure.


## Pasos siguientes

Ya está listo para usar el disco duro virtual de Debian para crear nuevas máquinas virtuales de Azure. Si es la primera vez que carga el archivo .vhd en Azure, consulte los pasos 2 y 3 de [Creación y carga de un disco duro virtual que contiene el sistema operativo Linux](virtual-machines-linux-classic-create-upload-vhd.md).

<!---HONumber=AcomDC_0831_2016-->