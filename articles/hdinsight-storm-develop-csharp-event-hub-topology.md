<properties
   pageTitle="Procesamiento de eventos desde Centro de eventos con Storm en HDInsight | Azure"
   description="Aprenda a procesar datos de Centro de eventos con una topología de Storm de C# creada en Visual Studio con las herramientas de HDInsight para Visual Studio."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="03/03/2015"
   ms.author="larryfr"/>

# Procesamiento de eventos desde Centro de eventos de Azure con Storm en HDInsight (C#)

Centro de eventos de Azure le permite procesar grandes volúmenes de datos provenientes de sitios web, aplicaciones y dispositivos. El spout de Centro de eventos permite que Apache Storm en HDInsight analice fácilmente estos datos en tiempo real. También es posible escribir datos en Centro de eventos desde Storm usando el bolt de Centro de eventos. 

En este documento, aprenderá a utilizar las herramientas de HDInsight para Visual Studio y el spout y bolt de Centro de eventos para crear dos topologías híbridas C#/Java:

* **EventHubWriter**: genera datos de manera aleatoria y los escribe en el Centro de eventos.

* **EventHubReader**: lee datos del Centro de eventos y los almacena en el almacenamiento de tablas de Azure.

## Requisitos previos

* Un <a href="../hdinsight-storm-getting-started/" target="_blank">clúster de Apache Storm en HDInsight</a>

* Un <a href="../service-bus-event-hubs-csharp-ephcs-getstarted/" target="_blank">Centro de eventos de Azure</a>

* El <a href="http://azure.microsoft.com/downloads/" target="_blank">SDK .NET de Azure</a>

* Las <a href="../hdinsight-hadoop-visual-studio-tools-get-started/" target="_blank">herramientas de HDInsight para Visual Studio</a>, la versión del 17 de febrero de 2015 o más reciente.

## Proyecto completado

