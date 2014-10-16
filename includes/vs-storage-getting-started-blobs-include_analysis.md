###### Creación de un contenedor

Al igual que los archivos residen en carpetas, los blobs de almacenamiento residen en contenedores.

-   Puede utilizar un objeto **CloudBlobClient** para obtener una referencia al contenedor que desee usar.

-   Puede llamar al método CreateCloudBlobClient() para crear un contenedor si lo necesita.

El código siguiente muestra cómo crear un contenedor de almacenamiento de blobs. El código crea un objeto **BlobClient** que le permite tener acceso a las funciones del objeto, como la creación de un contenedor de almacenamiento. Entonces, el código intenta hacer referencia a un contenedor de almacenamiento denominado "mycontainer". Si no encuentra un contenedor con ese nombre, lo crea.

    // Create a blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Get a reference to a container named “mycontainer.”
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // If “mycontainer” doesn’t exist, create it.
    container.CreateIfNotExists();

**NOTA:** utilice este bloque de código delante del código en las secciones siguientes.

###### Un blob en un contenedor

Para cargar un archivo de blob en un contenedor, obtenga una referencia de contenedor y utilícela para obtener una referencia de blob. Una vez que disponga de la referencia de blob, puede cargar cualquier secuencia de datos en ella llamando al método UploadFromStream(). Esta operación creará el blob si todavía no existe, o bien lo sobrescribirá si ya existe. En el siguiente ejemplo se muestra cómo cargar un blob en un contenedor creado anteriormente.

    // Create or overwrite the "myblob" blob with the contents of a local file
    // named “myfile”.
    using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
    {
        blockBlob.UploadFromStream(fileStream);
    }

###### Los blobs de un contenedor

Para enumerar los blobs de un contenedor, primero obtenga una referencia de contenedor. A continuación, puede llamar al método ListBlobs() del contenedor para recuperar los blobs o los directorios que contiene. Para obtener acceso a las numerosas propiedades y métodos de una lista **IListBlobItem** recuperada, debe convertirla en un objeto **CloudBlockBlob**, **CloudPageBlob** o **CloudBlobDirectory**. Si no conoce el tipo de blob, puede utilizar una comprobación de tipo para determinar el formato al que debe convertirlo. El código siguiente demuestra cómo recuperar y consultar el URI de cada elemento en un contenedor denominado "photos".

    // Get a reference to a previously created container.
    CloudBlobContainer container = blobClient.GetContainerReference("photos");

    // Loop through items in the container and output the length and URI for each 
    // item.
    foreach (IListBlobItem item in container.ListBlobs(null, false))
    {
        if (item.GetType() == typeof(CloudBlockBlob))
        {
            CloudBlockBlob blob = (CloudBlockBlob)item;

            Console.WriteLine("Block blob of length {0}: {1}", blob.Properties.Length, blob.Uri);

        }
        else if (item.GetType() == typeof(CloudPageBlob))
        {
            CloudPageBlob pageBlob = (CloudPageBlob)item;

            Console.WriteLine("Page blob of length {0}: {1}", pageBlob.Properties.Length, pageBlob.Uri);

        }
        else if (item.GetType() == typeof(CloudBlobDirectory))
        {
            CloudBlobDirectory directory = (CloudBlobDirectory)item;

            Console.WriteLine("Directory: {0}", directory.Uri);
        }
    }

Hay otras maneras de enumerar el contenido de un contenedor de blobs. Consulte [Uso del almacenamiento de blobs en .NET][Uso del almacenamiento de blobs en .NET] para obtener más información.

###### Descarga de un blob

Para descargar un blob, primero obtenga una referencia al blob y, a continuación, llame al método DownloadToStream(). En el siguiente ejemplo se usa el método DownloadToStream() para transferir el contenido del blob a un objeto de secuencia que, a continuación, puede guardar como archivo local.

    // Get a reference to a blob named "photo1.jpg".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

    // Save the blob contents to a file named “myfile”.
    using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
    {
        blockBlob.DownloadToStream(fileStream);
    }

Hay otras maneras de guardar blobs como archivos. Consulte [Uso del almacenamiento de blobs en .NET][1] para obtener más información.

###### Eliminación de un blob

Para eliminar un blob, primero obtenga una referencia al blob y, a continuación, llame al método Delete() en él.

    // Get a reference to a blob named "myblob.txt".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");

    // Delete the blob.
    blockBlob.Delete();

  [Uso del almacenamiento de blobs en .NET]: http://azure.microsoft.com/es-es/documentation/articles/storage-dotnet-how-to-use-blobs/#list-blob
  [1]: http://azure.microsoft.com/es-es/documentation/articles/storage-dotnet-how-to-use-blobs/#download-blobs
