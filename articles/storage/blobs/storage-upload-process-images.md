---
title: Carga de datos de imagen en la nube con Azure Storage | Microsoft Docs
description: "Uso de Azure Blob Storage con una aplicación web para almacenar datos de aplicación"
services: storage
documentationcenter: 
author: georgewallace
manager: timlt
editor: 
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: csharp
ms.topic: tutorial
ms.date: 09/19/2017
ms.author: gwallace
ms.custom: mvc
ms.openlocfilehash: 8d187e51cbb391ee1f34fb5934c8ae1868bb6244
ms.sourcegitcommit: 3cdc82a5561abe564c318bd12986df63fc980a5a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/05/2018
---
# <a name="upload-image-data-in-the-cloud-with-azure-storage"></a>Carga de datos de imagen en la nube con Azure Storage

Este tutorial es la primera parte de una serie. En este tutorial se muestra cómo implementar una aplicación web que usa la biblioteca de cliente de Azure Storage para cargar imágenes en una cuenta de almacenamiento. Cuando haya terminado, tendrá una aplicación web que almacena y muestra imágenes desde Azure Storage.

![Vista del contenedor de imágenes](media/storage-upload-process-images/figure2.png)

En la primera parte de la serie, se aprende a:

> [!div class="checklist"]
> * Crear una cuenta de almacenamiento
> * Crear un contenedor y establecer permisos
> * Recuperar una clave de acceso
> * Configuración de la aplicación
> * Implementar una aplicación web en Azure
> * Interactuar con la aplicación web

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Si decide instalar y usar la CLI localmente, para este tutorial es preciso que ejecute la CLI de Azure versión 2.0.4 o posterior. Ejecute `az --version` para encontrar la versión. Si necesita instalarla o actualizarla, consulte [Instalación de la CLI de Azure 2.0]( /cli/azure/install-azure-cli). 

## <a name="create-a-resource-group"></a>Crear un grupo de recursos 

