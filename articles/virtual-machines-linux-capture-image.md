<properties linkid="manage-linux-howto-capture-an-image" urlDisplayName="Capture an image" pageTitle="Capture an image of a virtual machine running Linux" metaKeywords="Azure Linux vm, Linux vm" description="Learn how to capture an image of an Azure virtual machine (VM) running Linux. " metaCanonical="" services="virtual-machines" documentationCenter="" title="How to Capture an Image of a Virtual Machine Running Linux" authors="kathydav" solutions="" manager="jeffreyg" editor="tysonn" />

Captura de una imagen de una máquina virtual que ejecuta Linux
==============================================================

Puede utilizar imágenes de la galería de imágenes para crear fácilmente máquinas virtuales, o bien, puede capturar y utilizar sus propias imágenes para crear máquinas virtuales personalizadas. Una imagen es un archivo de disco duro virtual (.vhd) que se utiliza como plantilla para crear una máquina virtual. Una imagen es una plantilla porque no tiene una configuración específica como una máquina virtual configurada, tal como el nombre del host y la configuración de la cuenta de usuario. Si desea crear varias máquinas virtuales configuradas de la misma manera, puede capturar una imagen de una máquina virtual configurada y utilizar esa imagen como plantilla.

1.  Siga los pasos que aparecen en [Inicio de sesión en una máquina virtual con Linux](../virtual-machines-linux-how-to-log-on) para conectarse a la máquina virtual.

2.  En la ventana SSH, escriba el siguiente comando y, a continuación, especifique la contraseña para la cuenta que creó en la máquina virtual. Observe que el resultado de `waagent` puede variar levemente según la versión de esta utilidad:

    `sudo waagent -deprovision`

    ![Desaprovisionamiento de la máquina virtual](./media/virtual-machines-linux-capture-image/LinuxDeprovision.png)

3.  Escriba **y** para continuar.

    ![Desaprovisionamiento correcto de la máquina virtual](./media/virtual-machines-linux-capture-image/LinuxDeprovision2.png)

4.  Escriba **Exit** para cerrar el cliente SSH.

5.  En el [Portal de administración](http://manage.windowsazure.com), seleccione la máquina virtual y, a continuación, haga clic en **Shutdown**.

    ![Apagado de la máquina virtual](./media/virtual-machines-linux-capture-image/ShutdownVM.png)

6.  Haga clic en **Yes** para aceptar que se le siga facturando por la máquina virtual cuando no esté en ejecución.

7.  Cuando la máquina virtual se detenga, en la barra de comandos haga clic en **Capture**.

    ![Captura de una imagen de la máquina virtual](./media/virtual-machines-linux-capture-image/CaptureVM.png)

    Aparece el cuadro de diálogo **Capture Virtual Machine**.

    ![Escribir los detalles de la captura](./media/virtual-machines-linux-capture-image/CaptureLinux.png)

8.  En **Image Name**, escriba el nombre de la imagen nueva.

9.  Es necesario *desaprovisionar* todas las imágenes de Linux mediante la ejecución del comando `waagent` con la opción `-deprovision`. Haga clic en **I have run the de-provision command on the Virtual Machine** para indicar que el sistema operativo está preparado para ser una imagen.

10. Haga clic en la marca de verificación para capturar la imagen.

    La nueva imagen ahora está disponible en **Images**. La máquina virtual se elimina una vez capturada la imagen.

    ![Captura correcta de la imagen](./media/virtual-machines-linux-capture-image/CaptureSuccess.png)

    Cuando crea una máquina virtual con el método **From Gallery**, puede utilizar la imagen que capturó haciendo clic en **My Images** en la página **Select the virtual machine operating system**.


