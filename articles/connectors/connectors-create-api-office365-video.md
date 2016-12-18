---
title: Uso del conector de Office 365 Video en Logic Apps | Microsoft Docs
description: "Introducción al uso del conector de Office 365 Video en las aplicaciones lógicas del Servicio de aplicaciones de Microsoft Azure"
services: 
documentationcenter: 
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: 738e5aa7-2523-4116-8b65-211b9063852d
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/18/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 959eaca7aafd8516cfc6f3b5dd2a44ce21ec4825


---
# <a name="get-started-with-the-office365-video-connector"></a>Introducción al conector de Office 365 Video
Conéctese a Office 365 Video para conseguir información acerca de un vídeo Office 365 específico, obtener una lista de vídeos y mucho más. El conector de Office 365 Video puede usarse desde:

* Aplicaciones lógicas 

> [!NOTE]
> Esta versión del artículo se aplica a la versión de esquema 2015-08-01-preview de las aplicaciones lógicas. Este conector no se admite en las versiones anteriores del esquema.
> 
> 

Con Office 365 Video puede:

* Compilar el flujo de su negocio en función de los datos que obtiene de Office 365 Video. 
* Usar acciones que comprueban el estado del portal de vídeo, obtener una lista de todos los vídeos en un canal y mucho más. Estas acciones obtienen una respuesta y luego dejan el resultado a disposición de otras acciones. Por ejemplo, puede utilizar el conector de Búsqueda de Bing para buscar vídeos de Office 365 y, después, usar el conector de Office 365 Video para conseguir información sobre ese vídeo. Si el vídeo cumple sus requisitos, puede publicarlo en Facebook. 

