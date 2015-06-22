<properties
    pageTitle="Utilización de las colas del Bus de servicio (.NET) - Azure"
    description="Obtenga información acerca de cómo usar las colas del Bus de servicio en Azure. Ejemplos de código escritos en C# con la API de .NET."
    services="service-bus"
    documentationCenter=".net"
    authors="sethmanheim"
    manager="timlt"
    editor="mattshel"/>

<tags
    ms.service="service-bus"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="02/26/2015"
    ms.author="sethm"/>





# Utilización de las colas del Bus de servicio

<span>Esta guía describe cómo utilizar las colas del Bus de servicio. Los ejemplos están escritos en C# y usan la API de .NET. Los escenarios descritos incluyen la **creación de colas, el envío y la recepción de mensajes** y la **eliminación de colas**. Para obtener más información acerca de las colas, consulte la sección [Pasos siguientes]. </span>

[AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

[AZURE.INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

## Configuración de la aplicación para usar el bus de servicio

Cuando cree una aplicación que use el bus de servicio, deberá agregar una referencia al conjunto del bus de servicio e incluir los espacios de nombres correspondientes.

## Agregar el paquete NuGet del bus de servicio

El paquete **NuGet** del bus de servicio es la forma más sencilla de obtener la API del bus de servicio y configurar su aplicación con todas las dependencias del bus de servicio. La extensión NuGet Visual Studio facilita la instalación y la actualización de las bibliotecas y las herramientas en Visual Studio y Visual Studio Express. El paquete NuGet del bus de servicio es la forma más sencilla de obtener la API del bus de servicio y configurar su aplicación con todas las dependencias del bus de servicio.

Realice los pasos siguientes para instalar el paquete NuGet en su aplicación:

1.  En el Explorador de soluciones, haga clic con el botón secundario en **Referencias** y, a continuación, haga clic en **Administración de paquetes de NuGet**.
2.  Busque "Service Bus" y seleccione el elemento **Microsoft Azure Service Bus**. Haga clic en **Install** para completar la instalación y, luego, cierre este cuadro de diálogo.

    ![][7]

De este modo ya estará listo para escribir código para el bus de servicio.


## Configuración de una cadena de conexión del bus de servicio

El bus de servicio usa una cadena de conexión para almacenar extremos y credenciales. Puede poner la cadena de conexión en un archivo de configuración en vez de codificarla de forma rígida:

- Si utiliza los servicios en la nube de Azure, es recomendable que almacene la cadena de conexión mediante el sistema de configuración de servicios de Azure (archivos `*.csdef` and `*.cscfg`).
- Al usar Sitios web de Azure o Máquinas virtuales de Azure, es recomendable que almacene su cadena de conexión usando el sistema de configuración .NET (por ejemplo, el archivo  `web.config`).

En ambos casos, puede recuperar la cadena de conexión utilizando el método `CloudConfigurationManager.GetSetting`, como se muestra más adelante en esta guía.

### Configuración de la cadena de conexión si utiliza los servicios en la nube

El mecanismo de configuración de servicios es exclusivo para los proyectos de los servicios en la nube de Azure y le permite cambiar dinámicamente la configuración desde el Portal de administración de Azure sin volver a implementar la aplicación.  Por ejemplo, agregue una opción de configuración a su archivo de definición de servicio (`*.csdef`), como se indica a continuación:

    <ServiceDefinition name="Azure1">
    ...
        <WebRole name="MyRole" vmsize="Small">
            <ConfigurationSettings>
                <Setting name="Microsoft.ServiceBus.ConnectionString" />
            </ConfigurationSettings>
        </WebRole>
    ...
    </ServiceDefinition>

A continuación, especifique los valores del archivo de configuración de servicio (`*.cscfg`):

    <ServiceConfiguration serviceName="Azure1">
    ...
        <Role name="MyRole">
            <ConfigurationSettings>
                <Setting name="Microsoft.ServiceBus.ConnectionString"
                         value="Endpoint=sb://yourServiceNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey" />
            </ConfigurationSettings>
        </Role>
    ...
    </ServiceConfiguration>

Utilice el emisor y los valores de clave de la Firma de acceso compartido (SAS) recuperados del Portal de administración como se indica en la sección anterior.

### Configuración de la cadena de conexión al usar Sitios web o Máquinas virtuales

Al usar Sitios web o Máquinas virtuales, es recomendable que utilice el sistema de configuración .NET (por ejemplo,  `web.config`).  Almacene la cadena de conexión usando el elemento `<appSettings>`:

    <configuration>
        <appSettings>
            <add key="Microsoft.ServiceBus.ConnectionString"
                 value="Endpoint=sb://yourServiceNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey" />
        </appSettings>
    </configuration>

Utilice el emisor y los valores clave recuperados del Portal de administración como se indica en la sección anterior.

## Creación de una cola

Puede realizar operaciones de administración para las colas del bus de servicio a través de la clase **NamespaceManager**. La clase **NamespaceManager** proporciona métodos para crear, enumerar y eliminar colas.

En este ejemplo se construye un objeto **NamespaceManager** usando la clase **CloudConfigurationManager** de Azure con una cadena de conexión que consta de una dirección base de un espacio de nombres del bus de servicio y las credenciales SAS pertinentes con los permisos para administrarla. Esta cadena de conexión tiene la forma siguiente:

    Endpoint=sb://yourServiceNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedSecretValue=yourKey

Por ejemplo, con las opciones de configuración de la sección anterior:

    // Create the queue if it does not exist already
    string connectionString =
        CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

    var namespaceManager =
        NamespaceManager.CreateFromConnectionString(connectionString);

    if (!namespaceManager.QueueExists("TestQueue"))
    {
        namespaceManager.CreateQueue("TestQueue");
    }

Hay sobrecargas del método **CreateQueue** que le permiten ajustar las propiedades de la cola (por ejemplo, establecer el valor "período de vida" predeterminado para que se aplique a los mensajes enviados a la cola). Esta configuración se aplica mediante la clase **QueueDescription**. En el siguiente ejemplo se muestra cómo crear una cola con el nombre "TestQueue" con un tamaño máximo de 5 GB y un período de vida de mensaje predeterminado de un minuto:

    // Configure Queue Settings
    QueueDescription qd = new QueueDescription("TestQueue");
    qd.MaxSizeInMegabytes = 5120;
    qd.DefaultMessageTimeToLive = new TimeSpan(0, 1, 0);

    // Create a new Queue with custom settings
    string connectionString =
        CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

    var namespaceManager =
        NamespaceManager.CreateFromConnectionString(connectionString);

    if (!namespaceManager.QueueExists("TestQueue"))
    {
        namespaceManager.CreateQueue(qd);
    }

**Nota:** puede usar el método **QueueExists** en los objetos **NamespaceManager** para comprobar si ya existe una cola con un nombre especificado en un espacio de nombres de servicio.

## Envío de mensajes a una cola

Para enviar un mensaje a una cola del Bus de servicio, la aplicación crea un objeto **QueueClient** utilizando la cadena de conexión.

El código siguiente muestra cómo crear un objeto **QueueClient** para la cola "TestQueue" creada anteriormente con la llamada de la API **CreateFromConnectionString**:

    string connectionString =
        CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

    QueueClient Client =
        QueueClient.CreateFromConnectionString(connectionString, "TestQueue");

    Client.Send(new BrokeredMessage());

Los mensajes enviados a las colas del bus de servicio y recibidos en ellas son instancias de la clase **BrokeredMessage**. **Los objetos BrokeredMessage** cuentan con un conjunto de propiedades estándar (como **Label** y **TimeToLive**), un diccionario que se usa para mantener las propiedades personalizadas específicas de la aplicación y un conjunto de datos arbitrarios de aplicaciones. Una aplicación puede establecer el cuerpo del mensaje pasando todos los objetos serializables al constructor de **BrokeredMessage** y, a continuación, se usará el elemento **DataContractSerializer** adecuado para serializar el objeto. Como alternativa, se puede proporcionar un elemento **System.IO.Stream**.

En el siguiente ejemplo se demuestra cómo enviar cinco mensajes de prueba a "TestQueue" **QueueClient** que se ha obtenido en el fragmento de código anterior:

     for (int i=0; i<5; i++)
     {
       // Create message, passing a string message for the body
       BrokeredMessage message = new BrokeredMessage("Test message " + i);

       // Set some addtional custom app-specific properties
       message.Properties["TestProperty"] = "TestValue";
       message.Properties["Message number"] = i;

       // Send message to the queue
       Client.Send(message);
     }

Las colas del bus de servicio admiten un tamaño máximo del mensaje de 256 Kb (el encabezado, que incluye las propiedades estándar y personalizadas de la aplicación, puede tener un tamaño máximo de 64 Kb). No hay límite para el número de mensajes que contiene una cola, pero hay un tope para el tamaño total de los mensajes contenidos en una cola. El tamaño de la cola se define en el momento de la creación, con un límite de 5 GB.

## Recepción de mensajes de una cola

La forma más sencilla de recibir mensajes desde una cola es usar un objeto **QueueClient**. Estos objetos pueden funcionar de dos formas distintas: **ReceiveAndDelete** y **PeekLock**.

Al usar el modo **ReceiveAndDelete**, la operación de recepción consta de una sola fase; es decir, cuando el bus de servicio recibe una solicitud de lectura para un mensaje de una cola, marca el mensaje como consumido y lo devuelve a la aplicación. El modo **ReceiveAndDelete** es el modelo más sencillo y funciona mejor para los escenarios en los que una aplicación puede tolerar no procesar un mensaje en caso de error. Para entenderlo mejor, pongamos una situación en la que un consumidor emite la solicitud de recepción que se bloquea antes de procesarla. Como el Bus de servicio habrá marcado el mensaje como consumido, cuando la aplicación se reinicie y empiece a consumir mensajes de nuevo, habrá perdido el mensaje que se consumió antes del bloqueo.

En el modo **PeekLock** (que es el predeterminado), el proceso de recepción es una operación en dos fases que hace posible admitir aplicaciones que no toleran la pérdida de mensajes. Cuando el Bus de servicio recibe una solicitud, busca el siguiente mensaje que se va a consumir, lo bloquea para impedir que otros consumidores lo reciban y, a continuación, lo devuelve a la aplicación. Una vez que la aplicación termina de procesar el mensaje (o lo almacena de forma fiable para su futuro procesamiento), completa la segunda fase del proceso de recepción creando la llamada **Complete** en el mensaje recibido. Cuando el Bus de servicio ve la llamada **Complete**, marca el mensaje como consumido y lo elimina de la cola.

En el ejemplo que aparece a continuación, se indica cómo se pueden procesar y recibir los mensajes usando el modo predeterminado **PeekLock**. Para especificar otro valor **ReceiveMode**, puede usar otra sobrecarga  **CreateFromConnectionString**. Este ejemplo usa la devolución de llamada **OnMessage** para procesar los mensajes conforme llegan a "TestQueue".

    string connectionString =
      CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");
    QueueClient Client =
      QueueClient.CreateFromConnectionString(connectionString, "TestQueue");

    // Configure the callback options
    OnMessageOptions options = new OnMessageOptions();
    options.AutoComplete = false;
    options.AutoRenewTimeout = TimeSpan.FromMinutes(1);

    // Callback to handle received messages
    Client.OnMessage((message) =>
    {
        try
        {
            // Process message from queue
            Console.WriteLine("Body: " + message.GetBody<string>());
            Console.WriteLine("MessageID: " + message.MessageId);
            Console.WriteLine("Test Property: " +
            message.Properties["TestProperty"]);

            // Remove message from queue
            message.Complete();
        }
            catch (Exception)
        {
            // Indicates a problem, unlock message in queue
            message.Abandon();
        }
    }, options);

Este ejemplo configura la devolución de llamada **OnMessage** utilizando un objeto **OnMessageOptions**. **Autocompletar** se establece en **false** para habilitar el control manual de cuándo llamar a **Complete** en el mensaje recibido.
**AutoRenewTimeout** se establece en un minuto, lo que hace que el cliente espere un minuto un mensaje antes de que se agote el tiempo de espera de la llamada y el cliente realiza una nueva llamada para comprobar los mensajes; de esta manera se reduce el número de veces que el cliente realiza llamadas facturables que no recuperan mensajes.

## Actuación ante errores de la aplicación y mensajes que no se pueden leer

El Bus de servicio proporciona una funcionalidad que le ayuda a superar sin problemas los errores de la aplicación o las dificultades para procesar un mensaje. Si por cualquier motivo una aplicación de recepción no puede procesar el mensaje, entonces realice la llamada al método **Abandon** del mensaje recibido (en lugar del método **Complete**). Esto hará que el Bus de servicio desbloquee el mensaje de la cola y esté disponible para que pueda volver a recibirse, ya sea por la misma aplicación que lo consume o por otra.

También hay un tiempo de espera asociado con un mensaje bloqueado en la cola, y si la aplicación no puede procesar el mensaje antes de que expire el tiempo de espera de bloqueo (por ejemplo, si la aplicación sufre un error), el Bus de servicio desbloquea el mensaje automáticamente y hace que esté disponible para que pueda volver a recibirse.

En caso de que la aplicación se bloquee después de procesar el mensaje y antes de emitir la solicitud **Complete**, entonces el mensaje se volverá a entregar a la aplicación cuando esta se reinicie. Esto suele denominarse **At Least Once Processing**; es decir, cada mensaje se procesará al menos una vez; aunque en determinadas situaciones podría volver a entregarse el mismo mensaje. Si el escenario no puede tolerar el procesamiento duplicado, entonces los desarrolladores de la aplicación deberían agregar lógica adicional a su aplicación para solucionar la entrega de mensajes duplicados. A menudo esto se consigue mediante la propiedad **MessageId** del mensaje, que permanecerá constante en todos los intentos de entrega.

## Pasos siguientes

Ahora que conoce los fundamentos de las colas del Bus de servicio, siga estos vínculos para obtener más información.

-   Consulte la referencia de MSDN: [Colas, temas y suscripciones del bus de servicio][].
-   Creación de una aplicación que envíe y reciba mensajes desde la cola del bus de servicio y hacia ella: [Tutorial de .NET de mensajería con confianza establecida de servicio][Tutorial de .NET de mensajería con confianza establecida del bus de servicio].

  [Pasos siguientes]: #next-steps
  [¿Qué son las colas del bus de servicio?]: #what-queues
  [Creación de un espacio de nombres de servicio]: #create-namespace
  [Obtención de credenciales de administración predeterminadas para el espacio de nombres]: #obtain-creds
  [Configuración de la aplicación para usar el Bus de servicio]: #configure-app
  [Codificación una cadena de conexión del bus de servicio]: #set-up-connstring
  [Codificación la cadena de conexión]: #config-connstring
  [Codificación de una cola]: #create-queue
  [Codificación mensajes a una cola]: #send-messages
  [Codificación mensajes desde una cola]: #receive-messages
  [Codificación ante errores de la aplicación y mensajes que no se pueden leer]: #handle-crashes
  [Portal de administración de Azure]: http://manage.windowsazure.com
  [7]: ./media/service-bus-dotnet-how-to-use-queues/getting-started-multi-tier-13.png
  [Colas, temas y suscripciones del bus de servicio]: http://msdn.microsoft.com/library/hh367516.aspx
  [Tutorial de .NET de mensajería con confianza establecida del bus de servicio]: http://msdn.microsoft.com/library/hh367512.aspx

<!--HONumber=47-->
 