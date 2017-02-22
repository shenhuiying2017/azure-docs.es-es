---
title: "Adición del conector de OneDrive a Logic Apps | Microsoft Docs"
description: "Información general del conector de OneDrive con parámetros de la API de REST"
services: logic-apps
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: 47a8582a-1b1a-4fc3-beb5-97c60c4306fe
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 10/18/2016
ms.author: mandia
translationtype: Human Translation
ms.sourcegitcommit: 66fc8f7e1da55dbe6bb1dd8b8d6a535c498c1cf7
ms.openlocfilehash: 60c1d307ede5d66f8e92b048581148d6c7112dac


---
# <a name="get-started-with-the-onedrive-connector"></a>Introducción al conector de OneDrive
Conéctese a OneDrive para administrar los archivos, incluyendo las tareas de carga, obtención y eliminación de archivos, y muchas más. 

Con OneDrive, puede: 

* Crear un flujo de trabajo almacenando archivos en OneDrive o actualizar las archivos que ya tenga en OneDrive. 
* Usar desencadenadores para iniciar el flujo de trabajo cuando se crea o se actualiza un archivo en OneDrive.
* Usar acciones para crear o eliminar un archivo, entre otras muchas cosas. Por ejemplo, cuando se reciba un nuevo correo electrónico de Office 365 con datos adjuntos (desencadenador), cree un nuevo archivo en OneDrive (acción).

En este tema se muestra cómo usar el conector de OneDrive en una aplicación lógica, y se enumeran los desencadenadores y las acciones.

> [!NOTE]
> Esta versión del artículo se aplica a la disponibilidad general de las aplicaciones lógicas. 
> 
> 

