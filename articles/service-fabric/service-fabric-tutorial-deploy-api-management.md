---
title: "Integración de Azure Service Fabric con API Management | Microsoft Docs"
description: "Obtenga información sobre cómo empezar a usar rápidamente Azure API Management y Service Fabric."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/10/2017
ms.author: ryanwi
ms.openlocfilehash: 97bcf312621ec0fed28e26179d4c4aa101a8a92d
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/15/2017
---
# <a name="deploy-api-management-with-service-fabric"></a>Implementación de API Management con Service Fabric
Este tutorial es la tercera parte de una serie.  La implementación de Azure API Management con Service Fabric es un escenario avanzado.  API Management es útil cuando es necesario publicar API con un completo conjunto de reglas de enrutamiento para los servicios de Service Fabric de back-end. Las aplicaciones en la nube normalmente necesitan una puerta de enlace front-end para proporcionar un único punto de entrada para usuarios, dispositivos u otras aplicaciones. En Service Fabric, una puerta de enlace puede ser cualquier servicio sin estado diseñado para la entrada de tráfico, como una aplicación ASP.NET Core, Event Hubs, IoT Hub o Azure API Management. 

En este tutorial se muestra cómo configurar [Azure API Management](../api-management/api-management-key-concepts.md) con Service Fabric para enrutar el tráfico a un servicio back-end de Service Fabric.  Cuando haya terminado, habrá implementado API Management en una red virtual y configurado una operación de API para enviar tráfico a servicios sin estado de back-end. Para más información sobre escenarios de Azure API Management con Service Fabric, consulte el artículo de [introducción](service-fabric-api-management-overview.md).

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Implementación de API Management
> * Configuración de API Management
> * Creación de una operación de API
> * Configuración de una directiva de back-end
> * Incorporación de la API a un producto

En esta serie de tutoriales, se aprende a:
> [!div class="checklist"]
> * Cree un [clúster de Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) o [clúster de Linux](service-fabric-tutorial-create-vnet-and-linux-cluster.md) en Azure mediante una plantilla
> * [Escalado o reducción horizontal](/service-fabric-tutorial-scale-cluster.md)
> * Implementación de API Management con Service Fabric

