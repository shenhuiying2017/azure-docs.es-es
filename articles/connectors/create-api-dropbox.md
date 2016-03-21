<properties
	pageTitle="Incorporación de la API de Dropbox en PowerApps Enterprise o aplicaciones lógicas | Microsoft Azure"
	description="Información general de la API de Dropbox con parámetros de la API de REST"
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
   ms.date="03/02/2016"
   ms.author="mandia"/>

# Introducción a la API de Dropbox 
Conéctese a Dropbox para administrar archivos, por ejemplo, crearlos, obtenerlos, entre otras muchas cosas. La API de Dropbox se puede usar desde:

- Aplicaciones lógicas 
- PowerApps

> [AZURE.SELECTOR]
- [Aplicaciones lógicas](../articles/connectors/create-api-dropbox.md)
- [PowerApps Enterprise](../articles/power-apps/powerapps-create-api-dropbox.md)

&nbsp;

>[AZURE.NOTE] Esta versión del artículo se aplica a la versión de esquema 2015-08-01-preview de las aplicaciones lógicas. Para la versión de esquema 2014-12-01-preview, haga clic en [Conector de Dropbox](../app-service-logic/app-service-logic-connector-dropbox.md).


Con Dropbox, puede hacer lo siguiente:

- Compilar el flujo de negocio en función de los datos que obtiene de Dropbox. 
- Usar desencadenadores para cuando se crea o actualiza un archivo.
- Usar acciones para crear o eliminar un archivo, entre otras muchas cosas. Estas acciones obtienen una respuesta y luego dejan el resultado a disposición de otras acciones. Por ejemplo, cuando se cree un nuevo archivo en Dropbox, puede enviar ese archivo por correo electrónico mediante Office 365.
- Agregar la API de Dropbox a PowerApps Enterprise. Así los usuarios pueden usar esta API en sus aplicaciones. 

Si desea información sobre cómo agregar una API en PowerApps Enterprise, acuda a [Registro de una API administrada por Microsoft o una API administrada por TI](../power-apps/powerapps-register-from-available-apis.md).

