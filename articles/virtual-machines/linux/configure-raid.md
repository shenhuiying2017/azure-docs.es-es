---
title: "Configuración del software RAID en una máquina virtual Linux | Microsoft Docs"
description: Aprenda a utilizar mdadm para configurar RAID en Linux en Azure.
services: virtual-machines-linux
documentationcenter: na
author: rickstercdn
manager: timlt
editor: tysonn
tag: azure-service-management,azure-resource-manager
ms.assetid: f3cb2786-bda6-4d2c-9aaf-2db80f490feb
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/02/2017
ms.author: rclaus
ms.openlocfilehash: 12f540a700fbf85e579e8aadc9f6def039299ff7
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="configure-software-raid-on-linux"></a>Configuración del software RAID en Linux
Es un escenario habitual usar el software RAID en máquinas virtuales con Linux en Azure para presentar varios discos de datos conectados como un único dispositivo RAID. Se puede utilizar normalmente para aumentar el rendimiento y permitir una capacidad de proceso mejorada en comparación con el uso de un solo disco.

## <a name="attaching-data-disks"></a>Conexión de discos de datos
Se necesitan dos o más discos de datos vacíos para configurar un dispositivo RAID.  La razón principal para crear un dispositivo RAID es mejorar el rendimiento de la E/S de disco.  Según sus requisitos de E/S, puede decidir asociar discos que estén almacenados en nuestro almacenamiento estándar con hasta 500 E/S por segundo por disco o Almacenamiento Premium con hasta 5000 E/S por segundo por disco. En este artículo no entramaremos en detalles sobre cómo asociar discos de datos a una máquina virtual Linux.  Consulte el artículo de Microsoft Azure sobre la [conexión de un disco](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) para obtener instrucciones detalladas sobre cómo conectar un disco de datos vacío a una máquina virtual Linux en Azure.

## <a name="install-the-mdadm-utility"></a>Instalación de la utilidad mdadm
* **Ubuntu**
```bash
sudo apt-get update
sudo apt-get install mdadm
```

* **CentOS y Oracle Linux**
```bash
sudo yum install mdadm
```

* **SLES y openSUSE**
```bash  
zypper install mdadm
```

## <a name="create-the-disk-partitions"></a>Creación de las particiones de disco
En este ejemplo, vamos a crear una única partición de disco en /dev/sdc. Por tanto, la partición de disco nueva se llamará /dev/sdc1.

1. Inicie `fdisk` para empezar a crear particiones.

    ```bash
    sudo fdisk /dev/sdc
    Device contains neither a valid DOS partition table, nor Sun, SGI or OSF disklabel
    Building a new DOS disklabel with disk identifier 0xa34cb70c.
    Changes will remain in memory only, until you decide to write them.
    After that, of course, the previous content won't be recoverable.

    WARNING: DOS-compatible mode is deprecated. It's strongly recommended to
                    switch off the mode (command 'c') and change display units to
                    sectors (command 'u').
    ```

2. Presione ' n ' en el símbolo del sistema para crear un  **n** partición ew:

    ```bash
    Command (m for help): n
    ```

3. A continuación, presione "p" para crear una partición **p**rincipal:

    ```bash 
    Command action
            e   extended
            p   primary partition (1-4)
    ```

4. Presione "1" para seleccionar el número de la partición 1:

    ```bash
    Partition number (1-4): 1
    ```

5. Seleccione el punto de partida de la partición nueva o presione `<enter>` para aceptar el valor predeterminado para colocar la partición al principio del espacio disponible en la unidad:

    ```bash   
    First cylinder (1-1305, default 1):
    Using default value 1
    ```

6. Seleccione el tamaño de la partición; por ejemplo, escriba "+10G" para crear una partición de 10 gigabytes. O simplemente presione `<enter>` para crear una única partición que extienda la unidad completa:

    ```bash   
    Last cylinder, +cylinders or +size{K,M,G} (1-1305, default 1305): 
    Using default value 1305
    ```

7. A continuación, cambie el identificador y el **t**ipo de la partición del identificador predeterminado "83" (Linux) por el identificador "fd" (Linux raid auto):

    ```bash  
    Command (m for help): t
    Selected partition 1
    Hex code (type L to list codes): fd
    ```

8. Por último, escriba la tabla de particiones en la unidad y cierre fdisk:

    ```bash   
    Command (m for help): w
    The partition table has been altered!
    ```

## <a name="create-the-raid-array"></a>Creación de la matriz RAID
1. En el siguiente ejemplo, se "seccionan" (RAID nivel 0) tres particiones ubicadas en tres discos de datos independientes (sdc1, sdd1 y sde1).  Después de ejecutar este comando, se creará un nuevo dispositivo RAID llamado **/dev/md127** . Tenga en cuenta también que, si estos discos de datos formaban parte anteriormente de otra matriz RAID inactiva, puede ser necesario agregar el parámetro `--force` al comando `mdadm`:

    ```bash  
    sudo mdadm --create /dev/md127 --level 0 --raid-devices 3 \
        /dev/sdc1 /dev/sdd1 /dev/sde1
    ```

