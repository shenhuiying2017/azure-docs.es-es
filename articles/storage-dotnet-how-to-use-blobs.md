<properties 
	pageTitle="Uso del almacenamiento de blobs en .NET | Azure" 
	description="Aprenda a usar el almacenamiento de blobs de Microsoft Azure para cargar, descargar, enumerar y eliminar contenidos de blobs. Los ejemplos están escritos en C#." 
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
	ms.topic="article" 
	ms.date="11/10/2014" 
	ms.author="tamram"/>

# Uso del almacenamiento de blobs en .NET

Esta guía demuestra cómo realizar algunas tareas comunes a través del servicio de almacenamiento de blobs de Azure. Los ejemplos están escritos en código C# y utilizan la biblioteca del cliente de almacenamiento de Azure para .NET. Dichas tareas comunes incluyen la **carga**, **enumeración**, **descarga** y **eliminación** de blobs. Para obtener más información acerca de los blobs, consulte la sección [Pasos siguientes][].

> [AZURE.NOTE] Esta guía se destina a la biblioteca de cliente de almacenamiento .NET de Azure 2.x y posterior. La versión recomendada es la biblioteca de cliente de almacenamiento 4.x, que está disponible a través de [NuGet](https://www.nuget.org/packages/WindowsAzure.Storage/) o como parte del [SDK de Azure para .NET](/es-es/downloads/). Consulte [Acceso al almacenamiento de blobs mediante programación][] que aparece a continuación para obtener más detalles sobre cómo obtener la Biblioteca de cliente de almacenamiento.

##Tabla de contenido

-   [Qué es el almacenamiento de blobs][]
-   [Conceptos][]
-   [Creación de una cuenta de almacenamiento de Azure][]
-   [Configuración de una cadena de conexión de almacenamiento][]
-   [Acceso al almacenamiento de blobs mediante programación][]
-   [Creación de un contenedor][]
-   [Carga de un blob en un contenedor][]
-   [Enumeración de los blobs de un contenedor][]
-   [Descarga de blobs][]
-   [Eliminación de blobs][]
-   [Enumeración de los blobs en las páginas de manera asincrónica][]
-   [Pasos siguientes][]

[AZURE.INCLUDE [howto-blob-storage](../includes/howto-blob-storage.md)]

[AZURE.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

[AZURE.INCLUDE [storage-configure-connection-string](../includes/storage-configure-connection-string.md)]

## <a name="configure-access"></a>Acceso al almacenamiento de blobs mediante programación

###Obtención del ensamblado 
Se recomienda el uso de NuGet para obtener el ensamblado `Microsoft.WindowsAzure.Storage.dll`. Haga clic con el botón secundario en el proyecto en el **Explorador de soluciones** y seleccione **Administrar paquetes de NuGet**.  Busque "WindowsAzure.Storage" en línea y haga clic en **Instalar** para instalar el paquete y las dependencias de Almacenamiento de Azure.

`Microsoft.WindowsAzure.Storage.dll` también se incluye en el SDK de Azure para .NET, que se puede descargar en el <a href="http://azure.microsoft.com/develop/net/#">Centro de desarrolladores de .NET</a>. El ensamblado se instala en el directorio  `%Program Files%\Microsoft SDKs\Windows Azure\.NET SDK\<sdk-version>\ref\`.

###Las declaraciones de los espacios de nombres
Agregan las siguientes declaraciones de espacios de nombres en la parte superior de cualquier archivo C# en el que desee obtener acceso al almacenamiento de Azure mediante programación:

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Blob;

Asegúrese de hacer referencia al ensamblado  `Microsoft.WindowsAzure.Storage.dll`.

###Recuperación de la cadena de conexión
Puede usar el tipo **CloudStorageAccount** para representar la información de su cuenta de almacenamiento. Si está utilizando unaplantilla de proyecto de Azure o hace referencia aMicrosoft.Azure.CloudConfigurationManager, puedeutilizar el tipo **CloudConfigurationManager** para recuperarla cadena de conexión de almacenamiento y la información de la cuentade almacenamiento de la configuración de servicios de Azure:

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

Si está creando una aplicación sin ninguna referencia a Microsoft.WindowsAzure.CloudConfigurationManager y la cadena de conexión está situada en el archivo `web.config` o `app.config`, como se muestra anteriormente, puede utilizar **ConfigurationManager** para recuperar dicha cadena. Deberá agregar una referencia a System.Configuration.dll a su proyecto, así como otra declaración de espacio de nombres para ella:

	using System.Configuration;
	...
	CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
		ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString);

El tipo **CloudBlobClient** le permite recuperar objetos que representan contenedores y blobs almacenados en el servicio de almacenamiento de blobs. El código siguiente crea un objeto **CloudBlobClient** utilizando el objeto de cuenta de almacenamiento recuperado anteriormente:

    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

###Dependencias ODataLib
Las dependencias ODataLib de la biblioteca de clientes de almacenamiento de .NET se resuelven mediante los paquetes de ODataLib (versión 5.0.2) disponibles a través de NuGet y no a través de los servicios de datos de WCF. A través de NuGet, es posible descargar directamente las bibliotecas ODataLib o bien hacer referencia a ellas con el código del proyecto. Los paquetes ODataLib específicos son [OData], [Edm] y [Spatial].

## <a name="create-container"> </a>Creación de un contenedor

Cada blob del almacenamiento de Azure debe residir en un contenedor. En este ejemplo se muestra cómo crear un contenedor si todavía no existe:

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

## <a name="upload-blob"></a>Carga de un blob en un contenedor

El almacenamiento de blobs de Azure admite blobs en bloques y en páginas. En la mayoría de los casos, se recomienda usar blobs en bloques.

Para cargar un archivo en un blob en bloques, obtenga una referencia de contenedor y utilícela para obtener una referencia de blob en bloques. Una vez que disponga de la referencia de blob, puede cargar cualquier secuencia de datos en ella mediante el método **UploadFromStream**. De este modo, se creará el blob si no existía anteriormente, o bien se sobrescribirá si ya existía. En el siguiente ejemplo se muestra cómo cargar un blob en un contenedor creado anteriormente.

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

##<a name="list-blob"></a>Enumeración de los blobs de un contenedor

Para enumerar los blobs de un contenedor, primero obtenga una referencia de contenedor. A continuación, puede utilizar el método **ListBlobs** del contenedor para recuperar los blobs y los directorios dentro de él. Para obtener acceso a las numerosas propiedades y métodos de una lista **IListBlobItem** recuperada, debe convertirla en un objeto **CloudBlockBlob**, **CloudPageBlob** o **CloudBlobDirectory**. Si se desconoce el tipo, puede realizar una comprobación de tipo para determinar el formato al que debe convertirla. El código siguiente demuestra cómo recuperar y consultar el URI de cada elemento del contenedor `photos`:

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

Como se muestra anteriormente, en el servicio de blobs los contenedores también incluyen directorios. De este modo, es posible organizar los blobs en una estructura similar a la estructura de carpetas. Por ejemplo, observe el siguiente conjunto de blobs en bloques incluidos en un contenedor denominado `photos`:

	photo1.jpg
	2010/architecture/description.txt
	2010/architecture/photo3.jpg
	2010/architecture/photo4.jpg
	2011/architecture/photo5.jpg
	2011/architecture/photo6.jpg
	2011/architecture/description.txt
	2011/photo7.jpg

Al llamar a **ListBlobs** en el contenedor 'photos' (como en el ejemplo anterior), la lista que se obtenga contendrá objetos **CloudBlobDirectory** y **CloudBlockBlob** que representan los directorios y los blobs que existen en el nivel superior. Este sería el resultado:

	Directory: https://<accountname>.blob.core.windows.net/photos/2010/
	Directory: https://<accountname>.blob.core.windows.net/photos/2011/
	Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg


También existe la opción de establecer el parámetro **UseFlatBlobListing** del método **ListBlobs** como 
**true**. De este modo, todos los blobs aparecerían como **CloudBlockBlob**, independientemente el directorio. Esa sería la llamada a **ListBlobs**:

    // Loop over items within the container and output the length and URI.
	foreach (IListBlobItem item in container.ListBlobs(null, true))
	{
	   ...
	}
 y estos serían los resultados:

	Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2010/architecture/description.txt
	Block blob of length 314618: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo3.jpg
	Block blob of length 522713: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo4.jpg
	Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2011/architecture/description.txt
	Block blob of length 419048: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo5.jpg
	Block blob of length 506388: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo6.jpg
	Block blob of length 399751: https://<accountname>.blob.core.windows.net/photos/2011/photo7.jpg
	Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg

Para obtener más información, consulte [CloudBlobContainer.ListBlobs][].

## <a name="download-blobs"></a>Descarga de blobs

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

##<a name="delete-blobs"></a>Eliminación de blobs

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


##<a name="list-blobs-async"></a>Enumeración de los blobs en páginas de manera asincrónica

Si enumera un gran número de blobs o desea controlar el número de resultados que devuelve en una operación de listado, puede enumerar blobs en páginas de resultados. En este ejemplo se muestra cómo devolver resultados en páginas asincrónicamente de forma que la ejecución no se bloquee mientras se espera a devolver un conjunto grande de resultados.

En este ejemplo se muestra un listado de blobs plano, pero también puede realizar un listado jerárquico estableciendo el parámetro  `useFlatBlobListing` del método **ListBlobsSegmentedAsync** en  `false`.

Dado que el método de ejemplo llama a un método asincrónico, debe ir precedido por la palabra clave  `async` y debe devolver un objeto **Task**. La palabra clave await especificada para el método **ListBlobsSegmentedAsync** suspende la ejecución del método de ejemplo hasta que la tarea de enumeración se completa.

    async public static Task ListBlobsSegmentedInFlatListing()
    {
        // Retrieve storage account from connection string.
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
            CloudConfigurationManager.GetSetting("StorageConnectionString"));

        // Create the blob client.
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

        // Retrieve reference to a previously created container.
        CloudBlobContainer container = blobClient.GetContainerReference("myblobs");

        //List blobs in pages.
        Console.WriteLine("List blobs in pages:");

        //List blobs with a paging size of 10, for the purposes of the example. 
		//The first call does not include the continuation token.
        BlobResultSegment resultSegment = await container.ListBlobsSegmentedAsync(
                "", true, BlobListingDetails.All, 10, null, null, null);

        //Enumerate the result segment returned.
        int i = 0;
        if (resultSegment.Results.Count<IListBlobItem>() > 0) { Console.WriteLine("Page {0}:", ++i); }
        foreach (var blobItem in resultSegment.Results)
        {
            Console.WriteLine("\t{0}", blobItem.StorageUri.PrimaryUri);
        }
        Console.WriteLine();

        //Get the continuation token, if there are additional pages of results.
        BlobContinuationToken continuationToken = resultSegment.ContinuationToken;

        //Check whether there are more results and list them in pages of 10 while a continuation token is returned.
        while (continuationToken != null)
        {
            //This overload allows control of the page size. 
			//You can return all remaining results by passing null for the maxResults parameter, 
            //or by calling a different overload.
            resultSegment = await container.ListBlobsSegmentedAsync(
					"", true, BlobListingDetails.All, 10, continuationToken, null, null);
            if (resultSegment.Results.Count<IListBlobItem>() > 0) { Console.WriteLine("Page {0}:", ++i); }
            foreach (var blobItem in resultSegment.Results)
            {
                Console.WriteLine("\t{0}", blobItem.StorageUri.PrimaryUri);
            }
            Console.WriteLine();

            //Get the next continuation token.
            continuationToken = resultSegment.ContinuationToken;
        }
    }

## <a name="next-steps"></a>Pasos siguientes

Ahora que está familiarizado con los aspectos básicos del almacenamiento de blobs, siga estos vínculos para obtener más información acerca de cómo realizar tareas de almacenamiento más complejas.
<ul>
<li>Consulte la documentación de referencia del servicio de blobs para obtener información detallada acerca de las API disponibles:
  <ul>
    <li><a href="http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409">Referencia acerca de la biblioteca de clientes de almacenamiento para .NET</a>
    </li>
    <li><a href="http://msdn.microsoft.com/library/windowsazure/dd179355">Referencia de API de REST</a></li>
  </ul>
</li>
<li>Obtenga información acerca de las tareas más avanzadas que se pueden realizar con el almacenamiento de Azure en <a href="http://msdn.microsoft.com/library/windowsazure/gg433040.aspx">Almacenamiento de datos y acceso a los mismos en Azure</a>.</li>
<li>Aprenda a simplificar el código que escriba para trabajar con el Almacenamiento de Azure mediante la <a href="../websites-dotnet-webjobs-sdk/">SDK de WebJobs de Azure.</li>
<li>Consulte más guías de características para obtener información acerca de otras opciones del almacenamiento de datos en Azure.
  <ul>
    <li>Utilice <a href="/es-es/documentation/articles/storage-dotnet-how-to-use-tables/">Almacenamiento de tablas</a> para almacenar datos estructurados.</li>
    <li>Utilice <a href="/es-es/documentation/articles/storage-dotnet-how-to-use-queues/">Almacenamiento en cola</a> para almacenar datos no estructurados.</li>
    <li>Utilice <a href="/es-es/documentation/articles/sql-database-dotnet-how-to-use/">Base de datos SQL</a> para almacenar datos relacionales.</li>
  </ul>
</li>
</ul>

  [Pasos siguientes]: #next-steps
  [Qué es el almacenamiento de blobs]: #what-is
  [Conceptos]: #concepts
  [Creación de una cuenta de almacenamiento de Azure]: #create-account
  [Configuración de una cadena de conexión de almacenamiento]: #setup-connection-string
  [Acceso al almacenamiento de blobs mediante programación]: #configure-access
  [Creación de un contenedor]: #create-container
  [Carga de un blob en un contenedor]: #upload-blob
  [Enumeración de los blobs de un contenedor]: #list-blob
  [Descarga de blobs]: #download-blobs
  [Eliminación de blobs]: #delete-blobs
  [Enumeración de los blobs en las páginas de manera asincrónica]: #list-blobs-async
  [Blob5]: ./media/storage-dotnet-how-to-use-blobs/blob5.png
  [Blob6]: ./media/storage-dotnet-how-to-use-blobs/blob6.png
  [Blob7]: ./media/storage-dotnet-how-to-use-blobs/blob7.png
  [Blob8]: ./media/storage-dotnet-how-to-use-blobs/blob8.png
  [Blob9]: ./media/storage-dotnet-how-to-use-blobs/blob9.png
  
  [Almacenamiento de datos y acceso a los mismos en Azure]: http://msdn.microsoft.com/library/windowsazure/gg433040.aspx
  [Blog del equipo de almacenamiento de Azure]: http://blogs.msdn.com/b/windowsazurestorage/
  [Configurar cadenas de conexión]: http://msdn.microsoft.com/library/windowsazure/ee758697.aspx
  [Referencia a la biblioteca de clientes de .NET]: http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409
  [Referencia de API de REST]: http://msdn.microsoft.com/library/windowsazure/dd179355
  [OData]: http://nuget.org/packages/Microsoft.Data.OData/5.0.2
  [Edm]: http://nuget.org/packages/Microsoft.Data.Edm/5.0.2
  [Spatial]: http://nuget.org/packages/System.Spatial/5.0.2
<!--HONumber=42-->
