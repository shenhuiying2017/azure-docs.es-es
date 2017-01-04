---
title: Procedimiento para usar Power BI Embedded con REST | Microsoft Docs
description: 'Aprenda a usar Power BI Embedded con REST. '
services: power-bi-embedded
documentationcenter: 
author: guyinacube
manager: erikre
editor: 
tags: 
ms.assetid: 8bcef780-cca0-4f30-9a9b-9daa1a7ae865
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 10/04/2016
ms.author: asaxton
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 7aadb6ceba1a8c672ed9eeea8651c965e5b665fd


---
# <a name="how-to-use-power-bi-embedded-with-rest"></a>Procedimiento para usar Power BI Embedded con REST
## <a name="power-bi-embedded-what-it-is-and-what-its-for"></a>Power BI Embedded: qué es y para qué sirve
En el sitio oficial de [Power BI Embedded](https://azure.microsoft.com/services/power-bi-embedded/)puede encontrar información general sobre este servicio, pero vamos a resumir brevemente sus aspectos principales antes de adentrarnos en los detalles sobre cómo utilizarlo con REST.

Es bastante sencillo. En ocasiones, un ISV quiere usar las visualizaciones de datos dinámicos de [Power BI](https://powerbi.microsoft.com) en su propia aplicación como bloques de creación de interfaz de usuario.

Sin embargo, como sabe, ya se pueden incrustar informes o iconos de Power BI en las páginas web sin necesidad de utilizar el servicio de Azure Power BI Embedded gracias a la **API de Power BI**. Cuando quiera compartir los informes en la misma organización, puede incrustar los informes con la autenticación de Azure AD. El usuario que vea los informes debe iniciar sesión con su propia cuenta de Azure AD. Cuando quiera compartir los informes con todos los usuarios (incluidos los externos), solo tendrá que incrustarlos con acceso anónimo.

No obstante, esta sencilla solución de incrustación no satisface las necesidades de una aplicación de ISV.
La mayoría de las aplicaciones de ISV tienen que entregar los datos de sus propios clientes, que no tienen por qué ser usuarios de su organización. Por ejemplo, si brinda algún servicio a la compañía A y B, los usuarios de la A solo deberían ver datos de su propia empresa. Es decir, deben habilitarse los servicios multiinquilino para realizar la entrega.

La aplicación de ISV también podría ofrecer sus propios métodos de autenticación, como la autenticación de formularios, la autenticación básica, etc. Después, la solución de incrustación debe colaborar con este método de autenticación existente de forma segura. También se requiere que los usuarios puedan usar estas aplicaciones de ISV sin comprar más licencias de suscripción de Power BI.

 **Power BI Embedded** se ha diseñado para, precisamente, estos tipos de escenarios de ISV. Por tanto, ahora que ya hemos visto una breve introducción de este servicio, pasemos a los detalles.

Puede utilizar el SDK de .NET \(C# o Node.js para compilar fácilmente una aplicación con Power BI Embedded. Sin embargo, en este artículo explicaremos el flujo HTTP \(incluida la autorización) de Power BI sin los SDK. Al entender este flujo, podrá compilar su aplicación **con cualquier lenguaje de programación** y comprender a fondo la esencia de Power BI Embedded.

## <a name="create-power-bi-workspace-collection-and-get-access-key-provisioning"></a>Creación de una colección de áreas de trabajo de Power BI y obtención de la clave de acceso \(aprovisionamiento)
Power BI Embedded es uno de los servicios de Azure. Solo al ISV que usa Azure Portal se le cobran cuotas de uso \(por sesión de usuario y hora); al usuario que ve el informe no se le cobra nada ni se le exige tener una suscripción de Azure.
Antes de comenzar a desarrollar nuestra aplicación, debemos crear la **colección de áreas de trabajo de Power BI** mediante el Portal de Azure.

Cada área de trabajo de Power BI Embedded es también la de cada cliente (inquilino); podemos agregar numerosas áreas de trabajo en cada colección de áreas de trabajo. En cada colección de áreas de trabajo se utiliza la misma clave de acceso. De hecho, la colección de áreas de trabajo constituye el límite de seguridad de Power BI Embedded.

![](media/power-bi-embedded-iframe/create-workspace.png)

Cuando termine de crear la colección de áreas de trabajo, copie la clave de acceso desde el Portal de Azure.

![](media/power-bi-embedded-iframe/copy-access-key.png)

> [!NOTE]
> También podemos aprovisionar la colección de áreas de trabajo y obtener la clave de acceso a través de la API de REST. Para obtener más información, consulte [Power BI Resource Provider APIs](https://msdn.microsoft.com/library/azure/mt712306.aspx)(API del proveedor de recursos de Power BI).
> 
> 

## <a name="create-pbix-file-with-power-bi-desktop"></a>Creación del archivo .pbix con Power BI Desktop
Después, tenemos que crear la conexión de datos y los informes que vamos a incrustar.
Para esta tarea no hay que agregar código ni realizar trabajos de programación; basta con usar Power BI Desktop.
En este artículo, no veremos los detalles de cómo usar Power BI Desktop. Si necesita más ayuda con este tema, consulte [Introducción a Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-getting-started/). En nuestro ejemplo, usaremos el [ejemplo Análisis de venta directa](https://powerbi.microsoft.com/documentation/powerbi-sample-datasets/).

![](media/power-bi-embedded-iframe/power-bi-desktop-1.png)

## <a name="create-a-power-bi-workspace"></a>Creación de un área de trabajo de Power BI
Ahora que hemos terminado de realizar el aprovisionamiento, comenzaremos creando el área de trabajo del cliente en la colección de áreas de trabajo a través de las API de REST. La siguiente solicitud POST HTTP (REST) va a crear la nueva área de trabajo en nuestra colección de áreas de trabajo existente. En nuestro ejemplo, el nombre de la colección de áreas de trabajo es **mypbiapp**.
Solo hay que establecer la clave de acceso, que copiamos anteriormente, como **AppKey**. Como podrá comprobar, se trata de una autenticación muy sencilla.

**Solicitud HTTP**

```
POST https://api.powerbi.com/v1.0/collections/mypbiapp/workspaces
Authorization: AppKey MpaUgrTv5e...
```

**Respuesta HTTP**

```
HTTP/1.1 201 Created
Content-Type: application/json; odata.metadata=minimal; odata.streaming=true
Location: https://wabi-us-east2-redirect.analysis.windows.net/v1.0/collections/mypbiapp/workspaces
RequestId: 4220d385-2fb3-406b-8901-4ebe11a5f6da

{
  "@odata.context": "http://wabi-us-east2-redirect.analysis.windows.net/v1.0/collections/mypbiapp/$metadata#workspaces/$entity",
  "workspaceId": "32960a09-6366-4208-a8bb-9e0678cdbb9d",
  "workspaceCollectionName": "mypbiapp"
}
```

El valor devuelto **workspaceId** se utiliza en las siguientes llamadas de API posteriores. Nuestra aplicación debe conservar este valor.

## <a name="import-pbix-file-into-the-workspace"></a>Importación del archivo .pbix en el área de trabajo
Cada área de trabajo puede hospedar un solo archivo de Power BI Desktop con un conjunto de datos \(incluida la configuración de los orígenes de datos) y los informes. Podemos importar el archivo .pbix al área de trabajo, tal y como se muestra en el código siguiente. Como puede observar, podemos cargar el binario del archivo .pbix utilizando varias partes MIME en HTTP.

El fragmento de URI **32960a09-6366-4208-a8bb-9e0678cdbb9d** es el id. del área de trabajo, y el parámetro de consulta **datasetDisplayName** es el nombre del conjunto de datos que se va a crear. El conjunto de datos creado contiene todos los artefactos relacionados con los datos del archivo .pbix, como los datos importados, el puntero al origen de datos, etc.

```
POST https://api.powerbi.com/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/imports?datasetDisplayName=mydataset01
Authorization: AppKey MpaUgrTv5e...
Content-Type: multipart/form-data; boundary="A300testx"

--A300testx
Content-Disposition: form-data

{the content (binary) of .pbix file}
--A300testx--
```

La ejecución de esta tarea de importación puede durar unos instantes. Cuando haya finalizado, nuestra aplicación puede solicitar el estado de la tarea mediante el id. de importación. En nuestro ejemplo, el id. de importación es **4eec64dd-533b-47c3-a72c-6508ad854659**.

```
HTTP/1.1 202 Accepted
Content-Type: application/json; charset=utf-8
Location: https://wabi-us-east2-redirect.analysis.windows.net/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/imports/4eec64dd-533b-47c3-a72c-6508ad854659?tenantId=myorg
RequestId: 658bd6b4-b68d-4ec3-8818-2a94266dc220

{"id":"4eec64dd-533b-47c3-a72c-6508ad854659"}
```

El siguiente código solicita el estado con este id. de importación:

```
GET https://api.powerbi.com/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/imports/4eec64dd-533b-47c3-a72c-6508ad854659
Authorization: AppKey MpaUgrTv5e...
```

Si la tarea no finaliza, la respuesta HTTP podría ser similar a la siguiente:

```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
RequestId: 614a13a5-4de7-43e8-83c9-9cd225535136

{
  "id": "4eec64dd-533b-47c3-a72c-6508ad854659",
  "importState": "Publishing",
  "createdDateTime": "2016-07-19T07:36:06.227",
  "updatedDateTime": "2016-07-19T07:36:06.227",
  "name": "mydataset01"
}
```

Si la tarea finaliza, la respuesta HTTP podría ser más parecida a esta:

```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
RequestId: eb2c5a85-4d7d-4cc2-b0aa-0bafee4b1606

{
  "id": "4eec64dd-533b-47c3-a72c-6508ad854659",
  "importState": "Succeeded",
  "createdDateTime": "2016-07-19T07:36:06.227",
  "updatedDateTime": "2016-07-19T07:36:06.227",
  "reports": [
    {
      "id": "2027efc6-a308-4632-a775-b9a9186f087c",
      "name": "mydataset01",
      "webUrl": "https://app.powerbi.com/reports/2027efc6-a308-4632-a775-b9a9186f087c",
      "embedUrl": "https://app.powerbi.com/appTokenReportEmbed?reportId=2027efc6-a308-4632-a775-b9a9186f087c"
    }
  ],
  "datasets": [
    {
      "id": "458e0451-7215-4029-80b3-9627bf3417b0",
      "name": "mydataset01",
      "tables": [
      ],
      "webUrl": "https://app.powerbi.com/datasets/458e0451-7215-4029-80b3-9627bf3417b0"
    }
  ],
  "name": "mydataset01"
}
```

## <a name="data-source-connectivity-and-multi-tenancy-of-data"></a>Conectividad de los orígenes de datos \(y servicio multiinquilino de datos)
Aunque casi todos los artefactos del archivo .pbix se importan en nuestra área de trabajo, no lo hacen las credenciales de los orígenes de datos. Como resultado, al utilizar el **modo DirectQuery**, no se mostrará correctamente el informe incrustado. Sin embargo, al usar el **modo de importación**, podremos ver el informe usando los datos importados existentes. En este caso, debemos establecer la credencial siguiendo los pasos que figuran abajo a través de llamadas REST.

En primer lugar, debe obtener el origen de datos de la puerta de enlace. Sabemos que el conjunto de datos **id** es el identificador que se devolvió antes.

**Solicitud HTTP**

```
GET https://api.powerbi.com/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/datasets/458e0451-7215-4029-80b3-9627bf3417b0/Default.GetBoundGatewayDatasources
Authorization: AppKey MpaUgrTv5e...
```

**Respuesta HTTP**

```
GET HTTP/1.1 200 OK
Content-Type: application/json; odata.metadata=minimal; odata.streaming=true
RequestId: 574b0b18-a6fa-46a6-826c-e65840cf6e15

{
  "@odata.context": "http://wabi-us-east2-redirect.analysis.windows.net/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/$metadata#gatewayDatasources",
  "value": [
    {
      "id": "5f7ee2e7-4851-44a1-8b75-3eb01309d0ea",
      "gatewayId": "ca17e77f-1b51-429b-b059-6b3e3e9685d1",
      "datasourceType": "Sql",
      "connectionDetails": "{\"server\":\"testserver.database.windows.net\",\"database\":\"testdb01\"}"
    }
  ]
}
```

Cuando obtengamos el id. del conjunto de datos y de la puerta de enlace \(consulte los identificadores **gatewayId** e **id** anteriores en el resultado devuelto), podremos cambiar la credencial de este origen de datos de la siguiente manera:

**Solicitud HTTP**

```
PATCH https://api.powerbi.com/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/gateways/ca17e77f-1b51-429b-b059-6b3e3e9685d1/datasources/5f7ee2e7-4851-44a1-8b75-3eb01309d0ea
Authorization: AppKey MpaUgrTv5e...
Content-Type: application/json; charset=utf-8

{
  "credentialType": "Basic",
  "basicCredentials": {
    "username": "demouser",
    "password": "P@ssw0rd"
  }
}
```

**Respuesta HTTP**

```
HTTP/1.1 200 OK
Content-Type: application/octet-stream
RequestId: 0e533c13-266a-4a9d-8718-fdad90391099
```

En el entorno de producción, también podemos establecer la cadena de conexión diferente para cada área de trabajo mediante la API de REST. \(es decir, podemos separar la base de datos de cada cliente).

El siguiente código va a cambiar la cadena de conexión del origen de datos a través de REST.

```
POST https://api.powerbi.com/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/datasets/458e0451-7215-4029-80b3-9627bf3417b0/Default.SetAllConnections
Authorization: AppKey MpaUgrTv5e...
Content-Type: application/json; charset=utf-8

{
  "connectionString": "data source=testserver02.database.windows.net;initial catalog=testdb02;persist security info=True;encrypt=True;trustservercertificate=False"
}
```

También podemos usar la característica de seguridad de nivel de fila de Power BI Embedded y separar los datos de cada uno de los usuarios en un informe. Como resultado, podemos aprovisionar cada informe de cliente con el mismo archivo .pbix \(interfaz de usuario, etc.) y distintos orígenes de datos.

> [!NOTE]
> Si está utilizando el **modo de importación** en lugar del **modo DirectQuery**, no se podrán actualizar modelos a través de la API. Además, en Power BI Embedded todavía no se admiten orígenes de datos locales a través de la puerta de enlace de Power BI. Sin embargo, le recomendamos que eche un vistazo al [blog de Power BI](https://powerbi.microsoft.com/blog/) para ver las novedades y los cambios que incorporarán las futuras versiones.
> 
> 

## <a name="authentication-and-hosting-embedding-reports-in-our-web-page"></a>Autenticación y hospedaje de informes (incrustación) en nuestra página web
En la API de REST anterior, podemos usar la clave de acceso **AppKey** como encabezado de autorización. Como estas llamadas pueden controlarse en el servidor backend, se trata de un método seguro.

Sin embargo, cuando incrustamos el informe en nuestra página web, este tipo de información de seguridad se controlaría con JavaScript \(front-end). Después, debe protegerse el valor del encabezado de autorización. Si un código o un usuario malintencionados averiguan nuestra clave de acceso, pueden llamar a cualquier operación con esta clave.

Cuando incrustamos el informe se incrusta en nuestra página web, debemos utilizar el token calculado en lugar de la clave de acceso **AppKey**. Nuestra aplicación debe crear el token JSON Web Token \(JWT) de OAuth, que consta de las notificaciones y la firma digital calculada. Tal y como se muestra a continuación, este JWT de OAuth es un token de cadena codificada delimitada por puntos.

![](media/power-bi-embedded-iframe/oauth-jwt.png)

En primer lugar, debemos preparar el valor de entrada, que se firma más adelante. Este valor es la cadena con codificación URL en formato Base64 (rfc4648) del siguiente JSON, y está delimitada por el carácter de punto \(.). del informe.

> [!NOTE]
> Si quiere usar la característica de seguridad de nivel de fila (RLS) con Power BI Embedded, debe especificar también el **nombre de usuario** y los **roles** en las notificaciones.
> 
> 

```
{
  "typ":"JWT",
  "alg":"HS256"
}
```

```
{
  "wid":"{workspace id}",
  "rid":"{report id}",
  "wcn":"{workspace collection name}",
  "iss":"PowerBISDK",
  "ver":"0.2.0",
  "aud":"https://analysis.windows.net/powerbi/api",
  "nbf":{start time of token expiration},
  "exp":{end time of token expiration}
}
```

Después, deberemos crear la cadena codificada en Base64 de HMAC \(la firma) con el algoritmo SHA256. Este valor de entrada firmado es la cadena anterior.

Por último, debemos combinar la cadena de valor y la cadena de firma con el carácter de punto \(.). La cadena completa es el token de aplicación para la incrustación de informes. Aunque un usuario malintencionado averigüe el token de aplicación, no podrá obtener la clave de acceso original. Este token de aplicación expira rápidamente.

Este es un ejemplo PHP de estos pasos:

```
<?php
// 1. power bi access key
$accesskey = "MpaUgrTv5e...";

// 2. construct input value
$token1 = "{" .
  "\"typ\":\"JWT\"," .
  "\"alg\":\"HS256\"" .
  "}";
$token2 = "{" .
  "\"wid\":\"32960a09-6366-4208-a8bb-9e0678cdbb9d\"," . // workspace id
  "\"rid\":\"2027efc6-a308-4632-a775-b9a9186f087c\"," . // report id
  "\"wcn\":\"mypbiapp\"," . // workspace collection name
  "\"iss\":\"PowerBISDK\"," .
  "\"ver\":\"0.2.0\"," .
  "\"aud\":\"https://analysis.windows.net/powerbi/api\"," .
  "\"nbf\":" . date("U") . "," .
  "\"exp\":" . date("U" , strtotime("+1 hour")) .
  "}";
$inputval = rfc4648_base64_encode($token1) .
  "." .
  rfc4648_base64_encode($token2);

// 3. get encoded signature
$hash = hash_hmac("sha256",
    $inputval,
    $accesskey,
    true);
$sig = rfc4648_base64_encode($hash);

// 4. show result (which is the apptoken)
$apptoken = $inputval . "." . $sig;
echo($apptoken);

// helper functions
function rfc4648_base64_encode($arg) {
  $res = $arg;
  $res = base64_encode($res);
  $res = str_replace("/", "_", $res);
  $res = str_replace("+", "-", $res);
  $res = rtrim($res, "=");
  return $res;
}
?>
```

## <a name="finally-embed-the-report-into-the-web-page"></a>Incrustación del informe en la página web (último paso)
Para incrustar el informe, debe obtener la URL de incrustación y el **id.** del informe mediante la siguiente API de REST.

**Solicitud HTTP**

```
GET https://api.powerbi.com/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/reports
Authorization: AppKey MpaUgrTv5e...
```

**Respuesta HTTP**

```
HTTP/1.1 200 OK
Content-Type: application/json; odata.metadata=minimal; odata.streaming=true
RequestId: d4099022-405b-49d3-b3b7-3c60cf675958

{
  "@odata.context": "http://wabi-us-east2-redirect.analysis.windows.net/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/$metadata#reports",
  "value": [
    {
      "id": "2027efc6-a308-4632-a775-b9a9186f087c",
      "name": "mydataset01",
      "webUrl": "https://app.powerbi.com/reports/2027efc6-a308-4632-a775-b9a9186f087c",
      "embedUrl": "https://embedded.powerbi.com/appTokenReportEmbed?reportId=2027efc6-a308-4632-a775-b9a9186f087c",
      "isFromPbix": false
    }
  ]
}
```

Podemos incrustar el informe en nuestra aplicación web con el token de aplicación anterior.
Si observamos el código de ejemplo siguiente, la primera parte es la misma que la del ejemplo anterior. En la última parte, este ejemplo muestra el elemento **embedUrl** \(consulte el resultado anterior) del iframe y va a publicar el token de aplicación en dicho iframe.

> [!NOTE]
> Tendrá que cambiar el valor del id. del informe a uno suyo. Además, debido a un error de nuestro sistema de gestión de contenidos, la etiqueta iframe del ejemplo de código se lee literalmente. Quite el texto en mayúsculas de la etiqueta si va a copiar y pegar este código de ejemplo.
> 
> 

```
    <?php
    // 1. power bi access key
    $accesskey = "MpaUgrTv5e...";

    // 2. construct input value
    $token1 = "{" .
      "\"typ\":\"JWT\"," .
      "\"alg\":\"HS256\"" .
      "}";
    $token2 = "{" .
      "\"wid\":\"32960a09-6366-4208-a8bb-9e0678cdbb9d\"," . // workspace id
      "\"rid\":\"2027efc6-a308-4632-a775-b9a9186f087c\"," . // report id
      "\"wcn\":\"mypbiapp\"," . // workspace collection name
      "\"iss\":\"PowerBISDK\"," .
      "\"ver\":\"0.2.0\"," .
      "\"aud\":\"https://analysis.windows.net/powerbi/api\"," .
      "\"nbf\":" . date("U") . "," .
      "\"exp\":" . date("U" , strtotime("+1 hour")) .
      "}";
    $inputval = rfc4648_base64_encode($token1) .
      "." .
      rfc4648_base64_encode($token2);

    // 3. get encoded signature value
    $hash = hash_hmac("sha256",
        $inputval,
        $accesskey,
        true);
    $sig = rfc4648_base64_encode($hash);

    // 4. get apptoken
    $apptoken = $inputval . "." . $sig;

    // helper functions
    function rfc4648_base64_encode($arg) {
      $res = $arg;
      $res = base64_encode($res);
      $res = str_replace("/", "_", $res);
      $res = str_replace("+", "-", $res);
      $res = rtrim($res, "=");
      return $res;
    }
    ?>
    <!DOCTYPE html>
    <html>
    <head>
      <meta charset="utf-8" />
      <meta http-equiv="X-UA-Compatible" content="IE=edge">
      <title>Test page</title>
      <meta name="viewport" content="width=device-width, initial-scale=1">
    </head>
    <body>
      <button id="btnView">View Report !</button>
      <div id="divView">
        <**REMOVE THIS CAPPED TEXT IF COPIED** iframe id="ifrTile" width="100%" height="400"></iframe>
      </div>
      <script>
        (function () {
          document.getElementById('btnView').onclick = function() {
            var iframe = document.getElementById('ifrTile');
            iframe.src = 'https://embedded.powerbi.com/appTokenReportEmbed?reportId=2027efc6-a308-4632-a775-b9a9186f087c';
            iframe.onload = function() {
              var msgJson = {
                action: "loadReport",
                accessToken: "<?=$apptoken?>",
                height: 500,
                width: 722
              };
              var msgTxt = JSON.stringify(msgJson);
              iframe.contentWindow.postMessage(msgTxt, "*");
            };
          };
        }());
      </script>
    </body>
```

Este es el resultado:

![](media/power-bi-embedded-iframe/view-report.png)

En este momento, Power BI Embedded solo muestra el informe en el iframe. No obstante, eche un vistazo al [blog de Power BI](). En futuras mejoras se podrán usar nuevas API del lado cliente con la que podremos enviar información en el iframe, además de extraer datos. Sin duda, una característica realmente útil.

## <a name="see-also"></a>Otras referencias
* [Autenticación y autorización con Power BI Embedded](power-bi-embedded-app-token-flow.md)




<!--HONumber=Nov16_HO3-->


