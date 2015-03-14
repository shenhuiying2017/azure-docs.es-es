<properties 
	pageTitle="Uso de la API de administración de servicios (Python) - Guía de características" 
	description="Vea cómo ejecutar tareas comunes de administración de servicios mediante programación con Python." 
	services="cloud-services" 
	documentationCenter="python" 
	authors="huguesv" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="python" 
	ms.topic="article" 
	ms.date="09/25/2014" 
	ms.author="huvalo"/>




# Uso de la administración de servicios con Python

En esta guía se describe cómo ejecutar tareas comunes de administración de servicios mediante programación con Python. La clase **ServiceManagementService** del [SDK de Azure para Python][download-SDK-Python] admite el acceso mediante programación a gran parte de la funcionalidad relacionada con la administración de servicios que se encuentra disponible en el [portal de administración][management-portal] (como **crear, actualizar y eliminar servicios en la nube, implementaciones, servicios de administración de datos, máquinas virtuales y grupos de afinidad**). Esta funcionalidad puede resultar útil para compilar aplicaciones que precisan de acceso mediante programación a la administración de servicios. 

## Tabla de contenido

* [Qué es la administración de servicios][]
* [Conceptos][]
* [Conexión a la administración de servicios][]
* [Lista de las ubicaciones disponibles][]
* [Creación de un servicio en la nube][]
* [Eliminación de un servicio en la nube][]
* [Creación de una implementación][]
* [Actualización de una implementación][]
* [Transferencia de implementaciones entre ensayo y producción][]
* [Eliminación de una implementación][]
* [Creación de un servicio de almacenamiento][]
* [Eliminación de un servicio de almacenamiento][]
* [Creación de un grupo de afinidad][]
* [Eliminación de un grupo de afinidad][]
* [Lista de los sistemas operativos disponibles][]
* [Creación de una imagen de sistema operativo][]
* [Eliminación de una imagen de sistema operativo][]
* [Creación de una máquina virtual][]
* [Eliminación de una máquina virtual][]
* [Pasos siguientes][]

## <a name="WhatIs"> </a>Qué es la administración de servicios
La API de administración de servicios proporciona acceso mediante programación a gran parte de la funcionalidad de administración de servicios disponible a través del [portal de administración][management-portal]. El SDK de Azure para Python le permite administrar los servicios en la nube, las cuentas de almacenamiento y los grupos de afinidad.

