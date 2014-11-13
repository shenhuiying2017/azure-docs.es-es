1.  En el [Portal de administración de Azure][Portal de administración de Azure], haga clic en **Máquinas virtuales** y, a continuación, seleccione la máquina virtual que acaba de crear (**testlinuxvm**).

2.  En la barra de comandos, haga clic en **Attach** y, a continuación, haga clic en **Attach Empty Disk**.

    Aparece el cuadro de diálogo **Attach Empty Disk**.

3.  **Virtual Machine Name**, **Storage Location** y **File Name** ya están definidos. Solo tiene que especificar el tamaño que desea utilizar para el disco. Escriba **5** en el campo **Size**.

    ![Acoplar disco vacío][Acoplar disco vacío]

    **Nota:** Todos los discos se crean a partir de un archivo .vhd en el almacenamiento de Azure. Puede proporcionar un nombre para el archivo .vhd que se agregue al almacenamiento, pero Azure genera el nombre del disco automáticamente.

4.  Haga clic en la marca de verificación para acoplar el disco de datos a la máquina virtual.

5.  Haga clic en el nombre de la máquina virtual para mostrar el panel de modo que pueda comprobar que el disco de datos se ha adjuntado correctamente a la máquina virtual. El disco que acopló aparece en la tabla **Disks**.

    Al conectar un disco de datos, este no estará listo para usar hasta que inicie sesión para completar la configuración.

## Conexión a la máquina virtual con SSH o PuTTY y finalización de la instalación

Inicie sesión en la máquina virtual para completar la configuración del disco de modo que pueda usarlo para almacenar datos.

1.  Una vez aprovisionada la máquina virtual, conéctese con SSH o PuTTY e inicie sesión como **newuser** (tal y como se describe en los pasos anteriores).

2.  En la ventana SSH o PuTTY, escriba el siguiente comando y, a continuación, la contraseña de la cuenta:

    `$ sudo grep SCSI /var/log/messages`

    Puede buscar el identificador del último disco de datos que se agregó en los mensajes que se muestran (**sdc**, en este ejemplo).

    ![GREP][GREP]

3.  En la ventana SSH o PuTTY, escriba el siguiente comando para crear una partición en el disco **/dev/sdc**:

    `$ sudo fdisk /dev/sdc`

4.  Escriba **n** para crear una nueva partición.

    ![FDISK][FDISK]

5.  Escriba **p** para que la partición sea la partición principal, escriba **1** para que la partición sea la primera partición y, a continuación, escriba Entrar para aceptar el valor predeterminado (1) para el cilindro.

    ![FDISK][1]

6.  Escriba **p** para ver los detalles del disco en el que se va a crear la partición.

    ![FDISK][2]

7.  Escriba **w** para escribir la configuración del disco.

    ![FDISK][3]

8.  Dé formato al nuevo disco con el comando **mkfs**:

    `$ sudo mkfs -t ext4 /dev/sdc1`

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

    > [WACOM.NOTE] Es posible que blkid no requiera acceso sudo en todos los casos. Sin embargo, puede que sea más sencillo ejecutar `sudo -i` en algunas distribuciones si /sbin o /usr/sbin no se encuentran en su `$PATH`.

    **Precaución:** la edición incorrecta del archivo /etc/fstab puede tener como resultado un sistema que no se pueda arrancar. Si no está seguro, consulte la documentación de distribución para obtener información sobre cómo editar correctamente ese archivo. También se recomienda realizar una copia de seguridad del archivo /etc/fstab antes de editarlo.

    Use un editor de texto para especificar la información sobre el nuevo sistema de archivos al final del archivo /etc/fstab. En este ejemplo usaremos el valor de UUID para el nuevo dispositivo **/dev/sdc1** que se creó en los pasos anteriores y el punto de montaje **/datadrive**:

        `UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults   1   2`

    Si se crean particiones o unidades de datos adicionales, tendrá que especificarlas también en /etc/fstab por separado.

    Ahora puede probar que el sistema de archivos está correctamente montado con solo desmontar y volver a montar el sistema de archivos, es decir, usando el punto de montaje de ejemplo `/datadrive` que se creó en los pasos anteriores:

        `sudo umount /datadrive`
        `sudo mount /datadrive`

    Si el segundo comando genera un error, compruebe la sintaxis correcta del archivo /etc/fstab.

    > [WACOM.NOTE] Posteriormente, la eliminación de un disco de datos sin editar fstab podría provocar un error en el inicio de la máquina virtual. Si ocurre habitualmente, la mayoría de distribuciones proporcionan las opciones de fstab `nofail` y/o `nobootwait` que permitirán que el sistema se inicie incluso si el disco no está presente. Consulte la documentación de su distribución para obtener más información sobre estos parámetros.

  [Portal de administración de Azure]: http://manage.windowsazure.com
  [Acoplar disco vacío]: ./media/attach-data-disk-centos-vm-in-portal/AttachDataDiskLinuxVM2.png
  [GREP]: ./media/attach-data-disk-centos-vm-in-portal/GrepScsiMessages.png
  [FDISK]: ./media/attach-data-disk-centos-vm-in-portal/fdisk1.png
  [1]: ./media/attach-data-disk-centos-vm-in-portal/fdisk2.png
  [2]: ./media/attach-data-disk-centos-vm-in-portal/fdisk3.png
  [3]: ./media/attach-data-disk-centos-vm-in-portal/fdisk4.png
