<properties urlDisplayName="Blob Service" pageTitle="Uso del almacenamiento de blobs en .NET | Azure" metaKeywords="Get started Azure blob   Azure unstructured data   Azure unstructured storage   Azure blob   Azure blob storage   Azure blob .NET   Azure blob C#   Azure blob C#" description="Learn how to use Microsoft Azure Blob storage to upload,  download, list, and delete blob content. Samples are written in C#." metaCanonical="" disqusComments="1" umbracoNaviHide="1" services="storage" documentationCenter=".NET" title="How to use Microsoft Azure Blob storage in .NET" authors="tamram" manager="adinah" editor="cgronlun" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="tamram" />

# Uso del almacenamiento de blobs en .NET

Esta guía muestra cómo realizar algunas tareas comunes a través del
servicio de almacenamiento de blobs de Azure. Los ejemplos están escritos en C\# y
usan la biblioteca del cliente de almacenamiento de Azure para .NET. Entre los escenarios descritos se incluyen
**cargar**, **enumerar**, **descargar** y **eliminar** blobs. Para
obtener más información acerca de los blobs, consulte la sección [Pasos siguientes][Pasos siguientes].

> [WACOM.NOTE] Esta guía se destina a la biblioteca de cliente de almacenamiento .NET de Azure 2.x y posterior. La versión recomendada es la biblioteca de cliente de almacenamiento 4.x, que está disponible a través de [NuGet](https://www.nuget.org/packages/WindowsAzure.Storage/) o como parte del [SDK de Azure para .NET](/es-es/downloads/). Consulte [Acceso al almacenamiento de blobs mediante programación][Acceso al almacenamiento de blobs mediante programación] a continuación para obtener más información acerca de la obtención de una biblioteca de cliente de almacenamiento.

##Tabla de contenido

-   [Qué es el almacenamiento de blobs][Qué es el almacenamiento de blobs]
-   [Conceptos][Conceptos]
-   [Creación de una cuenta de almacenamiento de Azure][Creación de una cuenta de almacenamiento de Azure]
-   [Configuración de una cadena de conexión de almacenamiento][Configuración de una cadena de conexión de almacenamiento]
-   [Direccionamiento del almacenamiento de blobs mediante programación][Direccionamiento del almacenamiento de blobs mediante programación]
-   [Direccionamiento del un contenedor][Direccionamiento del un contenedor]
-   [Direccionamiento del un blob en un contenedor][Direccionamiento del un blob en un contenedor]
-   [Direccionamiento del los blobs de un contenedor][Direccionamiento del los blobs de un contenedor]
-   [Direccionamiento del blobs][Direccionamiento del blobs]
-   [Direccionamiento del blobs][Direccionamiento del blobs]
-   [Direccionamiento del Enumeración de blobs en páginas asincrónicamente][Direccionamiento del Enumeración de blobs en páginas asincrónicamente]
-   [Pasos siguientes][Pasos siguientes]

[WACOM.INCLUDE [howto-blob-storage](../includes/howto-blob-storage.md)]

##<a name="create-account"></a><span  class="short-header">Creación de una cuenta de almacenamiento de Azure</span>
[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

##<a name="setup-connection-string"></a><span  class="short-header">Configuración de una cadena de conexión de almacenamiento</span>

[WACOM.INCLUDE [storage-configure-connection-string](../includes/storage-configure-connection-string.md)]

## <a name="configure-access"> </a><span  class="short-header">Direccionamiento del almacenamiento de blobs mediante programación</span>

###Obtención del ensamblado
Le recomendamos que utilice NuGet para obtener el ensamblado `Microsoft.WindowsAzure.Storage.dll`. Haga clic con el botón secundario en el proyecto, en el **Explorador de soluciones**, y elija **Administrar paquetes de NuGet**.  Busque "Azure.Storage" en línea y haga clic en **Instalar** para instalar el paquete y las dependencias de almacenamiento de Azure.

`Microsoft.Azure.Storage.dll` también se incluye en el SDK de Azure para .NET, que se puede descargar en <a href="http://www.windowsazure.com/es-es/develop/net/#">.NET Developer Center</a>. El ensamblado se instala en el directorio `%Program Files%\Microsoft SDKs\Windows Azure\.NET SDK\<sdk-version>\ref\`.

###Declaraciones de espacio de nombres
Agregue las siguientes declaraciones de espacio de nombres en la parte superior de todo archivo C\#
en el que desee obtener acceso al almacenamiento de Azure mediante programación:

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
	using Microsoft.WindowsAzure.Storage.Blob;

Asegúrese de hacer referencia al ensamblado `Microsoft.WindowsAzure.Storage.dll`.

###Recuperación de la cadena de conexión
Puede utilizar el tipo **CloudStorageAccount** para representar 
la información de la cuenta de almacenamiento. Si está utilizando una 
plantilla de proyecto de Azure o hace referencia a 
Microsoft.WindowsAzure.CloudConfigurationManager, 
puede utilizar el tipo **CloudConfigurationManager**
para recuperar la cadena de conexión de almacenamiento y la información de la cuenta
de almacenamiento de la configuración de servicios de Azure:

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

Si está creando una aplicación sin ninguna referencia a Microsoft.WindowsAzure.CloudConfigurationManager y la cadena de conexión está situada en el archivo `web.config` o `app.config` como se muestra anteriormente, puede utilizar **ConfigurationManager** para recuperar dicha cadena.  Deberá agregar una referencia a System.Configuration.dll a su proyecto, así como otra declaración de espacio de nombres para ella:

	using System.Configuration;
	...
	CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
		ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString);

El tipo **CloudBlobClient** le permite recuperar objetivos que representan
contenedores y blobs almacenados en el servicio de almacenamiento de blobs. El
código siguiente crea un objeto **CloudBlobClient** utilizando el objeto de cuenta de 
almacenamiento recuperado anteriormente:

    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

###Dependencias ODataLib
Las dependencias ODataLib de la biblioteca de clientes de almacenamiento para .NET se resuelven mediante los paquetes ODataLib (versión 5.0.2) disponibles a través de NuGet y no a través de los servicios de datos de WCF.  A través de NuGet, es posible descargar directamente las bibliotecas ODataLib o bien hacer referencia a ellas con el código del proyecto.  Los paquetes ODataLib son [OData], [Edm] y [Spatial].

## <a name="create-container"> </a><span  class="short-header">Direccionamiento del un contenedor</span>

Todos los blobs del almacenamiento de Azure deben residir en un contenedor. Este ejemplo muestra cómo crear un contenedor si todavía no existe:

    // Recuperar cuenta de almacenamiento desde cadena de conexión.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Cree el cliente blob.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Recupere una referencia a un contenedor. 
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Cree el contenedor si todavía no existe.
    container.CreateIfNotExists();

De manera predeterminada, el nuevo contenedor es privado y debe especificar su
clave de acceso de almacenamiento para descargar blobs
de él. Si desea poner los archivos del contenedor a disposición
de todo el mundo, puede convertir el contenedor en público utilizando el código
siguiente:

    container.SetPermissions(
        new BlobContainerPermissions { PublicAccess = 
 	    BlobContainerPublicAccessType.Blob }); 

Cualquier usuario de Internet puede ver los blobs de los contenedores públicos, pero solo
es posible modificarlos o eliminarlos si se dispone de la clave de acceso apropiada.

## <a name="upload-blob"> </a><span  class="short-header">Direccionamiento del un blob en un contenedor</span>

El almacenamiento de blobs de Azure admite blobs en bloques y en páginas.  En la mayoría de los casos, se recomienda usar blobs en bloques.

Para cargar un archivo en un blob en bloques, obtenga una referencia de contenedor y utilícela para obtener
una referencia de blob en bloques. Una vez que disponga de la referencia de blob, puede cargar cualquier
secuencia de datos en ella mediante el método **UploadFromStream**. Esta operación creará el blob si todavía no existe,
o bien lo sobrescribirá si ya existe. En el siguiente ejemplo se muestra cómo cargar un blob en un contenedor y presupone que ya se ha creado el contenedor.

    // Recuperar cuenta de almacenamiento desde cadena de conexión.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Cree el cliente blob.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Recupere una referencia a un contenedor creado previamente.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Recupere una referencia a un blob denominado "myblob".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");

    // Cree o sobrescriba el blob "myblob" con contenidos desde un archivo local.
    using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
    {
        blockBlob.UploadFromStream(fileStream);
    } 

##<a name="list-blob"> </a><span  class="short-header">Direccionamiento del los blobs de un contenedor</span>

Para enumerar los blobs de un contenedor, primero obtenga una referencia de contenedor. A
continuación, puede utilizar el método **ListBlobs** del contenedor para recuperar los blobs o los directorios
que contiene. Para obtener acceso a las numerosas propiedades y métodos de una 
lista **IListBlobItem**recuperada, debe convertirla en un objeto **CloudBlockBlob**,  
**CloudPageBlob** o **CloudBlobDirectory**.  Si se desconoce el tipo, puede realizar una 
comprobación de tipo para determinar el formato al que debe convertirla.  El código siguiente 
demuestra cómo recuperar y consultar el URI de cada elemento del 
contenedor `photos`:

    // Recuperar cuenta de almacenamiento desde cadena de conexión.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Cree el cliente blob. 
	CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

	// Recupere una referencia a un contenedor creado previamente.
	CloudBlobContainer container = blobClient.GetContainerReference("photos");

	// Aplique bucle sobre los elementos dentro del contenedor y consulte la longitud y el URI.
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

Como se muestra anteriormente, en el servicio de blobs los contenedores también incluyen
directorios. De este modo, es posible organizar los blobs en una estructura similar a la estructura de
carpetas. Por ejemplo, observe el siguiente conjunto de blobs en bloques incluidos
en un contenedor denominado 'photos':

	photo1.jpg
	2010/architecture/description.txt
	2010/architecture/photo3.jpg
	2010/architecture/photo4.jpg
	2011/architecture/photo5.jpg
	2011/architecture/photo6.jpg
	2011/architecture/description.txt
	2011/photo7.jpg

Al llamar a **ListBlobs** en el contenedor 'photos' (como en el ejemplo anterior), la recopilación devuelta
contendrá objetos **CloudBlobDirectory** y **CloudBlockBlob**
que representan los directorios y los blobs que existen en el nivel superior. Este sería el resultado:

	Directory: https://<accountname>.blob.core.windows.net/photos/2010/
	Directory: https://<accountname>.blob.core.windows.net/photos/2011/
	Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg


También existe la opción de establecer el parámetro **UseFlatBlobListing** del método **ListBlobs** en  
**true**. De este modo, todos los blobs aparecerían como **CloudBlockBlob**,
con independencia del directorio.  Esta sería la llamada a **ListBlobs**:

    // Aplique bucle sobre los elementos dentro del contenedor y consulte la longitud y el URI.
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

Para obtener más información, consulte [CloudBlobContainer.ListBlobs][CloudBlobContainer.ListBlobs].

## <a name="download-blobs"> </a><span  class="short-header">Direccionamiento del blobs</span>

Para descargar blobs, primero recupere una referencia de blob y, a continuación, llame al método **DownloadToStream**. En el ejemplo
siguiente, se usa el método **DownloadToStream** para transferir el contenido del blob
a un objeto de secuencia que, a continuación, puede guardar en un archivo local.

    // Recuperar cuenta de almacenamiento desde cadena de conexión.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Cree el cliente blob.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Recupere una referencia a un contenedor creado previamente.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Recupere una referencia a un blob denominado "photo1.jpg".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

    // Guarde contenido de blob en un archivo.
    using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
    {
        blockBlob.DownloadToStream(fileStream);
    } 

También puede usar el método **DownloadToStream** para descargar el contenido de un blob en forma de cadena de texto.

	// Recuperar cuenta de almacenamiento desde cadena de conexión.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Cree el cliente blob.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Recupere una referencia a un contenedor creado previamente.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

	// Recupere una referencia a un blob denominado "myblob.txt".
	CloudBlockBlob blockBlob2 = container.GetBlockBlobReference("myblob.txt");

	string text;
	using (var memoryStream = new MemoryStream())
	{
		blockBlob2.DownloadToStream(memoryStream);
		text = System.Text.Encoding.UTF8.GetString(memoryStream.ToArray());
	}

##<a name="delete-blobs"> </a><span  class="short-header">Direccionamiento del blobs</span>

Para eliminar un blob, obtenga una referencia del blob y llame al método
**Delete**.

    // Recuperar cuenta de almacenamiento desde cadena de conexión.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Cree el cliente blob.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Recupere una referencia a un contenedor creado previamente.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Recuperar una referencia a un blob denominado "myblob.txt".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");

    // Elimine el blob.
    blockBlob.Delete(); 


##<a name="list-blobs-async"> </a><span  class="short-header">Direccionamiento del Enumeración de blobs en páginas asincrónicamente</span> 

Si está enumerando un gran número de blobs o si desea controlar el número de resultados que devuelve en una operación de enumeración, puede enumerar blobs en las páginas de resultados. Este ejemplo muestra cómo devolver resultados asincrónicamente en las páginas, para que la ejecución no se bloquee mientras se espera devolver un gran conjunto de resultados.

En este ejemplo se muestra una enumeración de blobs sin formatos, pero también puede ejecutar una enumeración jerárquica establecimiento el parámetro `useFlatBlobListing` del método **ListBlobsSegmentedAsync** en `false`.

Como el método de ejemplo llama a un método asincrónico, debe ir precedido con la palabra clave `async` y debe devolver un objeto **Task**. La palabra clave await especificada para el método **ListBlobsSegmentedAsync** suspende la ejecución del método de muestra hasta que se complete la tarea de enumeración.

    async public static Task ListBlobsSegmentedInFlatListing()
    {
        // Recuperar cuenta de almacenamiento desde cadena de conexión.
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
            CloudConfigurationManager.GetSetting("StorageConnectionString"));

        // Cree el cliente blob.
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

        // Recupere una referencia a un contenedor creado previamente.
        CloudBlobContainer container = blobClient.GetContainerReference("myblobs");

        //Enumerar blobs en páginas.
        Console.WriteLine("List blobs in pages:");

        //Enumerar blobs con un tamaño de paginación de 10, para el objetivo del ejemplo. 
		//La primera llamada no incluye el token de continuación.
        BlobResultSegment resultSegment = await container.ListBlobsSegmentedAsync(
                "", true, BlobListingDetails.All, 10, null, null, null);

        //Enumerar el segmento de resultados devuelto.
        int i = 0;
        if (resultSegment.Results.Count<IListBlobItem>() > 0) { Console.WriteLine("Page {0}:", ++i); }
        foreach (var blobItem in resultSegment.Results)
        {
            Console.WriteLine("\t{0}", blobItem.StorageUri.PrimaryUri);
        }
        Console.WriteLine();

        //Obtenga el token de continuación, si hay páginas adicionales de resultados.
        BlobContinuationToken continuationToken = resultSegment.ContinuationToken;

        //Compruebe si hay más resultados y enumérelos en páginas de 10 mientras se devuelve un token de continuación.
        while (continuationToken != null)
        {
            //Esta sobrecarga permite el control del tamaño de página. 
			//Puede devolver todos los resultados restantes pasando el valor null al parámetro maxResults, 
            //o llamando a una sobrecarga diferente.
            resultSegment = await container.ListBlobsSegmentedAsync(
					"", true, BlobListingDetails.All, 10, continuationToken, null, null);
            if (resultSegment.Results.Count<IListBlobItem>() > 0) { Console.WriteLine("Page {0}:", ++i); }
            foreach (var blobItem in resultSegment.Results)
            {
                Console.WriteLine("\t{0}", blobItem.StorageUri.PrimaryUri);
            }
            Console.WriteLine();

            //Obtener el siguiente token de continuación.
            continuationToken = resultSegment.ContinuationToken;
        }
    }

## <a name="next-steps"></a><span  class="short-header">Pasos siguientes</span>

Ahora que ha aprendido los conceptos básicos del almacenamiento de blobs, siga estos vínculos
para obtener más información acerca de cómo realizar tareas de almacenamiento más complejas.
<ul>
<li>Consulte la documentación de referencia del servicio de blobs para obtener información detallada acerca de las API disponibles:
  <ul>
    <li><a href="http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409">Referencia acerca de la biblioteca de clientes de almacenamiento para .NET</a>
    </li>
    <li><a href="http://msdn.microsoft.com/es-es/library/windowsazure/dd179355">Referencia de la API REST</a></li>
  </ul>
</li>
<li>Obtenga información acerca de las tareas más avanzadas que se pueden realizar con el almacenamiento de Azure en <a href="http://msdn.microsoft.com/es-es/library/windowsazure/gg433040.aspx">Almacenamiento de datos y acceso a los mismos en Azure</a>.</li>
<li>Obtenga información acerca de cómo trabajar con Almacenamiento de Azure en procesos de back-end para Sitios web Azure en <a href="/es-es/documentation/articles/websites-dotnet-webjobs-sdk-get-started/">Introducción al SDK de WebJobs de Azure</a>.</li>
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
  [Conceptos]: Conceptos
  [Creación de una cuenta de almacenamiento de Azure]: #create-account
  [Configuración de una cadena de conexión de almacenamiento]: #setup-connection-string
  
  [OData]: http://nuget.org/packages/Microsoft.Data.OData/5.0.2
  [Edm]: http://nuget.org/packages/Microsoft.Data.Edm/5.0.2
  [Spatial]: http://nuget.org/packages/System.Spatial/5.0.2
