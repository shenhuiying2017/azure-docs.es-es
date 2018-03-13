---
title: Uso de Blob Storage (almacenamiento de objetos) en Ruby | Microsoft Docs
description: Almacene datos no estructurados en la nube con Almacenamiento de blobs (objetos) de Azure.
services: storage
documentationcenter: ruby
author: tamram
manager: timlt
editor: tysonn
ms.assetid: e2fe4c45-27b0-4d15-b3fb-e7eb574db717
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: ruby
ms.topic: article
ms.date: 01/18/2018
ms.author: tamram
ms.openlocfilehash: c4c6d47511acdae7afaf4a535c24c6fcc7e389b1
ms.sourcegitcommit: 1fbaa2ccda2fb826c74755d42a31835d9d30e05f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/22/2018
---
# <a name="how-to-use-blob-storage-from-ruby"></a>Uso del almacenamiento de blobs de Ruby
[!INCLUDE [storage-selector-blob-include](../../../includes/storage-selector-blob-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>Información general
Almacenamiento de blobs de Azure es un servicio que almacena datos no estructurados en la nube como objetos o blobs. El Almacenamiento de blobs puede almacenar cualquier tipo de datos binarios o texto, como un documento, un archivo multimedia o un instalador de aplicación. El Almacenamiento de blobs a veces se conoce como "almacenamiento de objetos".

En esta guía se muestra cómo realizar algunas tareas comunes con Almacenamiento de blobs. Los ejemplos están escritos usando la API Ruby. En los escenarios, se explica la **carga, enumeración, descarga** y **eliminación** de blobs.

[!INCLUDE [storage-blob-concepts-include](../../../includes/storage-blob-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-ruby-application"></a>Creación de una aplicación de Ruby
Cree una aplicación de Ruby. Para obtener más instrucciones, consulte [Creación de una aplicación de Ruby en App Service en Linux](https://docs.microsoft.com/azure/app-service/containers/quickstart-ruby).


## <a name="configure-your-application-to-access-storage"></a>Configuración de la aplicación para obtener acceso al almacenamiento
Para usar Azure Storage, tendrá que descargar y usar el paquete Ruby azure, que incluye un conjunto de útiles bibliotecas que se comunican con los servicios REST de almacenamiento.

### <a name="use-rubygems-to-obtain-the-package"></a>Uso de RubyGems para obtener el paquete
1. Use una interfaz de línea de comandos como **PowerShell** (Windows), **Terminal** (Mac) o **Bash** (Unix).
2. Escriba "gem install azure-storage-blob" en la ventana de comandos para instalar la gema y las dependencias.

### <a name="import-the-package"></a>Importación del paquete
Con el editor de texto que prefiera, agregue lo siguiente al principio del archivo de Ruby en el que pretenda utilizar el almacenamiento:

```ruby
require "azure/storage/blob"
```

## <a name="set-up-an-azure-storage-connection"></a>Configuración de una conexión de Azure Storage
El módulo azure leerá las variables de entorno **AZURE\_STORAGE\_ACCOUNT** y **AZURE\_STORAGE\_ACCESS_KEY** para obtener información necesaria para conectarse a su cuenta de Azure Storage. Si no se establecen estas variables de entorno, debe especificar la información de la cuenta mediante **Azure::Blob::BlobService::create** con el siguiente código:

```ruby
blob_client = Azure::Storage::Blob::BlobService.create(
    storage_account_name: account_name,
    storage_access_key: account_key
    )
```

Para obtener estos valores desde una cuenta de almacenamiento de Azure Resource Manager o clásica en el Portal de Azure:

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
2. Vaya a la cuenta de almacenamiento que desea utilizar.
3. En la hoja Configuración que se encuentra a la derecha, haga clic en **Claves de acceso**.
4. En la hoja Claves de acceso que aparece, verá la clave de acceso 1 y 2. Puede usar cualquiera de estas.
5. Haga clic en el icono de copia para copiar la clave en el Portapapeles.

## <a name="create-a-container"></a>Crear un contenedor
[!INCLUDE [storage-container-naming-rules-include](../../../includes/storage-container-naming-rules-include.md)]

El objeto **Azure::Storage::Blob::BlobService** permite trabajar con contenedores y blobs. Para crear un contenedor, use el método **create\_container()**.

En el siguiente ejemplo de código se crea un contenedor o se imprime el error, si hay alguno.

```ruby
azure_blob_service = Azure::Storage::Blob::BlobService.create_from_env
begin
    container = azure_blob_service.create_container("test-container")
rescue
    puts $!
end
```

Si desea hacer públicos los archivos del contenedor, debe configurar los permisos del contenedor.

Puede modificar solo la llamada <strong>create\_\_container()</strong> para pasar la opción **:public\__access\__level**:

```ruby
container = azure_blob_service.create_container("test-container",
    :public_access_level => "<public access level>")
```

Los valores válidos para la opción **:public\_access\_level** son:

* **blob:** habilita el acceso de lectura público a los blobs. Los datos de blob dentro de este contenedor pueden leerse a través de una solicitud anónima, pero los datos del contenedor no están disponibles. Los clientes no pueden enumerar los blobs incluidos en el contenedor mediante una solicitud anónima.
* **container:** habilita el acceso de lectura público completo a los datos del contenedor y los blobs. Los clientes pueden enumerar los blobs del contenedor a través de una solicitud anónima, pero no pueden enumerar los contenedores que están en la cuenta de almacenamiento.

Si lo desea, también puede modificar el nivel de acceso público de un contenedor usando el método **set\_container\_acl()** para especificarlo.

El ejemplo de código siguiente cambia el nivel de acceso público a **container**:

```ruby
azure_blob_service.set_container_acl('test-container', "container")
```

## <a name="upload-a-blob-into-a-container"></a>Cargar un blob en un contenedor
Para cargar contenido en un blob, use el método **create\_block\_blob()** para crear el blob y utilice un archivo o una cadena como contenido del blob.

El siguiente código carga el archivo **test.png** como un blob nuevo llamado "image-blob" en el contenedor.

```ruby
content = File.open("test.png", "rb") { |file| file.read }
blob = azure_blob_service.create_block_blob(container.name,
    "image-blob", content)
puts blob.name
```

## <a name="list-the-blobs-in-a-container"></a>Enumerar los blobs de un contenedor
Para enumerar los contenedores, use el método **list_containers()**.
Para enumerar los blobs de un contenedor, utilice el método **list\_blobs()**. Para enumerar todos los blobs de un contenedor, debe seguir un token de continuación devuelto por el servicio y continuar ejecutando list_blobs con este token. Consulte la [API de REST para List Blobs](https://docs.microsoft.com/rest/api/storageservices/list-blobs) para obtener más información.

El código siguiente da como resultado todos los blobs de un contenedor.

```ruby
nextMarker = nil
loop do
    blobs = azure_blob_service.list_blobs(container_name, { marker: nextMarker })
    blobs.each do |blob|
        puts "\tBlob name #{blob.name}"
    end
    nextMarker = blobs.continuation_token
    break unless nextMarker && !nextMarker.empty?
end
```

## <a name="download-blobs"></a>Descargar blobs
Si desea descargar blobs, use el método **get\_blob()** para recuperar el contenido.

En el ejemplo de código siguiente se muestra cómo se utiliza **get\_blob()** para descargar el contenido de "image-blob" y escribirlo en un archivo local.

```ruby
blob, content = azure_blob_service.get_blob(container.name,"image-blob")
File.open("download.png","wb") {|f| f.write(content)}
```

## <a name="delete-a-blob"></a>un blob
Finalmente, para eliminar un blob, use el método **delete\_blob**. En el siguiente ejemplo de código se muestra cómo eliminar un blob.

```ruby
azure_blob_service.delete_blob(container.name, "image-blob")
```

## <a name="next-steps"></a>pasos siguientes
Para obtener información acerca de tareas de almacenamiento más complejas, siga estos vínculos:

* [Blog del equipo de Azure Storage](http://blogs.msdn.com/b/windowsazurestorage/)
* Repositorio del [SDK de Azure Storage para Ruby](https://github.com/azure/azure-storage-ruby) en GitHub
* [Transferencia de datos con la utilidad en línea de comandos AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