2. Cree el sistema de archivos en el nuevo dispositivo RAID.
   
    a. **CentOS, Oracle Linux, SLES 12, openSUSE y Ubuntu**

    ```bash   
    sudo mkfs -t ext4 /dev/md127
    ```
   
    b. **SLES 11**

    ```bash
    sudo mkfs -t ext3 /dev/md127
    ```
   
    c. **SLES 11**: habilitar boot.md y crear mdadm.conf

    ```bash
    sudo -i chkconfig --add boot.md
    sudo echo 'DEVICE /dev/sd*[0-9]' >> /etc/mdadm.conf
    ```
   
   > [!NOTE]
   > Es posible que sea necesario reiniciar después de realizar estos cambios en los sistemas SUSE. Este paso *no* es necesario en SLES 12.
   > 
   > 

## <a name="add-the-new-file-system-to-etcfstab"></a>Incorporación del nuevo sistema de archivos a /etc/fstab
> [!IMPORTANT]
> La edición incorrecta del archivo /etc/fstab puede tener como resultado un sistema que no se pueda arrancar. Si no está seguro, consulte la documentación de distribución para obtener información sobre cómo editar correctamente ese archivo. También se recomienda realizar una copia de seguridad del archivo /etc/fstab antes de editarlo.

1. Cree el punto de montaje deseado para el nuevo sistema de archivos, por ejemplo:

    ```bash
    sudo mkdir /data
    ```
2. Al editar /etc/fstab, el **UUID** debe usarse para hacer referencia al sistema de archivos en lugar de al nombre del dispositivo.  Use la utilidad `blkid` para determinar el UUID del nuevo sistema de archivos:

    ```bash   
    sudo /sbin/blkid
    ...........
    /dev/md127: UUID="aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee" TYPE="ext4"
    ```

3. Abra /etc/fstab en un editor de texto y agregue una entrada al nuevo sistema de archivos, por ejemplo:

    ```bash   
    UUID=aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext4  defaults  0  2
    ```
   
    O bien, en **SLES 11**:

    ```bash
    /dev/disk/by-uuid/aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext3  defaults  0  2
    ```
   
    A continuación, guarde y cierre /etc/fstab.

4. Pruebe que la entrada /etc/fstab sea correcta:

    ```bash  
    sudo mount -a
    ```

    Si este comando genera un mensaje de error, compruebe la sintaxis del archivo /etc/fstab.
   
    A continuación, ejecute el comando `mount` para garantizar el montaje del sistema de archivos:

    ```bash   
    mount
    .................
    /dev/md127 on /data type ext4 (rw)
    ```

5. (Opcional) Parámetros de arranque a prueba de errores
   
    **Configuración de fstab**
   
    Muchas distribuciones incluyen los parámetros de montaje `nobootwait` o `nofail` que pueden agregarse al archivo /etc/fstab. Estos parámetros admiten errores al montar un sistema de archivos concreto y permiten que el sistema Linux continúe iniciándose incluso aunque no pueda montar correctamente el sistema de archivos RAID. Consulte la documentación de su distribución para obtener más información sobre estos parámetros.
   
    Ejemplo (Ubuntu):

    ```bash  
    UUID=aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext4  defaults,nobootwait  0  2
    ```   

    **Parámetros de inicio de Linux**
   
    Además de los parámetros anteriores, el parámetro de kernel`bootdegraded=true`puede permitir que el sistema se inicie incluso si RAID se percibe como dañado o degradado, por ejemplo si una unidad de datos se quita accidentalmente de la máquina virtual. De manera predeterminada, esto podría resultar en un sistema no iniciable.
   
    Consulte la documentación sobre la distribución para obtener información acerca de cómo editar correctamente los parámetros de kernel. Por ejemplo, en muchas distribuciones (CentOS, Oracle Linux y SLES 11), estos parámetros pueden agregarse manualmente al archivo "`/boot/grub/menu.lst`".  En Ubuntu, este parámetro puede agregarse a la variable `GRUB_CMDLINE_LINUX_DEFAULT` en "/etc/default/grub".


## <a name="trimunmap-support"></a>Compatibilidad con TRIM y UNMAP
Algunos kernels de Linux admiten operaciones TRIM/UNMAP para descartar bloques no usados del disco. Estas operaciones son especialmente útiles en el almacenamiento estándar para informar a Azure de que las páginas eliminadas ya no son válidas y se pueden descartar. El descarte de páginas puede suponer un ahorro de dinero si crea archivos grandes y, a continuación, los elimina.

> [!NOTE]
> Es posible que RAID no pueda emitir comandos de descartar si el tamaño del fragmento de la matriz se establece en un valor inferior al predeterminado (512 kB). Esto se debe a que la granularidad de UNMAP del host también es de 512 kB. Si ha modificado el tamaño del fragmento de la matriz a través del parámetro `--chunk=` de mdadm, el kernel puede pasar por alto las solicitudes de TRIM y UNMAP.

Hay dos maneras de habilitar la compatibilidad con TRIM en su máquina virtual Linux. Como es habitual, consulte la documentación de distribución para ver el enfoque recomendado:

- Use la opción de montaje `discard` en `/etc/fstab`, por ejemplo:

    ```bash
    UUID=aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext4  defaults,discard  0  2
    ```

- En algunos casos, la opción `discard` podría tener afectar al rendimiento. Como alternativa, puede ejecutar el comando `fstrim` manualmente desde la línea de comandos o agregarlo a su crontab para ejecutar con regularidad:

    **Ubuntu**

    ```bash
    # sudo apt-get install util-linux
    # sudo fstrim /data
    ```

    **RHEL/CentOS**
    ```bash
    # sudo yum install util-linux
    # sudo fstrim /data
    ```
