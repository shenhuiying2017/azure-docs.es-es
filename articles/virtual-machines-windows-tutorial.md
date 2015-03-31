<properties 
	pageTitle="Creación de una máquina virtual que ejecuta Windows en Azure" 
	description="Aprenda a crear una máquina virtual de Windows (VM) en Azure para, a continuación, iniciar sesión y conectar un disco de datos" 
	services="virtual-machines" 
	documentationCenter="" 
	authors="KBDAzure" 
	manager="timlt" 
	editor="tysonn"/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-windows" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/04/2015" 
	ms.author="kathydav"/>



# Creación de una máquina virtual que ejecuta Windows

<div class="dev-center-tutorial-selector sublanding"><a href="/documentation/articles/virtual-machines-windows-tutorial/" title="Azure Portal" class="current">Portal de Azure</a><a href="/documentation/articles/virtual-machines-windows-tutorial-azure-preview/" title="Azure Preview Portal">Portal de vista previa de Azure</a></div>

Este tutorial muestra lo fácil que resulta crear una máquina virtual (VM) de Azure. En este tutorial se usa una imagen de Windows Server, pero eso es solo una de las muchas imágenes disponibles con Azure. Incluye sistemas operativos Windows, sistemas operativos basados en Linux e imágenes con aplicaciones preinstaladas. Las imágenes que puede elegir dependen del tipo de suscripción que tiene. Por ejemplo, las imágenes de escritorio pueden estar disponibles para los suscriptores MSDN.


