


## <a name="attach-an-empty-disk"></a>Conectar un disco vacío
El acoplamiento de un disco vacío supone un método sencillo de agregar un disco de datos, porque Azure crea el archivo .vhd y lo guarda en la cuenta de almacenamiento.

1. Haga clic en **Virtual Machines (clásico)** y luego seleccione la máquina virtual adecuada.

2. En el menú Configuración, haga clic en **Discos**.

   ![Conectar un nuevo disco vacío](./media/howto-attach-disk-windows-linux/menudisksattachnew.png)

3. En la barra de comandos, haga clic en **Asociar nuevo**.  
    Aparece el cuadro de diálogo **Asociar nuevo disco**.

    ![Conexión de un disco nuevo](./media/howto-attach-disk-windows-linux/newdiskdetail.png)

    Rellene la información siguiente:
    - En **Nombre de archivo**, acepte el nombre predeterminado o escriba otro para el archivo .vhd. El disco de datos usa un nombre generado automáticamente, incluso si escribe otro nombre para el archivo .vhd
    - Seleccione el **tipo** del disco de datos. Todas las máquinas virtuales admiten discos estándares. Muchas máquinas virtuales también admiten discos premium.
    - Seleccione el **tamaño (GB)** del disco de datos.
    - Para **Almacenamiento en caché de host**, elija Ninguno o Solo lectura.
    - Haga clic en Aceptar para finalizar.

4. Una vez creado y conectado el disco de datos, este aparece en la sección de discos de la máquina virtual.

   ![Disco de datos nuevo y vacío conectado correctamente](./media/howto-attach-disk-windows-linux/newdiskemptysuccessful.png)

> [!NOTE]
> Después de agregar un disco de datos, es preciso iniciar sesión en la máquina virtual e inicializar el disco para que se pueda usar.

## <a name="how-to-attach-an-existing-disk"></a>Acoplamiento de un disco existente
El acoplamiento de un disco existente requiere que disponga de un .vhd disponible en la cuenta de almacenamiento. Use el cmdlet [Add-AzureVhd](https://msdn.microsoft.com/library/azure/dn495173.aspx) para cargar el archivo .vhd a la cuenta de almacenamiento. Una vez que se haya creado y cargado el archivo .vhd, puede acoplarlo a una VM.

1. Haga clic en **Virtual Machines (clásico)** y luego seleccione la máquina virtual correspondiente.

2. En el menú Configuración, haga clic en **Discos**.

3. En la barra de comandos, haga clic en **Asociar existente**.

    ![Acoplar disco de datos](./media/howto-attach-disk-windows-linux/menudisksattachexisting.png)

4. Haga clic en **Ubicación**. Se muestran las cuentas de almacenamiento disponibles. A continuación, seleccione una cuenta de almacenamiento adecuada de las que aparecen.

    ![Proporcionar la cuenta de almacenamiento de disco](./media/howto-attach-disk-windows-linux/existdiskstorageaccounts.png)

5. Una **cuenta de almacenamiento** contiene uno o varios contenedores que contienen unidades de disco (discos duros virtuales). Seleccione el contenedor adecuado de los que aparecen.

    ![Proporcionar el contenedor de máquinas virtuales Windows](./media/howto-attach-disk-windows-linux/existdiskcontainers.png)

6. En el panel de **discos duros virtuales** se muestran las unidades de disco que contiene el contenedor. Haga clic en uno de los discos y luego en Seleccionar.

    ![Proporcionar la imagen de disco para máquinas virtuales de Windows](./media/howto-attach-disk-windows-linux/existdiskvhds.png)

7. Se muestra de nuevo el panel **Asociar disco existente**, con la ubicación que contiene la cuenta de almacenamiento, el contenedor y la unidad de disco duro virtual seleccionada que se agregará a la máquina virtual.

  Establezca **Almacenamiento en caché de host** en Ninguno o Solo lectura y luego haga clic en Aceptar.

    ![Disco de datos conectado correctamente](./media/howto-attach-disk-windows-linux/exisitingdisksuccessful.png)
