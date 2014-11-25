<properties urlDisplayName="Queue Service" pageTitle="Uso del servicio de cola (Python) | Microsoft Azure" metaKeywords="Azure Queue Service messaging Python" description="Learn how to use the Azure Queue service to create and delete queues, and insert, get, and delete messages. Samples written in Python." metaCanonical="" services="storage" documentationCenter="Python" title="How to Use the Queue Storage Service from Python" authors="huvalo" solutions="" manager="wpickett" editor="" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="python" ms.topic="article" ms.date="09/19/2014" ms.author="huvalo" />



# Uso del servicio de almacenamiento en cola desde Python
Esta guía le indicará cómo actuar en situaciones habituales usando el
servicio de almacenamiento en cola de Azure. Los ejemplos están escritos usando la API
Python. Entre los escenarios descritos se incluyen la **inserción**, **inspección**,
**obtención** y **eliminación** de los mensajes en cola, así como la **creación y
eliminación de colas**. Para obtener más información acerca de las colas, consulte la sección [Pasos siguientes][Pasos siguientes].

## Tabla de contenido

[¿Qué es el almacenamiento en cola?][¿Qué es el almacenamiento en cola?]   
 [Conceptos][Conceptos]   
 [Creación de una cuenta de almacenamiento de Azure][Creación de una cuenta de almacenamiento de Azure]   
 [Eliminación de una cola][Eliminación de una cola]   
 [Eliminación de un mensaje en una cola][Eliminación de un mensaje en una cola]   
 [Eliminación de siguiente mensaje][Eliminación de siguiente mensaje]   
 [Eliminación de siguiente mensaje de la cola][Eliminación de siguiente mensaje de la cola]   
 [Eliminación de contenido de un mensaje en cola][Eliminación de contenido de un mensaje en cola]   
 [Eliminación de quitar mensajes de la cola][Eliminación de quitar mensajes de la cola]   
 [Eliminación de la longitud de la cola][Eliminación de la longitud de la cola]   
 [Eliminación de una cola][Eliminación de una cola]   
 [Pasos siguientes][Pasos siguientes]

[WACOM.INCLUDE [howto-queue-storage](../includes/howto-queue-storage.md)]

## <a name="create-account"> </a>Creación de una cuenta de almacenamiento de Azure
[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]


**Nota:** Si necesita instalar Python o las bibliotecas de clientes, consulte la [guía de instalación de Python](../python-how-to-install/) (en inglés).

## <a name="create-queue"> </a>Eliminación de una cola

El objetivo **QueueService** le permite trabajar con colas. El siguiente código crea un objeto **QueueService**. Agregue lo siguiente cerca de la parte superior de todo archivo Python en el que desee obtener acceso al almacenamiento de Azure mediante programación:

	from azure.storage import QueueService

El código siguiente crea un objeto **QueueService** utilizando el nombre de la cuenta de almacenamiento y la clave de la cuenta: sustituya "myaccount" y "mykey" por la cuenta real y la clave.

	queue_service = QueueService(account_name='myaccount', account_key='mykey')

	queue_service.create_queue('taskqueue')


## <a name="insert-message"> </a>Eliminación de un mensaje en una cola

Para insertar un mensaje en una cola, utilice el método **put\_message** para
crear un nuevo mensaje y agregarlo a la cola.

	queue_service.put_message('taskqueue', 'Hello World')


## <a name="peek-message"> </a>Eliminación de siguiente mensaje

Puede inspeccionar el mensaje situado en la parte delantera de una cola, sin quitarlo
de la cola, mediante una llamada al método **peek\_messages**. De forma predetermianda,
**peek\_messages** inspecciona un único mensaje.

	messages = queue_service.peek_messages('taskqueue')
	for message in messages:
		print(message.message_text)


## <a name="get-message"> </a>Eliminación de siguiente mensaje de la cola

