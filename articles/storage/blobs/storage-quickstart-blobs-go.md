---
title: "Inicio rápido de Azure: Carga, descarga y enumeración de blobs en Azure Storage mediante Go | Microsoft Docs"
description: "En esta guía de inicio rápido, puede crear una cuenta de almacenamiento y un contenedor. Después, puede usar la biblioteca de clientes de almacenamiento para Go a fin de cargar un blob en Azure Storage, descargar un blob o enumerar los blobs de un contenedor."
services: storage
author: seguler
manager: jeconnoc
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 02/22/2018
ms.author: seguler
ms.openlocfilehash: a30d012ea663b04028a4b0ebb2880532a596500b
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/24/2018
---
# <a name="quickstart-upload-download-and-list-blobs-using-go"></a>Inicio rápido: Carga, descarga y enumeración de blobs mediante Go

En esta guía de inicio rápido, aprenderá a usar el lenguaje de programación Go para cargar, descargar y enumerar blobs en bloques en un contenedor en Azure Blob Storage. 

## <a name="prerequisites"></a>requisitos previos

Para completar esta guía de inicio rápido: 
* Instalación de [Go 1.8 o posterior](https://golang.org/dl/)
* Descargue e instale el [SDK de Azure Storage Blob para Go](https://github.com/azure/azure-storage-blob-go/) mediante `go get -u github.com/azure/azure-storage-blob-go/2016-05-31/azblob`. 

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

[!INCLUDE [storage-quickstart-tutorial-create-account-portal](../../../includes/storage-quickstart-tutorial-create-account-portal.md)]

## <a name="download-the-sample-application"></a>Descarga de la aplicación de ejemplo
La [aplicación de ejemplo](https://github.com/Azure-Samples/storage-blobs-go-quickstart.git) utilizada en esta guía de inicio rápido es una aplicación Go básica.  

Use [git](https://git-scm.com/) para descargar una copia de la aplicación en su entorno de desarrollo. 

```bash
git clone https://github.com/Azure-Samples/storage-blobs-go-quickstart 
```

Este comando clona el repositorio en la carpeta git local. Para abrir el ejemplo de Go para Blob Storage, busque storage-quickstart.go.  

## <a name="configure-your-storage-connection-string"></a>Configuración de la cadena de conexión de almacenamiento.
Esta solución requiere que el nombre y la clave de la cuenta de almacenamiento estén guardadas de forma segura en variables de entorno locales de la máquina en la que se ejecuta el programa de ejemplo. Siga uno de estos ejemplos dependiendo de su sistema operativo para crear las variables de entorno.

# <a name="linuxtablinux"></a>[Linux](#tab/Linux)

```
export AZURE_STORAGE_ACCOUNT="<youraccountname>"
export AZURE_STORAGE_ACCESS_KEY="<youraccountkey>"
```

# <a name="windowstabwindows"></a>[Windows](#tab/Windows)

```
setx AZURE_STORAGE_ACCOUNT "<youraccountname>"
setx AZURE_STORAGE_ACCESS_KEY "<youraccountkey>"
```

---

## <a name="run-the-sample"></a>Ejecución del ejemplo
El programa de ejemplo crea un archivo de prueba en la carpeta actual, lo carga en Blob Storage, enumera los blobs en el contenedor y descarga el archivo en un búfer. 

Para ejecutar el ejemplo, use el siguiente comando: 

```go run storage-quickstart.go```

La salida siguiente es un ejemplo de la salida devuelta al ejecutar la aplicación:
  
```
Azure Blob storage quick start sample
Creating a container named quickstart-5568059279520899415
Creating a dummy file to test the upload and download
Uploading the file with blob name: 630910657703031215
Blob name: 630910657703031215
Downloaded the blob: hello world
this is a blob
Press the enter key to delete the sample files, example container, and exit the application.
```
Al presionar la tecla para continuar, el programa de ejemplo elimina el contenedor de almacenamiento y los archivos. 

> [!TIP]
> También puede usar una herramienta como [Explorador de Azure Storage](http://storageexplorer.com) para ver los archivos de Blob Storage. El Explorador de Azure Storage es una herramienta gratuita multiplataforma que permite acceder a la información de la cuenta de almacenamiento. 
>

## <a name="understand-the-sample-code"></a>Descripción del código de ejemplo

A continuación, explicaremos el código de ejemplo para ayudarle a comprender saber cómo funciona.

### <a name="create-containerurl-and-bloburl-objects"></a>Creación de objetos ContainerURL y BlobURL
Lo primero que hay que hacer es crear las referencias a los objetos ContainerURL y BlobURL usados para acceder a Blob Storage y administrarlo. Estos objetos ofrecen API de bajo nivel, como Create, PutBlob y GetBlob para emitir API de REST.

* Use la estructura **SharedKeyCredential** para almacenar sus credenciales. 

* Cree una **canalización** usando las credenciales y las opciones. La canalización especifica elementos tales como directivas de reintentos, el registro, la deserialización de cargas de respuesta HTTP y mucho más.  

* Cree instancias de un nuevo objeto ContainerURL y un nuevo objeto BlobURL para ejecutar operaciones en un contenedor (Create) y en blobs (PutBlob y GetBlob).


Una vez que disponga del objeto ContainerURL, puede crear una instancia del objeto **BlobURL** que apunte a un blob y realizar operaciones como cargar, descargar y copiar.

> [!IMPORTANT]
> Los nombres de contenedor deben estar en minúsculas. Para más información sobre la nomenclatura de contenedores y blobs, consulte [Naming and Referencing Containers, Blobs, and Metadata](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) (Asignación de nombres y referencia a contenedores, blobs y metadatos).

En esta sección, creará un nuevo contenedor. El contenedor se denomina **quickstartblobs-[random string]**. 

```go 
// From the Azure portal, get your storage account name and key and set environment variables.
accountName, accountKey := os.Getenv("AZURE_STORAGE_ACCOUNT"), os.Getenv("AZURE_STORAGE_ACCESS_KEY")
if len(accountName) == 0 || len(accountKey) == 0 {
    log.Fatal("Either the AZURE_STORAGE_ACCOUNT or AZURE_STORAGE_ACCESS_KEY environment variable is not set")
}

// Create a default request pipeline using your storage account name and account key.
credential := azblob.NewSharedKeyCredential(accountName, accountKey)
p := azblob.NewPipeline(credential, azblob.PipelineOptions{})

// Create a random string for the quick start container
containerName := fmt.Sprintf("quickstart-%s", randomString())

// From the Azure portal, get your storage account blob service URL endpoint.
URL, _ := url.Parse(
    fmt.Sprintf("https://%s.blob.core.windows.net/%s", accountName, containerName))

// Create a ContainerURL object that wraps the container URL and a request
// pipeline to make requests.
containerURL := azblob.NewContainerURL(*URL, p)

// Create the container
fmt.Printf("Creating a container named %s\n", containerName)
ctx := context.Background() // This example uses a never-expiring context
_, err := containerURL.Create(ctx, azblob.Metadata{}, azblob.PublicAccessNone)
handleErrors(err)
```
### <a name="upload-blobs-to-the-container"></a>Carga de blobs al contenedor

Blob Storage admite blobs en bloques, blobs en anexos y blobs en páginas. Los blobs en bloques son los que se usan con más frecuencia y serán los que utilicemos en este tutorial de inicio rápido.  

Para cargar un archivo en un blob, abra el archivo con **os.Open**. A continuación, puede cargar el archivo en la ruta de acceso especificada con una de las API de REST: PutBlob, PutBlock/PutBlockList. 

Como alternativa, el SDK ofrece [API de alto nivel](https://github.com/Azure/azure-storage-blob-go/blob/master/2016-05-31/azblob/highlevel.go) que se basan en las API de REST de bajo nivel. Por ejemplo, la función ***UploadFileToBlockBlob*** utiliza las operaciones de PutBlock para cargar simultáneamente un archivo en fragmentos para optimizar el rendimiento. Si el archivo es inferior a 256 MB, usa PutBlob para completar la transferencia en una sola transacción.

En el ejemplo siguiente se carga el archivo en el contenedor denominado **quickstartblobs-[randomstring]**.

```go
// Here's how to upload a blob.
blobURL := containerURL.NewBlockBlobURL(fileName)
file, err := os.Open(fileName)
handleErrors(err)

// You can use the low-level PutBlob API to upload files. Low-level APIs are simple wrappers for the Azure Storage REST APIs.
// Note that PutBlob can upload up to 256MB data in one shot. Details: https://docs.microsoft.com/en-us/rest/api/storageservices/put-blob
// Following is commented out intentionally because we will instead use UploadFileToBlockBlob API to upload the blob
// _, err = blobURL.PutBlob(ctx, file, azblob.BlobHTTPHeaders{}, azblob.Metadata{}, azblob.BlobAccessConditions{})
// handleErrors(err)

// The high-level API UploadFileToBlockBlob function uploads blocks in parallel for optimal performance, and can handle large files as well.
// This function calls PutBlock/PutBlockList for files larger 256 MBs, and calls PutBlob for any file smaller
fmt.Printf("Uploading the file with blob name: %s\n", fileName)
_, err = azblob.UploadFileToBlockBlob(ctx, file, blobURL, azblob.UploadToBlockBlobOptions{
    BlockSize:   4 * 1024 * 1024,
    Parallelism: 16})
handleErrors(err)
```

### <a name="list-the-blobs-in-a-container"></a>Enumerar los blobs de un contenedor

Obtenga una lista de archivos del contenedor con el método **ListBlobs** en un objeto **ContainerURL**. ListBlobs devuelve un único segmento de blobs (hasta 5000) a partir del **marcador** especificado. Utilice un marcador vacío para iniciar la enumeración desde el principio. Los nombres de blobs se devuelven en orden lexicográfico. Después de obtener un segmento, procéselo y, a continuación, llame a ListBlobs de nuevo pasando el marcador devuelto anteriormente.  

```go
// List the blobs in the container
for marker := (azblob.Marker{}); marker.NotDone(); {
    // Get a result segment starting with the blob indicated by the current Marker.
    listBlob, err := containerURL.ListBlobs(ctx, marker, azblob.ListBlobsOptions{})
    handleErrors(err)

    // ListBlobs returns the start of the next segment; you MUST use this to get
    // the next segment (after processing the current result segment).
    marker = listBlob.NextMarker

    // Process the blobs returned in this result segment (if the segment is empty, the loop body won't execute)
    for _, blobInfo := range listBlob.Blobs.Blob {
        fmt.Print("Blob name: " + blobInfo.Name + "\n")
    }
}
```

### <a name="download-the-blob"></a>Descarga del blob

Descargue blobs con el método de bajo nivel **GetBlob** en un objeto BlobURL. También puede crear un flujo y leer rangos de él mediante la API de alto nivel **NewDownloadStream** que se proporciona en [highlevel.go](https://github.com/Azure/azure-storage-blob-go/blob/master/2016-05-31/azblob/highlevel.go). La función NewDownloadStream se reintenta si se produce un error de conexión, mientras que la API de GetBlob solo se reintenta con códigos de estado HTTP, como el código 503 (servidor ocupado). El código siguiente descarga el blob mediante la función **NewDownloadStream**. El contenido del blob se escribe en un búfer y se muestra en la consola.

```go
// Here's how to download the blob. NOTE: This method automatically retries if the connection fails
// during download (the low-level GetBlob function does NOT retry errors when reading from its stream).
stream := azblob.NewDownloadStream(ctx, blobURL.GetBlob, azblob.DownloadStreamOptions{})
downloadedData := &bytes.Buffer{}
_, err = downloadedData.ReadFrom(stream)
handleErrors(err)
```

### <a name="clean-up-resources"></a>Limpieza de recursos
Si ya no necesita los blobs cargados en esta guía de inicio rápido, puede eliminar todo el contenedor mediante el método **Delete**. 

```go
// Cleaning up the quick start by deleting the container and the file created locally
fmt.Printf("Press the enter key to delete the sample files, example container, and exit the application.\n")
bufio.NewReader(os.Stdin).ReadBytes('\n')
fmt.Printf("Cleaning up.\n")
containerURL.Delete(ctx, azblob.ContainerAccessConditions{})
file.Close()
os.Remove(fileName)
```

## <a name="next-steps"></a>pasos siguientes
 
En esta guía de inicio rápido, ha aprendido a transferir archivos entre un disco local y Azure Blob Storage mediante Go. Para más información sobre el SDK de Azure Storage Blob, vea el [código fuente](https://github.com/Azure/azure-storage-blob-go/) y la [referencia de la API](https://godoc.org/github.com/Azure/azure-storage-blob-go/2016-05-31/azblob).
