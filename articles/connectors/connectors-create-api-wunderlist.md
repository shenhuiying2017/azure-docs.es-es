<properties
pageTitle="Wunderlist | Microsoft Azure"
description="Cree aplicaciones lógicas con el Servicio de aplicaciones de Azure. Wunderlist proporciona una lista de tareas pendientes y un administrador de tareas para ayudar a los usuarios a hacer su trabajo. Ya sea que comparta una lista de la compra con un ser querido, trabaje en un proyecto o planee unas vacaciones, capturar, compartir y completar tareas pendientes con Wunderlist es sencillo. Wunderlist sincroniza de forma instantánea su teléfono, tableta o PC para que pueda tener acceso a todas las tareas desde cualquier lugar."
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
ms.date="08/18/2016"
ms.author="deonhe"/>

# Introducción al conector Wunderlist

Wunderlist proporciona una lista de tareas pendientes y un administrador de tareas para ayudar a los usuarios a hacer su trabajo. Ya sea que comparta una lista de la compra con un ser querido, trabaje en un proyecto o planee unas vacaciones, capturar, compartir y completar tareas pendientes con Wunderlist es sencillo. Wunderlist sincroniza de forma instantánea su teléfono, tableta o PC para que pueda tener acceso a todas las tareas desde cualquier lugar.

>[AZURE.NOTE] Esta versión del artículo se aplica a la versión de esquema 2015-08-01-preview de las aplicaciones lógicas.

