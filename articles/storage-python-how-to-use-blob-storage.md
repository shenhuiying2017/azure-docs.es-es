<properties urlDisplayName="Blob Service" pageTitle="Uso del almacenamiento de blobs (Python) | Microsoft Azure" metaKeywords="Azure blob service Python, Azure blobs Python" description="Learn how to use the Azure Blob service to upload, list, download, and delete blobs." metaCanonical="" disqusComments="1" umbracoNaviHide="0" services="storage" documentationCenter="Python" title="How to use the Blob service from Python" authors="huvalo" videoId="" scriptId="" manager="wpickett" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="python" ms.topic="article" ms.date="09/19/2014" ms.author="huvalo" />

# Uso del servicio de almacenamiento de blobs desde Python
Esta guía muestra cómo realizar algunas tareas comunes a través del
servicio de almacenamiento BLOB de Azure. Los ejemplos están escritos usando la
API Python. Entre los escenarios descritos se incluyen la **carga**, **listado**,
**descarga** y **eliminación de** blobs. Para obtener más información sobre blobs,
consulte la sección [Pasos siguientes][Pasos siguientes].

## Tabla de contenido

[¿Qué es el almacenamiento de blobs?][¿Qué es el almacenamiento de blobs?]   
 [Conceptos][Conceptos]   
 [Creación de una cuenta de almacenamiento de Azure][Creación de una cuenta de almacenamiento de Azure]   
 [Creación de un contenedor][Creación de un contenedor]   
 [Carga de un blob en un contenedor][Carga de un blob en un contenedor]   
 [Listado de blobs en un contenedor][Listado de blobs en un contenedor]   
 [Descarga de blobs][Descarga de blobs]   
 [Eliminación de un blob][Eliminación de un blob]   
 [Carga y descarga de blobs de gran tamaño][Carga y descarga de blobs de gran tamaño]   
 [Pasos siguientes][Pasos siguientes]

[WACOM.INCLUDE [howto-blob-storage](../includes/howto-blob-storage.md)]

## <a name="create-account"> </a>Creación de una cuenta de almacenamiento de Azure

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

## <a name="create-container"> </a>Creación de un contenedor

**Nota:** Si necesita instalar Python o las bibliotecas de clientes, consulte la [guía de instalación de Python](../python-how-to-install/) (en inglés).


El objetivo **BlobService** le permite trabajar con contenedores y blobs. El
código siguiente crea un objeto **BlobService**. Agregue lo siguiente cerca
de la parte superior de todo archivo Python en el que desee obtener acceso al almacenamiento de Azure mediante programación:

	from azure.storage import BlobService

El código siguiente crea un objeto **BlobService** utilizando el nombre de la cuenta de almacenamiento y la clave de la cuenta.  sustituya "myaccount" y "mykey" por la cuenta real y la clave.

	blob_service = BlobService(account_name='myaccount', account_key='mykey')

Todos los blobs de almacenamiento se encuentran en un contenedor. Puede utilizar un objeto **BlobService** para crear el contenedor si todavía no existe:

	blob_service.create_container('mycontainer')

De manera predeterminada, el nuevo contenedor es privado, por lo que debe especificar su clave de acceso de almacenamiento (como hizo anteriormente) para descargar blobs de él. Si desea poner los archivos del contenedor a disposición de todo el mundo, puede crear el contenedor y pasar al nivel de acceso público utilizando el código siguiente:

	blob_service.create_container('mycontainer', x_ms_blob_public_access='container') 

También tiene la posibilidad de modificar un contenedor después de haberlo creado mediante el siguiente código:

	blob_service.set_container_acl('mycontainer', x_ms_blob_public_access='container')

Después de este cambio, cualquiera con Internet puede ver blobs en un
contenedor público, pero solo usted puede modificarlos o eliminarlos.

## <a name="upload-blob"> </a>Carga de un blob en un contenedor

Para cargar datos en un blob, use los métodos **put\_block\_blob\_from\_path**, **put\_block\_blob\_from\_file**, **put\_block\_blob\_from\_bytes** o **put\_block\_blob\_from\_text**. Se trata de métodos de alto nivel que realizan la fragmentación necesaria cuando el tamaño de los datos supera los 64 MB.

**put\_block\_blob\_from\_path** carga el contenido de un archivo desde la ruta de acceso especificada, **put\_block\_blob\_from\_file** carga el contenido desde un archivo o una secuencia ya abiertos. **put\_block\_blob\_from\_bytes** carga un conjunto de bytes, **put\_block\_blob\_from\_text** carga el valor de texto especificado usando la codificación especificada (que adopta como valor predeterminado UTF-8).

En el siguiente ejemplo se carga el contenido del archivo **task1.txt** en el blob **myblob**.

	blob_service.put_block_blob_from_path('mycontainer', 'myblob', 'task1.txt')

## <a name="list-blob"> </a>Direccionamiento del de los blobs de un contenedor

Para enumerar los blobs de un contenedor, use el método **list\_blobs** con un
bucle **for** para mostrar el nombre de cada blob del contenedor. El
código siguiente ofrece el **nombre** y la **url** de cada blob de un contenedor a la
consola.

	blobs = blob_service.list_blobs('mycontainer')
	Para blobs en blobs:
		print(blob.name)
		print(blob.url)

## <a name="download-blobs"> </a>Direccionamiento del blobs

Para descargar datos de un blob, use **get\_blob\_to\_path**, **get\_blob\_to\_file**, **get\_blob\_to\_bytes** o **get\_blob\_to\_text**. Se trata de métodos de alto nivel que realizan la fragmentación necesaria cuando el tamaño de los datos supera los 64 MB.

En el siguiente ejemplo se demuestra cómo usar **get\_blob\_to\_path** para descargar el contenido del blob **myblob** y almacenarlo en el archivo **out-task1.txt**:

	blob_service.get_blob_to_path('mycontainer', 'myblob', 'out-task1.txt')

## <a name="delete-blobs"> </a>Eliminación de un blob

Finalmente, para eliminar un blob, llame a **delete_blob**.

	blob_service.delete_blob('mycontainer', 'myblob') 

## <a name="next-steps"> </a>Pasos siguientes

Ahora que está familiarizado con los aspectos básicos del almacenamiento de blobs, siga estos vínculos
para obtener más información acerca de cómo realizar tareas de almacenamiento más complejas.

-   Consulte la referencia de MSDN: [Almacenamiento de datos y acceso a los mismos en Azure][Almacenamiento de datos y acceso a los mismos en Azure]
-   Visite el [blog del equipo de almacenamiento de Azure][blog del equipo de almacenamiento de Azure]

  [Pasos siguientes]: #next-steps
  [¿Qué es el almacenamiento de blobs?]: #what-is
  [Conceptos]: #concepts
  [Creación de una cuenta de almacenamiento de Azure]: #create-account
  [Creación de un contenedor]: #create-container
  [Carga de un blob en un contenedor]: #upload-blob
  [Listado de blobs en un contenedor]: #list-blob
  [Descarga de blobs]: #download-blobs
  [Eliminación de un blob]: #delete-blobs
  [Carga y descarga de blobs de gran tamaño]: #large-blobs
  [Almacenamiento de datos y acceso a los mismos en Azure]: http://msdn.microsoft.com/es-es/library/windowsazure/gg433040.aspx
