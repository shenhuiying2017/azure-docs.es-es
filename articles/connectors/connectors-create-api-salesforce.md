<properties
pageTitle="Incorporación del conector de Salesforce a PowerApps Enterprise y a las aplicaciones lógicas| Microsoft Azure"
description="Información general del conector de Salesforce con parámetros de la API de REST"
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
ms.date="05/19/2016"
ms.author="deonhe"/>

# Introducción al conector de Salesforce 
Conéctese a Salesforce y cree objetos, obtenga objetos, etc. El conector de Salesforce puede usarse desde:

- Aplicaciones lógicas 
- PowerApps

> [AZURE.SELECTOR]
- [Aplicaciones lógicas](../articles/connectors/connectors-create-api-salesforce.md)
- [PowerApps Enterprise](../articles/power-apps/powerapps-create-api-salesforce.md)

&nbsp;

>[AZURE.NOTE] Esta versión del artículo se aplica a la versión de esquema 2015-08-01-preview de las aplicaciones lógicas.

Con Salesforce, puede:

- Compilar el flujo de negocio en función de los datos que obtiene de SalesForce. 
- Usar desencadenadores para cuando se crea o actualiza un objeto.
- Usar acciones para crear un objeto, eliminar un objeto, etc. Estas acciones obtienen una respuesta y luego dejan el resultado a disposición de otras acciones. Por ejemplo, cuando se crea un nuevo objeto en Salesforce, puede enviar un correo electrónico mediante Office 365.
- Agregar el conector de Salesforce a PowerApps Enterprise. Así, los usuarios pueden utilizar este conector en sus aplicaciones. 

Si desea obtener información sobre cómo agregar un conector a PowerApps Enterprise, vaya a [Registro de una API administrada por Microsoft o una API administrada por TI](../power-apps/powerapps-register-from-available-apis.md).

