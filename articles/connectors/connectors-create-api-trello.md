---
title: Trello | Microsoft Docs
description: "Cree aplicaciones lógicas con el Servicio de aplicaciones de Azure. Trello le ofrece perspectiva sobre todos los proyectos, en el trabajo y en casa.  Es una manera fácil, gratis, flexible y visual de administrar los proyectos y organizar todo.  Conéctese a Trello para administrar los paneles, listas y tarjetas"
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: fe7a4377-5c24-4f72-ab1a-6d9d23e8d895
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/18/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: d7a8111e299e846eb384f97bdef5a8c214214578


---
# <a name="get-started-with-the-trello-connector"></a>Introducción al conector Trello
Trello le ofrece perspectiva sobre todos los proyectos, en el trabajo y en casa.  Es una manera fácil, gratis, flexible y visual de administrar los proyectos y organizar todo.  Conéctese a Trello para administrar los paneles, listas y tarjetas.

> [!NOTE]
> Esta versión del artículo se aplica a la versión de esquema 2015-08-01-preview de aplicaciones lógicas.
> 
> 

Puede empezar creando una aplicación lógica ahora. Para ello, consulte [Creación de una nueva aplicación lógica mediante la conexión de servicios de SaaS](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Desencadenadores y acciones
El conector Trello se puede usar como acción; tiene desencadenadores. Todos los conectores admiten datos en formato JSON y XML.

 El conector Trello tiene las siguientes acciones y desencadenadores disponibles:

### <a name="trello-actions"></a>Acciones de Trello
Puede realizar estas acciones:

| Acción | Descripción |
| --- | --- |
| [ListCards](connectors-create-api-trello.md#listcards) |Enumera las tarjetas en el panel |
| [GetCard](connectors-create-api-trello.md#getcard) |Obtiene las tarjetas según el identificador |
| [UpdateCard](connectors-create-api-trello.md#updatecard) |Actualiza las tarjetas |
| [DeleteCard](connectors-create-api-trello.md#deletecard) |Elimina las tarjetas |
| [CreateCard](connectors-create-api-trello.md#createcard) |Crea una nueva tarjeta en la cuenta de Trello |
| [ListBoards](connectors-create-api-trello.md#listboards) |Enumera los paneles |
| [GetBoard](connectors-create-api-trello.md#getboard) |Obtiene paneles según el identificador |
| [ListLists](connectors-create-api-trello.md#listlists) |Enumera listas de tarjetas en el panel |
| [GetList](connectors-create-api-trello.md#getlist) |Obtiene listas según el identificador |

### <a name="trello-triggers"></a>Desencadenadores de Trello
Se pueden escuchar estos eventos:

| Desencadenador | Description |
| --- | --- |
| Cuando se agrega una nueva tarjeta a un panel |Se desencadena un flujo cuando se agrega una nueva tarjeta a un panel |
| Cuando se agrega una nueva tarjeta a una lista |Se desencadena un flujo cuando se agrega una nueva tarjeta a una lista |

## <a name="create-a-connection-to-trello"></a>Creación de una conexión a Trello
Para crear aplicaciones lógicas con Trello, primero necesita crear una **conexión** y, después, especificar los detalles de las siguientes propiedades:

| Propiedad | Obligatorio | Descripción |
| --- | --- | --- |
| Se necesita el cifrado de tokens |Sí |Proporciona las credenciales de Trello |

Después de crear la conexión, puede usarla para ejecutar las acciones y escuchar los desencadenadores descritos en este artículo.

> [!INCLUDE [Steps to create a connection to Trello](../../includes/connectors-create-api-trello.md)]
> 
> [!TIP]
> Puede usar esta conexión en otras aplicaciones lógicas.
> 
> 

## <a name="reference-for-trello"></a>Referencia para Trello
Se aplica a la versión: 1.0

## <a name="onnewcardinboard"></a>OnNewCardInBoard
Cuando se agrega una nueva tarjeta a un panel: desencadena un flujo cuando se agrega una nueva tarjeta a un panel

```GET: /trigger/boards/{board_id}/cards```

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Description |
| --- | --- | --- | --- | --- | --- |
| board_id |string |Sí |path |Ninguna |Identificador único del panel en el que capturar las tarjetas |

#### <a name="response"></a>Response
| Nombre | Descripción |
| --- | --- |
| 200 |OK |
| 400 |Bad Request |
| 401 |No autorizado |
| 403 |Prohibido |
| 404 |No encontrado |
| 500 |Error interno del servidor. Error desconocido |
| default |Error en la operación |

## <a name="onnewcardinlist"></a>OnNewCardInList
Cuando se agrega una nueva tarjeta a una lista: desencadena un flujo cuando se agrega una nueva tarjeta a una lista

```GET: /trigger/lists/{list_id}/cards```

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Description |
| --- | --- | --- | --- | --- | --- |
| board_id |string |yes |query |Ninguna |Identificador único del panel en el que capturar las tarjetas |
| list_id |string |Sí |path |Ninguna |Identificador único de la lista en la que capturar las tarjetas |

#### <a name="response"></a>Response
| Nombre | Descripción |
| --- | --- |
| 200 |OK |
| 400 |Bad Request |
| 401 |No autorizado |
| 403 |Prohibido |
| 404 |No encontrado |
| 500 |Error interno del servidor. Error desconocido |
| default |Error en la operación |

## <a name="listcards"></a>ListCards
Enumera las tarjetas en el panel: enumera las tarjetas en el panel

```GET: /boards/{board_id}/cards```

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Description |
| --- | --- | --- | --- | --- | --- |
| board_id |string |Sí |path |Ninguna |Identificador del panel en el que capturar todas las tarjetas |
| actions |string |no |query |Ninguna |Enumera las acciones a devolver. Especifica 'all' o una lista de valores válidos separados por comas |
| attachments |boolean |no |query |Ninguna |Muestra los datos adjuntos |
| attachment_fields |string |no |query |Ninguna |Enumera los campos de datos adjuntos a devolver. Especifica 'all' o una lista de valores válidos separados por comas |
| stickers |boolean |no |query |Ninguna |Muestra los adhesivos |
| members |boolean |no |query |Ninguna |Muestra los miembros |
| member_fields |string |no |query |Ninguna |Enumera los campos de los miembros a devolver. Especifica 'all' o una lista de valores válidos separados por comas |
| CheckItemStates |boolean |no |query |Ninguna |Devuelve los estados de la tarjeta |
| Checklists |string |no |query |Ninguna |Muestra las listas de comprobación |
| limit |integer |no |query |Ninguna |El número máximo de resultados que se devolverán, entre 1 y 1000 |
| since |string |no |query |Ninguna |Captura todas las tarjetas posteriores a esta fecha |
| antes de |string |no |query |Ninguna |Captura todas las tarjetas anteriores a esta fecha |
| filter |cadena |no |query |Ninguna |Filtra la respuesta |
| fields |cadena |no |query |Ninguna |Enumera los campos de las tarjetas a devolver. Especifica 'all' o una lista de valores válidos separados por comas |

#### <a name="response"></a>Response
| Nombre | Descripción |
| --- | --- |
| 200 |OK |
| 400 |Bad Request |
| 401 |No autorizado |
| 403 |Prohibido |
| 404 |No encontrado |
| 500 |Error interno del servidor. Error desconocido |
| default |Error en la operación |

## <a name="getcard"></a>GetCard
Obtiene la tarjeta según el identificador: obtiene la tarjeta según el identificador

```GET: /cards/{card_id}```

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Description |
| --- | --- | --- | --- | --- | --- |
| board_id |string |yes |query |Ninguna |Identificador del panel en el que capturar las tarjetas |
| card_id |string |Sí |path |Ninguna |Identificador de la tarjeta a capturar |
| actions |string |no |query |Ninguna |Enumera las acciones a devolver. Especifica 'all' o una lista de valores válidos separados por comas |
| actions_entities |boolean |no |query |Ninguna |Devuelve entidades de acción |
| actions_display |boolean |no |query |Ninguna |Devuelve presentaciones de acciones |
| actions_limit |integer |no |query |Ninguna |Número máximo de acciones a devolver |
| action_fields |string |no |query |Ninguna |Lista de campos de acción a devolver para cada acción. Especifica 'all' o una lista de valores válidos separados por comas |
| action_memberCreator_fields |string |no |query |Ninguna |Lista de campos de creador de miembros de acciones a devolver. Especifica 'all' o una lista de valores válidos separados por comas |
| attachments |boolean |no |query |Ninguna |Devuelve datos adjuntos |
| attachement_fields |string |no |query |Ninguna |Lista de campos de datos adjuntos a devolver para cada archivo adjunto. Especifica 'all' o una lista de valores válidos separados por comas |
| members |boolean |no |query |Ninguna |Devuelve miembros |
| member_fields |string |no |query |Ninguna |Lista de campos de miembros a devolver para cada miembro. Especifica 'all' o una lista de valores válidos separados por comas |
| membersVoted |boolean |no |query |Ninguna |Devuelve miembros votados |
| memberVoted_fields |string |no |query |Ninguna |Lista de campos de miembros votados a devolver para cada miembro votado. Especifica 'all' o una lista de valores válidos separados por comas |
| checkItemStates |boolean |no |query |Ninguna |Devuelve estados de la tarjeta |
| checkItemState_fields |string |no |query |Ninguna |Lista de campos de estado que se van a devolver para cada estado del artículo de la tarjeta. Especifica 'all' o una lista de valores válidos separados por comas |
| checklists |string |no |query |Ninguna |Devuelve listas de comprobación |
| checklist_fields |string |no |query |Ninguna |Lista de campos de la lista de comprobación a devolver para cada lista de comprobación. Especifica 'all' o una lista de valores válidos separados por comas |
| board |boolean |no |query |Ninguna |Devuelve el panel al que pertenece la tarjeta |
| board_fields |string |no |query |Ninguna |Enumera los campos de los paneles a devolver. Especifica 'all' o una lista de valores válidos separados por comas |
| list |boolean |no |query |Ninguna |Devuelve la lista a la que pertenece la tarjeta |
| list_fields |string |no |query |Ninguna |Enumera los campos de la lista a devolver. Especifica 'all' o una lista de valores válidos separados por comas |
| stickers |boolean |no |query |Ninguna |Devuelve adhesivos |
| sticker_fields |string |no |query |Ninguna |Lista de los campos de adhesivo a devolver para cada adhesivo. Especifica 'all' o una lista de valores válidos separados por comas |
| fields |cadena |no |query |Ninguna |Enumera los campos de las tarjetas a devolver. Especifica 'all' o una lista de valores válidos separados por comas |

#### <a name="response"></a>Response
| Nombre | Descripción |
| --- | --- |
| 200 |OK |
| 400 |Bad Request |
| 401 |No autorizado |
| 403 |Prohibido |
| 404 |No encontrado |
| 500 |Error interno del servidor. Error desconocido |
| default |Error en la operación |

## <a name="updatecard"></a>UpdateCard
Tarjeta de actualización: tarjeta de actualización

```PUT: /cards/{card_id}```

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Description |
| --- | --- | --- | --- | --- | --- |
| board_id |string |yes |query |Ninguna |Identificador del panel desde el que capturar las tarjetas |
| card_id |string |Sí |path |Ninguna |Identificador de la tarjeta a actualizar |
| updateCard | |yes |body |Ninguna |Valores actualizados de tarjeta |

#### <a name="response"></a>Response
| Nombre | Descripción |
| --- | --- |
| 200 |OK |
| 400 |Bad Request |
| 401 |No autorizado |
| 403 |Prohibido |
| 404 |No encontrado |
| 500 |Error interno del servidor. Error desconocido |
| default |Error en la operación |

## <a name="deletecard"></a>DeleteCard
Elimina la tarjeta: elimina la tarjeta

```DELETE: /cards/{card_id}```

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Description |
| --- | --- | --- | --- | --- | --- |
| board_id |string |yes |query |Ninguna |Identificador del panel desde el que capturar las tarjetas |
| card_id |string |Sí |path |Ninguna |Identificador de la tarjeta a eliminar |

#### <a name="response"></a>Response
| Nombre | Descripción |
| --- | --- |
| 200 |OK |
| 400 |Bad Request |
| 401 |No autorizado |
| 403 |Prohibido |
| 404 |No encontrado |
| 500 |Error interno del servidor. Error desconocido |
| default |Error en la operación |

## <a name="createcard"></a>CreateCard
Crea tarjetas: crea una nueva tarjeta en la cuenta de Trello

```POST: /cards```

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Description |
| --- | --- | --- | --- | --- | --- |
| board_id |string |yes |query |Ninguna |Identificador único del panel en el que crear las tarjetas |
| newCard | |yes |body |Ninguna |Nueva tarjeta para agregar al panel de Trello |

#### <a name="response"></a>Response
| Nombre | Descripción |
| --- | --- |
| 200 |OK |
| 400 |Bad Request |
| 401 |No autorizado |
| 403 |Prohibido |
| 404 |No encontrado |
| 500 |Error interno del servidor. Error desconocido |
| default |Error en la operación |

## <a name="listboards"></a>ListBoards
Enumera los paneles: enumera los paneles

```GET: /member/me/boards```

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Description |
| --- | --- | --- | --- | --- | --- |
| filter |cadena |no |query |Ninguna |Enumera los filtros a aplicar a los resultados del panel. Especifica 'all' o una lista de valores válidos separados por comas |
| fields |cadena |no |query |Ninguna |Enumera los campos de los paneles a devolver. Especifica 'all' o una lista de valores válidos separados por comas |
| actions |string |no |query |Ninguna |Enumera los campos de acción a devolver. Especifica 'all' o una lista de valores válidos separados por comas |
| actions_entities |boolean |no |query |Ninguna |Devuelve entidades de acción |
| actions_limit |integer |no |query |Ninguna |Número máximo de acciones a devolver |
| actions_format |string |no |query |Ninguna |Especifica el formato de las acciones a devolver |
| actions_since |string |no |query |Ninguna |Devuelve las acciones posteriores a la fecha especificada |
| action_fields |string |no |query |Ninguna |Enumera los campos de la acción a devolver. Especifica 'all' o una lista de valores válidos separados por comas |
| memberships |string |no |query |Ninguna |Especifica la información de pertenencia a devolver. Especifica 'all' o una lista de valores válidos separados por comas |
| organization |boolean |no |query |Ninguna |Especifica la información de la organización a devolver |
| organization_fields |string |no |query |Ninguna |Enumera los campos de la organización a devolver. Especifica 'all' o una lista de valores válidos separados por comas |
| lists |string |no |query |Ninguna |Especifica si desea devolver listas que pertenecen al panel |

#### <a name="response"></a>Response
| Nombre | Descripción |
| --- | --- |
| 200 |OK |
| 400 |Bad Request |
| 401 |No autorizado |
| 403 |Prohibido |
| 404 |No encontrado |
| 500 |Error interno del servidor. Error desconocido |
| default |Error en la operación |

## <a name="getboard"></a>GetBoard
Obtiene el panel según el identificador: obtiene el panel según el identificador.

```GET: /boards/{board_id}```

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Description |
| --- | --- | --- | --- | --- | --- |
| board_id |string |Sí |path |Ninguna |Identificador único del panel a obtener |
| actions |string |no |query |Ninguna |Enumera las acciones a devolver. Especifica 'all' o una lista de valores válidos separados por comas |
| action_entities |boolean |no |query |Ninguna |Especifica si desea devolver entidades de acción |
| actions_display |boolean |no |query |Ninguna |Especifica si desea devolver presentaciones de acciones |
| actions_format |string |no |query |Ninguna |Especifica el formato de las acciones a devolver |
| actions_since |string |no |query |Ninguna |Solo se devuelven las acciones posteriores a esta fecha |
| actions_limit |integer |no |query |Ninguna |Número máximo de acciones a devolver |
| action_fields |string |no |query |Ninguna |Enumera los campos a devolver con cada campo. Especifica 'all' o una lista de valores válidos separados por comas |
| action_member |boolean |no |query |Ninguna |Especifica si desea devolver miembros de acción |
| action_member_fields |string |no |query |Ninguna |Enumera los campos de miembros a devolver con cada miembro de la acción. Especifica 'all' o una lista de valores válidos separados por comas |
| action_memberCreator |boolean |no |query |Ninguna |Especifica si desea devolver un creador de miembros de acciones |
| action_memberCreator_fields |string |no |query |Ninguna |Enumera los campos de creador de miembros de acciones a devolver. Especifica 'all' o una lista de valores válidos separados por comas |
| cards |string |no |query |Ninguna |Especifica las tarjetas a devolver |
| card_fields |string |no |query |Ninguna |Enumera los campos a devolver para cada tarjeta. Especifica 'all' o una lista de valores válidos separados por comas |
| card_attachments |boolean |yes |query |Ninguna |Especifica si desea devolver los datos adjuntos de tarjetas |
| card_attachment_fields |string |no |query |Ninguna |Enumera los campos de datos adjuntos a devolver para cada archivo adjunto. Especifica 'all' o una lista de valores válidos separados por comas |
| card_checklists |string |no |query |Ninguna |Especifica las listas de comprobación a devolver para cada tarjeta |
| card_stickers |boolean |no |query |Ninguna |Especifica si desea devolver adhesivos de tarjetas |
| boardStarts |string |no |query |Ninguna |Especifica las estrellas del panel a devolver |
| etiquetas |string |no |query |Ninguna |Especifica las etiquetas a devolver |
| label_fields |string |no |query |Ninguna |Enumera los campos de etiqueta a devolver para cada etiqueta. Especifica 'all' o una lista de valores válidos separados por comas |
| labels_limits |integer |no |query |Ninguna |Número máximo de etiquetas a devolver |
| lists |string |no |query |Ninguna |Especifica las listas a devolver |
| list_fields |string |no |query |Ninguna |Enumera los campos de lista a devolver para cada lista. Especifica 'all' o una lista de valores válidos separados por comas |
| memberships |string |no |query |Ninguna |Enumera las pertenencias a devolver. Especifica 'all' o una lista de valores válidos separados por comas |
| memberships_member |boolean |no |query |Ninguna |Especifica si desea devolver miembros de pertenencia |
| memberships_member_fields |string |no |query |Ninguna |Enumera los campos de miembros a devolver para cada miembro de pertenencia. Especifica 'all' o una lista de valores válidos separados por comas |
| members |string |no |query |Ninguna |Enumera los miembros a devolver. |
| member_fields |string |no |query |Ninguna |Enumera los campos de miembros a devolver para cada miembro. Especifica 'all' o una lista de valores válidos separados por comas |
| membersInvited |string |no |query |Ninguna |Especifica los miembros invitados a devolver |
| membersInvited_fields |string |no |query |Ninguna |Enumera los campos a devolver para cada miembro invitado. Especifica 'all' o una lista de valores válidos separados por comas |
| checklists |string |no |query |Ninguna |Especifica las listas de comprobación a devolver |
| checklist_fields |string |no |query |Ninguna |Enumera los campos de la lista de comprobación a devolver para cada lista de comprobación. Especifica 'all' o una lista de valores válidos separados por comas |
| organization |boolean |no |query |Ninguna |Especifica si desea devolver la información de la organización |
| organization_fields |string |no |query |Ninguna |Enumera los campos de la organización a devolver para cada organización. Especifica 'all' o una lista de valores válidos separados por comas |
| organization_memberships |string |no |query |Ninguna |Enumera las pertenencias de la organización a devolver. Especifica 'all' o una lista de valores válidos separados por comas |
| myPerfs |boolean |no |query |Ninguna |Especifica si desea devolver mis rendimientos |
| fields |cadena |no |query |Ninguna |Enumera los campos a devolver. Especifica 'all' o una lista de valores válidos separados por comas |

#### <a name="response"></a>Response
| Nombre | Descripción |
| --- | --- |
| 200 |OK |
| 400 |Bad Request |
| 401 |No autorizado |
| 403 |Prohibido |
| 404 |No encontrado |
| 500 |Error interno del servidor. Error desconocido |
| default |Error en la operación |

## <a name="listlists"></a>ListLists
Enumera las listas de tarjeta en el panel: enumera las listas de tarjeta en el panel

```GET: /boards/{board_id}/lists```

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Description |
| --- | --- | --- | --- | --- | --- |
| board_id |string |Sí |path |Ninguna |Identificador único del panel en el que capturar las listas |
| cards |string |no |query |Ninguna |Especifica las tarjetas a devolver |
| card_fields |string |no |query |Ninguna |Enumera los campos de las tarjetas desde los que devolver. Especifica 'all' o una lista de valores válidos separados por comas |
| filter |cadena |no |query |Ninguna |Especifica la propiedad de filtro para las listas |
| fields |cadena |no |query |Ninguna |Enumera los campos a devolver. Especifica 'all' o una lista de valores válidos separados por comas |

#### <a name="response"></a>Response
| Nombre | Descripción |
| --- | --- |
| 200 |OK |
| 400 |Bad Request |
| 401 |No autorizado |
| 403 |Prohibido |
| 404 |No encontrado |
| 500 |Error interno del servidor. Error desconocido |
| default |Error en la operación |

## <a name="getlist"></a>GetList
Obtiene listas según el identificador: obtiene listas según el identificador

```GET: /lists/{list_id}```

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Description |
| --- | --- | --- | --- | --- | --- |
| board_id |string |yes |query |Ninguna |Identificador único del panel desde el que capturar las listas |
| list_id |string |Sí |path |Ninguna |Identificador único de la lista a capturar |
| cards |string |no |query |Ninguna |Especifica las tarjetas a devolver |
| card_fields |string |no |query |Ninguna |Enumera los campos de tarjeta a devolver para cada tarjeta. Especifica 'all' o una lista de valores válidos separados por comas |
| board |boolean |no |query |Ninguna |Especifica si desea devolver información del panel |
| board_fields |string |no |query |Ninguna |Enumera los campos de los paneles a devolver. Especifica 'all' o una lista de valores válidos separados por comas |
| fields |cadena |no |query |Ninguna |Enumera los campos de la lista a devolver. Especifica 'all' o una lista de valores válidos separados por comas |

#### <a name="response"></a>Response
| Nombre | Descripción |
| --- | --- |
| 200 |OK |
| 400 |Bad Request |
| 401 |No autorizado |
| 403 |Prohibido |
| 404 |No encontrado |
| 500 |Error interno del servidor. Error desconocido |
| default |Error en la operación |

## <a name="object-definitions"></a>Definiciones de objeto
### <a name="card"></a>Card
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| id |string |No |
| checkItemStates |array |No |
| closed |boolean |No |
| dateLastActivity |string |No |
| desc |string |No |
| idBoard |string |No |
| idList |string |No |
| idMembersVoted |array |No |
| idShort |integer |No |
| idAttachmentCover |string |No |
| manualCoverAttachment |boolean |No |
| idLabels |array |No |
| name |string |No |
| pos |integer |No |
| shortLink |string |No |
| badges |not defined |No |
| due |string |No |
| email |string |No |
| shortUrl |string |No |
| subscribed |boolean |No |
| url |string |No |

### <a name="badges"></a>Notificaciones
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| Votes |integer |No |
| ViewingMemberVoted |boolean |No |
| Subscribed |boolean |No |
| Fogbugz |string |No |
| CheckItems |integer |No |
| CheckItemsChecked |integer |No |
| Comentarios |integer |No |
| Datos adjuntos |integer |No |
| Description |boolean |No |
| Due |string |No |

### <a name="object"></a>Objeto
### <a name="createcard"></a>CreateCard
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| idList |string |Sí |
| name |cadena |Sí |
| desc |string |No |
| pos |string |No |
| idMembers |string |No |
| idLabels |string |No |
| urlSource |string |No |
| fileSource |string |No |
| idCardSource |string |No |
| keepFromSource |string |No |

### <a name="updatecard"></a>UpdateCard
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| Nombre |string |No |
| desc |string |No |
| closed |boolean |No |
| idMembers |string |No |
| idAttachmentCover |string |No |
| idList |string |No |
| idBoard |string |No |
| pos |string |No |
| due |string |No |
| subscribed |boolean |No |

### <a name="board"></a>Board
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| id |string |No |
| closed |boolean |No |
| dateLastActivity |string |No |
| dateLastView |string |No |
| desc |string |No |
| idOrganization |string |No |
| invitations |array |No |
| invited |boolean |No |
| labelNames |not defined |No |
| memberships |array |No |
| name |string |No |
| pinned |boolean |No |
| powerUps |array |No |
| perfs |not defined |No |
| shortLink |string |No |
| shortUrl |string |No |
| starred |string |No |
| subscribed |string |No |
| url |string |No |

### <a name="label"></a>Etiqueta
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| green |string |No |
| yellow |string |No |
| orange |string |No |
| red |string |No |
| purple |string |No |
| blue |string |No |
| sky |string |No |
| lime |string |No |
| pink |string |No |
| black |string |No |

### <a name="membership"></a>Pertenencia
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| id |string |No |
| idMember |string |No |
| memberType |string |No |
| unconfirmed |boolean |No |

### <a name="perfs"></a>Rendimientos
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| permissionLevel |string |No |
| voting |string |No |
| comentarios |string |No |
| invitations |string |No |
| selfJoin |boolean |No |
| cardCovers |boolean |No |
| calendarFeedEnabled |boolean |No |
| background |string |No |
| backgroundColor |string |No |
| backgroundImage |string |No |
| backgroundImageScaled |string |No |
| backgroundTile |boolean |No |
| backgroundBrightness |string |No |
| canBePublic |boolean |No |
| canBeOrg |boolean |No |
| canBePrivate |boolean |No |
| canInvite |boolean |No |

### <a name="list"></a>Enumerar
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| id |string |No |
| name |string |No |
| closed |boolean |No |
| idBoard |string |No |
| pos |número |No |
| subscribed |boolean |No |
| cards |array |No |
| board |not defined |No |

## <a name="next-steps"></a>Pasos siguientes
[Creación de una aplicación lógica](../app-service-logic/app-service-logic-create-a-logic-app.md)




<!--HONumber=Nov16_HO3-->


