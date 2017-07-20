---
title: "Inicio rápido de Azure Service Fabric con API Management | Microsoft Docs"
description: "En esta guía se muestra cómo empezar a usar rápidamente Azure API Management y Service Fabric."
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: 
ms.assetid: 96176149-69bb-4b06-a72e-ebbfea84454b
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/01/2017
ms.author: vturecek
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: 2160e2e65de5c65df8a13248bad4f626def86e49
ms.contentlocale: es-es
ms.lasthandoff: 06/28/2017


---

# <a name="service-fabric-with-azure-api-management-quick-start"></a>Inicio rápido de Service Fabric con Azure API Management

Esta guía muestra cómo configurar Azure API Management con Service Fabric y la primera operación de API para enviar tráfico a los servicios de back-end de Service Fabric. Para más información sobre escenarios de Azure API Management con Service Fabric, consulte el artículo de [introducción](service-fabric-api-management-overview.md). 

## <a name="deploy-api-management-and-service-fabric-to-azure"></a>Implementación de API Management y Service Fabric en Azure

El primer paso es implementar API Management y un clúster de Service Fabric en Azure, en una red virtual compartida. Esto permite a API Management comunicarse directamente con Service Fabric para que realice la detección de servicios, la resolución de la partición de servicio y que reenvíe el tráfico directamente a cualquier servicio de back-end de Service Fabric.

### <a name="topology"></a>Topología

Esta guía distribuye en Azure la topología siguiente, donde API Management y Service Fabric se encuentran en subredes de la misma red virtual:

 ![Leyenda de la imagen][sf-apim-topology-overview]

Para empezar a trabajar rápidamente, se proporcionan plantillas de Resource Manager para cada paso de la implementación:

 - Topología de red:
    - [network.json][network-arm]
    - [network.parameters.json][network-parameters-arm]
 - Clúster de Service Fabric:
    - [cluster.json][cluster-arm]
    - [cluster.parameters.json][cluster-parameters-arm]
 - API Management:
    - [apim.json][apim-arm]
    - [apim.parameters.json][apim-parameters-arm]

### <a name="sign-in-to-azure-and-select-your-subscription"></a>Inicio de sesión en Azure y selección de la suscripción

En esta guía se usa [Azure PowerShell][azure-powershell]. Al iniciar una nueva sesión de PowerShell, inicie sesión en su cuenta de Azure y seleccione su suscripción antes de ejecutar comandos de Azure.
 
Inicio de sesión en la cuenta de Azure

```powershell
PS > Login-AzureRmAccount
```

Seleccione su suscripción:

```powershell
PS > Get-AzureRmSubscription
PS > Set-AzureRmContext -SubscriptionId <guid>
```

### <a name="create-a-resource-group"></a>Crear un grupo de recursos

Cree un grupo de recursos para la implementación. Asígnele un nombre y una ubicación.

```powershell
PS > New-AzureRmResourceGroup -Name <my-resource-group> -Location westus
```

### <a name="deploy-the-network-topology"></a>Implementación de la topología de red

El primer paso es configurar la topología de la red de implementación de API Management y el clúster de Service Fabric. La plantilla de Resource Manager [network.json][network-arm] está configurada para crear una red Virtual (VNET) con dos subredes y dos grupos de seguridad de red (NSG). 

El archivo de parámetros [network.parameters.json][network-parameters-arm] contiene los nombres de las subredes y los grupos de seguridad de red donde se implementarán API Management y Service Fabric. En esta guía, los valores de los parámetros no deben cambiarse. Las plantillas de Resource Manager para API Management y Service Fabric usan estos valores, por lo que, si los modifica aquí, debe modificarlos también en las demás plantillas de Resource Manager. 

 1. Descargue la plantilla de Resource Manager y el archivo de parámetros siguientes:

    - [network.json][network-arm]
    - [network.parameters.json][network-parameters-arm]

 2. Use el siguiente comando de PowerShell para implementar la plantilla de Resource Manager y los archivos de parámetros para la configuración de la red:

    ```powershell
    PS > New-AzureRmResourceGroupDeployment -ResourceGroupName <my-resource-group> -TemplateFile .\network.json -TemplateParameterFile .\network.parameters.json -Verbose
    ```

### <a name="deploy-the-service-fabric-cluster"></a>Implementación del clúster de Service Fabric

