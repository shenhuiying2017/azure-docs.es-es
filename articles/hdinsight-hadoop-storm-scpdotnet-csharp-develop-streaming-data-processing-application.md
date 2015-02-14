<properties 
	pageTitle="Desarrollo de aplicaciones de procesamiento de datos de streaming con SCP.NET en Storm | Azure" 
	description="Aprenda a desarrollar aplicaciones de procesamiento de datos de streaming con SCP.NET y C# en Storm de HDInsight." 
	services="hdinsight" 
	documentationCenter="" 
	authors="weedqian" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/15/2014" 
	ms.author="qixia"/>

#Desarrollo de aplicaciones de procesamiento de datos de streaming en C# con Stream Computing Platform y Storm en HDInsight

Stream Computing Platform (SCP) es una plataforma para la creación de aplicaciones de procesamiento de datos en tiempo real, confiables, distribuidas, coherentes y de alto rendimiento mediante .NET. Creado a partir de [Apache Storm](http://storm.incubator.apache.org/), un sistema de procesamiento de secuencias de código abierto y en tiempo real que está disponible con HDInsight.

En este artículo, aprenderá lo siguiente:

* Qué es SCP y cómo funciona con Storm

* Cómo crear una solución de SCP

* Cómo probar una solución de SCP

* Cómo implementar una solución de SCP en un clúster de Storm en HDInsight

##Requisitos previos

* Una suscripción de Azure

* Un clúster de Storm en HDInsight

* Visual Studio 2010 o 2013

##Tabla de contenido

* [SCP y Storm](#scpandstorm)

##<a id="scpandstorm"></a>SCP y Storm

Apache Storm es un sistema de cálculo distribuido que se ejecuta en clústeres de Hadoop, y le permite realizar procesamiento de datos en tiempo real. Aunque Storm se ejecuta en la Máquina virtual Java (JVM), se ha diseñado para que se puedan implementar soluciones (conocidas como **topologías**) en diversos lenguajes de programación. Incluso puede crear una topología que sea una mezcla de componentes escritos en varios lenguajes.

SCP proporciona las bibliotecas que facilitan la creación de soluciones de Storm mediante .NET. Los clústeres de Storm en HDInsight incluyen los componentes de servidor necesarios para ejecutar las soluciones de SCP que crea.

Para obtener más información acerca de Storm en HDInsight, consulte [Introducción a Storm de HDInsight](http://azure.microsoft.com/es-es/documentation/articles/hdinsight-storm-overview/).

###Diseño de una solución de SCP

SCP proporciona interfaces que le permiten crear los siguientes componentes de Storm:

* Spout: consume datos de un origen (archivo, base de datos, API, etc.) y emite una o más secuencias de tuplas (una lista ordenada de elementos)
* Bolt: consume una o más secuencias y, de manera opcional, emite una o más secuencias
* Topología: define cómo fluyen los datos entre spouts y bolts, el paralelismo de los spouts y bolts y la información de configuración

> [AZURE.NOTE] Los spouts y bolts también implementan procesamiento genérico. Por ejemplo, un spout puede separar los mensajes entrantes en varias tuplas, o simplemente emitir una tupla y dejar que un bolt extraiga los valores que necesita. De igual forma, si necesita escribir datos en un almacén de datos, esto lo implementaría en un bolt.

En el diseño de una solución intervienen los siguientes aspectos:

* ¿Qué origen de datos va a consumir? Debe tener un spout que implemente esto.
* ¿Qué procesamiento debe tener lugar? Debe implementar esto en los spouts o bolts.
* Si el procesamiento se reparte entre varios bolts, ¿cuál es el flujo de datos entre ellos? Debe describir esto mediante la topología.
* ¿Cómo se distribuirá el procesamiento entre los nodos del clúster de HDInsight? Debe describir esto mediante la topología.

##Instalación del SDK de SCP

El SDK de SCP se proporciona en el clúster de Storm en HDInsight. Después de [crear un clúster de Storm en HDInsight](http://azure.microsoft.com/es-es/documentation/articles/hdinsight-storm-getting-started/), siga estos pasos para descargar el SDK en su entorno de desarrollo local.

1. Inicie sesión en el [Portal de administración de Azure](https://manage.windowsazure.com).

2. Haga clic en **HDINSIGHT** en el panel izquierdo. Aparecerá una lista de los clústeres de HDInsight implementados.

3. Haga clic en el clúster de HDInsight al que desea conectarse.

4. Desde la parte superior de la página, haga clic en **CONFIGURACIÓN**.

5. Desde la parte inferior de la página, haga clic en **HABILITAR DE FORMA REMOTA**.

6. En el asistente para Configurar Escritorio remoto, escriba un **nombre de usuario** y una **contraseña** para el escritorio remoto. Escriba una fecha de caducidad en el cuadro **CADUCA EL** y luego haga clic en el icono de comprobación.

7. Una vez habilitado Escritorio remoto, haga clic en **CONECTAR** en la parte inferior de la página y luego siga las instrucciones.

8. Después de conectarse al clúster a través de Escritorio remoto, abra el **Explorador de archivos** y escriba **%storm_home%\examples** en la barra de direcciones.

9. Haga clic con el botón secundario en la carpeta **SDK** y luego seleccione **Copiar**.

10. En el entorno de desarrollo local, abra el **Explorador de archivos** y busque la ubicación en la que desea almacenar el SDK. Haga clic con el botón secundario y seleccione **Pegar**.

##Creación de una solución de SCP

1. En Visual Studio, cree un nuevo proyecto para una **aplicación de consola**. Llame a esta solución **WordCount**.

2. En el **Explorador de soluciones**, haga clic con el botón secundario en **Referencias** y luego seleccione **Agregar referencia**.

3. Seleccione el botón **Examinar** en la parte inferior del **Administrador de referencias** y luego vaya a la carpeta SDK que descargó anteriormente. Seleccione **Microsoft.SCPNet.dll** y **Microsoft.SCPLogger.dll**y, a continuación, haga clic en **Agregar**. Haga clic en **Aceptar** para cerrar la ventana del Administrador de referencias.

###Creación del spout

1. En el **Explorador de soluciones**, haga clic con el botón secundario en **WordCount** y luego seleccione **Agregar | Nuevo elemento**. Seleccione **Clase** y escriba **WordSpout.cs** como nombre. Por último, haga clic en **Agregar**.

2. Abra el archivo **WordSpout.cs** y sustituya el código existente por el siguiente. Asegúrese de leer los comentarios para comprender cómo funciona este código.

        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Text;
        using System.Threading.Tasks;
        using Microsoft.SCP;
        using System.Threading;

        namespace WordCount
        {
            class WordSpout : ISCPSpout
            {
                //Context
                private Context ctx;

                private Random rand = new Random();
                //The sentences to emit
                string[] sentences = new string[] {
                                                  "the cow jumped over the moon",
                                                  "an apple a day keeps the doctor away",
                                                  "four score and seven years ago",
                                                  "snow white and the seven dwarfs",
                                                  "i am at two with nature"};

                //Constructor
                public WordSpout(Context ctx)
                {
                    //Log that we are starting
                    Context.Logger.Info("WordSpout constructor called");
                    //Store the context that was passed
                    this.ctx = ctx;

                    //Define the schema for the emitted tuples
                    Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
                    //In this case, just a string tuple
                    outputSchema.Add("default", new List<Type>() { typeof(string) });
                    //Declare the schema for the stream
                    this.ctx.DeclareComponentSchema(new ComponentStreamSchema(null, outputSchema));
                }

                //Return a new instance of the spout
                public static WordSpout Get(Context ctx, Dictionary<string, Object> parms)
                {
                    return new WordSpout(ctx);
                }

                //Emit the next tuple
                //NOTE: When using data from an external data source
                //such as Service Bus, Event Hub, Twitter, etc.,
                //you would read and emit it in NextTuple
                public void NextTuple(Dictionary<string, object> parms)
                {
                    Context.Logger.Info("NextTuple enter");

                    //Get a random sentence
                    string sentence = sentences[rand.Next(0, sentences.Length - 1)];
                    Context.Logger.Info("Emit: {0}", sentence);
                    //Emit the sentence
                    this.ctx.Emit(new Values(sentence));

                    Context.Logger.Info("NextTuple exit");
                }

                //Ack's are not implemented
                public void Ack(long seqId, Dictionary<string, object> parms)
                {
                    throw new NotImplementedException();
                }

                //Ack's are not implemented, so
                //fail should never be called
                public void Fail(long seqId, Dictionary<string, object> parms)
                {
                    throw new NotImplementedException();
                }
            }
        }

###Creación de pruebas

1. En el **Explorador de soluciones**, haga clic con el botón secundario en **WordCount** y luego seleccione **Agregar | Nuevo elemento**. Seleccione **Clase** y escriba **LocalTest.cs** como nombre. Por último, haga clic en **Agregar**.

2. Abra el archivo **LocalTest.cs** y sustituya el código existente por el siguiente. El código usado para probar el spout es muy parecido al código que se usará para probar los bolts.

        using Microsoft.SCP;
        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Text;
        using System.Threading.Tasks;

        namespace WordCount
        {
            class LocalTest
            {
                //Run tests
                public void RunTestCase()
                {
                    Dictionary<string, Object> emptyDictionary = new Dictionary<string, object>();
                    //Spout tests
                    {
                        //Get local context
                        LocalContext spoutCtx = LocalContext.Get();
                        //Get an instance of the spout
                        WordSpout spout = WordSpout.Get(spoutCtx, emptyDictionary);
                        //Call NextTuple to emit data
                        for (int i = 0; i < 10; i++)
                        {
                            spout.NextTuple(emptyDictionary);
                        }
                        //Store the stream for the next component
                        spoutCtx.WriteMsgQueueToFile("spout.txt");
                    }
                }
            }
        }

3. Abra el archivo **Program.cs** y sustituya el código existente por el siguiente.

        using Microsoft.SCP;
        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Text;
        using System.Threading.Tasks;

        namespace WordCount
        {
            class Program
            {
                static void Main(string[] args)
                {
                    if (args.Count() > 0)
                    {
                        //Code to run on HDInsight cluster will go here
                    }
                    else
                    {
                        //Set log prefix information for the component being tested
                        System.Environment.SetEnvironmentVariable("microsoft.scp.logPrefix", "WordCount-LocalTest");
                        //Initialize the runtime
                        SCPRuntime.Initialize();

                        //If we are not running under the local context, throw an error
                        if (Context.pluginType != SCPPluginType.SCP_NET_LOCAL)
                        {
                            throw new Exception(string.Format("unexpected pluginType: {0}", Context.pluginType));
                        }
                        //Create an instance of LocalTest
                        LocalTest localTest = new LocalTest();
                        //Run the tests
                        localTest.RunTestCase();
                    }
                }
            }
        }

3. Ejecute la aplicación. A continuación, mire en el directorio **WordCount\bin\debug** bajo el proyecto de Visual Studio. Debe haber un archivo llamado **spout.txt**, que contendrá los datos emitidos por el spout durante esta prueba. El contenido debe ser similar al siguiente texto.

        {"__isset":{"streamId":true,"tupleId":true,"evt":true,"data":true},"StreamId":"default","TupleId":"","Evt":1000,"Data":[[97,110,32,97,112,112,108,101,32,97,32,100,97,121,32,107,101,101,112,115,32,116,104,101,32,100,111,99,116,111,114,32,97,119,97,121]]}
        {"__isset":{"streamId":true,"tupleId":true,"evt":true,"data":true},"StreamId":"default","TupleId":"","Evt":1000,"Data":[[116,104,101,32,99,111,119,32,106,117,109,112,101,100,32,111,118,101,114,32,116,104,101,32,109,111,111,110]]}

    > [AZURE.NOTE] En las líneas anteriores, "Data" es la cadena emitida por el spout, pero almacenada como una matriz de bytes. Por ejemplo, `[[97,110,32,97,112,112,108,101,32,97,32,100,97,121,32,107,101,101,112,115,32,116,104,101,32,100,111,99,116,111,114,32,97,119,97,121]]` es "Una manzana al día del médico te libraría".

###Creación de los bolts

1. En el **Explorador de soluciones**, haga clic con el botón secundario en **WordCount** y luego seleccione **Agregar | Nuevo elemento**. Seleccione **Clase** y escriba **SplitterBolt.cs** como nombre. Por último, haga clic en **Agregar**. Repita este paso para agregar una clase llamada **CounterBolt.cs**.

2. Abra el archivo **SplitterBolt.cs** y sustituya el código existente por el siguiente.

        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Text;
        using System.Threading.Tasks;
        using Microsoft.SCP;
        using System.Threading;

        namespace WordCount
        {
            class SplitterBolt : ISCPBolt
            {
                //Context
                private Context ctx;

                //Constructor
                public SplitterBolt(Context ctx)
                {
                    Context.Logger.Info("Splitter constructor called");
                    //Set context
                    this.ctx = ctx;
                    //Define the schema for the incoming tuples from spout
                    Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
                    //In this case, just a string tuple
                    inputSchema.Add("default", new List<Type>() {typeof (string)});
                    //Define the schema for tuples to be emitted from this bolt
                    Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
                    //We are also only emitting a string tuple
                    outputSchema.Add("default", new List<Type>() {typeof (string)});
                    //Declare both incoming and outbound schemas
                    this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, outputSchema));
                }

                //Get a new instance
                public static SplitterBolt Get(Context ctx, Dictionary<string, Object> parms)
                {
                    return new SplitterBolt(ctx);
                }

                //Process a tuple from the stream
                public void Execute(SCPTuple tuple)
                {
                    Context.Logger.Info("Execute enter");
                    //Get the incomin tuple value
                    string sentence = tuple.GetString(0);
                    //Split it
                    foreach (string word in sentence.Split(' '))
                    {
                        //Emit each word to the outbound stream
                        Context.Logger.Info("Emit: {0}", word);
                        this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new List<SCPTuple> { tuple }, new Values(word));
                    }
                    Context.Logger.Info("Execute exit");
                }
            }
        }

4. Abra el archivo **CounterBolt.cs** y sustituya el código existente por el siguiente.

        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Text;
        using System.Threading.Tasks;
        using Microsoft.SCP;

        namespace WordCount
        {
            class CounterBolt : ISCPBolt
            {
                //Context
                private Context ctx;

                //Store each word and a count of how many times it has
                //been emitted by the splitter
                private Dictionary<string, int> counts = new Dictionary<string, int>();

                //Constructor
                public CounterBolt(Context ctx)
                {
                    Context.Logger.Info("Counter constructor called");

                    //Set context
                    this.ctx = ctx;
                    //Define the schema for the incoming tuples from spout
                    Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
                    //Just a string, which will contain a word from the splitter
                    inputSchema.Add("default", new List<Type>() { typeof(string) });
                    //Define the schema for tuples to be emitted from this bolt
                    Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
                    //In this case, a string (containing a word) and an int (containing the count)
                    outputSchema.Add("default", new List<Type>() { typeof(string), typeof(int) });
                    this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, outputSchema));
                }

                //Get an instance
                public static CounterBolt Get(Context ctx, Dictionary<string, Object> parms)
                {
                    return new CounterBolt(ctx);
                }

                //Process a tuple from the stream
                public void Execute(SCPTuple tuple)
                {
                    Context.Logger.Info("Execute enter");
                    //Get the word that was emitted from the splitter
                    string word = tuple.GetString(0);
                    //Have we seen this word before?
                    int count = counts.ContainsKey(word) ? counts[word] : 0;
                    //Increment and store the count for this word
                    count++;
                    counts[word] = count;

                    Context.Logger.Info("Emit: {0}, count: {1}", word, count);
                    //Emit the word and the count
                    this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new List<SCPTuple> { tuple }, new Values(word, count));

                    Context.Logger.Info("Execute exit");
                }
            }
        }

    > [AZURE.NOTE] Este bolt emite una secuencia, que resulta útil para la prueba. En una solución del mundo real, almacenaría los datos en una base de datos, cola u otro almacén persistente al finalizar el procesamiento.

