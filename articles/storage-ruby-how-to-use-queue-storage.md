<properties urlDisplayName="Queue Service" pageTitle="Uso del servicio de cola (Ruby) | Microsoft Azure" metaKeywords="Azure Queue Service get messages Ruby" description="Learn how to use the Azure Queue service to create and delete queues, and insert, get, and delete messages. Samples written in Ruby." metaCanonical="" services="storage" documentationCenter="Ruby" title="How to Use the Queue Storage Service from Ruby" authors="tomfitz" solutions="" manager="wpickett" editor="" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="ruby" ms.topic="article" ms.date="11/24/2014" ms.author="tomfitz" />





# Uso del servicio de almacenamiento en cola desde Ruby

Esta guía le indicará cómo actuar en situaciones habituales usando el servicio de almacenamiento en cola de Azure. Los ejemplos están escritos usando la API Ruby de Azure.
Entre los escenarios descritos se incluyen la **inserción**, **inspección**, **obtención** y **eliminación** de mensajes de la cola, así como la **creación y eliminación de colas**. Para obtener más información acerca de las colas, consulte la sección [Pasos
siguientes](#next-steps) .

## Tabla de contenido

* [¿Qué es el almacenamiento en cola?](#what-is)
* [Conceptos](#concepts)
* [Creación de una cuenta de almacenamiento de Azure](#CreateAccount)
* [Creación de una aplicación de Ruby](#create-a-ruby-application)
* [Configuración de su aplicación para obtener acceso al almacenamiento](#configure-your-application-to-access-storage)
* [Configuración de una conexión de almacenamiento de Azure](#setup-a-windows-azure-storage-connection)
* [Cambio de una cola](#how-to-create-a-queue)
* [Cambio un mensaje en una cola](#how-to-insert-a-message-into-a-queue)
* [Cambio del siguiente mensaje](#how-to-peek-at-the-next-message)
* [Cambio siguiente mensaje de la cola](#how-to-dequeue-the-next-message)
* [Cambio contenido de un mensaje en cola](#how-to-change-the-contents-of-a-queued-message)
* [Cambio para quitar mensajes de la cola](#how-to-additional-options-for-dequeuing-messages)
* [Cambio la longitud de la cola](#how-to-get-the-queue-length)
* [Cambio una cola](#how-to-delete-a-queue)
* [Pasos siguientes](#next-steps)

[WACOM.INCLUDE [howto-queue-storage](../includes/howto-queue-storage.md)]

## <a id="CreateAccount"></a>Crear una cuenta de almacenamiento de Azure

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

## <a id="create-a-ruby-application"></a>Creación de una aplicación de Ruby

Cree una aplicación de Ruby. Para obtener instrucciones, consulte [Creación de una aplicación de Ruby en Azure](/en-us/develop/ruby/tutorials/web-app-with-linux-vm/).

## <a id="configure-your-application-to-access-storage"></a>Configuración de su aplicación para obtener acceso al almacenamiento

Para usar el almacenamiento de Azure tendrá que descargar y usar el paquete Ruby azure, que incluye un conjunto de útiles bibliotecas que se comunican con los servicios REST de almacenamiento.

### Uso de RubyGems para obtener el paquete

1. Use una interfaz de línea de comandos como **PowerShell** (Windows), **Terminal** (Mac) o **Bash** (Unix).

2. Escriba "gem install azure" en la ventana de comandos para instalar la gema y las dependencias.

### Importación del paquete

Con el editor de texto que prefiera, agregue lo siguiente al principio del archivo de Ruby en el que pretenda utilizar el almacenamiento:

	require "azure"

## <a id="setup-a-windows-azure-storage-connection"></a>Configuración de una conexión de almacenamiento de Azure

El módulo de Azure leerá las variables de entorno **AZURE\_STORAGE\_ACCOUNT** y **AZURE\_STORAGE\_ACCESS_KEY** 
para obtener la información necesaria para conectarse con la cuenta de almacenamiento de Azure. Si estas variables de entorno no están establecidas, 
debe especificar la información de la cuenta antes de usar **Azure::QueueService** con el código siguiente:

	Azure.config.storage_account_name = "<your azure storage account>"
	Azure.config.storage_access_key = "<your Azure storage access key>"

Para obtener estos valores:

1. Inicie sesión en el [Portal de administración de Azure](https://manage.windowsazure.com/).
2. Vaya a la cuenta de almacenamiento que desea utilizar.
3. Haga clic en **ADMINISTRAR CLAVES** en la parte inferior del panel de navegación.
4. En el cuadro de diálogo emergente, verá el nombre de cuenta de almacenamiento, la clave de acceso principal y la clave de acceso secundaria. Para la clave de acceso, puede elegir la principal o la secundaria.

## <a id="how-to-create-a-queue"></a>Deshabilitación o habilitación Creación de una cola

El siguiente código crea un objeto **Azure::QueueService**, que le permite trabajar con colas.

	azure_queue_service = Azure::QueueService.new

Utilice el método **create_queue()** para crear una cola con el nombre indicado.

	begin
	  azure_queue_service.create_queue("test-queue")
	rescue
	  puts $!
	end

## <a id="how-to-insert-a-message-into-a-queue"></a>Deshabilitación o habilitación un mensaje en una cola

Para insertar un mensaje en una cola, utilice el método **create_message()** para crear un nuevo mensaje y agregarlo a la cola.

	azure_queue_service.create_message("test-queue", "test message")

## <a id="how-to-peek-at-the-next-message"></a>Deshabilitación o habilitación siguiente mensaje

Puede inspeccionar el mensaje situado en la parte delantera de una cola, sin quitarlo de la cola, mediante una llamada al método **peek\_messages()**. De forma predeterminada,  **peek\_messages()** inspecciona un único mensaje. También puede indicar cuántos mensajes desea inspeccionar.

	result = azure_queue_service.peek_messages("test-queue",
	  {:number_of_messages => 10})

## <a id="how-to-dequeue-the-next-message"></a>Deshabilitación o habilitación siguiente mensaje de la cola

Puede borrar un mensaje de una cola en dos pasos.

1. Si llama a **list\_messages()**, obtiene, de forma predeterminada, el siguiente mensaje en una cola. También puede indicar cuántos mensajes desea obtener. Los mensajes devueltos por **list\_messages()** se hacen invisibles para cualquier otro código de lectura de mensajes de esta cola. Usted proporciona el tiempo de espera de la visibilidad en segundos a modo de parámetro.

2. Para terminar de quitar el mensaje de la cola, también debe llamar a **delete_message()**.

Este proceso de extracción de un mensaje que consta de dos pasos garantiza que si su código no puede procesar un mensaje a causa de un error de hardware o software, otra instancia de su código puede obtener el mismo mensaje e intentarlo de nuevo. Su código llama a **delete\_message()** justo después de que se haya procesado el mensaje.

	messages = azure_queue_service.list_messages("test-queue", 30)
	azure_queue_service.delete_message("test-queue", 
	  messages[0].id, messages[0].pop_receipt)

## <a id="how-to-change-the-contents-of-a-queued-message"></a>Deshabilitación o habilitación contenido de un mensaje en cola

Puede cambiar el contenido de un mensaje local en la cola. El código siguiente utiliza el método **update_message()** para actualizar un mensaje. Este método devolverá una tupla que contiene la recepción de confirmación del mensaje en cola y un valor de fecha y hora UTC que representa el momento en que el mensaje estará visible en la cola.

	message = azure_queue_service.list_messages("test-queue", 30)
	pop_receipt, time_next_visible = azure_queue_service.update_message(
	  "test-queue", message.id, message.pop_receipt, "updated test message", 
	  30)

## <a id="how-to-additional-options-for-dequeuing-messages"></a>Deshabilitación o habilitación quitar mensajes de la cola

Hay dos formas de personalizar la recuperación de mensajes de una cola.

1. En primer lugar, puede obtener un lote de mensajes.

2. En segundo lugar, puede establecer un tiempo de espera de la invisibilidad más largo o más corto para que el código disponga de más o menos tiempo para procesar cada mensaje.

El siguiente ejemplo de código utiliza el método **list\_messages()** para obtener 15 mensajes en una llamada. A continuación, imprime y elimina cada mensaje. También establece el tiempo de espera de la invisibilidad en cinco minutos para cada mensaje.

	azure_queue_service.list_messages("test-queue", 300
	  {:number_of_messages => 15}).each do |m|
	  puts m.message_text
	  azure_queue_service.delete_message("test-queue", m.id, m.pop_receipt)
	end

## <a id="how-to-get-the-queue-length"></a>Deshabilitación o habilitación la longitud de la cola

Puede obtener una estimación del número de mensajes existentes en la cola. El método **get\_queue\_metadata()** solicita al servicio de colas que devuelva el recuento aproximado de mensajes y metadatos sobre la cola.

	message_count, metadata = azure_queue_service.get_queue_metadata(
	  "test-queue")

## <a id="how-to-delete-a-queue"></a>Deshabilitación o habilitación una cola

Para eliminar una cola y todos los mensajes contenidos en ella, llame al método **delete\_queue()** en el objeto de cola.

	azure_queue_service.delete_queue("test-queue")

## <a id="next-steps"></a>Pasos siguientes

Ahora que está familiarizado con los aspectos básicos del almacenamiento en cola, utilice estos vínculos para obtener más información acerca de cómo realizar tareas de almacenamiento más complejas.

- Consulte la referencia de MSDN: [Almacenamiento de datos y acceso a los mismos en Azure](http://msdn.microsoft.com/en-us/library/windowsazure/gg433040.aspx)
- Visite el [blog del equipo de almacenamiento de Azure].(http://blogs.msdn.com/b/windowsazurestorage/)
- Visite el repositorio del [SDK de Azure para Ruby](https://github.com/WindowsAzure/azure-sdk-for-ruby) en GitHub

Para realizar una comparación entre el servicio de colas de Azure tratado en este artículo y las colas del Bus de servicio de Azure abordadas en el artículo [Utilización de las colas de Bus de servicio](/en-us/develop/ruby/how-to-guides/service-bus-queues/) , consulte [Colas de Azure y Colas de Bus de servicio de Azure: comparación y diferencias](http://msdn.microsoft.com/en-us/library/windowsazure/hh767287.aspx)

<!--HONumber=35_1-->
