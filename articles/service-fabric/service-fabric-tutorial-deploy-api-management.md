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
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/13/2017
ms.author: ryanwi
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 1cadb543660b636ce7f0285973e6fb4141841b99
ms.contentlocale: es-es
ms.lasthandoff: 09/25/2017

---

# <a name="deploy-api-management-with-service-fabric"></a>Implementación de API Management con Service Fabric
Este tutorial es la segunda parte de una serie. En este tutorial se muestra cómo configurar [Azure API Management](../api-management/api-management-key-concepts.md) con Service Fabric para enrutar el tráfico a un servicio back-end de Service Fabric.  Cuando haya terminado, habrá implementado API Management en una red virtual y configurado una operación de API para enviar tráfico a servicios sin estado de back-end. Para más información sobre escenarios de Azure API Management con Service Fabric, consulte el artículo de [introducción](service-fabric-api-management-overview.md).

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
> * Implementación de API Management con Service Fabric

## <a name="prerequisites"></a>Requisitos previos
Antes de empezar este tutorial:
- Si no tiene ninguna suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Instale la [versión 4.1 o superior del módulo de Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) o la [CLI de Azure 2.0](/cli/azure/install-azure-cli).
- Creación de un [clúster de Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) o [clúster de Linux](service-fabric-tutorial-create-vnet-and-linux-cluster.md) en Azure

## <a name="sign-in-to-azure-and-select-your-subscription"></a>Inicio de sesión en Azure y selección de la suscripción
En este tutorial se usa [Azure PowerShell][azure-powershell]. Al iniciar una nueva sesión de PowerShell, inicie sesión en su cuenta de Azure y seleccione su suscripción antes de ejecutar comandos de Azure.
 
Inicie sesión en la cuenta de Azure y seleccione su suscripción:

```powershell
Login-AzureRmAccount
Get-AzureRmSubscription
Set-AzureRmContext -SubscriptionId <guid>
```

```azurecli
az login
az account set --subscription <guid>
```

## <a name="deploy-api-management"></a>Implementación de API Management
Las aplicaciones en la nube normalmente necesitan una puerta de enlace front-end para proporcionar un único punto de entrada para usuarios, dispositivos u otras aplicaciones. En Service Fabric, una puerta de enlace puede ser cualquier servicio sin estado como una aplicación ASP.NET Core u otro servicio designado para la entrada de tráfico, como Event Hubs, IoT Hub o Azure API Management. Este tutorial es una introducción al uso de Azure API Management como puerta de enlace para las aplicaciones de Service Fabric. API Management se integra directamente con Service Fabric, lo que le permite publicar API con un amplio conjunto de reglas de enrutamiento para los servicios back-end de Service Fabric. 

Ahora que tiene un [clúster de Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) o [clúster de Linux](service-fabric-tutorial-create-vnet-and-linux-cluster.md) en Azure, implemente API Management en la red virtual (VNET), en la subred y en el grupo de seguridad de red diseñados para API Management. Para este tutorial, la plantilla de Resource Manager para API Management está preconfigurada para los nombres de la red virtual, la subred y el grupo de seguridad de red que configuró en el anterior [tutorial de clúster de Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) o [tutorial de clúster de Linux](service-fabric-tutorial-create-vnet-and-linux-cluster.md). 

Descargue la plantilla de Resource Manager y el archivo de parámetros siguientes:
 
- [apim.json][apim-arm]
- [apim.parameters.json][apim-parameters-arm]

Rellene los parámetros vacíos en el archivo `apim.parameters.json` para la implementación.

Use el siguiente script para implementar la plantilla de Resource Manager y los archivos de parámetros para API Management:

```powershell
$ResourceGroupName = "tutorialgroup"
New-AzureRmResourceGroupDeployment -ResourceGroupName $ResourceGroupName -TemplateFile .\apim.json -TemplateParameterFile .\apim.parameters.json -Verbose
```

```azurecli
ResourceGroupName="tutorialgroup"
az group deployment create --name ApiMgmtDeployment --resource-group $ResourceGroupName --template-file apim.json --parameters @apim.parameters.json 
```

## <a name="configure-api-management"></a>Configuración de API Management

Una vez implementados API Management y el clúster de Service Fabric, puede establecer la configuración de seguridad y configurar un back-end de Service Fabric en API Management. De este modo, podrá crear una directiva de servicio de back-end que envíe tráfico al clúster de Service Fabric.

### <a name="configure-api-management-security"></a>Configuración de Seguridad de API Management

