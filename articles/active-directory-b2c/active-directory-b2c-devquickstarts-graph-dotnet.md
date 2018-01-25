---
title: 'Uso de Graph API: Azure AD B2C | Microsoft Docs'
description: "Cómo llamar a Graph API para un inquilino de B2C mediante una identidad de aplicación para automatizar el proceso."
services: active-directory-b2c
documentationcenter: .net
author: parakhj
manager: mtillman
editor: parakhj
ms.assetid: f9904516-d9f7-43b1-ae4f-e4d9eb1c67a0
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/07/2017
ms.author: parakhj
ms.openlocfilehash: 33df6c4255d4ca672e65237c8be45b3f0bc7864e
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/24/2018
---
# <a name="azure-ad-b2c-use-the-azure-ad-graph-api"></a>Azure AD B2C: uso de Graph API de Azure AD

>[!NOTE]
> Debe utilizar [Graph API de Azure AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-operations-overview?f=255&MSPPError=-2147217396) para administrar los usuarios de un directorio de Azure AD B2C. Esto es diferente de Microsoft Graph API. Obtenga más información [aquí](https://blogs.msdn.microsoft.com/aadgraphteam/2016/07/08/microsoft-graph-or-azure-ad-graph/).

Los inquilinos de Azure Active Directory (Azure AD) B2C tienden a ser muy grandes. Esto supone que muchas tareas comunes de administración de inquilinos necesitan realizarse mediante programación. Un ejemplo importante es la administración de usuarios. Quizá necesite migrar un almacén de usuario existente a un inquilino de B2C. Puede que desee hospedar un registro de usuarios en su propia página y crear cuentas de usuario en su directorio de Azure AD B2C en segundo plano. Estos tipos de tareas requieren la capacidad de crear, leer, actualizar y eliminar cuentas de usuario. Puede realizar estas tareas mediante Graph API de Azure AD.

Para los inquilinos de B2C, existen dos modos de comunicación principales con Graph API.

* Para las tareas interactivas, que se ejecutan una vez, deberá actuar como una cuenta de administrador en el inquilino de B2C al ejecutar las tareas. Este modo requiere que un administrador inicie sesión con credenciales antes de que este pueda realizar llamadas a Graph API.
* Para las tareas automatizadas y continuas, tiene que usar algún tipo de cuenta de servicio a la que conceda los privilegios necesarios para realizar tareas de administración. En Azure AD, puede hacerlo mediante el registro de una aplicación y la autenticación en Azure AD. Esto se realiza con un **identificador de aplicación** que usa la [concesión de credenciales de cliente OAuth 2.0](../active-directory/develop/active-directory-authentication-scenarios.md#daemon-or-server-application-to-web-api). En este caso, la aplicación actúa como tal, no como un usuario, para llamar a Graph API.

En este artículo, abordaremos cómo realizar este caso de uso automatizado. Para demostrarlo, crearemos un `B2CGraphClient` de .NET 4.5 que realiza operaciones de creación, lectura, actualización y eliminación (CRUD) de usuario. El cliente tendrá una interfaz de línea de comandos (CLI) de Windows que permite invocar diversos métodos. Sin embargo, el código se escribe para que se comporte de forma no interactiva y automatizada.

## <a name="get-an-azure-ad-b2c-tenant"></a>Obtención de un inquilino de Azure AD B2C
Antes de crear aplicaciones, usuarios o interactuar con Azure AD, necesitará un inquilino de Azure AD B2C y una cuenta de administrador global en ese inquilino. Si aún no tiene ningún inquilino, [comience con la introducción a Azure AD B2C](active-directory-b2c-get-started.md).

## <a name="register-your-application-in-your-tenant"></a>Registro de la aplicación en el inquilino
Una vez que tenga un inquilino B2C, debe registrar la aplicación a través de [Azure Portal](https://portal.azure.com).

> [!IMPORTANT]
> Para usar Graph API con el inquilino B2C, debe registrar una aplicación dedicada mediante el menú genérico *Registros de aplicaciones* en Azure Portal, **NO** mediante el menú *Aplicaciones* de Azure AD B2C. No puede volver a usar las aplicaciones B2C ya existentes que registró en el menú *Aplicaciones* de Azure AD B2C.

1. Inicie sesión en el [Azure Portal](https://portal.azure.com).
2. Para elegir el inquilino de Azure AD B2C, seleccione una cuenta en la esquina superior derecha de la página.
3. En el panel de navegación izquierdo, elija **Más servicios**, haga clic en **Registros de aplicaciones**y, luego, en **Agregar**.
4. Siga las indicaciones y cree una nueva aplicación. 
    1. Seleccione **Aplicación web o API** como Tipo de aplicación.    
    2. Proporcione **cualquier URI de redirección** (p. ej., https://B2CGraphAPI), ya que no es relevante para este ejemplo.  
5. La aplicación aparecerá ahora en la lista de aplicaciones. Haga clic en ella para obtener el **Identificador de aplicación** (también conocido como id. de cliente). Cópielo, pues lo necesitará en una sección posterior.
6. En el menú Configuración, haga clic en **Claves** y agregue una nueva clave (también conocida como secreto de cliente). Cópiela también para usarla en una sección posterior.

## <a name="configure-create-read-and-update-permissions-for-your-application"></a>Configuración de permisos de creación, lectura y actualización para la aplicación
Ahora debe configurar la aplicación para obtener todos los permisos necesarios para crear, leer, actualizar y eliminar usuarios.

1. Continuando en el menú Registros de aplicaciones de Azure Portal, seleccione su aplicación.
2. En el menú Configuración, haga clic en **Permisos necesarios**.
3. En la hoja Permisos necesarios, haga clic en **Microsoft Azure Active Directory**.
4. En el menú Habilitar el acceso, seleccione el permiso **Leer y escribir datos de directorio** en **Permisos de la aplicación** y haga clic en **Guardar**.
5. Por último, de nuevo en el menú Permisos necesarios, haga clic en el botón **Conceder permisos**.

Ahora tiene una aplicación con permiso para crear, leer y actualizar usuarios en el inquilino B2C.

> [!NOTE]
> La concesión de permisos puede tardar unos minutos en procesarse completamente.
> 
> 

## <a name="configure-delete-permissions-for-your-application"></a>Configuración de permisos de eliminación para la aplicación
Actualmente, el permiso *Leer y escribir en datos de directorio* **NO** incluye la capacidad de llevar a cabo cualquier eliminación, como la de usuarios. Si desea dotar a la aplicación de la capacidad de eliminar usuarios, deberá seguir estos pasos adicionales que hacen partícipe a PowerShell. De lo contrario, puede ir a la sección siguiente.

En primer lugar, si no se ha instalado previamente, instale el [módulo de PowerShell v1 de Azure AD (MSOnline)](https://docs.microsoft.com/powershell/azure/active-directory/install-msonlinev1?view=azureadps-1.0):

```powershell
Install-Module MSOnline
```

Una vez instalado el módulo de PowerShell, conéctese al inquilino de Azure AD B2C.

> [!IMPORTANT]
> Debe usar una cuenta de administrador de inquilinos B2C que sea **local** para el inquilino B2C. Estas cuentas tienen este aspecto: myusername@myb2ctenant.onmicrosoft.com.

```powershell
Connect-MsolService
```

Ahora usaremos el **Identificador de aplicación** en el script siguiente para asignar a la aplicación el rol de administrador de cuentas de usuario que permitirá a esta eliminar usuarios. Estos roles tienen identificadores conocidos, por lo que lo único que debe hacer es escribir el **Identificador de aplicación** en el siguiente script.

```powershell
$applicationId = "<YOUR_APPLICATION_ID>"
$sp = Get-MsolServicePrincipal -AppPrincipalId $applicationId
Add-MsolRoleMember -RoleObjectId fe930be7-5e62-47db-91af-98c3a49a38b1 -RoleMemberObjectId $sp.ObjectId -RoleMemberType servicePrincipal
```

Ahora la aplicación cuenta con permisos para eliminar usuarios del inquilino B2C.

## <a name="download-configure-and-build-the-sample-code"></a>Descarga, configuración y compilación del código de ejemplo
En primer lugar, descargue el código de ejemplo y ejecútelo. A continuación, lo examinaremos más de cerca.  Puede [descargar el código de ejemplo como archivo .zip](https://github.com/AzureADQuickStarts/B2C-GraphAPI-DotNet/archive/master.zip). También puede clonarlo en un directorio de su elección:

```
git clone https://github.com/AzureADQuickStarts/B2C-GraphAPI-DotNet.git
```

Abra la solución de Visual Studio `B2CGraphClient\B2CGraphClient.sln` en Visual Studio. En el proyecto `B2CGraphClient`, abra el archivo `App.config`. Reemplace las tres configuraciones de la aplicación por sus propios valores:

```
<appSettings>
    <add key="b2c:Tenant" value="{Your Tenant Name}" />
    <add key="b2c:ClientId" value="{The ApplicationID from above}" />
    <add key="b2c:ClientSecret" value="{The Key from above}" />
</appSettings>
```

[!INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

A continuación, haga clic con el botón derecho en la solución `B2CGraphClient` y vuelva a compilar el ejemplo. Si todo es correcto, ahora debería tener un archivo ejecutable `B2C.exe` que se encuentra en `B2CGraphClient\bin\Debug`.

## <a name="build-user-crud-operations-by-using-the-graph-api"></a>Compilación de operaciones CRUD de usuario mediante Graph API
Para usar B2CGraphClient, abra un símbolo del sistema de Windows `cmd` y cambie al directorio `Debug`. A continuación, ejecute el comando `B2C Help` .

```
> cd B2CGraphClient\bin\Debug
> B2C Help
```

Se mostrará una breve descripción de cada comando. Cada vez que invoque uno de estos comandos, `B2CGraphClient` realiza una solicitud a Graph API de Azure AD.

### <a name="get-an-access-token"></a>Obtención de un token de acceso
Cualquier solicitud a Graph API requiere un token de acceso para la autenticación. `B2CGraphClient` utiliza la Biblioteca de autenticación de Active Directory (ADAL) para ayudarle a adquirir tokens de acceso. ADAL facilita la adquisición de tokens al proporcionar una API sencilla y ocuparse de algunos detalles importantes, como el almacenamiento en caché de tokens de acceso. Sin embargo, no tiene que usar ADAL para obtener tokens. También puede obtener tokens elaborando solicitudes HTTP.

> [!NOTE]
> Este ejemplo de código usa ADAL v2 para comunicarse con Graph API.  Debe usar AAL v2 o v3 con el fin de obtener los tokens de acceso que se pueden utilizar con Graph API de Azure AD.
> 
> 

Cuando se ejecuta `B2CGraphClient`, se crea una instancia de la clase `B2CGraphClient`. El constructor para esta clase configura scaffolding de autenticación de ADAL:

```csharp
public B2CGraphClient(string clientId, string clientSecret, string tenant)
{
    // The client_id, client_secret, and tenant are provided in Program.cs, which pulls the values from App.config
    this.clientId = clientId;
    this.clientSecret = clientSecret;
    this.tenant = tenant;

    // The AuthenticationContext is ADAL's primary class, in which you indicate the tenant to use.
    this.authContext = new AuthenticationContext("https://login.microsoftonline.com/" + tenant);

    // The ClientCredential is where you pass in your client_id and client_secret, which are
    // provided to Azure AD in order to receive an access_token by using the app's identity.
    this.credential = new ClientCredential(clientId, clientSecret);
}
```

Vamos a usar el comando `B2C Get-User` como ejemplo. Cuando se invoca `B2C Get-User` sin ninguna entrada adicional, la CLI llama al método `B2CGraphClient.GetAllUsers(...)`. Este método llama a `B2CGraphClient.SendGraphGetRequest(...)`, que envía una solicitud HTTP GET a Graph API. Antes de que `B2CGraphClient.SendGraphGetRequest(...)` envíe la solicitud GET, primero obtiene un token de acceso mediante ADAL:

```csharp
public async Task<string> SendGraphGetRequest(string api, string query)
{
    // First, use ADAL to acquire a token by using the app's identity (the credential)
    // The first parameter is the resource we want an access_token for; in this case, the Graph API.
    AuthenticationResult result = authContext.AcquireToken("https://graph.windows.net", credential);

    ...

```

Puede obtener un token de acceso para Graph API mediante una llamada al método `AuthenticationContext.AcquireToken(...)` de ADAL. ADAL devuelve un `access_token` que representa la identidad de la aplicación.

### <a name="read-users"></a>Lectura de usuarios
Si desea obtener una lista de usuarios o un usuario determinado de Graph API, puede enviar una solicitud HTTP `GET` al punto de conexión `/users`. Una solicitud para todos los usuarios de un inquilino tiene este aspecto:

```
GET https://graph.windows.net/contosob2c.onmicrosoft.com/users?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
```

Para ver esta solicitud, ejecute:

 ```
 > B2C Get-User
 ```

Hay dos aspectos importantes que se deben tener en cuenta:

* El token de acceso adquirido mediante ADAL se agrega al encabezado `Authorization` según el esquema `Bearer`.
* Para los inquilinos de B2C, debe usar el parámetro de consulta `api-version=1.6`.

Estos dos detalles se controlan en el método `B2CGraphClient.SendGraphGetRequest(...)` :

```csharp
public async Task<string> SendGraphGetRequest(string api, string query)
{
    ...

    // For B2C user management, be sure to use the 1.6 Graph API version.
    HttpClient http = new HttpClient();
    string url = "https://graph.windows.net/" + tenant + api + "?" + "api-version=1.6";
    if (!string.IsNullOrEmpty(query))
    {
        url += "&" + query;
    }

    // Append the access token for the Graph API to the Authorization header of the request by using the Bearer scheme.
    HttpRequestMessage request = new HttpRequestMessage(HttpMethod.Get, url);
    request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);
    HttpResponseMessage response = await http.SendAsync(request);

    ...
```

### <a name="create-consumer-user-accounts"></a>Creación de cuentas de usuario consumidor
Al crear cuentas de usuario en el inquilino de B2C, puede enviar una solicitud HTTP `POST` al punto de conexión `/users`:

```
POST https://graph.windows.net/contosob2c.onmicrosoft.com/users?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
Content-Type: application/json
Content-Length: 338

{
    // All of these properties are required to create consumer users.

    "accountEnabled": true,
    "signInNames": [                            // controls which identifier the user uses to sign in to the account
        {
            "type": "emailAddress",             // can be 'emailAddress' or 'userName'
            "value": "joeconsumer@gmail.com"
        }
    ],
    "creationType": "LocalAccount",            // always set to 'LocalAccount'
    "displayName": "Joe Consumer",                // a value that can be used for displaying to the end user
    "mailNickname": "joec",                        // an email alias for the user
    "passwordProfile": {
        "password": "P@ssword!",
        "forceChangePasswordNextLogin": false   // always set to false
    },
    "passwordPolicies": "DisablePasswordExpiration"
}
```

La mayoría de las propiedades de esta solicitud son necesarias para crear usuarios consumidores. Para obtener más información, haga clic [aquí](https://msdn.microsoft.com/library/azure/ad/graph/api/users-operations#CreateLocalAccountUser). Tenga en cuenta que los comentarios `//` se han incluido con fines ilustrativos. No los incluya en una solicitud real.

Para ver la solicitud, ejecute uno de los siguientes comandos:

```
> B2C Create-User ..\..\..\usertemplate-email.json
> B2C Create-User ..\..\..\usertemplate-username.json
```

El comando `Create-User` toma un archivo .json como parámetro de entrada. Contiene una representación JSON de un objeto de usuario. Hay dos archivos .json de ejemplo en el código de ejemplo: `usertemplate-email.json` y `usertemplate-username.json`. Puede modificar estos archivos para satisfacer sus necesidades. Además de los campos obligatorios anteriores, hay varios campos opcionales que puede usar incluidos en estos archivos. Encontrará detalles sobre los campos opcionales en la [referencia de entidad de Graph API de Azure AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#user-entity).

Puede ver cómo se construye la solicitud POST en `B2CGraphClient.SendGraphPostRequest(...)`.

* Adjunta un token de acceso al encabezado `Authorization` de la solicitud.
* Establece `api-version=1.6`.
* Incluye el objeto de usuario JSON en el cuerpo de la solicitud.

> [!NOTE]
> Si las cuentas que se van a migrar desde un almacén de usuario existente tienen menos seguridad de contraseña que la [seguridad de contraseña exigida por Azure AD B2C](https://msdn.microsoft.com/library/azure/jj943764.aspx), puede deshabilitar el requisito de contraseña segura mediante el valor `DisableStrongPassword` de la propiedad `passwordPolicies`. Por ejemplo, puede modificar la solicitud de creación de usuario proporcionada anteriormente de la siguiente manera: `"passwordPolicies": "DisablePasswordExpiration, DisableStrongPassword"`.
> 
> 

### <a name="update-consumer-user-accounts"></a>Actualización de cuentas de usuario consumidor
Al actualizar objetos de usuario, el proceso es similar al que se utiliza para crear objetos de usuario. Sin embargo, este proceso usa el método HTTP `PATCH` :

```
PATCH https://graph.windows.net/contosob2c.onmicrosoft.com/users/<user-object-id>?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
Content-Type: application/json
Content-Length: 37

{
    "displayName": "Joe Consumer",                // this request updates only the user's displayName
}
```

Intente actualizar un usuario mediante la actualización de los archivos JSON con nuevos datos. A continuación, puede usar `B2CGraphClient` para ejecutar uno de estos comandos:

```
> B2C Update-User <user-object-id> ..\..\..\usertemplate-email.json
> B2C Update-User <user-object-id> ..\..\..\usertemplate-username.json
```

Consulte el método `B2CGraphClient.SendGraphPatchRequest(...)` para obtener más información sobre cómo enviar esta solicitud.

### <a name="search-users"></a>Búsqueda de usuarios
Puede buscar usuarios en el inquilino de B2C de dos maneras. Una es con el identificador de objeto del usuario, y otra es con el identificador de inicio de sesión del usuario (es decir, la propiedad `signInNames` ).

Ejecute uno de los comandos siguientes para buscar un usuario específico:

```
> B2C Get-User <user-object-id>
> B2C Get-User <filter-query-expression>
```

Estos son algunos ejemplos:

```
> B2C Get-User 2bcf1067-90b6-4253-9991-7f16449c2d91
> B2C Get-User $filter=signInNames/any(x:x/value%20eq%20%27joeconsumer@gmail.com%27)
```

### <a name="delete-users"></a>Eliminación de usuarios
El proceso para eliminar un usuario es sencillo. Use el método HTTP `DELETE` y construya la dirección URL con el identificador de objeto correcto:

```
DELETE https://graph.windows.net/contosob2c.onmicrosoft.com/users/<user-object-id>?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
```

Para ver un ejemplo, ejecute este comando y vea la solicitud Delete que se imprime en la consola:

```
> B2C Delete-User <object-id-of-user>
```

Consulte el método `B2CGraphClient.SendGraphDeleteRequest(...)` para obtener más información sobre cómo enviar esta solicitud.

Puede realizar muchas otras acciones con Graph API de Azure AD además de la administración de usuarios. La [referencia de Graph API de Azure AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog) proporciona detalles sobre cada acción, junto con solicitudes de ejemplo.

## <a name="use-custom-attributes"></a>Uso de atributos personalizados
La mayoría de las aplicaciones de consumidor necesita almacenar algún tipo de información de perfil de usuario personalizada. Una manera de hacerlo es definiendo un atributo personalizado en el inquilino de B2C. Puede tratar este atributo de la misma manera que trataría cualquier otra propiedad en un objeto de usuario. Puede actualizar el atributo, eliminarlo, consultarlo, enviarlo como notificación en tokens de inicio de sesión, etc.

Para definir un atributo personalizado en el inquilino de B2C, consulte la [referencia de atributos personalizados de B2C](active-directory-b2c-reference-custom-attr.md).

Puede ver los atributos personalizados definidos en el inquilino de B2C mediante `B2CGraphClient`:

```
> B2C Get-B2C-Application
> B2C Get-Extension-Attribute <object-id-in-the-output-of-the-above-command>
```

El resultado de estas funciones muestra los detalles de cada atributo personalizado, como:

```JSON
{
      "odata.type": "Microsoft.DirectoryServices.ExtensionProperty",
      "objectType": "ExtensionProperty",
      "objectId": "cec6391b-204d-42fe-8f7c-89c2b1964fca",
      "deletionTimestamp": null,
      "appDisplayName": "",
      "name": "extension_55dc0861f9a44eb999e0a8a872204adb_Jersey_Number",
      "dataType": "Integer",
      "isSyncedFromOnPremises": false,
      "targetObjects": [
        "User"
      ]
}
```

Puede usar el nombre completo, por ejemplo `extension_55dc0861f9a44eb999e0a8a872204adb_Jersey_Number`, como propiedad en los objetos de usuario.  Actualice el archivo .json con la nueva propiedad, un valor para la propiedad y ejecute:

```
> B2C Update-User <object-id-of-user> <path-to-json-file>
```

Con `B2CGraphClient`, tiene una aplicación de servicio que puede administrar sus usuarios del inquilino de B2C mediante programación. `B2CGraphClient` usa su propia identidad de aplicación para autenticarse en Graph API de Azure AD. También adquiere tokens mediante un secreto de cliente. Según vaya incorporando esta funcionalidad a su aplicación, debe recordar algunos puntos clave para las aplicaciones B2C:

* Tiene que conceder a la aplicación los permisos adecuados en el inquilino.
* Por ahora, debe usar ADAL (no MSAL) para obtener tokens de acceso. (Puede también enviar mensajes de protocolo directamente, sin usar una biblioteca.)
* Cuando llame a Graph API, use `api-version=1.6`.
* Al crear y actualizar los usuarios consumidores, hay algunas propiedades obligatorias, tal como se describió anteriormente.

Si tiene alguna pregunta o desea presentar una solicitud para acciones que le gustaría realizar con Graph API en su inquilino B2C, deje un comentario en este artículo o registre un problema en el repositorio de ejemplos de código de GitHub.

