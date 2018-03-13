---
title: Uso de Azure Blob Storage (almacenamiento de objetos) en Python | Microsoft Docs
description: Almacene datos no estructurados en la nube con Almacenamiento de blobs (objetos) de Azure.
services: storage
documentationcenter: python
author: tamram
manager: timlt
editor: tysonn
ms.assetid: 0348e360-b24d-41fa-bb12-b8f18990d8bc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 2/24/2017
ms.author: tamram
ms.openlocfilehash: b94a0f95454f9243ef09ce37a62466bca4003a91
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/29/2017
---
# <a name="how-to-use-azure-blob-storage-from-python"></a>Uso del almacenamiento de blobs de Azure desde Python
[!INCLUDE [storage-selector-blob-include](../../../includes/storage-selector-blob-include.md)]

[!INCLUDE [storage-try-azure-tools-blobs](../../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>Información general
Almacenamiento de blobs de Azure es un servicio que almacena datos no estructurados en la nube como objetos o blobs. El Almacenamiento de blobs puede almacenar cualquier tipo de datos binarios o texto, como un documento, un archivo multimedia o un instalador de aplicación. El Almacenamiento de blobs a veces se conoce como "almacenamiento de objetos".

Este artículo le muestra cómo realizar algunas tareas comunes con el almacenamiento de blobs. Los ejemplos están escritos en Python y usan el [SDK de Microsoft Azure Storage para Python]. Entre los escenarios descritos se incluyen cargar, enumerar, descargar y eliminar blobs.

[!INCLUDE [storage-blob-concepts-include](../../../includes/storage-blob-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="download-and-install-azure-storage-sdk-for-python"></a>Descarga e instalación del SDK de Azure Storage para Python

El SDK de Azure Storage para Python requiere Python 2.7, 3.3, 3.4, 3.5 o 3.6, y viene en cuatro paquetes diferentes: `azure-storage-blob`, `azure-storage-file`, `azure-storage-table` y `azure-storage-queue`. En este tutorial se usará el paquete `azure-storage-blob`.
 
### <a name="install-via-pypi"></a>Instalación mediante PyPi

Para realizar la instalación mediante el índice de paquetes de Python (PyPI), escriba:

```bash
pip install azure-storage-blob
```


> [!NOTE]
> Si va a actualizar desde el SDK de Azure Storage para la versión de Python 0.36 o anterior, primero deberá desinstalarlo mediante `pip uninstall azure-storage` dado que ya no se publica el SDL de Storage para Python en un único paquete.
> 
> 

Si quiere conocer métodos de instalación alternativos, consulte el [SDK de Azure Storage para Python en Github](https://github.com/Azure/azure-storage-python/).

## <a name="create-a-container"></a>Crear un contenedor
Según el tipo de blob que desee usar, cree un objeto **BlockBlobService**, **AppendBlobService** o **PageBlobService**. El código siguiente usa un objeto **BlockBlobService** . Agregue lo siguiente cerca de la parte superior de todo archivo Python en el que desee obtener acceso al almacenamiento de blobs en bloques de Azure mediante programación.

```python
from azure.storage.blob import BlockBlobService
```

El código siguiente crea un objeto **BlockBlobService** usando la clave de la cuenta y el nombre de la cuenta de almacenamiento.  Reemplace 'myaccount' y 'mykey' por la clave y el nombre de cuenta.

```python
block_blob_service = BlockBlobService(account_name='myaccount', account_key='mykey')
```

[!INCLUDE [storage-container-naming-rules-include](../../../includes/storage-container-naming-rules-include.md)]

En el siguiente ejemplo de código, puede usar un objeto **BlockBlobService** para crear el contenedor si no existe.

```python
block_blob_service.create_container('mycontainer')
```

De manera predeterminada, el nuevo contenedor es privado, por lo que tiene que especificar su clave de acceso de almacenamiento (como hizo anteriormente) para descargar blobs de él. Si desea poner los blobs del contenedor a disposición de todo el mundo, puede crear el contenedor y pasar al nivel de acceso público usando el código siguiente.

```python
from azure.storage.blob import PublicAccess
block_blob_service.create_container('mycontainer', public_access=PublicAccess.Container)
```

También tiene la posibilidad de modificar un contenedor después de haberlo creado mediante el siguiente código.

```python
block_blob_service.set_container_acl('mycontainer', public_access=PublicAccess.Container)
```

Después de este cambio, cualquier usuario de Internet podrá ver los blobs de los contenedores públicos, pero solo usted podrá modificarlos o eliminarlos.

## <a name="upload-a-blob-into-a-container"></a>Cargar un blob en un contenedor
Para crear un blob en bloques y cargar datos, use los métodos **create\_blob\_from\_path**, **create\_blob\_from\_stream**, **create\_blob\_from\_bytes** o **create\_blob\_from\_text**. Se trata de métodos de alto nivel que realizan la fragmentación necesaria cuando el tamaño de los datos supera los 64 MB.

**create\_blob\_from\_path** carga el contenido de un archivo de la ruta de acceso especificada, y **create\_blob\_from\_stream** carga el contenido de una transmisión o archivo ya abierto. **create\_blob\_from\_bytes** carga una matriz de bytes, y **create\_blob\_from\_text** carga el valor de texto especificado con la codificación especificada (el valor predeterminado es UTF-8).

En el siguiente ejemplo se carga el contenido del archivo **sunset.png** en el blob **myblockblob**.

```python
from azure.storage.blob import ContentSettings
block_blob_service.create_blob_from_path(
    'mycontainer',
    'myblockblob',
    'sunset.png',
    content_settings=ContentSettings(content_type='image/png')
            )
```

## <a name="list-the-blobs-in-a-container"></a>Enumerar los blobs de un contenedor
Para enumerar los blobs de un contenedor, use el método **list\_blobs**. Este método devuelve un generador. El código siguiente ofrece el **nombre** de todos los blobs de un contenedor a la consola.

```python
generator = block_blob_service.list_blobs('mycontainer')
for blob in generator:
    print(blob.name)
```

## <a name="download-blobs"></a>Descargar blobs
Para descargar datos de un blob, use **get\_blob\_to\_path**, **get\_blob\_to\_stream**, **get\_blob\_to\_bytes**, o **get\_blob\_to\_text**. Se trata de métodos de alto nivel que realizan la fragmentación necesaria cuando el tamaño de los datos supera los 64 MB.

En el ejemplo siguiente se muestra cómo usar **get\_blob\_to\_path** para descargar el contenido del blob **myblockblob** y almacenarlo en el archivo **out-sunset.png**.

```python
block_blob_service.get_blob_to_path('mycontainer', 'myblockblob', 'out-sunset.png')
```

## <a name="delete-a-blob"></a>Eliminar un blob
Finalmente, para eliminar un blob, llame a **delete_blob**.

```python
block_blob_service.delete_blob('mycontainer', 'myblockblob')
```

## <a name="writing-to-an-append-blob"></a>Escritura en un blob en anexos
Un blob en anexos se optimiza para las operaciones de anexado, como el registro. Como un blob en bloques, un blob en anexos se compone también de bloques, pero en el caso del blob en anexos cuando se agrega un nuevo bloque, siempre se anexa al final del blob. No se puede actualizar o eliminar un bloque existente en un blob en anexos. Los identificadores de bloque para un blob en anexos no está expuestos como lo están en el caso de los blobs en bloques.

Cada bloque en un blob en anexos puede tener un tamaño diferente, hasta un máximo de 4 MB y el blob puede incluir un máximo de 50.000 bloques. El tamaño máximo de un blob en anexos es, por tanto, ligeramente superior a 195 GB (4 MB X 50.000 bloques).

El ejemplo siguiente crea un nuevo blob en anexos y le anexa algunos datos, para simular una operación de registro simple.

```python
from azure.storage.blob import AppendBlobService
append_blob_service = AppendBlobService(account_name='myaccount', account_key='mykey')

# The same containers can hold all types of blobs
append_blob_service.create_container('mycontainer')

# Append blobs must be created before they are appended to
append_blob_service.create_blob('mycontainer', 'myappendblob')
append_blob_service.append_blob_from_text('mycontainer', 'myappendblob', u'Hello, world!')

append_blob = append_blob_service.get_blob_to_text('mycontainer', 'myappendblob')
```

## <a name="next-steps"></a>Pasos siguientes
Ahora que está familiarizado con los aspectos básicos del Almacenamiento de blobs, siga estos vínculos para obtener más información.

* [Centro para desarrolladores de Python](https://azure.microsoft.com/develop/python/)
* [API de REST de servicios de Azure Storage](http://msdn.microsoft.com/library/azure/dd179355)
* [Blog del equipo de Azure Storage]
* [SDK de Microsoft Azure Storage para Python]

[Blog del equipo de Azure Storage]: http://blogs.msdn.com/b/windowsazurestorage/
[SDK de Microsoft Azure Storage para Python]: https://github.com/Azure/azure-storage-python
