---
title: Adición del conector de OneDrive a las aplicaciones lógicas | Microsoft Docs
description: Información general del conector de OneDrive con parámetros de la API de REST
services: logic-apps
documentationcenter: ''
author: MandiOhlinger
manager: erikre
editor: ''
tags: connectors

ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 07/26/2016
ms.author: mandia

---
# Introducción al conector de OneDrive
Conéctese a OneDrive para administrar los archivos, incluyendo las tareas de carga, obtención y eliminación de archivos, y muchas más.

Con OneDrive, puede:

* Crear un flujo de trabajo almacenando archivos en OneDrive o actualizar las archivos que ya tenga en OneDrive.
* Usar desencadenadores para iniciar el flujo de trabajo cuando se crea o se actualiza un archivo en OneDrive.
* Usar acciones para crear o eliminar un archivo, entre otras muchas cosas. Por ejemplo, cuando se reciba un nuevo correo electrónico de Office 365 con datos adjuntos (desencadenador), cree un nuevo archivo en OneDrive (acción).

En este tema se muestra cómo usar el conector de OneDrive en una aplicación lógica y también se enumeran los desencadenadores y las acciones.

> [!NOTE]
> Esta versión del artículo se aplica a la disponibilidad general de las aplicaciones lógicas.
> 
> 