3. Abra **LocalTest.cs** y agregue las siguientes pruebas para SplitterBolt y CounterBolt tras el bloqueo de prueba del spout anterior.

        //SplitterBolt tests
        {
            LocalContext splitterCtx = LocalContext.Get();
            SplitterBolt splitter = SplitterBolt.Get(splitterCtx, emptyDictionary);
            //Read from the 'stream' emitted by the spout
            splitterCtx.ReadFromFileToMsgQueue("spout.txt");
            List<SCPTuple> batch = splitterCtx.RecvFromMsgQueue();
            foreach (SCPTuple tuple in batch)
            {
                splitter.Execute(tuple);
            }
            //Store the stream for the next component
            splitterCtx.WriteMsgQueueToFile("splitter.txt");
        }
        //CounterBolt tests
        {
            LocalContext counterCtx = LocalContext.Get();
            CounterBolt counter = CounterBolt.Get(counterCtx, emptyDictionary);
            //Read from the 'stream' emitted by the splitter
            counterCtx.ReadFromFileToMsgQueue("splitter.txt");
            List<SCPTuple> batch = counterCtx.RecvFromMsgQueue();
            foreach (SCPTuple tuple in batch)
            {
                counter.Execute(tuple);
            }
            //Store the stream for the next component
            counterCtx.WriteMsgQueueToFile("counter.txt");
        }

