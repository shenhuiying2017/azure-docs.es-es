---
title: Wunderlist | Microsoft Docs
description: "Cree aplicaciones lógicas con el Servicio de aplicaciones de Azure. Wunderlist proporciona una lista de tareas pendientes y un administrador de tareas para ayudar a los usuarios a hacer su trabajo.  Ya sea que comparta una lista de la compra con un ser querido, trabaje en un proyecto o planee unas vacaciones, capturar, compartir y completar tareas pendientes con Wunderlist es sencillo. Wunderlist sincroniza de forma instantánea su teléfono, tableta o PC para que pueda tener acceso a todas las tareas desde cualquier lugar."
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: e4773ecf-3ad3-44b4-a1b5-ee5f58baeadd
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/18/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 7a528a47373d7e9ae2cdc7d62328ed8e83112863


---
# <a name="get-started-with-the-wunderlist-connector"></a>Introducción al conector Wunderlist
Wunderlist proporciona una lista de tareas pendientes y un administrador de tareas para ayudar a los usuarios a hacer su trabajo.  Ya sea que comparta una lista de la compra con un ser querido, trabaje en un proyecto o planee unas vacaciones, capturar, compartir y completar tareas pendientes con Wunderlist es sencillo. Wunderlist sincroniza de forma instantánea su teléfono, tableta o PC para que pueda tener acceso a todas las tareas desde cualquier lugar.

> [!NOTE]
> Esta versión del artículo se aplica a la versión de esquema 2015-08-01-preview de aplicaciones lógicas.
> 
> 