Una vez implementados los recursos de red, el siguiente paso es implementar un clúster de Service Fabric a la red virtual, en la subred y el grupo de seguridad de red designados para el clúster de Service Fabric. Para este tutorial, la plantilla de Resource Manager para Service Fabric está preconfigurada para los nombres de la red virtual, la subred y el grupo de seguridad de red que configuró en el paso anterior. 

La plantilla de Resource Manager para el clúster de Service Fabric está configurada para crear un clúster con certificado seguro. El certificado se utiliza para proteger la comunicación de nodo a nodo del clúster y para administrar el acceso de los usuarios al clúster de Service Fabric. API Management usa este certificado para acceder al servicio de nomenclatura de Service Fabric para detectar servicios.

Este paso requiere un certificado en Key Vault para la seguridad del clúster. Para más información sobre cómo configurar un clúster seguro en Key Vault, consulte [esta guía sobre la creación de un clúster de Azure con Resource Manager](service-fabric-cluster-creation-via-arm.md).

> [!NOTE]
> Puede agregar la autenticación de Azure Active Directory además del certificado de acceso al clúster. Azure Active Directory es la manera recomendada para administrar el acceso de usuario al clúster de Service Fabric, pero esto no es necesario para completar el tutorial. En cualquier caso, se necesita un certificado para la seguridad de nodo a nodo del clúster y para la autenticación de Azure API Management, que actualmente no admite la autenticación con Azure Active Directory para el back-end de Service Fabric.

 1. Descargue la plantilla de Resource Manager y el archivo de parámetros siguientes:
 
    - [cluster.json][cluster-arm]
    - [cluster.parameters.json][cluster-parameters-arm]

 2. Rellene los parámetros vacíos del archivo `cluster.parameters.json` para la implementación, incluida la [información del almacén de claves](service-fabric-cluster-creation-via-arm.md#set-up-a-key-vault) para el certificado del clúster.

 3. Use el siguiente comando de PowerShell para implementar la plantilla de Resource Manager y los archivos de parámetros para crear el clúster de Service Fabric:

    ```powershell
    PS > New-AzureRmResourceGroupDeployment -ResourceGroupName <my-resource-group> -TemplateFile .\cluster.json -TemplateParameterFile .\cluster.parameters.json -Verbose
    ```

### <a name="deploy-api-management"></a>Implementación de API Management

Por último, implemente API Management en la red virtual, en la subred y el grupo de seguridad de red designados para API Management. No es necesario esperar a que finalice la implementación del clúster de Service Fabric para implementar API Management. 

Para este tutorial, la plantilla de Resource Manager para API Management está preconfigurada para los nombres de la red virtual, la subred y el grupo de seguridad de red que configuró en el paso anterior. 

 1. Descargue la plantilla de Resource Manager y el archivo de parámetros siguientes:
 
    - [apim.json][apim-arm]
    - [apim.parameters.json][apim-parameters-arm]

 2. Rellene los parámetros vacíos en el archivo `apim.parameters.json` para la implementación.

 3. Use el siguiente comando de PowerShell para implementar la plantilla de Resource Manager y los archivos de parámetros para API Management:

    ```powershell
    PS > New-AzureRmResourceGroupDeployment -ResourceGroupName <my-resource-group> -TemplateFile .\apim.json -TemplateParameterFile .\apim.parameters.json -Verbose
    ```

## <a name="configure-api-management"></a>Configuración de API Management

Una vez implementados API Management y el clúster de Service Fabric, puede configurar un back-end de Service Fabric en API Management. De este modo, podrá crear una directiva de servicio de back-end que envíe tráfico al clúster de Service Fabric.

### <a name="api-management-security"></a>Seguridad de API Management

Para configurar el back-end de Service Fabric, primero debe realizar la configuración de seguridad de API Management. Para configurar las opciones de seguridad, vaya a su servicio de API Management en Azure Portal.

#### <a name="enable-the-api-management-rest-api"></a>Habilitación de la API de REST de API Management

La API de REST de API Management actualmente es la única manera de configurar un servicio back-end. El primer paso es habilitar la API de REST de API Management y protegerla.

 1. En **Seguridad** del servicio API Management, seleccione **API Management (versión preliminar)**.
 2. Marque la casilla **Habilitar API de REST de API Management**.
 3. Anote la URL de API Management, se usará más adelante para configurar el back-end de Service Fabric.
 4. Genere un **token de acceso**; para ello, seleccione una fecha de expiración y una clave, y haga clic en el botón **Generar** situado hacia la parte inferior de la página.
 5. Copie el **token de acceso** y guárdelo, lo usaremos en los pasos siguientes. Tenga en cuenta que es distinto de las claves principal y secundaria.

#### <a name="upload-a-service-fabric-client-certificate"></a>Carga de un certificado de cliente de Service Fabric

API Management debe autenticarse con el clúster de Service Fabric para la detección de servicios mediante un certificado de cliente con acceso al clúster. Para facilitar las cosas, en este tutorial se usa el mismo certificado que se especificó al crear el clúster de Service Fabric, que, de forma predeterminada, se puede usar para acceder al clúster.

 1. En **Seguridad** del servicio API Management, seleccione **Certificados de cliente (versión preliminar)**.
 2. Haga clic en el botón **+ Agregar**.
 2. Seleccione el archivo de clave privada (.pfx) del certificado de clúster que especificó al crear el clúster de Service Fabric, asígnele un nombre y escriba la contraseña de clave privada.

> [!NOTE]
> En este tutorial se usa el mismo certificado para la autenticación de cliente y la seguridad de nodo a nodo del clúster. Puede utilizar otro certificado de cliente si tiene uno configurado para acceder al clúster de Service Fabric.

### <a name="configure-the-backend"></a>Configuración del back-end

Ahora que se ha configurado la seguridad de API Management, puede configurar el back-end de Service Fabric. Para Service Fabric, su clúster sirve de back-end, en lugar de usar un servicio de Service Fabric específico. Esto permite que una única directiva enrute a más de un servicio del clúster.

Este paso requiere el token de acceso que se generó anteriormente y la huella digital para el certificado de clúster que se cargó en API Management en el paso anterior.

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
            "partitionResolutionRequestTimeout" : "00:02:00",
            "maxPartitionResolutionRetries" : 5
        }
    }
}
```

Este parámetro **url** es el nombre de acceso completo del servicio en el clúster al que se enrutan todas las solicitudes de forma predeterminada si no se especifica ningún nombre de servicio en una directiva de back-end. Puede usar un nombre de servicio falso, como "fabric:/fake/service" si no desea un servicio de reserva.

Para más información sobre cada campo, consulte la [documentación de referencia de la API de back-end](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-contract-reference#a-namebackenda-backend) de API Management.

#### <a name="example"></a>Ejemplo

```http
PUT https://your-apim.management.azure-api.net/backends/servicefabric?api-version=2016-10-10
Authorization: SharedAccessSignature 230948023984&Ld93cRGcNU6KZ4uVz7JlfTec4eX43Q9Nu8ndatOgBzs6+f559Pkf3iHX2cSge+r42pn35qGY3TitjrIl13hwcQ==
Content-Type: application/json

