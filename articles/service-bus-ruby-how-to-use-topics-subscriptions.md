<properties linkid="dev-ruby-how-to-service-bus-topics" urlDisplayName="Service Bus Topics" pageTitle="How to use Service Bus topics (Ruby) - Azure" metaKeywords="Get started Azure Service Bus topics, Get Started Service Bus topics, Azure publish subscribe messaging, Azure messaging topics and subscriptions, Service Bus topic ruby" description="Learn how to use Service Bus topics and subscriptions in Azure. Code samples are written for Ruby applications." metaCanonical="" services="service-bus" documentationCenter="Ruby" title="How to Use Service Bus Topics/Subscriptions" authors="guayan" solutions="" manager="" editor="" />

<tags ms.service="service-bus" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="ruby" ms.topic="article" ms.date="01/01/1900" ms.author="guayan"></tags>

# Uso de temas/suscripciones del bus de servicio

En esta guía se indica cómo usar los temas y las suscripciones del bus de servicio desde aplicaciones Ruby. Entre los escenarios tratados se incluyen **la creación de temas y suscripciones, la creación de filtros de suscripción, el envío de mensajes a un tema**, **la recepción de mensajes de una suscripción** y **la eliminación de temas y suscripciones**. Para obtener más información acerca de los temas y las suscripciones, consulte la sección [Pasos siguientes][].

## Tabla de contenido

-   [Qué son las suscripciones y los temas del bus de servicio][]
-   [Creación de un espacio de nombres de servicio][]
-   [Obtención de credenciales de administración predeterminadas para el espacio de nombres][]
-   [Creación de una aplicación de Ruby][]
-   [Configuración de la aplicación para usar el bus de servicio][]
-   [Configuración de una conexión del bus de servicio de Azure][]
-   [Creación de un tema][]
-   [Creación de suscripciones][]
-   [Envío de mensajes a un tema][]
-   [Recepción de mensajes de una suscripción][]
-   [Actuación ante errores de la aplicación y mensajes que no se pueden leer][]
-   [Eliminación de temas y suscripciones][]
-   [Pasos siguientes][]

[WACOM.INCLUDE [howto-service-bus-topics][]]

## <span id="create-a-ruby-application"></span></a>Creación de una aplicación de Ruby

Cree una aplicación de Ruby. Para obtener instrucciones, consulte [Creación de una aplicación de Ruby en Azure][].

## <span id="configure-your-application-to-use-service-bus"></span></a>Configuración de la aplicación para usar el bus de servicio

Para usar el bus de servicio de Azure tendrá que descargar y usar el paquete Ruby azure, que incluye un conjunto de útiles bibliotecas que se comunican con los servicios REST de almacenamiento.

### Uso de RubyGems para obtener el paquete

1.  Use una interfaz de línea de comandos como **PowerShell** (Windows), **Terminal** (Mac) o **Bash** (Unix).

2.  Escriba "gem install azure" en la ventana de comandos para instalar la gema y las dependencias.

### Importación del paquete

Con el editor de texto que prefiera, agregue lo siguiente al principio del archivo de Ruby en el que pretenda utilizar el almacenamiento:

    require "azure"

## <span id="setup-a-windows-azure-service-bus-connection"></span></a>Configuración de una conexión del bus de servicio de Azure

El módulo azure leerá las variables de entorno **AZURE\_SERVICEBUS\_NAMESPACE** y **AZURE\_SERVICEBUS\_ACCESS\_KEY**
 para obtener la información necesaria para conectarse a su espacio de nombres del bus de servicio de Azure. Si no configura estas variables de entorno, debe especificar la información del espacio de nombres antes de usar **Azure::ServiceBusService** con el siguiente código:

    Azure.config.sb_namespace = "<your azure service bus namespace>"
    Azure.config.sb_access_key = "<your azure service bus access key>"

## <span id="how-to-create-a-topic"></span></a>Creación de un tema

El objeto **Azure::ServiceBusService** le permite trabajar con temas. El siguiente código crea un objeto **Azure::ServiceBusService**. Para crear un tema, use el método **create\_topic()**. En el siguiente ejemplo se crea un tema o se imprime el error si lo hubiera.

    azure_service_bus_service = Azure::ServiceBusService.new
    begin
      topic = azure_service_bus_service.create_queue("test-topic")
    rescue
      puts $!
    end

