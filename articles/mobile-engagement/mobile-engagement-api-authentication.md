---
title: "Autenticación con las API de REST de Mobile Engagement"
description: "Se describe cómo autenticarse con las API de REST de Azure Mobile Engagement"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: da82cb36-957a-4e19-a805-b44733cf6597
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 10/05/2016
ms.author: wesmc;ricksal
ms.openlocfilehash: 66bcd738b86f846eae3499b289a6629323009a44
ms.sourcegitcommit: d6984ef8cc057423ff81efb4645af9d0b902f843
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/05/2018
---
# <a name="authenticate-with-mobile-engagement-rest-apis"></a>Autenticación con las API de REST de Mobile Engagement

## <a name="overview"></a>Información general

En este documento se describe cómo obtener un token válido de OAuth de Azure AD para autenticarse en las API de REST de Mobile Engagement.

Se supone que tiene una suscripción válida a Azure y que ha creado una aplicación de Mobile Engagement con uno de los [tutoriales para desarrolladores](mobile-engagement-windows-store-dotnet-get-started.md).

## <a name="authentication"></a>Autenticación

Se usa un token de OAuth basado en Microsoft Azure Active Directory para la autenticación. 

A fin de autenticar una solicitud de API, es necesario agregar un encabezado de autorización a cada solicitud, que presenta la siguiente forma:

    Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGmJlNmV2ZWJPamg2TTNXR1E...

> [!NOTE]
> Los tokens de Azure Active Directory expiran en una hora.
> 
> 

Existen diferentes formas de obtener un token. Debido a que a las API se las llama desde un servicio en la nube, preferirá usar una clave de API. En la terminología de Azure, a una clave de API se la conoce como contraseña de entidad de servicio. El siguiente procedimiento describe un modo de configurarla manualmente.

### <a name="one-time-setup-using-script"></a>Instalación única (mediante script)

Siga el conjunto de instrucciones indicado más abajo para realizar la instalación mediante un script de PowerShell, que tarda la cantidad de tiempo mínima, pero usa los valores predeterminados más permisivos. Opcionalmente, también puede seguir las instrucciones de [instalación manual](mobile-engagement-api-authentication-manual.md) para llevar esto a cabo directamente desde el Portal de Azure y especificar una configuración más detallada.