Para agregar una operación en aplicaciones lógicas, consulte [Creación de una nueva aplicación lógica mediante la conexión de servicios de SaaS](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Desencadenadores y acciones
La API de Salesforce incluye los siguientes desencadenadores y acciones.

| Desencadenadores | Acciones|
| --- | --- |
|<ul><li>Cuando se crea un objeto</li><li>Cuando se modifica un objeto</li></ul> | <ul><li>Crear objeto</li><li>Obtener objetos</li><li>Cuando se crea un objeto</li><li>Cuando se modifica un objeto</li><li>Eliminar objeto</li><li>Obtener objeto</li><li>Obtener tipos de objeto (SObjects)</li><li>Actualizar objeto</li></ul>

Todos los conectores admiten datos en formato JSON y XML.

## Creación de una conexión a Salesforce 

Al agregar este conector a las aplicaciones lógicas, debe autorizar a estas para que se conecten a su Salesforce.

>[AZURE.INCLUDE [Pasos para crear una conexión a Salesforce](../../includes/connectors-create-api-salesforce.md)]

Después de crear la conexión, especifique las propiedades de Salesforce, como el nombre de tabla. En la **referencia de la API de REST** de este tema, se describen estas propiedades.

>[AZURE.TIP] Puede usar esta misma conexión en otras aplicaciones lógicas.

## Referencia de API de REST de Swagger
Se aplica a la versión: 1.0.


### Crear objeto
Crea un objeto de Salesforce. ```POST: /datasets/default/tables/{table}/items```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|table|cadena|yes|path|Ninguna|Tipo SObject de Salesforce (ejemplo: 'Lead')|
|item| |yes|body|Ninguna|Objeto de Salesforce para crear|

### Respuesta
|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|



### Obtener objeto
Recupera un objeto de Salesforce. ```GET: /datasets/default/tables/{table}/items/{id}```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|table|cadena|yes|path|Ninguna|Tipo SObject de Salesforce (ejemplo: 'Lead')|
|id|cadena|yes|path|Ninguna|Identificador único del objeto de Salesforce para recuperar|

### Respuesta

|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|



### Eliminar objeto
Elimina un objeto de Salesforce. ```DELETE: /datasets/default/tables/{table}/items/{id}```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|table|cadena|yes|path|Ninguna|Tipo SObject de Salesforce (ejemplo: 'Lead')|
|id|cadena|yes|path|Ninguna|Identificador único del objeto de Salesforce para eliminar|

### Respuesta
|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|



### Actualizar objeto
Actualiza un objeto de Salesforce. ```PATCH: /datasets/default/tables/{table}/items/{id}```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|table|cadena|yes|path|Ninguna|Tipo SObject de Salesforce (ejemplo: 'Lead')|
|id|cadena|yes|path|Ninguna|Identificador único del objeto de Salesforce para actualizar|
|item| |yes|body|Ninguna|Objeto de Salesforce con propiedades cambiadas|

### Respuesta
|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|



### Cuando se crea un objeto
Desencadena un flujo cuando se crea un objeto en Salesforce. ```GET: /datasets/default/tables/{table}/onnewitems```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|table|cadena|yes|path|Ninguna|Tipo SObject de Salesforce (ejemplo: 'Lead')|
|$skip|integer|no|query|Ninguna|Número de entradas para omitir (valor predeterminado = 0)|
|$top|integer|no|query|Ninguna|Número máximo de entradas para recuperar (valor predeterminado = 256)|
|$filter|cadena|no|query|Ninguna|Consulta de filtro de ODATA para restringir el número de entradas|
|$orderby|cadena|no|query|Ninguna|Consulta orderBy de ODATA para especificar el orden de las entradas|

### Respuesta
|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|



### Cuando se modifica un objeto 
Desencadena un flujo cuando se modifica un objeto en Salesforce. ```GET: /datasets/default/tables/{table}/onupdateditems```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|table|cadena|yes|path|Ninguna|Tipo SObject de Salesforce (ejemplo: 'Lead')|
|$skip|integer|no|query|Ninguna|Número de entradas para omitir (valor predeterminado = 0)|
|$top|integer|no|query|Ninguna|Número máximo de entradas para recuperar (valor predeterminado = 256)|
|$filter|cadena|no|query|Ninguna|Consulta de filtro de ODATA para restringir el número de entradas|
|$orderby|cadena|no|query|Ninguna|Consulta orderBy de ODATA para especificar el orden de las entradas|

### Respuesta
|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|



## Definiciones de objeto 

#### DataSetsMetadata

| Nombre | Tipo de datos | Obligatorio|
|---|---|---|
|tabular|not defined|no|
|blob|not defined|no|


#### TabularDataSetsMetadata

| Nombre | Tipo de datos | Obligatorio|
|---|---|---|
|de origen|cadena|no|
|DisplayName|cadena|no|
|urlEncoding|cadena|no|
|tableDisplayName|cadena|no|
|tablePluralName|cadena|no|


#### BlobDataSetsMetadata

| Nombre | Tipo de datos | Obligatorio|
|---|---|---|
|de origen|cadena|no|
|DisplayName|cadena|no|
|urlEncoding|cadena|no|


#### TableMetadata

| Nombre | Tipo de datos | Obligatorio|
|---|---|---|
|name|cadena|no|
|título|cadena|no|
|x-ms-permission|cadena|no|
|schema|not defined|no|


#### DataSetsList

| Nombre | Tipo de datos | Obligatorio|
|---|---|---|
|value|array|no|


#### DataSet

| Nombre | Tipo de datos | Obligatorio|
|---|---|---|
|Nombre|cadena|
|DisplayName|cadena|no|


#### Tabla

| Nombre | Tipo de datos | Obligatorio|
|---|---|---|
|Nombre|cadena|no|
|DisplayName|cadena|no|


#### Elemento

| Nombre | Tipo de datos | Obligatorio|
|---|---|---|
|ItemInternalId|cadena|no|


#### ItemsList

| Nombre | Tipo de datos | Obligatorio|
|---|---|---|
|value|array|no|


#### TablesList

| Nombre | Tipo de datos | Obligatorio|
|---|---|---|
|value|array|no|


## Pasos siguientes

[Crear una aplicación lógica](../app-service-logic/app-service-logic-create-a-logic-app.md).

Volver a la [lista de API](apis-list.md).


[5]: https://developer.salesforce.com
[6]: ./media/connectors-create-api-salesforce/salesforce-developer-homepage.png
[7]: ./media/connectors-create-api-salesforce/salesforce-create-app.png
[8]: ./media/connectors-create-api-salesforce/salesforce-new-app.png

<!---HONumber=AcomDC_0525_2016-->