## <a name="prerequisites"></a>Requisitos previos
Antes de empezar este tutorial:
- Si no tiene ninguna suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Instale la [versión 4.1 o superior del módulo de Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) o la [CLI de Azure 2.0](/cli/azure/install-azure-cli).
- Creación de un [clúster de Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) o [clúster de Linux](service-fabric-tutorial-create-vnet-and-linux-cluster.md) en Azure
- Si implementa un clúster de Windows, configure un entorno de desarrollo de Windows. Instale [Visual Studio 2017](http://www.visualstudio.com) y las cargas de trabajo de **desarrollo Azure**, **desarrollo web y ASP.NET**, y **desarrollo a través de plataformas .NET Core**.  Después, configure un [entorno de desarrollo .NET](service-fabric-get-started.md).
- Si implementa un clúster Linux, configure un entorno de desarrollo Java en [Linux](service-fabric-get-started-linux.md) o [MacOS](service-fabric-get-started-mac.md).  Instale la [CLI de Service Fabric](service-fabric-cli.md). 

## <a name="network-topology"></a>Topología de red
Ahora que tiene un [clúster de Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) o [clúster de Linux](service-fabric-tutorial-create-vnet-and-linux-cluster.md) en Azure, implemente API Management en la red virtual (VNET), en la subred y en el grupo de seguridad de red diseñados para API Management. Para este tutorial, la plantilla de Resource Manager para API Management está preconfigurada para los nombres de la red virtual, la subred y el grupo de seguridad de red que configuró en el anterior [tutorial de clúster de Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) o [tutorial de clúster de Linux](service-fabric-tutorial-create-vnet-and-linux-cluster.md). En este tutorial se implementa la topología siguiente en Azure, donde API Management y Service Fabric se encuentran en subredes de la misma red virtual:

 ![Leyenda de la imagen][sf-apim-topology-overview]

## <a name="sign-in-to-azure-and-select-your-subscription"></a>Inicio de sesión en Azure y selección de la suscripción
Inicie sesión en su cuenta de Azure y seleccione su suscripción antes de ejecutar comandos de Azure.

```powershell
Login-AzureRmAccount
Get-AzureRmSubscription
Set-AzureRmContext -SubscriptionId <guid>
```

```azurecli
az login
az account set --subscription <guid>
```

## <a name="deploy-a-service-fabric-back-end-service"></a>Implementación de un servicio de back-end de Service Fabric

Antes de configurar API Management para enrutar el tráfico a un servicio back-end de Service Fabric, primero necesita un servicio en ejecución que acepte solicitudes.  Si ha creado anteriormente un [clúster Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md), implemente un servicio .NET Service Fabric.  Si ha creado anteriormente un [clúster Linux](service-fabric-tutorial-create-vnet-and-linux-cluster.md), implemente un servicio Java Service Fabric.

### <a name="deploy-a-net-service-fabric-service"></a>Implementación de un servicio .NET Service Fabric

En este tutorial, se creará un servicio básico confiable de ASP.NET Core sin estado con la plantilla de proyecto de Web API predeterminada. Así se crea un punto de conexión HTTP para el servicio que se expone en Azure API Management.

Inicie Visual Studio como administrador y cree un servicio ASP.NET Core:

 1. En Visual Studio, seleccione Archivo -> Nuevo proyecto.
 2. En Nube, seleccione la plantilla de aplicación de Service Fabric y asígnele el nombre **"ApiApplication"**.
 3. Seleccione la plantilla de servicio de ASP.NET Core y denomine el proyecto **"WebApiService"**.
 4. Seleccione la plantilla de proyecto de ASP.NET Core 1.1 de API web.
 5. Una vez creado el proyecto, abra `PackageRoot\ServiceManifest.xml` y quite los atributos `Port` de la configuración del recurso de punto de conexión:
 
    ```xml
    <Resources>
      <Endpoints>
        <Endpoint Protocol="http" Name="ServiceEndpoint" Type="Input" />
      </Endpoints>
    </Resources>
    ```

    Al quitar el puerto, Service Fabric puede especificar un puerto dinámicamente del intervalo de puertos de la aplicación, abierta a través del grupo de seguridad de red en la plantilla del clúster de Resource Manager, que permite que el tráfico fluya a él desde API Management.
 
 6. Presione F5 en Visual Studio para comprobar que la API web está disponible de manera local. 

    Abra Service Fabric Explorer y vaya a una instancia específica del servicio ASP.NET Core para ver la dirección base donde escucha el servicio. Agregue `/api/values` a la dirección base y ábralo en un explorador. Esto invoca el método Get en ValuesController en la plantilla de Web API. devuelve la respuesta predeterminada de la plantilla, una matriz JSON con dos cadenas:

    ```json
    ["value1", "value2"]`
    ```

    Este es el punto de conexión que debe exponer a través de API Management en Azure.

 7. Por último, implemente la aplicación en el clúster en Azure. In Visual Studio, haga clic con el botón derecho en el proyecto de la aplicación y seleccione **Publicar**. Proporcione el punto de conexión del clúster (por ejemplo, `mycluster.southcentralus.cloudapp.azure.com:19000`) para implementar la aplicación en el clúster de Service Fabric en Azure.

En el clúster de Service Fabric en Azure ahora se ejecutará un servicio sin estado ASP.NET Core denominado `fabric:/ApiApplication/WebApiService`.

### <a name="create-a-java-service-fabric-service"></a>Creación de un servicio Java Service Fabric
En este tutorial, se implementará un servidor web básico que devuelve mensajes al usuario. Esta aplicación de ejemplo de servidor de eco contiene un punto de conexión HTTP para el servicio que se expone en Azure API Management.

1. Clone los ejemplos de introducción a Java.

   ```bash
   git clone https://github.com/Azure-Samples/service-fabric-java-getting-started.git
   cd service-fabric-java-getting-started
   ```

2. Edite *Services/EchoServer/EchoServer1.0/EchoServerApplication/EchoServerPkg/ServiceManifest.xml*. Actualice el punto de conexión para que el servicio atienda en el puerto 8081.

   ```xml
   <Endpoint Name="WebEndpoint" Protocol="http" Port="8081" />
   ```

3. Guarde *ServiceManifest.xml*; a continuación, compile la aplicación EchoServer1.0.

   ```bash
   cd Services/EchoServer/EchoServer1.0/
   gradle
   ```

4. Implemente la aplicación en el clúster.

   ```bash
   cd Scripts
   sfctl cluster select --endpoint http://mycluster.southcentralus.cloudapp.azure.com:19080
   ./install.sh
   ```

   Ahora se ejecutará un servicio sin estado Java denominado `fabric:/EchoServerApplication/EchoServerService` en el clúster Service Fabric en Azure.

5. Abra un explorador y escriba http://mycluster.southcentralus.cloudapp.azure.com:8081/getMessage; debería ver "[version 1.0]Hello World !!!" en la pantalla.

## <a name="download-and-understand-the-resource-manager-template"></a>Descarga e información de la plantilla de Resource Manager
Descargue y guarde la plantilla de Resource Manager y el archivo de parámetros siguientes:
 
- [apim.json][apim-arm]
- [apim.parameters.json][apim-parameters-arm]

En las siguientes secciones se describen los recursos que se definen mediante la plantilla *apim.json*. Para más información, siga los vínculos a la documentación de referencia de plantilla dentro de cada sección. Los parámetros configurables que se definen en el archivo de parámetros *apim.parameters.json* se establecen más adelante en este artículo.

### <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service
[Microsoft.ApiManagement/service](/azure/templates/microsoft.apimanagement/service) describe la instancia de servicio de API Management: nombre, SKU o nivel, ubicación del grupo de recursos, información del editor y red virtual.

### <a name="microsoftapimanagementservicecertificates"></a>Microsoft.ApiManagement/service/certificates
[Microsoft.ApiManagement/service/certificates](/azure/templates/microsoft.apimanagement/service/certificates) configura la seguridad de API Management. API Management debe autenticarse con el clúster de Service Fabric para la detección de servicios mediante un certificado de cliente con acceso al clúster. En este tutorial se usa el mismo certificado que se especificó anteriormente al crear el [clúster de Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md#createvaultandcert_anchor) o [clúster de Linux](service-fabric-tutorial-create-vnet-and-linux-cluster.md#createvaultandcert_anchor), que, de forma predeterminada, se puede usar para tener acceso al clúster. 

En este tutorial se usa el mismo certificado para la autenticación de cliente y la seguridad de nodo a nodo del clúster. Puede utilizar otro certificado de cliente si tiene uno configurado para acceder al clúster de Service Fabric. Rellene los campos **name**, **password** y **data** (cadena codificada en base-64) del archivo de clave privada (.pfx) del certificado del clúster que especificó al crear el clúster de Service Fabric.

### <a name="microsoftapimanagementservicebackends"></a>Microsoft.ApiManagement/service/backends
[Microsoft.ApiManagement/service/backends](/azure/templates/microsoft.apimanagement/service/backends) describe el servicio  back-end al que se reenvía el tráfico. 

En los servidores back-end de Service Fabric, su clúster sirve de back-end, en lugar de usar un servicio de Service Fabric específico. Esto permite que una única directiva enrute a más de un servicio del clúster. Aquí el campo **url** es el nombre completo de un servicio del clúster al que se enrutan todas las solicitudes de forma predeterminada si no se especifica ningún nombre de servicio en una directiva de back-end. Puede usar un nombre de servicio falso, como "fabric:/fake/service" si no desea un servicio de reserva. **resourceId** especifica el punto de conexión de administración del clúster.  **clientCertificateThumbprint** y **serverCertificateThumbprints** identifican los certificados usados para autenticarse con el clúster.

### <a name="microsoftapimanagementserviceproducts"></a>Microsoft.ApiManagement/service/products
[Microsoft.ApiManagement/service/products](/azure/templates/microsoft.apimanagement/service/products) crea un producto. En Azure API Management, un producto contiene una o varias API, así como una cuota de uso y los términos de uso. Una vez publicado un producto, los desarrolladores pueden suscribirse al producto y comenzar a usar las API del producto. 

Rellene el campo **displayName** con un valor descriptivo y el campo **description** del producto. En este tutorial, se necesita una suscripción pero no su aprobación por un administrador.  El valor de **state** de este producto es "publicado" y es visible para los suscriptores. 

### <a name="microsoftapimanagementserviceapis"></a>Microsoft.ApiManagement/service/apis
[Microsoft.ApiManagement/service/apis](/azure/templates/microsoft.apimanagement/service/apis) crea una API. En Administración de API, una API representa un conjunto de operaciones que puede invocarse por las aplicaciones cliente. Una vez agregadas las operaciones, la API se agrega a un producto y se puede publicar. Una vez publicada una API, pueden usarla y suscribirse a ella los desarrolladores.

- **displayName** puede ser cualquier nombre para la API. Use "Service Fabric App" en este tutorial.
- **name** proporciona un nombre único y descriptivo para la API, como "aplicación de service fabric". Se muestra en el portal para desarrolladores y en el portal del publicador. 
- **serviceUrl** hace referencia al servicio HTTP que implementa la API. Administración de API envía las solicitudes a esta dirección. En servidores back-end de Service Fabric, este valor de dirección URL no se usa. Puede escribir aquí cualquier valor. Por ejemplo, en este tutorial, "http://servicefabric". 
- **path** se anexa a la dirección URL base del servicio API Management. La dirección URL base es común para todas las API hospedadas en una instancia del servicio Administración de API. Administración de API distingue las API por su sufijo, por lo que el sufijo debe ser único para cada API de un publicador determinado. 
- El campo **protocols** determina los protocolos que se pueden usar para acceder a la API. En este tutorial, se muestran **http** y **https**.
- **path** es un sufijo de la API. Use "myapp" en este tutorial.

### <a name="microsoftapimanagementserviceapisoperations"></a>Microsoft.ApiManagement/service/apis/operations
[Microsoft.ApiManagement/service/apis/operations](/azure/templates/microsoft.apimanagement/service/apis/operations): antes de usar una API de API Management, se deben agregar operaciones a la API.  Los clientes externos usan una operación para comunicarse con el servicio sin estado de ASP.NET Core que se ejecuta en el clúster de Service Fabric.

Para agregar una operación de API de front-end, rellene los valores:

- **displayName** y **description** describen la operación. Use "Values" en este tutorial.
- **method** especifica el verbo HTTP.  En este tutorial, especifique **GET**.
- **urlTemplate** se anexa a la dirección URL base de la API e identifica una única operación HTTP.  En este tutorial, use `/api/values` si agregó el servicio back-end de .NET o `getMessage` si agregó el servicio back-end de Java.  De forma predeterminada, la ruta de acceso URL que se especifica aquí se envía al servicio de back-end de Service Fabric. Si usa aquí la misma ruta de acceso URL que usa el servicio, como "/api/values", la operación funciona sin más modificaciones. También puede especificar aquí una ruta de acceso URL distinta de la que usa el servicio de back-end de Service Fabric, en cuyo caso también debe especificar después una ruta de acceso de reescritura en la directiva de la operación.

### <a name="microsoftapimanagementserviceapispolicies"></a>Microsoft.ApiManagement/service/apis/policies
[Microsoft.ApiManagement/service/apis/policies](/azure/templates/microsoft.apimanagement/service/apis/policies) crea una directiva de back-end, que vincula todos los elementos. Es donde se configura el servicio de back-end de Service Fabric al que se enrutan las solicitudes. Puede aplicar esta directiva a cualquier operación de API.  Para más información, consulte [Introducción a las directivas](/azure/api-management/api-management-howto-policies). 

La [configuración de back-end de Service Fabric](/azure/api-management/api-management-transformation-policies#SetBackendService) proporciona los controles de enrutamiento de solicitudes siguientes: 
 - Selección de instancias de servicio mediante la especificación de un nombre de instancia de servicio de Service Fabric, ya sea codificado de forma rígida (por ejemplo, `"fabric:/myapp/myservice"`) o generado a partir de la solicitud HTTP (por ejemplo, `"fabric:/myapp/users/" + context.Request.MatchedParameters["name"]`).
 - Resolución de la partición mediante la generación de una clave de partición a partir de cualquier esquema de partición de Service Fabric.
 - Selección de réplicas para los servicios con estado.
 - Condiciones de reintento de resolución que permiten especificar las condiciones para volver a resolver una ubicación de servicio y volver a enviar una solicitud.

**policyContent** es el contenido XML con escape JSON de la directiva.  En este tutorial, cree una directiva de back-end que enrute las solicitudes directamente al servicio sin estado .NET o Java implementado anteriormente. Agregue una directiva `set-backend-service` bajo las directivas de entrada.  Reemplace "service-name" por `fabric:/ApiApplication/WebApiService` si anteriormente implementó el servicio back-end .NET, o `fabric:/EchoServerApplication/EchoServerService` si implementó el servicio Java.
    
```xml
<policies>
  <inbound>
    <base/>
    <set-backend-service 
        backend-id="servicefabric"
        sf-service-instance-name="service-name"
        sf-resolve-condition="@((int)context.Response.StatusCode != 200)" />
  </inbound>
  <backend>
    <base/>
  </backend>
  <outbound>
    <base/>
  </outbound>
</policies>
```

Para obtener un conjunto completo de los atributos de directiva de back-end de Service Fabric, consulte la [documentación de back-end de API Management](https://docs.microsoft.com/azure/api-management/api-management-transformation-policies#SetBackendService).

## <a name="set-parameters-and-deploy-api-management"></a>Definición de los parámetros e implementación de API Management
Rellene los siguientes parámetros vacíos en *apim.parameters.json* para la implementación. 

|Parámetro|Valor|
|---|---|
|apimInstanceName|sf-apim|
|apimPublisherEmail|myemail@contosos.com|
|apimSku|Developer|
|serviceFabricCertificateName|sfclustertutorialgroup320171031144217|
|certificatePassword|q6D7nN%6ck@6| 
|serviceFabricCertificateThumbprint|C4C1E541AD512B8065280292A8BA6079C3F26F10 |
|serviceFabricCertificate|&lt;cadena codificada en base 64&gt;|
|url_path|/api/values|
|clusterHttpManagementEndpoint|https://mysfcluster.southcentralus.cloudapp.azure.com:19080|
|inbound_policy|&lt;Cadena XML&gt;|

*certificatePassword* y *serviceFabricCertificateThumbprint* deben coincidir con el certificado usado para configurar el clúster.  

*serviceFabricCertificate* es el certificado como una cadena codificada en base 64, que se puede generar mediante el siguiente script:

```powershell
$bytes = [System.IO.File]::ReadAllBytes("C:\mycertificates\sfclustertutorialgroup220171109113527.pfx");
$b64 = [System.Convert]::ToBase64String($bytes);
[System.Io.File]::WriteAllText("C:\mycertificates\sfclustertutorialgroup220171109113527.txt", $b64);
```

En *inbound_policy*, reemplace "service-name" por `fabric:/ApiApplication/WebApiService` si anteriormente implementó el servicio back-end .NET, o `fabric:/EchoServerApplication/EchoServerService` si implementó el servicio Java.

```xml
<policies>
  <inbound>
    <base/>
    <set-backend-service 
        backend-id="servicefabric"
        sf-service-instance-name="service-name"
        sf-resolve-condition="@((int)context.Response.StatusCode != 200)" />
  </inbound>
  <backend>
    <base/>
  </backend>
  <outbound>
    <base/>
  </outbound>
</policies>
```

Use el siguiente script para implementar la plantilla de Resource Manager y los archivos de parámetros para API Management:

```powershell
$ResourceGroupName = "sfclustertutorialgroup"
New-AzureRmResourceGroupDeployment -ResourceGroupName $ResourceGroupName -TemplateFile .\apim.json -TemplateParameterFile .\apim.parameters.json -Verbose
```

```azurecli
ResourceGroupName="sfclustertutorialgroup"
az group deployment create --name ApiMgmtDeployment --resource-group $ResourceGroupName --template-file apim.json --parameters @apim.parameters.json 
```

## <a name="test-it"></a>Pruébelo.

Ahora puede intentar enviar una solicitud al servicio back-end de Service Fabric a través de API Management directamente desde [Azure Portal](https://portal.azure.com).

 1. En el servicio API Management, seleccione **API**.
 2. En la API de **Service Fabric App** que creó en los pasos anteriores, seleccione la pestaña **Prueba** y, a continuación, la operación **Values**.
 3. Haga clic en el botón **Enviar** para enviar una solicitud de prueba al servicio de back-end.  La respuesta HTTP debe ser similar a la siguiente:

    ```http
    HTTP/1.1 200 OK

    Transfer-Encoding: chunked

    Content-Type: application/json; charset=utf-8

    Vary: Origin

    Access-Control-Allow-Origin: https://apimanagement.hosting.portal.azure.net

    Access-Control-Allow-Credentials: true

    Access-Control-Expose-Headers: Transfer-Encoding,Date,Server,Vary,Ocp-Apim-Trace-Location

    Ocp-Apim-Trace-Location: https://apimgmtstuvyx3wa3oqhdbwy.blob.core.windows.net/apiinspectorcontainer/RaVVuJBQ9yxtdyH55BAsjQ2-1?sv=2015-07-08&sr=b&sig=Ab6dPyLpTGAU6TdmlEVu32DMfdCXTiKAASUlwSq3jcY%3D&se=2017-09-15T05%3A49%3A53Z&sp=r&traceId=ed9f1f4332e34883a774c34aa899b832

    Date: Thu, 14 Sep 2017 05:49:56 GMT


    [
    "value1",
    "value2"
    ]
    ```

## <a name="clean-up-resources"></a>Limpieza de recursos

Un clúster está formado por muchos otros recursos de Azure, además del propio recurso del clúster. La manera más sencilla de eliminar el clúster y todos los recursos que consume es eliminar el grupo de recursos.

Inicie sesión en Azure y seleccione el identificador de suscripción con el que quiere quitar el clúster.  Para encontrar el identificador de suscripción, inicie sesión en [Azure Portal](http://portal.azure.com). Elimine el grupo de recursos y todos los recursos de clúster mediante el cmdlet [Remove-AzureRMResourceGroup](/en-us/powershell/module/azurerm.resources/remove-azurermresourcegroup).

```powershell
$ResourceGroupName = "sfclustertutorialgroup"
Remove-AzureRmResourceGroup -Name $ResourceGroupName -Force
```

```azurecli
ResourceGroupName="sfclustertutorialgroup"
az group delete --name $ResourceGroupName
```

## <a name="conclusion"></a>Conclusión
En este tutorial, ha aprendido cómo:

> [!div class="checklist"]
> * Implementación de API Management
> * Configuración de API Management
> * Creación de una operación de API
> * Configuración de una directiva de back-end
> * Incorporación de la API a un producto

[azure-powershell]: https://azure.microsoft.com/documentation/articles/powershell-install-configure/

[apim-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/apim.json
[apim-parameters-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/apim.parameters.json

[network-arm]: https://github.com/Azure-Samples/service-fabric-api-management/blob/master/network.json
[network-parameters-arm]: https://github.com/Azure-Samples/service-fabric-api-management/blob/master/network.parameters.json

[cluster-arm]: https://github.com/Azure-Samples/service-fabric-api-management/blob/master/cluster.json
[cluster-parameters-arm]: https://github.com/Azure-Samples/service-fabric-api-management/blob/master/cluster.parameters.json

<!-- pics -->
[sf-apim-topology-overview]: ./media/service-fabric-tutorial-deploy-api-management/sf-apim-topology-overview.png
