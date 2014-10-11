<properties linkid="dev-net-how-to-blob-storage" urlDisplayName="Blob Service" pageTitle="How to use blob storage from .NET | Azure" metaKeywords="Get started Azure blob   Azure unstructured data   Azure unstructured storage   Azure blob   Azure blob storage   Azure blob .NET   Azure blob C#   Azure blob C#" description="Learn how to use Microsoft Azure Blob storage to upload,  download, list, and delete blob content. Samples are written in C#." metaCanonical="" disqusComments="1" umbracoNaviHide="1" services="storage" documentationCenter=".NET" title="How to use Microsoft Azure Blob storage in .NET" authors="tamram" manager="mbaldwin" editor="cgronlun" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="tamram"></tags>

# Uso del almacenamiento de blobs en .NET

Esta guía demuestra cómo realizar algunas tareas comunes a través del
servicio de almacenamiento de blobs de Azure. Los ejemplos están escritos en C# y
utilizan la biblioteca del cliente de almacenamiento de Azure para .NET. Dichas tareas comunes incluyen
**cargar**, **enumerar**, **descargar** y **eliminar** blobs. Para
obtener más información acerca de los blobs, consulte la sección [Pasos siguientes][].

> [WACOM.NOTE] Esta guía se destina a la biblioteca de cliente de almacenamiento .NET de Azure 2.x y posterior. La versión recomendada es la biblioteca de cliente de almacenamiento 4.x, que está disponible a través de [NuGet][] o como parte del [SDK de Azure para .NET][]. Consulte [Acceso al almacenamiento de blobs mediante programación][] a continuación para obtener más información acerca de la obtención de una biblioteca de cliente de almacenamiento.

## Tabla de contenido

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
-   [Pasos siguientes][]

[WACOM.INCLUDE [howto-blob-storage][]]

## <a name="create-account"></a><span class="short-header">Creación de una cuenta</span>Creación de una cuenta de almacenamiento de Azure

[WACOM.INCLUDE [create-storage-account][]]

## <a name="setup-connection-string"></a><span class="short-header">Configuración de una cadena de conexión</span>Configuración de una cadena de conexión de almacenamiento

[WACOM.INCLUDE [storage-configure-connection-string][]]

## <a name="configure-access"> </a><span class="short-header">Acceso mediante programación</span>Acceso al almacenamiento de blobs mediante programación

### Obtención del ensamblado

Puede utilizar NuGet para obtener el ensamblado `Microsoft.WindowsAzure.Storage.dll`. Haga clic con el botón secundario en el proyecto, en el **Explorador de soluciones**, y elija **Manage NuGet Packages**. Busque "Azure.Storage" en línea y haga clic en **Install** para instalar el paquete y las dependencias de almacenamiento de Azure.