Para agregar una operación en aplicaciones lógicas, consulte [Creación de una aplicación lógica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Desencadenadores y acciones
El conector de Office 365 Video tiene las siguientes acciones disponibles. No hay desencadenadores.

| Desencadenadores | Acciones |
| --- | --- |
| None |<ul><li>Comprueba el estado del portal de vídeo</li><li>Obtiene todos los canales visibles</li><li>Obtiene la dirección URL de reproducción del manifiesto de Servicios multimedia de Azure para un vídeo</li><li>Obtener el token de portador para obtener acceso para descifrar el vídeo</li><li>Obtiene información sobre un determinado vídeo de Office365 </li><li>Enumera todos los vídeos de Office365 presentes en un canal</li></ul> |

Todos los conectores admiten datos en formato JSON y XML. 

## <a name="create-a-connection-to-office365-video-connector"></a>Creación de una conexión al conector de Office 365 Video
Al agregar este conector a las aplicaciones lógicas, debe iniciar sesión en su cuenta de Office 365 Video y permitir que estas se conecten a su cuenta.

> [!INCLUDE [Steps to create a connection to Office 365 Video](../../includes/connectors-create-api-office365video.md)]
> 
> 

Después de crear la conexión, especifique las propiedades del vídeo de Office 365, como el nombre de inquilino o el identificador del canal. En la **referencia de la API de REST** de este tema se describen estas propiedades.

> [!TIP]
> Puede usar esta misma conexión de Office 365 Video en otras aplicaciones lógicas.
> 
> 

## <a name="swagger-rest-api-reference"></a>Referencia de la API de REST de Swagger
Se aplica a la versión: 1.0.

### <a name="checks-video-portal-status"></a>Comprueba el estado del portal de vídeo
Comprueba el estado del portal de vídeo para ver si los servicios de vídeo están habilitados.  
```GET: /{tenant}/IsEnabled``` 

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Descripción |
| --- | --- | --- | --- | --- | --- |
| tenant |cadena |Sí |path |None |El nombre del inquilino para el directorio del que el usuario forma parte |

#### <a name="response"></a>Respuesta
| Nombre | Descripción |
| --- | --- |
| 200 |Operación realizada correctamente |
| 400 |BadRequest |
| 401 |No autorizado |
| 404 |No encontrado |
| 500 |Internal Server Error |
| default |Error en la operación. |

### <a name="get-all-viewable-channels"></a>Obtiene todos los canales visibles
Obtiene todos los canales para los que el usuario tiene acceso de visión.  
```GET: /{tenant}/Channels``` 

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Descripción |
| --- | --- | --- | --- | --- | --- |
| tenant |cadena |Sí |path |None |El nombre del inquilino para el directorio del que el usuario forma parte |

#### <a name="response"></a>Respuesta
| Nombre | Descripción |
| --- | --- |
| 200 |Operación realizada correctamente |
| 400 |BadRequest |
| 401 |No autorizado |
| 404 |No encontrado |
| 500 |Internal Server Error |
| default |Error en la operación. |

### <a name="lists-all-the-office365-videos-present-in-a-channel"></a>Enumera todos los vídeos de Office365 presentes en un canal
Enumera todos los vídeos de Office365 presentes en un canal.  
```GET: /{tenant}/Channels/{channelId}/Videos``` 

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Descripción |
| --- | --- | --- | --- | --- | --- |
| tenant |cadena |Sí |path |None |El nombre del inquilino para el directorio del que el usuario forma parte |
| channelId |cadena |Sí |path |None |El identificador de canal desde el que necesitan recuperarse los vídeos |

#### <a name="response"></a>Respuesta
| Nombre | Descripción |
| --- | --- |
| 200 |Operación realizada correctamente |
| 400 |BadRequest |
| 401 |No autorizado |
| 404 |No encontrado |
| 500 |Internal Server Error |
| default |Error en la operación. |

### <a name="gets-information-about-a-particular-office365-video"></a>Obtiene información sobre un determinado vídeo de Office365 
Obtiene información sobre un determinado vídeo de Office365.  
```GET: /{tenant}/Channels/{channelId}/Videos/{videoId}``` 

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Descripción |
| --- | --- | --- | --- | --- | --- |
| tenant |cadena |Sí |path |None |El nombre del inquilino para el directorio del que el usuario forma parte |
| channelId |cadena |Sí |path |None |El identificador de canal |
| videoId |cadena |Sí |path |None |El identificador de vídeo |

#### <a name="response"></a>Respuesta
| Nombre | Descripción |
| --- | --- |
| 200 |Operación realizada correctamente |
| 400 |BadRequest |
| 401 |No autorizado |
| 404 |No encontrado |
| 500 |Internal Server Error |
| default |Error en la operación. |

### <a name="get-playback-url-of-the-azure-media-services-manifest-for-a-video"></a>Obtiene la dirección URL de reproducción del manifiesto de Servicios multimedia de Azure para un vídeo
Obtiene la dirección URL de reproducción del manifiesto de los Servicios multimedia de Azure para un vídeo.  
```GET: /{tenant}/Channels/{channelId}/Videos/{videoId}/playbackurl``` 

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Descripción |
| --- | --- | --- | --- | --- | --- |
| tenant |cadena |Sí |path |None |El nombre del inquilino para el directorio del que el usuario forma parte |
| channelId |cadena |Sí |path |None |El identificador de canal |
| videoId |cadena |Sí |path |None |El identificador de vídeo |
| streamingFormatType |cadena |yes |query |None |Tipo de formato de streaming. 1 - Smooth Streaming o MPEG-DASH. 0 - Streaming HLS |

#### <a name="response"></a>Respuesta
| Nombre | Descripción |
| --- | --- |
| 200 |Operación realizada correctamente |
| 400 |BadRequest |
| 401 |No autorizado |
| 404 |No encontrado |
| 500 |Internal Server Error |
| default |Error en la operación. |

### <a name="get-the-bearer-token-to-get-access-to-decrypt-the-video"></a>Obtener el token de portador para obtener acceso para descifrar el vídeo
Obtiene el token de portador para conseguir acceso con el fin de descifrar el vídeo.  
```GET: /{tenant}/Channels/{channelId}/Videos/{videoId}/token```

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Descripción |
| --- | --- | --- | --- | --- | --- |
| tenant |cadena |Sí |path |None |El nombre del inquilino para el directorio del que el usuario forma parte |
| channelId |cadena |Sí |path |None |El identificador de canal |
| videoId |cadena |Sí |path |None |El identificador de vídeo |

#### <a name="response"></a>Respuesta
| Nombre | Descripción |
| --- | --- |
| 200 |Operación realizada correctamente |
| 400 |BadRequest |
| 401 |No autorizado |
| 404 |No encontrado |
| 500 |Internal Server Error |
| default |Error en la operación. |

## <a name="object-definitions"></a>Definiciones de objeto
#### <a name="channel-channel-class"></a>Canal: Clase de canal
| Nombre | Tipo de datos | Obligatorio |
| --- | --- | --- |
| id |cadena |no |
| Título |cadena |no |
| Descripción |cadena |no |

#### <a name="video"></a>Vídeo
| Nombre | Tipo de datos | Obligatorio |
| --- | --- | --- |
| id |cadena |no |
| Título |cadena |no |
| Descripción |cadena |no |
| CreationDate |cadena |no |
| Propietario |cadena |no |
| ThumbnailUrl |cadena |no |
| VideoUrl |cadena |no |
| VideoDuration |integer |no |
| VideoProcessingStatus |integer |no |
| ViewCount |integer |no |

## <a name="next-steps"></a>Pasos siguientes
[Creación de una aplicación lógica](../app-service-logic/app-service-logic-create-a-logic-app.md).




<!--HONumber=Nov16_HO3-->


