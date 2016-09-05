<properties
    pageTitle="Incorporación del conector de Facebook a las aplicaciones lógicas | Microsoft Azure"
    description="Información general del conector de Facebook con parámetros de la API de REST"
    services=""
    documentationCenter="" 
    authors="MandiOhlinger"
    manager="erikre"
    editor=""
    tags="connectors"/>

<tags
   ms.service="multiple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="08/18/2016"
   ms.author="mandia"/>

# Introducción al conector de Facebook
Conéctese a Facebook y publique en una biografía, obtenga una fuente de página y mucho más.

>[AZURE.NOTE] Esta versión del artículo se aplica a la versión de esquema 2015-08-01-preview de las aplicaciones lógicas.


Con Facebook, puede hacer lo siguiente:

- Compilar el flujo de negocio en función de los datos que obtiene de Facebook.
- Utilizar un desencadenador cuando se reciba un nuevo mensaje.
- Usar acciones para publicar en la biografía, obtener una fuente de página y mucho más. Estas acciones obtienen una respuesta y luego dejan el resultado a disposición de otras acciones. Por ejemplo, cuando haya un nuevo mensaje en su biografía, puede tomar ese mensaje e insertarlo en su fuente de Twitter.



Para agregar una operación en aplicaciones lógicas, consulte [Creación de una nueva aplicación lógica mediante la conexión de servicios de SaaS](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Desencadenadores y acciones
El conector de Facebook incluye los siguientes desencadenadores y acciones.

| Desencadenadores | Acciones|
| --- | --- |
| <ul><li>Cuando haya un nuevo mensaje en mi biografía</li></ul> |<ul><li>Obtener fuente de mi biografía</li><li>Publicar en mi biografía</li><li>Cuando haya un nuevo mensaje en mi biografía</li><li>Obtener fuente de página</li><li>Obtener biografía de usuario</li><li>Publicar en la página</li></ul>

Todos los conectores admiten datos en formato JSON y XML.

## Creación de una conexión a Facebook
Al agregar este conector a las aplicaciones lógicas, debe autorizar a estas para que se conecten a su Facebook.

1. Inicie sesión en su cuenta de Facebook.
2. Seleccione **Autorizar** y permita que sus aplicaciones lógicas se conecten y utilicen su aplicación de Facebook.

>[AZURE.INCLUDE [Pasos para crear una conexión a Facebook](../../includes/connectors-create-api-facebook.md)]

>[AZURE.TIP] Puede usar esta misma conexión de Facebook en otras aplicaciones lógicas.

## Referencia de la API de REST de Swagger
Se aplica a la versión: 1.0.

### Obtener fuente de mi biografía
Obtiene las fuentes de la biografía del usuario que ha iniciado sesión. ```GET: /me/feed```

| Nombre|Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Description|
| ---|---|---|---|---|---|
|fields|string|no|query|Ninguna |Especificar los campos que desea que se devuelvan. Ejemplo (id,name,picture).|
|limit|integer|no|query| Ninguna|Número máximo de mensajes que se van a recuperar|
|por|string|no|query| Ninguna|Restringir la lista de mensajes a solo aquellos con ubicación adjuntada.|
|filter|string|no|query| Ninguna|Recuperar solo los mensajes que coincidan con un filtro de transmisión en particular.|

#### Response
|Nombre|Descripción|
|---|---|
|200|OK|
|400|Bad Request|
|500|Internal Server Error|
|default|Error en la operación.|


### Publicar en mi biografía
Publica un mensaje de estado en la biografía del usuario que ha iniciado la sesión. ```POST: /me/feed```

| Nombre|Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Description|
| ---|---|---|---|---|---|
|post|string |yes|body|Ninguna |Nuevo mensaje que se va a publicar|

#### Response
|Nombre|Descripción|
|---|---|
|200|OK|
|400|Bad Request|
|500|Internal Server Error|
|default|Error en la operación.|


### Cuando haya un nuevo mensaje en mi biografía
Desencadena un nuevo flujo cuando hay un nuevo mensaje en la biografía del usuario que ha iniciado la sesión. ```GET: /trigger/me/feed```

No hay ningún parámetro.

#### Response
|Nombre|Descripción|
|---|---|
|200|OK|
|400|Bad Request|
|500|Internal Server Error|
|default|Error en la operación.|


### Obtener fuente de página
Obtiene entradas de la fuente de una página especificada. ```GET: /{pageId}/feed```

| Nombre|Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Description|
| ---|---|---|---|---|---|
|pageId|string|yes|path| Ninguna|Identificador de la página de la que se deben recuperar los mensajes.|
|limit|integer|no|query| Ninguna|Número máximo de mensajes que se van a recuperar|
|include\_hidden|boolean|no|query|Ninguna |Si se incluirán o no los mensajes que haya ocultado la página|
|fields|string|no|query|Ninguna |Especificar los campos que desea que se devuelvan. Ejemplo (id,name,picture).|

#### Response
|Nombre|Descripción|
|---|---|
|200|OK|
|400|Bad Request|
|500|Internal Server Error|
|default|Error en la operación.|


### Obtener biografía de usuario
Obtiene los mensajes de la biografía de un usuario. ```GET: /{userId}/feed```

| Nombre|Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Description|
| ---|---|---|---|---|---|
|userId|string|yes|path|Ninguna |Id. del usuario cuya biografía se debe recuperar.|
|limit|integer|no|query|Ninguna |Número máximo de mensajes que se van a recuperar|
|por|string|no|query|Ninguna |Restringir la lista de mensajes a solo aquellos con ubicación adjuntada.|
|filter|string|no|query| Ninguna|Recuperar solo los mensajes que coincidan con un filtro de transmisión en particular.|
|fields|string|no|query| Ninguna|Especificar los campos que desea que se devuelvan. Ejemplo (id,name,picture).|

#### Response
|Nombre|Descripción|
|---|---|
|200|OK|
|400|Bad Request|
|500|Internal Server Error|
|default|Error en la operación.|


### Publicar en la página
Publica un mensaje en una página de Facebook con el usuario que inició sesión. ```POST: /{pageId}/feed```

| Nombre|Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Description|
| ---|---|---|---|---|---|
|pageId|string|yes|path|Ninguna |Id. de la página que se va a publicar|
|post|many |yes|body|Ninguna |Nuevo mensaje para publicar.|

#### Response
|Nombre|Descripción|
|---|---|
|200|OK|
|400|Bad Request|
|500|Internal Server Error|
|default|Error en la operación.|


## Definiciones de objeto

#### GetFeedResponse

|Nombre de propiedad | Tipo de datos | Obligatorio|
|---|---|---|
|data|array|no|

#### TriggerFeedResponse

|Nombre de propiedad | Tipo de datos |Obligatorio|
|---|---|---|
|data|array|no|

#### PostItem: una sola entrada en la fuente de un perfil
El perfil puede ser un usuario, una página, una aplicación o un grupo.

|Nombre de propiedad | Tipo de datos |Obligatorio|
|---|---|---|
|id|string|no|
|admin\_creator|array|no|
|caption|string|no|
|created\_time|string|no|
|description|string|no|
|feed\_targeting|not defined|no|
|from|not defined|no|
|icon|string|no|
|is\_hidden|boolean|no|
|is\_published|boolean|no|
|link|string|no|
|message|string|no|
|name|string|no|
|object\_id|string|no|
|picture|string|no|
|place|not defined|no|
|privacy|not defined|no|
|propiedades|array|no|
|de origen|string|no|
|status\_type|string|no|
|story|string|no|
|targeting|not defined|no|
|to|array|no|
|type|string|no|
|updated\_time|string|no|
|with\_tags|not defined|no|

#### TriggerItem: una sola entrada en la fuente de un perfil
El perfil puede ser un usuario, una página, una aplicación o un grupo.

|Nombre de propiedad | Tipo de datos |Obligatorio|
|---|---|---|
|id|string|no|
|created\_time|string|no|
|from|not defined|no|
|message|string|no|
|type|string|no|

#### AdminItem

|Nombre de propiedad | Tipo de datos |Obligatorio|
|---|---|---|
|id|string|no|
|link|string|no|

#### PropertyItem

|Nombre de propiedad | Tipo de datos |Obligatorio|
|---|---|---|
|name|string|no|
|text|string|no|
|href|string|no|

#### UserPostFeedRequest

|Nombre de propiedad | Tipo de datos |Obligatorio|
|---|---|---|
|message|string|yes|
|link|string|no|
|picture|string|no|
|name|string|no|
|caption|string|no|
|description|string|no|
|place|string|no|
|etiquetas|string|no|
|privacy|not defined|no|
|object\_attachment|string|no|

#### PagePostFeedRequest

|Nombre de propiedad | Tipo de datos |Obligatorio|
|---|---|---|
|message|string|yes|
|link|string|no|
|picture|string|no|
|name|string|no|
|caption|string|no|
|description|string|no|
|actions|array|no|
|place|string|no|
|etiquetas|string|no|
|object\_attachment|string|no|
|targeting|not defined|no|
|feed\_targeting|not defined|no|
|published|boolean|no|
|scheduled\_publish\_time|string|no|
|backdated\_time|string|no|
|backdated\_time\_granularity|string|no|
|child\_attachments|array|no|
|multi\_share\_end\_card|boolean|no|

#### PostFeedResponse

|Nombre de propiedad | Tipo de datos |Obligatorio|
|---|---|---|
|id|string|no|

#### ProfileCollection

|Nombre de propiedad | Tipo de datos |Obligatorio|
|---|---|---|
|data|array|no|

#### UserItem

|Nombre de propiedad | Tipo de datos |Obligatorio|
|---|---|---|
|id|string|no|
|first\_name|string|no|
|last\_name|string|no|
|name|string|no|
|gender|string|no|
|about|string|no|

#### ActionItem

|Nombre de propiedad | Tipo de datos |Obligatorio|
|---|---|---|
|name|string|no|
|link|string|no|

#### TargetItem

|Nombre de propiedad | Tipo de datos |Obligatorio|
|---|---|---|
|countries|array|no|
|locales|array|no|
|regions|array|no|
|cities|array|no|

#### FeedTargetItem: objeto que controla la dirección de la fuente de noticias de este mensaje
Es más probable que cualquier miembro de estos grupos vea este mensaje y existe una menor probabilidad en el caso de otras personas. Solo se aplica a las páginas.

|Nombre de propiedad | Tipo de datos |Obligatorio|
|---|---|---|
|countries|array|no|
|regions|array|no|
|cities|array|no|
|age\_min|integer|no|
|age\_max|integer|no|
|genders|array|no|
|relationship\_statuses|array|no|
|interested\_in|array|no|
|college\_years|array|no|
|interests|array|no|
|relevant\_until|integer|no|
|education\_statuses|array|no|
|locales|array|no|

#### PlaceItem

|Nombre de propiedad | Tipo de datos |Obligatorio|
|---|---|---|
|id|string|no|
|name|string|no|
|overall\_rating|número|no|
|location|not defined|no|

#### LocationItem

|Nombre de propiedad | Tipo de datos |Obligatorio|
|---|---|---|
|city|string|no|
|country|string|no|
|latitude|número|no|
|located\_in|string|no|
|longitude|número|no|
|name|string|no|
|region|string|no|
|state|string|no|
|street|string|no|
|zip|string|no|

#### PrivacyItem

|Nombre de propiedad | Tipo de datos |Obligatorio|
|---|---|---|
|description|string|no|
|value|string|yes|
|allow|string|no|
|deny|string|no|
|friends|string|no|

#### ChildAttachmentsItem

|Nombre de propiedad | Tipo de datos |Obligatorio|
|---|---|---|
|link|string|no|
|picture|string|no|
|image\_hash|string|no|
|name|string|no|
|description|string|no|

#### PostPhotoRequest

|Nombre de propiedad | Tipo de datos |Obligatorio|
|---|---|---|
|url|string|yes|
|caption|string|no|

#### PostPhotoResponse

|Nombre de propiedad | Tipo de datos |Obligatorio|
|---|---|---|
|id|string|yes|
|post\_id|string|yes|

#### PostVideoRequest

|Nombre de propiedad | Tipo de datos |Obligatorio|
|---|---|---|
|videoData|string|yes|
|description|string|yes|
|título|string|yes|
|uploadedVideoName|string|no|

#### GetPhotoResponse

|Nombre de propiedad | Tipo de datos |Obligatorio|
|---|---|---|
|data|not defined|yes|

#### GetPhotoResponseItem

|Nombre de propiedad | Tipo de datos |Obligatorio|
|---|---|---|
|url|string|yes|
|is\_silhouette|boolean|yes|
|height|string|no|
|width|string|no|

#### GetEventResponse

|Nombre de propiedad | Tipo de datos |Obligatorio|
|---|---|---|
|data|array|yes|

#### GetEventResponseItem

|Nombre de propiedad | Tipo de datos |Obligatorio|
|---|---|---|
|id|string|yes|
|name|string|yes|
|start\_time|string|no|
|end\_time|string|no|
|timezone|string|no|
|location|string|no|
|description|string|no|
|ticket\_uri|string|no|
|rsvp\_status|string|yes|


## Pasos siguientes

[Creación de una aplicación lógica](../app-service-logic/app-service-logic-create-a-logic-app.md)

<!---HONumber=AcomDC_0824_2016-->