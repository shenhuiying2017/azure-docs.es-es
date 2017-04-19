---
title: "Introducción a Azure File Storage en Windows | Microsoft Docs"
description: "Almacene datos de archivo en la nube con el Almacenamiento de archivos de Azure y monte un recurso compartido de archivos de nube desde una máquina virtual (VM) de Azure o desde una aplicación local con Windows."
services: storage
documentationcenter: .net
author: RenaShahMSFT
manager: aungoo
editor: tysonn
ms.assetid: 6a889ee1-1e60-46ec-a592-ae854f9fb8b6
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 03/27/2017
ms.author: renash
translationtype: Human Translation
ms.sourcegitcommit: 538f282b28e5f43f43bf6ef28af20a4d8daea369
ms.openlocfilehash: b835b04d6ef6d06e35add4f503e6800099e97383
ms.lasthandoff: 04/07/2017


---
# <a name="get-started-with-azure-file-storage-on-windows"></a>Introducción a Almacenamiento de archivos de Azure en Windows
[!INCLUDE [storage-selector-file-include](../../includes/storage-selector-file-include.md)]

[!INCLUDE [storage-check-out-samples-dotnet](../../includes/storage-check-out-samples-dotnet.md)]

[!INCLUDE [storage-file-overview-include](../../includes/storage-file-overview-include.md)]

Para más información sobre Almacenamiento de archivos con Linux, consulte [Uso de Almacenamiento de archivos de Azure con Linux](storage-how-to-use-files-linux.md).

Para más información acerca de los objetivos de rendimiento y escalabilidad de Almacenamiento de archivos, consulte [Objetivos de escalabilidad y rendimiento del almacenamiento de Azure](storage-scalability-targets.md#scalability-targets-for-blobs-queues-tables-and-files).

[!INCLUDE [storage-dotnet-client-library-version-include](../../includes/storage-dotnet-client-library-version-include.md)]

[!INCLUDE [storage-file-concepts-include](../../includes/storage-file-concepts-include.md)]

## <a name="video-using-azure-file-storage-with-windows"></a>Vídeo: Uso del almacenamiento de archivos de Azure con Windows
Éste es un vídeo que muestra cómo crear y usar recursos compartidos de archivos de Azure en Windows.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-File-Storage-with-Windows/player]
> 
> 

## <a name="about-this-tutorial"></a>Acerca de este tutorial
En este tutorial introductorio se muestran los conceptos básicos del uso de Almacenamiento de archivos de Microsoft Azure. En este tutorial, veremos lo siguiente:

* Use Azure Portal o PowerShell para crear un nuevo recurso compartido de archivos de Azure, agregar un directorio, cargar un archivo local en el recurso compartido y enumerar los archivos del directorio.
* Monte el recurso compartido de archivos exactamente igual que si fuera cualquier recurso compartido del protocolo SMB.
* Use la Biblioteca de cliente de almacenamiento de Azure para .NET para tener acceso al recurso compartido de archivos desde una aplicación local. Cree una aplicación de consola y lleve a cabo estas acciones con el recurso compartido de archivos:
  * Escriba el contenido de un archivo del recurso compartido en la ventana de la consola.
  * Establezca la cuota (tamaño máximo) para el recurso compartido de archivos.
  * Cree una firma de acceso compartido para un archivo que utiliza una directiva de acceso compartido definida en el recurso compartido.
  * Copie un archivo en otro en la misma cuenta de almacenamiento.
  * Copie un archivo en un blob en la misma cuenta de almacenamiento.
* Uso de las métricas de Almacenamiento de Azure para solucionar problemas

