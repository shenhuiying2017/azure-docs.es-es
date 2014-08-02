<properties linkid="develop-python-service-bus-queues" urlDisplayName="Service Bus Queues" pageTitle="How to use Service Bus queues (Python) - Azure" metaKeywords="Azure Service Bus queues, Azure queues, Azure messaging, Azure queues Python" description="Learn how to use Service Bus queues in Azure. Code samples written in Python." metaCanonical="" services="service-bus" documentationCenter="Python" title="How to Use Service Bus Queues" authors="" solutions="" manager="" editor="" />

Utilización de las colas del bus de servicio
============================================

Esta guía le mostrará cómo usar las colas del bus de servicio. Los ejemplos están escritos en Python y usan el modelo de Azure para Python. Entre los escenarios proporcionados se incluyen los siguientes: **creación de colas, envío y recepción de mensajes** y **eliminación de colas**. Para obtener más información acerca de las colas, consulte la sección [Pasos siguientes](#next-steps).

Tabla de contenido
------------------

-   [¿Qué son las colas del bus de servicio?](#what-are-service-bus-queues)
-   [Creación de un espacio de nombres de servicio](#create-a-service-namespace)
-   [Obtención de credenciales de administración predeterminadas para el espacio de nombres](#obtain-default-credentials)
-   [Creación de una cola](#create-queue)
-   [Envío de mensajes a una cola](#send-messages)
-   [Recepción de mensajes desde una cola](#receive-messages)
-   [Actuación ante errores de la aplicación y mensajes que no se pueden leer](#handle-crashes)
-   [Pasos siguientes](#next-steps)

[WACOM.INCLUDE [howto-service-bus-queues](../includes/howto-service-bus-queues.md)]

**Nota:** Si necesita instalar Python o las bibliotecas de clientes, consulte la [guía de instalación de Python](../python-how-to-install/) (en inglés).

Creación de una cola
--------------------

El objeto **ServiceBusService** le permite trabajar con colas. Agregue lo siguiente cerca de la parte superior de todo archivo Python en el que desee obtener acceso al bus de servicio de Azure mediante programación:

    from azure.servicebus import *

El siguiente código crea un objeto **ServiceBusService**. Reemplace 'mynamespace', 'mykey' y 'myissuer' por el espacio de nombres, la clave y el emisor reales.

    bus_service = ServiceBusService(service_namespace='mynamespace', account_key='mykey', issuer='myissuer')

    bus_service.create_queue('taskqueue')

**create\_queue** también admite opciones adicionales, lo que permite modificar la configuración predeterminada de las colas, como el período de vida de los mensajes o el tamaño máximo de la cola. En el siguiente ejemplo se muestra cómo establecer el tamaño máximo de las colas en 5 GB y el período de vida en 1 minuto:

    queue_options = Queue()
    queue_options.max_size_in_megabytes = '5120'
    queue_options.default_message_time_to_live = 'PT1M'

    bus_service.create_queue('taskqueue', queue_options)

Envío de mensajes a una cola
----------------------------

Para enviar un mensaje a una cola del bus de servicio, la aplicación debe utilizar el método **send\_queue\_message** del objeto **ServiceBusService**.

El siguiente ejemplo demuestra cómo se debe enviar un mensaje de prueba a la cola llamada *taskqueue con* **send\_queue\_message**:

    msg = Message('Test Message')
    bus_service.send_queue_message('taskqueue', msg)

Las colas del bus de servicio admiten mensajes con un tamaño máximo de 256 KB (el encabezado, que incluye las propiedades estándar y personalizadas de la aplicación, puede tener como máximo un tamaño de 64 KB). No hay límite para el número de mensajes que contiene una cola, pero hay un tope para el tamaño total de los mensajes contenidos en una cola. El tamaño de la cola se define en el momento de la creación, con un límite de 5 GB.

Recepción de mensajes de una cola
---------------------------------

Los mensajes se reciben de una cola utilizando el método **receive\_queue\_message** del objeto **ServiceBusService**.

    msg = bus_service.receive_queue_message('taskqueue')
    print(msg.body)

Los mensajes se eliminan de la cola una vez que se leen; sin embargo, puede leer y bloquear los mensajes sin eliminarlos de la cola estableciendo el parámetro opcional **peek\_lock** en **True**.

El funcionamiento predeterminado por el que los mensajes se eliminan tras leerlos como parte del proceso de recepción es el modelo más sencillo y el que mejor funciona en aquellas situaciones en las que una aplicación puede tolerar que no se procese un mensaje en caso de error. Para entenderlo mejor, pongamos una situación en la que un consumidor emite la solicitud de recepción que se bloquea antes de procesarla. Como el bus de servicio habrá marcado el mensaje como consumido, cuando la aplicación se reinicie y empiece a consumir mensajes de nuevo, habrá perdido el mensaje que se consumió antes del bloqueo.

Si el parámetro **peek\_lock** está establecido en **True**, el proceso de recepción se convierte en una operación en dos fases que hace posible admitir aplicaciones que no toleran la pérdida de mensajes. Cuando el bus de servicio recibe una solicitud, busca el siguiente mensaje que se va a consumir, lo bloquea para impedir que otros consumidores lo reciban y, a continuación, lo devuelve a la aplicación. Una vez que la aplicación termina de procesar el mensaje (o lo almacena de forma confiable para su futuro procesamiento), completa la segunda fase del proceso de recepción llamando al método **delete** en el objeto **Message**. El método **delete** marcará el mensaje como consumido y lo eliminará de la cola.

    msg = bus_service.receive_queue_message('taskqueue', peek_lock=True)
    print(msg.body)

    msg.delete()

Actuación ante errores de la aplicación y mensajes que no se pueden leer
------------------------------------------------------------------------

El bus de servicio proporciona una funcionalidad que le ayuda a superar sin problemas los errores de la aplicación o las dificultades para procesar un mensaje. Si por cualquier motivo una aplicación de recepción es incapaz de procesar el mensaje, entonces puede llamar al método **unlock** del objeto **Message**. Esto hará que el bus de servicio desbloquee el mensaje de la cola y esté disponible para que pueda volver a recibirse, ya sea por la misma aplicación que lo consume o por otra.

También hay un tiempo de espera asociado con un mensaje bloqueado en la cola y, si la aplicación no puede procesar el mensaje antes de que finalice el tiempo de espera del bloqueo (por ejemplo, si la aplicación sufre un error), entonces el bus de servicio desbloquea el mensaje automáticamente y hace que esté disponible para que pueda volver a recibirse.

En caso de que la aplicación sufra un error después de procesar el mensaje y antes de llamar al método **delete**, entonces el mensaje se volverá a entregar a la aplicación cuando esta se reinicie. Habitualmente se denomina **At Least Once Processing**; es decir, cada mensaje se procesará al menos una vez; aunque en determinadas situaciones podría volver a entregarse el mismo mensaje. Si el escenario no puede tolerar el procesamiento duplicado, entonces los desarrolladores de la aplicación deberían agregar lógica adicional a su aplicación para solucionar la entrega de mensajes duplicados. A menudo, esto se consigue usando la propiedad **MessageId** del mensaje, que permanecerá constante en todos los intentos de entrega.

Pasos siguientes
----------------

Ahora que conoce los fundamentos de las colas del bus de servicio, siga estos vínculos para obtener más información.

-   Consulte la referencia de MSDN: [Colas, temas y suscripciones del bus de servicio](http://msdn.microsoft.com/en-us/library/windowsazure/hh367516.aspx)

