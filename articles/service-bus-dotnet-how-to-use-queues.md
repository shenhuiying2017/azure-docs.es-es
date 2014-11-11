<properties linkid="dev-net-how-to-service-bus-queues" urlDisplayName="Service Bus Queues" pageTitle="How to use Service Bus queues (.NET) - Azure" metaKeywords="Azure Service Bus queues, Azure queues, Azure messaging, Azure queues C#, Azure queues .NET" description="Learn how to use Service Bus queues in Azure. Code samples written in C# using the .NET API." metaCanonical="" services="service-bus" documentationCenter=".NET" title="How to Use Service Bus Queues" authors="sethm" solutions="" manager="timlt" editor="mattshel" />

<tags ms.service="service-bus" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="09/24/2014" ms.author="sethm" />

# Utilización de las colas del bus de servicio

<span>Esta guía le mostrará cómo usar las colas del bus de servicio. Los
ejemplos están escritos en C# y utilizan la API de .NET. Entre los escenarios proporcionados se
incluyen los siguientes: **creación de colas, envío y recepción de mensajes** y
**eliminación de colas**. Para obtener más información acerca de las colas, consulte la sección [Pasos siguientes][Pasos siguientes]. </span>

[WACOM.INCLUDE [create-account-note](../includes/create-account-note.md)]

[WACOM.INCLUDE [howto-service-bus-queues](../includes/howto-service-bus-queues.md)]

## <span class="short-header">Configuración de la aplicación</span>Configuración de la aplicación para usar el bus de servicio

Cuando cree una aplicación que use el bus de servicio,
deberá agregar una referencia al conjunto del bus de servicio e incluir los
espacios de nombres correspondientes.

## <span class="short-header">Obtención del paquete NuGet</span>Obtención del paquete NuGet del bus de servicio

El paquete **NuGet** del bus de servicio es la forma más sencilla de obtener
la API del bus de servicio y configurar su aplicación con todas
las dependencias del bus de servicio. La extensión NuGet Visual Studio
facilita la instalación y la actualización de las bibliotecas y las herramientas en Visual Studio y
Visual Studio Express 2012 para Web.

Realice los pasos siguientes para instalar el paquete NuGet en su aplicación:

1.  En el Explorador de soluciones, haga clic con el botón secundario en **References** y, a continuación, en
    **Manage NuGet Packages**.
2.  Busque WindowsAzure" y seleccione el elemento **Azure
    Servicie Bus**. Haga clic en **Instaló** para completar la instalación y,
    a continuación, cierre este cuadro de diálogo.

    ![][0]

De este modo ya estará listo para escribir código en el bus de servicio.

## <span class="short-header">Configuración de la cadena de conexión</span>Configuración de una cadena de conexión del bus de servicio

El bus de servicio usa una cadena de conexión para almacenar extremos y credenciales. Puede poner la cadena de conexión en un archivo de configuración en vez de codificarla de forma rígida:

-   Si utiliza los servicios en la nube de Azure, es recomendable que almacene la cadena de conexión mediante el sistema de configuración de servicios de Azure (archivos `*.csdef` and `*.cscfg`).
-   Al usar Sitios web Azure o Máquinas virtuales de Azure, es recomendable que almacene su cadena de conexión usando el sistema de configuración .NET (por ejemplo, el archivo `web.config`).

En ambos casos, puede recuperar la cadena de conexión utilizando el método `CloudConfigurationManager.GetSetting`, como se muestra más adelante en esta guía.

### <a name="config-connstring"> </a>Configuración de la cadena de conexión si utiliza los servicios en la nube

El mecanismo de configuración de servicios es exclusivo para los
proyectos de los servicios en la nube de Azure y le permite cambiar dinámicamente la configuración
desde el Portal de administración de Azure sin volver a implementar
la aplicación. Por ejemplo, agregue una opción de configuración a su archivo de definición de servicio (`*.csdef`), como se indica a continuación:

    <ServiceDefinition name="WindowsAzure1">
    ...
        <WebRole name="MyRole" vmsize="Small">
            <ConfigurationSettings>
                <Setting name="Microsoft.ServiceBus.ConnectionString" />
            </ConfigurationSettings>
        </WebRole>
    ...
    </ServiceDefinition>

A continuación, especifique los valores del archivo de configuración de servicio (`*.cscfg`):

    <ServiceConfiguration serviceName="WindowsAzure1">
    ...
        <Role name="MyRole">
            <ConfigurationSettings>
                <Setting name="Microsoft.ServiceBus.ConnectionString" 
                         value="Endpoint=sb://[yourServiceNamespace].servicebus.windows.net/;SharedSecretIssuer=[issuerName];SharedSecretValue=[yourDefaultKey]" />
            </ConfigurationSettings>
        </Role>
    ...
    </ServiceConfiguration>

