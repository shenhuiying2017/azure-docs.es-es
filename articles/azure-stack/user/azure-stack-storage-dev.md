---
title: Empezar a trabajar con herramientas de desarrollo de Azure Stack Storage
description: "Guía para empezar a trabajar con herramientas de desarrollo de Azure Stack Storage"
services: azure-stack
author: xiaofmao
ms.author: xiaofmao
ms.date: 9/25/2017
ms.topic: get-started-article
ms.service: azure-stack
ms.openlocfilehash: 5b2898c64c0f1b5d804e63fa4e4e1218fa7a672c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-azure-stack-storage-development-tools"></a>Empezar a trabajar con herramientas de desarrollo de Azure Stack Storage

*Se aplica a: Sistemas integrados de Azure Stack y Azure Stack Development Kit*


Microsoft Azure Stack proporciona un conjunto de servicios de almacenamiento que incluye Azure Blob Storage, Table Storage y Queue Storage.

Este artículo proporciona una guía rápida sobre cómo empezar a usar las herramientas de desarrollo de Azure Stack Storage. Puede encontrar información más detallada y código de ejemplo en los tutoriales correspondientes de Azure Storage.

Hay algunas diferencias conocidas entre Azure Storage y Azure Stack Storage, incluidos algunos requisitos específicos para cada plataforma. Por ejemplo, hay requisitos de bibliotecas de cliente y de sufijos de puntos de conexión que son específicos de Azure Stack. Para más información, consulte [Azure Stack Storage: Diferencias y consideraciones](azure-stack-acs-differences.md).

## <a name="azure-client-libraries"></a>Bibliotecas de clientes de Azure
La versión de API de REST compatible con Azure Stack Storage es 2015-04-05. No tiene una paridad completa con la versión más reciente de la API de REST de Azure Storage. Por tanto, en lo que respecta a las bibliotecas de cliente de almacenamiento debe conocer cuál es la versión compatible con la API de REST 2015-04-05.


