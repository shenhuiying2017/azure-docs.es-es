<properties 
	pageTitle="Creación y carga de un VHD de SUSE Linux en Azure" 
	description="Aprenda a crear y cargar un disco duro virtual de Azure (VHD) que contiene un sistema operativo SUSE Linux." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="szarkos" 
	manager="timlt" 
	editor="tysonn"/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-linux" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/15/2015" 
	ms.author="szarkos"/>


# Preparación de una máquina virtual SLES u openSUSE para Azure

- [Preparación de una máquina virtual SLES 11 SP3 para Azure](#sles11)
- [Preparación de una máquina virtual openSUSE 13.1+ para Azure](#osuse)

##Requisitos previos##

En este artículo se supone que ya ha instalado un sistema operativo Linux SUSE u openSUSE en un disco duro virtual. Existen varias herramientas para crear archivos .vhd; por ejemplo, una solución de virtualización como Hyper-V. Para obtener instrucciones, consulte [Instalación del rol de Hyper-V y configuración de una máquina Virtual](http://technet.microsoft.com/library/hh846766.aspx).


**Notas sobre la instalación de SLES/openSUSE**

 - [SUSE Studio](http://www.susestudio.com)  puede crear y administrar fácilmente sus imágenes de SLES / openSUSE para Hyper-V y Azure. Este es el enfoque recomendado para personalizar sus propias imágenes SUSE y openSUSE. Las siguientes imágenes oficiales de la Galería de SUSE Studio se pueden descargar o clonar en su propio SUSE Studio:

  - [SLES 11 SP3 para Azure en la galería de SUSE Studio](http://susestudio.com/a/02kbT4/sles-11-sp3-for-windows-azure)
  - [openSUSE 13.1 para Azure en la galería de SUSE Studio](https://susestudio.com/a/02kbT4/opensuse-13-1-for-windows-azure)

- el reciente formato VHDX no se admite en Azure. Puede convertir el disco al formato VHD con el Administrador de Hyper-V o el cmdlet Convert-VHD.

- Al instalar el sistema Linux se recomienda utilizar las particiones estándar en lugar de un LVM (que a menudo viene de forma predeterminada en muchas instalaciones). De este modo se impedirá que el nombre del LVM entre en conflicto con las máquinas virtuales clonadas, especialmente si en algún momento hace falta adjuntar un disco de SO a otra máquina virtual para solucionar problemas. LVM o [RAID](virtual-machines-linux-configure-raid.md) se pueden utilizar en discos de datos si así se prefiere.

- No cree una partición de intercambio en el disco del SO. El agente de Linux se puede configurar para crear un archivo de intercambio en el disco de recursos temporal. Puede encontrar más información al respecto en los pasos que vienen a continuación.

- El tamaño de todos los archivos VHD debe ser múltiplo de 1 MB.


## <a id="sles11"> </a>Preparación de SUSE Linux Enterprise Server 11 SP3 ##

1. Seleccione la máquina virtual en el panel central del Administrador de Hyper-V.

2. Haga clic en **Conectar** para abrir la ventana de la máquina virtual.

3. Registre el sistema de SUSE Linux Enterprise para permitir que descargue actualizaciones e instale paquetes.

4. Actualice el sistema con las revisiones más recientes:

		# sudo zypper update

5. Instale el Agente de Linux de Azure desde el repositorio SLES:

		# sudo zypper install WALinuxAgent

6. Modifique la línea de arranque de kernel de su configuración grub para que incluya parámetros de kernel adicionales para Azure. Para ello, abra "/boot/grub/menu.lst" en un editor de texto y asegúrese de que el kernel predeterminado incluye los parámetros siguientes:

		console=ttyS0 earlyprintk=ttyS0 rootdelay=300

	Así se asegurará de que todos los mensajes de la consola se envían al primer puerto serie, lo que puede ayudar al soporte técnico de Azure con los problemas de depuración de errores.

7.	Se recomienda editar el archivo "/etc/sysconfig/network/dhcp" y cambiar el parámetro `DHCLIENT_SET_HOSTNAME` por lo siguiente:

		DHCLIENT_SET_HOSTNAME="no"

8.	En "/etc/sudoers", convierta en comentario o quite las líneas siguientes, si existen:

		Defaults targetpw   # ask for the password of the target user i.e. root
		ALL    ALL=(ALL) ALL   # WARNING! Only use this together with 'Defaults targetpw'!

9.	Asegúrese de que el servidor SSH se haya instalado y configurado para iniciarse en el tiempo de arranque. Este es normalmente el valor predeterminado.

10.	No cree un espacio de intercambio en el disco del sistema operativo.

	El Agente de Linux de Azure puede configurar automáticamente un espacio de intercambio utilizando el disco de recursos local que se adjunta a la máquina virtual después de aprovisionarse en Azure. Tenga en cuenta que el disco de recursos local es un disco *temporal* que debe vaciarse cuando la máquina virtual se desaprovisiona. Después de instalar el Agente de Linux de Azure (consulte el paso anterior), modifique apropiadamente los parámetros siguientes en /etc/waagent.conf:

		ResourceDisk.Format=y
		ResourceDisk.Filesystem=ext4
		ResourceDisk.MountPoint=/mnt/resource
		ResourceDisk.EnableSwap=y
		ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

11.	Ejecute los comandos siguientes para desaprovisionar la máquina virtual y prepararla para aprovisionarse en Azure:

		# sudo waagent -force -deprovision
		# export HISTSIZE=0
		# logout

12. Haga clic en** Acción -> Apagar** en el Administrador de Hyper-V. El VHD de Linux ya está listo para cargarse en Azure.


----------

## <a id="osuse"> </a>Preparación de openSUSE 13.1+ ##

1. Seleccione la máquina virtual en el panel central del Administrador de Hyper-V.

2. Haga clic en **Conectar** para abrir la ventana de la máquina virtual

3. En el shell, ejecute el comando '`zypper lr`'. Si este comando devuelve una salida similar a la siguiente (tenga en cuenta que los números de la versión pueden diferir):

		# | Alias                 | Name                  | Enabled | Refresh
		--+-----------------------+-----------------------+---------+--------
		1 | Cloud:Tools_13.1      | Cloud:Tools_13.1      | Yes     | Yes
		2 | openSUSE_13.1_OSS     | openSUSE_13.1_OSS     | Yes     | Yes
		3 | openSUSE_13.1_Updates | openSUSE_13.1_Updates | Yes     | Yes

	entonces los repositorios están configurados según lo previsto y no es necesario realizar ajustes.

	Si el comando devuelve el mensaje "No repositories defined...", utilice los comandos siguientes para agregar estos repositorios:

		# sudo zypper ar -f http://download.opensuse.org/repositories/Cloud:Tools/openSUSE_13.1 Cloud:Tools_13.1 
		# sudo zypper ar -f http://download.opensuse.org/distribution/13.1/repo/oss openSUSE_13.1_OSS
		# sudo zypper ar -f http://download.opensuse.org/update/13.1 openSUSE_13.1_Updates

	Puede verificar entonces que se han agregado los repositorios al volver a ejecutar el comando '`zypper lr`'. En caso de que no haya un repositorio de actualizaciones relevante habilitado, habilítelo con el comando siguiente:

		# sudo zypper mr -e [NUMBER OF REPOSITORY]


4. Actualice el kernel a la versión más reciente disponible:

		# sudo zypper up kernel-default

	O para actualizar el sistema con todos los parches más recientes:

		# sudo zypper update

5.	Instalación del agente de Linux de Azure

		# sudo zypper install WALinuxAgent

6.	Modifique la línea de arranque de kernel de su configuración grub para que incluya parámetros de kernel adicionales para Azure. Para ello, abra "/boot/grub/menu.lst" en un editor de texto y asegúrese de que el kernel predeterminado incluye los parámetros siguientes:

		console=ttyS0 earlyprintk=ttyS0 rootdelay=300

	Así se asegurará de que todos los mensajes de la consola se envían al primer puerto serie, lo que puede ayudar al soporte técnico de Azure con los problemas de depuración de errores. Además, elimine los parámetros siguientes de la línea de arranque de kernel, si es que están:

		libata.atapi_enabled=0 reserve=0x1f0,0x8

7.	Se recomienda editar el archivo "/etc/sysconfig/network/dhcp" y cambiar el parámetro `DHCLIENT_SET_HOSTNAME` por lo siguiente:

		DHCLIENT_SET_HOSTNAME="no"

8.	**Importante:** en "/etc/sudoers", realice un comentario o quite las siguientes líneas si existen:

		Defaults targetpw   # ask for the password of the target user i.e. root
		ALL    ALL=(ALL) ALL   # WARNING! Only use this together with 'Defaults targetpw'!

9.	Asegúrese de que el servidor SSH se haya instalado y configurado para iniciarse en el tiempo de arranque. Este es normalmente el valor predeterminado.

10.	No cree un espacio de intercambio en el disco del sistema operativo.

	El Agente de Linux de Azure puede configurar automáticamente un espacio de intercambio utilizando el disco de recursos local que se adjunta a la máquina virtual después de aprovisionarse en Azure. Tenga en cuenta que el disco de recursos local es un disco *temporal* que debe vaciarse cuando la máquina virtual se desaprovisiona. Después de instalar el Agente de Linux de Azure (consulte el paso anterior), modifique apropiadamente los parámetros siguientes en /etc/waagent.conf:

		ResourceDisk.Format=y
		ResourceDisk.Filesystem=ext4
		ResourceDisk.MountPoint=/mnt/resource
		ResourceDisk.EnableSwap=y
		ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

11.	Ejecute los comandos siguientes para desaprovisionar la máquina virtual y prepararla para aprovisionarse en Azure:

		# sudo waagent -force -deprovision
		# export HISTSIZE=0
		# logout

12. Asegúrese de que el Agente de Linux de Azure se ejecute al inicio:

		# sudo systemctl enable waagent.service

13. Haga clic en** Acción -> Apagar** en el Administrador de Hyper-V. El VHD de Linux ya está listo para cargarse en Azure.

<!---HONumber=58-->