Para más información sobre Logic Apps, consulte [¿Qué son las aplicaciones lógicas?](../logic-apps/logic-apps-what-are-logic-apps.md) y [Creación de una aplicación lógica](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="connect-to-onedrive"></a>Conexión a OneDrive
Antes de que la aplicación lógica pueda acceder a cualquier servicio, cree primero una *conexión* a este. Una conexión proporciona conectividad entre una aplicación lógica y otro servicio. Por ejemplo, para conectarse a OneDrive, primero necesita una *conexión* de OneDrive. Para crear una conexión, escriba las credenciales que utiliza normalmente para acceder al servicio al que desea conectarse. Por lo tanto, con OneDrive, escriba las credenciales de la cuenta de OneDrive para crear la conexión.

### <a name="create-the-connection"></a>Creación de la conexión
> [!INCLUDE [Steps to create a connection to OneDrive](../../includes/connectors-create-api-onedrive.md)]
> 
> 

## <a name="use-a-trigger"></a>Uso de un desencadenador
Un desencadenador es un evento que se puede utilizar para iniciar el flujo de trabajo definido en una aplicación lógica. Los desencadenadores "sondean" el servicio en el intervalo y la frecuencia que desee. [Más información sobre los desencadenadores](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts).

1. En la aplicación lógica, escriba "onedrive" para obtener una lista de los desencadenadores:  
   
    ![](./media/connectors-create-api-onedrive/onedrive-1.png)
2. Seleccione **Cuando se modifica un archivo**. Si ya existe una conexión, seleccione el botón Mostrar Selector para seleccionar una carpeta.
   
    ![](./media/connectors-create-api-onedrive/sample-folder.png)
   
    Si se le solicita que inicie sesión, escriba los datos de inicio de sesión para crear la conexión. En la sección [Creación de la conexión](connectors-create-api-onedrive.md#create-the-connection) de este tema se enumeran los pasos. 
   
   > [!NOTE]
   > En este ejemplo, la aplicación lógica se ejecuta cuando un archivo de la carpeta que elija se actualiza. Para ver los resultados de este desencadenador, agregue otra acción que envíe un correo electrónico. Por ejemplo, agregue la acción *Enviar un correo electrónico* de Office 365 Outlook que le envía un correo electrónico cuando se actualiza un archivo. 
   > 
   > 
3. Seleccione el botón **Editar** y defina los valores de **Frecuencia** e **Intervalo**. Por ejemplo, si desea que el desencadenador sondee cada 15 minutos, establezca el valor de **Frecuencia** en **Minuto** y el de **Intervalo** en **15**. 
   
    ![](./media/connectors-create-api-onedrive/trigger-properties.png)
4. **Guarde** los cambios (esquina superior izquierda de la barra de herramientas). La aplicación lógica se guarda y se puede habilitar automáticamente.

## <a name="use-an-action"></a>Uso de una acción
Una acción es una operación que se lleva a cabo mediante el flujo de trabajo definido en una aplicación lógica. [Más información acerca de las acciones](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts).

1. Seleccione el signo más. Aparecen varias opciones: **Agregar una acción**, **Agregar una condición** o una de las opciones de **Más**.
   
    ![](./media/connectors-create-api-onedrive/add-action.png)
2. Elija **Add an action**(Agregar una acción).
3. En el cuadro de texto, escriba "onedrive" para obtener una lista de todas las acciones disponibles.
   
    ![](./media/connectors-create-api-onedrive/onedrive-actions.png) 
4. En nuestro ejemplo, elija **OneDrive - Crear archivo**. Si ya existe una conexión, seleccione la **Ruta de la carpeta** en la que se incluirá el archivo, escriba el **Nombre de archivo** y elija el **Contenido de archivo** que desee:  
   
    ![](./media/connectors-create-api-onedrive/sample-action.png)
   
    Si se le solicita la información de conexión, escriba los detalles para crear la conexión. Estas propiedades se describen en la sección [Creación de la conexión](connectors-create-api-onedrive.md#create-the-connection) de este tema. 
   
   > [!NOTE]
   > En este ejemplo, creamos un nuevo archivo en una carpeta de OneDrive. Puede utilizar la salida de otro desencadenador para crear el archivo de OneDrive. Por ejemplo, agregue el desencadenador *Cuando llega un nuevo correo electrónico* de Office 365 Outlook. A continuación, agregue la acción *Crear archivo* de OneDrive, que usa los campos Attachments y Content-Type de una instrucción ForEach para crear el nuevo archivo en OneDrive. 
   > 
   > ![](./media/connectors-create-api-onedrive/foreach-action.png)
   > 
   > 
5. **Guarde** los cambios (esquina superior izquierda de la barra de herramientas). La aplicación lógica se guarda y se puede habilitar automáticamente.

## <a name="technical-details"></a>Detalles técnicos
## <a name="triggers"></a>Desencadenadores
| Desencadenador | Descripción |
| --- | --- |
| [Cuando se crea un archivo](connectors-create-api-onedrive.md#when-a-file-is-created) |Esta operación desencadena un flujo al crear un archivo en una carpeta. |
| [Cuando se modifica un archivo](connectors-create-api-onedrive.md#when-a-file-is-modified) |Esta operación desencadena un flujo al modificar un archivo en una carpeta. |

## <a name="actions"></a>Acciones
| Acción | Description |
| --- | --- |
| [Obtener metadatos de archivo](connectors-create-api-onedrive.md#get-file-metadata) |Esta operación obtiene los metadatos de un archivo. |
| [Actualizar archivo](connectors-create-api-onedrive.md#update-file) |Esta operación actualiza un archivo. |
| [Eliminar archivo](connectors-create-api-onedrive.md#delete-file) |Esta operación elimina un archivo. |
| [Obtener metadatos de archivo mediante la ruta de acceso](connectors-create-api-onedrive.md#get-file-metadata-using-path) |Esta operación obtiene los metadatos de un archivo mediante la ruta de acceso. |
| [Obtener contenido de archivo mediante la ruta de acceso](connectors-create-api-onedrive.md#get-file-content-using-path) |Esta operación obtiene el contenido de un archivo mediante la ruta de acceso. |
| [Obtener contenido de archivo](connectors-create-api-onedrive.md#get-file-content) |Esta operación obtiene el contenido de un archivo. |
| [Crear archivo](connectors-create-api-onedrive.md#create-file) |Esta operación crea un archivo. |
| [Copiar archivo](connectors-create-api-onedrive.md#copy-file) |Esta operación copia un archivo en OneDrive. |
| [Enumerar archivos de la carpeta](connectors-create-api-onedrive.md#list-files-in-folder) |Esta operación obtiene la lista de archivos y subcarpetas de una carpeta. |
| [Enumerar archivos de la carpeta raíz](connectors-create-api-onedrive.md#list-files-in-root-folder) |Esta operación obtiene la lista de archivos y subcarpetas de la carpeta raíz. |
| [Extraer archivo en la carpeta](connectors-create-api-onedrive.md#extract-archive-to-folder) |Esta operación extrae un archivo de almacenamiento en una carpeta (por ejemplo: .zip). |

### <a name="action-details"></a>Detalles de la acción
En esta sección, consulte los detalles específicos acerca de cada acción, incluidas las propiedades de entrada obligatorias u opcionales y cualquier salida correspondiente asociada con el conector.

#### <a name="get-file-metadata"></a>Obtener metadatos de archivo
Esta operación obtiene los metadatos de un archivo. 

| Nombre de propiedad | Display Name (Nombre para mostrar) | Description |
| --- | --- | --- |
| id* |Archivo |Seleccionar un archivo |

Un asterisco (*) significa que la propiedad es obligatoria.

##### <a name="output-details"></a>Detalles de salida
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

#### <a name="update-file"></a>Actualizar archivo
Esta operación actualiza un archivo. 

| Nombre de propiedad | Display Name (Nombre para mostrar) | Description |
| --- | --- | --- |
| id* |Archivo |Seleccionar un archivo |
| body* |Contenido del archivo |Contenido del archivo |

Un asterisco (*) significa que la propiedad es obligatoria.

##### <a name="output-details"></a>Detalles de salida
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

#### <a name="delete-file"></a>Eliminar archivo
Esta operación elimina un archivo. 

| Nombre de propiedad | Display Name (Nombre para mostrar) | Description |
| --- | --- | --- |
| id* |Archivo |Seleccionar un archivo |

Un asterisco (*) significa que la propiedad es obligatoria.

##### <a name="output-details"></a>Detalles de salida
Ninguno.

#### <a name="get-file-metadata-using-path"></a>Obtener metadatos de archivo mediante la ruta de acceso
Esta operación obtiene los metadatos de un archivo mediante la ruta de acceso. 

| Nombre de propiedad | Display Name (Nombre para mostrar) | Description |
| --- | --- | --- |
| path* |Ruta de acceso del archivo |Seleccionar un archivo |

Un asterisco (*) significa que la propiedad es obligatoria.

##### <a name="output-details"></a>Detalles de salida
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

#### <a name="get-file-content-using-path"></a>Obtener contenido de archivo mediante la ruta de acceso
Esta operación obtiene el contenido de un archivo mediante la ruta de acceso. 

| Nombre de propiedad | Display Name (Nombre para mostrar) | Description |
| --- | --- | --- |
| path* |Ruta de acceso del archivo |Seleccionar un archivo |

Un asterisco (*) significa que la propiedad es obligatoria.

##### <a name="output-details"></a>Detalles de salida
Ninguno.

#### <a name="get-file-content"></a>Obtener contenido de archivo
Esta operación obtiene el contenido de un archivo. 

| Nombre de propiedad | Display Name (Nombre para mostrar) | Description |
| --- | --- | --- |
| id* |Archivo |Seleccionar un archivo |

Un asterisco (*) significa que la propiedad es obligatoria.

##### <a name="output-details"></a>Detalles de salida
Ninguno.

#### <a name="create-file"></a>Crear archivo
Esta operación crea un archivo. 

| Nombre de propiedad | Display Name (Nombre para mostrar) | Descripción |
| --- | --- | --- |
| folderPath* |Ruta de acceso a la carpeta |Seleccionar una carpeta |
| name* |Nombre de archivo |Nombre del archivo |
| body* |Contenido del archivo |Contenido del archivo |

Un asterisco (*) significa que la propiedad es obligatoria.

##### <a name="output-details"></a>Detalles de salida
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

#### <a name="copy-file"></a>Copiar archivo
Esta operación copia un archivo en OneDrive. 

| Nombre de propiedad | Display Name (Nombre para mostrar) | Description |
| --- | --- | --- |
| source* |Dirección URL de origen |Dirección URL al archivo de origen |
| destination* |Ruta de acceso del archivo de destino |Ruta de acceso al archivo de destino, incluido el nombre del archivo de destino |
| overwrite |¿Sobrescribir? |Sobrescribe el archivo de destino si está establecido en 'true' |

Un asterisco (*) significa que la propiedad es obligatoria.

##### <a name="output-details"></a>Detalles de salida
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

#### <a name="when-a-file-is-created"></a>Cuando se crea un archivo
Esta operación desencadena un flujo al crear un archivo en una carpeta. 

| Nombre de propiedad | Display Name (Nombre para mostrar) | Descripción |
| --- | --- | --- |
| folderId* |Carpeta |Seleccionar una carpeta |

Un asterisco (*) significa que la propiedad es obligatoria.

##### <a name="output-details"></a>Detalles de salida
Ninguno.

#### <a name="when-a-file-is-modified"></a>Cuando se modifica un archivo
Esta operación desencadena un flujo al modificar un archivo en una carpeta. 

| Nombre de propiedad | Display Name (Nombre para mostrar) | Descripción |
| --- | --- | --- |
| folderId* |Carpeta |Seleccionar una carpeta |

Un asterisco (*) significa que la propiedad es obligatoria.

##### <a name="output-details"></a>Detalles de salida
Ninguno.

#### <a name="list-files-in-folder"></a>Enumerar archivos de la carpeta
Esta operación obtiene la lista de archivos y subcarpetas de una carpeta.

| Nombre de propiedad | Display Name (Nombre para mostrar) | Description |
| --- | --- | --- |
| id* |Carpeta |Seleccionar una carpeta |

Un asterisco (*) significa que la propiedad es obligatoria.

##### <a name="output-details"></a>Detalles de salida
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

#### <a name="list-files-in-root-folder"></a>Enumerar archivos de la carpeta raíz
Esta operación obtiene la lista de archivos y subcarpetas de la carpeta raíz. 

No hay parámetros para esta llamada.

##### <a name="output-details"></a>Detalles de salida
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

#### <a name="extract-archive-to-folder"></a>Extraer archivo en la carpeta
Esta operación extrae un archivo de almacenamiento en una carpeta (por ejemplo: .zip). 

| Nombre de propiedad | Display Name (Nombre para mostrar) | Description |
| --- | --- | --- |
| source* |Ruta de acceso del archivo de origen |Ruta de acceso al archivo de almacenamiento |
| destination* |Ruta de acceso a la carpeta de destino |Ruta de acceso para extraer el contenido del archivo |
| overwrite |¿Sobrescribir? |Sobrescribe los archivos de destino si está establecido en 'true' |

Un asterisco (*) significa que la propiedad es obligatoria.

##### <a name="output-details"></a>Detalles de salida
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
En la tabla siguiente se enumeran y describen las respuestas a las acciones y los desencadenadores:  

| Nombre | Descripción |
| --- | --- |
| 200 |OK |
| 202 |Accepted |
| 400 |Bad Request |
| 401 |No autorizado |
| 403 |Prohibido |
| 404 |No encontrado |
| 500 |Error interno del servidor. Error desconocido |
| default |Error en la operación. |

## <a name="next-steps"></a>Pasos siguientes
[Creación de una aplicación lógica](../logic-apps/logic-apps-create-a-logic-app.md). Explore los demás conectores disponibles en Logic Apps en nuestra [lista de API](apis-list.md).




<!--HONumber=Jan17_HO3-->


