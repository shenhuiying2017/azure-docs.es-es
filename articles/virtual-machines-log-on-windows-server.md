<properties  linkid="manage-windows-howto-logon" urlDisplayName="Log on to a VM" pageTitle="Log on to a virtual machine running Windows Server" metaKeywords="Azure logging on vm, vm portal" description="Learn how to log on to a virtual machine running Windows Server 2008 R2 by using the Azure Management Portal." metaCanonical="" services="virtual-machines" documentationCenter="" title="How to Log on to a Virtual Machine Running Windows Server" authors="kathydav" solutions="" manager="jeffreyg" editor="tysonn" />

# Inicio de sesión en una máquina virtual con Windows Server

En las máquinas virtuales con el sistema operativo Windows Server, use el botón Connect del Portal de administración para iniciar una Conexión a Escritorio remoto.

1.  Si no lo ha hecho todavía, inicie sesión en el [Portal de administración de Azure][1].

2.  Haga clic en **Máquinas virtuales** y, a continuación, seleccione la máquina virtual correspondiente.

3.  En la barra de comandos, haga clic en **Connect**.
    
    ![Iniciar sesión en la nueva máquina
    virtual](./media/virtual-machines-log-on-windows-server/connectwindows.png)

4.  Haga clic en **Open** para usar el archivo de protocolo de escritorio remoto que se creó automáticamente para la máquina virtual.

5.  Haga clic en **Connect** para continuar con el proceso de conexión.
    
    ![Continuar con la
    conexión](./media/virtual-machines-log-on-windows-server/connectpublisher.png)

6.  Escriba el nombre de usuario y la contraseña de la cuenta administrativa en la máquina virtual y, a continuación, haga clic en **OK**.

7.  Haga clic en **Yes** para verificar la identidad de la máquina virtual.
    
    ![Verificar la identidad de la
    máquina](./media/virtual-machines-log-on-windows-server/connectverify.png)
    
    Ahora puede trabajar con la máquina virtual igual que hace con cualquier otro servidor.



[1]: http://manage.windowsazure.com