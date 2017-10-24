---
title: "Creación de un nuevo informe a partir de un conjunto de datos en Colecciones de áreas de trabajo de Power BI | Microsoft Docs"
description: "Los informes de Colección de áreas de trabajo de Power BI ahora se pueden crear a partir de un conjunto de datos de su propia aplicación."
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
ms.openlocfilehash: aa902cbc4992292420948b36d85e52fafc7224de
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-new-report-from-a-dataset-in-power-bi-workspace-collections"></a>Creación de un nuevo informe a partir de un conjunto de datos en Colecciones de áreas de trabajo de Power BI

Los informes de Colección de áreas de trabajo de Power BI ahora se pueden crear a partir de un conjunto de datos de su propia aplicación.

> [!IMPORTANT]
> Colecciones de áreas de trabajo de Power BI está en desuso y estará disponible hasta junio de 2018 o hasta cuando lo indique su contrato. Se recomienda planear la migración a Power BI Embedded para evitar la interrupción de la aplicación. Para obtener más información sobre cómo migrar los datos a Power BI Embedded, consulte [Migración de contenido de la colección de áreas de trabajo de Power BI Embedded a Power BI](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

El método de autenticación es similar al de la inserción de informes. Se basa en tokens de acceso que son específicos de un conjunto de datos. Los tokens que se usan para PowerBI.com los emite Azure Active Directory (AAD). Los tokens de Colección de áreas de trabajo de Power BI los emite su propia ampliación.

Cuando se crea un informe insertado, los tokens emitidos son para un conjunto de datos concreto. Los tokens deben asociarse a la dirección URL de inserción del mismo elemento para asegurarse de que cada uno de ellos tiene un token único. Para crear un informe insertado, se deben proporcionar los ámbitos *Dataset.Read y Workspace.Report.Create* en el token de acceso.

## <a name="create-access-token-needed-to-create-new-report"></a>Creación de los tokens de acceso necesarios para crear un informe nuevo

Colección de áreas de trabajo de Power BI usa tokens de inserción, es decir, tokens web JSON con firma HMAC. Los tokens se firman con la clave de acceso de Colección de áreas de trabajo de Power BI. De manera predeterminada, los tokens de inserción se utilizan para proporcionar acceso de solo lectura a un informe para insertarlo en una aplicación. Los tokens de inserción se emiten para un informe concreto y se deben asociar a una dirección URL de inserción.

Los tokens de acceso deben crearse en el servidor, ya que las claves de acceso se utilizan para firmar o cifrar los tokens. Para obtener información sobre cómo crear un token de acceso, consulte [Autenticación y autorización con Power BI Embedded](app-token-flow.md). También puede revisar el método [CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN#methods_). Aquí hay un ejemplo de cómo sería si se usara el SDK de .NET de Power BI.

En este ejemplo, tenemos nuestro identificador de conjunto de datos en el deseamos crear el nuevo informe. También es preciso agregar los ámbitos de *Dataset.Read y Workspace.Report.Create*.

La *clase PowerBIToken* requiere que instale el [paquete NuGet Power BI Core](https://www.nuget.org/packages/Microsoft.PowerBI.Core/).

**Instalación de un paquete NuGet**

```
Install-Package Microsoft.PowerBI.Core
```

**Código de C#**

```
using Microsoft.PowerBI.Security;

// rlsUsername and roles are optional
string scopes = "Dataset.Read Workspace.Report.Create";
PowerBIToken embedToken = PowerBIToken.CreateReportEmbedTokenForCreation(workspaceCollectionName, workspaceId, datasetId, null, null, scopes);

var token = embedToken.Generate("{access key}");
```

## <a name="create-a-new-blank-report"></a>Creación de un informe en blanco nuevo

Para crear un informe nuevo, debe proporcionarse la configuración de creación. Aquí se debe incluir el token de acceso y los valores de embedURL y el datasetID con los que deseamos crear el informe. Esto requiere instalar el paquete NuGet [Power BI JavaScript](https://www.nuget.org/packages/Microsoft.PowerBI.JavaScript/). El valor de embedUrl será https://embedded.powerbi.com/appTokenReportEmbed.

> [!NOTE]
> Puede usar el [ejemplo de inserción de informe JavaScript](https://microsoft.github.io/PowerBI-JavaScript/demo/) para probar la funcionalidad. También proporciona ejemplos de código de las distintas operaciones que están disponibles.

**Instalación de un paquete NuGet**

```
Install-Package Microsoft.PowerBI.JavaScript
```

**Código JavaScript**

```
<div id="reportContainer"></div>
  
var embedCreateConfiguration = {
        accessToken: 'eyJ0eXAiO...Qron7qYpY9MI',
        embedUrl: 'https://embedded.powerbi.com/appTokenReportEmbed',
        datasetId: '5dac7a4a-4452-46b3-99f6-a25915e0fe55',
    };
    
    // Grab the reference to the div HTML element that will host the report
    var reportContainer = $('#reportContainer')[0];

    // Create report
    var report = powerbi.createReport(reportContainer, embedCreateConfiguration);
```

Al llamar a *powerbi.createReport()*, aparecerá un lienzo en blanco en modo de edición en el elemento *div*.

![Nuevo informe en blanco](media/create-report-from-dataset/create-new-report.png)

## <a name="save-new-reports"></a>Guardado de informes nuevos

El informe no se creará hasta que llame a la operación **save as**, lo que se puede hacer desde el menú archivo o desde JavaScript.

```
 // Get a reference to the embedded report.
    report = powerbi.get(reportContainer);
    
    var saveAsParameters = {
        name: "newReport"
    };

    // SaveAs report
    report.saveAs(saveAsParameters);
```

> [!IMPORTANT]
> Los informes nuevo solo se pueden crear después de llamar a **save as**. Después de guardar, el lienzo seguirá mostrando el conjunto de datos en modo de edición y no el informe. Será preciso que vuelva a cargar el nuevo informe como cualquier otro.

![Menú Archivo - Guardar como](media/create-report-from-dataset/save-new-report.png)

## <a name="load-the-new-report"></a>Carga del nuevo informe

Para interactuar con el nuevo informe es preciso que lo inserte de la misma manera en que la aplicación inserta un informe normal, es decir, se debe emitir un token nuevo específico para el nuevo informe y, después, llame al método de inserción.

```
<div id="reportContainer"></div>
  
var embedConfiguration = {
        accessToken: 'eyJ0eXAiO...Qron7qYpY9MJ',
        embedUrl: 'https://embedded.powerbi.com/appTokenReportEmbed',
        reportId: '5dac7a4a-4452-46b3-99f6-a25915e0fe54',
    };
    
    // Grab the reference to the div HTML element that will host the report
    var reportContainer = $('#reportContainer')[0];

    // Embed report
    var report = powerbi.embed(reportContainer, embedConfiguration);
```

## <a name="automate-save-and-load-of-a-new-report-using-the-saved-event"></a>Guardado y carga automáticos de un informe nuevo mediante el informe "guardado"

Para automatizar el proceso de "save as" y, luego, cargar el informe nuevo, puede usar el evento "guardado". Este evento se desencadena cuando se completa la operación save y devuelve un objeto JSON que contiene el identificador nuevo reportId, el nombre del informe, el objeto reportId anterior (si hay) y si la operación fue saveAs o save.

```
{
  "reportObjectId": "5dac7a4a-4452-46b3-99f6-a25915e0fe54",
  "reportName": "newReport",
  "saveAs": true,
  "originalReportObjectId": null
}
```

Para automatizar el proceso puede escuchar el evento "guardado", tomar el nuevo objeto reportId, crear el nuevo token e insertar el nuevo informe.

```
<div id="reportContainer"></div>
  
var embedCreateConfiguration = {
        accessToken: 'eyJ0eXAiO...Qron7qYpY9MI',
        embedUrl: 'https://embedded.powerbi.com/appTokenReportEmbed',
        datasetId: '5dac7a4a-4452-46b3-99f6-a25915e0fe55',
    };
    
    // Grab the reference to the div HTML element that will host the report
    var reportContainer = $('#reportContainer')[0];

    // Create report
    var report = powerbi.createReport(reportContainer, embedCreateConfiguration);


   var saveAsParameters = {
        name: "newReport"
    };

    // SaveAs report
    report.saveAs(saveAsParameters);

    // report.on will add an event handler which prints to Log window.
    report.on("saved", function(event) {
        
         // get new Token
         var newReportId =  event.detail.reportObjectId;

        // create new Token. This is a function that the application should provide
        var newToken = createAccessToken(newReportId,scopes /*provide the wanted scopes*/);
        
        
    var embedConfiguration = {
        accessToken: newToken ,
        embedUrl: 'https://embedded.powerbi.com/appTokenReportEmbed',
        reportId: newReportId,
    };

    // Embed report
    var report = powerbi.embed(reportContainer, embedConfiguration);
       
   // report.off removes a given event handler if it exists.
   report.off("saved");
    });
```

## <a name="see-also"></a>Otras referencias

[Get started with Microsoft Power BI Embedded sample (Introducción a Microsoft Power BI Embedded: ejemplo)](get-started-sample.md)  
[Almacenamiento de informes](save-reports.md)  
[Embed a report in Power BI Embedded](embed-report.md) (Inserción de un informe en Power BI Embedded)  
[Autenticación y autorización con Colecciones de áreas de trabajo de Power BI](app-token-flow.md)  
[Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)  
[JavaScript Embed Sample](https://microsoft.github.io/PowerBI-JavaScript/demo/) (Ejemplo de inserción de JavaScript)  
[Paquete NuGet Power BI Core](https://www.nuget.org/packages/Microsoft.PowerBI.Core/)  
[Paquete Power BI JavaScript](https://www.nuget.org/packages/Microsoft.PowerBI.JavaScript/)  

¿Tiene más preguntas? [Pruebe la comunidad de Power BI](http://community.powerbi.com/)