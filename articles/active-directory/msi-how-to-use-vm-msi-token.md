---
title: "Uso de una identidad de servicio administrada (MSI) de máquina virtual de Azure para obtener un token de acceso"
description: "Instrucciones paso a paso y ejemplos de uso de una identidad de servicio administrada de máquina virtual de Azure para obtener un token de acceso de OAuth."
services: active-directory
documentationcenter: 
author: bryanla
manager: mtillman
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/01/2017
ms.author: bryanla
ms.openlocfilehash: 6a02b52e7103c9b6e60b09617026fbf6010e76c8
ms.sourcegitcommit: 48fce90a4ec357d2fb89183141610789003993d2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/12/2018
---
# <a name="how-to-use-an-azure-vm-managed-service-identity-msi-for-token-acquisition"></a>Uso de una identidad de servicio administrada de máquina virtual de Azure para obtener tokens 

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]  
En este artículo se proporcionan diversos ejemplos de códigos y scripts para obtener tokens, así como instrucciones sobre temas importantes como el control de errores HTTP y la expiración de tokens.

## <a name="prerequisites"></a>Requisitos previos

[!INCLUDE [msi-qs-configure-prereqs](../../includes/active-directory-msi-qs-configure-prereqs.md)]

Si tiene previsto usar los ejemplos de Azure PowerShell de este artículo, no se olvide de instalar la última versión de [Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM).


> [!IMPORTANT]
> - En todo el código y scripts de ejemplo de este artículo se da por supuesto que el cliente se ejecuta en una máquina virtual con MSI habilitado. Use la característica "Conectar" de la máquina virtual en Azure Portal para conectarse a la máquina virtual de forma remota. Para más información sobre la habilitación de MSI en una máquina virtual, consulte [Configuración de Managed Service Identity (MSI) en una máquina virtual mediante Azure Portal](msi-qs-configure-portal-windows-vm.md) o uno de los artículos variantes (mediante PowerShell, la CLI, una plantilla o un SDK de Azure). 

## <a name="overview"></a>Información general

