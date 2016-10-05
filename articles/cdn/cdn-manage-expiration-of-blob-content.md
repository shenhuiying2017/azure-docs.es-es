<properties
 pageTitle="Administración de la expiración del contenido de los blobs de Almacenamiento de Azure en la red CDN de Azure | Microsoft Azure"
 description="Obtenga información sobre las opciones para controlar el período de vida de los blobs de almacenamiento en caché de CDN de Azure."
 services="cdn"
 documentationCenter=""
 authors="camsoper"
 manager="erikre"
 editor=""/>
<tags
 ms.service="cdn"
 ms.workload="media"
 ms.tgt_pltfrm="na"
 ms.devlang="multiple"
 ms.topic="article"
 ms.date="09/15/2016"
 ms.author="casoper"/>


# Administración de la expiración del contenido de los blobs de Almacenamiento de Azure en la red CDN de Azure

> [AZURE.SELECTOR]
- [Azure Web Apps/Cloud Services, ASP.NET o IIS](cdn-manage-expiration-of-cloud-service-content.md)
- [Azure Storage Blob service](cdn-manage-expiration-of-blob-content.md)

El [servicio BLOB](../storage/storage-introduction.md#blob-storage) en [Almacenamiento de Azure](../storage/storage-introduction.md) es uno de diferentes orígenes basados en Azure integrados con la red CDN de Azure. Cualquier contenido de blob accesible públicamente se puede almacenar en caché en la red CDN de Azure hasta que transcurra su tiempo de vida (TTL). El TTL viene determinado por el encabezado [*Cache-Control* ](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.9) en la respuesta HTTP del Almacenamiento de Azure.

>[AZURE.TIP] Puede optar por no configurar ningún TTL en un blob. En este caso, la red CDN de Azure aplica automáticamente un valor predeterminado de TTL de siete días.
>
>Para obtener más información acerca del funcionamiento de la red CDN de Azure para acelerar el acceso a los blobs y otros archivos, consulte [Información general de la red de entrega de contenido (CDN) de Azure](./cdn-overview.md).
>
>Para obtener más detalles acerca del servicio BLOB de Almacenamiento de Azure, vea [Conceptos del servicio BLOB](https://msdn.microsoft.com/library/dd179376.aspx).

Este tutorial muestra varias maneras de establecer el TTL de un blob en Almacenamiento de Azure.

## Azure PowerShell

[Azure PowerShell](../powershell-install-configure.md) es una de las maneras más rápidas y más eficaces de administrar los servicios de Azure. Utilice el cmdlet `Get-AzureStorageBlob` para obtener una referencia al blob y, a continuación, establezca la propiedad `.ICloudBlob.Properties.CacheControl`.

```powershell
# Create a storage context
$context = New-AzureStorageContext -StorageAccountName "<storage account name>" -StorageAccountKey "<storage account key>"

# Get a reference to the blob
$blob = Get-AzureStorageBlob -Context $context -Container "<container name>" -Blob "<blob name>"

# Set the CacheControl property to expire in 1 hour (3600 seconds)
$blob.ICloudBlob.Properties.CacheControl = "public, max-age=3600"

# Send the update to the cloud
$blob.ICloudBlob.SetProperties()
```

>[AZURE.TIP] También puede usar PowerShell para [administrar los perfiles y puntos de conexión de la red CDN](./cdn-manage-powershell.md).

## Biblioteca de cliente de Almacenamiento de Azure para .NET

Para establecer el TTL de un blob con .NET, utilice la [Biblioteca de cliente de Almacenamiento de Azure para .NET](../storage/storage-dotnet-how-to-use-blobs.md) para configurar la propiedad [CloudBlob.Properties.CacheControl](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.blobproperties.cachecontrol.aspx).

```csharp
class Program
{
	const string connectionString = "<storage connection string>";
	static void Main()
	{
		// Retrieve storage account information from connection string
		CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString);
		
		// Create a blob client for interacting with the blob service.
		CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
		
		// Create a reference to the container
		CloudBlobContainer container = blobClient.GetContainerReference("<container name>");

		// Create a reference to the blob
		CloudBlob blob = container.GetBlobReference("<blob name>");

		// Set the CacheControl property to expire in 1 hour (3600 seconds)
		blob.Properties.CacheControl = "public, max-age=3600";

		// Update the blob's properties in the cloud
		blob.SetProperties();
	}
}
```

>[AZURE.TIP] Hay muchos más ejemplos de código de .NET en [Azure Blob Storage Samples for .NET](https://azure.microsoft.com/documentation/samples/storage-blob-dotnet-getting-started/) (Ejemplos de Almacenamiento de blobs de Azure para .NET).

## Otros métodos

- [Interfaz de la línea de comandos de Azure](../xplat-cli-install.md)

	Al cargar el blob, establezca la propiedad *cacheControl* utilizando el conmutador `-p`. En este ejemplo se establece el TTL en una hora (3600 segundos).

	```text
	azure storage blob upload -c <connectionstring> -p cacheControl="public, max-age=3600" .\test.txt myContainer test.txt
	```

- [API de REST de servicios de almacenamiento de Azure](https://msdn.microsoft.com/library/azure/dd179355.aspx)

	Establezca explícitamente la propiedad *x-ms-blob-cache-control* en una solicitud [Put Blob](https://msdn.microsoft.com/es-ES/library/azure/dd179451.aspx), [Put Block List](https://msdn.microsoft.com/es-ES/library/azure/dd179467.aspx) o [Set Blob Properties](https://msdn.microsoft.com/library/azure/ee691966.aspx).

- Herramientas de administración de almacenamiento de terceros

	Algunas herramientas de administración de Almacenamiento de Azure de terceros permiten establecer la propiedad *CacheControl* en blobs.

## Prueba del encabezado *Cache-Control*

Puede comprobar fácilmente el TTL de los blobs. Mediante las [herramientas para desarrollador](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/) del explorador, pruebe que el blob incluye el encabezado de respuesta *Cache-Control*. También puede utilizar una herramienta como **wget**, [Postman](https://www.getpostman.com/) o [Fiddler](http://www.telerik.com/fiddler) para examinar los encabezados de respuesta.

## Pasos siguientes

- [Lea acerca del encabezado *Cache-Control*](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.9).
- [Aprenda a administrar la expiración del contenido del Servicio en la nube en la red CDN de Azure](./cdn-manage-expiration-of-cloud-service-content.md)

<!---HONumber=AcomDC_0921_2016-->