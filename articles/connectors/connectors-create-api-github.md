<properties
pageTitle="GitHub | Microsoft Azure"
description="Cree aplicaciones lógicas con el Servicio de aplicaciones de Azure. GitHub es un servicio de hospedaje del repositorio Git basado en la Web. Ofrece toda la funcionalidad de administración de código fuente (SCM) y control de revisiones distribuidas de Git, además de agregar sus propias características."
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

# Introducción al conector GitHub

GitHub es un servicio de hospedaje del repositorio Git basado en la Web. Ofrece toda la funcionalidad de administración de código fuente (SCM) y control de revisiones distribuidas de Git, además de agregar sus propias características.

>[AZURE.NOTE] Esta versión del artículo se aplica a la versión de esquema 2015-08-01-preview de las aplicaciones lógicas.

Puede empezar creando una aplicación lógica ahora. Para ello, consulte [Creación de una nueva aplicación lógica mediante la conexión de servicios de SaaS](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Desencadenadores y acciones

El conector GitHub se puede usar como acción; tiene desencadenadores. Todos los conectores admiten datos en formato JSON y XML.

 El conector GitHub tiene las siguientes acciones y desencadenadores disponibles:

### Acciones de GitHub
Puede realizar estas acciones:

|Acción|Description|
|--- | ---|
|[CreateIssue](connectors-create-api-github.md#createissue)|Crea un problema|
### Desencadenadores de GitHub
Se pueden escuchar estos eventos:

|Desencadenador | Description|
|--- | ---|
|Cuando se abre un problema|Se abre un problema|
|Cuando se cierra un problema|Se cierra un problema|
|Cuando se asigna un problema|Se asigna un problema|


## Creación de una conexión a GitHub
Para crear aplicaciones lógicas con GitHub, primero debe crear una **conexión** y, después, especificar los detalles de las siguientes propiedades:

|Propiedad| Obligatorio|Description|
| ---|---|---|
|SWT|Sí|Proporcionar las credenciales de GitHub|
Después de crear la conexión, puede usarla para ejecutar las acciones y escuchar los desencadenadores descritos en este artículo.

>[AZURE.INCLUDE [Pasos para crear una conexión a GitHub](../../includes/connectors-create-api-github.md)]

>[AZURE.TIP] Puede usar esta conexión en otras aplicaciones lógicas.

## Referencia de GitHub
Se aplica a la versión: 1.0

## CreateIssue
Crear un problema: se crea un problema

```POST: /repos/{repositoryOwner}/{repositoryName}/issues```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Description|
| ---|---|---|---|---|---|
|repositoryOwner|string|yes|path|Ninguna|Propietario del repositorio|
|repositoryName|string|yes|path|Ninguna|Nombre del repositorio|
|issueBasicDetails| |yes|body|Ninguna|Detalles del problema|

#### Response

|Nombre|Descripción|
|---|---|
|200|OK|
|400|Bad Request|
|401|No autorizado|
|403|Prohibido|
|404|No encontrado|
|500|Error interno del servidor. Error desconocido|
|default|Error en la operación.|


## IssueOpened
Cuando se abre un problema: se abre un problema

```GET: /trigger/issueOpened```

No hay parámetros para esta llamada
#### Response

|Nombre|Descripción|
|---|---|
|200|OK|
|400|Bad Request|
|401|No autorizado|
|403|Prohibido|
|404|No encontrado|
|500|Error interno del servidor. Error desconocido|
|default|Error en la operación.|


## IssueClosed
Cuando se cierra un problema: se cierra un problema

```GET: /trigger/issueClosed```

No hay parámetros para esta llamada
#### Response

|Nombre|Descripción|
|---|---|
|200|OK|
|400|Bad Request|
|401|No autorizado|
|403|Prohibido|
|404|No encontrado|
|500|Error interno del servidor. Error desconocido|
|default|Error en la operación.|


## IssueAssigned
Cuando se asigna un problema: se asigna un problema

```GET: /trigger/issueAssigned```

No hay parámetros para esta llamada
#### Response

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

### IssueBasicDetailsModel


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|título|string|Sí |
|body|string|Sí |
|assignee|string|Sí |



### IssueDetailsModel


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|título|string|Sí |
|body|string|Sí |
|assignee|string|Sí |
|número|string|No |
|state|string|No |
|created\_at|string|No |
|repository\_url|string|No |


## Pasos siguientes
[Creación de una aplicación lógica](../app-service-logic/app-service-logic-create-a-logic-app.md)

<!---HONumber=AcomDC_0824_2016-->