<properties
pageTitle="ProjectOnline | Microsoft Azure"
description="Cree aplicaciones lógicas con el Servicio de aplicaciones de Azure. Project Online en es una solución en línea flexible para la administración de carteras de proyectos (PPM) y el trabajo diario de Microsoft. Ofrecida a través de Office 365, Project Online permite a las organizaciones empezar a trabajar rápidamente con grandes capacidades de administración de proyectos para planear, priorizar y administrar proyectos e inversiones de carteras de proyectos, desde prácticamente cualquier lugar en casi todos los dispositivos."
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
ms.date="05/16/2016"
ms.author="deonhe"/>

# Introducción al conector ProjectOnline

Project Online en es una solución en línea flexible para la administración de carteras de proyectos (PPM) y el trabajo diario de Microsoft. Ofrecida a través de Office 365, Project Online permite a las organizaciones empezar a trabajar rápidamente con grandes capacidades de administración de proyectos para planear, priorizar y administrar proyectos e inversiones de carteras de proyectos, desde prácticamente cualquier lugar en casi todos los dispositivos.

El conector ProjectOnline se puede usar desde:

- [Aplicaciones lógicas](../app-service-logic/app-service-logic-what-are-logic-apps.md)
- [PowerApps](http://powerapps.microsoft.com)
- [Flujo](http://flow.microsoft.com)

>[AZURE.NOTE] Esta versión del artículo se aplica a la versión de esquema 2015-08-01-preview de las aplicaciones lógicas.

Puede empezar creando una aplicación lógica ahora. Para ello, consulte [Crear una aplicación lógica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Desencadenadores y acciones

El conector ProjectOnline se puede usar como acción; tiene desencadenadores. Todos los conectores admiten datos en formato JSON y XML.

 El conector ProjectOnline tiene las siguientes acciones y desencadenadores disponibles:

### Acciones de ProjectOnline
Puede realizar estas acciones:

|Acción|Descripción|
|--- | ---|
|[ListProjects](connectors-create-api-projectonline.md#listprojects)|Enumera los proyectos de su sitio de proyectos en línea.|
|[CreateProject](connectors-create-api-projectonline.md#createproject)|Crea un proyecto nuevo en su sitio de proyectos en línea.|
|[CreateTask](connectors-create-api-projectonline.md#createtask)|Crea una tarea nueva en el proyecto.|
|[CreateResource](connectors-create-api-projectonline.md#createresource)|Crea un recurso empresarial en su sitio de proyectos en línea.|
|[ListTasks](connectors-create-api-projectonline.md#listtasks)|Enumera las tareas publicadas en un proyecto.|
|[CheckoutProject](connectors-create-api-projectonline.md#checkoutproject)|Desprotege un proyecto en el sitio.|
|[PublishProject](connectors-create-api-projectonline.md#publishproject)|Protege y publica un proyecto existente en el sitio.|
### Desencadenadores de ProjectOnline
Se pueden escuchar estos eventos:

|Desencadenador | Descripción|
|--- | ---|
|Cuando se crea un nuevo proyecto|Desencadena un flujo cada vez que se crea un nuevo proyecto.|
|Cuando se crea un nuevo recurso|Desencadena un nuevo flujo cada vez que se crea un nuevo recurso.|
|Cuando se crea una nueva tarea|Desencadena un nuevo flujo cada vez que se crea una nueva tarea.|


## Creación de una conexión a ProjectOnline
Para crear aplicaciones lógicas con ProjectOnline, primero debe crear una **conexión** y, a continuación, proporcionar los detalles de las siguientes propiedades:

|Propiedad| Obligatorio|Descripción|
| ---|---|---|
|Se necesita el cifrado de tokens|Sí|Proporciona las credenciales de ProjectOnline.|

>[AZURE.INCLUDE [Pasos para crear una conexión a ProjectOnline](../../includes/connectors-create-api-projectonline.md)]

>[AZURE.TIP] Puede usar esta conexión en otras aplicaciones lógicas.

## Referencia de ProjectOnline
Se aplica a la versión: 1.0

## OnNewProject
Cuando se crea un nuevo proyecto: desencadena un flujo cada vez que se crea un nuevo proyecto.

```GET: /trigger/_api/ProjectData/Projects```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|siteUrl|cadena|yes|query|Ninguna|URL del sitio raíz de su sitio de proyectos (ejemplo: https://sampletenant.sharepoint.com/teams/sampleteam )|

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


## OnNewResource
Cuando se crea un nuevo recurso: desencadena un nuevo flujo cada vez que se crea un nuevo recurso.

```GET: /trigger/_api/ProjectData/Resources```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|siteUrl|cadena|yes|query|Ninguna|URL del sitio raíz de su sitio de proyectos (ejemplo: https://sampletenant.sharepoint.com/teams/sampleteam )|

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


## OnNewTask
Cuando se crea una nueva tarea: desencadena un flujo cuando se crea una nueva tarea.

```GET: /trigger/_api/ProjectData/Tasks```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|siteUrl|cadena|yes|query|Ninguna|URL del sitio raíz de su sitio de proyectos (ejemplo: https://sampletenant.sharepoint.com/teams/sampleteam )|

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


## ListProjects
Enumerar proyectos: enumera los proyectos de su sitio de proyectos en línea.

```GET: /_api/ProjectServer/Projects```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|siteUrl|cadena|yes|query|Ninguna|URL del sitio raíz de su sitio de proyectos (ejemplo: https://sampletenant.sharepoint.com/teams/sampleteam )|

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


## CreateProject
Crear nuevo proyecto: crea un nuevo proyecto en su sitio de proyectos en línea.

```POST: /_api/ProjectServer/Projects```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|siteUrl|cadena|yes|query|Ninguna|URL del sitio raíz de su sitio de proyectos (ejemplo: https://sampletenant.sharepoint.com/teams/sampleteam )|
|proj| |yes|body|Ninguna|Nuevo proyecto que se creará|

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


## CreateTask
Crear nueva tarea: crea una tarea nueva en el proyecto.

```POST: /_api/ProjectServer/Projects('{project_id}')/Draft/Tasks/Add```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|siteUrl|cadena|yes|query|Ninguna|URL del sitio raíz de su sitio de proyectos (ejemplo: https://sampletenant.sharepoint.com/teams/sampleteam )|
|project\_id|cadena|yes|path|Ninguna|Identificador único del proyecto al que se agregará la tarea|
|task| |yes|body|Ninguna|Nueva tarea que se agregará al proyecto|

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


## CreateResource
Crear nuevo recurso: crea un recurso empresarial en su sitio de proyectos en línea.

```POST: /_api/ProjectServer/EnterpriseResources```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|siteUrl|cadena|yes|query|Ninguna|URL del sitio raíz de su sitio de proyectos (ejemplo: https://sampletenant.sharepoint.com/teams/sampleteam )|
|resource| |yes|body|Ninguna|Nuevo recurso empresarial que se agregará al proyecto|

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


## ListTasks
Enumerar tareas: enumera las tareas publicadas en un proyecto.

```GET: /_api/ProjectServer/Projects('{project_id}')/Tasks```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|siteUrl|cadena|yes|query|Ninguna|URL del sitio raíz de su sitio de proyectos (ejemplo: https://sampletenant.sharepoint.com/teams/sampleteam )|
|project\_id|cadena|yes|path|Ninguna|Identificador único del proyecto para obtener tareas|

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


## CheckoutProject
Desproteger un proyecto: desprotege un proyecto del sitio.

```POST: /_api/ProjectServer/Projects('{project_id}')/checkOut```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|siteUrl|cadena|yes|query|Ninguna|URL del sitio raíz de su sitio de proyectos (ejemplo: https://sampletenant.sharepoint.com/teams/sampleteam )|
|project\_id|cadena|yes|path|Ninguna|Identificador único del proyecto al que se agregará la tarea|

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


## PublishProject
Proteger y publicar proyecto: protege y publica un proyecto existente en el sitio.

```POST: /_api/ProjectServer/Projects('{project_id}')/Draft/Publish(true)```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|siteUrl|cadena|yes|query|Ninguna|URL del sitio raíz de su sitio de proyectos (ejemplo: https://sampletenant.sharepoint.com/teams/sampleteam )|
|project\_id|cadena|yes|path|Ninguna|Identificador único del proyecto que se protegerá|

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


## Definiciones de objeto 

### TriggerProjectsWrapper


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|value|array|No |



### TriggerProject


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|ProjectStartDate|cadena|No |
|ProjectFinishDate|cadena|No |
|ProjectCreatedDate|cadena|No |
|ProjectId|cadena|No |
|ProjectModifiedDate|cadena|No |
|ProjectType|integer|No |
|ProjectName|cadena|No |



### TriggerResourcesWrapper


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|value|array|No |



### TriggerResource


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|ResourceId|cadena|No |
|ResourceBaseCalendar|cadena|No |
|ResourceBookingType|integer|No |
|ResourceCanLevel|boolean|No |
|ResourceCostPerUse|número|No |
|ResourceCreatedDate|cadena|No |
|ResourceEarliestAvailableFrom|cadena|No |
|ResourceEmail|cadena|No |
|ResourceInitials|cadena|No |
|ResourceIsActive|boolean|No |
|ResourceIsGeneric|boolean|No |
|ResourceLatestAvailableTo|cadena|No |
|ResourceModifiedDate|cadena|No |
|ResourceName|cadena|No |
|ResourceStatsuName|cadena|No |
|ResourceType|integer|No |
|TypeDescription|cadena|No |
|TypeName|cadena|No |



### TriggerTasksWrapper


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|value|array|No |



### TriggerTask


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|ProjectId|cadena|No |
|TaskId|cadena|No |
|ProjectName|cadena|No |
|TaskName|cadena|No |
|TaskCreatedDate|cadena|No |
|TaskModifieddate|cadena|No |
|TaskStartDate|cadena|No |
|TaskFinishDate|cadena|No |
|TaskPriority|integer|No |
|TaskIsActive|boolean|No |



### NuevoProyecto


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|Nombre|cadena|Sí |
|Descripción|cadena|No |
|Iniciar|cadena|No |



### NewResource


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|Nombre|cadena|Sí |
|IsBudget|boolean|No |
|IsGeneric|boolean|No |
|IsInactive|boolean|No |



### proyecto


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|ApprovedStart|cadena|No |
|ApprovedEnd|cadena|No |
|CheckedOutDate|cadena|No |
|CheckOutDescription|cadena|No |
|CheckOutId|cadena|No |
|CreatedDate|cadena|No |
|Id|cadena|No |
|IsCheckedOut|boolean|No |
|LastPublishedDate|cadena|No |
|LastSavedDate|cadena|No |
|OptimizerDecision|integer|No |
|PlannerDecision|integer|No |
|ProjectType|integer|No |
|Nombre|cadena|No |
|WinprojVersion|cadena|No |



### ProjectsWrapper


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|value|array|No |



### NewTask


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|parameters|not defined|Sí |



### TaskParameters


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|Nombre|cadena|Sí |
|Notas|cadena|No |
|Iniciar|cadena|No |
|Duración|cadena|No |



### EnterpriseResource


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|CanLevel|boolean|No |
|Código|cadena|No |
|CostAccrual|integer|No |
|CostCenter|cadena|No |
|Creado|cadena|No |
|DefaultBookingType|integer|No |
|Email|cadena|No |
|ExternalId|cadena|No |
|Grupo|cadena|No |
|HireDate|cadena|No |
|Id|cadena|No |
|Initials|cadena|No |
|IsActive|boolean|No |
|IsBudget|boolean|No |
|IsCheckedOut|boolean|No |
|IsGeneric|boolean|No |
|IsTeam|boolean|No |
|MaterialLabel|cadena|No |
|Modified|cadena|No |
|Nombre|cadena|No |
|Phonetics|cadena|No |
|ResourceType|integer|No |
|TerminationDate|cadena|No |



### TasksWrapper


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|value|array|No |



### Tarea


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|Creado|cadena|No |
|Modified|cadena|No |
|Iniciar|cadena|No |
|Finalización|cadena|No |
|Nombre|cadena|No |
|Id|cadena|No |
|Prioridad|integer|No |
|PercentComplete|integer|No |
|Notas|cadena|No |
|Contacto|cadena|No |


## Pasos siguientes
[Creación de una aplicación lógica](../app-service-logic/app-service-logic-create-a-logic-app.md)

<!---HONumber=AcomDC_0727_2016-->