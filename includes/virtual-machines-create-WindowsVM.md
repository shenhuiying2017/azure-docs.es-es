1.  Inicie sesión en el [Portal de administración][Portal de administración] de Azure. Si todavía no dispone de una suscripción, consulte la oferta de [Versión de evaluación gratuita][Versión de evaluación gratuita].

2.  En la barra de comandos que se encuentra en la parte inferior de la ventana, haga clic en **Nuevo**.

3.  En **Proceso**, haga clic en **Máquina virtual** y en **De la galería**.

    ![Navigate to From Gallery in the Command Bar][Navigate to From Gallery in the Command Bar]

4.  La primera pantalla permite que **Elija una imagen** para la máquina virtual de una de las listas de la galería de imágenes. (Las imágenes disponibles pueden ser diferentes según la suscripción que esté usando). Haga clic en la flecha para continuar.

    ![Choose an image][Choose an image]

5.  La segunda pantalla permite seleccionar el nombre del equipo y el tamaño, así como el nombre de usuario y la contraseña del usuario administrativo. Si solo desea probar máquinas virtuales de Azure, rellene los campos tal como se muestra en la imagen siguiente. En caso contrario, elija el nivel y el tamaño necesarios para ejecutar la aplicación o carga de trabajo. A continuación, se ofrecen algunos datos para ayudarle a rellenarlos:

    -   **Nuevo nombre de usuario** se refiere a la cuenta administrativa que se usa para administrar el servidor. Cree una contraseña única para esta cuenta y asegúrese de recordarla. **Necesitará un nombre de usuario y una contraseña para iniciar sesión en la máquina virtual**.

    -   El tamaño de la máquina virtual afecta al coste de usarla, así como a algunas opciones de configuración como, por ejemplo, el número de discos de datos que se pueden acoplar. Para obtener información más detallada, consulte [Tamaños de máquinas virtuales y servicios en la nube de Azure][Tamaños de máquinas virtuales y servicios en la nube de Azure].

    ![Configure the properties of the virtual machine][Configure the properties of the virtual machine]

6.  La tercera pantalla permite configurar los recursos relacionados con las redes, el almacenamiento y la disponibilidad. A continuación, se ofrecen algunos datos para ayudarle a rellenarlos:

    -   **Nombre DNS de servicio en la nube** es el nombre DNS global que forma parte del URI que se usa para establecer contacto con la máquina virtual. Tendrá que proponer su propio nombre de servicio en la nube porque debe ser único en Azure. Los servicios en la nube tienen importancia en los escenarios que usen [varias máquinas virtuales][varias máquinas virtuales].

    -   En **Región/grupo de afinidad/red virtual**, use una región que sea adecuada a su ubicación. También puede optar por especificar una red virtual en su lugar.

    > [WACOM.NOTE] Si desea que una máquina virtual use una red virtual, **debe** especificar la red virtual cuando cree la máquina virtual. No puede incorporar una máquina virtual a una red virtual después de crear la VM. Para obtener más información, consulte [Información general sobre redes virtuales de Azure][Información general sobre redes virtuales de Azure].

    -   Para obtener información detallada sobre la configuración de extremos, consulte [Configuración de extremos en una máquina virtual][Configuración de extremos en una máquina virtual].

    ![Configure the connected resources of the virtual machine][Configure the connected resources of the virtual machine]

7.  La cuarta pantalla de configuración permite configurar el agente de máquina virtual y algunas de las extensiones disponibles. Haga clic en la marca de verificación para crear la máquina virtual.

    ![Configure VM Agent and extensions for the virtual machine][Configure VM Agent and extensions for the virtual machine]

    > [WACOM.NOTE] El agente de máquina virtual proporciona el entorno para que pueda instalar las extensiones que pueden ayudarlo a interactuar con la máquina virtual o a administrarla. Para conocer los detalles, consulte [Using Extensions][Using Extensions].

8.  Una vez creada la máquina virtual, en el Portal de administración se muestra la nueva máquina virtual en **Máquinas virtuales**. También se crean el servicio en la nube y la cuenta de almacenamiento correspondientes y se muestran en dichas secciones. La máquina virtual y el servicio en la nube se inician automáticamente, y aparecen en el Portal de administración con el estado **En ejecución**.

    ![Configure VM Agent and the endpoints of the virtual machine][Configure VM Agent and the endpoints of the virtual machine]

  [Portal de administración]: http://manage.windowsazure.com
  [Versión de evaluación gratuita]: http://www.windowsazure.com/es-es/pricing/free-trial/
  [Navigate to From Gallery in the Command Bar]: ./media/virtual-machines-create-WindowsVM/fromgallery.png
  [Choose an image]: ./media/virtual-machines-create-WindowsVM/chooseimage.png
  [Tamaños de máquinas virtuales y servicios en la nube de Azure]: http://go.microsoft.com/fwlink/p/?LinkId=466520
  [Configure the properties of the virtual machine]: ./media/virtual-machines-create-WindowsVM/vmconfiguration.png
  [varias máquinas virtuales]: http://www.windowsazure.com/es-es/documentation/articles/cloud-services-connect-virtual-machine/
  [Información general sobre redes virtuales de Azure]: http://go.microsoft.com/fwlink/p/?LinkID=294063
  [Configuración de extremos en una máquina virtual]: http://www.windowsazure.com/es-es/documentation/articles/virtual-machines-set-up-endpoints/
  [Configure the connected resources of the virtual machine]: ./media/virtual-machines-create-WindowsVM/resourceconfiguration.png
  [Configure VM Agent and extensions for the virtual machine]: ./media/virtual-machines-create-WindowsVM/agent-and-extensions.png
  [Using Extensions]: http://go.microsoft.com/FWLink/p/?LinkID=390493
  [Configure VM Agent and the endpoints of the virtual machine]: ./media/virtual-machines-create-WindowsVM/vmcreated.png
