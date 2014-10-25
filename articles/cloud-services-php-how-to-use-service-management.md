<properties linkid="develop-php-how-to-guides-service-management" urlDisplayName="Service Management" pageTitle="How to use Azure service management APIs (PHP)" metaKeywords="" description="Learn how to use the Azure PHP Service Management APIs to manage cloud services and other Azure applications." metaCanonical="" services="" documentationCenter="PHP" title="How to use Service Management from PHP" authors="robmcm" solutions="" manager="wpickett" editor="mollybos" videoId="" scriptId="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="01/01/1900" ms.author="robmcm"></tags>

# Uso de la Administración de servicios con PHP

En esta guía se describe cómo ejecutar tareas comunes de administración de servicios mediante programación con PHP. La clase [ServiceManagementRestProxy][] del [SDK de Azure para PHP][] admite el acceso programático a gran parte de la funcionalidad relacionada con la administración de servicios que se encuentra disponible en el [portal de administración][] (como **creación, actualización y eliminación de servicios en la nube, implementaciones, servicios de almacenamiento y grupos de afinidad**). Esta funcionalidad puede resultar útil para compilar aplicaciones que precisan de acceso mediante programación a la administración de servicios.

## Tabla de contenido

-   [Qué es la administración de servicios][]
-   [Conceptos][]
-   [Creación de una aplicación PHP][]
-   [Obtención de las bibliotecas de clientes de Azure][]
-   [Direccionamiento del administración de servicios][]
-   [Direccionamiento del ubicaciones disponibles][]
-   [Direccionamiento del servicio en la nube][]
-   [Direccionamiento del servicio en la nube][1]
-   [Direccionamiento del implementación][]
-   [Direccionamiento del implementación][2]
-   [Direccionamiento del implementaciones entre ensayo y producción][]
-   [Direccionamiento del implementación][3]
-   [Direccionamiento del servicio de almacenamiento][]
-   [Direccionamiento del servicio de almacenamiento][4]
-   [Direccionamiento del grupo de afinidad][]
-   [Direccionamiento del grupo de afinidad][5]

## <span id="WhatIs"></span></a>Qué es la administración de servicios

La API de administración de servicios ofrece acceso mediante programación a gran parte de la funcionalidad de administración de servicios disponible a través del [portal de administración][]. El SDK de Azure para PHP le permite administrar los servicios en la nube, las cuentas de almacenamiento y los grupos de afinidad.

Para usar la API de administración de servicios, necesita [crear una cuenta de Azure][].

## <span id="Concepts"></span></a>Conceptos

El SDK de Azure para PHP ajusta la [API de administración de servicios de Azure][], que es una API de REST. Todas las operaciones de la API se realizan mediante SSL y se autentican mutuamente con los certificados X.509 v3. Se puede obtener acceso al servicio de administración desde un servicio que se ejecute en Azure, o bien directamente a través de Internet desde cualquier aplicación que envíe una solicitud HTTPS y reciba una respuesta HTTPS.

## <span id="CreateApplication"></span></a>Creación de una aplicación PHP

El único requisito a la hora de crear una aplicación PHP que usa la Administración de servicios de Azure es que el código haga referencia a clases del SDK de Azure para PHP. Puede utilizar cualquier herramienta de desarrollo para crear la aplicación, incluido el Bloc de notas.

En esta guía, utilizará funciones del servicio a las que se puede llamar desde una aplicación PHP localmente o bien mediante código a través de un rol web, rol de trabajo o sitio web de Azure.

## <span id="GetClientLibraries"></span></a>Obtención de las bibliotecas de clientes de Azure

[WACOM.INCLUDE [get-client-libraries][]]

## <span id="Connect"></span></a>Direccionamiento del a la administración de servicios

Para conectarse al extremo de la Administración de servicios, necesita el identificador de la suscripción a Azure y la ruta de acceso a un certificado de administración válido. Puede obtener el identificador de la suscripción a través del [portal de administración][] y, además, puede crear certificados de administración de varias maneras. En esta guía se usa [OpenSSL][], que puede [descargar para Windows][] y ejecutar en una consola.

