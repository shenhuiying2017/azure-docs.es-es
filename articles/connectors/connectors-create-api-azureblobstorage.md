<properties
    pageTitle="Incorporación del conector de almacenamiento de blobs de Azure en las aplicaciones lógicas | Microsoft Azure"
    description="Información general sobre el conector de almacenamiento de blobs de Azure con los parámetros de la API de REST"
    services=""
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
   ms.date="07/18/2016"
   ms.author="mandia"/>

# Introducción al conector de almacenamiento de blobs de Azure
Almacenamiento de blobs de Azure es un servicio para almacenar grandes cantidades de datos no estructurados. Puede realizar diversas acciones en archivos como cargar, actualizar, obtener y eliminar blobs en el almacenamiento de blobs de Azure.

- Genere el flujo de trabajo mediante la carga de nuevos proyectos o la obtención de archivos que se hayan actualizado recientemente.
- Use acciones para obtener metadatos de archivos, eliminar un archivo, copiar archivos y muchas otras cosas. Por ejemplo, si se actualiza una herramienta en un sitio web de Azure (un desencadenador), actualice a continuación un archivo en el almacenamiento de blobs (una acción).

En este tema se muestra cómo usar el conector de almacenamiento de blobs en una aplicación lógica y también se enumeran las acciones.

>[AZURE.NOTE] Esta versión del artículo se aplica a la disponibilidad general de las aplicaciones lógicas.

Introducción a la [creación de una aplicación lógica](../app-service-logic/app-service-logic-create-a-logic-app.md).

>[AZURE.INCLUDE [Qué necesita para empezar](../../includes/connectors-create-api-azureblobstorage.md)]


## Conexión con el almacenamiento de blobs de Azure

Antes de que la aplicación lógica pueda acceder a cualquier servicio, cree primero una *conexión* a este. Una conexión proporciona conectividad entre una aplicación lógica y otro servicio. Por ejemplo, para conectarse a Dropbox, debe crear primero una *conexión* a Dropbox. Para crear una conexión, escriba las credenciales que utiliza normalmente para acceder al servicio al que se está conectando. De esta forma, en el ejemplo de Dropbox, escriba sus credenciales de Dropbox para crear la conexión a este servicio.

Cuando agrega este conector a las aplicaciones lógicas, crea una conexión a la cuenta de almacenamiento de blobs. La primera vez que agregue este conector, se le pedirá la información de conexión:

![](./media/connectors-create-api-azureblobstorage/connection-details.png)


#### Creación de la conexión

1. Escriba los detalles de la cuenta de almacenamiento. Aquellas propiedades con un asterisco son obligatorias.

	| Propiedad | Detalles |
|---|---|
| Nombre de la conexión * | Escriba cualquier nombre para la conexión. |
| Nombre de la cuenta de Almacenamiento de Azure * | Escriba el nombre de la cuenta de almacenamiento. El nombre de la cuenta de almacenamiento se muestra en las propiedades de almacenamiento del Portal de Azure. |
| Clave de acceso de la cuenta de Almacenamiento de Azure * | Escriba la clave de la cuenta de almacenamiento. Las claves de acceso se muestran en las propiedades de almacenamiento del Portal de Azure. |

	Estas credenciales se usan para autorizar a la aplicación lógica a conectarse y acceder a sus datos. Una vez completado, los detalles de la conexión presentan un aspecto similar al siguiente:

	![Paso de creación de conexión de blobs de Azure](./media/connectors-create-api-azureblobstorage/sample-connection.png)

2. Seleccione **Crear**.

 
## Uso de un desencadenador

Este conector no tiene ningún desencadenador. Utilice otros desencadenadores para iniciar la aplicación lógica como, por ejemplo, un desencadenador de periodicidad, un desencadenador de Webhook HTTP, los desencadenadores disponibles con otros conectores y otros varios. La [creación de una aplicación lógica](../app-service-logic/app-service-logic-create-a-logic-app.md) sirve de ejemplo.

## Uso de una acción
	
Una acción es una operación que se lleva a cabo mediante el flujo de trabajo definido en una aplicación lógica.

