---
title: MailChimp | Microsoft Docs
description: Cree aplicaciones lógicas con el Servicio de aplicaciones de Azure. MailChimp es un servicio SaaS que permite a las empresas a administrar y automatizar actividades de marketing por correo electrónico, incluido el envío de correos electrónicos de marketing, mensajes automatizados y campañas de destino.
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: ''
tags: connectors

ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/18/2016
ms.author: deonhe

---
# Get started with the MailChimp connector (Introducción al conector MailChimp)
MailChimp es un servicio SaaS que permite a las empresas a administrar y automatizar actividades de marketing por correo electrónico, incluido el envío de correos electrónicos de marketing, mensajes automatizados y campañas de destino.

> [!NOTE]
> Esta versión del artículo se aplica a la versión de esquema 2015-08-01-preview de las aplicaciones lógicas.
> 
> 

Puede empezar creando una aplicación lógica ahora. Para ello, consulte [Creación de una nueva aplicación lógica mediante la conexión de servicios de SaaS](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Desencadenadores y acciones
El conector MailChimp se puede usar como acción; tiene desencadenadores. Todos los conectores admiten datos en formato JSON y XML.

 El conector MailChimp tiene disponibles las siguientes acciones o desencadenadores:

### Acciones de MailChimp
Puede realizar estas acciones:

| Acción | Description |
| --- | --- |
| [newcampaign](connectors-create-api-mailchimp.md#newcampaign) |Creación de una nueva campaña basada en un tipo de campaña, lista de destinatarios y configuración de la campaña (línea de asunto, título, from\_name and reply\_to) |
| [newlist](connectors-create-api-mailchimp.md#newlist) |Creación de una nueva lista en su cuenta de MailChimp |
| [addmember](connectors-create-api-mailchimp.md#addmember) |Adición o actualización de un miembro de la lista |
| [removemember](connectors-create-api-mailchimp.md#removemember) |Eliminación de un miembro de una lista |
| [updatemember](connectors-create-api-mailchimp.md#updatemember) |Actualización de información de un miembro específico de la lista |

### Desencadenadores de MailChimp
Se pueden escuchar estos eventos:

| Desencadenador | Description |
| --- | --- |
| Cuando se ha agregado un miembro a una lista |Desencadena un flujo de trabajo cuando se ha agregado un nuevo miembro a una lista |
| Cuando se crea una nueva lista |Desencadena un flujo de trabajo cuando se crea una nueva lista |

## Creación de una conexión a MailChimp
Para crear aplicaciones lógicas con MailChimp, primero debe crear una **conexión** y, después, especificar los detalles de las siguientes propiedades:

| Propiedad | Obligatorio | Description |
| --- | --- | --- |
| SWT |Sí |Proporcionar las credenciales de MailChimp |

> [!INCLUDE [Pasos para crear una conexión a MailChimp](../../includes/connectors-create-api-mailchimp.md)]
> 
> [!TIP]
> Puede usar esta conexión en otras aplicaciones lógicas.
> 
> 

## Referencia para MailChimp
Se aplica a la versión: 1.0

## newcampaign
Nueva campaña: creación de una nueva campaña basada en un tipo de campaña, lista de destinatarios y configuración de la campaña (línea de asunto, título, from\_name and reply\_to)

```POST: /campaigns```

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Description |
| --- | --- | --- | --- | --- | --- |
| newCampaignRequest | |yes |body |Ninguna |Objeto JSON que se va a enviar en el cuerpo con los nuevos parámetros de solicitud de la campaña |

#### Response
| Nombre | Descripción |
| --- | --- |
| 200 |OK |
| 400 |Bad Request |
| 401 |No autorizado |
| 403 |Prohibido |
| 404 |No encontrado |
| 500 |Error interno del servidor. Error desconocido |
| default |Error en la operación. |

## newlist
Nueva lista: creación de una nueva lista en su cuenta de MailChimp

```POST: /lists```

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Description |
| --- | --- | --- | --- | --- | --- |
| newListRequest | |yes |body |Ninguna |Objeto JSON que se va a enviar en el cuerpo con los nuevos parámetros de solicitud de la campaña |

#### Response
| Nombre | Descripción |
| --- | --- |
| 200 |OK |
| 400 |Bad Request |
| 401 |No autorizado |
| 403 |Prohibido |
| 404 |No encontrado |
| 500 |Error interno del servidor. Error desconocido |
| default |Error en la operación. |

## addmember
Agregar miembro a lista: adición o actualización de un miembro de la lista

```POST: /lists/{list_id}/members```

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Description |
| --- | --- | --- | --- | --- | --- |
| list\_id |string |yes |path |Ninguna |El identificador único de la lista |
| newMemberInList | |yes |body |Ninguna |Objeto JSON que se va a enviar en el cuerpo con la información del nuevo miembro |

#### Response
| Nombre | Descripción |
| --- | --- |
| 200 |OK |
| 400 |Bad Request |
| 401 |No autorizado |
| 403 |Prohibido |
| 404 |No encontrado |
| 500 |Error interno del servidor. Error desconocido |
| default |Error en la operación. |

## removemember
Quitar miembro de la lista: eliminación de un miembro de una lista.

```DELETE: /lists/replacemailwithhash/{list_id}/members/{member_email}```

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Description |
| --- | --- | --- | --- | --- | --- |
| list\_id |string |yes |path |Ninguna |El identificador único de la lista |
| member\_email |string |yes |path |Ninguna |La dirección de correo electrónico del miembro que se va a eliminar |

#### Response
| Nombre | Descripción |
| --- | --- |
| 200 |OK |
| 400 |Bad Request |
| 401 |No autorizado |
| 403 |Prohibido |
| 404 |No encontrado |
| 500 |Error interno del servidor. Error desconocido |
| default |Error en la operación. |

## updatemember
Actualizar información del miembro: actualización de información de un miembro específico de la lista

```PATCH: /lists/replacemailwithhash/{list_id}/members/{member_email}```

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Description |
| --- | --- | --- | --- | --- | --- |
| list\_id |string |yes |path |Ninguna |El identificador único de la lista |
| member\_email |string |yes |path |Ninguna |La dirección de correo electrónico única del miembro que se va a actualizar |
| updateMemberInListRequest | |yes |body |Ninguna |Objeto JSON que se va a enviar en el cuerpo con la información actualizada del miembro |

#### Response
| Nombre | Descripción |
| --- | --- |
| 200 |OK |
| 400 |Bad Request |
| 401 |No autorizado |
| 403 |Prohibido |
| 404 |No encontrado |
| 500 |Error interno del servidor. Error desconocido |
| default |Error en la operación. |

## OnMemberSubscribed
Cuando se ha agregado un miembro a una lista: desencadena un flujo de trabajo cuando se ha agregado un nuevo miembro a una lista

```GET: /trigger/lists/{list_id}/members```

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Description |
| --- | --- | --- | --- | --- | --- |
| list\_id |string |yes |path |Ninguna |El identificador único de la lista |

#### Response
| Nombre | Descripción |
| --- | --- |
| 200 |OK |
| 202 |Accepted |
| 400 |Bad Request |
| 401 |No autorizado |
| 403 |Prohibido |
| 404 |No encontrado |
| 500 |Error interno del servidor. Error desconocido |
| default |Error en la operación. |

## OnCreateList
Cuando se crea una nueva lista: desencadena un flujo de trabajo cuando se crea una nueva lista

```GET: /trigger/lists```

No hay parámetros para esta llamada

#### Response
| Nombre | Descripción |
| --- | --- |
| 200 |OK |
| 202 |Accepted |
| 400 |Bad Request |
| 401 |No autorizado |
| 403 |Prohibido |
| 404 |No encontrado |
| 500 |Error interno del servidor. Error desconocido |
| default |Error en la operación. |

## Definiciones de objeto
### NewCampaignRequest
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| type |string |Sí |
| recipients |not defined |Sí |
| configuración |not defined |Sí |
| variate\_settings |not defined |No |
| tracking |not defined |No |
| rss\_opts |not defined |No |
| social\_card |not defined |No |

### Recipient
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| list\_id |string |Sí |
| segment\_opts |not defined |No |

### Settings
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| subject\_line |string |Sí |
| título |string |No |
| from\_name |string |Sí |
| reply\_to |string |Sí |
| use\_conversation |boolean |No |
| to\_name |string |No |
| folder\_id |integer |No |
| authenticate |boolean |No |
| auto\_footer |boolean |No |
| inline\_css |boolean |No |
| auto\_tweet |boolean |No |
| auto\_fb\_post |array |No |
| fb\_comments |boolean |No |

### Variate\_Settings
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| winner\_criteria |string |No |
| wait\_time |integer |No |
| test\_size |integer |No |
| subject\_lines |array |No |
| send\_times |array |No |
| from\_names |array |No |
| reply\_to\_addresses |array |No |

### Seguimiento
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| opens |boolean |No |
| html\_clicks |boolean |No |
| text\_clicks |boolean |No |
| goal\_tracking |boolean |No |
| ecomm360 |boolean |No |
| google\_analytics |string |No |
| clicktale |string |No |
| salesforce |not defined |No |
| highrise |not defined |No |
| capsule |not defined |No |

### RSS\_Opts
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| feed\_url |string |No |
| frequency |string |No |
| constrain\_rss\_img |string |No |
| schedule |not defined |No |

### Social\_Card
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| image\_url |string |No |
| description |string |No |
| título |string |No |

### Segment\_Opts
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| saved\_segment\_id |integer |No |
| match |string |No |

### Salesforce
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| campaign |boolean |No |
| HDInsight |boolean |No |

### Highrise
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| campaign |boolean |No |
| HDInsight |boolean |No |

### Capsule
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| HDInsight |boolean |No |

### Schedule
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| hour |integer |No |
| daily\_send |not defined |No |
| weekly\_send\_day |string |No |
| monthly\_send\_date |número |No |

### Daily\_Send
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| sunday |boolean |No |
| monday |boolean |No |
| tuesday |boolean |No |
| wednesday |boolean |No |
| thursday |boolean |No |
| friday |boolean |No |
| saturday |boolean |No |

### CampaignResponseModel
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| id |string |No |
| type |string |No |
| create\_time |string |No |
| archive\_url |string |No |
| status |string |No |
| emails\_sent |integer |No |
| send\_time |string |No |
| content\_type |string |No |
| recipient |array |No |
| configuración |not defined |No |
| variate\_settings |not defined |No |
| tracking |not defined |No |
| rss\_opts |not defined |No |
| ab\_split\_opts |not defined |No |
| social\_card |not defined |No |
| report\_summary |not defined |No |
| delivery\_status |not defined |No |
| \_links |array |No |

### AB\_Split\_Opts
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| split\_test |string |No |
| pick\_winner |string |No |
| wait\_units |string |No |
| wait\_time |integer |No |
| split\_size |integer |No |
| from\_name\_a |string |No |
| from\_name\_b |string |No |
| reply\_email\_a |string |No |
| reply\_email\_b |string |No |
| subject\_a |string |No |
| subject\_b |string |No |
| send\_time\_a |string |No |
| send\_time\_b |string |No |
| send\_time\_winner |string |No |

### Report\_Summary
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| opens |integer |No |
| unique\_opens |integer |No |
| open\_rate |número |No |
| clicks |integer |No |
| subscriber\_clicks |número |No |
| click\_rate |número |No |

### Delivery\_Status
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| enabled |boolean |No |
| can\_cancel |boolean |No |
| status |string |No |
| emails\_sent |integer |No |
| emails\_canceled |integer |No |

### Vínculo
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| rel |string |No |
| href |string |No |
| estático |string |No |
| targetSchema |string |No |
| schema |string |No |

### NewListRequest
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| name |string |Sí |
| contact |not defined |Sí |
| permission\_reminder |string |Sí |
| use\_archive\_bar |boolean |No |
| campaign\_defaults |not defined |Sí |
| notify\_on\_subscribe |string |No |
| notify\_on\_unsubscribe |string |No |
| email\_type\_option |boolean |Sí |
| visibility |string |No |

### Contacto
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| company |string |Sí |
| address1 |string |Sí |
| address2 |string |No |
| city |string |Sí |
| state |string |Sí |
| zip |string |Sí |
| country |string |Sí |
| phone |string |Sí |

### Campaign\_Defaults
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| from\_name |string |Sí |
| from\_email |string |Sí |
| subject |string |No |
| language |string |Sí |

### CreateNewListResponseModel
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| id |string |Sí |
| name |string |Sí |
| contact |not defined |Sí |
| permission\_reminder |string |Sí |
| use\_archive\_bar |boolean |No |
| campaign\_defaults |not defined |Sí |
| notify\_on\_subscribe |string |No |
| notify\_on\_unsubscribe |string |No |
| date\_created |string |No |
| list\_rating |integer |No |
| email\_type\_option |boolean |Sí |
| subscribe\_url\_short |string |No |
| subscribe\_url\_long |string |No |
| beamer\_address |string |No |
| visibility |string |No |
| modules |array |No |
| stats |not defined |No |
| \_links |array |No |

### Estadísticas
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| member\_count |integer |No |
| unsubscribe\_count |integer |No |
| cleaned\_count |integer |No |
| member\_count\_since\_send |integer |No |
| unsubscribe\_count\_since\_send |integer |No |
| cleaned\_count\_since\_send |integer |No |
| campaign\_count |integer |No |
| campaign\_last\_sent |integer |No |
| merge\_field\_count |integer |No |
| avg\_sub\_rate |número |No |
| avg\_unsub\_rate |número |No |
| target\_sub\_rate |número |No |
| open\_rate |número |No |
| click\_rate |número |No |
| last\_sub\_date |string |No |
| last\_unsub\_date |string |No |

### GetListsResponseModel
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| lists |array |No |
| total\_items |integer |No |

### NewMemberInListRequest
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| email\_type |string |No |
| status |string |Sí |
| merge\_fields |not defined |No |
| interests |string |No |
| language |string |No |
| vip |boolean |No |
| location |not defined |No |
| email\_address |string |Sí |

### FirstAndLastName
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| FNAME |string |No |
| LNAME |string |No |

### Ubicación
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| latitude |número |No |
| longitude |número |No |

### MemberResponseModel
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| id |string |No |
| email\_address |string |No |
| unique\_email\_id |string |No |
| email\_type |string |No |
| status |string |No |
| merge\_fields |not defined |No |
| interests |string |No |
| stats |not defined |No |
| ip\_signup |string |No |
| timestamp\_signup |string |No |
| ip\_opt |string |No |
| timestamp\_opt |string |No |
| member\_rating |integer |No |
| last\_changed |string |No |
| language |string |No |
| vip |boolean |No |
| email\_client |string |No |
| location |not defined |No |
| last\_note |not defined |No |
| list\_id |string |No |
| \_links |array |No |

### Last\_Note
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| note\_id |integer |No |
| created\_at |string |No |
| created\_by |string |No |
| note |string |No |

### GetAllMembersResponseModel
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| members |array |No |
| list\_id |string |No |
| total\_items |integer |No |

### Objeto
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
|  | | |

### UpdateMemberInListRequest
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| email\_address |string |No |
| email\_type |string |No |
| status |string |Sí |
| merge\_fields |not defined |No |
| interests |string |No |
| language |string |No |
| vip |boolean |No |
| location |not defined |No |

### GetMembersResponseModel
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| members |array |No |
| list\_id |string |No |
| total\_items |integer |No |

### AddUserResponseModel
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| id |string |Sí |
| email\_address |string |Sí |
| unique\_email\_id |string |No |
| email\_type |string |No |
| status |string |No |
| merge\_fields |not defined |Sí |
| interests |string |No |
| stats |not defined |No |
| ip\_signup |string |No |
| timestamp\_signup |string |No |
| ip\_opt |string |No |
| timestamp\_opt |string |No |
| member\_rating |integer |No |
| last\_changed |string |No |
| language |string |No |
| vip |boolean |No |
| email\_client |string |No |
| location |not defined |No |
| last\_note |not defined |No |
| list\_id |string |No |
| \_links |array |No |

## Pasos siguientes
[Creación de una aplicación lógica](../app-service-logic/app-service-logic-create-a-logic-app.md)

<!---HONumber=AcomDC_0824_2016-->