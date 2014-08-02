<properties linkid="manage-linux-howto-logon-linux-vm" urlDisplayName="Log on to a VM" pageTitle="Log on to a virtual machine running Linux in Azure" metaKeywords="Azure Linux vm, Linux SSH" description="Learn how to log on to an Azure virtual machine running Linux by using a Secure Shell (SSH) client." metaCanonical="" services="virtual-machines" documentationCenter="" title="How to Log on to a Virtual Machine Running Linux" authors="" solutions="" manager="" editor="" />

Inicio de sesión en una máquina virtual con Linux
=================================================

Para una máquina virtual con el sistema operativo Linux, utiliza un cliente Secure Shell (SSH) para iniciar sesión.

Debe instalar un cliente de SSH en el equipo que desea usar para iniciar sesión en la máquina virtual. Existen muchos programas cliente de SSH donde elegir. Las siguientes son algunas posibles opciones:

-   Si va a usar un equipo que está ejecutando un sistema operativo Windows, es posible que desee usar un cliente de SSH, como por ejemplo PuTTY. Para obtener más información, consulte la [página de descarga de PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) (en inglés).
-   Si va a usar un equipo que está ejecutando un sistema operativo Linux, es posible que desee usar un cliente de SSH, como por ejemplo OpenSSH. Para obtener más información, consulte [OpenSSH](http://www.openssh.org/).

Este procedimiento le muestra cómo usar el programa PuTTY para tener acceso a la máquina virtual.

1.  Busque el nombre de host (**Host Name**) y la información de puerto (**Port information**) en el [Portal de administración](http://manage.windowsazure.com). Puede encontrar la información que necesita en el panel de la máquina virtual. Haga clic en el nombre de la máquina virtual y busque **SSH Details** en la sección **Quick Glance** del panel.

    ![Obtener detalles de SSH](./media/virtual-machines-linux-how-to-log-on/sshdetails.png)

2.  Abra el programa PuTTY.

3.  Escriba el nombre de host y la información de puerto que recopiló del panel y, a continuación, haga clic en **Open**.

    ![Abrir PuTTY](./media/virtual-machines-linux-how-to-log-on/putty.png)

4.  Inicie sesión en la máquina virtual usando la cuenta que especificó cuando se creó la máquina.

    ![Iniciar sesión en la nueva máquina virtual](./media/virtual-machines-linux-how-to-log-on/sshlogin.png)

    Ahora puede trabajar con la máquina virtual igual que hace con cualquier otro servidor.