Para configurar el back-end de Service Fabric, primero debe realizar la configuración de seguridad de API Management. Para configurar las opciones de seguridad, vaya a su servicio de API Management en Azure Portal.

#### <a name="enable-the-api-management-rest-api"></a>Habilitación de la API de REST de API Management

La API de REST de API Management actualmente es la única manera de configurar un servicio back-end. El primer paso es habilitar la API de REST de API Management y protegerla.

 1. En **Seguridad** del servicio API Management, seleccione **API de Administración**.
 2. Marque la casilla **Habilitar API de REST de API Management**.
 3. Anote la **URL de API de Administración**, que se usará más adelante para configurar el back-end de Service Fabric.
 4. Genere un **token de acceso**; para ello, seleccione una fecha de expiración y una clave, y haga clic en el botón **Generar** situado hacia la parte inferior de la página.
 5. Copie el **token de acceso** y guárdelo.  Usaremos el token de acceso en los siguientes pasos. Tenga en cuenta que es distinto de las claves principal y secundaria.

#### <a name="upload-a-service-fabric-client-certificate"></a>Carga de un certificado de cliente de Service Fabric

API Management debe autenticarse con el clúster de Service Fabric para la detección de servicios mediante un certificado de cliente con acceso al clúster. Para facilitar las cosas, en este tutorial se usa el mismo certificado que se especificó anteriormente al crear el [clúster de Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md#createvaultandcert_anchor) o [clúster de Linux](service-fabric-tutorial-create-vnet-and-linux-cluster.md#createvaultandcert_anchor), que, de forma predeterminada, se puede usar para tener acceso al clúster.

 1. En **Seguridad** del servicio API Management, seleccione **Certificados de cliente**.
 2. Haga clic en el botón **+ Agregar**.
 2. Seleccione el archivo de clave privada (.pfx) del certificado de clúster que especificó al crear el clúster de Service Fabric, asígnele un nombre y escriba la contraseña de clave privada.

> [!NOTE]
> En este tutorial se usa el mismo certificado para la autenticación de cliente y la seguridad de nodo a nodo del clúster. Puede utilizar otro certificado de cliente si tiene uno configurado para acceder al clúster de Service Fabric.

### <a name="configure-the-backend"></a>Configuración del back-end

Ahora que se ha configurado la seguridad de API Management, puede configurar el back-end de Service Fabric. Para Service Fabric, su clúster sirve de back-end, en lugar de usar un servicio de Service Fabric específico. Esto permite que una única directiva enrute a más de un servicio del clúster.

Este paso requiere el token de acceso que se generó anteriormente y la huella digital para el certificado de clúster que anteriormente se cargó en API Management.

> [!NOTE]
> Si usó un certificado de cliente independiente en el paso anterior para API Management, para este paso necesita la huella digital del certificado de cliente además de la huella digital de certificado de clúster.

Envíe la solicitud de PUT de HTTP siguiente a la URL de la API de API Management que anotó anteriormente al habilitar la API de REST de API Management para configurar el servicio de back-end de Service Fabric. Verá una respuesta `HTTP 201 Created` al ejecutarse el comando correctamente. Para más información sobre cada campo, consulte la [documentación de referencia de la API de back-end](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-contract-reference#a-namebackenda-backend) de API Management.

Comando HTTP y dirección URL:
```http
PUT https://your-apim.management.azure-api.net/backends/servicefabric?api-version=2016-10-10
```

Encabezados de solicitud:
```http
Authorization: SharedAccessSignature <your access token>
Content-Type: application/json
```

Cuerpo de la solicitud:
```http
{
    "description": "<description>",
    "url": "<fallback service name>",
    "protocol": "http",
    "resourceId": "<cluster HTTP management endpoint>",
    "properties": {
        "serviceFabricCluster": {
            "managementEndpoints": [ "<cluster HTTP management endpoint>" ],
            "clientCertificateThumbprint": "<client cert thumbprint>",
            "serverCertificateThumbprints": [ "<cluster cert thumbprint>" ],
            "maxPartitionResolutionRetries" : 5
        }
    }
}
```

Este parámetro **url** es el nombre de acceso completo del servicio en el clúster al que se enrutan todas las solicitudes de forma predeterminada si no se especifica ningún nombre de servicio en una directiva de back-end. Puede usar un nombre de servicio falso, como "fabric:/fake/service" si no desea un servicio de reserva.

Para más información sobre cada campo, consulte la [documentación de referencia de la API de back-end](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-contract-reference#a-namebackenda-backend) de API Management.

```python
#import requests library for making REST calls
import requests
import json

#specify url
url = 'https://ryanwiapimgmt.management.azure-api.net/backends/servicefabric?api-version=2016-10-10'

token = "SharedAccessSignature integration&201710140514&Lqnbei7n2Sot6doiNtxMFPUi/m9LsNa+1ZK/PdxqFl49JFWjXh1wW5Gd99r/tIOeHp6dU8UV5iZUdOPfcrm5hg=="

payload = {
    "description": "My Service Fabric backend",
    "url": "fabric:/ApiApplication/ApiWebService",
    "protocol": "http",
    "resourceId": "https://tutorialcluster.eastus.cloudapp.azure.com:19080",
    "properties": {
        "serviceFabricCluster": {
            "managementEndpoints": [ "https://tutorialcluster.eastus.cloudapp.azure.com:19080" ],
            "clientCertificateThumbprint": "97EDD7E4979FB072AF3E480A5E5EE34B1B89DD80",
            "serverCertificateThumbprints": [ "97EDD7E4979FB072AF3E480A5E5EE34B1B89DD80" ],
            "maxPartitionResolutionRetries" : 5
        }
    }
}

headers = {'Authorization': token, "Content-Type": "application/json"}

#Call REST API
response = requests.put(url, data=json.dumps(payload), headers=headers)

#Print Response
print(response.status_code)
print(response.text)
```

## <a name="deploy-a-service-fabric-back-end-service"></a>Implementación de un servicio de back-end de Service Fabric

Ahora que tiene Service Fabric configurado como back-end para API Management, puede crear directivas de back-end para las API que envían tráfico a los servicios de Service Fabric. Pero primero necesita que un servicio que se ejecute en Service Fabric acepte las solicitudes.

### <a name="create-a-service-fabric-service-with-an-http-endpoint"></a>Creación de un servicio de Service Fabric con un punto de conexión HTTP

Para este tutorial, vamos a crear un servicio básico confiable de ASP.NET Core sin estado con la plantilla de proyecto de API web predeterminada. Así se crea un punto de conexión HTTP para el servicio que se expondrá en Azure API Management:

```
/api/values
```

Empiece por [configurar el entorno de desarrollo para ASP.NET Core](service-fabric-add-a-web-frontend.md#set-up-your-environment-for-aspnet-core).

Una vez configurado el entorno de desarrollo, inicie Visual Studio como administrador y cree un servicio ASP.NET Core:

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

    Así, Service Fabric puede especificar un puerto dinámicamente desde el intervalo de puertos de la aplicación, que habremos abierto a través del grupo de seguridad de red en la plantilla del clúster de Resource Manager para que el tráfico fluya a él desde API Management.
 
 6. Presione F5 en Visual Studio para comprobar que la API web está disponible de manera local. 

    Abra Service Fabric Explorer y vaya a una instancia específica del servicio ASP.NET Core para ver la dirección base donde escucha el servicio. Agregue `/api/values` a la dirección base y ábrala en un explorador. Esta acción invoca el método Get en la clase ValuesController de la plantilla de API web y devuelve la respuesta predeterminada de la plantilla, una matriz JSON con dos cadenas:

    ```json
    ["value1", "value2"]`
    ```

    Este es el punto de conexión que deberá exponer a través de API Management en Azure.

 7. Por último, implemente la aplicación en el clúster en Azure. **Con Visual Studio**, haga clic con el botón derecho en el proyecto de la aplicación y seleccione [Publicar](service-fabric-publish-app-remote-cluster.md#to-publish-an-application-using-the-publish-service-fabric-application-dialog-box). Proporcione el punto de conexión del clúster (por ejemplo, `mycluster.westus.cloudapp.azure.com:19000`) para implementar la aplicación en el clúster de Service Fabric en Azure.

En el clúster de Service Fabric en Azure ahora se ejecutará un servicio sin estado ASP.NET Core denominado `fabric:/ApiApplication/WebApiService`.

## <a name="create-an-api-operation"></a>Creación de una operación de API

Ahora estamos listos crear una operación en API Management que los clientes externos usarán para comunicarse con el servicio sin estado ASP.NET Core que se ejecuta en el clúster de Service Fabric.

1. Inicie sesión en Azure Portal y vaya a la implementación del servicio API Management.
2. En la hoja del servicio API Management, seleccione **API**
3. Haga clic en **+API** y en el cuadro **Blank API** (API en blanco) y rellene el cuadro de diálogo para agregar una API nueva:

    - **URL de servicio Web**: para los back-end de Service Fabric, no se utiliza. Puede escribir aquí cualquier valor. Para ver este tutorial, use: "http://servicefabric".
    - **Nombre para mostrar**: proporcione cualquier nombre para la API. Use "Service Fabric App" en este tutorial.
    - **Nombre**: escriba "service-fabric-app".
    - **Esquema URL**: seleccione **HTTP**, **HTTPS** o **ambos**. Use **ambos** en este tutorial.
    - **API URL Suffix** (Sufijo de URL para API): proporcione un sufijo para la API. Use "myapp" en este tutorial.
 
4. Seleccione **Service Fabric App** en la lista de API y haga clic en **+ Agregar operación** para agregar una operación de API de front-end. Rellene los valores:
    
    - **URL**: seleccione **GET** y proporcione una ruta de acceso URL para la API. Use "/api/values" en este tutorial.  De forma predeterminada, la ruta de acceso URL que se especifica aquí se envía al servicio de back-end de Service Fabric. Si utiliza aquí la misma ruta de acceso URL que usa el servicio, en este caso, "/api/values", la operación funciona sin más modificaciones. También puede especificar aquí una ruta de acceso URL distinta de la que usa el servicio de back-end de Service Fabric, en cuyo caso también deberá especificar después una ruta de acceso de reescritura en la directiva de la operación.
    - **Nombre para mostrar**: proporcione cualquier nombre para la API. Use "Values" en este tutorial.

5. Haga clic en **Guardar**.

## <a name="configure-a-backend-policy"></a>Configuración de una directiva de back-end

La directiva de back-end une todos los elementos. Es donde se configura el servicio de back-end de Service Fabric al que se enrutan las solicitudes. Puede aplicar esta directiva a cualquier operación de API. La [configuración de back-end de Service Fabric](https://docs.microsoft.com/azure/api-management/api-management-transformation-policies#SetBackendService) proporciona los controles de enrutamiento de solicitudes siguientes: 
 - Selección de instancias de servicio mediante la especificación de un nombre de instancia de servicio de Service Fabric, ya sea codificado de forma rígida (por ejemplo, `"fabric:/myapp/myservice"`) o generado a partir de la solicitud HTTP (por ejemplo, `"fabric:/myapp/users/" + context.Request.MatchedParameters["name"]`).
 - Resolución de la partición mediante la generación de una clave de partición a partir de cualquier esquema de partición de Service Fabric.
 - Selección de réplicas para los servicios con estado.
 - Condiciones de reintento de resolución que permiten especificar las condiciones para volver a resolver una ubicación de servicio y volver a enviar una solicitud.

Para este tutorial, cree una directiva de back-end que enrute las solicitudes directamente al servicio sin estado ASP.NET Core implementado anteriormente:

 1. Seleccione y edite las **directivas de entrada** para la operación Values; para ello, haga clic en el icono de edición y seleccione **Vista Código**.
 2. En el editor de código de directivas, agregue una directiva `set-backend-service` en las directivas de entrada tal y como se muestra aquí y haga clic en el botón **Guardar**:
    
    ```xml
    <policies>
      <inbound>
        <base/>
        <set-backend-service 
           backend-id="servicefabric"
           sf-service-instance-name="fabric:/ApiApplication/WebApiService"
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

### <a name="add-the-api-to-a-product"></a>Incorporación de la API a un producto 

Antes de que se pueda llamar a la API, debe agregarse a un producto para conceder acceso a los usuarios. 

 1. En el servicio API Management, seleccione **Productos**.
 2. De forma predeterminada, API Management ofrece dos productos: Starter y Unlimited. Seleccione el producto Unlimited.
 3. Seleccione **+Agregar API**.
 4. Seleccione la API `Service Fabric App` que creó en los pasos anteriores y haga clic en el botón **Seleccionar**.

### <a name="test-it"></a>Pruébelo.

Ahora puede intentar enviar una solicitud al servicio back-end de Service Fabric a través de API Management directamente desde Azure Portal.

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
$ResourceGroupName = "tutorialgroup"
Remove-AzureRmResourceGroup -Name $ResourceGroupName -Force
```

```azurecli
ResourceGroupName="tutorialgroup"
az group delete --name $ResourceGroupName
```

## <a name="next-steps"></a>Pasos siguientes
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
