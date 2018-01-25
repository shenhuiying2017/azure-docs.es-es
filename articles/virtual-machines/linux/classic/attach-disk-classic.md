---
title: "Conexión de un disco a una VM de Linux en Azure | Microsoft Docs"
description: "Aprenda a conectar un disco de datos a una máquina virtual Linux mediante el modelo de implementación clásica y a inicializar el disco para que esté preparado para su uso."
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-service-management
ROBOTS: NOINDEX
ms.assetid: 4901384d-2a6f-4f46-bba0-337a348b7f87
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/09/2017
ms.author: iainfou
ms.openlocfilehash: b47451bb8e53154760b893e452649f15ffdff7f9
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/19/2018
---
# <a name="how-to-attach-a-data-disk-to-a-linux-virtual-machine"></a>Acoplamiento de un disco de datos a una máquina virtual Linux
> [!IMPORTANT] 
> Azure tiene dos modelos de implementación diferentes para crear recursos y trabajar con ellos: [Resource Manager y el clásico](../../../resource-manager-deployment-model.md). En este artículo se trata el modelo de implementación clásico. Microsoft recomienda que las implementaciones más recientes usen el modelo de Resource Manager. Consulte cómo [conectar un disco de datos mediante el modelo de implementación de Resource Manager](../add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Puede acoplar tanto discos vacíos como discos que contengan datos a las máquinas virtuales de Azure. Ambos tipos de discos son archivos .vhd que residen en una cuenta de almacenamiento de Azure. Como sucede al agregar cualquier disco a una máquina Linux, después de acoplarlo, debe inicializarlo y formatearlo para que esté listo para su uso. En este artículo se detalla cómo acoplar discos vacíos y discos que ya contengan datos a las máquinas virtuales, así como la forma de inicializar y formatear un disco nuevo.

> [!NOTE]
> Es recomendable utilizar uno o varios discos independientes para almacenar los datos de una máquina virtual. Al crear una máquina virtual de Azure, esta cuenta con un disco para el sistema operativo y un disco temporal. **No utilice el disco temporal para almacenar datos permanentes.** Como señala su nombre, esta ofrece únicamente almacenamiento temporal. No ofrece redundancia o copias de seguridad porque no reside en el almacenamiento de Azure.
> El Agente de Linux de Azure normalmente administra el disco temporal, y este se monta automáticamente en **/mnt/resource** (o **/mnt** en las imágenes de Ubuntu). Por otro lado, el kernel de Linux podría denominar al disco de datos de forma similar a `/dev/sdc`, y los usuarios necesitan crear particiones, dar formato y montar ese recurso. Consulte la [Guía de usuario del Agente de Linux de Azure][Agent] para obtener más información.
> 
> 

[!INCLUDE [howto-attach-disk-windows-linux](../../../../includes/howto-attach-disk-linux.md)]

## <a name="initialize-a-new-data-disk-in-linux"></a>un nuevo disco de datos en Linux
1. SSH en la máquina virtual. Para obtener más información, consulte [Inicio de sesión en una máquina virtual Linux][Logon].
2. A continuación deberá buscar el identificador de dispositivo para inicializar el disco de datos. Existen dos formas de hacerlo:
   
    (a) Busque con grep dispositivos SCSI en los registros, como se muestra en el siguiente comando:
   
    ```bash
    sudo grep SCSI /var/log/messages
    ```
   
    Para las distribuciones de Ubuntu recientes, puede que necesite usar `sudo grep SCSI /var/log/syslog` porque el inicio de sesión en `/var/log/messages` puede deshabilitarse de forma predeterminada.
   
    Puede buscar el identificador del último disco de datos conectado en los mensajes que se muestran.
   
    ![Obtener mensajes de disco](./media/attach-disk/scsidisklog.png)
   
    OR
   
    b) Use el comando `lsscsi` para conocer el identificador del dispositivo. `lsscsi` se puede instalar mediante `yum install lsscsi` (en distribuciones basadas en Red Hat) o `apt-get install lsscsi` (en distribuciones basadas en Debian). Puede encontrar el disco que busca por su *lun* o **número de unidad lógica**. Por ejemplo, el *lun* de los discos que conectó puede verse fácilmente en `azure vm disk list <virtual-machine>`, de la manera siguiente:

    ```azurecli
    azure vm disk list myVM
    ```

    La salida será similar a la siguiente:

    ```azurecli
    info:    Executing command vm disk list
    + Fetching disk images
    + Getting virtual machines
    + Getting VM disks
    data:    Lun  Size(GB)  Blob-Name                         OS
    data:    ---  --------  --------------------------------  -----
    data:         30        myVM-2645b8030676c8f8.vhd  Linux
    data:    0    100       myVM-76f7ee1ef0f6dddc.vhd
    info:    vm disk list command OK
    ```
   
    Compare estos datos con la salida de `lsscsi` para la misma máquina virtual de ejemplo:
   
    ```bash
    [1:0:0:0]    cd/dvd  Msft     Virtual CD/ROM   1.0   /dev/sr0
    [2:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sda
    [3:0:1:0]    disk    Msft     Virtual Disk     1.0   /dev/sdb
    [5:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sdc
    ```
   
    El último número de la tupla en cada fila es el *lun*. Vea `man lsscsi` para obtener más información.
