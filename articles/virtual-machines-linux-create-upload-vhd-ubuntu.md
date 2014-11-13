<properties urlDisplayName="Upload an Ubuntu Linux VHD" pageTitle="Creaci&oacute;n y carga de un VHD de Ubuntu Linux en Azure" metaKeywords="Azure VHD, uploading Linux VHD, Ubuntu" description="Aprenda a crear y cargar un disco duro virtual de Azure (VHD) que contiene el sistema operativo Ubuntu Linux." metaCanonical="" services="virtual-machines" documentationCenter="" title="Creaci&oacute;n y carga de un disco duro virtual que contiene un sistema operativo Ubuntu Linux" authors="kathydav" solutions="" manager="timlt" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="06/05/2014" ms.author="kathydav, szarkos" />

# Preparación de una máquina virtual Ubuntu para Azure

## Requisitos previos

En este artículo se supone que ya ha instalado un sistema operativo Ubuntu Linux en un disco duro virtual. Existen varias herramientas para crear archivos .vhd; por ejemplo, una solución de virtualización como Hyper-V. Para obtener instrucciones al respecto, consulte [Instalar el rol Hyper-V y configurar una máquina virtual][Instalar el rol Hyper-V y configurar una máquina virtual].

**Notas de instalación de Ubuntu**

-   el reciente formato VHDX no se admite en Azure. Puede convertir el disco al formato VHD con el Administrador de Hyper-V o el cmdlet Convert-VHD.

-   Al instalar el sistema Linux se recomienda utilizar las particiones estándar en lugar de un LVM (que a menudo viene de forma predeterminada en muchas instalaciones). De este modo se impedirá que el nombre del LVM entre en conflicto con las máquinas virtuales clonadas, especialmente si en algún momento hace falta adjuntar un disco de SO a otra máquina virtual para solucionar problemas. LVM o [RAID][RAID] se pueden utilizar en discos de datos si así se prefiere.

-   No cree una partición de intercambio en el disco del SO. El agente de Linux se puede configurar para crear un archivo de intercambio en el disco de recursos temporal. Puede encontrar más información al respecto en los pasos que vienen a continuación.

-   El tamaño de todos los archivos VHD debe ser múltiplo de 1 MB.

## <span id="ubuntu"></span> </a>Ubuntu 12.04+

1.  Seleccione la máquina virtual en el panel central del Administrador de Hyper-V.

2.  Haga clic en **Connect** para abrir la ventana de la máquina virtual.

3.  Sustituya los repositorios actuales de la imagen para utilizar los repositorios de Azure de Ubuntu. Los pasos varían ligeramente en función de la versión de Ubuntu.

    Antes de modificar /etc/apt/sources.list, se recomienda que realice una copia de seguridad.

        # sudo cp /etc/apt/sources.list /etc/apt/sources.list.bak

    Ubuntu 12,04:

        # sudo sed -i "s/[a-z][a-z].archive.ubuntu.com/azure.archive.ubuntu.com/g" /etc/apt/sources.list
        # sudo apt-add-repository 'http://archive.canonical.com/ubuntu precise-backports main'
        # sudo apt-get update

    Ubuntu 12.10:

        # sudo sed -i "s/[a-z][a-z].archive.ubuntu.com/azure.archive.ubuntu.com/g" /etc/apt/sources.list
        # sudo apt-add-repository 'http://archive.canonical.com/ubuntu quantal-backports main'
        # sudo apt-get update

    Ubuntu 14.04+:

        # sudo sed -i "s/[a-z][a-z].archive.ubuntu.com/azure.archive.ubuntu.com/g" /etc/apt/sources.list
        # sudo apt-get update

4.  Actualice el sistema operativo con el kernel más reciente ejecutando los comandos siguientes:

    Ubuntu 12,04:

        # sudo apt-get update
        # sudo apt-get install hv-kvp-daemon-init linux-backports-modules-hv-precise-virtual
        (recommended) sudo apt-get dist-upgrade

        # sudo reboot

    Ubuntu 12.10:

        # sudo apt-get update
        # sudo apt-get install hv-kvp-daemon-init linux-backports-modules-hv-quantal-virtual
        (recommended) sudo apt-get dist-upgrade

        # sudo reboot

    Ubuntu 14.04+:

        # sudo apt-get update
        # sudo apt-get install hv-kvp-daemon-init
        (recommended) sudo apt-get dist-upgrade

        # sudo reboot

5.  (opcional) Si el sistema Ubuntu encuentra un error y se reinicia, a menudo esperará la solicitud de información del usuario del arranque de grub, impidiendo así que el sistema se inicie correctamente. Para impedir que esto ocurra, realice los pasos siguientes:

    a) Abra el archivo /etc/grub.d/00\_header.

    b) En la función **make\_timeout()**, busque **if ["\\${recordfail}" = 1 ]; then**

    c) Cambie la instrucción que se encuentra debajo de esta línea a **set timeout=5**.

    d) Ejecute 'sudo update-grub'.

6.  Modifique la línea de arranque de kernel para que Grub incluya los parámetros de kernel adicionales para Azure. Para ello, abra /etc/default/grub en un editor de texto, busque la variable llamada `GRUB_CMDLINE_LINUX_DEFAULT` (o agréguela si fuera necesario) y edítela para que incluya los parámetros siguientes:

        GRUB_CMDLINE_LINUX_DEFAULT="console=ttyS0 earlyprintk=ttyS0 rootdelay=300"

    Guarde el archivo, ciérrelo y, a continuación, ejecute "`sudo update-grub`". Así se asegurará de que todos los mensajes de la consola se envían al primer puerto serie, lo que puede ayudar al soporte técnico de Azure con los problemas de depuración de errores.

7.  Asegúrese de que el servidor SSH se haya instalado y configurado para iniciarse en el tiempo de arranque. Este es normalmente el valor predeterminado.

8.  Instale el Agente de Linux de Azure:

        # sudo apt-get update
        # sudo apt-get install walinuxagent

    Tenga en cuenta que al instalar el paquete `walinuxagent` se eliminan los paquetes `NetworkManager` y `NetworkManager-gnome`, si están instalados.

9.  Ejecute los comandos siguientes para desaprovisionar la máquina virtual y prepararla para aprovisionarse en Azure:

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

10. Haga clic en **Acción \> Apagar** en el Administrador de Hyper-V. El VHD de Linux ya está listo para cargarse en Azure.

  [Instalar el rol Hyper-V y configurar una máquina virtual]: http://technet.microsoft.com/library/hh846766.aspx
  [RAID]: ../virtual-machines-linux-configure-raid
