<properties
pageTitle="Dropbox | Microsoft Azure"
description="Cree aplicaciones lógicas con el Servicio de aplicaciones de Azure. Conéctese a Dropbox para administrar los archivos. En Dropbox, puede realizar diversas acciones, como cargar, actualizar, obtener y eliminar archivos."
services="logic-apps"	
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
ms.date="07/15/2016"
ms.author="deonhe"/>

# Introducción al conector de Dropbox

Conéctese a Dropbox para administrar los archivos. En Dropbox, puede realizar diversas acciones, como cargar, actualizar, obtener y eliminar archivos.

Para poder usar [un conector](./apis-list.md), primero debe crear una aplicación lógica. Por tanto, puede comenzar [creando una aplicación lógica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Conexión con Dropbox

Para que la aplicación lógica pueda acceder a un servicio, primero debe crear una *conexión* con dicho servicio. Una conexión proporciona conectividad entre una aplicación lógica y otro servicio. Por ejemplo, para poder conectarse a Dropbox, primero debe crear una *conexión* a Dropbox. Para ello, tendrá que especificar las credenciales que usa habitualmente para acceder al servicio al que desea conectarse. Por tanto, en el ejemplo de Dropbox, deberá escribir las credenciales de la cuenta de Dropbox para crear la conexión con este servicio. [Más información sobre las conexiones]()

### Creación de una conexión con Dropbox

>[AZURE.INCLUDE [Pasos para crear una conexión a Dropbox](../../includes/connectors-create-api-dropbox.md)]

## Uso de un desencadenador de Dropbox

Un desencadenador es un evento que se puede utilizar para iniciar el flujo de trabajo definido en una aplicación lógica. [Más información sobre los desencadenadores](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts).

En este ejemplo, vamos a utilizar el desencadenador **When a file is created** (Cuando se crea un archivo). Cuando se active este desencadenador, se invocará la acción de Dropbox **Get file content using path** (Obtener contenido del archivo mediante la ruta de acceso).

1. Escriba *dropbox* en el cuadro de búsqueda del diseñador de aplicaciones lógicas y seleccione el desencadenador **Dropbox - When a file is created** (Dropbox - Cuando se cree un archivo). ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-trigger.PNG)
  
2. Seleccione la carpeta en la que se va a supervisar la creación de archivos. Seleccione la opción ... (marcada con un cuadro rojo) y busque la carpeta que desea seleccionar para la entrada del desencadenador. ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-trigger-2.PNG)

## Uso de una acción de Dropbox

Una acción es una operación que se lleva a cabo mediante el flujo de trabajo definido en una aplicación lógica. [Más información sobre las acciones](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts).

Ahora que se ha agregado el desencadenador, siga estos pasos para incorporar una acción que obtenga el contenido del nuevo archivo.

1. Seleccione **+ Nuevo paso** para agregar la acción que desea que se ejecute cuando se cree un nuevo archivo. ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-action.PNG)

2. Seleccione **Add an action** (Agregar una acción). Se abrirá el cuadro de búsqueda en el que podrá buscar cualquier acción que quiera realizar. ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-action-2.PNG)

3. Escriba *dropbox* para buscar las acciones relacionadas con Dropbox.

4. Seleccione **Dropbox - Get file content using path** (Dropbox: obtener el contenido del archivo mediante la ruta de acceso). Esta será la acción que se ejecutará cuando se cree un nuevo archivo en la carpeta de Dropbox seleccionada. Se abre el bloque de control de acción. Si no lo ha hecho previamente, se le pedirá que autorice a la aplicación lógica para que pueda acceder a la cuenta de Dropbox. ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-action-3.PNG)

5. Seleccione la opción ... (la encontrará a la derecha del control **Ruta del archivo**) y busque la ruta de archivo que desea usar. También puede utilizar el token de la **ruta de archivo** para crear más rápido la aplicación lógica. ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-action-4.PNG)

6. Guarde el trabajo y cree un nuevo archivo en Dropbox para activar el flujo de trabajo.

## Detalles técnicos

Estos son los detalles sobre los desencadenadores, las acciones y las respuestas compatibles con esta conexión:

