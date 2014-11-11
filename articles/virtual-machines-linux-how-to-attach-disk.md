<properties linkid="manage-linux-howto-attach-a-disk" urlDisplayName="Attach a disk" pageTitle="Attach a disk to a virtual machine running Linux in Azure" metaKeywords="disk VM Azure, initialize new disk Azure, initialize disk Azure Linux, attaching empty disk Azure" description="Learn how to attach a data disk to an Azure virtual machine and initialize it so it's ready for use." metaCanonical="http://www.windowsazure.com/es-es/manage/windows/how-to-guides/attach-a-disk/" services="virtual-machines" documentationCenter="" title="" authors="kathydav" solutions="" manager="timlt" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="7/29/2014" ms.author="kathydav" />

# Acoplamiento de un disco de datos a una máquina virtual Linux

Puede acoplar tanto discos vacíos como discos que contienen datos. En ambos casos, se trata realmente de archivos .vhd que residen en una cuenta de almacenamiento de Azure. También en ambos casos, una vez acoplado el disco, tendrá que inicializarlo para que esté listo para utilizarse.

> [WACOM.NOTE] Es recomendable utilizar uno o varios discos independientes para almacenar los datos de una máquina virtual. Al crear una máquina virtual de Azure, esta cuenta con un disco para el sistema operativo y un disco temporal. **No utilice el disco temporal para almacenar datos.** Como señala su nombre, esta ofrece únicamente almacenamiento temporal. No ofrece redundancia o copias de seguridad porque no reside en el almacenamiento de Azure.
> En Linux, el disco de recursos se administra generalmente mediante el Agente de Linux de Azure y se monta automáticamente en **/mnt/resource** (o **/mnt** en las imágenes de Ubuntu). Por otra parte, en Linux el kernel debe poner al disco de datos el nombre `/dev/sdc`. Si ese fuera el caso, tiene que crear particiones en ese recurso, darle formato y montarlo. Consulte la [Guía de usuario del Agente de Linux de Azure][Guía de usuario del Agente de Linux de Azure] para obtener más información.

-   [Direccionamiento del un disco vacío][Direccionamiento del un disco vacío]
-   [Direccionamiento del un disco existente][Direccionamiento del un disco existente]
-   [Direccionamiento del un nuevo disco de datos en Linux][Direccionamiento del un nuevo disco de datos en Linux]

[WACOM.INCLUDE [howto-attach-disk-windows-linux](../includes/howto-attach-disk-windows-linux.md)]

## <span id="initializeinlinux"></span></a>Direccionamiento del un nuevo disco de datos en Linux

1.  Siga los pasos que aparecen en [Inicio de sesión en una máquina virtual con Linux][Inicio de sesión en una máquina virtual con Linux] para conectarse a una máquina virtual.

2.  En la ventana SSH, escriba el siguiente comando y, a continuación, especifique la contraseña para la cuenta que creó para administrar la máquina virtual:

        # sudo grep SCSI /var/log/messages

    Puede buscar el identificador del último disco de datos conectado en los mensajes que se muestran.

    ![Obtener mensajes de disco][Obtener mensajes de disco]

3.  En la ventana SSH, escriba el siguiente comando para crear un nuevo dispositivo y, a continuación, especifique la contraseña de la cuenta:

        # sudo fdisk /dev/sdc

    > [WACOM.NOTE] En este ejemplo, es posible que tenga que utilizar `sudo -i` en algunas distribuciones si /sbin o /usr/sbin no se encuentran en su `$PATH`.

4.  Escriba **n** para crear una nueva partición.

    ![Crear un dispositivo nuevo][Crear un dispositivo nuevo]

5.  Escriba **p** para que la partición sea la partición principal, escriba **1** para que sea la primera partición y, a continuación, escriba "enter" para aceptar el valor predeterminado para el cilindro.

    ![Crear partición][Crear partición]

6.  Escriba **p** para ver los detalles del disco en el que se va a crear la partición.

    ![Enumerar la información del disco][Enumerar la información del disco]

7.  Escriba **w** para escribir la configuración del disco.

    ![Escribir los cambios del disco][Escribir los cambios del disco]

8.  Cree el sistema de archivos en la partición nueva. Como ejemplo, escriba el siguiente comando y, a continuación, especifique la contraseña de la cuenta:

        # sudo mkfs -t ext4 /dev/sdc1

    ![Crear sistema de archivos][Crear sistema de archivos]

    > [WACOM.NOTE] Tenga en cuenta que los sistemas SUSE Linux Enterprise 11 únicamente admiten acceso de solo lectura a sistemas de archivos ext4. Para estos sistemas, es recomendable dar formato al nuevo sistema de archivos como ext3 en vez de ext4.

