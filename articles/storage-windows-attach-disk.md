<properties linkid="manage-linux-howto-attach-a-disk" urlDisplayName="Attach a disk" pageTitle="Attach a disk to a virtual machine | Azure" metaKeywords="Azure Windows virtual machine, Azure attach disk, Azure initialize disk" description="Learn how to attach a data disk to an Azure virtual machine and initialize it so it's ready for use." metaCanonical="" services="virtual-machines,storage" documentationCenter="" title="How to Attach a Data Disk to a Virtual Machine" authors="kathydav" solutions="" manager="dongill" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-windows" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="kathydav"/>

# Acoplamiento de un disco de datos a una máquina virtual de Windows

Puede acoplar tanto discos vacíos como discos que contienen datos. En ambos casos, se trata realmente de archivos .vhd que residen en una cuenta de almacenamiento de Azure. También en ambos casos, una vez acoplado el disco, tendrá que inicializarlo para que esté listo para utilizarse.

> [WACOM.NOTE] Es recomendable utilizar uno o varios discos independientes para almacenar los datos de una máquina virtual. Al crear una máquina virtual de Azure, esta cuenta con un disco para el sistema operativo asignado a la unidad C y un disco temporal asignado a la unidad D. **No utilice la unidad D para almacenar datos.** Como señala su nombre, esta ofrece únicamente almacenamiento temporal. No ofrece redundancia o copias de seguridad porque no reside en el almacenamiento de Azure.

-   [Acoplamiento de un disco vacío][]
-   [Acoplamiento de un disco existente][]
-   [Inicialización de un nuevo disco de datos en Windows Server][]

[WACOM.INCLUDE [howto-attach-disk-windows-linux][]]

## <span id="initializeinWS"></span></a>Inicialización de un nuevo disco de datos en Windows Server

1.  Siga los pasos que aparecen en [Inicio de sesión en una máquina virtual con Windows Server][] para conectarse a una máquina virtual.

2.  Una vez que haya iniciado sesión, abra **Server Manager**, en el panel izquierdo, expanda **Almacenamiento** y, a continuación, haga clic en **Disk Management**.

    ![Abrir Administrador de servidores][]

3.  Haga clic con el botón secundario en **Disco 2**, haga clic en **Inicializar disco** y, a continuación, haga clic en **Aceptar**.

    ![Initialize the disk][]

4.  Haga clic con el botón secundario en el área de asignación de espacio para el Disco 2, haga clic en **New Simple Volume** y, a continuación, finalice el asistente con los valores predeterminados.

    ![Initialize the volume][]

<!-- -->

   The disk is now online and ready to use with a new drive letter.

  ![Volume successfully initialized](./media/storage-windows-attach-disk/InitializeSuccess.png)

  [Acoplamiento de un disco vacío]: #attachempty
  [Acoplamiento de un disco existente]: #attachexisting
  [Inicialización de un nuevo disco de datos en Windows Server]: #initializeinWS
  [howto-attach-disk-windows-linux]: ../includes/howto-attach-disk-windows-linux.md
  [Inicio de sesión en una máquina virtual con Windows Server]: ../virtual-machines-log-on-windows-server/
  [Abrir Administrador de servidores]: ./media/storage-windows-attach-disk/ServerManager.png
  [Initialize the disk]: ./media/storage-windows-attach-disk/InitializeDisk.png
  [Initialize the volume]: ./media/storage-windows-attach-disk/InitializeDiskVolume.png