El código borra un mensaje de una cola en dos pasos. Si llama a
**get_messages**, obtiene, de forma predeterminada, el siguiente mensaje en una cola. Un
mensaje devuelto por **get\_messages** se hace invisible a cualquier otro
código de lectura de mensajes de esta cola. De forma predeterminada, este mensaje permanece
invisible durante 30 segundos. Para terminar de quitar el mensaje de la cola,
debe llamar también a **delete\_message**. Este proceso de extracción de un mensaje que consta de dos pasos
garantiza que si su código no puede procesar un mensaje a causa de
un error de hardware o software, otra instancia de su código puede obtener
el mismo mensaje e intentarlo de nuevo. El código llama a **delete\_message** justo
después de que se procese el mensaje.

	messages = queue_service.get_messages('taskqueue')
	for message in messages:
		print(message.message_text)
		queue_service.delete_message('taskqueue', message.message_id, message.pop_receipt)


## <a name="change-contents"> </a>Eliminación de contenido de un mensaje en cola

Puede cambiar el contenido de un mensaje local en la cola. Si el
mensaje representa una tarea de trabajo, puede utilizar esta característica para actualizar el
estado de la tarea de trabajo. El código siguiente utiliza el método **update\_message**
para actualizar un mensaje.

	messages = queue_service.get_messages('taskqueue')
	for message in messages:
		queue_service.update_message('taskqueue', message.message_id, 'Hello World Again', message.pop_receipt, 0)

## <a name="advanced-get"> </a>Eliminación de quitar mensajes de la cola

Hay dos formas de personalizar la recuperación de mensajes de una cola.
En primer lugar, puede obtener un lote de mensajes (hasta 32). En segundo lugar, puede establecer un
tiempo de espera de la invisibilidad más largo o más corto para que el código disponga de más o menos
tiempo para procesar cada mensaje. El siguiente ejemplo de código utiliza el
método **get\_messages** para obtener 16 mensajes en una llamada. A continuación, se procesa
cada mensaje con un bucle for. También establece el tiempo de espera de la invisibilidad en
cinco minutos para cada mensaje.

	messages = queue_service.get_messages('taskqueue', numofmessages=16, visibilitytimeout=5*60)
	for message in messages:
		print(message.message_text)
		queue_service.delete_message('taskqueue', message.message_id, message.pop_receipt)

## <a name="get-queue-length"> </a>Eliminación de la longitud de la cola

Puede obtener una estimación del número de mensajes existentes en una cola. El
método **get\_queue\_metadata** solicita al servicio de colas que devuelva los metadatos
sobre la cola, y se debe usar **x-ms-approximate-messages-count** como el índice en el diccionario devuelto para buscar el recuento.
El resultado es solo aproximado porque los mensajes se pueden agregar o quitar después
de que el servicio de colas responda a su solicitud.

	queue_metadata = queue_service.get_queue_metadata('taskqueue')
	count = queue_metadata['x-ms-approximate-messages-count']

## <a name="delete-queue"> </a>Eliminación de una cola

Para eliminar una cola y todos los mensajes contenidos en ella, llame
al método **delete\_queue**.

	queue_service.delete_queue('taskqueue')

## <a name="next-steps"> </a>Pasos siguientes

Ahora que está familiarizado con los aspectos básicos del almacenamiento de colas, siga estos vínculos
para obtener más información acerca de cómo realizar tareas de almacenamiento más complejas.

-   Consulte la referencia de MSDN: [Almacenamiento de datos y acceso a los mismos en Azure][Almacenamiento de datos y acceso a los mismos en Azure]
-   Visite el [blog del equipo de almacenamiento de Azure][blog del equipo de almacenamiento de Azure]

  [Pasos siguientes]: #next-steps
  [¿Qué es el almacenamiento en cola?]: #what-is
  [Conceptos]: #concepts
  [Creación de una cuenta de almacenamiento de Azure]: #create-account
  [Eliminación de una cola]: #delete-queue
  [Almacenamiento de datos y acceso a los mismos en Azure]: http://msdn.microsoft.com/es-es/library/windowsazure/gg433040.aspx