Puede empezar creando una aplicación lógica ahora. Para ello, consulte [Creación de una nueva aplicación lógica mediante la conexión de servicios de SaaS](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Desencadenadores y acciones
El conector Wunderlist se puede usar como acción; tiene desencadenadores. Todos los conectores admiten datos en formato JSON y XML.

 El conector Wunderlist tiene las siguientes acciones y desencadenadores disponibles:

### <a name="wunderlist-actions"></a>Acciones de Wunderlist
Puede realizar estas acciones:

| Acción | Descripción |
| --- | --- |
| [RetrieveLists](connectors-create-api-wunderlist.md#retrievelists) |Recupera las listas asociadas a su cuenta. |
| [CreateList](connectors-create-api-wunderlist.md#createlist) |Crea una lista. |
| [ListTasks](connectors-create-api-wunderlist.md#listtasks) |Recupera las tareas de una lista específica. |
| [CreateTask](connectors-create-api-wunderlist.md#createtask) |Crea una tarea. |
| [ListSubTasks](connectors-create-api-wunderlist.md#listsubtasks) |Recupera las subtareas de una lista o tarea específica. |
| [CreateSubTask](connectors-create-api-wunderlist.md#createsubtask) |Crear una subtarea dentro de una tarea específica. |
| [ListNotes](connectors-create-api-wunderlist.md#listnotes) |Recupera las notas de una lista o tarea específica. |
| [CreateNote](connectors-create-api-wunderlist.md#createnote) |Agrega una nota a una tarea específica. |
| [ListComments](connectors-create-api-wunderlist.md#listcomments) |Recupera los comentarios de tarea de una lista o tarea específica. |
| [CreateComment](connectors-create-api-wunderlist.md#createcomment) |Agrega un comentario a una tarea específica. |
| [RetrieveReminders](connectors-create-api-wunderlist.md#retrievereminders) |Recupera los recordatorios de una lista o tarea específica. |
| [CreateReminder](connectors-create-api-wunderlist.md#createreminder) |Establece un recordatorio. |
| [RetrieveFiles](connectors-create-api-wunderlist.md#retrievefiles) |Recupera los archivos de una lista o tarea específica. |
| [GetList](connectors-create-api-wunderlist.md#getlist) |Recupera una lista específica. |
| [DeleteList](connectors-create-api-wunderlist.md#deletelist) |Elimina una lista. |
| [UpdateList](connectors-create-api-wunderlist.md#updatelist) |Actualiza una lista específica. |
| [GetTask](connectors-create-api-wunderlist.md#gettask) |Recupera una tarea específica. |
| [UpdateTask](connectors-create-api-wunderlist.md#updatetask) |Actualiza una tarea específica. |
| [DeleteTask](connectors-create-api-wunderlist.md#deletetask) |Elimina una tarea específica. |
| [GetSubTask](connectors-create-api-wunderlist.md#getsubtask) |Recupera una subtarea específica. |
| [UpdateSubTask](connectors-create-api-wunderlist.md#updatesubtask) |Actualiza una subtarea específica. |
| [DeleteSubTask](connectors-create-api-wunderlist.md#deletesubtask) |Elimina una subtarea específica. |
| [GetNote](connectors-create-api-wunderlist.md#getnote) |Recupera una nota específica. |
| [UpdateNote](connectors-create-api-wunderlist.md#updatenote) |Actualiza una nota específica. |
| [DeleteNote](connectors-create-api-wunderlist.md#deletenote) |Elimina una nota específica. |
| [GetComment](connectors-create-api-wunderlist.md#getcomment) |Recupera un comentario de tarea específico. |
| [UpdateReminder](connectors-create-api-wunderlist.md#updatereminder) |Actualiza un recordatorio específico. |
| [DeleteReminder](connectors-create-api-wunderlist.md#deletereminder) |Elimina un recordatorio específico. |

### <a name="wunderlist-triggers"></a>Desencadenadores de Wunderlist
Se pueden escuchar estos eventos:

| Desencadenador | Description |
| --- | --- |
| Cuando vence una tarea |Desencadena un nuevo flujo cuando vence una tarea de la lista. |
| Cuando se crea una nueva tarea |Desencadena un nuevo flujo cuando se crea una nueva tarea en la lista. |
| Cuando se produce un recordatorio |Desencadena un nuevo flujo cuando se produce un recordatorio |

## <a name="create-a-connection-to-wunderlist"></a>Creación de una conexión a Wunderlist
Para crear aplicaciones lógicas con Wunderlist, primero necesita establecer una **conexión** y, después, especificar los detalles de las siguientes propiedades:

| Propiedad | Obligatorio | Descripción |
| --- | --- | --- |
| Se necesita el cifrado de tokens |Sí |Proporciona las credenciales de Wunderlist. |

Después de crear la conexión, puede usarla para ejecutar las acciones y escuchar los desencadenadores descritos en este artículo.

> [!INCLUDE [Steps to create a connection to Wunderlist](../../includes/connectors-create-api-wunderlist.md)]
> 
> [!TIP]
> Puede usar esta conexión en otras aplicaciones lógicas.
> 
> 

## <a name="reference-for-wunderlist"></a>Referencia de Wunderlist
Se aplica a la versión: 1.0

## <a name="triggertaskdue"></a>TriggerTaskDue
Cuando vence una tarea: desencadena un nuevo flujo cuando vence una tarea de la lista.

```GET: /trigger/tasksdue```

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Description |
| --- | --- | --- | --- | --- | --- |
| list_id |integer |yes |query |Ninguna |Identificador de la lista |

#### <a name="response"></a>Response
| Nombre | Descripción |
| --- | --- |
| 200 |Operación correcta |

## <a name="triggertasknew"></a>TriggerTaskNew
Cuando se crea una nueva tarea: desencadena un nuevo flujo cuando se crea una nueva tarea en la lista.

```GET: /trigger/tasksnew```

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Description |
| --- | --- | --- | --- | --- | --- |
| list_id |integer |yes |query |Ninguna |Identificador de la lista |

#### <a name="response"></a>Response
| Nombre | Descripción |
| --- | --- |
| 200 |Operación correcta |

## <a name="triggerreminder"></a>TriggerReminder
Cuando se produce un recordatorio: desencadena un nuevo flujo cuando se produce un recordatorio.

```GET: /trigger/reminders```

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Description |
| --- | --- | --- | --- | --- | --- |
| list_id |integer |yes |query |Ninguna |Identificador de la lista |
| task_id |integer |no |query |Ninguna |Identificador de la tarea |

#### <a name="response"></a>Response
| Nombre | Descripción |
| --- | --- |
| 200 |Operación correcta |

## <a name="retrievelists"></a>RetrieveLists
Obtener listas: recupera las listas asociadas a su cuenta.

```GET: /lists```

#### <a name="response"></a>Response
| Nombre | Descripción |
| --- | --- |
| 200 |Operación correcta |
| 400 |Bad Request |
| 500 |Error interno del servidor. Error desconocido |
| default |Error en la operación. |

## <a name="createlist"></a>CreateList
Crear una lista: crea una lista.

```POST: /lists```

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Descripción |
| --- | --- | --- | --- | --- | --- |
| post | |yes |body |Ninguna |Nueva lista que se creará |

#### <a name="response"></a>Response
| Nombre | Descripción |
| --- | --- |
| 200 |Operación correcta |
| default |Error en la operación. |

## <a name="listtasks"></a>ListTasks
Obtener tareas: recupera las tareas de una lista específica.

```GET: /tasks```

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Description |
| --- | --- | --- | --- | --- | --- |
| list_id |integer |yes |query |Ninguna |Identificador de la lista |
| completed |boolean |no |query |Ninguna |Completed |

#### <a name="response"></a>Response
| Nombre | Descripción |
| --- | --- |
| 200 |Operación correcta |
| 400 |Bad Request |
| 500 |Error interno del servidor. Error desconocido |
| default |Error en la operación. |

## <a name="createtask"></a>CreateTask
Crear una tarea: crea una tarea.

```POST: /tasks```

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Descripción |
| --- | --- | --- | --- | --- | --- |
| post | |yes |body |Ninguna |Nueva tarea que se creará |

#### <a name="response"></a>Response
| Nombre | Descripción |
| --- | --- |
| 201 |Creado |

## <a name="listsubtasks"></a>ListSubTasks
Obtener subtareas: recupera las subtareas de una lista o tarea específica.

```GET: /subtasks```

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Description |
| --- | --- | --- | --- | --- | --- |
| list_id |integer |yes |query |Ninguna |Identificador de la lista |
| task_id |integer |no |query |Ninguna |Identificador de la tarea |
| completed |boolean |no |query |Ninguna |Completed |

#### <a name="response"></a>Response
| Nombre | Descripción |
| --- | --- |
| 200 |Operación correcta |
| 400 |Bad Request |
| 500 |Error interno del servidor. Error desconocido |
| default |Error en la operación. |

## <a name="createsubtask"></a>CreateSubTask
Crear una subtarea: crea una subtarea dentro de una tarea específica.

```POST: /subtasks```

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Descripción |
| --- | --- | --- | --- | --- | --- |
| post | |yes |body |Ninguna |Nueva subtarea que se creará |

#### <a name="response"></a>Response
| Nombre | Descripción |
| --- | --- |
| 201 |Creado |

## <a name="listnotes"></a>ListNotes
Obtener notas: recupera las notas de una lista o tarea específica.

```GET: /notes```

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Description |
| --- | --- | --- | --- | --- | --- |
| list_id |integer |yes |query |Ninguna |Identificador de la lista |
| task_id |integer |no |query |Ninguna |Identificador de la tarea |

#### <a name="response"></a>Response
| Nombre | Descripción |
| --- | --- |
| 200 |Operación correcta |
| 400 |Bad Request |
| 500 |Error interno del servidor. Error desconocido |
| default |Error en la operación. |

## <a name="createnote"></a>CreateNote
Crear una nota: agrega una nota a una tarea específica.

```POST: /notes```

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Descripción |
| --- | --- | --- | --- | --- | --- |
| post | |yes |body |Ninguna |Nueva nota que se creará |

#### <a name="response"></a>Response
| Nombre | Descripción |
| --- | --- |
| 201 |Creado |

## <a name="listcomments"></a>ListComments
Obtener comentarios de tarea: recupera los comentarios de tarea de una lista o tarea específica.

```GET: /task_comments```

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Description |
| --- | --- | --- | --- | --- | --- |
| list_id |integer |yes |query |Ninguna |Identificador de la lista |
| task_id |integer |no |query |Ninguna |Identificador de la tarea |

#### <a name="response"></a>Response
| Nombre | Descripción |
| --- | --- |
| 200 |Operación correcta |
| 400 |Bad Request |
| 500 |Error interno del servidor. Error desconocido |
| default |Error en la operación. |

## <a name="createcomment"></a>CreateComment
Agregar un comentario a una tarea: agrega un comentario a una tarea específica.

```POST: /task_comments```

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Descripción |
| --- | --- | --- | --- | --- | --- |
| post | |yes |body |Ninguna |Nuevo comentario de tarea que se creará |

#### <a name="response"></a>Response
| Nombre | Descripción |
| --- | --- |
| 201 |Creado |

## <a name="retrievereminders"></a>RetrieveReminders
Obtener recordatorios: recupera los recordatorios de una lista o tarea específica.

```GET: /reminders```

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Description |
| --- | --- | --- | --- | --- | --- |
| list_id |integer |yes |query |Ninguna |Identificador de la lista |
| task_id |integer |no |query |Ninguna |Identificador de la tarea |

#### <a name="response"></a>Response
| Nombre | Descripción |
| --- | --- |
| 200 |Operación correcta |
| 400 |Bad Request |
| 500 |Error interno del servidor. Error desconocido |
| default |Error en la operación. |

## <a name="createreminder"></a>CreateReminder
Establecer un recordatorio: establece un recordatorio.

```POST: /reminders```

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Descripción |
| --- | --- | --- | --- | --- | --- |
| post | |yes |body |Ninguna |Nuevo recordatorio que se creará |

#### <a name="response"></a>Response
| Nombre | Descripción |
| --- | --- |
| 200 |Operación correcta |
| default |Error en la operación. |

## <a name="retrievefiles"></a>RetrieveFiles
Obtener archivos: recupera los archivos de una lista o tarea específica.

```GET: /files```

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Description |
| --- | --- | --- | --- | --- | --- |
| list_id |integer |yes |query |Ninguna |Identificador de la lista |
| task_id |integer |no |query |Ninguna |Identificador de la tarea |

#### <a name="response"></a>Response
| Nombre | Descripción |
| --- | --- |
| 200 |Operación correcta |
| 400 |Bad Request |
| 500 |Error interno del servidor. Error desconocido |
| default |Error en la operación. |

## <a name="getlist"></a>GetList
Obtener fila: recupera una lista específica.

```GET: /lists/{id}```

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Descripción |
| --- | --- | --- | --- | --- | --- |
| id |cadena |Sí |path |Ninguna |Identificador de la lista |

#### <a name="response"></a>Response
| Nombre | Descripción |
| --- | --- |
| 200 |OK |

## <a name="deletelist"></a>DeleteList
Eliminar lista: elimina una lista.

```DELETE: /lists/{id}```

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Descripción |
| --- | --- | --- | --- | --- | --- |
| id |integer |Sí |path |Ninguna |Identificador de la lista |
| revision |integer |yes |query |Ninguna |Revisión |

#### <a name="response"></a>Response
| Nombre | Description |
| --- | --- |
| 204 |Ningún contenido |

## <a name="updatelist"></a>UpdateList
Actualizar una lista: actualiza una lista específica.

```PATCH: /lists/{id}```

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Descripción |
| --- | --- | --- | --- | --- | --- |
| id |integer |Sí |path |Ninguna |Identificador de la lista |
| post | |yes |body |Ninguna |Detalles de la lista |

#### <a name="response"></a>Response
| Nombre | Descripción |
| --- | --- |
| 200 |OK |

## <a name="gettask"></a>GetTask
Obtener tarea: recupera una tarea específica.

```GET: /tasks/{id}```

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Description |
| --- | --- | --- | --- | --- | --- |
| list_id |integer |yes |query |Ninguna |Identificador de la lista |
| id |integer |Sí |path |Ninguna |Identificador de la tarea |

#### <a name="response"></a>Response
| Nombre | Descripción |
| --- | --- |
| 200 |OK |

## <a name="updatetask"></a>UpdateTask
Actualizar una tarea: actualiza una tarea específica.

```PATCH: /tasks/{id}```

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Description |
| --- | --- | --- | --- | --- | --- |
| list_id |integer |yes |query |Ninguna |Identificador de la lista |
| id |integer |Sí |path |Ninguna |Identificador de la tarea |
| post | |yes |body |Ninguna |Detalles de la tarea |

#### <a name="response"></a>Response
| Nombre | Descripción |
| --- | --- |
| 200 |OK |

## <a name="deletetask"></a>DeleteTask
Eliminar tarea: elimina una tarea específica.

```DELETE: /tasks/{id}```

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Description |
| --- | --- | --- | --- | --- | --- |
| list_id |integer |yes |query |Ninguna |Identificador de la lista |
| id |integer |Sí |path |Ninguna |Identificador de la tarea |
| revision |integer |yes |query |Ninguna |Revisión |

#### <a name="response"></a>Response
| Nombre | Description |
| --- | --- |
| 204 |Ningún contenido |

## <a name="getsubtask"></a>GetSubTask
Obtener subtarea: recupera una subtarea específica.

```GET: /subtasks/{id}```

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Descripción |
| --- | --- | --- | --- | --- | --- |
| id |cadena |Sí |path |Ninguna |Identificador de la subtarea |

#### <a name="response"></a>Response
| Nombre | Descripción |
| --- | --- |
| 200 |OK |

## <a name="updatesubtask"></a>UpdateSubTask
Actualizar una subtarea: actualiza una subtarea específica.

```PATCH: /subtasks/{id}```

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Descripción |
| --- | --- | --- | --- | --- | --- |
| id |integer |Sí |path |Ninguna |Identificador de la subtarea |
| post | |yes |body |Ninguna |Detalles de la subtarea |

#### <a name="response"></a>Response
| Nombre | Descripción |
| --- | --- |
| 200 |OK |

## <a name="deletesubtask"></a>DeleteSubTask
Eliminar una subtarea: elimina una subtarea específica.

```DELETE: /subtasks/{id}```

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Descripción |
| --- | --- | --- | --- | --- | --- |
| id |integer |Sí |path |Ninguna |Identificador de la subtarea |
| revision |integer |yes |query |Ninguna |Revisión |

#### <a name="response"></a>Response
| Nombre | Description |
| --- | --- |
| 204 |Ningún contenido |

## <a name="getnote"></a>GetNote
Obtener una nota: recupera una nota específica.

```GET: /notes/{id}```

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Descripción |
| --- | --- | --- | --- | --- | --- |
| id |cadena |Sí |path |Ninguna |Identificador de la nota |

#### <a name="response"></a>Response
| Nombre | Descripción |
| --- | --- |
| 200 |OK |

## <a name="updatenote"></a>UpdateNote
Actualizar una nota: actualiza una lista específica.

```PATCH: /notes/{id}```

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Descripción |
| --- | --- | --- | --- | --- | --- |
| id |integer |Sí |path |Ninguna |Identificador de la nota |
| post | |yes |body |Ninguna |Detalles de la nota |

#### <a name="response"></a>Response
| Nombre | Descripción |
| --- | --- |
| 200 |OK |

## <a name="deletenote"></a>DeleteNote
Eliminar una nota: elimina una nota específica.

```DELETE: /notes/{id}```

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Descripción |
| --- | --- | --- | --- | --- | --- |
| id |integer |Sí |path |Ninguna |Identificador de la nota |
| revision |integer |yes |query |Ninguna |Revisión |

#### <a name="response"></a>Response
| Nombre | Description |
| --- | --- |
| 204 |Ningún contenido |

## <a name="getcomment"></a>GetComment
Obtener comentario de tarea: recupera un comentario de tarea específico.

```GET: /task_comments/{id}```

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Descripción |
| --- | --- | --- | --- | --- | --- |
| id |cadena |Sí |path |Ninguna |Identificador del comentario |

#### <a name="response"></a>Response
| Nombre | Descripción |
| --- | --- |
| 200 |OK |

## <a name="updatereminder"></a>UpdateReminder
Actualizar un recordatorio: actualiza un recordatorio específico.

```PATCH: /reminders/{id}```

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Descripción |
| --- | --- | --- | --- | --- | --- |
| id |integer |Sí |path |Ninguna |Identificador del recordatorio |
| post | |yes |body |Ninguna |Detalles del recordatorio |

#### <a name="response"></a>Response
| Nombre | Descripción |
| --- | --- |
| 200 |OK |

## <a name="deletereminder"></a>DeleteReminder
Eliminar un recordatorio: elimina un recordatorio específico.

```DELETE: /reminders/{id}```

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Descripción |
| --- | --- | --- | --- | --- | --- |
| id |integer |Sí |path |Ninguna |Identificador del recordatorio |
| revision |integer |yes |query |Ninguna |Revisión |

#### <a name="response"></a>Response
| Nombre | Description |
| --- | --- |
| 204 |Ningún contenido |

## <a name="object-definitions"></a>Definiciones de objeto
### <a name="list"></a>Enumerar
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| id |integer |No |
| created_at |string |No |
| título |string |No |
| list_type |string |No |
| type |string |No |
| revision |integer |No |

### <a name="createdlist"></a>CreatedList
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| id |integer |No |
| created_at |string |No |
| título |string |No |
| revision |integer |No |
| type |string |No |

### <a name="task"></a>Tarea
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| id |integer |No |
| assignee_id |integer |No |
| assigner_id |integer |No |
| created_at |string |No |
| created_by_id |integer |No |
| due_date |string |No |
| list_id |integer |No |
| revision |integer |No |
| starred |boolean |No |
| título |string |No |

### <a name="subtask"></a>Subtask
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| id |integer |No |
| task_id |integer |No |
| created_at |string |No |
| created_by_id |integer |No |
| revision |string |No |
| título |string |No |

### <a name="note"></a>Nota:
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| id |integer |No |
| task_id |integer |No |
| contenido |string |No |
| created_at |string |No |
| updated_at |string |No |
| revision |integer |No |

### <a name="comment"></a>Comentario
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| id |integer |No |
| task_id |integer |No |
| revision |integer |No |
| text |string |No |
| type |string |No |
| created_at |string |No |

### <a name="reminder"></a>Reminder
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| id |integer |No |
| fecha |string |No |
| task_id |integer |No |
| revision |integer |No |
| type |string |No |
| created_at |string |No |
| updated_at |string |No |

### <a name="createdreminder"></a>CreatedReminder
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| id |integer |No |
| fecha |string |No |
| task_id |integer |No |
| revision |integer |No |
| created_at |string |No |
| updated_at |string |No |

### <a name="file"></a>Archivo
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| id |integer |No |
| url |string |No |
| task_id |integer |No |
| list_id |integer |No |
| user_id |integer |No |
| file_name |string |No |
| content_type |string |No |
| file_size |integer |No |
| local_created_at |string |No |
| created_at |string |No |
| updated_at |string |No |
| type |string |No |
| revision |integer |No |

### <a name="newtask"></a>NewTask
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| list_id |integer |Sí |
| título |string |Sí |
| assignee_id |integer |No |
| completed |boolean |No |
| recurrence_type |string |No |
| recurrence_count |integer |No |
| due_date |string |No |
| starred |boolean |No |

### <a name="newlist"></a>NewList
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| título |string |Sí |

### <a name="newsubtask"></a>NewSubtask
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| list_id |integer |Sí |
| task_id |integer |Sí |
| título |string |Sí |
| completed |boolean |No |

### <a name="newnote"></a>NewNote
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| list_id |integer |Sí |
| task_id |integer |Sí |
| contenido |string |Sí |

### <a name="newcomment"></a>NewComment
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| list_id |integer |Sí |
| task_id |integer |Sí |
| text |string |Sí |

### <a name="newreminder"></a>NewReminder
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| list_id |integer |Sí |
| task_id |integer |Sí |
| fecha |string |Sí |

### <a name="updatetask"></a>UpdateTask
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| revision |integer |No |
| título |string |No |
| assignee_id |integer |No |
| completed |boolean |No |
| recurrence_type |string |No |
| recurrence_count |integer |No |
| due_date |string |No |
| starred |boolean |No |

### <a name="updatelist"></a>UpdateList
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| revision |integer |No |
| título |string |No |

### <a name="updatesubtask"></a>UpdateSubtask
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| revision |integer |No |
| título |string |No |
| completed |boolean |No |

### <a name="updatenote"></a>UpdateNote
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| revision |integer |No |
| contenido |string |No |

### <a name="updatereminder"></a>UpdateReminder
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| fecha |string |No |
| revision |integer |No |

## <a name="next-steps"></a>Pasos siguientes
[Creación de una aplicación lógica](../app-service-logic/app-service-logic-create-a-logic-app.md)




<!--HONumber=Nov16_HO3-->


