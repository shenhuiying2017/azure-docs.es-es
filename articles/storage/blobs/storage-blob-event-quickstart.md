---
title: "Enrutamiento de eventos de Azure Blob Storage a un punto de conexión web personalizado (versión preliminar) | Microsoft Docs"
description: Utilice Azure Event Grid para suscribirse a los eventos de Blob Storage.
services: storage,event-grid
keywords: 
author: cbrooksmsft
ms.author: cbrooks
ms.date: 08/18/2017
ms.topic: hero-article
ms.service: storage
ms.translationtype: HT
ms.sourcegitcommit: a0b98d400db31e9bb85611b3029616cc7b2b4b3f
ms.openlocfilehash: a68d5c4ee8ad69cd888765a96566a7ca6c13cff3
ms.contentlocale: es-es
ms.lasthandoff: 08/29/2017

---

# <a name="route-blob-storage-events-to-a-custom-web-endpoint-preview"></a>Enrutamiento de eventos de Blob Storage a un punto de conexión web personalizado (versión preliminar)

Azure Event Grid es un servicio de eventos para la nube. En este artículo, se usa la CLI de Azure para suscribirse a los eventos de Blob Storage y desencadenar el evento para ver el resultado. 

> [!IMPORTANT]
> Para completar este tutorial, debe estar registrado para la versión preliminar de eventos de Blob Storage.  Para más información acerca del programa de versión preliminar, haga clic [aquí](storage-blob-event-overview.md#join-the-preview).

Por lo general, los eventos se envían a un punto de conexión que responde ante ellos, como un webhook o Azure Function. Para simplificar el ejemplo de este artículo, enviamos los eventos a una dirección URL que simplemente recopila los mensajes. Esta dirección URL se crea mediante una herramienta de código abierto de terceros llamada [RequestBin](https://requestb.in/).

> [!NOTE]
> **RequestBin** es una herramienta de código abierto que no está pensada para una utilización de alto rendimiento. El uso de la herramienta aquí es meramente ilustrativo. Si inserta más de un evento a la vez, puede que no vea todos los eventos en la herramienta.

Al completar los pasos descritos en este artículo, verá que los datos del evento se han enviado a un punto de conexión.

![Datos de evento](./media/storage-blob-event-quickstart/request-result.png)

[!INCLUDE [quickstarts-free-trial-note.md](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Si decide instalar y usar la CLI de Azure localmente, para los fines de este artículo es preciso que ejecute la versión más reciente (2.0.14 o posterior). Para encontrar la versión, ejecute `az --version`. Si necesita instalarla o actualizarla, consulte [Instalación de la CLI de Azure 2.0](/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Crear un grupo de recursos

Los temas de Event Grid son recursos de Azure y se deben colocar en un grupo de recursos de Azure. El grupo de recursos de Azure es una colección lógica en la que se implementan y administran los recursos de Azure.

Cree un grupo de recursos con el comando [az group create](/cli/azure/group#create). 

En el ejemplo siguiente, se crea un grupo de recursos denominado `<resource_group_name>` en la ubicación *westcentralus*.  Reemplace `<resource_group_name>` por un nombre único para grupo de recursos.

```azurecli-interactive
az group create --name <resource_group_name> --location westcentralus
```

## <a name="create-a-blob-storage-account"></a>Creación de una cuenta de Blob Storage

Para usar Azure Storage, necesita una cuenta de almacenamiento.  Actualmente, los eventos de Blob Storage solo están disponibles en las cuentas de Blob Storage.

Una cuenta de Almacenamiento de blobs es una cuenta de almacenamiento especializado para almacenar los datos no estructurados como blobs (objetos) en Almacenamiento de Azure. Las cuentas de Almacenamiento de blobs son similares a las cuentas de almacenamiento de uso general existentes y comparten las excelentes características de rendimiento, escalabilidad, disponibilidad y durabilidad que se usan en la actualidad, incluida la coherencia total de la API para blobs en bloques y blobs en anexos. Para las aplicaciones que requieren solo Almacenamiento de blobs en bloque o en anexos, se recomienda utilizar cuentas de Almacenamiento de blobs.

> [!NOTE]
> Para el lanzamiento de la versión preliminar, los eventos de Blob Storage solo están disponibles para las cuentas de almacenamiento de la ubicación **westcentralus**.

Reemplace `<storage_account_name>` por el nombre único de la cuenta de almacenamiento y `<resource_group_name>` por el grupo de recursos que creó anteriormente.

```azurecli-interactive
az storage account create \
  --name <storage_account_name> \
  --location westcentralus \
  --resource-group <resource_group_name> \
  --sku Standard_LRS \
  --kind BlobStorage \
  --access-tier Hot
```

## <a name="create-a-message-endpoint"></a>Creación de un punto de conexión de mensaje

Antes de suscribirse a los eventos desde la cuenta de Blob Storage, vamos a crear el punto de conexión para el mensaje del evento. En lugar de escribir código para responder al evento, crearemos un punto de conexión que recopile los mensajes para que pueda verlos. RequestBin es una herramienta de código abierto de terceros que le permite crear un punto de conexión y ver las solicitudes que se envían a él. Vaya a [RequestBin](https://requestb.in/) y haga clic en **Create a RequestBin** (Crear RequestBin).  Copie la dirección URL de la ubicación, la necesitará para suscribirse al tema.

## <a name="subscribe-to-your-blob-storage-account"></a>Suscripción a una cuenta de Blob Storage

Suscríbase a un tema para indicar a Event Grid los eventos cuyo seguimiento desea realizar. En el ejemplo siguiente se realiza la suscripción a la cuenta de Blob Storage que ha creado y se pasa la dirección URL de RequestBin como punto de conexión para la notificación de eventos. Reemplace `<event_subscription_name>` por un nombre único para la suscripción de eventos y `<URL_from_RequestBin>` por el valor de la sección anterior. Al especificar un punto de conexión cuando se suscribe, Event Grid controla el enrutamiento de los eventos a ese punto de conexión. En `<resource_group_name>` y `<storage_account_name>`, use los valores que creó anteriormente. 

```azurecli-interactive
az eventgrid resource event-subscription create \
--endpoint <URL_from_RequestBin> \
--name <event_subscription_name> \
--provider-namespace Microsoft.Storage \
--resource-type storageAccounts \
--resource-group <resource_group_name> \
--resource-name <storage_account_name>
```

## <a name="trigger-an-event-from-blob-storage"></a>Desencadenamiento de un evento desde Blob Storage

Ahora, vamos a desencadenar un evento para ver cómo Event Grid distribuye el mensaje al punto de conexión. En primer lugar, vamos a configurar el nombre y la clave de la cuenta de almacenamiento, luego, crearemos un contenedor y, después, crearemos y actualizaremos un archivo. De nuevo, utilice los valores `<storage_account_name>` y `<resource_group_name>` que creó anteriormente.

```azurecli-interactive
export AZURE_STORAGE_ACCOUNT=<storage_account_name>
export AZURE_STORAGE_ACCESS_KEY="$(az storage account keys list --account-name <storage_account_name> --resource-group <resource_group_name> --query "[0].value" --output tsv)"

az storage container create --name testcontainer

touch testfile.txt
az storage blob upload --file testfile.txt --container-name testcontainer --name testfile.txt
```

Ha desencadenado el evento y Event Grid envió el mensaje al punto de conexión configurado durante la suscripción. Vaya a la dirección URL de RequestBin que creó anteriormente. O bien, haga clic en Actualizar en el explorador de RequestBin abierto. Verá el evento que se acaba de enviar. 

```json
[{
  "topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myrg/providers/Microsoft.Storage/storageAccounts/myblobstorageaccount",
  "subject": "/blobServices/default/containers/testcontainer/blobs/testfile.txt",
  "eventType": "Microsoft.Storage.BlobCreated",
  "eventTime": "2017-08-16T20:33:51.0595757Z",
  "id": "4d96b1d4-0001-00b3-58ce-16568c064fab",
  "data": {
    "api": "PutBlockList",
    "clientRequestId": "d65ca2e2-a168-4155-b7a4-2c925c18902f",
    "requestId": "4d96b1d4-0001-00b3-58ce-16568c000000",
    "eTag": "0x8D4E4E61AE038AD",
    "contentType": "text/plain",
    "contentLength": 0,
    "blobType": "BlockBlob",
    "url": "https://myblobstorageaccount.blob.core.windows.net/testcontainer/testblob1.txt",
    "sequencer": "00000000000000EB0000000000046199",
    "storageDiagnostics": {
      "batchId": "dffea416-b46e-4613-ac19-0371c0c5e352"
    }
  }
}]

```

## <a name="clean-up-resources"></a>Limpieza de recursos
Si planea seguir trabajando con esta cuenta de almacenamiento y suscripción de eventos, no elimine los recursos creados en este artículo. Si no va a continuar, use el siguiente comando para eliminar los recursos creados en este artículo.

Sustituya `<resource_group_name>` por el nombre del grupo de recursos que ha creado.

```azurecli-interactive
az group delete --name <resource_group_name>
```

## <a name="next-steps"></a>Pasos siguientes

Ahora que sabe cómo crear suscripciones a temas y eventos, obtenga más información acerca de los eventos de Blob Storage y lo que Event Grid puede ayudarle a hacer:

- [Reacción a eventos de Blob Storage](storage-blob-event-overview.md)
- [About Event Grid](../../event-grid/overview.md) (Acerca de Event Grid)

