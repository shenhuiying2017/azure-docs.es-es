<properties
pageTitle="Trello | Microsoft Azure"
description="Cree aplicaciones lógicas con el Servicio de aplicaciones de Azure. Trello le ofrece perspectiva sobre todos los proyectos, en el trabajo y en casa. Es una manera fácil, gratis, flexible y visual de administrar los proyectos y organizar todo. Conéctese a Trello para administrar los paneles, listas y tarjetas"
services="app-servicelogic"	
documentationCenter=".net,nodejs,java" 	
authors="msftman"	
manager="erikre"	
editor=""
tags="connectors" />

<tags
ms.service="logic-apps"
ms.devlang="multiple"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="integration"
ms.date="05/18/2016"
ms.author="deonhe"/>

# Introducción al conector Trello



El conector Trello puede usarse desde:

- [Aplicaciones lógicas](../app-service-logic/app-service-logic-what-are-logic-apps.md)
- [PowerApps](http://powerapps.microsoft.com)
- [Flujo](http://flows.microsoft.com)

>[AZURE.NOTE] Esta versión del artículo se aplica a la versión de esquema 2015-08-01-preview de las aplicaciones lógicas.

Puede empezar creando una aplicación lógica ahora. Para ello, consulte [Crear una aplicación lógica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Desencadenadores y acciones

El conector Trello se puede usar como acción; tiene desencadenadores. Todos los conectores admiten datos en formato JSON y XML.

 El conector Trello tiene las siguientes acciones y desencadenadores disponibles:

### Acciones de Trello
Puede realizar estas acciones:

|Acción|Descripción|
|--- | ---|
|[ListCards](connectors-create-api-trello.md#listcards)|Enumera las tarjetas en el panel|
|[GetCard](connectors-create-api-trello.md#getcard)|Obtiene las tarjetas según el identificador|
|[UpdateCard](connectors-create-api-trello.md#updatecard)|Actualiza las tarjetas|
|[DeleteCard](connectors-create-api-trello.md#deletecard)|Elimina las tarjetas|
|[CreateCard](connectors-create-api-trello.md#createcard)|Crea una nueva tarjeta en la cuenta de Trello|
|[ListBoards](connectors-create-api-trello.md#listboards)|Enumera los paneles|
|[GetBoard](connectors-create-api-trello.md#getboard)|Obtiene paneles según el identificador|
|[ListLists](connectors-create-api-trello.md#listlists)|Enumera listas de tarjetas en el panel|
|[GetList](connectors-create-api-trello.md#getlist)|Obtiene listas según el identificador|
### Desencadenadores de Trello
Se pueden escuchar estos eventos:

|Desencadenador | Descripción|
|--- | ---|
|Cuando se agrega una nueva tarjeta a un panel|Se desencadena un flujo cuando se agrega una nueva tarjeta a un panel|
|Cuando se agrega una nueva tarjeta a una lista|Se desencadena un flujo cuando se agrega una nueva tarjeta a una lista|


## Creación de una conexión a Trello
Para crear aplicaciones lógicas con Trello, primero debe crear una **conexión** y, a continuación, proporcionar los detalles de las siguientes propiedades:

|Propiedad| Obligatorio|Descripción|
| ---|---|---|
|Se necesita el cifrado de tokens|Sí|Proporciona las credenciales de Trello|
Después de crear la conexión, puede usarla para ejecutar las acciones y escuchar los desencadenadores descritos en este artículo.

>[AZURE.INCLUDE [Pasos para crear una conexión a Trello](../../includes/connectors-create-api-trello.md)]

>[AZURE.TIP] Puede usar esta conexión en otras aplicaciones lógicas.

## Referencia para Trello
Se aplica a la versión: 1.0

## OnNewCardInBoard
Cuando se agrega una nueva tarjeta a un panel: desencadena un flujo cuando se agrega una nueva tarjeta a un panel

```GET: /trigger/boards/{board_id}/cards```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|board\_id|cadena|yes|path|Ninguna|Identificador único del panel en el que capturar las tarjetas|

#### Respuesta

|Nombre|Descripción|
|---|---|
|200|OK|
|400|Bad Request|
|401|No autorizado|
|403|Prohibido|
|404|No encontrado|
|500|Error interno del servidor. Error desconocido|
|default|Error en la operación|


## OnNewCardInList
Cuando se agrega una nueva tarjeta a una lista: desencadena un flujo cuando se agrega una nueva tarjeta a una lista

```GET: /trigger/lists/{list_id}/cards```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|board\_id|cadena|yes|query|Ninguna|Identificador único del panel en el que capturar las tarjetas|
|list\_id|cadena|yes|path|Ninguna|Identificador único de la lista en la que capturar las tarjetas|

#### Respuesta

|Nombre|Descripción|
|---|---|
|200|OK|
|400|Bad Request|
|401|No autorizado|
|403|Prohibido|
|404|No encontrado|
|500|Error interno del servidor. Error desconocido|
|default|Error en la operación|


## ListCards
Enumera las tarjetas en el panel: enumera las tarjetas en el panel

```GET: /boards/{board_id}/cards```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|board\_id|cadena|yes|path|Ninguna|Identificador del panel en el que capturar todas las tarjetas|
|actions|cadena|no|query|Ninguna|Enumera las acciones a devolver. Especifica 'all' o una lista de valores válidos separados por comas|
|attachments|boolean|no|query|Ninguna|Muestra los datos adjuntos|
|attachment\_fields|cadena|no|query|Ninguna|Enumera los campos de datos adjuntos a devolver. Especifica 'all' o una lista de valores válidos separados por comas|
|stickers|boolean|no|query|Ninguna|Muestra los adhesivos|
|members|boolean|no|query|Ninguna|Muestra los miembros|
|member\_fields|cadena|no|query|Ninguna|Enumera los campos de los miembros a devolver. Especifica 'all' o una lista de valores válidos separados por comas|
|CheckItemStates|boolean|no|query|Ninguna|Devuelve los estados de la tarjeta|
|Checklists|cadena|no|query|Ninguna|Muestra las listas de comprobación|
|limit|integer|no|query|Ninguna|El número máximo de resultados que se devolverán, entre 1 y 1000|
|since|cadena|no|query|Ninguna|Captura todas las tarjetas posteriores a esta fecha|
|antes de|cadena|no|query|Ninguna|Captura todas las tarjetas anteriores a esta fecha|
|filter|cadena|no|query|Ninguna|Filtra la respuesta|
|fields|cadena|no|query|Ninguna|Enumera los campos de las tarjetas a devolver. Especifica 'all' o una lista de valores válidos separados por comas|

#### Respuesta

|Nombre|Descripción|
|---|---|
|200|OK|
|400|Bad Request|
|401|No autorizado|
|403|Prohibido|
|404|No encontrado|
|500|Error interno del servidor. Error desconocido|
|default|Error en la operación|


## GetCard
Obtiene la tarjeta según el identificador: obtiene la tarjeta según el identificador

```GET: /cards/{card_id}```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|board\_id|cadena|yes|query|Ninguna|Identificador del panel en el que capturar las tarjetas|
|card\_id|cadena|yes|path|Ninguna|Identificador de la tarjeta a capturar|
|actions|cadena|no|query|Ninguna|Enumera las acciones a devolver. Especifica 'all' o una lista de valores válidos separados por comas|
|actions\_entities|boolean|no|query|Ninguna|Devuelve entidades de acción|
|actions\_display|boolean|no|query|Ninguna|Devuelve presentaciones de acciones|
|actions\_limit|integer|no|query|Ninguna|Número máximo de acciones a devolver|
|action\_fields|cadena|no|query|Ninguna|Lista de campos de acción a devolver para cada acción. Especifica 'all' o una lista de valores válidos separados por comas|
|action\_memberCreator\_fields|cadena|no|query|Ninguna|Lista de campos de creador de miembros de acciones a devolver. Especifica 'all' o una lista de valores válidos separados por comas|
|attachments|boolean|no|query|Ninguna|Devuelve datos adjuntos|
|attachement\_fields|cadena|no|query|Ninguna|Lista de campos de datos adjuntos a devolver para cada archivo adjunto. Especifica 'all' o una lista de valores válidos separados por comas|
|members|boolean|no|query|Ninguna|Devuelve miembros|
|member\_fields|cadena|no|query|Ninguna|Lista de campos de miembros a devolver para cada miembro. Especifica 'all' o una lista de valores válidos separados por comas|
|membersVoted|boolean|no|query|Ninguna|Devuelve miembros votados|
|memberVoted\_fields|cadena|no|query|Ninguna|Lista de campos de miembros votados a devolver para cada miembro votado. Especifica 'all' o una lista de valores válidos separados por comas|
|checkItemStates|boolean|no|query|Ninguna|Devuelve estados de la tarjeta|
|checkItemState\_fields|cadena|no|query|Ninguna|Lista de campos de estado que se van a devolver para cada estado del artículo de la tarjeta. Especifica 'all' o una lista de valores válidos separados por comas|
|checklists|cadena|no|query|Ninguna|Devuelve listas de comprobación|
|checklist\_fields|cadena|no|query|Ninguna|Lista de campos de la lista de comprobación a devolver para cada lista de comprobación. Especifica 'all' o una lista de valores válidos separados por comas|
|board|boolean|no|query|Ninguna|Devuelve el panel al que pertenece la tarjeta|
|board\_fields|cadena|no|query|Ninguna|Enumera los campos de los paneles a devolver. Especifica 'all' o una lista de valores válidos separados por comas|
|list|boolean|no|query|Ninguna|Devuelve la lista a la que pertenece la tarjeta|
|list\_fields|cadena|no|query|Ninguna|Enumera los campos de la lista a devolver. Especifica 'all' o una lista de valores válidos separados por comas|
|stickers|boolean|no|query|Ninguna|Devuelve adhesivos|
|sticker\_fields|cadena|no|query|Ninguna|Lista de los campos de adhesivo a devolver para cada adhesivo. Especifica 'all' o una lista de valores válidos separados por comas|
|fields|cadena|no|query|Ninguna|Enumera los campos de las tarjetas a devolver. Especifica 'all' o una lista de valores válidos separados por comas|

#### Respuesta

|Nombre|Descripción|
|---|---|
|200|OK|
|400|Bad Request|
|401|No autorizado|
|403|Prohibido|
|404|No encontrado|
|500|Error interno del servidor. Error desconocido|
|default|Error en la operación|


## UpdateCard
Tarjeta de actualización: tarjeta de actualización

```PUT: /cards/{card_id}```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|board\_id|cadena|yes|query|Ninguna|Identificador del panel desde el que capturar las tarjetas|
|card\_id|cadena|yes|path|Ninguna|Identificador de la tarjeta a actualizar|
|updateCard| |yes|body|Ninguna|Valores actualizados de tarjeta|

#### Respuesta

|Nombre|Descripción|
|---|---|
|200|OK|
|400|Bad Request|
|401|No autorizado|
|403|Prohibido|
|404|No encontrado|
|500|Error interno del servidor. Error desconocido|
|default|Error en la operación|


## DeleteCard
Elimina la tarjeta: elimina la tarjeta

```DELETE: /cards/{card_id}```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|board\_id|cadena|yes|query|Ninguna|Identificador del panel desde el que capturar las tarjetas|
|card\_id|cadena|yes|path|Ninguna|Identificador de la tarjeta a eliminar|

#### Respuesta

|Nombre|Descripción|
|---|---|
|200|OK|
|400|Bad Request|
|401|No autorizado|
|403|Prohibido|
|404|No encontrado|
|500|Error interno del servidor. Error desconocido|
|default|Error en la operación|


## CreateCard
Crea tarjetas: crea una nueva tarjeta en la cuenta de Trello

```POST: /cards```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|board\_id|cadena|yes|query|Ninguna|Identificador único del panel en el que crear las tarjetas|
|newCard| |yes|body|Ninguna|Nueva tarjeta para agregar al panel de Trello|

#### Respuesta

|Nombre|Descripción|
|---|---|
|200|OK|
|400|Bad Request|
|401|No autorizado|
|403|Prohibido|
|404|No encontrado|
|500|Error interno del servidor. Error desconocido|
|default|Error en la operación|


## ListBoards
Enumera los paneles: enumera los paneles

```GET: /member/me/boards```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|filter|cadena|no|query|Ninguna|Enumera los filtros a aplicar a los resultados del panel. Especifica 'all' o una lista de valores válidos separados por comas|
|fields|cadena|no|query|Ninguna|Enumera los campos de los paneles a devolver. Especifica 'all' o una lista de valores válidos separados por comas|
|actions|cadena|no|query|Ninguna|Enumera los campos de acción a devolver. Especifica 'all' o una lista de valores válidos separados por comas|
|actions\_entities|boolean|no|query|Ninguna|Devuelve entidades de acción|
|actions\_limit|integer|no|query|Ninguna|Número máximo de acciones a devolver|
|actions\_format|cadena|no|query|Ninguna|Especifica el formato de las acciones a devolver|
|actions\_since|cadena|no|query|Ninguna|Devuelve las acciones posteriores a la fecha especificada|
|action\_fields|cadena|no|query|Ninguna|Enumera los campos de la acción a devolver. Especifica 'all' o una lista de valores válidos separados por comas|
|memberships|cadena|no|query|Ninguna|Especifica la información de pertenencia a devolver. Especifica 'all' o una lista de valores válidos separados por comas|
|organization|boolean|no|query|Ninguna|Especifica la información de la organización a devolver|
|organization\_fields|cadena|no|query|Ninguna|Enumera los campos de la organización a devolver. Especifica 'all' o una lista de valores válidos separados por comas|
|lists|cadena|no|query|Ninguna|Especifica si desea devolver listas que pertenecen al panel|

#### Respuesta

|Nombre|Descripción|
|---|---|
|200|OK|
|400|Bad Request|
|401|No autorizado|
|403|Prohibido|
|404|No encontrado|
|500|Error interno del servidor. Error desconocido|
|default|Error en la operación|


## GetBoard
Obtiene el panel según el identificador: obtiene el panel según el identificador.

```GET: /boards/{board_id}```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|board\_id|cadena|yes|path|Ninguna|Identificador único del panel a obtener|
|actions|cadena|no|query|Ninguna|Enumera las acciones a devolver. Especifica 'all' o una lista de valores válidos separados por comas|
|action\_entities|boolean|no|query|Ninguna|Especifica si desea devolver entidades de acción|
|actions\_display|boolean|no|query|Ninguna|Especifica si desea devolver presentaciones de acciones|
|actions\_format|cadena|no|query|Ninguna|Especifica el formato de las acciones a devolver|
|actions\_since|cadena|no|query|Ninguna|Solo se devuelven las acciones posteriores a esta fecha|
|actions\_limit|integer|no|query|Ninguna|Número máximo de acciones a devolver|
|action\_fields|cadena|no|query|Ninguna|Enumera los campos a devolver con cada campo. Especifica 'all' o una lista de valores válidos separados por comas|
|action\_member|boolean|no|query|Ninguna|Especifica si desea devolver miembros de acción|
|action\_member\_fields|cadena|no|query|Ninguna|Enumera los campos de miembros a devolver con cada miembro de la acción. Especifica 'all' o una lista de valores válidos separados por comas|
|action\_memberCreator|boolean|no|query|Ninguna|Especifica si desea devolver un creador de miembros de acciones|
|action\_memberCreator\_fields|cadena|no|query|Ninguna|Enumera los campos de creador de miembros de acciones a devolver. Especifica 'all' o una lista de valores válidos separados por comas|
|cards|cadena|no|query|Ninguna|Especifica las tarjetas a devolver|
|card\_fields|cadena|no|query|Ninguna|Enumera los campos a devolver para cada tarjeta. Especifica 'all' o una lista de valores válidos separados por comas|
|card\_attachments|boolean|yes|query|Ninguna|Especifica si desea devolver los datos adjuntos de tarjetas|
|card\_attachment\_fields|cadena|no|query|Ninguna|Enumera los campos de datos adjuntos a devolver para cada archivo adjunto. Especifica 'all' o una lista de valores válidos separados por comas|
|card\_checklists|cadena|no|query|Ninguna|Especifica las listas de comprobación a devolver para cada tarjeta|
|card\_stickers|boolean|no|query|Ninguna|Especifica si desea devolver adhesivos de tarjetas|
|boardStarts|cadena|no|query|Ninguna|Especifica las estrellas del panel a devolver|
|etiquetas|cadena|no|query|Ninguna|Especifica las etiquetas a devolver|
|label\_fields|cadena|no|query|Ninguna|Enumera los campos de etiqueta a devolver para cada etiqueta. Especifica 'all' o una lista de valores válidos separados por comas|
|labels\_limits|integer|no|query|Ninguna|Número máximo de etiquetas a devolver|
|lists|cadena|no|query|Ninguna|Especifica las listas a devolver|
|list\_fields|cadena|no|query|Ninguna|Enumera los campos de lista a devolver para cada lista. Especifica 'all' o una lista de valores válidos separados por comas|
|memberships|cadena|no|query|Ninguna|Enumera las pertenencias a devolver. Especifica 'all' o una lista de valores válidos separados por comas|
|memberships\_member|boolean|no|query|Ninguna|Especifica si desea devolver miembros de pertenencia|
|memberships\_member\_fields|cadena|no|query|Ninguna|Enumera los campos de miembros a devolver para cada miembro de pertenencia. Especifica 'all' o una lista de valores válidos separados por comas|
|members|cadena|no|query|Ninguna|Enumera los miembros a devolver.|
|member\_fields|cadena|no|query|Ninguna|Enumera los campos de miembros a devolver para cada miembro. Especifica 'all' o una lista de valores válidos separados por comas|
|membersInvited|cadena|no|query|Ninguna|Especifica los miembros invitados a devolver|
|membersInvited\_fields|cadena|no|query|Ninguna|Enumera los campos a devolver para cada miembro invitado. Especifica 'all' o una lista de valores válidos separados por comas|
|checklists|cadena|no|query|Ninguna|Especifica las listas de comprobación a devolver|
|checklist\_fields|cadena|no|query|Ninguna|Enumera los campos de la lista de comprobación a devolver para cada lista de comprobación. Especifica 'all' o una lista de valores válidos separados por comas|
|organization|boolean|no|query|Ninguna|Especifica si desea devolver la información de la organización|
|organization\_fields|cadena|no|query|Ninguna|Enumera los campos de la organización a devolver para cada organización. Especifica 'all' o una lista de valores válidos separados por comas|
|organization\_memberships|cadena|no|query|Ninguna|Enumera las pertenencias de la organización a devolver. Especifica 'all' o una lista de valores válidos separados por comas|
|myPerfs|boolean|no|query|Ninguna|Especifica si desea devolver mis rendimientos|
|fields|cadena|no|query|Ninguna|Enumera los campos a devolver. Especifica 'all' o una lista de valores válidos separados por comas|

#### Respuesta

|Nombre|Descripción|
|---|---|
|200|OK|
|400|Bad Request|
|401|No autorizado|
|403|Prohibido|
|404|No encontrado|
|500|Error interno del servidor. Error desconocido|
|default|Error en la operación|


## ListLists
Enumera las listas de tarjeta en el panel: enumera las listas de tarjeta en el panel

```GET: /boards/{board_id}/lists```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|board\_id|cadena|yes|path|Ninguna|Identificador único del panel en el que capturar las listas|
|cards|cadena|no|query|Ninguna|Especifica las tarjetas a devolver|
|card\_fields|cadena|no|query|Ninguna|Enumera los campos de las tarjetas desde los que devolver. Especifica 'all' o una lista de valores válidos separados por comas|
|filter|cadena|no|query|Ninguna|Especifica la propiedad de filtro para las listas|
|fields|cadena|no|query|Ninguna|Enumera los campos a devolver. Especifica 'all' o una lista de valores válidos separados por comas|

#### Respuesta

|Nombre|Descripción|
|---|---|
|200|OK|
|400|Bad Request|
|401|No autorizado|
|403|Prohibido|
|404|No encontrado|
|500|Error interno del servidor. Error desconocido|
|default|Error en la operación|


## GetList
Obtiene listas según el identificador: obtiene listas según el identificador

```GET: /lists/{list_id}```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|board\_id|cadena|yes|query|Ninguna|Identificador único del panel desde el que capturar las listas|
|list\_id|cadena|yes|path|Ninguna|Identificador único de la lista a capturar|
|cards|cadena|no|query|Ninguna|Especifica las tarjetas a devolver|
|card\_fields|cadena|no|query|Ninguna|Enumera los campos de tarjeta a devolver para cada tarjeta. Especifica 'all' o una lista de valores válidos separados por comas|
|board|boolean|no|query|Ninguna|Especifica si desea devolver información del panel|
|board\_fields|cadena|no|query|Ninguna|Enumera los campos de los paneles a devolver. Especifica 'all' o una lista de valores válidos separados por comas|
|fields|cadena|no|query|Ninguna|Enumera los campos de la lista a devolver. Especifica 'all' o una lista de valores válidos separados por comas|

#### Respuesta

|Nombre|Descripción|
|---|---|
|200|OK|
|400|Bad Request|
|401|No autorizado|
|403|Prohibido|
|404|No encontrado|
|500|Error interno del servidor. Error desconocido|
|default|Error en la operación|


## Definiciones de objeto 

### Card


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|id|cadena|No |
|checkItemStates|array|No |
|closed|boolean|No |
|dateLastActivity|cadena|No |
|desc|cadena|No |
|idBoard|cadena|No |
|idList|cadena|No |
|idMembersVoted|array|No |
|idShort|integer|No |
|idAttachmentCover|cadena|No |
|manualCoverAttachment|boolean|No |
|idLabels|array|No |
|name|cadena|No |
|pos|integer|No |
|shortLink|cadena|No |
|badges|not defined|No |
|due|cadena|No |
|email|cadena|No |
|shortUrl|cadena|No |
|subscribed|boolean|No |
|url|cadena|No |



### Notificaciones


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|Votes|integer|No |
|ViewingMemberVoted|boolean|No |
|Subscribed|boolean|No |
|Fogbugz|cadena|No |
|CheckItems|integer|No |
|CheckItemsChecked|integer|No |
|Comentarios|integer|No |
|Datos adjuntos|integer|No |
|Descripción|boolean|No |
|Due|cadena|No |



### Objeto


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|



### CreateCard


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|idList|cadena|Sí |
|name|cadena|Sí |
|desc|cadena|No |
|pos|cadena|No |
|idMembers|cadena|No |
|idLabels|cadena|No |
|urlSource|cadena|No |
|fileSource|cadena|No |
|idCardSource|cadena|No |
|keepFromSource|cadena|No |



### UpdateCard


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|name|cadena|No |
|desc|cadena|No |
|closed|boolean|No |
|idMembers|cadena|No |
|idAttachmentCover|cadena|No |
|idList|cadena|No |
|idBoard|cadena|No |
|pos|cadena|No |
|due|cadena|No |
|subscribed|boolean|No |



### Board


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|id|cadena|No |
|closed|boolean|No |
|dateLastActivity|cadena|No |
|dateLastView|cadena|No |
|desc|cadena|No |
|idOrganization|cadena|No |
|invitations|array|No |
|invited|boolean|No |
|labelNames|not defined|No |
|memberships|array|No |
|name|cadena|No |
|pinned|boolean|No |
|powerUps|array|No |
|perfs|not defined|No |
|shortLink|cadena|No |
|shortUrl|cadena|No |
|starred|cadena|No |
|subscribed|cadena|No |
|url|cadena|No |



### Etiqueta


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|green|cadena|No |
|yellow|cadena|No |
|orange|cadena|No |
|red|cadena|No |
|purple|cadena|No |
|blue|cadena|No |
|sky|cadena|No |
|lime|cadena|No |
|pink|cadena|No |
|black|cadena|No |



### Pertenencia


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|id|cadena|No |
|idMember|cadena|No |
|memberType|cadena|No |
|unconfirmed|boolean|No |



### Rendimientos


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|permissionLevel|cadena|No |
|voting|cadena|No |
|comentarios|cadena|No |
|invitations|cadena|No |
|selfJoin|boolean|No |
|cardCovers|boolean|No |
|calendarFeedEnabled|boolean|No |
|background|cadena|No |
|backgroundColor|cadena|No |
|backgroundImage|cadena|No |
|backgroundImageScaled|cadena|No |
|backgroundTile|boolean|No |
|backgroundBrightness|cadena|No |
|canBePublic|boolean|No |
|canBeOrg|boolean|No |
|canBePrivate|boolean|No |
|canInvite|boolean|No |



### Enumerar


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|id|cadena|No |
|name|cadena|No |
|closed|boolean|No |
|idBoard|cadena|No |
|pos|número|No |
|subscribed|boolean|No |
|cards|array|No |
|board|not defined|No |


## Pasos siguientes
[Creación de una aplicación lógica](../app-service-logic/app-service-logic-create-a-logic-app.md)

<!---HONumber=AcomDC_0727_2016-->