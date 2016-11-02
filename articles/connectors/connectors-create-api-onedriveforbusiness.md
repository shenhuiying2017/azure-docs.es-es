<properties
pageTitle="OneDrive para la Empresa | Microsoft Azure"
description="Cree aplicaciones lógicas con el Servicio de aplicaciones de Azure. Conéctese a OneDrive para la Empresa para administrar sus archivos. Puede realizar diversas acciones en archivos: carga, actualización, obtención y eliminación."
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

# Get started with the OneDrive for Business connector (Introducción al conector de OneDrive para la Empresa)

Conéctese a OneDrive para la Empresa para administrar sus archivos. Puede realizar diversas acciones en archivos: carga, actualización, obtención y eliminación.

>[AZURE.NOTE] Esta versión del artículo se aplica a la versión de esquema 2015-08-01-preview de las aplicaciones lógicas.

Puede empezar creando una aplicación lógica ahora. Para ello, consulte [Creación de una nueva aplicación lógica mediante la conexión de servicios de SaaS](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Desencadenadores y acciones

El conector de OneDrive para la Empresa se puede usar como acción; tiene desencadenadores. Todos los conectores admiten datos en formato JSON y XML.

 El conector de OneDrive para la Empresa tiene disponibles las siguientes acciones o desencadenadores:

### Acciones de OneDrive para la Empresa
Puede realizar estas acciones:

|Acción|Description|
|--- | ---|
|[GetFileMetadata](connectors-create-api-onedriveforbusiness.md#getfilemetadata)|Recupera metadatos de un archivo en OneDrive para la Empresa mediante el identificador|
|[UpdateFile](connectors-create-api-onedriveforbusiness.md#updatefile)|Actualiza un archivo en OneDrive para la Empresa|
|[DeleteFile](connectors-create-api-onedriveforbusiness.md#deletefile)|Elimina un archivo de OneDrive para la Empresa|
|[GetFileMetadataByPath](connectors-create-api-onedriveforbusiness.md#getfilemetadatabypath)|Recupera metadatos de un archivo en OneDrive para la Empresa mediante la ruta de acceso|
|[GetFileContentByPath](connectors-create-api-onedriveforbusiness.md#getfilecontentbypath)|Recupera el contenido de un archivo en OneDrive para la Empresa mediante la ruta de acceso|
|[GetFileContent](connectors-create-api-onedriveforbusiness.md#getfilecontent)|Recupera el contenido de un archivo en OneDrive para la Empresa mediante el identificador|
|[CreateFile](connectors-create-api-onedriveforbusiness.md#createfile)|Carga un archivo en OneDrive para la Empresa|
|[CopyFile](connectors-create-api-onedriveforbusiness.md#copyfile)|Copia un archivo en OneDrive para la Empresa|
|[ListFolder](connectors-create-api-onedriveforbusiness.md#listfolder)|Enumera los archivos en una carpeta de OneDrive para la Empresa|
|[ListRootFolder](connectors-create-api-onedriveforbusiness.md#listrootfolder)|Enumera los archivos en la carpeta raíz de OneDrive para la Empresa|
|[ExtractFolderV2](connectors-create-api-onedriveforbusiness.md#extractfolderv2)|Extrae una carpeta a OneDrive para la Empresa|
### Desencadenadores de OneDrive para la Empresa
Se pueden escuchar estos eventos:

|Desencadenador | Description|
|--- | ---|
|Cuando se crea un archivo|Desencadena un flujo al crearse un nuevo archivo en una carpeta de OneDrive para la Empresa|
|Cuando se modifica un archivo|Desencadena un flujo al modificarse un archivo en una carpeta de OneDrive para la Empresa|


## Creación de una conexión a OneDrive para la Empresa
A fin de crear aplicaciones lógicas con OneDrive para la Empresa, primero debe crear una **conexión** y, después, proporcionar los detalles de las siguientes propiedades:

|Propiedad| Obligatorio|Description|
| ---|---|---|
|SWT|Sí|Proporcionar las credenciales de OneDrive para la Empresa|
Después de crear la conexión, puede usarla para ejecutar las acciones y escuchar los desencadenadores descritos en este artículo.

>[AZURE.INCLUDE [Pasos para crear una conexión a OneDrive para la Empresa](../../includes/connectors-create-api-onedriveforbusiness.md)]

>[AZURE.TIP] Puede usar esta conexión en otras aplicaciones lógicas.

## Referencia para OneDrive para la Empresa
Se aplica a la versión: 1.0

## GetFileMetadata
Obtener metadatos mediante el identificador: recupera metadatos de un archivo en OneDrive para la Empresa mediante el identificador

```GET: /datasets/default/files/{id}```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Description|
| ---|---|---|---|---|---|
|id|string|yes|path|Ninguna|Especificación del archivo|

#### Response

|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|


## UpdateFile
Actualizar archivo: actualiza un archivo en OneDrive para la Empresa

```PUT: /datasets/default/files/{id}```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Description|
| ---|---|---|---|---|---|
|id|string|yes|path|Ninguna|Especificación del archivo que se va a actualizar|
|body| |yes|body|Ninguna|Contenido del archivo que se va a actualizar en OneDrive para la Empresa|

#### Response

|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|


## DeleteFile
Eliminar archivo: elimina un archivo de OneDrive para la Empresa

```DELETE: /datasets/default/files/{id}```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Description|
| ---|---|---|---|---|---|
|id|string|yes|path|Ninguna|Especificación del archivo que se va a eliminar|

#### Response

|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|


## GetFileMetadataByPath
Obtener metadatos de archivo mediante la ruta de acceso: recupera metadatos de un archivo en OneDrive para la Empresa mediante la ruta de acceso

```GET: /datasets/default/GetFileByPath```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Description|
| ---|---|---|---|---|---|
|path|string|yes|query|Ninguna|Ruta de acceso única al archivo en OneDrive para la Empresa|

#### Response

|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|


## GetFileContentByPath
Obtener contenido de archivo mediante la ruta de acceso: recupera contenido de un archivo en OneDrive para la Empresa mediante la ruta de acceso

```GET: /datasets/default/GetFileContentByPath```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Description|
| ---|---|---|---|---|---|
|path|string|yes|query|Ninguna|Ruta de acceso única al archivo en OneDrive para la Empresa|

#### Response

|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|


## GetFileContent
Obtener contenido de archivo mediante el identificador: recupera contenido de un archivo en OneDrive para la Empresa mediante el identificador

```GET: /datasets/default/files/{id}/content```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Description|
| ---|---|---|---|---|---|
|id|string|yes|path|Ninguna|Especificación del archivo|

#### Response

|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|


## CreateFile
Crear archivo: carga un archivo en OneDrive para la Empresa

```POST: /datasets/default/files```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Description|
| ---|---|---|---|---|---|
|folderPath|string|yes|query|Ninguna|Ruta de acceso de carpeta para cargar el archivo en OneDrive para la Empresa|
|name|string|yes|query|Ninguna|Nombre del archivo que se va a crear en OneDrive para la Empresa|
|body| |yes|body|Ninguna|Contenido del archivo que se va a cargar en OneDrive para la Empresa|

#### Response

|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|


## CopyFile
Copiar archivo: copia un archivo en OneDrive para la Empresa

```POST: /datasets/default/copyFile```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Description|
| ---|---|---|---|---|---|
|de origen|string|yes|query|Ninguna|Dirección URL al archivo de origen|
|de destino|string|yes|query|Ninguna|Ruta de acceso al archivo de destino en OneDrive para la Empresa, incluido el nombre de archivo de destino|
|overwrite|boolean|no|query|false|Sobrescribe el archivo de destino si está establecido en 'true'|

#### Response

|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|


## OnNewFile
Cuando se crea un archivo: desencadena un flujo al crearse un nuevo archivo en una carpeta de OneDrive para la Empresa

```GET: /datasets/default/triggers/onnewfile```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Description|
| ---|---|---|---|---|---|
|folderId|string|yes|query|Ninguna|Especificación de una carpeta|

#### Response

|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|


## OnUpdatedFile
Cuando se modifica un archivo: desencadena un flujo al modificarse un archivo en una carpeta de OneDrive para la Empresa

```GET: /datasets/default/triggers/onupdatedfile```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Description|
| ---|---|---|---|---|---|
|folderId|string|yes|query|Ninguna|Especificación de una carpeta|

#### Response

|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|


## ListFolder
Enumerar archivos de la carpeta: enumera los archivos en una carpeta de OneDrive para la Empresa

```GET: /datasets/default/folders/{id}```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Description|
| ---|---|---|---|---|---|
|id|string|yes|path|Ninguna|Especificación de la carpeta|

#### Response

|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|


## ListRootFolder
Enumerar carpeta raíz: enumera los archivos en la carpeta raíz de OneDrive para la Empresa

```GET: /datasets/default/folders```

No hay parámetros para esta llamada
#### Response

|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|


## ExtractFolderV2
Extraer carpeta: extrae una carpeta a OneDrive para la Empresa

```POST: /datasets/default/extractFolderV2```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Description|
| ---|---|---|---|---|---|
|de origen|string|yes|query|Ninguna|Ruta de acceso al archivo de almacenamiento|
|de destino|string|yes|query|Ninguna|Ruta de acceso de OneDrive para la Empresa para extraer el contenido del archivo|
|overwrite|boolean|no|query|false|Sobrescribe los archivos de destino si está establecido en 'true'|

#### Response

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
|de origen|string|No |
|DisplayName|string|No |
|urlEncoding|string|No |
|tableDisplayName|string|No |
|tablePluralName|string|No |



### BlobDataSetsMetadata


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|de origen|string|No |
|DisplayName|string|No |
|urlEncoding|string|No |



### BlobMetadata


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|Id|string|No |
|Nombre|string|No |
|DisplayName|string|No |
|Ruta de acceso|string|No |
|LastModified|string|No |
|Tamaño|integer|No |
|MediaType|string|No |
|IsFolder|boolean|No |
|ETag|string|No |
|FileLocator|string|No |



### Objeto


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|


## Pasos siguientes
[Creación de una aplicación lógica](../app-service-logic/app-service-logic-create-a-logic-app.md)

<!---HONumber=AcomDC_0824_2016-->