3. En el símbolo del sistema, escriba el siguiente comando para crear el dispositivo:
   
    ```bash
    sudo fdisk /dev/sdc
    ```

4. Cuando se le pida, escriba **n** para crear una partición.

    ![Crear dispositivo](./media/attach-disk/fdisknewpartition.png)

5. Cuando se le solicite, escriba **p** para que la partición sea la partición principal. Escriba **1** para que sea la primera partición y, a continuación, escriba "enter" para aceptar el valor predeterminado para el cilindro. En algunos sistemas, puede mostrar los valores predeterminados del primer sector y el último sector, en lugar del cilindro. Puede aceptar estos valores predeterminados.

    ![Crear partición](./media/attach-disk/fdisknewpartdetails.png)


6. Escriba **p** para ver los detalles del disco en el que se va a crear la partición.

    ![Enumerar la información del disco](./media/attach-disk/fdiskpartitiondetails.png)


7. Escriba **w** para escribir la configuración del disco.

    ![Escribir los cambios del disco](./media/attach-disk/fdiskwritedisk.png)

8. Ahora puede crear el sistema de archivos en la nueva partición. Anexe el número de partición al id. de dispositivo (en el ejemplo siguiente, `/dev/sdc1`). En el ejemplo siguiente se crea una partición de ext4 en /dev/sdc1:
   
    ```bash
    sudo mkfs -t ext4 /dev/sdc1
    ```
   
    ![Crear sistema de archivos](./media/attach-disk/mkfsext4.png)
   
   > [!NOTE]
   > Los sistemas SUSE Linux Enterprise 11 únicamente admiten acceso de solo lectura a sistemas de archivos ext4. Para estos sistemas, es recomendable dar formato al nuevo sistema de archivos como ext3 en vez de ext4.

9. Cree un directorio para montar el nuevo sistema de archivos como se indica a continuación:
   
    ```bash
    sudo mkdir /datadrive
    ```

10. Por último, se puede montar la unidad como se indica a continuación:
   
    ```bash
    sudo mount /dev/sdc1 /datadrive
    ```
   
    El disco de datos está ahora listo para usarse como **/datadrive**.
   
    ![Crear el directorio y montar el disco](./media/attach-disk/mkdirandmount.png)

