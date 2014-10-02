<properties writer="kathydav" editor="tysonn" manager="jeffreyg" />

# Creación rápida de una máquina virtual

Use el método **Quick Create** para crear rápidamente una máquina virtual en el Portal de administración. Cuando cree esa máquina, use un cuadro de diálogo para proporcionar la información de la configuración.

**Nota**: en este artículo se crea una máquina virtual que no está conectada a una red virtual. Si desea que la máquina virtual use una red virtual, utilice el método **From Gallery** y especifique la red virtual cuando cree la máquina virtual. Para obtener más información acerca de redes virtuales, consulte [Información general sobre redes virtuales de Azure][].

1.  Inicie sesión en el [Portal de administración de Azure][].

2.  En la barra de comandos, haga clic en **New**.

    ![una máquina virtual][]

3.  Haga clic en **Máquinas virtuales** y, a continuación, haga clic en **Quick Create**.

    ![Creación rápida de una máquina virtual][]

    Aparecerá el cuadro de diálogo **Create a New Virtual Machine**.

4.  Escriba la siguiente información para la nueva máquina virtual:

    -   **DNS Name**: El nombre que se usa para la máquina virtual creada y el servicio en la nube que contiene la máquina virtual.
    -   **Image**: La imagen de plataforma usada para crear la máquina virtual.
    -   **User Name**: El nombre de la cuenta que desea usar para administrar la máquina virtual.
    -   **Account Password**: Escriba y confirme una contraseña segura para la cuenta.
    -   **Location**: La región que contiene la máquina virtual.

5.  Haga clic en la marca de verificación para crear la máquina virtual.

    **Nota:** se crea una cuenta de almacenamiento automáticamente para que contenga esa máquina virtual.

    Verá la nueva máquina virtual en la página **Máquinas virtuales**.

    ![Creación correcta de la máquina virtual][]

  [Información general sobre redes virtuales de Azure]: http://go.microsoft.com/fwlink/p/?LinkID=294063
  [Portal de administración de Azure]: http://manage.windowsazure.com
  [una máquina virtual]: ./media/howto-quick-create-vm/create.png
  [Creación rápida de una máquina virtual]: ./media/howto-quick-create-vm/createquick.png
  [Creación correcta de la máquina virtual]: ./media/howto-quick-create-vm/vmsuccesswindows.png
