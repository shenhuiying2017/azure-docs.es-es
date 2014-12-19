<properties urlDisplayName="Service Bus Queues" pageTitle="Uso de las colas del bus de servicio (Python) - Azure" metaKeywords="colas del bus de servicio de Azure, colas de Azure, mensajería de Azure, colas de Azure en Python" description="Learn how to use Service Bus queues in Azure. Code samples written in Python." metaCanonical="" services="service-bus" documentationCenter="Python" title="How to Use Service Bus Queues" authors="huvalo" solutions="" manager="wpickett" editor="" />

<tags ms.service="service-bus" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="python" ms.topic="article" ms.date="09/19/2014" ms.author="huvalo" />




# Utilización de las colas del bus de servicio
Esta guía le mostrará cómo usar las colas del bus de servicio. Los ejemplos están
escritos en Python y utilizan el módulo de Azure para Python. Los escenarios
cubiertos incluyen **creación de colas, envío y recepción de mensajes** y 
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

[WACOM.INCLUDE [howto-service-bus-queues](../includes/howto-service-bus-queues.md)]

**Nota:** Si necesita instalar Python o las bibliotecas de clientes, consulte la [guía de instalación de Python](../python-how-to-install/).


## <a name="create-queue"> </a>Creación de una cola

El objeto **ServiceBusService** le permite trabajar con colas. Agregue lo siguiente cerca de la parte superior de todo archivo Python en el que desee obtener acceso al bus de servicio de Azure mediante programación:

	from azure.servicebus import ServiceBusService, Message, Queue

El siguiente código crea un objeto **ServiceBusService**. Reemplace "mynamespace", "sharedaccesskeyname" y "sharedaccesskey" por el espacio de nombres real, y el nombre clave y el valor de la firma de acceso compartido (SAS).

	bus_service = ServiceBusService(
		service_namespace='mynamespace',
		shared_access_key_name='sharedaccesskeyname',
		shared_access_key_value='sharedaccesskey')

Los valores para el nombre clave y el valor de la SAS pueden encontrarse en la información de conexión del Portal de Azure o en la ventana Propiedades de Visual Studio al seleccionar el espacio de nombres del bus de servicio en el Explorador de servidores (como se muestra en la sección anterior).

	bus_service.create_queue('taskqueue')

**create_queue** también admite opciones adicionales, que
permiten sobrescribir la configuración de cola predeterminada como el tiempo que dura
la transmisión de un mensaje o el tamaño máximo de la cola. En el ejemplo siguiente se muestra cómo establecer
el tamaño máximo de la cola en 5 GB con un tiempo de transmisión de 1 minuto:

	queue_options = Queue()
	queue_options.max_size_in_megabytes = '5120'
	queue_options.default_message_time_to_live = 'PT1M'

	bus_service.create_queue('taskqueue', queue_options)

## <a name="send-messages"> </a>Envío de mensajes a una cola

Para enviar un mensaje a una cola de bus de servicio, la aplicación llamará al método
**send\_queue\_message** en el objeto **ServiceBusService**.

En el siguiente ejemplo se demuestra cómo enviar un mensaje de prueba a
la cola llamada *taskqueue using* **send\_queue\_message**:

	msg = Message(b'Test Message')
	bus_service.send_queue_message('taskqueue', msg)

Las colas de bus de servicio admiten un mensaje con un tamaño máximo de 256 KB (el encabezado,
que incluye las propiedades de la aplicación estándar y personalizadas, puede tener
un tamaño máximo de 64 KB). No hay ningún límite en el número de mensajes
contenidos en una cola, pero sí hay un límite en el tamaño total de los mensajes
que una cola contiene. El tamaño de esta cola se define en tiempo de creación, con un
límite máximo de 5 GB.

## <a name="receive-messages"> </a>Recepción de mensajes de una cola