1. Obtenga la versión más reciente de Azure PowerShell [aquí](http://aka.ms/webpi-azps). Para obtener más información sobre las instrucciones de descarga, puede consultar este [vínculo](/powershell/azure/overview).
2. Una vez instalado Azure PowerShell, use los siguientes comandos para asegurarse de que tiene el **módulo de Azure** instalado:

    a. Asegúrese de que el módulo de Azure PowerShell está disponible en la lista de módulos disponibles.

        Get-Module –ListAvailable

    ![Módulos de Azure disponibles][1]

    b. Si no encuentra el módulo de Azure PowerShell en la lista anterior, deberá ejecutar:

        Import-Module Azure
3. Inicie sesión en Azure Resource Manager desde PowerShell. Para ello, ejecute el siguiente comando y especifique el nombre de usuario y la contraseña de su cuenta de Azure: 

        Login-AzureRmAccount
4. Si tiene varias suscripciones, debe ejecutar:

    a. Obtenga una lista de todas sus subscripciones y copie el SubscriptionId de la suscripción que quiera usar. Asegúrese de que esta suscripción sea la misma que tenga la aplicación de Mobile Engagement con la que vaya a interactuar mediante las API. 

        Get-AzureRmSubscription

    b. Ejecute el siguiente comando proporcionando el SubscriptionId para configurar la suscripción que se debe usar.

        Select-AzureRmSubscription –SubscriptionId <subscriptionId>
5. Copie el texto para el script [New-AzureRmServicePrincipalOwner.ps1](https://raw.githubusercontent.com/matt-gibbs/azbits/master/src/New-AzureRmServicePrincipalOwner.ps1) en su máquina local, guárdelo como un cmdlet de PowerShell (p. ej., `APIAuth.ps1`) y ejecútelo `.\APIAuth.ps1`.
6. El script le pedirá que especifique una entrada para **principalName**. Proporcione un nombre adecuado que quiera utilizar para crear la aplicación de Active Directory (p. ej., APIAuth). 
7. Una vez que se haya finalizado el script, mostrará los siguientes cuatro valores que tendrá que autenticar de manera programática con AD, por lo que no olvide copiarlos. 

    **TenantId**, **SubscriptionId**, **ApplicationId** y **Secret**.

    Usará TenantId como `{TENANT_ID}`, ApplicationId como `{CLIENT_ID}` y Secret como `{CLIENT_SECRET}`.

   > [!NOTE]
   > Es posible que la directiva de seguridad predeterminada le impida ejecutar scripts de PowerShell. Si es así, configure temporalmente la directiva de ejecución para permitir la ejecución de scripts mediante el comando siguiente:
   > 
   > Set-ExecutionPolicy RemoteSigned
8. Este es el aspecto que presentaría el conjunto de cmdlets de PowerShell.
    ![][3]
9. En Azure Portal, vaya a Active Directory, haga clic en **Registros de aplicaciones** y busque la aplicación para asegurarse de que existe ![][4]

### <a name="steps-to-get-a-valid-token"></a>Pasos para obtener un token válido

1. Llame a la API con los siguientes parámetros y asegúrese de sustituir TENANT\_ID, CLIENT\_ID y CLIENT\_SECRET:
   
   * **Dirección URL de la solicitud** como `https://login.microsoftonline.com/{TENANT_ID}/oauth2/token`
   * **Encabezado Content-Type HTTP** como `application/x-www-form-urlencoded`
   * **Cuerpo de la solicitud HTTP** como `grant_type=client\_credentials&client_id={CLIENT_ID}&client_secret={CLIENT_SECRET}&resource=https%3A%2F%2Fmanagement.core.windows.net%2F`
     
    La siguiente es una solicitud de ejemplo:
    ```
    POST /{TENANT_ID}/oauth2/token HTTP/1.1
    Host: login.microsoftonline.com
    Content-Type: application/x-www-form-urlencoded
    grant_type=client_credentials&client_id={CLIENT_ID}&client_secret={CLIENT_SECRET}&reso
    urce=https%3A%2F%2Fmanagement.core.windows.net%2F
    ```
    Este es un ejemplo de respuesta:
    ```
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Content-Length: 1234

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1445395811","not_before":"144
    5391911","resource":"https://management.core.windows.net/","access_token":{ACCESS_TOKEN}}
    ```
     En este ejemplo se incluye la codificación con direcciones URL de los parámetros POST; el valor `resource` es en realidad `https://management.core.windows.net/`. Tenga cuidado también al codificar con dirección URL `{CLIENT_SECRET}` , ya que puede contener caracteres especiales.

     > [!NOTE]
     > Para las pruebas, puede usar una herramienta de cliente HTTP, como [Fiddler](http://www.telerik.com/fiddler) o la extensión [Postman de Chrome](https://chrome.google.com/webstore/detail/postman/fhbjgbiflinjbdggehcddcbncdddomop). 
     > 
     > 
2. Ahora, en cada llamada de API, incluya el encabezado de la solicitud de autorización:
   
        Authorization: Bearer {ACCESS_TOKEN}
   
    Si se le devuelve un código de estado 401, compruebe el cuerpo de la respuesta, ya que puede indicar que el token ha caducado. En ese caso, obtenga un nuevo token.

## <a name="using-the-apis"></a>Uso de las API
Ahora que tiene un token válido, está listo para realizar las llamadas de API.

1. En cada solicitud de API, debe pasar un token válido y no caducado que haya obtenido en la sección anterior.
2. Deberá especificar algunos parámetros en el URI de la solicitud que identifica la aplicación. El URI de la solicitud será similar al siguiente:
   
        https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/
        providers/Microsoft.MobileEngagement/appcollections/{app-collection}/apps/{app-resource-name}/
   
    Para obtener los parámetros, haga clic en el nombre de la aplicación y luego en el Panel; así aparecerá una página similar a la siguiente con los tres parámetros.
   
   * **1**`{subscription-id}`
   * **2**`{app-collection}`
   * **3**`{app-resource-name}`
   * **4** El nombre de su grupo de recursos será **MobileEngagement** a menos que haya creado uno nuevo. 

> [!NOTE]
> <br/>
> 
> 1. Omita la dirección raíz de la API, ya que era para las API anteriores.<br/>
> 2. Si creó la aplicación con Azure Portal, debe usar el nombre del recurso de la aplicación que es diferente del nombre de la propia aplicación. Si creó la aplicación en Azure Portal, debe usar el propio nombre de la aplicación (no hay diferencia entre el nombre de recurso de aplicación y el nombre de aplicación para aplicaciones creadas en el nuevo portal).  
> 
> 

<!-- Images -->
[1]: ./media/mobile-engagement-api-authentication/azure-module.png
[2]: ./media/mobile-engagement-api-authentication/mobile-engagement-api-uri-params.png
[3]: ./media/mobile-engagement-api-authentication/ps-cmdlets.png
[4]: ./media/mobile-engagement-api-authentication/search-app.png



