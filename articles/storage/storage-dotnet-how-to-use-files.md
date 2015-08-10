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
      ms.date="07/06/2015"
      ms.author="tamram" />

# Uso de Almacenamiento de archivos de Azure con PowerShell y .NET

## Información general

El servicio de archivos de Azure expone recursos compartidos de archivos mediante el protocolo SMB 2.1 estándar. Ahora, las aplicaciones que se ejecutan en Azure pueden compartir archivos entre las máquinas virtuales usando conocidas API de sistemas de archivos como ReadFile y WriteFile. Además, también se puede acceder a los archivos al mismo tiempo a través de una interfaz REST, que abre una variedad de escenarios híbridos. Por último, el servicio de archivos de Azure se basa en la misma tecnología que los servicios de blobs, tablas y colas, lo que significa que el servicio de archivos de Azure puede aprovechar la disponibilidad, durabilidad, escalabilidad y redundancia geográfica existentes que están integradas en nuestra plataforma.

## Acerca de este tutorial

En este tutorial introductorio se muestran los conceptos básicos del uso de Almacenamiento de archivos de Microsoft Azure. En este tutorial, veremos lo siguiente:

- El uso de PowerShell para mostrar cómo crear un nuevo recurso compartido de archivos de Azure, agregar un directorio, cargar un archivo local en el recurso compartido y mostrar los archivos del directorio.
- Cómo montar el recurso compartido de archivos desde una maquina virtual de Azure, exactamente igual que si fuera cualquier recurso compartido SMB.

Para los usuarios que deseen tener acceso a archivos que se encuentren en un recurso compartido desde una aplicación local, así como desde un servicio en la nube o una máquina virtual de Azure, mostraremos cómo usar la biblioteca del cliente de Almacenamiento de Azure para .NET para trabajar con el mismo recurso compartido de archivos desde una aplicación de escritorio.

> [AZURE.NOTE]La ejecución de ejemplos de código .NET en esta guía requiere la biblioteca del cliente de almacenamiento de Azure para .NET 4.x o posterior. La biblioteca de clientes de almacenamiento está disponible a través de [NuGet](https://www.nuget.org/packages/WindowsAzure.Storage/).

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

> [AZURE.NOTE]Dado que hizo persistir las credenciales de la cuenta de almacenamiento en el paso anterior, no necesita proporcionarlas con el comando `net use`. Si todavía no tiene credenciales de persistencia, inclúyalas como un parámetro pasado al comando `net use`.

    net use <drive-letter>: \<storage-account-name>.file.core.windows.net<share-name> /u:<storage-account-name> <storage-account-key>

	example :
	net use z: \\samples.file.core.windows.net\logs /u:samples <storage-account-key>

Ahora puede trabajar con el recurso compartido de Almacenamiento de archivos desde la máquina virtual como lo haría con cualquier otra unidad. Puede emitir comandos de archivo estándar desde el símbolo del sistema o ver el recurso compartido montado y su contenido desde el explorador de archivos. También puede ejecutar código desde la máquina virtual que accede al recurso compartido de archivos usando las API de E/S de archivos Windows estándar, como las proporcionadas por los [espacios de nombres System.IO] (http://msdn.microsoft.com/library/gg145019(v=vs.110).aspx) en .NET Framework.

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
	using Microsoft.WindowsAzure.Storage.File;

### Recuperación de la cadena de conexión mediante programación

Puede recuperar las credenciales guardadas desde el archivo app.config usando una de estas dos clases: `Microsoft.WindowsAzure.CloudConfigurationManager` o `System.Configuration.ConfigurationManager `. El siguiente ejemplo muestra cómo recuperar las credenciales usando la clase `CloudConfigurationManager` y cómo encapsularlas con la clase `CloudStorageAccount`. Agregue el siguiente código al método `Main()` en program.cs:

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    	CloudConfigurationManager.GetSetting("StorageConnectionString"));

### Acceso al recurso compartido de Almacenamiento de archivos mediante programación

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

## Montaje del recurso compartido desde una máquina virtual de Azure con Linux

Cuando se crea una máquina virtual de Azure, puede especificar una imagen de Ubuntu de la galería de imágenes del disco para asegurar la compatibilidad con SMB 2.1. Sin embargo, cualquier distribución de Linux que admita SMB 2.1 puede montar un recurso compartido de archivos de Azure.

Para obtener una demostración sobre cómo montar un recurso compartido de archivos de Azure en Linux, consulte [Almacenamiento compartido en Linux a través de la vista previa de archivos de Azure - parte 1](http://channel9.msdn.com/Blogs/Open/Shared-storage-on-Linux-via-Azure-Files-Preview-Part-1) en el Canal 9.

## Pasos siguientes

Consulte los vínculos siguientes para obtener más información acerca de Almacenamiento de archivos de Azure.

### Referencia

- [Referencia de la biblioteca de clientes de almacenamiento para .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx)
- [Referencia de la API REST del servicio de archivos](http://msdn.microsoft.com/library/azure/dn167006.aspx)

### Publicaciones de blog

- [Introducing Microsoft Azure File Service](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx) (Introducción al servicio de archivos de Microsoft Azure)
- [Persisting connections to Microsoft Azure Files](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx) (Persistencia de conexiones en archivos de Microsoft Azure)
 

<!---HONumber=July15_HO5-->