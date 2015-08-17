<properties
			pageTitle="Uso de Almacenamiento de archivos de Azure con PowerShell y .NET | Microsoft Azure"
            description="Aprenda a usar el Almacenamiento de archivos de Azure para crear recursos compartidos de archivos en la nube y administrar contenido de archivos. Almacenamiento de archivos permite a las empresas mover a Azure aplicaciones que dependen de recursos compartidos de archivos SMB. Conserve las credenciales de la cuenta de almacenamiento para la máquina virtual para volver a conectarse con el recurso compartido de archivos al reiniciar."
            services="storage"
            documentationCenter=".net"
            authors="tamram"
            manager="adinah"
            editor="" />

<tags ms.service="storage"
      ms.workload="storage"
      ms.tgt_pltfrm="na"
      ms.devlang="dotnet"
      ms.topic="hero-article"
      ms.date="08/04/2015"
      ms.author="tamram" />

# Uso de Almacenamiento de archivos de Azure con PowerShell y .NET

## Información general

El servicio de archivos de Azure expone recursos compartidos de archivos mediante el protocolo SMB 2.1 estándar. Ahora, las aplicaciones que se ejecutan en Azure pueden compartir archivos entre las máquinas virtuales usando conocidas API de sistemas de archivos como ReadFile y WriteFile. Además, también se puede acceder a los archivos al mismo tiempo a través de una interfaz REST, que abre una variedad de escenarios híbridos. Por último, el servicio de archivos de Azure se basa en la misma tecnología que los servicios de blobs, tablas y colas, lo que significa que el servicio de archivos de Azure puede aprovechar la disponibilidad, durabilidad, escalabilidad y redundancia geográfica existentes que están integradas en la plataforma de almacenamiento de Azure.

## Acerca de este tutorial

En este tutorial introductorio se muestran los conceptos básicos del uso de Almacenamiento de archivos de Microsoft Azure. En este tutorial, veremos lo siguiente:

- El uso de Azure PowerShell para mostrar cómo crear un nuevo recurso compartido de archivos de Azure, agregar un directorio, cargar un archivo local en el recurso compartido y mostrar los archivos del directorio.
- Monte el recurso compartido de archivos desde una máquina virtual de Azure exactamente igual que si fuera cualquier recurso compartido SMB.
- Use la biblioteca de cliente de almacenamiento de Azure para que .NET tenga acceso al recurso compartido de archivos desde una aplicación local. Cree una aplicación de consola y lleve a cabo estas acciones con el recurso compartido de archivos:
	- Escriba el contenido de un archivo del recurso compartido en la ventana de la consola.
	- Establezca la cuota (tamaño máximo) para el recurso compartido de archivos.
	- Cree una firma de acceso compartido para un archivo que utiliza una directiva de acceso compartido definida en el recurso compartido.
	- Copie un archivo en otro en la misma cuenta de almacenamiento.
	- Copie un archivo en un blob en la misma cuenta de almacenamiento.

[AZURE.INCLUDE [storage-dotnet-client-library-version-include](../../includes/storage-dotnet-client-library-version-include.md)]

[AZURE.INCLUDE [storage-file-concepts-include](../../includes/storage-file-concepts-include.md)]


## Crear una cuenta de Almacenamiento de Azure

Almacenamiento de archivos de Azure se encuentra actualmente en vista previa. Para solicitar acceso a la vista previa, navegue a la [página de vista previa de Microsoft Azure](/services/preview/) y solicite acceso a **Archivos de Azure**. Cuando la solicitud esté aprobada, recibirá una notificación de que puede acceder a la vista previa de Almacenamiento de archivos. Después, puede crear una cuenta de almacenamiento para acceder a Almacenamiento de archivos.

> [AZURE.NOTE]Actualmente, Almacenamiento de archivos solamente está disponible para nuevas cuentas de almacenamiento. Después de que se haya concedido acceso a su suscripción a Almacenamiento de archivos, cree una nueva cuenta de almacenamiento para utilizar con esta guía.
>
> Actualmente, Almacenamiento de archivos de Azure no admite firmas de acceso compartido.

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## Uso de PowerShell para crear un recurso compartido de archivos

A continuación, utilizaremos Azure PowerShell para crear un recurso compartido de archivos. Una vez que se ha creado el recurso compartido de archivos, se puede montar desde cualquier sistema de archivos que admita SMB 2.1.

