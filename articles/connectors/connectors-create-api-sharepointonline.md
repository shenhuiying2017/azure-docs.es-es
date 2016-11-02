<properties
pageTitle="Aprenda a utilizar el conector de SharePoint Online en las aplicaciones lógicas | Microsoft Azure"
description="Cree aplicaciones lógicas con el conector de SharePoint Online para administrar listas en SharePoint."
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
ms.date="07/19/2016"
ms.author="deonhe"/>

# Introducción al conector de SharePoint Online

Use el conector de SharePoint Online para administrar las listas de SharePoint.

Para poder usar [un conector](./apis-list.md), primero debe crear una aplicación lógica. Por tanto, puede comenzar [creando una aplicación lógica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Conexión a SharePoint Online

Para que la aplicación lógica pueda acceder a un servicio, primero debe crear una *conexión* con dicho servicio. Una [conexión](./connectors-overview.md) proporciona conectividad entre una aplicación lógica y otro servicio.

### Creación de una conexión a SharePoint Online

>[AZURE.INCLUDE [Pasos para crear una conexión a SharePoint](../../includes/connectors-create-api-sharepointonline.md)]

## Uso de un desencadenador de SharePoint Online

Un desencadenador es un evento que se puede utilizar para iniciar el flujo de trabajo definido en una aplicación lógica. [Más información sobre los desencadenadores](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts).

>[AZURE.INCLUDE [Pasos para crear un desencadenador de SharePoint Online](../../includes/connectors-create-api-sharepointonline-trigger.md)]

## Uso de una acción de SharePoint Online

Una acción es una operación que se lleva a cabo mediante el flujo de trabajo definido en una aplicación lógica. [Más información sobre las acciones](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts).

>[AZURE.INCLUDE [Pasos para crear una acción de SharePoint Online](../../includes/connectors-create-api-sharepointonline-action.md)]

## Detalles técnicos

Estos son los detalles sobre los desencadenadores, las acciones y las respuestas compatibles con esta conexión:

## Desencadenadores de SharePoint Online

SharePoint tiene el siguiente desencadenador o desencadenadores:

|Desencadenador | Descripción|
|--- | ---|
|[Cuando se crea un archivo](connectors-create-api-sharepointonline.md#when-a-file-is-created)|Esta operación desencadena un flujo al crear un archivo en una carpeta de SharePoint.|
|[Cuando se modifica un archivo](connectors-create-api-sharepointonline.md#when-a-file-is-modified)|Esta operación desencadena un flujo al modificar un archivo en una carpeta de SharePoint.|
|[Cuando se crea un elemento](connectors-create-api-sharepointonline.md#when-a-new-item-is-created)|Esta operación desencadena un flujo al crear un elemento en una lista de SharePoint.|
|[Cuando se modifica un elemento existente](connectors-create-api-sharepointonline.md#when-an-existing-item-is-modified)|Esta operación desencadena un flujo al modificar un elemento existente en una lista de SharePoint.|


## Acciones de SharePoint Online

SharePoint tiene las siguientes acciones:


|Acción|Descripción|
|--- | ---|
|[Obtención de metadatos de archivo](connectors-create-api-sharepointonline.md#get-file-metadata)|Esta operación obtiene metadatos de archivo mediante el id. del archivo.|
|[Actualizar archivo](connectors-create-api-sharepointonline.md#update-file)|Esta operación actualiza el contenido del archivo.|
|[Eliminar archivo](connectors-create-api-sharepointonline.md#delete-file)|Esta operación elimina un archivo.|
|[Obtener metadatos de archivo mediante la ruta de acceso](connectors-create-api-sharepointonline.md#get-file-metadata-using-path)|Esta operación obtiene metadatos de archivo mediante la ruta de acceso del archivo.|
|[Obtener contenido de archivo mediante la ruta de acceso](connectors-create-api-sharepointonline.md#get-file-content-using-path)|Esta operación obtiene el contenido de archivo mediante la ruta de acceso del archivo.|
|[Obtener contenido de archivo](connectors-create-api-sharepointonline.md#get-file-content)|Esta operación obtiene el contenido de archivo mediante el id. del archivo.|
|[Crear archivo](connectors-create-api-sharepointonline.md#create-file)|Esta operación carga un archivo en un sitio de SharePoint.|
|[Copiar archivo](connectors-create-api-sharepointonline.md#copy-file)|Esta operación copia un archivo en un sitio de SharePoint.|
|[Enumerar carpeta](connectors-create-api-sharepointonline.md#list-folder)|Esta operación obtiene los archivos contenidos en una carpeta de SharePoint.|
|[Enumerar carpeta raíz](connectors-create-api-sharepointonline.md#list-root-folder)|Esta operación obtiene los archivos en la carpeta raíz de SharePoint.|
|[Extraer carpeta](connectors-create-api-sharepointonline.md#extract-folder)|Esta operación extrae un archivo de almacenamiento en una carpeta de SharePoint (por ejemplo: .zip).|
|[Obtener elementos](connectors-create-api-sharepointonline.md#get-items)|Esta operación obtiene elementos de una lista de SharePoint.|
|[Crear elemento](connectors-create-api-sharepointonline.md#create-item)|Esta operación obtiene un elemento nuevo de una lista de SharePoint.|
|[Obtener elemento](connectors-create-api-sharepointonline.md#get-item)|Esta operación obtiene un elemento único por su id. de una lista de SharePoint.|
|[Eliminar elemento](connectors-create-api-sharepointonline.md#delete-item)|Esta operación elimina un elemento de una lista de SharePoint.|
|[Actualizar elemento](connectors-create-api-sharepointonline.md#update-item)|Esta operación actualiza un elemento de una lista de SharePoint.|
|[Obtener valores de entidad](connectors-create-api-sharepointonline.md#get-entity-values)|Esta operación obtiene los valores posibles para una entidad de SharePoint.|
|[Obtener listas](connectors-create-api-sharepointonline.md#get-lists)|Esta operación obtiene las listas de SharePoint desde un sitio.|
### Detalles de la acción

Estos son los detalles de las acciones y desencadenadores de este conector, junto con sus respuestas:



### Obtención de metadatos de archivo
Esta operación obtiene metadatos de archivo mediante el id. del archivo.


|Nombre de propiedad| Display Name (Nombre para mostrar)|Descripción|
| ---|---|---|
|dataset*|Dirección URL del sitio|Dirección URL del sitio de SharePoint como http://contoso.sharepoint.com/sites/mysite|
|id*|Identificador de archivo|Seleccionar un archivo|

El símbolo * indica que la propiedad es obligatoria.

#### Detalles de salida

BlobMetadata


| Nombre de propiedad | Tipo de datos |
|---|---|---|
|Id|cadena|
|Nombre|cadena|
|DisplayName|cadena|
|Ruta de acceso|cadena|
|LastModified|cadena|
|Tamaño|integer|
|MediaType|cadena|
|IsFolder|boolean|
|ETag|cadena|
|FileLocator|cadena|




### Actualizar archivo
Esta operación actualiza el contenido del archivo.


|Nombre de propiedad| Display Name (Nombre para mostrar)|Descripción|
| ---|---|---|
|dataset*|Dirección URL del sitio|Dirección URL del sitio de SharePoint como http://contoso.sharepoint.com/sites/mysite|
|id*|Identificador de archivo|Seleccionar un archivo|
|body*|Contenido del archivo|Contenido del archivo|

El símbolo * indica que la propiedad es obligatoria.

#### Detalles de salida

BlobMetadata


| Nombre de propiedad | Tipo de datos |
|---|---|---|
|Id|cadena|
|Nombre|cadena|
|DisplayName|cadena|
|Ruta de acceso|cadena|
|LastModified|cadena|
|Tamaño|integer|
|MediaType|cadena|
|IsFolder|boolean|
|ETag|cadena|
|FileLocator|cadena|




### Eliminar archivo
Esta operación elimina un archivo.


|Nombre de propiedad| Display Name (Nombre para mostrar)|Descripción|
| ---|---|---|
|dataset*|Dirección URL del sitio|Dirección URL del sitio de SharePoint como http://contoso.sharepoint.com/sites/mysite|
|id*|Identificador de archivo|Seleccionar un archivo|

El símbolo * indica que la propiedad es obligatoria.




### Obtener metadatos de archivo mediante la ruta de acceso
Esta operación obtiene metadatos de archivo mediante la ruta de acceso del archivo.


|Nombre de propiedad| Display Name (Nombre para mostrar)|Descripción|
| ---|---|---|
|dataset*|Dirección URL del sitio|Dirección URL del sitio de SharePoint como http://contoso.sharepoint.com/sites/mysite|
|path*|Ruta de acceso del archivo|Seleccionar un archivo|

El símbolo * indica que la propiedad es obligatoria.

#### Detalles de salida

BlobMetadata


| Nombre de propiedad | Tipo de datos |
|---|---|---|
|Id|cadena|
|Nombre|cadena|
|DisplayName|cadena|
|Ruta de acceso|cadena|
|LastModified|cadena|
|Tamaño|integer|
|MediaType|cadena|
|IsFolder|boolean|
|ETag|cadena|
|FileLocator|cadena|




### Obtener contenido de archivo mediante la ruta de acceso
Esta operación obtiene el contenido de archivo mediante la ruta de acceso del archivo.


|Nombre de propiedad| Display Name (Nombre para mostrar)|Descripción|
| ---|---|---|
|dataset*|Dirección URL del sitio|Dirección URL del sitio de SharePoint como http://contoso.sharepoint.com/sites/mysite|
|path*|Ruta de acceso del archivo|Seleccionar un archivo|

El símbolo * indica que la propiedad es obligatoria.




### Obtener contenido de archivo
Esta operación obtiene el contenido de archivo mediante el id. del archivo.


|Nombre de propiedad| Display Name (Nombre para mostrar)|Descripción|
| ---|---|---|
|dataset*|Dirección URL del sitio|Dirección URL del sitio de SharePoint como http://contoso.sharepoint.com/sites/mysite|
|id*|Identificador de archivo|Seleccionar un archivo|

El símbolo * indica que la propiedad es obligatoria.




### Crear archivo
Esta operación carga un archivo en un sitio de SharePoint.


|Nombre de propiedad| Display Name (Nombre para mostrar)|Descripción|
| ---|---|---|
|dataset*|Dirección URL del sitio|Dirección URL del sitio de SharePoint como http://contoso.sharepoint.com/sites/mysite|
|folderPath*|Ruta de acceso a la carpeta|Seleccionar un archivo|
|name*|Nombre de archivo|Nombre del archivo|
|body*|Contenido del archivo|Contenido del archivo|

El símbolo * indica que la propiedad es obligatoria.

#### Detalles de salida

BlobMetadata


| Nombre de propiedad | Tipo de datos |
|---|---|---|
|Id|cadena|
|Nombre|cadena|
|DisplayName|cadena|
|Ruta de acceso|cadena|
|LastModified|cadena|
|Tamaño|integer|
|MediaType|cadena|
|IsFolder|boolean|
|ETag|cadena|
|FileLocator|cadena|




### Copiar archivo
Esta operación copia un archivo en un sitio de SharePoint.


|Nombre de propiedad| Display Name (Nombre para mostrar)|Descripción|
| ---|---|---|
|dataset*|Dirección URL del sitio|Dirección URL del sitio de SharePoint como http://contoso.sharepoint.com/sites/mysite|
|source*|Ruta de acceso del archivo de origen|Ruta de acceso al archivo de origen|
|destination*|Ruta de acceso del archivo de destino|Ruta de acceso al archivo de destino|
|overwrite|Marca de sobrescritura|Si desea o no sobrescribir el archivo de destino si existe|

El símbolo * indica que la propiedad es obligatoria.

#### Detalles de salida

BlobMetadata


| Nombre de propiedad | Tipo de datos |
|---|---|---|
|Id|cadena|
|Nombre|cadena|
|DisplayName|cadena|
|Ruta de acceso|cadena|
|LastModified|cadena|
|Tamaño|integer|
|MediaType|cadena|
|IsFolder|boolean|
|ETag|cadena|
|FileLocator|cadena|




### Cuando se crea un archivo
Esta operación desencadena un flujo al crear un archivo en una carpeta de SharePoint.


|Nombre de propiedad| Display Name (Nombre para mostrar)|Descripción|
| ---|---|---|
|dataset*|Dirección URL del sitio|Dirección URL del sitio de SharePoint|
|folderId*|Folder id (Identificador de carpeta)|Seleccionar una carpeta|

El símbolo * indica que la propiedad es obligatoria.




### Cuando se modifica un archivo
Esta operación desencadena un flujo al modificar un archivo en una carpeta de SharePoint.


|Nombre de propiedad| Display Name (Nombre para mostrar)|Descripción|
| ---|---|---|
|dataset*|Dirección URL del sitio|Dirección URL del sitio de SharePoint|
|folderId*|Folder id (Identificador de carpeta)|Seleccionar una carpeta|

El símbolo * indica que la propiedad es obligatoria.




### Enumerar carpeta
Esta operación obtiene los archivos contenidos en una carpeta de SharePoint.


|Nombre de propiedad| Display Name (Nombre para mostrar)|Descripción|
| ---|---|---|
|dataset*|Dirección URL del sitio|Dirección URL del sitio de SharePoint como http://contoso.sharepoint.com/sites/mysite|
|id*|Identificador de archivo|Identificador único de la carpeta|

El símbolo * indica que la propiedad es obligatoria.



#### Detalles de salida

BlobMetadata


| Nombre de propiedad | Tipo de datos |
|---|---|---|
|Id|cadena|
|Nombre|cadena|
|DisplayName|cadena|
|Ruta de acceso|cadena|
|LastModified|cadena|
|Tamaño|integer|
|MediaType|cadena|
|IsFolder|boolean|
|ETag|cadena|
|FileLocator|cadena|




### Enumerar carpeta raíz
Esta operación obtiene los archivos en la carpeta raíz de SharePoint.


|Nombre de propiedad| Display Name (Nombre para mostrar)|Descripción|
| ---|---|---|
|dataset*|Dirección URL del sitio|Dirección URL del sitio de SharePoint como http://contoso.sharepoint.com/sites/mysite|

El símbolo * indica que la propiedad es obligatoria.



#### Detalles de salida

BlobMetadata


| Nombre de propiedad | Tipo de datos |
|---|---|---|
|Id|cadena|
|Nombre|cadena|
|DisplayName|cadena|
|Ruta de acceso|cadena|
|LastModified|cadena|
|Tamaño|integer|
|MediaType|cadena|
|IsFolder|boolean|
|ETag|cadena|
|FileLocator|cadena|




### Extraer carpeta
Esta operación extrae un archivo de almacenamiento en una carpeta de SharePoint (por ejemplo: .zip).


|Nombre de propiedad| Display Name (Nombre para mostrar)|Descripción|
| ---|---|---|
|dataset*|Dirección URL del sitio|Dirección URL del sitio de SharePoint como http://contoso.sharepoint.com/sites/mysite|
|source*|Ruta de acceso del archivo de origen|Ruta de acceso al archivo de origen|
|destination*|Ruta de acceso a la carpeta de destino|Ruta de acceso a la carpeta de destino|
|overwrite|Marca de sobrescritura|Si desea o no sobrescribir el archivo de destino si existe|

El símbolo * indica que la propiedad es obligatoria.



#### Detalles de salida

BlobMetadata


| Nombre de propiedad | Tipo de datos |
|---|---|---|
|Id|cadena|
|Nombre|cadena|
|DisplayName|cadena|
|Ruta de acceso|cadena|
|LastModified|cadena|
|Tamaño|integer|
|MediaType|cadena|
|IsFolder|boolean|
|ETag|cadena|
|FileLocator|cadena|




### Cuando se crea un elemento
Esta operación desencadena un flujo al crear un elemento en una lista de SharePoint.


|Nombre de propiedad| Display Name (Nombre para mostrar)|Descripción|
| ---|---|---|
|dataset*|URL del sitio|Dirección URL del sitio de SharePoint (ejemplo: http://contoso.sharepoint.com/sites/mysite)|
|table*|Nombre de lista|Nombre de lista de SharePoint|
|$filter|Consulta de filtro|Consulta de filtro de ODATA para restringir la devolución de entradas|
|$orderby|Ordenar por|Consulta orderBy de ODATA para especificar el orden de las entradas|
|$skip|Omitir conteo|Número de entradas para omitir (valor predeterminado = 0)|
|$top|Número máximo de entradas|Número máximo de entradas para recuperar (valor predeterminado = 256)|

El símbolo * indica que la propiedad es obligatoria.

#### Detalles de salida

ItemsList


| Nombre de propiedad | Tipo de datos | 
|---|---|
|value|array|




### Cuando se modifica un elemento existente
Esta operación desencadena un flujo al modificar un elemento existente en una lista de SharePoint.


|Nombre de propiedad| Display Name (Nombre para mostrar)|Descripción|
| ---|---|---|
|dataset*|URL del sitio|Dirección URL del sitio de SharePoint (ejemplo: http://contoso.sharepoint.com/sites/mysite)|
|table*|Nombre de lista|Nombre de lista de SharePoint|
|$filter|Consulta de filtro|Consulta de filtro de ODATA para restringir la devolución de entradas|
|$orderby|Ordenar por|Consulta orderBy de ODATA para especificar el orden de las entradas|
|$skip|Omitir conteo|Número de entradas para omitir (valor predeterminado = 0)|
|$top|Número máximo de entradas|Número máximo de entradas para recuperar (valor predeterminado = 256)|

El símbolo * indica que la propiedad es obligatoria.

#### Detalles de salida

ItemsList


| Nombre de propiedad | Tipo de datos |
|---|---|
|value|array|




### Obtener elementos
Esta operación obtiene elementos de una lista de SharePoint.


|Nombre de propiedad| Display Name (Nombre para mostrar)|Descripción|
| ---|---|---|
|dataset*|URL del sitio|Dirección URL del sitio de SharePoint (ejemplo: http://contoso.sharepoint.com/sites/mysite)|
|table*|Nombre de lista|Nombre de lista de SharePoint|
|$filter|Consulta de filtro|Consulta de filtro de ODATA para restringir la devolución de entradas|
|$orderby|Ordenar por|Consulta orderBy de ODATA para especificar el orden de las entradas|
|$skip|Omitir conteo|Número de entradas para omitir (valor predeterminado = 0)|
|$top|Número máximo de entradas|Número máximo de entradas para recuperar (valor predeterminado = 256)|

El símbolo * indica que la propiedad es obligatoria.

#### Detalles de salida

ItemsList


| Nombre de propiedad | Tipo de datos |
|---|---|
|value|array|




### Crear elemento
Esta operación obtiene un elemento nuevo de una lista de SharePoint.


|Nombre de propiedad| Display Name (Nombre para mostrar)|Descripción|
| ---|---|---|
|dataset*|URL del sitio|Dirección URL del sitio de SharePoint (ejemplo: http://contoso.sharepoint.com/sites/mysite)|
|table*|Nombre de lista|Nombre de lista de SharePoint|
|item*|Elemento|Elemento que se va a crear|

El símbolo * indica que la propiedad es obligatoria.

#### Detalles de salida

Elemento


| Nombre de propiedad | Tipo de datos |
|---|---|
|ItemInternalId|cadena|




### Obtener elemento
Esta operación obtiene un elemento único por su id. de una lista de SharePoint.


|Nombre de propiedad| Display Name (Nombre para mostrar)|Descripción|
| ---|---|---|
|dataset*|URL del sitio|Dirección URL del sitio de SharePoint (ejemplo: http://contoso.sharepoint.com/sites/mysite)|
|table*|Nombre de lista|Nombre de lista de SharePoint|
|id*|Id|Identificador único del elemento que se va a recuperar|

El símbolo * indica que la propiedad es obligatoria.

#### Detalles de salida

Elemento


| Nombre de propiedad | Tipo de datos |
|---|---|
|ItemInternalId|cadena|




### Eliminar elemento
Esta operación elimina un elemento de una lista de SharePoint.


|Nombre de propiedad| Display Name (Nombre para mostrar)|Descripción|
| ---|---|---|
|dataset*|URL del sitio|Dirección URL del sitio de SharePoint (ejemplo: http://contoso.sharepoint.com/sites/mysite)|
|table*|Nombre de lista|Nombre de lista de SharePoint|
|id*|Id|Identificador único del elemento que se va a eliminar|

El símbolo * indica que la propiedad es obligatoria.




### Actualizar elemento
Esta operación actualiza un elemento de una lista de SharePoint.


|Nombre de propiedad| Display Name (Nombre para mostrar)|Descripción|
| ---|---|---|
|dataset*|URL del sitio|Dirección URL del sitio de SharePoint (ejemplo: http://contoso.sharepoint.com/sites/mysite)|
|table*|Nombre de lista|Nombre de lista de SharePoint|
|id*|Id|Identificador único del elemento que se va a actualizar|
|item*|Elemento|Elemento con propiedades cambiadas|

El símbolo * indica que la propiedad es obligatoria.

#### Detalles de salida

Elemento


| Nombre de propiedad | Tipo de datos |
|---|---|
|ItemInternalId|cadena|




### Obtener valores de entidad
Esta operación obtiene los valores posibles para una entidad de SharePoint.


|Nombre de propiedad| Display Name (Nombre para mostrar)|Descripción|
| ---|---|---|
|dataset*|Dirección URL del sitio de SharePoint|Dirección URL del sitio de SharePoint|
|table*|Nombre de la tabla|Nombre de la tabla|
|id*|Identificador de entidad|Identificador de entidad|

El símbolo * indica que la propiedad es obligatoria.

#### Detalles de salida





### Obtener listas
Esta operación obtiene las listas de SharePoint desde un sitio.


|Nombre de propiedad| Display Name (Nombre para mostrar)|Descripción|
| ---|---|---|
|dataset*|URL del sitio|Dirección URL del sitio de SharePoint (ejemplo: http://contoso.sharepoint.com/sites/mysite)|

El símbolo * indica que la propiedad es obligatoria.

#### Detalles de salida

TablesList


| Nombre de propiedad | Tipo de datos |
|---|---|
|value|array|



## Respuestas HTTP

Las acciones y los desencadenadores anteriores pueden devolver uno o varios de los siguientes códigos de estado HTTP:

|Name|Descripción|
|---|---|
|200|OK|
|202|Accepted|
|400|Bad Request|
|401|No autorizado|
|403|Prohibido|
|404|No encontrado|
|500|Error interno del servidor. Error desconocido.|
|default|Error en la operación.|















## Pasos siguientes
[Creación de una aplicación lógica](../app-service-logic/app-service-logic-create-a-logic-app.md)

<!----HONumber=AcomDC_0803_2016-->