4. Ejecute la solución. Una vez finalizada, debería tener ahora los siguientes archivos en el directorio **WordCount\bin\debug**.

    * **spout.txt**, los datos emitidos por WordSpout
    * **splitter.txt**, los datos emitidos por SplitterBolt
    * **counter.txt**, los datos emitidos por CounterBolt

###Adición de código para ejecutar en un clúster

1. Abra **Program.cs** y reemplace el `//Code to run on HDInsight cluster will go here` línea por la siguiente, a continuación, vuelva a generar el proyecto

        //The component to run
        string compName = args[0];
        //Run the component
        if ("wordspout".Equals(compName))
        {
            //Set the prefix for logging
            System.Environment.SetEnvironmentVariable("microsoft.scp.logPrefix", "WordCount-Spout");
            //Initialize the runtime
            SCPRuntime.Initialize();
            //Run the plugin (WordSpout)
            SCPRuntime.LaunchPlugin(new newSCPPlugin(WordSpout.Get));
        }
        else if ("splitterbolt".Equals(compName))
        {
            System.Environment.SetEnvironmentVariable("microsoft.scp.logPrefix", "WordCount-Splitter");
            SCPRuntime.Initialize();
            SCPRuntime.LaunchPlugin(new newSCPPlugin(SplitterBolt.Get));
        }
        else if ("counterbolt".Equals(compName))
        {
            System.Environment.SetEnvironmentVariable("microsoft.scp.logPrefix", "WordCount-Counter");
            SCPRuntime.Initialize();
            SCPRuntime.LaunchPlugin(new newSCPPlugin(CounterBolt.Get));
        }
        else
        {
            throw new Exception(string.Format("unexpected compName: {0}", compName));
        }

