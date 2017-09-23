---
title: Guardado de informes en Azure Power BI Embedded | Microsoft Docs
description: "Aprenda a guardar informes en Power BI Embedded. Para que esta operación funcione correctamente, se requieren los permisos adecuados."
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
ms.translationtype: Human Translation
ms.sourcegitcommit: c1cd1450d5921cf51f720017b746ff9498e85537
ms.openlocfilehash: ad895004cc2972f2ded81566186325a16d401151
ms.contentlocale: es-es
ms.lasthandoff: 03/14/2017

---
# <a name="save-reports-in-power-bi-embedded"></a>Guardado de informes en Power BI Embedded

Aprenda a guardar informes en Power BI Embedded. Para que esta operación funcione correctamente, se requieren los permisos adecuados.

En Power BI Embedded, es posible editar los informes existentes y guardarlos. También se puede crear un informe nuevo y guardarlo como un nuevo informe para crear uno.

Para guardar un informe, en primer lugar hay que crear un token para el informe concreto con los ámbitos correctos:

* Para habilitar la operación save, se requiere el ámbito Report.ReadWrite
* Para habilitar la operación save as, se requieren los ámbitos Report.Read y Workspace.Report.Copy
* Para habilitar las operaciones save y save as, se requieren Report.ReadWrite y Workspace.Report.Copy

Para habilitar los botones de save o save as, respectivamente, correctos en el menú archivo es preciso proporcionar el permiso adecuado en la configuración de la inserción al insertar el informe:

* models.Permissions.ReadWrite
* models.Permissions.Copy
* models.Permissions.All

> [!NOTE]
> El token de acceso también necesita los ámbitos adecuados. Para más información, consulte [Scopes](power-bi-embedded-app-token-flow.md#scopes) (Ámbitos).

## <a name="embed-report-in-edit-mode"></a>Inserción de informes en modo de edición

Supongamos que desea insertar un informe en modo de edición en la aplicación; para ello, simplemente pase las propiedades correctas en la configuración de la inserción y llame a powerbi.embed(). Tendrá que suministrar permisos y una propiedad viewMode para ver los botones de save y save as en modo de edición. Para más información, consulte [Embed configuration details](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Embed-Configuration-Details) (Detalles de configuración de la inserción).

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
        permissions: models.Permissions.All /*both save & save as buttons will be visible*/,
        viewMode: models.ViewMode.Edit,
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

Ahora, se insertará un informe en la aplicación en modo de edición.

## <a name="save-report"></a>Guardar informe

Después de insertar el informe en modo de edición con el token y los permisos adecuados puede guardar el informe desde el menú archivo o desde Javascript:

```
 // Get a reference to the embedded report.
    report = powerbi.get(reportContainer);

 // Save report
    report.save();
```

## <a name="save-as"></a>Guardar como

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
> Solo después de *save as* se crea un informe nuevo. Después de save, el lienzo sigue mostrando el informe anterior en modo de edición, no el nuevo informe. Necesitará insertar el nuevo informe que se creó. Esto requiere un nuevo token de acceso, ya que se crean por informe.

Después tendrá que cargar el informe nuevo después de *save as*. Esta operación es similar a la inserción de cualquier informe.

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

## <a name="see-also"></a>Consulte también

[Get started with Microsoft Power BI Embedded sample (Introducción a Microsoft Power BI Embedded: ejemplo)](power-bi-embedded-get-started-sample.md)  
[Embed a report in Power BI Embedded](power-bi-embedded-embed-report.md) (Inserción de un informe en Power BI Embedded)  
[Creación de un nuevo informe a partir de un conjunto de datos](power-bi-embedded-create-report-from-dataset.md)  
[Autenticación y autorización con Power BI Embedded](power-bi-embedded-app-token-flow.md)  
[Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)  
[JavaScript Embed Sample](https://microsoft.github.io/PowerBI-JavaScript/demo/) (Ejemplo de inserción de JavaScript)  
¿Tiene más preguntas? [Pruebe la comunidad de Power BI](http://community.powerbi.com/)


