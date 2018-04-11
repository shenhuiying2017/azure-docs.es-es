---
title: Uso de perfiles de la versión de la API con GO en Azure Stack | Microsoft Docs
description: Información acerca de los perfiles de la versión de la API con GO en Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 84475302-EFC2-4C35-B4CF-60C857A96345
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2018
ms.author: mabrigg
ms.reviewer: sijuman
ms.openlocfilehash: 09a774e5dda71d87c3862a6152ff5d2c9468c40c
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/03/2018
---
# <a name="use-api-version-profiles-with-go-in-azure-stack"></a>Uso de los perfiles de la versión de la API con GO en Azure Stack

*Se aplica a: sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

## <a name="go-and-version-profiles"></a>GO y perfiles de la versión

Un perfil es una combinación de diferentes tipos de recursos con distintas versiones de diferentes servicios. Los perfiles le ayudarán a mezclar y combinar diferentes tipos de recursos. Pueden proporcionar:

 - Estabilidad para la aplicación mediante el bloqueo de versión específicas de la API.
 - Compatibilidad para la aplicación con Azure Stack y centros de datos de Azure regionales.

En el SDK para GO, los perfiles están disponibles en la ruta de acceso profiles/, con la versión en formato **AAAA-MM-DD**. En este momento, la última versión del perfil de Azure Stack es **2017-03-09**. Para importar un servicio determinado de un perfil, debe importar su módulo correspondiente del perfil. Por ejemplo, para importar el servicio **Compute** desde el perfil **2017-03-09**:

````go
import "github.com/Azure/azure-sdk-for-go/profi1es/2e17-e3-eg/compute/mgmt/compute" 
````