9.  Cree un directorio para montar el nuevo sistema de archivos. Como ejemplo, escriba el siguiente comando y, a continuación, especifique la contraseña de la cuenta:

        # sudo mkdir /datadrive

10. Escriba el siguiente comando para montar la unidad:

        # sudo mount /dev/sdc1 /datadrive

    El disco de datos está ahora listo para usarse como **/datadrive**.

11. Agregue la nueva unidad a /etc/fstab:

    Para asegurarse de que la unidad se vuelve a montar automáticamente después de reiniciar, debe agregarse al archivo /etc/fstab. Además, se recomienda encarecidamente que se use el UUID (identificador único global) en /etc/fstab para hacer referencia a la unidad en lugar de solo el nombre del dispositivo (es decir, /dev/sdc1). Para buscar el UUID de la unidad nueva, puede usar la utilidad **blkid**:

        # sudo -i blkid

    El resultado será similar al siguiente:

        /dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"
        /dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"
        /dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"

    > [WACOM.NOTE] La edición incorrecta del archivo **/etc/fstab** puede tener como resultado un sistema que no se pueda arrancar. Si no está seguro, consulte la documentación de distribución para obtener información sobre cómo editar correctamente ese archivo. También se recomienda realizar una copia de seguridad del archivo /etc/fstab antes de editarlo.

    Después abra el archivo **/etc/fstab** en un editor de texto. Tenga en cuenta que /etc/fstab es un archivo del sistema, por lo que deberá utilizar `sudo` para editarlo, por ejemplo:

        # sudo vi /etc/fstab

    En este ejemplo usaremos el valor de UUID para el nuevo dispositivo **/dev/sdc1** que se creó en los pasos anteriores y el punto de montaje **/datadrive**. Agregue la siguiente línea al final del archivo **/etc/fstab**:

        UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults   1   2

    O bien, en sistemas basados en SUSE Linux, es posible que tenga que utilizar un formato diferente:

        /dev/disk/by-uuid/33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /   ext3   defaults   1   2

    Ahora puede probar que el sistema de archivos está correctamente montado con solo desmontar y volver a montar el sistema de archivos, es decir, usando el punto de montaje de ejemplo `/datadrive` que se creó en los pasos anteriores:

        # sudo umount /datadrive
        # sudo mount /datadrive

    Si el segundo comando `mount` genera un error, compruebe la sintaxis correcta del archivo /etc/fstab. Si se crean particiones o unidades de datos adicionales, tendrá que especificarlas también en /etc/fstab por separado.

    > [WACOM.NOTE] Posteriormente, la eliminación de un disco de datos sin editar fstab podría provocar un error en el inicio de la máquina virtual. Si ocurre habitualmente, la mayoría de distribuciones proporcionan las opciones de fstab `nofail` y/o `nobootwait` que permitirán que el sistema se inicie incluso si el disco no no se monta al arrancar. Consulte la documentación de su distribución para obtener más información sobre estos parámetros.

  [Guía de usuario del Agente de Linux de Azure]: http://www.windowsazure.com/es-es/manage/linux/how-to-guides/linux-agent-guide/
  [Direccionamiento del un disco vacío]: #attachempty
  [Direccionamiento del un disco existente]: #attachexisting
  [Direccionamiento del un nuevo disco de datos en Linux]: #initializeinlinux
  [howto-attach-disk-windows-linux]: ../includes/howto-attach-disk-windows-linux.md
  [Inicio de sesión en una máquina virtual con Linux]: ../virtual-machines-linux-how-to-log-on/
  [Obtener mensajes de disco]: ./media/virtual-machines-linux-how-to-attach-disk/DiskMessages.png
  [Crear un dispositivo nuevo]: ./media/virtual-machines-linux-how-to-attach-disk/DiskPartition.png
  [Crear partición]: ./media/virtual-machines-linux-how-to-attach-disk/DiskCylinder.png
  [Enumerar la información del disco]: ./media/virtual-machines-linux-how-to-attach-disk/DiskInfo.png
  [Escribir los cambios del disco]: ./media/virtual-machines-linux-how-to-attach-disk/DiskWrite.png
  [Crear sistema de archivos]: ./media/virtual-machines-linux-how-to-attach-disk/DiskFileSystem.png
