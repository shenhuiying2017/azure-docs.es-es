<properties  authors="kathydav" editor="tysonn" manager="jeffreyg" />
# Creación de una máquina virtual personalizada

La creación de una máquina virtual personalizada le permite seleccionar
opciones que no están disponibles si usa el método **Quick Create**.
Entre estas opciones se incluye la conexión a una máquina virtual, a un
servicio en la nube existente y a un conjunto de disponibilidad.

Cada máquina virtual se asocia a un servicio en la nube, ya sea con ella
misma o con otras máquinas virtuales del mismo servicio en la nube. Una
razón común por la que conviene colocar más de una máquina virtual en el
mismo servicio en la nube es la de proporcionar el equilibrio de carga
para la aplicación. Si la aplicación necesita solo una máquina virtual o
está creando la primera máquina virtual, se creará el servicio en la
nube cuando se cree la máquina virtual. De lo contrario, agregará la
nueva máquina virtual a un servicio en la nube existente.

**Importante**: si desea que su máquina virtual use una red virtual
para la conexión a ella directamente mediante un nombre de host o que
configure conexiones entre locales, asegúrese de que especifica la
máquina virtual cuando la cree. Puede configurarse una máquina virtual
para que se una a una red virtual solo cuando se cree la máquina
virtual. Para obtener más información acerca de redes virtuales,
consulte [Información general sobre redes virtuales de Azure][1].

1.  Inicie sesión en el [Portal de administración de Azure][2].

2.  En la barra de comandos, haga clic en **New**.

3.  Haga clic en **Proceso**, en **Máquina virtual** y, a continuación,
    en **From Gallery**.

4.  En **Platform Images**, seleccione una imagen de plataforma que
    desee usar y, a continuación, haga clic en la flecha para continuar.

5.  Si hay varias versiones disponibles de la imagen, en **Version
    Release Date**, seleccione la versión que desea usar.

6.  En **Virtual Machine Name**, escriba el nombre que desea usar para
    la máquina virtual.

7.  En **Size**, seleccione el tamaño que desea usar para la máquina
    virtual. El tamaño que seleccione depende de la cantidad de núcleos
    que se necesitan para su aplicación.

8.  En **New User Name**, escriba un nombre para la cuenta
    administrativa que desea usar para administrar el servidor.

9.  En **New Password**, escriba la contraseña segura que vaya a usar
    para la cuenta administrativa en la máquina virtual. En **Confirm
    Password**, vuelva a escribir la contraseña.

10. Haga clic en la flecha para continuar.

11. En **Servicio en la nube**, realice los siguientes pasos:

* Si es la primera o única máquina virtual en el servicio en la nube,
  seleccione **Create a New Cloud Service**. A continuación, en **Cloud
  Service DNS Name**, escriba un nombre que use entre 3 y 24 letras
  minúsculas y números. Este nombre se convierte en parte del URI que se
  usa para ponerse en contacto con la máquina virtual mediante el
  servicio en la nube.
* Si la máquina virtual se agrega a un servicio en la nube, selecciónelo
  en la lista.
  
	**Nota**: para obtener más información sobre la colocación de máquinas virtuales en el mismo servicio en la nube, consulte [Conexión	de máquinas virtuales en un Servicio en la nube][3].

1.  En **Region/Affinity Group/Virtual Network**, seleccione la región,
    el grupo de afinidad o la red virtual que desee usar para la máquina
    virtual. Para obtener más información sobre los grupos de afinidad,
    consulte [Acerca de los grupos de afinidad para la red virtual][4].

2.  En **Cuenta de almacenamiento**, seleccione una cuenta de
    almacenamiento existente para el archivo VHD o use una cuenta de
    almacenamiento generada automáticamente. Solo se crea una cuenta de
    almacenamiento por región de manera automática. Todas las demás
    máquinas virtuales que crea con esta configuración se ubican en esta
    cuenta de almacenamiento. Tiene un límite de 20 cuentas de
    almacenamiento.

3.  Si desea que la máquina virtual pertenezca a un conjunto de
    disponibilidad, en **Conjunto de disponibilidad**, seleccione
    **Create availability set** o agréguela a un conjunto de
    disponibilidad existente.
    
    **Nota**: las máquinas virtuales que sean miembro de un conjunto de
    disponibilidad se implementan en dominios de errores distintos. La
    colocación de varias máquinas virtuales en un conjunto de
    disponibilidad ayuda a garantizar que la aplicación esté disponible
    durante los errores de red, los errores de hardware de disco local y
    cualquier tiempo de inactividad planificado.

4.  En **VM Agent**, decida si va a instalar el Agente de VM. Este
    agente proporciona el entorno para que pueda instalar las
    extensiones que pueden ayudarlo a interactuar con la máquina
    virtual. Para conocer los detalles, consulte [Using Extensions][5].

5.  En **Endpoints**, revise los nuevos extremos que se crearán para
    permitir las conexiones con la máquina virtual, como el cliente
    Shell seguro (SSH) o Escritorio remoto. Puede también agregar
    extremos ahora, o crearlos más tarde. Para obtener instrucciones
    sobre la creación de extremos más adelante, consulte
    [Establecimiento de comunicación con una máquina virtual][6].

6.  Haga clic en la flecha para crear la máquina virtual.
    
    ![Creaci&oacute;n correcta de la m&aacute;quina virtual
    personalizada](./media/howto-custom-create-vm/VMSuccessWindows.png)



[1]: http://go.microsoft.com/fwlink/p/?LinkID=294063
[2]: http://manage.windowsazure.com
[3]: http://www.windowsazure.com/en-us/manage/windows/how-to-guides/connect-to-a-cloud-service/
[4]: http://msdn.microsoft.com/en-us/library/windowsazure/
[5]: http://go.microsoft.com/FWLink/p/?LinkID=390493
[6]: http://www.windowsazure.com/en-us/manage/linux/how-to-guides/setup-endpoints/
