---
title: Operaciones en Azure Queue Storage con PowerShell | Microsoft Docs
description: Operaciones en Azure Queue Storage con PowerShell
services: storage
documentationcenter: storage
author: robinsh
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 09/14/2017
ms.author: robinsh
ms.openlocfilehash: 36eb6db83bb902b35efb8c9666ab06e0c618d602
ms.sourcegitcommit: 3ab5ea589751d068d3e52db828742ce8ebed4761
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/27/2017
---
# <a name="perform-azure-queue-storage-operations-with-azure-powershell"></a>Operaciones en Azure Queue Storage con Azure PowerShell

El almacenamiento en cola de Azure es un servicio para almacenar grandes cantidades de mensajes a los que puede obtenerse acceso desde cualquier lugar del mundo a través de llamadas autenticadas con HTTP o HTTPS. Para más información, consulte [Introducción a las colas de Azure](storage-queues-introduction.md). En este artículo de ayuda se describen operaciones de Queue Storage habituales. Aprenderá a:

> [!div class="checklist"]
> * Creación de una cola
> * Recuperar una cola
> * Agregar un mensaje
> * Leer un mensaje
> * Eliminar un mensaje 
> * Eliminación de una cola

Este artículo de ayuda requiere la versión 3.6 del módulo de Azure PowerShell o cualquier versión posterior. Ejecute `Get-Module -ListAvailable AzureRM` para encontrar la versión. Si necesita actualizarla, consulte [Instalación del módulo de Azure PowerShell](/powershell/azure/install-azurerm-ps).

No hay ningún cmdlet de PowerShell para el plano de datos de las colas. Para realizar operaciones de plano de datos como agregar un mensaje, leer un mensaje y eliminar un mensaje, debe usar la biblioteca del cliente de Storage de .NET como se expone en PowerShell. Cree un objeto de mensaje y después podrá usar comandos como AddMessage para realizar operaciones en dicho mensaje. En este artículo se explica cómo hacerlo.

## <a name="sign-in-to-azure"></a>Inicio de sesión en Azure

Inicie sesión en la suscripción de Azure con el comando `Login-AzureRmAccount` y siga las instrucciones de la pantalla.

```powershell
Login-AzureRmAccount
```

## <a name="retrieve-list-of-locations"></a>Recuperación de la lista de ubicaciones

Si no sabe qué ubicación desea usar, puede enumerar las ubicaciones disponibles. Cuando se muestre la lista, busque la que desee usar. En este ejercicio se usará **eastus**. Guárdela en la variable **location** para usarla más adelante.

```powershell
Get-AzureRmLocation | select Location 
$location = "eastus"
```

## <a name="create-resource-group"></a>Creación de un grupo de recursos

Cree un grupo de recursos con el comando [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). 

Un grupo de recursos de Azure es un contenedor lógico en el que se implementan y se administran los recursos de Azure. Guarde el nombre del grupo de recursos en una variable para usarlo más adelante. En este ejemplo se crea un grupo de recursos denominado *howtoqueuesrg* en la región *eastus*.

```powershell
$resourceGroup = "howtoqueuesrg"
New-AzureRmResourceGroup -ResourceGroupName $resourceGroup -Location $location
```

## <a name="create-storage-account"></a>Crear cuenta de almacenamiento

Cree una cuenta de almacenamiento genérica estándar con almacenamiento con redundancia local (LRS) mediante [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/New-AzureRmStorageAccount). Obtenga el contexto de la cuenta de almacenamiento que define la cuenta de almacenamiento que se usará. Cuando actúa en una cuenta de almacenamiento, hace referencia al contexto en lugar de proporcionar varias veces las credenciales.

```powershell
$storageAccountName = "howtoqueuestorage"
$storageAccount = New-AzureRmStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageAccountName `
  -Location $location `
  -SkuName Standard_LRS

$ctx = $storageAccount.Context
```