## Desencadenadores de Dropbox

El conector de Dropbox dispone de los siguientes desencadenadores:

|Desencadenador | Descripción|
|--- | ---|
|[Cuando se crea un archivo](connectors-create-api-dropbox.md#when-a-file-is-created)|Esta operación desencadena un flujo al crear un archivo en una carpeta.|
|[Cuando se modifica un archivo](connectors-create-api-dropbox.md#when-a-file-is-modified)|Esta operación desencadena un flujo al modificar un archivo en una carpeta.|

## Acciones de Dropbox

El conector de Dropbox dispone de las siguientes acciones:

|Acción|Descripción|
|--- | ---|
|[Obtención de metadatos de archivo](connectors-create-api-dropbox.md#get-file-metadata)|Esta operación obtiene los metadatos de un archivo.|
|[Actualizar archivo](connectors-create-api-dropbox.md#update-file)|Esta operación actualiza un archivo.|
|[Eliminar archivo](connectors-create-api-dropbox.md#delete-file)|Esta operación elimina un archivo.|
|[Obtener metadatos de archivo mediante la ruta de acceso](connectors-create-api-dropbox.md#get-file-metadata-using-path)|Esta operación obtiene los metadatos de un archivo mediante la ruta de acceso.|
|[Obtener contenido de archivo mediante la ruta de acceso](connectors-create-api-dropbox.md#get-file-content-using-path)|Esta operación obtiene el contenido de un archivo mediante la ruta de acceso.|
|[Obtener contenido de archivo](connectors-create-api-dropbox.md#get-file-content)|Esta operación obtiene el contenido de un archivo.|
|[Crear archivo](connectors-create-api-dropbox.md#create-file)|Esta operación crea un archivo.|
|[Copiar archivo](connectors-create-api-dropbox.md#copy-file)|Esta operación copia un archivo en Dropbox.|
|[Enumerar archivos de la carpeta](connectors-create-api-dropbox.md#list-files-in-folder)|Esta operación obtiene la lista de archivos y subcarpetas de una carpeta.|
|[Enumerar archivos de la carpeta raíz](connectors-create-api-dropbox.md#list-files-in-root-folder)|Esta operación obtiene la lista de archivos y subcarpetas de la carpeta raíz.|
|[Extraer archivo en la carpeta](connectors-create-api-dropbox.md#extract-archive-to-folder)|Esta operación extrae un archivo de almacenamiento en una carpeta (por ejemplo: .zip).|

### Detalles de la acción

Estos son los detalles de las acciones y desencadenadores de este conector, junto con sus respuestas:


### Obtención de metadatos de archivo
Esta operación obtiene los metadatos de un archivo.

                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 
|Nombre de propiedad| Display Name (Nombre para mostrar)|Descripción|
| ---|---|---|
|id*|Archivo|Seleccionar un archivo|

El símbolo * indica que la propiedad es obligatoria.

#### Detalles de salida

BlobMetadata


| Nombre de propiedad | Tipo de datos |
|---|---|
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
Esta operación actualiza un archivo.


|Nombre de propiedad| Display Name (Nombre para mostrar)|Descripción|
| ---|---|---|
|id*|Archivo|Seleccionar un archivo|
|body*|Contenido del archivo|Contenido del archivo|

El símbolo * indica que la propiedad es obligatoria.

#### Detalles de salida

BlobMetadata


| Nombre de propiedad | Tipo de datos |
|---|---|
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
|id*|Archivo|Seleccionar un archivo|

El símbolo * indica que la propiedad es obligatoria.




### Obtener metadatos de archivo mediante la ruta de acceso
Esta operación obtiene los metadatos de un archivo mediante la ruta de acceso.


|Nombre de propiedad| Display Name (Nombre para mostrar)|Descripción|
| ---|---|---|
|path*|Ruta de acceso del archivo|Seleccionar un archivo|

El símbolo * indica que la propiedad es obligatoria.

#### Detalles de salida

BlobMetadata


| Nombre de propiedad | Tipo de datos |
|---|---|
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
Esta operación obtiene el contenido de un archivo mediante la ruta de acceso.


|Nombre de propiedad| Display Name (Nombre para mostrar)|Descripción|
| ---|---|---|
|path*|Ruta de acceso del archivo|Seleccionar un archivo|

El símbolo * indica que la propiedad es obligatoria.




### Obtener contenido de archivo
Esta operación obtiene el contenido de un archivo.


|Nombre de propiedad| Display Name (Nombre para mostrar)|Descripción|
| ---|---|---|
|id*|Archivo|Seleccionar un archivo|

El símbolo * indica que la propiedad es obligatoria.




### Crear archivo
Esta operación crea un archivo.


|Nombre de propiedad| Display Name (Nombre para mostrar)|Descripción|
| ---|---|---|
|folderPath*|Ruta de acceso a la carpeta|Seleccionar una carpeta|
|name*|Nombre de archivo|Nombre del archivo|
|body*|Contenido del archivo|Contenido del archivo|

El símbolo * indica que la propiedad es obligatoria.

#### Detalles de salida

BlobMetadata


| Nombre de propiedad | Tipo de datos |
|---|---|
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
Esta operación copia un archivo en Dropbox.


|Nombre de propiedad| Display Name (Nombre para mostrar)|Descripción|
| ---|---|---|
|source*|Dirección URL de origen|Dirección URL al archivo de origen|
|destination*|Ruta de acceso del archivo de destino|Ruta de acceso al archivo de destino, incluido el nombre del archivo de destino|
|overwrite|¿Sobrescribir?|Sobrescribe el archivo de destino si está establecido en 'true'|

El símbolo * indica que la propiedad es obligatoria.

#### Detalles de salida

BlobMetadata


| Nombre de propiedad | Tipo de datos |
|---|---|
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
Esta operación desencadena un flujo al crear un archivo en una carpeta.


|Nombre de propiedad| Display Name (Nombre para mostrar)|Descripción|
| ---|---|---|
|folderId*|Carpeta|Seleccionar una carpeta|

El símbolo * indica que la propiedad es obligatoria.




### Cuando se modifica un archivo
Esta operación desencadena un flujo al modificar un archivo en una carpeta.


|Nombre de propiedad| Display Name (Nombre para mostrar)|Descripción|
| ---|---|---|
|folderId*|Carpeta|Seleccionar una carpeta|

El símbolo * indica que la propiedad es obligatoria.




### Enumerar archivos de la carpeta
Esta operación obtiene la lista de archivos y subcarpetas de una carpeta.


|Nombre de propiedad| Display Name (Nombre para mostrar)|Descripción|
| ---|---|---|
|id*|Carpeta|Seleccionar una carpeta|

El símbolo * indica que la propiedad es obligatoria.



#### Detalles de salida

BlobMetadata


| Nombre de propiedad | Tipo de datos |
|---|---|
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




### Enumerar archivos de la carpeta raíz
Esta operación obtiene la lista de archivos y subcarpetas de la carpeta raíz.


No hay parámetros para esta llamada

#### Detalles de salida

BlobMetadata


| Nombre de propiedad | Tipo de datos |
|---|---|
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




### Extraer archivo en la carpeta
Esta operación extrae un archivo de almacenamiento en una carpeta (por ejemplo: .zip).


|Nombre de propiedad| Display Name (Nombre para mostrar)|Descripción|
| ---|---|---|
|source*|Ruta de acceso del archivo de origen|Ruta de acceso al archivo de almacenamiento|
|destination*|Ruta de acceso a la carpeta de destino|Ruta de acceso para extraer el contenido del archivo|
|overwrite|¿Sobrescribir?|Sobrescribe los archivos de destino si está establecido en 'true'|

El símbolo * indica que la propiedad es obligatoria.



#### Detalles de salida

BlobMetadata


| Nombre de propiedad | Tipo de datos |
|---|---|
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



## Respuestas HTTP

Las acciones y los desencadenadores anteriores pueden devolver uno o varios de los siguientes códigos de estado HTTP:

| Name | Descripción |
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

<!---HONumber=AcomDC_0803_2016-->