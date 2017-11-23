---
title: "Inicio rápido de Azure: transferencia de objetos a y desde Azure Blob Storage mediante Java | Microsoft Docs"
description: "Aprendizaje rápido para transferir objetos a y desde Azure Blob Storage mediante Java"
author: roygara
manager: timlt
services: storage
ms.service: storage
ms.topic: quickstart
ms.date: 11/01/2017
ms.author: v-rogara
ms.custom: mvc
ms.openlocfilehash: 5676cef446de7a68d3d8fd1a3b6833a5de184ea1
ms.sourcegitcommit: a036a565bca3e47187eefcaf3cc54e3b5af5b369
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2017
---
# <a name="transfer-objects-tofrom-azure-blob-storage-using-java"></a>Transferencia de objetos a y desde Azure Blob Storage mediante Java

En este tutorial de inicio rápido, aprenderá a usar Java para cargar, descargar y enumerar blobs en bloques en un contenedor en Azure Blob Storage.

## <a name="prerequisites"></a>Requisitos previos

Para completar esta guía de inicio rápido:

* Instale un IDE que tenga la integración de Maven.

* También puede instalar y configurar Maven para que funcione desde la línea de comandos.

En este tutorial se usa [Eclipse](http://www.eclipse.org/downloads/) con la configuración de "Eclipse IDE para desarrolladores de Java".

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

[!INCLUDE [storage-quickstart-tutorial-create-account-portal](../../../includes/storage-quickstart-tutorial-create-account-portal.md)]

## <a name="download-the-sample-application"></a>Descarga de la aplicación de ejemplo

La [aplicación de ejemplo](https://github.com/Azure-Samples/storage-blobs-java-quickstart) utilizada en este tutorial de inicio rápido es una aplicación de consola básica. 

Use [git](https://git-scm.com/) para descargar una copia de la aplicación en su entorno de desarrollo. 

```bash
git clone https://github.com/Azure-Samples/storage-blobs-java-quickstart.git
```

Este comando clona el repositorio en la carpeta git local. Para abrir el proyecto, inicie Eclipse y cierre la pantalla de bienvenida. Seleccione **File** (Archivo) y después **Open Projects from File System...** (Abrir proyectos desde el sistema de archivos...). Asegúrese de la casilla **Detect and configure project natures** (Detectar y configurar los tipos de proyecto) está activada. Seleccione **Directory** (Directorio), navegue hasta donde se ha almacenado el repositorio clonado y dentro de él seleccione la carpeta **javaBlobsQuickstart**. Asegúrese de que el proyecto **javaBlobsQuickstarts** aparece como un proyecto de Eclipse y, después, seleccione **Finish** (Finalizar).

Cuando se termine de importar el proyecto, abra **AzureApp.java** (ubicado en **blobQuickstart.blobAzureApp** dentro de **src/principal/java**) y reemplace `accountname` y `accountkey` dentro de la cadena `storageConnectionString`. Después, ejecute la aplicación.
     

## <a name="configure-your-storage-connection-string"></a>Configuración de la cadena de conexión de almacenamiento.
    
En la aplicación, debe proporcionar la cadena de conexión para la cuenta de almacenamiento. Abra el archivo **AzureApp.Java**. Encuentre la variable `storageConnectionString`. Reemplace los valores `AccountName` y `AccountKey` de la cadena de conexión por los valores que guardó en Azure Portal. El elemento `storageConnectionString` debe ser similar al siguiente:

```java
public static final String storageConnectionString =
"DefaultEndpointsProtocol=https;" +
"AccountName=<account-name>;" +
"AccountKey=<account-key>";
```

## <a name="run-the-sample"></a>Ejecución del ejemplo

En este ejemplo se crea un archivo de prueba en el directorio predeterminado (Mis documentos, para usuarios de Windows), se carga a Blob Storage, se enumeran los blobs del contenedor y luego se descarga el archivo con un nombre nuevo para poder comparar los archivos antiguos y nuevos. 

Ejecute el ejemplo presionando **Ctrl + F11** en Eclipse.

Si desea ejecutar el ejemplo mediante Maven en la línea de comandos, abra un shell y navegue hasta **blobAzureApp** dentro de su directorio clonado. A continuación, escriba `mvn compile exec:java`.

Lo siguiente es un ejemplo de salida si fuera a ejecutar la aplicación en Windows.

```
Azure Blob storage quick start sample
Creating container: quickstartcontainer
Creating a sample file at: C:\Users\<user>\AppData\Local\Temp\sampleFile514658495642546986.txt
Uploading the sample file 
URI of blob is: https://myexamplesacct.blob.core.windows.net/quickstartcontainer/sampleFile514658495642546986.txt
The program has completed successfully.
Press the 'Enter' key while in the console to delete the sample files, example container, and exit the application.

Deleting the container
Deleting the source, and downloaded files
```

 Antes de continuar, compruebe el directorio predeterminado (Mis documentos, para usuarios de Windows) de los dos archivos. Puede abrirlos y ver que son idénticas. Copie la dirección URL para el blob fuera de la ventana de consola y péguela en un explorador para ver el contenido del archivo de Blob Storage. Al presionar la tecla Entrar, se elimina el contenedor de almacenamiento y los archivos.

También puede usar una herramienta como [Explorador de Azure Storage](http://storageexplorer.com/?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) para ver los archivos de Blob Storage. El Explorador de Azure Storage es una herramienta gratuita multiplataforma que permite acceder a la información de la cuenta de almacenamiento. 

Después de haber comprobado los archivos, presione la tecla Entrar para finalizar la demostración y eliminar los archivos de prueba. Ahora que sabe lo que hace el ejemplo, abra el archivo **AzureApp.java** para examinar el código. 

## <a name="understand-the-sample-code"></a>Descripción del código de ejemplo

A continuación, explicaremos el código de ejemplo para ayudarle a comprender saber cómo funciona.

### <a name="get-references-to-the-storage-objects"></a>Obtención de referencias a los objetos de almacenamiento

Lo primero que hay que hacer es crear las referencias a los objetos usados para acceder a Blob Storage y administrarlo. Estos objetos dependen unos de otros: cada uno es utilizado por el siguiente de la lista.

* Cree una instancia del objeto **CloudStorageAccount** que apunte a la [cuenta de almacenamiento](/java/api/com.microsoft.azure.management.storage._storage_account).

    El objeto **CloudStorageAccount** es una representación de su cuenta de almacenamiento, que le permite establecer las propiedades de la cuenta de almacenamiento mediante programación, así como acceder a estas. Con el objeto **CloudStorageAccount** se puede crear una instancia del objeto **CloudBlobClient**, que es necesario para acceder a Blob service.

* Cree una instancia del objeto **CloudBlobClient**, que apunte a [Blob service](/java/api/com.microsoft.azure.storage.blob._cloud_blob_client) de la cuenta de almacenamiento.

    El objeto **CloudBlobClient** proporciona un punto de acceso a Blob service, lo que le permite establecer las propiedades de Blob Storage mediante programación, así como acceder a estas. Con el objeto **CloudBlobClient** se puede crear una instancia del objeto **CloudBlobContainer**, que es necesario para crear contenedores.

* Cree una instancia del objeto **CloudBlobContainer**, que representa el [contenedor](/java/api/com.microsoft.azure.storage.blob._cloud_blob_container) al que está accediendo. Los contenedores se usan para organizar los blobs al igual que se usan las carpetas en el equipo para organizar los archivos.    

    Una vez que tenga **CloudBlobContainer**, puede crear una instancia del objeto **CloudBlockBlob** que apunte al [blob](/java/api/com.microsoft.azure.storage.blob._cloud_block_blob) específico en el que está interesado y realizar una operación de carga, descarga, copia, etc.

> [!IMPORTANT]
> Los nombres de contenedor deben estar en minúsculas. Para más información sobre la nomenclatura de contenedores y blobs, consulte [Naming and Referencing Containers, Blobs, and Metadata](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) (Asignación de nombres y referencia a contenedores, blobs y metadatos).

### <a name="create-a-container"></a>Crear un contenedor 

En esta sección, se crean instancias de los objetos, se crea un nuevo contenedor y luego se establecen permisos en el contenedor para que los blobs sean públicos y se pueda acceder a ellos con tan solo una dirección URL. El contenedor se denomina **quickstartblobs**. 

En este ejemplo se utiliza **CreateIfNotExists** porque queremos crear un nuevo contenedor cada vez que se ejecuta el ejemplo. En un entorno de producción donde se usa el mismo contenedor en toda una aplicación, lo más recomendable es llamar solamente a **CreateIfNotExists** una vez. O bien se puede crear el contenedor con antelación para que no necesite crearlo en el código.

```java
// Parse the connection string and create a blob client to interact with Blob storage
storageAccount = CloudStorageAccount.parse(storageConnectionString);
blobClient = storageAccount.createCloudBlobClient();
container = blobClient.getContainerReference("quickstartcontainer");

// Create the container if it does not exist with public access.
System.out.println("Creating container: " + container.getName());
container.createIfNotExists(BlobContainerPublicAccessType.CONTAINER, new BlobRequestOptions(), new OperationContext());
```

### <a name="upload-blobs-to-the-container"></a>Carga de blobs al contenedor

Blob Storage admite blobs en bloques, blobs en anexos y blobs en páginas. Los blobs en bloques son los que se usan con más frecuencia y serán los que utilicemos en este tutorial de inicio rápido. 

Para cargar un archivo a un blob, obtenga una referencia al blob en el contenedor de destino. Cuando tenga la referencia de blob, puede cargar datos en él mediante [CloudBlockBlob.Upload](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._cloud_block_blob.upload#com_microsoft_azure_storage_blob__cloud_block_blob_upload_final_InputStream_final_long). Esta operación crea el blob si todavía no existe o lo sobrescribe si ya existe.

El código de ejemplo crea un archivo local que se usará para las operaciones de carga y descarga. El archivo que se va a cargar se guarda como **source** y el nombre del blob es **blob**. En el ejemplo siguiente se carga el archivo al contenedor denominado **quickstartblobs**.

```java
//Creating a sample file
sourceFile = File.createTempFile("sampleFile", ".txt");
System.out.println("Creating a sample file at: " + sourceFile.toString());
Writer output = new BufferedWriter(new FileWriter(sourceFile));
output.write("Hello Azure!");
output.close();

//Getting a blob reference
CloudBlockBlob blob = container.getBlockBlobReference(sourceFile.getName());

//Creating blob and uploading file to it
System.out.println("Uploading the sample file ");
blob.uploadFromFile(sourceFile.getAbsolutePath());
```

Existen varios [métodos de carga](/java/api/com.microsoft.azure.storage.blob._cloud_block_blob) que puede usar con Blob Storage. Por ejemplo, si tiene una cadena, puede usar el método UploadText en lugar de emplear el método Upload. 

Los blobs en bloques pueden ser cualquier tipo de archivo de texto o binario. Los blobs en páginas se utilizan principalmente para los archivos VHD usados para respaldar VM IaaS. Los blobs en anexos se utilizan para el registro, por ejemplo, cuando desea escribir en un archivo y luego sigue agregando más información. La mayoría de los objetos almacenados en Blob Storage son blobs en bloques.

### <a name="list-the-blobs-in-a-container"></a>Enumerar los blobs de un contenedor

Puede obtener una lista de archivos del contenedor mediante [CloudBlobContainer.ListBlobs](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._cloud_blob_container.listblobs#com_microsoft_azure_storage_blob__cloud_blob_container_listBlobs). El código siguiente recupera la lista de blobs, luego los recorre y se muestran los URI de los blobs encontrados. Puede copiar el URI desde la ventana de comandos y pegarlo en un explorador para ver el archivo.

```java
//Listing contents of container
for (ListBlobItem blobItem : container.listBlobs()) {
    System.out.println("URI of blob is: " + blobItem.getUri());
}
```

### <a name="download-blobs"></a>Descargar blobs

Descargue blobs al disco local mediante [CloudBlob.DownloadToFile](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._cloud_blob.downloadtofile#com_microsoft_azure_storage_blob__cloud_blob_downloadToFile_final_String).

El código siguiente descarga el blob cargado en una sección anterior, agregando el sufijo "_DOWNLOADED" al nombre de blob para que pueda ver ambos archivos en el disco local. 

```java
// Download blob. In most cases, you would have to retrieve the reference
// to cloudBlockBlob here. However, we created that reference earlier, and 
// haven't changed the blob we're interested in, so we can reuse it. 
// Here we are creating a new file to download to. Alternatively you can also pass in the path as a string into downloadToFile method: blob.downloadToFile("/path/to/new/file").
downloadedFile = new File(sourceFile.getParentFile(), "downloadedFile.txt");
blob.downloadToFile(downloadedFile.getAbsolutePath());
```

### <a name="clean-up-resources"></a>Limpieza de recursos

Si ya no necesita los blobs cargados en este tutorial de inicio rápido, puede eliminar todo el contenedor mediante [CloudBlobContainer.DeleteIfExists](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._cloud_blob_container.deleteifexists#com_microsoft_azure_storage_blob__cloud_blob_container_deleteIfExists). Esto también elimina los archivos en el contenedor.

```java
try {
if(container != null)
    container.deleteIfExists();
} catch (StorageException ex) {
System.out.println(String.format("Service error. Http code: %d and error code: %s", ex.getHttpStatusCode(), ex.getErrorCode()));
}

System.out.println("Deleting the source, and downloaded files");

if(downloadedFile != null)
downloadedFile.deleteOnExit();
        
if(sourceFile != null)
sourceFile.deleteOnExit();
```

## <a name="next-steps"></a>Pasos siguientes

En este tutorial de inicio rápido aprendió a transferir archivos entre un disco local y Azure Blob Storage mediante Java. Para más información sobre cómo trabajar con Blob Storage, continúe con los procedimientos de Blob Storage.

> [!div class="nextstepaction"]
> [Procedimientos de las operaciones de Blob Storage](storage-java-how-to-use-blob-storage.md)

Para más información sobre el Explorador de Storage y los blobs, consulte [Administración de recursos de Azure Blob Storage con el Explorador de Storage (versión preliminar)](../../vs-azure-tools-storage-explorer-blobs.md).
