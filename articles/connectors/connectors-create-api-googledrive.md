<properties
    pageTitle="Incorporación del conector de Google Drive a aplicaciones lógicas | Microsoft Azure"
    description="Información general del conector de Google Drive con parámetros de la API de REST"
    services=""
    suite=""
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
   ms.date="08/18/2016"
   ms.author="mandia"/>

# Introducción al conector de Google Drive
Conéctese a Google Drive para crear archivos, obtener filas, etc. Con Google Drive, puede:

- Compilar el flujo de negocio en función de los datos obtenidos de la búsqueda.
- Usar acciones para buscar imágenes, noticias, etc. Estas acciones obtienen una respuesta y luego dejan el resultado a disposición de otras acciones. Por ejemplo, puede buscar un vídeo y luego usar Twitter para publicar ese vídeo en una fuente de Twitter.

Para agregar una operación en aplicaciones lógicas, consulte [Creación de una nueva aplicación lógica mediante la conexión de servicios de SaaS](../app-service-logic/app-service-logic-create-a-logic-app.md).


## Desencadenadores y acciones
Google Drive incluye las siguientes acciones. No hay desencadenadores.

Desencadenadores | Acciones
--- | ---
None | <ul><li>Crear archivo</li><li>Insertar fila</li><li>Copiar archivo</li><li>Eliminar archivo</li><li>Eliminar fila</li><li>Extraer archivo en carpeta</li><li>Obtener contenido de archivo mediante identificador</li><li>Obtener contenido de archivo mediante ruta de acceso</li><li>Obtener metadatos de archivo mediante identificador</li><li>Obtener metadatos de archivo mediante ruta de acceso</li><li>Obtener fila</li><li>Actualizar archivo</li><li>Actualizar fila</li></ul>

Todos los conectores admiten datos en formato JSON y XML.


## Creación de la conexión a Google Drive

Al agregar este conector a las aplicaciones lógicas, debe autorizar a estas para que se conecten a su Google Drive.

>[AZURE.INCLUDE [Pasos para crear una conexión a Google Drive](../../includes/connectors-create-api-googledrive.md)]

Después de crear la conexión, especifique las propiedades de Google Drive, como la ruta de acceso a la carpeta o el nombre de archivo. La **referencia de la API de REST** de este tema describe estas propiedades.

>[AZURE.TIP] Puede usar esta misma conexión de Google Drive en otras aplicaciones lógicas.


## Referencia de la API de REST de Swagger
Se aplica a la versión: 1.0.

### Crear archivo    
Carga un archivo en Google Drive. ```POST: /datasets/default/files```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Description|
| ---|---|---|---|---|---|
|folderPath|string|yes|query|Ninguna |Ruta de acceso de carpeta para cargar el archivo en Google Drive|
|name|string|yes|query|Ninguna |Nombre del archivo que se va a crear en Google Drive|
|body|string(binary) |yes|body| Ninguna|Contenido del archivo que se va a cargar en Google Drive|

#### Response
|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|


### Insertar fila    
Inserta una fila en una hoja de Google. ```POST: /datasets/{dataset}/tables/{table}/items```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Description|
| ---|---|---|---|---|---|
|dataset|string|yes|path| Ninguna|Identificador único del archivo de hoja de Google|
|table|string|yes|path|Ninguna |Identificador único de la hoja de cálculo|
|item|ItemInternalId: string |yes|body|Ninguna |Fila que se va a insertar en la hoja especificada|

#### Response
|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|


### Copiar archivo    
Copia un archivo en Google Drive. ```POST: /datasets/default/copyFile```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Description|
| ---|---|---|---|---|---|
|de origen|string|yes|query| Ninguna|Dirección URL al archivo de origen|
|de destino|string|yes|query|Ninguna |Ruta de acceso al archivo de destino en Google Drive, incluido el nombre de archivo de destino|
|overwrite|boolean|no|query|Ninguna |Sobrescribe el archivo de destino si está establecido en 'true'|

#### Response
|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|


### Eliminar archivo    
Elimina un archivo de Google Drive. ```DELETE: /datasets/default/files/{id}```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Description|
| ---|---|---|---|---|---|
|id|string|yes|path|Ninguna |Identificador único del archivo que se va a eliminar de Google Drive|

#### Response
|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|


### Eliminar fila    
Elimina una fila de una hoja de Google. ```DELETE: /datasets/{dataset}/tables/{table}/items/{id}```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Description|
| ---|---|---|---|---|---|
|dataset|string|yes|path|Ninguna |Identificador único del archivo de hoja de Google|
|table|string|yes|path|Ninguna |Identificador único de la hoja de cálculo|
|id|string|yes|path|Ninguna |Identificador único de la fila que se va a eliminar|

#### Response
|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|


### Extraer archivo en la carpeta    
Extrae un archivo de almacenamiento en una carpeta de Google Drive (por ejemplo: .zip). ```POST: /datasets/default/extractFolderV2```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Description|
| ---|---|---|---|---|---|
|de origen|string|yes|query|Ninguna |Ruta de acceso al archivo de almacenamiento|
|de destino|string|yes|query|Ninguna |Ruta de acceso de Google Drive para extraer el contenido del archivo|
|overwrite|boolean|no|query|Ninguna |Sobrescribe los archivos de destino si está establecido en 'true'|

