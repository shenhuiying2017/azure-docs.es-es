---
title: "Creación de filtros con la API de REST de Azure Media Services | Microsoft Docs"
description: "En este tema se describe cómo crear filtros para que su cliente pueda usarlos para el streaming de secciones específicas de una transmisión. Servicios multimedia crea manifiestos dinámicos para lograr este streaming selectivo."
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.assetid: f7d23daf-7cd2-49c7-a195-ab902912ab3c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 08/10/2017
ms.author: juliako;cenkdin
ms.openlocfilehash: 76d2721138668d9f0a908af3fa42840309b068ef
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/29/2017
---
# <a name="creating-filters-with-azure-media-services-rest-api"></a>Creación de filtros con la API de REST de Servicios multimedia de Azure
> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-dynamic-manifest.md)
> * [REST](media-services-rest-dynamic-manifest.md)
> 
> 

A partir de la versión 2.11, los Servicios multimedia permiten definir filtros para los activos. Estos filtros son reglas del lado servidor que permitirán a los clientes elegir realizar acciones como: reproducir solo una sección de un vídeo (en lugar de reproducir el vídeo completo), o especificar solo un subconjunto de las representaciones de audio y vídeo que el dispositivo de su cliente puede controlar (en lugar de todas las copias asociadas al activo). Este filtrado de sus activos se archiva a través de los **manifiestos dinámicos**que se crean tras la solicitud del cliente para transmitir un vídeo en función de los filtros especificados.

Para obtener más información detallada relacionada con filtros y manifiesto dinámico, vea [Información general de manifiesto dinámico](media-services-dynamic-manifest-overview.md).

En este tema se muestra cómo usar las API de REST para crear, actualizar y eliminar filtros. 

## <a name="types-used-to-create-filters"></a>Tipos usados para crear filtros
Al crear filtros, se usan los siguientes tipos:  

