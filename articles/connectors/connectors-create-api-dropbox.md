---
title: Dropbox | Microsoft Docs
description: "Cree aplicaciones lógicas con el Servicio de aplicaciones de Azure. Conéctese a Dropbox para administrar los archivos. En Dropbox, puede realizar diversas acciones, como cargar, actualizar, obtener y eliminar archivos."
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: cb0ae033-aba7-4ac9-beaa-be561a0f0cac
ms.service: app-service-logic
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 07/15/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 0038c582d1a597dfcbb1fe8255b3a0fac1235e25


---
# <a name="get-started-with-the-dropbox-connector"></a>Introducción al conector de Dropbox
Conéctese a Dropbox para administrar los archivos. En Dropbox, puede realizar diversas acciones, como cargar, actualizar, obtener y eliminar archivos.

Para poder usar [un conector](apis-list.md), primero debe crear una aplicación lógica. Puede empezar [creando una aplicación lógica ahora](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="connect-to-dropbox"></a>Conexión con Dropbox
Para que la aplicación lógica pueda acceder a un servicio, primero debe crear una *conexión* con dicho servicio. Una conexión proporciona conectividad entre una aplicación lógica y otro servicio. Por ejemplo, para poder conectarse a Dropbox, primero necesita crear una *conexión* a Dropbox. Para ello, tendrá que especificar las credenciales que usa habitualmente para acceder al servicio al que desea conectarse. Por tanto, en el ejemplo de Dropbox, deberá escribir las credenciales de la cuenta de Dropbox para crear la conexión con este servicio. [Más información sobre las conexiones]()

### <a name="create-a-connection-to-dropbox"></a>Creación de una conexión con Dropbox
> [!INCLUDE [Steps to create a connection to Dropbox](../../includes/connectors-create-api-dropbox.md)]
> 
> 

## <a name="use-a-dropbox-trigger"></a>Uso de un desencadenador de Dropbox
Un desencadenador es un evento que se puede utilizar para iniciar el flujo de trabajo definido en una aplicación lógica. [Más información sobre los desencadenadores](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts).

En este ejemplo, vamos a usar el desencadenador **When a file is created (Cuando se crea un archivo)**. Cuando se active este desencadenador, se invocará la acción de Dropbox **Get file content using path (Obtener contenido del archivo mediante la ruta de acceso)**. 

1. Escriba *dropbox* en el cuadro de búsqueda del diseñador de Logic Apps y seleccione el desencadenador **Dropbox - When a file is created (Dropbox - Cuando se cree un archivo)**.      
   ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-trigger.PNG)  
2. Seleccione la carpeta en la que se va a supervisar la creación de archivos. Seleccione la opción … (marcada con un cuadro rojo) y busque la carpeta que quiere seleccionar para la entrada del desencadenador.  
   ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-trigger-2.PNG)  

## <a name="use-a-dropbox-action"></a>Uso de una acción de Dropbox
Una acción es una operación que se lleva a cabo mediante el flujo de trabajo definido en una aplicación lógica. [Más información acerca de las acciones](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts).

Ahora que se ha agregado el desencadenador, siga estos pasos para incorporar una acción que obtenga el contenido del nuevo archivo.

1. Seleccione **+ Nuevo paso** para agregar la acción que quiere que se ejecute cuando se cree un archivo.  
   ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-action.PNG)
2. Seleccione **Add an action**(Agregar una acción). Se abrirá el cuadro de búsqueda en el que podrá buscar cualquier acción que quiera realizar.  
   ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-action-2.PNG)
3. Escriba *dropbox* para buscar las acciones relacionadas con Dropbox.  
4. Seleccione **Dropbox - Get file content using path (Dropbox: obtener el contenido del archivo mediante la ruta de acceso)**. Esta será la acción que se ejecutará cuando se cree un archivo en la carpeta de Dropbox seleccionada. Se abre el bloque de control de acción. Si no lo ha hecho previamente, se le pedirá que autorice a la aplicación lógica para que pueda acceder a la cuenta de Dropbox.  
   ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-action-3.PNG)  
5. Seleccione la opción … (la encontrará a la derecha del control **Ruta de archivo**) y busque la ruta de archivo que quiere usar. También puede usar el token de la **ruta de archivo** para crear más rápido la aplicación lógica.  
   ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-action-4.PNG)  
6. Guarde el trabajo y cree un nuevo archivo en Dropbox para activar el flujo de trabajo.  

