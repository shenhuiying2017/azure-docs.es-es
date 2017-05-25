---
title: "Topologías de Apache Storm con Visual Studio y C# - Azure | Microsoft Docs"
description: "Aprenda a crear topologías de Storm en C# al crear una sencilla topología de recuento de palabras en Visual Studio con las herramientas de Hadoop para Visual Studio."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 380d804f-a8c5-4b20-9762-593ec4da5a0d
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/01/2017
ms.author: larryfr
ms.translationtype: Human Translation
ms.sourcegitcommit: 95b8c100246815f72570d898b4a5555e6196a1a0
ms.openlocfilehash: 7b267ba427da276f53326c9068417521c8976e63
ms.contentlocale: es-es
ms.lasthandoff: 05/18/2017


---
# <a name="develop-c-topologies-for-apache-storm-on-hdinsight-using-hadoop-tools-for-visual-studio"></a>Desarrollo de topologías de C# para Apache Storm en HDInsight con herramientas de Hadoop para Visual Studio

Aprenda a crear una topología de Storm de C# con Herramientas de Data Lake (Hadoop) para Visual Studio. Este documento le guía a través del proceso de creación de un proyecto de Storm en Visual Studio, probarlo localmente e implementarlo en un clúster de Apache Storm en HDInsight.

También aprenderá a crear topologías híbridas que usan componentes de C# y Java.

> [!IMPORTANT]
> Aunque los pasos descritos en este documento se basan en un entorno de desarrollo de Windows con Visual Studio, el proyecto compilado se puede enviar a un clúster de HDInsight basado en Windows o Linux. __Solo los clústeres basados en Linux creados con posterioridad al 28/10/2016 admiten topologías SCP.NET__.
>
> Para usar una topología de C# con un clúster basado en Linux, debe actualizar el paquete de NuGet Microsoft.SCP.Net.SDK usado en el proyecto a la versión 0.10.0.6 u otra superior. La versión del paquete también debe coincidir con la versión principal de Storm instalada en HDInsight. Por ejemplo, en las versiones de HDInsight 3.3 y 3.4 se usa Storm 0.10.x, mientras que en HDInsight 3.5 se usa Storm 1.0.x.
>
> Las topologías de C# en clústeres basados en Linux deben usar .NET 4.5, y emplear Mono para ejecutarse en el clúster de HDInsight. La mayoría funcionará; no obstante, debe comprobar el documento de [compatibilidad de Mono](http://www.mono-project.com/docs/about-mono/compatibility/) para ver las posibles incompatibilidades.


## <a name="prerequisites"></a>Requisitos previos

