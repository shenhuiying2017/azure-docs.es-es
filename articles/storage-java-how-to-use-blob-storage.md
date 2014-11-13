<properties urlDisplayName="Blob Service" pageTitle="Uso de almacenamiento de blobs (Java) | Microsoft Azure" metaKeywords="Get started Azure blob, Azure unstructured data, Azure unstructured storage, Azure blob, Azure blob storage, Azure blob Java" description="Aprenda a utilizar el servicio BLOB de Azure para cargar, descargar, incluir en un listado y eliminar contenido de blobs. Ejemplos escritos en Java." metaCanonical="" services="storage" documentationCenter="Java" title="Uso del almacenamiento de blobs en Java" authors="tamram" solutions="" manager="adinah" editor="" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author="tamram" />

# Uso del almacenamiento de blobs en Java

Esta guía muestra cómo realizar algunas tareas comunes con el servicio de almacenamiento de blobs de Microsoft Azure. Los ejemplos están escritos en Java y utilizan el [SDK de almacenamiento de Azure para Java][SDK de almacenamiento de Azure para Java]. Dichas tareas comunes incluyen **cargar**, **enumerar**, **descargar** y **eliminar** blobs. Para obtener más información acerca de los blobs, consulte la sección [Pasos siguientes][Pasos siguientes].

Nota: hay un SDK disponible para los desarrolladores que usen el almacenamiento de Azure en dispositivos Android. Para obtener más información, consulte el [SDK de almacenamiento de Azure para Android][SDK de almacenamiento de Azure para Android].

## <a name="Contents"> </a>Tabla de contenido

-   [Qué es el almacenamiento de blobs][Qué es el almacenamiento de blobs]
-   [Conceptos][Conceptos]
-   [Crear una cuenta de almacenamiento de Azure][Crear una cuenta de almacenamiento de Azure]
-   [Creación de una aplicación Java][Creación de una aplicación Java]
-   [Configuración de su aplicación para obtener acceso al almacenamiento de blobs][Configuración de su aplicación para obtener acceso al almacenamiento de blobs]
-   [Configuración de una cadena de conexión de almacenamiento de Azure][Configuración de una cadena de conexión de almacenamiento de Azure]
-   [Direccionamiento del un contenedor][Direccionamiento del un contenedor]
-   [Direccionamiento del un blob en un contenedor][Direccionamiento del un blob en un contenedor]
-   [Direccionamiento del los blobs de un contenedor][Direccionamiento del los blobs de un contenedor]
-   [Direccionamiento del un blob][Direccionamiento del un blob]
-   [Direccionamiento del un blob][1]
-   [Direccionamiento del un contenedor de blobs][Direccionamiento del un contenedor de blobs]
-   [Pasos siguientes][Pasos siguientes]

[WACOM.INCLUDE [howto-blob-storage](../includes/howto-blob-storage.md)]

## <span id="CreateAccount"></span></a>Creación de una cuenta de almacenamiento de Azure

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

## <a name="CreateApplication"> </a>Creación de una aplicación Java

En esta guía utilizará funciones del almacenamiento que puede ejecutar en una aplicación Java localmente o bien mediante código a través de un rol web o un rol de trabajo de Azure.

Para ello, deberá instalar el Kit de desarrollo de Java (JDK) y crear una cuenta de almacenamiento de Azure en su suscripción de Azure. A continuación, deberá verificar que su sistema de desarrollo satisface los requisitos mínimos y las dependencias que se indican en el repositorio del [SDK de almacenamiento de Azure para Java][SDK de almacenamiento de Azure para Java] en GitHub. Si su sistema cumple esos requisitos, puede seguir las instrucciones para descargar e instalar las bibliotecas de almacenamiento de Azure para Java en su sistema desde ese repositorio. Cuando haya completado esas tareas, podrá crear una aplicación Java que use los ejemplos de este artículo.

## <a name="ConfigureStorage"> </a>Configuración de su aplicación para obtener acceso al almacenamiento de blobs

Agregue las siguientes instrucciones de importación al principio del archivo Java en el que desea utilizar las API de almacenamiento de Azure para obtener acceso a los blobs:

    // Include the following imports to use blob APIs.
    import com.microsoft.azure.storage.*;
    import com.microsoft.azure.storage.blob.*;

## <a name="ConnectionString"> </a>Configuración de una cadena de conexión de almacenamiento de Azure

Un cliente de almacenamiento de Azure utiliza una cadena de conexión de almacenamiento
para almacenar extremos y credenciales con el fin de obtener acceso a los servicios de administración de datos. Al ejecutarse en una aplicación cliente, debe proporcionar la cadena de conexión de almacenamiento en el siguiente formato, utilizando el nombre de su cuenta de almacenamiento y la clave de acceso principal de la cuenta de almacenamiento que se muestra en el Portal de administración para los valores *AccountName* y *AccountKey*. En este ejemplo se muestra cómo puede declarar un campo estático para mantener la cadena de conexión:

    // Define the connection-string with your values
    public static final String storageConnectionString = 
        "DefaultEndpointsProtocol=http;" + 
        "AccountName=your_storage_account;" + 
        "AccountKey=your_storage_account_key";

