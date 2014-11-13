<properties title="Desarrollo de aplicaciones de procesamiento de datos de streaming con SCP.NET y C# en Storm de HDInsight" pageTitle="Desarrollo de aplicaciones de procesamiento de datos de streaming con SCP.NET en Storm | Azure" description="Aprenda a desarrollar aplicaciones de procesamiento de datos de streaming con SCP.NET y C# en Storm de HDInsight." services="hdinsight" solutions="" documentationCenter="" authors="Qianlin Xia" videoId="" scriptId="" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/15/2014" ms.author="qixia" />

# Desarrollo de aplicaciones de procesamiento de datos de streaming con SCP.NET y C# en Storm de HDInsight

## Introducción a SCP

SCP es una plataforma para compilar aplicaciones de procesamiento de datos confiables, coherentes y de alto rendimiento en tiempo real. Se basa en Storm (http://storm.incubator.apache.org/), un sistema de procesamiento de secuencias diseñado por las comunidades de OSS. Storm ha sido diseñado por Nathan Marz con código abierto en Twitter. Aprovecha ZooKeeper (http://zookeeper.apache.org/), otro proyecto de Apache que permite una coordinación distribuida muy confiable y la administración de estados.

El proyecto SCP no solo trasladó Storm a Windows sino que también agregó las extensiones y la personalización del ecosistema Windows. Entre las extensiones se incluye la experiencia de desarrolladores de .NET y las bibliotecas; la personalización incluye implementación basada en Windows.

La extensión y la personalización se ha realizado de tal forma que no necesitamos bifurcar los proyectos de OSS y pudimos aprovechar los ecosistemas derivados basados en Storm.

## Modelo de procesamiento

Los datos de SCP se han modelado como secuencias continuas de tuplas. Normalmente, las tuplas se introducen en primer lugar en alguna consulta, luego se seleccionan y se transforman mediante la lógica de negocios hospedada en una topología Storm, para finalmente poder canalizar el resultado como tuplas a otro sistema SCP o se confirma en almacenes como un sistema de archivos distribuidos o bases de datos como SQL Server.

![][0]

En Storm, una topología de aplicación define un gráfico de cálculo. Cada nodo de la topología contiene lógica de procesamiento y los vínculos entre nodos indican del flujo de datos. Los nodos que inyectan los datos de entrada en la topología se denominan spouts, que pueden usarse para secuenciar los datos. Los datos de entrada pueden residir en registros de archivo, en una base de datos transaccional, un contador de rendimiento del sistema, etc. Los nodos con flujos de datos de entrada y de salida se denominan bolts, que realizan el filtrado de datos reales y las selecciones, así como la agregación y la incorporación.

SCP admite procesamiento de datos de tipo best-effort, at-least-once y exactly-once. En una aplicación de procesamiento distribuido de streaming, se pueden producir varios errores durante el procesamiento de datos como, por ejemplo, interrupción de la red, error de la máquina, error de código de usuario, etc. El procesamiento at-least-once garantiza que todos los datos se procesarán como mínimo una vez reproduciendo automáticamente los mismos datos cuando se produce el error. El procesamiento at-least-once es sencillo y confiable, y es muy adecuado para muchas aplicaciones. Sin embargo, cuando, por ejemplo, la aplicación requiere un recuento exacto, el procesamiento at-least-once es insuficiente ya que existe la posibilidad de que los mismos datos se reproduzcan en la topología de la aplicación. En este caso, el procesamiento exactly-once se ha diseñado para asegurar que el resultados sea correcto aunque los datos se puedan reproducir y procesar varias veces.

SCP permite a los desarrolladores de .NET desarrollar aplicaciones de proceso en tiempo real mientras que de forma encubierta se aprovecha Storm basado en JVM. .NET y JVM se comunican a través del socket local de TCP. Básicamente, cada spout/bolt es un par de procesos .Net/Java, donde la lógica del usuario se ejecuta en el proceso .Net como complemento.

Para compilar una aplicación de procesamiento de datos encima de SCP, se requieren varios pasos:

-   Diseño e implementación de los spouts para extraer datos de la cola.
-   Diseño e implementación de bolts para procesar los datos de entrada y guardado de los datos en almacenes externos como, por ejemplo, Base de datos.
-   Diseño de la topología y posterior envío y ejecución de la misma. La topología define vértices y los datos fluyen entre los vértices. SCP tomará la especificación de topología y la implementa en un clúster de Storm, en el que cada vértice ejecuta un nodo lógico. El programador de tareas de Storm se encargará de la conmutación por error y el escalado.

En este documento se usarán algunos ejemplos sencillos para examinar cómo se compila una aplicación de procesamiento de datos con SCP.

## Interfaz de complemento de SCP

Los complementos (o aplicaciones) de SCP son EXE independientes que pueden ejecutarse en Visual Studio durante la fase de desarrollo y conectarse al proceso de Storm tras su implementación en producción. La escritura del complemento de SCP es exactamente igual que la escritura de las demás aplicaciones estándar de consola de Windows. La plataforma SCP.NET declara alguna interfaz de spout/bolt y el código de complemento del usuario se debe implementar en estas interfaces. El objetivo principal de este diseño es que el usuario pueda centrarse en su propia lógica del negocio y dejar que la plataforma SCP.NET controle otras cosas.

El código de complemento del usuario debe implementar una de las siguientes interfaces, en función de que la topología sea transaccional o no transaccional y de que el componentes sea un spout o un bolt.

-   ISCPSpout
-   ISCPBolt
-   ISCPTxSpout
-   ISCPBatchBolt

### ISCPPlugin

ISCPPlugin es la interfaz común para todos los tipos de complementos. Actualmente es una interfaz ficticia.

    public interface ISCPPlugin
    {
    }  

### ISCPSpout

ISCPSpout es la interfaz del spout no transaccional.

    public interface ISCPSpout : ISCPPlugin
    {
        void NextTuple(Dictionary<string, Object> parms);
        void Ack(long seqId, Dictionary<string, Object> parms);
        void Fail(long seqId, Dictionary<string, Object> parms);
    }  

Cuando se llama a NextTuple(), el código C# del usuario puede emitir una o más tuplas. Si no hay nada que emitir, este método se debe devolver sin emitir nada. Debe señalarse que NextTuple(), Ack() y Fail() se llaman todas en un bucle cerrado en un solo subproceso del proceso de C#. Cuando no hay tuplas que emitir, lo correcto es mantener inactiva NextTuple durante una breve cantidad de tiempo (como, por ejemplo, 10 milisegundos) para que no se desperdicie demasiada CPU.

Ack() y Fail() solo se llamarán cuando el mecanismo de confirmación esté habilitado en el archivo de especificación. Se usa “seqId” para identificar la tupla que se confirma o incluye errores. Por lo que si la confirmación se habilita en una topología no transaccional, se debe usar la siguiente función de emisión en spout:

        public abstract void Emit(string streamId, List<object> values, long seqId);  

Si la confirmación no se admite en la topología no transaccional, las funciones Ack() y Fail() pueden dejarse como función vacía.

Los parámetros de entrada “parms” de estas funciones son solo un diccionario vacío, se reservan para su uso en el futuro.

### ISCPBolt

ISCPBolt es la interfaz del bolt no transaccional.

    public interface ISCPBolt : ISCPPlugin
    {
        void Execute(SCPTuple tuple);
    }

Cuando hay una nueva tupla disponible, se llamará a la función Execute() para que la procese.

### ISCPTxSpout

ISCPTxSpout es la interfaz del spout transaccional.

    public interface ISCPTxSpout : ISCPPlugin
    {
        void NextTx(out long seqId, Dictionary<string, Object> parms);
        void Ack(long seqId, Dictionary<string, Object> parms);
        void Fail(long seqId, Dictionary<string, Object> parms);
    }  

Del mismo modo que su contrapartida no transaccional, NextTx(), Ack() y Fail() se llaman todas en un bucle cerrado en un solo subproceso del proceso de C#. Cuando no hay datos que emitir, lo correcto es mantener inactiva NextTx durante una breve cantidad de tiempo (10 milisegundos) para que no se desperdicie demasiada CPU.

NextTx() se llama para iniciar una nueva transacción, el parámetro de salida “seqId” se usa para identificar la transacción, que también se usa en Ack() y Fail(). En NextTx(), el usuario puede emitir datos a la parte Java. Los datos se almacenarán en ZooKeeper para admitir la reproducción. Puesto que la capacidad de ZooKeeper es muy limitada, el usuario solo debe emitir metadatos, no datos masivos en un spout transaccional.

Storm reproducirá automáticamente la transacción en caso de error, por lo que no se debe llamar a Fail() en condiciones normales. Pero, si SCP puede comprobar los datos emitidos por el spout transaccional, puede llamar a Fail() cuando los metadatos no son válidos.

Los parámetros de entrada “parms” de estas funciones son solo un diccionario vacío, se reservan para su uso en el futuro.

### ISCPBatchBolt

ISCPBatchBolt es la interfaz del bolt transaccional.

    public interface ISCPBatchBolt : ISCPPlugin
    {
        void Execute(SCPTuple tuple);
        void FinishBatch(Dictionary<string, Object> parms);
    }  

Execute() se llama cuando una nueva tupla llega al bolt. FinishBatch() se lama cuando esta transacción finaliza. El parámetro de entrada “parms” se reserva para su uso en el futuro.

En el caso de una topología transaccional, existe un concepto importante, “StormTxAttempt”. Tiene dos campos, “TxId” y “AttemptId”. “TxId” se usa para identificar una transacción específica y, para una transacción determinada, puede que haya varios intentos en caso de errores en la transacción y se reproduce. SCP.NET usará otro objeto ISCPBatchBolt para procesar cada “StormTxAttempt”, del mismo modo que hace Storm en la parte Java. La finalidad de este diseño es admitir el procesamiento en paralelo de transacciones. El usuario no debe olvidar que cuando un intento de transacción finaliza, el objeto ISCPBatchBolt correspondiente se destruirá y se recopilará como no utilizado.

## Modelo de objetos

SCP.NET también proporciona un conjunto sencillo de objetos clave para que los desarrolladores programen con ellos. Son **Context**, **StateStore** y **SCPRuntime**. Se tratarán en la parte restante de esta sección.

### Context

Context proporciona un entorno de ejecución para la aplicación. Cada instancia de ISCPPlugin (ISCPSpout/ISCPBolt/ISCPTxSpout/ISCPBatchBolt) tiene su correspondiente instancia de Context. La funcionalidad suministrada por Context se puede dividir en dos partes: (1) la parte estática que está disponible en todo el proceso de C# y (2) la parte dinámica que solo está disponible para la instancia concreta de Context.

#### Parte estática

    public static ILogger Logger = null;
    public static SCPPluginType pluginType;
    public static Config Config { get; set; }
    public static TopologyContext TopologyContext { get; set; }  

“Logger” se proporciona con fines de registro.

“pluginType” se usa para indicar el tipo de complemento del proceso de C#. Si el proceso de C# se ejecuta en modo de prueba local (sin Java), el tipo de complemento es “SCP\_NET\_LOCAL”.

    public enum SCPPluginType
    {
        SCP_NET_LOCAL = 0,
        SCP_NET_SPOUT = 1,
        SCP_NET_BOLT = 2,
        SCP_NET_TX_SPOUT = 3,
        SCP_NET_BATCH_BOLT = 4
    }  

“Config” se proporciona para obtener parámetros de configuración de la parte Java. Los parámetros se pasan desde la parte Java al inicializar el complemento de C#. Los parámetros “Config” se dividen en dos partes: “stormConf” y “pluginConf”

    public Dictionary<string, Object> stormConf { get; set; }
    public Dictionary<string, Object> pluginConf { get; set; }  

“stormConf” corresponde a los parámetros definidos por Storm y “pluginConf”, a los parámetros definidos por SCP. Por ejemplo:

    public class Constants
    {
        … …

        // constant string for pluginConf
        public static readonly String NONTRANSACTIONAL_ENABLE_ACK = "nontransactional.ack.enabled";

        // constant string for stormConf
        public static readonly String STORM_ZOOKEEPER_SERVERS = "storm.zookeeper.servers";
        public static readonly String STORM_ZOOKEEPER_PORT = "storm.zookeeper.port";
    }  

“TopologyContext” se proporciona para obtener el contexto de la topología, resulta más útil en el caso de componentes con paralelismo múltiple. Aquí tiene un ejemplo:

    //demo how to get TopologyContext info
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

#### Parte dinámica

Las siguientes interfaces son adecuadas para una instancia determinada de Context. La instancia de Context se crea con la plataforma SCP.NET y se pasa al código de usuario:

    /* Declare the Output and Input Stream Schemas */
    public void DeclareComponentSchema(ComponentStreamSchema schema);
    /* Emit tuple to default stream. */
    public abstract void Emit(List<object> values);

    /* Emit tuple to the specific stream. */
    public abstract void Emit(string streamId, List<object> values);

Para un spout no transaccional que admita confirmación, se proporciona el siguiente método:

    /* for non-transactional Spout which supports ack */
    public abstract void Emit(string streamId, List<object> values, long seqId);

Para un bolt no transaccional que admita confirmación, se debe aplicar Ack() o Fail() explícitamente a la tupla que recibe. Además, al emitir una nueva tupla, también se deben especificar los delimitadores de la nueva tupla. Se proporcionan los siguientes métodos.

    public abstract void Emit(string streamId, IEnumerable<SCPTuple> anchors, List<object> values);
    public abstract void Ack(SCPTuple tuple);
    public abstract void Fail(SCPTuple tuple);  

### StateStore

**StateStore** proporciona servicios de metadatos, generación de secuencias monotónicas y coordinación sin espera. En **StateStore**, se pueden compilar abstracciones de simultaneidad distribuidas de nivel más alto, como bloqueos distribuidos, colas distribuidas, barreras y servicios de transacciones.

Las aplicaciones de SCP pueden usar el objeto **State** para conservar información en ZooKeeper, sobre todo en el caso de una topología transaccional. Al hacerlo, si un spout transaccional tiene errores y se reinicia, puede recuperar la información necesaria de ZooKeeper y reiniciar del proceso.

El objeto **StateStore** tiene principalmente estos métodos:

    /// <summary>
    /// Static method to retrieve a state store of the given path and connStr
    /// </summary>
    /// <param name="storePath">StateStore Path</param>
    /// <param name="connStr">StateStore Address</param>
    /// <returns>Instance of StateStore</returns>
    public static StateStore Get(string storePath, string connStr);

    /// <summary>
    /// Create a new state object in this state store instance
    /// </summary>
    /// <returns>State from StateStore</returns>
    public State Create();

    /// <summary>
    /// Retrieve all states that were previously uncommitted, excluding all aborted states
    /// </summary>
    /// <returns>Uncommited States</returns>
    public IEnumerable<State> GetUnCommitted();

    /// <summary>
    /// Get all the States in the StateStore
    /// </summary>
    /// <returns>All the States</returns>
    public IEnumerable<State> States();

    /// <summary>
    /// Get state or registry object
    /// </summary>
    /// <param name="info">Registry Name(Registry only)</param>
    /// <typeparam name="T">Type, Registry or State</typeparam>
    /// <returns>Return Registry or State</returns>
    public T Get<T>(string info = null);

    /// <summary>
    /// List all the committed states
    /// </summary>
    /// <returns>Registries contain the Committed State </returns>
    public IEnumerable<Registry> Commited();

    /// <summary>
    /// List all the Aborted State in the StateStore
    /// </summary>
    /// <returns>Registries contain the Aborted State</returns>
    public IEnumerable<Registry> Aborted();

    /// <summary>
    /// Retrieve an existing state object from this state store instance
    /// </summary>
    /// <returns>State from StateStore</returns>
    /// <typeparam name="T">stateId, id of the State</typeparam>
    public State GetState(long stateId)

El objeto **State** tiene principalmente estos métodos:

    /// <summary>
    /// Set the status of the state object to commit
    /// </summary>
    public void Commit(bool simpleMode = true);

    /// <summary>
    /// Set the status of the state object to abort
    /// </summary>
    public void Abort();

    /// <summary>
    /// Put an attribute value under the give key
    /// </summary>
    /// <param name="key">Key</param>
    /// <param name="attribute">State Attribute</param>
    public void PutAttribute<T>(string key, T attribute);

    /// <summary>
    /// Get the attribute value associated with the given key
    /// </summary>
    /// <param name="key">Key</param>
    /// <returns>State Attribute</returns>
    public T GetAttribute<T>(string key);  

En el caso del método Commit(), cuando simpleMode se establece en "true", simplemente eliminará el ZNode correspondiente de ZooKeeper. De lo contrario, eliminará el ZNode actual y agregará un nuevo nodo en COMMITTED\_PATH.

### SCPRuntime

SCPRuntime proporciona los dos métodos siguientes.

        public static void Initialize();

        public static void LaunchPlugin(newSCPPlugin createDelegate);

Initialize() se usa para inicializar el entorno de tiempo de ejecución de SCP. En este método, el proceso de C# se conectará a la parte Java, y obtiene los parámetros de configuración y el contexto de la topología.

LaunchPlugin() se usa para iniciar el bucle de procesamiento de mensajes. En este bucle, el complemento de C# recibirá mensajes de la parte Java (incluidas las tuplas y las señales de control) y luego procesa los mensajes, tal vez llamando al método de la interfaz proporcionado por el código de usuario. El parámetro de entrada del método LaunchPlugin() es un delegado que puede devolver un objeto que implemente la interfaz ISCPSpout/IScpBolt/ISCPTxSpout/ISCPBatchBolt.

    public delegate ISCPPlugin newSCPPlugin(Context ctx, Dictionary<string, Object> parms);  

En el caso de ISCPBatchBolt, podemos obtener “StormTxAttempt” de “parms” y usarlo para decidir si se trata de un intento reproducido. Esto suele realizarse en el bolt de confirmación y se muestra en el ejemplo de “HelloWorldTx”.

En términos generales, los complementos de SCP pueden ejecutarse aquí en dos modos:

1.  Modo de prueba local: En este modo, los complementos de SCP (el código de usuario de C#) se ejecutan en Visual Studio durante la fase de desarrollo. “LocalContext” se puede usar en este modo, lo que proporciona un método para serializar las tuplas emitidas a archivos locales y leerlas de nuevo en la memoria.

        public interface ILocalContext
        {
            List<SCPTuple> RecvFromMsgQueue();
            void WriteMsgQueueToFile(string filepath, bool append = false);
            void ReadFromFileToMsgQueue(string filepath);
        }  

2.  Modo normal: En este modo, los complementos de SCP se inician con el proceso java de storm.

    A continuación se ofrece un ejemplo de inicio de un complemento de SCP:

        namespace Scp.App.HelloWorld
        {
          public class Generator : ISCPSpout
          {
            … …
            public static Generator Get(Context ctx, Dictionary<string, Object> parms)
            {
              return new Generator(ctx);
            }
          }

          class HelloWorld
          {
            static void Main(string[] args)
            {
              /* Setting the environment variable here can change the log file name */
              System.Environment.SetEnvironmentVariable("microsoft.scp.logPrefix", "HelloWorld");

              SCPRuntime.Initialize();
              SCPRuntime.LaunchPlugin(new newSCPPlugin(Generator.Get));
        }
          }
        }

## Lenguaje de especificación de topologías

La especificación de topologías de SCP es un lenguaje específico de dominios para la descripción y configuración de topologías de SCP. Se basa en Clojure DSL de Storm (<http://storm.incubator.apache.org/documentation/Clojure-DSL.html>) y se amplía con SCP.

Las especificaciones de topologías se pueden enviar directamente a un clúster de Storm para su ejecución a través del comando ***runspec***.

SCP.NET ha agregado las siguientes funciones para definir la topología transaccional:

| Nuevas funciones       | Parámetros    | Descripción                                                                                                                                     |
|------------------------|---------------|-------------------------------------------------------------------------------------------------------------------------------------------------|
| **tx-topolopy**        | topology-name 
                           spout-map     
                          bolt-map       | Permite definir una topología transaccional con el nombre de la topología, el mapa de definición de spouts y el mapa de definición de bolts.    |
| **scp-tx-spout**       | exec-name     
                          args           
                          fields         | Permite definir un spout transaccional. Ejecutará la aplicación con ***exec-name*** usando ***args***.                                          
                                          ***fields*** corresponde a los campos de salida del spout.                                                                                       |
| **scp-tx-batch-bolt**  | exec-name     
                          args           
                          fields         | Permite definir un bolt de lote. Ejecutará la aplicación con ***exec-name*** usando ***args***.                                                 
                                          Fields corresponde a los campos de salida del bolt.                                                                                              |
| **scp-tx-commit-bolt** | exec-name     
                          args           
                          fields         | Permite definir un bolt de autor. Ejecutará la aplicación con ***exec-name*** usando args.                                                      
                                          ***fields*** corresponde a los campos de salida del bolt.                                                                                        |
| **nontx-topolopy**     | topology-name 
                           spout-map     
                          bolt-map       | Permite definir una topología no transaccional con el nombre de la topología, el mapa de definición de spouts y el mapa de definición de bolts. |
| **scp-spout**          | exec-name     
                          args           
                          fields         
                          parameters     | Permite definir un spout no transaccional. Ejecutará la aplicación con ***exec-name*** usando ***args***.                                       
                                          ***fields*** corresponde a los campos de salida del spout                                                                                        
                                          ***parameters*** es opcional, se usa para especificar algunos parámetros como, por ejemplo, "nontransactional.ack.enabled".                      |
| **scp-bolt**           | exec-name     
                          args           
                          fields         
                          parameters     | Permite definir un bolt no transaccional. Ejecutará la aplicación con ***exec-name*** usando ***args***.                                        
                                          ***fields*** corresponde a los campos de salida del bolt                                                                                         
                                          ***parameters*** es opcional, se usa para especificar algunos parámetros como, por ejemplo, "nontransactional.ack.enabled".                      |

SCP.NET tiene definidas las siguientes palabras clave:

| Palabras clave | Descripción                                                                   |
|----------------|-------------------------------------------------------------------------------|
| **:name**      | Permite definir el nombre de la topología                                     |
| **:topology**  | Permite definir la topología con las funciones anteriores e incorporar otras. |
| **:p**         | Permite definir la sugerencia de paralelismo para cada spout o bolt.          |
| **:config**    | Permite definir un parámetro de configuración o actualizar los existentes.    |
| **:schema**    | Permite definir el esquema de la secuencia.                                   |

Y los parámetros de uso frecuente:

| Parámetro                          | Descripción                                                                 |
|------------------------------------|-----------------------------------------------------------------------------|
| **"plugin.name"**                  | Nombre del archivo exe del complemento de C#                               |
| **"plugin.args"**                  | Argumentos del complemento                                                  |
| **"output.schema"**                | Esquema de salida                                                           |
| **"nontransactional.ack.enabled"** | Indica si se ha habilitado confirmación para una topología no transaccional |

El comando runspec se implementará junto con los bits, el uso es similar a:

    .\bin\runSpec.cmd
    usage: runSpec [spec-file target-dir [resource-dir] [-cp classpath]]
     ex: runSpec examples\HelloWorld\HelloWorld.spec specs examples\HelloWorld\Target

El parámetro ***resource-dir*** es opcional, es necesario especificarlo cuando se desea conectar una aplicación de C#. Este directorio contendrá la aplicación, las dependencias y las configuraciones.

El parámetro ***classpath*** también es opcional. Se usa para especificar el classpath de Java si el archivo de especificación contiene un spout o un bolt de Java.

## Características varias

### Declaración de esquema de entrada y salida

El usuario puede emitir una tupla de un proceso de C#, la plataforma necesita serializar la tupla en byte[], transferirla a la parte Java, y Storm transferirá esta tupla a los destinos. Mientras tanto, en el componente de bajada, el proceso de C# recibirá de nuevo la tupla de la parte Java y la convierte a los tipos originales por plataforma; todas estas operaciones quedan ocultas por la plataforma.

Para que admita la serialización y la deserialización, es necesario que el código de usuario declare el esquema de las entradas y salidas.

El esquema de secuencias de entrada y salida se define como diccionario, la clave es el StreamId y el valor corresponde a los tipos de la columnas. El componente puede tener declaradas multisecuencias.

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

En el objeto Context, hemos agregado la siguiente API:

     public void DeclareComponentSchema(ComponentStreamSchema schema)

El código de usuario debe comprobar que las tuplas emitidas siguen el esquema definido para la secuencia o el sistema lanzará una excepción en tiempo de ejecución.

### Compatibilidad de multisecuencias

SCP admite que el código de usuario emita o reciba desde varias secuencias distintas a la vez. La compatibilidad se refleja en el objeto Context cuando el método Emit adopta un parámetro de identificador de secuencia opcional.

Se han agregado dos métodos al objeto Context de SCP.NET. Se usan para emitir una tupla o tuplas para especificar StreamId. StreamId es una cadena y debe ser coherente en C# y en la especificación de definición de la topología.

        /* Emit tuple to the specific stream. */
        public abstract void Emit(string streamId, List<object> values);

        /* for non-transactional Spout only */
        public abstract void Emit(string streamId, List<object> values, long seqId);  

La emisión a una secuencia inexistente provocará excepciones en tiempo de ejecución.

### Agrupación de campos

La agrupación de campos integrada de Storm no funciona correctamente en SCP.NET. En la parte de Proxy Java, todos los tipos de datos son en realidad byte[] y la agrupación de campos usa el código hash del objeto byte[] para realizar la agrupación. El código hash del objeto byte[] es la dirección de este objeto en la memoria. Por lo que la agrupación será incorrecta para dos objetos byte[] que compartan el mismo contenido pero no la misma dirección.

SCP.NET agrega un método de agrupación personalizado y usará el contenido del objeto byte[] para realizar la agrupación. En el archivo **SPEC**, la sintaxis es similar a:

    (bolt-spec
        {
            “spout_test” (scp-field-group :non-tx [0,1])
        }
        …
    )  

Aquí,

1.  “scp-field-group” significa “Agrupación personalizada de campos implementada mediante SCP”.
2.  “:tx” o “:non-tx” indica si se trata de una topología transaccional. Necesitamos esta información porque el índice inicial es diferente en las topologías tx y non-tx.
3.  [0,1] indica un hashset de campos de identificadores, a partir de 0.

### Topologías híbridas

El Storm nativo se escribe en Java y la característica más importante del proyecto de SCP es permitir que nuestros clientes escriban código C# para tratar su lógica de negocios. Pero también admitimos topologías híbridas, que no solo contengan spouts y bolts de C#, sino también spouts o bolts de Java.

#### Especificación de spout o bolt de Java en el archivo de especificación

En el archivo de especificación, también pueden usarse "scp-spout" y "scp-bolt" para especificar spouts o bolts de Java; a continuación se ofrece un ejemplo:

    (spout-spec 
      (microsoft.scp.example.HybridTopology.Generator.)           
      :p 1)  

Aquí, “microsoft.scp.example.HybridTopology.Generator” es el nombre de la clase Spout de Java.

#### Especificación de classpath de Java en el comando runSpec

Si desea enviar una topología que contenga spouts o bolts de Java, debe compilar primero los spouts o bolts de Java y obtener los archivos Jar. A continuación, debe especificar el classpath de Java que contiene los archivos Jar al enviar la topología. Aquí tiene un ejemplo:

    bin\runSpec.cmd examples\HybridTopology\HybridTopology.spec specs examples\HybridTopology\net\Target -cp examples\HybridTopology\java\target\*  

Aquí, "examples\\HybridTopology\\java\\target" es la carpeta que contiene el archivo Jar.

#### Serialización y deserialización entre Java y C

En una topología híbrida, debemos admitir la serialización de un objeto arbitrario en la parte Java y la deserialización en la parte C#, y viceversa. **Actualmente, solo admitimos spout de Java y bolt de C#**, y queda una mejora adicional para una versión futura.

En primer lugar, proporcionamos la implementación predeterminada de serialización en la parte Java y deserialización en la parte C#. El método de serialización de la parte Java se puede especificar en el archivo SPEC:

      (scp-bolt
        {
          "plugin.name" "HybridTopology.exe"
          "plugin.args" ["displayer"]
          "output.schema" {}
          "customized.java.serializer" ["microsoft.scp.storm.multilang.CustomizedInteropJSONSerializer"]
        })  

El método de deserialización de la parte C# debe especificarse en el código de usuario de C#:

    Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
    inputSchema.Add("default", new List<Type>() { typeof(Person) });
    this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, null));
    this.ctx.DeclareCustomizedDeserializer(new CustomizedInteropJSONDeserializer());  

Esta implementación predeterminada debe tratar la mayoría de los casos si el tipo de datos no es demasiado complejo. En determinados casos, bien porque el tipo de datos del usuario sea demasiado complejo o porque el rendimiento de nuestra implementación predeterminada no satisfaga los requisitos del cliente, el usuario puede conectar su propia implementación.

La interfaz de serialización en la parte Java se define de la manera siguiente:

    public interface ICustomizedInteropJavaSerializer {
        public void prepare(String[] args);
        public List<ByteBuffer> serialize(List<Object> objectList);
    }  

La interfaz de deserialización en la parte C# se define de la manera siguiente:

    public interface ICustomizedInteropCSharpDeserializer
    {
        List<Object> Deserialize(List<byte[]> dataList, List<Type> targetTypes);
    }  

## Modo host de SCP

En este modo, el usuario puede compilar sus códigos en DLL y usar el SCPHost.exe proporcionado por SCP para enviar la topología. El archivo de especificación es similar al siguiente:

        (scp-spout
          {
            "plugin.name" "SCPHost.exe"
            "plugin.args" ["HelloWorld.dll" "Scp.App.HelloWorld.Generator" "Get"]
            "output.schema" {"default" ["sentence"]}
          })

Aquí, "plugin.name" se especifica como el "SCPHost.exe" suministrado por el SDK de SCP. SCPHost.exe que acepta exactamente tres parámetros:

1.  El primero es el nombre del archivo DLL que, en este ejemplo, es "HelloWorld.dll".
2.  El segundo es el nombre de clase que, en este ejemplo, es "Scp.App.HelloWorld.Generator".
3.  El tercero es el nombre de un método estático público, que se puede invocar para obtener una instancia de ISCPPlugin.

En modo host, el código de usuario se compila como DLL y lo invoca la plataforma SCP. Por consiguiente, la plataforma SCP puede obtener el control completo de toda la lógica de procesamiento. Por consiguiente, recomendamos a nuestros clientes que envíen la topología en modo host de SCP, ya que puede simplificar la experiencia de desarrollo y proporcionarnos mayor flexibilidad y también mejor compatibilidad con versiones anteriores para una versión posterior.

## Ejemplos de programación de SCP

### HelloWorld

HelloWorld es un ejemplo muy sencillo para dar una idea de SCP.Net. Usa una topología no transaccional, son un spout denominado “generator” y dos bolts denominados “splitter” y “counter”. El spout “generator” generará aleatoriamente algunas oraciones y emitirá estas oraciones a “splitter”. El bolt “splitter” dividirá las oraciones en palabras y emitirá estas palabras al bolt “counter”. El bolt “counter” usa un diccionario para registrar el número de repeticiones de cada palabra.

En este ejemplo, hay dos archivos spec, “HelloWorld.spec” y “HelloWorld\_EnableAck.spec”. En el código C#, se puede descubrir si se ha habilitado confirmación obteniendo pluginConf de la parte Java.

    /* demo how to get pluginConf info */
    if (Context.Config.pluginConf.ContainsKey(Constants.NONTRANSACTIONAL_ENABLE_ACK))
    {
        enableAck = (bool)(Context.Config.pluginConf[Constants.NONTRANSACTIONAL_ENABLE_ACK]);
    }
    Context.Logger.Info("enableAck: {0}", enableAck);  

En el spout, si se ha habilitado confirmación, se usa un diccionario para almacenar en caché las tuplas que no se han confirmado. Si llama a Fail(), la tupla con errores se reproducirá:

    public void Fail(long seqId, Dictionary<string, Object> parms)
    {
        Context.Logger.Info("Fail, seqId: {0}", seqId);
        if (cachedTuples.ContainsKey(seqId))
        {
            /* get the cached tuple */
            string sentence = cachedTuples[seqId];

            /* replay the failed tuple */
            Context.Logger.Info("Re-Emit: {0}, seqId: {1}", sentence, seqId);
            this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new Values(sentence), seqId);
        }
        else
        {
            Context.Logger.Warn("Fail(), can't find cached tuple for seqId {0}!", seqId);
        }
    }

