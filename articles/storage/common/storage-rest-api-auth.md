---
title: "Llamada a las operaciones de API de REST de Azure Storage Services que incluyen autenticación | Microsoft Docs"
description: "Llamada a las operaciones de API de REST de Azure Storage Services que incluyen autenticación"
services: storage
documentationcenter: na
author: robinsh
manager: timlt
ms.assetid: f4704f58-abc6-4f89-8b6d-1b1659746f5a
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 11/27/2017
ms.author: robinsh
ms.openlocfilehash: 521487c3ed38f191308e14e4d542358438945556
ms.sourcegitcommit: 562a537ed9b96c9116c504738414e5d8c0fd53b1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/12/2018
---
# <a name="using-the-azure-storage-rest-api"></a>Uso de la API de REST de Azure Storage

En este artículo se muestra cómo usar la API de REST del servicio Blob Storage y cómo autenticar la llamada al servicio. Se ha escrito desde el punto de vista de alguien que no sabe nada sobre REST y no tiene idea de cómo realizar una llamada a REST, pero es un desarrollador. Se examinó la documentación de referencia de una llamada de REST y se vio cómo traducirla a una llamada de REST real: ¿qué campos van en cada sitio? Después de aprender a configurar una llamada de REST, puede aprovechar este conocimiento para usar cualquier otra API de REST del servicio de almacenamiento.

## <a name="prerequisites"></a>Requisitos previos 

La aplicación muestra los contenedores de almacenamiento de blobs de una cuenta de almacenamiento. Para probar el código de este artículo, necesita los siguientes elementos: 

