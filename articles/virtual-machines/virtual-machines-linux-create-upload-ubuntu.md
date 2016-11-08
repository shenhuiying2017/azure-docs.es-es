---
title: Creación y carga de un VHD de Ubuntu Linux en Azure
description: Aprenda a crear y cargar un disco duro virtual de Azure (VHD) que contiene el sistema operativo Ubuntu Linux.
services: virtual-machines-linux
documentationcenter: ''
author: szarkos
manager: timlt
editor: tysonn
tags: azure-resource-manager,azure-service-management

ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 08/24/2016
ms.author: szark

---
# Preparación de una máquina virtual Ubuntu para Azure
[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## Imágenes oficiales de Ubuntu Cloud
Ahora Ubuntu publica VHD de Azure oficiales que se pueden descargar en [http://cloud-images.ubuntu.com/](http://cloud-images.ubuntu.com/). Si necesita crear su propia imagen de Ubuntu especializada para Azure, en lugar de usar el siguiente procedimiento manual se recomienda comenzar con estos VHD conocidos y personalizarlos según sea necesario. Las versiones más recientes de las imágenes siempre se encuentran en las siguientes ubicaciones:

* Ubuntu 12.04/precisa: [ubuntu-12.04-server-cloudimg-amd64-disk1.vhd.zip](http://cloud-images.ubuntu.com/releases/precise/release/ubuntu-12.04-server-cloudimg-amd64-disk1.vhd.zip)
* Ubuntu 14.04/Trusty: [ubuntu-14.04-server-cloudimg-amd64-disk1.vhd.zip](http://cloud-images.ubuntu.com/releases/trusty/release/ubuntu-14.04-server-cloudimg-amd64-disk1.vhd.zip)
* Ubuntu 16.04/Xenial: [ubuntu-16.04-server-cloudimg-amd64-disk1.vhd.zip](http://cloud-images.ubuntu.com/releases/xenial/release/ubuntu-16.04-server-cloudimg-amd64-disk1.vhd.zip)

## Requisitos previos
En este artículo se supone que ya ha instalado un sistema operativo Ubuntu Linux en un disco duro virtual. Existen varias herramientas para crear archivos .vhd; por ejemplo, una solución de virtualización como Hyper-V. Para obtener instrucciones, consulte [Instalación del rol de Hyper-V y configuración de una máquina Virtual](http://technet.microsoft.com/library/hh846766.aspx).

**Notas de instalación de Ubuntu**

* Consulte también [Notas generales sobre la instalación de Linux](virtual-machines-linux-create-upload-generic.md#general-linux-installation-notes) para obtener más consejos sobre la preparación de Linux para Azure.
* El formato VHDX no se admite en Azure, solo el **VHD fijo**. Puede convertir el disco al formato VHD con el Administrador de Hyper-V o el cmdlet Convert-VHD.
* Al instalar el sistema Linux se recomienda utilizar las particiones estándar en lugar de un LVM (que a menudo viene de forma predeterminada en muchas instalaciones). De este modo se impedirá que el nombre del LVM entre en conflicto con las máquinas virtuales clonadas, especialmente si en algún momento hace falta adjuntar un disco de SO a otra máquina virtual para solucionar problemas. Se pueden utilizar [LVM](virtual-machines-linux-configure-lvm.md) o [RAID](virtual-machines-linux-configure-raid.md) en discos de datos si así se prefiere.
* No cree una partición de intercambio en el disco del SO. El agente de Linux se puede configurar para crear un archivo de intercambio en el disco de recursos temporal. Puede encontrar más información al respecto en los pasos que vienen a continuación.
* El tamaño de todos los archivos VHD debe ser múltiplo de 1 MB.

## Pasos manuales
> [!NOTE]
> Antes de crear su propia imagen personalizada de Ubuntu para Azure, considere la posibilidad de utilizar las imágenes de [http://cloud-images.ubuntu.com/](http://cloud-images.ubuntu.com/) en su lugar.
> 
> 

1. Seleccione la máquina virtual en el panel central del Administrador de Hyper-V.
2. Haga clic en **Conectar** para abrir la ventana de la máquina virtual.
3. Sustituya los repositorios actuales de la imagen para utilizar los repositorios de Azure de Ubuntu. Los pasos varían ligeramente en función de la versión de Ubuntu.
   
   Antes de editar /etc/apt/sources.list, se recomienda que realice una copia de seguridad:
   
   # sudo cp /etc/apt/sources.list /etc/apt/sources.list.bak
   Ubuntu 12.04:
   
   # sudo sed -i "s/[a-z][a-z].archive.ubuntu.com/azure.archive.ubuntu.com/g" /etc/apt/sources.list
   # sudo apt-get update
   Ubuntu 14.04:
   
   # sudo sed -i "s/[a-z][a-z].archive.ubuntu.com/azure.archive.ubuntu.com/g" /etc/apt/sources.list
   # sudo apt-get update
4. Las imágenes de Azure de Ubuntu ahora siguen el kernel de *habilitación de hardware* (HWE). Actualice el sistema operativo con el kernel más reciente ejecutando los comandos siguientes:
   
    Ubuntu 12.04:
   
        # sudo apt-get update
        # sudo apt-get install linux-image-generic-lts-trusty linux-cloud-tools-generic-lts-trusty
        # sudo apt-get install hv-kvp-daemon-init
        (recommended) sudo apt-get dist-upgrade
   
        # sudo reboot
   
    Ubuntu 14.04:
   
        # sudo apt-get update
        # sudo apt-get install linux-image-virtual-lts-vivid linux-lts-vivid-tools-common
        # sudo apt-get install hv-kvp-daemon-init
        (recommended) sudo apt-get dist-upgrade
   
        # sudo reboot
5. Modifique la línea de arranque de kernel para que Grub incluya los parámetros de kernel adicionales para Azure. Para ello, abra "/etc/default/grub" en un editor de texto, busque la variable llamada`GRUB_CMDLINE_LINUX_DEFAULT` (o agréguela si fuera necesario) y edítela para que incluya los parámetros siguientes:
   
        GRUB_CMDLINE_LINUX_DEFAULT="console=tty1 console=ttyS0,115200n8 earlyprintk=ttyS0,115200 rootdelay=300"
   
    Guarde el archivo, ciérrelo y, a continuación, ejecute '`sudo update-grub`'. Así se asegurará de que todos los mensajes de la consola se envían al primer puerto serie, lo que puede ayudar al soporte técnico de Azure con los problemas de depuración de errores.
6. Asegúrese de que el servidor SSH se haya instalado y configurado para iniciarse en el tiempo de arranque. Este es normalmente el valor predeterminado.
7. Instale el Agente de Linux de Azure:
   
   # sudo apt-get update
   # sudo apt-get install walinuxagent
   Tenga en cuenta que al instalar el paquete `walinuxagent` se eliminan los paquetes `NetworkManager` y `NetworkManager-gnome`, si están instalados.
8. Ejecute los comandos siguientes para desaprovisionar la máquina virtual y prepararla para aprovisionarse en Azure:
   
   # sudo waagent -force -deprovision
   # export HISTSIZE=0
   # logout
9. Haga clic en** Acción -> Apagar** en el Administrador de Hyper-V. El VHD de Linux ya está listo para cargarse en Azure.

## Pasos siguientes
Ya está listo para usar el disco duro virtual de Ubuntu para crear nuevas máquinas virtuales de Azure. Si es la primera vez que carga el archivo .vhd en Azure, consulte los pasos 2 y 3 de [Creación y carga de un disco duro virtual que contiene el sistema operativo Linux](virtual-machines-linux-classic-create-upload-vhd.md).

## Referencias
Kernel de habilitación de hardware (HWE) de Ubuntu

* [http://blog.utlemming.org/2015/01/ubuntu-1404-azure-images-now-tracking.html](http://blog.utlemming.org/2015/01/ubuntu-1404-azure-images-now-tracking.html)
* [http://blog.utlemming.org/2015/02/1204-azure-cloud-images-now-using-hwe.html](http://blog.utlemming.org/2015/02/1204-azure-cloud-images-now-using-hwe.html)

<!---HONumber=AcomDC_0831_2016-->