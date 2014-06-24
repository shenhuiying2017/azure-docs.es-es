<properties  writer="kathydav" editor="tysonn" manager="jeffreyg" />

**Nota**: en este artículo se crea una máquina virtual que no está
conectada a una red virtual. Si desea que su máquina virtual use una red
virtual para permitir la conexión a las máquinas virtuales directamente
mediante un nombre de host o que configure conexiones entre locales,
utilice el método **Desde galería** y especifique la red virtual cuando
cree la máquina virtual. Para obtener más información acerca de redes
virtuales, consulte [Información general sobre redes virtuales de
Azure][1].

1.  Inicie sesión en el Portal de administración de Azure con la cuenta
    de Azure.
2.  En el Portal de administración, en la parte inferior izquierda de la
    página web, haga clic en **+New**, en **Máquina virtual** y, a
    continuación, en **From Gallery**.
    
    ![Crear una m&aacute;quina
    virtual](./media/create-and-configure-centos-vm-in-portal/CreateVM.png)

3.  Seleccione una imagen de máquina virtual CentOS en **Platform
    Images** y, a continuación, haga clic en la flecha de avance de la
    parte inferior derecha de la página.

4.  Proporcione la siguiente información en la página **Virtual machine
    configuration**:
    * Proporcione un nombre en el cuadro **Virtual machine name**, como
      "testlinuxvm".
    * Especifique un nuevo nombre de usuario en **New User Name**, como
      "newuser", que se agregará al archivo de lista de Sudoers.
    * En el cuadro **New Password**, escriba una [contraseña segura][2].
    * En el cuadro **Confirm Password**, vuelva a escribir la
      contraseña.
    * Seleccione el tamaño correspondiente en la lista desplegable
      **Size**.
    
    Haga clic en la flecha de avance para continuar.

5.  Proporcione la siguiente información en la página **Virtual machine
    mode**:
    * Seleccione **Standalone Virtual Machine**.
    * En el cuadro **DNS Name**, escriba una dirección DNS válida. Por
      ejemplo, "testlinuxvm".
    * En el cuadro **Cuenta de almacenamiento**, seleccione **Use an
      Automatically Generated Storage Account**.
    * En el cuadro **Region/Affinity Group/Virtual Network**, seleccione
      una región en la que se alojará la imagen virtual.
    
    Haga clic en la flecha de avance para continuar.

6.  En la página **Virtual machine options**, seleccione **(none)** en
    el cuadro **Conjunto de disponibilidad**.
    
    Haga clic en la marca de verificación para continuar.

7.  Espere a que Azure prepare la máquina virtual.
## Configuración de extremos

Una vez creada una máquina virtual, configure los extremos para realizar
la conexión remota.

1.  En el Portal de administración, haga clic en **Máquinas virtuales**,
    en el nombre de la nueva máquina virtual y, a continuación, en
    **Endpoints**.

2.  Haga clic en **Edit Endpoint** en la parte inferior de la página y
    edite el extremo SSH de manera que **Public Port** sea 22.
## Conexión a una máquina virtual

Una vez que se haya realizado el aprovisionamiento en la máquina virtual
y los extremos se hayan configurado, puede proceder a la conexión
mediante SSH o PuTTY.
### Conexión mediante SSH

Si usa Linux, realice la conexión a la máquina virtual mediante SSH. En
el símbolo del sistema, ejecute:

    $ ssh newuser@testlinuxvm.cloudapp.net -o ServerAliveInterval=180

Especifique la contraseña del usuario.
### Conexión mediante PuTTY

Si usa un equipo con Windows, conéctese a la máquina virtual mediante
PuTTY. PuTTY puede descargarse desde la [página de descarga de PuTTY (en
inglés)][3].

1.  Descargue y guarde **putty.exe** en un directorio de su equipo. Abra
    una secuencia de comandos, diríjase a esa carpeta y ejecute
    **putty.exe**.

2.  Especifique "testlinuxvm.cloudapp.net" en **Host Name** y "22" en
    **Port**. ![Pantalla de
    PuTTY](./media/create-and-configure-centos-vm-in-portal/putty.png)
## Actualización de la máquina virtual (opcional)

Una vez que se haya conectado a la máquina virtual, tiene la opción de
instalar actualizaciones. Ejecute:

     $ sudo yum update

Especifique la contraseña de nuevo. Espere a que las actualizaciones se
instalen.



[1]: http://go.microsoft.com/fwlink/p/?LinkID=294063
[2]: http://msdn.microsoft.com/en-us/library/ms161962.aspx
[3]: http://www.puttyssh.org/download.html