4. Cree un nuevo archivo llamado **WordCount.spec** y utilice lo siguiente para el contenido. Esto define la topología: los componentes, cuántas instancias se crearán entre los nodos del clúster y cómo fluyen los datos entre ellos. Para escribir esto se usa el [Lenguaje de especificación de la topología](#spec).

        {
          :name "WordCount"
          :topology
            (nontx-topolopy
              "WordCount"

              {
                "spout"

                (spout-spec
                  (scp-spout
                    {
                      "plugin.name" "WordCount.exe"
                      "plugin.args" ["wordspout"]
                      "output.schema" {"default" ["sentence"]}
                    })

                  :p 1)
              }

              {
                "splitter"

                (bolt-spec
                  {
                    "spout" :shuffle
                  }

                  (scp-bolt
                    {
                      "plugin.name" "WordCount.exe"
                      "plugin.args" ["splitterbolt"]
                      "output.schema" {"default" ["word"]}
                    })

                  :p 1)

                "counter"

                (bolt-spec
                  {
                    "splitter" :global
                  }

                  (scp-bolt
                    {
                      "plugin.name" "WordCount.exe"
                      "plugin.args" ["counterbolt"]
                      "output.schema" {"default" ["word" "count"]}
                    })

                  :p 1)
              })

          :config
            {
              "topology.kryo.register" ["[B"]
            }
        }

4. Conéctese al clúster de Storm de HDInsight mediante Escritorio remoto y copie la carpeta **bin\debug** de su proyecto WordCountlocal en dicho clúster. Por ejemplo, cópiela en la carpeta **%storm_home%\examples** y cambie su nombre por **WordCount**.

3. Copie también **WordCount.spec** en el servidor de HDInsight. Colóquelo en el directorio **%storm_home%\examples**.

4. En el clúster de Storm, use el icono de **línea de comandos de Storm** que hay en el escritorio para abrir una línea de comandos y use luego el siguiente comando para iniciar la topología de WordCount.

        bin\runspec examples\WordCount.spec temp examples\WordCount

    De esta forma se creará una nueva carpeta llamada **temp**, y se usará la especificación y los archivos de la solución WordCount para crear un archivo **WordCount.jar**, que luego se envía a Storm.

5. Cuando finalice el comando, abra la **UI de Storm** desde el escritorio. Debe mostrarse la topología de **WordCount**. Puede seleccionar la topología de la **UI de Storm** para ver las estadísticas.

6. Para detener la topología, en la **UI de Storm**, seleccione **WordCount** y luego seleccione **Kill** (Eliminar) en **Topology actions** (Acciones de topología).

##Resumen

En los pasos anteriores, ha aprendido a crear, probar e implementar una topología básica de recuento de palabras creada con Stream Computing Platform. Para ver más ejemplos, consulte el directorio **%storm_home%\examples** en el clúster de HDInsight. Para obtener información detallada sobre SCP, consulte la siguiente referencia.

##Referencia de SCP

###Interfaz de complemento de SCP

Las aplicaciones SCP (conocidas como complementos) se conectan a la canalización de Storm. Un complemento es una aplicación de consola .NET que implementa una o más de las siguientes interfaces.  

-	**ISCPSpout**: úselo cuando implemente un spout no transaccional
- 	**ISCPTxSpout**: úselo cuando implemente un spout no transaccional
-	**ISCPBolt**: úselo cuando implemente un bolt no transaccional
-	**ISCPBatchBolt**: úselo cuando implemente un bolt transaccional

####ISCPPlugin

ISCPPlugin es la interfaz base de la que heredan todas las demás interfaces de SCP. Actualmente es una interfaz ficticia.  

    public interface ISCPPlugin
    {
    }  

####ISCPSpout

ISCPSpout es la interfaz de un spout no transaccional.  

    public interface ISCPSpout : ISCPPlugin
    {
        void NextTuple(Dictionary<string, Object> parms);
        void Ack(long seqId, Dictionary<string, Object> parms);
        void Fail(long seqId, Dictionary<string, Object> parms);
    }  

Cuando se llama a `NextTuple()`, el código puede emitir una o más tuplas. Si no hay nada que emitir, este método se debe devolver sin emitir nada.

`Ack()` y `Fail()` solo se llamarán cuando la confirmación esté habilitada en el archivo de especificación. Se usa `seqId` para identificar la tupla que se confirma o incluye errores. Si la confirmación se habilita en una topología no transaccional, se debe usar la siguiente función de emisión en el spout:  

    public abstract void Emit(string streamId, List<object> values, long seqId);  

Si la confirmación no se admite en la topología no transaccional, `Ack()` y `Fail()` pueden dejarse como funciones vacías.  

El parámetro `parms` de estas funciones es un objeto de diccionario vacío y está reservado para uso futuro.  

> [AZURE.NOTE] `NextTuple()`, `Ack()`, y `Fail()` se llaman en un bucle ajustado en un único subproceso. Si no hay tuplas para emitir, podría usar `sleep` durante un corto espacio de tiempo (por ejemplo, 10 milisegundos) para conservar los ciclos de CPU.

####ISCPTxSpout

ISCPTxSpout es la interfaz de un spout transaccional.  

    public interface ISCPTxSpout : ISCPPlugin
    {
        void NextTx(out long seqId, Dictionary<string, Object> parms);
        void Ack(long seqId, Dictionary<string, Object> parms);
        void Fail(long seqId, Dictionary<string, Object> parms);
    }  

`NextTx()` se llama para iniciar una nueva transacción. El `seqId` se utiliza para identificar la transacción, que también se utiliza en `Ack()` y `Fail()`. Los datos emitidos desde `NextTx()` se almacenarán en ZooKeeper para permitir la reproducción. Como la capacidad de almacenamiento de ZooKeeper es muy limitada, solo debe emitir metadatos, no datos masivos en un spout transaccional.  

Storm reproducirá automáticamente la transacción en caso de error, por lo que no se debe llamar a Fail() en condiciones normales. Pero, si SCP puede comprobar los datos emitidos por el spout transaccional, puede llamar a Fail() cuando los metadatos no son válidos.

El parámetro `parms` de estas funciones es un objeto de diccionario vacío y está reservado para uso futuro.

> [AZURE.NOTE] `NextTx()`, `Ack()`, y `Fail()` se llaman en un bucle ajustado en un único subproceso. Si no hay tuplas para emitir, podría usar `sleep` durante un corto espacio de tiempo (por ejemplo, 10 milisegundos) para conservar los ciclos de CPU.

####ISCPBolt

ISCPBolt es la interfaz de un bolt no transaccional.  

    public interface ISCPBolt : ISCPPlugin
    {
        void Execute(SCPTuple tuple);
    }

Cuando hay una nueva tupla disponible, se llamará a la función `Execute()` para procesarla.

####ISCPBatchBolt

ISCPBatchBolt es la interfaz del bolt transaccional.  

    public interface ISCPBatchBolt : ISCPPlugin
    {
        void Execute(SCPTuple tuple);
        void FinishBatch(Dictionary<string, Object> parms);
    }  

Cuando hay una nueva tupla disponible, se llamará a la función `Execute()` para procesarla. `FinishBatch()` se llamará cuando haya finalizado esta transacción.

El parámetro `parms` de estas funciones es un objeto de diccionario vacío y está reservado para uso futuro.

> [AZURE.NOTE] Los bolts que implementan `ISCPBatchBolt` pueden obtener `StormTxAttempt` desde `parms`. `StormTxAttempt` se puede usar para determinar si una tupla es original o un intento de reproducción. Esto suele realizarse en el bolt de confirmación y se muestra en el ejemplo de **HelloWorldTx**.  

###Modelo de objetos

SCP.NET también proporciona un conjunto sencillo de objetos clave para que los desarrolladores programen con ellos.

* `Context`: proporciona información sobre el entorno de ejecución de la aplicación

* `StateStore`: proporciona servicios de metadatos, generación de secuencias monotónicas y coordinación sin espera. En `StateStore`, se pueden compilar abstracciones de simultaneidad distribuidas de nivel más alto, como bloqueos distribuidos, colas distribuidas, barreras y servicios de transacciones.

* `SCPRuntime`: inicializa el entorno de tiempo de ejecución e inicia los complementos cuando se ejecuta una solución en Storm

* `LocalContext`: proporciona métodos para serializar y deserializar las tuplas emitidas a los archivos locales cuando se prueba una aplicación localmente en Visual Studio

####Context

Cada instancia de ISCPPlugin (ISCPSpout/ISCPBolt/ISCPTxSpout/ISCPBatchBolt) tiene su correspondiente instancia de Context. La funcionalidad suministrada por Context se puede dividir en dos partes:

* **Estática**: disponible en todo el proceso de C#
* **Dinámica**: disponible para la instancia específica  

**Contexto estático**

* `public static ILogger Logger = null;`: se proporciona con fines de registro.  

* `public static SCPPluginType pluginType;`: obtiene el tipo de complemento del proceso de C#. Si el proceso de C# se ejecuta en modo de prueba local (sin Java), el tipo de complemento es "SCP_NET_LOCAL".

        public enum SCPPluginType
        {
            SCP_NET_LOCAL = 0,
            SCP_NET_SPOUT = 1,
            SCP_NET_BOLT = 2,
            SCP_NET_TX_SPOUT = 3,
            SCP_NET_BATCH_BOLT = 4
        }  

* `public static Config Config { get; set; }`: obtiene los parámetros de configuración de JVM. Los parámetros se pasan desde JVM cuando se inicializa el complemento. `Config` contiene dos diccionarios.

    * `public Dictionary<string, Object> stormConf { get; set; }`: contiene los parámetros definidos por Storm.

    * `public Dictionary<string, Object> pluginConf { get; set; }`: contiene los parámetros definidos por SCP.

* `public static TopologyContext TopologyContext { get; set; } ` : obtiene el contexto de la topología. Es más útil para componentes con paralelismo múltiple. A continuación se demuestra cómo acceder al contexto de la topología:

        if (Context.pluginType != SCPPluginType.SCP_NET_LOCAL)
        {
            Context.Logger.Info("TopologyContext info:");
            TopologyContext topologyContext = Context.TopologyContext;
            Context.Logger.Info("taskId: {0}", topologyContext.GetThisTaskId());
            taskIndex = topologyContext.GetThisTaskIndex();
            Context.Logger.Info("taskIndex: {0}", taskIndex);
            string componentId = topologyContext.GetThisComponentId();
            Context.Logger.Info("componentId: {0}", componentId);
            List<int> componentTasks = topologyContext.GetComponentTasks(componentId);
            Context.Logger.Info("taskNum: {0}", componentTasks.Count);
        }

**Contexto dinámico**

* `public void DeclareComponentSchema(ComponentStreamSchema schema);`: declara el esquema de salida y de entrada para las secuencias. En el ejemplo siguiente se declara un esquema de entrada que consta de una única tupla de cadena y un esquema de salida que consta de una tupla de cadena y una tupla de entero.

        this.ctx = context;
        Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
        inputSchema.Add("default", new List<Type>() { typeof(string) });
        outputSchema.Add("default", new List<Type>() { typeof(string), typeof(int) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, outputSchema));

* `public abstract void Emit(List<object> values);`: emite una o más tuplas en la secuencia predeterminada. En el ejemplo siguiente se emiten dos tuplas a la secuencia predeterminada:

        this.ctx.Emit(new Values(word, count));

* `public abstract void Emit(string streamId, List<object> values);`: emite una o más tuplas en la secuencia especificada. En el ejemplo siguiente se emiten dos tuplas a la secuencia llamada 'mystream'.

        this.ctx.Emit("mystream", new Values(word, count));

Cuando se usen spouts y bolts no transaccionales que tienen habilitada la confirmación, utilice lo siguiente.

* `public abstract void Emit(string streamId, List<object> values, long seqId);`: emite una o varias tuplas y un identificador de secuencia **desde un spout** en la secuencia especificada. En el ejemplo siguiente se emite una tupla y un identificador de secuencia a la secuencia predeterminada:

        this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new Values(word), lastSeqId);

