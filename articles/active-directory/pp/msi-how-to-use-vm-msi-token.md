---
title: "Uso de una identidad de servicio administrada asignada por el usuario para adquirir un token de acceso en una máquina virtual."
description: "Instrucciones detalladas y ejemplos de uso de una identidad de servicio administrada asignada por el usuario de una máquina virtual de Azure para obtener un token de acceso de OAuth."
services: active-directory
documentationcenter: 
author: BryanLa
manager: mtillman
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/22/2017
ms.author: bryanla
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 5c9bf052ecb2e9c79e0eb627a0fd709d587125cd
ms.sourcegitcommit: a648f9d7a502bfbab4cd89c9e25aa03d1a0c412b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/22/2017
---
# <a name="acquire-an-access-token-for-a-vm-user-assigned-managed-service-identity-msi"></a>Obtención de un token de acceso para una identidad de servicio administrada (MSI) asignada por el usuario de la máquina virtual

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)] En este artículo se proporcionan diversos ejemplos de códigos y scripts para obtener tokens, así como instrucciones sobre temas importantes como el control de errores HTTP y la expiración de tokens.

## <a name="prerequisites"></a>requisitos previos

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

Si tiene previsto usar los ejemplos de Azure PowerShell de este artículo, no se olvide de instalar la última versión de [Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM).

> [!IMPORTANT]
> - En todo el código y scripts de ejemplo de este artículo se da por supuesto que el cliente se ejecuta en una máquina virtual con MSI habilitado. Use la característica "Conectar" de la máquina virtual en Azure Portal para conectarse a la máquina virtual de forma remota. Para más información sobre la habilitación de MSI en una máquina virtual, consulte [Configuración de una identidad de servicio administrada (MSI) asignada por el usuario para una máquina virtual con la CLI de Azure](msi-qs-configure-cli-windows-vm.md) o uno de los artículos variantes (mediante Azure Portal, PowerShell, una plantilla o un SDK de Azure). 

## <a name="overview"></a>Información general

