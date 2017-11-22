---
title: Administrar la caducidad de Azure Blob Storage en Azure Content Delivery Network | Microsoft Docs
description: "Obtenga información sobre las opciones para controlar el período de vida de los blobs de almacenamiento en caché de CDN de Azure."
services: cdn
documentationcenter: 
author: zhangmanling
manager: erikre
editor: 
ms.assetid: ad4801e9-d09a-49bf-b35c-efdc4e6034e8
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 11/10/2017
ms.author: mazha
ms.openlocfilehash: 41b8f9d439184b91f8105e6bd136e48525632a85
ms.sourcegitcommit: 659cc0ace5d3b996e7e8608cfa4991dcac3ea129
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/13/2017
---
# <a name="manage-expiration-of-azure-blob-storage-in-azure-content-delivery-network"></a>Administrar la caducidad de Azure Blob Storage en Azure Content Delivery Network
> [!div class="op_single_selector"]
> * [Azure Web Apps/Cloud Services, ASP.NET o IIS](cdn-manage-expiration-of-cloud-service-content.md)
> * [Almacenamiento de blobs de Azure](cdn-manage-expiration-of-blob-content.md)
> 
> 

[Blob service](../storage/common/storage-introduction.md#blob-storage) de [Azure Storage](../storage/common/storage-introduction.md) es uno de los muchos orígenes basados en Azure que están integrados con Azure Content Delivery Network (CDN). Cualquier contenido de blob accesible públicamente se puede almacenar en caché en la red CDN de Azure hasta que transcurra su tiempo de vida (TTL). El TTL se determina en función del encabezado [`Cache-Control` ](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.9) en la respuesta HTTP de Azure Storage.

> [!TIP]
> Puede optar por no configurar ningún TTL en un blob. En este caso, la red CDN de Azure aplica automáticamente un valor predeterminado de TTL de siete días.
> 
> Para obtener más información sobre el funcionamiento de Azure CDN para acelerar el acceso a los blobs y a otros archivos, consulte [Información general de Azure Content Delivery Network](cdn-overview.md).
> 
> Para obtener más información acerca de Azure Blob Storage, consulte [Introducción a Blob Storage](https://docs.microsoft.com/en-us/azure/storage/blobs/storage-blobs-introduction).
 

Este tutorial muestra varias maneras de establecer el TTL de un blob en Azure Storage.  

## <a name="azure-powershell"></a>Azure PowerShell
[Azure PowerShell](/powershell/azure/overview) es una de las maneras más rápidas y más eficaces de administrar los servicios de Azure.  Utilice el cmdlet `Get-AzureStorageBlob` para obtener una referencia al blob y, a continuación, establezca la propiedad `.ICloudBlob.Properties.CacheControl`. 

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

> [!TIP]
> También puede usar PowerShell para [administrar los perfiles y puntos de conexión de la red CDN](cdn-manage-powershell.md).
> 
> 

## <a name="azure-storage-client-library-for-net"></a>Biblioteca de cliente de Azure Storage para .NET
Para establecer el TTL de un blob con .NET, use la [Biblioteca de cliente de Azure Storage para .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md) para configurar la propiedad [CloudBlob.Properties.CacheControl](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.blobproperties.cachecontrol.aspx).

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

> [!TIP]
> Hay muchos más ejemplos de código de .NET en [Azure Blob Storage Samples for .NET](https://azure.microsoft.com/documentation/samples/storage-blob-dotnet-getting-started/) (Ejemplos de Azure Blob Storage para .NET).
> 
> 

## <a name="other-methods"></a>Otros métodos
* [Interfaz de la línea de comandos de Azure](../cli-install-nodejs.md)
  
    Al cargar el blob, establezca la propiedad *cacheControl* mediante el modificador `-p`. En este ejemplo se establece el TTL en una hora (3600 segundos).
  
    ```text
    azure storage blob upload -c <connectionstring> -p cacheControl="public, max-age=3600" .\test.txt myContainer test.txt
    ```
* [API de REST de servicios de Azure Storage](https://msdn.microsoft.com/library/azure/dd179355.aspx)
  
    Establezca explícitamente la propiedad *x-ms-blob-cache-control* en una solicitud [Put Blob](https://msdn.microsoft.com/en-us/library/azure/dd179451.aspx), [Put Block List](https://msdn.microsoft.com/en-us/library/azure/dd179467.aspx) o [Set Blob Properties](https://msdn.microsoft.com/library/azure/ee691966.aspx).
* Herramientas de administración de almacenamiento de terceros
  
    Algunas herramientas de administración de Azure Storage de terceros permiten establecer la propiedad *CacheControl* en blobs. 

## <a name="testing-the-cache-control-header"></a>Prueba del encabezado Cache-Control
Puede comprobar fácilmente el TTL de los blobs.  Mediante las [herramientas para desarrollador](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/) del explorador, compruebe que el blob incluye el encabezado de respuesta `Cache-Control`. Asimismo, también puede usar una herramienta como **wget**, [Postman](https://www.getpostman.com/) o [Fiddler](http://www.telerik.com/fiddler) para examinar los encabezados de respuesta.

## <a name="next-steps"></a>Pasos siguientes
* [Obtenga información sobre el encabezado `Cache-Control`](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.9)
* [Obtenga información sobre cómo administrar la expiración del contenido de Servicio en la nube en la red CDN de Azure](cdn-manage-expiration-of-cloud-service-content.md)

