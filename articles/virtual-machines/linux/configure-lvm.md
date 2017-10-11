---
title: "Configuración de LVM en una máquina virtual que ejecuta Linux | Microsoft Docs"
description: Aprenda a configurar LVM en Linux en Azure.
services: virtual-machines-linux
documentationcenter: na
author: szarkos
manager: timlt
editor: tysonn
tag: azure-service-management,azure-resource-manager
ms.assetid: 7f533725-1484-479d-9472-6b3098d0aecc
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/02/2017
ms.author: szark
ms.openlocfilehash: 7926627aaa3f0da935131f491d927ab5cb4b35c9
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="configure-lvm-on-a-linux-vm-in-azure"></a>Configuración del LVM en una máquina virtual Linux en Azure
Este documento describe cómo configurar el administrador de volúmenes lógicos (LVM, Logical Volume Manager) en una máquina virtual de Azure. Aunque el LVM se puede configurar en cualquier disco conectado a la máquina virtual, de forma predeterminada, la mayoría de las imágenes de nube no tendrá el LVM configurado en el disco del sistema operativo. El motivo es evitar problemas con los grupos de volúmenes duplicados si el disco del sistema operativo se conecta en algún momento a otra máquina virtual de la misma distribución y tipo, es decir, durante un escenario de recuperación. Por lo tanto, se recomienda usar el LVM únicamente en los discos de datos.

## <a name="linear-vs-striped-logical-volumes"></a>Volúmenes lógicos lineales frente a seccionados
El LVM se puede utilizar para combinar diversos discos físicos en un único volumen de almacenamiento. De forma predeterminada, el LVM suele crear volúmenes lógicos lineales, lo que significa que el almacenamiento físico se concatena todo junto. En este caso, las operaciones de lectura y escritura normalmente solo se enviarán a un único disco. En cambio, también podemos crear volúmenes lógicos seccionados en los que las lecturas y escrituras se distribuyan en varios discos del grupo de volúmenes (es decir, similar a RAID0). Por motivos de rendimiento, es probable que deba seccionar los volúmenes lógicos para que las lecturas y escrituras utilicen todos los discos de datos conectados.

Este documento describe cómo combinar varios discos de datos en un único grupo de volúmenes y crear un volumen lógico seccionado. Los pasos siguientes están en cierto modo generalizados para que funcionen con la mayoría de las distribuciones. En la mayoría de los casos, las utilidades y los flujos de trabajo para la administración de LVM en Azure no se diferencian en esencia de los de otros entornos. Como de costumbre, consulte a su proveedor de Linux con el fin de obtener la documentación y los procedimientos recomendados para usar el LVM en una distribución en particular.

## <a name="attaching-data-disks"></a>Conexión de discos de datos
Normalmente, cuando utilice el LVM, conviene comenzar al menos dos discos de datos vacíos. Según sus requisitos de E/S, puede decidir asociar discos que estén almacenados en nuestro almacenamiento estándar con hasta 500 E/S por segundo por disco o Almacenamiento Premium con hasta 5000 E/S por segundo por disco. En este artículo no entramaremos en detalles sobre cómo asociar discos de datos a una máquina virtual Linux. Consulte el artículo de Microsoft Azure sobre la [conexión de un disco](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) para obtener instrucciones detalladas sobre cómo conectar un disco de datos vacío a una máquina virtual Linux en Azure.

## <a name="install-the-lvm-utilities"></a>Instalación de las utilidades del LVM
* **Ubuntu**

    ```bash  
    sudo apt-get update
    sudo apt-get install lvm2
    ```

* **RHEL, CentOS y Oracle Linux**

    ```bash   
    sudo yum install lvm2
    ```

* **12 SLES y openSUSE**

    ```bash   
    sudo zypper install lvm2
    ```

* **SLES 11**

    ```bash   
    sudo zypper install lvm2
    ```

    En SLES11 también debe editar `/etc/sysconfig/lvm` y establecer `LVM_ACTIVATED_ON_DISCOVERED` en "habilitar":

    ```sh   
    LVM_ACTIVATED_ON_DISCOVERED="enable" 
    ```

## <a name="configure-lvm"></a>Configuración del LVM
En esta guía, se presupone la conexión de tres discos de datos, a los que se llamará `/dev/sdc`, `/dev/sdd` y `/dev/sde`. Tenga en cuenta que estos podrían no presentar siempre los mismos nombres de ruta de acceso en la máquina virtual. Puede ejecutar`sudo fdisk -l`o un comando similar para ver la lista de los discos disponibles.

1. Prepare los volúmenes físicos:

    ```bash    
    sudo pvcreate /dev/sd[cde]
    Physical volume "/dev/sdc" successfully created
    Physical volume "/dev/sdd" successfully created
    Physical volume "/dev/sde" successfully created
    ```