`Microsoft.WindowsAzure.Storage.dll` también se incluye en el SDK de Azure para .NET, que se puede descargar en el [Centro de desarrolladores de .NET][]. El ensamblado se instala en el directorio `%Program Files%\Microsoft SDKs\Windows Azure\.NET SDK%Program Files%\Microsoft SDKs\Windows Azure\.NET SDK\<sdk-version>\ref\`.

### Declaraciones de espacio de nombres

Agregue las siguientes declaraciones de espacio de nombres en la parte superior
de todo archivo C# en el que desee obtener acceso al almacenamiento de Azure mediante programación:

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Blob;

Asegúrese de hacer referencia al ensamblado `Microsoft.WindowsAzure.Storage.dll`.

### Recuperación de la cadena de conexión

Puede utilizar el tipo **CloudStorageAccount** para representar la información
de la cuenta de almacenamiento. Si está utilizando una
plantilla de proyecto de Azure o hace referencia a
Microsoft.Azure.CloudConfigurationManager, puede
utilizar el tipo **CloudConfigurationManager** para recuperar
la cadena de conexión de almacenamiento y la información de la cuenta
de almacenamiento de la configuración de servicios de Azure:

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

Si está creando una aplicación sin ninguna referencia a Microsoft.WindowsAzure.CloudConfigurationManager y la cadena de conexión está situada en el archivo `web.config` o `app.config`, como se muestra anteriormente, puede utilizar **ConfigurationManager** para recuperar dicha cadena. Deberá agregar una referencia a System.Configuration.dll a su proyecto, así como otra declaración de espacio de nombres para ella:

    using System.Configuration;
    ...
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString);

El tipo **CloudBlobClient** le permite recuperar objetos que representan
contenedores y blobs almacenados en el servicio de almacenamiento de blobs. El código
siguiente crea un objeto **CloudBlobClient** utilizando el objeto de cuenta
de almacenamiento recuperado anteriormente:

    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

### Dependencias ODataLib

Las dependencias ODataLib de la biblioteca de clientes de almacenamiento para .NET se resuelven mediante los paquetes ODataLib (versión 5.0.2) disponibles a través de NuGet y no a través de los servicios de datos de WCF. A través de NuGet, es posible descargar directamente las bibliotecas ODataLib o bien hacer referencia a ellas con el código del proyecto. Los paquetes ODataLib específicos son [OData][], [Edm][] y [Spatial][].

## <a name="create-container"> </a><span class="short-header">Creación de un contenedor</span>Creación de un contenedor

Todos los blobs de almacenamiento se encuentran en un contenedor. Puede utilizar un objeto
**CloudBlobClient** para obtener una referencia al contenedor que desee
usar. En caso de que el contenedor todavía no exista, es posible crearlo:

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve a reference to a container. 
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Create the container if it doesn't already exist.
    container.CreateIfNotExists();

De manera predeterminada, el nuevo contenedor es privado y debe
especificar su clave de acceso de almacenamiento para descargar
blobs del mismo. Si desea poner los archivos del contenedor a disposición
de todo el mundo, puede convertir el contenedor en público utilizando el código
siguiente:

    container.SetPermissions(
        new BlobContainerPermissions { PublicAccess = 
        BlobContainerPublicAccessType.Blob }); 

Cualquier usuario de Internet puede ver los blobs de los contenedores públicos, pero solo
es posible modificarlos o eliminarlos si se dispone de la clave de acceso apropiada.

## <a name="upload-blob"> </a><span class="short-header">Carga en un contenedor</span>Carga de un blob en un contenedor

El almacenamiento de blobs de Azure admite blobs en bloques y en páginas. En la mayoría de los casos, se recomienda usar blobs en bloques.

Para cargar un archivo en un blob en bloques, obtenga una referencia de contenedor y utilícela
para obtener una referencia de blob en bloques. Una vez que disponga de la referencia de blob, puede cargar cualquier
secuencia de datos en ella mediante el método **UploadFromStream**. De este modo, se creará el blob si no existía anteriormente,
o bien se sobrescribirá si ya existía. En el siguiente ejemplo se muestra cómo cargar un blob en un contenedor creado anteriormente.

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

## <a name="list-blob"> </a><span class="short-header">Enumeración de blobs en un contenedor</span>Enumeración de los blobs de un contenedor

Para enumerar los blobs de un contenedor, primero obtenga una referencia de contenedor. A
continuación, puede utilizar el método **ListBlobs** del contenedor para recuperar los blobs o los directorios
que contiene. Para obtener acceso a las numerosas propiedades y métodos
de una lista **IListBlobItem** recuperada, debe convertirla en un objeto **CloudBlockBlob**,
**CloudPageBlob** o **CloudBlobDirectory**. Si se desconoce el tipo, puede realizar una
comprobación de tipo para determinar el formato al que debe convertirla. El código siguiente
demuestra cómo recuperar y consultar el URI de cada elemento del
contenedor `photos`:

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

Como se muestra anteriormente, en el servicio de blobs los contenedores también
incluyen directorios. De este modo, es posible organizar los blobs en una estructura similar
a la estructura de carpetas. Por ejemplo, observe el siguiente conjunto de blobs en bloques incluidos en un contenedor
denominado `photos`:

    photo1.jpg
    2010/architecture/description.txt
    2010/architecture/photo3.jpg
    2010/architecture/photo4.jpg
    2011/architecture/photo5.jpg
    2011/architecture/photo6.jpg
    2011/architecture/description.txt
    2011/photo7.jpg

Al llamar a **ListBlobs** en el contenedor "photos" (como en el ejemplo anterior), la lista que se obtenga
contendrá objetos **CloudBlobDirectory** y **CloudBlockBlob** que representan
los directorios y los blobs que existen en el nivel superior. Este sería el resultado:

    Directory: https://<accountname>.blob.core.windows.net/photos/2010/
    Directory: https://<accountname>.blob.core.windows.net/photos/2011/
    Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg

También existe la opción de establecer el parámetro **UseFlatBlobListing** del método **ListBlobs** en
**true**. De este modo, todos los blobs aparecerían como **CloudBlockBlob**
, con independencia del directorio. Esta sería la llamada a **ListBlobs**:

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

## <a name="download-blobs"> </a><span class="short-header">Descarga de blobs</span>Descarga de blobs

Para descargar blobs, primero recupere una referencia de blob y, a continuación, llame al método **DownloadToStream**. En el siguiente
ejemplo, se usa el método **DownloadToStream** para transferir el contenido del blob
a un objeto de secuencia que, a continuación, puede guardar en un archivo local.

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

## <a name="delete-blobs"> </a><span class="short-header">Eliminación de blobs</span>Eliminación de blobs

Para eliminar un blob, primero obtenga una referencia de blob y, a continuación, llame al método
**Delete** en ella.

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

## <a name="next-steps"></a><span class="short-header">Pasos siguientes</span>Pasos siguientes

Ahora que está familiarizado con los aspectos básicos del
almacenamiento de blobs, siga estos vínculos para obtener más información acerca de cómo realizar tareas de almacenamiento más complejas.

-   Consulte la documentación de referencia del servicio de blobs para obtener información detallada acerca de las API disponibles:
    -   [Referencia acerca de la biblioteca de clientes de almacenamiento para .NET][]
    -   [Referencia de la API REST][]

-   Obtenga información acerca de las tareas más avanzadas que se pueden realizar con el almacenamiento de Azure en [Almacenamiento][].
-   Obtenga información acerca de cómo trabajar con Almacenamiento de Azure en procesos de back-end para Sitios web Azure en [Introducción al SDK de WebJobs de Azure][].
-   Consulte más guías de características para obtener información acerca de otras opciones del almacenamiento de datos en Azure.
    -   Utilice [Almacenamiento de tablas][] para almacenar datos estructurados.
    -   Utilice [Almacenamiento de cola][] para almacenar datos no estructurados.
    -   Utilice [Base de datos SQL][] para almacenar datos relacionales.

</p>

  [Pasos siguientes]: #next-steps
  [NuGet]: https://www.nuget.org/packages/WindowsAzure.Storage/
  [SDK de Azure para .NET]: /en-us/downloads/
  [Acceso al almacenamiento de blobs mediante programación]: #configure-access
  [Qué es el almacenamiento de blobs]: #what-is
  [Conceptos]: #concepts
  [Creación de una cuenta de almacenamiento de Azure]: #create-account
  [Configuración de una cadena de conexión de almacenamiento]: #setup-connection-string
  [Creación de un contenedor]: #create-container
  [Carga de un blob en un contenedor]: #upload-blob
  [Enumeración de los blobs de un contenedor]: #list-blob
  [Descarga de blobs]: #download-blobs
  [Eliminación de blobs]: #delete-blobs
  [howto-blob-storage]: ../includes/howto-blob-storage.md
  [create-storage-account]: ../includes/create-storage-account.md
  [storage-configure-connection-string]: ../includes/storage-configure-connection-string.md
  [Centro de desarrolladores de .NET]: http://www.windowsazure.com/en-us/develop/net/#
  [OData]: http://nuget.org/packages/Microsoft.Data.OData/5.0.2
  [Edm]: http://nuget.org/packages/Microsoft.Data.Edm/5.0.2
  [Spatial]: http://nuget.org/packages/System.Spatial/5.0.2
  [Referencia acerca de la biblioteca de clientes de almacenamiento para .NET]: http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409
  [Referencia de la API REST]: http://msdn.microsoft.com/en-us/library/windowsazure/dd179355
  [Almacenamiento]: http://msdn.microsoft.com/en-us/library/windowsazure/gg433040.aspx
  [Introducción al SDK de WebJobs de Azure]: /en-us/documentation/articles/websites-dotnet-webjobs-sdk-get-started/
  [Almacenamiento de tablas]: /en-us/documentation/articles/storage-dotnet-how-to-use-tables/
  [Almacenamiento de cola]: /en-us/documentation/articles/storage-dotnet-how-to-use-queues/
  [Base de datos SQL]: /en-us/documentation/articles/sql-database-dotnet-how-to-use/
