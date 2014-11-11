<properties linkid="develop-python-blob-service" urlDisplayName="Blob Service" pageTitle="How to use blob storage (Python) | Microsoft Azure" metaKeywords="Azure blob service Python, Azure blobs Python" description="Learn how to use the Azure Blob service to upload, list, download, and delete blobs." metaCanonical="" disqusComments="1" umbracoNaviHide="0" services="storage" documentationCenter="Python" title="How to use the Blob service from Python" authors="huvalo" videoId="" scriptId="" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="python" ms.topic="article" ms.date="01/01/1900" ms.author="huvalo"/>

# Uso del servicio de almacenamiento de blobs desde Python

Esta guía muestra cómo realizar algunas tareas comunes a través del servicio de almacenamiento de blobs
de Azure. Los ejemplos están escritos usando la API
Python. Dichas tareas comunes incluyen **cargar**, **enumerar**,
**descargar** y **eliminar** blobs. Para obtener más información acerca de los blobs,
consulte la sección [Pasos siguientes][Pasos siguientes].

## Tabla de contenido

<p><a href="#what-is">&iquest;Qu&eacute; es el almacenamiento de blobs?</a><br /> <a href="#concepts">Conceptos</a><br /> <a href="#create-account">Creaci&oacute;n de una cuenta de almacenamiento de Azure</a><br /> <a href="#create-container">Creaci&oacute;n de un contenedor</a><br /> <a href="#upload-blob">Carga de un blob en un contenedor</a><br /> <a href="#list-blob">Enumeraci&oacute;n de los blobs de un contenedor</a><br /> <a href="#download-blobs">Descarga de blobs</a><br /> <a href="#delete-blobs">Eliminaci&oacute;n de un blob</a><br /> <a href="#large-blobs">Carga y descarga de blobs de gran tama&ntilde;o</a><br /> <a href="#next-steps">Pasos siguientes</a></p>

[WACOM.INCLUDE [howto-blob-storage](../includes/howto-blob-storage.md)]

## <a name="create-account"> </a>Creación de una cuenta de almacenamiento de Azure

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

## <a name="create-container"> </a>Creación de un contenedor

**Nota:** Si necesita instalar Python o las bibliotecas de clientes, consulte la [guía de instalación de Python][guía de instalación de Python] (en inglés).

El objeto **BlobService** le permite trabajar con contenedores y blobs. El
código siguiente crea un objeto **BlobService**. Agregue lo siguiente cerca
de la parte superior de todo archivo Python en el que desee obtener acceso al almacenamiento de Azure mediante programación:

    from azure.storage import *

El código siguiente crea un objeto **BlobService** utilizando el nombre de la cuenta de almacenamiento y la clave de la cuenta: sustituya "myaccount" y "mykey" por la cuenta real y la clave.

    blob_service = BlobService(account_name='myaccount', account_key='mykey')

Todos los blobs de almacenamiento se encuentran en un contenedor. Puede utilizar un objeto **BlobService** para crear el contenedor si todavía no existe:

    blob_service.create_container('mycontainer')

De manera predeterminada, el nuevo contenedor es privado, por lo que debe especificar su clave de acceso de almacenamiento (como hizo anteriormente) para descargar blobs de él. Si desea poner los archivos del contenedor a disposición de todo el mundo, puede crear el contenedor y pasar al nivel de acceso público utilizando el código siguiente:

    blob_service.create_container('mycontainer', x_ms_blob_public_access='container') 

También tiene la posibilidad de modificar un contenedor después de haberlo creado mediante el siguiente código:

    blob_service.set_container_acl('mycontainer', x_ms_blob_public_access='container')

Después de este cambio, cualquier usuario de Internet podrá ver los blobs de los contenedores
públicos, pero solo usted podrá modificarlos o eliminarlos.

## <a name="upload-blob"> </a>Carga de un blob en un contenedor

Para cargar datos en un blob, use los métodos **put\_block\_blob\_from\_path**, **put\_block\_blob\_from\_file**, **put\_block\_blob\_from\_bytes** o **put\_block\_blob\_from\_text**. Se trata de métodos de alto nivel que realizan la fragmentación necesaria cuando el tamaño de los datos supera los 64 MB.

**put\_block\_blob\_from\_path** carga el contenido de un archivo desde la ruta de acceso especificada, **put\_block\_blob\_from\_file** carga el contenido desde un archivo o una secuencia ya abiertos. **put\_block\_blob\_from\_bytes** carga un conjunto de bytes, **put\_block\_blob\_from\_text** carga el valor de texto especificado usando la codificación especificada (que adopta como valor predeterminado UTF-8).

En el siguiente ejemplo se carga el contenido del archivo **task1.txt** en el blob **myblob**.

    blob_service.put_block_blob_from_path('mycontainer', 'myblob', 'task1.txt')

## <a name="list-blob"> </a>Enumeración de los blobs de un contenedor

Para enumerar los blobs de un contenedor, use el método **list\_blobs** con un bucle
**for** para mostrar el nombre de cada blob del contenedor. El
código siguiente ofrece el **nombre** y la **url** de cada blob de un contenedor a la
consola.

    blobs = blob_service.list_blobs('mycontainer')
    for blob in blobs:
        print(blob.name)
        print(blob.url)

## <a name="download-blobs"> </a>Descarga de blobs

Para descargar datos de un blob, use **get\_blob\_to\_path**, **get\_blob\_to\_file**, **get\_blob\_to\_bytes** o **get\_blob\_to\_text**. Se trata de métodos de alto nivel que realizan la fragmentación necesaria cuando el tamaño de los datos supera los 64 MB.

En el siguiente ejemplo se demuestra cómo usar **get\_blob\_to\_path** para descargar el contenido del blob **myblob** y almacenarlo en el archivo **out-task1.txt**:

    blob_service.get_blob_to_path('mycontainer', 'myblob', 'out-task1.txt')

## <a name="delete-blobs"> </a>Eliminación de un blob

Finalmente, para eliminar un blob, llame a **delete\_blob**.

    blob_service.delete_blob('mycontainer', 'myblob') 

## <a name="next-steps"> </a>Pasos siguientes

Ahora que está familiarizado con los aspectos básicos del almacenamiento de blobs, siga estos
vínculos para obtener más información acerca de cómo realizar tareas de almacenamiento más complejas.

-   Consulte la referencia de MSDN: [Almacenamiento de datos y acceso a los mismos en Azure][Almacenamiento de datos y acceso a los mismos en Azure]
-   Visite el [blog del equipo de almacenamiento de Azure][blog del equipo de almacenamiento de Azure] (en inglés).

  [Pasos siguientes]: #next-steps
  [howto-blob-storage]: ../includes/howto-blob-storage.md
  [create-storage-account]: ../includes/create-storage-account.md
  [guía de instalación de Python]: ../python-how-to-install/
  [Almacenamiento de datos y acceso a los mismos en Azure]: http://msdn.microsoft.com/es-es/library/windowsazure/gg433040.aspx
  [blog del equipo de almacenamiento de Azure]: http://blogs.msdn.com/b/windowsazurestorage/
