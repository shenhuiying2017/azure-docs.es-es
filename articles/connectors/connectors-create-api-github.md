---
title: GitHub | Microsoft Docs
description: "Cree aplicaciones lógicas con el Servicio de aplicaciones de Azure. GitHub es un servicio de hospedaje del repositorio Git basado en la Web. Ofrece toda la funcionalidad de administración de código fuente (SCM) y control de revisiones distribuidas de Git, además de agregar sus propias características."
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: 8f873e6c-f4c0-4c2e-a5bd-2e953efe5e2b
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/18/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: b92f954680603891ced503a1134791312b5214f0
ms.openlocfilehash: 6f336db8c8719b99420b353dca82e36a0d837769


---
# <a name="get-started-with-the-github-connector"></a>Introducción al conector GitHub
GitHub es un servicio de hospedaje del repositorio Git basado en la Web. Ofrece toda la funcionalidad de administración de código fuente (SCM) y control de revisiones distribuidas de Git, además de agregar sus propias características.

> [!NOTE]
> Esta versión del artículo se aplica a la versión de esquema 2015-08-01-preview de aplicaciones lógicas. 
> 
> 

Puede empezar creando una aplicación lógica ahora. Para ello, consulte [Creación de una nueva aplicación lógica mediante la conexión de servicios de SaaS](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Desencadenadores y acciones
El conector GitHub se puede usar como acción; tiene desencadenadores. Todos los conectores admiten datos en formato JSON y XML. 

 El conector GitHub tiene las siguientes acciones y desencadenadores disponibles:

### <a name="github-actions"></a>Acciones de GitHub
Puede realizar estas acciones:

| Acción | Descripción |
| --- | --- |
| [CreateIssue](connectors-create-api-github.md#createissue) |Crea un problema |

### <a name="github-triggers"></a>Desencadenadores de GitHub
Se pueden escuchar estos eventos:

| Desencadenador | Description |
| --- | --- |
| Cuando se abre un problema |Se abre un problema |
| Cuando se cierra un problema |Se cierra un problema |
| Cuando se asigna un problema |Se asigna un problema |

## <a name="create-a-connection-to-github"></a>Creación de una conexión a GitHub
Para crear Logic Apps con GitHub, primero necesita crear una **conexión** y, después, especificar los detalles de las siguientes propiedades: 

| Propiedad | Obligatorio | Descripción |
| --- | --- | --- |
| Se necesita el cifrado de tokens |Sí |Proporcionar las credenciales de GitHub |

Después de crear la conexión, puede usarla para ejecutar las acciones y escuchar los desencadenadores descritos en este artículo. 

> [!INCLUDE [Steps to create a connection to GitHub](../../includes/connectors-create-api-github.md)]
> 
> [!TIP]
> Puede usar esta conexión en otras aplicaciones lógicas.
> 
> 

## <a name="reference-for-github"></a>Referencia de GitHub
Se aplica a la versión: 1.0

## <a name="createissue"></a>CreateIssue
Crear un problema: se crea un problema 

```POST: /repos/{repositoryOwner}/{repositoryName}/issues``` 

| Nombre | Tipo de datos | Obligatorio | Ubicado en | Valor predeterminado | Description |
| --- | --- | --- | --- | --- | --- |
| repositoryOwner |string |Sí |path |Ninguna |Propietario del repositorio |
| repositoryName |string |Sí |path |Ninguna |Nombre del repositorio |
| issueBasicDetails | |yes |body |Ninguna |Detalles del problema |

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

## <a name="issueopened"></a>IssueOpened
Cuando se abre un problema: se abre un problema 

```GET: /trigger/issueOpened``` 

No hay parámetros para esta llamada

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

## <a name="issueclosed"></a>IssueClosed
Cuando se cierra un problema: se cierra un problema 

```GET: /trigger/issueClosed``` 

No hay parámetros para esta llamada

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

## <a name="issueassigned"></a>IssueAssigned
Cuando se asigna un problema: se asigna un problema 

```GET: /trigger/issueAssigned``` 

No hay parámetros para esta llamada

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
### <a name="issuebasicdetailsmodel"></a>IssueBasicDetailsModel
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| título |string |Sí |
| body |string |Sí |
| assignee |string |Sí |

### <a name="issuedetailsmodel"></a>IssueDetailsModel
| Nombre de propiedad | Tipo de datos | Obligatorio |
| --- | --- | --- |
| título |string |Sí |
| body |string |Sí |
| assignee |string |Sí |
| número |string |No |
| state |string |No |
| created_at |string |No |
| repository_url |string |No |

## <a name="next-steps"></a>Pasos siguientes
[Creación de una aplicación lógica](../logic-apps/logic-apps-create-a-logic-app.md)




<!--HONumber=Jan17_HO3-->


