<properties
pageTitle="Incorporación de la API de Dynamic CRM Online en PowerApps Enterprise o en las Aplicaciones lógicas| Microsoft Azure"
description="Información general de la API de CRM Online con los parámetros de la API de REST"
services=""	
documentationCenter="" 	
authors="msftman"	
manager="erikre"	
editor="" tags="connectors" />

<tags
ms.service="multiple"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="integration"
ms.date="03/16/2016"
ms.author="deonhe"/>

# Introducción a la API de CRM
Conéctese a Dynamics CRM Online para crear un nuevo registro, actualizar un elemento y mucho más. La API de CRM Online puede utilizarse desde:

- Aplicaciones lógicas
- PowerApps

> [AZURE.SELECTOR]
- [Aplicaciones lógicas](../articles/connectors/connectors-create-api-crmonline.md)
- [PowerApps Enterprise](../articles/power-apps/powerapps-create-api-crmonline.md)

Con CRM Online, puede hacer lo siguiente:

- Compilar el flujo de negocio en función de los datos que obtiene de CRM Online. 
- Usar acciones para eliminar registros, obtener entidades y mucho más. Estas acciones obtienen una respuesta y luego dejan el resultado a disposición de otras acciones. Por ejemplo, cuando se actualice un elemento en CRM, puede enviar un correo electrónico mediante Office 365.


Para obtener información sobre cómo agregar una API en PowerApps Enterprise, vaya a [Registro de una API en PowerApps](../power-apps/powerapps-register-from-available-apis.md).

