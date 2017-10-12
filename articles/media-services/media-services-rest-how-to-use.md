---
title: "Información general sobre la API de REST de operaciones de Media Services | Microsoft Docs"
description: "Información general de la API de REST de Servicios multimedia"
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.assetid: a5f1c5e7-ec52-4e26-9a44-d9ea699f68d9
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/10/2017
ms.author: juliako
ms.openlocfilehash: eada8f2bcd2488d5ed36b0c24aa3d1b917815517
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="media-services-operations-rest-api-overview"></a>Información general sobre la API de REST de operaciones de Media Services
[!INCLUDE [media-services-selector-setup](../../includes/media-services-selector-setup.md)]

La API de **REST de Media Services Operations** se usa para crear trabajos, activos, directivas de acceso y otras operaciones en objetos de una cuenta de Media Services. Para obtener más información, consulte la [referencia de la API de REST de Media Services Operations](https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference).

Servicios multimedia de Microsoft Azure es un servicio que acepta solicitudes HTTP basadas en OData y puede responder en JSON detallado o atom+pub. Como los Servicios multimedia cumplen las directrices de diseño de Azure, hay un conjunto de encabezados HTTP obligatorios que cada cliente debe usar al conectarse a los Servicios multimedia, así como un conjunto de encabezados opcionales que se pueden usar. En las secciones siguientes se describen los encabezados y los verbos HTTP que puede usar al crear solicitudes y recibir respuestas de los Servicios multimedia.

Este tema ofrece información general sobre cómo utilizar REST v2 con Media Services.

## <a name="considerations"></a>Consideraciones

Las siguientes consideraciones se aplican al usar REST.

