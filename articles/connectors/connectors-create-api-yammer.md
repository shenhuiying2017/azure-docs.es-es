<properties
pageTitle="Incorporación de la API de Yammer en las Aplicaciones lógicas | Microsoft Azure"
description="Información general de la API de Yammer con parámetros de la API de REST"
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
ms.date="03/16/2016"
ms.author="deonhe"/>

# Introducción a la API de Yammer

Conectarse a Yammer para acceder a conversaciones en la red empresarial.

>[AZURE.NOTE] Esta versión del artículo se aplica a la versión de esquema 2015-08-01-preview de las aplicaciones lógicas.

Con Yammer, puede:

- Compilar el flujo de negocio en función de los datos que obtiene de Yammer. 
- Usar desencadenadores cuando haya un nuevo mensaje en un grupo o en una fuente que le sigue.
- Usar acciones para publicar un mensaje, obtener todos los mensajes y mucho más. Estas acciones obtienen una respuesta y luego dejan el resultado a disposición de otras acciones. Por ejemplo, cuando aparece un nuevo mensaje, puede enviar un correo electrónico mediante Office 365.

Para agregar una operación en aplicaciones lógicas, consulte [Creación de una nueva aplicación lógica mediante la conexión de servicios de SaaS](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Desencadenadores y acciones
Dropbox incluye los desencadenadores y las acciones siguientes.

Desencadenador | Acciones
--- | ---
<ul><li>Cuando haya un nuevo mensaje en un grupo</li><li>Cuando haya un nuevo mensaje en la fuente de las personas a quienes sigo</li></ul>| <ul><li>Obtener todos los mensajes</li><li>Obtiene mensajes de un grupo</li><li>Obtiene mensajes de la fuente de las personas a quienes sigo</li><li>Publicar mensaje</li><li>Cuando haya un nuevo mensaje en un grupo</li><li>Cuando haya un nuevo mensaje en la fuente de las personas a quienes sigo</li></ul>

Todas las API admiten datos en formato JSON y XML.

## Creación de una conexión a Yammer
Para usar la API de Yammer, cree primero una **conexión** y, después, especifique los detalles de las siguientes propiedades:

|Propiedad| Obligatorio|Descripción|
| ---|---|---|
|Se necesita el cifrado de tokens|Sí|Proporcionar credenciales de Yammer|

>[AZURE.INCLUDE [Pasos para crear una conexión a Yammer](../../includes/connectors-create-api-yammer.md)]


>[AZURE.TIP] Puede usar esta conexión en otras aplicaciones lógicas.

## Referencia de API de REST de Yammer
Esta documentación corresponde a la versión: 1.0


### Obtener todos los mensajes públicos en la red Yammer del usuario conectado
Corresponde a "todas" las conversaciones de la interfaz web de Yammer. ```GET: /messages.json```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|older\_then|integer|no|query|Ninguna|Devuelve mensajes más antiguos que el identificador de mensaje especificado como cadena numérica. Resulta útil para paginar los mensajes. Por ejemplo, si actualmente está viendo 20 mensajes y el más antiguo es el número 2912, puede anexar "? older\_than = 2912″ a su solicitud para obtener los 20 mensajes anteriores a los que está viendo.|
|newer\_then|integer|no|query|Ninguna|Devuelve los mensajes más recientes que el identificador de mensaje especificado como cadena numérica. Se debe usar al sondear en busca de nuevos mensajes. Si está buscando mensajes, y el mensaje más reciente devuelto es 3516, puede realizar una solicitud con el parámetro "? newer\_than = 3516″ para asegurarse de que no se obtienen copias duplicadas de mensajes que se encuentran ya en la página.|
|limit|integer|no|query|Ninguna|Devolver solo el número especificado de mensajes.|
|page|integer|no|query|Ninguna|Obtener la página especificada. Si los datos devueltos superan el límite, puede usar este campo para acceder a las páginas siguientes|


### Respuesta

|Nombre|Descripción|
|---|---|
|200|OK|
|400|Bad Request|
|408|Tiempo de espera de solicitud|
|429|Demasiadas solicitudes|
|500|Error interno del servidor. Error desconocido|
|503|Servicio de Yammer no disponible|
|504|Tiempo de espera de puerta de enlace|
|default|Error en la operación.|


### Publicar un mensaje en un grupo o una fuente de toda la compañía
Si se proporciona el identificador de grupo, el mensaje se publicará en el grupo especificado; si no, se publicará en la fuente de toda la empresa. ```POST: /messages.json```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|input| |yes|body|Ninguna|Solicitud de publicación de mensaje|


### Respuesta

|Nombre|Descripción|
|---|---|
|201|Creado|



## Definiciones de objeto

#### Message: mensaje de Yammer

| Nombre | Tipo de datos | Obligatorio |
|---|---| --- | 
|id|integer|no|
|content\_excerpt|cadena|no|
|sender\_id|integer|no|
|replied\_to\_id|integer|no|
|created\_at|cadena|no|
|network\_id|integer|no|
|message\_type|cadena|no|
|sender\_type|cadena|no|
|url|cadena|no|
|web\_url|cadena|no|
|group\_id|integer|no|
|body|not defined|no|
|thread\_id|integer|no|
|direct\_message|boolean|no|
|client\_type|cadena|no|
|client\_url|cadena|no|
|language|cadena|no|
|notified\_user\_ids|array|no|
|privacy|cadena|no|
|liked\_by|not defined|no|
|system\_message|boolean|no|

#### PostOperationRequest: representa una solicitud post del conector de Yammer para publicar en Yammer

| Nombre | Tipo de datos | Obligatorio |
|---|---| --- | 
|body|cadena|yes|
|group\_id|integer|no|
|replied\_to\_id|integer|no|
|direct\_to\_id|integer|no|
|broadcast|boolean|no|
|topic1|cadena|no|
|topic2|cadena|no|
|topic3|cadena|no|
|topic4|cadena|no|
|topic5|cadena|no|
|topic6|cadena|no|
|topic7|cadena|no|
|topic8|cadena|no|
|topic9|cadena|no|
|topic10|cadena|no|
|topic11|cadena|no|
|topic12|cadena|no|
|topic13|cadena|no|
|topic14|cadena|no|
|topic15|cadena|no|
|topic16|cadena|no|
|topic17|cadena|no|
|topic18|cadena|no|
|topic19|cadena|no|
|topic20|cadena|no|

#### MessageList: lista de mensajes

| Nombre | Tipo de datos | Obligatorio |
|---|---| --- | 
|messages|array|no|


#### MessageBody: cuerpo del mensaje

| Nombre | Tipo de datos | Obligatorio |
|---|---| --- | 
|parsed|cadena|no|
|plain|cadena|no|
|rich|cadena|no|

#### LikedBy: le gusta a

| Nombre | Tipo de datos | Obligatorio |
|---|---| --- | 
|count|integer|no|
|names|array|no|

#### YammmerEntity: le gusta a

| Nombre | Tipo de datos | Obligatorio |
|---|---| --- | 
|type|cadena|no|
|id|integer|no|
|full\_name|cadena|no|


## Pasos siguientes
[Creación de una nueva aplicación lógica mediante la conexión de servicios de SaaS](../app-service-logic/app-service-logic-create-a-logic-app.md)

[1]: ./media/connectors-create-api-yammer/connectionconfig1.png
[2]: ./media/connectors-create-api-yammer/connectionconfig2.png
[3]: ./media/connectors-create-api-yammer/connectionconfig3.png
[4]: ./media/connectors-create-api-yammer/connectionconfig4.png
[5]: ./media/connectors-create-api-yammer/connectionconfig5.png

<!---HONumber=AcomDC_0518_2016-->