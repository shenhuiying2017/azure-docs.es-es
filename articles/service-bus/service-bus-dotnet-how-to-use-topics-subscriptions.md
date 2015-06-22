<properties 
    pageTitle="Cómo usar temas de Service Bus (.NET) - Azure" 
    description="Aprenda a usar los temas y suscripciones de Bus de servicio de Azure. Los ejemplos de código están escritos para aplicaciones .NET." 
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





# Uso de temas/suscripciones del Bus de servicio

En esta guía se indica cómo usar los temas y las suscripciones de Bus de servicio. Los ejemplos están escritos en C# y usan la API de .NET. Entre los escenarios tratados se incluyen **la creación de temas y suscripciones, la creación de filtros de suscripción, el envío de mensajes a un tema**, **la recepción de mensajes de una suscripción** y **la eliminación de temas y suscripciones**. Para obtener más información acerca de los temas y las suscripciones, consulte la sección [Pasos siguientes][].

[AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

[AZURE.INCLUDE [howto-service-bus-topics](../../includes/howto-service-bus-topics.md)]

## Configuración de la aplicación para usar el bus de servicio

Cuando cree una aplicación que usa el bus de servicio, tendrá que agregar una referencia al ensamblado del bus de servicio e incluir los espacios de nombres correspondientes.

## Obtenga el paquete NuGet del bus de servicio

El paquete de **NuGet** de bus de servicio es la forma más fácil de obtener la API de bus de servicio y configurar la aplicación con todas las dependencias del bus de servicio. La extensión NuGet Visual Studio facilita la instalación y la actualización de las bibliotecas y las herramientas en Visual Studio y Visual Studio Express. El paquete NuGet del bus de servicio es la forma más sencilla de obtener la API del bus de servicio y configurar su aplicación con todas las dependencias del bus de servicio.

Realice los pasos siguientes para instalar el paquete NuGet en su aplicación:

1.  En el Explorador de soluciones, haga clic con el botón secundario en **Referencias** y, a continuación, haga clic en **Administración de paquetes de NuGet**.
2.  Busque "Bus de servicio" y seleccione el elemento **Microsoft Azure Service Bus**. Haga clic en **Instalar** para completar la instalación y, a continuación, cierre este cuadro de diálogo.

    ![][7]

De este modo ya estará listo para escribir código para el bus de servicio.

## Configuración de una cadena de conexión del bus de servicio

El bus de servicio usa una cadena de conexión para almacenar extremos y credenciales. Puede poner la cadena de conexión en un archivo de configuración en vez de codificarla de forma rígida:

- Si utiliza los servicios en la nube de Azure, es recomendable que almacene la cadena de conexión mediante el sistema de configuración de servicios de Azure (archivos `*.csdef` and `*.cscfg`).
- Al usar Sitios web de Azure o Máquinas virtuales de Azure, es recomendable que almacene su cadena de conexión usando el sistema de configuración .NET (por ejemplo, el archivo  `web.config`).

En ambos casos, puede recuperar la cadena de conexión utilizando el método `CloudConfigurationManager.GetSetting`, como se muestra más adelante en esta guía.

### Configuración de la cadena de conexión si utiliza los servicios en la nube

El mecanismo de configuración de servicios es exclusivo para los proyectos de los servicios en la nube de Azure y le permite cambiar dinámicamente la configuración desde el portal de administración de Azure sin volver a implementar la aplicación.  Por ejemplo, agregue una opción de configuración a su archivo de definición de servicio (`*.csdef`), como se indica a continuación:

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

Utilice los valores claves recuperados del portal de administración como se describe en la sección anterior.

### Configuración de la cadena de conexión al usar Sitios web o Máquinas virtuales

Al usar Sitios web o Máquinas virtuales, es recomendable que utilice el sistema de configuración .NET (por ejemplo,  `web.config`).  Almacene la cadena de conexión usando el elemento `<appSettings>`:

    <configuration>
        <appSettings>
            <add key="Microsoft.ServiceBus.ConnectionString"
                 value="Endpoint=sb://yourServiceNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey" />
        </appSettings>
    </configuration>

Utilice el emisor y los valores clave recuperados del portal de administración como se indica en la sección anterior.

## Creación de un tema

Puede realizar operaciones de administración en los temas y las suscripciones del bus de servicio a través de la clase **NamespaceManager**. La clase **NamespaceManager** proporciona métodos para crear, enumerar y eliminar colas. 

En este ejemplo, se construye un objeto **NamespaceManager** usando la clase **CloudConfigurationManager** de Azure con una cadena de conexión que consta de una dirección base de un espacio de nombres del bus de servicio y las credenciales pertinentes con los permisos para administrarla. Esta cadena de conexión tiene la forma siguiente:

    Endpoint=sb://<yourServiceNamespace>.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey

Por ejemplo, con las opciones de configuración de la sección anterior:

    // Create the topic if it does not exist already
    string connectionString = 
        CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

    var namespaceManager = 
        NamespaceManager.CreateFromConnectionString(connectionString);

    if (!namespaceManager.TopicExists("TestTopic"))
    {
        namespaceManager.CreateTopic("TestTopic");
    }

Hay sobrecargas del método **CreateTopic** que le permiten ajustar las propiedades del tema, por ejemplo, para configurar el valor predeterminado "time-to-live" a fin de que se aplique a los mensajes enviados al tema. Estas opciones de configuración se aplican usando la clase **TopicDescription**. En el ejemplo siguiente se indica cómo crear un tema llamado "TestTopic" con un tamaño máximo de 5 GB y un período de vida de mensaje predeterminado de un minuto.

    // Configure Topic Settings
    TopicDescription td = new TopicDescription("TestTopic");
    td.MaxSizeInMegabytes = 5120;
    td.DefaultMessageTimeToLive = new TimeSpan(0, 1, 0);

    // Create a new Topic with custom settings
    string connectionString = 
        CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

    var namespaceManager = 
        NamespaceManager.CreateFromConnectionString(connectionString);

    if (!namespaceManager.TopicExists("TestTopic"))
    {
        namespaceManager.CreateTopic(td);
    }

**Nota:** puede usar el método **TopicExists** en los objetos **NamespaceManager** para comprobar si ya existe un tema con un nombre especificado en un espacio de nombres de servicio.

<h2>Creación de suscripciones</h2>

También puede crear suscripciones de temas con la clase **NamespaceManager**. A las suscripciones se les puede asignar un nombre y pueden tener un filtro opcional que restrinja el conjunto de mensajes que pasan a la cola virtual de la suscripción.

### Creación de una suscripción con el filtro predeterminado (MatchAll)

El filtro predeterminado **MatchAll** se usa en caso de que no se haya especificado ninguno al crear una suscripción. Al usar el filtro **MatchAll**, todos los mensajes publicados en el tema se colocan en la cola virtual de la suscripción. En el ejemplo siguiente se crea una suscripción llamada "AllMessages" que usa el filtro predeterminado **MatchAll**.

    string connectionString = 
        CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

    var namespaceManager = 
        NamespaceManager.CreateFromConnectionString(connectionString);

    if (!namespaceManager.SubscriptionExists("TestTopic", "AllMessages"))
    {
        namespaceManager.CreateSubscription("TestTopic", "AllMessages");
    }

### Creación de suscripciones con filtros

También puede configurar filtros que le permitan especificar qué mensajes enviados a un tema deben aparecer dentro de una suscripción de tema determinada.

El tipo más flexible de filtro compatible con las suscripciones es **SqlFilter**, que implementa un subconjunto de SQL92. Los filtros de SQL operan en las propiedades de los mensajes que se publican en el tema. Para obtener más información acerca de las expresiones que se pueden usar con un filtro de SQL, revise la sintaxis de [SqlFilter.SqlExpression][].

El ejemplo siguiente crea una suscripción llamada "HighMessages" con un filtro **SqlFilter** que solo selecciona los mensajes con una propiedad **MessageNumber** personalizada superior a 3:

     // Create a "HighMessages" filtered subscription
     SqlFilter highMessagesFilter = 
        new SqlFilter("MessageNumber > 3");

     namespaceManager.CreateSubscription("TestTopic", 
        "HighMessages", 
        highMessagesFilter);

Del mismo modo, en el ejemplo que aparece a continuación se crea una suscripción denominada "LowMessages" con un filtro **SqlFilter** que solo selecciona los mensajes que tienen una propiedad **MessageNumber** inferior o igual a 3:

     // Create a "LowMessages" filtered subscription
     SqlFilter lowMessagesFilter = 
        new SqlFilter("MessageNumber <= 3");

     namespaceManager.CreateSubscription("TestTopic", 
        "LowMessages", 
        lowMessagesFilter);

Ahora, cuando se envíe un mensaje a "TestTopic", este se entregará siempre a los destinatarios suscritos al tema "AllMessages" y se entregará de forma selectiva a los destinatarios suscritos a los temas "HighMessages" y "LowMessages" (según el contenido del mensaje).

## Envío de mensajes a un tema

Para enviar un mensaje a un tema de Bus de servicio, la aplicación crea un objeto **TopicClient** utilizando la cadena de conexión.

El código que aparece a continuación indica cómo crear un objeto **TopicClient** para el tema "TestTopic" creado anteriormente usando la llamada de la API **CreateFromConnectionString**:

    string connectionString = 
        CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

    TopicClient Client = 
        TopicClient.CreateFromConnectionString(connectionString, "TestTopic");

    Client.Send(new BrokeredMessage());
 

Los mensajes enviados a los temas de Bus de servicio son instancias de la clase **BrokeredMessage**. Los objetos **BrokeredMessage** cuentan con un conjunto de propiedades estándar (como **Label** y **TimeToLive**), un diccionario que se usa para mantener las propiedades personalizadas específicas de la aplicación y un conjunto de datos arbitrarios de aplicaciones. Una aplicación puede establecer el cuerpo del mensaje pasando todos los objetos serializables al constructor de **BrokeredMessage** y, a continuación, se usará el elemento **DataContractSerializer** adecuado para serializar el objeto. Como alternativa, se puede proporcionar un elemento **System.IO.Stream**.

En el siguiente ejemplo se demuestra cómo enviar cinco mensajes de prueba a "TestTopic" **TopicClient** que se ha obtenido en el fragmento de código anterior.
Observe cómo el valor de la propiedad **MessageNumber** de cada mensaje varía en función de la iteración del bucle (así se determinará qué suscripción lo recibe):

     for (int i=0; i<5; i++)
     {
       // Create message, passing a string message for the body
       BrokeredMessage message = 
        new BrokeredMessage("Test message " + i);

       // Set additional custom app-specific property
       message.Properties["MessageNumber"] = i;

       // Send message to the topic
       Client.Send(message);
     }

Los temas de bus de servicio admiten un tamaño máximo del mensaje de 256 Kb (el encabezado, que incluye las propiedades estándar y personalizadas de la aplicación, puede tener un tamaño máximo de 64 Kb). No hay límite para el número de mensajes que contiene un tema, pero hay un tope para el tamaño total de los mensajes contenidos en un tema. El tamaño de la cola se define en el momento de la creación, con un límite de 5 GB.

## Recepción de mensajes de una suscripción

La manera recomendada de recibir mensajes de una suscripción es usar un objeto **SubscriptionClient**. Los objetos **SubscriptionClient** pueden funcionar en dos modos diferentes: **ReceiveAndDelete** y **PeekLock**.

Al usar el modo **ReceiveAndDelete**, la operación de recepción consta de una sola fase; es decir, cuando el bus de servicio recibe una solicitud de lectura para un mensaje en una suscripción, marca el mensaje como consumido y lo devuelve a la aplicación. **El modo ReceiveAndDelete** es el modelo más sencillo y funciona mejor para los escenarios en los que una aplicación puede tolerar no procesar un mensaje en caso de error. Para entenderlo mejor, pongamos una situación en la que un consumidor emite la solicitud de recepción que se bloquea antes de procesarla. Como el bus de servicio habrá marcado el mensaje como consumido, cuando la aplicación se reinicie y empiece a consumir mensajes de nuevo, habrá perdido el mensaje que se consumió antes del bloqueo.

En el modo **PeekLock** (que es el predeterminado), el proceso de recepción es una operación en dos fases que hace posible admitir aplicaciones que no toleran la pérdida de mensajes. Cuando el Bus de servicio recibe una solicitud, busca el siguiente mensaje que se va a consumir, lo bloquea para impedir que otros consumidores lo reciban y, a continuación, lo devuelve a la aplicación. Una vez que la aplicación termina de procesar el mensaje (o lo almacena de forma fiable para su futuro procesamiento), completa la segunda fase del proceso de recepción creando la llamada **Complete** en el mensaje recibido. Cuando el Bus de servicio ve la llamada **Complete**, marca el mensaje como consumido y lo elimina de la suscripción.

En el ejemplo siguiente se muestra cómo se pueden recibir y procesar mensajes con el modo **PeekLock** predeterminado. Para especificar un valor de **ReceiveMode** diferente, puede usar otra sobrecarga para **CreateFromConnectionString**. Este ejemplo utiliza la devolución de llamada **OnMessage** para procesar mensajes a medida que llegan en la suscripción "HighMessages".

    string connectionString = 
        CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

    SubscriptionClient Client = 
        SubscriptionClient.CreateFromConnectionString
                (connectionString, "TestTopic", "HighMessages");

    // Configure the callback options
    OnMessageOptions options = new OnMessageOptions();
    options.AutoComplete = false;
    options.AutoRenewTimeout = TimeSpan.FromMinutes(1);

    subscriptionClientHigh.OnMessage((message) =>
    {
        try
        {
            // Process message from subscription
            Console.WriteLine("\n**High Messages**");
            Console.WriteLine("Body: " + message.GetBody<string>());
            Console.WriteLine("MessageID: " + message.MessageId);
            Console.WriteLine("Message Number: " +
                message.Properties["MessageNumber"]);

            // Remove message from subscription
            message.Complete();
        }
        catch (Exception)
        {
            // Indicates a problem, unlock message in subscription
            message.Abandon();
        }
    }, options);

Este ejemplo configura la devolución de llamada **OnMessage** utilizando un objeto **OnMessageOptions**. **Autocompletar** se establece en **false** para habilitar  el control manual de cuándo llamar a **Complete** en el mensaje recibido.
**AutoRenewTimeout** se establece en un minuto, lo que hace que el cliente espere un minuto un mensaje antes de que se agote el tiempo de espera de la llamada y el cliente realiza una nueva llamada para comprobar si hay mensajes, con lo que se reduce el número de veces que el cliente realiza llamadas facturables que no se recuperan los mensajes.

## Actuación ante errores de la aplicación y mensajes que no se pueden leer

El Bus de servicio proporciona una funcionalidad que le ayuda a superar sin problemas los errores de la aplicación o las dificultades para procesar un mensaje. Si por cualquier motivo una aplicación de recepción no puede procesar el mensaje, entonces realice la llamada al método **Abandon** del mensaje recibido (en lugar del método **Complete**). Esto hará que el bus de servicio desbloquee el mensaje de la suscripción y esté disponible para que pueda volver a recibirse, ya sea por la misma aplicación que lo consume o por otra.

También hay otro tiempo de espera asociado con un mensaje bloqueado en la suscripción y, si la aplicación no puede procesar el mensaje antes de que finalice el tiempo de espera del bloqueo (por ejemplo, si la aplicación se bloquea), entonces el bus de servicio desbloquea en mensaje automáticamente y hace que esté disponible para que pueda volver a recibirse.

En caso de que la aplicación se bloquee después de procesar el mensaje y antes de emitir la solicitud **Complete**, entonces el mensaje se volverá a entregar a la aplicación cuando esta se reinicie. Habitualmente se denomina **At Least Once Processing**; es decir, cada mensaje se procesará al menos una vez; aunque en determinadas situaciones podría volver a entregarse el mismo mensaje. Si el escenario no puede tolerar el procesamiento duplicado, entonces los desarrolladores de la aplicación deberían agregar lógica adicional a su aplicación para solucionar la entrega de mensajes duplicados. A menudo, esto se consigue usando la propiedad **MessageId** del mensaje, que permanecerá constante en todos los intentos de entrega.

## Eliminación de temas y suscripciones

En el ejemplo de abajo se indica cómo eliminar el tema llamado **TestTopic** del espacio de nombres de servicio **HowToSample**:

     // Delete Topic
     namespaceManager.DeleteTopic("TestTopic");

Al eliminar un tema también se eliminan todas las suscripciones que estén registradas con él. También se pueden eliminar las suscripciones de forma independiente. El código siguiente muestra cómo eliminar una suscripción llamada **HighMessages** desde el tema **TestTopic**:

      namespaceManager.DeleteSubscription("TestTopic", "HighMessages");

## Pasos siguientes

Ahora que conoce los fundamentos de los temas y las suscripciones del bus de servicio, siga estos vínculos para obtener más información.

-   Consulte la referencia de MSDN: [Colas, temas y suscripciones del bus de servicio][].
-   Referencia de API para [Clase SqlFilter][SqlFilter].
-   Creación de una aplicación que envíe y reciba mensajes desde la cola del bus de servicio y hacia ella: [Tutorial de .NET de mensajería con confianza establecida de servicio][Tutorial de .NET de mensajería con confianza establecida del bus de servicio].

  [Pasos siguientes]: #nextsteps
  [Qué son las suscripciones y los temas del Bus de servicio]: #what-is
  [Creación de un espacio de nombres de servicio]: #create-namespace
  [Obtención de credenciales de administración predeterminadas para el espacio de nombres]: #obtain-creds
  [Configuración de la aplicación para usar el Bus de servicio]: #configure-app
  [Codificación una cadena de conexión del bus de servicio]: #set-up-connstring
  [Codificación la cadena de conexión]: #config-connstring
  [Codificación de un tema]: #create-topic
  [Codificación suscripciones]: #create-subscriptions
  [Codificación mensajes a un tema]: #send-messages
  [Codificación mensajes de una suscripción]: #receive-messages
  [Codificación ante errores de la aplicación y mensajes que no se pueden leer]: #handle-crashes
  [Codificación temas y suscripciones]: #delete-topics
  
  [Conceptos de tema]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/sb-topics-01.png
  [Portal de administración de Azure]: http://manage.windowsazure.com
  
  
  
  
  
  
  [7]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/getting-started-multi-tier-13.png
  
  [Colas, temas y suscripciones del Bus de servicio]: http://msdn.microsoft.com/library/hh367516.aspx
  [SqlFilter]: http://msdn.microsoft.com/library/microsoft.servicebus.messaging.sqlfilter.aspx
  [Tutorial de .NET de mensajería con confianza establecida del bus de servicio]: http://msdn.microsoft.com/library/hh367512.aspx

<!--HONumber=47-->
 