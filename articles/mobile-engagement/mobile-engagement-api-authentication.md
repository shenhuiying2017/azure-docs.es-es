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
ms.openlocfilehash: 574e699a1cfca2caef0cf20872570bbb8650117b
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/23/2018
---
# <a name="authenticate-with-mobile-engagement-rest-apis"></a>Autenticación con las API de REST de Mobile Engagement

## <a name="overview"></a>Información general

En este documento se describe cómo obtener un token válido de OAuth de Azure Active Directory (Azure AD) para autenticarse en las API de REST de Mobile Engagement.

En este procedimiento, se supone que tiene una suscripción válida a Azure y que ha creado una aplicación de Mobile Engagement con uno de los [tutoriales para desarrolladores](mobile-engagement-windows-store-dotnet-get-started.md).

## <a name="authentication"></a>Autenticación

Se usa un token de OAuth basado en Microsoft Azure Active Directory para la autenticación. 

Para autenticar una solicitud de API, es necesario agregar un encabezado de autorización a cada solicitud. Dicho encabezado de autorización debe tener el formato siguiente:

    Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGmJlNmV2ZWJPamg2TTNXR1E...

> [!NOTE]
> Los tokens de Azure Active Directory expiran en una hora.
> 
> 

Existen diferentes formas de obtener un token. Debido a que a las API se las llama desde un servicio en la nube, se recomienda usar una clave de API. En la terminología de Azure, a una clave de API se la conoce como una contraseña de entidad de servicio. El siguiente procedimiento describe un modo de configurarla manualmente.

### <a name="one-time-setup-using-a-script"></a>Instalación única (mediante script)

Para llevar a cabo la instalación mediante un script de PowerShell, siga los pasos que se incluyen en las siguientes instrucciones. Un script de PowerShell requiere poco tiempo para la instalación, pero utiliza los valores predeterminados más permitidos. 

Opcionalmente, también puede seguir las instrucciones de [instalación manual](mobile-engagement-api-authentication-manual.md) para llevar esto a cabo directamente desde Azure Portal. Cuando realiza la configuración desde Azure Portal, puede hacerla más detalladamente.