Utilice el emisor y los valores clave recuperados del Portal de administración como
se indica en la sección anterior.

### Configuración de la cadena de conexión al usar Sitios web o Máquinas virtuales

Al usar Sitios web o Máquinas virtuales, es recomendable que utilice el sistema de configuración .NET (por ejemplo, `web.config`). Almacene la cadena de conexión con el elemento `<appSettings>`:

    <configuration>
        <appSettings>
            <add key="Microsoft.ServiceBus.ConnectionString"
                 value="Endpoint=sb://[yourServiceNamespace].servicebus.windows.net/;SharedSecretIssuer=[issuerName];SharedSecretValue=[yourDefaultKey]" />
        </appSettings>
    </configuration>

Utilice el emisor y los valores clave recuperados del Portal de administración como
se indica en la sección anterior.

## <span class="short-header">Creación de una cola</span>Creación de una cola

Puede realizar operaciones de administración para las colas del bus de servicio a través de la clase **NamespaceManager**. La clase **NamespaceManager** proporciona métodos para crear, enumerar y eliminar colas.

En este ejemplo, se construye un objeto **NamespaceManager** usando la clase **CloudConfigurationManager** de Azure
con una cadena de conexión que consta de una dirección base de un espacio de nombres del bus de servicio y
las credenciales pertinentes con los permisos para administrarla. Esta cadena de conexión tiene la forma siguiente:

    Endpoint=sb://[yourServiceNamespace].servicebus.windows.net/;SharedSecretIssuer=[issuerName];SharedSecretValue=[yourDefaultKey]

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

Hay sobrecargas del método **CreateQueue** que le permiten ajustar las propiedades
de la cola (por ejemplo, establecer el valor "período de vida" predeterminado para que se aplique a los mensajes enviados a la cola). Esta
configuración se aplica mediante la clase **QueueDescription**. En el
siguiente ejemplo se muestra cómo crear una cola con el nombre "TestQueue" con un tamaño
máximo de 5 GB y un período de vida de mensaje predeterminado de 1 minuto:

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

**Nota:** Puede usar el método **QueueExists** en los objetos **NamespaceManager**
 para comprobar si ya existe una cola con un nombre especificado en
un espacio de nombres de servicio.

## <span class="short-header">Envío de mensajes a la cola</span>Envío de mensajes a la cola

Para enviar un mensaje a una cola del bus de servicio, la aplicación crea un objeto
**QueueClient** mediante la cadena de conexión.

El código siguiente muestra cómo crear un objeto **QueueClient** para
la cola "TestQueue" creada anteriormente con la llamada de la API **CreateFromConnectionString**:

    string connectionString = 
        CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

    QueueClient Client = 
        QueueClient.CreateFromConnectionString(connectionString, "TestQueue");

    Client.Send(new BrokeredMessage());

Los mensajes enviados a las colas del bus de servicio y recibidos en ellas son instancias de
la clase **BrokeredMessage**. Los objetos **BrokeredMessage** cuentan con un conjunto de propiedades
estándar (como **Label** y **TimeToLive**), un diccionario
que se usa para mantener las propiedades personalizadas específicas de la aplicación y un conjunto de datos
arbitrarios de aplicaciones. Una aplicación puede configurar el cuerpo del
mensaje pasando todos los objetos serializables al constructor de
**BrokeredMessage** y, a continuación, se usará el **DataContractSerializer** adecuado
para serializar el objeto. También se puede proporcionar un
**System.IO.Stream**.

En el ejemplo que aparece a continuación se indica cómo enviar cinco mensajes de
prueba al "TestQueue" **QueueClient** que se ha obtenido en el fragmento de código anterior:

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

Las colas del bus de servicio admiten mensajes con un tamaño máximo de 256 Kb (el encabezado,
que incluye las propiedades estándar y personalizadas de la aplicación, puede tener como máximo un
tamaño de 64 Kb). No hay límite para el número de mensajes que
contiene una cola, pero hay un tope para el tamaño total de los mensajes
contenidos en una cola. El tamaño de la cola se define en el momento de la creación, con un
límite de 5 GB.

## <span class="short-header">Recepción de mensajes desde una cola</span>Recepción de mensajes desde una cola

La forma más sencilla de recibir mensajes desde una cola es usar un objeto
**QueueClient**. Estos objetos pueden funcionar
de dos formas distintas: **ReceiveAndDelete** y **PeekLock**.

Al usar el modo **ReceiveAndDelete**, la operación de recepción consta
de una sola fase; es decir, cuando el bus de servicio recibe una solicitud
de lectura para un mensaje de una cola, marca el mensaje como consumido y
lo devuelve a la aplicación. El modo **ReceiveAndDelete** es el modelo más sencillo
y funciona mejor para los escenarios en los que una aplicación puede tolerar
no procesar un mensaje en caso de error. Para entenderlo mejor,
pongamos una situación en la que un consumidor emite la solicitud de recepción
que se bloquea antes de procesarla. Como el bus de servicio habrá marcado
el mensaje como consumido, cuando la aplicación se reinicie y
empiece a consumir mensajes de nuevo, habrá perdido el mensaje que se
consumió antes del bloqueo.

