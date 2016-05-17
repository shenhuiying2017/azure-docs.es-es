<properties
pageTitle="Sql Connector | Microsoft Azure"
description="Cree aplicaciones lógicas con el Servicio de aplicaciones de Azure. Sql Connector proporciona una API para trabajar con bases de datos SQL."
services="app-servicelogic"	
documentationCenter=".net,nodejs,java" 	
authors="msftman"	
manager="erikre"	
editor=""
tags="connectors" />

<tags
ms.service="app-service-logic"
ms.devlang="multiple"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="integration"
ms.date="04/29/2016"
ms.author="deonhe"/>

# Get started with the Sql Connector connector (Introducción al conector Sql Connector)



El conector Sql Connector puede usarse desde:

- [Aplicaciones lógicas](../app-service-logic/app-service-logic-what-are-logic-apps.md)  
- [PowerApps](http://powerapps.microsoft.com)  
- [Flujos](http://flows.microsoft.com)  

>[AZURE.NOTE] Esta versión del artículo se aplica a la versión de esquema 2015-08-01-preview de las aplicaciones lógicas.

Puede empezar creando una aplicación lógica ahora. Para ello, consulte [Crear una aplicación lógica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Desencadenadores y acciones

El conector Sql Connector se puede usar como acción; tiene desencadenadores. Todos los conectores admiten datos en formato JSON y XML.

 El conector Sql Connector tiene disponibles las siguientes acciones o desencadenadores:

### Acciones de Sql Connector
Puede realizar estas acciones:

|Acción|Descripción|
|--- | ---|
|[GetTables](connectors-create-api-sqlconnector.md#gettables)|Recupera tablas de una base de datos SQL|
|[PostItem](connectors-create-api-sqlconnector.md#postitem)|Inserta una nueva fila en una tabla SQL|
|[GetItem](connectors-create-api-sqlconnector.md#getitem)|Recupera una sola fila de una tabla SQL|
|[DeleteItem](connectors-create-api-sqlconnector.md#deleteitem)|Elimina una fila de una tabla SQL|
|[PatchItem](connectors-create-api-sqlconnector.md#patchitem)|Actualiza una fila existente en una tabla SQL|
### Desencadenadores de Sql Connector
Se pueden escuchar estos eventos:

|Desencadenador | Descripción|
|--- | ---|


## Creación de una conexión a Sql Connector
Para crear aplicaciones lógicas con Sql Connector, primero debe crear una **conexión** y, a continuación, proporcionar los detalles de las siguientes propiedades:

|Propiedad| Obligatorio|Descripción|
| ---|---|---|
|SqlConnectionString|Sí|Proporcionar su cadena de conexión SQL|
Después de crear la conexión, puede usarla para ejecutar las acciones y escuchar los desencadenadores descritos en este artículo.

>[AZURE.TIP] Puede usar esta conexión en otras aplicaciones lógicas.

## Referencia para Sql Connector
Se aplica a la versión: 1.0

## GetTables
Obtener tablas: recupera tablas de una base de datos SQL

```GET: /datasets/default/tables```

No hay parámetros para esta llamada
#### Respuesta

|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|


## PostItem
Insertar fila: inserta una nueva fila en una tabla SQL

```POST: /datasets/default/tables/{table}/items```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|table|cadena|yes|path|Ninguna|Nombre de la tabla SQL|
|item| |yes|body|Ninguna|Fila que se va a insertar en la tabla especificada en SQL|

#### Respuesta

|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|


## GetItem
Obtener fila: recupera una sola fila de una tabla SQL

```GET: /datasets/default/tables/{table}/items/{id}```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|table|cadena|yes|path|Ninguna|Nombre de la tabla SQL|
|id|cadena|yes|path|Ninguna|Identificador único de la fila que se va a recuperar|

#### Respuesta

|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|


## DeleteItem
Eliminar fila: elimina una fila de una tabla SQL

```DELETE: /datasets/default/tables/{table}/items/{id}```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|table|cadena|yes|path|Ninguna|Nombre de la tabla SQL|
|id|cadena|yes|path|Ninguna|Identificador único de la fila que se va a eliminar|

#### Respuesta

|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|


## PatchItem
Actualizar fila: actualiza una fila existente en una tabla SQL

```PATCH: /datasets/default/tables/{table}/items/{id}```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|table|cadena|yes|path|Ninguna|Nombre de la tabla SQL|
|id|cadena|yes|path|Ninguna|Identificador único de la fila que se va a actualizar|
|item| |yes|body|Ninguna|Fila con valores actualizados|

#### Respuesta

|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|


## Definiciones de objeto 

### DataSetsMetadata


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|tabular|not defined|No |
|blob|not defined|No |



### TabularDataSetsMetadata


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|de origen|cadena|No |
|DisplayName|cadena|No |
|urlEncoding|cadena|No |
|tableDisplayName|cadena|No |
|tablePluralName|cadena|No |



### BlobDataSetsMetadata


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|de origen|cadena|No |
|DisplayName|cadena|No |
|urlEncoding|cadena|No |



### TableMetadata


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|name|cadena|No |
|título|cadena|No |
|x-ms-permission|cadena|No |
|x-ms-capabilities|not defined|No |
|schema|not defined|No |



### TableCapabilitiesMetadata


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|sortRestrictions|not defined|No |
|filterRestrictions|not defined|No |
|filterFunctions|array|No |



### Objeto


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|



### TableSortRestrictionsMetadata


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|sortable|boolean|No |
|unsortableProperties|array|No |
|ascendingOnlyProperties|array|No |



### TableFilterRestrictionsMetadata


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|filterable|boolean|No |
|nonFilterableProperties|array|No |
|requiredProperties|array|No |



### DataSetsList


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|value|array|No |



### DataSet


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|Nombre|cadena|No |
|DisplayName|cadena|No |



### Tabla


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|Nombre|cadena|No |
|DisplayName|cadena|No |



### Elemento


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|ItemInternalId|cadena|No |



### TablesList


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|value|array|No |



### ItemsList


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|value|array|No |


## Pasos siguientes
[Creación de una aplicación lógica](../app-service-logic/app-service-logic-create-a-logic-app.md)

<!---HONumber=AcomDC_0504_2016-->