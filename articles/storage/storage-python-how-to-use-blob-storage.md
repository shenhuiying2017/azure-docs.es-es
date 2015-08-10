<properties 
	pageTitle="Uso del almacenamiento de blobs de Python | Microsoft Azure" 
	description="Aprenda a usar el servicio de blobs de Azure de Python para cargar, enumerar, descargar y eliminar blobs." 
	services="storage" 
	documentationCenter="python" 
	authors="huguesv" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="python" 
	ms.topic="article" 
	ms.date="05/11/2015" 
	ms.author="huvalo"/>

# Uso del almacenamiento de blobs de Python

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]

## Información general

Esta guía muestra cómo realizar algunas tareas comunes a través del servicio de almacenamiento de blobs de Azure. Los ejemplos están escritos en Python y usan el [paquete de Azure para Python][]. Entre los escenarios descritos se incluyen **cargar**, **enumerar**, **descargar** y **eliminar** blobs.

[AZURE.INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## Creación de un contenedor

> [AZURE.NOTE]Si necesita instalar Python o el [paquete de Azure para Python][], consulte la [Guía de instalación de Python](../python-how-to-install.md).

El objeto **BlobService** permite trabajar con contenedores y blobs. El código siguiente crea un objeto **BlobService**. Agregue lo siguiente cerca de la parte superior de todo archivo Python en el que desee obtener acceso al almacenamiento de Azure mediante programación:

	from azure.storage import BlobService

El código siguiente crea un objeto **BlobService** utilizando el nombre de la cuenta de almacenamiento y la clave de la cuenta. sustituya "myaccount" y "mykey" por la cuenta real y la clave.

	blob_service = BlobService(account_name='myaccount', account_key='mykey')

[AZURE.INCLUDE [storage-container-naming-rules-include](../../includes/storage-container-naming-rules-include.md)]

Puede usar un objeto **BlobService** para crear el contenedor si no existe:

	blob_service.create_container('mycontainer')

De manera predeterminada, el nuevo contenedor es privado, por lo que debe especificar su clave de acceso de almacenamiento (como hizo anteriormente) para descargar blobs de él. Si desea poner los archivos del contenedor a disposición de todo el mundo, puede crear el contenedor y pasar al nivel de acceso público utilizando el código siguiente:

	blob_service.create_container('mycontainer', x_ms_blob_public_access='container') 

También tiene la posibilidad de modificar un contenedor después de haberlo creado mediante el siguiente código:

	blob_service.set_container_acl('mycontainer', x_ms_blob_public_access='container')

Después de este cambio, cualquier usuario de Internet podrá ver los blobs de los contenedores públicos, pero solo usted podrá modificarlos o eliminarlos.

## Carga de un blob en un contenedor

Para cargar datos en un blob, use los métodos **put\_block\_blob\_from\_path**, **put\_block\_blob\_from\_file**, **put\_block\_blob\_from\_bytes** o **put\_block\_blob\_from\_text**. Se trata de métodos de alto nivel que realizan la fragmentación necesaria cuando el tamaño de los datos supera los 64 MB.

**put\_block\_blob\_from\_path** carga el contenido de un archivo desde la ruta de acceso especificada, **put\_block\_blob\_from\_file** carga el contenido desde un archivo o una secuencia ya abiertos.. **put\_block\_blob\_from\_bytes** carga un conjunto de bytes, **put\_block\_blob\_from\_text** carga el valor de texto especificado usando la codificación especificada (que adopta como valor predeterminado UTF-8).

En el siguiente ejemplo se carga el contenido del archivo **sunset.png** en el blob **myblob**.

	blob_service.put_block_blob_from_path(
        'mycontainer',
        'myblob',
        'sunset.png',
        x_ms_blob_content_type='image/png'
    )

## Listado de blobs en un contenedor

Para enumerar los blobs de un contenedor, use el método **list\_blobs** con un bucle **for** para mostrar el nombre de cada blob del contenedor. El código siguiente ofrece el **nombre** de todos los blobs de un contenedor a la consola.

	blobs = blob_service.list_blobs('mycontainer')
	for blob in blobs:
		print(blob.name)

**list\_blobs** sólo devuelve un máximo de 5000 blobs. Si el contenedor contiene más de 5000 blobs, utilice el código siguiente.

	blobs = []
	marker = None
	while True:
		batch = blob_service.list_blobs('mycontainer', marker=marker)
		blobs.extend(batch)
		if not batch.next_marker:
			break
		marker = batch.next_marker
	for blob in blobs:
		print(blob.name)

## Descarga de blobs

Para descargar datos de un blob, use **get\_blob\_to\_path**, **get\_blob\_to\_file**, **get\_blob\_to\_bytes** o **get\_blob\_to\_text**. Se trata de métodos de alto nivel que realizan la fragmentación necesaria cuando el tamaño de los datos supera los 64 MB.

En el ejemplo siguiente se muestra cómo utilizar **get\_blob\_to\_path** para descargar el contenido del blob **myblob** y almacenarlo en el archivo **out-sunset.png**:

	blob_service.get_blob_to_path('mycontainer', 'myblob', 'out-sunset.png')

## Eliminación de un blob

Finalmente, para eliminar un blob, llame a **delete\_blob**.

	blob_service.delete_blob('mycontainer', 'myblob') 

## Pasos siguientes

Ahora que está familiarizado con los aspectos básicos del almacenamiento de blobs, utilice estos vínculos para obtener más información acerca de tareas de almacenamiento más complejas.

-   Vea la referencia de MSDN: [Almacenamiento de datos y acceso a los mismos en Azure][]
-   Visite el [blog del equipo de almacenamiento de Azure][] (en inglés).

[Almacenamiento de datos y acceso a los mismos en Azure]: http://msdn.microsoft.com/library/azure/gg433040.aspx
[blog del equipo de almacenamiento de Azure]: http://blogs.msdn.com/b/windowsazurestorage/
[paquete de Azure para Python]: https://pypi.python.org/pypi/azure
 

<!---HONumber=July15_HO5-->