En una aplicación que se esté ejecutando en un rol de Microsoft Azure, esta cadena se puede almacenar en el archivo de configuración del servicio, *ServiceConfiguration.cscfg*, y se puede obtener acceso a él con una llamada al método **RoleEnvironment.getConfigurationSettings**. A continuación se muestra un ejemplo de cómo obtener la cadena de conexión desde un elemento de **configuración** denominado *StorageConnectionString* en el archivo de configuración del servicio:

    // Retrieve storage account from connection-string.
    String storageConnectionString = 
        RoleEnvironment.getConfigurationSettings().get("StorageConnectionString");

En los ejemplos siguientes se supone que ha usado uno de estos dos métodos para obtener la cadena de conexión de almacenamiento.

## <a name="CreateContainer"> </a>Direccionamiento del Creación de un contenedor

Los objetos CloudBlobClient le permiten obtener objetos de referencia para los contenedores y los blobs. El siguiente código crea un objeto **CloudBlobClient**. (Nota: existen otras maneras de crear objetos **CloudStorageAccount**; para obtener más información, consulte **CloudStorageAccount** en la [Referencia del SDK del cliente de almacenamiento de Azure][Referencia del SDK del cliente de almacenamiento de Azure]).

Todos los blobs residen en un contenedor. Utilice el objeto **CloudBlobClient** para obtener una referencia al contenedor que desea utilizar. Puede crear el contenedor si no existe con el método **createIfNotExists**, que devolverá el contenedor existente. De manera predeterminada, el nuevo contenedor es privado, por lo que debe especificar su clave de acceso de almacenamiento (como hizo anteriormente) para descargar blobs de él.

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

        // Create the blob client.
       CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

       // Get a reference to a container.
       // The container name must be lower case
       CloudBlobContainer container = blobClient.getContainerReference("mycontainer");

       // Create the container if it does not exist.
        container.createIfNotExists();
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

### Opcional: Configuración de un contenedor para acceso público

Los permisos de un contenedor están configurados para acceso privado de forma predeterminada, pero puede configurarlos fácilmente para permitir acceso público de solo lectura a todos los usuarios de Internet:

    // Create a permissions object.
    BlobContainerPermissions containerPermissions = new BlobContainerPermissions();

    // Include public access in the permissions object.
    containerPermissions.setPublicAccess(BlobContainerPublicAccessType.CONTAINER);

    // Set the permissions on the container.
    container.uploadPermissions(containerPermissions);

## <a name="UploadBlob"> </a>Direccionamiento del un blob en un contenedor

Para cargar un archivo en un blob, obtenga una referencia de contenedor y utilícela para obtener una referencia de blob. Una vez tenga una referencia de blob, puede cargar cualquier flujo mediante una llamada de carga en la referencia del blob. De este modo, se creará el blob si no existe, o bien se sobrescribirá si ya existe. El siguiente ejemplo de código muestra esto y presupone que ya se ha creado el contenedor.

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

        // Create the blob client.
        CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

       // Retrieve reference to a previously created container.
        CloudBlobContainer container = blobClient.getContainerReference("mycontainer");
            
        // Define the path to a local file.
        final String filePath = "C:\\myimages\\myimage.jpg";

        // Create or overwrite the "myimage.jpg" blob with contents from a local file.
        CloudBlockBlob blob = container.getBlockBlobReference("myimage.jpg");
        File source = new File(filePath);
        blob.upload(new FileInputStream(source), source.length());
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="ListBlobs"> </a>Direccionamiento del los blobs de un contenedor

Para enumerar los blobs de un contenedor, obtenga primero una referencia del contenedor del mismo modo que al cargar un blob. Puede utilizar el método **listBlobs** del contenedor con un bucle **for**. El código siguiente ofrece el Uri de todos los blobs de un contenedor a la consola.

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

        // Create the blob client.
        CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

        // Retrieve reference to a previously created container.
        CloudBlobContainer container = blobClient.getContainerReference("mycontainer");
            
        // Loop over blobs within the container and output the URI to each of them.
        for (ListBlobItem blobItem : container.listBlobs()) {
           System.out.println(blobItem.getUri());
       }
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

En el servicio de blobs, los contenedores también incluyen directorios. De este modo, es posible organizar los blobs en una estructura similar a la estructura de carpetas.

Por ejemplo, puede disponer de un contenedor con el nombre "photos", en el que puede cargar blobs con los nombres "rootphoto1", "2010/photo1", "2010/photo2" y "2011/photo1". De esta forma, se crean los directorios virtuales "2010" y "2011" dentro del contenedor "photos". Al llamar a **listBlobs** en el contenedor "photos", la recopilación devuelta contendrá objetos **CloudBlobDirectory** y **CloudBlob** que representan los directorios y los blobs que existen en el nivel superior. En este caso, se devolverán los directorios "2010" y "2011" y la foto "rootphoto1". Puede utilizar el operador **instanceof** para distinguir estos objetos.

