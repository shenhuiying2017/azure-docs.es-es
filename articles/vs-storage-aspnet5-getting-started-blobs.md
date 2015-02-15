<properties 
	pageTitle="Introducción a Almacenamiento de Azure" 
	description="" 
	services="storage" 
	documentationCenter="" 
	authors="kempb" 
	manager="douge" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="web" 
	ms.tgt_pltfrm="vs-getting-started" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/10/2014" 
	ms.author="kempb"/>

> [AZURE.SELECTOR]
> - [Introducción](/documentation/articles/vs-storage-aspnet5-getting-started-blobs/)
> - [¿Qué ha ocurrido?](/documentation/articles/vs-storage-aspnet5-what-happened/)

## Introducción a Almacenamiento de Azure (proyectos ASP.NET 5)

> [AZURE.SELECTOR]
> - [Blobs](/documentation/articles/vs-storage-aspnet5-getting-started-blobs/)
> - [Colas](/documentation/articles/vs-storage-aspnet5-getting-started-queues/)
> - [Tablas](/documentation/articles/vs-storage-aspnet5-getting-started-tables/)

El almacenamiento de blobs de Azure es un servicio para almacenar grandes cantidades de datos no estructurados a los que puede obtenerse acceso desde cualquier lugar del mundo a través de HTTP o HTTPS. Un solo blob puede tener cualquier tamaño. Los blobs pueden tener forma de imágenes, archivos de audio y vídeo, archivos sin procesar y archivos de documentos.

Para empezar, debe crear una cuenta de almacenamiento de Azure y, a continuación, cree uno o varios contenedores en el almacenamiento. Por ejemplo, puede crear un almacenamiento llamado "Scrapbook", a continuación crear contenedores en el almacenamiento denominados "images" para almacenar imágenes y "audio" para almacenar archivos de audio. Una vez creados los contenedores, puede cargar archivos de blob individuales a ellos. Consulte [Uso del almacenamiento de blobs en .NET](http://azure.microsoft.com/es-es/documentation/articles/storage-dotnet-how-to-use-blobs/ "How to use Blob Storage from .NET") para obtener más información sobre la manipulación de blobs mediante programación.

Para obtener acceso mediante programación a los blobs en los proyectos ASP.NET 5, deberá agregar los elementos siguientes, si no están presentes aún.

1. Agregue las siguientes declaraciones de espacio de nombres de código en la parte superior de todo archivo C# en el que desee obtener acceso al Almacenamiento de Azure mediante programación.

		using Microsoft.Framework.ConfigurationModel;
		using Microsoft.WindowsAzure.Storage;
		using Microsoft.WindowsAzure.Storage.Blob;
		using System.Threading.Tasks;

2. Utilice el comando siguiente para obtener los valores de configuración.

		 Configuration = new Configuration()
                .AddJsonFile("config.json")
                .AddEnvironmentVariables();

##### Obtención de la cadena de conexión de almacenamiento
Antes de realizar cualquier acción con un blob, debe obtener la cadena de conexión para la cuenta de almacenamiento en la que residirán los blobs. Puede usar el tipo **CloudStorageAccount** para representar la información de la cuenta de almacenamiento. Si está usando un proyecto ASP.NET 5, puede llamar al método get del objeto Configuration para obtener la información de la cuenta de almacenamiento y la cadena de conexión de almacenamiento de la configuración del servicio de Azure, como se muestra en el código siguiente.

	CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
      config.Get("MicrosoftAzureStorage:<storageAccountName>_AzureStorageConnectionString"));

##### Creación de un contenedor
Al igual que los archivos residen en carpetas, los blobs de almacenamiento residen en contenedores. Use un objeto **CloudBlobClient** para hacer referencia a un contenedor existente, o bien llame al método CreateCloudBlobClient() para crear un contenedor nuevo.

El código siguiente muestra cómo crear un contenedor de almacenamiento de blobs nuevo. El código crea primero un objeto **BlobClient** que permite el acceso a las funciones del objeto, como la creación de un contenedor de almacenamiento. Después, el código intenta hacer referencia a un contenedor de almacenamiento denominado "mycontainer". Si no encuentra ningún contenedor con ese nombre, lo crea.