### Instalación de cmdlets de PowerShell para Almacenamiento de Azure

Para prepararse para usar PowerShell, descargue e instale los cmdlets de Azure PowerShell. Consulte [Instalación y configuración de Azure PowerShell](../install-configure-powershell.md) para obtener instrucciones sobre el punto de instalación y la instalación.

> [AZURE.NOTE]Los cmdlets de PowerShell para el servicio de archivos está disponible solamente en el módulo más reciente de Azure PowerShell, versión 0.8.5 y posterior. Es recomendable descargar e instalar el módulo más reciente de Azure PowerShell o actualizar a dicho módulo.

Abra una ventana de Azure PowerShell haciendo clic en **Inicio** y escribiendo **Azure PowerShell**. La ventana de Azure PowerShell cargará el módulo de Azure Powershell.

### Creación de un contexto para la cuenta y clave de almacenamiento

Ahora, cree el contexto de la cuenta de almacenamiento. El contexto encapsula el nombre y la clave de cuenta de almacenamiento. Para obtener instrucciones sobre la copia de la clave de cuenta desde el portal de Azure, consulte [Vista, copia y regeneración de las claves de acceso de almacenamiento](storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys).

Reemplace `storage-account-name` y `storage-account-key` por el nombre y clave de cuenta de almacenamiento en el siguiente ejemplo:

	# create a context for account and key
	$ctx=New-AzureStorageContext storage-account-name storage-account-key

### Creación de un nuevo recurso compartido de archivos

A continuación, cree el nuevo recurso compartido, llamado `logs` en este ejemplo:

	# create a new share
	$s = New-AzureStorageShare logs -Context $ctx

Ahora tiene un recurso compartido de archivos en Almacenamiento de archivos. A continuación, agregaremos un directorio y un archivo.