Opcionalmente, puede pasar parámetros al método **listBlobs** con el
parámetro **useFlatBlobListing** establecido en true. De este modo, se devuelven todos los blobs
con independencia del directorio. Para obtener
más información, consulte **CloudBlobContainer.listBlobs** en la [Documentación de referencia del SDK de cliente de Almacenamiento de Azure][Referencia del SDK del cliente de almacenamiento de Azure].

## <a name="DownloadBlob"> </a>Direccionamiento del Descarga de un blob

Para descargar blobs, siga los mismos pasos que realizó para cargar un blob con el fin de obtener una referencia de blob. En el ejemplo de carga, llamó a la función upload en el objeto del blob. En el siguiente ejemplo, llame a la función download para transferir los contenidos del blob a un objeto de secuencia como un **FileOutputStream** que puede utilizar para conservar el blob en un archivo local.

    try
    {
        // Retrieve storage account from connection-string.
       CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

       // Create the blob client.
       CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

       // Retrieve reference to a previously created container.
       CloudBlobContainer container = blobClient.getContainerReference("mycontainer");
            
       // Loop through each blob item in the container.
       for (ListBlobItem blobItem : container.listBlobs()) {
           // If the item is a blob, not a virtual directory.
           if (blobItem instanceof CloudBlob) {
               // Download the item and save it to a file with the same name.
                CloudBlob blob = (CloudBlob) blobItem;
                blob.download(new FileOutputStream("C:\\mydownloads\\" + blob.getName()));
            }
        }
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="DeleteBlob"> </a>Direccionamiento del Eliminación de un blob

Para eliminar un blob, obtenga una referencia del blob y llame a la función **deleteIfExist**.

    try
    {
       // Retrieve storage account from connection-string.
       CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

       // Create the blob client.
       CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

       // Retrieve reference to a previously created container.
       CloudBlobContainer container = blobClient.getContainerReference("mycontainer");
            
       // Retrieve reference to a blob named "myimage.jpg".
       CloudBlockBlob blob = container.getBlockBlobReference("myimage.jpg");

       // Delete the blob.
       blob.deleteIfExists();
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="DeleteContainer"> </a>Direccionamiento del un contenedor de blobs

Finalmente, para eliminar un contenedor de blobs, obtenga una referencia
del contenedor de blobs y llame a la función **deleteIfExists**.

    try
    {
       // Retrieve storage account from connection-string.
       CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

       // Create the blob client.
       CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

       // Retrieve reference to a previously created container.
       CloudBlobContainer container = blobClient.getContainerReference("mycontainer");
            
       // Delete the blob container.
       container.deleteIfExists();
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="NextSteps"> </a>Pasos siguientes

Ahora que está familiarizado con los aspectos básicos del almacenamiento de blobs, siga estos vínculos para obtener más información acerca de cómo realizar tareas de almacenamiento más complejas.

-   [SDK de almacenamiento de Azure para Java][SDK de almacenamiento de Azure para Java]
-   [Referencia del SDK de cliente de almacenamiento de Azure][Referencia del SDK del cliente de almacenamiento de Azure]
-   [API REST de almacenamiento de Azure][API REST de almacenamiento de Azure]
-   [Blog del equipo de almacenamiento de Azure][Blog del equipo de almacenamiento de Azure]

  [SDK de almacenamiento de Azure para Java]: https://github.com/azure/azure-storage-java
  [Pasos siguientes]: #NextSteps
  [SDK de almacenamiento de Azure para Android]: https://github.com/azure/azure-storage-android
  [Qué es el almacenamiento de blobs]: #what-is
  [Conceptos]: #Concepts
  [Crear una cuenta de almacenamiento de Azure]: #CreateAccount
  [Creación de una aplicación Java]: #CreateApplication
  [Configuración de su aplicación para obtener acceso al almacenamiento de blobs]: #ConfigureStorage
  [Configuración de una cadena de conexión de almacenamiento de Azure]: #ConnectionString
  [Direccionamiento del un contenedor]: #CreateContainer
  [Direccionamiento del un blob en un contenedor]: #UploadBlob
  [Direccionamiento del los blobs de un contenedor]: #ListBlobs
  [Direccionamiento del un blob]: #DownloadBlob
  [1]: #DeleteBlob
  [Direccionamiento del un contenedor de blobs]: #DeleteContainer
  [Referencia del SDK del cliente de almacenamiento de Azure]: http://dl.windowsazure.com/storage/javadoc/
  [API REST de almacenamiento de Azure]: http://msdn.microsoft.com/es-es/library/azure/gg433040.aspx
  [Blog del equipo de almacenamiento de Azure]: http://blogs.msdn.com/b/windowsazurestorage/
