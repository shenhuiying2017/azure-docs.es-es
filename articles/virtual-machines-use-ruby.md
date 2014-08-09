<properties  linkid="manage-services-identity-multi-factor-authentication" urlDisplayName="What is Azure Multi-Factor Authentication?" pageTitle="What is Azure Multi-Factor Authentication?" metaKeywords="" description="Learn more about Azure Multi-Factor Authentication, a method of authentication that requires the use of more than one verification method and adds a critical second layer of security to user sign-ins and transactions." metaCanonical="" services="active-directory,multi-factor-authentication" documentationCenter="" title="How to Manage Azure Virtual Machines using Ruby" authors="larryfr" solutions="" manager="" editor="" />

# Administración de Máquinas virtuales de Azure usando Ruby

En esta guía se indica cómo realizar las tareas de administración habituales mediante programación para Máquinas virtuales de Azure, como la creación y la configuración de las máquinas virtuales y la incorporación de discos de datos. El SDK de Azure para Ruby proporciona acceso a la funcionalidad de administración de diferentes servicios de Azure, entre los que se incluyen l servicio Máquinas virtuales de Azure.

## Tabla de contenido

* [Qué es la administración de servicios](#what-is)
* [Conceptos](#concepts)
* [Creación de un certificado de administración](#setup-certificate)
* [Creación de una aplicación de Ruby](#create-app)
* [Configuración de la aplicación para que use el
  SDK](#configure-access)
* [Configuración de una conexión de administración de
  Azure](#setup-connection)
* [Trabajo con Máquinas virtuales](#virtual-machine)
* [Trabajo con imágenes y discos](#vm-images)
* [Trabajo con Servicios en la nube](#cloud-services)
* [Trabajo con servicios de almacenamiento](#storage-services)
* [Pasos siguientes](#next-steps)

## <a name="what-is"> </a>Qué es la administración de servicios

Azure proporciona las [API REST para las operaciones de administración de servicios][1], entre las que se incluyen la administración de Máquinas virtuales de Azure. El SDK de Azure para Ruby expone operaciones de administración para Máquinas virtuales mediante la clase **Azure::VirtualMachineSerivce**. Se puede obtener acceso a gran parte de la funcionalidad de administración de máquinas virtuales en el [Portal de administración de Azure][2] usando esta clase.

Mientras que la API de administración de servicios se puede usar para administrar deferentes servicios hospedados en Azure, este documento solo proporciona información sobre la administración de Máquinas virtuales de Azure.

## <a name="concepts"> </a>Conceptos

Las máquinas virtuales de Azure se implementan como roles dentro de un servicio en la nube. Cada servicio en la nube puede contener uno o varios roles que se agrupan de forma lógica en implementaciones. El rol define las características físicas generales de la máquina virtual, como la cantidad de memoria disponible o la cantidad de núcleos de CPU, entre otros aspectos.

Además, cada máquina virtual tiene un disco del SO que contiene el sistema operativo de arranque. Una máquina virtual tiene uno o varios discos de datos. Estos son los discos adicionales que deberían usarse para almacenar datos de la aplicación. Los discos se implementan como discos duros virtuales (VHD) almacenados en el almacenamiento de blobs de Azure. Los VHD también se pueden exponer como imágenes, que son plantillas usadas para crear discos que la máquina virtual utiliza, a su vez, durante la creación de las máquinas virtuales. Por ejemplo, si crea una nueva máquina virtual que usa una imagen de Ubuntu conseguirá que se cree un nuevo disco del SO a partir de la imagen de Ubuntu.

La mayoría de las imágenes las proporciona Microsoft o los socios; no obstante, puede crear sus propias imágenes o crear una desde una máquina virtual hospedada en Azure.

## <a name="setup-certificate"> </a>Creación de un certificado de administración de Azure

Al realizar operaciones de administración de servicios, como las expuestas mediante la clase **Azure::VirtualMachineService**, tiene que proporcionar el Id. de suscripción de Azure y un archivo que contenga un certificado de administración para la suscripción. El SDK usa ambos al autenticase en la API REST de Azure.

Puede obtener el Id. de suscripción y un certificado de administración usando la interfaz de línea de comandos entre plataformas de Azure (xplat-cli). Consulte [Instalación y configuración de la interfaz de línea de comandos entre plataformas de Azure][3] para obtener información acerca de cómo instalar y configurar la xplat-cli.

Una vez configurada la xplat-cli, puede realizar los pasos siguientes para recuperar el Id. de suscripción de Azure y exportar un certificado de administración:

1.  Para recuperar el Id. de suscripción, use el comando siguiente:
    
         azure account list

2.  Para exportar el certificado de administración, use el comando siguiente:
    
         azure account cert export
    
    Cuando el comando finalice, el certificado se exportará a un archivo
    llamado <a zure-subscription-name>.pem. Por ejemplo, si la suscripción se llama **mygreatsubscription**, el archivo creado se llamará **mygreatsubscription.pem**.

Anote el Id. de suscripción y la ubicación del archivo PEM que contiene el certificado exportado, puesto que estos datos se utilizarán más tarde en este documento.

## <a name="create-app"></a>Creación de una aplicación de Ruby

Cree una aplicación de Ruby. Los ejemplos utilizados en este documento se pueden implementar en un solo archivo **.rb**.

## <a name="configure-access"></a>Configuración de la aplicación

Para administrar los servicios de Azure, tiene que descargar y usar la gema de Azure que contiene el SDK de Azure para Ruby.

### Uso del comando gem para instalar el paquete

1.  Utilice una interfaz de línea de comandos como **PowerShell**
    (Windows), **Terminal** (Mac) o **Bash** (UNIX) y vaya a la carpeta donde ha creado la aplicación de ejemplo.

2.  Utilice el comando siguiente para instalar la gema de Azure:
    
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
    
    <div  class="dev-callout" markdown="1">

**Nota:**
Si ve un error relacionado con los permisos, use `sudo gem install azure` en lugar de lo anterior.


    </div>

### Gema obligatoria

Agregue lo siguiente a la parte superior del archivo de la aplicación de Ruby con un editor de texto. De este modo, se cargará la gema de Azure y el SDK de Azure para Ruby estará disponible para la aplicación:

    require 'azure'

## <a name="setup-connection"> </a>Conexión a la administración de servicios

Para realizar correctamente operaciones de administración de servicios con Azure, debe especificar el Id. de suscripción y el certificado obtenido en la sección [Creación de un certificado de administración de Azure](#setup-certificate). La forma más sencilla de hacerlo es especificando el Id. y la ruta de acceso al archivo de certificado mediante el uso de las variables de entorno siguientes:

* AZURE_MANAGEMENT_CERTIFICATE: Ruta de acceso al archivo .PEM que
  contiene el certificado de administración.

* AZURE_SUBSCRIPTION_ID: Id. de su suscripción a Azure.

También puede configurar estos valores mediante programación en la aplicación usando lo siguiente:

    Azure.configure do |config|
      config.management_certificate = 'path/to/certificate'
      config.subscription_id = 'subscription ID'
    end

## <a name="virtual-machine"> </a>Trabajo con Máquinas virtuales

Las operaciones de administración para Máquinas virtuales de Azure se realizan usando la clase **Azure::VirtualMachineService**.

### Creación de una máquina virtual

Para crear una máquina virtual, use el método
**create_virtual_machine**. Este método acepta un hash que contiene los parámetros siguientes y devuelve una instancia de **Azure::VirtualMachineManagement::VirtualMachine** que describe la máquina virtual creada:

**Parámetros**

* **:vm_name**: Nombre de la máquina virtual.

* **:vm_user**: Nombre de usuario administrador o raíz.

* **:password**: Contraseña usada para el usuario administrador o raíz.

* **:image**: Imagen del SO que se usará para crear el disco del SO para esta máquina virtual. El disco del SO se almacenará en un VHD creado en el almacenamiento de blobs.

* **:location**: Región en la que se creará la máquina virtual. Esta debe ser la misma que la de la cuenta de almacenamiento que contiene los discos que usa la máquina virtual.

Este es un ejemplo de creación de una nueva máquina virtual usando estos parámetros:

    vm_params = {
      :vm_name => 'mygreatvm',
      :vm_user => 'myuser',
      :password => 'mypassword',
      :image => 'b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-13_04-amd64-server-20130824-en-us-30GB',
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

A continuación encontrará las opciones disponibles al usar el método
**create_virtual_machine**:

* **:storage_account_name**: Nombre de la cuenta de almacenamiento que se va a utilizar para almacenar imágenes de disco. Si la cuenta de almacenamiento no existe todavía, se crea una nueva. Si se omite, se crea una nueva cuenta de almacenamiento con un nombre basado en el parámetro :vm_name.

* **:cloud_service_name**: Nombre del servicio en la nube que hay que usar para hospedar la máquina virtual. Si el servicio en la nube no existe todavía, se crea uno nuevo. Si se omite, se crea una nueva cuenta de servicio en la nube con un nombre basado en el parámetro :vm_name.

* **:deployment_name**: Nombre de la implementación que se va a usar al implementar la configuración de la máquina virtual.

* **:tcp_endpoints**: Puertos TCP que se exponen públicamente para esta máquina virtual. No es necesario especificar los extremos SSH (para las máquinas virtuales basadas en Linux) y WinRM (para las máquinas virtuales basadas en Windows), ya que se crearán automáticamente. Se pueden especificar varios puertos separados por comas. Para asociar un puerto interno a uno público usando un número de puerto diferente, utilice el formato **public port:internal port**. Por ejemplo, 80:8080 expone el puerto interno 8080 como puerto público 80.

* **:service_location**: Ubicación de almacenamiento del certificado de destino en la máquina virtual. Solo se aplica a máquinas virtuales basadas en Windows.

* **:ssh_private_key_file**: Archivo que contiene la clave privada que se usará para proteger el acceso SSH a la máquina virtual basada en Linux. También se usa para especificar el certificado utilizado para proteger WinRM si se selecciona el transporte HTTPS. Si se omite tanto **:ssh_private_key_file** como **:ssh_certificate_file**, SSH usará solo la autenticación de contraseña.

* **:ssh_certificate_file**: Archivo que contiene el archivo de certificado que se usará para proteger el acceso SSH a las máquinas virtuales basadas en Linux. También se usa para especificar el certificado utilizado para proteger WinRM si se selecciona el transporte HTTPS. Si se omite tanto **:ssh_private_key_file** como **:ssh_certificate_file**, SSH usará solo la autenticación de contraseña.

* **:ssh_port**: Puerto público que se usará para la comunicación SSH.
  Si se omite, el valor de puerto SSH predeterminado será 22.

* **:vm_size**: Tamaño de la máquina virtual. Determina el tamaño de la memoria, el número de núcleos, el ancho de banda y otras características físicas de la máquina virtual. Consulte [Tamaños de máquinas virtuales y servicios en la nube de Azure][4] para ver las características físicas y los tamaños disponibles.

* **:winrm_transport**: Matriz de transportes disponible para su uso con WinRM. Los transportes válidos son http y https. Si se especifica "https" como transporte, también debe usar **:ssh_private_key_file** y **:ssh_certificate_file** para especificar el certificado usado para proteger las comunicaciones HTTPS.

A continuación encontrará un ejemplo de cómo crear una nueva máquina virtual que use una pequeña instancia de proceso, exponga públicamente los puertos para tráfico HTTP (puerto local 8080, puerto público 80) y HTTPS (443), y permita la autenticación de certificados para las sesiones SSH usando los archivos de certificado especificados:

    vm_params = {
      :vm_name => 'myvm',
      :vm_user => 'myuser',
      :password => 'mypassword',
      :image => 'b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-13_04-amd64-server-20130824-en-us-30GB',
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

### Enumeración de máquinas virtuales

Para enumerar las máquinas virtuales existentes de la suscripción de Azure, use el método **list_virtual_machines**. Este método devuelve una matriz de objetos **Azure::VirtualMachineManagement::VirtualMachine**:

    vm_mgr = Azure::VirtualMachineService.new
    virtual_machines = vm_mgr.list_virtual_machines

### Obtención de información acerca de una máquina virtual

Para obtener una instancia de **Azure::VirtualMachineManagement::VirtualMachine** para una máquina virtual concreta, use el método **get_virtual_machine** y proporcione los nombres de la máquina virtual y del servicio en la nube:

    vm_mgr = Azure::VirtualMachineService.new
    vm = vm_mgr.get_virtual_machine('myvm', 'mycloudservice')

### Eliminación de una máquina virtual

Para eliminar una máquina virtual, use el método **delete_virtual_machine** y proporcione los nombres de la máquina virtual y del servicio en la nube:

    vm_mgr = Azure::VirtualMachineService.new
    vm = vm_mgr.delete_virtual_machine('myvm', 'mycloudservice')

<div  class="dev-callout" markdown="1">
**Advertencia**
El método **delete_virtual_machine** elimina el servicio en la nube y todos los discos asociados a la máquina virtual.


</div>

### Apagado de una máquina virtual

Para apagar una máquina virtual, use el método
**shutdown_virtual_machine** y proporcione los nombres de la máquina virtual y del servicio en la nube:

    vm_mgr = Azure::VirtualMachineService.new
    vm = vm_mgr.shutdown_virtual_machine('myvm', 'mycloudservice')

### Inicio de una máquina virtual

Para iniciar una máquina virtual, use el método
**start_virtual_machine** y proporcione los nombres de la máquina virtual y del servicio en la nube:

    vm_mgr = Azure::VirtualMachineService.new
    vm = vm_mgr.start_virtual_machine('myvm', 'mycloudservice')

## <a name="vm-images"> </a>Trabajo con discos e imágenes de la máquina virtual

La operaciones en las imágenes de la máquina virtual se realizan usando la clase **Azure::VirtualMachineImageService**. La operaciones en los discos se realizan usando la clase **Azure::VirtualMachineImageManagement::VirtualMachineDiskManagementService**.

### Enumeración de imágenes de máquinas virtuales

Para enumerar las imágenes de las máquinas virtuales disponibles, use el método **list_virtual_machine_images**. Este método devuelve una matriz de objetos **Azure::VirtualMachineImageService**.

    image_mgr = Azure::VirtualMachineImageService.new
    images = image_mgr.list_virtual_machine_images

### Enumeración de discos

Para enumerar los discos de la suscripción de Azure, use el método **list_virtual_machine_disks**. Este método devuelve una matriz de objetos **Azure::VirtualMachineImageManagement::VirtualMachineDisk**.

    disk_mgr = Azure::VirtualMachineImageManagement::VirtualMachineDiskManagementService.new
    disks = disk_mgr.list_virtual_machine_disks

### Eliminación de un disco

Para eliminar un disco, use el método **delete_virtual_machine_disk** y especifique el nombre del disco que desee eliminar:

    disk_mgr = Azure::VirtualMachineImageManagement::VirtualMachineDiskManagementService.new
    disk_mgr.delete_virtual_machine_disk

## <a name="cloud-services"> </a>Trabajo con Servicios en la nube

Las operaciones de administración de Servicios en la nube de Azure se realizan usando la clase **Azure::CloudService**.

### Creación de un servicio en la nube

Para crear un servicio en la nube, use el método **create_cloud_service** y proporcione un nombre y un hash de opciones.
Las opciones válidas son:

* **:location**: *Obligatoria*. Región en la que se creará el servicio en la nube.

* **:description**: Descripción del servicio en la nube.

Lo siguiente crea un servicio en la nube en la región East US:

    cs_mgr = Azure::CloudService.new
    cs_mgr.create_cloud_service('mycloudservice', { :location => 'East US' })

### Enumeración de servicios en la nube

Para enumerar los servicios en la nube de la suscripción de Azure, use el método **list_cloud_services**. Este método devuelve una matriz de objetos **Azure::CloudServiceManagement::CloudService**:

    cs_mgr = Azure::CloudService.new
    cloud_services = cs_mgr.list_cloud_services

### Comprobación de la existencia de un servicio en la nube

Para comprobar si ya existe un servicio en la nube concreto, use el método **get_cloud_service** y proporcione el nombre del servicio en la nube. Devuelve **true** si ya existe un servicio en la nube con el nombre especificado; si no existe, devuelve **false**.

    cs_mgr = Azure::CloudService.new
    cs_exists = cs_mgr.get_cloud_service('mycloudservice')

### Eliminación de un servicio en la nube

Para eliminar un servicio en la nube, use el método
**delete_cloud_service** y proporcione el nombre del servicio en la
nube:

    cs_mgr = Azure::CloudService.new
    cs_mgr.delete_cloud_service('mycloudservice')

### Eliminación de una implementación

Para eliminar la implementación de un servicio en la nube, use el método **delete_cloud_service_deployment** y proporcione el nombre del servicio en la nube:

    cs_mgr = Azure::CloudService.new
    cs_mgr.delete_cloud_service_deployment('mycloudservice')

## <a name="storage-services"> </a>Trabajo con servicios de almacenamiento

Las operaciones de administración de Servicios en la nube de Azure se realizan usando la clase **Azure::StorageService**.

### Creación de una cuenta de almacenamiento

Para crear una cuenta de almacenamiento, use el método
**create_storage_account** y proporcione un nombre y un hash de opciones. Las opciones válidas son:

* **:location**: *Obligatoria*. Región en la que se creará la cuenta de almacenamiento.

* **:description**: Descripción de la cuenta de almacenamiento.

Lo siguiente crea una cuenta de almacenamiento en la región East US:

    storage_mgr = Azure::StorageService.new
    storage_mgr.create_storage_account('mystorage', { :location => 'East US' })

### Enumeración de cuentas de almacenamiento

Para obtener una lista con las cuentas de almacenamiento de la suscripción de Azure, use el método **list_storage_accounts**. Este método devuelve una matriz de objetos **Azure::StorageManagement::StorageAccount**:

    storage_mgr = Azure::StorageService.new
    accounts = storage_mgr.list_storage_accounts

### Comprobación de la existencia de una cuenta de almacenamiento

Para ver si existe una cuenta de almacenamiento concreta, use el método **get_storage_account** y especifique el nombre de la cuenta de almacenamiento. Este método devuelve **true** si ya existe la cuenta de almacenamiento o **false** si no existe.

    storage_mgr = Azure::StorageService.new
    store_exists = storage_mgr.get_storage_account('mystorage')

### Eliminación de una cuenta de almacenamiento

Para eliminar una cuenta de almacenamiento, use el método **delete_storage_account** y especifique el nombre de la cuenta de
almacenamiento:

    storage_mgr = Azure::StorageService.new
    storage_mgr.delete_storage_account('mystorage')

## <a name="next-steps"> </a>Pasos siguientes

Ahora que ya conoce los aspectos básicos de la creación de máquinas virtuales de Azure mediante programación, utilice estos vínculos para obtener más información acerca de cómo trabajar con las máquinas virtuales.

* Visite la página de características de las [máquinas virtuales][5].
* Consulte la referencia de MSDN: [Máquinas virtuales][6]
* Más información acerca de cómo hospedar una [Aplicación de Ruby on
  Rails en una máquina virtual][7]



[1]: http://msdn.microsoft.com/es-es/library/windowsazure/ee460799.aspx
[2]: https://manage.windowsazure.com
[3]: http://www.windowsazure.com/es-es/manage/install-and-configure-cli/
[4]: http://msdn.microsoft.com/es-es/library/windowsazure/dn197896.aspx
[5]: http://www.windowsazure.com/es-es/documentation/services/virtual-machines/
[6]: http://msdn.microsoft.com/es-es/library/windowsazure/jj156003.aspx
[7]: http://www.windowsazure.com/es-es/develop/ruby/tutorials/web-app-with-linux-vm/