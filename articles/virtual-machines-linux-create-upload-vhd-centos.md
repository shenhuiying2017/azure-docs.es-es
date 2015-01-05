<properties urlDisplayName="Upload a CentOS-based VHD" pageTitle="Creación y carga de un VHD de Linux basado en CentOS en Azure" metaKeywords="Azure VHD, uploading Linux VHD, CentOS" description="Learn to create and upload an Azure virtual hard disk (VHD) that contains a CentOS-based Linux operating system." metaCanonical="" services="virtual-machines" documentationCenter="" title="Creating and Uploading a Virtual Hard Disk that Contains a CentOS-based Linux Operating System" authors="szarkos" solutions="" manager="timlt" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="06/05/2014" ms.author="szarkos" />

# Preparación de una máquina virtual basada en CentOS para Azure

- [Preparación de una máquina virtual CentOS 6.x para Azure](#centos6)
- [Preparación de una máquina virtual CentOS 7.0+ para Azure](#centos7)

##Requisitos previos##

En este artículo se supone que ya ha instalado un sistema operativo Linux CentOS (o un derivado similar) en un disco duro virtual. Existen varias herramientas para crear archivos .vhd; por ejemplo, una solución de virtualización como Hyper-V. Para obtener instrucciones al respecto, consulte [Instalar el rol Hyper-V y configurar una máquina virtual](http://technet.microsoft.com/library/hh846766.aspx). 


**Notas de instalación de CentOS**

- el reciente formato VHDX no se admite en Azure. Puede convertir el disco al formato VHD con el Administrador de Hyper-V o el cmdlet Convert-VHD.

- Al instalar el sistema Linux se recomienda utilizar las particiones estándar en lugar de un LVM (que a menudo viene de forma predeterminada en muchas instalaciones). De este modo se impedirá que el nombre del LVM entre en conflicto con las máquinas virtuales clonadas, especialmente si en algún momento hace falta adjuntar un disco de SO a otra máquina virtual para solucionar problemas.  LVM o [RAID](../virtual-machines-linux-configure-raid) se pueden utilizar en discos de datos si así se prefiere.

- NUMA no se admite para tamaños de máquina virtual más grandes debido a un error en las versiones del kernel de Linux anteriores a la 2.6.37. Este problema afecta principalmente a las distribuciones que usan el kernel Red Hat 2.6.32 de canal de subida. La instalación manual del agente de Linux de Azure (waagent) deshabilitará automáticamente NUMA en la configuración GRUB para el kernel de Linux. Puede encontrar más información al respecto en los pasos que vienen a continuación.

- No cree una partición de intercambio en el disco del SO. El agente de Linux se puede configurar para crear un archivo de intercambio en el disco de recursos temporal.  Puede encontrar más información al respecto en los pasos que vienen a continuación.

- El tamaño de todos los archivos VHD debe ser múltiplo de 1 MB.


## <a id="centos6"> </a>CentOS 6.x ##

1. En el Administrador de Hyper-V, seleccione la máquina virtual.

2. Haga clic en **Conectar** para abrir la ventana de consola de la máquina virtual.

3. Desinstale NetworkManager ejecutando el comando siguiente:

		# sudo rpm -e --nodeps NetworkManager

	**Nota:** si el paquete todavía no está instalado, se producirá un mensaje de error en este comando. Se espera que esto sea así.

4.	Cree un archivo llamado **network** en el directorio "/etc/sysconfig/" que contenga el texto siguiente:

		NETWORKING=yes
		HOSTNAME=localhost.localdomain

5.	Cree un archivo llamado **ifcfg-eth0** en el directorio "/etc/sysconfig/network-scripts/" que contenga el texto siguiente:

		DEVICE=eth0
		ONBOOT=yes
		BOOTPROTO=dhcp
		TYPE=Ethernet
		USERCTL=no
		PEERDNS=yes
		IPV6INIT=no

6.	Mueva (o elimine) las reglas udev para impedir que se generen reglas estáticas para la interfaz Ethernet. Estas reglas pueden causar problemas al clonar una máquina virtual en Microsoft Azure o Hyper-V:

		# sudo mkdir -m 0700 /var/lib/waagent
		# sudo mv /lib/udev/rules.d/75-persistent-net-generator.rules /var/lib/waagent/ 2>/dev/null
		# sudo mv /etc/udev/rules.d/70-persistent-net.rules /var/lib/waagent/ 2>/dev/null

7. Asegúrese de que el servicio de red se inicie en el arranque ejecutando el comando siguiente:

		# sudo chkconfig network on


8. **Solo CentOS 6.3**: Instalar los controladores para los servicios de integración de Linux

	**Importante: el paso solo es válido para CentOS 6.3 y versiones anteriores.**  En CentOS 6.4+ los servicios de integración de Linux *ya están disponibles en el kernel*.

	a) Obtenga el archivo .iso que contiene los controladores para los servicios de integración de Linux en el [Centro de descarga de Microsoft](http://www.microsoft.com/en-us/download/details.aspx?id=41554).

	b) En el Administrador de Hyper-V, en el panel **Acciones**, haga clic en **Configuración**.

	![Open Hyper-V settings](./media/virtual-machines-linux-create-upload-vhd/settings.png)

	c) En el panel **Hardware**, haga clic en **Controlador IDE 1**.

	![Add DVD drive with install media](./media/virtual-machines-linux-create-upload-vhd/installiso.png)

	d) En el cuadro **Controlador IDE**, haga clic en **Unidad de DVD** y, a continuación, haga clic en **Agregar**.

	e) Seleccione **Archivo de imagen**, vaya a **Linux IC v3.2.iso** y, a continuación, haga clic en **Abrir**.

	f) En la página **Configuración**, haga clic en **Aceptar**.

	g) Haga clic en **Conectar** para abrir la ventana de la máquina virtual.

	h) En la ventana del símbolo del sistema, escriba los comandos siguientes:

		# sudo mount /dev/cdrom /media
		# sudo /media/install.sh
		# sudo reboot

