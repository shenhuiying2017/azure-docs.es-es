<properties
    pageTitle="Incorporación del conector de SQL Azure a las aplicaciones lógicas | Microsoft Azure"
    description="Información general del conector de SQL Azure con parámetros de la API de REST"
    services=""
    documentationCenter="" 
    authors="MandiOhlinger"
    manager="erikre"
    editor=""
    tags="connectors"/>

<tags
   ms.service="multiple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="05/19/2016"
   ms.author="mandia"/>


# Introducción al conector de SQL Azure
Conéctese a Base de datos SQL de Azure para administrar las tablas y filas, como insertar filas, obtener tablas y mucho más.

El conector de Base de datos SQL de Azure puede usarse desde:

- Aplicaciones lógicas (descritas en este tema)
- PowerApps (consulte la [lista de conexiones de PowerApps](https://powerapps.microsoft.com/tutorials/connections-list/) para obtener una lista completa)

>[AZURE.NOTE] Esta versión del artículo se aplica a la versión de esquema 2015-08-01-preview de las aplicaciones lógicas.

Con Base de datos SQL de Azure, puede:

- Compilar el flujo de negocio en función de los datos obtenidos de Base de datos SQL de Azure 
- Usar acciones para obtener una fila, insertar una fila y mucho más. Estas acciones obtienen una respuesta y luego dejan el resultado a disposición de otras acciones. Por ejemplo, puede obtener una fila de datos de Base de datos SQL de Azure y luego agregar datos a Excel. 

Para agregar una operación en aplicaciones lógicas, consulte [Creación de una nueva aplicación lógica mediante la conexión de servicios de SaaS](../app-service-logic/app-service-logic-create-a-logic-app.md).


## Desencadenadores y acciones
SQL incluye las siguientes acciones. No hay desencadenadores.

Desencadenadores | Acciones
--- | ---
None | <ul><li>Obtener fila</li><li>Obtener filas</li><li>Insertar fila</li><li>Eliminar fila</li><li>Obtener tablas</li><li>Actualizar fila</li></ul>

Todos los conectores admiten datos en formato JSON y XML.

## Creación de la conexión a SQL

>[AZURE.INCLUDE [Pasos para crear una conexión a SQL](../../includes/connectors-create-api-sqlazure.md)]


Después de crear la conexión, especifique las propiedades de SQL, como el nombre de tabla. En la **referencia de la API de REST** de este tema, se describen estas propiedades.

>[AZURE.TIP] Puede usar esta conexión en otras aplicaciones lógicas.

## Referencia de la API de REST de Swagger
Se aplica a la versión: 1.0.

### Obtener fila 
Recupera una sola fila de una tabla SQL. ```GET: /datasets/default/tables/{table}/items/{id}```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|table|cadena|yes|path|Ninguna|Nombre de la tabla SQL|
|id|cadena|yes|path|Ninguna|Identificador único de la fila que se va a recuperar|

#### Respuesta
|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|


### Obtener filas 
Recupera filas de una tabla SQL. ```GET: /datasets/default/tables/{table}/items```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|table|cadena|yes|path|Ninguna|Nombre de la tabla SQL|
|$skip|integer|no|query|Ninguna|Número de entradas para omitir (valor predeterminado = 0)|
|$top|integer|no|query|Ninguna|Número máximo de entradas para recuperar (valor predeterminado = 256)|
|$filter|cadena|no|query|Ninguna|Consulta de filtro de ODATA para restringir el número de entradas|
|$orderby|cadena|no|query|Ninguna|Consulta orderBy de ODATA para especificar el orden de las entradas|

#### Respuesta
|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|


### Insertar fila 
Inserta una nueva fila en una tabla SQL. ```POST: /datasets/default/tables/{table}/items```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|table|cadena|yes|path|Ninguna|Nombre de la tabla SQL|
|item|ItemInternalId: string|yes|body|Ninguna|Fila que se va a insertar en la tabla especificada en SQL|

#### Respuesta
|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|


### Eliminar fila 
Elimina una fila de una tabla SQL. ```DELETE: /datasets/default/tables/{table}/items/{id}```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|table|cadena|yes|path|Ninguna|Nombre de la tabla SQL|
|id|cadena|yes|path|Ninguna|Identificador único de la fila que se va a eliminar|

#### Respuesta
|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|


### Obtener tablas 
Recupera tablas de una base de datos SQL. ```GET: /datasets/default/tables```

No hay parámetros para esta llamada.

#### Respuesta
|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|


### Actualizar fila 
Actualiza una fila existente en una tabla SQL. ```PATCH: /datasets/default/tables/{table}/items/{id}```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|table|cadena|yes|path|Ninguna|Nombre de la tabla SQL|
|id|cadena|yes|path|Ninguna|Identificador único de la fila que se va a actualizar|
|item|ItemInternalId: string|yes|body|Ninguna|Fila con valores actualizados|

#### Respuesta
|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|

## Definiciones de objeto

#### DataSetsMetadata

|Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|tabular|not defined|no|
|blob|not defined|no|

#### TabularDataSetsMetadata

|Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|de origen|cadena|no|
|DisplayName|cadena|no|
|urlEncoding|cadena|no|
|tableDisplayName|cadena|no|
|tablePluralName|cadena|no|

#### BlobDataSetsMetadata

|Nombre de propiedad | Tipo de datos |Obligatorio |
|---|---|---|
|de origen|cadena|no|
|DisplayName|cadena|no|
|urlEncoding|cadena|no|

#### TableMetadata

|Nombre de propiedad | Tipo de datos |Obligatorio |
|---|---|---|
|name|cadena|no|
|título|cadena|no|
|x-ms-permission|cadena|no|
|schema|not defined|no|

#### DataSetsList

|Nombre de propiedad | Tipo de datos |Obligatorio |
|---|---|---|
|value|array|no|

#### DataSet

|Nombre de propiedad | Tipo de datos |Obligatorio |
|---|---|---|
|Nombre|cadena|no|
|DisplayName|cadena|no|

#### Tabla

|Nombre de propiedad | Tipo de datos |Obligatorio |
|---|---|---|
|Nombre|cadena|no|
|DisplayName|cadena|no|

#### Elemento

|Nombre de propiedad | Tipo de datos |Obligatorio |
|---|---|---|
|ItemInternalId|cadena|no|

#### ItemsList

|Nombre de propiedad | Tipo de datos |Obligatorio |
|---|---|---|
|value|array|no|

#### TablesList

|Nombre de propiedad | Tipo de datos |Obligatorio |
|---|---|---|
|value|array|no|


## Pasos siguientes

[Creación de una aplicación lógica](../app-service-logic/app-service-logic-create-a-logic-app.md).

<!---HONumber=AcomDC_0525_2016-->