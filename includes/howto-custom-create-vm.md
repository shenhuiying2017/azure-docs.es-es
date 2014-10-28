<properties authors="kathydav" editor="tysonn" manager="jeffreyg" />

# Creación de una máquina virtual personalizada

Una máquina virtual *custom* significa una máquina virtual creada con el método **From Gallery** porque le permite configurar más opciones que el método **Quick Create**. Entre estas opciones se incluyen:

-   Numerosas opciones para la imagen utilizada para crear una máquina virtual (VM)
-   Conexión de la VM a una red virtual
-   Adición de la VM a un servicio de nube existente
-   Adición de la VM a un conjunto de disponibilidad

**Importante**: si desea que su máquina virtual use una red virtual para la conexión a ella directamente mediante un nombre de host o que configure conexiones entre locales, asegúrese de que especifica la máquina virtual cuando la cree. Puede configurarse una máquina virtual para que se una a una red virtual solo cuando se cree la máquina virtual. Para obtener más información acerca de redes virtuales, consulte [Información general sobre redes virtuales de Azure][].

1.  Inicie sesión en el [Portal de administración de Azure][].

2.  En la barra de comandos, haga clic en **New**.

3.  Haga clic en **Proceso**, en **Máquina virtual** y, a continuación, en **From Gallery**.

4.  Elija la imagen que desee usar y, a continuación, haga clic en la flecha para continuar.

5.  Si hay varias versiones disponibles de la imagen, en **Version Release Date**, seleccione la versión que desea usar.

6.  En **Virtual Machine Name**, escriba el nombre que desea usar para la máquina virtual.

7.  Utilice **Tier** y **Size** para seleccionar el tamaño adecuado de máquina virtual. El tamaño que seleccione afectará a la configuración máxima de la máquina virtual y también su precio. Para obtener los detalles de configuración, vea [Tamaños de máquinas virtuales y servicios en la nube de Azure][].

8.  En **New User Name**, escriba un nombre para la cuenta administrativa que desea usar para administrar el servidor.

9.  En **New Password**, escriba una contraseña segura para la cuenta administrativa. En **Confirm Password**, vuelva a escribir la contraseña.

10. Haga clic en la flecha para continuar.

11. En **Servicio en la nube**, realice los siguientes pasos:

    -   Si es la primera o única máquina virtual en el servicio en la nube, seleccione **Create a New Cloud Service**. A continuación, en **Cloud Service DNS Name**, escriba un nombre que use entre 3 y 24 letras minúsculas y números. Este nombre se convierte en parte del URI que se usa para ponerse en contacto con la máquina virtual mediante el servicio en la nube.
    -   Si la máquina virtual se agrega a un servicio en la nube, selecciónelo en la lista.

    **Nota**: para obtener más información sobre la colocación de máquinas virtuales en el mismo servicio en la nube, consulte [Conexión de máquinas virtuales en un Servicio en la nube][].

12. En **Region/Affinity Group/Virtual Network**, seleccione la región, el grupo de afinidad o la red virtual que desee usar para la máquina virtual. Para obtener más información sobre los grupos de afinidad, consulte [Acerca de los grupos de afinidad para la red virtual][].

13. En **Cuenta de almacenamiento**, seleccione una cuenta de almacenamiento existente para el archivo VHD o use una cuenta de almacenamiento generada automáticamente. Solo se crea una cuenta de almacenamiento por región de manera automática. Todas las demás máquinas virtuales que crea con esta configuración se ubican en esta cuenta de almacenamiento. Tiene un límite de 20 cuentas de almacenamiento.

14. Si desea que la máquina virtual pertenezca a un conjunto de disponibilidad, en **Conjunto de disponibilidad**, seleccione **Create availability set** o agréguela a un conjunto de disponibilidad existente.

    **Nota**: las máquinas virtuales que sean miembro de un conjunto de disponibilidad se implementan en dominios de errores distintos. La colocación de varias máquinas virtuales en un conjunto de disponibilidad ayuda a garantizar que la aplicación esté disponible durante los errores de red, los errores de hardware de disco local y cualquier tiempo de inactividad planificado.

15. En **Endpoints**, revise los nuevos extremos que se crearán para permitir las conexiones con la máquina virtual, como el cliente Shell seguro (SSH) o Escritorio remoto. Puede también agregar extremos ahora, o crearlos más tarde. Para obtener instrucciones sobre la creación de extremos más adelante, consulte [Configuración de extremos en una máquina virtual][].

16. En **VM Agent**, decida si va a instalar el Agente de VM. Este agente proporciona el entorno para que pueda instalar las extensiones que pueden ayudarlo a interactuar con la máquina virtual. Para conocer los detalles, consulte [Administrar extensiones][].

17. Haga clic en la flecha para crear la máquina virtual.

    ![Creación correcta de la máquina virtual personalizada][]

## Pasos siguientes

Una vez creada una máquina virtual, esta arrancará automáticamente. Cuando el portal se muestre el estado como en ejecución, podrá iniciar sesión en la máquina virtual. Si desea instrucciones, consulte uno de los artículos siguientes:

-   [Inicio de sesión en una máquina virtual con Linux][]
-   [Inicio de sesión en una máquina virtual con Windows Server][]

  [Información general sobre redes virtuales de Azure]: http://go.microsoft.com/fwlink/p/?LinkID=294063
  [Portal de administración de Azure]: http://manage.windowsazure.com
  [Tamaños de máquinas virtuales y servicios en la nube de Azure]: http://go.microsoft.com/fwlink/p/?LinkID=389844
  [Conexión de máquinas virtuales en un Servicio en la nube]: http://www.windowsazure.com/es-es/manage/windows/how-to-guides/connect-to-a-cloud-service/
  [Acerca de los grupos de afinidad para la red virtual]: http://msdn.microsoft.com/es-es/library/azure/jj156085.aspx
  [Configuración de extremos en una máquina virtual]: http://azure.microsoft.com/es-es/documentation/articles/virtual-machines-set-up-endpoints/
  [Administrar extensiones]: http://go.microsoft.com/FWLink/p/?LinkID=390493
  [Creación correcta de la máquina virtual personalizada]: ./media/howto-custom-create-vm/VMSuccessWindows.png
  [Inicio de sesión en una máquina virtual con Linux]: ../virtual-machines-linux-how-to-log-on
  [Inicio de sesión en una máquina virtual con Windows Server]: ../virtual-machines-log-on-windows-server
