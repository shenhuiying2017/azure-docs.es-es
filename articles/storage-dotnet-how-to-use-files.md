<properties 
	pageTitle="Uso de Almacenamiento de archivos de Azure | Microsoft Azure" 
	description="Aprenda a usar el Almacenamiento de archivos de Microsoft Azure para crear recursos compartidos de archivos y administrar contenido de archivos. Los ejemplos están escritos en PowerShell y C#." 
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
	ms.date="03/06/2015" 
	ms.author="tamram"/>

# Uso de Almacenamiento de archivos de Azure

## Información general

En esta guía introductoria mostraremos los conceptos básicos del uso de Almacenamiento de archivos de Microsoft Azure. En primer lugar, usamos PowerShell para mostrar cómo crear un nuevo recurso compartido de archivos de Azure, agregar un directorio, cargar un archivo local en el recurso compartido y mostrar los archivos del directorio. Después mostraremos cómo montar el recurso compartido de archivos desde una maquina virtual de Azure, exactamente igual que si fuera cualquier recurso compartido SMB.

Para los usuarios que deseen tener acceso a archivos que se encuentren en un recurso compartido desde una aplicación local, así como desde un servicio en la nube o una máquina virtual de Azure, mostraremos cómo usar la biblioteca del cliente de Almacenamiento de Azure para .NET para trabajar con el mismo recurso compartido de archivos desde una aplicación de escritorio.

