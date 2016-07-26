<properties
   pageTitle="Microsoft Power BI Embedded: inserción de un informe de Power BI con un objeto IFrame"
   description="Microsoft Power BI Embedded: código esencial para integrar un informe en la aplicación, cómo autenticarse con el token de aplicación de Power BI Embedded, cómo obtener informes"
   services="power-bi-embedded"
   documentationCenter=""
   authors="minewiskan"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="power-bi-embedded"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="powerbi"
   ms.date="07/19/2016"
   ms.author="owend"/>

# Inserción de un informe de Power BI con un iframe
En este artículo se muestra código esencial para usar la API de REST de **Power BI Embedded**, los tokens de aplicación, un IFrame y algo de código JavaScript para integrar o insertar un informe en la aplicación.

En [Introducción a Microsoft Power BI Embedded](power-bi-embedded-get-started.md), aprenderá a configurar una **colección de áreas de trabajo** para que incluya una o varias **áreas de trabajo** con el contenido del informe. Después, en [Introducción al ejemplo de Microsoft Power BI Embedded](power-bi-embedded-get-started-sample.md), importe un informe en un **área de trabajo**.

En este artículo se describen los pasos necesario para insertar un informe en la aplicación. Para seguir este artículo, debe descargar el ejemplo de [integración de un informe con un IFrame](https://github.com/Azure-Samples/power-bi-embedded-iframe) en GitHub. Este ejemplo es una sencilla aplicación de formulario web de ASP.NET cuyo objetivo es describir el código C# y JavaScript esencial necesario para integrar un informe. Para un ejemplo más avanzado que utiliza el patrón de diseño Model-View-Controller (MVC) para integrar un informe, consulte la [aplicación web de panel de ejemplo](http://go.microsoft.com/fwlink/?LinkId=761493) en GitHub.

Estos son los pasos necesarios para integrar un informe:

- Paso 1: [Obtención de un informe en un área de trabajo](#GetReport). En este paso, utilizará un flujo de tokens de aplicación para obtener un token de acceso que llame a la operación de REST [Get Reports](https://msdn.microsoft.com/library/mt711510.aspx). Si obtiene un informe desde la lista **Get Reports**, podrá insertarlo en una aplicación utilizando un elemento **IFrame**.
- Paso 2: [Inserción de un informe en una aplicación](#EmbedReport). En este paso, va a usar un token que se inserta en un informe, algo de código JavaScript y un objeto IFrame para integrar, o insertar, un informe en una aplicación web.

Si desea ejecutar el ejemplo de [integración de un informe con un objeto IFrame](https://github.com/Azure-Samples/power-bi-embedded-iframe), descárguelo de GitHub y configure estas tres opciones de Web.Config:

- **AccessKey**: los objetos **AccessKey** se utilizan para generar una instancia de JSON Web Token (JWT), que se emplea para obtener los informes e insertar uno de ellos.
- **Nombre de la colección de áreas de trabajo**: identifica el área de trabajo.
- **Id. del área de trabajo**: identificador único del área de trabajo.

Para más información acerca de cómo obtener una clave de acceso, el nombre de una colección de áreas de trabajo y un identificador de área de trabajo en el Portal de Azure, consulte la [introducción a Microsoft Power BI Embedded](power-bi-embedded-get-started.md).

<a name="GetReport"/>
## Obtención de un informe en un área de trabajo

Para integrar un informe en una aplicación, necesita el **identificador** y la dirección **embedUrl** de un informe. Para ello, debe llamar a la operación REST [Get Reports](https://msdn.microsoft.com/library/mt711510.aspx) y elegir un informe en la lista JSON.

### Respuesta JSON de Get Reports
```
{
  "@odata.context":"https://api.powerbi.com/v1.0/collections/{WorkspaceName}/workspaces/{WorkspaceId}/$metadata#reports","value":[
    {
      "id":"804d3664-…-e71882055dba","name":"Import report sample","webUrl":"https://embedded.powerbi.com/reports/804d3664-...-e71882055dba","embedUrl":"https://embedded.powerbi.com/appTokenReportEmbed?reportId=804d3664-...-e71882055dba"
    },{
      "id":"1d7cff58-…-380610e263a9","name":"Sample Report 2","webUrl":"https://embedded.powerbi.com/reports/1d7cff58-...-380610e263a9","embedUrl":"https://embedded.powerbi.com/appTokenReportEmbed?reportId=1d7cff58-...-380610e263a9"
    }
  ]
}

```

Para llamar a la operación REST [Get Reports](https://msdn.microsoft.com/library/mt711510.aspx), debe utilizar un token de aplicación. Para más información, consulte [Autenticación y autorización con Power BI Embedded](power-bi-embedded-app-token-flow.md). El código siguiente describe cómo obtener una lista JSON de informes.

```
protected void getReportsButton_Click(object sender, EventArgs e)
{
    //Construct reports uri resource string
    var uri = String.Format("https://api.powerbi.com/v1.0/collections/{0}/workspaces/{1}/reports", workspaceName, workspaceId);

    //Configure reports request
    System.Net.WebRequest request = System.Net.WebRequest.Create(uri) as System.Net.HttpWebRequest;
    request.Method = "GET";
    request.ContentLength = 0;

    //Set the WebRequest header to AppToken, and jwt
    //Note the use of AppToken instead of Bearer
    request.Headers.Add("Authorization", String.Format("AppKey {0}", accessKey));

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

Antes de poder insertar un informe en la aplicación, necesita un token de inserción para un informe. Este token es similar a los tokens de aplicación que se usan para llamar a las operaciones REST de Power BI Embedded salvo porque, en lugar de generarse para un recurso de REST, se genera para un recurso de informe. Lo siguiente es el código para obtener un token de aplicación para un informe.

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

Para incrustar un informe de **Power BI** en la aplicación, puede utilizar un objeto IFrame y código JavaScript. A continuación se muestra un ejemplo del objeto IFrame y de código JavaScript para insertar un informe. Si desea ver todo el código de ejemplo que se utiliza para incrustar un informe, consulte el ejemplo de [integración de un informe con un objeto IFrame](https://github.com/Azure-Samples/power-bi-embedded-iframe) en GitHub.

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

Para filtrar por un valor, utilice una sintaxis de consulta de **$filter** con un operador **eq** tal y como se indica a continuación:

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

Para ocultar el **panel de filtro**, agregue **filterPaneEnabled** a la cadena de consulta del informe tal y como se indica a continuación:

```
&filterPaneEnabled=false
```

## Recursos adicionales

En este artículo, se ha presentado el código necesario para integrar un informe de **Power BI** en la aplicación. No olvide consultar estos otros ejemplos de GitHub:

- [Integrate a report with an IFrame sample (Ejemplo de integración de un informe con un objeto IFrame)](https://github.com/Azure-Samples/power-bi-embedded-iframe)
- [Aplicación web de panel de ejemplo](http://go.microsoft.com/fwlink/?LinkId=761493)

## Otras referencias
- [System.IdentityModel.Tokens.SigningCredentials](https://msdn.microsoft.com/library/system.identitymodel.tokens.signingcredentials.aspx)
- [System.IdentityModel.Tokens.JwtSecurityToken](https://msdn.microsoft.com/library/system.identitymodel.tokens.jwtsecuritytoken.aspx)
- [System.IdentityModel.Tokens.JwtSecurityTokenHandler](https://msdn.microsoft.com/library/system.identitymodel.tokens.signingcredentials.aspx)

<!---HONumber=AcomDC_0720_2016-->