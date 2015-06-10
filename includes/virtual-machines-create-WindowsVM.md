1. Inicie sesión en el [Portal de administración de Azure](http://manage.windowsazure.com). Consulte la oferta [gratuita](http://azure.microsoft.com/pricing/free-trial/) si no tiene todavía una suscripción.

2. En la barra de comandos que se encuentra en la parte inferior de la ventana, haga clic en **Nuevo**.

3. En **Proceso**, haga clic en **Máquina virtual**, y, a continuación, en **Desde la galería**.

	![Navigate to From Gallery in the Command Bar](./media/virtual-machines-create-WindowsVM/fromgallery.png)

4. La primera pantalla le permite **Elegir una imagen** para la máquina virtual de una de las listas de la Galería de imágenes. (Las imágenes disponibles pueden ser diferentes según la suscripción que esté usando). Haga clic en la flecha para continuar.

	![Elija una imagen](./media/virtual-machines-create-WindowsVM/chooseimage.png)

5. La segunda pantalla permite seleccionar el nombre del equipo y el tamaño, así como el nombre de usuario y la contraseña del usuario administrativo. Si solo desea probar Máquinas virtuales de Azure, rellene los campos tal como se muestra en la imagen siguiente. En caso contrario, elija el nivel y el tamaño necesarios para ejecutar la aplicación o carga de trabajo. A continuación, se ofrecen algunos datos para ayudarle a rellenarlos:

	- **Nuevo nombre de usuario** se refiere a la cuenta administrativa que se usa para administrar el servidor. Cree una contraseña única para esta cuenta y asegúrese de recordarla. **Necesitará el nombre de usuario y la contraseña para iniciar sesión en la máquina virtual**.

	- El tamaño de la máquina virtual afecta al coste de usarla, así como a algunas opciones de configuración como, por ejemplo, el número de discos de datos que se pueden acoplar. Para obtener información más detallada, consulte [Tamaños de máquinas virtuales y servicios en la nube de Azure](http://go.microsoft.com/fwlink/p/?LinkId=466520).

	![Configure the properties of the virtual machine](./media/virtual-machines-create-WindowsVM/vmconfiguration.png)



6. La tercera pantalla permite configurar los recursos relacionados con las redes, el almacenamiento y la disponibilidad. A continuación, se ofrecen algunos datos para ayudarle a rellenarlos:


	- El **Nombre DNS de servicio en la nube** es el nombre DNS global que forma parte del URI que se usa para establecer contacto con la máquina virtual. Tendrá que proponer su propio nombre de servicio en la nube porque debe ser único en Azure. Los servicios en la nube tienen importancia en los escenarios que usan [varias máquinas virtuales](../articles/cloud-services-connect-virtual-machine.md).

	- En** Región/Grupo de afinidad/Red virtual**, use una región que sea adecuada para su ubicación. También puede optar por especificar una red virtual en su lugar.

	>[AZURE.NOTE]Si desea que una máquina virtual use una red virtual, **debe** especificar la red virtual cuando cree la máquina virtual. No puede incorporar una máquina virtual a una red virtual después de crear la VM. Para obtener más información, consulte [Información general sobre redes virtuales de Azure](http://go.microsoft.com/fwlink/p/?LinkID=294063).

	- Para obtener información detallada sobre la configuración de extremos, consulte [Configuración de extremos en una máquina virtual](../articles/virtual-machines-set-up-endpoints-classic-portal.md).

	![Configure the connected resources of the virtual machine](./media/virtual-machines-create-WindowsVM/resourceconfiguration.png)

7. La cuarta pantalla de configuración permite configurar el agente de máquina virtual y algunas de las extensiones disponibles. Haga clic en la marca de verificación para crear la máquina virtual.


	![Configure VM Agent and extensions for the virtual machine](./media/virtual-machines-create-WindowsVM/agent-and-extensions.png)

	>[AZURE.NOTE]El agente de máquina virtual proporciona el entorno para que pueda instalar las extensiones que pueden ayudarle a interactuar con la máquina virtual o a administrarla. Para conocer los detalles, consulte [Uso de extensiones](http://go.microsoft.com/FWLink/p/?LinkID=390493).

8. Una vez creada la máquina virtual, en el Portal de administración se muestra la nueva máquina virtual en **Máquinas virtuales**. También se crean el servicio en la nube y la cuenta de almacenamiento correspondientes y se muestran en dichas secciones. La máquina virtual y el servicio en la nube se inician automáticamente, y su estado aparece como **En ejecución**.

	![Configure VM Agent and the endpoints of the virtual machine](./media/virtual-machines-create-WindowsVM/vmcreated.png)

<!---HONumber=58-->