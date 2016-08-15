<properties
pageTitle="MailChimp | Microsoft Azure"
description="Cree aplicaciones lógicas con el Servicio de aplicaciones de Azure. MailChimp es un servicio SaaS que permite a las empresas a administrar y automatizar actividades de marketing por correo electrónico, incluido el envío de correos electrónicos de marketing, mensajes automatizados y campañas de destino."
services="logic-apps"	
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
ms.date="05/17/2016"
ms.author="deonhe"/>

# Get started with the MailChimp connector (Introducción al conector MailChimp)



El conector MailChimp puede usarse desde:

- [Aplicaciones lógicas](../app-service-logic/app-service-logic-what-are-logic-apps.md)
- [PowerApps](http://powerapps.microsoft.com)
- [Flujo](http://flows.microsoft.com)

>[AZURE.NOTE] Esta versión del artículo se aplica a la versión de esquema 2015-08-01-preview de las aplicaciones lógicas.

Puede empezar creando una aplicación lógica ahora. Para ello, consulte [Crear una aplicación lógica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Desencadenadores y acciones

El conector MailChimp se puede usar como acción; tiene desencadenadores. Todos los conectores admiten datos en formato JSON y XML.

 El conector MailChimp tiene disponibles las siguientes acciones o desencadenadores:

### Acciones de MailChimp
Puede realizar estas acciones:

|Acción|Descripción|
|--- | ---|
|[newcampaign](connectors-create-api-mailchimp.md#newcampaign)|Creación de una nueva campaña basada en un tipo de campaña, lista de destinatarios y configuración de la campaña (línea de asunto, título, from\_name and reply\_to)|
|[newlist](connectors-create-api-mailchimp.md#newlist)|Creación de una nueva lista en su cuenta de MailChimp|
|[addmember](connectors-create-api-mailchimp.md#addmember)|Adición o actualización de un miembro de la lista|
|[removemember](connectors-create-api-mailchimp.md#removemember)|Eliminación de un miembro de una lista|
|[updatemember](connectors-create-api-mailchimp.md#updatemember)|Actualización de información de un miembro específico de la lista|
### Desencadenadores de MailChimp
Se pueden escuchar estos eventos:

|Desencadenador | Descripción|
|--- | ---|
|Cuando se ha agregado un miembro a una lista|Desencadena un flujo de trabajo cuando se ha agregado un nuevo miembro a una lista|
|Cuando se crea una nueva lista|Desencadena un flujo de trabajo cuando se crea una nueva lista|


## Creación de una conexión a MailChimp
Para crear aplicaciones lógicas con MailChimp, primero debe crear una **conexión** y, a continuación, proporcionar los detalles de las siguientes propiedades:

|Propiedad| Obligatorio|Descripción|
| ---|---|---|
|Se necesita el cifrado de tokens|Sí|Proporcionar las credenciales de MailChimp|

>[AZURE.INCLUDE [Pasos para crear una conexión a MailChimp](../../includes/connectors-create-api-mailchimp.md)]

>[AZURE.TIP] Puede usar esta conexión en otras aplicaciones lógicas.

## Referencia para MailChimp
Se aplica a la versión: 1.0

## newcampaign
Nueva campaña: creación de una nueva campaña basada en un tipo de campaña, lista de destinatarios y configuración de la campaña (línea de asunto, título, from\_name and reply\_to)

```POST: /campaigns```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|newCampaignRequest| |yes|body|Ninguna|Objeto JSON que se va a enviar en el cuerpo con los nuevos parámetros de solicitud de la campaña|

#### Respuesta

|Nombre|Descripción|
|---|---|
|200|OK|
|400|Bad Request|
|401|No autorizado|
|403|Prohibido|
|404|No encontrado|
|500|Error interno del servidor. Error desconocido|
|default|Error en la operación.|


## newlist
Nueva lista: creación de una nueva lista en su cuenta de MailChimp

```POST: /lists```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|newListRequest| |yes|body|Ninguna|Objeto JSON que se va a enviar en el cuerpo con los nuevos parámetros de solicitud de la campaña|

#### Respuesta

|Nombre|Descripción|
|---|---|
|200|OK|
|400|Bad Request|
|401|No autorizado|
|403|Prohibido|
|404|No encontrado|
|500|Error interno del servidor. Error desconocido|
|default|Error en la operación.|


## addmember
Agregar miembro a lista: adición o actualización de un miembro de la lista

```POST: /lists/{list_id}/members```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|list\_id|cadena|yes|path|Ninguna|El identificador único de la lista|
|newMemberInList| |yes|body|Ninguna|Objeto JSON que se va a enviar en el cuerpo con la información del nuevo miembro|

#### Respuesta

|Nombre|Descripción|
|---|---|
|200|OK|
|400|Bad Request|
|401|No autorizado|
|403|Prohibido|
|404|No encontrado|
|500|Error interno del servidor. Error desconocido|
|default|Error en la operación.|


## removemember
Quitar miembro de la lista: eliminación de un miembro de una lista.

```DELETE: /lists/replacemailwithhash/{list_id}/members/{member_email}```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|list\_id|cadena|yes|path|Ninguna|El identificador único de la lista|
|member\_email|cadena|yes|path|Ninguna|La dirección de correo electrónico del miembro que se va a eliminar|

#### Respuesta

|Nombre|Descripción|
|---|---|
|200|OK|
|400|Bad Request|
|401|No autorizado|
|403|Prohibido|
|404|No encontrado|
|500|Error interno del servidor. Error desconocido|
|default|Error en la operación.|


## updatemember
Actualizar información del miembro: actualización de información de un miembro específico de la lista

```PATCH: /lists/replacemailwithhash/{list_id}/members/{member_email}```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|list\_id|cadena|yes|path|Ninguna|El identificador único de la lista|
|member\_email|cadena|yes|path|Ninguna|La dirección de correo electrónico única del miembro que se va a actualizar|
|updateMemberInListRequest| |yes|body|Ninguna|Objeto JSON que se va a enviar en el cuerpo con la información actualizada del miembro|

#### Respuesta

|Nombre|Descripción|
|---|---|
|200|OK|
|400|Bad Request|
|401|No autorizado|
|403|Prohibido|
|404|No encontrado|
|500|Error interno del servidor. Error desconocido|
|default|Error en la operación.|


## OnMemberSubscribed
Cuando se ha agregado un miembro a una lista: desencadena un flujo de trabajo cuando se ha agregado un nuevo miembro a una lista

```GET: /trigger/lists/{list_id}/members```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|list\_id|cadena|yes|path|Ninguna|El identificador único de la lista|

#### Respuesta

|Nombre|Descripción|
|---|---|
|200|OK|
|202|Accepted|
|400|Bad Request|
|401|No autorizado|
|403|Prohibido|
|404|No encontrado|
|500|Error interno del servidor. Error desconocido|
|default|Error en la operación.|


## OnCreateList
Cuando se crea una nueva lista: desencadena un flujo de trabajo cuando se crea una nueva lista

```GET: /trigger/lists```

No hay parámetros para esta llamada
#### Respuesta

|Nombre|Descripción|
|---|---|
|200|OK|
|202|Accepted|
|400|Bad Request|
|401|No autorizado|
|403|Prohibido|
|404|No encontrado|
|500|Error interno del servidor. Error desconocido|
|default|Error en la operación.|


## Definiciones de objeto 

### NewCampaignRequest


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|type|cadena|Sí |
|recipients|not defined|Sí |
|configuración|not defined|Sí |
|variate\_settings|not defined|No |
|tracking|not defined|No |
|rss\_opts|not defined|No |
|social\_card|not defined|No |



### Recipient


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|list\_id|cadena|Sí |
|segment\_opts|not defined|No |



### Settings


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|subject\_line|cadena|Sí |
|título|cadena|No |
|from\_name|cadena|Sí |
|reply\_to|cadena|Sí |
|use\_conversation|boolean|No |
|to\_name|cadena|No |
|folder\_id|integer|No |
|authenticate|boolean|No |
|auto\_footer|boolean|No |
|inline\_css|boolean|No |
|auto\_tweet|boolean|No |
|auto\_fb\_post|array|No |
|fb\_comments|boolean|No |



### Variate\_Settings


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|winner\_criteria|cadena|No |
|wait\_time|integer|No |
|test\_size|integer|No |
|subject\_lines|array|No |
|send\_times|array|No |
|from\_names|array|No |
|reply\_to\_addresses|array|No |



### Seguimiento


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|opens|boolean|No |
|html\_clicks|boolean|No |
|text\_clicks|boolean|No |
|goal\_tracking|boolean|No |
|ecomm360|boolean|No |
|google\_analytics|cadena|No |
|clicktale|cadena|No |
|salesforce|not defined|No |
|highrise|not defined|No |
|capsule|not defined|No |



### RSS\_Opts


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|feed\_url|cadena|No |
|frequency|cadena|No |
|constrain\_rss\_img|cadena|No |
|schedule|not defined|No |



### Social\_Card


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|image\_url|cadena|No |
|description|cadena|No |
|título|cadena|No |



### Segment\_Opts


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|saved\_segment\_id|integer|No |
|match|cadena|No |



### Salesforce


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|campaign|boolean|No |
|HDInsight|boolean|No |



### Highrise


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|campaign|boolean|No |
|HDInsight|boolean|No |



### Capsule


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|HDInsight|boolean|No |



### Schedule


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|hour|integer|No |
|daily\_send|not defined|No |
|weekly\_send\_day|cadena|No |
|monthly\_send\_date|número|No |



### Daily\_Send


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|sunday|boolean|No |
|monday|boolean|No |
|tuesday|boolean|No |
|wednesday|boolean|No |
|thursday|boolean|No |
|friday|boolean|No |
|saturday|boolean|No |



### CampaignResponseModel


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|id|cadena|No |
|type|cadena|No |
|create\_time|cadena|No |
|archive\_url|cadena|No |
|status|cadena|No |
|emails\_sent|integer|No |
|send\_time|cadena|No |
|content\_type|cadena|No |
|recipient|array|No |
|configuración|not defined|No |
|variate\_settings|not defined|No |
|tracking|not defined|No |
|rss\_opts|not defined|No |
|ab\_split\_opts|not defined|No |
|social\_card|not defined|No |
|report\_summary|not defined|No |
|delivery\_status|not defined|No |
|\_links|array|No |



### AB\_Split\_Opts


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|split\_test|cadena|No |
|pick\_winner|cadena|No |
|wait\_units|cadena|No |
|wait\_time|integer|No |
|split\_size|integer|No |
|from\_name\_a|cadena|No |
|from\_name\_b|cadena|No |
|reply\_email\_a|cadena|No |
|reply\_email\_b|cadena|No |
|subject\_a|cadena|No |
|subject\_b|cadena|No |
|send\_time\_a|cadena|No |
|send\_time\_b|cadena|No |
|send\_time\_winner|cadena|No |



### Report\_Summary


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|opens|integer|No |
|unique\_opens|integer|No |
|open\_rate|número|No |
|clicks|integer|No |
|subscriber\_clicks|número|No |
|click\_rate|número|No |



### Delivery\_Status


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|enabled|boolean|No |
|can\_cancel|boolean|No |
|status|cadena|No |
|emails\_sent|integer|No |
|emails\_canceled|integer|No |



### Vínculo


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|rel|cadena|No |
|href|cadena|No |
|estático|cadena|No |
|targetSchema|cadena|No |
|schema|cadena|No |



### NewListRequest


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|name|cadena|Sí |
|contact|not defined|Sí |
|permission\_reminder|cadena|Sí |
|use\_archive\_bar|boolean|No |
|campaign\_defaults|not defined|Sí |
|notify\_on\_subscribe|cadena|No |
|notify\_on\_unsubscribe|cadena|No |
|email\_type\_option|boolean|Sí |
|visibility|cadena|No |



### Contacto


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|company|cadena|Sí |
|address1|cadena|Sí |
|address2|cadena|No |
|city|cadena|Sí |
|state|cadena|Sí |
|zip|cadena|Sí |
|country|cadena|Sí |
|phone|cadena|Sí |



### Campaign\_Defaults


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|from\_name|cadena|Sí |
|from\_email|cadena|Sí |
|subject|cadena|No |
|language|cadena|Sí |



### CreateNewListResponseModel


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|id|cadena|Sí |
|name|cadena|Sí |
|contact|not defined|Sí |
|permission\_reminder|cadena|Sí |
|use\_archive\_bar|boolean|No |
|campaign\_defaults|not defined|Sí |
|notify\_on\_subscribe|cadena|No |
|notify\_on\_unsubscribe|cadena|No |
|date\_created|cadena|No |
|list\_rating|integer|No |
|email\_type\_option|boolean|Sí |
|subscribe\_url\_short|cadena|No |
|subscribe\_url\_long|cadena|No |
|beamer\_address|cadena|No |
|visibility|cadena|No |
|modules|array|No |
|stats|not defined|No |
|\_links|array|No |



### Estadísticas


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|member\_count|integer|No |
|unsubscribe\_count|integer|No |
|cleaned\_count|integer|No |
|member\_count\_since\_send|integer|No |
|unsubscribe\_count\_since\_send|integer|No |
|cleaned\_count\_since\_send|integer|No |
|campaign\_count|integer|No |
|campaign\_last\_sent|integer|No |
|merge\_field\_count|integer|No |
|avg\_sub\_rate|número|No |
|avg\_unsub\_rate|número|No |
|target\_sub\_rate|número|No |
|open\_rate|número|No |
|click\_rate|número|No |
|last\_sub\_date|cadena|No |
|last\_unsub\_date|cadena|No |



### GetListsResponseModel


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|lists|array|No |
|total\_items|integer|No |



### NewMemberInListRequest


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|email\_type|cadena|No |
|status|cadena|Sí |
|merge\_fields|not defined|No |
|interests|cadena|No |
|language|cadena|No |
|vip|boolean|No |
|location|not defined|No |
|email\_address|cadena|Sí |



### FirstAndLastName


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|FNAME|cadena|No |
|LNAME|cadena|No |



### Ubicación


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|latitude|número|No |
|longitude|número|No |



### MemberResponseModel


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|id|cadena|No |
|email\_address|cadena|No |
|unique\_email\_id|cadena|No |
|email\_type|cadena|No |
|status|cadena|No |
|merge\_fields|not defined|No |
|interests|cadena|No |
|stats|not defined|No |
|ip\_signup|cadena|No |
|timestamp\_signup|cadena|No |
|ip\_opt|cadena|No |
|timestamp\_opt|cadena|No |
|member\_rating|integer|No |
|last\_changed|cadena|No |
|language|cadena|No |
|vip|boolean|No |
|email\_client|cadena|No |
|location|not defined|No |
|last\_note|not defined|No |
|list\_id|cadena|No |
|\_links|array|No |



### Last\_Note


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|note\_id|integer|No |
|created\_at|cadena|No |
|created\_by|cadena|No |
|note|cadena|No |



### GetAllMembersResponseModel


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|members|array|No |
|list\_id|cadena|No |
|total\_items|integer|No |



### Objeto


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|



### UpdateMemberInListRequest


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|email\_address|cadena|No |
|email\_type|cadena|No |
|status|cadena|Sí |
|merge\_fields|not defined|No |
|interests|cadena|No |
|language|cadena|No |
|vip|boolean|No |
|location|not defined|No |



### GetMembersResponseModel


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|members|array|No |
|list\_id|cadena|No |
|total\_items|integer|No |



### AddUserResponseModel


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|id|cadena|Sí |
|email\_address|cadena|Sí |
|unique\_email\_id|cadena|No |
|email\_type|cadena|No |
|status|cadena|No |
|merge\_fields|not defined|Sí |
|interests|cadena|No |
|stats|not defined|No |
|ip\_signup|cadena|No |
|timestamp\_signup|cadena|No |
|ip\_opt|cadena|No |
|timestamp\_opt|cadena|No |
|member\_rating|integer|No |
|last\_changed|cadena|No |
|language|cadena|No |
|vip|boolean|No |
|email\_client|cadena|No |
|location|not defined|No |
|last\_note|not defined|No |
|list\_id|cadena|No |
|\_links|array|No |


## Pasos siguientes
[Creación de una aplicación lógica](../app-service-logic/app-service-logic-create-a-logic-app.md)

<!---HONumber=AcomDC_0803_2016-->