> [AZURE.NOTE] La ejecución de ejemplos de código .NET en esta guía requiere la biblioteca del cliente de almacenamiento de Azure para .NET 4.x o posterior. La biblioteca de clientes de almacenamiento está disponible a través de [NuGet](https://www.nuget.org/packages/WindowsAzure.Storage/).

## ¿Qué es Almacenamiento de archivos de Azure?

Almacenamiento de archivos ofrece almacenamiento compartido para aplicaciones que usan el protocolo SMB 2.1 estándar. Las máquinas virtuales y los servicios en la nube de Microsoft Azure pueden compartir datos de archivo entre componentes de aplicaciones a través de recursos compartidos montados, y las aplicaciones locales pueden acceder a datos de archivo de un recurso compartido a través de la API de Almacenamiento de archivos.

Las aplicaciones que se ejecutan en máquinas virtuales o servicios en la nube de Azure pueden montar un recurso compartido de Almacenamiento de archivos para acceder a archivos de datos, igual que montaría una aplicación de escritorio un recurso compartido SMB normal. Cualquier número de roles o máquinas virtuales de Azure puede montar y acceder simultáneamente al recurso compartido de Almacenamiento de archivos.

Puesto que un recurso compartido de Almacenamiento de archivos es un recurso compartido de archivos SMB 2.1 estándar, las aplicaciones que se ejecutan en Azure pueden obtener acceso a los datos del recurso compartido a través de la API de E/S de archivos. Por tanto, los desarrolladores pueden aprovechar el código y los conocimientos que ya tienen para migrar las aplicaciones actuales. Los profesionales de TI pueden usar cmdlets de PowerShell para crear, montar y administrar recursos compartidos de Almacenamiento de archivos como parte de la administración de aplicaciones de Azure. Esta guía le mostrará ejemplos de ambos.

Almacenamiento de archivos suele usarse para realizar las siguientes tareas:

- Migrar aplicaciones locales que se basan en recursos compartidos de archivos para ejecutarse en máquinas virtuales o servicios en la nube de Azure, sin necesidad de reescribir código, con el coste que eso supone;
- Almacenar configuraciones de aplicaciones compartidas, por ejemplo en archivos de configuración;
- Almacenar datos de diagnóstico como registros, métricas y volcados de memoria en una ubicación compartida; 
- Almacenar herramientas y utilidades necesarias para desarrollar o administrar máquinas virtuales o servicios en la nube de Azure.

## Conceptos de Almacenamiento de archivos

Almacenamiento de archivos contiene los siguientes componentes:

![files-concepts][files-concepts]


-   **Cuenta de almacenamiento:** todo el acceso al almacenamiento de Azure se realiza a través de una cuenta de almacenamiento. Consulte [Objetivos de rendimiento y escalabilidad de almacenamiento de Azure](http://msdn.microsoft.com/library/azure/dn249410.aspx) para obtener más información acerca de la capacidad de la cuenta de almacenamiento.

-   **Compartir:** un recurso compartido de Almacenamiento de archivos es un recurso compartido de archivos de SMB 2.1 en Azure. 
    Todos los directorios y archivos se deben crear en un recurso compartido principal. Una cuenta puede contener un número ilimitado de recursos compartidos y un recurso compartido puede almacenar un número ilimitado de archivos, hasta los límites de capacidad de la cuenta de almacenamiento.

-   **Directorio:** una jerarquía de directorios opcional.

-	**Archivo:** un archivo en el recurso compartido. Un archivo puede ocupar hasta 1 TB.

-   **Formato de dirección URL:** los archivos son direccionables mediante la siguiente dirección URL
    format:   
    https://`<storage
    account>`.file.core.windows.net/`<share>`/`<directory/directories>`/`<file>`  
    
    En el diagrama anterior podría usarse la siguiente dirección URL de ejemplo para dirigir uno de los archivos:  
    `http://acmecorp.file.core.windows.net/cloudfiles/diagnostics/log.txt`



Para obtener detalles sobre cómo asignar un nombre a recursos compartidos, directorios y archivos, consulte [Asignación de nombres y referencia a recursos compartidos, directorios, archivos y metadatos](http://msdn.microsoft.com/library/azure/dn167011.aspx).

## Creación de una cuenta de almacenamiento de Azure

Almacenamiento de archivos de Azure se encuentra actualmente en vista previa. Para solicitar acceso a la vista previa, navegue a la [página de vista previa de Microsoft Azure](/services/preview/) y solicite acceso a **Archivos de Azure**. Cuando la solicitud esté aprobada, recibirá una notificación de que puede acceder a la vista previa de Almacenamiento de archivos. Después, puede crear una cuenta de almacenamiento para acceder a Almacenamiento de archivos.

> [AZURE.NOTE] Actualmente, Almacenamiento de archivos solamente está disponible para nuevas cuentas de almacenamiento. Después de que se haya concedido acceso a su suscripción a Almacenamiento de archivos, cree una nueva cuenta de almacenamiento para utilizar con esta guía.

[AZURE.INCLUDE [storage-create-account-include](../includes/storage-create-account-include.md)]

## Uso de PowerShell para crear un recurso compartido de archivos

A continuación, utilizaremos Azure PowerShell para crear un recurso compartido de archivos. Una vez que se ha creado el recurso compartido de archivos, se puede montar desde cualquier sistema de archivos que admita SMB 2.1. 

### Instalación de cmdlets de PowerShell para Almacenamiento de Azure

Para prepararse para usar PowerShell, descargue e instale los cmdlets de Azure PowerShell. Consulte [Instalación y configuración de Azure PowerShell](install-configure-powershell.md) para obtener el punto de instalación y las instrucciones de la misma.

> [AZURE.NOTE] Los cmdlets de PowerShell para el servicio de archivos está disponible solamente en el módulo más reciente de Azure PowerShell, versión 0.8.5 y posterior. Es recomendable descargar e instalar el módulo más reciente de Azure PowerShell o actualizar a dicho módulo.

Abra una ventana de Azure PowerShell haciendo clic en **Inicio** y escribiendo **Azure PowerShell**. La ventana de Azure PowerShell cargará el módulo de Azure Powershell.

### Creación de un contexto para la cuenta y clave de almacenamiento

Ahora, cree el contexto de la cuenta de almacenamiento. El contexto encapsula el nombre y la clave de cuenta. Reemplace `account-name` y `account-key` por el nombre y clave de cuenta en el siguiente ejemplo:

    # create a context for account and key
    $ctx=New-AzureStorageContext account-name account-key
    
### Creación de un nuevo recurso compartido de archivos

A continuación, cree el nuevo recurso compartido, llamado `sampleshare` en este ejemplo:

    # create a new share
    $s = New-AzureStorageShare sampleshare -Context $ctx

Ahora tiene un recurso compartido de archivos en Almacenamiento de archivos. A continuación, agregaremos un directorio y un archivo.

### Creación de un directorio en el recurso compartido de archivos

Seguidamente, crearemos un directorio en el recurso compartido. En el siguiente ejemplo, el directorio se llama `sampledir`:

    # create a directory in the share
    New-AzureStorageDirectory -Share $s -Path sampledir

### Carga de un archivo local al directorio

Ahora cargará un archivo local al directorio. El siguiente ejemplo carga un archivo desde `C:\temp\samplefile.txt`. Edite la ruta de acceso al archivo de forma que apunte a un archivo válido situado en la máquina local: 
    
    # upload a local file to the new directory
    Set-AzureStorageFileContent -Share $s -Source C:\temp\samplefile.txt -Path sampledir

### Visualización de los archivos del directorio en una lista

Para ver el archivo en el directorio, puede mostrar los archivos de este en una lista. Este comando también mostrará subdirectorios, pero en este ejemplo, no hay ningún subdirectorio, por lo que solamente se mostrará el archivo.  

    # list files in the new directory
    Get-AzureStorageFile -Share $s -Path sampledir

## Monte el recurso compartido desde una máquina virtual de Azure en la que se ejecute Windows

Para mostrar cómo montar un recurso compartido de archivos de Azure, ahora crearemos una máquina virtual de Azure en la que se ejecuta Windows y accederemos a ella de forma remota para montar el recurso compartido. 

1. En primer lugar, crearemos una nueva máquina virtual de Azure siguiendo las instrucciones existentes en [Creación de una máquina virtual que ejecuta Windows Server.](virtual-machines-windows-tutorial.md).
2. Después, acceda a la máquina virtual de forma remota siguiendo las instrucciones existentes en [Inicio de sesión en una máquina virtual que ejecuta desde Windows Server](virtual-machines-log-on-windows-server.md).
3. Abra una ventana de PowerShell en la máquina virtual. 

### Persistencia de las credenciales de la cuenta de almacenamiento para la máquina virtual

Antes de montar el recurso compartido de archivos, primero haga persistir las credenciales de la cuenta de almacenamiento en la máquina virtual. Este paso permite a Windows reconectarse automáticamente con el recurso compartido de archivos cuando la máquina virtual se reinicia. Para hacer persistir las credenciales de cuenta, ejecute el comando `cmdkey` desde la ventana de PowerShell en la máquina virtual. Reemplace `<storage-account>` por el nombre de su cuenta de almacenamiento y `<account-key>` por la clave de la cuenta de almacenamiento:

	cmdkey /add:<storage-account>.file.core.windows.net /user:<storage-account> /pass:<account-key>

Windows se reconectará al recurso compartido de archivos cuando la máquina virtual se reinicie. Ejecutando el comando `net use` desde una ventana de PowerShell puede comprobar que el recurso compartido se ha reconectado.

### Montaje del recurso compartido de archivos usando credenciales de persistencia

Una vez tenga una conexión remota con la máquina virtual, puede ejecutar el comando  `net use` para montar el recurso compartido de archivos usando la siguiente sintaxis. Reemplace `<storage-account>` por el nombre de su cuenta de almacenamiento y `<share-name>` por el nombre del recurso compartido de Almacenamiento de archivos.

	net use z: <storage-account>.file.core.windows.net<share-name>

> [AZURE.NOTE] Dado que hizo persistir las credenciales de la cuenta de almacenamiento en el paso anterior, no necesita proporcionarlas con el comando  `net use`. Si todavía no tiene credenciales de persistencia, inclúyalas como un parámetro pasado al comando  `net use`. Reemplace `<storage-account>` por el nombre de su cuenta de almacenamiento, `<share-name>` por el nombre del recurso compartido de Almacenamiento de archivos y `<account-key>` por la clave de la cuenta de almacenamiento:
	   
	net use z: <storage-account>.file.core.windows.net<share-name> /u:<storage-account> <account-key>

Ahora puede trabajar con el recurso compartido de Almacenamiento de archivos desde la máquina virtual como lo haría con cualquier otra unidad. Puede emitir comandos de archivo estándar desde el símbolo del sistema o ver el recurso compartido montado y su contenido desde el explorador de archivos. También puede ejecutar código desde la máquina virtual que accede al recurso compartido de archivos usando las API de E/S de archivos Windows estándar, como las proporcionadas por los [espacios de nombres System.IO](http://msdn.microsoft.com/library/gg145019(v=vs.110).aspx) en .NET Framework. 

También puede montar el recurso compartido de archivos desde un rol que se ejecute en un servicio en la nube de Azure mediante una conexión remota con el rol.

## Creación de una aplicación local para trabajar con Almacenamiento de archivos

Puede montar un recurso compartido de archivos desde una máquina virtual o un servicio en la nube que se ejecute en Azure, tal y como se describió anteriormente. Sin embargo, no puede montar un recurso compartido de archivos desde una aplicación local. Para acceder a los datos del recurso compartido desde una aplicación local, debe usar la API de Almacenamiento de archivos. En este ejemplo se muestra cómo trabajar con un recurso compartido de archivos a través de la [biblioteca de clientes de Almacenamiento de Azure para .NET](http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409) 

Para mostrar cómo usar la API desde una aplicación local, crearemos una aplicación de consola sencilla que se ejecute en el escritorio.

### Creación de la aplicación de consola y obtención del ensamblado

Para crear una nueva aplicación de consola en Visual Studio e instalar el paquete NuGet de Almacenamiento de Azure:

1. En Visual Studio, elija **Archivo** -> **Proyecto nuevo** y elija **Windows** -> **Aplicación de consola** en la lista de plantillas de Visual C#.
2. Proporcione un nombre para la aplicación de consola y haga clic en **Aceptar**.
3. Una vez creado el proyecto, haga clic con el botón secundario en el mismo Explorador de soluciones y elija **Administrador de soluciones**. Busque "WindowsAzure.Storage" en línea y haga clic en **Instalar** para instalar el paquete y las dependencias de Almacenamiento de Azure.

### Guardar las credenciales de la cuenta de almacenamiento en el archivo app.config

A continuación, guardará las credenciales en el archivo app.config del proyecto. Edite el archivo app.config de forma que su aspecto sea similar al del siguiente ejemplo, reemplazando  `myaccount` por el nombre de cuenta de almacenamiento y  `mykey` por la clave de la cuenta de almacenamiento:

    <?xml version="1.0" encoding="utf-8" ?>
	<configuration>
		<startup> 
			<supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
		</startup>
		<appSettings>
			<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey" />
		</appSettings>
	</configuration>

> [AZURE.NOTE] La versión más reciente del emulador de Almacenamiento de Azure no admite Almacenamiento de archivos. La cadena de conexión debe estar destinada a una cuenta de Almacenamiento de Azure en la nube con acceso a la vista previa de archivos.


### Incorporación de declaraciones de espacio de nombres
Abra el archivo program.cs file desde el Explorador de soluciones y agregue las siguientes declaraciones de espacio de nombres en la parte superior del archivo:

    using Microsoft.WindowsAzure;
	using Microsoft.WindowsAzure.Storage;
	using Microsoft.WindowsAzure.Storage.File;

### Recuperación de la cadena de conexión mediante programación
Puede recuperar las credenciales guardadas desde el archivo app.config usando una de estas dos clases: `Microsoft.WindowsAzure.CloudConfigurationManager` o `System.Configuration.ConfigurationManager `. El siguiente ejemplo muestra cómo recuperar las credenciales usando la clase `CloudConfigurationManager` y encapsularlas con la clase `CloudStorageAccount`. Agregue el siguiente código al método  `Main()` en program.cs:

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

### Acceso al recurso compartido de Almacenamiento de archivos mediante programación

A continuación, agregue el siguiente código al método  `Main()`, después del código mostrado anteriormente para recuperar la cadena de conexión. Este código obtiene una referencia al archivo que creamos anteriormente y envía su contenido a la ventana de consola.

	//Create a CloudFileClient object for credentialed access to File storage.
    CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

	//Get a reference to the file share we created previously.
    CloudFileShare share = fileClient.GetShareReference("sampleshare");

	//Ensure that the share exists.
    if (share.Exists())
    {
		//Get a reference to the root directory for the share.
        CloudFileDirectory rootDir = share.GetRootDirectoryReference();

		//Get a reference to the sampledir directory we created previously.
        CloudFileDirectory sampleDir = rootDir.GetDirectoryReference("sampledir");

		//Ensure that the directory exists.
        if (sampleDir.Exists())
        {
			//Get a reference to the file we created previously.
            CloudFile file = sampleDir.GetFileReference("samplefile.txt");

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

Para obtener una demostración de cómo montar un recurso compartido de archivos de Azure en Linux, consulte [Almacenamiento compartido en Linux a través de la vista previa de archivos de Azure - parte 1](http://channel9.msdn.com/Blogs/Open/Shared-storage-on-Linux-via-Azure-Files-Preview-Part-1) en el Canal 9.

## Pasos siguientes

Ahora que ha aprendido los conceptos básicos de Almacenamiento de archivo, siga estos vínculos
para obtener información más detallada.
<ul>
<li>Consulte la documentación de referencia del servicio de archivos para obtener información detallada acerca de las API disponibles:
  <ul>
    <li><a href="http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409">Referencia acerca de la biblioteca de clientes de almacenamiento para .NET</a>
    </li>
    <li><a href="http://msdn.microsoft.com/library/azure/dn167006.aspx">Referencia de la API REST del servicio de archivos</a></li>
  </ul>
</li>
<li>Consulte las publicaciones en blogs del equipo de almacenamiento de Azure relacionadas con el servicio de archivos:
  <ul>
    <li><a href="http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx">Introducción al servicio de archivos de Microsoft Azure</a>
    </li>
    <li><a href="http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx">Persistencia de conexiones en archivos de Microsoft Azure</a></li>
  </ul>
</li><li>Consulte más guías de características para obtener información acerca de otras opciones del almacenamiento de datos en Azure.
  <ul>
    <li>Use <a href="/documentation/articles/storage-dotnet-how-to-use-blobs/">Almacenamiento de blobs</a> para almacenar datos no estructurados.</li>
    <li>Utilice <a href="/documentation/articles/storage-dotnet-how-to-use-tables/">Almacenamiento de tablas</a> para almacenar datos estructurados.</li>
    <li>Use <a href="/documentation/articles/storage-dotnet-how-to-use-queues/">Almacenamiento de colas</a> para almacenar mensajes de forma fiable.</li>
    <li>Utilice <a href="/documentation/articles/sql-database-dotnet-how-to-use/">Base de datos SQL</a> para almacenar datos relacionales.</li>
  </ul>
</li>
</ul>

[files-concepts]: ./media/storage-dotnet-how-to-use-files/files-concepts.png


<!--HONumber=49--> 

<!--HONumber=49-->