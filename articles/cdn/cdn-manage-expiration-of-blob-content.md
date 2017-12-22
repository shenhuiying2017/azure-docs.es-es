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
ms.openlocfilehash: 6f82ae396a17f903a522c716f73a5f7d2de660e7
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/21/2017
---
# <a name="manage-expiration-of-azure-blob-storage-in-azure-content-delivery-network"></a>Administrar la caducidad de Azure Blob Storage en Azure Content Delivery Network
> [!div class="op_single_selector"]
> * [Contenido web de Azure](cdn-manage-expiration-of-cloud-service-content.md)
> * [Almacenamiento de blobs de Azure](cdn-manage-expiration-of-blob-content.md)
> 
> 

El [servicio Blob Storage](../storage/common/storage-introduction.md#blob-storage) de Azure Storage es uno de los distintos orígenes basados en Azure que están integrados en Azure Content Delivery Network (CDN). Cualquier contenido de blob accesible públicamente se puede almacenar en caché en la red CDN de Azure hasta que transcurra su tiempo de vida (TTL). El período de vida viene determinado por el encabezado `Cache-Control` en la respuesta HTTP del servidor de origen. En este artículo se describen las distintas maneras en que se puede establecer el encabezado `Cache-Control` de un blob en Azure Storage.

También puede controlar la configuración de caché desde Azure Portal estableciendo [reglas de almacenamiento en caché de la red CDN](cdn-caching-rules.md). Si configura una o más reglas de almacenamiento en caché y establece el comportamiento de dicho almacenamiento en **Invalidar** u **Omitir caché**, se omite la configuración de almacenamiento en caché proporcionada por el origen que se trata en este artículo. Para información sobre conceptos generales de almacenamiento en caché, consulte [Funcionamiento del almacenamiento en caché](cdn-how-caching-works.md).

> [!TIP]
> Puede optar por no configurar ningún TTL en un blob. En este caso, Azure CDN aplica automáticamente un TTL predeterminado de siete días, a menos que haya configurado reglas de almacenamiento en caché en Azure Portal. Este TTL predeterminado solo se aplica a las optimizaciones de entrega web general. Para las optimizaciones de archivos de gran tamaño, el TTL predeterminado es un día, y para las optimizaciones de streaming multimedia, el TTL predeterminado es un año.
> 
> Para obtener más información sobre el funcionamiento de Azure CDN para acelerar el acceso a los blobs y a otros archivos, consulte [Información general de Azure Content Delivery Network](cdn-overview.md).
> 
> Para obtener más información acerca de Azure Blob Storage, consulte [Introducción a Blob Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction).
 

## <a name="setting-cache-control-headers-by-using-azure-powershell"></a>Establecimiento de los encabezados Cache-Control mediante Azure PowerShell
[Azure PowerShell](/powershell/azure/overview) es una de las formas más rápidas y eficaces de administrar los servicios de Azure. Utilice el cmdlet `Get-AzureStorageBlob` para obtener una referencia al blob y, a continuación, establezca la propiedad `.ICloudBlob.Properties.CacheControl`. 

Por ejemplo:

```powershell
# Create a storage context
$context = New-AzureStorageContext -StorageAccountName "<storage account name>" -StorageAccountKey "<storage account key>"

# Get a reference to the blob
$blob = Get-AzureStorageBlob -Context $context -Container "<container name>" -Blob "<blob name>"

# Set the CacheControl property to expire in 1 hour (3600 seconds)
$blob.ICloudBlob.Properties.CacheControl = "max-age=3600"

# Send the update to the cloud
$blob.ICloudBlob.SetProperties()
```

> [!TIP]
> También puede usar PowerShell para [administrar los perfiles y puntos de conexión de la red CDN](cdn-manage-powershell.md).
> 
>

## <a name="setting-cache-control-headers-by-using-net"></a>Establecimiento de los encabezados Cache-Control mediante .NET
Para establecer el encabezado `Cache-Control` de un blob mediante el código .NET, use la [Biblioteca de cliente de Azure Storage para .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md) para configurar la propiedad [CloudBlob.Properties.CacheControl](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.blobproperties.cachecontrol.aspx).

Por ejemplo:

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
        blob.Properties.CacheControl = "max-age=3600";

        // Update the blob's properties in the cloud
        blob.SetProperties();
    }
}
```

> [!TIP]
> Hay más ejemplos de código de .NET en [Azure Blob Storage Samples for .NET](https://azure.microsoft.com/documentation/samples/storage-blob-dotnet-getting-started/) (Ejemplos de Azure Blob Storage para .NET).
> 

## <a name="setting-cache-control-headers-by-using-other-methods"></a>Establecimiento de los encabezados Cache-Control mediante otros métodos

### <a name="azure-storage-explorer"></a>Explorador de Azure Storage
Con el [Explorador de Azure Storage](https://azure.microsoft.com/en-us/features/storage-explorer/), puede ver y modificar los recursos de Blob Storage, incluidas las propiedades como *CacheControl*. 

Para actualizar la propiedad *CacheControl* de un blob con el Explorador de Azure Storage:
   1. Seleccione un blob y después seleccione **Propiedades** en el menú contextual. 
   2. Desplácese hacia abajo hasta la propiedad *CacheControl*.
   3. Escriba un valor y después haga clic en **Guardar**.


![Propiedades del Explorador de Azure Storage](./media/cdn-manage-expiration-of-blob-content/cdn-storage-explorer-properties.png)

### <a name="azure-command-line-interface"></a>Interfaz de la línea de comandos de Azure
Con la [interfaz de la línea de comandos de Azure](https://docs.microsoft.com/cli/azure/overview?view=azure-cli-latest) (CLI), puede administrar recursos de blob de Azure desde la línea de comandos. Para establecer el encabezado de control de caché cuando cargue un blob con la CLI de Azure, establezca la propiedad *cacheControl* mediante el uso del modificador `-p`. En el siguiente ejemplo, se muestra cómo se establece el período de vida en una hora (3600 segundos):
  
```azurecli
azure storage blob upload -c <connectionstring> -p cacheControl="max-age=3600" .\test.txt myContainer test.txt
```

### <a name="azure-storage-services-rest-api"></a>API de REST de servicios de Azure Storage
Puede usar la [API de REST de servicios de Azure Storage](https://msdn.microsoft.com/library/azure/dd179355.aspx) para establecer explícitamente la propiedad *x-ms-blob-cache-control* mediante el uso de las siguientes operaciones en una solicitud:
  
   - [Put Blob](https://msdn.microsoft.com/en-us/library/azure/dd179451.aspx)
   - [Put Block List](https://msdn.microsoft.com/en-us/library/azure/dd179467.aspx)
   - [Set Blob Properties](https://msdn.microsoft.com/library/azure/ee691966.aspx)

## <a name="testing-the-cache-control-header"></a>Prueba del encabezado Cache-Control
La configuración de TTL de los blobs se puede comprobar con facilidad. Con las [herramientas de desarrollo](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/) del explorador, compruebe que el blob incluye el encabezado de respuesta `Cache-Control`. Asimismo, también puede usar una herramienta como [Wget](https://www.gnu.org/software/wget/), [Postman](https://www.getpostman.com/) o [Fiddler](http://www.telerik.com/fiddler) para examinar los encabezados de respuesta.

## <a name="next-steps"></a>Pasos siguientes
* [Obtenga información sobre cómo administrar la expiración del contenido de Servicio en la nube en la red CDN de Azure](cdn-manage-expiration-of-cloud-service-content.md)
* [Obtenga información sobre conceptos del almacenamiento en caché](cdn-how-caching-works.md)

