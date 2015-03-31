<properties services="virtual-machines" title="Inicio de sesión en una máquina virtual con Windows Server" authors="KBDAzure" solutions="" manager="timlt" editor="tysonn" />

>[AZURE.NOTE] Si necesita restablecer el nombre de usuario o la contraseña, o habilitar RDP en la máquina virtual, puede usar la extensión [VMAccess](http://go.microsoft.com/fwlink/p/?LinkId=396856). Para comprobar los requisitos u obtener sugerencias para solucionar problemas de inicio de sesión, vea [Conectarse a una máquina virtual de Azure con RDP o SSH](http://go.microsoft.com/fwlink/p/?LinkId=398294).

1. Si aún no lo ha hecho, inicie sesión en el [Portal de administración de Azure](http://manage.windowsazure.com).

2. Haga clic en **Máquinas virtuales** y, a continuación, seleccione la máquina virtual correspondiente.

3. En la barra de comandos, haga clic en **Conectar**.

	![Log on to the virtual machine](./media/virtual-machines-log-on-win-server/connectwindows.png)

4. Haga clic en **Abrir** para usar el archivo de protocolo de escritorio remoto que se creó automáticamente para la máquina virtual.
	
5. Haga clic en **Conectar** para continuar.

	![Continue with connecting](./media/virtual-machines-log-on-win-server/connectpublisher.png)

6. Escriba las credenciales de la cuenta administrativa en la máquina virtual y luego haga clic en **Aceptar**. 

 >[AZURE.TIP] En la mayoría de los casos, usará el nombre de usuario y contraseña que se especificó al crear la máquina virtual. Compruebe el nombre de usuario para asegurarse de que tiene la información de dominio correcta:

>- Si la máquina virtual pertenece a un dominio de su organización, asegúrese de que el nombre de usuario incluye el nombre de ese dominio.
- Si la máquina virtual no pertenece a un dominio, quite cualquier información de dominio comenzando la línea con "\" o use el nombre de la máquina virtual como el nombre de dominio. Por ejemplo, `\MyUserName` o  `MyTestVM\MyUserName`. 
- Si la máquina virtual es un controlador de dominio, escriba el nombre de usuario y la contraseña de una cuenta de administrador de dominio para ese dominio.

Haga clic en **Sí** para verificar la identidad de la máquina virtual.

![Verify the identity of the machine](./media/virtual-machines-log-on-win-server/connectverify.png)

Ahora puede trabajar con la máquina virtual de forma remota.


<!--HONumber=47-->
