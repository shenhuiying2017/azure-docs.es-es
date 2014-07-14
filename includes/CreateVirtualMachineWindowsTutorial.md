
# Creación de una máquina virtual que ejecuta Windows Server #

Es fácil crear una máquina virtual que ejecute el sistema operativo Windows Server utilizando la Galería de imágenes en el Portal de administración de Azure. Este tutorial le enseña a crear una máquina virtual que ejecute Windows Server en la nube a la que podrá tener acceso y que podrá personalizar a su gusto. No es necesario que tenga experiencia previa con Azure para usar este tutorial.

Aprenderá lo siguiente:

* [Acerca de las máquinas virtuales en Azure](#virtualmachine)
* [Creación de una máquina virtual](#custommachine)
* [Inicio de sesión en una máquina virtual después de su
  creación](#logon)
* [Acoplamiento de un disco de datos a la nueva máquina
  virtual](#attachdisk)

**Nota:** este tutorial crea una máquina virtual que no está conectada a
una red virtual. Si desea que una máquina virtual use una red virtual, debe especificar la red virtual cuando cree la máquina virtual. Para obtener más información acerca de redes virtuales, consulte [Información general sobre redes virtuales de Azure][1].

## <a id="virtualmachine"> </a>Acerca de las máquinas virtuales en Azure

Una máquina virtual en Azure es un servidor en la nube que puede controlar y administrar. Después de crear una máquina virtual en Azure, tiene la opción de eliminarla y volver a crearla cada vez que lo necesite, además de tener acceso a ella igual que hace con cualquier otro servidor. Para obtener más información, consulte [Máquinas virtuales][2].

## <a id="custommachine"> </a>Creación de una máquina virtual

Para crear una máquina virtual personalizada, use este tutorial que le enseña a usar el método **From Gallery** en el Portal de administración. Este método ofrece más opciones que el método **Quick Create** para configurar una máquina virtual al crearla.

1.  Inicie sesión en su suscripción para utilizar el [Portal de administración][3] de Azure. Si no tiene una suscripción, puede registrarse para obtener una [evaluación gratuita][4].

2.  En la barra de comandos, haga clic en **New**.

3.  Haga clic en **Máquina virtual** y, a continuación, haga clic en **From Gallery**.

4.  En **Choose an Image**, seleccione **Windows Server 2012 R2 Datacenter**. (Las imágenes disponibles pueden ser diferentes según la suscripción que esté usando). Haga clic en la flecha para continuar.

5.  Si hay varias versiones disponibles de la imagen, en **Version Release Date**, seleccione la versión que desea usar.

6.  En **Virtual Machine Name**, escriba el nombre que desea usar para la máquina virtual. Para esta máquina virtual, escriba **MyTestVM1**.

7.  En **Size**, seleccione el tamaño de la máquina virtual. El tamaño que debe seleccionar depende de la cantidad de núcleos necesarios para ejecutar la aplicación. Para esta máquina virtual, seleccione el tamaño más pequeño disponible.

8.  En **New User Name**, escriba un nombre para la cuenta administrativa que desea usar para administrar el servidor. Para esta máquina virtual, escriba **MyTestVM1Admin**.

9.  En **New Password**, escriba una contraseña segura para la cuenta administrativa en la máquina virtual. En **Confirm Password**, vuelva a escribir la contraseña. Haga clic en la flecha para continuar.

10. Puede poner máquinas virtuales juntas en el servicio en la nube para proporcionar aplicaciones sólidas; no obstante, en este tutorial solo se crea una máquina virtual. Para hacer esto, seleccione **Create a new cloud service**.

11. En **Cloud Service DNS Name**, escriba un nombre que use entre 3 y 24 letras minúsculas y números. Este nombre se convierte en parte del URI que se usa para ponerse en contacto con la máquina virtual mediante el servicio en la nube. Para esta máquina virtual, escriba **MyService1**.

12. En **Region/Affinity Group/Virtual Network**, seleccione dónde desea poner la máquina virtual.

13. Para almacenar el VHD que utilizará la máquina virtual, acepte la configuración predeterminada de **Use an Automatically Generated Storage Account**.

14. En **Conjunto de disponibilidad**, para los propósitos de este tutorial, use la configuración predeterminada de **None**. Haga clic en la flecha para continuar.

15. En **VM Agent**, decida si va a instalar el Agente de VM. Este agente proporciona el entorno para que pueda instalar las extensiones que pueden ayudarlo a interactuar con la máquina virtual. Para conocer los detalles, consulte [Using Extensions][5].

16. En **Endpoints**, revise los extremos nuevos que se van a crear para permitir conexiones a la máquina virtual, como por ejemplo a través del Escritorio remoto y la comunicación remota de Windows PowerShell. Puede también agregar extremos ahora, o crearlos más tarde. Para obtener instrucciones sobre la creación de extremos más adelante, consulte [Establecimiento de comunicación con una máquina virtual][6].

17. Haga clic en la marca de verificación para crear la máquina virtual.
    
    Después de que sea crea la máquina virtual y el servicio en la nube, el Portal de administración muestra la máquina virtual nueva en **Máquinas virtuales** y el servicio en la nube en **Servicios en la nube**. Tanto la máquina virtual como el servicio en la nube se inician automáticamente.

## <a id="logon"> </a>Inicio de sesión en una máquina virtual después de su creación

Puede iniciar sesión en la máquina virtual que creó para administrar tanto su configuración como las aplicaciones que se ejecutan en ella.

1.  Inicie sesión en el [Portal de administración][3] de Azure.

2.  Haga clic en **Virtual Machines** y, a continuación, seleccione la máquina virtual **MyTestVM1**.

3.  En la barra de comandos, haga clic en **Connect**.

4.  Haga clic en **Open** para usar el archivo de protocolo de escritorio remoto que se creó automáticamente para la máquina virtual.

5.  Haga clic en **Connect**.
    
    ![Continuar con la conexi&oacute;n](./media/CreateVirtualMachineWindowsTutorial/connectpublisher.png)

6.  En el cuadro de contraseña, escriba el nombre de usuario y la contraseña que especificó al crear la máquina virtual y, a continuación, haga clic en **OK**.

7.  Haga clic en **Yes** para verificar la identidad de la máquina virtual.
    
    ![Verifique la identidad de la m&aacute;quina](./media/CreateVirtualMachineWindowsTutorial/connectverify.png)
    
    Ahora puede trabajar con la máquina virtual al igual como lo hace con un servidor en su oficina.

## <a id="attachdisk"> </a>Acoplamiento de un disco de datos a la nueva máquina virtual

Es posible que su aplicación necesite almacenar datos. Para configurar esta opción, acople un disco de datos a la máquina virtual. La manera más fácil de hacer esto es acoplar un disco de datos vacío a la máquina virtual.

1.  Inicie sesión en el [Portal de administración][3] de Azure.

2.  Haga clic en **Virtual Machines** y, a continuación, seleccione la máquina virtual **MyTestVM1**.

3.  En la barra de comandos, haga clic en **Attach** y, a continuación, haga clic en **Attach Empty Disk**.
    
    Aparece el cuadro de diálogo **Attach Empty Disk**.
    
    > [WACOM.NOTE\] Es posible que aparezca la página Quick Start en
    > lugar del panel y la barra de comandos. Si esto ocurre, haga clic
    > en **Panel** en la parte superior.

4.  **Virtual Machine Name**, **Storage Location**, **File Name** y **Host Cache Preference** ya están definidos para usted. Solo tiene que especificar el tamaño que desea utilizar para el disco. Escriba **5** en el campo **Size**.
    
    **Nota:** todos los discos se crean a partir de un archivo VHD en el almacenamiento de Azure. Puede proporcionar un nombre para el archivo VHD que se agregue al almacenamiento, pero Azure genera el nombre del disco automáticamente.

5.  Haga clic en la marca de verificación para acoplar el disco de datos a la máquina virtual.

6.  Haga clic en el nombre de la máquina virtual. De esta manera aparece el panel para que pueda verificar que el disco de datos se acopló correctamente a la máquina virtual.
    
    La máquina virtual tiene ahora 2 discos. El disco que acopló aparece en la tabla **Disks**.
    
    ![Acoplar disco vac&iacute;o](./media/CreateVirtualMachineWindowsTutorial/attachemptysuccess.png)
    
    Después de acoplar el disco de datos a la máquina virtual, el disco permanecerá desconectado y no inicializado. Antes de que pueda usarlo para almacenar datos, tendrá que iniciar sesión en la máquina virtual e inicializarlo.

7.  Conéctese a la máquina virtual usando los pasos que se mencionaron en la sección anterior, **Inicio de sesión en una máquina virtual después de su creación**.

8.  Después de iniciar sesión en la máquina virtual, abra el **Server Manager**. En el panel izquierdo, expanda **Almacenamiento** y, a continuación, haga clic en **Disk Management**.
    
    ![Inicializar el disco en el Administrador de servidores](./media/CreateVirtualMachineWindowsTutorial/servermanager.png)

9.  Haga clic con el botón secundario en **Disk 2** y, a continuación, haga clic en **Initialize Disk**.
    
    ![Comenzar la inicializaci&oacute;n](./media/CreateVirtualMachineWindowsTutorial/initializedisk.png)

10. Haga clic en **OK** para comenzar el proceso de inicialización.

11. Haga clic con el botón secundario en el área de asignación de espacio para el Disco 2, haga clic en **New Simple Volume** y, a continuación, finalice el asistente con los valores predeterminados.
    
    ![Crear el volumen](./media/CreateVirtualMachineWindowsTutorial/initializediskvolume.png)
    
    El disco está ahora en línea y listo para usarse con una nueva letra de unidad.
    
    ![Inicializaci&oacute;n correcta](./media/CreateVirtualMachineWindowsTutorial/initializesuccess.png)

## Pasos siguientes

Para obtener más información sobre la configuración de máquinas virtuales de Windows en Azure, consulte los siguientes artículos:

- [Conexión de máquinas virtuales en un Servicio en la nube][7]

- [Administración de la disponibilidad de las máquinas virtuales][8]



[1]: http://go.microsoft.com/fwlink/p/?LinkID=294063
[2]: http://go.microsoft.com/fwlink/p/?LinkID=271224
[3]: http://manage.windowsazure.com
[4]: http://go.microsoft.com/fwlink/p/?LinkID=23435
[5]: http://go.microsoft.com/FWLink/p/?LinkID=390493
[6]: http://www.windowsazure.com/en-us/manage/linux/how-to-guides/setup-endpoints/
[7]: http://www.windowsazure.com/en-us/documentation/articles/cloud-services-connect-virtual-machine/
[8]: http://www.windowsazure.com/en-us/documentation/articles/manage-availability-virtual-machines/