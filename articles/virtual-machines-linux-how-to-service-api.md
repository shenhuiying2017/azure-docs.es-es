<properties urlDisplayName="Service Management API" pageTitle="Uso de la API de administraci&oacute;n de servicios para m&aacute;quinas virtuales - Azure" metaKeywords="" description="Aprenda a utilizar la API de administraci&oacute;n de servicios de Azure para una m&aacute;quina virtual de Linux." metaCanonical="" services="virtual-machines" documentationCenter="" title="Uso de la API de administraci&oacute;n de servicios" authors="timlt" solutions="" manager="timlt" editor="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="timlt" />

# Uso de la API de administración de servicios

## Inicialización

Para invocar la API de administración de servicios IaaS de Azure desde NodeJS, se usa el módulo `azure`.

    var azure = require('azure');

En primer lugar, cree una instancia de ServiceManagementService. Todas las llamadas a la API usarán este objeto. El identificador de la suscripción, las credenciales y otras opciones de conexión se establecen en este momento. Para administrar más de una suscripción, cree más de una instancia de ServiceManagementService.

    var iaasClient = azure.createServiceManagementService(subscriptionid, auth, options);

-   Subscriptionid es una cadena obligatoria, que debe ser el identificador de suscripción de la cuenta a la que se obtiene acceso.
-   Auth es un objeto opcional que especifica la clave privada y el certificado público que se van a usar con esta cuenta.

    -   keyfile: Ruta de acceso del archivo .pem que tiene la clave privada. Ignorar si se ha especificado keyvalue.
    -   keyvalue: Valor real de la clave privada almacenado en un archivo .pem.
    -   certfile: Ruta de acceso del archivo .pem que tiene el certificado público. Ignorar si se ha especificado certvalue.
    -   certvalue: Valor real del certificado público almacenado en un archivo .pem.
    -   Si los valores anteriores no se han especificado, se leen y se utilizan los valores de las variables de entorno `CLIENT_AUTH_KEYFILE` y `CLIENT_AUTH_CERTFILE` del proceso. En caso de no haberse establecido estos, se prueba con los valores predeterminados de los archivos: priv.pem y pub.pem.
    -   Si no es posible cargar la clave privada y el certificado público, se produce un error.
-   Options es un objeto opcional que se puede usar para controlar las propiedades utilizadas por el cliente.

    -   host: Nombre de host del servidor de administración de Azure. Si no se especifica, se usa el host predeterminado.
    -   apiversion: Cadena de versión que se va a usar en el encabezado HTTP. Si no se especifica, se usa la versión predeterminada.
    -   serializetype: Puede ser XML o JSON. Si no se especifica, se usa la serialización predeterminada.

De manera opcional, se puede usar un proxy de túnel para habilitar la solicitud HTTPS de forma que pase por un proxy. Al crear el objeto IaasClient, este procesará la variable de entorno `HTTPS_PROXY`. Si se establece en una dirección URL válida, el nombre de host y el puerto de la dirección URL se analizan y se utilizan en las solicitudes posteriores para identificar el proxy.

        iaasClient.SetProxyUrl(proxyurl)

Puede llamarse a SetProxyUrl para establecer explícitamente el host del proxy y el puerto. Esto tiene el mismo efecto que establecer la variable de entorno `HTTPS_PROXY` e invalidará la configuración del entorno.

## Devoluciones de llamada

Todas las API tienen un argumento de devolución de llamada obligatorio. La finalización de la solicitud se señala mediante la llamada a la función transferida en la devolución de llamada.

    callback(rsp)

-   La devolución de llamada tiene un único parámetro, que es el objeto response.
-   isSuccessful: Puede ser true o false.
-   statusCode: Código de estado HTTP de la respuesta.
-   response: Respuesta analizada en un objeto JavaScript. Se establece si el valor de isSuccessful es true.
-   error: Objeto JavaScript que contiene información de error y se establece si el valor de isSuccessful es false.
-   body: Cuerpo de la respuesta como cadena.
-   headers: Encabezados HTTP de la respuesta.
-   reqopts: Opciones de solicitud HTTP del nodo utilizadas para realizar la solicitud.

Tenga en cuenta que, en algunos casos, la finalización indica simplemente que la solicitud se ha aceptado. En este caso, use **GetOperationStatus** para obtener el estado final.

## API existentes

**iaasClient.GetOperationStatus(requested, callback)**

-   Antes de finalizar la operación se devuelven numerosas llamadas administrativas. Estas devuelven el valor 202 - Aceptado y colocan un valor solicitado en el encabezado HTTP ms-request-id. Para realizar un sondeo sobre la finalización de la solicitud, use esta API y pase el valor solicitado.
-   callback es un valor obligatorio.

