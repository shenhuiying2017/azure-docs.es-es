---
title: MailChimp | Microsoft Docs
description: "Cree aplicaciones lógicas con el Servicio de aplicaciones de Azure. MailChimp es un servicio SaaS que permite a las empresas a administrar y automatizar actividades de marketing por correo electrónico, incluido el envío de correos electrónicos de marketing, mensajes automatizados y campañas de destino."
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: 36559de2-94f0-4355-b492-2926dfc56486
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/18/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: b92f954680603891ced503a1134791312b5214f0
ms.openlocfilehash: cee2c702c705e1451c0a6d01b140e6291b1e5ce1


---
# <a name="get-started-with-the-mailchimp-connector"></a>Get started with the MailChimp connector (Introducción al conector MailChimp)
MailChimp es un servicio SaaS que permite a las empresas a administrar y automatizar actividades de marketing por correo electrónico, incluido el envío de correos electrónicos de marketing, mensajes automatizados y campañas de destino.

> [!NOTE]
> Esta versión del artículo se aplica a la versión de esquema 2015-08-01-preview de aplicaciones lógicas.
> 
> 

Puede empezar creando una aplicación lógica ahora. Para ello, consulte [Creación de una nueva aplicación lógica mediante la conexión de servicios de SaaS](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Desencadenadores y acciones
El conector MailChimp se puede usar como acción; tiene desencadenadores. Todos los conectores admiten datos en formato JSON y XML.

 El conector MailChimp tiene disponibles las siguientes acciones o desencadenadores:

### <a name="mailchimp-actions"></a>Acciones de MailChimp
Puede realizar estas acciones:

| Acción | Descripción |
| --- | --- |
| [newcampaign](connectors-create-api-mailchimp.md#newcampaign) |Creación de una nueva campaña basada en un tipo de campaña, lista de destinatarios y configuración de la campaña (línea de asunto, título, from_name and reply_to) |
| [newlist](connectors-create-api-mailchimp.md#newlist) |Creación de una nueva lista en su cuenta de MailChimp |
| [addmember](connectors-create-api-mailchimp.md#addmember) |Adición o actualización de un miembro de la lista |
| [removemember](connectors-create-api-mailchimp.md#removemember) |Eliminación de un miembro de una lista |
| [updatemember](connectors-create-api-mailchimp.md#updatemember) |Actualización de información de un miembro específico de la lista |

### <a name="mailchimp-triggers"></a>Desencadenadores de MailChimp
Se pueden escuchar estos eventos:

| Desencadenador | Description |
| --- | --- |
| Cuando se ha agregado un miembro a una lista |Desencadena un flujo de trabajo cuando se ha agregado un nuevo miembro a una lista |
| Cuando se crea una nueva lista |Desencadena un flujo de trabajo cuando se crea una nueva lista |

## <a name="create-a-connection-to-mailchimp"></a>Creación de una conexión a MailChimp
Para crear aplicaciones lógicas con MailChimp, primero debe crear una **conexión** y, después, especificar los detalles de las siguientes propiedades:

| Propiedad | Obligatorio | Descripción |
| --- | --- | --- |
| Se necesita el cifrado de tokens |Sí |Proporcionar las credenciales de MailChimp |

> [!INCLUDE [Steps to create a connection to MailChimp](../../includes/connectors-create-api-mailchimp.md)]
> 
> [!TIP]
> Puede usar esta conexión en otras aplicaciones lógicas.
> 
> 

## <a name="reference-for-mailchimp"></a>Referencia para MailChimp
Se aplica a la versión: 1.0

## <a name="newcampaign"></a>newcampaign
Nueva campaña: creación de una nueva campaña basada en un tipo de campaña, lista de destinatarios y configuración de la campaña (línea de asunto, título, from_name and reply_to)

```POST: /campaigns```

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Description |
| --- | --- | --- | --- | --- | --- |
| newCampaignRequest | |yes |body |Ninguna |Objeto JSON que se va a enviar en el cuerpo con los nuevos parámetros de solicitud de la campaña |

#### <a name="response"></a>Response
| Nombre | Descripción |
| --- | --- |
| 200 |OK |
| 400 |Bad Request |
| 401 |No autorizado |
| 403 |Prohibido |
| 404 |No encontrado |
| 500 |Error interno del servidor. Error desconocido |
| default |Error en la operación. |

## <a name="newlist"></a>newlist
Nueva lista: creación de una nueva lista en su cuenta de MailChimp

```POST: /lists```

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Description |
| --- | --- | --- | --- | --- | --- |
| newListRequest | |yes |body |Ninguna |Objeto JSON que se va a enviar en el cuerpo con los nuevos parámetros de solicitud de la campaña |

#### <a name="response"></a>Response
| Nombre | Descripción |
| --- | --- |
| 200 |OK |
| 400 |Bad Request |
| 401 |No autorizado |
| 403 |Prohibido |
| 404 |No encontrado |
| 500 |Error interno del servidor. Error desconocido |
| default |Error en la operación. |

## <a name="addmember"></a>addmember
Agregar miembro a lista: adición o actualización de un miembro de la lista

```POST: /lists/{list_id}/members```

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Description |
| --- | --- | --- | --- | --- | --- |
| list_id |string |Sí |path |Ninguna |El identificador único de la lista |
| newMemberInList | |yes |body |Ninguna |Objeto JSON que se va a enviar en el cuerpo con la información del nuevo miembro |

#### <a name="response"></a>Response
| Nombre | Descripción |
| --- | --- |
| 200 |OK |
| 400 |Bad Request |
| 401 |No autorizado |
| 403 |Prohibido |
| 404 |No encontrado |
| 500 |Error interno del servidor. Error desconocido |
| default |Error en la operación. |

## <a name="removemember"></a>removemember
Quitar miembro de la lista: eliminación de un miembro de una lista.

```DELETE: /lists/replacemailwithhash/{list_id}/members/{member_email}```

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Description |
| --- | --- | --- | --- | --- | --- |
| list_id |string |Sí |path |Ninguna |El identificador único de la lista |
| member_email |string |Sí |path |Ninguna |La dirección de correo electrónico del miembro que se va a eliminar |

#### <a name="response"></a>Response
| Nombre | Descripción |
| --- | --- |
| 200 |OK |
| 400 |Bad Request |
| 401 |No autorizado |
| 403 |Prohibido |
| 404 |No encontrado |
| 500 |Error interno del servidor. Error desconocido |
| default |Error en la operación. |

## <a name="updatemember"></a>updatemember
Actualizar información del miembro: actualización de información de un miembro específico de la lista

```PATCH: /lists/replacemailwithhash/{list_id}/members/{member_email}```

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Description |
| --- | --- | --- | --- | --- | --- |
| list_id |string |Sí |path |Ninguna |El identificador único de la lista |
| member_email |string |Sí |path |Ninguna |La dirección de correo electrónico única del miembro que se va a actualizar |
| updateMemberInListRequest | |yes |body |Ninguna |Objeto JSON que se va a enviar en el cuerpo con la información actualizada del miembro |

#### <a name="response"></a>Response
| Nombre | Descripción |
| --- | --- |
| 200 |OK |
| 400 |Bad Request |
| 401 |No autorizado |
| 403 |Prohibido |
| 404 |No encontrado |
| 500 |Error interno del servidor. Error desconocido |
| default |Error en la operación. |

## <a name="onmembersubscribed"></a>OnMemberSubscribed
Cuando se ha agregado un miembro a una lista: desencadena un flujo de trabajo cuando se ha agregado un nuevo miembro a una lista

```GET: /trigger/lists/{list_id}/members```

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Description |
| --- | --- | --- | --- | --- | --- |
| list_id |string |Sí |path |Ninguna |El identificador único de la lista |

#### <a name="response"></a>Response
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

## <a name="oncreatelist"></a>OnCreateList
Cuando se crea una nueva lista: desencadena un flujo de trabajo cuando se crea una nueva lista

```GET: /trigger/lists```

No hay parámetros para esta llamada

#### <a name="response"></a>Response
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

## <a name="object-definitions"></a>Definiciones de objeto
### <a name="newcampaignrequest"></a>NewCampaignRequest
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| type |string |Sí |
| recipients |not defined |Sí |
| configuración |not defined |Sí |
| variate_settings |not defined |No |
| tracking |not defined |No |
| rss_opts |not defined |No |
| social_card |not defined |No |

### <a name="recipient"></a>Recipient
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| list_id |string |Sí |
| segment_opts |not defined |No |

### <a name="settings"></a>Settings
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| subject_line |string |Sí |
| título |string |No |
| from_name |string |Sí |
| reply_to |string |Sí |
| use_conversation |boolean |No |
| to_name |string |No |
| folder_id |integer |No |
| authenticate |boolean |No |
| auto_footer |boolean |No |
| inline_css |boolean |No |
| auto_tweet |boolean |No |
| auto_fb_post |array |No |
| fb_comments |boolean |No |

### <a name="variatesettings"></a>Variate_Settings
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| winner_criteria |string |No |
| wait_time |integer |No |
| test_size |integer |No |
| subject_lines |array |No |
| send_times |array |No |
| from_names |array |No |
| reply_to_addresses |array |No |

### <a name="tracking"></a>Seguimiento
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| opens |boolean |No |
| html_clicks |boolean |No |
| text_clicks |boolean |No |
| goal_tracking |boolean |No |
| ecomm360 |boolean |No |
| google_analytics |string |No |
| clicktale |string |No |
| salesforce |not defined |No |
| highrise |not defined |No |
| capsule |not defined |No |

### <a name="rssopts"></a>RSS_Opts
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| feed_url |string |No |
| frequency |string |No |
| constrain_rss_img |string |No |
| schedule |not defined |No |

### <a name="socialcard"></a>Social_Card
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| image_url |string |No |
| description |string |No |
| título |string |No |

### <a name="segmentopts"></a>Segment_Opts
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| saved_segment_id |integer |No |
| match |string |No |

### <a name="salesforce"></a>Salesforce
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| campaign |boolean |No |
| HDInsight |boolean |No |

### <a name="highrise"></a>Highrise
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| campaign |boolean |No |
| HDInsight |boolean |No |

### <a name="capsule"></a>Capsule
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| HDInsight |boolean |No |

### <a name="schedule"></a>Schedule
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| hour |integer |No |
| daily_send |not defined |No |
| weekly_send_day |string |No |
| monthly_send_date |número |No |

### <a name="dailysend"></a>Daily_Send
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| sunday |boolean |No |
| monday |boolean |No |
| tuesday |boolean |No |
| wednesday |boolean |No |
| thursday |boolean |No |
| friday |boolean |No |
| saturday |boolean |No |

### <a name="campaignresponsemodel"></a>CampaignResponseModel
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| id |string |No |
| type |string |No |
| create_time |string |No |
| archive_url |string |No |
| status |string |No |
| emails_sent |integer |No |
| send_time |string |No |
| content_type |string |No |
| recipient |array |No |
| configuración |not defined |No |
| variate_settings |not defined |No |
| tracking |not defined |No |
| rss_opts |not defined |No |
| ab_split_opts |not defined |No |
| social_card |not defined |No |
| report_summary |not defined |No |
| delivery_status |not defined |No |
| _links |array |No |

### <a name="absplitopts"></a>AB_Split_Opts
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| split_test |string |No |
| pick_winner |string |No |
| wait_units |string |No |
| wait_time |integer |No |
| split_size |integer |No |
| from_name_a |string |No |
| from_name_b |string |No |
| reply_email_a |string |No |
| reply_email_b |string |No |
| subject_a |string |No |
| subject_b |string |No |
| send_time_a |string |No |
| send_time_b |string |No |
| send_time_winner |string |No |

### <a name="reportsummary"></a>Report_Summary
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| opens |integer |No |
| unique_opens |integer |No |
| open_rate |número |No |
| clicks |integer |No |
| subscriber_clicks |número |No |
| click_rate |número |No |

### <a name="deliverystatus"></a>Delivery_Status
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| enabled |boolean |No |
| can_cancel |boolean |No |
| status |string |No |
| emails_sent |integer |No |
| emails_canceled |integer |No |

### <a name="link"></a>Vínculo
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| rel |string |No |
| href |string |No |
| estático |string |No |
| targetSchema |string |No |
| schema |string |No |

### <a name="newlistrequest"></a>NewListRequest
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| Nombre |cadena |Sí |
| contact |not defined |Sí |
| permission_reminder |string |Sí |
| use_archive_bar |boolean |No |
| campaign_defaults |not defined |Sí |
| notify_on_subscribe |string |No |
| notify_on_unsubscribe |string |No |
| email_type_option |boolean |Sí |
| visibility |string |No |

### <a name="contact"></a>Contacto
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

### <a name="campaigndefaults"></a>Campaign_Defaults
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| from_name |string |Sí |
| from_email |string |Sí |
| subject |string |No |
| language |string |Sí |

### <a name="createnewlistresponsemodel"></a>CreateNewListResponseModel
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| id |cadena |Sí |
| name |cadena |Sí |
| contact |not defined |Sí |
| permission_reminder |string |Sí |
| use_archive_bar |boolean |No |
| campaign_defaults |not defined |Sí |
| notify_on_subscribe |string |No |
| notify_on_unsubscribe |string |No |
| date_created |string |No |
| list_rating |integer |No |
| email_type_option |boolean |Sí |
| subscribe_url_short |string |No |
| subscribe_url_long |string |No |
| beamer_address |string |No |
| visibility |string |No |
| modules |array |No |
| stats |not defined |No |
| _links |array |No |

### <a name="stats"></a>Estadísticas
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| member_count |integer |No |
| unsubscribe_count |integer |No |
| cleaned_count |integer |No |
| member_count_since_send |integer |No |
| unsubscribe_count_since_send |integer |No |
| cleaned_count_since_send |integer |No |
| campaign_count |integer |No |
| campaign_last_sent |integer |No |
| merge_field_count |integer |No |
| avg_sub_rate |número |No |
| avg_unsub_rate |número |No |
| target_sub_rate |número |No |
| open_rate |número |No |
| click_rate |número |No |
| last_sub_date |string |No |
| last_unsub_date |string |No |

### <a name="getlistsresponsemodel"></a>GetListsResponseModel
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| lists |array |No |
| total_items |integer |No |

### <a name="newmemberinlistrequest"></a>NewMemberInListRequest
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| email_type |string |No |
| status |string |Sí |
| merge_fields |not defined |No |
| interests |string |No |
| language |string |No |
| vip |boolean |No |
| location |not defined |No |
| email_address |string |Sí |

### <a name="firstandlastname"></a>FirstAndLastName
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| FNAME |string |No |
| LNAME |string |No |

### <a name="location"></a>Ubicación
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| latitude |número |No |
| longitude |número |No |

### <a name="memberresponsemodel"></a>MemberResponseModel
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| id |string |No |
| email_address |string |No |
| unique_email_id |string |No |
| email_type |string |No |
| status |string |No |
| merge_fields |not defined |No |
| interests |string |No |
| stats |not defined |No |
| ip_signup |string |No |
| timestamp_signup |string |No |
| ip_opt |string |No |
| timestamp_opt |string |No |
| member_rating |integer |No |
| last_changed |string |No |
| language |string |No |
| vip |boolean |No |
| email_client |string |No |
| location |not defined |No |
| last_note |not defined |No |
| list_id |string |No |
| _links |array |No |

### <a name="lastnote"></a>Last_Note
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| note_id |integer |No |
| created_at |string |No |
| created_by |string |No |
| note |string |No |

### <a name="getallmembersresponsemodel"></a>GetAllMembersResponseModel
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| members |array |No |
| list_id |string |No |
| total_items |integer |No |

### <a name="object"></a>Objeto
### <a name="updatememberinlistrequest"></a>UpdateMemberInListRequest
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| email_address |string |No |
| email_type |string |No |
| status |string |Sí |
| merge_fields |not defined |No |
| interests |string |No |
| language |string |No |
| vip |boolean |No |
| location |not defined |No |

### <a name="getmembersresponsemodel"></a>GetMembersResponseModel
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| members |array |No |
| list_id |string |No |
| total_items |integer |No |

### <a name="adduserresponsemodel"></a>AddUserResponseModel
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| id |cadena |Sí |
| email_address |string |Sí |
| unique_email_id |string |No |
| email_type |string |No |
| status |string |No |
| merge_fields |not defined |Sí |
| interests |string |No |
| stats |not defined |No |
| ip_signup |string |No |
| timestamp_signup |string |No |
| ip_opt |string |No |
| timestamp_opt |string |No |
| member_rating |integer |No |
| last_changed |string |No |
| language |string |No |
| vip |boolean |No |
| email_client |string |No |
| location |not defined |No |
| last_note |not defined |No |
| list_id |string |No |
| _links |array |No |

## <a name="next-steps"></a>Pasos siguientes
[Creación de una aplicación lógica](../logic-apps/logic-apps-create-a-logic-app.md)




<!--HONumber=Jan17_HO3-->


