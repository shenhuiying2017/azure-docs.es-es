---
title: "Creación de un procesador de multimedia | Microsoft Docs"
description: Aprenda a crear un componente de procesador de multimedia para codificar, cifrar, descifrar o convertir el formato de contenido multimedia para Servicios multimedia de Azure.
services: media-services
documentationcenter: 
author: Juliako
manager: erikre
editor: 
ms.assetid: f9ff1997-0da6-4528-aaed-792837e5be41
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/20/2017
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: d1d05b46591fe4b72c92c59d357681c8e1cdb336
ms.openlocfilehash: c208660fc1439ca831ada6c9bb348dbc3eadc18c


---
# <a name="how-to-get-a-media-processor-instance"></a>Obtención de una instancia de procesador multimedia
> [!div class="op_single_selector"]
> * [.NET](media-services-get-media-processor.md)
> * [REST](media-services-rest-get-media-processor.md)
> 
> 

## <a name="overview"></a>Información general
En los Servicios multimedia, un procesador multimedia es un componente que controla una tarea de procesamiento específica, como codificación, conversión de formato, cifrado o descifrado de contenido multimedia. Normalmente crea un procesador multimedia cuando crea una tarea para codificar, cifrar o convertir el formato de contenido multimedia.

La siguiente tabla proporciona el nombre y la descripción de cada procesador multimedia disponible.

| Nombre de procesador multimedia | Description | Más información |
| --- | --- | --- |
| Media Encoder Estándar |Proporciona funciones estándar de codificación a petición. |[Información general y comparación de codificadores multimedia a petición de Azure](media-services-encode-asset.md) |
| Flujo de trabajo del Codificador multimedia |Le permite ejecutar tareas de codificación con el flujo de trabajo Premium del Codificador multimedia. |[Información general y comparación de codificadores multimedia a petición de Azure](media-services-encode-asset.md) |
| Azure Media Indexer |Le permite crear archivos multimedia y contenido que se puede buscar, así como generar pistas y palabras clave de subtítulos (CC). |[Azure Media Indexer](media-services-index-content.md) |
| Azure Media Hyperlapse (versión preliminar) |Permite suavizar los “saltos” en el vídeo con estabilización de vídeo. También permite acelerar su contenido en un clip consumible. |[Azure Media Hyperlapse](media-services-hyperlapse-content.md) |
| Codificador multimedia de Azure |En desuso | |
| Storage Decryption |En desuso | |
| Azure Media Packager |En desuso | |
| Azure Media Encryptor |En desuso | |

## <a name="get-mediaprocessor"></a>Obtener MediaProcessor
> [!NOTE]
> Al trabajar con la API de REST de Servicios multimedia, se aplican las consideraciones siguientes:
> 
> Al obtener acceso a las entidades de Servicios multimedia, debe establecer los campos de encabezado específicos y los valores en las solicitudes HTTP. Para obtener más información, consulte [Configuración del desarrollo de la API de REST de Servicios multimedia](media-services-rest-how-to-use.md).
> 
> Después de conectarse correctamente a https://media.windows.net, recibirá una redirección 301 que especifica otro URI de Servicios multimedia. Debe realizar las llamadas subsiguientes al nuevo URI como se describe en [Conexión a Servicios multimedia con la API de REST](media-services-rest-connect-programmatically.md). 
> 
> 

La siguiente llamada REST muestra cómo obtener una instancia de procesador multimedia por nombre (en este caso, **Codificador multimedia estándar**). 

Solicitud:

    GET https://media.windows.net/api/MediaProcessors()?$filter=Name%20eq%20'Media%20Encoder%20Standard' HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer <token>
    x-ms-version: 2.11
    Host: media.windows.net

Respuesta:

    . . .

    {  
       "odata.metadata":"https://media.windows.net/api/$metadata#MediaProcessors",
       "value":[  
          {  
             "Id":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
             "Description":"Media Encoder Standard",
             "Name":"Media Encoder Standard",
             "Sku":"",
             "Vendor":"Microsoft",
             "Version":"1.1"
          }
       ]
    }


## <a name="media-services-learning-paths"></a>Rutas de aprendizaje de Servicios multimedia
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Envío de comentarios
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Pasos siguientes
Ahora que sabe cómo obtener una instancia de procesador multimedia, consulte el tema sobre la [codificación de un recurso](media-services-rest-get-started.md) , que le mostrará cómo utilizar el servicio Media Encoder estándar para codificar un recurso.




<!--HONumber=Feb17_HO3-->


