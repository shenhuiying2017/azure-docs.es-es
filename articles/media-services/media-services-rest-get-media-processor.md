---
title: "Obtención de un procesador de multimedia mediante REST | Microsoft Docs"
description: Aprenda a crear un componente de procesador de multimedia para codificar, cifrar, descifrar o convertir el formato de contenido multimedia para Azure Media Services.
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.assetid: f9ff1997-0da6-4528-aaed-792837e5be41
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/10/2017
ms.author: juliako
ms.translationtype: HT
ms.sourcegitcommit: a9cfd6052b58fe7a800f1b58113aec47a74095e3
ms.openlocfilehash: 0650d3727df1b6738f140439ce4c22078d188d83
ms.contentlocale: es-es
ms.lasthandoff: 08/12/2017

---
# <a name="how-to-get-a-media-processor-instance"></a>Obtención de una instancia del procesador de multimedia
> [!div class="op_single_selector"]
> * [.NET](media-services-get-media-processor.md)
> * [REST](media-services-rest-get-media-processor.md)
> 
> 

## <a name="overview"></a>Información general
En Media Services, un procesador multimedia es un componente que controla una tarea de procesamiento específica, como codificación, conversión de formato, cifrado o descifrado de contenido multimedia. Normalmente crea un procesador multimedia cuando crea una tarea para codificar, cifrar o convertir el formato de contenido multimedia.

## <a name="azure-media-processors"></a>Procesadores de multimedia de Azure 

En el siguiente tema se proporcionan listas de procesadores de multimedia:

* [Codificación de procesadores de multimedia](scenarios-and-availability.md#encoding-media-processors)
* [Procesadores de multimedia de Analytics](scenarios-and-availability.md#analytics-media-processors)

>[!NOTE]
>Al obtener acceso a las entidades de Media Services, debe establecer los campos de encabezado específicos y los valores en las solicitudes HTTP. Para obtener más información, consulte [Configuración del desarrollo de la API de REST de Media Services](media-services-rest-how-to-use.md).

## <a name="connect-to-media-services"></a>Conexión con Media Services

Para obtener más información sobre cómo conectarse a la API de Azure Media Services, consulte [Acceso a la API de Azure Media Services con la autenticación de Azure AD](media-services-use-aad-auth-to-access-ams-api.md). 

>[!NOTE]
>Después de conectarse correctamente a https://media.windows.net, recibirá una redirección 301 que especifica otro URI de Media Services. Debe realizar las llamadas posteriores al nuevo URI.

## <a name="get-a-media-processor"></a>Obtención de un procesador multimedia

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


## <a name="media-services-learning-paths"></a>Rutas de aprendizaje de Media Services
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Envío de comentarios
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Pasos siguientes
Ahora que sabe cómo obtener una instancia de procesador multimedia, consulte el tema sobre la [codificación de un recurso](media-services-rest-get-started.md) , que le mostrará cómo utilizar el servicio Media Encoder estándar para codificar un recurso.


