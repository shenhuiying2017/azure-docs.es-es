
Para obtener más información sobre los discos, consulte [Acerca de los discos de máquina virtual en Azure](http://go.microsoft.com/fwlink/p/?LinkId=403697).

##<a id="attachempty"></a>Acoplamiento de un disco vacío
El acoplamiento de un disco vacío supone el método más sencillo de agregar un disco de datos, porque Azure crea el archivo .vhd en su lugar y lo almacena en la cuenta de almacenamiento.

1. Haga clic en **Máquinas virtuales** y, a continuación, seleccione la máquina virtual correspondiente.

2. En la barra de comandos, haga clic en **Conectar** y luego en **Conectar disco vacío**.


	![Conectar un disco vacío](./media/howto-attach-disk-window-linux/AttachEmptyDisk.png)

3.	Aparecerá el cuadro de diálogo **Conectar un disco vacío**.


	![Conectar un nuevo disco vacío](./media/howto-attach-disk-window-linux/AttachEmptyDetail.png)

 
	Haga lo siguiente:

	- En **Nombre de archivo**, acepte el nombre predeterminado o escriba otro para el archivo .vhd, que se usa para el disco. El disco de datos usa un nombre generado automáticamente, incluso si escribe otro nombre para el archivo .vhd

	- Escriba el **Tamaño (GB)** del disco de datos.

	- Haga clic en la marca de verificación para continuar.

4.	Una vez creado y conectado el disco de datos, este aparece en el panel de la máquina virtual.

	![Disco de datos vacío conectado correctamente](./media/howto-attach-disk-window-linux/AttachEmptySuccess.png)

##<a id="attachexisting"></a>Acoplamiento de un disco existente

El acoplamiento de un disco existente requiere que disponga de un .vhd disponible en la cuenta de almacenamiento. Use el cmdlet [Add-AzureVhd](http://go.microsoft.com/FWLink/p/?LinkID=391684) para cargar el archivo .vhd a la cuenta de almacenamiento. Una vez que haya creado y cargado el archivo .vhd, puede acoplarlo a una máquina virtual.

1. Haga clic en **Máquinas virtuales** y, a continuación, seleccione la máquina virtual correspondiente.

2. En la barra de comandos, haga clic en **Conectar** y luego elija **Conectar disco**.


	![Acoplar disco de datos](./media/howto-attach-disk-window-linux/AttachExistingDisk.png)

	Aparecerá el cuadro de diálogo **Conectar disco**.



	![Especificar la información del disco de datos](./media/howto-attach-disk-window-linux/AttachExistingDetail.png)

3. Seleccione el disco de datos que desee acoplar a la máquina virtual.

4. Haga clic en la marca de verificación para acoplar el disco de datos a la máquina virtual.
 
5.	Una vez conectado el disco de datos, este aparece en el panel de la máquina virtual.


	![Disco de datos conectado correctamente](./media/howto-attach-disk-window-linux/AttachExistingSuccess.png)

> [AZURE.NOTE]Después de conectar un disco de datos, tendrá que iniciar sesión en la máquina virtual e inicializar el disco para que la máquina virtual pueda usar el disco para el almacenamiento.

<!---HONumber=58_postMigration-->