---
title: "Creación y carga de un VHD de Linux basado en CentOS en Azure"
description: Aprenda a crear y cargar un disco duro virtual de Azure (VHD) que contiene un sistema operativo Linux basado en CentOS.
services: virtual-machines-linux
documentationcenter: 
author: szarkos
manager: timlt
editor: tysonn
tags: azure-resource-manager,azure-service-management
ms.assetid: 0e518e92-e981-43f4-b12c-9cba1064c4bb
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 05/09/2016
ms.author: szark
translationtype: Human Translation
ms.sourcegitcommit: ee34a7ebd48879448e126c1c9c46c751e477c406
ms.openlocfilehash: 6919a8c2147db4f8c86230e336540c8c88c714e1


---
# <a name="prepare-a-centos-based-virtual-machine-for-azure"></a>Preparación de una máquina virtual basada en CentOS para Azure
* [Preparación de una máquina virtual CentOS 6.x para Azure](#centos-6x)
* [Preparación de una máquina virtual CentOS 7.0+ para Azure](#centos-70)

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## <a name="prerequisites"></a>Requisitos previos
En este artículo se supone que ya ha instalado un sistema operativo Linux CentOS (o un derivado similar) en un disco duro virtual. Existen varias herramientas para crear archivos .vhd; por ejemplo, una solución de virtualización como Hyper-V. Para obtener instrucciones, consulte [Instalación del rol de Hyper-V y configuración de una máquina Virtual](http://technet.microsoft.com/library/hh846766.aspx).

**Notas de instalación de CentOS**

* Consulte también [Notas generales sobre la instalación de Linux](virtual-machines-linux-create-upload-generic.md#general-linux-installation-notes) para obtener más consejos sobre la preparación de Linux para Azure.
* El formato VHDX no se admite en Azure, solo el **VHD fijo**.  Puede convertir el disco al formato VHD con el Administrador de Hyper-V o el cmdlet Convert-VHD.
* Al instalar el sistema Linux se recomienda utilizar las particiones estándar en lugar de un LVM (que a menudo viene de forma predeterminada en muchas instalaciones). De este modo se impedirá que el nombre del LVM entre en conflicto con las máquinas virtuales clonadas, especialmente si en algún momento hace falta adjuntar un disco de SO a otra máquina virtual para solucionar problemas.  [LVM](virtual-machines-linux-configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) o [RAID](virtual-machines-linux-configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) se pueden utilizar en discos de datos si así se prefiere.
* NUMA no se admite para tamaños de máquina virtual más grandes debido a un error en las versiones del kernel de Linux anteriores a la 2.6.37. Este problema afecta principalmente a las distribuciones que usan el kernel Red Hat 2.6.32 de canal de subida. La instalación manual del agente de Linux de Azure (waagent) deshabilitará automáticamente NUMA en la configuración GRUB para el kernel de Linux. Puede encontrar más información al respecto en los pasos que vienen a continuación.
* No cree una partición de intercambio en el disco del SO. El agente de Linux se puede configurar para crear un archivo de intercambio en el disco de recursos temporal.  Puede encontrar más información al respecto en los pasos que vienen a continuación.
* El tamaño de todos los archivos VHD debe ser múltiplo de 1 MB.

## <a name="centos-6x"></a>CentOS 6.x
1. En el Administrador de Hyper-V, seleccione la máquina virtual.
2. Haga clic en **Conectar** para abrir una ventana de consola de la máquina virtual.
3. Desinstale NetworkManager ejecutando el comando siguiente:
   
        # sudo rpm -e --nodeps NetworkManager
   
    **Nota:** si el paquete todavía no está instalado, se producirá un mensaje de error en este comando. Se espera que esto sea así.
4. Cree un archivo llamado **network** in the `/etc/sysconfig/` que contenga el texto siguiente:
   
     NETWORKING=yes   HOSTNAME=localhost.localdomain
5. Cree un archivo llamado **ifcfg-eth0** in the `/etc/sysconfig/network-scripts/` que contenga el texto siguiente:
   
     DEVICE=eth0   ONBOOT=yes   BOOTPROTO=dhcp   TYPE=Ethernet   USERCTL=no   PEERDNS=yes   IPV6INIT=no
6. Modifique las reglas udev a fin de impedir que se generen reglas estáticas para las interfaces Ethernet. Estas reglas pueden causar problemas al clonar una máquina virtual en Microsoft Azure o Hyper-V:
   
   # <a name="sudo-ln--s-devnull-etcudevrulesd75-persistent-net-generatorrules"></a>sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
   # <a name="sudo-rm--f-etcudevrulesd70-persistent-netrules"></a>sudo rm -f /etc/udev/rules.d/70-persistent-net.rules
7. Asegúrese de que el servicio de red se inicie en el arranque ejecutando el comando siguiente:
   
        # sudo chkconfig network on
8. **Solo CentOS 6.3**: instalación de los controladores para los servicios de integración de Linux (LIS)
   
    **Importante: el paso solo es válido para CentOS 6.3 y versiones anteriores.**  En CentOS 6.4+, los servicios de integración de Linux *ya están disponibles en el kernel estándar*.
   
   * Siga las instrucciones de instalación de la [página de descarga de LIS](https://www.microsoft.com/en-us/download/details.aspx?id=46842) e instale el RPM en la imagen.  
9. Instale el paquete python-pyasn1 ejecutando el comando siguiente:
   
        # sudo yum install python-pyasn1
10. Si desea utilizar los espejos OpenLogic hospedados en los centros de datos de Azure, sustituya el archivo /etc/yum.repos.d/CentOS-Base.repo por los siguientes repositorios.  De este modo se agregará también el repositorio **[openlogic]** que incluye paquetes para el agente de Linux de Azure:
    
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
    
    **Nota:** en el resto de esta guía se da por hecho que está usando al menos el repositorio [openlogic], que se usará para instalar el agente de Linux de Azure a continuación.
11. Agregue la línea siguiente a /etc/yum.conf:
    
     http_caching=packages
    
    Y **solo en CentOS 6.3** , agregue la línea siguiente:
    
     exclude=kernel*
12. Deshabilite "fastestmirror" en el módulo yum; para ello, edite el archivo "/etc/yum/pluginconf.d/fastestmirror.conf" y en `[main]`, escriba lo siguiente:
    
        set enabled=0
13. Ejecute el comando siguiente para borrar los metadatos de yum actuales:
    
    # <a name="yum-clean-all"></a>yum clean all
14. **Solo en CentOS 6.3**, actualice el kernel usando el comando siguiente:
    
        # sudo yum --disableexcludes=all install kernel
15. Modifique la línea de arranque de kernel de su configuración grub para que incluya parámetros de kernel adicionales para Azure. Para ello, abra "/boot/grub/menu.lst" en un editor de texto y asegúrese de que el kernel predeterminado incluye los parámetros siguientes:
    
     console=ttyS0 earlyprintk=ttyS0 rootdelay=300 numa=off
    
    Así también se asegurará de que todos los mensajes de la consola se envían al primer puerto serie, lo que puede ayudar al soporte técnico de Azure con los problemas de depuración de errores. Esto deshabilitará NUMA debido a un error en la versión del kernel usada por CentOS 6.
    
    Además de lo anterior, se recomienda *quitar* los parámetros siguientes:
    
     rhgb quiet crashkernel=auto
    
    Los arranques gráfico y silencioso no resultan útiles en un entorno de nube, donde queremos que todos los registros se envíen al puerto serie.
    
    Es posible dejar la opción `crashkernel` configurada si así se desea, pero tenga en cuenta que este parámetro reducirá la cantidad de memoria disponible en la máquina virtual en 128 MB o más, lo cual puede resultar problemático en tamaños de VM más reducidos.
16. Asegúrese de que el servidor SSH se haya instalado y configurado para iniciarse en el tiempo de arranque.  Este es normalmente el valor predeterminado.
17. Instale el Agente de Linux de Azure ejecutando el comando siguiente:
    
        # sudo yum install WALinuxAgent
    
    Tenga en cuanta que al instalar el paquete WALinuxAgent se eliminarán los paquetes NetworkManager y NetworkManager-gnome, si es que aún no se han eliminado como se indica en el paso 2.
18. No cree espacio de intercambio en el disco del SO.
    
    El Agente de Linux de Azure puede configurar automáticamente un espacio de intercambio utilizando el disco de recursos local que se adjunta a la máquina virtual después de aprovisionarse en Azure. Tenga en cuenta que el disco de recursos local es un disco *temporal* que debe vaciarse cuando la máquina virtual se desaprovisiona. Después de instalar el Agente de Linux de Azure (consulte el paso anterior), modifique apropiadamente los parámetros siguientes en /etc/waagent.conf:
    
     ResourceDisk.Format=y  ResourceDisk.Filesystem=ext4  ResourceDisk.MountPoint=/mnt/resource  ResourceDisk.EnableSwap=y  ResourceDisk.SwapSizeMB=2048    ## NOTA: Establezca este valor en lo que necesite que sea.
19. Ejecute los comandos siguientes para desaprovisionar la máquina virtual y prepararla para aprovisionarse en Azure:
    
    # <a name="sudo-waagent--force--deprovision"></a>sudo waagent -force -deprovision
    # <a name="export-histsize0"></a>export HISTSIZE=0
    # <a name="logout"></a>logout
20. Haga clic en **Acción -> Apagar** en el Administrador de Hyper-V. El VHD de Linux ya está listo para cargarse en Azure.

- - -
## <a name="centos-70"></a>CentOS 7.0+
**Cambios en CentOS 7 (y derivados similares)**

La preparación de una máquina virtual CentOS 7 para Azure es muy similar a CentOS 6, aunque hay varias diferencias importantes que es necesario tener en cuenta:

* El paquete NetworkManager ya no entra en conflicto con el agente de Linux de Azure. Este paquete está instalado de manera predeterminada y recomendamos que no se quite.
* GRUB2 se utiliza ahora como cargador de arranque predeterminado; por ello, el proceso de edición de los parámetros de kernel ha cambiado (ver más adelante).
* XFS es ahora el sistema de archivos predeterminado. El sistema de archivos ext4 se puede seguir utilizando si así se desea.

**Pasos de configuración**

1. En el Administrador de Hyper-V, seleccione la máquina virtual.
2. Haga clic en **Conectar** para abrir una ventana de consola de la máquina virtual.
3. Cree un archivo llamado **network** in the `/etc/sysconfig/` que contenga el texto siguiente:
   
     NETWORKING=yes   HOSTNAME=localhost.localdomain
4. Cree un archivo llamado **ifcfg-eth0** in the `/etc/sysconfig/network-scripts/` que contenga el texto siguiente:
   
     DEVICE=eth0   ONBOOT=yes   BOOTPROTO=dhcp   TYPE=Ethernet   USERCTL=no   PEERDNS=yes   IPV6INIT=no
5. Modifique las reglas udev a fin de impedir que se generen reglas estáticas para las interfaces Ethernet. Estas reglas pueden causar problemas al clonar una máquina virtual en Microsoft Azure o Hyper-V:
   
   # <a name="sudo-ln--s-devnull-etcudevrulesd75-persistent-net-generatorrules"></a>sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
6. Asegúrese de que el servicio de red se inicie en el arranque ejecutando el comando siguiente:
   
        # sudo chkconfig network on
7. Instale el paquete python-pyasn1 ejecutando el comando siguiente:
   
        # sudo yum install python-pyasn1
8. Si desea utilizar los espejos OpenLogic hospedados en los centros de datos de Azure, sustituya el archivo /etc/yum.repos.d/CentOS-Base.repo por los siguientes repositorios.  De este modo se agregará también el repositorio **[openlogic]** que incluye paquetes para el agente de Linux de Azure:
   
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

    **Nota:** en el resto de esta guía se da por hecho que está usando al menos el repositorio [openlogic], que se usará para instalar el agente de Linux de Azure a continuación.

1. Ejecute el comando siguiente para borrar los metadatos de yum actuales e instalar las actualizaciones:
   
   # <a name="sudo-yum-clean-all"></a>sudo yum clean all
   # <a name="sudo-yum--y-update"></a>sudo yum -y update
2. Modifique la línea de arranque de kernel de su configuración grub para que incluya parámetros de kernel adicionales para Azure. Para ello, abra "/etc/default/grub" en un editor de texto y edite el parámetro `GRUB_CMDLINE_LINUX` ; por ejemplo:
   
    GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
   
   Así también se asegurará de que todos los mensajes de la consola se envían al primer puerto serie, lo que puede ayudar al soporte técnico de Azure con los problemas de depuración de errores. Esto también desactiva las nuevas convenciones de nomenclatura de CentOS 7 para NIC. Además de lo anterior, se recomienda *quitar* los parámetros siguientes:
   
    rhgb quiet crashkernel=auto
   
   Los arranques gráfico y silencioso no resultan útiles en un entorno de nube, donde queremos que todos los registros se envíen al puerto serie.
   
   Es posible dejar la opción `crashkernel` configurada si así se desea, pero tenga en cuenta que este parámetro reducirá la cantidad de memoria disponible en la máquina virtual en 128 MB o más, lo cual puede resultar problemático en tamaños de VM más reducidos.
3. Una vez que haya finalizado de editar "/etc/default/grub" como se indica anteriormente, ejecute el comando siguiente para volver a compilar la configuración de grub:
   
       # sudo grub2-mkconfig -o /boot/grub2/grub.cfg
4. Asegúrese de que el servidor SSH se haya instalado y configurado para iniciarse en el tiempo de arranque.  Este es normalmente el valor predeterminado.
5. **Solo si la imagen se crea desde VMWare, VirtualBox o KVM:** agregue módulos de Hyper-V en initramfs:
   
   Edite `/etc/dracut.conf`y agregue contenido:
   
    add_drivers+=”hv_vmbus hv_netvsc hv_storvsc”
   
   Vuelva a generar initramfs:
   
   # <a name="dracut-f--v"></a>dracut –f -v
6. Instale el Agente de Linux de Azure ejecutando el comando siguiente:
   
       # sudo yum install WALinuxAgent
       # sudo systemctl enable waagent
7. No cree espacio de intercambio en el disco del SO.
   
   El Agente de Linux de Azure puede configurar automáticamente un espacio de intercambio utilizando el disco de recursos local que se adjunta a la máquina virtual después de aprovisionarse en Azure. Tenga en cuenta que el disco de recursos local es un disco *temporal* que debe vaciarse cuando la máquina virtual se desaprovisiona. Después de instalar el Agente de Linux de Azure (consulte el paso anterior), modifique apropiadamente los parámetros siguientes en /etc/waagent.conf:
   
    ResourceDisk.Format=y  ResourceDisk.Filesystem=ext4  ResourceDisk.MountPoint=/mnt/resource  ResourceDisk.EnableSwap=y  ResourceDisk.SwapSizeMB=2048    ## NOTA: Establezca este valor en lo que necesite que sea.
8. Ejecute los comandos siguientes para desaprovisionar la máquina virtual y prepararla para aprovisionarse en Azure:
   
   # <a name="sudo-waagent--force--deprovision"></a>sudo waagent -force -deprovision
   # <a name="export-histsize0"></a>export HISTSIZE=0
   # <a name="logout"></a>logout
9. Haga clic en **Acción -> Apagar** en el Administrador de Hyper-V. El VHD de Linux ya está listo para cargarse en Azure.

## <a name="next-steps"></a>Pasos siguientes
Ya está listo para usar el disco duro virtual de CentOS para crear nuevas máquinas virtuales de Azure. Si es la primera vez que carga el archivo .vhd en Azure, consulte los pasos 2 y 3 de [Creación y carga de un disco duro virtual que contiene el sistema operativo Linux](virtual-machines-linux-classic-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).




<!--HONumber=Nov16_HO3-->


