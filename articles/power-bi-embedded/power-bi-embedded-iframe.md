<properties
   pageTitle="Versión preliminar de Microsoft Power BI Embedded: inserción de un informe de Power BI con un objeto IFrame"
   description="Versión preliminar de Microsoft Power BI Embedded: código esencial para integrar un informe en la aplicación, cómo debe autenticarse con el token de aplicación de Power BI Embedded, cómo obtener informes"
   services="power-bi-embedded"
   documentationCenter=""
   authors="dvana"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="power-bi-embedded"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="powerbi"
   ms.date="05/16/2016"
   ms.author="derrickv"/>

# Inserción de un informe de Power BI con un iframe
En este artículo se muestra código esencial para usar la API de REST de **Power BI Embedded**, los tokens de aplicación, un IFrame y algo de código JavaScript para integrar o insertar un informe en la aplicación.

En [Introducción a la versión preliminar de Microsoft Power BI Embedded](power-bi-embedded-get-started.md), aprenderá a configurar una **colección de áreas de trabajo** para contener una o varias **áreas de trabajo** para el contenido del informe. Después, en [Introducción al ejemplo de Microsoft Power BI Embedded](power-bi-embedded-get-started-sample.md), importe un informe en un **área de trabajo**.

En este artículo se muestran los pasos necesarios para insertar un informe en la aplicación. Para seguir este artículo, debe descargar el ejemplo de [integración de un informe con un IFrame](https://github.com/Azure-Samples/power-bi-embedded-iframe) en GitHub. Este ejemplo es una sencilla aplicación de formulario web de ASP.NET cuyo objetivo es describir el código C# y JavaScript esencial necesario para integrar un informe. Para un ejemplo más avanzado que utiliza el patrón de diseño Model-View-Controller (MVC) para integrar un informe, consulte la [aplicación web de panel de ejemplo](http://go.microsoft.com/fwlink/?LinkId=761493) en GitHub.

Comencemos por describir cómo integrar un informe de **Power BI Embedded** en la aplicación.

Estos son los pasos necesarios para integrar un informe.

- Paso 1: [Obtención de un informe en un área de trabajo](#GetReport). En este paso, utilice un flujo de tokens de la aplicación para obtener un token de acceso para llamar a la operación de REST [Get Reports](https://msdn.microsoft.com/library/mt711510.aspx). Cuando obtiene un informe desde la lista **Get Reports**, también puede insertarlo en una aplicación con un elemento **IFrame**.
- Paso 2: [Inserción de un informe en una aplicación](#EmbedReport). En este paso, va a usar un token que se inserta en un informe, algo de código JavaScript y un objeto IFrame para integrar, o insertar, un informe en una aplicación web.

Si desea ejecutar el ejemplo para ver cómo integrar un informe, descargue en GitHub el ejemplo de [integración un informe con un objeto IFrame](https://github.com/Azure-Samples/power-bi-embedded-iframe) en GitHub y configure tres opciones de Web.Config:

- **AccessKey**: se usa un objeto **AccessKey** para generar un Token web JSON (JWT), que se utiliza para obtener informes e insertar uno de ellos. Para más información sobre cómo obtener un atributo **AccessKey**, consulte [Introducción a la versión preliminar de Microsoft Power BI Embedded](power-bi-embedded-get-started.md).
- **WorkspaceName**: Para aprender cómo obtener un atributo **WorkspaceName**, consulte [Introducción a la versión preliminar de Microsoft Power BI Embedded](power-bi-embedded-get-started.md).
- **WorkspaceId**: Para aprender cómo obtener un atributo **WorkspaceId**, consulte [Introducción a la versión preliminar de Microsoft Power BI Embedded](power-bi-embedded-get-started.md).

En las secciones siguientes se muestra el código que se necesita para integrar un informe.

<a name="GetReport"/>
## Obtención de un informe en un área de trabajo

Para integrar un informe en una aplicación, necesita un **ID** y una dirección **embedUrl** del informe. Para obtener un **ID** y una dirección **embedUrl** de informe, hay que llamar a la operación REST [Get Reports](https://msdn.microsoft.com/library/mt711510.aspx) y elegir un informe en la lista JSON. En [Inserción de un informe en una aplicación](#EmbedReport), utilice un **ID** y una dirección **embedUrl** de informe para insertar el informe en la aplicación.

### Respuesta JSON de Get Reports
```
{
  "@odata.context":"https://api.powerbi.com/beta/collections/{WorkspaceName}/workspaces/{WorkspaceId}/$metadata#reports","value":[
    {
      "id":"804d3664-…-e71882055dba","name":"Import report sample","webUrl":"https://embedded.powerbi.com/reports/804d3664-...-e71882055dba","embedUrl":"https://embedded.powerbi.com/appTokenReportEmbed?reportId=804d3664-...-e71882055dba"
    },{
      "id":"1d7cff58-…-380610e263a9","name":"Sample Report 2","webUrl":"https://embedded.powerbi.com/reports/1d7cff58-...-380610e263a9","embedUrl":"https://embedded.powerbi.com/appTokenReportEmbed?reportId=1d7cff58-...-380610e263a9"
    }
  ]
}

```

Para llamar a la operación REST [Get Reports](https://msdn.microsoft.com/library/mt711510.aspx), utilice un token de aplicación. Para más información sobre el flujo del token de aplicación, consulte [Acerca del flujo del token de aplicación en Power BI Embedded](power-bi-embedded-app-token-flow.md). El código siguiente describe cómo obtener una lista JSON de informes. Para insertar un informe, consulte [Inserción de un informe en una aplicación](#EmbedReport).

```
protected void getReportsButton_Click(object sender, EventArgs e)
{
    //Get an app token to generate a JSON Web Token (JWT). An app token flow is a claims-based design pattern.
    //To learn how you can code an app token flow to generate a JWT, see the PowerBIToken class.
    var appToken = PowerBIToken.CreateDevToken(workspaceName, workspaceId);

    //After you get a PowerBIToken which has Claims including your WorkspaceName and WorkspaceID,
    //you generate JSON Web Token (JWT) . The Generate() method uses classes from System.IdentityModel.Tokens: SigningCredentials,
    //JwtSecurityToken, and JwtSecurityTokenHandler.
    string jwt = appToken.Generate(accessKey);

    //Set app token textbox to JWT string to show that the JWT was generated
    appTokenTextbox.Text = jwt;

    //Construct reports uri resource string
    var uri = String.Format("https://api.powerbi.com/beta/collections/{0}/workspaces/{1}/reports", workspaceName, workspaceId);

    //Configure reports request
    System.Net.WebRequest request = System.Net.WebRequest.Create(uri) as System.Net.HttpWebRequest;
    request.Method = "GET";
    request.ContentLength = 0;

    //Set the WebRequest header to AppToken, and jwt
    //Note the use of AppToken instead of Bearer
    request.Headers.Add("Authorization", String.Format("AppToken {0}", jwt));

    //Get reports response from request.GetResponse()
    using (var response = request.GetResponse() as System.Net.HttpWebResponse)
    {
        //Get reader from response stream
        using (var reader = new System.IO.StreamReader(response.GetResponseStream()))
        {
            //Deserialize JSON string into PBIReports
            PBIReports PBIReports = JsonConvert.DeserializeObject<PBIReports>(reader.ReadToEnd());

            //Clear Textbox
            tb_reportsResult.Text = string.Empty;

            //Get each report
            foreach (PBIReport rpt in PBIReports.value)
            {
                tb_reportsResult.Text += String.Format("{0}\t{1}\t{2}\n", rpt.id, rpt.name, rpt.embedUrl);
            }
        }
    }
}
```

<a name="EmbedReport"/>
## Inserción de un informe en una aplicación

Antes de poder insertar un informe en la aplicación, necesita un token de inserción para un informe. Este token es similar a un token de aplicación que se usa para llamar a las operaciones REST de **Power BI Embedded**, pero se genera para un recurso de informe en lugar de para un recurso REST. Lo siguiente es el código para obtener un token de aplicación para un informe. Para usar el token de aplicación de informes, consulte [Inserción de un informe en la aplicación](#EmbedReportJS).

<a name="EmbedReportToken"/>
### Obtención del token una aplicación para un informe

```
protected void getReportAppTokenButton_Click(object sender, EventArgs e)
{
    //Get an embed token for a report.
    var token = PowerBIToken.CreateReportEmbedToken(workspaceName, workspaceId, getReportAppTokenTextBox.Text);

    //After you get a PowerBIToken which has Claims including your WorkspaceName and WorkspaceID,
    //you generate JSON Web Token (JWT) . The Generate() method uses classes from System.IdentityModel.Tokens: SigningCredentials,
    //JwtSecurityToken, and JwtSecurityTokenHandler.
    string jwt = token.Generate(accessKey);

    //Set textbox to JWT string. The JavaScript embed code uses the embed jwt for a report.
    reportAppTokenTextbox.Text = jwt;
}
```

<a name="EmbedReportJS"/>
### Inserción de un informe en la aplicación

Para insertar un informe de **Power BI** en la aplicación, utilice un objeto IFrame y código JavaScript. A continuación se muestra un ejemplo del objeto IFrame y de código JavaScript para insertar un informe. Para ver todo el código de ejemplo para insertar un informe, consulte el ejemplo de [integración de un informe con un objeto IFrame](https://github.com/Azure-Samples/power-bi-embedded-iframe) en GitHub.

![Iframe](media\power-bi-embedded-integrate-report\Iframe.png)


```
window.onload = function () {
    // Client side click to embed a selected report.
    var el = document.getElementById("bEmbedReportAction");
    if (el.addEventListener) {
        el.addEventListener("click", updateEmbedReport, false);
    } else {
        el.attachEvent('onclick', updateEmbedReport);
    }

};

// Update embed report
function updateEmbedReport() {
    // Check if the embed url was selected
    var embedUrl = document.getElementById('tb_EmbedURL').value;

    // To load a report do the following:
    // 1: Set the url
    // 2: Add a onload handler to submit the auth token
    var iframe = document.getElementById('iFrameEmbedReport');
    iframe.src = embedUrl;
    iframe.onload = postActionLoadReport;
}

// Post the auth token to the iFrame.
function postActionLoadReport() {

    // Get the app token.
    accessToken = document.getElementById('MainContent_reportAppTokenTextbox').value;

    // Construct the push message structure
    var m = { action: "loadReport", accessToken: accessToken };
    message = JSON.stringify(m);

    // Push the message.
    iframe = document.getElementById('iFrameEmbedReport');
    iframe.contentWindow.postMessage(message, "*");
}
```
Cuando tenga un informe insertado en la aplicación, puede filtrar el informe. La sección siguiente le muestra cómo filtrar un informe mediante una sintaxis de dirección URL.

## Filtro de un informe

Puede filtrar un informe incrustado mediante una sintaxis de dirección URL. Para ello, agregue un parámetro de cadena de consulta a la dirección URL de src de iFrame con el filtro especificado. También puede **filtrar por un valor** y **ocultar el panel de filtro**.


**Filtrar por un valor**

Para filtrar por un valor, utilice una sintaxis de consulta de **$filter** con un operador **eq** como sigue:

```
https://app.powerbi.com/reportEmbed
?reportId=d2a0ea38-0694-...-ee9655d54a4a&
$filter={tableName/fieldName}%20eq%20'{fieldValue}'
```

Por ejemplo, puede filtrar por la cadena del almacén 'Lindseys'. La parte de filtro de la dirección URL tendría el siguiente aspecto:

```
$filter=Store/Chain%20eq%20'Lindseys'
```

> [AZURE.NOTE] {tableName/fieldName} no puede incluir espacios ni caracteres especiales. {fieldValue} acepta un único valor de categoría.

**Ocultar el panel de filtro**

Para ocultar el **panel de filtro**, agregue **filterPaneEnabled** a la cadena de consulta del informe como sigue:

```
&filterPaneEnabled=false
```

## Conclusión

En este artículo, se le ha presentado el código para integrar un informe de **Power BI** en la aplicación. Para empezar rápidamente a integrar un informe en una aplicación, descargue estos ejemplos en GitHub:

- [Integrate a report with an IFrame sample (Ejemplo de integración de un informe con un objeto IFrame)](https://github.com/Azure-Samples/power-bi-embedded-iframe)
- [Aplicación web de panel de ejemplo](http://go.microsoft.com/fwlink/?LinkId=761493)

## Otras referencias
- [Get started with Microsoft Power BI Embedded Preview (Introducción a la versión preliminar de Microsoft Power BI Embedded)](power-bi-embedded-get-started.md)
- [Get started with Microsoft Power BI Embedded sample (Introducción a Microsoft Power BI Embedded: ejemplo)](power-bi-embedded-get-started-sample.md)
- [System.IdentityModel.Tokens.SigningCredentials](https://msdn.microsoft.com/library/system.identitymodel.tokens.signingcredentials.aspx)
- [System.IdentityModel.Tokens.JwtSecurityToken](https://msdn.microsoft.com/library/system.identitymodel.tokens.jwtsecuritytoken.aspx)
- [System.IdentityModel.Tokens.JwtSecurityTokenHandler](https://msdn.microsoft.com/library/system.identitymodel.tokens.signingcredentials.aspx)
- [Get Reports](https://msdn.microsoft.com/library/mt711510.aspx)

<!---HONumber=AcomDC_0518_2016-->