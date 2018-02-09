---
title: "Enrutamiento de eventos de Azure Blob Storage a un punto de conexión web personalizado - Powershell | Microsoft Docs"
description: Utilice Azure Event Grid para suscribirse a los eventos de Blob Storage.
services: storage,event-grid
keywords: 
author: david-stanford
ms.author: dastanfo
ms.date: 01/30/2018
ms.topic: article
ms.service: storage
ms.openlocfilehash: 329a79511b810159244b5530a49a5916440d2046
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/01/2018
---
# <a name="route-blob-storage-events-to-a-custom-web-endpoint-with-powershell"></a>Enrutamiento de eventos de Blob Storage a un punto de conexión web personalizado con PowerShell

Azure Event Grid es un servicio de eventos para la nube. En este artículo, aprenderá a usar Azure PowerShell para suscribirse a los eventos de Blob Storage, desencadenar un evento y ver el resultado. 

Por lo general, los eventos se envían a un punto de conexión que responde ante ellos, como un webhook o Azure Function. Para simplificar el ejemplo de este artículo, los eventos se envían a una dirección URL que, simplemente, recopila los mensajes. Debe crear esta dirección URL mediante herramientas de terceros desde [RequestBin](https://requestb.in/) o [Hookbin](https://hookbin.com/).

> [!NOTE]
> Tenga en cuenta que **RequestBin** y **Hookbin** no están diseñados para el uso de alto rendimiento. El uso de estas herramientas es meramente ilustrativo. Si inserta más de un evento a la vez, puede que no vea todos los eventos en la herramienta.

Al completar los pasos descritos en este artículo, verá que los datos del evento se han enviado a un punto de conexión.

![Datos de evento](./media/storage-blob-event-quickstart/request-result.png)

## <a name="setup"></a>Configuración

Para este artículo es necesario ejecutar la versión más reciente de Azure PowerShell. Si necesita instalarlas o actualizarlas, vea [Install and configure Azure PowerShell](/powershell/azure/install-azurerm-ps) (Instalación y configuración de Azure PowerShell).

## <a name="log-in-to-azure"></a>Inicie sesión en Azure.

Inicie sesión en la suscripción de Azure con el comando `Login-AzureRmAccount` y siga las instrucciones de la pantalla para autenticarse.

```powershell
Login-AzureRmAccount
```

> [!NOTE]
> La disponibilidad de los eventos de almacenamiento está asociada a la [disponibilidad](../../event-grid/overview.md) de Event Grid. Estarán disponibles en otras regiones cuando lo esté Event Grid.

Este ejemplo utiliza **westus2** y almacena la selección en una variable que se puede usar en todo momento.

```powershell
$location = "westus2"
```

## <a name="create-a-resource-group"></a>Crear un grupo de recursos

Los temas de Event Grid son recursos de Azure y se deben colocar en un grupo de recursos de Azure. El grupo de recursos de Azure es una colección lógica en la que se implementan y administran los recursos de Azure.

Cree un grupo de recursos con el comando [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup).

En el ejemplo siguiente, se crea un grupo de recursos denominado **gridResourceGroup** en la ubicación **westus2**.  

```powershell
$resourceGroup = "gridResourceGroup"
New-AzureRmResourceGroup -Name $resourceGroup -Location $location
```

## <a name="create-a-storage-account"></a>Crear una cuenta de almacenamiento

Para utilizar eventos de Blob Storage, necesita una [cuenta de Blob Storage](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#blob-storage-accounts) o una [cuenta de almacenamiento de uso general v2](../common/storage-account-options.md#general-purpose-v2). Las cuentas de **uso general v2 (GPv2)** son cuentas de almacenamiento que admiten todas las características de todos los servicios de almacenamiento como, por ejemplo, Blobs, Files, Queues y Tables. Una **cuenta de Blob Storage** es una cuenta de almacenamiento especializada para almacenar los datos no estructurados como blobs (objetos) en Azure Storage. Las cuentas de Blob Storage son similares a las cuentas de almacenamiento de uso general y comparten las excelentes características de rendimiento, escalabilidad, disponibilidad y durabilidad que se usan en la actualidad, incluida la coherencia total de la API con blobs en bloques y blobs en anexos. Para las aplicaciones que requieren solo Almacenamiento de blobs en bloque o en anexos, se recomienda utilizar cuentas de Almacenamiento de blobs.  

Cree una cuenta de Blob Storage con la replicación de LRS mediante [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/New-AzureRmStorageAccount). A continuación, recupere el contexto de la cuenta de almacenamiento que define la cuenta de almacenamiento que se usará. Cuando actúa en una cuenta de almacenamiento, hace referencia al contexto en lugar de proporcionar varias veces las credenciales. En este ejemplo, se crea una cuenta de almacenamiento denominada **gridstorage** con almacenamiento con redundancia local (LRS) y cifrado de blob (habilitados de forma predeterminada). 

> [!NOTE]
> Los nombres de cuenta de almacenamiento están en un espacio de nombres global, por lo que necesita anexar algunos caracteres aleatorios al nombre que se proporciona en este script.

```powershell
$storageName = "gridstorage"
$storageAccount = New-AzureRmStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageName `
  -Location $location `
  -SkuName Standard_LRS `
  -Kind BlobStorage `
  -AccessTier Hot

$ctx = $storageAccount.Context
```

## <a name="create-a-message-endpoint"></a>Creación de un punto de conexión de mensaje

Antes de suscribirse al tema, vamos a crear el punto de conexión para el mensaje de evento. En lugar de escribir código para responder al evento, vamos a crear un punto de conexión que recopile los mensajes para que pueda verlos. RequestBin y Hookbin son herramientas de terceros que permiten crear un punto de conexión y ver las solicitudes enviadas a este. Vaya a [RequestBin](https://requestb.in/) y haga clic en **Create a RequestBin** (Crear un elemento de RequestBin), o vaya a [Hookbin](https://hookbin.com/) y haga clic en **Create New Endpoint** (Crear un nuevo punto de conexión). Copie la dirección URL de la papelera y reemplace `<bin URL>` en el siguiente script.

```powershell
$binEndPoint = "<bin URL>"
```

## <a name="subscribe-to-your-storage-account"></a>Suscríbase a una cuenta de almacenamiento

Suscríbase a un tema para indicar a Event Grid los eventos cuyo seguimiento desea realizar. En el ejemplo siguiente se realiza la suscripción a la cuenta de almacenamiento que ha creado y se pasa la dirección URL de RequestBin o Hookbin como punto de conexión para la notificación de eventos. 

```powershell
$storageId = (Get-AzureRmStorageAccount -ResourceGroupName $resourceGroup -AccountName $storageName).Id
New-AzureRmEventGridSubscription `
  -EventSubscriptionName gridBlobQuickStart `
  -Endpoint $binEndPoint `
  -ResourceId $storageId
```

## <a name="trigger-an-event-from-blob-storage"></a>Desencadenamiento de un evento desde Blob Storage

Ahora, vamos a desencadenar un evento para ver cómo Event Grid distribuye el mensaje al punto de conexión. En primer lugar, vamos a crear un contenedor y un objeto. A continuación, cargaremos el objeto en el contenedor.

```powershell
$containerName = "gridcontainer"
New-AzureStorageContainer -Name $containerName -Context $ctx

echo $null >> gridTestFile.txt

Set-AzureStorageBlobContent -File gridTestFile.txt -Container $containerName -Context $ctx -Blob gridTestFile.txt
```

Ha desencadenado el evento y Event Grid envió el mensaje al punto de conexión configurado durante la suscripción. Vaya a la dirección URL del punto de conexión que creó anteriormente. O bien, haga clic en Actualizar en el explorador abierto. Verá el evento que se acaba de enviar. 

```json
[{
  "topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myrg/providers/Microsoft.Storage/storageAccounts/myblobstorageaccount",
  "subject": "/blobServices/default/containers/gridcontainer/blobs/gridTestFile.txt",
  "eventType": "Microsoft.Storage.BlobCreated",
  "eventTime": "2017-08-16T20:33:51.0595757Z",
  "id": "4d96b1d4-0001-00b3-58ce-16568c064fab",
  "data": {
    "api": "PutBlockList",
    "clientRequestId": "Azure-Storage-PowerShell-d65ca2e2-a168-4155-b7a4-2c925c18902f",
    "requestId": "4d96b1d4-0001-00b3-58ce-16568c000000",
    "eTag": "0x8D4E4E61AE038AD",
    "contentType": "application/octet-stream",
    "contentLength": 0,
    "blobType": "BlockBlob",
    "url": "https://myblobstorageaccount.blob.core.windows.net/gridcontainer/gridTestFile.txt",
    "sequencer": "00000000000000EB0000000000046199",
    "storageDiagnostics": {
      "batchId": "dffea416-b46e-4613-ac19-0371c0c5e352"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]

```

## <a name="clean-up-resources"></a>Limpieza de recursos
Si planea seguir trabajando con esta cuenta de almacenamiento y suscripción de eventos, no elimine los recursos creados en este artículo. Si no va a continuar, use el siguiente comando para eliminar los recursos creados en este artículo.

```powershell
Remove-AzureRmResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>pasos siguientes

Ahora que sabe cómo crear suscripciones a temas y eventos, obtenga más información acerca de los eventos de Blob Storage y lo que Event Grid puede ayudarle a hacer:

- [Reacción a eventos de Blob Storage](storage-blob-event-overview.md)
- [About Event Grid](../../event-grid/overview.md) (Acerca de Event Grid)