**iaasClient.GetOSImage(imagename, callback)**

-   imagename es un nombre de cadena requerido de la imagen.
-   callback es un valor obligatorio.
-   El objeto response contendrá propiedades de la imagen con nombre, si es correcto.

**iaasClient.ListOSImages(callback)**

-   callback es un valor obligatorio.
-   El objeto response contendrá una matriz de objetos de imagen, si es correcto.

**iaasClient.CreateOSImage(imageName, mediaLink, imageOptions, callback)**

-   imageName es un nombre de cadena requerido de la imagen.
-   mediaLink es un nombre de cadena requerido del objeto mediaLink que se va a usar.
-   imageOptions es un objeto opcional que puede contener las siguientes propiedades:

    -   Category
    -   Label: Si no tiene establecido un valor predeterminado, este será imageName.
    -   Location
    -   RoleSize
-   callback es un valor obligatorio. (Si imageOptions no se ha establecido, este puede ser el tercer parámetro).
-   El objeto response contendrá propiedades de la imagen creada, si es correcto.

**iaasClient.ListHostedServices(callback)**

-   callback es un valor obligatorio.
-   El objeto response contendrá una matriz de objetos de servicio hospedado, si es correcto.

**iaasClient.GetHostedService(serviceName, callback)**

-   serviceName es un nombre de cadena requerido del servicio hospedado.
-   callback es un valor obligatorio.
-   El objeto response contendrá propiedades del servicio hospedado, si es correcto.

**iaasClient.CreateHostedService(serviceName, serviceOptions, callback)**

-   serviceName es un nombre de cadena requerido del servicio hospedado.
-   serviceOptions es un objeto opcional que puede contener las siguientes propiedades:

    -   Description: Tiene como valor predeterminado el host de servicio.
    -   Label: Si no tiene establecido un valor predeterminado, este será serviceName.
    -   Ubicación: La región en la que se creará el servicio
-   callback es un valor obligatorio.

**iaasClient.GetStorageAccountKeys(serviceName, callback)**

-   serviceName es un nombre de cadena requerido del servicio hospedado.
-   callback es un valor obligatorio.
-   El objeto response contendrá las claves de acceso de almacenamiento, si es correcto.

**iaasClient.GetDeployment(serviceName, deploymentName, callback)**

-   serviceName es un nombre de cadena requerido del servicio hospedado.
-   deploymentName es un nombre de cadena requerido de la implementación.
-   callback es un valor obligatorio.
-   El objeto response contendrá propiedades de la implementación con nombre, si es correcto.

**iaasClient.GetDeploymentBySlot(serviceName, deploymentSlot, callback)**

-   serviceName es un nombre de cadena requerido del servicio hospedado.
-   deploymentSlot es un nombre de cadena requerido de la ranura ("Ensayo" o "Producción").
-   callback es un valor obligatorio.
-   El objeto response contendrá propiedades de las implementaciones de la ranura, si es correcto.

**iaasClient.CreateDeployment(serviceName, deploymentName, VMRole, deployOptions, callback)**

-   serviceName es un nombre de cadena requerido del servicio hospedado.
-   deploymentName es un nombre de cadena requerido de la implementación.
-   VMRole es un objeto obligatorio que incluye las propiedades del rol que se va a crear para la implementación.
-   deployOptions es un objeto opcional que puede contener las siguientes propiedades:

    -   DeploymentSlot: Tiene "Producción" como valor predeterminado.
    -   Label: Si no tiene establecido un valor predeterminado, este será deploymentName.
    -   UpgradeDomainCount: No tiene un valor predeterminado.
-   callback es un valor obligatorio.

**iaasClient.GetRole(serviceName, deploymentName, roleName, callback)**

-   serviceName es un nombre de cadena requerido del servicio hospedado.
-   deploymentName es un nombre de cadena requerido de la implementación.
-   roleName es un nombre de cadena requerido del rol.
-   callback es un valor obligatorio.
-   El objeto response contendrá propiedades del rol con nombre, si es correcto.

**iaasClient.AddRole(serviceName, deploymentName, VMRole, callback)**

-   serviceName es un nombre de cadena requerido del servicio hospedado.
-   deploymentName es un nombre de cadena requerido de la implementación.
-   VMRole es un objeto obligatorio que incluye las propiedades del rol que se va a agregar a la implementación.
-   callback es un valor obligatorio.

**iaasClient.ModifyRole(serviceName, deploymentName, roleName, VMRole, callback)**

-   serviceName es un nombre de cadena requerido del servicio hospedado.
-   deploymentName es un nombre de cadena requerido de la implementación.
-   roleName es un nombre de cadena requerido del rol.
-   VMRole es un objeto obligatorio que incluye las propiedades que se van a modificar en el rol.
-   callback es un valor obligatorio.

