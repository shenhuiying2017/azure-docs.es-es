<properties linkid="develop-python-service-bus-queues" urlDisplayName="Service Bus Queues" pageTitle="How to use Service Bus queues (Python) - Azure" metaKeywords="Azure Service Bus queues, Azure queues, Azure messaging, Azure queues Python" description="Learn how to use Service Bus queues in Azure. Code samples written in Python." metaCanonical="" services="service-bus" documentationCenter="Python" title="How to Use Service Bus Queues" authors="huvalo" solutions="" manager="" editor="" />

<tags ms.service="service-bus" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="python" ms.topic="article" ms.date="01/01/1900" ms.author="huvalo"></tags>

# Utilización de las colas del bus de servicio

Esta guía le mostrará cómo usar las colas del bus de servicio. Los ejemplos están
escritos en Python y usan el modelo de Azure para Python. Entre los escenarios
proporcionados se incluyen los siguientes: **creación de colas, envío y recepción de mensajes** y
**eliminación de colas**. Para obtener más información acerca de las colas, consulte la sección [Pasos siguientes][].

## Tabla de contenido

-   [¿Qué son las colas del bus de servicio?][]
-   [Creación de un espacio de nombres de servicio][]
-   [Obtención de credenciales de administración predeterminadas para el espacio de nombres][]
-   [Direccionamiento del una cola][]
-   [Direccionamiento del mensajes a una cola][]
-   [Direccionamiento del mensajes desde una cola][]
-   [Direccionamiento del ante errores de la aplicación y mensajes que no se pueden leer][]
-   [Pasos siguientes][]

[WACOM.INCLUDE [howto-service-bus-queues][]]

**Nota:** Si necesita instalar Python o las bibliotecas de clientes, consulte la [guía de instalación de Python][] (en inglés).

## <a name="create-queue"> </a>Creación de una cola

El objeto **ServiceBusService** le permite trabajar con colas. Agregue lo siguiente cerca de la parte superior de todo archivo Python en el que desee obtener acceso al bus de servicio de Azure mediante programación:

    from azure.servicebus import *

El siguiente código crea un objeto **ServiceBusService**. Reemplace "mynamespace", "sharedaccesskeyname" y "sharedaccesskey" por el espacio de nombres real, y el nombre clave y el valor de la firma de acceso compartido (SAS).

    bus_service = ServiceBusService(
        service_namespace='mynamespace',
        shared_access_key_name='sharedaccesskeyname',
        shared_access_key_value='sharedaccesskey')

Los valores para el nombre clave y el valor de la SAS pueden encontrarse en la información de conexión del Portal de Azure o en la ventana Propiedades de Visual Studio al seleccionar el espacio de nombres del bus de servicio en el Explorador de servidores (como se muestra en la sección anterior).

    bus_service.create_queue('taskqueue')

**create\_queue** también admite opciones adicionales, lo que
permite modificar la configuración predeterminada de las colas, como el período de vida de los mensajes
o el tamaño máximo de la cola. En el siguiente ejemplo se muestra cómo establecer el
tamaño máximo de las colas en 5 GB y el período de vida en 1 minuto:

    queue_options = Queue()
    queue_options.max_size_in_megabytes = '5120'
    queue_options.default_message_time_to_live = 'PT1M'

    bus_service.create_queue('taskqueue', queue_options)

## <a name="send-messages"> </a>Envío de mensajes a una cola

Para enviar un mensaje a una cola del bus de servicio, la aplicación debe utilizar el método
**send\_queue\_message** del objeto **ServiceBusService**.

El siguiente ejemplo demuestra cómo se debe enviar un mensaje de prueba a la
cola llamada *taskqueue con* **send\_queue\_message**:

    msg = Message('Test Message')
    bus_service.send_queue_message('taskqueue', msg)

Las colas del bus de servicio admiten mensajes con un tamaño máximo de 256 KB (el encabezado,
que incluye las propiedades estándar y personalizadas de la aplicación, puede tener como máximo un
tamaño de 64 KB). No hay límite para el número de mensajes que
contiene una cola, pero hay un tope para el tamaño total de los mensajes
contenidos en una cola. El tamaño de la cola se define en el momento de la creación, con un
límite de 5 GB.

## <a name="receive-messages"> </a>Recepción de mensajes de una cola

Los mensajes se reciben de una cola utilizando el método **receive\_queue\_message**
 del objeto **ServiceBusService**.

    msg = bus_service.receive_queue_message('taskqueue', peek_lock=False)
    print(msg.body)