> [AZURE.IMPORTANT]El nombre del recurso compartido de archivos debe estar en minúsculas. Para obtener detalles completos sobre cómo asignar un nombre a recursos compartidos y archivos, consulte [Asignación de nombres y referencia a recursos compartidos, directorios, archivos y metadatos](https://msdn.microsoft.com/library/azure/dn167011.aspx).

### Creación de un directorio en el recurso compartido de archivos

Seguidamente, crearemos un directorio en el recurso compartido. En el siguiente ejemplo, el directorio se llama `CustomLogs`:

    # create a directory in the share
    New-AzureStorageDirectory -Share $s -Path CustomLogs

### Carga de un archivo local al directorio

Ahora cargará un archivo local al directorio. El siguiente ejemplo carga un archivo desde `C:\temp\Log1.txt`. Edite la ruta de acceso al archivo de forma que apunte a un archivo válido situado en la máquina local:

    # upload a local file to the new directory
    Set-AzureStorageFileContent -Share $s -Source C:\temp\Log1.txt -Path CustomLogs

### Visualización de los archivos del directorio en una lista

Para ver el archivo en el directorio, puede mostrar los archivos de este en una lista. Este comando también mostrará subdirectorios, pero en este ejemplo, no hay ningún subdirectorio, por lo que solamente se mostrará el archivo.

	# list files in the new directory
	Get-AzureStorageFile -Share $s -Path CustomLogs

## Monte el recurso compartido desde una máquina virtual de Azure en la que se ejecute Windows

Para mostrar cómo montar un recurso compartido de archivos de Azure, ahora crearemos una máquina virtual de Azure en la que se ejecuta Windows y accederemos a ella de forma remota para montar el recurso compartido.

1. En primer lugar, cree una nueva máquina virtual de Azure siguiendo las instrucciones existentes en [Creación de una máquina virtual que ejecuta Windows Server](../virtual-machines-windows-tutorial.md).
2. Después, acceda a la máquina virtual de forma remota siguiendo las instrucciones existentes en [Inicio de sesión en una máquina virtual que ejecuta desde Windows Server](../virtual-machines-log-on-windows-server.md).
3. Abra una ventana de PowerShell en la máquina virtual.

### Persistencia de las credenciales de la cuenta de almacenamiento para la máquina virtual

Antes de montar el recurso compartido de archivos, primero haga persistir las credenciales de la cuenta de almacenamiento en la máquina virtual. Este paso permite a Windows reconectarse automáticamente con el recurso compartido de archivos cuando la máquina virtual se reinicia. Para hacer persistir las credenciales de cuenta, ejecute el comando `cmdkey` desde la ventana de PowerShell en la máquina virtual. Reemplace `<storage-account-name>` por el nombre de su cuenta de almacenamiento y `<storage-account-key>` por la clave de cuenta de almacenamiento:

	cmdkey /add:<storage-account-name>.file.core.windows.net /user:<storage-account-name> /pass:<storage-account-key>

Windows se reconectará al recurso compartido de archivos cuando la máquina virtual se reinicie. Ejecutando el comando `net use` desde una ventana de PowerShell puede comprobar que el recurso compartido se ha reconectado.

### Montaje del recurso compartido de archivos usando credenciales de persistencia

Una vez tenga una conexión remota con la máquina virtual, puede ejecutar el comando `net use` para montar el recurso compartido de archivos usando la siguiente sintaxis. Reemplace `<storage-account-name>` por el nombre de su cuenta de almacenamiento y `<share-name>` por el nombre del recurso compartido de Almacenamiento de archivos:

    net use <drive-letter>: \<storage-account-name>.file.core.windows.net<share-name>

	example :
	net use z: \\samples.file.core.windows.net\logs

Dado que hizo persistir las credenciales de la cuenta de almacenamiento en el paso anterior, no necesita proporcionarlas con el comando `net use`. Si todavía no tiene credenciales de persistencia, inclúyalas como un parámetro pasado al comando `net use`, como se muestra en este ejemplo.

    net use <drive-letter>: \<storage-account-name>.file.core.windows.net<share-name> /u:<storage-account-name> <storage-account-key>

	example :
	net use z: \\samples.file.core.windows.net\logs /u:samples <storage-account-key>

Ahora puede trabajar con el recurso compartido de Almacenamiento de archivos desde la máquina virtual como lo haría con cualquier otra unidad. Puede emitir comandos de archivo estándar desde el símbolo del sistema o ver el recurso compartido montado y su contenido desde el explorador de archivos. También puede ejecutar código desde la máquina virtual que accede al recurso compartido de archivos usando las API de E/S de archivos Windows estándar, como por ejemplo las proporcionadas por los [espacios de nombres System.IO](http://msdn.microsoft.com/library/gg145019.aspx) en .NET Framework.

También puede montar el recurso compartido de archivos desde un rol que se ejecute en un servicio en la nube de Azure mediante una conexión remota con el rol.

## Creación de una aplicación local para trabajar con Almacenamiento de archivos

Puede montar un recurso compartido de archivos desde una máquina virtual o un servicio en la nube que se ejecute en Azure, tal y como se describió anteriormente. Sin embargo, no puede montar un recurso compartido de archivos desde una aplicación local. Para acceder a los datos del recurso compartido desde una aplicación local, debe usar la API de Almacenamiento de archivos. En este ejemplo se muestra cómo trabajar con un recurso compartido de archivos a través de la [biblioteca de clientes de Almacenamiento de Azure para .NET](http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409).

Para mostrar cómo usar la API desde una aplicación local, crearemos una aplicación de consola sencilla que se ejecute en el escritorio.

### Creación de la aplicación de consola y obtención del ensamblado

Para crear una nueva aplicación de consola en Visual Studio e instalar el paquete NuGet de Almacenamiento de Azure:

1. En Visual Studio, elija **Archivo** -> **Proyecto nuevo** y elija **Windows** -> **Aplicación de consola** en la lista de plantillas de Visual C#.
2. Proporcione un nombre para la aplicación de consola y haga clic en **Aceptar**.
3. Una vez creado el proyecto, haga clic con el botón derecho en el Explorador de soluciones y elija **Administrar paquetes de NuGet**. Busque "WindowsAzure.Storage" en línea y haga clic en **Instalar** para instalar el paquete y las dependencias de Almacenamiento de Azure.

### Guardar las credenciales de la cuenta de almacenamiento en el archivo app.config

A continuación, guardará las credenciales en el archivo app.config del proyecto. Edite el archivo app.config de forma que su aspecto sea similar al del siguiente ejemplo, reemplazando `myaccount` por el nombre de cuenta de almacenamiento y `mykey` por la clave de la cuenta de almacenamiento:

	<?xml version="1.0" encoding="utf-8" ?>
	<configuration>
	    <startup>
	        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
	    </startup>
	    <appSettings>
	        <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=StorageAccountKeyEndingIn==" />
	    </appSettings>
	</configuration>

> [AZURE.NOTE]La versión más reciente del emulador de Almacenamiento de Azure no admite Almacenamiento de archivos. La cadena de conexión debe estar destinada a una cuenta de Almacenamiento de Azure en la nube con acceso a la vista previa de archivos.


### Incorporación de declaraciones de espacio de nombres

Abra el archivo program.cs file desde el Explorador de soluciones y agregue las siguientes declaraciones de espacio de nombres en la parte superior del archivo:

	using Microsoft.WindowsAzure;
	using Microsoft.WindowsAzure.Storage;
	using Microsoft.WindowsAzure.Storage.Blob;
	using Microsoft.WindowsAzure.Storage.File;

### Recuperación de la cadena de conexión mediante programación

Puede recuperar las credenciales guardadas desde el archivo app.config usando una de estas dos clases: `Microsoft.WindowsAzure.CloudConfigurationManager` o `System.Configuration.ConfigurationManager `. El paquete del Administrador de configuración de Microsoft Azure, que incluye la clase `Microsoft.WindowsAzure.CloudConfigurationManager`, está disponible en [Nuget](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager).

El siguiente ejemplo muestra cómo recuperar las credenciales usando la clase `CloudConfigurationManager` y cómo encapsularlas con la clase `CloudStorageAccount`. Agregue el siguiente código al método `Main()` en program.cs:

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    	CloudConfigurationManager.GetSetting("StorageConnectionString")); 

### Obtener acceso al recurso compartido de archivos mediante programación

A continuación, agregue el siguiente código al método `Main()`, después del código mostrado anteriormente para recuperar la cadena de conexión. Este código obtiene una referencia al archivo que creamos anteriormente y envía su contenido a la ventana de consola.

	//Create a CloudFileClient object for credentialed access to File storage.
	CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

	//Get a reference to the file share we created previously.
	CloudFileShare share = fileClient.GetShareReference("logs");

	//Ensure that the share exists.
	if (share.Exists())
	{
	    //Get a reference to the root directory for the share.
	    CloudFileDirectory rootDir = share.GetRootDirectoryReference();

	    //Get a reference to the directory we created previously.
	    CloudFileDirectory sampleDir = rootDir.GetDirectoryReference("CustomLogs");

	    //Ensure that the directory exists.
	    if (sampleDir.Exists())
	    {
	        //Get a reference to the file we created previously.
	        CloudFile file = sampleDir.GetFileReference("Log1.txt");

	        //Ensure that the file exists.
	        if (file.Exists())
	        {
	            //Write the contents of the file to the console window.
	            Console.WriteLine(file.DownloadTextAsync().Result);
	        }
	    }
	}

Ejecute la aplicación de consola para ver la salida.

## Establecer el tamaño máximo para un recurso compartido de archivos

A partir de la versión 5.x de la biblioteca de cliente de almacenamiento de Azure, puede establecer la cuota (o el tamaño máximo) para un recurso compartido, en gigabytes. Al establecer la cuota para un recurso compartido, puede limitar el tamaño total de los archivos almacenados en el recurso compartido.

Si el tamaño total de archivos del recurso compartido supera la cuota establecida en el recurso compartido, los clientes podrán aumentar el tamaño de los archivos existentes o crear nuevos archivos, a menos que estén vacíos.

En el ejemplo siguiente se muestra cómo establecer la cuota para un recurso compartido de archivos existente.

    //Parse the connection string for the storage account.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

    //Create a CloudFileClient object for credentialed access to File storage.
    CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

    //Get a reference to the file share we created previously.
    CloudFileShare share = fileClient.GetShareReference("logs");

    //Ensure that the share exists.
    if (share.Exists())
    {
		//Specify the maximum size of the share, in GB.
	    share.Properties.Quota = 100;
	    share.SetProperties();
	}

Para obtener el valor de cualquier cuota existente para el recurso compartido, llame al método **FetchAttributes()** para recuperar las propiedades del recurso compartido.

## Generar una firma de acceso compartido para un archivo o recurso compartido de archivos

A partir de la versión 5.x de la biblioteca de cliente de almacenamiento de Azure, puede generar una firma de acceso compartido (SAS) para un recurso compartido de archivos o para un archivo individual. También puede crear una directiva de acceso compartido en un recurso compartido de archivos para administrar firmas de acceso compartido. Se recomienda la creación de una directiva de acceso compartido, ya que ofrece un medio de revocar la SAS si esta se encuentra en peligro.

En el ejemplo siguiente se crea una directiva de acceso compartido en un recurso compartido y luego usa esa directiva para ofrecer las restricciones para una SAS en un archivo del recurso compartido.

    //Parse the connection string for the storage account.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

    //Create a CloudFileClient object for credentialed access to File storage.
    CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

    //Get a reference to the file share we created previously.
    CloudFileShare share = fileClient.GetShareReference("logs");

    //Ensure that the share exists.
    if (share.Exists())
    {
        string policyName = "sampleSharePolicy" + DateTime.UtcNow.Ticks;

        //Create a new shared access policy and define its constraints.
        SharedAccessFilePolicy sharedPolicy = new SharedAccessFilePolicy()
            {
                SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
                Permissions = SharedAccessFilePermissions.Read | SharedAccessFilePermissions.Write
            };

        //Get existing permissions for the share.
        FileSharePermissions permissions = share.GetPermissions();

        //Add the shared access policy to the share's policies. Note that each policy must have a unique name.
        permissions.SharedAccessPolicies.Add(policyName, sharedPolicy);
        share.SetPermissions(permissions);

        //Generate a SAS for a file in the share and associate this access policy with it.
        CloudFileDirectory rootDir = share.GetRootDirectoryReference();
        CloudFileDirectory sampleDir = rootDir.GetDirectoryReference("CustomLogs");
        CloudFile file = sampleDir.GetFileReference("Log1.txt");
        string sasToken = file.GetSharedAccessSignature(null, policyName);
        Uri fileSasUri = new Uri(file.StorageUri.PrimaryUri.ToString() + sasToken);

        //Create a new CloudFile object from the SAS, and write some text to the file. 
        CloudFile fileSas = new CloudFile(fileSasUri);
        fileSas.UploadText("This write operation is authenticated via SAS.");
        Console.WriteLine(fileSas.DownloadText());
    }

Para obtener más información sobre la creación y el uso de firmas de acceso compartido, vea [Firmas de acceso compartido: Descripción del modelo de firmas de acceso compartido](storage-dotnet-shared-access-signature-part-1.md) y [Crear y usar una SAS con el servicio BLOB](storage-dotnet-shared-access-signature-part-2.md).

## Copiar archivos

A partir de la versión 5.x de la biblioteca de cliente de almacenamiento de Azure, puede copiar un archivo en otro, un archivo en un blob o un blob en un archivo. A continuación mostramos cómo realizar estas operaciones de copia mediante programación.

También puede usar AzCopy para copiar un archivo en otro o para copiar un blob en un archivo o viceversa. Vea [Uso de AzCopy con Almacenamiento de Microsoft Azure](storage-use-azcopy.md#copy-files-in-azure-file-storage-with-azcopy-preview-version-only) para obtener detalles sobre la copia de archivos con AzCopy.

> [AZURE.NOTE]Si va a copiar un blob en un archivo, o un archivo en un blob, debe usar una firma de acceso compartido (SAS) para autenticar el objeto de origen, incluso si está copiando en la misma cuenta de almacenamiento.

### Copiar un archivo en otro

En el ejemplo siguiente se copia un archivo en otro en el mismo recurso compartido. Dado que en esta operación de copia se copia entre archivos de la misma cuenta de almacenamiento, puede usar la autenticación de clave compartida para realizar la copia.

    //Parse the connection string for the storage account.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

    //Create a CloudFileClient object for credentialed access to File storage.
    CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

    //Get a reference to the file share we created previously.
    CloudFileShare share = fileClient.GetShareReference("logs");

    //Ensure that the share exists.
    if (share.Exists())
    {
        //Get a reference to the root directory for the share.
        CloudFileDirectory rootDir = share.GetRootDirectoryReference();

        //Get a reference to the directory we created previously.
        CloudFileDirectory sampleDir = rootDir.GetDirectoryReference("CustomLogs");

        //Ensure that the directory exists.
        if (sampleDir.Exists())
        {
            //Get a reference to the file we created previously.
            CloudFile sourceFile = sampleDir.GetFileReference("Log1.txt");

            //Ensure that the source file exists.
            if (sourceFile.Exists())
            {
                //Get a reference to the destination file.
                CloudFile destFile = sampleDir.GetFileReference("Log1Copy.txt");

                //Start the copy operation.
                destFile.StartCopy(sourceFile);

                //Write the contents of the destination file to the console window.
                Console.WriteLine(destFile.DownloadText());
            }
        }
    }


### Copiar un archivo en un blob

En el ejemplo siguiente se crea un archivo y se copia a un blob de la misma cuenta de almacenamiento. El ejemplo se crea una SAS para el archivo de origen, que el servicio usa para autenticar el acceso al archivo de origen durante la operación de copia.

    //Parse the connection string for the storage account.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

    //Create a CloudFileClient object for credentialed access to File storage.
    CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

    //Create a new file share, if it does not already exist.
    CloudFileShare share = fileClient.GetShareReference("sample-share");
    share.CreateIfNotExists();

    //Create a new file in the root directory.
    CloudFile sourceFile = share.GetRootDirectoryReference().GetFileReference("sample-file.txt");
    sourceFile.UploadText("A sample file in the root directory.");

    //Get a reference to the blob to which the file will be copied.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    CloudBlobContainer container = blobClient.GetContainerReference("sample-container");
    container.CreateIfNotExists();
    CloudBlockBlob destBlob = container.GetBlockBlobReference("sample-blob.txt");

    //Create a SAS for the file that's valid for 24 hours.
    //Note that when you are copying a file to a blob, or a blob to a file, you must use a SAS
    //to authenticate access to the source object, even if you are copying within the same 
    //storage account.
    string fileSas = sourceFile.GetSharedAccessSignature(new SharedAccessFilePolicy()
    {
        //Only read permissions are required for the source file.
        Permissions = SharedAccessFilePermissions.Read,
        SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24)
    });

    //Construct the URI to the source file, including the SAS token. 
    Uri fileSasUri = new Uri(sourceFile.StorageUri.PrimaryUri.ToString() + fileSas);

    //Copy the file to the blob.
    destBlob.StartCopy(fileSasUri);

    //Write the contents of the file to the console window.
    Console.WriteLine("Source file contents: {0}", sourceFile.DownloadText());
    Console.WriteLine("Destination blob contents: {0}", destBlob.DownloadText());

Puede copiar un blob en un archivo de la misma manera. Si el objeto de origen es un blob, cree una SAS para autenticar el acceso a dicho blob durante la operación de copia.

## Usar el almacenamiento de archivos con Linux

Para crear y administrar un recurso compartido de archivos desde Linux, use la CLI de Azure. Vea [Uso de la CLI de Azure con Almacenamiento de Azure](storage-azure-cli.md#create-and-manage-file-shares) para obtener información acerca del uso de la CLI de Azure con el almacenamiento de archivos.

Puede montar un recurso compartido de archivos de Azure desde una máquina virtual que ejecuta Linux Cuando cree su máquina virtual de Azure, puede especificar una imagen de Linux que admita SMB 2.1 desde la galería de imágenes de Azure, como la versión más reciente de Ubuntu. Sin embargo, cualquier distribución de Linux que admita SMB 2.1 puede montar un recurso compartido de archivos de Azure.

Para obtener más información sobre cómo montar un recurso compartido de archivos de Azure en Linux, consulte [Almacenamiento compartido en Linux a través de la vista previa de archivos de Azure - parte 1](http://channel9.msdn.com/Blogs/Open/Shared-storage-on-Linux-via-Azure-Files-Preview-Part-1) en el Canal 9.

## Pasos siguientes

Consulte los vínculos siguientes para obtener más información acerca de Almacenamiento de archivos de Azure.

### Tutoriales y referencia

- [Referencia de la biblioteca de clientes de almacenamiento para .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx)
- [Referencia de la API REST del servicio de archivos](http://msdn.microsoft.com/library/azure/dn167006.aspx)
- [Usar AzCopy con Almacenamiento de Microsoft Azure](storage-use-azcopy.md)
- [Usar Azure PowerShell con Almacenamiento de Azure](storage-powershell-guide-full.md)
- [Uso de la CLI de Azure con Almacenamiento de Azure](storage-azure-cli.md)

### Publicaciones de blog

- [Introducing Microsoft Azure File Service](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx) (Introducción al servicio de archivos de Microsoft Azure)
- [Persisting connections to Microsoft Azure Files](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx) (Persistencia de conexiones en archivos de Microsoft Azure)
 

<!---HONumber=August15_HO6-->