1. Inicie sesión en el [Portal de administración de Azure](http://manage.windowsazure.com).
En la barra de comandos, haga clic en **New**.

2. Haga clic en **Máquina virtual** y, a continuación, haga clic en **De la galería**.

3. En **elegir una imagen**, seleccione una imagen de una de las listas. (Las imágenes disponibles pueden ser diferentes según la suscripción que esté usando). Haga clic en la flecha para continuar.

4. Si hay varias versiones disponibles de la imagen, en **Fecha de lanzamiento de la versión**, seleccione la versión que desea usar.

5. En **Nombre de máquina virtual**, escriba el nombre que desea usar. Para esta máquina virtual, escriba **MyTestVM1**.

6. En **Tamaño**, seleccione el tamaño que desea usar para la máquina virtual. El tamaño que seleccione depende de la cantidad de núcleos que se necesitan para su aplicación.  Para esta máquina virtual, seleccione el tamaño más pequeño disponible.

7. En **Nombre de usuario nuevo**, escriba el nombre de la cuenta que usará para administrar la máquina virtual No puede usar la raíz para el nombre de usuario. Para esta máquina virtual, escriba **NewUser1**.

8. En Autenticación, active **Proporcionar una contraseña**. Luego, proporcione la información necesaria y haga clic en la flecha para continuar.

9. Puede poner máquinas virtuales juntas en el servicio en la nube; no obstante, en este tutorial solo va a crear una máquina virtual. Para ello, seleccione **Crear un nuevo servicio en la nube**.

10. En **Nombre DNS de servicio en la nube**, escriba un nombre que use entre 3 y 24 letras minúsculas y números. Tendrá que proponer su propio nombre de servicio en la nube porque debe ser único en Azure. Este nombre se convierte en parte del URI que se usa para ponerse en contacto con la máquina virtual mediante el servicio en la nube.

11. En **Región/grupo de afinidad/red virtual**, seleccione dónde desea ubicar la máquina virtual.

12. Puede seleccionar una cuenta de almacenamiento donde se almacena el archivo de VHD. Para este tutorial, acepte la configuración predeterminada de **Usar una cuenta de almacenamiento generada automáticamente**.

13. En **Conjunto de disponibilidad**, a efectos de este tutorial, use la configuración predeterminada de **Ninguno**. 

14.	En **Extremos**, revise el extremo que se crea automáticamente para permitir conexiones de Shell seguro (SSH) a la máquina virtual. (Los extremos permiten que los recursos en Internet u otras redes virtuales se comuniquen con una máquina virtual). Puede agregar más extremos ahora o crearlos más adelante. Para obtener instrucciones sobre la creación de extremos más adelante, consulte [Configuración de extremos en una máquina virtual](http://azure.microsoft.com/documentation/articles/virtual-machines-set-up-endpoints/).

15.  En **Agente de máquina virtual**, revise las extensiones disponibles. Estas extensiones proporcionan varias características que facilitan el uso y la administración de una máquina virtual. Para conocer los detalles, consulte [Extensiones de máquina virtual de Azure](http://go.microsoft.com/FWLink/p/?LinkID=390493). 


Una vez que Azure crea la máquina virtual y el servicio en la nube, el Portal de administración muestra la máquina virtual nueva en **Máquinas virtuales** y el servicio en la nube en **Servicios en la nube**. Tanto la máquina virtual como el servicio en la nube se inician automáticamente.
\<!--HONumber=42-->
