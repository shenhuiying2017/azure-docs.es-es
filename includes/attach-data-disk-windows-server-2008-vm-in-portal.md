Siga estos pasos para acoplar un disco de datos:

1.  En el [Portal de administración de Azure][1], haga clic en **Máquinas virtuales** y, a continuación, seleccione la máquina virtual que acaba de crear (**testwinvm**).

2.  En la barra de comandos, haga clic en **Attach** y, a continuación, haga clic en **Attach Empty Disk**.
    
    Aparece el cuadro de diálogo **Attach empty disk to virtual machine**.

3.  **Virtual Machine Name**, **Storage Location** y **File Name** ya están definidos. Solo tiene que especificar el tamaño que desea utilizar para el disco. Escriba **5** en el campo **Size**.
    
    ![Acoplar disco
    vac&iacute;o](./media/attach-data-disk-windows-server-2008-vm-in-portal/AttachDataDiskWinVM2.png)
    
    **Nota:** todos los discos se crean a partir de un archivo VHD en el almacenamiento de Azure. Puede proporcionar un nombre para el archivo VHD que se agregue al almacenamiento, pero Azure genera el nombre del disco automáticamente.

4.  Haga clic en la marca de verificación para acoplar el disco de datos a la máquina virtual.

5.  Haga clic en el nombre de la máquina virtual para mostrar el panel; de este modo podrá comprobar que el disco de datos se ha adjuntado correctamente a la máquina virtual.
    
    Ahora, el número de discos de la máquina virtual es 2. El disco que acopló aparece en la tabla Disks.
    
    ![Acoplar disco
    vac&iacute;o](./media/attach-data-disk-windows-server-2008-vm-in-portal/AttachDataDiskWinVM3.png)
    
    Después de acoplar el disco de datos a la máquina virtual, el disco permanecerá desconectado y no inicializado. Debe iniciar sesión en la máquina virtual e inicializar el disco antes de que pueda usarlo para almacenar datos.

## Conexión a la máquina virtual con Escritorio remoto y finalización de la instalación

1.  Una vez aprovisionada la máquina virtual, en el Portal de administración, haga clic en **Máquinas virtuales** y, a continuación, haga clic en su nueva máquina virtual. Se muestra información acerca de su máquina virtual.

2.  En la parte inferior de la página, haga clic en **Conectar**. Abra el archivo .rpd con el programa Escritorio remoto de Windows (*%windir%\\system32\\mstsc.exe*).

3.  En el cuadro de diálogo **Seguridad de Windows**, proporcione la contraseña de la cuenta **Administrador**. (Puede que se le pida comprobar las credenciales de la máquina virtual). Es posible que, la primera vez que inicie sesión en esta máquina virtual, deban completarse varios procesos, entre los que se incluyen la configuración del escritorio, las actualizaciones de Windows y la finalización de las tareas de configuración inicial de Windows. Una vez se haya conectado a la máquina virtual con el Escritorio remoto de Windows, la máquina virtual funcionará como cualquier otro equipo.

4.  Después de iniciar sesión en la máquina virtual, abra el **Server Manager**. En el panel izquierdo, expanda **Almacenamiento** y, a continuación, haga clic en **Disk Management**.
    
    ![Administrador de
    servidores](./media/attach-data-disk-windows-server-2008-vm-in-portal/servermanager.png)

5.  Aparece la ventana **Initalize Disk**. Haga clic en **OK**.
    
    ![Inicializar
    disco](./media/attach-data-disk-windows-server-2008-vm-in-portal/initializedisk0.png)

6.  Haga clic con el botón secundario en el área de asignación de espacio para el Disco 2, haga clic en **New Simple Volume** y, a continuación, finalice el asistente con los valores predeterminados.
    
    ![Nuevo volumen
    simple](./media/attach-data-disk-windows-server-2008-vm-in-portal/initializediskvolume.png)
    
    El disco está ahora en línea y listo para usarse con una nueva letra de unidad.
    
    ![La inicializaci&oacute;n se ha realizado
    correctamente.](./media/attach-data-disk-windows-server-2008-vm-in-portal/initializesuccess.png)



[1]: http://manage.windowsazure.com