---
title: "Realización de operaciones en Azure Blob Storage con la CLI de Azure | Microsoft Docs"
description: "Aprenda a cargar y descargar blobs en Azure Blob Storage, así como a crear una firma de acceso compartido (SAS) para administrar el acceso a un blob en la cuenta de almacenamiento."
services: storage
documentationcenter: na
author: tamram
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 06/15/2017
ms.author: tamram
ms.openlocfilehash: bd96cf7eb1c0c7f51b110da848a8df7914ad85c7
ms.sourcegitcommit: c4cc4d76932b059f8c2657081577412e8f405478
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/11/2018
---
# <a name="perform-blob-storage-operations-with-azure-cli"></a>Realización de operaciones de Blob Storage con la CLI de Azure

El Almacenamiento de blobs de Azure es un servicio para almacenar grandes cantidades de datos de objetos no estructurados, como texto o datos binarios, a los que puede acceder desde cualquier lugar del mundo a través de HTTP o HTTPS. Este tutorial trata las operaciones básicas en Azure Blob Storage como la carga, descarga y eliminación de blobs. Aprenderá a:

> [!div class="checklist"]
> * Crear un contenedor
> * Cargar un archivo (blob) en un contenedor
> * Enumerar los blobs de un contenedor
> * Descarga de un blob de un contenedor
> * Copiar un blob entre cuentas de almacenamiento
> * Eliminar un blob
> * Ver y editar propiedades y metadatos de blob
> * Administrar la seguridad con una firma de acceso compartido (SAS)

Para realizar este tutorial es necesaria la versión 2.0.4 o superior de la CLI de Azure. Ejecute `az --version` para encontrar la versión. Si necesita instalarla o actualizarla, consulte [Instalación de la CLI de Azure 2.0](/cli/azure/install-azure-cli). 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [storage-quickstart-tutorial-intro-include-cli](../../../includes/storage-quickstart-tutorial-intro-include-cli.md)]

## <a name="create-a-container"></a>Crear un contenedor

Los contenedores son similares a los directorios en el equipo, gracias a ellos puede organizar los grupos de blobs en un contenedor de la misma forma que organiza los archivos en un directorio. Una cuenta de almacenamiento puede tener cualquier número de contenedores. Puede almacenar hasta 500 TB de datos de blob en un contenedor, que es la cantidad máxima de datos en una cuenta de almacenamiento.