En el modo **PeekLock** (que es el predeterminado), el proceso de recepción es una operación en dos fases que hace posible admitir aplicaciones
que no toleran la pérdida de mensajes. Cuando un bus de servicio recibe una solicitud,
busca el siguiente mensaje que se va a consumir, lo bloquea para impedir que otros
consumidores lo reciban y, a continuación, lo devuelve a la aplicación. Una vez que
la aplicación termina de procesar el mensaje (o lo almacena de forma fiable
para su futuro procesamiento), completa la segunda fase del
proceso de recepción creando la llamada **Complete** en el mensaje recibido. Cuando el bus
de servicio ve la llamada **Complete**, marca el mensaje como
consumido y lo elimina de la cola.

En el ejemplo que aparece a continuación, se indica cómo se pueden
procesar y recibir los mensajes usando el modo predeterminado **PeekLock**. Para especificar un valor **ReceiveMode** diferente, puede usar otra sobrecarga para **CreateFromConnectionString**. En este ejemplo se crea un bucle infinito y se procesan mensajes a medida que llegan a "TestQueue":

    Client.Receive();
     
    // Continuously process messages sent to the "TestQueue" 
    while (true) 
    {  
       BrokeredMessage message = Client.Receive();

       if (message != null)
       {
          try 
          {
             Console.WriteLine("Body: " + message.GetBody<string>());
             Console.WriteLine("MessageID: " + message.MessageId);
             Console.WriteLine("Test Property: " + 
                message.Properties["TestProperty"]);

             // Remove message from queue
             message.Complete();
          }
          catch (Exception)
          {
             // Indicate a problem, unlock message in queue
             message.Abandon();
          }
       }
    } 

## <span class="short-header">Bloqueos de la aplicación y mensajes ilegibles</span>Administración de bloqueos de aplicaciones y mensajes ilegibles

El bus de servicio proporciona la funcionalidad que le ayuda a recuperarse correctamente de
los errores de la aplicación o de las dificultades para procesar un mensaje. Si por
cualquier motivo una aplicación de recepción no puede procesar el mensaje,
entonces realice la llamada al método **Abandon** del mensaje recibido (en lugar
del método **Complete**). Esto hará que el bus de servicio desbloquee
el mensaje de la cola y esté disponible para que pueda volver a recibirse,
ya sea por la misma aplicación
que lo consume o por otra.

También hay otro tiempo de espera asociado con un mensaje bloqueado en
la cola y, si la aplicación no puede procesar el mensaje antes de que finalice el tiempo de espera del
bloqueo (por ejemplo, si la aplicación se bloquea), entonces el
bus de servicio desbloquea en mensaje automáticamente y hace que esté disponible para
que pueda volver a recibirse.

En caso de que la aplicación se bloquee después de procesar el mensaje y
antes de emitir la solicitud **Complete**, entonces el mensaje se
volverá a entregar a la aplicación cuando esta se reinicie. Habitualmente se denomina
**At Least Once Processing**; es decir, cada mensaje se procesará al
menos una vez; aunque en determinadas situaciones podría
volver a entregarse el mismo mensaje. Si el escenario no puede tolerar el procesamiento duplicado, entonces
los desarrolladores de la aplicación deberían agregar lógica adicional a su aplicación para solucionar
la entrega de mensajes duplicados. A menudo, esto se consigue usando la propiedad
**MessageId** del mensaje, que permanecerá constante en todos los
intentos de entrega.

## <span class="short-header">Pasos siguientes</span>Pasos siguientes

Ahora que conoce los fundamentos de las colas del bus de servicio, siga estos
vínculos para obtener más información.

-   Consulte la referencia de MSDN: [Colas, temas y suscripciones del bus de servicio][Colas, temas y suscripciones del bus de servicio]
-   Creación de una aplicación que envíe y reciba mensajes desde la cola
    del bus de servicio y hacia ella: [Tutorial de .NET de mensajería con confianza establecida del
    bus de servicio][Tutorial de .NET de mensajería con confianza establecida del
    bus de servicio].

  [Pasos siguientes]: #next-steps
  [create-account-note]: ../includes/create-account-note.md
  [howto-service-bus-queues]: ../includes/howto-service-bus-queues.md
  [0]: ./media/service-bus-dotnet-how-to-use-queues/getting-started-multi-tier-13.png
  [Colas, temas y suscripciones del bus de servicio]: http://msdn.microsoft.com/es-es/library/windowsazure/hh367516.aspx