### HelloWorldTx

El ejemplo HelloWorldTx muestra cómo implementar la topología transaccional. Incluye un spout denominado "generator", un bolt de lote denominado "partial-count" y un bolt de confirmación denominado "count-sum". Existen también tres archivos txt creados previamente: "DataSource0.txt", "DataSource1.txt" y "DataSource2.txt".

En cada transacción, el spout "generator" elegirá aleatoriamente dos de los tres archivos creados previamente y emitirá los nombres de estos dos archivos al bolt "partial-count". El bolt "partial-count" obtendrá primero el nombre de archivo de la tupla recibida, luego abrirá el archivo y contará el número de palabras que hay en el mismo y, por último, emitirá el número de palabras al bolt "count-sum". El bolt "count-sum" resumirá el recuento total.

Para obtener la semántica "exactly once", es necesario que el bolt de confirmación "count-sum" decida si se trata de una transacción reproducida. En este ejemplo se incluye una variable de miembro estática:

    public static long lastCommittedTxId = -1;  

Una vez creada una instancia ISCPBatchBolt, obtendrá txAttempt de los parámetros de entrada:

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

Cuando se llame a FinishBatch(), se actualizará lastCommittedTxId si no se trata de una transacción reproducida.

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
        … …
    }  

### HybridTopology

Esta topología contiene un spout de Java y un bolt de C#. Usa la implementación predeterminada de serialización y deserialización que proporciona la plataforma SCP. Consulte "HybridTopology.spec" en la carpeta "examples\\HybridTopology" para obtener los detalles del archivo spec y "SubmitTopology.bat" para saber cómo se especifica el classpath de Java.

### Demostración de SCPHost

Este ejemplo es, básicamente, igual a HelloWorld. La única diferencia es que el código de usuario se compila como DLL y la topología se envía mediante SCPHost.exe. Consulte la sección “Modo host de SCP” para obtener una explicación más detallada.

  [0]: ./media/hdinsight-hadoop-storm-scpdotnet-csharp-develop-streaming-data-processing-application/hdinsight-hadoop-storm-scpdotnet-csharp-develop-streaming-data-processing-application-01.png
