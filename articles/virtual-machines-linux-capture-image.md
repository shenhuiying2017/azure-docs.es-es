<properties linkid="manage-linux-howto-capture-an-image" urlDisplayName="Capture an image" pageTitle="Capture an image of a virtual machine running Linux" metaKeywords="Azure Linux vm, Linux vm" description="Learn how to capture an image of an Azure virtual machine (VM) running Linux. " metaCanonical="" services="virtual-machines" documentationCenter="" title="How to Capture an Image of a Virtual Machine Running Linux" authors="kathydav" solutions="" manager="timlt" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="kathydav"></tags>

# Cómo capturar una máquina virtual Linux para usarla como plantilla.

En este artículo se muestra como puede capturar una máquina virtual de Linux para usarla como plantilla en la creación de otras máquinas virtuales. Esta plantilla de máquina virtual incluye el disco del sistema operativo y cualquier otro disco de datos que acompañe a la máquina virtual. No incluye la configuración de red, por lo que deberá configurarla usted mismo cuando cree las otras máquinas virtuales que utilicen la plantilla.

Después de capturar la máquina virtual, estará disponible en **Mis imágenes** cuando cree una máquina virtual. El archivo de imagen se almacena como un VHD en su cuenta de almacenamiento. Para obtener más información sobre imágenes, consulte [Administrar discos e imágenes][].

## Antes de empezar

Para seguir estos pasos se supone que ya ha creado un máquina virtual Azure y ha configurado el sistema operativo, incluida la anexión de cualquier disco de datos. Si no ha realizado esto todavía, siga estas instrucciones:

-   [Creación de una máquina virtual personalizada][]
-   [Acoplamiento de un disco de datos a una máquina virtual][]

## Capture la máquina virtual

1.  Establezca conexión con la máquina virtual y haga clic en **Conectar** en la barra de comandos. Para obtener los detalles, vea [Inicio de sesión en una máquina virtual con Linux][].

2.  En la ventana SSH, escriba el siguiente comando y, a continuación, especifique la contraseña para la cuenta que creó en la máquina virtual. Observe que el resultado de `waagent` puede variar ligeramente según la versión de esta utilidad:

    `sudo waagent -deprovision`

    ![Desaprovisionamiento de la máquina virtual][]

3.  Escriba **y** para continuar.

    ![Desaprovisionamiento correcto de la máquina virtual][]

4.  Escriba **Exit** para cerrar el cliente SSH.

5.  En el [Portal de administración][], seleccione la máquina virtual y después haga clic en **Shut down**.

6.  Cuando la máquina virtual se detenga, en la barra de comandos haga clic en **Capture** para abrir el cuadro de diálogo **Capture the Virtual Machine**.

7.  En **Image Name**, escriba un nombre para la nueva imagen.

8.  Es necesario *desaprovisionar* todas las imágenes de Linux mediante la ejecución del comando `waagent` con la opción `-deprovision`. Haga clic en **I have run waagent-deprovision on the virtual machine** para indicar que el sistema operativo está preparado para ser una imagen.

9.  Haga clic en la marca de verificación para capturar la imagen.

    La nueva imagen ahora está disponible en **Images**. La máquina virtual se elimina una vez capturada la imagen.

    ![Captura correcta de la imagen][]

## Pasos siguientes

La imagen está lista para ser utilizada como plantilla para crear máquinas virtuales. Para ello, cree una máquina virtual personalizada con el método **De la galería** y seleccione la imagen que acaba de crear. Para obtener instrucciones, consulte [Creación de una máquina virtual personalizada][].

  [Administrar discos e imágenes]: http://go.microsoft.com/fwlink/p/?LinkId=397536
  [Creación de una máquina virtual personalizada]: ../virtual-machines-create-custom/
  [Acoplamiento de un disco de datos a una máquina virtual]: ../storage-windows-attach-disk/
  [Inicio de sesión en una máquina virtual con Linux]: ../virtual-machines-linux-how-to-log-on
  [Desaprovisionamiento de la máquina virtual]: ./media/virtual-machines-linux-capture-image/LinuxDeprovision.png
  [Desaprovisionamiento correcto de la máquina virtual]: ./media/virtual-machines-linux-capture-image/LinuxDeprovision2.png
  [Portal de administración]: http://manage.windowsazure.com
  [Captura correcta de la imagen]: ./media/virtual-machines-linux-capture-image/VMCapturedImageAvailable.png