* [Filter](https://docs.microsoft.com/rest/api/media/operations/filter)
* [AssetFilter](https://docs.microsoft.com/rest/api/media/operations/assetfilter)
* [PresentationTimeRange](https://docs.microsoft.com/rest/api/media/operations/presentationtimerange)
* [FilterTrackSelect y FilterTrackPropertyCondition](https://docs.microsoft.com/rest/api/media/operations/filtertrackselect)

>[!NOTE]

>Al obtener acceso a las entidades de Servicios multimedia, debe establecer los campos de encabezado específicos y los valores en las solicitudes HTTP. Para obtener más información, consulte [Configuración del desarrollo de la API de REST de Servicios multimedia](media-services-rest-how-to-use.md).

## <a name="connect-to-media-services"></a>Conexión con Servicios multimedia

Para obtener más información sobre cómo conectarse a la API de Azure Media Services, consulte [Acceso a la API de Azure Media Services con la autenticación de Azure AD](media-services-use-aad-auth-to-access-ams-api.md). 

>[!NOTE]
>Después de conectarse correctamente a https://media.windows.net, recibirá una redirección 301 que especifica otro URI de Servicios multimedia. Debe realizar las llamadas posteriores al nuevo URI.

## <a name="create-filters"></a>Crear filtros
### <a name="create-global-filters"></a>Crear filtros globales
Para crear un filtro global, use las siguientes solicitudes HTTP:  

#### <a name="http-request"></a>Solicitud HTTP
Encabezados de solicitud

    POST https://media.windows.net/API/Filters HTTP/1.1 
    DataServiceVersion:3.0 
    MaxDataServiceVersion: 3.0 
    Content-Type: application/json 
    Accept: application/json 
    Accept-Charset: UTF-8 
    Authorization: Bearer <token value> 
    x-ms-version: 2.11 
    x-ms-client-request-id: 00000000-0000-0000-0000-000000000000 
    Host:media.windows.net 

Request body 

    {  
       "Name":"GlobalFilter",
       "PresentationTimeRange":{  
          "StartTimestamp":"0",
          "EndTimestamp":"9223372036854775807",
          "PresentationWindowDuration":"12000000000",
          "LiveBackoffDuration":"0",
          "Timescale":"10000000"
       },
       "Tracks":[  
          {  
             "PropertyConditions":
                  [  
                {  
                   "Property":"Type",
                   "Value":"audio",
                   "Operator":"Equal"
                },
                {  
                   "Property":"Bitrate",
                   "Value":"0-2147483647",
                   "Operator":"Equal"
                }
             ]
          }
       ]
    }




#### <a name="http-response"></a>Respuesta HTTP
    HTTP/1.1 201 Created 

### <a name="create-local-assetfilters"></a>Crear AssetFilters locales
Para crear un AssetFilter local, use las siguientes solicitudes HTTP:  

#### <a name="http-request"></a>Solicitud HTTP
Encabezados de solicitud

    POST https://media.windows.net/API/AssetFilters HTTP/1.1 
    DataServiceVersion: 3.0 
    MaxDataServiceVersion: 3.0 
    Content-Type: application/json 
    Accept: application/json 
    Accept-Charset: UTF-8 
    Authorization: Bearer <token value> 
    x-ms-version: 2.11 
    x-ms-client-request-id: 00000000-0000-0000-0000-000000000000 
    Host: media.windows.net  

Request body 

    {   
       "Name":"AssetFilter", 
       "ParentAssetId":"nb:cid:UUID:536e555d-1500-80c3-92dc-f1e4fdc6c592", 
       "PresentationTimeRange":{   
          "StartTimestamp":"0", 
          "EndTimestamp":"9223372036854775807", 
          "PresentationWindowDuration":"12000000000", 
          "LiveBackoffDuration":"0", 
          "Timescale":"10000000" 
       }, 
       "Tracks":[   
          {   
             "PropertyConditions": 
                  [   
                {   
                   "Property":"Type", 
                   "Value":"audio", 
                   "Operator":"Equal" 
                }, 
                {   
                   "Property":"Bitrate", 
                   "Value":"0-2147483647", 
                   "Operator":"Equal" 
                } 
             ] 
          } 
       ] 
    } 

#### <a name="http-response"></a>Respuesta HTTP
    HTTP/1.1 201 Created 
    . . . 

## <a name="list-filters"></a>Enumerar filtros
### <a name="get-all-global-filters-in-the-ams-account"></a>Obtener todos los **filtro**s globales en la cuenta de AMS
Para enumerar filtros, use las siguientes solicitudes HTTP: 

#### <a name="http-request"></a>Solicitud HTTP
    GET https://media.windows.net/API/Filters HTTP/1.1 
    DataServiceVersion:3.0 
    MaxDataServiceVersion: 3.0 
    Accept: application/json 
    Accept-Charset: UTF-8 
    Authorization: Bearer <token value> 
    x-ms-version: 2.11 
    Host: media.windows.net 

### <a name="get-assetfilters-associated-with-an-asset"></a>Obtener **AssetFilter**s asociados a un recurso
#### <a name="http-request"></a>Solicitud HTTP
    GET https://media.windows.net/API/Assets('nb%3Acid%3AUUID%3A536e555d-1500-80c3-92dc-f1e4fdc6c592')/AssetFilters HTTP/1.1 
    DataServiceVersion: 3.0 
    MaxDataServiceVersion: 3.0 
    Accept: application/json 
    Accept-Charset: UTF-8 
    Authorization: Bearer <token value> 
    x-ms-version: 2.11 
    x-ms-client-request-id: 00000000-0000-0000-0000-000000000000 
    Host: media.windows.net 

### <a name="get-an-assetfilter-based-on-its-id"></a>Obtener un **AssetFilter** según su id.
#### <a name="http-request"></a>Solicitud HTTP
    GET https://media.windows.net/API/AssetFilters('nb%3Acid%3AUUID%3A536e555d-1500-80c3-92dc-f1e4fdc6c592__%23%23%23__TestFilter') HTTP/1.1 
    DataServiceVersion: 3.0 
    MaxDataServiceVersion: 3.0 
    Accept: application/json 
    Accept-Charset: UTF-8 
    Authorization: Bearer <token value> 
    x-ms-version: 2.11 
    x-ms-client-request-id: 00000000


## <a name="update-filters"></a>Actualizar filtros
Use PATCH, PUT o MERGE para actualizar un filtro con nuevos valores de propiedad.  Para obtener más información acerca de estas operaciones, vea [PATCH, PUT, MERGE](http://msdn.microsoft.com/library/dd541276.aspx).

Si actualiza un filtro, se pueden tardar hasta 2 minutos en que el extremo de streaming actualice las reglas. Si el contenido se proporcionó con este filtro (y se almacenó en caché en los servidores proxy y cachés de CDN), la actualización de este filtro puede generar errores del reproductor. Se recomienda borrar la memoria caché después de actualizar el filtro. Si esta opción no es posible, piense en usar un filtro diferente.  

### <a name="update-global-filters"></a>Actualizar los filtros globales
Para actualizar un filtro global, use las siguientes solicitudes HTTP: 

#### <a name="http-request"></a>Solicitud HTTP
Encabezados de solicitud: 

    MERGE https://media.windows.net/API/Filters('filterName') HTTP/1.1 
    DataServiceVersion:3.0 
    MaxDataServiceVersion: 3.0 
    Content-Type: application/json 
    Accept: application/json 
    Accept-Charset: UTF-8 
    Authorization: Bearer <token value> 
    x-ms-version: 2.11 
    x-ms-client-request-id: 00000000-0000-0000-0000-000000000000 
    Host: media.windows.net 
    Content-Length: 384

Cuerpo de la solicitud: 

    { 
       "Tracks":[   
          {   
             "PropertyConditions": 
             [   
                {   
                   "Property":"Type", 
                   "Value":"audio", 
                   "Operator":"Equal" 
                }, 
                {   
                   "Property":"Bitrate", 
                   "Value":"0-2147483647", 
                   "Operator":"Equal" 
                } 
             ] 
          } 
       ] 
    } 

### <a name="update-local-assetfilters"></a>Actualización de AssetFilters locales
Para actualizar un filtro local, use las siguientes solicitudes HTTP: 

#### <a name="http-request"></a>Solicitud HTTP
Encabezados de solicitud: 

    MERGE https://media.windows.net/API/AssetFilters('nb%3Acid%3AUUID%3A536e555d-1500-80c3-92dc-f1e4fdc6c592__%23%23%23__TestFilter')  HTTP/1.1 
    DataServiceVersion: 3.0 
    MaxDataServiceVersion: 3.0 
    Content-Type: application/json 
    Accept: application/json 
    Accept-Charset: UTF-8 
    Authorization: Bearer <token value> 
    x-ms-version: 2.11 
    x-ms-client-request-id: 00000000-0000-0000-0000-000000000000 
    Host: media.windows.net 

Cuerpo de la solicitud: 

    { 
       "Tracks":[   
          {   
             "PropertyConditions": 
             [   
                {   
                   "Property":"Type", 
                   "Value":"audio", 
                   "Operator":"Equal" 
                }, 
                {   
                   "Property":"Bitrate", 
                   "Value":"0-2147483647", 
                   "Operator":"Equal" 
                } 
             ] 
          } 
       ] 
    } 


## <a name="delete-filters"></a>Eliminar filtros
### <a name="delete-global-filters"></a>Eliminar filtros globales
Para eliminar un filtro local, use las siguientes solicitudes HTTP:

#### <a name="http-request"></a>Solicitud HTTP
    DELETE https://media.windows.net/api/Filters('GlobalFilter') HTTP/1.1 
    DataServiceVersion:3.0 
    MaxDataServiceVersion: 3.0 
    Accept: application/json 
    Accept-Charset: UTF-8 
    Authorization: Bearer <token value> 
    x-ms-version: 2.11 
    Host: media.windows.net 


### <a name="delete-local-assetfilters"></a>Eliminar AssetFilters locales
Para eliminar un AssetFilter local, use las siguientes solicitudes HTTP:

#### <a name="http-request"></a>Solicitud HTTP
    DELETE https://media.windows.net/API/AssetFilters('nb%3Acid%3AUUID%3A536e555d-1500-80c3-92dc-f1e4fdc6c592__%23%23%23__LocalFilter') HTTP/1.1 
    DataServiceVersion: 3.0 
    MaxDataServiceVersion: 3.0 
    Accept: application/json 
    Accept-Charset: UTF-8 
    Authorization: Bearer <token value> 
    x-ms-version: 2.11 
    Host: media.windows.net 

## <a name="build-streaming-urls-that-use-filters"></a>Generar direcciones URL de streaming que usan filtros
Para obtener información sobre cómo publicar y entregar los activos, vea [Información general de entrega de contenido a clientes](media-services-deliver-content-overview.md).

En los ejemplos siguientes se muestra cómo agregar filtros a sus URL de streaming.

**MPEG DASH** 

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf, filter=MyFilter)

**Apple HTTP Live Streaming (HLS) V4**

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl, filter=MyFilter)

**Apple HTTP Live Streaming (HLS) V3**

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3, filter=MyFilter)

**Smooth Streaming**

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(filter=MyFilter)

    
## <a name="media-services-learning-paths"></a>Rutas de aprendizaje de Servicios multimedia
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Envío de comentarios
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Otras referencias
[Información general de manifiestos dinámicos](media-services-dynamic-manifest-overview.md)