## <a name="install-azure-sdk-for-go"></a>Instalación del SDK de Azure para GO

  1. Instale Git. Para instrucciones, consulte [Introducción: instalación de Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).
  2. Instale el [lenguaje de programación de GO](https://golang.org/dl).  
  Los perfiles de la API de Azure requerirán la versión 1.9 de GO u otra más reciente.
  3. Instale el SDK de Azure para GO y sus dependencias mediante la ejecución del siguiente comando de Bash:
  ```
    go get -u -d github.com/Azure/azure-sdk-for-go/...
  ```

### <a name="the-go-sdk"></a>SDK para GO

Más información sobre el SDK de Azure para GO en:
- SDK de Azure para GO en [Instalación del SDK de Azure para Go](https://docs.microsoft.com/go/azure/azure-sdk-go-install).
- El SDK de Azure para GO está públicamente disponible en GitHub, en [azure-sdk-for-go](https://github.com/Azure/azure-sdk-for-go).

### <a name="go-autorest-dependencies"></a>Dependencias de Go-AutoRest

El SDK para GO depende de los módulos de Azure Go-AutoRest para enviar solicitudes REST a puntos de conexión de Azure Resource Manager. Debe importar las dependencias de los módulos de Azure Go-AutoRest de [Go-AutoRest de Azure en GitHub](https://github.com/Azure/go-autorest). Puede encontrar los comandos de Bash de instalación en la sección **Instalación**.

## <a name="how-to-use-go-sdk-profiles-on-azure-stack"></a>Uso de perfiles de SDK para GO en Azure Stack

Para ejecutar un ejemplo de código de GO en Azure Stack:
  1. Instale el SDK de Azure para GO y sus dependencias. Para obtener instrucciones, consulte la sección anterior, [Instalación del SDK de Azure para GO](#install-azure-sdk-for-go).
  2. Obtenga la información de metadatos del punto de conexión de Resource Manager. El punto de conexión devuelve un archivo JSON con la información necesaria para ejecutar el código de GO.
  > [!note]  
  > El valor de **ResourceManagerUrl** del Kit de desarrollo de Azure Stack (ASDK) es: `https://management.local.azurestack.external/`  
  > El valor de **ResourceManagerUrl** en los sistemas integrados es: `https://management.<location>.ext-<machine-name>.masd.stbtest.microsoft.com/`  
  > Para recuperar los metadatos necesarios: `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0`
  
  Archivo JSON de ejemplo:

  ```json
  { "galleryEndpoint": "https://portal.local.azurestack.external:30015/",  
    "graphEndpoint": "https://graph.windows.net/",  
    "portal Endpoint": "https://portal.local.azurestack.external/", 
    "authentication": {
      "loginEndpoint": "https://login.windows.net/", 
      "audiences": ["https://management.<yourtenant>.onmicrosoft.com/3cc5febd-e4b7-4a85-a2ed-1d730e2f5928"]
    }
  }
  ```

  3. Si no está disponible, cree una suscripción y guarde su identificador para usarlo más adelante. Para información sobre la creación de una suscripción, consulte [Creación de suscripciones para ofertas en Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-subscribe-plan-provision-vm). 
  4. Cree una entidad de servicio con ámbito de suscripción y el rol **Propietario**. Guarde el identificador y el secreto de la entidad de servicio. Para información sobre la creación de una entidad de servicio para Azure Stack, consulte [Creación de una entidad de servicio](https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals#create-service-principal-for-azure-ad). El entorno de Azure Stack se ha configurado.
  5. Importe un módulo de servicio del perfil del SDK para GO del código. La versión actual del perfil de Azure Stack es **2017-03-09**. Por ejemplo, para importar el módulo de red del tipo de perfil **2017-03-09**: 

  ````go
    package main 
    import "github.com/Azure/azure-sdk-for-go/profiles/2017-03-09/network/mgmt/network"
  ````
  
  6. En la función, cree y autentique un cliente con una llamada de función de cliente **New**. Para crear un cliente de red virtual, puede usar el siguiente código:  

  ````go
  package main 

  import "github.com/Azure/azure-sdk-for-go/profiles/2017-03-09/network/mgmt/network" 

  func main() { 
      vnetClient := network.NewVirtualNetworksClientWithBaseURI("<baseURI>", "(subscriptionID>")
      vnetClient.Authorizer = autorest.NewBearerAuthorizer(token)
  ````

  Establezca `<baseURI>` en el valor de **ResourceManagerUrl** que utilizó en el segundo paso.
  Establezca `<subscriptionID>` en el valor de **SubscriptionID** que guardó en el tercer paso.
  Para crear tokens, consulte la sección de autenticación más adelante.  

  7. Invoque métodos de API con el cliente que creó en el paso anterior. Por ejemplo, para crear una red virtual mediante el cliente del paso anterior: 
  
````go
package main

import "github.com/Azure/azure-sdk-for-go/profiles/2017-03-09/network/mgmt/network" 
func main() { 
  vnetC1ient := network.NewVirtualNetworksClientWithBaseURI("<baseURI>", "(subscriptionID>")
  vnetClient .Authorizer = autorest.NewBearerAuthorizer(token)

  vnetClient .CreateOrUpdate( ) 
````
  
  Para ver un ejemplo completo sobre la creación de una red virtual en Azure Stack mediante el perfil del SDK para GO, consulte [Ejemplo](#example).

## <a name="authentication"></a>Autenticación

Para obtener la propiedad Authorizer de Azure Active Directory mediante el SDK para GO, instale los módulos de Go-AutoRest. Estos módulos se deben haber instalado ya con la instalación del SDK para GO; si no es así, instale el [paquete de autenticación de GitHub](https://github.com/Azure/go-autorest/tree/master/autorest/adal).

Authorizer se debe establecer como autorizador del cliente de recursos. Existen varios métodos para obtener un autorizador; aquí encontrará una lista completa.

En esta sección, se presenta una forma común de obtener tokens de autorizador en Azure Stack mediante credenciales de cliente:

  1. Si hay una entidad de servicio con el rol de propietario en la suscripción disponible, omita este paso. De lo contrario, cree una entidad de servicio ([instrucciones]( https://docs.microsoft.com/en-us/azure/azure-stack/azure-stack-create-service-principals)) y asígnele un rol de propietario con ámbito en la suscripción ([instrucciones]( https://docs.microsoft.com/en-us/azure/azure-stack/azure-stack-create-service-principals#assign-role-to-service-principal)). Guarde el identificador y el secreto de la aplicación de la entidad de servicio. 

  2. Importe el paquete **adal** de Go-AutoRest en el código. 
  
  ````go
  package main
  import "github.com/Azure/go-autorest/autorest/adal" 
  ````

  3. Cree oauthConfig mediante el método NewOAuthConfig del módulo **adal**. 
  
  ````go
  package main 

  import "github.com/Azure/go-autorest/autorest/ada1" 

  func CreateToken() (adal.OAuthTokenProvider, error) {
      var token adal.OAuthTokenProvider
      oauthConfig, err := adal.NewOAuthConfig(activeDirectoryEndpoint, tenantID)
  ````
   
  Establezca `<activeDirectoryEndpoint>` en el valor de la propiedad "loginEndpoint" de los metadatos de ResourceManagerUrl recuperados en la sección anterior de este documento.
  Establezca el valor `<tenantID>` como identificador del inquilino de Azure Stack. 

  4. Finalmente, cree un token de entidad de servicio mediante el método NewServicePrincipalToken del módulo adal. 

  ````go
  package main 

  import "github.com/Azure/go-autorest/autorest/adal" 

  func CreateToken() (adal.OAuthTokenProvider, error) {
      var token adal.OAuthTokenProvider
      oauthConfig, err := adal.NewOAuthConfig(activeDirectoryEndpoint, tenantID)
      token, err = adal.NewServicePrincipalToken(
          *oauthConfig,
          clientID,
          clientSecret,
          activeDirectoryResourceID)
      return token, err
  ````
  
  Establezca `<activeDirectoryResourceID>` en uno de los valores de la lista "audience" de los metadatos de ResourceManagerUrl recuperados en la sección anterior de este documento.  
  Establezca `<clientID>` en el identificador de aplicación de la entidad de servicio que guardó al crear esta última en la sección anterior de este documento.  
  Establezca `<clientSecret>` en el secreto de aplicación de la entidad de servicio que guardó al crear esta última en la sección anterior de este documento.  

## <a name="example"></a>Ejemplo

En esta sección, se muestra un ejemplo de código de GO para crear una red virtual en Azure Stack. Para ver ejemplos completos de SDK para GO, consulte el [repositorio de ejemplos de SDK de Azure para GO](https://github.com/Azure-Samples/azure-sdk-for-go-samples). Hay ejemplos de Azure Stack disponibles en la ruta de acceso hybrid/, dentro de las carpetas de servicios del repositorio.
> [!note]  
> Para ejecutar el código de este ejemplo, compruebe que la suscripción utilizada tiene el proveedor de recursos **Red** como **Registrado**. Para confirmarlo, busque la suscripción en el portal de Azure Stack y haga clic en **Proveedores de recursos**.

1. Importe los paquetes necesarios en el código. Debe usar el perfil más reciente disponible en Azure Stack para importar el módulo de red. 
  
  ````go
  package main

  import (
      "context"
      "fmt"
      "github.com/Azure/azure-sdk-for-go/profiles/2017-03-09/network/mgmt/network"
      "github.com/Azure/go-autorest/autorest"
      "github.com/Azure/go-autorest/autorest/adal"
      "github.com/Azure/go-autorest/autorest/to"
  )
  ````

2. Defina las variables del entorno. Tenga en cuenta que, para crear una red virtual, debe tener un grupo de recursos. 

  ````go
  var (
      activeDirectoryEndpoint = "yourLoginEndpointFromResourceManagerUrlMetadata"
      tenantID = "yourAzureStackTenantID"
      clientID = "yourServicePrincipalApplicationID"
      clientSecret = "yourServicePrincipalSecret"
      activeDirectoryResourceID = "yourAudienceFromResourceManagerUrlMetadata"
      subscriptionID = "yourSubscriptionID"
      baseURI = "yourResourceManagerURL"
      resourceGroupName = "existingResourceGroupName"
  )
  ````

3. Ahora que ha definido las variables del entorno, agregue un método para crear el token de autenticación mediante el paquete **adal**. Consulte los detalles acerca de la autenticación en la sección anterior.
  
  ````go
  //CreateToken creates a service principal token
  func CreateToken() (adal.OAuthTokenProvider, error) {
      var token adal.OAuthTokenProvider
      oauthConfig, err := adal.NewOAuthConfig(activeDirectoryEndpoint, tenantID)
      token, err = adal.NewServicePrincipalToken(
          *oauthConfig,
          clientID,
          clientSecret,
          activeDirectoryResourceID)
      return token, err
  }
  ````

4. Agregue el método principal. El método principal primero obtiene un token mediante el método que se definió en el paso anterior. A continuación, crea a un cliente mediante el módulo de red del perfil. Por último, crea una red virtual. 
  
````go
package main

import (
    "context"
    "fmt"
    "github.com/Azure/azure-sdk-for-go/profiles/2017-03-09/network/mgmt/network"
    "github.com/Azure/go-autorest/autorest"
    "github.com/Azure/go-autorest/autorest/adal"
    "github.com/Azure/go-autorest/autorest/to"
)

var (
    activeDirectoryEndpoint = "yourLoginEndpointFromResourceManagerUrlMetadata"
    tenantID = "yourAzureStackTenantID"
    clientID = "yourServicePrincipalApplicationID"
    clientSecret = "yourServicePrincipalSecret"
    activeDirectoryResourceID = "yourAudienceFromResourceManagerUrlMetadata"
    subscriptionID = "yourSubscriptionID"
    baseURI = "yourResourceManagerURL"
    resourceGroupName = "existingResourceGroupName"
)

//CreateToken creates a service principal token
func CreateToken() (adal.OAuthTokenProvider, error) {
    var token adal.OAuthTokenProvider
    oauthConfig, err := adal.NewOAuthConfig(activeDirectoryEndpoint, tenantID)
    token, err = adal.NewServicePrincipalToken(
        *oauthConfig,
        clientID,
        clientSecret,
        activeDirectoryResourceID)
    return token, err
}

func main() {
    token, _ := CreateToken()
    vnetClient := network.NewVirtualNetworksClientWithBaseURI(baseURI, subscriptionID)
    vnetClient.Authorizer = autorest.NewBearerAuthorizer(token)
    future, _ := vnetClient.CreateOrUpdate(
        context.Background(),
        resourceGroupName,
        "sampleVnetName",
        network.VirtualNetwork{
            Location: to.StringPtr("local"),
            VirtualNetworkPropertiesFormat: &network.VirtualNetworkPropertiesFormat{
                AddressSpace: &network.AddressSpace{
                    AddressPrefixes: &[]string{"10.0.0.0/8"},
                },
                Subnets: &[]network.Subnet{
                    {
                        Name: to.StringPtr("subnetName"),
                        SubnetPropertiesFormat: &network.SubnetPropertiesFormat{
                            AddressPrefix: to.StringPtr("10.0.0.0/16"),
                        },
                    },
                },
            },
        })
    err := future.WaitForCompletion(context.Background(), vnetClient.Client)
    if err != nil {
        fmt.Printf(err.Error())
        return
    }
}

````

## <a name="next-steps"></a>Pasos siguientes
* [Install PowerShell for Azure Stack](azure-stack-powershell-install.md) (Instalación de PowerShell para Azure Stack)
* [Configuración del entorno de PowerShell del usuario de Azure Stack](azure-stack-powershell-configure-user.md)  
