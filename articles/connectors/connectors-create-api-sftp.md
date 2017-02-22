---
title: "Información sobre cómo utilizar el conector de SFTP en Logic Apps | Microsoft Docs"
description: "Cree aplicaciones lógicas con el Servicio de aplicaciones de Azure. Conéctese a la API de SFTP para enviar y recibir archivos. Puede realizar varias operaciones, como crear, actualizar, obtener o eliminar archivos."
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: 697eb8b0-4a66-40c7-be7b-6aa6b131c7ad
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 07/20/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: 66fc8f7e1da55dbe6bb1dd8b8d6a535c498c1cf7
ms.openlocfilehash: 2977404fb408ea5301c88caa7ce6767a906ca9c7


---
# <a name="get-started-with-the-sftp-connector"></a>Introducción al conector de SFTP
Use el conector SFTP a fin de acceder a una cuenta SFTP para enviar y recibir archivos. Puede realizar varias operaciones, como crear, actualizar, obtener o eliminar archivos.  

Para poder usar [un conector](apis-list.md), primero debe crear una aplicación lógica. Por tanto, puede comenzar [creando una aplicación lógica](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="connect-to-sftp"></a>Conexión a SFTP
Para que la aplicación lógica pueda acceder a un servicio, primero debe crear una *conexión* con dicho servicio. Una [conexión](connectors-overview.md) proporciona conectividad entre una aplicación lógica y otro servicio.  

### <a name="create-a-connection-to-sftp"></a>Creación de una conexión a SFTP
> [!INCLUDE [Steps to create a connection to SFTP](../../includes/connectors-create-api-sftp.md)]
> 
> 

## <a name="use-an-sftp-trigger"></a>Uso de un desencadenador de SFTP
Un desencadenador es un evento que se puede utilizar para iniciar el flujo de trabajo definido en una aplicación lógica. [Más información sobre los desencadenadores](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts).  

En este ejemplo, le enseñaremos a usar el desencadenador **SFTP - When a file is added or modified** (SFTP - Cuando se agrega o modifica un archivo) para que, cuando se agregue o se modifique un archivo en un servidor SFTP, se inicie el flujo de trabajo de una aplicación lógica. En el ejemplo, también aprenderá a agregar una condición que compruebe el contenido del archivo nuevo o modificado y a tomar una decisión para extraer el archivo si su contenido indica que debe extraerse antes de usar el contenido. Por último, aprenderá a agregar una acción para extraer el contenido de un archivo y colocar el contenido extraído en una carpeta del servidor SFTP. 

En un entorno empresarial, podría utilizar este desencadenador para supervisar los nuevos archivos que se agregan a una carpeta SFTP y que representan los pedidos de los clientes.  Podría utilizar una acción del conector de SFTP, como **Get file content** (Obtener contenido del archivo), para obtener el contenido del archivo a fin de procesarlo y almacenarlo después en la base de datos de pedidos.

> [!INCLUDE [Steps to create an SFTP trigger](../../includes/connectors-create-api-sftp-trigger.md)]
> 
> 

## <a name="add-a-condition"></a>Agregar una condición
> [!INCLUDE [Steps to add a condition](../../includes/connectors-create-api-sftp-condition.md)]
> 
> 

## <a name="use-an-sftp-action"></a>Uso de una acción de SFTP
Una acción es una operación que se lleva a cabo mediante el flujo de trabajo definido en una aplicación lógica. [Más información acerca de las acciones](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts).  

> [!INCLUDE [Steps to create an SFTP action](../../includes/connectors-create-api-sftp-action.md)]
> 
> 

## <a name="technical-details"></a>Detalles técnicos
Estos son los detalles sobre los desencadenadores, las acciones y las respuestas compatibles con esta conexión:

## <a name="sftp-triggers"></a>Desencadenadores de SFTP
SFTP tiene el siguiente desencadenador o desencadenadores:  

| Desencadenador | Descripción |
| --- | --- |
| [When a file is added or modified (Cuando se agrega o modifica un archivo)](connectors-create-api-sftp.md#when-a-file-is-added-or-modified) |Esta operación desencadena un flujo cuando se agrega o modifica un archivo en una carpeta. |

## <a name="sftp-actions"></a>Acciones SFTP
SFTP tiene las siguientes acciones:

| . | Description |
| --- | --- |
| [Obtener metadatos de archivo](connectors-create-api-sftp.md#get-file-metadata) |Esta operación obtiene metadatos de archivo mediante el id. del archivo. |
| [Actualizar archivo](connectors-create-api-sftp.md#update-file) |Esta operación actualiza el contenido del archivo. |
| [Eliminar archivo](connectors-create-api-sftp.md#delete-file) |Esta operación elimina un archivo. |
| [Obtener metadatos de archivo mediante la ruta de acceso](connectors-create-api-sftp.md#get-file-metadata-using-path) |Esta operación obtiene metadatos de archivo mediante la ruta de acceso del archivo. |
| [Obtener contenido de archivo mediante la ruta de acceso](connectors-create-api-sftp.md#get-file-content-using-path) |Esta operación obtiene el contenido de archivo mediante la ruta de acceso del archivo. |
| [Obtener contenido de archivo](connectors-create-api-sftp.md#get-file-content) |Esta operación obtiene el contenido de archivo mediante el id. del archivo. |
| [Crear archivo](connectors-create-api-sftp.md#create-file) |Esta operación carga un archivo en un servidor SFTP. |
| [Copiar archivo](connectors-create-api-sftp.md#copy-file) |Esta operación copia un archivo en un servidor SFTP. |
| [Enumerar archivos de la carpeta](connectors-create-api-sftp.md#list-files-in-folder) |Esta operación obtiene los archivos contenidos en una carpeta. |
| [Enumerar archivos de la carpeta raíz](connectors-create-api-sftp.md#list-files-in-root-folder) |Esta operación obtiene los archivos en la carpeta raíz. |
| [Extract folder](connectors-create-api-sftp.md#extract-folder) |Esta operación extrae un archivo de almacenamiento en una carpeta (por ejemplo: .zip). |

### <a name="action-details"></a>Detalles de la acción
Estos son los detalles de las acciones y desencadenadores de este conector, junto con sus respuestas:

### <a name="get-file-metadata"></a>Obtención de metadatos de archivo
Esta operación obtiene metadatos de archivo mediante el id. del archivo. 

| Nombre de propiedad | Display Name (Nombre para mostrar) | Description |
| --- | --- | --- |
| id* |Archivo |Especificación del archivo |

El símbolo * indica que la propiedad es obligatoria.

#### <a name="output-details"></a>Detalles de salida
BlobMetadata

| Nombre de propiedad | Tipo de datos |
| --- | --- | --- |
| id |cadena |
| Nombre |cadena |
| DisplayName |cadena |
| path |cadena |
| LastModified |cadena |
| Tamaño |integer |
| MediaType |cadena |
| IsFolder |boolean |
| ETag |cadena |
| FileLocator |string |

### <a name="update-file"></a>Actualizar archivo
Esta operación actualiza el contenido del archivo. 

| Nombre de propiedad | Display Name (Nombre para mostrar) | Description |
| --- | --- | --- |
| id* |Archivo |Especificación del archivo |
| body* |Contenido del archivo |Contenido del archivo que se va a actualizar |

El símbolo * indica que la propiedad es obligatoria.

#### <a name="output-details"></a>Detalles de salida
BlobMetadata

| Nombre de propiedad | Tipo de datos |
| --- | --- | --- |
| id |cadena |
| Nombre |cadena |
| DisplayName |cadena |
| path |cadena |
| LastModified |cadena |
| Tamaño |integer |
| MediaType |cadena |
| IsFolder |boolean |
| ETag |cadena |
| FileLocator |string |

### <a name="delete-file"></a>Eliminar archivo
Esta operación elimina un archivo. 

| Nombre de propiedad | Display Name (Nombre para mostrar) | Description |
| --- | --- | --- |
| id* |Archivo |Especificación del archivo |

El símbolo * indica que la propiedad es obligatoria.

### <a name="get-file-metadata-using-path"></a>Obtener metadatos de archivo mediante la ruta de acceso
Esta operación obtiene metadatos de archivo mediante la ruta de acceso del archivo. 

| Nombre de propiedad | Display Name (Nombre para mostrar) | Description |
| --- | --- | --- |
| path* |Ruta de acceso del archivo |Ruta de acceso única del archivo |

El símbolo * indica que la propiedad es obligatoria.

#### <a name="output-details"></a>Detalles de salida
BlobMetadata

| Nombre de propiedad | Tipo de datos |
| --- | --- | --- |
| id |cadena |
| Nombre |cadena |
| DisplayName |cadena |
| path |cadena |
| LastModified |cadena |
| Tamaño |integer |
| MediaType |cadena |
| IsFolder |boolean |
| ETag |cadena |
| FileLocator |string |

### <a name="get-file-content-using-path"></a>Obtener contenido de archivo mediante la ruta de acceso
Esta operación obtiene el contenido de archivo mediante la ruta de acceso del archivo. 

| Nombre de propiedad | Display Name (Nombre para mostrar) | Description |
| --- | --- | --- |
| path* |Ruta de acceso del archivo |Ruta de acceso única del archivo |

El símbolo * indica que la propiedad es obligatoria.

### <a name="get-file-content"></a>Obtener contenido de archivo
Esta operación obtiene el contenido de archivo mediante el id. del archivo. 

| Nombre de propiedad | Display Name (Nombre para mostrar) | Description |
| --- | --- | --- |
| id* |Archivo |Especificación del archivo |

El símbolo * indica que la propiedad es obligatoria.

### <a name="create-file"></a>Crear archivo
Esta operación carga un archivo en un servidor SFTP. 

| Nombre de propiedad | Display Name (Nombre para mostrar) | Descripción |
| --- | --- | --- |
| folderPath* |Ruta de acceso a la carpeta |Ruta de acceso única de la carpeta |
| name* |Nombre de archivo |Nombre del archivo |
| body* |Contenido del archivo |Contenido del archivo que se va a crear |

El símbolo * indica que la propiedad es obligatoria.

#### <a name="output-details"></a>Detalles de salida
BlobMetadata

|  | Nombre de propiedad | Tipo de datos |
| --- | --- | --- |
| id |cadena | |
| Nombre |cadena | |
| DisplayName |cadena | |
| path |cadena | |
| LastModified |cadena | |
| Tamaño |integer | |
| MediaType |cadena | |
| IsFolder |boolean | |
| ETag |cadena | |
| FileLocator |string | |

### <a name="copy-file"></a>Copiar archivo
Esta operación copia un archivo en un servidor SFTP. 

| Nombre de propiedad | Display Name (Nombre para mostrar) | Description |
| --- | --- | --- |
| source* |Ruta de acceso del archivo de origen |Ruta de acceso al archivo de origen |
| destination* |Ruta de acceso del archivo de destino |Ruta de acceso al archivo de destino, incluido el nombre de archivo |
| overwrite |¿Sobrescribir? |Sobrescribe el archivo de destino si está establecido en 'true' |

El símbolo * indica que la propiedad es obligatoria.

#### <a name="output-details"></a>Detalles de salida
BlobMetadata

| Nombre de propiedad | Tipo de datos |
| --- | --- | --- |
| id |cadena |
| Nombre |cadena |
| DisplayName |cadena |
| path |cadena |
| LastModified |cadena |
| Tamaño |integer |
| MediaType |cadena |
| IsFolder |boolean |
| ETag |cadena |
| FileLocator |string |

### <a name="when-a-file-is-added-or-modified"></a>When a file is added or modified (Cuando se agrega o modifica un archivo)
Esta operación desencadena un flujo cuando se agrega o modifica un archivo en una carpeta. 

| Nombre de propiedad | Display Name (Nombre para mostrar) | Descripción |
| --- | --- | --- |
| folderId* |Carpeta |Especificación de una carpeta |

El símbolo * indica que la propiedad es obligatoria.

### <a name="list-files-in-folder"></a>Enumerar archivos de la carpeta
Esta operación obtiene los archivos contenidos en una carpeta. 

| Nombre de propiedad | Display Name (Nombre para mostrar) | Description |
| --- | --- | --- |
| id* |Carpeta |Especificación de la carpeta |

El símbolo * indica que la propiedad es obligatoria.

#### <a name="output-details"></a>Detalles de salida
BlobMetadata

| Nombre de propiedad | Tipo de datos |
| --- | --- | --- |
| id |cadena |
| Nombre |cadena |
| DisplayName |cadena |
| path |cadena |
| LastModified |cadena |
| Tamaño |integer |
| MediaType |cadena |
| IsFolder |boolean |
| ETag |cadena |
| FileLocator |string |

### <a name="list-files-in-root-folder"></a>Enumerar archivos de la carpeta raíz
Esta operación obtiene los archivos en la carpeta raíz. 

No hay parámetros para esta llamada

#### <a name="output-details"></a>Detalles de salida
BlobMetadata

| Nombre de propiedad | Tipo de datos |
| --- | --- | --- |
| id |cadena |
| Nombre |cadena |
| DisplayName |cadena |
| path |cadena |
| LastModified |cadena |
| Tamaño |integer |
| MediaType |cadena |
| IsFolder |boolean |
| ETag |cadena |
| FileLocator |string |

### <a name="extract-folder"></a>Extraer carpeta
Esta operación extrae un archivo de almacenamiento en una carpeta (por ejemplo: .zip). 

| Nombre de propiedad | Display Name (Nombre para mostrar) | Description |
| --- | --- | --- |
| source* |Ruta de acceso del archivo de origen |Ruta de acceso al archivo de almacenamiento |
| destination* |Ruta de acceso a la carpeta de destino |Ruta de acceso a la carpeta de destino |
| overwrite |¿Sobrescribir? |Sobrescribe los archivos de destino si está establecido en 'true' |

El símbolo * indica que la propiedad es obligatoria.

#### <a name="output-details"></a>Detalles de salida
BlobMetadata

| Nombre de propiedad | Tipo de datos |
| --- | --- | --- |
| id |cadena |
| Nombre |cadena |
| DisplayName |cadena |
| path |cadena |
| LastModified |cadena |
| Tamaño |integer |
| MediaType |cadena |
| IsFolder |boolean |
| ETag |cadena |
| FileLocator |string |

## <a name="http-responses"></a>Respuestas HTTP
Las acciones y los desencadenadores anteriores pueden devolver uno o varios de los siguientes códigos de estado HTTP: 

| Nombre | Descripción |
| --- | --- |
| 200 |OK |
| 202 |Accepted |
| 400 |Bad Request |
| 401 |No autorizado |
| 403 |Prohibido |
| 404 |No encontrado |
| 500 |Error interno del servidor. Error desconocido. |
| default |Error en la operación. |

## <a name="next-steps"></a>Pasos siguientes
[Creación de una aplicación lógica](../logic-apps/logic-apps-create-a-logic-app.md)




<!--HONumber=Feb17_HO3-->


