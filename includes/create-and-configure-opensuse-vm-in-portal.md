1. Inicie sesión en el [Portal de Azure clásico](http://manage.windowsazure.com).  
2. En la barra de comandos que se encuentra en la parte inferior de la ventana, haga clic en **Nuevo**.
3. En **Proceso**, haga clic en **Máquina virtual** y, después, en **De la galería**.
   
    ![Crear una máquina virtual][Image1]
4. En el grupo **SUSE** , seleccione una imagen de máquina virtual de OpenSUSE y luego haga clic en la flecha para continuar.
5. En la primera página de **Configuración de máquina virtual** :
   
   * Escriba un **nombre de máquina virtual**, como "testlinuxvm". El nombre debe contener entre 3 y 15 caracteres, puede contener solo letras, números y guiones y debe comenzar con una letra y terminar con una letra o un número.
   * Compruebe el **Nivel** y seleccione un **Tamaño**. El nivel determina los tamaños entre los que elegir. El tamaño afecta al coste de usarla, así como a algunas opciones de configuración como el número de discos de datos que se pueden acoplar. Para obtener más información, consulte [Tamaños de máquinas virtuales](../articles/virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
   * Escriba un **Nuevo nombre de usuario**, o acepte el predeterminado, **azureuser**. Este nombre se agrega al archivo de lista de Sudoers.
   * Decida qué tipo de **Autenticación** desea usar. Para obtener instrucciones generales sobre contraseñas, consulte [Contraseñas seguras](http://msdn.microsoft.com/library/ms161962.aspx).
6. En la página siguiente **Configuración de máquina virtual** :
   
   * Use el valor predeterminado de **Crear un nuevo servicio en la nube**.
   * En el cuadro **Nombre DNS** , escriba un nombre DNS válido para utilizarlo como parte de la dirección, por ejemplo, "testlinuxvm".
   * En el cuadro **Región/Grupo de afinidad/Red virtual** , seleccione una región donde se hospedará esta imagen virtual.
   * En **Puntos de conexión**, mantenga el punto de conexión de SSH. Puede agregar otros ahora, o agregarlos, cambiarlos o eliminarlos una vez creada la máquina virtual.
     
     > [!NOTE]
     > Si desea que una máquina virtual use una red virtual, **debe** especificar la red virtual cuando cree la máquina virtual. No puede agregar una máquina virtual a una red virtual después de crear la máquina virtual. Para más información, consulte [Información general sobre Virtual Network](../articles/virtual-network/virtual-networks-overview.md).
     > 
     > 
7. En la última página **Configuración de máquina virtual** , mantenga la configuración predeterminada y, luego, haga clic en la marca de verificación para finalizar.

El portal muestra la nueva máquina virtual en **Máquinas virtuales**. Mientras se notifique el estado **(Aprovisionando)**, la máquina virtual se está configurando. Cuando se notifique el estado **Ejecutando**, puede continuar al paso siguiente.

## <a name="connect-to-the-virtual-machine"></a>Conexión a una máquina virtual
Usará SSH o PuTTY para conectarse a la máquina virtual, en función del sistema operativo que se ejecute en el equipo desde el que se va a conectar:

* Desde un equipo que ejecuta Linux, use SSH. En el símbolo del sistema, escriba:
  
    `$ ssh newuser@testlinuxvm.cloudapp.net -o ServerAliveInterval=180`
  
    Escriba la contraseña del usuario.
* Desde un equipo que ejecute Linux, use PuTTY. Si no lo ha instalado, descárguelo desde la [página de descarga de PuTTY][PuTTYDownload].
  
    Guarde **putty.exe** en un directorio de su equipo. Abra un símbolo del sistema, vaya a esa carpeta y ejecute **putty.exe**.
  
    Escriba el nombre de host, como "testlinuxvm.cloudapp.net" y escriba "22" en **Puerto**.
  
    ![Pantalla de PuTTY][Image6]  

## <a name="update-the-virtual-machine-optional"></a>Actualización de la máquina virtual (opcional)
1. Después de conectarse a la máquina virtual, tiene la opción de instalar revisiones y actualizaciones del sistema. Para ejecutar la actualización, ejecute:
   
    `$ sudo zypper update`
2. Seleccione **Software** y, después, **Actualización en línea** para enumerar las actualizaciones disponibles. Seleccione **Aceptar** para iniciar la instalación y aplicar todas las revisiones nuevas disponibles (salvo las que son opcionales).
3. Una vez completada la instalación, seleccione **Finalizar**.  El sistema estará ahora actualizado.

[PuTTYDownload]: http://www.puttyssh.org/download.html

[Image1]: ./media/create-and-configure-opensuse-vm-in-portal/CreateVM.png

[Image6]: ./media/create-and-configure-opensuse-vm-in-portal/putty.png