#### Response
|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|


### Obtener contenido de archivo mediante el identificador    
Recupera el contenido del archivo de Google Drive mediante el identificador.```GET: /datasets/default/files/{id}/content```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Description|
| ---|---|---|---|---|---|
|id|string|yes|path|Ninguna |Identificador único del archivo que se va a recuperar en Google Drive|

#### Response
|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|


### Obtener contenido de archivo mediante la ruta de acceso    
Recupera el contenido del archivo de Google Drive mediante la ruta de acceso. ```GET: /datasets/default/GetFileContentByPath```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Description|
| ---|---|---|---|---|---|
|path|string|yes|query|Ninguna |Ruta de acceso del archivo de Google Drive|

#### Response
|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|


### Obtener metadatos de archivo mediante el identificador    
Recupera los metadatos del archivo de Google Drive mediante el identificador. ```GET: /datasets/default/files/{id}```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Description|
| ---|---|---|---|---|---|
|id|string|yes|path|Ninguna |Identificador único del archivo en Google Drive|

#### Response
|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|


### Obtener metadatos de archivo mediante la ruta de acceso    
Recupera los metadatos del archivo de Google Drive mediante la ruta de acceso. ```GET: /datasets/default/GetFileByPath```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Description|
| ---|---|---|---|---|---|
|path|string|yes|query|Ninguna |Ruta de acceso del archivo de Google Drive|

#### Response
|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|


### Obtener fila    
Recupera una sola fila de una hoja de Google. ```GET: /datasets/{dataset}/tables/{table}/items/{id}```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Description|
| ---|---|---|---|---|---|
|dataset|string|yes|path|Ninguna |Identificador único del archivo de hoja de Google|
|table|string|yes|path|Ninguna |Identificador único de la hoja de cálculo|
|id|string|yes|path| Ninguna|Identificador único de la fila que se va a recuperar|

#### Response
|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|


### Actualizar archivo    
Actualiza un archivo en Google Drive. ```PUT: /datasets/default/files/{id}```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Description|
| ---|---|---|---|---|---|
|id|string|yes|path|Ninguna |Identificador único del archivo que se va a actualizar en Google Drive|
|body|string(binary) |yes|body| Ninguna|Contenido del archivo que se va a cargar en Google Drive|

#### Response
|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|


### Actualizar fila    
Actualiza una fila en una hoja de Google. ```PATCH: /datasets/{dataset}/tables/{table}/items/{id}```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Description|
| ---|---|---|---|---|---|
|dataset|string|yes|path|Ninguna |Identificador único del archivo de hoja de Google|
|table|string|yes|path| Ninguna|Identificador único de la hoja de cálculo|
|id|string|yes|path|Ninguna |Identificador único de la fila que se va a actualizar|
|item|ItemInternalId: string |yes|body|Ninguna |Fila con valores actualizados|

#### Response
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
|de origen|string|no|
|DisplayName|string|no|
|urlEncoding|string|no|
|tableDisplayName|string|no|
|tablePluralName|string|no|

#### BlobDataSetsMetadata

|Nombre de propiedad | Tipo de datos |Obligatorio|
|---|---|---|
|de origen|string|no|
|DisplayName|string|no|
|urlEncoding|string|no|

#### BlobMetadata

|Nombre de propiedad | Tipo de datos |Obligatorio|
|---|---|---|
|Id|string|no|
|Nombre|string|no|
|DisplayName|string|no|
|Ruta de acceso|string|no|
|LastModified|string|no|
|Tamaño|integer|no|
|MediaType|string|no|
|IsFolder|boolean|no|
|ETag|string|no|
|FileLocator|string|no|

#### TableMetadata

|Nombre de propiedad | Tipo de datos |Obligatorio|
|---|---|---|
|name|string|no|
|título|string|no|
|x-ms-permission|string|no|
|schema|not defined|no|

#### TablesList

|Nombre de propiedad | Tipo de datos |Obligatorio|
|---|---|---|
|value|array|no|

#### Tabla

|Nombre de propiedad | Tipo de datos |Obligatorio|
|---|---|---|
|Nombre|string|no|
|DisplayName|string|no|

#### Elemento

|Nombre de propiedad | Tipo de datos |Obligatorio|
|---|---|---|
|ItemInternalId|string|no|

#### ItemsList

|Nombre de propiedad | Tipo de datos |Obligatorio|
|---|---|---|
|value|array|no|


## Pasos siguientes

[Creación de una aplicación lógica](../app-service-logic/app-service-logic-create-a-logic-app.md)

Volver a la [lista de API](apis-list.md).


<!--References-->
[5]: https://console.developers.google.com/
[6]: ./media/connectors-create-api-googledrive/google-developers-console.png
[8]: ./media/connectors-create-api-googledrive/use-google-apis.png
[9]: ./media/connectors-create-api-googledrive/googledrive-api-overview.png
[10]: ./media/connectors-create-api-googledrive/enable-googledrive-api.png
[12]: ./media/connectors-create-api-googledrive/googledrive-api-credentials-add.png
[13]: ./media/connectors-create-api-googledrive/configure-consent-screen.png
[14]: ./media/connectors-create-api-googledrive/create-client-id.png

<!---HONumber=AcomDC_0824_2016-->