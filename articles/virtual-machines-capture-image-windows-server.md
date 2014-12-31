<properties urlDisplayName="Capture an image" pageTitle="Captura de una imagen de una máquina virtual que ejecuta Windows Server" metaKeywords="Azure capture image vm, capturing vm" description="Learn how to capture an image of an Azure virtual machine (VM) running Windows Server 2008 R2. " metaCanonical="" services="virtual-machines" documentationCenter="" title="How to Capture an Image of a Virtual Machine Running Windows Server" authors="kathydav" solutions="" manager="timlt" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-windows" ms.devlang="na" ms.topic="article" ms.date="11/24/2014" ms.author="kathydav" />

#Cómo capturar una máquina virtual Windows para usarla como plantilla#

En este artículo se muestra cómo puede capturar una máquina virtual de Azure Con Windows para usarla como plantilla en la creación de otras máquinas virtuales. Esta plantilla de máquina virtual incluye el disco del sistema operativo y cualquier otro disco de datos conectado a la máquina virtual. No incluye la configuración de red, por lo que deberá configurarla usted mismo cuando cree las otras máquinas virtuales que utilicen la plantilla.

Azure trata esta plantilla como una imagen y la almacena en **Mis imágentes**. Aquí también están almacenadas todas las imágenes que ha cargado. Para obtener más información sobre las imágenes, consulte [Acerca de las imágenes de máquina virtual en Azure] [].

##Antes de empezar##

Para seguir estos pasos se supone que ya ha creado un máquina virtual Azure y ha configurado el sistema operativo, incluida la anexión de cualquier disco de datos. Si no ha realizado esto todavía, siga estas instrucciones:

- [Creación de una máquina virtual personalizada] []
- [Acoplamiento de un disco de datos a una máquina virtual] []

##Capture la máquina virtual##

1. Establezca conexión con la máquina virtual y haga clic en **Conectar** en la barra de comandos. Para obtener los detalles, consulte [Inicio de sesión en una máquina virtual con Windows Server] [].

2.	Abra una ventana de símbolo del sistema como administrador.


3.	Cambie el directorio a "%windir%\system32\sysprep" y, a continuación, ejecute sysprep.exe.


4. 	Se abre el cuadro de diálogo **Herramienta de preparación del sistema**. Haga lo siguiente:


	- En **Acción de limpieza del sistema**, seleccione **Escriba la experiencia inmediata con el sistema (OOBE)** y asegúrese de que la opción **Generalizar** está marcada. Para obtener más información acerca del uso Sysprep, consulte [Uso de Sysprep: (introducción)][].

	- En **Opciones de apagado**, seleccione **Apagar**.

	- Haga clic en **Aceptar**.

	![Run Sysprep](./media/virtual-machines-capture-image-windows-server/SysprepGeneral.png)

7.	Sysprep apaga la máquina virtual, lo que cambia su estado en el [Portal de administración](http://manage.windowsazure.com) a **Detenido**.


8.	Haga clic en **Máquinas virtuales** y, a continuación, seleccione la máquina virtual que desea capturar.

9.	En la barra de comandos, haga clic en **Capturar**.

	![Capture virtual machine](./media/virtual-machines-capture-image-windows-server/CaptureVM.png)

	Aparece el cuadro de diálogo **Capturar la máquina virtual**.

10.	En **Nombre de la imagen**, escriba un nombre para la nueva imagen.

11.	Antes de agregar una imagen de Windows Server al conjunto de imágenes personalizadas, ejecute Sysprep para generalizarla siguiendo las instrucciones de los pasos anteriores. Haga clic en **He ejecutado Sysprep en la máquina virtual** para indicar que ha realizado esta acción.

12.	Haga clic en la marca de verificación para capturar la imagen. Al capturar la imagen de una máquina virtual generalizada, la máquina virtual se elimina.

	La nueva imagen ahora está disponible en **Imágenes**.

	![Image capture successful](./media/virtual-machines-capture-image-windows-server/VMCapturedImageAvailable.png)

##Pasos siguientes##
La imagen está lista para ser utilizada como plantilla para crear máquinas virtuales. Para ello, cree una máquina virtual personalizada con el método **Desde la galería** y seleccione la imagen que acaba de crear. Para obtener instrucciones, consulte [Creación de una máquina virtual personalizada] [].

	
[Acerca de las imágenes de máquina virtual en Azure]: http://msdn.microsoft.com/en-us/library/azure/dn790290.aspx
[Creación de una máquina virtual personalizada]: ../virtual-machines-create-custom/
[Acoplamiento de un disco de datos a una máquina virtual]: ../storage-windows-attach-disk/
[Inicio de sesión en una máquina virtual con Windows Server]:http://www.windowsazure.com/es-es/manage/windows/how-to-guides/log-on-a-windows-vm/
[Uso de Sysprep (introducción)]:http://technet.microsoft.com/es-es/library/bb457073.aspx
[Ejecución de Sysprep.exe]: ./media/virtual-machines-capture-image-windows-server/SysprepCommand.png
[Definición de las opciones de Sysprep.exe]: ./media/virtual-machines-capture-image-windows-server/SysprepGeneral.png
[La máquina virtual se detiene]: ./media/virtual-machines-capture-image-windows-server/SysprepStopped.png
[Captura de una imagen de la máquina virtual]: ./media/virtual-machines-capture-image-windows-server/CaptureVM.png
[Escriba el nombre de la imagen]: ./media/virtual-machines-capture-image-windows-server/Capture.png
[Captura correcta de la imagen]: ./media/virtual-machines-capture-image-windows-server/CaptureSuccess.png
[Uso de la imagen capturada]: ./media/virtual-machines-capture-image-windows-server/MyImagesWindows.png

<!--HONumber=35_1-->