## <a name="technical-details"></a>Detalles técnicos
Estos son los detalles sobre los desencadenadores, las acciones y las respuestas compatibles con esta conexión:

## <a name="dropbox-triggers"></a>Desencadenadores de Dropbox
El conector de Dropbox dispone de los siguientes desencadenadores:  

| Desencadenador | Descripción |
| --- | --- |
| [Cuando se crea un archivo](connectors-create-api-dropbox.md#when-a-file-is-created) |Esta operación desencadena un flujo al crear un archivo en una carpeta. |
| [Cuando se modifica un archivo](connectors-create-api-dropbox.md#when-a-file-is-modified) |Esta operación desencadena un flujo al modificar un archivo en una carpeta. |

## <a name="dropbox-actions"></a>Acciones de Dropbox
El conector de Dropbox dispone de las siguientes acciones:

| Acción | Description |
| --- | --- |
| [Obtener metadatos de archivo](connectors-create-api-dropbox.md#get-file-metadata) |Esta operación obtiene los metadatos de un archivo. |
| [Actualizar archivo](connectors-create-api-dropbox.md#update-file) |Esta operación actualiza un archivo. |
| [Eliminar archivo](connectors-create-api-dropbox.md#delete-file) |Esta operación elimina un archivo. |
| [Obtener metadatos de archivo mediante la ruta de acceso](connectors-create-api-dropbox.md#get-file-metadata-using-path) |Esta operación obtiene los metadatos de un archivo mediante la ruta de acceso. |
| [Obtener contenido de archivo mediante la ruta de acceso](connectors-create-api-dropbox.md#get-file-content-using-path) |Esta operación obtiene el contenido de un archivo mediante la ruta de acceso. |
| [Obtener contenido de archivo](connectors-create-api-dropbox.md#get-file-content) |Esta operación obtiene el contenido de un archivo. |
| [Crear archivo](connectors-create-api-dropbox.md#create-file) |Esta operación crea un archivo. |
| [Copiar archivo](connectors-create-api-dropbox.md#copy-file) |Esta operación copia un archivo en Dropbox. |
| [Enumerar archivos de la carpeta](connectors-create-api-dropbox.md#list-files-in-folder) |Esta operación obtiene la lista de archivos y subcarpetas de una carpeta. |
| [Enumerar archivos de la carpeta raíz](connectors-create-api-dropbox.md#list-files-in-root-folder) |Esta operación obtiene la lista de archivos y subcarpetas de la carpeta raíz. |
| [Extraer archivo en la carpeta](connectors-create-api-dropbox.md#extract-archive-to-folder) |Esta operación extrae un archivo de almacenamiento en una carpeta (por ejemplo: .zip). |

### <a name="action-details"></a>Detalles de la acción
Estos son los detalles de las acciones y desencadenadores de este conector, junto con sus respuestas:

### <a name="get-file-metadata"></a>Obtención de metadatos de archivo
Esta operación obtiene los metadatos de un archivo. 

| Nombre de propiedad | Display Name (Nombre para mostrar) | Description |
| --- | --- | --- |
| id* |Archivo |Seleccionar un archivo |

El símbolo * indica que la propiedad es obligatoria.

#### <a name="output-details"></a>Detalles de salida
BlobMetadata

| Nombre de propiedad | Tipo de datos |
| --- | --- |
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
Esta operación actualiza un archivo. 

| Nombre de propiedad | Display Name (Nombre para mostrar) | Description |
| --- | --- | --- |
| id* |Archivo |Seleccionar un archivo |
| body* |Contenido del archivo |Contenido del archivo |

El símbolo * indica que la propiedad es obligatoria.

#### <a name="output-details"></a>Detalles de salida
BlobMetadata

| Nombre de propiedad | Tipo de datos |
| --- | --- |
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
| id* |Archivo |Seleccionar un archivo |

El símbolo * indica que la propiedad es obligatoria.

### <a name="get-file-metadata-using-path"></a>Obtener metadatos de archivo mediante la ruta de acceso
Esta operación obtiene los metadatos de un archivo mediante la ruta de acceso. 

| Nombre de propiedad | Display Name (Nombre para mostrar) | Description |
| --- | --- | --- |
| path* |Ruta de acceso del archivo |Seleccionar un archivo |

El símbolo * indica que la propiedad es obligatoria.

#### <a name="output-details"></a>Detalles de salida
BlobMetadata

| Nombre de propiedad | Tipo de datos |
| --- | --- |
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
Esta operación obtiene el contenido de un archivo mediante la ruta de acceso. 

| Nombre de propiedad | Display Name (Nombre para mostrar) | Description |
| --- | --- | --- |
| path* |Ruta de acceso del archivo |Seleccionar un archivo |

El símbolo * indica que la propiedad es obligatoria.

### <a name="get-file-content"></a>Obtener contenido de archivo
Esta operación obtiene el contenido de un archivo. 

| Nombre de propiedad | Display Name (Nombre para mostrar) | Description |
| --- | --- | --- |
| id* |Archivo |Seleccionar un archivo |

El símbolo * indica que la propiedad es obligatoria.

### <a name="create-file"></a>Crear archivo
Esta operación crea un archivo. 

| Nombre de propiedad | Display Name (Nombre para mostrar) | Descripción |
| --- | --- | --- |
| folderPath* |Ruta de acceso a la carpeta |Seleccionar una carpeta |
| name* |Nombre de archivo |Nombre del archivo |
| body* |Contenido del archivo |Contenido del archivo |

El símbolo * indica que la propiedad es obligatoria.

#### <a name="output-details"></a>Detalles de salida
BlobMetadata

| Nombre de propiedad | Tipo de datos |
| --- | --- |
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

### <a name="copy-file"></a>Copiar archivo
Esta operación copia un archivo en Dropbox. 

| Nombre de propiedad | Display Name (Nombre para mostrar) | Description |
| --- | --- | --- |
| source* |Dirección URL de origen |Dirección URL al archivo de origen |
| destination* |Ruta de acceso del archivo de destino |Ruta de acceso al archivo de destino, incluido el nombre del archivo de destino |
| overwrite |¿Sobrescribir? |Sobrescribe el archivo de destino si está establecido en 'true' |

El símbolo * indica que la propiedad es obligatoria.

#### <a name="output-details"></a>Detalles de salida
BlobMetadata

| Nombre de propiedad | Tipo de datos |
| --- | --- |
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

### <a name="when-a-file-is-created"></a>Cuando se crea un archivo
Esta operación desencadena un flujo al crear un archivo en una carpeta. 

| Nombre de propiedad | Display Name (Nombre para mostrar) | Descripción |
| --- | --- | --- |
| folderId* |Carpeta |Seleccionar una carpeta |

El símbolo * indica que la propiedad es obligatoria.

### <a name="when-a-file-is-modified"></a>Cuando se modifica un archivo
Esta operación desencadena un flujo al modificar un archivo en una carpeta. 

| Nombre de propiedad | Display Name (Nombre para mostrar) | Descripción |
| --- | --- | --- |
| folderId* |Carpeta |Seleccionar una carpeta |

El símbolo * indica que la propiedad es obligatoria.

### <a name="list-files-in-folder"></a>Enumerar archivos de la carpeta
Esta operación obtiene la lista de archivos y subcarpetas de una carpeta. 

| Nombre de propiedad | Display Name (Nombre para mostrar) | Description |
| --- | --- | --- |
| id* |Carpeta |Seleccionar una carpeta |

El símbolo * indica que la propiedad es obligatoria.

#### <a name="output-details"></a>Detalles de salida
BlobMetadata

| Nombre de propiedad | Tipo de datos |
| --- | --- |
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
Esta operación obtiene la lista de archivos y subcarpetas de la carpeta raíz. 

No hay parámetros para esta llamada

#### <a name="output-details"></a>Detalles de salida
BlobMetadata

| Nombre de propiedad | Tipo de datos |
| --- | --- |
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

### <a name="extract-archive-to-folder"></a>Extraer archivo en la carpeta
Esta operación extrae un archivo de almacenamiento en una carpeta (por ejemplo: .zip). 

| Nombre de propiedad | Display Name (Nombre para mostrar) | Description |
| --- | --- | --- |
| source* |Ruta de acceso del archivo de origen |Ruta de acceso al archivo de almacenamiento |
| destination* |Ruta de acceso a la carpeta de destino |Ruta de acceso para extraer el contenido del archivo |
| overwrite |¿Sobrescribir? |Sobrescribe los archivos de destino si está establecido en 'true' |

El símbolo * indica que la propiedad es obligatoria.

#### <a name="output-details"></a>Detalles de salida
BlobMetadata

| Nombre de propiedad | Tipo de datos |
| --- | --- |
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
[Creación de una aplicación lógica](../app-service-logic/app-service-logic-create-a-logic-app.md)




<!--HONumber=Nov16_HO3-->


