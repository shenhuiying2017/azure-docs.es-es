---
title: "Inserción de un informe en Colecciones de áreas de trabajo de Azure Power BI | Microsoft Docs"
description: "Aprenda a insertar en una aplicación un informe que se encuentre en Power BI Embedded."
services: power-bi-embedded
documentationcenter: 
author: guyinacube
manager: erikre
editor: 
tags: 
ROBOTS: NOINDEX
ms.assetid: 
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 09/20/2017
ms.author: asaxton
ms.openlocfilehash: 56e7ca90132527c0ef9d4bd478e99b75ca055272
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="embed-a-report-in-power-bi-workspace-collections"></a>Inserción de un informe en Colecciones de áreas de trabajo de Azure Power BI

Aprenda a insertar en una aplicación un informe que se encuentre en Power BI Embedded.

> [!IMPORTANT]
> Colecciones de áreas de trabajo de Power BI está en desuso y estará disponible hasta junio de 2018 o hasta cuando lo indique su contrato. Se recomienda planear la migración a Power BI Embedded para evitar la interrupción de la aplicación. Para obtener más información sobre cómo migrar los datos a Power BI Embedded, consulte [Migración de contenido de la colección de áreas de trabajo de Power BI Embedded a Power BI](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

En este artículo, se examina la forma en que realmente se inserta un informe en una aplicación. Aquí se asume que ya tiene un informe que existe en un área de trabajo de su colección de áreas de trabajo. Si aún no ha dado ese paso, consulte [Introducción a Colecciones de áreas de trabajo de Power BI](get-started.md).

Puede utilizar el SDK de .NET (C#) o Node.js, junto con JavaScript, para compilar fácilmente una aplicación con Colecciones de áreas de trabajo de Power BI.

## <a name="using-the-access-keys-to-use-rest-apis"></a>Uso de las teclas de acceso para utilizar las API de REST

Para llamar a la API de REST, puede pasar la clave de acceso que puede obtener de Azure Portal para una colección de áreas de trabajo dada. Para obtener más información, consulte [Introducción a Colecciones de áreas de trabajo de Power BI](get-started.md).

## <a name="get-a-report-id"></a>Obtención de un identificador de informe

Todos los tokens de acceso se basan en algún informe. Por consiguiente, tendrá que obtener el identificador de informe determinado del informe que desea insertar. Esto se puede hacer basándose en las llamadas a la API de REST de [Get Reports](https://msdn.microsoft.com/library/azure/mt711510.aspx). Esto devolverá el identificador del informe y la dirección URL de inserción. Esto se puede hacer mediante el SDK de .NET de Power BI, o bien llamando directamente a la API de REST.

### <a name="using-the-power-bi-net-sdk"></a>Uso del SDK de .NET de Power BI

Si se usa el SDK. de NET, será preciso crear una credencial de token que se base en la clave de acceso que se obtenga de Azure Portal. Esto requiere que se instale el [paquete NuGet Power BI API](https://www.nuget.org/profiles/powerbi).

**Instalación de un paquete NuGet**

```
Install-Package Microsoft.PowerBI.Api
```

**Código de C#**

```
using Microsoft.PowerBI.Api.V1;
using Microsoft.Rest;

var credentials = new TokenCredentials("{access key}", "AppKey");
var client = new PowerBIClient(credentials);
client.BaseUri = new Uri(https://api.powerbi.com);

var reports = (IList<Report>)client.Reports.GetReports(workspaceCollectionName, workspaceId).Value;

// Select the report that you want to work with from the collection of reports.
```

### <a name="calling-the-rest-api-directly"></a>Llamada directa a la API de REST

```
System.Net.WebRequest request = System.Net.WebRequest.Create("https://api.powerbi.com/v1.0/collections/{collectionName}/workspaces/{workspaceId}/Reports") as System.Net.HttpWebRequest;

request.Method = "GET";
request.ContentLength = 0;
request.Headers.Add("Authorization", String.Format("AppKey {0}", accessToken.Value));

using (var response = request.GetResponse() as System.Net.HttpWebResponse)
{
    using (var reader = new System.IO.StreamReader(response.GetResponseStream()))
    {
        // Work with JSON response to get the report you want to work with.
    }

}
```

## <a name="create-an-access-token"></a>Creación de un token de acceso

Colección de áreas de trabajo de Power BI usa tokens de inserción, es decir, tokens web JSON con firma HMAC. Los tokens se firman con la clave de acceso de Colección de áreas de trabajo de Power BI. De manera predeterminada, los tokens de inserción se utilizan para proporcionar acceso de solo lectura a un informe para insertarlo en una aplicación. Los tokens de inserción se emiten para un informe concreto y se deben asociar a una dirección URL de inserción.

Los tokens de acceso deben crearse en el servidor, ya que las claves de acceso se utilizan para firmar o cifrar los tokens. Para obtener información sobre cómo crear un token de acceso, consulte [Autenticación y autorización con Power BI Embedded](app-token-flow.md). También puede revisar el método [CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN#methods_). Aquí hay un ejemplo de cómo sería si se usara el SDK de .NET de Power BI.

Se utilizará el identificador de informe que recuperó anteriormente. Una vez creado el token de inserción, se usará la clave de acceso para generar el token que se puede utilizar desde la perspectiva de JavaScript. La *clase PowerBIToken* requiere que instale el [paquete NuGet Power BI Core](https://www.nuget.org/packages/Microsoft.PowerBI.Core/).

**Instalación de un paquete NuGet**

```
Install-Package Microsoft.PowerBI.Core
```

**Código de C#**

```
using Microsoft.PowerBI.Security;

// rlsUsername, roles and scopes are optional.
embedToken = PowerBIToken.CreateReportEmbedToken(workspaceCollectionName, workspaceId, reportId, rlsUsername, roles, scopes);

var token = embedToken.Generate("{access key}");
```

### <a name="adding-permission-scopes-to-embed-tokens"></a>Adición de ámbitos de permiso a tokes de inserción

Si usa tokens de inserción, puede restringir el uso de los recursos a los que proporciona acceso. Por este motivo, puede generar un token con permisos con ámbito. Para más información, consulte [Scopes](app-token-flow.md#scopes) (Ámbitos)

## <a name="embed-using-javascript"></a>Inserción mediante JavaScript

Una vez que tenga el token de acceso y el identificador del informe, el informe se puede insertar mediante JavaScript. Se requiere instalar el paquete NuGet [Power BI JavaScript](https://www.nuget.org/packages/Microsoft.PowerBI.JavaScript/). El valor de embedUrl será https://embedded.powerbi.com/appTokenReportEmbed.

> [!NOTE]
> Puede usar el [ejemplo de inserción de informe JavaScript](https://microsoft.github.io/PowerBI-JavaScript/demo/) para probar la funcionalidad. También proporciona ejemplos de código de las distintas operaciones que están disponibles.

**Instalación de un paquete NuGet**

```
Install-Package Microsoft.PowerBI.JavaScript
```

**Código JavaScript**

```
<script src="/scripts/powerbi.js"></script>
<div id="reportContainer"></div>

var embedConfiguration = {
    type: 'report',
    accessToken: 'eyJ0eXAiO...Qron7qYpY9MI',
    id: '5dac7a4a-4452-46b3-99f6-a25915e0fe55',
    embedUrl: 'https://embedded.powerbi.com/appTokenReportEmbed'
};

var $reportContainer = $('#reportContainer');
var report = powerbi.embed($reportContainer.get(0), embedConfiguration);
```

### <a name="set-the-size-of-embedded-elements"></a>Establecimiento del tamaño de los elementos insertados

El informe se insertará automáticamente según el tamaño de su contenedor. Para invalidar el tamaño predeterminado del elemento de inserción, agregue un atributo de clase CSS o estilos en línea de ancho y alto.

## <a name="see-also"></a>Otras referencias

[Get started with Microsoft Power BI Embedded sample (Introducción a Microsoft Power BI Embedded: ejemplo)](get-started-sample.md)  
[Autenticación y autorización con Colecciones de áreas de trabajo de Power BI](app-token-flow.md)  
[CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN#methods_)  
[JavaScript Embed Sample](https://microsoft.github.io/PowerBI-JavaScript/demo/) (Ejemplo de inserción de JavaScript)  
[Paquete Power BI JavaScript](https://www.nuget.org/packages/Microsoft.PowerBI.JavaScript/)  
[Paquete NuGet Power BI API](https://www.nuget.org/profiles/powerbi)
[Paquete NuGet Power BI Core](https://www.nuget.org/packages/Microsoft.PowerBI.Core/)  
[Repositorio GIT PowerBI-CSharp](https://github.com/Microsoft/PowerBI-CSharp)  
[Repositorio GIT PowerBI-Node](https://github.com/Microsoft/PowerBI-Node)  

¿Tiene más preguntas? [Pruebe la comunidad de Power BI](http://community.powerbi.com/)
