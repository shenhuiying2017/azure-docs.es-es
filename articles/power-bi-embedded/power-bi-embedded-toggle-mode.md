---
title: "Alternancia entre los modo de vista y de edición de informes en Azure Power BI Embedded | Microsoft Docs"
description: "Aprenda a alternar entre los modos de vista y de edición en los informes de Power BI Embedded."
services: power-bi-embedded
documentationcenter: 
author: guyinacube
manager: erikre
editor: 
tags: 
ms.assetid: 
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 03/11/2017
ms.author: asaxton
translationtype: Human Translation
ms.sourcegitcommit: c1cd1450d5921cf51f720017b746ff9498e85537
ms.openlocfilehash: f73bf05b41523a5833cc9366fb84cb7021b4b7a9
ms.lasthandoff: 03/14/2017

---
# <a name="toggle-between-view-and-edit-mode-for-reports-in-power-bi-embedded"></a>Alternancia entre los modo de vista y de edición de informes en Power BI Embedded

Aprenda a alternar entre los modos de vista y de edición en los informes de Power BI Embedded.

## <a name="creating-an-access-token"></a>Creación de un token de acceso

Tendrá que crear un token de acceso que le proporcione la capacidad de ver y editar informes. Para editar y guardar un informe, necesitará el permiso del token **Report.ReadWrite**. Para más información, consulte [Autenticación y autorización con Power BI Embedded](power-bi-embedded-app-token-flow.md).

> [!NOTE]
> Esto le permitirá realizar modificaciones y guardar los cambios en un informe existente. Si también desea la función de admitir **Guardar como**, será preciso que proporcione permisos adicionales. Para más información, consulte [Scopes](power-bi-embedded-app-token-flow.md#scopes) (Ámbitos).

```
using Microsoft.PowerBI.Security;

// rlsUsername and roles are optional
string scopes = "Report.ReadWrite";
PowerBIToken embedToken = PowerBIToken.CreateReportEmbedTokenForCreation(workspaceCollectionName, workspaceId, datasetId, null, null, scopes);

var token = embedToken.Generate("{access key}");
```

## <a name="embed-configuration"></a>Configuración de la inserción

Tendrá que suministrar permisos y una propiedad viewMode para ver el botones de save en modo de edición. Para más información, consulte [Embed configuration details](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Embed-Configuration-Details) (Detalles de configuración de la inserción).

Por ejemplo, en JavaScript:

```
   <div id="reportContainer"></div>

    // Get models. Models, it contains enums that can be used.
    var models = window['powerbi-client'].models;

    // Embed configuration used to describe the what and how to embed.
    // This object is used when calling powerbi.embed.
    // This also includes settings and options such as filters.
    // You can find more information at https://github.com/Microsoft/PowerBI-JavaScript/wiki/Embed-Configuration-Details.
    var config= {
        type: 'report',
        accessToken: 'eyJ0eXAiO...Qron7qYpY9MI',
        embedUrl: 'https://embedded.powerbi.com/appTokenReportEmbed',
        id:  '5dac7a4a-4452-46b3-99f6-a25915e0fe55',
        permissions: models.Permissions.ReadWrite /*both save & save as buttons will be visible*/,
        viewMode: models.ViewMode.View,
        settings: {
            filterPaneEnabled: true,
            navContentPaneEnabled: true
        }
    };

    // Get a reference to the embedded report HTML element
    var reportContainer = $('#reportContainer')[0];

    // Embed the report and display it within the div container.
    var report = powerbi.embed(reportContainer, config);
```

Esto se indicará para insertar el informe en modo de vista en función del **viewMode** que se establece en **modelos.ViewMode.View**.

## <a name="view-mode"></a>Modo de vista

Si se encuentra en modo de edición, puede usar el siguiente código de JavaScript para cambiar al modo de vista.

```
// Get a reference to the embedded report HTML element
var reportContainer = $('#reportContainer')[0];

// Get a reference to the embedded report.
report = powerbi.get(reportContainer);

// Switch to view mode.
report.switchMode("view");

```

## <a name="edit-mode"></a>Modo de edición

Si se encuentra en modo de vista, puede usar el siguiente código de JavaScript para cambiar al modo de edición.

```
// Get a reference to the embedded report HTML element
var reportContainer = $('#reportContainer')[0];

// Get a reference to the embedded report.
report = powerbi.get(reportContainer);

// Switch to edit mode.
report.switchMode("edit");

```

## <a name="see-also"></a>Consulte también

[Get started with Microsoft Power BI Embedded sample (Introducción a Microsoft Power BI Embedded: ejemplo)](power-bi-embedded-get-started-sample.md)  
[Embed a report in Power BI Embedded](power-bi-embedded-embed-report.md) (Inserción de un informe en Power BI Embedded)  
[Autenticación y autorización con Power BI Embedded](power-bi-embedded-app-token-flow.md)  
[CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN#methods_)  
[JavaScript Embed Sample](https://microsoft.github.io/PowerBI-JavaScript/demo/) (Ejemplo de inserción de JavaScript)  
[Repositorio GIT PowerBI-CSharp](https://github.com/Microsoft/PowerBI-CSharp)  
[Repositorio GIT PowerBI-Node](https://github.com/Microsoft/PowerBI-Node)  
¿Tiene más preguntas? [Pruebe la comunidad de Power BI](http://community.powerbi.com/)