* [Java](https://java.com) 1.7 o superior en el entorno de desarrollo. Java se utiliza para empaquetar la topología cuando se envía al clúster de HDInsight.

  * La variable de entorno **JAVA_HOME** debe señalar al directorio que contiene Java.
  * El directorio **%JAVA_HOME%/bin** debe estar en la ruta de acceso.

* Una de las siguientes versiones de Visual Studio:

  * Visual Studio 2012 con [Update 4](http://www.microsoft.com/download/details.aspx?id=39305)
  * Visual Studio 2013 con [Update 4](http://www.microsoft.com/download/details.aspx?id=44921) o [Visual Studio Community 2013](http://go.microsoft.com/fwlink/?LinkId=517284)
  * Visual Studio 2015 o [Visual Studio Community 2015](https://go.microsoft.com/fwlink/?LinkId=532606)
  * Visual Studio 2017 (cualquier edición)

* Azure SDK 2.9.5 o posterior

* Herramientas de Data Lake para Visual Studio: para instalarlo y configurarlo, vea [Conectarse a Azure HDInsight y ejecutar consultas de Hive con Herramientas de Data Lake para Visual Studio](hdinsight-hadoop-visual-studio-tools-get-started.md).

  > [!NOTE]
  > Herramientas de Data Lake para Visual Studio no se puede usar en Visual Studio Express.

* Clúster Apache Storm en HDInsight: vea [Introducción a Apache Storm en HDInsight](hdinsight-apache-storm-tutorial-get-started.md) para conocer los pasos para crear un clúster.

  > [!IMPORTANT]
  > Linux es el único sistema operativo que se usa en la versión 3.4 de HDInsight, o en las superiores. Para más información, consulte [El contrato de nivel de servicio para las versiones de clúster de HDInsight](hdinsight-component-versioning.md#hdi-version-33-nearing-deprecation-date).

## <a name="templates"></a>Plantillas

Herramientas de Data Lake para Visual Studio proporciona las siguientes plantillas:

| Tipo de proyecto | Muestra |
| --- | --- |
| Storm Application |Un proyecto vacío de topología de Storm |
| Storm Azure SQL Writer Sample |Cómo escribir en Base de datos SQL de Azure |
| Storm Azure Cosmos DB Reader Sample |Cómo leer desde Azure Cosmos DB |
| Storm Azure Cosmos DB Writer Sample |Cómo escribir en Azure Cosmos DB |
| Storm EventHub Reader Sample |Cómo leer en los centros de eventos de Azure |
| Storm EventHub Writer Sample |Cómo escribir en los centros de eventos de Azure |
| Storm HBase Reader Sample |Cómo leer de HBase en clústeres de HDInsight |
| Storm HBase Writer Sample |Cómo escribir en HBase en clústeres de HDInsight |
| Storm Hybrid Sample |Cómo utilizar un componente de Java |
| Storm Sample |Una topología de recuento de palabras básica |

En los pasos de este documento, usará el tipo de proyecto Storm Application básico para crear una topología.

### <a name="hbase-templates-notes"></a>Notas de plantillas de HBase

Las plantillas de lector y escritor de HBase usan la API de REST de HBase para comunicarse con un HBase en un clúster de HDInsight, no la API de Java de HBase.

### <a name="eventhub-templates-notes"></a>Notas de plantillas de EventHub

> [!IMPORTANT]
> El componente de spout de EventHub basado en Java incluido con la plantilla de lector de EventHub no funcionará con Storm en HDInsight versión 3.5. En su lugar, utilice el componente de spout de EventHub de [https://000aarperiscus.blob.core.windows.net/certs/storm-eventhubs-1.0.2-jar-with-dependencies.jar](https://000aarperiscus.blob.core.windows.net/certs/storm-eventhubs-1.0.2-jar-with-dependencies.jar).

Para conocer una topología de ejemplo que utiliza este componente y funciona con Storm en HDInsight 3.5, vea [https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub).

## <a name="create-a-c-topology"></a>Creación de una topología de C#

1. Si todavía no tiene instalada la versión más reciente de Herramientas de Data Lake para Visual Studio, vea [Conectarse a Azure HDInsight y ejecutar consultas de Hive con Herramientas de Data Lake para Visual Studio](hdinsight-hadoop-visual-studio-tools-get-started.md).

2. Abra Visual Studio, seleccione **Archivo** > **Nuevo** y luego **Proyecto**.

3. En la pantalla **Nuevo proyecto**, expanda **Instalado** > **Plantillas** y seleccione **Azure Data Lake**. En la lista de plantillas, seleccione **Aplicación de Storm**. En la parte inferior de la pantalla, escriba **WordCount** como nombre de la aplicación.

    ![imagen](./media/hdinsight-storm-develop-csharp-visual-studio-topology/new-project.png)

4. Cuando se haya creado el proyecto, debe tener los siguientes archivos:

   * **Program.cs**: este archivo define la topología del proyecto. Una topología predeterminada que consta de un spout y un bolt se crea de manera predeterminada.

   * **Spout.cs**: un spout de ejemplo que emite números aleatorios

   * **Bolt.cs**: un bolt de ejemplo que mantiene un recuento de los números emitidos por el spout.

     Como parte de la creación del proyecto, el [paquete SCP.NET](https://www.nuget.org/packages/Microsoft.SCP.Net.SDK/) más reciente se descargará de NuGet.

     [!INCLUDE [scp.net version important](../../includes/hdinsight-storm-scpdotnet-version.md)]

En las secciones siguientes, modificará este proyecto en una aplicación básica de WordCount.

### <a name="implement-the-spout"></a>Implementación del spout

1. Abra **Spout.cs**. Los spouts se usan para leer los datos en una topología de un origen externo. Los componentes principales de un spout son:

   * **NextTuple**: llamado por Storm cuando se permite que el spout emita nuevas tuplas

   * **Ack** (solo topología transaccional): controla las confirmaciones iniciadas por otros componentes de la topología para tuplas enviadas desde el spout. La confirmación de una tupla permite que el spout conozca que se ha procesado correctamente por componentes de bajada.

   * **Fail** (solo topología transaccional): controla las tuplas que producen un error al procesar otros componentes de la topología. Implementar un método Fail le permite volver a emitir la tupla para que se pueda procesar de nuevo.

2. Reemplace el contenido de la clase **Spout** por el texto siguiente. Este spout emite aleatoriamente una frase en la topología.

    ```csharp
    private Context ctx;
    private Random r = new Random();
    string[] sentences = new string[] {
        "the cow jumped over the moon",
        "an apple a day keeps the doctor away",
        "four score and seven years ago",
        "snow white and the seven dwarfs",
        "i am at two with nature"
    };

    public Spout(Context ctx)
    {
        // Set the instance context
        this.ctx = ctx;

        Context.Logger.Info("Generator constructor called");

        // Declare Output schema
        Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
        // The schema for the default output stream is
        // a tuple that contains a string field
        outputSchema.Add("default", new List<Type>() { typeof(string) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(null, outputSchema));
    }

    // Get an instance of the spout
    public static Spout Get(Context ctx, Dictionary<string, Object> parms)
    {
        return new Spout(ctx);
    }

    public void NextTuple(Dictionary<string, Object> parms)
    {
        Context.Logger.Info("NextTuple enter");
        // The sentence to be emitted
        string sentence;

        // Get a random sentence
        sentence = sentences[r.Next(0, sentences.Length - 1)];
        Context.Logger.Info("Emit: {0}", sentence);
        // Emit it
        this.ctx.Emit(new Values(sentence));

        Context.Logger.Info("NextTuple exit");
    }

    public void Ack(long seqId, Dictionary<string, Object> parms)
    {
        // Only used for transactional topologies
    }

    public void Fail(long seqId, Dictionary<string, Object> parms)
    {
        // Only used for transactional topologies
    }
    ```

    Dedique un momento a leer los comentarios para entender lo que hace este código.

### <a name="implement-the-bolts"></a>Implementación de los bolts

1. Elimine el archivo **Bolt.cs** existente del proyecto.

2. En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto y seleccione **Agregar** > **Nuevo elemento**. En la lista, seleccione **Bolt de Storm** y escriba **Splitter.cs** como nombre. Repita este proceso para crear un segundo bolt denominado **Counter.cs**.

   * **Splitter.cs**: implementa un bolt que divide la frase en palabras individuales y emite una nueva secuencia de palabras.

   * **Counter.cs**: implementa un bolt que cuenta cada palabra y emite una nueva secuencia de palabras y el recuento de cada palabra.

     > [!NOTE]
     > Estos bolts leen y escriben en las secuencias, pero también se puede usar un bolt para comunicarse con orígenes como una base de datos o un servicio.

3. Abra **Splitter.cs**. Solo tiene un método de forma predeterminada: **Execute**. El método Execute se llama cuando el bolt recibe una tupla para el procesamiento. En este caso, puede leer y procesar las tuplas entrantes y emitir tuplas salientes.

4. Reemplace el contenido de la clase **Splitter** por el código siguiente:

    ```csharp
    private Context ctx;

    // Constructor
    public Splitter(Context ctx)
    {
        Context.Logger.Info("Splitter constructor called");
        this.ctx = ctx;

        // Declare Input and Output schemas
        Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
        // Input contains a tuple with a string field (the sentence)
        inputSchema.Add("default", new List<Type>() { typeof(string) });
        Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
        // Outbound contains a tuple with a string field (the word)
        outputSchema.Add("default", new List<Type>() { typeof(string) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, outputSchema));
    }

    // Get a new instance of the bolt
    public static Splitter Get(Context ctx, Dictionary<string, Object> parms)
    {
        return new Splitter(ctx);
    }

    // Called when a new tuple is available
    public void Execute(SCPTuple tuple)
    {
        Context.Logger.Info("Execute enter");

        // Get the sentence from the tuple
        string sentence = tuple.GetString(0);
        // Split at space characters
        foreach (string word in sentence.Split(' '))
        {
            Context.Logger.Info("Emit: {0}", word);
            //Emit each word
            this.ctx.Emit(new Values(word));
        }

        Context.Logger.Info("Execute exit");
    }
    ```

    Dedique un momento a leer los comentarios para entender lo que hace este código.

5. Abra **Counter.cs** y reemplace el contenido de la clase por lo siguiente:

    ```csharp
    private Context ctx;

    // Dictionary for holding words and counts
    private Dictionary<string, int> counts = new Dictionary<string, int>();

    // Constructor
    public Counter(Context ctx)
    {
        Context.Logger.Info("Counter constructor called");
        // Set instance context
        this.ctx = ctx;

        // Declare Input and Output schemas
        Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
        // A tuple containing a string field - the word
        inputSchema.Add("default", new List<Type>() { typeof(string) });

        Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
        // A tuple containing a string and integer field - the word and the word count
        outputSchema.Add("default", new List<Type>() { typeof(string), typeof(int) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, outputSchema));
    }

    // Get a new instance
    public static Counter Get(Context ctx, Dictionary<string, Object> parms)
    {
        return new Counter(ctx);
    }

    // Called when a new tuple is available
    public void Execute(SCPTuple tuple)
    {
        Context.Logger.Info("Execute enter");

        // Get the word from the tuple
        string word = tuple.GetString(0);
        // Do we already have an entry for the word in the dictionary?
        // If no, create one with a count of 0
        int count = counts.ContainsKey(word) ? counts[word] : 0;
        // Increment the count
        count++;
        // Update the count in the dictionary
        counts[word] = count;

        Context.Logger.Info("Emit: {0}, count: {1}", word, count);
        // Emit the word and count information
        this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new List<SCPTuple> { tuple }, new Values(word, count));
        Context.Logger.Info("Execute exit");
    }
    ```

    Dedique un momento a leer los comentarios para entender lo que hace este código.

### <a name="define-the-topology"></a>Definición de la topología

Los spouts y bolts se organizan en un gráfico, que define cómo fluyen los datos entre componentes. Para esta topología, el gráfico es como sigue:

![imagen de cómo se organizan los componentes](./media/hdinsight-storm-develop-csharp-visual-studio-topology/wordcount-topology.png)

Las frases se emiten desde el spout, que se distribuyen a las instancias del bolt divisor. El bolt divisor divide las frases en palabras, que se distribuyen en el bolt contador.

Dado que el recuento de palabras se guarda localmente en la instancia del contador, queremos asegurarnos de que determinadas palabras fluyan a la misma instancia del bolt contador. Cada instancia realiza el seguimiento de palabras específicas. Dado que el bolt divisor no mantiene ningún estado, realmente no importa qué instancia del divisor recibe cada frase.

Abra **Program.cs**. El método importante aquí es **GetTopologyBuilder**, que se usa para definir la topología que se envía a Storm. Reemplace el contenido de **GetTopologyBuilder** por el código siguiente para implementar la topología descrita anteriormente:

```csharp
// Create a new topology named 'WordCount'
TopologyBuilder topologyBuilder = new TopologyBuilder("WordCount" + DateTime.Now.ToString("yyyyMMddHHmmss"));

// Add the spout to the topology.
// Name the component 'sentences'
// Name the field that is emitted as 'sentence'
topologyBuilder.SetSpout(
    "sentences",
    Spout.Get,
    new Dictionary<string, List<string>>()
    {
        {Constants.DEFAULT_STREAM_ID, new List<string>(){"sentence"}}
    },
    1);
// Add the splitter bolt to the topology.
// Name the component 'splitter'
// Name the field that is emitted 'word'
// Use suffleGrouping to distribute incoming tuples
//   from the 'sentences' spout across instances
//   of the splitter
topologyBuilder.SetBolt(
    "splitter",
    Splitter.Get,
    new Dictionary<string, List<string>>()
    {
        {Constants.DEFAULT_STREAM_ID, new List<string>(){"word"}}
    },
    1).shuffleGrouping("sentences");

// Add the counter bolt to the topology.
// Name the component 'counter'
// Name the fields that are emitted 'word' and 'count'
// Use fieldsGrouping to ensure that tuples are routed
//   to counter instances based on the contents of field
//   position 0 (the word). This could also have been
//   List<string>(){"word"}.
//   This ensures that the word 'jumped', for example, will always
//   go to the same instance
topologyBuilder.SetBolt(
    "counter",
    Counter.Get,
    new Dictionary<string, List<string>>()
    {
        {Constants.DEFAULT_STREAM_ID, new List<string>(){"word", "count"}}
    },
    1).fieldsGrouping("splitter", new List<int>() { 0 });

// Add topology config
topologyBuilder.SetTopologyConfig(new Dictionary<string, string>()
{
    {"topology.kryo.register","[\"[B\"]"}
});

return topologyBuilder;
```

Dedique un momento a leer los comentarios para entender lo que hace este código.

## <a name="submit-the-topology"></a>Envío de la topología

1. En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto y seleccione **Submit to Storm on HDInsight** (Enviar a Storm en HDInsight).

   > [!NOTE]
   > Si se le solicita, introduzca las credenciales de inicio de sesión de su suscripción de Azure. Si tiene más de una suscripción, inicie sesión en la que contenga el clúster de Storm en HDInsight.

2. Seleccione el clúster de Storm en HDInsight desde el menú desplegable **Storm Cluster**  (Clúster de Storm y seleccione **Submit** (Enviar). Puede supervisar si el envío es correcto mediante la ventana **Salida** .

3. Cuando la topología se envíe correctamente, debe aparecer **topologías de Storm** del clúster. Seleccione la topología **WordCount** en la lista para consultar la información acerca de la topología en ejecución.

   > [!NOTE]
   > También puede ver las **topologías de Storm** en el **Explorador de servidores**: expanda **Azure** > **HDInsight**, haga clic con el botón derecho en un clúster de Storm en HDInsight y luego seleccione **Ver topologías de Storm**.

    Para ver información sobre los componentes de la topología, haga doble clic en el componente en el diagrama.

4. Desde la vista **Resumen de la topología**, haga clic en **Eliminar** para detener la topología.

   > [!NOTE]
   > Las topologías de Storm continúan ejecutándose hasta que se desactiven o se elimine el clúster.

## <a name="transactional-topology"></a>Topología transaccional

La topología anterior no es transaccional. Los componentes de la topología no implementan la funcionalidad para reproducir los mensajes. Para ver un ejemplo de topología transaccional, cree un proyecto y seleccione **Muestra de Storm** como tipo de proyecto.

Las topologías transaccionales implementan lo siguiente para que admitan la reproducción de datos:

* **Almacenamiento en caché de metadatos**: el spout debe almacenar metadatos sobre los datos emitidos para que los datos puedan recuperarse y volver a emitirse si se produce un error. Dado que los datos que emite la muestra son pequeños, los datos sin procesar de cada tupla se almacenan en un diccionario para la reproducción.

* **Ack**: cada bolt de la topología puede llamar a `this.ctx.Ack(tuple)` para confirmar si el proceso de una tupla se ha realizado correctamente. Una vez que todos los bolts han confirmado la tupla, se llama al método `Ack` del spout. El método `Ack` permite al spout quitar los datos que se almacenaron en caché para la reproducción.

* **Fail**: cada bolt puede llamar a `this.ctx.Fail(tuple)` para indicar que se produjo un error al procesar una tupla. El error se propaga al método `Fail` del spout, donde la tupla puede reproducirse mediante metadatos almacenados en caché.

* **Id. de secuencia**: al emitir una tupla, se puede especificar un identificador de secuencia único. Este valor identifica la tupla para el procesamiento de reproducción (Ack y Fail). Por ejemplo, el spout del proyecto **Muestra de Storm** utiliza los siguiente al emitir datos:

        this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new Values(sentence), lastSeqId);

    Este código emite una tupla que contiene una frase a la secuencia predeterminada, con el valor de identificador de secuencia contenido en **lastSeqId**. En este ejemplo, **lastSeqId** se incrementa con cada tupla emitida.

Como se muestra en el proyecto **Muestra de Storm** , se puede establecer si un componente es transaccional en tiempo de ejecución, según la configuración.

## <a name="hybrid-topology-with-c-and-java"></a>Topología híbrida con C# y Java

Herramientas de Data Lake para Visual Studio también se puede usar para crear topologías híbridas, donde algunos componentes son de C# y otros, de Java.

Para ver una topología híbrida de ejemplo, cree un proyecto y seleccione **Muestra híbrida de Storm**. Este tipo de ejemplo ilustra los conceptos siguientes:

* **Spout de Java** y **bolt de C#**: definidos en **HybridTopology_javaSpout_csharpBolt**

    * Una versión transaccional se define en **HybridTopologyTx_javaSpout_csharpBolt**

* **Spout de C#** y **bolt de Java**: definidos en **HybridTopology_csharpSpout_javaBolt**

    * Una versión transaccional se define en **HybridTopologyTx_csharpSpout_javaBolt**

  > [!NOTE]
  > Esta versión también muestra cómo usar código de Clojure desde un archivo de texto como un componente de Java.


Para cambiar entre la topología que se usa cuando se envía el proyecto, simplemente mueva la instrucción `[Active(true)]` a la topología que quiere usar antes de enviarla al clúster.

> [!NOTE]
> Todos los archivos de Java necesarios se ofrecen como parte de este proyecto en la carpeta **JavaDependency** .


Tenga en cuenta lo siguiente al crear y enviar una topología híbrida:

* **JavaComponentConstructor** debe usarse para crear una instancia de la clase de Java para un spout o bolt.

* **microsoft.scp.storm.multilang.CustomizedInteropJSONSerializer** debe usarse para serializar datos hacia y desde los componentes de Java, a partir de objetos de Java a JSON

* Al enviar la topología al servidor, debe usar la opción **Configuraciones adicionales** para especificar las **rutas de acceso del archivo Java**. La ruta especificada debe ser el directorio que contiene los archivos JAR que contienen las clases de Java.

### <a name="azure-event-hubs"></a>Centros de eventos de Azure

La versión 0.9.4.203 de SCP.Net presenta una nueva clase y método específicos para trabajar con el spout del Centro de eventos (un spout de Java que lee desde el Centro de eventos.) Al crear una topología que usa un spout del Centro de eventos, use los métodos siguientes:

* Clase **EventHubSpoutConfig**: crea un objeto que contiene la configuración del componente spout.

* Método **TopologyBuilder.SetEventHubSpout**: agrega el componente spout del Centro de eventos a la topología.

> [!NOTE]
> Deberá seguir usando CustomizedInteropJSONSerializer para serializar los datos producidos por el spout.

## <a id="configurationmanager"></a>Uso de ConfigurationManager

No use ConfigurationManager para recuperar valores de configuración de los componentes bolt y spout. Si lo hace, puede generar una excepción de puntero nulo. En su lugar, la configuración de su proyecto se pasa a la topología de Storm como un par clave-valor en el contexto de la topología. Cada componente que se basa en los valores de configuración debe recuperarlos del contexto durante la inicialización.

El código siguiente muestra cómo recuperar estos valores:

```csharp
public class MyComponent : ISCPBolt
{
    // To hold configuration information loaded from context
    Configuration configuration;
    ...
    public MyComponent(Context ctx, Dictionary<string, Object> parms)
    {
        // Save a copy of the context for this component instance
        this.ctx = ctx;
        // If it exists, load the configuration for the component
        if(parms.ContainsKey(Constants.USER_CONFIG))
        {
            this.configuration = parms[Constants.USER_CONFIG] as System.Configuration.Configuration;
        }
        // Retrieve the value of "Foo" from configuration
        var foo = this.configuration.AppSettings.Settings["Foo"].Value;
    }
    ...
}
```

Si usa un método `Get` para devolver una instancia de su componente, debe asegurarse de que pasa los parámetros `Context` y `Dictionary<string, Object>` al constructor. El ejemplo siguiente es un método `Get` básico que pasa estos valores correctamente:

```csharp
public static MyComponent Get(Context ctx, Dictionary<string, Object> parms)
{
    return new MyComponent(ctx, parms);
}
```

## <a name="how-to-update-scpnet"></a>Cómo actualizar SCP.NET

Las versiones recientes de SCP.NET admiten la actualización de paquetes a través de NuGet. Cuando está disponible una nueva actualización, recibirá una notificación de actualización. Para comprobar manualmente una actualización, siga estos pasos:

1. En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto y seleccione **Administrar paquetes NuGet**.

2. En el administrador de paquetes, seleccione **Actualizaciones**. Si hay disponible una actualización, se mostrará una lista. Haga clic en el botón **Actualizar** para que el paquete la instale.

> [!IMPORTANT]
> Si el proyecto se creó con una versión anterior de SCP.NET que no usó NuGet, debe realizar los pasos siguientes para actualizar a una versión más reciente:
>
> 1. En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto y seleccione **Administrar paquetes NuGet**.
> 2. Mediante el campo **Búsqueda**, busque **Microsoft.SCP.Net.SDK** y agréguelo al proyecto.

## <a name="troubleshooting-common-issues-with-topologies"></a>Solución de problemas comunes en las topologías

### <a name="null-pointer-exceptions"></a>Excepciones de puntero nulo

Al usar una topología de C# con un clúster de HDInsight basado en Linux, los componentes de bolt y spout que usan ConfigurationManager para leer los valores de configuración en tiempo de ejecución pueden devolver excepciones de puntero nulo.

La configuración del proyecto se pasa a la topología de Storm como un par clave-valor en el contexto de la topología. Se puede recuperar del objeto de diccionario que se pasó a los componentes cuando se inicializaron.

Para más información, vea la sección [ConfigurationManager](#configurationmanager) de este documento.

### <a name="systemtypeloadexception"></a>System.TypeLoadException

Al usar una topología de C# con un clúster de HDInsight basado en Linux, puede encontrar el siguiente error:

    System.TypeLoadException: Failure has occurred while loading a type.

Este error se produce cuando se usa un archivo binario que no es compatible con la versión de .NET que admite mono.

Para clústeres de HDInsight basados en Linux, debe asegurarse de que el proyecto usa archivos binarios compilados para .NET 4.5.

### <a name="test-a-topology-locally"></a>Prueba de una topología localmente

Aunque es fácil implementar una topología en un clúster, en algunos casos puede que deba probar localmente una topología. Siga los pasos que se muestran a continuación para ejecutar y probar localmente la topología de ejemplo de este tutorial en su entorno de desarrollo.

> [!WARNING]
> Las pruebas locales solo funcionan para topologías básicas de C#. No se pueden usar pruebas locales para las topologías híbridas o para las topologías que usan varias secuencias.

1. En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto y seleccione **Propiedades**. En las propiedades del proyecto, cambie el **tipo de salida** a **Aplicación de consola**.

    ![tipo de salida](./media/hdinsight-storm-develop-csharp-visual-studio-topology/outputtype.png)

   > [!NOTE]
   > No olvide cambiar el **tipo de salida** de nuevo a **Biblioteca de clases** antes de implementar la topología en un clúster.

2. En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto y seleccione **Agregar** > **Nuevo elemento**. Seleccione **Clase** y escriba **LocalTest.cs** como nombre de clase. Por último, haga clic en **Agregar**.

3. Abra **LocalTest.cs** y agregue la siguiente instrucción **using** en la parte superior:

    ```csharp
    using Microsoft.SCP;
    ```

4. Use el código siguiente como contenido de la clase **LocalTest**:

    ```csharp
    // Drives the topology components
    public void RunTestCase()
    {
        // An empty dictionary for use when creating components
        Dictionary<string, Object> emptyDictionary = new Dictionary<string, object>();

        #region Test the spout
        {
            Console.WriteLine("Starting spout");
            // LocalContext is a local-mode context that can be used to initialize
            // components in the development environment.
            LocalContext spoutCtx = LocalContext.Get();
            // Get a new instance of the spout, using the local context
            Spout sentences = Spout.Get(spoutCtx, emptyDictionary);

            // Emit 10 tuples
            for (int i = 0; i < 10; i++)
            {
                sentences.NextTuple(emptyDictionary);
            }
            // Use LocalContext to persist the data stream to file
            spoutCtx.WriteMsgQueueToFile("sentences.txt");
            Console.WriteLine("Spout finished");
        }
        #endregion

        #region Test the splitter bolt
        {
            Console.WriteLine("Starting splitter bolt");
            // LocalContext is a local-mode context that can be used to initialize
            // components in the development environment.
            LocalContext splitterCtx = LocalContext.Get();
            // Get a new instance of the bolt
            Splitter splitter = Splitter.Get(splitterCtx, emptyDictionary);

            // Set the data stream to the data created by the spout
            splitterCtx.ReadFromFileToMsgQueue("sentences.txt");
            // Get a batch of tuples from the stream
            List<SCPTuple> batch = splitterCtx.RecvFromMsgQueue();
            // Process each tuple in the batch
            foreach (SCPTuple tuple in batch)
            {
                splitter.Execute(tuple);
            }
            // Use LocalContext to persist the data stream to file
            splitterCtx.WriteMsgQueueToFile("splitter.txt");
            Console.WriteLine("Splitter bolt finished");
        }
        #endregion

        #region Test the counter bolt
        {
            Console.WriteLine("Starting counter bolt");
            // LocalContext is a local-mode context that can be used to initialize
            // components in the development environment.
            LocalContext counterCtx = LocalContext.Get();
            // Get a new instance of the bolt
            Counter counter = Counter.Get(counterCtx, emptyDictionary);

            // Set the data stream to the data created by splitter bolt
            counterCtx.ReadFromFileToMsgQueue("splitter.txt");
            // Get a batch of tuples from the stream
            List<SCPTuple> batch = counterCtx.RecvFromMsgQueue();
            // Process each tuple in the batch
            foreach (SCPTuple tuple in batch)
            {
                counter.Execute(tuple);
            }
            // Use LocalContext to persist the data stream to file
            counterCtx.WriteMsgQueueToFile("counter.txt");
            Console.WriteLine("Counter bolt finished");
        }
        #endregion
    }
    ```

    Dedique un momento para leer los comentarios del código. Este código usa **LocalContext** para ejecutar los componentes en el entorno de desarrollo y conserva la secuencia de datos entre componentes de los archivos de texto en la unidad local.

1. Abra **Program.cs** y agregue lo siguiente al método **Main**:

    ```csharp
    Console.WriteLine("Starting tests");
    System.Environment.SetEnvironmentVariable("microsoft.scp.logPrefix", "WordCount-LocalTest");
    // Initialize the runtime
    SCPRuntime.Initialize();

    //If we are not running under the local context, throw an error
    if (Context.pluginType != SCPPluginType.SCP_NET_LOCAL)
    {
        throw new Exception(string.Format("unexpected pluginType: {0}", Context.pluginType));
    }
    // Create test instance
    LocalTest tests = new LocalTest();
    // Run tests
    tests.RunTestCase();
    Console.WriteLine("Tests finished");
    Console.ReadKey();
    ```

2. Guarde los cambios y presione **F5** o seleccione **Depurar** > **Iniciar depuración** para iniciar el proyecto. Debe aparecer una ventana de consola y el estado del registro a medida que progresen las pruebas. Cuando se muestre **Pruebas finalizadas** , presione cualquier tecla para cerrar la ventana.

3. Use el **Explorador de Windows** para buscar el directorio que contiene el proyecto; por ejemplo, **C:\Usuarios\<su_nombre_de_usuario>\Documentos\Visual Studio 2013\Projects\WordCount\WordCount**. En este directorio, abra **Bin** y haga clic en **Depurar**. Debería ver los archivos de texto que se generaron cuando se ejecutaron las pruebas: sentences.txt, counter.txt y splitter.txt. Abra cada archivo de texto e inspeccione los datos.

   > [!NOTE]
   > Los datos de cadena se almacenan como una matriz de valores decimales en estos archivos. Por ejemplo, \[[97,103,111]] en el archivo **splitter.txt** es la palabra 'and'.

> [!NOTE]
> Asegúrese de volver a establecer el **tipo de proyecto** en **Biblioteca de clases** antes de implementarlo en un clúster de Storm en HDInsight.

### <a name="log-information"></a>Información del registro

Puede registrar fácilmente información de los componentes de la topología mediante `Context.Logger`. Por ejemplo, lo siguiente creará una entrada del registro informativo:

```csharp
Context.Logger.Info("Component started");
```

Se puede ver la información registrada desde el **registro del servicio Hadoop**, que se encuentra en el **Explorador de servidores**. Expanda la entrada para el clúster de Storm en HDInsight y, después, expanda **Registro del servicio Hadoop**. Por último, seleccione el archivo de registro que desea consultar.

> [!NOTE]
> Los registros se almacenan en la cuenta de almacenamiento de Azure que usa el clúster. Para ver los registros en Visual Studio, debe iniciar sesión en la suscripción de Azure a la que pertenece la cuenta de almacenamiento.

### <a name="view-error-information"></a>Visualización de la información del error

Para ver los errores que se han producido en una topología en ejecución, siga estos pasos:

1. En el **Explorador de servidores**, haga clic con el botón derecho en el clúster de Storm en HDInsight y seleccione **Ver topologías de Storm**.

2. Para los componentes **spout** y **bolt**, la columna **Último error** tendrá información sobre el último error que ha ocurrido.

3. Seleccione el **id. de spout** o el **id. de bolt** del componente del que se muestra un error. En la página de detalles que se muestra, se enumerará información adicional sobre el error en la sección **Errores** de la parte inferior de la página.

4. Para más información, seleccione un **puerto** en la sección **Executors** (Ejecutores) de la página para ver el registro de trabajo de Storm de los últimos minutos.

### <a name="errors-submitting-topologies"></a>Errores en el envío de topologías

Si se producen errores al enviar una topología a HDInsight, puede encontrar registros para los componentes de servidor que controlan el envío de la topología en su clúster de HDInsight. Para recuperar estos registros, utilice el siguiente comando desde una línea de comandos:

    scp sshuser@clustername-ssh.azurehdinsight.net:/var/log/hdinsight-scpwebapi/hdinsight-scpwebapi.out .

Reemplace __sshuser__ por la cuenta de usuario SSH del clúster. Reemplace __clustername__ por el nombre del clúster de HDInsight. Si usó una contraseña para la cuenta SSH, se le pedirá que la escriba. El comando descargará el archivo en el directorio desde el que se ejecutó el comando.

## <a name="next-steps"></a>Pasos siguientes

Para obtener un ejemplo de procesamiento de datos desde Event Hubs, vea [Procesamiento de eventos desde Azure Event Hub con Storm en HDInsight](hdinsight-storm-develop-csharp-event-hub-topology.md).

Para ver un ejemplo de una topología de C# que divide los datos de una secuencia en varias secuencias, consulte [Ejemplo de Storm en C#](https://github.com/Blackmist/csharp-storm-example).

Para obtener más información acerca de cómo crear topologías de C#, visite [SCP.NET GettingStarted.md](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/SCPNet-GettingStarted.md).

Para conocer más formas de trabajar con HDInsight y obtener más ejemplos de Storm en HDInsight, vea los siguientes documentos:

**Microsoft SCP.NET**

* [Guía de programación de SCP](hdinsight-storm-scp-programming-guide.md)

**Apache Storm en HDInsight**

* [Implementación y supervisión de topologías con Apache Storm en HDInsight](hdinsight-storm-deploy-monitor-topology.md)
* [Topologías de ejemplo para Storm en HDInsight](hdinsight-storm-example-topology.md)

**Apache Hadoop en HDInsight**

* [Uso de Hive con Hadoop en HDInsight](hdinsight-use-hive.md)
* [Uso de Pig con Hadoop en HDInsight](hdinsight-use-pig.md)
* [Uso de MapReduce con Hadoop en HDInsight](hdinsight-use-mapreduce.md)

**Apache HBase en HDInsight**

* [Introducción a HBase en HDInsight](hdinsight-hbase-tutorial-get-started.md)