También puede pasar un objeto **Azure::ServiceBus::Topic** con opciones adicionales, lo que le permite reemplazar la configuración de la cola predeterminada, como el período de vida del mensaje o el tamaño de cola máximo. En el siguiente ejemplo se muestra cómo establecer el tamaño máximo de las colas en 5 GB y el período de vida en 1 minuto:

    topic = Azure::ServiceBus::Topic.new("test-topic")
    topic.max_size_in_megabytes = 5120
    topic.default_message_time_to_live = "PT1M"

    topic = azure_service_bus_service.create_topic(topic)

## <span id="how-to-create-subscriptions"></span></a>Creación de suscripciones

Las suscripciones a temas también se crean con el objeto **Azure::ServiceBusService**. A las suscripciones se les asigna un nombre y pueden tener un filtro opcional que restrinja el conjunto de mensajes que pasan a su cola virtual.

**Nota:**
 las suscripciones son permanentes y seguirán existiendo hasta que se eliminen o se elimine el tema al que están asociadas. Si la aplicación contiene lógica para crear una suscripción, primero debe comprobar si esta ya existe utilizando el método getSubscription.

### Creación de una suscripción con el filtro predeterminado (MatchAll)

El filtro predeterminado **MatchAll** se usa en caso de que no se haya especificado ninguno al crear una suscripción. Al usar el filtro **MatchAll**, todos los mensajes publicados en el tema se colocan en la cola virtual de la suscripción. En el ejemplo siguiente se crea una suscripción llamada "all-messages" que usa el filtro predeterminado **MatchAll**.

    subscription = azure_service_bus_service.create_subscription("test-topic", 
      "all-messages")

### <span id="how-to-create-subscriptions"></span></a>Creación de suscripciones con filtros

También puede configurar filtros que le permitan especificar qué mensajes enviados a un tema deben aparecer dentro de una suscripción de tema determinada.

El tipo de filtro más flexible compatible con las suscripciones es **Azure::ServiceBus::SqlFilter**, que implementa un subconjunto de SQL92. Los filtros de SQL operan en las propiedades de los mensajes que se publican en el tema. Para obtener más información acerca de las expresiones que se pueden usar con un filtro de SQL, revise la sintaxis de [SqlFilter.SqlExpression][].

Es posible agregar filtros a una suscripción utilizando el método **create\_rule()** del objeto **Azure::ServiceBusService**. Este método le permite agregar nuevos filtros a una suscripción existente.

**Nota:**
 ya que el filtro predeterminado se aplica automáticamente a todas las suscripciones nuevas, primero debe eliminar el filtro predeterminado **MatchAll** si no quiere que este anule todos los otros filtros que especifique. Puede eliminar el filtro predeterminado utilizando el método **delete\_rule()** del objeto **Azure::ServiceBusService**.

En los ejemplos que aparecen a continuación se crea una suscripción llamada "high-messages" con un **Azure::ServiceBus::SqlFilter** que solo selecciona los mensajes con una propiedad **Azure::ServiceBus::SqlFilter** personalizada superior a 3:

    subscription = azure_service_bus_service.create_subscription("test-topic", 
      "high-messages")
    azure_service_bus_service.delete_rule("test-topic", "high-messages", 
      "$Default")

    rule = Azure::ServiceBus::Rule.new("high-messages-rule")
    rule.topic = "test-topic"
    rule.subscription = "high-messages"
    rule.filter = Azure::ServiceBus::SqlFilter.new({ 
      :sql_expression => "message_number > 3" })
    rule = azure_service_bus_service.create_rule(rule)

Del mismo modo, en el ejemplo que aparece a continuación se crea una suscripción llamada "low-messages" con un **Azure::ServiceBus::SqlFilter** que solo selecciona los mensajes con una propiedad **message\_number** igual a 3 o menor:

    subscription = azure_service_bus_service.create_subscription("test-topic", 
      "low-messages")
    azure_service_bus_service.delete_rule("test-topic", "low-messages", 
      "$Default")

    rule = Azure::ServiceBus::Rule.new("low-messages-rule")
    rule.topic = "test-topic"
    rule.subscription = "low-messages"
    rule.filter = Azure::ServiceBus::SqlFilter.new({ 
      :sql_expression => "message_number <= 3" })
    rule = azure_service_bus_service.create_rule(rule)

Ahora, cuando se envíe un mensaje a "test-topic", siempre se entregará a los destinatarios suscritos a la suscripción de tema "all-messages" y, selectivamente, a los destinatarios suscritos a la suscripción de tema "high-messages" o "low-messages" (dependiendo del contenido del mensaje).