9. Instale el paquete python-pyasn1 ejecutando el comando siguiente:

		# sudo yum install python-pyasn1

10. Si desea utilizar los espejos OpenLogic hospedados en los centros de datos de Azure, sustituya el archivo /etc/yum.repos.d/CentOS-Base.repo por los siguientes repositorios.  De este modo se agregará también el repositorio **[openlogic]** que incluye paquetes para el agente de Linux de Azure.

		[openlogic]
		name=CentOS-$releasever - openlogic packages for $basearch
		baseurl=http://olcentgbl.trafficmanager.net/openlogic/$releasever/openlogic/$basearch/
		enabled=1
		gpgcheck=0
		
		[base]
		name=CentOS-$releasever - Base
		baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/os/$basearch/
		gpgcheck=1
		gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6
		
		#released updates
		[updates]
		name=CentOS-$releasever - Updates
		baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/updates/$basearch/
		gpgcheck=1
		gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6
		
		#additional packages that may be useful
		[extras]
		name=CentOS-$releasever - Extras
		baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/extras/$basearch/
		gpgcheck=1
		gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6
		
		#additional packages that extend functionality of existing packages
		[centosplus]
		name=CentOS-$releasever - Plus
		baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/centosplus/$basearch/
		gpgcheck=1
		enabled=0
		gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6
		
		#contrib - packages by Centos Users
		[contrib]
		name=CentOS-$releasever - Contrib
		baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/contrib/$basearch/
		gpgcheck=1
		enabled=0
		gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

	**Nota:** En el resto de esta guía se supondrá que está utilizando al menos el repositorio [openlogic], que se utilizará para instalar el agente de Linux de Azure a continuación.


11.	Agregue la línea siguiente a /etc/yum.conf:

		http_caching=packages

	Y **solo en CentOS 6.3**, agregue la línea siguiente:

		exclude=kernel*

12. Deshabilite "fastestmirror" del módulo yum mediante la edición del archivo "/etc/yum/pluginconf.d/fastestmirror.conf" y en "[main]" escriba lo siguiente:

		set enabled=0

13.	Ejecute el comando siguiente para borrar los metadatos de yum actuales:

		# yum clean all

14. **Solo en CentOS 6.3**, actualice el kernel utilizando el comando siguiente:

		# sudo yum --disableexcludes=all install kernel

