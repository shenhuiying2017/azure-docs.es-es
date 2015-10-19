<properties
	pageTitle="Uso del almacenamiento de blobs de .NET | Microsoft Azure"
	description="Aprenda a usar el almacenamiento de blobs de Azure y cómo crear un contenedor para cargar, descargar, enumerar y eliminar contenido de blobs."
	services="storage"
	documentationCenter=".net"
	authors="tamram"
	manager="adinah"
	editor=""/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="hero-article" 
	ms.date="08/04/2015"
	ms.author="tamram"/>


# Uso del almacenamiento de blobs de .NET

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]

## Información general

Esta guía demuestra cómo realizar algunas tareas comunes a través del servicio de almacenamiento de blobs de Azure. Los ejemplos están escritos en C# y utilizan la biblioteca del cliente de almacenamiento de Azure para .NET. Entre los escenarios descritos se incluyen **cargar**, **enumerar**, **descargar** y **eliminar** blobs.

[AZURE.INCLUDE [storage-dotnet-client-library-version-include](../../includes/storage-dotnet-client-library-version-include.md)]

[AZURE.INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

[AZURE.INCLUDE [storage-configure-connection-string-include](../../includes/storage-configure-connection-string-include.md)]

## Acceso al almacenamiento de blobs mediante programación

[AZURE.INCLUDE [storage-dotnet-obtain-assembly](../../includes/storage-dotnet-obtain-assembly.md)]

### Declaraciones de espacio de nombres
Agregue las siguientes declaraciones de espacio de nombres en la parte superior de todo archivo C# en el que desee obtener acceso al almacenamiento de Azure mediante programación:

    using Microsoft.WindowsAzure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Blob;

Asegúrese de hacer referencia al ensamblado `Microsoft.WindowsAzure.Storage.dll`.

[AZURE.INCLUDE [storage-dotnet-retrieve-conn-string](../../includes/storage-dotnet-retrieve-conn-string.md)]

El tipo **CloudBlobClient** permite recuperar objetos que representan contenedores y blobs almacenados en el servicio de almacenamiento de blobs. El código siguiente crea un objeto **CloudBlobClient** utilizando el objeto de cuenta de almacenamiento recuperado anteriormente:

    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

## Crear un contenedor

[AZURE.INCLUDE [storage-container-naming-rules-include](../../includes/storage-container-naming-rules-include.md)]

En este ejemplo se muestra cómo crear un contenedor si todavía no existe:

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve a reference to a container.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Create the container if it doesn't already exist.
    container.CreateIfNotExists();

De manera predeterminada, el nuevo contenedor es privado y debe especificar su clave de acceso de almacenamiento para descargar blobs de él. Si desea poner los archivos del contenedor a disposición de todo el mundo, puede convertir el contenedor en público utilizando el código siguiente:

    container.SetPermissions(
        new BlobContainerPermissions { PublicAccess =
 	    BlobContainerPublicAccessType.Blob });

Cualquier usuario de Internet puede ver los blobs de los contenedores públicos, pero solo es posible modificarlos o eliminarlos si se dispone de la clave de acceso apropiada.

## Cargar un blob en un contenedor

El almacenamiento de blobs de Azure admite blobs en bloques y en páginas. En la mayoría de los casos, se recomienda usar blobs en bloques.

Para cargar un archivo en un blob en bloques, obtenga una referencia de contenedor y utilícela para obtener una referencia de blob en bloques. Una vez que disponga de la referencia de blob, puede cargar cualquier secuencia de datos en ella llamando al método **UploadFromStream**. De este modo, se creará el blob si no existía anteriormente, o bien se sobrescribirá si ya existía.

En el siguiente ejemplo se muestra cómo cargar un blob en un contenedor creado anteriormente.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Retrieve reference to a blob named "myblob".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");

    // Create or overwrite the "myblob" blob with contents from a local file.
    using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
    {
        blockBlob.UploadFromStream(fileStream);
    }

## Enumerar los blobs de un contenedor

Para enumerar los blobs de un contenedor, primero obtenga una referencia de contenedor. A continuación, puede utilizar el método **ListBlobs** del contenedor para recuperar los blobs y los directorios que contiene. Para obtener acceso a las numerosas propiedades y métodos de una lista **IListBlobItem** recuperada, debe convertir esta última en un objeto **CloudBlockBlob**, **CloudPageBlob**, o **CloudBlobDirectory**. Si se desconoce el tipo, puede realizar una comprobación de tipo para determinar el formato al que se debe convertir. El código siguiente muestra cómo recuperar y consultar el URI de cada elemento del contenedor `photos`:

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
	CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

	// Retrieve reference to a previously created container.
	CloudBlobContainer container = blobClient.GetContainerReference("photos");

	// Loop over items within the container and output the length and URI.
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

Como se mostró anteriormente, puede asignar nombre a los blobs incluyendo información de ruta de acceso. De este modo crea una estructura de directorios virtuales que puede organizar y recorrer tal como lo haría en un sistema de archivos tradicional. Tenga en cuenta que la estructura de directorios es solo virtual: los únicos recursos disponibles en el almacenamiento de blobs son contenedores y blobs. Sin embargo, la biblioteca de clientes de almacenamiento ofrece un objeto **CloudBlobDirectory** para hacer referencia a un directorio virtual y simplificar el proceso de trabajo con blobs organizados de este modo.

Por ejemplo, observe el siguiente conjunto de blobs en bloques incluidos en un contenedor denominado `photos`:

	photo1.jpg
	2010/architecture/description.txt
	2010/architecture/photo3.jpg
	2010/architecture/photo4.jpg
	2011/architecture/photo5.jpg
	2011/architecture/photo6.jpg
	2011/architecture/description.txt
	2011/photo7.jpg

Al llamar a **ListBlobs** en el contenedor "photos" (como en el ejemplo anterior), se devuelve una lista jerárquica. Contiene objetos **CloudBlobDirectory** y **CloudBlockBlob** que representan, respectivamente, los directorios y los blobs del contenedor. El resultado tiene el siguiente aspecto:

	Directory: https://<accountname>.blob.core.windows.net/photos/2010/
	Directory: https://<accountname>.blob.core.windows.net/photos/2011/
	Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg


También existe la opción de establecer el parámetro **UseFlatBlobListing** del método **ListBlobs** en **true**. En este caso, los blobs del contenedor se devuelven como objetos **CloudBlockBlob**. La llamada a **ListBlobs** para devolver una lista plana tiene el siguiente aspecto:

    // Loop over items within the container and output the length and URI.
	foreach (IListBlobItem item in container.ListBlobs(null, true))
	{
	   ...
	}

y el resultado tiene este aspecto:

	Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2010/architecture/description.txt
	Block blob of length 314618: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo3.jpg
	Block blob of length 522713: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo4.jpg
	Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2011/architecture/description.txt
	Block blob of length 419048: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo5.jpg
	Block blob of length 506388: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo6.jpg
	Block blob of length 399751: https://<accountname>.blob.core.windows.net/photos/2011/photo7.jpg
	Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg


## Descargar blobs

Para descargar blobs, primero recupere una referencia de blob y, a continuación, llame al método **DownloadToStream**. En el siguiente ejemplo, se usa el método **DownloadToStream** para transferir el contenido del blob a un objeto de secuencia que, a continuación, puede guardar en un archivo local.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Retrieve reference to a blob named "photo1.jpg".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

    // Save blob contents to a file.
    using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
    {
        blockBlob.DownloadToStream(fileStream);
    }

También puede usar el método **DownloadToStream** para descargar el contenido de un blob en forma de cadena de texto.

	// Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

	// Retrieve reference to a blob named "myblob.txt"
	CloudBlockBlob blockBlob2 = container.GetBlockBlobReference("myblob.txt");

	string text;
	using (var memoryStream = new MemoryStream())
	{
		blockBlob2.DownloadToStream(memoryStream);
		text = System.Text.Encoding.UTF8.GetString(memoryStream.ToArray());
	}

## Eliminar blobs

Para eliminar un blob, primero obtenga una referencia de blob y, a continuación, llame al método **Delete** en ella.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Retrieve reference to a blob named "myblob.txt".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");

    // Delete the blob.
    blockBlob.Delete();


## Enumerar blobs en páginas de forma asincrónica

Si enumera un gran número de blobs o desea controlar el número de resultados que devuelve en una operación de listado, puede enumerar blobs en páginas de resultados. En este ejemplo se muestra cómo devolver resultados en páginas asincrónicamente de forma que la ejecución no se bloquee mientras se espera a devolver un conjunto grande de resultados.

En este ejemplo se muestra un listado de blobs plano, pero también puede realizar un listado jerárquico estableciendo el parámetro `useFlatBlobListing` del método **ListBlobsSegmentedAsync** en `false`.

Dado que el método de ejemplo llama a un método asincrónico, debe ir precedido por la palabra clave `async` y debe devolver un objeto **Task**. La palabra clave await especificada para el método **ListBlobsSegmentedAsync** suspende la ejecución del método de ejemplo hasta que la tarea de enumeración se completa.

    async public static Task ListBlobsSegmentedInFlatListing(CloudBlobContainer container)
    {
        //List blobs to the console window, with paging.
        Console.WriteLine("List blobs in pages:");

        int i = 0;
        BlobContinuationToken continuationToken = null;
        BlobResultSegment resultSegment = null;

        //Call ListBlobsSegmentedAsync and enumerate the result segment returned, while the continuation token is non-null.
        //When the continuation token is null, the last page has been returned and execution can exit the loop.
        do
        {
            //This overload allows control of the page size. You can return all remaining results by passing null for the maxResults parameter,
            //or by calling a different overload.
            resultSegment = await container.ListBlobsSegmentedAsync("", true, BlobListingDetails.All, 10, continuationToken, null, null);
            if (resultSegment.Results.Count<IListBlobItem>() > 0) { Console.WriteLine("Page {0}:", ++i); }
            foreach (var blobItem in resultSegment.Results)
            {
                Console.WriteLine("\t{0}", blobItem.StorageUri.PrimaryUri);
            }
            Console.WriteLine();

            //Get the continuation token.
            continuationToken = resultSegment.ContinuationToken;
        }
        while (continuationToken != null);
    }

## Escritura en un blob en anexos

Un blob en anexos es un nuevo tipo de blob, que se introdujo con la versión 5.x de la biblioteca de cliente de almacenamiento de Azure para. NET. Un blob en anexos se optimiza para las operaciones de anexado, como el registro. Como un blob en bloques, un blob en anexos se compone también de bloques, pero en el caso del blob en anexos cuando se agrega un nuevo bloque, siempre se anexa al final del blob. No se puede actualizar o eliminar un bloque existente en un blob en anexos. Los identificadores de bloque para un blob en anexos no está expuestos como lo están en el caso de los blobs en bloques.
 
Cada bloque en un blob en anexos puede tener un tamaño diferente, hasta un máximo de 4 MB y el blob puede incluir un máximo de 50.000 bloques. El tamaño máximo de un blob en anexos es, por tanto, ligeramente superior a 195 GB (4 MB X 50.000 bloques).

El ejemplo siguiente crea un nuevo blob en anexos y le anexa algunos datos, para simular una operación de registro simple.

    //Parse the connection string for the storage account.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

    //Create service client for credentialed access to the Blob service.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    //Get a reference to a container.
    CloudBlobContainer container = blobClient.GetContainerReference("my-append-blobs");

    //Create the container if it does not already exist. 
    container.CreateIfNotExists();

    //Get a reference to an append blob.
    CloudAppendBlob appendBlob = container.GetAppendBlobReference("append-blob.log");

    //Create the append blob. Note that if the blob already exists, the CreateOrReplace() method will overwrite it.
    //You can check whether the blob exists to avoid overwriting it by using CloudAppendBlob.Exists().
    appendBlob.CreateOrReplace();

    int numBlocks = 10;

    //Generate an array of random bytes.
    Random rnd = new Random();
    byte[] bytes = new byte[numBlocks];
    rnd.NextBytes(bytes);
        
    //Simulate a logging operation by writing text data and byte data to the end of the append blob.
    for (int i = 0; i < numBlocks; i++)
    {
        appendBlob.AppendText(String.Format("Timestamp: {0:u} \tLog Entry: {1}{2}",
            DateTime.UtcNow, bytes[i], Environment.NewLine));
    }

    //Read the append blob to the console window.
    Console.WriteLine(appendBlob.DownloadText());

Consulte [Descripción Blobs en bloques, en anexos y en páginas](https://msdn.microsoft.com/library/azure/ee691964.aspx) para obtener más información acerca de las diferencias entre los tres tipos de blobs.

## Pasos siguientes

Ahora que está familiarizado con los aspectos básicos del almacenamiento de blobs, utilice estos vínculos para obtener más información acerca de tareas de almacenamiento más complejas. <ul> <li>Consulte la documentación de referencia del servicio de blobs para obtener información detallada acerca de las API disponibles: <ul> <li>Referencia de la biblioteca de clientes de almacenamiento para .NET<a href="http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409"></a> </li> <li><a href="http://msdn.microsoft.com/library/azure/dd179355">Referencia de la API REST</a></li> </ul> </li> Obtenga información acerca de las tareas más avanzadas que se pueden realizar con Almacenamiento de Azure en <li><a href="http://msdn.microsoft.com/library/azure/gg433040.aspx">Almacenamiento y acceso a datos en Azure</a>.</li> <li>Aprenda a simplificar el código que escriba para trabajar con Almacenamiento de Azure mediante el <a href="../websites-dotnet-webjobs-sdk/">SDK de WebJobs de Azure</li>. <li>Consulte más guías de características para obtener información acerca de otras opciones del almacenamiento de datos en Azure. <ul> <li> Utilice <a href="/documentation/articles/storage-dotnet-how-to-use-tables/">Almacenamiento de tablas</a> para almacenar datos estructurados.</li> <li>Utilice <a href="/documentation/articles/storage-dotnet-how-to-use-queues/">Almacenamiento en cola</a> para almacenar datos no estructurados.</li> <li>Utilice <a href="/documentation/articles/sql-database-dotnet-how-to-use/">Base de datos SQL</a> para almacenar datos relacionales.</li> </ul> </li> </ul>

  [Blob5]: ./media/storage-dotnet-how-to-use-blobs/blob5.png
  [Blob6]: ./media/storage-dotnet-how-to-use-blobs/blob6.png
  [Blob7]: ./media/storage-dotnet-how-to-use-blobs/blob7.png
  [Blob8]: ./media/storage-dotnet-how-to-use-blobs/blob8.png
  [Blob9]: ./media/storage-dotnet-how-to-use-blobs/blob9.png

  [Azure Storage]: http://msdn.microsoft.com/library/azure/gg433040.aspx
  [Azure Storage Team Blog]: http://blogs.msdn.com/b/windowsazurestorage/
  [Configuring Connection Strings]: http://msdn.microsoft.com/library/azure/ee758697.aspx
  [.NET client library reference]: http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409
  [REST API reference]: http://msdn.microsoft.com/library/azure/dd179355
 

<!---HONumber=Oct15_HO2-->