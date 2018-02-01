---
title: Uso de Blob Storage (almacenamiento de objetos) en PHP | Microsoft Docs
description: Almacene datos no estructurados en la nube con Almacenamiento de blobs (objetos) de Azure.
documentationcenter: php
services: storage
author: tamram
manager: timlt
editor: tysonn
ms.assetid: 1af56b59-b3f0-4b46-8441-aab463ae088e
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 01/11/2018
ms.author: tamram
ms.openlocfilehash: 6bc7fd799eddca14e728f965601acd244d88870c
ms.sourcegitcommit: a0d2423f1f277516ab2a15fe26afbc3db2f66e33
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/16/2018
---
# <a name="how-to-use-blob-storage-from-php"></a>Uso del almacenamiento de blobs de PHP
[!INCLUDE [storage-selector-blob-include](../../../includes/storage-selector-blob-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>Información general
Almacenamiento de blobs de Azure es un servicio que almacena datos no estructurados en la nube como objetos o blobs. El Almacenamiento de blobs puede almacenar cualquier tipo de datos binarios o texto, como un documento, un archivo multimedia o un instalador de aplicación. El Almacenamiento de blobs a veces se conoce como "almacenamiento de objetos".

Esta guía muestra cómo realizar algunas tareas comunes a través del servicio BLOB de Azure. Los ejemplos están escritos en PHP y usan la [biblioteca de cliente de Azure Storage Blob para PHP][download]. Entre los escenarios descritos se incluyen **cargar**, **enumerar**, **descargar**, y **eliminar** blobs. Para obtener más información acerca de los blobs, consulte la sección [Pasos siguientes](#next-steps) .

[!INCLUDE [storage-blob-concepts-include](../../../includes/storage-blob-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-php-application"></a>Creación de una aplicación PHP
El único requisito a la hora de crear una aplicación PHP para obtener acceso a Azure Blob service es que el código haga referencia a clases de la [biblioteca de cliente de Azure Storage para PHP][download]. Puede utilizar cualquier herramienta de desarrollo para crear la aplicación, incluido el Bloc de notas.

En esta guía, usará funciones del servicio Blob Storage a las que se puede llamar desde una aplicación PHP localmente o bien mediante código a través de un rol web, rol de trabajo o sitio web de Azure.

## <a name="get-the-azure-client-libraries"></a>Obtención de las bibliotecas de clientes de Azure
### <a name="install-via-composer"></a>Instalación mediante el compositor
1. Cree un archivo con el nombre **composer.json** en la raíz del proyecto y agréguele el código siguiente:
   
    ```json
    {
      "require": {
        "microsoft/azure-storage-blob": "*"
      }
    }
    ```
2. Descargue **[composer.phar][composer-phar]** en la raíz del proyecto.
3. Abra un símbolo del sistema y ejecute el siguiente comando en la raíz del proyecto
   
    ```
    php composer.phar install
    ```

También puede ir a la [biblioteca de cliente de PHP de Azure Storage][download] en GitHub para clonar el código fuente.

## <a name="configure-your-application-to-access-the-blob-service"></a>Configuración de la aplicación para obtener acceso al servicio BLOB
Para usar las API del servicio BLOB de Azure, necesita:

1. Hacer referencia al archivo autocargador mediante la instrucción [require_once] y
2. Hacer referencia a todas las clases que utilice.

En el siguiente ejemplo se muestra cómo incluir el archivo autocargador y hacer referencia a la clase **BlobRestProxy**.

```php
require_once 'vendor/autoload.php';
use MicrosoftAzure\Storage\Blob\BlobRestProxy;
```

En los ejemplos que aparecen a continuación, la instrucción `require_once` se muestra siempre, pero solo se hará referencia a las clases necesarias para la ejecución del ejemplo.

## <a name="set-up-an-azure-storage-connection"></a>Configuración de una conexión de Almacenamiento de Azure
Para crear una instancia de un cliente del servicio BLOB de Azure, primero debe disponer de una cadena de conexión válida. El formato de las cadenas de conexión del servicio BLOB es:

Para obtener acceso a un servicio en directo:

```php
DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]
```

Para obtener acceso al emulador de almacenamiento:

```php
UseDevelopmentStorage=true
```

Para crear un cliente de Azure Blob service, debe usar la clase **BlobRestProxy**. Puede:

* pasarle directamente la cadena de conexión, o bien
* usar variables de entorno en la aplicación web para almacenar la cadena de conexión. Consulte el documento sobre la [configuración de aplicaciones web en Azure](../../app-service/web-sites-configure.md) para configurar cadenas de conexión.

En los ejemplos descritos aquí, la cadena de conexión se pasa directamente.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Blob\BlobRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

$blobClient = BlobRestProxy::createBlobService($connectionString);
```

## <a name="create-a-container"></a>Crear un contenedor
[!INCLUDE [storage-container-naming-rules-include](../../../includes/storage-container-naming-rules-include.md)]

Un objeto **BlobRestProxy** le permite crear un contenedor de blobs mediante el método **createContainer**. Al crear un contenedor, puede establecer opciones en él, aunque no es obligatorio. (El ejemplo siguiente muestra cómo establecer la lista de control de acceso o ACL y los metadatos del contenedor).

```php
require_once 'vendor\autoload.php';

use MicrosoftAzure\Storage\Blob\BlobRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;
use MicrosoftAzure\Storage\Blob\Models\CreateContainerOptions;
use MicrosoftAzure\Storage\Blob\Models\PublicAccessType;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create blob client.
$blobClient = BlobRestProxy::createBlobService($connectionString);


// OPTIONAL: Set public access policy and metadata.
// Create container options object.
$createContainerOptions = new CreateContainerOptions();

// Set public access policy. Possible values are
// PublicAccessType::CONTAINER_AND_BLOBS and PublicAccessType::BLOBS_ONLY.
// CONTAINER_AND_BLOBS:
// Specifies full public read access for container and blob data.
// proxys can enumerate blobs within the container via anonymous
// request, but cannot enumerate containers within the storage account.
//
// BLOBS_ONLY:
// Specifies public read access for blobs. Blob data within this
// container can be read via anonymous request, but container data is not
// available. proxys cannot enumerate blobs within the container via
// anonymous request.
// If this value is not specified in the request, container data is
// private to the account owner.
$createContainerOptions->setPublicAccess(PublicAccessType::CONTAINER_AND_BLOBS);

// Set container metadata.
$createContainerOptions->addMetaData("key1", "value1");
$createContainerOptions->addMetaData("key2", "value2");

try    {
    // Create container.
    $blobClient->createContainer("mycontainer", $createContainerOptions);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/azure/dd179439.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

Si llama a **setPublicAccess(PublicAccessType::CONTAINER\_AND\_BLOBS)**, los datos del contenedor y los blobs pasan a ser accesibles mediante solicitudes anónimas. Si llama a **setPublicAccess(PublicAccessType::BLOBS_ONLY)**, solo los datos de los blobs pasan a ser accesibles mediante solicitudes anónimas. Para obtener más información sobre las ACL del contenedor, consulte [Set container ACL (API de REST)][container-acl].

Para obtener más información sobre los códigos de error de Blob service, consulte [Códigos de error de Blob Service][error-codes].

## <a name="upload-a-blob-into-a-container"></a>Cargar un blob en un contenedor
Para cargar un archivo como blob, use el método **BlobRestProxy->createBlockBlob**. De este modo, se creará el blob si no existe, o se sobrescribirá si ya existe. En el ejemplo de código siguiente, se asume que el contenedor ya se creó y se utiliza [fopen][fopen] para abrir el archivo como secuencia.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Blob\BlobRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create blob REST proxy.
$blobClient = BlobRestProxy::createBlobService($connectionString);

$content = fopen("c:\myfile.txt", "r");
$blob_name = "myblob";

try    {
    //Upload blob
    $blobClient->createBlockBlob("mycontainer", $blob_name, $content);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/azure/dd179439.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

Observe que en el ejemplo anterior se carga un blob en forma de secuencia. Sin embargo, también es posible cargar un blob en forma de cadena utilizando, por ejemplo, la función [file\_get\_contents][file_get_contents]. Para hacer esto en el ejemplo anterior, cambie `$content = fopen("c:\myfile.txt", "r");` a `$content = file_get_contents("c:\myfile.txt");`.

## <a name="list-the-blobs-in-a-container"></a>Enumerar los blobs de un contenedor
Para enumerar los blobs de un contenedor, utilice el método **BlobRestProxy->listBlobs** aplicando un bucle **foreach** al resultado. El código siguiente permite obtener en el explorador el nombre y el URI de cada uno de los blobs de un contenedor.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Blob\BlobRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

// Create blob REST proxy.
$blobClient = BlobRestProxy::createBlobService($connectionString);

try    {
    // List blobs.
    $blob_list = $blobClient->listBlobs("mycontainer");
    $blobs = $blob_list->getBlobs();

    foreach($blobs as $blob)
    {
        echo $blob->getName().": ".$blob->getUrl()."<br />";
    }
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/azure/dd179439.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="download-a-blob"></a>Descarga de un blob
Para descargar un blob, llame al método **BlobRestProxy->getBlob** y, a continuación, al método **getContentStream** en el objeto **GetBlobResult** resultante.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Blob\BlobRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

// Create blob REST proxy.
$blobClient = BlobRestProxy::createBlobService($connectionString);


try    {
    // Get blob.
    $blob = $blobClient->getBlob("mycontainer", "myblob");
    fpassthru($blob->getContentStream());
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/azure/dd179439.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

Observe que en el ejemplo anterior se obtiene un blob en forma de recurso de secuencia (opción predeterminada). Sin embargo, es posible utilizar la función [stream\_get\_contents][stream-get-contents] para convertir la secuencia devuelta en una cadena.

## <a name="delete-a-blob"></a>Eliminar un blob
Para eliminar un blob, pase el nombre del contenedor y del blob a **BlobRestProxy->deleteBlob**.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Blob\BlobRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

// Create blob REST proxy.
$blobClient = BlobRestProxy::createBlobService($connectionString);

try    {
    // Delete blob.
    $blobClient->deleteBlob("mycontainer", "myblob");
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/azure/dd179439.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="delete-a-blob-container"></a>Eliminación de contenedores de blobs
Finalmente, para eliminar un contenedor de blobs, pase el nombre del contenedor a **BlobRestProxy->deleteContainer**.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Blob\BlobRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

// Create blob REST proxy.
$blobClient = BlobRestProxy::createBlobService($connectionString);

try    {
    // Delete container.
    $blobClient->deleteContainer("mycontainer");
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/azure/dd179439.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="next-steps"></a>pasos siguientes
Ahora que está familiarizado con los aspectos básicos del servicio BLOB de Azure, use estos vínculos para obtener más información acerca de tareas de almacenamiento más complejas.

* Consulte la [referencia de API para la biblioteca de cliente de Azure Storage para PHP](http://azure.github.io/azure-storage-php/).
* Consulte el [ejemplo de blob avanzado](https://github.com/Azure/azure-storage-php/blob/master/samples/BlobSamples.php).

[download]: https://github.com/Azure/azure-storage-php
[container-acl]: http://msdn.microsoft.com/library/azure/dd179391.aspx
[error-codes]: http://msdn.microsoft.com/library/azure/dd179439.aspx
[file_get_contents]: http://php.net/file_get_contents
[require_once]: http://php.net/require_once
[fopen]: http://www.php.net/fopen
[stream-get-contents]: http://www.php.net/stream_get_contents
[install-git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[composer-phar]: http://getcomposer.org/composer.phar
