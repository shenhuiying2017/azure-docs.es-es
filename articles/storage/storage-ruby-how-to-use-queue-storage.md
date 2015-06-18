<properties 
	pageTitle="Uso del almacenamiento de colas de Ruby | Microsoft Azure" 
	description="Aprenda a utilizar el servicio Cola de Azure para crear y eliminar colas e insertar, obtener y eliminar mensajes. Los ejemplos están escritos en Ruby." 
	services="storage" 
	documentationCenter="ruby" 
	authors="tfitzmac,tamram" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="ruby" 
	ms.topic="article" 
	ms.date="03/11/2015" 
	ms.author="tomfitz"/>


# Uso del almacenamiento de colas de Ruby

[AZURE.INCLUDE [storage-selector-queue-include](../../includes/storage-selector-queue-include.md)]

## Información general

Esta guía le indicará cómo actuar en situaciones habituales usando el
servicio de almacenamiento en cola de Azure. Los ejemplos están escritos usando la API Ruby de Azure.
Entre los escenarios descritos se incluyen **insertar**, **ojear**, **obtener** y **eliminar** mensajes de la cola, así como **crear y eliminar colas**.

[AZURE.INCLUDE [storage-queue-concepts-include](../../includes/storage-queue-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## Creación de una aplicación de Ruby

Cree una aplicación de Ruby. Para obtener instrucciones, consulte [Creación de una aplicación de Ruby en Azure](/develop/ruby/tutorials/web-app-with-linux-vm/).

## Configuración de su aplicación para obtener acceso al almacenamiento

Para usar el almacenamiento de Azure tendrá que descargar y usar el paquete Ruby azure, que incluye un conjunto de útiles bibliotecas que se comunican con los servicios REST de almacenamiento.

### Uso de RubyGems para obtener el paquete

1. Use una interfaz de línea de comandos como **PowerShell** (Windows), **Terminal** (Mac) o **Bash** (Unix).

2. Escriba "gem install azure" en la ventana de comandos para instalar la gema y las dependencias.

### Importación del paquete

Con el editor de texto que prefiera, agregue lo siguiente al principio del archivo de Ruby en el que pretenda utilizar el almacenamiento:

	require "azure"

## Configuración de una conexión de almacenamiento de Azure

El módulo azure leerá las variables de entorno **AZURE_STORAGE_ACCOUNT** y **AZURE_STORAGE_ACCESS_KEY** para obtener información necesaria para conectarse a su cuenta de almacenamiento de Azure. Si no se establecen estas variables de entorno, debe especificar la información de la cuenta antes de usar **Azure::QueueService** con el siguiente código:

	Azure.config.storage_account_name = "<your azure storage account>"
	Azure.config.storage_access_key = "<your Azure storage access key>"

Para obtener estos valores:

1. Inicie sesión en el [Portal de administración de Azure](https://manage.windowsazure.com/).
2. Vaya a la cuenta de almacenamiento que desea utilizar.
3. Haga clic en **ADMINISTRAR CLAVES** en la parte inferior del panel de navegación.
4. En el cuadro de diálogo emergente, verá el nombre de cuenta de almacenamiento, la clave de acceso principal y la clave de acceso secundaria. Para la clave de acceso, puede elegir la principal o la secundaria.

## Trabajo de una cola

El siguiente código crea un objeto **Azure::QueueService**, que permite trabajar con colas.

	azure_queue_service = Azure::QueueService.new

Utilice el método **create_queue()**) para crear una cola con el nombre especificado.

	begin
	  azure_queue_service.create_queue("test-queue")
	rescue
	  puts $!
	end

## Trabajo Inserción de un mensaje en una cola

Para insertar un mensaje en una cola, utilice el método **create_message()** para crear un nuevo mensaje y agregarlo a la cola.

	azure_queue_service.create_message("test-queue", "test message")

## Trabajo Inspección del siguiente mensaje

Puede ojear el mensaje situado en la parte delantera de una cola, sin quitarlo de la cola, llamando al método **peek_messages()**. De forma predeterminada, **peek_messages()** ojea un único mensaje. También puede indicar cuántos mensajes desea inspeccionar.

	result = azure_queue_service.peek_messages("test-queue",
	  {:number_of_messages => 10})

## Trabajo siguiente mensaje de la cola

Puede borrar un mensaje de una cola en dos pasos.

1. Al llamar a **list_messages()**, obtiene, de forma predeterminada, el siguiente mensaje en una cola También puede indicar cuántos mensajes desea obtener. Los mensajes devueltos por **list_messages()** se hacen invisibles para cualquier otro código que lea mensajes de esta cola. Usted proporciona el tiempo de espera de la visibilidad en segundos a modo de parámetro.

2. Para terminar quitando el mensaje de la cola, también debe llamar a **delete_message()**.

Este proceso de extracción de un mensaje que consta de dos pasos garantiza que si su código no puede procesar un mensaje a causa de un error de hardware o software, otra instancia de su código puede obtener el mismo mensaje e intentarlo de nuevo. El código llama a **delete_message()** justo después de que se haya procesado el mensaje.

	messages = azure_queue_service.list_messages("test-queue", 30)
	azure_queue_service.delete_message("test-queue", 
	  messages[0].id, messages[0].pop_receipt)

## Trabajo contenido de un mensaje en cola

Puede cambiar el contenido de un mensaje local en la cola. El código siguiente utiliza el método **update_message()** para actualizar un mensaje. Este método devolverá una tupla que contiene la recepción de confirmación del mensaje en cola y un valor de fecha y hora UTC que representa el momento en que el mensaje estará visible en la cola.

	message = azure_queue_service.list_messages("test-queue", 30)
	pop_receipt, time_next_visible = azure_queue_service.update_message(
	  "test-queue", message.id, message.pop_receipt, "updated test message", 
	  30)

## Trabajo Opciones adicionales para quitar mensajes de la cola

Hay dos formas de personalizar la recuperación de mensajes de una cola.

1. En primer lugar, puede obtener un lote de mensajes.

2. En segundo lugar, puede establecer un tiempo de espera de la invisibilidad más largo o más corto para que el código disponga de más o menos tiempo para procesar cada mensaje.

El siguiente ejemplo de código utiliza el método **list_messages()** para obtener 15 mensajes en una llamada. A continuación, imprime y elimina cada mensaje. También establece el tiempo de espera de la invisibilidad en cinco minutos para cada mensaje.

	azure_queue_service.list_messages("test-queue", 300
	  {:number_of_messages => 15}).each do |m|
	  puts m.message_text
	  azure_queue_service.delete_message("test-queue", m.id, m.pop_receipt)
	end

## Trabajo la longitud de la cola

Puede obtener una estimación del número de mensajes existentes en la cola. El método **get_queue_metadata()** solicita al servicio de colas que devuelva el recuento aproximado de mensajes y metadatos sobre la cola.

	message_count, metadata = azure_queue_service.get_queue_metadata(
	  "test-queue")

## Trabajo una cola

Para eliminar una cola y todos los mensajes contenidos en ella, llame al método **delete_queue()** en el objeto de cola.

	azure_queue_service.delete_queue("test-queue")

## Pasos siguientes

Ahora que está familiarizado con los aspectos básicos del almacenamiento de colas, utilice estos vínculos para obtener más información acerca de tareas de almacenamiento más complejas.

- Consulte la referencia de MSDN: [Almacenamiento de Azure](http://msdn.microsoft.com/library/azure/gg433040.aspx)
- Visite el [Blog del equipo de almacenamiento de Azure](http://blogs.msdn.com/b/windowsazurestorage/)
- Visite el repositorio de [SDK de Azure para Ruby](https://github.com/WindowsAzure/azure-sdk-for-ruby) en GitHub

Para ver una comparación entre el servicio de colas de Azure tratado en este artículo y las colas del Bus de servicio de Azure abordadas en el artículo [Utilización de las colas del Bus de servicio]develop/ruby/how-to-guides/service-bus-queues/), consulte [Colas de Azure y Colas de Bus de servicio de Azure: comparación y diferencias](http://msdn.microsoft.com/library/azure/hh767287.aspx)

<!--HONumber=49--> 