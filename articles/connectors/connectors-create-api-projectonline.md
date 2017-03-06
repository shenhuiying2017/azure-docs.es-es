---
title: ProjectOnline | Microsoft Docs
description: "Cree aplicaciones lógicas con el Servicio de aplicaciones de Azure. Project Online en es una solución en línea flexible para la administración de carteras de proyectos (PPM) y el trabajo diario de Microsoft. Ofrecida a través de Office 365, Project Online permite a las organizaciones empezar a trabajar rápidamente con grandes capacidades de administración de proyectos para planear, priorizar y administrar proyectos e inversiones de carteras de proyectos, desde prácticamente cualquier lugar en casi todos los dispositivos."
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: 40ce621e-4925-4653-93bb-71ab9abcbdf1
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/18/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: b92f954680603891ced503a1134791312b5214f0
ms.openlocfilehash: 77e106d9170fc49d66d322bd9c92bf0e14869259
ms.lasthandoff: 01/20/2017


---
# <a name="get-started-with-the-projectonline-connector"></a>Introducción al conector ProjectOnline
Project Online en es una solución en línea flexible para la administración de carteras de proyectos (PPM) y el trabajo diario de Microsoft. Ofrecida a través de Office 365, Project Online permite a las organizaciones empezar a trabajar rápidamente con grandes capacidades de administración de proyectos para planear, priorizar y administrar proyectos e inversiones de carteras de proyectos, desde prácticamente cualquier lugar en casi todos los dispositivos.

> [!NOTE]
> Esta versión del artículo se aplica a la versión de esquema 2015-08-01-preview de aplicaciones lógicas. 
> 
> 