Una aplicación cliente puede solicitar un [token de acceso de solo aplicación](develop/active-directory-dev-glossary.md#access-token) de MSI para acceder a un recurso determinado. El token [se basa en la entidad de servicio de MSI](msi-overview.md#how-does-it-work). Por lo tanto, no es necesario que el cliente se registre automáticamente para obtener un token de acceso en su propia entidad de servicio. El token es adecuado para utilizarse como un token de portador en [llamadas de servicio a servicio que requieren credenciales de cliente](active-directory-protocols-oauth-service-to-service.md).

|  |  |
| -------------- | -------------------- |
| [Obtención de un token con HTTP](#get-a-token-using-http) | Detalles del protocolo del punto de conexión de token de MSI |
| [Obtención de un token con C#](#get-a-token-using-c) | Ejemplo de cómo utilizar el punto de conexión REST de MSI desde un cliente de C# |
| [Obtención de un token con Go](#get-a-token-using-go) | Ejemplo de cómo utilizar el punto de conexión REST de MSI desde un cliente de Go |
| [Obtención de un token con Azure PowerShell](#get-a-token-using-azure-powershell) | Ejemplo de cómo utilizar el punto de conexión REST de MSI desde un cliente de PowerShell |
| [Obtención de un token con CURL](#get-a-token-using-curl) | Ejemplo de cómo utilizar el punto de conexión REST de MSI desde un cliente de Bash/CURL |
| [Control de la expiración de tokens](#handling-token-expiration) | Instrucciones para controlar los tokens de acceso expirados |
| [Control de errores](#error-handling) | Instrucciones para controlar los errores HTTP devueltos desde el punto de conexión de token de MSI |
| [Identificadores de recurso de servicios de Azure](#resource-ids-for-azure-services) | Dónde obtener identificadores de recurso de los servicios de Azure admitidos |

## <a name="get-a-token-using-http"></a>Obtención de un token con HTTP 

La interfaz básica para obtener un token de acceso se basa en REST, de modo que podrá acceder a cualquier aplicación cliente que se ejecute en la máquina virtual que es capaz de realizar llamadas REST de HTTP. Algo parecido ocurre con el modelo de programación de Azure AD, solo que el cliente usa un punto de conexión de host local de la máquina virtual (y no un punto de conexión de Azure AD).

Solicitud de ejemplo:

```
GET http://localhost:50342/oauth2/token?resource=https%3A%2F%2Fmanagement.azure.com%2F HTTP/1.1
Metadata: true
```

| Elemento | DESCRIPCIÓN |
| ------- | ----------- |
| `GET` | El verbo HTTP, indicando que se van a recuperar datos desde el punto de conexión. En este caso, el token de acceso de OAuth. | 
| `http://localhost:50342/oauth2/token` | El punto de conexión de MSI, donde 50342 es el puerto predeterminado y es configurable. |
| `resource` | Un parámetro de cadena de consulta, que indica el URI del identificador de aplicación del recurso de destino. También aparece en la notificación `aud` (audiencia) del token emitido. En este ejemplo, se solicita un token para acceder a Azure Resource Manager, cuyo URI del identificador de aplicación es https://management.azure.com/. |
| `Metadata` | Un campo de encabezado de la solicitud HTTP, requerido por MSI como mitigación frente a ataques de falsificación de solicitud de lado del servidor (SSRF). Este valor debe establecerse en "true", todo en minúsculas.

Respuesta de ejemplo:

```
HTTP/1.1 200 OK
Content-Type: application/json
{
  "access_token": "eyJ0eXAi...",
  "refresh_token": "",
  "expires_in": "3599",
  "expires_on": "1506484173",
  "not_before": "1506480273",
  "resource": "https://management.azure.com/",
  "token_type": "Bearer"
}
```

| Elemento | DESCRIPCIÓN |
| ------- | ----------- |
| `access_token` | El token de acceso solicitado. Al llamar a una API de REST protegida, el token se inserta en el campo `Authorization` del encabezado de la solicitud como un token de "portador", lo que permite a la API autenticar el llamador. | 
| `refresh_token` | No se utiliza por MSI. |
| `expires_in` | El número de segundos que el token de acceso sigue siendo válido, antes de expirar, desde la hora de emisión. La hora de emisión puede encontrarse en la notificación `iat` del token. |
| `expires_on` | La hora a la que expira el token de acceso. La fecha se representa como el número de segundos desde "1970-01-01T0:0:0Z UTC" (se corresponde con la notificación `exp` del token). |
| `not_before` | El intervalo de tiempo cuando el token de acceso tiene efecto y se puede aceptar. La fecha se representa como el número de segundos desde "1970-01-01T0:0:0Z UTC" (se corresponde con la notificación `nbf` del token). |
| `resource` | El recurso para el que se solicitó el token de acceso, que coincide con el parámetro de la cadena de consulta `resource` de la solicitud. |
| `token_type` | El tipo de token, que es un token de acceso al "Portador", lo que significa que el recurso puede permitir el acceso al portador del token. |

## <a name="get-a-token-using-c"></a>Obtención de un token con C#

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Net;
using System.Web.Script.Serialization; 

// Build request to acquire MSI token
HttpWebRequest request = (HttpWebRequest)WebRequest.Create("http://localhost:50342/oauth2/token?resource=https://management.azure.com/");
request.Headers["Metadata"] = "true";
request.Method = "GET";

try
{
    // Call /token endpoint
    HttpWebResponse response = (HttpWebResponse)request.GetResponse();

    // Pipe response Stream to a StreamReader, and extract access token
    StreamReader streamResponse = new StreamReader(response.GetResponseStream()); 
    string stringResponse = streamResponse.ReadToEnd();
    JavaScriptSerializer j = new JavaScriptSerializer();
    Dictionary<string, string> list = (Dictionary<string, string>) j.Deserialize(stringResponse, typeof(Dictionary<string, string>));
    string accessToken = list["access_token"];
}
catch (Exception e)
{
    string errorText = String.Format("{0} \n\n{1}", e.Message, e.InnerException != null ? e.InnerException.Message : "Acquire token failed");
}

```

## <a name="get-a-token-using-go"></a>Obtención de un token con Go

```
package main

import (
  "fmt"
  "io/ioutil"
  "net/http"
  "net/url"
  "encoding/json"
)

type responseJson struct {
  AccessToken string `json:"access_token"`
  RefreshToken string `json:"refresh_token"`
  ExpiresIn string `json:"expires_in"`
  ExpiresOn string `json:"expires_on"`
  NotBefore string `json:"not_before"`
  Resource string `json:"resource"`
  TokenType string `json:"token_type"`
}

func main() {
    
    // Create HTTP request for MSI token to access Azure Resource Manager
    var msi_endpoint *url.URL
    msi_endpoint, err := url.Parse("http://localhost:50342/oauth2/token")
    if err != nil {
      fmt.Println("Error creating URL: ", err)
      return 
    }
    msi_parameters := url.Values{}
    msi_parameters.Add("resource", "https://management.azure.com/")
    msi_endpoint.RawQuery = msi_parameters.Encode()
    req, err := http.NewRequest("GET", msi_endpoint.String(), nil)
    if err != nil {
      fmt.Println("Error creating HTTP request: ", err)
      return 
    }
    req.Header.Add("Metadata", "true")

    // Call MSI /token endpoint
    client := &http.Client{}
    resp, err := client.Do(req) 
    if err != nil{
      fmt.Println("Error calling token endpoint: ", err)
      return
    }

    // Pull out response body
    responseBytes,err := ioutil.ReadAll(resp.Body)
    defer resp.Body.Close()
    if err != nil {
      fmt.Println("Error reading response body : ", err)
      return
    }

    // Unmarshall response body into struct
    var r responseJson
    err = json.Unmarshal(responseBytes, &r)
    if err != nil {
      fmt.Println("Error unmarshalling the response:", err)
      return
    }

    // Print HTTP response and marshalled response body elements to console
    fmt.Println("Response status:", resp.Status)
    fmt.Println("access_token: ", r.AccessToken)
    fmt.Println("refresh_token: ", r.RefreshToken)
    fmt.Println("expires_in: ", r.ExpiresIn)
    fmt.Println("expires_on: ", r.ExpiresOn)
    fmt.Println("not_before: ", r.NotBefore)
    fmt.Println("resource: ", r.Resource)
    fmt.Println("token_type: ", r.TokenType)
}
```

## <a name="get-a-token-using-azure-powershell"></a>Obtención de un token con Azure PowerShell

En el ejemplo siguiente se muestra cómo utilizar el punto de conexión REST de MSI desde un cliente de PowerShell para las siguientes tareas:

1. Obtener un token de acceso.
2. Usar el token de acceso para llamar a una API de REST de Azure Resource Manager y consultar información sobre la máquina virtual. No olvide sustituir el identificador de suscripción, el nombre del grupo de recursos y el nombre de máquina virtual en `<SUBSCRIPTION-ID>`, `<RESOURCE-GROUP>` y `<VM-NAME>` respectivamente.

```azurepowershell
# Get an access token for the MSI
$response = Invoke-WebRequest -Uri http://localhost:50342/oauth2/token `
                              -Method GET -Body @{resource="https://management.azure.com/"} -Headers @{Metadata="true"}
$content =$response.Content | ConvertFrom-Json
$access_token = $content.access_token
echo "The MSI access token is $access_token"

# Use the access token to get resource information for the VM
$vmInfoRest = (Invoke-WebRequest -Uri https://management.azure.com/subscriptions/<SUBSCRIPTION-ID>/resourceGroups/<RESOURCE-GROUP>/providers/Microsoft.Compute/virtualMachines/<VM-NAME>?api-version=2017-12-01 -Method GET -ContentType "application/json" -Headers @{ Authorization ="Bearer $access_token"}).content
echo "JSON returned from call to get VM info:"
echo $vmInfoRest

```

## <a name="get-a-token-using-curl"></a>Obtención de un token con CURL

```bash
response=$(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true -s)
access_token=$(echo $response | python -c 'import sys, json; print (json.load(sys.stdin)["access_token"])')
echo The MSI access token is $access_token
```

## <a name="handling-token-expiration"></a>Control de la expiración de un token

El subsistema MSI local almacena en memoria caché los tokens. Por lo tanto, se puede llamar con tanta frecuencia como necesite y solo se realiza una llamada a Azure AD si:
- se produce un error de caché debido a no existir ningún token en la memoria caché
- el token ha expirado

Si almacena el token en el código, debe estar preparado para administrar escenarios en los que el recurso indica que el token ha expirado.

## <a name="error-handling"></a>Control de errores 

El punto de conexión de MSI señala los errores a través del campo de código de estado del encabezado del mensaje de respuesta HTTP, por ejemplo, errores 4xx o 5xx:

| Código de estado | Motivo del error | Realización del control |
| ----------- | ------------ | ------------- |
| Error 4xx en la solicitud. | Uno o varios de los parámetros de solicitud eran incorrectos. | No vuelva a intentarlo.  Consulte los detalles del error para obtener más información.  Los errores 4xx son de tiempo de diseño.|
| Error transitorio 5xx del servicio. | El subsistema de MSI o Azure Active Directory devolvió un error transitorio. | Es seguro volver a intentarlo después de esperar al menos 1 segundo.  Si vuelve a intentarlo demasiado pronto o demasiadas veces, Azure AD puede devolver un error de límite de frecuencia (429).|

Si se produce un error, el cuerpo de respuesta HTTP correspondiente contiene los detalles del error en formato JSON:

| Elemento | DESCRIPCIÓN |
| ------- | ----------- |
| error   | Identificador del error. |
| error_description | Descripción detallada del error. **Las descripciones de error pueden cambiar en cualquier momento. No escriba código que cree ramas en función de los valores de la descripción del error.**|

### <a name="http-response-reference"></a>Referencia de la respuesta HTTP

En esta sección se documentan las posibles respuestas de error. Un estado de "200 OK" es una respuesta correcta y el token de acceso se encuentra en el cuerpo de respuesta JSON; en el elemento access_token.

| Código de estado | Error | Descripción del error | Solución |
| ----------- | ----- | ----------------- | -------- |
| 400 - Solicitud incorrecta | invalid_resource | AADSTS50001: la aplicación denominada *\<URI\>* no se encontró en el inquilino llamado *\<TENANT-ID\>*. Esto puede pasar si el administrador del inquilino no es el que ha instalado el administrador del inquilino o no ha recibido el consentimiento de ningún usuario del inquilino. Es posible que haya enviado la solicitud de autenticación al inquilino incorrecto. | (Solo Linux). |
| 400 - Solicitud incorrecta | bad_request_102 | Encabezado de metadatos necesario no especificado. | El campo `Metadata` del encabezado de la solicitud no está en la solicitud o tiene un formato incorrecto. El valor debe especificarse como `true`, todo en minúsculas. Consulte "Solicitud de ejemplo" en la [sección REST anterior](#rest) para obtener un ejemplo.|
| 401 No autorizado | unknown_source | Origen desconocido *\<URI\>*. | Compruebe que el identificador URI de la solicitud HTTP GET tiene el formato correcto. La parte `scheme:host/resource-path` debe especificarse como `http://localhost:50342/oauth2/token`. Consulte "Solicitud de ejemplo" en la [sección REST anterior](#rest) para obtener un ejemplo.|
|           | invalid_request | En la solicitud falta un parámetro necesario, incluye un valor de parámetro no válido o un parámetro repetido, o bien no tiene el formato correcto. |  |
|           | unauthorized_client | El cliente no está autorizado a solicitar un token de acceso con este método. | Esto se debe a que una solicitud no usó el bucle invertido local para llamar a la extensión, o bien que una máquina virtual no tiene una identidad de servicio administrada configurada correctamente. Consulte [Configuración de la identidad de servicio administrada (MSI) de máquina virtual con Azure Portal](msi-qs-configure-portal-windows-vm.md) si necesita ayuda con la configuración de la máquina virtual. |
|           | access_denied | El propietario del recurso o el servidor de consentimiento rechazaron la solicitud. |  |
|           | unsupported_response_type | El servidor de consentimiento no admite la obtención de un token de acceso con este método. |  |
|           | invalid_scope | El ámbito solicitado no es válido, es desconocido o tiene un formato incorrecto. |  |
| 500 Error interno del servidor | unknown | No se pudo recuperar el token de Active Directory. Para obtener información, consulte los registros en *\<ruta de acceso del archivo\>*. | Compruebe que la identidad de servicio administrada se ha habilitado correctamente. Consulte [Configuración de la identidad de servicio administrada (MSI) de máquina virtual con Azure Portal](msi-qs-configure-portal-windows-vm.md) si necesita ayuda con la configuración de la máquina virtual.<br><br>Compruebe que el URI de la solicitud HTTP GET tiene el formato correcto, especialmente el del recurso especificado en la cadena de consulta. Consulte "Solicitud de ejemplo" en la [sección REST anterior](#rest) para obtener un ejemplo o [Servicios de Azure que admiten la autenticación de Azure AD](msi-overview.md#azure-services-that-support-azure-ad-authentication) para obtener una lista de servicios y sus respectivos identificadores de recurso.

## <a name="resource-ids-for-azure-services"></a>Identificadores de recurso para los servicios de Azure

Consulte [Servicios de Azure que admiten la autenticación de Azure AD](msi-overview.md#azure-services-that-support-azure-ad-authentication) para ver una lista de recursos que admite Azure AD y que se han probado con la identidad de servicio administrada, y sus respectivos identificadores de recurso.


## <a name="related-content"></a>Contenido relacionado

- Para habilitar la identidad de servicio administrada en una máquina virtual de Azure, consulte [Configuración de la identidad de servicio administrada (MSI) de máquina virtual con Azure Portal](msi-qs-configure-portal-windows-vm.md).

Use la siguiente sección de comentarios para proporcionar sus opiniones y ayudarnos a afinar y remodelar el contenido.








