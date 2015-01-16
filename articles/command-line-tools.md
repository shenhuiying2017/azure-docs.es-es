<properties urlDisplayName="Command-Line Tools" pageTitle="Herramientas de línea de comandos de Azure para Mac y Linux" metaKeywords="Azure command-line, Azure tools Mac, Azure tools Linux" description="Obtenga información acerca del uso de la herramienta de línea de comandos para Mac y Linux en Azure." metaCanonical="" services="web-sites,virtual-machines,mobile-services,cloud-services" documentationCenter="" title="" authors="carolz" solutions="" manager="need to identify contact" editor="" />

<tags ms.service="multiple" ms.workload="multiple" ms.tgt_pltfrm="command-line-interface" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="carolz" />

#Herramienta de línea de comandos de Azure para Mac y Linux

Esta herramienta proporciona funciones para la creación, implementación y administración de máquinas virtuales, sitios web y Servicios móviles de Azure desde escritorios de Mac y Linux. Su funcionalidad es parecida a la proporcionada por los cmdlets de Windows PowerShell que se han instalado con los SDK de Azure para .NET, Node.JS y PHP.

Para instalar la herramienta en Mac, descargue y ejecute el [instalador del SDK de Azure](http://go.microsoft.com/fwlink/?LinkId=252249).

Para instalar la herramienta en Linux, instale la última versión de Node.JS y, a continuación, use NPM para instalar:

    npm install azure-cli -g

Se muestran parámetros opcionales entre corchetes (por ejemplo, [parámetro]). Los demás parámetros son obligatorios.

Además de los parámetros opcionales específicos de los comandos documentados aquí, existen tres parámetros opcionales que pueden usarse para mostrar el resultado detallado como opciones de solicitud y códigos de estado. El parámetro -v ofrece un resultado detallado y el parámetro -w ofrece un resultado incluso más detallado. La opción --json mostrará el resultado en formato json sin procesar.

**Tabla de contenido:**

* [Administración de la información de la cuenta y publicación de la configuración](#Manage_your_account_information_and_publish_settings)
* [Comandos para administrar máquinas virtuales de Azure](#Commands_to_manage_your_Azure_virtual_machines)
* [Comandos para administrar extremos de máquinas virtuales de Azure](#Commands_to_manage_your_Azure_virtual_machine_endpoints)
* [Comandos para administrar imágenes de máquinas virtuales de Azure](#Commands_to_manage_your_Azure_virtual_machine_images)
* [Comandos para administrar discos de datos de máquinas virtuales de Azure](#Commands_to_manage_your_Azure_virtual_machine_data_disks)
* [Comandos para administrar servicios en la nube de Azure](#Commands_to_manage_your_Azure_cloud_services)
* [Comandos para administrar certificados de Azure](#Commands_to_manage_your_Azure_certificates)
* [Comandos para administrar sitios web](#Commands_to_manage_your_web_sites)
* [Comandos para administrar Servicios Móviles de Azure](#Commands_to_manage_mobile_services)
* [Administración de la configuración local de las herramientas](#Manage_tool_local_settings)
* [Comandos para administrar el bus de servicio](#Commands_to_manage_service_bus)
* [Comandos para administrar objetos de almacenamiento](#Commands_to_manage_your_Storage_objects)
* [Comandos para administrar bases de datos SQL](#Commands_to_manage_sql)
* [Comandos para administrar redes virtuales](#Commands_to_manage_vnet)

##<a name="Manage_your_account_information_and_publish_settings"></a>Administración de la información de la cuenta y publicación de la configuración
La herramienta usa la información de la suscripción de Azure para la conexión a su cuenta. Esta información se puede obtener en el portal de Azure en un archivo de configuración de publicación como se describe aquí. Puede importar el archivo de configuración de publicación como un valor de configuración local permanente que la herramienta usará para operaciones posteriores. Solo tiene que importar la configuración de publicación una vez.

**account download [opciones]**

Este comando permite iniciar un explorador para descargar el archivo .publishsettings desde el portal de Azure.

	~$ azure account download
	info:   Executing command account download
	info:   Launching browser to https://windows.azure.com/download/publishprofile.aspx
	help:   Save the downloaded file, then execute the command
	help:   account import <file>
	info:   account download command OK

**account import [opciones] <Archivo>**


Este comando permite importar un archivo publishsettings o un certificado de manera que la herramienta pueda usarlos en el futuro.

	~$ azure account import publishsettings.publishsettings
	info:   Importing publish settings file publishsettings.publishsettings
	info:   Found subscription: 3-Month Free Trial
	info:   Found subscription: Pay-As-You-Go
	info:   Setting default subscription to: 3-Month Free Trial
	warn:   The 'publishsettings.publishsettings' file contains sensitive information.
	warn:   Remember to delete it now that it has been imported.
	info:   Account publish settings imported successfully

<div class="dev-callout"><b>Nota:</b>
   <p>Este archivo publishsettings puede contener información (es decir, el nombre de suscripción y el identificador) sobre más de una suscripción. Cuando importa el archivo publishsettings, la primera suscripción se usa como descripción predeterminada. Para usar una suscripción diferente, ejecute el siguiente comando.</p>
<code>~$ azure config set subscription <other-subscription-id></code>
</div>

**account clear [opciones]**

Este comando permite quitar la configuración de publicación almacenada que se ha importado. Use este comando si ha acabado de usar la herramienta en esta máquina y quiere asegurarse de que la herramienta no puede usarse con la cuenta en el futuro.

	~$ azure account clear
	Clearing account info.
	info:   OK

**account list [opciones]**

Permite ver las suscripciones importadas.

	~$ azure account list
	info:    Executing command account list
	data:    Name                                    Id
	       Current
	data:    --------------------------------------  -------------------------------
	-----  -------
	data:    Forums Subscription                     8679c8be-3b05-49d9-b8fb  true
	data:    Evangelism Team Subscription            9e672699-1055-41ae-9c36  false
	data:    MSOpenTech-Prod                         c13e6a92-706e-4cf5-94b6  false

**account set [opciones] <suscripción>**

Permite establecer la suscripción actual.

###Comandos para administrar los grupos de afinidad

**account affinity-group list [opciones]**

Este comando permite ver los grupos de afinidad de Azure.

Los grupos de afinidad pueden configurarse cuando un grupo de máquinas virtuales amplían varias máquinas físicas. El grupo de afinidad especifica que las máquinas físicas deben estar lo más cerca posible de las otras para reducir la latencia de red.
 
	~$ azure account affinity-group list
	+ Fetching affinity groups
	data:   Name                                  Label   Location
	data:   ------------------------------------  ------  --------
	data:   535EBAED-BF8B-4B18-A2E9-8755FB9D733F  opentec  West US
	info:   account affinity-group list command OK

**account affinity-group create [opciones] <nombre>**

Este comando permite crear un nuevo grupo de afinidad.

	~$ azure account affinity-group create opentec -l "West US"
	info:    Executing command account affinity-group create
	+ Creating affinity group
	info:    account affinity-group create command OK

**account affinity-group show [opciones] <nombre>**

Este comando permite ver la información del grupo de afinidad.

	~$ azure account affinity-group show opentec
	info:    Executing command account affinity-group show
	+ Getting affinity groups
	data:    $ xmlns "http://schemas.microsoft.com/windowsazure"
	data:    $ xmlns:i "http://www.w3.org/2001/XMLSchema-instance"
	data:    Name "opentec"
	data:    Label "b3BlbnRlYw=="
	data:    Description $ i:nil "true"
	data:    Location "West US"
	data:    HostedServices ""
	data:    StorageServices ""
	data:    Capabilities Capability 0 "PersistentVMRole"
	data:    Capabilities Capability 1 "HighMemory"
	info:    account affinity-group show command OK

**account affinity-group delete [opciones] <nombre>**

Este comando permite quitar el grupo de afinidad especificado.

	~$ azure account affinity-group delete opentec
	info:    Executing command account affinity-group delete
	Delete affinity group opentec? [y/n] y
	+ Deleting affinity group
	info:    account affinity-group delete command OK

###Comandos para administrar el entorno de cuenta

**account env list [opciones]**

Permite ver los entornos de cuenta.

	C:\windows\system32>azure account env list
	info:    Executing command account env list
	data:    Name
	data:    ---------------
	data:    AzureCloud
	data:    AzureChinaCloud
	info:    account env list command OK

**account env show [opciones] [entorno]**

Permite ver la información del entorno de cuenta.

	~$ azure account env show
	info:    Executing command account env show
	Environment name: AzureCloud
	data:    Environment publishingProfile  http://go.microsoft.com/fwlink/?LinkId=2544
	data:    Environment portal  http://go.microsoft.com/fwlink/?LinkId=2544
	info:    account env show command OK

**account env add [opciones] [entorno]**

Este comando permite agregar un entorno a la cuenta.

**account env set [opciones] [entorno]**

Este comando permite establecer el entorno de cuenta.

**account env delete [opciones] [entorno]**

Este comando permite eliminar el entorno especificado de la cuenta.

##<a name="Commands_to_manage_your_Azure_virtual_machines"></a>Comandos para administrar máquinas virtuales de Azure
El siguiente diagrama muestra cómo se han hospedado las máquinas virtuales de Azure en el entorno de implementación de producción de un servicio en la nube de Azure.
 
![Azure Technical Diagram](./media/command-line-tools/architecturediagram.jpg)	

**create-new** permite crear la unidad en el almacenamiento de blobs (es decir, e:\ en el diagrama); **attach** permite conectar un disco ya creado pero sin conectar a una máquina virtual.

**vm create [opciones] <nombre-dns> <imagen> <nombreUsuario> [contraseña]**

Este comando permite crear una nueva máquina virtual de Azure. De forma predeterminada, cada máquina virtual (VM) se crea en su propio servicio en la nube; sin embargo, puede especificar que se agregue una máquina virtual a un servicio en la nube existente mediante la opción -c como aparece aquí.

El comando vm create, como el portal de Azure, solo permite crear máquinas virtuales en el entorno de implementación de la producción. No existe ninguna opción para crear una máquina virtual en el entorno de implementación de almacenamiento provisional de un servicio en la nube. Si su suscripción no tiene una cuenta de almacenamiento de Azure, el comando creará una.

Puede especificar una ubicación a través del parámetro --location o puede especificar un grupo de afinidad a través del parámetro --affinity-group. Si no se proporciona ninguno, se le solicitará que lo haga a partir de una lista de ubicaciones válidas.

La contraseña proporcionada debe tener una longitud de entre 8 y 123 caracteres y cumplir con los requisitos de complejidad de la contraseña del sistema operativo que esté usando para esta máquina virtual.

Si cree que va a necesitar usar SSH para administrar una máquina virtual de Linux implementada (que suele ser normalmente el caso), debe habilitar SSH a través de la opción -e cuando cree la máquina virtual. No es posible habilitar SSH después de que se haya creado la máquina virtual.

Las máquinas virtuales de Windows pueden habilitar RDP más tarde agregando el puerto 3389 como extremo.

Los siguientes parámetros opcionales son compatibles con este comando:

**-c, --connect** permite crear la máquina virtual dentro de una implementación ya creada en un servicio de hospedaje. Si no se usa -vmname con esta opción, el nombre de la nueva máquina virtual se generará automáticamente.<br />
**-n, --vm-name** permite especificar el nombre de la máquina virtual. Este parámetro toma el servicio de hospedaje de forma predeterminada. Si no se especifica -vmname, el nombre de la nueva máquina virtual se genera como <nombre-servicio><identificador>, donde <identificador> es el número de máquinas virtuales existentes en el servicio más 1. Por ejemplo, si usa este comando para agregar una nueva máquina virtual a un servicio de hospedaje MyService que cuente con una máquina virtual existente, el nombre de la nueva máquina virtual será MyService2.   <br /> 
**-u, --blob-url** permite especificar una dirección URL de almacenamiento de blobs desde la que puede crear un disco del sistema de máquina virtual. <br />
**-z, --vm-size** permite especificar el tamaño de la máquina virtual. Los valores válidos son "extrasmall", "small", "medium", "large" y "extralarge". El valor predeterminado es "small". <br />
**-r** permite agregar conectividad RDP a una máquina virtual de Windows. <br />
**-e, --ssh** permite agregar conectividad SSH a una máquina virtual de Windows. <br />
**-t, --ssh-cert** permite especificar el certificado SSH. <br />
**-s** La suscripción <br />
**-o, --community** La imagen especificada es una imagen de comunidad <br />
**-w** El nombre de red virtual <br/>
**-l, --location** especifica la ubicación (por ejemplo, "Centro y norte de EE. UU."). <br />
**-a, --affinity-group** permite especificar el grupo de afinidad.<br />
**-w, --virtual-network-name** permite especificar la red virtual en la que agrega la nueva máquina virtual. Las redes virtuales pueden configurarse y administrarse desde el portal de Azure.<br />
**-b, --subnet-names** permite especificar los nombres de subred que asignar a la máquina virtual.

En este ejemplo, MSFT__Win2K8R2SP1-120514-1520-141205-01-es-es-30GB es una imagen proporcionada por la plataforma. Para obtener más información sobre las imágenes del sistema operativo, consulte vm image list.

	~$ azure vm create my-vm-name MSFT__Windows-Server-2008-R2-SP1.11-29-2011 username --location "Western US" -r
	info:   Executing command vm create
	Enter VM 'my-vm-name' password: ************                                     
	info:   vm create command OK

**vm create-from <nombre-dns> <archivo-rol>**

Este comando permite crear una nueva máquina virtual de Azure desde un archivo de rol JSON.

	~$ azure vm create-from my-vm example.json
	info:   OK

**vm list [opciones]**

Este comando permite ver las máquinas virtuales de Azure. La opción -json especifica que los resultados se devuelven con un formato JSON sin procesar. 

	~$ azure vm list
	info:   Executing command vm list
	data:   DNS Name                          VM Name      Status                  
	data:   --------------------------------  -----------  ---------
	data:   my-vm-name.cloudapp-preview.net        my-vm        ReadyRole
	info:   vm list command OK

**vm location list [opciones]**

Este comando muestra todas las ubicaciones de cuenta de Azure disponibles.

	~$ azure vm location list
	info:   Executing command vm location list
	data:   Name                   Display Name                                    
	data:   ---------------------  ------------
	data:   Azure Preview  West US     
	info:   account location list command OK

**vm show [opciones] <nombre>**

Este comando muestra información sobre una máquina virtual de Azure. La opción -json especifica que los resultados se devuelven con un formato JSON sin procesar. 

	~$ azure vm show my-vm
	info:   Executing command vm show
	data:   {                                                                      
	data:       InstanceSize: 'Small',
	data:       InstanceStatus: 'ReadyRole',
	data:       DataDisks: [],
	data:       IPAddress: '10.26.192.206',
	data:       DNSName: 'my-vm.cloudapp.net',
	data:       InstanceStateDetails: {},
	data:       VMName: 'my-vm',
	data:       Network: {
	data:           Endpoints: [
	data:               {
	data:                   Protocol: 'tcp',
	data:                   Vip: '65.52.250.250',
	data:                   Port: '63238' ,
	data:                   LocalPort: '3389',
	data:                   Name: 'RemoteDesktop'
	data:               }
	data:           ]
	data:       },
	data:       Image: 'MSFT__Windows-Server-2008-R2-SP1.11-29-2011',
	data:       OSVersion: 'WA-GUEST-OS-1.18_201203-01'
	data:   } 
	info:   vm show command OK

**vm delete [opciones] <nombre>**

Este comando permite eliminar una máquina virtual de Azure. De forma predeterminada, este comando no elimina el blob de Azure desde el que se crean el disco de datos y el disco del sistema operativo. Para eliminar el blob y la máquina virtual en la que se basa, especifique la opción -b.

	~$ azure vm delete my-vm 
	info:   Executing command vm delete
	info:   vm delete command OK

**vm start [opciones] <nombre>**

Este comando permite iniciar una máquina virtual de Azure.

	~$ azure vm start my-vm
	info:   Executing command vm start
	info:   vm start command OK

**vm restart [opciones] <nombre>**

Este comando permite reiniciar una máquina virtual de Azure.

	~$ azure vm restart my-vm
	info:   Executing command vm restart
	info:   vm restart command OK

**vm shutdown [opciones] <nombre>**

Este comando permite apagar una máquina virtual de Azure. Puede usar la opción -p para especificar que el recurso de proceso no se lance en el apagado.

```
~$ azure vm shutdown my-vm
info:   Executing command vm shutdown
info:   vm shutdown command OK  
```

**vm capture <nombre-máquina-virtual> <nombre-imagen-destino>**

Este comando permite capturar una imagen de máquina virtual de Azure.

No puede capturarse una imagen de máquina virtual mientras exista un estado en la máquina virtual, a no ser que el estado sea **Detenido**.

	~$ azure.cmd vm capture my-vm mycaptureimagename --delete
	info:   Executing command vm capture
	+ Fetching VMs
	+ Capturing VM
	info:   vm capture command OK

**vm export [opciones] <nombre-máquina-virtual> <ruta-acceso-archivo>**

Este comando permite exportar una imagen de la máquina virtual de Azure a un archivo.

	~$ azure vm export "myvm" "C:\"
	info:    Executing command vm export
	+ Getting virtual machines
	+ Exporting the VM
	info:   vm export command OK

##<a name="Commands_to_manage_your_Azure_virtual_machine_endpoints"></a>Comandos para administrar extremos de máquinas virtuales de Azure
El siguiente diagrama muestra la arquitectura de una implementación típica de varias instancias de una máquina virtual. Tenga en cuenta que en este ejemplo, el puerto 3389 está abierto en cada máquina virtual (para el acceso RDP) y existe también una dirección IP interna (por ejemplo, 168.55.11.1) en cada máquina virtual que utilice el equilibrador de carga para dirigir el tráfico a la máquina virtual. Esta dirección IP interna también puede usarse para la comunicación entre máquinas virtuales.

![azurenetworkdiagram](./media/command-line-tools/networkdiagram.jpg)
 
Las solicitudes externas a máquinas virtuales pasan por un equilibrador de carga. Debido a esto, las solicitudes no pueden especificarse en una máquina virtual determinada en las implementaciones con varias máquinas virtuales. Para las implementaciones con varias máquinas virtuales, la asignación de puertos debe configurarse entre las máquinas virtuales (puerto-máquina-virtual) y el equilibrador de carga (puerto-equilibrador-carga).

**vm endpoint create <nombre-máquina-virtual> <puerto-equilibrador-carga> [puerto-máquina-virtual]**

Este comando permite crear un extremo de máquina virtual. También puede usar -u o --enable-direct-server-return para especificar si habilitar el retorno de servidor directo en este extremo, que se encuentra deshabilitado de forma predeterminada.

	~$ azure vm endpoint create my-vm 8888 8888
	azure vm endpoint create my-vm 8888 8888
	info:   Executing command vm endpoint create
	+ Fetching VM
	+ Reading network configuration
	+ Updating network configuration
	info:   vm endpoint create command OK

**vm endpoint create-multiple [opciones] <nombre-máquina-virtual> <puerto-equilibrador-carga>[:<puerto-máquina-virtual>[:<protocolo>[:<nombre-conjunto-equilibrador-carga>[:<protocolo-prob>:<puerto-prob-equilibrador-carga>[:<ruta-acceso-prob>]]]]] ]{1-*}**

Cree varios extremos de máquina virtual. También puede usar -u o --enable-direct-server-return para especificar si habilitar el retorno de servidor directo en este extremo, que se encuentra deshabilitado de forma predeterminada.

**vm endpoint delete <nombre-máquina-virtual> <puerto-equilibrador-carga>**

Este comando permite eliminar un extremo de máquina virtual.

	~$ azure vm endpoint delete my-vm 8888
	azure vm endpoint delete my-vm 8888
	info:   Executing command vm endpoint delete
	+ Fetching VM
	+ Reading network configuration
	+ Updating network configuration
	info:   vm endpoint delete command OK

**vm endpoint list <nombre-máquina-virtual>**

Este comando permite ver todos los extremos de máquina virtual. La opción -json especifica que los resultados se devuelven con un formato JSON sin procesar. 

	~$ azure vm endpoint list my-linux-vm
	data:   Name  External Port  Local Port                                        
	data:   ----  -------------  ----------
	data:   ssh   22             22

**vm endpoint update [opciones] <nombre-máquina-virtual> <nombre-extremo>**

Este comando permite actualizar vm endpoint con nuevos valores mediante estas opciones.

    -n, --endpoint-name <name>          the new endpoint name
    -lo, --lb-port <port>                the new load balancer port
    -t, --vm-port <port>                the new local port
    -o, --endpoint-protocol <protocol>  the new transport layer protocol for port (tcp or udp) 

**vm endpoint show [opciones] <nombre-máquina-virtual>**

Este comando permite ver la información de los extremos en una máquina virtual.

	~$ azure vm endpoint show "mycouchvm"
	info:    Executing command vm endpoint show
	+ Getting virtual machines
	data:    Network Endpoints 0 LoadBalancedEndpointSetName "CouchDB_EP-5984"
	data:    Network Endpoints 0 LocalPort "5984"
	data:    Network Endpoints 0 Name "CouchDB_EP"
	data:    Network Endpoints 0 Port "5984"
	data:    Network Endpoints 0 Protocol "tcp"
	data:    Network Endpoints 0 Vip "168.61.9.97"
	data:    Network Endpoints 1 LoadBalancedEndpointSetName "CouchEP_2-2020"
	data:    Network Endpoints 1 LocalPort "2020"
	data:    Network Endpoints 1 Name "CouchEP_2"
	data:    Network Endpoints 1 Port "2020"
	data:    Network Endpoints 1 Protocol "tcp"
	data:    Network Endpoints 1 Vip "168.61.9.97"
	data:    Network Endpoints 2 LocalPort "3389"
	data:    Network Endpoints 2 Name "RemoteDesktop"
	data:    Network Endpoints 2 Port "3389"
	data:    Network Endpoints 2 Protocol "tcp"
	data:    Network Endpoints 2 Vip "168.61.9.97"
	info:    vm endpoint show command OK

##<a name="Commands_to_manage_your_Azure_virtual_machine_images"></a>Comandos para administrar imágenes de máquinas virtuales de Azure

Las imágenes de máquina virtual son capturas de máquinas virtuales ya configuradas que pueden replicarse según sea necesario.

**vm image list [opciones]**

Este comando permite obtener una lista de imágenes de máquina virtual. Existen tres tipos de imágenes: imágenes creadas por Microsoft, que tiene el prefijo "MSFT", imágenes creadas por terceros, que tienen normalmente el prefijo del nombre del proveedor y las imágenes creadas por usted. Para crear imágenes, puede capturar una máquina virtual existente o crear una imagen desde un .vhd personalizado cargado en el almacenamiento de blobs. Para obtener más información sobre el uso de .vhd personalizado, consulte vm image create.
La opción -json especifica que los resultados se devuelven con un formato JSON sin procesar. 

	~$ azure vm image list
	data:   Name                                                                   Category   OS
	data:   ---------------------------------------------------------------------  ---------  -------
	data:   CANONICAL__Canonical-Ubuntu-12-04-20120519-2012-05-19-es-es-30GB.vhd   Canonical  Linux
	data:   MSFT__Windows-Server-2008-R2-SP1.11-29-2011                            Microsoft  Windows
	data:   MSFT__Windows-Server-2008-R2-SP1-with-SQL-Server-2012-Eval.11-29-2011  Microsoft  Windows
	data:   MSFT__Windows-Server-8-Beta.es-es.30GB.2012-03-22                      Microsoft  Windows
	data:   MSFT__Windows-Server-8-Beta.2-17-2012                                  Microsoft  Windows
	data:   MSFT__Windows-Server-2008-R2-SP1.es-es.30GB.2012-3-22                  Microsoft  Windows
	data:   OpenLogic__OpenLogic-CentOS-62-20120509-es-es-30GB.vhd                 OpenLogic  Linux
	data:   SUSE__SUSE-Linux-Enterprise-Server-11SP2-20120521-es-es-30GB.vhd       SUSE       Linux
	data:   SUSE__OpenSUSE64121-03192012-es-es-15GB.vhd                            SUSE       Linux
	data:   WIN2K8-R2-WINRM                                                        User       Windows
	info:   vm image list command OK   

**vm image show [opciones] <nombre>**

Este comando permite ver la información de una imagen de máquina virtual.

	~$ azure vm image show MSFT__Windows-Server-2008-R2-SP1.11-29-2011
	+ Fetching VM image
	info:   Executing command vm image show
	data:   {                                                                      
	data:       Label: 'Windows Server 2008 R2 SP1, Nov 2011',
	data:       Name: 'MSFT__Windows-Server-2008-R2-SP1.11-29-2011',
	data:       Description: 'Microsoft Windows Server 2008 R2 SP1',
	data:       @: { xmlns: 'http://schemas.microsoft.com/windowsazure', xmlns:i: 'http://www.w3.org/2001/XMLSchema-instance' },
	data:       Category: 'Microsoft',
	data:       OS: 'Windows',
	data:       Eula: 'http://www.microsoft.com',
	data:       LogicalSizeInGB: '30'
	data:   }
	info:   vm image show command OK 

**vm image delete [opciones] <nombre>**

Este comando permite eliminar una imagen de máquina virtual.

	~$ azure vm image delete my-vm-image
	info:   Executing command vm image delete
	info:   VM image deleted: my-vm-image                                         
	info:   vm image delete command OK

**vm image create <nombre> [ruta-acceso-origen]**

Este comando permite crear una imagen de máquina virtual. Los archivos .vhd personalizados se cargan en el almacenamiento de blobs y, a continuación, la imagen de máquina virtual se crea a partir de él. A continuación, use esta imagen de máquina virtual para crear una máquina virtual. Los parámetros de ubicación y sistema operativo son obligatorios.

Algunos sistemas imponen límites de descriptor de archivos por proceso. Si se supera este límite, la herramienta muestra un error de límite de descriptor de archivos. Puede ejecutar el comando de nuevo con el parámetro -p <número> para reducir el número máximo de cargas paralelas. El número máximo predeterminado de cargas paralelas es 96.

	~$ azure vm image create mytestimage ./Sample.vhd -o windows -l "West US"
	info:   Executing command vm image create
	+ Retrieving storage accounts
	info:   VHD size : 13 MB
	info:   Uploading 13312.5 KB
	Requested:100.0% Completed:100.0% Running: 105 Time:    8s Speed:  1721 KB/s
	info:   http://myaccount.blob.core.azure.com/vm-images/Sample.vhd is uploaded successfully
	info:   vm image create command OK

##<a name="Commands_to_manage_your_Azure_virtual_machine_data_disks"></a>Comandos para administrar discos de datos de máquinas virtuales de Azure

Los discos de datos son archivos .vhd en el almacenamiento de blobs que puede usar la máquina virtual. Para obtener más información sobre cómo se implementan los discos de datos en el almacenamiento de blobs, consulte el diagrama técnico de Azure mostrado anteriormente. 

Los comandos para conectar discos de datos (azure vm disk attach y azure vm disk attach-new) asignan un número de unidad lógica (LUN) al disco de datos conectado como requiere el protocolo SCSI. Al primer disco de datos conectado a una máquina virtual se le asigna LUN 0, al siguiente LUN 1, y así sucesivamente.

Cuando desconecte un disco de datos con el comando azure vm disk detach, use el parámetro <lun> para indicar qué disco desconectar. 

<div class="dev-callout"><b>Nota:</b>
   <p>Tenga en cuenta que siempre debe desconectar los discos de datos en orden inverso, empezando por el LUN con el número mayor que se haya asignado. La capa SCSI de Linux no es compatible con la desconexión de un LUN con un número inferior si hay un LUN con número superior conectado. Por ejemplo, no debe desconectar LUN 0 si LUN 1 sigue estando conectado.</p>
</div>

**vm disk show [opciones] <nombre>**

Este comando muestra información sobre un disco de Azure.

	~$ azure vm disk show anucentos-anucentos-0-20120524070008
	info:   Executing command vm disk show
	data:   AttachedTo DeploymentName "mycentos"
	data:   AttachedTo HostedServiceName "myanucentos"
	data:   AttachedTo RoleName "myanucentos"
	data:   OS "Linux"
	data:   Location "Azure Preview"
	data:   LogicalDiskSizeInGB "30"
	data:   MediaLink "http://mystorageaccount.blob.core.azure-preview.com/vhd-store/mycentos-cb39b8223b01f95c.vhd"
	data:   Name "mycentos-mycentos-0-20120524070008"
	data:   SourceImageName "OpenLogic__OpenLogic-CentOS-62-20120509-es-es-30GB.vhd"
	info:   vm disk show command OK

**vm disk list [opciones] [nombre>]**

Este comando permite ver los discos de Azure o los discos conectados a una máquina virtual especificada. Si se ejecuta con un parámetro de nombre de máquina virtual, devuelve todos los discos conectados a la máquina virtual. Lun 1 se crea con la máquina virtual y los demás discos que aparecen se conectan de forma independiente.

	~$ azure vm disk list mycentos
	info:   Executing command vm disk list
	data:   Lun  Size(GB)  Blob-Name
	data:   ---  --------  --------------------------------
	data:   1    30        mycentos-cb39b8223b01f95c.vhd
	data:   2    10        mycentos-e3f0d717950bb78d.vhd
	info:   vm disk list command OK                                               

La ejecución de este comando sin un parámetro de nombre de máquina virtual devuelve todos los discos.

	~$ azure vm disk list 
	data:   Name                                        OS
	data:   ------------------------------------------  -------
	data:   mycentos-mycentos-0-20120524070008          Linux
	data:   mycentos-mycentos-2-20120525055052
	data:   mywindows-winvm-20120522223119              Windows
	info:   vm disk list command OK

**vm disk delete [opciones] <nombre>**

Este comando elimina un disco de Azure de un repositorio personal. El disco debe desconectarse de la máquina virtual antes de eliminarlo.

	~$ azure vm disk delete mycentos-mycentos-2-20120525055052
	info:   Executing command vm disk delete
	info:   Disk deleted: mycentos-mycentos-2-20120525055052                  
	info:   vm disk delete command OK

**vm disk create <nombre> [ruta-acceso-origen]**

Este comando permite cargar y registrar un disco de Azure. --blob-url, --location o --affinity-group deben especificarse. Si usa este comando con [ruta-acceso-origen], el archivo .vhd especificado se carga y se crea una nueva imagen. Puede conectar esta imagen a una máquina virtual mediante vm disk attach.

Algunos sistemas imponen límites de descriptor de archivos por proceso. Si se supera este límite, la herramienta muestra un error de límite de descriptor de archivos. Puede ejecutar el comando de nuevo con el parámetro -p <número> para reducir el número máximo de cargas paralelas. El número máximo predeterminado de cargas paralelas es 96. 

	~$ azure vm disk create my-data-disk ~/test.vhd --location "Western US"
	info:   Executing command vm disk create
	info:   VHD size : 10 MB                                                       
	info:   Uploading 10240.5 KB
	Requested:100.0% Completed:100.0% Running:  81 Time:   11s Speed:   952 KB/s 
	info:   http://account.blob.core.azure.com/disks/test.vhd is uploaded successfully
	info:   vm disk create command OK

**vm disk upload [opciones] <ruta-acceso-origen> <url-blob> <clave-cuenta-almacenamiento>**

Este comando permite cargar vm disk.

	~$ azure vm disk upload "http://sourcestorage.blob.core.windows.net/vhds/sample.vhd" "http://destinationstorage.blob.core.windows.net/vhds/sample.vhd" "DESTINATIONSTORAGEACCOUNTKEY"
	info:   Executing command vm disk upload                                                      
	info:   Uploading 12351.5 KB
	info:   vm disk upload command OK

**vm disk attach <nombre-máquina-virtual> <nombre-imagen-disco>**

Este comando permite conectar un disco existente en el almacenamiento de blogs a una máquina virtual existente implementada en un servicio en la nube.

	~$ azure vm disk attach my-vm my-vm-my-vm-2-201242418259
	info:   Executing command vm disk attach
	info:   vm disk attach command OK

**vm disk attach-new <nombre-máquina-virtual> <tamaño-en-gb> [url-blob]**

Este comando permite conectar un disco de datos a una máquina virtual de Azure. En este ejemplo, 20 es el tamaño del nuevo disco, en gigabytes, que se va a conectar. Puede opcionalmente usar una dirección URL de blob como el último argumento para especificar de manera explícita el blob de destino que crear. Si no especifica una dirección URL de blob, un objeto de blob se generará automáticamente.

	~$ azure vm disk attach-new nick-test36 20 http://nghinazz.blob.core.azure-preview.com/vhds/vmdisk1.vhd
	info:   Executing command vm disk attach-new
	info:   vm disk attach-new command OK  

**vm disk detach <nombre-máquina-virtual> <lun>**

Este comando desconecta un disco de datos conectado a una máquina virtual de Azure. <lun> identifica el disco que se va a desconectar. Para obtener una lista de discos asociados a un disco antes de desconectarlo, use vm disk-list <nombre-máquina-virtual>.

	~$ azure vm disk detach my-vm 2
	info:   Executing command vm disk detach
	info:   vm disk detach command OK

##<a name="Commands_to_manage_your_Azure_cloud_services"></a>Comandos para administrar servicios en la nube de Azure

Los servicios en la nube de Azure son aplicaciones y servicios hospedados en roles web y roles de trabajo. Los siguientes comandos pueden usarse para administrar servicios en la nube de Azure.

**service create [opciones] <nombreServicio>**

Este comando permite crear un nuevo servicio en la nube.

	~$ azure service create newservicemsopentech
	info:    Executing command service create
	+ Getting locations
	help:    Location:
	  1) East Asia
	  2) Southeast Asia
	  3) North Europe
	  4) West Europe
	  5) East US
	  6) West US
	  : 6
	+ Creating cloud service
	data:    Cloud service name newservicemsopentech
	info:    service create command OK

**service show [opciones] <nombreServicio>**

Este comando permite ver la información de un servicio en la nube de Azure.

	~$ azure service show newservicemsopentech
	info:    Executing command service show
	+ Getting cloud service
	data:    Name newservicemsopentech
	data:    Url https://management.core.windows.net/9e672699-1055-41ae-9c36-e85152f2e352/services/hostedservices/newservicemsopentech
	data:    Properties location West US
	data:    Properties label newservicemsopentech
	data:    Properties status Created
	data:    Properties dateCreated
	data:    Properties dateLastModified
	info:    service show command OK

**service list [opciones]**

Este comando permite ver los servicios en la nube de Azure.

	~$ azure service list
	info:   Executing command service list
	data:   Name         Status                                                    
	data:   -----------  -------
	data:   service1     Created
	data:   service2     Created
	info:   service list command OK

**service delete [opciones] <nombre>**

Este comando permite eliminar un servicio en la nube de Azure.

	~$ azure service delete myservice
	info:   Executing command service delete myservice 
	info:   cloud-service delete command OK

Para forzar la eliminación, use el parámetro `-q`.


##<a name="Commands_to_manage_your_Azure_certificates"></a>Comandos para administrar certificados de Azure

Los certificados de servicio de Azure son certificados SSL conectados a la cuenta de Azure. Para obtener más información sobre los certificados de Azure, vea [Administrar certificados](http://msdn.microsoft.com/es-es/library/azure/gg981929.aspx).

**service cert list [opciones]**

Este comando permite ver los certificados de Azure.

	~$ azure service cert list
	info:   Executing command service cert list
	+ Fetching cloud services                                                      
	+ Fetching certificates                                                        
	data:   Service   Thumbprint                                Algorithm
	data:   --------  ----------------------------------------  ---------
	data:   myservice  262DBF95B5E61375FA27F1E74AC7D9EAE842916C  sha1     
	info:   service cert list command OK

**service cert create <prefijo-dns> <archivo> [contraseña]**

Este comando permite cargar un certificado. Deje la solicitud de la contraseña en blanco para los certificados que no tengan protección mediante contraseña.

	~$ azure service cert create nghinazz ~/publishSet.pfx
	info:   Executing command service cert create
	Cert password: 
	+ Creating certificate                                                         
	info:   service cert create command OK

**service cert delete [opciones] <huella-digital>**

Este comando permite eliminar un certificado.

	~$ azure service cert delete 262DBF95B5E61375FA27F1E74AC7D9EAE842916C
	info:   Executing command service cert delete
	+ Deleting certificate                                                         
	info:   nghinazz : cert deleted
	info:   service cert delete command OK


##<a name="Commands_to_manage_your_web_sites"></a>Commands to manage your websites

Un sitio web de Azure es una configuración web a la que puede obtener acceso mediante un URI. Los sitios web se hospedan en máquinas virtuales, pero no tiene que pensar en la información de creación e implementación de la máquina virtual. De esa información se encarga Azure.

**site list [opciones]**

Este comando permite ver los sitios web.

	~$ azure site list
	info:   Executing command site list
	data:   Name            State    Host names                                        
	data:   --------------  -------  --------------------------------------------------
	data:   mongosite       Running  mongosite.antdf0.antares.windows.net     
	data:   myphpsite       Running  myphpsite.antdf0.antares.windows.net     
	data:   mydrupalsite36  Running  mydrupalsite36.antdf0.antares.windows.net
	info:   site list command OK

**site set [opciones] [nombre]**

Este comando establecerá las opciones de configuración para el sitio web [nombre]

	~$ azure site set
	info:    Executing command site set
	Web site name: mydemosite
	+ Getting sites
	+ Updating site config information
	info:    site set command OK

**site deploymentscript [opciones]**

Este comando le permitirá generar un script de implementación personalizado.

	~$ azure site deploymentscript --node
	info:    Executing command site deploymentscript
	info:    Generating deployment script for node.js Web Site
	info:    Generated deployment script files
	info:    site deploymentscript command OK

**site create [opciones] [nombre]**

Este comando crea un nuevo sitio web y directorio local. 

	~$ azure site create mysite
	info:   Executing command site create
	info:   Using location northeuropewebspace
	info:   Creating a new web site
	info:   Created web site at  mysite.antdf0.antares.windows.net
	info:   Initializing repository
	info:   Repository initialized
	info:   site create command OK

<div class="dev-callout"><b>Nota:</b>
   <p>El nombre del sitio debe ser exclusivo. No puede crear un sitio con el mismo nombre DNS que un sitio existente.</p>
</div>

**site browse [opciones] [nombre]**

Este comando permite abrir el sitio web en un explorador.

	~$ azure site browse mysite
	info:   Executing command site browse
	info:   Launching browser to http://mysite.antdf0.antares-test.windows-int.net
	info:   site browse command OK

**site show [opciones] [nombre]**

Este comando permite ver información sobre un sitio web.

	~$ azure site show mysite
	info:   Executing command site show
	info:   Showing details for site
	data:   Site AdminEnabled true
	data:   Site HostNames mysite.antdf0.antares-test.windows-int.net
	data:   Site Name mysite
	data:   Site Owner 00060000814EDDEE
	data:   Site RepositorySiteName mysite
	data:   Site SelfLink https://s1.api.antdf0.antares.windows.net:454/subscriptions/444e62ff-4c5f-4116-a695-5c803ed584a5/webspaces/northeuropewebspace/sites/mysite
	data:   Site State Running
	data:   Site UsageState Normal
	data:   Site WebSpace northeuropewebspace
	data:   Config AppSettings
	data:   Config ConnectionStrings
	data:   Config DefaultDocuments 0=Default.htm, 1=Default.asp, 2=index.htm, 3=index.html, 4=iisstart.htm, 5=default.aspx, 6=index.php, 7=hostingstart.aspx
	data:   Config DetailedErrorLoggingEnabled false
	data:   Config HttpLoggingEnabled false
	data:   Config Metadata
	data:   Config NetFrameworkVersion v4.0
	data:   Config NumberOfWorkers 1
	data:   Config PhpVersion 5.3
	data:   Config PublishingPassword rJ}[Er2v[Y]q16B6vTD]n$[C2z}Z.pvgLfRcLnAp%ax]xstiLny};o@vmMAote@d
	data:   Config RequestTracingEnabled false
	data:   Repository https://mysite.scm.antdf0.antares-test.windows-int.net/
	info:   site show command OK

**site delete [opciones] [nombre]**

Este comando permite eliminar un sitio web.

	~$ azure site delete mysite
	info:   Executing command site delete
	info:   Deleting site mysite
	info:   Site mysite has been deleted
	info:   site delete command OK

 **site swap [opciones] [nombre]**
 
Este comando intercambia entre dos espacios de sitio web.

Este comando es compatible con la siguiente opción adicional:

**-q o **--quiet**: No solicita confirmación. Use esta opción en los scripts automáticos.


**site start [opciones] [nombre]**

Este comando permite iniciar un sitio web.

	~$ azure site start mysite
	info:   Executing command site start
	info:   Starting site mysite
	info:   Site mysite has been started
	info:   site start command OK

**site stop [opciones] [nombre]**

Este comando permite detener un sitio web.

	~$ azure site stop mysite
	info:   Executing command site stop
	info:   Stopping site mysite
	info:   Site mysite has been stopped
	info:   site stop command OK

**site restart [opciones] [nombre]

Este comando detiene y después inicia un sitio web especificado.

Este comando es compatible con la siguiente opción adicional:

**--slot** <ranura>: Nombre del espacio que se va a reiniciar.


**site location list [opciones]**

Este comando permite ver las ubicaciones de los sitios web.

	~$ azure site location list
	info:    Executing command site location list
	+ Getting locations
	data:    Name
	data:    ----------------
	data:    West Europe
	data:    West US
	data:    North Central US
	data:    North Europe
	data:    East Asia
	data:    East US
	info:    site location list command OK

###Comandos para administrar la configuración de aplicaciones de sitios web

**site appsetting list [opciones] [nombre]**

Este comando permite ver la configuración de la aplicación agregada al sitio web.

	~$ azure site appsetting list
	info:    Executing command site appsetting list
	Web site name: mydemosite
	+ Getting sites
	+ Getting site config information
	data:    Name  Value
	data:    ----  -----
	data:    test  value
	info:    site appsetting list command OK

**site appsetting add [opciones] <par-clave-valor> [nombre]**

Este comando permite agregar una configuración de la aplicación a su sitio web como un par clave-valor.

	~$ azure site appsetting add test=value
	info:    Executing command site appsetting add
	Web site name: mydemosite
	+ Getting sites
	+ Getting site config information
	+ Updating site config information
	info:    site appsetting add command OK

**site appsetting delete [opciones] <clave> [nombre]**

Este comando permite eliminar la configuración de la aplicación especificada desde el sitio web.

	~$ azure site appsetting delete test
	info:    Executing command site appsetting delete
	Web site name: mydemosite
	+ Getting sites
	+ Getting site config information
	Delete application setting test? [y/n] y
	+ Updating site config information
	info:    site appsetting delete command OK

**site appsetting show [opciones] <clave> [nombre]**

Este comando muestra información de la configuración de la aplicación especificada.

	~$ azure site appsetting show test
	info:    Executing command site appsetting show
	Web site name: mydemosite
	+ Getting sites
	+ Getting site config information
	data:    Value:  value
	info:    site appsetting show command OK

###Comandos para administrar certificados de sitios web

**site cert list [opciones] [nombre]**

Este comando permite ver una lista de certificados del sitio web.

	~$ azure site cert list
	info:    Executing command site cert list
	Web site name: mydemosite
	+ Getting sites
	+ Getting site information
	data:    Subject                       Expiration Date	                  Thumbprint
	data:    ----------------------------  -----------------------------------------
	----------------  ----------------------------------------
	data:    *.msopentech.com              Fri Nov 28 2014 09:49:57 GMT-0800 (Pacific Standard Time)  A40E82D3DC0286D1F58650E570ECF8224F69A148
	data:    msopentech.azurewebsites.net  Fri Jun 19 2015 11:57:32 GMT-0700 (Pacific Daylight Time)  CE1CD6538852BF7A5DC32001C2E26A29B541F0E8
	info:    site cert list command OK

**site cert add [opciones] <ruta-acceso-certificado> [nombre]**

**site cert delete [opciones] <huella-digital> [nombre]**

**site cert show [opciones] <huella-digital> [nombre]**

Este comando permite ver la información del certificado.

	~$ azure site cert show CE1CD65852B38DC32001C2E0E8F7A526A29B541F
	info:    Executing command site cert show
	Web site name: mydemosite
	+ Getting sites
	+ Getting site information
	data:    Certificate hostNames 0=msopentech.azurewebsites.net
	data:    Certificate expirationDate
	data:    Certificate friendlyName msopentech.azurewebsites.net
	data:    Certificate issueDate
	data:    Certificate issuer CN=MSIT Machine Auth CA 2, DC=redmond, DC=corp, DC=microsoft, DC=com
	data:    Certificate subjectName msopentech.azurewebsites.net
	data:    Certificate thumbprint CE1CD65852B38DC32001C2E0E8F7A526A29B541F
	info:    site cert show command OK

###Comandos para administrar cadenas de conexión de sitios web

**site connectionstring list [opciones] [nombre]**

**site connectionstring add [opciones] <nombre-conexión> <valor> <tipo> [nombre]**

**site connectionstring delete [opciones] <nombre-conexión> [nombre]**

**site connectionstring show [opciones] <nombre-conexión> [nombre]**

###Comandos para administrar documentos predeterminados de sitios web

**site defaultdocument list [opciones] [nombre]**

**site defaultdocument add [opciones] <documento> [nombre]**

**site defaultdocument delete [opciones] <documento> [nombre]**

###Comandos para administrar implementaciones de sitios web

**site deployment list [opciones] [nombre]**

**site deployment show [opciones] <id.-confirmar> [nombre]**

**site deployment redeploy [opciones] <id.-confirmar> [nombre]**

**site deployment github [opciones] [nombre]**

**site deployment user set [opciones] [nombre-usuario] [contraseña]**

###Comandos para administrar dominios de sitios web

**site domain list [opciones] [nombre]**

**site domain add [opciones] <nd> [nombre]**

**site domain delete [opciones] <nd> [nombre]**

###Comandos para administrar asignaciones de controlador de sitios web

**site handler list [opciones] [nombre]**

**site handler add [opciones] <extensión> <procesador> [nombre]**

**site handler delete [opciones] <extensión> [nombre]**

###Comandos para administrar trabajos web de sitios web

**site job list [opciones] [nombre]**

Este comando enumera todos los trabajos web de un sitio web.

Este comando es compatible con las siguientes opciones adicionales:

+ **--job-type** <tipo-trabajo>: Opcional. Tipo de trabajo web. El valor válido es "desencadenado" o "continuo". De forma predeterminada, se devuelven
los trabajos webde todos los tipos.
+ **--slot** <ranura>: Nombre del espacio que se va a reiniciar.

**site job show [opciones] <nombreTrabajo> <tipoTrabajo> [nombre]**

Este comando permite ver la información de un trabajo web especificado.

Este comando es compatible con las siguientes opciones adicionales:

+ **--job-name** <nombre-trabajo>: Obligatorio. Nombre del trabajo web.
+ **--job-type** <tipo-trabajo>: Obligatorio. Tipo de trabajo web. El valor válido es "desencadenado" o "continuo".
+ **--slot** <ranura>: Nombre del espacio que se va a reiniciar.

**site job delete [opciones] <nombreTrabajo> <tipoTrabajo> [nombre]**

Este comando elimina el trabajo web especificado.

Este comando es compatible con las siguientes opciones adicionales:

+ **--job-name** <nombre-trabajo> obligatorio. Nombre del trabajo web.
+ **--job-type** <<tipo-trabajo> obligatorio. Tipo de trabajo web. El valor válido es "desencadenado" o "continuo".
+ **-q** o **--quiet**: No solicita confirmación. Use esta opción en los scripts automáticos.
+ **--slot** <ranura>: Nombre del espacio que se va a reiniciar.

**site job upload [opciones] <nombreTrabajo> <tipoTrabajo> <archivoTrabajo> [nombre]**

Este comando elimina el trabajo web especificado.

Este comando es compatible con las siguientes opciones adicionales:

+ **--job-name** <nombre-trabajo>: Obligatorio. Nombre del trabajo web.
+ **--job-type** <tipo-trabajo>: Obligatorio. Tipo de trabajo web. El valor válido es "desencadenado" o "continuo".
+ **--job-file** <archivo-trabajo>: Obligatorio. Archivo de trabajo.
+ **--slot** <ranura>: Nombre del espacio que se va a reiniciar.

**site job start [opciones] <nombreTrabajo> <tipoTrabajo> [nombre]**

Este comando inicia el trabajo web especificado.

Este comando es compatible con las siguientes opciones adicionales:

+ **--job-name** <nombre-trabajo>: Obligatorio. Nombre del trabajo web.
+ **--job-type** <tipo-trabajo>: Obligatorio. Tipo de trabajo web. El valor válido es "desencadenado" o "continuo".
+ **--slot** <ranura>: Nombre del espacio que se va a reiniciar.

**site job stop [opciones] <nombreTrabajo> <tipoTrabajo> [nombre]**

Este comando detiene el trabajo web especificado. Solo se pueden detener trabajos continuos.

Este comando es compatible con las siguientes opciones adicionales:

+ **--job-name** <nombre-trabajo>: Obligatorio. Nombre del trabajo web.
+ **--slot** <ranura>: Nombre del espacio que se va a reiniciar.

###Comandos para administrar el historial de trabajos web de sitios web

**site job history list [opciones] [nombreTrabajo] [nombre]**

Este comando muestra un historial de las ejecuciones del trabajo web especificado.

Este comando es compatible con las siguientes opciones adicionales:

+ **--job-name** <nombre-trabajo>: Obligatorio. Nombre del trabajo web.
+ **--slot** <ranura>: Nombre del espacio que se va a reiniciar.

**site job history show [opciones] [nombreTrabajo] [id.-ejecución] [nombre]**

Este comando obtiene los detalles de la ejecución de trabajo del trabajo web especificado.

Este comando es compatible con las siguientes opciones adicionales:

+ **--job-name** <nombre-trabajo>: Obligatorio. Nombre del trabajo web.
+ **--run-id** <id.-ejecución>: Opcional. Id. del historial de ejecuciones. Si no se especifica, muestra la ejecución más reciente.
+ **--slot** <ranura>: Nombre del espacio que se va a reiniciar.

###Comandos para administrar diagnósticos de sitios web

**site log download [opciones] [nombre]**

Permite descargar un archivo .zip que contiene el diagnóstico del sitio web.

	~$ azure site log download
	info:    Executing command site log download
	Web site name: mydemosite
	+ Getting sites
	+ Getting site information
	+ Downloading diagnostic log to diagnostics.zip
	info:    site log download command OK

**site log tail [opciones] [nombre]**

Este comando permite conectar su terminal al servicio de streaming de registros.

	~$ azure site log tail
	info:    Executing command site log tail
	Web site name: mydemosite
	+ Getting sites
	+ Getting site information
	2013-11-19T17:24:17  Welcome, you are now connected to log-streaming service.

**site log set [opciones] [nombre]**

Este comando configura las opciones de diagnóstico para el sitio web.

	~$ azure site log set -a
	info:    Executing command site log set
	+ Getting output options
	help:    Output:
	  1) file
	  2) storage
	  : 1
	Web site name: mydemosite
	+ Getting locations
	+ Getting sites
	+ Getting site information
	+ Getting diagnostic settings
	+ Updating diagnostic settings
	info:    site log set command OK

###Comandos para administrar repositorios de sitios web

**site repository branch [opciones] <rama> [nombre]**

**site repository delete [opciones] [nombre]**

**site repository sync [opciones] [nombre]**

###Comandos para administrar el escalado de sitios web

**site scale mode [opciones] <moda> [nombre]**

**site scale instances [opciones] <instancias> [nombre]**


##<a name="Commands_to_manage_mobile_services"></a>Comandos para administrar Servicios Móviles de Azure

Servicios móviles de Azure reúne un conjunto de servicios de Azure que habilitan las capacidades back-end para sus aplicaciones. Los comandos de Servicios móviles se dividen en las siguientes categorías:

+ [Comandos para administrar instancias de servicios móviles](#Mobile_Services)
+ [Comandos para administrar la configuración de servicios móviles](#Mobile_Configuration)
+ [Comandos para administrar tablas de servicios móviles](#Mobile_Tables)
+ [Comandos para administrar scripts de servicios móviles](#Mobile_Scripts)
+ [Comandos para administrar trabajos programados](#Mobile_Jobs)
+ [Comandos para escalar un servicio móvil](#Mobile_Scale)

Las opciones siguientes se aplican a la mayor parte de los comandos de Servicios móviles:

+ **-h** o **--help**: Muestra la información de uso del resultado.
+ **-s `<identificador>`** o **--subscription `<identificador>`**: Permite usar una suscripción determinada que se especifica como `<identificador>`.
+ **-v** o **--verbose**: Permite escribir el resultado detallado.
+ **--json**: Permite escribir el resultado de JSON.

###<a name="Mobile_Services"></a>Comandos para administrar instancias de servicios móviles

**mobile locations [opciones]**

Este comando permite ver las ubicaciones geográficas compatibles con Servicios móviles.

	~$ azure mobile locations
	info:    Executing command mobile locations
	info:    East US (default)
	info:    West US		
	info:    North Europe

**mobile create [opciones] [nombre-servicio] [nombreUsuarioAdminSQL] [contraseñaAdminSQL]**

Este comando permite crear un servicio móvil junto con un servidor y una base de datos SQL.

	~$ azure mobile create todolist your_login_name Secure$Password
	info:    Executing command mobile create
	+ Creating mobile service
	info:    Overall application state: Healthy
	info:    Mobile service (todolist) state: ProvisionConfigured
	info:    SQL database (todolist_db) state: Provisioned
	info:    SQL server (e96ean1c6v) state: ProvisionConfigured
	info:    mobile create command OK

Este comando es compatible con las siguientes opciones adicionales:

+ **-r `<servidorSQL>`**  o **--sqlServer `<servidorSQL>`**:  Permite usar un servidor de base de datos SQL existente, especificado como `<servidorSQL>`.
+ **-d `<BDSql>`** o **--sqlDb `<BDSql>`**: Permite usar la base de datos SQL existente, especificada como `<BDSql>`.
+ **-l `<ubicación>`** o **--location `<ubicación>`**: Permite crear el servicio en una ubicación determinada, que se especifica como `<ubicación>`. Ejecute ubicaciones móviles de Azure para obtener ubicaciones disponibles.	
+ **--sqlLocation `<ubicación>`**: Permite crear el servidor SQL en una `<ubicación>` específica; el valor predeterminado es la ubicación del servicio móvil.

**mobile delete [opciones] [nombre-servicio]**

Este comando permite eliminar un servicio móvil junto con su servidor y base de datos SQL.

	~$ azure mobile delete todolist -a -q
	info:    Executing command mobile delete
	data:    Mobile service todolist
	data:    SQL database todolistAwrhcL60azo1C401
	data:    SQL server fh1kvbc7la
	+ Deleting mobile service
	info:    Deleted mobile service
	+ Deleting SQL server
	info:    Deleted SQL server
	+ Deleting mobile application
	info:    Deleted mobile application
	info:    mobile delete command OK

Este comando es compatible con las siguientes opciones adicionales:

+ **-d** o **--deleteData**: Permite eliminar todos los datos de este servicio móvil de la base de datos.
+ **-a** o **--deleteAll**: Permite eliminar el servidor y la base de datos SQL.
+ **-q** o **--quiet**: No solicita confirmación. Use esta opción en los scripts automáticos.

**mobile list [opciones]**

Este comando permite ver los servicios móviles.

	~$ azure mobile list
	info:    Executing command mobile list
	data:    Name          State  URL
	data:    ------------  -----  --------------------------------------
	data:    todolist      Ready  https://todolist.azure-mobile.net/
	data:    mymobileapp   Ready  https://mymobileapp.azure-mobile.net/
	info:    mobile list command OK

**mobile show [opciones] [nombre-servicio]**

Este comando muestra información sobre un servicio móvil.

	~$ azure mobile show todolist
	info:    Executing command mobile show
	+ Getting information
	info:    Mobile application
	data:    status Healthy
	data:    Mobile service name todolist
	data:    Mobile service status ProvisionConfigured
	data:    SQL database name todolistAwrhcL60azo1C401
	data:    SQL database status Linked
	data:    SQL server name fh1kvbc7la
	data:    SQL server status Linked
	info:    Mobile service
	data:    name todolist
	data:    state Ready
	data:    applicationUrl https://todolist.azure-mobile.net/
	data:    applicationKey XXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
	data:    masterKey XXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
	data:    webspace WESTUSWEBSPACE
	data:    region West US
	data:    tables TodoItem
	info:    mobile show command OK	

**mobile restart [opciones] [nombre-servicio]**

Este comando permite reiniciar una instancia de servicio móvil.

	~$ azure mobile restart todolist
	info:    Executing command mobile restart
	+ Restarting mobile service
	info:    Service was restarted.
	info:    mobile restart command OK

**mobile log [opciones] [nombre-servicio]**

Este comando permite devolver registros de servicios móviles filtrando todos los tipos de registros excepto `error`.

	~$ azure mobile log todolist -t error
	info:    Executing command mobile log
	data:
	data:    timeCreated 2013-01-07T16:04:43.351Z
	data:    type error
	data:    source /scheduler/TestingLogs.js
	data:    message This is an error.
	data:
	info:    mobile log command OK

Este comando es compatible con las siguientes opciones adicionales:

+ **-r `<consulta>`** o **--query `<consulta>`**: Permite ejecutar la consulta de registro especificada.
+ **-t `<tipo>`** o **--type `<tipo>`**:  Permite filtrar los registros devueltos por la entrada `<tipo>`, que puede ser `information`, `warning` o `error`.
+ **-k `<omitir>`** o **--skip `<omitir>`**: Permite omitir el número de filas especificado por `<omitir>`.
+ **-p `<superior>`** o **--top `<superior>`**: Devuelve el número determinado de filas especificado por `<superior>`.

<div class="dev-callout"><b>Nota:</b>
   <p>El parámetro <b>--query</b> prevalece sobre <b>--type</b>, <b>--skip</b> y <b>--top</b>.</p>
</div>

**mobile recover [opciones] [nombre-servicio-mal-estado] [nombre-servicio-buen-estado]**

Este comando permite recuperar un servicio móvil que no está en buen estado moviéndolo a un servicio móvil en buen estado de otra región.

Este comando es compatible con la siguiente opción adicional:

**-q** o **--quiet**: Suprime la solicitud de confirmación de recuperación.

**mobile key regenerate [opciones] [nombre-servicio] [tipo]**

Este comando vuelve a generar la clave de la aplicación del servicio móvil.

	~$ azure mobile key regenerate todolist application
	info:    Executing command mobile key regenerate
	info:    New application key is SmLorAWVfslMcOKWSsuJvuzdJkfUpt40
	info:    mobile key regenerate command OK

Los tipos de clave son `master` y `application`.

<div class="dev-callout"><b>Nota:</b>
   <p>Cuando vuelva a generar las claves, es posible que los clientes que usan la clave antigua no puedan obtener acceso a su servicio móvil. Si vuelve a generar la clave de aplicación, debe actualizar su aplicación con el nuevo valor de clave. </p>
</div> 

**mobile key set [opciones] [nombre-servicio] [tipo] [valor]**

Este comando establece la clave de servicio móvil en un valor específico.


###<a name="Mobile_Configuration"></a>Comandos para administrar la configuración de servicios móviles

**mobile config list [opciones] [nombre-servicio]**

Este comando permite ver las opciones de configuración para un servicio móvil.

	~$ azure mobile config list todolist
	info:    Executing command mobile config list
	+ Getting mobile service configuration
	data:    dynamicSchemaEnabled true
	data:    microsoftAccountClientSecret Not configured
	data:    microsoftAccountClientId Not configured
	data:    microsoftAccountPackageSID Not configured
	data:    facebookClientId Not configured
	data:    facebookClientSecret Not configured
	data:    twitterClientId Not configured
	data:    twitterClientSecret Not configured
	data:    googleClientId Not configured
	data:    googleClientSecret Not configured
	data:    apnsMode none
	data:    apnsPassword Not configured
	data:    apnsCertifcate Not configured
	info:    mobile config list command OK

**mobile config get [opciones] [nombre-servicio] [clave]**

Este comando permite obtener una opción de configuración específica para un servicio móvil. En este caso, se trata de un esquema dinámico.

	~$ azure mobile config get todolist dynamicSchemaEnabled
	info:    Executing command mobile config get
	data:    dynamicSchemaEnabled true
	info:    mobile config get command OK

**mobile config set [opciones] [nombre-servicio] [clave] [valor]**

Este comando permite establecer una opción de configuración específica para un servicio móvil. En este caso, se trata de un esquema dinámico.

	~$ azure mobile config set todolist dynamicSchemaEnabled false
	info:    Executing command mobile config set
	info:    mobile config set command OK


###<a name="Mobile_Tables"></a>Comandos para administrar tablas de servicios móviles

**mobile table list [opciones] [nombre-servicio]**

Este comando permite ver todas las tablas del servicio móvil.

	~$azure mobile table list todolist
	info:    Executing command mobile table list
	data:    Name      Indexes  Rows
	data:    --------  -------  ----
	data:    Channel   1        0
	data:    TodoItem  1        0
	info:    mobile table list command OK

**mobile table show [opciones] [nombre-servicio] [nombre-tabla]**

Este comando permite ver la información de devolución sobre una tabla específica.

	~$azure mobile table show todolist
	info:    Executing command mobile table show
	+ Getting table information
	info:    Table statistics:
	data:    Number of records 5
	info:    Table operations:
	data:    Operation  Script       Permissions
	data:    ---------  -----------  -----------
	data:    insert     1900 bytes   user
	data:    read       Not defined  user
	data:    update     Not defined  user
	data:    delete     Not defined  user
	info:    Table columns:
	data:    Name  Type           Indexed
	data:    ----  -------------  -------
	data:    id    bigint(MSSQL)  Yes
	data:    text      string
	data:    complete  boolean
	info:    mobile table show command OK

**mobile table create [opciones] [nombre-servicio] [nombre-tabla]**

Este comando permite crear una tabla.

	~$azure mobile table create todolist Channels
	info:    Executing command mobile table create
	+ Creating table
	info:    mobile table create command OK

Este comando es compatible con la siguiente opción adicional:

+ **-p `<permisos>`** o **--permissions `<permisos>`**: Lista delimitada por comas de pares `<operación>`=`<permiso>`, donde `<operación>` es `insert`, `read`, `update`, o `delete` y `<permiso>` es `public`, `application` (predeterminada), `user` o `admin`.

**mobile data read [opciones] [nombre-servicio] [nombre-tabla] [consulta]**

Este comando permite leer los datos de una tabla.

	~$azure mobile data read todolist TodoItem
	info:    Executing command mobile data read
	data:    id  text     complete
	data:    --  -------  --------
	data:    1   item #1  false
	data:    2   item #2  true
	data:    3   item #3  false
	data:    4   item #4  true
	info:    mobile data read command OK

Este comando es compatible con las siguientes opciones adicionales:

+ **-k `<omitir>`** o **--skip `<omitir>`**: Permite omitir el número de filas especificado por `<omitir>`.
+ **-t `<superior>`** o **--top `<superior>`**: Devuelve el número determinado de filas especificado por `<superior>`.
+ **-l** o **--list**: Permite devolver los datos en un formato de lista.

**mobile table update [opciones] [nombre-servicio] [nombre-tabla]**

Este comando permite cambiar los permisos de eliminación en una tabla solo para los administradores.

	~$azure mobile table update todolist Channels -p delete=admin
	info:    Executing command mobile table update
	+ Updating permissions
	info:    Updated permissions
	info:    mobile table update command OK

Este comando es compatible con las siguientes opciones adicionales:

+ **-p `<permisos>`** o **--permissions `<permisos>`**: Lista delimitada por comas de pares `<operación>`=`<permiso>`, donde `<operación>` es `insert`, `read`, `update`, o `delete` y `<permiso>` es `public`, `application` (predeterminada), `user` o `admin`.
+ **--deleteColumn `<columnas>`**: Lista delimitada por comas de columnas para eliminar, como `<columnas>`.
+ **-q** o **--quiet**: Permite eliminar columnas sin solicitar confirmación.
+ **--addIndex `<columnas>`**: Lista delimitada por comas de columnas para incluir en el índice.
+ **--deleteIndex `<columnas>`**: Lista delimitada por comas de columnas para excluir del índice.

**mobile table delete [opciones] [nombre-servicio] [nombre-tabla]**

Este comando permite eliminar una tabla.

	~$azure mobile table delete todolist Channels
	info:    Executing command mobile table delete
	Do you really want to delete the table (yes/no): yes
	+ Deleting table
	info:    mobile table delete command OK

Especifique el parámetro -q para eliminar la tabla sin que sea necesaria la confirmación. Esto evita el bloqueo de los scripts de automatización.

**mobile data truncate [opciones] [nombre-servicio] [nombre-tabla]**

Este comando permite quitar todas las filas de datos de la tabla.

	~$azure mobile data truncate todolist TodoItem
	info:    Executing command mobile data truncate
	info:    There are 7 data rows in the table.
	Do you really want to delete all data from the table? (y/n): y
	info:    Deleted 7 rows.
	info:    mobile data truncate command OK


###<a name="Mobile_Scripts"></a>Comandos para administrar scripts

Los comandos de esta sección se usan para administrar los scripts de servidor que pertenecen a un servicio móvil. Para obtener más información, consulte [Uso de scripts del servidor en Servicios móviles](http://www.windowsazure.com/es-es/develop/mobile/how-to-guides/work-with-server-scripts/).

**mobile script list [opciones] [nombre-servicio]**

Este comando permite ver los scripts registrados, incluidos los scritps del programador y de tablas.

	~$azure mobile script list todolist
	info:    Executing command mobile script list
	+ Getting script information
	info:    Table scripts
	data:    Name                   Size
	data:    ---------------------  ----
	data:    table/TodoItem.delete  256
	data:    table/Devices.insert   1660
	error:   Unable to get shared scripts
	info:    Scheduler scripts
	data:    Name                 Status     Interval   Last run   Next run
	data:    -------------------  ---------  ---------  ---------  ---------
	data:    scheduler/undefined  undefined  undefined  undefined  undefined
	data:    scheduler/undefined  undefined  undefined  undefined  undefined
	info:    mobile script list command OK

**mobile script download [opciones] [nombre-servicio] [nombre-script]**

Este comando permite descargar el script de inserción de la tabla TodoItem en un archivo con el nombre `todoitem.insert.js` en la subcarpeta `table`.

	~$azure mobile script download todolist table/todoitem.insert.js
	info:    Executing command mobile script download
	info:    Saved script to ./table/todoitem.insert.js
	info:    mobile script download command OK

Este comando es compatible con las siguientes opciones adicionales:

+ **-p `<ruta-acceso>`** o **--path `<ruta-acceso>`**: La ubicación en el archivo en el que guardar el script, donde el directorio de trabajo actual es el valor predeterminado.
+ **-f `<archivo>`** o **--file `<archivo>`**: El nombre del archivo en el que guardar el script.
+ **-o** o **--override**: Permite sobrescribir un archivo existente.
+ **-c** o **--console**: Permite escribir el script en la consola en lugar de en un archivo.

**mobile script upload [opciones] [nombre-servicio] [nombre-script]**

Este comando carga un nuevo script con el nombre `todoitem.insert.js` desde la subcarpeta `table`.

	~$azure mobile script upload todolist table/todoitem.insert.js
	info:    Executing command mobile script upload
	info:    mobile script upload command OK

El nombre del archivo debe estar compuesto por los nombres de operación y de tabla, y debe encontrarse en la subcarpeta table relativa a la ubicación en la que se ejecuta el comando. También puede usar el parámetro **-f `<archivo>`** o **--file `<archivo>`** para especificar una ruta y un nombre de archivo distintos para el archivo que contienen el script para registrar.


**mobile script delete [opciones] [nombre-servicio] [nombre-script]**

Este comando permite eliminar el script de inserción existente de la tabla TodoItem.

	~$azure mobile script delete todolist table/todoitem.insert.js
	info:    Executing command mobile script delete
	info:    mobile script delete command OK

###<a name="Mobile_Jobs"></a>Comandos para administrar trabajos programados

Los comandos de esta sección se usan para administrar los trabajos programados que pertenecen a un servicio móvil. Para obtener más información, consulte [Programar trabajos](http://msdn.microsoft.com/es-es/library/windowsazure/jj860528.aspx).

**mobile job list [opciones] [nombre-servicio]**

Este comando permite ver los trabajos programados.

	~$azure mobile job list todolist
	info:    Executing command mobile job list
	info:    Scheduled jobs
	data:    Job name    Script name           Status    Interval     Last run              Next run
	data:    ----------  --------------------  --------  -----------  --------------------  --------------------
	data:    getUpdates  scheduler/getUpdates  enabled   15 [minute]  2013-01-14T16:15:00Z  2013-01-14T16:30:00Z
	info:    You can manipulate scheduled job scripts using the 'azure mobile script' command.
	info:    mobile job list command OK

**mobile job create [opciones] [nombre-servicio] [nombre-trabajo]**

Este comando permite crear un nuevo trabajo con el nombre `getUpdates` que se programa para ejecutarse cada hora.

	~$azure mobile job create -i 1 -u hour todolist getUpdates 
	info:    Executing command mobile job create
	info:    Job was created in disabled state. You can enable the job using the 'azure mobile job update' command.
	info:    You can manipulate the scheduled job script using the 'azure mobile script' command.
	info:    mobile job create command OK

Este comando es compatible con las siguientes opciones adicionales:

+ **-i `<número>`** o **--interval `<número>`**: El intervalo de trabajo (número entero); el valor predeterminado es `15`.
+ **-u `<unidad>`** o **--intervalUnit `<unidad>`**: La unidad para _interval_, que puede ser uno de los siguientes valores: 
	+ **minute** (predeterminado)
	+ **hour**
	+ **day**
	+ **month** 
	+ **none** (trabajos a petición)
+ **-t `<hora>`** **--startTime `<hora>`** La hora de inicio de la primera ejecución del script, en formato ISO; el valor predeterminado es `now`.

<div class="dev-callout"><b>Nota:</b>
   <p>Los nuevos trabajos se crean en un estado deshabilitado porque aún se debe cargar un script. Use el comando <strong>mobile script upload</strong> para cargar un script y el comando <strong>mobile job update</strong> para habilitar el trabajo.</p>
</div> 

**mobile job update [opciones] [nombre-servicio] [nombre-trabajo]**

El siguiente comando habilita el trabajo deshabilitado `getUpdates`.

	~$azure mobile job update -a enabled todolist getUpdates 
	info:    Executing command mobile job update
	info:    mobile job update command OK

Este comando es compatible con las siguientes opciones adicionales:

+ **-i `<número>`** o **--interval `<número>`**: El intervalo de trabajo (número entero); el valor predeterminado es `15`.
+ **-u `<unidad>`** o **--intervalUnit `<unidad>`**: La unidad para _interval_, que puede ser uno de los siguientes valores: 
	+ **minute** (predeterminado)
	+ **hour**
	+ **day**
	+ **month** 
	+ **none** (trabajos a petición)
+ **-t `<hora>`** **--startTime `<hora>`** La hora de inicio de la primera ejecución del script, en formato ISO; el valor predeterminado es `now`.
+ **-a `<estado>`** o **--status `<estado>`**: El estado del trabajo, que puede ser `enabled` o `disabled`.

**mobile job delete [opciones] [nombre-servicio] [nombre-trabajo]**

Este comando quita el trabajo programado getUpdates del servidor TodoList.

	~$azure mobile job delete todolist getUpdates
	info:    Executing command mobile job delete
	info:    mobile job delete command OK

<div class="dev-callout"><b>Nota:</b>
   <p>Si se elimina un trabajo, también se elimina el script cargado.</p>
</div> 

###<a name="Mobile_Scale"></a>Comandos para escalar un servicio móvil

Los comandos de esta sección se usan para escalar un servicio móvil. Para obtener más información, consulte [Escalar un servicio móvil](http://msdn.microsoft.com/es-es/library/windowsazure/jj193178.aspx).

**mobile scale show [opciones] [nombre-servicio]**

Este comando permite ver la información de la escalación, incluido el modo de proceso actual y el número de instancias.

	~$azure mobile scale show todolist
	info:    Executing command mobile scale show
	data:    webspace WESTUSWEBSPACE
	data:    computeMode Free
	data:    numberOfInstances 1
	info:    mobile scale show command OK

**mobile scale change [opciones] [nombre-servicio]**

Este comando permite cambiar la escala del servicio móvil del modo libre a premium.

	~$azure mobile scale change -c Reserved -i 1 todolist
	info:    Executing command mobile scale change
	+ Rescaling the mobile service
	info:    mobile scale change command OK

Este comando es compatible con las siguientes opciones adicionales:

+ **-c `<modo>`** o **--computeMode `<modo>`**: El modo de proceso debe ser `Free` o `Reserved`.
+ **-i `<recuento>`** o **--numberOfInstances `<recuento>`**: El número de instancias usadas al ejecutarse en modo reservado.

<div class="dev-callout"><b>Nota:</b>
   <p>Cuando establece el modo de proceso en `Reserved`, todos los servicios móviles de la misma región se ejecutan en el modo premium.</p>
</div>  


###Comandos para habilitar características de vista previa para su servicio móvil

**mobile preview list [opciones] [nombre-servicio]**

Este comando muestra las características de vista previa disponibles en el servicio especificado y si están habilitadas.

	~$ azure mobile preview list mysite
	info:    Executing command mobile preview list
	+ Getting preview features
	data:    Preview feature  Enabled
	data:    ---------------  -------
	data:    SourceControl    No
	data:    Users            No
	info:    You can enable preview features using the 'azure mobile preview enable' command.
	info:    mobile preview list command OK

**mobile preview enable [opciones] [nombre-servicio] [nombre-característica]**

Este comando habilita la característica de vista previa especificada para un servicio móvil. Tenga en cuenta que una vez habilitadas, las características de vista previa no se pueden deshabilitar para un servicio móvil.

###Comandos para administrar las API de servicios móviles

**mobile api list [opciones] [nombre-servicio]**

Este comando muestra una lista de API personalizadas de servicio móvil que creó para su servicio móvil.

	~$ azure mobile api list mysite
	info:    Executing command mobile api list
	+ Retrieving list of APIs
	info:    APIs
	data:    Name                  Get          Put          Post         Patch        Delete
	data:    --------------------  -----------  -----------  -----------  -----------  -----------
	data:    myCustomRetrieveAPI   application  application  application  application  application
	info:    You can manipulate API scripts using the 'azure mobile script' command.
	info:    mobile api list command OK

**mobile api create [opciones] [nombre-servicio] [nombre-api]**

Creación de API personalizada de servicio móvil

	~$ azure mobile api create mysite myCustomRetrieveAPI
	info:    Executing command mobile api create
	+ Creating custom API: 'myCustomRetrieveAPI'
	info:    API was created successfully. You can modify the API using the 'azure mobile script' command.
	info:    mobile api create command OK

Este comando es compatible con la siguiente opción adicional:

**-p** o **--permissions** <permisos>:  Lista delimitada por comas de pares <método>=<permiso>.

**mobile api update [opciones] [nombre-servicio] [nombre-api]**

Este comando actualiza la API personalizada del servicio móvil especificado.

Este comando es compatible con la siguiente opción adicional:

Este comando es compatible con las siguientes opciones adicionales:

+ **-p** o **--permissions** <permisos>: Lista delimitada por comas de pares <método>=<permiso>  .
+ **-f** o **--force**: Invalida los cambios personalizados en el archivo de metadatos de permisos.

**mobile api delete [opciones] [nombre-servicio] [nombre-api]**

	~$ azure mobile api delete mysite myCustomRetrieveAPI
	info:    Executing command mobile api delete
	+ Deleting API: 'myCustomRetrieveAPI'
	info:    mobile api delete command OK

Este comando elimina la API personalizada del servicio móvil especificado.

###Comandos para administrar la configuración de aplicaciones móviles

**mobile appsetting list [opciones] [nombre-servicio]**

Este comando muestra la configuración de aplicaciones móviles del servicio especificado.

	~$ azure mobile appsetting list mysite
	info:    Executing command mobile appsetting list
	+ Retrieving app settings
	data:    Name               Value
	data:    -----------------  -----
	data:    enablebetacontent  true
	info:    mobile appsetting list command OK

**mobile appsetting add [opciones] [nombre-servicio] [nombre] [valor]**

Este comando agrega una configuración de aplicación personalizada a su servicio móvil.

	~$ azure mobile appsetting add mysite enablebetacontent true
	info:    Executing command mobile appsetting add
	+ Retrieving app settings
	+ Adding app setting
	info:    mobile appsetting add command OK

**mobile appsetting delete [opciones] [nombre-servicio] [nombre]**

Este comando quita la configuración de la aplicación especificada de su servicio móvil.

	~$ azure mobile appsetting delete mysite enablebetacontent
	info:    Executing command mobile appsetting delete
	+ Retrieving app settings
	+ Removing app setting 'enablebetacontent'
	info:    mobile appsetting delete command OK

**mobile appsetting show [opciones] [nombre-servicio] [nombre]**

Este comando quita la configuración de la aplicación especificada de su servicio móvil.

	~$ azure mobile appsetting show mysite enablebetacontent
	info:    Executing command mobile appsetting show
	+ Retrieving app settings
	info:    enablebetacontent: true
	info:    mobile appsetting show command OK

##<a name="Manage_tool_local_settings"></a>Administración de la configuración local de las herramientas

La configuración local es el identificador de la suscripción y el nombre de cuenta de almacenamiento predeterminado.

**config list [opciones]**

Este comando muestra la configuración config.

	~$ azure config list
	info:   Displaying config settings
	data:   Setting                Value                               
	data:   ---------------------  ------------------------------------
	data:   subscription           32-digit-subscription-key
	data:   defaultStorageAccount  name

**config set [opciones] <nombre>,<valor>**

Este comando permite cambiar una configuración config.

	~$ azure config set defaultStorageAccount myname
	info:   Setting 'defaultStorageAccount' to value 'myname'
	info:   Changes saved.

##<a name ="Commands_to_manage_service_bus"></a>Comandos para administrar el bus de servicio

Use estos comandos para administrar la cuenta del bus de servicio.

**sb namespace check [opciones] <nombre>**

Compruebe que hay un espacio de nombres de Bus de servicio legal y disponible.

**sb namespace create <nombre> <ubicación>**

Permite crear un nuevo espacio de nombres del Bus de servicio.

	~$ azure sb namespace create mysbnamespacea-test "West US"
	info:    Executing command sb namespace create
	+ Creating namespace mysbnamespacea-test in region West US
	data:    Name: mysbnamespacea-test
	data:    Region: West US
	data:    DefaultKey: fBu8nQ9svPIesFfMFVhCFD+/sY0rRbifWMoRpYy0Ynk=
	data:    Status: Activating
	data:    CreatedAt: 2013-11-14T16:23:29.32Z
	data:    AcsManagementEndpoint: https://mysbnamespacea-test-sb.accesscontrol.windows.net/
	data:    ServiceBusEndpoint: https://mysbnamespacea-test.servicebus.windows.net/
	
	data:    ConnectionString: Endpoint=sb://mysbnamespacea-test.servicebus.windows.
	net/;SharedSecretIssuer=owner;SharedSecretValue=fBu8nQ9svPIesFfMFVhCFD+/sY0rRbif
	WMoRpYy0Ynk=
	data:    SubscriptionId: 8679c8be3b0549d9b8fb4bd232a48931
	data:    Enabled: true
	data:    _: [object Object]
	info:    sb namespace create command OK


**sb namespace delete <nombre>**

Permite quitar un espacio de nombres.

	~$ azure sb namespace delete mysbnamespacea-test
	info:    Executing command sb namespace delete
	Delete namespace mysbnamespacea-test? [y/n] y
	+ Deleting namespace mysbnamespacea-test
	info:    sb namespace delete command OK

**sb namespace list**

Enumera todos los espacios de nombres creados para su cuenta.

	~$ azure sb namespace list
	info:    Executing command sb namespace list
	+ Getting namespaces
	data:    Name                 Region   Status
	data:    -------------------  -------  ------
	data:    mysbnamespacea-test  West US  Active
	info:    sb namespace list command OK


**sb namespace location list**

Permite ver una lista de todas las ubicaciones de espacio de nombres disponibles.

	~$ azure sb namespace location list
	info:    Executing command sb namespace location list
	+ Getting locations
	data:    Name              Code
	data:    ----------------  ----------------
	data:    East Asia         East Asia
	data:    West Europe       West Europe
	data:    North Europe      North Europe
	data:    East US           East US
	data:    Southeast Asia    Southeast Asia
	data:    North Central US  North Central US
	data:    West US           West US
	data:    South Central US  South Central US
	info:    sb namespace location list command OK

**sb namespace show <nombre>**

Permite ver información sobre un espacio de nombres específico.

	~$ azure sb namespace show mysbnamespacea-test
	info:    Executing command sb namespace show
	+ Getting namespace
	data:    Name: mysbnamespacea-test
	data:    Region: West US
	data:    DefaultKey: fBu8nQ9svPIesFfMFVhCFD+/sY0rRbifWMoRpYy0Ynk=
	data:    Status: Active
	data:    CreatedAt: 2013-11-14T16:23:29.32Z
	data:    AcsManagementEndpoint: https://mysbnamespacea-test-sb.accesscontrol.windows.net/
	data:    ServiceBusEndpoint: https://mysbnamespacea-test.servicebus.windows.net/
	
	data:    ConnectionString: Endpoint=sb://mysbnamespacea-test.servicebus.windows.
	net/;SharedSecretIssuer=owner;SharedSecretValue=fBu8nQ9svPIesFfMFVhCFD+/sY0rRbif
	WMoRpYy0Ynk=
	data:    SubscriptionId: 8679c8be3b0549d9b8fb4bd232a48931
	data:    Enabled: true
	data:    UpdatedAt: 2013-11-14T16:25:37.85Z
	info:    sb namespace show command OK

**sb namespace verify <nombre>**

Compruebe si el espacio de nombres está disponible.

##<a name="Commands_to_manage_your_Storage_objects"></a>Comandos para administrar objetos de almacenamiento

###Comandos para administrar cuentas de almacenamiento

**storage account list [opciones]**

Este comando muestra las cuentas de almacenamiento de su suscripción.

	~$ azure storage account list
	info:    Executing command storage account list
	+ Getting storage accounts
	data:    Name             Label  Location
	data:    ---------------  -----  --------
	data:    mybasestorage           West US
	info:    storage account list command OK

**storage account show [opciones] <nombre>**

Este comando muestra información sobre la cuenta de almacenamiento especificada, como el URI y las propiedades de la cuenta.

**storage account create [opciones] <nombre>**

Este comando permite crear una cuenta de almacenamiento en función de las opciones proporcionadas.

	~$ azure storage account create mybasestorage --label PrimaryStorage --location "West US"
	info:    Executing command storage account create
	+ Creating storage account
	info:    storage account create command OK

Este comando es compatible con las siguientes opciones adicionales:

+ **-e** o **--label** <etiqueta>: Etiqueta de la cuenta de almacenamiento.
+ **-d** o **--description** <descripción>:  Descripción de la cuenta de almacenamiento.
+ **-l** o **--location** <nombre>: Región geográfica donde se creará la cuenta de almacenamiento.
+ **-a** o **--affinity-group** <nombre>: Grupo de afinidad al que se asociará la cuenta de almacenamiento.
+ **--geoReplication**:  Indica si la replicación geográfica está habilitada.
+ **--disable-geoReplication**: Indica si la replicación geográfica está deshabilitada.

**storage account set [opciones] <nombre>**

Este comando actualiza la cuenta de almacenamiento especificada.

	~$ azure storage account set mybasestorage --geoReplication
	info:    Executing command storage account set
	+ Updating storage account
	info:    storage account set command OK

Este comando es compatible con las siguientes opciones adicionales:

+ **-e** o **--label** <etiqueta>: Etiqueta de la cuenta de almacenamiento.
+ **-d** o **--description** <descripción>:  Descripción de la cuenta de almacenamiento.
+ **-l** o **--location** <nombre>: Región geográfica donde se creará la cuenta de almacenamiento.
+ **--geoReplication**:  Indica si la replicación geográfica está habilitada.
+ **--disable-geoReplication**: Indica si la replicación geográfica está deshabilitada.

**storage account delete [opciones] <nombre>**

Este comando elimina la cuenta de almacenamiento especificada.

Este comando es compatible con la siguiente opción adicional:

**-q** o **--quiet**: No solicita confirmación. Use esta opción en los scripts automáticos.

###Comandos para administrar claves de cuenta de almacenamiento

**storage account keys list [opciones] <nombre>**

Este comando enumera las claves principal y secundaria para la cuenta de almacenamiento especificada.

**storage account keys renew [opciones] <nombre>**

###Comandos para administrar el contenedor de almacenamiento

**storage container list [opciones] [prefijo]**

Este comando muestra la lista de contenedores de almacenamiento de una cuenta de almacenamiento especificada. La cuenta de almacenamiento está especificada por la cadena de conexión o por el nombre y la clave de la cuenta de almacenamiento.

Este comando es compatible con las siguientes opciones adicionales:

+ **-p** o **-prefix** <prefijo>: Prefijo del nombre del contenedor de almacenamiento.
+ **-a** o **--account-name** <nombreCuenta>: El nombre de la cuenta de almacenamiento.
+ **-k** o **--account-key** <nombreCuenta>: La clave de la cuenta de almacenamiento.
+ **-c** o **--connection-string** <cadenaConexión>: Cadena de conexión de almacenamiento.
+ **--debug**: Ejecuta el comando de almacenamiento en modo de depuración.

**storage container show [opciones] [contenedor]**
**storage container create [opciones] [contenedor]**

Este comando permite crear un contenedor de almacenamiento para la cuenta de almacenamiento especificada. La cuenta de almacenamiento está especificada por la cadena de conexión o por el nombre y la clave de la cuenta de almacenamiento.

Este comando es compatible con las siguientes opciones adicionales:

+ **--container** <contenedor>: Nombre del contenedor de almacenamiento que se va a crear.
+ **-p** o **-prefix** <prefijo>: Prefijo del nombre del contenedor de almacenamiento.
+ **-a** o **--account-name** <nombreCuenta>: Nombre de cuenta de almacenamiento
+ **-k** o **--account-key** <nombreCuenta>: La clave de la cuenta de almacenamiento
+ **-c** o **--connection-string** <cadenaConexión>: Cadena de conexión de almacenamiento
+ **--debug**: Ejecuta el comando de almacenamiento en modo de depuración.

**storage container delete [opciones] [contenedor]**

Este comando elimina el contenedor de almacenamiento especificado. La cuenta de almacenamiento está especificada por la cadena de conexión o por el nombre y la clave de la cuenta de almacenamiento.

Este comando es compatible con las siguientes opciones adicionales:

+ **--container** <contenedor>: Nombre del contenedor de almacenamiento que se va a crear.
+ **-p** o **-prefix** <prefijo>: Prefijo del nombre del contenedor de almacenamiento.
+ **-a** o **--account-name** <nombreCuenta>: El nombre de la cuenta de almacenamiento.
+ **-k** o **--account-key** <nombreCuenta>: La clave de la cuenta de almacenamiento.
+ **-c** o **--connection-string** <cadenaConexión>: Cadena de conexión de almacenamiento.
+ **--debug**: Ejecuta el comando de almacenamiento en modo de depuración.

**storage container set [opciones] [contenedor]**

Este comando establece la lista de control de acceso del control de acceso del contenedor de almacenamiento. La cuenta de almacenamiento está especificada por la cadena de conexión o por el nombre y la clave de la cuenta de almacenamiento.

Este comando es compatible con las siguientes opciones adicionales:

+ **--container** <contenedor>: Nombre del contenedor de almacenamiento que se va a crear.
+ **-p** o **-prefix** <prefijo>: Prefijo del nombre del contenedor de almacenamiento.
+ **-a** o **--account-name** <nombreCuenta>: El nombre de la cuenta de almacenamiento.
+ **-k** o **--account-key** <nombreCuenta>: La clave de la cuenta de almacenamiento.
+ **-c** o **--connection-string** <cadenaConexión>: Cadena de conexión de almacenamiento.
+ **--debug**: Ejecuta el comando de almacenamiento en modo de depuración.

###Comandos para administrar el blob de almacenamiento

**storage blob list [opciones] [contenedor] [prefijo]**

Este comando devuelve una lista de blobs de almacenamiento en el contenedor de almacenamiento especificado.

Este comando es compatible con las siguientes opciones adicionales:

+ **--container** <contenedor>: Nombre del contenedor de almacenamiento que se va a crear.
+ **-p** o **-prefix** <prefijo>: Prefijo del nombre del contenedor de almacenamiento.
+ **-a** o **--account-name** <nombreCuenta>: El nombre de la cuenta de almacenamiento.
+ **-k** o **--account-key** <nombreCuenta>: La clave de la cuenta de almacenamiento.
+ **-c** o **--connection-string** <cadenaConexión>: Cadena de conexión de almacenamiento.
+ **--debug**: Ejecuta el comando de almacenamiento en modo de depuración.

**storage blob show [opciones] [contenedor] [blob]**

Este comando muestra información del blob de almacenamiento especificado.

Este comando es compatible con las siguientes opciones adicionales:

+ **--container** <contenedor>: Nombre del contenedor de almacenamiento que se va a crear.
+ **-p** o **-prefix** <prefijo>: Prefijo del nombre del contenedor de almacenamiento.
+ **-a** o **--account-name** <nombreCuenta>: El nombre de la cuenta de almacenamiento.
+ **-k** o **--account-key** <nombreCuenta>: La clave de la cuenta de almacenamiento.
+ **-c** o **--connection-string** <cadenaConexión>: Cadena de conexión de almacenamiento.
+ **--debug**: Ejecuta el comando de almacenamiento en depuración.

**storage blob delete [opciones] [contenedor] [blob]**

Este comando es compatible con las siguientes opciones adicionales:

+ **--container** <contenedor>: Nombre del contenedor de almacenamiento que se va a crear.
+ **-b** o **--blob** <nombreBlob>: Nombre del blob de almacenamiento que se va a eliminar.
+ **-q** o **--quiet**: Permite quitar el blob de almacenamiento especificado sin confirmación.
+ **-a** o **--account-name** <nombreCuenta>: El nombre de la cuenta de almacenamiento.
+ **-k** o **--account-key** <nombreCuenta>: La clave de la cuenta de almacenamiento.
+ **-c** o **--connection-string** <cadenaConexión>: Cadena de conexión de almacenamiento.
+ **--debug**: Ejecuta el comando de almacenamiento en depuración.

**storage blob upload [opciones] [archivo] [contenedor] [blob]**

Este comando carga el archivo especificado en el blob de almacenamiento especificado.

Este comando es compatible con las siguientes opciones adicionales:

+ **--container** <contenedor>: Nombre del contenedor de almacenamiento que se va a crear.
+ **-b** o **--blob** <nombreBlob>: Nombre del blob de almacenamiento que se va a cargar.
+ **-t** o **--blobtype** <tipo-blob>: Tipo de blob de almacenamiento: Página o bloque.
+ **-p** o **--properties** <propiedades>: Propiedades de blob de almacenamiento para el archivo cargado. Las propiedades son pares clave=valor separadas por punto y coma (;). Las propiedades disponibles son contentType, contentEncoding, contentLanguage y cacheControl.
+ **-m** o **--metadata** <metadatos>: Metadatos de blob de almacenamiento para el archivo cargado. Los metadatos son pares clave=valor separados por punto y coma (;).
+ **--concurrenttaskcount** <recuento-tareas-simultáneas>: Número máximo de solicitudes de carga simultáneas.
+ **-q** o **--quiet**: Sobrescribe el blob de almacenamiento especificado sin confirmación.
+ **-a** o **--account-name** <nombreCuenta>: El nombre de la cuenta de almacenamiento.
+ **-k** o **--account-key** <nombreCuenta>: La clave de la cuenta de almacenamiento.
+ **-c** o **--connection-string** <cadenaConexión>: Cadena de conexión de almacenamiento.
+ **--debug**: Ejecuta el comando de almacenamiento en depuración.

**storage blob download [opciones] [contenedor] [blob] [destino]**

Este comando descarga el blob de almacenamiento especificado.

Este comando es compatible con las siguientes opciones adicionales:

+ **--container** <contenedor>: Nombre del contenedor de almacenamiento que se va a crear.
+ **-b** o **--blob** <nombreBlob>: Nombre de blob de almacenamiento.
+ **-d** o **--destination** [destino]: Archivo de destino o ruta de directorio de descarga.
+ **-m** o **--checkmd5**: La comprobación md5sum del archivo descargado.
+ **--concurrenttaskcount** <recuento-tareas-simultáneas>  El número máximo de solicitudes de carga simultáneas
+ **-q** o **--quiet**: Sobrescribe el archivo de destino sin confirmación.
+ **-a** o **--account-name** <nombreCuenta>: El nombre de la cuenta de almacenamiento.
+ **-k** o **--account-key** <nombreCuenta>: La clave de la cuenta de almacenamiento.
+ **-c** o **--connection-string** <cadenaConexión>: Cadena de conexión de almacenamiento.
+ **--debug**: Ejecuta el comando de almacenamiento en depuración.

##<a name ="Commands_to_manage_sql"></a>Comandos para administrar bases de datos SQL

Use estos comandos para administrar sus bases de datos SQL de Azure.

###Comandos para administrar servidores SQL.

Use estos comandos para administrar sus servidores SQL.

**sql server create <inicioSesiónAdministrador> <contraseñaAdministrador> <ubicación>**

Permite crear un nuevo servidor de base de datos.

	~$ azure sql server create test T3stte$t "West US"
	info:    Executing command sql server create
	+ Creating SQL Server
	data:    Server Name i1qwc540ts
	info:    sql server create command OK

**sql server show <nombre>**

Permite ver información sobre el servidor.

	~$ azure sql server show xclfgcndfg
	info:    Executing command sql server show
	+ Getting SQL server
	data:    SQL Server Name xclfgcndfg
	data:    SQL Server AdministratorLogin msopentechforums
	data:    SQL Server Location West US
	data:    SQL Server FullyQualifiedDomainName xclfgcndfg.database.windows.net
	info:    sql server show command OK

**sql server list**

Permite obtener la lista de servidores.

	~$ azure sql server list
	info:    Executing command sql server list
	+ Getting SQL server
	data:    Name        Location
	data:    ----------  --------
	data:    xclfgcndfg  West US
	info:    sql server list command OK

**sql server delete <nombre>**

Permite eliminar un servidor. 

	~$ azure sql server delete i1qwc540ts
	info:    Executing command sql server delete
	Delete server i1qwc540ts? [y/n] y
	+ Removing SQL Server
	info:    sql server delete command OK

###Comandos para administrar bases de datos SQL

Use estos comandos para administrar sus bases de datos SQL.

**sql db create [opciones] <nombreServidor> <nombreBaseDatos> <contraseñaAdministrador>**

Permite crear una nueva instancia de base de datos.

	~$ azure sql db create fr8aelne00 newdb test
	info:    Executing command sql db create
	Administrator password: ********
	+ Creating SQL Server Database
	info:    sql db create command OK

**sql db show [opciones] <nombreServidor> <nombreBaseDatos> <contraseñaAdministrador>**

Permite ver la información de la base de datos.

	C:\windows\system32>azure sql db show fr8aelne00 newdb test
	info:    Executing command sql db show
	Administrator password: ********
	+ Getting SQL server databases
	data:    Database _ ContentRootElement=m:properties, id=https://fr8aelne00.datab
	ase.windows.net/v1/ManagementService.svc/Server2('fr8aelne00')/Databases(4), ter
	m=Microsoft.SqlServer.Management.Server.Domain.Database, scheme=http://schemas.m
	icrosoft.com/ado/2007/08/dataservices/scheme, link=[rel=edit, title=Database, hr
	ef=Databases(4), rel=http://schemas.microsoft.com/ado/2007/08/dataservices/relat
	ed/Server, type=application/atom+xml;type=entry, title=Server, href=Databases(4)
	/Server, rel=http://schemas.microsoft.com/ado/2007/08/dataservices/related/Servi
	ceObjective, type=application/atom+xml;type=entry, title=ServiceObjective, href=
	Databases(4)/ServiceObjective, rel=http://schemas.microsoft.com/ado/2007/08/data
	services/related/DatabaseMetrics, type=application/atom+xml;type=entry, title=Da
	tabaseMetrics, href=Databases(4)/DatabaseMetrics, rel=http://schemas.microsoft.c
	om/ado/2007/08/dataservices/related/DatabaseCopies, type=application/atom+xml;ty
	pe=feed, title=DatabaseCopies, href=Databases(4)/DatabaseCopies], title=, update
	d=2013-11-18T19:48:27Z, name=
	data:    Database Id 4
	data:    Database Name newdb
	data:    Database ServiceObjectiveId 910b4fcb-8a29-4c3e-958f-f7ba794388b2
	data:    Database AssignedServiceObjectiveId 910b4fcb-8a29-4c3e-958f-f7ba794388b2
	data:    Database ServiceObjectiveAssignmentState 1
	data:    Database ServiceObjectiveAssignmentStateDescription Complete
	data:    Database ServiceObjectiveAssignmentErrorCode
	data:    Database ServiceObjectiveAssignmentErrorDescription
	data:    Database ServiceObjectiveAssignmentSuccessDate
	data:    Database Edition Web
	data:    Database MaxSizeGB 1
	data:    Database MaxSizeBytes 1073741824
	data:    Database CollationName SQL_Latin1_General_CP1_CI_AS
	data:    Database CreationDate
	data:    Database RecoveryPeriodStartDate
	data:    Database IsSystemObject
	data:    Database Status 1
	data:    Database IsFederationRoot
	data:    Database SizeMB -1
	data:    Database IsRecursiveTriggersOn
	data:    Database IsReadOnly
	data:    Database IsFederationMember
	data:    Database IsQueryStoreOn
	data:    Database IsQueryStoreReadOnly
	data:    Database QueryStoreMaxSizeMB
	data:    Database QueryStoreFlushPeriodSeconds
	data:    Database QueryStoreIntervalLengthMinutes
	data:    Database QueryStoreClearAll
	data:    Database QueryStoreStaleQueryThresholdDays
	info:    sql db show command OK

**sql db list [opciones] <nombreServidor> <contraseñaAdministrador>**

Enumera las bases de datos.

	~$ azure sql db list fr8aelne00 test
	info:    Executing command sql db list
	Administrator password: ********
	+ Getting SQL server databases
	data:    Name    Edition  Collation                     MaxSizeInGB
	data:    ------  -------  ----------------------------  -----------
	data:    master  Web      SQL_Latin1_General_CP1_CI_AS  5
	info:    sql db list command OK

**sql db delete [opciones] <nombreServidor> <nombreBaseDatos> <contraseñaAdministrador>**

Permite eliminar una base de datos.

	~$ azure sql db delete fr8aelne00 newdb test
	info:    Executing command sql db delete
	Administrator password: ********
	Delete database newdb? [y/n] y
	+ Getting SQL server databases
	+ Removing database
	info:    sql db delete command OK

###Comandos para administrar las reglas de firewall de SQL Server

Use estos comandos para administrar las reglas de firewall de SQL Server.

**sql firewallrule create [opciones] <nombreServidor> <nombreRegla> <direcciónIPInicial> <direcciónIPfinal>**

Permite crear una regla de firewall nueva para SQL Server.

	~$ azure sql firewallrule create fr8aelne00 allowed 131.107.0.0 131.107.255.255
	info:    Executing command sql firewallrule create
	+ Creating Firewall Rule
	info:    sql firewallrule create command OK

**sql firewallrule show [opciones] <nombreServidor> <nombreRegla>**

Permite ver la información de la regla de firewall.

	~$ azure sql firewallrule show fr8aelne00 allowed
	info:    Executing command sql firewallrule show
	+ Getting firewall rule
	data:    Firewall rule Name allowed
	data:    Firewall rule Type Microsoft.SqlAzure.FirewallRule
	data:    Firewall rule State Normal
	data:    Firewall rule SelfLink https://management.core.windows.net/9e672699-105
	5-41ae-9c36-e85152f2e352/services/sqlservers/servers/fr8aelne00/firewallrules/allowed
	data:    Firewall rule ParentLink https://management.core.windows.net/9e672699-1
	055-41ae-9c36-e85152f2e352/services/sqlservers/servers/fr8aelne00
	data:    Firewall rule StartIPAddress 131.107.0.0
	data:    Firewall rule EndIPAddress 131.107.255.255
	info:    sql firewallrule show command OK

**sql firewallrule list [opciones] <nombreServidor>**

Enumera las reglas de firewall.

	~$ azure sql firewallrule list fr8aelne00
	info:    Executing command sql firewallrule list
	\data:    Name     Start IP address  End IP address
	data:    -------  ----------------  ---------------
	data:    allowed  131.107.0.0       131.107.255.255
	+
	info:    sql firewallrule list command OK

**sql firewallrule delete [opciones] <nombreServidor> <nombreRegla>**

Este comando permite eliminar una regla de firewall.

	~$ azure sql firewallrule delete fr8aelne00 allowed
	info:    Executing command sql firewallrule delete
	Delete rule allowed? [y/n] y
	+ Removing firewall rule
	info:    sql firewallrule delete command OK

##<a name ="Commands_to_manage_vnet"></a>Comandos para administrar redes virtuales

Use estos comandos para administrar sus redes virtuales.

**network vnet create [opciones] <ubicación>**

Permite crear una nueva red virtual.

	~$ azure network vnet create vnet1 --location "West US" -v
	info:    Executing command network vnet create
	info:    Using default address space start IP: 10.0.0.0
	info:    Using default address space cidr: 8
	info:    Using default subnet start IP: 10.0.0.0
	info:    Using default subnet cidr: 11
	verbose: Address Space [Starting IP/CIDR (Max VM Count)]: 10.0.0.0/8 (16777216)
	verbose: Subnet [Starting IP/CIDR (Max VM Count)]: 10.0.0.0/11 (2097152)
	verbose: Fetching Network Configuration
	verbose: Fetching or creating affinity group
	verbose: Fetching Affinity Groups
	verbose: Fetching Locations
	verbose: Creating new affinity group AG1
	info:    Using affinity group AG1
	verbose: Updating Network Configuration
	info:    network vnet create command OK

**network vnet show <nombre>**

Permite ver la información de una red virtual.

	~$ azure network vnet show vnet1
	info:    Executing command network vnet show
	+ Fetching Virtual Networks
	data:    Name "vnet1"
	data:    Id "25786fbe-08e8-4e7e-b1de-b98b7e586c7a"
	data:    AffinityGroup "AG1"
	data:    State "Created"
	data:    AddressSpace AddressPrefixes 0 "10.0.0.0/8"
	data:    Subnets 0 Name "subnet-1"
	data:    Subnets 0 AddressPrefix "10.0.0.0/11"
	info:    network vnet show command OK

**vnet list**

Enumera todas las máquinas virtuales existentes.

	~$ azure network vnet list
	info:    Executing command network vnet list
	+ Fetching Virtual Networks
	data:    Name        Status   AffinityGroup
	data:    ----------  -------  -------------
	data:    vnet1      Created  AG1
	data:    vnet2      Created  AG1
	data:    vnet3      Created  AG1
	data:    vnet4      Created  AG1
	info:    network vnet list command OK


**network vnet delete <nombre>**

Permite eliminar la red virtual especificada.

	~$ azure network vnet delete opentechvn1
	info:    Executing command network vnet delete
	+ Fetching Network Configuration
	Delete the virtual network opentechvn1 ?  (y/n) y
	+ Deleting the virtual network opentechvn1
	info:    network vnet delete command OK

**network export [ruta-acceso-archivo]**

Para obtener una configuración de red avanzada, puede exportar la configuración de red localmente. Tenga en cuenta que la configuración de red exportada incluye la configuración del servidor DNS, la configuración de red virtual, la configuración del sitio de red local y otras configuraciones.

**network import [ruta-acceso-archivo]**

Permite importar una configuración de red local.

**network dnsserver register [opciones] <IPdns>**

Permite registrar el servidor DNS que desee usar para una resolución de nombres en la configuración de red.

	~$ azure network dnsserver register 98.138.253.109 --dns-id FrontEndDnsServer
	info:    Executing command network dnsserver register
	+ Fetching Network Configuration
	+ Updating Network Configuration
	info:    network dnsserver register command OK

**network dnsserver list**

Enumera todos los servidores DNS registrados en la configuración de red.

	~$ azure network dnsserver list
	info:    Executing command network dnsserver list
	+ Fetching Network Configuration
	data:    DNS Server ID         DNS Server IP
	data:    --------------------  --------------
	data:    DNS-bb39b4ac34d66a86  44.55.22.11
	data:    FrontEndDnsServer     98.138.253.109
	info:    network dnsserver list command OK

**network dnsserver unregister [opciones] <IPdns>**

Permite eliminar una entrada del servidor DNS de la configuración de red.

	~$ azure network dnsserver unregister 77.88.99.11
	info:    Executing command network dnsserver unregister
	+ Fetching Network Configuration
	Delete the DNS server entry dns-4 ( 77.88.99.11 ) %s ? (y/n) y
	+ Deleting the DNS server entry dns-4 ( 77.88.99.11 )
	info:    network dnsserver unregister command OK
