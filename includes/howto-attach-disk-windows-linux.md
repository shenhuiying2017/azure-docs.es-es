Para obtener más información sobre discos en máquinas virtuales de Azure, consulte [Acerca de discos de máquina virtual en Azure][].

## <span id="attachempty"></span></a>Acoplamiento de un disco vacío

El acoplamiento de un disco vacío supone el método más sencillo de agregar un disco de datos, porque Azure crea el archivo .vhd en su lugar y lo almacena en la cuenta de almacenamiento.

1.  Haga clic en **Máquinas virtuales** y, a continuación, seleccione la máquina virtual correspondiente.

2.  En la barra de comandos, haga clic en **Attach** y, a continuación, seleccione **Attach Empty Disk**.

    ![Acoplar un disco vacío][]

    Aparece el cuadro de diálogo **Attach Empty Disk**.

    ![Conectar un nuevo disco vacío][]

3.  En **Nombre de archivo**, acepte el nombre generado automáticamente o escriba un nombre descriptivo. El disco de datos que se crea a partir del archivo .vhd siempre usará el nombre generado automáticamente.

4.  En **Size**, especifique el tamaño del disco de datos.

5.  Haga clic en la casilla de verificación para conectar un disco de datos vacío.

    El disco de datos aparecerá ahora en el panel de la máquina virtual.

    ![Disco de datos vacío conectado correctamente][]

## <span id="attachexisting"></span></a>Acoplamiento de un disco existente

El acoplamiento de un disco existente requiere que disponga de un .vhd disponible en la cuenta de almacenamiento. Use el cmdlet [Add-AzureVhd][] para cargar el archivo .vhd a la cuenta de almacenamiento. Una vez que haya creado y cargado el archivo .vhd, puede acoplarlo a una máquina virtual.

1.  Haga clic en **Máquinas virtuales** y, a continuación, seleccione la máquina virtual correspondiente.

2.  En la barra de comandos, haga clic en **Attach** y, a continuación, seleccione **Attach Disk**.

    ![Acoplar disco de datos][]

    Aparecerá el cuadro de diálogo **Attach Disk**.

    ![Especificar la información del disco de datos][]

3.  Seleccione el disco de datos que desee acoplar a la máquina virtual.
4.  Haga clic en la marca de verificación para acoplar el disco de datos a la máquina virtual.

    El disco de datos aparecerá ahora en el panel de la máquina virtual.

    ![Disco de datos conectado correctamente][]

> [WACOM.NOTE]
> Después de agregar un disco de datos, tendrá que iniciar sesión en la máquina virtual e inicializar el disco para que la máquina virtual pueda usar dicho disco para el almacenamiento.

  [Acerca de discos de máquina virtual en Azure]: http://go.microsoft.com/fwlink/p/?LinkId=403697
  [Acoplar un disco vacío]: ./media/howto-attach-disk-window-linux/AttachDiskWindows.png
  [Conectar un nuevo disco vacío]: ./media/howto-attach-disk-window-linux/AttachNewDiskWindows.png
  [Disco de datos vacío conectado correctamente]: ./media/howto-attach-disk-window-linux/AttachEmptySuccess.png
  [Add-AzureVhd]: http://go.microsoft.com/FWLink/p/?LinkID=391684
  [Acoplar disco de datos]: ./media/howto-attach-disk-window-linux/AttachExistingDiskWindows.png
  [Especificar la información del disco de datos]: ./media/howto-attach-disk-window-linux/AttachExistingDisk.png
  [Disco de datos conectado correctamente]: ./media/howto-attach-disk-window-linux/AttachSuccess.png
