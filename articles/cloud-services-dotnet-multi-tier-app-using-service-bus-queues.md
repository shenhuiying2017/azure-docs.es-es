<properties linkid="dev-net-e2e-multi-tier" urlDisplayName="Multi-Tier Application" pageTitle=".NET Multi-Tier Application - Azure Tutorial" metaKeywords="Azure Service Bus queue tutorial, Azure queue tutorial, Azure worker role tutorial, Azure .NET queue tutorial, Azure C# queue tutorial, Azure C# worker role tutorial" description="A tutorial that helps you develop a multi-tier app in Azure that uses Service Bus queues to communicate between tiers. Samples in .NET." metaCanonical="" services="cloud-services,service-bus" documentationCenter=".NET" title=".NET Multi-Tier Application Using Service Bus Queues" authors="sethm" solutions="" manager="dwrede" editor="mattshel" />

Aplicación de niveles múltiples .NET con colas del bus de servicio
==================================================================

Desarrollar para Azure es muy sencillo con Visual Studio 2013 y el SDK de Azure para .NET gratuito. Si todavía no tiene Visual Studio 2013, el SDK instalará automáticamente Visual Studio Express 2013 para que pueda comenzar a desarrollar para Azure sin costo alguno. En esta guía se asume que no tiene experiencia previa con Azure. Al finalizar esta guía, tendrá una aplicación que utiliza varios recursos de Azure ejecutándose en su entorno local y mostrando cómo funciona una aplicación de niveles múltiples.

Aprenderá lo siguiente:

-   Cómo habilitar el equipo para el desarrollo de Azure con una única descarga e instalación.
-   Cómo utilizar Visual Studio para desarrollar para Azure.
-   Cómo crear una aplicación de niveles múltiples en Azure mediante el uso de roles web y de trabajo.
-   Cómo comunicarse entre niveles mediante las colas del bus de servicio.

[WACOM.INCLUDE [create-account-note](../includes/create-account-note.md)]

