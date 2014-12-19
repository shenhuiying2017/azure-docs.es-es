<properties urlDisplayName="Service Bus Queues" pageTitle="Uso de las colas del bus de servicio (Ruby) - Azure" metaKeywords="colas del bus de servicio de Azure, colas de Azure, mensajería de Azure, colas de Azure en Ruby" description="Learn how to use Service Bus queues in Azure. Code samples written in Ruby." metaCanonical="" services="service-bus" documentationCenter="Ruby" title="How to Use Service Bus Queues" authors="guayan" solutions="" manager="wpickett" editor="" />

<tags ms.service="service-bus" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="ruby" ms.topic="article" ms.date="01/01/1900" ms.author="guayan" />




# Utilización de las colas del bus de servicio

Esta guía le mostrará cómo usar las colas del bus de servicio. Los ejemplos están escritos en Ruby y usan la gema de Azure. Entre los escenarios proporcionados se incluyen los siguientes: **creación de colas, envío y recepción de mensajes** y **eliminación de colas**. Para obtener más información acerca de las colas, consulte la sección [Pasos siguientes](#next-steps).

## Tabla de contenido

* [¿Qué son las colas del bus de servicio?](#what-are-service-bus-queues)
* [Creación de un espacio de nombres de servicio](#create-a-service-namespace)
* [Obtención de credenciales de administración predeterminadas para el espacio de nombres](#obtain-default-credentials)
* [Creación de una aplicación de Ruby](#create-a-ruby-application)
* [Configuración de la aplicación para usar el bus de servicio](#configure-your-application-to-use-service-bus)
* [Configuración de una conexión del bus de servicio de Azure](#setup-a-windows-azure-service-bus-connection)
* [Creación de una cola](#how-to-create-a-queue)
* [Envío de mensajes a una cola](#how-to-send-messages-to-a-queue)
* [Recepción de mensajes de una cola](#how-to-receive-messages-from-a-queue)
* [Actuación ante errores de la aplicación y mensajes que no se pueden leer](#how-to-handle-application-crashes-and-unreadable-messages)
* [Pasos siguientes](#next-steps)

[WACOM.INCLUDE [howto-service-bus-queues](../includes/howto-service-bus-queues.md)]

## <a id="create-a-ruby-application"></a>Creación de una aplicación de Ruby

Cree una aplicación de Ruby. Para obtener instrucciones, consulte [Creación de una aplicación de Ruby en Azure](/es-es/develop/ruby/tutorials/web-app-with-linux-vm/).

## <a id="configure-your-application-to-use-service-bus"></a>Configuración de la aplicación para usar el bus de servicio

Para usar el bus de servicio de Azure tendrá que descargar y usar el paquete Ruby azure, que incluye un conjunto de útiles bibliotecas que se comunican con los servicios REST de almacenamiento.

### Uso de RubyGems para obtener el paquete

1. Use una interfaz de línea de comandos como **PowerShell** (Windows), **Terminal** (Mac) o **Bash** (Unix).

2. Escriba "gem install azure" en la ventana de comandos para instalar la gema y las dependencias.

### Importación del paquete

Con el editor de texto que prefiera, agregue lo siguiente al principio del archivo de Ruby en el que pretenda utilizar el almacenamiento:

    require "azure"

## <a id="setup-a-windows-azure-service-bus-connection"></a>Configuración de una conexión del bus de servicio de Azure

El módulo azure leerá las variables de entorno **AZURE\_SERVICEBUS\_NAMESPACE** y **AZURE\_SERVICEBUS\_ACCESS_KEY** para obtener la información necesaria para conectarse a su espacio de nombres del bus de servicio de Azure. Si no configura estas variables de entorno, debe especificar la información del espacio de nombres antes de usar **Azure::ServiceBusService** con el siguiente código:

    Azure.config.sb_namespace = "<your azure service bus namespace>"
    Azure.config.sb_access_key = "<your azure service bus access key>"

## <a id="how-to-create-a-queue"></a>Creación de una cola

El objeto **Azure::ServiceBusService** le permite trabajar con colas. Para crear una cola, use el método **create_queue()**. En el siguiente ejemplo se crea una cola o se imprime el error, si hay alguno.

    azure_service_bus_service = Azure::ServiceBusService.new
    begin
      queue = azure_service_bus_service.create_queue("test-queue")
    rescue
      puts $!
    end

También puede pasar un objeto **Azure::ServiceBus::Queue** con opciones adicionales, lo que le permite reemplazar la configuración de la cola predeterminada, como el período de vida del mensaje o el tamaño de cola máximo. En el siguiente ejemplo se muestra cómo establecer el tamaño máximo de las colas en 5 GB y el período de vida en 1 minuto:

    queue = Azure::ServiceBus::Queue.new("test-queue")
    queue.max_size_in_megabytes = 5120
    queue.default_message_time_to_live = "PT1M"

    queue = azure_service_bus_service.create_queue(queue)

## <a id="how-to-send-messages-to-a-queue"></a>Envío de mensajes a una cola

Para enviar un mensaje a una cola del bus de servicio, la aplicación debe llamar al método **send\_queue\_message()** en el objeto **Azure::ServiceBusService**. Los mensajes enviados a las colas del bus de servicio (y recibidos de ellas) son objetos **Azure::ServiceBus::BrokeredMessage** y cuentan con un conjunto de propiedades estándar (como **label** y **time\_to\_live**), un diccionario que se usa para mantener las propiedades personalizadas específicas de la aplicación y un conjunto de datos arbitrarios de aplicaciones. Una aplicación puede establecer el cuerpo del mensaje pasando un valor de cadena como mensaje, con lo que las propiedades estándar requeridas adquieren valores predeterminados.

El ejemplo siguiente demuestra cómo enviar un mensaje de prueba a la cola llamada "test-queue" usando **send\_queue\_message()**:

    message = Azure::ServiceBus::BrokeredMessage.new("test queue message")
    message.correlation_id = "test-correlation-id"
    azure_service_bus_service.send_queue_message("test-queue", message)

Las colas del bus de servicio admiten mensajes con un tamaño máximo de 256 KB (el encabezado, que incluye las propiedades estándar y personalizadas de la aplicación, puede tener como máximo un tamaño de 64 KB). No hay límite para el número de mensajes que contiene una cola, pero hay un tope para el tamaño total de los mensajes contenidos en una cola. El tamaño de la cola se define en el momento de la creación, con un límite de 5 GB.

## <a id="how-to-receive-messages-from-a-queue"></a>Recepción de mensajes de una cola

Los mensajes se reciben de una cola utilizando el método **receive\_queue\_message()** del objeto **Azure::ServiceBusService**. De forma predeterminada, los mensajes se leen y bloquean sin que se eliminen de la cola. Sin embargo, puede eliminar mensajes de la cola a medida que se leen estableciendo la opción **:peek_lock** en **false**.

El comportamiento predeterminado convierte la lectura y eliminación en una operación de dos fases que hace posible admitir aplicaciones que no toleran la pérdida de mensajes. Cuando el bus de servicio recibe una solicitud, busca el siguiente mensaje que se va a consumir, lo bloquea para impedir que otros consumidores lo reciban y, a continuación, lo devuelve a la aplicación. Una vez que la aplicación termina de procesar el mensaje (o lo almacena de forma fiable para su futuro procesamiento), completa la segunda fase del proceso de recepción llamando al método **delete\_queue\_message()** y facilitando el mensaje que se va a eliminar a modo de parámetro. El método **delete\_queue\_message()** marcará el mensaje como consumido y lo eliminará de la cola.

Si el parámetro **:peek\_lock** se define en **false**, la lectura y eliminación del mensaje se convierte en el modelo más simple y funciona mejor para los escenarios en los que una aplicación puede tolerar no procesar un mensaje en caso de error. Para entenderlo mejor, pongamos una situación en la que un consumidor emite la solicitud de recepción que se bloquea antes de procesarla. Como el bus de servicio habrá marcado el mensaje como consumido, cuando la aplicación se reinicie y empiece a consumir mensajes de nuevo, habrá perdido el mensaje que se consumió antes del bloqueo.

En el ejemplo que aparece a continuación, se indica cómo se pueden recibir y procesar los mensajes usando **receive\_queue\_message()**. El ejemplo primero recibe y elimina un mensaje mediante **:peek\_lock** establecido en **false**; a continuación, recibe otro mensaje y lo elimina mediante **delete\_queue\_message()**:

    message = azure_service_bus_service.receive_queue_message("test-queue", 
	  { :peek_lock => false })
    message = azure_service_bus_service.receive_queue_message("test-queue")
    azure_service_bus_service.delete_queue_message(message)

## <a id="how-to-handle-application-crashes-and-unreadable-messages"></a>Actuación ante errores de la aplicación y mensajes que no se pueden leer

El bus de servicio proporciona una funcionalidad que le ayuda a superar sin problemas los errores de la aplicación o las dificultades para procesar un mensaje. Si por cualquier motivo una aplicación de recepción es incapaz de procesar el mensaje, entonces puede llamar al método **unlock\_queue\_message()** del objeto **Azure::ServiceBusService**. Esto hará que el bus de servicio desbloquee el mensaje de la cola y esté disponible para que pueda volver a recibirse, ya sea por la misma aplicación que lo consume o por otra.

También hay un tiempo de espera asociado con un mensaje bloqueado en la cola y, si la aplicación no puede procesar el mensaje antes de que finalice el tiempo de espera del bloqueo (por ejemplo, si la aplicación sufre un error), entonces el bus de servicio desbloquea el mensaje automáticamente y hace que esté disponible para que pueda volver a recibirse.

En caso de que la aplicación sufra un error después de procesar el mensaje mensaje y antes de llamar al método **delete\_queue\_message()**, entonces el mensaje se volverá a entregar a la aplicación cuando esta se reinicie. Habitualmente se denomina **At Least Once Processing**; es decir, cada mensaje se procesará al menos una vez; aunque en determinadas situaciones podría volver a entregarse el mismo mensaje. Si el escenario no puede tolerar el procesamiento duplicado, entonces los desarrolladores de la aplicación deberían agregar lógica adicional a su aplicación para solucionar la entrega de mensajes duplicados. A menudo, esto se consigue usando la propiedad **message\_id** del mensaje, que permanecerá constante en todos los intentos de entrega.

## <a id="next-steps"></a>Pasos siguientes

Ahora que conoce los fundamentos de las colas del bus de servicio, siga estos vínculos para obtener más información.

-   Consulte la referencia de MSDN: [Colas, temas y suscripciones del Service Bus](http://msdn.microsoft.com/es-es/library/windowsazure/hh367516.aspx)
-   Visite el repositorio de [SDK de Azure para Ruby](https://github.com/WindowsAzure/azure-sdk-for-ruby) en GitHub.

Para ver la comparación entre Colas del Bus de servicio de Azure de este artículo y Colas de Azure del artículo [Uso del servicio Cola de Azure](/es-es/develop/ruby/how-to-guides/queue-service/), consulte [Colas de Azure y Colas de bus de servicio de Azure: comparación y diferencias](http://msdn.microsoft.com/es-es/library/windowsazure/hh767287.aspx)
