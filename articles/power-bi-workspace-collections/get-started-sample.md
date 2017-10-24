---
title: "Introducción a un ejemplo"
description: "En este artículo, ofreceremos el ejemplo introductorio de las colecciones de áreas de trabajo de Power BI."
services: power-bi-embedded
documentationcenter: 
author: guyinacube
manager: erikre
editor: 
tags: 
ROBOTS: NOINDEX
ms.assetid: d8a9ef78-ad4e-4bc7-9711-89172dc5c548
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 09/25/2017
ms.author: asaxton
ms.openlocfilehash: 9049f95c9f81c0217c96469a45561b6cd0b33ae9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-power-bi-workspace-collections-sample"></a>Introducción a un ejemplo de colecciones de áreas de trabajo de Power BI

Con las **colecciones de áreas de trabajo de Microsoft Power BI**, puede integrar informes de Power BI directamente en las aplicaciones web o móviles. En este artículo, ofrecemos el ejemplo introductorio de las **colecciones de áreas de trabajo de Power BI**.

> [!IMPORTANT]
> Las colecciones de áreas de trabajo de Power BI están en desuso y estarán disponibles hasta junio de 2018 o hasta cuando lo indique su contrato. Se recomienda planear la migración a Power BI Embedded para evitar la interrupción de la aplicación. Para más información sobre cómo migrar los datos a Power BI Embedded, consulte [Migración de contenido de Colección de áreas de trabajo de Power BI a Power BI Embedded](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

Antes de seguir avanzando, hay que guardar los siguientes recursos, pues ayudan a integrar los informes de Power BI en la aplicación de ejemplo y también sus propias aplicaciones.

* [Aplicación web de área de trabajo de ejemplo](http://go.microsoft.com/fwlink/?LinkId=761493)
* [Referencia de API de colecciones de áreas de trabajo de Power BI](https://msdn.microsoft.com/library/azure/mt711507.aspx)
* [SDK de .NET de Power BI](http://go.microsoft.com/fwlink/?LinkId=746472) (disponible a través de NuGet)
* [Ejemplo de inserción de informe de JavaScript](https://microsoft.github.io/PowerBI-JavaScript/demo)

> [!NOTE]
> Para poder configurar y ejecutar el ejemplo introductorio de las colecciones de áreas de trabajo de Power BI, debe crear al menos una **colección de área de trabajo** en su suscripción de Azure. Para ver cómo crear una **colección de áreas de trabajo** en Azure Portal, consulte [Introducción a las colecciones de áreas de trabajo de Power BI](get-started.md).

## <a name="configure-the-sample-app"></a>Configuración de la aplicación de ejemplo

Veamos la configuración del entorno de desarrollo de Visual Studio para acceder a los componentes necesarios para ejecutar la aplicación de ejemplo.

1. Descargue y descomprima el ejemplo [Power BI Workspace Collections - Integrate a report into a web app](http://go.microsoft.com/fwlink/?LinkId=761493) (Colecciones de áreas de trabajo de Power BI - Integración de un informe en una aplicación web) en GitHub.
2. Abra **PowerBI embedded.sln** en Visual Studio. Puede que necesite ejecutar el comando **Update-Package** en la Consola del Administrador de paquetes NuGET para actualizar los paquetes utilizados en esta solución.
3. Compile la solución.
4. Ejecute la aplicación de consola **ProvisionSample** . En la aplicación de consola del ejemplo, aprovisione un área de trabajo e importe un archivo PBIX.
5. Para aprovisionar una nueva **área de trabajo**, seleccione la opción 1, **Collection management** (Administración de colecciones), y luego seleccione la opción 6, **Provision a new Workspace** (Aprovisionar una nueva área de trabajo).
6. Para importar un nuevo **informe**, seleccione la opción 2, **Report management** (Administración de informes) y luego seleccione la opción 3, **Import PBIX Desktop file into a workspace** (Importar archivo PBIX de Desktop en un área de trabajo).

7. Escriba el nombre de su **colección de área de trabajo** y la **clave de acceso**. Puede obtener las claves en **Azure Portal**. Para obtener más información sobre cómo obtener la **clave de acceso**, consulte [Visualización de las claves de acceso de API de Power BI](get-started.md#view-power-bi-api-access-keys) en Introducción a la versión preliminar de Microsoft Power BI Embedded.

    ![Claves de acceso en Azure Portal](media/get-started-sample/azure-portal.png)
8. Copie y guarde el recién creado **identificador del área de trabajo** para utilizarlo más adelante en este artículo. Una vez creado el **identificador del área de trabajo**, **Azure Portal** puede encontrarlo.

    ![Identificador de área de trabajo en Azure Portal](media/get-started-sample/workspace-id.png)
9. Para importar un archivo PBIX en su **área de trabajo**, seleccione la opción **6. Importe el archivo de escritorio PBIX en un área de trabajo existente**. Si no tiene una archivo PBIX a mano, puede descargar la [muestra de PBIX de análisis comercial](http://go.microsoft.com/fwlink/?LinkID=780547).
10. Si se le solicita, escriba un nombre descriptivo para su **Dataset**.

Debería obtener una respuesta similar a la siguiente:

```
Checking import state... Publishing
Checking import state... Succeeded
```

> [!NOTE]
> Si el archivo PBIX contiene las conexiones de consulta directa, ejecute la opción 7 para actualizar las cadenas de conexión.

En este momento, tiene un informe PBIX de Power BI importado en su **área de trabajo**. Ahora, veamos cómo ejecutar la aplicación web de ejemplo introductorio de **Colecciones de áreas de trabajo de Power BI**.

## <a name="run-the-sample-web-app"></a>Ejecución de la aplicación web de ejemplo

El ejemplo de aplicación web es una aplicación de ejemplo que representa los informes importados a su **área de trabajo**. Vea cómo configurar el ejemplo de aplicación web.

1. En la solución de Visual Studio **PowerBI-embedded**, haga clic con el botón derecho en la aplicación web **EmbedSample** y elija **Establecer como proyecto de inicio**.
2. En **web.config**, en la aplicación web **EmbedSample**, edite los valores de **appSettings**: **AccessKey**, nombre de **WorkspaceCollection** y **WorkspaceId**.

    ```
    <appSettings>
        <add key="powerbi:AccessKey" value="" />
        <add key="powerbi:ApiUrl" value="https://api.powerbi.com" />
        <add key="powerbi:WorkspaceCollection" value="" />
        <add key="powerbi:WorkspaceId" value="" />
    </appSettings>
    ```
3. Ejecute la aplicación web **EmbedSample**.

Una vez ejecutada la aplicación web **EmbedSample**, el panel de navegación izquierdo debe contener un menú **Informes** menú. Para ver el informe que ha importado, expanda **Informes** y haga clic en un informe. Por ejemplo, si ha importado la [muestra de PBIX de análisis comercial](http://go.microsoft.com/fwlink/?LinkID=780547), la aplicación web de muestra tendría el siguiente aspecto:

![Barra de navegación izquierda de ejemplo dentro de la aplicación de ejemplo](media/get-started-sample/sample-left-nav.png)

Tras hacer clic en un informe, la aplicación web **EmbedSample** debe tener un aspecto similar al siguiente:

![Informe de ejemplo que se muestra dentro de la aplicación](media/get-started-sample/sample-web-app.png)

## <a name="explore-the-sample-code"></a>Exploración del código de ejemplo

El ejemplo de **Colección de áreas de trabajo de Microsoft Power BI** es una aplicación web de panel de ejemplo que muestra cómo integrar informes de **Power BI** en la aplicación. Usa un modelo de diseño Model-View-Controller (MVC) para demostrar las prácticas recomendadas. En esta sección se destacan las partes del ejemplo de código que puede explorar dentro de la solución de aplicación web **PowerBI-embedded**. El modelo Model-View-Controller (MVC) separa el modelado de dominio, la presentación y las acciones basadas en la entrada del usuario en tres clases distintas: Model, View y Control. Para más información sobre MVC, consulte [Learn About ASP.NET](http://www.asp.net/mvc) (Más información sobre ASP.NET).

El ejemplo de código de **Colección de áreas de trabajo de Microsoft Power BI** se divide de la forma siguiente. Cada sección incluye el nombre de archivo de la solución PowerBI-embedded.sln para que pueda encontrar fácilmente el código en el ejemplo.

> [!NOTE]
> Esta sección es un resumen del código de ejemplo que muestra cómo se escribe el código. Para ver el ejemplo completo, cargue la solución PowerBI-embedded.sln en Visual Studio.

### <a name="model"></a>Modelo

El ejemplo incluye las clases **ReportsViewModel** y **ReportViewModel**.

**ReportsViewModel.cs**: representa los informes de Power BI.

    public class ReportsViewModel
    {
        public List<Report> Reports { get; set; }
    }

**ReportViewModel.cs**: representa un informe de Power BI.

    public classReportViewModel
    {
        public IReport Report { get; set; }

        public string AccessToken { get; set; }
    }

### <a name="connection-string"></a>Cadena de conexión

La cadena de conexión debe tener el formato siguiente:

```
Data Source=tcp:MyServer.database.windows.net,1433;Initial Catalog=MyDatabase
```

No se pueden utilizar correctamente los atributos comunes de servidor y base de datos. Por ejemplo: Server=tcp:MyServer.database.windows.net,1433;Database=MyDatabase,

### <a name="view"></a>Ver

En la **vista** se administra la presentación de los **informes** de Power BI y de un **informe** de Power BI.

**Reports.cshtml**: recorre en iteración **Model.Reports** para crear un **ActionLink**. **ActionLink** se compone de los siguientes elementos:

| Elemento | Descripción |
| --- | --- |
| Título |Nombre del informe. |
| QueryString |Vínculo al identificador del informe. |

    <div id="reports-nav" class="panel-collapse collapse">
        <div class="panel-body">
            <ul class="nav navbar-nav">
                @foreach (var report in Model.Reports)
                {
                    var reportClass = Request.QueryString["reportId"] == report.Id ? "active" : "";
                    <li class="@reportClass">
                        @Html.ActionLink(report.Name, "Report", new { reportId = report.Id })
                    </li>
                }
            </ul>
        </div>
    </div>

Report.cshtml: establece **Model.AccessToken** y la expresión lambda para **PowerBIReportFor**.

    @model ReportViewModel

    ...

    <div class="side-body padding-top">
        @Html.PowerBIAccessToken(Model.AccessToken)
        @Html.PowerBIReportFor(m => m.Report, new { style = "height:85vh" })
    </div>

### <a name="controller"></a>Controller

**DashboardController.cs**: crea una clase PowerBIClient que pasa un **token de aplicación**. Se genera un token JSON Web Token (JWT) a partir de la **clave de firma** para obtener las **credenciales**. Las **credenciales** se usan para crear una instancia de **PowerBIClient**. Cuando tenga una instancia de **PowerBIClient**, puede llamar a GetReports() y GetReportsAsync().

CreatePowerBIClient()

    private IPowerBIClient CreatePowerBIClient()
    {
        var credentials = new TokenCredentials(accessKey, "AppKey");
        var client = new PowerBIClient(credentials)
        {
            BaseUri = new Uri(apiUrl)
        };

        return client;
    }

ActionResult Reports()

    public ActionResult Reports()
    {
        using (var client = this.CreatePowerBIClient())
        {
            var reportsResponse = client.Reports.GetReports(this.workspaceCollection, this.workspaceId);

            var viewModel = new ReportsViewModel
            {
                Reports = reportsResponse.Value.ToList()
            };

            return PartialView(viewModel);
        }
    }


Tarea<ActionResult> Report(string reportId)

    public async Task<ActionResult> Report(string reportId)
    {
        using (var client = this.CreatePowerBIClient())
        {
            var reportsResponse = await client.Reports.GetReportsAsync(this.workspaceCollection, this.workspaceId);
            var report = reportsResponse.Value.FirstOrDefault(r => r.Id == reportId);
            var embedToken = PowerBIToken.CreateReportEmbedToken(this.workspaceCollection, this.workspaceId, report.Id);

            var viewModel = new ReportViewModel
            {
                Report = report,
                AccessToken = embedToken.Generate(this.accessKey)
            };

            return View(viewModel);
        }
    }

### <a name="integrate-a-report-into-your-app"></a>Integración de un informe en la aplicación

Cuando tenga un **informe**, puede usar **IFrame** para insertar el **informe** de Power BI. Este es un fragmento de código de powerbi.js en el ejemplo de versión preliminar de **Colección de áreas de trabajo de Microsoft Power BI**.

```
init: function() {
    var embedUrl = this.getEmbedUrl();
    var iframeHtml = '<igrame style="width:100%;height:100%;" src="' + embedUrl + 
        '" scrolling="no" allowfullscreen="true"></iframe>';
    this.element.innerHTML = iframeHtml;
    this.iframe = this.element.childNodes[0];
    this.iframe.addEventListener('load', this.load.bind(this), false);
}
```

## <a name="filter-reports-embedded-in-your-application"></a>Filtro de informes incrustados en la aplicación

Puede filtrar un informe incrustado mediante una sintaxis de dirección URL. Para ello, agregue un parámetro de cadena de consulta **$filter** con un operador **eq** a la dirección URL de src de iFrame con el filtro especificado. Esta es la sintaxis de consulta de filtro:

```
https://app.powerbi.com/reportEmbed
?reportId=d2a0ea38-...-9673-ee9655d54a4a&
$filter={tableName/fieldName}%20eq%20'{fieldValue}'
```

> [!NOTE]
> {tableName/fieldName} no puede incluir espacios ni caracteres especiales. {fieldValue} acepta un único valor de categoría.  

## <a name="see-also"></a>Otras referencias

[Escenarios comunes de Colección de áreas de trabajo de Microsoft Power BI](scenarios.md)  
[Authenticating and authorizing in Power BI Workspace Collections](app-token-flow.md) (Autenticación y autorización en las colecciones de áreas de trabajo de Power BI)  
[Embed a report in Power BI Embedded](embed-report.md) (Inserción de un informe en Power BI Embedded)  
[Creación de un nuevo informe a partir de un conjunto de datos](create-report-from-dataset.md)  
[Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)  
[JavaScript Embed Sample](https://microsoft.github.io/PowerBI-JavaScript/demo/) (Ejemplo de inserción de JavaScript)  

¿Tiene más preguntas? [Pruebe la comunidad de Power BI](http://community.powerbi.com/)