Puede empezar creando una aplicación lógica ahora. Para ello, consulte [Creación de una nueva aplicación lógica mediante la conexión de servicios de SaaS](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Desencadenadores y acciones

El conector Wunderlist se puede usar como acción; tiene desencadenadores. Todos los conectores admiten datos en formato JSON y XML.

 El conector Wunderlist tiene las siguientes acciones y desencadenadores disponibles:

### Acciones de Wunderlist
Puede realizar estas acciones:

|Acción|Description|
|--- | ---|
|[RetrieveLists](connectors-create-api-wunderlist.md#retrievelists)|Recupera las listas asociadas a su cuenta.|
|[CreateList](connectors-create-api-wunderlist.md#createlist)|Crea una lista.|
|[ListTasks](connectors-create-api-wunderlist.md#listtasks)|Recupera las tareas de una lista específica.|
|[CreateTask](connectors-create-api-wunderlist.md#createtask)|Crea una tarea.|
|[ListSubTasks](connectors-create-api-wunderlist.md#listsubtasks)|Recupera las subtareas de una lista o tarea específica.|
|[CreateSubTask](connectors-create-api-wunderlist.md#createsubtask)|Crear una subtarea dentro de una tarea específica.|
|[ListNotes](connectors-create-api-wunderlist.md#listnotes)|Recupera las notas de una lista o tarea específica.|
|[CreateNote](connectors-create-api-wunderlist.md#createnote)|Agrega una nota a una tarea específica.|
|[ListComments](connectors-create-api-wunderlist.md#listcomments)|Recupera los comentarios de tarea de una lista o tarea específica.|
|[CreateComment](connectors-create-api-wunderlist.md#createcomment)|Agrega un comentario a una tarea específica.|
|[RetrieveReminders](connectors-create-api-wunderlist.md#retrievereminders)|Recupera los recordatorios de una lista o tarea específica.|
|[CreateReminder](connectors-create-api-wunderlist.md#createreminder)|Establece un recordatorio.|
|[RetrieveFiles](connectors-create-api-wunderlist.md#retrievefiles)|Recupera los archivos de una lista o tarea específica.|
|[GetList](connectors-create-api-wunderlist.md#getlist)|Recupera una lista específica.|
|[DeleteList](connectors-create-api-wunderlist.md#deletelist)|Elimina una lista.|
|[UpdateList](connectors-create-api-wunderlist.md#updatelist)|Actualiza una lista específica.|
|[GetTask](connectors-create-api-wunderlist.md#gettask)|Recupera una tarea específica.|
|[UpdateTask](connectors-create-api-wunderlist.md#updatetask)|Actualiza una tarea específica.|
|[DeleteTask](connectors-create-api-wunderlist.md#deletetask)|Elimina una tarea específica.|
|[GetSubTask](connectors-create-api-wunderlist.md#getsubtask)|Recupera una subtarea específica.|
|[UpdateSubTask](connectors-create-api-wunderlist.md#updatesubtask)|Actualiza una subtarea específica.|
|[DeleteSubTask](connectors-create-api-wunderlist.md#deletesubtask)|Elimina una subtarea específica.|
|[GetNote](connectors-create-api-wunderlist.md#getnote)|Recupera una nota específica.|
|[UpdateNote](connectors-create-api-wunderlist.md#updatenote)|Actualiza una nota específica.|
|[DeleteNote](connectors-create-api-wunderlist.md#deletenote)|Elimina una nota específica.|
|[GetComment](connectors-create-api-wunderlist.md#getcomment)|Recupera un comentario de tarea específico.|
|[UpdateReminder](connectors-create-api-wunderlist.md#updatereminder)|Actualiza un recordatorio específico.|
|[DeleteReminder](connectors-create-api-wunderlist.md#deletereminder)|Elimina un recordatorio específico.|
### Desencadenadores de Wunderlist
Se pueden escuchar estos eventos:

|Desencadenador | Description|
|--- | ---|
|Cuando vence una tarea|Desencadena un nuevo flujo cuando vence una tarea de la lista.|
|Cuando se crea una nueva tarea|Desencadena un nuevo flujo cuando se crea una nueva tarea en la lista.|
|Cuando se produce un recordatorio|Desencadena un nuevo flujo cuando se produce un recordatorio|


## Creación de una conexión a Wunderlist
Para crear aplicaciones lógicas con Wunderlist, primero debe establecer una **conexión** y, después, especificar los detalles de las siguientes propiedades:

|Propiedad| Obligatorio|Description|
| ---|---|---|
|SWT|Sí|Proporciona las credenciales de Wunderlist.|
Después de crear la conexión, puede usarla para ejecutar las acciones y escuchar los desencadenadores descritos en este artículo.


>[AZURE.INCLUDE [Pasos para crear una conexión a Wunderlist](../../includes/connectors-create-api-wunderlist.md)]


>[AZURE.TIP] Puede usar esta conexión en otras aplicaciones lógicas.

## Referencia de Wunderlist
Se aplica a la versión: 1.0

## TriggerTaskDue
Cuando vence una tarea: desencadena un nuevo flujo cuando vence una tarea de la lista.

```GET: /trigger/tasksdue```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Description|
| ---|---|---|---|---|---|
|list\_id|integer|yes|query|Ninguna|Identificador de la lista|

#### Response

|Nombre|Descripción|
|---|---|
|200|Operación correcta|


## TriggerTaskNew
Cuando se crea una nueva tarea: desencadena un nuevo flujo cuando se crea una nueva tarea en la lista.

```GET: /trigger/tasksnew```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Description|
| ---|---|---|---|---|---|
|list\_id|integer|yes|query|Ninguna|Identificador de la lista|

#### Response

|Nombre|Descripción|
|---|---|
|200|Operación correcta|


## TriggerReminder
Cuando se produce un recordatorio: desencadena un nuevo flujo cuando se produce un recordatorio.

```GET: /trigger/reminders```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Description|
| ---|---|---|---|---|---|
|list\_id|integer|yes|query|Ninguna|Identificador de la lista|
|task\_id|integer|no|query|Ninguna|Identificador de la tarea|

#### Response

|Nombre|Descripción|
|---|---|
|200|Operación correcta|


## RetrieveLists
Obtener listas: recupera las listas asociadas a su cuenta.

```GET: /lists```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Description|
| ---|---|---|---|---|---|

#### Response

|Nombre|Descripción|
|---|---|
|200|Operación correcta|
|400|Bad Request|
|500|Error interno del servidor. Error desconocido|
|default|Error en la operación.|


## CreateList
Crear una lista: crea una lista.

```POST: /lists```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Description|
| ---|---|---|---|---|---|
|post| |yes|body|Ninguna|Nueva lista que se creará|

#### Response

|Nombre|Descripción|
|---|---|
|200|Operación correcta|
|default|Error en la operación.|


## ListTasks
Obtener tareas: recupera las tareas de una lista específica.

```GET: /tasks```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Description|
| ---|---|---|---|---|---|
|list\_id|integer|yes|query|Ninguna|Identificador de la lista|
|completed|boolean|no|query|Ninguna|Completed|

#### Response

|Nombre|Descripción|
|---|---|
|200|Operación correcta|
|400|Bad Request|
|500|Error interno del servidor. Error desconocido|
|default|Error en la operación.|


## CreateTask
Crear una tarea: crea una tarea.

```POST: /tasks```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Description|
| ---|---|---|---|---|---|
|post| |yes|body|Ninguna|Nueva tarea que se creará|

#### Response

|Nombre|Description|
|---|---|
|201|Creado|


## ListSubTasks
Obtener subtareas: recupera las subtareas de una lista o tarea específica.

```GET: /subtasks```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Description|
| ---|---|---|---|---|---|
|list\_id|integer|yes|query|Ninguna|Identificador de la lista|
|task\_id|integer|no|query|Ninguna|Identificador de la tarea|
|completed|boolean|no|query|Ninguna|Completed|

#### Response

|Nombre|Descripción|
|---|---|
|200|Operación correcta|
|400|Bad Request|
|500|Error interno del servidor. Error desconocido|
|default|Error en la operación.|


## CreateSubTask
Crear una subtarea: crea una subtarea dentro de una tarea específica.

```POST: /subtasks```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Description|
| ---|---|---|---|---|---|
|post| |yes|body|Ninguna|Nueva subtarea que se creará|

#### Response

|Nombre|Description|
|---|---|
|201|Creado|


## ListNotes
Obtener notas: recupera las notas de una lista o tarea específica.

```GET: /notes```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Description|
| ---|---|---|---|---|---|
|list\_id|integer|yes|query|Ninguna|Identificador de la lista|
|task\_id|integer|no|query|Ninguna|Identificador de la tarea|

#### Response

|Nombre|Descripción|
|---|---|
|200|Operación correcta|
|400|Bad Request|
|500|Error interno del servidor. Error desconocido|
|default|Error en la operación.|


## CreateNote
Crear una nota: agrega una nota a una tarea específica.

```POST: /notes```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Description|
| ---|---|---|---|---|---|
|post| |yes|body|Ninguna|Nueva nota que se creará|

#### Response

|Nombre|Description|
|---|---|
|201|Creado|


## ListComments
Obtener comentarios de tarea: recupera los comentarios de tarea de una lista o tarea específica.

```GET: /task_comments```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Description|
| ---|---|---|---|---|---|
|list\_id|integer|yes|query|Ninguna|Identificador de la lista|
|task\_id|integer|no|query|Ninguna|Identificador de la tarea|

#### Response

|Nombre|Descripción|
|---|---|
|200|Operación correcta|
|400|Bad Request|
|500|Error interno del servidor. Error desconocido|
|default|Error en la operación.|


## CreateComment
Agregar un comentario a una tarea: agrega un comentario a una tarea específica.

```POST: /task_comments```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Description|
| ---|---|---|---|---|---|
|post| |yes|body|Ninguna|Nuevo comentario de tarea que se creará|

#### Response

|Nombre|Description|
|---|---|
|201|Creado|


## RetrieveReminders
Obtener recordatorios: recupera los recordatorios de una lista o tarea específica.

```GET: /reminders```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Description|
| ---|---|---|---|---|---|
|list\_id|integer|yes|query|Ninguna|Identificador de la lista|
|task\_id|integer|no|query|Ninguna|Identificador de la tarea|

#### Response

|Nombre|Descripción|
|---|---|
|200|Operación correcta|
|400|Bad Request|
|500|Error interno del servidor. Error desconocido|
|default|Error en la operación.|


## CreateReminder
Establecer un recordatorio: establece un recordatorio.

```POST: /reminders```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Description|
| ---|---|---|---|---|---|
|post| |yes|body|Ninguna|Nuevo recordatorio que se creará|

#### Response

|Nombre|Descripción|
|---|---|
|200|Operación correcta|
|default|Error en la operación.|


## RetrieveFiles
Obtener archivos: recupera los archivos de una lista o tarea específica.

```GET: /files```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Description|
| ---|---|---|---|---|---|
|list\_id|integer|yes|query|Ninguna|Identificador de la lista|
|task\_id|integer|no|query|Ninguna|Identificador de la tarea|

#### Response

|Nombre|Descripción|
|---|---|
|200|Operación correcta|
|400|Bad Request|
|500|Error interno del servidor. Error desconocido|
|default|Error en la operación.|


## GetList
Obtener fila: recupera una lista específica.

```GET: /lists/{id}```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Description|
| ---|---|---|---|---|---|
|id|string|yes|path|Ninguna|Identificador de la lista|

#### Response

|Nombre|Descripción|
|---|---|
|200|OK|


## DeleteList
Eliminar lista: elimina una lista.

```DELETE: /lists/{id}```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Description|
| ---|---|---|---|---|---|
|id|integer|yes|path|Ninguna|Identificador de la lista|
|revision|integer|yes|query|Ninguna|Revisión|

#### Response

|Nombre|Description|
|---|---|
|204|Ningún contenido|


## UpdateList
Actualizar una lista: actualiza una lista específica.

```PATCH: /lists/{id}```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Description|
| ---|---|---|---|---|---|
|id|integer|yes|path|Ninguna|Identificador de la lista|
|post| |yes|body|Ninguna|Detalles de la lista|

#### Response

|Nombre|Descripción|
|---|---|
|200|OK|


## GetTask
Obtener tarea: recupera una tarea específica.

```GET: /tasks/{id}```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Description|
| ---|---|---|---|---|---|
|list\_id|integer|yes|query|Ninguna|Identificador de la lista|
|id|integer|yes|path|Ninguna|Identificador de la tarea|

#### Response

|Nombre|Descripción|
|---|---|
|200|OK|


## UpdateTask
Actualizar una tarea: actualiza una tarea específica.

```PATCH: /tasks/{id}```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Description|
| ---|---|---|---|---|---|
|list\_id|integer|yes|query|Ninguna|Identificador de la lista|
|id|integer|yes|path|Ninguna|Identificador de la tarea|
|post| |yes|body|Ninguna|Detalles de la tarea|

#### Response

|Nombre|Descripción|
|---|---|
|200|OK|


## DeleteTask
Eliminar tarea: elimina una tarea específica.

```DELETE: /tasks/{id}```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Description|
| ---|---|---|---|---|---|
|list\_id|integer|yes|query|Ninguna|Identificador de la lista|
|id|integer|yes|path|Ninguna|Identificador de la tarea|
|revision|integer|yes|query|Ninguna|Revisión|

#### Response

|Nombre|Description|
|---|---|
|204|Ningún contenido|


## GetSubTask
Obtener subtarea: recupera una subtarea específica.

```GET: /subtasks/{id}```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Description|
| ---|---|---|---|---|---|
|id|string|yes|path|Ninguna|Identificador de la subtarea|

#### Response

|Nombre|Descripción|
|---|---|
|200|OK|


## UpdateSubTask
Actualizar una subtarea: actualiza una subtarea específica.

```PATCH: /subtasks/{id}```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Description|
| ---|---|---|---|---|---|
|id|integer|yes|path|Ninguna|Identificador de la subtarea|
|post| |yes|body|Ninguna|Detalles de la subtarea|

#### Response

|Nombre|Descripción|
|---|---|
|200|OK|


## DeleteSubTask
Eliminar una subtarea: elimina una subtarea específica.

```DELETE: /subtasks/{id}```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Description|
| ---|---|---|---|---|---|
|id|integer|yes|path|Ninguna|Identificador de la subtarea|
|revision|integer|yes|query|Ninguna|Revisión|

#### Response

|Nombre|Description|
|---|---|
|204|Ningún contenido|


## GetNote
Obtener una nota: recupera una nota específica.

```GET: /notes/{id}```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Description|
| ---|---|---|---|---|---|
|id|string|yes|path|Ninguna|Identificador de la nota|

#### Response

|Nombre|Descripción|
|---|---|
|200|OK|


## UpdateNote
Actualizar una nota: actualiza una lista específica.

```PATCH: /notes/{id}```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Description|
| ---|---|---|---|---|---|
|id|integer|yes|path|Ninguna|Identificador de la nota|
|post| |yes|body|Ninguna|Detalles de la nota|

#### Response

|Nombre|Descripción|
|---|---|
|200|OK|


## DeleteNote
Eliminar una nota: elimina una nota específica.

```DELETE: /notes/{id}```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Description|
| ---|---|---|---|---|---|
|id|integer|yes|path|Ninguna|Identificador de la nota|
|revision|integer|yes|query|Ninguna|Revisión|

#### Response

|Nombre|Description|
|---|---|
|204|Ningún contenido|


## GetComment
Obtener comentario de tarea: recupera un comentario de tarea específico.

```GET: /task_comments/{id}```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Description|
| ---|---|---|---|---|---|
|id|string|yes|path|Ninguna|Identificador del comentario|

#### Response

|Nombre|Descripción|
|---|---|
|200|OK|


## UpdateReminder
Actualizar un recordatorio: actualiza un recordatorio específico.

```PATCH: /reminders/{id}```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Description|
| ---|---|---|---|---|---|
|id|integer|yes|path|Ninguna|Identificador del recordatorio|
|post| |yes|body|Ninguna|Detalles del recordatorio|

#### Response

|Nombre|Descripción|
|---|---|
|200|OK|


## DeleteReminder
Eliminar un recordatorio: elimina un recordatorio específico.

```DELETE: /reminders/{id}```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Description|
| ---|---|---|---|---|---|
|id|integer|yes|path|Ninguna|Identificador del recordatorio|
|revision|integer|yes|query|Ninguna|Revisión|

#### Response

|Nombre|Description|
|---|---|
|204|Ningún contenido|


## Definiciones de objeto 

### Enumerar


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|id|integer|No |
|created\_at|string|No |
|título|string|No |
|list\_type|string|No |
|type|string|No |
|revision|integer|No |



### CreatedList


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|id|integer|No |
|created\_at|string|No |
|título|string|No |
|revision|integer|No |
|type|string|No |



### Tarea


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|id|integer|No |
|assignee\_id|integer|No |
|assigner\_id|integer|No |
|created\_at|string|No |
|created\_by\_id|integer|No |
|due\_date|string|No |
|list\_id|integer|No |
|revision|integer|No |
|starred|boolean|No |
|título|string|No |



### Subtask


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|id|integer|No |
|task\_id|integer|No |
|created\_at|string|No |
|created\_by\_id|integer|No |
|revision|string|No |
|título|string|No |



### Nota:


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|id|integer|No |
|task\_id|integer|No |
|contenido|string|No |
|created\_at|string|No |
|updated\_at|string|No |
|revision|integer|No |



### Comentario


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|id|integer|No |
|task\_id|integer|No |
|revision|integer|No |
|text|string|No |
|type|string|No |
|created\_at|string|No |



### Reminder


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|id|integer|No |
|fecha|string|No |
|task\_id|integer|No |
|revision|integer|No |
|type|string|No |
|created\_at|string|No |
|updated\_at|string|No |



### CreatedReminder


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|id|integer|No |
|fecha|string|No |
|task\_id|integer|No |
|revision|integer|No |
|created\_at|string|No |
|updated\_at|string|No |



### Archivo


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|id|integer|No |
|url|string|No |
|task\_id|integer|No |
|list\_id|integer|No |
|user\_id|integer|No |
|file\_name|string|No |
|content\_type|string|No |
|file\_size|integer|No |
|local\_created\_at|string|No |
|created\_at|string|No |
|updated\_at|string|No |
|type|string|No |
|revision|integer|No |



### NewTask


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|list\_id|integer|Sí |
|título|string|Sí |
|assignee\_id|integer|No |
|completed|boolean|No |
|recurrence\_type|string|No |
|recurrence\_count|integer|No |
|due\_date|string|No |
|starred|boolean|No |



### NewList


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|título|string|Sí |



### NewSubtask


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|list\_id|integer|Sí |
|task\_id|integer|Sí |
|título|string|Sí |
|completed|boolean|No |



### NewNote


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|list\_id|integer|Sí |
|task\_id|integer|Sí |
|contenido|string|Sí |



### NewComment


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|list\_id|integer|Sí |
|task\_id|integer|Sí |
|text|string|Sí |



### NewReminder


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|list\_id|integer|Sí |
|task\_id|integer|Sí |
|fecha|string|Sí |



### UpdateTask


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|revision|integer|No |
|título|string|No |
|assignee\_id|integer|No |
|completed|boolean|No |
|recurrence\_type|string|No |
|recurrence\_count|integer|No |
|due\_date|string|No |
|starred|boolean|No |



### UpdateList


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|revision|integer|No |
|título|string|No |



### UpdateSubtask


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|revision|integer|No |
|título|string|No |
|completed|boolean|No |



### UpdateNote


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|revision|integer|No |
|contenido|string|No |



### UpdateReminder


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|fecha|string|No |
|revision|integer|No |


## Pasos siguientes
[Creación de una aplicación lógica](../app-service-logic/app-service-logic-create-a-logic-app.md)

<!---HONumber=AcomDC_0824_2016-->