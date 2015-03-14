<properties 
	pageTitle="Aplicación .NET de niveles múltiples - Tutorial de Azure"
	description="Un tutorial que le ayuda a desarrollar una aplicación de varios niveles en Azure que utiliza colas del bus de servicio para comunicarse entre niveles. Ejemplos en .NET." 
	services="cloud-services, service-bus" 
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
	ms.date="02/10/2015" 
	ms.author="sethm"/>





# Aplicación de niveles múltiples .NET con colas del bus de servicio

El desarrollo para Azure es sencillo con Visual Studio 2013 y el
SDK de Azure gratuito para. NET. Si aún no tiene Visual
Studio 2013, el SDK instalará automáticamente Visual Studio Express 2013, por lo que puede comenzar a desarrollar para Azure completamente
gratis. En esta guía se asume que no tiene experiencia previa con Microsoft
Azure. Al finalizar esta guía, tendrá una aplicación que usa
varios recursos de Azure que se ejecutan en su entorno local y
muestran cómo funciona una aplicación de niveles múltiples.

Aprenderá a:

-   Cómo habilitar el equipo para desarrollo de Azure con una
    única descarga e instalación.
-   Cómo utilizar Visual Studio para desarrollar para Azure.
-   Cómo crear una aplicación de niveles múltiples en Azure usando
    roles web y de trabajo.
-   Cómo comunicarse entre niveles mediante las colas del bus de servicio.

[WACOM.INCLUDE [create-account-note](../includes/create-account-note.md)]