## <a name="create-a-queue"></a>Creación de una cola

En el siguiente ejemplo, primero se establece una conexión a Almacenamiento de Azure mediante el contexto de cuenta de almacenamiento, en el cual se incluyen el nombre de la cuenta y su clave de acceso. A continuación llama al cmdlet [New-AzureStorageQueue](/powershell/module/azure.storage/new-azurestoragequeue) para crear una cola llamada "queuename".

```powershell
$queueName = "howtoqueue"
$queue = New-AzureStorageQueue –Name $queueName -Context $ctx
```

Para obtener información sobre las convenciones de nomenclatura del servicio Cola de Azure, consulte [Nomenclatura de colas y metadatos](http://msdn.microsoft.com/library/azure/dd179349.aspx).

## <a name="retrieve-a-queue"></a>Recuperar una cola

Puede consultar y recuperar una cola específica o una lista de todas las colas de una cuenta de almacenamiento. Los ejemplos siguientes muestran cómo recuperar todas las colas de la cuenta de almacenamiento y una cola específica; ambos comandos usan el cmdlet [Get-AzureStorageQueue](/powershell/module/azure.storage/get-azurestoragequeue).

```powershell
# Retrieve a specific queue
$queue = Get-AzureStorageQueue –Name $queueName –Context $ctx
# Show the properties of the queue
$queue

# Retrieve all queues and show their names
Get-AzureStorageQueue -Context $ctx | select Name
```

## <a name="add-a-message-to-a-queue"></a>un mensaje a una cola

Las operaciones que afectan a los mensajes reales de la cola usan la biblioteca del cliente de Storage de .NET como se expone en PowerShell. Para agregar un mensaje a una cola, cree una instancia del objeto de mensaje, clase [Microsoft.WindowsAzure.Storage.Queue.CloudQueueMessage](http://msdn.microsoft.com/library/azure/jj732474.aspx). A continuación, llame al método [AddMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.addmessage.aspx) . Se puede crear un CloudQueueMessage a partir de una cadena (en formato UTF-8) o de una matriz de bytes.

En el siguiente ejemplo le mostraremos cómo agregar un mensaje a la cola.

```powershell
# Create a new message using a constructor of the CloudQueueMessage class
$queueMessage = New-Object -TypeName Microsoft.WindowsAzure.Storage.Queue.CloudQueueMessage `
  -ArgumentList "This is message 1"
# Add a new message to the queue
$queue.CloudQueue.AddMessage($QueueMessage)

# Add two more messages to the queue 
$queueMessage = New-Object -TypeName Microsoft.WindowsAzure.Storage.Queue.CloudQueueMessage `
  -ArgumentList "This is message 2"
$queue.CloudQueue.AddMessage($QueueMessage)
$queueMessage = New-Object -TypeName Microsoft.WindowsAzure.Storage.Queue.CloudQueueMessage `
  -ArgumentList "This is message 3"
$queue.CloudQueue.AddMessage($QueueMessage)
```

Si usa el [Explorador de Azure Storage](http://storageexplorer.com), puede conectarse a su cuenta de Azure, ver las colas de la cuenta de almacenamiento y explorar una de ellas para ver los mensajes de la cola. 

## <a name="read-a-message-from-the-queue-then-delete-it"></a>Lectura y eliminación de un mensaje de la cola

Los mensajes se leen, en lo posible, siguiendo un orden de tipo primero en entrar, primero en salir, si bien no se ofrece ninguna garantía al respecto. Al leer el mensaje de la cola, se hace invisible para todos los demás procesos que observan la cola. De este modo, se garantiza que si su código no puede procesar el mensaje a causa de un error de hardware o software, otra instancia de su código pueda obtener el mismo mensaje e intentarlo de nuevo.  

Este **tiempo de expiración de invisibilidad** define cuánto tiempo permanece invisible el mensaje antes de volver a estar disponible para procesarse. El valor predeterminado es 30 segundos. 

El código lee un mensaje de la cola en dos pasos. Cuando llama al método [Microsoft.WindowsAzure.Storage.Queue.CloudQueue.GetMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.getmessage.aspx), verá el siguiente mensaje en la cola. Un mensaje devuelto por **GetMessage** se hace invisible a cualquier otro código de lectura de mensajes de esta cola. Para terminar de eliminar el mensaje de la cola, debe llamar al método [Microsoft.WindowsAzure.Storage.Queue.CloudQueue.DeleteMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.deletemessage.aspx). 

En el ejemplo siguiente, leerá los tres mensajes de la cola y, después, esperará diez segundos (el tiempo de expiración de invisibilidad). A continuación, leerá los tres mensajes de nuevo, eliminando los mensajes tras leerlos mediante una llamada a **DeleteMessage**. Si intenta leer la cola después de que se eliminen los mensajes, se devolverá $queueMessage como NULL.

```powershell
# Set the amount of time you want to entry to be invisible after read from the queue
# If it is not deleted by the end of this time, it will show up in the queue again
$invisibleTimeout = [System.TimeSpan]::FromSeconds(10)

# Read the message from the queue, then show the contents of the message. Read the other two messages, too.
$ queueMessage = $queue.CloudQueue.GetMessage($invisibleTimeout)
$ queueMessage 
$ queueMessage = $queue.CloudQueue.GetMessage($invisibleTimeout)
$ queueMessage 
$ queueMessage = $queue.CloudQueue.GetMessage($invisibleTimeout)
$ queueMessage 

# After 10 seconds, these messages reappear on the queue. 
# Read them again, but delete each one after reading it.
# Delete the message.
$ queueMessage = $queue.CloudQueue.GetMessage($invisibleTimeout)
$ queue.CloudQueue.DeleteMessage($queueMessage)
$ queueMessage = $queue.CloudQueue.GetMessage($invisibleTimeout)
$ queue.CloudQueue.DeleteMessage($queueMessage)
$ queueMessage = $queue.CloudQueue.GetMessage($invisibleTimeout)
$ queue.CloudQueue.DeleteMessage($queueMessage)
```

## <a name="delete-a-queue"></a>Eliminación de una cola
Para eliminar una cola y todos los mensajes que contenga, llame al cmdlet Remove-AzureStorageQueue. En el siguiente ejemplo se muestra cómo eliminar la cola específica utilizada en este ejercicio mediante el cmdlet Remove-AzureStorageQueue.

```powershell
# Delete the queue 
Remove-AzureStorageQueue –Name $queueName –Context $ctx
```

## <a name="clean-up-resources"></a>Limpieza de recursos

Para quitar todos los activos que ha creado en este ejercicio, quite el grupo de recursos. Esto también elimina todos los recursos contenidos en el grupo. En este caso, se quita la cuenta de almacenamiento creada y el propio grupo de recursos.

```powershell
Remove-AzureRmResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Pasos siguientes

En este artículo de ayuda, ha aprendido conceptos básicos sobre la administración de Queue Storage con PowerShell. Por ejemplo:

> [!div class="checklist"]
> * Creación de una cola
> * Recuperar una cola
> * Agregar un mensaje
> * Leer el siguiente mensaje
> * Eliminar un mensaje 
> * Eliminación de una cola

### <a name="microsoft-azure-powershell-storage-cmdlets"></a>Cmdlets de almacenamiento de Microsoft Azure PowerShell
* [Cmdlets de PowerShell de almacenamiento](/powershell/module/azurerm.storage#storage)

### <a name="microsoft-azure-storage-explorer"></a>Explorador de almacenamiento de Microsoft Azure
* El [Explorador de Microsoft Azure Storage](../../vs-azure-tools-storage-manage-with-storage-explorer.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) es una aplicación independiente y gratuita de Microsoft que permite trabajar visualmente con los datos de Azure Storage en Windows, macOS y Linux.