<properties urlDisplayName="Blob Service" pageTitle="Uso del almacenamiento de blobs en .NET | Azure" metaKeywords="Get started Azure blob   Azure unstructured data   Azure unstructured storage   Azure blob   Azure blob storage   Azure blob .NET   Azure blob C#   Azure blob C#" description="Aprenda a usar el almacenamiento de blobs de Microsoft Azure para cargar, descargar, enumerar y eliminar contenidos de blobs. Los ejemplos están escritos en C#." metaCanonical="" disqusComments="1" umbracoNaviHide="1" services="storage" documentationCenter=".NET" title="How to use Microsoft Azure Blob storage in .NET" authors="tamram" manager="adinah" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="11/10/2014" ms.author="tamram" />

# Uso del almacenamiento de blobs en .NET

Esta guía demuestra cómo realizar algunas tareas comunes a través del
servicio de almacenamiento de blobs de Azure. Los ejemplos están escritos en C\# y
utilizan la biblioteca del cliente de Almacenamiento de Azure para .NET. Los escenarios cubiertos incluyen
**carga**, **escucha**, **descarga** y **eliminación** de blobs. Para
obtener más información acerca de los blobs, consulte la sección [Pasos siguientes][].

> [WACOM.NOTE] Esta guía se destina a la biblioteca de cliente de almacenamiento .NET de Azure 2.x y posterior. La versión recomendada es Biblioteca de cliente de almacenamiento 4.x, que se encuentra disponible a través de [NuGet](https://www.nuget.org/packages/WindowsAzure.Storage/) o como parte del [SDK de Azure para .NET](/en-us/downloads/). Consulte [Acceso  al almacenamiento de blobs mediante programación][] que aparece a continuación para obtener más detalles sobre cómo obtener la Blioteca de cliente de almacenamiento.

##Tabla de contenido

-   [¿Qué es el almacenamiento de blobs?][]
-   [Conceptos][]
-   [Creación de una cuenta de Almacenamiento de Azure][]
-   [Configuración de una cadena de conexión de almacenamiento][]
-   [Acceso al almacenamiento de blobs mediante programación][]
-   [Creación de un contenedor][]
-   [Carga de un blob en un contenedor][]
-   [Enumeración de los blobs de un contenedor][]
-   [Descarga de blobs][]
-   [Eliminación de blobs][]
-   [Enumeración de blobs en páginas asincrónica][]
-   [Pasos siguientes][]

[WACOM.INCLUDE [howto-blob-storage](../includes/howto-blob-storage.md)]

##<a name="create-account"></a>Creación de una cuenta de Almacenamiento de Azure
[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

##<a name="setup-connection-string"></a>Configuración de una cadena de conexión de almacenamiento

[WACOM.INCLUDE [storage-configure-connection-string](../includes/storage-configure-connection-string.md)]

## <a name="configure-access"> </a>Reproducción de almacenamiento de blobs mediante programación

###Obtención del ensamblado
Le recomendamos que use NuGet para obtener el ensamblado `Microsoft.WindowsAzure.Storage.dll`. Haga clic con el botón secundario en el proyecto en el **Explorador de soluciones** y elija **Administrar paquetes de NuGet**.  Busque "WindowsAzure.Storage" en línea y haga clic en **Instalar** para instalar las dependencias y el paquete de Almacenamiento de Azure.

`Microsoft.WindowsAzure.Storage.dll` también se incluye en el SDK de Azure para .NET, que se puede descargar en <a href="http://www.windowsazure.com/en-us/develop/net/#">.NET Developer Center</a>. El ensamblado está instalado en el directorio `%Program Files%\Microsoft SDKs\Windows Azure\.NET SDK\<versión del sdk>\ref\`.

###Declaraciones de espacio de nombres
Agregue las siguientes declaraciones de espacio de nombres en la parte superior de cualquier archivo C\#
en el que desee obtener acceso al almacenamiento de Azure mediante programación:

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
	using Microsoft.WindowsAzure.Storage.Blob;

Asegúrese de hacer referencia al ensamblado `Microsoft.WindowsAzure.Storage.dll`.

###Recuperación de la cadena de conexión
Puede usar el tipo **CloudStorageAccount** para mostrar 
la información de la cuenta de Almacenamiento. Si está en una 
plantilla de proyectos de Azure y/o tiene una referencia a 
Microsoft.WindowsAzure.CloudConfigurationManager, puede 
utilizar el tipo **CloudConfigurationManager**
para recuperar la cadena de conexión de almacenamiento y la información de
la cuenta de almacenamiento de la configuración de servicios de Azure:

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

Si está creado una aplicación sin ninguna referencia a Microsoft.MicrosoftAzure.CloudConfigurationManager y la cadena de conexión está situada en el archivo "app.config", como se indicó anteriormente, puede usar **ConfigurationManager** para recuperar la cadena de conexión.  Deberá agregar una referencia a System.Configuration.dll a su proyecto, así como otra declaración de espacio de nombres para ella:

	using System.Configuration;
	...
	CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
		ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString);

Un tipo **CloudBlobClient** le permite recuperar objetos que representan
contenedores y blobs almacenados en el servicio de almacenamiento de blobs. El
siguiente código crea un objeto **CloudBlobClient** que usa el objeto que utiliza el objeto de
cuenta de almacenamiento, tal como indicamos que funciona la recuperación anteriormente:

    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

###Dependencias ODataLib
Las dependencias ODataLib de la biblioteca de clientes de almacenamiento para .NET se resuelven mediante los paquetes ODataLib (versión 5.0.2) disponibles a través de NuGet y no a través de los servicios de datos de WCF.  A través de NuGet, es posible descargar directamente las bibliotecas ODataLib o bien hacer referencia a ellas con el código del proyecto.  Los paquetes ODataLib específicos son [OData], [Edm] y [Spatial].

## <a name="create-container"> </a>Reproducción de Creación de un contenedor

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

## <a name="upload-blob"> </a>Reproducción de un blob en un contenedor

El almacenamiento de blobs de Azure admite blobs en bloques y en páginas.  En la mayoría de los casos, se recomienda usar blobs en bloques.

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

##<a name="list-blob"> </a>Reproducción de los blobs de un contenedor

Para enumerar los blobs de un contenedor, primero obtenga una referencia de contenedor. A continuación, puede utilizar el método **ListBlobs** del contenedor para recuperar los blobs y los directorios dentro de él. Para obtener acceso a las numerosas propiedades y métodos de una lista **IListBlobItem** devuelta, debe convertirla en un objeto **CloudBlockBlob** **CloudPageBlob** o **CloudBlobDirectory**.  Si se desconoce el tipo, puede realizar una comprobación de tipo para determinar el formato al que se debe convertir.  El código siguiente demuestra cómo recuperar y consultar el URI de cada elemento en el 
elemento contenedor "de animales":

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

Al llamar a **ListBlobs**, en el contenedor 'photos' (como en el ejemplo anterior), la recopilación devuelta contendrá objetos **CloudBlobDirectory** y **CloudBlockBlob**
que representan los directorios y blobs contenidos en el nivel superior. Este sería el resultado:

	Directorio: https://<accountname>.blob.core.windows.net/photos/2010/
	Directorio: https://<accountname>.blob.core.windows.net/photos/2011/
	Blob en bloque de longitud 505623:: https://<accountname>.blob.core.windows.net/photos/photo1.jpg


También existe la opción de establecer el parámetro **UseFlatBlobListing** de un método **ListBlobs** en **true**. De este modo, todos los blobs aparecerían como **CloudBlockBlob**, independientemente el directorio.  Esa sería la llamada a **ListBlobs**:

    // Loop over items within the container and output the length and URI.
	foreach (IListBlobItem item in container.ListBlobs(null, true))
	{
	   ...
	}

y estos serían los resultados:

	Blob en bloque de longitud 4:: https://<accountname>.blob.core.windows.net/photos/2010/architecture/description.txt
	Blob en bloque de longitud 314618:: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo3.jpg
	Blob en bloque de longitud 522713:: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo4.jpg
	Blob en bloque de longitud 4:: https://<accountname>.blob.core.windows.net/photos/2011/architecture/description.txt
	Blob en bloque de longitud 419048:: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo5.jpg
	Blob en bloque de longitud 506388:: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo6.jpg
	Blob en bloque de longitud 399751:: https://<accountname>.blob.core.windows.net/photos/2011/photo7.jpg
	Blob en bloque de longitud 505623:: https://<accountname>.blob.core.windows.net/photos/photo1.jpg

Para obtener más información, consulte [CloudBlobContainer.ListBlobs][].

## <a name="download-blobs"> </a>Descarga de blobs

Para descargar blobs, primero recupere una referencia de blob y, a continuación, llame al método **DownloadToStream**. El siguiente
ejemplo usa el método **DownloadToStream** para transferir el contenido del
blob a un objeto de secuencia que, a continuación, puede guardar en un archivo local.

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

##<a name="delete-blobs"> </a>Eliminación de blobs

Para eliminar un blob, primero obtenga una referencia de blob y, a continuación, llame al método
**Delete** que hay en ella.

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


##<a name="list-blobs-async"> </a>Enumeración de blobs en páginas asincrónica

Si enumera un gran número de blobs o desea controlar el número de resultados que devuelve en una operación de listado, puede enumerar blobs en páginas de resultados. En este ejemplo se muestra cómo devolver resultados en páginas asincrónicamente de forma que la ejecución no se bloquee mientras se espera a devolver un conjunto grande de resultados.

En este ejemplo se muestra un listado de blobs plano, pero también puede realizar un listado jerárquico estableciendo en parámetro "useFlatBlobListing" del método **ListBlobsSegmentedAsync** en `false`.

Dado que el método de ejemplo llama a un método asincrónico, debe ir precedido por la palabra clave "asyn" y debe devolver un objeto **Task**. La palabra clave await especificada para el método **ListBlobsSegmentedAsync** suspende la ejecución del método de ejemplo hasta que la tarea de enumeración se completa.

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
    <li><a href="http://msdn.microsoft.com/en-us/library/windowsazure/dd179355">Referencia de la API REST</a></li>
  </ul>
</li>
<li>Obtenga información acerca de las tareas más avanzadas que se pueden realizan con Almacenamiento de Azure en <a href="http://msdn.microsoft.com/en-us/library/windowsazure/gg433040.aspx">Almacenamiento de datos y acceso a los mismos en Azure</a>.</li>
<li>Obtenga información acerca de cómo trabajar con Almacenamiento de Azure en procesos de back-end para Sitios web de Azure en <a href="/en-us/documentation/articles/websites-dotnet-webjobs-sdk-get-started/">Introducción al SDK de WebJobs de Azure</a>.</li>
<li>Consulte más guías de características para obtener información acerca de otras opciones del almacenamiento de datos en Azure.
  <ul>
    <li>Uso de <a href="/en-us/documentation/articles/storage-dotnet-how-to-use-tables/">Almacenamiento de tablas</a> para almacenar datos estructurados.</li>
    <li>Uso de <a href="/en-us/documentation/articles/storage-dotnet-how-to-use-queues/">Almacenamiento de colas</a> para almacenar datos no estructurados.</li>
    <li>Uso de <a href="/en-us/documentation/articles/sql-database-dotnet-how-to-use/">Base de datos SQL</a> para almacenar datos relacionales.</li>
  </ul>
</li>
</ul>

  [Pasos siguientes]: #next-steps
  [¿Qué es el almacenamiento de blobs?]: #what-is
  [Conceptos]: #concepts
  [Creación de una cuenta de almacenamiento de Azure]: #create-account
  [Configuración de una cadena de conexión de almacenamiento]: #setup-connection-string
  [Obtener acceso al almacenamiento de blobs mediante programación]: #configure-access
  [Creación de un contenedor]: #create-container
  [Carga de un blob en un contenedor]: #upload-blob
  [Enumeración de los blobs en un contenedor]: #list-blob
  [Descarga de blobs]: #download-blobs
  [Eliminar blobs]: #delete-blobs
  [Enumeración de blobs en páginas de manera asincrónica]: #list-blobs-async
  [Blob5]: ./media/storage-dotnet-how-to-use-blobs/blob5.png
  [Blob6]: ./media/storage-dotnet-how-to-use-blobs/blob6.png
  [Blob7]: ./media/storage-dotnet-how-to-use-blobs/blob7.png
  [Blob8]: ./media/storage-dotnet-how-to-use-blobs/blob8.png
  [Blob9]: ./media/storage-dotnet-how-to-use-blobs/blob9.png
  
  [Almacenamiento u acceso a los datos en Azure]: http://msdn.microsoft.com/en-us/library/windowsazure/gg433040.aspx
  [Blog del equipo de Almacenamiento de Azure]: http://blogs.msdn.com/b/windowsazurestorage/
  [Configuración de cadenas de conexión]: http://msdn.microsoft.com/en-us/library/windowsazure/ee758697.aspx
  [Referencia a la biblioteca de clases de .NET]: http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409
  [Referencia de API de REST]: http://msdn.microsoft.com/en-us/library/windowsazure/dd179355
  [OData]: http://nuget.org/packages/Microsoft.Data.OData/5.0.2
  [Edm]: http://nuget.org/packages/Microsoft.Data.Edm/5.0.2
  [Spatial]: http://nuget.org/packages/System.Spatial/5.0.2

<!--HONumber=35.1-->