Los mensajes se reciben de una cola con la utilización del método **receive\_queue\_message**
en el objeto **ServiceBusService**:

	msg = bus_service.receive_queue_message('taskqueue', peek_lock=False)
	print(msg.body)

Los mensajes se eliminan de la cola a medida que se leen cuando el parámetro
**peek\_lock** está establecido en **False**. Puede leer (peek) y bloquear el
mensaje sin eliminarlo de la cola si define el parámetro
**peek\_lock** en **True**.

El comportamiento de leer y eliminar el mensaje como parte de la
operación de recepción es el modelo más sencillo y funciona mejor para los escenarios
en que una aplicación puede tolerar no procesar un mensaje en caso
de un error. Para comprender esto, considere un escenario en el que el
consumidor emite la solicitud de recepción y, a continuación, se bloquea antes de
procesarla. Dado que el Bus de servicio habrá marcado el mensaje como consumido,
a continuación, cuando la aplicación se reinicia y comienza a consumir mensajes de nuevo,
habrá perdido el mensaje que se consumió antes del bloqueo.


Si el parámetro **peek\_lock** está establecido en **True**, la recepción
se realiza en una operación en dos fases, lo que permite admitir aplicaciones
que no pueden tolerar la pérdida de mensajes. Cuando el Bus de servicio recibe una
solicitud, busca el siguiente mensaje que se va a consumir, lo bloquea para impedir que
otros consumidores lo reciban y lo devuelve a la aplicación.
Después de que la aplicación termina de procesar el mensaje (o lo almacena
de forma confiable para su futuro procesamiento), completa la segunda fase del
proceso de recepción al llamar al método **eliminar** en el objeto **Mensaje**
. El método **eliminar** marcará el mensaje como consumido
y lo eliminará de la cola.

	msg = bus_service.receive_queue_message('taskqueue', peek_lock=True)
	print(msg.body)

	msg.delete()

## <a name="handle-crashes"> </a>Actuación ante errores de la aplicación y mensajes que no se pueden leer

El bus de servicio proporciona funcionalidad para ayudarle a recuperarse correctamente de
errores de la aplicación o de las dificultades para procesar un mensaje. Si una
aplicación de recepción es incapaz de procesar el mensaje por alguna razón,
puede llamar al método **desbloquear** en el objeto
**Mensaje**. Esto hará que el Bus de servicio desbloquee el
mensaje de la cola y esté disponible para recibirlo de nuevo,
por la misma aplicación que lo consume o por otra
aplicación.

También hay un tiempo de espera asociado con un mensaje bloqueado en la
cola, y si la aplicación no puede procesar el mensaje antes del vencimiento
del tiempo de espera (por ejemplo, si se bloquea la aplicación), el bus de servicio
desbloquea el mensaje automáticamente y lo vuelve a poner a disposición
para que se pueda volver a recibir.

En caso de que la aplicación se bloquee después de procesar el mensaje
pero antes de llamar al método **eliminar**, el mensaje
se volverá a entregar a la aplicación cuando se reinicie. Esto suele denominarse
**Al menos un procesamiento**, es decir, que cada mensaje se procesará
al menos una vez, pero en ciertas situaciones se puede volver a entregar
el mismo mensaje. Si el escenario no puede tolerar el procesamiento duplicado,
los desarrolladores de aplicaciones deben agregar lógica adicional a su aplicación
para controlar la entrega de mensajes duplicados. A menudo esto se consigue mediante la
propiedad del mensaje **MessageId**, que permanecerá constante entre los
intentos de entrega.

## <a name="next-steps"> </a>Pasos siguientes

Ahora que ha aprendido los conceptos básicos de las colas del bus de servicio, siga estos
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
  [Conceptos de cola]: ../../../DevCenter/dotNet/Media/sb-queues-08.png
  [Portal de administración de Azure]: http://manage.windowsazure.com
  
  
  
  
  
  [Colas, temas y suscripciones del bus de servicio]: http://msdn.microsoft.com/es-es/library/windowsazure/hh367516.aspx
