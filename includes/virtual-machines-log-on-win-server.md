<properties services="virtual-machines" title="How to Log on to a Virtual Machine Running Windows Server" authors="cynthn" solutions="" manager="timlt" editor="tysonn" />

4. Al hacer clic en **Conectar** se crea y descarga un archivo de Protocolo de escritorio remoto (archivo .rdp). Haga clic en **Abrir** para utilizar este archivo.

	![Captura de pantalla del archivo .rdp descargado.](./media/virtual-machines-log-on-win-server/open-rdp.png)

5. Aparece una advertencia que indica que el archivo .rdp procede de un publicador desconocido. Esto es normal. En la ventana de Escritorio remoto, haga clic en **Conectar** para continuar.

	![Captura de pantalla de una advertencia sobre un publicador desconocido.](./media/virtual-machines-log-on-win-server/rdp-warn.png)

6. En la ventana de Seguridad de Windows, escriba las credenciales de la cuenta en la máquina virtual y luego haga clic en **Aceptar**.

 	En la mayoría de los casos, las credenciales son el nombre de usuario y la contraseña de la cuenta local que especificó al crear la máquina virtual. En este caso, el dominio es el nombre de la máquina virtual y se escribe como *nombredevm*&#92;*nombredeusuario*.
	
	Si la máquina virtual pertenece a un dominio de su organización, asegúrese de que el nombre de usuario incluya el nombre del dominio con el formato *Dominio*&#92;*Nombredeusuario*. La cuenta también debe estar en el grupo Administradores o tener privilegios de acceso remoto a la máquina virtual.
	
	Si la máquina virtual es un controlador de dominio, escriba el nombre de usuario y la contraseña de una cuenta de administrador de dominio para ese dominio.

7.	Haga clic en **Sí** para comprobar la identidad de la máquina virtual y finalizar el inicio de sesión.

	![Captura de pantalla que muestra un mensaje sobre la comprobación de la identidad de la máquina virtual.](./media/virtual-machines-log-on-win-server/cert-warning.png)

<!---HONumber=AcomDC_0504_2016-->