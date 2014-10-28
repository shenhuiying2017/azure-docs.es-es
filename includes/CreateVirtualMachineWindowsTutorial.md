<properties title="Create a Virtual Machine Running Windows Server" pageTitle="How to create a Virtual Machine Running Windows Server" description="Describes how to create a Windows virtual machine, add a data disk, and log on remotely" metaKeywords="" services="virtual machines" solutions="" documentationCenter="" authors="kathydav" videoId="" scriptId="" />

# Creación de una máquina virtual que ejecuta Windows Server

En este tutorial se muestra lo fácil que es crear una máquina virtual de Azure que ejecute Windows Server, usando la Galería de imágenes del Portal de administración de Azure. La Galería de imágenes ofrece una gran variedad de imágenes, incluidos los sistemas operativos Windows, sistemas operativos basados en Linux e imágenes de aplicaciones.

> [WACOM.NOTE] No es necesario que tenga experiencia con máquinas virtuales de Azure para completar este tutorial. Sin embargo, sí necesita una cuenta de Azure. Puede crear una cuenta de evaluación gratuita en pocos minutos. Para obtener más información, consulte [Creación de una cuenta de Azure][].

En este tutorial se explica lo siguiente:

-   [Creación de una máquina virtual][]
-   [Inicio de sesión en una máquina virtual después de su creación][]
-   [Acoplamiento de un disco de datos a la nueva máquina virtual][]

Si desea obtener más información, consulte [Máquinas virtuales][].

## <span id="createvirtualmachine"></span> </a>Creación de una máquina virtual

En esta sección se muestra cómo usar la opción **De la galería** en el Portal de administración para crear una máquina virtual. Esta opción proporciona más opciones de configuración que la opción **Creación rápida**. Por ejemplo, si desea unir una máquina virtual a una red virtual, deberá usar la opción **De la galería**.

[WACOM.INCLUDE [virtual-machines-create-WindowsVM][]]

## <span id="logon"></span> </a>Inicio de sesión en una máquina virtual después de su creación

En esta sección se muestra cómo iniciar sesión en una máquina virtual para poder administrar su configuración y las aplicaciones que va a ejecutar en ella.

1.  Inicie sesión en el [Portal de administración][] de Azure.

2.  Haga clic en **Máquinas virtuales** y seleccione **MyTestVM**.

    ![Seleccione MyTestVM][]

3.  En la barra de comandos, haga clic en **Connect**.

    ![Conecte a MyTestVM][]

4.  Haga clic en **Open** para usar el archivo de protocolo de escritorio remoto que se creó automáticamente para la máquina virtual.

    ![Abra el archivo rdp][]

5.  Haga clic en **Connect**.

    ![Continuar con la conexión][]

6.  En el cuadro de contraseña, escriba el nombre de usuario y la contraseña que especificó al crear la máquina virtual y, a continuación, haga clic en **OK**.

7.  Haga clic en **Yes** para verificar la identidad de la máquina virtual.

    ![Verificar la identidad de la máquina][]

    Ahora puede trabajar con la máquina virtual al igual como lo hace con un servidor en su oficina.

## <span id="attachdisk"></span> </a>Acoplamiento de un disco de datos a la nueva máquina virtual

