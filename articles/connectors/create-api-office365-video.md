<properties
pageTitle="Uso de la API de Office 365 Video en las aplicaciones lógicas | Microsoft Azure"
description="Introducción al uso de la API de Office 365 Video (conector) en las aplicaciones lógicas del Servicio de aplicaciones de Microsoft Azure"
services=""	
documentationCenter="" 	
authors="msftman"	
manager="erikre"	
editor=""
tags="connectors"/>

<tags
ms.service="multiple"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="na"
ms.date="02/18/2016"
ms.author="deonhe"/>

# Introducción a la API de Office365 Video

La API de Office 365 Video proporciona una API para trabajar con canales y vídeos de Office 365.

>[AZURE.NOTE] Esta versión del artículo se aplica a la versión de esquema 2015-08-01-preview de aplicaciones lógicas. Para la versión de esquema 2014-12-01-preview, haga clic en [Conector de Office365 Video](../app-service-logic/app-service-logic-connector-Office365 Video Connector.md).

Con Office365 Video, puede:

* Usarlo para crear aplicaciones lógicas

Para agregar una operación en aplicaciones lógicas, consulte [Creación de una nueva aplicación lógica mediante la conexión de servicios de SaaS](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Hablemos de acciones y desencadenadores

La API de Office365 Video puede usarse como una acción; no hay desencadenadores. Todas las API admiten datos en formato JSON y XML.

 La API de Office365 Video tiene las siguientes acciones y desencadenadores disponibles:

### Acciones de la API de Office365 Video
Puede realizar estas acciones:

|Acción|Descripción|
|--- | ---|
|IsVideoPortalEnabled|Comprueba el estado del portal de vídeo para ver si los servicios de vídeo están habilitados|
|ListViewableChannels|Obtiene todos los canales que los que el usuario tiene acceso para ver|
|ListVideos|Enumera todos los vídeos de Office365 presentes en un canal|
|GetVideo|Obtiene información sobre un determinado vídeo de Office365|
|GetPlaybackUrl|Obtiene la dirección URL de reproducción del manifiesto de Servicios multimedia de Azure para un vídeo|
|GetStreamingKeyAccessToken|Obtener el token de portador para obtener acceso para descifrar el vídeo|
ActionsTableReplaceMeLater## Creación de una conexión a la API de Office365 Video Para usar la API de Office365 Video, cree primero una **conexión** y después proporcione los detalles de estas propiedades:

|Propiedad| Obligatorio|Descripción|
| ---|---|---|
|Se necesita el cifrado de tokens|Sí|Proporcionar credenciales de SharePoint Online|


>[AZURE.TIP] Puede usar esta conexión en otras aplicaciones lógicas.

## Referencia de API de REST de Office365 Video
#### Esta documentación corresponde a la versión: 1.0


### Comprueba el estado del portal de vídeo para ver si los servicios de vídeo están habilitados
**```GET: /{tenant}/IsEnabled```**



| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|tenant|cadena|yes|path|Ninguna|El nombre del inquilino para el directorio del que el usuario forma parte|


### Estas son las posibles respuestas:

|Nombre|Descripción|
|---|---|
|200|Operación realizada correctamente|
|400|BadRequest|
|401|No autorizado|
|404|No encontrado|
|500|Internal Server Error|
|default|Error en la operación.|
------



### Obtiene todos los canales que los que el usuario tiene acceso para ver
**```GET: /{tenant}/Channels```**



| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|tenant|cadena|yes|path|Ninguna|El nombre del inquilino para el directorio del que el usuario forma parte|


### Estas son las posibles respuestas:

|Nombre|Descripción|
|---|---|
|200|Operación realizada correctamente|
|400|BadRequest|
|401|No autorizado|
|404|No encontrado|
|500|Internal Server Error|
|default|Error en la operación.|
------



### Enumera todos los vídeos de Office365 presentes en un canal
**```GET: /{tenant}/Channels/{channelId}/Videos```**



| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|tenant|cadena|yes|path|Ninguna|El nombre del inquilino para el directorio del que el usuario forma parte|
|channelId|cadena|yes|path|Ninguna|El identificador de canal desde el que necesitan recuperarse los vídeos|


### Estas son las posibles respuestas:

|Nombre|Descripción|
|---|---|
|200|Operación realizada correctamente|
|400|BadRequest|
|401|No autorizado|
|404|No encontrado|
|500|Internal Server Error|
|default|Error en la operación.|
------



### Obtiene información sobre un determinado vídeo de Office365
**```GET: /{tenant}/Channels/{channelId}/Videos/{videoId}```**



| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|tenant|cadena|yes|path|Ninguna|El nombre del inquilino para el directorio del que el usuario forma parte|
|channelId|cadena|yes|path|Ninguna|El identificador de canal|
|videoId|cadena|yes|path|Ninguna|El identificador de vídeo|


### Estas son las posibles respuestas:

|Nombre|Descripción|
|---|---|
|200|Operación realizada correctamente|
|400|BadRequest|
|401|No autorizado|
|404|No encontrado|
|500|Internal Server Error|
|default|Error en la operación.|
------



### Obtiene la dirección URL de reproducción del manifiesto de Servicios multimedia de Azure para un vídeo
**```GET: /{tenant}/Channels/{channelId}/Videos/{videoId}/playbackurl```**



| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|tenant|cadena|yes|path|Ninguna|El nombre del inquilino para el directorio del que el usuario forma parte|
|channelId|cadena|yes|path|Ninguna|El identificador de canal|
|videoId|cadena|yes|path|Ninguna|El identificador de vídeo|
|streamingFormatType|cadena|yes|query|Ninguna|Tipo de formato de streaming. 1 - Smooth Streaming o MPEG-DASH. 0 - Streaming HLS|


### Estas son las posibles respuestas:

|Nombre|Descripción|
|---|---|
|200|Operación realizada correctamente|
|400|BadRequest|
|401|No autorizado|
|404|No encontrado|
|500|Internal Server Error|
|default|Error en la operación.|
------



### Obtener el token de portador para obtener acceso para descifrar el vídeo
**```GET: /{tenant}/Channels/{channelId}/Videos/{videoId}/token```**



| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|tenant|cadena|yes|path|Ninguna|El nombre del inquilino para el directorio del que el usuario forma parte|
|channelId|cadena|yes|path|Ninguna|El identificador de canal|
|videoId|cadena|yes|path|Ninguna|El identificador de vídeo|


### Estas son las posibles respuestas:

|Nombre|Descripción|
|---|---|
|200|Operación realizada correctamente|
|400|BadRequest|
|401|No autorizado|
|404|No encontrado|
|500|Internal Server Error|
|default|Error en la operación.|
------



## Definiciones de objeto: 

 **Channel**: clase de canal

Propiedades obligatorias para Channel:


Ninguna de las propiedades es obligatoria.


**Todas las propiedades**:


| Nombre | Tipo de datos |
|---|---|
|Id|cadena|
|Título|cadena|
|Descripción|cadena|



 **Video**: clase de vídeo

Propiedades obligatorias para Video:


Ninguna de las propiedades es obligatoria.


**Todas las propiedades**:


| Nombre | Tipo de datos |
|---|---|
|Id|cadena|
|Título|cadena|
|Descripción|cadena|
|CreationDate|cadena|
|Propietario|cadena|
|ThumbnailUrl|cadena|
|VideoUrl|cadena|
|VideoDuration|integer|
|VideoProcessingStatus|integer|
|ViewCount|integer|


## Pasos siguientes
[Creación de una aplicación lógica](../app-service-logic/app-service-logic-create-a-logic-app.md).

<!---HONumber=AcomDC_0224_2016-->