También puede crear máquinas virtuales de Windows con [sus propias imágenes como plantillas](../virtual-machines-create-upload-vhd-windows-server/). Para obtener más información acerca de las máquinas virtuales de Azure, vea [Información general sobre las máquinas virtuales de Azure](http://msdn.microsoft.com/library/azure/jj156143.aspx).

[AZURE.INCLUDE [free-trial-note](../includes/free-trial-note.md)]

## <a id="createvirtualmachine"> </a>Creación de una máquina virtual

En esta sección se muestra cómo usar la opción **Desde la galería** en el Portal de administración para crear una máquina virtual. Esta opción proporciona más opciones de configuración que la opción **Creación rápida**. Por ejemplo, si desea unir una máquina virtual a una red virtual, deberá usar la opción **Desde la galería**.

> [AZURE.NOTE] También puede probar el [Portal de vista previa de Azure](https://portal.azure.com), más completo y personalizable, para crear una máquina virtual, automatizar la implementación de plantillas de aplicación para varias máquinas virtuales, usar características mejoradas de supervisión y diagnóstico de máquinas virtuales y mucho más. Las opciones de configuración de máquinas virtuales disponibles en los dos portales son muy similares pero no idénticas.  

[AZURE.INCLUDE [virtual-machines-create-WindowsVM](../includes/virtual-machines-create-WindowsVM.md)]

## <a id="logon"> </a>Inicio de sesión en una máquina virtual después de su creación 

En esta sección se muestra cómo iniciar sesión en una máquina virtual para poder administrar su configuración y las aplicaciones que va a ejecutar en ella.

[AZURE.INCLUDE [virtual-machines-log-on-win-server](../includes/virtual-machines-log-on-win-server.md)]

## <a id="attachdisk"> </a>Acoplamiento de un disco de datos a la nueva máquina virtual 

En esta sección se muestra cómo asociar un disco de datos vacío a una máquina virtual. Consulte el tutorial [Adjuntar un disco de datos](http://azure.microsoft.com/documentation/articles/storage-windows-attach-disk/) para obtener más información, incluido el proceso de adjuntar discos existentes.

1. Inicie sesión en el [Portal de administración de Azure](http://manage.windowsazure.com).

2. Haga clic en **Máquinas virtuales** y, a continuación, seleccione la máquina virtual **MyTestVM**.

	![Select MyTestVM](./media/virtual-machines-windows-tutorial/selectvm.png)
	
3. Puede ver la página Inicio rápido en primer lugar. Si es así, seleccione **Panel** en la parte superior.

	![Select Dashboard](./media/virtual-machines-windows-tutorial/dashboard.png)

4. En la barra de comandos, haga clic en **Conectar** y, a continuación, en **Conectar disco vacío** cuando aparezca esta opción.

	![Select Attach from the command bar](./media/virtual-machines-windows-tutorial/commandbarattach.png)	

5. Las opciones **Nombre de la máquina virtual**, **Ubicación de almacenamiento**, **Nombre de archivo** y **Preferencia de caché de host** ya están predefinidas. Solo tendrá que especificar un tamaño para el disco. Por ejemplo, escriba **5** en el campo **Tamaño**. Haga clic en la marca de verificación para conectar el disco.


	>[AZURE.NOTE] Todos los discos se crean a partir de archivos .vhd en el almacenamiento de Azure. **El Nombre de archivo** permite asignar el nombre al archivo .vhd que usa el disco, no el nombre del disco. Azure asigna automáticamente un nombre para el disco. 

	![Specify the size of the empty disk](./media/virtual-machines-windows-tutorial/emptydisksize.png)	
	
	>[AZURE.NOTE] Los archivos .vhd se almacenan como blobs de página en el almacenamiento de Azure. Fuera de Azure, los discos duros virtuales pueden utilizar un formato VHD o VHDX. Se pueden fijar, expandir dinámicamente o diferenciar. Azure admite discos fijos, en formato VHD. Para obtener más información, consulte [Acerca de los discos duros virtuales en Azure](http://msdn.microsoft.com/library/azure/dn790344.aspx)  

6. Vuelva al panel para comprobar que el disco de datos vacío se ha asociado correctamente a la máquina virtual. Debe aparecer en la lista **Discos** después Disco del SO.

	![Attach empty disk](./media/virtual-machines-windows-tutorial/disklistwithdatadisk.png)

	Cuando se conecta un disco de datos, este está sin conexión y sin iniciar. Antes de que pueda usarlo para almacenar datos, tendrá que iniciar sesión en la máquina virtual e inicializarlo.

7. Conéctese e inicie sesión en la máquina virtual usando los pasos que se mencionaron en la sección anterior, [Inicio de sesión en una máquina virtual después de su creación](#logon).

8. Después de iniciar sesión en la máquina virtual, abra el **Administrador del servidor**. En el panel izquierdo, seleccione **Servicios de archivos y almacenamiento**.

	![Expand File and Storage Services in Server Manager](./media/virtual-machines-windows-tutorial/fileandstorageservices.png)

9. Seleccione **Discos** en el menú expandido.

	![Expand File and Storage Services in Server Manager](./media/virtual-machines-windows-tutorial/selectdisks.png)	
	
10.	La sección **Discos** muestra el disco 0, el disco 1 y el disco 2. El disco 0 es el disco del sistema operativo, el disco 1 es un disco de recursos temporales (que no debe usarse para almacenamiento de datos) y el disco 2 es el disco de datos que ha conectado a la máquina virtual. El disco de datos tiene una capacidad de 5 GB, en función de lo que se especificó al conectar el disco. Haga clic con el botón secundario en el disco 2 y  seleccione **Inicializar**.

	![Start initialization](./media/virtual-machines-windows-tutorial/initializedisk.png)

11. Haga clic en **Sí**.

	![Continue initialization](./media/virtual-machines-windows-tutorial/yesinitialize.png)

12. Haga clic con el botón secundario de nuevo en el disco 2 y seleccione **Nuevo volumen**. 

	![Create the volume](./media/virtual-machines-windows-tutorial/initializediskvolume.png)

13. Complete el asistente usando los valores predeterminados que se proporcionan. Cuando haya finalizado el asistente, la sección **Volúmenes** mostrará el nuevo volumen. El disco está ahora conectado y listo para almacenar los datos. 

	![Create the volume](./media/virtual-machines-windows-tutorial/newvolumecreated.png)
	
## Pasos siguientes 

Para obtener más información acerca de la configuración de máquinas virtuales en Azure, consulte los artículos siguientes:

[Cómo conectar máquinas virtuales con una red virtual o un servicio de nube](../cloud-services-connect-virtual-machine/)

[Creación y carga de un VHD de Windows Server en Azure](../virtual-machines-create-upload-vhd-windows-server/)

[Administración de la disponibilidad de las máquinas virtuales](../manage-availability-virtual-machines/)

[Acerca de los ajustes de configuración de máquinas virtuales en Azure](http://msdn.microsoft.com/library/azure/dn763935.aspx)

[VÍDEO: Introducción a los VHD - Qué ocurre en realidad](http://azure.microsoft.com/documentation/videos/getting-started-with-azure-virtual-machines)

[VÍDEO: P+F con Mark Russinovich - ¿Microsoft Azure se ejecuta en Windows?](http://azure.microsoft.com/documentation/videos/mark-russinovich-windows-on-azure)

[VÍDEO: Incorporación de una nueva máquina virtual a una granja web haciendo imágenes reutilizables](http://azure.microsoft.com/documentation/videos/adding-virtual-machines-web-farm)

[VÍDEO: Incorporación de unidades de discos duros virtuales, cuentas de almacenamiento y escalado de máquinas virtuales](http://azure.microsoft.com/documentation/videos/adding-drives-scaling-virtual-machines)

[VÍDEO: Scott Guthrie empieza por las máquinas virtuales](http://azure.microsoft.com/documentation/videos/virtual-machines-scottgu)

[VÍDEO: Conceptos básicos de discos y almacenamiento con máquinas virtuales de Azure](http://azure.microsoft.com/documentation/videos/storage-and-disks-virtual-machines)



[Acerca de las máquinas virtuales en Azure]: #virtualmachine
[Creación de una máquina virtual]: #custommachine
[Inicio de sesión en una máquina virtual después de su creación]: #logon
[Acoplamiento de un disco de datos a la nueva máquina virtual]: #attachdisk
[Establecimiento de la comunicación con la máquina virtual]: #endpoints

<!--HONumber=47-->
