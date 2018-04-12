---
title: 'Inicio rápido de Azure: Carga, descarga y enumeración de blobs en Azure Storage mediante Node.js | Microsoft Docs'
description: En esta guía de inicio rápido, puede crear una cuenta de almacenamiento y un contenedor. Después, puede usar la biblioteca de clientes de almacenamiento para Node.js a fin de cargar un blob en Azure Storage, descargar un blob o enumerar los blobs de un contenedor.
services: storage
author: craigshoemaker
manager: jeconnoc
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 03/15/2018
ms.author: cshoe
ms.openlocfilehash: 8783b83a1a94caf4a49f9da7a2dd30c9cb52df22
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/06/2018
---
# <a name="quickstart-upload-download-and-list-blobs-using-nodejs"></a>Inicio rápido: Carga, descarga y enumeración de blobs mediante Node.js

En esta guía de inicio rápido, aprenderá a usar Node.js para cargar, descargar y enumerar blobs en bloques en un contenedor con Azure Blob Storage.

Para completar esta guía de inicio rápido, necesitará una [suscripción de Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [storage-quickstart-tutorial-create-account-portal](../../../includes/storage-quickstart-tutorial-create-account-portal.md)]

## <a name="download-the-sample-application"></a>Descarga de la aplicación de ejemplo

