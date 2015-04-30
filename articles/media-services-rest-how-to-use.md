<properties 
	pageTitle="Información general de la API de REST de Servicios multimedia: Azure" 
	description="Información general de la API de REST de Servicios multimedia" 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="02/02/2015" 
	ms.author="juliako"/>


# Información general de la API de REST de Servicios multimedia 

Servicios multimedia de Microsoft Azure es un servicio que acepta solicitudes HTTP basadas en OData y puede responder en JSON detallado o atom+pub. Como los Servicios multimedia cumplen las directrices de diseño de Azure, hay un conjunto de encabezados HTTP obligatorios que cada cliente debe usar al conectarse a los Servicios multimedia, así como un conjunto de encabezados opcionales que se pueden usar. En las secciones siguientes se describen los encabezados y los verbos HTTP que puede usar al crear solicitudes y recibir respuestas de los Servicios multimedia.


## Encabezados de solicitud HTTP estándar compatibles con los Servicios multimedia

Para cada llamada que realice en los Servicios multimedia, hay un conjunto de encabezados necesarios que debe incluir en la solicitud y también un conjunto de encabezados opcionales que puede incluir si lo desea. En la tabla siguiente se enumeran los encabezados necesarios:


<table border="1">
<tr><th>Encabezado</th><th>Tipo</th><th>Valor</th></tr>
<tr><td>Autorización</td><td>Portador</td><td>El portador es el único mecanismo de autorización aceptado. El valor también debe incluir el token de acceso proporcionado por ACS.</td></tr>
<tr><td>x-ms-version</td><td>Decimal</td><td>2,9</td></tr>
<tr><td>DataServiceVersion</td><td>Decimal</td><td>3,0</td></tr>
<tr><td>MaxDataServiceVersion</td><td>Decimal</td><td>3,0</td></tr>
</table><br/>


>[AZURE.NOTE] Dado que los Servicios multimedia usan OData para exponer su repositorio de metadatos de recursos subyacente a través de las API de REST, los encabezados DataServiceVersion y MaxDataServiceVersion deben incluirse en cualquier solicitud; sin embargo, si no se incluyen, los Servicios multimedia actuales suponen que el valor de DataServiceVersion es 3.0.

A continuación se muestra un conjunto de encabezados opcionales:

<table border="1">
<tr><th>Encabezado</th><th>Tipo</th><th>Valor</th></tr>
<tr><td>Date</td><td>Fecha RFC 1123</td><td>Marca de tiempo de la solicitud</td></tr>
<tr><td>Accept</td><td>Tipo de contenido</td><td>El tipo de contenido solicitado para la respuesta, por ejemplo, el siguiente:
<ul><li>aplicación/json;odata=verbose</li><li>aplicación/atom+xml</li></ul></br> Las respuestas pueden tener distintos tipos de contenido, como una captura de blob, donde una respuesta correcta contendrá la secuencia de blob como la carga.</td></tr>
<tr><td>Accept-Encoding</td><td>Gzip, deflate</td><td>Codificación GZIP y DEFLATE, según corresponda. Nota: En el caso de recursos grandes, los Servicios multimedia pueden ignorar el encabezado y devolver datos sin comprimir.
</td></tr>
<tr><td>Accept-Language</td><td>"en", "es", etc.</td><td>Especifica el idioma preferido para la respuesta.</td></tr>
<tr><td>Accept-Charset</td><td>Tipo de juego de caracteres como "UTF-8"</td><td>El valor predeterminado es UTF-8.</td></tr>
<tr><td>X-HTTP-Method</td><td>Método HTTP</td><td>Permite a los clientes o firewalls que no admiten métodos HTTP como PUT o DELETE usar estos métodos mediante túnel a través de una llamada GET.</td></tr>
<tr><td>Content-Type</td><td>Tipo de contenido</td><td>Tipo de contenido del cuerpo de la solicitud en solicitudes PUT o POST.</td></tr>
<tr><td>client-request-id</td><td>String</td><td>Valor definido por el autor de la llamada que identifica la solicitud determinada. Si se especifica, este valor se incluirá en el mensaje de respuesta como una manera de asignar la solicitud. <br/><br/>
<b>Importante</b><br/>
Los valores deben estar limitados a 2096b (2k).</td></tr>
</table><br/>


## Encabezados de respuesta HTTP estándar compatibles con los Servicios multimedia

A continuación se muestra un conjunto de encabezados que pueden devolverse según el recurso que estuviera solicitando y la acción que deseara realizar.


<table border="1">
<tr><th>Encabezado</th><th>Tipo</th><th>Valor</th></tr>
<tr><td>request-id</td><td>String</td><td>Identificador único de la operación o servicio actual generado.</td></tr>
<tr><td>client-request-id</td><td>String</td><td>Identificador especificado por el autor de la llamada en la solicitud original, si está presente.</td></tr>
<tr><td>Date</td><td>Fecha RFC 1123</td><td>Fecha en que se procesó la solicitud.</td></tr>
<tr><td>Content-Type</td><td>Varía</td><td>Tipo de contenido del cuerpo de respuesta.</td></tr>
<tr><td>Content-Encoding</td><td>Varía</td><td>Gzip o deflate, según corresponda.</td></tr>
</table><br/>

## Verbos HTTP estándar compatibles con los Servicios multimedia

A continuación se muestra una lista completa de verbos HTTP que se pueden usar al realizar solicitudes HTTP:


<table border="1">
<tr><th>Verbo</th><th>Descripción</th></tr>
<tr><td>GET</td><td>Devuelve el valor actual de un objeto.</td></tr>
<tr><td>POST</td><td>Crea un objeto basado en los datos proporcionados o envía un comando.</td></tr>
<tr><td>PUT</td><td>Reemplaza un objeto o crea un objeto con nombre (si procede).</td></tr>
<tr><td>DELETE</td><td>Elimina un objeto.</td></tr>
<tr><td>MERGE</td><td>Actualiza un objeto existente con los cambios de propiedad con nombre.</td></tr>
<tr><td>HEAD</td><td>Devuelve los metadatos de un objeto para una respuesta GET.</td></tr>
</table><br/>

## Detección del modelo de Servicios multimedia

Para que las entidades de los Servicios multimedia sean más reconocibles, se puede usar la operación de metadatos. Permite recuperar todos los tipos de entidad válidos, las propiedades de entidad, las asociaciones, las funciones, las acciones, etc. En el ejemplo siguiente se muestra cómo construir el URI.: https://media.windows.net/API/$metadata.

Anexe "?api-version=2.x" al final del URI si desea ver los metadatos en un explorador o no incluya el encabezado x-ms-version en la solicitud.


<!-- Anchors. -->


<!-- URLs. -->
  
  [Portal de administración]: http://manage.windowsazure.com/




<!--HONumber=52-->