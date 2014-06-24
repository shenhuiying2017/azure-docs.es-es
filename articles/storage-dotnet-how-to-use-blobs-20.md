<properties  linkid="dev-net-2-how-to-blob-storage" urlDisplayName="Blob Service" pageTitle="How to use blob storage from .NET | Microsoft Azure" metaKeywords="Get started Azure blob   Azure unstructured data   Azure unstructured storage   Azure blob   Azure blob storage   Azure blob .NET   Azure blob C#   Azure blob C#" description="Learn how to use the Windows Azure blob service to upload,  download, list, and delete blob content. Samples are written in C#." metaCanonical="" disqusComments="1" umbracoNaviHide="1" services="storage" documentationCenter="" title="How to use the Windows Azure Blob Storage Service in .NET" authors="tamram" />
# Uso del almacenamiento de blobs en .NET

Esta guía demuestra cómo realizar algunas tareas comunes a través del
servicio de almacenamiento de blobs de Azure. Los ejemplos están
escritos en C# y utilizan la biblioteca del cliente de almacenamiento de
Azure para .NET. Dichas tareas comunes incluyen **cargar**,
**enumerar**, **descargar** y **eliminar** blobs. Para obtener más
información acerca de los blobs, consulte la sección [Pasos
siguientes](#next-steps).
## Tabla de contenido

* [Qué es el almacenamiento de blobs](#what-is)
* [Conceptos](#concepts)
* [Creación de una cuenta de almacenamiento de Azure](#create-account)
* [Configuración de una cadena de conexión de
  almacenamiento](#setup-connection-string)
* [Acceso al almacenamiento de blobs mediante
  programación](#configure-access)
* [Creación de un contenedor](#create-container)
* [Carga de un blob en un contenedor](#upload-blob)
* [Enumeración de blobs en un contenedor](#list-blob)
* [Descarga de blobs](#download-blobs)
* [Eliminación de blobs](#delete-blobs)
* [Pasos siguientes](#next-steps)

[WACOM.INCLUDE [howto-blob-storage](../includes/howto-blob-storage.md)]
## <a name="create-account"></a><span  class="short-header">Creación de una cuenta</span>Creación de una cuenta de almacenamiento de Azure

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]
## <a name="setup-connection-string"></a><span  class="short-header">Configuración de una cadena de conexión</span>Configuración de una cadena de conexión de almacenamiento

La biblioteca del cliente de almacenamiento de Azure para .NET admite el
uso de una cadena de conexión de almacenamiento para configurar extremos
y credenciales a fin de obtener acceso a los servicios de
almacenamiento. Puede poner la cadena de conexión de almacenamiento en
un archivo de configuración en vez de codificarla de forma rígida:

* Si utiliza los servicios en la nube de Azure, es recomendable que
  almacene la cadena de conexión mediante el sistema de configuración de
  servicios de Azure (archivos `*.csdef` y `*.cscfg`).
* Si utiliza los Sitios web Azure, las Máquinas virtuales de Azure o
  crea aplicaciones .NET de desarrollo diseñadas para ejecutarse al
  margen de Azure, es recomendable que almacene la cadena de conexión
  mediante el sistema de configuración .NET (por ejemplo, un archivo
  `web.config` o `app.config`).

Más adelante en esta guía se muestra cómo recuperar la cadena de
conexión.
### Configuración de la cadena de conexión si utiliza los servicios en la nube

El mecanismo de configuración de servicios es exclusivo para los
proyectos de los servicios en la nube de Azure y le permite cambiar
dinámicamente la configuración desde el Portal de administración de
Azure sin volver a implementar la aplicación.

Siga estos pasos para configurar la cadena de conexión mediante la
configuración de servicios de Azure:

1.  En el Explorador de soluciones de Visual Studio, en la carpeta
    **Roles** del proyecto de implementación de Azure, haga clic con el
    botón derecho en su rol web o de trabajo y, a continuación, haga
    clic en **Propiedades**.  
     ![Seleccionar las propiedades de un rol del servicio en la nube en
    Visual Studio](./media/storage-dotnet-how-to-use-blobs-20/blob5.png)

2.  Haga clic en la pestaña **Configuración** y, a continuación, en el
    botón **Agregar configuración**.  
     ![Agregar una configuración del servicio en la nube en
    Visual Studio](./media/storage-dotnet-how-to-use-blobs-20/blob6.png)
    
    Entonces aparecerá una nueva entrada, **Setting1**, en la cuadrícula
    de configuración.

3.  En el menú desplegable **Tipo** de la nueva entrada **Setting1**,
    elija **Cadena de conexión**.  
     ![Blob7](./media/storage-dotnet-how-to-use-blobs-20/blob7.png)

4.  Haga clic en el botón **...** situado en el extremo derecho de la
    entrada **Setting1**. Se abrirá el diálogo **Cadena de conexión de
    cuenta de almacenamiento**.

5.  Elija si desea utilizar el emulador de almacenamiento
    (almacenamiento de Azure simulado en su equipo local) o una cuenta
    de almacenamiento en la nube. El código de esta guía funciona con
    cualquiera de estas opciones. Escriba el valor **Clave de acceso
    primaria** copiado del paso anterior de este tutorial si desea
    almacenar los datos de blobs en la cuenta de almacenamiento que se
    creó anteriormente en Azure.   
     ![Blob8](./media/storage-dotnet-how-to-use-blobs-20/blob8.png)

6.  Cambie el contenido del campo **Nombre** de **Setting1** a algo más
    intuitivo, como **CadenaConexiónAlmacenamiento**. Más adelante en
    esta guía hará referencia a esta cadena de conexión.  
     ![Blob9](./media/storage-dotnet-how-to-use-blobs-20/blob9.png)
### Configuración de la cadena de conexión mediante la configuración .NET

Si está desarrollando una aplicación ajena a los servicios en la nube de
Azure (consulte la sección anterior), es recomendable que utilice el
sistema de configuración .NET (por ejemplo., `web.config` o
`app.config`). Esto afecta a las aplicaciones que se ejecutan en los
Sitios web Azure y en las Máquinas virtuales de Azure, así como a
aquellas diseñadas para ejecutarse al margen de Azure. La cadena de
conexión se almacena utilizando el elemento `<appSettings>`, tal y como sigue:

    <configuration>

<appsettings markdown="1">    		<add  key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=[AccountName];AccountKey=[AccountKey" ></a>

 ``     </configuration>

 Consulte [Configurar las cadenas de conexión de Azure][1] para obtener
más información acerca de las cadenas de conexión de almacenamiento.

 Ahora está preparado para realizar los procedimientos de esta guía.
## <a name="configure-access"> </a><span  class="short-header">Acceso mediante programación</span>Acceso al almacenamiento de blobs mediante programación
### Obtención del ensamblado

 Puede utilizar NuGet para obtener el ensamblado
`Microsoft.Azure.Storage.dll`. Haga clic con el botón derecho en el
proyecto, en el **Explorador de soluciones**, y elija **Administrar
paquetes NuGet**. Busque "Azure.Storage" en línea y haga clic en
**Instalar** para instalar el paquete y las dependencias de
almacenamiento de Azure.

 `Microsoft.Azure.Storage.dll` también se incluye en el SDK de Azure para
.NET, que se puede descargar del [Centro para desarrolladores de
.NET][2]. El ensamblado se instala en el
directorio `%Program Files%\Microsoft SDKs\Azure\.NET SDK\<sdk>\ref\`.
### Declaraciones de espacio de nombres

 Agregue las siguientes declaraciones de espacio de nombres en la parte
superior de todo archivo C# en el que desee obtener acceso al
almacenamiento de Azure mediante programación:

    using Microsoft.Azure.Storage;
    using Microsoft.Azure.Storage.Auth;
    using Microsoft.Azure.Storage.Blob;

 Asegúrese de hacer referencia al ensamblado
`Microsoft.Azure.Storage.dll`.
### Recuperación de la cadena de conexión

 Puede utilizar el tipo **CloudStorageAccount** para representar la
información de la cuenta de almacenamiento. Si está utilizando una
plantilla de proyecto de Azure o hace referencia a
Microsoft.Azure.CloudConfigurationManager, puede utilizar el tipo
**CloudConfigurationManager** para recuperar la cadena de conexión de
almacenamiento y la información de la cuenta de almacenamiento de la
configuración de servicios de Azure:

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

 Si está creando una aplicación sin ninguna referencia a
Microsoft.WindowsAzure.CloudConfigurationManager y la cadena de conexión
está situada en el archivo `web.config` o `app.config`, como se muestra
anteriormente, puede utilizar **ConfigurationManager** para recuperar
dicha cadena. Deberá agregar una referencia a System.Configuration.dll a
su proyecto, así como otra declaración de espacio de nombres para ella:

    using System.Configuration;
    ...
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    	ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString);

 El tipo **CloudBlobClient** le permite recuperar objetos que representan
contenedores y blobs almacenados en el servicio de almacenamiento de
blobs. El código siguiente crea un objeto **CloudBlobClient** utilizando
el objeto de cuenta de almacenamiento recuperado anteriormente:

    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
### Dependencias ODataLib

 Las dependencias ODataLib de la biblioteca de clientes de almacenamiento
para .NET se resuelven mediante los paquetes ODataLib (versión 5.0.2)
disponibles a través de NuGet y no a través de los servicios de datos de
WCF. A través de NuGet, es posible descargar directamente las
bibliotecas ODataLib o bien hacer referencia a ellas con el código del
proyecto. Los paquetes ODataLib específicos son [OData][3], [Edm][4] y
[Spatial][5].
## <a name="create-container"> </a><span  class="short-header">Creación de un contenedor</span>Creación de un contenedor

 Todos los blobs de almacenamiento se encuentran en un contenedor. Puede
utilizar un objeto **CloudBlobClient** para obtener una referencia al
contenedor que desee usar. En caso de que el contenedor todavía no
exista, es posible crearlo:

    // Recupere cuenta de almacenamiento desde cadena de conexión.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));
    
    // Cree el cliente blob.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    
    // Recupere una referencia a un contenedor. 
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");
    
    // Cree el contenedor si todavía no existe.
    container.CreateIfNotExists();

 De manera predeterminada, el nuevo contenedor es privado y debe
especificar su clave de acceso de almacenamiento para descargar blobs de
él. Si desea poner los archivos del contenedor a disposición de todo el
mundo, puede convertir el contenedor en público utilizando el código
siguiente:

    container.SetPermissions(
        new BlobContainerPermissions { PublicAccess = 

 BlobContainerPublicAccessType.Blob }); Cualquier usuario de Internet puede ver los blobs de los contenedores
públicos, pero solo es posible modificarlos o eliminarlos si se dispone
de la clave de acceso apropiada.

 <h2> <a name="upload-blob"> </a><span  class="short-header">Carga en un contenedor</span>Carga de un blob en un contenedor</h2>


 El almacenamiento de blobs de Azure admite blobs en bloques y en
páginas. En la mayoría de los casos, se recomienda usar blobs en
bloques.

 Para cargar un archivo en un blob en bloques, obtenga una referencia de
contenedor y utilícela para obtener una referencia de blob en bloques.
Una vez que disponga de la referencia de blob, puede cargar cualquier
secuencia de datos en ella mediante el método **UploadFromStream**. De
este modo, se creará el blob si no existía anteriormente, o bien se
sobrescribirá si ya existía. En el siguiente ejemplo se muestra cómo
cargar un blob en un contenedor creado anteriormente.

    // Recupere cuenta de almacenamiento desde cadena de conexión.
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
## <a name="list-blob"> </a><span  class="short-header">Enumeración de blobs en un contenedor</span>Enumeración de blobs en un contenedor

 Para enumerar los blobs de un contenedor, primero obtenga una referencia
de contenedor. A continuación, puede utilizar el método **ListBlobs**
del contenedor para recuperar los blobs o los directorios que contiene.
Para obtener acceso a las numerosas propiedades y métodos de una lista
**IListBlobItem** recuperada, debe convertirla en un objeto
**CloudBlockBlob**, **CloudPageBlob** o **CloudBlobDirectory**. Si se
desconoce el tipo, puede realizar una comprobación de tipo para
determinar el formato al que debe convertirla. El código siguiente
demuestra cómo recuperar y consultar el URI de cada elemento del
contenedor `photos`:

    // Recupere cuenta de almacenamiento desde cadena de conexión.
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

 Como se muestra anteriormente, en el servicio de blobs los contenedores
también incluyen directorios. De este modo, es posible organizar los
blobs en una estructura similar a la estructura de carpetas. Por
ejemplo, observe el siguiente conjunto de blobs en bloques incluidos en
un contenedor denominado `photos`:

    photo1.jpg
    2010/architecture/description.txt
    2010/architecture/photo3.jpg
    2010/architecture/photo4.jpg
    2011/architecture/photo5.jpg
    2011/architecture/photo6.jpg
    2011/architecture/description.txt
    2011/photo7.jpg

 Al llamar a **ListBlobs** en el contenedor "photos" (como en el ejemplo
anterior), la lista que se obtenga contendrá objetos
**CloudBlobDirectory** y **CloudBlockBlob** que representan los
directorios y los blobs que existen en el nivel superior. Este sería el
resultado:

    Directory: https://<accountname>.blob.core.windows.net/photos/2010/
    Directory: https://<accountname>.blob.core.windows.net/photos/2011/
    Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg

 También existe la opción de establecer el parámetro
**UseFlatBlobListing** del método **ListBlobs** en **true**. De este
modo, todos los blobs aparecerían como **CloudBlockBlob**, con
independencia del directorio. Esta sería la llamada a **ListBlobs**:

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

 Para obtener más información, consulte
[CloudBlobContainer.ListBlobs][].
## <a name="download-blobs"> </a><span  class="short-header">Descarga de blobs</span>Descarga de blobs

 Para descargar blobs, primero recupere una referencia de blob y, a
continuación, llame al método **DownloadToStream**. En el siguiente
ejemplo, se usa el método **DownloadToStream** para transferir el
contenido del blob a un objeto de secuencia que, a continuación, puede
guardar en un archivo local.

    // Recupere cuenta de almacenamiento desde cadena de conexión.
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

 También puede usar el método **DownloadToStream** para descargar el
contenido de un blob en forma de cadena de texto.

    // Recupere cuenta de almacenamiento desde cadena de conexión.
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
## <a name="delete-blobs"> </a><span  class="short-header">Eliminación de blobs</span>Eliminación de blobs

 Para eliminar un blob, primero obtenga una referencia de blob y, a
continuación, llame al método **Delete** en ella.

    // Recupere cuenta de almacenamiento desde cadena de conexión.
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
## <a name="next-steps"></a><span  class="short-header">Pasos siguientes</span>Pasos siguientes

 Ahora que está familiarizado con los aspectos básicos del almacenamiento
de blobs, utilice estos enlaces para obtener más información acerca de
cómo realizar tareas de almacenamiento más complejas.

 *  Consulte la documentación de referencia del servicio de blobs para
  obtener información detallada acerca de las API disponibles:
  *  [Referencia acerca de la biblioteca de clientes de almacenamiento
    para .NET][6]
  *  [Referencia acerca de la API de REST][7]
  

*  Obtenga información acerca de las tareas más avanzadas que se pueden
  realizar con el almacenamiento de Azure en [Almacenamiento][8].
*  Consulte más guías de características para obtener información acerca
  de otras opciones del almacenamiento de datos en Azure.
  *  Utilice el [Almacenamiento de
    tablas](/en-us/develop/net/how-to-guides/table-services/) para almacenar datos estructurados.
  *  Utilice [Base de datos
    SQL](/en-us/develop/net/how-to-guides/sql-database/) para almacenar datos relacionales.
  


 </appsettings>



[1]: http://msdn.microsoft.com/en-us/library/windowsazure/ee758697.aspx
[2]: http://www.windowsazure.com/en-us/develop/net/#
[3]: http://nuget.org/packages/Microsoft.Data.OData/5.0.2
[4]: http://nuget.org/packages/Microsoft.Data.Edm/5.0.2
[5]: http://nuget.org/packages/System.Spatial/5.0.2
[6]: http://msdn.microsoft.com/en-us/library/windowsazure/dn495001(v=azure.10).aspx
[7]: http://msdn.microsoft.com/en-us/library/windowsazure/dd179355
[8]: http://msdn.microsoft.com/en-us/library/windowsazure/gg433040.aspx
