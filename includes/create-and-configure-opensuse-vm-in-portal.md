**Importante**: si quiere que la máquina virtual use una red virtual, asegúrese de especificar la red virtual cuando cree la máquina virtual. Puede configurarse una máquina virtual para que se una a una red virtual solo cuando se cree la máquina virtual. Para obtener más información sobre las redes virtuales, consulte [Información general sobre redes virtuales de Azure](http://go.microsoft.com/fwlink/p/?LinkID=294063).


1. Inicie sesión en el [Portal de administración de Azure][AzurePreviewPortal] con la cuenta de Azure.

2. En el Portal de administración, en la parte inferior izquierda de la página web, haga clic en **+Nuevo**, en **Máquina virtual** y, a continuación, en **Desde la galería**.

	![Crear una máquina virtual][Image1]

3. En el grupo **SUSE**, seleccione una imagen de máquina virtual OpenSUSE y, a continuación, haga clic en la flecha de avance en la parte inferior derecha de la página.


4. Rellene la primera página **Configuración de la máquina virtual** o compruebe la configuración:

	- Escriba un **nombre de máquina virtual**, como "testlinuxvm".
	- Compruebe el **Nivel** y seleccione un **Tamaño**. El nivel determina los tamaños entre los que elegir.
	- Escriba un **Nombre de usuario nuevo**, como "newuser", que se agregará al archivo de lista de Sudoers.
	- Decida qué tipo de **Autenticación** desea usar. Para obtener instrucciones generales sobre contraseñas, consulte [Contraseñas seguras](http://msdn.microsoft.com/library/ms161962.aspx).


5. Rellene la siguiente página de **Configuración de la máquina virtual** o compruebe la configuración:
	- Use el valor predeterminado de **Crear un nuevo servicio en la nube**.
	- En el cuadro **Nombre DNS**, escriba un nombre DNS válido para utilizarlo como parte de la dirección, como "testlinuxvm".
	- En el cuadro **Región/Grupo de afinidad/Red virtual**, seleccione una región donde se hospedará esta imagen virtual.

6.	Haga clic en la flecha Siguiente para finalizar; espere mientras Azure prepara la máquina virtual hasta que la inicie.

##Conexión a una máquina virtual
Usará SSH o PuTTY para conectarse a la máquina virtual, en función del sistema operativo que ejecute en el equipo:

- Si usa Linux para conectarse en la máquina virtual, utilice SSH. En el símbolo del sistema, ejecute: 

	`$ ssh newuser@testlinuxvm.cloudapp.net -o ServerAliveInterval=180`
	
	Escriba la contraseña del usuario.

- Si usa Windows para conectarse en la máquina virtual, utilice PuTTY. PuTTY puede descargarse desde la [página de descarga de PuTTY][PuTTYDownLoad].

	Descargue y guarde **putty.exe** en un directorio del equipo. Abra un símbolo del sistema, vaya hasta la carpeta correspondiente y ejecute **putty.exe**.

	Escriba el nombre de host, como "testlinuxvm.cloudapp.net" y escriba "22" en **Puerto**.

	![Pantalla de PuTTY][Image6]

##Actualización de la máquina virtual (opcional)
1. Después de conectarse a la máquina virtual, tiene la opción de instalar revisiones y actualizaciones del sistema. Para ejecutar la actualización, ejecute:

	`$ sudo zypper update`

2. Seleccione **Software** y, a continuación, **Actualización en línea** para enumerar las actualizaciones disponibles. Seleccione **Aceptar** para iniciar la instalación y aplicar todas las revisiones nuevas disponibles (salvo las que son opcionales).

3. Una vez completada la instalación, seleccione **Finalizar**. El sistema estará ahora actualizado.

[PuTTYDownload]: http://www.puttyssh.org/download.html
[AzurePreviewPortal]: http://manage.windowsazure.com

[Image1]: ./media/create-and-configure-opensuse-vm-in-portal/CreateVM.png

[Image6]: ./media/create-and-configure-opensuse-vm-in-portal/putty.png

<!---HONumber=July15_HO1-->