El almacenamiento de archivos ahora es compatible con todas las cuentas de almacenamiento, por lo que se puede usar una cuenta de almacenamiento existente o crear una cuenta de almacenamiento nueva. Para más información acerca de la creación de una nueva cuenta de almacenamiento, consulte [Creación de una cuenta de almacenamiento](storage-create-storage-account.md#create-a-storage-account) .

## <a name="use-the-azure-portal-to-manage-a-file-share"></a>Uso de Azure Portal para administrar un recurso compartido de archivos
[Azure Portal](https://portal.azure.com) proporciona una interfaz de usuario para que los clientes administren recursos compartidos de archivos. En el portal puede:

* Crear un recurso compartido de archivos
* Cargar y descargar archivos al recurso compartido de archivos y desde este.
* Supervisar el uso real de cada recurso compartido de archivos.
* Ajustar la cuota de tamaño del recurso compartido.
* Obtener el comando `net use` que se usa para montar el recurso compartido de archivos desde un cliente Windows.

### <a name="create-file-share"></a>Creación de un recurso compartido de archivos
1. Inicie sesión en el Portal de Azure.
2. En el menú de navegación, haga clic en **Cuentas de almacenamiento** o **Cuentas de almacenamiento (clásico)**.
   
    ![Captura de pantalla que muestra cómo crear un recurso compartido de archivos en el portal](./media/storage-dotnet-how-to-use-files/files-create-share-0.png)
3. Elija la cuenta de almacenamiento.
   
    ![Captura de pantalla que muestra cómo crear un recurso compartido de archivos en el portal](./media/storage-dotnet-how-to-use-files/files-create-share-1.png)
4. Elija el servicio "Archivos".
   
    ![Captura de pantalla que muestra cómo crear un recurso compartido de archivos en el portal](./media/storage-dotnet-how-to-use-files/files-create-share-2.png)
5. Haga clic en "Recursos compartidos de archivos" y siga el vínculo para crear su primer recurso compartido de archivos.
   
    ![Captura de pantalla que muestra cómo crear un recurso compartido de archivos en el portal](./media/storage-dotnet-how-to-use-files/files-create-share-3.png)
6. Rellene el nombre del recurso compartido de archivos y su tamaño (hasta 5120 GB) para crear su primer recurso compartido de archivos. Una vez que se ha creado el recurso compartido de archivos, se puede montar desde cualquier sistema de archivos que admita SMB 2.1 o SMB 3.0.
   
    ![Captura de pantalla que muestra cómo crear un recurso compartido de archivos en el portal](./media/storage-dotnet-how-to-use-files/files-create-share-4.png)

### <a name="upload-and-download-files"></a>Carga y descarga de archivos
1. Elija un recurso compartido de archivos que ya ha creado.
   
    ![Captura de pantalla que muestra cómo cargar y descargar archivos desde el portal](./media/storage-dotnet-how-to-use-files/files-upload-download-1.png)
2. Haga clic en **Cargar** para abrir la interfaz de usuario para cargar los archivos.
   
    ![Captura de pantalla que muestra cómo cargar archivos desde el portal](./media/storage-dotnet-how-to-use-files/files-upload-download-2.png)
3. Haga clic con el botón derecho en un archivo y elija **Descargar** para descargarlo en local.
   
    ![Captura de pantalla que muestra cómo descargar archivos desde el portal](./media/storage-dotnet-how-to-use-files/files-upload-download-3.png)

### <a name="manage-file-share"></a>Administración del recurso compartido de archivos
1. Haga clic en **Cuota** para cambiar el tamaño del recurso compartido de archivos (hasta 5120 GB).
   
    ![Captura de pantalla que muestra cómo configurar la cuota del recurso compartido de archivos](./media/storage-dotnet-how-to-use-files/files-manage-1.png)
2. Haga clic en **Conectar** para obtener la línea de comandos para montar el recurso compartido de archivos de Windows.
   
    ![Captura de pantalla que muestra cómo montar el recurso compartido de archivos](./media/storage-dotnet-how-to-use-files/files-manage-2.png)
   
    ![Captura de pantalla que muestra cómo montar el recurso compartido de archivos](./media/storage-dotnet-how-to-use-files/files-manage-3.png)
   
   > [!TIP]
   > Para buscar la clave de acceso de la cuenta de almacenamiento para el montaje, haga clic en **Configuración** de su cuenta de almacenamiento y, después, en **Claves de acceso**.
   > 
   > 
   
    ![Captura de pantalla muestra cómo buscar la clave de acceso de la cuenta de almacenamiento](./media/storage-dotnet-how-to-use-files/files-manage-4.png)
   
    ![Captura de pantalla muestra cómo buscar la clave de acceso de la cuenta de almacenamiento](./media/storage-dotnet-how-to-use-files/files-manage-5.png)

## <a name="use-powershell-to-manage-a-file-share"></a>Uso de PowerShell para administrar un recurso compartido de archivos
Como alternativa, puede usar Azure PowerShell para crear y administrar recursos compartidos de archivos.

### <a name="install-the-powershell-cmdlets-for-azure-storage"></a>Instalación de cmdlets de PowerShell para Almacenamiento de Azure
Para prepararse para usar PowerShell, descargue e instale los cmdlets de Azure PowerShell. Consulte [Instalación y configuración de Azure PowerShell](/powershell/azureps-cmdlets-docs) para obtener instrucciones sobre el punto de instalación y la instalación.

> [!NOTE]
> Es recomendable descargar e instalar el módulo más reciente de Azure PowerShell o actualizar a dicho módulo.
> 
> 

Abra una ventana de Azure PowerShell haciendo clic en **Inicio** y escribiendo **Windows PowerShell**. La ventana de PowerShell cargará el módulo de Azure Powershell.

### <a name="create-a-context-for-your-storage-account-and-key"></a>Creación de un contexto para la cuenta y clave de almacenamiento
Ahora, cree el contexto de la cuenta de almacenamiento. El contexto encapsula el nombre y la clave de cuenta de almacenamiento. Para obtener instrucciones acerca de cómo copiar la clave de una cuenta desde [Azure Portal](https://portal.azure.com), consulte [Visualización y copia de las claves de acceso de almacenamiento](storage-create-storage-account.md#view-and-copy-storage-access-keys).

Sustituya `storage-account-name` y `storage-account-key` por el nombre y clave de su cuenta de almacenamiento  en el siguiente ejemplo.

```powershell
# create a context for account and key
$ctx=New-AzureStorageContext storage-account-name storage-account-key
```

### <a name="create-a-new-file-share"></a>Creación de un nuevo recurso compartido de archivos
A continuación, cree el nuevo recurso compartido, denominado `logs`.

```powershell
# create a new share
$s = New-AzureStorageShare logs -Context $ctx
```

Ahora tiene un recurso compartido de archivos en Almacenamiento de archivos. A continuación, agregaremos un directorio y un archivo.

> [!IMPORTANT]
> El nombre del recurso compartido de archivos debe estar en minúsculas. Para obtener detalles completos sobre cómo asignar un nombre a recursos compartidos y archivos, consulte [Asignación de nombres y referencia a recursos compartidos, directorios, archivos y metadatos](https://msdn.microsoft.com/library/azure/dn167011.aspx).
> 
> 

### <a name="create-a-directory-in-the-file-share"></a>Creación de un directorio en el recurso compartido de archivos
Seguidamente, crearemos un directorio en el recurso compartido. En el siguiente ejemplo, el directorio se llama `CustomLogs`.

```powershell
# create a directory in the share
New-AzureStorageDirectory -Share $s -Path CustomLogs
```

### <a name="upload-a-local-file-to-the-directory"></a>Carga de un archivo local al directorio
Ahora cargará un archivo local al directorio. El siguiente ejemplo carga un archivo desde `C:\temp\Log1.txt`. Edite la ruta de acceso al archivo de forma que apunte a un archivo válido situado en su equipo local.

```powershell
# upload a local file to the new directory
Set-AzureStorageFileContent -Share $s -Source C:\temp\Log1.txt -Path CustomLogs
```

### <a name="list-the-files-in-the-directory"></a>Visualización de los archivos del directorio en una lista
Para ver el archivo en el directorio, puede mostrar todos los archivos de este en una lista. Este comando devuelve los archivos y subdirectorios (si hay alguno) del directorio CustomLogs.

```powershell
# list files in the new directory
Get-AzureStorageFile -Share $s -Path CustomLogs | Get-AzureStorageFile
```

Get-AzureStorageFile devuelve una lista de archivos y directorios para cualquier objeto de directorio que se pase. "Get-AzureStorageFile -Share $s" devuelve una lista de archivos y directorios en el directorio raíz. Para obtener una lista de los archivos de un subdirectorio, tiene que pasar el subdirectorio a Get-AzureStorageFile. Esto es lo que hace: la primera parte del comando hasta la barra vertical devuelve una instancia de directorio del subdirectorio CustomLogs. A continuación, esto se pasa a Get-AzureStorageFile, que devuelve los archivos y directorios en CustomLogs.

### <a name="copy-files"></a>Copiar archivos
A partir de la versión 0.9.7 de Azure PowerShell, puede copiar un archivo en otro, un archivo en un blob o un blob en un archivo. A continuación se muestra cómo realizar estas operaciones de copia mediante cmdlets de PowerShell.

```powershell
# copy a file to the new directory
Start-AzureStorageFileCopy -SrcShareName srcshare -SrcFilePath srcdir/hello.txt -DestShareName destshare -DestFilePath destdir/hellocopy.txt -Context $srcCtx -DestContext $destCtx

# copy a blob to a file directory
Start-AzureStorageFileCopy -SrcContainerName srcctn -SrcBlobName hello2.txt -DestShareName hello -DestFilePath hellodir/hello2copy.txt -DestContext $ctx -Context $ctx
```

## <a name="mount-the-file-share"></a>Montaje del recurso compartido de archivos
Dada su compatibilidad con SMB 3.0, el almacenamiento de archivos admite el cifrado y los identificadores persistentes de los clientes SMB 3.0. La compatibilidad con el cifrado significa que los clientes SMB 3.0 pueden montar un recurso compartido de archivos desde cualquier lugar, incluyendo desde:

* Una máquina virtual de Azure de la misma región (también compatible con SMB 2.1)
* Una máquina virtual de Azure de otra región (solo SMB 3.0)
* Una aplicación cliente local (solo SMB 3.0)

Cuando un cliente accede al almacenamiento de archivos, la versión SMB que se usa depende de la versión de SMB compatible con el sistema operativo. La tabla siguiente proporciona un resumen de la compatibilidad de los clientes de Windows. Consulte este blog para más información sobre las [versiones de SMB](http://blogs.technet.com/b/josebda/archive/2013/10/02/windows-server-2012-r2-which-version-of-the-smb-protocol-smb-1-0-smb-2-0-smb-2-1-smb-3-0-or-smb-3-02-you-are-using.aspx).

| Cliente Windows | Versión de SMB admitida |
|:--- |:--- |
| Windows 7 |SMB 2.1 |
| Windows Server 2008 R2 |SMB 2.1 |
| Windows 8 |SMB 3.0 |
| Windows Server 2012 |SMB 3.0 |
| Windows Server 2012 R2 |SMB 3.0 |
| Windows 10 |SMB 3.0 |

### <a name="mount-the-file-share-from-an-azure-virtual-machine-running-windows"></a>Montaje del recurso compartido de archivos desde una máquina virtual de Azure en la que se ejecute Windows
Para mostrar cómo montar un recurso compartido de archivos de Azure, ahora crearemos una máquina virtual de Azure en la que se ejecuta Windows y accederemos a ella de forma remota para montar el recurso compartido.

1. En primer lugar, cree una nueva máquina virtual de Azure siguiendo las instrucciones que encontrará en [Create a Windows virtual machine with the Azure portal](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Creación de una máquina virtual Windows con Azure Portal).
2. Después, acceda de forma remota a la máquina virtual siguiendo las instrucciones que se indican en [How to connect and log on to an Azure virtual machine running Windows](../virtual-machines/virtual-machines-windows-connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Conexión e inicio de sesión en una máquina virtual Windows mediante Azure Portal).
3. Abra una ventana de PowerShell en la máquina virtual.

### <a name="persist-your-storage-account-credentials-for-the-virtual-machine"></a>Persistencia de las credenciales de la cuenta de almacenamiento para la máquina virtual
Antes de montar el recurso compartido de archivos, primero haga persistir las credenciales de la cuenta de almacenamiento en la máquina virtual. Este paso permite a Windows reconectarse automáticamente con el recurso compartido de archivos cuando la máquina virtual se reinicia. Para que las credenciales de la cuenta sean persistentes, ejecute el comando `cmdkey` desde la ventana de PowerShell de la máquina virtual. Reemplace `<storage-account-name>` por el nombre de la cuenta de almacenamiento y `<storage-account-key>` por la clave de la cuenta de almacenamiento. Tendrá que especificar explícitamente el dominio "AZURE" como en el ejemplo siguiente. 

```
cmdkey /add:<storage-account-name>.file.core.windows.net /user:AZURE\<storage-account-name> /pass:<storage-account-key>
```

Windows se reconectará al recurso compartido de archivos cuando la máquina virtual se reinicie. Para comprobar que el recurso compartido se volvió a conectar, ejecute el comando `net use` desde una ventana de PowerShell.

Tenga en cuenta que las credenciales solo persisten en el contexto en el que se ejecuta `cmdkey`. Si va a desarrollar una aplicación que se ejecute como servicio, será preciso que conserve las credenciales en ese contexto también.

### <a name="mount-the-file-share-using-the-persisted-credentials"></a>Montaje del recurso compartido de archivos usando credenciales de persistencia
Una vez que tenga una conexión remota con la máquina virtual, puede ejecutar el comando `net use` para montar el recurso compartido de archivos, para lo que usará la sintaxis siguiente. Reemplace `<storage-account-name>` por el nombre de su cuenta de almacenamiento y `<share-name>` por el nombre del recurso compartido de Almacenamiento de archivos:

```
net use <drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name>

example :
net use z: \\samples.file.core.windows.net\logs
```

Dado que hizo persistir las credenciales de la cuenta de almacenamiento en el paso anterior, no necesita proporcionarlas con el comando `net use`. Si aún no hizo persistir las credenciales, inclúyalas como parámetro, que se pasará al comando `net use` , como se muestra en el siguiente ejemplo.

```
net use <drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> /u:AZURE\<storage-account-name> <storage-account-key>

example :
net use z: \\samples.file.core.windows.net\logs /u:AZURE\samples <storage-account-key>
```

Ahora puede trabajar con el recurso compartido de Almacenamiento de archivos desde la máquina virtual como lo haría con cualquier otra unidad. Puede emitir comandos de archivo estándar desde el símbolo del sistema o ver el recurso compartido montado y su contenido desde el explorador de archivos. También puede ejecutar código desde la máquina virtual que accede al recurso compartido de archivos usando las API de E/S de archivos Windows estándar, como por ejemplo las proporcionadas por los [espacios de nombres System.IO](http://msdn.microsoft.com/library/gg145019.aspx) en .NET Framework.

También puede montar el recurso compartido de archivos desde un rol que se ejecute en un servicio en la nube de Azure mediante una conexión remota con el rol.

### <a name="mount-the-file-share-from-an-on-premises-client-running-windows"></a>Montaje del recurso compartido de archivos desde un cliente local con Windows
Para montar el recurso compartido de archivos desde un cliente local, es preciso seguir estos pasos:

* Instale una versión de Windows compatible con SMB 3.0. Windows aprovechará el cifrado SMB 3.0 para transferir datos de forma segura entre el cliente local y el recurso compartido de archivos de Azure en la nube.
* Abra el acceso a Internet para el puerto 445 (salida TCP) en la red local, ya que es necesario para el protocolo SMB.

> [!NOTE]
> Algunos proveedores de servicios de Internet pueden bloquear el puerto 445, por lo que es posible que deba ponerse en contacto con su proveedor de servicios.
> 
> 

### <a name="unmount-the-file-share"></a>Desmontaje del recurso compartido de archivos
Para desmontar el recurso compartido de archivos, puede usar el comando `net use` con la opción `/delete`.

```
net use <drive-letter> /delete

example :
net use z: /delete
```

## <a name="develop-with-file-storage"></a>Desarrollo con almacenamiento de archivos
Para escribir código que llame a Almacenamiento de archivos, puede usar las bibliotecas de cliente de almacenamiento para .NET y Java, o la API de REST de Almacenamiento de Azure. En el ejemplo de esta sección se muestra cómo trabajar con un recurso compartido de archivos mediante el uso de la [Biblioteca del cliente de Almacenamiento de Azure para .NET](https://msdn.microsoft.com/library/mt347887.aspx) desde una aplicación de consola simple que se ejecuta en el escritorio.

### <a name="create-the-console-application-and-obtain-the-assembly"></a>Creación de la aplicación de consola y obtención del ensamblado
En Visual Studio, cree una nueva aplicación de consola de Windows. Los pasos siguientes muestran cómo crear una aplicación de consola en Visual Studio 2017; sin embargo, en otras versiones de Visual Studio los pasos son similares.

1. Seleccione **Archivo** > **Nuevo** > **Proyecto**
2. Seleccione **Instalado** > **Plantillas** > **Visual C#** > **Escritorio clásico de Windows**
3. Seleccione **Aplicación de consola (.NET Framework)**
4. Escriba el nombre de la aplicación en el campo **Nombre:**.
5. Seleccione **Aceptar**.

Todos los ejemplos de código de este tutorial se pueden agregar al método `Main()` del archivo `Program.cs` de la aplicación de consola.

La biblioteca del cliente de Azure Storage se puede usar en cualquier tipo de aplicación. NET, incluidos cualquier servicio en la nube o aplicación web de Azure, y aplicaciones de escritorio o móviles. En esta guía, usamos una aplicación de consola para hacerlo más sencillo.

### <a name="use-nuget-to-install-the-required-packages"></a>Uso de NuGet para instalar los paquetes necesarios
Para completar este tutorial, es preciso que haga referencia a dos paquetes en el proyecto:

* [Biblioteca del cliente de Almacenamiento de Microsoft Azure para .NET](https://www.nuget.org/packages/WindowsAzure.Storage/): este paquete proporciona acceso mediante programación a los recursos de datos de la cuenta de almacenamiento.
* [Biblioteca de Administrador de configuración de Microsoft Azure para .NET](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/): este paquete proporciona una clase para analizar una cadena de conexión en un archivo de configuración, independientemente del lugar en que se ejecute la aplicación.

Puede usar NuGet para obtener ambos paquetes. Siga estos pasos:

1. Haga clic con el botón derecho en el proyecto, en el **Explorador de soluciones**, y elija **Administrar paquetes NuGet**.
2. Busque "WindowsAzure.Storage" en línea y haga clic en **Instalar** para instalar el paquete Biblioteca del cliente de Almacenamiento y sus dependencias.
3. Busque "WindowsAzure.ConfigurationManager" en línea y haga clic en **Instalar** para instalar Azure Configuration Manager.

### <a name="save-your-storage-account-credentials-to-the-appconfig-file"></a>Guardar las credenciales de la cuenta de almacenamiento en el archivo app.config
A continuación, guardará las credenciales en el archivo app.config del proyecto. Edite el archivo app.config de forma que su aspecto sea similar al del siguiente ejemplo, pero reemplace `myaccount` por el nombre de su cuenta de almacenamiento y `mykey` por la clave de su cuenta de almacenamiento.

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
    <startup>
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
    </startup>
    <appSettings>
        <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=StorageAccountKeyEndingIn==" />
    </appSettings>
</configuration>
```

> [!NOTE]
> La versión más reciente del emulador de Almacenamiento de Azure no admite Almacenamiento de archivos. Para trabajar con el almacenamiento de archivos, el objetivo de la cadena de conexión debe ser una cuenta de almacenamiento de Azure en la nube.
> 
> 

### <a name="add-using-directives"></a>Adición de directivas using
Abra el archivo `Program.cs` desde el Explorador de soluciones y agregue las siguientes directivas using al principio del archivo.

```csharp
using Microsoft.Azure; // Namespace for Azure Configuration Manager
using Microsoft.WindowsAzure.Storage; // Namespace for Storage Client Library
using Microsoft.WindowsAzure.Storage.Blob; // Namespace for Blob storage
using Microsoft.WindowsAzure.Storage.File; // Namespace for File storage
```

[!INCLUDE [storage-cloud-configuration-manager-include](../../includes/storage-cloud-configuration-manager-include.md)]

### <a name="access-the-file-share-programmatically"></a>Obtener acceso al recurso compartido de archivos mediante programación
A continuación, agregue el siguiente código al método `Main()` (después del código mostrado anteriormente) para recuperar la cadena de conexión. Este código obtiene una referencia al archivo que creamos anteriormente y envía su contenido a la ventana de consola.

```csharp
// Create a CloudFileClient object for credentialed access to File storage.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Get a reference to the file share we created previously.
CloudFileShare share = fileClient.GetShareReference("logs");

// Ensure that the share exists.
if (share.Exists())
{
    // Get a reference to the root directory for the share.
    CloudFileDirectory rootDir = share.GetRootDirectoryReference();

    // Get a reference to the directory we created previously.
    CloudFileDirectory sampleDir = rootDir.GetDirectoryReference("CustomLogs");

    // Ensure that the directory exists.
    if (sampleDir.Exists())
    {
        // Get a reference to the file we created previously.
        CloudFile file = sampleDir.GetFileReference("Log1.txt");

        // Ensure that the file exists.
        if (file.Exists())
        {
            // Write the contents of the file to the console window.
            Console.WriteLine(file.DownloadTextAsync().Result);
        }
    }
}
```

Ejecute la aplicación de consola para ver la salida.

### <a name="set-the-maximum-size-for-a-file-share"></a>Establecer el tamaño máximo para un recurso compartido de archivos
A partir de la versión 5.x de la biblioteca de cliente de almacenamiento de Azure, puede establecer la cuota (o el tamaño máximo) para un recurso compartido de archivos, en gigabytes. También puede comprobar cuántos datos se almacenan actualmente en el recurso compartido.

Al establecer la cuota para un recurso compartido, puede limitar el tamaño total de los archivos almacenados en el recurso compartido. Si el tamaño total de archivos del recurso compartido supera la cuota establecida en el recurso compartido, los clientes no podrán aumentar el tamaño de los archivos existentes ni crear nuevos archivos, a menos que estén vacíos.

En el ejemplo siguiente se muestra cómo comprobar el uso actual de un recurso compartido y cómo establecer la cuota para el recurso compartido.

```csharp
// Parse the connection string for the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create a CloudFileClient object for credentialed access to File storage.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Get a reference to the file share we created previously.
CloudFileShare share = fileClient.GetShareReference("logs");

// Ensure that the share exists.
if (share.Exists())
{
    // Check current usage stats for the share.
    // Note that the ShareStats object is part of the protocol layer for the File service.
    Microsoft.WindowsAzure.Storage.File.Protocol.ShareStats stats = share.GetStats();
    Console.WriteLine("Current share usage: {0} GB", stats.Usage.ToString());

    // Specify the maximum size of the share, in GB.
    // This line sets the quota to be 10 GB greater than the current usage of the share.
    share.Properties.Quota = 10 + stats.Usage;
    share.SetProperties();

    // Now check the quota for the share. Call FetchAttributes() to populate the share's properties.
    share.FetchAttributes();
    Console.WriteLine("Current share quota: {0} GB", share.Properties.Quota);
}
```

### <a name="generate-a-shared-access-signature-for-a-file-or-file-share"></a>Generar una firma de acceso compartido para un archivo o recurso compartido de archivos
A partir de la versión 5.x de la biblioteca de cliente de almacenamiento de Azure, puede generar una firma de acceso compartido (SAS) para un recurso compartido de archivos o para un archivo individual. También puede crear una directiva de acceso compartido en un recurso compartido de archivos para administrar firmas de acceso compartido. Se recomienda la creación de una directiva de acceso compartido, ya que ofrece un medio de revocar la SAS si esta se encuentra en peligro.

En el ejemplo siguiente se crea una directiva de acceso compartido en un recurso compartido y luego se usa esa directiva para ofrecer las restricciones para una SAS en un archivo del recurso compartido.

```csharp
// Parse the connection string for the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create a CloudFileClient object for credentialed access to File storage.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Get a reference to the file share we created previously.
CloudFileShare share = fileClient.GetShareReference("logs");

// Ensure that the share exists.
if (share.Exists())
{
    string policyName = "sampleSharePolicy" + DateTime.UtcNow.Ticks;

    // Create a new shared access policy and define its constraints.
    SharedAccessFilePolicy sharedPolicy = new SharedAccessFilePolicy()
        {
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Permissions = SharedAccessFilePermissions.Read | SharedAccessFilePermissions.Write
        };

    // Get existing permissions for the share.
    FileSharePermissions permissions = share.GetPermissions();

    // Add the shared access policy to the share's policies. Note that each policy must have a unique name.
    permissions.SharedAccessPolicies.Add(policyName, sharedPolicy);
    share.SetPermissions(permissions);

    // Generate a SAS for a file in the share and associate this access policy with it.
    CloudFileDirectory rootDir = share.GetRootDirectoryReference();
    CloudFileDirectory sampleDir = rootDir.GetDirectoryReference("CustomLogs");
    CloudFile file = sampleDir.GetFileReference("Log1.txt");
    string sasToken = file.GetSharedAccessSignature(null, policyName);
    Uri fileSasUri = new Uri(file.StorageUri.PrimaryUri.ToString() + sasToken);

    // Create a new CloudFile object from the SAS, and write some text to the file.
    CloudFile fileSas = new CloudFile(fileSasUri);
    fileSas.UploadText("This write operation is authenticated via SAS.");
    Console.WriteLine(fileSas.DownloadText());
}
```

Para más información sobre la creación y el uso de firmas de acceso compartido, consulte [Uso de firmas de acceso compartido (SAS)](storage-dotnet-shared-access-signature-part-1.md) y [Creación y uso de una SAS con Blob Storage](storage-dotnet-shared-access-signature-part-2.md).

### <a name="copy-files"></a>Copiar archivos
A partir de la versión 5.x de la biblioteca de cliente de almacenamiento de Azure, puede copiar un archivo en otro, un archivo en un blob o un blob en un archivo. En las siguientes secciones, mostramos cómo realizar estas operaciones de copia mediante programación.

También puede usar AzCopy para copiar un archivo en otro o para copiar un blob en un archivo o viceversa. Consulte [Transferencia de datos con la utilidad en línea de comandos AzCopy](storage-use-azcopy.md).

> [!NOTE]
> Si va a copiar un blob en un archivo, o un archivo en un blob, debe usar una firma de acceso compartido (SAS) para autenticar el objeto de origen, incluso si está copiando en la misma cuenta de almacenamiento.
> 
> 

**Copiar un archivo en otro**

En el ejemplo siguiente se copia un archivo en otro en el mismo recurso compartido. Dado que en esta operación de copia se copia entre archivos de la misma cuenta de almacenamiento, puede usar la autenticación de clave compartida para realizar la copia.

```csharp
// Parse the connection string for the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create a CloudFileClient object for credentialed access to File storage.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Get a reference to the file share we created previously.
CloudFileShare share = fileClient.GetShareReference("logs");

// Ensure that the share exists.
if (share.Exists())
{
    // Get a reference to the root directory for the share.
    CloudFileDirectory rootDir = share.GetRootDirectoryReference();

    // Get a reference to the directory we created previously.
    CloudFileDirectory sampleDir = rootDir.GetDirectoryReference("CustomLogs");

    // Ensure that the directory exists.
    if (sampleDir.Exists())
    {
        // Get a reference to the file we created previously.
        CloudFile sourceFile = sampleDir.GetFileReference("Log1.txt");

        // Ensure that the source file exists.
        if (sourceFile.Exists())
        {
            // Get a reference to the destination file.
            CloudFile destFile = sampleDir.GetFileReference("Log1Copy.txt");

            // Start the copy operation.
            destFile.StartCopy(sourceFile);

            // Write the contents of the destination file to the console window.
            Console.WriteLine(destFile.DownloadText());
        }
    }
}
```

**Copiar un archivo en un blob**

En el ejemplo siguiente se crea un archivo y se copia en un blob de la misma cuenta de almacenamiento. El ejemplo se crea una SAS para el archivo de origen, que el servicio usa para autenticar el acceso al archivo de origen durante la operación de copia.

```csharp
// Parse the connection string for the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create a CloudFileClient object for credentialed access to File storage.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Create a new file share, if it does not already exist.
CloudFileShare share = fileClient.GetShareReference("sample-share");
share.CreateIfNotExists();

// Create a new file in the root directory.
CloudFile sourceFile = share.GetRootDirectoryReference().GetFileReference("sample-file.txt");
sourceFile.UploadText("A sample file in the root directory.");

// Get a reference to the blob to which the file will be copied.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
CloudBlobContainer container = blobClient.GetContainerReference("sample-container");
container.CreateIfNotExists();
CloudBlockBlob destBlob = container.GetBlockBlobReference("sample-blob.txt");

// Create a SAS for the file that's valid for 24 hours.
// Note that when you are copying a file to a blob, or a blob to a file, you must use a SAS
// to authenticate access to the source object, even if you are copying within the same
// storage account.
string fileSas = sourceFile.GetSharedAccessSignature(new SharedAccessFilePolicy()
{
    // Only read permissions are required for the source file.
    Permissions = SharedAccessFilePermissions.Read,
    SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24)
});

// Construct the URI to the source file, including the SAS token.
Uri fileSasUri = new Uri(sourceFile.StorageUri.PrimaryUri.ToString() + fileSas);

// Copy the file to the blob.
destBlob.StartCopy(fileSasUri);

// Write the contents of the file to the console window.
Console.WriteLine("Source file contents: {0}", sourceFile.DownloadText());
Console.WriteLine("Destination blob contents: {0}", destBlob.DownloadText());
```

Puede copiar un blob en un archivo de la misma manera. Si el objeto de origen es un blob, cree una SAS para autenticar el acceso a dicho blob durante la operación de copia.

## <a name="troubleshooting-file-storage-using-metrics"></a>Solución de problemas de almacenamiento de archivos mediante métricas
Análisis de almacenamiento de Azure admite métricas para el almacenamiento de archivos. Con los datos de las métricas, es posible seguir paso a paso las solicitudes y diagnosticar problemas.

En [Azure Portal](https://portal.azure.com) se puede habilitar la métrica de File Storage. También se puede habilitar mediante programación, para lo que hay que llamar a la operación establecer propiedades del servicio de archivos a través de la API de REST o una de sus análogas de la Biblioteca del cliente de almacenamiento.

En el siguiente ejemplo de código se muestra cómo usar la Biblioteca del cliente de almacenamiento para .NET a fin de habilitar las métricas para Almacenamiento de archivos.

En primer lugar, agregue las siguientes directivas `using` a su archivo `Program.cs`, además de las que ya ha agregado:

```csharp
using Microsoft.WindowsAzure.Storage.File.Protocol;
using Microsoft.WindowsAzure.Storage.Shared.Protocol;
```

Tenga en cuenta que mientras que los almacenamientos en blobs, tablas y colas utilizan el tipo `ServiceProperties` compartido en el espacio de nombres `Microsoft.WindowsAzure.Storage.Shared.Protocol`, el Almacenamiento de archivos utiliza el suyo propio, el tipo `FileServiceProperties` en el espacio de nombres `Microsoft.WindowsAzure.Storage.File.Protocol`. No obstante, se debe hacer referencia a ambos espacios de nombres en el código para que se pueda compilar.

```csharp
// Parse your storage connection string from your application's configuration file.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));
// Create the File service client.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Set metrics properties for File service.
// Note that the File service currently uses its own service properties type,
// available in the Microsoft.WindowsAzure.Storage.File.Protocol namespace.
fileClient.SetServiceProperties(new FileServiceProperties()
{
    // Set hour metrics
    HourMetrics = new MetricsProperties()
    {
        MetricsLevel = MetricsLevel.ServiceAndApi,
        RetentionDays = 14,
        Version = "1.0"
    },
    // Set minute metrics
    MinuteMetrics = new MetricsProperties()
    {
        MetricsLevel = MetricsLevel.ServiceAndApi,
        RetentionDays = 7,
        Version = "1.0"
    }
});

// Read the metrics properties we just set.
FileServiceProperties serviceProperties = fileClient.GetServiceProperties();
Console.WriteLine("Hour metrics:");
Console.WriteLine(serviceProperties.HourMetrics.MetricsLevel);
Console.WriteLine(serviceProperties.HourMetrics.RetentionDays);
Console.WriteLine(serviceProperties.HourMetrics.Version);
Console.WriteLine();
Console.WriteLine("Minute metrics:");
Console.WriteLine(serviceProperties.MinuteMetrics.MetricsLevel);
Console.WriteLine(serviceProperties.MinuteMetrics.RetentionDays);
Console.WriteLine(serviceProperties.MinuteMetrics.Version);
```

Además, puede hacer referencia al [artículo de solución de problemas de archivos de Azure](storage-troubleshoot-file-connection-problems.md) para ver una guía completa de solución de problemas. 

## <a name="file-storage-faq"></a>Preguntas más frecuentes sobre el almacenamiento de archivos
1. **¿Admite el almacenamiento de archivos la autenticación basada en Active Directory?**
   
    En la actualidad no se admite la autenticación basada en AD ni las ACL, pero se admitirá en un futuro cercano. Por ahora, las claves de las cuentas de Almacenamiento de Azure se usan para proporcionar autenticación al recurso compartido de archivos. La solución alternativa es usar las firmas de acceso compartido (SAS) a través de la API de REST o de las bibliotecas de cliente. Con SAS se pueden generar tokens con permisos específicos que son válidos durante un intervalo especificado. Por ejemplo, se puede generar un token con acceso de solo lectura a un archivo dado. Todos los usuarios que posean dicho token, mientras tenga validez, tendrán acceso de solo lectura al archivo.
   
    SAS solo se admite a través de la API de REST o de las bibliotecas de cliente. Si el recurso compartido de archivos se monta a través del protocolo SMB, no se puede usar una SAS para delegar el acceso a su contenido. 

2. **¿Cómo se puede proporcionar acceso a un archivo específico a través de un explorador web?**
   Con SAS se pueden generar tokens con permisos específicos que son válidos durante un intervalo especificado. Por ejemplo, puede generar un token con acceso de solo lectura a un archivo determinado para un período de tiempo específico. Cualquier persona que posea esta dirección URL puede realizar la descarga directamente desde cualquier explorador web mientras sea válido. Las claves SAS se pueden generar fácilmente desde una interfaz de usuario, como el Explorador de Storage.

3.   **¿Cuáles son las diferentes formas de acceder a los archivos de Azure File Storage?**
    Puede montar el recurso compartido de archivos en la máquina local mediante el protocolo SMB 3.0 o usar herramientas como el [Explorador de Storage](http://storageexplorer.com/) o Cloudberry para acceder a los archivos en el recurso compartido de archivos. Desde su aplicación, puede usar las bibliotecas de cliente, la API de REST o Powershell para acceder a los archivos en el recurso compartido de archivos de Azure.
    
4.   **¿Cómo puedo montar un recurso compartido de archivos de Azure en mi máquina local?** Puede montar el recurso compartido de archivos a través del protocolo SMB siempre que el puerto 445 (salida TCP) esté abierto y el cliente admita el protocolo SMB 3.0 (*p. ej.*, Windows 8 o Windows Server 2012). Trabaje con su proveedor de ISP local para desbloquear el puerto. Mientras tanto, puede ver los archivos con el Explorador de Storage u otras aplicaciones de terceros como Cloudberry.

5. **¿Cuenta el tráfico de red entre una máquina virtual de Azure y un número de recursos compartidos de archivos como ancho de banda externo que se carga a la suscripción?**
   
    Si el recurso compartido de archivos y la máquina virtual se encuentran en distintas regiones, el tráfico entre ellos se cargará como ancho de banda externo.
6. **Si el tráfico de red se produce entre una máquina virtual y un recurso compartido de archivos de la misma región, ¿es gratuito?**
   
    Sí. Es gratis si el tráfico se produce en la misma región.
7. **¿Depende la conexión entre máquinas virtuales locales y el almacenamiento de archivos de Azure de Azure ExpressRoute?**
   
    No. Aunque no se disponga de ExpressRoute, es posible acceder al recurso compartido de archivos de forma local, siempre que el puerto 445 (salida TCP) esté abierto para el acceso a Internet. Sin embargo, si se desea, es posible usar ExpressRoute con el almacenamiento de archivos.
8. **¿Es un "testigo del recurso compartido de archivos" para un clúster de conmutación por error uno de los casos de uso del almacenamiento de archivos de Azure?**
   
    En la actualidad no se admite.
9. **En la actualidad, el almacenamiento de archivos solo se replica a través de LRS o GRS, ¿es así?**  
   
    Está previsto que RA-GRS se admite, pero aún no se puede indicar ninguna fecha.
10. **¿Cuándo se podrán usar las cuentas de almacenamiento existentes con el almacenamiento de archivos de Azure?**
   
    El almacenamiento de archivos de Azure ya está habilitado para todas las cuentas de almacenamiento.
11. **¿Se agregará también una operación de cambio de nombre a la API de REST?**
   
    La operación de cambio de nombre aún no se admite en la API de REST.
12. **¿Es posible tener recursos compartidos anidados, es decir, un recurso compartido en un recurso compartido?**
    
    No. El recurso compartido de archivos es el controlador virtual que se puede montar, por lo que no se admiten recursos compartidos anidados.
13. **¿Es posible especificar permisos de solo lectura o solo escritura en las carpetas del recurso compartido?**
    
    Si el recurso compartido de archivos se monta a través de SMB, no se tiene este nivel de control sobre los permisos. Sin embargo, se puede lograr. Para ello, es preciso crear una firma de acceso compartido (SAS) mediante la API de REST o las bibliotecas de cliente.  
14. **Mi rendimiento era lento al intentar descomprimir archivos en el almacenamiento de archivos. ¿qué debo hacer?**
    
    Para transferir una gran cantidad de archivos al almacenamiento de archivos es aconsejable usar AzCopy, Azure Powershell (Windows) o la CLI de Azure (Linux/Unix), ya que estas herramientas se optimizaron para la transferencia a través de red.
15. **Revisión publicada para corregir el problema de rendimiento lento con archivos de Azure**
    
    El equipo de Windows ha publicado recientemente una revisión para solucionar un problema de rendimiento lento cuando el cliente accede a Almacenamiento de archivos de Azure desde Windows 8.1 o Windows Server 2012 R2. Para más información, consulte el artículo asociado de Knowledge Base, [Rendimiento lento en el acceso a Azure Files Storage desde Windows 8.1 o Server 2012 R2](https://support.microsoft.com/kb/3114025).
16. **Uso del Almacenamiento de archivos de Azure con IBM MQ**
    
    IBM ha publicado un documento para guiar a los clientes de IBM MQ a la hora de configurar el Almacenamiento de archivos de Azure con su servicio. Para más información, consulte [How to setup IBM MQ Multi instance queue manager with Microsoft Azure File Service](https://github.com/ibm-messaging/mq-azure/wiki/How-to-setup-IBM-MQ-Multi-instance-queue-manager-with-Microsoft-Azure-File-Service)(Configuración del administrador de colas de varias instancias de IBM MQ con el servicio de archivos de Microsoft Azure).
17. **¿Cómo se pueden solucionar los errores de Azure File Storage?**
    
    Puede hacer referencia al [artículo de solución de problemas de archivos de Azure](storage-troubleshoot-file-connection-problems.md) para ver una guía completa de solución de problemas.               

18. **¿Cómo se puede habilitar el cifrado del lado del servidor en Azure Files?**
> [!NOTE]
> El [cifrado del lado del servidor](storage-service-encryption.md) para Azure Files está actualmente en versión preliminar. Puede ponerse en contacto con [SSEDiscussion](mailto:ssediscussions@microsoft.com) si tiene alguna pregunta durante la versión preliminar.

    [Server Side Encryption](storage-service-encryption.md) for Azure Files is currently in preview. During preview, you can enable this feature only on new Azure Resource Manager storage accounts created by using the [Azure portal](https://portal.azure.com). There is no additional charge for enabling this feature. When you enable Storage Service Encryption for Azure File Storage, your data is automatically encrypted for you. 
    
    We plan to support enabling encryption for file storage with [Azure PowerShell](/powershell/resourcemanager/azurerm.storage/v2.7.0/azurerm.storage), [Azure CLI](storage-azure-cli.md), and the [Azure Storage Resource Provider REST API](/rest/api/storagerp/storageaccounts) in the future. 
    See [Storage Service Encryption](storage-service-encryption.md) for more information about encryption at rest in Azure Storage, and you can contact ssediscussions@microsoft.com if you have questions during the preview.

## <a name="next-steps"></a>Pasos siguientes
Consulte los vínculos siguientes para obtener más información acerca de Almacenamiento de archivos de Azure.

### <a name="conceptual-articles-and-videos"></a>Artículos y vídeos conceptuales
* [Almacenamiento de archivos de Azure: un sistema de archivos SMB en la nube sin dificultades para Windows y Linux](https://azure.microsoft.com/documentation/videos/azurecon-2015-azure-files-storage-a-frictionless-cloud-smb-file-system-for-windows-and-linux/)
* [Uso del almacenamiento de archivos de Azure con Linux](storage-how-to-use-files-linux.md)

### <a name="tooling-support-for-file-storage"></a>Compatibilidad de herramientas con el almacenamiento de archivos
* [Usar Azure PowerShell con Almacenamiento de Azure](storage-powershell-guide-full.md)
* [Uso de AzCopy con Almacenamiento de Microsoft Azure](storage-use-azcopy.md)
* [Uso de la CLI de Azure con Almacenamiento de Azure](storage-azure-cli.md#create-and-manage-file-shares)
* [Solución de problemas de Azure File Storage](https://docs.microsoft.com/azure/storage/storage-troubleshoot-file-connection-problems)

### <a name="reference"></a>Referencia
* [Referencia de la biblioteca de clientes de almacenamiento para .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx)
* [Referencia de la API REST del servicio de archivos](http://msdn.microsoft.com/library/azure/dn167006.aspx)

### <a name="blog-posts"></a>Publicaciones de blog
* [El almacenamiento de archivos de Azure ya está disponible de manera general](https://azure.microsoft.com/blog/azure-file-storage-now-generally-available/)
* [Almacenamiento de archivos dentro de Azure](https://azure.microsoft.com/blog/inside-azure-file-storage/)
* [Introducing Microsoft Azure File Service (Introducción al servicio de archivos de Microsoft Azure)](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx)
* [Persisting connections to Microsoft Azure Files (Persistencia de conexiones en archivos de Microsoft Azure)](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx)

