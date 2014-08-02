<properties linkid="manage-services-storage-using-storage-with-windows-store-apps" urlDisplayName="Azure Storage in Windows Store apps" pageTitle="Use Azure storage in Windows Store Apps | Azure" metaKeywords="" description="Learn how to use Azure blobs, queues, and tables to store data for a Windows Store app." metaCanonical="" services="storage" documentationCenter="" title="How to use Azure Storage in Windows Store Apps" authors="" solutions="" manager="" editor="" />

Uso de Almacenamiento de Azure en las aplicaciones de la Tienda Windows
=======================================================================

Esta guía le muestra cómo comenzar con el desarrollo de una aplicación de la Tienda Windows que haga uso del almacenamiento de Azure.

Descarga de las herramientas necesarias
---------------------------------------

-   [Visual Studio 2012](http://msdn.microsoft.com/en-us/library/windows/apps/br211384) hace que sea fácil compilar, depurar, localizar, empaquetar e implementar aplicaciones de la Tienda Windows.
-   La [Biblioteca del cliente de almacenamiento de Azure para Windows en tiempo de ejecución](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/11/05/windows-azure-storage-client-library-for-windows-runtime.aspx) contiene los tipos usados para la comunicación con almacenamiento de Azure, empaquetada en un componente de Windows en tiempo de ejecución.
-   El componente [Herramientas de servicios de datos WCF para aplicaciones de la Tienda Windows](http://www.microsoft.com/en-us/download/details.aspx?id=30714) amplía la experiencia de incorporación de referencia de servicios con soporte OData del cliente para aplicaciones de la Tienda Windows en Visual Studio 2012.

Desarrollo de aplicaciones
--------------------------

### Preparación

Cree un nuevo proyecto de aplicaciones de la Tienda Windows en Visual Studio 2012:

![store-apps-storage-vs-project](./media/storage-use-store-apps/store-apps-storage-vs-project.png)

A continuación, agregue una referencia a la biblioteca del cliente de almacenamiento de Azure haciendo clic con el botón secundario en **References**, seleccionando **Add Reference** y buscando la biblioteca de cliente de almacenamiento para Windows en tiempo de ejecución que ha descargado:

![store-apps-storage-choose-library](./media/storage-use-store-apps/store-apps-storage-choose-library.png)

### Uso de la biblioteca con los servicios BLOB y Cola

En este momento, la aplicación está preparada para comunicarse con los servicios BLOB y Cola. Agregue las siguientes instrucciones **using** de manera que pueda hacerse referencia directamente a los tipos de Almacenamiento de Azure:

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;

A continuación, agregue un botón a la página. Agregue el siguiente código al evento **Click** y modifique el método del controlador de eventos con [async (Referencia de C\#)](http://msdn.microsoft.com/en-US/library/vstudio/hh156513.aspx):

    var credentials = new StorageCredentials(accountName, accountKey);
    var account = new CloudStorageAccount(credentials, true);
    var blobClient = account.CreateCloudBlobClient();
    var container = blobClient.GetContainerReference("container1");
    await container.CreateIfNotExistsAsync();

Este código presupone que dispone de dos variables de cadena, *accountName* y *accountKey*, que representan el nombre de la cuenta de almacenamiento y la clave de cuenta asociada a esa cuenta.

Compile y ejecute la aplicación. Si hace clic en el botón, se comprobará primero si existe un contenedor con el nombre *container1* en la cuenta y se creará si no existiera.

### Uso de la biblioteca con el servicio Tabla

Los tipos usados para comunicarse con el servicio Tabla dependen de la biblioteca de servicios de datos WCF para las aplicaciones de la Tienda Windows. A continuación, agregue una referencia a las bibliotecas WCF necesarias mediante la consola del administrador de paquetes:

![store-apps-storage-package-manager](./media/storage-use-store-apps/store-apps-storage-package-manager.png)

Use el siguiente comando para que el administrador de paquetes apunte a la ubicación de la máquina:

    Install-Package Microsoft.Data.OData.WindowsStore -Source "C:\Program Files (x86)\Microsoft WCF Data Services.0\bin\NuGet"

Este comando agregará automáticamente todas las referencias necesarias a su proyecto. Si no desea usar la consola del administrador de paquetes, también puede agregar la carpeta NuGet de servicios de datos WCF en su máquina local a la lista de orígenes de paquetes y, a continuación, agregar la referencia a través de la interfaz de usuario como se describe en [Administración de paquetes NuGet mediante el cuadro de diálogo](http://docs.nuget.org/docs/start-here/Managing-NuGet-Packages-Using-The-Dialog).

Cuando haya hecho referencia al paquete NuGet de servicios de datos WCF, cambie el código en el evento **Click** del botón:

    var credentials = new StorageCredentials(accountName, accountKey);
    var account = new CloudStorageAccount(credentials, true);
    var tableClient = account.CreateCloudTableClient();
    var table = tableClient.GetTableReference("table1");
    await table.CreateIfNotExistsAsync();

Este código comprueba si la tabla con el nombre *table1* existe en su cuenta y se crea si no existiera.

También puede agregar una referencia a Microsoft.WindowsAzure.Storage.Table.dll disponible en el mismo paquete que descargó. Esta biblioteca contiene una funcionalidad adicional como consultas genéricas y de serialización basadas en reflejo. Tenga en cuenta que esta biblioteca no es compatible con JavaScript.