15.	Modifique la línea de arranque de kernel de su configuración grub para que incluya parámetros de kernel adicionales para Azure. Para ello, abra "/boot/grub/menu.lst" en un editor de texto y asegúrese de que el kernel predeterminado incluye los parámetros siguientes:

		console=ttyS0 earlyprintk=ttyS0 rootdelay=300 numa=off

	Así también se asegurará de que todos los mensajes de la consola se envían al primer puerto serie, lo que puede ayudar al soporte técnico de Azure con los problemas de depuración de errores. Esto deshabilitará NUMA debido a un error en la versión del kernel usada por CentOS 6.

	Además de lo anterior, se recomienda *quitar* los parámetros siguientes:

		rhgb quiet crashkernel=auto

	Los arranques gráfico y silencioso no resultan útiles en un entorno de nube, donde queremos que todos los registros se envíen al puerto serie.

	Es posible dejar la opción "crashkernel" configurada si así se desea, pero tenga en cuenta que este parámetro reducirá la cantidad de memoria disponible en la máquina virtual en 128 MB o más, lo cual puede resultar problemático en tamaños de VM más reducidos.


16.	Asegúrese de que el servidor SSH se haya instalado y configurado para iniciarse en el tiempo de arranque.  Este es normalmente el valor predeterminado.

17. Instale el Agente de Linux de Azure ejecutando el comando siguiente:

		# sudo yum install WALinuxAgent

	Tenga en cuanta que al instalar el paquete WALinuxAgent se eliminarán los paquetes NetworkManager y NetworkManager-gnome, si es que aún no se han eliminado como se indica en el paso 2.

18.	No cree un espacio de intercambio en el disco del sistema operativo.

	El Agente de Linux de Azure puede configurar automáticamente un espacio de intercambio utilizando el disco de recursos local que se adjunta a la máquina virtual después de aprovisionarse en Azure. Tenga en cuenta que el disco de recursos local es un disco *temporal* que debe vaciarse cuando la máquina virtual se desaprovisiona. Después de instalar el Agente de Linux de Azure (consulte el paso anterior), modifique apropiadamente los parámetros siguientes en /etc/waagent.conf:

		ResourceDisk.Format=y
		ResourceDisk.Filesystem=ext4
		ResourceDisk.MountPoint=/mnt/resource
		ResourceDisk.EnableSwap=y
		ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

19.	Ejecute los comandos siguientes para desaprovisionar la máquina virtual y prepararla para aprovisionarse en Azure:

		# sudo waagent -force -deprovision
		# export HISTSIZE=0
		# logout

20. Haga clic en **Acción -> Apagar** en el Administrador de Hyper-V. El VHD de Linux ya está listo para cargarse en Azure.


----------


## <a id="centos7"> </a>CentOS 7.0+ ##

**Cambios en CentOS 7 (y derivados similares)**

La preparación de una máquina virtual CentOS 7 para Azure es muy similar a CentOS 6, aunque hay varias diferencias importantes que es necesario tener en cuenta:

 - El paquete NetworkManager ya no entra en conflicto con el agente de Linux de Azure. Este paquete está instalado de manera predeterminada y recomendamos que no se quite.
 - GRUB2 se utiliza ahora como cargador de arranque predeterminado; por ello, el proceso de edición de los parámetros de kernel ha cambiado (ver más adelante).
 - XFS es ahora el sistema de archivos predeterminado. El sistema de archivos ext4 se puede seguir utilizando si así se desea.


**Pasos de configuración**

1. En el Administrador de Hyper-V, seleccione la máquina virtual.

2. Haga clic en **Conectar** para abrir la ventana de consola de la máquina virtual.

3.	Cree un archivo llamado **network** en el directorio "/etc/sysconfig/" que contenga el texto siguiente:

		NETWORKING=yes
		HOSTNAME=localhost.localdomain

4.	Cree un archivo llamado **ifcfg-eth0** en el directorio "/etc/sysconfig/network-scripts/" que contenga el texto siguiente:

		DEVICE=eth0
		ONBOOT=yes
		BOOTPROTO=dhcp
		TYPE=Ethernet
		USERCTL=no
		PEERDNS=yes
		IPV6INIT=no

5.	Mueva (o elimine) las reglas udev para impedir que se generen reglas estáticas para la interfaz Ethernet. Estas reglas pueden causar problemas al clonar una máquina virtual en Microsoft Azure o Hyper-V:

		# sudo mkdir -m 0700 /var/lib/waagent
		# sudo mv /lib/udev/rules.d/75-persistent-net-generator.rules /var/lib/waagent/ 2>/dev/null
		# sudo mv /etc/udev/rules.d/70-persistent-net.rules /var/lib/waagent/ 2>/dev/null

6. Asegúrese de que el servicio de red se inicie en el arranque ejecutando el comando siguiente:

		# sudo chkconfig network on