Una aplicación cliente puede solicitar un [token de acceso de solo aplicación](~/articles/active-directory/develop/active-directory-dev-glossary.md#access-token) de MSI para acceder a un recurso determinado. El token [se basa en la entidad de servicio de MSI](msi-overview.md#how-does-it-work). Por lo tanto, no es necesario que el cliente se registre automáticamente para obtener un token de acceso en su propia entidad de servicio. El token es adecuado para utilizarse como un token de portador en [llamadas de servicio a servicio que requieren credenciales de cliente](~/articles/active-directory/active-directory-protocols-oauth-service-to-service.md).

|  |  |
| -------------- | -------------------- |
| [Obtención de un token con HTTP](#get-a-token-using-http) | Detalles del protocolo del punto de conexión de token de MSI |
| [Obtención de un token con CURL](#get-a-token-using-curl) | Ejemplo de cómo utilizar el punto de conexión REST de MSI desde un cliente de Bash/CURL |
| [Control de la expiración de tokens](#handling-token-expiration) | Instrucciones para controlar los tokens de acceso expirados |
| [Control de errores](#error-handling) | Instrucciones para controlar los errores HTTP devueltos desde el punto de conexión de token de MSI |
| [Identificadores de recurso de servicios de Azure](#resource-ids-for-azure-services) | Dónde obtener identificadores de recurso de los servicios de Azure admitidos |

## <a name="get-a-token-using-http"></a>Obtención de un token con HTTP 

La interfaz básica para obtener un token de acceso se basa en REST, de modo que podrá acceder a cualquier aplicación cliente que se ejecute en la máquina virtual que es capaz de realizar llamadas REST de HTTP. Algo parecido ocurre con el modelo de programación de Azure AD, solo que el cliente usa un punto de conexión de host local de la máquina virtual (y no un punto de conexión de Azure AD).

Solicitud de ejemplo:

```
GET http://localhost:50342/oauth2/token?resource=https%3A%2F%2Fmanagement.azure.com%2F&client_id=712eac09-e943-418c-9be6-9fd5c91078bl HTTP/1.1
Metadata: true
```

| Elemento | DESCRIPCIÓN |
| ------- | ----------- |
| `GET` | El verbo HTTP, indicando que se van a recuperar datos desde el punto de conexión. En este caso, el token de acceso de OAuth. | 
| `http://localhost:50342/oauth2/token` | El punto de conexión de MSI, donde 50342 es el puerto predeterminado y es configurable. |
| `resource` | Un parámetro de cadena de consulta, que indica el URI del identificador de aplicación del recurso de destino. También aparece en la notificación `aud` (audiencia) del token emitido. En este ejemplo, se solicita un token para acceder a Azure Resource Manager, cuyo URI del identificador de aplicación es https://management.azure.com/. |
| `client_id` | Un parámetro de cadena de consulta, que indica el identificador de cliente (también conocido como Id. de aplicación) de la entidad de servicio que representa la MSI asignada por el usuario. Este valor se devuelve en la propiedad `clientId` durante la creación de una MSI asignada por el usuario. En este ejemplo, se solicita un token para el identificador de cliente "712eac09-e943-418c-9be6-9fd5c91078bl". |
| `Metadata` | Un campo de encabezado de la solicitud HTTP, requerido por MSI como mitigación frente a ataques de falsificación de solicitud de lado del servidor (SSRF). Este valor debe establecerse en "true", todo en minúsculas.

Respuesta de ejemplo:

```
HTTP/1.1 200 OK
Content-Type: application/json
{
  "access_token": "eyJ0eXAi...",
  "expires_in": "3599",
  "expires_on": "1506484173",
  "not_before": "1506480273",
  "resource": "https://management.azure.com/",
  "token_type": "Bearer"
  "client_id":"712eac09-e943-418c-9be6-9fd5c91078bl"
}
```

| Elemento | DESCRIPCIÓN |
| ------- | ----------- |
| `access_token` | El token de acceso solicitado. Al llamar a una API de REST protegida, el token se inserta en el campo `Authorization` del encabezado de la solicitud como un token de "portador", lo que permite a la API autenticar el llamador. | 
| `expires_in` | El número de segundos que el token de acceso sigue siendo válido, antes de expirar, desde la hora de emisión. La hora de emisión puede encontrarse en la notificación `iat` del token. |
| `expires_on` | La hora a la que expira el token de acceso. La fecha se representa como el número de segundos desde "1970-01-01T0:0:0Z UTC" (se corresponde con la notificación `exp` del token). |
| `not_before` | El intervalo de tiempo cuando el token de acceso tiene efecto y se puede aceptar. La fecha se representa como el número de segundos desde "1970-01-01T0:0:0Z UTC" (se corresponde con la notificación `nbf` del token). |
| `resource` | El recurso para el que se solicitó el token de acceso, que coincide con el parámetro de la cadena de consulta `resource` de la solicitud. |
| `token_type` | El tipo de token, que es un token de acceso al "Portador", lo que significa que el recurso puede permitir el acceso al portador del token. |
| `client_id` | El identificador de cliente (también conocido como Id. de aplicación) de la entidad de servicio que representa la MSI asignada por el usuario para la que se solicitó el token. |

## <a name="get-a-token-using-curl"></a>Obtención de un token con CURL

No olvide sustituir el identificador de cliente (también conocido como Id. de aplicación) de la entidad de servicio de la MSI asignada por el usuario para el valor <MSI CLIENT ID> del parámetro `client_id`. Este valor se devuelve en la propiedad `clientId` durante la creación de una MSI asignada por el usuario.

   ```bash
   response=$(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/&client_id=<MSI CLIENT ID>" -H Metadata:true -s)
   access_token=$(echo $response | python -c 'import sys, json; print (json.load(sys.stdin)["access_token"])')
   echo The MSI access token is $access_token
   ```

   Respuestas de ejemplo:

   ```bash
   user@vmLinux:~$ response=$(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/&client_id=9d484c98-b99d-420e-939c-z585174b63bl" -H Metadata:true -s)
   user@vmLinux:~$ access_token=$(echo $response | python -c 'import sys, json; print (json.load(sys.stdin)["access_token"])')
   user@vmLinux:~$ echo The MSI access token is $access_token
   The MSI access token is eyJ0eXAiOiJKV1QiLCJhbGciO...
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
| 400 - Solicitud incorrecta | bad_request_102 | Encabezado de metadatos necesario no especificado. | El campo `Metadata` del encabezado de la solicitud no está en la solicitud o tiene un formato incorrecto. El valor debe especificarse como `true`, todo en minúsculas. Consulte "Solicitud de ejemplo" en la sección [Obtención de un token con HTTP](#get-a-token-using-http) para obtener un ejemplo.|
| 401 No autorizado | unknown_source | Origen desconocido *\<URI\>*. | Compruebe que el identificador URI de la solicitud HTTP GET tiene el formato correcto. La parte `scheme:host/resource-path` debe especificarse como `http://localhost:50342/oauth2/token`. Consulte "Solicitud de ejemplo" en la sección [Obtención de un token con HTTP](#get-a-token-using-http) para obtener un ejemplo.|
|           | invalid_request | En la solicitud falta un parámetro necesario, incluye un valor de parámetro no válido o un parámetro repetido, o bien no tiene el formato correcto. |  |
|           | unauthorized_client | El cliente no está autorizado a solicitar un token de acceso con este método. | Esto se debe a que una solicitud no usó el bucle invertido local para llamar a la extensión, o bien que una máquina virtual no tiene una identidad de servicio administrada configurada correctamente. Consulte [Configuración de la identidad de servicio administrada (MSI) de máquina virtual con Azure Portal](msi-qs-configure-portal-windows-vm.md) si necesita ayuda con la configuración de la máquina virtual. |
|           | access_denied | El propietario del recurso o el servidor de consentimiento rechazaron la solicitud. |  |
|           | unsupported_response_type | El servidor de consentimiento no admite la obtención de un token de acceso con este método. |  |
|           | invalid_scope | El ámbito solicitado no es válido, es desconocido o tiene un formato incorrecto. |  |
| 500 Error interno del servidor | unknown | No se pudo recuperar el token de Active Directory. Para obtener información, consulte los registros en *\<ruta de acceso del archivo\>*. | Compruebe que la identidad de servicio administrada se ha habilitado correctamente. Consulte [Configuración de la identidad de servicio administrada (MSI) de máquina virtual con Azure Portal](msi-qs-configure-portal-windows-vm.md) si necesita ayuda con la configuración de la máquina virtual.<br><br>Compruebe que el URI de la solicitud HTTP GET tiene el formato correcto, especialmente el del recurso especificado en la cadena de consulta. Consulte "Solicitud de ejemplo" en la sección [Obtención de un token con HTTP](#get-a-token-using-http) para obtener un ejemplo o [Servicios de Azure que admiten la autenticación de Azure AD](msi-overview.md#azure-services-that-support-azure-ad-authentication) para obtener una lista de servicios y sus respectivos identificadores de recurso.

## <a name="resource-ids-for-azure-services"></a>Identificadores de recurso para los servicios de Azure

Consulte [Servicios de Azure que admiten la autenticación de Azure AD](msi-overview.md#azure-services-that-support-azure-ad-authentication) para ver una lista de recursos que admite Azure AD y que se han probado con la identidad de servicio administrada, y sus respectivos identificadores de recurso.


## <a name="next-steps"></a>pasos siguientes

- Para configurar una MSI asignada por el usuario en una máquina virtual de Azure, vea [Configuración de una identidad de servicio administrada (MSI) asignada por el usuario para una máquina virtual con la CLI de Azure](msi-qs-configure-cli-windows-vm.md).

Use la siguiente sección de comentarios para proporcionar sus opiniones y ayudarnos a afinar y remodelar el contenido.








