---
title: Enlaces de Microsoft Graph para Azure Functions
description: "Descubra cómo utilizar desencadenadores y enlaces de almacenamiento de Microsoft Graph en Azure Functions."
services: functions
author: mattchenderson
manager: cfowler
editor: 
ms.service: functions
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 12/20/2017
ms.author: mahender
ms.openlocfilehash: 63b94c0a9b77a3f3a6fd394a130bf8f132d51369
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/04/2018
---
# <a name="microsoft-graph-bindings-for-azure-functions"></a>Enlaces de Microsoft Graph para Azure Functions

En este artículo se explica cómo configurar y trabajar con desencadenadores de Microsoft Graph y enlaces de Azure Functions. Toda esta información le prepará para usar Azure Functions para trabajar con datos, información y eventos de [Microsoft Graph](https://graph.microsoft.io).

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

Los enlaces de Microsoft Graph están disponibles a través de las _extensiones de enlace_. Las extensiones de enlace son componentes opcionales del tiempo de ejecución de Azure Functions. En esta sección se muestra cómo configurar las extensiones del token de autenticación y Microsoft Graph.

### <a name="enabling-functions-20-preview"></a>Habilitar la versión preliminar de Functions 2.0

Las extensiones de enlace solo están disponibles para la versión preliminar de Azure Functions 2.0. 

Para información sobre cómo establecer una aplicación de función para usar la versión preliminar 2.0 del entorno de ejecución de Functions, consulte [Selección de destino de la versión 2.0 del runtime](functions-versions.md#target-the-version-20-runtime).

### <a name="installing-the-extension"></a>Instalación de la extensión

Para instalar una extensión desde Azure Portal, navegue a una plantilla o a un enlace que haga referencia a ella. Cree una nueva función y, en la pantalla de selección de plantillas, elija el escenario de "Microsoft Graph". Seleccione una de las plantillas de este escenario. Como alternativa, puede navegar a la pestaña "Integrar" de una función existente y seleccionar uno de los enlaces que se tratan en este artículo.

En ambos casos, aparecerá una advertencia que especifica la extensión que se va a instalar. Haga clic en **Instalar** para obtener la extensión.

> [!Note] 
> Cada extensión solo puede instalarse una vez por cada aplicación de la función. El proceso de instalación en el portal puede tardar hasta 10 minutos en un plan de consumo.

Si utiliza Visual Studio, puede obtener las extensiones mediante la instalación de estos paquetes de NuGet:
- [Microsoft.Azure.WebJobs.Extensions.AuthTokens](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.AuthTokens/)
- [Microsoft.Azure.WebJobs.Extensions.MicrosoftGraph](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.MicrosoftGraph/)

### <a name="configuring-authentication--authorization"></a>Configuración de autenticación o autorización

Los enlaces que se describen en este artículo requieren una identidad para usarlos. Esto permite que Microsoft Graph aplique permisos y audite interacciones. La identidad puede ser el acceso de un usuario a la aplicación o la propia aplicación. Para configurar esta identidad, configure [Autenticación o autorización de App Service](https://docs.microsoft.com/azure/app-service/app-service-authentication-overview) con Azure Active Directory. También deberá solicitar los permisos del recurso que requieren sus funciones.

> [!Note] 
> La extensión de Microsoft Graph solo admite la autenticación de Azure AD. Los usuarios deben iniciar sesión con una cuenta profesional o educativa.

Si usa Azure Portal, verá una advertencia debajo del mensaje para instalar la extensión. La advertencia le pedirá configurar la autenticación o autorización de App Service y solicitar cualquier permiso que requieran la plantilla o el enlace. Haga clic en **Configurar Azure AD ahora** o **Agregar permisos ahora** según corresponda.



<a name="token-input"></a>
## <a name="auth-token"></a>Token de autenticación

Este enlace de entrada del token de autenticación obtiene un token de Azure AD para un recurso determinado y lo proporciona al código como una cadena. El recurso puede ser cualquiera para el que la aplicación tenga permisos. 

Esta sección contiene las siguientes subsecciones:

* [Ejemplo](#auth-token---example)
* [Atributos](#auth-token---attributes)
* [Configuración](#auth-token---configuration)
* [Uso](#auth-token---usage)

### <a name="auth-token---example"></a>Token de autenticación: ejemplo

Vea el ejemplo específico del lenguaje:

* [Script de C# (.csx)](#auth-token---c-script-example)
* [JavaScript](#auth-token---javascript-example)

#### <a name="auth-token---c-script-example"></a>Token de autenticación: ejemplo de script de C#

En el ejemplo siguiente se obtiene información sobre el perfil de usuario.

El archivo *function.json* define un desencadenador HTTP con un enlace de entrada de token:

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

El código de script de C# usa el token para realizar una llamada HTTP a Microsoft Graph y devuelve el resultado:

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

#### <a name="auth-token---javascript-example"></a>Token de autenticación: ejemplo de JavaScript

En el ejemplo siguiente se obtiene información sobre el perfil de usuario.

El archivo *function.json* define un desencadenador HTTP con un enlace de entrada de token:

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
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

El código de JavaScript usa el token parea realizar una llamada HTTP a Microsoft Graph y devuelve el resultado.

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

### <a name="auth-token---attributes"></a>Token de autenticación: atributos

Para [bibliotecas de clases de C#](functions-dotnet-class-library.md), use el atributo [Token](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/TokenBinding/TokenAttribute.cs), que se define en el paquete NuGet [Microsoft.Azure.WebJobs.Extensions.AuthTokens](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.AuthTokens/).

### <a name="auth-token---configuration"></a>Token de autenticación: configuración

En la siguiente tabla se explican las propiedades de configuración de enlace que se definen en el archivo *function.json* y el atributo `Token`.

|Propiedad de function.json | Propiedad de atributo |DESCRIPCIÓN|
|---------|---------|----------------------|
|**name**||(Requerido): nombre de la variable que se usa en el código de función para el token de autenticación. Consulte [Uso de un enlace de entrada del token de autenticación desde el código](#token-input-code).|
|**type**||Requerida: se debe establecer en `token`.|
|**dirección**||Requerida: se debe establecer en `in`.|
|**identity**|**Identidad**|(Requerido): identidad que se usará para realizar la acción. Puede ser uno de los siguientes valores:<ul><li><code>userFromRequest</code>: solo es válido con el [desencadenador HTTP]. Usa la identidad del usuario que realiza la llamada.</li><li><code>userFromId</code>: usa la identidad de un usuario que inició sesión previamente con el identificador especificado. Consulte la propiedad <code>userId</code>.</li><li><code>userFromToken</code>: usa la identidad representada por el token especificado. Consulte la propiedad <code>userToken</code>.</li><li><code>clientCredentials</code>: usa la identidad de la aplicación de función.</li></ul>|
|**userId**|**UserId**  |Necesario si y solo si _identity_ está establecido en `userFromId`. Identificador de una entidad de seguridad de usuario asociado a un usuario que inició sesión previamente.|
|**userToken**|**UserToken**|Necesario si y solo si _identity_ está establecido en `userFromToken`. Token válido para la aplicación de función. |
|**Recurso**|**resource**|Requerido: dirección URL de recursos de Azure AD para la que se solicita el token.|

<a name="token-input-code"></a>
### <a name="auth-token---usage"></a>Token de autenticación: uso

El enlace mismo no requiere ningún permiso de Azure AD, pero, según cómo se use el token, puede que necesite solicitar permisos adicionales. Compruebe los requisitos del recurso al que pretenda obtener acceso con el token.

El token se presenta siempre al código como una cadena.




<a name="excel-input"></a>
## <a name="excel-input"></a>Entrada de Excel

El enlace de entrada de la tabla de Excel lee el contenido de una tabla almacenada en OneDrive.

Esta sección contiene las siguientes subsecciones:

* [Ejemplo](#excel-input---example)
* [Atributos](#excel-input---attributes)
* [Configuración](#excel-input---configuration)
* [Uso](#excel-input---usage)

### <a name="excel-input---example"></a>Entrada de Excel: ejemplo

Vea el ejemplo específico del lenguaje:

* [Script de C# (.csx)](#excel-input---c-script-example)
* [JavaScript](#excel-input---javascript-example)

#### <a name="excel-input---c-script-example"></a>Entrada de Excel: ejemplo de script de C#

El archivo *function.json* siguiente define un desencadenador HTTP con un enlace de entrada de Excel:

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

El código de script de C# siguiente lee el contenido de la tabla especificada y lo devuelve al usuario:

```csharp
using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives; 

public static IActionResult Run(HttpRequest req, string[][] excelTableData, TraceWriter log)
{
    return new OkObjectResult(excelTableData);
}
```

#### <a name="excel-input---javascript-example"></a>Entrada de Excel: ejemplo de JavaScript

El archivo *function.json* siguiente define un desencadenador HTTP con un enlace de entrada de Excel:

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
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

El código de script de JavaScript siguiente lee el contenido de la tabla especificada y lo devuelve al usuario.

```js
module.exports = function (context, req) {
    context.res = {
        body: context.bindings.excelTableData
    };
    context.done();
};
```

### <a name="excel-input---attributes"></a>Entrada de Excel: atributos

Para [bibliotecas de clases de C#](functions-dotnet-class-library.md), use el atributo [Excel](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/ExcelAttribute.cs), que se define en el paquete NuGet [Microsoft.Azure.WebJobs.Extensions.MicrosoftGraph](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.MicrosoftGraph/).

### <a name="excel-input---configuration"></a>Entrada de Excel: configuración

En la siguiente tabla se explican las propiedades de configuración de enlace que se definen en el archivo *function.json* y el atributo `Excel`.

|Propiedad de function.json | Propiedad de atributo |DESCRIPCIÓN|
|---------|---------|----------------------|
|**name**||(Requerido): nombre de la variable que se usa en el código de función para la tabla de Excel. Consulte [Uso de un enlace de entrada de la tabla de Excel desde el código](#excel-input-code).|
|**type**||Requerida: se debe establecer en `excel`.|
|**dirección**||Requerida: se debe establecer en `in`.|
|**identity**|**Identidad**|(Requerido): identidad que se usará para realizar la acción. Puede ser uno de los siguientes valores:<ul><li><code>userFromRequest</code>: solo es válido con el [desencadenador HTTP]. Usa la identidad del usuario que realiza la llamada.</li><li><code>userFromId</code>: usa la identidad de un usuario que inició sesión previamente con el identificador especificado. Consulte la propiedad <code>userId</code>.</li><li><code>userFromToken</code>: usa la identidad representada por el token especificado. Consulte la propiedad <code>userToken</code>.</li><li><code>clientCredentials</code>: usa la identidad de la aplicación de función.</li></ul>|
|**userId**|**UserId**  |Necesario si y solo si _identity_ está establecido en `userFromId`. Identificador de una entidad de seguridad de usuario asociado a un usuario que inició sesión previamente.|
|**userToken**|**UserToken**|Necesario si y solo si _identity_ está establecido en `userFromToken`. Token válido para la aplicación de función. |
|**path**|**Ruta de acceso**|(Requerido): ruta de acceso de OneDrive al libro de Excel.|
|**worksheetName**|**WorksheetName**|Hoja de cálculo en la que se encuentra la tabla.|
|**tableName**|**TableName**|El nombre de la tabla. Si no se especifica, se usará el contenido de la hoja de cálculo.|

<a name="excel-input-code"></a>
### <a name="excel-input---usage"></a>Entrada de Excel: uso

Este enlace requiere los siguientes permisos de Azure AD:
|Recurso|Permiso|
|--------|--------|
|Microsoft Graph|Leer los archivos de usuario|

El enlace expone los siguientes tipos de funciones. NET:
- string[][]
- Microsoft.Graph.WorkbookTable
- Tipos de objeto personalizado (con el enlace de modelos estructurales)










<a name="excel-output"></a>
## <a name="excel-output"></a>Salida de Excel

Este enlace de salida de Excel modifica el contenido de una tabla de Excel almacenada en OneDrive.

Esta sección contiene las siguientes subsecciones:

* [Ejemplo](#excel-output---example)
* [Atributos](#excel-output---attributes)
* [Configuración](#excel-output---configuration)
* [Uso](#excel-output---usage)

### <a name="excel-output---example"></a>Salida de Excel: ejemplo

Vea el ejemplo específico del lenguaje:

* [Script de C# (.csx)](#excel-output---c-script-example)
* [JavaScript](#excel-output---javascript-example)

#### <a name="excel-output---c-script-example"></a>Salida de Excel: ejemplo de script de C#

En el ejemplo siguiente se agregan filas a una tabla de Excel.

El archivo *function.json* define un desencadenador HTTP con un enlace de salida de Excel:

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

El código de script de C# agrega una nueva fila a la tabla (se supone que solo tiene una columna) basándose en la entrada desde la cadena de consulta:

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

#### <a name="excel-output---javascript-example"></a>Salida de Excel: ejemplo de JavaScript

En el ejemplo siguiente se agregan filas a una tabla de Excel.

El archivo *function.json* define un desencadenador HTTP con un enlace de salida de Excel:

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
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

El código de JavaScript siguiente agrega una nueva fila a la tabla (se supone que solo tiene una columna) basándose en la entrada desde la cadena de consulta.

```js
module.exports = function (context, req) {
    context.bindings.newExcelRow = {
        text: req.query.text
        // Add other properties for additional columns here
    }
    context.done();
};
```

### <a name="excel-output---attributes"></a>Salida de Excel: atributos

Para [bibliotecas de clases de C#](functions-dotnet-class-library.md), use el atributo [Excel](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/ExcelAttribute.cs), que se define en el paquete NuGet [Microsoft.Azure.WebJobs.Extensions.MicrosoftGraph](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.MicrosoftGraph/).

### <a name="excel-output---configuration"></a>Salida de Excel: configuración

En la siguiente tabla se explican las propiedades de configuración de enlace que se definen en el archivo *function.json* y el atributo `Excel`.

|Propiedad de function.json | Propiedad de atributo |DESCRIPCIÓN|
|---------|---------|----------------------|
|**name**||(Requerido): nombre de la variable que se usa en el código de función para el token de autenticación. Consulte [Uso de un enlace de salida de la tabla de Excel desde el código](#excel-output-code).|
|**type**||Requerida: se debe establecer en `excel`.|
|**dirección**||Requerida: se debe establecer en `out`.|
|**identity**|**Identidad**|(Requerido): identidad que se usará para realizar la acción. Puede ser uno de los siguientes valores:<ul><li><code>userFromRequest</code>: solo es válido con el [desencadenador HTTP]. Usa la identidad del usuario que realiza la llamada.</li><li><code>userFromId</code>: usa la identidad de un usuario que inició sesión previamente con el identificador especificado. Consulte la propiedad <code>userId</code>.</li><li><code>userFromToken</code>: usa la identidad representada por el token especificado. Consulte la propiedad <code>userToken</code>.</li><li><code>clientCredentials</code>: usa la identidad de la aplicación de función.</li></ul>|
|**UserId** |**userId** |Necesario si y solo si _identity_ está establecido en `userFromId`. Identificador de una entidad de seguridad de usuario asociado a un usuario que inició sesión previamente.|
|**userToken**|**UserToken**|Necesario si y solo si _identity_ está establecido en `userFromToken`. Token válido para la aplicación de función. |
|**path**|**Ruta de acceso**|(Requerido): ruta de acceso de OneDrive al libro de Excel.|
|**worksheetName**|**WorksheetName**|Hoja de cálculo en la que se encuentra la tabla.|
|**tableName**|**TableName**|El nombre de la tabla. Si no se especifica, se usará el contenido de la hoja de cálculo.|
|**updateType**|**UpdateType**|(Requerido): tipo de cambio para realizar en la tabla. Puede ser uno de los siguientes valores:<ul><li><code>update</code>: reemplaza el contenido de la tabla en OneDrive.</li><li><code>append</code>: agrega la carga al final de la tabla de OneDrive mediante la creación de nuevas filas.</li></ul>|

<a name="excel-output-code"></a>
### <a name="excel-output---usage"></a>Salida de Excel: uso

Este enlace requiere los siguientes permisos de Azure AD:
|Recurso|Permiso|
|--------|--------|
|Microsoft Graph|Tener acceso completo a los archivos del usuario|

El enlace expone los siguientes tipos de funciones. NET:
- string[][]
- Newtonsoft.Json.Linq.JObject
- Microsoft.Graph.WorkbookTable
- Tipos de objeto personalizado (con el enlace de modelos estructurales)





<a name="onedrive-input"></a>
## <a name="file-input"></a>Entrada de archivo

El enlace de entrada de archivo de OneDrive lee el contenido de un archivo almacenado en OneDrive.

Esta sección contiene las siguientes subsecciones:

* [Ejemplo](#file-input---example)
* [Atributos](#file-input---attributes)
* [Configuración](#file-input---configuration)
* [Uso](#file-input---usage)

### <a name="file-input---example"></a>Entrada de archivo: ejemplo

Vea el ejemplo específico del lenguaje:

* [Script de C# (.csx)](#file-input---c-script-example)
* [JavaScript](#file-input---javascript-example)

#### <a name="file-input---c-script-example"></a>Entrada de archivo: ejemplo de script de C#

El ejemplo siguiente lee un archivo que se almacena en OneDrive.

El archivo *function.json* define un desencadenador HTTP con un enlace de entrada de archivo de OneDrive:

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

El código de script de C# lee el archivo especificado en la cadena de consulta y registra su longitud:

```csharp
using System.Net;

public static void Run(HttpRequestMessage req, Stream myOneDriveFile, TraceWriter log)
{
    log.Info(myOneDriveFile.Length.ToString());
}
```

#### <a name="file-input---javascript-example"></a>Entrada de archivo: ejemplo de JavaScript

El ejemplo siguiente lee un archivo que se almacena en OneDrive.

El archivo *function.json* define un desencadenador HTTP con un enlace de entrada de archivo de OneDrive:

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
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

El código de JavaScript siguiente lee el archivo especificado en la cadena de consulta y devuelve su longitud.

```js
module.exports = function (context, req) {
    context.res = {
        body: context.bindings.myOneDriveFile.length
    };
    context.done();
};
```

### <a name="file-input---attributes"></a>Entrada de archivo: atributos

Para [bibliotecas de clases de C#](functions-dotnet-class-library.md), use el atributo [OneDrive](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/OneDriveAttribute.cs), que se define en el paquete NuGet [Microsoft.Azure.WebJobs.Extensions.MicrosoftGraph](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.MicrosoftGraph/).

### <a name="file-input---configuration"></a>Entrada de archivo: configuración

En la siguiente tabla se explican las propiedades de configuración de enlace que se definen en el archivo *function.json* y el atributo `OneDrive`.

|Propiedad de function.json | Propiedad de atributo |DESCRIPCIÓN|
|---------|---------|----------------------|
|**name**||(Requerido): nombre de la variable que se usa en el código de función para el archivo. Consulte [Configuración de un enlace de entrada del archivo de OneDrive](#onedrive-input-code).|
|**type**||Requerida: se debe establecer en `onedrive`.|
|**dirección**||Requerida: se debe establecer en `in`.|
|**identity**|**Identidad**|(Requerido): identidad que se usará para realizar la acción. Puede ser uno de los siguientes valores:<ul><li><code>userFromRequest</code>: solo es válido con el [desencadenador HTTP]. Usa la identidad del usuario que realiza la llamada.</li><li><code>userFromId</code>: usa la identidad de un usuario que inició sesión previamente con el identificador especificado. Consulte la propiedad <code>userId</code>.</li><li><code>userFromToken</code>: usa la identidad representada por el token especificado. Consulte la propiedad <code>userToken</code>.</li><li><code>clientCredentials</code>: usa la identidad de la aplicación de función.</li></ul>|
|**userId**|**UserId**  |Necesario si y solo si _identity_ está establecido en `userFromId`. Identificador de una entidad de seguridad de usuario asociado a un usuario que inició sesión previamente.|
|**userToken**|**UserToken**|Necesario si y solo si _identity_ está establecido en `userFromToken`. Token válido para la aplicación de función. |
|**path**|**Ruta de acceso**|(Requerido): ruta de acceso de OneDrive al archivo.|

<a name="onedrive-input-code"></a>
### <a name="file-input---usage"></a>Entrada de archivo: uso

Este enlace requiere los siguientes permisos de Azure AD:
|Recurso|Permiso|
|--------|--------|
|Microsoft Graph|Leer los archivos de usuario|

El enlace expone los siguientes tipos de funciones. NET:
- byte[]
- Stream
- cadena
- Microsoft.Graph.DriveItem






<a name="onedrive-output"></a>
## <a name="file-output"></a>Salida de archivo

El enlace de salida de archivo de OneDrive modifica el contenido de un archivo almacenado en OneDrive.

Esta sección contiene las siguientes subsecciones:

* [Ejemplo](#file-output---example)
* [Atributos](#file-output---attributes)
* [Configuración](#file-output---configuration)
* [Uso](#file-output---usage)

### <a name="file-output---example"></a>Salida de archivo: ejemplo

Vea el ejemplo específico del lenguaje:

* [Script de C# (.csx)](#file-output---c-script-example)
* [JavaScript](#file-output---javascript-example)

#### <a name="file-output---c-script-example"></a>Salida de archivo: ejemplo de script de C#

El ejemplo siguiente escribe en un archivo que se almacena en OneDrive.

El archivo *function.json* define un desencadenador HTTP con un enlace de salida de OneDrive:

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

El código de script de C# obtiene texto de la cadena de consulta y lo escribe en un archivo de texto (FunctionsText.txt tal como se define en el ejemplo anterior) en la raíz de OneDrive del llamador:

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

#### <a name="file-output---javascript-example"></a>Salida de archivo: ejemplo de JavaScript

El ejemplo siguiente escribe en un archivo que se almacena en OneDrive.

El archivo *function.json* define un desencadenador HTTP con un enlace de salida de OneDrive:

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
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

El código de JavaScript obtiene texto de la cadena de consulta y lo escribe en un archivo de texto (FunctionsTest.txt tal como se define en la configuración anterior) en la raíz de OneDrive del llamador.

```js
module.exports = function (context, req) {
    context.bindings.myOneDriveFile = req.query.text;
    context.done();
};
```

### <a name="file-output---attributes"></a>Salida de archivo: atributos

Para [bibliotecas de clases de C#](functions-dotnet-class-library.md), use el atributo [OneDrive](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/OneDriveAttribute.cs), que se define en el paquete NuGet [Microsoft.Azure.WebJobs.Extensions.MicrosoftGraph](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.MicrosoftGraph/).

### <a name="file-output---configuration"></a>Salida de archivo: configuración

En la siguiente tabla se explican las propiedades de configuración de enlace que se definen en el archivo *function.json* y el atributo `OneDrive`.

|Propiedad de function.json | Propiedad de atributo |DESCRIPCIÓN|
|---------|---------|----------------------|
|**name**||(Requerido): nombre de la variable que se usa en el código de función del archivo. Consulte [Uso de un enlace de salida del archivo de OneDrive desde código](#onedrive-output-code).|
|**type**||Requerida: se debe establecer en `onedrive`.|
|**dirección**||Requerida: se debe establecer en `out`.|
|**identity**|**Identidad**|(Requerido): identidad que se usará para realizar la acción. Puede ser uno de los siguientes valores:<ul><li><code>userFromRequest</code>: solo es válido con el [desencadenador HTTP]. Usa la identidad del usuario que realiza la llamada.</li><li><code>userFromId</code>: usa la identidad de un usuario que inició sesión previamente con el identificador especificado. Consulte la propiedad <code>userId</code>.</li><li><code>userFromToken</code>: usa la identidad representada por el token especificado. Consulte la propiedad <code>userToken</code>.</li><li><code>clientCredentials</code>: usa la identidad de la aplicación de función.</li></ul>|
|**UserId** |**userId** |Necesario si y solo si _identity_ está establecido en `userFromId`. Identificador de una entidad de seguridad de usuario asociado a un usuario que inició sesión previamente.|
|**userToken**|**UserToken**|Necesario si y solo si _identity_ está establecido en `userFromToken`. Token válido para la aplicación de función. |
|**path**|**Ruta de acceso**|(Requerido): ruta de acceso de OneDrive al archivo.|

<a name="onedrive-output-code"></a>
#### <a name="file-output---usage"></a>Salida de archivo: uso

Este enlace requiere los siguientes permisos de Azure AD:
|Recurso|Permiso|
|--------|--------|
|Microsoft Graph|Tener acceso completo a los archivos del usuario|

El enlace expone los siguientes tipos de funciones. NET:
- byte[]
- Stream
- cadena
- Microsoft.Graph.DriveItem





<a name="outlook-output"></a>
## <a name="outlook-output"></a>Salida de Outlook

El enlace de salida de mensajes de Outlook envía un mensaje de correo electrónico a través de Outlook.

Esta sección contiene las siguientes subsecciones:

* [Ejemplo](#outlook-output---example)
* [Atributos](#outlook-output---attributes)
* [Configuración](#outlook-output---configuration)
* [Uso](#outlook-outnput---usage)

### <a name="outlook-output---example"></a>Salida de Outlook: ejemplo

Vea el ejemplo específico del lenguaje:

* [Script de C# (.csx)](#outlook-output---c-script-example)
* [JavaScript](#outlook-output---javascript-example)

#### <a name="outlook-output---c-script-example"></a>Salida de Outlook: ejemplo de script de C#

El ejemplo siguiente envía un correo electrónico a través de Outlook.

El archivo *function.json* define un desencadenador HTTP con un enlace de salida de mensajes de Outlook:

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

El código de script de C# envía un correo desde el llamador a un destinatario especificado en la cadena de consulta:

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

#### <a name="outlook-output---javascript-example"></a>Salida de Outlook: ejemplo de JavaScript

El ejemplo siguiente envía un correo electrónico a través de Outlook.

El archivo *function.json* define un desencadenador HTTP con un enlace de salida de mensajes de Outlook:

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

El código de script de JavaScript envía un correo desde el llamador a un destinatario especificado en la cadena de consulta:

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

### <a name="outlook-output---attributes"></a>Salida de Outlook: atributos

Para [bibliotecas de clases de C#](functions-dotnet-class-library.md), use el atributo [Outlook](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/OutlookAttribute.cs), que se define en el paquete NuGet [Microsoft.Azure.WebJobs.Extensions.MicrosoftGraph](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.MicrosoftGraph/).

### <a name="outlook-output---configuration"></a>Salida de Outlook: configuración

En la siguiente tabla se explican las propiedades de configuración de enlace que se definen en el archivo *function.json* y el atributo `Outlook`.

|Propiedad de function.json | Propiedad de atributo |DESCRIPCIÓN|
|---------|---------|----------------------|
|**name**||(Requerido): nombre de la variable que se usa en el código de función del mensaje de correo. Consulte [Configuración de un enlace de salida del archivo de OneDrive](#outlook-output-code).|
|**type**||Requerida: se debe establecer en `outlook`.|
|**dirección**||Requerida: se debe establecer en `out`.|
|**identity**|**Identidad**|(Requerido): identidad que se usará para realizar la acción. Puede ser uno de los siguientes valores:<ul><li><code>userFromRequest</code>: solo es válido con el [desencadenador HTTP]. Usa la identidad del usuario que realiza la llamada.</li><li><code>userFromId</code>: usa la identidad de un usuario que inició sesión previamente con el identificador especificado. Consulte la propiedad <code>userId</code>.</li><li><code>userFromToken</code>: usa la identidad representada por el token especificado. Consulte la propiedad <code>userToken</code>.</li><li><code>clientCredentials</code>: usa la identidad de la aplicación de función.</li></ul>|
|**userId**|**UserId**  |Necesario si y solo si _identity_ está establecido en `userFromId`. Identificador de una entidad de seguridad de usuario asociado a un usuario que inició sesión previamente.|
|**userToken**|**UserToken**|Necesario si y solo si _identity_ está establecido en `userFromToken`. Token válido para la aplicación de función. |

<a name="outlook-output-code"></a>
### <a name="outlook-output---usage"></a>Salida de Outlook: uso

Este enlace requiere los siguientes permisos de Azure AD:
|Recurso|Permiso|
|--------|--------|
|Microsoft Graph|Enviar correo como usuario|

El enlace expone los siguientes tipos de funciones. NET:
- Microsoft.Graph.Message
- Newtonsoft.Json.Linq.JObject
- cadena
- Tipos de objeto personalizado (con el enlace de modelos estructurales)






## <a name="webhooks"></a>Webhooks

Los webhooks le permiten reaccionar antes los eventos de Microsoft Graph. Para admitir webhooks, las funciones son necesarias para crear _suscripciones de weebhook_, actualizarlas y reaccionar ante ellas. Una solución de webhook completa requiere una combinación de los enlaces siguientes:
- Un [desencadenador de webhook de Microsoft Graph](#webhook-trigger) permite reaccionar ante un webhook de entrada.
- Un [enlace de entrada de la suscripción de webhook de Microsoft Graph](#webhook-input) le permite mostrar las suscripciones existentes y, opcionalmente, actualizarlas.
- Un [enlace de salida de la suscripción de webhook de Microsoft Graph](#webhook-output) le permite crear o eliminar suscripciones de webhook.

Los enlaces en sí mismos no requieren ningún permiso de Azure AD, pero es necesario solicitar permisos relevantes para el tipo de recurso ante el que va a reaccionar. Para obtener una lista de los permisos necesarios para cada tipo de recurso, consulte [permisos de suscripción](https://developer.microsoft.com/graph/docs/api-reference/v1.0/api/subscription_post_subscriptions#permissions).

Para más información sobre los webhooks, consulte [Trabajar con webhooks en Microsoft Graph].





## <a name="webhook-trigger"></a>Desencadenador de webhook

El desencadenador de webhook de Microsoft Graph permite que una función reaccione ante un webhook entrante desde Microsoft Graph. Cada instancia de este desencadenador puede reaccionar a un tipo de recurso de Microsoft Graph.

Esta sección contiene las siguientes subsecciones:

* [Ejemplo](#webhook-trigger---example)
* [Atributos](#webhook-trigger---attributes)
* [Configuración](#webhook-trigger---configuration)
* [Uso](#webhook-trigger---usage)

### <a name="webhook-trigger---example"></a>Desencadenador de webhook: ejemplo

Vea el ejemplo específico del lenguaje:

* [Script de C# (.csx)](#webhook-trigger---c-script-example)
* [JavaScript](#webhook-trigger---javascript-example)

#### <a name="webhook-trigger---c-script-example"></a>Desencadenador de webhook: ejemplo de script de C#

En el ejemplo siguiente se controlan los webhooks para los mensajes de Outlook entrantes. Para usar un desencadenador de webhook, debe [crear una suscripción](#webhook-output---example) y puede [actualizar la suscripción](#webhook-subscription-refresh) para evitar que expire.

El archivo *function.json* define un desencadenador de webhook:

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

El código de script de C# reacciona ante los mensajes de correo entrantes y registra el cuerpo de los enviados por el destinatario que contienen "Azure Functions" en el asunto:

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

#### <a name="webhook-trigger---javascript-example"></a>Desencadenador de webhook: ejemplo de JavaScript

En el ejemplo siguiente se controlan los webhooks para los mensajes de Outlook entrantes. Para usar un desencadenador de webhook, debe [crear una suscripción](#webhook-output---example) y puede [actualizar la suscripción](#webhook-subscription-refresh) para evitar que expire.

El archivo *function.json* define un desencadenador de webhook:

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

El código de JavaScript reacciona ante los mensajes de correo entrantes y registra el cuerpo de los enviados por el destinatario que contienen "Azure Functions" en el asunto:

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

### <a name="webhook-trigger---attributes"></a>Desencadenador de webhook: atributos

Para [bibliotecas de clases de C#](functions-dotnet-class-library.md), use el atributo [GraphWebHookTrigger](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/GraphWebHookTriggerAttribute.cs), que se define en el paquete NuGet [Microsoft.Azure.WebJobs.Extensions.MicrosoftGraph](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.MicrosoftGraph/).

### <a name="webhook-trigger---configuration"></a>Desencadenador de webhook: configuración

En la siguiente tabla se explican las propiedades de configuración de enlace que se definen en el archivo *function.json* y el atributo `GraphWebHookTrigger`.

|Propiedad de function.json | Propiedad de atributo |DESCRIPCIÓN|
|---------|---------|----------------------|
|**name**||(Requerido): nombre de la variable que se usa en el código de función del mensaje de correo. Consulte [Configuración de un enlace de salida del archivo de OneDrive](#outlook-output-code).|
|**type**||Requerida: se debe establecer en `graphWebhook`.|
|**dirección**||Requerida: se debe establecer en `trigger`.|
|**resourceType**|**ResourceType**|(Requerido): recurso de Graph por el que esta función debería responder a webhooks. Puede ser uno de los siguientes valores:<ul><li><code>#Microsoft.Graph.Message</code>: cambios realizados en los mensajes de Outlook.</li><li><code>#Microsoft.Graph.DriveItem</code>: cambios realizados en elementos raíz de OneDrive.</li><li><code>#Microsoft.Graph.Contact</code> : cambios realizados en los contactos personales de Outlook.</li><li><code>#Microsoft.Graph.Event</code>: cambios realizados en los elementos de calendario de Outlook.</li></ul>|

> [!Note]
> Una aplicación de función solo puede tener una función registrada en un determinado valor `resourceType`.

### <a name="webhook-trigger---usage"></a>Desencadenador de webhook: uso

El enlace expone los siguientes tipos de funciones. NET:
- Tipos de SDK de Microsoft Graph relacionados con el tipo de recurso, como `Microsoft.Graph.Message` o `Microsoft.Graph.DriveItem`.
- Tipos de objeto personalizado (con el enlace de modelos estructurales)




<a name="webhook-input"></a>
## <a name="webhook-input"></a>Entrada de webhook

El enlace de entrada de webhook de Microsoft Graph le permite recuperar la lista de suscripciones que administra esta aplicación de función. El enlace lee desde el almacenamiento de la aplicación de función, por lo que no refleja el resto de suscripciones que se crean desde fuera de la aplicación.

Esta sección contiene las siguientes subsecciones:

* [Ejemplo](#webhook-input---example)
* [Atributos](#webhook-input---attributes)
* [Configuración](#webhook-input---configuration)
* [Uso](#webhook-input---usage)

### <a name="webhook-input---example"></a>Entrada de webhook: ejemplo

Vea el ejemplo específico del lenguaje:

* [Script de C# (.csx)](#webhook-input---c-script-example)
* [JavaScript](#webhook-input---javascript-example)

#### <a name="webhook-input---c-script-example"></a>Entrada de webhook: ejemplo de script de C#

El ejemplo siguiente obtiene todas las suscripciones del usuario que realiza la llamada y las elimina.

El archivo *function.json* define un desencadenador HTTP con un enlace de entrada de suscripción y un enlace de salida de suscripción que usa la acción de eliminación:

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

El código de script de C# obtiene las suscripciones y las elimina:

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

#### <a name="webhook-input---javascript-example"></a>Entrada de webhook: ejemplo de JavaScript

El ejemplo siguiente obtiene todas las suscripciones del usuario que realiza la llamada y las elimina.

El archivo *function.json* define un desencadenador HTTP con un enlace de entrada de suscripción y un enlace de salida de suscripción que usa la acción de eliminación:

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

El código de JavaScript obtiene las suscripciones y las elimina:

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

### <a name="webhook-input---attributes"></a>Entrada de webhook: atributos

Para [bibliotecas de clases de C#](functions-dotnet-class-library.md), use el atributo [GraphWebHookSubscription](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/GraphWebHookSubscriptionAttribute.cs), que se define en el paquete NuGet [Microsoft.Azure.WebJobs.Extensions.MicrosoftGraph](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.MicrosoftGraph/).

### <a name="webhook-input---configuration"></a>Entrada de webhook: configuración

En la siguiente tabla se explican las propiedades de configuración de enlace que se definen en el archivo *function.json* y el atributo `GraphWebHookSubscription`.

|Propiedad de function.json | Propiedad de atributo |DESCRIPCIÓN|
|---------|---------|----------------------|
|**name**||(Requerido): nombre de la variable que se usa en el código de función del mensaje de correo. Consulte [Configuración de un enlace de salida del archivo de OneDrive](#outlook-output-code).|
|**type**||Requerida: se debe establecer en `graphWebhookSubscription`.|
|**dirección**||Requerida: se debe establecer en `in`.|
|**filter**|**Filter**| Si se establece en `userFromRequest`, el enlace solo recuperará las suscripciones propiedad del usuario que realiza la llamada (válido únicamente con [desencadenador HTTP]).| 

### <a name="webhook-input---usage"></a>Entrada de webhook: uso

El enlace expone los siguientes tipos de funciones. NET:
- string[]
- Matrices de tipos de objeto personalizado
- Newtonsoft.Json.Linq.JObject[]
- Microsoft.Graph.Subscription[]





## <a name="webhook-output"></a>Salida de webhook

El enlace de salida de suscripción de webhook le permite crear, eliminar y actualizar las suscripciones de webhook en Microsoft Graph.

Esta sección contiene las siguientes subsecciones:

* [Ejemplo](#webhook-output---example)
* [Atributos](#webhook-output---attributes)
* [Configuración](#webhook-output---configuration)
* [Uso](#webhook-output---usage)

### <a name="webhook-output---example"></a>Salida de webhook: ejemplo

Vea el ejemplo específico del lenguaje:

* [Script de C# (.csx)](#webhook-output---c-script-example)
* [JavaScript](#webhook-output---javascript-example)

#### <a name="webhook-output---c-script-example"></a>Salida de webhook: ejemplo de script de C#

El ejemplo siguiente crea una suscripción. Puede [actualizar la suscripción](#webhook-subscription-refresh) para evitar que expire.

El archivo *function.json* define un desencadenador HTTP con un enlace de salida de suscripción y que usa una acción de creación:

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

El código de script de C# siguiente registra un webhook que enviará una notificación a esta aplicación de función cuando el usuario que realiza la llamada reciba un mensaje de Outlook:

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

#### <a name="webhook-output---javascript-example"></a>Salida de webhook: ejemplo de JavaScript

El ejemplo siguiente crea una suscripción. Puede [actualizar la suscripción](#webhook-subscription-refresh) para evitar que expire.

El archivo *function.json* define un desencadenador HTTP con un enlace de salida de suscripción y que usa una acción de creación:

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

El código de JavaScript registra un webhook que enviará una notificación a esta aplicación de función cuando el usuario que realiza la llamada reciba un mensaje de Outlook:

```js
const uuidv4 = require('uuid/v4');

module.exports = function (context, req) {
    context.bindings.clientState = uuidv4();
    context.done();
};
```

### <a name="webhook-output---attributes"></a>Salida de webhook: atributos

Para [bibliotecas de clases de C#](functions-dotnet-class-library.md), use el atributo [GraphWebHookSubscription](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/GraphWebHookSubscriptionAttribute.cs), que se define en el paquete NuGet [Microsoft.Azure.WebJobs.Extensions.MicrosoftGraph](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.MicrosoftGraph/).

### <a name="webhook-output---configuration"></a>Salida de webhook: configuración

En la siguiente tabla se explican las propiedades de configuración de enlace que se definen en el archivo *function.json* y el atributo `GraphWebHookSubscription`.

|Propiedad de function.json | Propiedad de atributo |DESCRIPCIÓN|
|---------|---------|----------------------|
|**name**||(Requerido): nombre de la variable que se usa en el código de función del mensaje de correo. Consulte [Configuración de un enlace de salida del archivo de OneDrive](#outlook-output-code).|
|**type**||Requerida: se debe establecer en `graphWebhookSubscription`.|
|**dirección**||Requerida: se debe establecer en `out`.|
|**identity**|**Identidad**|(Requerido): identidad que se usará para realizar la acción. Puede ser uno de los siguientes valores:<ul><li><code>userFromRequest</code>: solo es válido con el [desencadenador HTTP]. Usa la identidad del usuario que realiza la llamada.</li><li><code>userFromId</code>: usa la identidad de un usuario que inició sesión previamente con el identificador especificado. Consulte la propiedad <code>userId</code>.</li><li><code>userFromToken</code>: usa la identidad representada por el token especificado. Consulte la propiedad <code>userToken</code>.</li><li><code>clientCredentials</code>: usa la identidad de la aplicación de función.</li></ul>|
|**userId**|**UserId**  |Necesario si y solo si _identity_ está establecido en `userFromId`. Identificador de una entidad de seguridad de usuario asociado a un usuario que inició sesión previamente.|
|**userToken**|**UserToken**|Necesario si y solo si _identity_ está establecido en `userFromToken`. Token válido para la aplicación de función. |
|**action**|**Acción**|(Requerido): especifica la acción que debe realizar el enlace. Puede ser uno de los siguientes valores:<ul><li><code>create</code>: registra una suscripción nueva.</li><li><code>delete</code>: elimina una determinada suscripción.</li><li><code>refresh</code>: actualiza una determinada suscripción para impedir que expire.</li></ul>|
|**subscriptionResource**|**SubscriptionResource**|Necesario si y solo si _action_ está establecido en `create`. Especifica el recurso de Microsoft Graph que se va a supervisar para los cambios. Consulte [Trabajar con webhooks en Microsoft Graph]. |
|**changeType**|**ChangeType**|Necesario si y solo si _action_ está establecido en `create`. Indica el tipo de cambio en el recurso suscrito que generará una notificación. Los valores admitidos son: `created`, `updated` y `deleted`. Pueden combinarse varios valores mediante una lista separada por comas.|

### <a name="webhook-output---usage"></a>Salida de webhook: uso

El enlace expone los siguientes tipos de funciones. NET:
- cadena
- Microsoft.Graph.Subscription




<a name="webhook-examples"></a>
## <a name="webhook-subscription-refresh"></a>Actualización de suscripción de webhook

Existen dos enfoques para la actualización de las suscripciones:

- Use la identidad de la aplicación para tratar todas las suscripciones. Esto requerirá el consentimiento de un administrador de Azure Active Directory. Esto puede usarse por todos los idiomas admitidos por Azure Functions.
- Use la identidad asociada con cada suscripción enlazando manualmente cada identificador de usuario. Será necesario código personalizado para realizar el enlace. Solo pueden usarlo funciones. NET.

En esta sección se incluye un ejemplo de cada uno de estos enfoques:

* [Ejemplo de identidad de aplicación](#webhook-subscription-refresh---app-identity-example)
* [Ejemplo de identidad de usuario](#webhook-subscription-refresh---user-identity-example)

### <a name="webhook-subscription-refresh---app-identity-example"></a>Actualización de suscripción de webhook: ejemplo de identidad de aplicación

Vea el ejemplo específico del lenguaje:

* [Script de C# (.csx)](#app-identity-refresh---c-script-example)
* [JavaScript](#app-identity-refresh---javascript-example)

### <a name="app-identity-refresh---c-script-example"></a>Actualización de identidad de aplicación: ejemplo de script de C#

En el ejemplo siguiente se usa la identidad de aplicación para actualizar una suscripción.

El archivo *function.json* define un desencadenador de temporizador con un enlace de entrada de suscripción y un enlace de salida de suscripción:

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

El código de script de C# actualiza las suscripciones:

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

### <a name="app-identity-refresh---c-script-example"></a>Actualización de identidad de aplicación: ejemplo de script de C#

En el ejemplo siguiente se usa la identidad de aplicación para actualizar una suscripción.

El archivo *function.json* define un desencadenador de temporizador con un enlace de entrada de suscripción y un enlace de salida de suscripción:

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

El código de JavaScript actualiza las suscripciones:

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

### <a name="webhook-subscription-refresh---user-identity-example"></a>Actualización de suscripción de webhook: ejemplo de identidad de usuario

En el ejemplo siguiente se usa la identidad de usuario para actualizar una suscripción.

El archivo *function.json* define un desencadenador de temporizador y difiere el enlace de entrada de suscripción al código de función:

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

El código de script de C# actualiza las suscripciones y crea el enlace de salida en el código, usando la identidad de cada usuario:

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

## <a name="next-steps"></a>pasos siguientes

> [!div class="nextstepaction"]
> [Más información sobre desencadenadores y enlaces de Azure Functions](functions-triggers-bindings.md)

[desencadenador HTTP]: functions-bindings-http-webhook.md
[Trabajar con webhooks en Microsoft Graph]: https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/webhooks
