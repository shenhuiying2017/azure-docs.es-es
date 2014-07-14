# Creación de una máquina virtual que ejecuta Linux

Es fácil crear una máquina virtual que ejecute el sistema operativo Linux utilizando la Galería de imágenes en el Portal de administración de Azure. En esta guía se asume que no tiene ninguna experiencia previa con Azure. Puede crear una máquina virtual que ejecute el sistema operativo Linux en la nube; podrá tener acceso a ella y personalizarla a su gusto.

Aprenderá lo siguiente:

* [Acerca de las máquinas virtuales en Azure](#virtualmachine)
* [Creación de una máquina virtual](#custommachine)
* [Inicio de sesión en una máquina virtual después de su
  creación](#logon)
* [Acoplamiento de un disco de datos a la nueva máquina
  virtual](#attachdisk)

**Nota**: este tutorial crea una máquina virtual que no está conectada
a una red virtual. Si desea que una máquina virtual use una red virtual, debe especificar la red virtual cuando cree la máquina virtual. Para obtener más información acerca de redes virtuales, consulte [Información general sobre redes virtuales de Azure][1].

## <a id="virtualmachine"> </a>Acerca de las máquinas virtuales en Azure

Una máquina virtual en Azure es un servidor en la nube que puede controlar y administrar. Después de crear una máquina virtual en Azure, tiene la opción de eliminarla y volver a crearla cada vez que lo necesite, además de tener acceso a ella igual que hace con un servidor de su oficina. Para crear una máquina virtual, se usan archivos de un disco duro virtual (VHD). Se usan los siguientes tipos de discos duros virtuales para una máquina virtual:

* **Imagen**: Un VHD que se usa como plantilla para crear una máquina
  virtual nueva. Una imagen es una plantilla porque no tiene una
  configuración específica como una máquina virtual en ejecución, tal
  como el nombre del equipo y la configuración de la cuenta de usuario.
  Si crea una máquina virtual usando una imagen, se crea un disco de
  sistema operativo automáticamente para la máquina virtual nueva.
* **Disco**: Un disco es un VHD que puede arrancar y montar como una
  versión en ejecución de un sistema operativo. Después de que se
  proporciona una imagen, se convierte en un disco. Siempre se crea un
  disco al usar una imagen para crear una máquina virtual. Un disco es
  todo VHD que se adjunta a hardware virtualizado y que se ejecuta como
  parte de un servicio.

Para crear una máquina virtual con el uso de imágenes tiene disponibles las opciones siguientes:

* Cree una máquina virtual usando una imagen que se proporciona en la
  Galería de imágenes del Portal de administración de Azure.
* Cree y cargue un archivo .vhd que contenga una imagen de Azure y luego
  cree una máquina virtual usando la imagen. Para obtener más
  información sobre la creación y la carga de una imagen personalizada,
  consulte [Creación y carga de un disco duro virtual que contiene el
  sistema operativo
  Linux](/en-us/manage/linux/common-tasks/upload-a-vhd/).

Cada máquina virtual reside en un servicio en la nube, ya sea sola o agrupada con otras máquinas virtuales. Puede poner máquinas virtuales en el mismo servicio en la nube para que las máquinas virtuales puedan comunicarse entre sí, equilibrar la carga de tráfico de red entre las máquinas virtuales y mantener la alta disponibilidad de las máquinas. Para obtener más información sobre los servicios de nube y las máquinas virtuales, consulte la sección "Modelos de ejecución" en [Introducción a Azure][2].

## <a id="custommachine"> </a>Creación de una máquina virtual

Use el método **From Gallery** para crear una máquina virtual personalizada en el Portal de administración. Este método ofrece más opciones para configurar la máquina virtual cuando la crea, como los recursos conectados, el nombre de DNS y la conectividad de red, si es necesario.

1.  Inicie sesión en el [Portal de administración][3] de Azure. 
En la barra de comandos, haga clic en **New**.

2.  Haga clic en **Máquina virtual** y, a continuación, haga clic en **From Gallery**.

3.  En **Choose an Image**, seleccione una imagen de una de las listas. (Las imágenes disponibles pueden ser diferentes según la suscripción que esté usando). Haga clic en la flecha para continuar.

4.  Si hay varias versiones disponibles de la imagen, en **Version Release Date**, seleccione la versión que desea usar.

5.  En **Virtual Machine Name**, escriba el nombre que desea usar. Para esta máquina virtual, escriba **MyTestVM1**.

6.  En **Size**, seleccione el tamaño que desea usar para la máquina virtual. El tamaño que seleccione depende de la cantidad de núcleos que se necesitan para su aplicación. Para esta máquina virtual, seleccione el tamaño más pequeño disponible.

7.  En **New User Name**, escriba el nombre de la cuenta que va a usar para administrar la máquina virtual. No puede usar la raíz para el nombre de usuario. Para esta máquina virtual, escriba **NewUser1**.

8.  En Autenticación, marque **Provide a Password**. Luego, proporcione la información necesaria y haga clic en la flecha para continuar.

9.  Puede poner máquinas virtuales juntas en el servicio en la nube; no obstante, en este tutorial solo va a crear una máquina virtual. Para hacer esto, seleccione **Create a new cloud service**.

10. En **Cloud Service DNS Name**, escriba un nombre que use entre 3 y 24 letras minúsculas y números. Este nombre se convierte en parte del URI que se usa para ponerse en contacto con la máquina virtual mediante el servicio en la nube. Para esta máquina virtual, escriba **MyService1**.

11. En **Region/Affinity Group/Virtual Network**, seleccione dónde desea poner la máquina virtual.

12. Puede seleccionar una cuenta de almacenamiento donde se almacena el archivo de VHD. Para este tutorial, acepte la configuración predeterminada de **Use an Automatically Generated Storage Account**.

13. En **Conjunto de disponibilidad**, para los propósitos de este tutorial, use la configuración predeterminada de **None**. Haga clic en la marca de verificación para crear la máquina virtual y, a continuación, haga clic en la flecha para continuar.

14. En **VM Agent**, decida si va a instalar el Agente de VM. Este agente proporciona el entorno para que pueda instalar las extensiones que pueden ayudarlo a interactuar con la máquina virtual. Para conocer los detalles, consulte [Using Extensions][4].

15. En **Endpoints**, revise el extremo que se crea automáticamente para permitir conexiones de Secure Shell (SSH) con la máquina virtual. (Los extremos permiten que los recursos en Internet u otras redes virtuales se comuniquen con una máquina virtual). Puede agregar más extremos ahora o crearlos más adelante. Para obtener instrucciones sobre la creación de extremos más adelante, consulte [Establecimiento de comunicación con una máquina virtual][5].

Después de que sea crea la máquina virtual y el servicio en la nube, el Portal de administración muestra la máquina virtual nueva en **Máquinas virtuales** y el servicio en la nube en **Servicios en la nube**. Tanto la máquina virtual como el servicio en la nube se inician automáticamente.

## <a id="logon"> </a>Inicio de sesión en una máquina virtual después de su creación

Para administrar la configuración de la máquina virtual y las aplicaciones que se ejecutan en la máquina, puede usar un cliente SSH. Para ello, debe instalar un cliente de SSH en el equipo que desea usar para tener acceso a la máquina virtual. Existen muchos programas cliente de SSH donde elegir. Las siguientes son algunas posibles opciones:

* Si va a usar un equipo que está ejecutando un sistema operativo
  Windows, es posible que desee usar un cliente de SSH, como por ejemplo
  PuTTY. Para obtener más información, consulte [Descarga de PuTTY][6].
* Si va a usar un equipo que está ejecutando un sistema operativo Linux,
  es posible que desee usar un cliente de SSH, como por ejemplo OpenSSH.
  Para obtener más información, consulte [OpenSSH][7].

Este tutorial le muestra cómo usar el programa PuTTY para tener acceso a la máquina virtual.

1.  Busque el nombre de host (**Host Name**) y la información de puerto (**Port information**) en el Portal de administración. Puede buscar la información que necesita desde el panel de la máquina virtual. Haga clic en el nombre de la máquina virtual y busque los **SSH Details** en la sección **Quick Glance** del panel.
    
    ![Buscar detalles de
    SSH](./media/CreateVirtualMachineLinuxTutorial/SSHdetails.png)

2.  Abra el programa PuTTY.

3.  Escriba los valores en **Host Name** y **Port information** que recopiló en el panel y, a continuación, haga clic en **Open**.
    
    ![Escribir el nombre de host y la informaci&oacute;n de
    puerto](./media/CreateVirtualMachineLinuxTutorial/putty.png)

4.  Inicie sesión en la máquina virtual usando la cuenta NewUser1 que especificó cuando se creó la máquina.
    
    ![Iniciar sesi&oacute;n en la nueva m&aacute;quina virtual](./media/CreateVirtualMachineLinuxTutorial/sshlogin.png)
    
    Puede trabajar con la máquina virtual al igual que hace con cualquier otro servidor.

## <a id="attachdisk"> </a>Acoplamiento de un disco de datos a la nueva máquina virtual

Es posible que su aplicación necesite almacenar datos. Para hacerlo posible, adjunta un disco de datos a la máquina virtual que creó anteriormente. La manera más fácil de hacerlo es adjuntar un disco de datos vacío a la máquina.

**Nota: disco de datos frente a disco de recursos**  
 Los discos de datos residen en el Almacenamiento de Azure y se pueden usar para un almacenamiento continuo de archivos y datos de aplicaciones.

Cada máquina virtual que se crea tiene también adjunto un *Disco de recursos* local temporal. Debido a que los datos de un disco de recursos podrían no resistir los diversos reinicios, muchas veces los usan aplicaciones y procesos que se ejecutan en la máquina virtual para un almacenamiento de datos transitorio y temporal. También se usan para almacenar archivos de intercambio y de paginación para el sistema operativo.

En Linux, el disco de recursos se administra generalmente mediante el Agente de Linux de Azure y se monta automáticamente en **/mnt/resource** (o **/mnt** en las imágenes de Ubuntu). Consulte la [Guía de usuario del Agente de Linux de Azure][8] para obtener más información.

1.  Si no lo ha hecho todavía, inicie sesión en el Portal de administración de Azure.

2.  Haga clic en **Máquinas virtuales** y, a continuación, seleccione la máquina virtual **MyTestVM1** que creó anteriormente.

3.  En la barra de comandos, haga clic en **Attach** y, a continuación, haga clic en **Attach Empty Disk**.
    
    Aparece el cuadro de diálogo **Attach Empty Disk**.
    
    ![Definir los detalles del disco](./media/CreateVirtualMachineLinuxTutorial/attachnewdisklinux.png)

4.  **Virtual Machine Name**, **Storage Location** y **File Name** ya están definidos. Solo tiene que especificar el tamaño que desea utilizar para el disco. Escriba **5** en el campo **Size**.
    
    **Nota:** todos los discos se crean a partir de un archivo VHD en el almacenamiento de Azure. Puede proporcionar un nombre para el archivo VHD que se agregue al almacenamiento, pero el nombre del disco se general automáticamente.

5.  Haga clic en la marca de verificación para acoplar el disco de datos a la máquina virtual.

6.  Puede verificar que el disco de datos se haya adjuntado correctamente a la máquina virtual si observa el panel. Haga clic en el nombre de la máquina virtual para ver el panel.
    
    La cantidad de discos ahora es 2 para la máquina virtual y el disco que adjuntó se enumera en la tabla **Discos**.
    
    ![Disco adjuntado
    correctamente](./media/CreateVirtualMachineLinuxTutorial/attachemptysuccess.png)

El disco de datos que acaba de adjuntar a la máquina virtual permanecerá desconectado y no inicializado después de haberlo agregado. Debe iniciar sesión en la máquina virtual e inicializar el disco para usarlo para almacenar datos.

1.  Conéctese a la máquina virtual siguiendo los pasos que se mencionaron anteriormente en **Inicio de sesión en una máquina virtual después de su creación**.

2.  En la ventana SSH, escriba el siguiente comando y, a continuación, la contraseña de la cuenta:
    
    `sudo grep SCSI /var/log/messages`
    
    Puede buscar el identificador del último disco de datos conectado en los mensajes que se muestran.
    
    ![Identificar
    disco](./media/CreateVirtualMachineLinuxTutorial/diskmessages.png)

3.  En la ventana SSH, escriba el siguiente comando para crear un nuevodispositivo y, a continuación, especifique la contraseña de la cuenta:
    
    `sudo fdisk /dev/sdc`
    
    > \[WACOM.NOTE\] En este ejemplo, es posible que tenga que utilizar `sudo -i` en algunas distribuciones si /sbin o /usr/sbin no se encuentran en su `$PATH`.

4.  Escriba **n** para crear una nueva partición.
    
    ![Crear un dispositivo nuevo](./media/CreateVirtualMachineLinuxTutorial/diskpartition.png)

5.  Escriba **p** para que la partición sea la partición principal, escriba **1** para que sea la primera partición y, a continuación, escriba "enter" para aceptar el valor predeterminado para el cilindro.
    
    ![Crear partici&oacute;n](./media/CreateVirtualMachineLinuxTutorial/diskcylinder.png)

6.  Escriba **p** para ver los detalles del disco en el que se va a crear la partición.
    
    ![Enumerar la informaci&oacute;n del disco](./media/CreateVirtualMachineLinuxTutorial/diskinfo.png)

7.  Escriba **w** para escribir la configuración del disco.
    
    ![Escribir los cambios del disco](./media/CreateVirtualMachineLinuxTutorial/diskwrite.png)

8.  Debe crear el sistema de archivos en la partición nueva. Como ejemplo, escriba el siguiente comando para crear el sistema de archivos y, a continuación, especifique la contraseña de la cuenta:
    
    `sudo mkfs -t ext4 /dev/sdc1`
    
    ![Crear sistema de archivos](./media/CreateVirtualMachineLinuxTutorial/diskfilesystem.png)
    
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

## Pasos siguientes

Para obtener más información sobre Linux en Azure, consulte los siguientes artículos:

* [Introducción a Linux en Azure][9]

* [Uso de las herramientas de línea de comandos de Azure para Mac y
  Linux][10]



[1]: http://go.microsoft.com/fwlink/p/?LinkID=294063
[2]: http://go.microsoft.com/fwlink/p/?LinkId=311926
[3]: http://manage.windowsazure.com
[4]: http://go.microsoft.com/FWLink/p/?LinkID=390493
[5]: http://www.windowsazure.com/en-us/manage/linux/how-to-guides/setup-endpoints/
[6]: http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html
[7]: http://www.openssh.org/
[8]: http://www.windowsazure.com/en-us/manage/linux/how-to-guides/linux-agent-guide/
[9]: http://www.windowsazure.com/en-us/documentation/articles/introduction-linux/
[10]: http://www.windowsazure.com/en-us/documentation/articles/xplat-cli/