2. Cree un grupo de volúmenes. En este ejemplo, el grupo de volúmenes se llamará `data-vg01`:

    ```bash    
    sudo vgcreate data-vg01 /dev/sd[cde]
    Volume group "data-vg01" successfully created
    ```

3. Cree los volúmenes lógicos. El comando siguiente creará un único volumen lógico llamado `data-lv01` para abarcar todo el grupo de volúmenes, pero tenga en cuenta que también se pueden crear varios volúmenes lógicos dentro del grupo de volúmenes.

    ```bash   
    sudo lvcreate --extents 100%FREE --stripes 3 --name data-lv01 data-vg01
    Logical volume "data-lv01" created.
    ```

4. Formatee el volumen lógico.

    ```bash  
    sudo mkfs -t ext4 /dev/data-vg01/data-lv01
    ```
   
   > [!NOTE]
   > En SLES11, utilice `-t ext3` en vez de ext4. SLES11 admite únicamente el acceso de solo lectura a los sistemas de archivos ext4.

## <a name="add-the-new-file-system-to-etcfstab"></a>Incorporación del nuevo sistema de archivos a /etc/fstab
> [!IMPORTANT]
> La edición incorrecta del archivo `/etc/fstab` puede tener como resultado un sistema que no se pueda arrancar. Si no está seguro, consulte la documentación de distribución para obtener información sobre cómo editar correctamente ese archivo. También se recomienda realizar una copia de seguridad del archivo `/etc/fstab` antes de editarlo.

1. Cree el punto de montaje deseado para el nuevo sistema de archivos, por ejemplo:

    ```bash  
    sudo mkdir /data
    ```

2. Busque la ruta de acceso del volumen lógico.

    ```bash    
    lvdisplay
    --- Logical volume ---
    LV Path                /dev/data-vg01/data-lv01
    ....
    ```

3. Abra `/etc/fstab` en un editor de texto y agregue una entrada al nuevo sistema de archivos, por ejemplo:

    ```bash    
    /dev/data-vg01/data-lv01  /data  ext4  defaults  0  2
    ```   
    A continuación, guarde y cierre `/etc/fstab`.

4. Compruebe que la entrada `/etc/fstab` es correcta:

    ```bash    
    sudo mount -a
    ```

    Si este comando genera un mensaje de error, compruebe la sintaxis del archivo `/etc/fstab`.
   
    A continuación, ejecute el comando `mount` para garantizar el montaje del sistema de archivos:

    ```bash    
    mount
    ......
    /dev/mapper/data--vg01-data--lv01 on /data type ext4 (rw)
    ```

5. (Opcional) Parámetros de arranque a prueba de errores en `/etc/fstab`
   
    Muchas distribuciones incluyen los parámetros de montaje `nobootwait` o `nofail` que pueden agregarse al archivo `/etc/fstab`. Estos parámetros admiten errores al montar un sistema de archivos concreto y permiten que el sistema Linux continúe iniciándose incluso aunque no pueda montar correctamente el sistema de archivos RAID. Consulte la documentación de su distribución para obtener más información sobre estos parámetros.
   
    Ejemplo (Ubuntu):

    ```bash 
    /dev/data-vg01/data-lv01  /data  ext4  defaults,nobootwait  0  2
    ```

## <a name="trimunmap-support"></a>Compatibilidad con TRIM y UNMAP
Algunos kernels de Linux admiten operaciones TRIM/UNMAP para descartar bloques no usados del disco. Estas operaciones son especialmente útiles en el almacenamiento estándar para informar a Azure de que las páginas eliminadas ya no son válidas y se pueden descartar. El descarte de páginas puede suponer un ahorro de dinero si crea archivos grandes y, a continuación, los elimina.

Hay dos maneras de habilitar la compatibilidad con TRIM en su máquina virtual Linux. Como es habitual, consulte la documentación de distribución para ver el enfoque recomendado:

- Use la opción de montaje `discard` en `/etc/fstab`, por ejemplo:

    ```bash 
    /dev/data-vg01/data-lv01  /data  ext4  defaults,discard  0  2
    ```

- En algunos casos, la opción `discard` podría tener afectar al rendimiento. Como alternativa, puede ejecutar el comando `fstrim` manualmente desde la línea de comandos o agregarlo a su crontab para ejecutar con regularidad:

    **Ubuntu**

    ```bash 
    # sudo apt-get install util-linux
    # sudo fstrim /datadrive
    ```

    **RHEL/CentOS**

    ```bash 
    # sudo yum install util-linux
    # sudo fstrim /datadrive
    ```
