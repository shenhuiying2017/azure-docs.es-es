<properties linkid="manage-windows-howto-logon" urlDisplayName="Log on to a VM" pageTitle="Inicio de sesi&oacute;n en una m&aacute;quina virtual que ejecuta Windows Server" metaKeywords="Azure logging on vm, vm portal" description="Aprenda a iniciar sesi&oacute;n en una m&aacute;quina virtual que ejecuta Windows Server 2008 R2 utilizando el Portal de administraci&oacute;n de Azure." metaCanonical="" services="virtual-machines" documentationCenter="" title="Inicio de sesi&oacute;n en una m&aacute;quina virtual con Windows Server" authors="kathydav" solutions="" manager="dongill" editor="tysonn" />

> [WACOM.NOTE] Para conocer los requisitos y sugerencias de solución de problemas, consulte [Conexión con una máquina virtual de Azure con RDP o SSH][Conexión con una máquina virtual de Azure con RDP o SSH].

1.  Si no lo ha hecho todavía, inicie sesión en el [Portal de administración de Azure][Portal de administración de Azure].

2.  Haga clic en **Máquinas virtuales** y, a continuación, seleccione la máquina virtual correspondiente.

3.  En la barra de comandos, haga clic en **Connect**.

    ![Iniciar sesión en la nueva máquina virtual][Iniciar sesión en la nueva máquina virtual]

4.  Haga clic en **Abrir** para usar el archivo de protocolo de escritorio remoto que se creó automáticamente para la máquina virtual.

5.  Haga clic en **Conectar** para continuar con el proceso de conexión.

    ![Continuar con la conexión][Continuar con la conexión]

6.  Escriba el nombre de usuario y la contraseña de la cuenta administrativa en la máquina virtual y, a continuación, haga clic en **Aceptar**.

7.  Haga clic en **Sí** para verificar la identidad de la máquina virtual.

    ![Verificar la identidad de la máquina][Verificar la identidad de la máquina]

    Ahora puede trabajar con la máquina virtual igual que hace con cualquier otro servidor.

  [Conexión con una máquina virtual de Azure con RDP o SSH]: http://go.microsoft.com/fwlink/p/?LinkId=398294
  [Portal de administración de Azure]: http://manage.windowsazure.com
  [Iniciar sesión en la nueva máquina virtual]: ./media/virtual-machines-log-on-win-server/connectwindows.png
  [Continuar con la conexión]: ./media/virtual-machines-log-on-win-server/connectpublisher.png
  [Verificar la identidad de la máquina]: ./media/virtual-machines-log-on-win-server/connectverify.png