* Al consultar entidades, hay un límite de 1000 entidades devueltas a la vez, porque la REST v2 pública limita los resultados de consulta a 1000. Debe usar **Skip** y **Take** (.NET)/ **top** (REST) como se describe en [este ejemplo de .NET](media-services-dotnet-manage-entities.md#enumerating-through-large-collections-of-entities) y de [API de REST](media-services-rest-manage-entities.md#enumerating-through-large-collections-of-entities). 
* Cuando se utiliza JSON y se especifica el uso de la palabra clave **__metadata** en la solicitud (por ejemplo, para referencias a un objeto vinculado) DEBE establecer el encabezado **Accept** en [formato JSON detallado](http://www.odata.org/documentation/odata-version-3-0/json-verbose-format/) (consulte el ejemplo siguiente). Odata no entiende la propiedad **__metadata** en la solicitud, a menos que se establezca en el modo detallado.  
  
        POST https://media.windows.net/API/Jobs HTTP/1.1
        Content-Type: application/json;odata=verbose
        Accept: application/json;odata=verbose
        DataServiceVersion: 3.0
        MaxDataServiceVersion: 3.0
        x-ms-version: 2.11
        Authorization: Bearer <token> 
        Host: media.windows.net
  
        {
            "Name" : "NewTestJob", 
            "InputMediaAssets" : 
                [{"__metadata" : {"uri" : "https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3Aba5356eb-30ff-4dc6-9e5a-41e4223540e7')"}}]
        . . . 

## <a name="standard-http-request-headers-supported-by-media-services"></a>Encabezados de solicitud HTTP estándar compatibles con los Servicios multimedia
Para cada llamada que realice en los Servicios multimedia, hay un conjunto de encabezados necesarios que debe incluir en la solicitud y también un conjunto de encabezados opcionales que puede incluir si lo desea. En la tabla siguiente se enumeran los encabezados necesarios:

| Encabezado | Tipo | Valor |
| --- | --- | --- |
| Autorización |Portador |El portador es el único mecanismo de autorización aceptado. El valor también debe incluir el token de acceso proporcionado por ACS. |
| x-ms-version |Decimal |2.11 |
| DataServiceVersion |Decimal |3.0 |
| MaxDataServiceVersion |Decimal |3.0 |

> [!NOTE]
> Dado que los Servicios multimedia usan OData para exponer su repositorio de metadatos de recursos subyacente a través de las API de REST, los encabezados DataServiceVersion y MaxDataServiceVersion deben incluirse en cualquier solicitud; sin embargo, si no se incluyen, los Servicios multimedia actuales suponen que el valor de DataServiceVersion es 3.0.
> 
> 

A continuación se muestra un conjunto de encabezados opcionales:

| Encabezado | Tipo | Valor |
| --- | --- | --- |
| Date |Fecha RFC 1123 |Marca de tiempo de la solicitud |
| Accept |Tipo de contenido |El tipo de contenido solicitado para la respuesta, por ejemplo, el siguiente:<p> -application/json;odata=verbose<p> - application/atom+xml<p> Las respuestas pueden tener distintos tipos de contenido, como una captura de blob, donde una respuesta correcta contendrá la secuencia de blob como la carga. |
| Accept-Encoding |Gzip, deflate |Codificación GZIP y DEFLATE, según corresponda. En el caso de recursos grandes, Servicios multimedia puede ignorar el encabezado y devolver datos sin comprimir. |
| Accept-Language |"en", "es", etc. |Especifica el idioma preferido para la respuesta. |
| Accept-Charset |Tipo de juego de caracteres como "UTF-8" |El valor predeterminado es UTF-8. |
| X-HTTP-Method |Método HTTP |Permite a los clientes o firewalls que no admiten métodos HTTP como PUT o DELETE usar estos métodos mediante túnel a través de una llamada GET. |
| Content-Type |Tipo de contenido |Tipo de contenido del cuerpo de la solicitud en solicitudes PUT o POST. |
| client-request-id |String |Valor definido por el autor de la llamada que identifica la solicitud determinada. Si se especifica, este valor se incluirá en el mensaje de respuesta como una manera de asignar la solicitud. <p><p>**Importante**<p>Los valores deben estar limitados a 2096b (2k). |

## <a name="standard-http-response-headers-supported-by-media-services"></a>Encabezados de respuesta HTTP estándar compatibles con los Servicios multimedia
A continuación se muestra un conjunto de encabezados que pueden devolverse según el recurso que estuviera solicitando y la acción que deseara realizar.

| Encabezado | Tipo | Valor |
| --- | --- | --- |
| request-id |String |Identificador único de la operación o servicio actual generado. |
| client-request-id |String |Identificador especificado por el autor de la llamada en la solicitud original, si está presente. |
| Date |Fecha RFC 1123 |Fecha en que se procesó la solicitud. |
| Content-Type |Varía |Tipo de contenido del cuerpo de respuesta. |
| Content-Encoding |Varía |Gzip o deflate, según corresponda. |

## <a name="standard-http-verbs-supported-by-media-services"></a>Verbos HTTP estándar compatibles con los Servicios multimedia
A continuación se muestra una lista completa de verbos HTTP que se pueden usar al realizar solicitudes HTTP:

| Verbo | Description |
| --- | --- |
| GET |Devuelve el valor actual de un objeto. |
| POST |Crea un objeto basado en los datos proporcionados o envía un comando. |
| PUT |Reemplaza un objeto o crea un objeto con nombre (si procede). |
| DELETE |Elimina un objeto. |
| MERGE |Actualiza un objeto existente con los cambios de propiedad con nombre. |
| HEAD |Devuelve los metadatos de un objeto para una respuesta GET. |

## <a name="discover-media-services-model"></a>Detección del modelo de Media Services
Para que las entidades de los Servicios multimedia sean más reconocibles, se puede usar la operación de metadatos. Permite recuperar todos los tipos de entidad válidos, las propiedades de entidad, las asociaciones, las funciones, las acciones, etc. En el ejemplo siguiente se muestra cómo construir el URI: https://media.windows.net/API/$metadata.

Anexe "?api-version=2.x" al final del URI si desea ver los metadatos en un explorador o no incluya el encabezado x-ms-version en la solicitud.

## <a name="connect-to-media-services"></a>Conexión con Servicios multimedia

Para obtener más información sobre cómo conectarse a la API de Azure Media Services, consulte [Acceso a la API de Azure Media Services con la autenticación de Azure AD](media-services-use-aad-auth-to-access-ams-api.md). Después de conectarse correctamente a https://media.windows.net, recibirá una redirección 301 que especifica otro URI de Servicios multimedia. Debe realizar las llamadas posteriores al nuevo URI.

## <a name="next-steps"></a>Pasos siguientes

Para tener acceso a las API de AMS con REST, consulte [Uso de la autenticación de Azure AD para tener acceso a la API de Azure Media Services con REST](media-services-rest-connect-with-aad.md).

## <a name="media-services-learning-paths"></a>Rutas de aprendizaje de Servicios multimedia
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Envío de comentarios
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