Puede empezar creando una aplicación lógica ahora. Para ello, consulte [Creación de una nueva aplicación lógica mediante la conexión de servicios de SaaS](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Desencadenadores y acciones
El conector ProjectOnline se puede usar como acción; tiene desencadenadores. Todos los conectores admiten datos en formato JSON y XML. 

 El conector ProjectOnline tiene las siguientes acciones y desencadenadores disponibles:

### <a name="projectonline-actions"></a>Acciones de ProjectOnline
Puede realizar estas acciones:

| Acción | Descripción |
| --- | --- |
| [ListProjects](connectors-create-api-projectonline.md#listprojects) |Enumera los proyectos de su sitio de proyectos en línea. |
| [CreateProject](connectors-create-api-projectonline.md#createproject) |Crea un proyecto nuevo en su sitio de proyectos en línea. |
| [CreateTask](connectors-create-api-projectonline.md#createtask) |Crea una tarea nueva en el proyecto. |
| [CreateResource](connectors-create-api-projectonline.md#createresource) |Crea un recurso empresarial en su sitio de proyectos en línea. |
| [ListTasks](connectors-create-api-projectonline.md#listtasks) |Enumera las tareas publicadas en un proyecto. |
| [CheckoutProject](connectors-create-api-projectonline.md#checkoutproject) |Desprotege un proyecto en el sitio. |
| [PublishProject](connectors-create-api-projectonline.md#publishproject) |Protege y publica un proyecto existente en el sitio. |

### <a name="projectonline-triggers"></a>Desencadenadores de ProjectOnline
Se pueden escuchar estos eventos:

| Desencadenador | Description |
| --- | --- |
| Cuando se crea un nuevo proyecto |Desencadena un flujo cada vez que se crea un nuevo proyecto. |
| Cuando se crea un nuevo recurso |Desencadena un nuevo flujo cada vez que se crea un nuevo recurso. |
| Cuando se crea una nueva tarea |Desencadena un nuevo flujo cada vez que se crea una nueva tarea. |

## <a name="create-a-connection-to-projectonline"></a>Creación de una conexión a ProjectOnline
Para crear aplicaciones lógicas con ProjectOnline, primero debe crear una **conexión** y, después, facilitar los detalles de las siguientes propiedades: 

| Propiedad | Obligatorio | Descripción |
| --- | --- | --- |
| Se necesita el cifrado de tokens |Sí |Proporciona las credenciales de ProjectOnline. |

> [!INCLUDE [Steps to create a connection to ProjectOnline](../../includes/connectors-create-api-projectonline.md)]
> 
> [!TIP]
> Puede usar esta conexión en otras aplicaciones lógicas.
> 
> 

## <a name="reference-for-projectonline"></a>Referencia de ProjectOnline
Se aplica a la versión: 1.0

## <a name="onnewproject"></a>OnNewProject
Cuando se crea un nuevo proyecto: desencadena un flujo cada vez que se crea un nuevo proyecto. 

```GET: /trigger/_api/ProjectData/Projects``` 

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Description |
| --- | --- | --- | --- | --- | --- |
| siteUrl |string |yes |query |Ninguna |Dirección URL del sitio raíz del sitio de proyecto (ejemplo: https://sampletenant.sharepoint.com/teams/sampleteam) |

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

## <a name="onnewresource"></a>OnNewResource
Cuando se crea un nuevo recurso: desencadena un nuevo flujo cada vez que se crea un nuevo recurso. 

```GET: /trigger/_api/ProjectData/Resources``` 

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Description |
| --- | --- | --- | --- | --- | --- |
| siteUrl |string |yes |query |Ninguna |Dirección URL del sitio raíz del sitio de proyecto (ejemplo: https://sampletenant.sharepoint.com/teams/sampleteam) |

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

## <a name="onnewtask"></a>OnNewTask
Cuando se crea una nueva tarea: desencadena un flujo cuando se crea una nueva tarea. 

```GET: /trigger/_api/ProjectData/Tasks``` 

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Description |
| --- | --- | --- | --- | --- | --- |
| siteUrl |string |yes |query |Ninguna |Dirección URL del sitio raíz del sitio de proyecto (ejemplo: https://sampletenant.sharepoint.com/teams/sampleteam) |

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

## <a name="listprojects"></a>ListProjects
Enumerar proyectos: enumera los proyectos de su sitio de proyectos en línea. 

```GET: /_api/ProjectServer/Projects``` 

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Description |
| --- | --- | --- | --- | --- | --- |
| siteUrl |string |yes |query |Ninguna |Dirección URL del sitio raíz del sitio de proyecto (ejemplo: https://sampletenant.sharepoint.com/teams/sampleteam) |

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

## <a name="createproject"></a>CreateProject
Crear nuevo proyecto: crea un nuevo proyecto en su sitio de proyectos en línea. 

```POST: /_api/ProjectServer/Projects``` 

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Description |
| --- | --- | --- | --- | --- | --- |
| siteUrl |string |yes |query |Ninguna |Dirección URL del sitio raíz del sitio de proyecto (ejemplo: https://sampletenant.sharepoint.com/teams/sampleteam) |
| proj | |yes |body |Ninguna |Nuevo proyecto que se creará |

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

## <a name="createtask"></a>CreateTask
Crear nueva tarea: crea una tarea nueva en el proyecto. 

```POST: /_api/ProjectServer/Projects('{project_id}')/Draft/Tasks/Add``` 

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Description |
| --- | --- | --- | --- | --- | --- |
| siteUrl |string |yes |query |Ninguna |Dirección URL del sitio raíz del sitio de proyecto (ejemplo: https://sampletenant.sharepoint.com/teams/sampleteam) |
| project_id |string |Sí |path |Ninguna |Identificador único del proyecto al que se agregará la tarea |
| task | |yes |body |Ninguna |Nueva tarea que se agregará al proyecto |

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

## <a name="createresource"></a>CreateResource
Crear nuevo recurso: crea un recurso empresarial en su sitio de proyectos en línea. 

```POST: /_api/ProjectServer/EnterpriseResources``` 

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Description |
| --- | --- | --- | --- | --- | --- |
| siteUrl |string |yes |query |Ninguna |Dirección URL del sitio raíz del sitio de proyecto (ejemplo: https://sampletenant.sharepoint.com/teams/sampleteam) |
| resource | |yes |body |Ninguna |Nuevo recurso empresarial que se agregará al proyecto |

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

## <a name="listtasks"></a>ListTasks
Enumerar tareas: enumera las tareas publicadas en un proyecto. 

```GET: /_api/ProjectServer/Projects('{project_id}')/Tasks``` 

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Description |
| --- | --- | --- | --- | --- | --- |
| siteUrl |string |yes |query |Ninguna |Dirección URL del sitio raíz del sitio de proyecto (ejemplo: https://sampletenant.sharepoint.com/teams/sampleteam) |
| project_id |string |Sí |path |Ninguna |Identificador único del proyecto para obtener tareas |

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

## <a name="checkoutproject"></a>CheckoutProject
Desproteger un proyecto: desprotege un proyecto del sitio. 

```POST: /_api/ProjectServer/Projects('{project_id}')/checkOut``` 

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Description |
| --- | --- | --- | --- | --- | --- |
| siteUrl |string |yes |query |Ninguna |Dirección URL del sitio raíz del sitio de proyecto (ejemplo: https://sampletenant.sharepoint.com/teams/sampleteam) |
| project_id |string |Sí |path |Ninguna |Identificador único del proyecto al que se agregará la tarea |

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

## <a name="publishproject"></a>PublishProject
Proteger y publicar proyecto: protege y publica un proyecto existente en el sitio. 

```POST: /_api/ProjectServer/Projects('{project_id}')/Draft/Publish(true)``` 

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Description |
| --- | --- | --- | --- | --- | --- |
| siteUrl |string |yes |query |Ninguna |Dirección URL del sitio raíz del sitio de proyecto (ejemplo: https://sampletenant.sharepoint.com/teams/sampleteam) |
| project_id |string |Sí |path |Ninguna |Identificador único del proyecto que se protegerá |

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

## <a name="object-definitions"></a>Definiciones de objeto
### <a name="triggerprojectswrapper"></a>TriggerProjectsWrapper
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| value |array |No |

### <a name="triggerproject"></a>TriggerProject
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| ProjectStartDate |string |No |
| ProjectFinishDate |string |No |
| ProjectCreatedDate |string |No |
| ProjectId |string |No |
| ProjectModifiedDate |string |No |
| ProjectType |integer |No |
| ProjectName |string |No |

### <a name="triggerresourceswrapper"></a>TriggerResourcesWrapper
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| value |array |No |

### <a name="triggerresource"></a>TriggerResource
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| ResourceId |string |No |
| ResourceBaseCalendar |string |No |
| ResourceBookingType |integer |No |
| ResourceCanLevel |boolean |No |
| ResourceCostPerUse |número |No |
| ResourceCreatedDate |string |No |
| ResourceEarliestAvailableFrom |string |No |
| ResourceEmail |string |No |
| ResourceInitials |string |No |
| ResourceIsActive |boolean |No |
| ResourceIsGeneric |boolean |No |
| ResourceLatestAvailableTo |string |No |
| ResourceModifiedDate |string |No |
| ResourceName |string |No |
| ResourceStatsuName |string |No |
| ResourceType |integer |No |
| TypeDescription |string |No |
| TypeName |string |No |

### <a name="triggertaskswrapper"></a>TriggerTasksWrapper
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| value |array |No |

### <a name="triggertask"></a>TriggerTask
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| ProjectId |string |No |
| TaskId |string |No |
| ProjectName |string |No |
| TaskName |string |No |
| TaskCreatedDate |string |No |
| TaskModifieddate |string |No |
| TaskStartDate |string |No |
| TaskFinishDate |string |No |
| TaskPriority |integer |No |
| TaskIsActive |boolean |No |

### <a name="newproject"></a>NuevoProyecto
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| Nombre |string |Sí |
| Description |string |No |
| Iniciar |string |No |

### <a name="newreource"></a>NewResource
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| Nombre |string |Sí |
| IsBudget |boolean |No |
| IsGeneric |boolean |No |
| IsInactive |boolean |No |

### <a name="project"></a>proyecto
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| ApprovedStart |string |No |
| ApprovedEnd |string |No |
| CheckedOutDate |string |No |
| CheckOutDescription |string |No |
| CheckOutId |string |No |
| CreatedDate |string |No |
| Id |string |No |
| IsCheckedOut |boolean |No |
| LastPublishedDate |string |No |
| LastSavedDate |string |No |
| OptimizerDecision |integer |No |
| PlannerDecision |integer |No |
| ProjectType |integer |No |
| Nombre |string |No |
| WinprojVersion |string |No |

### <a name="projectswrapper"></a>ProjectsWrapper
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| value |array |No |

### <a name="newtask"></a>NewTask
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| parameters |not defined |Sí |

### <a name="taskparameters"></a>TaskParameters
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| Nombre |string |Sí |
| Notas |string |No |
| Iniciar |string |No |
| Duración |string |No |

### <a name="enterpriseresource"></a>EnterpriseResource
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| CanLevel |boolean |No |
| Código |string |No |
| CostAccrual |integer |No |
| CostCenter |string |No |
| Creado |string |No |
| DefaultBookingType |integer |No |
| Email |string |No |
| ExternalId |string |No |
| Grupo |string |No |
| HireDate |string |No |
| Id |string |No |
| Initials |string |No |
| IsActive |boolean |No |
| IsBudget |boolean |No |
| IsCheckedOut |boolean |No |
| IsGeneric |boolean |No |
| IsTeam |boolean |No |
| MaterialLabel |string |No |
| Modified |string |No |
| Nombre |string |No |
| Phonetics |string |No |
| ResourceType |integer |No |
| TerminationDate |string |No |

### <a name="taskswrapper"></a>TasksWrapper
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| value |array |No |

### <a name="task"></a>Tarea
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| Creado |string |No |
| Modified |string |No |
| Iniciar |string |No |
| Finalización |string |No |
| Nombre |string |No |
| Id |string |No |
| Prioridad |integer |No |
| PercentComplete |integer |No |
| Notas |string |No |
| Contacto |string |No |

## <a name="next-steps"></a>Pasos siguientes
[Creación de una aplicación lógica](../logic-apps/logic-apps-create-a-logic-app.md)