11. Agregue la nueva unidad a /etc/fstab:
   
    Para asegurarse de que la unidad se vuelve a montar automáticamente después de reiniciar, debe agregarse al archivo /etc/fstab. Además, se recomienda encarecidamente que se use el UUID (identificador único global) en /etc/fstab para hacer referencia a la unidad en lugar de solo el nombre del dispositivo (es decir, /dev/sdc1). El uso de UUID evita que el disco incorrecto se monte en una ubicación determinada si el sistema operativo detecta un error de disco durante el inicio y el resto de discos de datos se asignan a esos id. de dispositivo. Para buscar el UUID de la unidad nueva, puede usar la utilidad **blkid**:
   
    ```bash
    sudo -i blkid
    ```
   
    La salida es similar a la siguiente:
   
    ```bash
    /dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"
    /dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"
    /dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"
    ```

    > [!NOTE]
    > La edición incorrecta del archivo **/etc/fstab** puede tener como resultado un sistema que no se pueda arrancar. Si no está seguro, consulte la documentación de distribución para obtener información sobre cómo editar correctamente ese archivo. También se recomienda realizar una copia de seguridad del archivo /etc/fstab antes de editarlo.

    Después, abra el archivo **/etc/fstab** en un editor de texto:

    ```bash
    sudo vi /etc/fstab
    ```

    En este ejemplo, usamos el valor de UUID para el nuevo dispositivo **/dev/sdc1** que se ha creado en los pasos anteriores y el punto de montaje **/datadrive**. Agregue la siguiente línea al final del archivo **/etc/fstab** :

    ```sh
    UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,nofail   1   2
    ```

    O bien, en sistemas basados en SUSE Linux, es posible que tenga que utilizar un formato ligeramente diferente:

    ```sh
    /dev/disk/by-uuid/33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext3   defaults,nofail   1   2
    ```

    > [!NOTE]
    > La opción `nofail` garantiza que la máquina virtual se inicia incluso si el sistema de archivos está dañado o el disco no existe al arrancar. Sin esta opción, puede encontrarse con el comportamiento que se describe en [Cannot SSH to Linux VM due to FSTAB errors](https://blogs.msdn.microsoft.com/linuxonazure/2016/07/21/cannot-ssh-to-linux-vm-after-adding-data-disk-to-etcfstab-and-rebooting/) (No se puede conectar mediante SSH a una máquina virtual Linux debido a errores de FSTAB).

    Ahora puede probar que el sistema de archivos está correctamente montado desmontando y volviendo a montar el sistema de archivos; es decir, usando el punto de montaje de ejemplo `/datadrive` creado en los pasos anteriores:

    ```bash
    sudo umount /datadrive
    sudo mount /datadrive
    ```

    Si el comando `mount` genera un error, compruebe la sintaxis correcta del archivo /etc/fstab. Si se crean particiones o unidades de datos adicionales, especifíquelas también en /etc/fstab por separado.

    Haga se pueda escribir en la unidad mediante este comando:

    ```bash
    sudo chmod go+w /datadrive
    ```

    > [!NOTE]
    > Posteriormente, la eliminación de un disco de datos sin editar fstab podría provocar un error en el inicio de la máquina virtual. Si ocurre habitualmente, la mayoría de distribuciones proporcionan las opciones de fstab `nofail` o `nobootwait` que permite que el sistema se inicie incluso si el disco no se monta al arrancar. Consulte la documentación de su distribución para obtener más información sobre estos parámetros.

### <a name="trimunmap-support-for-linux-in-azure"></a>Compatibilidad de TRIM/UNMAP con Linux en Azure
Algunos kernels de Linux admiten operaciones TRIM/UNMAP para descartar bloques no usados del disco. Estas operaciones son especialmente útiles en el almacenamiento estándar para informar a Azure de que las páginas eliminadas ya no son válidas y se pueden descartar. El descarte de páginas puede suponer un ahorro de dinero si crea archivos grandes y, a continuación, los elimina.

Hay dos maneras de habilitar la compatibilidad con TRIM en su máquina virtual Linux. Como es habitual, consulte la documentación de distribución para ver el enfoque recomendado:

* Use la opción de montaje `discard` en `/etc/fstab`, por ejemplo:

    ```sh
    UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,discard   1   2
    ```

* En algunos casos, la opción `discard` podría tener afectar al rendimiento. Como alternativa, puede ejecutar el comando `fstrim` manualmente desde la línea de comandos o agregarlo a su crontab para ejecutar con regularidad:
  
    **Ubuntu**
  
    ```bash
    sudo apt-get install util-linux
    sudo fstrim /datadrive
    ```
  
    **RHEL/CentOS**
  
    ```bash
    sudo yum install util-linux
    sudo fstrim /datadrive
    ```

## <a name="troubleshooting"></a>solución de problemas
[!INCLUDE [virtual-machines-linux-lunzero](../../../../includes/virtual-machines-linux-lunzero.md)]

## <a name="next-steps"></a>Pasos siguientes
Puede leer más sobre el uso de la máquina virtual con Linux en los siguientes artículos:

* [Inicio de sesión en una máquina virtual con Linux][Logon]
* [Desconexión de un disco de una máquina virtual de Linux](detach-disk-classic.md)
* [Comandos CLI de Azure en modo de Administración de servicios de Azure (asm)](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2)
* [Configuración de RAID en una máquina virtual Linux en Azure](../configure-raid.md)
* [Configuración del LVM en una máquina virtual Linux en Azure](../configure-lvm.md)

<!--Link references-->
[Agent]:../agent-user-guide.md
[Logon]:../mac-create-ssh-keys.md
