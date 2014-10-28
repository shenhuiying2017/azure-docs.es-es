<properties linkid="manage-linux-howto-logon-linux-vm" urlDisplayName="Log on to a VM" pageTitle="Log on to a virtual machine running Linux in Azure" metaKeywords="Azure Linux vm, Linux SSH" description="Learn how to log on to an Azure virtual machine running Linux by using a Secure Shell (SSH) client." metaCanonical="" services="virtual-machines" documentationCenter="" title="How to Log on to a Virtual Machine Running Linux" authors="kathydav" solutions="" manager="timlt" editor="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="kathydav"></tags>

# Inicio de sesión en una máquina virtual con Linux

Para una máquina virtual con el sistema operativo Linux, utilice un cliente Secure Shell (SSH) para iniciar sesión.

Debe instalar un cliente de SSH en el equipo que desea usar para iniciar sesión en la máquina virtual. Existen muchos programas cliente de SSH donde elegir. Las siguientes son algunas posibles opciones:

-   En un equipo que ejecute un sistema operativo Windows, quizás desee utilizar un cliente de SSH, como PuTTY. Para obtener más información, consulte la [página de descarga de PuTTY][] (en inglés).
-   En un equipo que ejecute un sistema operativo Linux, quizás desee utilizar un cliente de SSH, como OpenSSH. Para obtener más información, consulte [OpenSSH][].

> [WACOM.NOTE] Para obtener más consejos acerca de los requisitos y la solución de problemas, vea [Conectar a una máquina virtual de Azure con RDP o SSH][].

Este procedimiento le muestra cómo usar el programa PuTTY para tener acceso a la máquina virtual.

1.  Busque el nombre de host (**Host Name**) y la información de puerto (**Port information**) en el [Portal de administración][]. Puede encontrar la información que necesita en el panel de la máquina virtual. Haga clic en el nombre de la máquina virtual y busque **SSH Details** en la sección **Quick Glance** del panel.

    ![Obtener detalles de SSH][]

2.  Abra el programa PuTTY.

3.  Escriba el nombre de host y la información de puerto que recopiló del panel y, a continuación, haga clic en **Open**.

    ![Abrir PuTTY][]

4.  Inicie sesión en la máquina virtual usando la cuenta que especificó cuando se creó la máquina. De manera predeterminada el nombre de usuario es azureuser.

    ![Iniciar sesión en la nueva máquina virtual][]

> [WACOM.NOTE] La extensión VMAccess le puede ayudar a restablecer la contraseña o clave de SSH si la ha olvidado. Si ha olvidado el nombre de usuario, puede utilizar la extensión para crear otro nuevo con autoridad sudo. Para obtener instrucciones, vea [Utilizar la extensión VMAccess para restablecer las credenciales de inicio de sesión para Linux VM][].

Ahora puede trabajar con la máquina virtual igual que hace con cualquier otro servidor.

<!-- LINKS -->

  [página de descarga de PuTTY]: http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html
  [OpenSSH]: http://www.openssh.org/
  [Conectar a una máquina virtual de Azure con RDP o SSH]: http://go.microsoft.com/fwlink/p/?LinkId=398294
  [Portal de administración]: http://manage.windowsazure.com
  [Obtener detalles de SSH]: ./media/virtual-machines-linux-how-to-log-on/sshdetails.png
  [Abrir PuTTY]: ./media/virtual-machines-linux-how-to-log-on/putty.png
  [Iniciar sesión en la nueva máquina virtual]: ./media/virtual-machines-linux-how-to-log-on/sshlogin.png
  [Utilizar la extensión VMAccess para restablecer las credenciales de inicio de sesión para Linux VM]: http://go.microsoft.com/fwlink/p/?LinkId=512138
