<properties
pageTitle="Aprenda a utilizar el conector de SFTP en las aplicaciones lógicas | Microsoft Azure"
description="Cree aplicaciones lógicas con el Servicio de aplicaciones de Azure. Conéctese a la API de SFTP para enviar y recibir archivos. Puede realizar varias operaciones, como crear, actualizar, obtener o eliminar archivos."
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
ms.date="07/20/2016"
ms.author="deonhe"/>

# Introducción al conector de SFTP

Use el conector SFTP a fin de acceder a una cuenta SFTP para enviar y recibir archivos. Puede realizar varias operaciones, como crear, actualizar, obtener o eliminar archivos.

Para poder usar [un conector](./apis-list.md), primero debe crear una aplicación lógica. Por tanto, puede comenzar [creando una aplicación lógica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Conexión a SFTP

Para que la aplicación lógica pueda acceder a un servicio, primero debe crear una *conexión* con dicho servicio. Una [conexión](./connectors-overview.md) proporciona conectividad entre una aplicación lógica y otro servicio.

### Creación de una conexión a SFTP

>[AZURE.INCLUDE [Pasos para crear una conexión a SFTP](../../includes/connectors-create-api-sftp.md)]

## Uso de un desencadenador de SFTP

Un desencadenador es un evento que se puede utilizar para iniciar el flujo de trabajo definido en una aplicación lógica. [Más información sobre los desencadenadores](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts).

En este ejemplo, le enseñaremos a usar el desencadenador **SFTP - When a file is added or modified** (SFTP - Cuando se agrega o modifica un archivo) para que, cuando se agregue o se modifique un archivo en un servidor SFTP, se inicie el flujo de trabajo de una aplicación lógica. En el ejemplo, también aprenderá a agregar una condición que compruebe el contenido del archivo nuevo o modificado y a tomar una decisión para extraer el archivo si su contenido indica que debe extraerse antes de usar el contenido. Por último, aprenderá a agregar una acción para extraer el contenido de un archivo y colocar el contenido extraído en una carpeta del servidor SFTP.

En un entorno empresarial, podría utilizar este desencadenador para supervisar los nuevos archivos que se agregan a una carpeta SFTP y que representan los pedidos de los clientes. Podría utilizar una acción del conector de SFTP, como **Get file content** (Obtener contenido del archivo), para obtener el contenido del archivo a fin de procesarlo y almacenarlo después en la base de datos de pedidos.

>[AZURE.INCLUDE [Pasos para crear un desencadenador de SFTP](../../includes/connectors-create-api-sftp-trigger.md)]

## Agregar una condición

>[AZURE.INCLUDE [Pasos para agregar una condición](../../includes/connectors-create-api-sftp-condition.md)]

## Uso de una acción de SFTP

Una acción es una operación que se lleva a cabo mediante el flujo de trabajo definido en una aplicación lógica. [Más información sobre las acciones](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts).

>[AZURE.INCLUDE [Pasos para crear una acción de SFTP](../../includes/connectors-create-api-sftp-action.md)]


## Detalles técnicos

Estos son los detalles sobre los desencadenadores, las acciones y las respuestas compatibles con esta conexión:

## Desencadenadores de SFTP

SFTP tiene el siguiente desencadenador o desencadenadores:

|Desencadenador | Descripción|
|--- | ---|
|[When a file is added or modified](connectors-create-api-sftp.md#when-a-file-is-added-or-modified) (Cuando se agrega o modifica un archivo)|Esta operación desencadena un flujo cuando se agrega o modifica un archivo en una carpeta.|


## Acciones SFTP

SFTP tiene las siguientes acciones:


|Acción|Descripción|
|--- | ---|
|[Obtención de metadatos de archivo](connectors-create-api-sftp.md#get-file-metadata)|Esta operación obtiene metadatos de archivo mediante el id. del archivo.|
|[Actualizar archivo](connectors-create-api-sftp.md#update-file)|Esta operación actualiza el contenido del archivo.|
|[Eliminar archivo](connectors-create-api-sftp.md#delete-file)|Esta operación elimina un archivo.|
|[Obtener metadatos de archivo mediante la ruta de acceso](connectors-create-api-sftp.md#get-file-metadata-using-path)|Esta operación obtiene metadatos de archivo mediante la ruta de acceso del archivo.|
|[Obtener contenido de archivo mediante la ruta de acceso](connectors-create-api-sftp.md#get-file-content-using-path)|Esta operación obtiene el contenido de archivo mediante la ruta de acceso del archivo.|
|[Obtener contenido de archivo](connectors-create-api-sftp.md#get-file-content)|Esta operación obtiene el contenido de archivo mediante el id. del archivo.|
|[Crear archivo](connectors-create-api-sftp.md#create-file)|Esta operación carga un archivo en un servidor SFTP.|
|[Copiar archivo](connectors-create-api-sftp.md#copy-file)|Esta operación copia un archivo en un servidor SFTP.|
|[Enumerar archivos de la carpeta](connectors-create-api-sftp.md#list-files-in-folder)|Esta operación obtiene los archivos contenidos en una carpeta.|
|[Enumerar archivos de la carpeta raíz](connectors-create-api-sftp.md#list-files-in-root-folder)|Esta operación obtiene los archivos en la carpeta raíz.|
|[Extraer carpeta](connectors-create-api-sftp.md#extract-folder)|Esta operación extrae un archivo de almacenamiento en una carpeta (por ejemplo: .zip).|
### Detalles de la acción

Estos son los detalles de las acciones y desencadenadores de este conector, junto con sus respuestas:



### Obtención de metadatos de archivo
Esta operación obtiene metadatos de archivo mediante el id. del archivo.


|Nombre de propiedad| Display Name (Nombre para mostrar)|Descripción|
| ---|---|---|
|id*|Archivo|Especificación del archivo|

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
|id*|Archivo|Especificación del archivo|
|body*|Contenido del archivo|Contenido del archivo que se va a actualizar|

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
|id*|Archivo|Especificación del archivo|

El símbolo * indica que la propiedad es obligatoria.




### Obtener metadatos de archivo mediante la ruta de acceso
Esta operación obtiene metadatos de archivo mediante la ruta de acceso del archivo.


|Nombre de propiedad| Display Name (Nombre para mostrar)|Descripción|
| ---|---|---|
|path*|Ruta de acceso del archivo|Ruta de acceso única del archivo|

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
|path*|Ruta de acceso del archivo|Ruta de acceso única del archivo|

El símbolo * indica que la propiedad es obligatoria.




### Obtener contenido de archivo
Esta operación obtiene el contenido de archivo mediante el id. del archivo.


|Nombre de propiedad| Display Name (Nombre para mostrar)|Descripción|
| ---|---|---|
|id*|Archivo|Especificación del archivo|

El símbolo * indica que la propiedad es obligatoria.




### Crear archivo
Esta operación carga un archivo en un servidor SFTP.


|Nombre de propiedad| Display Name (Nombre para mostrar)|Descripción|
| ---|---|---|
|folderPath*|Ruta de acceso a la carpeta|Ruta de acceso única de la carpeta|
|name*|Nombre de archivo|Nombre del archivo|
|body*|Contenido del archivo|Contenido del archivo que se va a crear|

El símbolo * indica que la propiedad es obligatoria.

#### Detalles de salida

BlobMetadata


|| Nombre de propiedad | Tipo de datos |
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
Esta operación copia un archivo en un servidor SFTP.


|Nombre de propiedad| Display Name (Nombre para mostrar)|Descripción|
| ---|---|---|
|source*|Ruta de acceso del archivo de origen|Ruta de acceso al archivo de origen|
|destination*|Ruta de acceso del archivo de destino|Ruta de acceso al archivo de destino, incluido el nombre de archivo|
|overwrite|¿Sobrescribir?|Sobrescribe el archivo de destino si está establecido en 'true'|

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




### When a file is added or modified (Cuando se agrega o modifica un archivo)
Esta operación desencadena un flujo cuando se agrega o modifica un archivo en una carpeta.


|Nombre de propiedad| Display Name (Nombre para mostrar)|Descripción|
| ---|---|---|
|folderId*|Carpeta|Especificación de una carpeta|

El símbolo * indica que la propiedad es obligatoria.




### Enumerar archivos de la carpeta
Esta operación obtiene los archivos contenidos en una carpeta.


|Nombre de propiedad| Display Name (Nombre para mostrar)|Descripción|
| ---|---|---|
|id*|Carpeta|Especificación de la carpeta|

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




### Enumerar archivos de la carpeta raíz
Esta operación obtiene los archivos en la carpeta raíz.


No hay parámetros para esta llamada

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
Esta operación extrae un archivo de almacenamiento en una carpeta (por ejemplo: .zip).


|Nombre de propiedad| Display Name (Nombre para mostrar)|Descripción|
| ---|---|---|
|source*|Ruta de acceso del archivo de origen|Ruta de acceso al archivo de almacenamiento|
|destination*|Ruta de acceso a la carpeta de destino|Ruta de acceso a la carpeta de destino|
|overwrite|¿Sobrescribir?|Sobrescribe los archivos de destino si está establecido en 'true'|

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

<!---HONumber=AcomDC_0803_2016-->