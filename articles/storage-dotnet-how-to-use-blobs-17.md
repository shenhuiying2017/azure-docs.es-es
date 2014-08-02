<properties linkid="dev-net-2-how-to-blob-storage" urlDisplayName="Blob Service (2.0)" pageTitle="How to use blob storage in .NET | Microsoft Azure" metaKeywords="Get started Azure blob, Azure unstructured data, Azure unstructured storage, Azure blob, Azure blob storage, Azure blob .NET, Azure blob C#, Azure blob C#" description="Learn how to use the Azure blob service to upload,,download, list, and delete blob content. Samples are written in C#." metaCanonical="" services="storage" documentationCenter=".NET" title="How to use the Azure Blob Storage Service in .NET" authors="" solutions="" manager="paulettm" editor="cgronlun" />

Uso del servicio de almacenamiento de blobs de Azure en .NET
============================================================

[Versión 1.7](/en-us/develop/net/how-to-guides/blob-storage-v17/ "version 1.7") [Versión 2.0](/en-us/develop/net/how-to-guides/blob-storage/ "version 2.0")

Esta guía demuestra cómo realizar algunas tareas comunes a través del servicio de almacenamiento de blobs de Azure. Los ejemplos están escritos en C\# y utilizan la API .NET. Dichas tareas comunes incluyen **cargar**, **enumerar**, **descargar** y **eliminar** blobs. Para obtener más información acerca de los blobs, consulte la sección [Pasos siguientes](#next-steps).

Tabla de contenido
------------------