{
    "description": "My Service Fabric backend",
    "url": "fabric:/myapp/myservice",
    "protocol": "http",
    "resourceId": "https://your-cluster.westus.cloudapp.azure.com:19080",
    "properties": {
        "serviceFabricCluster": {
            "managementEndpoints": ["https://your-cluster.westus.cloudapp.azure.com:19080"],
            "clientCertificateThumbprint": "57bc463aba3aea3a12a18f36f44154f819f0fe32",
            "serverCertificateThumbprints": ["57bc463aba3aea3a12a18f36f44154f819f0fe32"],
            "partitionResolutionRequestTimeout" : "00:02:00",
            "maxPartitionResolutionRetries" : 5
        }
    }
}
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
 2. En la hoja del servicio API Management, seleccione **API - VERSIÓN PRELIMINAR**.
 3. Haga clic en el cuadro **Blank API** (API en blanco) y rellene el cuadro de diálogo para agregar una API nueva:

     - **URL de servicio Web**: para los back-end de Service Fabric, no se utiliza. Puede escribir aquí cualquier valor. Para este tutorial usaremos `http://servicefabric`.
     - **Nombre**: proporcione cualquier nombre para la API. Para este tutorial usaremos `Service Fabric App`.
     - **Esquema URL**: seleccione HTTP, HTTPS o ambos. Para este tutorial usaremos `both`.
     - **API URL Suffix** (Sufijo de URL para API): proporcione un sufijo para la API. Para este tutorial usaremos `myapp`.
 
 4. Una vez creada la API, haga clic en **+ Add operation** (+ Agregar operación) para agregar una operación de API de front-end. Rellene los valores:
    
     - **URL**: seleccione `GET` y proporcione una ruta de acceso URL para la API. Para este tutorial usaremos `/api/values`.
     
       De forma predeterminada, la ruta de acceso URL que se especifica aquí se envía al servicio de back-end de Service Fabric. Si utiliza aquí la misma ruta de acceso URL que usa el servicio, en este caso, `/api/values`, la operación funciona sin más modificaciones. También puede especificar aquí una ruta de acceso URL distinta de la que usa el servicio de back-end de Service Fabric, en cuyo caso también deberá especificar después una ruta de acceso de reescritura en la directiva de la operación.
     - **Nombre para mostrar**: proporcione cualquier nombre para la API. Para este tutorial usaremos `Values`.

