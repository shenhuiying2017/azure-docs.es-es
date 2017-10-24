---
title: Enlaces de Microsoft Graph en Azure Functions | Microsoft Docs
description: "Descubra cómo utilizar desencadenadores y enlaces de almacenamiento de Microsoft Graph en Azure Functions."
services: functions
author: mattchenderson
manager: cfowler
editor: 
ms.service: functions
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 09/19/2017
ms.author: mahender
ms.openlocfilehash: 8cf2e4e9e9007549dbdc931b4485c4230c536479
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="azure-functions-microsoft-graph-bindings"></a>Enlaces de Microsoft Graph en Azure Functions
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

En este artículo se explica cómo configurar y trabajar con desencadenadores de Microsoft Graph y enlaces de Azure Functions.
Toda esta información le prepará para usar Azure Functions para trabajar con datos, información y eventos de [Microsoft Graph](https://graph.microsoft.io).

La extensión de Microsoft Graph proporciona los siguientes enlaces:
- Un [enlace de entrada del token de autenticación](#token-input) que le permite interactuar con las API de Microsoft Graph.
- Un [enlace de entrada de la tabla de Excel](#excel-input) que le permite leer datos de Excel.
- Un [enlace de salida de la tabla de Excel](#excel-output) que le permite modificar los datos de Excel.
- Un [enlace de entrada del archivo de OneDrive](#onedrive-input) que le permite leer archivos de OneDrive.
- Un [enlace de salida del archivo de OneDrive](#onedrive-output) que le permite escribir en archivos de OneDrive.
- Un [enlace de salida del mensaje de Outlook](#outlook-output) que le permite enviar correos electrónicos a través de Outlook.
- Una colección de [enlaces y desencadenadores de webhook de Microsoft Graph](#webhooks) que le permite reaccionar a los eventos de Microsoft Graph.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!Note] 
> Los enlaces de Microsoft Graph se encuentran actualmente en versión preliminar.

## <a name="setting-up-the-extensions"></a>Configuración de las extensiones

Los enlaces de Microsoft Graph están disponibles a través de las _extensiones de enlace_. Las extensiones de enlace son componentes opcionales del tiempo de ejecución de Azure Functions. En esta sección se le mostrará cómo configurar las extensiones del token de autenticación y Microsoft Graph.

### <a name="enabling-functions-20-preview"></a>Habilitar la versión preliminar de Functions 2.0

Las extensiones de enlace solo están disponibles para la versión preliminar de Azure Functions 2.0. 

[!INCLUDE [functions-set-runtime-version](../../includes/functions-set-runtime-version.md)]

Para más información, consulte [Cómo seleccionar un destino para versiones en tiempo de ejecución de Azure Functions](functions-versions.md).

### <a name="installing-the-extension"></a>Instalación de la extensión

Para instalar una extensión desde el Azure Portal, deberá navegar a una plantilla o enlace que haga referencia a ella. Cree una nueva función y, en la pantalla de selección de plantillas, elija el escenario de "Microsoft Graph". Seleccione una de las plantillas de este escenario. Como alternativa, puede navegar a la pestaña "Integrar" de una función existente y seleccionar uno de los enlaces que se tratan en este tema.

En ambos casos, aparecerá una advertencia que especifica la extensión que se va a instalar. Haga clic en **Instalar** para obtener la extensión.

> [!Note] 
> Cada extensión solo puede instalarse una vez por cada aplicación de la función. El proceso de instalación en el portal puede tardar hasta 10 minutos en un plan de consumo.

Si utiliza Visual Studio, puede obtener las extensiones mediante la instalación de estos paquetes de NuGet:
- [Microsoft.Azure.WebJobs.Extensions.AuthTokens](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.AuthTokens/)
- [Microsoft.Azure.WebJobs.Extensions.MicrosoftGraph](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.MicrosoftGraph/)

### <a name="configuring-app-service-authentication--authorization"></a>Configuración de la autenticación y la autorización de App Service

Los enlaces que se describen en este tema requieren una identidad para usarlos. Esto permite que Microsoft Graph aplique permisos y audite interacciones. La identidad puede ser el acceso de un usuario a la aplicación o la propia aplicación. Para configurar esta identidad, debe configurar [Autenticación o autorización de App Service](https://docs.microsoft.com/azure/app-service/app-service-authentication-overview) con Azure Active Directory. También deberá solicitar los permisos del recurso que requieren sus funciones.

> [!Note] 
> La extensión de Microsoft Graph solo admite la autenticación de AAD. Los usuarios deben iniciar sesión con una cuenta profesional o educativa.

Si usa Azure Portal, debajo del aviso para instalar la extensión, verá una advertencia que le solicitará configurar Autenticación o autorización de App Service y solicitará cualquier permiso que necesite la plantilla o el enlace. Haga clic en **Configurar AAD ahora** o **Agregar permisos ahora** según corresponda.






<a name="token-input"></a>
## <a name="auth-token-input-binding"></a>Enlace de entrada del token de autenticación

Este enlace obtiene un token de AAD para un recurso determinado y lo proporciona al código como una cadena. El recurso puede ser cualquiera para el que la aplicación tenga permisos. 

### <a name="configuring-an-auth-token-input-binding"></a>Configuración de un enlace de entrada del token de autenticación

El propio enlace no requiere ningún permiso de AAD, pero dependiendo de cómo se utilice el token, puede que necesite solicitar permisos adicionales. Compruebe los requisitos del recurso al que pretenda obtener acceso con el token.

El enlace admite las siguientes propiedades:

|Propiedad|Descripción|
|--------|--------|
|**name**|(Requerido): nombre de la variable que se usa en el código de función para el token de autenticación. Consulte [Uso de un enlace de entrada del token de autenticación desde el código](#token-input-code).|
|**type**|Requerida: se debe establecer en `token`.|
|**dirección**|Requerida: se debe establecer en `in`.|
|**identity**|(Requerido): identidad que se usará para realizar la acción. Puede ser uno de los siguientes valores:<ul><li><code>userFromRequest</code>: solo es válido con el [desencadenador HTTP]. Usa la identidad del usuario que realiza la llamada.</li><li><code>userFromId</code>: usa la identidad de un usuario que inició sesión previamente con el identificador especificado. Consulte la propiedad <code>userId</code>.</li><li><code>userFromToken</code>: usa la identidad representada por el token especificado. Consulte la propiedad <code>userToken</code>.</li><li><code>clientCredentials</code>: usa la identidad de la aplicación de función.</li></ul>|
|**userId** |Necesario si y solo si _identity_ está establecido en `userFromId`. Identificador de una entidad de seguridad de usuario asociado a un usuario que inició sesión previamente.|
|**userToken**|Necesario si y solo si _identity_ está establecido en `userFromToken`. Token válido para la aplicación de función. |
|**resource**|(Requerido): dirección URL de recursos AAD para el que se solicita el token.|

<a name="token-input-code"></a>
### <a name="using-an-auth-token-input-binding-from-code"></a>Uso de un enlace de entrada del token de autenticación desde el código

El token se presenta siempre al código como una cadena.

#### <a name="sample-getting-user-profile-information"></a>Ejemplo: Obtener información de perfiles de usuario

Supongamos que tiene el siguiente archivo function.json que define un desencadenador HTTP con un enlace de entrada de token:

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "type": "token",
      "direction": "in",
      "name": "graphToken",
      "resource": "https://graph.microsoft.com",
      "identity": "userFromRequest"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

El siguiente ejemplo de C# utiliza el token para realizar una llamada HTTP a Microsoft Graph y devuelve el resultado:

```csharp
using System.Net; 
using System.Net.Http; 
using System.Net.Http.Headers; 

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, string graphToken, TraceWriter log)
{
    HttpClient client = new HttpClient();
    client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", graphToken);
    return await client.GetAsync("https://graph.microsoft.com/v1.0/me/");
}
```

El siguiente ejemplo de JS utiliza el token para realizar una llamada HTTP a Microsoft Graph y devuelve el resultado: En el archivo `function.json` anterior, cambie primero `$return` a `res`.

```js
const rp = require('request-promise');

module.exports = function (context, req) {
    let token = "Bearer " + context.bindings.graphToken;

    let options = {
        uri: 'https://graph.microsoft.com/v1.0/me/',
        headers: {
            'Authorization': token
        }
    };
    
    rp(options)
        .then(function(profile) {
            context.res = {
                body: profile
            };
            context.done();
        })
        .catch(function(err) {
            context.res = {
                status: 500,
                body: err
            };
            context.done();
        });
};
```





<a name="excel-input"></a>
## <a name="excel-table-input-binding"></a>Enlace de entrada de la tabla de Excel

Este enlace lee el contenido de una tabla de Excel almacenada en OneDrive.

### <a name="configuring-an-excel-table-input-binding"></a>Configuración de un enlace de entrada de una tabla de Excel

Este enlace requiere los siguientes permisos de AAD:
|Recurso|Permiso|
|--------|--------|
|Microsoft Graph|Leer los archivos de usuario|

El enlace admite las siguientes propiedades:

|Propiedad|Descripción|
|--------|--------|
|**name**|(Requerido): nombre de la variable que se usa en el código de función para la tabla de Excel. Consulte [Uso de un enlace de entrada de la tabla de Excel desde el código](#excel-input-code).|
|**type**|Requerida: se debe establecer en `excel`.|
|**dirección**|Requerida: se debe establecer en `in`.|
|**identity**|(Requerido): identidad que se usará para realizar la acción. Puede ser uno de los siguientes valores:<ul><li><code>userFromRequest</code>: solo es válido con el [desencadenador HTTP]. Usa la identidad del usuario que realiza la llamada.</li><li><code>userFromId</code>: usa la identidad de un usuario que inició sesión previamente con el identificador especificado. Consulte la propiedad <code>userId</code>.</li><li><code>userFromToken</code>: usa la identidad representada por el token especificado. Consulte la propiedad <code>userToken</code>.</li><li><code>clientCredentials</code>: usa la identidad de la aplicación de función.</li></ul>|
|**userId** |Necesario si y solo si _identity_ está establecido en `userFromId`. Identificador de una entidad de seguridad de usuario asociado a un usuario que inició sesión previamente.|
|**userToken**|Necesario si y solo si _identity_ está establecido en `userFromToken`. Token válido para la aplicación de función. |
|**path**|(Requerido): ruta de acceso de OneDrive al libro de Excel.|
|**worksheetName**|Hoja de cálculo en la que se encuentra la tabla.|
|**tableName**|El nombre de la tabla. Si no se especifica, se usará el contenido de la hoja de cálculo.|

<a name="excel-input-code"></a>
### <a name="using-an-excel-table-input-binding-from-code"></a>Uso de un enlace de entrada de la tabla de Excel desde el código

El enlace expone los siguientes tipos de funciones. NET:
- string[][]
- Microsoft.Graph.WorkbookTable
- Tipos de objeto personalizado (con el enlace de modelos estructurales)

#### <a name="sample-reading-an-excel-table"></a>Ejemplo: Leer una tabla de Excel

Supongamos que tiene el siguiente archivo function.json que define un desencadenador HTTP con un enlace de entrada de Excel:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "type": "excel",
      "direction": "in",
      "name": "excelTableData",
      "path": "{query.workbook}",
      "identity": "UserFromRequest",
      "tableName": "{query.table}"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

El siguiente ejemplo de C# lee el contenido de la tabla especificada y lo devuelve al usuario:

```csharp
using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives; 

public static IActionResult Run(HttpRequest req, string[][] excelTableData, TraceWriter log)
{
    return new OkObjectResult(excelTableData);
}
```

El siguiente ejemplo de C# lee el contenido de la tabla especificada y lo devuelve al usuario: En el archivo `function.json` anterior, cambie primero `$return` a `res`.

```js
module.exports = function (context, req) {
    context.res = {
        body: context.bindings.excelTableData
    };
    context.done();
};
```

<a name="excel-output"></a>
## <a name="excel-table-output-binding"></a>Enlace de salida de la tabla de Excel

Este enlace modifica el contenido de una tabla de Excel almacenada en OneDrive.

### <a name="configuring-an-excel-table-output-binding"></a>Configuración de un enlace de salida de una tabla de Excel

Este enlace requiere los siguientes permisos de AAD:
|Recurso|Permiso|
|--------|--------|
|Microsoft Graph|Tener acceso completo a los archivos del usuario|

El enlace admite las siguientes propiedades:

|Propiedad|Descripción|
|--------|--------|
|**name**|(Requerido): nombre de la variable que se usa en el código de función para el token de autenticación. Consulte [Uso de un enlace de salida de la tabla de Excel desde el código](#excel-output-code).|
|**type**|Requerida: se debe establecer en `excel`.|
|**dirección**|Requerida: se debe establecer en `out`.|
|**identity**|(Requerido): identidad que se usará para realizar la acción. Puede ser uno de los siguientes valores:<ul><li><code>userFromRequest</code>: solo es válido con el [desencadenador HTTP]. Usa la identidad del usuario que realiza la llamada.</li><li><code>userFromId</code>: usa la identidad de un usuario que inició sesión previamente con el identificador especificado. Consulte la propiedad <code>userId</code>.</li><li><code>userFromToken</code>: usa la identidad representada por el token especificado. Consulte la propiedad <code>userToken</code>.</li><li><code>clientCredentials</code>: usa la identidad de la aplicación de función.</li></ul>|
|**userId** |Necesario si y solo si _identity_ está establecido en `userFromId`. Identificador de una entidad de seguridad de usuario asociado a un usuario que inició sesión previamente.|
|**userToken**|Necesario si y solo si _identity_ está establecido en `userFromToken`. Token válido para la aplicación de función. |
|**path**|(Requerido): ruta de acceso de OneDrive al libro de Excel.|
|**worksheetName**|Hoja de cálculo en la que se encuentra la tabla.|
|**tableName**|El nombre de la tabla. Si no se especifica, se usará el contenido de la hoja de cálculo.|
|**updateType**|(Requerido): tipo de cambio para realizar en la tabla. Puede ser uno de los siguientes valores:<ul><li><code>update</code>: reemplaza el contenido de la tabla en OneDrive.</li><li><code>append</code>: agrega la carga al final de la tabla de OneDrive mediante la creación de nuevas filas.</li></ul>|

<a name="excel-output-code"></a>
### <a name="using-an-excel-table-output-binding-from-code"></a>Uso de un enlace de salida de la tabla de Excel desde el código

El enlace expone los siguientes tipos de funciones. NET:
- string[][]
- Newtonsoft.Json.Linq.JObject
- Microsoft.Graph.WorkbookTable
- Tipos de objeto personalizado (con el enlace de modelos estructurales)

#### <a name="sample-adding-rows-to-an-excel-table"></a>Ejemplo: Agregar filas a una tabla de Excel

Supongamos que tiene el siguiente archivo function.json que define un desencadenador HTTP con un enlace de salida de Excel:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "newExcelRow",
      "type": "excel",
      "direction": "out",
      "identity": "userFromRequest",
      "updateType": "append",
      "path": "{query.workbook}",
      "tableName": "{query.table}"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```


El siguiente ejemplo de C# agrega una nueva fila a la tabla (se supone que solo tiene una columna) basándose en la entrada desde la cadena de consulta:

```csharp
using System.Net;
using System.Text;

public static async Task Run(HttpRequest req, IAsyncCollector<object> newExcelRow, TraceWriter log)
{
    string input = req.Query
        .FirstOrDefault(q => string.Compare(q.Key, "text", true) == 0)
        .Value;
    await newExcelRow.AddAsync(new {
        Text = input
        // Add other properties for additional columns here
    });
    return;
}
```

El siguiente ejemplo de JS agrega una nueva fila a la tabla (se supone que solo tiene una columna) basándose en la entrada desde la cadena de consulta: En el archivo `function.json` anterior, cambie primero `$return` a `res`.

```js
module.exports = function (context, req) {
    context.bindings.newExcelRow = {
        text: req.query.text
        // Add other properties for additional columns here
    }
    context.done();
};
```




<a name="onedrive-input"></a>
## <a name="onedrive-file-input-binding"></a>Enlace de entrada de archivo de OneDrive

Este enlace lee el contenido de un archivo almacenado en OneDrive.

### <a name="configuring-a-onedrive-file-input-binding"></a>Configuración de un enlace de entrada del archivo de OneDrive

Este enlace requiere los siguientes permisos de AAD:
|Recurso|Permiso|
|--------|--------|
|Microsoft Graph|Leer los archivos de usuario|

El enlace admite las siguientes propiedades:

|Propiedad|Descripción|
|--------|--------|
|**name**|(Requerido): nombre de la variable que se usa en el código de función para el archivo. Consulte [Configuración de un enlace de entrada del archivo de OneDrive](#onedrive-input-code).|
|**type**|Requerida: se debe establecer en `onedrive`.|
|**dirección**|Requerida: se debe establecer en `in`.|
|**identity**|(Requerido): identidad que se usará para realizar la acción. Puede ser uno de los siguientes valores:<ul><li><code>userFromRequest</code>: solo es válido con el [desencadenador HTTP]. Usa la identidad del usuario que realiza la llamada.</li><li><code>userFromId</code>: usa la identidad de un usuario que inició sesión previamente con el identificador especificado. Consulte la propiedad <code>userId</code>.</li><li><code>userFromToken</code>: usa la identidad representada por el token especificado. Consulte la propiedad <code>userToken</code>.</li><li><code>clientCredentials</code>: usa la identidad de la aplicación de función.</li></ul>|
|**userId** |Necesario si y solo si _identity_ está establecido en `userFromId`. Identificador de una entidad de seguridad de usuario asociado a un usuario que inició sesión previamente.|
|**userToken**|Necesario si y solo si _identity_ está establecido en `userFromToken`. Token válido para la aplicación de función. |
|**path**|(Requerido): ruta de acceso de OneDrive al archivo.|

<a name="onedrive-input-code"></a>
### <a name="using-a-onedrive-file-input-binding-from-code"></a>Configuración de un enlace de entrada del archivo de OneDrive

El enlace expone los siguientes tipos de funciones. NET:
- byte[]
- Stream
- cadena
- Microsoft.Graph.DriveItem

#### <a name="sample-reading-a-file-from-onedrive"></a>Ejemplo: Leer un archivo de OneDrive

Supongamos que tiene el siguiente archivo function.json que define un desencadenador HTTP con un enlace de entrada de OneDrive:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "myOneDriveFile",
      "type": "onedrive",
      "direction": "in",
      "path": "{query.filename}",
      "identity": "userFromRequest"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

El siguiente ejemplo de C# lee el archivo especificado en la cadena de consulta y registra su longitud:

```csharp
using System.Net;

public static void Run(HttpRequestMessage req, Stream myOneDriveFile, TraceWriter log)
{
    log.Info(myOneDriveFile.Length.ToString());
}
```

El siguiente ejemplo de JS lee el archivo especificado en la cadena de consulta y devuelve su longitud: En el archivo `function.json` anterior, cambie primero `$return` a `res`.

```js
module.exports = function (context, req) {
    context.res = {
        body: context.bindings.myOneDriveFile.length
    };
    context.done();
};
```


<a name="onedrive-output"></a>
## <a name="onedrive-file-output-binding"></a>Enlace de salida del archivo de OneDrive

Este enlace modifica el contenido de un archivo almacenado en OneDrive.

### <a name="configuring-a-onedrive-file-output-binding"></a>Configuración de un enlace de salida del archivo de OneDrive

Este enlace requiere los siguientes permisos de AAD:
|Recurso|Permiso|
|--------|--------|
|Microsoft Graph|Tener acceso completo a los archivos del usuario|

El enlace admite las siguientes propiedades:

|Propiedad|Descripción|
|--------|--------|
|**name**|(Requerido): nombre de la variable que se usa en el código de función del archivo. Consulte [Uso de un enlace de salida del archivo de OneDrive desde código](#onedrive-output-code).|
|**type**|Requerida: se debe establecer en `onedrive`.|
|**dirección**|Requerida: se debe establecer en `out`.|
|**identity**|(Requerido): identidad que se usará para realizar la acción. Puede ser uno de los siguientes valores:<ul><li><code>userFromRequest</code>: solo es válido con el [desencadenador HTTP]. Usa la identidad del usuario que realiza la llamada.</li><li><code>userFromId</code>: usa la identidad de un usuario que inició sesión previamente con el identificador especificado. Consulte la propiedad <code>userId</code>.</li><li><code>userFromToken</code>: usa la identidad representada por el token especificado. Consulte la propiedad <code>userToken</code>.</li><li><code>clientCredentials</code>: usa la identidad de la aplicación de función.</li></ul>|
|**userId** |Necesario si y solo si _identity_ está establecido en `userFromId`. Identificador de una entidad de seguridad de usuario asociado a un usuario que inició sesión previamente.|
|**userToken**|Necesario si y solo si _identity_ está establecido en `userFromToken`. Token válido para la aplicación de función. |
|**path**|(Requerido): ruta de acceso de OneDrive al archivo.|

<a name="onedrive-output-code"></a>
### <a name="using-a-onedrive-file-output-binding-from-code"></a>Uso de un enlace de salida del archivo de OneDrive desde código

El enlace expone los siguientes tipos de funciones. NET:
- byte[]
- Stream
- cadena
- Microsoft.Graph.DriveItem

#### <a name="sample-writing-to-a-file-in-onedrive"></a>Ejemplo: Escribir un archivo de OneDrive

Supongamos que tiene el siguiente archivo function.json que define un desencadenador HTTP con un enlace de salida de OneDrive:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "myOneDriveFile",
      "type": "onedrive",
      "direction": "out",
      "path": "FunctionsTest.txt",
      "identity": "userFromRequest"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

El siguiente ejemplo de C# obtiene texto de la cadena de consulta y lo escribe en un archivo de texto (FunctionsTest.txt tal como se define en la configuración anterior) en la raíz de OneDrive del llamador:

```csharp
using System.Net;
using System.Text;

public static async Task Run(HttpRequest req, TraceWriter log, Stream myOneDriveFile)
{
    string data = req.Query
        .FirstOrDefault(q => string.Compare(q.Key, "text", true) == 0)
        .Value;
    await myOneDriveFile.WriteAsync(Encoding.UTF8.GetBytes(data), 0, data.Length);
    return;
}
```
El siguiente ejemplo de JS obtiene texto de la cadena de consulta y lo escribe en un archivo de texto (FunctionsTest.txt tal como se define en la configuración anterior) en la raíz de OneDrive del llamador: En el archivo `function.json` anterior, cambie primero `$return` a `res`.

```js
module.exports = function (context, req) {
    context.bindings.myOneDriveFile = req.query.text;
    context.done();
};
```



<a name="outlook-output"></a>
## <a name="outlook-message-output-binding"></a>Enlace de salida del archivo de OneDrive

Envía un mensaje de correo electrónico mediante Outlook.

### <a name="configuring-an-outlook-message-output-binding"></a>Configuración de un enlace de salida del archivo de OneDrive

Este enlace requiere los siguientes permisos de AAD:
|Recurso|Permiso|
|--------|--------|
|Microsoft Graph|Enviar correo como usuario|

El enlace admite las siguientes propiedades:

|Propiedad|Descripción|
|--------|--------|
|**name**|(Requerido): nombre de la variable que se usa en el código de función del mensaje de correo. Consulte [Configuración de un enlace de salida del archivo de OneDrive](#outlook-output-code).|
|**type**|Requerida: se debe establecer en `outlook`.|
|**dirección**|Requerida: se debe establecer en `out`.|
|**identity**|(Requerido): identidad que se usará para realizar la acción. Puede ser uno de los siguientes valores:<ul><li><code>userFromRequest</code>: solo es válido con el [desencadenador HTTP]. Usa la identidad del usuario que realiza la llamada.</li><li><code>userFromId</code>: usa la identidad de un usuario que inició sesión previamente con el identificador especificado. Consulte la propiedad <code>userId</code>.</li><li><code>userFromToken</code>: usa la identidad representada por el token especificado. Consulte la propiedad <code>userToken</code>.</li><li><code>clientCredentials</code>: usa la identidad de la aplicación de función.</li></ul>|
|**userId** |Necesario si y solo si _identity_ está establecido en `userFromId`. Identificador de una entidad de seguridad de usuario asociado a un usuario que inició sesión previamente.|
|**userToken**|Necesario si y solo si _identity_ está establecido en `userFromToken`. Token válido para la aplicación de función. |

<a name="outlook-output-code"></a>
### <a name="using-an-outlook-message-output-binding-from-code"></a>Configuración de un enlace de salida del archivo de OneDrive

El enlace expone los siguientes tipos de funciones. NET:
- Microsoft.Graph.Message
- Newtonsoft.Json.Linq.JObject
- cadena
- Tipos de objeto personalizado (con el enlace de modelos estructurales)

#### <a name="sample-sending-an-email-through-outlook"></a>Ejemplo: Enviar un correo electrónico a través de Outlook

Supongamos que tiene el siguiente archivo function.json que define un desencadenador HTTP con un enlace de salida del mensaje de Outlook:

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "message",
      "type": "outlook",
      "direction": "out",
      "identity": "userFromRequest"
    }
  ],
  "disabled": false
}
```

El siguiente ejemplo de C#, envía un correo desde el llamador a un destinatario especificado en la cadena de consulta:

```csharp
using System.Net;

public static void Run(HttpRequest req, out Message message, TraceWriter log)
{ 
    string emailAddress = req.Query["to"];
    message = new Message(){
        subject = "Greetings",
        body = "Sent from Azure Functions",
        recipient = new Recipient() {
            address = emailAddress
        }
    };
}

public class Message {
    public String subject {get; set;}
    public String body {get; set;}
    public Recipient recipient {get; set;}
}

public class Recipient {
    public String address {get; set;}
    public String name {get; set;}
}
```

El siguiente ejemplo de JS, envía un correo desde el llamador a un destinatario especificado en la cadena de consulta:

```js
module.exports = function (context, req) {
    context.bindings.message = {
        subject: "Greetings",
        body: "Sent from Azure Functions with JavaScript",
        recipient: {
            address: req.query.to 
        } 
    };
    context.done();
};
```





<a name="webhooks"></a>
## <a name="microsoft-graph-webhook-bindings"></a>Enlaces de webhook de Microsoft Graph

Los webhooks le permiten reaccionar antes los eventos de Microsoft Graph. Para admitir webhooks, las funciones son necesarias para crear _suscripciones de weebhook_, actualizarlas y reaccionar ante ellas. Una solución de webhook completa requerirá una combinación de los siguientes enlaces:
- Un [desencadenador de webhook de Microsoft Graph](#webhook-trigger) permite reaccionar ante un webhook de entrada.
- Un [enlace de entrada de la suscripción de webhook de Microsoft Graph](#webhook-input) le permite mostrar las suscripciones existentes y, opcionalmente, actualizarlas.
- Un [enlace de salida de la suscripción de webhook de Microsoft Graph](#webhook-output) le permite crear o eliminar suscripciones de webhook.

Los enlaces en sí mismos no requieren ningún permiso de AAD, pero necesitará solicitar permisos relevantes para el tipo de recurso ante el que va a reaccionar. Para obtener una lista de los permisos necesarios para cada tipo de recurso, consulte [permisos de suscripción](https://developer.microsoft.com/en-us/graph/docs/api-reference/v1.0/api/subscription_post_subscriptions#permissions).

Para obtener más información acerca de los webhooks, consulte [Trabajar con webhooks en Microsoft Graph].





<a name="webhook-trigger"></a>
### <a name="microsoft-graph-webhook-trigger"></a>Desencadenador del webhook de Microsoft Graph

Este desencadenador permite que una función reaccione ante un webhook entrante desde Microsoft Graph. Cada instancia de este desencadenador puede reaccionar a un tipo de recurso de Microsoft Graph.

#### <a name="configuring-a-microsoft-graph-webhook-trigger"></a>Configuración de un desencadenador de webhook de Microsoft Graph

El enlace admite las siguientes propiedades:

|Propiedad|Descripción|
|--------|--------|
|**name**|(Requerido): nombre de la variable que se usa en el código de función del mensaje de correo. Consulte [Configuración de un enlace de salida del archivo de OneDrive](#outlook-output-code).|
|**type**|Requerida: se debe establecer en `graphWebhook`.|
|**dirección**|Requerida: se debe establecer en `trigger`.|
|**resourceType**|(Requerido): recurso de Graph por el que esta función debería responder a webhooks. Puede ser uno de los siguientes valores:<ul><li><code>#Microsoft.Graph.Message</code>: cambios realizados en los mensajes de Outlook.</li><li><code>#Microsoft.Graph.DriveItem</code>: cambios realizados en elementos raíz de OneDrive.</li><li><code>#Microsoft.Graph.Contact - changes made to personal contacts in Outlook.</code></li><li><code>#Microsoft.Graph.Event - changes made to Outlook calendar items.</code></li></ul>|

> [!Note]
> Una aplicación de función solo puede tener una función registrada en un determinado valor `resourceType`.

#### <a name="using-a-microsoft-graph-webhook-trigger-from-code"></a>Uso de un desencadenador de webhook de Microsoft Graph desde código

El enlace expone los siguientes tipos de funciones. NET:
- Tipos de SDK de Microsoft Graph relacionados con el tipo de recurso; p. ej., Microsoft.Graph.Message, Microsoft.Graph.DriveItem
- Tipos de objeto personalizado (con el enlace de modelos estructurales)

Para obtener ejemplos del uso de este enlace en código, consulte [Ejemplos de webhook de Microsoft Graph](#webhook-samples).



<a name="webhook-input"></a>
### <a name="microsoft-graph-webhook-subscription-input-binding"></a>Enlace de entrada de la suscripción del webhook de Microsoft Graph

Este enlace le permite recuperar la lista de suscripciones que administra esta aplicación de función. El enlace se lee desde el almacenamiento de la aplicación de función y no refleja el resto de suscripciones que se crean desde fuera de la aplicación.

#### <a name="configuring-a-microsoft-graph-webhook-subscription-input-binding"></a>Configuración de un enlace de entrada de la suscripción del webhook de Microsoft Graph

El enlace admite las siguientes propiedades:

|Propiedad|Descripción|
|--------|--------|
|**name**|(Requerido): nombre de la variable que se usa en el código de función del mensaje de correo. Consulte [Configuración de un enlace de salida del archivo de OneDrive](#outlook-output-code).|
|**type**|Requerida: se debe establecer en `graphWebhookSubscription`.|
|**dirección**|Requerida: se debe establecer en `in`.|
|**filter**| Si se establece en `userFromRequest`, el enlace solo recuperará las suscripciones propiedad del usuario que realiza la llamada (válido únicamente con [desencadenador HTTP]).| 

#### <a name="using-a-microsoft-graph-webhook-subscription-input-binding-from-code"></a>Uso de un enlace de entrada de la suscripción del webhook de Microsoft Graph desde el código

El enlace expone los siguientes tipos de funciones. NET:
- string[]
- Matrices de tipos de objeto personalizado
- Newtonsoft.Json.Linq.JObject[]
- Microsoft.Graph.Subscription[]

Para obtener ejemplos del uso de este enlace en código, consulte [Ejemplos de webhook de Microsoft Graph](#webhook-samples).


<a name="webhook-output"></a>
### <a name="microsoft-graph-webhook-subscription-output-binding"></a>Enlace de salida de la suscripción del webhook de Microsoft Graph

Este enlace le permite crear, eliminar y actualizar las suscripciones de webhook en Microsoft Graph.

#### <a name="configuring-a-microsoft-graph-webhook-subscription-output-binding"></a>Configuración de un enlace de salida de la suscripción del webhook de Microsoft Graph

El enlace admite las siguientes propiedades:

|Propiedad|Descripción|
|--------|--------|
|**name**|(Requerido): nombre de la variable que se usa en el código de función del mensaje de correo. Consulte [Configuración de un enlace de salida del archivo de OneDrive](#outlook-output-code).|
|**type**|Requerida: se debe establecer en `graphWebhookSubscription`.|
|**dirección**|Requerida: se debe establecer en `out`.|
|**identity**|(Requerido): identidad que se usará para realizar la acción. Puede ser uno de los siguientes valores:<ul><li><code>userFromRequest</code>: solo es válido con el [desencadenador HTTP]. Usa la identidad del usuario que realiza la llamada.</li><li><code>userFromId</code>: usa la identidad de un usuario que inició sesión previamente con el identificador especificado. Consulte la propiedad <code>userId</code>.</li><li><code>userFromToken</code>: usa la identidad representada por el token especificado. Consulte la propiedad <code>userToken</code>.</li><li><code>clientCredentials</code>: usa la identidad de la aplicación de función.</li></ul>|
|**userId** |Necesario si y solo si _identity_ está establecido en `userFromId`. Identificador de una entidad de seguridad de usuario asociado a un usuario que inició sesión previamente.|
|**userToken**|Necesario si y solo si _identity_ está establecido en `userFromToken`. Token válido para la aplicación de función. |
|**action**|(Requerido): especifica la acción que debe realizar el enlace. Puede ser uno de los siguientes valores:<ul><li><code>create</code>: registra una suscripción nueva.</li><li><code>delete</code>: elimina una determinada suscripción.</li><li><code>refresh</code>: actualiza una determinada suscripción para impedir que expire.</li></ul>|
|**subscriptionResource**|Necesario si y solo si _action_ está establecido en `create`. Especifica el recurso de Microsoft Graph que se va a supervisar para los cambios. Consulte [Trabajar con webhooks en Microsoft Graph]. |
|**changeType**|Necesario si y solo si _action_ está establecido en `create`. Indica el tipo de cambio en el recurso suscrito que generará una notificación. Los valores admitidos son: `created`, `updated` y `deleted`. Pueden combinarse varios valores mediante una lista separada por comas.|

#### <a name="using-a-microsoft-graph-webhook-subscription-output-binding-from-code"></a>Uso de un enlace de salida de la suscripción del webhook de Microsoft Graph desde el código

El enlace expone los siguientes tipos de funciones. NET:
- cadena
- Microsoft.Graph.Subscription

Para obtener ejemplos del uso de este enlace en código, consulte [Ejemplos de webhook de Microsoft Graph](#webhook-samples).
 
<a name="webhook-samples"></a>
### <a name="microsoft-graph-webhook-samples"></a>Ejemplos de webhook de Microsoft Graph

#### <a name="sample-creating-a-subscription"></a>Ejemplo: Crear una suscripción

Supongamos que tiene el siguiente archivo function.json que define un desencadenador HTTP con un enlace de salida de suscripción y que usa una acción de creación:

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "type": "graphwebhook",
      "name": "clientState",
      "direction": "out",
      "action": "create",
      "listen": "me/mailFolders('Inbox')/messages",
      "changeTypes": [
        "created"
      ],
      "identity": "userFromRequest"
    },
    {
      "type": "http",
      "name": "$return",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

El ejemplo de C# siguiente registra un webhook que enviará una notificación a esta aplicación de función cuando el usuario que realiza la llamada reciba un mensaje de Outlook:

```csharp
using System;
using System.Net;

public static HttpResponseMessage run(HttpRequestMessage req, out string clientState, TraceWriter log)
{
  log.Info("C# HTTP trigger function processed a request.");
    clientState = Guid.NewGuid().ToString();
    return new HttpResponseMessage(HttpStatusCode.OK);
}
```

El ejemplo de JS siguiente registra un webhook que enviará una notificación a esta aplicación de función cuando el usuario que realiza la llamada reciba un mensaje de Outlook:

```js
const uuidv4 = require('uuid/v4');

module.exports = function (context, req) {
    context.bindings.clientState = uuidv4();
    context.done();
};
```

#### <a name="sample-handling-notifications"></a>Ejemplo: Controlar notificaciones

Supongamos que tiene el siguiente archivo function.json que define un desencadenador del webhook de Graph para controlar mensajes de Outlook:

```json
{
  "bindings": [
    {
      "name": "msg",
      "type": "GraphWebhookTrigger",
      "direction": "in",
      "resourceType": "#Microsoft.Graph.Message"
    }
  ],
  "disabled": false
}
```

El siguiente ejemplo de C# reacciona ante los mensajes de correo entrantes y registra el cuerpo de los enviados por el destinatario que contienen "Azure Functions" en el asunto:

```csharp
#r "Microsoft.Graph"
using Microsoft.Graph;
using System.Net;

public static async Task Run(Message msg, TraceWriter log)  
{
    log.Info("Microsoft Graph webhook trigger function processed a request.");

    // Testable by sending oneself an email with the subject "Azure Functions" and some text body
    if (msg.Subject.Contains("Azure Functions") && msg.From.Equals(msg.Sender)) {
        log.Info($"Processed email: {msg.BodyPreview}");
    }
}
```

El siguiente ejemplo de JS reacciona ante los mensajes de correo entrantes y registra el cuerpo de los enviados por el destinatario que contienen "Azure Functions" en el asunto:

```js
module.exports = function (context) {
    context.log("Microsoft Graph webhook trigger function processed a request.");
    const msg = context.bindings.msg
    // Testable by sending oneself an email with the subject "Azure Functions" and some text body
    if((msg.subject.indexOf("Azure Functions") > -1) && (msg.from === msg.sender) ) {
      context.log(`Processed email: ${msg.bodyPreview}`);
    }
    context.done();
};
```

#### <a name="sample-deleting-subscriptions"></a>Ejemplo: Eliminar suscripciones

Supongamos que tiene el siguiente archivo function.json que define un desencadenador HTTP con un enlace de entrada de suscripción y un enlace de salida de suscripción que usa la acción de eliminación:

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "existingSubscriptions",
      "direction": "in",
      "filter": "userFromRequest"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "subscriptionsToDelete",
      "direction": "out",
      "action": "delete",
      "identity": "userFromRequest"
    },
    {
      "type": "http",
      "name": "res",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

El siguiente ejemplo de C# obtiene todas las suscripciones del usuario que realiza la llamada y, después, las elimina:

```csharp
using System.Net;

public static async Task Run(HttpRequest req, string[] existingSubscriptions, IAsyncCollector<string> subscriptionsToDelete, TraceWriter log)
{
    log.Info("C# HTTP trigger function processed a request.");
    foreach (var subscription in existingSubscriptions)
    {
        log.Info($"Deleting subscription {subscription}");
        await subscriptionsToDelete.AddAsync(subscription);
    }
}
```

El siguiente ejemplo de JS obtiene todas las suscripciones del usuario que realiza la llamada y, después, las elimina:

```js
module.exports = function (context, req) {
    const existing = context.bindings.existingSubscriptions;
    var toDelete = [];
    for (var i = 0; i < existing.length; i++) {
        context.log(`Deleting subscription ${existing[i]}`);
        todelete.push(existing[i]);
    }
    context.bindings.subscriptionsToDelete = toDelete;
    context.done();
};
```

#### <a name="sample-refreshing-subscriptions"></a>Ejemplo: Actualizar suscripciones

Existen dos enfoques para la actualización de las suscripciones:
- Use la identidad de la aplicación para tratar todas las suscripciones. Esto requerirá el consentimiento de un administrador de Azure Active Directory. Esto puede usarse por todos los idiomas admitidos por Azure Functions.
- Use la identidad asociada con cada suscripción enlazando manualmente cada identificador de usuario. Será necesario código personalizado para realizar el enlace. Solo pueden usarlo funciones. NET.

Ambas opciones se muestran a continuación.

**Uso de la identidad de la aplicación**

Supongamos que tiene el siguiente archivo function.json que define un desencadenador de temporizador con un enlace de entrada de suscripción y un enlace de salida de suscripción con la identidad de la aplicación:

```json
{
  "bindings": [
    {
      "name": "myTimer",
      "type": "timerTrigger",
      "direction": "in",
      "schedule": "0 * * */2 * *"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "existingSubscriptions",
      "direction": "in"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "subscriptionsToRefresh",
      "direction": "out",
      "action": "refresh",
      "identity": "clientCredentials"
    }
  ],
  "disabled": false
}
```

El siguiente ejemplo de C# actualiza las suscripciones de un temporizador con la identidad de la aplicación:

```csharp
using System;

public static void Run(TimerInfo myTimer, string[] existingSubscriptions, ICollector<string> subscriptionsToRefresh, TraceWriter log)
{
    // This template uses application permissions and requires consent from an Azure Active Directory admin.
    // See https://go.microsoft.com/fwlink/?linkid=858780
    log.Info($"C# Timer trigger function executed at: {DateTime.Now}");
    foreach (var subscription in existingSubscriptions)
    {
      log.Info($"Refreshing subscription {subscription}");
      subscriptionsToRefresh.Add(subscription);
    }
}
```

El siguiente ejemplo de JS actualiza las suscripciones de un temporizador con la identidad de la aplicación:

```js
// This template uses application permissions and requires consent from an Azure Active Directory admin.
// See https://go.microsoft.com/fwlink/?linkid=858780

module.exports = function (context) {
    const existing = context.bindings.existingSubscriptions;
    var toRefresh = [];
    for (var i = 0; i < existing.length; i++) {
        context.log(`Deleting subscription ${existing[i]}`);
        todelete.push(existing[i]);
    }
    context.bindings.subscriptionsToRefresh = toRefresh;
    context.done();
};
```

**Uso de identidades de usuario dinámicas**

Para la opción alternativa, supongamos que tiene el siguiente archivo function.json que define un desencadenador de temporizador, aplazando el enlace al enlace de entrada de la suscripción al código de la función:

```json
{
  "bindings": [
    {
      "name": "myTimer",
      "type": "timerTrigger",
      "direction": "in",
      "schedule": "0 * * */2 * *"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "existingSubscriptions",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

El siguiente ejemplo de C# actualiza las suscripciones de un temporizador, utilizando la identidad de cada usuario mediante la creación del enlace de salida en el código:
```csharp
using System;

public static async Task Run(TimerInfo myTimer, UserSubscription[] existingSubscriptions, IBinder binder, TraceWriter log)
{
  log.Info($"C# Timer trigger function executed at: {DateTime.Now}");
    foreach (var subscription in existingSubscriptions)
    {
        // binding in code to allow dynamic identity
        using (var subscriptionsToRefresh = await binder.BindAsync<IAsyncCollector<string>>(
            new GraphWebhookSubscriptionAttribute() {
                Action = "refresh",
                Identity = "userFromId",
                UserId = subscription.UserId
            }
        ))
        {
            log.Info($"Refreshing subscription {subscription}");
            await subscriptionsToRefresh.AddAsync(subscription);
        }

    }
}

public class UserSubscription {
    public string UserId {get; set;}
    public string Id {get; set;}
}
```



[desencadenador HTTP]: functions-bindings-http-webhook.md
[Trabajar con webhooks en Microsoft Graph]: https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/webhooks
