<properties
	pageTitle="Uso del almacenamiento de blobs de Azure desde Python | Microsoft Azure"
	description="Aprenda a usar el servicio de almacenamiento Blob de Azure desde Python para cargar, enumerar, descargar y eliminar blobs."
	services="storage"
	documentationCenter="python"
	authors="emgerner-msft"
	manager="wpickett"
	editor="tysonn"/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="python"
	ms.topic="article"
	ms.date="12/11/2015"
	ms.author="emgerner"/>

# Uso del almacenamiento de blobs de Azure desde Python

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]

## Información general

Este artículo le muestra cómo realizar algunas tareas comunes con el almacenamiento de blobs. Los ejemplos están escritos en Python y usan el [paquete de almacenamiento de Azure para Python][]. Entre los escenarios descritos se incluyen cargar, enumerar, descargar y eliminar blobs.

[AZURE.INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## Crear un contenedor

> [AZURE.NOTE]Si necesita instalar Python o el [paquete de Azure para Python][], consulte la [Guía de instalación de Python](../python-how-to-install.md).

El objeto **BlobService** permite trabajar con contenedores y blobs. El código siguiente crea un objeto **BlobService**. Agregue lo siguiente cerca de la parte superior de cualquier archivo Python en el que desee obtener acceso al almacenamiento de Azure mediante programación.

	from azure.storage.blob import BlobService

El código siguiente crea un objeto **BlobService** utilizando el nombre de la cuenta de almacenamiento y la clave de la cuenta: sustituya "myaccount" y "mykey" por la cuenta real y la clave.

	blob_service = BlobService(account_name='myaccount', account_key='mykey')

[AZURE.INCLUDE [storage-container-naming-rules-include](../../includes/storage-container-naming-rules-include.md)]

En el siguiente ejemplo de código, puede usar un objeto **BlobService** para crear el contenedor si no existe:

	blob_service.create_container('mycontainer')

De manera predeterminada, el nuevo contenedor es privado, por lo que tiene que especificar su clave de acceso de almacenamiento (como hizo anteriormente) para descargar blobs de él. Si desea poner los archivos del contenedor a disposición de todo el mundo, puede crear el contenedor y pasar al nivel de acceso público usando el código siguiente.

	blob_service.create_container('mycontainer', x_ms_blob_public_access='container')

También tiene la posibilidad de modificar un contenedor después de haberlo creado mediante el siguiente código.

	blob_service.set_container_acl('mycontainer', x_ms_blob_public_access='container')

Después de este cambio, cualquier usuario de Internet podrá ver los blobs de los contenedores públicos, pero solo usted podrá modificarlos o eliminarlos.

## Cargar un blob en un contenedor

Para cargar datos en un blob, use los métodos **put\_block\_blob\_from\_path**, **put\_block\_blob\_from\_file**, **put\_block\_blob\_from\_bytes** o **put\_block\_blob\_from\_text**. Se trata de métodos de alto nivel que realizan la fragmentación necesaria cuando el tamaño de los datos supera los 64 MB.

**put\_block\_blob\_from\_path** carga el contenido de un archivo desde la ruta de acceso especificada, y **put\_block\_blob\_from\_file** carga el contenido desde un archivo o una secuencia ya abiertos.. **put\_block\_blob\_from\_bytes** carga un conjunto de bytes, y **put\_block\_blob\_from\_text** carga el valor de texto especificado usando la codificación especificada (que adopta como valor predeterminado UTF-8).

En el siguiente ejemplo se carga el contenido del archivo **sunset.png** en el blob **myblob**.

	blob_service.put_block_blob_from_path(
        'mycontainer',
        'myblob',
        'sunset.png',
        x_ms_blob_content_type='image/png'
    )

## Enumerar los blobs de un contenedor

Para enumerar los blobs de un contenedor, use el método **list\\_blobs**. Cada llamada a **list\_blobs** devolverá un segmento de resultados. Para obtener todos los resultados, compruebe el **next\_marker** de los resultados y vuelva a llamar a **list\_blobs** de nuevo según sea necesario. El código siguiente ofrece el **nombre** de todos los blobs de un contenedor a la consola.

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

## Descargar blobs

Cada segmento de resultados puede contener un número variable de blobs hasta un máximo de 5000. Si hay **next\_marker** para un segmento determinado, puede haber más blobs en el contenedor.

Para descargar datos de un blob, use **get\_blob\_to\_path**, **get\_blob\_to\_file**, **get\_blob\_to\_bytes** o **get\_blob\_to\_text**. Se trata de métodos de alto nivel que realizan la fragmentación necesaria cuando el tamaño de los datos supera los 64 MB.

En el ejemplo siguiente se muestra cómo usar **get\_blob\_to\_path** para descargar el contenido del blob **myblob** y almacenarlo en el archivo **out-sunset.png**:

	blob_service.get_blob_to_path('mycontainer', 'myblob', 'out-sunset.png')

## Eliminar un blob

Finalmente, para eliminar un blob, llame a **delete\_blob**.

	blob_service.delete_blob('mycontainer', 'myblob')

## Pasos siguientes

Ahora que está familiarizado con los aspectos básicos del almacenamiento de blobs, use estos vínculos para obtener más información acerca de tareas de almacenamiento más complejas.

- Visite el [blog del equipo de almacenamiento de Azure][] (en inglés).
- [Introducción a la utilidad de línea de comandos AzCopy](storage-use-azcopy)

Para obtener más información, consulte también el [Centro para desarrolladores de Python](/develop/python/).

[blog del equipo de almacenamiento de Azure]: http://blogs.msdn.com/b/windowsazurestorage/
[paquete de Azure para Python]: https://pypi.python.org/pypi/azure
[paquete de almacenamiento de Azure para Python]: https://pypi.python.org/pypi/azure-storage

<!---HONumber=AcomDC_0114_2016-->