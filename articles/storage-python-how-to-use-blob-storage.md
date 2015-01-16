<properties urlDisplayName="Blob Service" pageTitle="Uso del almacenamiento de blobs (Python) | Microsoft Azure" metaKeywords="Azure blob service Python, Azure blobs Python" description="Aprenda a utilizar el servicio BLOB de Azure para cargar, incluir en un listado, descargar y eliminar blobs." metaCanonical="" disqusComments="1" umbracoNaviHide="0" services="storage" documentationCenter="Python" title="How to use the Blob service from Python" authors="huvalo" videoId="" scriptId="" manager="wpickett" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="python" ms.topic="article" ms.date="09/19/2014" ms.author="robmcm" />

# Uso del servicio de almacenamiento de blobs desde Python
Esta guía muestra cómo realizar algunas tareas comunes a través del servicio de almacenamiento de blobs de Azure. Los ejemplos están escritos usando la API Python. Los escenarios cubiertos incluyen **cargar**, **escuchar**, **descargar** y **eliminar** blobs. Para obtener más información acerca de los blobs, consulte la sección [Pasos siguientes][].

## Tabla de contenido

[Qué es el almacenamiento de blobs][]   
 [Conceptos][]   
 [Creación de una cuenta de almacenamiento de Azure][]   
 [Actualización de un contenedor][]   
 [Actualización de un blob en un contenedor][]   
 [Actualización de los blobs de un contenedor][]   
 [Actualización de blobs][]   
 [Actualización de un blob][]   
 [Carga y descarga de blobs de gran tamaño][]   
 [Pasos siguientes][]

[WACOM.INCLUDE [howto-blob-storage](../includes/howto-blob-storage.md)]

## <a name="create-account"> </a>Creación de una cuenta de almacenamiento de Azure

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

## <a name="create-container"> </a>Reproducción de un contenedor

**Nota:** Si necesita instalar Python o las bibliotecas de clientes, consulte la  [guía de instalación de Python](../python-how-to-install/).


El objeto **BlobService** le permite trabajar con contenedores y blobs. El código siguiente crea un objeto **BlobService**. Agregue lo siguiente cerca de la parte superior de todo archivo Python en el que desee obtener acceso al almacenamiento de Azure mediante programación:

	from azure.storage import BlobService

El código siguiente crea un objeto **BlobService** utilizando el nombre de la cuenta de almacenamiento y la clave de la cuenta:  sustituya "myaccount" y "mykey" por la cuenta real y la clave.

	blob_service = BlobService(account_name='myaccount', account_key='mykey')

Todos los blobs de almacenamiento se encuentran en un contenedor. Puede utilizar un objeto **BlobService** para crear el contenedor si todavía no existe:

	blob_service.create_container('mycontainer')

De manera predeterminada, el nuevo contenedor es privado, por lo que debe especificar su clave de acceso de almacenamiento (como hizo anteriormente) para descargar blobs de él. Si desea poner los archivos del contenedor a disposición de todo el mundo, puede crear el contenedor y pasar al nivel de acceso público utilizando el código siguiente:

	blob_service.create_container('mycontainer', x_ms_blob_public_access='container') 

También tiene la posibilidad de modificar un contenedor después de haberlo creado mediante el siguiente código:

	blob_service.set_container_acl('mycontainer', x_ms_blob_public_access='container')

Después de este cambio, cualquier usuario de Internet podrá ver los blobs de los contenedores públicos, pero solo usted podrá modificarlos o eliminarlos.

## <a name="upload-blob"> </a>Reproducción de un blob en un contenedor

Para cargar datos en un blob, use los métodos **put\_block\_blob\_from\_path**, **put\_block\_blob\_from\_file**, **put\_block\_blob\_from\_bytes** o **put\_block\_blob\_from\_text**. Se trata de métodos de alto nivel que realizan la fragmentación necesaria cuando el tamaño de los datos supera los 64 MB.

**put\_block\_blob\_from\_path** carga el contenido de un archivo desde una ruta de acceso específica, **put\_block\_blob\_from\_file** carga el contenido desde una secuencia o un archivo que ya están abiertos. **put\_block\_blob\_from\_bytes** carga una matriz de bytes, **put\_block\_blob\_from\_text** carga el valor de texto especificado con la codificación especificada (la predeterminada es UTF-8).

En el siguiente ejemplo se carga el contenido del archivo **task1.txt** en el blob **myblob**.

	blob_service.put_block_blob_from_path('mycontainer', 'myblob', 'task1.txt')

## <a name="list-blob"> </a>Reproducción de los blobs de un contenedor

Para enumerar los blobs de un contenedor, use el método **list\_blobs** con un bucle
**for** para visualizar el nombre de cada blob del contenedor. Muestra
El código siguiente ofrece el  **nombre** y la **url** de todos los blobs de un contenedor a la consola.

	blobs = blob_service.list_blobs('mycontainer')
	for blob in blobs:
		print(blob.name)
		print(blob.url)

## <a name="download-blobs"> </a>Reproducción de blobs

Para descargar datos de un blob, use **get\_blob\_to\_path**, **get\_blob\_to\_file**, **get\_blob\_to\_bytes** o **get\_blob\_to\_text**. Se trata de métodos de alto nivel que realizan la fragmentación necesaria cuando el tamaño de los datos supera los 64 MB.

En el ejemplo siguiente se demuestra cómo usar **get\_blob\_to\_path** para descargar el contenido del blob **myblob** y almacenarlo en el archivo **out-task1.txt**:

	blob_service.get_blob_to_path('mycontainer', 'myblob', 'out-task1.txt')

## <a name="delete-blobs"> </a>Reproducción de un blob

Finalmente, para eliminar un blob, llame a **delete_blob**.

	blob_service.delete_blob('mycontainer', 'myblob') 

## <a name="next-steps"> </a>Pasos siguientes

Ahora que ha aprendido los conceptos básicos de Almacenamiento de blobs, siga estos vínculos
para obtener información sobre cómo hacer tareas de almacenamiento más complejas.

-   Consulte la referencia de MSDN: [Almacenamiento de datos y acceso a los mismos en Azure][]
-   Visite el [blog del equipo de almacenamiento de Azure][].

  [Pasos siguientes]: #next-steps
  [Qué es el almacenamiento de blobs]: #what-is
  [Conceptos]: #concepts
  [Creación de una cuenta de almacenamiento de Azure]: #create-account
  [Actualización de un contenedor]: #create-container
  [Actualización de un blob en un contenedor]: #upload-blob
  [Actualización de los blobs de un contenedor]: #list-blob
  [Actualización de blobs]: #download-blobs
  [Actualización de un blob]: #delete-blobs
  [Actualización y descarga de blobs de gran tamaño]: #large-blobs
  [Almacenamiento de datos y acceso a los mismos en Azure]: http://msdn.microsoft.com/en-us/library/windowsazure/gg433040.aspx
  [Blog del equipo de almacenamiento de Azure]: http://blogs.msdn.com/b/windowsazurestorage/

<!--HONumber=35.1-->