## <a name="configure-a-backend-policy"></a>Configuración de una directiva de back-end

La directiva de back-end une todos los elementos. Es donde se configura el servicio de back-end de Service Fabric al que se enrutan las solicitudes. Puede aplicar esta directiva a cualquier operación de API. La [configuración de back-end de Service Fabric](https://docs.microsoft.com/azure/api-management/api-management-transformation-policies#SetBackendService) proporciona los controles de enrutamiento de solicitudes siguientes: 
 - Selección de instancias de servicio mediante la especificación de un nombre de instancia de servicio de Service Fabric, ya sea codificado de forma rígida (por ejemplo, `"fabric:/myapp/myservice"`) o generado a partir de la solicitud HTTP (por ejemplo, `"fabric:/myapp/users/" + context.Request.MatchedParameters["name"]`).
 - Resolución de la partición mediante la generación de una clave de partición a partir de cualquier esquema de partición de Service Fabric.
 - Selección de réplicas para los servicios con estado.
 - Condiciones de reintento de resolución que permiten especificar las condiciones para volver a resolver una ubicación de servicio y volver a enviar una solicitud.

Para este tutorial, cree una directiva de back-end que enrute las solicitudes directamente al servicio sin estado ASP.NET Core implementado anteriormente:

 1. Seleccione y edite las **directivas de entrada** para la operación `Values`; para ello, haga clic en el icono de edición y seleccione **Vista Código**.
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

 1. En el servicio API Management, seleccione **Productos - VERSIÓN PRELIMINAR**.
 2. De forma predeterminada, API Management ofrece dos productos: Starter y Unlimited. Seleccione el producto Unlimited.
 3. Seleccione las API.
 4. Haga clic en el botón **+ Agregar**.
 5. Seleccione la API `Service Fabric App` que creó en los pasos anteriores y haga clic en el botón **Seleccionar**.

### <a name="test-it"></a>Pruébelo.

Ahora puede intentar enviar una solicitud al servicio back-end de Service Fabric a través de API Management directamente desde Azure Portal.

 1. En el servicio API Management, seleccione **API - PREVIEW** (API [versión preliminar]).
 2. En la API `Service Fabric App` que creó en los pasos anteriores, seleccione la pestaña **Prueba**.
 3. Haga clic en el botón **Enviar** para enviar una solicitud de prueba al servicio de back-end.

## <a name="next-steps"></a>Pasos siguientes

Llegados a este punto, debe tener una configuración básica de Service Fabric y API Management.

Para una rápida configuración, en este tutorial se utiliza la autenticación de usuario básica basada en certificados para el clúster de Service Fabric, aunque para el clúster de Service Fabric es preferible una autenticación de usuarios más avanzada con [autenticación de Azure Active Directory](service-fabric-cluster-creation-via-arm.md#set-up-azure-active-directory-for-client-authentication). 

A continuación, [cree y configure las opciones de producto avanzadas en Azure API Management](https://docs.microsoft.com/azure/api-management/api-management-howto-product-with-rules) para preparar la aplicación para el tráfico del mundo real.

<!-- links -->
[azure-powershell]:https://azure.microsoft.com/documentation/articles/powershell-install-configure/

[network-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/network.json
[network-parameters-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/network.parameters.json

[apim-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/apim.json
[apim-parameters-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/apim.parameters.json

[cluster-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/cluster.json
[cluster-parameters-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/cluster.parameters.json


<!-- pics -->
[sf-apim-topology-overview]: ./media/service-fabric-api-management-quickstart/sf-apim-topology-overview.png

