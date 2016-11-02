<properties
pageTitle="Uso del conector de SharePoint Online en las aplicaciones lógicas | Microsoft Azure"
description="Introducción al uso del conector de SharePoint Online del Servicio de aplicaciones de Azure en las aplicaciones lógicas."
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
ms.date="08/18/2016"
ms.author="deonhe"/>

# Introducción al conector de SharePoint Online 

El conector de SharePoint ofrece una manera de trabajar con listas en SharePoint.

>[AZURE.NOTE] Esta versión del artículo se aplica a la versión de esquema 2015-08-01-preview de las aplicaciones lógicas.


Para agregar una operación en aplicaciones lógicas, consulte [Creación de una nueva aplicación lógica mediante la conexión de servicios de SaaS](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Hablemos de acciones y desencadenadores

El conector de SharePoint se puede usar como acción; tiene desencadenadores. Todos los conectores admiten datos en formato JSON y XML.

El conector de SharePoint tiene las siguientes acciones o desencadenadores disponibles:

### Acciones de SharePoint
Puede realizar estas acciones:

|Acción|Description|
|--- | ---|
|GetFileMetadata|Se usa para obtener los metadatos de un archivo en la biblioteca de documentos|
|UpdateFile|Se usa para actualizar un archivo en la biblioteca de documentos|
|DeleteFile|Se usa para eliminar un archivo en la biblioteca de documentos|
|GetFileMetadataByPath|Se usa para obtener los metadatos de un archivo en la biblioteca de documentos|
|GetFileContentByPath|Se usa para obtener un archivo en la biblioteca de documentos|
|GetFileContent|Se usa para obtener un archivo en la biblioteca de documentos|
|CreateFile|Se usa para cargar un archivo en la biblioteca de documentos|
|CopyFile|Se usa para copiar un archivo en la biblioteca de documentos|
|ExtractFolderV2|Se usa para extraer un archivo en la biblioteca de documentos|
|PostItem|Crea un elemento en una lista de SharePoint|
|GetItem|Recupera un solo elemento de una lista de SharePoint|
|DeleteItem|Elimina un elemento de una lista de SharePoint|
|PatchItem|Actualiza un elemento de una lista de SharePoint|
### Desencadenadores de SharePoint
Se pueden escuchar estos eventos:

|Desencadenador | Description|
|--- | ---|
|OnNewFile|Desencadena un flujo al crear un archivo en una carpeta de SharePoint|
|OnUpdatedFile|Desencadena un flujo al modificar un archivo en una carpeta de SharePoint|
|GetOnNewItems|Al crear un elemento en una lista de SharePoint|
|GetOnUpdatedItems|Al modificar un elemento existente en una lista de SharePoint|


## Creación de una conexión a SharePoint
Para usar el conector de SharePoint, cree primero una **conexión** y, después, especifique los detalles de las siguientes propiedades:

|Propiedad| Obligatorio|Description|
| ---|---|---|
|SWT|Sí|Proporcionar credenciales de SharePoint|

Para poder establecer la conexión con **SharePoint Online**, tiene que proporcionar su identidad (nombre de usuario y contraseña, credenciales de tarjeta inteligente, etc.) a SharePoint Online. Una vez que se ha autenticado, podrá usar el conector de SharePoint Online en la aplicación lógica.

Mientras se encuentra en el diseñador de la aplicación lógica, siga estos pasos para iniciar sesión en SharePoint y crear la **conexión** que usará en la aplicación lógica:

1. Escriba "SharePoint" en el cuadro de búsqueda y espere a que aparezcan todas las entradas que incluyan "SharePoint" en el nombre: ![Configurar SharePoint][1]
2. Seleccione **SharePoint Online - When a file is created** (SharePoint Online - cuando se crea un archivo).
3. Seleccione **Sign in to SharePoint Online** (Iniciar sesión en SharePoint Online): ![Configurar SharePoint][2]
4. Especifique sus credenciales de SharePoint para iniciar sesión y autenticarse con SharePoint ![Configurar SharePoint][3]
5. Cuando finalice la autenticación, se le redirigirá a la aplicación lógica para que la complete configurando el cuadro de diálogo de SharePoint **When a file is created** (Cuando se crea un archivo). ![Configurar SharePoint][4]
6. A continuación, puede agregar otros desencadenadores y acciones que necesita para completar la aplicación lógica.
7. Guarde el trabajo seleccionando **Guardar** en la barra de menús superior.


## Referencia de la API de REST de SharePoint
#### Esta documentación corresponde a la versión: 1.0


### Se usa para obtener los metadatos de un archivo en la biblioteca de documentos
**```GET: /datasets/{dataset}/files/{id}```**



| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Description|
| ---|---|---|---|---|---|
|dataset|string|yes|path|Ninguna|Dirección URL del sitio de SharePoint. P. ej., http://contoso.sharepoint.com/sites/mysite|.
|id|string|yes|path|Ninguna|Identificador único del archivo|


### Estas son las posibles respuestas:

|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|
------



### Se usa para actualizar un archivo en la biblioteca de documentos
**```PUT: /datasets/{dataset}/files/{id}```**



| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Description|
| ---|---|---|---|---|---|
|dataset|string|yes|path|Ninguna|Dirección URL del sitio de SharePoint. P. ej., http://contoso.sharepoint.com/sites/mysite|.
|id|string|yes|path|Ninguna|Identificador único del archivo|
|body| |yes|body|Ninguna|Contenido del archivo:|


### Estas son las posibles respuestas:

|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|
------



### Se usa para eliminar un archivo en la biblioteca de documentos
**```DELETE: /datasets/{dataset}/files/{id}```**



| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Description|
| ---|---|---|---|---|---|
|dataset|string|yes|path|Ninguna|Dirección URL del sitio de SharePoint. P. ej., http://contoso.sharepoint.com/sites/mysite|.
|id|string|yes|path|Ninguna|Identificador único del archivo|


### Estas son las posibles respuestas:

|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|
------



### Se usa para obtener los metadatos de un archivo en la biblioteca de documentos
**```GET: /datasets/{dataset}/GetFileByPath```**



| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Description|
| ---|---|---|---|---|---|
|dataset|string|yes|path|Ninguna|Dirección URL del sitio de SharePoint. P. ej., http://contoso.sharepoint.com/sites/mysite|.
|path|string|yes|query|Ninguna|Ruta de acceso del archivo|


### Estas son las posibles respuestas:

|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|
------



### Se usa para obtener un archivo en la biblioteca de documentos
**```GET: /datasets/{dataset}/GetFileContentByPath```**



| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Description|
| ---|---|---|---|---|---|
|dataset|string|yes|path|Ninguna|Dirección URL del sitio de SharePoint. P. ej., http://contoso.sharepoint.com/sites/mysite|.
|path|string|yes|query|Ninguna|Ruta de acceso del archivo|


### Estas son las posibles respuestas:

|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|
------



### Se usa para obtener un archivo en la biblioteca de documentos
**```GET: /datasets/{dataset}/files/{id}/content```**



| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Description|
| ---|---|---|---|---|---|
|dataset|string|yes|path|Ninguna|Dirección URL del sitio de SharePoint. P. ej., http://contoso.sharepoint.com/sites/mysite|.
|id|string|yes|path|Ninguna|Identificador único del archivo|


### Estas son las posibles respuestas:

|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|
------



### Se usa para cargar un archivo en la biblioteca de documentos
**```POST: /datasets/{dataset}/files```**



| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Description|
| ---|---|---|---|---|---|
|dataset|string|yes|path|Ninguna|Dirección URL del sitio de SharePoint. P. ej., http://contoso.sharepoint.com/sites/mysite|.
|folderPath|string|yes|query|Ninguna|Ruta de acceso a la carpeta|
|name|string|yes|query|Ninguna|Nombre del archivo|
|body| |yes|body|Ninguna|Contenido del archivo:|


### Estas son las posibles respuestas:

|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|
------



### Se usa para copiar un archivo en la biblioteca de documentos
**```POST: /datasets/{dataset}/copyFile```**



| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Description|
| ---|---|---|---|---|---|
|dataset|string|yes|path|Ninguna|Dirección URL del sitio de SharePoint. P. ej., http://contoso.sharepoint.com/sites/mysite|.
|de origen|string|yes|query|Ninguna|Ruta de acceso al archivo de origen|
|de destino|string|yes|query|Ninguna|Ruta de acceso al archivo de destino|
|overwrite|boolean|no|query|false|Especifica si se sobrescribe un archivo existente|


### Estas son las posibles respuestas:

|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|
------



### Desencadena un flujo al crear un archivo en una carpeta de SharePoint
**```GET: /datasets/{dataset}/triggers/onnewfile```**



| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Description|
| ---|---|---|---|---|---|
|dataset|string|yes|path|Ninguna|Dirección URL del sitio de SharePoint|
|folderId|string|yes|query|Ninguna|Identificador único de la carpeta en SharePoint|


### Estas son las posibles respuestas:

|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|
------



### Desencadena un flujo al modificar un archivo en una carpeta de SharePoint
**```GET: /datasets/{dataset}/triggers/onupdatedfile```**



| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Description|
| ---|---|---|---|---|---|
|dataset|string|yes|path|Ninguna|Dirección URL del sitio de SharePoint|
|folderId|string|yes|query|Ninguna|Identificador único de la carpeta en SharePoint|


### Estas son las posibles respuestas:

|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|
------



### Se usa para extraer un archivo en la biblioteca de documentos
**```POST: /datasets/{dataset}/extractFolderV2```**



| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Description|
| ---|---|---|---|---|---|
|dataset|string|yes|path|Ninguna|Dirección URL del sitio de SharePoint. P. ej., http://contoso.sharepoint.com/sites/mysite|.
|de origen|string|yes|query|Ninguna|Ruta de acceso al archivo de origen|
|de destino|string|yes|query|Ninguna|Ruta de acceso a la carpeta de destino|
|overwrite|boolean|no|query|false|Especifica si se sobrescribe un archivo existente|


### Estas son las posibles respuestas:

|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|
------



### Al crear un elemento en una lista de SharePoint
**```GET: /datasets/{dataset}/tables/{table}/onnewitems```**



| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Description|
| ---|---|---|---|---|---|
|dataset|string|yes|path|Ninguna|Dirección URL del sitio de SharePoint (ejemplo: http://contoso.sharepoint.com/sites/mysite)|
|table|string|yes|path|Ninguna|Nombre de lista de SharePoint|
|$skip|integer|no|query|Ninguna|Número de entradas para omitir (valor predeterminado = 0)|
|$top|integer|no|query|Ninguna|Número máximo de entradas para recuperar (valor predeterminado = 256)|
|$filter|string|no|query|Ninguna|Consulta de filtro de ODATA para restringir el número de entradas|
|$orderby|string|no|query|Ninguna|Consulta orderBy de ODATA para especificar el orden de las entradas|


### Estas son las posibles respuestas:

|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|
------



### Al modificar un elemento existente en una lista de SharePoint
**```GET: /datasets/{dataset}/tables/{table}/onupdateditems```**



| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Description|
| ---|---|---|---|---|---|
|dataset|string|yes|path|Ninguna|Dirección URL del sitio de SharePoint (ejemplo: http://contoso.sharepoint.com/sites/mysite)|
|table|string|yes|path|Ninguna|Nombre de lista de SharePoint|
|$skip|integer|no|query|Ninguna|Número de entradas para omitir (valor predeterminado = 0)|
|$top|integer|no|query|Ninguna|Número máximo de entradas para recuperar (valor predeterminado = 256)|
|$filter|string|no|query|Ninguna|Consulta de filtro de ODATA para restringir el número de entradas|
|$orderby|string|no|query|Ninguna|Consulta orderBy de ODATA para especificar el orden de las entradas|


### Estas son las posibles respuestas:

|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|
------



### Crea un elemento en una lista de SharePoint
**```POST: /datasets/{dataset}/tables/{table}/items```**



| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Description|
| ---|---|---|---|---|---|
|dataset|string|yes|path|Ninguna|Dirección URL del sitio de SharePoint (ejemplo: http://contoso.sharepoint.com/sites/mysite)|
|table|string|yes|path|Ninguna|Nombre de lista de SharePoint|
|item| |yes|body|Ninguna|Elemento que se va a crear|


### Estas son las posibles respuestas:

|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|
------



### Recupera un solo elemento de una lista de SharePoint
**```GET: /datasets/{dataset}/tables/{table}/items/{id}```**



| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Description|
| ---|---|---|---|---|---|
|dataset|string|yes|path|Ninguna|Dirección URL del sitio de SharePoint (ejemplo: http://contoso.sharepoint.com/sites/mysite)|
|table|string|yes|path|Ninguna|Nombre de lista de SharePoint|
|id|integer|yes|path|Ninguna|Identificador único del elemento que se va a recuperar|


### Estas son las posibles respuestas:

|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|
------



### Elimina un elemento de una lista de SharePoint
**```DELETE: /datasets/{dataset}/tables/{table}/items/{id}```**



| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Description|
| ---|---|---|---|---|---|
|dataset|string|yes|path|Ninguna|Dirección URL del sitio de SharePoint (ejemplo: http://contoso.sharepoint.com/sites/mysite)|
|table|string|yes|path|Ninguna|Nombre de lista de SharePoint|
|id|integer|yes|path|Ninguna|Identificador único del elemento que se va a eliminar|


### Estas son las posibles respuestas:

|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|
------



### Actualiza un elemento de una lista de SharePoint
**```PATCH: /datasets/{dataset}/tables/{table}/items/{id}```**



| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Description|
| ---|---|---|---|---|---|
|dataset|string|yes|path|Ninguna|Dirección URL del sitio de SharePoint (ejemplo: http://contoso.sharepoint.com/sites/mysite)|
|table|string|yes|path|Ninguna|Nombre de lista de SharePoint|
|id|integer|yes|path|Ninguna|Identificador único del elemento que se va a actualizar|
|item| |yes|body|Ninguna|Elemento con propiedades cambiadas|


### Estas son las posibles respuestas:

|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|
------



## Definiciones de objeto: 

 **DataSetsMetadata**:

Propiedades obligatorias para DataSetsMetadata:


Ninguna de las propiedades es obligatoria.


**Todas las propiedades**:


| Nombre | Tipo de datos |
|---|---|
|tabular|not defined|
|blob|not defined|



 **TabularDataSetsMetadata**:

Propiedades obligatorias para TabularDataSetsMetadata:


Ninguna de las propiedades es obligatoria.


**Todas las propiedades**:


| Nombre | Tipo de datos |
|---|---|
|de origen|string|
|DisplayName|string|
|urlEncoding|string|
|tableDisplayName|string|
|tablePluralName|string|



 **BlobDataSetsMetadata**:

Propiedades obligatorias para BlobDataSetsMetadata:


Ninguna de las propiedades es obligatoria.


**Todas las propiedades**:


| Nombre | Tipo de datos |
|---|---|
|de origen|string|
|DisplayName|string|
|urlEncoding|string|



 **BlobMetadata**:

Propiedades obligatorias para BlobMetadata:


Ninguna de las propiedades es obligatoria.


**Todas las propiedades**:


| Nombre | Tipo de datos |
|---|---|
|Id|string|
|Nombre|string|
|DisplayName|string|
|Ruta de acceso|string|
|LastModified|string|
|Tamaño|integer|
|MediaType|string|
|IsFolder|boolean|
|ETag|string|
|FileLocator|string|



 **Object**:

Propiedades obligatorias para Object:


Ninguna de las propiedades es obligatoria.


**Todas las propiedades**:


| Nombre | Tipo de datos |
|---|---|



 **TableMetadata**:

Propiedades obligatorias para TableMetadata:


Ninguna de las propiedades es obligatoria.


**Todas las propiedades**:


| Nombre | Tipo de datos |
|---|---|
|name|string|
|título|string|
|x-ms-permission|string|
|schema|not defined|



 **DataSetsList**:

Propiedades obligatorias para DataSetsList:


Ninguna de las propiedades es obligatoria.


**Todas las propiedades**:


| Nombre | Tipo de datos |
|---|---|
|value|array|



 **DataSet**:

Propiedades obligatorias para DataSet:


Ninguna de las propiedades es obligatoria.


**Todas las propiedades**:


| Nombre | Tipo de datos |
|---|---|
|Nombre|string|
|DisplayName|string|



 **Tabla**:

Propiedades obligatorias para Table:


Ninguna de las propiedades es obligatoria.


**Todas las propiedades**:


| Nombre | Tipo de datos |
|---|---|
|Nombre|string|
|DisplayName|string|



 **Item**:

Propiedades obligatorias para Item:


Ninguna de las propiedades es obligatoria.


**Todas las propiedades**:


| Nombre | Tipo de datos |
|---|---|
|ItemInternalId|string|



 **ItemsList**:

Propiedades obligatorias para ItemsList:


Ninguna de las propiedades es obligatoria.


**Todas las propiedades**:


| Nombre | Tipo de datos |
|---|---|
|value|array|



 **TablesList**:

Propiedades obligatorias para TablesList:


Ninguna de las propiedades es obligatoria.


**Todas las propiedades**:


| Nombre | Tipo de datos |
|---|---|
|value|array|


## Pasos siguientes
[Creación de una aplicación lógica](../app-service-logic/app-service-logic-create-a-logic-app.md)

[1]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig1.png
[2]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig2.png
[3]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig3.png
[4]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig4.png
[5]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig5.png

<!---HONumber=AcomDC_0824_2016-->