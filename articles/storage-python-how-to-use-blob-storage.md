<properties linkid="develop-python-blob-service" urlDisplayName="Blob Service" pageTitle="How to use blob storage (Python) | Microsoft Azure" metaKeywords="Azure blob service Python, Azure blobs Python" description="Learn how to use the Azure Blob service to upload, list, download, and delete blobs." metaCanonical="" disqusComments="1" umbracoNaviHide="0" services="storage" documentationCenter="Python" title="How to use the Blob service from Python" authors="" videoId="" scriptId="" />

Uso del servicio de almacenamiento de blobs desde Python
========================================================

Esta guía muestra cómo realizar algunas tareas comunes a través del servicio de almacenamiento de blobs de Azure. Los ejemplos están escritos usando la API Python. Dichas tareas comunes incluyen **cargar**, **enumerar**, **descargar** y **eliminar** blobs. Para obtener más información acerca de los blobs, consulte la sección [Pasos siguientes](#next-steps).

Tabla de contenido
------------------

[¿Qué es el almacenamiento de blobs?](#what-is)
 [Conceptos](#concepts)
 [Creación de una cuenta de almacenamiento de Azure](#create-account)
 [Creación de un contenedor](#create-container)
 [Carga de un blob en un contenedor](#upload-blob)
 [Enumeración de los blobs de un contenedor](#list-blob)
 [Descarga de blobs](#download-blobs)
 [Eliminación de un blob](#delete-blobs)
 [Carga y descarga de blobs de gran tamaño](#large-blobs)
 [Pasos siguientes](#next-steps)

[WACOM.INCLUDE [howto-blob-storage](../includes/howto-blob-storage.md)]

Creación de una cuenta de almacenamiento de Azure
-------------------------------------------------

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

Creación de un contenedor
-------------------------

**Nota:** Si necesita instalar Python o las bibliotecas de clientes, consulte la [guía de instalación de Python](../python-how-to-install/) (en inglés).

El objeto **BlobService** le permite trabajar con contenedores y blobs. El código siguiente crea un objeto **BlobService**. Agregue lo siguiente cerca de la parte superior de todo archivo Python en el que desee obtener acceso al almacenamiento de Azure mediante programación:

    from azure.storage import *

El código siguiente crea un objeto **BlobService** utilizando el nombre de la cuenta de almacenamiento y la clave de la cuenta: sustituya "myaccount" y "mykey" por la cuenta real y la clave.

    blob_service = BlobService(account_name='myaccount', account_key='mykey')

Todos los blobs de almacenamiento se encuentran en un contenedor. Puede utilizar un objeto **BlobService** para crear el contenedor si todavía no existe:

    blob_service.create_container('mycontainer')

De manera predeterminada, el nuevo contenedor es privado, por lo que debe especificar su clave de acceso de almacenamiento (como hizo anteriormente) para descargar blobs de él. Si desea poner los archivos del contenedor a disposición de todo el mundo, puede crear el contenedor y pasar al nivel de acceso público utilizando el código siguiente:

    blob_service.create_container('mycontainer', x_ms_blob_public_access='container') 

También tiene la posibilidad de modificar un contenedor después de haberlo creado mediante el siguiente código:

    blob_service.set_container_acl('mycontainer', x_ms_blob_public_access='container')

Después de este cambio, cualquier usuario de Internet podrá ver los blobs de los contenedores públicos, pero solo usted podrá modificarlos o eliminarlos.

Carga de un blob en un contenedor
---------------------------------

Para cargar un archivo a un blob, utilice el método **put\_blob** para crear el blob mediante una secuencia de archivos como contenido del blob. En primer lugar, cree un archivo llamado **task1.txt** (cualquier contenido es válido) y almacénelo en el mismo directorio que su archivo Python.

    myblob = open(r'task1.txt', 'r').read()
    blob_service.put_blob('mycontainer', 'myblob', myblob, x_ms_blob_type='BlockBlob')

Enumeración de los blobs de un contenedor
-----------------------------------------

Para enumerar los blobs de un contenedor, use el método **list\_blobs** con un bucle **for** para mostrar el nombre de cada blob del contenedor. El código siguiente ofrece el **nombre** y la **url** de todos los blobs de un contenedor a la consola.

    blobs = blob_service.list_blobs('mycontainer')
    Para blobs en blobs:
        print(blob.name)
        print(blob.url)

Descarga de blobs
-----------------

En el siguiente ejemplo, se usa el método **get\_blob** para transferir el contenido del blob a un objeto de secuencia que, a continuación, puede guardar en un archivo local.

    blob = blob_service.get_blob('mycontainer', 'myblob')
    with open(r'out-task1.txt', 'w') as f:
        f.write(blob)

Eliminación de un blob
----------------------

Finalmente, para eliminar un blob, llame a **delete\_blob**.

    blob_service.delete_blob('mycontainer', 'myblob') 

Carga y descarga de blobs de gran tamaño
----------------------------------------

El tamaño máximo para un blob en bloques es de 200 GB. Para blobs de menos de 64 MB, el blob se puede cargar o descargar con una llamada sencilla a **put\_blob** o **get\_blob**, tal y como se ha mostrado anteriormente. Para blobs de más de 64 MB, es necesario cargar o descargar el blob en bloques de 4 MB como máximo.

El siguiente código muestra ejemplos de funciones para cargar o descargar blobs en bloques de cualquier tamaño.

    import base64

    chunk_size = 4 * 1024 * 1024

    def upload(blob_service, container_name, blob_name, file_path):
        blob_service.create_container(container_name, None, None, False)
        blob_service.put_blob(container_name, blob_name, '', 'BlockBlob')

        block_ids = []
        index = 0
        with open(file_path, 'rb') as f:
            while True:
                data = f.read(chunk_size)
                if data:
                    length = len(data)
                    block_id = base64.b64encode(str(index))
                    blob_service.put_block(container_name, blob_name, data, block_id)
                    block_ids.append(block_id)
                    index += 1
                else:
                    break

        blob_service.put_block_list(container_name, blob_name, block_ids)

    def download(blob_service, container_name, blob_name, file_path):
        props = blob_service.get_blob_properties(container_name, blob_name)
        blob_size = int(props['content-length'])

        index = 0
        with open(file_path, 'wb') as f:
            while index < blob_size:
                chunk_range = 'bytes={}-{}'.format(index, index + chunk_size - 1)
                data = blob_service.get_blob(container_name, blob_name, x_ms_range=chunk_range)
                length = len(data)
                index += length
                if length > 0:
                    f.write(data)
                    if length < chunk_size:
                        break
                else:
                    break

Si necesita utilizar blobs de más de 200 GB, puede utilizar un blob en páginas en lugar de un blob en bloques. El tamaño máximo de un blob en páginas es de 1 TB, con páginas que se alinean en límites de página de 512 bytes. Utilice **put\_blob** para crear un blob en páginas, **put\_page** para escribir en él y **get\_blob** para leer en él.

Pasos siguientes
----------------

Ahora que está familiarizado con los aspectos básicos del almacenamiento de blobs, siga estos vínculos para obtener más información acerca de cómo realizar tareas de almacenamiento más complejas.

-   Consulte la referencia de MSDN: [Almacenamiento de datos y acceso a los mismos en Azure](http://msdn.microsoft.com/es-es/library/windowsazure/gg433040.aspx)
-   Visite el [blog del equipo de almacenamiento de Azure](http://blogs.msdn.com/b/windowsazurestorage/) (en inglés).

