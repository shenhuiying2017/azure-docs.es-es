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
ms.date="05/17/2016"
ms.author="deonhe"/>

# Introducción al conector GitHub



El conector GitHub se puede usar desde:

- [Aplicaciones lógicas](../app-service-logic/app-service-logic-what-are-logic-apps.md)
- [PowerApps](http://powerapps.microsoft.com)
- [Flujo](http://flows.microsoft.com)

>[AZURE.NOTE] Esta versión del artículo se aplica a la versión de esquema 2015-08-01-preview de las aplicaciones lógicas.

Puede empezar creando una aplicación lógica ahora. Para ello, consulte [Crear una aplicación lógica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Desencadenadores y acciones

El conector GitHub se puede usar como acción; tiene desencadenadores. Todos los conectores admiten datos en formato JSON y XML.

 El conector GitHub tiene las siguientes acciones y desencadenadores disponibles:

### Acciones de GitHub
Puede realizar estas acciones:

|Acción|Descripción|
|--- | ---|
|[CreateIssue](connectors-create-api-github.md#createissue)|Crea un problema|
### Desencadenadores de GitHub
Se pueden escuchar estos eventos:

|Desencadenador | Descripción|
|--- | ---|
|Cuando se abre un problema|Se abre un problema|
|Cuando se cierra un problema|Se cierra un problema|
|Cuando se asigna un problema|Se asigna un problema|


## Creación de una conexión a GitHub
Para crear aplicaciones lógicas con GitHub, primero debe crear una **conexión** y, a continuación, proporcionar los detalles de las siguientes propiedades:

|Propiedad| Obligatorio|Descripción|
| ---|---|---|
|Se necesita el cifrado de tokens|Sí|Proporcionar las credenciales de GitHub|
Después de crear la conexión, puede usarla para ejecutar las acciones y escuchar los desencadenadores descritos en este artículo.

>[AZURE.INCLUDE [Pasos para crear una conexión a GitHub](../../includes/connectors-create-api-github.md)]

>[AZURE.TIP] Puede usar esta conexión en otras aplicaciones lógicas.

## Referencia de GitHub
Se aplica a la versión: 1.0

## CreateIssue
Crear un problema: se crea un problema

```POST: /repos/{repositoryOwner}/{repositoryName}/issues```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|repositoryOwner|cadena|yes|path|Ninguna|Propietario del repositorio|
|repositoryName|cadena|yes|path|Ninguna|Nombre del repositorio|
|issueBasicDetails| |yes|body|Ninguna|Detalles del problema|

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


## IssueOpened
Cuando se abre un problema: se abre un problema

```GET: /trigger/issueOpened```

No hay parámetros para esta llamada
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


## IssueClosed
Cuando se cierra un problema: se cierra un problema

```GET: /trigger/issueClosed```

No hay parámetros para esta llamada
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


## IssueAssigned
Cuando se asigna un problema: se asigna un problema

```GET: /trigger/issueAssigned```

No hay parámetros para esta llamada
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

### IssueBasicDetailsModel


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|título|cadena|Sí |
|body|cadena|Sí |
|assignee|cadena|Sí |



### IssueDetailsModel


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|título|cadena|Sí |
|body|cadena|Sí |
|assignee|cadena|Sí |
|número|cadena|No |
|state|cadena|No |
|created\_at|cadena|No |
|repository\_url|cadena|No |


## Pasos siguientes
[Creación de una aplicación lógica](../app-service-logic/app-service-logic-create-a-logic-app.md)

<!---HONumber=AcomDC_0803_2016-->