En este tutorial compilará y ejecutará la aplicación de niveles múltiples en un servicio en la nube de Azure. El front-end será un rol web de ASP.NET MVC y el back-end será un rol de trabajo Puede crear la misma aplicación de niveles múltiples con el front-end como un proyecto web que se puede implementar en un sitio web de Azure en lugar de en un servicio en la nube. Para obtener instrucciones acerca de cómo realizar de manera diferente un front-end de sitio web de Azure, consulte la sección [Pasos siguientes](#nextsteps).

A continuación, se muestra una captura de la pantalla de la aplicación finalizada:

![][0]

**Nota**: Azure también proporciona funcionalidad de colas de almacenamiento. Para obtener más información sobre las colas de almacenamiento de Azure y las colas del bus de servicio, consulte [Colas de Windows Azure y Colas de Bus de servicio de Microsoft Azure: comparación y diferencias][sbqueuecomparison].  

<h2>Información general del escenario: comunicación entre roles</h2>

Para enviar un pedido para procesar, el componente de UI de front-end que se ejecuta
en el rol web, necesita interactuar con la lógica de nivel intermedio que se ejecuta en
el rol de trabajo. Este ejemplo usa mensajería asíncrona de Bus de servicio para
la comunicación entre los niveles.

El uso de la mensajería asíncrona entre los niveles web e intermedio desacopla los
dos componentes. A diferencia de la mensajería directa (es decir, TCP o HTTP),
el nivel web no se conecta al nivel intermedio directamente; en su lugar,
inserta unidades de trabajo, como mensajes, en el Bus de servicio, que las
conserva de forma fiable hasta que el nivel intermedio está preparado para consumirlas y procesarlas.

El bus de servicio ofrece dos entidades para admitir la mensajería asíncrona:
colas y temas. Con las colas, cada mensaje enviado a la cola se
consume por un único receptor. Los temas admiten el patrón de publicación/suscripción
en el que cada mensaje publicado pasa a estar disponible para cada
suscripción registrada con el tema. Cada suscripción
mantiene lógicamente su propia cola de mensajes. Las suscripciones también se pueden
configurar con reglas de filtro que restringen el conjunto de mensajes que se pasa a
la cola de suscripción a aquellos que cumplen los criterios del filtro. Este ejemplo usa
colas de Bus de servicio.

![][1]

Este mecanismo de comunicación tiene varias ventajas sobre la
mensajería directa, a saber:

-   **Desacoplamiento temporal.** Con el patrón de mensajería asincrónica,
    los productores y consumidores no necesitan estar en línea al mismo tiempo. Bus de servicio
    almacena de forma fiable mensajes hasta que el consumidor está preparado para
    recibirlos. Esto permite que los componentes de la aplicación distribuida
    estén desconectados, ya sea voluntariamente, por ejemplo, para
    mantenimiento, o debido a un bloqueo de componente, sin que afecte al
    sistema en su conjunto. Además, la aplicación consumidora puede
    estar en línea solamente durante determinadas horas del día.

-   **Redistribución de carga**. En muchas aplicaciones, la carga del sistema varía con el
    tiempo mientras que el tiempo de procesamiento necesario para cada unidad de trabajo
    suele ser constante. La intermediación de productores y consumidores de mensajes
    con una cola significa que la aplicación consumidora (el trabajador) solo
    se debe aprovisionar para acomodar la carga media en lugar de la carga
    de pico. La profundidad de la cola aumentará y se contraerá a medida que varíe
    la carga entrante. Esto directamente ahorra dinero en cuanto a la cantidad de
    infraestructura necesaria para dar servicio a la carga de aplicaciones.

-   **Equilibrio de carga.** A medida que aumenta la carga, se pueden agregar más procesos de trabajo
    para leer de la cola. Cada mensaje se procesa por uno solo
    de los procesos de trabajo. Además, este equilibrio de carga basado en extracción
    permite la utilización óptima de máquinas de trabajo incluso si
    dichas máquinas difieren en términos de capacidad de procesamiento ya que
    extraerán los mensajes a su propia velocidad máxima. Este patrón se da en
    llamar el patrón de consumo de competencia.

    ![][2]

En las secciones siguientes se explica el código que implementa esta
arquitectura.

<h2>Configuración del entorno de desarrollo</h2>

Antes de comenzar a desarrollar la aplicación de Azure, debe
obtener las herramientas y configurar el entorno de desarrollo.

1.  Para instalar el SDK de Azure para .NET, haga clic en el botón siguiente:

    [Obtención de herramientas y de SDK][]

2. 	Haga clic en **Instalar el SDK**.

3. 	Elija el vínculo de la versión de Visual Studio que está utilizando. Para los pasos de este tutorial se utiliza Visual Studio 2013:

	![][32]

4. 	Cuando se le solicite ejecutar o guardar el archivo de instalación, haga clic en
    **Ejecutar**:

    ![][3]

5.  En el instalador de plataforma web, haga clic en **Instalar** y continúe con la instalación:

    ![][33]

6.  Una vez completada la instalación, tendrá todo
    lo necesario para comenzar a desarrollar. El SDK incluye herramientas que le permitirán
    desarrollar fácilmente aplicaciones de Azure en Visual Studio. Si
    no tiene instalado Visual Studio, también instala la versión de
    Visual Studio Express gratuita para Web.

<h2>Configuración del espacio de nombres de Bus de servicio</h2>

El paso siguiente consiste en crear un espacio de nombres de servicio y obtener una
clave secreta compartida. Un espacio de nombres de servicio proporciona un límite de aplicación para
cada aplicación que se expone a través del Bus de servicio. Una clave secreta compartida
se genera automáticamente por el sistema cuando se crea un espacio
de nombres de servicio. La combinación del espacio de nombres de servicio y la clave secreta compartida
proporciona una credencial para Bus de servicio para autenticar el acceso a una
aplicación.

Tenga en cuenta que también puede administrar espacios de nombres y entidades de mensajería del bus de servicio mediante el Explorador de servidores de Visual Studio, pero solo puede crear nuevos espacios de nombres desde el portal. 

###Configuración del espacio de nombres mediante el Portal de administración

1.  Inicie sesión en el [Portal de administración de Azure][].

2.  En el panel de navegación izquierdo del Portal de administración, haga clic en
    **Bus de servicio**.

3.  En el panel inferior del Portal de administración, haga clic en **Crear**.

    ![][6]

4.  En el cuadro de diálogo **Agregar un nuevo espacio de nombres**, especifique un nombre de espacio de nombres.
    El sistema realiza la comprobación automáticamente para ver si el nombre está disponible.   
    ![][7]

5.  Después de asegurarse de que el espacio de nombres está disponible, elija el
    país o región donde se debe hospedar el espacio de nombres (asegúrese
    de usar el mismo país o región donde va a implementar sus
    recursos de proceso).

    IMPORTANTE: seleccione la **misma región** que vaya a elegir para la
    implementación de la aplicación. Con esto conseguirá el máximo rendimiento.

6.  Haga clic en la marca de verificación. El sistema ahora crea el espacio
    de nombres del servicio y lo habilita. Es posible que tenga que esperar algunos minutos mientras el
    sistema realiza el aprovisionamiento de los recursos para la cuenta.

	![][27]

7.  En la ventana principal, haga clic en el nombre del espacio de nombres de servicio.

	![][30]

8. Haga clic en **Información de conexión**.

	![][31]

9.  En el panel **Información de conexión de acceso**, busque los valores **Emisor predeterminado** y **Clave predeterminada**.

10.  Anote la clave o cópiela en el Portapapeles.

###Administración de espacios de nombres y de entidades de mensajería mediante el Explorador de servidores de Visual Studio

Para administrar un espacio de nombres y obtener la información de conexión utilizando Visual Studio en vez del Portal de administración, siga el procedimiento descrito [aquí](http://http://msdn.microsoft.com/library/windowsazure/ff687127.aspx), en la sección titulada **Para conectarse a Azure desde Visual Studio**. Al iniciar sesión en Azure, el nodo **Bus de servicio** bajo el árbol **Microsoft Azure** del Explorador de servidores se rellena automáticamente con los espacios de nombre que ya haya creado. Haga clic con el botón secundario en cualquier espacio de nombre, a continuación haga clic en **Propiedades** para ver la cadena de conexión y otros metadatos asociados a este nombre de espacio en el panel **Propiedades** de Visual Studio. 

Anote el valor de **SharedAccessKey**, o cópielo en el Portapapeles:

![][34]

<h2>Creación de un rol web</h2>

En esta sección, creará el front-end de la aplicación. Primero
creará las distintas páginas que mostrará la aplicación.
Después, agregará el código para enviar elementos a una cola de Bus
de servicio y mostrar información de estado acerca de la cola.

### Creación del proyecto

1.  Con privilegios de administrador, inicie Microsoft Visual Studio
    2013 o Microsoft Visual Studio Express. Para iniciar Visual
    Studio con privilegios de administrador, haga clic con el botón secundario en **Microsoft Visual
    Studio 2013 (o Microsoft Visual Studio Express)** y,
    a continuación, haga clic en **Ejecutar como administrador**. El emulador de proceso de Azure
    descrito más adelante en esta guía, requiere que Visual Studio
    se inicie con privilegios de administrador.

    En Visual Studio, en el menú **Archivo**, haga clic en **Nuevo** y, a continuación, en
    **Proyecto**.

    ![][8]


2.  En **Plantillas instaladas**, bajo **Visual C#**, haga clic en **Nube** y,
    a continuación, haga clic en **Servicio en la nube de Azure**. Asigne el nombre
    **MultiTierApp** al proyecto. A continuación, haga clic en **Aceptar**.

    ![][9]

3.  En los roles de **.NET Framework 4.5**, haga doble clic en **Rol web
    de ASP.NET**.

    ![][10]

4.  Mantenga el puntero sobre **WebRole1** bajo la **solución Servicio de nube de Azure**, haga clic en
    el icono de lápiz y cambie el nombre del rol web a **FrontendWebRole**. A continuación, haga clic en **Aceptar**. (Asegúrese de que escribe "Frontend" con "e" minúscula, no "FrontEnd").

    ![][11]

5.  En el cuadro de diálogo **Nuevo proyecto de ASP.NET**, en la lista **Seleccionar una plantilla**, haga clic en **MVC**
    y, a continuación, haga clic en **Aceptar**.

    ![][12]

6.  En el **Explorador de soluciones**, haga clic con el botón secundario en **References** y, a continuación, haga clic en
    **Administrar paquetes de NuGet** o **Agregar referencia de paquete de biblioteca**.

7.  Seleccione **En línea** a la izquierda del cuadro de diálogo. Busque
    "**WindowsAzure**" y seleccione el elemento **Bus de servicio
    de Azure**. Después finalice la instalación y cierre este cuadro de diálogo.

    ![][13]

8.  Tenga en cuenta que ahora se hace referencia a los ensamblados del cliente y se
    han agregado nuevos archivos de código.

9.  En **Explorador de soluciones**, haga clic con el botón secundario del mouse en **Modelos**, luego haga clic en **Agregar** y,
    a continuación, haga clic en **Clase**. En el cuadro Nombre, escriba el nombre
    **OnlineOrder.cs**. A continuación, haga clic en **Agregar**.

### Especificación del código del rol web

En esta sección, creará las distintas páginas que mostrará
la aplicación.

1.  En el archivo **OnlineOrder.cs** de Visual Studio, sustituya la
    definición de espacio de nombres existente por el código siguiente:

        namespace FrontendWebRole.Models
        {
            public class OnlineOrder
            {
                public string Customer { get; set; }
                public string Product { get; set; }
            }
        }

2.  En el **Explorador de soluciones**, haga doble clic en
    **Controllers\HomeController.cs**. Agregue las siguientes instrucciones **using**:
    en la parte superior del archivo para incluir los espacios de nombres para el
    modelo que acaba de crear, así como el Bus de servicio:

        using FrontendWebRole.Models;
        using Microsoft.ServiceBus.Messaging;
        using Microsoft.ServiceBus;

3.  También en el archivo **HomeController.cs** de Visual Studio, sustituya la
    definición de espacio de nombres existente con el código siguiente. Este código
    contiene métodos para controlar el envío de elementos a la cola:

        namespace FrontendWebRole.Controllers
        {
            public class HomeController : Controller
            {
                public ActionResult Index()
                {
                    // Simply redirect to Submit, since Submit will serve as the
                    // front page of this application
                    return RedirectToAction("Submit");
                }

                public ActionResult About()
                {
                    return View();
                }

                // GET: /Home/Submit
                // Controller method for a view you will create for the submission
                // form
                public ActionResult Submit()
                {
                    // Will put code for displaying queue message count here.

                    return View();
                }

                // POST: /Home/Submit
                // Controller method for handling submissions from the submission
                // form 
                [HttpPost]
				// Attribute to help prevent cross-site scripting attacks and 
				// cross-site request forgery  
    			[ValidateAntiForgeryToken] 
                public ActionResult Submit(OnlineOrder order)
                {
                    if (ModelState.IsValid)
                    {
                        // Will put code for submitting to queue here.
                    
                        return RedirectToAction("Submit");
                    }
                    else
                    {
                        return View(order);
                    }
                }
            }
        }

4.  En el menú **Compilar**, haga clic en **Compilar solución**.

5.  Ahora, creará la vista para el método **Submit()** que ha
    creado más arriba. Haga clic con el botón secundario en el método Submit() y elija
    **Agregar vista**

    ![][14]

6.  Se muestra un cuadro de diálogo para crear la vista. Seleccione la
clase     **OnlineOrder** en la lista desplegable **Clase de modelo** y elija
    **Crear** en la lista desplegable **Plantilla**.

    ![][15]

7.  Haga clic en **Agregar**.

8.  Ahora, cambiará el nombre mostrado de la aplicación. En el
    **Explorador de soluciones**, haga doble clic en el archivo
    **Views\Shared\\_Layout.cshtml** para abrirlo en el editor de Visual
    Studio.

9.  Reemplace todas las apariciones de **Mi aplicación de ASP.NET MVC** por
    **LITWARE'S Awesome Products**.

10.	Reemplace **"your logo here"** por **LITWARE'S Awesome Products**:

	![][16]

11. Quite los vínculos **Home**, **About** y **Contact**. Elimine el código resaltado:

	![][28]
  

12. Finalmente, modifique la página de envío para incluir información
    sobre la cola. En el **Explorador de soluciones**, haga doble clic en el archivo
    **Views\Home\Submit.cshtml** para abrirlo en el editor de Visual
    Studio. Agregue la línea siguiente después de **<h2>Submit</h2>**. Por ahora,
    **ViewBag.MessageCount** está vacío. Lo rellenará más adelante.

        <p>Current Number of Orders in Queue Waiting to be Processed: @ViewBag.MessageCount</p>
             

13. Acaba de implementar su interfaz de usuario. Puede presionar **F5** para ejecutar la
    aplicación y confirmar que tiene el aspecto que se esperaba.

    ![][17]

### Especificación del código para enviar elementos a una cola del bus de servicio

Ahora, agregará código para enviar elementos a una cola. Primero creará
una clase que contiene la información de conexión de la cola de
Bus de servicio. A continuación, inicializará la conexión en
**Global.aspx.cs**. Por último, se actualizará el código de envío que
creó anteriormente en **HomeController.cs** para enviar realmente elementos a una
cola de Bus de servicio.

1.  En el Explorador de soluciones, haga clic con el botón secundario en **FrontendWebRole** (haga clic con el botón secundario en el proyecto, no en el rol). Seleccione **Agregar** y, a continuación, haga clic en **Clase**.

2.  Asigne el nombre **QueueConnector.cs** a la clase. Haga clic en **Agregar** para crear la clase.

3.  Ahora pegará el código que encapsula la información de
    conexión y contiene métodos para inicializar la conexión a
    una cola del Bus de servicio. En QueueConnector.cs, pegue el código siguiente y escriba
    valores para **Namespace**, **IssuerName** y **IssuerKey**. Puede
    obtener estos valores en el [Portal de administración][Azure Management Portal] o en el Explorador de servidores de Visual Studio en el nodo bajo el nodo **Bus de servicio**.

        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Web;
        using Microsoft.ServiceBus.Messaging;
        using Microsoft.ServiceBus;

        namespace FrontendWebRole
        {
            public static class QueueConnector
            {
                // Thread-safe. Recommended that you cache rather than recreating it
                // on every request.
                public static QueueClient OrdersQueueClient;

                // Obtain these values from the Management Portal
                public const string Namespace = "your service bus namespace";
                public const string IssuerName = "issuer name";
                public const string IssuerKey = "issuer key";

                // The name of your queue
                public const string QueueName = "OrdersQueue";

                public static NamespaceManager CreateNamespaceManager()
                {
                    // Create the namespace manager which gives you access to
                    // management operations
                    var uri = ServiceBusEnvironment.CreateServiceUri(
                        "sb", Namespace, String.Empty);
                    var tP = TokenProvider.CreateSharedSecretTokenProvider(
                        IssuerName, IssuerKey);
                    return new NamespaceManager(uri, tP);
                }

                public static void Initialize()
                {
                    // Using Http to be friendly with outbound firewalls
                    ServiceBusEnvironment.SystemConnectivity.Mode = 
                        ConnectivityMode.Http;

                    // Create the namespace manager which gives you access to 
                    // management operations
                    var namespaceManager = CreateNamespaceManager();

                    // Create the queue if it does not exist already
                    if (!namespaceManager.QueueExists(QueueName))
                    {
                        namespaceManager.CreateQueue(QueueName);
                    }

                    // Get a client to the queue
                    var messagingFactory = MessagingFactory.Create(
                        namespaceManager.Address, 
                        namespaceManager.Settings.TokenProvider);
                    OrdersQueueClient = messagingFactory.CreateQueueClient(
                        "OrdersQueue");
                }
            }
        }

4.  Ahora, se asegurará de que se llame al método **Initialize**. En el **Explorador de soluciones**, haga doble clic en **Global.asax\Global.asax.cs**.

5.  Agregue la línea siguiente a la parte inferior del método **Application_Start**
    :

        FrontendWebRole.QueueConnector.Initialize();

6.  Finalmente, va a actualizar el código web que ha creado antes para
    enviar elementos a la cola. En el **Explorador de soluciones**,
    haga doble clic en **Controllers\HomeController.cs** que ha creado
    antes.

7.  Actualice el método **Submit()** de la forma siguiente para obtener el recuento de mensajes
    para la cola:

        public ActionResult Submit()
        {            
            // Get a NamespaceManager which allows you to perform management and
            // diagnostic operations on your Service Bus Queues.
            var namespaceManager = QueueConnector.CreateNamespaceManager();

            // Get the queue, and obtain the message count.
            var queue = namespaceManager.GetQueue(QueueConnector.QueueName);
            ViewBag.MessageCount = queue.MessageCount;

            return View();
        }

8.  Actualice el método **Submit(OnlineOrder order)** de la forma siguiente para enviar
    información de pedido a la cola:

        public ActionResult Submit(OnlineOrder order)
        {
            if (ModelState.IsValid)
            {
                // Create a message from the order
                var message = new BrokeredMessage(order);
                
                // Submit the order
                QueueConnector.OrdersQueueClient.Send(message);
                return RedirectToAction("Submit");
            }
            else
            {
                return View(order);
            }
        }

9.  Ahora puede volver a ejecutar la aplicación. Cada vez que envía un
    pedido, el recuento de mensajes aumenta.

    ![][18]

<h2>Administrador de configuración de nube</h2>

Azure admite un conjunto de API administradas que ofrece una forma coherente de crear nuevas instancias de clientes de servicio de Azure (como el bus de servicio) en los servicios en la nube de Microsoft. Estas API le permiten crear instancias de estos clientes (por ejemplo, **CloudBlobClient**, **QueueClient**, **TopicClient**) con independencia de dónde se hospede la aplicación; localmente, en un servicio en la nube de Microsoft, en sitios web o en un rol de máquina virtual persistente. También puede usar estas API para recuperar la información de la configuración necesaria para crear una instancia de estos clientes y cambiar la configuración sin tener que volver a implementar la aplicación de llamada. Las API están ubicadas en la clase [Microsoft.WindowsAzure.Configuration.CloudConfigurationManager][]. También hay API en el cliente.

### Cadena de conexión

Para crear una instancia de un cliente (por ejemplo una **QueueClient** del Bus de servicio), puede representar la información de configuración como una cadena de conexión. En el cliente, hay un método **CreateFromConnectionString()** que crea una instancia del tipo de cliente utilizando esa cadena de conexión. Por ejemplo, dada la sección de configuración siguiente:

	<ConfigurationSettings>
    ...
    	<Setting name="Microsoft.ServiceBus.ConnectionString" value="Endpoint=sb://[yourServiceNamespace].servicebus.windows.net/;SharedSecretIssuer=[issuerName];SharedSecretValue=[yourDefaultKey]" />
	</ConfigurationSettings>

El código siguiente recupera la cadena de conexión, crea una cola e inicializa la conexión a la cola:

	QueueClient Client; 

	string connectionString =
     CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");
	
    var namespaceManager =
     NamespaceManager.CreateFromConnectionString(connectionString); 

	if (!namespaceManager.QueueExists(QueueName))
    {
        namespaceManager.CreateQueue(QueueName);
    }

	// Initialize the connection to Service Bus Queue
	Client = QueueClient.CreateFromConnectionString(connectionString, QueueName);

El código de la sección siguiente utiliza estas API de administración de configuración.

<h2>Creación del rol de trabajo</h2>

Ahora creará el rol de trabajo que procesa los envíos
del pedido. Este ejemplo utiliza la plantilla de proyecto de Visual Studio de **Rol de trabajo con cola de Bus de servicio**. En primer lugar, utilizará el Explorador de servidores en Visual Studio para obtener las credenciales requeridas.

1. Si ya ha conectado Visual Studio a su cuenta de Azure, tal como se ha descrito en la sección **Configuración del espacio de nombres mediante el Explorador de servidores de Visual Studio**, vaya al paso 5. 

3. En la barra de menús de Visual Studio, elija **Ver** y, a continuación, haga clic en **Explorador de servidores**. Un nodo **Bus de servicio** aparece en **Azure** dentro la jerarquía del Explorador de servicios, como en la figura siguiente.

	![][21]

2. En el Explorador de servidores, expanda **Azure**, haga clic con el botón secundario en **Bus de servicio** y, a continuación, en **Agregar nueva conexión**.

3. En el cuadro de diálogo **Agregar conexión**, escriba el nombre del espacio de nombres del servicio, el nombre del emisor y la clave del emisor. A continuación, haga clic en **Aceptar** para conectarse.

	![][22]

4.  En Visual Studio, en el **Explorador de soluciones**, haga clic con el botón secundario
en la carpeta de     **Roles** en el proyecto **MultiTierApp**.

5.  Haga clic en **Agregar** y, a continuación, haga clic en **Nuevo proyecto de rol de trabajador**. Aparecerá el cuadro de diálogo **Agregar nuevo proyecto de rol**.

	![][26]

6.  En el cuadro de diálogo **Agregar nuevo proyecto de rol**, haga clic en **Rol de trabajo con cola de Bus de servicio**, como en la figura siguiente:

	![][23]

7.  En el cuadro **Nombre**, asigne el nombre de **OrderProcessingRole** al proyecto. A continuación, haga clic en **Agregar**.

8.  En el Explorador de servidores, haga clic con el botón secundario en el espacio de nombres del servicio y, a continuación, haga clic en **Propiedades**. En el panel **Propiedades** de Visual Studio, la primera entrada contiene una cadena de conexión que se rellena con el extremo del espacio de nombres del servicio que contiene las credenciales de autorización requeridas. Por ejemplo, consulte la figura siguiente. Haga doble clic en **ConnectionString** y, a continuación, presione **Ctrl+C** para copiar esta cadena en el Portapapeles.

	![][24]

9.  En el Explorador de soluciones, haga clic con el botón secundario en **OrderProcessingRole** que ha creado en el paso 7 (asegúrese de que hace clic con el botón secundario en **OrderProcessingRole** en **Roles** y no en la clase). A continuación, haga clic en **Propiedades**.

10.  En la pestaña **Configuración** del cuadro de diálogo **Propiedades**, haga clic dentro del cuadro **Valor** para **Microsoft.ServiceBus.ConnectionString** y, a continuación, pegue el valor del extremo que ha copiado en el paso 8.

	![][25]

11.  Cree una clase **OnlineOrder** para representar los pedidos cuando los procese de la cola. Puede reutilizar una clase que ya ha creado. En el Explorador de soluciones, haga clic con el botón secundario en el proyecto **OrderProcessingRole** (haga clic con el botón secundario en el proyecto, no en el rol). Seleccione **Agregar** y, a continuación, haga clic en **Elemento existente**.

12. Busque **FrontendWebRole\Models** en la subcarpeta y haga doble clic en **OnlineOrder.cs** para agregarla a este proyecto.

13. En WorkerRole.cs, reemplace el valor de la variable **QueueName** en **WorkerRole.cs** de `"ProcessingQueue"` por `"OrdersQueue"` como en el código siguiente:

		// The name of your queue
		const string QueueName = "OrdersQueue";

14. Agregue la siguiente instrucción using en la parte superior del archivo WorkerRole.cs:

		using FrontendWebRole.Models;

15. En la función `Run()`, dentro de la llamada `OnMessage`, agregue el código siguiente dentro de la cláusula `try`:

		Trace.WriteLine("Processing", receivedMessage.SequenceNumber.ToString());
		// View the message as an OnlineOrder
		OnlineOrder order = receivedMessage.GetBody<OnlineOrder>();
		Trace.WriteLine(order.Customer + ": " + order.Product, "ProcessingMessage");
		receivedMessage.Complete();

16.  Ha completado la aplicación. Puede probar la
    aplicación completa como ha hecho antes presionando F5. Tenga en cuenta que el contador de mensajes no se aumenta, ya que el rol de trabajo procesa los elementos de la cola y los marca como finalizados. Puede ver el resultado de seguimiento de su
    rol de trabajo viendo la interfaz de usuario del emulador de proceso de Azure. Para ello,
    haga clic con el botón secundario en el icono del emulador del área 
    de notificación de la barra de tareas y seleccione **Mostrar la UI del emulador de proceso**.

    ![][19]

    ![][20]

<h2><a name="nextsteps"></a>Pasos siguientes</h2>  

Para obtener más información sobre el bus de servicio, consulte los siguientes recursos:  
  
* [Bus de servicio de Azure][sbmsdn]  
* [Procedimientos del Bus de servicio][sbwacom]  
* [Utilización de las colas del Bus de servicio][sbwacomqhowto]  

Para obtener más información sobre los escenarios de niveles múltiples o sobre cómo implementar una aplicación en un servicio en la nube, consulte:  

* [Aplicación .NET de niveles múltiples utilizando tablas, colas y blobs de almacenamiento][mutitierstorage]  

Es posible que desee implementar el front-end de una aplicación de niveles múltiples en un sitio web de Azure en lugar de en un servicio en la nube de Azure. Para obtener más información sobre la diferencia entre sitios web y servicios en la nube, consulte [Modelos de ejecución de Azure][executionmodels].

Para implementar la aplicación que ha creado en este tutorial como un proyecto web estándar en lugar de como un rol web de servicio en la nube, siga los pasos de este tutorial con las diferentes siguientes:

1. Cuando crea el proyecto, elija la plantilla del proyecto **Aplicación web de ASP.NET MVC 4** en la categoría **Web** en lugar de la plantilla **Servicio en la nube** en la categoría **Nube**. A continuación, siga las mismas instrucciones para crear la aplicación MVC hasta que llegue a la sección **Administrador de configuración de nube**.

2. Cuando crea el rol de trabajo, créelo de una nueva solución independiente, similar a las instrucciones originales para el rol web. Tenga en cuenta, sin embargo, que está creando solo el rol de trabajo en el proyecto del servicio en la nube. A continuación, siga las mismas instrucciones para crear el rol de trabajo.

3. Puede probar por separado el front-end y el back-end, o bien puede ejecutar ambos simultáneamente en instancias separadas de Visual Studio.

Para obtener información sobre cómo implementar el front-end en un sitio web de Azure, consulte [Implementación de una aplicación web ASP.NET a un sitio web de Azure](http://azure.microsoft.com/develop/net/tutorials/get-started/). Para obtener información sobre cómo implementar el back-end en un servicio en la nube de Azure, consulte [Aplicación .NET de niveles múltiples utilizando tablas, colas y blobs de almacenamiento][mutitierstorage].


  [0]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-01.png
  [1]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-100.png
  [sbqueuecomparison]: http://msdn.microsoft.com/library/windowsazure/hh767287.aspx
  [2]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-101.png
  [Obtención de herramientas y de SDK]: http://go.microsoft.com/fwlink/?LinkId=271920
  [3]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-3.png
  
  
  
  [Portal de administración de Azure]: http://manage.windowsazure.com
  [6]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/sb-queues-03.png
  [7]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/sb-queues-04.png
  [8]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-09.png
  [9]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-10.jpg
  [10]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-11.png
  [11]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-02.png
  [12]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-12.png
  [13]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-13.png
  [14]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-33.png
  [15]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-34.png
  [16]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-35.png
  [17]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-36.png
  [18]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-37.png
  
  [19]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-38.png
  [20]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-39.png
  [21]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/SBExplorer.png
  [22]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/SBExplorerAddConnect.png
  [23]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/SBWorkerRole1.png
  [24]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/SBExplorerProperties.png
  [25]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/SBWorkerRoleProperties.png
  [26]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/SBNewWorkerRole.png
  [27]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-27.png
  [28]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-40.png
  [30]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/sb-queues-09.png
  [31]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/sb-queues-06.png
  [32]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-41.png
  [33]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-4-2-WebPI.png
  [34]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/VSProperties.png
  [sbmsdn]: http://msdn.microsoft.com/library/windowsazure/ee732537.aspx  
  [sbwacom]: /es-es/documentation/services/service-bus/  
  [sbwacomqhowto]: /es-es/develop/net/how-to-guides/service-bus-queues/  
  [mutitierstorage]: /es-es/develop/net/tutorials/multi-tier-web-site/1-overview/ 
  [executionmodels]: http://azure.microsoft.com/develop/net/fundamentals/compute/

<!--HONumber=46--> 