**iaasClient.DeleteRole(serviceName, deploymentName, roleName, callback)**

-   serviceName es un nombre de cadena requerido del servicio hospedado.
-   deploymentName es un nombre de cadena requerido de la implementación.
-   roleName es un nombre de cadena requerido del rol.
-   callback es un valor obligatorio.

**iaasClient.AddDataDisk(serviceName, deploymentName, roleName, datadisk, callback)**

-   serviceName es un nombre de cadena requerido del servicio hospedado.
-   deploymentName es un nombre de cadena requerido de la implementación.
-   roleName es un nombre de cadena requerido del rol.
-   Datadisk es un objeto obligatorio que se usa para especificar cómo se creará el disco de datos.
-   callback es un valor obligatorio.

**iaasClient.ModifyDataDisk(serviceName, deploymentName, roleName, LUN, datadisk, callback)**

-   serviceName es un nombre de cadena requerido del servicio hospedado.
-   deploymentName es un nombre de cadena requerido de la implementación.
-   roleName es un nombre de cadena requerido del rol.
-   LUN es el número que identifica el disco de datos.
-   Datadisk es un objeto obligatorio que se usa para especificar cómo se modificará el disco de datos.
-   callback es un valor obligatorio.

**iaasClient.RemoveDataDisk(serviceName, deploymentName, roleName, LUN, callback)**

-   serviceName es un nombre de cadena requerido del servicio hospedado.
-   deploymentName es un nombre de cadena requerido de la implementación.
-   roleName es un nombre de cadena requerido del rol.
-   LUN es el número que identifica el disco de datos.
-   callback es un valor obligatorio.

**iaasClient.ShutdownRoleInstance(serviceName, deploymentName, roleInstance, callback)**

-   serviceName es un nombre de cadena requerido del servicio hospedado.
-   deploymentName es un nombre de cadena requerido de la implementación.
-   roleInstance es un nombre de cadena requerido de la instancia del rol.
-   callback es un valor obligatorio.

**iaasClient.RestartRoleInstance(serviceName, deploymentName, roleInstance, callback)**

-   serviceName es un nombre de cadena requerido del servicio hospedado.
-   deploymentName es un nombre de cadena requerido de la implementación.
-   roleInstance es un nombre de cadena requerido de la instancia del rol.
-   callback es un valor obligatorio.

**iaasClient.CaptureRoleInstance(serviceName, deploymentName, roleInstance, captOptions, callback)**

-   serviceName es un nombre de cadena requerido del servicio hospedado.
-   deploymentName es un nombre de cadena requerido de la implementación.
-   roleInstance es un nombre de cadena requerido de la instancia del rol.
-   captOptions es un objeto obligatorio que define las acciones de captura:

    -   PostCaptureActions
    -   ProvisioningConfiguration
    -   SupportsStatelessDeployment
    -   TargetImageLabel
    -   TargetImageName
-   callback es un valor obligatorio.

## Objetos de datos

Las API toman objetos como entrada al crear o modificar una implementación, un rol o un disco. Otras API devolverán objetos similares en una operación Get o List.
En esta sección se describen brevemente las propiedades de los objetos.
Implementación

-   Name: Cadena
-   DeploymentSlot: "Ensayo" o "Producción"
-   Status: Cadena, solo salida
-   PrivateID: Cadena, solo salida
-   Label: Cadena
-   UpgradeDomainCount: Número
-   SdkVersion: Cadena
-   Locked: True o false
-   RollbackAllowed: True o false
-   RoleInstance: Objeto, solo salida
-   Role: Objeto VMRole
-   InputEndpointList: Matriz de InputEndpoint

**VMRole**

-   RoleName: Cadena, necesaria para crear.
-   RoleSize - Cadena, opcional para crear.
-   RoleType: Cadena, su valor predeterminado es "PersistentVMRole" si no se ha especificado durante la creación.
-   OSDisk: Objeto, necesaria para crear.
-   DataDisks: Matriz de objetos, opcional para crear.
-   ConfigurationSets: Matriz de objetos Configuration.

**RoleInstance**

-   RoleName: Cadena
-   InstanceName: Cadena
-   InstanceStatus: Cadena
-   InstanceUpgradeDomain: Número
-   InstanceFaultDomain: Número
-   InstanceSize: Cadena
-   IpAddress: Cadena

**OSDisk**

-   SourceImageName: Cadena, necesaria para crear.
-   DisableWriteCache: True o false
-   DiskName: Cadena
-   MediaLink: Cadena

**DataDisk**

