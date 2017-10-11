---
title: "Creación y carga de un VHD de Red Hat Enterprise Linux para su uso en Azure | Microsoft Docs"
description: Aprenda a crear y cargar un disco duro virtual (VHD) de Azure que contiene un sistema operativo Red Hat Linux.
services: virtual-machines-linux
documentationcenter: 
author: szarkos
manager: timlt
editor: tysonn
tags: azure-resource-manager,azure-service-management
ms.assetid: 6c6b8f72-32d3-47fa-be94-6cb54537c69f
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 04/28/2017
ms.author: szark
ms.openlocfilehash: b753c76b8c3d789c681d7fbff6aa07590b860be5
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/18/2017
---
# <a name="prepare-a-red-hat-based-virtual-machine-for-azure"></a>Preparación de una máquina virtual basada en Red Hat para Azure
En este artículo, aprenderá a preparar una máquina virtual de Red Hat Enterprise Linux (RHEL) para usarla en Azure. Las versiones de RHEL que se tratan en este artículo son 6.7 y 7.1. Los hipervisores de preparación que se tratan en este artículo son Hyper-V, máquina virtual basada en kernel (KVM) y VMware. Para más información sobre los requisitos para poder participar en el programa de acceso a la nube de Red Hat, visite el sitio [web de acceso a la nube de Red Hat](http://www.redhat.com/en/technologies/cloud-computing/cloud-access) y [Ejecución de RHEL en Azure](https://access.redhat.com/ecosystem/ccsp/microsoft-azure).

## <a name="prepare-a-red-hat-based-virtual-machine-from-hyper-v-manager"></a>Preparación de una máquina virtual basada en Red Hat desde el Administrador de Hyper-V

### <a name="prerequisites"></a>Requisitos previos
En esta sección, se supone que ya obtuvo un archivo ISO en el sitio web de Red Hat y que ha instalado la imagen RHEL en un disco duro virtual (VHD). Para más información acerca de cómo usar el Administrador de Hyper-V para instalar una imagen de sistema operativo, vea [Instalar Hyper-V y crear una máquina virtual](http://technet.microsoft.com/library/hh846766.aspx).

**Notas de instalación de RHEL**

* Azure no admite el formato VHDX. Azure solo admite VHD fijo. Puede usar el Administrador de Hyper-V para convertir el disco al formato VHD, o puede usar el cmdlet convert-vhd. Si usa VirtualBox, seleccione **Tamaño fijo** a diferencia de la opción predeterminada asignada dinámicamente al crear el disco.
* Azure solo admite máquinas virtuales de la generación 1. Puede convertir una máquina virtual de generación 1 de VHDX para el formato de archivo VHD desde un disco de expansión dinámica a otro de tamaño fijo. No puede cambiar la generación de una máquina virtual. Para más información, consulte [¿Debería crear una máquina virtual de generación 1 o 2 en Hyper-V?](https://technet.microsoft.com/windows-server-docs/compute/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v).
* El tamaño máximo permitido para los discos duros virtuales es de 1023 GB.
* Al instalar el sistema operativo Linux se recomienda usar las particiones estándar en lugar de Logical Volume Manager (LVM) que a menudo viene de forma predeterminada en muchas instalaciones. Esta práctica evitará los conflictos de nombres LVM con máquinas virtuales clonadas, especialmente si alguna vez necesita conectar un disco de sistema operativo a otra máquina virtual idéntica para solucionar el problema. [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) o [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) se pueden utilizar en discos de datos.
* Se requiere la compatibilidad de kernel para el montaje de sistemas de archivos de formato de disco universal (UDF). Al arrancar Azure la primera vez, los medios con formato UDF conectados al invitado pasan la configuración de aprovisionamiento a la máquina virtual Linux. El agente Linux de Azure debe poder montar el sistema de archivos UDF para leer su configuración y aprovisionar la máquina virtual.
* Las versiones del kernel de Linux anteriores a 2.6.37 no admiten el acceso a memoria no uniforme (NUMA) en Hyper-V con tamaños de máquina virtual mayores. Este problema afecta principalmente a las distribuciones anteriores que usan el kernel Red Hat 2.6.32 de canal de subida y se ha corregido en RHEL 6.6 (kernel-2.6.32-504). Los sistemas que ejecutan kernels personalizados cuyas versiones son anteriores a la versión 2.6.37, o bien kernels basados en RHEL cuyas versiones son anteriores a la versión 2.6.32-504, deben establecer el parámetro de inicio `numa=off` en la línea de comandos de kernel en grub.conf. Para más información, consulte Red Hat [KB 436883](https://access.redhat.com/solutions/436883).
* No configure una partición de intercambio en el disco del sistema operativo. El agente de Linux se puede configurar para crear un archivo de intercambio en el disco de recursos temporal.  Puede encontrar más información al respecto en los pasos siguientes.
* El tamaño de todos los VHD debe ser múltiplo de 1 MB.

### <a name="prepare-a-rhel-6-virtual-machine-from-hyper-v-manager"></a>Preparar una máquina virtual RHEL 6 desde el Administrador de Hyper-V

1. En el Administrador de Hyper-V, seleccione la máquina virtual.

2. Haga clic en **Conectar** para abrir una ventana de consola de la máquina virtual.

3. En RHEL 6, NetworkManager puede interferir en el Agente de Linux de Azure. Desinstale el paquete ejecutando el comando siguiente:
   
        # sudo rpm -e --nodeps NetworkManager

4. Cree o edite el archivo `/etc/sysconfig/network` y agregue el siguiente texto:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain

5. Cree o edite el archivo `/etc/sysconfig/network-scripts/ifcfg-eth0` y agregue el siguiente texto:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

6. Mueva (o elimine) las reglas udev para impedir que se generen reglas estáticas para la interfaz Ethernet. Estas reglas pueden causar problemas cuando clone una máquina virtual en Microsoft Azure o Hyper-V:

        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
        
        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules

7. Asegúrese de que el servicio de red se inicia en el arranque ejecutando el comando siguiente:

        # sudo chkconfig network on

8. Registre la suscripción de Red Hat para habilitar la instalación de paquetes desde el repositorio RHEL ejecutando el siguiente comando:

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

9. El paquete WALinuxAgent `WALinuxAgent-<version>` se ha insertado en el repositorio de extras de Red Hat. Habilite el repositorio de extras ejecutando el comando siguiente:

        # subscription-manager repos --enable=rhel-6-server-extras-rpms

10. Modifique la línea de arranque de kernel de su configuración grub para que incluya parámetros de kernel adicionales para Azure. Para realizar esta modificación, abra `/boot/grub/menu.lst` en un editor de texto y asegúrese de que el kernel predeterminado incluye los parámetros siguientes:
    
        console=ttyS0 earlyprintk=ttyS0 rootdelay=300
    
    Así también se asegurará de que todos los mensajes de la consola se envían al primer puerto serie, lo que puede ayudar al soporte técnico de Azure con los problemas de depuración de errores.
    
    Además, se recomienda quitar los parámetros siguientes:
    
        rhgb quiet crashkernel=auto
    
    Los arranques gráfico y silencioso no resultan útiles en un entorno de nube, donde queremos que todos los registros se envíen al puerto serie.  Puede dejar la opción `crashkernel` configurada si lo desea. Tenga en cuenta que este parámetro reduce la cantidad de memoria disponible en la máquina virtual unos 128 MB o más. Esta configuración podría resultar problemática en tamaños de máquinas virtuales más pequeños.

    >[!Important]
    RHEL 6.5 y las versiones anteriores también deben establecer el parámetro `numa=off` del kernel. Consulte Red Hat [KB 436883](https://access.redhat.com/solutions/436883).

11. Asegúrese de que el servidor Secure Shell (SSH) está instalado y configurado para iniciarse en el tiempo de arranque, que suele ser el predeterminado. Modifique /etc/ssh/sshd_config para que incluya la siguiente línea:

        ClientAliveInterval 180

12. Instale el Agente de Linux de Azure ejecutando el comando siguiente:

        # sudo yum install WALinuxAgent

        # sudo chkconfig waagent on

    La instalación del paquete WALinuxAgent elimina los paquetes NetworkManager y NetworkManager-gnome, si es que aún no se han quitado en el paso 3.

13. No cree espacio de intercambio en el disco del sistema operativo.

    El Agente de Linux de Azure puede configurar automáticamente un espacio de intercambio usando el disco de recursos local que se adjunta a la máquina virtual, después de que la máquina virtual se aprovisione en Azure. Tenga en cuenta que el disco de recursos local es un disco temporal que debe vaciarse cuando la máquina virtual se desaprovisiona. Después de instalar el agente de Linux de Azure en el paso anterior, modifique apropiadamente los parámetros siguientes en /etc/waagent.conf:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

14. Para anular el registro de la suscripción (si es necesario), ejecute el siguiente comando:

        # sudo subscription-manager unregister

15. Ejecute los comandos siguientes para desaprovisionar la máquina virtual y prepararla para aprovisionarse en Azure:

        # sudo waagent -force -deprovision

        # export HISTSIZE=0

        # logout

16. Haga clic en **Acción**  >  **Apagar** en el Administrador de Hyper-V. El VHD de Linux ya está listo para cargarse en Azure.


### <a name="prepare-a-rhel-7-virtual-machine-from-hyper-v-manager"></a>Preparar una máquina virtual RHEL 7 desde el Administrador de Hyper-V

1. En el Administrador de Hyper-V, seleccione la máquina virtual.

2. Haga clic en **Conectar** para abrir una ventana de consola de la máquina virtual.

3. Cree o edite el archivo `/etc/sysconfig/network` y agregue el siguiente texto:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain

4. Cree o edite el archivo `/etc/sysconfig/network-scripts/ifcfg-eth0` y agregue el siguiente texto:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
        NM_CONTROLLED=no

5. Asegúrese de que el servicio de red se inicia en el arranque ejecutando el comando siguiente:

        # sudo chkconfig network on

6. Registre la suscripción de Red Hat para habilitar la instalación de paquetes desde el repositorio RHEL ejecutando el siguiente comando:

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

7. Modifique la línea de arranque de kernel de su configuración grub para que incluya parámetros de kernel adicionales para Azure. Para realizar esta modificación, abra `/etc/default/grub` en un editor de texto y edite el parámetro `GRUB_CMDLINE_LINUX`. Por ejemplo:
   
        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
   
   Así también se asegurará de que todos los mensajes de la consola se envían al primer puerto serie, lo que puede ayudar al soporte técnico de Azure con los problemas de depuración de errores. Esta configuración también desactiva las nuevas convenciones de nomenclatura de RHEL 7 para NIC. Además, se recomienda quitar los parámetros siguientes:
   
        rhgb quiet crashkernel=auto
   
    Los arranques gráfico y silencioso no resultan útiles en un entorno de nube, donde queremos que todos los registros se envíen al puerto serie. Puede dejar la opción `crashkernel` configurada si lo desea. Tenga en cuenta que este parámetro reduce la cantidad de memoria disponible en la máquina virtual mediante 128 MB o más, lo que podría resultar problemática en tamaños de máquina virtual más pequeños.

8. Una vez que termine de editar `/etc/default/grub`, ejecute el comando siguiente para recompilar la configuración de GRUB:

        # sudo grub2-mkconfig -o /boot/grub2/grub.cfg

9. Asegúrese de que el servidor SSH se haya instalado y configurado para iniciarse en el tiempo de arranque, que suele ser el predeterminado. Modifique `/etc/ssh/sshd_config` para que incluya la siguiente línea:

        ClientAliveInterval 180

10. El paquete WALinuxAgent `WALinuxAgent-<version>` se ha insertado en el repositorio de extras de Red Hat. Habilite el repositorio de extras ejecutando el comando siguiente:

        # subscription-manager repos --enable=rhel-7-server-extras-rpms

11. Instale el Agente de Linux de Azure ejecutando el comando siguiente:

        # sudo yum install WALinuxAgent

        # sudo systemctl enable waagent.service

12. No cree espacio de intercambio en el disco del sistema operativo.

    El Agente de Linux de Azure puede configurar automáticamente un espacio de intercambio usando el disco de recursos local que se adjunta a la máquina virtual, después de que la máquina virtual se aprovisione en Azure. Tenga en cuenta que el disco de recursos local es un disco temporal que debe vaciarse cuando la máquina virtual se desaprovisiona. Después de instalar el agente de Linux de Azure en el paso anterior, modifique apropiadamente los parámetros siguientes en `/etc/waagent.conf`:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

13. Si quiere anular el registro de la suscripción, ejecute el siguiente comando:

        # sudo subscription-manager unregister

14. Ejecute los comandos siguientes para desaprovisionar la máquina virtual y prepararla para aprovisionarse en Azure:

        # sudo waagent -force -deprovision

        # export HISTSIZE=0

        # logout

15. Haga clic en **Acción**  >  **Apagar** en el Administrador de Hyper-V. El VHD de Linux ya está listo para cargarse en Azure.


## <a name="prepare-a-red-hat-based-virtual-machine-from-kvm"></a>Preparación de una máquina virtual basada en Red Hat desde KVM
### <a name="prepare-a-rhel-6-virtual-machine-from-kvm"></a>Preparar una máquina virtual RHEL 6 desde KVM

1. Descargue la imagen KVM de RHEL 6 desde el sitio web de Red Hat.

2. Establezca una contraseña raíz.

    Genere una contraseña cifrada y copie el resultado del comando:

        # openssl passwd -1 changeme

    Establezca una contraseña raíz con guestfish:
        
        # guestfish --rw -a <image-name>
        > <fs> run
        > <fs> list-filesystems
        > <fs> mount /dev/sda1 /
        > <fs> vi /etc/shadow
        > <fs> exit

   Cambie el segundo campo del usuario raíz de "!!" a la contraseña cifrada.

3. Cree una máquina virtual en KVM a partir de la imagen qcow2. Establezca el tipo de disco en **qcow2** y el modelo de dispositivo de interfaz de red virtual en **virtio**. Después, inicie la máquina virtual e inicie sesión como raíz.

4. Cree o edite el archivo `/etc/sysconfig/network` y agregue el siguiente texto:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain

5. Cree o edite el archivo `/etc/sysconfig/network-scripts/ifcfg-eth0` y agregue el siguiente texto:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

6. Mueva (o elimine) las reglas udev para impedir que se generen reglas estáticas para la interfaz Ethernet. Estas reglas pueden causar problemas cuando clone una máquina virtual en Azure o Hyper-V:

        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules

        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules

7. Asegúrese de que el servicio de red se inicia en el arranque ejecutando el comando siguiente:

        # chkconfig network on

8. Registre la suscripción de Red Hat para habilitar la instalación de paquetes desde el repositorio RHEL ejecutando el siguiente comando:

        # subscription-manager register --auto-attach --username=XXX --password=XXX

9. Modifique la línea de arranque de kernel de su configuración grub para que incluya parámetros de kernel adicionales para Azure. Para realizar esta configuración, abra `/boot/grub/menu.lst` en un editor de texto y asegúrese de que el kernel predeterminado incluye los parámetros siguientes:
    
        console=ttyS0 earlyprintk=ttyS0 rootdelay=300
    
    Así también se asegurará de que todos los mensajes de la consola se envían al primer puerto serie, lo que puede ayudar al soporte técnico de Azure con los problemas de depuración de errores.
    
    Además, se recomienda quitar los parámetros siguientes:
    
        rhgb quiet crashkernel=auto
    
    Los arranques gráfico y silencioso no resultan útiles en un entorno de nube, donde queremos que todos los registros se envíen al puerto serie. Puede dejar la opción `crashkernel` configurada si lo desea. Tenga en cuenta que este parámetro reduce la cantidad de memoria disponible en la máquina virtual mediante 128 MB o más, lo que podría resultar problemática en tamaños de máquina virtual más pequeños.

    >[!Important]
    RHEL 6.5 y las versiones anteriores también deben establecer el parámetro `numa=off` del kernel. Consulte Red Hat [KB 436883](https://access.redhat.com/solutions/436883).

10. Agregue módulos de Hyper-V a initramfs:  

    Edite `/etc/dracut.conf` y agregue el siguiente contenido:

        add_drivers+="hv_vmbus hv_netvsc hv_storvsc"

    Recompile initramfs:

        # dracut -f -v

11. Desinstale cloud-init:

        # yum remove cloud-init

12. Asegúrese de que el servidor SSH esté instalado y configurado para iniciarse en el tiempo de arranque:

        # chkconfig sshd on

    Modifique /etc/ssh/sshd_config para que incluya las siguientes líneas:

        PasswordAuthentication yes
        ClientAliveInterval 180

13. El paquete WALinuxAgent `WALinuxAgent-<version>` se ha insertado en el repositorio de extras de Red Hat. Habilite el repositorio de extras ejecutando el comando siguiente:

        # subscription-manager repos --enable=rhel-6-server-extras-rpms

14. Instale el Agente de Linux de Azure ejecutando el comando siguiente:

        # yum install WALinuxAgent

        # chkconfig waagent on

15. El Agente de Linux de Azure puede configurar automáticamente un espacio de intercambio usando el disco de recursos local que se adjunta a la máquina virtual, después de que la máquina virtual se aprovisione en Azure. Tenga en cuenta que el disco de recursos local es un disco temporal que debe vaciarse cuando la máquina virtual se desaprovisiona. Después de instalar el agente de Linux de Azure en el paso anterior, modifique apropiadamente los parámetros siguientes en **/etc/waagent.conf**:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

16. Para anular el registro de la suscripción (si es necesario), ejecute el siguiente comando:

        # subscription-manager unregister

17. Ejecute los comandos siguientes para desaprovisionar la máquina virtual y prepararla para aprovisionarse en Azure:

        # waagent -force -deprovision

        # export HISTSIZE=0

        # logout

18. Apague la máquina virtual en KVM.

19. Convierta la imagen qcow2 al formato VHD.

    Primero convierta la imagen al formato raw:

        # qemu-img convert -f qcow2 -O raw rhel-6.8.qcow2 rhel-6.8.raw

    Asegúrese de que el tamaño de la imagen sin procesar está alineado con 1 MB. De lo contrario, redondee hacia arriba el tamaño para alinear con 1 MB:

        # MB=$((1024*1024))
        # size=$(qemu-img info -f raw --output json "rhel-6.8.raw" | \
          gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

        # rounded_size=$((($size/$MB + 1)*$MB))
        # qemu-img resize rhel-6.8.raw $rounded_size

    Convierta el disco sin procesar en un disco duro virtual (VHD) de tamaño fijo:

        # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-6.8.raw rhel-6.8.vhd


### <a name="prepare-a-rhel-7-virtual-machine-from-kvm"></a>Preparar una máquina virtual RHEL 7 desde KVM

1. Descargue la imagen KVM de RHEL 7 desde el sitio web de Red Hat. Este procedimiento utiliza RHEL 7 como el ejemplo.

2. Establezca una contraseña raíz.

    Genere una contraseña cifrada y copie el resultado del comando:

        # openssl passwd -1 changeme

    Establezca una contraseña raíz con guestfish:

        # guestfish --rw -a <image-name>
        > <fs> run
        > <fs> list-filesystems
        > <fs> mount /dev/sda1 /
        > <fs> vi /etc/shadow
        > <fs> exit

   Cambie el segundo campo de usuario raíz de "!!" a la contraseña cifrada.

3. Cree una máquina virtual en KVM a partir de la imagen qcow2. Establezca el tipo de disco en **qcow2** y el modelo de dispositivo de interfaz de red virtual en **virtio**. Después, inicie la máquina virtual e inicie sesión como raíz.

4. Cree o edite el archivo `/etc/sysconfig/network` y agregue el siguiente texto:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain

5. Cree o edite el archivo `/etc/sysconfig/network-scripts/ifcfg-eth0` y agregue el siguiente texto:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
        NM_CONTROLLED=no

6. Asegúrese de que el servicio de red se inicia en el arranque ejecutando el comando siguiente:

        # chkconfig network on

7. Registre la suscripción de RedHat para habilitar la instalación de paquetes desde el repositorio RHEL ejecutando el siguiente comando:

        # subscription-manager register --auto-attach --username=XXX --password=XXX

8. Modifique la línea de arranque de kernel de su configuración grub para que incluya parámetros de kernel adicionales para Azure. Para realizar esta configuración, abra `/etc/default/grub` en un editor de texto y edite el parámetro `GRUB_CMDLINE_LINUX`. Por ejemplo:
   
        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
   
   Este comando también asegura que todos los mensajes de la consola se envían al primer puerto serie, lo que puede ayudar al soporte técnico de Azure con los problemas de depuración de errores. El comando también desactiva las nuevas convenciones de nomenclatura de RHEL 7 para NIC. Además, se recomienda quitar los parámetros siguientes:
   
        rhgb quiet crashkernel=auto
   
    Los arranques gráfico y silencioso no resultan útiles en un entorno de nube, donde queremos que todos los registros se envíen al puerto serie. Puede dejar la opción `crashkernel` configurada si lo desea. Tenga en cuenta que este parámetro reduce la cantidad de memoria disponible en la máquina virtual mediante 128 MB o más, lo que podría resultar problemática en tamaños de máquina virtual más pequeños.

9. Una vez que termine de editar `/etc/default/grub`, ejecute el comando siguiente para recompilar la configuración de GRUB:

        # grub2-mkconfig -o /boot/grub2/grub.cfg

10. Agregue módulos de Hyper-V a initramfs.

    Edite `/etc/dracut.conf` y agregue contenido:

        add_drivers+="hv_vmbus hv_netvsc hv_storvsc"

    Recompile initramfs:

        # dracut -f -v

11. Desinstale cloud-init:

        # yum remove cloud-init

12. Asegúrese de que el servidor SSH esté instalado y configurado para iniciarse en el tiempo de arranque:

        # systemctl enable sshd

    Modifique /etc/ssh/sshd_config para que incluya las siguientes líneas:

        PasswordAuthentication yes
        ClientAliveInterval 180

13. El paquete WALinuxAgent `WALinuxAgent-<version>` se ha insertado en el repositorio de extras de Red Hat. Habilite el repositorio de extras ejecutando el comando siguiente:

        # subscription-manager repos --enable=rhel-7-server-extras-rpms

14. Instale el Agente de Linux de Azure ejecutando el comando siguiente:

        # yum install WALinuxAgent

    Habilite el servicio waagent:

        # systemctl enable waagent.service

15. No cree espacio de intercambio en el disco del sistema operativo.

    El Agente de Linux de Azure puede configurar automáticamente un espacio de intercambio usando el disco de recursos local que se adjunta a la máquina virtual, después de que la máquina virtual se aprovisione en Azure. Tenga en cuenta que el disco de recursos local es un disco temporal que debe vaciarse cuando la máquina virtual se desaprovisiona. Después de instalar el agente de Linux de Azure en el paso anterior, modifique apropiadamente los parámetros siguientes en `/etc/waagent.conf`:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

16. Para anular el registro de la suscripción (si es necesario), ejecute el siguiente comando:

        # subscription-manager unregister

17. Ejecute los comandos siguientes para desaprovisionar la máquina virtual y prepararla para aprovisionarse en Azure:

        # sudo waagent -force -deprovision

        # export HISTSIZE=0

        # logout

18. Apague la máquina virtual en KVM.

19. Convierta la imagen qcow2 al formato VHD.

    Primero convierta la imagen al formato raw:

        # qemu-img convert -f qcow2 -O raw rhel-7.3.qcow2 rhel-7.3.raw

    Asegúrese de que el tamaño de la imagen sin procesar está alineado con 1 MB. De lo contrario, redondee hacia arriba el tamaño para alinear con 1 MB:

        # MB=$((1024*1024))
        # size=$(qemu-img info -f raw --output json "rhel-6.8.raw" | \
          gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

        # rounded_size=$((($size/$MB + 1)*$MB))
        # qemu-img resize rhel-6.8.raw $rounded_size

    Convierta el disco sin procesar en un disco duro virtual (VHD) de tamaño fijo:

        # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-7.3.raw rhel-7.3.vhd

## <a name="prepare-a-red-hat-based-virtual-machine-from-vmware"></a>Preparación de una máquina virtual basada en Red Hat para VMware
### <a name="prerequisites"></a>Requisitos previos
En esta sección se supone que ya instaló una máquina virtual RHEL en VMware. Para más información acerca de cómo instalar un sistema operativo en VMware, consulte [VMware Guest Operating System Installation Guide](http://partnerweb.vmware.com/GOSIG/home.html)(Guía de instalación de sistema operativo invitado de VMware).

* Al instalar el sistema Linux se recomienda usar las particiones estándar en lugar de un LVM, que a menudo viene de forma predeterminada en muchas instalaciones. De este modo se impedirá que el nombre del LVM entre en conflicto con las máquinas virtuales clonadas, especialmente si en algún momento hace falta adjuntar un disco de sistema operativo a otra máquina virtual para solucionar problemas. Para los discos de datos se puede utilizar LVM o RAID si así se prefiere.
* No configure una partición de intercambio en el disco del sistema operativo. Es posible configurar el agente de Linux para crear un archivo de intercambio en el disco de recursos temporal. Puede encontrar más información al respecto en los pasos a continuación.
* Cuando cree el disco duro virtual, seleccione **Almacenar disco virtual como un solo archivo**.

### <a name="prepare-a-rhel-6-virtual-machine-from-vmware"></a>Preparar una máquina virtual RHEL 6 desde VMware
1. En RHEL 6, NetworkManager puede interferir en el Agente de Linux de Azure. Desinstale el paquete ejecutando el comando siguiente:
   
        # sudo rpm -e --nodeps NetworkManager

2. Cree un archivo llamado **network** en el directorio /etc/sysconfig/ que contenga el texto siguiente:

        NETWORKING=yes
        HOSTNAME=localhost.localdomain

3. Cree o edite el archivo `/etc/sysconfig/network-scripts/ifcfg-eth0` y agregue el siguiente texto:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

4. Mueva (o elimine) las reglas udev para impedir que se generen reglas estáticas para la interfaz Ethernet. Estas reglas pueden causar problemas cuando clone una máquina virtual en Azure o Hyper-V:

        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules

        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules

5. Asegúrese de que el servicio de red se inicia en el arranque ejecutando el comando siguiente:

        # sudo chkconfig network on

6. Registre la suscripción de Red Hat para habilitar la instalación de paquetes desde el repositorio RHEL ejecutando el siguiente comando:

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

7. El paquete WALinuxAgent `WALinuxAgent-<version>` se ha insertado en el repositorio de extras de Red Hat. Habilite el repositorio de extras ejecutando el comando siguiente:

        # subscription-manager repos --enable=rhel-6-server-extras-rpms

8. Modifique la línea de arranque de kernel de su configuración grub para que incluya parámetros de kernel adicionales para Azure. Para ello, abra `/etc/default/grub` en un editor de texto y edite el parámetro `GRUB_CMDLINE_LINUX`. Por ejemplo:
   
        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0"
   
   Así también se asegurará de que todos los mensajes de la consola se envían al primer puerto serie, lo que puede ayudar al soporte técnico de Azure con los problemas de depuración de errores. Además, se recomienda quitar los parámetros siguientes:
   
        rhgb quiet crashkernel=auto
   
    Los arranques gráfico y silencioso no resultan útiles en un entorno de nube, donde queremos que todos los registros se envíen al puerto serie. Puede dejar la opción `crashkernel` configurada si lo desea. Tenga en cuenta que este parámetro reduce la cantidad de memoria disponible en la máquina virtual mediante 128 MB o más, lo que podría resultar problemática en tamaños de máquina virtual más pequeños.

9. Agregue módulos de Hyper-V a initramfs:

    Edite `/etc/dracut.conf` y agregue el siguiente contenido:

        add_drivers+="hv_vmbus hv_netvsc hv_storvsc"

    Recompile initramfs:

        # dracut -f -v

10. Asegúrese de que el servidor SSH se haya instalado y configurado para iniciarse en el tiempo de arranque, que suele ser el predeterminado. Modifique `/etc/ssh/sshd_config` para que incluya la siguiente línea:

    ClientAliveInterval 180

11. Instale el Agente de Linux de Azure ejecutando el comando siguiente:

        # sudo yum install WALinuxAgent

        # sudo chkconfig waagent on

12. No cree espacio de intercambio en el disco del sistema operativo.

    El Agente de Linux de Azure puede configurar automáticamente un espacio de intercambio usando el disco de recursos local que se adjunta a la máquina virtual, después de que la máquina virtual se aprovisione en Azure. Tenga en cuenta que el disco de recursos local es un disco temporal que debe vaciarse cuando la máquina virtual se desaprovisiona. Después de instalar el agente de Linux de Azure en el paso anterior, modifique apropiadamente los parámetros siguientes en `/etc/waagent.conf`:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

13. Para anular el registro de la suscripción (si es necesario), ejecute el siguiente comando:

        # sudo subscription-manager unregister

14. Ejecute los comandos siguientes para desaprovisionar la máquina virtual y prepararla para aprovisionarse en Azure:

        # sudo waagent -force -deprovision

        # export HISTSIZE=0

        # logout

15. Apague la máquina virtual y convierta el archivo VMDK en un archivo .vhd.

    Primero convierta la imagen al formato raw:

        # qemu-img convert -f vmdk -O raw rhel-6.8.vmdk rhel-6.8.raw

    Asegúrese de que el tamaño de la imagen sin procesar está alineado con 1 MB. De lo contrario, redondee hacia arriba el tamaño para alinear con 1 MB:

        # MB=$((1024*1024))
        # size=$(qemu-img info -f raw --output json "rhel-6.8.raw" | \
          gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

        # rounded_size=$((($size/$MB + 1)*$MB))
        # qemu-img resize rhel-6.8.raw $rounded_size

    Convierta el disco sin procesar en un disco duro virtual (VHD) de tamaño fijo:

        # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-6.8.raw rhel-6.8.vhd

### <a name="prepare-a-rhel-7-virtual-machine-from-vmware"></a>Preparar una máquina virtual RHEL 7 desde VMware
1. Cree o edite el archivo `/etc/sysconfig/network` y agregue el siguiente texto:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain

2. Cree o edite el archivo `/etc/sysconfig/network-scripts/ifcfg-eth0` y agregue el siguiente texto:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
        NM_CONTROLLED=no

3. Asegúrese de que el servicio de red se inicia en el arranque ejecutando el comando siguiente:

        # sudo chkconfig network on

4. Registre la suscripción de Red Hat para habilitar la instalación de paquetes desde el repositorio RHEL ejecutando el siguiente comando:

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

5. Modifique la línea de arranque de kernel de su configuración grub para que incluya parámetros de kernel adicionales para Azure. Para realizar esta modificación, abra `/etc/default/grub` en un editor de texto y edite el parámetro `GRUB_CMDLINE_LINUX`. Por ejemplo:
   
        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
   
   Esta configuración también asegura que todos los mensajes de la consola se envían al primer puerto serie, lo que puede ayudar al soporte técnico de Azure con los problemas de depuración de errores. Esto también desactiva las nuevas convenciones de nomenclatura de RHEL 7 para NIC. Además, se recomienda quitar los parámetros siguientes:
   
        rhgb quiet crashkernel=auto
   
    Los arranques gráfico y silencioso no resultan útiles en un entorno de nube, donde queremos que todos los registros se envíen al puerto serie. Puede dejar la opción `crashkernel` configurada si lo desea. Tenga en cuenta que este parámetro reduce la cantidad de memoria disponible en la máquina virtual mediante 128 MB o más, lo que podría resultar problemática en tamaños de máquina virtual más pequeños.

6. Una vez que termine de editar `/etc/default/grub`, ejecute el comando siguiente para recompilar la configuración de GRUB:

        # sudo grub2-mkconfig -o /boot/grub2/grub.cfg

7. Agregue módulos de Hyper-V a initramfs.

    Edite `/etc/dracut.conf`y agregue contenido:

        add_drivers+="hv_vmbus hv_netvsc hv_storvsc"

    Recompile initramfs:

        # dracut -f -v

8. Asegúrese de que el servidor SSH se haya instalado y configurado para iniciarse en el tiempo de arranque. Esta configuración es normalmente el valor predeterminado. Modifique `/etc/ssh/sshd_config` para que incluya la siguiente línea:

        ClientAliveInterval 180

9. El paquete WALinuxAgent `WALinuxAgent-<version>` se ha insertado en el repositorio de extras de Red Hat. Habilite el repositorio de extras ejecutando el comando siguiente:

        # subscription-manager repos --enable=rhel-7-server-extras-rpms

10. Instale el Agente de Linux de Azure ejecutando el comando siguiente:

        # sudo yum install WALinuxAgent

        # sudo systemctl enable waagent.service

11. No cree espacio de intercambio en el disco del sistema operativo.

    El Agente de Linux de Azure puede configurar automáticamente un espacio de intercambio usando el disco de recursos local que se adjunta a la máquina virtual, después de que la máquina virtual se aprovisione en Azure. Tenga en cuenta que el disco de recursos local es un disco temporal que debe vaciarse cuando la máquina virtual se desaprovisiona. Después de instalar el agente de Linux de Azure en el paso anterior, modifique apropiadamente los parámetros siguientes en `/etc/waagent.conf`:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

12. Si quiere anular el registro de la suscripción, ejecute el siguiente comando:

        # sudo subscription-manager unregister

13. Ejecute los comandos siguientes para desaprovisionar la máquina virtual y prepararla para aprovisionarse en Azure:

        # sudo waagent -force -deprovision

        # export HISTSIZE=0

        # logout

14. Apague la máquina virtual y convierta el archivo VMDK al formato VHD.

    Primero convierta la imagen al formato raw:

        # qemu-img convert -f vmdk -O raw rhel-7.3.vmdk rhel-7.3.raw

    Asegúrese de que el tamaño de la imagen sin procesar está alineado con 1 MB. De lo contrario, redondee hacia arriba el tamaño para alinear con 1 MB:

        # MB=$((1024*1024))
        # size=$(qemu-img info -f raw --output json "rhel-6.8.raw" | \
          gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

        # rounded_size=$((($size/$MB + 1)*$MB))
        # qemu-img resize rhel-6.8.raw $rounded_size

    Convierta el disco sin procesar en un disco duro virtual (VHD) de tamaño fijo:

        # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-7.3.raw rhel-7.3.vhd

## <a name="prepare-a-red-hat-based-virtual-machine-from-an-iso-by-using-a-kickstart-file-automatically"></a>Preparación de una máquina virtual basada en Red Hat desde una imagen ISO con un archivo kickstart automáticamente
### <a name="prepare-a-rhel-7-virtual-machine-from-a-kickstart-file"></a>Preparar una máquina virtual RHEL 7 desde un archivo kickstart

1.  Cree un archivo kickstart que incluye el contenido siguiente y guarde el archivo. Para obtener información más detallada sobre la instalación de Kickstart, consulte la [guía de instalación de Kickstart](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Installation_Guide/chap-kickstart-installations.html).

        # Kickstart for provisioning a RHEL 7 Azure VM

        # System authorization information
          auth --enableshadow --passalgo=sha512

        # Use graphical install
        text

        # Do not run the Setup Agent on first boot
        firstboot --disable

        # Keyboard layouts
        keyboard --vckeymap=us --xlayouts='us'

        # System language
        lang en_US.UTF-8

        # Network information
        network  --bootproto=dhcp

        # Root password
        rootpw --plaintext "to_be_disabled"

        # System services
        services --enabled="sshd,waagent,NetworkManager"

        # System timezone
        timezone Etc/UTC --isUtc --ntpservers 0.rhel.pool.ntp.org,1.rhel.pool.ntp.org,2.rhel.pool.ntp.org,3.rhel.pool.ntp.org

        # Partition clearing information
        clearpart --all --initlabel

        # Clear the MBR
        zerombr

        # Disk partitioning information
        part /boot --fstype="xfs" --size=500
        part / --fstyp="xfs" --size=1 --grow --asprimary

        # System bootloader configuration
        bootloader --location=mbr

        # Firewall configuration
        firewall --disabled

        # Enable SELinux
        selinux --enforcing

        # Don't configure X
        skipx

        # Power down the machine after install
        poweroff

        %packages
        @base
        @console-internet
        chrony
        sudo
        parted
        -dracut-config-rescue

        %end

        %post --log=/var/log/anaconda/post-install.log

        #!/bin/bash

        # Register Red Hat Subscription
        subscription-manager register --username=XXX --password=XXX --auto-attach --force

        # Install latest repo update
        yum update -y

        # Enable extras repo
        subscription-manager repos --enable=rhel-7-server-extras-rpms

        # Install WALinuxAgent
        yum install -y WALinuxAgent

        # Unregister Red Hat subscription
        subscription-manager unregister

        # Enable waaagent at boot-up
        systemctl enable waagent

        # Disable the root account
        usermod root -p '!!'

        # Configure swap in WALinuxAgent
        sed -i 's/^\(ResourceDisk\.EnableSwap\)=[Nn]$/\1=y/g' /etc/waagent.conf
        sed -i 's/^\(ResourceDisk\.SwapSizeMB\)=[0-9]*$/\1=2048/g' /etc/waagent.conf

        # Set the cmdline
        sed -i 's/^\(GRUB_CMDLINE_LINUX\)=".*"$/\1="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"/g' /etc/default/grub

        # Enable SSH keepalive
        sed -i 's/^#\(ClientAliveInterval\).*$/\1 180/g' /etc/ssh/sshd_config

        # Build the grub cfg
        grub2-mkconfig -o /boot/grub2/grub.cfg

        # Configure network
        cat << EOF > /etc/sysconfig/network-scripts/ifcfg-eth0
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
        NM_CONTROLLED=no
        EOF

        # Deprovision and prepare for Azure
        waagent -force -deprovision

        %end

2. Coloque el archivo kickstart donde el sistema de la instalación puede acceder a él.

3. En el Administrador de Hyper-V, cree una nueva máquina virtual. En la página **Conectar disco duro virtual**, seleccione **Exponer un disco duro virtual más adelante** y complete el Asistente para crear nueva máquina virtual.

4. Abra la configuración de la máquina virtual:

    a.  Cargue un nuevo disco duro virtual en la máquina virtual. Asegúrese de seleccionar **Formato VHD** y **Tamaño fijo**.

    b.  Adjunte la imagen ISO de instalación a la unidad de DVD.

    c.  Configure el BIOS para que arranque desde el CD.

5. Inicie la máquina virtual. Cuando aparezca la guía de instalación, presione la tecla **Tab** para configurar las opciones de arranque.

6. Escriba `inst.ks=<the location of the kickstart file>` al final de las opciones de arranque y presione **Entrar**.

7. Espere hasta que la instalación se complete. Cuando haya finalizado, la máquina virtual se apagará automáticamente. El VHD de Linux ya está listo para cargarse en Azure.

## <a name="known-issues"></a>Problemas conocidos
### <a name="the-hyper-v-driver-could-not-be-included-in-the-initial-ram-disk-when-using-a-non-hyper-v-hypervisor"></a>El controlador de Hyper-V no se pudo incluir en el disco RAM inicial al usar un hipervisor de Hyper-V

En algunos casos, los instaladores de Linux pueden no incluir los controladores de Hyper-V en el disco RAM inicial (initrd o initramfs) a menos que Linux detecte que se está ejecutando un entorno de Hyper-V.

Cuando utilice un sistema de virtualización diferente (es decir, Virtualbox, KVM, etc.) para preparar su imagen de Linux, puede que necesite recompilar initrd para asegurarse de que al menos los módulos kernel hv_vmbus y hv_storvsc kernel están disponibles en el disco RAM inicial. Esto es un problema conocido al menos en sistemas basados en el nivel superior de distribución de Red Hat.

Para resolver este problema, agregue los módulos de Hyper-V en initramfs y recompilarlo:

Edite `/etc/dracut.conf` y agregue el siguiente contenido:

        add_drivers+="hv_vmbus hv_netvsc hv_storvsc"

Recompile initramfs:

        # dracut -f -v

Para obtener más detalles, consulte la información sobre cómo [recompilar initramfs](https://access.redhat.com/solutions/1958).

## <a name="next-steps"></a>Pasos siguientes
Ya está listo para usar el disco duro virtual de Red Hat Enterprise Linux para crear nuevas máquinas virtuales de Azure. Si es la primera vez que carga el archivo .vhd en Azure, consulte los pasos 2 y 3 de [Creación y carga de un disco duro virtual que contiene el sistema operativo Linux](classic/create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).

Para obtener información más detallada sobre los hipervisores certificados para ejecutar Red Hat Enterprise Linux, visite el [sitio web de Red Hat](https://access.redhat.com/certified-hypervisors).
