<properties  writer="kathydav" editor="tysonn" manager="jeffreyg" />

**Nota**: en este artículo se crea una máquina virtual que no está
conectada a una red virtual. Si desea que su máquina virtual use una red
virtual para permitir la conexión a las máquinas virtuales directamente
mediante un nombre de host o que configure conexiones entre locales,
utilice el método **Desde galería** y especifique la red virtual cuando
cree la máquina virtual. Para obtener más información acerca de redes
virtuales, consulte [Información general sobre redes virtuales de
Azure][1].

Siga estos pasos para crear una máquina virtual:

1.  Inicie sesión en el [Portal de administración de Azure][2] con la
    cuenta de Azure.

2.  En el Portal de administración, en la parte inferior izquierda de la
    página web, haga clic en **+New**, en **Máquina virtual** y, a
    continuación, en **From Gallery**. ![Crear una m&aacute;quina
    virtual](./media/create-and-configure-windows-server-2008-vm-in-portal/CreateWinVM.png)

3.  Seleccione una imagen de la máquina virtual de Windows Server 2008
    R2 SP1 y, a continuación, haga clic en la flecha de avance en la
    parte inferior derecha de la página.

4.  Proporcione la siguiente información en la página **Virtual machine
    configuration**:

* Proporcione un **Virtual Machine Name**, como "testwinvm".
* En el cuadro **New User Name**, escriba "Administrator".
* En el cuadro **New Password**, escriba una [contraseña segura][3].
* En el cuadro **Confirm Password**, vuelva a escribir la contraseña.
* Seleccione el tamaño correspondiente en la lista desplegable **Size**.
  
  Haga clic en la flecha de avance para continuar.

1.  Proporcione la siguiente información en la página **Virtual machine
    mode**:

* Seleccione **Standalone Virtual Machine**.
* En el cuadro **DNS Name**, escriba un subdominio válido con el formato
  **testwinvm.cloudapp.net**.
* En el cuadro **Region/Affinity Group/Virtual Network**, seleccione una
  región en la que se alojará la imagen virtual.
  
  Haga clic en la flecha de avance para continuar.

1.  En la página **Virtual machine options**, seleccione **(none)** en
    el cuadro **Conjunto de disponibilidad**. Haga clic en la marca de
    verificación para continuar.

2.  Espere a que Azure prepare la máquina virtual.



[1]: http://go.microsoft.com/fwlink/p/?LinkID=294063
[2]: http://manage.windowsazure.com
[3]: http://msdn.microsoft.com/en-us/library/ms161962.aspx
