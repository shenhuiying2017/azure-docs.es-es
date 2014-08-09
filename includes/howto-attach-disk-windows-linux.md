
# Acoplamiento de un disco de datos a una máquina virtual

* [Conceptos](#concepts)
* [Acoplamiento de un disco existente](#attachexisting)
* [Acoplamiento de un disco vacío](#attachempty)
* [Inicialización de un nuevo disco de datos en Windows Server 2008
  R2](#initializeinWS)
* [Inicialización de un nuevo disco de datos en
  Linux](#initializeinlinux)

## <a id="concepts" /></a>Conceptos

Puede acoplar un disco de datos a una máquina virtual para almacenar datos de la aplicación. Un disco de datos es un disco duro virtual (VHD) que puede crear localmente con su propio equipo o en la nube con Azure. También es posible administrar los discos de datos también en la máquina virtual de la misma forma que lo hace en un servidor de su oficina.

Puede usar el Portal de administración para cargar y conectar un disco de datos que contenga datos en la máquina virtual. Además, es posible agregar un disco vacío desde la misma cuenta de almacenamiento que usa la máquina virtual. En este artículo se describen esos procesos. Para acoplar un disco vacío ubicado en una cuenta de almacenamiento diferente, use el cmdlet [Add-AzureDataDisk][1] que se encuentra disponible en el módulo de Azure PowerShell. Para descargar el módulo, consulte la página [Descargas][2].

La máquina virtual no se detiene para agregar el disco. El número de discos que puede acoplar a una máquina virtual depende del tamaño de esta. Para obtener información sobre la máquina virtual y los tamaños de disco, consulte [Tamaños de máquina virtual para Azure][3].

> \[WACOM.NOTE\] El almacenamiento de Azure es compatible con blobs de hasta 1 TB, que alojan un VHD con un tamaño virtual máximo de 999 GB. Sin embargo, si usa Hyper-V para crear un nuevo VHD, el tamaño que especifique representa el tamaño virtual. Para usar VHD en Azure, especifique un tamaño que no sea superior a 999 GB.

**Disco de datos frente a disco de recursos**  
 Los discos de datos residen en el Almacenamiento de Azure y se pueden usar para un almacenamiento continuo de archivos y datos de aplicaciones.

Las máquinas virtuales también disponen de un *disco de recursos* local y temporal conectado. Debido a que los datos de un disco de recursos podrían no resistir los diversos reinicios, muchas veces los usan aplicaciones y procesos que se ejecutan en la máquina virtual para un almacenamiento de datos transitorio y temporal. También se usan para almacenar archivos de intercambio y de paginación para el sistema operativo.

En Windows, el disco de recursos se etiqueta como unidad **D:**. En Linux, el disco de recursos se administra generalmente mediante el Agente de Linux de Azure y se monta automáticamente en **/mnt/resource** (o **/mnt** en las imágenes de Ubuntu). Consulte la [Guía de usuario del
Agente de Linux de Azure][4] para obtener más información.

Para obtener más información sobre el uso de discos de datos, consulte esta información sobre [Administrar discos e imágenes][5].

## <a id="attachexisting" /></a>Acoplamiento de un disco existente

1.  Si no lo ha hecho todavía, inicie sesión en el [Portal de administración de Azure][6].

2.  Haga clic en **Máquinas virtuales** y, a continuación, seleccione la máquina virtual en la que desee conectar el disco.

3.  En la barra de comandos, haga clic en **Attach** y, a continuación, seleccione **Attach Disk**.
    
    ![Acoplar disco de
    datos](./media/howto-attach-disk-window-linux/AttachExistingDiskWindows.png)
    
    Aparecerá el cuadro de diálogo **Attach Disk**.
    
    ![Especificar la informaci&oacute;n del disco de
    datos](./media/howto-attach-disk-window-linux/AttachExistingDisk.png)

4.  Seleccione el disco de datos que desee acoplar a la máquina virtual. 
5.  Haga clic en la marca de verificación para acoplar el disco de datos a la máquina virtual.

	El disco de datos aparecerá ahora en el panel de la máquina virtual.
    
    ![Disco de datos conectado
    correctamente](./media/howto-attach-disk-window-linux/AttachSuccess.png)

## <a id="attachempty" /></a>Acoplamiento de un disco vacío

Una vez que haya creado y cargado un archivo .vhd para usarlo como disco vacío, puede conectarlo a una máquina virtual. Use el cmdlet
[Add-AzureVhd][7] para cargar el archivo .vhd a la cuenta de almacenamiento.

1.  Haga clic en **Máquinas virtuales** y, a continuación, seleccione la máquina virtual en la que desee conectar el disco de datos.

2.  En la barra de comandos, haga clic en **Attach** y, a continuación, seleccione **Attach Empty Disk**.
    
    ![Acoplar un disco vac&iacute;o](./media/howto-attach-disk-window-linux/AttachDiskWindows.png)
    
    Aparece el cuadro de diálogo **Attach Empty Disk**.
    
    ![Conectar un nuevo disco vac&iacute;o](./media/howto-attach-disk-window-linux/AttachNewDiskWindows.png)

3.  En **File Name**, acepte el nombre que se genera automáticamente o especifique el nombre que desee usar para el archivo VHD que se ha almacenado. El disco de datos que se crea a partir del archivo VHD siempre usará el nombre generado automáticamente.

4.  En **Size**, especifique el tamaño del disco de datos.

5.  Haga clic en la casilla de verificación para conectar un disco de datos vacío.
    
    El disco de datos aparecerá ahora en el panel de la máquina virtual.
    
    ![Disco de datos vac&iacute;o conectado correctamente](./media/howto-attach-disk-window-linux/AttachEmptySuccess.png)

> \[WACOM.NOTE\] Después de conectar un disco de datos, tendrá que iniciar sesión en la máquina virtual e inicializar el disco para que la máquina virtual pueda usar el disco para el almacenamiento.

## <a id="initializeinWS" /></a>Inicialización de un nuevo disco de datos en Windows Server

1.  Siga los pasos que aparecen en [Inicio de sesión en una máquina virtual con Windows Server](../virtual-machines-log-on-windows-server/) para conectarse a una máquina virtual.

2.  Una vez que haya iniciado sesión, abra **Server Manager**, en el panel izquierdo, expanda **Almacenamiento** y, a continuación, haga clic en **Disk Management**.
    
    ![Abrir Administrador de servidores](./media/howto-attach-disk-window-linux/ServerManager.png)

3.  Haga clic con el botón secundario en **Disk 2** y, a continuación, haga clic en **Initialize Disk**.
    
    ![Inicializar el disco](./media/howto-attach-disk-window-linux/InitializeDisk.png)

4.  Haga clic en **OK** para comenzar el proceso de inicialización.

5.  Haga clic con el botón secundario en el área de asignación de espacio para el Disco 2, haga clic en **New Simple Volume** y, a continuación, finalice el asistente con los valores predeterminados.
    
    ![Inicializar el volumen](./media/howto-attach-disk-window-linux/InitializeDiskVolume.png)
    
    El disco está ahora en línea y listo para usarse con una nueva letra de unidad.
    
    ![Volumen inicializado correctamente](./media/howto-attach-disk-window-linux/InitializeSuccess.png)

## <a id="initializeinlinux" /></a>Inicialización de un nuevo disco de datos en Linux

1.  Siga los pasos que aparecen en [Inicio de sesión en una máquina virtual con Linux](../virtual-machines-linux-how-to-log-on/) para conectarse a una máquina virtual.

2.  En la ventana SSH, escriba el siguiente comando y, a continuación, especifique la contraseña para la cuenta que creó para administrar la máquina virtual:
    
	`sudo grep SCSI /var/log/messages`
    
    Puede buscar el identificador del último disco de datos conectado en los mensajes que se muestran.
    
    ![Obtener mensajes de disco](./media/howto-attach-disk-window-linux/DiskMessages.png)

3.  En la ventana SSH, escriba el siguiente comando para crear un nuevo dispositivo y, a continuación, especifique la contraseña de la cuenta:
    
    `sudo fdisk /dev/sdc`
    
    > \[WACOM.NOTE\] En este ejemplo, es posible que tenga que utilizar
    > `sudo -i` en algunas distribuciones si /sbin o /usr/sbin no se
    > encuentran en su `$PATH`.

4.  Escriba **n** para crear una nueva partición.
    
    ![Crear un dispositivo nuevo](./media/howto-attach-disk-window-linux/DiskPartition.png)

5.  Escriba **p** para que la partición sea la partición principal, escriba **1** para que sea la primera partición y, a continuación, escriba "enter" para aceptar el valor predeterminado para el cilindro.
    
    ![Crear partici&oacute;n](./media/howto-attach-disk-window-linux/DiskCylinder.png)

6.  Escriba **p** para ver los detalles del disco en el que se va a crear la partición.
    
    ![Enumerar la informaci&oacute;n del disco](./media/howto-attach-disk-window-linux/DiskInfo.png)

7.  Escriba **w** para escribir la configuración del disco.
    
    ![Escribir los cambios del disco](./media/howto-attach-disk-window-linux/DiskWrite.png)

8.  Debe crear el sistema de archivos en la partición nueva. Como ejemplo, escriba el siguiente comando para crear el sistema de archivos y, a continuación, especifique la contraseña de la cuenta:
    
    `sudo mkfs -t ext4 /dev/sdc1`
    
    ![Crear sistema de
    archivos](./media/howto-attach-disk-window-linux/DiskFileSystem.png)
    
    > \[WACOM.NOTE\] Tenga en cuenta que los sistemas SUSE Linux Enterprise 11 ofrecen únicamente acceso de solo lectura a sistemas de archivos ext4. Para estos sistemas, es recomendable dar formato al nuevo sistema de archivos como ext3 en vez de ext4.

9.  A continuación, debe tener un directorio disponible para montar el nuevo sistema de archivos. Como ejemplo, escriba el siguiente comando para crear un nuevo directorio para el montaje de la unidad y, a continuación, especifique la contraseña de la cuenta:
    
    `sudo mkdir /datadrive`

10. Escriba el siguiente comando para montar la unidad:
    
    `sudo mount /dev/sdc1 /datadrive`
    
    El disco de datos está ahora listo para usarse como **/datadrive**.

11. Agregue la nueva unidad a /etc/fstab:
    
    Para asegurarse de que la unidad se vuelve a montar automáticamente después de reiniciar, debe agregarse al archivo /etc/fstab. Además, se recomienda encarecidamente que se use el UUID (identificador único global) en /etc/fstab para hacer referencia a la unidad en lugar de solo el nombre del dispositivo (es decir, /dev/sdc1). Para buscar el UUID de la unidad nueva, puede usar la utilidad **blkid**:
    
        `sudo -i blkid`
    
    El resultado será similar al siguiente:
    
        `/dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"`
        `/dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"`
        `/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"`
    
    > \[WACOM.NOTE\] Es posible que blkid no requiera acceso sudo en todos los casos. Sin embargo, puede que sea más sencillo ejecutar `sudo -i` en algunas distribuciones si /sbin o /usr/sbin no se encuentran en su `$PATH`.
    
    **Precaución:** la edición incorrecta del archivo /etc/fstab puede tener como resultado un sistema que no se pueda arrancar. Si no está seguro, consulte la documentación de distribución para obtener información sobre cómo editar correctamente ese archivo. También se recomienda realizar una copia de seguridad del archivo /etc/fstab antes de editarlo.
    
    Use un editor de texto para especificar la información sobre el nuevo sistema de archivos al final del archivo /etc/fstab. En este ejemplo usaremos el valor de UUID para el nuevo dispositivo **/dev/sdc1** que se creó en los pasos anteriores y el punto de montaje **/datadrive**:
    
        `UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults   1   2`
    
    O bien, en sistemas basados en SUSE Linux, es posible que tenga que utilizar un formato diferente:
    
        `/dev/disk/by-uuid/33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /   ext3   defaults   1   2`
    
    Si se crean particiones o unidades de datos adicionales, tendrá que especificarlas también en /etc/fstab por separado.
    
    Ahora puede probar que el sistema de archivos está correctamente montado con solo desmontar y volver a montar el sistema de archivos, es decir, usando el punto de montaje de ejemplo `/datadrive` que se creó en los pasos anteriores:
    
        `sudo umount /datadrive`
        `sudo mount /datadrive`
    
    Si el segundo comando genera un error, compruebe la sintaxis correcta del archivo /etc/fstab.
    
    > \[WACOM.NOTE\] Posteriormente, la eliminación de un disco de datos sin editar fstab podría provocar un error en el inicio de la máquina virtual. Si ocurre habitualmente, la mayoría de distribuciones proporcionan las opciones de fstab `nofail` o `nobootwait` que permitirán que el sistema se inicie incluso si el disco no está presente. Consulte la documentación de su distribución para obtener más información sobre estos parámetros.



[1]: http://go.microsoft.com/fwlink/p/?LinkId=391661
[2]: http://www.windowsazure.com/es-es/downloads/
[3]: http://go.microsoft.com/FWLink/p/?LinkID=294683
[4]: http://www.windowsazure.com/es-es/manage/linux/how-to-guides/linux-agent-guide/
[5]: http://msdn.microsoft.com/es-es/library/windowsazure/jj672979.aspx
[6]: http://manage.windowsazure.com
[7]: http://go.microsoft.com/FWLink/p/?LinkID=391684