Para más información sobre Logic Apps, consulte [¿Qué son las aplicaciones lógicas?](../app-service-logic/app-service-logic-what-are-logic-apps.md) y [Creación de una aplicación lógica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Conexión a OneDrive
Antes de que la aplicación lógica pueda acceder a cualquier servicio, cree primero una *conexión* a este. Una conexión proporciona conectividad entre una aplicación lógica y otro servicio. Por ejemplo, para conectarse a OneDrive, primero necesita una *conexión* de OneDrive. Para crear una conexión, escriba las credenciales que utiliza normalmente para acceder al servicio al que desea conectarse. Por lo tanto, con OneDrive, escriba las credenciales de la cuenta de OneDrive para crear la conexión.

### Creación de la conexión
> [!INCLUDE [Pasos para crear una conexión a OneDrive](../../includes/connectors-create-api-onedrive.md)]
> 
> 

## Uso de un desencadenador
Un desencadenador es un evento que se puede utilizar para iniciar el flujo de trabajo definido en una aplicación lógica. Los desencadenadores "sondean" el servicio en el intervalo y la frecuencia que desee. [Más información sobre los desencadenadores](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts).

1. En la aplicación lógica, escriba "onedrive" para obtener una lista de los desencadenadores:
   
    ![](./media/connectors-create-api-onedrive/onedrive-1.png)
2. Seleccione **When a file is modified** (Cuando se modifica un archivo). Si ya existe una conexión, seleccione el botón Mostrar Selector para seleccionar una carpeta.
   
    ![](./media/connectors-create-api-onedrive/sample-folder.png)
   
    Si se le solicita que inicie sesión, escriba los datos de inicio de sesión para crear la conexión. En este tema, en [Creación de la conexión](connectors-create-api-onedrive.md#create-the-connection) se enumeran los pasos.
   
   > [!NOTE]
   > En este ejemplo, la aplicación lógica se ejecuta cuando un archivo de la carpeta que elija se actualiza. Para ver los resultados de este desencadenador, agregue otra acción que envíe un correo electrónico. Por ejemplo, agregue la acción *Send an email* (Enviar un correo electrónico) de Outlook para Office 365 que le envía un correo electrónico cuando se actualiza un archivo.
   > 
   > 
3. Seleccione el botón **Editar** y defina los valores para **Frecuencia** e **Intervalo**. Por ejemplo, si desea que el desencadenador sondee cada 15 minutos, establezca el valor **Frecuencia** en **Minuto** y el de **Intervalo** en **15**.
   
    ![](./media/connectors-create-api-onedrive/trigger-properties.png)
4. **Guarde** los cambios (esquina superior izquierda de la barra de herramientas). La aplicación lógica se guarda y se puede habilitar automáticamente.

## Uso de una acción
Una acción es una operación que se lleva a cabo mediante el flujo de trabajo definido en una aplicación lógica. [Más información sobre las acciones](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts).

1. Seleccione el signo más. Aparecen varias opciones: **Add an action** (Agregar una acción), **Add a condition** (Agregar una condición) o una de las opciones de **More** (Más).
   
    ![](./media/connectors-create-api-onedrive/add-action.png)
2. Elija **Add an action** (Agregar una acción).
3. En el cuadro de texto, escriba "onedrive" para obtener una lista de todas las acciones disponibles.
   
    ![](./media/connectors-create-api-onedrive/onedrive-actions.png)
4. En nuestro ejemplo, elija **OneDrive - Create file** (OneDrive - Crear archivo). Si ya existe una conexión, seleccione la **ruta de la carpeta** en la que incluir el archivo, escriba el **nombre de archivo** y elija el **contenido del archivo** que desee:
   
    ![](./media/connectors-create-api-onedrive/sample-action.png)
   
    Si se le solicita la información de conexión, escriba los detalles para crear la conexión. Estas propiedades se describen en la sección [Creación de la conexión](connectors-create-api-onedrive.md#create-the-connection) de este tema.
   
   > [!NOTE]
   > En este ejemplo, creamos un nuevo archivo en una carpeta de OneDrive. Puede utilizar la salida de otro desencadenador para crear el archivo de OneDrive. Por ejemplo, agregue el desencadenador *When a new email arrives* (Cuando llega un nuevo correo electrónico) de Outlook para Office 365. A continuación, agregue la acción *Create file* (Crear archivo) de OneDrive, que usa los campos Datos adjuntos y Tipo de contenido de una instrucción ForEach para crear el nuevo archivo en OneDrive.
   > 
   > ![](./media/connectors-create-api-onedrive/foreach-action.png)
   > 
   > 
5. **Guarde** los cambios (esquina superior izquierda de la barra de herramientas). La aplicación lógica se guarda y se puede habilitar automáticamente.

## Detalles técnicos
## Desencadenadores
| Desencadenador | Descripción |
| --- | --- |
| [Cuando se crea un archivo](connectors-create-api-onedrive.md#when-a-file-is-created) |Esta operación desencadena un flujo al crear un archivo en una carpeta. |
| [Cuando se modifica un archivo](connectors-create-api-onedrive.md#when-a-file-is-modified) |Esta operación desencadena un flujo al modificar un archivo en una carpeta. |

## Acciones
| Acción | Descripción |
| --- | --- |
| [Obtención de metadatos de archivo](connectors-create-api-onedrive.md#get-file-metadata) |Esta operación obtiene los metadatos de un archivo. |
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

### Detalles de la acción
En esta sección, consulte los detalles específicos acerca de cada acción, incluidas las propiedades de entrada obligatorias u opcionales y cualquier salida correspondiente asociada con el conector.

#### Obtención de metadatos de archivo
Esta operación obtiene los metadatos de un archivo.

| Nombre de propiedad | Display Name (Nombre para mostrar) | Descripción |
| --- | --- | --- |
| id* |Archivo |Seleccionar un archivo |

Un asterisco (*) significa que la propiedad es obligatoria.

##### Detalles de salida
BlobMetadata

| Nombre de propiedad | Tipo de datos |
| --- | --- |
| Id |cadena |
| Nombre |cadena |
| DisplayName |cadena |
| Ruta de acceso |cadena |
| LastModified |cadena |
| Tamaño |integer |
| MediaType |cadena |
| IsFolder |boolean |
| ETag |cadena |
| FileLocator |cadena |

#### Actualizar archivo
Esta operación actualiza un archivo.

| Nombre de propiedad | Display Name (Nombre para mostrar) | Descripción |
| --- | --- | --- |
| id* |Archivo |Seleccionar un archivo |
| body* |Contenido del archivo |Contenido del archivo |

Un asterisco (*) significa que la propiedad es obligatoria.

##### Detalles de salida
BlobMetadata

| Nombre de propiedad | Tipo de datos |
| --- | --- |
| Id |cadena |
| Nombre |cadena |
| DisplayName |cadena |
| Ruta de acceso |cadena |
| LastModified |cadena |
| Tamaño |integer |
| MediaType |cadena |
| IsFolder |boolean |
| ETag |cadena |
| FileLocator |cadena |

#### Eliminar archivo
Esta operación elimina un archivo.

| Nombre de propiedad | Display Name (Nombre para mostrar) | Descripción |
| --- | --- | --- |
| id* |Archivo |Seleccionar un archivo |

Un asterisco (*) significa que la propiedad es obligatoria.

##### Detalles de salida
Ninguno.

#### Obtener metadatos de archivo mediante la ruta de acceso
Esta operación obtiene los metadatos de un archivo mediante la ruta de acceso.

| Nombre de propiedad | Display Name (Nombre para mostrar) | Descripción |
| --- | --- | --- |
| path* |Ruta de acceso del archivo |Seleccionar un archivo |

Un asterisco (*) significa que la propiedad es obligatoria.

##### Detalles de salida
BlobMetadata

| Nombre de propiedad | Tipo de datos |
| --- | --- |
| Id |cadena |
| Nombre |cadena |
| DisplayName |cadena |
| Ruta de acceso |cadena |
| LastModified |cadena |
| Tamaño |integer |
| MediaType |cadena |
| IsFolder |boolean |
| ETag |cadena |
| FileLocator |cadena |

#### Obtener contenido de archivo mediante la ruta de acceso
Esta operación obtiene el contenido de un archivo mediante la ruta de acceso.

| Nombre de propiedad | Display Name (Nombre para mostrar) | Descripción |
| --- | --- | --- |
| path* |Ruta de acceso del archivo |Seleccionar un archivo |

Un asterisco (*) significa que la propiedad es obligatoria.

##### Detalles de salida
Ninguno.

#### Obtener contenido de archivo
Esta operación obtiene el contenido de un archivo.

| Nombre de propiedad | Display Name (Nombre para mostrar) | Descripción |
| --- | --- | --- |
| id* |Archivo |Seleccionar un archivo |

Un asterisco (*) significa que la propiedad es obligatoria.

##### Detalles de salida
Ninguno.

#### Crear archivo
Esta operación crea un archivo.

| Nombre de propiedad | Display Name (Nombre para mostrar) | Descripción |
| --- | --- | --- |
| folderPath* |Ruta de acceso a la carpeta |Seleccionar una carpeta |
| name* |Nombre de archivo |Nombre del archivo |
| body* |Contenido del archivo |Contenido del archivo |

Un asterisco (*) significa que la propiedad es obligatoria.

##### Detalles de salida
BlobMetadata

| Nombre de propiedad | Tipo de datos |
| --- | --- |
| Id |cadena |
| Nombre |cadena |
| DisplayName |cadena |
| Ruta de acceso |cadena |
| LastModified |cadena |
| Tamaño |integer |
| MediaType |cadena |
| IsFolder |boolean |
| ETag |cadena |
| FileLocator |cadena |

#### Copiar archivo
Esta operación copia un archivo en OneDrive.

| Nombre de propiedad | Display Name (Nombre para mostrar) | Descripción |
| --- | --- | --- |
| source* |Dirección URL de origen |Dirección URL al archivo de origen |
| destination* |Ruta de acceso del archivo de destino |Ruta de acceso al archivo de destino, incluido el nombre del archivo de destino |
| overwrite |¿Sobrescribir? |Sobrescribe el archivo de destino si está establecido en 'true' |

Un asterisco (*) significa que la propiedad es obligatoria.

##### Detalles de salida
BlobMetadata

| Nombre de propiedad | Tipo de datos |
| --- | --- |
| Id |cadena |
| Nombre |cadena |
| DisplayName |cadena |
| Ruta de acceso |cadena |
| LastModified |cadena |
| Tamaño |integer |
| MediaType |cadena |
| IsFolder |boolean |
| ETag |cadena |
| FileLocator |cadena |

#### Cuando se crea un archivo
Esta operación desencadena un flujo al crear un archivo en una carpeta.

| Nombre de propiedad | Display Name (Nombre para mostrar) | Descripción |
| --- | --- | --- |
| folderId* |Carpeta |Seleccionar una carpeta |

Un asterisco (*) significa que la propiedad es obligatoria.

##### Detalles de salida
Ninguno.

#### Cuando se modifica un archivo
Esta operación desencadena un flujo al modificar un archivo en una carpeta.

| Nombre de propiedad | Display Name (Nombre para mostrar) | Descripción |
| --- | --- | --- |
| folderId* |Carpeta |Seleccionar una carpeta |

Un asterisco (*) significa que la propiedad es obligatoria.

##### Detalles de salida
Ninguno.

#### Enumerar archivos de la carpeta
Esta operación obtiene la lista de archivos y subcarpetas de una carpeta.

| Nombre de propiedad | Display Name (Nombre para mostrar) | Descripción |
| --- | --- | --- |
| id* |Carpeta |Seleccionar una carpeta |

Un asterisco (*) significa que la propiedad es obligatoria.

##### Detalles de salida
BlobMetadata

| Nombre de propiedad | Tipo de datos |
| --- | --- |
| Id |cadena |
| Nombre |cadena |
| DisplayName |cadena |
| Ruta de acceso |cadena |
| LastModified |cadena |
| Tamaño |integer |
| MediaType |cadena |
| IsFolder |boolean |
| ETag |cadena |
| FileLocator |cadena |

#### Enumerar archivos de la carpeta raíz
Esta operación obtiene la lista de archivos y subcarpetas de la carpeta raíz.

No hay parámetros para esta llamada.

##### Detalles de salida
BlobMetadata

| Nombre de propiedad | Tipo de datos |
| --- | --- |
| Id |cadena |
| Nombre |cadena |
| DisplayName |cadena |
| Ruta de acceso |cadena |
| LastModified |cadena |
| Tamaño |integer |
| MediaType |cadena |
| IsFolder |boolean |
| ETag |cadena |
| FileLocator |cadena |

#### Extraer archivo en la carpeta
Esta operación extrae un archivo de almacenamiento en una carpeta (por ejemplo: .zip).

| Nombre de propiedad | Display Name (Nombre para mostrar) | Descripción |
| --- | --- | --- |
| source* |Ruta de acceso del archivo de origen |Ruta de acceso al archivo de almacenamiento |
| destination* |Ruta de acceso a la carpeta de destino |Ruta de acceso para extraer el contenido del archivo |
| overwrite |¿Sobrescribir? |Sobrescribe los archivos de destino si está establecido en 'true' |

Un asterisco (*) significa que la propiedad es obligatoria.

##### Detalles de salida
BlobMetadata

| Nombre de propiedad | Tipo de datos |
| --- | --- |
| Id |cadena |
| Nombre |cadena |
| DisplayName |cadena |
| Ruta de acceso |cadena |
| LastModified |cadena |
| Tamaño |integer |
| MediaType |cadena |
| IsFolder |boolean |
| ETag |cadena |
| FileLocator |cadena |

## Respuestas HTTP
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

## Pasos siguientes
[Creación de una aplicación lógica](../app-service-logic/app-service-logic-create-a-logic-app.md). Explore los demás conectores disponibles en Logic Apps en nuestra [lista de API](apis-list.md).

<!---HONumber=AcomDC_0803_2016-->