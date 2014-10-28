<properties linkid="manage-windows-howto-logon" urlDisplayName="Log on to a VM" pageTitle="Log on to a virtual machine running Windows Server" metaKeywords="Azure logging on vm, vm portal" description="Learn to use the Azure Management Portal to log on to a virtual machine running Windows Server." metaCanonical="" services="virtual-machines" documentationCenter="" title="How to Log on to a Virtual Machine Running Windows Server" authors="kathydav" solutions="" manager="timlt" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-windows" ms.devlang="na" ms.topic="article" ms.date="08/05/2014" ms.author="kathydav"></tags>

# Inicio de sesión en una máquina virtual con Windows Server

En las máquinas virtuales con el sistema operativo Windows Server, use el botón Connect del Portal de administración para iniciar una Conexión a Escritorio remoto.

> [WACOM.NOTE] Si necesita restablecer la nombre de usuario o la contraseña, o para habilitar RDP en la máquina virtual, puede utilizar la extensión [VMAccess][]. Para obtener consejos acerca de los requisitos y la solución de problemas, vea [Conectar a una máquina virtual de Azure con RDP o SSH][].

1.  Si no lo ha hecho todavía, inicie sesión en el [Portal de administración de Azure][].

2.  Haga clic en **Máquinas virtuales** y, a continuación, seleccione la máquina virtual correspondiente.

3.  En la barra de comandos, haga clic en **Connect**.

    ![Iniciar sesión en la nueva máquina virtual][]

4.  Haga clic en **Open** para usar el archivo de protocolo de escritorio remoto que se creó automáticamente para la máquina virtual.

5.  Haga clic en **Connect** para continuar.

    ![Continuar con la conexión][]

6.  Escriba el nombre de usuario y la contraseña de la cuenta administrativa en la máquina virtual y, a continuación, haga clic en **OK**. Este es el nombre de usuario y la contraseña que especificó al crear la máquina virtual, a no ser que la máquina virtual sea ahora un controlador de dominio. En ese caso, escriba el nombre de usuario y la contraseña de una cuenta de administrador de dominio para dicho dominio.

7.  Haga clic en **Yes** para verificar la identidad de la máquina virtual.

    ![Verificar la identidad de la máquina][]

    Ahora puede trabajar con la máquina virtual igual que hace con cualquier otro servidor.

  [VMAccess]: http://go.microsoft.com/fwlink/p/?LinkId=396856
  [Conectar a una máquina virtual de Azure con RDP o SSH]: http://go.microsoft.com/fwlink/p/?LinkId=398294
  [Portal de administración de Azure]: http://manage.windowsazure.com
  [Iniciar sesión en la nueva máquina virtual]: ./media/virtual-machines-log-on-windows-server/connectwindows.png
  [Continuar con la conexión]: ./media/virtual-machines-log-on-windows-server/connectpublisher.png
  [Verificar la identidad de la máquina]: ./media/virtual-machines-log-on-windows-server/connectverify.png