Para agregar una operación en aplicaciones lógicas, consulte [Creación de una nueva aplicación lógica mediante la conexión de servicios de SaaS](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Desencadenadores y acciones
Dropbox incluye los desencadenadores y las acciones siguientes.

Desencadenadores | Acciones
--- | ---
<ul><li>Cuando se crea un archivo</li><li>Cuando se modifica un archivo</li></ul> | <ul><li>Crear archivo</li><li>Cuando se crea un archivo</li><li>Copiar archivo</li><li>Eliminar archivo</li><li>Extraer archivo en carpeta</li><li>Obtener contenido de archivo mediante identificador</li><li>Obtener archivo mediante ruta de acceso</li><li>Obtener metadatos de archivo mediante identificador</li><li>Obtener metadatos de archivo mediante ruta de acceso</li><li>Actualizar archivo</li><li>Cuando se modifica un archivo</li></ul>

Todas las API admiten datos en formato JSON y XML.

## Creación de la conexión a Dropbox

Cuando agregue esta API a las aplicaciones lógicas, debe autorizar a estas para que se conecten a su instancia de Dropbox.

1. Inicie sesión en su cuenta de Dropbox.
2. Seleccione **Authorize** (Autorizar) y permita que sus aplicaciones lógicas se conecten a Dropbox y utilicen esta aplicación. 

Después de crear la conexión, especifique las propiedades de Dropbox, como el nombre de archivo o la ruta de acceso de la carpeta. En la **referencia de la API de REST** de este tema se describen estas propiedades.

>[AZURE.TIP] Puede usar esta misma conexión de Dropbox en otras aplicaciones lógicas.

## Referencia de la API de REST de Swagger
Se aplica a la versión: 1.0.

### Crear archivo    
Carga un archivo en Dropbox. ```POST: /datasets/default/files```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|folderPath|cadena|yes|query|Ninguna |Ruta de acceso de la carpeta para cargar el archivo en Dropbox|
|name|cadena|yes|query|Ninguna |Nombre del archivo que se va a crear en Dropbox|
|body|string(binary) |yes|body|Ninguna |Contenido del archivo que se va a cargar en Dropbox|

#### Respuesta
|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|


### Cuando se crea un archivo    
Desencadena un flujo cuando se crea un nuevo archivo en una carpeta de Dropbox. ```GET: /datasets/default/triggers/onnewfile```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|folderId|cadena|yes|query|Ninguna |Identificador único de la carpeta en Dropbox|

#### Respuesta
|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|


### Copiar archivo    
Copia un archivo en Dropbox. ```POST: /datasets/default/copyFile```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|de origen|cadena|yes|query|Ninguna |Dirección URL al archivo de origen|
|de destino|cadena|yes|query| Ninguna|Ruta de acceso al archivo de destino en Dropbox, incluido el nombre del archivo de destino|
|overwrite|boolean|no|query|Ninguna |Sobrescribe el archivo de destino si está establecido en 'true'|

#### Respuesta
|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|


### Eliminar archivo    
Elimina un archivo de Dropbox. ```DELETE: /datasets/default/files/{id}```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|id|cadena|yes|path|Ninguna|Identificador único del archivo que se va a eliminar de Dropbox|

#### Respuesta
|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|


### Extraer archivo en la carpeta    
Extrae un archivo de almacenamiento en una carpeta de Dropbox (por ejemplo: .zip). **```POST: /datasets/default/extractFolderV2```**

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|de origen|cadena|yes|query|Ninguna |Ruta de acceso al archivo de almacenamiento|
|de destino|cadena|yes|query|Ninguna |Ruta de acceso en Dropbox para extraer el contenido del archivo|
|overwrite|boolean|no|query|Ninguna |Sobrescribe los archivos de destino si está establecido en 'true'|

#### Respuesta
|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|


### Obtener contenido de archivo mediante el identificador    
Recupera el contenido del archivo de Dropbox mediante el identificador. ```GET: /datasets/default/files/{id}/content```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|id|cadena|yes|path|Ninguna |Identificador único del archivo en Dropbox|

#### Respuesta
|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|


### Obtener contenido de archivo mediante la ruta de acceso    
Recupera el contenido del archivo de Dropbox mediante la ruta de acceso. ```GET: /datasets/default/GetFileContentByPath```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|path|cadena|yes|query|Ninguna |Ruta de acceso única al archivo en Dropbox|

#### Respuesta
|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|


### Obtener metadatos de archivo mediante el identificador    
Recupera los metadatos del archivo de Dropbox mediante el identificador de archivo. ```GET: /datasets/default/files/{id}```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|id|cadena|yes|path|Ninguna |Identificador único del archivo en Dropbox|

#### Respuesta
|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|


### Obtener metadatos de archivo mediante la ruta de acceso    
Recupera los metadatos del archivo de Dropbox mediante la ruta de acceso. ```GET: /datasets/default/GetFileByPath```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|path|cadena|yes|query|Ninguna |Ruta de acceso única al archivo en Dropbox|

#### Respuesta
|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|


### Actualizar archivo    
Actualiza un archivo en Dropbox. ```PUT: /datasets/default/files/{id}```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|id|cadena|yes|path| Ninguna|Identificador único del archivo que se va a actualizar en Dropbox|
|body|string(binary) |yes|body|Ninguna |Contenido del archivo que se va a actualizar en Dropbox|

#### Respuesta
|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|


### Cuando se modifica un archivo    
Desencadena un flujo cuando se modifica un archivo en una carpeta de Dropbox. ```GET: /datasets/default/triggers/onupdatedfile```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|folderId|cadena|yes|query|Ninguna |Identificador único de la carpeta en Dropbox|

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

#### BlobMetadata

|Nombre de propiedad | Tipo de datos |Obligatorio|
|---|---|---|
|Id|cadena|no|
|Nombre|cadena|no|
|DisplayName|cadena|no|
|Ruta de acceso|cadena|no|
|LastModified|cadena|no|
|Tamaño|integer|no|
|MediaType|cadena|no|
|IsFolder|boolean|no|
|ETag|cadena|no|
|FileLocator|cadena|no|

## Pasos siguientes

[Crear una aplicación lógica](../app-service-logic/app-service-logic-create-a-logic-app.md).

Volver a la [lista de API](apis-list.md).


<!--References-->
[1]: https://www.dropbox.com/login
[2]: https://www.dropbox.com/developers/apps/create
[3]: https://www.dropbox.com/developers/apps
[8]: ./media/create-api-dropbox/dropbox-developer-site.png
[9]: ./media/create-api-dropbox/dropbox-create-app.png
[10]: ./media/create-api-dropbox/dropbox-create-app-page1.png
[11]: ./media/create-api-dropbox/dropbox-create-app-page2.png

<!---HONumber=AcomDC_0309_2016-->