1. Seleccione el signo más. Aparecen varias opciones: **Agregar una acción**, **Agregar una condición** o una de las opciones de **Más**.

	![](./media/connectors-create-api-azureblobstorage/add-action.png)

2. Elija **Agregar una acción**.

3. En el cuadro de texto, escriba "blob" para obtener una lista de todas las acciones disponibles.

	![](./media/connectors-create-api-azureblobstorage/actions.png)

4. En nuestro ejemplo, elija **AzureBlob: obtener metadatos de archivo mediante la ruta de acceso**. Si ya existe una conexión, seleccione el botón **...** (Mostrar selector) para seleccionar un archivo.

	![](./media/connectors-create-api-azureblobstorage/sample-file.png)

	Si se le solicita la información de conexión, escriba los detalles para crear la conexión. [Creación de la conexión](connectors-create-api-azureblobstorage.md#create-the-connection): en este tema se describen estas propiedades.

	> [AZURE.NOTE] En este ejemplo, obtenemos los metadatos de un archivo. Para ver los metadatos, agregue otra acción que cree un archivo nuevo mediante otro conector. Por ejemplo, agregue una acción de OneDrive que cree un nuevo archivo de "prueba" basándose en los metadatos.

5. **Guarde** los cambios (esquina superior izquierda de la barra de herramientas). La aplicación lógica se guarda y se puede habilitar automáticamente.

> [AZURE.TIP] [Storage Explorer](http://storageexplorer.com/) es una excelente herramienta para administrar varias cuentas de almacenamiento.

## Detalles técnicos

## Acciones

|Acción|Descripción|
|--- | ---|
|[Obtención de metadatos de archivo](connectors-create-api-azureblobstorage.md#get-file-metadata)|Esta operación obtiene metadatos de archivo mediante el identificador de archivo.|
|[Actualizar archivo](connectors-create-api-azureblobstorage.md#update-file)|Esta operación actualiza un archivo.|
|[Eliminar archivo](connectors-create-api-azureblobstorage.md#delete-file)|Esta operación elimina un archivo.|
|[Obtener metadatos de archivo mediante la ruta de acceso](connectors-create-api-azureblobstorage.md#get-file-metadata-using-path)|Esta operación obtiene metadatos de archivo mediante la ruta de acceso.|
|[Obtener contenido de archivo mediante la ruta de acceso](connectors-create-api-azureblobstorage.md#get-file-content-using-path)|Esta operación obtiene el contenido de archivo mediante la ruta de acceso.|
|[Obtener contenido de archivo](connectors-create-api-azureblobstorage.md#get-file-content)|Esta operación obtiene el contenido de archivo mediante el identificador.|
|[Crear archivo](connectors-create-api-azureblobstorage.md#create-file)|Esta operación carga un archivo.|
|[Copiar archivo](connectors-create-api-azureblobstorage.md#copy-file)|Esta operación copia un archivo en el Almacenamiento de blobs de Azure.|
|[Extraer archivo en la carpeta](connectors-create-api-azureblobstorage.md#extract-archive-to-folder)|Esta operación extrae un archivo de almacenamiento en una carpeta (por ejemplo: .zip).|

### Detalles de la acción

En esta sección, consulte los detalles específicos acerca de cada acción, incluidas las propiedades de entrada obligatorias u opcionales y cualquier salida correspondiente asociada con el conector.

#### Obtención de metadatos de archivo
Esta operación obtiene metadatos de archivo mediante el identificador de archivo.

|Nombre de propiedad| Display Name (Nombre para mostrar)|Descripción|
| ---|---|---|
|id*|Archivo|Seleccionar un archivo|

Un asterisco (*) significa que la propiedad es obligatoria.

##### Detalles de salida
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


#### Actualizar archivo
Esta operación actualiza un archivo.

|Nombre de propiedad| Display Name (Nombre para mostrar)|Descripción|
| ---|---|---|
|id*|Archivo|Seleccionar un archivo|
|body*|Contenido del archivo|Contenido del archivo que se va a actualizar|

Un asterisco (*) significa que la propiedad es obligatoria.

##### Detalles de salida
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


#### Eliminar archivo
Esta operación elimina un archivo.

|Nombre de propiedad| Display Name (Nombre para mostrar)|Descripción|
| ---|---|---|
|id*|Archivo|Seleccionar un archivo|

Un asterisco (*) significa que la propiedad es obligatoria.

##### Detalles de salida
Ninguno.


#### Obtener metadatos de archivo mediante la ruta de acceso
Esta operación obtiene metadatos de archivo mediante la ruta de acceso.

|Nombre de propiedad| Display Name (Nombre para mostrar)|Descripción|
| ---|---|---|
|path*|Ruta de acceso del archivo|Seleccionar un archivo|

Un asterisco (*) significa que la propiedad es obligatoria.

##### Detalles de salida
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


#### Obtener contenido de archivo mediante la ruta de acceso
Esta operación obtiene el contenido de archivo mediante la ruta de acceso.

|Nombre de propiedad| Display Name (Nombre para mostrar)|Descripción|
| ---|---|---|
|path*|Ruta de acceso del archivo|Seleccionar un archivo|

Un asterisco (*) significa que la propiedad es obligatoria.

##### Detalles de salida
Ninguno.


#### Obtener contenido de archivo
Esta operación obtiene el contenido de archivo mediante el identificador.

|Nombre de propiedad| Tipo de datos|Descripción|
| ---|---|---|
|id*|cadena|Seleccionar un archivo|

Un asterisco (*) significa que la propiedad es obligatoria.

##### Detalles de salida
Ninguno.


#### Crear archivo
Esta operación carga un archivo.

|Nombre de propiedad| Display Name (Nombre para mostrar)|Descripción|
| ---|---|---|
|folderPath*|Ruta de acceso a la carpeta|Seleccionar una carpeta|
|name*|Nombre de archivo|Nombre del archivo que va a cargar|
|body*|Contenido del archivo|Contenido del archivo que va a cargar|

Un asterisco (*) significa que la propiedad es obligatoria.

##### Detalles de salida
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


#### Copiar archivo
Esta operación copia un archivo en el Almacenamiento de blobs de Azure.

|Nombre de propiedad| Display Name (Nombre para mostrar)|Descripción|
| ---|---|---|
|source*|Dirección URL de origen|Especifica la dirección URL al archivo de origen|
|destination*|Ruta de acceso del archivo de destino|Especifica la ruta de acceso al archivo de destino, incluido el nombre de archivo de destino|
|overwrite|¿Sobrescribir?|¿Se debe sobrescribir un archivo de destino existente (verdadero/falso)? |

Un asterisco (*) significa que la propiedad es obligatoria.

##### Detalles de salida
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

#### Extraer archivo en la carpeta
Esta operación extrae un archivo de almacenamiento en una carpeta (por ejemplo: .zip).

|Nombre de propiedad| Display Name (Nombre para mostrar)|Descripción|
| ---|---|---|
|source*|Ruta de acceso del archivo de origen|Seleccionar un archivo de almacenamiento|
|destination*|Ruta de acceso a la carpeta de destino|Selecciona el contenido que se va a extraer|
|overwrite|¿Sobrescribir?|¿Se debe sobrescribir un archivo de destino existente (verdadero/falso)?|

Un asterisco (*) significa que la propiedad es obligatoria.

##### Detalles de salida
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

Al realizar llamadas a las distintas acciones, es posible que obtenga determinadas respuestas. En la tabla siguiente se describen las respuestas y sus descripciones:

|Nombre|Descripción|
|---|---|
|200|OK|
|202|Accepted|
|400|Bad Request|
|401|No autorizado|
|403|Prohibido|
|404|No encontrado|
|500|Error interno del servidor. Error desconocido|
|default|Error en la operación.|

## Pasos siguientes

[Creación de una aplicación lógica](../app-service-logic/app-service-logic-create-a-logic-app.md). Explore los demás conectores disponibles en aplicaciones lógicas en nuestra [lista de API](apis-list.md).

<!---HONumber=AcomDC_0720_2016-->