* `public abstract void Emit(string streamId, IEnumerable<SCPTuple> anchors, List<object> values);`: emite una o varias tuplas y un identificador de secuencia **desde un bolt** en la secuencia especificada. Las tuplas emitidas están ancladas a las tuplas entrantes especificadas como `anchors`. Esto permite que las confirmaciones fluyan hacia la canalización siguiendo la cadena de tuplas entrantes y salientes. En el ejemplo siguiente se emite una tupla y un identificador de secuencia a la secuencia predeterminada, y se anclan las tuplas emitidas contra las tuplas entrantes contenidas en `tuple`

        this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new List<SCPTuple> { tuple }, new Values(word));

* `public abstract void Ack(SCPTuple tuple);`: confirma una tupla. Invoca la función `ISCPSpout.Ack` del spout que produjo originalmente la tupla.

* `public abstract void Fail(SCPTuple tuple);`: se produce un error de una tupla. Invoca la función `ISCPSpout.Fail` del spout que produjo originalmente la tupla.

####StateStore

Las aplicaciones de SCP pueden usar el objeto `State` para conservar información en ZooKeeper, sobre todo en el caso de una topología transaccional. Esto permite que los spouts transaccionales recuperen el estado de ZooKeeper y reinicien la canalización en caso de bloqueo.  