## <span id="how-to-send-messages-to-a-topic"></span></a>Envío de mensajes a un tema

Para enviar un mensaje a un tema del bus de servicio, la aplicación debe utilizar el método **send\_topic\_message()** del objeto **Azure::ServiceBusService**. Los mensajes enviados a los temas del bus de servicio son objetos **Azure::ServiceBus::BrokeredMessage**. Los objetos **Azure::ServiceBus::BrokeredMessage** cuentan con un conjunto de propiedades estándar (como **label** y **time\_to\_live**), un diccionario que se usa para mantener las propiedades personalizadas específicas de la aplicación y un conjunto de datos de cadenas. Una aplicación puede establecer el cuerpo del mensaje pasando un valor de cadena al método **send\_topic\_message()**, con lo que las propiedades estándar requeridas adquieren valores predeterminados.

En el siguiente ejemplo se demuestra cómo enviar cinco mensajes de prueba a "test-topic". Fíjese en cómo el valor de la propiedad **message\_number** de cada mensaje varía en función de la iteración del bucle (así se determinará qué suscripción lo recibe):

    5.times do |i|
      message = Azure::ServiceBus::BrokeredMessage.new("test message " + i, 
        { :message_number => i })
      azure_service_bus_service.send_topic_message("test-topic", message)
    end

Los temas del bus de servicio admiten mensajes con un tamaño máximo de 256 MB (el encabezado, que incluye las propiedades estándar y personalizadas de la aplicación, puede tener como máximo un tamaño de 64 MB). No hay límite para el número de mensajes que contiene un tema, pero hay un tope para el tamaño total de los mensajes contenidos en un tema. El tamaño de los temas se define en el momento de la creación (el límite máximo es de 5 GB).

## <span id="how-to-receive-messages-from-a-subscription"></span></a>Recepción de mensajes de una suscripción

Los mensajes se reciben de una suscripción utilizando el método **receive\_subscription\_message** del objeto **ServiceBusService**. De forma predeterminada, los mensajes se leen (máximo) y bloquean sin que se eliminen de la suscripción. Puede leer y eliminar el mensaje de la suscripción estableciendo la opción **:peek\_lock** en **false**.

El comportamiento predeterminado convierte la lectura y eliminación en una operación de dos fases que hace posible admitir aplicaciones que no toleran la pérdida de mensajes. Cuando el bus de servicio recibe una solicitud, busca el siguiente mensaje que se va a consumir, lo bloquea para impedir que otros consumidores lo reciban y, a continuación, lo devuelve a la aplicación. Una vez que la aplicación termina de procesar el mensaje (o lo almacena de forma fiable para su futuro procesamiento), completa la segunda fase del proceso de recepción llamando al método **delete\_subscription\_message()** y facilitando el mensaje que se va a eliminar a modo de parámetro. El método **delete\_subscription\_message()** marcará el mensaje como consumido y lo eliminará de la suscripción.

Si el parámetro **:peek\_lock** se establece en **false**, la lectura y eliminación del mensaje se convierte en el modelo más simple y funciona mejor para los escenarios en los que una aplicación puede tolerar no procesar un mensaje en caso de error. Para entenderlo mejor, pongamos una situación en la que un consumidor emite la solicitud de recepción que se bloquea antes de procesarla. Como el bus de servicio habrá marcado el mensaje como consumido, cuando la aplicación se reinicie y empiece a consumir mensajes de nuevo, habrá perdido el mensaje que se consumió antes del bloqueo.

El siguiente ejemplo muestra cómo pueden recibirse y procesarse los mensajes con **receive\_subscription\_message()**. El ejemplo primero recibe y elimina un mensaje de la suscripción "low-messages" mediante **:peek\_lock** establecido en **false** y, a continuación, recibe otro mensaje de "high-messages" y lo elimina mediante **delete\_subscription\_message()**:

    message = azure_service_bus_service.receive_subscription_message(
      "test-topic", "low-messages", { :peek_lock => false })
    message = azure_service_bus_service.receive_subscription_message(
      "test-topic", "high-messages")
    azure_service_bus_service.delete_subscription_message(message)

## <span id="how-to-handle-application-crashes-and-unreadable-messages"></span></a>Actuación ante errores de la aplicación y mensajes que no se pueden leer

