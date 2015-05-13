1. Inicie sesión en el [portal clásico](http://manage.windowsazure.com) de Azure. Consulte la oferta [gratuita](http://azure.microsoft.com/pricing/free-trial/) si no tiene todavía una suscripción.

2. En la barra de comandos que se encuentra en la parte inferior de la ventana, haga clic en **Nuevo**.

3. En **Proceso**, haga clic en **Máquina virtual**, y, a continuación, en **Desde la galería**.

	![Navegue a Desde la galería en la barra de comandos](./media/virtual-machines-create-WindowsVM/fromgallery.png)
	
4. La primera pantalla le permite **Elegir una imagen** para la máquina virtual de una de las listas de la Galería de imágenes. \(Las imágenes disponibles pueden ser diferentes según la suscripción que esté usando\). Haga clic en la flecha para continuar.

	![Elija una imagen](./media/virtual-machines-create-WindowsVM/chooseimage.png)

5. La segunda pantalla permite seleccionar el nombre del equipo y el tamaño, así como el nombre de usuario y la contraseña del usuario administrativo. Si solo desea probar Máquinas virtuales de Azure, rellene los campos tal como se muestra en la imagen siguiente. En caso contrario, elija el nivel y el tamaño necesarios para ejecutar la aplicación o carga de trabajo. A continuación, se ofrecen algunos datos para ayudarle a rellenarlos:
	
	- **Nuevo nombre de usuario** se refiere a la cuenta administrativa que se usa para administrar el servidor. Cree una contraseña única para esta cuenta y asegúrese de recordarla. **Necesitará el nombre de usuario y la contraseña para iniciar sesión en la máquina virtual**. 

	- El tamaño de la máquina virtual afecta al coste de usarla, así como a algunas opciones de configuración como, por ejemplo, el número de discos de datos que se pueden acoplar. Para obtener información más detallada, consulte [Tamaños de máquinas virtuales y servicios en la nube de Azure](http://go.microsoft.com/fwlink/p/?LinkId=466520).

	![Configure las propiedades de la máquina virtual](./media/virtual-machines-create-WindowsVM/vmconfiguration.png)



6. La tercera pantalla permite configurar los recursos relacionados con las redes, el almacenamiento y la disponibilidad. A continuación, se ofrecen algunos datos para ayudarle a rellenarlos: 
	

	- The **Cloud Service DNS Name** is the global DNS name that becomes part of the URI that's used to contact the virtual machine. You'll need to come up with your own cloud service name because it must be unique in Azure. Cloud services are important for scenarios using [multiple virtual machines](../articles/cloud-services-connect-virtual-machine.md).
 
	- For **Region/Affinity Group/Virtual Network**, use a region that's appropriate to your location. You can also choose to specify a virtual network instead.
 
	>[AZURE.NOTE] If you want a virtual machine to use a virtual network, you **must** specify the virtual network when you create the virtual machine. You can't join the virtual machine to a virtual network after you create the VM. For more information, see [Azure Virtual Network Overview](http://go.microsoft.com/fwlink/p/?LinkID=294063).

	- For details about configuring endpoints, see [How to Set Up Endpoints to a Virtual Machine](../articles/virtual-machines-set-up-endpoints-classic-portal.md).

	![Configure the connected resources of the virtual machine](./media/virtual-machines-create-WindowsVM/resourceconfiguration.png)

7. La cuarta pantalla de configuración permite configurar el agente de máquina virtual y algunas de las extensiones disponibles. Haga clic en la marca de verificación para crear la máquina virtual.


	![Configure VM Agent and extensions for the virtual machine](./media/virtual-machines-create-WindowsVM/agent-and-extensions.png)

	>[AZURE.NOTE] The VM agent provides the environment for you to install extensions that can help you interact with or manage the virtual machine. For details, see [Using Extensions](http://go.microsoft.com/FWLink/p/?LinkID=390493).  
    
8. Una vez creada la máquina virtual, en el portal clásico se muestra la nueva máquina virtual en **Máquinas virtuales**. También se crean el servicio en la nube y la cuenta de almacenamiento correspondientes y se muestran en dichas secciones. La máquina virtual y el servicio en la nube se inician automáticamente, y su estado aparece como **En ejecución**. 

	![Configure el agente de máquina virtual y los extremos de la máquina virtual](./media/virtual-machines-create-WindowsVM/vmcreated.png) 

<!--HONumber=52-->