El objeto `StateStore` proporciona los métodos siguientes.

* `public static StateStore Get(string storePath, string connStr);`: obtiene un `StateStore` para la cadena de conexión y la ruta de acceso especificada.

* `public State Create();`: crea un nuevo objeto `State` en esta instancia de almacén de estado.

* `public IEnumerable<State> GetUnCommitted();`: obtiene todos los objetos `State` que están sin confirmar, excepto los estados anulados.

* `public IEnumerable<State> States();`: obtiene todos objetos 'State' en el `StateStore`.


* `public T Get<T>(string info = null);`: obtiene un objeto `State` o `Registry`.

    * `info`: el nombre de `Registry` que se va a obtener. Solo se utiliza al recuperar un objeto `Registry`.

    * `T`: tipo de `State` o `Registry`

* `public IEnumerable<Registry> Commited();`: obtiene los objetos `Registry` que contienen un `State` confirmado.

* `public IEnumerable<Registry> Aborted();`: obtiene los objetos `Registry` que contienen un `State` anulado.

* `public State GetState(long stateId)`: obtiene el objeto `State` para el identificador de estado especificado

El objeto **State** proporciona los siguientes métodos.

* `public void Commit(bool simpleMode = true);`: establece el estado del objeto `State` para confirmar.

    > [AZURE.NOTE] Cuando `simpleMode` está establecido en true, se eliminará simplemente el ZNode correspondiente en ZooKeeper. De lo contrario, se eliminará el ZNode actual y se agregará un nuevo nodo en `COMMITTED_PATH`.

* `public void Abort();`: establece el estado del objeto `State` para anular.

* `public void PutAttribute<T>(string key, T attribute);`: establece un valor de atributo para la clave proporcionada.

    * `key`: la clave para la que se establece el atributo.

    * `attribute`: el valor de atributo.

* `public T GetAttribute<T>(string key);`: obtiene un valor de atributo para la clave proporcionada.

####SCPRuntime

SCPRuntime proporciona los métodos siguientes.

* `public static void Initialize();`: inicializa el entorno de tiempo de ejecución de SCP. Cuando se llama, el proceso de C# se conectará a la JVM y obtendrá los parámetros de configuración y el contexto de la topología.

* `public static void LaunchPlugin(newSCPPlugin pluginDelegate);`: inicia el bucle de procesamiento de mensajes. En este bucle, el complemento de C# recibirá mensajes de la JVM (incluidas las tuplas y las señales de control) y luego procesará los mensajes

    * `pluginDelegate`: un delegado puede devolver un objeto que implementa ISCPSpout/IScpBolt/ISCPTxSpout/ISCPBatchBolt.

####LocalContext


* `List<SCPTuple> RecvFromMsgQueue();`: recibe tuplas de la cola.

* `void WriteMsgQueueToFile(string filepath, bool append = false);`: conserva las tuplas en el archivo.

* `void ReadFromFileToMsgQueue(string filepath);`: lee las tuplas del archivo.


###<a id="spec"></a>Lenguaje de especificación de topologías

