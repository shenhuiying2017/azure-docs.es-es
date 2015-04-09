<properties 
	pageTitle="Uso del almacenamiento de blobs de Python | Microsoft Azure" 
	description="Aprenda a utilizar el servicio de blobs de Azure de Python para cargar, enumerar, descargar y eliminar blobs." 
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
	ms.date="03/11/2015" 
	ms.author="huvalo"/>

# Uso del almacenamiento de blobs de Python

[AZURE.INCLUDE [storage-selector-blob-include](../includes/storage-selector-blob-include.md)]

## Información general

Esta guía muestra cómo realizar algunas tareas comunes a través del
servicio de almacenamiento de blobs de Azure. Los ejemplos están escritos en Python y usan el [paquete de Azure para Python][]. Entre los escenarios descritos se incluyen la **carga****, listado**,
**descarga** y **eliminación de** blobs.

[AZURE.INCLUDE [storage-blob-concepts-include](../includes/storage-blob-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../includes/storage-create-account-include.md)]

## Procedimientos: Creación de un contenedor

> [AZURE.NOTE] Si necesita instalar Python o el [paquete de Azure para Python][], consulte la [Guía de instalación de Python](python-how-to-install.md).


El objeto **BlobService** le permite trabajar con contenedores y blobs. El código siguiente crea un objeto **BlobService**. Agregue lo siguiente cerca de la parte superior de todo archivo Python en el que desee obtener acceso al almacenamiento de Azure mediante programación:

	from azure.storage import BlobService

El código siguiente crea un objeto **BlobService** utilizando el nombre de la cuenta de almacenamiento y la clave de la cuenta:  Reemplace 'myaccount' y 'mykey' con la cuenta y la clave reales.

	blob_service = BlobService(account_name='myaccount', account_key='mykey')

Todos los blobs de almacenamiento se encuentran en un contenedor. Puede utilizar un objeto **BlobService** para crear el contenedor si no existe:

	blob_service.create_container('mycontainer')

De manera predeterminada, el nuevo contenedor es privado, por lo que debe especificar su clave de acceso de almacenamiento (como hizo anteriormente) para descargar blobs de él. Si desea poner los archivos del contenedor a disposición de todo el mundo, puede crear el contenedor y pasar al nivel de acceso público utilizando el código siguiente:

	blob_service.create_container('mycontainer', x_ms_blob_public_access='container') 

También tiene la posibilidad de modificar un contenedor después de haberlo creado mediante el siguiente código:

	blob_service.set_container_acl('mycontainer', x_ms_blob_public_access='container')

Después de este cambio, cualquier usuario de Internet podrá ver los blobs de los contenedores públicos, pero solo usted podrá modificarlos o eliminarlos.

## Procedimientos: Carga de un blob en un contenedor

Para cargar datos en un blob, use los métodos **put\_block\_blob\_from\_path**, **put\_block\_blob\_from\_file**, **put\_block\_blob\_from\_bytes** o **put\_block\_blob\_from\_text**. Se trata de métodos de alto nivel que realizan la fragmentación necesaria cuando el tamaño de los datos supera los 64 MB.

**put\_block\_blob\_from\_path** carga el contenido de un archivo desde una ruta de acceso específica, **put\_block\_blob\_from\_file** carga el contenido desde una secuencia o un archivo que ya están abiertos. **put\_block\_blob\_from\_bytes** carga una matriz de bytes, **put\_block\_blob\_from\_text** carga el valor de texto especificado con la codificación especificada (la predeterminada es UTF-8).

En el siguiente ejemplo se carga el contenido del archivo **sunset.png** en el blob **myblob**.

	blob_service.put_block_blob_from_path(
        'mycontainer',
        'myblob',
        'sunset.png',
        x_ms_blob_content_type='image/png'
    )

## Procedimientos: Enumeración de los blobs de un contenedor

Para enumerar los blobs de un contenedor, use el método **list\_blobs** con un bucle
**for** para mostrar el nombre de cada blob del contenedor. El código siguiente ofrece el **nombre** y la **url** de todos los blobs de un contenedor a la consola.

	blobs = blob_service.list_blobs('mycontainer')
	for blob in blobs:
		print(blob.name)
		print(blob.url)

## Procedimientos: Descarga de blobs

Para descargar datos de un blob, use **get\_blob\_to\_path**, **get\_blob\_to\_file**, **get\_blob\_to\_bytes** o **get\_blob\_to\_text**. Se trata de métodos de alto nivel que realizan la fragmentación necesaria cuando el tamaño de los datos supera los 64 MB.

En el ejemplo siguiente se muestra cómo utilizar **get\_blob\_to\_path** para descargar el contenido del blob **myblob** y almacenarlo en el archivo **out-sunset.png**:

	blob_service.get_blob_to_path('mycontainer', 'myblob', 'out-sunset.png')

## Procedimientos: Eliminación de un blob

Finalmente, para eliminar un blob, llame a **delete_blob**.

	blob_service.delete_blob('mycontainer', 'myblob') 

## Pasos siguientes

Ahora que está familiarizado con los aspectos básicos del almacenamiento de blobs, utilice estos vínculos para obtener más información acerca de tareas de almacenamiento más complejas.

-   Consulte la referencia de MSDN: [Almacenamiento de datos y acceso a los mismos en Azure][]
-   Visite el [blog del equipo de Almacenamiento de Azure][]

[Almacenamiento de datos y acceso a los mismos en Azure]: http://msdn.microsoft.com/library/azure/gg433040.aspx
[Blog del equipo de almacenamiento de Azure]: http://blogs.msdn.com/b/windowsazurestorage/
[Paquete de Azure para Python]: https://pypi.python.org/pypi/azure  

<!--HONumber=49-->