La [aplicación de ejemplo](https://github.com/Azure-Samples/storage-blobs-node-quickstart.git) utilizada en esta guía de inicio rápido es una sencilla aplicación de consola de Node.js. Para comenzar, clone el repositorio en el equipo con el comando siguiente:

```bash
git clone https://github.com/Azure-Samples/storage-blobs-node-quickstart.git
```

Para abrir la aplicación, busque la carpeta *storage-blobs-node-quickstart* y ábrala en el entorno de edición de código de su elección.

[!INCLUDE [storage-copy-connection-string-portal](../../../includes/storage-copy-connection-string-portal.md)]

## <a name="configure-your-storage-connection-string"></a>Configuración de la cadena de conexión de almacenamiento.

Antes de ejecutar la aplicación, debe proporcionar la cadena de conexión de la cuenta de almacenamiento. El repositorio de ejemplo incluye un archivo llamado *.env.example*. Puede cambiar el nombre de este archivo quitando la extensión *.example*, lo que resulta en un archivo llamado *.env*. Dentro del archivo *.env*, agregue el valor de la cadena de conexión después de la clave *AZURE_STORAGE_CONNECTION_STRING*.

## <a name="install-required-packages"></a>Instalación de los paquetes requeridos

En el directorio de la aplicación, ejecute *npm install* para instalar los paquetes necesarios para la aplicación.

```bash
npm install
```

## <a name="run-the-sample"></a>Ejecución del ejemplo
Ahora que las dependencias están instaladas, puede ejecutar el ejemplo pasando comandos al script. Por ejemplo, para crear un contenedor de blobs ejecute el siguiente comando:

```bash
node index.js --command createContainer
```

Entre los comandos disponibles se incluyen:

| Get-Help | DESCRIPCIÓN |
|---------|---------|
|*createContainer* | Crea un contenedor llamado *test-container* (se realiza correctamente aunque ya exista el contenedor) |
|*upload*          | Carga el archivo *example.txt* en el contenedor *test-container* |
|*download*        | Descarga el contenido del blob *example* en *example.downloaded.txt* |
|*delete*          | Elimina el blob *example* |
|*list*            | Enumera el contenido del contenedor *test-container* en la consola |


## <a name="understanding-the-sample-code"></a>Descripción del código de ejemplo
Este ejemplo de código usa algunos módulos para interactuar con el sistema de archivos y la línea de comandos. 

```javascript
if (process.env.NODE_ENV !== 'production') {
    require('dotenv').load();
}
const path = require('path');
const args = require('yargs').argv;
const storage = require('azure-storage');
```

El propósito de los módulos es el siguiente: 

- *dotenv* carga las variables de entorno definidas en un archivo llamado *.env* en el contexto de ejecución actual
- *path* es necesario para determinar la ruta de acceso absoluta del archivo que se va a cargar en Blob Storage
- *yargs* expone una interfaz sencilla para acceder a los argumentos de la línea de comandos
- *azure-storage* es el módulo del [SDK de Azure Storage](/nodejs/api/azure-storage) para Node.js

A continuación, se inicializan diversas variables:

```javascript
const blobService = storage.createBlobService();
const containerName = 'test-container';
const sourceFilePath = path.resolve('./example.txt');
const blobName = path.basename(sourceFilePath, path.extname(sourceFilePath));
```

Las variables se establecen en los siguientes valores:

- *blobService* se establece en una nueva instancia de Azure Blob Service
- *containerName* se establece en el nombre del contenedor
- *sourceFilePath* se establece en la ruta de acceso absoluta del archivo que se va a cargar
- *blobName* se crea tomando el nombre de archivo y eliminando la extensión de nombre archivo

En la implementación siguiente, cada una de las funciones de *blobService* se encapsula en una *Promesa*, lo que permite el acceso a la función *asincrónica* de JavaScript y al operador *await* para ajustarse a la naturaleza de devolución de llamada de la [API de Azure Storage](/nodejs/api/azure-storage/blobservice). Cuando se devuelve una respuesta correcta para cada función, la promesa resuelve con los datos pertinentes junto con un mensaje específico para la acción.

### <a name="create-a-blob-container"></a>Creación de un contenedor de blobs

La función *createContainer* llama a [createContainerIfNotExists](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_createContainerIfNotExists) y establece el nivel de acceso adecuado para el blob.

```javascript
const createContainer = () => {
    return new Promise((resolve, reject) => {
        blobService.createContainerIfNotExists(containerName, { publicAccessLevel: 'blob' }, err => {
            if(err) {
                reject(err);
            } else {
                resolve({ message: `Container '${containerName}' created` });
            }
        });
    });
};
```

El segundo parámetro (*options*) de **createContainerIfNotExists** acepta un valor para [publicAccessLevel](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_createContainerIfNotExists). El valor *blob* para *publicAccessLevel* especifica que los datos del blob en cuestión se exponen al público. Esta configuración es opuesta al nivel de acceso *container*, que otorga la capacidad de enumerar el contenido del contenedor.

El uso de **createContainerIfNotExists** permite a la aplicación ejecutar el comando *createContainer* varias veces sin devolver errores cuando el contenedor ya existe. En un entorno de producción, a menudo solo se llama a **createContainerIfNotExists** una vez, ya que se usa el mismo contenedor a lo largo de la aplicación. En estos casos, puede crear el contenedor con antelación a través del portal o mediante la CLI de Azure.

### <a name="upload-a-blob-to-the-container"></a>Carga de un blob en el contenedor

La función *upload* utiliza la función [createBlockBlobFromLocalFile](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_createBlockBlobFromLocalFile) para cargar y escribir o sobrescribir un archivo desde el sistema de archivos a Blob Storage. 

```javascript
const upload = () => {
    return new Promise((resolve, reject) => {
        blobService.createBlockBlobFromLocalFile(containerName, blobName, sourceFilePath, err => {
            if(err) {
                reject(err);
            } else {
                resolve({ message: `Upload of '${blobName}' complete` });
            }
        });
    });
};
```
En el contexto de la aplicación de ejemplo, el archivo llamado *example.txt* se carga en un blob llamado *example* dentro de un contenedor llamado *test-container*. Otros métodos disponibles para cargar contenido en blobs incluyen trabajar con [texto](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_createBlockBlobFromText) y [secuencias](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_createBlockBlobFromStream).

Para comprobar que el archivo se ha cargado en Blob Storage, puede usar el [Explorador de Azure Storage](https://azure.microsoft.com/en-us/features/storage-explorer/) para ver los datos de la cuenta.

### <a name="list-the-blobs-in-a-container"></a>Enumerar los blobs de un contenedor

La función *list* llama al método [listBlobsSegmented](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_createBlockBlobFromText) para devolver una lista de los metadatos de los blobs del contenedor. 

```javascript
const list = () => {
    return new Promise((resolve, reject) => {
        blobService.listBlobsSegmented(containerName, null, (err, data) => {
            if(err) {
                reject(err);
            } else {
                resolve({ message: `Items in container '${containerName}':`, data: data });
            }
        });
    });
};
```

Al llamar a *listBlobsSegmented*, devuelve los metadatos de los blobs como una matriz de instancias de [BlobResult](/nodejs/api/azure-storage/blobresult). Los resultados se devuelven en lotes de un incremento de 5 000 (segmentos). Si hay más de 5 000 blobs en un contenedor, los resultados incluyen un valor para **continuationToken**. Para enumerar los segmentos posteriores del contenedor de blobs, puede pasar el token de continuación a **listBlobSegmented** como segundo argumento.

### <a name="download-a-blob-from-the-container"></a>Descarga de un blob del contenedor

La función *download* utiliza [getBlobToLocalFile](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_getBlobToLocalFile) para descargar el contenido del blob en la ruta de acceso absoluta del archivo especificada.

```javascript
const download = () => {
    const dowloadFilePath = sourceFilePath.replace('.txt', '.downloaded.txt');
    return new Promise((resolve, reject) => {
        blobService.getBlobToLocalFile(containerName, blobName, dowloadFilePath, err => {
            if(err) {
                reject(err);
            } else {
                resolve({ message: `Download of '${blobName}' complete` });
            }
        });
    });
};
```
La implementación que se muestra a continuación cambia la ruta de acceso del archivo de origen para anexar *.downloaded.txt* al nombre de archivo. En contextos del mundo real, puede cambiar la ubicación, así como el nombre de archivo cuando se selecciona un destino de descarga.

### <a name="delete-blobs-in-the-container"></a>Eliminación de blobs del contenedor

La función *deleteBlock* (cuyo alias es el comando de la consola *delete*) llama a la función [deleteBlobIfExists](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_deleteBlobIfExists). Como su nombre indica, esta función no devuelve un error si el blob ya se ha eliminado.

```javascript
const deleteBlock = () => {
    return new Promise((resolve, reject) => {
        blobService.deleteBlobIfExists(containerName, blobName, err => {
            if(err) {
                reject(err);
            } else {
                resolve({ message: `Block blob '${blobName}' deleted` });
            }
        });
    });
};
```

### <a name="upload-and-list"></a>Carga y enumeración

Una de las ventajas de usar promesas es que es posible encadenar comandos. La función **uploadAndList** muestra lo fácil que es mostrar el contenido de un blob directamente después de cargar un archivo.

```javascript
const uploadAndList = () => {
    return _module.upload().then(_module.list);
};
```

### <a name="calling-code"></a>Llamadas al código

Para exponer las funciones implementadas en la línea de comandos, cada una de las funciones se asigna a un literal de objeto.

```javascript
const _module = {
    "createContainer": createContainer,
    "upload": upload,
    "download": download,
    "delete": deleteBlock,
    "list": list,
    "uploadAndList": uploadAndList
};
```

Con *_module* ahora en su lugar, cada uno de los comandos está disponible desde la línea de comandos.

```javascript
const commandExists = () => exists = !!_module[args.command];
```

Si un comando dado no existe, se representan las propiedades de *_module* en la consola como texto de ayuda para el usuario. 

La función *executeCommand* es una función *asincrónica* que llama al comando especificado mediante el operador *await* y registra los mensajes de datos en la consola.

```javascript
const executeCommand = async () => {
    const response = await _module[args.command]();

    console.log(response.message);

    if (response.data) {
        response.data.entries.forEach(entry => {
            console.log('Name:', entry.name, ' Type:', entry.blobType)
        });
    }
};
```

Por último, la ejecución del código llama primero a *commandExists* para comprobar que se pasa al script un comando conocido. Si se selecciona un comando existente, se ejecuta dicho comando y los errores se registran en la consola.

```javascript
try {
    const cmd = args.command;

    console.log(`Executing '${cmd}'...`);

    if (commandExists()) {
        executeCommand();
    } else {
        console.log(`The '${cmd}' command does not exist. Try one of these:`);
        Object.keys(_module).forEach(key => console.log(` - ${key}`));
    }
} catch (e) {
    console.log(e);
}
```

## <a name="clean-up-resources"></a>Limpieza de recursos
Si no planea usar los datos o las cuentas creadas en este artículo, puede que desee eliminarlos para evitar una facturación no deseada. Para eliminar el blob y los contenedores, puede usar los métodos [deleteBlobIfExists](/nodejs/api/azure-storage/blobservice?view=azure-node-latest#deleteBlobIfExists_container__blob__options__callback_) y [deleteContainerIfExists](/nodejs/api/azure-storage/blobservice?view=azure-node-latest#deleteContainerIfExists_container__options__callback_). También puede eliminar la cuenta de almacenamiento [a través del portal](../common/storage-create-storage-account.md).

## <a name="resources-for-developing-nodejs-applications-with-blobs"></a>Recursos para el desarrollo de aplicaciones Node.js con blobs

Consulte estos recursos adicionales para el desarrollo de Node.js con Blob Storage:

### <a name="binaries-and-source-code"></a>Archivos binarios y código fuente

- Consulte e instale el [código fuente de la biblioteca de cliente de Node.js](https://github.com/Azure/azure-storage-node) para Azure Storage en GitHub.

### <a name="client-library-reference-and-samples"></a>Referencia a la biblioteca de clientes y ejemplos

- Para más información acerca de la biblioteca de cliente de Node.js, consulte la [referencia de la API de Node.js](https://docs.microsoft.com/en-us/javascript/api/overview/azure/storage).
- Explore los [ejemplos de Blob Storage](https://azure.microsoft.com/resources/samples/?sort=0&service=storage&platform=nodejs&term=blob) escritos mediante la biblioteca de cliente de Node.js.

## <a name="next-steps"></a>Pasos siguientes

Esta guía de inicio rápido muestra cómo cargar un archivo entre un disco local y Azure Blob Storage mediante Node.js. Para más información sobre cómo trabajar con Blob Storage, continúe con los procedimientos de Blob Storage.

> [!div class="nextstepaction"]
> [Procedimientos de las operaciones de Blob Storage](storage-nodejs-how-to-use-blob-storage.md)

Para la referencia de Node.js para Azure Storage, consulte [azure-storage package](https://docs.microsoft.com/javascript/api/azure-storage/?view=azure-node-latest) (Paquete azure-storage).///