La especificación de topologías de SCP es un lenguaje específico de dominios para la descripción y configuración de topologías de SCP. Se basa en [Clojure DSL](http://storm.incubator.apache.org/documentation/Clojure-DSL.html)de Storm.  

Use lo siguiente para definir una topología.

|Nuevas funciones|	Parámetros|	Descripción
|-------------|-----------|-----------
|**tx-topolopy**|	topology-name<br> spout-map<br> bolt-map|	 Permite definir una topología transaccional con el nombre de la topología, el mapa de definición de spouts y el mapa de definición de bolts.
|**scp-tx-spout**|	exec-name<br> args<br> fields|	Permite definir un spout transaccional. La aplicación se ejecutará con ***exec-name*** mediante ***args***.<br><br>***fields*** son los campos de salida del spout.
|**scp-tx-batch-bolt**|	exec-name<br> args<br> fields| 	Permite definir un bolt de lote. La aplicación se ejecutará con ***exec-name*** mediante ***args***.<br><br>Fields son los campos de salida del bolt.
|**scp-tx-commit-bolt**|	exec-name<br>args<br>fields|	Permite definir un bolt de autor. La aplicación se ejecutará con ***exec-name*** mediante args.<br><br>***fields*** son los campos de salida del bolt.
|**nontx-topolopy**|	topology-name<br> spout-map<br>bolt-map|	Define una topología no transaccional con el nombre de la topología,  el mapa de definición de spouts y el mapa de definición de bolts.
|**scp-spout**|	exec-name<br>args<br>fields<br>parameters|	Permite definir un spout no transaccional. La aplicación se ejecutará con ***exec-name*** mediante ***args***.<br><br>***fields*** son los campos de salida del spout.<br><br>***parameters*** es opcional, y se utiliza para especificar algunos parámetros como "nontransactional.ack.enabled".
|**scp-bolt**|	exec-name<br>args<br>fields<br>parameters|	Permite definir un bolt no transaccional. La aplicación se ejecutará con ***exec-name*** mediante ***args***.<br><br>***fields*** son los campos de salida del bolt.<br><br>***parameters*** es opcional, y se utiliza para especificar algunos parámetros como "nontransactional.ack.enabled".

Las siguientes son palabras clave que se pueden usar al definir una topología

|Palabras clave|	Descripción
|---------|------------
|**:name**|	Permite definir el nombre de la topología
|**:topology**|	Permite definir la topología con las funciones anteriores e incorporar otras.
|**:p**|	Permite definir la sugerencia de paralelismo para cada spout o bolt.
|**:config**|	Permite definir un parámetro de configuración o actualizar los existentes.
|**:schema**|	Permite definir el esquema de la secuencia.

Parámetros usados con frecuencia

|Parámetro|	Descripción
|---------|------------
|**"plugin.name"**|	Nombre del archivo exe del complemento de C#
|**"plugin.args"**|	Argumentos del complemento
|**"output.schema"**|	Esquema de salida
|**"nontransactional.ack.enabled"**|	Indica si se ha habilitado confirmación para una topología no transaccional

Las especificaciones de la topología se pueden enviar directamente al clúster de Storm para su ejecución a través del comando ***runspec***, que está ubicado en el directorio **%storm_home%\bin** en los clústeres de Storm.  

    usage: runSpec [spec-file target-dir [resource-dir] [-cp classpath]]
     ex: runSpec examples\HelloWorld\HelloWorld.spec target examples\HelloWorld\Target

> [AZURE.NOTE] El parámetro ***resource-dir*** es opcional, es necesario especificarlo cuando se desea ejecutar una aplicación de C#. Este directorio contendrá la aplicación, las dependencias y las configuraciones.  

El parámetro ***classpath*** también es opcional. Se usa para especificar el classpath de Java si el archivo de especificación contiene un spout o un bolt de Java.  

###Características varias

####Declaración de esquema de entrada y salida

Cuando se llama a `Emit()`, la plataforma serializa la tupla en una matriz de bytes y la transfiere a la JVM. Storm transfiere luego esta tupla a los destinos. Los bolts reciben entonces la tupla. Para los bolts de C#, la tupla se recibe desde la JVM y se convierte de nuevo al tipo original.

Para permitir esta serialización y deserialización, debe declarar el esquema de las entradas y salidas. Estas se definen como objetos `Dictionary<string, List<Type>`, donde la clave es el ID de secuencia y el valor es los `Types` de las tuplas que se emitirán. El componente puede declarar varias secuencias.

    public class ComponentStreamSchema
    {
        public Dictionary<string, List<Type>> InputStreamSchema { get; set; }
        public Dictionary<string, List<Type>> OutputStreamSchema { get; set; }
        public ComponentStreamSchema(Dictionary<string, List<Type>> input, Dictionary<string, List<Type>> output)
        {
            InputStreamSchema = input;
            OutputStreamSchema = output;
        }
    }

El objeto `Context` proporciona `DeclareComponentSchema`, que se puede usar para declarar el esquema de serialización/deserialización.

     public void DeclareComponentSchema(ComponentStreamSchema schema)

> [AZURE.NOTE] Debe asegurarse de que las tuplas emitidas obedezcan el esquema definido para esa secuencia o recibirá una excepción en tiempo de ejecución.  

####Compatibilidad de multisecuencias

Puede emitir a varias secuencias realizando varias llamadas a `Emit()`, mediante la especificación del parámetro `streamId` para cada secuencia en la que se va a escribir.

> [AZURE.NOTE] La emisión a una secuencia inexistente provocará excepciones en tiempo de ejecución.

####Agrupación de campos

La agrupación de campos integrada de Strom no funciona correctamente en SCP.NET. Al transmitir los datos mediante proxy a la JVM, todos los tipos de datos de campos son en realidad `byte[]`, y la agrupación de campos usa el código hash del objeto  `byte[]` para realizar la agrupación. El código hash del objeto `byte[]` es la dirección de este objeto en memoria, así que la agrupación será incorrecta para dos objetos `byte[]` que comparten el mismo contenido pero no la misma dirección.

Para solucionar este problema, use `scp-field-group` en su especificación. De esta manera se usará el contenido del `byte[]` para realizar la agrupación. El siguiente es un ejemplo de cómo usar esto en una especificación.  

    (bolt-spec
        {
            "spout_test""" (scp-field-group :non-tx [0,1])
        }
        ...
    )  

Este ejemplo hace lo siguiente.

* `scp-field-group`: usa la agrupación personalizada.  
* `:tx` o `:non-tx`: indica si es transaccional o no transaccional.
* `[0,1]`: usa un hashset de identificador de campo, partiendo de 0.  

####Topologías híbridas

La mayoría de los spouts, los bolts y las topologías nativos de Storm se implementan en Java. Para admitir la reutilización de estos componentes en soluciones que usan componentes de C#, SCP le permite crear y definir topologías híbridas en la especificación.

* **Spout o bolt de Java **: en el archivo de especificación,  `scp-spout` y  `scp-bolt` se pueden usar también para especificar spouts y bolts de Java. A continuación se demuestra la especificación de un spout de Java con un nombre de clase de `microsoft.scp.example.HybridTopology.Generator`

        (spout-spec
          (microsoft.scp.example.HybridTopology.Generator.)
          :p 1)

* **Classpath de Java**: al usar spouts o bolts de Java, primero debe compilar el spout o el bolt en un archivo JAR. Luego, agregue la classpath de Java con el parámetro `-cp` al usar el comando **runSpec**. El siguiente ejemplo incluye los archivos JAR ubicados en el directorio **examples\HybridTopology\java\target**.  

        bin\runSpec.cmd examples\HybridTopology\HybridTopology.spec specs examples\HybridTopology\net\Target -cp examples\HybridTopology\java\target\*  


* **Serialización y deserialización entre Java y C#**: para serializar objetos entre Java y C#, use `CustomizedInteropJSONSerializer`.

    > [AZURE.NOTE] Actualmente `CustomizedInteropJSONSerializer` solo admite **spouts de Java** y **bolts de C#**.

    * Especifique el serializador para componentes de Java en el archivo de especificación.

            (scp-bolt
              {
                "plugin.name" "HybridTopology.exe"
                "plugin.args" ["displayer"]
                "output.schema" {}
                "customized.java.serializer" ["microsoft.scp.storm.multilang.CustomizedInteropJSONSerializer"]
              })  

    * Use el serializador en sus componentes de C#.

            Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
            inputSchema.Add("default", new List<Type>() { typeof(Person) });
            this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, null));
            this.ctx.DeclareCustomizedDeserializer(new CustomizedInteropJSONDeserializer());  

    La implementación predeterminada debe tratar la mayoría de los casos si el tipo de datos no es demasiado complejo.  En caso de que lo sea o que el rendimiento de la implementación predeterminada no cumpla sus requisitos, puede crear una implementación personalizada que se adapte a sus necesidades mediante las interfaces siguientes:

    **Interfaz de serialización en Java**  

            public interface ICustomizedInteropJavaSerializer {
                public void prepare(String[] args);
                public List<ByteBuffer> serialize(List<Object> objectList);
            }  

    **Interfaz de deserialización en C#**

            public interface ICustomizedInteropCSharpDeserializer
            {
                List<Object> Deserialize(List<byte[]> dataList, List<Type> targetTypes);
            }  

