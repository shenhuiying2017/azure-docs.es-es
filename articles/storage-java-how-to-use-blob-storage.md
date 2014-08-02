<properties linkid="dev-net-how-to-use-blog-storage-service-java" urlDisplayName="Blob Service" pageTitle="How to use blob storage (Java) | Microsoft Azure" metaKeywords="Get started Azure blob, Azure unstructured data, Azure unstructured storage, Azure blob, Azure blob storage, Azure blob Java" description="Learn how to use the Azure blob service to upload, download, list, and delete blob content. Samples written in Java." metaCanonical="" services="storage" documentationCenter="Java" title="How to use Blob Storage from Java" authors="" solutions="" manager="" editor="" />

Uso del almacenamiento de blobs en Java
=======================================

Esta guía muestra cómo realizar algunas tareas comunes a través del servicio de almacenamiento de blobs de Azure. Los ejemplos están escritos en Java y utilizan el [SDK de Azure para Java](http://www.windowsazure.com/en-us/develop/java/). Dichas tareas comunes incluyen **cargar**, **enumerar**, **descargar** y **eliminar** blobs. Para obtener más información acerca de los blobs, consulte la sección [Pasos siguientes](#NextSteps).

Tabla de contenido
------------------

-   [Qué es el almacenamiento de blobs](#what-is)
-   [Conceptos](#Concepts)
-   [Creación de una cuenta de almacenamiento de Azure](#CreateAccount)
-   [Creación de una aplicación de Java](#CreateApplication)
-   [Configuración de su aplicación para obtener acceso al almacenamiento de blobs](#ConfigureStorage)
-   [Configuración de una cadena de conexión de almacenamiento de Azure](#ConnectionString)
-   [Creación de un contenedor](#CreateContainer)
-   [Carga de un blob en un contenedor](#UploadBlob)
-   [Enumeración de los blobs de un contenedor](#ListBlobs)
-   [Descarga de un blob](#DownloadBlob)
-   [Eliminación de un blob](#DeleteBlob)
-   [Eliminación de un contenedor de blobs](#DeleteContainer)
-   [Pasos siguientes](#NextSteps)

[WACOM.INCLUDE [howto-blob-storage](../includes/howto-blob-storage.md)]

Creación de una cuenta de almacenamiento de Azure
-------------------------------------------------

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

Creación de una aplicación de Java
----------------------------------

En esta guía utilizará funciones del almacenamiento que puede ejecutar en una aplicación de Java localmente o bien mediante código a través de un rol web o rol de trabajo de Azure. Suponemos que ha descargado e instalado el Kit de desarrollo de Java (JDK), que ha seguido las instrucciones de la página de [descarga del SDK de Azure para Java](http://www.windowsazure.com/en-us/develop/java/) (en inglés) para instalar las Bibliotecas de Azure para Java y el SDK de Azure y que ha creado una cuenta de almacenamiento de Azure en su suscripción de Azure.

Puede utilizar cualquier herramienta de desarrollo para crear la aplicación, incluido el Bloc de notas. Todo lo que necesita es la capacidad para compilar un proyecto de Java y crear referencias a las Bibliotecas de Azure para Java.

Configuración de su aplicación para obtener acceso al almacenamiento de blobs
-----------------------------------------------------------------------------

Agregue las siguientes instrucciones de importación en la parte superior del archivo Java en el que desea utilizar las API de almacenamiento de Azure para obtener acceso a los blobs:

    // Incluya las siguientes instrucciones de importación para utilizar las API de los blobs.
    import com.microsoft.azure.services.core.storage.*;
    import com.microsoft.azure.services.blob.client.*;

Configuración de una cadena de conexión de almacenamiento de Azure
------------------------------------------------------------------

Un cliente de almacenamiento de Azure utiliza una cadena de conexión de almacenamiento de Azure para almacenar extremos y credenciales para obtener acceso a los servicios de administración de datos. Al ejecutarse en una aplicación cliente, debe proporcionar la cadena de conexión de almacenamiento en el siguiente formato, utilizando el nombre de su cuenta de almacenamiento y la clave de acceso principal de la cuenta de almacenamiento que se visualiza en el Portal de administración para los valores *AccountName* y *AccountKey*. Este ejemplo muestra la forma en la que puede declarar un campo estático para mantener la cadena de conexión:

    // Defina de la cadena de conexión con sus valores.
    public static final String storageConnectionString = 
        "DefaultEndpointsProtocol=http;" + 
        "AccountName=your_storage_account;" + 
        "AccountKey=your_storage_account_key";

En una aplicación que se esté ejecutando en un rol de Azure, esta cadena se puede almacenar en el archivo de configuración del servicio, ServiceConfiguration.cscfg, y se puede obtener acceso a él llamando al método **RoleEnvironment.getConfigurationSettings**. A continuación se muestra un ejemplo de obtención de una cadena de conexión desde un elemento de **configuración** denominado *StorageConnectionString* en el archivo de configuración del servicio:

    // Recupere la cuenta de almacenamiento desde la cadena de conexión.
    String storageConnectionString = 
        RoleEnvironment.getConfigurationSettings().get("StorageConnectionString");

Creación de un contenedor
-------------------------

Los objetos CloudBlobClient le permiten obtener objetos de referencia para los contenedores y los blobs. El siguiente código crea un objeto **CloudBlobClient**.

    // Recupere la cuenta de almacenamiento desde la cadena de conexión.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Cree el cliente blob.
    CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

Todos los blobs residen en un contenedor. Utilice el objeto **CloudBlobClient** para obtener una referencia al contenedor que desea utilizar. Puede crear el contenedor si no existe con el método **createIfNotExist**, que devolverá el contenedor existente. De manera predeterminada, el nuevo contenedor es privado, por lo que debe especificar su clave de acceso de almacenamiento (como hizo anteriormente) para descargar blobs de él.

    // Obtenga una referencia a un contenedor.
    // El nombre del contenedor debe aparecer en letras minúsculas.
    CloudBlobContainer container = blobClient.getContainerReference("mycontainer");

    // Cree el contenedor si todavía no existe.
    container.createIfNotExist();

Si desea hacer públicos los archivos, debe configurar los permisos del contenedor.

    // Cree un objeto de permisos.
    BlobContainerPermissions containerPermissions = new BlobContainerPermissions();

    // Incluya un acceso público en el objeto de permisos.
    containerPermissions.setPublicAccess(BlobContainerPublicAccessType.CONTAINER);

    // Configure los permisos en el contenedor.
    container.uploadPermissions(containerPermissions);

Cualquier usuario de Internet puede ver los blobs de un contenedor público, pero el acceso está limitado a solo lectura.

Carga de un blob en un contenedor
---------------------------------

Para cargar un archivo en un blob, obtenga una referencia de contenedor y utilícela para obtener una referencia de blob. Una vez tenga una referencia de blob, puede cargar cualquier flujo mediante una llamada de carga en la referencia del blob. De este modo, se creará el blob si no existe, o bien se sobrescribirá si ya existe. El siguiente ejemplo de código muestra esto y presupone que ya se ha creado el contenedor.

    // Recupere la cuenta de almacenamiento desde la cadena de conexión.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Cree el cliente blob.
    CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

    // Recupere una referencia a un contenedor creado previamente.
    CloudBlobContainer container = blobClient.getContainerReference("mycontainer");

    // Cree o sobrescriba el blob "myimage.jpg" con contenidos desde un archivo local.
    CloudBlockBlob blob = container.getBlockBlobReference("myimage.jpg");
    File source = new File("c:\myimages\myimage.jpg");
    blob.upload(new FileInputStream(source), source.length());

Enumeración de los blobs de un contenedor
-----------------------------------------

Para enumerar los blobs de un contenedor, obtenga primero una referencia del contenedor del mismo modo que al cargar un blob. Puede utilizar el método **listBlobs** del contenedor con un bucle for. El código siguiente ofrece el Uri de todos los blobs de un contenedor a la consola.

    // Recupere la cuenta de almacenamiento desde la cadena de conexión.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Cree el cliente blob.
    CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

    // Recupere una referencia a un contenedor creado previamente.
    CloudBlobContainer container = blobClient.getContainerReference("mycontainer");

    // Aplique un bucle sobre los blobs dentro del contenedor y consulte el URI de cada uno de ellos.
    for (ListBlobItem blobItem : container.listBlobs()) {
        System.out.println(blobItem.getUri());
    }

En el servicio de blobs, los contenedores también incluyen directorios. De este modo, es posible organizar los blobs en una estructura similar a la estructura de carpetas.

Por ejemplo, puede disponer de un contenedor con el nombre "photos", en el que puede cargar blobs con los nombres "rootphoto1", "2010/photo1", "2010/photo2" y "2011/photo1". De esta forma, se crean los directorios virtuales "2010" y "2011" dentro del contenedor "photos". Al llamar a **listBlobs** en el contenedor "photos", la recopilación devuelta contendrá objetos **CloudBlobDirectory** y **CloudBlob** que representan los directorios y los blobs que existen en el nivel superior. En este caso, se devolverán los directorios "2010" y "2011" y la foto "rootphoto1". Puede utilizar el operador **instanceof** para distinguir estos objetos.

Opcionalmente, puede pasar parámetros al método **listBlobs** con el parámetro **useFlatBlobListing** establecido en "true". De este modo, se devuelven todos los blobs con independencia del directorio. Para obtener más información, consulte CloudBlobContainer.listBlobs en la documentación Javadocs.

Descarga de un blob
-------------------

Para descargar blobs, siga los mismos pasos que realizó para cargar un blob con el fin de obtener una referencia de blob. En el ejemplo de carga, llamó a la función upload en el objeto del blob. En el siguiente ejemplo, llame a la función download para transferir los contenidos del blob a un objeto de secuencia como un **FileOutputStream** que puede utilizar para conservar el blob en un archivo local.

    // Recupere la cuenta de almacenamiento desde la cadena de conexión.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Cree el cliente blob.
    CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

    // Recupere una referencia a un contenedor creado previamente.
    CloudBlobContainer container = blobClient.getContainerReference("mycontainer");

    // Para cada elemento del contenedor.
    for (ListBlobItem blobItem : container.listBlobs()) {
        // Si el elemento es un blob, no un directorio virtual.
        if (blobItem instanceof CloudBlob) {
            // Descargue el elemento y guárdelo en un archivo con el mismo nombre.
            CloudBlob blob = (CloudBlob) blobItem;
            blob.download(new FileOutputStream(blob.getName()));
        }
    }

Eliminación de un blob
----------------------

Para eliminar un blob, obtenga una referencia del blob y llame a la función **delete**.

    // Recupere la cuenta de almacenamiento desde la cadena de conexión.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Cree el cliente blob.
    CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

    // Recupere una referencia a un contenedor creado previamente.
    CloudBlobContainer container = blobClient.getContainerReference("mycontainer");

    // Recupere una referencia a un blob denominado "myimage.jpg".
    CloudBlockBlob blob = container.getBlockBlobReference("myimage.jpg");

    // Elimine el blob.
    blob.delete();

Eliminación de un contenedor de blobs
-------------------------------------

Finalmente, para eliminar un contenedor de blobs, obtenga una referencia del contenedor de blobs y llame a la función delete.

    // Recupere la cuenta de almacenamiento desde la cadena de conexión.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Cree el cliente blob.
    CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

    // Recupere una referencia a un contenedor creado previamente.
    CloudBlobContainer container = blobClient.getContainerReference("mycontainer");

    // Elimine el contenedor de blobs.
    container.delete();

Pasos siguientes
----------------

Ahora que está familiarizado con los aspectos básicos del almacenamiento de blobs, siga estos vínculos para obtener más información acerca de cómo realizar tareas de almacenamiento más complejas.

-   Consulte la referencia de MSDN: [Almacenamiento de datos y acceso a los mismos en Azure]
-   Obtenga acceso al blog del equipo de almacenamiento de Azure: &lt;http://blogs.msdn.com/b/windowsazurestorage/\>