En esta sección se muestra cómo asociar un disco de datos vacío a una máquina virtual. Consulte el [Tutorial sobre cómo asociar un disco de datos] (<http://www.windowsazure.com/es-es/documentation/articles/storage-windows-attach-disk/>) para obtener más información sobre la asociación de discos vacíos y discos existentes.

1.  Inicie sesión en el [Portal de administración][] de Azure.

2.  Haga clic en **Máquinas virtuales** y seleccione **MyTestVM**.

    ![Seleccione MyTestVM][]

3.  Quizá el sistema le ha llevado primero a la página Inicio rápido. Si es así, seleccione **Panel** en la parte superior.

    ![Seleccione Panel][]

4.  En la barra de comandos, haga clic en **Adjuntar** y después en **Conectar disco vacío** cuando aparezca esta opción.

    ![Seleccione Adjuntar en la barra de comandos][]

5.  **Virtual Machine Name**, **Storage Location**, **File Name** y **Host Cache Preference** ya están definidos para usted. Solo tiene que especificar el tamaño que desea utilizar para el disco. Escriba **5** en el campo **Size**. Haga clic en la marca de verificación para asociar el disco vacío a la máquina virtual.

    ![Especifique el tamaño del disco vacío][]

    > [WACOM.NOTE] Todos los discos se crean a partir de un archivo VHD en el almacenamiento de Azure. En **Nombre de archivo**, puede proporcionar un nombre para el archivo VHD que se agrega al almacenamiento, pero Azure genera el nombre del disco automáticamente.

6.  Vuelva al panel para comprobar que el disco de datos vacío se ha asociado correctamente a la máquina virtual. Debe aparecer como segundo disco en la lista **Discos** junto con Disco del SO.

    ![Acoplar disco vacío][]

    Después de acoplar el disco de datos a la máquina virtual, el disco permanecerá desconectado y no inicializado. Antes de que pueda usarlo para almacenar datos, tendrá que iniciar sesión en la máquina virtual e inicializarlo.

7.  Conéctese a la máquina virtual usando los pasos de la sección anterior, [Inicio de sesión en una máquina virtual después de su creación][] (\#logon).

8.  Después de iniciar sesión en la máquina virtual, abra el **Server Manager**. En el panel izquierdo, seleccione **Servicios de archivos y almacenamiento**.

    ![Expanda Servicios de archivos y almacenamiento en el Administrador del servidor][]

9.  Seleccione **Discos** en el menú expandido.

    ![Expanda Servicios de archivos y almacenamiento en el Administrador del servidor][1]

10. En la sección **Discos**, hay tres discos en la lista: disco 0, disco 1 y disco 2. El disco 0 es el disco del sistema operativo, el disco 1 es un disco de recursos temporales (que no debe usarse para almacenamiento de datos) y el disco 2 es el disco de datos que ha asociado a la máquina virtual. Tenga en cuenta que el disco de datos tiene una capacidad de 5 GB como se especificó anteriormente. Haga clic con el botón secundario en el disco 2 y seleccione **Inicializar**.

    ![Comenzar la inicialización][]

11. Haga clic en **Sí** para comenzar el proceso de inicialización.

    ![Continúe la inicialización][]

12. Haga clic con el botón secundario de nuevo en el disco 2 y seleccione **Nuevo volumen**.

    ![Crear el volumen][]

13. Complete el asistente usando los valores predeterminados que se proporcionan. Una vez finalizado el asistente, aparece un volumen nuevo en la sección **Volúmenes**.

    ![Crear el volumen][2]

    El disco está ahora en línea y listo para usarse con una nueva letra de unidad.

## Pasos siguientes

Para obtener más información sobre la configuración de máquinas virtuales de Windows en Azure, consulte los siguientes artículos:

[Conexión de máquinas virtuales en un Servicio en la nube][]

[Creación y carga de su propio disco duro virtual con el sistema operativo Windows Server][]

[Asociación de discos de datos a una máquina virtual][]

[Administración de la disponibilidad de las máquinas virtuales][]

  [Creación de una cuenta de Azure]: http://www.windowsazure.com/es-es/develop/php/tutorials/create-a-windows-azure-account/
  [Creación de una máquina virtual]: #createvirtualmachine
  [Inicio de sesión en una máquina virtual después de su creación]: #logon
  [Acoplamiento de un disco de datos a la nueva máquina virtual]: #attachdisk
  [Máquinas virtuales]: http://go.microsoft.com/fwlink/p/?LinkID=271224
  [virtual-machines-create-WindowsVM]: ../includes/virtual-machines-create-WindowsVM.md
  [Portal de administración]: http://manage.windowsazure.com
  [Seleccione MyTestVM]: ./media/CreateVirtualMachineWindowsTutorial/selectvm.png
  [Conecte a MyTestVM]: ./media/CreateVirtualMachineWindowsTutorial/commandbarconnect.png
  [Abra el archivo rdp]: ./media/CreateVirtualMachineWindowsTutorial/openrdp.png
  [Continuar con la conexión]: ./media/CreateVirtualMachineWindowsTutorial/connectrdc.png
  [Verificar la identidad de la máquina]: ./media/CreateVirtualMachineWindowsTutorial/certificate.png
  [Seleccione Panel]: ./media/CreateVirtualMachineWindowsTutorial/dashboard.png
  [Seleccione Adjuntar en la barra de comandos]: ./media/CreateVirtualMachineWindowsTutorial/commandbarattach.png
  [Especifique el tamaño del disco vacío]: ./media/CreateVirtualMachineWindowsTutorial/emptydisksize.png
  [Acoplar disco vacío]: ./media/CreateVirtualMachineWindowsTutorial/disklistwithdatadisk.png
  [Expanda Servicios de archivos y almacenamiento en el Administrador del servidor]: ./media/CreateVirtualMachineWindowsTutorial/fileandstorageservices.png
  [1]: ./media/CreateVirtualMachineWindowsTutorial/selectdisks.png
  [Comenzar la inicialización]: ./media/CreateVirtualMachineWindowsTutorial/initializedisk.png
  [Continúe la inicialización]: ./media/CreateVirtualMachineWindowsTutorial/yesinitialize.png
  [Crear el volumen]: ./media/CreateVirtualMachineWindowsTutorial/initializediskvolume.png
  [2]: ./media/CreateVirtualMachineWindowsTutorial/newvolumecreated.png
  [Conexión de máquinas virtuales en un Servicio en la nube]: http://www.windowsazure.com/es-es/documentation/articles/cloud-services-connect-virtual-machine/
  [Creación y carga de su propio disco duro virtual con el sistema operativo Windows Server]: http://www.windowsazure.com/es-es/documentation/articles/virtual-machines-create-upload-vhd-windows-server/
  [Asociación de discos de datos a una máquina virtual]: http://www.windowsazure.com/es-es/documentation/articles/storage-windows-attach-disk/
  [Administración de la disponibilidad de las máquinas virtuales]: http://www.windowsazure.com/es-es/documentation/articles/manage-availability-virtual-machines/
