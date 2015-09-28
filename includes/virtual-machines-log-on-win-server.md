<properties services="virtual-machines" title="How to Log on to a Virtual Machine Running Windows Server" authors="KBDAzure" solutions="" manager="timlt" editor="tysonn" />

4. Al hacer clic en **Conectar** se crea y descarga un archivo de Protocolo de escritorio remoto (archivo .rdp). Haga clic en **Abrir** para utilizar este archivo.

5. En la ventana de Escritorio remoto, haga clic en **Conectar** para continuar.

	![Continuar con la conexión](./media/virtual-machines-log-on-win-server/connectpublisher.png)

6. En la ventana de Seguridad de Windows, escriba las credenciales de la cuenta administrativa en la máquina virtual y luego haga clic en **Aceptar**.

 	>[AZURE.TIP]En la mayoría de los casos, usará el nombre de usuario y contraseña que se especificó al crear la máquina virtual. Compruebe el nombre de usuario para asegurarse de que tiene la información de dominio correcta:
	>
	>- Si la máquina virtual pertenece a un dominio de su organización, asegúrese de que el nombre de usuario incluye el nombre de ese dominio.
	>- Si la máquina virtual no pertenece a un dominio, quite cualquier información de dominio cuya primera línea comience con '' o use el nombre de la VM como nombre de dominio. Por ejemplo, `\MyUserName` o `MyTestVM\MyUserName`.
	>- Si la máquina virtual es un controlador de dominio, escriba el nombre de usuario y la contraseña de una cuenta de administrador de dominio para ese dominio.

7.	Haga clic en **Sí** para comprobar la identidad de la máquina virtual y finalizar el inicio de sesión.

	![Verificar la identidad de la máquina](./media/virtual-machines-log-on-win-server/connectverify.png)

<!---HONumber=Sept15_HO3-->