* Instale [Visual Studio 2017](https://www.visualstudio.com/visual-studio-homepage-vs.aspx) con la carga de trabajo siguiente:
    - Desarrollo de Azure

* Una suscripción de Azure. Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

* Una cuenta de almacenamiento de uso general. Si aún no dispone de una cuenta de almacenamiento, consulte [Create a storage account](storage-quickstart-create-account.md) (Creación de una cuenta de almacenamiento).

* En el ejemplo de este artículo se indica cómo mostrar los contenedores de una cuenta de almacenamiento. Para ver la salida, agregue algunos contenedores al almacenamiento de blobs en la cuenta de almacenamiento antes de comenzar.

## <a name="download-the-sample-application"></a>Descarga de la aplicación de ejemplo

La aplicación de ejemplo es una aplicación de consola escrita en C#.

Use [git](https://git-scm.com/) para descargar una copia de la aplicación en su entorno de desarrollo. 

```bash
git clone https://github.com/Azure-Samples/storage-dotnet-rest-api-with-auth.git
```

Este comando clona el repositorio en la carpeta git local. Para abrir la solución de Visual Studio, busque la carpeta storage-dotnet-rest-api-with-auth, ábrala y haga doble clic en StorageRestApiAuth.sln. 

## <a name="why-do-i-need-to-know-rest"></a>¿Por qué es necesario conocer REST?

Tener conocimientos sobre cómo usar REST resulta útil. El equipo de producto de Azure publica con frecuencia nuevas características. En muchas ocasiones, se accede a las nuevas características mediante la interfaz de REST, pero aún no se han traído a la superficie a través de **todas** las bibliotecas de cliente de almacenamiento o la interfaz de usuario (como en el caso de Azure Portal). Si siempre va a querer usar lo más último y mejor, aprender REST es un requisito. Además, si quiere escribir su propia biblioteca para interactuar con Azure Storage, o quiere acceder a Azure Storage con un lenguaje de programación que no tenga un SDK o una biblioteca de cliente de almacenamiento, puede usar la API de REST.

## <a name="what-is-rest"></a>¿Qué es REST?

REST significa *transferencia de estado representacional*. Para ver una definición específica, consulte la [Wikipedia](http://en.wikipedia.org/wiki/Representational_state_transfer).

Básicamente, REST es una arquitectura que puede usar para llamar a las API o permitir que las API estén disponibles para que se llamen. Es independiente de lo que sucede en cada lado, y de lo que haga otro software que se use al enviar o recibir llamadas de REST. Puede escribir una aplicación que se ejecute en un equipo Mac, Windows o Linux, en una tableta o un teléfono Android, o en un iPhone, un iPod o un sitio web, y usar la misma API de REST en todas esas plataformas. Los datos se pueden pasar como entrada o salida cuando se llama a la API de REST. A la API de REST no le importa cuál sea la plataforma a la que se llame; lo que importa es la información que se pasa a la solicitud y los datos proporcionados en la respuesta.

## <a name="heres-the-plan"></a>Este es el plan

El proyecto de ejemplo muestra los contenedores de una cuenta de almacenamiento. Una vez que comprenda cómo la información de la documentación de la API de REST se correlaciona con el código real, resulta más fácil determinar otras llamadas de REST. 

Si examina la [API de REST de Blob service](/rest/api/storageservices/fileservices/Blob-Service-REST-API), verá todas las operaciones que puede realizar en el almacenamiento de blobs. Las bibliotecas de cliente de almacenamiento son contenedores de las API de REST, que permiten acceder de forma fácil al almacenamiento sin usar directamente las API de REST. Pero como se ha mencionado anteriormente, en ocasiones querrá usar la API de REST en lugar de una biblioteca de cliente de almacenamiento.

## <a name="rest-api-reference-list-containers-api"></a>Referencia de la API de REST: API List Containers

Echemos un vistazo a la página de la referencia de API de REST de la operación [ListContainers](/rest/api/storageservices/fileservices/List-Containers2) para que comprenda de dónde vienen algunos de los campos en la solicitud y respuesta de la sección siguiente con el código.

**Método de solicitud**: GET. Este verbo es el método HTTP que se especifica como una propiedad del objeto de solicitud. Otros valores de este verbo incluyen HEAD, PUT y DELETE, según la API a la que llame.

El **URI de solicitud**: https://myaccount.blob.core.windows.net/?comp=list, se crea desde el punto de conexión de la cuenta de almacenamiento de blobs `http://myaccount.blob.core.windows.net` y la cadena de recurso `/?comp=list`.

[Parámetros de URI](/rest/api/storageservices/fileservices/List-Containers2#uri-parameters): hay parámetros de consulta adicionales que puede usar al llamar a ListContainers. Dos de estos parámetros son *timeout* para la llamada (en segundos) y *prefix*, que se usa para el filtrado.

Otro parámetro útil es *maxresults:* si hay un número de contenedores disponibles superior a este valor, el cuerpo de respuesta contendrá un elemento *NextMarker* que indica el siguiente contenedor que se devuelve en la solicitud siguiente. Para usar esta característica, proporcionará el valor *NextMarker* como el parámetro *marker* en el identificador URI cuando realice la solicitud siguiente. El uso de esta característica es análogo a la paginación a través de los resultados. 

Para usar parámetros adicionales, anéxelos a la cadena de recurso con el valor, como en este ejemplo:

```
/?comp=list&timeout=60&maxresults=100
```

[Encabezados de solicitud](/rest/api/storageservices/fileservices/List-Containers2#request-headers)**:** en esta sección se muestran los encabezados de solicitud obligatorios y opcionales. Se requieren tres de los encabezados: un encabezado de *autorización*, *x-ms-date* (contiene la hora UTC de la solicitud) y *x-ms-version* (especifica la versión de la API de REST que se usará). Incluir *x-ms-client-request-id* en los encabezados es opcional; el valor de este campo se puede establecer en nada, y se escribe en los registros de análisis de almacenamiento cuando se habilita el registro.

[Cuerpo de la solicitud](/rest/api/storageservices/fileservices/List-Containers2#request-body)**:** no hay ningún cuerpo de solicitud para ListContainers. El cuerpo de solicitud se usa en todas las operaciones PUT al cargar los blobs, así como en SetContainerAccessPolicy, que permite enviar una lista de XML de directivas de acceso almacenadas que se aplicarán. Las directivas de acceso almacenadas se describen en el artículo [Uso de firmas de acceso compartido (SAS)](storage-dotnet-shared-access-signature-part-1.md).

[Código de estado de respuesta](/rest/api/storageservices/fileservices/List-Containers2#status-code)**:** indica los códigos de estado que necesita saber. En este ejemplo, un código de estado HTTP de 200 es correcto. Para ver una lista completa de códigos de estado HTTP, consulte [Status Code Definitions](http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) (Definiciones de código de estado). Para ver los códigos de error específicos de las API de REST de almacenamiento, consulte [Common REST API error codes](/rest/api/storageservices/common-rest-api-error-codes) (Códigos de error comunes de API de REST).

[Encabezados de respuesta](/rest/api/storageservices/fileservices/List-Containers2#response-headers)**:** incluyen *Tipo de contenido*; *x-ms-request-id* (el identificador de solicitud que pasó como entrada, si es aplicable); *x-ms-version* (indica la versión de Blob service usada) y *Fecha* (UTC, indica la hora a la que se realizó la solicitud).

[Cuerpo de respuesta](/rest/api/storageservices/fileservices/List-Containers2#response-body): este campo es una estructura XML que proporciona los datos solicitados. En este ejemplo, la respuesta es una lista de contenedores y sus propiedades.

## <a name="creating-the-rest-request"></a>Creación de la solicitud de REST

Un par de notas antes de empezar: por motivos de seguridad cuando se ejecute en producción, use siempre HTTPS en lugar de HTTP. En este ejercicio, debe usar HTTP para que pueda ver los datos de solicitud y respuesta. Para ver la información de solicitud y respuesta en las llamadas de REST reales, puede descargar [Fiddler](http://www.telerik.com/fiddler) o una aplicación similar. En la solución de Visual Studio, el nombre y la clave de la cuenta de almacenamiento están codificados de forma rígida en la clase, y el método ListContainersAsyncREST pasa el nombre y la clave de la cuenta de almacenamiento a los métodos que se usan para crear los diversos componentes de la solicitud de REST. En una aplicación real, el nombre y la clave de la cuenta de almacenamiento residirían en un archivo de configuración, en las variables de entorno o se recuperarían de una instancia de Azure Key Vault.

En nuestro proyecto de ejemplo, el código para crear el encabezado de autorización está en una clase independiente, con la idea de que pueda tomar la clase entera y agregarla a su propia solución y usarla "tal cual". El código del encabezado de autorización funciona con la mayoría de las llamadas de la API de REST a Azure Storage.

Para crear la solicitud, que se encuentra en un objeto HttpRequestMessage, vaya a ListContainersAsyncREST en Program.cs. Los pasos para crear la solicitud son: 

* Cree el URI que se usará para llamar al servicio. 
* Cree el objeto HttpRequestMessage y establezca la carga. La carga es nula para ListContainersAsyncREST porque no estamos pasando nada como entrada.
* Agregue los encabezados de solicitud para x-ms-date y x-ms-version.
* Obtenga el encabezado de autorización y agréguelo.

Alguna información básica que necesita: 

*  Para ListContainers, el **método** es `GET`. Este valor se establece cuando se crea una instancia de la solicitud. 
*  El **recurso** es la parte de la consulta del URI que indica cuál es la API a la que se llama, de modo que el valor es `/?comp=list`. Como se indicó anteriormente, el recurso está en la página de documentación de referencia que muestra la información sobre la [API ListContainers](/rest/api/storageservices/fileservices/List-Containers2).
*  El URI se construye mediante la creación del punto de conexión de Blob service para esa cuenta de almacenamiento y la concatenación del recurso. El valor del **URI de solicitud** acaba siendo `http://contosorest.blob.core.windows.net/?comp=list`.
*  Con ListContainers, **requestBody** es nulo y no hay ningún **encabezado** adicional.

Distintas API pueden tener otros parámetros para pasar como entrada, por ejemplo *ifMatch*. Un ejemplo de dónde podría usar ifMatch es al llamar a PutBlob. En ese caso, se establece ifMatch en un valor eTag, y solo se actualiza el blob si el valor eTag que se proporciona coincide con el valor eTag actual en el blob. Si alguien más ha actualizado el blob desde que se recuperó el valor eTag, sus cambios se invalidarán. 

En primer lugar, establezca los valores `uri` y `payload`. 

```csharp
// Construct the URI. This will look like this:
//   https://myaccount.blob.core.windows.net/resource
String uri = string.Format("http://{0}.blob.core.windows.net?comp=list", storageAccountName);

// Set this to whatever payload you desire. Ours is null because 
//   we're not passing anything in.
Byte[] requestPayload = null;
```

A continuación, cree una instancia de la solicitud; para ello, establezca el método en `GET` y proporcione el URI.

```csharp 
//Instantiate the request message with a null payload.
using (var httpRequestMessage = new HttpRequestMessage(HttpMethod.Get, uri)
{ Content = (requestPayload == null) ? null : new ByteArrayContent(requestPayload) })
{
```

Agregue los encabezados de solicitud para x-ms-date y x-ms-version. Este lugar en el código también es donde se agregan los encabezados de solicitud adicionales necesarios para la llamada. En este ejemplo, no hay ningún encabezado adicional. Un ejemplo de una API que pasa encabezados adicionales como entrada es SetContainerACL. Con Blob Storage, se agrega un encabezado llamado "x-ms-blob-public-access" y el valor del nivel de acceso.

```csharp
    // Add the request headers for x-ms-date and x-ms-version.
    DateTime now = DateTime.UtcNow;
    httpRequestMessage.Headers.Add("x-ms-date", now.ToString("R", CultureInfo.InvariantCulture));
    httpRequestMessage.Headers.Add("x-ms-version", "2017-04-17");
    // If you need any additional headers, add them here before creating
    //   the authorization header. 
```

Llame al método que crea el encabezado de autorización y agréguelo a los encabezados de solicitud. Verá cómo crear el encabezado de autorización más adelante en el artículo. El nombre del método es GetAuthorizationHeader, que puede ver en este fragmento de código:

```csharp
    // Get the authorization header and add it.
    httpRequestMessage.Headers.Authorization = AzureStorageAuthenticationHelper.GetAuthorizationHeader(
        storageAccountName, storageAccountKey, now, httpRequestMessage);
```

En este momento, `httpRequestMessage` contiene la solicitud REST completa con los encabezados de autorización. 

## <a name="call-the-rest-api-with-the-request"></a>Llamada a la API de REST con la solicitud

Ahora que tiene la solicitud, puede llamar a SendAsync para enviar la solicitud de REST. SendAsync llama a la API y recibe la respuesta. Examine la respuesta StatusCode (200 es correcto) y, a continuación, analícela. En este caso, se obtiene una lista XML de contenedores. Vamos a examinar el código para llamar al método GetRESTRequest y crear la solicitud, ejecutar la solicitud y luego examinar la respuesta de la lista de contenedores.

```csharp 
    // Send the request.
    using (HttpResponseMessage httpResponseMessage = 
      await new HttpClient().SendAsync(httpRequestMessage, cancellationToken))
    {
        // If successful (status code = 200), 
        //   parse the XML response for the container names.
        if (httpResponseMessage.StatusCode == HttpStatusCode.OK)
        {
            String xmlString = await httpResponseMessage.Content.ReadAsStringAsync();
            XElement x = XElement.Parse(xmlString);
            foreach (XElement container in x.Element("Containers").Elements("Container"))
            {
                Console.WriteLine("Container name = {0}", container.Element("Name").Value);
            }
        }
    }
}
```

Si ejecuta un rastreador de red como [Fiddler](https://www.telerik.com/fiddler) al realizar la llamada a SendAsync, puede ver la información de solicitud y respuesta. Vamos a echar un vistazo. El nombre de la cuenta de almacenamiento es *contosorest*.

**Solicitud:**

```
GET /?comp=list HTTP/1.1
```

**Encabezados de solicitud:**

```
x-ms-date: Thu, 16 Nov 2017 23:34:04 GMT
x-ms-version: 2014-02-14
Authorization: SharedKey contosorest:1dVlYJWWJAOSHTCPGiwdX1rOS8B4fenYP/VrU0LfzQk=
Host: contosorest.blob.core.windows.net
Connection: Keep-Alive
```

**Encabezados de solicitud y respuesta devueltos tras la ejecución:**

```
HTTP/1.1 200 OK
Content-Type: application/xml
Server: Windows-Azure-Blob/1.0 Microsoft-HTTPAPI/2.0
x-ms-request-id: 3e889876-001e-0039-6a3a-5f4396000000
x-ms-version: 04-17
Date: Fri, 17 Nov 2017 00:23:42 GMT
Content-Length: 1511
```

**Cuerpo de respuesta (XML):** con ListContainers, muestra la lista de contenedores y sus propiedades.

```xml  
<?xml version="1.0" encoding="utf-8"?>
<EnumerationResults 
  ServiceEndpoint="http://contosorest.blob.core.windows.net/">
  <Containers>
    <Container>
      <Name>container-1</Name>
      <Properties>
        <Last-Modified>Thu, 16 Mar 2017 22:39:48 GMT</Last-Modified>
        <Etag>"0x8D46CBD5A7C301D"</Etag>
        <LeaseStatus>unlocked</LeaseStatus>
        <LeaseState>available</LeaseState>
      </Properties>
    </Container>
    <Container>
      <Name>container-2</Name>
      <Properties>
        <Last-Modified>Thu, 16 Mar 2017 22:40:50 GMT</Last-Modified>
        <Etag>"0x8D46CBD7F49E9BD"</Etag>
        <LeaseStatus>unlocked</LeaseStatus>
        <LeaseState>available</LeaseState>
      </Properties>
    </Container>
    <Container>
      <Name>container-3</Name>
      <Properties>
        <Last-Modified>Thu, 16 Mar 2017 22:41:10 GMT</Last-Modified>
        <Etag>"0x8D46CBD8B243D68"</Etag>
        <LeaseStatus>unlocked</LeaseStatus>
        <LeaseState>available</LeaseState>
      </Properties>
    </Container>
    <Container>
      <Name>container-4</Name>
      <Properties>
        <Last-Modified>Thu, 16 Mar 2017 22:41:25 GMT</Last-Modified>
        <Etag>"0x8D46CBD93FED46F"</Etag>
        <LeaseStatus>unlocked</LeaseStatus>
        <LeaseState>available</LeaseState>
        </Properties>
      </Container>
      <Container>
        <Name>container-5</Name>
        <Properties>
          <Last-Modified>Thu, 16 Mar 2017 22:41:39 GMT</Last-Modified>
          <Etag>"0x8D46CBD9C762815"</Etag>
          <LeaseStatus>unlocked</LeaseStatus>
          <LeaseState>available</LeaseState>
        </Properties>
      </Container>
  </Containers>
  <NextMarker />
</EnumerationResults>
```

Ahora que comprende cómo crear la solicitud, llamar al servicio y analizar los resultados, veamos cómo crear el encabezado de autorización. Crear ese encabezado es complicado, pero lo bueno es que una vez que tenga el código en funcionamiento, sirve para todas las API de REST de servicios de almacenamiento.

## <a name="creating-the-authorization-header"></a>Creación del encabezado de autorización

Hay un artículo que explica los conceptos (no el código) de cómo realizar la [autenticación para Azure Storage Services](/rest/api/storageservices/fileservices/Authentication-for-the-Azure-Storage-Services).
Vamos a reducir ese artículo a lo que es estrictamente necesario y mostrar el código.

En primer lugar, use una autenticación de clave compartida. El formato de encabezado de autorización tiene el siguiente aspecto:

```  
Authorization="SharedKey <storage account name>:<signature>"  
```

El campo de firma es un código de autentificación de mensajes basados en hash (HMAC) creado a partir de la solicitud y calculado mediante el algoritmo SHA256, que posteriormente se codifica mediante la codificación Base64. ¿Entiende esto? (Espere, aún no ha oído hablar de la palabra *formato canónico*).

Este fragmento de código muestra el formato de la cadena de firma de clave compartida:

```csharp  
StringToSign = VERB + "\n" +  
               Content-Encoding + "\n" +  
               Content-Language + "\n" +  
               Content-Length + "\n" +  
               Content-MD5 + "\n" +  
               Content-Type + "\n" +  
               Date + "\n" +  
               If-Modified-Since + "\n" +  
               If-Match + "\n" +  
               If-None-Match + "\n" +  
               If-Unmodified-Since + "\n" +  
               Range + "\n" +  
               CanonicalizedHeaders +  
               CanonicalizedResource;  
```

La mayoría de estos campos se usan muy poco. Con Blob Storage, especificará VERB, md5, longitud de contenido, encabezados con formato canónico y recursos con formato canónico. Puede dejar los demás en blanco (pero ponga la `\n` para que se sepa que están en blanco).

¿Qué son CanonicalizedHeaders y CanonicalizedResource? Buena pregunta. De hecho, ¿qué significa formato canónico? Ni siquiera Microsoft Word lo reconoce como una palabra. Esto es lo que la [Wikipedia dice de la canonización](http://en.wikipedia.org/wiki/Canonicalization): *en informática, la canonización (en ocasiones la estandarización o la normalización) es un proceso para convertir los datos que tienen más de una posible representación en forma canónica, "normal" o "estándar".* En terminología normal, esto significa tomar la lista de elementos (como los encabezados en el caso de los encabezados con formato canónico) y estandarizarlos en un formato requerido. Básicamente, Microsoft se decidió por un formato y hay que buscar la coinciencia con él.

Comencemos con esos dos campos con formato canónico, puesto que son necesarios para crear el encabezado de autorización.

**Encabezados con formato canónico**

Para crear este valor, recupere los encabezados que comienzan con "x-ms-" y ordénelos, luego conviértalos en una cadena de instancias de `[key:value\n]`, concatenadas en una cadena. En este ejemplo, los encabezados con formato canónico tienen este aspecto: 

```
x-ms-date:Fri, 17 Nov 2017 00:44:48 GMT\nx-ms-version:2017-04-17\n
```

Este es el código usado para crear esa salida:

```csharp 
private static string GetCanonicalizedHeaders(HttpRequestMessage httpRequestMessage)
{
    var headers = from kvp in httpRequestMessage.Headers
                    where kvp.Key.StartsWith("x-ms-", StringComparison.OrdinalIgnoreCase)
                    orderby kvp.Key
                    select new { Key = kvp.Key.ToLowerInvariant(), kvp.Value };

    StringBuilder sb = new StringBuilder();

    // Create the string in the right format; this is what makes the headers "canonicalized" --
    //   it means put in a standard format. http://en.wikipedia.org/wiki/Canonicalization
    foreach (var kvp in headers)
    {
        StringBuilder headerBuilder = new StringBuilder(kvp.Key);
        char separator = ':';

        // Get the value for each header, strip out \r\n if found, then append it with the key.
        foreach (string headerValues in kvp.Value)
        {
            string trimmedValue = headerValues.TrimStart().Replace("\r\n", String.Empty);
            headerBuilder.Append(separator).Append(trimmedValue);

            // Set this to a comma; this will only be used 
            //   if there are multiple values for one of the headers.
            separator = ',';
        }
        sb.Append(headerBuilder.ToString()).Append("\n");
    }
    return sb.ToString();
}      
```

**Recurso con formato canónico**

Esta parte de la cadena de firma representa la cuenta de almacenamiento que tiene como destino la solicitud. Recuerde que el URI de solicitud es `<http://contosorest.blob.core.windows.net/?comp=list>`, con el nombre de cuenta real (en este caso, `contosorest`). En este ejemplo, se devuelve lo siguiente:

```
/contosorest/\ncomp:list
```

Si tiene parámetros de consulta, también se incluyen. Este es el código, que también administra parámetros de consulta adicionales y parámetros de consulta con varios valores. Recuerde que va a compilar este código para que funcione con todas las API de REST, así que querrá incluir todas las posibilidades, incluso si el método ListContainers no las necesita todas.

```csharp 
private static string GetCanonicalizedResource(Uri address, string storageAccountName)
{
    // The absolute path will be "/" because for we're getting a list of containers.
    StringBuilder sb = new StringBuilder("/").Append(storageAccountName).Append(address.AbsolutePath);

    // Address.Query is the resource, such as "?comp=list".
    // This ends up with a NameValueCollection with 1 entry having key=comp, value=list.
    // It will have more entries if you have more query parameters.
    NameValueCollection values = HttpUtility.ParseQueryString(address.Query);

    foreach (var item in values.AllKeys.OrderBy(k => k))
    {
        sb.Append('\n').Append(item).Append(':').Append(values[item]);
    }

    return sb.ToString();
}
```

Ahora que están establecidas las cadenas con formato canónico, echemos un vistazo a cómo crear el encabezado de autorización propiamente dicho. Para comenzar, se crea una cadena de la firma de mensaje en el formato de StringToSign mostrado anteriormente en este artículo. Este concepto es más fácil de explicar mediante comentarios en el código, así que aquí está, el método final que devuelve el encabezado de autorización:

```csharp
internal static AuthenticationHeaderValue GetAuthorizationHeader(
    string storageAccountName, string storageAccountKey, DateTime now,
    HttpRequestMessage httpRequestMessage, string ifMatch = "", string md5 = "")
{
    // This is the raw representation of the message signature.
    HttpMethod method = httpRequestMessage.Method;
    String MessageSignature = String.Format("{0}\n\n\n{1}\n{5}\n\n\n\n{2}\n\n\n\n{3}{4}",
                method.ToString(),
                (method == HttpMethod.Get || method == HttpMethod.Head) ? String.Empty
                  : httpRequestMessage.Content.Headers.ContentLength.ToString(),
                ifMatch,
                GetCanonicalizedHeaders(httpRequestMessage),
                GetCanonicalizedResource(httpRequestMessage.RequestUri, storageAccountName),
                md5);

    // Now turn it into a byte array.
    byte[] SignatureBytes = Encoding.UTF8.GetBytes(MessageSignature);

    // Create the HMACSHA256 version of the storage key.
    HMACSHA256 SHA256 = new HMACSHA256(Convert.FromBase64String(storageAccountKey));

    // Compute the hash of the SignatureBytes and convert it to a base64 string.
    string signature = Convert.ToBase64String(SHA256.ComputeHash(SignatureBytes));

    // This is the actual header that will be added to the list of request headers.
    AuthenticationHeaderValue authHV = new AuthenticationHeaderValue("SharedKey",
        storageAccountName + ":" + Convert.ToBase64String(SHA256.ComputeHash(SignatureBytes)));
    return authHV;
}
```

Cuando se ejecuta este código, el elemento MessageSignature resultante se parece a este:

```
GET\n\n\n\n\n\n\n\n\n\n\n\nx-ms-date:Fri, 17 Nov 2017 01:07:37 GMT\nx-ms-version:2017-04-17\n/contosorest/\ncomp:list
```

Este es el valor final de AuthorizationHeader:

```
SharedKey contosorest:Ms5sfwkA8nqTRw7Uury4MPHqM6Rj2nfgbYNvUKOa67w=
```

AuthorizationHeader es el último encabezado colocado en los encabezados de solicitud antes de publicar la respuesta.

Hasta aquí todo lo que necesita saber, junto con el código, para preparar una clase que pueda usar para crear una solicitud que se utilizará para llamar a las API de REST de los servicios de almacenamiento.

## <a name="how-about-another-example"></a>¿Otro ejemplo? 

Vamos a ver cómo cambiar el código para llamar a ListBlobs en el contenedor *container-1*. Este código es casi idéntico al que se usa para mostrar los contenedores, la única diferencia es el URI y el modo de analizar la respuesta. 

Si examina la documentación de referencia de [ListBlobs](/rest/api/storageservices/fileservices/List-Blobs), verá que el método es *GET* y el elemento RequestURI es:

```
https://myaccount.blob.core.windows.net/container-1?restype=container&comp=list
```

En ListContainersAsyncREST, cambie el código que establece el URI en la API de ListBlobs. El nombre del contenedor es **container-1**.

```csharp
String uri = 
    string.Format("http://{0}.blob.core.windows.net/container-1?restype=container&comp=list",
      storageAccountName);

```

A continuación, donde administra la respuesta, cambie el código para buscar blobs en lugar de contenedores.

```csharp
foreach (XElement container in x.Element("Blobs").Elements("Blob"))
{
    Console.WriteLine("Blob name = {0}", container.Element("Name").Value);
}
```

Al ejecutar este ejemplo, obtiene resultados como los siguientes:

**Encabezados con formato canónico:**

```
x-ms-date:Fri, 17 Nov 2017 05:16:48 GMT\nx-ms-version:2017-04-17\n
```

**Recurso con formato canónico:**

```
/contosorest/container-1\ncomp:list\nrestype:container
```

**MessageSignature:**

```
GET\n\n\n\n\n\n\n\n\n\n\n\nx-ms-date:Fri, 17 Nov 2017 05:16:48 GMT
  \nx-ms-version:2017-04-17\n/contosorest/container-1\ncomp:list\nrestype:container
```

**AuthorizationHeader:**

```
SharedKey contosorest:uzvWZN1WUIv2LYC6e3En10/7EIQJ5X9KtFQqrZkxi6s=
```

Los valores siguientes son de [Fiddler](http://www.telerik.com/fiddler):

**Solicitud:**

```
GET http://contosorest.blob.core.windows.net/container-1?restype=container&comp=list HTTP/1.1
```

**Encabezados de solicitud:**

```
x-ms-date: Fri, 17 Nov 2017 05:16:48 GMT
x-ms-version: 2017-04-17
Authorization: SharedKey contosorest:uzvWZN1WUIv2LYC6e3En10/7EIQJ5X9KtFQqrZkxi6s=
Host: contosorest.blob.core.windows.net
Connection: Keep-Alive
```

**Encabezados de solicitud y respuesta devueltos tras la ejecución:**

```
HTTP/1.1 200 OK
Content-Type: application/xml
Server: Windows-Azure-Blob/1.0 Microsoft-HTTPAPI/2.0
x-ms-request-id: 7e9316da-001e-0037-4063-5faf9d000000
x-ms-version: 2017-04-17
Date: Fri, 17 Nov 2017 05:20:21 GMT
Content-Length: 1135
```

**Cuerpo de respuesta (XML):** esta respuesta XML muestra la lista de blobs y sus propiedades. 

```xml
<?xml version="1.0" encoding="utf-8"?>
<EnumerationResults 
    ServiceEndpoint="http://contosorest.blob.core.windows.net/" ContainerName="container-1">
    <Blobs>
        <Blob>
            <Name>DogInCatTree.png</Name>
            <Properties><Last-Modified>Fri, 17 Nov 2017 01:41:14 GMT</Last-Modified>
            <Etag>0x8D52D5C4A4C96B0</Etag>
            <Content-Length>419416</Content-Length>
            <Content-Type>image/png</Content-Type>
            <Content-Encoding />
            <Content-Language />
            <Content-MD5 />
            <Cache-Control />
            <Content-Disposition />
            <BlobType>BlockBlob</BlobType>
            <LeaseStatus>unlocked</LeaseStatus>
            <LeaseState>available</LeaseState>
            <ServerEncrypted>true</ServerEncrypted>
            </Properties>
        </Blob>
        <Blob>
            <Name>GuyEyeingOreos.png</Name>
            <Properties>
                <Last-Modified>Fri, 17 Nov 2017 01:41:14 GMT</Last-Modified>
                <Etag>0x8D52D5C4A25A6F6</Etag>
                <Content-Length>167464</Content-Length>
                <Content-Type>image/png</Content-Type>
                <Content-Encoding />
                <Content-Language />
                <Content-MD5 />
                <Cache-Control />
                <Content-Disposition />
                <BlobType>BlockBlob</BlobType>
                <LeaseStatus>unlocked</LeaseStatus>
                <LeaseState>available</LeaseState>
                <ServerEncrypted>true</ServerEncrypted>
            </Properties>
            </Blob>
        </Blobs>
    <NextMarker />
</EnumerationResults>
```

## <a name="summary"></a>Resumen

En este artículo, aprendió a realizar una solicitud a la API de REST de almacenamiento de blobs para recuperar una lista de contenedores o una lista de blobs de un contenedor. También aprendió a crear la firma de autorización de la llamada a la API de REST, cómo usarla en la solicitud de REST y cómo examinar la respuesta.

## <a name="next-steps"></a>pasos siguientes

* [Blob Service REST API](/rest/api/storageservices/blob-service-rest-api) (API de REST de Blob service)
* [File Service REST API](/rest/api/storageservices/file-service-rest-api) (API de REST de File Service)
* [API de REST del servicio Cola](/rest/api/storageservices/queue-service-rest-api)