Para agregar una operación en aplicaciones lógicas, consulte [Creación de una aplicación lógica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Desencadenadores y acciones
La API de CRM incluye las siguientes acciones. No hay desencadenadores.

| Desencadenadores | Acciones|
| --- | --- |
|None| <ul><li>Crear un nuevo registro</li><li>Obtener registros</li><li>Eliminar un registro</li><li>Obtener un registro</li><li>Obtener entidades</li><li>Actualizar un elemento</li></ul>

Todas las API admiten datos en formato JSON y XML.

## Creación de una conexión a CRM Online

Al agregar esta API a las aplicaciones lógicas, debe iniciar sesión en Dynamics CRM Online. Siga estos pasos para iniciar sesión en CRM Online y completar la configuración de la **conexión** en la aplicación lógica:

1. En la aplicación lógica, seleccione **Agregar una acción**: ![Configurar CRM Online][13]
4. Escriba CRM en el cuadro de búsqueda y espere a que la búsqueda devuelva todas las entradas que incluyan CRM en el nombre.
5. Seleccione **Dynamics CRM Online: Crear un nuevo registro**.
6. Seleccione **Sign in to Dynamics CRM Online** (Iniciar sesión en Dynamics CRM Online): ![Configurar CRM Online][14]
7. Proporcione sus credenciales de CRM Online para iniciar sesión y autorizar la aplicación ![Configurar CRM Online][15].  
8. Después de iniciar sesión, vuelva a la aplicación lógica para completar el proceso agregando otros desencadenadores y otras acciones que necesite.
9. Para guardar el trabajo, seleccione **Guardar** en la barra de menús anterior.

Después de crear la conexión, especifique las propiedades de CRM Online, como table o dataset. En la **referencia de la API de REST** de este tema se describen estas propiedades.

>[AZURE.TIP] Puede usar esta conexión en otras aplicaciones lógicas.

## Referencia de la API de REST de Swagger
Se aplica a la versión: 1.0.

### Crear un nuevo registro 
Crea un nuevo registro en una entidad. ```POST: /datasets/{dataset}/tables/{table}/items```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|dataset|cadena|yes|path|Ninguna|Nombre único para la organización de CRM contoso.crm|
|table|cadena|yes|path|Ninguna|Nombre de la entidad|
|item| |yes|body|Ninguna|Registro para crear|

#### Respuesta
|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|


### Obtiene registros 
 Obtiene registros de una entidad. ```GET: /datasets/{dataset}/tables/{table}/items```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|dataset|cadena|yes|path|Ninguna|Nombre único para la organización de CRM contoso.crm|
|table|cadena|yes|path|Ninguna|Nombre de la entidad|
|$skip|integer|no|query|Ninguna|Número de entradas para omitir. El valor predeterminado es 0.|
|$top|integer|no|query|Ninguna|Número máximo de entradas para capturar. El valor predeterminado es 100.|
|$filter|cadena|no|query|Ninguna|Consulta de filtro ODATA para restringir el número de entradas.|
|$orderby|cadena|no|query|Ninguna|Consulta orderBy de ODATA para especificar el orden de las entradas.|

#### Respuesta
|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|




### Devolver los conjuntos de datos 
 Devuelve los conjuntos de datos. ```GET: /datasets```

No hay parámetros para esta llamada.

#### Respuesta
|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|



### Obtiene un elemento de tabla 
Se usa para obtener un determinado registro que existe para una entidad CRM. ```GET: /datasets/{dataset}/tables/{table}/items/{id}```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|dataset|cadena|yes|path|Ninguna|Nombre único para la organización de CRM contoso.crm|
|table|cadena|yes|path|Ninguna|Nombre de la entidad|
|id|cadena|yes|path|Ninguna|Identificador del registro|

#### Respuesta
|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|

### Eliminar un elemento de una lista 
Elimina un elemento de una lista. ```DELETE: /datasets/{dataset}/tables/{table}/items/{id}```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|dataset|cadena|yes|path|Ninguna|Nombre único para la organización de CRM contoso.crm|
|table|cadena|yes|path|Ninguna|Nombre de la entidad|
|id|cadena|yes|path|Ninguna|Identificador del registro|

#### Respuesta
|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|



### Aplica un parche a un elemento de tabla existente 
Se usa para actualizar parcialmente un registro existente para una entidad CRM. ```PATCH: /datasets/{dataset}/tables/{table}/items/{id}```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|dataset|cadena|yes|path|Ninguna|Nombre único para la organización de CRM contoso.crm|
|table|cadena|yes|path|Ninguna|Nombre de la entidad|
|id|cadena|yes|path|Ninguna|Identificador del registro|
|item| |yes|body|Ninguna|Registro para actualizar|

#### Respuesta
|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|

### Obtiene entidades 
Se usa para obtener la lista de entidades existentes en una instancia de CRM. ```GET: /datasets/{dataset}/tables```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|dataset|cadena|yes|path|Ninguna|Nombre único para la organización de CRM contoso.crm|

#### Respuesta
|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|


## Definiciones de objeto

#### DataSetsMetadata

|Nombre de propiedad | Tipo de datos | Obligatorio|
|---|---|---|
|tabular|not defined|no|
|blob|not defined|no|

#### TabularDataSetsMetadata

|Nombre de propiedad | Tipo de datos |Obligatorio|
|---|---|---|
|de origen|cadena|no|
|DisplayName|cadena|no|
|urlEncoding|cadena|no|
|tableDisplayName|cadena|no|
|tablePluralName|cadena|no|

#### BlobDataSetsMetadata

|Nombre de propiedad | Tipo de datos |Obligatorio|
|---|---|---|
|de origen|cadena|no|
|DisplayName|cadena|no|
|urlEncoding|cadena|no|


#### TableMetadata

|Nombre de propiedad | Tipo de datos |Obligatorio|
|---|---|---|
|name|cadena|no|
|título|cadena|no|
|x-ms-permission|cadena|no|
|schema|not defined|no|

#### DataSetsList

|Nombre de propiedad | Tipo de datos |Obligatorio|
|---|---|---|
|value|array|no|

#### DataSet

|Nombre de propiedad | Tipo de datos |Obligatorio|
|---|---|---|
|Nombre|cadena|no|
|DisplayName|cadena|no|


#### Tabla

|Nombre de propiedad | Tipo de datos |Obligatorio|
|---|---|---|
|Nombre|cadena|no|
|DisplayName|cadena|no|

#### Elemento

|Nombre de propiedad | Tipo de datos |Obligatorio|
|---|---|---|
|ItemInternalId|cadena|no|

#### ItemsList

|Nombre de propiedad | Tipo de datos |Obligatorio|
|---|---|---|
|value|array|no|


#### TablesList

|Nombre de propiedad | Tipo de datos |Obligatorio|
|---|---|---|
|value|array|no|


## Pasos siguientes

[Creación de una aplicación lógica](../app-service-logic/app-service-logic-create-a-logic-app.md).

Volver a la [lista de API](apis-list.md).


[9]: ./media/connectors-create-api-crmonline/aad-tenant-applications-add-appinfo.png
[10]: ./media/connectors-create-api-crmonline/aad-tenant-applications-add-app-properties.png
[12]: ./media/connectors-create-api-crmonline/contoso-aad-app-configure.png
[13]: ./media/connectors-create-api-crmonline/crmconfig1.png
[14]: ./media/connectors-create-api-crmonline/crmconfig2.png
[15]: ./media/connectors-create-api-crmonline/crmconfig3.png

<!---HONumber=AcomDC_0323_2016-->