###Modo host de SCP

El modo de host le permite compilar su proyecto en una DLL y hospedarlo mediante **SCPHost.exe**. Esta es la manera recomendada de hospedar su solución en un escenario de producción. Cuando se usa el modo de host, el archivo de especificación mostrará `SCPHost.exe` como el complemento.

El siguiente es un ejemplo de un archivo de especificación que usa el modo de host en el ejemplo de HelloWorld.

    (scp-spout
      {
        "plugin.name" "SCPHost.exe"
        "plugin.args" ["HelloWorld.dll" "Scp.App.HelloWorld.Generator" "Get"]
        "output.schema" {"default" ["sentence"]}
      })

* **HelloWorld.dll**: el ensamblado que contiene los spouts y bolts
* **Scp.App.HelloWorld.Generator**: el nombre de clase de un spout contenido en **HelloWorld.dll**
* **Get**: el método para invocar la obtención de una instancia del spout


##Ejemplos de programación de SCP

Las siguientes aplicaciones de ejemplo escritas con SCP se pueden encontrar en el clúster de Storm de HDInsight en **%storm_home%\examples**.

* **HelloWorld**: HelloWorld es un ejemplo muy simple de SCP.Net, y es parecido aj ejemplo de recuento de palabras usado anteriormente en este artículo. Se usa una topología no transaccional, con un spout llamado **generator**, y dos bolts llamados **splitter** y **counter**. El spout **generator** generará aleatoriamente algunas oraciones y emitirá estas oraciones a **splitter**. El bolt **splitter** dividirá las oraciones en palabras y emitirá estas palabras al bolt **counter**. El bolt **counter** usa un diccionario para registrar el número de repeticiones de cada palabra.

    En este ejemplo, hay dos archivos spec, **HelloWorld.spec** y **HelloWorld_EnableAck.spec**. Al usar **HelloWorld_EnableAck.spec**, la muestra confirmará las tuplas que pasan por los bolts, sin embargo **splitter** se ha diseñado para errar algunos bolts de manera aleatoria a fin de demostrar la gestión de errores en topologías no transaccionales.

* **HelloWorldTx**: una muestra de cómo implementar una topología transaccional. Tiene un spout llamado **generator**, un bolt de lote llamado **partial-count** y un bolt de confirmación llamado **count-sum**. Hay también tres archivos txt ya creados para usar con esta topología: **DataSource0.txt**, **DataSource1.txt** y **DataSource2.txt**.

    En cada transacción, el spout elegirá aleatoriamente dos archivos y emitirá los nombres de estos dos archivos al bolt **partial-count**. El bolt obtendrá el nombre de archivo de la tupla recibida, luego abrirá el archivo y contará el número de palabras que hay en el mismo. Finalmente, emitirá las palabras y los números al bolt **count-sum**. El bolt **count-sum** resumirá el recuento total.  

    Para obtener la semántica "exactly once", el bolt **count-sum** debe determinar si va a procesar una transacción reproducida. En este ejemplo se incluye una variable de miembro estática:  

        public static long lastCommittedTxId = -1;  

    Cuando se crea una instancia del bolt, se obtendrá el intento de transacción (`txAttempt`,) de los parámetros de entrada:  

        public static CountSum Get(Context ctx, Dictionary<string, Object> parms)
        {
            /* for transactional topology, we can get txAttempt from the input parms */
            if (parms.ContainsKey(Constants.STORM_TX_ATTEMPT))
            {
                StormTxAttempt txAttempt = (StormTxAttempt)parms[Constants.STORM_TX_ATTEMPT];
                return new CountSum(ctx, txAttempt);
            }
            else
            {
                throw new Exception("null txAttempt");
            }
        }  

    Cuando se llame a `FinishBatch()`, se actualizará `lastCommittedTxId` si no se trata de una transacción reproducida.  

        public void FinishBatch(Dictionary<string, Object> parms)
        {
            /* judge whether it is a replayed transaction? */
            bool replay = (this.txAttempt.TxId <= lastCommittedTxId);

            if (!replay)
            {
                /* If it is not replayed, update the toalCount and lastCommittedTxId vaule */
                totalCount = totalCount + this.count;
                lastCommittedTxId = this.txAttempt.TxId;
            }
            ... ...
        }  

* **HybridTopology**: esta topología contiene un spout de Java y un bolt de C#. Usa la implementación predeterminada de serialización y deserialización que proporciona SCP. Consulte **%storm_home%examples\HybridTopology\HybridTopology.spec** para obtener más información y **SubmitTopology.bat** para saber cómo especificar la classpath de Java al enviar la topología.  


[1]: ./media/hdinsight-hadoop-storm-scpdotnet-csharp-develop-streaming-data-processing-application/hdinsight-hadoop-storm-scpdotnet-csharp-develop-streaming-data-processing-application-01.png
<!--HONumber=42-->