Puede descargar una versión completa del proyecto creado en este artículo en [https://github.com/Blackmist/eventhub-storm-hybrid](https://github.com/Blackmist/eventhub-storm-hybrid); sin embargo, de todos modos deberá seguir los pasos de este documento para proporcionar ajustes de configuración.

> [AZURE.NOTE] Cuando se utiliza el proyecto completado, debe usar el **Administrador de paquetes NuGet** para restaurar los paquetes que requiere esta solución.

## Spout y bolt de Centro de eventos

El spout y bolt de Centro de eventos son componentes de Java que le permiten trabajar con facilidad con el Centro de eventos desde Apache Storm. A pesar de que estos componentes están escritos en Java, las herramientas de HDInsight para Visual Studio le permiten crear topologías híbridas que combinan componentes de Java y C#.

El spout y bolt se distribuyen como un archivo Java (.jar) único llamado **eventhubs-storm-spout-0.9-jar-with-dependencies.jar**.

### Descarga del archivo .jar

La versión más reciente del archivo **eventhubs-storm-spout-0.9-jar-with-dependencies.jar** está incluida en el proyecto <a href="https://github.com/hdinsight/hdinsight-storm-examples" target="_blank">https://github.com/hdinsight/hdinsight-storm-examples</a> que se encuentra en la carpeta **lib**. Para descargar el archivo, use uno de los siguientes métodos.

> [AZURE.NOTE] El spout y bolt se enviaron para su inclusión en el proyecto de Apache Storm. Para obtener más información, consulte la <a href="https://github.com/apache/storm/pull/336/files">solicitud de extracción</a>.

* **Descargue un archivo ZIP**: en el sitio <a href="https://github.com/hdinsight/hdinsight-storm-examples" target="_blank">https://github.com/hdinsight/hdinsight-storm-examples</a>, seleccione el botón **Descargar ZIP** para descargar un archivo .zip que contiene el proyecto.

	![download zip button](./media/hdinsight-storm-develop-csharp-event-hub-topology/download.png)

	Una vez que lo descargue, puede extraer el archivo que se encontrará en el directorio **lib**.

* **Clone el proyecto**: si tiene instalado <a href="http://git-scm.com/" target="_blank">Git</a>, use el siguiente comando para clonar de manera local el repositorio y, a continuación, encuentre el archivo en el directorio **lib**.

		git clone https://github.com/hdinsight/hdinsight-storm-examples

## Configuración del centro de eventos

Centro de eventos es el origen de datos para este ejemplo. Utilice los pasos siguientes para crear un centro de eventos.

1. En el [Portal de Azure](https://manage.windowsazure.com), seleccione **NUEVO | Bus de servicio | Centro de eventos | Creación personalizada**.

2. En el cuadro de diálogo **Agregar un nuevo centro de eventos**, escriba un **nombre del centro de eventos**, seleccione la **región** donde se va a crear el centro y cree un nuevo espacio de nombres o seleccione uno existente. Por último, haga clic en la **flecha**.

	![wizard page 1](./media/hdinsight-storm-develop-csharp-event-hub-topology/wiz1.png)

	> [AZURE.NOTE] Debe seleccionar la misma **ubicación** de su servidor Storm en HDInsight para disminuir la latencia y los costos.

2. En el cuadro de diálogo **Configuración del centro de eventos**, escriba los valores **Recuento de particiones** y **Retención de mensajes**. En este ejemplo, utilice un recuento de particiones de 10 y una retención de mensajes de 1. Anote el número de particiones, porque necesitará más adelante este valor.

	![wizard page 2](./media/hdinsight-storm-develop-csharp-event-hub-topology/wiz2.png)

3. Una vez creado el centro de eventos, seleccione el espacio de nombres y, a continuación, seleccione **Centros de eventos**. Finalmente, seleccione el centro de eventos que ha creado antes.

4. Seleccione **Configurar** y, a continuación, cree dos directivas de acceso con la información siguiente:

	<table>
	<tr><th>Nombre</th><th>Permisos</th></tr>
	<tr><td>escritor</td><td>Enviar</td></tr>
	<tr><td>lector</td><td>Escuchar</td></tr>
	</table>

	Después de crear los permisos, seleccione el icono **Guardar** en la parte inferior de la página. Con esto se crean las directivas de acceso compartido que se usarán para enviar (escritor) y escuchar (lector) a este Centro de eventos.

	![policies](./media/hdinsight-storm-develop-csharp-event-hub-topology/policy.png)

5. Después de guardar las directivas, utilice el **generador de claves de acceso compartido** que se encuentra en la parte inferior de la página para recuperar la clave para las directivas de **escritor** y **lector**. Guárdelas ya que se utilizarán después.

## Configuración del almacenamiento de tabla

El almacenamiento de tabla se utilizará para contener valores leído desde el Centro de eventos, debido a que puede ver fácilmente el almacenamiento de tabla desde Visual Studio mediante el **Explorador de servidores**. Utilice los siguientes pasos para crear un almacenamiento de tabla nuevo.

1. En el [Portal de Azure](https://manage.windowsazure.com), seleccione **NUEVO | Servicios de datos | Almacenamiento | Creación rápida**.

	![quick create storage](./media/hdinsight-storm-develop-csharp-event-hub-topology/storagecreate.png)

2. Escriba un **nombre** para la cuenta de almacenamiento, seleccione una **ubicación** y, a continuación, seleccione la **marca de verificación** para crear la cuenta de almacenamiento.

	> [AZURE.NOTE] Debe seleccionar la misma **ubicación** del Centro de eventos y del servidor de Storm en HDInsight para disminuir la latencia y los costos.

3. Una vez que se aprovisiona la cuenta de almacenamiento nueva, seleccione la cuenta y, a continuación, utilice el vínculo **Administrar claves de acceso** que se encuentra en la parte inferior de la página para recuperar el **nombre de la cuenta de almacenamiento** y la **clave de acceso primaria**. Guarde esta información, porque se utilizará más adelante.

	![access keys](./media/hdinsight-storm-develop-csharp-event-hub-topology/managekeys.png)

## Creación de EventHubWriter

En esta sección se creará una topología que escribe datos en el Centro de eventos usando el bolt de Centro de eventos.

1. Si todavía no tiene instalada la versión más reciente de las herramientas de HDInsight para Visual Studio, consulte <a href="../hdinsight-hadoop-visual-studio-tools-get-started/" target="_blank">Introducción al uso de las herramientas de HDInsight para Visual Studio</a>.

2. Abra Visual Studio, seleccione **Archivo**, **Nuevo** y, a continuación, **Proyecto**.

3. En el cuadro de diálogo **Nuevo proyecto**, expanda **Instalado**, **Plantillas** y seleccione **HDInsight**. En la lista de plantillas, seleccione **Aplicación de Storm**. En la parte inferior del cuadro de diálogo, escriba **EventHubWriter** como el nombre de la aplicación.

	![image](./media/hdinsight-storm-develop-csharp-event-hub-topology/newproject.png)

4. Una vez creado el proyecto, debe tener los siguientes archivos:

	* **Program.cs**: define la topología para el proyecto. Tenga en cuenta que se ha creado una topología predeterminada que consta de un spout y un bolt de manera predeterminada

	* **Spout.cs**: un spout de ejemplo.

	* **Bolt.cs**: un bolt de ejemplo. Esto se eliminará cuando use el bolt de Centro de eventos para escribir en el Centro de eventos.

### Configuración

1. En el **Explorador de soluciones**, haga clic con el botón secundario en **EventHubWriter** y, a continuación, seleccione **Propiedades**.

2. En las propiedades del proyecto, seleccione **Configuración** y, a continuación, seleccione **Este proyecto no contiene un archivo de configuración predeterminado. Haga clic aquí para crear uno.**

3. Escriba la siguiente configuración. Use la información para el Centro de eventos que creó anteriormente en la columna **Valor**.

	<table>
	<tr><th style="text-align:left">Nombre</th><th style="text-align:left">Tipo</th><th style="text-align:left">Scope</th></tr>
	<tr><td style="text-align:left">EventHubPolicyName</td><td style="text-align:left">cadena</td><td style="text-align:left">Application</td></tr>
	<tr><td style="text-align:left">EventHubPolicyKey</td><td style="text-align:left">cadena</td><td style="text-align:left">Application</td></tr>
	<tr><td style="text-align:left">EventHubNamespace</td><td style="text-align:left">cadena</td><td style="text-align:left">Application</td></tr>
	<tr><td style="text-align:left">EventHubName</td><td style="text-align:left">cadena</td><td style="text-align:left">Application</td></tr>
	<tr><td style="text-align:left">EventHubPartitionCount</td><td style="text-align:left">int</td><td style="text-align:left">Application</td></tr>
	</table>

4. Guarde y cierre la página de propiedades.

### Definición de la topología

1. En el **Explorador de soluciones**, haga clic con el botón secundario en **Bolt.cs** y seleccione **Eliminar**. No necesita este archivo, porque usa el bolt de Centro de eventos de Java.

2. Abra el archivo **Program.cs** y agregue lo siguiente inmediatamente después de la línea `TopologyBuilder topologyBuilder = new TopologyBuilder("EventHubWriter");`.

		int partitionCount = Properties.Settings.Default.EventHubPartitionCount;
		List<string> javaDeserializerInfo =
            new List<string>() { "microsoft.scp.storm.multilang.CustomizedInteropJSONDeserializer", "java.lang.String" };

	La primera línea lee el número de particiones desde las propiedades anteriormente definidas. La segunda línea define un deserializador que se usa para deserializar los datos JSON que genera el spout. en un `java.lang.String` para que el bolt de Centro de eventos pueda consumir los datos.

4. Encuentre el código siguiente.

		topologyBuilder.SetSpout(
            "Spout",
            Spout.Get,
            new Dictionary<string, List<string>>()
            {
                {Constants.DEFAULT_STREAM_ID, new List<string>(){"count"}}
            },
            1);

	Reemplácelo por el siguiente.

		topologyBuilder.SetSpout(
            "Spout",
            Spout.Get,
            new Dictionary<string, List<string>>()
            {
                {Constants.DEFAULT_STREAM_ID, new List<string>(){"Event"}}
            },
            partitionCount).
            DeclareCustomizedJavaDeserializer(javaDeserializerInfo);

	De este modo, se crea un spout y se utiliza el número de particiones de Centro de eventos como indicación de paralelismo de este componente. Con esto se debe crear una instancia del spout para cada partición.
	
	Esto también indica que el flujo de salida debe usar el deserializador anteriormente creado.

5. Agregue lo siguiente inmediatamente después del código anterior.

		JavaComponentConstructor constructor =
            JavaComponentConstructor.CreateFromClojureExpr(
            String.Format(@"(com.microsoft.eventhubs.bolt.EventHubBolt. (com.microsoft.eventhubs.bolt.EventHubBoltConfig. " +
            @"""{0}"" ""{1}"" ""{2}"" ""{3}"" ""{4}"" {5}))",
            Properties.Settings.Default.EventHubPolicyName,
            Properties.Settings.Default.EventHubPolicyKey,
            Properties.Settings.Default.EventHubNamespace,
            "servicebus.windows.net", //suffix for servicebus fqdn
            Properties.Settings.Default.EventHubName, 
			"true"));

	Con esto se crea un constructor nuevo para el bolt de Java, que se usa en tiempo de ejecución para configurar una instancia nueva del bolt. En este caso, utiliza <a href="http://storm.apache.org/documentation/Clojure-DSL.html" target="_blank">Apache Storm Clojure DSL</a> para configurar el spout con la información de configuración de Centro de eventos que agregó anteriormente. Más específicamente, HDInsight utiliza este código en tiempo de ejecución para hacer lo siguiente:

	* Crear una instancia nueva de **com.microsoft.eventhubs.bolt.EventHubBoltConfig** con la información de Centro de eventos que proporciona.
	* Crear una instancia nueva de **com.microsoft.eventhubs.bolt.EventHubBolt**, que se pasa en la instancia **EventHubBoltConfig**.

6. Encuentre el código siguiente.

		topologyBuilder.SetBolt(
            "Bolt",
            Bolt.Get,
            new Dictionary<string, List<string>>(),
            1).shuffleGrouping("Spout");

	Reemplácelo por el siguiente.

		topologyBuilder.SetJavaBolt(
            "EventHubBolt",
            constructor,
            partitionCount).
            shuffleGrouping("Spout");

	Este código indica a la topología que utilice **JavaComponentConstructor** del paso anterior como el bolt. En esta topología, es posible que el componente se conozca con el nombre descriptivo de "EventHubBolt". La indicación de paralelismo se define en la cantidad de particiones para el Centro de eventos y se suscribe a datos generados por el spout ("Spout").

En este punto ha terminado con **Program.cs**. La topología está definida, pero ahora debe modificar **Spout.cs** para que genere datos en un formato que el bolt de Centro de eventos puede utilizar.

### Modificación del spout

El bolt de Centro de eventos espera un valor de cadena único, el que se redirigirá al Centro de eventos. En el siguiente ejemplo, modificará el archivo **Spout.cs** predeterminado para que genere una cadena JSON.

1. En el **Explorador de soluciones**, haga clic con el botón secundario en el proyecto **EventHubWriter** y seleccione **Administrar paquetes NuGet**. Busque el paquete **Json.Net** y, a continuación, agréguelo a la solución. Esto nos permitirá crear fácilmente datos JSON que se enviarán al Centro de eventos mediante el bolt.

1. Abra **Spout.cs** y agregue lo siguiente en la parte superior del archivo.

		using Newtonsoft.Json;
		using Newtonsoft.Json.Linq;

	Esto nos permitirá trabajar con los datos JSON con mayor facilidad.

3. Encuentre el código siguiente.

		Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
        outputSchema.Add("default", new List<Type>() { typeof(int) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(null, outputSchema));

	Reemplácelo por el siguiente.

		Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
        outputSchema.Add("default", new List<Type>() { typeof(string) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(null, outputSchema));
        this.ctx.DeclareCustomizedSerializer(new CustomizedInteropJSONSerializer());

	Con esto cambia la definición de los datos creados por el spout para utilizar datos de **cadena** y un **serializador JSON personalizado**.

2. Reemplace el método **NextTuple** por lo siguiente.

		public void NextTuple(Dictionary<string, Object> parms)
        {
            JObject eventData = new JObject();
            eventData.Add("deviceId", r.Next(10));
            eventData.Add("deviceValue", r.Next());
            ctx.Emit(new Values(eventData.ToString(Formatting.None)));
        }

	Esto generará de manera aleatoria un identificador de dispositivo, un valor y, a continuación, usará Json.net para emitir un objeto JSON con estos valores.

3. Guarde el archivo **Spout.cs**.

En este punto, tiene una topología básica que generará datos aleatorios y los almacenarán en el Centro de eventos mediante el uso del bolt de Centro de eventos. A continuación, creará el lector.

## Creación de EventHubReader

En esta sección, creará una topología que lee datos desde el Centro de eventos mediante el spout de Centro de eventos.

2. Abra Visual Studio, seleccione **Archivo**, **Nuevo** y, a continuación, **Proyecto**.

3. En el cuadro de diálogo **Nuevo proyecto**, expanda **Instalado**, **Plantillas** y seleccione **HDInsight**. En la lista de plantillas, seleccione **Aplicación de Storm**. En la parte inferior del cuadro de diálogo, escriba **EventHubReader** como el nombre de la aplicación.

### Configuración

1. En el **Explorador de soluciones**, haga clic con el botón derecho en **EventHubReader** y, a continuación, seleccione **Propiedades**.

2. En las propiedades del proyecto, seleccione **Configuración** y, a continuación, seleccione **Este proyecto no contiene un archivo de configuración predeterminado. Haga clic aquí para crear uno.**

3. Escriba la siguiente configuración. Utilice la información para el Centro de eventos y la cuenta de almacenamiento que creó anteriormente en la columna **Valor**.

	<table>
	<tr><th style="text-align:left">Nombre</th><th style="text-align:left">Tipo</th><th style="text-align:left">Scope</th></tr>
	<tr><th style="text-align:left">EventHubPolicyName</th><th style="text-align:left">cadena</th><th style="text-align:left">Application</th></tr>
	<tr><th style="text-align:left">EventHubPolicyKey</th><th style="text-align:left">cadena</th><th style="text-align:left">Application</th></tr>
	<tr><th style="text-align:left">EventHubNamespace</th><th style="text-align:left">cadena</th><th style="text-align:left">Application</th></tr>
	<tr><th style="text-align:left">EventHubName</th><th style="text-align:left">cadena</th><th style="text-align:left">Application</th></tr>
	<tr><th style="text-align:left">EventHubPartitionCount</th><th style="text-align:left">int</th><th style="text-align:left">Application</th></tr>
	<tr><th style="text-align:left">StorageConnection</th><th style="text-align:left">(cadena de conexión)</th><th style="text-align:left">Application</th></tr>
	<tr><th style="text-align:left">TableName</th><th style="text-align:left">cadena</th><th style="text-align:left">Application</th></tr>
	</table>

	En **TableName**, escriba el nombre de la tabla en la que desea que se almacenen los eventos.

	La topología usará estos valores para comunicarse con Centro de eventos y almacenamiento de tabla.

4. Guarde y cierre la página de propiedades.

### Definición de la topología

1. En el **Explorador de soluciones**, haga clic con el botón secundario en **Spout.cs** y seleccione **Eliminar**. No necesita este archivo, porque utiliza el spout de Centro de eventos de Java.

2. Abra el archivo **Program.cs** y agregue lo siguiente inmediatamente después de la línea `TopologyBuilder topologyBuilder = new TopologyBuilder("EventHubReader");`.

		int partitionCount = Properties.Settings.Default.EventHubPartitionCount;
		JavaComponentConstructor constructor = JavaComponentConstructor.CreateFromClojureExpr(
            String.Format(@"(com.microsoft.eventhubs.spout.EventHubSpout. (com.microsoft.eventhubs.spout.EventHubSpoutConfig. " +
                @"""{0}"" ""{1}"" ""{2}"" ""{3}"" {4} ""{5}""))",
                Properties.Settings.Default.EventHubPolicyName,
                Properties.Settings.Default.EventHubPolicyKey,
                Properties.Settings.Default.EventHubNamespace,
                Properties.Settings.Default.EventHubName,
                partitionCount,
                "")); //Last value is the zookeeper connection string - leave empty

	Se lee el número de particiones y se asigna a una variable local, dado que de utilizará varias veces.

	El `JavaComponentConstructor` define la manera en que se construirá el spout de Java en tiempo de ejecución. En este caso, utiliza <a href="http://storm.apache.org/documentation/Clojure-DSL.html" target="_blank">Apache Storm Clojure DSL</a> para configurar el spout con la información de configuración de Centro de eventos que agregó anteriormente. Más específicamente, HDInsight utiliza este código en tiempo de ejecución para hacer lo siguiente:

	* Crear una instancia nueva de **com.microsoft.eventhubs.spout.EventHubSpoutConfig** con la información de Centro de eventos que proporciona.
	
	* Crear una instancia nueva de **com.microsoft.eventhubs.spout.EventHubSpout**, que se pasa en la instancia de **EventHubSpoutConfig**.

5. Encuentre el código siguiente.

		topologyBuilder.SetSpout(
            "Spout",
            Spout.Get,
            new Dictionary<string, List<string>>()
            {
                {Constants.DEFAULT_STREAM_ID, new List<string>(){"count"}}
            },
            1);

	Reemplácelo por el siguiente.

        topologyBuilder.SetJavaSpout(
            "EventHubSpout",
            constructor,
            partitionCount);

	Este código indica a la topología que utilice **JavaComponentConstructor** del paso anterior como el spout y que le asigne un nombre de "EventHubSpout". También define la indicación de paralelismo para este componente en la cantidad de particiones en Centro de eventos. 

2. Agregue lo siguiente inmediatamente después del código anterior.

         List<string> javaSerializerInfo = new List<string>() { "microsoft.scp.storm.multilang.CustomizedInteropJSONSerializer" };

	Con esto se crea un serializador personalizado, el que se utilizará para serializar la información generada por el spout de Java en formato JSON.

3. Encuentre el código siguiente.

		topologyBuilder.SetBolt(
            "Bolt",
            Bolt.Get,
            new Dictionary<string, List<string>>(),
            1).shuffleGrouping("Spout");

	Y reemplácelo con lo siguiente.

		topologyBuilder.SetBolt(
            "Bolt",
            Bolt.Get,
            new Dictionary<string, List<string>>(),
            partitionCount).
            DeclareCustomizedJavaSerializer(javaSerializerInfo).
            shuffleGrouping("EventHubSpout");

	Este código indica a la topología que utilice un bolt (definido en Bolt.cs). Las adiciones indican a la topología que utilice el serializador personalizado para los datos consumidor por este componente, que provienen de **EventHubSpout**, es decir, el spout de Java.

En este punto ha terminado con **Program.cs**. Se definió la topología, pero ahora debe crear una clase auxiliar para escribir datos en el almacenamiento de tabla y, a continuación, debe modificar **Bolt.cs** para que pueda comprender los datos generados por el spout.

### Creación de una clase auxiliar

Al escribir datos en el almacenamiento de tabla, debe crear una clase que describe los datos que se escribirán.

1. En el **Explorador de soluciones**, haga clic con el botón secundario en el proyecto **EventHubReader** y seleccione **Agregar**, **Nueva clase**. Llame a la nueva clase **Devices.cs**.

2. Abra **Devices.cs** y reemplace el código predeterminado por lo siguiente.

		using System;
		using System.Collections.Generic;
		using System.Linq;
		using System.Text;
		using System.Threading.Tasks;
		using Microsoft.WindowsAzure.Storage.Table;
		
		namespace EventHubReader
		{
		    class Device : TableEntity
		    {
		        public int value { get; set; }
		
		        public Device() { }
		        public Device(int id)
		        {
		            this.PartitionKey = id.ToString();
		            this.RowKey = System.Guid.NewGuid().ToString();
		        }
		    }
		}

	Esto creará las entidades en el almacenamiento de tabla que se componen de una clave de partición (que se definirá en el identificador del dispositivo, leído desde Centro de eventos), una clave de fila única y un valor que se lee desde Centro de eventos. Cada entidad tendrá también una marca de tiempo, que se creará automáticamente cuando la entidad se inserte en la tabla.

### Modificación del bolt

1. En el **Explorador de soluciones**, haga clic con el botón secundario en el proyecto **EventHubReader** y seleccione **Administrar paquetes NuGet**. Busque el paquete **Json.Net** y, a continuación, agréguelo a la solución. Esto nos permitirá procesar fácilmente los datos JSON recibidos del spout. Agregue también el paquete **Almacenamiento de Microsoft Azure**, que nos permitirá escribir en el almacenamiento de tabla.

1. Abra **Bolt.cs** y agregue lo siguiente en la parte superior del archivo.

		using Newtonsoft.Json.Linq;
		using Microsoft.WindowsAzure.Storage;
		using Microsoft.WindowsAzure.Storage.Table;

	Esto nos permitirá trabajar más fácilmente con datos JSON desde el bolt y escribir datos en almacenamiento de tabla.

2. Encuentre la instrucción `private int count;` y reemplácela por lo siguiente.

        private CloudTable table;

	Esto se usará al conectarse a la tabla.

4. Encuentre el código siguiente.

		Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
        inputSchema.Add("default", new List<Type>() { typeof(int) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, null));

	Reemplácelo por el siguiente.

		Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
        inputSchema.Add("default", new List<Type>() { typeof(string) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, null));
        this.ctx.DeclareCustomizedDeserializer(new CustomizedInteropJSONDeserializer());

	Esto indica al bolt que recibirá un valor de **string** en lugar de un **int** y que los datos se deben deserializar mediante el **CustomizedInteropJSONDeserialzer**.

3. Agregue lo siguiente inmediatamente después del código anterior.

		CloudStorageAccount storageAccount = CloudStorageAccount.Parse(Properties.Settings.Default.StorageConnection);
        CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
        table = tableClient.GetTableReference(Properties.Settings.Default.TableName);
        table.CreateIfNotExists();

	Esto se conectará a la tabla de **eventos** usando la cadena de conexión anteriormente configurada. Si no existe, se creará.

2. Encuentre el método **Execute** y reemplácelo por lo siguiente.

		public void Execute(SCPTuple tuple)
        {
            Context.Logger.Info("Processing events");
            string eventValue = (string)tuple.GetValue(0);
            if (eventValue != null)
            {
                JObject eventData = JObject.Parse(eventValue);

                Device device = new Device((int)eventData["deviceId"]);
                device.value = (int)eventData["deviceValue"];

                TableOperation insertOperation = TableOperation.Insert(device);

                table.Execute(insertOperation);
            }
        }

	Esto utiliza Json.net para analizar los datos JSON desde el spout y, a continuación, recoge los campos **deviceId** y **deviceValue**. Se crea un nuevo objeto de **dispositivo**, usando **deviceId** durante la inicialización para definir la clave de partición de la tabla. De este modo, se define el valor en **deviceValue** y, finalmente, la entidad se inserta en la tabla.

En este punto, tiene una topología completa que leerá datos desde Centro de eventos y los almacenará en almacenamiento de tabla en una tabla llamada **eventos**.

## Implementación de las topologías

1. Abra la solución **EventHubReader**. En el **Explorador de soluciones**, haga clic con el botón secundario en el proyecto **EventHubReader** y seleccione **Enviar a Storm en HDInsight**.

	![submit to storm](./media/hdinsight-storm-develop-csharp-event-hub-topology/submittostorm.png)

2. En el cuadro de diálogo **Enviar topología**, seleccione su **clúster de Storm**. Expanda **Configuraciones adicionales**, seleccione **Rutas de acceso del archivo Java**, **...** y, a continuación, seleccione el directorio que contiene el archivo **eventhubs-storm-spout-0.9-jar-with-dependencies.jar** anteriormente descargado. Por último, seleccione **Enviar**.

	![Image of submission dialog](./media/hdinsight-storm-develop-csharp-event-hub-topology/submit.png)

3. Una vez que se ha enviado la topología, aparecerá el **visor de topologías de Storm**. Seleccione la topología **EventHubReader** a la izquierda del cuadro de diálogo para ver estadísticas de la topología. Actualmente, no debe haber ocurrido nada, puesto que todavía no se han escrito eventos en Centro de eventos.

	![example storage view](./media/hdinsight-storm-develop-csharp-event-hub-topology/topologyviewer.png)

4. Abra la solución **EventHubWriter**. En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto **EventHubWriter** y seleccione **Enviar a Storm en HDInsight**.

2. En el cuadro de diálogo **Enviar topología**, seleccione su **clúster de Storm**. Expanda **Configuraciones adicionales**, seleccione **Rutas de acceso del archivo Java**, **...** y, a continuación, seleccione el directorio que contiene el archivo **eventhubs-storm-spout-0.9-jar-with-dependencies.jar** anteriormente descargado. Por último, seleccione **Enviar**.

5. Una vez que se ha enviado la topología, actualice la lista de topologías en el **visor de topologías de Stom** para comprobar que ambas se estén ejecutando en el clúster.

6. Una vez que ambas estén en ejecución, seleccione el **Explorador de servidores**, expanda **Azure**, **Almacenamiento** y, por último, expanda la cuenta de almacenamiento que creó anteriormente. En la cuenta de almacenamiento, expanda **Tablas**. Finalmente, haga doble clic en la tabla de **eventos** para abrirla. Debiera ver datos que se han almacenado en la tabla desde la topología **EventHubReader**.

	* La topología **EventHubWriter** genera los eventos y los escribe en el Centro de eventos.

	* **EventHubReader** luego lee los eventos desde Centro de eventos y los almacena en el almacenamiento de tabla, en la tabla de **eventos**.

## Detención de las topologías

Para detener las topologías, seleccione cada topología en el **visor de topologías de Storm** y, a continuación, seleccione **Eliminar**.

![image of killing a topology](./media/hdinsight-storm-develop-csharp-event-hub-topology/killtopology.png)

## Resumen

En este documento aprendió a utilizar el spout y bolt de Centro de eventos de Java desde una topología de C# para trabajar con datos en Centro de eventos de Azure. Para obtener más información acerca de cómo crear topologías de C#, consulte lo siguiente

* [Desarrollo de topologías de C# para Apache Storm en HDInsight con Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md)

* [Ejemplos de Storm en HDInsight](https://github.com/hdinsight/hdinsight-storm-examples)








<!--HONumber=47-->
