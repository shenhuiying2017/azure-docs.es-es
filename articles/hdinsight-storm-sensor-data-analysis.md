<properties 
	pageTitle="Análisis de los datos de sensor con Apache Storm y Microsoft Azure HDInsight (Hadoop)" 
	description="Aprenda a usar  Apache Storm para procesar datos de sensor en tiempo real con HDInsight (Hadoop)" 
	services="hdinsight" 
	documentationCenter="" 
	authors="blackmist" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/30/2014" 
	ms.author="larryfr"/>

# Análisis de los datos de sensor con Storm y HBase en HDInsight (Hadoop)

Aprenda a compilar una solución que use un clúster de Storm en HDInsight para procesar los datos de sensor de los centros de eventos de Azure. Durante el procesamiento, la topología de Storm almacenará datos entrantes en un clúster de HBase. La topología también utilizará SignalR para proporcionar información casi en tiempo real mediante un panel de control basado en web hospedado en Sitios web de Azure.

> [AZURE.NOTE] En [https://github.com/Blackmist/hdinsight-eventhub-example](https://github.com/Blackmist/hdinsight-eventhub-example) hay disponible una versión completa de este proyecto.

## Requisitos previos

* Una suscripción de Azure

* Visual Studio con el [SDK de Microsoft Azure para .NET](http://azure.microsoft.com/es-es/downloads/archive-net-downloads/)

* [Java y JDK](http://www.oracle.com/technetwork/java/javase/downloads/index.html)

* [Maven](http://maven.apache.org/what-is-maven.html)

* [Git](http://git-scm.com/)

> [AZURE.NOTE] Java, JDK, Maven y Git también están disponibles en el administrador de paquetes [Chocolatey NuGet](http://chocolatey.org/).

## Creación del panel

El panel se utiliza para mostrar información del sensor casi en tiempo real. En este caso, el panel es una aplicación de ASP.NET hospedada en un sitio web de Azure. El objetivo principal de la aplicación es que sirva como concentrador de [SignalR](http://www.asp.net/signalr/overview/getting-started/introduction-to-signalr) que recibe información de la topología de Storm a medida que se procesan los mensajes.

El sitio web también contiene un archivo estático index.html, que también se conecta a Signal R y utiliza D3.js para realizar un gráfico de los datos transmitidos por la topología Storm.

> [AZURE.NOTE] Aunque también puede utilizar WebSockets sin procesar en lugar de SignalR, WebSockets no proporciona un mecanismo de escala integrado si necesita escalar horizontalmente el sitio web. SignalR se puede escalar mediante el Bus de servicio de Azure ([http://www.asp.net/signalr/overview/performance/scaleout-with-windows-azure-service-bus](http://www.asp.net/signalr/overview/performance/scaleout-with-windows-azure-service-bus)).
>
> Para ver un ejemplo sobre cómo usar una topología Storm para comunicarse con un sitio web Python utilizando WebSockets sin procesar, consulte el proyecto [Storm Tweet Sentiment D3 Visualization](https://github.com/P7h/StormTweetsSentimentD3Viz).

1. En Visual Studio, cree una nueva aplicación C# utilizando la plantilla de proyecto **Aplicación web ASP.NET**. Llame a la nueva aplicación **Panel**.

2. En la ventana **Nuevo proyecto de ASP.NET**, seleccione la plantilla de aplicación **vacía**. En la sección **Microsoft Azure**, seleccione **Host en la nube** y **Sitio web**. Por último, haga clic en **Aceptar**.

	> [AZURE.NOTE] Si se le indica, inicie sesión en su suscripción Azure.

3. En el cuadro de diálogo **Configurar sitio de Microsoft Azure**, escriba un **nombre de sitio** y una **región** para el sitio web y, a continuación, haga clic en **Aceptar**. Se creará un Sitio web de Azure que albergará el panel.

4. En el **Explorador de soluciones**, haga clic con el botón secundario en el proyecto y luego seleccione **Agregar | SignalR Hub Class (v2)**. Llame a la clase **DashHub.cs** y agréguela al proyecto. Contendrá el concentrador SignalR que se utiliza para comunicar datos entre HDInsight y la página web del panel.

	> [AZURE.NOTE] Si está usando Visual Studio 2012, la plantilla **SignalR Hub Class (v2)** no estará disponible. Puede agregar una **clase** sin formato llamada DashHub. También tendrá que instalar manualmente el paquete SignalR. Para ello, abra **Herramientas | Administrador de paquetes de la biblioteca | Consola del administrador de paquetes** y ejecute el siguiente comando:
	>
	> `install-package Microsoft.AspNet.SignalR`

5. Reemplace el código de **DashHub.cs** por el siguiente:

		using System;
		using System.Collections.Generic;
		using System.Linq;
		using System.Web;
		using Microsoft.AspNet.SignalR;

		namespace dashboard
		{
		    public class DashHub : Hub
		    {
		        public void Send(string message)
		        {
		            // Call the broadcastMessage method to update clients.
		            Clients.All.broadcastMessage(message);
		        }
		    }
		}

6. En el **Explorador de soluciones**, haga clic con el botón secundario en el proyecto y luego seleccione **Agregar | Clase de inicio OWIN**. Llame a la nueva clase **Startup.cs**.

	> [AZURE.NOTE] Si está usando Visual Studio 2012, la plantilla **Clase de inicio OWIN** no estará disponible. Puede crear en su lugar una **clase** llamada Startup.

7. Reemplace el contenido de **Startup.cs** por lo siguiente:

		using System;
		using System.Threading.Tasks;
		using Microsoft.Owin;
		using Owin;

		[assembly: OwinStartup(typeof(dashboard.Startup))]

		namespace dashboard
		{
		    public class Startup
		    {
		        public void Configuration(IAppBuilder app)
		        {
		            // For more information on how to configure your application, visit http://go.microsoft.com/fwlink/?LinkID=316888
		            app.MapSignalR();
		        }
		    }
		}

8. En el **Explorador de soluciones**, haga clic con el botón secundario en el proyecto y, a continuación, haga clic en **Agregar | Página HTML**. Llame a la nueva página **index.html**. Esta página contiene el panel en tiempo real de este proyecto. Recibirá información de DashHub y mostrará un gráfico utilizando D3.js.

9. En el **Explorador de soluciones**, haga clic con el botón secundario en **index.html** y seleccione **Establecer como página principal**.

10. Reemplace el código del archivo **index.html** por el siguiente:

		<!DOCTYPE html>
		<html xmlns="http://www.w3.org/1999/xhtml">
		<head>
		    <title>Dashboard</title>
		    <style>

		        .x.axis line {
		            shape-rendering: auto;
		        }

		        .line {
		            fill: none;
		            stroke-width: 1.5px;
		        }

		    </style>
		    <!--Script references. -->
		    <!--Reference the jQuery library. -->
		    <script src="Scripts/jquery-1.10.2.min.js"></script>
		    <!--Reference the SignalR library. -->
		    <script src="Scripts/jquery.signalR-2.0.2.min.js"></script>
		    <!--Reference the autogenerated SignalR hub script. -->
		    <script src="signalr/hubs"></script>
		    <!--Reference d3.js.-->
		    <script src="http://d3js.org/d3.v3.min.js"></script>
		</head>
		<body>
		    <script>
		        $(function () {
		            //Huge thanks to Mike Bostok for his Path Transitions article - http://bost.ocks.org/mike/path/
		            var n = 243,                                 //number of x coordinates in the graph
		            duration = 750,                          //duration for transitions
		            deviceValue=[0,0,0,0,0,0,0,0,0,0],       //temp holding for each device value
		            now = new Date(Date.now() - duration),   //Now
		            //fill an array of arrays with dummy data to start the chart
		            //each item in the top-level array is a line
		            //each item in the line arrays represents the X coordinate across a graph
		            //The 'value' within each line array represents the Y coordinate for that point
		            data = [
		                d3.range(n).map(function () { return { value: 0 }; }),
		                d3.range(n).map(function () { return { value: 0 }; }),
		                d3.range(n).map(function () { return { value: 0 }; }),
		                d3.range(n).map(function () { return { value: 0 }; }),
		                d3.range(n).map(function () { return { value: 0 }; }),
		                d3.range(n).map(function () { return { value: 0 }; }),
		                d3.range(n).map(function () { return { value: 0 }; }),
		                d3.range(n).map(function () { return { value: 0 }; }),
		                d3.range(n).map(function () { return { value: 0 }; }),
		                d3.range(n).map(function () { return { value: 0 }; }),
		                d3.range(n).map(function () { return { value: 0 }; })
		            ];

		            //Color scale for 10 items
		            var color = d3.scale.category10();
		            //The domain for color (the device IDs)
		            var devices = ["0", "1", "2", "3", "4", "5", "6", "7", "8", "9"];
		            //This will auto-generate colors for this range of IDs
		            color.domain(devices);

		            //set margins and figure out width/height
		            var margin = {top: 6, right: 0, bottom: 20, left: 40},
		                width = 960 - margin.right,
		                height = 240 - margin.top - margin.bottom;

		            //the time scale for the X axis
		            var x = d3.time.scale()
		                .domain([now - (n - 2) * duration, now - duration])
		                .range([0, width]);

		            //the numerical scale for the Y axis
		            var y = d3.scale.linear()
		                .domain([100, 0])
		                .range([0, height]);

		            //The line, which is really just a
		            //couple functions that we can pass data to
		            //in order to get back x/y coords.
		            var line = d3.svg.line()
		                .interpolate("basis")
		                .x(function (d, i) { return x(now - (n - 1 - i) * duration); })
		                .y(function (d, i) { return y(d.value); });

		            //Find the HTML body element and add a child SVG element
		            var svg = d3.select("body").append("svg")
		                .attr("width", width + margin.left + margin.right)
		                .attr("height", height + margin.top + margin.bottom)
		              .append("g")
		                .attr("transform", "translate(" + margin.left + "," + margin.top + ")");

		            //Define a clipping path, because we need to clip
		            //the graph to render only the bits we want to see
		            //as it moves
		            svg.append("defs").append("clipPath")
		                .attr("id", "clip")
		              .append("rect")
		                .attr("width", width)
		                .attr("height", height);

		            //Append the x axis
		            var axis = svg.append("g")
		                .attr("class", "x axis")
		                .attr("transform", "translate(0," + height + ")")
		                .call(x.axis = d3.svg.axis().scale(x).orient("bottom"));

		            //append the y axis
		            var yaxis = svg.append("g")
		                .attr("class", "y axis")
		                .call(y.axis = d3.svg.axis().scale(y).orient("left").ticks(5));

		            //append the clipping path
		            var linegroup = svg.append("g")
		              .attr("clip-path", "url(#clip)");

		            //magic. Select all paths with a class of .line
		            //if they don't exist, make them.
		            //use the points in the line object to define
		            //the paths
		            //set the color to the cooresponding auto-generated coclor
		            var path = linegroup.selectAll(".line")
		              .data(data)
		              .enter().append("path")
		              .attr("class", "line")
		              .attr("d", line)
		              .style("stroke", function (d, i) { return color(i); });

		            //We need to transition the graph after all
		            //lines have been updated. There's no
		            //built-in for this, so this function
		            //does reference counting on end events
		            //for each line, then applies whatever
		            //callback when all are finished.
		            function endall(transition, callback) {
		                var n = 0;
		                transition
		                    .each(function () { ++n; })
		                    .each("end", function () { if (!--n) callback.apply(this, arguments); });
		            }

		            //wire up the SignalR client and listen for messages
		            var chat = $.connection.dashHub;
		            chat.client.broadcastMessage = function (message) {
		                //parse the JSON data
		                var incomingData = JSON.parse(message);
		                //stuff it in the global array slot for the device ID
		                deviceValue[incomingData.device] = incomingData.temperature;

		            };
		            //start listening
		            $.connection.hub.start();
		            //tick for D3 graphics
		            tick();

		            function tick() {
		                // update the domains
		                now = new Date();
		                x.domain([now - (n - 2) * duration, now - duration]);

		                //push the (presumably) fresh data deviceValue array onto
		                //the arrays that define the lines.
		                for (i = 0; i < 10; i++) {
		                    data[i].push({ value: deviceValue[i] });
		                    //data[1].push({ value: maxValue });
		                }
		                //slide the x-axis left
		                axis.transition()
		                    .duration(duration)
		                    .ease("linear")
		                    .call(x.axis);

		                //Update the paths based on the updated line data
		                //and slide left
		                path
		                    .attr("d", line)
		                    .attr("transform", null)
		                .transition()
		                    .duration(duration)
		                    .ease("linear")
		                    .attr("transform", "translate(" + x(now - (n - 1) * duration) + ",0)")
		                    .call(endall, tick);

		                // pop the old data point off the front
		                // of the arrays
		                for (var i = 0; i < data.length; i++) {
		                    data[i].shift();
		                };
		            };
		         })()
		        </script>
		    </body>
		</html>

	> [AZURE.NOTE] El administrador de paquetes puede instalar una versión posterior de los scripts de SignalR. Compruebe que las referencias al script siguientes se corresponden a las versiones de los archivos de script en el proyecto (serán diferentes si ha agregado SignalR mediante NuGet en lugar de agregar un concentrador).

11. En el **Explorador de soluciones**, haga clic con el botón secundario en el proyecto y, a continuación, haga clic en **Agregar | Página HTML**. Llame a la nueva página **test.html**. Esta página puede usarse para probar DashHub y el panel mediante el envío y recepción de mensajes.

12. Reemplace el código del archivo **test.html** por el siguiente:

		<!DOCTYPE html>
		<html>
		<head>
		    <title>Test</title>
		    <style type="text/css">
		        .container {
		            background-color: #99CCFF;
		            border: thick solid #808080;
		            padding: 20px;
		            margin: 20px;
		        }
		    </style>
		</head>
		<body>
		    <div class="container">
		        <input type="text" id="message" />
		        <input type="button" id="sendmessage" value="Send" />
		        <input type="hidden" id="displayname" />
		        <ul id="discussion"></ul>
		    </div>
		    <!--Script references. -->
		    <!--Reference the jQuery library. -->
		    <script src="Scripts/jquery-1.10.2.min.js"></script>
		    <!--Reference the SignalR library. -->
		    <script src="Scripts/jquery.signalR-2.0.2.min.js"></script>
		    <!--Reference the autogenerated SignalR hub script. -->
		    <script src="signalr/hubs"></script>
		    <!--Add script to update the page and send messages.-->
		    <script type="text/javascript">
		        $(function () {
		            // Declare a proxy to reference the hub.
		            var chat = $.connection.dashHub;
		            // Create a function that the hub can call to broadcast messages.
		            chat.client.broadcastMessage = function (message) {
		                // Html encode display the message.
		                var encodedMsg = $('<div />').text(message).html();
		                // Add the message to the page.
		                $('#discussion').append('<li>' + encodedMsg + '</li>');
		            };
		            // Set initial focus to message input box.
		            $('#message').focus();
		            // Start the connection.
		            $.connection.hub.start().done(function () {
		                $('#sendmessage').click(function () {
		                    // Call the Send method on the hub.
		                    chat.server.send($('#message').val());
		                    // Clear text box and reset focus for next comment.
		                    $('#message').val('').focus();
		                });
		            });
		        });
		    </script>
		</body>
		</html>

13. Seleccione **Guardar todo** para el proyecto.

14. En el **Explorador de soluciones**, haga clic con el botón secundario en el proyecto del **Panel** y seleccione **Publicar**. Seleccione el sitio web que ha creado para este proyecto y, a continuación, haga clic en **Publicar**.

15. Una vez publicado el sitio, se abre una página web mostrando una escala de tiempo móvil.

### Prueba del panel

1. Para comprobar que SignalR funciona y que el panel mostrará líneas de gráficos para los datos enviados a SignalR, abra una nueva ventana del explorador a la página **test.html** de este sitio web. Por ejemplo, **http://mydashboard.azurewebsites.net/test.html**.

2. El panel espera datos en formato JSON, con un valor para **device id** y para **temperature**. Por ejemplo **{"device":0, "temperature":80}**. Introduzca algunos valores de prueba en la página **test.html**, utilizando los identificadores 0 a 9, mientras el panel está abierto en otra página. Tenga en cuenta que las líneas de cada identificador de dispositivo se trazan con un color diferente.

## Configuración del centro de eventos

El centro de eventos se utiliza para recibir mensajes (eventos) de los sensores. Utilice los pasos siguientes para crear un centro de eventos.

1. En el [Portal de Azure](https://manage.windowsazure.com), seleccione **NUEVO | Bus de servicio | Centro de eventos | Creación personalizada**.

2. En el cuadro de diálogo **Agregar un nuevo centro de eventos**, escriba un **nombre del centro de eventos**, seleccione la **región** donde se va a crear el centro y cree un nuevo espacio de nombres o seleccione uno existente. Por último, haga clic en la **flecha**.

3. En el cuadro de diálogo **Configuración del centro de eventos**, escriba los valores **Recuento de particiones** y **Retención de mensajes**. En este ejemplo, utilice un recuento de particiones de 10 y una retención de mensajes de 1.

4. Una vez creado el centro de eventos, seleccione el espacio de nombres y, a continuación, seleccione **Centros de eventos**. Finalmente, seleccione el centro de eventos que ha creado antes.

5. Seleccione **Configurar** y, a continuación, cree dos directivas de acceso con la información siguiente:

	<table>
	<tr><th>Nombre</th><th>Permisos</th></tr>
	<tr><td>devices</td><td>Enviar</td></tr>
	<tr><td>storm</td><td>Escuchar</td></tr>
	</table>

	Después de crear los permisos, seleccione el icono **Guardar** en la parte inferior de la página. Se crean las directivas de acceso compartidas que se utilizarán para enviar mensajes a este concentrador, así como para leer mensajes de él.

6. Después de guardar las directivas, use el **Generador de claves de acceso compartido** en la parte inferior de la página para recuperar la clave de las directivas **devices** y **storm**. Guárdelas ya que se utilizarán después.

### Envío de mensajes al centro de eventos

Ya que no hay ningún conjunto fácil ni estándar de sensores disponibles para todos, se utilizará una aplicación .NET para generar números aleatorios. La aplicación .NET creada mediante los pasos siguientes generará eventos para 10 dispositivos cada segundo, hasta que detenga la aplicación presionando una tecla.

1. En Visual Studio, cree un nuevo proyecto de **Escritorio de Windows** y seleccione la plantilla de proyecto **Aplicación de consola**. Llame al proyecto **SendEvents** y luego haga clic en **Aceptar**.

2. En el **Explorador de soluciones**, haga clic con el botón secundario en **SendEvents** y, a continuación, seleccione **Administrar paquetes de NuGet**.

3. En **Administrar paquetes de NuGet**, busque e instale los paquetes siguientes:

	* **Bus de servicio de Microsoft Azure**
	* **JSON.Net**

	Una vez instalados los paquetes, **cierre** el administrador de paquetes.

4. Reemplace el contenido de **Program.cs** por lo siguiente:

		using System;
		using System.Collections.Generic;
		using System.Linq;
		using System.Text;
		using System.Threading.Tasks;
		using Microsoft.ServiceBus.Messaging;
		using Newtonsoft.Json;
		using Microsoft.ServiceBus;
		using System.Threading;

		namespace SendEvents
		{
		    class Program
		    {

		        static int numberOfDevices = 10;
		        static string eventHubName = "temperature";
		        static string eventHubNamespace = "namespace";
		        static string sharedAccessPolicyName = "devices";
		        static string sharedAccessPolicyKey = "key for devices policy";

		        static void Main(string[] args)
		        {
		            var settings = new MessagingFactorySettings()
		            {
		                TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider(sharedAccessPolicyName, sharedAccessPolicyKey),
		                TransportType = TransportType.Amqp
		            };
		            var factory = MessagingFactory.Create(
		                 ServiceBusEnvironment.CreateServiceUri("sb", eventHubNamespace, ""), settings);

		            EventHubClient client = factory.CreateEventHubClient(eventHubName);

		            try
		            {

		                List<Task> tasks = new List<Task>();
		                // Send messages to Event Hub
		                Console.WriteLine("Sending messages to Event Hub {0}", client.Path);
		                Random random = new Random();
		                //for (int i = 0; i < numberOfMessages; ++i)
		                while(!Console.KeyAvailable)
		                {
		                    // One event per device
		                    for(int devices = 0; devices < numberOfDevices; devices++)
		                    {
		                        // Create the device/temperature metric
		                        Event info = new Event() {
		                            TimeStamp = DateTime.UtcNow,
		                            DeviceId = random.Next(numberOfDevices),
		                            Temperature = random.Next(100)
		                        };
		                        // Serialize to JSON
		                        var serializedString = JsonConvert.SerializeObject(info);
		                        Console.WriteLine(serializedString);
		                        EventData data = new EventData(Encoding.UTF8.GetBytes(serializedString))
		                        {
		                            PartitionKey = info.DeviceId.ToString()
		                        };

		                        // Send the metric to Event Hub
		                        tasks.Add(client.SendAsync(data));
		                    }
		                    // Sleep a second
		                    Thread.Sleep(1000);
		                };

		                Task.WaitAll(tasks.ToArray());
		            }
		            catch (Exception exp)
		            {
		                Console.WriteLine("Error on send: " + exp.Message);
		            }

		        }
		    }
		}

	Ahora recibirá una advertencia sobre las líneas que hacen referencia a la clase Event. Ignórelas de momento.

5. En el archivo **Program.cs**, establezca el valor de las variables siguientes al comienzo del archivo en los valores correspondientes recuperados del Centro de eventos del Portal de administración de Azure.

	<table>
	<tr><th>Establezca esto...</th><th>Para esto...</th></tr>
	<tr><td>eventHubName</td><td>El nombre el centro de eventos. Por ejemplo: <strong>temperature</strong>.</td></tr>
	<tr><td>eventHubNamespace</td><td>El espacio de nombres del centro de eventos. Por ejemplo: <strong>sensors-ns</strong>.</td></tr>
	<tr><td>sharedAccessPolicyName</td><td>La directiva creada con acceso de envío. Por ejemplo: <strong>devices</strong>.</td></tr>
	<tr><td>sharedAccessPolicyKey</td><td>La clave de la directiva con acceso de envío.</td></tr>
	</table>

6. En el **Explorador de soluciones**, haga clic con el botón secundario en **SendEvents** y seleccione **Agregar | Clase**. Llame a la nueva clase **Event.cs**. Describirá el mensaje enviado al Centro de eventos.

7. Reemplace el contenido de **Event.cs** por lo siguiente:

		using System;
		using System.Collections.Generic;
		using System.Linq;
		using System.Runtime.Serialization;
		using System.Text;
		using System.Threading.Tasks;

		namespace SendEvents
		{
		    [DataContract]
		    public class Event
		    {
		    	[DataMember]
		    	public DateTime TimeStamp { get; set; }
		        [DataMember]
		        public DateTime TimeStamp { get; set; }
		        [DataMember]
		        public int DeviceId { get; set; }
		        [DataMember]
		        public int Temperature { get; set; }
		    }
		}

	Esta clase describe los datos que estamos enviando: un valor TimeStamp, DeviceID y Temperature.

8. Seleccione **Guardar todo** y ejecute la aplicación para rellenar el Centro de eventos con mensajes.

## Creación de una red virtual de Azure

Para que la topología que se ejecuta en el clúster de Storm se comunique directamente con HBase, debe aprovisionar ambos servidores en una red virtual de Azure.

1. Inicie sesión en el [Portal de administración de Azure][azure-portal].

2. En la parte inferior de la página, haga clic en **+NUEVO**, **Servicios de red**, **Red virtual** y después en **Creación rápida**.

3. Escriba o seleccione los valores siguientes:

	- **Nombre**: El nombre de la red virtual.
	- **Espacio de direcciones**: elija un espacio de direcciones para la red virtual lo bastante grande para proporcionar direcciones para todos los nodos del clúster. De lo contrario, se producirá un error en el aprovisionamiento.
	- **Número máximo de VM**: elija uno de los recuentos de VM máximos.
	- **Ubicación**: la ubicación debe ser la misma que el clúster de HBase que creará.
	- **Servidor DNS**: en este artículo se usa un servidor DNS interno proporcionado por Azure, de modo que puede elegir **Ninguno**. Asimismo, se admite una configuración de red más avanzada con servidores DNS personalizados. Para obtener instrucciones detalladas, consulte [http://msdn.microsoft.com/library/azure/jj156088.aspx](http://msdn.microsoft.com/library/azure/jj156088.aspx).

4. Haga clic en **Crear una red virtual**. El nombre de la nueva red virtual aparecerá en la lista. Espere hasta que aparezca **Creado** en la columna de estado.

5. En el panel principal, haga clic en la red virtual que acaba de crear.

6. En la parte superior de la página, haga clic en **PANEL**.

7. En **vista rápida**, tome nota del **ID. DE RED VIRTUAL**. Lo necesitará al aprovisionar los clústeres de Storm y de HBase.

8. En la parte superior de la página, haga clic en **CONFIGURAR**.

9. En la parte inferior de la página, el nombre de subred predeterminado es **Subred-1**. Use el botón **agregar subred** para agregar **Subred-2**. Estas subredes albergarán los clústeres de Storm y de HBase.

	> [AZURE.NOTE] En este artículo, usaremos los clústeres con solo un nodo. Si está creando clústeres de varios nodos, debe comprobar el valor de **CIDR(RECUENTO DE DIRECCIONES)** de la subred que se utilizará para el clúster. El recuento de direcciones debe ser mayor que el número de nodos de trabajo más siete (puerta de enlace: 2, nodo principal: 2, Zookeeper: 3). Por ejemplo, si necesita un clúster de HBase de 10 nodos, el recuento de direcciones para la subred debe ser mayor que 17 (10+7). De lo contrario, se producirá un error en la implementación.
	>
	> Se recomienda encarecidamente designar una única subred para un clúster.

11. Haga clic en **Guardar** en la parte inferior de la página.

## Creación del clúster de Storm de HDInsight

1. Inicie sesión en el [Portal de Administración de Azure][azureportal].

2. Haga clic en **HDInsight** a la izquierda y, a continuación, en **+NUEVO** en la esquina inferior izquierda de la página.

3. Haga clic en el icono de HDInsight en la segunda columna y después seleccione **Personalizado**.

4. En la página **Detalles del clúster**, escriba el nombre del nuevo clúster y seleccione **Storm** como **Tipo de clúster**. Seleccione la flecha para continuar.

5. Especifique 1 como el número de **Nodos de datos** para usar en este clúster. En **Región/Red virtual**, seleccione la red virtual de Azure creada anteriormente. En **Subredes de la red virtual**, seleccione **Subred-2**.

	> [AZURE.NOTE] Para minimizar el coste del clúster usado en este artículo, reduzca el **Tamaño del clúster** a 1 y elimine el clúster cuando haya terminado de usarlo.

6. Escriba el **Nombre de usuario** y la **Contraseña** del administrador y luego seleccione la flecha para continuar.

7. En **Cuenta de almacenamiento**, seleccione **Crear nuevo almacenamiento** o seleccione una cuenta de almacenamiento existente. Seleccione o escriba el **Nombre de cuenta** y el **Contenedor predeterminado** que se va a usar. Haga clic en el icono de verificación en la parte inferior izquierda para crear el clúster de Storm.

## Creación del clúster de HBase de HDInsight

1. Inicie sesión en el [Portal de Administración de Azure][azure-portal].

2. Haga clic en **HDInsight** a la izquierda y, a continuación, en **+NUEVO** en la esquina inferior izquierda de la página.

3. Haga clic en el icono de HDInsight en la segunda columna y después seleccione **Personalizado**.

4. En la página **Detalles del clúster**, escriba el nombre del nuevo clúster y seleccione **HBase** como **Tipo de clúster**. Seleccione la flecha para continuar.

5. Especifique 1 como el número de **Nodos de datos** para usar en este clúster. En **Región/Red virtual**, seleccione la red virtual de Azure creada anteriormente. En **Subredes de la red virtual**, seleccione **Subred-1**.

	> [AZURE.NOTE] Para minimizar el coste del clúster usado en este artículo, reduzca el **Tamaño del clúster** a 1 y elimine el clúster cuando haya terminado de usarlo.

6. Escriba el **Nombre de usuario** y la **Contraseña** del administrador y luego seleccione la flecha para continuar.

7. En **Cuenta de almacenamiento**, seleccione **Crear nuevo almacenamiento** o seleccione una cuenta de almacenamiento existente. Seleccione o escriba el **Nombre de cuenta** y el **Contenedor predeterminado** que se va a usar. Haga clic en el icono de verificación en la parte inferior izquierda para crear el clúster de Storm.

	> [AZURE.NOTE] Debe utilizar un contenedor diferente al utilizado en el clúster de Storm.

### Habilitación de escritorio remoto

En este tutorial vamos a utilizar el escritorio remoto para acceder a los clústeres de Storm y HBase. Utilice estos pasos para habilitar el Escritorio remoto en ambos.

1. Inicie sesión en el [Portal de Administración de Azure][azure-portal].

2. Seleccione **HDInsight** a la izquierda y después seleccione su clúster de Storm en la lista. Por último, seleccione **Configurar** en la parte superior de la página.

3. En la parte inferior de la página, seleccione **Habilitar de forma remota**. Cuando se le indique, introduzca un nombre de usuario, una contraseña y una fecha cuando expire el acceso al Escritorio remoto. Haga clic en la marca de verificación para habilitar el Escritorio remoto.

Una vez habilitado el Escritorio remoto, puede seleccionar **Conectar** en la parte inferior de la página. Siga las indicaciones para conectarse al clúster.

### Detección del sufijo DNS de HBase

Para escribir a HBase desde el clúster de Storm, debe utilizar el nombre completo del clúster de HBase. Utilice los pasos siguientes para detectar esta información.

1. Conectarse al clúster de HBase a través del Escritorio remoto.

2. Después de conectarse al clúster, abra la línea de comandos de Hadoop y ejecute el comando **ipconfig** para obtener el sufijo DNS. El **Sufijo DNS específico de la conexión** contendrá el valor del sufijo. Por ejemplo, **mycluster.b4.internal.cloudapp.net**. Guarde esta información.

## Desarrollo de la topología de Storm

> [AZURE.NOTE] Los pasos de esta sección deben realizarse en el entorno de desarrollo local.

### Descarga y compilación de dependencias externas

Varias de las dependencias utilizadas en este proyecto deben descargarse y compilarse de manera individual y después instalarse en el repositorio Maven local en el entorno de desarrollo. En esta sección descargará e instalará.

* El spout del Centro de eventos que lee mensajes del Centro de eventos.

* El SDK del cliente Java de SignalR

#### Descarga y compilación del spout del Centro de eventos.

Para recibir datos desde el Centro de eventos, utilizaremos **eventhubs-storm-spout**.

1. Utilice el Escritorio remoto para conectarse a su clúster de Storm y después copiar el archivo **%STORM_HOME%\examples\eventhubspout\eventhubs-storm-spout-0.9-jar-with-dependencies.jar** en su entorno de desarrollo local. Contiene **events-storm-spout**.

2. Utilice el comando siguiente para instalar el paquete en el almacén Maven local. Esto nos permitirá agregarlo fácilmente como referencia al proyecto de Storm en un paso posterior.

		mvn install:install-file -Dfile=target/eventhubs-storm-spout-0.9-jar-with-dependencies.jar -DgroupId=com.microsoft.eventhubs -DartifactId=eventhubs-storm-spout -Dversion=0.9 -Dpackaging=jar

#### Descarga y compilación del cliente de SignalR

Para enviar mensajes al Panel de ASP.NET, use el [SDK de cliente de SignalR para Java](https://github.com/SignalR/java-client).

1. Abra el símbolo del sistema.

2. Cambie los directorios a donde desea descargar y almacenar el proyecto del SDK del cliente SignalR.

3. Utilice el comando siguiente para descargar el proyecto de GitHub.

	git clone https://github.com/SignalR/java-client

4. Cambie los directorios por el directorio **java-client\signalr-client-sdk** y compile el proyecto en un archivo JAR utilizando los comandos siguientes:

		cd java-client\signalr-client-sdk
		mvn package

	> [AZURE.NOTE] Si recibe un error que indica que la dependencia **gson** no se puede descargar, quite las líneas siguientes del archivo **java-client\signalr-client-sdk\pom.xml**.
	> 
<repository>
<id>central</id>
<name>Central</name>
<url>http://maven.eclipse.org/build</url>
</repository>
</repositories>

	> Si se quitan estas líneas, Maven extraerá el archivo del repositorio central (el comportamiento predeterminado). Para forzar a Maven a reintentar el repositorio, use el comando `-U`. Por ejemplo, `mvn package -U`

5. Utilice el comando siguiente para instalar el paquete en el almacén Maven local. Esto nos permitirá agregarlo fácilmente como referencia al proyecto de Storm en un paso posterior.

		mvn install:install-file -Dfile=target/signalr-client-sdk-1.0.jar -DgroupId=microsoft.aspnet.signalr -DartifactId=signalr-client-sdk -Dversion=1.0 -Dpackaging=jar

### Técnica scaffolding del proyecto de topología de Storm

Ahora que hemos instalado el spout del Centro de eventos y el cliente de SignalR en el repositorio local, utilice Maven para crear la técnica scaffolding en el proyecto de topología de Storm.

1. Abra un símbolo del sistema, sesión Bash, sesión Terminal o la que utilice para escribir comandos en el sistema.

2. Cambie los directorios a la ubicación que desea crear este proyecto. Por ejemplo, si tiene un directorio donde almacena todos los proyectos de código.

3. Utilice el comando Maven siguiente para crear una técnica scaffolding básica para su aplicación.

		mvn archetype:generate -DarchetypeArtifactId=maven-archetype-quickstart -DgroupId=com.microsoft.examples -DartifactId=TemperatureMonitor -DinteractiveMode=false

	Este comando...

	* Creará un nuevo directorio usando el *artifactId* especificado. En este caso, **Temperature**.
	* Creará un archivo **pom.xml**, que contiene la información de Maven para este proyecto.
	* Creará una estructura de directorios **src**, que contiene algún código y pruebas básicos.

### Incorporación de dependencias y complementos

A continuación, modifique el archivo **pom.xml** para hacer referencia a las dependencias de este proyecto, así como a los complementos de Maven que se van a usar al compilar y empaquetar.

1. Con un editor de texto, abra el archivo **pom.xml** y agregue lo siguiente a la sección **&lt;dependencies>**. Puede agregarlos al final de la sección, después de la dependencia para junit.

		<dependency>
	      <groupId>org.apache.storm</groupId>
	      <artifactId>storm-core</artifactId>
	      <version>0.9.2-incubating</version>
	      <!-- keep storm out of the jar-with-dependencies -->
	      <scope>provided</scope>
	    </dependency>
	    <dependency>
	      <groupId>microsoft.aspnet.signalr</groupId>
	      <artifactId>signalr-client-sdk</artifactId>
	      <version>1.0</version>
	    </dependency>
	    <dependency>
	      <groupId>com.microsoft.eventhubs</groupId>
	      <artifactId>eventhubs-storm-spout</artifactId>
	      <version>0.9</version>
	    </dependency>
	    <dependency>
	      <groupId>com.google.code.gson</groupId>
	      <artifactId>gson</artifactId>
	      <version>2.2.2</version>
	    </dependency>
		<dependency>
      	  <groupId>com.github.ptgoetz</groupId>
      	  <artifactId>storm-hbase</artifactId>
      	  <version>0.1.2</version>
    	</dependency>
	    <dependency>
	      <groupId>com.netflix.curator</groupId>
	      <artifactId>curator-framework</artifactId>
	      <version>1.3.3</version>
	      <exclusions>
	        <exclusion>
	          <groupId>log4j</groupId>
	            <artifactId>log4j</artifactId>
	          </exclusion>
	        <exclusion>
	          <groupId>org.slf4j</groupId>
	            <artifactId>slf4j-log4j12</artifactId>
	        </exclusion>
	      </exclusions>
	      <scope>provided</scope>
	    </dependency>

	Se agregan dependencias para...

	* eventhubs-storm-spout: el spout del Centro de eventos
	* signalr-client-sdk: el cliente de SignalR
	* gson: es una dependencia del cliente de SignalR y también se utilizará para crear JSON al escribir en SignalR
	* storm-core: proporciona funcionalidad central para las aplicaciones Storm
	* slf4j: proporciona capacidades de registro y utilizadas por eventhubs-storm-spout
	* curator-framework: utilizada por eventhubs-storm-spout
	* storm-core: clases centrales de Storm
	* storm-hbase: clases que permiten escribir a HBase

	> [AZURE.NOTE] Tenga en cuenta que algunas dependencias están marcadas con un ámbito de **proporcionado** para indicar que estas dependencias deben descargarse del repositorio de Maven y utilizarse para compilar y probar localmente la aplicación, pero que también estarán disponibles en su entorno de ejecución y no necesitan compilarse e incluirse en el JAR creado por este proyecto.

2. Al final del archivo **pom.xml**, justo delante de la entrada **&lt;/project>**, agregue lo siguiente:

		  <build>
		    <plugins>
		      <plugin>
		        <groupId>org.apache.maven.plugins</groupId>
		        <artifactId>maven-compiler-plugin</artifactId>
		        <version>2.3.2</version>
		        <configuration>
		          <source>1.7</source>
		          <target>1.7</target>
		        </configuration>
		      </plugin>
		      <plugin>
		        <groupId>org.apache.maven.plugins</groupId>
		        <artifactId>maven-shade-plugin</artifactId>
		        <version>2.3</version>
		        <configuration>
		          <transformers>
		            <transformer implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer">
		            </transformer>
		          </transformers>
		        </configuration>
		        <executions>
		          <execution>
		            <phase>package</phase>
		            <goals>
		              <goal>shade</goal>
		            </goals>
		          </execution>
		        </executions>
		      </plugin>
		      <plugin>
		        <groupId>org.codehaus.mojo</groupId>
		        <artifactId>exec-maven-plugin</artifactId>
		        <version>1.2.1</version>
		        <executions>
		          <execution>
		          <goals>
		            <goal>exec</goal>
		          </goals>
		          </execution>
		        </executions>
		        <configuration>
		          <executable>java</executable>
		          <includeProjectDependencies>true</includeProjectDependencies>
		          <includePluginDependencies>false</includePluginDependencies>
		          <classpathScope>compile</classpathScope>
		          <mainClass>${storm.topology}</mainClass>
		        </configuration>
		      </plugin>
		    </plugins>
		    <resources>
		      <resource>
		        <directory>${basedir}/conf</directory>
		        <filtering>false</filtering>
		        <includes>
		          <include>Config.properties</include>
				  <include>hbase-site.xml</include>
		        </includes>
		      </resource>
		    </resources>
		  </build>

	Esto indica a Maven que haga lo siguiente al generar el proyecto:

	* Incluir el archivo de recursos **/conf/Config.properties**. Este archivo se creará después, pero contendrá información de configuración para conectarse al Centro de eventos de Azure.
	* Incluir el archivo de recursos **/conf/hbase-site.xml**. Este archivo se creará después, pero contendrá información sobre cómo conectarse a HBase.
	* Usar **maven-compiler-plugin** para compilar la aplicación.
	* Usar **maven-shade-plugin** para generar un uberjar o fatjar, que contiene este proyecto y cualquier dependencia requerida.
	* Usar **exec-maven-plugin**, que permite ejecutar la aplicación localmente sin un clúster de Hadoop.

### Incorporación de archivos de configuración

**eventhubs-storm-spout** lee la información de configuración de un archivo **Config.properties**. Esto indica el Centro de eventos al que conectarse. Aunque puede especificar un archivo de configuración al iniciar la topología en un clúster, incluyendo el del proyecto que le proporciona una configuración predeterminada conocida.

1. En el directorio **TemperatureMonitor**, cree un directorio nuevo con el nombre **conf**.

2. En el directorio **conf**, cree dos nuevos archivos:

	* **Config.properties**: contiene la configuración del centro de eventos.
	* **hbase-site.xml**: contiene la configuración para la conexión a hbase.

3. Use lo siguiente como contenido del archivo **Config.properties**:

		eventhubspout.username = storm

		eventhubspout.password = <the key of the 'storm' policy>

		eventhubspout.namespace = <the event hub namespace>

		eventhubspout.entitypath = <the event hub name>

		eventhubspout.partitions.count = <the number of partitions for the event hub>

		# if not provided, will use storm's zookeeper settings
		# zookeeper.connectionstring=localhost:2181

		eventhubspout.checkpoint.interval = 10

		eventhub.receiver.credits = 1024

	Reemplace **password** por la clave de la directiva de **storm** creada anteriormente en el Centro de eventos.
	
	Reemplace **namespace** por el espacio de nombres del Centro de eventos.
	
	Reemplace **entitpath** por el nombre del Centro de eventos.

4. Utilice lo siguiente como contenido del archivo **hbase-site.xml**:

		<?xml version="1.0"?>
		<?xml-stylesheet type="text/xsl" href="configuration.xsl"?>
		<!--
		/**
		 * Copyright 2010 The Apache Software Foundation
		 *
		 * Licensed to the Apache Software Foundation (ASF) under one
		 * or more contributor license agreements.  See the NOTICE file
		 * distributed with this work for additional information
		 * regarding copyright ownership.  The ASF licenses this file
		 * to you under the Apache License, Version 2.0 (the
		 * "License"); you may not use this file except in compliance
		 * with the License.  You may obtain a copy of the License at
		 *
		 *     http://www.apache.org/licenses/LICENSE-2.0
		 *
		 * Unless required by applicable law or agreed to in writing, software
		 * distributed under the License is distributed on an "AS IS" BASIS,
		 * WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
		 * See the License for the specific language governing permissions and
		 * limitations under the License.
		 */
		-->
		<configuration>
		  <property>
		    <name>hbase.cluster.distributed</name>
		    <value>true</value>
		  </property>
		  <property>
		    <name>hbase.zookeeper.quorum</name>
		    <value>zookeeper0.suffix,zookeeper1.suffix,zookeeper2.suffix</value>
		  </property>
		  <property>
		    <name>hbase.zookeeper.property.clientPort</name>
		    <value>2181</value>
		  </property>
		</configuration>

5. En el archivo **hbase-site.xml**, reemplace el valor de **suffix** para las entradas de zookeeper con el sufijo de DNS que ha recuperado antes de HBase. Por ejemplo, **zookeeper0.mycluster.b4.internal.cloudapp.net, zookeeper1.mycluster.b4.internal.cloudapp.net, zookeeper2.mycluster.b4.internal.cloudapp.net**.

6. Guarde los archivos.

### Incorporación de aplicaciones auxiliares

Para admitir la serialización hacia y desde JSON, necesitamos algunas clases de aplicaciones auxiliares que definan la estructura del objeto.

1. En el directorio **\temperaturemonitor\src\main\java\com\microsoft\examples**, cree un nuevo directorio denominado **helpers**.

2. En el directorio **helpers**, cree dos nuevos archivos:

	* **EventHubMessage.java**: define el formato de mensajes del centro de eventos.

	* **SignalRMessage.java**: define el formato del mensaje enviado a SignalR.

3. Use lo siguiente como contenido del archivo **EventHubMessage.java**:

		package com.microsoft.examples;

		public class EventHubMessage {
		  String TimeStamp;
		  int DeviceId;
		  int Temperature;
		}

4. Use lo siguiente como contenido del archivo **SignalRMessage.java**:

		package com.microsoft.examples;

		public class SignalRMessage {
		  int device;
		  int temperature;
		}

5. Guarde y cierre estos archivos.

### Incorporación de bolts

Los bolts se encargan del procesamiento principal de una topología. Para esta topología hay tres bolts, aunque uno de ellos es el hbase-bolt, que se descargará automáticamente cuando se genere el proyecto.

1. En el directorio **\temperaturemonitor\src\main\java\com\microsoft\examples**, cree un nuevo directorio denominado **bolts**.

2. En el directorio **bolts**, cree dos nuevos archivos:

	* **ParserBolt.java**: analiza el mensaje entrante desde el Centro de eventos en campos individuales y después emite dos secuencias.
	* **DashboardBolt.java**: registra información en el panel web mediante SignalR.

3. Use lo siguiente como contenido del archivo **ParserBolt.java**:

		package com.microsoft.examples;

		import backtype.storm.topology.base.BaseBasicBolt;
		import backtype.storm.topology.BasicOutputCollector;
		import backtype.storm.topology.OutputFieldsDeclarer;
		import backtype.storm.tuple.Tuple;
		import backtype.storm.tuple.Fields;
		import backtype.storm.tuple.Values;

		import com.google.gson.Gson;
		import com.google.gson.GsonBuilder;

		public class ParserBolt extends BaseBasicBolt {

		  //Declare output fields & streams
		  //hbasestream is all fields, and goes to hbase
		  //dashstream is just the device and temperature, and goes to the dashboard
		  @Override
		  public void declareOutputFields(OutputFieldsDeclarer declarer) {
		    declarer.declareStream("hbasestream", new Fields("timestamp", "deviceid", "temperature"));
		    declarer.declareStream("dashstream", new Fields("deviceid", "temperature"));
		  }

		  //Process tuples
		  @Override
		  public void execute(Tuple tuple, BasicOutputCollector collector) {
		    Gson gson = new Gson();
		    //Should only be one tuple, which is the JSON message from the spout
		    String value = tuple.getString(0);

			//Deal with cases where we get multiple
			//EventHub messages in one tuple
			String[] arr = value.split("}");
			for (String ehm : arr)
			{

			    //Convert it from JSON to an object
				EventHubMessage msg = new Gson().fromJson(ehm.concat("}"),EventHubMessage.class);

			    //Pull out the values and emit as a stream
			    String timestamp = msg.TimeStamp;
			    int deviceid = msg.DeviceId;
			    int temperature = msg.Temperature;
			    collector.emit("hbasestream", new Values(timestamp, deviceid, temperature));
			    collector.emit("dashstream", new Values(deviceid, temperature));
			}
		  }
		}

4. Use lo siguiente como contenido del archivo **DashboardBolt.java**:

		package com.microsoft.examples;

		import backtype.storm.topology.BasicOutputCollector;
		import backtype.storm.topology.OutputFieldsDeclarer;
		import backtype.storm.topology.base.BaseBasicBolt;
		import backtype.storm.tuple.Tuple;
		import backtype.storm.task.TopologyContext;
		import backtype.storm.Config;
		import backtype.storm.Constants;

		import microsoft.aspnet.signalr.client.Action;
		import microsoft.aspnet.signalr.client.ErrorCallback;
		import microsoft.aspnet.signalr.client.LogLevel;
		import microsoft.aspnet.signalr.client.Logger;
		import microsoft.aspnet.signalr.client.MessageReceivedHandler;
		import microsoft.aspnet.signalr.client.hubs.HubConnection;
		import microsoft.aspnet.signalr.client.hubs.HubProxy;

		import com.google.gson.Gson;
		import com.google.gson.GsonBuilder;

		import java.util.Map;

		public class DashboardBolt extends BaseBasicBolt {
		  //Connection and proxy for SignalR hub
		  private HubConnection conn;
		  private HubProxy proxy;

		  //Declare output fields
		  @Override
		  public void declareOutputFields(OutputFieldsDeclarer declarer) {
		    //no stream output - we talk directly to SignalR
		  }

		  @Override
		  public void prepare(Map config, TopologyContext context) {

		    // Connect to the DashHub SignalR server
		    conn = new HubConnection("http://dashboard.azurewebsites.net/");
		    // Create the hub proxy
		    proxy = conn.createHubProxy("DashHub");
		    // Subscribe to the error event
		    conn.error(new ErrorCallback() {
		      @Override
		      public void onError(Throwable error) {
		        error.printStackTrace();
		      }
		    });
		    // Subscribe to the connected event
		    conn.connected(new Runnable() {
		      @Override
		      public void run() {
		        System.out.println("CONNECTED");
		      }
		    });
		    // Subscribe to the closed event
		    conn.closed(new Runnable() {
		      @Override
		      public void run() {
		        System.out.println("DISCONNECTED");
		      }
		    });
		    // Start the connection
		    conn.start()
		      .done(new Action<Void>() {
		        @Override
		        public void run(Void obj) throws Exception {
		          System.out.println("Done Connecting!");
		        }
		    });
		  }

		  //Process tuples
		  @Override
		  public void execute(Tuple tuple, BasicOutputCollector collector) {
		    Gson gson = new Gson();
		    try {
		      //Get the deviceid and temperature by field name
		      int deviceid = tuple.getIntegerByField("deviceid");
		      int temperature = tuple.getIntegerByField("temperature");
		      //Construct the SignalR message
		      SignalRMessage srMessage = new SignalRMessage();
		      srMessage.device = deviceid;
		      srMessage.temperature = temperature;
		      // send it as JSON
		      proxy.invoke("send", gson.toJson(srMessage));
		    } catch (Exception e) {
		       // LOG.error("Bolt execute error: {}", e);
		       collector.reportError(e);
		    }
		  }
		}

	Reemplace `http://dashboard.azurewebsites.net/` por la dirección del sitio web de Azure en el que ha publicado el panel anteriormente. Por ejemplo, http://mydashboard.azurewebsites.net.

5. Guarde y cierre los archivos.

### Definición de la topología

La topología describe cómo se transmiten los datos entre los spouts y bolts en una topología, así como un grado de paralelismo para la topología y los componentes dentro de ella.

1. En el directorio **\temperaturemonitor\src\main\java\com\microsoft\examples**, cree un nuevo archivo denominado **Temperature.java**.

2. Abra el archivo **Temperature.java** y use lo siguiente como contenido.

		package com.microsoft.examples;

		import backtype.storm.Config;
		import backtype.storm.LocalCluster;
		import backtype.storm.StormSubmitter;
		import backtype.storm.generated.StormTopology;
		import backtype.storm.topology.TopologyBuilder;
		import backtype.storm.tuple.Fields;
		import com.microsoft.eventhubs.spout.EventHubSpout;
		import com.microsoft.eventhubs.spout.EventHubSpoutConfig;

		import java.io.FileReader;
		import java.util.Properties;

		//hbase
		import org.apache.storm.hbase.bolt.mapper.SimpleHBaseMapper;
		import org.apache.storm.hbase.bolt.HBaseBolt;
		import java.util.Map;
		import java.util.HashMap;
		import backtype.storm.tuple.Fields;

		public class Temperature
		{
		  protected EventHubSpoutConfig spoutConfig;
		  protected int numWorkers;

		  // Reads the configuration information for the Event Hub spout
		  protected void readEHConfig(String[] args) throws Exception {
		    Properties properties = new Properties();
		    if(args.length > 1) {
		      properties.load(new FileReader(args[1]));
		    }
		    else {
		      properties.load(Temperature.class.getClassLoader().getResourceAsStream(
		        "Config.properties"));
		    }

		    String username = properties.getProperty("eventhubspout.username");
		    String password = properties.getProperty("eventhubspout.password");
		    String namespaceName = properties.getProperty("eventhubspout.namespace");
		    String entityPath = properties.getProperty("eventhubspout.entitypath");
		    String zkEndpointAddress = properties.getProperty("zookeeper.connectionstring");
		    int partitionCount = Integer.parseInt(properties.getProperty("eventhubspout.partitions.count"));
		    int checkpointIntervalInSeconds = Integer.parseInt(properties.getProperty("eventhubspout.checkpoint.interval"));
		    int receiverCredits = Integer.parseInt(properties.getProperty("eventhub.receiver.credits"));
		    System.out.println("Eventhub spout config: ");
		    System.out.println("  partition count: " + partitionCount);
		    System.out.println("  checkpoint interval: " + checkpointIntervalInSeconds);
		    System.out.println("  receiver credits: " + receiverCredits);
		    spoutConfig = new EventHubSpoutConfig(username, password,
		      namespaceName, entityPath, partitionCount, zkEndpointAddress,
		      checkpointIntervalInSeconds, receiverCredits);

		    //set the number of workers to be the same as partition number.
		    //the idea is to have a spout and a partial count bolt co-exist in one
		    //worker to avoid shuffling messages across workers in storm cluster.
		    numWorkers = spoutConfig.getPartitionCount();

		    if(args.length > 0) {
		      //set topology name so that sample Trident topology can use it as stream name.
		      spoutConfig.setTopologyName(args[0]);
		    }
		  }

		  // Create the spout using the configuration
		  protected EventHubSpout createEventHubSpout() {
		    EventHubSpout eventHubSpout = new EventHubSpout(spoutConfig);
		    return eventHubSpout;
		  }

		  // Build the topology
		  protected StormTopology buildTopology(EventHubSpout eventHubSpout, SimpleHBaseMapper mapper) {
		    TopologyBuilder topologyBuilder = new TopologyBuilder();
		    // Name the spout 'EventHubsSpout', and set it to create
		    // as many as we have partition counts in the config file
		    topologyBuilder.setSpout("EventHub", eventHubSpout, spoutConfig.getPartitionCount())
		      .setNumTasks(spoutConfig.getPartitionCount());
		    // Create the parser bolt, which subscribes to the stream from EventHub
		    topologyBuilder.setBolt("Parser", new ParserBolt(), spoutConfig.getPartitionCount())
		      .localOrShuffleGrouping("EventHub").setNumTasks(spoutConfig.getPartitionCount());
		    // Create the dashboard bolt, which subscribes to the stream from Parser
		    topologyBuilder.setBolt("Dashboard", new DashboardBolt(), spoutConfig.getPartitionCount())
		      .fieldsGrouping("Parser", "dashstream", new Fields("deviceid")).setNumTasks(spoutConfig.getPartitionCount());
		    // Create the HBase bolt, which subscribes to the stream from Parser
		    // WARNING - uncomment the following two lines when deploying
			// leave commented when testing locally
			// topologyBuilder.setBolt("HBase", new HBaseBolt("SensorData", mapper).withConfigKey("hbase.conf"), spoutConfig.getPartitionCount())
		    //  .fieldsGrouping("Parser", "hbasestream", new Fields("deviceid")).setNumTasks(spoutConfig.getPartitionCount());
		    return topologyBuilder.createTopology();
		  }

		  protected void submitTopology(String[] args, StormTopology topology, Config config) throws Exception {
		    // Config config = new Config();
		    config.setDebug(false);

		    //Enable metrics
		    config.registerMetricsConsumer(backtype.storm.metric.LoggingMetricsConsumer.class, 1);

		    // Is this running locally, or on an HDInsight cluster?
		    if (args != null && args.length > 0) {
		      config.setNumWorkers(numWorkers);
		      StormSubmitter.submitTopology(args[0], config, topology);
		    } else {
		      config.setMaxTaskParallelism(2);

		      LocalCluster localCluster = new LocalCluster();
		      localCluster.submitTopology("test", config, topology);

		      Thread.sleep(5000000);

		      localCluster.shutdown();
		    }
		  }

		  // Loads the configuration, creates the spout, builds the topology,
		  // and then submits it
		  protected void runScenario(String[] args) throws Exception{
		    readEHConfig(args);
		    Config config = new Config();

		    //hbase configuration
		    Map<String, Object> hbConf = new HashMap<String, Object>();
		    if(args.length > 0) {
		      hbConf.put("hbase.rootdir", args[0]);
		    }
		    config.put("hbase.conf", hbConf);
		    SimpleHBaseMapper mapper = new SimpleHBaseMapper()
		          .withRowKeyField("deviceid")
		          .withColumnFields(new Fields("timestamp", "temperature"))
		          .withColumnFamily("cf");

		    EventHubSpout eventHubSpout = createEventHubSpout();
		    StormTopology topology = buildTopology(eventHubSpout, mapper);
		    submitTopology(args, topology, config);
		  }

		  public static void main(String[] args) throws Exception {
		    Temperature scenario = new Temperature();
		    scenario.runScenario(args);
		  }
		}

	> [AZURE.NOTE] Tenga en cuenta que las líneas de **HBaseBolt** incluyen comentarios. Esto es debido a que en el paso siguiente se va a ejecutar la topología localmente. Como HBaseBolt se comunica directamente con HBase, se devolverán errores si está habilitado. A no ser que haya configurado una red virtual con un servidor DNS y haya unido también su máquina local a la red virtual.

### Prueba de la topología de manera local

Para compilar y probar el archivo en su equipo de desarrollo, utilice los pasos siguientes.

1. Inicie la aplicación **SendEvents** .NET para comenzar a enviar eventos, de forma que tengamos algo que leer desde el Centro de eventos.

2. Abra un explorador web en el panel web que ha implementado antes en un Sitio web de Azure. Esto le permitirá ver el trazado gráfico de los valores a medida que transcurren por la topología.

3. Inicie la topología localmente utilizando el comando siguiente

	mvn compile exec:java -Dstorm.topology=com.microsoft.examples.Temperature

	Esto iniciará la topología, leerá los archivos desde el Centro de eventos y los enviará al panel que se está ejecutando en los Sitios web de Azure. Debería ver líneas que aparecen en el panel web.

4. Después de comprobar que funciona, detenga la topología introduciendo Ctrl-C. Para detener la aplicación SendEvent, seleccione la ventana y presione cualquier tecla.

### Habilitación de HBaseBolt y preparación de HBase

1. Abra el archivo **Temperature.java** y quite el comentario (//) de las líneas siguientes:

		//topologyBuilder.setBolt("HBase", new HBaseBolt("SensorData", mapper).withConfigKey("hbase.conf"), spoutConfig.getPartitionCount())
    	//  .fieldsGrouping("Parser", "hbasestream", new Fields("deviceid")).setNumTasks(spoutConfig.getPartitionCount());

	De esta forma se habilita el bolt de HBase.

2. Guarde **Temperature.java**.

3. Con el Escritorio remoto, conéctese al clúster de HBase.

4. Desde el escritorio, inicie la línea de comandos de HDInsight e introduzca los comandos siguientes.

		cd %hbase_home%
		bin\hbase shell

5. Desde el shell de HBase, introduzca el comando siguiente para crear una tabla en la que se almacenarán los datos del sensor.

		create 'SensorData', 'cf'

6. Compruebe que la tabla no contiene datos introduciendo el comando siguiente.

		scan 'SensorData'

Deje este aviso abierto de momento en el shell de HBase.

## Empaquetado e implementación de la topología en HDInsight

Utilice los pasos siguientes en el entorno de desarrollo para ejecutar la topología Temperature en el  clúster de Storm.

1. Utilice el comando siguiente para crear un paquete JAR desde el proyecto.

		mvn package

	Se creará un archivo denominado **TemperatureMonitor-1.0-SNAPSHOT.jar** en el directorio **target** del proyecto.

2. En el equipo de desarrollo local, inicie la aplicación **SendEvents** .NET de forma que podamos tener algunos eventos para leer.

3. Conecte el clúster de Storm mediante el Escritorio remoto y copie el archivo  **TemperatureMonitor-1.0-SNAPSHOT.jar** en el directorio **c:\apps\dist\storm&lt;version number>**.

4. Use el icono de la **línea de comandos de HDInsight** en el escritorio del clúster para abrir un nuevo símbolo del sistema y utilice los comandos siguientes para ejecutar la topología.

		cd %storm_home%
		bin\storm jar TemperatureMonitor-1.0-SNAPSHOT.jar com.microsoft.examples.Temperature Temperature

5. Una vez iniciada la topología, pueden pasar unos pocos segundos antes de que los elementos comiencen a aparecer en el panel web.

6. Una vez hayan aparecido los elementos en el panel, cambie a la sesión del Escritorio remoto en el clúster de HBase.

7. Desde el shell de HBase, introduzca el comando siguiente.

		scan 'SensorData'

	Tenga en cuenta que ahora se devuelven varias filas de datos que se han escrito en la topología de Storm.

8. Para detener la topología, vaya a la sesión del Escritorio remoto con el clúster de Storm e introduzca lo siguiente en la línea de comandos de HDInsight.

		bin\storm kill Temperature

	Tras unos segundos, la topología se detendrá.

## Resumen

Ahora ha aprendido a utilizar Storm para leer datos desde el Centro de eventos, a almacenar datos en HBase y a mostrar información desde Storm en un panel externo utilizando SignalR y D3.js.

* Para obtener más información sobre Apache Storm, consulte [https://storm.incubator.apache.org/](https://storm.incubator.apache.org/)

* Para obtener más información sobre HBase con HDInsight, consulte [Información general de HBase de HDInsight](http://azure.microsoft.com/es-es/documentation/articles/hdinsight-hbase-overview/)

* Para obtener más información sobre SignalR, consulte [ASP.NET SignalR](http://signalr.net/)

* Para obtener más información sobre D3.js, consulte [D3.js - Documentos orientados a datos](http://d3js.org/)

* Para obtener información sobre la creación de topologías en .NET, consulte [Desarrollo de aplicaciones de procesamiento de datos de transmisión por secuencias con SCP.NET y C# en Storm en HDInsight](/es-es/documentation/articles/hdinsight-hadoop-storm-scpdotnet-csharp-develop-streaming-data-processing-application/)

[azure-portal]: https://manage.windowsazure.com/

<!--HONumber=42-->
