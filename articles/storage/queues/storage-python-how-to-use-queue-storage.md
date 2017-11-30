---
title: Uso de Queue Storage en Python | Microsoft Docs
description: Aprenda a usar el servicio de colas de Azure de Python para crear y eliminar colas e insertar, obtener y eliminar mensajes.
services: storage
documentationcenter: python
author: tamram
manager: timlt
editor: tysonn
ms.assetid: cc0d2da2-379a-4b58-a234-8852b4e3d99d
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 12/08/2016
ms.author: tamram
ms.openlocfilehash: c7976c01436b1c30880bfd4c57cb97f72a4f48b0
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/29/2017
---
# <a name="how-to-use-queue-storage-from-python"></a>Uso del almacenamiento de colas de Python
[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Información general
Esta guía muestra cómo realizar algunas tareas comunes a través del servicio de almacenamiento en cola de Azure. Los ejemplos están escritos en Python y usan el [SDK de Microsoft Azure Storage para Python]. Entre los escenarios descritos se incluyen **insertar**, **ojear**, **obtener** y **eliminar** mensajes de la cola, así como **crear y eliminar colas**. Para obtener más información acerca de las colas, consulte la sección [Pasos siguientes].

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="download-and-install-azure-storage-sdk-for-python"></a>Descarga e instalación del SDK de Azure Storage para Python

El SDK de Azure Storage para Python requiere Python 2.7, 3.3, 3.4, 3.5 o 3.6, y viene en cuatro paquetes diferentes: `azure-storage-blob`, `azure-storage-file`, `azure-storage-table` y `azure-storage-queue`. En este tutorial se usará el paquete `azure-storage-queue`.
 
### <a name="install-via-pypi"></a>Instalación mediante PyPi

Para realizar la instalación mediante el índice de paquetes de Python (PyPI), escriba:

```bash
pip install azure-storage-queue
```


> [!NOTE]
> Si va a actualizar desde el SDK de Azure Storage para la versión de Python 0.36 o anterior, primero deberá desinstalarlo mediante `pip uninstall azure-storage` dado que ya no se publica el SDL de Storage para Python en un único paquete.
> 
> 

Si quiere conocer métodos de instalación alternativos, consulte el [SDK de Azure Storage para Python en Github](https://github.com/Azure/azure-storage-python/).

## <a name="how-to-create-a-queue"></a>Creación de una cola
El objeto **QueueService** permite trabajar con colas. El siguiente código crea un objeto **QueueService** . Agregue lo siguiente cerca de la parte superior de todo archivo Python en el que desee obtener acceso a Azure Storage mediante programación:

```python
from azure.storage.queue import QueueService
```

El código siguiente crea un objeto **QueueService** utilizando el nombre de la cuenta de almacenamiento y la clave de la cuenta. Reemplace 'myaccount' y 'mykey' por la clave y el nombre de cuenta.

```python
queue_service = QueueService(account_name='myaccount', account_key='mykey')

queue_service.create_queue('taskqueue')
```

## <a name="how-to-insert-a-message-into-a-queue"></a>Inserción de un mensaje en una cola
Para insertar un mensaje en una cola, use el método **put\_message** para crear un nuevo mensaje y agregarlo a la cola.

```python
queue_service.put_message('taskqueue', u'Hello World')
```

## <a name="how-to-peek-at-the-next-message"></a>Inspección del siguiente mensaje
Puede inspeccionar el mensaje situado en la parte delantera de una cola, sin quitarlo de la cola, mediante una llamada al método **peek\_messages**. De forma predeterminada, **peek\_messages** inspecciona un único mensaje.

```python
messages = queue_service.peek_messages('taskqueue')
for message in messages:
    print(message.content)
```

## <a name="how-to-dequeue-messages"></a>Cómo quitar mensajes de la cola
El código borra un mensaje de una cola en dos pasos. Si llama a **get\_messages**, obtiene, de forma predeterminada, el siguiente mensaje en una cola. Un mensaje devuelto por **get\_messages** se hace invisible a cualquier otro código de lectura de mensajes de esta cola. De forma predeterminada, este mensaje permanece invisible durante 30 segundos. Para terminar de quitar el mensaje de la cola, también debe llamar a **delete\_message**. Este proceso de extracción de un mensaje que consta de dos pasos garantiza que si su código no puede procesar un mensaje a causa de un error de hardware o software, otra instancia de su código puede obtener el mismo mensaje e intentarlo de nuevo. Su código llama a **delete\_message** justo después de que se haya procesado el mensaje.

```python
messages = queue_service.get_messages('taskqueue')
for message in messages:
    print(message.content)
    queue_service.delete_message('taskqueue', message.id, message.pop_receipt)
```

Hay dos formas de personalizar la recuperación de mensajes de una cola.
En primer lugar, puede obtener un lote de mensajes (hasta 32). En segundo lugar, puede establecer un tiempo de espera de la invisibilidad más largo o más corto para que el código disponga de más o menos tiempo para procesar cada mensaje. El siguiente ejemplo de código utiliza el método **get\_messages** para obtener 16 mensajes en una llamada. A continuación, procesa cada mensaje con un bucle for. También establece el tiempo de espera de la invisibilidad en cinco minutos para cada mensaje.

```python
messages = queue_service.get_messages('taskqueue', num_messages=16, visibility_timeout=5*60)
for message in messages:
    print(message.content)
    queue_service.delete_message('taskqueue', message.id, message.pop_receipt)        
```

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Cambio del contenido de un mensaje en cola
Puede cambiar el contenido de un mensaje local en la cola. Si el mensaje representa una tarea de trabajo, puede usar esta característica para actualizar el estado de la tarea de trabajo. El código siguiente utiliza el método **update\_message** para actualizar un mensaje. El tiempo de espera de visibilidad se establece en 0, lo que significa que el mensaje aparece inmediatamente y se actualiza el contenido.

```python
messages = queue_service.get_messages('taskqueue')
for message in messages:
    queue_service.update_message('taskqueue', message.id, message.pop_receipt, 0, u'Hello World Again')
```

## <a name="how-to-get-the-queue-length"></a>Obtención de la longitud de la cola
Puede obtener una estimación del número de mensajes existentes en una cola. El método **get\_queue\_metadata** solicita a Queue service que devuelva los metadatos sobre la cola y **approximate_message_count**. El resultado solo es aproximado, ya que se pueden agregar o borrar mensajes después de que el servicio de cola haya respondido su solicitud.

```python
metadata = queue_service.get_queue_metadata('taskqueue')
count = metadata.approximate_message_count
```

## <a name="how-to-delete-a-queue"></a>Eliminación de una cola
Para eliminar una cola y todos los mensajes contenidos en ella, llame al método **delete\_queue**.

```python
queue_service.delete_queue('taskqueue')
```

## <a name="next-steps"></a>Pasos siguientes
Ahora que está familiarizado con los aspectos básicos del Almacenamiento en cola, siga estos vínculos para obtener más información.

* [Centro para desarrolladores de Python](/develop/python/)
* [API de REST de servicios de Azure Storage](http://msdn.microsoft.com/library/azure/dd179355)
* [Blog del equipo de Azure Storage]
* [SDK de Microsoft Azure Storage para Python]

[Blog del equipo de Azure Storage]: http://blogs.msdn.com/b/windowsazurestorage/
[SDK de Microsoft Azure Storage para Python]: https://github.com/Azure/azure-storage-python