Realmente necesita crear dos certificados, uno para el servidor (un archivo `.cer`.cer) y otro para el cliente (un archivo `.pem`). Para crear el archivo `.pem`, ejecute este comando:

    `openssl req -x509 -nodes -days 365 -newkey rsa:1024 -keyout mycert.pem -out mycert.pem`

Para crear el certificado `.cer`, ejecute este comando:

    `openssl x509 -inform pem -in mycert.pem -outform der -out mycert.cer`

Para obtener más información acerca de los certificados de Azure, consulte [Información general sobre los certificados de Azure][]. Para obtener una descripción completa de los parámetros de OpenSSL, consulte la documentación disponible en [http://www.openssl.org/docs/apps/openssl.html](http://www.openssl.org/docs/apps/openssl.html).

Si ha descargado e importado el archivo de configuración de publicación con las [Herramientas de línea de comandos de Azure][], puede usar el archivo `.pem` que las herramientas crean en lugar de crear su propio archivo. Las herramientas crean un archivo `.cer`, lo cargan en Azure y, a continuación, colocan el archivo `.pem` correspondiente en el directorio `.azure` del equipo (en el directorio de usuarios).

Después de haber creado estos archivos, necesitará cargar el archivo `.cer` en Azure mediante el [portal de administración][]; además, deberá anotar dónde ha guardado el archivo `.pem`.

Tras haber obtenido el identificador de la suscripción, creado un certificado y cargado el archivo `.cer` en Azure, puede conectarse al extremo de administración de Azure; para ello, cree una cadena de conexión y transfiérala al método **createServiceManagementService** en la clase **ServicesBuilder**:

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;

    $conn_string = "SubscriptionID=<your_subscription_id>;CertificatePath=<path_to_.pem_certificate>";

    $serviceManagementRestProxy = ServicesBuilder::getInstance()->createServiceManagementService($conn_string);

En el ejemplo anterior, `$serviceManagementRestProxy` es un objeto [ServiceManagementRestProxy][]. La clase **ServiceManagementRestProxy** es la clase principal usada para administrar los servicios de Azure.

## <span id="ListAvailableLocations"></span></a>Direccionamiento del de las ubicaciones disponibles

Para enumerar las ubicaciones disponibles para los servicios hospedados, use el método **ServiceManagementRestProxy-\>listLocations**:

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;

    try{
        $serviceManagementRestProxy = ServicesBuilder::getInstance()->createServiceManagementService($conn_string);

        $result = $serviceManagementRestProxy->listLocations();

        $locations = $result->getLocations();

        foreach($locations as $location){
              echo $location->getName()."<br />";
        }
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/es-es/library/windowsazure/ee460801
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

Al crear un servicio en la nube, servicio de almacenamiento o grupo de afinidad, necesitará proporcionar una ubicación válida. El método **listLocations** siempre devolverá una lista actualizada de las ubicaciones disponibles actualmente. En el momento de la redacción de este documento, las ubicaciones disponibles son:

-   En cualquier parte de EE. UU.
-   En cualquier parte de Europa
-   Europa occidental
-   En cualquier parte de Asia
-   Sudeste asiático
-   Asia oriental
-   Centro-Norte de EE. UU
-   Europa del Norte
-   Centro-Sur de EE. UU
-   Oeste de EE. UU.
-   Este de EE. UU.

> [WACOM.NOTE]
> En los siguientes ejemplos de código, las ubicaciones se traspasan a los métodos como cadenas. Sin embargo, también puede pasar las ubicaciones como enumeraciones con la utilización de la clase `WindowsAzure\ServiceManagement\Models\Locations`. Por ejemplo, en lugar de pasar "Oeste de EE. UU." a un método que acepta una ubicación, podría pasar `Locations::WEST_US`.

## <span id="CreateCloudService"></span></a>Direccionamiento del un servicio en la nube

Al crear una aplicación y ejecutarla en Azure, al código y a la configuración se les denomina de forma conjunta un [servicio en la nube][] de Azure (conocido como un *servicio hospedado* en las versiones anteriores de Azure). El método **createHostedServices** le permite crear un nuevo servicio hospedado, para lo que debe proporcionar un nombre de servicio hospedado (que debe ser exclusivo en Azure), una etiqueta (el nombre del servicio hospedado codificado como base64) y un objeto **CreateServiceOptions**. El objeto [CreateServiceOptions][] le permite definir la ubicación *o* el grupo de afinidad para el servicio.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\ServiceManagement\Models\CreateServiceOptions;
    use WindowsAzure\Common\ServiceException;

    try{
        // Create REST proxy.
        $serviceManagementRestProxy = ServicesBuilder::getInstance()->createServiceManagementService($conn_string);
        
        $name = "myhostedservice";
        $label = base64_encode($name);
        $options = new CreateServiceOptions();
        $options->setLocation('West US');
        // Instead of setLocation, you can use setAffinityGroup
        // to set an affinity group.

        $result = $serviceManagementRestProxy->createHostedService($name, $label, $options);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/es-es/library/windowsazure/ee460801
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

Puede enumerar todos los servicios hospedados para la suscripción con el método **listHostedServices** que devuelve un objeto [ListHostedServicesResult][]. La llamada al método **getHostedServices** le permite entonces aplicar un bucle a través de una matriz de objetos [HostedServices] y recuperar las propiedades del servicio:

    $listHostedServicesResult = $serviceManagementRestProxy->listHostedServices();

    $hosted_services = $listHostedServicesResult->getHostedServices();

    foreach($hosted_services as $hosted_service){
        echo "Service name: ".$hosted_service->getName()."<br />";
        echo "Management URL: ".$hosted_service->getUrl()."<br />";
        echo "Affinity group: ".$hosted_service->getAffinityGroup()."<br />";
        echo "Location: ".$hosted_service->getLocation()."<br />";
        echo "------<br />";
        }

Si desea obtener información acerca de un servicio hospedado en particular, puede hacerlo si transfiere el nombre del servicio hospedado al método **getHostedServiceProperties**:

    $getHostedServicePropertiesResult = $serviceManagementRestProxy->getHostedServiceProperties("myhostedservice");
        
    $hosted_service = $getHostedServicePropertiesResult->getHostedService();
        
    echo "Service name: ".$hosted_service->getName()."<br />";
    echo "Management URL: ".$hosted_service->getUrl()."<br />";
    echo "Affinity group: ".$hosted_service->getAffinityGroup()."<br />";
    echo "Location: ".$hosted_service->getLocation()."<br />";

Después de haber creado un servicio en la nube, puede implementar el código en el servicio con el método [createDeployment][Direccionamiento del implementación].

## <span id="DeleteCloudService"></span></a>Direccionamiento del un servicio en la nube

Puede eliminar un servicio en la nube si transfiere el nombre del servicio al método **deleteHostedService**:

    $serviceManagementRestProxy->deleteHostedService("myhostedservice");

Tenga en cuenta que antes de eliminar un servicio, primero es necesario eliminar todas las implementaciones de dicho servicio. (Consulte [Eliminación de una implementación][3] para obtener información detallada).

## <span id="CreateDeployment"></span></a>Direccionamiento del de una implementación

El método **createDeployment** carga un nuevo [paquete de servicio][] y crea una implementación nueva en el entorno de ensayo o producción. Los parámetros para este método son los siguientes:

-   **$name**: el nombre del servicio hospedado.
-   **$deploymentName**: el nombre de la implementación.
-   **$slot**: una enumeración que indica la ranura staging o production.
-   **$packageUrl**: la URL del paquete de implementación (un archivo .cspgk). El archivo del paquete debe almacenarse en una cuenta de almacenamiento de blobs de Azure con la misma suscripción que el servicio hospedado en el que se carga el paquete. Puede crear un paquete de implementación con los [cmdlets de Azure PowerShell][] o con la [Herramienta de línea de comandos CSPack][].
-   **$configuration**: el archivo de configuración del servicio (archivo .cscfg).
-   **$label**: el nombre del servicio codificado como base64.

El ejemplo siguiente crea una nueva implementación en la ranura production de un servicio hospedado llamado `myhostedservice`:

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\ServiceManagement\Models\DeploymentSlot;
    use WindowsAzure\Common\ServiceException;

    try{
        // Create REST proxy.
        $serviceManagementRestProxy = ServicesBuilder::getInstance()->createServiceManagementService($conn_string);
        
        $name = "myhostedservice";
        $deploymentName = "v1";
        $slot = DeploymentSlot::PRODUCTION;
        $packageUrl = "URL_for_.cspkg_file";
        $configuration = base64_encode(file_get_contents('path_to_.cscfg_file'));
        $label = base64_encode($name);

        $result = $serviceManagementRestProxy->createDeployment($name,
                                                         $deploymentName,
                                                         $slot,
                                                         $packageUrl,
                                                         $configuration,
                                                         $label);
        
        $status = $serviceManagementRestProxy->getOperationStatus($result);
        echo "Operation status: ".$status->getStatus()."<br />";
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/es-es/library/windowsazure/ee460801
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

Observe en el ejemplo anterior que el estado de la operación **createDeployment** puede recuperarse transfiriendo el resultado devuelto por **createDeployment** al método **getOperationStatus**.

Puede obtener acceso a las propiedades de implementación con el método **getDeployment**. El siguiente ejemplo recupera una implementación al especificar la ranura de implementación en el objeto [GetDeploymentOptions][ListHostedServicesResult], pero podría en cambio especificar el nombre de la implementación. En el ejemplo también se itera a través de todas las instancias de la implementación:

    $options = new GetDeploymentOptions();
    $options->setSlot(DeploymentSlot::PRODUCTION);
        
    $getDeploymentResult = $serviceManagementRestProxy->getDeployment("myhostedservice", $options);
    $deployment = $getDeploymentResult->getDeployment();

    echo "Name: ".$deployment->getName()."<br />";
    echo "Slot: ".$deployment->getSlot()."<br />";
    echo "Private ID: ".$deployment->getPrivateId()."<br />";
    echo "Status: ".$deployment->getStatus()."<br />";
    echo "Instances: <br />";
    foreach($deployment->getroleInstanceList() as $roleInstance){
        echo "Instance name: ".$roleInstance->getInstanceName()."<br />";
        echo "Instance status: ".$roleInstance->getInstanceStatus()."<br />";
        echo "Instance size: ".$roleInstance->getInstanceSize()."<br />";
    }
    echo "------<br />";

## <span id="UpdateDeployment"></span></a>Direccionamiento del de una implementación

Una implementación se puede actualizar al usar el método **changeDeploymentConfiguration** o el método **updateDeploymentStatus**.

El método **changeDeploymentConfiguration** le permite cargar un nuevo archivo de configuración (`.cscfg`) del servicio, que cambiará algunas de las opciones de configuración del servicio (incluido el número de instancias de una implementación). Para obtener más información, consulte [Esquema de configuración del servicio de Azure (archivo .cscfg)][]. En el siguiente ejemplo se demuestra cómo cargar el nuevo archivo de configuración del servicio:

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\ServiceManagement\Models\ChangeDeploymentConfigurationOptions;
    use WindowsAzure\ServiceManagement\Models\DeploymentSlot;
    use WindowsAzure\Common\ServiceException;

    try{
        // Create REST proxy.
        $serviceManagementRestProxy = ServicesBuilder::getInstance()->createServiceManagementService($conn_string);
        
        $name = "myhostedservice";
        $configuration = base64_encode(file_get_contents('path to .cscfg file'));
        $options = new ChangeDeploymentConfigurationOptions();
        $options->setSlot(DeploymentSlot::PRODUCTION);

        $result = $serviceManagementRestProxy->changeDeploymentConfiguration($name, $configuration, $options);
        
        $status = $serviceManagementRestProxy->getOperationStatus($result);
        echo "Operation status: ".$status->getStatus()."<br />";
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/es-es/library/windowsazure/ee460801
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

Observe en el ejemplo anterior que el estado de la operación **changeDeploymentConfiguration** puede recuperarse transfiriendo el resultado devuelto por **changeDeploymentConfiguration** al método **getOperationStatus**.

El método **updateDeploymentStatus** le permite definir el estado de una implementación como RUNNING o SUSPENDED. En el siguiente ejemplo se demuestra cómo definir el estado en RUNNING para una implementación en la ranura de producción de un servicio hospedado llamado `myhostedservice`:

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\ServiceManagement\Models\DeploymentStatus;
    use WindowsAzure\ServiceManagement\Models\DeploymentSlot;
    use WindowsAzure\ServiceManagement\Models\GetDeploymentOptions;
    use WindowsAzure\Common\ServiceException;

    try{
        // Create REST proxy.
        $serviceManagementRestProxy = ServicesBuilder::getInstance()->createServiceManagementService($conn_string);
        
        $options = new GetDeploymentOptions();
        $options->setSlot(DeploymentSlot::PRODUCTION);
        
        $result = $serviceManagementRestProxy->updateDeploymentStatus("myhostedservice", DeploymentStatus::RUNNING, $options);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/es-es/library/windowsazure/ee460801
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

## <span id="MoveDeployments"></span></a>Direccionamiento del de implementaciones entre ensayo y producción

Azure ofrece dos entornos de implementación: ensayo y producción. Normalmente un servicio se implementa en el entorno de ensayo para probarlo antes de implementarlo en el entorno de producción. Cuando sea el momento de promover el servicio en ensayo al entorno de producción, puede hacerlo sin tener que volver a implementar el servicio. Esto puede hacerse mediante el intercambio de las implementaciones. (Para obtener más información acerca del intercambio de implementaciones, consulte [Información general sobre la administración de implementaciones en Azure][]).

En el ejemplo siguiente se muestra cómo usar el método **swapDeployment** para intercambiar dos implementaciones (con los nombres de implementación `v1` y `v2`). En el ejemplo, antes de llamar a **swapDeployment**, la implementación `v1` está en la ranura de producción y la implementación `v2` está en la ranura de ensayo. Después de llamar a **swapDeployment**, `v2` está en producción y `v1` está en ensayo.

    require_once 'vendor\autoload.php'; 

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;

    try{
        // Create REST proxy.
        $serviceManagementRestProxy = ServicesBuilder::getInstance()->createServiceManagementService($conn_string);
        
        $result = $serviceManagementRestProxy->swapDeployment("myhostedservice", "v2", "v1");
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/es-es/library/windowsazure/ee460801
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

## <span id="DeleteDeployment"></span></a>Direccionamiento del una implementación

Para eliminar una implementación, use el método **deleteDeployment**. En el siguiente ejemplo se muestra cómo eliminar una implementación en el entorno de ensayo mediante el método **setSlot** en un objeto [GetDeploymentOptions][ListHostedServicesResult] para transferirlo a continuación a **deleteDeployment**. En lugar de especificar una implementación por la ranura, puede utilizar el método **setName** en la clase [GetDepolymentOptions] para especificar una implementación mediante el nombre de la implementación.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\ServiceManagement\Models\GetDeploymentOptions;
    use WindowsAzure\ServiceManagement\Models\DeploymentSlot;
    use WindowsAzure\Common\ServiceException;

    try{
        // Create REST proxy.
        $serviceManagementRestProxy = ServicesBuilder::getInstance()->createServiceManagementService($conn_string);
        
        $options = new GetDeploymentOptions();
        $options->setSlot(DeploymentSlot::STAGING);
        
        $result = $serviceManagementRestProxy->deleteDeployment("myhostedservice", $options);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/es-es/library/windowsazure/ee460801
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

## <span id="CreateStorageService"></span></a>Direccionamiento del de un servicio de almacenamiento

Un [servicio de almacenamiento][] le proporciona acceso a [blobs][], [tablas][] y [colas][] de Azure. Para crear un servicio de almacenamiento, necesita un nombre para el servicio (entre 3 y 24 caracteres en minúscula y exclusivos en Azure), una etiqueta (un nombre codificado como base64 para el servicio, hasta 100 caracteres) y una ubicación o un grupo de afinidad. Proporcionar una descripción para el servicio es opcional. La ubicación, el grupo de afinidad y la descripción se encuentran en un objeto [CreateServiceOptions][] que se transfiere al método **createStorageService**. En el ejemplo siguiente se muestra cómo crear un servicio de almacenamiento mediante la definición de una ubicación. Si desea usar un grupo de afinidad, primero tiene que crearlo (consulte [Creación de un grupo de afinidad][Direccionamiento del grupo de afinidad]) y defínalo con el método **CreateServiceOptions-\>setAffinityGroup**.

    require_once 'vendor\autoload.php';
     
    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\ServiceManagement\Models\CreateServiceOptions;
    use WindowsAzure\Common\ServiceException;
     
     
    try{
        // Create REST proxy.
        $serviceManagementRestProxy = ServicesBuilder::getInstance()->createServiceManagementService($conn_string);
        
        $name = "mystorageaccount";
        $label = base64_encode($name);
        $options = new CreateServiceOptions();
        $options->setLocation('West US');
        $options->setDescription("My storage account description.");

        $result = $serviceManagementRestProxy->createStorageService($name, $label, $options);

        $status = $serviceManagementRestProxy->getOperationStatus($result);
        echo "Operation status: ".$status->getStatus()."<br />";
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/es-es/library/windowsazure/ee460801
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

Observe en el ejemplo anterior que el estado de la operación **createStorageService** puede recuperarse transfiriendo el resultado devuelto por **createStorageService** al método **getOperationStatus**.

Puede enumerar las cuentas de almacenamiento y sus propiedades con el método **listStorageServices**:

    // Create REST proxy.
    $serviceManagementRestProxy = ServicesBuilder::getInstance()->createServiceManagementService($conn_string);
    $getStorageServicesResult = $serviceManagementRestProxy->listStorageServices();
    $storageServices = $getStorageServicesResult->getStorageServices();

    foreach($storageServices as $storageService){
        echo "Service name: ".$storageService->getName()."<br />";
        echo "Service URL: ".$storageService->getUrl()."<br />";
        echo "Affinity group: ".$storageService->getAffinityGroup()."<br />";
        echo "Location: ".$storageService->getLocation()."<br />";
        echo "------<br />";
    }

## <span id="DeleteStorageService"></span></a>Direccionamiento del de un servicio de almacenamiento

Puede eliminar un servicio de almacenamiento si transfiere el nombre de dicho servicio al método **deleteStorageService**. Con la eliminación del servicio de almacenamiento también se eliminarán todos los datos almacenados en el servicio (blobs, tablas y colas).

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;

    try{
        // Create REST proxy.
        $serviceManagementRestProxy = ServicesBuilder::getInstance()->createServiceManagementService($conn_string);
        
        $serviceManagementRestProxy->deleteStorageService("mystorageservice");
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/es-es/library/windowsazure/ee460801
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

## <span id="CreateAffinityGroup"></span></a>Direccionamiento del de un grupo de afinidad

Un grupo de afinidad es una agrupación lógica de los servicios de Azure que pide a Azure que localice los servicios para obtener un rendimiento optimizado. Por ejemplo, puede crear un grupo de afinidad en la ubicación "Oeste de EE. UU." y, a continuación, crear un [servicio en la nube][Direccionamiento del servicio en la nube] en dicho grupo de afinidad. Si posteriormente crea un servicio de almacenamiento en el mismo grupo de afinidad, Azure sabe colocarlo en la ubicación "Oeste de EE. UU." y optimizarlo dentro del centro de datos para obtener el máximo rendimiento con los servicios en la nube en el mismo grupo de afinidad.

Para crear un grupo de afinidad, necesita un nombre, una etiqueta (el nombre de codificado como base64) y la ubicación. Si lo desea, también puede proporcionar una descripción:

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\ServiceManagement\Models\CreateAffinityGroupOptions;
    use WindowsAzure\Common\ServiceException;
     
    try{
        // Create REST proxy.
        $serviceManagementRestProxy = ServicesBuilder::getInstance()->createServiceManagementService($conn_string);
        
        $name = "myAffinityGroup";
        $label = base64_encode($name);
        $location = "West US";

        $options = new CreateAffinityGroupOptions();
        $options->setDescription = "My affinity group description.";
        
        $serviceManagementRestProxy->createAffinityGroup($name, $label, $location, $options);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/es-es/library/windowsazure/ee460801
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

Tras haber creado un grupo de afinidad, puede especificar el grupo (en lugar de una ubicación) al [crear un servicio de almacenamiento][Direccionamiento del servicio de almacenamiento].

Puede enumerar grupos de afinidad e inspeccionar sus propiedades si llama al método **listAffinityGroups** y, a continuación, llama a los métodos adecuados en la clase [AffinityGroup][]:

    $result = $serviceManagementRestProxy->listAffinityGroups();

    $groups = $result->getAffinityGroups();

    foreach($groups as $group){
        echo "Name: ".$group->getName()."<br />";
        echo "Description: ".$group->getDescription()."<br />";
        echo "Location: ".$group->getLocation()."<br />";
        echo "------<br />";
    }

## <span id="DeleteAffinityGroup"></span></a>Direccionamiento del de un grupo de afinidad

Puede eliminar un grupo de afinidad si transfiere el nombre del grupo al método **deleteAffinityGroup**. Tenga en cuenta que para eliminar un grupo de afinidad, es necesario desasociar el grupo de afinidad de cualquier servicio (o deben eliminarse los servicios que usan el grupo de afinidad).

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;

    try{
        // Create REST proxy.
        $serviceManagementRestProxy = ServicesBuilder::getInstance()->createServiceManagementService($conn_string);
        
        // An affinity group must be disassociated from all services 
        // before it can be deleted.
        $serviceManagementRestProxy->deleteAffinityGroup("myAffinityGroup");
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/es-es/library/windowsazure/ee460801
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

  [ServiceManagementRestProxy]: https://github.com/WindowsAzure/azure-sdk-for-php/blob/master/WindowsAzure/ServiceManagement/ServiceManagementRestProxy.php
  [SDK de Azure para PHP]: ../php-download-sdk/
  [portal de administración]: https://manage.windowsazure.com/
  [Qué es la administración de servicios]: #WhatIs
  [Conceptos]: #Concepts
  [Creación de una aplicación PHP]: #CreateApplication
  [Obtención de las bibliotecas de clientes de Azure]: #GetClientLibraries
  [Direccionamiento del administración de servicios]: #Connect
  [Direccionamiento del ubicaciones disponibles]: #ListAvailableLocations
  [Direccionamiento del servicio en la nube]: #CreateCloudService
  [1]: #DeleteCloudService
  [Direccionamiento del implementación]: #CreateDeployment
  [2]: #UpdateDeployment
  [Direccionamiento del implementaciones entre ensayo y producción]: #MoveDeployments
  [3]: #DeleteDeployment
  [Direccionamiento del servicio de almacenamiento]: #CreateStorageService
  [4]: #DeleteStorageService
  [Direccionamiento del grupo de afinidad]: #CreateAffinityGroup
  [5]: #DeleteAffinityGroup
  [crear una cuenta de Azure]: /es-es/pricing/free-trial/
  [API de administración de servicios de Azure]: http://msdn.microsoft.com/es-es/library/windowsazure/ee460799.aspx
  [get-client-libraries]: ../includes/get-client-libraries.md
  [OpenSSL]: http://www.openssl.org/
  [descargar para Windows]: http://www.openssl.org/related/binaries.html
  [Información general sobre los certificados de Azure]: http://msdn.microsoft.com/es-es/library/windowsazure/gg981935.aspx
  
  [Herramientas de línea de comandos de Azure]: ../command-line-tools/
  [servicio en la nube]: ../cloud-services-what-is/
  [CreateServiceOptions]: https://github.com/WindowsAzure/azure-sdk-for-php/blob/master/WindowsAzure/ServiceManagement/Models/CreateServiceOptions.php
  [ListHostedServicesResult]: https://github.com/WindowsAzure/azure-sdk-for-php/blob/master/WindowsAzure/ServiceManagement/Models/GetDeploymentOptions.php
  [paquete de servicio]: http://msdn.microsoft.com/es-es/library/windowsazure/gg433093
  [cmdlets de Azure PowerShell]: ../install-configure-powershell/
  [Herramienta de línea de comandos CSPack]: http://msdn.microsoft.com/es-es/library/windowsazure/gg432988.aspx
  [Esquema de configuración del servicio de Azure (archivo .cscfg)]: http://msdn.microsoft.com/es-es/library/windowsazure/ee758710.aspx
  [Información general sobre la administración de implementaciones en Azure]: http://msdn.microsoft.com/es-es/library/windowsazure/hh386336.aspx
  [servicio de almacenamiento]: ../storage-whatis-account/
  [blobs]: ../storage-php-how-to-use-blobs/
  [tablas]: ../storage-php-how-to-use-table-storage/
  [colas]: ../storage-php-how-to-use-queues/
  [AffinityGroup]: https://github.com/WindowsAzure/azure-sdk-for-php/blob/master/WindowsAzure/ServiceManagement/Models/AffinityGroup.php