Cree un contenedor para almacenar blobs con el comando [az storage container create](/cli/azure/storage/container#create).

```azurecli-interactive
az storage container create --name mystoragecontainer
```

Los nombres de contenedor tiene que comenzar por una letra o un número, y pueden contener solo letras, números y el carácter de guión (-). Para conocer más reglas sobre la nomenclatura de los contenedores de almacenamiento, consulte [Asignación de nombres y referencias a contenedores, blobs y metadatos](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

## <a name="enable-public-read-access-for-a-container"></a>Habilitación del acceso de lectura público para un contenedor

Un contenedor recién creado es privado de forma predeterminada. Es decir, nadie puede tener acceso al contenedor sin una [firma de acceso compartido](#create-a-shared-access-signature-sas) o las teclas de acceso para la cuenta de almacenamiento. Usando la CLI de Azure, puede modificar este comportamiento estableciendo permisos para el contenedor a uno de estos tres niveles:

| | |
|---|---|
| `--public-access off` | (Valor predeterminado) Sin acceso de lectura público |
| `--public-access blob` | Acceso de lectura público solo a los blobs |
| `--public-access container` | Acceso de lectura público a los blobs, puede enumerar blobs en el contenedor |

Al establecer acceso público a `blob` o `container`, se habilita el acceso de solo lectura para todos los usuarios de Internet. Por ejemplo, si desea mostrar imágenes almacenadas como blobs en su sitio web, tiene que habilitar el acceso de lectura público. Si desea habilitar el acceso de lectura/escritura, tiene que utilizar una [firma de acceso compartido (SAS)](#create-a-shared-access-signature-sas).

Habilite el acceso de lectura público para el contenedor con el comando [az storage container set-permission](/cli/azure/storage/container#create).

```azurecli-interactive
az storage container set-permission \
    --name mystoragecontainer \
    --public-access container
```

## <a name="upload-a-blob-to-a-container"></a>Carga de un blob en un contenedor

Blob Storage admite blobs en bloques, blobs en anexos y blobs en páginas. Los blobs en bloques son el tipo más común de blob almacenado en Azure Storage. Los blobs en anexos se usan cuando los datos tienen que agregarse a un blob existente sin modificar el contenido que ya tiene, como, por ejemplo, para realizar operaciones de registro. Los blobs en páginas respaldan los archivos VHD de máquinas virtuales IaaS.

En este ejemplo, se carga un blob en el contenedor que creamos en el último paso con el comando [az storage blob upload](/cli/azure/storage/blob#upload).

```azurecli-interactive
az storage blob upload \
    --container-name mystoragecontainer \
    --name blobName \
    --file ~/path/to/local/file
```

Esta operación crea el blob si todavía no existe y lo sobrescribe si ya existe. Cargue varios archivos para que pueda ver varias entradas al enumerar los blobs en el paso siguiente.

## <a name="list-the-blobs-in-a-container"></a>Enumerar los blobs de un contenedor

Enumere los blobs del contenedor con el comando [az storage blob list](/cli/azure/storage/blob#list).

```azurecli-interactive
az storage blob list \
    --container-name mystoragecontainer \
    --output table
```

Salida de ejemplo:

```
Name            Blob Type      Length  Content Type              Last Modified
--------------  -----------  --------  ------------------------  -------------------------
ISSUE_TEMPLATE  BlockBlob         761  application/octet-stream  2017-04-21T18:29:50+00:00
LICENSE         BlockBlob       18653  application/octet-stream  2017-04-21T18:28:50+00:00
LICENSE-CODE    BlockBlob        1090  application/octet-stream  2017-04-21T18:29:02+00:00
README.md       BlockBlob        6700  application/octet-stream  2017-04-21T18:27:33+00:00
dir1/file1.txt  BlockBlob        6700  application/octet-stream  2017-04-21T18:32:51+00:00
```

## <a name="download-a-blob"></a>Descarga de un blob

Descargue el blob que cargó en un paso anterior mediante el comando [az storage blob download](/cli/azure/storage/blob#download).

```azurecli-interactive
az storage blob download \
    --container-name mystoragecontainer \
    --name blobName \
    --file ~/destination/path/for/file
```

## <a name="copy-a-blob-between-storage-accounts"></a>Copiar un blob entre cuentas de almacenamiento

Los blobs se pueden copiar dentro de las cuentas de almacenamiento y regiones, o entre ellas, de forma asincrónica.

En el siguiente ejemplo se muestra cómo copiar blobs de una cuenta de almacenamiento a otra. Primero creamos un contenedor en la cuenta de almacenamiento de origen, especificando el acceso de lectura público para sus blobs. Después cargamos un archivo en el contenedor y, por último, copiamos el blob del contenedor en un contenedor de la cuenta de almacenamiento de destino.

En este ejemplo, el contenedor de destino tiene que existir ya en la cuenta de almacenamiento de destino para que la operación de copia se realice correctamente. Además, el blob de origen especificado en el argumento `--source-uri` debe incluir un token de firma de acceso compartido (SAS), o ser accesible públicamente, como en este ejemplo.

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
    --file ~/path/to/sourcefile \
    --name sourcefile

# Copy blob from source account to destination account (destcontainer must exist)
az storage blob copy start \
    --account-name destaccountname \
    --account-key destaccountkey \
    --destination-blob destfile.png \
    --destination-container destcontainer \
    --source-uri https://sourceaccountname.blob.core.windows.net/sourcecontainer/sourcefile.png
```

## <a name="delete-a-blob"></a>Eliminar un blob

Elimine el blob del contenedor mediante el comando [az storage blob delete](/cli/azure/storage/blob#delete).

```azurecli-interactive
az storage blob delete \
    --container-name mystoragecontainer \
    --name blobName
```

## <a name="set-the-content-type"></a>Establecimiento del tipo de contenido

El tipo de contenido, también conocido como el tipo MIME, identifica el formato de los datos en el blob. Los exploradores y otro software usan el tipo de contenido para determinar cómo procesar los datos. En el ejemplo siguiente se establece el tipo de contenido en `image/png`.

```azurecli-interactive
# Set the content type
az storage blob update
    --container-name mystoragecontainer 
    --name blobName 
    --content-type image/png
```

## <a name="display-and-modify-blob-properties-and-metadata"></a>Ver y editar propiedades y metadatos de blob

Cada blob tiene varias propiedades definidas por el servicio que puede mostrar con el comando [az storage blob show](/cli/azure/storage/blob#show), que incluye su nombre, tipo, longitud y otros. También puede configurar un blob con sus propias propiedades y sus valores mediante el comando [az storage blob metadata update](/cli/azure/storage/blob/metadata#update).

En este ejemplo, mostraremos primero las propiedades definidas por el servicio de un blob, y luego actualizaremos el blob con dos nuestras propias propiedades de metadatos. Por último, se muestran las propiedades de metadatos del blob y sus valores con el comando [az storage blob metadata show](/cli/azure/storage/blob/metadata#show).

```azurecli-interactive
# Show properties of a blob
az storage blob show \
    --container-name mystoragecontainer \
    --name blobName \
    --output table

# Set metadata properties of a blob (replaces all existing metadata)
az storage blob metadata update \
    --container-name mystoragecontainer \
    --name blobName \
    --metadata "key1=value1" "key2=value2"

# Show metadata properties of a blob
az storage blob metadata show \
    --container-name mystoragecontainer \
    --name blobName
```

## <a name="create-a-shared-access-signature-sas"></a>Creación de una firma de acceso compartido (SAS)

Las firmas de acceso compartido (SAS) ofrecen una manera de conceder acceso limitado a los objetos de la cuenta de almacenamiento sin exponer las claves de acceso de la misma. Para generar un URI que permita el acceso a un recurso privado, puede crear el token SAS con los permisos deseados y la ventana de validez (su duración efectiva), y anexarlo como una cadena de consulta a la dirección URL de un recurso, formando así su URI de SAS completo. Cualquier persona con este URI de SAS (la dirección URL del recurso y el token de SAS) puede tener acceso al recurso privado durante la ventana de validez del token SAS. Por ejemplo, puede que quiera permitir el acceso de lectura a un blob privado durante dos minutos para que alguien pueda verlo.

En los pasos siguientes, podrá deshabilitar acceso público al contenedor, probar el acceso de solo privados, y a continuación, generar y probar un URI de SAS.

### <a name="disable-container-public-access"></a>Deshabilitación del acceso público a contenedores

En primer lugar, establezca el nivel de acceso del contenedor en `off`. Esto especifica que no hay ningún acceso al contenedor o sus blobs sin una firma de acceso compartido o una clave de acceso de la cuenta de almacenamiento.

```azurecli-interactive
az storage container set-permission \
    --name mystoragecontainer \
    --public-access off
```

### <a name="verify-private-access"></a>Comprobación del acceso privado

Para comprobar que no hay ningún acceso de lectura público a los blobs en ese contenedor, obtenga la dirección URL para uno de sus blobs con el comando [az storage blob url](/cli/azure/storage/blob#url).

```azurecli-interactive
az storage blob url \
    --container-name mystoragecontainer \
    --name blobName \
    --output tsv
```

Navegue a la dirección URL del blob en una ventana privada del explorador. Se le presentará un error `ResourceNotFound` porque el blob es privado y no se ha incluido una firma de acceso compartido.

### <a name="create-a-sas-uri"></a>Creación de un URI de SAS

Ahora vamos a crear un URI de SAS que permita el acceso al blob. En el ejemplo siguiente, primero se rellena una variable con la dirección URL para el blob con [az storage blob url](/cli/azure/storage/blob#url), a continuación, se rellena otra variable con un token de SAS generado con el comando [az storage blob generate-sas](/cli/azure/storage/blob#generate-sas). Por último, se saca el URI de SAS completo para el blob mediante la concatenación de los dos, separados por el separador de cadena de consulta `?`.

```azurecli-interactive
# Get UTC datetimes for SAS start and expiry (Example: 1994-11-05T13:15:30Z)
sas_start=`date -u +'%Y-%m-%dT%H:%M:%SZ'`
sas_expiry=`date -u +'%Y-%m-%dT%H:%M:%SZ' -d '+2 minute'`

# Get the URL for the blob
blob_url=`az storage blob url \
    --container-name mystoragecontainer \
    --name blobName \
    --output tsv`

# Obtain a SAS token granting read (r) access between the
# SAS start and expiry times
sas_token=`az storage blob generate-sas \
    --container-name mystoragecontainer \
    --name blobName \
    --start $sas_start \
    --expiry $sas_expiry \
    --permissions r \
    --output tsv`

# Display the full SAS URI for the blob
echo $blob_url?$sas_token
```

### <a name="test-the-sas-uri"></a>Prueba del URI de SAS

En una ventana privada del explorador, navegue hasta el URI de SAS completo que se muestran con el comando `echo` en el fragmento de código anterior. Esta vez, no se le presentará un error y puede ver o descargar el blob.

Espere el tiempo suficiente para que la dirección URL expire (dos minutos en este ejemplo), y desplácese al URI en otra ventana privada del explorador. Verá ahora un error `AuthenticationFailed` porque el token de SAS ha expirado.

## <a name="clean-up-resources"></a>Limpieza de recursos

Si ya no necesita ninguno de los recursos del grupo de recursos, incluida la cuenta de almacenamiento que creó y cualquiera de los blobs que haya cargado en este tutorial, elimine el grupo de recursos con el comando [az group delete](/cli/azure/group#delete).

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>pasos siguientes

En este tutorial ha aprendido los conceptos básicos de cómo trabajar con blobs en Azure Storage:

> [!div class="checklist"]
> * Crear un contenedor
> * Cargar un archivo (blob) en un contenedor
> * Enumerar los blobs de un contenedor
> * Descarga de un blob de un contenedor
> * Copiar un blob entre cuentas de almacenamiento
> * Eliminar un blob
> * Ver y editar propiedades y metadatos de blob
> * Administrar la seguridad con una firma de acceso compartido (SAS)

Los siguientes recursos proporcionan información adicional acerca de cómo trabajar con la CLI de Azure y con los recursos de la cuenta de almacenamiento.

* CLI de Azure
  * [Inicie sesión con Azure CLI 2.0](/cli/azure/authenticate-azure-cli): obtenga información sobre los distintos métodos de autenticación con la CLI, incluido el inicio de sesión no interactivo a través de [entidad de servicio](/cli/azure/authenticate-azure-cli#logging-in-with-a-service-principal) para ejecutar scripts de la CLI de Azure desatendidos.
  * [Referencia de comandos de la CLI de Azure 2.0](/cli/azure/)
* Explorador de Microsoft Azure Storage
  * El [Explorador de Microsoft Azure Storage](../../vs-azure-tools-storage-manage-with-storage-explorer.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) es una aplicación independiente y gratuita de Microsoft que permite trabajar visualmente con los datos de Azure Storage en Windows, macOS y Linux.