**NOTA:** las API que realizan llamadas al almacenamiento de Azure en ASP.NET 5 son asincrónicas. Consulte [Programación asincrónica con Async y Await](http://msdn.microsoft.com/library/hh191443.aspx) para obtener más información. El código siguiente supone que se están usando métodos de programación asincrónica.

	// Create a blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Get a reference to a container named "mycontainer."
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // If "mycontainer" doesn't exist, create it.
    await container.CreateIfNotExistsAsync();    

De manera predeterminada, el nuevo contenedor es privado y debe especificar su clave de acceso de almacenamiento para descargar blobs de él. Si desea poner los archivos del contenedor a disposición de todo el mundo, puede convertir el contenedor en público utilizando el código siguiente:

	await container.SetPermissionsAsync(new BlobContainerPermissions
    {
        PublicAccess = BlobContainerPublicAccessType.Blob
    });

**NOTA:** use todo este código delante del código que aparece en las secciones siguientes.

##### Carga de un blob en un contenedor
Para cargar un archivo de blob en un contenedor, obtenga una referencia de contenedor y utilícela para obtener una referencia de blob. Una vez que disponga de la referencia de blob, puede cargar cualquier secuencia de datos en ella llamando al método **UploadFromStreamAsync()**. Esta operación crea el blob si no existe o lo sobrescribe si ya existe. En el siguiente ejemplo se muestra cómo cargar un blob en un contenedor creado anteriormente.

	// Get a reference to a blob named "myblob".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");            

    // Create or overwrite the "myblob" blob with the contents of a local file
    // named "myfile".
    using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
    {
        await blockBlob.UploadFromStreamAsync(fileStream);
    }

##### Enumeración de los blobs de un contenedor
Para enumerar los blobs de un contenedor, primero obtenga una referencia de contenedor. A continuación, llame al método **ListBlobsSegmentedAsync()** del contenedor para recuperar los blobs o los directorios que contiene. Para obtener acceso a las numerosas propiedades y métodos de una lista **IListBlobItem**, recuperada, debe convertir esta última en un objeto **CloudBlockBlob**, **CloudPageBlob** o **CloudBlobDirectory**. Si no conoce el tipo de blob, puede utilizar una comprobación de tipo para determinar el formato al que debe convertirlo. El código siguiente demuestra cómo recuperar y consultar el URI de cada elemento del contenedor.

	BlobContinuationToken token = null;
        do
        {
            BlobResultSegment resultSegment = await container.ListBlobsSegmentedAsync(token);
            token = resultSegment.ContinuationToken;

            foreach (IListBlobItem item in resultSegment.Results)
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
        } while (token != null);

Hay otras maneras de enumerar el contenido de un contenedor de blobs. Para obtener más información, consulte [Uso del almacenamiento de blobs en .NET](http://azure.microsoft.com/es-es/documentation/articles/storage-dotnet-how-to-use-blobs/#list-blob).

##### Descarga de un blob
Para descargar un blob, primero obtenga una referencia al blob y, a continuación, llame al método **DownloadToStreamAsync()**. En el siguiente ejemplo se usa el método **DownloadToStreamAsync()** para transferir el contenido del blob a un objeto de secuencia que, a continuación, puede guardar como archivo local.

	// Get a reference to a blob named "photo1.jpg".
	CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

	// Save the blob contents to a file named "myfile".
	using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
	{
    	await blockBlob.DownloadToStreamAsync(fileStream);
	}

Hay otras maneras de guardar blobs como archivos. Para obtener más información, consulte [Uso del almacenamiento de blobs en .NET](http://azure.microsoft.com/es-es/documentation/articles/storage-dotnet-how-to-use-blobs/#download-blobs).

##### Eliminación de un blob
Para eliminar un blob, primero obtenga una referencia al blob y, a continuación, llame al método **DeleteAsync()**.

	// Get a reference to a blob named "myblob.txt".
	CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");

	// Delete the blob.
	await blockBlob.DeleteAsync();

[Más información sobre Almacenamiento de Azure](http://azure.microsoft.com/documentation/services/storage/)
Consulte también [Explorar y administrar recursos de almacenamiento con el Explorador de servidores](http://msdn.microsoft.com/es-es/library/azure/ff683677.aspx) y [ASP.NET 5](http://www.asp.net/vnext).
<!--HONumber=42-->