-   DisableReadCache: True o false
-   EnableWriteCache: True o false
-   DiskName: Cadena
-   MediaLink: Cadena
-   LUN: Número (de 0 a 15)
-   LogicalDiskSizeInGB: Número
-   SourceMediaLink: Cadena
-   Existen tres formas de especificar el disco durante la creación: por nombre, por soporte multimedia o por tamaño. Las opciones especificadas determinarán su funcionamiento. Consulte la documentación de la API de RDFE para obtener información detallada.

**ProvisioningConfiguration**

-   ConfigurationSetType: "ProvisioningConfiguration"
-   AdminPassword: Cadena
-   MachineName: Cadena
-   ResetPasswordOnFirstLogon: True o false

**NetworkConfiguration**

-   ConfigurationSetType: "NetworkConfiguration"
-   InputEndpoints: Matriz de ExternalEndpoints

**InputEndpoint**

-   RoleName
-   Vip
-   Port

**ExternalEndpoint**

-   LocalPort
-   Nombre
-   Port
-   Protocol

## Código de ejemplo

A continuación se muestra código JavaScript de ejemplo que crea un servicio hospedado y una implementación y, a continuación, sondea el estado de finalización de la implementación.

    var azure = require('azure');

    // specify where certificate files are located
    var auth = {
      keyfile : '../certs/priv.pem',
      certfile : '../certs/pub.pem'
    }

    // names and IDs for subscription, service, deployment
    var subscriptionId = '167a0c69-cb6f-4522-ba3e-d3bdc9c504e1';
    var serviceName = 'sampleService2';
    var deploymentName = 'sampleDeployment';

    // poll for completion every 10 seconds
    var pollPeriod = 10000;

    // data used to define deployment and role

    var deploymentOptions = {
      DeploymentSlot: 'Staging',
      Label: 'Deployment Label'
    }

    var osDisk = {
      SourceImageName : 'Win2K8SP1.110809-2000.201108-01.en.us.30GB.vhd',
    };

    var dataDisk1 = {
      LogicalDiskSizeInGB : 10,
      LUN : '0'
    };

    var provisioningConfigurationSet = {
      ConfigurationSetType: 'ProvisioningConfiguration',
      AdminPassword: 'myAdminPwd1',
      MachineName: 'sampleMach1',
      ResetPasswordOnFirstLogon: false
    };

    var externalEndpoint1 = {
      Name: 'endpname1',
      Protocol: 'tcp',
      Port: '59919',
      LocalPort: '3395'
    };

    var networkConfigurationSet = {
      ConfigurationSetType: 'NetworkConfiguration',
      InputEndpoints: [externalEndpoint1]
    };

    var VMRole = {
      RoleName: 'sampleRole',
      RoleSize: 'Small',
      OSDisk: osDisk,
      DataDisks: [dataDisk1],
      ConfigurationSets: [provisioningConfigurationSet, networkConfigurationSet]
    }


    // function to show error messages if failed
    function showErrorResponse(rsp) {
      console.log('There was an error response from the service');
      console.log('status code=' + rsp.statusCode);
      console.log('Error Code=' + rsp.error.Code);
      console.log('Error Message=' + rsp.error.Message);
    }

    // polling for completion
    function PollComplete(reqid) {
      iaasCli.GetOperationStatus(reqid, function(rspobj) {
        if (rspobj.isSuccessful && rspobj.response) {
          var rsp = rspobj.response;
          if (rsp.Status == 'InProgress') {
            setTimeout(PollComplete(reqid), pollPeriod);
            process.stdout.write('.');
          } else {
            console.log(rsp.Status);
            if (rsp.HttpStatusCode) console.log('HTTP Status: ' + rsp.HttpStatusCode);
            if (rsp.Error) {      
              console.log('Error code: ' + rsp.Error.Code);
              console.log('Error Message: ' + rsp.Error.Message);
            }
          }
        } else {
          showErrorResponse(rspobj);
        }
      });
    }


    // create the client object
    var iaasCli = azure.createServiceManagementService(subscriptionId, auth);

    // create a hosted service.
    // if successful, create deployment
    // if accepted, poll for completion
    iaasCli.CreateHostedService(serviceName, function(rspobj) {
      if (rspobj.isSuccessful) {
        iaasCli.CreateDeployment(serviceName, 
                                 deploymentName,
                                 VMRole, deploymentOptions,
                                  function(rspobj) {
          if (rspobj.isSuccessful) {
          // get request id, and start polling for completion
            var reqid = rspobj.headers['x-ms-request-id'];
            process.stdout.write('Polling');
            setTimeout(PollComplete(reqid), pollPeriod);
          } else {
            showErrorResponse(rspobj);
          }
        });
      } else {
        showErrorResponse(rspobj);
      }
    });
