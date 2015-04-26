<properties 
	pageTitle="¿Qué es Azure Multi-Factor Authentication?" 
	description="Obtenga más información acerca de Azure Multi-Factor Authentication, un método de autenticación que requiere el uso de más de un método de verificación y agrega una segunda capa importante de seguridad a las transacciones e inicios de sesión del usuario." 
	services="active-directory, multi-factor-authentication" 
	documentationCenter="" 
	authors="blackmist" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="ruby" 
	ms.topic="article" 
	ms.date="09/17/2014" 
	ms.author="larryfr"/>



#Administración de Máquinas virtuales de Azure usando Ruby

En esta guía se indica cómo realizar las tareas de administración habituales mediante programación para Máquinas virtuales de Azure, como la creación y la configuración de las máquinas virtuales y la incorporación de discos de datos. El SDK de Azure para Ruby proporciona acceso a la funcionalidad de administración de diferentes servicios de Azure, entre los que se incluyen l servicio Máquinas virtuales de Azure.

##Tabla de contenido

* [Qué es la administración de servicios](#what-is)
* [Conceptos](#concepts)
* [Creación de un certificado de administración](#setup-certificate)
* [Creación de una aplicación de Ruby](#create-app)
* [Configuración de la aplicación para que use el SDK](#configure-access)
* [Configuración de una conexión de administración de Azure](#setup-connection)
* [Configuración Máquinas virtuales](#virtual-machine)
* [Configuración imágenes y discos](#vm-images)
* [Configuración Servicios en la nube](#cloud-services)
* [Configuración servicios de almacenamiento](#storage-services)
* [Pasos siguientes](#next-steps)

## <a name="what-is"> </a>Qué es la administración de servicios

Azure proporciona las [API REST para las operaciones de administración de servicios](http://msdn.microsoft.com/library/windowsazure/ee460799.aspx), entre las que se incluyen la administración de Máquinas virtuales de Azure. El SDK de Azure para Ruby expone operaciones de administración para Máquinas virtuales mediante la clase **Azure::VirtualMachineSerivce**. Se puede obtener acceso a gran parte de la funcionalidad de administración de máquinas virtuales en el [Portal de administración de Azure](https://manage.windowsazure.com) usando esta clase.

Mientras que la API de administración de servicios se puede usar para administrar deferentes servicios hospedados en Azure, este documento solo proporciona información sobre la administración de Máquinas virtuales de Azure.

## <a name="concepts"> </a>Conceptos

Las máquinas virtuales de Azure se implementan como roles ('roles') dentro de un servicio en la nube. Cada servicio en la nube puede contener uno o varios roles que se agrupan de forma lógica en implementaciones. El rol define las características físicas generales de la máquina virtual, como la cantidad de memoria disponible o la cantidad de núcleos de CPU, entre otros aspectos.

Además, cada máquina virtual tiene un disco del SO que contiene el sistema operativo de arranque. Una máquina virtual tiene uno o varios discos de datos. Estos son los discos adicionales que deberían usarse para almacenar datos de la aplicación. Los discos se implementan como discos duros virtuales (VHD) almacenados en el almacenamiento de blobs de Azure. Los VHD también se pueden exponer como imágenes ('images'), que son plantillas usadas para crear discos que la máquina virtual utiliza durante la creación de las máquinas virtuales. Por ejemplo, si crea una nueva máquina virtual que usa una imagen de Ubuntu conseguirá que se cree un nuevo disco del SO a partir de la imagen de Ubuntu.

La mayoría de las imágenes las proporciona Microsoft o los socios; no obstante, puede crear sus propias imágenes o crear una desde una máquina virtual hospedada en Azure.

## <a name="setup-certificate"> </a>Creación de un certificado de administración de Azure

Al realizar operaciones de administración de servicios, como las expuestas mediante la clase **Azure::VirtualMachineService**, tiene que proporcionar el Id. de suscripción de Azure y un archivo que contenga un certificado de administración para la suscripción. El SDK usa ambos al autenticase en la API REST de Azure.

Puede obtener el Id. de suscripción y un certificado de administración usando la interfaz de línea de comandos entre plataformas de Azure (xplat-cli). Consulte [Instalación y configuración de la interfaz de línea de comandos entre plataformas de Azure](http://azure.microsoft.com/manage/install-and-configure-cli/) para obtener información acerca de cómo instalar y configurar la xplat-cli.

Una vez configurada la xplat-cli, puede realizar los pasos siguientes para recuperar el Id. de suscripción de Azure y exportar un certificado de administración:

1. Para recuperar el Id. de suscripción, use el comando siguiente:

		azure account list

2. Para exportar el certificado de administración, use el comando siguiente:

		azure account cert export

	Una vez completado el comando, el certificado se exportará a un archivo denominado &lt;nombre de la suscripción de azure&gt;.pem. Por ejemplo, si la suscripción se llama **mygreatsubscription**, el archivo creado se llamará **mygreatsubscription.pem**.

Anote el Id. de suscripción y la ubicación del archivo PEM que contiene el certificado exportado, puesto que estos datos se utilizarán más tarde en este documento.

## <a name="create-app"></a>Creación de una aplicación de Ruby

Cree una aplicación de Ruby. Los ejemplos utilizados en este documento se pueden implementar en un solo archivo **.rb**.

## <a name="configure-access"></a>Configuración de la aplicación

Para administrar los servicios de Azure, tiene que descargar y usar la gema de Azure que contiene el SDK de Azure para Ruby.

### Uso del comando gem para instalar el paquete

1.  Utilice una interfaz de línea de comandos como **PowerShell** (Windows), **Terminal** (Mac) o **Bash** (Unix) y vaya a la carpeta donde ha creado la aplicación de ejemplo.

2. Utilice el comando siguiente para instalar la gema de Azure:

		gem install azure

	Debería ver una salida similar a la siguiente:

		Fetching: mini_portile-0.5.1.gem (100%)
		Fetching: nokogiri-1.6.0-x86-mingw32.gem (100%)
		Fetching: mime-types-1.25.gem (100%)
		Fetching: systemu-2.5.2.gem (100%)
		Fetching: macaddr-1.6.1.gem (100%)
		Fetching: uuid-2.3.7.gem (100%)
		Fetching: azure-0.5.0.gem (100%)
		Successfully installed mini_portile-0.5.1
		Successfully installed nokogiri-1.6.0-x86-mingw32
		Successfully installed mime-types-1.25
		Successfully installed systemu-2.5.2
		Successfully installed macaddr-1.6.1
		Successfully installed uuid-2.3.7
		Successfully installed azure-0.5.0
		7 gems installed

	> [AZURE.NOTE] Si obtiene un error relacionado con los permisos, use <code>sudo gem install azure</code> en su lugar.

### Gema obligatoria

Agregue lo siguiente a la parte superior del archivo de la aplicación de Ruby con un editor de texto. De este modo, se cargará la gema de Azure y el SDK de Azure para Ruby estará disponible para la aplicación:

	require 'azure'

## <a name="setup-connection"> </a>Codificación a la administración de servicios

Para realizar correctamente operaciones de administración de servicios con Azure, debe especificar el identificador de suscripción y el certificado obtenido en [Creación de un certificado de administración de Azure](#setup-certificate). La forma más sencilla de hacerlo es especificando el Id. y la ruta de acceso al archivo de certificado mediante el uso de las variables de entorno siguientes:

* AZURE\_MANAGEMENT\_CERTIFICATE: la ruta de acceso al archivo .PEM que contiene el certificado de administración.

* AZURE\_SUBSCRIPTION\_ID: el identificador de su suscripción a Azure.

También puede configurar estos valores mediante programación en la aplicación usando lo siguiente:

	Azure.configure do |config|
	  config.management_certificate = 'path/to/certificate'
	  config.subscription_id = 'subscription ID'
	end

##<a name="virtual-machine"> </a>Codificación Máquinas virtuales

Las operaciones de administración para Máquinas virtuales de Azure se realizan usando la clase **Azure::VirtualMachineService**.

###Codificación Crear una máquina virtual

Para crear una nueva máquina virtual, use el método **create\_virtual\_machine**. Este método acepta un hash que contiene los parámetros siguientes y devuelve una instancia de **Azure::VirtualMachineManagement::VirtualMachine** que describe la máquina virtual creada:

**Parámetros**

* **:vm\_name**: el nombre de la máquina virtual.

* **:vm\_user**: el nombre de usuario administrador o raíz.

* **:password**: la contraseña usada para el usuario administrador o raíz.

* **:image**: la imagen del SO que se usará para crear el disco del SO para esta máquina virtual. El disco del SO se almacenará en un VHD creado en el almacenamiento de blobs.

* **:location**: la región en la que se creará la máquina virtual. Esta debe ser la misma que la de la cuenta de almacenamiento que contiene los discos que usa la máquina virtual.

Este es un ejemplo de creación de una nueva máquina virtual usando estos parámetros:

	vm_params = {
	  :vm_name => 'mygreatvm',
	  :vm_user => 'myuser',
	  :password => 'mypassword',
	  :image => 'b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-13_04-amd64-server-20130824-es-es-30GB',
	  :location = 'East US'
	}

	vm_mgr = Azure::VirtualMachineService.new
	vm = vm_mgr.create_virtual_machine(vm_params)

	puts "A VM named #{vm.vm_name} was created in a cloud service named #{vm.cloud_service_name}."
	puts "It uses a disk named #{vm.disk_name}, which was created from a #{vm.os_type}-based image."
	puts "The virtual IP address of the machine is #{vm.ipaddress}."
    puts "The fully qualified domain name is #{vm.cloud_service_name}.cloudapp.net."

**Opciones**

Puede proporcionar un hash de parámetros opcionales que permita invalidar el comportamiento predeterminado de la creación de máquinas virtuales, como la especificación de una cuenta de almacenamiento existente en lugar de la creación de una nueva.

A continuación se muestran las opciones que están disponibles al utilizar el método **create\_virtual\_machine**:

* **:storage\_account\_name**: el nombre de la cuenta de almacenamiento que se va a usar para almacenar imágenes de disco. Si la cuenta de almacenamiento no existe todavía, se crea una nueva. Si se omite, se creará una cuenta de almacenamiento con un nombre basado en el parámetro :vm\_name.

* **:cloud\_service\_name**: el nombre del servicio en la nube que hay que usar para hospedar la máquina virtual. Si el servicio en la nube no existe todavía, se crea uno nuevo. Si se omite, se creará una nueva cuenta de servicio de nube con un nombre basado en el parámetro :vm\_name.

* **:deployment_name**: el nombre de la implementación que se va a usar al implementar la configuración de la máquina virtual.

* **:tcp_endpoints**: los puertos TCP que se exponen públicamente para esta máquina virtual. No es necesario especificar los extremos SSH (para las máquinas virtuales basadas en Linux) y WinRM (para las máquinas virtuales basadas en Windows), ya que se crearán automáticamente. Se pueden especificar varios puertos separados por comas. Para asociar un puerto interno a uno público usando un número de puerto diferente, utilice el formato **public port:internal port**. Por ejemplo, 80:8080 expone el puerto interno 8080 como puerto público 80.

* **:service_location**: la ubicación de almacenamiento del certificado de destino en la máquina virtual. Solo se aplica a máquinas virtuales basadas en Windows.

* **:ssh\_private\_key\_file**: archivo que contiene la clave privada que se usará para proteger el acceso SSH a la máquina virtual basada en Linux. También se usa para especificar el certificado utilizado para proteger WinRM si se selecciona el transporte HTTPS. Si **:ssh\_private\_key\_file** y **:ssh\_certificate\_file** se omiten, SSH usará solo la autenticación de contraseña.

* **:ssh\_certificate\_file**: el archivo que contiene el archivo de certificado que se usará para proteger el acceso SSH a las máquinas virtuales basadas en Linux. También se usa para especificar el certificado utilizado para proteger WinRM si se selecciona el transporte HTTPS. Si **:ssh\_private\_key\_file** y **:ssh\_certificate\_file** se omiten, SSH usará solo la autenticación de contraseña.

* **:ssh\_port**: el puerto público que se usará para la comunicación SSH. Si se omite, el valor de puerto SSH predeterminado será 22.

* **: vm\_size**: el tamaño de la máquina virtual. Determina el tamaño de la memoria, el número de núcleos, el ancho de banda y otras características físicas de la máquina virtual. Consulte [Tamaños de máquinas virtuales y servicios en la nube de Azure](http://msdn.microsoft.com/library/windowsazure/dn197896.aspx) para ver las características físicas y los tamaños disponibles.

* **:winrm_transport**: una matriz de transportes disponible para su uso con WinRM. Los transportes válidos son  'http' y  'https'. Si  'https' se especifica como un transporte, también debe usar **:ssh\_private\_key\_file** y **:ssh\_certificate\_file** para especificar el certificado usado para proteger las comunicaciones HTTPS.

A continuación encontrará un ejemplo de cómo crear una nueva máquina virtual que use una pequeña instancia de proceso, exponga públicamente los puertos para tráfico HTTP (puerto local 8080, puerto público 80) y HTTPS (443), y permita la autenticación de certificados para las sesiones SSH usando los archivos de certificado especificados:

	vm_params = {
	  :vm_name => 'myvm',
	  :vm_user => 'myuser',
	  :password => 'mypassword',
	  :image => 'b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-13_04-amd64-server-20130824-es-es-30GB',
	  :location = 'East US'
	}

	vm_opts = {
      :tcp_endpoints => '80:8080,443',
      :vm_size => 'Small',
      :ssh_private_key_file => '../sshkey/mykey.key',
      :ssh_certificate_file => '../sshkey/mykey.pem'
	}

	vm_mgr = Azure::VirtualMachineService.new
	vm = vm_mgr.create_virtual_machine(vm_params, vm_opts)

###Codificación máquinas virtuales

Para enumerar las máquinas virtuales existentes para la suscripción de Azure, use el método **list\_virtual\_machines**. Este método devuelve una matriz de objetos **Azure::VirtualMachineManagement::VirtualMachine**:

	vm_mgr = Azure::VirtualMachineService.new
	virtual_machines = vm_mgr.list_virtual_machines

###Codificación información acerca de una máquina virtual

Para obtener una instancia de **Azure::VirtualMachineManagement::VirtualMachine** para una máquina virtual concreta, use el método **get\_virtual\_machine** y proporcione los nombres de la máquina virtual y del servicio en la nube:

	vm_mgr = Azure::VirtualMachineService.new
	vm = vm_mgr.get_virtual_machine('myvm', 'mycloudservice')

###Codificación una máquina virtual

Para eliminar una máquina virtual, use el método **delete\_virtual\_machine** y proporcione los nombres de servicio de nube y de máquina virtual:

	vm_mgr = Azure::VirtualMachineService.new
	vm = vm_mgr.delete_virtual_machine('myvm', 'mycloudservice')

> [AZURE.WARNING] El método **delete_virtual_machine** elimina el servicio en la nube y todos los discos asociados a la máquina virtual.

###Codificación una máquina virtual

Para cerrar una máquina virtual, use el método **shutdown\_virtual\_machine** y proporcione los nombres de servicio de nube y de máquina virtual:

	vm_mgr = Azure::VirtualMachineService.new
	vm = vm_mgr.shutdown_virtual_machine('myvm', 'mycloudservice')

###Codificación una máquina virtual

Para iniciar una máquina virtual, use el método **start\_virtual\_machine** y proporcione los nombres de servicio de nube y de máquina virtual:

	vm_mgr = Azure::VirtualMachineService.new
	vm = vm_mgr.start_virtual_machine('myvm', 'mycloudservice')

##<a name="vm-images"> </a>Codificación discos e imágenes de la máquina virtual

Las operaciones en las imágenes de la máquina virtual  se realizan usando la clase **Azure::VirtualMachineImageService**. La operaciones en los discos se realizan usando la clase **Azure::VirtualMachineImageManagement::VirtualMachineDiskManagementService**.

###Codificación imágenes de máquinas virtuales

Para mostrar las imágenes de máquina virtual disponibles, use el método **list\_virtual\_machine\_images**. Este método devuelve una matriz de objetos **Azure::VirtualMachineImageService**.

	image_mgr = Azure::VirtualMachineImageService.new
	images = image_mgr.list_virtual_machine_images

###Codificación discos

Para mostrar los discos para la suscripción de Azure, use el método **list\_virtual\_machine\_disks**. Este método devuelve una matriz de objetos **Azure::VirtualMachineImageManagement::VirtualMachineDisk**.

	disk_mgr = Azure::VirtualMachineImageManagement::VirtualMachineDiskManagementService.new
	disks = disk_mgr.list_virtual_machine_disks

###Codificación un disco

Para eliminar un disco, use el método **delete\_virtual\_machine\_disk** y especifique el nombre del disco que se va a eliminar:

	disk_mgr = Azure::VirtualMachineImageManagement::VirtualMachineDiskManagementService.new
	disk_mgr.delete_virtual_machine_disk

##<a name="cloud-services"> </a>Codificación Servicios en la nube

Las operaciones de administración de Servicios en la nube de Azure se realizan usando la clase **Azure::CloudService**.

###Codificación de un servicio en la nube

Para crear un nuevo servicio de nube, use el método **create\_cloud\_service** y proporcione un nombre y un hash de opciones. Las opciones válidas son:

* **:location**:  *Required*. Región en la que se creará el servicio en la nube.

* **:description**: una descripción del servicio en la nube.

Lo siguiente crea un servicio en la nube en la región East US:

	cs_mgr = Azure::CloudService.new
	cs_mgr.create_cloud_service('mycloudservice', { :location => 'East US' })

###Codificación servicios en la nube

Para mostrar los servicios de nube para la suscripción de Azure, use el método **list\_cloud\_services**. Este método devuelve una matriz de objetos **Azure::CloudServiceManagement::CloudService**:

	cs_mgr = Azure::CloudService.new
	cloud_services = cs_mgr.list_cloud_services

###Codificación la existencia de un servicio en la nube

Para comprobar si ya existe un servicio en la nube concreto, use el método **get\_cloud\_service** y proporcione el nombre del servicio en la nube. Devuelve **true** si ya existe un servicio en la nube con el nombre especificado; si no existe, devuelve **false**.

	cs_mgr = Azure::CloudService.new
	cs_exists = cs_mgr.get_cloud_service('mycloudservice')

###Codificación de un servicio en la nube

Para eliminar un servicio de nube, use el método **delete\_cloud\_service** y proporcione el nombre del servicio de nube:

	cs_mgr = Azure::CloudService.new
	cs_mgr.delete_cloud_service('mycloudservice')

###Codificación de una implementación

Para eliminar una implementación de un servicio de nube, use el método **delete\_cloud\_service\_deployment** y proporcione el nombre de servicio de nube:

	cs_mgr = Azure::CloudService.new
	cs_mgr.delete_cloud_service_deployment('mycloudservice')

##<a name="storage-services"> </a>Codificación servicios de almacenamiento

Las operaciones de administración de Servicios en la nube de Azure se realizan usando la clase **Azure::StorageService**.

###Codificación de una cuenta de almacenamiento

Para crear una nueva cuenta de almacenamiento, use el método **create\_storage\_account** y proporcione un nombre y un hash de opciones. Las opciones válidas son:

* **:location**:  *Required*. Región en la que se creará la cuenta de almacenamiento.

* **:description**: la descripción de la cuenta de almacenamiento.

Lo siguiente crea una cuenta de almacenamiento en la región East US:

	storage_mgr = Azure::StorageService.new
	storage_mgr.create_storage_account('mystorage', { :location => 'East US' })

###Codificación cuentas de almacenamiento

Para obtener una lista de cuentas de almacenamiento de su suscripción de Azure, use el método **list\_storage\_accounts**. Este método devuelve una matriz de objetos **Azure::StorageManagement::StorageAccount**.

	storage_mgr = Azure::StorageService.new
	accounts = storage_mgr.list_storage_accounts

###Codificación la existencia de una cuenta de almacenamiento

Para ver si existe una cuenta de almacenamiento concreta, use el método **get_storage_account** y especifique el nombre de la cuenta de almacenamiento. Este método devuelve **true** si ya existe la cuenta de almacenamiento o **false** si no existe.

	storage_mgr = Azure::StorageService.new
	store_exists = storage_mgr.get_storage_account('mystorage')

###Codificación de una cuenta de almacenamiento

Para eliminar una cuenta de almacenamiento, use el método **delete\_storage\_account** y especifique el nombre de la cuenta de almacenamiento:

	storage_mgr = Azure::StorageService.new
	storage_mgr.delete_storage_account('mystorage')

##<a name="next-steps"> </a>Pasos siguientes

Ahora que ya conoce los aspectos básicos de la creación de máquinas virtuales de Azure mediante programación, utilice estos vínculos para obtener más información acerca de cómo trabajar con las máquinas virtuales.

* Visite la página de características de las [máquinas virtuales](http://azure.microsoft.com/documentation/services/virtual-machines/).
*  Consulte la referencia de MSDN: [Máquinas virtuales](http://msdn.microsoft.com/library/windowsazure/jj156003.aspx)
* Más información acerca de cómo hospedar una [Aplicación de Ruby on Rails en una máquina virtual](http://azure.microsoft.com/develop/ruby/tutorials/web-app-with-linux-vm/)

<!--HONumber=45--> 
