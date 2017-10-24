---
title: "Cómo usar Managed Service Identity de una máquina virtual de Azure para el inicio de sesión y la adquisición de tokens"
description: "Instrucciones paso a paso para usar una entidad de servicio de MSI de una máquina virtual de Azure para el inicio de sesión y para adquirir un token de acceso."
services: active-directory
documentationcenter: 
author: bryanla
manager: mbaldwin
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/04/2017
ms.author: bryanla
ms.openlocfilehash: 2f6cf4709c77ca1bb051b7d5c9e7d1d5d125c343
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-an-azure-vm-managed-service-identity-msi-for-sign-in-and-token-acquisition"></a>Cómo usar Managed Service Identity (MSI) de una máquina virtual de Azure para el inicio de sesión y la adquisición de tokens 
[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]Después de habilitar MSI en una máquina virtual de Azure, puede usar MSI para el inicio de sesión y para solicitar un token de acceso. En este artículo se muestran varias maneras de utilizar una [entidad de servicio](develop/active-directory-dev-glossary.md#service-principal-object) de MSI para el inicio de sesión y para adquirir un [token de acceso de solo aplicación](develop/active-directory-dev-glossary.md#access-token) para tener acceso a otros recursos, incluidos:

- Inicio de sesión silencioso y desatendido desde PowerShell o la CLI de Azure
- Adquisición de tokens para varios clientes, incluidos .NET, PowerShell, Bash/CURL y REST
- Inicio de sesión con un SDK de Azure, incluidos .NET, Java, Node.js, Python y Ruby

## <a name="prerequisites"></a>Requisitos previos

[!INCLUDE [msi-qs-configure-prereqs](../../includes/msi-qs-configure-prereqs.md)]

Si planea usar los ejemplos de PowerShell de este artículo, asegúrese de instalar [Azure PowerShell versión 4.3.1](https://www.powershellgallery.com/packages/AzureRM) o superior. Si tiene previsto utilizar los ejemplos de la CLI de Azure de este artículo, tiene tres opciones:
- Usar [Azure Cloud Shell](../cloud-shell/overview.md) desde Azure Portal.
- Usar Azure Cloud Shell integrado a través del botón "Pruébelo", situado en la esquina superior derecha de cada bloque de código de la CLI de Azure.
- [Instalar la versión más reciente de la CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.13 o versiones posteriores), si prefiere usar la CLI en un símbolo del sistema local. 


> [!IMPORTANT]
> - En todo el código y scripts de ejemplo de este artículo se da por supuesto que el cliente se ejecuta en una máquina virtual con MSI habilitado. Para más información sobre la habilitación de MSI en una máquina virtual, consulte [Configuración de Managed Service Identity (MSI) en una máquina virtual mediante Azure Portal](msi-qs-configure-portal-windows-vm.md) o uno de los artículos variantes (mediante PowerShell, la CLI, una plantilla o un SDK de Azure). 
> - Para evitar errores de autorización (403/AuthorizationFailed) en los ejemplos de código y scripts, se debe conceder acceso de "Lector" a la identidad de la máquina virtual en el ámbito de la máquina virtual para permitir las operaciones de Azure Resource Manager en la máquina virtual. Consulte [Asignación de un acceso de Managed Service Identity (MSI) a un recurso mediante Azure Portal](msi-howto-assign-access-portal.md) para más detalles.
> - Antes de continuar con las siguientes secciones, use la característica "Conectar" de la máquina virtual en Azure Portal para conectarse remotamente a la máquina virtual con MSI habilitado.

## <a name="how-to-sign-in-from-powershell-or-cli-using-msi"></a>Cómo iniciar sesión desde PowerShell o la CLI mediante MSI

Anteriormente, la ejecución de un script bajo su propia identidad significaba:
- registrarlo como una aplicación de cliente confidencial o web en Azure AD
- iniciar sesión con las credenciales de identificador de cliente y secreto de la aplicación

Con MSI, el cliente del script ya no necesita registrarse en Azure AD ni proporcionar credenciales. En los ejemplos siguientes, se muestra cómo utilizar una entidad de servicio de MSI de la máquina virtual para el inicio de sesión.

### <a name="azure-powershell"></a>Azure PowerShell

El script siguiente muestra cómo:

- adquirir un token de acceso de MSI para una máquina virtual de Azure
- usar el token de acceso para iniciar sesión en Azure AD como la entidad de servicio de MSI correspondiente
- usar la entidad de servicio de MSI para realizar una llamada a Azure Resource Manager, para obtener el identificador de la entidad de servicio

```powershell
# Get an access token from MSI
$response = Invoke-WebRequest -Uri http://localhost:50342/oauth2/token `
                              -Method GET -Body @{resource="https://management.azure.com/"} -Headers @{Metadata="true"}
$content =$response.Content | ConvertFrom-Json
$access_token = $content.access_token

# Use the access token to sign in under the MSI service principal
Login-AzureRmAccount -AccessToken $access_token -AccountId “CLIENT”

# The MSI service principal is now signed in for this session.
# Next, a call to Azure Resource Manager is made to get the service principal ID for the VM's MSI. 
$spID = (Get-AzureRMVM -ResourceGroupName <RESOURCE-GROUP> -Name <VM-NAME>).identity.principalid
echo "The MSI service principal ID is $spID"
```
   
### <a name="azure-cli"></a>CLI de Azure

El script siguiente muestra cómo:

- iniciar sesión en Azure AD como la entidad de servicio de MSI de la máquina virtual
- usar la entidad de servicio de MSI para realizar una llamada a Azure Resource Manager, para obtener el identificador de la entidad de servicio

```azurecli-interactive
az login --msi
spID=$(az resource list -n <VM-NAME> --query [*].identity.principalId --out tsv)
echo The MSI service principal ID is $spID
```

## <a name="how-to-acquire-an-access-token-from-msi"></a>Cómo adquirir un token de acceso de MSI

Con MSI, ya no es necesario registrar su aplicación cliente o script en Azure AD, ni presentar su identificador de cliente y el secreto para obtener un token de acceso. El cliente puede simplemente pedir al punto de conexión local de MSI un token de acceso, sin presentar credenciales de la aplicación. El token de acceso de solo de aplicación se emite para la entidad de servicio de MSI y es adecuado para su uso como un token al portador en [llamadas de servicio a servicio que requieren credenciales de cliente](active-directory-protocols-oauth-service-to-service.md).

En los ejemplos siguientes, se muestra cómo utilizar MSI de la máquina virtual para la adquisición de tokens.

### <a name="net"></a>.NET

> [!IMPORTANT]
> La biblioteca de autenticación de Azure AD (ADAL) no está integrada con MSI. Para obtener un token de acceso mediante MSI, realice una solicitud al punto de conexión local de MSI en una máquina virtual. Para más información, consulte [Preguntas más frecuentes y problemas conocidos de MSI](msi-known-issues.md#frequently-asked-questions-faqs).

```csharp
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

### <a name="azure-powershell-token"></a>Azure PowerShell

```powershell
# Get an access token from MSI
$response = Invoke-WebRequest -Uri http://localhost:50342/oauth2/token `
                              -Method GET -Body @{resource="https://management.azure.com/"} -Headers @{Metadata="true"}
$content =$response.Content | ConvertFrom-Json
$access_token = $content.access_token
echo "The MSI access token is $access_token"
```

### <a name="bashcurl"></a>Bash/CURL

```bash
response=$(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true -s)
access_token=$(echo $response | python -c 'import sys, json; print (json.load(sys.stdin)["access_token"])')
echo The MSI access token is $access_token
```

### <a name="rest"></a>REST

Solicitud de ejemplo:

```
GET http://localhost:50342/oauth2/token?resource=https%3A%2F%2Fmanagement.azure.com%2F HTTP/1.1
Metadata: true
```

| Elemento | Descripción |
| ------- | ----------- |
| `GET` | El verbo HTTP, indicando que se van a recuperar datos desde el punto de conexión. En este caso, el token de acceso de OAuth. | 
| `http://localhost:50342/oauth2/token` | El punto de conexión de MSI, donde 50342 es el puerto predeterminado y es configurable. |
| `resource` | Un parámetro de cadena de consulta, que indica el URI del identificador de aplicación del recurso de destino. También aparece en la notificación `aud` (audiencia) del token emitido. En este ejemplo, se está solicitando un token para tener acceso a Azure Resource Manager, cuyo URI del identificador de aplicación es https://management.azure.com/. |
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

| Elemento | Descripción |
| ------- | ----------- |
| `access_token` | El token de acceso solicitado. Al llamar a una API de REST, el token se inserta en el campo `Authorization` del encabezado de la solicitud como un token al "portador", lo que permite a la API autenticar al llamador. | 
| `refresh_token` | No se utiliza por MSI. |
| `expires_in` | El número de segundos que el token de acceso sigue siendo válido, antes de expirar, desde la hora de emisión. La hora de emisión puede encontrarse en la notificación `iat` del token. |
| `expires_on` | La hora a la que expira el token de acceso. La fecha se representa como el número de segundos desde "1970-01-01T0:0:0Z UTC" (se corresponde con la notificación `exp` del token). |
| `not_before` | El intervalo de tiempo cuando el token de acceso tiene efecto y se puede aceptar. La fecha se representa como el número de segundos desde "1970-01-01T0:0:0Z UTC" (se corresponde con la notificación `nbf` del token). |
| `resource` | El recurso para el que se solicitó el token de acceso, que coincide con el parámetro de la cadena de consulta `resource` de la solicitud. |
| `token_type` | El tipo de token, que es un token de acceso al "Portador", lo que significa que el recurso puede permitir el acceso al portador del token. |

## <a name="how-to-sign-in-with-azure-sdk-libraries-using-msi"></a>Cómo iniciar sesión con las bibliotecas del SDK de Azure mediante MSI

Azure admite varias plataformas de programación a través de una serie de [SDK de Azure](https://azure.microsoft.com/downloads). Varios se han actualizado para admitir el inicio de sesión con MSI y proporcionan los ejemplos correspondientes para mostrar el uso. Esta lista se actualiza a medida que se agrega compatibilidad adicional:

| SDK | Muestra |
| --- | ------ | 
| .NET   | [Administración de recursos desde una máquina virtual con MSI habilitado](https://azure.microsoft.com/resources/samples/aad-dotnet-manage-resources-from-vm-with-msi/) |
| Java   | [Administración de almacenamiento desde una máquina virtual con MSI habilitado](https://azure.microsoft.com/resources/samples/compute-java-manage-resources-from-vm-with-msi-in-aad-group/)|
| Node.js| [Administración de recursos con Managed Service Identity](https://azure.microsoft.com/resources/samples/resources-node-manage-resources-with-msi/) |
| Python | [Uso de MSI para una autenticación simple desde una máquina virtual](https://azure.microsoft.com/resources/samples/resource-manager-python-manage-resources-with-msi/) |
| Ruby   | [Administración de recursos desde una máquina virtual con MSI habilitado](https://azure.microsoft.com/resources/samples/resources-ruby-manage-resources-with-msi/) | 

## <a name="additional-information"></a>Información adicional

### <a name="token-expiration"></a>Expiración del token

El subsistema MSI local almacena en memoria caché los tokens. Por lo tanto, se puede llamar con tanta frecuencia como necesite y solo se realiza una llamada a Azure AD si:
- se produce un error de caché debido a no existir ningún token en la memoria caché
- el token ha expirado

Si almacena el token en el código, debe estar preparado para administrar escenarios en los que el recurso indica que el token ha expirado.

### <a name="resource-ids-for-azure-services"></a>Identificadores de recurso para los servicios de Azure

Consulte [Servicios de Azure que admiten la autenticación de Azure AD](msi-overview.md#azure-services-that-support-azure-ad-authentication) para obtener una lista de servicios que admiten MSI y sus respectivos identificadores de recurso.

## <a name="troubleshooting"></a>Solución de problemas

### <a name="sign-in-or-token-acquisition-fails"></a>Se produce un error en el inicio de sesión o en la adquisición del token

Compruebe que MSI se ha habilitado correctamente. Vuelva a la máquina virtual de Azure en [Azure Portal](https://portal.azure.com) y:

- Buscar en la página "Configuración" y asegurarnos de que MSI habilitado = "Sí".
- Buscar en la página "Extensiones" y asegurarnos de que la extensión MSI se ha implementado correctamente.

Si alguna de las opciones no es correcta, puede que tenga que volver a implementar la identidad de servicio administrada en el recurso nuevo o solucionar el error de implementación.

### <a name="http-request-error-responses"></a>Respuestas de error de solicitud HTTP

- *bad_request_102: encabezado de metadatos necesario no especificado*

  El campo `Metadata` del encabezado de la solicitud no está en la solicitud o tiene un formato incorrecto. El valor debe especificarse como `true`, todo en minúsculas. Consulte "Solicitud de ejemplo" en la [sección REST anterior](#rest) para obtener un ejemplo.

- *desconocido: no se pudo recuperar el token de Active directory. Para obtener detalles, consulte los registros en \<ruta de acceso del archivo\>*

  Compruebe que el identificador URI de la solicitud HTTP GET tiene el formato correcto, especialmente el identificador URI del recurso especificado en la cadena de consulta. Consulte "Solicitud de ejemplo" en la [sección REST anterior](#rest) para obtener un ejemplo o [Servicios de Azure que admiten la autenticación de Azure AD](msi-overview.md#azure-services-that-support-azure-ad-authentication) para obtener una lista de servicios y sus respectivos identificadores de recurso.

- *unknown_source: origen desconocido \<URI\>*

  Compruebe que el identificador URI de la solicitud HTTP GET tiene el formato correcto. La parte `scheme:host/resource-path` debe especificarse como `http://localhost:50342/oauth2/token`. Consulte "Solicitud de ejemplo" en la [sección REST anterior](#rest) para obtener un ejemplo.

## <a name="related-content"></a>Contenido relacionado

- Para obtener información general sobre MSI, consulte [Managed Service Identity overview](msi-overview.md) (Introducción a Managed Service Identity).
- Para habilitar MSI en una máquina virtual de Azure, consulte [Configuración de Managed Service Identity (MSI) de una máquina virtual de Azure con PowerShell](msi-qs-configure-powershell-windows-vm.md).

Use la siguiente sección de comentarios para proporcionar sus opiniones y ayudarnos a afinar y remodelar el contenido.