Para usar la API de administración de servicios, necesita [crear una cuenta de Azure](http://azure.microsoft.com/pricing/free-trial/). 

## <a name="Concepts"> </a>Conceptos
El SDK de Azure para Python ajusta la [API de administración de servicios de Azure][svc-mgmt-rest-api], que es una API de REST. Todas las operaciones de la API se realizan mediante SSL y se autentican mutuamente con los certificados X.509 v3. Se puede obtener acceso al servicio de administración desde un servicio que se ejecute en Azure, o bien directamente a través de Internet desde cualquier aplicación que envíe una solicitud HTTPS y reciba una respuesta HTTPS.

## <a name="Connect"> </a>Conexión a la administración de servicios
Para conectarse al extremo de la administración de servicios, necesita el identificador de la suscripción a Azure y un certificado de administración válido. Puede obtener el identificador de la suscripción a través del [portal de administración][management-portal].

> [AZURE.NOTE] A partir del SDK de Azure para Python v0.8.0, es posible usar certificados creados con OpenSSL al ejecutar en Windows.  Esto requiere Python 2.7.4 o posterior.

### Certificados de administración en Windows (MakeCert)

Puede crear un certificado de administración autofirmado en la máquina con `makecert.exe`.  Abra un **símbolo del sistema de Visual Studio** como **administrador** y use el siguiente comando, donde debe sustituir *AzureCertificate* por el nombre del certificado que desea usar.

    makecert -sky exchange -r -n "CN=AzureCertificate" -pe -a sha1 -len 2048 -ss My "AzureCertificate.cer"

El comando creará el archivo `.cer` y lo instalará en el almacén de certificados **Personal**. Para obtener información más detallada, consulte [Crear y cargar un certificado de administración para Azure](http://msdn.microsoft.com/library/windowsazure/gg551722.aspx).

Después de haber creado el certificado, necesitará cargar el archivo `.cer` en Azure mediante la acción "Cargar" de la pestaña "Configuración" del [portal de administración][management-portal].

Tras haber obtenido el identificador de la suscripción, haber creado un certificado y haber cargado el archivo `.cer` en Azure, puede conectarse al extremo de administración de Azure; para ello, transfiera el identificador de la suscripción y la ubicación del certificado del almacén de certificados **Personal** a **ServiceManagementService** (sustituya *AzureCertificate* por el nombre de su certificado):

	from azure import *
	from azure.servicemanagement import *

	subscription_id = '<your_subscription_id>'
	certificate_path = 'CURRENT_USER\\my\\AzureCertificate'

	sms = ServiceManagementService(subscription_id, certificate_path)

En el ejemplo anterior, `sms` es un objeto **ServiceManagementService**. La clase **ServiceManagementService** es la clase principal usada para administrar los servicios de Azure. 

### Certificados de administración en Windows/Mac/Linux (OpenSSL)
Puede usar [OpenSSL](http://www.openssl.org/) para crear el certificado de administración.  Necesitará crear dos certificados, uno para el servidor (un archivo `.cer`) y otro para el cliente (un archivo `.pem`). Para crear el archivo `.pem`, ejecute este comando:

	`openssl req -x509 -nodes -days 365 -newkey rsa:1024 -keyout mycert.pem -out mycert.pem`

Para crear el certificado `.cer`, ejecute este comando:

	`openssl x509 -inform pem -in mycert.pem -outform der -out mycert.cer`

Para obtener más información acerca de los certificados de Azure, consulte [Administración de certificados en Azure](http://msdn.microsoft.com/library/windowsazure/gg981929.aspx). Para obtener una descripción completa de los parámetros de OpenSSL, consulte la documentación en [http://www.openssl.org/docs/apps/openssl.html](http://www.openssl.org/docs/apps/openssl.html).

Después de haber creado estos archivos, necesitará actualizar el archivo `.cer` en Azure a través de la acción "Cargar" de la pestaña "Configuración" del [portal de administración][management-portal]; además, deberá anotar dónde ha guardado el archivo `.pem`.

Tras haber obtenido el identificador de la suscripción, creado un certificado y cargado el archivo `.cer` en Azure, puede conectarse al extremo de administración de Azure; para ello, transfiera el identificador de la suscripción y la ruta de acceso al archivo `.pem` a **ServiceManagementService**:

	from azure import *
	from azure.servicemanagement import *

	subscription_id = '<your_subscription_id>'
	certificate_path = '<path_to_.pem_certificate>'
	
	sms = ServiceManagementService(subscription_id, certificate_path)

En el ejemplo anterior, `sms` es un objeto **ServiceManagementService**. La clase **ServiceManagementService** es la clase principal usada para administrar los servicios de Azure. 

## <a name="ListAvailableLocations"> </a>Lista de las ubicaciones disponibles

Para enumerar las ubicaciones disponibles para los servicios hospedados, use el método **list\_locations**:

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	result = sms.list_locations()
	for location in result:
		print(location.name)

Al crear un servicio en la nube, servicio de almacenamiento o grupo de afinidad, necesitará proporcionar una ubicación válida. El método **list\_locations** siempre devolverá una lista actualizada de las ubicaciones disponibles actualmente. En el momento de la redacción de este documento, las ubicaciones disponibles son:

- Europa occidental 
- Sudeste asiático 
- Asia oriental 
- Centro-Norte de EE. UU 
- Europa del Norte 
- Centro-Sur de EE. UU 
- Oeste de EE. UU. 
- Este de EE. UU.

## <a name="CreateCloudService"> </a>Creación de un servicio en la nube

Cuando se crea una aplicación y se ejecuta en Azure, el código y configuración en conjunto se denominan un [servicio en la nube] de Azure (conocido como un  *hosted service* en versiones anteriores de Azure). El método **create\_hosted\_service** le permite crear un nuevo servicio hospedado, para lo que debe proporcionar un nombre de servicio hospedado (que debe ser exclusivo en Azure), una etiqueta (codificada automáticamente como base62), una descripción y una ubicación. Puede especificar un grupo de afinidad en lugar de una ubicación para el servicio. 

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	name = 'myhostedservice'
	label = 'myhostedservice'
	desc = 'my hosted service'
	location = 'West US'

	# You can either set the location or an affinity_group
	sms.create_hosted_service(name, label, desc, location)

Puede enumerar todos los servicios hospedados para la suscripción con el método **list\_hosted\_services**:

	result = sms.list_hosted_services()

	for hosted_service in result:
		print('Service name: ' + hosted_service.service_name)
		print('Management URL: ' + hosted_service.url)
		print('Affinity group: ' + hosted_service.hosted_service_properties.affinity_group)
		print('Location: ' + hosted_service.hosted_service_properties.location)
		print('')

Si desea obtener información acerca de un servicio hospedado en particular, puede hacerlo si transfiere el nombre del servicio hospedado al método **get\_hoster\_service\_properties**:

	hosted_service = sms.get_hosted_service_properties('myhostedservice')

	print('Service name: ' + hosted_service.service_name)
	print('Management URL: ' + hosted_service.url)
	print('Affinity group: ' + hosted_service.hosted_service_properties.affinity_group)
	print('Location: ' + hosted_service.hosted_service_properties.location)
			
Después de haber creado un servicio en la nube, puede implementar el código en el servicio con el método **create\_deployment**.

## <a name="DeleteCloudService"> </a>Eliminación de un servicio en la nube

Puede eliminar un servicio en la nube si transfiere el nombre del archivo al método **delete\_hosted\_service**:

	sms.delete_hosted_service('myhostedservice')

Tenga en cuenta que antes de eliminar un servicio, primero es necesario eliminar todas las implementaciones de dicho servicio. (Consulte [Eliminación implementación](#DeleteDeployment) para obtener información detallada.

## <a name="CreateDeployment"> </a>Configuración de una implementación

El método **create\_deployment** carga un nuevo [paquete de servicio] y crea una implementación nueva en el entorno de ensayo o producción. Los parámetros para este método son los siguientes:

* **name**: el nombre del servicio hospedado.
* **deployment\_name**: el nombre de la implementación.
* **slot**: una cadena que indica el espacio de  `staging` o `production`.
* **package_url**: la URL del paquete de implementación (un archivo .cspgk). El archivo del paquete debe almacenarse en una cuenta de almacenamiento de blobs de Azure con la misma suscripción que el servicio hospedado en el que se carga el paquete. Puede crear un paquete de implementación con los [cmdlets de Azure PowerShell] o con la [herramienta de línea de comandos cspack].
* **configuration**: El archivo de configuración del servicio (archivo .cscfg) codificado como base64.
* **label**: La etiqueta para el nombre del servicio hospedado (codificada automáticamente como base64).

En el ejemplo siguiente se crea una nueva implementación `v1` for a hosted service called `myhostedservice`:

	from azure import *
	from azure.servicemanagement import *
	import base64

	sms = ServiceManagementService(subscription_id, certificate_path)

	name = 'myhostedservice'
	deployment_name = 'v1'
	slot = 'production'
	package_url = 'URL_for_.cspkg_file'
	configuration = base64.b64encode(open('path_to_cscfg', 'rb'))
	label = 'myhostedservice'

	result = sms.create_deployment(name, slot, deployment_name, package_url, label, configuration)

	operation_result = sms.get_operation_status(result.request_id)
	print('Operation status: ' + operation_result.status)

Observe en el ejemplo anterior que el estado de la operación **create\_deployment** puede recuperarse transfiriendo el resultado devuelto por **create\_deployment** al método **get\_operation\_status**.

Puede tener acceso a las propiedades de implementación con el método **get\_deployment\_by\_slot** o el método **get\_deployment\_by\_name**. En el ejemplo siguiente se recupera una implementación mediante la definición de la ranura de implementación. En el ejemplo también se itera a través de todas las instancias de la implementación:

	result = sms.get_deployment_by_slot('myhostedservice', 'production')

	print('Name: ' + result.name)
	print('Slot: ' + result.deployment_slot)
	print('Private ID: ' + result.private_id)
	print('Status: ' + result.status)
	print('Instances:')
	for instance in result.role_instance_list:
		print('Instance name: ' + instance.instance_name)
		print('Instance status: ' + instance.instance_status)
		print('Instance size: ' + instance.instance_size)

## <a name="UpdateDeployment"> </a>Configuración de una implementación

Una implementación se puede actualizar al usar el método **change\_deployment\_configuration** o el método **update\_deployment\_status**.

El método **change\_deployment\_configuration** le permite cargar un nuevo archivo de configuración (`.cscfg`) del servicio, que cambiará algunas de las opciones de configuración del servicio (incluido el número de instancias de una implementación). Para obtener más información, consulte [Esquema de configuración del servicio de Azure (.cscfg)]. En el siguiente ejemplo se demuestra cómo cargar el nuevo archivo de configuración del servicio:

	from azure import *
	from azure.servicemanagement import *
	import base64

	sms = ServiceManagementService(subscription_id, certificate_path)

	name = 'myhostedservice'
	deployment_name = 'v1'
	configuration = base64.b64encode(open('path_to_cscfg', 'rb'))

	result = sms.change_deployment_configuration(name, deployment_name, configuration)

	operation_result = sms.get_operation_status(result.request_id)
	print('Operation status: ' + operation_result.status)


Observe en el ejemplo anterior que el estado de la operación **change\_deployment\_configuration** puede recuperarse transfiriendo el resultado devuelto por **change\_deployment\_configuration** al método **get\_operation\_status**.

El método **update\_deployment\_status** le permite definir el estado de una implementación como RUNNING o SUSPENDED. En el siguiente ejemplo se demuestra cómo definir el estado en RUNNING para una implementación llamada `v1` of a hosted service called `myhostedservice`:

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	name = 'myhostedservice'
	deployment_name = 'v1'

	result = update_deployment_status(name, deployment_name, 'Running')

## <a name="MoveDeployments"> </a>Transferencia de implementaciones entre ensayo y producción

Azure proporciona dos entornos de implementación: ensayo y producción. Normalmente un servicio se implementa en el entorno de ensayo para probarlo antes de implementarlo en el entorno de producción. Cuando sea el momento de promover el servicio en ensayo al entorno de producción, puede hacerlo sin tener que volver a implementar el servicio. Esto puede hacerse mediante el intercambio de las implementaciones. (Para obtener más información acerca del intercambio de implementaciones, consulte [Implementación de un servicio de Azure]).

En el siguiente ejemplo se muestra cómo usar el método **swap\_deployment** para intercambiar dos implementaciones (con los nombres de implementación) `v1` y `v2`). En el ejemplo, antes de llamar a **swap\_deployment**, la implementación `v1` está en el espacio de producción y la implementación `v2` está en el espacio de ensayo. Después de llamar a **swap\_deployment**, `v2` está en producción y `v1` está en ensayo.  

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	result = sms.swap_deployment('myhostedservice', 'v1', 'v2')

## <a name="DeleteDeployment"> </a>Eliminación de una implementación

Para eliminar una implementación, use el método **delete\_deployment**. En el ejemplo siguiente se muestra cómo eliminar una implementación llamada `v1`.

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	sms.delete_deployment('myhostedservice', 'v1')

## <a name="CreateStorageService"> </a>Creación de un servicio de almacenamiento

Un [servicio de almacenamiento] le proporciona acceso a [blobs][azure-blobs], [tablas][azure-tables] y [colas][azure-queues]. Para crear un servicio de almacenamiento, necesita un nombre para el servicio (entre 3 y 24 caracteres en minúscula y exclusivos en Azure), una descripción, una etiqueta (hasta 100 caracteres, codificados automáticamente como base64) y una ubicación o un grupo de afinidad. En el ejemplo siguiente se muestra cómo crear un servicio de almacenamiento mediante la definición de una ubicación. Si desea usar un grupo de afinidad, primero tiene que crearlo (consulte [Creación grupo de afinidad](#CreateAffinityGroup)) y establecerlo con el parámetro **affinity\_group**.

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	name = 'mystorageaccount'
	label = 'mystorageaccount'
	location = 'West US'
	desc = 'My storage account description.'

	result = sms.create_storage_account(name, desc, label, location=location)

	operation_result = sms.get_operation_status(result.request_id)
	print('Operation status: ' + operation_result.status)

Observe en el ejemplo anterior que el estado de la operación **create\_storage\_account** puede recuperarse transfiriendo el resultado devuelto por **create\_storage\_account** al método **get\_operation\_status**.  

Puede enumerar las cuentas de almacenamiento y sus propiedades con el método **list\_storage\_accounts**:

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	result = sms.list_storage_accounts()
	for account in result:
		print('Service name: ' + account.service_name)
		print('Affinity group: ' + account.storage_service_properties.affinity_group)
		print('Location: ' + account.storage_service_properties.location)
		print('')

## <a name="DeleteStorageService"> </a>Eliminación de un servicio de almacenamiento

Puede eliminar un servicio de almacenamiento si transfiere el nombre de dicho servicio de almacenamiento al método **delete\_storage\_account**. Con la eliminación del servicio de almacenamiento también se eliminarán todos los datos almacenados en el servicio (blobs, tablas y colas).

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	sms.delete_storage_account('mystorageaccount')

## <a name="CreateAffinityGroup"> </a>Configuración de un grupo de afinidad

Un grupo de afinidad es una agrupación lógica de los servicios de Azure que pide a Azure que localice los servicios para obtener un rendimiento optimizado. Por ejemplo, podría crear un grupo de afinidad en la ubicación "Oeste de EE. UU." y, a continuación, crear un [servicio en la nube](#CreateCloudService) en dicho grupo de afinidad. Si posteriormente crea un servicio de almacenamiento en el mismo grupo de afinidad, Azure sabe colocarlo en la ubicación "Oeste de EE. UU." y optimizarlo dentro del centro de datos para obtener el máximo rendimiento con los servicios en la nube en el mismo grupo de afinidad.

Para crear un grupo de afinidad, necesita un nombre, una etiqueta (codificada automáticamente como base64) y una ubicación. Si lo desea, también puede proporcionar una descripción:

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	name = 'myAffinityGroup'
	label = 'myAffinityGroup'
	location = 'West US'
	desc = 'my affinity group'

	sms.create_affinity_group(name, label, location, desc)

Tras haber creado un grupo de afinidad, puede especificar el grupo (en lugar de una ubicación) al [crear un servicio de almacenamiento](#CreateStorageService).

Puede enumerar los grupos de afinidad e inspeccionar sus propiedades mediante la llamada al método **list\_affinity\_groups**:

	result = sms.list_affinity_groups()

	for group in result:
		print('Name: ' + group.name)
		print('Description: ' + group.description)
		print('Location: ' + group.location)
		print('')

## <a name="DeleteAffinityGroup"> </a>Eliminación de un grupo de afinidad
	
Puede eliminar un grupo de afinidad si transfiere el nombre del grupo al método **delete\_affinity\_group**. Tenga en cuenta que para eliminar un grupo de afinidad, es necesario desasociar el grupo de afinidad de cualquier servicio (o deben eliminarse los servicios que usan el grupo de afinidad).

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	sms.delete_affinity_group('myAffinityGroup')

## <a name="ListOperatingSystems"> </a>Lista de los sistemas operativos disponibles

Para enumerar los sistemas operativos que están disponibles para los servicios hospedados, use el método **list\_operating\_systems**:

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	result = sms.list_operating_systems()

	for os in result:
		print('OS: ' + os.label)
		print('Family: ' + os.family_label)
		print('Active: ' + str(os.is_active))

De manera alternativa, también puede usar el método **list\_operating\_system\_families**, que agrupa los sistemas operativos por familia:

	result = sms.list_operating_system_families()

	for family in result:
		print('Family: ' + family.label)
		for os in family.operating_systems:
			if os.is_active:
				print('OS: ' + os.label)
				print('Version: ' + os.version)
		print('')

## <a name="CreateVMImage"> </a>Creación de una imagen de sistema operativo

Para agregar una imagen del sistema operativo al repositorio de imágenes, use el método **add\_os\_image**:

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	name = 'mycentos'
	label = 'mycentos'
	os = 'Linux' # Linux or Windows
	media_link = 'url_to_storage_blob_for_source_image_vhd'

	result = sms.add_os_image(label, media_link, name, os)

	operation_result = sms.get_operation_status(result.request_id)
	print('Operation status: ' + operation_result.status)

Para enumerar las imágenes del sistema operativo disponibles, use el método **list\_os\_images**. Estas imágenes comprenden todas las imágenes de la plataforma y las imágenes de usuario:

	result = sms.list_os_images()

	for image in result:
		print('Name: ' + image.name)
		print('Label: ' + image.label)
		print('OS: ' + image.os)
		print('Category: ' + image.category)
		print('Description: ' + image.description)
		print('Location: ' + image.location)
		print('Affinity group: ' + image.affinity_group)
		print('Media link: ' + image.media_link)
		print('')

## <a name="DeleteVMImage"> </a>Eliminación de una imagen de sistema operativo

Para eliminar una imagen de usuario, use el método **delete\_os\_image**:

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	result = sms.delete_os_image('mycentos')

	operation_result = sms.get_operation_status(result.request_id)
	print('Operation status: ' + operation_result.status)

## <a name="CreateVM"> </a>Creación de una máquina virtual

Para crear una máquina virtual, primero debe crear un [servicio en la nube](#CreateCloudService).  A continuación, cree la implementación de máquina virtual con el método **create\_virtual\_machine\_deployment**:

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	name = 'myvm'
	location = 'West US'

	# You can either set the location or an affinity_group
	sms.create_hosted_service(service_name=name,
		label=name,
		location=location)

	# Name of an os image as returned by list_os_images
	image_name = 'OpenLogic__OpenLogic-CentOS-62-20120531-es-es-30GB.vhd'

	# Destination storage account container/blob where the VM disk
	# will be created
	media_link = 'url_to_target_storage_blob_for_vm_hd'

	# Linux VM configuration, you can use WindowsConfigurationSet
	# for a Windows VM instead
	linux_config = LinuxConfigurationSet('myhostname', 'myuser', 'mypassword', True)

	os_hd = OSVirtualHardDisk(image_name, media_link)

	sms.create_virtual_machine_deployment(service_name=name,
		deployment_name=name,
		deployment_slot='production',
		label=name,
		role_name=name,
		system_config=linux_config,
		os_virtual_hard_disk=os_hd,
		role_size='Small')

## <a name="DeleteVM"> </a>Eliminación una máquina virtual

Para eliminar una máquina virtual, primero debe eliminar la implementación con el método **delete\_deployment**:

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	sms.delete_deployment(service_name='myvm',
		deployment_name='myvm')

El servicio en la nube puede eliminarse entonces con el método **delete\_hosted\_service**:

	sms.delete_hosted_service(service_name='myvm')

## <a name="NextSteps"> </a>Pasos siguientes

Ahora que está familiarizado con los aspectos básicos de la administración de servicios, siga estos vínculos para realizar tareas más complejas.

-   Consulte la referencia de MSDN: [Servicios en la nube][]
-   Consulte la referencia de MSDN: [Máquinas virtuales][]

[Qué es la administración de servicios]: #WhatIs
[Conceptos]: #Concepts
[Conexión a la administración de servicios]: #Connect
[Lista de las ubicaciones disponibles]: #ListAvailableLocations
[Creación de un servicio en la nube]: #CreateCloudService
[Eliminación de un servicio en la nube]: #DeleteCloudService
[Creación de una implementación]: #CreateDeployment
[Actualización de una implementación]: #UpdateDeployment
[Transferencia de implementaciones entre ensayo y producción]: #MoveDeployments
[Eliminación de una implementación]: #DeleteDeployment
[Creación de un servicio de almacenamiento]: #CreateStorageService
[Eliminación de un servicio de almacenamiento]: #DeleteStorageService
[Creación de un grupo de afinidad]: #CreateAffinityGroup
[Eliminación de un grupo de afinidad]: #DeleteAffinityGroup
[Lista de los sistemas operativos disponibles]: #ListOperatingSystems
[Creación de una imagen de sistema operativo]: #CreateVMImage
[Eliminación de una imagen de sistema operativo]: #DeleteVMImage
[Creación de una máquina virtual]: #CreateVM
[Eliminación de una máquina virtual]: #DeleteVM
[Pasos siguientes]: #NextSteps
[management-portal]: https://manage.windowsazure.com/
[svc-mgmt-rest-api]: http://msdn.microsoft.com/library/windowsazure/ee460799.aspx


[download-SDK-Python]: https://www.windowsazure.com/es-es/develop/python/common-tasks/install-python/
[servicio en la nube]: http://windowsazure.com/es-es/documentation/articles/cloud-services-what-is
[paquete de servicio]: http://msdn.microsoft.com/library/windowsazure/jj155995.aspx
[cmdlets de Azure PowerShell]: https://www.windowsazure.com/es-es/develop/php/how-to-guides/powershell-cmdlets/
[herramienta de línea de comandos cspack]: http://msdn.microsoft.com/library/windowsazure/gg432988.aspx
[Implementación de un servicio de Azure]: http://msdn.microsoft.com/library/windowsazure/gg433027.aspx
[servicio de almacenamiento]: https://www.windowsazure.com/es-es/manage/services/storage/what-is-a-storage-account/
[azure-blobs]: https://www.windowsazure.com/es-es/develop/python/how-to-guides/blob-service/
[azure-tables]: https://www.windowsazure.com/es-es/develop/python/how-to-guides/table-service/
[azure-queues]: https://www.windowsazure.com/es-es/develop/python/how-to-guides/queue-service/
[Esquema de configuración del servicio de Azure (.cscfg)]: http://msdn.microsoft.com/library/windowsazure/ee758710.aspx
[Servicios en la nube]: http://msdn.microsoft.com/library/windowsazure/jj155995.aspx
[Máquinas virtuales]: http://msdn.microsoft.com/library/windowsazure/jj156003.aspx

<!--HONumber=45--> 
