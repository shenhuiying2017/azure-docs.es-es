<properties services="virtual-machines" title="How to Log on to a Virtual Machine Running Windows Server" authors="KBDAzure" solutions="" manager="timlt" editor="tysonn" />

1. Si aún no lo ha hecho, inicie sesión en el [Portal de administración de Azure](http://manage.windowsazure.com).

2. Haga clic en **Máquinas virtuales** y, a continuación, seleccione la máquina virtual correspondiente.

3. En la barra de comandos, haga clic en **Conectar**.

	![Iniciar sesión en la nueva máquina virtual](./media/virtual-machines-log-on-win-server/connectwindows.png)

4. Haga clic en **Abrir** para usar el archivo de protocolo de escritorio remoto que se creó automáticamente para la máquina virtual.
	
5. Haga clic en **Conectar** para continuar.

	![Continuar con la conexión](./media/virtual-machines-log-on-win-server/connectpublisher.png)

6. Escriba las credenciales de la cuenta administrativa en la máquina virtual y luego haga clic en **Aceptar**.

 >[AZURE.TIP]En la mayoría de los casos, usará el nombre de usuario y contraseña que se especificó al crear la máquina virtual. Compruebe el nombre de usuario para asegurarse de que tiene la información de dominio correcta:

>- Si la máquina virtual pertenece a un dominio de su organización, asegúrese de que el nombre de usuario incluye el nombre de ese dominio.
- Si la máquina virtual no pertenece a un dominio, quite cualquier información de dominio comenzando la línea con '' o use el nombre de la máquina virtual como el nombre de dominio. Por ejemplo, `\MyUserName` o `MyTestVM\MyUserName`. 
- Si la máquina virtual es un controlador de dominio, escriba el nombre de usuario y la contraseña de una cuenta de administrador de dominio para ese dominio.

Haga clic en **Sí** para comprobar la identidad de la máquina virtual.

![Verificar la identidad de la máquina](./media/virtual-machines-log-on-win-server/connectverify.png)

Ahora puede trabajar con la máquina virtual de forma remota.

<!---HONumber=58_postMigration-->