<properties linkid="manage-windows-howto-capture-an-image" urlDisplayName="Capture an image" pageTitle="Capture an image of a virtual machine running Windows Server" metaKeywords="Azure capture image vm, capturing vm" description="Learn how to capture an image of an Azure virtual machine (VM) running Windows Server 2008 R2. " metaCanonical="" services="virtual-machines" documentationCenter="" title="How to Capture an Image of a Virtual Machine Running Windows Server" authors="kathydav" solutions="" manager="jeffreyg" editor="tysonn" />

Captura de una imagen de una máquina virtual que ejecuta Windows Server
=======================================================================

Puede utilizar imágenes de la galería de imágenes para crear fácilmente máquinas virtuales, o bien, puede capturar y utilizar sus propias imágenes para crear máquinas virtuales personalizadas. Una imagen es un archivo de disco duro virtual (.vhd) que se utiliza como plantilla para crear una máquina virtual. Es una plantilla porque no tiene la configuración específica de una máquina virtual configurada, tal como el nombre del equipo y la configuración de la cuenta de usuario. Si desea crear varias máquinas virtuales configuradas de la misma manera, puede capturar una imagen de una máquina virtual configurada y utilizar esa imagen como plantilla.

1.  Siga los pasos que aparecen en [Inicio de sesión en una máquina virtual con Windows Server](http://www.windowsazure.com/en-us/manage/windows/how-to-guides/log-on-a-windows-vm/) para conectarse a una máquina virtual.

2.  Abra una ventana de símbolo del sistema como administrador.

3.  Cambie el directorio a `%windir%\system32\sysprep` y, a continuación, ejecute sysprep.exe.

4.  Aparecerá el cuadro de diálogo **System Preparation Tool**.

    En **System Cleanup Action**, seleccione **Enter System Out-of-Box Experience (OOBE)** y asegúrese de que la casilla **Generalize** esté seleccionada. Para obtener más información acerca del uso Sysprep, consulte [How to Use Sysprep: An Introduction](http://technet.microsoft.com/en-us/library/bb457073.aspx).

5.  En **Shutdown Options**, seleccione **Shutdown**.

6.  Haga clic en **OK**.

7.  Sysprep apaga la máquina virtual, lo que cambia el estado de la máquina a **Stopped** en el [Portal de administración](http://manage.windowsazure.com).

8.  Haga clic en **Máquinas virtuales** y, a continuación, seleccione la máquina virtual que desea capturar.

9.  En la barra de comandos, haga clic en **Capture**.

    Aparecerá el cuadro de diálogo **Capture an Image from a Virtual Machine**.

10. En **Image Name**, escriba un nombre para la nueva imagen.

11. Antes de agregar una imagen de Windows Server al conjunto de imágenes personalizadas, ejecute Sysprep para generalizarla siguiendo las instrucciones de los pasos anteriores. Haga clic en **I have run Sysprep on the virtual machine** para indicar que ha realizado esta acción.

12. Haga clic en la marca de verificación para capturar la imagen. Al capturar la imagen de una máquina virtual, la máquina se elimina.

    La nueva imagen ahora está disponible en **Images**.

    Cuando se crea una máquina virtual mediante el método **From Gallery**, puede usarse la imagen capturada haciendo clic en **My Images** en la página **Choose an Image**.