-   [Qué es el almacenamiento de blobs](#what-is)
-   [Conceptos](#concepts)
-   [Creación de una cuenta de almacenamiento de Azure](#create-account)
-   [Configuración de una cadena de conexión de almacenamiento](#setup-connection-string)
-   [Acceso al almacenamiento de blobs mediante programación](#configure-access)
-   [Creación de un contenedor](#create-container)
-   [Carga de un blob en un contenedor](#upload-blob)
-   [Enumeración de blobs en un contenedor](#list-blob)
-   [Descarga de blobs](#download-blobs)
-   [Eliminación de blobs](#delete-blobs)
-   [Pasos siguientes](#next-steps)

[WACOM.INCLUDE [howto-blob-storage](../includes/howto-blob-storage.md)]

Creación de una cuentaCreación de una cuenta de almacenamiento de Azure
-----------------------------------------------------------------------

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

Configuración de una cadena de conexiónConfiguración de una cadena de conexión de almacenamiento
------------------------------------------------------------------------------------------------

La API de almacenamiento para .NET de Azure admite el uso de una cadena de conexión de almacenamiento para configurar extremos y credenciales a fin de obtener acceso a los servicios de almacenamiento. Puede poner la cadena de conexión de almacenamiento en un archivo de configuración en vez de codificarla de forma rígida:

-   Si utiliza los servicios en la nube de Azure, es recomendable que almacene la cadena de conexión mediante el sistema de configuración de servicios de Azure (archivos `*.csdef` y `*.cscfg`).
-   Al usar Sitios web Azure o Máquinas virtuales de Azure, es recomendable que almacene su cadena de conexión usando el sistema de configuración .NET (por ejemplo, el archivo `web.config`).

En ambos casos, puede recuperar la cadena de conexión utilizando el método `CloudConfigurationManager.GetSetting`, como se muestra más adelante en esta guía.

### Configuración de la cadena de conexión si utiliza los servicios en la nube

El mecanismo de configuración de servicios es exclusivo para los proyectos de los servicios en la nube de Azure y le permite cambiar dinámicamente la configuración desde el Portal de administración de Azure sin volver a implementar la aplicación.

Siga estos pasos para configurar la cadena de conexión mediante la configuración de servicios de Azure:

1.  En el Explorador de soluciones de Visual Studio, en la carpeta **Roles** del proyecto de implementación de Azure, haga clic con el botón derecho en su rol web o de trabajo y, a continuación, haga clic en **Propiedades**.
     ![Seleccionar las propiedades de un rol del servicio en la nube en Visual Studio](./media/storage-dotnet-how-to-use-blobs-17/blob5.png)

2.  Haga clic en la pestańa **Configuración** y, a continuación, en el botón **Agregar configuración**.
     ![Agregar una configuración del servicio en la nube en Visual Studio](./media/storage-dotnet-how-to-use-blobs-17/blob6.png)

    Entonces aparecerá una nueva entrada, **Setting1** en la cuadrícula de configuración.

3.  En el menú desplegable **Tipo** de la nueva entrada **Setting1**, elija **Cadena de conexión**.
     ![Blob7](./media/storage-dotnet-how-to-use-blobs-20/blob7.png)

4.  Haga clic en el botón **...** situado en el extremo derecho de la entrada **Setting1**. Se abrirá el cuadro de diálogo **Cadena de conexión de cuenta de almacenamiento**.

5.  Elija si desea utilizar el emulador de almacenamiento (almacenamiento de Azure simulado en su equipo local) o una cuenta de almacenamiento real en la nube. El código de esta guía funciona con cualquiera de estas opciones. Escriba el valor **Clave de acceso primaria** copiado del paso anterior de este tutorial si desea almacenar los datos de blobs en la cuenta de almacenamiento que se creó anteriormente en Azure.
     ![Blob8](./media/storage-dotnet-how-to-use-blobs-20/blob8.png)

6.  Cambie el contenido del campo **Nombre** de **Setting1** a algo más intuitivo, como **CadenaConexiónAlmacenamiento**. Más adelante en esta guía hará referencia a esta cadena de conexión.
     ![Blob9](./media/storage-dotnet-how-to-use-blobs-20/blob9.png)

### Configuración de la cadena de conexión al usar Sitios web o Máquinas virtuales

Al usar Sitios web o Máquinas virtuales, es recomendable que utilice el sistema de configuración .NET (por ejemplo, `web.config`). La cadena de conexión se almacena utilizando el elemento `<appSettings>`:

    <configuration>
        <appSettings>
            <add key="StorageConnectionString"
                 value="DefaultEndpointsProtocol=https;AccountName=[AccountName];AccountKey=[AccountKey]" />
        </appSettings>
    </configuration>

Consulte [Configurar las cadenas de conexión de Azure](http://msdn.microsoft.com/en-us/library/windowsazure/ee758697.aspx) para obtener más información acerca de las cadenas de conexión de almacenamiento.

Ahora está preparado para realizar los procedimientos de esta guía.

Acceso mediante programaciónAcceso al almacenamiento de blobs mediante programación
-----------------------------------------------------------------------------------

Agregue las siguientes declaraciones de espacio de nombres de código en la parte superior de todo archivo C\# en el que desee obtener acceso al almacenamiento de Azure mediante programación:

    using Microsoft.WindowsAzure;
    using Microsoft.WindowsAzure.StorageClient;

Puede usar el tipo **CloudStorageAccount** y el tipo **CloudConfigurationManager** para recuperar la información de la cuenta de almacenamiento y la cadena de conexión de almacenamiento de la configuración del servicio de Azure:

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

El tipo **CloudBlobClient** le permite recuperar objetos que representan contenedores y blobs almacenados en el servicio de almacenamiento de blobs. El código siguiente crea un objeto **CloudBlobClient** utilizando el objeto de cuenta de almacenamiento recuperado anteriormente:

    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

Creación de un contenedorCreación de un contenedor
--------------------------------------------------

Todos los blobs de almacenamiento se encuentran en un contenedor. Puede utilizar un objeto **CloudBlobClient** para obtener una referencia al contenedor que desee usar. En caso de que el contenedor todavía no exista, es posible crearlo:

    // Recupere la cuenta de almacenamiento de cadena de conexión.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Cree el cliente blob. 
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Recupere una referencia a un contenedor. 
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Cree el contenedor si todavía no existe.
    container.CreateIfNotExist();

De manera predeterminada, el nuevo contenedor es privado, por lo que debe especificar su clave de acceso de almacenamiento (como hizo anteriormente) para descargar blobs de él. Si desea poner los archivos del contenedor a disposición de todo el mundo, puede convertir el contenedor en público utilizando el código siguiente:

    container.SetPermissions(
        new BlobContainerPermissions { PublicAccess = 
        BlobContainerPublicAccessType.Blob });

Cualquier usuario de Internet puede ver los blobs de los contenedores públicos, pero solo es posible modificarlos o eliminarlos si se dispone de la clave de acceso apropiada.

Carga en un contenedorCarga de un blob en un contenedor
-------------------------------------------------------

Para cargar un archivo en un blob, obtenga una referencia de contenedor y utilícela para obtener una referencia de blob. Una vez que disponga de la referencia de blob, puede cargar cualquier secuencia de datos en ella mediante el método **UploadFromStream** en la referencia de blob. De este modo, se creará el blob si no existía, o bien se sobrescribirá si ya existía. El siguiente ejemplo de código muestra esto y presupone ya se ha creado el contenedor.

    // Recupere la cuenta de almacenamiento de cadena de conexión.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Cree el cliente blob.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Recupere una referencia a un contenedor creado previamente.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Recupere una referencia a un blob denominado "myblob".
    CloudBlob blob = container.GetBlobReference("myblob");

    // Cree o sobrescriba el blob "myblob" con contenidos desde un archivo local.
    using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
    {
        blob.UploadFromStream(fileStream);
    } 

Enumeración de blobs en un contenedorEnumeración de blobs en un contenedor
--------------------------------------------------------------------------

Para enumerar los blobs de un contenedor, primero obtenga una referencia de contenedor. A continuación, puede utilizar el método **ListBlobs** del contenedor para recuperar los blobs que contiene. El código siguiente demuestra cómo recuperar y consultar el **Uri** de cada elemento del contenedor:

    // Recupere la cuenta de almacenamiento de cadena de conexión.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Cree el cliente blob.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Recupere una referencia a un contenedor creado previamente.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Aplique bucle sobre los blobs dentro del contenedor y consulte el URI de cada uno de ellos.
    foreach (var blobItem in container.ListBlobs())
    {
        Console.WriteLine(blobItem.Uri);
    } 

En el servicio de blobs, los contenedores también incluyen directorios. De este modo, es posible organizar los blobs en una estructura similar a la estructura de carpetas. Por ejemplo, puede disponer de un contenedor con el nombre "photos", en el que puede cargar blobs con los nombres "rootphoto1", "2010/photo1", "2010/photo2" y "2011/photo1". De esta forma, se crean prácticamente los directorios "2010" y "2011" dentro del contenedor "photos". Al llamar a **ListBlobs** en el contenedor "photos", la lista que se obtenga contendrá objetos **CloudBlobDirectory** y **CloudBlob** que representan los directorios y los blobs que existen en el nivel superior. En este caso, se devolverán los directorios "2010" y "2011", y la foto "rootphoto1". Opcionalmente, puede pasar una nueva clase **BlobRequestOptions** con **UseFlatBlobListing** establecido en **true**. De este modo, se devuelven todos los blobs con independencia del directorio. Para obtener más información, consulte [CloudBlobContainer.ListBlobs][].

Descarga de blobsDescarga de blobs
----------------------------------

Para descargar blobs, primero recupere una referencia de blob. En el siguiente ejemplo, se usa el método **DownloadToStream** para transferir el contenido del blob a un objeto de secuencia que, a continuación, puede guardar en un archivo local. También puede llamar a los métodos **DownloadToFile**, **DownloadByteArray** o **DownloadText** del blob.

    // Recupere la cuenta de almacenamiento de cadena de conexión.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Cree el cliente blob.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Recupere una referencia a un contenedor creado previamente.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Recupere una referencia a un blob denominado "myblob".
    CloudBlob blob = container.GetBlobReference("myblob");

    // Guarde contenido de blob en el disco.
    using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
    {
        blob.DownloadToStream(fileStream);
    } 

Eliminación de blobsEliminación de blobs
----------------------------------------

Finalmente, para eliminar un blob, obtenga una referencia de blob y, a continuación, llame al método **Delete** en ella.

    // Recupere la cuenta de almacenamiento de cadena de conexión.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Cree el cliente blob.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Recupere una referencia a un contenedor creado previamente.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Recupere una referencia a un blob denominado "myblob".
    CloudBlob blob = container.GetBlobReference("myblob");

    // Elimine el blob.
    blob.Delete(); 

Pasos siguientesPasos siguientes
--------------------------------

Ahora que está familiarizado con los aspectos básicos del almacenamiento de blobs, siga estos vínculos para obtener más información acerca de cómo realizar tareas de almacenamiento más complejas.

-   Consulte la documentación de referencia del servicio de blobs para obtener información detallada acerca de las API disponibles:
    -   [Referencia a la biblioteca de clases de .NET](http://msdn.microsoft.com/en-us/library/windowsazure/wl_svchosting_mref_reference_home)
    -   [Referencia de la API REST](http://msdn.microsoft.com/en-us/library/windowsazure/dd179355)
-   Obtenga información acerca de las tareas más avanzadas que se pueden realizar con el almacenamiento de Azure en [Almacenamiento](http://msdn.microsoft.com/en-us/library/windowsazure/gg433040.aspx).
-   Consulte más guías de características para obtener información acerca de otras opciones del almacenamiento de datos en Azure.
    -   Utilice [Almacenamiento de tablas](/en-us/develop/net/how-to-guides/table-services/) para almacenar datos estructurados.
    -   Utilice [Base de datos SQL](/en-us/develop/net/how-to-guides/sql-database/) para almacenar datos relacionales.