Cree un grupo de recursos con el comando [az group create](/cli/azure/group#create). Un grupo de recursos de Azure es un contenedor lógico en el que se implementan y se administran los recursos de Azure.
 
En el ejemplo siguiente se crea un grupo de recursos denominado `myResourceGroup`.
 
```azurecli-interactive 
az group create --name myResourceGroup --location westcentralus 
``` 

## <a name="create-a-storage-account"></a>Crear una cuenta de almacenamiento
 
En el ejemplo se cargan imágenes en un contenedor de blobs en una cuenta de Azure Storage. Una cuenta de almacenamiento proporciona un espacio de nombres único para almacenar y tener acceso a los objetos de datos de almacenamiento de Azure. Cree una cuenta de almacenamiento en el grupo de recursos que ha creado con el comando [az storage account create](/cli/azure/storage/account#az_storage_account_create). 

> [!IMPORTANT] 
> En la parte 2 del tutorial, usará suscripciones de eventos en Blob Storage. Las suscripciones de eventos solo se admiten actualmente para cuentas de Blob Storage de Centro de EE. UU. y Oeste de EE. UU 2. Debido a esta restricción, debe crear una cuenta de Blob Storage que la aplicación de ejemplo use para almacenar imágenes y miniaturas.   

En el siguiente comando, sustituya su propio nombre único de manera global por la cuenta de Blob Storage donde vea el marcador de posición `<blob_storage_account>`.  

```azurecli-interactive 
az storage account create --name <blob_storage_account> \
--location westcentralus --resource-group myResourceGroup \
--sku Standard_LRS --kind blobstorage --access-tier hot 
``` 
 
## <a name="create-blob-storage-containers"></a>Creación de contenedores de almacenamiento de blobs
 
La aplicación usa dos contenedores en la cuenta de Blob Storage. Los contenedores son similares a carpetas y se usan para almacenar blobs. El contenedor de _imágenes_ es donde la aplicación carga imágenes a alta resolución. En una parte posterior de la serie, una aplicación de función de Azure carga imágenes en miniatura cambiadas de tamaño en el contenedor _thumbs_. 

Obtenga la clave de la cuenta de almacenamiento mediante el comando [az storage account keys list](/cli/azure/storage/account/keys#list). A continuación, use esta clave para crear dos contenedores mediante el comando [az storage container create](/cli/azure/storage/container#az_storage_container_create).  
 
En este caso, `<blob_storage_account>` es el nombre de la cuenta de Blob Storage que creó. El acceso público a los contenedores _images_ se establece en `off` y a los contenedores de _thumbs_ en `container`. la configuración de acceso público de `container` permite que las miniaturas sean visibles para las personas que visitan la página web.
 
```azurecli-interactive 
blobStorageAccount=<blob_storage_account>

blobStorageAccountKey=$(az storage account keys list -g myResourceGroup \
-n $blobStorageAccount --query [0].value --output tsv) 

az storage container create -n images --account-name $blobStorageAccount \
--account-key $blobStorageAccountKey --public-access off 

az storage container create -n thumbs --account-name $blobStorageAccount \
--account-key $blobStorageAccountKey --public-access container

echo "Make a note of your blob storage account key..." 
echo $blobStorageAccountKey 
``` 

Anote el nombre y la clave de su cuenta de almacenamiento de blobs. La aplicación de ejemplo usa esta configuración para conectarse a la cuenta de almacenamiento y cargar imágenes. 

## <a name="create-an-app-service-plan"></a>Creación de un plan de App Service 

Un [plan de App Service](../../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) especifica la ubicación, el tamaño y las características de la granja de servidores web que hospeda la aplicación. 

Cree un plan de App Service con el comando [az appservice plan create](/cli/azure/appservice/plan#az_appservice_plan_create). 

En el siguiente ejemplo se crea un plan de App Service denominado `myAppServicePlan` con el plan de tarifa **Gratis**: 

```azurecli-interactive 
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku FREE 
``` 

## <a name="create-a-web-app"></a>Creación de una aplicación web 

La aplicación web proporciona un espacio de hospedaje para el código de aplicación de ejemplo que se implementa desde el repositorio de ejemplo de GitHub. Cree una [aplicación web](../../app-service/app-service-web-overview.md) en el plan de App Service `myAppServicePlan` con el comando [az webapp create](/cli/azure/webapp#az_webapp_create).  
 
En el siguiente comando, reemplace `<web_app>` por un nombre único (los caracteres válidos son `a-z`, `0-9` y `-`). Si `<web_app>` no es único, recibirá el mensaje de error: _Ya existe un sitio web `<web_app>` con el nombre especificado._ La dirección URL predeterminada de la aplicación web es `https://<web_app>.azurewebsites.net`.  

```azurecli-interactive 
az webapp create --name <web_app> --resource-group myResourceGroup --plan myAppServicePlan 
``` 

## <a name="deploy-the-sample-app-from-the-github-repository"></a>Implementación de la aplicación de ejemplo desde el repositorio de GitHub 

App Service admite varias maneras de implementar contenido en una aplicación web. En este tutorial, se implementa la aplicación web desde un [repositorio de ejemplo público de GitHub](https://github.com/Azure-Samples/storage-blob-upload-from-webapp). Configure la implementación de GitHub local en la aplicación web con el comando [az webapp deployment source config](/cli/azure/webapp/deployment/source#az_webapp_deployment_source_config). Reemplace `<web_app>` por el nombre de la aplicación web que creó en el paso anterior.

El proyecto de ejemplo contiene una aplicación [ASP.NET MVC](https://www.asp.net/mvc) que acepta una imagen, la guarda en una cuenta de almacenamiento y la muestra desde un contenedor de miniaturas. La aplicación web usa los espacios de nombres [Microsoft.WindowsAzure.Storage](/dotnet/api/microsoft.windowsazure.storage?view=azure-dotnet), [Microsoft.WindowsAzure.Storage.Blob](/dotnet/api/microsoft.windowsazure.storage.blob?view=azure-dotnet) y [Microsoft.WindowsAzure.Storage.Auth](/dotnet/api/microsoft.windowsazure.storage.auth?view=azure-dotnet) de la biblioteca de cliente de Azure Storage para interactuar con Azure Storage. 

```azurecli-interactive 
az webapp deployment source config --name <web_app> \
--resource-group myResourceGroup --branch master --manual-integration \
--repo-url https://github.com/Azure-Samples/storage-blob-upload-from-webapp
``` 

## <a name="configure-web-app-settings"></a>Configurar aplicaciones web 

La aplicación web de ejemplo usa la [biblioteca de cliente de Azure Storage](/dotnet/api/overview/azure/storage?view=azure-dotnet) para solicitar tokens de acceso, que se usan para cargar imágenes. Las credenciales de cuenta de almacenamiento que usa el SDK de Storage se establecen en la configuración de aplicación de la aplicación web. Agregue la configuración de aplicación a la aplicación implementada con el comando [az webapp config appsettings set](/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_set). 

En el siguiente comando, `<blob_storage_account>` es el nombre de la cuenta de Blob Storage y `<blob_storage_key>` es la clave asociada. Reemplace `<web_app>` por el nombre de la aplicación web que creó en el paso anterior.     

```azurecli-interactive 
az webapp config appsettings set --name <web_app> --resource-group myResourceGroup \
--settings AzureStorageConfig__AccountName=<blob_storage_account> \
AzureStorageConfig__ImageContainer=images  \
AzureStorageConfig__ThumbnailContainer=thumbs \
AzureStorageConfig__AccountKey=<blob_storage_key>  
``` 

Una vez que la aplicación web está implementada y configurada, puede probar la funcionalidad de carga de imágenes en la aplicación.   

## <a name="upload-an-image"></a>Carga de una imagen 

Para probar la aplicación web, vaya a la dirección URL de la aplicación publicada. La dirección URL predeterminada de la aplicación web es `https://<web_app>.azurewebsites.net`. Seleccione la región **Upload photos** (Cargar fotos) para seleccionar y cargar un archivo o arrastre y coloque un archivo en la región. La imagen desaparece si se carga correctamente.

![Aplicación ImageResizer](media/storage-upload-process-images/figure1.png)

En el código de ejemplo, la tarea `UploadFiletoStorage` del archivo `Storagehelper.cs` se usa para cargar las imágenes en el contenedor `images` en la cuenta de almacenamiento mediante el método [UploadFromStreamAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob.uploadfromstreamasync?view=azure-dotnet). El siguiente código de ejemplo contiene la tarea `UploadFiletoStorage`. 

```csharp
public static async Task<bool> UploadFileToStorage(Stream fileStream, string fileName, AzureStorageConfig _storageConfig)
{
    // Create storagecredentials object by reading the values from the configuration (appsettings.json)
    StorageCredentials storageCredentials = new StorageCredentials(_storageConfig.AccountName, _storageConfig.AccountKey);

    // Create cloudstorage account by passing the storagecredentials
    CloudStorageAccount storageAccount = new CloudStorageAccount(storageCredentials, true);

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Get reference to the blob container by passing the name by reading the value from the configuration (appsettings.json)
    CloudBlobContainer container = blobClient.GetContainerReference(_storageConfig.ImageContainer);

    // Get the reference to the block blob from the container
    CloudBlockBlob blockBlob = container.GetBlockBlobReference(fileName);

    // Upload the file
    await blockBlob.UploadFromStreamAsync(fileStream);

    return await Task.FromResult(true);
}
```

En la tarea anterior se usan las clases y los métodos siguientes:

|Clase  |Método  |
|---------|---------|
|[StorageCredentials](/dotnet/api/microsoft.windowsazure.storage.auth.storagecredentials?view=azure-dotnet)     |         |
|[CloudStorageAccount](/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount?view=azure-dotnet)    |  [CreateCloudBlobClient](/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount.createcloudblobclient?view=azure-dotnet#Microsoft_WindowsAzure_Storage_CloudStorageAccount_CreateCloudBlobClient)       |
|[CloudBlobClient](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobclient?view=azure-dotnet)     |[GetContainerReference](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobclient.getcontainerreference?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_CloudBlobClient_GetContainerReference_System_String_)         |
|[CloudBlobContainer](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer?view=azure-dotnet)    | [GetBlockBlobReference](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.getblockblobreference?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_CloudBlobContainer_GetBlockBlobReference_System_String_)        |
|[CloudBlockBlob](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob?view=azure-dotnet)     | [UploadFromStreamAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob.uploadfromstreamasync?view=azure-dotnet)        |

## <a name="verify-the-image-is-shown-in-the-storage-account"></a>Comprobación de que la imagen se muestra en la cuenta de almacenamiento

Inicie sesión en el [Azure Portal](https://portal.azure.com). En el menú izquierdo, seleccione **Cuentas de almacenamiento** y seleccione el nombre de la cuenta de almacenamiento. En **Introducción**, seleccione el contenedor **images**.

Compruebe que la imagen se muestra en el contenedor.

![Vista del contenedor de imágenes](media/storage-upload-process-images/figure13.png)

## <a name="test-thumbnail-viewing"></a>Prueba de la vista de miniaturas

Para probar la vista de miniaturas, cargará una imagen en el contenedor de miniaturas para garantizar que la aplicación puede leer dicho contenedor.

Inicie sesión en el [Azure Portal](https://portal.azure.com). En el menú izquierdo, seleccione **Cuentas de almacenamiento** y seleccione el nombre de la cuenta de almacenamiento. Seleccione **Contenedores** en **Blob service** y seleccione el contenedor **thumbs**. Seleccione **Cargar** para abrir el panel **Cargar blob**.

Elija un archivo mediante el selector de archivos y seleccione **Cargar**.

Vuelva a la aplicación para comprobar que la imagen cargada en el contenedor **thumbs** está visible.

![Vista del contenedor de imágenes](media/storage-upload-process-images/figure2.png)

En el contenedor **thumbs** de Azure Portal, seleccione la imagen que descargó y elija **Eliminar** para eliminar la imagen. En la parte dos de la serie, automatizará la creación de las imágenes en miniatura, así que esta imagen de prueba no es necesaria.

CDN puede habilitarse para almacenar en caché el contenido desde la cuenta de almacenamiento de Azure. Aunque no se describe en este tutorial, para aprender a habilitar la red CDN con la cuenta de almacenamiento de Azure, puede visitar [Integración de una cuenta de Azure Storage con la red CDN de Azure](../../cdn/cdn-create-a-storage-account-with-cdn.md).

## <a name="next-steps"></a>pasos siguientes

En la parte uno de la serie, aprendió cómo configurar una aplicación web mediante la interacción con el almacenamiento. Por ejemplo, a:

> [!div class="checklist"]
> * Crear una cuenta de almacenamiento
> * Crear un contenedor y establecer permisos
> * Recuperar una clave de acceso
> * Configuración de la aplicación
> * Implementar una aplicación web en Azure
> * Interactuar con la aplicación web

Siga con la parte dos de la serie para aprender a usar Event Grid para desencadenar una función de Azure que cambie el tamaño de una imagen.

> [!div class="nextstepaction"]
> [Uso de Event Grid para desencadenar una función de Azure Function que cambie de tamaño una imagen cargada](../../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
