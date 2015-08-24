<properties 
	pageTitle="Uso del almacenamiento de colas de Python | Microsoft Azure" 
	description="Aprenda a usar el servicio de colas de Azure de Python para crear y eliminar colas e insertar, obtener y eliminar mensajes." 
	services="storage" 
	documentationCenter="python" 
	authors="huguesv" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="python" 
	ms.topic="article" 
	ms.date="03/11/2015" 
	ms.author="huvalo"/>

# Uso del almacenamiento de colas de Python

[AZURE.INCLUDE [storage-selector-queue-include](../../includes/storage-selector-queue-include.md)]

## Información general

Esta guía muestra cómo realizar algunas tareas comunes a través del servicio de almacenamiento en cola de Azure. Los ejemplos están escritos en Python y usan el [paquete de Azure para Python][]. Entre los escenarios descritos se incluyen **insertar**, **ojear**, **obtener** y **eliminar** mensajes de la cola, así como **crear y eliminar colas**. Para obtener más información acerca de las colas, consulte la sección [Pasos siguientes][].

[AZURE.INCLUDE [storage-queue-concepts-include](../../includes/storage-queue-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]


> [AZURE.NOTE]Si necesita instalar Python o el [paquete de Azure para Python][], consulte la [Guía de instalación de Python](../python-how-to-install.md).

## Creación de una cola

El objeto **QueueService** permite trabajar con colas. El siguiente código crea un objeto **QueueService**. Agregue lo siguiente cerca de la parte superior de todo archivo Python en el que desee obtener acceso al almacenamiento de Azure mediante programación:

	from azure.storage import QueueService

El código siguiente crea un objeto **QueueService** utilizando el nombre de la cuenta de almacenamiento y la clave de la cuenta. sustituya "myaccount" y "mykey" por la cuenta real y la clave.

	queue_service = QueueService(account_name='myaccount', account_key='mykey')

	queue_service.create_queue('taskqueue')


## Inserción de un mensaje en una cola

Para insertar un mensaje en una cola, use el método **put\_message** para crear un nuevo mensaje y agregarlo a la cola.

	queue_service.put_message('taskqueue', 'Hello World')


## Inspección del siguiente mensaje

Puede ojear el mensaje situado en la parte delantera de una cola, sin quitarlo de la cola, llamando al método **peek\_messages**. De forma predeterminada, **peek\_messages** ojea un único mensaje.

	messages = queue_service.peek_messages('taskqueue')
	for message in messages:
		print(message.message_text)


## Extracción del siguiente mensaje de la cola

El código borra un mensaje de una cola en dos pasos. Si llama a **get\_messages**, obtiene el siguiente mensaje en una cola de forma predeterminada. Un mensaje devuelto por **get\_messages** se hace invisible a cualquier otro código que lea mensajes de esta cola. De forma predeterminada, este mensaje permanece invisible durante 30 segundos. Para terminar quitando el mensaje de la cola, también debe llamar a **delete\_message**. Este proceso de extracción de un mensaje que consta de dos pasos garantiza que si su código no puede procesar un mensaje a causa de un error de hardware o software, otra instancia de su código puede obtener el mismo mensaje e intentarlo de nuevo. El código llama a **delete\_message** justo después de que se haya procesado el mensaje.

	messages = queue_service.get_messages('taskqueue')
	for message in messages:
		print(message.message_text)
		queue_service.delete_message('taskqueue', message.message_id, message.pop_receipt)


## Cambio del contenido de un mensaje en cola

Puede cambiar el contenido de un mensaje local en la cola. Si el mensaje representa una tarea de trabajo, puede usar esta característica para actualizar el estado de la tarea de trabajo. El código siguiente usa el método **update\_message** para actualizar un mensaje.

	messages = queue_service.get_messages('taskqueue')
	for message in messages:
		queue_service.update_message('taskqueue', message.message_id, 'Hello World Again', message.pop_receipt, 0)

## Opciones adicionales para extraer mensajes de la cola

Hay dos formas de personalizar la recuperación de mensajes de una cola. En primer lugar, puede obtener un lote de mensajes (hasta 32). En segundo lugar, puede establecer un tiempo de espera de la invisibilidad más largo o más corto para que el código disponga de más o menos tiempo para procesar cada mensaje. El siguiente ejemplo de código utiliza el método **get\_messages** para obtener 16 mensajes en una llamada. A continuación, procesa cada mensaje con un bucle for. También establece el tiempo de espera de la invisibilidad en cinco minutos para cada mensaje.

	messages = queue_service.get_messages('taskqueue', numofmessages=16, visibilitytimeout=5*60)
	for message in messages:
		print(message.message_text)
		queue_service.delete_message('taskqueue', message.message_id, message.pop_receipt)

## Obtención de la longitud de la cola

Puede obtener una estimación del número de mensajes existentes en una cola. El método **get\_queue\_metadata** pide al servicio de cola que devuelva metadatos acerca de la cola y se debe usar**x-ms-approximate-messages-count** como el índice en el diccionario devuelto para buscar el recuento. El resultado solo es aproximado, ya que se pueden agregar o borrar mensajes después de que el servicio de cola haya respondido su solicitud.

	queue_metadata = queue_service.get_queue_metadata('taskqueue')
	count = queue_metadata['x-ms-approximate-messages-count']

## Eliminación de una cola

Para eliminar una cola y todos los mensajes contenidos en ella, llame al método **delete\_queue**.

	queue_service.delete_queue('taskqueue')

## Pasos siguientes

Ahora que está familiarizado con los aspectos básicos del almacenamiento de colas, utilice estos vínculos para obtener más información acerca de tareas de almacenamiento más complejas.

-   Vea la referencia de MSDN: [Almacenamiento de datos y acceso a los mismos en Azure][]
-   Visite el [Blog del equipo de almacenamiento de Azure][]

[Almacenamiento de datos y acceso a los mismos en Azure]: http://msdn.microsoft.com/library/azure/gg433040.aspx
[Blog del equipo de almacenamiento de Azure]: http://blogs.msdn.com/b/windowsazurestorage/
[paquete de Azure para Python]: https://pypi.python.org/pypi/azure
 

<!---HONumber=August15_HO7-->