|Biblioteca de cliente|Versión compatible de Azure Stack|Vínculo|Especificación de punto de conexión|
|---------|---------|---------|---------|
|.NET     |6.2.0|Paquete NuGet:<br>[https://www.nuget.org/packages/WindowsAzure.Storage/6.2.0](https://www.nuget.org/packages/WindowsAzure.Storage/6.2.0)<br><br>Versión de GitHub:<br>[https://github.com/Azure/azure-storage-net/releases/tag/v6.2.1](https://github.com/Azure/azure-storage-net/releases/tag/v6.2.1)|archivo app.config|
|Java|4.1.0|Paquete Maven:<br>[http://mvnrepository.com/artifact/com.microsoft.azure/azure-storage/4.1.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-storage/4.1.0)<br><br>Versión de GitHub:<br> [https://github.com/Azure/azure-storage-java/releases/tag/v4.1.0](https://github.com/Azure/azure-storage-java/releases/tag/v4.1.0)|Configuración de la cadena de conexión|
|Node.js     |1.1.0|Vínculo NPM:<br>[https://www.npmjs.com/package/azure-storage](https://www.npmjs.com/package/azure-storage)<br>(ejecute: `npm install azure-storage@1.1.0)`<br><br>Versión de GitHub:<br>[https://github.com/Azure/azure-storage-node/releases/tag/1.1.0](https://github.com/Azure/azure-storage-node/releases/tag/1.1.0)|Declaración de instancia de servicio||C++|2.4.0|Paquete NuGet:<br>[https://www.nuget.org/packages/wastorage.v140/2.4.0](https://www.nuget.org/packages/wastorage.v140/2.4.0)<br><br>Versión de GitHub:<br>[https://github.com/Azure/azure-storage-cpp/releases/tag/v2.4.0](https://github.com/Azure/azure-storage-cpp/releases/tag/v2.4.0)|Configuración de la cadena de conexión|
|C++|2.4.0|Paquete NuGet:<br>[https://www.nuget.org/packages/wastorage.v140/2.4.0](https://www.nuget.org/packages/wastorage.v140/2.4.0)<br><br>Versión de GitHub:<br>[https://github.com/Azure/azure-storage-cpp/releases/tag/v2.4.0](https://github.com/Azure/azure-storage-cpp/releases/tag/v2.4.0)|Configuración de la cadena de conexión|
|PHP|0.15.0|Versión de GitHub:<br>[https://github.com/Azure/azure-storage-php/releases/tag/v0.15.0](https://github.com/Azure/azure-storage-php/releases/tag/v0.15.0)<br><br>Instalación a través de Composer (consulte los detalles a continuación)|Configuración de la cadena de conexión|
|Python     |0.30.0|Paquete PIP:<br> [https://pypi.python.org/pypi/azure-storage/0.30.0](https://pypi.python.org/pypi/azure-storage/0.30.0)<br>(Ejecute: `pip install -v azure-storage==0.30.0)`<br><br>Versión de GitHub:<br> [https://github.com/Azure/azure-storage-python/releases/tag/v0.30.0](https://github.com/Azure/azure-storage-python/releases/tag/v0.30.0)|Declaración de instancia de servicio|
|Ruby|0.12.1<br>Vista previa|Paquete de RubyGems:<br> [https://rubygems.org/gems/azure-storage/versions/0.12.1.preview](https://rubygems.org/gems/azure-storage/versions/0.12.1.preview)<br><br>Versión de GitHub:<br> [https://github.com/Azure/azure-storage-ruby/releases/tag/v0.12.1](https://github.com/Azure/azure-storage-ruby/releases/tag/v0.12.1)|Configuración de la cadena de conexión|

> [!NOTE]
> Detalles de PHP<br><br>
>Instalación mediante Composer:
>1. Cree un archivo denominado `composer.json` en la raíz del proyecto con el código siguiente:<br>
>
>   ```
>   {
>       "require":{
>           "Microsoft/azure-storage":"0.15.0"
>        }
>    }
>   ```
>
>2. Descargue el archivo [composer.phar](http://getcomposer.org/composer.phar) en la raíz del proyecto.
>3. Ejecute `php composer.phar install`.
>


## <a name="endpoint-declaration"></a>Declaración de punto de conexión
Un punto de conexión de Azure Stack incluye dos partes: el nombre de una región y el dominio de Azure Stack.
En Azure Stack Development Kit, el punto de conexión predeterminado es **local.azurestack.external**.
Si no está seguro de cuál es su punto de conexión, póngase en contacto con el administrador de la nube.

## <a name="examples"></a>Ejemplos


### <a name="net"></a>.NET

Para Azure Stack, el sufijo del punto de conexión se especifica en el archivo app.config:

```
<add key="StorageConnectionString" 
value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey;
EndpointSuffix=local.azurestack.external;" />
```
### <a name="java"></a>Java

Para Azure Stack, el sufijo del punto de conexión se especifica en la configuración de la cadena de conexión:

```
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account;" +
    "AccountKey=your_storage_account_key;" +
    "EndpointSuffix=local.azurestack.external";
```

### <a name="nodejs"></a>Node.js

Para Azure Stack, el sufijo del punto de conexión se especifica en la instancia de la declaración:

```
var blobSvc = azure.createBlobService('myaccount', 'mykey',
'myaccount.blob.local.azurestack.external');
```
### <a name="c"></a>C++

Para Azure Stack, el sufijo del punto de conexión se especifica en la configuración de la cadena de conexión:

```
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;
AccountName=your_storage_account;
AccountKey=your_storage_account_key;
EndpointSuffix=local.azurestack.external"));
```

### <a name="php"></a>PHP

Para Azure Stack, el sufijo del punto de conexión se especifica en la configuración de la cadena de conexión:

```
$connectionString = 'BlobEndpoint=http://<storage account name>.blob.local.azurestack.external/;
QueueEndpoint=http:// <storage account name>.queue.local.azurestack.external/;
TableEndpoint=http:// <storage account name>.table.local.azurestack.external/;
AccountName=<storage account name>;AccountKey=<storage account key>'
```

### <a name="python"></a>Python

Para Azure Stack, el sufijo del punto de conexión se especifica en la instancia de la declaración:

```
block_blob_service = BlockBlobService(account_name='myaccount',
account_key='mykey',
endpoint_suffix='local.azurestack.external')
```
### <a name="ruby"></a>Ruby

Para Azure Stack, el sufijo del punto de conexión se especifica en la configuración de la cadena de conexión:

```
set
AZURE_STORAGE_CONNECTION_STRING=DefaultEndpointsProtocol=https;
AccountName=myaccount;
AccountKey=mykey;
EndpointSuffix=local.azurestack.external
```

## <a name="blob-storage"></a>Almacenamiento de blobs

Los siguientes tutoriales de Azure Blob Storage son aplicables a Azure Stack. Tenga en cuenta los requisitos específicos de los sufijos de punto de conexión de Azure Stack que se describen en la sección de [ejemplos](#examples) anterior.

* [Introducción al Almacenamiento de blobs de Azure mediante .NET](../../storage/blobs/storage-dotnet-how-to-use-blobs.md)
* [Uso de Blob Storage en Java](../../storage/blobs/storage-java-how-to-use-blob-storage.md)
* [Cómo usar Blob Storage desde Node.js]../../storage/blobs/storage-nodejs-how-to-use-blob-storage.md)
* [Uso del almacenamiento de blobs en C++](../../storage/blobs/storage-c-plus-plus-how-to-use-blobs.md)
* [Uso del almacenamiento de blobs de PHP](../../storage/blobs/storage-php-how-to-use-blobs.md)
* [Uso de Azure Blob Storage desde Python](../../storage/blobs/storage-python-how-to-use-blob-storage.md)
* [Uso de Blob Storage en Ruby](../../storage/blobs/storage-ruby-how-to-use-blob-storage.md)

## <a name="queue-storage"></a>Queue Storage

Los siguientes tutoriales de Azure Queue Storage son aplicables a Azure Stack. Tenga en cuenta los requisitos específicos de los sufijos de punto de conexión de Azure Stack que se describen en la sección de [ejemplos](#examples) anterior.

* [Introducción a Azure Queue Storage mediante .NET](../../storage/queues/storage-dotnet-how-to-use-queues.md)
* [Uso de Queue Storage en Java](../../storage/queues/storage-java-how-to-use-queue-storage.md)
* [Uso del almacenamiento de colas de Node.js](../../storage/queues/storage-nodejs-how-to-use-queues.md)
* [Uso del almacenamiento de colas en C++](../../storage/queues/storage-c-plus-plus-how-to-use-queues.md)
* [Uso del almacenamiento de colas de PHP](../../storage/queues/storage-php-how-to-use-queues.md)
* [Uso de Queue Storage en Python](../../storage/queues/storage-python-how-to-use-queue-storage.md)
* [Uso del almacenamiento de colas de Ruby](../../storage/queues/storage-ruby-how-to-use-queue-storage.md)


## <a name="table-storage"></a>Almacenamiento de tablas

Los siguientes tutoriales de Azure Table Storage son aplicables a Azure Stack. Tenga en cuenta los requisitos específicos de los sufijos de punto de conexión de Azure Stack que se describen en la sección de [ejemplos](#examples) anterior.

* [Introducción a Azure Table Storage mediante .NET](../../cosmos-db/table-storage-how-to-use-dotnet.md)
* [Uso de Table Storage en Java](../../cosmos-db/table-storage-how-to-use-java.md)
* [Uso de Azure Table Storage en Node.js](../../cosmos-db/table-storage-how-to-use-nodejs.md)
* [Uso de Table Storage desde C++](../../cosmos-db/table-storage-how-to-use-c-plus.md)
* [Uso de Table Storage en PHP](../../cosmos-db/table-storage-how-to-use-php.md)
* [Uso de Table Storage en Python](../../cosmos-db/table-storage-how-to-use-python.md)
* [Uso de Table Storage en Ruby](../../cosmos-db/table-storage-how-to-use-ruby.md)

## <a name="next-steps"></a>Pasos siguientes

* [Introducción a Almacenamiento de Microsoft Azure](../../storage/common/storage-introduction.md)