7. Instale el paquete python-pyasn1 ejecutando el comando siguiente:

		# sudo yum install python-pyasn1

8. Si desea utilizar los espejos OpenLogic hospedados en los centros de datos de Azure, sustituya el archivo /etc/yum.repos.d/CentOS-Base.repo por los siguientes repositorios.  De este modo se agregará también el repositorio **[openlogic]** que incluye paquetes para el agente de Linux de Azure.

		[openlogic]
		name=CentOS-$releasever - openlogic packages for $basearch
		baseurl=http://olcentgbl.trafficmanager.net/openlogic/$releasever/openlogic/$basearch/
		enabled=1
		gpgcheck=0
		
		[base]
		name=CentOS-$releasever - Base
		baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/os/$basearch/
		gpgcheck=1
		gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
		
		#released updates
		[updates]
		name=CentOS-$releasever - Updates
		baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/updates/$basearch/
		gpgcheck=1
		gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
		
		#additional packages that may be useful
		[extras]
		name=CentOS-$releasever - Extras
		baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/extras/$basearch/
		gpgcheck=1
		gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
		
		#additional packages that extend functionality of existing packages
		[centosplus]
		name=CentOS-$releasever - Plus
		baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/centosplus/$basearch/
		gpgcheck=1
		enabled=0
		gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
		
		#contrib - packages by Centos Users
		[contrib]
		name=CentOS-$releasever - Contrib
		baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/contrib/$basearch/
		gpgcheck=1
		enabled=0
		gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7


	**Nota:** En el resto de esta guía se supondrá que está utilizando al menos el repositorio [openlogic], que se utilizará para instalar el agente de Linux de Azure a continuación.

9.	Ejecute el comando siguiente para borrar los metadatos de yum actuales e instalar las actualizaciones:

		# sudo yum clean all
		# sudo yum -y update

10.	Modifique la línea de arranque de kernel de su configuración grub para que incluya parámetros de kernel adicionales para Azure. Para ello, abra "/etc/default/grub" en un editor de texto y edite el parámetro "GRUB_CMDLINE_LINUX"; por ejemplo:

		GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0"

	Así también se asegurará de que todos los mensajes de la consola se envían al primer puerto serie, lo que puede ayudar al soporte técnico de Azure con los problemas de depuración de errores. Además de lo anterior, se recomienda *quitar* los parámetros siguientes:

		rhgb quiet crashkernel=auto

	Los arranques gráfico y silencioso no resultan útiles en un entorno de nube, donde queremos que todos los registros se envíen al puerto serie.

	Es posible dejar la opción "crashkernel" configurada si así se desea, pero tenga en cuenta que este parámetro reducirá la cantidad de memoria disponible en la máquina virtual en 128 MB o más, lo cual puede resultar problemático en tamaños de VM más reducidos.

11. Una vez que haya finalizado de editar "/etc/default/grub" como se indica anteriormente, ejecute el comando siguiente para volver a compilar la configuración de grub:

		# sudo grub2-mkconfig -o /boot/grub2/grub.cfg

12.	Asegúrese de que el servidor SSH se haya instalado y configurado para iniciarse en el tiempo de arranque.  Este es normalmente el valor predeterminado.

13. Instale el Agente de Linux de Azure ejecutando el comando siguiente:

		# sudo yum install WALinuxAgent

14.	No cree un espacio de intercambio en el disco del sistema operativo.

	El Agente de Linux de Azure puede configurar automáticamente un espacio de intercambio utilizando el disco de recursos local que se adjunta a la máquina virtual después de aprovisionarse en Azure. Tenga en cuenta que el disco de recursos local es un disco *temporal* que debe vaciarse cuando la máquina virtual se desaprovisiona. Después de instalar el Agente de Linux de Azure (consulte el paso anterior), modifique apropiadamente los parámetros siguientes en /etc/waagent.conf:

		ResourceDisk.Format=y
		ResourceDisk.Filesystem=ext4
		ResourceDisk.MountPoint=/mnt/resource
		ResourceDisk.EnableSwap=y
		ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

15.	Ejecute los comandos siguientes para desaprovisionar la máquina virtual y prepararla para aprovisionarse en Azure:

		# sudo waagent -force -deprovision
		# export HISTSIZE=0
		# logout

16. Haga clic en **Acción -> Apagar** en el Administrador de Hyper-V. El VHD de Linux ya está listo para cargarse en Azure.



<!--HONumber=35.1-->
