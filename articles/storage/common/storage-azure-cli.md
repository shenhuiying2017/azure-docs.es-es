---
title: Uso de la CLI de Azure 2.0 con Azure Storage | Microsoft Docs
description: "Aprenda a usar la interfaz de línea de comandos (CLI de Azure) 2.0 de Azure con Azure Storage para crear y administrar cuentas de almacenamiento y trabajar con archivos y blobs de Azure. La CLI de Azure 2.0 es una herramienta multiplataforma escrita en Python."
services: storage
documentationcenter: na
author: tamram
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: article
ms.date: 06/02/2017
ms.author: tamram
ms.openlocfilehash: 34780001afb309a2986cc21dae948d9d94f1a63f
ms.sourcegitcommit: 28178ca0364e498318e2630f51ba6158e4a09a89
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/24/2018
---
# <a name="using-the-azure-cli-20-with-azure-storage"></a>Uso de la CLI de Azure 2.0 con Azure Storage

La CLI de Azure 2.0 es de código abierto y multiplataforma, y proporciona un conjunto de comandos para trabajar con la plataforma de Azure. Proporciona muchas de las funciones que se encuentran en el [portal de Azure](https://portal.azure.com), incluido el acceso a datos enriquecidos.

En esta guía, se muestra cómo utilizar la [CLI de Azure 2.0](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2) para realizar varias tareas trabajando con recursos en su cuenta de Azure Storage. Antes de usar esta guía es aconsejable descargar e instalar la versión de la CLI 2.0 más reciente, o actualizarse a ella.

Los ejemplos de la guía se basan en el shell Bash en Ubuntu, pero el resto de las plataformas tienen un comportamiento semejante. 

[!INCLUDE [storage-cli-versions](../../../includes/storage-cli-versions.md)]

## <a name="prerequisites"></a>requisitos previos
En esta guía se supone que conoce los conceptos básicos de Azure Storage. También se da por sentado que puede cumplir los requisitos para la creación de cuentas que se especifican a continuación en Azure y el servicio Almacenamiento.

### <a name="accounts"></a>Cuentas
* **Cuenta de Azure**: si aún no tiene ninguna suscripción a Azure, [cree una cuenta gratuita de Azure](https://azure.microsoft.com/free/).
* **Cuenta de almacenamiento**: consulte la sección [Crear una cuenta de almacenamiento](storage-create-storage-account.md#create-a-storage-account) del artículo [Acerca de las cuentas de Azure Storage](storage-create-storage-account.md).

### <a name="install-the-azure-cli-20"></a>Instalación de la CLI de Azure 2.0

Descargue e instale la CLI de Azure 2.0 siguiendo las instrucciones que encontrará en [Instalación de la CLI de Azure 2.0](/cli/azure/install-az-cli2).

> [!TIP]
> Si tiene problemas con la instalación, consulte la sección [Solución de problemas de instalación](/cli/azure/install-az-cli2#installation-troubleshooting) del artículo y la guía [Install Troubleshooting](https://github.com/Azure/azure-cli/blob/master/doc/install_troubleshooting.md) (Solución de problemas de instalación) en GitHub.
>

## <a name="working-with-the-cli"></a>Trabajo con la CLI

Cuando haya instalado la CLI, puede usar el comando `az` desde su interfaz de la línea de comandos (Bash, Terminal, símbolo del sistema) para obtener acceso a los comandos de la CLI de Azure. Escriba el comando `az` para ver una lista completa de los comandos base (se truncó el resultado del ejemplo siguiente):

```
     /\
    /  \    _____   _ _ __ ___
   / /\ \  |_  / | | | \'__/ _ \
  / ____ \  / /| |_| | | |  __/
 /_/    \_\/___|\__,_|_|  \___|


Welcome to the cool new Azure CLI!

Here are the base commands:

    account          : Manage subscriptions.
    acr              : Manage Azure container registries.
    acs              : Manage Azure Container Services.
    ad               : Synchronize on-premises directories and manage Azure Active Directory
                       resources.
    ...
```

En la interfaz de la línea de comandos, ejecute el comando `az storage --help` para ver una lista de comandos de los subgrupos de `storage`. Las descripciones de los subgrupos proporcionan información general de la función que proporciona la CLI de Azure para trabajar con sus recursos de almacenamiento.

```
Group
    az storage: Durable, highly available, and massively scalable cloud storage.

Subgroups:
    account  : Manage storage accounts.
    blob     : Object storage for unstructured data.
    container: Manage blob storage containers.
    cors     : Manage Storage service Cross-Origin Resource Sharing (CORS).
    directory: Manage file storage directories.
    entity   : Manage table storage entities.
    file     : File shares that use the standard SMB 3.0 protocol.
    logging  : Manage Storage service logging information.
    message  : Manage queue storage messages.
    metrics  : Manage Storage service metrics.
    queue    : Use queues to effectively scale applications according to traffic.
    share    : Manage file shares.
    table    : NoSQL key-value storage using semi-structured datasets.
```

## <a name="connect-the-cli-to-your-azure-subscription"></a>Conexión de la CLI a una suscripción de Azure

Para trabajar con los recursos de su suscripción de Azure, primero debe iniciar sesión en su cuenta de Azure con `az login`. Hay varias formas de iniciar sesión:

* **Inicio de sesión interactivo**: `az login`
* **Inicio de sesión con nombre de usuario y contraseña**: `az login -u johndoe@contoso.com -p VerySecret`
  * Esta opción no es compatible con cuentas de Microsoft o cuentas que usan autenticación multifactor.
* **Inicio de sesión con una entidad de servicio**: `az login --service-principal -u http://azure-cli-2016-08-05-14-31-15 -p VerySecret --tenant contoso.onmicrosoft.com`

## <a name="azure-cli-20-sample-script"></a>Script de ejemplo de CLI de Azure 2.0

A continuación, vamos a trabajar con un pequeño script de shell que emite algunos comandos básicos de la CLI de Azure 2.0 para interactuar con los recursos de Azure Storage. El script crea primero un nuevo contenedor en su cuenta de almacenamiento, y después carga un archivo existente (p. ej., un blob) en ese contenedor. Después enumera todos los blobs del contenedor y, por último, descarga el archivo en el destino que especifique del PC local.

```bash
#!/bin/bash
# A simple Azure Storage example script

export AZURE_STORAGE_ACCOUNT=<storage_account_name>
export AZURE_STORAGE_ACCESS_KEY=<storage_account_key>

export container_name=<container_name>
export blob_name=<blob_name>
export file_to_upload=<file_to_upload>
export destination_file=<destination_file>

echo "Creating the container..."
az storage container create --name $container_name

echo "Uploading the file..."
az storage blob upload --container-name $container_name --file $file_to_upload --name $blob_name

echo "Listing the blobs..."
az storage blob list --container-name $container_name --output table

echo "Downloading the file..."
az storage blob download --container-name $container_name --name $blob_name --file $destination_file --output table

echo "Done"
```

**Configuración y ejecución del script**

1. Abra el editor de texto que prefiera; copie y pegue el script anterior en el editor.

2. Después actualice las variables del script para incluir sus opciones de configuración. Reemplace los valores siguientes tal y como se especifica:

   * **\<storage_account_name\>** El nombre de la cuenta de almacenamiento.
   * **\<storage_account_key\>** La clave de acceso principal o secundaria para su cuenta de almacenamiento.
   * **\<container_name\>** Un nombre para el nuevo contenedor creado, por ejemplo, "contenedor-ejemplo-cli-azure".
   * **\<blob_name\>** Un nombre para el blob de destino en el contenedor.
   * **\<file_to_upload\>** La ruta a un archivo pequeño en su PC local, como "~/imágenes/HolaMundo.png".
   * **\<destination_file\>** La ruta de acceso del archivo de destino, como "~/imagenDescargada.png".

3. Después de actualizar las variables necesarias, guarde el script y salga del editor. En los siguientes pasos se da por hecho que ha llamado al script **my_storage_sample.sh**.

4. Marque el script como archivo ejecutable si es necesario: `chmod +x my_storage_sample.sh`

5. Ejecute el script. Por ejemplo, en Bash: `./my_storage_sample.sh`

Debería ver un resultado similar al siguiente, y el **\<archivo_destino\>** que especificó en el script debería estar en su PC local.

```
Creating the container...
{
  "created": true
}
Uploading the file...
Percent complete: %100.0
Listing the blobs...
Name       Blob Type      Length  Content Type              Last Modified
---------  -----------  --------  ------------------------  -------------------------
README.md  BlockBlob        6700  application/octet-stream  2017-05-12T20:54:59+00:00
Downloading the file...
Name
---------
README.md
Done
```

> [!TIP]
> El resultado anterior está en formato de **tabla**. Puede especificar el formato de salida que se usa indicando el argumento `--output` en los comandos de la CLI, o definirlo a nivel global mediante `az configure`.
>

## <a name="manage-storage-accounts"></a>Administrar cuentas de almacenamiento

### <a name="create-a-new-storage-account"></a>Creación de una cuenta de almacenamiento nueva
Para usar Azure Storage, necesita una cuenta de almacenamiento. Puede crear una nueva cuenta de Azure Storage después de configurar el equipo para que pueda [conectarse a su suscripción](#connect-to-your-azure-subscription).

```azurecli
az storage account create \
    --location <location> \
    --name <account_name> \
    --resource-group <resource_group> \
    --sku <account_sku>
```

* `--location` [Requerido]: Ubicación. Por ejemplo, "Oeste de EE. UU.".
* `--name` [Requerido]: Nombre de la cuenta de almacenamiento. Debe tener entre 3 y 24 caracteres y usar solo caracteres alfanuméricos en minúsculas.
* `--resource-group`[Required]: Nombre del grupo de recursos.
* `--sku` [Requerido]: SKU de la cuenta de almacenamiento. Valores permitidos:
  * `Premium_LRS`
  * `Standard_GRS`
  * `Standard_LRS`
  * `Standard_RAGRS`
  * `Standard_ZRS`
```

### Set default Azure storage account environment variables
You can have multiple storage accounts in your Azure subscription. To select one of them to use for all subsequent storage commands, you can set these environment variables:

```azurecli
export AZURE_STORAGE_ACCOUNT=<account_name>
export AZURE_STORAGE_ACCESS_KEY=<key>
```

Otra forma de establecer una cuenta de almacenamiento predeterminada es mediante una cadena de conexión. En primer lugar, obtenga la cadena de conexión con el comando `show-connection-string`:

```azurecli
az storage account show-connection-string \
    --name <account_name> \
    --resource-group <resource_group>
```

Después copie el resultado de la cadena de conexión y establezca la variable de entorno `AZURE_STORAGE_CONNECTION_STRING` (tal vez tenga que incluir la cadena de conexión entre comillas):

```azurecli
export AZURE_STORAGE_CONNECTION_STRING="<connection_string>"
```

> [!NOTE]
> Todos los ejemplos en las secciones siguientes de este artículo asumen que ha establecido las variables de entorno `AZURE_STORAGE_ACCOUNT` y `AZURE_STORAGE_ACCESS_KEY`.
>

## <a name="create-and-manage-blobs"></a>Creación y administración de blobs
El almacenamiento de blobs de Azure es un servicio para almacenar grandes cantidades de datos no estructurados, como texto o datos binarios, a los que puede acceder desde cualquier lugar del mundo a través de HTTP o HTTPS. En esta sección se supone que ya está familiarizado con los conceptos del almacenamiento de Azure Blob Storage. Para más información, consulte [Introducción a Azure Blob Storage mediante .NET](../blobs/storage-dotnet-how-to-use-blobs.md) y [Conceptos de Blob service](/rest/api/storageservices/blob-service-concepts).

### <a name="create-a-container"></a>Crear un contenedor
Todos los blobs del almacenamiento de Azure han de estar en un contenedor. Puede crear un contenedor con el comando `az storage container create`:

```azurecli
az storage container create --name <container_name>
```

Puede establecer uno de los tres niveles de acceso de lectura para un nuevo contenedor especificando el argumento  opcional `--public-access`:

* `off` (predeterminado): los datos del contenedor son privados (del dueño de la cuenta).
* `blob`: acceso de lectura público solo para blobs.
* `container`: acceso de lectura y lista públicos para todo el contenedor.

Para más información, consulte [Administración del acceso de lectura anónimo a contenedores y blobs](../blobs/storage-manage-access-to-resources.md).

### <a name="upload-a-blob-to-a-container"></a>Carga de un blob en un contenedor
Azure Blob Storage admite blobs de bloque, anexados y en páginas. Puede cargar blobs en un contenedor con el comando `blob upload`:

```azurecli
az storage blob upload \
    --file <local_file_path> \
    --container-name <container_name> \
    --name <blob_name>
```

 De manera predeterminada, el comando `blob upload` carga archivos *.vhd blobs en páginas o blobs en bloques en caso contrario. Para especificar otro tipo al cargar un blob, puede usar el argumento `--type`; los valores permitidos son `append`, `block` y `page`.

 Para obtener más información sobre los distintos tipos de blobs, consulte [Understanding Block Blobs, Append Blobs, and Page Blobs](/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs) (Descripción de los blobs en bloques, en anexos y en páginas).


### <a name="download-a-blob-from-a-container"></a>Descarga de un blob de un contenedor
En el siguiente ejemplo se muestra cómo descargar blobs de un contenedor:

```azurecli
az storage blob download \
    --container-name mycontainer \
    --name myblob.png \
    --file ~/mydownloadedblob.png
```

### <a name="list-the-blobs-in-a-container"></a>Enumerar los blobs de un contenedor

Enumere los blobs de un contenedor con el comando [az storage blob list](/cli/azure/storage/blob#list).

```azurecli
az storage blob list \
    --container-name mycontainer \
    --output table
```

### <a name="copy-blobs"></a>Copia de blobs
Los blobs se pueden copiar dentro de las cuentas de almacenamiento y regiones, o entre ellas, de forma asincrónica.

En el siguiente ejemplo se muestra cómo copiar blobs de una cuenta de almacenamiento a otra. Primero creamos un contenedor en la cuenta de almacenamiento de origen, especificando el acceso de lectura público para sus blobs. Después cargamos un archivo en el contenedor y, por último, copiamos el blob del contenedor en un contenedor de la cuenta de almacenamiento de destino.

```azurecli
# Create container in source account
az storage container create \
    --account-name sourceaccountname \
    --account-key sourceaccountkey \
    --name sourcecontainer \
    --public-access blob

# Upload blob to container in source account
az storage blob upload \
    --account-name sourceaccountname \
    --account-key sourceaccountkey \
    --container-name sourcecontainer \
    --file ~/Pictures/sourcefile.png \
    --name sourcefile.png

# Copy blob from source account to destination account (destcontainer must exist)
az storage blob copy start \
    --account-name destaccountname \
    --account-key destaccountkey \
    --destination-blob destfile.png \
    --destination-container destcontainer \
    --source-uri https://sourceaccountname.blob.core.windows.net/sourcecontainer/sourcefile.png
```

En el ejemplo anterior, el contenedor de destino ya debe existir en la cuenta de almacenamiento de destino para que la operación de copia se realice correctamente. Además, el blob de origen especificado en el argumento `--source-uri` debe incluir un token de firma de acceso compartido (SAS), o ser accesible públicamente, como en este ejemplo.

### <a name="delete-a-blob"></a>Eliminar un blob
Para eliminar un blob, use el comando `blob delete`:

```azurecli
az storage blob delete --container-name <container_name> --name <blob_name>
```

## <a name="create-and-manage-file-shares"></a>Creación y administración de recursos compartidos de archivos
Azure Files ofrece almacenamiento compartido para aplicaciones que usan el protocolo Bloque de mensajes del servidor (SMB). Los servicios en la nube y las máquinas virtuales de Microsoft Azure, así como las aplicaciones locales, pueden compartir datos de archivos a través de recursos compartidos montados. Los recursos compartidos de archivos y datos de archivos se pueden administrar a través de la CLI de Azure. Para obtener más información sobre Azure Files, consulte [Introducción a Azure Files](../files/storage-files-introduction.md).

### <a name="create-a-file-share"></a>Creación de un recurso compartido de archivos
Un recurso compartido de archivos de Azure es un recurso compartido de archivos de SMB en Azure. Todos los directorios y archivos se deben crear en un recurso compartido de archivos. Una cuenta puede contener un número ilimitado de recursos compartidos y un recurso compartido puede almacenar un número ilimitado de archivos, hasta los límites de capacidad de la cuenta de almacenamiento. En el siguiente ejemplo se crea un recurso compartido de archivos denominado **myshare**.

```azurecli
az storage share create --name myshare
```

### <a name="create-a-directory"></a>Creación de directorios
Un directorio proporciona una estructura jerárquica para los recursos compartidos de archivos de Azure. En el ejemplo siguiente se crea el directorio **myDir** en el recurso compartido de archivos.

```azurecli
az storage directory create --name myDir --share-name myshare
```

La ruta de acceso al directorio puede incluir varios niveles, por ejemplo, **dir1/dir2**. Pero debe asegurarse de que existen todos los directorios principales antes de crear un subdirectorio. Por ejemplo, para la ruta de acceso **dir1/dir2**, primero se debe crear el directorio **dir1** y luego el directorio **dir2**.

### <a name="upload-a-local-file-to-a-share"></a>Carga de un archivo local a un recurso compartido
El siguiente ejemplo carga un archivo de **~/temp/samplefile.txt** en el raíz del recurso compartido de archivos **recurso**. El argumento `--source` especifica el archivo local existente para cargar.

```azurecli
az storage file upload --share-name myshare --source ~/temp/samplefile.txt
```

Al igual que sucede con la creación de directorios, puede especificar una ruta de directorio dentro del recurso compartido para cargar el archivo a un directorio existente dentro del recurso compartido:

```azurecli
az storage file upload --share-name myshare/myDir --source ~/temp/samplefile.txt
```

Los archivos del recurso compartido pueden tener un tamaño máximo de 1 TB.

### <a name="list-the-files-in-a-share"></a>Enumeración de los archivos en un recurso compartido
Puede enumerar archivos y directorios en un recurso compartido mediante el comando `az storage file list`:

```azurecli
# List the files in the root of a share
az storage file list --share-name myshare --output table

# List the files in a directory within a share
az storage file list --share-name myshare/myDir --output table

# List the files in a path within a share
az storage file list --share-name myshare --path myDir/mySubDir/MySubDir2 --output table
```

### <a name="copy-files"></a>Copiar archivos      
Puede copiar un archivo en otro, un archivo en un blob o un blob en un archivo. Por ejemplo, para copiar un archivo en un directorio de un recurso compartido diferente:        
        
```azurecli
az storage file copy start \
--source-share share1 --source-path dir1/file.txt \
--destination-share share2 --destination-path dir2/file.txt     
```

## <a name="create-share-snapshot"></a>Creación de una instantánea de recurso compartido
Puede crear una instantánea del recurso compartido con el comando `az storage share snapshot`:

```cli
az storage share snapshot -n <share name>
```

Salida de ejemplo
```json
{
  "metadata": {},
  "name": "<share name>",
  "properties": {
    "etag": "\"0x8D50B7F9A8D7F30\"",
    "lastModified": "2017-10-04T23:28:22+00:00",
    "quota": null
  },
  "snapshot": "2017-10-04T23:28:35.0000000Z"
}
```

### <a name="list-share-snapshots"></a>Enumerar instantáneas del recurso compartido

Puede enumerar las instantáneas del recurso compartido de un recurso compartido determinado mediante `az storage share list --include-snapshots`

```cli
az storage share list --include-snapshots
```

**Salida de ejemplo**
```json
[
  {
    "metadata": null,
    "name": "sharesnapshotdefs",
    "properties": {
      "etag": "\"0x8D50B5F4005C975\"",
      "lastModified": "2017-10-04T19:36:46+00:00",
      "quota": 5120
    },
    "snapshot": "2017-10-04T19:44:13.0000000Z"
  },
  {
    "metadata": null,
    "name": "sharesnapshotdefs",
    "properties": {
      "etag": "\"0x8D50B5F4005C975\"",
      "lastModified": "2017-10-04T19:36:46+00:00",
      "quota": 5120
    },
    "snapshot": "2017-10-04T19:45:18.0000000Z"
  },
  {
    "metadata": null,
    "name": "sharesnapshotdefs",
    "properties": {
      "etag": "\"0x8D50B5F4005C975\"",
      "lastModified": "2017-10-04T19:36:46+00:00",
      "quota": 5120
    },
    "snapshot": null
  }
]
```

### <a name="browse-share-snapshots"></a>Examen de las instantáneas de recurso compartido
También puede examinar una instantánea del recurso compartido determinada para ver su contenido mediante `az storage file list`. Uno debe especificar el nombre del recurso compartido `--share-name <snare name>` y la marca de tiempo `--snapshot '2017-10-04T19:45:18.0000000Z'`

```azurecli-interactive
az storage file list --share-name sharesnapshotdefs --snapshot '2017-10-04T19:45:18.0000000Z' -otable
```

**Salida de ejemplo**
```
Name            Content Length    Type    Last Modified
--------------  ----------------  ------  ---------------
HelloWorldDir/                    dir
IMG_0966.JPG    533568            file
IMG_1105.JPG    717711            file
IMG_1341.JPG    608459            file
IMG_1405.JPG    652156            file
IMG_1611.JPG    442671            file
IMG_1634.JPG    1495999           file
IMG_1635.JPG    974058            file

```
### <a name="restore-from-share-snapshots"></a>Restauración desde instantáneas de recurso compartido

Puede restaurar un archivo copiando o descargando un archivo desde una instantánea del recurso compartido mediante el comando `az storage file download`

```azurecli-interactive
az storage file download --path IMG_0966.JPG --share-name sharesnapshotdefs --snapshot '2017-10-04T19:45:18.0000000Z'
```
**Salida de ejemplo**
```
{
  "content": null,
  "metadata": {},
  "name": "IMG_0966.JPG",
  "properties": {
    "contentLength": 533568,
    "contentRange": "bytes 0-533567/533568",
    "contentSettings": {
      "cacheControl": null,
      "contentDisposition": null,
      "contentEncoding": null,
      "contentLanguage": null,
      "contentType": "application/octet-stream"
    },
    "copy": {
      "completionTime": null,
      "id": null,
      "progress": null,
      "source": null,
      "status": null,
      "statusDescription": null
    },
    "etag": "\"0x8D50B5F49F7ACDF\"",
    "lastModified": "2017-10-04T19:37:03+00:00",
    "serverEncrypted": true
  }
}
```
## <a name="delete-share-snapshot"></a>Eliminación de una instantánea de recurso compartido
Puede eliminar una instantánea del recurso compartido mediante el comando `az storage share delete` proporcionando el parámetro `--snapshot` con la marca de tiempo de la instantánea del recurso compartido:

```cli
az storage share delete -n <share name> --snapshot '2017-10-04T23:28:35.0000000Z' 
```

Salida de ejemplo
```json
{
  "deleted": true
}
```

## <a name="next-steps"></a>pasos siguientes
Estos son algunos recursos adicionales para obtener más información acerca de cómo trabajar con la CLI de Azure 2.0.

* [Introducción a la CLI de Azure 2.0](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2)
* [Referencia de comandos de la CLI de Azure 2.0](/cli/azure)
* [CLI de Azure 2.0 en GitHub](https://github.com/Azure/azure-cli)