Los mensajes se borran de la cola a medida que se leen cuando el parámetro
**peek\_lock** está establecido como **False**. Puede leer y bloquear
los mensajes sin eliminarlos de la cola si establece el parámetro opcional
**peek\_lock** como **True**.

El funcionamiento por el que los mensajes se eliminan tras
leerlos como parte del proceso de recepción es el modelo más sencillo y el que mejor funciona en
aquellas situaciones en las que una aplicación puede tolerar que no se procese un mensaje
en caso de error. Para entenderlo mejor, pongamos una situación en la que un
consumidor emite la solicitud de recepción que se bloquea antes de
procesarla. Como el bus de servicio habrá marcado el mensaje como consumido,
cuando la aplicación se reinicie y empiece a consumir mensajes de nuevo,
habrá perdido el mensaje que se consumió antes del bloqueo.

Si el parámetro **peek\_lock** está establecido en **True**, el proceso de recepción se convierte en una
operación en dos fases que hace posible admitir aplicaciones
que no toleran la pérdida de mensajes. Cuando el bus de servicio recibe una solicitud, busca el siguiente mensaje
que se va a consumir, lo bloquea para impedir que otros consumidores lo
reciban y, a continuación,
lo devuelve a la aplicación.
Una vez que la aplicación termina de procesar el mensaje (o lo almacena de forma confiable para su
futuro procesamiento), completa la segunda fase del proceso de recepción llamando al método **delete** en el objeto **Message**
. El método **delete** marcará el mensaje como consumido
y lo eliminará de la cola.

    msg = bus_service.receive_queue_message('taskqueue', peek_lock=True)
    print(msg.body)

    msg.delete()

## <a name="handle-crashes"> </a>Actuación ante errores de la aplicación y mensajes que no se pueden leer

El bus de servicio proporciona una funcionalidad que le ayuda a superar sin problemas los
errores de la aplicación o las dificultades para procesar un mensaje. Si por
cualquier motivo una aplicación de recepción es incapaz de procesar el mensaje,
entonces puede llamar al método **unlock** del objeto
**Message**. Esto hará que el bus de servicio
desbloquee el mensaje de la cola y esté disponible para que pueda volver a recibirse,
ya sea por la misma aplicación que lo
consume o por otra.

También hay un tiempo de espera
asociado con un mensaje bloqueado en la cola y, si la aplicación no puede procesar el
mensaje antes de que finalice el tiempo de espera del bloqueo (por ejemplo, si la aplicación sufre un error), entonces el
bus de servicio desbloquea el mensaje automáticamente y hace que esté disponible para que
pueda volver a recibirse.

En caso de que la aplicación sufra un error después de procesar el mensaje y antes de
llamar al método **delete**, entonces el mensaje se volverá a entregar a la
aplicación cuando esta se reinicie. Habitualmente se denomina
**At Least Once Processing**; es decir, cada mensaje se procesará al
menos una vez; aunque en determinadas situaciones podría
volver a entregarse el mismo mensaje. Si el escenario no puede tolerar el procesamiento duplicado, entonces los desarrolladores de la aplicación deberían agregar lógica adicional a su
aplicación para solucionar
la entrega de mensajes duplicados. A menudo, esto se consigue usando la propiedad
**MessageId** del mensaje, que permanecerá constante en todos los
intentos de entrega.

## <a name="next-steps"> </a>Pasos siguientes

Ahora que conoce los fundamentos de las colas del bus de servicio, siga estos
vínculos para obtener más información.

-   Consulte la referencia de MSDN: [Colas, temas y suscripciones del bus de servicio][]

  [Pasos siguientes]: #next-steps
  [¿Qué son las colas del bus de servicio?]: #what-are-service-bus-queues
  [Creación de un espacio de nombres de servicio]: #create-a-service-namespace
  [Obtención de credenciales de administración predeterminadas para el espacio de nombres]: #obtain-default-credentials
  [Direccionamiento del una cola]: #create-queue
  [Direccionamiento del mensajes a una cola]: #send-messages
  [Direccionamiento del mensajes desde una cola]: #receive-messages
  [Direccionamiento del ante errores de la aplicación y mensajes que no se pueden leer]: #handle-crashes
  [howto-service-bus-queues]: ../includes/howto-service-bus-queues.md
  [guía de instalación de Python]: ../python-how-to-install/
  [Colas, temas y suscripciones del bus de servicio]: http://msdn.microsoft.com/es-es/library/windowsazure/hh367516.aspx