En este tutorial compilará y ejecutará la aplicación de niveles múltiples en un servicio en la nube de Azure. El front-end será un rol web de ASP.NET MVC y el back-end será un rol de trabajo Puede crear la misma aplicación de niveles múltiples con el front-end como un proyecto web que se puede implementar en un sitio web de Azure en lugar de en un servicio en la nube. Para obtener instrucciones acerca de cómo realizar de manera diferente un front-end de sitio web de Azure, consulte la sección [Pasos siguientes](#nextsteps).

A continuación, se muestra una captura de la pantalla de la aplicación finalizada:

![](./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-01.png)

**Nota**: Azure también proporciona funcionalidad de colas de almacenamiento. Para obtener más información sobre las colas de almacenamiento de Azure y las colas del bus de servicio, consulte [Colas de Windows Azure y Colas de Service Bus de Windows Azure: comparación y diferencias](http://msdn.microsoft.com/es-es/library/windowsazure/hh767287.aspx).

[WACOM.INCLUDE [create-account-note](../includes/create-account-note.md)]

Comunicación entre rolesInformación general del escenario: comunicación entre roles
-----------------------------------------------------------------------------------

Para enviar una orden para su procesamiento, el componente de la interfaz de usuario de front-end, que se ejecuta en el rol web, tiene que interactuar con la lógica del nivel intermedio que se ejecuta en el rol de trabajo. En ese ejemplo se utiliza la mensajería asíncrona del bus de servicio para la comunicación entre los niveles.

El uso de la mensajería asíncrona entre los niveles de web e intermedio desacopla los dos componentes. Al contrario que en la mensajería directa (es decir, TCP o HTTP), el nivel web no se conecta directamente al nivel intermedio; por el contrario, inserta unidades de trabajo, como mensajes, en el bus de servicio que los conserva de manera confiable hasta que el nivel intermedio esté preparado para consumirlas y procesarlas.

El bus de servicio ofrece dos entidades para admitir la mensajería asíncrona: colas y temas. Con las colas, cada mensaje enviado a la cola lo consume un único receptor. Los temas admiten el patrón de publicación/suscripción, en el cual cada mensaje publicado está disponible para cada suscripción registrada con el tema. Cada suscripción mantiene lógicamente su propia cola de mensajes. Las suscripciones también se pueden configurar con reglas de filtro que restringen el conjunto de mensajes pasados a la cola de suscripción a aquellos que coinciden con el filtro. En este ejemplo se utilizan las colas de bus de servicio.

![](./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-100.png)

El mecanismo de comunicación tiene varias ventajas sobre la mensajería directa, a saber:

-   **Desacoplamiento temporal.** Con el patrón de mensajería asincrónica, los productores y los consumidores no tienen que estar en línea al mismo tiempo. El bus de servicio almacena los mensajes de manera confiable hasta que la parte consumidora esté lista para recibirlos. De esta forma los componentes de la aplicación distribuida se pueden desconectar, ya sea voluntariamente, por ejemplo, para mantenimiento, o debido a un bloqueo del componente, sin que afecte al sistema en su conjunto. Es más, la aplicación consumidora solo puede necesitar estar en línea durante determinados períodos del día.

-   **Redistribución de carga**. En muchas aplicaciones, la carga del sistema varía con el tiempo, mientras que el tiempo de procesamiento requerido por cada unidad de trabajo suele ser constante. La intermediación de productores y consumidores de mensajes con una cola implica que la aplicación consumidora (el trabajador) solo necesita ser aprovisionada para acomodar una carga promedio en lugar de una carga pico. La profundidad de la cola aumentará y se contraerá a medida que varíe la carga entrante, lo que permite ahorrar dinero directamente en función de la cantidad de infraestructura requerida para dar servicio a la carga de la aplicación.

-   **Equilibrio de carga.** A medida que aumenta la carga, se pueden agregar más procesos de trabajo para que puedan leerse desde la cola. Cada mensaje se procesa únicamente por uno de los procesos de trabajo. Es más, este equilibrio de carga basado en extracción permite la utilización óptima de los equipos de trabajo aunque estos equipos difieran en términos de capacidad de procesamiento ya que extraerán mensajes a su frecuencia máxima propia. Este patrón con frecuencia se denomina patrón de consumo de competidor.

    ![](./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-101.png)

En las secciones siguientes se explica el código que implementa esta arquitectura.

Configuración del entornoConfiguración del entorno de desarrollo
----------------------------------------------------------------

Antes de comenzar a desarrollar la aplicación de Azure, debe obtener las herramientas y configurar el entorno de desarrollo.

1.  Para instalar el SDK de Azure para .NET, haga clic en el botón siguiente:

    [Obtención de herramientas y de SDK](http://go.microsoft.com/fwlink/?LinkId=271920)

2.  Haga clic en **install the SDK**.

3.  Elija el vínculo de la versión de Visual Studio que está utilizando. Para los pasos de este tutorial se utiliza Visual Studio 2013:

    ![](./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-41.png)

4.  Cuando se le solicite ejecutar o guardar el archivo de instalación, haga clic en **Run**.

    ![](./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-3.png)

5.  En el instalador de plataforma web, haga clic en **Install** y continúe con la instalación:

    ![](./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-4-2-WebPI.png)

6.  Cuando la instalación se complete, dispondrá de todo lo necesario para iniciar el desarrollo. El SDK incluye las herramientas que le permiten desarrollar fácilmente aplicaciones Azure en Visual Studio. Si no tiene instalado Visual Studio, instale también el programa gratuito Visual Studio Express para Web.

Configuración del espacio de nombresConfiguración del espacio de nombres del bus de servicio
--------------------------------------------------------------------------------------------

El paso siguiente consiste en crear un espacio de nombres de servicio y obtener una clave secreta compartida. Un espacio de nombres proporciona un límite de aplicación para cada aplicación mostrada a través del bus de servicio. El sistema genera automáticamente una clave secreta compartida cuando se crea un espacio de nombres de servicio. La combinación del espacio de nombres de servicio y la clave secreta compartida proporciona una credencial para que el bus de servicio autentique el acceso a una aplicación.

Tenga en cuenta que también puede administrar espacios de nombres y entidades de mensajería del bus de servicio mediante el Explorador de servidores de Visual Studio, pero solo puede crear nuevos espacios de nombres desde el portal.

### Configuración del espacio de nombres mediante el Portal de administración

1.  Inicie sesión en el [Portal de administración de Azure](http://manage.windowsazure.com).

2.  En el panel de navegación izquierdo del Portal de administración, haga clic en **Bus de servicio**.

3.  En el panel inferior del Portal de administración, haga clic en **Create**.

    ![](./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/sb-queues-03.png)

4.  En el cuadro de diálogo **Add a new namespace**, especifique un nombre de espacio de nombres. El sistema realiza la comprobación automáticamente para ver si el nombre está disponible.
     ![](./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/sb-queues-04.png)

5.  Después de asegurarse de que el nombre de espacio de nombres esté disponible, seleccione el país o región en el que debe hospedarse el espacio de nombres (asegúrese de que usa el mismo país o la misma región en los que está realizando la implementación de los recursos de proceso).

    IMPORTANTE: seleccione la **misma región** que vaya a seleccionar para la implementación de la aplicación. Con esto conseguirá el máximo rendimiento.

6.  Haga clic en la marca de verificación. El sistema crea ahora el espacio de nombres del servicio y lo habilita. Es posible que tenga que esperar algunos minutos mientras el sistema realiza el aprovisionamiento de los recursos para la cuenta.

    ![](./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-27.png)

7.  En la ventana principal, haga clic en el nombre del espacio de nombres de servicio.

    ![](./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/sb-queues-09.png)

8.  Haga clic en **Connection Information**.

    ![](./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/sb-queues-06.png)

9.  En el cuadro de diálogo **Access connection information**, busque los valores **Default Issuer** y **Default Key**.

10. Anote la clave o cópiela en el Portapapeles.

### Administración de espacios de nombres y de entidades de mensajería mediante el Explorador de servidores de Visual Studio

Para administrar un espacio de nombres y obtener la información de conexión utilizando Visual Studio en vez del Portal de administración, siga el procedimiento descrito [aquí](http://http://msdn.microsoft.com/es-es/library/windowsazure/ff687127.aspx), en la sección titulada **Para conectarse a Azure desde Visual Studio**. Al iniciar sesión en Azure, el nodo **Bus de servicio** bajo el árbol **Microsoft Azure** del Explorador de servidores se rellena automáticamente con los espacios de nombre que ya haya creado. Haga clic con el botón secundario en cualquier espacio de nombre, a continuación haga clic en **Propiedades** para ver la cadena de conexión y otros metadatos asociados a este nombre de espacio en el panel **Propiedades** de Visual Studio.

Anote el valor de **SharedAccessKey**, o cópielo en el Portapapeles:

![](./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/VSProperties.png)

Creación de un rol webCreación de un rol web
--------------------------------------------

En esta sección, creará el front-end de la aplicación. Primero creará las distintas páginas que mostrará la aplicación. Después, agregará el código para enviar los elementos a una cola del bus de servicio y mostrar la información de estado sobre la cola.

### Creación del proyecto

1.  Con privilegios de administrador, inicie Microsoft Visual Studio 2013 o Microsoft Visual Studio Express. Para iniciar Visual Studio con privilegios de administrador, haga clic con el botón secundario en **Microsoft Visual Studio 2013 (o Microsoft Visual Studio Express)** y, a continuación, haga clic en **Ejecutar como administrador**. El emulador de proceso de Azure, descrito posteriormente en esta guía, requiere que se inicie Visual Studio con privilegios de administrador.

    En Visual Studio, en el menú **Archivo**, haga clic en **Nuevo** y, a continuación, en **Proyecto**.

    ![](./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-09.png)

2.  En **Plantillas instaladas**, en **Visual C\#**, haga clic en **Nube** y, a continuación, haga clic en **Servicio de nube de Azure**. Llame al proyecto **MultiTierApp**. A continuación, haga clic en **Aceptar**.

    ![](./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-10.jpg)

3.  En los roles de **.NET Framework 4.5**, haga doble clic en **Rol web de ASP.NET**.

    ![](./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-11.png)

4.  Mantenga el mouse sobre **WebRole1** en **Azure Cloud Service solution**, haga clic en el icono de lápiz y cambie el nombre del rol web a **FrontendWebRole**. A continuación, haga clic en **Aceptar**. (Asegúrese de que escribe "Frontend" con "e" minúscula, no "FrontEnd").

    ![](./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-02.png)

5.  En el cuadro de diálogo **New ASP.NET Project**, en la lista **Seleccionar una plantilla**, haga clic en **MVC** y, a continuación, haga clic en **Aceptar**.

    ![](./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-12.png)

6.  En el **Explorador de soluciones**, haga clic con el botón secundario en **Referencias** y, a continuación, haga clic en **Manage NuGet Packages...** o **Agregar referencia de paquetes de biblioteca**.

7.  Seleccione **En línea** a la izquierda del cuadro de diálogo. Busque "**WindowsAzure**" y seleccione el elemento **Azure Service Bus**. Después finalice la instalación y cierre este cuadro de diálogo.

    ![](./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-13.png)

8.  Tenga en cuenta que ahora se hace referencia a los ensamblados del cliente requeridos y que se han agregado algunos archivos de código nuevos.

9.  En el **Explorador de soluciones**, haga clic con el botón secundario en la carpeta **Modelos**, a continuación en **Add** y, por último, en **Class**. En el cuadro Nombre, escriba el nombre **OnlineOrder.cs**. A continuación, haga clic en **Add**.

### Especificación del código del rol web

En esta sección, creará las distintas páginas que mostrará la aplicación.

1.  En el archivo **OnlineOrder.cs** en Visual Studio, sustituya la definición del espacio de nombres existentes por el código siguiente:

        namespace FrontendWebRole.Models
        {
            public class OnlineOrder
            {
                public string Customer { get; set; }
                public string Product { get; set; }
            }
        }

2.  En el **Explorador de soluciones**, haga doble clic en **Controllers\\HomeController.cs**. Agregue las siguientes instrucciones **using** de la parte superior del archivo para incluir los espacios de nombres en el modelo que acaba de crear, así como el bus de servicio:

        using FrontendWebRole.Models;
        using Microsoft.ServiceBus.Messaging;
        using Microsoft.ServiceBus;

3.  En el archivo **HomeController.cs** en Visual Studio, sustituya la definición del espacio de nombres existentes por el código siguiente. Este código contiene métodos para controlar el envío de elementos a la cola:

        namespace FrontendWebRole.Controllers
        {
            public class HomeController : Controller
            {
                public ActionResult Index()
                {
                    // Simplemente se redirige a Submit, ya que Submit actuará como
                    // la página de información de esta aplicación
                    return RedirectToAction("Submit");
                }

                public ActionResult About()
                {
                    return View();
                }

                // GET: /Home/Submit
                // El método del controlador para una vista que creará para el formulario
                // de envío
                public ActionResult Submit()
                {
                    // Aquí incluirá el código para mostrar el contador de mensajes de la cola.

                    return View();
                }

                // POST: /Home/Submit
                // Método de controlador para tratar los envíos desde el formulario
                // de envío 
                [HttpPost]
                // Atributo para ayudar a evitar los ataques de scripts de sitios y 
                // falsificación de solicitud entre sitios  
                [ValidateAntiForgeryToken] 
                public ActionResult Submit(OnlineOrder order)
                {
                    if (ModelState.IsValid)
                    {
                        // Aquí incluirá código para enviar a la cola.
                        
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

5.  Ahora, creará la vista para el método **Submit()** que ha creado más arriba. Haga clic con el botón secundario en el método Submit() y elija **Agregar vista**.

    ![](./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-33.png)

6.  Se muestra un cuadro de diálogo para crear la vista. Seleccione la clase **OnlineOrder** en el menú desplegable **Model class** y elija **Crear** en el menú desplegable **Plantilla**.

    ![](./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-34.png)

7.  Haga clic en **Add**.

8.  Ahora, cambiará el nombre mostrado de la aplicación. En el **Explorador de soluciones**, haga doble clic en el archivo **Views\\Shared\\\_Layout.cshtml** para abrirlo en el editor de Visual Studio.

9.  Reemplace todas las ocurrencias de **My ASP.NET MVC Application** por **LITWARE'S Awesome Products**.

10. Reemplace **"your logo here"** por **LITWARE'S Awesome Products**:

    ![](./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-35.png)

11. Suprima los vínculos **Inicio**, **Acerca de** y **Contacto**. Elimine el código resaltado:

    ![](./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-40.png)

12. Finalmente, modifique la página de envío para incluir información sobre la cola. En el **Explorador de soluciones**, haga doble clic en el archivo **Views\\Home\\Submit.cshtml** para abrirlo en el editor de Visual Studio. Agregue la línea siguiente después de **&lt;h2\>Submit&lt;/h2\>**. De momento, **ViewBag.MessageCount** está vacío. Lo rellenará más adelante.

        <p>Número actual de pedidos en la cola en espera de ser procesados: @ViewBag.MessageCount</p>

13. Acaba de implementar su interfaz de usuario. Puede presionar **F5** para ejecutar la aplicación y confirmar que tiene el aspecto previsto.

    ![](./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-36.png)

### Especificación del código para enviar elementos a una cola del bus de servicio

Ahora, agregará código para enviar elementos a una cola. En primer lugar creará una clase que contiene su información de conexión a la cola del bus de servicio. A continuación, inicializará la conexión en **Global.aspx.cs**. Finalmente, actualizará el código de envío que ha creado antes en **HomeController.cs** para enviar realmente los elementos a una cola del bus de servicio.

1.  En el Explorador de soluciones, haga clic con el botón secundario en **FrontendWebRole** (haga clic con el botón secundario en el proyecto, no en el rol). Seleccione **Add** y, a continuación, haga clic en **Class**.

2.  Asigne a la clase el nombre **QueueConnector.cs**. Haga clic en **Add** para crear la clase.

3.  Ahora va a pegarlo en el código que encapsula su información de conexión y contiene métodos para inicializar la conexión a una cola del bus de servicio. En QueueConnector.cs, péguelo en el código siguiente e introduzca valores para **Namespace**, **IssuerName** y **IssuerKey**. Puede obtener estos valores en el [Portal de administración](http://manage.windowsazure.com) o en el Explorador de servidores de Visual Studio en el nodo **Bus de servicio**.

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
                // Seguro para subprocesos. Se recomienda que lo almacene en caché en lugar de volver a crearlo
                // en cada solicitud.
                public static QueueClient OrdersQueueClient;

                // Obtenga estos valores del Portal de administración
                public const string Namespace = "el espacio de nombres del bus de servicio";
                public const string IssuerName = "nombre del emisor";
                public const string IssuerKey = "clave del emisor";

                // El nombre de la cola
                public const string QueueName = "OrdersQueue";

                public static NamespaceManager CreateNamespaceManager()
                {
                    // Cree el administrador del espacio de nombres que le da acceso a las
                    // operaciones de administración
                    var uri = ServiceBusEnvironment.CreateServiceUri(
                        "sb", Namespace, String.Empty);
                    var tP = TokenProvider.CreateSharedSecretTokenProvider(
                        IssuerName, IssuerKey);
                    return new NamespaceManager(uri, tP);
                }

                public static void Initialize()
                {
                    // Se utiliza Http por compatibilidad con los firewalls salientes
                    ServiceBusEnvironment.SystemConnectivity.Mode = 
                        ConnectivityMode.Http;

                    // Cree el administrador del espacio de nombres que le da acceso a las 
                    // operaciones de administración
                    var namespaceManager = CreateNamespaceManager();

                    // Cree la cola si todavía no existe.
                    if (!namespaceManager.QueueExists(QueueName))
                    {
                        namespaceManager.CreateQueue(QueueName);
                    }

                    // Obtener un cliente para la cola
                    var messagingFactory = MessagingFactory.Create(
                        namespaceManager.Address, 
                        namespaceManager.Settings.TokenProvider);
                    OrdersQueueClient = messagingFactory.CreateQueueClient(
                        "OrdersQueue");
                }
            }
        }

4.  Ahora, se asegurará de que se llame al método **Initialize**. En el **Explorador de soluciones**, haga doble clic en **Global.asax\\Global.asax.cs**.

5.  Añada la línea siguiente a la parte inferior del método **Application\_Start**:

        FrontendWebRole.QueueConnector.Initialize();

6.  Finalmente, va a actualizar el código web que ha creado anteriormente para enviar elementos a la cola. En el **Explorador de soluciones**, haga doble clic en **Controllers\\HomeController.cs** que ha creado antes.

7.  Actualice el método **Submit()** de la forma siguiente para obtener el contador de mensajes para la cola:

        public ActionResult Submit()
        {            
            // Obtenga un NamespaceManager que le permite realizar la administración y
            // las operaciones de diagnóstico en sus colas del bus de servicio.
            var namespaceManager = QueueConnector.CreateNamespaceManager();

            // Obtenga la cola y obtenga el contador de mensajes.
            var queue = namespaceManager.GetQueue(QueueConnector.QueueName);
            ViewBag.MessageCount = queue.MessageCount;

            return View();
        }

8.  Actualice el método **Submit(OnlineOrder order)** de la forma siguiente para enviar información de pedido a la cola:

        public ActionResult Submit(OnlineOrder order)
        {
            if (ModelState.IsValid)
            {
                // Cree un mensaje desde el pedido
                var message = new BrokeredMessage(order);
                    
                // Envíe el pedido
                QueueConnector.OrdersQueueClient.Send(message);
                return RedirectToAction("Submit");
            }
            else
            {
                return View(order);
            }
        }

9.  Ahora puede volver a ejecutar la aplicación. Cada vez que envía un pedido, el contador de mensajes aumenta.

    ![](./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-37.png)

Administrador de configuraciónAdministrador de configuración de nube
--------------------------------------------------------------------

Azure admite un conjunto de API administradas que ofrece una forma coherente de crear nuevas instancias de clientes de servicio de Azure (como el bus de servicio) en los servicios en la nube de Microsoft. Estas API le permiten crear instancias de estos clientes (por ejemplo, **CloudBlobClient**, **QueueClient**, **TopicClient**) con independencia de dónde se hospede la aplicación; en local, en un servicio en la nube de Microsoft, en sitios web o en un rol de VM persistente. También puede usar estas API para recuperar la información de la configuración necesaria para crear una instancia de estos clientes y cambiar la configuración sin tener que volver a implementar la aplicación de llamada. Las API están ubicadas en la clase [Microsoft.WindowsAzure.Configuration.CloudConfigurationManager][]. También hay API en el cliente.

### Cadena de conexión

Para crear una instancia de un cliente (por ejemplo una **QueueClient** del bus de servicio), puede representar la información de configuración como una cadena de conexión. En el cliente, hay un método **CreateFromConnectionString()** que crea una instancia del tipo de cliente utilizando esa cadena de conexión. Por ejemplo, dada la sección de configuración siguiente:

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

    // Inicialice la conexión en la cola del bus de servicio
    Client = QueueClient.CreateFromConnectionString(connectionString, QueueName);

El código de la sección siguiente utiliza estas API de administración de configuración.

Creación del rol de trabajoCreación del rol de trabajo
------------------------------------------------------

Ahora creará el rol de trabajo que procesa los envíos del pedido. Este ejemplo utiliza la plantilla de proyecto de Visual Studio de **Worker Role with Service Bus Queue**. En primer lugar, utilizará el Explorador de servidores en Visual Studio para obtener las credenciales requeridas.

1.  Si ya ha conectado Visual Studio a su cuenta de Azure, tal como se ha descrito en la sección **Configuración del espacio de nombres mediante el Explorador de servidores de Visual Studio**, vaya al paso 5.

2.  En la barra de menús de Visual Studio, elija **Ver** y, a continuación, haga clic en **Explorador de servidores**. Un nodo **Bus de servicio** aparece en **Azure** dentro la jerarquía del Explorador de servicios, como en la figura siguiente.

    ![](./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/SBExplorer.png)

3.  En el Explorador de servidores, expanda **Azure**, haga clic con el botón secundario en **Bus de servicio** y, a continuación, en **Agregar nueva conexión**.

4.  En el cuadro de diálogo **Agregar conexión**, escriba el nombre del espacio de nombres del servicio, el nombre del emisor y la clave del emisor. A continuación, haga clic en **Aceptar** para conectarse.

    ![](./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/SBExplorerAddConnect.png)

5.  En Visual Studio, en el **Explorador de soluciones**, haga clic con el botón secundario en la carpeta **Roles** en el proyecto **MultiTierApp**.

6.  Seleccione **Add** y, a continuación, haga clic en **Nuevo proyecto de rol de trabajador**. Aparecerá el cuadro de diálogo **Agregar nuevo proyecto de rol**.

    ![](./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/SBNewWorkerRole.png)

7.  En el cuadro de diálogo **Agregar nuevo proyecto de rol**, haga clic en **Worker Role with Service Bus Queue**, como en la figura siguiente:

    ![](./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/SBWorkerRole1.png)

8.  En el cuadro **Name**, asigne el nombre de **OrderProcessingRole** al proyecto. A continuación, haga clic en **Add**.

9.  En el Explorador de servidores, haga clic con el botón secundario en el espacio de nombres del servicio y, a continuación, haga clic en **Propiedades**. En el panel **Propiedades** de Visual Studio, la primera entrada contiene una cadena de conexión que se rellena con el extremo del espacio de nombres del servicio que contiene las credenciales de autorización requeridas. Por ejemplo, consulte la figura siguiente. Haga doble clic en **ConnectionString** y, a continuación, presione **Ctrl+C** para copiar esta cadena en el Portapapeles.

    ![](./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/SBExplorerProperties.png)

10. En el Explorador de soluciones, haga clic con el botón secundario en **OrderProcessingRole** que ha creado en el paso 7 (asegúrese de que hace clic con el botón secundario en **OrderProcessingRole** en **Roles** y no en la clase). A continuación, haga clic en **Propiedades**.

11. En la pestaña **Configuración** del cuadro de diálogo **Propiedades**, haga clic dentro del cuadro **Valor** para **Microsoft.ServiceBus.ConnectionString** y, a continuación, pegue el valor del extremo que ha copiado en el paso 8.



	![][25]

1.  Cree una clase **OnlineOrder** para representar los pedidos cuando los procese de la cola. Puede reutilizar una clase que ya ha creado. En el Explorador de soluciones, haga clic con el botón secundario en el proyecto **OrderProcessingRole** (haga clic con el botón secundario en el proyecto, no en el rol). Seleccione **Add** y, a continuación, haga clic en **Elemento existente**.

2.  Busque **FrontendWebRole\\Models** en la subcarpeta y haga doble clic en **OnlineOrder.cs** para agregarla a este proyecto.

3.  En WorkerRole.cs, reemplace el valor de la variable **QueueName** en **WorkerRole.cs** de "ProcessingQueue" por "OrdersQueue" como en el código siguiente:

        // El nombre de la cola
        const string QueueName = "OrdersQueue";

4.  Agregue la siguiente instrucción using en la parte superior del archivo WorkerRole.cs:

        using FrontendWebRole.Models;

5.  En la función Run(), dentro de la llamada OnMessage, agregue el código siguiente dentro de la cláusula try:

        Trace.WriteLine("Processing", receivedMessage.SequenceNumber.ToString());
        // Considere el mensaje como un OnlineOrder
        OnlineOrder order = receivedMessage.GetBody<OnlineOrder>();
        Trace.WriteLine(order.Customer + ": " + order.Product, "ProcessingMessage");
        receivedMessage.Complete();

6.  Ha completado la aplicación. Puede probar la aplicación completa como ha hecho antes presionando F5. Tenga en cuenta que el contador de mensajes no se aumenta, ya que el rol de trabajo procesa los elementos de la cola y los marca como finalizados. Puede ver el resultado de seguimiento de su rol de trabajo viendo la interfaz de usuario del emulador de proceso de Azure. Para ello, haga clic con el botón secundario en el icono del emulador del área de notificación de la barra de tareas y seleccione **Show Compute Emulator UI**.



	![][19]

	![][20]

Pasos siguientesPasos siguientes
--------------------------------

Para obtener más información sobre el bus de servicio, consulte los siguientes recursos:

-   [Service Bus](http://msdn.microsoft.com/es-es/library/windowsazure/ee732537.aspx)
-   [Procedimientos del bus de servicio](/es-es/manage/services/service-bus/)
-   [Utilización de las colas del bus de servicio](/es-es/develop/net/how-to-guides/service-bus-queues/)

Para obtener más información sobre los escenarios de niveles múltiples o sobre cómo implementar una aplicación en un servicio en la nube, consulte:

-   [Aplicación .NET de niveles múltiples utilizando tablas, colas y blobs de almacenamiento](/es-es/develop/net/tutorials/multi-tier-web-site/1-overview/)

Es posible que desee implementar el front-end de una aplicación de niveles múltiples en un sitio web de Azure en lugar de en un servicio en la nube de Azure. Para obtener más información sobre la diferencia entre sitios web y servicios en la nube, consulte [Modelos de ejecución de Azure](http://www.windowsazure.com/es-es/develop/net/fundamentals/compute/).

Para implementar la aplicación que ha creado en este tutorial como un proyecto web estándar en lugar de como un rol web de servicio en la nube, siga los pasos de este tutorial con las diferentes siguientes:

1.  Cuando crea el proyecto, elija la plantilla del proyecto **Aplicación web de ASP.NET MVC 4** en la categoría **Web** en lugar de la plantilla **Servicio en la nube** en la categoría **Nube**. A continuación, siga las mismas instrucciones para crear la aplicación MVC hasta que llegue a la sección **Administrador de configuración de nube**.

2.  Cuando crea el rol de trabajo, créelo de una nueva solución independiente, similar a las instrucciones originales para el rol web. Tenga en cuenta, sin embargo, que está creando solo el rol de trabajo en el proyecto del servicio en la nube. A continuación, siga las mismas instrucciones para crear el rol de trabajo.

3.  Puede probar por separado el front-end y el back-end, o bien puede ejecutar ambos simultáneamente en instancias separadas de Visual Studio.

Para obtener información sobre cómo implementar el front-end en un sitio web de Azure, consulte [Implementación de una aplicación web ASP.NET a un sitio web de Azure](http://www.windowsazure.com/es-es/develop/net/tutorials/get-started/). Para obtener información sobre cómo implementar el back-end en un servicio en la nube de Azure, consulte [Aplicación .NET de niveles múltiples utilizando tablas, colas y blobs de almacenamiento](/es-es/develop/net/tutorials/multi-tier-web-site/1-overview/).

  [0]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-01.png
  [1]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-100.png
  [sbqueuecomparison]: http://msdn.microsoft.com/es-es/library/windowsazure/hh767287.aspx
  [2]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-101.png
  [Get Tools and SDK]: http://go.microsoft.com/fwlink/?LinkId=271920
  [3]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-3.png
  
  
  
  [Azure Management Portal]: http://manage.windowsazure.com
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
  [sbmsdn]: http://msdn.microsoft.com/es-es/library/windowsazure/ee732537.aspx  
  [sbwacom]: /es-es/manage/services/service-bus/  
  [sbwacomqhowto]: /es-es/develop/net/how-to-guides/service-bus-queues/  
  [mutitierstorage]: /es-es/develop/net/tutorials/multi-tier-web-site/1-overview/ 
  [executionmodels]: http://www.windowsazure.com/es-es/develop/net/fundamentals/compute/