El bus de servicio proporciona una funcionalidad que le ayuda a superar sin problemas los errores de la aplicación o las dificultades para procesar un mensaje. Si por cualquier motivo una aplicación de recepción es incapaz de procesar el mensaje, entonces puede llamar al método **unlock\_subscription\_message()** del objeto **Azure::ServiceBusService**. Esto hará que el bus de servicio desbloquee el mensaje de la suscripción y esté disponible para que pueda volver a recibirse, ya sea por la misma aplicación que lo consume o por otra.

También hay un tiempo de espera asociado con un mensaje bloqueado en la suscripción y, si la aplicación no puede procesar el mensaje antes de que finalice el tiempo de espera del bloqueo (por ejemplo, si la aplicación sufre un error), entonces el bus de servicio desbloquea el mensaje automáticamente y hace que esté disponible para que pueda volver a recibirse.

En caso de que la aplicación sufra un error después de procesar el mensaje y antes de llamar al método **delete\_subscription\_message()**, entonces el mensaje se volverá a entregar a la aplicación cuando esta se reinicie. Habitualmente se denomina **At Least Once Processing**; es decir, cada mensaje se procesará al menos una vez; aunque en determinadas situaciones podría volver a entregarse el mismo mensaje. Si el escenario no puede tolerar el procesamiento duplicado, entonces los desarrolladores de la aplicación deberían agregar lógica adicional a su aplicación para solucionar la entrega de mensajes duplicados. A menudo, esto se consigue usando la propiedad **message\_id** del mensaje, que permanecerá constante en todos los intentos de entrega.

## <span id="how-to-delete-topics-and-subscriptions"></span></a>Eliminación de temas y suscripciones

Los temas y suscripciones son permanentes, por lo que deben eliminarse explícitamente a través del [Portal de administración de Azure][] o mediante programación. En el ejemplo siguiente se muestra cómo eliminar el tema llamado "test-topic".

    azure_service_bus_service.delete_topic("test-topic")

Al eliminar un tema también se eliminan todas las suscripciones que estén registradas con él. También se pueden eliminar las suscripciones de forma independiente. El siguiente código muestra cómo eliminar la suscripción con el nombre "high-messages" del tema "test-topic":

    azure_service_bus_service.delete_subscription("test-topic", "high-messages")

## <span id="NextSteps"></span></a>Pasos siguientes

Ahora que conoce los fundamentos de los temas del bus de servicio, siga estos vínculos para obtener más información.

-   Consulte la referencia de MSDN: [Colas, temas y suscripciones del Service Bus][]
-   Referencia de API para [Clase SqlFilter][].
-   Visite el repositorio del [SDK de Azure para Ruby][] (en inglés) en GitHub.

  [Pasos siguientes]: #NextSteps
  [Qué son las suscripciones y los temas del bus de servicio]: #what-are-service-bus-topics
  [Creación de un espacio de nombres de servicio]: #create-a-service-namespace
  [Obtención de credenciales de administración predeterminadas para el espacio de nombres]: #obtain-default-credentials
  [Creación de una aplicación de Ruby]: #create-a-ruby-application
  [Configuración de la aplicación para usar el bus de servicio]: #configure-your-application-to-use-service-bus
  [Configuración de una conexión del bus de servicio de Azure]: #setup-a-windows-azure-service-bus-connection
  [Creación de un tema]: #how-to-create-a-topic
  [Creación de suscripciones]: #how-to-create-subscriptions
  [Envío de mensajes a un tema]: #how-to-send-messages-to-a-topic
  [Recepción de mensajes de una suscripción]: #how-to-receive-messages-from-a-subscription
  [Actuación ante errores de la aplicación y mensajes que no se pueden leer]: #how-to-handle-application-crashes-and-unreadable-messages
  [Eliminación de temas y suscripciones]: #how-to-delete-topics-and-subscriptions
  [howto-service-bus-topics]: ../includes/howto-service-bus-topics.md
  [Creación de una aplicación de Ruby en Azure]: /es-es/develop/ruby/tutorials/web-app-with-linux-vm/
  [SqlFilter.SqlExpression]: http://msdn.microsoft.com/es-es/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx
  [Portal de administración de Azure]: https://manage.windowsazure.com
  [Colas, temas y suscripciones del Service Bus]: http://msdn.microsoft.com/es-es/library/windowsazure/hh367516.aspx
  [Clase SqlFilter]: http://msdn.microsoft.com/es-es/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.aspx
  [SDK de Azure para Ruby]: https://github.com/WindowsAzure/azure-sdk-for-ruby
