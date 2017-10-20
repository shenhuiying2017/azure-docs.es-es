---
title: Uso de Blob Storage en Node.js | Microsoft Docs
description: Almacene datos no estructurados en la nube con Almacenamiento de blobs (objetos) de Azure.
services: storage
documentationcenter: nodejs
author: tamram
manager: timlt
editor: tysonn
ms.assetid: 8b0df222-1ca8-4967-8248-6d6d720947b8
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 12/08/2016
ms.author: tamram
ms.openlocfilehash: e52f38d5fb3c100e4275032f9a2a1234961c672b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-blob-storage-from-nodejs"></a>Uso de almacenamiento de blobs en Node.js
[!INCLUDE [storage-selector-blob-include](../../../includes/storage-selector-blob-include.md)]

[!INCLUDE [storage-check-out-samples-all](../../../includes/storage-check-out-samples-all.md)]

## <a name="overview"></a>Información general
En este artículo se muestra cómo realizar algunas tareas comunes con Almacenamiento de blobs. Los ejemplos están escritos con la API de Node.js. Los escenarios descritos incluyen cómo cargar, enumerar, descargar y eliminar blobs.

[!INCLUDE [storage-blob-concepts-include](../../../includes/storage-blob-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-nodejs-application"></a>Creación de una aplicación Node.js
Para instrucciones sobre cómo crear una aplicación Node.js, consulte [Creación de una aplicación web Node.js en Azure App Service], [Creación e implementación de una aplicación Node.js en un servicio en la nube de Azure](../../cloud-services/cloud-services-nodejs-develop-deploy-app.md) con Windows PowerShell o [Creación e implementación de una aplicación web Node.js en Azure con Web Matrix](https://www.microsoft.com/web/webmatrix/).

## <a name="configure-your-application-to-access-storage"></a>Configuración de la aplicación para acceder al almacenamiento
Para usar el almacenamiento de Azure necesitará el SDK de almacenamiento de Azure para Node.js, que incluye un conjunto de útiles bibliotecas que se comunican con los servicios REST de almacenamiento.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Uso del Administrador de paquetes para Node (NPM) para obtener el paquete
1. Use una interfaz de línea de comandos como **PowerShell** (Windows), **Terminal** (Mac) o **Bash** (Unix) para ir a la carpeta donde ha creado la aplicación de ejemplo.
2. Escriba **npm install azure-storage** en la ventana de comandos. La salida del comando es similar al ejemplo de código siguiente.

        azure-storage@0.5.0 node_modules\azure-storage
        +-- extend@1.2.1
        +-- xmlbuilder@0.4.3
        +-- mime@1.2.11
        +-- node-uuid@1.4.3
        +-- validator@3.22.2
        +-- underscore@1.4.4
        +-- readable-stream@1.0.33 (string_decoder@0.10.31, isarray@0.0.1, inherits@2.0.1, core-util-is@1.0.1)
        +-- xml2js@0.2.7 (sax@0.5.2)
        +-- request@2.57.0 (caseless@0.10.0, aws-sign2@0.5.0, forever-agent@0.6.1, stringstream@0.0.4, oauth-sign@0.8.0, tunnel-agent@0.4.1, isstream@0.1.2, json-stringify-safe@5.0.1, bl@0.9.4, combined-stream@1.0.5, qs@3.1.0, mime-types@2.0.14, form-data@0.2.0, http-signature@0.11.0, tough-cookie@2.0.0, hawk@2.3.1, har-validator@1.8.0)
3. Puede ejecutar manualmente el comando **ls** para comprobar si se ha creado la carpeta **node\_modules**. Dentro de dicha carpeta, busque el paquete **azure-storage** , que contiene las bibliotecas necesarias para el acceso al almacenamiento.

### <a name="import-the-package"></a>Importación del paquete
Con el Bloc de notas u otro editor de texto, agregue lo siguiente en la parte superior del archivo **server.js** de la aplicación en la que pretenda usar el almacenamiento:

```nodejs
var azure = require('azure-storage');
```

## <a name="set-up-an-azure-storage-connection"></a>Configuración de una conexión de almacenamiento de Azure
El módulo de Azure leerá las variables de entorno `AZURE_STORAGE_ACCOUNT` y `AZURE_STORAGE_ACCESS_KEY` o `AZURE_STORAGE_CONNECTION_STRING` para obtener la información necesaria para conectarse a la cuenta de almacenamiento de Azure. Si no se configuran estas variables de entorno, debe especificar la información de la cuenta llamando a **createBlobService**.

## <a name="create-a-container"></a>Crear un contenedor
El objeto **BlobService** permite trabajar con contenedores y blobs. El código siguiente crea un objeto **BlobService** . Agregue lo siguiente cerca de la parte superior de **server.js**:

```nodejs
var blobSvc = azure.createBlobService();
```

> [!NOTE]
> Puede obtener acceso a un blob de forma anónima mediante **createBlobServiceAnonymous** y proporcionando la dirección del host. Por ejemplo, use `var blobSvc = azure.createBlobServiceAnonymous('https://myblob.blob.core.windows.net/');`.
>
>

[!INCLUDE [storage-container-naming-rules-include](../../../includes/storage-container-naming-rules-include.md)]

Para crear un nuevo contenedor, use **createContainerIfNotExists**. En el siguiente ejemplo de código se crea un nuevo contenedor llamado "mycontainer":

```nodejs
blobSvc.createContainerIfNotExists('mycontainer', function(error, result, response){
    if(!error){
      // Container exists and is private
    }
});
```

Si se acaba de crear el contenedor, `result.created` es true. Si el contenedor ya existe, `result.created` es false. `response` contiene información sobre la operación, incluida la información de ETag del contenedor.

### <a name="container-security"></a>Seguridad del contenedor
De forma predeterminada, los nuevos contenedores son privados, así que no se puede acceder a ellos de forma anónima. Para que el contenedor sea público, de modo que pueda tener acceso de forma anónima, puede establecer el nivel de acceso del contenedor en **blob** o en **contenedor**.

* **blob** : permite el acceso de lectura anónimo al contenido del blob y a los metadatos del contenedor, pero no al listado de todos los blobs de un contenedor determinado.
* **contenedor** : permite el acceso de lectura anónimo al contenido del blob y los metadatos, incluidos los del contenedor.

En el ejemplo de código siguiente se demuestra cómo configurar el nivel de acceso en **blob**:

```nodejs
blobSvc.createContainerIfNotExists('mycontainer', {publicAccessLevel : 'blob'}, function(error, result, response){
    if(!error){
      // Container exists and allows
      // anonymous read access to blob
      // content and metadata within this container
    }
});
```

También puede modificar el nivel de acceso de un contenedor usando **setContainerAcl** para especificarlo. En el ejemplo de código siguiente se cambia el nivel de acceso a container:

```nodejs
blobSvc.setContainerAcl('mycontainer', null /* signedIdentifiers */, {publicAccessLevel : 'container'} /* publicAccessLevel*/, function(error, result, response){
  if(!error){
    // Container access level set to 'container'
  }
});
```

El resultado contiene información sobre la operación, junto con la información de **ETag** actual del contenedor.

### <a name="filters"></a>Filtros
Puede aplicar operaciones de filtrado opcionales a las operaciones realizadas mediante **BlobService**. Las operaciones de filtrado pueden incluir registros, reintentos automáticos, etc. Los filtros son objetos que implementan un método con la firma:

```nodejs
function handle (requestOptions, next)
```

Después de realizar el preprocesamiento en las opciones de solicitud, el método tiene que llamar a "next" pasando una devolución de llamada con la firma siguiente:

```nodejs
function (returnObject, finalCallback, next)
```

En esta devolución de llamada y después de procesar returnObject (la respuesta de la solicitud al servidor), la devolución de llamada tiene que invocar a next, si existe, para continuar procesando otros filtros, o bien simplemente invocar a finalCallback para finalizar la invocación del servicio.

Se incluyen dos filtros que implementan la lógica de reintento con el SDK de Azure para Node.js: **ExponentialRetryPolicyFilter** y **LinearRetryPolicyFilter**. Lo siguiente crea un objeto **BlobService** que usa **ExponentialRetryPolicyFilter**:

```nodejs
var retryOperations = new azure.ExponentialRetryPolicyFilter();
var blobSvc = azure.createBlobService().withFilter(retryOperations);
```

## <a name="upload-a-blob-into-a-container"></a>Cargar un blob en un contenedor
Hay tres tipos de blobs: blobs en bloques, blobs en páginas y blobs en anexos. Los blobs en bloques permiten cargar datos de gran tamaño de una forma más eficaz. Los blobs en anexos están optimizados para las operaciones de anexión. Los blobs en páginas están optimizados para las operaciones de lectura y escritura. Para más información, consulte [Descripción de los blobs en bloques, en anexos y en páginas](http://msdn.microsoft.com/library/azure/ee691964.aspx).

### <a name="block-blobs"></a>Blobs en bloques
Para cargar datos en un blob en bloque, use lo siguiente:

* **createBlockBlobFromLocalFile** : crea un nuevo blob en bloques y carga el contenido de un archivo.
* **createBlockBlobFromStream** : crea un nuevo blob en bloques y carga el contenido de una secuencia.
* **createBlockBlobFromText** : crea un nuevo blob en bloques y carga el contenido de una cadena.
* **createWriteStreamToBlockBlob** : proporciona una secuencia de escritura a un blob en bloques.

En el siguiente ejemplo de código se carga el contenido del archivo **test.txt** en **myblob**.

```nodejs
blobSvc.createBlockBlobFromLocalFile('mycontainer', 'myblob', 'test.txt', function(error, result, response){
  if(!error){
    // file uploaded
  }
});
```

El `result` que devuelven estos métodos contiene información sobre la operación, como el valor **ETag** del blob.

### <a name="append-blobs"></a>Blobs en anexos
Para cargar datos en un nuevo blob en anexos, use lo siguiente:

* **createAppendBlobFromLocalFile** : crea un nuevo blob en anexos y carga el contenido de un archivo.
* **createAppendBlobFromStream** : crea un nuevo blob en anexos y carga el contenido de una transmisión.
* **createAppendBlobFromText** : crea un nuevo blob en anexos y carga el contenido de una cadena.
* **createWriteStreamToNewAppendBlob** : crea un nuevo blob en anexos y luego proporciona una transmisión para escribir en él.

En el siguiente ejemplo de código se carga el contenido del archivo **test.txt** en **myappendblob**.

```nodejs
blobSvc.createAppendBlobFromLocalFile('mycontainer', 'myappendblob', 'test.txt', function(error, result, response){
  if(!error){
    // file uploaded
  }
});
```

Para anexar un bloque a un blob en anexos existente, use lo siguiente:

* **appendFromLocalFile** : anexa el contenido de un archivo a un blob en anexos existente.
* **appendFromStream** : anexa el contenido de una transmisión a un blob en anexos existente.
* **appendFromText** : anexa el contenido de una cadena a un blob en anexos existente.
* **appendBlockFromStream** : anexa el contenido de una transmisión a un blob en anexos existente.
* **appendBlockFromText** : anexa el contenido de una cadena a un blob en anexos existente.

> [!NOTE]
> Las API appendFromXXX realizarán alguna validación de cliente de errores para evitar llamadas innecesarias al servidor. appendBlockFromXXX no.
>
>

En el siguiente ejemplo de código se carga el contenido del archivo **test.txt** en **myappendblob**.

```nodejs
blobSvc.appendFromText('mycontainer', 'myappendblob', 'text to be appended', function(error, result, response){
  if(!error){
    // text appended
  }
});
```

### <a name="page-blobs"></a>Blobs en páginas
Para cargar datos en un blob en página, use lo siguiente:

* **createPageBlob** : crea un nuevo blob en páginas de una longitud especificada.
* **createPageBlobFromLocalFile** : crea un nuevo blob en páginas y carga el contenido de un archivo.
* **createPageBlobFromStream** : crea un nuevo blob en páginas y carga el contenido de una secuencia.
* **createWriteStreamToExistingPageBlob** : proporciona una secuencia de escritura a un blob en páginas existente
* **createWriteStreamToNewPageBlob** : crea un nuevo blob en páginas y luego proporciona una transmisión para escribir en él.

En el siguiente ejemplo de código se carga el contenido del archivo **test.txt** en **mypageblob**.

```nodejs
blobSvc.createPageBlobFromLocalFile('mycontainer', 'mypageblob', 'test.txt', function(error, result, response){
  if(!error){
    // file uploaded
  }
});
```

> [!NOTE]
> Los blobs en páginas constan de 'páginas' de 512 bytes. Al cargar datos con un tamaño que no sea múltiplo de 512 recibirá un error.
>
>

## <a name="list-the-blobs-in-a-container"></a>Enumerar los blobs de un contenedor
Para enumerar los blobs de un contenedor, use el método **listBlobsSegmented** . Si desea devolver blobs con un prefijo determinado, use **listBlobsSegmentedWithPrefix**.

```nodejs
blobSvc.listBlobsSegmented('mycontainer', null, function(error, result, response){
  if(!error){
      // result.entries contains the entries
      // If not all blobs were returned, result.continuationToken has the continuation token.
  }
});
```

El `result` contiene una colección de `entries`, que es una matriz de objetos que describen cada blob. Si no se pueden devolver todos los blobs, el `result` proporciona también un `continuationToken`, que puede usar como un segundo parámetro para recuperar entradas adicionales.

## <a name="download-blobs"></a>Descargar blobs
Para descargar datos de un blob, use lo siguiente:

* **getBlobToLocalFile** : escribe el contenido del blob en un archivo.
* **getBlobToStream** : escribe el contenido del blob en una secuencia.
* **getBlobToText** : escribe el contenido del blob en una cadena.
* **createReadStream** : proporciona una secuencia para leer del blob.

En el ejemplo de código siguiente se demuestra cómo usar **getBlobToStream** para descargar el contenido del blob **myblob** y almacenarlo en el archivo **output.txt** usando una secuencia:

```nodejs
var fs = require('fs');
blobSvc.getBlobToStream('mycontainer', 'myblob', fs.createWriteStream('output.txt'), function(error, result, response){
  if(!error){
    // blob retrieved
  }
});
```

El `result` contiene información acerca del blob, lo que incluye información de **ETag** .

## <a name="delete-a-blob"></a>Eliminar un blob
Finalmente, para eliminar un blob, llame a **deleteBlob**. En el ejemplo de código siguiente se elimina el blob llamado **myblob**.

```nodejs
blobSvc.deleteBlob(containerName, 'myblob', function(error, response){
  if(!error){
    // Blob has been deleted
  }
});
```

## <a name="concurrent-access"></a>simultáneo
Para permitir el acceso simultáneo a un blob desde varios clientes o varias instancias de proceso, puede usar etiquetas **ETag** o **concesiones**.

* **Etag** : proporciona una manera de detectar que otro proceso ha modificado el blob o el contenedor
* **Concesión** : proporciona una manera de obtener acceso exclusivo y renovable de escritura o eliminación a un blob durante un período de tiempo

### <a name="etag"></a>ETag
Use ETag si necesita permitir que varios clientes o instancias escriban en el blob en bloques o en páginas de manera simultánea. La etiqueta ETag le permite determinar si el contenedor o el blob se modificó desde que inicialmente lo leyera o creara. De esta forma, puede evitar que los cambios efectuados por otro cliente o proceso se sobrescriban.

Puede definir las condiciones de ETag mediante el parámetro opcional `options.accessConditions` . En el siguiente ejemplo de código solo se carga el archivo **test.txt** si el blob ya existe y tiene el valor ETag contenido por `etagToMatch`.

```nodejs
blobSvc.createBlockBlobFromLocalFile('mycontainer', 'myblob', 'test.txt', { accessConditions: { EtagMatch: etagToMatch} }, function(error, result, response){
    if(!error){
    // file uploaded
  }
});
```

El patrón general cuando se usan etiquetas ETag es el siguiente:

1. Obtener la etiqueta ETag como resultado de una operación create, list o get.
2. Realizar una acción, comprobando que el valor de ETag no se haya modificado.

Si el valor se modificó, significa que otro cliente u otra instancia ha modificado el blob o el contenedor desde que obtuvo el valor de ETag.

### <a name="lease"></a>Concesión
Puede adquirir una nueva concesión mediante el método **acquireLease** , especificando el blob o el contenedor sobre el que desea obtenerla. Por ejemplo, el siguiente código adquiere una concesión sobre **myblob**.

```nodejs
blobSvc.acquireLease('mycontainer', 'myblob', function(error, result, response){
  if(!error) {
    console.log('leaseId: ' + result.id);
  }
});
```

Las operaciones sucesivas sobre **myblob** deben proporcionar el parámetro `options.leaseId`. El id. de concesión se devuelve como `result.id` desde **acquireLease**.

> [!NOTE]
> De manera predeterminada, la duración de la concesión es infinita. Puede especificar una duración no infinita (entre 15 y 60 segundos) con el parámetro `options.leaseDuration` .
>
>

Para eliminar una concesión, use **releaseLease**. Para interrumpir una concesión, pero impedir que otros obtengan una nueva concesión hasta que expire la duración original, use **breakLease**.

## <a name="work-with-shared-access-signatures"></a>Trabajo con firmas de acceso compartido
Las firmas de acceso compartido (SAS) constituyen una manera segura de ofrecer acceso granular a blobs y contenedores sin proporcionar el nombre o las claves de su cuenta de almacenamiento. Las firmas de acceso compartido se usan con frecuencia para proporcionar acceso limitado a sus datos, por ejemplo, para permitir que una aplicación móvil acceda a los blobs.

> [!NOTE]
> Aunque también puede permitir el acceso anónimo a los blobs, las firmas de acceso compartido le permiten proporcionar acceso más controlado, puesto que debe generar la SAS.
>
>

Una aplicación de confianza, como un servicio basado en la nube, genera firmas de acceso compartido mediante el valor **generateSharedAccessSignature** del elemento **BlobService** y se lo proporciona a una aplicación en la que no se confía o en la que se confía parcialmente como, por ejemplo, una aplicación móvil. Las firmas de acceso compartido se generan usando una directiva que describe las fechas de inicio y finalización durante las que son válidas, junto con el nivel de acceso otorgado al titular de dichas firmas.

En el siguiente ejemplo de código se genera una nueva directiva de acceso compartido que permite al titular de las firmas de acceso compartido realizar operaciones de lectura en el blob **myblob** y que expira 100 minutos después de la hora en que se crea.

```nodejs
var startDate = new Date();
var expiryDate = new Date(startDate);
expiryDate.setMinutes(startDate.getMinutes() + 100);
startDate.setMinutes(startDate.getMinutes() - 100);

var sharedAccessPolicy = {
  AccessPolicy: {
    Permissions: azure.BlobUtilities.SharedAccessPermissions.READ,
    Start: startDate,
    Expiry: expiryDate
  },
};

var blobSAS = blobSvc.generateSharedAccessSignature('mycontainer', 'myblob', sharedAccessPolicy);
var host = blobSvc.host;
```

Tenga en cuenta que también se debe proporcionar la información del host, puesto que es necesaria cuando el titular de las firmas de acceso compartido intenta acceder al contenedor.

La aplicación cliente usa entonces firmas de acceso compartido con **BlobServiceWithSAS** para realizar operaciones en el blob. Lo siguiente obtiene información sobre **myblob**.

```nodejs
var sharedBlobSvc = azure.createBlobServiceWithSas(host, blobSAS);
sharedBlobSvc.getBlobProperties('mycontainer', 'myblob', function (error, result, response) {
  if(!error) {
    // retrieved info
  }
});
```

Puesto que las firmas de acceso compartido se generaron con acceso de solo lectura, si se intenta modificar el blob, se devolverá un error.

### <a name="access-control-lists"></a>Listas de control de acceso
También se puede usar una lista de control de acceso (ACL) para definir la directiva de acceso para SAS. Esto es útil si desea permitir que varios clientes accedan a un contenedor, pero cada uno con directivas de acceso diferentes.

Una ACL se implementa mediante el uso de un conjunto de directivas de acceso, con un Id. asociado a cada directiva. En el siguiente ejemplo de código se definen dos directivas; una para 'user1' y otra para 'user2':

```nodejs
var sharedAccessPolicy = {
  user1: {
    Permissions: azure.BlobUtilities.SharedAccessPermissions.READ,
    Start: startDate,
    Expiry: expiryDate
  },
  user2: {
    Permissions: azure.BlobUtilities.SharedAccessPermissions.WRITE,
    Start: startDate,
    Expiry: expiryDate
  }
};
```

En el siguiente ejemplo de código se obtiene la ACL actual de **mycontainer** y, a continuación, se agregan las nuevas directivas mediante **setBlobAcl**. Este enfoque permite lo siguiente:

```nodejs
var extend = require('extend');
blobSvc.getBlobAcl('mycontainer', function(error, result, response) {
  if(!error){
    var newSignedIdentifiers = extend(true, result.signedIdentifiers, sharedAccessPolicy);
    blobSvc.setBlobAcl('mycontainer', newSignedIdentifiers, function(error, result, response){
      if(!error){
        // ACL set
      }
    });
  }
});
```

Después de establecer una ACL, puede crear luego firmad de acceso compartido basadas en el id. de una directiva. En el ejemplo de código siguiente se crean nuevas firmas de acceso compartido para 'user 2':

```nodejs
blobSAS = blobSvc.generateSharedAccessSignature('mycontainer', { Id: 'user2' });
```

## <a name="next-steps"></a>Pasos siguientes
Para obtener más información, consulte los siguientes recursos:

* [Referencia de la API del SDK de Azure Storage para Node][Referencia de la API del SDK de Azure Storage para Node]  
* [Blog del equipo de Azure Storage] [Blog del equipo de Azure Storage]  
* [SDK de Azure Storage para Node.js][Azure Storage SDK for Node] en GitHub  
* [Centro para desarrolladores de Node.js](https://azure.microsoft.com/develop/nodejs/)  
* [Introducción a la utilidad de línea de comandos AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)  

[Azure Storage SDK for Node]: https://github.com/Azure/azure-storage-node  

[Build and deploy a Node.js web app to Azure using Web Matrix]: https://www.microsoft.com/web/webmatrix/  
[Using the REST API]: http://msdn.microsoft.com/library/azure/hh264518.aspx  
[Azure portal]: https://portal.azure.com  
[Compilación e implementación de una aplicación Node.js en una instancia de Azure Cloud Service](../../cloud-services/cloud-services-nodejs-develop-deploy-app.md)  
[Blog del equipo de Azure Storage]: http://blogs.msdn.com/b/windowsazurestorage/  
[Referencia de la API del SDK de Azure Storage para Node]: http://dl.windowsazure.com/nodestoragedocs/index.html  
