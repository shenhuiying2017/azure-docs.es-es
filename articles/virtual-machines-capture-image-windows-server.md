<properties urlDisplayName="Capture an image" pageTitle="Captura de una imagen de una m&aacute;quina virtual que ejecuta Windows Server" metaKeywords="Azure capture image vm, capturing vm" description="Aprenda a capturar una imagen de una m&aacute;quina virtual de Azure que ejecuta Windows Server 2008 R2. " metaCanonical="" services="virtual-machines" documentationCenter="" title="Captura de una imagen de una m&aacute;quina virtual que ejecuta Windows Server" authors="kathydav" solutions="" manager="timlt" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-windows" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="kathydav" />

# Cómo capturar una máquina virtual Windows para usarla como plantilla.

En este artículo se muestra cómo puede capturar una máquina virtual Windows para usarla como plantilla en la creación de otras máquinas virtuales. Esta plantilla de máquina virtual incluye el disco del sistema operativo y cualquier otro disco de datos que acompañe a la máquina virtual. No incluye la configuración de red, por lo que deberá configurarla usted mismo cuando cree las otras máquinas virtuales que utilicen la plantilla.

Después de capturar la máquina virtual, estará disponible en **Mis imágenes** cuando cree una máquina virtual. El archivo de imagen se almacena como un VHD en su cuenta de almacenamiento. Para obtener más información sobre imágenes, consulte [Administrar discos e imágenes][Administrar discos e imágenes].

## Antes de empezar

Para seguir estos pasos se supone que ya ha creado un máquina virtual Azure y ha configurado el sistema operativo, incluida la anexión de cualquier disco de datos. Si no ha realizado esto todavía, siga estas instrucciones:

-   [Creación de una máquina virtual personalizada][Creación de una máquina virtual personalizada]
-   [Acoplamiento de un disco de datos a una máquina virtual][Acoplamiento de un disco de datos a una máquina virtual]

## Capture la máquina virtual

1.  Establezca conexión con la máquina virtual y haga clic en **Conectar** en la barra de comandos. Para obtener los detalles, consulte [Inicio de sesión en una máquina virtual con Windows Server][Inicio de sesión en una máquina virtual con Windows Server].

2.  Abra una ventana de símbolo del sistema como administrador.

3.  Cambie el directorio a `%windir%\system32\sysprep` y después ejecute sysprep.exe.

4.  Aparecerá el cuadro de diálogo **System Preparation Tool**. Haga lo siguiente:

    -   En **System Cleanup Action**, seleccione **Enter System Out-of-Box Experience (OOBE)** y asegúrese de que la casilla **Generalize** esté seleccionada. Para obtener más información acerca del uso Sysprep, consulte [How to Use Sysprep: An Introduction][How to Use Sysprep: An Introduction].

    -   En **Shutdown Options**, seleccione **Shutdown**.

    -   Haga clic en **OK**.

    ![Ejecute Sysprep][Ejecute Sysprep]

5.  Sysprep apaga la máquina virtual, lo que cambia su estado en el [Portal de administración][Portal de administración] a **Detenido**.

6.  Haga clic en **Máquinas virtuales** y, a continuación, seleccione la máquina virtual que desea capturar.

7.  En la barra de comandos, haga clic en **Capture**.

    ![Capture la máquina virtual][Capture la máquina virtual]

    Aparece el cuadro de diálogo **Capturar la máquina virtual**.

8.  En **Image Name**, escriba un nombre para la nueva imagen.

9.  Antes de agregar una imagen de Windows Server al conjunto de imágenes personalizadas, ejecute Sysprep para generalizarla siguiendo las instrucciones de los pasos anteriores. Haga clic en **I have run Sysprep on the virtual machine** para indicar que ha realizado esta acción.

10. Haga clic en la marca de verificación para capturar la imagen. Al capturar la imagen de una máquina virtual generalizada, la máquina virtual se elimina.

    La nueva imagen ahora está disponible en **Images**.

    ![Captura correcta de la imagen][Captura correcta de la imagen]

## Pasos siguientes

La imagen está lista para ser utilizada como plantilla para crear máquinas virtuales. Para ello, cree una máquina virtual personalizada con el método **De la galería** y seleccione la imagen que acaba de crear. Para obtener instrucciones, consulte [Creación de una máquina virtual personalizada][Creación de una máquina virtual personalizada].

  [Administrar discos e imágenes]: http://go.microsoft.com/fwlink/p/?LinkId=397536
  [Creación de una máquina virtual personalizada]: ../virtual-machines-create-custom/
  [Acoplamiento de un disco de datos a una máquina virtual]: ../storage-windows-attach-disk/
  [Inicio de sesión en una máquina virtual con Windows Server]: http://www.windowsazure.com/es-es/manage/windows/how-to-guides/log-on-a-windows-vm/
  [How to Use Sysprep: An Introduction]: http://technet.microsoft.com/es-es/library/bb457073.aspx
  [Ejecute Sysprep]: ./media/virtual-machines-capture-image-windows-server/SysprepGeneral.png
  [Portal de administración]: http://manage.windowsazure.com
  [Capture la máquina virtual]: ./media/virtual-machines-capture-image-windows-server/CaptureVM.png
  [Captura correcta de la imagen]: ./media/virtual-machines-capture-image-windows-server/VMCapturedImageAvailable.png