1. Descargue [aquí](http://aka.ms/webpi-azps) la versión más reciente de Azure PowerShell. Para obtener más información acerca de las instrucciones de descarga, consulte esta [información general](/powershell/azure/overview).

2. Después de instalar Azure PowerShell, use los siguientes comandos para asegurarse de que tiene el **módulo de Azure** instalado:

    a. Asegúrese de que el módulo de Azure PowerShell está disponible en la lista de módulos disponibles.

        Get-Module –ListAvailable

    ![Módulos de Azure disponibles][1]

    b. Si no encuentra el módulo de Azure PowerShell en la lista anterior, deberá ejecutar:

        Import-Module Azure
3. Inicie sesión en Azure Resource Manager desde PowerShell mediante la ejecución del siguiente comando. Proporcione el nombre de usuario y la contraseña de la cuenta de Azure: 

        Login-AzureRmAccount
4. Si tiene varias suscripciones, debe realizar los pasos siguientes:

    a. Obtenga una lista de todas las suscripciones. A continuación, copie el valor **SubscriptionId** de la suscripción que desea usar. Asegúrese de que la suscripción tiene la aplicación Mobile Engagement, ya que esta aplicación se va a usar para interactuar con las API. 

        Get-AzureRmSubscription

    b. Ejecute el siguiente comando. Proporcione el valor de **SubscriptionId** para configurar la suscripción que va a utilizar:

        Select-AzureRmSubscription –SubscriptionId <subscriptionId>
5. Copie el texto para el script [New-AzureRmServicePrincipalOwner.ps1](https://raw.githubusercontent.com/matt-gibbs/azbits/master/src/New-AzureRmServicePrincipalOwner.ps1) en su máquina local. A continuación, guárdelo como un cmdlet de PowerShell (por ejemplo, `APIAuth.ps1`), y ejecútelo.

         `.\APIAuth.ps1`.

6. El script le solicitará que especifique una entrada para **principalName**. Proporcione un nombre adecuado aquí que quiera utilizar para la aplicación de Active Directory (p. ej., APIAuth). 

7. Cuando el script finaliza la ejecución, muestra los siguientes cuatro valores. Asegúrese de copiarlos, ya que los necesitará para autenticarse mediante programación con Active Directory: 

   - **TenantId**
   - **SubscriptionId**
   - **ApplicationId**
   - **Secret**

   Use TenantId como `{TENANT_ID}`, ApplicationId como `{CLIENT_ID}` y Secret como `{CLIENT_SECRET}`.

   > [!NOTE]
   > Es posible que la directiva de seguridad predeterminada le impida ejecutar scripts de PowerShell. Si es así, use el comando siguiente para configurar temporalmente la directiva de ejecución para permitir la ejecución de scripts:
   > 
   > Set-ExecutionPolicy RemoteSigned
8. Este es el aspecto del conjunto de cmdlets de PowerShell.
    ![Cmdlets de PowerShell][3]
9. En Azure Portal, vaya a Active Directory, seleccione **Registros de aplicaciones** y, después, busque la aplicación para asegurarse de que existe.
    ![Buscar su aplicación][4]

### <a name="steps-to-get-a-valid-token"></a>Pasos para obtener un token válido

1. Llame a la API con los parámetros siguientes. Asegúrese de reemplazar **TENANT\_ID**, **CLIENT\_ID** y **CLIENT\_SECRET**:
   
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
     En este ejemplo se incluye la codificación con direcciones URL de los parámetros POST; donde el valor `resource` es en realidad `https://management.core.windows.net/`. Tenga cuidado también al codificar con dirección URL `{CLIENT_SECRET}`, ya que puede contener caracteres especiales.

     > [!NOTE]
     > Para las pruebas, puede usar una herramienta de cliente HTTP, como [Fiddler](http://www.telerik.com/fiddler) o la extensión [Postman de Chrome](https://chrome.google.com/webstore/detail/postman/fhbjgbiflinjbdggehcddcbncdddomop). 
     > 
     > 
2. Ahora, en cada llamada de API, incluya el encabezado de la solicitud de autorización:
   
        Authorization: Bearer {ACCESS_TOKEN}
   
    Si la solicitud devuelve un código de estado 401, compruebe el cuerpo de respuesta. Puede indicar que el token ha caducado. En ese caso, obtenga un nuevo token.

## <a name="use-the-apis"></a>Usar las API
Ahora que tiene un token válido, está listo para realizar las llamadas de API.

1. En cada solicitud de API, debe pasar un token válido y vigente. En la sección anterior, obtuvo el token vigente.

2. Especifique algunos parámetros en el URI de la solicitud que identifica la aplicación. El URI de la solicitud será similar al siguiente código:
   
        https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/
        providers/Microsoft.MobileEngagement/appcollections/{app-collection}/apps/{app-resource-name}/
   
    Para obtener los parámetros, seleccione el nombre de la aplicación. A continuación, seleccione **Panel**. Verá una página con los tres parámetros, como se indica a continuación:
   
   * **1**`{subscription-id}`
   * **2**`{app-collection}`
   * **3**`{app-resource-name}`
   * **4** El nombre de su grupo de recursos será **MobileEngagement** a menos que haya creado uno nuevo. 

> [!NOTE]
> Omita la dirección raíz de la API, ya que era para las API anteriores.
> 
> Si creó la aplicación con Azure Portal, debe usar el nombre del recurso de la aplicación que es diferente del nombre de la propia aplicación. Si creó la aplicación con Azure Portal, deberá utilizar el nombre de la aplicación. (No hay ninguna diferencia entre el nombre de recurso de aplicación y el nombre de la aplicación para las aplicaciones que se crean en el nuevo portal).
> 
> 

<!-- Images -->
[1]: ./media/mobile-engagement-api-authentication/azure-module.png
[2]: ./media/mobile-engagement-api-authentication/mobile-engagement-api-uri-params.png
[3]: ./media/mobile-engagement-api-authentication/ps-cmdlets.png
[4]: ./media/mobile-engagement-api-authentication/search-app.png



